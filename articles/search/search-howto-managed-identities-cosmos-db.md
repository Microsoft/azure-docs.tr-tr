---
title: Yönetilen kimlik kullanarak Cosmos DB hesabıyla bağlantı kurma
titleSuffix: Azure Cognitive Search
description: Yönetilen kimlik kullanarak Cosmos DB hesaba Dizin Oluşturucu bağlantısı ayarlamayı öğrenin
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 2a1744feedc3e0ffae6cf2cd45cd090a6c2f06d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422102"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity"></a>Yönetilen kimlik kullanarak Cosmos DB veritabanına Dizin Oluşturucu bağlantısı kurma

Bu sayfada, veri kaynağı nesne bağlantı dizesinde kimlik bilgileri sağlamak yerine yönetilen kimlik kullanarak bir Azure Cosmos DB veritabanına Dizin Oluşturucu bağlantısının nasıl ayarlanacağı açıklanır.

Bu özellik hakkında daha fazla bilgi edinmek için, bir dizin oluşturucunun ne olduğu ve veri kaynağınız için dizin oluşturucunun nasıl ayarlanacağı hakkında bilgi sahibi olmanız önerilir. Aşağıdaki bağlantılarda daha fazla bilgi bulabilirsiniz:

* [Dizin Oluşturucu’ya genel bakış](search-indexer-overview.md)
* [Azure Cosmos DB dizinleyici](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Yönetilen kimlik kullanarak bağlantı kurma

### <a name="1---turn-on-system-assigned-managed-identity"></a>1-sistem tarafından atanan yönetilen kimliği aç

Sistem tarafından atanan bir yönetilen kimlik etkinleştirildiğinde Azure, arama hizmetiniz için aynı kiracı ve abonelik içindeki diğer Azure hizmetlerinde kimlik doğrulaması yapmak üzere kullanılabilecek bir kimlik oluşturur. Daha sonra bu kimliği, dizin oluşturma sırasında verilere erişime izin veren Azure rol tabanlı erişim denetimi (Azure RBAC) atamaları içinde kullanabilirsiniz.

![Sistem tarafından atanan yönetilen kimliği aç](./media/search-managed-identities/turn-on-system-assigned-identity.png "Sistem tarafından atanan yönetilen kimliği aç")

**Kaydet** ' i seçtikten sonra, arama hizmetinize atanmış BIR nesne kimliği görürsünüz.

![Nesne kimliği](./media/search-managed-identities/system-assigned-identity-object-id.png "Nesne kimliği")
 
### <a name="2---add-a-role-assignment"></a>2-rol ataması ekleme

Bu adımda, Azure Bilişsel Arama hizmetine, Cosmos DB veritabanınızdaki verileri okuma izni verirsiniz.

1. Azure portal, dizine eklemek istediğiniz verileri içeren Cosmos DB hesabına gidin.
2. **Erişim denetimi (IAM)** öğesini seçin
3. **Ekle** ' yi seçin ve **rol ataması Ekle**

    ![Rol ataması ekle](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Rol ataması ekle")

4. **Cosmos DB hesap okuyucu rolünü** seçin
5. **Azure AD kullanıcısı, Grup veya hizmet sorumlusu** olarak **erişim atamasını** bırak
6. Arama hizmetinizi arayın, seçin ve ardından **Kaydet** ' i seçin.

    ![Okuyucu ve veri erişimi rol ataması ekleme](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Okuyucu ve veri erişimi rol ataması ekleme")

### <a name="3---create-the-data-source"></a>3-veri kaynağını oluşturma

[REST API](/rest/api/searchservice/create-data-source), Azure Portal ve [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) , yönetilen kimlik bağlantı dizesini destekler. Aşağıda, [REST API](/rest/api/searchservice/create-data-source) ve yönetilen kimlik bağlantı dizesi kullanılarak Cosmos DB veri dizini oluşturmak için bir veri kaynağı oluşturma örneği verilmiştir. Yönetilen kimlik bağlantı dizesi biçimi REST API, .NET SDK ve Azure portal için aynıdır.

Kimlik doğrulaması için Yönetilen kimlikler kullanıldığında, **kimlik bilgileri** hesap anahtarı içermez.

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

İsteğin gövdesi, aşağıdaki alanları içermesi gereken veri kaynağı tanımını içerir:

| Alan   | Açıklama |
|---------|-------------|
| **ada** | Gereklidir. Veri kaynağı nesnenizin temsil edilebilmesi için herhangi bir ad seçin. |
|**türüyle**| Gereklidir. Olmalıdır `cosmosdb` . |
|**Credentials** | Gereklidir. <br/><br/>Yönetilen bir kimlik kullanılarak bağlanırken, **kimlik bilgileri** biçimi şu şekilde olmalıdır: *veritabanı = [veritabanı-adı]; RESOURCEID = [kaynak-kimliği-dize];(Apıkind = [api-Kind];)*<br/> <br/>RESOURCEID biçimi: *RESOURCEID =/Subscriptions/**abonelik kimliği**/ResourceGroups/**kaynak grubu adı**/Providers/Microsoft.DocUmentdb/databaseaccounts/**Cosmos DB hesabınızın adı**/;*<br/><br/>SQL koleksiyonları için bağlantı dizesi bir ApiKind gerektirmez.<br/><br/>MongoDB koleksiyonları için bağlantı dizesine **Apikind = MongoDb** ekleyin. <br/><br/>Gremlin grafikleri ve Cassandra tablolarında, önizlemeye erişim sağlamak için [geçitli Dizin Oluşturucu önizlemesine](https://aka.ms/azure-cognitive-search/indexer-preview) kaydolun ve kimlik bilgilerini biçimlendirme hakkında bilgi alın.<br/>|
| **kapsayıcı** | Aşağıdaki öğeleri içerir: <br/>**ad**: gerekli. Endekslenecek veritabanı koleksiyonunun KIMLIĞINI belirtin.<br/>**sorgu**: isteğe bağlı. Rastgele bir JSON belgesini, Azure Bilişsel Arama 'in dizinetarafından kullanılabilecek düz bir şemaya düzleştirmek için bir sorgu belirtebilirsiniz.<br/>MongoDB API 'SI, Gremlin API ve Cassandra API için sorgular desteklenmez. |
| **dataChangeDetectionPolicy** | Önerilen |
|**dataDeletionDetectionPolicy** | İsteğe Bağlı |

### <a name="4---create-the-index"></a>4-dizin oluşturma

Dizin, bir belge, öznitelik ve arama deneyimini şekillendirip diğer yapıların alanlarını belirtir.

Aranabilir bir alanla dizin oluşturma `booktitle` :

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Dizinler oluşturma hakkında daha fazla bilgi için bkz. [Dizin oluşturma](/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5-dizin oluşturucuyu oluşturma

Dizin Oluşturucu bir veri kaynağını hedef arama diziniyle bağlar ve veri yenilemeyi otomatikleştirmek için bir zamanlama sağlar.

Dizin ve veri kaynağı oluşturulduktan sonra Dizin oluşturucuyu oluşturmaya hazırsınız demektir.

Örnek Dizin Oluşturucu tanımı:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Bu Dizin Oluşturucu her iki saatte bir çalışır (zamanlama aralığı "PT2H" olarak ayarlanır). Her 30 dakikada bir dizin oluşturucu çalıştırmak için, aralığı "PT30M" olarak ayarlayın. Desteklenen en kısa Aralık 5 dakikadır. Zamanlama isteğe bağlıdır-atlanırsa, Dizin Oluşturucu yalnızca bir kez oluşturulduğunda çalışır. Ancak, bir dizin oluşturucuyu dilediğiniz zaman isteğe bağlı olarak çalıştırabilirsiniz.   

Dizin Oluşturucu oluşturma API 'SI hakkında daha fazla bilgi için bkz. [Dizin Oluşturucu oluştur](/rest/api/searchservice/create-indexer).

Dizin Oluşturucu zamanlamalarını tanımlama hakkında daha fazla bilgi için bkz. [Azure bilişsel arama için Dizin Oluşturucu zamanlama](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Sorun giderme

Cosmos DB verileri dizinlemeyebilirsiniz, aşağıdakileri göz önünde bulundurun:

1. Cosmos DB hesap anahtarlarınızı kısa süre önce döndürüyorsunuz, yönetilen kimlik bağlantı dizesinin çalışması için 15 dakikaya kadar beklemeniz gerekir.

1. Cosmos DB hesabının erişiminin ağ seçme ile sınırlı olup olmadığını denetleyin. Varsa, [Azure ağ güvenlik özellikleriyle korunan Içeriğe Dizin Oluşturucu erişimi](search-indexer-securing-resources.md)bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB dizinleyici](search-howto-index-cosmosdb.md)