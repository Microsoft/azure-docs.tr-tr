---
title: Azure yay bulutu hakkında sık sorulan sorular | Microsoft Docs
description: Bu makalede, Azure Spring Cloud hakkında sık sorulan sorular yanıtlanmaktadır.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 40275b3f643517236db26be8c2784144526fb7a1
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151753"
---
# <a name="azure-spring-cloud-faq"></a>Azure Spring Cloud hakkında SSS

Bu makalede, Azure Spring Cloud hakkında sık sorulan sorular yanıtlanmaktadır. 

## <a name="general"></a>Genel

### <a name="why-azure-spring-cloud"></a>Neden Azure yay bulutu?

Azure Spring Cloud, Spring Cloud geliştiricileri için hizmet olarak platform (PaaS) sağlar. Azure Spring Cloud, uygulama altyapınızı, uygulama kodu ve iş mantığına odaklanabilmeniz için yönetir. Azure yay bulutu 'nda yerleşik olarak bulunan temel özellikler Eureka, config Server, hizmet kayıt defteri sunucusu, özetleme oluşturma hizmeti, mavi-yeşil dağıtımlar ve daha fazlasını içerir. Bu hizmet, geliştiricilerin uygulamalarını Azure Cosmos DB, MySQL için Azure veritabanı ve Redsıs için Azure önbelleği gibi diğer Azure hizmetleriyle bağlamasını da sağlar.

Azure Spring Cloud, Azure Izleyici, Application Insights ve Log Analytics tümleştirerek geliştiriciler ve işleçler için uygulama tanılama deneyimini geliştirir.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure yay bulutu hangi hizmet planlarını sunuyor?

Azure yay bulutu, önizleme döneminde bir hizmet planı sunar.  Bir yay bulutu dağıtımı 16 vCPU çekirdeği ve 32 gigabayt (GB) bellek içerir.  Bir dağıtım içindeki her mikro hizmet örneği için üst sınır 8 GB bellek içeren 4 vCPU çekirdekdir.

Kaynak | Miktar
------- | -------
Yay bulut uygulaması başına uygulama örnekleri | 20
Azure yay bulut hizmeti örneği başına toplam uygulama örnekleri | 50 *
Abonelik başına bölge başına Azure yay bulut hizmeti örnekleri | iki
Kalıcı birimler | 10 x 50 GB

_limiti yükseltmek için \* bir [destek bileti](https://azure.microsoft.com/support/faq/)açın._

Daha fazla bilgi için bkz. [Azure DESTEĞI SSS](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Azure yay bulutu ne kadar güvenlidir?

Güvenlik ve gizlilik, Azure ve Azure Spring bulut müşterileri için en önemli önceliklerdir. Azure, tüm bu verileri güvenli bir şekilde şifreleyerek yalnızca müşterilerin uygulama verilerine, günlüklerine veya yapılandırmasına erişmesini sağlamaya yardımcı olur. Azure Spring Cloud 'daki tüm hizmet örnekleri birbirinden yalıtılmıştır.

Azure yay bulutu, tüm SSL ve sertifika yönetimi sağlar.

OpenJDK ve Spring Cloud çalışma zamanları için kritik güvenlik düzeltme ekleri, Azure Spring Cloud 'a mümkün olan en kısa sürede uygulanır.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Azure yay bulutu hangi bölgelerde kullanılabilir?

Doğu ABD, Batı ABD 2, Batı Avrupa ve Güneydoğu Asya.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure Spring Cloud 'ın bilinen sınırlamaları nelerdir?

Önizleme sürümü sırasında, Azure yay bulutu aşağıdaki bilinen sınırlamalara sahiptir:

* `spring.application.name`, her uygulamayı oluşturmak için kullanılan uygulama adı tarafından geçersiz kılınır.
* Git deposundan yapılandırma dosyasında `server.port` izin verilmez. Bunu yapılandırma dosyasına eklemek, büyük olasılıkla uygulamanızı diğer uygulamalardan veya internet 'ten ulaşılamaz hale işleyecek.
* Azure portal ve Azure Resource Manager şablonları uygulama paketlerinin yüklenmesini desteklemez. Uygulama paketlerini yalnızca uygulamayı Azure CLı aracılığıyla dağıtarak yükleyebilirsiniz.
* Kota sınırlamaları hakkında bilgi edinmek için bkz. [Azure yay bulutu hangi hizmet planlarına sunuyor?](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Nasıl geri bildirimde bulunabilirim ve rapor sorunları verebilir?

Azure Spring Cloud ile ilgili herhangi bir sorunla karşılaşırsanız bir [Azure destek isteği](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)oluşturun. Bir özellik isteği göndermek veya geri bildirim sağlamak için [Azure geri bildirimi](https://feedback.azure.com/forums/34192--general-feedback)' ne gidin.

## <a name="development"></a>Geliştirme

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Ben bir yay bulut geliştiricisiyim, ancak Azure 'da yeni. Azure yay bulut uygulaması geliştirmeyi öğrenmenin en hızlı yolu nedir?

Azure Spring Cloud ile çalışmaya başlamanın en hızlı yolu için [hızlı başlangıç: Azure Portal kullanarak bir Azure Spring Cloud uygulaması başlatma](spring-cloud-quickstart-launch-app-portal.md)' daki yönergeleri izleyin.

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure yay bulutu hangi Java çalışma zamanına destekler?

Azure yay bulutu, Java 8 ve 11 ' i destekler.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Yay bulutu uygulama Günlüklerimi ve ölçümlerini nereden görüntüleyebilirim?

Uygulama Genel Bakış sekmesinde ve [Azure izleyici](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) sekmesinde ölçümleri bulun.

Azure Spring Cloud, Azure depolama, EventHub ve [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries)yay bulut uygulaması günlüklerini ve ölçümlerini vermeyi destekler. Log Analytics tablo adı *Appplatformlogsforspring*' dir. Nasıl etkinleştireceğinizi öğrenmek için bkz. [Tanılama Hizmetleri](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure yay bulutu dağıtılmış izlemeyi destekliyor mu?

Evet. Daha fazla bilgi için bkz. [öğretici: Azure Spring Cloud Ile dağıtılmış Izleme kullanma](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Hizmet bağlama hangi kaynak türlerini destekler?

Şu anda üç hizmet desteklenmektedir: Azure Cosmos DB, MySQL için Azure veritabanı ve Redsıs için Azure önbelleği.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Uygulamalarım içinden kalıcı birimleri görüntüleyebilir, ekleyebilir veya taşıyabilir miyim?

Evet.

## <a name="deployment"></a>Dağıtım

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure yay bulutu mavi yeşil dağıtımı destekliyor mu?
Evet. Daha fazla bilgi için bkz. [hazırlama ortamı ayarlama](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Uygulama kapsayıcılarımı değiştirmek için Kubernetes 'e erişebilir miyim?

Hayır.  Azure Spring Cloud, geliştirici temel mimariden soyutlar, uygulama kodu ve iş mantığına odaklanmanızı sağlar.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure yay bulutu, kaynaktan kapsayıcılar oluşturmayı destekliyor mu?

Evet. Daha fazla bilgi için bkz. [Spring Cloud uygulamanızı kaynak koddan başlatma](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure yay bulutu, uygulama örneklerinde otomatik ölçeklendirmeyi destekliyor mu?

Hayır.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Mevcut yay bulutu mikro hizmetlerini Azure Spring Cloud 'a geçirmek için en iyi uygulamalar nelerdir?

Mevcut yay bulutu mikro hizmetlerini Azure Spring buluta geçirirken, aşağıdaki en iyi yöntemleri gözlemlemek iyi bir fikirdir:
* Tüm uygulama bağımlılıklarının çözülmesi gerekir.
* Yapılandırma girdilerinizi, ortam değişkenlerinizi ve JVM parametrelerinizi Azure Spring Cloud 'daki dağıtımla karşılaştırabilmeniz için hazırlayın.
* Hizmet bağlamayı kullanmak istiyorsanız, Azure hizmetlerinizi gözden geçirdiğinizden ve uygun erişim izinlerini ayarlamış olduğunuzdan emin olun.
* Hizmet bulma hizmeti, yapılandırma sunucusu gibi Azure Spring Cloud tarafından yönetilen hizmetlerle çakışabilecek gömülü Hizmetleri kaldırmanızı veya devre dışı bırakmanızı öneririz.
* Resmi, kararlı özette yay kitaplıklarını kullanmanızı öneririz. Özet Ilkelerin kitaplıklarının resmi olmayan, beta veya çatallı sürümlerinin hizmet düzeyi anlaşması (SLA) desteği yoktur.

Geçişten sonra, uygulama örneklerinin uygun şekilde ölçeklendirdiğinden emin olmak için CPU/RAM ölçümleri ve ağ trafiğinizi izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Başka sorularınız varsa bkz. [Azure yay bulutu sorun giderme kılavuzu](spring-cloud-troubleshoot.md).
