---
title: Azure SignalR hizmetindeki mesajlar ve bağlantılar
description: Azure SignalR hizmetindeki iletiler ve bağlantılarla ilgili temel kavramlara genel bakış.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: zhshang
ms.openlocfilehash: 3c4d28addac0ecfc9605678582562550a1c96b8d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491954"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Azure SignalR hizmetindeki mesajlar ve bağlantılar

Azure SignalR hizmeti için faturalandırma modeli, bağlantı sayısını ve ileti sayısını temel alır. Bu makalede, iletilerin ve bağlantıların faturalandırma için nasıl tanımlandığı ve sayılacağı açıklanmaktadır.


## <a name="message-formats"></a>İleti biçimleri 

Azure SignalR hizmeti ASP.NET Core SignalR: [JSON](https://www.json.org/) ve [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)ile aynı biçimleri destekler.

## <a name="message-size"></a>İleti boyutu

Azure SignalR hizmetinin iletiler için boyut sınırı yoktur.

Büyük iletiler 2 KB 'tan fazla olmayan ve ayrı olarak aktarılan daha küçük iletilere ayrılır. SDK 'lar ileti bölme ve montaj işlemesi. Geliştirici çabalarına gerek yoktur.

Büyük iletiler, mesajlaşma performansını olumsuz etkiler. Mümkün olan her durumda daha küçük iletileri kullanın ve her bir kullanım örneği senaryosu için en iyi ileti boyutunu belirleme sınamasını yapın.

## <a name="how-messages-are-counted-for-billing"></a>Faturalandırmayla ilgili mesajlar nasıl sayılır?

Faturalandırma için yalnızca Azure SignalR hizmetinden gelen giden iletiler sayılır. İstemciler ve sunucular arasındaki ping iletileri yok sayılır.

2 KB 'den büyük mesajlar her biri 2 KB 'lık birden fazla ileti olarak sayılır. Azure portal ileti sayısı grafiği her Hub için her 100 ileti için güncelleştirilir.

Örneğin, bir uygulama sunucunuz ve üç istemciniz olduğunu düşünün:

App Server tüm bağlı istemcilere 1 KB 'lik bir ileti yayınlar, App Server 'daki ileti, ücretsiz gelen ileti olarak kabul edilir. Yalnızca hizmetten her istemciye gönderilen üç ileti, giden iletiler olarak faturalandırılır.

A istemcisi, App Server 'a geçmeden diğer bir istemciye 1 KB 'lik bir ileti gönderir. İstemciden hizmete giden ileti, ücretsiz gelen iletidir. Hizmetten istemciye B 'ye ileti, giden ileti olarak faturalandırılır.

Üç istemciniz ve bir uygulama sunucunuz varsa. Bir istemci, sunucunun tüm istemcilere yayınlamasına izin vermek için 4 KB 'lik bir ileti gönderir. Faturalanan ileti sayısı sekiz ' dır: hizmetten uygulama sunucusuna bir ileti ve hizmetten istemcilere yapılan üç ileti. Her ileti 2 2 KB 'lik ileti olarak sayılır.

## <a name="how-connections-are-counted"></a>Bağlantılar nasıl sayılır?

Azure SignalR hizmeti ile sunucu bağlantıları ve istemci bağlantıları vardır. Varsayılan olarak, her bir uygulama sunucusu hub başına beş ilk bağlantı ile başlar ve her istemcide bir istemci bağlantısı vardır.

Örneğin, iki uygulama sunucunuz olduğunu ve kodda beş hub tanımladığınızı varsayalım. Sunucu bağlantı sayısı 50 olacaktır: 2 App Servers * 5 hub *, hub başına 5 bağlantı.

Azure portal gösterilen bağlantı sayısı sunucu bağlantılarını, istemci bağlantılarını, tanılama bağlantılarını ve canlı izleme bağlantılarını içerir. Bağlantı türleri aşağıdaki listede tanımlanmıştır:

- **Sunucu bağlantısı**: Azure SignalR hizmetini ve uygulama sunucusunu bağlar.
- **İstemci bağlantısı**: Azure SignalR hizmetini ve istemci uygulamasını bağlar.
- **Tanılama bağlantısı**: daha ayrıntılı bir günlük üretebilen, performansı etkileyebilecek özel bir istemci bağlantısı türü. Bu tür bir istemci, sorun giderme için tasarlanmıştır.
- **Canlı izleme bağlantısı**: canlı izleme uç noktasına bağlanır ve Azure SignalR hizmeti 'nin canlı izlemelerini alır. 
 
Canlı izleme bağlantısının, istemci bağlantısı veya sunucu bağlantısı olarak sayılmadığını unutmayın. 

ASP.NET SignalR sunucu bağlantılarını farklı bir şekilde hesaplar. Bu, tanımladığınız hublara ek olarak bir varsayılan Hub içerir. Varsayılan olarak, her uygulama sunucusunun beş daha fazla ilk sunucu bağlantısı olması gerekir. Varsayılan Hub için ilk bağlantı sayısı diğer hub 'larla tutarlı kalır.

Hizmet ve uygulama sunucusu, daha iyi performans ve hizmet kararlılığı elde etmek için bağlantı durumunu eşitlemeyi ve sunucu bağlantılarında ayarlama yapmayı devam etmesini sağlar.  Bu nedenle sunucu bağlantı numarası değişikliğini zaman zaman görebilirsiniz.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Gelen/giden trafik nasıl sayılır?

Hizmete gönderilen ileti, gelen iletidir. Hizmetten gönderilen ileti, giden iletidir. Trafik bayt cinsinden hesaplanır.

## <a name="related-resources"></a>İlgili kaynaklar

- [Azure Izleyici 'de toplama türleri](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR yapılandırması](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
