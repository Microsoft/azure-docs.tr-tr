---
title: Azure ön kapısının Standart/Premium yapılandırma sorunlarını giderme
description: Bu öğreticide, Azure ön kapısının Standart/Premium örneğiniz için yüz yüze bileceğiniz bazı yaygın sorunların nasıl giderileceği hakkında bilgi edineceksiniz.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 4690a513494d794377ee0c2e8cfb101e8fd66a0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100571"
---
# <a name="troubleshooting-common-routing-problems-with-azure-front-door-standardpremium"></a>Azure ön kapı standardı/Premium ile yaygın yönlendirme sorunlarını giderme

Bu makalede, Azure ön kapısının yapılandırmasına yönelik olabilecek yaygın yönlendirme sorunlarını giderme işlemleri açıklanır.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>birkaç saniye sonra Azure ön kapılarından 503 yanıt

### <a name="symptom"></a>Belirti

* Azure ön kapısına geçmeden arka ucunuza gönderilen düzenli istekler başarılı oluyor. Azure ön kapısının sonuçları 503 hata yanıtları ile sonuçlanır.
* Azure ön kapısından hata, genellikle yaklaşık 30 saniye içinde görüntülenir.

### <a name="cause"></a>Nedeni

Bu sorunun nedeni iki işlemlerden biri olabilir:
 
* Kaynak, Azure ön kapıdan gelen isteği almak için yapılandırılan zaman aşımından (varsayılan değer 30 saniye) daha uzun sürüyor.
* Azure ön kapısının isteğine yanıt göndermek için gereken süre, zaman aşımı değerinden daha uzun sürüyor. 

### <a name="troubleshooting-steps"></a>Sorun giderme adımları

* İsteği doğrudan arka uca gönderin (Azure ön kapısına geçmeden). Arka ucunuzun ne kadar süreyle yanıt verme için ne kadar sürdüğünü görün.
* Azure ön kapısı aracılığıyla isteği gönderin ve 503 yanıt aldığınızı öğrenin. Aksi takdirde, sorun bir zaman aşımı sorunu olmayabilir. Desteğe başvurun.
* Azure ön kapısının üzerinden geçmesi 503 hata yanıt koduna neden olursa, `sendReceiveTimeout` alanı Azure ön kapısına göre yapılandırın. Varsayılan zaman aşımını 4 dakikaya kadar genişletebilirsiniz (240 saniye). Ayar, `Endpoint Setting` ve olarak adlandırılır `Origin response timeout` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Özel etki alanına gönderilen istekler 400 durum kodu döndürüyor

### <a name="symptom"></a>Belirti

* Bir Azure ön kapısı örneği oluşturdunuz, ancak etki alanına veya ön uç konağına yönelik bir istek HTTP 400 durum kodu döndürüyor.
* Özel bir etki alanı için yapılandırdığınız ön uç konağına bir DNS eşlemesi oluşturdunuz. Ancak, özel etki alanı ana bilgisayar adına bir istek gönderdiğinizde bir HTTP 400 durum kodu döndürülür. Yapılandırdığınız arka uca yönlendirilmez.

### <a name="cause"></a>Nedeni

Bu sorun, ön uç ana bilgisayarı olarak eklenen özel etki alanı için bir yönlendirme kuralı yapılandırmadıysanız oluşur. Bu ön uç ana bilgisayarı için bir yönlendirme kuralının açık olarak eklenmesi gerekir. Bu, Azure ön kapısı alt etki alanı (*. azurefd.net) altında ön uç ana bilgisayar için bir yönlendirme kuralı zaten yapılandırılmış olsa bile geçerlidir.

### <a name="troubleshooting-steps"></a>Sorun giderme adımları

Trafiği seçili kaynak grubuna yönlendirmek için özel etki alanı için bir yönlendirme kuralı ekleyin.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure ön kapısı HTTP 'yi HTTPS 'ye yönlendirmez

### <a name="symptom"></a>Belirti

Azure ön kapısının HTTP 'yi HTTPS 'ye yönlendiren bir yönlendirme kuralı vardır ancak etki alanına erişmek için protokol olarak HTTP devam eder.

### <a name="cause"></a>Nedeni

Bu davranış, yönlendirme kurallarını Azure ön kapısının doğru şekilde yapılandırmadıysanız oluşabilir. Temel olarak, geçerli yapılandırmanız özel değildir ve çakışan kurallara sahip olabilir.

### <a name="troubleshooting-steps"></a>Sorun giderme adımları


## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>Ön uç ana bilgisayar adına yapılan istek bir 411 durum kodu döndürüyor

### <a name="symptom"></a>Belirti

Bir Azure ön kapısının Standart/Premium örneği oluşturdunuz ve ön uç ana bilgisayarı, içinde en az bir kaynağa sahip bir kaynak grubu ve ön uç konağını kaynak grubuna bağlayan bir yönlendirme kuralı oluşturdunuz. Bir HTTP 411 durum kodu döndürüldüğü için bir istek yapılandırılmış ön uç konağına geçtiğinde içeriğiniz kullanılamıyor gibi görünüyor.

Bu isteklere verilen yanıtlar, açıklayıcı bir ifade içeren yanıt gövdesinde bir HTML hata sayfası da içerebilir. Örneğin: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Nedeni

Bu belirtinin birkaç olası nedeni vardır. Genel neden HTTP isteğiniz tam olarak RFC uyumlu değildir. 

Uyumsuzluk örneği, `POST` bir `Content-Length` veya `Transfer-Encoding` üst bilgisi (örneğin, kullanılarak) olmadan gönderilen bir istedir `curl -X POST https://example-front-door.domain.com` . Bu istek, [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2)' de ayarlanan gereksinimleri karşılamıyor. Azure ön kapısı, bunu bir HTTP 411 yanıtı ile engeller.

Bu davranış, Azure ön kapısının Web uygulaması güvenlik duvarı (WAF) işlevlerinden farklıdır. Şu anda bu davranışı devre dışı bırakma yöntemi yoktur. WAF işlevselliği kullanımda olmasa bile tüm HTTP isteklerinin gereksinimleri karşılaması gerekir.

### <a name="troubleshooting-steps"></a>Sorun giderme adımları

- İsteklerinizin, gerekli RFC 'lerde ayarlanan gereksinimlerle uyumlu olduğunu doğrulayın.

- İsteğinize yanıt olarak döndürülen herhangi bir HTML ileti gövdesini göz atın. Bir ileti gövdesi genellikle isteğinizin uyumsuz olarak *nasıl* uyumlu olduğunu açıklar.

## <a name="next-steps"></a>Sonraki adımlar

[Ön kapı Standart/Premium oluşturmayı](create-front-door-portal.md)öğrenin.
