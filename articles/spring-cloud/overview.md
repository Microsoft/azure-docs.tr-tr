---
title: Azure Spring Cloud 'a giriş
description: Azure 'da Java Spring uygulamalarını dağıtmak ve yönetmek için Azure Spring Cloud 'ın özelliklerini ve avantajlarını öğrenin.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 12/02/2020
ms.author: brendm
ms.custom: devx-track-java, contperf-fy21q2
customer intent: As an Azure Cloud user, I want to deploy, run, and monitor Spring Boot microservices.
ms.openlocfilehash: a92e535cb1edeb0eeaa285e442b4b24766e20ead
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879367"
---
# <a name="what-is-azure-spring-cloud"></a>Azure Spring Cloud nedir?

Azure Spring Cloud, herhangi bir kod değişikliği yapmadan Spring Boot mikro hizmet uygulamalarını Azure 'a dağıtmayı kolaylaştırır.  Bu hizmet, geliştiricilerin koda odaklanabilmesi için yay bulutu uygulamalarının altyapısını yönetir.  Azure Spring Cloud, kapsamlı izleme ve tanılama, yapılandırma yönetimi, hizmet bulma, CI/CD tümleştirmesi, mavi-yeşil dağıtımlar ve daha fazlasını kullanarak yaşam döngüsü yönetimi sağlar.

## <a name="why-use-azure-spring-cloud"></a>Azure yay bulutu neden kullanılmalıdır?

Uygulamaların Azure Spring Cloud 'a dağıtılması birçok avantaj sunar.  Seçenekleriniz şunlardır:
* Mevcut yay uygulamalarını verimli bir şekilde geçirin ve bulut Ölçeklendirmesi ve maliyetlerini yönetin.
* Çeviklik ve teslimin hızını artırmak için yayı bulut desenleriyle modernleştirin.
* Bulut ölçeğinde Java 'Yı çalıştırın ve karmaşık altyapı olmadan daha yüksek kullanım yapın.
* Kapsayıcısız bağımlılıklar olmadan hızlı bir şekilde geliştirin ve dağıtın.
* Üretim iş yüklerini etkili ve kolay bir şekilde izleyin.

Azure Spring Cloud, hem Java [Spring Boot](https://spring.io/projects/spring-boot) hem de ASP.NET Core [steeltoe](https://steeltoe.io/) uygulamalarını destekler. Steeltoe desteği şu anda genel önizleme olarak sunulmaktadır. Genel Önizleme teklifleri, resmi sürümünden önceki yeni özelliklerle denemeler yapmanızı sağlar. Genel Önizleme özellikleri ve Hizmetleri üretim kullanımı için tasarlanmamıştır. Daha fazla bilgi için bkz. [SSS](https://azure.microsoft.com/support/faq/) veya dosya a [destek isteği](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="service-overview"></a>Hizmete genel bakış

Azure Spring Cloud, Azure ekosisteminin bir parçası olarak depolama, veritabanları, izleme ve daha fazlasını içeren diğer Azure hizmetlerine kolayca bağlamayı sağlar.  

  ![Azure yay bulutuna genel bakış](media/spring-cloud-principles/azure-spring-cloud-overview.png)

* Azure yay bulutu, altyapıyı yönetmenin sorunsuz bir şekilde uygulama oluşturmaya ve çalıştırmaya odaklanmanızı sağlayan Spring Boot uygulamaları için tam olarak yönetilen bir hizmettir.

* Yalnızca JARs veya kodunuzu dağıtmanız yeterlidir ve Azure Spring Cloud, uygulamalarınızı yay hizmeti çalışma zamanı ve yerleşik uygulama yaşam döngüsü ile otomatik olarak dağıtacaktır.

* İzleme basittir. Dağıtımdan sonra uygulama performansını izleyebilir, hataları giderebilir ve uygulamaları hızlı bir şekilde geliştirebilirsiniz. 

* Azure 'un ekosistemleri ve hizmetleriyle tam tümleştirme.

* Azure yay bulutu, tam olarak yönetilen altyapı, yerleşik yaşam döngüsü yönetimi ve izleme kolaylığı ile kurumsal olarak hazır.

## <a name="documentation-overview"></a>Belgelere genel bakış
Bu belge, Azure Spring Cloud Services 'ı kullanmaya başlama ve bu avantajlardan yararlanma hakkında bilgi sağlayan bölümleri içerir.

* başlarken
    * [İlk uygulamanızı yayınlama](spring-cloud-quickstart.md)
    * [Azure yay bulut hizmeti sağlama](spring-cloud-quickstart-provision-service-instance.md)
    * [Yapılandırma sunucusunu ayarlama]()
    * [Uygulama oluşturma ve dağıtma](spring-cloud-quickstart-deploy-apps.md)
    * [Günlük ölçümlerini ve izlemeyi kullanma](spring-cloud-quickstart-logs-metrics-tracing.md)
* Nasıl yapılır
    * [Geliştirme](how-to-prepare-app-deployment.md): mevcut bir Java Spring uygulamasını Azure Spring Cloud 'a dağıtmak için hazırlayın. Azure yay bulutu, düzgün yapılandırıldığında, Java Spring bulut uygulamalarını izlemek, ölçeklendirmek ve güncelleştirmek için güçlü hizmetler sağlar.
    * [Dağıtma](spring-cloud-howto-staging-environment.md): Azure yay bulutu 'ndaki mavi yeşil dağıtım modelini kullanarak hazırlama dağıtımı ayarlama. Mavi/yeşil dağıtım, yeni (yeşil) sürüm dağıtıldığı sırada mevcut (mavi) sürümü canlı tutma prensibine dayanan bir Azure DevOps Sürekli Teslim düzenidir.
    * [Uygulamaları yapılandırma](spring-cloud-howto-start-stop-delete.md): Azure Spring Cloud uygulamalarınızı başlatın, durdurun ve silin. Azure portal veya Azure CLı kullanarak Azure Spring Cloud 'da bir uygulamanın durumunu değiştirin.
    * [Ölçek](spring-cloud-howto-scale-manual.md): Azure Portal veya otomatik ölçeklendirme ayarlarını kullanarak Azure Spring Cloud panosunu kullanarak herhangi bir mikro hizmet uygulamasını ölçeklendirin. Veritabanları, depolama ve Anahtar kasaları gibi dış kaynaklarla iletişim kurmak için genel IP 'Ler kullanılabilir.
    * [Uygulamaları izleme](spring-cloud-howto-distributed-tracing.md): karmaşık sorunları kolayca ayıklamanız ve izlemek için dağıtılmış izleme araçları. Azure Spring Cloud, Azure 'ın Application Insights yay bulutuna karşı tümleşir. Bu tümleştirme Azure portal güçlü dağıtılmış izleme yeteneği sağlar.
    * [Güvenli uygulamalar](spring-cloud-howto-enable-system-assigned-managed-identity.md): Azure kaynakları, Azure Active Directory otomatik olarak yönetilen bir kimlik sağlar. Bu kimliği, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz.
    * [Diğer Azure hizmetleriyle tümleştirme](spring-cloud-howto-bind-cosmos.md): Spring Boot uygulamalarınızı el ile yapılandırmak yerine, seçili Azure hizmetlerini uygulamalarınıza otomatik olarak bağlayarak, örneğin uygulamanızı bir Azure Cosmos db veritabanına bağlayabilirsiniz.
    * [Otomatikleştir](spring-cloud-howto-cicd.md): sürekli tümleştirme ve sürekli teslim araçları, var olan uygulamalara en az çaba ve riske sahip güncelleştirmeleri hızlı bir şekilde dağıtmanızı sağlar. Azure DevOps bu temel görevleri düzenlemeye ve denetlemeye yardımcı olur. 
    * [Sorun giderme](spring-cloud-howto-self-diagnose-solve.md): Azure Spring Cloud Diagnostics, uygulama sorunlarını gidermenize yardımcı olacak etkileşimli bir deneyim sunar. Yapılandırma gerekmez. Sorunları bulduğunuzda, Azure yay Bulut Tanılaması sorunları tanımlar ve sorun gidermeye ve sorunları çözmeye yardımcı olan bilgiler için sizi yönlendirir.
    * [Geçiş](/azure/developer/java/migration/migrate-spring-boot-to-azure-spring-cloud): mevcut bir yay bulutu uygulamasını veya Spring Boot uygulamasını Azure Spring Cloud üzerinde çalışacak şekilde geçirme.

 Başlamak için bkz.:

> [!div class="nextstepaction"]
> [Yay bulutu hızlı başlangıç](spring-cloud-quickstart.md)

GitHub 'da örnekler mevcuttur: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).