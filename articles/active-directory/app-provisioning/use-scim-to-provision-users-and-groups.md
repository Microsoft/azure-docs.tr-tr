---
title: Öğretici-Azure AD 'den uygulamalara Kullanıcı hazırlama için bir SCıM uç noktası geliştirin
description: Etki alanları arası kimlik yönetimi (SCıM) sistemi, otomatik Kullanıcı sağlamayı standartlaştırlar. Bu öğreticide, bir SCıM uç noktası geliştirmeyi, SCıM API 'nizi Azure Active Directory tümleştirmenizi ve bulut uygulamalarınıza kullanıcıları ve grupları sağlamayı otomatik hale getirmeye başlayacağınızı öğreneceksiniz.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 04/12/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.openlocfilehash: 3d53c96c4b0306911b0c8a0b8576f35a73419db0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498161"
---
# <a name="tutorial-develop-and-plan-provisioning-for-a-scim-endpoint"></a>Öğretici: SCıM uç noktası için geliştirme ve plan sağlama

Uygulama geliştiricisi olarak, uygulamanız ve Azure AD (AAD) arasında kullanıcıları ve grupları otomatik olarak sağlamayı etkinleştirmek için, sistem etki alanları arası kimlik yönetimi (SCıM) Kullanıcı yönetimi API 'sini kullanabilirsiniz. Bu makalede bir SCıM uç noktası oluşturma ve AAD sağlama hizmeti ile tümleştirme açıklanır. SCıM belirtimi, sağlama için ortak bir Kullanıcı şeması sağlar. SAML veya OpenID Connect gibi Federasyon standartlarıyla birlikte kullanıldığında, SCıM yöneticilere erişim yönetimi için uçtan uca standartlara dayalı bir çözüm sunar.

![SCıM ile Azure AD 'den bir uygulamaya sağlama](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCıM iki uç noktanın standartlaştırılmış bir tanımıdır: `/Users` uç nokta ve `/Groups` uç nokta. Nesneleri oluşturmak, güncelleştirmek ve silmek için ortak REST fiillerini ve grup adı, Kullanıcı adı, adı, soyadı, soyadı ve e-posta gibi ortak özniteliklerin önceden tanımlanmış bir şemasını kullanır. Bir SCıM 2,0 REST API sunan uygulamalar, özel bir kullanıcı yönetimi API 'siyle çalışmayı azaltabilir veya ortadan kaldırabilir. Örneğin, tüm uyumlu SCıM istemcileri `/Users` Yeni bir kullanıcı girişi oluşturmak için uç noktaya BIR JSON NESNESININ http gönderisini nasıl yapılacağını bilir. Aynı temel eylemler için biraz farklı bir API sağlamak yerine, SCıM standardına uyan uygulamalar, önceden var olan istemcilerden, araçlardan ve koddan hemen faydalanabilir. 

SCıM 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) ' de tanımlanan yönetim için standart Kullanıcı nesne şeması ve REST API 'leri, kimlik sağlayıcılarının ve uygulamaların birbirleriyle daha kolay tümleşmesine olanak tanır. Bir SCıM uç noktası oluşturan uygulama geliştiricileri, özel iş yapmak zorunda kalmadan herhangi bir SCıM uyumlu istemcisiyle tümleştirilebilir.

Bir uygulamaya sağlamayı otomatik hale getirmek için Azure AD SCıM istemcisiyle bir SCıM uç noktası oluşturup tümleştirmesini gerektirir. Uygulamanıza Kullanıcı ve grup sağlamaya başlamak için aşağıdaki adımları kullanın. 
    
1. Kullanıcı ve grup şemanızı tasarlama

   AAD SCıM uygulamasının desteklediği Kullanıcı ve grup şemasına nasıl eşlendikleri belirlemek için uygulamanın nesne ve özniteliklerini belirleme.

1. AAD SCıM uygulamasını anlama

   AAD SCıM istemcisinin, SCıM protokol isteği işleme ve yanıtlarınızı modellemek için nasıl uygulandığını anlayın.

1. SCıM uç noktası oluşturma

   Bir uç noktanın AAD sağlama hizmeti ile tümleştirilebilen SCıM 2,0 ile uyumlu olması gerekir. Bir seçenek olarak, uç noktanızı derlemek için Microsoft ortak dil altyapısı (CLı) kitaplıklarını ve kod örneklerini kullanın. Bu örnekler yalnızca başvuru ve test içindir; bunları üretim uygulamanızda bağımlılıklar olarak kullanmanızı öneririz.

1. SCıM uç noktanızı AAD SCıM istemcisiyle tümleştirme 

   Kuruluşunuz AAD 'nin desteklediği bir SCıM 2,0 profili uygulamak için üçüncü taraf bir uygulama kullanıyorsa, kullanıcıların ve grupların sağlanması ve sağlanması için hızlı bir şekilde otomatik hale getirebilirsiniz.

1. Uygulamanızı AAD uygulama galerisine yayımlayın 

   Müşterilerin uygulamanızı bulmasını ve sağlamayı kolay bir şekilde yapılandırmasını kolaylaştırın. 

![SCıM uç noktasını Azure AD ile tümleştirme adımları](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="design-your-user-and-group-schema"></a>Kullanıcı ve grup şemanızı tasarlama

Her uygulama, Kullanıcı veya grup oluşturmak için farklı öznitelikler gerektirir. Uygulamanızın ihtiyaç duyduğu gerekli nesneleri (kullanıcılar, gruplar) ve öznitelikleri (ad, yönetici, iş unvanı, vb.) tanımlayarak tümleştirmenizi başlatın. 

SCıM standardı, kullanıcıları ve grupları yönetmek için bir şema tanımlar. 

**Çekirdek** Kullanıcı şeması yalnızca üç öznitelik gerektirir (diğer tüm öznitelikler isteğe bağlıdır):

- `id`, hizmet sağlayıcı tanımlı tanımlayıcı
- `userName`, Kullanıcı için benzersiz bir tanımlayıcı (genellikle Azure AD Kullanıcı asıl adıyla eşlenir)
- `meta`, hizmet sağlayıcısı tarafından tutulan *salt okuma* meta verileri

SIM standardı, **çekirdek** Kullanıcı şemasına ek olarak, uygulamanızın ihtiyaçlarını karşılamak için Kullanıcı şemasını genişletmek üzere bir modelle bir **Kurumsal** Kullanıcı uzantısı tanımlar. 

Örneğin, uygulamanız hem bir kullanıcının e-postasını hem de kullanıcının yöneticisini gerektiriyorsa, kullanıcının yöneticisini toplamak üzere kullanıcının e-postasını ve **kuruluş** Kullanıcı şemasını toplamak için **çekirdek** şemayı kullanın.

Şemanızı tasarlamak için aşağıdaki adımları izleyin:

1. Uygulamanızın gerektirdiği öznitelikleri listeleyin, ardından kimlik doğrulaması için gereken öznitelikler olarak (örn. loginName ve e-posta), Kullanıcı yaşam döngüsünü (ör. durum/etkin) yönetmek için gereken öznitelikleri ve uygulamanın çalışması için gereken diğer tüm öznitelikleri (ör. yönetici, etiket).

1. Özniteliklerin zaten **çekirdek** Kullanıcı şemasında veya **Kurumsal** Kullanıcı şemasında tanımlanmış olup olmadığını denetleyin. Aksi takdirde, Kullanıcı şemasına eksik öznitelikleri içeren bir uzantı tanımlamanız gerekir. Kullanıcıya bir uzantının Kullanıcı tarafından sağlanma izin vermek için aşağıdaki örneğe bakın `tag` .

1. SCıM özniteliklerini Azure AD 'deki Kullanıcı öznitelikleriyle eşleyin. SCıM uç noktanıza tanımladığınız özniteliklerden birinin Azure AD Kullanıcı şemasında açık bir karşılığı yoksa, kiracı yöneticisinin şemasını genişletmesine veya özellik için aşağıda gösterildiği gibi bir uzantı özniteliği kullanmasına kılavuzluk edin `tags` .

|Gerekli uygulama özniteliği|Eşlenmiş SCıM özniteliği|Eşlenen Azure AD özniteliği|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.familyName|Soyadı|
|Iş posta|e-postalar [tür EQ "iş"]. değer|Posta|
|manager|manager|manager|
|etiket|urn: IETF: params: Scim: schemas: Extension: 2.0: Customexgerilim: Tag|extensionAttribute1|
|durum|active|ısofdeleted (hesaplanan değer kullanıcı üzerinde depolanmaz)|

**Gerekli özniteliklerin örnek listesi**

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen@testuser.com",
     "id": "48af03ac28ad4fb88478",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
}   
```
**JSON yükü tarafından tanımlanan örnek şema**

> [!NOTE]
> Uygulama için gerekli olan özniteliklere ek olarak JSON temsili de gerekli `id` , `externalId` ve `meta` özniteliklerini içerir.

`/User` `/Group` Azure AD 'deki tüm varsayılan kullanıcı özniteliklerinin SCıM RFC 'ye göre sınıflandırmanıza yardımcı olur, bkz. [ÖZELLEŞTIRME özniteliklerinin Azure AD ile SCIM uç noktası arasında nasıl eşlendiğine](customize-application-attributes.md)bakın.


| Azure Active Directory Kullanıcı | "urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: user" |
| --- | --- |
| Issoftdeleted |active |
|bölüm|urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Department|
| displayName |displayName |
|Çalışan|urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers [tür EQ "Faks"]. değer |
| givenName |name.givenName |
| jobTitle |başlık |
| posta |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Manager |
| mobil |phoneNumbers[type eq "mobile"].value |
| postalCode |adresler [tür EQ "iş"]. PostaKodu |
| Proxy-adresler |e-postalar [tür EQ "Other"]. Deeri |
| fiziksel-teslim-OfficeName |adresler [tür EQ "Other"]. Miyor |
| streetAddress |adresler [tür EQ "Work"]. streetAddress |
| surname |name.familyName |
| telefon numarası |phoneNumbers[type eq "work"].value |
| User-PrincipalName |userName |

**Kullanıcı ve grup özniteliklerinin örnek listesi**

| Azure Active Directory grubu | urn: IETF: params: Scim: schemas: Core: 2.0: Grup |
| --- | --- |
| displayName |displayName |
| posta |emails[type eq "work"].value |
| mailNickname |displayName |
| üyeler |üyeler |
| objectId |externalId |
| proxyAddresses |e-postalar [tür EQ "Other"]. Deeri |

**Grup özniteliklerinin örnek listesi**

> [!NOTE]
> Hem kullanıcıları hem de grupları ya da burada gösterilen tüm öznitelikleri desteklemek zorunda değilsiniz; yalnızca Azure AD 'deki özniteliklerin SCıM protokolündeki özelliklerle nasıl eşlendiğine ilişkin bir başvurudur. 

SCıM RFC 'de tanımlanmış birkaç uç nokta vardır. `/User`Uç nokta ile başlayabilir ve ardından buradan genişletebilirsiniz. 

|Uç Nokta|Açıklama|
|--|--|
|/User|Bir kullanıcı nesnesi üzerinde CRUD işlemleri gerçekleştirin.|
|/Group|Bir grup nesnesi üzerinde CRUD işlemleri gerçekleştirin.|
|/Schemas|Her istemci ve hizmet sağlayıcı tarafından desteklenen öznitelik kümesi farklılık gösterebilir. Bir hizmet sağlayıcı, ve ' ı içerebilir, `name` `title` `emails` ancak başka bir hizmet sağlayıcısı `name` , ve ' ı kullanır `title` `phoneNumbers` . Şemalar uç noktası desteklenen özniteliklerin bulunmasına izin verir.|
|/Toplu|Toplu işlemler, tek bir işlemde büyük kaynak nesneleri koleksiyonu üzerinde işlemler gerçekleştirmenize olanak tanır (örneğin, büyük bir grup için üyelikleri güncelleştirme).|
|/ServiceProviderConfig|Desteklenen SCıM standardının özelliklerine ilişkin ayrıntıları, örneğin desteklenen kaynakları ve kimlik doğrulama yöntemini sağlar.|
|/ResourceTypes|Her kaynakla ilgili meta verileri belirtir.|

**Uç noktaların örnek listesi**

> [!NOTE]
> `/Schemas`Özel öznitelikleri desteklemek için uç noktasını kullanın veya bir istemcinin en güncel şemayı otomatik olarak almasına olanak sağlar. `/Bulk`Grupları desteklemek için uç noktayı kullanın.

## <a name="understand-the-aad-scim-implementation"></a>AAD SCıM uygulamasını anlama

Bir SCıM 2,0 kullanıcı yönetim API 'sini desteklemek için, bu bölümde AAD SCıM istemcisinin nasıl uygulandığı açıklanmaktadır ve SCıM protokol isteği işleme ve yanıtlarınızı nasıl modelleneceğini gösterir.

> [!IMPORTANT]
> Azure AD SCıM uygulamasının davranışı en son 18 Aralık 2018 tarihinde güncelleştirildi. Nelerin değiştiği hakkında daha fazla bilgi için bkz. [Azure AD Kullanıcı sağlama hizmeti 'Nin SCIM 2,0 protokol uyumluluğu](application-provisioning-config-problem-scim-compatibility.md).

[SCIM 2,0 protokol belirtimi](http://www.simplecloud.info/#Specification)içinde, uygulamanız bu gereksinimleri desteklemelidir:

|Gereksinim|Başvuru notları (SCıM Protokolü)|
|-|-|
|Kullanıcıları ve isteğe bağlı olarak grupları oluşturma|[Bölüm 3,3](https://tools.ietf.org/html/rfc7644#section-3.3)|
|Düzeltme istekleri olan kullanıcıları veya grupları değiştirme|[Bölüm 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2). Destekleme, grupların ve kullanıcıların bir performansa göre sağlanmasını sağlar.|
|Daha önce oluşturulan bir kullanıcı veya grup için bilinen bir kaynağı alma|[Bölüm 3.4.1](https://tools.ietf.org/html/rfc7644#section-3.4.1)|
|Kullanıcıları veya grupları sorgulama|[Bölüm 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Varsayılan olarak, kullanıcılar, `id` ve tarafından sorgulanır ve grupları tarafından `username` `externalId` sorgulanır `displayName` .|
|Kullanıcıyı KIMLIĞE ve yöneticiye göre sorgulama|Bölüm 3.4.2|
|Grupları KIMLIĞE ve üyeye göre sorgula|Bölüm 3.4.2|
|Grup kaynağı sorgulanırken filtre [Excludedattributes = Üyeler](#get-group)|Bölüm 3.4.2.5|
|Uygulamanızda AAD 'nin kimlik doğrulaması ve yetkilendirmesi için tek bir taşıyıcı belirtecini kabul edin.||
|Bir kullanıcıyı geçici olarak silme `active=false` ve kullanıcıyı geri yükleme `active=true`|Kullanıcı nesnesi, kullanıcının etkin olup olmadığı bir istekte döndürülmelidir. Kullanıcının döndürülmediği tek zaman, uygulamadan kalıcı olarak silinir.|
|/Schemas uç noktasını destekleme|[Bölüm 7](https://tools.ietf.org/html/rfc7643#page-30) Şema bulma uç noktası ek öznitelikleri bulmak için kullanılacaktır.|

AAD ile uyumluluğu sağlamak için bir SCıM uç noktası uygularken genel yönergeleri kullanın:

* `id` Tüm kaynaklar için gerekli bir özelliktir. Kaynak döndüren her yanıt, sıfır üye hariç olmak üzere her bir kaynağın bu özelliğe sahip olduğundan emin olmalıdır `ListResponse` .
* Bir sorgu/filtre isteğine yanıt her zaman bir olmalıdır `ListResponse` .
* Gruplar isteğe bağlıdır, ancak yalnızca SCıM uygulamasının **yama** isteklerini desteklemesi durumunda desteklenir.
* Bu, tüm kaynağı **yama** yanıtına dahil etmek için gerekli değildir.
* Microsoft AAD yalnızca şu işleçleri kullanır: `eq` , `and`
* 3.5.2 bölümünde tanımlandığı gibi, belirli bir **Düzeltme Eki** işlem değerlerinde SCIM içindeki yapısal öğelerde büyük/küçük harfe duyarlı bir eşleşme gerektirmez `op` . [](https://tools.ietf.org/html/rfc7644#section-3.5.2) AAD, değerlerini `op` **ekleme**, **değiştirme** ve **kaldırma** olarak yayar.
* Microsoft AAD, uç noktanın ve kimlik bilgilerinin geçerli olduğundan emin olmak için rastgele bir Kullanıcı ve grup getirme istekleri yapar. Ayrıca, [Azure Portal](https://portal.azure.com) **Test bağlantı** akışının bir parçası olarak da yapılır. 
* Kaynakların sorgulanabileceği öznitelik, [Azure Portal](https://portal.azure.com)uygulamada eşleşen bir öznitelik olarak ayarlanmalıdır, bkz. [Kullanıcı sağlama öznitelik eşlemelerini özelleştirme](customize-application-attributes.md).
* Yetkilendirmeler özniteliği desteklenmiyor.
* SCıM uç noktanıza HTTPS desteği.
* [Şema bulma](#schema-discovery)
  * Şema bulma işlemi şu anda özel uygulamada desteklenmiyor, ancak belirli Galeri uygulamalarında kullanılıyor. İleri, şema bulma bir bağlayıcıya ek öznitelikler eklemek için birincil yöntem olarak kullanılacaktır. 
  * Bir değer yoksa, null değerler göndermez.
  * Özellik değerleri ortası (örn. readWrite) olmalıdır.
  * Bir liste yanıtı döndürmelidir.
  
### <a name="user-provisioning-and-deprovisioning"></a>Kullanıcı hazırlama ve sağlamayı kaldırma

Aşağıdaki çizimde, AAD 'nin uygulamanızın kimlik deposundaki bir kullanıcının yaşam döngüsünü yönetmek için bir SCıM hizmetine gönderdiği iletiler gösterilmektedir.  

![Kullanıcı hazırlama ve sağlamayı kaldırma sırasını gösterir](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Kullanıcı hazırlama ve sağlamayı kaldırma sırası*

### <a name="group-provisioning-and-deprovisioning"></a>Grup sağlama ve sağlamayı kaldırma

Grup sağlama ve sağlamayı kaldırma isteğe bağlıdır. Uygulanan ve etkin olduğunda, aşağıdaki çizimde AAD 'nin uygulamanızın kimlik deposundaki bir grubun yaşam döngüsünü yönetmek için bir SCıM hizmetine gönderdiği iletiler gösterilmektedir. Bu iletiler kullanıcılar hakkındaki iletilerden iki şekilde farklılık gösterir:

* Grupları alma istekleri, Üyeler özniteliğinin, isteğe yanıt olarak belirtilen herhangi bir kaynaktan dışlanacağını belirtir.  
* Bir başvuru özniteliğinin belirli bir değere sahip olup olmadığını belirleme istekleri, Üyeler özniteliği hakkında isteklerdir.  

![Grup sağlama ve sağlamayı kaldırma sırasını gösterir](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Grup sağlama ve sağlamayı kaldırma sırası*

### <a name="scim-protocol-requests-and-responses"></a>SCıM protokol istekleri ve yanıtları
Bu bölümde AAD SCıM istemcisi tarafından yayılan örnek SCıM istekleri ve örnek beklenen yanıtlar sağlanmaktadır. En iyi sonuçlar için, bu istekleri bu biçimde işleyecek ve beklenen yanıtları yaymak üzere uygulamanızı kodmalısınız.

> [!IMPORTANT]
> AAD Kullanıcı sağlama hizmeti 'nin aşağıda açıklanan işlemleri nasıl ve ne zaman yaydığı hakkında bilgi edinmek için bkz. sağlama [döngüleri: başlangıç ve artımlı](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) , [sağlama nasıl?](how-provisioning-works.md).

[Kullanıcı Işlemleri](#user-operations)
  - [Kullanıcı oluştur](#create-user) ([istek](#request)  /  [yanıtı](#response))
  - [Kullanıcı al](#get-user) ([istek](#request-1)  /  [yanıtı](#response-1))
  - [Sorguya göre Kullanıcı al](#get-user-by-query) ([istek](#request-2)  /  [yanıtı](#response-2))
  - [Sorgu sıfır sonuçları](#get-user-by-query---zero-results) ([istek](#request-3)  /  [yanıtı](#response-3)) ile Kullanıcı al
  - [Kullanıcı güncelleştirme [çok değerli özellikler]](#update-user-multi-valued-properties) ([istek](#request-4)  /  [yanıtı](#response-4))
  - [Kullanıcı güncelleştirme [tek değerli özellikler]](#update-user-single-valued-properties) ([istek](#request-5)  /  [yanıtı](#response-5)) 
  - [Kullanıcıyı devre dışı bırak](#disable-user) ([istek](#request-14)  /  [yanıtı](#response-14))
  - [Kullanıcı silme](#delete-user) ([istek](#request-6)  /  [yanıtı](#response-6))

[Grup Işlemleri](#group-operations)
  - [Grup Oluştur](#create-group) ([istek](#request-7)  /  [yanıtı](#response-7))
  - [Grup Al](#get-group) ([istek](#request-8)  /  [yanıtı](#response-8))
  - [Group By DisplayName 'e göre Al](#get-group-by-displayname) ([istek](#request-9)  /  [yanıtı](#response-9))
  - [Güncelleştirme grubu [Üye olmayan öznitelikler]](#update-group-non-member-attributes) ([istek](#request-10)  /  [yanıtı](#response-10))
  - [Güncelleştirme grubu [Üye Ekleme]](#update-group-add-members) ([istek](#request-11)  /  [yanıtı](#response-11))
  - [Güncelleştirme grubu [üyeleri kaldır]](#update-group-remove-members) ([istek](#request-12)  /  [yanıtı](#response-12))
  - [Grup silme](#delete-group) ([istek](#request-13)  /  [yanıtı](#response-13))

[Şema bulma](#schema-discovery)
  - [Şemayı bul](#discover-schema) ([istek](#request-15)  /  [yanıtı](#response-15))

### <a name="user-operations"></a>Kullanıcı Işlemleri

* Kullanıcılar veya öznitelikleri tarafından sorgulanabilir `userName` `email[type eq "work"]` .  

#### <a name="create-user"></a>Kullanıcı Oluştur

##### <a name="request"></a>İstek

*POST/Users*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Yanıt

*HTTP/1.1 201 oluşturuldu*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Get User

###### <a name="request"></a><a name="request-1"></a>İstek
*/Users/5d48a0a8e9f04aa38008 al* 

###### <a name="response-user-found"></a><a name="response-1"></a>Yanıt (Kullanıcı bulundu)
*HTTP/1.1 200 TAMAM*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>İstek
*/Users/5171a35d82074e068ce2 al* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Yanıt (Kullanıcı bulunamadı. Ayrıntı gerekli değildir, yalnızca durum olur.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Sorguya göre Kullanıcı al

##### <a name="request"></a><a name="request-2"></a>İstek

*GET/Users? Filter = userName EQ "Test_User_dfeef4c5-5681 -4387-B016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Yanıt

*HTTP/1.1 200 TAMAM*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="get-user-by-query---zero-results"></a>Sorguya göre Kullanıcı al-sıfır sonuçları

##### <a name="request"></a><a name="request-3"></a>İstek

*/Users al? Filter = userName EQ "varolmayan kullanıcı"*

##### <a name="response"></a><a name="response-3"></a>Yanıt

*HTTP/1.1 200 TAMAM*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-user-multi-valued-properties"></a>Kullanıcı güncelleştirme [çok değerli özellikler]

##### <a name="request"></a><a name="request-4"></a>İstek

*PATCH/Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>Yanıt

*HTTP/1.1 200 TAMAM*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Kullanıcı güncelleştirme [tek değerli özellikler]

##### <a name="request"></a><a name="request-5"></a>İstek

*PATCH/Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>Yanıt

*HTTP/1.1 200 TAMAM*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>Disable User

##### <a name="request"></a><a name="request-14"></a>İstek

*PATCH/Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>Yanıt

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Kullanıcıyı Silme

##### <a name="request"></a><a name="request-6"></a>İstek

*/Users/5171a35d82074e068ce2 HTTP/1.1 SILME*

##### <a name="response"></a><a name="response-6"></a>Yanıt

*HTTP/1.1 204 Içerik yok*

### <a name="group-operations"></a>Grup Işlemleri

* Gruplar her zaman bir boş üye listesiyle oluşturulur.
* Gruplar, `displayName` özniteliğiyle sorgulanabilir.
* Grup düzeltme eki isteğine güncelleştirme, yanıtta bir *HTTP 204 içeriği* getirmelidir. Tüm üyelerin listesini içeren bir gövde döndürülmesi önerilmez.
* Grubun tüm üyelerini döndürmeyi desteklemek gerekli değildir.

#### <a name="create-group"></a>Grup Oluşturma

##### <a name="request"></a><a name="request-7"></a>İstek

*Son/gruplar HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>Yanıt

*HTTP/1.1 201 oluşturuldu*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Get Group

##### <a name="request"></a><a name="request-8"></a>İstek

*/Groups/40734ae655284ad3abcc? excludedAttributes = Members HTTP/1.1 alın*

##### <a name="response"></a><a name="response-8"></a>Yanıt
*HTTP/1.1 200 TAMAM*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Grubu displayName 'e göre al

##### <a name="request"></a><a name="request-9"></a>İstek
*/Groups al? excludedAttributes = Members&Filter = displayName EQ "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Yanıt

*HTTP/1.1 200 TAMAM*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Güncelleştirme grubu [Üye olmayan öznitelikler]

##### <a name="request"></a><a name="request-10"></a>İstek

*PATCH/Groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>Yanıt

*HTTP/1.1 204 Içerik yok*

### <a name="update-group-add-members"></a>Güncelleştirme grubu [Üye Ekleme]

##### <a name="request"></a><a name="request-11"></a>İstek

*PATCH/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>Yanıt

*HTTP/1.1 204 Içerik yok*

#### <a name="update-group-remove-members"></a>Güncelleştirme grubu [üyeleri kaldır]

##### <a name="request"></a><a name="request-12"></a>İstek

*PATCH/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>Yanıt

*HTTP/1.1 204 Içerik yok*

#### <a name="delete-group"></a>Grubu Silme

##### <a name="request"></a><a name="request-13"></a>İstek

*/Groups/cdb1ce18f65944079d37 HTTP/1.1 SILME*

##### <a name="response"></a><a name="response-13"></a>Yanıt

*HTTP/1.1 204 Içerik yok*

### <a name="schema-discovery"></a>Şema bulma
#### <a name="discover-schema"></a>Şemayı bul

##### <a name="request"></a><a name="request-15"></a>İstek
*/Schemas al* 
##### <a name="response"></a><a name="response-15"></a>Yanıt
*HTTP/1.1 200 TAMAM*
```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:ListResponse"
    ],
    "itemsPerPage": 50,
    "startIndex": 1,
    "totalResults": 3,
    "Resources": [
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:core:2.0:User",
    "name" : "User",
    "description" : "User Account",
    "attributes" : [
      {
        "name" : "userName",
        "type" : "string",
        "multiValued" : false,
        "description" : "Unique identifier for the User, typically
used by the user to directly authenticate to the service provider.
Each User MUST include a non-empty userName value.  This identifier
MUST be unique across the service provider's entire set of Users.
REQUIRED.",
        "required" : true,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "server"
      },                
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
        "/v2/Schemas/urn:ietf:params:scim:schemas:core:2.0:User"
    }
  },
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:core:2.0:Group",
    "name" : "Group",
    "description" : "Group",
    "attributes" : [
      {
        "name" : "displayName",
        "type" : "string",
        "multiValued" : false,
        "description" : "A human-readable name for the Group.
REQUIRED.",
        "required" : false,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "none"
      },
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
        "/v2/Schemas/urn:ietf:params:scim:schemas:core:2.0:Group"
    }
  },
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
    "name" : "EnterpriseUser",
    "description" : "Enterprise User",
    "attributes" : [
      {
        "name" : "employeeNumber",
        "type" : "string",
        "multiValued" : false,
        "description" : "Numeric or alphanumeric identifier assigned
to a person, typically based on order of hire or association with an
organization.",
        "required" : false,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "none"
      },
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
"/v2/Schemas/urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
    }
  }
]
}
```

### <a name="security-requirements"></a>Güvenlik gereksinimleri
**TLS protokol sürümleri**

Kabul edilebilir tek TLS protokol sürümleri, TLS 1,2 ve TLS 1,3 ' dir. Başka TLS sürümüne izin verilmez. SSL sürümüne izin verilmez. 
- RSA anahtarları en az 2.048 bit olmalıdır.
- ECC anahtarları, onaylanmış bir eliptik eğri kullanılarak oluşturulan en az 256 bit olmalıdır

**Anahtar uzunlukları**

Tüm hizmetlerin, yeterli uzunlukta şifreleme anahtarları kullanılarak oluşturulan X. 509.440 sertifikalarını kullanması gerekir, anlamı:

**Şifre paketleri**

Tüm hizmetlerin aşağıdaki şifre paketlerini kullanmak için aşağıda belirtilen kesin sırada yapılandırılması gerekir. Yalnızca bir RSA sertifikanız varsa, yüklü ECDSA şifre paketlerinin herhangi bir etkisi olmadığını unutmayın. </br>

TLS 1,2 şifre paketleri minimum çubuğu:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>IP aralıkları
Azure AD sağlama hizmeti şu anda [burada](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all)listelenen AZUREACTIVEDIRECTORY Için IP aralıkları altında çalışır. Azure AD sağlama hizmeti 'nin uygulamanıza bağlanmasına izin vermek için AzureActiveDirectory etiketinin altına listelenen IP aralıklarını ekleyebilirsiniz. Hesaplanan adresler için IP aralığı listesini dikkatle gözden geçirmeniz gerekeceğini unutmayın. ' 40.126.25.32 ' gibi bir adres, IP aralığı listesinde ' 40.126.0.0/18 ' olarak temsil edilebilir. Ayrıca, aşağıdaki [API 'yi](/rest/api/virtualnetwork/servicetags/list)kullanarak IP aralığı listesini programlı bir şekilde alabilirsiniz.

## <a name="build-a-scim-endpoint"></a>SCıM uç noktası oluşturma

Şemanızı tasarladıktan ve Azure AD SCıM uygulamasını anladığınıza göre, SCıM uç noktanızı geliştirmeye başlamanızı sağlayabilirsiniz. Sıfırdan başlamak ve uygulamayı tamamen kendi kendinize oluşturmak yerine, SCıM topluluğu tarafından yayımlanan bir dizi açık kaynaklı SCıM kitaplıklarını kullanabilirsiniz.

Örnekler de dahil olmak üzere bir SCıM uç noktası oluşturma hakkında yönergeler için bkz. [Örnek BIR SCIM uç noktası geliştirme](use-scim-to-build-users-and-groups-endpoints.md).

Azure AD sağlama ekibi tarafından yayımlanan açık kaynaklı .NET Core [başvuru kodu örneği](https://aka.ms/SCIMReferenceCode) , geliştirmeye başlayabilmenizi sağlayan bir kaynaktır. SCıM uç noktanızı oluşturduktan sonra test etmek isteyeceksiniz. Başvuru kodunun bir parçası olarak verilen [Postman testleri](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) koleksiyonunu kullanabilir veya [yukarıda](#user-operations)belirtilen örnek istekler/yanıtlar üzerinden çalıştırabilirsiniz.  

   > [!Note]
   > Başvuru kodu, SCıM uç noktanızı oluşturmaya başlamanıza ve "olduğu gıbı" sağlanmaya yardımcı olmak için tasarlanmıştır. Topluluk katkılarına, kodu oluşturmaya ve tutmaya yardımcı olmak için hoş geldiniz.

Çözüm, _Microsoft. SCIM_ ve _Microsoft. SCIM. webhostsample_ olmak üzere iki projeden oluşur.

_Microsoft. SCIM_ PROJESI, SCIM belirtimine uyan Web hizmetinin bileşenlerini tanımlayan kitaplıktır. _Microsoft. SCıM. IProvider_ arabirimini bildirir, istekler, bir kimlik deposunda çalışacak şekilde programlanan, sağlayıcının yöntemlerine yapılan çağrılara çevrilir.

![Döküm: sağlayıcının yöntemlerine yapılan çağrılara çevrilen bir istek](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

_Microsoft. SCıM. WebHostSample_ projesi, _boş_ şablonu temel alan bir Visual Studio ASP.NET Core Web uygulamasıdır. Bu, örnek kodun kapsayıcılar içinde veya Internet Information Services içinde barındırılan tek başına olarak dağıtılmasını sağlar. Ayrıca, sınıfları bir örnek kimlik deposu olarak bellekte tutarak _Microsoft. SCıM. ıprovider_ arabirimini de uygular.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Özel bir SCıM uç noktası oluşturma

SCıM hizmeti, kök sertifika yetkilisinin aşağıdaki adlardan biri olduğu bir HTTP adresi ve sunucu kimlik doğrulama sertifikasına sahip olmalıdır:

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* Coğrafi güven
* GlobalSign
* Go Daddy
* VeriSign
* WoSign
* DST kök CA x3

.NET Core SDK, geliştirme sırasında kullanılabilecek bir HTTPS geliştirme sertifikası içerir, sertifika ilk çalıştırma deneyiminin bir parçası olarak yüklenir. ASP.NET Core Web uygulamasını nasıl çalıştırdığınıza bağlı olarak, farklı bir bağlantı noktasını dinler:

* Microsoft. SCıM. WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

ASP.NET Core ' de HTTPS hakkında daha fazla bilgi için aşağıdaki bağlantıyı kullanın: [ASP.NET Core https 'Yi zorla](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Uç nokta kimlik doğrulamasını işleme

Azure Active Directory istekleri OAuth 2,0 taşıyıcı belirtecini içerir. İsteği alan herhangi bir hizmet, beklenen Azure Active Directory kiracının Azure Active Directory olarak veren tarafından doğrulanmalıdır.

Belirteçte veren, gibi bir ISS talebi tarafından tanımlanır `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"` . Bu örnekte, talep değerinin temel adresi `https://sts.windows.net` veren olarak Azure Active Directory tanımlar, göreli adres segmenti, _cbb1a5ac-f33b-45fa-9BF5-f37db0fed422_, belirtecin verildiği Azure Active Directory kiracının benzersiz tanımlayıcısıdır.

Belirtecin hedef kitlesi, galerideki uygulamanın uygulama şablonu KIMLIĞI olacaktır, tek bir kiracıya kayıtlı uygulamaların her biri `iss` SCIM istekleriyle aynı talebi alabilir. Tüm özel uygulamalar için uygulama şablonu KIMLIĞI, _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_' dir. Azure AD sağlama hizmeti tarafından oluşturulan belirteç yalnızca test için kullanılmalıdır. Üretim ortamlarında kullanılmamalıdır.

Örnek kodda, istekler Microsoft. AspNetCore. Authentication. Jwttaşıyıcı paketi kullanılarak doğrulanır. Aşağıdaki kod, belirli bir kiracı için Azure Active Directory tarafından verilen taşıyıcı belirteci kullanılarak hizmet uç noktalarından herhangi birine yönelik isteklerin kimliklerinin doğrulanmasını zorunlu kılar:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

Ayrıca, belirtilen [Postman testlerinin](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) kullanılması ve localhost kullanarak yerel hata ayıklama gerçekleştirmek için bir taşıyıcı belirteci de gereklidir. Örnek kod, geliştirme aşamasında kimlik doğrulama seçeneklerini değiştirmek için ASP.NET Core ortamları kullanır ve kendinden imzalı bir belirteç kullanmayı etkinleştirir.

ASP.NET Core birden çok ortam hakkında daha fazla bilgi için bkz. [ASP.NET Core birden çok ortam kullanma](/aspnet/core/fundamentals/environments).

Aşağıdaki kod, hizmetin uç noktalarından herhangi birine yönelik isteklerin kimliklerinin, özel anahtarla imzalanmış bir taşıyıcı belirteci kullanılarak doğrulanmasını sağlar:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    if (_env.IsDevelopment())
    {
        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
        })
        .AddJwtBearer(options =>
        {
            options.TokenValidationParameters =
                new TokenValidationParameters
                {
                    ValidateIssuer = false,
                    ValidateAudience = false,
                    ValidateLifetime = false,
                    ValidateIssuerSigningKey = false,
                    ValidIssuer = "Microsoft.Security.Bearer",
                    ValidAudience = "Microsoft.Security.Bearer",
                    IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                };
        });
    }
...
```

Geçerli bir taşıyıcı belirteci almak için belirteç denetleyicisine bir GET isteği gönderin, _GenerateJSONWebToken_ yöntemi, geliştirme için yapılandırılmış parametrelerle eşleşen bir belirteç oluşturmaktan sorumludur:

```csharp
private string GenerateJSONWebToken()
{
    // Create token key
    SymmetricSecurityKey securityKey =
        new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
    SigningCredentials credentials =
        new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

    // Set token expiration
    DateTime startTime = DateTime.UtcNow;
    DateTime expiryTime = startTime.AddMinutes(120);

    // Generate the token
    JwtSecurityToken token =
        new JwtSecurityToken(
            "Microsoft.Security.Bearer",
            "Microsoft.Security.Bearer",
            null,
            notBefore: startTime,
            expires: expiryTime,
            signingCredentials: credentials);

    string result = new JwtSecurityTokenHandler().WriteToken(token);
    return result;
}
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Kullanıcıları hazırlama ve sağlamayı kaldırma işlemlerini işleme

***Örnek 1. Hizmeti eşleşen bir kullanıcı için sorgulama***

Azure Active Directory (AAD), `externalId` AAD 'deki bir kullanıcının Mailrumuz öznitelik değeriyle eşleşen bir öznitelik değeri olan bir kullanıcı için hizmeti sorgular. Sorgu, bu örnek gibi bir Köprü Metni Aktarım Protokolü (HTTP) isteği olarak ifade edilir. burada jbaşak, Azure Active Directory bir kullanıcının Mailtakma adı örneğidir.

>[!NOTE]
> Bu yalnızca bir örnektir. Tüm kullanıcıların bir Mailrumuz özniteliği olmaz ve bir Kullanıcı, dizinde benzersiz olmayabilir. Ayrıca, eşleme için kullanılan öznitelik (Bu örnekte olduğu gibi `externalId` ) [AAD öznitelik eşlemelerinde](customize-application-attributes.md)yapılandırılabilir.

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

Örnek kodda, istek hizmetin sağlayıcısının QueryAsync yöntemine bir çağrıya çevrilir. Bu yöntemin imzası aşağıda verilmiştir: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource is defined in 
// Microsoft.SCIM.Schemas.  
// Microsoft.SCIM.IQueryParameters is defined in 
// Microsoft.SCIM.Protocol.  

Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

Örnek sorguda, özniteliği için verilen değere sahip olan bir kullanıcı için `externalId` , QueryAsync yöntemine geçirilen bağımsız değişkenlerin değerleri şunlardır:

* parametrelere. AlternateFilters. Count: 1
* parametrelere. AlternateFilters. ElementAt (0). AttributePath: "externalId"
* parametrelere. AlternateFilters. ElementAt (0). ComparisonOperator: ComparisonOperator. Equals
* parametrelere. AlternateFilter. ElementAt (0). ComparisonValue: "jbaşak"

***Örnek 2. Kullanıcı sağlama***

Bir kullanıcının Mailrumuz özniteliği değeriyle eşleşen bir öznitelik değeri olan bir kullanıcı için Web hizmetine yapılan bir sorgunun yanıtı `externalId` hiçbir Kullanıcı döndürmezse, AAD, HIZMETIN AAD 'deki bir kullanıcıya karşılık gelen bir Kullanıcı sağlamasını ister.  Bu tür bir istek örneği aşağıda verilmiştir: 

```
POST https://.../scim/Users HTTP/1.1
Authorization: Bearer ...
Content-type: application/scim+json
{
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung@testuser.com",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

Örnek kodda istek, hizmetin sağlayıcısının CreateAsync metoduna bir çağrıya çevrilir. Bu yöntemin imzası aşağıda verilmiştir: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource is defined in 
// Microsoft.SCIM.Schemas.  

Task<Resource> CreateAsync(IRequest<Resource> request);
```

Bir Kullanıcı sağlama isteğinde, kaynak bağımsız değişkeninin değeri Microsoft. SCIM. schemas kitaplığında tanımlanan Microsoft. SCıM. Core2EnterpriseUser sınıfının bir örneğidir.  Kullanıcı sağlama isteği başarılı olursa, yöntemin uygulanması, tanımlayıcı özelliği değeri ile yeni sağlanan kullanıcının benzersiz tanımlayıcısı olarak ayarlanan Microsoft. SCıM. Core2EnterpriseUser sınıfının bir örneğini döndürmesi beklenir.  

***Örnek 3. Kullanıcının geçerli durumunu sorgulama*** 

Bir SCıM tarafından alınan bir kimlik deposunda bulunan olarak bilinen bir kullanıcıyı güncelleştirmek için Azure Active Directory, bu kullanıcının geçerli durumunu hizmetten şu gibi bir istek ile isteyerek devam eder: 

```
GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
```

Örnek kodda, istek hizmetin sağlayıcısının RetrieveAsync metoduna bir çağrıya çevrilir. Bu yöntemin imzası aşağıda verilmiştir: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource and 
// Microsoft.SCIM.IResourceRetrievalParameters 
// are defined in Microsoft.SCIM.Schemas 

Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

Bir kullanıcının geçerli durumunu alma isteği örneğinde, parametre bağımsız değişkeninin değeri olarak belirtilen nesnenin özelliklerinin değerleri aşağıdaki gibidir: 
  
* Tanımlayıcı: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: user"

***Örnek 4. Bir başvuru özniteliğinin güncelleştirileceği değeri sorgula*** 

Bir başvuru özniteliği güncelleştirilürse Azure Active Directory, hizmet tarafından kullanılan kimlik deposundaki başvuru özniteliğinin geçerli değerinin Azure Active Directory ' deki bu özniteliğin değeriyle eşleşip eşleşmediğini belirleme hizmetini sorgular. Kullanıcılar için, geçerli değerin bu şekilde sorgulandığı tek özniteliği, yönetici özniteliğidir. Bir Kullanıcı nesnesinin Manager özniteliğinin Şu anda belirli bir değere sahip olup olmadığını belirleme isteğine bir örnek aşağıda verilmiştir: örnek kodda, isteğin, hizmetin sağlayıcısının QueryAsync yöntemine bir çağrıya çevrilmesi. Parametre bağımsız değişkeninin değeri olarak belirtilen nesnenin özelliklerinin değeri aşağıdaki gibidir: 
  
* parametrelere. AlternateFilters. Count: 2
* parametrelere. AlternateFilters. ElementAt (x). AttributePath: "KIMLIK"
* parametrelere. AlternateFilters. ElementAt (x). ComparisonOperator: ComparisonOperator. Equals
* parametrelere. AlternateFilter. ElementAt (x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parametrelere. AlternateFilters. ElementAt (y). AttributePath: "Yönetici"
* parametrelere. AlternateFilters. ElementAt (y). ComparisonOperator: ComparisonOperator. Equals
* parametrelere. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453A-919d-413861904646"
* parametrelere. RequestedAttributePaths. ElementAt (0): "KIMLIK"
* parametrelere. SchemaIdentifier: "urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: user"

Burada, x dizininin değeri 0 olabilir ve y dizininin değeri 1 olabilir, ya da x değeri 1 olabilir ve Filter sorgu parametresinin ifadelerinin sırasına bağlı olarak y değeri 0 olabilir.   

***Örnek 5. Bir kullanıcıyı güncelleştirmek için Azure AD 'den bir SCıM hizmetine istek*** 

Bir kullanıcıyı güncelleştirmek için bir SCıM hizmetine Azure Active Directory bir istek örneği aşağıda verilmiştir: 

```
PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
Content-type: application/scim+json
{
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

Örnek kodda, istek hizmetin sağlayıcısının UpdateAsync metoduna bir çağrıya çevrilir. Bu yöntemin imzası aşağıda verilmiştir: 

```csharp
// System.Threading.Tasks.Tasks and 
// System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in
// Microsoft.SCIM.Service.
// Microsoft.SCIM.IPatch, 
// is defined in Microsoft.SCIM.Protocol. 

Task UpdateAsync(IRequest<IPatch> request);
```

Bir kullanıcıyı güncelleştirme isteğine örnek olarak, Patch bağımsız değişkeninin değeri olarak girilen nesne bu özellik değerlerine sahiptir: 

|Bağımsız Değişken|Değer|
|-|-|
|ResourceIdentifier. Identifier|"54D382A4-2050-4C03-94D1-E769F1D15682"|
|ResourceIdentifier. SchemaIdentifier|"urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: user"|
|(PatchRequest2 olarak PatchRequest). Operations. Count|1|
|(PatchRequest2 olarak PatchRequest). Operations. ElementAt (0). OperationName|OperationName. Add|
|(PatchRequest2 olarak PatchRequest). Operations. ElementAt (0). Path. AttributePath|Manager|
|(PatchRequest2 olarak PatchRequest). Operations. ElementAt (0). Değer. Count|1|
|(PatchRequest2 olarak PatchRequest). Operations. ElementAt (0). Value. ElementAt (0). Başvurunun|http://.../scim/Users/2819c223-7f76-453a-919d-413861904646|
|(PatchRequest2 olarak PatchRequest). Operations. ElementAt (0). Value. ElementAt (0). Deeri| 2819c223-7f76-453A-919d-413861904646|

***Örnek 6. Bir kullanıcının sağlamasını kaldırma***

Bir kullanıcının SCıM hizmeti tarafından alınan bir kimlik deposundan sağlanması için, AAD şöyle bir istek gönderir:

```
DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
```

Örnek kodda, istek hizmetin sağlayıcısının DeleteAsync yöntemine bir çağrıya çevrilir. Bu yöntemin imzası aşağıda verilmiştir: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.IResourceIdentifier, 
// is defined in Microsoft.SCIM.Protocol. 

Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

ResourceIdentifier bağımsız değişkeninin değeri olarak sunulan nesne, bir kullanıcının sağlamasını kaldırma isteği örneğinde bu özellik değerlerine sahiptir: 

* ResourceIdentifier. Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier. SchemaIdentifier: "urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: user"

## <a name="integrate-your-scim-endpoint-with-the-aad-scim-client"></a>SCıM uç noktanızı AAD SCıM istemcisiyle tümleştirme

Azure AD, [SCIM 2,0 protokolünün](https://tools.ietf.org/html/rfc7644)belirli bir profilini uygulayan uygulamalara atanan kullanıcıları ve grupları otomatik olarak sağlayacak şekilde yapılandırılabilir. Profilin özellikleri [Azure AD SCıM uygulamasını anlama](#understand-the-aad-scim-implementation)bölümünde belgelenmiştir.

Bu gereksinimlerle uyumluluk bildirimleri için uygulama sağlayıcınıza veya uygulama sağlayıcınızın belgelerine danışın.

> [!IMPORTANT]
> Azure AD SCıM uygulaması, Azure AD Kullanıcı sağlama hizmeti 'nin üzerine kurulmuştur. Bu, kullanıcıların Azure AD ile hedef uygulama arasında sürekli olarak eşitlenmesi için tasarlanan ve çok özel bir standart işlem kümesi uyguladığı şekilde tasarlanmıştır. Azure AD SCıM istemcisinin davranışını anlamak için bu davranışları anlamak önemlidir. Daha fazla bilgi için bkz. sağlama [döngüleri: başlangıç ve artımlı](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) , [sağlama nasıl?](how-provisioning-works.md).

### <a name="getting-started"></a>Kullanmaya başlama

Bu makalede açıklanan SCıM profilini destekleyen uygulamalar, Azure AD uygulama galerisinde "Galeri dışı uygulama" özelliği kullanılarak Azure Active Directory bağlanabilir. Bağlantı kurulduktan sonra Azure AD, her 40 dakikada bir eşitleme işlemi çalıştırarak, atanan kullanıcılar ve gruplar için uygulamanın SCıM uç noktasını sorgular ve atama ayrıntılarına göre bunları oluşturur veya değiştirir.

**SCıM 'i destekleyen bir uygulamaya bağlanmak için:**

1. [AAD portalında](https://aad.portal.azure.com)oturum açın. [Geliştirici programına](https://developer.microsoft.com/office/dev-program) kaydolduktan sonra, P2 lisanslarıyla Azure Active Directory ücretsiz bir denemeye erişebileceğinizi unutmayın.
1. Sol bölmeden **Kurumsal uygulamalar** ' ı seçin. Galeriden eklenen uygulamalar dahil olmak üzere, yapılandırılan tüm uygulamaların bir listesi gösterilir.
1. **+ Yeni uygulama**  >  **+ kendi uygulamanızı oluştur ' u** seçin.
1. Uygulamanız için bir ad girin, "*galeride bulamadıysanız diğer uygulamaları tümleştirin*" seçeneğini belirleyin ve uygulama nesnesi oluşturmak için **Ekle** ' yi seçin. Yeni uygulama, kurumsal uygulamalar listesine eklenir ve uygulama yönetimi ekranına açılır.

   ![Ekran görüntüsünde Azure AD Uygulama Galerisi ](media/use-scim-to-provision-users-and-groups/scim-figure-2b-1.png)
    *Azure AD Uygulama Galerisi* gösterilmektedir

   > [!NOTE]
   > Eski uygulama Galerisi deneyimini kullanıyorsanız aşağıdaki ekran kılavuzunu izleyin.
   
   ![Ekran görüntüsünde Azure AD eski uygulama Galerisi deneyimi ](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)
    *Azure AD eski uygulama Galerisi deneyimi* gösterilmektedir

1. Uygulama yönetimi ekranında, sol panelde **sağlama** ' yı seçin.
1. **Sağlama modu** menüsünde **Otomatik**' i seçin.

   ![Örnek: Azure portal bir uygulamanın sağlama sayfası](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Azure portal sağlamayı yapılandırma*

1. **Kiracı URL 'si** alanına uygulamanın SCIM uç noktasının URL 'sini girin. Örnek: `https://api.contoso.com/scim/`
1. SCıM uç noktası, Azure AD 'den başka bir verenin bir OAuth taşıyıcı belirteci gerektiriyorsa, gerekli OAuth taşıyıcı belirtecini isteğe bağlı **gizli belirteç** alanına kopyalayın. Bu alan boş bırakılırsa Azure AD, Azure AD 'den her istekle verilen bir OAuth taşıyıcı belirteci içerir. Kimlik sağlayıcısı olarak Azure AD kullanan uygulamalar, bu Azure AD veren belirtecini doğrulayabilir. 
   > [!NOTE]
   > Bu alanı boş bırakmanız ve Azure AD tarafından oluşturulan bir belirtece güvenmamanız ***önerilmez.*** Bu seçenek öncelikle test amacıyla kullanılabilir.
1. SCıM uç noktasına bağlanmak Azure Active Directory denemek için **Bağlantıyı Sına** ' yı seçin. Deneme başarısız olursa, hata bilgileri görüntülenir.  

    > [!NOTE]
    > **Test bağlantısı** , mevcut olmayan bir kullanıcı için SCIM uç noktasını sorgular, bu da Azure AD yapılandırmasında eşleşen özellik olarak rastgele bir GUID kullanılıyor. Beklenen doğru yanıt, boş bir SCIM ListResponse iletisi ile HTTP 200 Tamam ' dır.

1. Uygulamaya bağlanma denemeleri başarılı olursa, yönetici kimlik bilgilerini kaydetmek için **Kaydet** ' i seçin.
1. **Eşlemeler** bölümünde, biri Kullanıcı nesneleri ve bir grup nesnesi için olmak üzere iki seçilebilir [öznitelik eşlemesi](customize-application-attributes.md)vardır. Uygulamanıza Azure Active Directory eşitlenecek öznitelikleri gözden geçirmek için her birini seçin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme işlemleri için uygulamanızdaki kullanıcıları ve grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** ' i seçin.

    > [!NOTE]
    > İsteğe bağlı olarak, "gruplar" eşlemesini devre dışı bırakarak grup nesnelerinin eşitlenmesini devre dışı bırakabilirsiniz.

1. **Ayarlar**' ın altında, **kapsam** alanı hangi kullanıcıların ve grupların eşitleneceğini tanımlar. Yalnızca **Kullanıcılar ve gruplar** sekmesinde atanan kullanıcıları ve grupları eşitlemek için **yalnızca atanmış kullanıcıları ve grupları Eşitle** (önerilir) öğesini seçin.
1. Yapılandırmanız tamamlandıktan sonra **sağlama durumunu** **Açık** olarak ayarlayın.
1. Azure AD sağlama hizmetini başlatmak için **Kaydet** ' i seçin.
1. Yalnızca atanmış kullanıcıları ve grupları eşitlese (önerilir), **Kullanıcılar ve gruplar** sekmesini seçtiğinizden emin olun ve eşitlemek istediğiniz kullanıcıları veya grupları atayın.

Başlangıç çevrimi başladıktan sonra, uygulamanızdaki sağlama hizmeti tarafından gerçekleştirilen tüm işlemleri gösteren ilerlemeyi izlemek için sol panelde **sağlama günlükleri** ' ni seçebilirsiniz. Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](check-status-user-account-provisioning.md).

> [!NOTE]
> İlk döngüyü daha sonra, hizmetin çalıştığı sürece yaklaşık 40 dakikada bir gerçekleşen daha sonraki eşitliden daha uzun sürer.

## <a name="publish-your-application-to-the-aad-application-gallery"></a>Uygulamanızı AAD uygulama galerisine yayımlayın

Birden fazla kiracı tarafından kullanılacak bir uygulama oluşturuyorsanız, Azure AD uygulama galerisinde kullanılabilir hale getirebilirsiniz. Bu, kuruluşların uygulamayı keşfetmesini ve sağlamayı yapılandırmasını kolaylaştırır. Uygulamanızı Azure AD galerisinde yayımlama ve sağlama sağlamak kolaydır. [Buradaki](../develop/v2-howto-app-gallery-listing.md)adımlara göz atın. Microsoft, uygulamanızı galerimize tümleştirme, uç noktanızı test etme ve müşterilerin kullanması için ekleme [belgelerini](../saas-apps/tutorial-list.md) yayınlama konusunda sizinle birlikte çalışacaktır.

### <a name="gallery-onboarding-checklist"></a>Galeri ekleme denetim listesi
Uygulamanızı hızla eklemek için denetim listesini kullanın ve müşterilerin sorunsuz bir dağıtım deneyimi vardır. Bu bilgiler, galeriye ekleme sırasında sizin için toplanacaktır. 
> [!div class="checklist"]
> * [SCIM 2,0](#understand-the-aad-scim-implementation) Kullanıcı ve grup uç noktasını destekleme (yalnızca bir tane gereklidir ancak her ikisi de önerilir)
> * Kullanıcıların ve grupların gecikme süresi olmadan sağlanması ve sağlanması için her kiracı için saniyede en az 25 istek desteklenir (gerekli)
> * Mühendisler için mühendislik ve destek kişileri oluşturun müşteri galerisini ekleme (gerekli)
> * uygulamanız için 3 süresi dolan test kimlik bilgileri (gerekli)
> * Aşağıda açıklandığı gibi, OAuth yetkilendirme kodu yetkisini veya uzun süreli bir belirteci destekler (gerekli)
> * Müşteri galerisinin ekleme işlemini desteklemesi için mühendislik ve destek iletişim noktası oluşturma (gerekli)
> * [Şema bulmayı destekleme (gerekli)](https://tools.ietf.org/html/rfc7643#section-6)
> * Tek bir düzeltme ekiyle birden çok grup üyeliğini güncelleştirme desteği
> * SCıM uç noktanızı genel olarak belgeleyin

### <a name="authorization-to-provisioning-connectors-in-the-application-gallery"></a>Uygulama galerisinde bağlayıcıları sağlama yetkilendirmesi
SCıM özelliği, kimlik doğrulama ve yetkilendirme için bir SCıM 'e özgü düzen tanımlamaz ve mevcut sektör standartlarının kullanımına dayanır.

|Yetkilendirme yöntemi|Avantajlar|Dezavantajlar|Destek|
|--|--|--|--|
|Kullanıcı adı ve parola (Azure AD tarafından önerilmez veya desteklenmez)|Kolayca uygulanır|Güvenli olmayan- [PA $ $Word önemi yoktur](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Galeri uygulamaları için büyük/küçük harf esasına göre desteklenir. Galeri olmayan uygulamalar için desteklenmez.|
|Uzun süreli taşıyıcı belirteci|Uzun süreli belirteçler için bir kullanıcının mevcut olması gerekmez. Yöneticiler, sağlama ayarlarken kolayca kullanılabilir.|Uzun süreli belirteçlerin, e-posta gibi güvenli olmayan yöntemler kullanmadan bir yönetici ile paylaşılması zor olabilir. |Galeri ve Galeri olmayan uygulamalar için desteklenir. |
|OAuth yetkilendirme kodu verme|Erişim belirteçleri parolalardan çok daha kısa süreli ve uzun süreli taşıyıcı belirteçlerinin sahip olmadığı otomatik bir yenileme mekanizmasına sahiptir.  İlk yetkilendirme sırasında, bir sorumluluk düzeyi ekleyerek gerçek bir kullanıcının mevcut olması gerekir. |Bir kullanıcının mevcut olmasını gerektirir. Kullanıcı kuruluştan ayrılırsa, belirteç geçersizdir ve yetkilendirmenin yeniden tamamlanması gerekir.|Galeri uygulamaları için desteklenir, ancak Galeri olmayan uygulamalar için desteklenmez. Ancak, kısa süreli test amaçları için Kullanıcı arabiriminde gizli belirteç olarak bir erişim belirteci sağlayabilirsiniz. Galeri dışı kimlik doğrulama/belirteç URL 'Lerine yönelik desteğe ek olarak, galeri dışındaki OAuth kod verme desteği kapsamımızda yer alır.|
|OAuth istemci kimlik bilgileri verme|Erişim belirteçleri parolalardan çok daha kısa süreli ve uzun süreli taşıyıcı belirteçlerinin sahip olmadığı otomatik bir yenileme mekanizmasına sahiptir. Hem yetkilendirme kodu verme hem de istemci kimlik bilgileri, aynı tür erişim belirtecini oluşturur, bu nedenle bu yöntemler arasında geçiş yapmak API 'ye saydamdır.  Sağlama tamamen otomatikleştirilebilir ve yeni belirteçler Kullanıcı etkileşimi olmadan sessizce istenebilir. ||Galeri ve Galeri olmayan uygulamalar için desteklenmez. Destek kapsamımızda.|

> [!NOTE]
> AAD sağlama yapılandırması özel uygulama kullanıcı arabiriminde belirteç alanını boş bırakmanız önerilmez. Oluşturulan belirteç öncelikle test amacıyla kullanılabilir.

### <a name="oauth-code-grant-flow"></a>OAuth kod verme akışı

Sağlama hizmeti [yetkilendirme kodu vermeyi](https://tools.ietf.org/html/rfc6749#page-24) destekler ve uygulamanızı Galeri 'de yayımlamaya yönelik isteğinizi gönderdikten sonra, takımımız aşağıdaki bilgileri toplamak için sizinle birlikte çalışacaktır:

- **Yetkilendirme URL 'si**, kaynak sahibinden Kullanıcı Aracısı yeniden yönlendirme aracılığıyla yetkilendirme elde etmek için istemcinin URL 'si. Kullanıcı, erişim yetkisi vermek için bu URL 'ye yeniden yönlendirilir. 

- **Belirteç Exchange URL**'si, genellikle istemci kimlik doğrulamasıyla bir erişim belirtecine yetki verme izni vermek için istemcinin URL 'si.

- **ISTEMCI kimliği**, yetkilendirme sunucusu kayıtlı istemciye, istemci tarafından sunulan kayıt bilgilerini temsil eden benzersiz bir dize olan istemci tanımlayıcısını verir.  İstemci tanımlayıcısı gizli değil; Kaynak sahibine gösterilir ve yalnızca istemci kimlik doğrulaması **için kullanılmamalıdır.**  

- Yetkilendirme sunucusu tarafından oluşturulan ve yalnızca yetkilendirme sunucusu için bilinen benzersiz bir değer olması gereken **gizli anahtar.** 

> [!NOTE]
> **Yetkilendirme URL 'si** ve **belirteç değişim URL 'si** Şu anda kiracı başına yapılandırılamaz.

> [!NOTE]
> İstemci parolasının pozlaması nedeniyle OAuth v1 desteklenmez. OAuth v2 desteklenir.  

En iyi uygulamalar (önerilir, ancak gerekli değildir):
* Birden çok yeniden yönlendirme URL 'sini destekler. Yöneticiler, "portal.azure.com" ve "aad.portal.azure.com" ile sağlamayı yapılandırabilir. Birden çok yeniden yönlendirme URL 'Leri desteklemek, kullanıcıların her iki portaldan da erişim yetkisi alabilmek için
* Kapalı kalma süresi olmadan kolay yenileme için birden çok gizli dizi desteği. 

#### <a name="how-to-setup-oauth-code-grant-flow"></a>OAuth kod verme akışını ayarlama

1. Azure Portal oturum açın, **Kurumsal uygulamalar**  >  **uygulama**  >  **sağlama** ' ya gidin ve **Yetkilendir**' i seçin.

   1. Azure portal kullanıcıyı, yetkilendirme URL 'sine (üçüncü taraf uygulamanın oturum açma sayfasına) yönlendirir.

   1. Yönetici, üçüncü taraf uygulama için kimlik bilgileri sağlar. 

   1. Üçüncü taraf uygulama, kullanıcıyı Azure portal geri yönlendirir ve izin kodunu sağlar 

   1. Azure AD sağlama hizmetleri, belirteç URL 'sini çağırır ve izin kodunu sağlar. Üçüncü taraf uygulama, erişim belirteci, yenileme belirteci ve sona erme tarihi ile yanıt verir

1. Sağlama çevrimi başladığında, hizmet geçerli erişim belirtecinin geçerli olup olmadığını denetler ve gerekirse yeni bir belirteç için bunu ister. Erişim belirteci, uygulamaya yapılan her istekte sağlanır ve isteğin geçerliliği her istekten önce denetlenir.

> [!NOTE]
> Galeri olmayan uygulamalarda OAuth kurulumu mümkün olmasa da, yetkilendirme sunucusundan el ile bir erişim belirteci oluşturabilir ve bunu Galeri olmayan bir uygulamaya gizli belirteç olarak girebilirsiniz. Bu, OAuth kod vermeyi destekleyen uygulama galerisine eklemeden önce, AAD SCıM istemcisiyle SCıM sunucunuzun uyumluluğunu doğrulamanızı sağlar.  

**Uzun süreli OAuth taşıyıcı belirteçleri:** Uygulamanız OAuth yetkilendirme kodu verme akışını desteklemiyorsa, bunun yerine yöneticinin sağlama tümleştirmesini ayarlamak için kullanabileceği uzun süreli bir OAuth taşıyıcı belirteci oluşturun. Belirtecin kalıcı olması gerekir, aksi takdirde, belirtecin süresi dolarsa sağlama işi [karantinaya](application-provisioning-quarantine-status.md) alınır.

Ek kimlik doğrulama ve yetkilendirme yöntemleri için [UserVoice](https://aka.ms/appprovisioningfeaturerequest)'ta bize bilgi verin.

### <a name="gallery-go-to-market-launch-check-list"></a>Galeri go-to-market başlatma onay listesi
Birleşme tümleştirmemiz için bir tanıma ve talep konusunda yardımcı olmak üzere, var olan belgelerinizi güncelleştirmenizi ve pazarlama kanallarınızın tümleştirmesinin korunmasını öneririz.  Aşağıda, başlatmayı desteklemek için tamamladığımız bir denetim listesi etkinliği kümesi verilmiştir

> [!div class="checklist"]
> * Satış ve müşteri destek ekiplerinizin farkında, hazırlayın ve tümleştirme özelliklerine göz atabildiğinden emin olun. Ekipleriniz hakkında daha fazla SSS sağlayın ve satış malzemelerinize tümleştirme dahil edin. 
> * Birleşik tümleştirmeyi, avantajları ve nasıl başlaılacağını açıklayan bir blog gönderisi veya bir yayın yayını oluşturun. [Örnek: ımprivata ve Azure Active Directory Press yayını](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
> * Müşterilerinize tümleştirmeyi yükseltmek için Twitter, Facebook veya LinkedIn gibi sosyal medya ortamınızdan yararlanın. @AzureADGönderinizi retweet. [Örnek: ımprivata Twitter gönderisi](https://twitter.com/azuread/status/1123964502909779968)
> * Ortak tümleştirmenin kullanılabilirliğini dahil etmek için pazarlama sayfalarınızı/Web sitenizi (örn. tümleştirme sayfası, iş ortağı sayfası, fiyatlandırma sayfası vb.) oluşturun veya güncelleştirin. [Örnek: Pingboard tümleştirme sayfası](https://pingboard.com/org-chart-for), [Smartsheet tümleştirme sayfası](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [Monday.com fiyatlandırma sayfası](https://monday.com/pricing/) 
> * Müşterilerin nasıl başlatıladığına ilişkin bir yardım merkezi makalesi veya teknik belgeler oluşturun. [Örnek: Envoy + Microsoft Azure Active Directory Tümleştirmesi.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
> * Müşteri iletişiminizdeki yeni tümleştirmede müşterileri uyarır (aylık bültenler, e-posta kampanyaları, ürün sürüm notları). 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Örnek SCIM uç noktası geliştirme](use-scim-to-build-users-and-groups-endpoints.md) 
>  [SaaS uygulamalarına](user-provisioning.md) 
>  Kullanıcı hazırlama ve sağlamayı kaldırma işlemlerini otomatikleştirme [Kullanıcı hazırlama](customize-application-attributes.md) 
>  Için öznitelik eşlemelerini özelleştirme [Öznitelik eşlemeleri](functions-for-customizing-application-data.md) 
>  için ifadeler yazma [Kullanıcı hazırlama](define-conditional-rules-for-provisioning-user-accounts.md) 
>  için kapsam filtreleri [Hesap sağlama bildirimleri](user-provisioning.md) 
>  [SaaS uygulamalarının nasıl tümleştirileceği hakkında öğreticiler listesi](../saas-apps/tutorial-list.md)
