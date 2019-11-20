---
title: Azure Active Directory ile SCıM Kullanıcı hazırlama | Microsoft Docs
description: Bir SCıM uç noktası oluşturmayı, SCıM API 'nizi Azure Active Directory ile tümleştirmeyi ve uygulamalarınıza kullanıcıları ve grupları sağlamayı otomatikleştirme işlemini başlatmayı öğrenin.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13a24ebd8aca3cebab7898689b00e590298a8d1e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144733"
---
# <a name="scim-user-provisioning-with-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) ile SCıM Kullanıcı sağlama

Bu makalede, bir uygulamaya Kullanıcı ve grupların sağlanması ve sağlanması otomatik hale getirmek üzere etki alanları arası kimlik yönetimi ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards)) için sistemin nasıl kullanılacağı açıklanır. SCıM belirtimi, sağlama için ortak bir Kullanıcı şeması sağlar. SAML veya OpenID Connect gibi Federasyon standartlarıyla birlikte kullanıldığında, SCıM yöneticilere erişim yönetimi için uçtan uca standartlara dayalı bir çözüm sunar.

SCıM iki uç noktanın standartlaştırılmış bir tanımıdır: bir/Users uç noktası ve bir/Groups uç noktası. Nesneleri oluşturmak, güncelleştirmek ve silmek için ortak REST fiillerini ve grup adı, Kullanıcı adı, adı, soyadı, soyadı ve e-posta gibi ortak özniteliklerin önceden tanımlanmış bir şemasını kullanır. Bir SCıM 2,0 REST API sunan uygulamalar, özel bir kullanıcı yönetimi API 'siyle çalışmayı azaltabilir veya ortadan kaldırabilir. Örneğin, tüm uyumlu SCıM istemcileri yeni bir kullanıcı girişi oluşturmak için/Users uç noktasına bir JSON nesnesinin HTTP GÖNDERISINI nasıl yapılacağını bilir. Aynı temel eylemler için biraz farklı bir API sağlamak yerine, SCıM standardına uyan uygulamalar, önceden var olan istemcilerden, araçlardan ve koddan hemen faydalanabilir. 

![SCıM ile Azure AD 'den bir uygulamaya sağlama](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCıM 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) ' de tanımlanan yönetim için standart Kullanıcı nesne şeması ve REST API 'leri, kimlik sağlayıcılarının ve uygulamaların birbirleriyle daha kolay tümleşmesine olanak tanır. Bir SCıM uç noktası oluşturan uygulama geliştiricileri, özel iş yapmak zorunda kalmadan herhangi bir SCıM uyumlu istemcisiyle tümleştirilebilir.

Bir uygulamaya sağlamanın otomatikleştirilmesi, Azure AD SCıM ile uyumlu bir SCıM uç noktası oluşturulmasını ve tümleştirmesini gerektirir. Uygulamanıza Kullanıcı ve grup sağlamaya başlamak için aşağıdaki adımları gerçekleştirin. 
    
  * **[1. Adım: Kullanıcı ve grup şemanızı tasarlayın.](#step-1-design-your-user-and-group-schema)** Uygulamanız için gereken nesneleri ve öznitelikleri tanımlayabilir ve bunların Azure AD SCıM uygulaması tarafından desteklenen Kullanıcı ve grup şemasına nasıl eşlendiğini saptayın.

  * **[2. Adım: Azure AD SCıM uygulamasını anlayın.](#step-2-understand-the-azure-ad-scim-implementation)** Azure AD SCıM istemcisinin nasıl uygulandığını anlayın ve SCıM protokol isteği işleme ve yanıtlarınızı modelleyin.

  * **[3. Adım: SCıM uç noktası oluşturma.](#step-3-build-a-scim-endpoint)** Azure AD sağlama hizmeti ile tümleştirilecek bir uç noktanın SCıM 2,0 ile uyumlu olması gerekir. Bir seçenek olarak, uç noktanızı derlemek için Microsoft ortak dil altyapısı (CLı) kitaplıklarını ve kod örneklerini kullanabilirsiniz. Bu örnekler yalnızca başvuru ve test içindir; üretim uygulamanızın bir bağımlılığı alacak şekilde kodlanmasını öneririz.

  * **[4. Adım: SCıM uç noktanızı Azure AD SCıM istemcisiyle tümleştirin.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Kuruluşunuz, Azure AD 'nin desteklediği SCıM 2,0 profilini uygulayan bir üçüncü taraf uygulama kullanıyorsa, kullanıcıların ve grupların sağlanması ve sağlanması için hemen otomatikleştirmeye başlayabilirsiniz.

  * **[5. Adım: uygulamanızı Azure AD Uygulama Galerisi 'nde yayımlayın.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Müşterilerin uygulamanızı bulmasını ve sağlamayı kolay bir şekilde yapılandırmasını kolaylaştırın. 

![SCıM uç noktasını Azure AD ile tümleştirme adımları](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>1\. Adım: Kullanıcı ve grup şemanızı tasarlama

Her uygulama, Kullanıcı veya grup oluşturmak için farklı öznitelikler gerektirir. Uygulamanızın gerektirdiği nesneleri (Kullanıcı, Grup) ve öznitelikleri (ad, yönetici, iş unvanı vb.) tanımlayarak tümleştirmenizi başlatın. Daha sonra, uygulamanızın gerektirdiği özniteliklerin Azure AD 'de bir özniteliğe ve SCıM RFC 'ye nasıl eşlendiğini anlamak için aşağıdaki tabloyu kullanabilirsiniz. Özniteliklerin Azure AD ile SCıM uç noktanız arasında nasıl eşleştirildiğini [özelleştirebilirsiniz](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) . 

Kullanıcı kaynakları, bu protokol belirtimine dahil olan `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`şema tanımlayıcısı tarafından tanımlanır: https://tools.ietf.org/html/rfc7643.  Azure AD 'deki kullanıcı özniteliklerinin özniteliklerin varsayılan eşlemesi, Tablo 1 ' de verilmiştir.  

Grup kaynaklarının şema tanımlayıcısı tarafından tanımlanan `urn:ietf:params:scim:schemas:core:2.0:Group`. Tablo 2, Azure AD 'deki grupların özniteliklerinin grup kaynaklarının özniteliklerine varsayılan eşlemesini gösterir.

Hem kullanıcıları hem de grupları veya aşağıda gösterilen tüm öznitelikleri desteklememeniz gerekmez. Bunlar, Azure AD 'deki özniteliklerin genellikle SCıM protokolündeki özelliklerle nasıl eşlendiğine yönelik bir başvurudur.  

### <a name="table-1-default-user-attribute-mapping"></a>Tablo 1: Varsayılan kullanıcı öznitelik eşlemesi

| Azure Active Directory kullanıcısı | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |etkin |
| displayName |displayName |
| Faks TelephoneNumber |PhoneNumber [türü eq "faks"] .value |
| givenName |name.givenName |
| İş Unvanı |title |
| posta |e-postaları [türü eq "İş"] .value |
| mailNickname |externalId |
| Yöneticisi |Yöneticisi |
| Mobil |PhoneNumber [türü eq "mobile"] .value |
| objectId |Kimlik |
| posta kodu |adresler [türü eq "İş"] .postalCode |
| Ara sunucu adresleri |e-postaları [type eq "diğer"]. Değer |
| fiziksel teslim OfficeName |adresler [type eq "diğer"]. Biçimlendirilmiş |
| streetAddress |adresler [türü eq "İş"] .streetAddress |
| Soyadı |name.familyName |
| telefon numarası |PhoneNumber [türü eq "İş"] .value |
| Kullanıcı PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tablo 2: Varsayılan grup öznitelik eşlemesi

| Azure Active Directory grubu | urn: IETF: params: Scim: schemas: Core: 2.0: Grup |
| --- | --- |
| displayName |externalId |
| posta |e-postaları [türü eq "İş"] .value |
| mailNickname |displayName |
| üyeler |üyeler |
| objectId |Kimlik |
| proxyAddresses |e-postaları [type eq "diğer"]. Değer |

## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>2\. Adım: Azure AD SCıM uygulamasını anlama
> [!IMPORTANT]
> Azure AD SCıM uygulamasının davranışı en son 18 Aralık 2018 tarihinde güncelleştirildi. Nelerin değiştiği hakkında daha fazla bilgi için bkz. [Azure AD Kullanıcı sağlama hizmeti 'Nin SCIM 2,0 protokol uyumluluğu](application-provisioning-config-problem-scim-compatibility.md).

SCıM 2,0 kullanıcı yönetim API 'sini destekleyen bir uygulama oluşturuyorsanız, bu bölümde Azure AD SCıM istemcisinin nasıl uygulandığı ayrıntılı bilgiler açıklanmaktadır. Ayrıca, SCıM protokol isteği işleme ve yanıtlarınızı nasıl modellebileceğiniz gösterilmektedir. SCıM uç noktanızı uygulandıktan sonra, önceki bölümde açıklanan yordamı izleyerek test edebilirsiniz.

[SCIM 2,0 protokol belirtimi](http://www.simplecloud.info/#Specification)içinde, uygulamanız şu gereksinimleri karşılamalıdır:

* , [SCıM protokolünün 3,3](https://tools.ietf.org/html/rfc7644#section-3.3)bölümünde olduğu gibi, kullanıcıların ve isteğe bağlı olarak grupların oluşturulmasını destekler.  
* , [SCıM protokolünün Bölüm 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2)göre, yama istekleri olan kullanıcı veya grupların değiştirilmesini destekler.  
* , [SCıM protokolünün bölüm başına 3.4.1](https://tools.ietf.org/html/rfc7644#section-3.4.1)göre daha önce oluşturulmuş bir kullanıcı veya grup için bilinen bir kaynağı almayı destekler.  
* , [SCıM protokolünün](https://tools.ietf.org/html/rfc7644#section-3.4.2)bölüm başına 3.4.2 göre Kullanıcı veya grupların sorgulanmasını destekler.  Varsayılan olarak, kullanıcılar `id` alınır ve `username` ve `externalid`tarafından sorgulanır ve gruplar `displayName`tarafından sorgulanır.  
* , SCıM protokolünün Bölüm 3.4.2 göre, kullanıcının KIMLIĞE ve yöneticiye göre sorgulanmasını destekler.  
* , SCıM protokolünün Bölüm 3.4.2 göre grupların KIMLIĞE ve üyeye göre sorgulanmasını destekler.  
* Azure AD 'nin uygulamanıza kimlik doğrulaması ve yetkilendirmesi için tek bir taşıyıcı belirtecini kabul eder.

Azure AD ile uyumluluğu sağlamak için bir SCıM uç noktası uygularken bu genel yönergeleri izleyin:

* `id` tüm kaynaklar için gerekli bir özelliktir. Kaynak döndüren her yanıt, sıfır üyeli `ListResponse` hariç olmak üzere her bir kaynağın bu özelliğe sahip olduğundan emin olmalıdır.
* Bir sorgu/filtre isteğine yanıt her zaman bir `ListResponse`olmalıdır.
* Gruplar isteğe bağlıdır, ancak yalnızca SCıM uygulamasının yama isteklerini desteklemesi durumunda desteklenir.
* Bu, tüm kaynağı yama yanıtına dahil etmek için gerekli değildir.
* Microsoft Azure AD yalnızca aşağıdaki işleçleri kullanır:  
    - `eq`
    - `and`
* Bazı düzeltme ekinde, https://tools.ietf.org/html/rfc7644#section-3.5.2tanımlanan şekilde işlem değerlerini `op`, özel bir düzeltme ekinde, SCıM içindeki yapısal öğelerde büyük/küçük harfe duyarlı bir eşleşme gerektirmez. Azure AD, ' op ' değerlerini `Add`, `Replace`ve `Remove`olarak yayar.
* Microsoft Azure AD bitiş noktasının ve kimlik bilgilerinin geçerli olduğundan emin olmak için rastgele bir Kullanıcı ve grup getirme isteklerinin olmasını sağlar. Ayrıca, [Azure Portal](https://portal.azure.com) **Test bağlantı** akışının bir parçası olarak da yapılır. 
* Kaynakların sorgulanabileceği öznitelik, [Azure Portal](https://portal.azure.com)uygulamada eşleşen bir öznitelik olarak ayarlanmalıdır. Daha fazla bilgi için bkz. [Kullanıcı hazırlama öznitelik eşlemelerini özelleştirme](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-deprovisioning"></a>Kullanıcı hazırlama ve sağlamayı kaldırma

Aşağıdaki çizim Azure Active Directory, uygulamanızın kimlik deposundaki bir kullanıcının yaşam döngüsünü yönetmek üzere bir SCıM hizmetine gönderdiği iletileri gösterir.  

![, Kullanıcı hazırlama ve sağlamayı kaldırma sırasını gösterir][4]<br/>
*Şekil 4: Kullanıcı hazırlama ve sağlamayı kaldırma sırası*

### <a name="group-provisioning-and-deprovisioning"></a>Grup sağlama ve sağlamayı kaldırma

Grup sağlama ve sağlamayı kaldırma isteğe bağlıdır. Uygulandığında ve etkinleştirildiğinde aşağıdaki çizimde, Azure AD 'nin uygulamanızın kimlik deposundaki bir grubun yaşam döngüsünü yönetmek için bir SCıM hizmetine gönderdiği iletiler gösterilmektedir.  Bu iletiler kullanıcılar hakkındaki iletilerden iki şekilde farklılık gösterir:

* Grupları alma istekleri, Üyeler özniteliğinin, isteğe yanıt olarak belirtilen herhangi bir kaynaktan dışlanacağını belirtir.  
* Bir başvuru özniteliği, belirli bir değere sahip olup olmadığını belirlemek üzere istekleri üyeleri özniteliği hakkında isteklerdir.  

![grup sağlama ve sağlamayı kaldırma sırasını gösterir][5]<br/>
*Şekil 5: Grup sağlama ve sağlamayı kaldırma sırası*

### <a name="scim-protocol-requests-and-responses"></a>SCıM protokol istekleri ve yanıtları
Bu bölümde, Azure AD SCıM istemcisi tarafından yayılan örnek SCıM istekleri ve örnek beklenen yanıtlar sağlanmaktadır. En iyi sonuçlar için, bu istekleri bu biçimde işleyecek ve beklenen yanıtları yaymak üzere uygulamanızı kodmalısınız.

> [!IMPORTANT]
> Azure AD Kullanıcı sağlama hizmeti 'nin aşağıda açıklanan işlemleri nasıl ve ne zaman anladığı hakkında bilgi edinmek için bkz. [Kullanıcı hazırlama sırasında ne olur](user-provisioning.md#what-happens-during-provisioning)?

[Kullanıcı Işlemleri](#user-operations)
  - [Kullanıcı oluştur](#create-user) ([istek](#request) / [yanıtı](#response))
  - [Kullanıcı al](#get-user) ([istek](#request-1) / [yanıtı](#response-1))
  - [Sorguya göre Kullanıcı al](#get-user-by-query) ([istek](#request-2) / [yanıtı](#response-2))
  - [Sorgu sıfıra sahip sonuçlara göre Kullanıcı al](#get-user-by-query---zero-results) ([istek](#request-3)
/ [yanıtı](#response-3))
  - [Kullanıcı [çok değerli özellikler] güncelleştirme](#update-user-multi-valued-properties) ([istek](#request-4) /  [yanıtı](#response-4))
  - [Kullanıcı güncelleştirme [tek değerli özellikler]](#update-user-single-valued-properties) ([istek](#request-5)
/ [yanıtı](#response-5)) 
  - [Kullanıcı silme](#delete-user) ([istek](#request-6) / 
[yanıtı](#response-6))

[Grup Işlemleri](#group-operations)
  - [Grup Oluştur](#create-group) ( [istek](#request-7) / [yanıtı](#response-7))
  - [Grup Al](#get-group) ( [istek](#request-8) / [yanıtı](#response-8))
  - [Group By DisplayName 'e göre Al](#get-group-by-displayname) ([istek](#request-9) / [yanıtı](#response-9))
  - [Güncelleştirme grubu [Üye olmayan öznitelikler]](#update-group-non-member-attributes) ([istek](#request-10) /
 [yanıtı](#response-10))
  - [Güncelleştirme grubu [Üye Ekleme]](#update-group-add-members) ( [istek](#request-11) /
[yanıtı](#response-11))
  - [Güncelleştirme grubu [üyeleri kaldır]](#update-group-remove-members) ( [istek](#request-12) /
[yanıtı](#response-12)) (
  - [Grubu Sil](#delete-group) ([istek](#request-13) /
[yanıtı](#response-13))

### <a name="user-operations"></a>Kullanıcı Işlemleri

* Kullanıcılar, `userName` veya `email[type eq "work"]` öznitelikleriyle sorgulanabilir.  

#### <a name="create-user"></a>Kullanıcı oluşturma

###### <a name="request"></a>İstek

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

#### <a name="get-user"></a>Kullanıcı Al

###### <a name="request-1"></a>İsteyen
*/Users/5d48a0a8e9f04aa38008 al* 

###### <a name="response-1"></a>Yanıt (Kullanıcı bulundu)
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

##### <a name="request-2"></a>İsteyen

*GET/Users? Filter = userName EQ "Test_User_dfeef4c5-5681 -4387-B016-bdf221e82081"*

##### <a name="response-2"></a>Yanıtıyla

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

##### <a name="request-3"></a>İsteyen

*/Users al? Filter = userName EQ "varolmayan kullanıcı"*

##### <a name="response-3"></a>Yanıtıyla

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

##### <a name="request-4"></a>İsteyen

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

##### <a name="response-4"></a>Yanıtıyla

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

##### <a name="request-5"></a>İsteyen

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

##### <a name="response-5"></a>Yanıtıyla

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

#### <a name="delete-user"></a>Kullanıcıyı Silme

##### <a name="request-6"></a>İsteyen

*/Users/5171a35d82074e068ce2 HTTP/1.1 SILME*

##### <a name="response-6"></a>Yanıtıyla

*HTTP/1.1 204 Içerik yok*

### <a name="group-operations"></a>Grup Işlemleri

* Gruplar her zaman bir boş üye listesiyle oluşturulur.
* Gruplar `displayName` özniteliğiyle sorgulanabilir.
* Grup düzeltme eki isteğine güncelleştirme, yanıtta bir *HTTP 204 içeriği* getirmelidir. Tüm üyelerin listesini içeren bir gövde döndürülmesi önerilmez.
* Grubun tüm üyelerini döndürmeyi desteklemek gerekli değildir.

#### <a name="create-group"></a>Grup Oluşturma

##### <a name="request-7"></a>İsteyen

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

##### <a name="response-7"></a>Yanıtıyla

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

#### <a name="get-group"></a>Grubunu Al

##### <a name="request-8"></a>İsteyen

*/Groups/40734ae655284ad3abcc? excludedAttributes = Members HTTP/1.1 alın*

##### <a name="response-8"></a>Yanıtıyla
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

##### <a name="request-9"></a>İsteyen
*/Groups al? excludedAttributes = Members & Filter = displayName EQ "displayName" HTTP/1.1*

##### <a name="response-9"></a>Yanıtıyla

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

##### <a name="request-10"></a>İsteyen

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

##### <a name="response-10"></a>Yanıtıyla

*HTTP/1.1 204 Içerik yok*

### <a name="update-group-add-members"></a>Güncelleştirme grubu [Üye Ekleme]

##### <a name="request-11"></a>İsteyen

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

##### <a name="response-11"></a>Yanıtıyla

*HTTP/1.1 204 Içerik yok*

#### <a name="update-group-remove-members"></a>Güncelleştirme grubu [üyeleri kaldır]

##### <a name="request-12"></a>İsteyen

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

##### <a name="response-12"></a>Yanıtıyla

*HTTP/1.1 204 Içerik yok*

#### <a name="delete-group"></a>Grubu Silme

##### <a name="request-13"></a>İsteyen

*/Groups/cdb1ce18f65944079d37 HTTP/1.1 SILME*

##### <a name="response-13"></a>Yanıtıyla

*HTTP/1.1 204 Içerik yok*

## <a name="step-3-build-a-scim-endpoint"></a>3\. Adım: SCıM uç noktası oluşturma

Azure Active Directory ile arabirimleri olan bir SCıM Web hizmeti oluşturarak, neredeyse tüm uygulama veya kimlik deposu için otomatik Kullanıcı sağlamayı etkinleştirebilirsiniz.

Şekli aşağıda verilmiştir:

1. Azure AD, aşağıda açıklanan kod örneklerine dahil olmak üzere Microsoft. Systemforcrossdomainıdentitymanagement adlı ortak bir dil altyapısı (CLı) kitaplığı sağlar. Sistem tümleştiricileri ve geliştiriciler, Azure AD 'yi herhangi bir uygulamanın kimlik deposuna bağlayabilen bir SCıM tabanlı Web hizmeti uç noktası oluşturup dağıtmak için bu kitaplığı kullanabilir.
2. Eşlemeleri, web hizmeti, standart kullanıcı şeması uygulamanın gerektirdiği protokolü ve kullanıcı şeması eşlemek için uygulanır. 
3. Uç nokta URL'si, Azure ad uygulama galerisinde özel bir uygulama bir parçası olarak kaydedilir.
4. Kullanıcıları ve grupları, Azure AD'de bu uygulama atanır. Atama sonrasında, hedef uygulamayla eşitlenecek bir kuyruğa yerleştirirler. Sıranın işleme eşitleme işlemi 40 dakikada bir çalışır.

### <a name="code-samples"></a>Kod örnekleri

Bu işlemi daha kolay hale getirmek için, bir SCıM Web hizmeti uç noktası oluşturan ve otomatik sağlamayı gösteren [kod örnekleri](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) verilmiştir. Örnek, kullanıcıları ve grupları temsil eden virgülle ayrılmış değerler satırlarıyla bir dosyayı tutan bir sağlayıcıdır.

**Önkoşullar**

* Visual Studio 2013 veya üzeri
* [.NET için Azure SDK](https://azure.microsoft.com/downloads/)
* SCIM uç noktası olarak kullanılacak ASP.NET framework 4. 5'i destekleyen Windows makinesi. Bu makinenin buluttan erişilebilir olması gerekir.
* [Bir Azure aboneliği deneme sürümü ya da lisanslı bir Azure AD Premium sürümü ile](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Başlarken

Azure ad sağlama isteklerini kabul edebilen bir SCIM uç noktası uygulamak için en kolay yolu, derleme ve dağıtma sağlanan kullanıcılar bir virgülle ayrılmış değer (CSV) dosyasına çıkarır kodu örneği sağlamaktır.

#### <a name="to-create-a-sample-scim-endpoint"></a>Örnek bir SCıM uç noktası oluşturmak için

1. Kod örnek paketini [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Paketin sıkıştırmasını açın ve Windows makinenizde C:\AzureAD-BYOA-Provisioning-Samples\ gibi bir konuma yerleştirin.
1. Bu klasörde, Visual Studio'da FileProvisioning\Host\FileProvisioningService.csproj projesi başlatın.
1. **Araçlar** > **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin ve çözüm başvurularını çözümlemek için fileprovisioningservice projesi için aşağıdaki komutları yürütün:

   ```powershell
    Update-Package -Reinstall
   ```

1. FileProvisioningService projeyi derleyin.
1. Windows komut istemi uygulamasını (Yönetici) olarak açmak ve kullanmak **cd** komut için dizini değiştirmek için **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**klasör.
1. `<ip-address>`, Windows makinenin IP adresi veya etki alanı adıyla değiştirerek aşağıdaki komutu çalıştırın:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. Windows **ayarları** > **Ağ & Internet ayarları**' nın altında, **Windows Güvenlik Duvarı** > **Gelişmiş ayarları**' nı seçin ve 9000 numaralı bağlantı noktasına gelen erişime izin veren bir **gelen kuralı** oluşturun.
1. Windows makinesi bir yönlendiricinin arkasındaysa, yönlendiricinin internet 'e açık olan 9000 bağlantı noktası ile Windows makinesinde bağlantı 9000 noktası arasında ağ erişim çevirisini çalıştıracak şekilde yapılandırılması gerekir. Bu yapılandırma, Azure AD 'nin buluttaki bu uç noktaya erişmesi için gereklidir.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Azure AD 'de örnek SCıM uç noktasını kaydetmek için

1. [Azure Active Directory portalında](https://aad.portal.azure.com)oturum açın. 
1. Sol bölmeden **Kurumsal uygulamalar** ' ı seçin. Galeriden eklenen uygulamalar dahil olmak üzere, yapılandırılan tüm uygulamaların bir listesi gösterilir.
1. **Galeri dışı** > **Tüm** >  **+ Yeni uygulama** ' yı seçin.
1. Uygulamanız için bir ad girin ve uygulama nesnesi oluşturmak için **Ekle** ' yi seçin. Oluşturulan uygulama nesnesi, için sağlama ve uygulama için çoklu oturum açmayı ve yalnızca SCIM uç noktası hedef uygulamayı temsil etmek üzere tasarlanmıştır.
1. Uygulama yönetimi ekranında, sol panelde **sağlama** ' yı seçin.
1. İçinde **sağlama modu** menüsünde **otomatik**.    
1. İçinde **Kiracı URL'si** uygulamanın SCIM uç nokta URL'sini girin. Örnek: https://api.contoso.com/scim/

1. SCIM uç noktanın bir OAuth taşıyıcı belirtecinden bir veren Azure AD dışındaki gerektiriyorsa, gerekli OAuth taşıyıcı belirteci sonra isteğe bağlı kopyalayın **gizli belirteç** alan. Bu alan boş bırakılırsa Azure AD, Azure AD 'den her istekle verilen bir OAuth taşıyıcı belirteci içerir. Kimlik sağlayıcısı olarak Azure AD kullanan uygulamalar, bu Azure AD veren belirtecini doğrulayabilir.
1. SCıM uç noktasına bağlanmak Azure Active Directory denemek için **Bağlantıyı Sına** ' yı seçin. Deneme başarısız olursa, hata bilgileri görüntülenir.  

    > [!NOTE]
    > **Test bağlantısı** , mevcut olmayan bir kullanıcı için SCIM uç noktasını sorgular, bu da Azure AD yapılandırmasında eşleşen özellik olarak rastgele bir GUID kullanılıyor. Beklenen doğru yanıt, boş bir SCıM ListResponse iletisi ile HTTP 200 Tamam

1. Uygulamaya bağlanma denemeleri başarılı olursa, yönetici kimlik bilgilerini kaydetmek için **Kaydet** ' i seçin.
1. İçinde **eşlemeleri** bölümünde, iki seçilebilir öznitelik eşlemelerini kümesi vardır: biri kullanıcı, nesneyi, diğeri için Grup nesneleri. Uygulamanızı Azure Active Directory'den eşitlenen öznitelikler gözden geçirmek için her birini seçin. Seçilen öznitelikler **eşleşen** özellikleri, kullanıcıları ve grupları güncelleştirme işlemleri için uygulamanızda eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** ' i seçin.
1. Altında **ayarları**, **kapsam** alanı grupları ve hangi kullanıcıların eşitleneceğini tanımlar. Yalnızca **Kullanıcılar ve gruplar** sekmesinde atanan kullanıcıları ve grupları eşitlemek için **"yalnızca atanan kullanıcıları ve grupları Eşitle** (önerilen) seçeneğini belirleyin.
1. Yapılandırmanız tamamlandıktan sonra **sağlama durumunu** **Açık**olarak ayarlayın.
1. Azure AD sağlama hizmetini başlatmak için **Kaydet** ' i seçin.
1. Yalnızca atanmış kullanıcıları ve grupları eşitlese (önerilir), **Kullanıcılar ve gruplar** sekmesini seçtiğinizden emin olun ve eşitlemek istediğiniz kullanıcıları veya grupları atayın.

Başlangıç çevrimi başladıktan sonra, uygulamanızdaki sağlama hizmeti tarafından gerçekleştirilen tüm işlemleri gösteren ilerlemeyi izlemek için sol paneldeki **Denetim günlükleri** ' ni seçebilirsiniz. Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](check-status-user-account-provisioning.md).

Örnek doğrulama son adım, Windows makinenizde \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug klasöründeki TargetFile.csv dosya açmaktır. Sağlama işlemi çalıştırıldıktan sonra bu dosya tüm ayrıntılarını atanan ve kullanıcıları ve grupları sağlanan gösterir.

### <a name="development-libraries"></a>Geliştirme kitaplıkları

SCIM Belirtimi'ne kendi web hizmeti geliştirmek için önce aşağıdaki kitaplıklar geliştirme süreci hızlandırmak için Microsoft tarafından sağlanan tanıyın:

* Ortak dil altyapısı (CLI) kitaplıkları, C# gibi bu altyapısının temel dilleri ile kullanım için sunulur. Bu kitaplıklardan biri olan Microsoft. Systemforcrossdomainıdentitymanagement. Service, aşağıdaki çizimde gösterilen Microsoft. Systemforcrossdomainıdentitymanagement. ıprovider arabirimini bildirir. Kitaplıkları kullanan bir geliştirici, bu arabirime, genel olarak sağlayıcı olarak başvurulabilen bir sınıfı uygular. Kitaplıklar, geliştiricinin SCıM belirtimine uyan bir Web hizmeti dağıtmasını sağlar. Web hizmeti Internet Information Services veya herhangi bir yürütülebilir CLı derlemesi içinde barındırılabilir. İstek, geliştirici tarafından bazı kimlik deposu üzerinde çalışılacak programlanmak sağlayıcının yöntemlere yapılan çağrılar veri dönüştürülür.
  
   ![Döküm: sağlayıcının yöntemlerine yapılan çağrılara çevrilen bir istek][3]
  
* [Express route işleyicileri](https://expressjs.com/guide/routing.html) bir node.js web hizmeti çağrıları (SCIM belirtimi tarafından tanımlanan) temsil eden node.js istek nesneleri ayrıştırmak için kullanılabilir yapılır.

### <a name="building-a-custom-scim-endpoint"></a>Özel bir SCıM uç noktası oluşturma

CLı kitaplıklarını kullanan geliştiriciler, hizmetlerinin herhangi bir yürütülebilir CLı derlemesinde veya Internet Information Services içinde barındırmasını sağlayabilir. İşte adresten yürütülebilir bir derleme içinde bir hizmet barındırma için örnek kod http://localhost:9000: 

```csharp
 private static void Main(string[] arguments)
 {
 // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
 // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
 // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

 Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
   new DevelopersMonitor();
 Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
   new DevelopersProvider(arguments[1]);
 Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
 try
 {
     webService = new WebService(monitor, provider);
     webService.Start("http://localhost:9000");

     Console.ReadKey(true);
 }
 finally
 {
     if (webService != null)
     {
         webService.Dispose();
         webService = null;
     }
 }
 }

 public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
 {
 private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
 private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

 public WebService(
   Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
   Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
 {
     this.monitor = monitoringBehavior;
     this.provider = providerBehavior;
 }

 public override IMonitor MonitoringBehavior
 {
     get
     {
         return this.monitor;
     }

     set
     {
         this.monitor = value;
     }
 }

 public override IProvider ProviderBehavior
 {
     get
     {
         return this.provider;
     }

     set
     {
         this.provider = value;
     }
 }
 }
```

Bu hizmet kök sertifika yetkilisi şu adlardan biri olduğu bir HTTP adresi ve sunucu kimlik doğrulama sertifikasında şunlar olmalıdır: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Bir sunucu kimlik doğrulama sertifikası için bir bağlantı noktası ağ Kabuğu yardımcı programını kullanarak Windows konağında bağlanabilir:

```
netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}
```

Rastgele bir genel benzersiz tanımlayıcı olsa da AppID bağımsız değişkeni için sağlanan değer, burada certhash bağımsız değişkeni için sağlanan sertifikanın parmak izini değerdir.  

Hizmeti Internet Information Services içinde barındırmak için bir geliştirici, derlemenin varsayılan ad alanında başlangıç adlı bir sınıf ile CLı kod kitaplığı derlemesi oluşturur.  Böyle bir sınıfın bir örneği aşağıdadır: 

```csharp
 public class Startup
 {
 // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
 // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
 // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

 Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

 public Startup()
 {
     Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
       new DevelopersMonitor();
     Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
       new DevelopersProvider();
     this.starter = 
       new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
         provider, 
         monitor);
 }

 public void Configuration(
   Owin.IAppBuilder builder) // Defined in Owin.dll.  
 {
     this.starter.ConfigureApplication(builder);
 }
 }
```

### <a name="handling-endpoint-authentication"></a>İşleme uç nokta kimlik doğrulaması

Azure Active Directory gelen istekleri bir OAuth 2.0 taşıyıcı belirteci içerir.   İsteği alan herhangi bir hizmet, Azure Active Directory Graph Web hizmetine erişmek için beklenen Azure Active Directory kiracının Azure Active Directory olarak kimlik doğrulaması yapması gerekir.  Belirteçte veren, "iss" gibi bir ISS talebi tarafından tanımlanır: "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  Bu örnekte, https://sts.windows.nettalep değerinin temel adresi veren olarak Azure Active Directory tanımlar, göreli adres segmenti, cbb1a5ac-f33b-45fa-9BF5-f37db0fed422, belirtecin verildiği Azure Active Directory kiracının benzersiz tanımlayıcısıdır. Belirtecin hedef kitlesi, galerideki uygulamanın uygulama şablonu KIMLIĞI olacaktır. Tüm özel uygulamalar için uygulama şablonu KIMLIĞI, 8adf8e6e-67b2-4cf2-a259-e3dc5476c621 ' dir. Galerideki her bir uygulama için uygulama şablonu KIMLIĞI değişir. Galeri uygulamasının uygulama şablonu KIMLIĞI hakkında sorularınız için lütfen ProvisioningFeedback@microsoft.com başvurun. Tek bir kiracıya kayıtlı uygulamaların her biri, SCıM istekleriyle aynı `iss` talebini alabilir.

Bir SCıM hizmeti oluşturmak için Microsoft tarafından sunulan CLı kitaplıklarını kullanan geliştiriciler, aşağıdaki adımları izleyerek Microsoft. Owin. Security. ActiveDirectory paketini kullanarak Azure Active Directory istekleri kimlik doğrulaması yapabilir: 

İlk olarak, bir sağlayıcıda, hizmet her başlatıldığında Çağrılacak bir yöntemi döndürerek Microsoft. Systemforcrossdomainıdentitymanagement. ıprovider. StartupBehavior özelliğini uygulayın: 

```csharp
  public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
  {
    get
    {
      return this.OnServiceStartup;
    }
  }

  private void OnServiceStartup(
    Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
    System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
  {
  }
```

Daha sonra, Azure AD Graph Web hizmetine erişim için, belirtilen bir kiracının Azure Active Directory tarafından verilen bir belirteci oluşturan herhangi bir hizmet uç noktası için bu yönteme herhangi bir istek eklemek üzere aşağıdaki kodu ekleyin: 

```csharp
  private void OnServiceStartup(
    Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
    System.Web.Http.HttpConfiguration HttpConfiguration configuration)
  {
    // IFilter is defined in System.Web.Http.dll.  
    System.Web.Http.Filters.IFilter authorizationFilter = 
      new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

    // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
    // System.IdentityModel.Token.Jwt.dll.
    SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
      new TokenValidationParameters()
      {
        ValidAudience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621"
      };

    // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
    // Microsoft.Owin.Security.ActiveDirectory.dll
    Microsoft.Owin.Security.ActiveDirectory.
    WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
      new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
      TokenValidationParameters = tokenValidationParameters,
      Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                    // identifier for this one.  
    };

    applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
  }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Kullanıcıları hazırlama ve sağlamayı kaldırma işlemlerini işleme

***Örnek 1. Hizmeti eşleşen bir kullanıcı için sorgulama***

Azure Active Directory, Azure AD'de kullanıcı mailNickname özniteliğinin değeri ile eşleşen bir externalID öznitelik değeri olan bir kullanıcı için hizmet sorgular. Sorgu, bu örnek gibi bir Köprü Metni Aktarım Protokolü (HTTP) isteği olarak ifade edilir. burada jbaşak, Azure Active Directory bir kullanıcının Mailtakma adı örneğidir.

>[!NOTE]
> Bu yalnızca bir örnektir. Tüm kullanıcıların bir Mailrumuz özniteliği olmaz ve bir Kullanıcı, dizinde benzersiz olmayabilir. Ayrıca, eşleştirme için kullanılan öznitelik (Bu örnekte externalId), [Azure AD öznitelik eşlemelerinde](customize-application-attributes.md)yapılandırılabilir.

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

Hizmet, SCıM hizmetlerini uygulamak için Microsoft tarafından sunulan CLı kitaplıkları kullanılarak oluşturulmuşsa, istek hizmet sağlayıcının sorgu metoduna yapılan çağrıya çevrilir.  Aşağıda, bu yöntem imzası verilmiştir: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
 // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
   Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
   string correlationIdentifier);
```

Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters arabirim tanımı aşağıda verilmiştir: 

```csharp
 public interface IQueryParameters: 
   Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
 {
     System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
     { get; }
 }

 public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
 {
   system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
   { get; }
   System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
   { get; }
   string SchemaIdentifier 
   { get; }
 }
```

```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
```

Hizmet SCIM Hizmetleri uygulamak için Microsoft tarafından sağlanan ortak dil altyapısı kitaplıklar kullanılarak oluşturulduysa, isteği hizmet sağlayıcısının sorgu yöntemine bir çağrı çevrilir.  Aşağıda, bu yöntem imzası verilmiştir: 

```csharp
  // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
  // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
  // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

  System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
    Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
    string correlationIdentifier);
```

Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters arabirim tanımı aşağıda verilmiştir: 

```csharp
  public interface IQueryParameters: 
    Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
  {
      System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
      { get; }
  }

  public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
  {
    system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
    { get; }
    System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
    { get; }
    string SchemaIdentifier 
    { get; }
  }

  public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
  {
      Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
        { get; set; }
      string AttributePath 
        { get; } 
      Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
        { get; }
      string ComparisonValue 
        { get; }
  }

  public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
  {
      Equals
  }
```

Aşağıdaki örnekte bir kullanıcı için bir sorgunun externalID özniteliği için belirtilen bir değerle sorgu yöntemine geçirilen bağımsız değişkenlerin değerleri şunlardır: 
* Parametreler. AlternateFilters.Count: 1
* Parametreler. AlternateFilters.ElementAt(0). AttributePath: "externalID ="
* Parametreler. AlternateFilters.ElementAt(0). ComparisonOperator: ComparisonOperator.Equals
* Parametreler. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin. RequestId"] 

***Örnek 2. Kullanıcı sağlama***

Bir kullanıcının Mailtakma özniteliği değeri ile eşleşen bir externalId özniteliği değeri olan bir kullanıcı için Web hizmetine yapılan bir sorgunun yanıtı, hiçbir Kullanıcı döndürmüyor, sonra hizmetin bir kullanıcıya karşılık gelen bir Kullanıcı sağlaması Azure Active Directory istekleri Azure Active Directory.  Böyle bir isteğin bir örnek aşağıda verilmiştir: 

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
   "userName":"jyoung",
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

SCıM hizmetlerini uygulamak için Microsoft tarafından sunulan CLı kitaplıkları, bu isteği hizmet sağlayıcının oluşturma yöntemine yönelik bir çağrıya çeviyordu.  Oluştur yönteminin bu imzaya sahip:

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
   Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
   string correlationIdentifier);
```

Bir kullanıcı sağlamak için istekte kaynak bağımsız değişkenin değeri Microsoft.SystemForCrossDomainIdentityManagement örneğidir. Microsoft.SystemForCrossDomainIdentityManagement.Schemas Kitaplığı'nda tanımlanan Core2EnterpriseUser sınıfı.  Kullanıcı sağlama isteği başarılı olursa, yöntemin uygulanmasını Microsoft.SystemForCrossDomainIdentityManagement örneğini döndürmesi beklenir. Yeni sağlanan kullanıcının benzersiz tanımlayıcısı için ayarlanmış tanımlayıcı özelliğinin değeri ile Core2EnterpriseUser sınıfı.  

***Örnek 3. Kullanıcının geçerli durumunu sorgulama*** 

Bilinen bir kimlik deposu bir SCIM fronted mevcut kullanıcıyı güncelleştirmek için Azure Active Directory gibi bir isteği ile hizmetten kullanıcının geçerli durumunu isteyerek çalışır: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

SCıM hizmetlerini uygulamak için Microsoft tarafından sunulan CLı kitaplıkları kullanılarak oluşturulan bir hizmette, istek hizmet sağlayıcının alma yöntemine çevrilir.  Al yönteminin imzası şu şekildedir:

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
 // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
 // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
    Retrieve(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
        parameters, 
        string correlationIdentifier);

 public interface 
   Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
     IRetrievalParameters
     {
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
         ResourceIdentifier 
           { get; }
 }
 public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
 {
     string Identifier 
       { get; set; }
     string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
       { get; set; }
 }
```

Bir kullanıcının geçerli durumunu almak için bir istek örnekte parametreleri bağımsız değişkenin değeri sağlanan nesne özelliklerinin değerleri aşağıdaki gibidir: 
  
* Tanımlayıcısı: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Örnek 4. Bir başvuru özniteliğinin güncelleştirileceği değeri sorgula*** 

Bir başvuru özniteliği güncelleştirilürse Azure Active Directory, hizmet tarafından kullanılan kimlik deposundaki başvuru özniteliğinin geçerli değerinin Azure etkin 'deki bu özniteliğin değeriyle zaten eşleşip eşleşmediğini belirleme hizmetini sorgular. Dizinden. Kullanıcılar için bu şekilde sorgulanan geçerli değeri yalnızca öznitelik manager özniteliğidir. Belirli kullanıcı nesnesinin manager özniteliği şu anda belirli bir değere sahip olup olmadığını belirlemek için bir isteğin bir örnek aşağıda verilmiştir: 

Hizmet, SCıM hizmetlerini uygulamak için Microsoft tarafından sunulan CLı kitaplıkları kullanılarak oluşturulmuşsa, istek hizmet sağlayıcının sorgu metoduna yapılan çağrıya çevrilir. Parametre bağımsız değişkenin değeri sağlanan nesne özelliklerini değerini aşağıdaki gibidir: 
  
* Parametreler. AlternateFilters.Count: 2
* Parametreler. AlternateFilters.ElementAt(x). AttributePath: "ID"
* Parametreler. AlternateFilters.ElementAt(x). ComparisonOperator: ComparisonOperator.Equals
* parametrelere. AlternateFilter. ElementAt (x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* Parametreler. AlternateFilters.ElementAt(y). AttributePath: "Yönetici"
* Parametreler. AlternateFilters.ElementAt(y). ComparisonOperator: ComparisonOperator.Equals
* parametrelere. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453A-919d-413861904646"
* Parametreler. RequestedAttributePaths.ElementAt(0): "ID"
* Parametreler. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

Burada, x dizininin değeri 0 olabilir ve y dizininin değeri 1 olabilir, ya da x değeri 1 olabilir ve Filter sorgu parametresinin ifadelerinin sırasına bağlı olarak y değeri 0 olabilir.   

***Örnek 5. Bir kullanıcıyı güncelleştirmek için Azure AD 'den bir SCıM hizmetine istek*** 

İsteğinin bir örneği bir Azure Active Directory'den kullanıcıyı güncelleştirmek için SCIM'yi hizmetine şöyledir: 

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

SCIM Hizmetleri uygulamak için Microsoft ortak dil altyapısı kitaplıkları hizmet sağlayıcısının güncelleştirme yöntemine bir çağrı isteği çevirir. Güncelleştirme metodun imzası şu şekildedir: 

```csharp
  // System.Threading.Tasks.Tasks and 
  // System.Collections.Generic.IReadOnlyCollection<T>
  // are defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
  // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
  // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
  // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
  // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
  // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
  // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
  // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

  System.Threading.Tasks.Task Update(
    Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
    string correlationIdentifier);

  public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
  {
  Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
    PatchRequest 
      { get; set; }
  Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
    ResourceIdentifier 
      { get; set; }        
  }

  public class PatchRequest2: 
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
  {
  public System.Collections.Generic.IReadOnlyCollection
    <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
      Operations
      { get;}

  public void AddOperation(
    Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
  }

  public class PatchOperation
  {
  public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
    Name
    { get; set; }

  public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
    Path
    { get; set; }

  public System.Collections.Generic.IReadOnlyCollection
    <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
    { get; }

  public void AddValue(
    Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
  }

  public enum OperationName
  {
    Add,
    Remove,
    Replace
  }

  public interface IPath
  {
    string AttributePath { get; }
    System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
    Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
  }

  public class OperationValue
  {
    public string Reference
    { get; set; }

    public string Value
    { get; set; }
  }
```

Kullanıcıyı güncelleştirmek için bir istek örnekte düzeltme eki bağımsız değişkenin değeri sağlanan nesne bu özelliği değer vardır: 
  
* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest PatchRequest2 olarak). Operations.Count: 1
* (PatchRequest PatchRequest2 olarak). Operations.ElementAt(0). OperationName: OperationName.Add
* (PatchRequest PatchRequest2 olarak). Operations.ElementAt(0). Path.AttributePath: "Yönetici"
* (PatchRequest PatchRequest2 olarak). Operations.ElementAt(0). Value.Count: 1
* (PatchRequest PatchRequest2 olarak). Operations.ElementAt(0). Value.ElementAt(0). Başvuru: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest PatchRequest2 olarak). Operations.ElementAt(0). Value.ElementAt(0). Değer: 2819c223-7f76-453a-919d-413861904646

***Örnek 6. Bir kullanıcının sağlamasını kaldırma***

Bir kullanıcının SCıM hizmeti tarafından belirtilen bir kimlik deposundan sağlamasını kaldırmak için, Azure AD şöyle bir istek gönderir:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

Hizmet SCIM Hizmetleri uygulamak için Microsoft tarafından sağlanan ortak dil altyapısı kitaplıklar kullanılarak oluşturulduysa, isteği hizmet sağlayıcısının silme yöntemine bir çağrı çevrilir.   Bu yöntem, bu imzaya sahip: 

```csharp
  // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
  // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
  System.Threading.Tasks.Task Delete(
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
      resourceIdentifier, 
    string correlationIdentifier);
```

ResourceIdentifier bağımsız değişkeninin değeri olarak sunulan nesne, bir kullanıcının sağlamasını kaldırma isteği örneğinde bu özellik değerlerine sahiptir: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>4\. Adım: SCıM uç noktanızı Azure AD SCıM istemcisiyle tümleştirme

Azure AD, [SCIM 2,0 protokolünün](https://tools.ietf.org/html/rfc7644)belirli bir profilini uygulayan uygulamalara atanan kullanıcıları ve grupları otomatik olarak sağlayacak şekilde yapılandırılabilir. Profilin özellikleri [2. Adım: Azure AD SCıM uygulamasını anlama](#step-2-understand-the-azure-ad-scim-implementation)bölümünde belgelenmiştir.

Uygulama sağlayıcınıza veya bilgilerinin bu gereksinimleri ile uyumluluk için uygulama sağlayıcının belgelerine başvurun.

> [!IMPORTANT]
> Azure AD SCıM uygulaması, Azure AD Kullanıcı sağlama hizmeti 'nin üzerine kurulmuştur. Bu, kullanıcıların Azure AD ile hedef uygulama arasında sürekli olarak eşitlenmesi için tasarlanan ve çok özel bir standart işlem kümesi uyguladığı şekilde tasarlanmıştır. Azure AD SCıM istemcisinin davranışını anlamak için bu davranışları anlamak önemlidir. Daha fazla bilgi için bkz. [Kullanıcı hazırlama sırasında ne olur?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Başlarken

Bu makalede açıklanan SCIM profilini destekleyen uygulamalar, Azure Active Directory Azure AD uygulama galerisinde bulunan "galeri dışı uygulama" özelliğini kullanarak bağlanabilir. Bağlantı kurulduktan sonra Azure AD eşitleme işlemi burada, uygulamanın SCIM uç noktası için atanan kullanıcılar ve gruplar, sorgular ve oluşturuyor veya bunları göre atama ayrıntıları 40 dakikada bir çalışır.

**SCIM'yi destekleyen bir uygulamaya bağlanmak için:**

1. [Azure Active Directory portalında](https://aad.portal.azure.com)oturum açın. [Geliştirici programına](https://developer.microsoft.com/office/dev-program) kaydolduktan sonra, P2 lisanslarıyla Azure Active Directory ücretsiz bir denemeye erişebileceğinizi unutmayın.
2. Sol bölmeden **Kurumsal uygulamalar** ' ı seçin. Galeriden eklenen uygulamalar dahil olmak üzere, yapılandırılan tüm uygulamaların bir listesi gösterilir.
3. **Galeri dışı** > **Tüm** >  **+ Yeni uygulama** ' yı seçin.
4. Uygulamanız için bir ad girin ve uygulama nesnesi oluşturmak için **Ekle** ' yi seçin. Yeni uygulama, kurumsal uygulamalar listesine eklenir ve uygulama yönetimi ekranına açılır.

   ![ekran görüntüsünde Azure AD Uygulama Galerisi gösterilmektedir][1]<br/>
   *Şekil 2: Azure AD Uygulama Galerisi*

5. Uygulama yönetimi ekranında, sol panelde **sağlama** ' yı seçin.
6. İçinde **sağlama modu** menüsünde **otomatik**.

   ![örnek: bir uygulamanın sağlama sayfası Azure portal][2]<br/>
   *Şekil 3: Azure portal sağlamayı yapılandırma*

7. İçinde **Kiracı URL'si** uygulamanın SCIM uç nokta URL'sini girin. Örnek: https://api.contoso.com/scim/
8. SCIM uç noktanın bir OAuth taşıyıcı belirtecinden bir veren Azure AD dışındaki gerektiriyorsa, gerekli OAuth taşıyıcı belirteci sonra isteğe bağlı kopyalayın **gizli belirteç** alan. Bu alan boş bırakılırsa Azure AD, Azure AD 'den her istekle verilen bir OAuth taşıyıcı belirteci içerir. Kimlik sağlayıcısı olarak Azure AD kullanan uygulamalar, bu Azure AD veren belirtecini doğrulayabilir. 
   > [!NOTE]
   > Bu alanı boş bırakmanız ve Azure AD tarafından oluşturulan bir belirtece güvenmamanız ***önerilmez.*** Bu seçenek öncelikle test amacıyla kullanılabilir.
9. SCıM uç noktasına bağlanmak Azure Active Directory denemek için **Bağlantıyı Sına** ' yı seçin. Deneme başarısız olursa, hata bilgileri görüntülenir.  

    > [!NOTE]
    > **Test bağlantısı** , mevcut olmayan bir kullanıcı için SCIM uç noktasını sorgular, bu da Azure AD yapılandırmasında eşleşen özellik olarak rastgele bir GUID kullanılıyor. Beklenen doğru yanıt, boş bir SCIM ListResponse iletisi ile HTTP 200 Tamam ' dır.

10. Uygulamaya bağlanma denemeleri başarılı olursa, yönetici kimlik bilgilerini kaydetmek için **Kaydet** ' i seçin.
11. **Eşlemeler** bölümünde, biri Kullanıcı nesneleri ve bir grup nesnesi için olmak üzere iki seçilebilir [öznitelik eşlemesi](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)vardır. Uygulamanızı Azure Active Directory'den eşitlenen öznitelikler gözden geçirmek için her birini seçin. Seçilen öznitelikler **eşleşen** özellikleri, kullanıcıları ve grupları güncelleştirme işlemleri için uygulamanızda eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** ' i seçin.

    > [!NOTE]
    > İsteğe bağlı olarak, "eşleme grupları" devre dışı bırakarak Grup nesnelerini eşitlemeyi devre dışı bırakın.

12. **Ayarlar**' ın altında, **kapsam** alanı hangi kullanıcıların ve grupların eşitleneceğini tanımlar. Yalnızca **Kullanıcılar ve gruplar** sekmesinde atanan kullanıcıları ve grupları eşitlemek için **yalnızca atanmış kullanıcıları ve grupları Eşitle** (önerilir) öğesini seçin.
13. Yapılandırmanız tamamlandıktan sonra **sağlama durumunu** **Açık**olarak ayarlayın.
14. Azure AD sağlama hizmetini başlatmak için **Kaydet** ' i seçin.
15. Yalnızca atanmış kullanıcıları ve grupları eşitlese (önerilir), **Kullanıcılar ve gruplar** sekmesini seçtiğinizden emin olun ve eşitlemek istediğiniz kullanıcıları veya grupları atayın.

Başlangıç çevrimi başladıktan sonra, uygulamanızdaki sağlama hizmeti tarafından gerçekleştirilen tüm işlemleri gösteren ilerlemeyi izlemek için sol panelde **sağlama günlükleri** ' ni seçebilirsiniz. Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](check-status-user-account-provisioning.md).

> [!NOTE]
> İlk döngüyü daha sonra, hizmetin çalıştığı sürece yaklaşık 40 dakikada bir gerçekleşen daha sonraki eşitliden daha uzun sürer.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>5\. Adım: uygulamanızı Azure AD Uygulama Galerisi 'nde yayımlama

Birden fazla kiracı tarafından kullanılacak bir uygulama oluşturuyorsanız, Azure AD uygulama galerisinde kullanılabilir hale getirebilirsiniz. Bu, kuruluşların uygulamayı keşfetmesini ve sağlamayı yapılandırmasını kolaylaştırır. Uygulamanızı Azure AD galerisinde yayımlama ve sağlama sağlamak kolaydır. [Buradaki](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)adımlara göz atın. Microsoft, uygulamanızı galerimize tümleştirme, uç noktanızı test etme ve müşterilerin kullanması için ekleme [belgelerini](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) yayınlama konusunda sizinle birlikte çalışacaktır. 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Azure AD sağlama hizmeti tarafından SCIM isteklerini yapmak için kullanılan IP adreslerine izin ver

Belirli uygulamalar, uygulamalarına gelen trafiğe izin verir. Azure AD sağlama hizmeti 'nin beklenen şekilde çalışması için, kullanılan IP adreslerine izin verilmelidir. Her bir hizmet etiketi/bölgesinin IP adresleri listesi için bkz. JSON dosyası- [Azure IP aralıkları ve hizmet etiketleri – genel bulut](https://www.microsoft.com/download/details.aspx?id=56519). Gerektiğinde bu IP 'Leri güvenlik duvarınızdan indirebilir ve programlayabilirsiniz. Azure AD sağlama için ayrılmış IP aralıkları "AzureActiveDirectoryDomainServices" altında bulunabilir.

## <a name="related-articles"></a>İlgili makaleler

* [Kullanıcı sağlama/sağlamayı kaldırma SaaS uygulamaları için otomatik hale getirin](user-provisioning.md)
* [Kullanıcı sağlama için öznitelik eşlemelerini özelleştirme](customize-application-attributes.md)
* [Öznitelik eşlemeleri için ifadeler yazma](functions-for-customizing-application-data.md)
* [Kullanıcı sağlama için kapsam oluşturma filtresi](define-conditional-rules-for-provisioning-user-accounts.md)
* [Hesap sağlama bildirimleri](user-provisioning.md)
* [SaaS uygulamalarını tümleştirme hakkında öğreticiler listesi](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
