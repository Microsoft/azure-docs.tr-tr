---
title: Azure SignalR hizmeti hakkında sık sorulan sorular
description: Azure SignalR hizmeti hakkında sık sorulan soruların ve sorun giderme ve tipik kullanım senaryolarında hızlı bir şekilde erişmesini sağlayabilirsiniz.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 9a8c3fd6c591f25aadc696c3c71f8c4cdfca745f
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74157559"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR hizmeti hakkında SSS

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR hizmeti üretim kullanımı için hazırlanıyor mu?

Evet.
Genel kullanılabilirlik duyurumız için bkz. [Azure SignalR hizmeti genel kullanıma sunuldu](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) tam olarak desteklenir.

ASP.NET SignalR desteği hala *genel önizlemede*. Aşağıda bir [kod örneği](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom)verilmiştir.

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Istemci bağlantısı, "sunucu kullanılamıyor" hata iletisiyle kapanıyor. Ne anlama geliyor?

Bu hata yalnızca istemciler SignalR hizmetine ileti gönderirken oluşur.

Herhangi bir uygulama sunucunuz yoksa ve yalnızca SignalR hizmeti REST API kullanıyorsanız, bu davranış **tasarıma göre**yapılır.
Sunucusuz mimaride, istemci bağlantıları **dinleme** modundadır ve SignalR hizmetine ileti göndermez.
[REST API](./signalr-quickstart-rest-api.md)hakkında daha fazla bilgi edinin.

Uygulama sunucularınız varsa, bu hata iletisi, SignalR hizmet örneğinize bağlı hiçbir uygulama sunucusu olmadığı anlamına gelir.

Olası nedenler şunlardır:
- SignalR hizmeti ile hiçbir uygulama sunucusu bağlanmadı. Olası bağlantı hataları için uygulama sunucusu günlüklerine bakın. Bu durumda, birden fazla uygulama sunucusu olan yüksek kullanılabilirlik ayarında nadir olarak karşılaşılır.
- SignalR hizmet örneklerinde bağlantı sorunları var. Bu sorun geçicidir ve otomatik olarak kurtarılır.
Bir saatten uzun süre devam ederse, [GitHub 'da bir sorun açın](https://github.com/Azure/azure-signalr/issues/new) veya [Azure 'da bir destek isteği oluşturun](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Birden çok uygulama sunucusu olduğunda, istemci iletileri tüm sunuculara mı, yoksa yalnızca biri mi gönderilir?

İstemci ve uygulama sunucusu arasında bire bir eşleme olur. Bir istemciden gelen iletiler her zaman aynı uygulama sunucusuna gönderilir.

İstemci veya uygulama sunucusu bağlantısı kesilene kadar, istemci ve uygulama sunucusu arasındaki eşleme korunur.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Uygulama sunucularım kapalıysa, nasıl öğrenebilirim ve bildirim alabilir?

SignalR hizmeti, uygulama sunucularından sinyalleri izler.
Belirli bir süre için sinyaller alınmıyorsa, uygulama sunucusu çevrimdışı kabul edilir. Bu uygulama sunucusuyla eşlenen tüm istemci bağlantılarının bağlantısı kesilecek.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>ASP.NET Core SignalR SDK 'dan Azure SignalR hizmeti SDK 'sına geçiş yaparken özel `IUserIdProvider` neden özel durum oluşturmaz?

`HubConnectionContext context` parametresi, `IUserIdProvider` çağrıldığında ASP.NET Core SignalR SDK ve Azure SignalR hizmeti SDK 'Sı arasında farklıdır.

ASP.NET Core SignalR 'de, `HubConnectionContext context` tüm özellikler için geçerli değerlerle fiziksel istemci bağlantısının bağlamıdır.

Azure SignalR hizmeti SDK 'sında `HubConnectionContext context`, mantıksal istemci bağlantısının bağlamıdır. Fiziksel istemci bağlantısı, SignalR hizmeti örneğine bağlanır, bu nedenle yalnızca sınırlı sayıda özellik sağlanır.

Şimdilik yalnızca `HubConnectionContext.GetHttpContext()` ve `HubConnectionContext.User` erişim için kullanılabilir.
Kaynak kodunu [buradan](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)denetleyebilirsiniz.

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>SignalR hizmetinde bulunan aktarımları, sunucu tarafında ASP.NET Core SignalR ile yapılandırma olarak yapılandırabilir miyim? Örneğin, WebSocket aktarımını devre dışı bırakmak istiyor musunuz?

Hayır.

Azure SignalR hizmeti, ASP.NET Core SignalR 'nin desteklediği üç aktarımı varsayılan olarak sağlar. Yapılandırılamaz. SignalR hizmeti, tüm istemci bağlantıları için bağlantıları ve aktarımları işleyecek.

İstemci tarafı taşımalarını [burada](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1#configure-allowed-transports)belgelenen şekilde yapılandırabilirsiniz.
