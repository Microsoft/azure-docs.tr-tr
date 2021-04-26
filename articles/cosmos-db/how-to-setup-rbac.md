---
title: Azure AD ile Azure Cosmos DB hesabınız için rol tabanlı erişim denetimi yapılandırma
description: Rol tabanlı erişim denetimini Azure Cosmos DB hesabınız için Azure Active Directory ile yapılandırma hakkında bilgi edinin
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/19/2021
ms.author: thweiss
ms.openlocfilehash: 9de41835e33d50a670a44089cb10d44cc57e92a7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818719"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Rol tabanlı erişim denetimini Azure Cosmos DB hesabınız için Azure Active Directory yapılandırma (Önizleme)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB rol tabanlı erişim denetimi şu anda önizleme aşamasındadır. Bu önizleme sürümü bir Hizmet Düzeyi Sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Bu makale Azure Cosmos DB ' de veri düzlemi işlemleri için rol tabanlı erişim denetimi ile ilgilidir. Yönetim düzlemi işlemlerini kullanıyorsanız, yönetim düzlemi işlemler makalenize uygulanan [rol tabanlı erişim denetimi](role-based-access-control.md) bölümüne bakın.

Azure Cosmos DB, size olanak sağlayan yerleşik bir rol tabanlı erişim denetimi (RBAC) sistemi sunar:

- Azure Active Directory (Azure AD) kimliğiyle veri isteklerinizin kimliğini doğrulayın.
- Veri isteklerinizi ayrıntılı, rol tabanlı bir izin modeliyle yetkilendirin.

## <a name="concepts"></a>Kavramlar

Azure Cosmos DB veri düzlemi RBAC, [Azure RBAC](../role-based-access-control/overview.md)gıbı diğer RBAC sistemlerinde yaygın olarak bulunan kavramlar üzerine kurulmuştur:

- [İzin modeli](#permission-model) bir dizi **eylemden** oluşur; Bu eylemlerin her biri bir veya birden çok veritabanı işlemine eşlenir. Bazı eylemlere örnek olarak öğe okuma, öğe yazma veya sorgu yürütme sayılabilir.
- Azure Cosmos DB kullanıcılar, izin verilen eylemlerin bir listesini içeren **[rol tanımları](#role-definitions)** oluşturur.
- Rol tanımları, **[rol atamaları](#role-assignments)** aracılığıyla belırlı Azure AD kimliklerine atanır. Rol ataması Ayrıca, rol tanımının geçerli olduğu kapsamı tanımlar; Şu anda üç kapsam şu anda:
    - Azure Cosmos DB hesap,
    - Bir Azure Cosmos DB veritabanı,
    - Bir Azure Cosmos DB kapsayıcısı.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="RBAC kavramları":::

> [!NOTE]
> RBAC Azure Cosmos DB yerleşik rol tanımlarını Şu anda göstermiyor.

## <a name="permission-model"></a><a id="permission-model"></a> İzin modeli

> [!IMPORTANT]
> Bu izin modeli yalnızca verileri okuyup yazmanıza izin veren veritabanı işlemlerini içerir. Kapsayıcı oluşturma veya aktarım hızını değiştirme gibi herhangi bir yönetim işlemi **türünü kapsamaz.** Bu, bir AAD kimliğiyle yönetim işlemlerinin kimliğini doğrulamak için **herhangi bir Azure Cosmos DB veri düzlemi SDK 'sını** kullanamayacağı anlamına gelir. Bunun yerine, [Azure RBAC](role-based-access-control.md) 'yi şu şekilde kullanmanız gerekir:
> - [ARM şablonları](manage-with-templates.md)
> - [Azure PowerShell betikler](manage-with-powershell.md),
> - [Azure CLI betikleri](manage-with-cli.md),
> - Azure Yönetim Kitaplıkları ' de kullanılabilir
>   - [.NET](https://www.nuget.org/packages/Azure.ResourceManager.CosmosDB)
>   - [Java](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-cosmos)
>   - [Python](https://pypi.org/project/azure-mgmt-cosmosdb/)

Aşağıdaki tabloda, izin modeli tarafından kullanıma sunulan tüm eylemler listelenmektedir.

| Name | Karşılık gelen veritabanı işlemleri |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Hesap meta verilerini okuyun. Ayrıntılar için [meta veri isteklerini](#metadata-requests) görüntüleyin. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Yeni öğe oluştur. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Tek bir öğeyi KIMLIĞE ve bölüm anahtarına göre okuyun (nokta-oku). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Varolan bir öğeyi değiştirme. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | "Upsert" bir öğe, yoksa onu oluşturur veya varsa onu değiştirir. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Bir öğeyi silin. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | Bir [SQL sorgusu](sql-query-getting-started.md)yürütün. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Kapsayıcının [değişiklik akışından](read-change-feed.md)okuyun. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | [Saklı yordam](stored-procedures-triggers-udfs.md)yürütün. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | Çok sayfalı bölge hesapları için [çakışmaları](conflict-resolution-policies.md) yönetin (diğer bir deyişle, çakışma akışındaki öğeleri Listeleme ve silme). |

Joker karakterler hem *kapsayıcılar* hem de *öğe* düzeylerinde desteklenir:

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Meta veri istekleri

Azure Cosmos DB SDK 'Ları kullanılırken, bu SDK 'lar başlatma sırasında salt okuma meta veri istekleri verir ve belirli veri isteklerini sunar. Bu meta veri istekleri aşağıdaki gibi çeşitli yapılandırma ayrıntılarını getirir: 

- Hesabınızın kullanılabilir olduğu Azure bölgelerini içeren, hesabınızın genel yapılandırması.
- Kapsayıcılarınızın veya bunların dizin oluşturma ilkelerinin bölüm anahtarı.
- Bir kapsayıcı ve bunların adreslerini oluşturan fiziksel bölümlerin listesi.

Hesabınızda depoladığınız verilerden herhangi birini *getirmez* .

İzin modelimizin en iyi saydamlığını sağlamak için bu meta veri istekleri eylem tarafından açıkça ele alınmıştır `Microsoft.DocumentDB/databaseAccounts/readMetadata` . Bu eyleme, Azure Cosmos DB hesabınıza Azure Cosmos DB SDK 'Lardan biri üzerinden erişildiği her durumda izin verilmelidir. Azure Cosmos DB hiyerarşisinde (yani, hesap, veritabanı veya kapsayıcı) herhangi bir düzeyde atanabilir (bir rol ataması aracılığıyla).

Eylem tarafından izin verilen gerçek meta veri istekleri, `Microsoft.DocumentDB/databaseAccounts/readMetadata` eylemin atandığı kapsama bağlıdır:

| Kapsam | Eylem tarafından izin verilen istekler |
|---|---|
| Hesap | -Hesabın altındaki veritabanlarını listeleme<br>-Hesap altındaki her veritabanı için, veritabanı kapsamındaki izin verilen eylemler |
| Veritabanı | -Veritabanı meta verileri okunuyor<br>-Veritabanı altındaki kapsayıcıları listeleme<br>-Veritabanı altındaki her kapsayıcı için kapsayıcı kapsamındaki izin verilen eylemler |
| Kapsayıcı | -Kapsayıcı meta verileri okunuyor<br>-Kapsayıcı altındaki fiziksel bölümleri listeleme<br>-Her fiziksel bölümün adresini çözme |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> Rol tanımları oluşturma

Rol tanımı oluştururken şunları sağlamanız gerekir:

- Azure Cosmos DB hesabınızın adı.
- Hesabınızı içeren kaynak grubu.
- Rol tanımının türü; yalnızca `CustomRole` Şu anda destekleniyor.
- Rol tanımının adı.
- Rolün izin vermek istediğiniz [eylemlerin](#permission-model) bir listesi.
- Rol tanımının atanabileceği bir veya birden fazla kapsam; Desteklenen kapsamlar şunlardır:
    - `/` (hesap düzeyi),
    - `/dbs/<database-name>` (veritabanı düzeyi),
    - `/dbs/<database-name>/colls/<container-name>` (kapsayıcı düzeyi).

> [!NOTE]
> Aşağıda açıklanan işlemler şu anda ' de kullanılabilir:
> - Azure PowerShell: [az. CosmosDB sürüm 2.0.1-Önizleme](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLı: [' cosmosdb-Preview ' uzantı sürümü 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma

Yalnızca okuma eylemleri içeren *Myreadonlyrole* adlı bir rol oluşturun:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadOnlyRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed') `
    -AssignableScope "/"
```

Tüm eylemleri içeren *Myreadwriterole* adlı bir rol oluşturun:

```powershell
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadWriteRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*') `
    -AssignableScope "/"
```

Kimliklerini getirmek için oluşturduğunuz rol tanımlarını listeleyin:

```powershell
Get-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName
```

```
RoleName         : MyReadWriteRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}

RoleName         : MyReadOnlyRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}
```

### <a name="using-the-azure-cli"></a>Azure CLI'yı kullanma

Yalnızca okuma eylemleri içeren *Myreadonlyrole* adlı bir rol oluşturun:

```json
// role-definition-ro.json
{
    "RoleName": "MyReadOnlyRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ]
    }]
}
```

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-ro.json
```

Tüm eylemleri içeren *Myreadwriterole* adlı bir rol oluşturun:

```json
// role-definition-rw.json
{
    "RoleName": "MyReadWriteRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}
```

```azurecli
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-rw.json
```

Kimliklerini getirmek için oluşturduğunuz rol tanımlarını listeleyin:

```azurecli
az cosmosdb sql role definition list --account-name $accountName --resource-group $resourceGroupName
```

```
[
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadWriteRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  },
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadOnlyRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  }
]
```

## <a name="create-role-assignments"></a><a id="role-assignments"></a> Rol atamaları oluşturma

Rol tanımlarınızı oluşturduktan sonra bunları AAD kimlikleriniz ile ilişkilendirebilirsiniz. Rol ataması oluştururken şunları sağlamanız gerekir:

- Azure Cosmos DB hesabınızın adı.
- Hesabınızı içeren kaynak grubu.
- Atanacak rol tanımının KIMLIĞI.
- Rol tanımının atanması gereken kimliğin asıl KIMLIĞI.
- Rol atamasının kapsamı; Desteklenen kapsamlar şunlardır:
    - `/` (hesap düzeyi)
    - `/dbs/<database-name>` (veritabanı düzeyi)
    - `/dbs/<database-name>/colls/<container-name>` (kapsayıcı düzeyi)

  Kapsam, rol tanımının atanabilir kapsamlarından birinin alt kapsamı ile eşleşmelidir veya aynı olmalıdır.

> [!NOTE]
> Bir hizmet sorumlusu için rol ataması oluşturmak istiyorsanız, **nesne kimliğini** **Azure Active Directory** portalı dikey penceresinin **Kurumsal uygulamalar** bölümünde bulunan olarak kullandığınızdan emin olun.

> [!NOTE]
> Aşağıda açıklanan işlemler şu anda ' de kullanılabilir:
> - Azure PowerShell: [az. CosmosDB sürüm 2.0.1-Önizleme](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLı: [' cosmosdb-Preview ' uzantı sürümü 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma

Bir kimliğe rol atama:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" // as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope $accountName `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>Azure CLI'yı kullanma

Bir kimliğe rol atama:

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' // as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

## <a name="initialize-the-sdk-with-azure-ad"></a>Azure AD ile SDK 'Yı başlatma

Uygulamanızda Azure Cosmos DB RBAC 'yi kullanmak için, Azure Cosmos DB SDK 'sını başlatma yönteminizi güncelleştirmeniz gerekir. Hesabınızın birincil anahtarını geçirmek yerine bir sınıfın örneğini geçirmeniz gerekir `TokenCredential` . Bu örnek, kullanmak istediğiniz kimlik adına bir AAD belirteci getirmek için gereken bağlamla Azure Cosmos DB SDK 'sını sağlar.

Örnek oluşturma yönteminiz `TokenCredential` Bu makalenin kapsamı dışındadır. Kullanmak istediğiniz AAD kimliği türüne (Kullanıcı sorumlusu, hizmet sorumlusu, Grup vb.) bağlı olarak böyle bir örnek oluşturmanın birçok yolu vardır. En önemlisi, `TokenCredential` örneğiniz, rollerinizi atadığınız kimliğe (asıl kimlik) çözümlenmelidir. Sınıf oluşturma örneklerini bulabilirsiniz `TokenCredential` :

- [.NET 'te](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [Java üzerinde](/java/api/overview/azure/identity-readme#credential-classes)
- [JavaScript 'te](/javascript/api/overview/azure/identity-readme#credential-classes)
- REST API

Aşağıdaki örneklerde bir örneği olan bir hizmet sorumlusu kullanılmaktadır `ClientSecretCredential` .

### <a name="in-net"></a>.NET 'te

Azure Cosmos DB RBAC, şu anda `preview` [.NET SDK V3](sql-api-sdk-dotnet-standard.md)sürümünde desteklenmektedir.

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>Java üzerinde

Azure Cosmos DB RBAC, [Java SDK v4](sql-api-sdk-java-v4.md)'de Şu anda destekleniyor.

```java
TokenCredential ServicePrincipal = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<azure-ad-tenant-id>")
    .clientId("<client-application-id>")
    .clientSecret("<client-application-secret>")
    .build();
CosmosAsyncClient Client = new CosmosClientBuilder()
    .endpoint("<account-endpoint>")
    .credential(ServicePrincipal)
    .build();
```

### <a name="in-javascript"></a>JavaScript 'te

Azure Cosmos DB RBAC, [JAVASCRIPT SDK V3](sql-api-sdk-node.md)'de Şu anda destekleniyor.

```javascript
const servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
const client = new CosmosClient({
    "<account-endpoint>",
    aadCredentials: servicePrincipal
});
```

### <a name="in-rest-api"></a>REST API

Azure Cosmos DB RBAC Şu anda REST API 2021-03-15 sürümü ile desteklenmektedir. [Yetkilendirme üst bilgisini](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)oluştururken, aşağıdaki örnekte gösterildiği gibi **tür** parametresini **AAD** olarak, karma imzasını **(SIG)** **OAuth belirtecine** ayarlayın:

`type=aad&ver=1.0&sig=<token-from-oauth>`

## <a name="auditing-data-requests"></a>Veri isteklerini denetleme

Azure Cosmos DB RBAC kullanılırken, [tanılama günlükleri](cosmosdb-monitor-resource-logs.md) her bir veri işlemi için kimlik ve yetkilendirme bilgileri ile genişletilmiş bir şekilde yapılır. Bu, ayrıntılı denetim gerçekleştirmenizi ve Azure Cosmos DB hesabınıza gönderilen her veri isteği için kullanılan AAD kimliğini almanızı sağlar.

Bu ek bilgiler **Dataplanerequests** günlük kategorisinde akar ve iki ek sütundan oluşur:

- `aadPrincipalId_g` isteğin kimliğini doğrulamak için kullanılan AAD kimliğinin asıl KIMLIĞINI gösterir.
- `aadAppliedRoleAssignmentId_g` istek yetkilendirirken kabul edilen [rol atamasını](#role-assignments) gösterir.

## <a name="limits"></a>Sınırlar

- Azure Cosmos DB hesap başına en çok 100 rol tanımı ve 2.000 rol ataması oluşturabilirsiniz.
- Rol tanımlarını yalnızca Azure Cosmos DB hesabınızla aynı Azure AD kiracısına ait olan Azure AD kimliklerine atayabilirsiniz.
- Azure AD grup çözümlemesi, 200 taneden fazla gruba ait olan kimlikler için şu anda desteklenmiyor.
- Azure AD belirteci Şu anda her bir tek istekle birlikte Azure Cosmos DB hizmetine gönderilen bir üst bilgi olarak geçirilir ve toplam yük boyutunu artırır.
- [Azure Cosmos DB Explorer](data-explorer.md) aracılığıyla VERILERINIZE Azure AD ile erişmek henüz desteklenmez. Azure Cosmos DB gezgin 'in kullanılması, kullanıcının şu anda hesabın birincil anahtarına erişmesini gerektirir.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>RBAC tarafından desteklenen Azure Cosmos DB API'leri hangileridir?

Şu anda yalnızca SQL API'si desteklenmektedir.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>Rol tanımları ve rol atamaları Azure portaldan yönetilebilir mi?

Rol yönetimi için Azure portal desteği henüz sağlanmamıştır.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Azure Cosmos DB SQL API 'sindeki SDK 'lar RBAC 'yi destekliyor mu?

[.Net v3](sql-api-sdk-dotnet-standard.md) ve [Java v4](sql-api-sdk-java-v4.md) SDK 'ları Şu anda desteklenmektedir.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Süresi dolan Azure AD belirteci Azure Cosmos DB SDK'ları tarafından otomatik olarak yenilenir mi?

Evet.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>RBAC kullanılırken hesap birincil anahtarının kullanımı devre dışı bırakılabilir mi?

Şu anda hesap birincil anahtarını devre dışı bırakmak mümkün değildir.

## <a name="next-steps"></a>Sonraki adımlar

- [Cosmos DB verilerine güvenli erişim](secure-access-to-data.md)hakkında genel bakış alın.
- [Azure Cosmos DB yönetimi Için RBAC](role-based-access-control.md)hakkında daha fazla bilgi edinin.