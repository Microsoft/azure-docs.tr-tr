---
title: Hızlı başlangıç-Azure Spring Cloud örnek uygulamaya giriş
description: Bu hızlı başlangıç serisinde Azure Spring Cloud 'a dağıtım için kullanılan örnek uygulamayı açıklar.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: dd36bb18e84ea299195b77286887a3b279f81469
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879372"
---
# <a name="introduction-to-the-sample-app"></a>Örnek uygulamaya giriş

::: zone pivot="programming-language-csharp"
Bu hızlı başlangıçların serisi, iki mikro hizmetten oluşan örnek bir uygulama kullanır ve bu da Azure Spring Cloud Service 'e .NET Core Steeltoe uygulamasının nasıl dağıtılacağını göstermektedir. Hizmet bulma, yapılandırma sunucusu, Günlükler, ölçümler ve dağıtılmış izleme gibi Azure Spring Cloud özelliklerini kullanacaksınız.

## <a name="functional-services"></a>İşlevsel hizmetler

Örnek uygulama iki mikro hizmetten oluşur:

* `planet-weather-provider`Hizmet, dünya adını belirten bir HTTP isteğine yanıt olarak hava durumu metni döndürür. Örneğin, dünya CII için "çok sıcak" döndürebilir. Yapılandırma sunucusundan Hava durumu verilerini alır. Yapılandırma sunucusu, git deposundaki bir YAML dosyasından Hava durumu verilerini alır, örneğin:

  ```yaml
  MercuryWeather: very warm
  VenusWeather: quite unpleasant
  MarsWeather: very cool
  SaturnWeather: a little bit sandy
  ```

* `solar-system-weather`Hizmet, HTTP isteğine yanıt olarak dört gezegene yönelik verileri döndürür. Uygulamasına dört HTTP isteği getirerek verileri alır `planet-weather-provider` . Çağırmak için Eureka sunucu bulma hizmetini kullanır `planet-weather-provider` . JSON döndürür, örneğin:

  ```json
  [{
      "Key": "Mercury",
      "Value": "very warm"
  }, {
      "Key": "Venus",
      "Value": "quite unpleasant"
  }, {
      "Key": "Mars",
      "Value": "very cool"
  }, {
      "Key": "Saturn",
      "Value": "a little bit sandy"
  }]
  ```

Aşağıdaki diyagramda örnek uygulama mimarisi gösterilmektedir:

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="Örnek uygulama diyagramı":::

## <a name="code-repository"></a>Kod deposu

Örnek uygulama, GitHub 'daki Azure-Samples/Azure-Spring-Cloud-Samples deposunun [steeltoe-Sample](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) klasöründe bulunur.

Aşağıdaki hızlı başlangıçlarda bulunan yönergeler, kaynak koda gerektiği şekilde başvurur.

::: zone-end

::: zone pivot="programming-language-java"
Bu hızlı başlangıçta, Azure Spring Cloud Service 'e bir uygulama dağıtmayı göstermek için Plımetrik adlı bir kişisel finans örneği kullanırız. Plımetrimetrik, mikro hizmet mimarisi modelini gösterir ve hizmet dökümünü vurgular. Hizmet bulma, yapılandırma sunucusu, Günlükler, ölçümler ve dağıtılmış izleme gibi güçlü Azure Spring Cloud Capabilities ile Azure 'a nasıl dağıtıldığını öğreneceksiniz.

Azure yay bulutu dağıtım örneklerini izlemek için, yalnızca kaynak kodun gerekli olduğu şekilde sağlandığı konuma ihtiyacınız vardır.

## <a name="functional-services"></a>İşlevsel hizmetler

Pıof ölçümleri üç çekirdekli mikro hizmete sahiptir. Bunların hepsi, iş etki alanlarına göre düzenlenmiş bağımsız olarak dağıtılabilir uygulamalardır.

* **Hesap hizmeti (dağıtılacak)**: genel kullanıcı giriş mantığını ve doğrulamayı içerir: giriş/gider öğeleri, tasarrufları ve hesap ayarları.
* **İstatistik hizmeti (Bu hızlı başlangıçta kullanılmıyor)**: büyük istatistik parametrelerinde hesaplamalar yapar ve her hesap için zaman serisini yakalar. DataPoint temel para birimine ve zaman dilimine göre normalleştirilmiş değerler içerir. Bu veriler nakit akışı Dynamics 'in hesap ömrünü izlemek için kullanılır.
* **Bildirim hizmeti (Bu hızlı başlangıçta kullanılmıyor)**: kullanıcıya, hatırlat ve yedekleme sıklığı gibi iletişim bilgilerini ve bildirim ayarlarını depolar. Zamanlanan çalışan, diğer hizmetlerden gerekli bilgileri toplar ve abone olunan müşterilere e-posta iletileri gönderir.

## <a name="infrastructure-services"></a>Altyapı Hizmetleri

Dağıtılmış sistemlerde çekirdek hizmetlerinin çalışmasını sağlamaya yardımcı olan birkaç ortak desen vardır. Azure yay bulutu, bu desenleri uygulamak için Spring Boot uygulamaları davranışını geliştiren güçlü araçlar sağlar: 

* **Yapılandırma hizmeti (Azure Spring Cloud tarafından barındırılan)**: Azure Spring Cloud config, dağıtılmış sistemler için yatay olarak ölçeklenebilir bir merkezi yapılandırma hizmetidir. Bu, şu anda yerel depolama, git ve alt sürümü destekleyen bir takılabilir depo kullanır.
* **Hizmet bulma (Azure yay bulutu tarafından barındırılan)**: otomatik ölçeklendirme, arızalar ve yükseltmeler nedeniyle dinamik olarak atanan adreslere sahip olabilecek hizmet örnekleri için ağ konumlarının otomatik olarak algılanmasını sağlar.
* **Kimlik doğrulama hizmeti (dağıtılacak)** Yetkilendirme sorumlulukları, arka uç kaynak hizmetleri için OAuth2 belirteçleri veren ayrı bir sunucuya tamamen ayıklanır. Auth Server, kullanıcı yetkilendirmesi ve çevre içinde makineden makineye iletişim güvenliğini sağlar.
* **API Gateway (dağıtılacak)**: üç çekirdekli hizmet, istemciye BIR dış API sunar. Gerçek dünyada sistemlerde, işlevlerin sayısı sistem karmaşıklığıyla çok daha hızlı büyüyebilir. Yüzlerce hizmet, karmaşık bir Web sayfasının işlenmesiyle ilgili olabilir. API ağ geçidi, istekleri işlemek ve bunları uygun arka uç hizmetine yönlendirmek veya birden fazla arka uç hizmetini çağırmak için kullanılan ve sonuçları toplayarak, sisteme tek bir giriş noktasıdır. 

## <a name="sample-usage-of-piggymetrics"></a>Pılem ölçümlerinin örnek kullanımı

Tam uygulama ayrıntıları için bkz. [Pıof ölçümleri](https://github.com/Azure-Samples/piggymetrics). Örnekler, kaynak koda gereken şekilde başvurur.
::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Spring Cloud Instance sağlama](spring-cloud-quickstart-provision-service-instance.md)
