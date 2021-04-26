---
title: Azure AD B2C kullanan dış işlemlerdeki dayanıklı arabirimler | Microsoft Docs
description: Dış işlemlerle dayanıklı arabirimler oluşturma yöntemleri
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ef522f5b048db0ef120625d9e894c8e14c070e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724416"
---
# <a name="resilient-interfaces-with-external-processes"></a>Dış işlemlerle esnek arabirimler

Bu makalede, Kullanıcı yolculuğunda yeniden yapılan API 'Lerin nasıl planlanacağı ve uygulanacağı hakkında rehberlik sunuyoruz ve uygulamanızı API hatalarıyla daha dayanıklı hale getirebilirsiniz.

![Görüntü, dış işlem bileşenlerine sahip arabirimleri gösterir](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>API 'lerin doğru yerleştirmesini sağlayın

Kimlik deneyimi çerçevesi (ıEF) ilkeleri, bir [RESTAN API teknik profili](../../active-directory-b2c/restful-technical-profile.md)kullanarak bir dış sistemi çağırmanızı sağlar. Dış sistemler ıEF çalışma zamanı ortamı tarafından denetlenmez ve olası bir hata noktasıdır.

### <a name="how-to-manage-external-systems-using-apis"></a>API 'Leri kullanarak dış sistemleri yönetme

- Belirli verilere erişmek için bir arabirim çağrılırken, verilerin kimlik doğrulama kararını yapıp yapamayacağını denetleyin. Bilgilerin uygulamanın temel işlevselliği için önemli olup olmadığını değerlendirin. Örneğin, bir e-ticaret vs. Yönetim gibi ikincil bir işlevsellik. Kimlik doğrulaması için gerekli değildir ve yalnızca ikincil senaryolar için gerekliyse, çağrıyı uygulama mantığına taşımayı göz önünde bulundurun.

- Kimlik doğrulaması için gerekli olan veriler nispeten statikse ve küçükse ve dizinden externalized başka bir iş nedenine sahip değilse, bunu dizinde bulundurmaktan düşünün.

- Mümkün olduğunda önceden kimliği doğrulanmış yoldan API çağrılarını kaldırın. Bu durumda, API 'lerinizin önünde hizmet reddi (DoS) ve dağıtılmış hizmet reddi (DDoS) saldırıları için katı korumalar yerleştirmeniz gerekir. Saldırganlar, oturum açma sayfasını yükleyebilir ve API 'nizi DoS saldırılarına ve uygulamanızı Cripple. Örneğin, oturum açma ortamınızda CAPTCHA 'yı kullanarak kaydolma akışı yardımcı olabilir.

- [Yerleşik kaydolma Kullanıcı akışının API bağlayıcılarını,](../../active-directory-b2c/api-connectors-overview.md) bir kimlik sağlayıcısıyla oturum açtıktan veya Kullanıcı oluşturmadan önce Web API 'leriyle tümleştirilebilen her yerde kullanın. Kullanıcı akışları zaten kapsamlı olarak test edildiğinden, büyük olasılıkla Kullanıcı akış düzeyinde işlevsel, performans veya ölçek testi gerçekleştirmeniz gerekmez. Uygulamalarınızın işlevselliği, performansı ve ölçeği için yine de test etmeniz gerekir.

- Azure AD Restsize API [Teknik profilleri](../../active-directory-b2c/restful-technical-profile.md) , herhangi bir önbelleğe alma davranışı sağlamaz. Bunun yerine, yeniden deneme mantığı ve ilke içinde yerleşik bir zaman aşımı uygular.

- Veri yazma ihtiyacı olan API 'Ler için bir görevi bir arka plan çalışanı tarafından yürütülen bu görevlere sahip olacak şekilde sıraya koyun. [Azure Kuyrukları](../../storage/queues/storage-queues-introduction.md) gibi hizmetler kullanılabilir. Bu, API 'nin ilke yürütme performansını verimli bir şekilde artırarak dönmesini sağlar.  

## <a name="api-error-handling"></a>API hata işleme

API 'Ler Azure AD B2C sistem dışında yaşar, teknik profilde doğru hata işlemenin olması gerekir. Son kullanıcının düzgün şekilde haberdar olduğundan ve uygulamanın hata ile sorunsuz bir şekilde ilgilenebildiğinden emin olun.

### <a name="how-to-gracefully-handle-api-errors"></a>API hatalarını dikkatlice işleme

- Bir API çeşitli nedenlerle başarısız olabilir, uygulamanızı bu hatalara karşı dayanıklı hale getirir. API isteği tamamlayamadıysanız [BIR http 4XX hata Iletisi döndürür](../../active-directory-b2c/restful-technical-profile.md#returning-validation-error-message) . Azure AD B2C ilkesinde, API 'nin kullanım dışı olduğunu düzgün bir şekilde işlemeyi deneyin, belki de daha az bir deneyim işleyebilir.

- [Geçici hataları sorunsuz](../../active-directory-b2c/restful-technical-profile.md#error-handling)bir şekilde işleyin. Yenilenmiş API profili, çeşitli [devre kesicileri](/azure/architecture/patterns/circuit-breaker)için hata mesajlarını yapılandırmanıza olanak tanır.

- Sürekli tümleştirme/sürekli teslim (CICD) proaktif olarak izleme ve kullanma, [Teknik profil altyapısı](../../active-directory-b2c/restful-technical-profile.md)tarafından kullanılan parolalar ve SERTIFIKALAR gibi API erişimi kimlik bilgilerini döndürün.

## <a name="api-management---best-practices"></a>API yönetimi-en iyi uygulamalar

REST API 'Leri dağıtırken ve yeniden elde edilen teknik profili yapılandırdığınızda, önerilen en iyi uygulamalardan sonra, sık karşılaşılan hataları ve şeyleri fazla gözlememe yapmanıza yardımcı olur.

### <a name="how-to-manage-apis"></a>API 'Leri yönetme

- API Management (APıM), API 'lerinizi yayımlar, yönetir ve analiz eder. APıM, arka uç hizmetlerine ve mikro hizmetlere güvenli erişim sağlamak için kimlik doğrulamasını da işler. API dağıtımlarını, önbelleğe almayı ve yük dengelemeyi ölçeklendirmek için bir API ağ geçidi kullanın.

- Öneri, her API için birden çok kez çağrı yapmak ve [bir Azure APIM API 'sini güvenli hale](../../active-directory-b2c/secure-api-management.md?tabs=app-reg-ga)getirmek yerine Kullanıcı yolculuğunun başlangıcında doğru belirteci almak içindir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2C geliştiricileri için esnekliği kaynakları](resilience-b2c.md)
  - [Dayanıklı son kullanıcı deneyimi](resilient-end-user-experience.md)
  - [Geliştirici en iyi uygulamaları esnekliği](resilience-b2c-developer-best-practices.md)
  - [İzleme ve analiz aracılığıyla esnekliği](resilience-with-monitoring-alerting.md)
- [Kimlik doğrulama altyapınızda esnekliği oluşturma](resilience-in-infrastructure.md)
- [Uygulamalarınızda kimlik doğrulama ve yetkilendirme esnekliği artırın](resilience-app-development-overview.md)