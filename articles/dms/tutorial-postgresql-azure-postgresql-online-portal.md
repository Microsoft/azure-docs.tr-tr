---
title: "Öğretici: Azure portal aracılığıyla PostgreSQL için Azure veritabanı 'na PostgreSQL için Azure DB 'yi geçirin"
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti 'ni Azure portal aracılığıyla kullanarak şirket içi PostgreSQL 'ten PostgreSQL için Azure veritabanı 'na çevrimiçi geçiş yapmayı öğrenin.
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
ms.openlocfilehash: d28c45b2d0fc1a123f44020f42c4d2c59c593cb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709929"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Öğretici: Azure portal aracılığıyla DMS kullanarak PostgreSQL için PostgreSQL için Azure DB 'ye geçiş yapma

Azure veritabanı geçiş hizmeti 'ni, bir şirket içi PostgreSQL örneğinden veritabanlarını, uygulamanın en az kapalı kalma süresine sahip [PostgreSQL Için Azure veritabanı](../postgresql/index.yml) 'na geçirmek için kullanabilirsiniz. Bu öğreticide, Azure veritabanı geçiş hizmeti 'ndeki çevrimiçi geçiş etkinliğini kullanarak PostgreSQL 9,6 'nin şirket içi bir örneğinden **DVD Kiralama** örneği veritabanını PostgreSQL Için Azure veritabanı 'na geçireceğiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> * Pg_dump yardımcı programını kullanarak örnek şemayı geçirin.
> * Azure Veritabanı Geçiş Hizmeti örneği oluşturun.
> * Azure veritabanı geçiş hizmeti 'nde bir geçiş projesi oluşturun.
> * Geçişi çalıştırma.
> * Geçişi izleme.
> * Cutover geçişi gerçekleştirin.

> [!NOTE]
> Çevrimiçi bir geçiş gerçekleştirmek için Azure veritabanı geçiş hizmeti 'nin kullanılması, Premium fiyatlandırma katmanını temel alan bir örnek oluşturulmasını gerektirir. Geçiş işlemi sırasında veri hırsızlığına engel olmak için disk şifreliyoruz

> [!IMPORTANT]
> En iyi geçiş deneyimi için, Microsoft, hedef veritabanıyla aynı Azure bölgesinde Azure veritabanı geçiş hizmeti örneği oluşturulmasını önerir. Verileri bölgeler veya coğrafyalar arasında taşımak, geçiş sürecini yavaşlatabilir ve hatalara neden olabilir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

* [PostgreSQL Community edition](https://www.postgresql.org/download/) 9,4, 9,5, 9,6 veya 10 sürümünü indirip yükleyin. Kaynak PostgreSQL Server sürümü 9,4, 9,5, 9,6, 10 veya 11 olmalıdır. Daha fazla bilgi için bkz. [PostgreSQL veritabanı sürümlerini destekleyen](../postgresql/concepts-supported-versions.md)makale.

    Ayrıca, PostgreSQL için hedef Azure veritabanının, şirket içi PostgreSQL sürümüne eşit veya ondan daha yeni olması gerektiğini unutmayın. Örneğin PostgreSQL 9,6, PostgreSQL için Azure veritabanı 9,6, 10 veya 11 ' e geçirebilir, ancak PostgreSQL için Azure veritabanı 9,5.

* [PostgreSQL Için Azure veritabanı sunucusu oluşturun](../postgresql/quickstart-create-server-database-portal.md) veya [PostgreSQL için Azure veritabanı-Hyperscale (Citus) sunucusu oluşturun](../postgresql/quickstart-create-hyperscale-portal.md).
* [ExpressRoute](../expressroute/expressroute-introduction.md) veya [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti için bir Microsoft Azure sanal ağ oluşturun. Sanal ağ oluşturma hakkında daha fazla bilgi için [sanal ağ belgelerine](../virtual-network/index.yml)ve özellikle adım adım ayrıntılarla birlikte hızlı başlangıç makalelerine bakın.

    > [!NOTE]
    > Sanal ağ kurulumu sırasında, Microsoft 'a ağ eşlemesi ile ExpressRoute kullanırsanız, hizmetin sağlanacağı alt ağa aşağıdaki hizmet [uç noktalarını](../virtual-network/virtual-network-service-endpoints-overview.md) ekleyin:
    >
    > * Hedef veritabanı uç noktası (örneğin, SQL uç noktası, Cosmos DB uç noktası vb.)
    > * Depolama uç noktası
    > * Service Bus uç noktası
    >
    > Azure veritabanı geçiş hizmeti internet bağlantısı olmadığından bu yapılandırma gereklidir.

* Sanal ağınız için ağ güvenlik grubu (NSG) kurallarının ServiceBus, Storage ve AzureMonitor için ServiceTag giden bağlantı noktası 443 ' i engellemediğinden emin olun. Sanal ağ NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/virtual-network-vnet-plan-design-arm.md)makalesine bakın.
* [Windows Güvenlik Duvarınızı veritabanı altyapısı erişimi](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) için yapılandırın.
* Azure veritabanı geçiş hizmeti 'nin kaynak PostgreSQL sunucusuna erişmesine izin vermek için Windows Güvenlik duvarınızı açın, varsayılan olarak TCP bağlantı noktası 5432 ' dir.
* Kaynak veritabanlarınızın önünde bir güvenlik duvarı cihazı kullanıyorsanız, Azure Veritabanı Geçiş Hizmeti'nin geçiş amacıyla kaynak veritabanlarına erişmesi için güvenlik duvarı kuralları eklemeniz gerekebilir.
* Azure veritabanı geçiş hizmeti 'nin hedef veritabanlarına erişmesine izin vermek için PostgreSQL için Azure veritabanı için sunucu düzeyinde bir [güvenlik duvarı kuralı](../postgresql/concepts-firewall-rules.md) oluşturun. Azure veritabanı geçiş hizmeti için kullanılan sanal ağın alt ağ aralığını belirtin.
* postgresql.config dosyasında mantıksal çoğaltmayı etkinleştirin ve aşağıdaki parametreleri ayarlayın:

  * wal_level = **logical**
  * max_replication_slots = [yuva sayısı], **beş yuva** ayarı önerilir
  * max_wal_senders =[eşzamanlı görev sayısı] - max_wal_senders parametresi, çalışabilecek eşzamanlı görevlerin sayısını ayarlar; önerilen ayar **10 görevdir**

> [!IMPORTANT]
> Mevcut veritabanınızdaki tüm tabloların, değişikliklerin hedef veritabanıyla eşitlendiğinden emin olmak için bir birincil anahtar gerekir.

## <a name="migrate-the-sample-schema"></a>Örnek şemayı geçirme

Tablo şemaları, dizinler ve saklı yordamlar gibi tüm veritabanı nesnelerini tamamlamak için kaynak veritabanındaki şemayı ayıklamamız ve veritabanına uygulamamız gerekir.

1. Bir veritabanına yönelik şema döküm dosyası oluşturmak için pg_dump -s komutunu kullanın.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Örneğin, **dvdkiralık** veritabanı için bir şema dökümü dosyası oluşturmak için:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    pg_dump yardımcı programını kullanma hakkında daha fazla bilgi için [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) öğreticisindeki örneklere bakın.

2. Hedef ortamınız olan PostgreSQL için Azure Veritabanı içinde boş bir veritabanı oluşturun.

    Veritabanına bağlanma ve oluşturma hakkında daha fazla bilgi için, [Azure Portal bir PostgreSQL Için Azure veritabanı sunucusu oluşturma](../postgresql/quickstart-create-server-database-portal.md) veya [Azure Portal bir PostgreSQL Için Azure veritabanı oluşturma-hiper ölçek (Citus) sunucusu oluşturma](../postgresql/quickstart-create-hyperscale-portal.md)makalesine bakın.

    > [!NOTE]
    > PostgreSQL için Azure veritabanı 'nın (Citus) bir örneği yalnızca tek bir veritabanına sahiptir: **Citus**.

3. Şema döküm dosyasını geri yükleyerek, şemayı oluşturduğunuz hedef veritabanına aktarın.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Örnek:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Bırakma yabancı anahtar betiğini ayıklamak ve hedefe eklemek için (PostgreSQL için Azure veritabanı), PgAdmin veya psql 'de aşağıdaki betiği çalıştırın.

   > [!IMPORTANT]
   > Şemadaki yabancı anahtarlar, geçişin ilk yükünün ve sürekli eşitlenmesi başarısız olur.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Sorgu sonucunda bırakma yabancı anahtarını (ikinci sütun) çalıştırın.

6. Hedef veritabanında Tetikleyicileri devre dışı bırakmak için aşağıdaki betiği çalıştırın.

   > [!IMPORTANT]
   > Verilerdeki Tetikleyiciler (ekleme veya güncelleştirme), kaynaktan çoğaltılan verilerin önünde bulunan veri bütünlüğünü uygular. Sonuç olarak, geçiş sırasında **Hedefteki** tüm tablolardaki Tetikleyicileri devre dışı bırakmanız ve ardından geçiş tamamlandıktan sonra Tetikleyicileri yeniden etkinleştirmeniz önerilir.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration kaynak sağlayıcısını kaydetme

1. Azure portal'da oturum açın, **Tüm hizmetler**'i ve ardından **Abonelikler**'i seçin.

   ![Portal aboneliklerini gösterme](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve ardından **kaynak sağlayıcıları**' nı seçin.

    ![Kaynak sağlayıcılarını gösterme](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. "migration" araması yapın ve **Microsoft.DataMigration** öğesinin sağ tarafındaki **Kaydet**'i seçin.

    ![Kaynak sağlayıcısını kaydetme](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS örneği oluşturma

1. Azure portal + **kaynak oluştur**' u seçin, Azure veritabanı geçiş hizmeti ' ni arayın ve ardından açılan listeden **Azure veritabanı geçiş hizmeti** ' ni seçin.

    ![Azure Market](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. **Geçiş hizmeti oluştur** ekranında, bir ad, abonelik, yeni veya mevcut bir kaynak grubu ve hizmetin konumunu belirtin.

4. Var olan bir sanal ağı seçin veya yeni bir ağ oluşturun.

    Sanal ağ, Azure veritabanı geçiş hizmeti 'ni kaynak PostgreSQL sunucusuna ve hedef Azure Database for PostgreSQL örneğine erişimi sağlar.

    Azure portal sanal ağ oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/quick-create-portal.md)makalesine bakın.

5. Fiyatlandırma katmanını seçin.

    Maliyetler ve fiyatlandırma katmanları hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://aka.ms/dms-pricing) bakın.

    ![Azure Veritabanı Geçiş Hizmeti örneği ayarlarını yapılandırma](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Hizmeti oluşturmak için **gözden geçir + oluştur** ' u seçin.

   Hizmet oluşturma, yaklaşık 10 ila 15 dakika içinde tamamlanır.

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Hizmet oluşturulduktan sonra Azure portaldan bulun, açın ve yeni bir geçiş projesi oluşturun.

1. Azure portalda **Tüm hizmetler**'i seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

      ![Azure veritabanı geçiş hizmeti 'nin tüm örneklerini bulun](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. **Azure veritabanı geçiş Hizmetleri** ekranında, oluşturduğunuz Azure veritabanı geçiş hizmeti örneğinin adını arayın, örneği seçin ve + **yeni geçiş projesi**' ni seçin.

3. **Yeni geçiş projesi** ekranında, proje için bir ad belirtin, **kaynak sunucu türü** metin kutusunda **PostgreSQL**' i seçin, **hedef sunucu türü** metin kutusunda, **PostgreSQL için Azure veritabanı**' nı seçin.

4. **Etkinlik türünü seçin** bölümünde **çevrimiçi veri geçişi**' ni seçin.

    ![Azure veritabanı geçiş hizmeti projesi oluştur](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Alternatif olarak, şimdi **proje oluştur** ' u seçerek geçiş projesini hemen oluşturabilir ve geçişi daha sonra yürütebilirsiniz.

5. **Kaydet**' i seçin, verileri geçirmek Için Azure veritabanı geçiş hizmetini başarılı bir şekilde kullanmaya yönelik gereksinimlere göz önünde ve sonra **etkinlik oluştur ve Çalıştır**' ı seçin.

## <a name="specify-source-details"></a>Kaynak ayrıntılarını belirtme

1. **Kaynak ayrıntıları Ekle** ekranında, kaynak PostgreSQL örneği için bağlantı ayrıntılarını belirtin.

    ![Kaynak Ayrıntıları Ekleyin ekranı](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. **Kaydet**’i seçin.

## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Hedef Ayrıntılar** ekranında, **DVD randevu** şemasının pg_dump kullanılarak dağıtıldığı önceden sağlanan hiper ölçek (Citus) örneği olan hedef hiper ölçek (Citus) sunucusu için bağlantı ayrıntılarını belirtin.

    ![Hedef ayrıntıları ekranı](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. **Kaydet**'i seçin ve **Hedef veritabanlarıyla eşleyin** ekranında geçiş yapılacak kaynak ve hedef veritabanlarını eşleyin.

    Hedef veritabanı, kaynak veritabanıyla aynı veritabanı adını içeriyorsa, Azure veritabanı geçiş hizmeti varsayılan olarak hedef veritabanını seçer.

    ![Hedef veritabanlarına eşle ekranı](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. **Kaydet**' i seçin ve ardından **geçiş ayarları** ekranında varsayılan değerleri kabul edin.

    ![Geçiş ayarları ekranı](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. **Kaydet**'i seçin, **Geçiş özeti** ekranındaki **Etkinlik adı** metin kutusunda geçiş etkinliği için bir ad belirtin ve ardından, kaynak ve hedef ayrıntılarının önceden belirttiğiniz ayrıntılarla eşleştiğinden emin olmak üzere özeti gözden geçirin.

    ![Geçiş Özeti ekranı](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Geçişi çalıştırma

* **Geçişi çalıştır**'ı seçin.

    Geçiş etkinliği penceresi görünür ve **devam eden yedekleme** olarak göstermek Için etkinliğin **durumu** güncellemelidir.

## <a name="monitor-the-migration"></a>Geçişi izleme

1. Geçiş etkinliği ekranında **Yenile**'yi seçerek, gösterilen verileri, geçişin **Durum** bilgisi **Tamamlandı** olana kadar güncelleştirebilirsiniz.

     ![Geçiş işlemini izleme](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Geçiş tamamlandığında, **veritabanı adı** altında, **tam veri yükü** ve **artımlı veri eşitleme** işlemleri için geçiş durumuna ulaşmak üzere belirli bir veritabanını seçin.

   > [!NOTE]
   > **Tam veri yükleme** , ilk yük geçiş durumunu gösterir, ancak **artımlı veri eşitleme** , DEĞIŞIKLIK verilerini yakalama (CDC) durumunu gösterir.

     ![Tam veri yükleme ayrıntıları](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Artımlı veri eşitleme ayrıntıları](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Tam geçiş gerçekleştirme

İlk Tam yük tamamlandıktan sonra, veritabanları **Geçiş için hazır** olarak işaretlenir.

1. Veritabanı geçişini tamamlamaya hazır olduğunuzda **Tam Geçişi Başlat** seçeneğini belirleyin.

2. Kaynak veritabanındaki tüm gelen işlemlerin durdurulduğundan emin olmak için **bekleyen değişiklikler** sayacı **0** ' ı gösterene kadar bekleyin, **Onayla** onay kutusunu seçin ve ardından **Uygula**' yı seçin.

    ![Tam geçişi ekranını doldurun](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Veritabanı geçiş durumu **tamamlandı** olarak görüntülendiğinde, [dizileri yeniden oluşturun](https://wiki.postgresql.org/wiki/Fixing_Sequences) (varsa) ve uygulamalarınızı PostgreSQL için Azure veritabanı 'nın yeni hedef örneğine bağlayın.

## <a name="next-steps"></a>Sonraki adımlar

* PostgreSQL için Azure Veritabanı'na yönelik çevrimiçi geçiş gerçekleştirirken karşılaşılan bilinen sorunlar ve sınırlamalar hakkında bilgi için [PostgreSQL için Azure Veritabanı geçiş işlemleri ile ilgili bilinen sorunlar ve geçici çözümler](known-issues-azure-postgresql-online.md) başlıklı makaleye bakın.
* Azure Veritabanı Geçiş Hizmeti hakkında bilgi için [What is the Azure Database Migration Service? (Azure Veritabanı Geçiş Hizmeti nedir?)](./dms-overview.md) başlıklı makaleye bakın.
* PostgreSQL için Azure veritabanı hakkında daha fazla bilgi için [PostgreSQL Için Azure veritabanı nedir?](../postgresql/overview.md)makalesine bakın.