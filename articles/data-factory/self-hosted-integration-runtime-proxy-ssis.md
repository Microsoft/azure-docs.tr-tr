---
title: Şirket içinde barındırılan tümleştirme çalışma zamanını SSSıS için proxy olarak Yapılandırma Azure Data Factory
description: Şirket içinde barındırılan Integration Runtime Azure-SSIS Integration Runtime için proxy olarak yapılandırmayı öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/12/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 55abdab6a427547ee8bd498500deee94b8f67453
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954736"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>ADF 'de Azure-SSIS IR için şirket içinde barındırılan IR 'yi bir ara sunucu olarak yapılandırma
Bu makalede, proxy olarak yapılandırılan şirket içinde barındırılan IR ile Azure Data Factory (ADF) üzerinde Azure-SSIS Integration Runtime (IR) için SQL Server Integration Services (SSIS) paketlerinin nasıl çalıştırılacağı açıklanmaktadır.  Bu özellik, [Azure-SSIS IR bir sanal ağa katılmadan](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)Şirket içindeki verilere erişmenizi sağlar.  Bu, şirket ağınızda Azure-SSIS IR eklemek için aşırı karmaşık bir yapılandırma/kısıtlayıcı ilke olduğunda faydalıdır.

Bu özellik, şirket içi veri kaynağı ile bir veri akışı görevi içeren paketinizi iki hazırlama görevine bölecektir: şirket içinde barındırılan IR üzerinde çalışan ilk, verileri şirket içi veri kaynağından Azure Blob depolama alanındaki bir hazırlama alanına taşıyacaktır, ancak Azure-SSIS IR ikinci bir işlem, hazırlama alanındaki verileri amaçlanan veri hedefine taşır.

Bu özellik ayrıca, Azure-SSIS IR tarafından henüz desteklenmeyen bölgelerde şirket içinde barındırılan IR sağlamanıza olanak sağladığından diğer avantajlar/özellikler de sağlar, veri kaynaklarınızın güvenlik duvarında şirket içinde barındırılan IR 'nin genel statik IP adresine izin verir, vb.

## <a name="prepare-self-hosted-ir"></a>Şirket içinde barındırılan IR hazırlama
Bu özelliği kullanmak için önce bir ADF oluşturmanız Azure-SSIS IR ve bu işlemi henüz yapmadıysanız, [bir Azure-SSIS IR nasıl](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) sağlayacağınızı takip etmeniz gerekir.

Daha sonra, şirket içinde barındırılan IR 'nin [nasıl](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) oluşturulacağı ile Azure-SSIS IR aynı ADF altında otomatik olarak barındırılan IR sağlamanız gerekir.

Son olarak, şirket içi makinenize/Azure sanal makinenize (VM) aşağıdaki gibi otomatik olarak barındırılan IR 'nin ve ek sürücü ve çalışma zamanının en son sürümünü indirip yüklemeniz gerekir:
- Lütfen şirket içinde barındırılan IR 'nin en son sürümünü [buradan](https://www.microsoft.com/download/details.aspx?id=39717)indirin ve yükleyin.
- Paketlerinizdeki OLEDB bağlayıcıları kullanıyorsanız, lütfen ilgili OLEDB sürücülerini indirip şirket içinde barındırılan IR 'nin yüklendiği makinede yükleyin ve daha önce yapmadıysanız.  SQL Server (SQLNCLI) için OLEDB sürücüsünün önceki sürümünü kullanıyorsanız, 64 bitlik sürümü [buradan](https://www.microsoft.com/download/details.aspx?id=50402)indirebilirsiniz.  SQL Server (MSOLEDBSQL) için OLEDB sürücüsünün en son sürümünü kullanıyorsanız, 64 bitlik sürümü [buradan](https://www.microsoft.com/download/details.aspx?id=56730)indirebilirsiniz.  PostgreSQL, MySQL, Oracle vb. gibi diğer veritabanı sistemleri için OLEDB sürücüleri kullanıyorsanız, 64 bitlik sürümü ilgili web sitelerinden indirebilirsiniz.
- Lütfen daha önce yapmadıysanız, C++ Visual (VC) çalışma zamanını şirket IÇINDE barındırılan IR 'nin yüklendiği makinede indirip yükleyin.  64 bitlik sürümü [buradan](https://www.microsoft.com/download/details.aspx?id=40784)indirebilirsiniz.

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Azure Blob depolama bağlı hizmetini hazırlama için hazırlama
Lütfen, [ADF bağlantılı hizmet oluşturma](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) makalesini izleyerek Azure-SSIS IR SAĞLANDıĞı aynı ADF altında Azure Blob depolama bağlı hizmeti oluşturun ve daha önce yapmadıysanız.  Lütfen şunlardan emin olun:
- **Veri deposu** Için **Azure Blob depolama** seçildi
- **Tümleştirme çalışma zamanı aracılığıyla Bağlan** Için **oto resolveıntegrationruntime** seçildi
- **Kimlik doğrulama yöntemi** için **hesap anahtarı**/**SAS URI**/**hizmet sorumlusu** seçili olmalıdır

![Azure Blob depolama bağlı hizmetini hazırlama için hazırlama](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Ara sunucu olarak şirket içinde barındırılan IR ile Azure-SSIS IR yapılandırma
Şirket içinde barındırılan IR ve Azure Blob depolama bağlı hizmetinizi hazırlama için hazırladığınıza göre, yeni/mevcut Azure-SSIS IR, kendiliğinden konak IR ile, ADF portalında/uygulamada bir proxy olarak yapılandırabilirsiniz.  Mevcut Azure-SSIS IR çalışıyorsa, bunu yapmadan önce lütfen durdurun ve ardından yeniden başlatın.

**Gelişmiş ayarlar** sayfasında, lütfen Azure-SSIS Integration Runtime onay kutusu **Için şirket içinde barındırılan Integration Runtime ara sunucu olarak ayarlayın** , kendi kendine barındırılan IR ve Azure Blob depolama bağlı hizmetini hazırlama için seçin ve bir blob belirtin isterseniz **hazırlama yolu** için kapsayıcı adı.

![Ara sunucu olarak şirket içinde barındırılan IR ile Azure-SSIS IR yapılandırma](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-settings-ssisir.png)

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>SSIS paketlerini proxy ile bağlanacak şekilde etkinleştir
[Buradan indirilebilen ve buradan](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) indirilebilecek tek başına bir yükleyici olarak, Visual STUDIO için SSIS projeleri Uzantısı ile en son SSDT 'yi [kullanarak, OLEDB](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)'ye eklenen yeni bir **connectbyproxy** özelliği bulabilirsiniz/ Düz dosya bağlantı yöneticileri.  

Şirket içindeki veritabanlarına/dosyalara erişmek için OLEDB/düz dosya kaynaklarıyla veri akışı görevleri içeren yeni paketler tasarlarken, ilgili bağlantı yöneticilerinin Özellikler panelinde onu **true** olarak ayarlayarak bu özelliği etkinleştirebilirsiniz.

![ConnectByProxy özelliğini etkinleştir](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Bu özelliği, var olan paketleri bir tane ile tek tek değiştirmek zorunda kalmadan çalıştırırken de etkinleştirebilirsiniz.  2 seçenek vardır:
- Azure-SSIS IR için en son SSDT ile bu paketleri içeren projeyi açma, yeniden oluşturma ve yeniden dağıtma: Bu özellik daha sonra bağlantı üzerinde görünen ilgili bağlantı yöneticileri için **true** olarak ayarlanarak etkinleştirilebilirSSMS 'den paket çalıştırırken paket açılır penceresini Çalıştır ' ın Yöneticiler sekmesi.

  ![ConnectByProxy Property2 etkinleştir](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Özelliği, ADF işlem hatlarında paket çalıştırırken [SSIS paketi yürütme etkinliğinin](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) **bağlantı yöneticileri** sekmesinde görünen Ilgili bağlantı yöneticileri için **true** olarak ayarlanarak da etkinleştirilebilir.
  
  ![ConnectByProxy property3 etkinleştir](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- SSIS IR 'niz üzerinde çalışmak üzere bu paketleri içeren projeyi yeniden dağıtma: özellik, özellik yolu ve `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`sağlayarak etkinleştirilebilir ve paket açılır penceresini yürütme penceresinin **Gelişmiş** sekmesinde özellik geçersiz kılma olarak **true** olarak ayarlanır. paketleri SSMS 'den çalıştırırken.

  ![ConnectByProxy property4 etkinleştir](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Özelliği, Ayrıca, ADF işlem hatlarında paket çalıştırırken [SSIS paketi yürütme etkinliğinin](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) **geçersiz kılınmasına** izin vererek özellik yolu, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`ve **true** olarak ayarlanarak etkinleştirilebilir.
  
  ![ConnectByProxy Property5 etkinleştir](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>İlk ve ikinci hazırlama görevlerinde hata ayıkla
Şirket içinde barındırılan IR 'de, çalışma zamanı günlüklerini `C:\ProgramData\SSISTelemetry` klasöre ve `C:\ProgramData\SSISTelemetry\ExecutionLog` klasöründeki ilk hazırlama görevlerinin yürütme günlüklerine bulabilirsiniz.  Paketleri SSıSDB veya dosya sistemi/dosya paylaşımları/Azure dosyalarında sakladığınıza bağlı olarak, ikinci hazırlama görevlerinin yürütme günlükleri SSıSDB veya belirtilen günlüğe kaydetme yollarında bulunabilir.  İlk hazırlama görevlerinin benzersiz kimlikleri, ikinci hazırlama görevlerinin yürütme günlüklerinde de bulunabilir, örn. 

![İlk hazırlama görevinin benzersiz KIMLIĞI](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="billing-for-the-first-and-second-staging-tasks"></a>İlk ve ikinci hazırlama görevleri için faturalandırma
Şirket içinde barındırılan IR üzerinde çalışan ilk hazırlama görevleri, [ADF veri ardışık düzen fiyatlandırma](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) makalesinde belirtilen şekilde faturalandırılır ve şirket IÇINDE barındırılan IR üzerinde çalışan tüm veri taşıma etkinlikleri aynı şekilde faturalandırılır.

Azure-SSIS IR çalışan ikinci hazırlama görevleri ayrı olarak faturalandırılmaz, ancak çalıştırdığınız Azure-SSIS IR [Azure-SSIS IR fiyatlandırma](https://azure.microsoft.com/pricing/details/data-factory/ssis/) makalesinde belirtilen şekilde faturalandırılır.

## <a name="current-limitations"></a>Geçerli sınırlamalar

- Yalnızca ODBC/OLEDB/düz dosya bağlantı yöneticileri ve ODBC/OLEDB/düz dosya kaynakları olan veri akışı görevleri şu anda desteklenmektedir. 
- Yalnızca **hesap anahtarı**/**SAS URI**/**hizmet sorumlusu** kimlik doğrulaması ile yapılandırılmış Azure Blob depolama bağlı hizmetleri şu anda destekleniyor.
- Yalnızca Azure-SSIS IR sağlandığı aynı ADF altında sağlanan şirket içinde barındırılan IR, şu anda desteklenmektedir.
- ODBC/OLEDB/düz dosya kaynakları ve bağlantı yöneticilerinin özelliklerinde SSIS parametreleri/değişkenleri kullanılması şu anda desteklenmiyor.

## <a name="next-steps"></a>Sonraki adımlar
Şirket içinde barındırılan IR 'yi, Azure-SSIS IR için bir ara sunucu olarak yapılandırdıktan sonra, ADF işlem hatlarında SSIS paket etkinliklerini yürütme olarak SSIS paketlerini yürütme gibi paketleri dağıtabilir ve çalıştırabilirsiniz. ADF işlem hatları 'nda SSIS paketi [yürütme ](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
