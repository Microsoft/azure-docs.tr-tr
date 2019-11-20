---
title: Azure özellikli dtexec yardımcı programıyla SQL Server Integration Services (SSIS) paketlerini yürütme
description: Azure özellikli dtexec yardımcı programıyla SQL Server Integration Services (SSIS) paketlerini yürütmeyi öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 9ab308d0e2145a0d0b40e8b37c8c5be07b55dac6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73673547"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Azure özellikli dtexec yardımcı programıyla SQL Server Integration Services paketlerini çalıştırma
Bu makalede, Azure etkinleştirilmiş dtexec (AzureDTExec) komut istemi yardımcı programı açıklanır. SQL Server Integration Services (SSIS) paketlerini Azure Data Factory Azure-SSIS Integration Runtime (IR) üzerinde çalıştırmak için kullanılır.

Geleneksel dtexec yardımcı programı SQL Server gelir. Daha fazla bilgi için bkz. [dtexec yardımcı programı](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Genellikle, şirket içi SSIS paketlerini çalıştırmak için ActiveBatch ve Control-d gibi üçüncü taraf düzenleyiciler veya zamanlayıcılar tarafından çağrılır. 

Modern AzureDTExec yardımcı programı bir SQL Server Management Studio (SSMS) aracıyla birlikte gelir. Ayrıca, Azure 'da SSIS paketlerini çalıştırmak için üçüncü taraf düzenleyiciler veya zamanlayıcılar tarafından çağrılabilir. SSIS paketlerinizin, buluta geçişini ve geçirilmesini veya geçirilmesini kolaylaştırır. Geçişten sonra, günlük işlemlarınızda üçüncü taraf düzenleyiciler veya zamanlayıcılar kullanmaya devam etmek istiyorsanız, artık dtexec yerine AzureDTExec ' ı çağırabilir.

AzureDTExec, paketlerinizi Data Factory işlem hatları 'nda SSIS paket etkinliklerini yürütme olarak çalıştırır. Daha fazla bilgi için bkz. [SSIS paketlerini Azure Data Factory etkinlikleri olarak çalıştırma](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

AzureDTExec, veri fabrikanıza işlem hatları üreten bir Azure Active Directory (Azure AD) uygulaması kullanmak için SSMS aracılığıyla yapılandırılabilir. Ayrıca, paketlerinizi depoladığınız dosya sistemlerine, dosya paylaşımlarına veya Azure dosyalarına erişecek şekilde yapılandırılabilir. AzureDTExec, çağırma seçeneklerine verdiğiniz değerlere bağlı olarak, içinde bir SSIS paketi yürütme etkinliği ile benzersiz bir Data Factory işlem hattı oluşturur ve çalıştırır. Options için aynı değerlerle AzureDTExec çağırma, mevcut ardışık düzeni yeniden çalıştırır.

## <a name="prerequisites"></a>Ön koşullar
AzureDTExec 'yi kullanmak için sürüm 18,3 veya üzeri olan SSMS 'nin en son sürümünü indirip yükleyin. [Bu Web sitesinden](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)indirin.

## <a name="configure-the-azuredtexec-utility"></a>AzureDTExec yardımcı programını yapılandırma
SSMS 'nin yerel makinenizde yüklenmesi de AzureDTExec ' i de yüklüyor. Ayarlarını yapılandırmak için SSMS 'yi **yönetici olarak çalıştır** seçeneğiyle başlatın. Ardından **Araçlar** ' ı seçin > Azure **'A geçir** > **Azure özellikli dtexec 'ı yapılandırın**.

![Azure etkin dtexec menüsünü yapılandırma](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Bu eylem, *Azuredtexec. Settings* dosyasına yazmak için yönetim ayrıcalıklarıyla açılması gereken bir **Azuredtexecconfig** penceresi açar. SSMS 'yi yönetici olarak çalıştırmadığınız takdirde, bir kullanıcı hesabı denetimi (UAC) penceresi açılır. Ayrıcalıklarınızı yükseltmek için yönetici parolanızı girin.

![Azure etkin dtexec ayarlarını yapılandırma](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

**Azuredtexecconfig** penceresinde yapılandırma ayarlarınızı aşağıdaki gibi girin:

- **ApplicationId**: veri fabrikanıza işlem hatları oluşturmak için doğru Izinlerle oluşturduğunuz Azure AD uygulamasının benzersiz tanımlayıcısını girin. Daha fazla bilgi için bkz. [Azure Portal aracılığıyla Azure AD uygulaması ve hizmet sorumlusu oluşturma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).
- **Authenticationkey**: Azure AD uygulamanızın kimlik doğrulama anahtarını girin.
- **Tenantıd**: Azure AD uygulamanızın altında OLUŞTURULACAĞı Azure AD kiracının benzersiz tanımlayıcısını girin.
- **SubscriptionID**: veri fabrikanızın oluşturulduğu Azure aboneliğinin benzersiz tanımlayıcısını girin.
- **ResourceGroup**: veri fabrikanızın oluşturulduğu Azure Kaynak grubunun adını girin.
- **DataFactory**: Ssredtexec 'yi çağırdığınızda belirtilen seçenek değerlerine dayanarak, içinde SSIS paketi yürütme etkinliği ile benzersiz işlem hatları oluşturulan veri fabrikasının adını girin.
- **Irname**: veri fabrikanızdaki Azure-SSIS IR adını girin, bu, ' ın evrensel adlandırma KURALı (UNC) yolunda belirtilen paketlerin AzureDTExec ' ı çağırdığınızda çalışacağını belirtin.
- **Packageaccessdomain**: AzureDTExec 'yi ÇAĞıRDıĞıNıZDA belirtilen UNC yolundaki paketlerinize erişmek için etki alanı kimlik bilgilerini girin.
- **Packageaccessusername**: AzureDTExec 'yi ÇAĞıRDıĞıNıZDA belirtilen UNC yolundaki paketlerinize erişmek için Kullanıcı adı bilgilerini girin.
- **Packageaccesspassword**: AzureDTExec 'yi ÇAĞıRDıĞıNıZDA belirtilen UNC yolundaki paketlerinize erişmek için parola kimlik bilgisini girin.
- **LogPath**: günlük klasörünün UNC yolunu, Azure-SSIS IR paket yürütmelerinin yazıldığı günlük dosyasına girin.
- **LogLevel**: Azure-SSIS IR paket yürütmeleri için önceden tanımlanmış **null**, **temel**, **ayrıntılı**veya **performans** seçeneklerinden günlük kaydının seçili kapsamını girin.
- **Logaccessdomain**: **logPath** belirtildiğinde ve **LogLevel** **null**olmadığında, günlük klasörünüze yazmak için, UNC yolundaki günlük klasörünüze erişmek için etki alanı kimlik bilgisini girin.
- **Logaccessusername**: **logPath** belirtildiğinde ve **LogLevel** **null**olmadığında, günlük klasörünüz için UNC yolundaki günlük klasörünüze erişmek için Kullanıcı adı kimlik bilgisini girin.
- **Logaccesspassword**: **logPath** belirtildiğinde ve **LogLevel** **null**olmadığında, günlük klasörünüz için UNC yolundaki günlük klasörünüze erişmek üzere parola kimlik bilgisini girin.
- **Ardışık düzen ınenamehashstrlen**: AzureDTExec 'yi çağırdığınızda sağladığınız seçenek değerlerinden oluşturulacak karma dizelerin uzunluğunu girin. Dizeler, paketlerinizi Azure-SSIS IR çalıştıran Data Factory işlem hatları için benzersiz adlar oluşturmak üzere kullanılır. Genellikle 32 karakterlik bir uzunluk yeterlidir.

Dosya sistemlerinde veya Şirket içindeki dosya paylaşımlarında paketlerinizi ve günlük dosyalarınızı depolamak için, Azure-SSIS IR paketlerinizi getirip günlük dosyalarınızı yazabilmesi için, şirket içi ağınıza bağlı bir sanal ağa katın. Daha fazla bilgi için bkz. bir [Azure-SSIS IR sanal ağa ekleme](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

*Azuredtexec. Settings* dosyasına yazılmış gizli değerlerin düz metin olarak gösterilmesini önlemek Için bunları Base64 kodlaması dizelerine kodlarız. AzureDTExec 'yi çağırdığınızda, tüm Base64 ile kodlanmış dizelerin özgün değerlerine yeniden çözülür. Bu hesaba erişebilen hesapları sınırlayarak *Azuredtexec. Settings* dosyasını daha da güvenli hale getirebilirsiniz.

## <a name="invoke-the-azuredtexec-utility"></a>AzureDTExec yardımcı programını çağırma
Komut satırı isteminde AzureDTExec ' i çağırabilir ve kullanım örneği senaryonuzun belirli seçenekler için ilgili değerleri sağlayabilirsiniz.

Yardımcı program `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`yüklendi. Her yerden çağrılması için yolunu ' PATH ' ortam değişkenine ekleyebilirsiniz.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

AzureDTExec çağırma, dtexec 'yi çağırarak benzer seçenekler sunar. Daha fazla bilgi için bkz. [dtexec yardımcı programı](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Şu anda desteklenen seçenekler şunlardır:

- **/F [ile]** : dosya sistemi, dosya paylaşımında veya Azure dosyalarında depolanan bir paketi yükler. Bu seçeneğin değeri olarak dosya sistemi, dosya paylaşımında veya Azure dosyaları. dtsx uzantısıyla birlikte paket dosyanız için UNC yolunu belirtebilirsiniz. Belirtilen UNC yolu herhangi bir boşluk içeriyorsa, tam yolun çevresine tırnak işareti koyun.
- **/Conf [ıgfile]** : değerlerin ayıklanacağı bir yapılandırma dosyasını belirtir. Bu seçeneği kullanarak, paketiniz için tasarım zamanında belirtilenden farklı bir çalışma zamanı yapılandırması belirleyebilirsiniz. Farklı ayarları bir XML yapılandırma dosyasında depolayıp paket yürütmeden önce yükleyebilirsiniz. Daha fazla bilgi için bkz. [SSIS paketi yapılandırması](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017). Bu seçeneğin değerini belirtmek için, dosya sistemi, dosya paylaşımında veya Azure dosyalarında dtsConfig uzantılı yapılandırma dosyanız için UNC yolunu kullanın. Belirtilen UNC yolu herhangi bir boşluk içeriyorsa, tam yolun çevresine tırnak işareti koyun.
- **/Conn [ection]** : paketinizdeki mevcut bağlantı yöneticileri için bağlantı dizelerini belirtir. Bu seçeneği kullanarak, paketinizdeki mevcut bağlantı yöneticileri için tasarım zamanında belirtilenlerden farklı çalışma zamanı bağlantı dizeleri ayarlayabilirsiniz. Bu seçenek için değeri şu şekilde belirtin: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`.
- **/Set**: paketteki bir parametrenin, değişkenin, özelliğin, kapsayıcının, günlük sağlayıcının, Foreach Numaralandırıcı veya bağlantının yapılandırmasını geçersiz kılar. Bu seçenek birden çok kez belirtilebilir. Bu seçenek için değeri şu şekilde belirtin: `property_path;value`. Örneğin, `\package.variables[counter].Value;1` `counter` değişkeninin değerini 1 olarak geçersiz kılar. Paketinizdeki değeri geçersiz kılmak istediğiniz öğelerin `property_path` değerini bulmak, kopyalamak ve yapıştırmak için **paket yapılandırma** sihirbazını kullanabilirsiniz. Daha fazla bilgi için bkz. [paket Yapılandırma Sihirbazı](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014).
- **/De [Crypt]** : **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** koruma düzeyiyle yapılandırılmış paketinizin şifre çözme parolasını ayarlar.

> [!NOTE]
> Options için yeni değerlerle AzureDTExec çağırmak, **/de [cript]** seçeneği dışında yeni bir işlem hattı oluşturur.

## <a name="next-steps"></a>Sonraki adımlar

SSIS paketini Yürüt etkinliğinin bulunduğu benzersiz işlem hatları, AzureDTExec çağrıldıktan sonra oluşturulup çalıştırılır. Bu, Data Factory portalında izlenebilirler. Daha fazla bilgi için bkz. [SSIS paketlerini Data Factory etkinlikleri olarak çalıştırma](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

> [!WARNING]
> Oluşturulan işlem hattının yalnızca AzureDTExec tarafından kullanılması beklenmektedir. Özellikleri veya parametreleri gelecekte değişebilir, bu nedenle başka amaçlar için bunları değiştirmeyin veya yeniden kullanmayın. Değişiklikler AzureDTExec ile kesilebilir. Bu durumda, işlem hattını silin. AzureDTExec, bir sonraki çağrılışında yeni bir işlem hattı oluşturur.
