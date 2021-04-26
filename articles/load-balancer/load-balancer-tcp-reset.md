---
title: Azure 'da TCP sıfırlaması ve boşta kalma zaman aşımı Load Balancer
titleSuffix: Azure Load Balancer
description: Bu makalede, boşta kalma zaman aşımı durumunda çift yönlü TCP RST paketlerine sahip Azure Load Balancer hakkında bilgi edinin.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2020
ms.author: allensu
ms.openlocfilehash: 9b555d7972e88df85e08dbcb1737231755c21361
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448857"
---
# <a name="load-balancer-tcp-reset-and-idle-timeout"></a>Load Balancer TCP sıfırlaması ve boşta kalma zaman aşımı

Belirli bir kural için boşta üzerinde TCP sıfırlamayı etkinleştirerek, senaryolarınız için daha öngörülebilir bir uygulama davranışı oluşturmak üzere [Standart Load Balancer](./load-balancer-overview.md) kullanabilirsiniz. Bir akışın boşta kalma zaman aşımı süresine ulaşıldığında Load Balancer varsayılan davranış sessizce bir şekilde düşürülemiyor.  Bu özelliği etkinleştirmek Load Balancer boşta kalma zaman aşımı durumunda çift yönlü TCP sıfırlama (TCP RST paketi) gönderilmesine neden olur.  Bu, uygulamanın uç noktalarını bağlantının zaman aşımına uğradığını ve artık kullanılamaz olduğunu bildirir.  Uç noktalar gerektiğinde hemen yeni bir bağlantı kurabilir.

![TCP sıfırlaması Load Balancer](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
## <a name="tcp-reset"></a>TCP sıfırlaması

Bu varsayılan davranışı değiştirir ve gelen NAT kurallarında, Yük Dengeleme kurallarında ve [giden kurallarda](./load-balancer-outbound-connections.md#outboundrules)boş zaman AŞıMı durumunda TCP sıfırlamaları göndermeyi etkinleştirin.  Kural başına etkinleştirildiğinde Load Balancer, tüm eşleşen akışlar için boşta kalma zaman aşımı durumunda hem istemci hem de sunucu uç noktalarına çift yönlü TCP sıfırlaması (TCP RST paketleri) gönderir.

TCP RST paketleri alan uç noktalar ilgili yuvayı hemen kapatır. Bu, bağlantı sürümünün gerçekleştiği uç noktalara anında bildirim sağlar ve ileride aynı TCP bağlantısıyla iletişim kurmak başarısız olur.  Uygulamalar, TCP bağlantısının son zaman aşımına ermesi gerekmeden, bağlantı gerektiğinde bağlantıları temizler ve yeniden yeniden kuracak.

Birçok senaryo için bu, bir akışın boşta kalma zaman aşımını yenilemek üzere TCP (veya uygulama katmanı) ile keepcanlı olarak gönderme gereksinimini azaltabilir. 

Boşta kalma süreleri, yapılandırma tarafından izin verilen değerleri aşarsa veya uygulamanız TCP sıfırlamaları etkin hale getirdik bir davranış gösteriyorsa, TCP bağlantılarının bir listesini izlemek için yine de TCP keeplıbekleme (veya uygulama katmanı keepka) kullanmanız gerekebilir.  Ayrıca, bu, bağlantının yolda bir yerde proxy olduğu durumlarda, özellikle de uygulama katmanı keepcanlı olarak da yararlı olabilir.  

TCP sıfırlamayı etkinleştirme, boşta kalma zaman aşımını ayarlama ve istenen uygulama davranışını sağlamak için ek adımlar gerekip gerekmediği hakkında karar vermek üzere uçtan uca senaryonun tamamını dikkatle inceleyin.

## <a name="configurable-tcp-idle-timeout"></a>Yapılandırılabilir TCP boşta kalma zaman aşımı

Azure Load Balancer şu boşta zaman aşımı aralığına sahiptir:
-  Giden kuralları için 4 dakika ila 100 dakika
-  Load Balancer kuralları ve gelen NAT kuralları için 4 dakika ila 30 dakika

Varsayılan olarak, 4 dakikaya ayarlanır. İşlem yapılmayan bir süre, zaman aşımı değerinden uzunsa, TCP veya HTTP oturumunun istemci ile bulut hizmetiniz arasında korunduğundan emin olmaz.

Bağlantı kapalıyken, istemci uygulamanız şu hata iletisini alabilir: "temel alınan bağlantı kapatıldı: etkin tutulması beklenen bir bağlantı sunucu tarafından kapatıldı."

Ortak bir uygulama, TCP etkin tutma özelliğini kullanmaktır. Bu uygulama, bağlantının daha uzun bir süre için etkin kalmasını önler. Daha fazla bilgi için, bkz. bu [.NET örnekleri](/dotnet/api/system.net.servicepoint.settcpkeepalive). Etkin tut özelliği etkinken, paketler bağlantı üzerinde işlem yapılmayan dönemler sırasında gönderilir. Canlı tut paketleri boştaki zaman aşımı değerine ulaşılmadığını ve bağlantının uzun bir süre boyunca korunmasını güvence altına aldığından emin olun.

Bu ayar yalnızca gelen bağlantılar için geçerlidir. Bağlantıyı kaybetmekten kaçınmak için, boşta kalma zaman aşımı ayarından daha az bir aralığa sahip TCP etkin tutmayı yapılandırın veya boşta kalma zaman aşımı değerini artırın. Bu senaryoları desteklemek için yapılandırılabilir bir boşta kalma zaman aşımı desteği eklenmiştir.

TCP etkin tutma, pil ömrünün kısıtlama olmadığı senaryolar için geçerlidir. Mobil uygulamalar için önerilmez. Bir mobil uygulamada TCP etkin tutma kullanmak cihaz pilinin daha hızlı tükenmesini sağlayabilir.


## <a name="limitations"></a>Sınırlamalar

- TCP sıfırlaması yalnızca belırlenen durumda TCP bağlantısı sırasında gönderilir.
- TCP boşta kalma zaman aşımı, UDP protokolünde Yük Dengeleme kurallarını etkilemez.

## <a name="next-steps"></a>Sonraki adımlar

- [Standart Load Balancer](./load-balancer-overview.md)hakkında bilgi edinin.
- [Giden kuralları](./load-balancer-outbound-connections.md#outboundrules)hakkında bilgi edinin.
- [Boştaki zaman aşımında TCP RST 'yi yapılandırma](load-balancer-tcp-idle-timeout.md)
