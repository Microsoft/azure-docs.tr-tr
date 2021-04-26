---
title: Windows kimlik doğrulamasıyla veri depolarına ve dosya paylaşımlarına erişme
description: Azure SQL veritabanı 'nda SSIS kataloğunu yapılandırmayı ve veri depolarına ve dosya paylaşımlarına Windows kimlik doğrulamasıyla erişen paketleri çalıştırmak için Azure Data Factory Azure-SSIS Integration Runtime öğrenin.
ms.date: 10/27/2020
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: jburchel
ms.openlocfilehash: 8cb92407954ab5376850e57b0ee91d934480b025
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786898"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Azure'da SSIS paketlerinden Windows kimlik doğrulaması ile veri depolarına ve dosya paylaşımlarına erişme

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory (ADF) içinde Azure-SSIS Integration Runtime (IR) üzerinde çalışan SSIS paketlerinden SQL sunucuları, dosya paylaşımları, Azure dosyaları vb. gibi veri depolarına erişmek için Windows kimlik doğrulamasını kullanabilirsiniz. Veri depolarınız, Azure sanal makinelerinde (VM 'Ler) barındırılan veya yönetilen hizmet olarak Azure 'da çalışan şirket içi olabilir. Şirket içinde yer alıyorsa Azure-SSIS IR, şirket içi ağınıza bağlı bir sanal ağa (Microsoft Azure Sanal Ağ) katılmanız gerekir, bkz. [Azure-SSIS IR ekleme Microsoft Azure sanal ağ](./join-azure-ssis-integration-runtime-virtual-network.md). Azure-SSIS IR üzerinde çalışan SSIS paketlerindeki Windows kimlik doğrulaması ile veri depolarına erişmek için dört yöntem vardır:

| Bağlantı yöntemi | Etkin kapsam | Kurulum adımı | Paketlerdeki erişim yöntemi | Kimlik bilgisi kümelerinin ve bağlı kaynakların sayısı | Bağlı kaynakların türü | 
|---|---|---|---|---|---|
| Etkinlik düzeyi yürütme bağlamı ayarlama | SSIS paketi yürütme etkinliği başına | **Windows kimlik doğrulama** ÖZELLIĞINI, SSIS paketlerini ADF işlem hatları 'nda SSIS paket etkinliklerini yürütme olarak çalıştırırken bir "yürütme/farklı çalıştır" bağlamını ayarlamak için yapılandırın.<br/><br/> Daha fazla bilgi için bkz. [SSIS paketi yürütme etkinliğini yapılandırma](./how-to-invoke-ssis-package-ssis-activity.md). | Kaynaklara doğrudan paketlerde erişin, örneğin, dosya paylaşımlarına veya Azure dosyalarına erişmek için UNC yolunu kullanın: `\\YourFileShareServerName\YourFolderName` veya `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Tüm bağlı kaynaklar için yalnızca bir kimlik bilgisi kümesini destekler | -Şirket içi/Azure VM 'lerinde dosya paylaşımları<br/><br/> -Azure dosyaları, bkz. [Azure dosya paylaşma kullanma](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> -Windows kimlik doğrulaması ile şirket içi/Azure VM 'lerinde SQL Server 'Lar<br/><br/> -Windows kimlik doğrulaması ile diğer kaynaklar |
| Katalog düzeyinde yürütme bağlamı ayarlama | Azure-SSIS IR başına, ancak etkinlik düzeyi yürütme bağlamı ayarlanırken geçersiz kılınır (yukarıya bakın) | `catalog.set_execution_credential`"Yürütme/farklı çalıştır" bağlamını ayarlamak IÇIN SSSıSDB saklı yordamını yürütün.<br/><br/> Daha fazla bilgi için aşağıdaki makalenin geri kalanına bakın. | Kaynaklara doğrudan paketlerde erişin, örneğin, dosya paylaşımlarına veya Azure dosyalarına erişmek için UNC yolunu kullanın: `\\YourFileShareServerName\YourFolderName` veya `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Tüm bağlı kaynaklar için yalnızca bir kimlik bilgisi kümesini destekler | -Şirket içi/Azure VM 'lerinde dosya paylaşımları<br/><br/> -Azure dosyaları, bkz. [Azure dosya paylaşma kullanma](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> -Windows kimlik doğrulaması ile şirket içi/Azure VM 'lerinde SQL Server 'Lar<br/><br/> -Windows kimlik doğrulaması ile diğer kaynaklar |
| Komut aracılığıyla kalıcı kimlik bilgileri `cmdkey` | Azure-SSIS IR başına, ancak etkinlik/Katalog düzeyinde yürütme bağlamı ayarlanırken geçersiz kılınır (yukarıya bakın) | `cmdkey` `main.cmd` Azure-SSIS IR, örneğin, dosya paylaşımları, Azure dosyaları veya SQL Server kullanıyorsanız, özel bir kurulum komut dosyasında () komutunu yürütün.<br/><br/> `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword`,<br/><br/> `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey`veya<br/><br/> `cmdkey /add:YourSQLServerFullyQualifiedDomainNameOrIPAddress:YorSQLServerPort /user:YourDomainName\YourUsername /pass:YourPassword`.<br/><br/> Daha fazla bilgi için bkz. [Azure-SSIS IR için kurulumu özelleştirme](./how-to-configure-azure-ssis-ir-custom-setup.md). | Kaynaklara doğrudan paketlerde erişin, örneğin, dosya paylaşımlarına veya Azure dosyalarına erişmek için UNC yolunu kullanın: `\\YourFileShareServerName\YourFolderName` veya `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Farklı bağlı kaynaklar için birden çok kimlik bilgisi kümesini destekleme | -Şirket içi/Azure VM 'lerinde dosya paylaşımları<br/><br/> -Azure dosyaları, bkz. [Azure dosya paylaşma kullanma](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> -Windows kimlik doğrulaması ile şirket içi/Azure VM 'lerinde SQL Server 'Lar<br/><br/> -Windows kimlik doğrulaması ile diğer kaynaklar |
| Paket yürütme sırasında (kalıcı olmayan) sürücü bağlama | Paket başına | `net use`Paketinizdeki denetim akışının başlangıcına eklenen Işlem yürütme görevinde komutu yürütün, örneğin:`net use D: \\YourFileShareServerName\YourFolderName` | Eşlenen sürücüler aracılığıyla dosya paylaşımlarına erişme | Farklı dosya paylaşımları için birden çok sürücüyü destekleme | -Şirket içi/Azure VM 'lerinde dosya paylaşımları<br/><br/> -Azure dosyaları, bkz. [Azure dosya paylaşma kullanma](../storage/files/storage-how-to-use-files-windows.md) |
|||||||

> [!WARNING]
> Windows kimlik doğrulaması ile veri depolarına erişmek için yukarıdaki yöntemlerden herhangi birini kullanmazsanız, Windows kimlik doğrulamasına bağlı olan paketleriniz bunlara erişemez ve çalışma zamanında başarısız olur. 

Bu makalenin geri kalanında, SQL veritabanı/SQL yönetilen örneği 'nde barındırılan SSIS kataloğunun (SSSıSDB), veri depolarına erişmek için Windows kimlik doğrulaması kullanan Azure-SSIS IR paketleri çalıştıracak şekilde nasıl yapılandırılacağı açıklanmaktadır. 

## <a name="you-can-only-use-one-set-of-credentials"></a>Yalnızca bir kimlik bilgileri kümesi kullanabilirsiniz

Windows kimlik doğrulamasını bir SSIS paketinde kullandığınızda yalnızca bir kimlik bilgileri kümesi kullanabilirsiniz. Bu makaledeki adımları uyguladığınızda sağladığınız etki alanı kimlik bilgileri, siz değiştirene veya onları kaldırana kadar Azure-SSIS IR tüm paket yürütmeleri için ve etkileşimli olarak zamanlanır. Paketinizin farklı kimlik bilgileri kümeleriyle birden çok veri deposu 'na bağlanması gerekiyorsa, yukarıdaki Alternatif yöntemleri göz önünde bulundurmanız gerekir.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Windows kimlik doğrulaması için etki alanı kimlik bilgilerini belirtin

Paketlerin Şirket içindeki veri depolarına erişmek için Windows kimlik doğrulamasını kullanmasına izin veren etki alanı kimlik bilgileri sağlamak için aşağıdaki işlemleri yapın:

1. SQL Server Management Studio (SSMS) veya başka bir araçla, SSıSDB barındıran SQL Database/SQL Managed örneğine bağlanın. Daha fazla bilgi için bkz. [Azure 'DA SSıSDB 'ye bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Geçerli veritabanı olarak SSSıSDB ile bir sorgu penceresi açın.

3. Aşağıdaki saklı yordamı çalıştırın ve uygun etki alanı kimlik bilgilerini sağlayın:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. SSIS paketlerinizi çalıştırın. Paketler, Windows kimlik doğrulamasıyla Şirket içindeki veri depolarına erişmek için verdiğiniz kimlik bilgilerini kullanır.

### <a name="view-domain-credentials"></a>Etki alanı kimlik bilgilerini görüntüleme

Etkin etki alanı kimlik bilgilerini görüntülemek için aşağıdaki işlemleri yapın:

1. SSMS veya başka bir araçla, SSSıSDB barındıran SQL veritabanı/SQL yönetilen örneğine bağlanın. Daha fazla bilgi için bkz. [Azure 'DA SSıSDB 'ye bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Geçerli veritabanı olarak SSSıSDB ile bir sorgu penceresi açın.

3. Aşağıdaki saklı yordamı çalıştırın ve çıktıyı denetleyin:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Etki alanı kimlik bilgilerini temizle
Bu makalede açıklandığı gibi verdiğiniz kimlik bilgilerini temizlemek ve kaldırmak için aşağıdaki işlemleri yapın:

1. SSMS veya başka bir araçla, SSSıSDB barındıran SQL veritabanı/SQL yönetilen örneğine bağlanın. Daha fazla bilgi için bkz. [Azure 'DA SSıSDB 'ye bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Geçerli veritabanı olarak SSSıSDB ile bir sorgu penceresi açın.

3. Aşağıdaki saklı yordamı çalıştırın:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Şirket içi bir SQL Server bağlanma

Şirket içi bir SQL Server bağlanıp bağlanamaıp bağlanamayacağını denetlemek için aşağıdaki işlemleri yapın:

1. Bu testi çalıştırmak için, etki alanına katılmış olmayan bir bilgisayar bulun.

2. Etki alanına katılmış olmayan bilgisayarda, SSMS 'yi kullanmak istediğiniz etki alanı kimlik bilgileriyle başlatmak için aşağıdaki komutu çalıştırın:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. SSMS 'den, şirket içi SQL Server bağlanıp bağlanamaıp bağlanamayacağını denetleyin.

### <a name="prerequisites"></a>Önkoşullar

Azure 'da çalışan paketlerden şirket içi bir SQL Server erişmek için aşağıdaki işlemleri yapın:

1.  SQL Server Yapılandırma Yöneticisi, TCP/IP protokolünü etkinleştirin.

2. Windows Güvenlik Duvarı üzerinden erişime izin ver. Daha fazla bilgi için bkz. [Windows Güvenlik Duvarı 'nı SQL Server erişmek Için yapılandırma](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access).

3. Azure-SSIS IR Şirket içindeki SQL Server bağlı bir Microsoft Azure Sanal Ağ ekleyin.  Daha fazla bilgi için bkz. [Azure-SSIS IR Microsoft Azure sanal ağ](./join-azure-ssis-integration-runtime-virtual-network.md)ekleme.

4. `catalog.set_execution_credential`Bu makalede açıklandığı gibi kimlik bilgilerini sağlamak IÇIN SSSıSDB saklı yordamını kullanın.

## <a name="connect-to-a-file-share-on-premises"></a>Şirket içinde bir dosya paylaşımıyla bağlantı

Şirket içinde bir dosya paylaşımıyla bağlanıp bağlanamaıp bağlanamayacağını denetlemek için aşağıdaki işlemleri yapın:

1. Bu testi çalıştırmak için, etki alanına katılmış olmayan bir bilgisayar bulun.

2. Etki alanına katılmış olmayan bilgisayarda aşağıdaki komutları çalıştırın. Bu komutlar, kullanmak istediğiniz etki alanı kimlik bilgileriyle bir komut istemi penceresi açar ve ardından dizin listesini alarak şirket içindeki dosya paylaşımıyla bağlantıyı test edin.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Şirket içindeki dosya paylaşımının dizin listesinin döndürülüp döndürülmediğini denetleyin.

### <a name="prerequisites"></a>Önkoşullar

Şirket içindeki bir dosya paylaşımıyla Azure 'da çalışan paketlerden erişmek için aşağıdaki işlemleri yapın:

1. Windows Güvenlik Duvarı üzerinden erişime izin ver.

2. Azure-SSIS IR, Şirket içindeki dosya paylaşımıyla bağlantılı bir Microsoft Azure Sanal Ağ ekleyin.  Daha fazla bilgi için bkz. [Azure-SSIS IR Microsoft Azure sanal ağ](./join-azure-ssis-integration-runtime-virtual-network.md)ekleme.

3. `catalog.set_execution_credential`Bu makalede açıklandığı gibi kimlik bilgilerini sağlamak IÇIN SSSıSDB saklı yordamını kullanın.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Azure VM 'de bir dosya paylaşımıyla bağlantı

Azure VM 'deki bir dosya paylaşımıyla Azure 'da çalışan paketlerden erişmek için aşağıdaki işlemleri yapın:

1. SSMS veya başka bir araçla, SSSıSDB barındıran SQL veritabanı/SQL yönetilen örneğine bağlanın. Daha fazla bilgi için bkz. [Azure 'DA SSıSDB 'ye bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Geçerli veritabanı olarak SSSıSDB ile bir sorgu penceresi açın.

3. Aşağıdaki saklı yordamı çalıştırın ve uygun etki alanı kimlik bilgilerini sağlayın:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Azure dosyalarındaki bir dosya paylaşımıyla bağlantı

Azure dosyaları hakkında daha fazla bilgi için bkz. [Azure dosyaları](https://azure.microsoft.com/services/storage/files/).

Azure dosyalarındaki bir dosya paylaşımıyla Azure 'da çalışan paketlerden erişmek için aşağıdaki işlemleri yapın:

1. SSMS veya başka bir araçla, SSSıSDB barındıran SQL veritabanı/SQL yönetilen örneğine bağlanın. Daha fazla bilgi için bkz. [Azure 'DA SSıSDB 'ye bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Geçerli veritabanı olarak SSSıSDB ile bir sorgu penceresi açın.

3. Aşağıdaki saklı yordamı çalıştırın ve uygun etki alanı kimlik bilgilerini sağlayın:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>Sonraki adımlar

- Paketlerinizi dağıtın. Daha fazla bilgi için bkz. [SSMS Ile Azure 'a BIR SSIS projesi dağıtma](/sql/integration-services/ssis-quickstart-deploy-ssms).
- Paketlerinizi çalıştırın. Daha fazla bilgi için bkz. [SSMS Ile Azure 'DA SSIS paketleri çalıştırma](/sql/integration-services/ssis-quickstart-run-ssms).
- Paketlerinizi zamanlayın. Daha fazla bilgi için bkz. [Azure 'DA SSIS paketlerini zamanlama](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).
