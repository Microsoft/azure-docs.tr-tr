---
title: Visual Studio App Center ve Azure hizmetleriyle mobil uygulamalarınızın dağıtımını ve sürümünü otomatikleştirin
description: Mobil uygulamalarınız için sürekli teslim işlem hattı ayarlamaya yardımcı olan App Center gibi hizmetler hakkında bilgi edinin.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 3b8305d02345abe98b674a8ed61626eaf6b5528d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795607"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Sürekli teslim hizmetleriyle mobil uygulamalarınızın dağıtımını ve sürümünü otomatikleştirin

Geliştiriciler olarak kod yazar ve kod deposuna iade edersiniz, ancak depoya yapılan işlemeler her zaman tutarlı olmayabilir. Aynı projede çalışan birden fazla geliştirici sayesinde, sorunlar tümleştirmeyle birlikte gelir ve takım işlerin çalışmaması durumunda, hatalar devam etmez ve proje geliştirme gecikir. Geliştiricilerin, hataları denetlemek için tüm yazılım kodunun oluşturulup Test edilene kadar beklemesi ve bu işlemin yavaş ve daha az yinelemeli olmasını sağlar.

**Sürekli teslim**sayesinde, uygulamayı bir test ediciler veya şirket çalışanları (Beta testi için) veya App Store (üretim için) grubuna dağıtmaktan bağımsız olarak, mobil uygulamalarınızın dağıtımını ve sürümünü otomatikleştirin. Dağıtımları daha az riskli hale getirir, hızlı yinelemeleri azaltır ve müşterilere yeni değişiklikleri sürekli şekilde yayınlamanızı sağlar.

## <a name="distribute-application-binaries-to-beta-testers"></a>Uygulama ikili dosyalarını Beta sınayıcılarına dağıtma
Mobil uygulamanız, uygulama geliştirme sürecinde kritik adımlardan biridir. Uygulamanızda daha erken hata ve sorunları bulmaya ve uygulama kalitelerinizi geliştiren geri bildirimin üretim kullanımı için hazırlanmaya yardımcı olur.

Mobil uygulamalarınızda sürekli teslim işlem hattını etkinleştirmek için aşağıdaki hizmetleri kullanın.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center dağıtımı](/appcenter/distribution/) , geliştiricilerin derlemeleri Son Kullanıcı cihazlarına hızlıca yayınlamasına yönelik bir araçtır. Tek bir portal yüklemesi deneyimi sayesinde, dağıtım yalnızca beta uygulama Sınayıcısı dağıtımı için güçlü bir çözüm değildir, ayrıca genel uygulama depoları aracılığıyla dağıtıma uygun bir alternatiftir. Geliştiriciler, App Center derleme ve genel uygulama mağazası tümleştirmeleri sayesinde dağıtım iş akışını otomatik hale getirebilir.

**Temel Özellikler**
- Uygulamanızı **Beta sınayıcılarına ve kullanıcılara dağıtın** ve tüm sınayıcılarınızın uygulamanızın en son sürümünde bulunduğundan emin olun.
- **Yeni yayınların sınayıcılarını** karşıdan yükleme akışından tekrar Ilerlemeden bildir.
- Uygulamanızın farklı sürümleri için **dağıtım gruplarını yönetin** .
- **Mağazalara dağıtım** 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- **Platform desteği** -IOS, Android, MacOS, tvOS, Xamarin, tepki veren yerel, Unity, Cordova.
- İOS cihazlarını sağlama profilinize otomatik olarak kaydedin.

**Başvur**
- [App Center kaydolun](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center dağıtma ile çalışmaya başlama](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) , tercih ettiğiniz git sağlayıcınızla birlikte çalışarak ve Azure hizmetleri dahil olmak üzere çok sayıda ana bulut hizmetine dağıtabileceğiniz, tam özellikli bir sürekli TÜMLEŞTIRME (CI) ve sürekli teslım (CD) hizmetidir. GitHub, GitHub Enterprise Server, GitLab, Bitbucket bulutu veya Azure Repos için kodunuzla başlayabilirsiniz. Daha sonra Microsoft Azure, Google Cloud Platform veya Amazon Web Services için kodunuzun derleme, test etme ve dağıtımını otomatik hale getirebilirsiniz.

**Temel Özellikler**
- **Yerel (Android, iOS ve Windows) ve platformlar arası (Xamarin, Cordova ve yanıt verme) mobil uygulamaları**IÇIN bir CI sunucusu ayarlamaya yönelik Basitleştirilmiş görev tabanlı deneyim.
- **Herhangi bir dil, platform ve bulut** oluşturma, test etme ve Node. js, Python, Java, php, Ruby, Go, C/C++, C#, Android ve iOS uygulamaları dağıtma. Linux, macOS ve Windows'da paralel çalıştırın. Azure, AWS ve GCP gibi bulut sağlayıcılarına dağıtın. Mobil uygulamaları Beta kanalları ve uygulama depoları aracılığıyla dağıtın.
- **Yerel kapsayıcı desteği** -kolay bir şekilde yeni kapsayıcılar oluşturun ve bunları herhangi bir kayıt defterine gönderin. Kapsayıcıları bağımsız konaklara veya Kubernetes 'e dağıtın.
- **Gelişmiş iş akışları ve Özellikler** -kolay yapı zinciri ve çoklu aşamalı derlemeler. YAML, test tümleştirmesi, yayın kapıları, raporlama ve daha fazlası için destek.
- **Genişletilebilir** -topluluk tarafından oluşturulan bir dizi derleme, test ve Dağıtım görevi (bolluk 'Ten SonarCloud 'a yüzlerce uzantı) kullanın. Jenkins gibi diğer CI sistemlerinden de dağıtım yapabilirsiniz. Web kancaları ve REST API 'Leri tümleştirmenize yardımcı olur
- Ortak ve özel depolar için **bulutta barındırılan ücretsiz derlemeler** .
- AWS, GCP vb. gibi **diğer bulut satıcılarına dağıtımı destekler** .

**Başvur**
- [Azure Pipelines Kılavuzu ile çalışmaya başlama](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps ile çalışmaya başlama](https://app.vsaex.visualstudio.com/signup/) 
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Uygulamanızı doğrudan uygulama mağazalarına dağıtın
Uygulamanız üretim kullanımına hazırlanıyor ve bu uygulamanın herkese açık bir şekilde kullanılmasını istiyorsanız, BT 'nin müşteriler tarafından indirilebileceği uygulama depolarına gönderilmesi gerekir. Uygulamanızı doğrudan uygulama depolarına dağıtmanın birden çok yolu vardır. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center dağıtımı](/appcenter/distribution/stores/) hizmeti, mobil uygulamalarınızı doğrudan uygulama mağazalarına yayımlamanıza olanak sağlar. Uygulamanız son kullanıcılar tarafından indirilmeye hazırsa, uygulama ikili dosyalarınızı doğrudan App Center portalından yayımlayabilirsiniz.  

Doğrudan dağıtım yapabilirsiniz:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
Apple tarafından geliştirilen ve korunan Uygulama Mağazası, kullanıcıların iOS, MacOS, WatchOS ve tvOS cihazlarında geliştirilen uygulamalara gözatıp indirmesini sağlar. Geliştiricilerin iOS uygulamalarını genel kullanım için Apple mağazası 'na göndermesi gerekir.

### <a name="google-play"></a>Google Play

Google Play, Android işletim sistemi için resmi uygulama deposudur ve kullanıcıların, Android cihazlar için geliştirilen ve Google aracılığıyla yayınlanan uygulamalara göz atmasına ve bunları indirmesini sağlar.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) kurumsal taşınabilirlik YÖNETIMI (EMM) alanındaki bulut tabanlı bir hizmettir ve Şirket verilerinizi korumalı tutarken iş gücünüzün üretken olmasına yardımcı olur. Intune ile şunları yapabilirsiniz 
- Şirket verilerine erişmek için iş gücünüzün kullandığı mobil cihazları ve bilgisayarları yönetin.
- İş gücünüzün kullandığı mobil uygulamaları yönetin.
- İş gücünüzün eriştiği ve paylaştığı yöntemi denetleyerek şirket bilgilerinizi koruyun.
- Cihazların ve uygulamaların şirket güvenlik gereksinimleriyle uyumlu olduğundan emin olun.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Güncelleştirmeleri doğrudan kullanıcıların cihazlarına dağıtma

### <a name="codepush"></a>CodePush
App Center 'daki [CodePush](/appcenter/distribution/codepush/) hizmeti Apache Cordova ve yerel geliştiricilerin mobil uygulama güncelleştirmelerini doğrudan kullanıcılarınızın cihazlarına dağıtmasını sağlar. Geliştiricilerin belirli güncelleştirmeleri (örneğin, JS, HTML, CSS ve resim değişiklikleri) yayımlayabildiği merkezi bir depo olarak hareket eterek işe yarar. Ardından, uygulamalar, belirtilen istemci SDK 'larını kullanarak depodan güncelleştirme sorgulayabilir. Bu sayede, son kullanıcılarınız ile daha belirleyici ve doğrudan bir katılım modeli sayesinde, hataları ele alırken veya bir ikili uygulama mağazalarından yeniden dağıtmanıza gerek kalmadan bir ikiliyi oluşturmanızı veya küçük özellikleri eklemenizi sağlar.

**Temel Özellikler**
- Cordova ve yerel geliştiricilerin, mobil uygulama güncelleştirmelerini bir mağazaya serbest bırakmadan doğrudan kullanıcılarının cihazlarına dağıtmasını sağlar.
- İkiliyi yeniden oluşturmak ve ilgili depolarla yeniden dağıtmak için gerekli olmayan küçük özellikleri eklemek veya kaldırmak için faydalıdır.

**Başvur**
- [App Center kaydolun](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center kod gönderimi ile çalışmaya başlama](/appcenter/distribution/codepush/)
- [CodePush CLı](/appcenter/distribution/codepush/cli)