---
title: Yönetilen kimlik kullanarak depolama hesabına bağlantı ayarlama
titleSuffix: Azure Cognitive Search
description: Yönetilen kimlik kullanarak bir Azure depolama hesabına Dizin Oluşturucu bağlantısı ayarlamayı öğrenin
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: f26ca04955dfa854a8ee17b7aa255a6ed991b8df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358380"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity"></a>Yönetilen kimlik kullanarak Azure Depolama hesabına bağlantıyı ayarlama

Bu sayfada, veri kaynağı nesne bağlantı dizesinde kimlik bilgileri sağlamak yerine yönetilen kimlik kullanarak bir Azure depolama hesabına Dizin Oluşturucu bağlantısının nasıl ayarlanacağı açıklanır.

Bu özellik hakkında daha fazla bilgi edinmek için, bir dizin oluşturucunun ne olduğu ve veri kaynağınız için dizin oluşturucunun nasıl ayarlanacağı hakkında bilgi sahibi olmanız önerilir. Aşağıdaki bağlantılarda daha fazla bilgi bulabilirsiniz:
* [Dizin Oluşturucu’ya genel bakış](search-indexer-overview.md)
* [Azure Blob Dizin Oluşturucu](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage 2. Dizin Oluşturucu](search-howto-index-azure-data-lake-storage.md)
* [Azure Tablo Dizin Oluşturucu](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>Bağlantıyı ayarlama

### <a name="1---turn-on-system-assigned-managed-identity"></a>1-sistem tarafından atanan yönetilen kimliği aç

Sistem tarafından atanan bir yönetilen kimlik etkinleştirildiğinde Azure, arama hizmetiniz için aynı kiracı ve abonelik içindeki diğer Azure hizmetlerinde kimlik doğrulaması yapmak üzere kullanılabilecek bir kimlik oluşturur. Daha sonra bu kimliği, dizin oluşturma sırasında verilere erişime izin veren Azure rol tabanlı erişim denetimi (Azure RBAC) atamaları içinde kullanabilirsiniz.

![Sistem tarafından atanan yönetilen kimliği aç](./media/search-managed-identities/turn-on-system-assigned-identity.png "Sistem tarafından atanan yönetilen kimliği aç")

**Kaydet** ' i seçtikten sonra, arama hizmetinize atanmış BIR nesne kimliği görürsünüz.

![Nesne kimliği](./media/search-managed-identities/system-assigned-identity-object-id.png "Nesne kimliği")
 
### <a name="2---add-a-role-assignment"></a>2-rol ataması ekleme

Bu adımda, Azure Bilişsel Arama Service 'e depolama hesabınızdan veri okuma izni vereceksiniz.

1. Azure portal, dizine eklemek istediğiniz verileri içeren depolama hesabına gidin.
2. **Erişim denetimi (IAM)** öğesini seçin
3. **Ekle** ' yi seçin ve **rol ataması Ekle**

    ![Rol ataması ekle](./media/search-managed-identities/add-role-assignment-storage.png "Rol ataması ekle")

4. Dizin oluşturmak istediğiniz depolama hesabı türüne göre uygun rolleri seçin:
    1. Azure Blob depolama, arama hizmetinizi **Depolama Blobu veri okuyucusu** rolüne eklemenizi gerektirir.
    1. Azure Data Lake Storage 2., arama hizmetinizi **Depolama Blobu veri okuyucusu** rolüne eklemenizi gerektirir.
    1. Azure Tablo depolaması, arama hizmetinizi **okuyucu ve veri erişim** rolüne eklemenizi gerektirir.
5.  **Azure AD kullanıcısı, Grup veya hizmet sorumlusu** olarak **erişim atamasını** bırak
6.  Arama hizmetinizi arayın, seçin ve ardından **Kaydet** ' i seçin.

    Azure Blob depolama ve Azure Data Lake Storage 2. için örnek:

    ![Depolama Blobu veri okuyucusu rol ataması Ekle](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "Depolama Blobu veri okuyucusu rol ataması Ekle")

    Azure Tablo Depolaması örneği:

    ![Okuyucu ve veri erişimi rol ataması ekleme](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "Okuyucu ve veri erişimi rol ataması ekleme")

### <a name="3---create-the-data-source"></a>3-veri kaynağını oluşturma

[REST API](/rest/api/searchservice/create-data-source), Azure Portal ve [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) , yönetilen kimlik bağlantı dizesini destekler. Aşağıda, [REST API](/rest/api/searchservice/create-data-source) ve yönetilen kimlik bağlantı dizesi kullanarak bir depolama hesabından veri dizini oluşturmak için bir veri kaynağı oluşturma örneği verilmiştir. Yönetilen kimlik bağlantı dizesi biçimi REST API, .NET SDK ve Azure portal için aynıdır.

Bir depolama hesabından dizin oluştururken, veri kaynağı aşağıdaki gerekli özelliklere sahip olmalıdır:

* **ad** , arama hizmetinizin içindeki veri kaynağının benzersiz adıdır.
* **türüyle**
    * Azure Blob depolama: `azureblob`
    * Azure Tablo Depolaması: `azuretable`
    * Azure Data Lake Storage 2.: [Bu form](https://aka.ms/azure-cognitive-search/mi-preview-request)kullanılarak önizlemeye kaydolduktan sonra **tür** sunulacaktır.
* **Credentials**
    * Kimlik doğrulaması için yönetilen bir kimlik kullanırken, **kimlik bilgileri** biçimi yönetilen kimlik kullanmaktan farklı. Burada, hesap anahtarı veya parolası olmayan bir RESOURCEID sağlarsınız. RESOURCEID, depolama hesabının abonelik KIMLIĞINI, depolama hesabının kaynak grubunu ve depolama hesabı adını içermelidir.
    * Yönetilen kimlik biçimi: 
        * *RESOURCEID =/Subscriptions/**ABONELIK kimliği**/ResourceGroups/**kaynak grubu adı**/Providers/Microsoft.Storage/storageAccounts/**depolama hesabınızın adı**/;*
* **kapsayıcı** , depolama hesabınızda bir kapsayıcı veya tablo adı belirtir. Varsayılan olarak, kapsayıcıdaki tüm Bloblar alınabilir. Yalnızca belirli bir sanal dizinde Blobları indekslemek istiyorsanız, bu dizini isteğe bağlı **sorgu** parametresini kullanarak belirtebilirsiniz.

[REST API](/rest/api/searchservice/create-data-source)kullanarak blob veri kaynağı nesnesinin nasıl oluşturulacağı hakkında örnek:

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

### <a name="4---create-the-index"></a>4-dizin oluşturma

Dizin, bir belge, öznitelik ve arama deneyimini şekillendirip diğer yapıların alanlarını belirtir.

`content`Bloblardan ayıklanan metni depolamak için aranabilir bir alanla dizin oluşturma:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

Dizinler oluşturma hakkında daha fazla bilgi için bkz. [Dizin oluşturma](/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5-dizin oluşturucuyu oluşturma

Bir Dizin Oluşturucu bir veri kaynağını hedef arama diziniyle bağlar ve veri yenilemeyi otomatikleştirmek için bir zamanlama sağlar.

Dizin ve veri kaynağı oluşturulduktan sonra Dizin oluşturucuyu oluşturmaya hazırsınız demektir.

Blob Dizin Oluşturucu için örnek Dizin Oluşturucu tanımı:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Bu Dizin Oluşturucu her iki saatte bir çalışır (zamanlama aralığı "PT2H" olarak ayarlanır). Her 30 dakikada bir dizin oluşturucu çalıştırmak için, aralığı "PT30M" olarak ayarlayın. Desteklenen en kısa Aralık 5 dakikadır. Zamanlama isteğe bağlıdır-atlanırsa, Dizin Oluşturucu yalnızca bir kez oluşturulduğunda çalışır. Ancak, bir dizin oluşturucuyu dilediğiniz zaman isteğe bağlı olarak çalıştırabilirsiniz.   

Dizin Oluşturucu oluşturma API 'SI hakkında daha fazla bilgi için bkz. [Dizin Oluşturucu oluştur](/rest/api/searchservice/create-indexer).

Dizin Oluşturucu zamanlamalarını tanımlama hakkında daha fazla bilgi için bkz. [Azure bilişsel arama için Dizin Oluşturucu zamanlama](search-howto-schedule-indexers.md).

## <a name="accessing-secure-data-in-storage-accounts"></a>Depolama hesaplarında güvenli verilere erişme

Azure depolama hesapları, güvenlik duvarları ve sanal ağlar kullanılarak daha da güvenli hale getirilir. Bir BLOB depolama hesabından veya güvenlik duvarı ya da sanal ağ kullanılarak güvenliği sağlanmış Data Lake Gen2 Storage hesabından içerik dizini eklemek istiyorsanız, [Güvenilen hizmet özel durumu aracılığıyla depolama hesaplarındaki verilere güvenli bir şekilde erişme](search-indexer-howto-access-trusted-service-exception.md)yönergelerini izleyin.

## <a name="see-also"></a>Ayrıca bkz.

Azure depolama Dizin oluşturucular hakkında daha fazla bilgi edinin:

* [Azure Blob Dizin Oluşturucu](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage 2. Dizin Oluşturucu](search-howto-index-azure-data-lake-storage.md)
* [Azure Tablo Dizin Oluşturucu](search-howto-indexing-azure-tables.md)