---
title: Azure ön kapı yapılandırma sorunlarını giderme
description: Bu öğreticide, Azure ön kapılarınız için yüz yüze bileceğiniz bazı yaygın sorunların nasıl giderileceğini öğreneceksiniz.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 15cdcefe628a392704e650b560243e2f6a134ec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629997"
---
# <a name="troubleshooting-common-routing-problems"></a>Yaygın yönlendirme sorunlarını giderme

Bu makalede, Azure ön kapısının yapılandırmasına yönelik olabilecek yaygın yönlendirme sorunlarını giderme işlemleri açıklanır.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>birkaç saniye sonra Azure ön kapılarından 503 yanıt

### <a name="symptom"></a>Belirti

* Azure ön kapısına geçmeden arka ucunuza gönderilen düzenli istekler başarılı oluyor. Azure ön kapısının sonuçları 503 hata yanıtları ile sonuçlanır.
* Azure ön kapısından hata, genellikle yaklaşık 30 saniye içinde görüntülenir.

### <a name="cause"></a>Nedeni

Bu sorunun nedeni iki işlemlerden biri olabilir:
 
* Azure ön kapılarından gelen isteği almak için arka ucunuz yapılandırılan zaman aşımından (varsayılan 30 saniye) daha uzun sürüyor.
* Azure ön kapısının isteğine yanıt göndermek için gereken süre, zaman aşımı değerinden daha uzun sürüyor. 

### <a name="troubleshooting-steps"></a>Sorun giderme adımları

* İsteği doğrudan arka uca gönderin (Azure ön kapısına geçmeden). Arka ucunuzun ne kadar süreyle yanıt verme için ne kadar sürdüğünü görün.
* Azure ön kapısı aracılığıyla isteği gönderin ve 503 yanıt aldığınızı öğrenin. Aksi takdirde, sorun bir zaman aşımı sorunu olmayabilir. Desteğe başvurun.
* Azure ön kapısının üzerinden geçmesi 503 hata yanıt koduna neden olursa, `sendReceiveTimeout` alanı Azure ön kapısına göre yapılandırın. Varsayılan zaman aşımını 4 dakikaya kadar genişletebilirsiniz (240 saniye). Ayar, `backendPoolSettings` ve olarak adlandırılır `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Özel etki alanına gönderilen istekler 400 durum kodu döndürüyor

### <a name="symptom"></a>Belirti

* Bir Azure ön kapısı örneği oluşturdunuz, ancak etki alanına veya ön uç konağına yönelik bir istek HTTP 400 durum kodu döndürüyor.
* Özel bir etki alanı için yapılandırdığınız ön uç konağına bir DNS eşlemesi oluşturdunuz. Ancak, özel etki alanı ana bilgisayar adına bir istek gönderdiğinizde bir HTTP 400 durum kodu döndürülür. Yapılandırdığınız arka uca yönlendirilmez.

### <a name="cause"></a>Nedeni

Bu sorun, ön uç ana bilgisayarı olarak eklenen özel etki alanı için bir yönlendirme kuralı yapılandırmadıysanız oluşur. Bu ön uç ana bilgisayarı için bir yönlendirme kuralının açık olarak eklenmesi gerekir. Bu, Azure ön kapısı alt etki alanı (*. azurefd.net) altında ön uç ana bilgisayar için bir yönlendirme kuralı zaten yapılandırılmış olsa bile geçerlidir.

### <a name="troubleshooting-steps"></a>Sorun giderme adımları

Trafiği seçilen arka uç havuzuna yönlendirmek için özel etki alanı için bir yönlendirme kuralı ekleyin.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure ön kapısı HTTP 'yi HTTPS 'ye yönlendirmez

### <a name="symptom"></a>Belirti

Azure ön kapısının HTTP 'yi HTTPS 'ye yönlendiren bir yönlendirme kuralı vardır ancak etki alanına erişmek için protokol olarak HTTP devam eder.

### <a name="cause"></a>Nedeni

Bu davranış, yönlendirme kurallarını Azure ön kapısının doğru şekilde yapılandırmadıysanız oluşabilir. Temel olarak, geçerli yapılandırmanız özel değildir ve çakışan kurallara sahip olabilir.

### <a name="troubleshooting-steps"></a>Sorun giderme adımları

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>Ön uç ana bilgisayar adına yapılan istek bir 404 durum kodu döndürüyor

### <a name="symptom"></a>Belirti

Ön uç konağını, içinde en az bir arka uca sahip bir arka uç havuzunu ve ön uç konağını arka uç havuzuna bağlayan bir yönlendirme kuralını yapılandırarak bir Azure ön kapı örneği oluşturdunuz. Yapılandırılmış ön uç konağına bir istek yaptığınızda içeriğiniz kullanılamaz. Sonuç olarak, istek bir HTTP 404 durum kodu döndürür.

### <a name="cause"></a>Nedeni

Bu belirtinin birkaç olası nedeni vardır:

* Arka uç, herkese açık bir arka uç değildir ve Azure ön kapısına görünmez.
* Arka uç yanlış yapılandırılmış ve Azure ön kapısının yanlış istek göndermesini sağlar. Diğer bir deyişle, arka ucunuz yalnızca HTTP kabul eder ve HTTPS 'ye izin vermezsiniz. Azure ön kapısı, HTTPS isteklerini iletmeyi deniyor.
* Arka uç, istekle birlikte arka uca iletilen konak üstbilgisini reddediyor.
* Arka uç yapılandırması henüz tam olarak dağıtılmadı.

### <a name="troubleshooting-steps"></a>Sorun giderme adımları

* Dağıtım süresini kontrol edin:
   * Yapılandırmanın dağıtılması için en az 10 dakika beklediğinizden emin olun.

* Arka uç ayarlarını denetleyin:
    * İsteğin yönlendirilmesi gereken arka uç havuzuna gidin. (Yönlendirme kuralını nasıl yapılandırdığınıza bağlıdır.) Arka uç konak türünün ve arka uç ana bilgisayar adının doğru olduğundan emin olun. Arka uç özel bir ana bilgisayar ise doğru yazdığınızdan emin olun. 

    * HTTP ve HTTPS bağlantı noktalarınızı denetleyin. Çoğu durumda, 80 ve 443 (sırasıyla) doğrudur ve hiçbir değişiklik yapılması gerekmez. Ancak, arka ucunuzun bu şekilde yapılandırılmadığı ve farklı bir bağlantı noktasında dinleme yaptığı bir şansınız vardır.

    * Ön uç konağın yönlendirilmesi gereken arka uç ana bilgisayar üst bilgisini denetleyin. Çoğu durumda bu üst bilgi, arka uç ana bilgisayar adıyla aynı olmalıdır. Ancak, arka uç farklı bir şeyi bekliyorsa, yanlış bir değer çeşitli HTTP 4xx durum kodlarına neden olabilir. Arka ucunuzun IP adresini girerseniz, arka uç ana bilgisayar üst bilgisini arka ucun ana bilgisayar adına ayarlamanız gerekebilir.

* Yönlendirme kuralı ayarlarını denetleyin:
    * Söz konusu ön uç ana bilgisayar adından bir arka uç havuzuna yönlendirilmesi gereken yönlendirme kuralına gidin. İstek iletildiğinde kabul edilen protokollerin doğru yapılandırıldığından emin olun. **Kabul edilen protokoller** alanı, Azure ön kapısının kabul etmesi gereken istekleri belirler. İletme protokolü, Azure ön kapısının isteği arka uca iletmek için kullanması gereken Protokolü belirler.
      
      Örnek olarak, arka uç yalnızca HTTP isteklerini kabul ediyorsa aşağıdaki yapılandırma geçerli olur:
            
      * Kabul edilen protokoller HTTP ve HTTPS 'DIR. İletme Protokolü HTTP 'dir. HTTPS izin verilen bir protokol olduğundan eşleşme isteği çalışmaz. Bir istek HTTPS olarak geldiyse Azure ön kapısı, HTTPS kullanarak bu dosyayı iletmeyi dener.

      * Kabul edilen protokol HTTP 'dir. İletme protokolü bir eşleşme isteği ya da HTTP.
    - **URL yeniden yazma** varsayılan olarak devre dışıdır. Bu alan, yalnızca kullanılabilir hale getirmek istediğiniz arka uç barındırılan kaynakların kapsamını daraltmak istiyorsanız kullanılır. Bu alan devre dışı bırakıldığında, Azure ön kapısının aldığı istek yolunu iletecektir. Bu alanı hatalı yapılandırmak mümkündür. Bu nedenle, Azure ön kapısı kullanılamayan bir kaynağı bir kaynak talep edildiğinde bir HTTP 404 durum kodu döndürür.

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>Ön uç ana bilgisayar adına yapılan istek bir 411 durum kodu döndürüyor

### <a name="symptom"></a>Belirti

Azure ön kapılı bir örnek oluşturdunuz ve ön uç konağını, içinde en az bir arka ucu olan bir arka uç havuzunu ve ön uç konağını arka uç havuzuna bağlayan bir yönlendirme kuralını yapılandırdınız. Bir HTTP 411 durum kodu döndürüldüğü için bir istek yapılandırılmış ön uç konağına geçtiğinde içeriğiniz kullanılamıyor gibi görünüyor.

Bu isteklere verilen yanıtlar, açıklayıcı bir ifade içeren yanıt gövdesinde bir HTML hata sayfası da içerebilir. Örneğin: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Nedeni

Bu belirtinin birkaç olası nedeni vardır. Genel neden HTTP isteğiniz tam olarak RFC uyumlu değildir. 

Uyumsuzluk örneği, `POST` bir `Content-Length` veya `Transfer-Encoding` üst bilgisi (örneğin, kullanılarak) olmadan gönderilen bir istedir `curl -X POST https://example-front-door.domain.com` . Bu istek, [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2)' de ayarlanan gereksinimleri karşılamıyor. Azure ön kapısı, bunu bir HTTP 411 yanıtı ile engeller.

Bu davranış, Azure ön kapısının Web uygulaması güvenlik duvarı (WAF) işlevlerinden farklıdır. Şu anda bu davranışı devre dışı bırakma yöntemi yoktur. WAF işlevselliği kullanımda olmasa bile tüm HTTP isteklerinin gereksinimleri karşılaması gerekir.

### <a name="troubleshooting-steps"></a>Sorun giderme adımları

- İsteklerinizin, gerekli RFC 'lerde ayarlanan gereksinimlere uyduğundan emin olun.

- İsteğinize yanıt olarak döndürülen herhangi bir HTML ileti gövdesini göz atın. Bir ileti gövdesi genellikle isteğinizin uyumsuz olarak *nasıl* uyumlu olduğunu açıklar.
