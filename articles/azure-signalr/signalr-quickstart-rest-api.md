---
title: Hızlı başlangıç-Azure SignalR hizmeti REST API
description: REST API Azure SignalR hizmeti ile aşağıdaki örnekleri kullanarak nasıl kullanacağınızı öğrenin. REST API belirtiminin ayrıntılarını bulun.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 17371e3bd426ea81b5e7e07610aac0073ea972c9
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74157688"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Hızlı Başlangıç: Konsol uygulamasından gerçek zamanlı iletiler yayımlama

Azure SignalR hizmeti, yayıncılık gibi sunucudan istemciye doğrudan iletişim senaryolarını desteklemek için [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)’sini sağlar. REST API çağrısı yapabilen herhangi bir programlama dilini seçebilirsiniz. Bağlı tüm istemcilere, adına göre belirli bir istemciye veya bir istemci grubuna iletiler gönderebilirsiniz.

Bu hızlı başlangıçta C# dilinde bir komut satırı uygulamasından bağlı istemci uygulamalarına nasıl ileti gönderebileceğinizi öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç; macOS, Windows veya Linux üzerinde çalıştırılabilir.

* [.NET Core SDK](https://www.microsoft.com/net/download/core)
* Tercih ettiğiniz bir metin veya kod düzenleyicisi.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure hesabınızla Azure portalında <https://portal.azure.com/> sayfasında oturum açın.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Hizmet dağıtılırken kodu hazırlamaya geçiş yapalım. [GitHub'dan örnek uygulamayı](https://github.com/aspnet/AzureSignalR-samples.git) kopyalayın, SignalR Hizmetinin bağlantı dizesini ayarlayın ve uygulamayı yerel olarak çalıştırın.

1. Bir git terminal penceresi açın. Örnek projeyi kopyalamak istediğiniz klasöre gidin.

1. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>Örneği derleme ve çalıştırma

Bu örnek, Azure SignalR Hizmetinin kullanımını gösteren bir konsol uygulamasıdır. İki mod sunar:

- Sunucu modu: Azure SignalR Hizmetinin REST API'sini çağırmak için basit komutlar kullanın.
- İstemci modu: Azure SignalR Hizmetine bağlanarak sunucudan ileti alın.

Ayrıca Azure SignalR Hizmeti ile kimlik doğrulaması için nasıl bir erişim belirteci oluşturacağınızı da öğrenebilirsiniz.

### <a name="build-the-executable-file"></a>Yürütülebilir dosyayı derleme

Örnek olarak macOS osx.10.13-x64 kullanıyoruz. Nasıl diğer platformlar için derleyeceğiniz hakkında [başvuru](https://docs.microsoft.com/dotnet/core/rid-catalog) belgeleri bulabilirsiniz.

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>İstemci başlatma

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Sunucu başlatma

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>Örneği yayımlamadan çalıştırma

Bir sunucu veya istemci başlatmak için aşağıdaki komutu da çalıştırabilirsiniz

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Bağlantı dizesini belirtmek için kullanıcı gizli dizileri kullanma

`dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` öğesini örneğin kök dizininde çalıştırabilirsiniz. Bundan sonra `-c "<ConnectionString>"` seçeneğine ihtiyacınız kalmaz.

## <a name="usage"></a>Kullanım

Sunucu başlatıldıktan sonra şu iletiyi göndermek için komutunu kullanın:

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Farklı istemci adları ile birden çok istemci başlatabilirsiniz.

## <a name="usage"> </a> Üçüncü taraf hizmetlerle tümleştirme

Azure SignalR hizmeti, üçüncü taraf hizmetlerin sistemle tümleştirilmesini sağlar.

### <a name="definition-of-technical-specifications"></a>Teknik belirtimlerin tanımı

Aşağıdaki tabloda, desteklenen tüm REST API sürümleri gösterilmektedir. Belirli bir sürüme ait tanım dosyasını da bulabilirsiniz

Sürüm | API Durumu | Kapı | Özel
--- | --- | --- | ---
`1.0-preview` | Kullanılabilir | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Kullanılabilir | Standart | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

Her sürüm için kullanılabilir API'lerin listesi aşağıda verilmiştir.

API | `1.0-preview` | `1.0`
--- | --- | ---
[Tümüne yayınla](#broadcast) | **&#x2713;** | **&#x2713;**
[Gruba yayınla](#broadcast-group) | **&#x2713;** | **&#x2713;**
Bazı gruplara yayın | **&#x2713;** Kullanım dışı | `N / A`
[Belirli kullanıcılara gönder](#send-user) | **&#x2713;** | **&#x2713;**
Bazı kullanıcılara gönder | **&#x2713;** Kullanım dışı | `N / A`
[Gruba kullanıcı ekleme](#add-user-to-group) | `N / A` | **&#x2713;**
[Gruptan kullanıcı kaldırma](#remove-user-from-group) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Herkese yayınlama

Sürüm | API HTTP Yöntemi | İstek URL'si | İstek gövdesi
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | Yukarıdakiyle aynı

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Gruba yayınla

Sürüm | API HTTP Yöntemi | İstek URL'si | İstek gövdesi
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | Yukarıdakiyle aynı

<a name="send-user"> </a>
### <a name="sending-to-specific-users"></a>Belirli kullanıcılara gönderme

Sürüm | API HTTP Yöntemi | İstek URL'si | İstek gövdesi
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Yukarıdakiyle aynı

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Gruba kullanıcı ekleme

Sürüm | API HTTP Yöntemi | İstek URL'si
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Gruptan kullanıcı kaldırma

Sürüm | API HTTP Yöntemi | İstek URL'si
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, SignalR hizmetinden istemcilere gerçek zamanlı ileti yayınlamak için REST API kullanmayı öğrendiniz. Daha sonra, REST API üzerine inşa olan SignalR hizmeti bağlamasıyla Azure Işlevleri geliştirme ve dağıtma hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure SignalR hizmeti bağlamalarını kullanarak Azure Işlevleri geliştirme](signalr-quickstart-azure-functions-csharp.md)
