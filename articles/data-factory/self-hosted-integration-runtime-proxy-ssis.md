---
title: Kendini barındırılan tümleştirme çalışma zamanını SSIS için proxy olarak yapılandırma
description: Şirket içinde barındırılan tümleştirme çalışma zamanının bir Azure-SSIS Integration Runtime proxy olarak nasıl yapılandırılacağını öğrenin.
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/19/2020
ms.openlocfilehash: 5f393e95a7d468ea2c6130077e42b25b038e839d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376233"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Azure Data Factory içinde Azure-SSIS IR için otomatik olarak barındırılan bir IR ara sunucu olarak yapılandırma

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu makalede, proxy olarak yapılandırılan şirket içinde barındırılan tümleştirme çalışma zamanı (Şirket içinde barındırılan IR) ile Azure Data Factory SQL Server Integration Services (SSIS) paketlerinin bir Azure-SSIS Integration Runtime (Azure-SSIS IR) üzerinde nasıl çalıştırılacağı açıklanmaktadır. 

Bu özellikle, [Azure-SSIS IR bir sanal ağa katmak](./join-azure-ssis-integration-runtime-virtual-network.md)zorunda kalmadan şirket içi verilere erişebilirsiniz. Bu özellik, kurumsal ağınızın bir yapılandırması çok karmaşık olduğunda veya bir ilke Azure-SSIS IR eklemek için çok fazla kısıtlayıcı olduğunda faydalıdır.

Bu özellik, SSIS veri akışı görevlerinizi uygun olduğunda iki hazırlama görevine ayırır: 
* **Şirket içi hazırlama görevi**: Bu görev, şirket IÇINDE barındırılan IR üzerinde şirket içi veri deposuna bağlanan veri akışı bileşeninizi çalıştırır. Verileri şirket içi veri deposundan Azure Blob depolama alanındaki bir hazırlama alanına veya tam tersi yönde taşıdıkça.
* **Bulut hazırlama görevi**: Bu görev, Azure-SSIS IR şirket içi veri deposuna bağlanmeyen veri akışı bileşeninizi çalıştırır. Verileri Azure Blob depolama alanındaki hazırlama alanından bir bulut veri deposuna veya tam tersi yönde taşıırlar.

Veri akışı görevinde verileri Şirket içinden buluta taşıdıysanız, ilk ve ikinci hazırlama görevleri sırasıyla Şirket içi ve bulut hazırlama görevleri olacaktır. Veri akışı göreviniz verileri buluttan şirket içine taşıdıysanız, ilk ve ikinci hazırlama görevleri sırasıyla bulut ve şirket içi hazırlama görevleri olacaktır. Veri akışı göreviniz Şirket içinden şirket içine veri taşıdıysanız, ilk ve ikinci hazırlama görevleri hem şirket içi hazırlama görevleri olacaktır. Veri akışı göreviniz verileri buluttan buluta taşıdıysa, bu özellik geçerli değildir.

Bu özelliğin diğer avantajları ve özellikleri, örneğin, henüz bir Azure-SSIS IR tarafından desteklenmeyen bölgelerde şirket içinde barındırılan IR ayarlamanıza ve veri kaynaklarınızın güvenlik duvarında şirket içinde barındırılan IR 'nizin genel statik IP adresine izin verir.

## <a name="prepare-the-self-hosted-ir"></a>Şirket içinde barındırılan IR 'yi hazırlama

Bu özelliği kullanmak için, önce bir veri fabrikası oluşturun ve içinde bir Azure-SSIS IR ayarlayın. Daha önce yapmadıysanız, [Azure-SSIS IR ayarlama](./tutorial-deploy-ssis-packages-azure.md)bölümündeki yönergeleri izleyin.

Daha sonra, Azure-SSIS IR ayarlandığı veri fabrikasında kendi kendine barındırılan IR 'yi ayarlarsınız. Bunu yapmak için bkz. [Şirket içinde BARıNDıRıLAN IR oluşturma](./create-self-hosted-integration-runtime.md).

Son olarak, şirket içi makinenize veya Azure sanal makinenize (VM) ek sürücü ve çalışma zamanının yanı sıra şirket içinde barındırılan IR 'nin en son sürümünü indirip yüklersiniz:
- [Şirket içinde BARıNDıRıLAN IR](https://www.microsoft.com/download/details.aspx?id=39717)'nin en son sürümünü indirip yükleyin.
- Paketlerinizdeki nesne bağlama ve katıştırma veritabanı (OLEDB), açık veritabanı bağlantısı (ODBC) veya ADO.NET bağlayıcıları kullanıyorsanız, zaten yapmadıysanız, ilgili sürücüleri şirket içinde barındırılan IR 'nin yüklü olduğu makinede indirin ve yükleyin.  

  SQL Server (SQL Server Native Client [SQLNCLI]) için OLEDB sürücüsünün önceki sürümünü kullanıyorsanız, [64 bitlik sürümü indirin](https://www.microsoft.com/download/details.aspx?id=50402).  

  SQL Server (MSOLEDBSQL) için OLEDB sürücüsünün en son sürümünü kullanıyorsanız, [64 bitlik sürümü indirin](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  PostgreSQL, MySQL, Oracle vb. gibi diğer veritabanı sistemleri için OLEDB/ODBC/ADO. NET sürücüleri kullanıyorsanız, 64 bitlik sürümleri Web sitelerinden indirebilirsiniz.
- Şimdiye kadar yapmadıysanız, [Visual C++ (VC) çalışma zamanının 64 bitlik sürümünü](https://www.microsoft.com/download/details.aspx?id=40784) , şirket IÇINDE barındırılan IR 'nin yüklü olduğu makinede indirin ve yükleyin.

### <a name="enable-windows-authentication-for-on-premises-staging-tasks"></a>Şirket içi hazırlama görevleri için Windows kimlik doğrulamasını etkinleştirme

Şirket içinde barındırılan IR 'de şirket içi hazırlama görevleri Windows kimlik doğrulaması gerektiriyorsa, [SSIS paketlerinizi aynı Windows kimlik doğrulamasını kullanacak şekilde yapılandırın](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). 

Şirket içi hazırlama görevleriniz, kendi kendine barındırılan IR Hizmeti hesabıyla (varsayılan olarak *NT SERVICE\DIAHostService*) çağrılacaktır ve veri depolarınız Windows kimlik doğrulama hesabıyla erişilecek. Her iki hesap de belirli güvenlik ilkelerinin atanmasını gerektirir. Şirket içinde barındırılan IR makinesinde **yerel güvenlik ilkesi**  >  **Yerel ilkeler**  >  **Kullanıcı hakları ataması**' na gidin ve ardından aşağıdakileri yapın:

1. *Bir işlem için bellek kotalarını ayarlama* ve *bir işlem düzeyi belirteç* ILKELERINI şirket içinde barındırılan IR Hizmeti hesabıyla değiştirme. Bu, kendi kendine barındırılan IR 'yi varsayılan hizmet hesabıyla yüklediğinizde otomatik olarak gerçekleşir. Değilse, bu ilkeleri el ile atayın. Farklı bir hizmet hesabı kullanıyorsanız, buna aynı ilkeleri atayın.

1. Windows kimlik doğrulama hesabına *hizmet olarak oturum* açma ilkesi atayın.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Azure Blob depolama bağlı hizmetini hazırlama için hazırlama

Daha önce yapmadıysanız, Azure-SSIS IR ayarlandığı veri fabrikasında bir Azure Blob depolama bağlı hizmeti oluşturun. Bunu yapmak için bkz. [Azure Data Factory 'ye bağlı hizmet oluşturma](./quickstart-create-data-factory-portal.md#create-a-linked-service). Aşağıdakileri yaptığınızdan emin olun:
- **Veri depolama** Için **Azure Blob depolama**' yı seçin.  
- **Tümleştirme çalışma zamanı aracılığıyla Bağlan** Için, Azure Blob depolama alanınızı erişim kimlik bilgilerini getirmek üzere varsayılan Azure IR kullandığımızda **Otomatik resolveıntegrationruntime** (Azure-SSIS IR ve kendi içinde barındırılan IR) seçeneğini belirleyin.
- **Kimlik doğrulama yöntemi** için **hesap anahtarı**, **SAS URI 'Si**, **hizmet sorumlusu** veya **yönetilen kimlik**' i seçin.  

>[!TIP]
>**Hizmet sorumlusu** yöntemini seçerseniz, hizmet sorumlusuna en az bir *Depolama Blobu veri katılımcısı* rolü verin. Daha fazla bilgi için bkz. [Azure Blob Storage Bağlayıcısı](connector-azure-blob-storage.md#linked-service-properties). **Yönetilen kimlik** yöntemini seçerseniz, Azure Blob depolamaya erışmek için ADF ile yönetilen kimlik uygun rollerinizi verin. Daha fazla bilgi için bkz. [ADF ile yönetilen kimlikle Azure Active Directory kimlik doğrulaması kullanarak Azure Blob depolamaya erişme](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication).

![Azure Blob depolama ile bağlantılı hizmeti hazırlama için hazırlama](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Ara sunucu olarak şirket içinde barındırılan IR ile bir Azure-SSIS IR yapılandırma

Şirket içinde barındırılan IR ve Azure Blob depolama ile bağlantılı hizmetinizi hazırlama için hazırladığınıza göre, artık yeni veya mevcut Azure-SSIS IR, Veri Fabrikası portalınızdaki veya uygulamanızda ara sunucu olarak şirket içinde barındırılan IR ile yapılandırabilirsiniz. Bunu yapmadan önce, mevcut Azure-SSIS IR zaten çalışıyorsa, durdurun ve yeniden başlatın.

1. **Tümleştirme çalışma zamanı kurulumu** bölmesinde, **İleri**' yi seçerek **Genel ayarlar** ve **SQL ayarları** bölümlerinin ötesine atlayın. 

1. **Gelişmiş ayarlar** bölümünde şunları yapın:

   1. Azure-SSIS Integration Runtime onay kutusu **için proxy olarak Self-Hosted ayarlama Integration Runtime** seçin. 

   1. Şirket içinde **barındırılan Integration Runtime** açılır listesinde, Azure-SSIS IR için mevcut şirket IÇINDE barındırılan IR 'yi bir ara sunucu olarak seçin.

   1. **Hazırlama depolama bağlı hizmeti** aşağı açılan listesinde, mevcut Azure Blob depolama ile bağlantılı hizmetinizi seçin veya hazırlama için yeni bir tane oluşturun.

   1. **Hazırlama yolu** kutusunda, seçili Azure Blob depolama hesabınızda bir blob kapsayıcısı belirtin veya hazırlama için varsayılan bir tane kullanmak üzere boş bırakın.

   1. **Devam**’ı seçin.

   ![Şirket içinde barındırılan IR ile gelişmiş ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Ayrıca, PowerShell kullanarak, yeni veya mevcut Azure-SSIS IR şirket içinde barındırılan IR ile proxy olarak yapılandırabilirsiniz.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>SSIS paketlerini proxy ile bağlanacak şekilde etkinleştir

Visual Studio için SSIS projeleri uzantısı ya da tek başına bir yükleyici olarak en son SSDT 'yi kullanarak, `ConnectByProxy` desteklenen veri akışı bileşenlerine yönelik bağlantı yöneticilerine eklenmiş yeni bir özellik bulabilirsiniz.
* [Visual Studio için SSIS projeleri uzantısını indirin](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Tek başına yükleyiciyi indirme](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)   

Şirket içindeki verilere erişen bileşenlerle veri akışı görevleri içeren yeni paketler tasarladığınızda, ilgili bağlantı yöneticilerinin **Özellikler** bölmesinde bu özelliği *true* olarak ayarlayarak etkinleştirebilirsiniz.

![ConnectByProxy özelliğini etkinleştir](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Bu özelliği, var olan paketleri çalıştırdığınızda, bunları bir tane ile el ile değiştirmek zorunda kalmadan da etkinleştirebilirsiniz.  İki seçenek vardır:
- **Seçenek A**: Azure-SSIS IR için en son SSDT ile bu paketleri içeren projeyi açın, yeniden oluşturun ve yeniden dağıtın. Daha sonra ilgili bağlantı yöneticileri için özelliği *true* olarak ayarlayarak özelliği etkinleştirebilirsiniz. SSMS 'den paket çalıştırırken, bu bağlantı yöneticileri **paket Çalıştır** açılır penceresinin **bağlantı yöneticileri** sekmesinde görüntülenir.

  ![ConnectByProxy Property2 etkinleştir](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Ayrıca, Data Factory işlem hatları 'nda paket çalıştırırken [SSIS paketi 'Ni Çalıştır etkinliğinin](./how-to-invoke-ssis-package-ssis-activity.md) **bağlantı yöneticileri** sekmesinde görünen ilgili bağlantı yöneticileri için özelliği *true* olarak ayarlayarak da etkinleştirebilirsiniz.
  
  ![ConnectByProxy property3 etkinleştir](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Seçenek B:** SSIS IR 'niz üzerinde çalışmak üzere bu paketlerin bulunduğu projeyi yeniden dağıtın. Daha sonra özellik yolunu sağlayarak özelliği etkinleştirebilir `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` ve paketleri SSMS 'den çalıştırırken **paket Çalıştır** açılır penceresinin **Gelişmiş** sekmesinde özellik geçersiz kılma olarak *true* olarak ayarlayabilirsiniz.

  ![ConnectByProxy property4 etkinleştir](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Ayrıca, özellik yolunu sağlayarak özelliği etkinleştirebilir `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` ve Data Factory işlem hatlarında paket ÇALıŞTıRıRKEN [SSIS paketi yürütme etkinliğinin](./how-to-invoke-ssis-package-ssis-activity.md) **geçersiz** kılınması sekmesinde özellik geçersiz kılma olarak *true olarak ayarlanır* .
  
  ![ConnectByProxy Property5 etkinleştir](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-on-premises-and-cloud-staging-tasks"></a>Şirket içi ve bulut hazırlama görevlerinde hata ayıklayın

Şirket içinde barındırılan IR 'de, çalışma zamanı günlüklerini *C:\ProgramData\SSISTelemetry* klasöründe ve şirket içi hazırlama görevlerinin yürütme günlüklerinde *C:\ProgramData\SSISTelemetry\ExecutionLog* klasöründe bulabilirsiniz.  Bulut hazırlama görevlerinin yürütme günlüklerini SSıSDB 'de, belirtilen günlük dosyası yolunuzda veya Azure Izleyici 'de paketlerinizi SSSıSDB 'de depoladığınıza, [Azure izleyici tümleştirmesine](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor), vb. bir şekilde bulabilirsiniz. Ayrıca, bulut hazırlama görevlerinin yürütme günlüklerinde şirket içi hazırlama görevlerinin benzersiz kimliklerini de bulabilirsiniz. 

![İlk hazırlama görevinin benzersiz KIMLIĞI](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

Müşteri desteği biletleri yaptıysanız, araştırılması için en son işlem/yürütme günlüklerini göndermek üzere şirket içinde barındırılan IR 'de yüklü **Microsoft Integration Runtime Configuration Manager** **Tanılama** sekmesinde **Günlükler gönder** düğmesini seçebilirsiniz.

## <a name="billing-for-the-on-premises-and-cloud-staging-tasks"></a>Şirket içi ve bulut hazırlama görevleri için faturalandırma

Şirket içinde barındırılan IR üzerinde çalışan şirket içi hazırlama görevleri, tek bir şekilde faturalandırılır, böylece şirket içinde barındırılan bir IR üzerinde çalışan tüm veri taşıma etkinlikleri faturalandırılır. Bu, [Azure Data Factory veri işlem hattı fiyatlandırma](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) makalesinde belirtilmiştir.

Azure-SSIS IR çalışan bulut hazırlama görevleri ayrı olarak faturalandırılmaz, ancak çalıştırdığınız Azure-SSIS IR [Azure-SSIS IR fiyatlandırma](https://azure.microsoft.com/pricing/details/data-factory/ssis/) makalesinde belirtilen şekilde faturalandırılır.

## <a name="enable-custom3rd-party-components"></a>Özel/üçüncü taraf bileşenlerini etkinleştir 

Özel/üçüncü taraf bileşenlerinizin, Azure-SSIS IR için proxy olarak şirket içinde barındırılan IR kullanarak şirket içi verilere erişmesini sağlamak için aşağıdaki yönergeleri izleyin:

1. [Standart/Express özel kurulumları](./how-to-configure-azure-ssis-ir-custom-setup.md)aracılığıyla Azure-SSIS IR SQL Server 2017 hedefleyen özel/3. taraf bileşenlerinizi yükler.

1. Zaten mevcut değilse, şirket içinde barındırılan IR 'de aşağıdaki DTSPath kayıt defteri anahtarlarını oluşturun:
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` ayarla `C:\Program Files\Microsoft SQL Server\140\DTS\`
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` ayarla `C:\Program Files (x86)\Microsoft SQL Server\140\DTS\`
   
1. Yukarıdaki DTSPath altındaki şirket içinde barındırılan IR 'ye SQL Server 2017 ' i hedefleyen özel/3. taraf bileşenlerinizi yükleme ve yükleme işleminizin şu şekilde olduğundan emin olun:

   1. `<DTSPath>`Zaten mevcut değilse,, `<DTSPath>/Connections` `<DTSPath>/PipelineComponents` ve `<DTSPath>/UpgradeMappings` klasörleri oluşturur.
   
   1. Klasöründe uzantı eşlemeleri için kendi XML dosyanızı oluşturur `<DTSPath>/UpgradeMappings` .
   
   1. Özel/3. taraf bileşen derlemeleriniz tarafından başvurulan tüm derlemeleri genel derleme önbelleği 'ne (GAC) kurar.

İş ortaklarımızın sunduğu [Obald yazılımlarının](https://kb.theobald-software.com/xtract-is/XIS-for-Azure-SHIR) ve akayıtlarımızda [](https://www.aecorsoft.com/blog/2020/11/8/using-azure-data-factory-to-bring-sap-data-to-azure-via-self-hosted-ir-and-ssis-ir), bir Azure-SSIS IR ara sunucu olarak hızlı özel kurulum ve şirket içinde barındırılan IR 'yi kullanmak için bileşenlerini uyarlayan örnekler aşağıda verilmiştir.

## <a name="enforce-tls-12"></a>TLS 1.2’yi zorlama

Güçlü şifreleme/daha güvenli ağ protokolü (TLS 1,2) kullanmanız ve şirket içinde barındırılan IR 'de eski SSL/TLS sürümlerini devre dışı bırakmanız gerekirse, genel önizleme kapsayıcımız için *Customsetupscript/Usersenaryolar/tls 1,2* klasöründe bulunan *Main. cmd* betiğini indirebilir ve çalıştırabilirsiniz.  [Azure Depolama Gezgini](https://storageexplorer.com/)kullanarak, aşağıdaki SAS URI 'sini girerek genel önizleme kapsayıcımıza bağlanabilirsiniz:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Geçerli sınırlamalar

- Yalnızca Hadoop/,/DQS bileşenleri hariç Azure-SSIS IR Standard sürümünde yerleşik/önceden yüklenmiş olan veri akışı bileşenleri Şu anda desteklenmektedir: [Azure-SSIS IR üzerinde tüm yerleşik/önceden yüklenmiş bileşenler](./built-in-preinstalled-components-ssis-integration-runtime.md).
- Yalnızca yönetilen kodda (.NET Framework) yazılan özel/3. taraf veri akışı bileşenleri Şu anda desteklenmektedir-yerel kodda (C++) yazılmış olanlar şu anda desteklenmemektedir.
- Hem şirket içi hem de bulut hazırlama görevlerinde değişken değerlerini değiştirmek Şu anda desteklenmemektedir.
- Şirket içi hazırlama görevlerinde tür nesnesinin değişken değerlerini değiştirmek diğer görevlere yansıtılmaz.
- OLEDB kaynağında *Parametermapping* Şu anda desteklenmiyor. Geçici bir çözüm olarak, lütfen bir SQL komutuna değişkenlerinizi/parametrelerini eklemek için ' den *AccessMode* olarak *değişkenden SQL komutunu* kullanın ve *ifadesini* kullanın. Bir çizim olarak, genel önizleme kapsayımızın *Selfhostedirproxy/sınırlamalar* klasöründe bulunan *parametermappingsample. dtsx* paketine bakın. Azure Depolama Gezgini kullanarak, yukarıdaki SAS URI 'sini girerek genel önizleme kapsayıcımıza bağlanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Şirket içinde barındırılan IR 'yi, Azure-SSIS IR için bir proxy olarak yapılandırdıktan sonra, Data Factory işlem hatlarında SSIS paketi etkinliklerini yürütmek üzere paketlerinizi dağıtıp çalıştırabilirsiniz. Nasıl yapılacağını öğrenmek için bkz. [Data Factory işlem hatları 'nda SSIS paketlerini yürütme SSIS paketi etkinlikleri](./how-to-invoke-ssis-package-ssis-activity.md).