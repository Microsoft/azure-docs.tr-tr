---
title: Active Directory kullanarak sonuçları kırpmak için güvenlik filtreleri
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama arama sonuçları ve güvenlik filtrelerini ve Azure Active Directory (AD) kimliklerini kullanarak güvenlik ayrıcalıklarını belge düzeyinde nasıl uygulayacağınızı öğrenin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5788585b2365b12a90a508e5a972b61f73e48c15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97629519"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Active Directory kimliklerini kullanarak Azure Bilişsel Arama sonuçlarını kırpmasına yönelik güvenlik filtreleri

Bu makalede, Kullanıcı grubu üyeliğine göre arama sonuçlarının kırpılmasından Azure Active Directory (AD) güvenlik kimliklerinin Azure Bilişsel Arama filtreleriyle birlikte nasıl kullanılacağı gösterilmektedir.

Bu makale aşağıdaki görevleri kapsar:
> [!div class="checklist"]
> - Azure AD grupları ve kullanıcıları oluşturma
> - Kullanıcıyı oluşturduğunuz grupla ilişkilendirin
> - Yeni grupları önbelleğe al
> - İlişkili gruplarıyla belge dizini oluştur
> - Grup tanımlayıcıları filtresiyle bir arama isteği verme
> 
> [!NOTE]
> Bu makaledeki örnek kod parçacıkları C# dilinde yazılmıştır. Tam kaynak kodunu [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started)'da bulabilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar

Azure Bilişsel Arama dizininizin, belgeye okuma erişimi olan grup kimliklerinin listesini depolamak için bir [güvenlik alanı](search-security-trimming-for-azure-search.md) olmalıdır. Bu kullanım örneği, güvenli kılınabilir bir öğe (örneğin, bir okul uygulaması) ve bu öğeye kimlerin erişimi olduğunu belirten bir güvenlik alanı (Admissions personeli) arasında bire bir yazışma olduğunu varsayar.

Bu kılavuzda Kullanıcı, Grup ve ilişki oluşturmak için gerekli olan Azure AD Yöneticisi izinlerine sahip olmanız gerekir. 

Uygulamanız Ayrıca, aşağıdaki yordamda açıklandığı gibi Azure AD 'ye çok kiracılı bir uygulama olarak kaydolmalıdır.

### <a name="register-your-application-with-azure-active-directory"></a>Uygulamanızı Azure Active Directory kaydetme

Bu adım, Kullanıcı ve grup hesaplarının oturum açma işlemlerini kabul etmek amacıyla uygulamanızı Azure AD ile tümleştirir. Kuruluşunuzda kiracı yöneticisi değilseniz, aşağıdaki adımları gerçekleştirmek için [Yeni bir kiracı oluşturmanız](../active-directory/develop/quickstart-create-new-tenant.md) gerekebilir.

1. [Azure Portal](https://portal.azure.com), aboneliğiniz için Azure Active Directory kaynağını bulun.

1. Sol tarafta, **Yönet** altında, **uygulama kayıtları** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.

1. Kayda, belki de arama uygulaması adına benzer bir ad verin. **Kaydet**’i seçin.

1. Uygulama kaydı oluşturulduktan sonra uygulama KIMLIĞINI kopyalayın. Bu dizeyi uygulamanıza sağlamanız gerekir.

   [Dotnethowtosecuritykırpması](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)içinde adımladıysanız, bu değeri **app.config** dosyasına yapıştırın.

   Kiracı KIMLIĞI için tekrarlayın.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Temel bileşenler bölümündeki uygulama KIMLIĞI":::

1. Sol tarafta **API izinleri** ' ni seçin ve **izin Ekle**' yi seçin. 

1. **Microsoft Graph** ' yi seçin ve ardından **temsilci izinleri**' ni seçin.

1. Aşağıdaki temsilci izinleri arayın ve ekleyin:

   - **Directory. ReadWrite. All**
   - **Group.ReadWrite.All**
   - **User. ReadWrite. All**

Microsoft Graph, Azure AD 'ye bir REST API aracılığıyla programlı erişim sağlayan bir API sağlar. Bu izlenecek yol için kod örneği, grupları, kullanıcıları ve ilişkilendirmeleri oluşturmak için Microsoft Graph API 'sini çağırmak için izinleri kullanır. API 'Ler, daha hızlı filtreleme için Grup tanımlayıcılarını önbelleğe almak için de kullanılır.

## <a name="create-users-and-groups"></a>Kullanıcı ve grup oluşturma

Oluşturulan bir uygulamaya arama ekliyorsanız, Azure AD 'de mevcut kullanıcı ve grup tanımlayıcılarından olabilirsiniz. Bu durumda, sonraki üç adımı atlayabilirsiniz. 

Ancak, mevcut kullanıcılarınız yoksa güvenlik sorumlularını oluşturmak için Microsoft Graph API 'Leri kullanabilirsiniz. Aşağıdaki kod parçacıkları, Azure Bilişsel Arama dizininizdeki güvenlik alanı için veri değerleri haline gelen tanımlayıcıların nasıl oluşturulacağını göstermektedir. Kuramsal üniversite sayede uygulamamızda bu, sayede personeli için güvenlik tanımlayıcıları olacaktır.

Kullanıcı ve grup üyeliği, özellikle büyük kuruluşlarda çok akıcı olabilir. Kullanıcı ve grup kimliklerini oluşturan kodun, kuruluş üyeliğindeki değişiklikleri çekmek için genellikle yeterince çalıştırması gerekir. Benzer şekilde, Azure Bilişsel Arama dizininiz izin verilen kullanıcı ve kaynakların geçerli durumunu yansıtmak için benzer bir güncelleştirme zamanlaması gerektirir.

### <a name="step-1-create-group"></a>1. Adım: [Grup oluşturma](/graph/api/group-post-groups) 

```csharp
private static Dictionary<Group, List<User>> CreateGroupsWithUsers(string tenant)
{
    Group group = new Group()
    {
        DisplayName = "My First Prog Group",
        SecurityEnabled = true,
        MailEnabled = false,
        MailNickname = "group1"
    };
```

### <a name="step-2-create-user"></a>2. Adım: [Kullanıcı oluşturma](/graph/api/user-post-users)

```csharp
User user1 = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = String.Format("user1@{0}", tenant),
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
```

### <a name="step-3-associate-user-and-group"></a>3. Adım: kullanıcıyı ve grubu Ilişkilendirme

```csharp
List<User> users = new List<User>() { user1, user2 };
Dictionary<Group, List<User>> groups = new Dictionary<Group, List<User>>() { { group, users } };
```

### <a name="step-4-cache-the-groups-identifiers"></a>4. Adım: Grup tanımlayıcılarını önbelleğe alma

İsteğe bağlı olarak, ağ gecikmesini azaltmak için Kullanıcı grubu ilişkilendirmelerini önbelleğe alarak, bir arama isteği verildiğinde gruplar önbellekten döndürülür ve Azure AD 'ye gidiş dönüş kaydedilir. Birden çok kullanıcıya sahip tek bir http isteği göndermek ve önbelleği oluşturmak için [Azure AD Batch API 'sini](/graph/json-batching) kullanabilirsiniz.

Microsoft Graph, yüksek hacimli istekleri işlemek için tasarlanmıştır. İstek sayısı ortaya çıkarsa, Microsoft Graph HTTP durum kodu 429 ile başarısız olur. Daha fazla bilgi için bkz. [Microsoft Graph azaltma](/graph/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Belge, izin verilen gruplarıyla dizin oluştur

Azure Bilişsel Arama 'de sorgu işlemleri bir Azure Bilişsel Arama dizininden yürütülür. Bu adımda, dizin oluşturma işlemi, güvenlik filtreleri olarak kullanılan tanımlayıcılar da dahil olmak üzere aranabilir verileri bir dizine aktarır. 

Azure Bilişsel Arama, kullanıcı kimliklerinin kimliğini doğrulamaz veya bir kullanıcının görüntüleme iznine sahip olduğu içeriği oluşturmak için mantık sağlar. Güvenlik kırpması için kullanım örneği, gizli bir belge ile bu belgeye erişim sahibi olan grup tanımlayıcısı arasındaki ilişkiyi, bir arama dizinine bir şekilde içeri aktardığınızı varsayar. 

Kuramsal örnekte, bir Azure Bilişsel Arama dizinindeki PUT isteğinin gövdesi, bir başvuranın okul esi veya dökümünü, bu içeriği görüntüleme iznine sahip olan grup tanımlayıcısıyla birlikte içermelidir. 

Bu izlenecek yol için kod örneğinde kullanılan genel örnekte, Dizin eylemi şöyle görünebilir:

```csharp
private static void IndexDocuments(string indexName, List<string> groups)
{
    IndexDocumentsBatch<SecuredFiles> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new SecuredFiles()
            {
                FileId = "1",
                Name = "secured_file_a",
                GroupIds = new[] { groups[0] }
            }),
              ...
            };

IndexDocumentsResult result = searchClient.IndexDocuments(batch);
```

## <a name="issue-a-search-request"></a>Arama isteği verme

Güvenlik kırpma amacıyla, dizindeki güvenlik alanındaki değerler, arama sonuçlarına belge ekleme veya dışlama için kullanılan statik değerlerdir. Örneğin, Admissions için grup tanımlayıcısı "A11B22C33D44-E55F66G77-H88I99JKK" ise, bir Azure Bilişsel Arama dizinindeki tüm belgeler, istek sahibine geri gönderilen arama sonuçlarına dahil edilir (veya hariç tutulur).

İstek veren kullanıcı gruplarına bağlı olarak arama sonuçlarında döndürülen belgeleri filtrelemek için aşağıdaki adımları gözden geçirin.

### <a name="step-1-retrieve-users-group-identifiers"></a>1. Adım: kullanıcının grup tanımlayıcılarını alma

Kullanıcının grupları zaten önbelleğe alınmamışsa veya önbelleğin süresi dolmuşsa, [gruplar](/graph/api/directoryobject-getmembergroups) isteğini yayınlayın.

```csharp
private static async void RefreshCache(IEnumerable<User> users)
{
    HttpClient client = new HttpClient();
    var userGroups = await _microsoftGraphHelper.GetGroupsForUsers(client, users);
    _groupsCache = new ConcurrentDictionary<string, List<string>>(userGroups);
}
```

### <a name="step-2-compose-the-search-request"></a>2. Adım: arama isteğini oluşturma

Kullanıcının grup üyeliğine sahip olduğunuz varsayılarak, arama isteğini uygun filtre değerleriyle verebilirsiniz.

```csharp
private static void SearchQueryWithFilter(string user)
{
    // Using the filter below, the search result will contain all documents that their GroupIds field   
    // contain any one of the Ids in the groups list
    string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", String.Join(",", _groupsCache[user])));
    SearchOptions searchOptions =
        new SearchOptions()
        {
            Filter = filter
        };
    searchOptions.Select.Add("name");

    SearchResults<SecuredFiles> results = searchClient.Search<SecuredFiles>("*", searchOptions);

    Console.WriteLine("Results for groups '{0}' : {1}", _groupsCache[user], results.GetResults().Select(r => r.Document.Name));
}
```

### <a name="step-3-handle-the-results"></a>3. Adım: sonuçları Işleme

Yanıt, kullanıcının görüntüleme iznine sahip olduğu öğelerden oluşan filtrelenmiş bir belge listesi içerir. Arama sonuçları sayfasını oluşturma yönteminize bağlı olarak, filtrelenmiş sonuç kümesini yansıtmak için görsel ipuçları dahil etmek isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, Azure AD oturum açma işlemlerini kullanarak Azure Bilişsel Arama sonuçlarında belgeleri filtreleyerek, istekte belirtilen filtreyle eşleşmeyen belgelerin sonuçlarını kırptırabilmeniz için bir model öğrendiniz. Daha basit olabilecek alternatif bir model veya diğer güvenlik özelliklerini tekrar ziyaret etmek için aşağıdaki bağlantılara bakın.

- [Sonuçları kırpma için güvenlik filtreleri](search-security-trimming-for-azure-search.md)
- [Azure Bilişsel Arama güvenliği](search-security-overview.md)