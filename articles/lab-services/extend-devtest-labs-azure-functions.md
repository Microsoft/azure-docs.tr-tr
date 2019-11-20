---
title: Azure Işlevleri 'ni kullanarak Azure DevTest Labs genişletme | Microsoft Docs
description: Azure Işlevleri 'ni kullanarak Azure DevTest Labs genişletmeyi öğrenin.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014366"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>DevTest Labs 'i genişletmek için Azure Işlevleri 'ni kullanma
DevTest Labs tarafından zaten desteklenenden daha fazla senaryoyu desteklemek için Azure Işlevleri 'ni kullanabilirsiniz. Azure Işlevleri, hizmetin yerleşik işlevselliğini işletmeye özgü gereksinimlerinizi karşılayacak şekilde genişletmek için kullanılabilir. Aşağıdaki listede olası senaryolardan bazıları verilmiştir. Bu makalede, bu örnek senaryolarından birinin nasıl uygulanacağı gösterilmektedir.

- Laboratuvarda sanal makinelerin (VM 'Ler) üst düzey özetini sağlama
- [Bir laboratuvarı Uzak Masaüstü Ağ geçidini kullanacak şekilde yapılandırma](configure-lab-remote-desktop-gateway.md)
- İç destek sayfasında uyumluluk raporlaması
- Kullanıcıların abonelikte daha fazla izin gerektiren işlemleri tamamlamasını sağlama
- [DevTest Labs olaylarını temel alan iş akışları başlatılıyor](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Genel Bakış
Azure [işlevleri](../azure-functions/functions-overview.md) , Azure 'da sunucusuz bir bilgi işlem platformudur. DevTest Labs ile bir çözümde Azure Işlevlerinin kullanılması, kendi özel kodumuza sahip mevcut özellikleri iyileştirmemize olanak sağlar. Azure Işlevleri hakkında daha fazla bilgi için bkz. [Azure işlevleri belgeleri](../azure-functions/functions-overview.md). Azure Işlevlerinin, DevTest Labs 'de gereksinimlerinizi veya tam senaryolarınızı karşılamasına nasıl yardımcı olduğunu göstermek için bu makalede, laboratuvarda bulunan VM 'lerin en üst düzey özetini aşağıda gösterildiği gibi bir örnek kullanılmaktadır:

**Örnek gereksinim/senaryo**: Kullanıcılar işletim sistemi, sahip ve uygulanan yapıtlar dahil olmak üzere laboratuvardaki tüm VM 'Ler hakkındaki ayrıntıları görebilir.  Ayrıca, **Windows güncelleştirmelerini Uygula** yapıtı son zamanlarda uygulanmadıysa, bunu uygulamak için kolay bir yol vardır.

Senaryoyu tamamlamaya yönelik olarak, aşağıdaki diyagramda açıklandığı gibi iki işlev kullanacaksınız:  

![Genel akış](./media/extend-devtest-labs-azure-functions/flow.png)

Bu örnek işlevlerin kaynak kodu, [DevTest Labs GitHub deposunda](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) ( C# ve PowerShell uygulamaları kullanılabilir) bulunur.

- **Updateınternalsupportpage**: Bu işlev DevTest Labs 'i sorgular ve dahili destek sayfasını doğrudan sanal makinelerle ilgili ayrıntılarla güncelleştirir.
- **Applywindowsupdateartifact**: Laboratuvardaki bir VM için, bu işlev **Windows Update** yapıtını uygular.

## <a name="how-it-works"></a>Nasıl çalışır?
Kullanıcılar, DevTest Labs 'de **iç destek** sayfasını seçtiklerinde, VM 'ler, laboratuvar sahipleri ve destek kişileri hakkında bilgi içeren bir önceden doldurulmuş sayfası vardır.  

**Yenilemek için burayı Seç** düğmesini seçtiğinizde, sayfa ilk Azure işlevini çağırır: **Updateınternalsupportpage**. İşlevi DevTest Labs 'i bilgi için sorgular ve ardından **iç destek** sayfasını yeni bilgilerle yeniden yazar.

Windows Update yapıtlarının son zamanlarda uygulanmadığı tüm VM 'Ler için gerçekleştirilebilecek ek bir eylem, VM 'ye Windows güncelleştirmelerini uygulamak için bir düğme olacaktır. Bir VM için ***Windows Update 'ı Çalıştır** düğmesini seçtiğinizde, sayfa Ikinci Azure işlevini çağırır: **Applywindowsupdateartifact**. Bu işlev, sanal makinenin çalışıp çalışmadığını denetler ve bu durumda [Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) yapıtı doğrudan uygular.

## <a name="step-by-step-walkthrough"></a>Adım adım yönergeler
Bu bölümde, **iç destek** sayfasını güncelleştirmek Için gereken Azure kaynaklarını ayarlamaya yönelik adım adım yönergeler sağlanmaktadır. Bu izlenecek yol, DevTest Labs 'i genişletmeyi bir örnek sağlar. Bu kalıbı diğer senaryolar için kullanabilirsiniz.

### <a name="step-1-create-a-service-principal"></a>1\. adım: Hizmet sorumlusu oluşturma 
İlk adım, Laboratuvarı içeren aboneliğe izin veren bir hizmet sorumlusu almaya yönelik bir hizmettir. Hizmet sorumlusu, parola tabanlı kimlik doğrulaması kullanmalıdır. [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)veya [Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)gerçekleştirilebilir. Kullanmak üzere zaten bir hizmet sorumlunuz varsa, bu adımı atlayabilirsiniz.

Hizmet sorumlusu için **uygulama kimliği**, **anahtar**ve **Kiracı kimliği** ' ni aklınızda edin. Bu izlenecek yolda daha sonra ihtiyacınız olacak. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>2\. adım: Visual Studio 2019 'de örneği indirme ve açma
Azure işlevleri örneğinin bir kopyasını yerel olarak indirin (Depoyu kopyalayarak ya da depoyu [buradan](https://github.com/Azure/azure-devtestlab/archive/master.zip)indirerek). [ C# ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp)  

1. Visual Studio 2019 ile örnek çözümü açın.  
1. Zaten yüklemediyseniz, Visual Studio için **Azure geliştirme** iş yükünü yükleyebilirsiniz. **Araçlar** -> **araçları ve özellikleri al** menü öğesi aracılığıyla yüklenebilir.

    ![Azure geliştirme iş yükü](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Çözümü oluşturun. **Oluştur** ' u ve ardından **çözüm oluştur** menü öğesini seçin.

### <a name="step-3-deploy-the-sample-to-azure"></a>3\. adım: Örneği Azure 'a dağıtma
Visual Studio 'da, **Çözüm Gezgini** penceresinde **Azurefunctions** projesine sağ tıklayın ve ardından **Yayımla**' yı seçin. Yeni veya mevcut bir Azure İşlev Uygulaması yayımlamayı gerçekleştirmek için Sihirbazı izleyin. Visual Studio kullanarak Azure işlevleri geliştirme ve dağıtma hakkında ayrıntılı bilgi için bkz. [Visual Studio kullanarak Azure Işlevleri geliştirme](../azure-functions/functions-develop-vs.md).

![Yayımla iletişim kutusu](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>4\. Adım:  Uygulama ayarlarını topla
İşlevler yayımlandıktan sonra, Azure portal bu işlevlerin URL 'Lerini almanız gerekir. 

1. [Azure portalına](https://portal.azure.com) gidin. 
1. İşlev uygulamasını bulun.
1. **Işlev uygulamaları** sayfasında, işlevi seçin. 
1. Aşağıdaki görüntüde gösterildiği gibi **işlev URL 'Sini al** ' ı seçin. 

    ![Azure işlevleri URL 'Leri](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Kopyalayın ve URL'yi kaydedin. Diğer Azure işlevi için bu adımları tekrarlayın. 

Ayrıca, uygulama KIMLIĞI, anahtar ve kiracı KIMLIĞI gibi hizmet sorumlusu hakkında ek bilgilere de ihtiyacınız olacaktır.


### <a name="step-5--update-application-settings"></a>5\. Adım:  Uygulama ayarlarını Güncelleştir
Visual Studio 'da, Azure Işlevini yayımladıktan sonra **Eylemler**altındaki **Azure App Service ayarlarını Düzenle** ' yi seçin. Aşağıdaki uygulama ayarlarını (uzak) güncelleştirin:

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (varsayılan değer 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Uygulama ayarları](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>6\. Adım: Azure işlevini test etme
Bu izlenecek yolun son adımı, Azure işlevini test etmek için kullanılır.  

1. Adım 3 ' te oluşturulan işlev uygulamasındaki **Updateınternalsupportpage** işlevine gidin. 
1. Sayfanın sağ tarafındaki **Test** ' i seçin. 
1. Yol özelliklerini (LABNAME, RESOURCEGROUPNAME ve SUBSCRIPTIONıD) girin.
1. İşlevi yürütmek için **Çalıştır** ' ı seçin.  

    Bu işlev, belirtilen laboratuvarın iç destek sayfasını güncelleştirecek. Ayrıca, kullanıcıların bir dahaki sefer işlevi doğrudan çağırması için bir düğme içerir

    ![Test işlevi](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Sonraki adımlar
Azure Işlevleri, yerleşik olarak bulunan ve müşterilerin takımlarına ait benzersiz gereksinimleri karşıladığından yardımcı olan DevTest Labs işlevselliğini genişletmeye yardımcı olabilir. Bu model daha da daha fazla genişleyebilmeleri için Genişletilebilir & genişletilebilir.  DevTest Labs hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın: 

- [DevTest Labs kurumsal başvuru mimarisi](devtest-lab-reference-architecture.md)
- [Sık Sorulan Sorular](devtest-lab-faq.md)
- [DevTest Labs 'i ölçeklendirme](devtest-lab-guidance-scale.md)
- [PowerShell ile DevTest Labs 'i otomatikleştirme](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








