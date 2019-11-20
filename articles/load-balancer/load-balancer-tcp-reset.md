---
title: Azure 'da boşta üzerinde TCP sıfırlaması Load Balancer
titleSuffix: Azure Load Balancer
description: Bu makalede, boşta kalma zaman aşımı durumunda çift yönlü TCP RST paketlerine sahip Azure Load Balancer hakkında bilgi edinin.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: b37253f37043d902d33504b99401781eb1c761c5
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075928"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>Boşta durumunda TCP sıfırlaması ile Load Balancer (Genel Önizleme)

Belirli bir kural için boşta üzerinde TCP sıfırlamayı etkinleştirerek, senaryolarınız için daha öngörülebilir bir uygulama davranışı oluşturmak üzere [Standart Load Balancer](load-balancer-standard-overview.md) kullanabilirsiniz. Bir akışın boşta kalma zaman aşımı süresine ulaşıldığında Load Balancer varsayılan davranış sessizce bir şekilde düşürülemiyor.  Bu özelliği etkinleştirmek Load Balancer boşta kalma zaman aşımı durumunda çift yönlü TCP sıfırlama (TCP RST paketi) gönderilmesine neden olur.  Bu, uygulamanın uç noktalarını bağlantının zaman aşımına uğradığını ve artık kullanılamaz olduğunu bildirir.  Uç noktalar gerektiğinde hemen yeni bir bağlantı kurabilir.

![TCP sıfırlaması Load Balancer](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>Boşta kalma zaman aşımı işlevselliğinde TCP sıfırlaması ile Load Balancer Şu anda genel önizleme olarak sunulmaktadır. Bu önizleme bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Bu varsayılan davranışı değiştirir ve gelen NAT kurallarında, Yük Dengeleme kurallarında ve [giden kurallarda](https://aka.ms/lboutboundrules)boş zaman AŞıMı durumunda TCP sıfırlamaları göndermeyi etkinleştirin.  Kural başına etkinleştirildiğinde Load Balancer, tüm eşleşen akışlar için boşta kalma zaman aşımı durumunda hem istemci hem de sunucu uç noktalarına çift yönlü TCP sıfırlaması (TCP RST paketleri) gönderir.

TCP RST paketleri alan uç noktalar ilgili yuvayı hemen kapatır. Bu, bağlantı sürümünün gerçekleştiği uç noktalara anında bildirim sağlar ve ileride aynı TCP bağlantısıyla iletişim kurmak başarısız olur.  Uygulamalar, TCP bağlantısının son zaman aşımına ermesi gerekmeden, bağlantı gerektiğinde bağlantıları temizler ve yeniden yeniden kuracak.

Birçok senaryo için bu, bir akışın boşta kalma zaman aşımını yenilemek üzere TCP (veya uygulama katmanı) ile keepcanlı olarak gönderme gereksinimini azaltabilir. 

Boşta kalma süreleri, yapılandırma tarafından izin verilen değerleri aşarsa veya uygulamanız TCP sıfırlamaları etkin hale getirdik bir davranış gösteriyorsa, TCP bağlantılarının bir listesini izlemek için yine de TCP keeplıbekleme (veya uygulama katmanı keepka) kullanmanız gerekebilir.  Ayrıca, bu, bağlantının yolda bir yerde proxy olduğu durumlarda, özellikle de uygulama katmanı keepcanlı olarak da yararlı olabilir.  

TCP sıfırlamayı etkinleştirme, boşta kalma zaman aşımını ayarlama ve istenen uygulama davranışını sağlamak için ek adımlar gerekip gerekmediği hakkında karar vermek üzere uçtan uca senaryonun tamamını dikkatle inceleyin.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Boşta kalma zaman aşımı durumunda TCP sıfırlamayı etkinleştirme

API sürüm 2018-07-01 ' yi kullanarak, boş zaman aşımı durumunda her kural temelinde çift yönlü TCP sıfırlamaları göndermeyi etkinleştirebilirsiniz:

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="regions"></a>Bölge kullanılabilirliği

Tüm bölgelerde kullanılabilir.

## <a name="limitations"></a>Sınırlamalar

- Portal, TCP sıfırlamayı yapılandırmak veya görüntülemek için kullanılamaz.  Bunun yerine şablonları, REST API, Az CLI 2. 0'ı veya PowerShell kullanın.
- TCP RST yalnızca TCP bağlantısı sırasında belırlenen durumda gönderilir.

## <a name="next-steps"></a>Sonraki adımlar

- Hakkında bilgi edinin [standart Load Balancer](load-balancer-standard-overview.md).
- [Giden kuralları](load-balancer-outbound-rules-overview.md)hakkında bilgi edinin.
