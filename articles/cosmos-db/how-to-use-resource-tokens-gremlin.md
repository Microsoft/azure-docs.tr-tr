---
title: Gremlin SDK ile Azure Cosmos DB kaynak belirteçleri kullanma
description: Kaynak belirteçleri oluşturmayı ve grafik veritabanına erişmek için bunları kullanmayı öğrenin.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.openlocfilehash: 443b6ea2583c7c8a1c633cf1825e83cc02bd168c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756077"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Gremlin SDK ile Azure Cosmos DB kaynak belirteçleri kullanma

Bu makalede, Gremlin SDK aracılığıyla grafik veritabanına erişmek için [Azure Cosmos DB kaynak belirteçlerinin](secure-access-to-data.md) nasıl kullanılacağı açıklanmaktadır.

## <a name="create-a-resource-token"></a>Kaynak belirteci oluşturma

Apache TinkerPop Gremlin SDK 'sı, kaynak belirteçleri oluşturmak için kullanılacak bir API 'ye sahip değil. *Kaynak belirteci* terimi bir Azure Cosmos DB kavramıdır. Kaynak belirteçleri oluşturmak için [Azure Cosmos DB SDK 'sını](sql-api-sdk-dotnet.md)indirin. Uygulamanızın, grafik veritabanına erişmek için kaynak belirteçleri oluşturması ve bunları kullanması gerekiyorsa, iki ayrı SDK gerektirir.

Kaynak belirteçlerinin üzerindeki nesne modeli hiyerarşisi aşağıdaki ana hatlarıyla gösterilmiştir:

- **Azure Cosmos DB hesabı** -kendisiyle ILIŞKILI bir DNS içeren en üst düzey varlık (örneğin, `contoso.gremlin.cosmos.azure.com`).
  - **Azure Cosmos DB veritabanı**
    - **Kullanıcısını**
      - **Yetkisi**
        - **Belirteç** -izin verilen veya reddedilen eylemleri belirten bir izin nesnesi özelliği.

Kaynak belirteci şu biçimi kullanır: `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`. Bu dize, istemciler için opaktır ve değişiklik veya yorum olmadan olduğu gibi kullanılmalıdır.

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Kaynak belirteci kullanma
GremlinServer sınıfını oluştururken kaynak belirteçlerini doğrudan bir "parola" özelliği olarak kullanabilirsiniz.

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a master key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Aynı yaklaşım, tüm TinkerPop Gremlin SDK 'lerinde de geçerlidir.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Sınır

Tek bir Gremlin hesabıyla sınırsız sayıda belirteç verebilirsiniz. Ancak, 1 saat içinde aynı anda en fazla 100 belirteç kullanabilirsiniz. Bir uygulama, saat başına belirteç sınırını aşarsa, bir kimlik doğrulama isteği reddedilir ve şu hata iletisini alırsınız: "eşzamanlı olarak kullanılabilecek izin verilen kaynak belirteci sınırı 100 aşıldı." Yeni belirteçler için yuvaları serbest bırakmak üzere belirli belirteçleri kullanan etkin bağlantıları kapatmak için çalışmaz. Azure Cosmos DB Gremlin veritabanı altyapısı, kimlik doğrulama isteğinden hemen önce, saat boyunca benzersiz belirteçleri izler.

## <a name="permission"></a>İzin

Uygulamaların kaynak belirteçlerini kullanırken karşılaştığı yaygın bir hata, "karşılık gelen istek için yetkilendirme üstbilgisinde izin verilmedi. Lütfen başka bir yetkilendirme üstbilgisiyle yeniden deneyin. " Bu hata, Gremlin geçişi bir kenar veya köşe yazmaya çalıştığında, ancak kaynak belirteci yalnızca *okuma* izinleri verdiğinde döndürülür. Şu adımlardan herhangi birini içerip içermediğini görmek için çapraz geçiş bilgilerinizi inceleyin: *. addv ()* , *. Adde ()* , *. Drop ()* veya *. Property ()* .

## <a name="next-steps"></a>Sonraki adımlar
* Azure Cosmos DB [rol tabanlı erişim denetimi](role-based-access-control.md)
* Azure Cosmos DB [veri erişimini güvenli hale getirme hakkında bilgi edinin](secure-access-to-data.md)
