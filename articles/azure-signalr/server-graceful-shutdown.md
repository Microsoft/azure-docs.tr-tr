---
title: Uygulama sunucunuzu sorunsuz bir şekilde durdurun.
description: Bu makalede, bir SignalR App Server düzgün şekilde kapatılıyor hakkında bilgi sağlanır
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: d9dd7ce9cf321628598a7bb866c5d1b1a6fb0e1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98201680"
---
# <a name="server-graceful-shutdown"></a>Sunucu düzgün kapanma
Microsoft Azure SignalR hizmeti, sunucuyu düzgün bir şekilde kapatmak için iki mod sağlar. 

Bu özelliği kullanmanın önemli avantajı, müşterinizin beklenmedik bağlantı düşerek yararlanmasını önlemektir. 

Bunun yerine, istemci bağlantılarınızı iş mantığınızla ilgili olarak kapatabilir veya hatta verileri kaybetmeden istemci bağlantısını başka bir sunucuya geçirebilirsiniz. 

## <a name="how-it-works"></a>Nasıl çalışır?

Genel olarak, düzgün kapanma sürecinde dört aşama olacaktır:

1. **Sunucuyu çevrimdışına ayarla**

    Bu sunucuya daha fazla istemci bağlantısı yönlendirilmeyecektir.

2. **Tetikleyici `OnShutdown` kancaları**

    Sunucunuza ait olduğunuz her Hub için kapalı kancaları kaydedebilirsiniz.
    Bunlar, Azure SignalR hizmetimizden gelen bir **Finack** yanıtı aldıktan sonra, bu sunucunun Azure SignalR hizmeti 'nde çevrimdışı olarak ayarlandığı anlamına gelen kayıtlı siparişle ilgili olarak çağrılacaktır.

    Bu aşamada ileti yayınlayabilir veya bazı temizleme işleri yapabilirsiniz, tüm kapatmalar kancaları yürütüldükten sonra bir sonraki aşamaya devam edeceğiz.

3. **Tüm istemci bağlantıları bitene kadar bekleyin**, seçtiğiniz moda bağlı olarak şunları yapabilirsiniz:

    **Mod WaitForClientsToClose olarak ayarlandı**

    Azure SignalR hizmeti mevcut istemcileri tutacaktır.

    Tüm istemcilere bir kapanış iletisi yayınla gibi bir yol tasarlamanızı ve sonra istemcilerinizin ne zaman kapatılıp yeniden bağlanacağına karar vermesini sağlayabilirsiniz.

    Örnek kullanım için [ChatSample](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample) okuyun ve bu, kapatma kancası 'nda istemci kapanışını tetiklemek için bir ' çıkış ' iletisi yayınlarız.

    **Mod MigrateClients olarak ayarlandı**

    Azure SignalR hizmeti, bu sunucudaki istemci bağlantısını başka bir geçerli sunucuya yeniden yönlendirçalışacak. 
    
    Bu senaryoda, `OnConnectedAsync` ve `OnDisconnectedAsync` Yeni sunucuda ve eski sunucuda sırasıyla `IConnectionMigrationFeature` , `HttpContext` istemci bağlantısının geçirilmekte olup olmadığını belirlemek için kullanılan ' de bir kümesiyle tetiklenecektir. Özellikle de durum bilgisi olan senaryolar için yararlı olabilir.

    İstemci bağlantısı, geçerli ileti teslim edildikten sonra hemen geçirilir, bu da sonraki iletinin yeni sunucuya yönlendirileceği anlamına gelir.

4. **Sunucu bağlantılarını durdur**

    Tüm istemci bağlantıları kapatıldıktan/geçirildikten sonra veya zaman aşımı (varsayılan olarak 30s) aşıldıktan sonra,

    SignalR Server SDK 'Sı, bu aşamaya kapatma işlemini devam edecektir ve tüm sunucu bağlantılarını kapatacaktır.

    İstemci bağlantıları kapatılamadığı/geçirilemeyen takdirde, yine de bırakılacaktır. Örneğin, uygun bir hedef sunucu/geçerli istemciden sunucuya ileti bitmedi.

## <a name="sample-codes"></a>Örnek kodlar.

Şu durumlarda aşağıdaki seçenekleri ekleyin `AddAzureSignalR` :

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    option.GracefulShutdown.Mode = GracefulShutdownMode.WaitForClientsClose;
    // option.GracefulShutdown.Mode = GracefulShutdownMode.MigrateClients;
    option.GracefulShutdown.Timeout = TimeSpan.FromSeconds(30);

    option.GracefulShutdown.Add<Chat>(async (c) =>
    {
        await c.Clients.All.SendAsync("exit");
    });
});
```

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>`OnConnected` `OnDisconnected` düzgün kapanma modunu yapılandırma ve olarak ayarlama `MigrateClients` .

Bir bağlantının geçirilip geçirilmeyeceğini göstermek için bir "ınewctionmigrationfeature" sunuyoruz.

```csharp
public class Chat : Hub {

    public override async Task OnConnectedAsync()
    {
        Console.WriteLine($"{Context.ConnectionId} connected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateTo}] {Context.ConnectionId} is migrated from {feature.MigrateFrom}.");
            // Your business logic.
        }

        await base.OnConnectedAsync();
    }

    public override async Task OnDisconnectedAsync(Exception e)
    {
        Console.WriteLine($"{Context.ConnectionId} disconnected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateFrom}] {Context.ConnectionId} will be migrated to {feature.MigrateTo}.");
            // Your business logic.
        }

        await base.OnDisconnectedAsync(e);
    }
}
```