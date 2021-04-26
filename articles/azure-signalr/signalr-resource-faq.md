---
title: Azure SignalR hizmeti hakkında sık sorulan sorular
description: Sorun giderme ve tipik kullanım senaryoları dahil olmak üzere Azure SignalR hizmeti hakkında sık sorulan soruların yanıtlarını alın.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: d3b84756f390930be5124c6bda54d07078d29053
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166930"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR hizmeti hakkında SSS

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR hizmeti üretim kullanımı için hazırlanıyor mu?

Evet, hem [ASP.NET Core SignalR](https://dotnet.microsoft.com/apps/aspnet/signalr) hem de [ASP.NET SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr) desteği genel kullanıma sunuldu.

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Birden çok uygulama sunucusu olduğunda, istemci iletileri tüm sunuculara mı, yoksa yalnızca biri mi gönderilir?

İstemci ve uygulama sunucusu arasında bire bir eşleme vardır. Bir istemciden gelen iletiler her zaman aynı uygulama sunucusuna gönderilir.

Eşleme, istemci veya uygulama sunucusu bağlantısı kesilene kadar korunur.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Uygulama sunucularım kapalıysa, nasıl öğrenebilirim ve bildirim alabilir?

Azure SignalR hizmeti, uygulama sunucularından sinyalleri izler.
Belirli bir süre için sinyaller alınmıyorsa, uygulama sunucusu çevrimdışı kabul edilir. Bu uygulama sunucusuyla eşlenen tüm istemci bağlantılarının bağlantısı kesilecek.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>`IUserIdProvider`ASP.NET Core SIGNALR SDK 'Dan Azure SignalR hizmeti SDK 'sına geçiş yaparken özel bir özel durum oluşturur mi?

Parametresi, `HubConnectionContext context` çağrıldığında ASP.NET Core SIGNALR SDK ve Azure SignalR hizmeti SDK 'sı arasında farklıdır `IUserIdProvider` .

ASP.NET Core SignalR 'de, `HubConnectionContext context` fiziksel istemci bağlantısının tüm özellikler için geçerli değerlerle olan bağlamıdır.

Azure SignalR hizmeti SDK 'sında, `HubConnectionContext context` mantıksal istemci bağlantısının bağlamıdır. Fiziksel istemci Azure SignalR hizmeti örneğine bağlanır, bu nedenle yalnızca sınırlı sayıda özellik sağlanır.

Şimdilik yalnızca `HubConnectionContext.GetHttpContext()` ve `HubConnectionContext.User` erişim için kullanılabilir.
[Kaynak kodunu kontrol](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)edebilirsiniz.

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Sunucu tarafında Azure SignalR hizmetinde bulunan aktarımları ASP.NET Core SignalR ile yapılandırabilir miyim? Örneğin, WebSocket taşımasını devre dışı bırakabilir miyim?

Hayır.

Azure SignalR hizmeti, ASP.NET Core SignalR 'nin desteklediği üç aktarımı varsayılan olarak sağlar. Yapılandırılamaz. Azure SignalR hizmeti, tüm istemci bağlantıları için bağlantıları ve aktarımları işleyecek.

İstemci tarafı taşımalarını [ASP.NET Core SignalR yapılandırmasında](/aspnet/core/signalr/configuration#configure-allowed-transports-1)belgelenen şekilde yapılandırabilirsiniz.

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>İleti sayısı veya Azure portal gösterilen bağlantı sayısı gibi ölçümlerin anlamı nedir? Ne tür bir toplama türü seçmem gerekir?

Bu ölçümleri [Azure SignalR hizmetindeki iletilerde ve bağlantılarda](signalr-concept-messages-and-connections.md)hesapladığımız ayrıntıları bulabilirsiniz.

Azure SignalR hizmeti kaynaklarının genel bakış bölmesinde, sizin için uygun toplama türünü zaten seçtik. Ölçümler bölmesine giderseniz, toplama türünü [Azure SignalR hizmetindeki mesajlar ve bağlantılar](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr) ' a başvuru olarak alabilirsiniz.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>`Default`, `Serverless` Ve `Classic` hizmet modlarının anlamı nedir? Nasıl seçebilirim?

Yeni uygulamalar için yalnızca varsayılan ve sunucusuz mod kullanılmalıdır. Temel fark, hizmete sunucu bağlantıları oluşturan uygulama sunucularınız olup olmadığı (örn. `AddAzureSignalR()` hizmete bağlanmak için kullanın). Evet ise varsayılan modu kullan, yoksa sunucusuz mod kullanın.

Klasik mod, mevcut uygulamalar için geriye dönük uyumluluk için tasarlanmıştır, bu nedenle yeni uygulamalar için kullanılmamalıdır.

Hizmet modu hakkında daha fazla bilgi için bkz. [Azure SignalR hizmetinde hizmet modu](concept-service-mode.md).

## <a name="can-i-send-message-from-client-in-serverless-mode"></a>İstemciden sunucusuz modda ileti gönderebilir miyim?

SignalR örneğiniz için yukarı akış yapılandırırsanız istemciden ileti gönderebilirsiniz. Yukarı akış, SignalR hizmetinden ileti ve bağlantı olayları alabilen bir uç nokta kümesidir. Yukarı akış yapılandırılmamışsa, istemciden gelen iletiler yok sayılır.

Yukarı akış hakkında daha fazla bilgi için bkz. [yukarı akış ayarları](concept-upstream.md).

Yukarı akış şu anda genel önizleme aşamasındadır.

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>ASP.NET SignalR ile Azure SignalR hizmetini kullanırken herhangi bir özellik farkı var mı?

Azure SignalR hizmetini kullanırken, ASP.NET SignalR 'nin bazı API 'Leri ve özellikleri desteklenmez:
- İstemciler ve merkez arasında rastgele durum geçirme özelliği (genellikle denir `HubState` ) desteklenmez.
- `PersistentConnection`Sınıf desteklenmiyor.
- *Süresiz çerçeve taşıması* desteklenmez.
- Azure SignalR hizmeti, istemci çevrimdışıyken istemciye gönderilen iletileri artık yeniden oynamıyor.
- Azure SignalR hizmetini kullanırken, bir istemci bağlantısının trafiği bağlantı süresince tek bir App Server örneğine her zaman yönlendirilir ( *yapışkan* olarak da adlandırılır).

ASP.NET SignalR desteği uyumlulukla odaklanmıştır, bu nedenle ASP.NET Core SignalR 'tan gelen yeni özelliklerin bazıları desteklenmez. Örneğin, *MessagePack* ve *akış* yalnızca ASP.NET Core SignalR uygulamaları için kullanılabilir.

Azure SignalR hizmetini farklı hizmet modları için yapılandırabilirsiniz: `Classic` , `Default` , ve `Serverless` . `Serverless`Mod, ASP.NET için desteklenmiyor. Veri düzlemi REST API de desteklenmez.

## <a name="where-does-my-data-reside"></a>Verilerim nerede bulunur?

Azure SignalR hizmeti bir veri işlemcisi hizmeti olarak çalışmaktadır. Hiçbir müşteri içeriğini depolamaz ve veri yerleşimi tasarım tarafından dahil edilir. Tanılama için Azure depolama gibi diğer Azure hizmetleriyle birlikte Azure SignalR hizmetini kullanıyorsanız, verilerin Azure bölgelerinde nasıl tutulabileceğine ilişkin yönergeler için [Bu teknik incelemeye](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) bakın.
