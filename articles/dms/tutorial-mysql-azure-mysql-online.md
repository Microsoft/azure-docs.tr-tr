---
title: "Öğretici: MySQL için Azure veritabanı 'na MySQL online geçirme"
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti 'ni kullanarak şirket içi MySQL 'ten MySQL için Azure veritabanı 'na çevrimiçi geçiş gerçekleştirmeyi öğrenin.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 754d8cc9e79bc100e87f56c6fc33102963e53e8d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818197"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Öğretici: DMS hizmetini kullanarak çevrimiçi ortamda MySQL'i MySQL için Azure Veritabanı'na geçirme

Azure veritabanı geçiş hizmeti 'ni kullanarak veritabanlarını şirket içi bir MySQL örneğinden, en az kapalı kalma süresiyle [MySQL Için Azure veritabanı](../mysql/index.yml) 'na geçirebilirsiniz. Diğer bir deyişle, geçiş işlemi, uygulamada minimum çalışmama süresi ile gerçekleştirilebilir. Bu öğreticide, Azure veritabanı geçiş hizmeti 'nde çevrimiçi geçiş etkinliği kullanarak MySQL 5,7 ' in şirket içi bir örneğinden **çalışanlar** örnek veritabanını MySQL Için Azure veritabanı 'na geçireceğiniz.

> [!IMPORTANT]
> "MySQL için Azure veritabanı" çevrimiçi geçiş senaryosu, 1 Haziran 2021 ' de bulunan paralel ve yüksek performanslı bir çevrimdışı geçiş senaryosu ile değiştiriliyor. Çevrimiçi geçişler için, bu yeni teklifi, [veri ile çoğaltma](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)ile birlikte kullanabilirsiniz. Alternatif olarak, çevrimiçi geçişler için veri-çoğaltma ile [Mydumper/MyLoader](https://centminmod.com/mydumper.html) gibi açık kaynaklı araçları kullanın. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> * mysqldump yardımcı programını kullanarak örnek şemayı geçirme.
> * Azure Veritabanı Geçiş Hizmeti örneği oluşturun.
> * Azure veritabanı geçiş hizmeti 'ni kullanarak bir geçiş projesi oluşturun.
> * Geçişi çalıştırma.
> * Geçişi izleme.

> [!NOTE]
> Çevrimiçi bir geçiş gerçekleştirmek için Azure veritabanı geçiş hizmeti 'nin kullanılması, Premium fiyatlandırma katmanını temel alan bir örnek oluşturulmasını gerektirir.

> [!IMPORTANT]
> En iyi geçiş deneyimi için, Microsoft, hedef veritabanıyla aynı Azure bölgesinde Azure veritabanı geçiş hizmeti örneği oluşturulmasını önerir. Verileri bölgeler veya coğrafyalar arasında taşımak, geçiş sürecini yavaşlatabilir ve hatalara neden olabilir.

> [!NOTE]
> Sapma ücretsiz iletişim
>
> Microsoft, farklı ve üçlü ortamları destekler. Bu makale, _İkincil_ sözcüğe başvurular içerir. Kullanım açısından [ücretsiz iletişim Için Microsoft Stil Kılavuzu](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) bunu bir exclusionword olarak tanır. Bu makalede, şu anda yazılımda görüntülenen sözcük olduğundan, bu makalede tutarlılık için kullanılır. Yazılım, sözcüğü kaldıracak şekilde güncelleniyorsa, bu makale hizalamayla olacak şekilde güncelleştirilir.
>


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

* [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6 veya 5.7'yi indirme ve yükleme. Şirket içi MySQL sürümü ile MySQL için Azure Veritabanı sürümü eşleşmelidir. Örneğin, MySQL 5.6 yalnızca MySQL için Azure Veritabanı 5.6'ya geçirilebilir; 5.7 sürümüne yükseltilemez. MySQL 8,0 veya 'tan geçiş desteklenmez.
* [MySQL için Azure Veritabanı örneği oluşturma](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Çalışma ekranı uygulamasını kullanarak bir veritabanını bağlama ve oluşturma hakkındaki ayrıntıları öğrenmek için [MySQL çalışma ekranı kullanma makalesine başvurun ve verileri sorgulayın](../mysql/connect-workbench.md) .  
* [ExpressRoute](../expressroute/expressroute-introduction.md) veya [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti için bir Microsoft Azure sanal ağ oluşturun. Sanal ağ oluşturma hakkında daha fazla bilgi için [sanal ağ belgelerine](../virtual-network/index.yml)ve özellikle adım adım ayrıntılarla birlikte hızlı başlangıç makalelerine bakın.

    > [!NOTE]
    > Sanal networkNet kurulumu sırasında, Microsoft 'a ağ eşlemesi ile ExpressRoute kullanırsanız, hizmetin sağlanacağı alt ağa aşağıdaki hizmet [uç noktalarını](../virtual-network/virtual-network-service-endpoints-overview.md) ekleyin:
    >
    > * Hedef veritabanı uç noktası (örneğin, SQL uç noktası, Cosmos DB uç noktası vb.)
    > * Depolama uç noktası
    > * Service Bus uç noktası
    >
    > Azure veritabanı geçiş hizmeti internet bağlantısı olmadığından bu yapılandırma gereklidir.

* Sanal ağ ağ güvenlik grubu kurallarınızın ServiceBus, Storage ve AzureMonitor için ServiceTag giden bağlantı noktası 443 ' i engellemediğinden emin olun. Sanal ağ NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/virtual-network-vnet-plan-design-arm.md)makalesine bakın.
* [Windows Güvenlik Duvarınızı veritabanı altyapısı erişimi](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) için yapılandırın.
* Azure veritabanı geçiş hizmeti 'nin kaynak MySQL sunucusuna erişmesine izin vermek için Windows Güvenlik duvarınızı açın, varsayılan olarak TCP bağlantı noktası 3306 ' dir.
* Kaynak veritabanınızın önünde bir güvenlik duvarı gereci kullanırken, Azure veritabanı geçiş hizmeti 'nin geçiş için kaynak veritabanına erişmesine izin vermek üzere güvenlik duvarı kuralları eklemeniz gerekebilir.
* Azure veritabanı geçiş hizmeti 'nin hedef veritabanlarına erişmesine izin vermek için MySQL için Azure veritabanı 'nın sunucu düzeyinde bir [güvenlik duvarı kuralı](../azure-sql/database/firewall-configure.md) oluşturun. Azure veritabanı geçiş hizmeti için kullanılan sanal ağın alt ağ aralığını belirtin.
* Kaynak MySQL'in desteklenen MySQL Community Edition sürümünde olması gerekir. MySQL örneğinin sürümünü belirlemek için, MySQL yardımcı programında veya MySQL Workbench'te şu komutu çalıştırın:

    ```
    SELECT @@version;
    ```

* MySQL için Azure Veritabanı yalnızca InnoDB tablolarını destekler. MyISAM tablolarını InnoDB'ye dönüştürmek için [Converting Tables from MyISAM to InnoDB (Tabloları MyISAM'dan InnoDB'ye Dönüştürme)](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) başlıklı makaleye bakın

* Şu yapılandırmayı kullanarak kaynak veritabanındaki my.ini (Windows) veya my.cnf (Unix) dosyasında ikili günlük dosyası kaydetmeyi etkinleştirin:

  * **server_id** = 1 veya üzeri (yalnızca MySQL 5.6 için geçerlidir)
  * **günlük kayıt bölmesi** = \<path> (yalnızca MySQL 5,6 için geçerlidir)    Örneğin: log-bin = E:\ MySQL_logs \BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (sıfır kullanılması önerilir; yalnızca MySQL 5,6 için geçerlidir)
  * **Binlog_row_image** = full (yalnızca MySQL 5.6 için geçerlidir)
  * **log_slave_updates** = 1

* Kullanıcının, şu ayrıcalıkları içeren ReplicationAdmin rolüne sahip olması gerekir:

  * **ÇOĞALTMA İSTEMCİSİ** - Yalnızca değişiklik işleme görevleri için gereklidir. Başka bir deyişle, bu ayrıcalık yalnızca Tam Yük görevleri için gerekli değildir.
  * **ÇOĞALTMA ÇOĞALTMASI** - Yalnızca değişiklik işleme görevleri için gereklidir. Başka bir deyişle, bu ayrıcalık yalnızca Tam Yük görevleri için gerekli değildir.
  * **SÜPER** - yalnızca MySQL 5.6.6'dan önceki sürümlerde gereklidir.

## <a name="migrate-the-sample-schema"></a>Örnek şemayı geçirme

Tablo şemaları, dizinler ve saklı yordamlar gibi tüm veritabanı nesnelerini tamamlamak için kaynak veritabanındaki şemayı ayıklamamız ve veritabanına uygulamamız gerekir. Şemayı ayıklamak için `--no-data` parametresiyle mysqldump yardımcı programını kullanabilirsiniz.

MySQL **çalışanlarının** şirket içi sistemde örnek veritabanı olduğunu varsayarak, mysqldump kullanarak şema geçişi yapmak için komutu:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Örnek:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Şemayı MySQL için Azure Veritabanı hedefine aktarmak için şu komutu çalıştırın:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Örnek:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Şemanızda yabancı anahtarlar varsa geçişe ilişkin ilk yük ve sürekli eşitleme başarısız olur.  Bırakma yabancı anahtar betiği çıkarmak ve yabancı anahtar betiği eklemek için MySQL çalışma ekranında aşağıdaki betiği yürütün.

```sql
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

Yabancı anahtarı bırakmak için sorgu sonucunda DROP yabancı anahtarını (ikinci sütun) çalıştırın.

> [!NOTE]
> Azure DMS, üst tabloda bir satır silindiğinde veya güncelleştirilirse alt tablodaki eşleşen bir satırı otomatik olarak silmeye veya güncelleştirmeye yardımcı olan CASCADE başvuru eylemini desteklemez. Daha fazla bilgi için MySQL belgelerinde [yabancı anahtar kısıtlamaları](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)makalesinin başvurusal Eylemler bölümüne bakın.
> Azure DMS, ilk veri yükü sırasında hedef veritabanı sunucusunda yabancı anahtar kısıtlamalarını bırakmayı gerektirir ve başvurusal eylemleri kullanamazsınız. İş yükünüz bu başvuru eylemi aracılığıyla ilişkili bir alt tablonun güncelleştirilmesine bağımlıysa bunun yerine bir [döküm ve geri yükleme](../mysql/concepts-migrate-dump-restore.md) gerçekleştirmenizi öneririz. 


> [!IMPORTANT]
> Bir yedekleme kullanarak veri içeri aktardıysanız, bir mysqldump gerçekleştirirken CREATE DEFINER komutlarını manuel olarak veya--Skip-definer komutunu kullanarak kaldırın. DEFINER, MySQL için Azure veritabanı 'nda oluşturulacak ve kısıtlanmış bir süper ayrıcalık gerektirir.

Veritabanında Tetikleyiciniz varsa, kaynaktan tam veri geçişinin önüne veri bütünlüğünü zorlayacaktır. Bu öneri, geçiş sırasında hedefteki tüm tablolardaki Tetikleyicileri devre dışı bırakıp, geçiş yapıldıktan sonra Tetikleyicileri etkinleştirir.

Bırakma tetikleyicisi betiğini ayıklamak ve tetikleyici betiği eklemek için hedef veritabanındaki MySQL çalışma sayfasında aşağıdaki betiği yürütün.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Tetikleyicileri hedef veritabanında bırakmak için sonuç içinde oluşturulan bırakma tetikleyicisi sorgusunu (DropQuery sütunu) çalıştırın. Ekleme tetikleyicisi sorgusu, veri geçişi sonrası işleminin tamamlanmasını kullanılmak üzere kaydedilebilir.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration kaynak sağlayıcısını kaydetme

1. Azure portal'da oturum açın, **Tüm hizmetler**'i ve ardından **Abonelikler**'i seçin.

   ![Portal aboneliklerini gösterme](media/tutorial-mysql-to-azure-mysql-online/01-portal-select-subscriptions.png)

2. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve ardından **kaynak sağlayıcıları**' nı seçin.

    ![Kaynak sağlayıcılarını gösterme](media/tutorial-mysql-to-azure-mysql-online/02-01-portal-select-resource-provider.png)

3. "migration" araması yapın ve **Microsoft.DataMigration** öğesinin sağ tarafındaki **Kaydet**'i seçin.

    ![Kaynak sağlayıcısını kaydetme](media/tutorial-mysql-to-azure-mysql-online/02-02-portal-register-resource-provider.png)

## <a name="create-a-database-migration-service-instance"></a>Veritabanı geçiş hizmeti örneği oluşturma

1. Azure portal + **kaynak oluştur**' u seçin, Azure veritabanı geçiş hizmeti ' ni arayın ve ardından açılan listeden **Azure veritabanı geçiş hizmeti** ' ni seçin.

    ![Azure Market](media/tutorial-mysql-to-azure-mysql-online/03-dms-portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/tutorial-mysql-to-azure-mysql-online/04-dms-portal-marketplace-create.png)
  
3. **Geçiş Hizmeti oluşturun** ekranında hizmet için bir ad belirtin, aboneliği ve yeni ya da var olan bir kaynak grubunu seçin.

4. Bir fiyatlandırma katmanı seçin ve ağ ekranına geçiş yapın. Çevrimdışı geçiş özelliği, hem standart hem de Premium fiyatlandırma katmanında kullanılabilir.

    Maliyetler ve fiyatlandırma katmanları hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://aka.ms/dms-pricing) bakın.

    ![Azure veritabanı geçiş hizmeti temel ayarlarını yapılandırma](media/tutorial-mysql-to-azure-mysql-online/05-dms-portal-create-basic.png)

5. Listeden var olan bir sanal ağı seçin veya oluşturulacak yeni sanal ağın adını belirtin. Gözden geçir + oluştur ekranına geçin. İsteğe bağlı olarak, Etiketler ekranını kullanarak hizmete Etiketler ekleyebilirsiniz.

    Sanal ağ, kaynak SQL Server ve hedef Azure SQL veritabanı örneğine erişimi olan Azure veritabanı geçiş hizmeti sağlar.

    ![Azure veritabanı geçiş hizmeti ağ ayarlarını yapılandırma](media/tutorial-mysql-to-azure-mysql-online/06-dms-portal-create-networking.png)

    Azure portal sanal ağ oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/quick-create-portal.md)makalesine bakın.

6. Yapılandırmayı gözden geçirin ve **Oluştur** ' u seçerek hizmeti oluşturun.
    
    ![Azure veritabanı geçiş hizmeti oluşturma](media/tutorial-mysql-to-azure-mysql-online/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Hizmet oluşturulduktan sonra Azure portaldan bulun, açın ve yeni bir geçiş projesi oluşturun.  

1. Azure portalda **Tüm hizmetler**'i seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

    ![Azure veritabanı geçiş hizmeti 'nin tüm örneklerini bulun](media/tutorial-mysql-to-azure-mysql-online/08-01-dms-portal-search-service.png)

2. Arama sonuçlarından geçiş hizmeti örneğinizi seçin ve + **yeni geçiş projesi ' ni** seçin.
    
    ![Yeni bir geçiş projesi oluşturma](media/tutorial-mysql-to-azure-mysql-online/08-02-dms-portal-new-project.png)

3. **Yeni geçiş projesi** ekranında, proje için bir ad belirtin, **kaynak sunucu türü** seçimi kutusunda **MySQL**' i seçin, **hedef sunucu türü** seçimi kutusunda MySQL **için Azure veritabanı** ' nı seçin ve **geçiş etkinlik türü** seçimi kutusunda **çevrimiçi veri geçişi**' ni seçin. **Etkinlik oluştur ve çalıştır** seçeneğini belirleyin.

    ![Veritabanı Geçiş Hizmeti Projesi Oluşturma](media/tutorial-mysql-to-azure-mysql-online/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > Alternatif olarak, şimdi **proje oluştur** ' u seçerek geçiş projesini hemen oluşturabilir ve geçişi daha sonra yürütebilirsiniz.

## <a name="configure-migration-project"></a>Geçiş projesini yapılandırma

1. **Kaynak Seç** ekranında, kaynak MySQL örneği için bağlantı ayrıntılarını belirtin ve ileri ' yi seçin **: hedef>>seçin**

    ![Kaynak ayrıntıları ekleme ekranı](media/tutorial-mysql-to-azure-mysql-online/10-dms-portal-project-mysql-source.png)

2. **Hedef Seç** ekranında, MySQL Için Azure veritabanı örneği için bağlantı ayrıntılarını belirtin ve ileri ' yi seçin **>>veritabanları** ' nı seçin.

    ![Hedef ayrıntıları ekleme ekranı](media/tutorial-mysql-to-azure-mysql-online/11-dms-portal-project-mysql-target.png)

3. **Veritabanları seçin** ekranında, kaynak ve hedef veritabanını geçiş için eşleyin ve ileri ' yi seçin **: geçiş ayarlarını yapılandırma>>**. Kaynağı salt okunur hale getirmek için **kaynak sunucuyu ReadOnly yap** seçeneğini belirleyebilirsiniz, ancak bunun bir sunucu düzeyi ayarı olduğundan emin olmanız gerekir. Seçilirse, yalnızca seçili veritabanlarını değil, tüm sunucuyu salt okunurdur.
    
    Hedef veritabanı, kaynak veritabanıyla aynı veritabanı adını içeriyorsa, Azure veritabanı geçiş hizmeti varsayılan olarak hedef veritabanını seçer.
    ![Veritabanı ayrıntıları ekranını Seç](media/tutorial-mysql-to-azure-mysql-online/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
   > Bu adımda birden çok veritabanı seçebilirsiniz, ancak her bir Azure veritabanı geçiş hizmeti örneği, eşzamanlı geçiş için en fazla 4 veritabanını destekler. Ayrıca, her bölge için abonelik başına Azure veritabanı geçiş hizmeti 'nin 10 örnek sınırı vardır. Örneğin, geçirilecek 80 veritabanlarına sahipseniz, aynı bölgeye 40 ' i aynı bölgeye geçirebilirsiniz, ancak yalnızca Azure veritabanı geçiş hizmeti 'nin 10 örneğini oluşturduysanız.

4. **Geçiş ayarlarını yapılandır** ekranında, geçişin parçası olacak tabloları seçin ve **İleri ' yi seçin: Özet>>**. Hedef tablolarda herhangi bir veri varsa, bunlar varsayılan olarak seçilmeyecektir, ancak bunları açık bir şekilde seçebilirsiniz ve Geçişe başlamadan önce bu dosyalar kesilir.

    ![Tablo seçme ekranı](media/tutorial-mysql-to-azure-mysql-online/13-dms-portal-project-mysql-select-tbl.png)

5. **Özet** ekranında, **etkinlik adı** metin kutusunda, geçiş etkinliği için bir ad belirtin ve kaynak ve hedef ayrıntılarının daha önce belirtdikleriniz ile eşleştiğinden emin olmak için Özeti gözden geçirin.

    ![Geçiş projesi özeti](media/tutorial-mysql-to-azure-mysql-online/14-dms-portal-project-mysql-activity-summary.png)

6. **Geçişi Başlat**' ı seçin. Geçiş etkinliği penceresi görünür ve etkinliğin **durumu** **başlatılıyor**. Tablo  geçişleri başlatıldığında durum **çalışıyor** olarak değişir.

## <a name="monitor-the-migration"></a>Geçişi izleme

1. Geçiş etkinliği ekranında **Yenile**'yi seçerek, gösterilen verileri, geçişin **Durum** bilgisi **Tamamlandı** olana kadar güncelleştirebilirsiniz.

     ![Etkinlik Durumu - tamamlandı](media/tutorial-mysql-to-azure-mysql-online/15-dms-activity-completed.png)

2. **Veritabanı Adı** bölümünde, **Tam veri yüklemesi** ve **Artımlı veri eşitleme** işlemleri için geçiş durumunu almak üzere belirli bir veritabanı seçin.

    Tam veri yüklemesi ilk yük geçiş durumunu, Artımlı veri eşitleme ise değişiklik verilerini yakalama (CDC) durumunu gösterir.

     ![Etkinlik Durumu - Tam yük tamamlandı](media/tutorial-mysql-to-azure-mysql-online/16-dms-activity-full-load-completed.png)

     ![Etkinlik Durumu - Artımlı veri eşitleme](media/tutorial-mysql-to-azure-mysql-online/17-dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Tam geçiş gerçekleştirme

İlk Tam yük tamamlandıktan sonra, veritabanları **Geçiş için hazır** olarak işaretlenir.

1. Veritabanı geçişini tamamlamaya hazır olduğunuzda **Tam Geçişi Başlat** seçeneğini belirleyin.

    ![Tam geçişi başlat](media/tutorial-mysql-to-azure-mysql-online/18-dms-start-cutover.png)

2. **Bekleyen değişiklikler** sayacı **0** değerini gösterene kadar bekleyerek kaynak veritabanına gelen tüm işlemleri durdurduğunuzdan emin olun.
3. **Onayla**'yı ve ardından, **Uygula**'yı seçin.
4. Veritabanı geçişi durumu **Tamamlandı** olarak gösterildiğinde, uygulamalarınızı yeni hedef Azure SQL Veritabanı'na bağlayın.

## <a name="next-steps"></a>Sonraki adımlar

* MySQL için Azure Veritabanı'na yönelik çevrimiçi geçiş gerçekleştirirken karşılaşılan bilinen sorunlar ve sınırlamalar hakkında bilgi için [MySQL için Azure Veritabanı geçiş işlemleri ile ilgili bilinen sorunlar ve geçici çözümler](known-issues-azure-mysql-online.md) başlıklı makaleye bakın.
* Azure veritabanı geçiş hizmeti hakkında daha fazla bilgi için [Azure veritabanı geçiş hizmeti nedir?](./dms-overview.md)makalesine bakın.
* MySQL için Azure veritabanı hakkında daha fazla bilgi için, [MySQL Için Azure veritabanı nedir?](../mysql/overview.md)makalesine bakın.
