---
title: Azure ön kapılı Web uygulaması güvenlik duvarı için ilke ayarları
description: Web uygulaması güvenlik duvarını (WAF) öğrenin.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f763ad9afd2238ebe63bf878882011eb3745df5c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517117"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Azure ön kapıda Web uygulaması güvenlik duvarı için ilke ayarları

Web uygulaması güvenlik duvarı (WAF) ilkesi, Web uygulamalarınıza erişimi bir dizi özel ve yönetilen kuralla denetlemenize olanak tanır. WAF ilkesi adı benzersiz olmalıdır. Mevcut bir adı kullanmayı denerseniz, doğrulama hatası alırsınız. Bu makalede açıklandığı gibi, bu ilke için belirtilen tüm kurallara uygulanan birden çok ilke düzeyi ayarı vardır.

## <a name="waf-state"></a>WAF durumu

Ön kapı için bir WAF ilkesi aşağıdaki iki durumdan birinde olabilir:
- **Etkin:** Bir ilke etkinleştirildiğinde WAF, gelen istekleri etkin bir şekilde inceliyor ve kural tanımlarına göre ilgili eylemleri alır
- **Devre dışı:** -bir ilke devre dışı bırakıldığında WAF incelemesi duraklatılır. Gelen istekler WAF 'yi atlar ve ön kapıya yönlendirme temelinde arka uca gönderilir.

## <a name="waf-mode"></a>WAF modu

WAF ilkesi aşağıdaki iki modda çalışacak şekilde yapılandırılabilir:

- **Algılama modu** Algılama modunda çalıştırıldığında WAF, Monitor dışında bir eylem gerçekleştirmez ve isteği ve eşleşen WAF kuralını WAF günlüklerine kaydeder. Ön kapı için günlük tanılamayı açın (portal kullanırken, Azure portal) **Tanılama** bölümüne giderek bu elde edilebilir.

- **Önleme modu** Önleme modunda çalışacak şekilde yapılandırıldığında, bir istek bir kuralla eşleşiyorsa WAF belirtilen eylemi alır. Tüm eşleşen istekler de WAF günlüklerine kaydedilir.

## <a name="waf-response-for-blocked-requests"></a>Engellenen istekler için WAF yanıtı

Varsayılan olarak, WAF eşleşen bir kural nedeniyle isteği engellediğinde, **istek engellendi** iletisi olan 403 durum kodunu döndürür. Günlüğe kaydetme için bir başvuru dizesi de döndürülür.

Bir istek WAF tarafından engellendiğinde özel bir yanıt durum kodu ve yanıt iletisi tanımlayabilirsiniz. Aşağıdaki özel durum kodları desteklenir:

- 200 TAMAM
- 403 Yasak
- 405 yöntemine izin verilmiyor
- 406 kabul edilemez
- 429 çok fazla istek

Özel yanıt durum kodu ve yanıt iletisi bir ilke düzeyi ayarıdır. Yapılandırıldıktan sonra, tüm Engellenen istekler aynı özel yanıt durumunu ve yanıt iletisini alır.

## <a name="uri-for-redirect-action"></a>Yeniden yönlendirme eylemi URI 'SI

Bir WAF ilkesinde bulunan kuralların herhangi biri için **yeniden yönlendirme** eylemi seçildiyse, istekleri yeniden yönlendirmek IÇIN bir URI tanımlamanız gerekir. Bu yeniden yönlendirme URI 'sinin geçerli bir HTTP (S) sitesi olması ve yapılandırıldıktan sonra, "REDIRECT" eylemine sahip kurallarla eşleşen tüm istekler belirtilen siteye yönlendirilir.


## <a name="next-steps"></a>Sonraki adımlar
- WAF [özel yanıtlarını](waf-front-door-configure-custom-response-code.md) nasıl tanımlayacağınızı öğrenin
