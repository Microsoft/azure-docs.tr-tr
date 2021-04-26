---
title: "Öğretici: RDS PostgreSQL online 'ı PostgreSQL için Azure veritabanı 'na geçirme"
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti 'ni kullanarak RDS PostgreSQL 'ten PostgreSQL için Azure veritabanı 'na çevrimiçi geçiş gerçekleştirmeyi öğrenin.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: c2a6a71365b48fa4349306ce632f5762c38dacf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597479"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>Öğretici: DMS kullanarak RDS PostgreSQL 'i PostgreSQL için Azure DB 'ye geçirme

Kaynak veritabanı geçiş sırasında çevrimiçi kaldığı halde, bir RDS PostgreSQL örneğinden veritabanlarını [PostgreSQL Için Azure veritabanı](../postgresql/index.yml) 'na geçirmek Için Azure veritabanı geçiş hizmeti 'ni kullanabilirsiniz. Diğer bir deyişle, geçişe uygulamanın en az kapalı kalma süresi ile ulaşılabilecek. Bu öğreticide, Azure veritabanı geçiş hizmeti 'ndeki çevrimiçi geçiş etkinliğini kullanarak, bir RDS PostgreSQL 9,6 örneğinden gelen **DVD Kiralama** örneği veritabanını PostgreSQL Için Azure veritabanı 'na geçireceğiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> * Örnek şemayı pg_dump yardımcı programını kullanarak geçirin.
> * Azure Veritabanı Geçiş Hizmeti örneği oluşturun.
> * Azure veritabanı geçiş hizmeti 'ni kullanarak bir geçiş projesi oluşturun.
> * Geçişi çalıştırma.
> * Geçişi izleme.
> * Cutover geçişi gerçekleştirin.

> [!NOTE]
> Çevrimiçi bir geçiş gerçekleştirmek için Azure veritabanı geçiş hizmeti 'nin kullanılması, Premium fiyatlandırma katmanını temel alan bir örnek oluşturulmasını gerektirir. Daha fazla bilgi için bkz. Azure veritabanı geçiş hizmeti [fiyatlandırma](https://azure.microsoft.com/pricing/details/database-migration/) sayfası. Geçiş işlemi sırasında veri hırsızlığına engel olmak için diski şifreliyoruz.

> [!IMPORTANT]
> En iyi geçiş deneyimi için Microsoft, Azure Veritabanı Geçiş Hizmeti’nin bir örneğini hedef veritabanıyla aynı Azure bölgesinde oluşturmayı önerir. Verileri bölgeler veya coğrafyalar arasında taşımak, geçiş sürecini yavaşlatabilir ve hatalara neden olabilir.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Bu makalede PostgreSQL 'in şirket içi örneğinden PostgreSQL için Azure veritabanı 'na çevrimiçi geçiş gerçekleştirme işlemi açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

* [PostgreSQL Community edition](https://www.postgresql.org/download/) 9,5, 9,6 veya 10 ' u indirin ve yükleyin. Kaynak PostgreSQL sunucusu sürümü 9.5.11, 9.6.7, 10 veya üzeri olmalıdır. Daha fazla bilgi için bkz. [PostgreSQL veritabanı sürümlerini destekleyen](../postgresql/concepts-supported-versions.md)makale.

   Ayrıca, PostgreSQL için hedef Azure veritabanı sürümünün RDS PostgreSQL sürümüne eşit veya ondan daha yeni olması gerektiğini unutmayın. Örneğin, RDS PostgreSQL 9,6 yalnızca PostgreSQL için Azure veritabanı 9,6, 10 veya 11 ' e geçiş yapabilir, ancak PostgreSQL için Azure veritabanı 9,5 için kullanılamaz.

* [PostgreSQL Için Azure veritabanı](../postgresql/quickstart-create-server-database-portal.md) veya [PostgreSQL için Azure veritabanı-hiper ölçek (Citus)](../postgresql/quickstart-create-hyperscale-portal.md)için bir örnek oluşturun. PgAdmin kullanarak PostgreSQL sunucusuna bağlanma hakkında ayrıntılı bilgi için belgenin bu [bölümüne](../postgresql/quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) bakın.
* [ExpressRoute](../expressroute/expressroute-introduction.md) veya [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti için bir Microsoft Azure sanal ağ oluşturun. Sanal ağ oluşturma hakkında daha fazla bilgi için [sanal ağ belgelerine](../virtual-network/index.yml)ve özellikle adım adım ayrıntılarla birlikte hızlı başlangıç makalelerine bakın.
* Sanal ağ ağ güvenlik grubu kurallarınızın ServiceBus, Storage ve AzureMonitor için ServiceTag giden bağlantı noktası 443 ' i engellemediğinden emin olun. Sanal ağ NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/virtual-network-vnet-plan-design-arm.md)makalesine bakın.
* [Windows Güvenlik Duvarınızı veritabanı altyapısı erişimi](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) için yapılandırın.
* Azure veritabanı geçiş hizmeti 'nin kaynak PostgreSQL sunucusuna erişmesine izin vermek için Windows Güvenlik duvarınızı açın, varsayılan olarak TCP bağlantı noktası 5432 ' dir.
* Kaynak veritabanlarınızın önünde bir güvenlik duvarı cihazı kullanıyorsanız, Azure Veritabanı Geçiş Hizmeti'nin geçiş amacıyla kaynak veritabanlarına erişmesi için güvenlik duvarı kuralları eklemeniz gerekebilir.
* Azure veritabanı geçiş hizmeti 'nin hedef veritabanlarına erişmesine izin vermek için PostgreSQL için Azure veritabanı sunucusu için sunucu düzeyinde bir [güvenlik duvarı kuralı](../postgresql/concepts-firewall-rules.md) oluşturun. Azure veritabanı geçiş hizmeti için kullanılan sanal ağın alt ağ aralığını belirtin.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Çoğaltma için AWS RDS PostgreSQL ayarlama

1. Yeni bir parametre grubu oluşturmak için, [DB parametre gruplarıyla çalışma](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html)makalesindeki AWS tarafından belirtilen yönergeleri izleyin.
2. Azure veritabanı geçiş hizmeti ' nden kaynağa bağlanmak için ana Kullanıcı adını kullanın. Ana Kullanıcı hesabı dışında bir hesap kullanıyorsanız, hesabın rds_superuser rolü ve rds_replication rolü olmalıdır. Rds_replication rolü, mantıksal yuvaları yönetme ve mantıksal yuvaları kullanarak veri akışı için izinler verir.
3. Aşağıdaki yapılandırmayla yeni bir parametre grubu oluşturun:

    a. DB parametre grubunuzda rds.logical_replication parametresini 1 olarak ayarlayın.

    b. max_wal_senders = [eşzamanlı görev sayısı]-max_wal_senders parametresi çalıştırılabilen eşzamanlı görevlerin sayısını belirler, 10 görev önerir.

    c. max_replication_slots – = [yuva sayısı], beş yuva olarak ayarla önerilir.

4. Oluşturduğunuz parametre grubunu RDS PostgreSQL örneğiyle ilişkilendirin.

## <a name="migrate-the-schema"></a>Şemayı geçirme

1. Şemayı kaynak veritabanından ayıklayın ve tablo şemaları, dizinler ve saklı yordamlar gibi tüm veritabanı nesnelerinin geçişini gerçekleştirmek için hedef veritabanına uygulayın.

    Yalnızca şemayı geçirmenin en kolay yolu, pg_dump-s seçeneği ile kullanmaktır. Daha fazla bilgi için bkz. Postgres pg_dump öğreticisindeki [örneklere](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) bakın.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Örneğin, **dvdkiralık** veritabanı için bir şema dosyasının dökümünü yapmak için aşağıdaki komutu kullanın:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Hedef hizmette PostgreSQL için Azure veritabanı olan boş bir veritabanı oluşturun. Bağlanmak ve bir veritabanı oluşturmak için aşağıdaki makalelerden birine bakın:

    * [Azure portal kullanarak PostgreSQL için Azure veritabanı sunucusu oluşturma](../postgresql/quickstart-create-server-database-portal.md)
    * [Azure portal kullanarak PostgreSQL için Azure veritabanı-hiper ölçek (Citus) sunucusu oluşturma](../postgresql/quickstart-create-hyperscale-portal.md)

3. Şemayı, PostgreSQL için Azure veritabanı olan hedef hizmete aktarın. Şema dökümü dosyasını geri yüklemek için şu komutu çalıştırın:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Örnek:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Şemanızda yabancı anahtarlar varsa geçişe ilişkin ilk yük ve sürekli eşitleme başarısız olur. Bırakma yabancı anahtar betiği çıkarmak ve hedefte yabancı anahtar betiği eklemek için (PostgreSQL için Azure veritabanı), PgAdmin 'de veya psql 'de aşağıdaki betiği çalıştırın:
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. Yabancı anahtarı bırakmak için sorgu sonucunda bırakma yabancı anahtarını (ikinci sütun) çalıştırın.

6. Verilerde Tetikleyiciler (ekleme veya güncelleştirme tetikleyicisi) varsa, verileri kaynaktan çoğaltmadan önce hedefte veri bütünlüğünü zorunlu kılar. Bu öneri, geçiş sırasında *Hedefteki* tüm tablolardaki Tetikleyicileri devre dışı bırakmak ve geçiş tamamlandıktan sonra Tetikleyicileri etkinleştirir.

    Hedef veritabanında Tetikleyicileri devre dışı bırakmak için:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name,' ON ', event_object_table, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration kaynak sağlayıcısını kaydetme

1. Azure portal'da oturum açın, **Tüm hizmetler**'i ve ardından **Abonelikler**'i seçin.

   ![Portal aboneliklerini gösterme](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve ardından **kaynak sağlayıcıları**' nı seçin.

    ![Kaynak sağlayıcılarını gösterme](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. "migration" araması yapın ve **Microsoft.DataMigration** öğesinin sağ tarafındaki **Kaydet**'i seçin.

    ![Kaynak sağlayıcısını kaydetme](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Veritabanı Geçiş Hizmeti'nin örneğini oluşturma

1. Azure portal + **kaynak oluştur**' u seçin, Azure veritabanı geçiş hizmeti ' ni arayın ve ardından açılan listeden **Azure veritabanı geçiş hizmeti** ' ni seçin.

    ![Azure Market](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. **Geçiş Hizmeti oluşturun** ekranında hizmet için bir ad belirtin, aboneliği ve yeni ya da var olan bir kaynak grubunu seçin.

4. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz konumu seçin.

5. Var olan bir sanal ağı seçin veya yeni bir ağ oluşturun.

    Sanal ağ, Azure veritabanı geçiş hizmeti 'ni kaynak PostgreSQL örneğine ve hedef Azure Database for PostgreSQL örneğine erişimi sağlar.

    Azure portal sanal ağ oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/quick-create-portal.md)makalesine bakın.

6. Fiyatlandırma katmanı seçin; Bu çevrimiçi geçiş için Premium: 4Vçekirdekleri fiyatlandırma katmanını seçtiğinizden emin olun.

    ![Azure Veritabanı Geçiş Hizmeti örneği ayarlarını yapılandırma](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. Hizmeti oluşturmak için **Oluştur**’u seçin.

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Hizmet oluşturulduktan sonra Azure portaldan bulun, açın ve yeni bir geçiş projesi oluşturun.

1. Azure portalda **Tüm hizmetler**'i seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

      ![Azure Veritabanı Geçiş Hizmeti’nin tüm örneklerini bulma](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. **Azure veritabanı geçiş Hizmetleri** ekranında, oluşturduğunuz Azure veritabanı geçiş hizmeti örneğinin adını arayın, örneği seçin ve + **yeni geçiş projesi**' ni seçin.
3. **Yeni geçiş projesi** ekranında, proje için bir ad belirtin, **kaynak sunucu türü** metın kutusunda **AWS RDS for PostgreSQL**' i seçin ve ardından **hedef sunucu türü** metin kutusunda **PostgreSQL için Azure veritabanı**' nı seçin.
4. **Etkinlik türünü seçin** bölümünde **çevrimiçi veri geçişi**' ni seçin.

    > [!IMPORTANT]
    > **Çevrimiçi veri geçişi**' ni seçtiğinizden emin olun; Bu senaryo için çevrimdışı geçişler desteklenmez.

    ![Veritabanı Geçiş Hizmeti Projesi Oluşturma](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Alternatif olarak, şimdi **proje oluştur** ' u seçerek geçiş projesini hemen oluşturabilir ve geçişi daha sonra yürütebilirsiniz.

5. **Kaydet**’i seçin.

6. Projeyi oluşturmak ve geçiş etkinliğini çalıştırmak için **Etkinlik oluştur ve çalıştır**'ı seçin.

    > [!NOTE]
    > Lütfen proje oluşturma dikey penceresinde çevrimiçi geçiş ayarlamak için gereken önkoşulları unutmayın.

## <a name="specify-source-details"></a>Kaynak ayrıntılarını belirtme

* **Kaynak ayrıntıları Ekle** ekranında, kaynak PostgreSQL örneği için bağlantı ayrıntılarını belirtin.

   ![Kaynak Ayrıntıları](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Kaydet**' i seçin ve ardından **hedef ayrıntıları** ekranında, önceden sağlanmış olan ve pg_dump kullanılarak **DVD** çalar, daha önce sağlanan PostgreSQL için Azure veritabanı sunucusu için bağlantı ayrıntılarını belirtin.

    ![Hedef ayrıntıları](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. **Kaydet**'i seçin ve **Hedef veritabanlarıyla eşleyin** ekranında geçiş yapılacak kaynak ve hedef veritabanlarını eşleyin.

    Hedef veritabanı, kaynak veritabanıyla aynı veritabanı adını içeriyorsa, Azure veritabanı geçiş hizmeti varsayılan olarak hedef veritabanını seçer.

    ![Hedef veritabanlarıyla eşleyin](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. **Kaydet**'i seçin, **Geçiş özeti** ekranındaki **Etkinlik adı** metin kutusunda geçiş etkinliği için bir ad belirtin ve ardından, kaynak ve hedef ayrıntılarının önceden belirttiğiniz ayrıntılarla eşleştiğinden emin olmak üzere özeti gözden geçirin.

    ![Geçiş Özeti](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Geçişi çalıştırma

* **Geçişi çalıştır**'ı seçin.

    Geçiş etkinliği penceresi görünür ve etkinliğin **durumu** **başlatılıyor**.

## <a name="monitor-the-migration"></a>Geçişi izleme

1. Geçiş etkinliği ekranında **Yenile**'yi seçerek, gösterilen verileri, geçişin **Durum** bilgisi **Çalıştırılıyor** olana kadar güncelleştirebilirsiniz.

    ![Etkinlik durumu-çalışıyor](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. **VERITABANı adı** altında, **tam veri yükü** ve **artımlı veri eşitleme** işlemleri için geçiş durumuna ulaşmak üzere belirli bir veritabanını seçin.

    **Tam veri yükleme** , ilk yük geçiş durumunu gösterir, ancak **artımlı veri eşitleme** , DEĞIŞIKLIK verilerini yakalama (CDC) durumunu gösterir.

    ![Envanter ekranı-tam veri yükleme](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Envanter ekranı-artımlı veri eşitleme](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Tam geçiş gerçekleştirme

İlk tam yükleme tamamlandıktan sonra veritabanları **cutover 'e başlamaya** göre işaretlenir.

1. Veritabanı geçişini tamamlamaya hazır olduğunuzda **Tam Geçişi Başlat** seçeneğini belirleyin.

2. Kaynak veritabanındaki tüm gelen işlemlerin durdurulduğundan emin olmak için **bekleyen değişiklikler** sayacı **0** ' ı gösterene kadar bekleyin, **Onayla** onay kutusunu seçin ve ardından **Uygula**' yı seçin.

    ![Tam geçişi ekranını doldurun](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. Veritabanı geçiş durumu **tamamlandı** olarak görüntülendiğinde, uygulamalarınızı yeni hedef PostgreSQL veritabanı Için Azure veritabanı 'na bağlayın.

Şirket içi bir RDS PostgreSQL örneğini PostgreSQL için Azure veritabanı 'na çevrimiçi geçişiniz artık tamamlanmıştır.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Veritabanı Geçiş Hizmeti hakkında bilgi için [What is the Azure Database Migration Service? (Azure Veritabanı Geçiş Hizmeti nedir?)](./dms-overview.md) başlıklı makaleye bakın.
* PostgreSQL için Azure veritabanı hakkında daha fazla bilgi için [PostgreSQL Için Azure veritabanı nedir?](../postgresql/overview.md)makalesine bakın.
* Diğer sorular için [Azure veritabanı geçişleri](mailto:AskAzureDatabaseMigrations@service.microsoft.com) diğer adına e-posta gönderin.
