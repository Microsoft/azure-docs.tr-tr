---
title: Azure Relay node API 'Lerine genel bakış | Microsoft Docs
description: Bu makalede Azure Relay hizmeti için Node.js API 'sine genel bakış sunulmaktadır. Ayrıca HYCO-WS düğüm paketinin nasıl kullanılacağını gösterir.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 558f49c09203192ff4cbb1af392eaeef8d705c94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91263496"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Geçiş Karma Bağlantılar node API 'sine genel bakış

## <a name="overview"></a>Genel Bakış

[`hyco-ws`](https://www.npmjs.com/package/hyco-ws)Azure Relay karma bağlantılar için düğüm paketi kurulmuştur ve [' ws '](https://www.npmjs.com/package/ws) NPM paketini genişletiyor. Bu paket, bu temel paketin tüm dışarı aktarmaları yeniden dışa aktarır ve Azure Relay hizmeti Karma Bağlantılar özelliğiyle tümleştirmeyi sağlayan yeni dışarı aktarmalar ekler. 

`require('ws')` `require('hyco-ws')` Aynı zamanda bir uygulamanın WebSocket bağlantılarını "güvenlik duvarının içinde" ve karma bağlantılar aracılığıyla yerel olarak dinleyebileceği hibrit senaryoları da sağlayan, bu paketi ile kullanılabilecek mevcut uygulamalar.
  
## <a name="documentation"></a>Belgeler

API 'Ler [Ana ' ws ' paketinde belgelenmiştir](https://github.com/websockets/ws/blob/master/doc/ws.md). Bu makalede bu paketin Bu taban çizgisinden nasıl farklı olduğu açıklanır. 

Temel paket ile bu ' HYCO-ws ' arasındaki temel farklılıklar yeni bir sunucu sınıfı eklemektedir, aracılığıyla dışarıya aktarılabilir `require('hyco-ws').RelayedServer` ve birkaç yardımcı yöntem.

### <a name="package-helper-methods"></a>Paket Yardımcısı yöntemleri

Paket dışarı aktarmasına aşağıdaki şekilde başvurulacağını sağlayan birkaç yardımcı program yöntemi vardır:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Yardımcı yöntemler bu paketle kullanım içindir, ancak Web veya cihaz istemcilerinin dinleyici veya gönderici oluşturmalarına olanak tanımak için bir düğüm sunucusu tarafından da kullanılabilir. Sunucu, kısa süreli belirteçleri katıştırayan URI 'Leri geçirerek bu yöntemleri kullanır. Bu URI 'Ler Ayrıca WebSocket el sıkışması için HTTP üstbilgileri ayarlamayı desteklemeyen ortak WebSocket yığınları ile birlikte kullanılabilir. Birincil olarak bu kitaplık dış kullanım senaryolarında yetkilendirme belirteçlerini URI 'ye katıştırma desteklenir. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Verilen ad alanı ve yol için geçerli bir Azure Relay karma bağlantı dinleyicisi URI 'SI oluşturur. Bu URI daha sonra WebSocketServer sınıfının geçiş sürümüyle birlikte kullanılabilir.

- `namespaceName` (gerekli)-kullanılacak Azure Relay ad alanının etki alanı nitelikli adı.
- `path` (gerekli)-Bu ad alanındaki mevcut Azure Relay karma bağlantısının adı.
- `token` (isteğe bağlı)-önceden verilen bir geçiş erişim belirteci, dinleyici URI 'sine katıştırılır (aşağıdaki örneğe bakın).
- `id` (isteğe bağlı)-isteklerin uçtan uca tanılama izlemesini sağlayan bir izleme tanımlayıcısı.

`token`Değer isteğe bağlıdır ve yalnızca, bu, W3C WebSocket yığınında olduğu gibi, WebSocket el sıkışması ile bırlıkte http üstbilgileri gönderilmesi mümkün olmadığında kullanılmalıdır.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Verilen ad alanı ve yol için geçerli Azure Relay karma bağlantı URI 'SI oluşturur. Bu URI, herhangi bir WebSocket istemcisiyle birlikte kullanılabilir.

- `namespaceName` (gerekli)-kullanılacak Azure Relay ad alanının etki alanı nitelikli adı.
- `path` (gerekli)-Bu ad alanındaki mevcut Azure Relay karma bağlantısının adı.
- `token` (isteğe bağlı)-önceden verilen bir geçiş erişim belirteci gönderme URI 'sine katıştırılır (aşağıdaki örneğe bakın).
- `id` (isteğe bağlı)-isteklerin uçtan uca tanılama izlemesini sağlayan bir izleme tanımlayıcısı.

`token`Değer isteğe bağlıdır ve yalnızca, bu, W3C WebSocket yığınında olduğu gibi, WebSocket el sıkışması ile bırlıkte http üstbilgileri gönderilmesi mümkün olmadığında kullanılmalıdır.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Verilen hedef URI, SAS kuralı ve SAS kural anahtarı için, süre sonu bağımsız değişkeni atlanırsa belirtilen saniye veya geçerli hızlı bir saat için geçerli olan bir Azure Relay paylaşılan erişim Imzası (SAS) belirteci oluşturur.

- `uri` (gerekli)-belirtecin verildiği URI. URI, HTTP şemasını kullanmak için normalleştirilir ve sorgu dizesi bilgileri çıkarılır.
- `ruleName` (gerekli)-belirtilen URI tarafından temsil edilen varlık ya da URI konak bölümü tarafından temsil edilen ad alanı için SAS kural adı.
- `key` (gerekli)-SAS kuralı için geçerli anahtar. 
- `expirationSeconds` (isteğe bağlı)-oluşturulan belirtecin süresi dolmadan önce geçmesi gereken saniye sayısı. Belirtilmemişse, varsayılan değer 1 saattir (3600).

Verilen belirteç verilen süre için belirtilen SAS kuralıyla ilişkili hakları bir şekilde çözün.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Bu yöntem, `createRelayToken` daha önce belgelenen yönteme işlevsel olarak eşdeğerdir, ancak GIRIŞ URI 'sine doğru şekilde eklenmiş olan belirteci döndürür.

### <a name="class-wsrelayedserver"></a>Sınıf WS. RelayedServer

`hycows.RelayedServer`Sınıfı, `ws.Server` yerel ağ üzerinde dinlemede olmayan ancak Azure Relay hizmetine dinleme yapan sınıfa bir alternatiftir.

İki sınıf çoğunlukla, sınıf kullanan mevcut bir uygulamanın `ws.Server` , geçişli sürümü kullanmak üzere kolayca değiştirilebileceği anlamına gelir. Ana farklılıklar oluşturucuda ve kullanılabilir seçeneklerde bulunur.

#### <a name="constructor"></a>Oluşturucu  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

`RelayedServer`Oluşturucu, `Server` tek başına dinleyici olmadığından veya var olan bir http dinleyicisi çerçevesine katıştırılabildiğinden, öğesinden farklı bir bağımsız değişken kümesini destekler. WebSocket yönetimi büyük ölçüde geçiş hizmeti için temsilci olarak oluşturulduğundan daha az seçenek mevcuttur.

Oluşturucu bağımsız değişkenleri:

- `server` (gerekli)-genellikle WebSocket. createRelayListenUri () yardımcı yöntemiyle oluşturulan bir karma bağlantı adı için tam URI.
- `token` (gerekli)-bu bağımsız değişken, önceden verilen bir belirteç dizesini veya böyle bir belirteç dizesini elde etmek için çağrılabilecek bir geri çağırma işlevini barındırır. Belirteç yenilemeyi sağladığından geri çağırma seçeneği tercih edilir.

#### <a name="events"></a>Ekinlikler

`RelayedServer` örnekler, gelen istekleri işleyebilmeniz, bağlantılar oluşturmanız ve hata koşullarını algılamanıza olanak tanıyan üç olay yayar. `connect`İletileri işlemek için olaya abone olmanız gerekir. 

##### <a name="headers"></a>bilgisinde

```JavaScript 
function(headers)
```

`headers`Olay, gelen bir bağlantı kabul edilmeden önce oluşturulur ve bu, istemciye gönderilmek üzere üstbilgilerin değiştirilmesini sağlar. 

##### <a name="connection"></a>bağlantı

```JavaScript
function(socket)
```

Yeni bir WebSocket bağlantısı kabul edildiğinde yayılır. Nesne `ws.WebSocket` , temel paket ile aynı türde.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Temeldeki sunucu bir hata yayar, buraya iletilir.  

#### <a name="helpers"></a>Yardımcı

Bir geçişli sunucu başlatmayı kolaylaştırmak ve gelen bağlantılara hemen abone olmak için, paket aşağıdaki gibi örneklerde de kullanılan basit bir yardımcı işlevi kullanıma sunar:

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Bu yöntem, RelayedServer yeni bir örneğini oluşturmak için oluşturucuyu çağırır ve ardından belirtilen geri aramayı ' bağlantı ' olayına abone olur.
 
##### <a name="relayedconnect"></a>relayedConnect

`createRelayedServer`İşlev içindeki yardımcıyı yansıtırken, `relayedConnect` bir istemci bağlantısı oluşturur ve elde edilen yuvada ' Open ' olayına abone olur.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>Sonraki adımlar
Azure Relay hakkında daha fazla bilgi edinmek için şu bağlantıları ziyaret edin:
* [Azure Relay nedir?](relay-what-is-it.md)
* [Kullanılabilir geçiş API 'Leri](relay-api-overview.md)
