---
title: Azure Cosmos DB veri erişimini güvenli hale getirme hakkında bilgi edinin
description: Ana anahtarlar, salt okuma anahtarları, kullanıcılar ve izinler dahil Azure Cosmos DB erişim denetimi kavramları hakkında bilgi edinin.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: ec32dfbe9aac13a9b3c08922b73b9a23d668d744
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73152280"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Azure Cosmos DB verilere güvenli erişim

Bu makalede, [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)depolanan verilere erişimin güvenliğini sağlamaya yönelik bir genel bakış sunulmaktadır.

Azure Cosmos DB, kullanıcıların kimliğini doğrulamak ve veri ve kaynaklarına erişim sağlamak için iki tür anahtar kullanır. 

|Anahtar türü|Kaynaklar|
|---|---|
|[Ana anahtarlar](#master-keys) |Yönetim kaynakları için kullanılır: veritabanı hesapları, veritabanları, kullanıcılar ve izinler|
|[Kaynak belirteçleri](#resource-tokens)|Uygulama kaynakları için kullanılır: kapsayıcılar, belgeler, ekler, saklı yordamlar, Tetikleyiciler ve UDF 'ler|

<a id="master-keys"></a>

## <a name="master-keys"></a>Ana anahtarlar 

Ana anahtarlar veritabanı hesabının tüm yönetim kaynaklarına erişim sağlar. Ana anahtarlar:  
- Hesaplara, veritabanlarına, kullanıcılara ve izinlere erişim sağlayın. 
- Kapsayıcılara ve belgelere parçalı erişim sağlamak için kullanılamaz.
- , Bir hesabın oluşturulması sırasında oluşturulur.
- Herhangi bir zamanda yeniden oluşturulabilir.

Her hesap iki ana anahtardan oluşur: birincil anahtar ve ikincil anahtar. Çift anahtarların amacı, hesap ve verilerinize sürekli erişim sağlamak için, anahtarları yeniden oluşturmak veya almak için kullanılır. 

Cosmos DB hesap için iki ana anahtara ek olarak, salt okunurdur iki anahtar vardır. Salt okunurdur bu anahtarlar yalnızca hesapta okuma işlemlerine izin verir. Salt okuma anahtarları okuma izni kaynaklarına erişim sağlamaz.

Birincil, ikincil, salt okuma ve okuma/yazma ana anahtarları Azure portal kullanılarak alınabilir ve yeniden oluşturulabilir. Yönergeler için bkz. [erişim anahtarlarını görüntüleme, kopyalama ve yeniden](manage-with-cli.md#regenerate-account-key)oluşturma.

![Azure portal-gösteren NoSQL veritabanı güvenliğine erişim denetimi (ıAM)](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Ana anahtarınızı döndürme işlemi basittir. İkincil anahtarınızı almak için Azure portal gidin, sonra birincil anahtarınızı uygulamanızdaki ikincil anahtarınızla değiştirin ve ardından Azure portal birincil anahtarı döndürün.

![Azure portal-gösteren NoSQL veritabanı güvenliğine ana anahtar döndürme](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Ana anahtar kullanmak için kod örneği

Aşağıdaki kod örneği, bir Cosmos DB hesabı uç noktasının ve ana anahtarın bir DocumentClient örneği oluşturmak ve bir veritabanı oluşturmak için nasıl kullanılacağını göstermektedir. 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Kaynak belirteçleri

Kaynak belirteçleri, bir veritabanı içindeki uygulama kaynaklarına erişim sağlar. Kaynak belirteçleri:
- Belirli kapsayıcılara, Bölüm anahtarlarına, belgelere, eklere, saklı yordamlara, tetikleyicilere ve UDF erişimine erişim sağlar.
- , Bir [kullanıcıya](#users) belirli bir kaynağa [izin](#permissions) verildiğinde oluşturulur.
- , Bir izin kaynağı GÖNDERI, al veya koy çağrısıyla üzerinde işlem yapıldığında yeniden oluşturulur.
- Kullanıcı, kaynak ve izin için özel olarak oluşturulan bir karma kaynak belirteci kullanın.
- , Özelleştirilebilir bir geçerlilik süresi ile zaman bağlalardır. Varsayılan geçerli TimeSpan bir saattir. Ancak, belirteç ömrü, en fazla beş saate kadar açıkça belirtilebilir.
- Ana anahtarı vermek için güvenli bir alternatif sağlayın. 
- İstemcilerin, izin verilen izinlere göre Cosmos DB hesabındaki kaynakları okumasını, yazmasını ve silmesini sağlar.

Ana anahtarla güvenilmeyen bir istemciye Cosmos DB hesabınızdaki kaynaklara erişim sağlamak istediğinizde bir kaynak belirteci (Cosmos DB kullanıcılar ve izinler oluşturarak) kullanabilirsiniz.  

Cosmos DB kaynak belirteçleri, istemcilerin Cosmos DB hesabınızdaki kaynakları, verdiğiniz izinlere göre okumasını, yazmasına ve silmesine olanak tanıyan güvenli bir alternatif sağlar.

Kaynak belirteçlerinin istendiği, üretibildiği ve istemcilere teslim edileceği tipik bir tasarım deseninin olması aşağıda verilmiştir:

1. Orta katman hizmeti, Kullanıcı fotoğraflarını paylaşmak üzere bir mobil uygulama sunacak şekilde ayarlanır. 
2. Orta katman hizmeti Cosmos DB hesabının ana anahtarına sahiptir.
3. Fotoğraf uygulaması, Son Kullanıcı mobil cihazlarına yüklenir. 
4. Oturum açıldığında, fotoğraf uygulaması, orta katman hizmeti ile kullanıcının kimliğini belirler. Bu kimlik kurulumu mekanizması, uygulamaya tamamen sahiptir.
5. Kimlik kurulduktan sonra, orta katman hizmeti kimlik temelinde izinleri ister.
6. Orta katman hizmeti, bir kaynak belirtecini telefon uygulamasına geri gönderir.
7. Telefon uygulaması, kaynak belirteci tarafından tanımlanan izinlerle ve kaynak belirtecinin izin verdiği aralığa göre Cosmos DB kaynaklarına doğrudan erişmek için kaynak belirtecini kullanmaya devam edebilir. 
8. Kaynak belirtecinin süresi dolmuşsa, sonraki istekler 401 Yetkisiz bir özel durum alır.  Bu noktada, telefon uygulaması kimliği yeniden oluşturur ve yeni bir kaynak belirteci ister.

    ![Kaynak belirteçleri Azure Cosmos DB iş akışı](./media/secure-access-to-data/resourcekeyworkflow.png)

Kaynak belirteci oluşturma ve yönetimi, yerel Cosmos DB istemci kitaplıkları tarafından gerçekleştirilir; Ancak REST kullanırsanız istek/kimlik doğrulama üst bilgilerini oluşturmanız gerekir. REST için kimlik doğrulama üstbilgileri oluşturma hakkında daha fazla bilgi için, bkz. [Access Control Cosmos DB kaynaklarında](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) veya [SDK 'lerimizin kaynak kodu](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Kaynak belirteçleri oluşturmak için kullanılan bir orta katman hizmeti örneği için, bkz. [Resourcetokenbroker uygulaması](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Kullanıcılar
Cosmos DB kullanıcılar bir Cosmos veritabanıyla ilişkilendirilir.  Her veritabanı sıfır veya daha fazla Kullanıcı Cosmos DB içerebilir.  Aşağıdaki kod örneği, bir Cosmos DB Kullanıcı kaynağının nasıl oluşturulacağını göstermektedir.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Her Cosmos DB kullanıcının, kullanıcıyla ilişkili [izin](#permissions) listesini almak için kullanılabilecek bir PermissionsLink özelliği vardır.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>İzinler
Cosmos DB izin kaynağı bir Cosmos DB kullanıcıyla ilişkilendirilir.  Her Kullanıcı sıfır veya daha fazla Cosmos DB izinleri içerebilir.  Bir izin kaynağı, kullanıcının belirli bir uygulama kaynağına erişmeyi denerken ihtiyaç duyacağı bir güvenlik belirtecine erişim sağlar.
Bir izin kaynağı tarafından sağlanarak kullanılabilecek iki erişim düzeyi vardır:

* Tümü: kullanıcının kaynakta tam izni vardır.
* Okuma: Kullanıcı yalnızca kaynağın içeriğini okuyabilir, ancak kaynakta yazma, güncelleştirme veya silme işlemlerini gerçekleştiremez.

> [!NOTE]
> Cosmos DB saklı yordamları çalıştırmak için kullanıcının saklı yordamın çalıştırılacağı kapsayıcıda tüm izne sahip olması gerekir.
> 
> 

### <a name="code-sample-to-create-permission"></a>İzin oluşturmak için kod örneği

Aşağıdaki kod örneği, bir izin kaynağı oluşturmayı, izin kaynağının kaynak belirtecini okumayı ve izinleri yukarıda oluşturulan [kullanıcıyla](#users) ilişkilendirmeyi gösterir.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = UriFactory.CreateDocumentCollectionUri("db", "collection"),
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Koleksiyonunuz için bir bölüm anahtarı belirttiyseniz, koleksiyon, belge ve ek kaynaklarına yönelik izin, ResourceLink 'e ek olarak ResourcePartitionKey ' i de içermelidir.

### <a name="code-sample-to-read-permissions-for-user"></a>Kullanıcı için okuma izinleri için kod örneği

Belirli bir kullanıcıyla ilişkili tüm izin kaynaklarını kolayca almak için Cosmos DB her kullanıcı nesnesi için bir izin akışı kullanılabilir hale getirir.  Aşağıdaki kod parçacığı, yukarıda oluşturulan kullanıcıyla ilişkili iznin nasıl alınacağını, bir izin listesi oluşturulduğunu ve Kullanıcı adına yeni bir DocumentClient örneği oluşturmayı gösterir.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="add-users-and-assign-roles"></a>Kullanıcı ekleme ve rol atama

Kullanıcı hesabınıza Azure Cosmos DB hesap okuyucusu erişimi eklemek için bir abonelik sahibine Azure portal aşağıdaki adımları uygulayın.

1. Azure portal açın ve Azure Cosmos DB hesabınızı seçin.
2. **Erişim denetimi (IAM)** sekmesine tıklayın ve ardından **+ rol ataması Ekle**' ye tıklayın.
3. **Rol ataması Ekle** bölmesinde, **rol** kutusunda **Cosmos DB hesap okuyucu rolü**' nü seçin.
4. **Erişim ata kutusunda** **Azure AD Kullanıcı, Grup veya uygulama**' yı seçin.
5. Dizininizde erişim vermek istediğiniz kullanıcı, Grup veya uygulamayı seçin.  Dizinde görünen ad, e-posta adresi veya nesne tanımlayıcıları ile arama yapabilirsiniz.
    Seçilen Kullanıcı, Grup veya uygulama seçilen Üyeler listesinde görünür.
6. **Kaydet** düğmesine tıklayın.

Varlık artık Azure Cosmos DB kaynaklarını okuyabilir.

## <a name="delete-or-export-user-data"></a>Kullanıcı verilerini silme veya dışarı aktarma
Azure Cosmos DB, veritabanı veya koleksiyonlardaki tüm kişisel verileri aramanıza, seçmenize, değiştirmenize ve silmenizi sağlar. Azure Cosmos DB, kişisel verileri bulmak ve silmek için API 'Ler sağlar. Bu, API 'Leri kullanmak ve kişisel verileri silmek için gerekli mantığı tanımlamak sizin sorumluluğunuzdadır. Her bir çok modelli API (SQL, MongoDB, Gremlin, Cassandra, tablo), kişisel verileri aramak ve silmek için yöntemler içeren farklı dil SDK 'leri sağlar. Ayrıca, belirli bir süreden sonra verileri otomatik olarak silmek için [yaşam süresi (TTL)](time-to-live.md) özelliğini de etkinleştirebilirsiniz.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Sonraki adımlar
* Cosmos veritabanı güvenliği hakkında daha fazla bilgi için bkz. [Cosmos DB: veritabanı güvenliği](database-security.md).
* Azure Cosmos DB yetkilendirme belirteçleri oluşturmayı öğrenmek için [Azure Cosmos DB kaynaklarında Access Control](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources)bakın.
