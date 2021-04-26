---
title: Azure Cosmos DB veri erişimini güvenli hale getirme hakkında bilgi edinin
description: Birincil anahtarlar, salt okuma anahtarları, kullanıcılar ve izinler dahil Azure Cosmos DB erişim denetimi kavramları hakkında bilgi edinin.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/11/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 8a16ecd2ee6ed939b2afd0e51e9cf531e419c8af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101656406"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Azure Cosmos DB'de verilere erişimin güvenliğini sağlama
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede Azure Cosmos DB içindeki veri erişimi denetimine genel bakış sunulmaktadır.

Azure Cosmos DB verilerinize erişimi denetlemek için üç yol sağlar.

| Erişim denetimi türü | Özellikler |
|---|---|
| [Birincil anahtarlar](#primary-keys) | Paylaşılan gizlilik, tüm yönetim veya veri işlemlerini sağlar. Hem okuma-yazma hem de salt okuma çeşitleri halinde gelir. |
| [Rol tabanlı erişim denetimi](#rbac) (Önizleme) | Kimlik doğrulaması için Azure Active Directory (AAD) kimlikleri kullanan ayrıntılı, rol tabanlı izin modeli. |
| [Kaynak belirteçleri](#resource-tokens)| Yerel Azure Cosmos DB kullanıcıları ve izinleri temel alan ayrıntılı izin modeli. |

## <a name="primary-keys"></a><a id="primary-keys"></a> Birincil anahtarlar

Birincil anahtarlar, veritabanı hesabının tüm yönetim kaynaklarına erişim sağlar. Her hesap iki birincil anahtardan oluşur: birincil anahtar ve ikincil anahtar. Çift anahtarların amacı, hesap ve verilerinize sürekli erişim sağlamak için yeniden oluşturmanız veya anahtar almanız sağlamaktır. Birincil anahtarlar hakkında daha fazla bilgi için bkz. [veritabanı güvenlik](database-security.md#primary-keys) makalesi.

### <a name="key-rotation"></a><a id="key-rotation"></a> Anahtar döndürme

Birincil anahtarınızı döndürme işlemi basittir. 

1. İkincil anahtarınızı almak için Azure portal gidin.
2. Birincil anahtarınızı uygulamanızdaki ikincil anahtarınızla değiştirin. Tüm dağıtımlardaki tüm Cosmos DB istemcilerinin hemen yeniden başlatıldığından ve güncelleştirilmiş anahtarın kullanılmasına başlayadığınızdan emin olun.
3. Azure portal birincil anahtarı döndürün.
4. Yeni birincil anahtarın tüm kaynaklara karşı çalışıp çalışmadığını doğrulayın. Anahtar döndürme işlemi, Cosmos DB hesabının boyutuna bağlı olarak bir dakikadan daha kısa bir sürede bir zaman alabilir.
5. İkincil anahtarı yeni birincil anahtarla değiştirin.

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png" alt-text="Azure portal-gösteren NoSQL veritabanı güvenliğine birincil anahtar döndürme" border="false":::

### <a name="code-sample-to-use-a-primary-key"></a>Birincil anahtar kullanmak için kod örneği

Aşağıdaki kod örneği, bir DocumentClient örneği oluşturmak ve veritabanı oluşturmak için Cosmos DB hesap uç noktası ve birincil anahtarın nasıl kullanılacağını göstermektedir:

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

Aşağıdaki kod örneği, bir nesneyi başlatmak için Azure Cosmos DB hesap uç noktası ve birincil anahtarın nasıl kullanılacağını göstermektedir `CosmosClient` :

:::code language="python" source="~/cosmosdb-python-sdk/sdk/cosmos/azure-cosmos/samples/access_cosmos_with_resource_token.py" id="configureConnectivity":::

## <a name="role-based-access-control-preview"></a><a id="rbac"></a> Rol tabanlı erişim denetimi (Önizleme)

Azure Cosmos DB, size olanak sağlayan yerleşik bir rol tabanlı erişim denetimi (RBAC) sistemi sunar:

- Azure Active Directory (AAD) kimliğiyle veri isteklerinizin kimliğini doğrulayın.
- Veri isteklerinizi ayrıntılı, rol tabanlı bir izin modeliyle yetkilendirin.

RBAC Azure Cosmos DB, şu durumlarda ideal erişim denetimi yöntemidir:

- Birincil anahtar gibi bir paylaşılan gizli dizi kullanmak istemezsiniz ve belirteç tabanlı bir kimlik doğrulama mekanizmasına dayalı olarak tercih edersiniz.
- İsteklerinizin kimliğini doğrulamak için Azure AD kimliklerini kullanmak istiyorsanız,
- Kimliklerinizin hangi veritabanı işlemlerini gerçekleştirmesine izin verileceğini sıkı bir şekilde kısıtlamak için hassas bir izin modeli gerekir,
- Erişim denetimi ilkelerinizi birden çok kimliğe atayabilecek "Roller" olarak kullanmak istiyorsunuz.

Azure Cosmos DB RBAC hakkında daha fazla bilgi için bkz. [Azure Cosmos DB hesabınız için rol tabanlı erişim denetimi yapılandırma](how-to-setup-rbac.md) .

## <a name="resource-tokens"></a><a id="resource-tokens"></a> Kaynak belirteçleri

Kaynak belirteçleri, bir veritabanı içindeki uygulama kaynaklarına erişim sağlar. Kaynak belirteçleri:

- Belirli kapsayıcılara, Bölüm anahtarlarına, belgelere, eklere, saklı yordamlara, tetikleyicilere ve UDF erişimine erişim sağlar.
- , Bir [kullanıcıya](#users) belirli bir kaynağa [izin](#permissions) verildiğinde oluşturulur.
- , Bir izin kaynağı GÖNDERI, al veya koy çağrısıyla üzerinde işlem yapıldığında yeniden oluşturulur.
- Kullanıcı, kaynak ve izin için özel olarak oluşturulan bir karma kaynak belirteci kullanın.
- , Özelleştirilebilir bir geçerlilik süresi ile zaman bağlalardır. Varsayılan geçerli zaman aralığı bir saattir. Ancak, belirteç ömrü, en fazla beş saate kadar açıkça belirtilebilir.
- Birincil anahtarı vermek için güvenli bir alternatif sağlayın.
- İstemcilerin, izin verilen izinlere göre Cosmos DB hesabındaki kaynakları okumasını, yazmasını ve silmesini sağlar.

Cosmos DB hesabınızdaki kaynaklara, birincil anahtarla güvenilmeyen bir istemciye erişim sağlamak istediğinizde, bir kaynak belirteci (Cosmos DB kullanıcılar ve izinler oluşturarak) kullanabilirsiniz.  

Cosmos DB kaynak belirteçleri, istemcilerin verdiğiniz izinlere göre Cosmos DB hesabınızdaki kaynakları okumasını, yazmasına ve silmesine olanak tanıyan güvenli bir alternatif sağlar ve birincil ya da salt okuma anahtarına gerek kalmadan.

Kaynak belirteçlerinin istendiği, üretibildiği ve istemcilere teslim edileceği tipik bir tasarım deseninin olması aşağıda verilmiştir:

1. Orta katman hizmeti, Kullanıcı fotoğraflarını paylaşmak üzere bir mobil uygulama sunacak şekilde ayarlanır.
2. Orta katman hizmeti Cosmos DB hesabının birincil anahtarına sahiptir.
3. Fotoğraf uygulaması, Son Kullanıcı mobil cihazlarına yüklenir.
4. Oturum açıldığında, fotoğraf uygulaması, orta katman hizmeti ile kullanıcının kimliğini belirler. Bu kimlik kurulumu mekanizması, uygulamaya tamamen sahiptir.
5. Kimlik kurulduktan sonra, orta katman hizmeti kimlik temelinde izinleri ister.
6. Orta katman hizmeti, bir kaynak belirtecini telefon uygulamasına geri gönderir.
7. Telefon uygulaması, kaynak belirteci tarafından tanımlanan izinlerle ve kaynak belirtecinin izin verdiği aralığa göre Cosmos DB kaynaklarına doğrudan erişmek için kaynak belirtecini kullanmaya devam edebilir.
8. Kaynak belirtecinin süresi dolmuşsa, sonraki istekler 401 Yetkisiz bir özel durum alır.  Bu noktada, telefon uygulaması kimliği yeniden oluşturur ve yeni bir kaynak belirteci ister.

    :::image type="content" source="./media/secure-access-to-data/resourcekeyworkflow.png" alt-text="Kaynak belirteçleri Azure Cosmos DB iş akışı" border="false":::

Kaynak belirteci oluşturma ve yönetimi, yerel Cosmos DB istemci kitaplıkları tarafından işlenir; Ancak REST kullanırsanız istek/kimlik doğrulama üst bilgilerini oluşturmanız gerekir. REST için kimlik doğrulama üstbilgileri oluşturma hakkında daha fazla bilgi için, bkz. [Access Control Cosmos DB kaynakları](/rest/api/cosmos-db/access-control-on-cosmosdb-resources) veya [.net SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/Authorization/AuthorizationHelper.cs) veya [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts)'mız için kaynak kodu.

Kaynak belirteçleri oluşturmak için kullanılan bir orta katman hizmeti örneği için, bkz. [Resourcetokenbroker uygulaması](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

### <a name="users"></a>Kullanıcılar<a id="users"></a>

Azure Cosmos DB kullanıcılar bir Cosmos veritabanıyla ilişkilendirilir.  Her veritabanı sıfır veya daha fazla Kullanıcı Cosmos DB içerebilir. Aşağıdaki kod örneği, [.NET SDK v3 Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement)kullanarak Cosmos DB bir kullanıcının nasıl oluşturulacağını göstermektedir.

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Her Cosmos DB kullanıcının, kullanıcıyla ilişkili [izin](#permissions) listesini almak için kullanılabilecek bir ReadAsync () yöntemi vardır.

### <a name="permissions"></a>İzinler<a id="permissions"></a>

Bir izin kaynağı, bir kullanıcıyla ilişkilendirilir ve bölüm anahtarı düzeyi olarak kapsayıcıda atanır. Her Kullanıcı sıfır veya daha fazla izin içerebilir. Bir izin kaynağı, belirli bir bölüm anahtarındaki belirli bir kapsayıcıya veya verilere erişmeyi denerken kullanıcının ihtiyacı olan bir güvenlik belirtecine erişim sağlar. Bir izin kaynağı tarafından sağlanarak kullanılabilecek iki erişim düzeyi vardır:

- Tümü: kullanıcının kaynakta tam izni vardır.
- Okuma: Kullanıcı yalnızca kaynağın içeriğini okuyabilir, ancak kaynakta yazma, güncelleştirme veya silme işlemlerini gerçekleştiremez.

> [!NOTE]
> Saklı yordamları çalıştırmak için kullanıcının saklı yordamın çalıştırılacağı kapsayıcıda tüm izne sahip olması gerekir.

[Veri düzlemi isteklerinde tanılama günlüklerini](cosmosdb-monitor-resource-logs.md)etkinleştirirseniz, izne karşılık gelen aşağıdaki iki özellik günlüğe kaydedilir:

* **Resourcetokenpermissionıd** -bu özellik belirttiğiniz kaynak belirteci izin kimliğini gösterir. 

* **Resourcetokenpermissionmode** -bu özellik, kaynak belirtecini oluştururken ayarlamış olduğunuz izin modunu gösterir. İzin modunun "All" veya "Read" gibi değerleri olabilir.

#### <a name="code-sample-to-create-permission"></a>İzin oluşturmak için kod örneği

Aşağıdaki kod örneği, bir izin kaynağı oluşturmayı, izin kaynağının kaynak belirtecini okumayı ve izinleri yukarıda oluşturulan [kullanıcıyla](#users) ilişkilendirmeyi gösterir.

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

#### <a name="code-sample-to-read-permission-for-user"></a>Kullanıcı için okuma izni için kod örneği

Aşağıdaki kod parçacığı, yukarıda oluşturulan kullanıcıyla ilişkili iznin nasıl alınacağını gösterir ve tek bir bölüm anahtarı kapsamındaki Kullanıcı adına yeni bir CosmosClient örneği oluşturur.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="differences-between-rbac-and-resource-tokens"></a>RBAC ve kaynak belirteçleri arasındaki farklılıklar

| Konu | RBAC | Kaynak belirteçleri |
|--|--|--|
| Kimlik Doğrulaması  | Azure Active Directory (Azure AD) ile. | Yerel Azure Cosmos DB kullanıcılara göre<br>Kaynak belirteçlerini Azure AD ile tümleştirmek, Azure AD kimliklerini ve Azure Cosmos DB kullanıcılarını köprülemek için ek iş gerektirir. |
| Yetkilendirme | Rol tabanlı: rol tanımları eşleme izin verilen eylemler ve birden çok kimliğe atanabilir. | İzin tabanlı: her Azure Cosmos DB Kullanıcı için veri erişim izinleri atamanız gerekir. |
| Belirteç kapsamı | AAD belirteci, istek sahibinin kimliğini taşır. Bu kimlik, yetkilendirme gerçekleştirmek için atanan tüm rol tanımlarına göre eşleştirilir. | Kaynak belirteci belirli bir Azure Cosmos DB kaynağında belirli bir Azure Cosmos DB kullanıcısına verilen izni taşır. Farklı kaynaklardaki yetkilendirme istekleri farklı belirteçler gerektirebilir. |
| Belirteç yenileme | AAD belirteci, süresi sona erdiğinde Azure Cosmos DB SDK 'lar tarafından otomatik olarak yenilenir. | Kaynak belirteci yenilemesi desteklenmiyor. Bir kaynak belirtecinin süresi dolarsa, yeni bir tane verilmesi gerekir. |

## <a name="add-users-and-assign-roles"></a>Kullanıcı ekleme ve rol atama

Kullanıcı hesabınıza Azure Cosmos DB hesap okuyucusu erişimi eklemek için bir abonelik sahibine Azure portal aşağıdaki adımları uygulayın.

1. Azure portal açın ve Azure Cosmos DB hesabınızı seçin.
2. **Erişim denetimi (IAM)** sekmesine tıklayın ve ardından **+ rol ataması Ekle**' ye tıklayın.
3. **Rol ataması Ekle** bölmesinde, **rol** kutusunda **Cosmos DB hesap okuyucu rolü**' nü seçin.
4. **Erişim ata kutusunda** **Azure AD Kullanıcı, Grup veya uygulama**' yı seçin.
5. Dizininizde erişim vermek istediğiniz kullanıcı, Grup veya uygulamayı seçin.  Dizinde görünen ad, e-posta adresi veya nesne tanımlayıcıları ile arama yapabilirsiniz.
    Seçilen Kullanıcı, Grup veya uygulama seçilen Üyeler listesinde görünür.
6. **Kaydet**’e tıklayın.

Varlık artık Azure Cosmos DB kaynaklarını okuyabilir.

## <a name="delete-or-export-user-data"></a>Kullanıcı verilerini silme veya dışarı aktarma

Bir veritabanı hizmeti olarak Azure Cosmos DB, veritabanınızda veya kapsayıcılarınızdaki verileri aramanıza, seçmenize, değiştirmenize ve silmenizi sağlar. Bununla birlikte, belirtilen API 'Leri kullanmak ve gerekirse kişisel verileri bulmak ve silmek için gereken mantığı tanımlamak sizin sorumluluğunuzdadır. Her bir çok modelli API (SQL, MongoDB, Gremlin, Cassandra, tablo), özel koşullara göre verileri aramak ve silmek için yöntemler içeren farklı dil SDK 'leri sağlar. Ayrıca, belirli bir süreden sonra verileri otomatik olarak silmek için [yaşam süresi (TTL)](time-to-live.md) özelliğini de etkinleştirebilirsiniz.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Sonraki adımlar

- Cosmos veritabanı güvenliği hakkında daha fazla bilgi edinmek için bkz. [Cosmos DB veritabanı güvenliği](database-security.md).
- Azure Cosmos DB yetkilendirme belirteçleri oluşturmayı öğrenmek için [Azure Cosmos DB kaynaklarında Access Control](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)bakın.
- Kullanıcılar ve izinlerle Kullanıcı yönetimi örnekleri, [.NET SDK V3 Kullanıcı yönetimi örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
