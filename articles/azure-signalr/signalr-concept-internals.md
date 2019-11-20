---
title: Azure SignalR Service iç işlevleri
description: Azure SignalR hizmeti iç işlevleri, mimari, bağlantılar ve verilerin nasıl aktarıldığı hakkında bilgi edinin.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 62afa5ee6993aa1bb3c7b5926e5320ab1fa510a2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74157605"
---
# <a name="azure-signalr-service-internals"></a>Azure SignalR Service iç işlevleri

Azure SignalR hizmeti ASP.NET Core SignalR çerçevesinin üzerine kurulmuştur. Ayrıca, önizleme özelliği olarak ASP.NET SignalR 'yi destekler.

> Azure SignalR hizmeti, ASP.NET SignalR 'yi desteklemek için ASP.NET Core Framework 'ün en üstünde ASP.NET SignalR 'nin veri protokolünü yeniden uygular

Bir yerel ASP.NET Core SignalR uygulamasını, birkaç satır kod değişikliğine sahip SignalR hizmeti ile çalışmak üzere kolayca geçirebilirsiniz.

Aşağıdaki diyagramda, SignalR hizmetini uygulama sunucunuz ile kullandığınızda tipik mimari açıklanmaktadır.

Şirket içinde barındırılan ASP.NET Core SignalR uygulamasının farklılıkları da ele alınmıştır.

![Mimari](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>Sunucu bağlantıları

Şirket içinde barındırılan ASP.NET Core SignalR uygulama sunucusu, istemcileri doğrudan dinler ve istemcilere bağlar.

SignalR hizmeti ile uygulama sunucusu artık kalıcı istemci bağlantılarını kabul etmiyor, bunun yerine:

1. `negotiate` uç noktası her Hub için Azure SignalR hizmeti SDK 'Sı tarafından sunulur.
1. Bu uç nokta, istemcinin anlaşma isteklerine yanıt verir ve istemcileri SignalR hizmetine yönlendirir.
1. Sonuç olarak, istemciler SignalR hizmetine bağlanır.

Daha fazla bilgi için bkz. [istemci bağlantıları](#client-connections).

Uygulama sunucusu başlatıldıktan sonra, 
- ASP.NET Core SignalR için Azure SignalR hizmeti SDK 'Sı, SignalR hizmetine hub başına 5 WebSocket bağlantısı açar. 
- ASP.NET SignalR için Azure SignalR hizmeti SDK 'Sı, SignalR hizmetine hub başına 5 WebSocket bağlantısı ve uygulama WebSocket bağlantısı başına bir tane açar.

5 WebSocket bağlantısı, [yapılandırmada](https://github.com/Azure/azure-signalr/blob/dev/docs/use-signalr-service.md#connectioncount)değiştirilebilen varsayılan değerdir.

İstemcilere gelen ve bu bağlantılara yönelik iletiler bu bağlantılar için çoğullanmış olacaktır.

Bu bağlantılar, her zaman SignalR hizmetine bağlı olmaya devam edecektir. Ağ sorunu için bir sunucu bağlantısı kesilirse,
- Bu sunucu bağlantısı bağlantı kesildiğinde sunulan tüm istemciler (hakkında daha fazla bilgi için, bkz. [istemci ve sunucu arasında veri aktarımı](#data-transmit-between-client-and-server));
- sunucu bağlantısı otomatik olarak yeniden bağlanmaya başlar.

## <a name="client-connections"></a>İstemci bağlantıları

SignalR hizmetini kullandığınızda istemciler, uygulama sunucusu yerine SignalR hizmetine bağlanır.
İstemci ile SignalR hizmeti arasında kalıcı bağlantılar kurmak için iki adım vardır.

1. İstemci, uygulama sunucusuna bir anlaşma isteği gönderir. Azure SignalR hizmeti SDK 'Sı ile, Application Server, SignalR hizmetinin URL 'SI ve erişim belirteci ile bir yeniden yönlendirme yanıtı döndürür.

- ASP.NET Core SignalR için tipik bir yeniden yönlendirme yanıtı şöyle görünür:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- ASP.NET SignalR için tipik bir yeniden yönlendirme yanıtı şöyle görünür:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. İstemci, yeniden yönlendirme yanıtını aldıktan sonra, SignalR hizmetine bağlanmak için normal işlemi başlatmak üzere yeni URL ve erişim belirtecini kullanır.

ASP.NET Core SignalR 'nin [taşıma protokolleri](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md)hakkında daha fazla bilgi edinin.

## <a name="data-transmit-between-client-and-server"></a>İstemci ve sunucu arasında veri aktarımı

İstemci, SignalR hizmetine bağlandığında, hizmet çalışma zamanı bu istemciye hizmet edecek bir sunucu bağlantısı bulur
- Bu adım yalnızca bir kez gerçekleşir ve istemci ile sunucu bağlantıları arasında bire bir eşleme olur.
- Eşleme, istemci veya sunucu bağlantısı kesilene kadar SignalR hizmetinde tutulur.

Bu noktada, uygulama sunucusu yeni istemciden bilgi içeren bir olay alır. Uygulama sunucusunda istemciye bir mantıksal bağlantı oluşturulur. Veri kanalı, SignalR hizmeti aracılığıyla istemciden uygulama sunucusuna oluşturulur.

SignalR hizmeti, verileri istemciden eşleştirme uygulama sunucusuna iletir. Ve uygulama sunucusundaki veriler eşlenen istemcilere gönderilir.

Gördüğünüz gibi, Azure SignalR hizmeti temelde uygulama sunucusu ve istemciler arasındaki mantıksal bir aktarım katmanıdır. Tüm kalıcı bağlantılar SignalR hizmetine boşaltılır.
Uygulama sunucusunun, istemci bağlantıları hakkında endişelenmeden yalnızca Hub sınıfında iş mantığını işlemesi gerekir.