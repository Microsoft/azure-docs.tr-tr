---
title: 'Hızlı başlangıç: node. js ile Redsıs için Azure önbelleğini kullanma'
description: Bu hızlı başlangıçta, Node. js ve node_redis ile redin için Azure önbelleğini nasıl kullanacağınızı öğreneceksiniz.
author: yegu-ms
ms.service: cache
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2018
ms.author: yegu
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: e0458fd257942a455daef911a303437fea03b11b
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122017"
---
# <a name="quickstart-use-azure-cache-for-redis-with-nodejs"></a>Hızlı başlangıç: node. js ile Redsıs için Azure önbelleğini kullanma

Bu hızlı başlangıçta, Azure 'daki herhangi bir uygulamadan erişilebilen güvenli, özel bir önbelleğe erişim sağlamak üzere redin için Azure önbelleğini bir Node. js uygulamasına katabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- [node_redis](https://github.com/mranney/node_redis)komut `npm install redis`ile yükleyebilirsiniz. 

Diğer Node.js istemcilerini kullanmaya ilişkin örnekler için [Node.js Redis istemcileri](https://redis.io/clients#nodejs) listesindeki Node.js istemcilerinin kendi belgelerine bakın.

## <a name="create-a-cache"></a>Bir önbellek oluşturma
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]


**ANA BİLGİSAYAR ADI** ve **Birincil** erişim anahtarınız için ortam değişkenleri ekleyin. Hassas bilgileri kodunuza doğrudan eklemek yerine kodunuzdaki bu değişkenleri kullanacaksınız.

```
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="connect-to-the-cache"></a>Önbelleğe bağlanma

[Node_redis](https://github.com/mranney/node_redis) en son yapıları, Reda Için Azure Cache 'e SSL kullanarak bağlanma desteği sağlar. Aşağıdaki örnek, 6380 SSL uç noktasını kullanarak Reda için Azure Cache 'e bağlanmayı gösterir. 

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380, process.env.REDISCACHEHOSTNAME,
    {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
```

Kodunuzdaki her işlem için yeni bağlantı oluşturmayın. Bunun yerine, mümkün olduğunca bağlantıları yeniden kullanın. 

## <a name="create-a-new-nodejs-app"></a>Yeni bir Node.js uygulaması oluşturma

*redistest.js* adlı yeni bir betik dosyası oluşturun.

Aşağıdaki örnek JavaScript’i dosyaya ekleyin. Bu kod, önbellek ana bilgisayar adı ve anahtar ortam değişkenlerini kullanarak Redsıs örneği için bir Azure önbelleğine bağlanmayı gösterir. Kod ayrıca önbellekte bir dize değeri depolar ve alır. Ayrıca `PING` ve `CLIENT LIST` komutları da yürütüldü. Redis’i [node_redis](https://github.com/mranney/node_redis) istemcisiyle kullanmaya ilişkin daha fazla örnek için bkz. [https://redis.js.org/](https://redis.js.org/).

```js
var redis = require("redis");
var bluebird = require("bluebird");

bluebird.promisifyAll(redis.RedisClient.prototype);
bluebird.promisifyAll(redis.Multi.prototype);

async function testCache() {

    // Connect to the Azure Cache for Redis over the SSL port using the key.
    var cacheConnection = redis.createClient(6380, process.env.REDISCACHEHOSTNAME, 
        {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
        
    // Perform cache operations using the cache connection object...

    // Simple PING command
    console.log("\nCache command: PING");
    console.log("Cache response : " + await cacheConnection.pingAsync());

    // Simple get and put of integral data types into the cache
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    console.log("\nCache command: SET Message");
    console.log("Cache response : " + await cacheConnection.setAsync("Message",
        "Hello! The cache is working from Node.js!"));    

    // Demonstrate "SET Message" executed as expected...
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    // Get the client list, useful to see if connection list is growing...
    console.log("\nCache command: CLIENT LIST");
    console.log("Cache response : " + await cacheConnection.clientAsync("LIST"));    
}

testCache();
```

Node.js ile betiği çalıştırın.

```
node redistest.js
```

Aşağıdaki örnekte, `Message` anahtarının Azure portaldaki Redis Konsolu kullanılarak ayarlanan, önceden önbelleğe alınmış bir değer içerdiğini görebilirsiniz. Uygulama, önbelleğe alınan bu değeri güncelleştirdi. Ayrıca uygulama, `PING` ve `CLIENT LIST` komutlarını da yürüttü.

![Redis Cache uygulama tamamlandı](./media/cache-nodejs-get-started/redis-cache-app-complete.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki öğreticiyle devam edecekseniz, bu hızlı başlangıçta oluşturulan kaynakları tutabilir ve sonraki öğreticide yeniden kullanabilirsiniz.

Aksi takdirde, hızlı başlangıç örnek uygulamasını tamamladıysanız ücret yansıtılmaması için bu hızlı başlangıçta oluşturulan Azure kaynaklarını silebilirsiniz. 

> [!IMPORTANT]
> Bir kaynak grubunu silme işlemi geri alınamaz ve kaynak grubunun ve içindeki tüm kaynaklar kalıcı olarak silinir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun. Bu örneği, tutmak istediğiniz kaynakları içeren mevcut bir kaynak grubunda barındırmak için kaynaklar oluşturduysanız, kaynak grubunu silmek yerine her kaynağı kendi ilgili dikey penceresinden tek tek silebilirsiniz.
>

[Azure portalında](https://portal.azure.com) oturum açın ve **Kaynak grupları**’nı seçin.

**Ada göre filtrele** metin kutusuna kaynak grubunuzun adını girin. Bu makaledeki yönergelerde *TestResources* adlı bir kaynak grubu kullanılmıştır. Sonuç listesindeki kaynak grubunuzda **.** .. öğesini seçin ve **kaynak grubunu silin**.

![Azure kaynak grubunu sil](./media/cache-nodejs-get-started/redis-cache-delete-resource-group.png)

Kaynak grubunun silinmesini onaylamanız istenir. Onaylamak için kaynak grubunuzun adını girin ve **Sil**' i seçin.

Birkaç dakika sonra kaynak grubu ve içerdiği kaynakların tümü silinir.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Node. js uygulamasından Redsıs için Azure önbelleğini nasıl kullanacağınızı öğrendiniz. Bir ASP.NET Web uygulamasıyla Redsıs için Azure önbelleği 'ni kullanmak üzere bir sonraki hızlı başlangıca devam edin.

> [!div class="nextstepaction"]
> [Redsıs için Azure önbelleği kullanan bir ASP.NET Web uygulaması oluşturun.](./cache-web-app-howto.md)
