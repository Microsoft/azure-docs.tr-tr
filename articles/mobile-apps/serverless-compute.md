---
title: Azure Işlevleri ve diğer hizmetlerle sunucusuz mobil uygulama arka ucu oluşturun
description: Katı, sunucusuz mobil uygulama arka ucu oluşturmak için Işlem hizmetleri hakkında bilgi edinin.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b8378be675b86ada10b8442c40a54b635ee115a9
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795867"
---
# <a name="build-mobile-backend-components-with-compute-services"></a>Işlem Hizmetleri ile mobil arka uç bileşenleri oluşturun
Her mobil uygulamanın, veri depolama, iş mantığı ve güvenlik açısından sorumlu bir arka uca ihtiyacı vardır. Arka uç kodunu barındırmak ve yürütmek için altyapıyı yönetmek, bir dizi sunucuyu yazmanızı, sağlamanızı ve ölçeklendirmenizi, işletim sistemi güncelleştirmelerini ve donanımı yönetmenizi, güvenlik düzeltme eklerini uygulamayı ve ardından bu altyapı bileşenlerinin tümünü performans için izlemenizi gerektirir. kullanılabilirlik ve hataya dayanıklılık. Bu, sunucusuz mimarinin yönetilecek bir sunucu olmadığı, işletim sistemi veya yönetilecek yazılım/donanım güncelleştirmeleri olmadığı için kullanışlı bir şekilde sunulur. Uygulama oluşturmaya yönelik daha hızlı ve enerji tasarrufu sağlayan çok sayıda geliştirici süresi ve maliyeti kaydeder.

## <a name="benefits-of-compute"></a>Işlem avantajları
- Sunucu soyutlama: barındırma, düzeltme eki uygulama ve güvenlik konusunda endişelenmenize gerek yoktur ve geliştiricilerin koda odaklanmasını sağlar.
- Anında ve verimli ölçekleme, kaynakların otomatik olarak veya ihtiyacınız olan herhangi bir ölçeğe göre isteğe bağlı olarak sağlanmasını sağlar.
- Yüksek kullanılabilirlik ve hataya dayanıklılık.
- Mikro faturalandırma yalnızca kodunuzun gerçekten çalıştığı zaman için faturalandırılabilmenizi sağlar.
- Bulutta çalışan ve seçtiğiniz dilde kod yazın.

Mobil uygulamalarınızda sunucusuz işlem yeteneklerini etkinleştirmek için aşağıdaki hizmetleri kullanın.

## <a name="azure-functions"></a>Azure İşlevleri
[Azure işlevleri](https://azure.microsoft.com/services/functions/) , sunucu hakkında endişelenmenize gerek kalmadan kendi seçtiğiniz programlama dilinde yazılmış kodunuzu yürütmenize olanak tanıyan olay odaklı bir işlem deneyimidir. Geliştiricilerin uygulamayı veya üzerinde çalışacağı altyapıyı yönetmesi gerekmez. İşlevler isteğe bağlı olarak ölçeklendirilir ve yalnızca kodunuzun çalıştırıldığı süre için ödeme yaparsınız. Azure Işlevleri, bir mobil uygulama için API uygulamanın harika bir yoludur, çünkü uygulama ve bakım yapmak ve HTTP üzerinden erişilebilir olmaları çok kolaydır.

**Temel Özellikler**
- Geliştiricilerin, bir işlevin ne zaman çağrıldığı ve bağlandığı verileri tanımlamak için **Tetikleyicileri ve bağlamaları** kullanabileceği **olay odaklı ve ölçeklenebilir** .
- **Kendi bağımlılıklarınızı getirin** - İşlevler NuGet ve NPM'yi desteklediğinden, sık kullandığınız kitaplıklarınızı kullanabilirsiniz.
- **Tümleşik güvenlik** , http ile tetiklenen işlevleri Azure Active Directory, Facebook, Google, Twitter ve Microsoft hesabı gibi OAuth sağlayıcılarıyla korumanıza olanak sağlar.
- Farklı [Azure hizmetleri](/azure/azure-functions/functions-overview#integrations) ve hizmet olarak yazılım (SaaS) teklifleriyle **Basitleştirilmiş tümleştirme** .
- **Esnek geliştirme** , işlevlerinizi portalda doğrudan kodlamanızı veya sürekli tümleştirmeyi ayarlamanıza ve kodunuzu GitHub, Azure DevOps Services ve diğer desteklenen geliştirme araçları aracılığıyla dağıtmanıza imkan tanır.
- Işlevler çalışma zamanı, [GitHub](https://github.com/azure/azure-webjobs-sdk-script)'da **açık kaynak** ve kullanılabilir.
-  Geliştiricilerin tercih ettiği düzenleyiciyi kullanarak yerel olarak kod, test ve hata ayıklama yapabildikleri ve tümleşik araçlarla ve yerleşik DevOps yeteneklerini izlemeye yönelik kullanımı kolay Web arabirimimiz, **Gelişmiş geliştirme deneyimi** .
- **Çeşitli programlama dilleri ve barındırma seçenekleri** -kullanarak, Node C#. js, Java, JavaScript veya Python ile geliştirin.
- **Kullandıkça ödeme fiyatlandırma modeli** - Yalnızca kodunuzu çalıştırmaya harcanan zaman için ödeme yapın.

**Başvur**
- [Azure portalda](https://portal.azure.com)
- [Belgelerle](/azure/azure-functions/)
- [Azure Işlevleri Geliştirici Kılavuzu](/azure/azure-functions/functions-reference)
- [Hızlı başlangıçlar](/azure/azure-functions/functions-create-first-function-vs-code)
- [Örnekler](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="app-service"></a>App Service
[Azure App Service](https://azure.microsoft.com/services/app-service/) Azure App Service, Web uygulamaları oluşturmanıza ve barındırmanıza ve altyapıyı yönetmeksizin tercih ettiğiniz programlama dilinde API 'Leri yeniden dağıtmanıza olanak sağlar. Otomatik ölçeklendirme ve yüksek kullanılabilirlik sunar, hem Windows hem de Linux’ı destekler ve GitHub, Azure DevOps veya herhangi bir Git deposundan otomatik dağıtımlar sağlar.

**Temel Özellikler**
- ASP.NET, ASP.NET Core, Java, Ruby, Node. js, PHP veya Python için **birden çok dil ve çerçeve** desteği. Ayrıca, PowerShell ve diğer betikleri veya yürütülebilir dosyaları arka plan hizmetleri olarak da çalıştırabilirsiniz.
- **DevOps iyileştirmesi** -Azure DevOps, GitHub, Bitbucket, Docker Hub veya Azure Container Registry ile sürekli tümleştirme ve dağıtım ayarlayın. Azure PowerShell veya platformlar arası komut satırı arabirimini (CLı) kullanarak App Service uygulamalarınızı yönetin.
- Ölçeği el ile veya otomatik olarak genişletmek için **yüksek kullanılabilirliğe sahip küresel ölçek** .
- **SaaS platformları ve şirket içi verilere** , kurumsal SISTEMLER (SAP gibi), SaaS Hizmetleri (Salesforce gibi) ve İnternet Hizmetleri (Facebook gibi) için 50 ' den fazla bağlayıcıdan seçim yapmak için bağlantı. Karma Bağlantılar ve Azure sanal ağlarını kullanarak şirket içi verilere erişin.
- **Güvenlik ve uyumluluk** -Azure App Service ISO, SOC ve PCI uyumludur. Azure Active Directory veya sosyal oturum açma (Google, Facebook, Twitter ve Microsoft) ile kullanıcıların kimliğini doğrulayın. IP adresi kısıtlamaları oluşturun ve hizmet kimliklerini yönetin.
- Azure Marketi 'nde WordPress, Joomla ve Drupal gibi kapsamlı uygulama şablonları listesinden seçim yapabileceğiniz **uygulama şablonları** .
- Visual Studio 'da adanmış araçlarla **Visual Studio tümleştirmesi** oluşturma, dağıtma ve hata ayıklama işini kolaylaştırır.

**Başvur**
- [Azure portalda](https://portal.azure.com/)
- [Belgelerle](/azure/app-service/)
- [Hızlı başlangıçlar](/azure/app-service/app-service-web-get-started-dotnet)
- [Örnekler](/azure/app-service/samples-cli)
- [Öğreticiler](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/), barındırılan Kubernetes ortamınızı yöneterek kapsayıcılı uygulamaları, kapsayıcı yönetimi uzmanlığı gerekmeden hızla ve kolayca dağıtma olanağı sunar. Ayrıca, kaynakları isteğe bağlı olarak sağlama, yükseltme ve ölçeklendirme işlemlerini uygulamalarınızı çevrimdışı duruma geçirmeden yaparak sürekliliği olan işlemlerin ve bakımların yükünü ortadan kaldırır. 

**Temel Özellikler**
- **Mevcut uygulamaları kapsayıcılara kolayca geçirin** ve aks içinde çalıştırın.
- Mikro hizmet tabanlı uygulamaların **dağıtımını ve yönetimini kolaylaştırın** .
- Hız ve güvenlik arasındaki dengeyi elde etmek ve ölçeği daha hızlı bir şekilde sunmak için **AKS Için güvenli DevOps** .
- **Aks ve aci 'yi kullanarak** , saniyeler içinde başlayan aci 'nin içinde Pod sağlamak için kolayca ölçeklendirin.
- **IoT cihaz dağıtımı ve** isteğe bağlı yönetim.
- TensorFlow ve KubeFlow gibi araçların kullanımıyla **makine öğrenimi modeli eğitimi** .

**Başvur**
- [Azure portalda](https://portal.azure.com/)
- [Belgelerle](/azure/aks/)
- [Hızlı başlangıçlar](/azure/aks/kubernetes-walkthrough-portal)
- [Öğreticiler](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container Instances (acı)](https://azure.microsoft.com/services/container-instances/) basit uygulamalar, görev otomasyonu ve derleme işleri dahil yalıtılmış kapsayıcılarda çalışabilen her senaryo için harika bir çözümdür. VM 'Leri yönetmeden uygulamaları hızlı bir şekilde geliştirin.

**Temel Özellikler**
- VM 'leri sağlamaya ve yönetmeye gerek kalmadan, Azure 'da kapsayıcıları saniyeler IÇINDE başlatabilirim.
- **Genel IP bağlantısı ve özel DNS adı**.
- Uygulamanızı güvence altına alan **hiper yönetici düzeyinde güvenlik** , bir VM 'de olduğu gibi bir kapsayıcıda yalıtılmıştır.
- CPU çekirdeklerinin ve belleğin tam özelliklerine izin vererek en iyi kullanım için **Özel boyutlar** . İhtiyaçlarınıza göre ödeme yapar ve saniye başına faturalandırılırsınız. Böylece harcamalarınızı ihtiyaçlarınıza uyacak şekilde ayarlayabilirsiniz.
- **Kalıcı depolama** ve durumu alma ve kalıcı hale getirme, Azure dosya paylaşımlarının doğrudan bağlanmasını sağlar.
- **Linux ve Windows kapsayıcıları** aynı API ile zamanlandı.

**Başvur**
- [Azure portalda](https://portal.azure.com/)
- [Belgelerle](/azure/container-instances/)
- [Hızlı başlangıçlar](/azure/container-instances/container-instances-quickstart-portal)
- [Örnekler](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Öğreticiler](/azure/container-instances/container-instances-tutorial-prepare-app)