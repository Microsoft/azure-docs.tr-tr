---
title: "Hızlı başlangıç: Java 'da Redsıs için Azure önbelleğini kullanma"
description: Bu hızlı başlangıçta, Redsıs için Azure önbelleği kullanan yeni bir Java uygulaması oluşturacaksınız
author: yegu-ms
ms.author: yegu
ms.date: 05/22/2020
ms.topic: quickstart
ms.service: cache
ms.devlang: java
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: 7128ff1e20439c57d3e6212f29e6f871997584c6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538617"
---
# <a name="quickstart-use-azure-cache-for-redis-in-java"></a>Hızlı başlangıç: Java 'da Redsıs için Azure önbelleğini kullanma

Bu hızlı başlangıçta, Azure 'daki herhangi bir uygulamadan erişilebilen güvenli, özel bir önbelleğe erişim sağlamak için [Jedsıs](https://github.com/xetorthio/jedis) redsıs Istemcisini kullanarak redsıs Için Azure önbelleğini bir Java uygulamasına dahil edersiniz.

## <a name="skip-to-the-code-on-github"></a>GitHub 'daki koda atlayın

Koda doğrudan atlamak istiyorsanız GitHub 'da [Java hızlı başlangıç](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/java) bölümüne bakın.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- [Apache Maven](https://maven.apache.org/download.cgi)

## <a name="create-an-azure-cache-for-redis"></a>Redis için Azure Cache oluşturma

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

## <a name="setting-up-the-working-environment"></a>Çalışma ortamını ayarlama 

İşletim sisteminize bağlı olarak, **ana bilgisayar adınız** ve **birincil erişim anahtarınız** için ortam değişkenleri ekleyin. Bir komut istemi veya bir Terminal penceresi açın ve aşağıdaki değerleri ayarlayın:

```CMD 
set REDISCACHEHOSTNAME=<YOUR_HOST_NAME>.redis.cache.windows.net
set REDISCACHEKEY=<YOUR_PRIMARY_ACCESS_KEY>
```

```bash
export REDISCACHEHOSTNAME=<YOUR_HOST_NAME>.redis.cache.windows.net
export REDISCACHEKEY=<YOUR_PRIMARY_ACCESS_KEY>
```

Yer tutucuları aşağıdaki değerlerle değiştirin:

- `<YOUR_HOST_NAME>`: Azure portal, Reddir kaynağı için Azure önbelleğinizin *Özellikler* bölümünden elde edilen DNS ana bilgisayar adı.
- `<YOUR_PRIMARY_ACCESS_KEY>`: Azure portal, Reddir kaynağı için Azure önbelleğinizin *erişim tuşları* bölümünden elde edilen birincil erişim anahtarı.

## <a name="create-a-new-java-app"></a>Yeni bir Java uygulaması oluşturma

Maven kullanarak yeni bir quickstart uygulaması oluşturun:

```CMD
mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.3 -DgroupId=example.demo -DartifactId=redistest -Dversion=1.0
```

Yeni *redistest* proje dizinine geçin.

*Pom.xml* dosyasını açın ve [Jedis](https://github.com/xetorthio/jedis) için bağımlılık ekleyin:

```xml
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>3.2.0</version>
        <type>jar</type>
        <scope>compile</scope>
    </dependency>
```

*pom.xml* dosyasını kaydedin.

*App.java*'yı açın ve kodu aşağıdaki kodla değiştirin:

```java
package example.demo;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisShardInfo;

/**
 * Redis test
 *
 */
public class App 
{
    public static void main( String[] args )
    {

        boolean useSsl = true;
        String cacheHostname = System.getenv("REDISCACHEHOSTNAME");
        String cachekey = System.getenv("REDISCACHEKEY");

        // Connect to the Azure Cache for Redis over the TLS/SSL port using the key.
        JedisShardInfo shardInfo = new JedisShardInfo(cacheHostname, 6380, useSsl);
        shardInfo.setPassword(cachekey); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);      

        // Perform cache operations using the cache connection object...

        // Simple PING command        
        System.out.println( "\nCache Command  : Ping" );
        System.out.println( "Cache Response : " + jedis.ping());

        // Simple get and put of integral data types into the cache
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        System.out.println( "\nCache Command  : SET Message" );
        System.out.println( "Cache Response : " + jedis.set("Message", "Hello! The cache is working from Java!"));

        // Demonstrate "SET Message" executed as expected...
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        // Get the client list, useful to see if connection list is growing...
        System.out.println( "\nCache Command  : CLIENT LIST" );
        System.out.println( "Cache Response : " + jedis.clientList());

        jedis.close();
    }
}
```

Bu kod, önbellek ana bilgisayar adı ve anahtar ortam değişkenlerini kullanarak Redsıs örneği için bir Azure önbelleğine bağlanmayı gösterir. Kod ayrıca önbellekte bir dize değeri depolar ve alır. Ayrıca `PING` ve `CLIENT LIST` komutları da yürütüldü. 

*App.java*'yı kaydedin.

## <a name="build-and-run-the-app"></a>Uygulamayı derleyin ve çalıştırın

Uygulamayı derlemek ve çalıştırmak için aşağıdaki Maven komutunu yürütün:

```CMD
mvn compile
mvn exec:java -D exec.mainClass=example.demo.App
```

Aşağıdaki örnekte, `Message` anahtarının Azure portalındaki Redis Konsolu kullanılarak ayarlanan, önceden önbelleğe alınmış bir değer içerdiğini görebilirsiniz. Uygulama, önbelleğe alınan bu değeri güncelleştirdi. Ayrıca uygulama, `PING` ve `CLIENT LIST` komutlarını da yürüttü.

![Redsıs uygulaması için Azure önbelleği tamamlandı](./media/cache-java-get-started/azure-cache-redis-complete.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki öğreticiyle devam edecekseniz, bu hızlı başlangıçta oluşturulan kaynakları tutabilir ve sonraki öğreticide yeniden kullanabilirsiniz.

Aksi takdirde, hızlı başlangıç örnek uygulamasını tamamladıysanız ücret yansıtılmaması için bu hızlı başlangıçta oluşturulan Azure kaynaklarını silebilirsiniz. 

> [!IMPORTANT]
> Bir kaynak grubunu silme işlemi geri alınamaz ve kaynak grubunun ve içindeki tüm kaynaklar kalıcı olarak silinir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun. Bu örneği, tutmak istediğiniz kaynakları içeren mevcut bir kaynak grubunda barındırmak için kaynaklar oluşturduysanız, kaynak grubunu silmek yerine her kaynağı kendi ilgili dikey penceresinden tek tek silebilirsiniz.
>

1. [Azure portalda](https://portal.azure.com) oturum açın ve **Kaynak grupları**’nı seçin.

1. **Ada göre filtrele** metin kutusuna kaynak grubunuzun adını yazın. Bu makaledeki yönergelerde *TestResources* adlı bir kaynak grubu kullanılmıştır. Sonuç listesindeki kaynak grubunuzda **.** .. öğesini seçin ve **kaynak grubunu silin**.

   ![Azure Kaynak grubu silindi](./media/cache-java-get-started/azure-cache-redis-delete-resource-group.png)

1. Kaynak grubunun silinmesini onaylamanız istenir. Onaylamak için kaynak grubunuzun adını yazın ve **Sil**' i seçin.

Birkaç dakika sonra kaynak grubu ve içerdiği kaynakların tümü silinir.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Java uygulamasından Redsıs için Azure önbelleğini nasıl kullanacağınızı öğrendiniz. Bir ASP.NET Web uygulamasıyla Redsıs için Azure önbelleği 'ni kullanmak üzere bir sonraki hızlı başlangıca devam edin.

> [!div class="nextstepaction"]
> [Redsıs için Azure önbelleği kullanan bir ASP.NET Web uygulaması oluşturun.](./cache-web-app-howto.md)
