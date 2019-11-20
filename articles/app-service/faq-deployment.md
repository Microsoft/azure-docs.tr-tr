---
title: Dağıtım SSS-Azure App Service | Microsoft Docs
description: Azure App Service Web Apps özelliğinin dağıtımı hakkında sık sorulan soruların yanıtlarını alın.
services: app-service\web
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 0a01f25894d1226466473dec7333859c7415f1fd
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121195"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Azure 'da Web Apps için dağıtım SSS

Bu makalede, [Azure App Service Web Apps özelliği](https://azure.microsoft.com/services/app-service/web/)için dağıtım sorunları hakkında sık sorulan soruların (SSS) yanıtları bulunur.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>App Service Web Apps kullanmaya yeni başladım. Kodumu Yayımla Nasıl yaparım??

Web uygulaması kodunuzu yayımlamaya yönelik bazı seçenekler şunlardır:

*   Visual Studio 'Yu kullanarak dağıtın. Visual Studio çözümünüz varsa, Web uygulaması projesine sağ tıklayın ve ardından **Yayımla**' yı seçin.
*   Bir FTP istemcisi kullanarak dağıtın. Azure portal, kodunuzu dağıtmak istediğiniz Web uygulaması için yayımlama profilini indirin. Ardından, aynı yayımlama profili FTP kimlik bilgilerini kullanarak dosyaları \site\wwwroot dosyasına yükleyin.

Daha fazla bilgi için bkz. [App Service uygulamanızı dağıtma](deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Visual Studio 'dan dağıtmaya çalıştığımda bir hata iletisi görüyorum. Bu hatayı çözmek Nasıl yaparım? mı?

Aşağıdaki iletiyi görürseniz, SDK 'nın eski bir sürümünü kullanıyor olabilirsiniz: "' YourResourceGroup ' kaynak grubundaki ' YourResourceName ' kaynağının dağıtımı sırasında hata: MissingRegistrationForLocation: ' Orta ABD ' konumundaki ' Components ' kaynak türü için abonelik kayıtlı değil. Bu konuma erişebilmek için bu sağlayıcıya yeniden kaydolun. " 

Bu hatayı çözmek için [en son SDK](https://azure.microsoft.com/downloads/)'ya yükseltin. Bu iletiyi görürseniz ve en son SDK 'ya sahipseniz, bir destek isteği gönderebilirsiniz.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Visual Studio 'dan App Service bir ASP.NET uygulamasını Nasıl yaparım? dağıtmak istiyor musunuz?
<a id="deployasp"></a>

[Azure 'da ilk ASP.NET Web uygulamanızı oluşturma öğreticisinde beş dakika içinde](app-service-web-get-started-dotnet.md) , Visual Studio kullanarak App Service bir Web uygulamasına bir Web uygulamasına nasıl dağıtılacağı gösterilmektedir.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Farklı dağıtım kimlik bilgileri türleri nelerdir?

App Service, yerel git dağıtımı ve FTP/S dağıtımı için iki tür kimlik bilgilerini destekler. Dağıtım kimlik bilgilerini yapılandırma hakkında daha fazla bilgi için bkz. [App Service dağıtım kimlik bilgilerini yapılandırma](deploy-configure-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>App Service Web uygulamamın dosya veya dizin yapısı nedir?

App Service uygulamanızın dosya yapısı hakkında daha fazla bilgi için bkz. [Azure 'Da dosya yapısı](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Nasıl yaparım? çözümleme "FTP hatası 550-Şu diskte yeterli alan yok", dosyalarımı FTP 'mi denediğimde

Bu iletiyi görürseniz, büyük olasılıkla web uygulamanız için hizmet planında bir disk kotasına çalıştırıyorsunuz olabilirsiniz. Disk alanı gereksinimlerinize göre daha yüksek bir hizmet katmanına kadar ölçeklendirmeniz gerekebilir. Fiyatlandırma planları ve kaynak limitleri hakkında daha fazla bilgi için bkz. [App Service fiyatlandırması](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Nasıl yaparım? App Service Web uygulamamın sürekli dağıtımını ayarladı musunuz?

Azure DevOps, OneDrive, GitHub, Bitbucket, Dropbox ve diğer Git depoları dahil olmak üzere çeşitli kaynaklardan sürekli dağıtım ayarlayabilirsiniz. Bu seçenekler portalda kullanılabilir. [App Service sürekli dağıtımı](deploy-continuous-deployment.md) , sürekli dağıtımı ayarlamayı anlatan yararlı bir öğreticidir.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>GitHub ve BitBucket 'dan sürekli dağıtımla ilgili sorunları gidermek Nasıl yaparım? mı?

GitHub veya Bitbucket 'un sürekli dağıtımıyla ilgili sorunları araştırmanıza yardımcı olması için bkz. [sürekli dağıtımı araştırma](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Siteme FTP yapamıyorum ve kodumu yayımlayamıyorum. Bu sorunu çözmek Nasıl yaparım? mı?

FTP sorunlarını gidermek için:

1. Doğru ana bilgisayar adını ve kimlik bilgilerini girdiğinizi doğrulayın. Farklı kimlik bilgileri türleri ve bunların nasıl kullanılacağı hakkında ayrıntılı bilgi için bkz. [dağıtım kimlik bilgileri](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. FTP bağlantı noktalarının bir güvenlik duvarı tarafından engellenmediğinden emin olun. Bağlantı noktaları şu ayarlara sahip olmalıdır:
    * FTP denetim bağlantı noktası: 21
    * FTP veri bağlantısı bağlantı noktası: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Kodumu App Service Nasıl yaparım? yayımlayın mi?

Azure hızlı başlangıç, uygulamanızı dağıtım yığınını ve tercih ettiğiniz yöntemi kullanarak dağıtmanıza yardımcı olmak için tasarlanmıştır. Hızlı başlangıç 'yi kullanmak için, Azure portal uygulama hizmetinize gidin, **dağıtım**altında **hızlı başlangıç**' ı seçin.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Uygulamam neden App Service dağıtım sonrasında bazen yeniden başlatılır?

Uygulama dağıtımının yeniden başlatma ile sonuçlanabileceğini gösteren koşullar hakkında bilgi edinmek için bkz. dağıtım ve [çalışma zamanı sorunları](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Makalede açıklandığı gibi, dosyaları Wwwroot klasörüne dağıtır App Service. Uygulamanızı hiçbir şekilde doğrudan yeniden başlatmaz.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Nasıl yaparım? Azure DevOps kodunu App Service tümleştirsin mi?

Azure DevOps ile sürekli dağıtımı kullanmak için iki seçeneğiniz vardır:

*   Git projesi kullanın. Dağıtım merkezini kullanarak App Service aracılığıyla bağlanın.
*   Team Foundation Sürüm Denetimi (TFVC) projesi kullanın. App Service için yapı aracısını kullanarak dağıtın.

Her iki seçenek için de sürekli kod dağıtımı var olan geliştirici iş akışlarına ve iade yordamlarına bağlıdır. Daha fazla bilgi için şu makalelere bakın: 

*   [Azure Web sitesine uygulamanızın sürekli dağıtımını uygulama](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Bir Web uygulamasına dağıtmak için bir Azure DevOps organizasyonu ayarlama](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Uygulamamı App Service dağıtmak için FTP veya FTPS kullanın Nasıl yaparım??

Web uygulamanızı App Service dağıtmak üzere FTP veya FTPS kullanma hakkında daha fazla bilgi için bkz. [FTP/S kullanarak uygulamanızı App Service dağıtma](deploy-ftp.md).
