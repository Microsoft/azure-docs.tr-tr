---
title: "Öğretici: DMS kullanarak MySQL için MySQL için Azure veritabanı 'na geçirin"
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti 'ni kullanarak şirket içi MySQL 'ten MySQL için Azure veritabanı 'na çevrimdışı geçiş gerçekleştirmeyi öğrenin.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: 71363771359ffef0ff165bd4a6744d864ea1856c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819727"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-offline-using-dms"></a>Öğretici: DMS kullanarak MySQL için MySQL için Azure veritabanı 'na geçirin

Azure veritabanı geçiş hizmeti 'ni kullanarak, yüksek hızlı veri geçişi özelliği olan [MySQL Için Azure veritabanı](../mysql/index.yml) 'na tek seferlik tam bir veritabanı geçişi gerçekleştirebilirsiniz. Bu öğreticide, Azure veritabanı geçiş hizmeti 'nde çevrimdışı geçiş etkinliği kullanarak MySQL 5,7 ' in şirket içi örneğinden bir örnek veritabanını MySQL için Azure veritabanı 'na (v 5.7) geçireceğiniz. Makaleler kaynağı bir MySQL veritabanı örneği ve hedefi MySQL için Azure veritabanı olacak şekilde varsaysa da, kaynak sunucu adını ve kimlik bilgilerini değiştirerek MySQL için bir Azure veritabanından diğerine geçiş yapmak için kullanılabilir. Ayrıca, daha düşük sürüm MySQL sunucularından (v 5.6 ve üzeri) daha yüksek sürümlere geçiş de desteklenir.

> [!IMPORTANT]
> Çevrimiçi geçişler için, bu yeni teklifi, [veri ile çoğaltma](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)ile birlikte kullanabilirsiniz. Alternatif olarak, çevrimiçi geçişler için veri-çoğaltma ile [Mydumper/MyLoader](https://centminmod.com/mydumper.html) gibi açık kaynaklı araçları kullanın. 

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]


Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Mysqldump yardımcı programını kullanarak veritabanı şemasını geçirin.
> * Azure Veritabanı Geçiş Hizmeti örneği oluşturun.
> * Azure veritabanı geçiş hizmeti 'ni kullanarak bir geçiş projesi oluşturun.
> * Geçişi çalıştırma.
> * Geçişi izleme.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

* Etkin aboneliği olan bir Azure hesabına sahip olmanız gerekir. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free).
* Sürüm 5,7 ile şirket içi bir MySQL veritabanı vardır. Aksi takdirde [MySQL Community edition](https://dev.mysql.com/downloads/mysql/) 5,7 ' ı indirip yükleyin.
* [MySQL için Azure Veritabanı örneği oluşturma](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Çalışma ekranı uygulamasını kullanarak bir veritabanını bağlama ve oluşturma hakkındaki ayrıntıları öğrenmek için [MySQL çalışma ekranı kullanma makalesine başvurun ve verileri sorgulayın](../mysql/connect-workbench.md) . MySQL için Azure veritabanı sürümü, şirket içi MySQL sürümüne eşit veya ondan daha yüksek olmalıdır. Örneğin, MySQL 5,7, MySQL için Azure veritabanı 5,7 ' e geçirebilir veya 8 ' e yükseltilir. 
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
* Azure veritabanı geçiş hizmeti için sanal ağ bağlantılarının kaynak MySQL sunucusuna erişmesine izin vermek için Windows Güvenlik duvarınızı açın, varsayılan olarak TCP bağlantı noktası 3306 ' dir.
* Kaynak veritabanınızın önünde bir güvenlik duvarı gereci kullanırken, Azure veritabanı geçiş hizmeti için sanal ağdan gelen bağlantıların geçiş için kaynak veritabanına erişmesine izin vermek üzere güvenlik duvarı kuralları eklemeniz gerekebilir.
* Hedef veritabanlarına Azure veritabanı geçiş hizmeti erişimi için sanal ağa izin vermek amacıyla, sunucu düzeyinde bir [güvenlik duvarı kuralı](../azure-sql/database/firewall-configure.md) oluşturun veya MySQL Için hedef Azure VERITABANı için [VNET hizmet uç noktalarını yapılandırın](../mysql/howto-manage-vnet-using-portal.md) .
* Kaynak MySQL'in desteklenen MySQL Community Edition sürümünde olması gerekir. MySQL örneğinin sürümünü belirlemek için, MySQL yardımcı programında veya MySQL Workbench'te şu komutu çalıştırın:

    ```
    SELECT @@version;
    ```

* MySQL için Azure Veritabanı yalnızca InnoDB tablolarını destekler. MyISAM tablolarını InnoDB'ye dönüştürmek için [Converting Tables from MyISAM to InnoDB (Tabloları MyISAM'dan InnoDB'ye Dönüştürme)](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) başlıklı makaleye bakın
* Kullanıcının, kaynak veritabanındaki verileri okuma ayrıcalıklarına sahip olması gerekir.

## <a name="migrate-database-schema"></a>Veritabanı şemasını geçir

Tablo şemaları, dizinler ve saklı yordamlar gibi tüm veritabanı nesnelerini aktarmak için, kaynak veritabanından şemayı ayıklamız ve hedef veritabanına uygulamanız gerekir. Şemayı ayıklamak için `--no-data` parametresiyle mysqldump yardımcı programını kullanabilirsiniz. Bunun için hem kaynak MySQL veritabanına hem de MySQL için Azure veritabanı 'na bağlanabilecek bir makineye ihtiyacınız vardır.

Şemayı mysqldump kullanarak dışarı aktarmak için aşağıdaki komutu çalıştırın:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Örnek:

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

Şemayı, MySQL için Azure veritabanı hedef olarak içeri aktarmak için aşağıdaki komutu çalıştırın:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Örnek:

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Şemanızda yabancı anahtarlarınız varsa, geçiş sırasında paralel veri yükü geçiş görevi tarafından işlenir. Şema geçişi sırasında yabancı anahtarları bırakmaya gerek yoktur.

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

Kaynak sağlayıcısı kaydının her bir Azure aboneliğinde yalnızca bir kez yapılması gerekir. Kayıt olmadan, **Azure veritabanı geçiş hizmeti**'nin bir örneğini oluşturabileceksiniz.

1. Azure portal'da oturum açın, **Tüm hizmetler**'i ve ardından **Abonelikler**'i seçin.

   ![Portal aboneliklerini gösterme](media/tutorial-mysql-to-azure-mysql-offline-portal/01-dms-portal-select-subscription.png)

2. Azure veritabanı geçiş hizmeti örneğini oluşturmak istediğiniz aboneliği seçin ve ardından **kaynak sağlayıcıları**' nı seçin.

3. "migration" araması yapın ve **Microsoft.DataMigration** öğesinin sağ tarafındaki **Kaydet**'i seçin.

    ![Kaynak sağlayıcısını kaydetme](media/tutorial-mysql-to-azure-mysql-offline-portal/02-dms-portal-register-rp.png)

## <a name="create-a-database-migration-service-instance"></a>Veritabanı geçiş hizmeti örneği oluşturma

1. Azure portal + **kaynak oluştur**' u seçin, Azure veritabanı geçiş hizmeti ' ni arayın ve ardından açılan listeden **Azure veritabanı geçiş hizmeti** ' ni seçin.

    ![Azure Market](media/tutorial-mysql-to-azure-mysql-offline-portal/03-dms-portal-marketplace.png)

2. **Azure Veritabanı Geçiş Hizmeti** ekranında **Oluştur**'u seçin.

    ![Azure Veritabanı Geçiş Hizmeti örneğini oluşturma](media/tutorial-mysql-to-azure-mysql-offline-portal/04-dms-portal-marketplace-create.png)
  
3. **Geçiş Hizmeti oluşturun** ekranında hizmet için bir ad belirtin, aboneliği ve yeni ya da var olan bir kaynak grubunu seçin.

4. Bir fiyatlandırma katmanı seçin ve ağ ekranına geçiş yapın. Çevrimdışı geçiş özelliği, hem standart hem de Premium fiyatlandırma katmanında kullanılabilir.

    Maliyetler ve fiyatlandırma katmanları hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://aka.ms/dms-pricing) bakın.

    ![Azure veritabanı geçiş hizmeti temel ayarlarını yapılandırma](media/tutorial-mysql-to-azure-mysql-offline-portal/05-dms-portal-create-basic.png)

5. Listeden var olan bir sanal ağı seçin veya oluşturulacak yeni sanal ağın adını belirtin. Gözden geçir + oluştur ekranına geçin. İsteğe bağlı olarak, Etiketler ekranını kullanarak hizmete Etiketler ekleyebilirsiniz.

    Sanal ağ, kaynak SQL Server ve hedef Azure SQL veritabanı örneğine erişimi olan Azure veritabanı geçiş hizmeti sağlar.

    ![Azure veritabanı geçiş hizmeti ağ ayarlarını yapılandırma](media/tutorial-mysql-to-azure-mysql-offline-portal/06-dms-portal-create-networking.png)

    Azure portal sanal ağ oluşturma hakkında daha fazla bilgi için [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/quick-create-portal.md)makalesine bakın.

6. Yapılandırmayı gözden geçirin ve **Oluştur** ' u seçerek hizmeti oluşturun.
    
    ![Azure veritabanı geçiş hizmeti oluşturma](media/tutorial-mysql-to-azure-mysql-offline-portal/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Hizmet oluşturulduktan sonra Azure portaldan bulun, açın ve yeni bir geçiş projesi oluşturun.  

1. Azure portalda **Tüm hizmetler**'i seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

    ![Azure veritabanı geçiş hizmeti 'nin tüm örneklerini bulun](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Arama sonuçlarından geçiş hizmeti örneğinizi seçin ve + **yeni geçiş projesi ' ni** seçin.
    
    ![Yeni bir geçiş projesi oluşturma](media/tutorial-mysql-to-azure-mysql-offline-portal/08-02-dms-portal-new-project.png)

3. **Yeni geçiş projesi** ekranında, proje için bir ad belirtin, **kaynak sunucu türü** seçimi kutusunda **MySQL**' i seçin, **hedef sunucu türü** seçimi kutusunda MySQL **için Azure veritabanı** ' nı seçin ve **geçiş etkinlik türü** seçimi kutusunda **veri geçiş \[ Önizlemesi \]**' ni seçin. **Etkinlik oluştur ve çalıştır** seçeneğini belirleyin.

    ![Veritabanı Geçiş Hizmeti Projesi Oluşturma](media/tutorial-mysql-to-azure-mysql-offline-portal/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > Alternatif olarak, şimdi **proje oluştur** ' u seçerek geçiş projesini hemen oluşturabilir ve geçişi daha sonra yürütebilirsiniz.

## <a name="configure-migration-project"></a>Geçiş projesini yapılandırma

1. **Kaynak Seç** ekranında, kaynak MySQL örneği için bağlantı ayrıntılarını belirtin ve ileri ' yi seçin **: hedef>>seçin**

    ![Kaynak ayrıntıları ekleme ekranı](media/tutorial-mysql-to-azure-mysql-offline-portal/10-dms-portal-project-mysql-source.png)

2. **Hedef Seç** ekranında, MySQL Için Azure veritabanı örneği için bağlantı ayrıntılarını belirtin ve ileri ' yi seçin **>>veritabanları** ' nı seçin.

    ![Hedef ayrıntıları ekleme ekranı](media/tutorial-mysql-to-azure-mysql-offline-portal/11-dms-portal-project-mysql-target.png)

3. **Veritabanları seçin** ekranında, kaynak ve hedef veritabanını geçiş için eşleyin ve ileri ' yi seçin **: geçiş ayarlarını yapılandırma>>**. Kaynağı salt okunur hale getirmek için **kaynak sunucuyu ReadOnly yap** seçeneğini belirleyebilirsiniz, ancak bunun bir sunucu düzeyi ayarı olduğundan emin olmanız gerekir. Seçilirse, yalnızca seçili veritabanlarını değil, tüm sunucuyu salt okunurdur.
    
    Hedef veritabanı, kaynak veritabanıyla aynı veritabanı adını içeriyorsa, Azure veritabanı geçiş hizmeti varsayılan olarak hedef veritabanını seçer.
    ![Veritabanı ayrıntıları ekranını Seç](media/tutorial-mysql-to-azure-mysql-offline-portal/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
    > Bu adımda birden çok veritabanı seçebilirsiniz, ancak her veritabanı işlem paylaştığından, bu şekilde veritabanlarının kaç ve ne kadar hızlı geçirilebileceğini gösteren sınırlar vardır. Premium SKU 'nun varsayılan yapılandırmasıyla, her geçiş görevi iki tabloyu paralel olarak geçirmeye çalışır. Bu tablolar Seçili veritabanlarının herhangi birinden olabilir. Bu yeterince hızlı değilse, veritabanı geçiş etkinliklerini farklı geçiş görevlerine bölebilir ve birden çok hizmet arasında ölçeklendirme yapabilirsiniz. Ayrıca, her bölge için abonelik başına Azure veritabanı geçiş hizmeti 'nin 10 örnek sınırı vardır.
    > Geçiş aktarım hızı ve paralelleştirme hakkında daha ayrıntılı denetim için, lütfen [PowerShell 'i kullanarak MySQL veritabanı 'ndan MySQL Için Azure veritabanı 'na çevrimdışı geçiş çalıştırma](./migrate-mysql-to-azure-mysql-powershell.md) başlıklı makaleye bakın.

4. **Geçiş ayarlarını yapılandır** ekranında, geçişin parçası olacak tabloları seçin ve **İleri ' yi seçin: Özet>>**. Hedef tablolarda herhangi bir veri varsa, bunlar varsayılan olarak seçilmeyecektir, ancak bunları açık bir şekilde seçebilirsiniz ve Geçişe başlamadan önce bu dosyalar kesilir.

    ![Tablo seçme ekranı](media/tutorial-mysql-to-azure-mysql-offline-portal/13-dms-portal-project-mysql-select-tbl.png)

5. **Özet** ekranında, **etkinlik adı** metin kutusunda, geçiş etkinliği için bir ad belirtin ve kaynak ve hedef ayrıntılarının daha önce belirtdikleriniz ile eşleştiğinden emin olmak için Özeti gözden geçirin.

    ![Geçiş projesi özeti](media/tutorial-mysql-to-azure-mysql-offline-portal/14-dms-portal-project-mysql-activity-summary.png)

6. **Geçişi Başlat**' ı seçin. Geçiş etkinliği penceresi görünür ve etkinliğin **durumu** **başlatılıyor**. Tablo  geçişleri başlatıldığında durum **çalışıyor** olarak değişir.
 
     ![Geçiş çalıştırılıyor](media/tutorial-mysql-to-azure-mysql-offline-portal/15-dms-portal-project-mysql-running.png)

## <a name="monitor-the-migration"></a>Geçişi izleme

1. Geçiş etkinliği ekranında, görünümü güncelleştirmek için **Yenile** ' yi seçin ve tamamlanan tablo sayısı hakkındaki ilerlemeyi görün. 

2. Her tablonun geçirilmiş olduğu şekliyle ilgili durumunu görmek için etkinlik ekranındaki veritabanı adına tıklayabilirsiniz. Görünümü güncelleştirmek için **Yenile** ' yi seçin. 

     ![Geçiş izleme](media/tutorial-mysql-to-azure-mysql-offline-portal/16-dms-portal-project-mysql-monitor.png)

## <a name="complete-the-migration"></a>Geçişi tamamlamayı

1. Geçiş etkinliği ekranında **Yenile**'yi seçerek, gösterilen verileri, geçişin **Durum** bilgisi **Tamamlandı** olana kadar güncelleştirebilirsiniz.

    ![Geçişi tamamlama](media/tutorial-mysql-to-azure-mysql-offline-portal/17-dms-portal-project-mysql-complete.png)

## <a name="post-migration-activities"></a>Geçiş sonrası etkinlikler

Çevrimdışı geçişte geçiş tam geçişi, bu belge için kapsam dışı olan, uygulamaya bağımlı bir işlemdir, ancak aşağıdaki geçiş sonrası etkinlikler önceden belirlenir:

1. Uygulama gereksinimlerine göre oturum açma, rol ve izinler oluşturun.
2. Hedef veritabanındaki tüm Tetikleyicileri geçiş öncesi adım sırasında ayıklanan şekilde yeniden oluşturun.
3. Geçişi onaylamak için, hedef veritabanında uygulamanın tasdikliği sınamasını gerçekleştirin. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Veritabanı geçiş hizmetini kullanmaya devam edemeyecekiniz, hizmeti aşağıdaki adımlarla silebilirsiniz:

1. Azure portalda **Tüm hizmetler**'i seçin, Azure Veritabanı Geçiş Hizmeti araması yapın ve **Azure Veritabanı Geçiş Hizmeti**'ni seçin.

    ![DMS 'in tüm örneklerini bulma](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Arama sonuçlarından geçiş hizmeti örneğinizi seçin ve **hizmeti Sil**' i seçin.
    
    ![Geçiş hizmetini silme](media/tutorial-mysql-to-azure-mysql-offline-portal/18-dms-portal-delete.png)

3. Onay iletişim kutusunda, **VERITABANı GEÇIŞ HIZMETI adı** metin kutusuna hizmetin adını yazın ve **Sil** ' i seçin.

    ![Geçiş hizmeti Silmeyi Onayla](media/tutorial-mysql-to-azure-mysql-offline-portal/19-dms-portal-deleteconfirm.png)

## <a name="next-steps"></a>Sonraki adımlar

* DMS kullanarak geçişler gerçekleştirirken oluşan bilinen sorunlar ve sınırlamalar hakkında bilgi için, [yaygın sorunlar-Azure veritabanı geçiş hizmeti](./known-issues-troubleshooting-dms.md)makalesine bakın.
* DMS kullanırken kaynak veritabanı bağlantı sorunlarını giderme hakkında bilgi için bkz. [kaynak veritabanlarını bağlama sorunları](./known-issues-troubleshooting-dms-source-connectivity.md).
* Azure veritabanı geçiş hizmeti hakkında daha fazla bilgi için [Azure veritabanı geçiş hizmeti nedir?](./dms-overview.md)makalesine bakın.
* MySQL için Azure veritabanı hakkında daha fazla bilgi için, [MySQL Için Azure veritabanı nedir?](../mysql/overview.md)makalesine bakın.
* DMS 'i PowerShell aracılığıyla kullanma hakkında rehberlik için bkz. [PowerShell: MySQL veritabanından MySQL Için Azure veritabanı 'na, DMS kullanarak çevrimdışı geçiş çalıştırma](./migrate-mysql-to-azure-mysql-powershell.md)