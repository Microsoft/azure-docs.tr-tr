---
title: Azure yay bulutu için sorun giderme kılavuzu | Microsoft Docs
description: Azure yay bulutu için sorun giderme kılavuzu
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 2a26a71e956a7e1313af9e99cc92232d0caf91e7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878810"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Yaygın Azure Spring Cloud sorunlarını giderme

Bu makalede, Azure Spring Cloud geliştirme sorunlarını gidermeye yönelik yönergeler sağlanmaktadır. Daha fazla bilgi için bkz. [Azure Spring Cloud SSS](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Kullanılabilirlik, performans ve uygulama sorunları

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Uygulamam başlatılamıyor (örneğin, uç nokta bağlanamaz veya birkaç yeniden denemeden sonra bir 502 döndürür)

Günlükleri Azure Log Analytics dışarı aktarın. Yay uygulama günlüklerinin tablosu *Appplatformlogsforspring* olarak adlandırılır. Daha fazla bilgi edinmek için bkz. [Tanılama ayarlarıyla günlükleri ve ölçümleri çözümleme](diagnostic-services.md).

Günlüklerinizin şu hata iletisi görünebilir:

> "org. springframework. Context. ApplicationContextException: Web sunucusu başlatılamıyor"

İleti olası iki sorunlardan birini gösterir: 
* Beans veya bağımlılıklarından biri eksik.
* Çekirdek özelliklerinden biri eksik veya geçersiz. Bu durumda, "Java. lang. Mıgalargumentexception" büyük olasılıkla görüntülenmeyecektir.

Hizmet bağlamaları Ayrıca uygulama başlatma hatalarıyla da neden olabilir. Günlükleri sorgulamak için, ilişkili hizmetlerle ilgili anahtar sözcükleri kullanın. Örneğin, uygulamanızın yerel sistem saatine ayarlanmış bir MySQL örneğine bağlanması olduğunu varsayalım. Uygulama başlamazsa, günlükte şu hata iletisi görünebilir:

> "Java. Sql. SQLException: sunucu saat dilimi değeri ' Eşgüdümlü Evrensel Saat ' tanınmıyor veya birden çok saat dilimini temsil ediyor."

Bu hatayı onarmak için MySQL örneğinizin bölümüne gidin `server parameters` ve `time_zone` değeri *sistemden* *+ 0:00* olarak değiştirin.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Uygulamam kilitleniyor veya beklenmeyen bir hata oluşturuyor

Uygulama kilitlenmelerinden hata ayıklaması yaparken, uygulamanın çalışma durumunu ve bulma durumunu denetleyerek başlatın. Bunu yapmak için, tüm uygulamaların durumlarının _çalışır_ durumda olduğundan emin olmak Için Azure Portal _uygulama yönetimi_ _' ne gidin._

* Durum _çalışıyor_ ancak bulma durumu _güncel_ değilse, ["Uygulamam kaydedilemedi"](#my-application-cant-be-registered) bölümüne gidin.

* Bulma durumu çalışıyorsa, uygulamanın sistem durumunu denetlemek için _Ölçümler ' e_ gidin. Aşağıdaki ölçümleri inceleyin:


  - `TomcatErrorCount` (_Tomcat. Global. Error_): tüm Spring Application özel durumları burada sayılır. Bu sayı büyükse, uygulama günlüklerinizi incelemek için Azure Log Analytics 'ye gidin.

  - `AppMemoryMax` (_JVM. Memory. Max_): uygulama için kullanılabilir maksimum bellek miktarı. Miktar tanımsız olabilir veya tanımlanmışsa zaman içinde değişebilir. Tanımlı ise, kullanılan ve kaydedilmiş bellek miktarı her zaman en fazla eşittir. Ancak, `OutOfMemoryError` ayrılan *> kullanılan* belleği artırmayı denerse, *<= Max* hala doğru olsa bile, bellek ayırma bir iletiyle başarısız olabilir. Böyle bir durumda, parametresini kullanarak en büyük yığın boyutunu artırmayı deneyin `-Xmx` .

  - `AppMemoryUsed` (_JVM. Memory. kullanılan_): uygulama tarafından şu anda kullanılmakta olan bellek miktarı (bayt cinsinden). Normal bir Java uygulaması için, bu ölçüm serisi, bellek kullanımı artmasıyla küçük artışlarla arttığı ve azaldıkça ve daha sonra model yinelenirse bir *testere dişi* modelini oluşturur. Bu ölçüm serisi, Java sanal makinesi içindeki çöp toplama işlemi nedeniyle, koleksiyon eylemlerinin testere dişi deseninin düştüğini gösterdiği yerdir.
    
    Bu ölçüm, aşağıdaki gibi bellek sorunlarını belirlemenize yardımcı olmak için önemlidir:
    * Çok başındaki bir bellek patlaması.
    * Belirli bir mantık yolu için aşırı gerilim bellek ayırması.
    * Aşamalı bellek sızıntıları.
  Daha fazla bilgi için bkz. [ölçümler](spring-cloud-concept-metrics.md).
  
* Uygulama başlamazsa, uygulamanın geçerli JVM parametreleri olduğunu doğrulayın. JVM belleği çok yüksek ayarlandıysa, günlüklerinizin şu hata iletisi görünebilir:

  >"gerekli bellek 2728741K, ayırma için kullanılabilir 2000M 'den büyük"



Azure Log Analytics hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici 'de Log Analytics kullanmaya başlama](../azure-monitor/logs/log-analytics-tutorial.md).

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Uygulamam yüksek CPU kullanımı veya yüksek bellek kullanımıyla karşılaşıyor

Uygulamanız yüksek CPU veya bellek kullanımı yaşıyorsa, iki işlemlerden biri doğrudur:
* Tüm uygulama örnekleri yüksek CPU veya bellek kullanımı ile karşılaşırsınız.
* Bazı uygulama örnekleri yüksek CPU veya bellek kullanımı yaşar.

Hangi durumun geçerli olduğunu belirlemek için aşağıdakileri yapın:

1. **Ölçümler**' e gidin ve ardından **hizmet CPU kullanımı yüzdesi** ya da **kullanılan hizmet belleği**' nı seçin.
2. İzlemek istediğiniz uygulamayı belirtmek için bir **App =** Filter ekleyin.
3. Ölçümleri **örneğe** göre Böl.

*Tüm örnekler* yüksek CPU veya bellek kullanımı yaşıyorsa, uygulamayı ÖLÇEKLENDIRMENIZ veya CPU ya da bellek kullanımını ölçeklendirmeniz gerekir. Daha fazla bilgi için bkz. [öğretici: Azure Spring Cloud 'da bir uygulamayı ölçeklendirme](spring-cloud-howto-scale-manual.md).

*Bazı örnekler* yüksek CPU veya bellek kullanımı yaşıyorsa, örnek durumunu ve bulma durumunu kontrol edin.

Daha fazla bilgi için bkz. [Azure yay bulutu Için ölçümler](spring-cloud-concept-metrics.md).

Tüm örnekler çalışıyor ve çalışıyorsa, uygulama günlüklerinizi sorgulamak ve kod mantığınızı gözden geçirmek için Azure Log Analytics 'ye gidin. Bu, bunlardan herhangi birinin ölçek Bölümlendirmeyi etkileyebileceğini görmenizi sağlayacak. Daha fazla bilgi için bkz. [Tanılama ayarlarıyla günlükleri ve ölçümleri çözümleme](diagnostic-services.md).

Azure Log Analytics hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici 'de Log Analytics kullanmaya başlama](../azure-monitor/logs/log-analytics-tutorial.md). [Kusto sorgu dilini](/azure/kusto/query/)kullanarak günlükleri sorgulayın.

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Spring uygulamanızı Azure Spring Cloud 'a dağıtmaya yönelik denetim listesi

Uygulamanızı eklemeden önce, aşağıdaki ölçütlere uyduğundan emin olun:

* Uygulama, belirtilen Java çalışma zamanı sürümüyle yerel olarak çalışabilir.
* Ortam yapılandırması (CPU/RAM/örnekler), uygulama sağlayıcısı tarafından ayarlanan en düşük gereksinimleri karşılar.
* Yapılandırma öğelerinin beklenen değerleri vardır. Daha fazla bilgi için bkz. [config Server](spring-cloud-howto-config-server.md).
* Ortam değişkenlerinin beklenen değerleri vardır.
* JVM parametrelerinin beklenen değerleri vardır.
* Katıştırılmış _yapılandırma sunucusu_ ve _Spring Service kayıt defteri_ hizmetlerini uygulama paketinden devre dışı bırakmanız veya kaldırmanız önerilir.
* _Hizmet Bağlama_ aracılığıyla bağlanacak Azure kaynakları varsa, hedef kaynakların çalıştığından emin olun.

## <a name="configuration-and-management"></a>Yapılandırma ve Yönetim

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Azure yay bulut hizmeti örneği oluştururken bir sorunla karşılaştım

Azure portal kullanarak bir Azure Spring Cloud Service örneği ayarlarken, Azure yay bulutu sizin için doğrulama gerçekleştirir.

Ancak Azure [CLI](/cli/azure/get-started-with-azure-cli) veya [Azure Resource Manager şablonunu](../azure-resource-manager/index.yml)kullanarak Azure yay bulut hizmeti örneğini ayarlamaya çalışırsanız şunları doğrulayın:

* Abonelik etkin.
* Konum, Azure Spring Cloud tarafından [desteklenir](spring-cloud-faq.md) .
* Örnek için kaynak grubu zaten oluşturulmuş.
* Kaynak adı adlandırma kuralına uyar. Yalnızca küçük harf, sayı ve kısa çizgi içermelidir. İlk karakter harf olmalıdır. Son karakter harf veya sayı olmalıdır. Değer, 2 ile 32 karakter arasında olmalıdır.

Azure yay bulut hizmeti örneğini Kaynak Yöneticisi şablonunu kullanarak ayarlamak istiyorsanız, önce [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlamak](../azure-resource-manager/templates/template-syntax.md)için bölümüne bakın.

Azure Spring Cloud Service örneğinin adı, altında bir alt etki alanı adı istemek için kullanılır, bu `azureapps.io` nedenle ad var olan bir adla çakışırsa kurulum başarısız olur. Etkinlik günlüklerinde daha fazla ayrıntı bulabilirsiniz.

### <a name="i-cant-deploy-a-net-core-app"></a>.NET Core uygulamasını dağıtamıyorum

.NET Core Steeltoe uygulamasının bir *. zip* dosyasını Azure portal veya Kaynak Yöneticisi şablonunu kullanarak karşıya yükleyemezsiniz.

[Azure CLI](/cli/azure/get-started-with-azure-cli)kullanarak uygulama paketinizi dağıttığınızda, Azure CLI dağıtım ilerlemesini düzenli aralıklarla yoklar ve sonunda dağıtım sonucunu görüntüler.

Uygulamanızın doğru *. zip* dosya biçiminde paketlendiğinden emin olun. Doğru paketlenmemişse, işlem yanıt vermeyi durdurur veya bir hata iletisi alırsınız.

### <a name="i-cant-deploy-a-jar-package"></a>JAR paketini dağıtamıyorum

Java arşiv dosyası (JAR)/Source paketini Azure portal veya Kaynak Yöneticisi şablonunu kullanarak karşıya yükleyemezsiniz.

[Azure CLI](/cli/azure/get-started-with-azure-cli)kullanarak uygulama paketinizi dağıttığınızda, Azure CLI dağıtım ilerlemesini düzenli aralıklarla yoklar ve sonunda dağıtım sonucunu görüntüler.

Yoklama kesintiye uğrarsa, yine de şu komutu kullanarak dağıtım günlüklerini getirebilirsiniz:

`az spring-cloud app show-deploy-log -n <app-name>`

Uygulamanızın doğru [YÜRÜTÜLEBILIR jar biçiminde](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)paketlendiğinden emin olun. Doğru paketlenmemişse, aşağıdakine benzer bir hata iletisi alırsınız:

> "Hata: geçersiz veya bozuk jarfile/jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Kaynak Paketi dağıtamıyorum

Azure portal veya Kaynak Yöneticisi şablonunu kullanarak JAR/kaynak paketini karşıya yükleyemezsiniz.

[Azure CLI](/cli/azure/get-started-with-azure-cli)kullanarak uygulama paketinizi dağıttığınızda, Azure CLI dağıtım ilerlemesini düzenli aralıklarla yoklar ve sonunda dağıtım sonucunu görüntüler.

Yoklama kesintiye uğrarsa, yine de şu komutu kullanarak derleme ve dağıtım günlüklerini getirebilirsiniz:

`az spring-cloud app show-deploy-log -n <app-name>`

Ancak, bir Azure yay bulut hizmeti örneğinin bir kaynak paketi için tek seferde yalnızca bir yapı işi tetikleyebileceğini unutmayın. Daha fazla bilgi için bkz. [Azure yay bulutu 'nda](spring-cloud-howto-staging-environment.md) [uygulama dağıtma](spring-cloud-quickstart.md) ve hazırlama ortamı ayarlama.

### <a name="my-application-cant-be-registered"></a>Uygulamam kaydedilemiyor

Çoğu durumda bu durum, *gerekli bağımlılıklar* ve *hizmet bulma* işlemi proje nesne modeli (pod) dosyasında düzgün şekilde yapılandırılmadığı zaman oluşur. Yapılandırıldıktan sonra, yerleşik hizmet kayıt defteri sunucu uç noktası, uygulamanızla bir ortam değişkeni olarak eklenir. Uygulamalar daha sonra kendilerini hizmet kayıt defteri sunucusuna kaydeder ve diğer bağımlı mikro hizmetleri bulur.

Yeni kaydedilmiş bir örnek trafik almaya başlamadan önce en az iki dakika bekleyin.

Mevcut bir yay bulut tabanlı çözümünü Azure 'a geçiriyorsanız, Azure Spring Cloud tarafından sağlanmış yönetilen örneklerle çakışmamak için geçici _hizmet kayıt_ defterinizin ve _yapılandırma sunucusu_ örneklerinizin kaldırıldığından (veya devre dışı bırakıldığından) emin olun.

Ayrıca Azure Log Analytics _hizmet kayıt defteri_ istemci günlüklerine bakabilirsiniz. Daha fazla bilgi için bkz. [Tanılama ayarlarıyla günlükleri ve ölçümleri çözümleme](diagnostic-services.md)

Azure Log Analytics hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici 'de Log Analytics kullanmaya başlama](../azure-monitor/logs/log-analytics-tutorial.md). [Kusto sorgu dilini](/azure/kusto/query/)kullanarak günlükleri sorgulayın.

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Uygulamamın ortam değişkenlerini incelemek istiyorum

Ortam değişkenleri, Azure Spring Cloud Framework 'ü bilgilendirerek uygulamanızı oluşturan hizmetlerin nerede ve nasıl yapılandırılacağını anladığından emin olun. Ortam değişkenlerinizin doğru olduğundan emin olmak olası sorunları gidermeye yönelik gerekli bir ilk adımdır.  Yay önyükleme çalıştırıcı uç noktasını, ortam değişkenlerinizi gözden geçirmek için kullanabilirsiniz.  

> [!WARNING]
> Bu yordam, test uç noktanızı kullanarak ortam değişkenlerinizi kullanıma sunar.  Test uç noktanız herkese açık ise veya uygulamanıza bir etki alanı adı atadıysanız devam edin.

1. `https://<your application test endpoint>/actuator/health` öğesine gidin.  
    - Aşağıdakine benzer bir yanıt `{"status":"UP"}` , uç noktanın etkinleştirildiğini gösterir.
    - Yanıt negatifse, *POM.xml* dosyanıza aşağıdaki bağımlılığı ekleyin:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Spring Boot çalıştırıcı uç noktası etkinken Azure portal gidin ve uygulamanızın yapılandırma sayfasına bakın.  Adı ve değeri olan bir ortam değişkeni ekleyin `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` `*` . 

1. Uygulamanızı yeniden başlatın.

1. Adresine gidin `https://<your application test endpoint>/actuator/env` ve yanıtı inceleyin.  Şu şekilde görünmelidir:

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

Adlı alt düğümü arayın `systemEnvironment` .  Bu düğüm, uygulamanızın ortam değişkenlerini içerir.

> [!IMPORTANT]
> Uygulamanızı herkese açık hale getirmeden önce ortam değişkenlerinizin görünürlüğünü ters çevirmeyi unutmayın.  Azure portal gidin, uygulamanızın yapılandırma sayfasına bakın ve bu ortam değişkenini silin:  `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` .

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Uygulamamın ölçütlerini veya günlüklerini bulamıyorum

Uygulama durumlarının _çalıştığından_ ve _en güncel_ olduğundan emin olmak için **uygulama yönetimi** 'ne gidin.

Uygulama paketinizdeki _JMX_ 'in etkinleştirilip etkinleştirilmediğini denetleyin. Bu özellik yapılandırma özelliği ile etkinleştirilebilir `spring.jmx.enabled=true` .  

`spring-boot-actuator`Bağımlılığın uygulama paketinizdeki etkinleştirilip etkinleştirilmediğini ve başarıyla önyükleme yapıp görmediğini denetleyin.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Uygulama günlüklerinizin bir depolama hesabına arşivlenmesi, ancak Azure Log Analytics 'e gönderilmemesi durumunda, [çalışma alanınızı doğru şekilde ayarlayıp ayarlamadığını](../azure-monitor/logs/quick-create-workspace.md)kontrol edin. Ücretsiz bir Azure Log Analytics katmanı kullanıyorsanız, [ücretsiz katmanın bir hizmet düzeyi sözleşmesi (SLA) sağlamayacağını](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Spring Cloud 'da sorunları kendi kendine tanılama ve çözme](spring-cloud-howto-self-diagnose-solve.md)
