---
title: Azure yay bulutu hakkında sık sorulan sorular | Microsoft Docs
description: Bu makalede, Azure Spring Cloud hakkında sık sorulan sorular yanıtlanmaktadır.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: f28ddfc272e6f95106595958cacfe732b2bfc3cc
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258699"
---
# <a name="azure-spring-cloud-faq"></a>Azure Spring Cloud hakkında SSS

Bu makalede, Azure Spring Cloud hakkında sık sorulan sorular yanıtlanmaktadır.

## <a name="general"></a>Genel

### <a name="why-azure-spring-cloud"></a>Neden Azure yay bulutu?

Azure Spring Cloud, Spring Cloud geliştiricileri için hizmet olarak platform (PaaS) sağlar. Azure Spring Cloud, uygulama altyapınızı, uygulama kodu ve iş mantığına odaklanabilmeniz için yönetir. Azure yay bulutu 'nda yerleşik olarak bulunan temel özellikler Eureka, config Server, hizmet kayıt defteri sunucusu, özetleme oluşturma hizmeti, mavi-yeşil dağıtım ve daha fazlasını içerir. Bu hizmet, geliştiricilerin uygulamalarını Azure Cosmos DB, MySQL için Azure veritabanı ve Redsıs için Azure önbelleği gibi diğer Azure hizmetleriyle bağlamasını da sağlar.

Azure Spring Cloud, Azure Izleyici, Application Insights ve Log Analytics tümleştirerek geliştiriciler ve işleçler için uygulama tanılama deneyimini geliştirir.

### <a name="how-secure-is-azure-spring-cloud"></a>Azure yay bulutu ne kadar güvenlidir?

Güvenlik ve gizlilik, Azure ve Azure Spring bulut müşterileri için en önemli önceliklerdir. Azure, tüm bu verileri güvenli bir şekilde şifreleyerek yalnızca müşterilerin uygulama verilerine, günlüklerine veya yapılandırmasına erişmesini sağlamaya yardımcı olur. 

* Azure Spring Cloud 'daki hizmet örnekleri birbirinden yalıtılmıştır.
* Azure yay bulutu, tüm TLS/SSL ve sertifika yönetimi sağlar.
* OpenJDK ve Spring Cloud çalışma zamanları için kritik güvenlik düzeltme ekleri, Azure Spring Cloud 'a mümkün olan en kısa sürede uygulanır.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Azure yay bulutu hangi bölgelerde kullanılabilir?

Doğu ABD, Doğu ABD 2, Orta ABD, Orta Güney ABD, Orta Kuzey ABD, Batı ABD, Batı ABD 2, Batı Avrupa, Kuzey Avrupa, UK Güney, Güneydoğu Asya, Avustralya Doğu, Kanada Orta, BAE Kuzey, Orta Hindistan, Kore Orta, Doğu Asya, Japonya Doğu, Güney Afrika Kuzey ve Çin Doğu 2 (bir pasta). [Daha Fazla Bilgi](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud)

### <a name="is-any-customer-data-stored-outside-of-the-specified-region"></a>Belirtilen bölgenin dışında depolanan müşteri verileri mi?

Azure yay bulutu, bölgesel bir hizmettir. Azure Spring Cloud 'daki tüm müşteri verileri, artıklık için belirtilen bölgenin aynı coğrafi bölge içinde birden çok bölgeye depolanır. Coğrafi ve bölge hakkında daha fazla bilgi edinmek için bkz. [Azure 'Da veri](https://azure.microsoft.com/global-infrastructure/data-residency/)yerleşimi.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure Spring Cloud 'ın bilinen sınırlamaları nelerdir?

Azure yay bulutu aşağıdaki bilinen sınırlamalara sahiptir:
    
* `spring.application.name` , her uygulamayı oluşturmak için kullanılan uygulama adı tarafından geçersiz kılınır.
* `server.port` Varsayılan bağlantı noktası 1025 ' dir. Başka herhangi bir değer uygulanırsa, geçersiz kılınır. Lütfen bu ayara de dikkat edin ve kodunuzda sunucu bağlantı noktası belirtmeyin.
* Azure portal ve Azure Resource Manager şablonları uygulama paketlerinin yüklenmesini desteklemez. Uygulama paketlerini yalnızca uygulamayı Azure CLı aracılığıyla dağıtarak yükleyebilirsiniz.

### <a name="what-pricing-tiers-are-available"></a>Hangi fiyatlandırma katmanları kullanılabilir? 
Hangi birini kullanmalıyım ve her katmandaki sınırlar nelerdir?
* Azure yay bulutu iki fiyatlandırma katmanı sunar: temel ve standart. Temel katman, geliştirme ve test için hedeflenir ve Azure yay bulutu 'nı deniyor. Standart katman, genel amaçlı üretim trafiği çalıştırmak için iyileştirilmiştir. Sınırlar ve özellik düzeyi karşılaştırması için bkz. [Azure Spring Cloud fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/spring-cloud/) .

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Nasıl geri bildirimde bulunabilirim ve rapor sorunları verebilir?

Azure Spring Cloud ile ilgili herhangi bir sorunla karşılaşırsanız bir [Azure destek isteği](../azure-portal/supportability/how-to-create-azure-support-request.md)oluşturun. Bir özellik isteği göndermek veya geri bildirim sağlamak için [Azure geri bildirimi](https://feedback.azure.com/forums/34192--general-feedback)' ne gidin.

## <a name="development"></a>Geliştirme

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Ben bir yay bulut geliştiricisiyim, ancak Azure 'da yeni. Azure yay bulut uygulaması geliştirmeyi öğrenmenin en hızlı yolu nedir?

Azure Spring Cloud ile çalışmaya başlamanın en hızlı yolu için [hızlı başlangıç: Azure Portal kullanarak bir Azure Spring Cloud uygulaması başlatma](spring-cloud-quickstart.md)' daki yönergeleri izleyin.

::: zone pivot="programming-language-java"
### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure yay bulutu hangi Java çalışma zamanına destekler?

Azure yay bulutu, Java 8 ve 11 ' i destekler. Bkz. [Java çalışma zamanı ve işletim sistemi sürümleri](#java-runtime-and-os-versions)

### <a name="is-spring-boot-24x-supported"></a>Spring Boot 2.4. x destekleniyor mu?
Spring Boot 2,4 ile ilgili bir sorun tanımladık ve şu anda bu sorunu çözmek için Spring Community ile çalışıyor. Bu sırada, uygulamalarınız ve Eureka arasında TLS kimlik doğrulamasını etkinleştirmek için lütfen bu iki bağımlılığı dahil edin.

```xml
<dependency> 
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-client</artifactId>
    <version>1.19.4</version>
</dependency>
<dependency>
    <groupId>com.sun.jersey.contribs</groupId>
    <artifactId>jersey-apache-client4</artifactId>
    <version>1.19.4</version>
</dependency>
```

::: zone-end

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Yay bulutu uygulama Günlüklerimi ve ölçümlerini nereden görüntüleyebilirim?

Uygulama Genel Bakış sekmesinde ve [Azure izleyici](../azure-monitor/essentials/data-platform-metrics.md#metrics-explorer) sekmesinde ölçümleri bulun.

Azure Spring Cloud, Azure depolama, EventHub ve [Log Analytics](../azure-monitor/logs/data-platform-logs.md)yay bulut uygulaması günlüklerini ve ölçümlerini vermeyi destekler. Log Analytics tablo adı *Appplatformlogsforspring*' dir. Nasıl etkinleştireceğinizi öğrenmek için bkz. [Tanılama Hizmetleri](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure yay bulutu dağıtılmış izlemeyi destekliyor mu?

Evet. Daha fazla bilgi için bkz. [öğretici: Azure Spring Cloud Ile dağıtılmış Izleme kullanma](spring-cloud-howto-distributed-tracing.md).

::: zone pivot="programming-language-java"
### <a name="what-resource-types-does-service-binding-support"></a>Hizmet bağlama hangi kaynak türlerini destekler?

Şu anda üç hizmet desteklenmektedir:
* Azure Cosmos DB
* MySQL için Azure Veritabanı
* Redsıs için Azure önbelleği.
::: zone-end

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Uygulamalarım içinden kalıcı birimleri görüntüleyebilir, ekleyebilir veya taşıyabilir miyim?

Evet.

### <a name="how-many-outbound-public-ip-addresses-does-an-azure-spring-cloud-instance-have"></a>Azure yay bulutu örneği kaç tane giden genel IP adresi kullanıyor?

Giden genel IP adreslerinin sayısı, katmanlara ve diğer faktörlere göre farklılık gösterebilir. 

| Azure yay bulutu örnek türü | Varsayılan giden genel IP adresi sayısı |
| -------------------------------- | ---------------------------------------------- |
| Temel katman örnekleri             | 1                                              |
| Standart katman örnekleri          | 2                                              |
| VNet ekleme örnekleri         | 1                                              |


### <a name="can-i-increase-the-number-of-outbound-public-ip-addresses"></a>Giden genel IP adresi sayısını artırabilir miyim?

Evet, daha fazla giden genel IP adresi istemek için bir [destek bileti](https://azure.microsoft.com/support/faq/)  açabilirsiniz.

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Azure yay bulut hizmeti örneğini sildiğimde/taşırken, uzantı kaynakları da silinir/taşınır mi?

Bu, uzantı kaynaklarına sahip olan kaynak sağlayıcılarının mantığına bağlıdır. Bir örneğin uzantı kaynakları `Microsoft.AppPlatform` aynı ad alanına ait değildir, bu nedenle davranış kaynak sağlayıcısına göre değişir. Örneğin, silme/taşıma işlemi, **Tanılama ayarları** kaynakları için basamaklanmaz. Silinen bir kaynak KIMLIĞIYLE yeni bir Azure yay bulutu örneği sağlandıysa veya önceki Azure yay bulutu örneği geri taşınırsa, önceki **Tanılama ayarları** kaynakları genişlemeye devam eder.

Azure CLı kullanarak Spring Cloud 'ın tanılama ayarlarını silebilirsiniz:

```azurecli
 az monitor diagnostic-settings delete --name $diagnosticSettingName --resource $azureSpringCloudResourceId
```

::: zone pivot="programming-language-java"
## <a name="java-runtime-and-os-versions"></a>Java çalışma zamanı ve işletim sistemi sürümleri

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>Azure Spring Cloud 'da hangi Java çalışma zamanı sürümleri desteklenir?

Azure yay bulutu, en son derlemeler ile Java LTS sürümlerini destekler, şu anda 2020 Haziran, Java 8 ve Java 11 desteklenir. Bkz [. Azure için JDK 'yi ve Azure Stack yüklemeyi](/azure/developer/java/fundamentals/java-jdk-install)

### <a name="who-built-these-java-runtimes"></a>Bu Java çalışma zamanlarını kim derlediniz?

Azul sistemleri. Microsoft ile Azul Systems’ın desteklediği Azul Zulu for Azure - Enterprise Edition JDK, OpenJDK’nın Azure ve Azure Stack’e yönelik çok platformlu, üretime hazır bir dağıtımıdır. Java SE uygulamalarını oluşturmak ve çalıştırmak için gereken tüm bileşenleri içerir.

### <a name="how-often-will-java-runtimes-get-updated"></a>Java çalışma zamanları ne sıklıkla güncelleştirilir?

LTS ve MTS JDK sürümleri, gerektiğinde üç aylık güvenlik güncelleştirmeleri, hata düzeltmeleri ve kritik bant dışı güncelleştirmeler ve düzeltme ekleri vardır. Bu destek, Java 7 ve 8 güvenlik güncelleştirmelerinin ve Java 11 gibi daha yeni Java sürümlerinde bildirilen hata düzeltmelerinin geri bağlantı noktalarını içerir.

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>Java 8 ve Java 11 LTS sürümleri ne kadar süreyle destekleniyor?

Bkz. [Azure ve Azure Stack için uzun süreli Java desteği](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* Java 8 LTS, Aralık 2030 ' ye kadar desteklenecektir.
* Java 11 LTS, 2027 Eylül tarihine kadar desteklenecektir.

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>Yerel geliştirme için desteklenen bir Java çalışma zamanını nasıl indirebilirim?

Bkz. [Azure için JDK 'Yi yükleyip Azure Stack](/azure/developer/java/fundamentals/java-jdk-install).

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>Eski Java çalışma zamanları için devre dışı bırakma ilkesi nedir?

Genel bildirim, herhangi bir eski çalışma zamanı sürümü kullanımdan kaldırıldıktan sonra 12 ay sonra gönderilir. Sonraki bir sürüme geçiş yapmak için 12 aya sahip olursunuz.

* Bir Java sürümünü devre dışı bırakacağız, abonelik yöneticileri e-posta bildirimi alır.
* Devre dışı bırakma bilgileri belgelerde yayımlanacak.

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>Java çalışma zamanı düzeyinde sorunlar için nasıl destek alabilirim?

Azure desteği ile bir destek bileti açabilirsiniz.  Bkz. [Azure destek isteği oluşturma](../azure-portal/supportability/how-to-create-azure-support-request.md).

### <a name="what-is-the-operation-system-to-run-my-apps"></a>Uygulamalarımı çalıştırmak için işletim sistemi nedir?

En son Ubuntu LTS sürümü kullanılır; Şu anda [ubuntu 20,04 LTS (odak)](https://releases.ubuntu.com/focal/) varsayılan işletim sistemi.

### <a name="how-often-are-os-security-patches-applied"></a>İşletim sistemi güvenlik düzeltme ekleri ne sıklıkta uygulandı?

Azure Spring Cloud için geçerli olan güvenlik düzeltme ekleri üretime aylık olarak dağıtılır.
Azure Spring Cloud için geçerli olan kritik güvenlik düzeltme ekleri (CVE puanı >= 9), mümkün olan en kısa sürede kullanıma sunulacaktır.
::: zone-end

## <a name="deployment"></a>Dağıtım

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure yay bulutu mavi yeşil dağıtımı destekliyor mu?
Evet. Daha fazla bilgi için bkz. [hazırlama ortamı ayarlama](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Uygulama kapsayıcılarımı değiştirmek için Kubernetes 'e erişebilir miyim?

Hayır.  Azure Spring Cloud, geliştirici temel mimariden soyutlar, uygulama kodu ve iş mantığına odaklanmanızı sağlar.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure yay bulutu, kaynaktan kapsayıcılar oluşturmayı destekliyor mu?

Evet. Daha fazla bilgi için bkz. [Spring Cloud uygulamanızı kaynak koddan başlatma](spring-cloud-quickstart.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure yay bulutu, uygulama örneklerinde otomatik ölçeklendirmeyi destekliyor mu?

Evet.  Daha fazla bilgi için bkz. [Otomatik ölçeklendirmeyi ayarlama](spring-cloud-tutorial-setup-autoscale.md).

::: zone pivot="programming-language-java"
### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Mevcut yay bulutu mikro hizmetlerini Azure Spring Cloud 'a geçirmek için en iyi uygulamalar nelerdir?

Mevcut yay bulutu mikro hizmetlerini Azure Spring buluta geçirirken, aşağıdaki en iyi yöntemleri gözlemlemek iyi bir fikirdir:
* Tüm uygulama bağımlılıklarının çözülmesi gerekir.
* Yapılandırma girdilerinizi, ortam değişkenlerinizi ve JVM parametrelerinizi Azure Spring Cloud 'daki dağıtımla karşılaştırabilmeniz için hazırlayın.
* Hizmet bağlamayı kullanmak istiyorsanız, Azure hizmetlerinizi gözden geçirdiğinizden ve uygun erişim izinlerini ayarlamış olduğunuzdan emin olun.
* Hizmet bulma hizmeti, yapılandırma sunucusu gibi Azure Spring Cloud tarafından yönetilen hizmetlerle çakışabilecek gömülü Hizmetleri kaldırmanızı veya devre dışı bırakmanızı öneririz.
* Resmi, kararlı özette yay kitaplıklarını kullanmanızı öneririz. Özet Ilkelerin kitaplıklarının resmi olmayan, beta veya çatallı sürümlerinin hizmet düzeyi anlaşması (SLA) desteği yoktur.

Geçişten sonra, uygulama örneklerinin uygun şekilde ölçeklendirdiğinden emin olmak için CPU/RAM ölçümleri ve ağ trafiğinizi izleyin.
::: zone-end

::: zone pivot="programming-language-csharp"
## <a name="net-core-versions"></a>.NET Core sürümleri

### <a name="which-net-core-versions-are-supported"></a>Hangi .NET Core sürümleri destekleniyor?

.NET Core 3,1 ve sonraki sürümleri.

### <a name="how-long-will-net-core-31-be-supported"></a>.NET Core 3,1 ne kadar desteklenecek?

3 Aralık 2022 ' a kadar. Bkz. [.NET Core destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).
::: zone-end


## <a name="troubleshooting"></a>Sorun giderme

### <a name="what-are-the-impacts-of-service-registry-rarely-unavailable"></a>Hizmet kayıt defterinin etkileri nadiren kullanılamıyor mu?

Nadiren meydana gelen bazı senaryolarda, şöyle bir hata görebilirsiniz: 
```
RetryableEurekaHttpClient: Request execution failure with status code 401; retrying on another server if available
```
Uygulama günlüklerinizin. Bu sorun, ağ kararsız veya diğer ağ sorunları nedeniyle Spring Framework tarafından çok düşük bir ücret sunarak tanıtılmıştır. 

Kullanıcı deneyimine hiçbir etkileri olmaması gerekir, Eureka istemcisinde bu işlemi gerçekleştirmek için hem sinyal hem de yeniden deneme ilkesi vardır. Bunu tek geçici bir hata olarak düşünebilirsiniz ve güvenle atlayabilirsiniz.

Bu bölümü geliştireceğiz ve kısa bir süre sonra kullanıcıların uygulamalarından bu hatadan kaçınacağız.


## <a name="next-steps"></a>Sonraki adımlar

Başka sorularınız varsa bkz. [Azure yay bulutu sorun giderme kılavuzu](spring-cloud-troubleshoot.md).
