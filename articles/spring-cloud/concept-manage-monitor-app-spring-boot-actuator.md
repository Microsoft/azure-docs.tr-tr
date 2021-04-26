---
title: Azure Spring Boot Actuator ile uygulama yönetme ve izleme
description: Spring Boot çalıştırıcı ile uygulamayı yönetme ve izleme hakkında bilgi edinin.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 93fd286aa76a0409a515abbf8c9dabd88a9a65c4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878747"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>Azure Spring Boot Actuator ile uygulama yönetme ve izleme

**Bu makale şu şekilde geçerlidir:** ✔️ Java

Uygulamanıza yeni ikili dağıttıktan sonra, işlevleri denetlemek ve çalışan uygulamanız hakkındaki bilgileri görmek isteyebilirsiniz. Bu makalede, Azure Spring Cloud tarafından sunulan bir test uç noktasından API 'ye nasıl erişebileceğiniz ve uygulamanız için üretime yönelik kullanıma sunma özelliklerinin nasıl kullanılacağı açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar
Bu makalede, Azure yay bulut hizmetinde başarıyla dağıtılabilecek ve önyüklenebilecek bir Spring Boot 2. x uygulamasına sahip olduğunuz varsayılır.  Bkz [. hızlı başlangıç: Azure Portal kullanarak mevcut bir Azure Spring Cloud uygulamasını başlatma](spring-cloud-quickstart.md)

## <a name="verify-app-through-test-endpoint"></a>Test uç noktası aracılığıyla uygulamayı doğrulama
1. **Uygulama panosu** ' na gidin ve uygulamaya genel bakış sayfasını girmek için uygulamanıza tıklayın.

1. **Genel bakış** bölmesinde **sınama uç noktası**' nı görmeniz gerekir.  Bu uç noktaya komut satırı veya tarayıcıdan erişin ve API yanıtını gözlemleyin.

1. Gelecek bölümde kullanılacak olan **sınama uç noktası** URI 'sini unutmayın.

>[!TIP]
> * Uygulama bir ön uç sayfası döndürürse ve göreli yol üzerinden diğer dosyalara başvuruyorsa, test uç noktanızın eğik çizgiyle (/) bitdiğini doğrulayın. Bu, CSS dosyasının doğru şekilde yüklendiğinden emin olur.
> * API 'nizi bir gözacının üzerinde görüntülediğinizde ve tarayıcınız sayfayı görüntülemek için oturum açma kimlik bilgilerini girmenizi gerektiriyorsa, test uç noktanızın kodunu çözmek için [URL kodunu çöz](https://www.urldecoder.org/) kullanın. URL kod çözme "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/ \<app-name> / \<deployment-name> " biçiminde bir URL döndürür.  Uç noktanıza erişmek için bu formu kullanın.

## <a name="add-actuator-dependency"></a>Çalıştırıcı bağımlılığı Ekle

Yük projesini Maven tabanlı bir projeye eklemek için ' Starter ' bağımlılığını ekleyin:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

Yeni ikiliyi derleyin ve uygulamanıza dağıtın.

## <a name="enable-production-ready-features"></a>Üretime hazırlamış özellikleri etkinleştir
Çalıştırıcı uç noktaları, uygulamanızı izlemenizi ve bunlarla etkileşime geçmesini sağlar. Varsayılan olarak, Spring Boot uygulaması, `health` `info` rastgele uygulama bilgilerini ve sistem durumu bilgilerini göstermek için ve bitiş noktalarını gösterir.

Yapılandırma ve yapılandırılabilir ortamı gözlemlemek için, `env` ve `configgrops` uç noktaların de etkinleştirilmesi gerekir.

1. Uygulamaya **genel bakış** bölmesine gidin, ayar menüsünde **yapılandırma** ' ya tıklayın, **ortam değişkenleri** yapılandırması sayfasına gidin.
1. Aşağıdaki özellikleri "anahtar: değer" biçiminde ekleyin. Bu ortam Spring çalıştırıcı uç noktasını "env", "Health", "Info" olarak açar.

   ```
   management.endpoints.web.exposure.include: env,health,info
   ```
1. **Kaydet** düğmesine tıklayın, uygulamanız otomatik olarak yeniden başlatılır ve yeni ortam değişkenlerini yükler.

Artık uygulamaya genel bakış bölmesine dönüp sağlama durumu "başarılı" olarak değişene kadar bekleyebilirsiniz.  Birden fazla çalışan örnek olacaktır.

> [!Note] 
> Uygulamayı herkese açık duruma getirdikten sonra, bu çalıştırıcı uç noktaları da genel kullanıma sunulur. Ortam değişkenlerini silerek tüm uç noktaları gizleyebilir `management.endpoints.web.exposure.include` ve `management.endpoints.web.exposure.exclude=*`

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>Uygulama bilgilerini görüntülemek için çalıştırıcı uç noktasını görüntüleme
1. Şimdi, `"<test-endpoint>/actuator/"` Spring Boot çalıştırıcı tarafından sunulan tüm uç noktaları görmek için URL 'ye erişebilirsiniz.
1. Erişim URL 'si `"<test-endpoint>/actuator/env"` , uygulama tarafından kullanılan etkin profilleri ve tüm ortam değişkenlerini görebilir.
1. Belirli bir ortamda arama yapmak istiyorsanız, URL 'yi  `"<test-endpoint>/actuator/env/{toMatch}"` görüntülemek için ona erişebilirsiniz.

Yerleşik tüm uç noktaları görüntülemek için bkz. [uç noktaları gösterme](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure yay bulutu için ölçümleri anlama](spring-cloud-concept-metrics.md)
* [Azure Spring Cloud’da uygulama durumunu anlama](spring-cloud-concept-app-status.md)

