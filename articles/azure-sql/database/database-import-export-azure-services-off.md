---
title: Azure hizmetlerinin sunucuya erişmesine izin vermeden bir Azure SQL veritabanını içeri veya dışarı aktarın.
description: Azure hizmetlerinin sunucuya erişmesine izin vermeden bir Azure SQL veritabanını içeri veya dışarı aktarın.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 3a02876234d43df2e98a3a4e60453fc3f1f74ef6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724178"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Azure hizmetlerinin sunucuya erişmesine izin vermeden bir Azure SQL veritabanını içeri veya dışarı aktarma
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bu makalede, *Azure hizmetlerinin* sunucuda *devre dışı* olarak AYARLANDıĞı durumlarda bir Azure SQL veritabanını içeri veya dışarı aktarma işlemi gösterilmektedir. İş akışı, içeri veya dışarı aktarma işlemini gerçekleştirmek üzere SqlPackage 'i çalıştırmak için bir Azure sanal makinesini kullanır.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-the-azure-virtual-machine"></a>Azure sanal makinesini oluşturma

**Azure 'A dağıt** düğmesini seçerek bir Azure sanal makinesi oluşturun.

Bu şablon, en son düzeltme eki uygulanan sürümü kullanarak, Windows sürümü için birkaç farklı seçenek kullanarak basit bir Windows sanal makinesini dağıtmanıza olanak tanır. Bu, kaynak grubu konumunda bir a2 boyutlu VM dağıtır ve sanal makinenin tam etki alanı adını döndürür.
<br><br>

[!["Azure 'a dağıt" etiketli bir düğmeyi gösteren resim.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json)

Daha fazla bilgi için bkz. [WINDOWS VM 'Nin çok basit dağıtımı](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).

## <a name="connect-to-the-virtual-machine"></a>Sanal makineye bağlanma

Aşağıdaki adımlarda, Uzak Masaüstü bağlantısı kullanarak sanal makinenize nasıl bağlanabilmeniz gösterilmektedir.

1. Dağıtım tamamlandıktan sonra sanal makine kaynağına gidin.

   ![Ekran görüntüsünde, Bağlan düğmesine sahip bir sanal makineye genel bakış sayfası gösterilir.](./media/database-import-export-azure-services-off/vm.png)  

2. **Bağlan**’ı seçin.

   Bir Uzak Masaüstü Protokolü dosya (. rdp dosyası) formu, sanal makinenin genel IP adresi ve bağlantı noktası numarasıyla birlikte görüntülenir.

   ![RDP formu](./media/database-import-export-azure-services-off/rdp.png)  

3. **RDP Dosyasını İndir**’i seçin.

   > [!NOTE]
   > Sanal makinenize bağlanmak için SSH de kullanabilirsiniz.

4. **Sanal makine bağlantısı** formunu kapatın.
5. VM'nize bağlanmak için indirilen RDP dosyasını açın.
6. İstendiğinde, **Bağlan**' ı seçin. Mac bilgisayarlarda, Mac App Store’dan bu [Uzak Masaüstü İstemcisi](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) gibi bir RDP istemcisi indirmeniz gerekir.

7. Sanal makineyi oluştururken belirttiğiniz kullanıcı adını ve parolayı girip **Tamam**' ı seçin.

8. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Bağlantıya devam etmek için **Evet** ' i veya **devam et** ' i seçin.

## <a name="install-sqlpackage"></a>SqlPackage 'i yükler

[SqlPackage 'in en son sürümünü indirip yükleyin](/sql/tools/sqlpackage-download).

Daha fazla bilgi için bkz. [SqlPackage.exe](/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Veritabanına VM erişimine izin vermek için bir güvenlik duvarı kuralı oluşturma

Sanal makinenin genel IP adresini sunucunun güvenlik duvarına ekleyin.

Aşağıdaki adımlarda, sanal makinenizin genel IP adresi için sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturulur ve sanal makine bağlantısı etkinleştirilir.

1. Sol taraftaki menüden **SQL veritabanları** ' nı seçin ve ardından **SQL veritabanları** sayfasında veritabanınızı seçin. Veritabanınızın genel bakış sayfası açılır ve tam sunucu adı (örneğin, **ServerName.Database.Windows.net**) görüntülenerek daha fazla yapılandırma seçeneği sunulur.

2. Sunucunuza ve veritabanlarına bağlanırken kullanmak için bu tam sunucu adını kopyalayın.

   ![sunucu adı](./media/database-import-export-azure-services-off/server-name.png)

3. Araç çubuğunda **sunucu güvenlik duvarını ayarla** ' yı seçin. Sunucu için **güvenlik duvarı ayarları** sayfası açılır.

   ![Sunucu düzeyi IP güvenlik duvarı kuralı](./media/database-import-export-azure-services-off/server-firewall-rule.png)

4. Sanal makinenizin genel IP adresini yeni bir sunucu düzeyi IP güvenlik duvarı kuralına eklemek için araç çubuğunda **istemci IP 'Si Ekle** ' yi seçin. Sunucu düzeyinde bir IP güvenlik duvarı kuralı, tek bir IP adresi veya IP adresi aralığı için 1433 bağlantı noktasını açabilir.

5. **Kaydet**’i seçin. Sunucuda 1433 numaralı bağlantı noktasını açan sanal makinenizin genel IP adresi için sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturulur.

6. **Güvenlik duvarı ayarları** sayfasını kapatın.

## <a name="export-a-database-using-sqlpackage"></a>SqlPackage kullanarak bir veritabanını dışarı aktarma

[SqlPackage](/sql/tools/sqlpackage) komut satırı yardımcı programını kullanarak BIR Azure SQL veritabanını dışarı aktarmak için bkz. [dışarı aktarma parametreleri ve özellikleri](/sql/tools/sqlpackage#export-parameters-and-properties). SqlPackage yardımcı programı, en son [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ve [SQL Server veri araçları](/sql/ssdt/download-sql-server-data-tools-ssdt)sürümleriyle birlikte gelir veya [SqlPackage](/sql/tools/sqlpackage-download)'in en son sürümünü indirebilirsiniz.

Çoğu üretim ortamında, ölçek ve performans için SqlPackage yardımcı programının kullanılmasını öneririz. BACPAC dosyalarını kullanarak geçiş hakkında bir SQL Server Müşteri Danışmanlık Ekibi blogu için bkz. [BACPAC Dosyalarını kullanarak SQL Server’dan Azure SQL Veritabanına Geçiş](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

Bu örnekte, Active Directory evrensel kimlik doğrulamasıyla SqlPackage.exe kullanarak bir veritabanını dışarı aktarma işlemi gösterilmektedir. Ortamınıza özgü değerlerle değiştirin.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-a-database-using-sqlpackage"></a>SqlPackage kullanarak bir veritabanını içeri aktarma

[SqlPackage](/sql/tools/sqlpackage) komut satırı yardımcı programını kullanarak bir SQL Server veritabanını içeri aktarmak için, bkz. [içeri aktarma parametreleri ve özellikleri](/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage en son [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ve [SQL Server veri araçları](/sql/ssdt/download-sql-server-data-tools-ssdt)sahiptir. [SqlPackage](/sql/tools/sqlpackage-download)'in en son sürümünü de indirebilirsiniz.

Ölçek ve performans için, Azure portal kullanmak yerine, çoğu üretim ortamında SqlPackage kullanılması önerilir. Dosya kullanarak geçiş hakkında bir SQL Server müşteri danışmanlık ekibi blogu için `BACPAC` , bkz. [bacpac dosyalarını kullanarak SQL Server Azure SQL veritabanı 'na geçirme](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

Aşağıdaki SqlPackage komutu, **AdventureWorks2017** veritabanını yerel depolamadan BIR Azure SQL veritabanına aktarır. **Premium** hizmet katmanı ve **P6** hizmeti hedefi ile **myMigratedDatabase** adlı yeni bir veritabanı oluşturur. Bu değerleri ortamınıza uygun şekilde değiştirin.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Şirket güvenlik duvarının arkasındaki tAzure SQL veritabanına bağlanmak için, güvenlik duvarında 1433 numaralı bağlantı noktası açık olmalıdır.

Bu örnek, Active Directory evrensel kimlik doğrulamasıyla SqlPackage kullanılarak bir veritabanının nasıl içeri aktarılacağını gösterir.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Performansla ilgili önemli noktalar

Dışarı aktarma hızları birçok faktöre (örneğin, veri şekli) göre farklılık gösterir, bu sayede hangi hızının beklendiğini tahmin etmek mümkün değildir. SqlPackage, özellikle büyük veritabanları için önemli ölçüde zaman alabilir.

En iyi performansı elde etmek için aşağıdaki stratejileri deneyebilirsiniz:

1. Veritabanında çalışan başka bir iş yükü olmadığından emin olun. Dışa aktarma işleminden önce bir kopya oluşturun başka iş yükünün çalıştığından emin olmak için en iyi çözüm olabilir.
2. Dışa aktarma iş yükünü (birincil olarak okuma g/ç) daha iyi işlemek için veritabanı hizmet düzeyi hedefini (SLO) artırın. Veritabanı şu anda GP_Gen5_4, belki de İş Açısından Kritik katmanı okuma iş yüküne yardımcı olabilir.
3. Özellikle büyük tablolar için kümelenmiş dizinler bulunduğundan emin olun.
4. Sanal makinelerin (VM 'Ler) ağ kısıtlamalarına engel olmak için veritabanıyla aynı bölgede olması gerekir.
5. VM 'Ler, blob depolamaya yüklemeden önce geçici yapıtlar oluşturmak için yeterli büyüklükte bir SSD 'ye sahip olmalıdır.
6. VM 'Ler, belirli bir veritabanı için yeterli çekirdek ve bellek yapılandırmasına sahip olmalıdır.

## <a name="store-the-imported-or-exported-bacpac-file"></a>İçeri aktarılmış veya aktarılmış olarak depolayın. BACPAC dosyası

İçin. BACPAC dosyası [Azure Blob](../../storage/blobs/storage-blobs-overview.md)'Larında veya [Azure dosyalarında](../../storage/files/storage-files-introduction.md)depolanabilir.

En iyi performansı elde etmek için Azure dosyaları 'nı kullanın. SqlPackage, Azure dosyalarına doğrudan erişebilmeleri için dosya sistemiyle çalışır.

Maliyeti azaltmak için, Azure Bloblarını kullanın ve bu, Premium bir Azure dosya paylaşımından maliyeti azaltır. Ancak, öğesini kopyalamanız gerekir [. ](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) İçeri veya dışarı aktarma işleminden önce blob ve yerel dosya sistemi arasında BACPAC dosyası. Sonuç olarak, işlem daha uzun sürer.

Yüklemek veya indirmek için. BACPAC dosyaları, bkz. [AzCopy ve BLOB Storage ile veri aktarma](../../storage/common/storage-use-azcopy-v10.md#transfer-data)ve [AzCopy ve dosya depolama ile veri aktarma](../../storage/common/storage-use-azcopy-files.md).

Ortamınıza bağlı olarak, [Azure depolama güvenlik duvarlarını ve sanal ağlarını yapılandırmanız](../../storage/common/storage-network-security.md)gerekebilir.

## <a name="next-steps"></a>Sonraki adımlar

- İçeri aktarılan bir SQL veritabanına bağlanma ve sorgu yapma hakkında bilgi edinmek için bkz. [hızlı başlangıç: Azure SQL veritabanı: verileri bağlamak ve sorgulamak için SQL Server Management Studio kullanın](connect-query-ssms.md).
- BACPAC dosyalarını kullanarak geçiş hakkında bir SQL Server Müşteri Danışmanlık Ekibi blogu için bkz. [BACPAC Dosyalarını kullanarak SQL Server’dan Azure SQL Veritabanına Geçiş](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Performans önerileri de dahil olmak üzere tüm SQL Server veritabanı geçiş süreci hakkında bir tartışma için bkz. [Azure SQL veritabanı 'na SQL Server veritabanı geçişi](migrate-to-database-from-sql-server.md).
- Depolama anahtarlarını ve paylaşılan erişim imzalarını güvenli bir şekilde yönetme ve paylaşma hakkında bilgi edinmek için bkz. [Azure Storage Güvenlik Kılavuzu](../../storage/blobs/security-recommendations.md).
