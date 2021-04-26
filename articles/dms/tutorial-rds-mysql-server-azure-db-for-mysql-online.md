---
title: "Öğretici: RDS MySQL 'i MySQL için Azure veritabanı 'na geçirme"
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti 'ni kullanarak RDS MySQL 'ten MySQL için Azure veritabanı 'na çevrimiçi geçiş gerçekleştirmeyi öğrenin.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 06/09/2020
ms.openlocfilehash: 13095824122f1d4b59ecde445efa1ea8bfa60dcd
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063585"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Öğretici: DMS hizmetini kullanarak RDS MySQL'i MySQL için Azure Veritabanı'na çevrimiçi geçirme

Azure veritabanı geçiş hizmeti 'ni, kaynak veritabanı geçiş sırasında çevrimiçi kaldığı sürece bir RDS MySQL örneğinden [MySQL Için Azure veritabanı](../mysql/index.yml) 'na geçirmek için kullanabilirsiniz. Diğer bir deyişle, geçişe uygulamanın en az kapalı kalma süresi ile ulaşılabilecek. Bu öğreticide, Azure veritabanı geçiş hizmeti 'ndeki çevrimiçi geçiş etkinliğini kullanarak **çalışanlar** örnek VERITABANıNı bir RDS MySQL örneğinden MySQL Için Azure veritabanı 'na geçireceğiniz.

> [!IMPORTANT]
> "MySQL için Azure veritabanı 'na RDS MySQL" çevrimiçi geçiş senaryosu, 1 Haziran 2021 ' de bulunan paralel ve yüksek performanslı bir çevrimdışı geçiş senaryosu ile değiştiriliyor. Çevrimiçi geçişler için, bu yeni teklifi, [veri ile çoğaltma](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)ile birlikte kullanabilirsiniz. Alternatif olarak, çevrimiçi geçişler için veri-çoğaltma ile [Mydumper/MyLoader](https://centminmod.com/mydumper.html) gibi açık kaynaklı araçları kullanın. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> * Mysqldump ve MySQL yardımcı programlarını kullanarak örnek şemayı geçirin.
> * Azure Veritabanı Geçiş Hizmeti örneği oluşturun.
> * Azure veritabanı geçiş hizmeti 'ni kullanarak bir geçiş projesi oluşturun.
> * Geçişi çalıştırma.
> * Geçişi izleme.

> [!NOTE]
> Çevrimiçi bir geçiş gerçekleştirmek için Azure veritabanı geçiş hizmeti 'nin kullanılması, Premium fiyatlandırma katmanını temel alan bir örnek oluşturulmasını gerektirir. Daha fazla bilgi için bkz. Azure veritabanı geçiş hizmeti [fiyatlandırma](https://azure.microsoft.com/pricing/details/database-migration/) sayfası.

> [!IMPORTANT]
> En iyi geçiş deneyimi için Microsoft, Azure Veritabanı Geçiş Hizmeti’nin bir örneğini hedef veritabanıyla aynı Azure bölgesinde oluşturmayı önerir. Verileri bölgeler veya coğrafyalar arasında taşımak, geçiş sürecini yavaşlatabilir ve hatalara neden olabilir.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Bu makalede, bir RDS MySQL örneğinden MySQL için Azure veritabanı 'na çevrimiçi geçiş gerçekleştirme işlemi açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

* Kaynak MySQL sunucusunun desteklenen bir MySQL Community sürümü çalıştırdığından emin olun. MySQL örneğinizin sürümünü öğrenmek için MySQL yardımcı programı veya MySQL çalışma ekranı 'nda komutunu çalıştırın:

    ```
    SELECT @@version;
    ```

    Daha fazla bilgi için bkz. [MySQL Için Azure veritabanı sürümleri desteği](../mysql/concepts-supported-versions.md).

* [MySQL **çalışanları** örnek veritabanını](https://dev.mysql.com/doc/employee/en/employees-installation.html)indirin ve yükleyin.
* [MySQL Için Azure veritabanı](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)'nın bir örneğini oluşturun.
* [ExpressRoute](../expressroute/expressroute-introduction.md) veya [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti için bir Microsoft Azure sanal ağ oluşturun. Sanal ağ oluşturma hakkında daha fazla bilgi için [sanal ağ belgelerine](../virtual-network/index.yml)ve özellikle adım adım ayrıntılarla birlikte hızlı başlangıç makalelerine bakın.
* Sanal ağ ağ güvenlik grubu kurallarınızın ServiceBus, Storage ve AzureMonitor için ServiceTag giden bağlantı noktası 443 ' i engellemediğinden emin olun. Sanal ağ NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/virtual-network-vnet-plan-design-arm.md)makalesine bakın.
* Veritabanı altyapısı erişimine izin vermek için [Windows Güvenlik duvarınızı](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (veya Linux Güvenlik duvarınızı) yapılandırın. MySQL Server için bağlantı noktası 3306 ' ya bağlantı için izin verin.

> [!NOTE]
> MySQL için Azure veritabanı yalnızca InnoDB tablolarını destekler. MyISAM tablolarını InnoDB 'e dönüştürmek için lütfen [MyISAM 'Den InnoDB 'e tabloları dönüştürme](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) makalesine bakın.

### <a name="set-up-aws-rds-mysql-for-replication"></a>Çoğaltma için AWS RDS MySQL ayarlama

1. Yeni bir parametre grubu oluşturmak için, [MySQL veritabanı günlük dosyaları](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)makalesindeki, **ikili günlük biçimi** bölümünde AWS tarafından belirtilen yönergeleri izleyin.
2. Aşağıdaki yapılandırmayla yeni bir parametre grubu oluşturun:
    * log_bin = açık
    * binlog_format = row
    * binlog_checksum = yok
3. Yeni parametre grubunu kaydedin.
4. Yeni parametre grubunu RDS MySQL örneğiyle ilişkilendirin. Yeniden başlatma gerekebilir.
5. Parametre grubu yerleştirildikten sonra MySQL örneğine bağlanın ve [binlog saklama](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_set_configuration.html#mysql_rds_set_configuration-usage-notes.binlog-retention-hours) süresini en az 5 güne ayarlayın.
```
call mysql.rds_set_configuration('binlog retention hours', 120);
```

## <a name="migrate-the-schema"></a>Şemayı geçirme

1. Şemayı kaynak veritabanından ayıklayın ve tablo şemaları, dizinler ve saklı yordamlar gibi tüm veritabanı nesnelerinin geçişini gerçekleştirmek için hedef veritabanına uygulayın.

    Yalnızca şemayı geçirmenin en kolay yolu, mysqldump for-Data parametresiyle birlikte kullanılır. Şemayı geçirme komutu:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Örneğin, **çalışanlar** veritabanı için bir şema dosyasının dökümünü yapmak için aşağıdaki komutu kullanın:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Şemayı, MySQL için Azure veritabanı olan Target Service 'e aktarın. Şema dökümü dosyasını geri yüklemek için şu komutu çalıştırın:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Örneğin, **çalışanlar** veritabanının şemasını içeri aktarmak için:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Şemanızda yabancı anahtarlar varsa geçişe ilişkin ilk yük ve sürekli eşitleme başarısız olur. Bırakma yabancı anahtar betiğini ayıklamak ve hedefte yabancı anahtar betiği eklemek için (MySQL için Azure veritabanı), MySQL çalışma ekranında aşağıdaki betiği çalıştırın:

    ```
    SET group_concat_max_len = 8192;
        SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
        FROM
        (SELECT
        KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
                    KCU.TABLE_NAME,
                    KCU.COLUMN_NAME,
                    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
                    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
                    WHERE
                      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
                      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Yabancı anahtarı bırakmak için sorgu sonucunda bırakma yabancı anahtarını (ikinci sütun) çalıştırın.

> [!NOTE]
> Azure DMS, üst tabloda bir satır silindiğinde veya güncelleştirilirse alt tablodaki eşleşen bir satırı otomatik olarak silmeye veya güncelleştirmeye yardımcı olan CASCADE başvuru eylemini desteklemez. Daha fazla bilgi için MySQL belgelerinde [yabancı anahtar kısıtlamaları](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)makalesinin başvurusal Eylemler bölümüne bakın.
> Azure DMS, ilk veri yükü sırasında hedef veritabanı sunucusunda yabancı anahtar kısıtlamalarını bırakmayı gerektirir ve başvurusal eylemleri kullanamazsınız. İş yükünüz bu başvuru eylemi aracılığıyla ilişkili bir alt tablonun güncelleştirilmesine bağımlıysa bunun yerine bir [döküm ve geri yükleme](../mysql/concepts-migrate-dump-restore.md) gerçekleştirmenizi öneririz. 

5. Verilerde Tetikleyiciler (ekleme veya güncelleştirme tetikleyicisi) varsa, verileri kaynaktan çoğaltmadan önce hedefte veri bütünlüğünü zorunlu kılar. Bu öneri, geçiş sırasında *Hedefteki* tüm tablolardaki Tetikleyicileri devre dışı bırakmak ve geçiş tamamlandıktan sonra Tetikleyicileri etkinleştirir.

    Hedef veritabanında Tetikleyicileri devre dışı bırakmak için:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Herhangi bir tabloda sabit listesi veri türü örnekleri varsa, hedef tablodaki ' karakter değişen ' veri türüne geçici olarak güncelleştirme yapmanız önerilir. Veri çoğaltma işlemi tamamlandığında, veri türünü sabıt listesine döndürür.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration kaynak sağlayıcısını kaydetme

1. Azure portal'da oturum açın, **Tüm hizmetler**'i ve ardından **Abonelikler**'i seçin.

   ![Portal aboneliklerini gösterme](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve ardından **kaynak sağlayıcıları**' nı seçin.

    ![Kaynak sağlayıcılarını gösterme](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. "migration" araması yapın ve **Microsoft.DataMigration** öğesinin sağ tarafındaki **Kaydet**'i seçin.

    ![Kaynak sağlayıcısını kaydetme](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Veritabanı Geçiş Hizmeti'nin örneğini oluşturma

1. Azure portal + **kaynak oluştur**' u seçin, Azure veritabanı geçiş hizmeti ' ni arayın ve ardından açılan listeden **Azure veritabanı geçiş hizmeti** ' ni seçin.

    ![Azure Market](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. **Geçiş Hizmeti oluşturun** ekranında hizmet için bir ad belirtin, aboneliği ve yeni ya da var olan bir kaynak grubunu seçin.

4. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz konumu seçin.

5. Var olan bir sanal ağı seçin veya yeni bir ağ oluşturun.

    Sanal ağ, Azure veritabanı geçiş hizmeti 'ne kaynak MySQL örneğine ve hedef Azure veritabanı için Azure veritabanı 'na erişim sağlar.

    Azure portal sanal ağ oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/quick-create-portal.md)makalesine bakın.

6. Fiyatlandırma katmanı seçin; Bu çevrimiçi geçiş için Premium: 4Vçekirdekleri fiyatlandırma katmanını seçtiğinizden emin olun.

    ![Azure Veritabanı Geçiş Hizmeti örneği ayarlarını yapılandırma](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Hizmeti oluşturmak için **Oluştur**’u seçin.

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Hizmet oluşturulduktan sonra Azure portaldan bulun, açın ve yeni bir geçiş projesi oluşturun.

1. Azure portalda **Tüm hizmetler**'i seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

      ![Azure Veritabanı Geçiş Hizmeti’nin tüm örneklerini bulma](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında oluşturduğunuz Azure Veritabanı Geçiş Hizmeti örneğinin adını arayın ve sonuçlardan bu örneği seçin.

     ![Azure Veritabanı Geçiş Hizmeti örneğinizi bulma](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. +**Yeni Geçiş Projesi**'ni seçin.
4. **Yeni geçiş projesi** ekranında, proje için bir ad belirtin, **kaynak sunucu türü** metin kutusunda **MySQL**' i seçin ve **hedef sunucu türü** metin kutusunda **AzureDbForMySQL**' yi seçin.
5. **Etkinlik türünü seçin** bölümünde **çevrimiçi veri geçişi**' ni seçin.

    > [!IMPORTANT]
    > **Çevrimiçi veri geçişi**' ni seçtiğinizden emin olun; Bu senaryo için çevrimdışı geçişler desteklenmez.

    ![Veritabanı Geçiş Hizmeti Projesi Oluşturma](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Alternatif olarak, şimdi **proje oluştur** ' u seçerek geçiş projesini hemen oluşturabilir ve geçişi daha sonra yürütebilirsiniz.

6. **Kaydet**’i seçin.

7. Projeyi oluşturmak ve geçiş etkinliğini çalıştırmak için **Etkinlik oluştur ve çalıştır**'ı seçin.

    > [!NOTE]
    > Lütfen proje oluşturma dikey penceresinde çevrimiçi geçiş ayarlamak için gereken önkoşulları unutmayın.

## <a name="specify-source-details"></a>Kaynak ayrıntılarını belirtme

* **Geçiş kaynağı ayrıntısı** ekranında, kaynak MySQL örneği için bağlantı ayrıntılarını belirtin.

   ![Kaynak Ayrıntıları](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Hedef ayrıntılarını belirtme

1. **Kaydet**' i seçin ve ardından **hedef ayrıntıları** ekranında, önceden sağlanmış olan MySQL için hedef Azure veritabanı için bağlantı ayrıntılarını belirtin ve mysqldump kullanılarak **çalışanlar** şeması dağıtılır.

    ![Hedef seçme](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. **Kaydet**'i seçin ve **Hedef veritabanlarıyla eşleyin** ekranında geçiş yapılacak kaynak ve hedef veritabanlarını eşleyin.

    Hedef veritabanı, kaynak veritabanıyla aynı veritabanı adını içeriyorsa, Azure veritabanı geçiş hizmeti varsayılan olarak hedef veritabanını seçer.

    ![Hedef veritabanlarıyla eşleyin](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. **Kaydet**'i seçin, **Geçiş özeti** ekranındaki **Etkinlik adı** metin kutusunda geçiş etkinliği için bir ad belirtin ve ardından, kaynak ve hedef ayrıntılarının önceden belirttiğiniz ayrıntılarla eşleştiğinden emin olmak üzere özeti gözden geçirin.

    ![Geçiş Özeti](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Geçişi çalıştırma

* **Geçişi çalıştır**'ı seçin.

    Geçiş etkinliği penceresi görünür ve etkinliğin **durumu** **başlatılıyor**.

## <a name="monitor-the-migration"></a>Geçişi izleme

1. Geçiş etkinliği ekranında **Yenile**'yi seçerek, gösterilen verileri, geçişin **Durum** bilgisi **Çalıştırılıyor** olana kadar güncelleştirebilirsiniz.

    ![Etkinlik durumu-çalışıyor](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. **VERITABANı adı** altında, **tam veri yükü** ve **artımlı veri eşitleme** işlemleri için geçiş durumuna ulaşmak üzere belirli bir veritabanını seçin.

    **Tam veri yükleme** , ilk yük geçiş durumunu gösterir, ancak **artımlı veri eşitleme** , DEĞIŞIKLIK verilerini yakalama (CDC) durumunu gösterir.

    ![Envanter ekranı-tam veri yükleme](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Envanter ekranı-artımlı veri eşitleme](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Tam geçiş gerçekleştirme

İlk tam yükleme tamamlandıktan sonra veritabanları **cutover 'e başlamaya** göre işaretlenir.

1. Veritabanı geçişini tamamlamaya hazır olduğunuzda **Tam Geçişi Başlat** seçeneğini belirleyin.

    ![Kesmeyi Başlat](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. **Bekleyen değişiklikler** sayacı **0** değerini gösterene kadar bekleyerek kaynak veritabanına gelen tüm işlemleri durdurduğunuzdan emin olun.
3. **Onayla**'yı ve ardından, **Uygula**'yı seçin.
4. Veritabanı geçiş durumu **tamamlandı** olarak görüntülendiğinde, uygulamalarınızı yeni hedef MySQL veritabanı Için Azure veritabanı 'na bağlayın.

MySQL için Azure veritabanı 'na yönelik şirket içi bir MySQL örneğini çevrimiçi geçişiniz artık tamamlanmıştır.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Veritabanı Geçiş Hizmeti hakkında bilgi için [What is the Azure Database Migration Service? (Azure Veritabanı Geçiş Hizmeti nedir?)](./dms-overview.md) başlıklı makaleye bakın.
* MySQL için Azure veritabanı hakkında daha fazla bilgi için, [MySQL Için Azure veritabanı nedir?](../mysql/overview.md)makalesine bakın.
* Diğer sorular için [Azure veritabanı geçişleri](mailto:AskAzureDatabaseMigrations@service.microsoft.com) diğer adına e-posta gönderin.
