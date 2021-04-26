---
title: "Öğretici: Azure portal aracılığıyla PostgreSQL için Azure DB 'yi PostgreSQL için Azure DB 'ye geçirme"
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti Azure portal aracılığıyla bir PostgreSQL için Azure DB 'den başka bir PostgreSQL için Azure veritabanı 'na çevrimiçi geçiş gerçekleştirmeyi öğrenin.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 07/21/2020
ms.openlocfilehash: 996523d507f0a4f2850e936df39a38769bc47cde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091299"
---
# <a name="tutorial-migrateupgrade-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server--online-using-dms-via-the-azure-portal"></a>Öğretici: PostgreSQL için Azure DB 'yi geçirme/yükseltme-tek sunucudan PostgreSQL için Azure VERITABANı-tek sunuculu, Azure portal aracılığıyla DMS kullanarak tek sunuculu bir çevrimiçi

Azure veritabanı geçiş hizmeti 'ni, [PostgreSQL Için Azure veritabanı-tek sunuculu](../postgresql/overview.md#azure-database-for-postgresql---single-server) bir SQL örneğinden, PostgreSQL için Azure veritabanı 'nın aynı veya farklı bir sürümüne, örneğin, PostgreSQL için Azure veritabanı 'na (en az kapalı kalma süresi Ile) esnek sunucuya geçirmek için kullanabilirsiniz. Bu öğreticide, Azure veritabanı geçiş hizmeti 'ndeki çevrimiçi geçiş etkinliğini kullanarak PostgreSQL için Azure veritabanı ile v10 arasındaki ' dan PostgreSQL için Azure veritabanı 'na (tek sunucu) ait **DVD Kiralama** örneği veritabanını geçireceğiniz.

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

> [!IMPORTANT]
> PostgreSQL için Azure veritabanından geçiş, PostgreSQL sürüm 10 ve üzeri için desteklenir. Bu öğreticiyi, bir PostgreSQL için Azure veritabanı örneğini PostgreSQL için başka bir Azure veritabanı örneğine veya Hyperscale (Citus) örneğine geçirmek için de kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

* Desteklenen geçiş ve sürüm birleşimleri için [Azure veritabanı geçiş hizmeti tarafından desteklenen geçiş senaryolarının durumunu](./resource-scenario-status.md) denetleyin. 
* [PostgreSQL için mevcut bir Azure veritabanı](../postgresql/index.yml) sürüm 10 ve daha sonraki bir örnek, **DVD Kiralama** veritabanıdır. 

    Ayrıca, PostgreSQL için hedef Azure veritabanının, şirket içi PostgreSQL sürümüne eşit veya ondan daha yeni olması gerektiğini unutmayın. Örneğin PostgreSQL 10, PostgreSQL için Azure veritabanı 10 veya 11 ' e geçiş yapabilir, ancak PostgreSQL için Azure veritabanı 9,6 ' e geçirilemez.

* [PostgreSQL Için Azure veritabanı sunucusu oluşturun](../postgresql/quickstart-create-server-database-portal.md) veya veri geçirilecek hedef veritabanı sunucusu olarak [PostgreSQL için Azure veritabanı-Hyperscale (Citus) sunucusu oluşturun](../postgresql/quickstart-create-hyperscale-portal.md) .
* Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti için bir Microsoft Azure Sanal Ağ oluşturun. Sanal ağ oluşturma hakkında daha fazla bilgi için [sanal ağ belgelerine](../virtual-network/index.yml)ve özellikle adım adım ayrıntılarla birlikte hızlı başlangıç makalelerine bakın.

* Sanal ağınız için ağ güvenlik grubu (NSG) kurallarının ServiceBus, Storage ve AzureMonitor için ServiceTag giden bağlantı noktası 443 ' i engellemediğinden emin olun. Sanal ağ NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/virtual-network-vnet-plan-design-arm.md)makalesine bakın.
* Azure veritabanı geçiş hizmeti 'nin kaynak veritabanlarına erişmesine izin vermek için PostgreSQL için Azure veritabanı kaynağı için sunucu düzeyinde bir [güvenlik duvarı kuralı](../azure-sql/database/firewall-configure.md) oluşturun. Azure veritabanı geçiş hizmeti için kullanılan sanal ağın alt ağ aralığını belirtin.
* Azure veritabanı geçiş hizmeti 'nin hedef veritabanlarına erişmesine izin vermek için PostgreSQL için Azure veritabanı hedefi için sunucu düzeyinde bir [güvenlik duvarı kuralı](../azure-sql/database/firewall-configure.md) oluşturun. Azure veritabanı geçiş hizmeti için kullanılan sanal ağın alt ağ aralığını belirtin.
* PostgreSQL için Azure DB kaynağı 'nda [mantıksal çoğaltmayı etkinleştirin](../postgresql/concepts-logical.md) . 
* Kaynak olarak kullanılan PostgreSQL için Azure veritabanı örneğinde aşağıdaki sunucu parametrelerini ayarlayın:

  * max_replication_slots = [yuva sayısı], **on yuva** olarak ayarlama önerilir
  * max_wal_senders =[eşzamanlı görev sayısı] - max_wal_senders parametresi, çalışabilecek eşzamanlı görevlerin sayısını ayarlar; önerilen ayar **10 görevdir**

> [!NOTE]
> Yukarıdaki sunucu parametreleri statiktir ve bu nesnelerin etkili olabilmesi için PostgreSQL için Azure veritabanı örneğinin yeniden başlatılmasını gerektirir. Sunucu parametrelerini değiştirme hakkında daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı sunucu parametrelerini yapılandırma](../postgresql/howto-configure-server-parameters-using-portal.md).

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
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
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
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Bırakma yabancı anahtar betiğini ayıklamak ve hedefe eklemek için (PostgreSQL için Azure veritabanı), PgAdmin veya psql 'de aşağıdaki betiği çalıştırın.

   > [!IMPORTANT]
   > Şemadaki yabancı anahtarlar, geçişin ilk yükünün ve sürekli eşitlenmesi başarısız olur.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name ,'"', STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ','"', foreignkey,'"'), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name,'"', STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ','"', foreignkey,'"', ' FOREIGN KEY (','"', column_name,'"', ')', ' REFERENCES ','"', foreign_table_schema,'"', '.','"', foreign_table_name,'"', '(','"', foreign_column_name,'"', ')',' ON UPDATE ',update_rule,' ON DELETE ',delete_rule), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name,
        S.update_rule,
        S.delete_rule
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name,
        rc.update_rule AS update_rule,
        rc.delete_rule AS delete_rule
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
        JOIN information_schema.referential_constraints as rc ON rc.constraint_name = tc.constraint_name AND rc.constraint_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name,S.update_rule,S.delete_rule
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

   ![Portal aboneliklerini gösterme](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve ardından **kaynak sağlayıcıları**' nı seçin.

    ![Kaynak sağlayıcılarını gösterme](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. "migration" araması yapın ve **Microsoft.DataMigration** öğesinin sağ tarafındaki **Kaydet**'i seçin.

    ![Kaynak sağlayıcısını kaydetme](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS örneği oluşturma

1. Azure portal + **kaynak oluştur**' u seçin, Azure veritabanı geçiş hizmeti ' ni arayın ve ardından açılan listeden **Azure veritabanı geçiş hizmeti** ' ni seçin.

    ![Azure Market](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. **Geçiş hizmeti oluştur** ekranında, bir ad, abonelik, yeni veya mevcut bir kaynak grubu ve hizmetin konumunu belirtin.

4. Var olan bir sanal ağı seçin veya yeni bir ağ oluşturun.

    Sanal ağ, Azure veritabanı geçiş hizmeti 'ni kaynak PostgreSQL sunucusuna ve hedef Azure Database for PostgreSQL örneğine erişimi sağlar.

    Azure portal sanal ağ oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/quick-create-portal.md)makalesine bakın.

5. Fiyatlandırma katmanını seçin.

    Maliyetler ve fiyatlandırma katmanları hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://aka.ms/dms-pricing) bakın.

    ![Azure Veritabanı Geçiş Hizmeti örneği ayarlarını yapılandırma](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Hizmeti oluşturmak için **gözden geçir + oluştur** ' u seçin.

   Hizmet oluşturma, yaklaşık 10 ila 15 dakika içinde tamamlanır.

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Hizmet oluşturulduktan sonra Azure portaldan bulun, açın ve yeni bir geçiş projesi oluşturun.

1. Azure portalda **Tüm hizmetler**'i seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

      ![Azure veritabanı geçiş hizmeti 'nin tüm örneklerini bulun](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. **Azure veritabanı geçiş Hizmetleri** ekranında, oluşturduğunuz Azure veritabanı geçiş hizmeti örneğinin adını arayın, örneği seçin ve + **yeni geçiş projesi**' ni seçin.

3. **Yeni geçiş projesi** ekranında, proje için bir ad belirtin, **kaynak sunucu türü** metin kutusunda **PostgreSQL**' i seçin, **hedef sunucu türü** metin kutusunda, **PostgreSQL için Azure veritabanı**' nı seçin.
    > [!NOTE]
    > Kaynak sunucuda PostgreSQL **Için Azure veritabanı** örneği olmasına rağmen **kaynak sunucu türünde** **PostgreSQL** öğesini seçin.  

4. **Etkinlik türünü seçin** bölümünde **çevrimiçi veri geçişi**' ni seçin.

    ![Azure veritabanı geçiş hizmeti projesi oluştur](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Alternatif olarak, şimdi **proje oluştur** ' u seçerek geçiş projesini hemen oluşturabilir ve geçişi daha sonra yürütebilirsiniz.

5. **Kaydet**' i seçin, verileri geçirmek Için Azure veritabanı geçiş hizmetini başarılı bir şekilde kullanmaya yönelik gereksinimlere göz önünde ve sonra **etkinlik oluştur ve Çalıştır**' ı seçin.

## <a name="specify-source-details"></a>Kaynak ayrıntılarını belirtme

1. **Kaynak ayrıntıları Ekle** ekranında, kaynak PostgreSQL örneği için bağlantı ayrıntılarını belirtin.

    ![Kaynak Ayrıntıları Ekleyin ekranı](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > **PostgreSQL Için Azure veritabanı** portalına "sunucu adı", "sunucu bağlantı noktası", "veritabanı adı" vb. gibi ayrıntıları bulabilirsiniz.

2. **Kaydet**’i seçin.

## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Hedef Ayrıntılar** ekranında, **DVD randevu** şemasının pg_dump kullanılarak dağıtıldığı önceden sağlanan hiper ölçek (Citus) örneği olan hedef hiper ölçek (Citus) sunucusu için bağlantı ayrıntılarını belirtin.

    ![Hedef ayrıntıları ekranı](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > PostgreSQL için Azure veritabanı örneğinden bir PostgreSQL için Azure veritabanı 'na veya bir Hyperscale (Citus) sunucusuna geçiş yapabilirsiniz.

2. **Kaydet**'i seçin ve **Hedef veritabanlarıyla eşleyin** ekranında geçiş yapılacak kaynak ve hedef veritabanlarını eşleyin.

    Hedef veritabanı, kaynak veritabanıyla aynı veritabanı adını içeriyorsa, Azure veritabanı geçiş hizmeti varsayılan olarak hedef veritabanını seçer.

    ![Hedef veritabanlarına eşle ekranı](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. **Kaydet**' i seçin ve ardından **geçiş ayarları** ekranında varsayılan değerleri kabul edin.

    ![Geçiş ayarları ekranı](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. **Kaydet**'i seçin, **Geçiş özeti** ekranındaki **Etkinlik adı** metin kutusunda geçiş etkinliği için bir ad belirtin ve ardından, kaynak ve hedef ayrıntılarının önceden belirttiğiniz ayrıntılarla eşleştiğinden emin olmak üzere özeti gözden geçirin.

    ![Geçiş Özeti ekranı](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Geçişi çalıştırma

* **Geçişi çalıştır**'ı seçin.

Geçiş etkinliği penceresi görünür ve **devam eden yedekleme** olarak göstermek Için etkinliğin **durumu** güncellemelidir. PostgreSQL için Azure DB 9,5 veya 9,6 ' den yükseltirken aşağıdaki hatayla karşılaşabilirsiniz:

**Bir senaryo bilinmeyen bir hata bildirdi. 28000: "40.121.141.121" konağından çoğaltma bağlantısı için pg_hba. conf girdisi yok, Kullanıcı "SR"**

Bunun nedeni, PostgreSQL 'in gerekli mantıksal çoğaltma yapıtları oluşturmak için uygun ayrıcalıklara sahip olmaması olabilir. Gerekli ayrıcalıkları etkinleştirmek için şunları yapabilirsiniz:

1. Geçirme/yükseltme yapmaya çalıştığınız PostgreSQL için kaynak Azure DB sunucusu için "bağlantı güvenliği" ayarlarını açın.
2. "_Replrule" ile biten bir ada sahip yeni bir güvenlik duvarı kuralı ekleyin ve hata iletisinden IP adresini başlangıç IP 'si ve bitiş IP 'si alanlarına ekleyin. Yukarıdaki hata örneği için-
> Güvenlik duvarı kuralı adı = sr_replrule; Başlangıç IP 'si = 40.121.141.121; Bitiş IP 'si = 40.121.141.121

3. Kaydet ' e tıklayın ve değişikliğin tamamlanmasını sağlayın. 
4. DMS etkinliğini yeniden deneyin. 

## <a name="monitor-the-migration"></a>Geçişi izleme

1. Geçiş etkinliği ekranında **Yenile**'yi seçerek, gösterilen verileri, geçişin **Durum** bilgisi **Tamamlandı** olana kadar güncelleştirebilirsiniz.

     ![Geçiş işlemini izleme](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Geçiş tamamlandığında, **veritabanı adı** altında, **tam veri yükü** ve **artımlı veri eşitleme** işlemleri için geçiş durumuna ulaşmak üzere belirli bir veritabanını seçin.

   > [!NOTE]
   > **Tam veri yükleme** , ilk yük geçiş durumunu gösterir, ancak **artımlı veri eşitleme** , DEĞIŞIKLIK verilerini yakalama (CDC) durumunu gösterir.

     ![Tam veri yükleme ayrıntıları](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Artımlı veri eşitleme ayrıntıları](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Tam geçiş gerçekleştirme

İlk Tam yük tamamlandıktan sonra, veritabanları **Geçiş için hazır** olarak işaretlenir.

1. Veritabanı geçişini tamamlamaya hazır olduğunuzda **Tam Geçişi Başlat** seçeneğini belirleyin.

2. Kaynak veritabanındaki tüm gelen işlemlerin durdurulduğundan emin olmak için **bekleyen değişiklikler** sayacı **0** ' ı gösterene kadar bekleyin, **Onayla** onay kutusunu seçin ve ardından **Uygula**' yı seçin.

    ![Tam geçişi ekranını doldurun](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Veritabanı geçiş durumu **tamamlandı** olarak görüntülendiğinde, [dizileri yeniden oluşturun](https://wiki.postgresql.org/wiki/Fixing_Sequences) (varsa) ve uygulamalarınızı PostgreSQL için Azure veritabanı 'nın yeni hedef örneğine bağlayın.
 
> [!NOTE]
> Azure veritabanı geçiş hizmeti, PostgreSQL için Azure veritabanı-tek sunucu 'da azaltılmış kapalı kalma süresiyle büyük sürüm yükseltmeleri gerçekleştirmek için kullanılabilir. İlk olarak, istenen daha fazla PostgreSQL sürümü, ağ ayarları ve parametreleri ile bir hedef veritabanı yapılandırırsınız. Daha sonra, yukarıda açıklanan yordamı kullanarak hedef veritabanlarına geçişi başlatabilirsiniz. Hedef veritabanı sunucusuna geçiş yaptıktan sonra, uygulama Bağlantı dizenizi hedef veritabanı sunucusuna işaret etmek üzere güncelleştirebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

* PostgreSQL için Azure Veritabanı'na yönelik çevrimiçi geçiş gerçekleştirirken karşılaşılan bilinen sorunlar ve sınırlamalar hakkında bilgi için [PostgreSQL için Azure Veritabanı geçiş işlemleri ile ilgili bilinen sorunlar ve geçici çözümler](known-issues-azure-postgresql-online.md) başlıklı makaleye bakın.
* Azure Veritabanı Geçiş Hizmeti hakkında bilgi için [What is the Azure Database Migration Service? (Azure Veritabanı Geçiş Hizmeti nedir?)](./dms-overview.md) başlıklı makaleye bakın.
* PostgreSQL için Azure veritabanı hakkında daha fazla bilgi için [PostgreSQL Için Azure veritabanı nedir?](../postgresql/overview.md)makalesine bakın.
