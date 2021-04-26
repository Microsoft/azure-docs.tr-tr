---
title: Yönetilen kimlik kullanarak Azure SQL veritabanı 'na bağlantı kurma
titleSuffix: Azure Cognitive Search
description: Yönetilen kimlik kullanarak Azure SQL veritabanı 'na bir Dizin Oluşturucu bağlantısı ayarlamayı öğrenin
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b940da2cf754e7e1cac91df6b517ecebe55e8c40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358431"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity"></a>Yönetilen kimlik kullanarak Azure SQL veritabanı 'na bir Dizin Oluşturucu bağlantısı kurma

Bu sayfada, veri kaynağı nesne bağlantı dizesinde kimlik bilgileri sağlamak yerine yönetilen bir kimlik kullanılarak Azure SQL veritabanı 'na bir Dizin Oluşturucu bağlantısının nasıl ayarlanacağı açıklanır.

Bu özellik hakkında daha fazla bilgi edinmek için, bir dizin oluşturucunun ne olduğu ve veri kaynağınız için dizin oluşturucunun nasıl ayarlanacağı hakkında bilgi sahibi olmanız önerilir. Aşağıdaki bağlantılarda daha fazla bilgi bulabilirsiniz:

* [Dizin Oluşturucu’ya genel bakış](search-indexer-overview.md)
* [Azure SQL dizin oluşturucu](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Yönetilen kimlik kullanarak bağlantı kurma

### <a name="1---turn-on-system-assigned-managed-identity"></a>1-sistem tarafından atanan yönetilen kimliği aç

Sistem tarafından atanan bir yönetilen kimlik etkinleştirildiğinde Azure, arama hizmetiniz için aynı kiracı ve abonelik içindeki diğer Azure hizmetlerinde kimlik doğrulaması yapmak üzere kullanılabilecek bir kimlik oluşturur. Daha sonra bu kimliği, dizin oluşturma sırasında verilere erişime izin veren Azure rol tabanlı erişim denetimi (Azure RBAC) atamaları içinde kullanabilirsiniz.

![Sistem tarafından atanan yönetilen kimliği aç](./media/search-managed-identities/turn-on-system-assigned-identity.png "Sistem tarafından atanan yönetilen kimliği aç")

**Kaydet** ' i seçtikten sonra, arama hizmetinize atanmış BIR nesne kimliği görürsünüz.

![Nesne kimliği](./media/search-managed-identities/system-assigned-identity-object-id.png "Nesne kimliği")

### <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2-SQL Server için Azure Active Directory Yöneticisi sağlama

Sonraki adımda veritabanına bağlanırken, arama hizmetinize veritabanına erişim izni vermek için veritabanına Yönetici erişimi olan bir Azure Active Directory (Azure AD) hesabıyla bağlanmanız gerekir.

Azure AD hesabı yöneticinize veritabanına erişim sağlamak için [buradaki](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-database) yönergeleri izleyin.

### <a name="3---assign-the-search-service-permissions"></a>3-arama hizmeti izinlerini atama

Veritabanını okumak için arama hizmeti iznini atamak için aşağıdaki adımları izleyin.

1. Visual Studio 'ya bağlanma

    ![Visual Studio 'ya bağlanma](./media/search-managed-identities/connect-with-visual-studio.png "Visual Studio 'ya bağlanma")

2. Azure AD hesabınızla kimlik doğrulama

    ![Kimlik doğrulaması](./media/search-managed-identities/visual-studio-authentication.png "Kimlik doğrulaması")

3. Aşağıdaki komutları yürütün:

    Arama hizmeti adınızın etrafına köşeli ayraç ekleyin.
    
    ```
    CREATE USER [your search service name here] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [your search service name here];
    ```

    ![Yeni sorgu](./media/search-managed-identities/visual-studio-new-query.png "Yeni sorgu")

    ![Sorguyu Yürüt](./media/search-managed-identities/visual-studio-execute-query.png "Sorguyu Yürüt")

>[!NOTE]
> Adım 1 ' deki arama hizmeti kimliği, bu adım tamamlandıktan sonra değiştirilirse, rol üyeliğini kaldırmalı ve SQL veritabanında kullanıcıyı kaldırmalı ve ardından adım 3 ' ü tamamlayarak izinleri yeniden eklemeniz gerekir.
> Rol üyeliğini ve kullanıcıyı kaldırmak aşağıdaki komutlar çalıştırılarak gerçekleştirilebilir:
> ```
> sp_droprolemember 'db_datareader', [your search service name];
> DROP USER IF EXISTS [your search service name];
> ```

### <a name="4---add-a-role-assignment"></a>4-rol ataması ekleme

Bu adımda, Azure Bilişsel Arama hizmetine SQL Server verileri okuma izni verirsiniz.

1. Azure portal Azure SQL Server sayfanıza gidin.
2. **Erişim denetimi (IAM)** öğesini seçin
3. **Ekle** ' yi seçin ve **rol ataması Ekle**

    ![Rol ataması ekle](./media/search-managed-identities/add-role-assignment-sql-server.png "Rol ataması ekle")

4. Uygun **okuyucu** rolünü seçin.
5. **Azure AD kullanıcısı, Grup veya hizmet sorumlusu** olarak **erişim atamasını** bırak
6. Arama hizmetinizi arayın, seçin ve ardından **Kaydet** ' i seçin.

    ![Okuyucu rolü ataması Ekle](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "Okuyucu rolü ataması Ekle")

### <a name="5---create-the-data-source"></a>5-veri kaynağını oluşturma

[REST API](/rest/api/searchservice/create-data-source), Azure Portal ve [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) , yönetilen kimlik bağlantı dizesini destekler. Aşağıda, [REST API](/rest/api/searchservice/create-data-source) ve yönetilen kimlik bağlantı dizesi kullanarak BIR Azure SQL veritabanından veri dizini oluşturmak için bir veri kaynağı oluşturma örneği verilmiştir. Yönetilen kimlik bağlantı dizesi biçimi REST API, .NET SDK ve Azure portal için aynıdır.

[REST API](/rest/api/searchservice/create-data-source)kullanarak bir veri kaynağı oluştururken, veri kaynağı aşağıdaki gerekli özelliklere sahip olmalıdır:

* **ad** , arama hizmetinizin içindeki veri kaynağının benzersiz adıdır.
* **tür**`azuresql`
* **Credentials**
    * Kimlik doğrulaması için yönetilen bir kimlik kullanırken, **kimlik bilgileri** biçimi, bir kimlik bilgisi kullanmaktan farklı. Burada, bir başlangıç kataloğu veya veritabanı adı ve hesap anahtarı ya da parolası olmayan bir RESOURCEID sağlarsınız. RESOURCEID, Azure SQL veritabanının abonelik KIMLIĞINI, SQL veritabanı kaynak grubunu ve SQL veritabanının adını içermelidir. 
    * Yönetilen kimlik bağlantı dizesi biçimi:
        * *İlk Katalog | Veritabanı =**veritabanı adı**; RESOURCEID =/Subscriptions/**ABONELIK kimliği**/ResourceGroups/**kaynak grubu adı**/Providers/Microsoft.SQL/Servers/**SQL Server adınız**/; Bağlantı zaman aşımı =**bağlantı zaman aşımı uzunluğu**;*
* **kapsayıcı** , dizin oluşturmak istediğiniz tablonun veya görünümün adını belirtir.

[REST API](/rest/api/searchservice/create-data-source)kullanarak BIR Azure SQL veri kaynağı nesnesinin nasıl oluşturulacağı hakkında örnek:

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { "connectionString" : "Database=sql-database;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azure-sql-resource-group/providers/Microsoft.Sql/servers/sql-server-search-demo;Connection Timeout=30;" },
    "container" : { "name" : "my-table" }
} 
```

### <a name="6---create-the-index"></a>6-dizini oluşturma

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

### <a name="7---create-the-indexer"></a>7-Dizin oluşturucuyu oluşturma

Bir Dizin Oluşturucu bir veri kaynağını hedef arama diziniyle bağlar ve veri yenilemeyi otomatikleştirmek için bir zamanlama sağlar.

Dizin ve veri kaynağı oluşturulduktan sonra Dizin oluşturucuyu oluşturmaya hazırsınız demektir.

Azure SQL Indexer için örnek Dizin Oluşturucu tanımı:

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

Bu Dizin Oluşturucu her iki saatte bir çalışır (zamanlama aralığı "PT2H" olarak ayarlanır). Her 30 dakikada bir dizin oluşturucu çalıştırmak için, aralığı "PT30M" olarak ayarlayın. Desteklenen en kısa Aralık 5 dakikadır. Zamanlama isteğe bağlıdır-atlanırsa, Dizin Oluşturucu yalnızca bir kez oluşturulduğunda çalışır. Ancak, bir dizin oluşturucuyu dilediğiniz zaman isteğe bağlı olarak çalıştırabilirsiniz.   

Dizin Oluşturucu oluşturma API 'SI hakkında daha fazla bilgi için bkz. [Dizin Oluşturucu oluştur](/rest/api/searchservice/create-indexer).

Dizin Oluşturucu zamanlamalarını tanımlama hakkında daha fazla bilgi için bkz. [Azure bilişsel arama için Dizin Oluşturucu zamanlama](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Sorun giderme

Dizin Oluşturucu istemcinin sunucuya erişmesine izin verilmediğini bildiren veri kaynağına bağlanmayı denediğinde hata alırsanız, [yaygın Dizin Oluşturucu hatalarına](./search-indexer-troubleshooting.md)göz atın.

## <a name="see-also"></a>Ayrıca bkz.

Azure SQL Indexer hakkında daha fazla bilgi edinin:
* [Azure SQL dizin oluşturucu](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)