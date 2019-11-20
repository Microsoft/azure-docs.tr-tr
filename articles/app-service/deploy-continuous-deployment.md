---
title: Sürekli dağıtım-Azure App Service | Microsoft Docs
description: Azure Uygulama Hizmeti’ne sürekli dağıtımı etkinleştirme hakkında bilgi edinin.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/23/2019
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 98f82914af8216789a04d3cfd2972f83c16b3fa0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070658"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Azure App Service için sürekli dağıtım

[Azure App Service](overview.md) , en son güncelleştirmeleri çekerek GitHub, bitbucket ve [Azure Repos](https://azure.microsoft.com/services/devops/repos/) depolarından sürekli dağıtım yapılmasını mümkün. Bu makalede, kudu derleme hizmeti veya [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)aracılığıyla uygulamanızı sürekli olarak dağıtmak için Azure Portal nasıl kullanılacağı gösterilmektedir. 

Kaynak denetim hizmetleri hakkında daha fazla bilgi için bkz. [depo oluşturma (GitHub)], [depo oluşturma (Bitbucket)]veya [Yeni git deposu oluşturma (Azure Repos)].

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Azure App Service yetkilendir 

Azure Repos kullanmak için Azure DevOps kuruluşunuzun Azure aboneliğinize bağlı olduğundan emin olun. Daha fazla bilgi için bkz. bir [Azure DevOps Services hesabını bir Web uygulamasına dağıtabilmesi Için ayarlama](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).

Bitbucket veya GitHub için Azure App Service, deponuza bağlanmak üzere yetkilendirin. Yalnızca bir kaynak denetim hizmetiyle bir kez yetkilendirme yapmanız gerekir. 

1. [Azure Portal](https://portal.azure.com) sol gezinti bölmesinde **uygulama hizmetleri** ' ni seçin ve ardından dağıtmak istediğiniz Web uygulamasını seçin. 
   
1. Uygulama sayfasında, sol taraftaki menüden **Dağıtım Merkezi** ' ni seçin.
   
1. **Dağıtım Merkezi** sayfasında **GitHub** veya **Bitbucket**' ı seçin ve ardından **Yetkilendir**' i seçin. 
   
   ![Kaynak denetimi hizmeti ' ni seçin, sonra Yetkilendir ' i seçin.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Gerekirse hizmette oturum açın ve yetkilendirme istemlerini izleyin. 

## <a name="enable-continuous-deployment"></a>Sürekli dağıtımı etkinleştir 

Bir kaynak denetim hizmetine yetki verdikten sonra, uygulamanızı yerleşik [kudu App Service derleme sunucusu](#option-1-use-the-app-service-build-service)aracılığıyla veya [Azure Pipelines](#option-2-use-azure-pipelines)aracılığıyla sürekli dağıtım için yapılandırın. 

### <a name="option-1-use-the-app-service-build-service"></a>Seçenek 1: App Service derleme hizmetini kullanın

Yerleşik kudu App Service derleme sunucusunu, GitHub, Bitbucket veya Azure Repos sürekli olarak dağıtmak için kullanabilirsiniz. 

1. [Azure Portal](https://portal.azure.com) sol gezinti bölmesinde **uygulama hizmetleri** ' ni seçin ve ardından dağıtmak istediğiniz Web uygulamasını seçin. 
   
1. Uygulama sayfasında, sol taraftaki menüden **Dağıtım Merkezi** ' ni seçin.
   
1. **Dağıtım Merkezi** sayfasında yetkili Kaynak denetimi sağlayıcınızı seçin ve **devam**' ı seçin. GitHub veya Bitbucket için, yetkili hesabı değiştirmek üzere **hesabı Değiştir** ' i de seçebilirsiniz. 
   
   > [!NOTE]
   > Azure Repos kullanmak için Azure DevOps Services kuruluşunuzun Azure aboneliğinize bağlı olduğundan emin olun. Daha fazla bilgi için bkz. bir [Azure DevOps Services hesabını bir Web uygulamasına dağıtabilmesi Için ayarlama](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
   
1. GitHub veya Azure Repos için, **Yapı sağlayıcısı** sayfasında **App Service derleme hizmeti**' ni seçin ve ardından **devam**' ı seçin. Bitbucket her zaman App Service derleme hizmetini kullanır.
   
   ![App Service Build Service ' i seçin ve ardından devam ' ı seçin.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. **Yapılandır** sayfasında:
   
   - GitHub için, aşağı açılan ve sürekli dağıtmak istediğiniz **kuruluş**, **Depo**ve **dalı** seçin.
     
     > [!NOTE]
     > Herhangi bir depo görmüyorsanız, GitHub 'da Azure App Service yetkilendirmeniz gerekebilir. GitHub deponuza gidin ve **Ayarlar** > **uygulamalar** > **yetkili OAuth uygulamalar**' a gidin. **Azure App Service**' yi seçin ve ardından **ver**' i seçin.
     
   - Bitbucket için, sürekli olarak dağıtmak istediğiniz Bitbucket **ekibini**, **deposunu**ve **dalını** seçin.
     
   - Azure Repos için, sürekli olarak dağıtmak istediğiniz **Azure DevOps organizasyonu**, **Proje**, **Depo**ve **dalı** seçin.
     
     > [!NOTE]
     > Azure DevOps kuruluşunuz listede yoksa Azure aboneliğinize bağlı olduğundan emin olun. Daha fazla bilgi için bkz. bir [Azure DevOps Services hesabını bir Web uygulamasına dağıtmak Için ayarlama](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
     
1. Seçin **devam**.
   
   ![Depo bilgilerini doldurup devam ' ı seçin.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Yapı sağlayıcısını yapılandırdıktan sonra, **Özet** sayfasındaki ayarları gözden geçirin ve ardından **son**' u seçin.
   
   Seçili depodaki ve daldaki yeni işlemeler artık App Service uygulamanıza sürekli olarak dağıtılır. Yürütmeleri ve dağıtımları **Dağıtım Merkezi** sayfasında izleyebilirsiniz.
   
   ![Dağıtım Merkezi 'nde işlemeleri ve dağıtımları izleme](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Seçenek 2: Azure Pipelines'ı kullanın 

Hesabınız gerekli izinlere sahipse, GitHub veya Azure Repos depolarından sürekli olarak dağıtmak için Azure Pipelines ayarlayabilirsiniz. Azure Pipelines aracılığıyla dağıtma hakkında daha fazla bilgi için bkz. [Azure App Services 'a Web uygulaması dağıtma](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

Azure App Service Azure DevOps kuruluşunuzda sürekli teslim Azure Pipelines oluşturmak için: 

- Azure hesabınızın Azure Active Directory yazma ve hizmet oluşturma izinlerine sahip olması gerekir. 
  
- Azure hesabınızın, Azure aboneliğinizde **sahip** rolü olmalıdır.

- Kullanmak istediğiniz Azure DevOps projesinde bir yönetici olmanız gerekir.

Azure Pipelines yapılandırmak için (Önizleme):

1. [Azure Portal](https://portal.azure.com) sol gezinti bölmesinde **uygulama hizmetleri** ' ni seçin ve ardından dağıtmak istediğiniz Web uygulamasını seçin. 
   
1. Uygulama sayfasında, sol taraftaki menüden **Dağıtım Merkezi** ' ni seçin.
   
1. **Yapı sağlayıcısı** sayfasında **Azure Pipelines (Önizleme)** öğesini seçin ve ardından **devam**' ı seçin. 
   
1. **Yapılandırma** sayfasında, **kod** bölümünde:
   
   - GitHub için, aşağı açılan ve sürekli dağıtmak istediğiniz **kuruluş**, **Depo**ve **dalı** seçin.
     
     > [!NOTE]
     > Herhangi bir depo görmüyorsanız, GitHub 'da Azure App Service yetkilendirmeniz gerekebilir. GitHub deponuza gidin ve **Ayarlar** > **uygulamalar** > **yetkili OAuth uygulamalar**' a gidin. **Azure App Service**' yi seçin ve ardından **ver**' i seçin.
     
   - Azure Repos için, sürekli olarak dağıtmak istediğiniz **Azure DevOps organizasyonu**, **Proje**, **Depo**ve **dalı** seçin ya da yeni bir Azure DevOps organizasyonu yapılandırın.
     
     > [!NOTE]
     > Mevcut Azure DevOps kuruluşunuz listede yoksa Azure aboneliğinize bağlamanız gerekebilir. Daha fazla bilgi için bkz. [CD yayın işlem hattınızı tanımlama](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
     
1. Seçin **devam**.
   
1. Azure Repos için, **Yapı** bölümünde, Azure Pipelines derleme görevlerini çalıştırmak için kullanması gereken dil çerçevesini belirtin ve ardından **devam**' ı seçin.
   
1. **Test** sayfasında, yük testlerini etkinleştirip etkinleştirmeyeceğinizi seçin ve ardından **devam**' ı seçin.
   
1. App Service planı [fiyatlandırma katmanınıza](https://azure.microsoft.com/pricing/details/app-service/plans/)bağlı olarak, **hazırlama sayfasına dağıt** sayfasına bakabilirsiniz. [Dağıtım yuvalarının](deploy-staging-slots.md)etkinleştirilip etkinleştirilmeyeceğini seçin ve ardından **devam**' ı seçin.
   
   > [!NOTE]
   > Azure Pipelines üretim yuvasına sürekli teslime izin vermez. Bu kısıtlama üretime yönelik yanlışlıkla dağıtımları önler. Hazırlama yuvasına sürekli teslim ayarlayın, değişiklikleri doğrulayın ve ardından, varsa yuvaları değiştirin.
   
1. Yapı sağlayıcısını yapılandırdıktan sonra, **Özet** sayfasındaki ayarları gözden geçirin ve ardından **son**' u seçin.
   
   Seçili depodaki ve daldaki yeni işlemeler artık App Service uygulamanıza sürekli olarak dağıtılır. Yürütmeleri ve dağıtımları **Dağıtım Merkezi** sayfasında izleyebilirsiniz.
   
   ![Dağıtım Merkezi 'nde işlemeleri ve dağıtımları izleme](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Sürekli dağıtımı devre dışı bırak

Sürekli dağıtımı devre dışı bırakmak için uygulamanızın **Dağıtım Merkezi** sayfasının en üstündeki **bağlantıyı kes** ' i seçin.

![Sürekli dağıtımı devre dışı bırak](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Desteklenmeyen depoları kullan

Windows uygulamaları için, [GitLab](https://gitlab.com/)gibi portalın doğrudan desteklemediği bir bulut git veya Mecurial deposundan sürekli dağıtımı el ile yapılandırabilirsiniz. Bunu, **Dağıtım Merkezi** sayfasındaki dış kutusunu seçerek yapabilirsiniz. Daha fazla bilgi için bkz. [el ile adımları kullanarak sürekli dağıtımı ayarlama](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Ek kaynaklar

* [Sürekli dağıtımla ilgili yaygın sorunları araştırın](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell’i kullanma](/powershell/azureps-cmdlets-docs)
* [Git belgeleri](https://git-scm.com/documentation)
* [Kudu Projesi](https://github.com/projectkudu/kudu/wiki)

[Depo oluşturma (GitHub)]: https://help.github.com/articles/create-a-repo
[Depo oluşturma (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Yeni git deposu oluşturma (Azure Repos)]: /azure/devops/repos/git/creatingrepo
