---
title: Hızlı başlangıç-Azure SignalR hizmeti REST API
description: REST API Azure SignalR hizmeti ile aşağıdaki örnekleri kullanarak nasıl kullanacağınızı öğrenin. REST API belirtiminin ayrıntılarını bulun.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: dfcbb00ec20797248f41cc1676809f3198d51527
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866170"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Hızlı Başlangıç: Konsol uygulamasından gerçek zamanlı iletiler yayımlama

Azure SignalR hizmeti, yayıncılık gibi sunucudan istemciye doğrudan iletişim senaryolarını desteklemek için [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)’sini sağlar. REST API çağrısı yapabilen herhangi bir programlama dilini seçebilirsiniz. Bağlı tüm istemcilere, adına göre belirli bir istemciye veya bir istemci grubuna iletiler gönderebilirsiniz.

Bu hızlı başlangıçta C# dilinde bir komut satırı uygulamasından bağlı istemci uygulamalarına nasıl ileti gönderebileceğinizi öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç; macOS, Windows veya Linux üzerinde çalıştırılabilir.

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* Tercih ettiğiniz bir metin veya kod düzenleyicisi.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsapi).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure hesabınızla Azure portalında <https://portal.azure.com/> sayfasında oturum açın.

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsapi).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsapi).

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Hizmet dağıtılırken kodu hazırlamaya geçiş yapalım. [GitHub'dan örnek uygulamayı](https://github.com/aspnet/AzureSignalR-samples.git) kopyalayın, SignalR Hizmetinin bağlantı dizesini ayarlayın ve uygulamayı yerel olarak çalıştırın.

1. Bir git terminal penceresi açın. Örnek projeyi kopyalamak istediğiniz klasöre gidin.

1. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```
Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsapi).

## <a name="build-and-run-the-sample"></a>Örneği derleme ve çalıştırma

Bu örnek, Azure SignalR Hizmetinin kullanımını gösteren bir konsol uygulamasıdır. İki mod sunar:

- Sunucu modu: Azure SignalR Hizmetinin REST API'sini çağırmak için basit komutlar kullanın.
- İstemci modu: Azure SignalR Hizmetine bağlanarak sunucudan ileti alın.

Ayrıca Azure SignalR Hizmeti ile kimlik doğrulaması için nasıl bir erişim belirteci oluşturacağınızı da öğrenebilirsiniz.

### <a name="build-the-executable-file"></a>Yürütülebilir dosyayı derleme

Örnek olarak macOS osx.10.13-x64 kullanıyoruz. Nasıl diğer platformlar için derleyeceğiniz hakkında [başvuru](/dotnet/core/rid-catalog) belgeleri bulabilirsiniz.

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

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsapi).

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

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsapi).

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

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsapi).

## <a name="integration-with-third-party-services"></a><a name="usage"> </a> Üçüncü taraf hizmetlerle tümleştirme

Azure SignalR hizmeti, üçüncü taraf hizmetlerin sistemle tümleştirilmesini sağlar.

### <a name="definition-of-technical-specifications"></a> Teknik özelliklerin tanımı

Aşağıdaki tabloda, desteklenen tüm REST API sürümleri gösterilmektedir. Belirli bir sürüme ait tanım dosyasını da bulabilirsiniz

Sürüm | API Durumu | Kapı | Özel
--- | --- | --- | ---
`1.0-preview` | Kullanılabilir | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Kullanılabilir | Standart | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

Her sürüm için kullanılabilir API'lerin listesi aşağıda verilmiştir.

API | 1,0-Önizleme | 1.0
--- | --- | ---
[Tümüne yayınla](#broadcast) | **&#x2713;** | **&#x2713;**
[Gruba yayınla](#broadcast-group) | **&#x2713;** | **&#x2713;**
Bazı gruplara yayın | **&#x2713;** (kullanım dışı) | `N / A`
[Kullanıcıya gönder](#send-user) | **&#x2713;** | **&#x2713;**
Bazı kullanıcılara gönder | **&#x2713;** (kullanım dışı) | `N / A`
[Gruba kullanıcı ekleme](#add-user-to-group) | `N / A` | **&#x2713;**
[Gruptan kullanıcı kaldırma](#remove-user-from-group) | `N / A` | **&#x2713;**
[Kullanıcı varlığını denetle](#check-user-existence) | `N / A` | **&#x2713;**
[Kullanıcıları tüm gruplardan kaldır](#remove-user-from-all-groups) | `N / A` | **&#x2713;**
[Bir bağlantıya gönder](#send-connection) | `N / A` | **&#x2713;**
[Gruba bağlantı ekleme](#add-connection-to-group) | `N / A` | **&#x2713;**
[Bir gruptan bağlantı kaldırma](#remove-connection-from-group) | `N / A` | **&#x2713;**
[İstemci bağlantısını kapatma](#close-connection) | `N / A` | **&#x2713;**
[Hizmet Durumu](#service-health) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Herkese yayınlama

Sürüm | API HTTP Yöntemi | İstek URL’si | İstek gövdesi
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | Yukarıdakiyle aynı

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Gruba yayınla

Sürüm | API HTTP Yöntemi | İstek URL’si | İstek gövdesi
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | Yukarıdakiyle aynı

<a name="send-user"> </a>
### <a name="sending-to-a-user"></a>Kullanıcıya gönderme

Sürüm | API HTTP Yöntemi | İstek URL’si | İstek gövdesi
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Yukarıdakiyle aynı

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Gruba kullanıcı ekleme

Sürüm | API HTTP Yöntemi | İstek URL’si
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Gruptan kullanıcı kaldırma

Sürüm | API HTTP Yöntemi | İstek URL’si
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="check-user-existence"> </a>
### <a name="check-user-existence-in-a-group"></a>Bir gruptaki Kullanıcı varlığını denetleme

API Sürümü | API HTTP Yöntemi | İstek URL’si
---|---|---
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups/<group-name>`
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>` 

Yanıt Durum Kodu | Description
---|---
`200` | Kullanıcı var
`404` | Kullanıcı yok

<a name="remove-user-from-all-groups"> </a>
### <a name="remove-a-user-from-all-groups"></a>Kullanıcıları tüm gruplardan kaldır

API Sürümü | API HTTP Yöntemi | İstek URL’si
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups`

<a name="send-connection"> </a>
### <a name="send-message-to-a-connection"></a>Bir bağlantıya ileti gönderin

API Sürümü | API HTTP Yöntemi | İstek URL’si | İstek Gövdesi
---|---|---|---
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>` | `{ "target":"<method-name>", "arguments":[ ... ] }`

<a name="add-connection-to-group"> </a>
### <a name="add-a-connection-to-a-group"></a>Gruba bağlantı ekleme

API Sürümü | API HTTP Yöntemi | İstek URL’si
---|---|---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="remove-connection-from-group"> </a>
### <a name="remove-a-connection-from-a-group"></a>Bir gruptan bağlantı kaldırma

API Sürümü | API HTTP Yöntemi | İstek URL’si
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="close-connection"> </a>
### <a name="close-a-client-connection"></a>İstemci bağlantısını kapatma

API Sürümü | API HTTP Yöntemi | İstek URL’si
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>?reason=<close-reason>`

<a name="service-health"> </a>
### <a name="service-health"></a>Hizmet Durumu

API Sürümü | API HTTP Yöntemi | İstek URL’si
---|---|---                             
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/health`

Yanıt Durum Kodu | Description
---|---
`200` | Hizmet Iyi
`5xx` | Hizmet hatası

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsapi).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Sorun mu yaşıyorsunuz? [Sorun giderme kılavuzunu](signalr-howto-troubleshoot-guide.md) deneyin veya [bize bilgi verin](https://aka.ms/asrs/qsapi).

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, SignalR hizmetinden istemcilere gerçek zamanlı ileti yayınlamak için REST API kullanmayı öğrendiniz. Daha sonra, REST API üzerine inşa olan SignalR hizmeti bağlamasıyla Azure Işlevleri geliştirme ve dağıtma hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure SignalR hizmeti bağlamalarını kullanarak Azure Işlevleri geliştirme](signalr-quickstart-azure-functions-csharp.md)
