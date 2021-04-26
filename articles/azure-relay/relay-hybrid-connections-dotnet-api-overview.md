---
title: Azure Relay .NET Standard API 'Lerine genel bakış | Microsoft Docs
description: Bu makalede, Azure Relay Karma Bağlantılar .NET Standard API 'sine genel bakış özetlenmektedir.
ms.topic: article
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 724fb1a62b82036b4a0fa8b9f4f3608293f608a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625140"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Azure Relay Karma Bağlantılar .NET Standard API 'ye Genel Bakış

Bu makalede, bazı anahtar Azure Relay Karma Bağlantılar .NET Standard [Istemci API 'leri](/dotnet/api/microsoft.azure.relay)özetlenmektedir.
  
## <a name="relay-connection-string-builder-class"></a>Geçiş bağlantı dizesi Oluşturucu sınıfı

[RelayConnectionStringBuilder][RelayConnectionStringBuilder] sınıfı, geçişe özgü bağlantı dizelerini biçimlendirir karma bağlantılar. Bunu bir bağlantı dizesinin biçimini doğrulamak veya sıfırdan bir bağlantı dizesi oluşturmak için kullanabilirsiniz. Örnek için aşağıdaki koda bakın:

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

Ayrıca, bir bağlantı dizesini doğrudan `RelayConnectionStringBuilder` yöntemine geçirebilirsiniz. Bu işlem, bağlantı dizesinin geçerli bir biçimde olduğunu doğrulamanızı sağlar. Parametrelerden herhangi biri geçersiz ise, Oluşturucu bir oluşturur `ArgumentException` .

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Karma bağlantı akışı

[Hybridconnectionstream][HCStream] sınıfı, bir [Hybridconnectionclient][HCClient]veya [hybridconnectionlistener][HCListener]ile çalıştığınızdan bir Azure Relay uç noktasından veri göndermek ve almak için kullanılan birincil nesnedir.

### <a name="getting-a-hybrid-connection-stream"></a>Karma bağlantı akışı alma

#### <a name="listener"></a>Dinleyici

Bir [Hybridconnectionlistener][HCListener] nesnesi kullanarak, `HybridConnectionStream` aşağıdaki gibi bir nesne elde edebilirsiniz:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>İstemci

Bir [Hybridconnectionclient][HCClient] nesnesi kullanarak, `HybridConnectionStream` aşağıdaki gibi bir nesne elde edebilirsiniz:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Veri alma

[Hybridconnectionstream][HCStream] sınıfı iki yönlü iletişime olanak tanıyor. Çoğu durumda, akıştan sürekli olarak alırsınız. Akıştan metin okuyorsanız, verilerin daha kolay ayrıştırılmasını sağlayan bir [StreamReader](/dotnet/api/system.io.streamreader) nesnesi de kullanmak isteyebilirsiniz. Örneğin, verileri yerine metin olarak okuyabilirsiniz `byte[]` .

Aşağıdaki kod, bir iptal istenene kadar her metin satırını akıştan okur:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Veriler gönderiliyor

Bir bağlantı kurulduktan sonra, geçiş uç noktasına bir ileti gönderebilirsiniz. Bağlantı nesnesi [Stream](/dotnet/api/system.io.stream)devraldığından verilerinizi bir olarak gönderin `byte[]` . Aşağıdaki örnek bunun nasıl yapılacağını göstermektedir:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Ancak, her seferinde dizeyi kodlamasına gerek kalmadan doğrudan metin göndermek istiyorsanız `hybridConnectionStream` nesneyi bir [StreamWriter](/dotnet/api/system.io.streamwriter) nesnesiyle sardırabilirsiniz.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Relay hakkında daha fazla bilgi edinmek için şu bağlantıları ziyaret edin:

* [Microsoft. Azure. Relay başvurusu](/dotnet/api/microsoft.azure.relay)
* [Azure Relay nedir?](relay-what-is-it.md)
* [Kullanılabilir geçiş API 'Leri](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener
