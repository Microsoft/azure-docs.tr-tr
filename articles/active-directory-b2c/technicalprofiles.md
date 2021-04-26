---
title: Teknik profiller
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C bir özel ilkenin teknisyen öğesini belirtin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bcff1ffd574db910c3206d82e4da0e9428db788f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631804"
---
# <a name="technical-profiles"></a>Teknik profiller

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

*Teknik bir profil* , farklı türlerde taraflar ile iletişim kurmak için yerleşik mekanizmaya sahip bir çerçeve sağlar. Teknik profiller, bir kullanıcı oluşturmak veya bir kullanıcı profilini okumak üzere Azure Active Directory B2C (Azure AD B2C) kiracınızla iletişim kurmak için kullanılır. Kullanıcı etkileşimi sağlamak için teknik bir profil kendi kendine eklenebilir. Örneğin, teknik bir profil kullanıcının kimlik bilgilerini toplayıp oturum açıp kaydolma sayfasını veya parola sıfırlama sayfasını işleyebilir.

## <a name="types-of-technical-profiles"></a>Teknik profillerin türleri

Teknik bir profil, bu tür senaryolara izin vermez:

- [Application Insights](analytics-with-application-insights.md): olay verilerini [Application Insights](../azure-monitor/app/app-insights-overview.md)gönderir.
- [Azure AD](active-directory-technical-profile.md): Azure AD B2C Kullanıcı yönetimi için destek sağlar.
- [Azure AD çok faktörlü kimlik doğrulaması](multi-factor-auth-technical-profile.md): Azure AD çok faktörlü kimlik doğrulaması kullanarak bir telefon numarasını doğrulamaya yönelik destek sağlar.
- [Talep dönüştürme](claims-transformation-technical-profile.md): talep değerlerini işlemek, talepleri doğrulamak veya bir çıkış talepleri kümesi için varsayılan değerleri ayarlamak için çıkış talep dönüştürmelerini çağırır.
- [Kimlik belirteci ipucu](id-token-hint.md): `id_token_hint` JWT belirteç imzasını, verenin adını ve belirteç kitleyi doğrular ve gelen belirteçten talebi ayıklar.
- [JWT belirteci veren](jwt-issuer-technical-profile.md): bağlı olan taraf uygulamasına GERI döndürülen JWT belirtecini yayar.
- [OAuth1](oauth1-technical-profile.md): herhangi bir OAuth 1,0 protokol kimliği sağlayıcısıyla Federasyon.
- [OAuth2](oauth2-technical-profile.md): herhangi bir OAuth 2,0 protokol kimliği sağlayıcısıyla Federasyon.
- [Bir kerelik parola](one-time-password-technical-profile.md): bir kerelik parolanın oluşturulmasını ve doğrulanmasını yönetmek için destek sağlar.
- [OpenID Connect](openid-connect-technical-profile.md): any OpenID Connect protokol kimliği sağlayıcısıyla Federasyon.
- [Telefon faktörü](phone-factor-technical-profile.md): telefon numaralarının kaydedilmesini ve doğrulanması destekler.
- [Yeniden işleme sağlayıcı](restful-technical-profile.md): Kullanıcı girişini doğrulama, Kullanıcı verilerini zenginleştirme veya iş kolu uygulamalarıyla tümleştirme gibi REST API hizmetleri çağırır.
- [SAML kimlik sağlayıcısı](identity-provider-generic-saml.md): HERHANGI bir saml protokol kimliği sağlayıcısıyla Federasyon.
- [SAML belirteci veren](saml-service-provider.md): bağlı olan taraf uygulamasına geri döndürülen bir SAML belirteci yayar.
- [Kendiliğinden onaylanan](self-asserted-technical-profile.md): kullanıcıyla etkileşime girer. Örneğin, oturum açmak için kullanıcının kimlik bilgilerini toplar, kaydolma sayfasını işleyebilir veya parolayı sıfırlayabilirsiniz.
- [Oturum yönetimi](custom-policy-reference-sso.md): farklı oturum türlerini işler.

## <a name="technical-profile-flow"></a>Teknik profil akışı

Tüm teknik profil türleri aynı kavramı paylaşır. Bunlar, giriş taleplerini okuyarak ve talep dönüştürmeleri çalıştırarak başlar. Ardından, kimlik sağlayıcısı, REST API veya Azure AD Dizin Hizmetleri gibi yapılandırılmış tarafla iletişim kurar. İşlem tamamlandıktan sonra teknik profil, çıkış taleplerini döndürür ve çıkış talep dönüştürmelerini çalıştırabilir. Aşağıdaki diyagramda, teknik profilde başvurulan dönüşümlerin ve eşlemelerin nasıl işlendiği gösterilmektedir. Talep dönüştürme yürütüldükten sonra, teknik profilin etkileşimde bulunduğu tarafa bakılmaksızın çıkış talepleri talep çantasında hemen depolanır.

![Teknik profil akışını gösteren diyagram.](./media/technical-profiles/technical-profile-flow.png)

1. **Çoklu oturum açma (SSO) oturum yönetimi**: [SSO oturum yönetimi](custom-policy-reference-sso.md)kullanarak teknik profilin oturum durumunu geri yükler.
1. **Giriş talep dönüşümü**: teknik profil başlatılmadan önce, Azure AD B2C giriş [talep dönüşümünü](claimstransformations.md)çalıştırır.
1. **Giriş talepleri**: talepler, teknik profil için kullanılan talep çantasından alınır.
1. **Teknik profil yürütme**: teknik profil, talepleri, yapılandırılan tarafla birlikte değiş tokuş eder. Örnek:
    - Oturum açma işleminin tamamlanabilmesi için kullanıcıyı kimlik sağlayıcısına yönlendirir. Başarılı oturum açma işleminden sonra Kullanıcı geri döner ve teknik profil yürütme devam eder.
    - Parametreleri ınputclaim olarak gönderirken ve Outputclaim olarak geri bilgi alırken bir REST API çağırır.
    - Kullanıcı hesabını oluşturur veya güncelleştirir.
    - Çok faktörlü kimlik doğrulama SMS iletisini gönderir ve doğrular.
1. **Doğrulama teknik profilleri**: [kendiliğinden onaylanan bir teknik profil](self-asserted-technical-profile.md) , Kullanıcı tarafından profili oluşturulan verileri doğrulamak için [doğrulama teknik profillerini](validation-technical-profile.md) çağırabilir.
1. **Çıkış talepleri**: talepler, talep çantasına geri döndürülür. Bu talepleri, sonraki düzenlemeler adımında veya çıkış talebi dönüşümlerindeki kullanılabilir.
1. **Çıkış talepleri dönüşümleri**: teknik profil tamamlandıktan sonra, Azure AD B2C çıktı [talep dönüştürmelerini](claimstransformations.md)çalıştırır.
1. **SSO oturum yönetimi**: [SSO oturum yönetimi](custom-policy-reference-sso.md)kullanarak teknik profilin verilerini oturuma devam ettirir.

Bir **teknisyen** öğesi, talep sağlayıcısı tarafından desteklenen bir teknik profiller kümesi içerir. Her talep sağlayıcısının en az bir teknik profili olmalıdır. Teknik profil, talep sağlayıcısıyla iletişim kurmak için gereken uç noktaları ve protokolleri belirler. Bir talep sağlayıcısının birden çok teknik profili olabilir.

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        ...
      </Metadata>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**Teknisyen** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
|---------|---------|---------|
| Id | Yes | Teknik profilin benzersiz tanıtıcısı. Bu tanımlayıcı, ilke dosyasındaki diğer öğelerden bu tanımlayıcıyı kullanarak, teknik profile başvurulabilir. Örnekler, **Orchestrationsteps** ve **Validationteknisyen alprofile**' dir. |

**Teknisyen** öğesi, aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| Etki alanı | 0:1 | Teknik profilin etki alanı adı. Örneğin, teknik profiliniz Facebook kimlik sağlayıcısını belirtiyorsa, etki alanı adı Facebook.com olur. |
| DisplayName | 1:1 | Teknik profilin görünen adı. |
| Description | 0:1 | Teknik profilin açıklaması. |
| Protokol | 1:1 | Diğer tarafla iletişim için kullanılan protokol. |
| Meta veri | 0:1 | Teknik profilin davranışını denetleyen bir anahtarlar ve değerler kümesi. |
| Inputtokenformat | 0:1 | Giriş belirtecinin biçimi. Olası değerler şunlardır,, `JSON` `JWT` `SAML11` veya `SAML2` . `JWT`Değer, IETF belirtimine göre bir JSON Web Token temsil eder. `SAML11`Değer, OASSıS belirtimine göre BIR SAML 1,1 güvenlik belirtecini temsil eder. `SAML2`Değer, OASSıS belirtimine göre BIR SAML 2,0 güvenlik belirtecini temsil eder. |
| OutputTokenFormat | 0:1 | Çıkış belirtecinin biçimi. Olası değerler şunlardır,, `JSON` `JWT` `SAML11` veya `SAML2` . |
| CryptographicKeys | 0:1 | Teknik profilde kullanılan şifreleme anahtarlarının bir listesi. |
| Inputclaimstransformations | 0:1 | Talep dönüşümlerine veya bağlı olan tarafa herhangi bir talep gönderilmeden önce yürütülmesi gereken talep dönüştürmelerinin önceden tanımlanmış başvuruları listesi. |
| Inputclaims | 0:1 | Teknik profilde girdi olarak alınan talep türlerine önceden tanımlanmış başvuruların listesi. |
| PersistedClaims | 0:1 | Teknik profille kalıcı olacak talep türlerine daha önceden tanımlanmış başvuruların listesi. |
| DisplayClaim | 0:1 | [Kendi kendini onaylanan teknik profille](self-asserted-technical-profile.md)sunulan talep türlerine önceden tanımlanmış başvuruların listesi. DisplayClaim özelliği şu anda önizlemededir. |
| Outputclaim | 0:1 | Teknik profilde çıkış olarak alınan talep türlerine daha önceden tanımlanmış başvuruların listesi. |
| OutputClaimsTransformations | 0:1 | Talep dönüşümlerinden talepler alındıktan sonra yürütülmesi gereken talep dönüştürmelerine yönelik önceden tanımlanmış başvuruların listesi. |
| Validationcealprofiles | 0: n | Teknik profilin doğrulama amacıyla kullandığı diğer teknik profillerin başvurularını içeren bir liste. Daha fazla bilgi için bkz. [doğrulama teknik profili](validation-technical-profile.md).|
| Subjectnamingınfo | 0:1 | Konu adının taleplerden ayrı olarak belirtildiği belirteçlerde konu adının üretimini denetler. Örnekler OAuth veya SAML. |
| Includeınsso | 0:1 | Bu teknik profilin kullanımının oturum için SSO davranışını uygulayıp uygulamamalıdır, bunun yerine açık etkileşim gerektir. Bu öğe yalnızca bir doğrulama teknik profili içinde kullanılan Selfasthted profillerinde geçerlidir. Olası değerler `true` (varsayılan) veya `false` . |
| IncludeClaimsFromTechnicalProfile | 0:1 | Tüm giriş ve çıkış taleplerinin bu teknik profile eklenmesini istediğiniz teknik profilin bir tanımlayıcısı. Başvurulan teknik profil aynı ilke dosyasında tanımlanmalıdır. |
| Includetekchnicalprofile |0:1 | Bu teknik profile tüm verilerin eklenmesini istediğiniz teknik profilin bir tanımlayıcısı. |
| Useteknisyen Alprofileforsessionmanagement | 0:1 | Oturum yönetimi için kullanılacak farklı bir teknik profil. |
|Enabledforuser, neys| 0:1 |Teknik profilin bir Kullanıcı yolculuğunda yürütülüp yürütülmeyeceğini denetler. |

## <a name="protocol"></a>Protokol

**Protokol** öğesi, diğer tarafla iletişim için kullanılacak protokolü belirtir. **Protokol** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Ad | Yes | Teknik profilin bir parçası olarak kullanılan Azure AD B2C tarafından desteklenen geçerli bir protokol adı. Olası değerler şunlardır,,,, `OAuth1` `OAuth2` `SAML2` `OpenIdConnect` `Proprietary` veya `None` . |
| İşleyici | No | Protokol adı olarak ayarlandığında `Proprietary` , protokol işleyicisini belirlemekte Azure AD B2C tarafından kullanılan derlemenin adını belirtir. |

## <a name="metadata"></a>Meta veri

**Meta veri** öğesi, belirli bir protokole ilişkin ilgili yapılandırma seçeneklerini içerir. Desteklenen meta verilerin listesi ilgili [Teknik profil](#types-of-technical-profiles) belirtiminde belgelenmiştir. **Meta veri** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| Öğe | 0: n | Teknik profille ilgili meta veriler. Her bir teknik profil türünün farklı bir meta veri öğesi kümesi vardır. Daha fazla bilgi için bkz. Teknik profil türleri bölümü. |

### <a name="item"></a>Öğe

**Meta veri** öğesinin **Item** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Anahtar | Yes | Meta veri anahtarı. Meta veri öğelerinin listesi için her [Teknik profil türüne](#types-of-technical-profiles) bakın. |

Aşağıdaki örnek, [OAuth2 Technical PROFILE](oauth2-technical-profile.md#metadata)ile ilgili meta verilerin kullanımını gösterir.

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  ...
  <Metadata>
    <Item Key="ProviderName">facebook</Item>
    <Item Key="authorization_endpoint">https://www.facebook.com/dialog/oauth</Item>
    <Item Key="AccessTokenEndpoint">https://graph.facebook.com/oauth/access_token</Item>
    <Item Key="HttpBinding">GET</Item>
    <Item Key="UsePolicyInRedirectUri">0</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

Aşağıdaki örnek, [REST API teknik profiliyle](restful-technical-profile.md#metadata)ilgili meta verilerin kullanımını gösterir.

```xml
<TechnicalProfile Id="REST-Validate-Email">
  ...
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

## <a name="cryptographic-keys"></a>Şifreleme anahtarları

Tümleştirildiği hizmetlerle güven sağlamak için, Azure AD B2C gizli dizileri ve sertifikaları [ilke anahtarları](policy-keys-overview.md)biçiminde depolar. Teknik profil yürütme sırasında, Azure AD B2C Azure AD B2C ilkesi anahtarlarından şifreleme anahtarlarını alır. Azure AD B2C, güven oluşturmak veya bir belirteci şifrelemek ya da imzalamak için anahtarları kullanır. Bu güvenler aşağıdakilerden oluşur:

- [OAuth1](oauth1-technical-profile.md#cryptographic-keys), [OAuth2](oauth2-technical-profile.md#cryptographic-keys)ve [SAML](identity-provider-generic-saml.md) Identity sağlayıcılarıyla Federasyon.
- [REST API hizmetleriyle](secure-rest-api.md)bağlantıyı güvenli hale getirme.
- [JWT](jwt-issuer-technical-profile.md#cryptographic-keys) ve [SAML](saml-service-provider.md) belirteçlerini imzalama ve şifreleme.

**Cryptographickeys** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| Anahtar | 1: n | Bu teknik profilde kullanılan bir şifreleme anahtarı. |

### <a name="key"></a>Anahtar

**Anahtar** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Id | No | İlke dosyasındaki diğer öğelerden başvurulan belirli bir anahtar çiftinin benzersiz tanıtıcısı. |
| Storagereferenceıd değerine | Yes | İlke dosyasındaki diğer öğelerden başvurulan bir depolama anahtarı kapsayıcısının tanımlayıcı değeri. |

## <a name="input-claims-transformations"></a>Giriş talep dönüştürmeleri

**Inputclaimstransformations** öğesi, giriş taleplerini değiştirmek veya yenilerini oluşturmak için kullanılan bir giriş talepleri dönüştürme öğeleri koleksiyonu içerebilir.

Talep dönüştürme koleksiyonundaki önceki talep dönüştürmesinin çıkış talepleri, sonraki bir giriş talebi dönüştürmesinin giriş talepleri olabilir. Bu şekilde, birbirlerine bağlı bir talep dönüştürmeleri dizisine sahip olabilirsiniz.

**Inputclaimstransformations** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| Inputclaimstranssize | 1: n | Talep sağlayıcısına veya bağlı olan tarafa herhangi bir talep gönderilmeden önce yürütülmesi gereken bir talep dönüştürmesi tanımlayıcısı. Bir talep dönüştürmesi, var olan ClaimsSchema taleplerini değiştirmek veya yenilerini oluşturmak için kullanılabilir. |

### <a name="inputclaimstransformation"></a>Inputclaimstranssize

**Inputclaimstransbir** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ReferenceId | Yes | İlke dosyasında veya üst ilke dosyasında zaten tanımlanmış olan bir talep dönüşümünün tanımlayıcısı. |

Aşağıdaki teknik profiller **Createothermailsfromemail** talep dönüşümüne başvurur. Talep dönüştürmesi, `email` `otherMails` verileri dizine kalıcı yapmadan önce koleksiyona ait değeri ekler.

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  ...
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  ...
</TechnicalProfile>
```

## <a name="input-claims"></a>Giriş talepleri

**Inputclaim** öğesi, teknik profil için kullanılan talep çantasından gelen talepleri seçer. Örneğin, [kendinden onaylanan bir teknik profil](self-asserted-technical-profile.md) , kullanıcının sağladığı çıkış taleplerini önceden doldurmak için giriş taleplerini kullanır. REST API teknik bir profil, giriş parametrelerini REST API uç noktasına göndermek için giriş taleplerini kullanır. Azure AD, bir hesabı okumak, güncelleştirmek veya silmek için benzersiz bir tanımlayıcı olarak bir giriş talebi kullanır.

**Inputclaim** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| Inputclaim | 1: n | Beklenen giriş talep türü. |

### <a name="inputclaim"></a>Inputclaim

**Inputclaim** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | Bir talep türünün tanımlayıcısı. Talep, ilke dosyası veya üst ilke dosyasının talep şeması bölümünde zaten tanımlanmış. |
| Değerinin | No | ClaimTypeReferenceId tarafından belirtilen talep yoksa, sonuçta elde edilen talebin teknik profil tarafından bir ınputclaim öğesi olarak kullanılabilmesi için bir talep oluşturmak için kullanılacak varsayılan değer. |
| PartnerClaimType | No | Belirtilen ilke talep türünün eşlendiği dış iş ortağının talep türünün tanımlayıcısı. PartnerClaimType özniteliği belirtilmemişse, belirtilen ilke talep türü aynı ada sahip iş ortağı talep türüne eşlenir. Talep türü adınız diğer taraftan farklıysa bu özelliği kullanın. Örnek, ilk talep *adı, iş* ortağı *First_name* adlı bir talep kullandığında bir örnektir. |

## <a name="display-claims"></a>Talepleri görüntüle

**DisplayClaim** öğesi, kullanıcıdan veri toplamak üzere ekranda sunulacak taleplerin bir listesini içerir. Görüntüleme talepleri koleksiyonunda, bir [talep türüne](claimsschema.md) veya oluşturduğunuz bir [görüntü denetimine](display-controls.md) başvuru ekleyebilirsiniz.

- Talep türü, ekranda görüntülenecek talebe yönelik bir başvurudur.
  - Kullanıcıyı belirli bir talep için bir değer sağlamaya zorlamak için, **DisplayClaim** öğesinin **gerekli** özniteliğini olarak ayarlayın `true` .
  - Görüntüleme taleplerinin değerlerini önceden doldurmak için, daha önce açıklanan giriş taleplerini kullanın. Öğesi de varsayılan bir değer içerebilir.
  - **DisplayClaim** koleksiyonundaki **ClaimType** öğesi, **userınputtype** öğesini Azure AD B2C tarafından desteklenen herhangi bir kullanıcı giriş türüne ayarlamış olmalıdır. Örnekler `TextBox` veya `DropdownSingleSelect` .
- Görüntüleme denetimi, özel işlevleri olan ve Azure AD B2C arka uç hizmetiyle etkileşime sahip bir kullanıcı arabirimi öğesidir. Kullanıcının arka uçta doğrulama teknik profilini çağıran sayfada eylemler gerçekleştirmesine olanak sağlar. Örneğin, bir e-posta adresi, telefon numarası veya müşteri bağlılık programı numarası doğrulanıyor.

**DisplayClaim** 'teki öğelerin sırası, Azure AD B2C ekranda talepleri işleyen sırayı belirtir.

**DisplayClaim** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| DisplayClaim | 1: n | Beklenen giriş talep türü. |

### <a name="displayclaim"></a>DisplayClaim

**DisplayClaim** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | No | İlke dosyası veya üst ilke dosyasında ClaimsSchema bölümünde zaten tanımlanmış olan bir talep türünün tanımlayıcısı. |
| Displaycontrolreferenceıd | No | İlke dosyası veya üst ilke dosyasında ClaimsSchema bölümünde zaten tanımlanmış olan bir [görüntüleme denetiminin](display-controls.md) tanımlayıcısı. |
| Gerekli | No | Görüntüleme talebinin gerekli olup olmadığını belirtir. |

Aşağıdaki örnek, kendi kendini onaylanan bir teknik profilde, görüntüleme taleplerinin ve görüntüleme denetimlerinin kullanımını gösterir.

![Görüntüleme taleplerini içeren, kendi kendine onaylanan bir teknik profili gösteren ekran görüntüsü.](./media/technical-profiles/display-claims.png)

Aşağıdaki teknik profilde:

- İlk görüntüleme talebi, `emailVerificationControl` e-posta adresini toplayıp doğrulayan görüntü denetimine bir başvuru yapar.
- Beşinci görüntüleme talebi, `phoneVerificationControl` bir telefon numarasını toplayıp doğrulayan görüntü denetimine bir başvuru yapar.
- Diğer görüntüleme talepleri, kullanıcıdan toplanacak ClaimType öğeleridir.

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

## <a name="persisted-claims"></a>Kalıcı talepler

**PersistedClaims** öğesi, Ilke ve Azure AD öznitelik adındaki [Claimsschema](claimsschema.md) bölümünde zaten tanımlanmış olan bir talep türü arasında olası eşleme BILGILERINI içeren bir [Azure AD teknik profili](active-directory-technical-profile.md) tarafından kalıcı hale gelmelidir.

Azure AD öznitelik adını içeren **Partnerclaimtype** özniteliği belirtilmediği takdirde talebin adı [Azure ad özniteliğinin](user-profile-attributes.md) adıdır.

**PersistedClaims** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | Kalıcı yapılacak talep türü. |

### <a name="persistedclaim"></a>PersistedClaim

**PersistedClaim** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | İlke dosyası veya üst ilke dosyasında ClaimsSchema bölümünde zaten tanımlanmış olan bir talep türünün tanımlayıcısı. |
| Değerinin | No | Talep yoksa bir talep oluşturmak için kullanılacak varsayılan değer. |
| PartnerClaimType | No | Belirtilen ilke talep türünün eşlendiği dış iş ortağının talep türünün tanımlayıcısı. PartnerClaimType özniteliği belirtilmemişse, belirtilen ilke talep türü aynı ada sahip iş ortağı talep türüne eşlenir. Talep türü adınız diğer taraftan farklıysa bu özelliği kullanın. Örnek, ilk talep *adı, iş* ortağı *First_name* adlı bir talep kullandığında bir örnektir. |

Aşağıdaki örnekte, yeni yerel hesap oluşturan **AAD-UserWriteUsingLogonEmail** teknik profili veya [başlangıç paketi](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)aşağıdaki talepleri sürdürür:

```xml
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
  <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
  <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
  <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
  <PersistedClaim ClaimTypeReferenceId="givenName" />
  <PersistedClaim ClaimTypeReferenceId="surname" />
</PersistedClaims>
```

## <a name="output-claims"></a>Çıkış talepleri

**Outputclaim** öğesi, teknik profil tamamlandıktan sonra talep çantasına geri döndürülen talepler koleksiyonudur. Bu talepleri, sonraki düzenlemeler adımında veya çıkış talebi dönüşümlerindeki kullanılabilir. **Outputclaim** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 1: n | Beklenen çıkış talep türü. |

### <a name="outputclaim"></a>OutputClaim

**Outputclaim** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | İlke dosyası veya üst ilke dosyasında ClaimsSchema bölümünde zaten tanımlanmış olan bir talep türünün tanımlayıcısı. |
| Değerinin | No | Talep yoksa bir talep oluşturmak için kullanılacak varsayılan değer. |
|AlwaysUseDefaultValue |No |Varsayılan değerin kullanımını zorlar. |
| PartnerClaimType | No | Belirtilen ilke talep türünün eşlendiği dış iş ortağının talep türünün tanımlayıcısı. İş ortağı talep türü özniteliği belirtilmezse, belirtilen ilke talep türü aynı ada sahip iş ortağı talep türüne eşlenir. Talep türü adınız diğer taraftan farklıysa bu özelliği kullanın. Örnek, ilk talep *adı, iş* ortağı *First_name* adlı bir talep kullandığında bir örnektir. |

## <a name="output-claims-transformations"></a>Çıkış talepleri dönüşümleri

**Outputclaimstransformations** öğesi bir **outputclaimstransreference** öğeleri koleksiyonu içerebilir. Çıkış talep dönüştürmeleri, çıkış taleplerini değiştirmek veya yenilerini oluşturmak için kullanılır. Yürütmeden sonra, çıkış talepleri talep çantasına geri konur. Bu talepleri sonraki düzenlemeler adımında kullanabilirsiniz.

Talep dönüştürme koleksiyonundaki önceki talep dönüştürmesinin çıkış talepleri, sonraki bir giriş talebi dönüştürmesinin giriş talepleri olabilir. Bu şekilde, birbirlerine bağlı bir talep dönüştürmeleri dizisine sahip olabilirsiniz.

**Outputclaimstransformations** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| Outputclaimstranssize | 1: n | Talepler, talep sağlayıcısına veya bağlı olan tarafa gönderilmeden önce yürütülmesi gereken talep dönüştürmelerinin tanımlayıcıları. Bir talep dönüştürmesi, var olan ClaimsSchema taleplerini değiştirmek veya yenilerini oluşturmak için kullanılabilir. |

### <a name="outputclaimstransformation"></a>Outputclaimstranssize

**Outputclaimstransbir** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ReferenceId | Yes | İlke dosyasında veya üst ilke dosyasında zaten tanımlanmış olan bir talep dönüşümünün tanımlayıcısı. |

Aşağıdaki teknik profilde, hesabın etkin olup olmadığını değerlendirmek için AssertAccountEnabledIsTrue talep dönüştürmesi, dizinden talebi okuduktan sonra değil `accountEnabled` .

```xml
<TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
  ...
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="accountEnabled" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
  ...
</TechnicalProfile>
```

## <a name="validation-technical-profiles"></a>Doğrulama teknik profilleri

Doğrulama teknik profili, [kendi kendine onaylanan bir teknik profilde](self-asserted-technical-profile.md#validation-technical-profiles)çıkış taleplerini doğrulamak için kullanılır. Bir doğrulama teknik profili, [Azure AD](active-directory-technical-profile.md) veya [REST API](restful-technical-profile.md)gibi herhangi bir protokolden alınan sıradan bir teknik profildir. Doğrulama teknik profili, çıkış taleplerini döndürür veya hata kodu döndürür. Hata iletisi ekranda kullanıcıya işlenir ve bu da kullanıcının yeniden denemesini sağlar.

Aşağıdaki diyagramda, Azure AD B2C kullanıcının kimlik bilgilerini doğrulamak için bir doğrulama teknik profili nasıl kullandığı gösterilmektedir.

![Doğrulama teknik profili akışını gösteren diyagram.](./media/technical-profiles/validation-technical-profile.png)

**Validation, Alprofiles** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| Validationteknisyen Alprofıle | 1: n | Kullanılan teknik profillerin tanımlayıcıları, başvuru yapan teknik profilin bazı veya tüm çıktı taleplerini doğrular. Başvurulan teknik profilin tüm giriş talepleri, başvuru yapan teknik profilin çıkış taleplerinden gelmelidir. |

### <a name="validationtechnicalprofile"></a>Validationteknisyen Alprofıle

**Validation, Alprofile** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ReferenceId | Yes | İlke dosyasında veya üst ilke dosyasında zaten tanımlanmış olan bir teknik profilin tanımlayıcısı. |

## <a name="subjectnaminginfo"></a>Subjectnamingınfo

**Subjectnamingınfo** öğesi, [bağlı olan taraf ilkesindeki](relyingparty.md#subjectnaminginfo)belirteçlerde kullanılan konu adını tanımlar. **Subjectnamingınfo** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ClaimType | Yes | İlke dosyasında ClaimsSchema bölümünde zaten tanımlanmış olan bir talep türünün tanımlayıcısı. |

## <a name="include-technical-profile"></a>Teknik profili dahil et

Teknik bir profil, ayarları değiştirmek veya yeni işlevsellik eklemek için başka bir teknik profil içerebilir. **Includetekchnicalprofile** öğesi, bir teknik profilin türetildiği ortak teknik profile bir başvurudur. İlke öğelerinizin yedeklilik ve karmaşıklığını azaltmak için, temel öğeleri paylaşan birden çok teknik profiliniz olduğunda dahil etme ' yi kullanın. Ortak bir yapılandırma kümesiyle ortak bir teknik profil kullanın, ortak teknik profil içeren belirli görev teknik profillerinin yanı da.

Farklı senaryolar için farklı talepler kümesi göndermeniz gereken tek bir uç nokta ile [REST API teknik bir profiliniz](restful-technical-profile.md) olduğunu varsayalım. REST API uç noktası URI 'SI, meta veriler, kimlik doğrulama türü ve şifreleme anahtarları gibi paylaşılan işlevlerle ortak bir teknik profil oluşturun. Ortak teknik profili içeren belirli bir görev teknik profili oluşturun. Ardından giriş ve çıkış taleplerini ekleyin veya bu teknik profille ilgili REST API uç nokta URI 'sinin üzerine yazın.

**Includetekchnicalprofile** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ReferenceId | Yes | İlke dosyasında veya üst ilke dosyasında zaten tanımlanmış olan bir teknik profilin tanımlayıcısı. |

Aşağıdaki örnek, ekleme işlevinin kullanımını göstermektedir:

- **REST-API-ortak**: temel yapılandırmayla ortak bir teknik profil.
- **Rest-ValidateProfile**: **REST API-ortak** teknik profilini içerir ve giriş ve çıkış taleplerini belirtir.
- **Rest-UpdateProfile**: **REST-API ortak** teknik profilini içerir, giriş taleplerini belirtir ve `ServiceUrl` meta verilerin üzerine yazar.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-API-Common">
      <DisplayName>Base REST API configuration</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
      </CryptographicKeys>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Validate the account and return promo code</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-UpdateProfile">
       <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/update</Item>
      </Metadata>
      <DisplayName>Update the user profile</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="multilevel-inclusion"></a>Çok düzeyli içerme

Teknik bir profil, tek bir teknik profil içerebilir. Dahil etme seviyelerinin sayısı için sınır yoktur. Örneğin, **AAD-userreadusingalternativesecurityıd-NoError** teknik profili **AAD-userreadusingalternativesecurityıd**' yi içerir. Bu teknik profil, `RaiseErrorIfClaimsPrincipalDoesNotExist` meta veri öğesini olarak ayarlar `true` ve bir sosyal hesap dizinde yoksa bir hata oluşturur. **AAD-Userreadusingalternativesecurityıd-NOERROR** bu davranışı geçersiz kılar ve bu hata iletisini devre dışı bırakır.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-Userreadusingalternativesecurityıd** `AAD-Common` Teknik profili içerir.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Hem **AAD-Userreadusingalternativesecurityıd-NoError** ve **AAD-Userreadusingalternativesecurityıd** , AAD **ortak** teknik profilinde belirtildiği için gerekli **protokol** öğesini belirtmiyor.

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

## <a name="use-technical-profile-for-session-management"></a>Oturum yönetimi için teknik profili kullanma

**Useteknisyen Alprofileforsessionmanagement** öğesi [SSO oturumu teknik profiline](custom-policy-reference-sso.md)başvurur. **Useteknisyen Alprofileforsessionmanagement** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ReferenceId | Yes | İlke dosyasında veya üst ilke dosyasında zaten tanımlanmış olan bir teknik profilin tanımlayıcısı. |

## <a name="enabled-for-user-journeys"></a>Kullanıcı yolculukları için etkinleştirildi

Kullanıcı yolculuğunda bulunan [Claimsproviderselection](userjourneys.md#claims-provider-selection) , talep sağlayıcı seçim seçeneklerinin listesini ve bunların sıralarını tanımlar. **Enabledforuser, neys** öğesi ile Kullanıcı için hangi talep sağlayıcısının kullanılabilir olduğunu filtrelemeniz gerekir. **Enabledforuser, neys** öğesi aşağıdaki değerlerden birini içerir:

- **Her zaman**: teknik profili yürütür.
- **Hiçbir** bir şekilde: teknik profili atlar.
- **OnClaimsExistence**: yalnızca teknik profilde belirtilen belirli bir talep varsa yürütülür.
- **OnItemExistenceInStringCollectionClaim**: yalnızca bir dize koleksiyonu talebinde bir öğe varsa yürütülür.
- **Onitemabsenceınstringcollectionclaim**: yalnızca bir öğe dize koleksiyonu talebinde yoksa yürütülür.

**OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** veya **Onitemabsenceınstringcollectionclaim** kullanmak için aşağıdaki meta verileri sağlamanız gerekir:

- **Claimtypeondenhtoenable**: değerlendirilecek olan talebin türünü belirtir.
- **Claimvalueondenhtoenable**: karşılaştırılacak değeri belirtir.

Aşağıdaki teknik profil yalnızca **IdentityProviders** dize koleksiyonu şu değeri içeriyorsa yürütülür `facebook.com` :

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
