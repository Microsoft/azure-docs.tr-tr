---
title: Visual Studio App Center ve Azure hizmetleriyle uygulamalarınızın yaşam döngüsünü otomatikleştirin
description: Mobil uygulamalarınız için sürekli derleme ve tümleştirme ayarlamaya yardımcı olan App Center gibi hizmetler hakkında bilgi edinin.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 07e8e0edc4638c6c0bf9acc205175aa731c0f8bc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795555"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Sürekli derleme ve tümleştirme sayesinde uygulamalarınızın yaşam döngüsünü otomatikleştirin

Geliştiriciler olarak kod yazar ve kod deposuna iade edersiniz, ancak depoya iade edilen işlemeler her zaman tutarlı olmayabilir. Aynı projede çalışan birden fazla geliştirici sayesinde, sorunlar tümleştirmeyle birlikte gelir ve takım işlerin çalışmaması durumunda, hatalar devam etmez ve proje geliştirme gecikir. Geliştiricilerin, hata olup olmadığını denetlemek için tüm yazılım kodunun oluşturulup test edildiğini beklemeniz gerekir ve bu işlem, işlemi yavaşlatır ve daha az yinelemeli hale getirir. 

**Sürekli derleme ve tümleştirmeyle**, geliştiriciler yalnızca kaynak kodu deposuna yaptığı değişiklikleri yerine getirme ve test etme işlemlerini basitleştirecek ancak her zaman çalışması için test ve doğrulamaları derleme ortamına yerleştirmelerine olanak sağlar kendi kodlarına karşı testler. Kaynak kodda yapılan tüm değişiklikler, depoya yapılan her bir kayıt olduğunda sürekli olarak oluşturulur. Her iadede, CI sunucusu geliştiricinin oluşturduğu testi doğrular ve yürütür. Testler geçmezse, daha fazla değişiklik için kod geri gönderilir. Bu, geliştiricinin oluşturulan derlemeleri bozmamaları ve ayrıca geliştirici üretkenliğini artıran tüm testleri bilgisayarlarında yerel olarak çalıştırmamaları için izin verir. 

## <a name="key-benefits"></a>Önemli avantajlar
- **Otomatik** derleme, test ve dağıtım işlem hattı.
- Daha hızlı yayın ücretleri sağlamak için **hataları tespit edin ve sorunları erken giderin** .
- **Kodu** daha sık işleyin ve uygulamaları hızla oluşturun.
- Herhangi bir sorunla karşılaşmadan kodu hızla değiştirme **esnekliği** .
- **Pazara daha hızlı** bir şekilde vakit, yalnızca iyi kalite kodunun tüm şekilde kullanmasını sağlar.
- Küçük kod, küçük kod parçalarını tek seferde tümleştirmesine izin verdiğinden **değişir** .
- **Ekip saydamlığının ve sorumluluklığın artması** , yalnızca müşterilerinizden ancak takımınızdan değil, **sürekli geri bildirimler** almanızı sağlar.

Mobil uygulamalarınızda sürekli tümleştirme işlem hattını etkinleştirmek için aşağıdaki hizmetleri kullanın.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center yapı](/appcenter/build/) hizmeti, takımınızın üzerinde çalıştığı, güvenli bir bulut altyapısı kullanarak yerel ve platformlar arası uygulamalar oluşturmanıza yardımcı olur. App Center, deponuzu kolayca bağlayabilir ve derleme sunucularını yerel olarak yapılandırma hakkında endişelenmenize gerek kalmadan ve iş arkadaşınızın makinesinde, karmaşık yapılandırmalarda ve bir iş arkadaşınızın makinesinde bulunan ancak sizinkiyle olmayan koddan Uygulamanızı buluta oluşturmaya başlayabilirsiniz.

App Center hizmetlerinin gücü sayesinde iş akışınızı daha da otomatik hale getirebilirsiniz. App Center dağıtmak üzere otomatik olarak, test ediciler ve genel uygulama depolarında yapıları serbest bırakın ya da bulutta bulunan binlerce gerçek cihazda ve işletim sistemi yapılandırmasında otomatik UI testleri çalıştırın App Center Test.

**Temel Özellikler**
- Dakikalar içinde **sürekli tümleştirme ayarlayın** ve uygulamaları daha sık ve hızlı bir şekilde oluşturun.
- **GitHub, BitBucket, Azure DevOps ve GitLab**ile tümleştirin.
- Yönetilen, bulutta barındırılan makinelerde **hızlı ve güvenli derlemeler** .
- **Derlemelerinizi "test Başlat" olarak etkinleştirin** ve uygulamanın gerçek dünyada IOS ve Android cihazlarda oluşturulup oluşturulmayacağını doğrulayın.
- **Yerel ve platformlar arası destek** -IOS, Android, MacOS, Windows, Xamarin, yerel olarak tepki verme.
- Kopyalama sonrası, derleme öncesi ve derleme sonrası betikleri ekleyerek **derlemelerinizi özelleştirin** .

**Başvur**
- [App Center kaydolun](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center derleme ile çalışmaya başlama](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 Azure DevOps 'daki bir hizmet olan [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), tercih ettiğiniz git sağlayıcınızla birlikte çalışan ve Azure dahil olmak üzere çok sayıda ana bulut hizmetine dağıtabileceğiniz, tam özellikli bir sürekli TÜMLEŞTIRME (CI) ve sürekli teslım (CD) hizmetidir. GitHub, GitHub Enterprise Server, GitLab, Bitbucket bulutu veya Azure Repos için kodunuzla başlayabilirsiniz. Daha sonra Microsoft Azure, Google Cloud Platform veya Amazon Web Services için kodunuzun derleme, test etme ve dağıtımını otomatik hale getirebilirsiniz.

**Temel Özellikler**
- Microsoft ve Microsoft olmayan (node. js, Java) tabanlı sunucu 'ya ek olarak hem yerel (Android, iOS, hem de Windows) ve platformlar arası (Xamarin, Cordova ve tepki verme) mobil uygulamaları için bir CI sunucusu ayarlamaya yönelik Basitleştirilmiş görev tabanlı deneyim teknolojilerinin.
- **Herhangi bir dil, platform ve bulut** oluşturma, test etme ve Node. js, Python, Java, php, Ruby, Go, C/C++, C#, Android ve iOS uygulamaları dağıtma. Linux, macOS ve Windows'da paralel çalıştırın. Azure, AWS ve GCP gibi bulut sağlayıcılarına dağıtın. Mobil uygulamaları Beta kanalları ve uygulama depoları aracılığıyla dağıtın.
- **Yerel kapsayıcı desteği** -kolay bir şekilde yeni kapsayıcılar oluşturun ve bunları herhangi bir kayıt defterine gönderin. Kapsayıcıları bağımsız konaklara veya Kubernetes 'e dağıtın.
- **Gelişmiş iş akışları** -kolay yapı zinciri ve çoklu aşamalı derlemeler. YAML, test tümleştirmesi, yayın kapıları, raporlama ve daha fazlası için destek.
- **Genişletilebilir** -topluluk tarafından oluşturulan bir dizi derleme, test ve Dağıtım görevi (bolluk 'Ten SonarCloud 'a yüzlerce uzantı) kullanın. Jenkins gibi diğer CI sistemlerinden de dağıtım yapabilir ve tümleştirmenize yardımcı olması için Web kancaları ve REST API 'Leri kullanabilirsiniz
- Ortak ve özel depolar için **bulutta barındırılan ücretsiz derlemeler** .
- Amazon Web Services, Google Cloud Platform vb. gibi **diğer bulut satıcılarına dağıtımı destekler** .

**Başvur**
- [Azure Pipelines Kılavuzu ile çalışmaya başlama](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps ile çalışmaya başlama](https://app.vsaex.visualstudio.com/signup/) 
- [Hızlı başlangıçlar](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Uygulama derlemelerinizi için doğru hizmeti seçin, [App Center derleme ile Azure Pipelines](/appcenter/build/choose-between-services)karşılaştıran makaleyi izleyin.
