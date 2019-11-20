---
title: Sorun giderme-Azure ön kapı hizmeti yapılandırmanızla ilgili sorunları giderin | Microsoft Docs
description: Bu öğreticide, ön kapılarınız için yüz yüze olabilecek bazı yaygın sorunları nasıl giderebileceğinizi öğreneceksiniz.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: 420d7afe0d825da9149f2cb2ae1540a2805b357c
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335884"
---
# <a name="troubleshooting-common-routing-issues"></a>Yaygın yönlendirme sorunlarını giderme
Bu makalede, Azure ön kapı hizmeti yapılandırmanız için kullanabileceğiniz bazı yaygın yönlendirme sorunları nasıl giderilir açıklanmaktadır. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>Ana bilgisayar adı arka uca yönlendirmedi ve 400 durum kodu döndürüyor


### <a name="symptom"></a>Belirti
- Ön kapı oluşturdunuz, ancak ön uç konağına bir istek HTTP 400 durum kodu döndürüyor.

  - Özel bir etki alanından yapılandırdığınız ön uç konağına bir DNS eşlemesi oluşturdunuz. Ancak, özel etki alanı ana bilgisayar adına bir istek gönderdiğinizde bir HTTP 400 durum kodu döndürülür ve yapılandırdığınız arka uçlara yol gösterilmiyor.

### <a name="cause"></a>Nedeni
- Bu belirti, ön uç Konağı olarak eklediğiniz özel etki alanı için bir yönlendirme kuralı yapılandırmadıysanız meydana gelebilir. Özel etki alanınızın bir DNS eşlemesine sahip olduğu ön kapı alt etki alanı (*. azurefd.net) altındaki ön uç ana bilgisayar için zaten yapılandırılmış olsa bile, bu ön uç ana bilgisayar için bir yönlendirme kuralının açıkça eklenmesi gerekir.

### <a name="troubleshooting-steps"></a>Sorun giderme adımları
- Özel etki alanından istenen arka uç havuzuna bir yönlendirme kuralı ekleyin.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Ön uç konak adı isteği 404 durum kodu döndürüyor

### <a name="symptom"></a>Belirti
- Ön kapı oluşturdunuz ve bir ön uç Konağı, içinde en az bir arka uç içeren bir arka uç havuzu ve ön uç konağını arka uç havuzuna bağlayan bir yönlendirme kuralı yapılandırdınız. Bir HTTP 404 durum kodu döndürüldüğünden, içerik yapılandırılmış ön uç konağına bir istek gönderilirken kullanılamıyor gibi görünüyor.

### <a name="cause"></a>Nedeni
Bu belirtinin birkaç olası nedeni vardır:
 - Arka uç, genel kullanıma yönelik bir arka uç değildir ve ön kapı hizmeti için görünür değildir.

- Arka uç, ön kapı hizmetinin yanlış isteği göndermesini sağlayan (Yani arka ucunuz yalnızca HTTP 'yi kabul eder, ancak ön kapıda HTTPS isteklerini iletmeyi denemenize izin verilmemiştir.
- Arka uç, istekle birlikte arka uca iletilen konak üstbilgisini reddediyor.
- Arka ucun yapılandırması henüz tam olarak dağıtılmadı.

### <a name="troubleshooting-steps"></a>Sorun giderme adımları
1. Dağıtım saati
    - Yapılandırmanın dağıtılması için yaklaşık 10 dakika beklediğinizden emin olun.

2. Arka uç ayarlarını denetleyin
   - İsteğin yönlendirilmesi gereken arka uç havuzuna gidin (yönlendirme kuralını nasıl yapılandırdığınıza bağlıdır) ve _arka uç konak türünün_ ve arka uç ana bilgisayar adının doğru olduğundan emin olun. Arka uç özel bir ana bilgisayar ise doğru yazdığınızdan emin olun. 

   - HTTP ve HTTPS bağlantı noktalarınızı denetleyin. Çoğu durumda, 80 ve 443 (sırasıyla) doğru olur ve hiçbir değişiklik yapmanız gerekmez. Ancak, arka ucunuzun bu şekilde yapılandırılmadığı ve farklı bir bağlantı noktasında dinleme yaptığı bir şansınız vardır.

     - Ön uç konağın yönlendirilmesi gereken _arka uç ana bilgisayar üst bilgisini_ denetleyin. Çoğu durumda bu üst bilgi, _arka uç ana bilgisayar adıyla_aynı olmalıdır. Ancak, arka uç farklı bir şeyi bekliyorsa, yanlış bir değer çeşitli HTTP 4xx durum kodlarına neden olabilir. Arka ucunuzun IP adresini girverirseniz, arka uç _ana bilgisayar üst bilgisini_ arka ucun ana bilgisayar adına ayarlamanız gerekebilir.


3. Yönlendirme kuralı ayarlarını denetleyin
     - Söz konusu ön uç ana bilgisayar adından bir arka uç havuzuna yönlendirilmesi gereken yönlendirme kuralına gidin. Kabul edilen protokollerin doğru yapılandırıldığından emin olun, aksi takdirde, isteğin iletilmesi doğru şekilde yapılandırıldığında protokol ön kapısının kullanılmasını sağlayın. _Kabul edilen protokoller_ hangi Isteklerin ön kapısının kabul edeceğini belirler ve _iletme Protokolü_ , isteği arka uca Iletmek Için hangi protokol ön kapısının kullanılacağını belirler.
          - Örnek olarak, arka uç yalnızca HTTP isteklerini kabul ediyorsa aşağıdaki yapılandırma geçerli olacaktır:
               - _Kabul edilen protokoller_ http ve https 'dir. _İletme Protokolü_ http 'dir. HTTPS izin verilen bir protokol olduğundan ve bir istek HTTPS olarak verildiyse, ön kapı HTTPS kullanarak iletmeyi dener.

               - _Kabul edilen protokoller_ http. _İletme Protokolü_ , istek ya da https ile eşleşiyor.

   - _URL yeniden yazma_ varsayılan olarak devre dışıdır ve yalnızca kullanılabilir hale getirmek istediğiniz arka uç barındırılan kaynakların kapsamını daraltmak istiyorsanız bu alanı kullanmanız gerekir. Devre dışı bırakıldığında, ön kapıda aldığı istek yolunu iletecektir. Bu alan yanlış yapılandırılmış ve ön kapı, bir HTTP 404 durum kodu döndüren arka uçta bir kaynak istiyor olabilir.

