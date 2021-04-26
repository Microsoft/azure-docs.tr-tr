---
title: RelyingParty-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C özel bir ilkenin RelyingParty öğesini belirtin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b1c8bf5cb8944b990737d557326b2741716bab3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579765"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Bağlı Olan Taraf** öğesi Azure Active Directory B2C'ye (Azure AD B2C) yönelik geçerli istek için zorlanacak kullanıcı yolculuğunu belirtir. Ayrıca verilen belirtecin bir parçası olarak, bağlı olan taraf (RP) uygulamasına gereken taleplerin listesini de belirtir. Web, mobil veya masaüstü uygulaması gibi bir RP uygulaması, RP ilke dosyasını çağırır. RP ilke dosyası, oturum açma, parola sıfırlama veya bir profili düzenlemeyle ilgili belirli bir görevi yürütür. Birden çok uygulama aynı RP ilkesini kullanabilir ve tek bir uygulama birden çok ilke kullanabilir. Tüm RP uygulamaları taleplerle aynı belirteci alır ve Kullanıcı aynı kullanıcı yolculuğuna gider.

Aşağıdaki örnek *B2C_1A_signup_signin* ilke dosyasında bir **RelyingParty** öğesi gösterir:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

İsteğe bağlı **RelyingParty** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| Defaultuseryolculuğu | 1:1 | RP uygulaması için varsayılan kullanıcı yolculuğu. |
| Uç Noktalar | 0:1 | Uç noktaların listesi. Daha fazla bilgi için bkz. [UserInfo uç noktası](userinfo-endpoint.md). |
| Kullanıcıbağlantısı Neyıdavranışları | 0:1 | Kullanıcı yolculuğu davranışlarının kapsamı. |
| Teknisyen | 1:1 | RP uygulaması tarafından desteklenen teknik bir profil. Teknik profil, RP uygulamasının Azure AD B2C ile iletişim kurabilmesi için bir sözleşme sağlar. |

## <a name="endpoints"></a>Uç Noktalar

**Endpoints** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| Uç Nokta | 1:1 | Bir uç noktaya başvuru.|

**Endpoint** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Id | Yes | Uç noktanın benzersiz tanımlayıcısı.|
| Kullanıcıbağlantısı Neyıreferenceıd | Yes | İlkede Kullanıcı yolculuğu için bir tanımlayıcı. Daha fazla bilgi için bkz. [Kullanıcı yolculukları](userjourneys.md)  | 

Aşağıdaki örnekte, [UserInfo uç noktası](userinfo-endpoint.md)ile bağlı olan taraf gösterilmektedir:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
  ...
```

## <a name="defaultuserjourney"></a>Defaultuseryolculuğu

`DefaultUserJourney`Öğesi, temel veya uzantılar ilkesinde tanımlanan Kullanıcı yolculuğunun tanımlayıcısına yönelik bir başvuru belirtir. Aşağıdaki örneklerde, **RelyingParty** öğesinde belirtilen kaydolma veya oturum açma Kullanıcı yolculuğu gösterilmektedir:

*B2C_1A_signup_signin* ilkesi:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* veya *B2C_1A_TrustFrameworkExtensionPolicy*:

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

**Defaultuseryolculuney** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ReferenceId | Yes | İlkede Kullanıcı yolculuğu için bir tanımlayıcı. Daha fazla bilgi için bkz. [Kullanıcı yolculukları](userjourneys.md) |

## <a name="userjourneybehaviors"></a>Kullanıcıbağlantısı Neyıdavranışları

**Userınewydavranışlar** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Kullanıcı yolculuğunun tek oturum açma (SSO) oturum davranışının kapsamı. |
| Ssosession |0:1 | Oturumun kimlik doğrulama davranışı. Olası değerler: `Rolling` veya `Absolute` . `Rolling`Değer (varsayılan), Kullanıcı uygulamada sürekli olarak etkin olduğu sürece kullanıcının oturum açmış olarak kaldığını gösterir. `Absolute`Değer, kullanıcının uygulama oturumu ömrü ile belirtilen zaman süresinden sonra yeniden kimlik doğrulaması zorlaması gerektiğini gösterir. |
| Sessionexpirınseconds | 0:1 | Başarılı kimlik doğrulamasından sonra kullanıcının tarayıcısında depolanan bir tamsayı olarak belirtilen Azure AD B2C's oturum tanımlama bilgisinin ömrü. |
| Bağlantı, Neyelik | 0:1 | Kullanılacak Azure Application Insights izleme anahtarı. |
| ContentDefinitionParameters | 0:1 | İçerik tanımı yük URI 'sine eklenecek anahtar değer çiftlerinin listesi. |
|ScriptExecution| 0:1| Desteklenen [JavaScript](javascript-and-page-layout.md) yürütme modları. Olası değerler: `Allow` veya `Disallow` (varsayılan).
| Bağlantı, Neyıçerçeveleme | 0:1| Bu ilkenin Kullanıcı arabiriminin bir iframe 'e yüklenmesine izin verir. |

### <a name="singlesignon"></a>SingleSignOn

**SingleSignon** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Kapsam | Yes | Çoklu oturum açma davranışının kapsamı. Olası değerler: `Suppressed` , `Tenant` , `Application` veya `Policy` . `Suppressed`Değer, davranışın bastırıldığını ve kullanıcıdan her zaman bir kimlik sağlayıcı seçimi istendiğini gösterir.  `Tenant`Değer, davranışın Kiracıdaki tüm ilkelere uygulanacağını gösterir. Örneğin, bir kiracı için iki ilke ile gezinmekte olan bir kullanıcıya bir kimlik sağlayıcısı seçimi istenmez. `Application`Değer, davranışın istek yapan uygulamanın tüm ilkelerine uygulanacağını gösterir. Örneğin, bir uygulama için iki ilke ile gezinmekte olan bir kullanıcıya bir kimlik sağlayıcısı seçimi istenmez. `Policy`Değer, davranışın yalnızca bir ilke için geçerli olduğunu gösterir. Örneğin, bir güven çerçevesi için iki ilke ile gezinerek bir Kullanıcı, ilkeler arasında geçiş yaparken bir kimlik sağlayıcısı seçimine sorulur. |
| Keepaliveındays | No | Kullanıcının ne kadar süreyle oturum açdığına ilişkin denetim. Değerin 0 olarak ayarlanması, KMSI işlevini devre dışı bırakır. Daha fazla bilgi için bkz. Oturumumu [açık tut](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi). |
|Enforceıdtokenhintonlogout| No|  Daha önce verilen bir KIMLIK belirtecini, son kullanıcının istemci ile geçerli kimlik doğrulamalı oturum hakkında bir ipucu olarak oturum kapatma uç noktasına geçirmeye zorlayın. Olası değerler: `false` (varsayılan) veya `true` . Daha fazla bilgi için bkz. [OpenID Connect Ile web oturumu açma](openid-connect.md).  |


## <a name="journeyinsights"></a>Bağlantı, Neyelik

I, **Newınghts** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| TelemetryEngine | Yes | Değer olmalıdır `ApplicationInsights` . |
| Instrumentationkey | Yes | Application Insights öğesi için izleme anahtarını içeren dize. |
| DeveloperMode | Yes | Olası değerler: `true` veya `false` . `true`Application Insights, işlem hattı aracılığıyla Telemetriyi bir şekilde yükseltir. Bu ayar geliştirme için uygundur, ancak yüksek birimlerde kısıtlanmıştır. Ayrıntılı etkinlik günlükleri yalnızca özel ilkelerin geliştirilmesine yardımcı olmak için tasarlanmıştır. Üretim ortamında geliştirme modunu kullanmayın. Günlükler, geliştirme sırasında kimlik sağlayıcılardan gelen ve giden tüm talepleri toplar. Üretimde kullanılıyorsa, geliştirici, sahip oldukları App Insights günlüğünde toplanan kişisel verilerin sorumluluğunu kabul eder. Bu ayrıntılı Günlükler yalnızca bu değer olarak ayarlandığında toplanır `true` .|
| ClientEnabled | Yes | Olası değerler: `true` veya `false` . `true`, İzleme sayfası görünümü ve istemci tarafı hataları için Application Insights istemci tarafı betiği gönderir. |
| Sunucuetkin | Yes | Olası değerler: `true` veya `false` . Varsa `true` , Application Insights için var olan Kullanıcıgünneyıkaydedicisi JSON 'sini özel bir olay olarak gönderir. |
| TelemetryVersion | Yes | Değer olmalıdır `1.0.0` . |

Daha fazla bilgi için bkz. [günlükleri toplama](troubleshoot-with-application-insights.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Azure AD B2C özel ilkeleri kullanarak bir sorgu dizesinde bir parametre gönderebilirsiniz. Parametreyi HTML uç noktanıza ileterek sayfa içeriğini dinamik olarak değiştirebilirsiniz. Örneğin web veya mobil uygulamanızdan ilettiğiniz bir parametreye göre Azure AD B2C kaydolma veya oturum açma sayfanızdaki arka plan görüntüsünü değiştirebilirsiniz. Azure AD B2C, sorgu dizesi parametrelerini aspx dosyası gibi dinamik HTML dosyanıza geçirir.

Aşağıdaki örnek, `campaignId` sorgu dizesinde değeri olan adlı bir parametre geçirir `hawaii` :

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

**Contentdefinitionparameters** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | Bir içerik tanımı yük URI 'sinin sorgu dizesine eklenen anahtar değer çiftini içeren bir dize. |

**Contentdefinitionparameter** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Ad | Yes | Anahtar değer çiftinin adı. |

Daha fazla bilgi için bkz [. özel ilkeler kullanarak dinamik içerikle Kullanıcı arabirimini yapılandırma](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri)

### <a name="journeyframing"></a>Bağlantı, Neyıçerçeveleme

Giden **Neyçerçeveleme** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Etkin | Yes | Bu ilkenin bir iframe içinde yüklenmesine olanak sağlar. Olası değerler: `false` (varsayılan) veya `true` . |
| Kaynaklar | Yes | İframe 'i barındıracak olan etki alanlarını içerir. Daha fazla bilgi için bkz. [Azure B2C 'yi bir iframe 'e yükleme](embedded-login.md). |

## <a name="technicalprofile"></a>Teknisyen

**Teknisyen** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Id | Yes | Değer olmalıdır `PolicyProfile` . |

**Teknisyen** aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | Teknik profilin adını içeren dize. |
| Description | 0:1 | Teknik profilin açıklamasını içeren dize. |
| Protokol | 1:1 | Federasyon için kullanılan protokol. |
| Meta veri | 0:1 | Bağlı olan taraf ve diğer topluluk katılımcıları arasında etkileşimi yapılandırmak üzere bir işlem sırasında uç noktayla iletişim için kullanılan anahtar/değer çiftlerinin *öğe* koleksiyonu. |
| Outputclaim | 1:1 | Teknik profilde çıkış olarak gerçekleştirilen talep türlerinin listesi. Bu öğelerin her biri, **Claimsschema** bölümünde veya bu ilke dosyasının devraldığı bir ilkede zaten tanımlanmış olan bir **ClaimType** başvurusu içerir. |
| Subjectnamingınfo | 1:1 | Belirteçlerde kullanılan konu adı. |

**Protokol** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Ad | Yes | Teknik profilin bir parçası olarak kullanılan Azure AD B2C tarafından desteklenen geçerli bir protokol adı. Olası değerler: `OpenIdConnect` veya `SAML2` . `OpenIdConnect`Değer, OpenID Foundation belirtimine göre OpenID Connect 1,0 Protokol standardını temsil eder. , `SAML2` Oassıs belirtimine göre SAML 2,0 Protokol standardını temsil eder. |

### <a name="metadata"></a>Meta veri

Protokol olduğunda `SAML` , meta veri öğesi aşağıdaki öğeleri içerir. Daha fazla bilgi için bkz. [Azure AD B2C SAML uygulaması kaydetme seçenekleri](saml-service-provider-options.md).

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Idpınitisenabled Profiletkin | No | IDP tarafından başlatılan akışın desteklenip desteklenmediğini belirtir. Olası değerler: `true` veya `false` (varsayılan). | 
| XmlSignatureAlgorithm | No | Azure AD B2C SAML Yanıtını imzalamak için kullandığı yöntem. Olası değerler: `Sha256` , `Sha384` , `Sha512` veya `Sha1` . Aynı değere sahip her iki tarafta de imza algoritmasını yapılandırdığınızdan emin olun. Yalnızca sertifikanızın desteklediği algoritmayı kullanın. SAML onaylama 'yı yapılandırmak için bkz. [SAML verenin teknik profil meta verileri](saml-issuer-technical-profile.md#metadata). |
| DataEncryptionMethod | No | Azure AD B2C, Gelişmiş Şifreleme Standardı (AES) algoritmasını kullanarak verileri şifrelemek için kullandığı yöntemi gösterir. Meta veri, `<EncryptedData>` SAML yanıtında öğesinin değerini denetler. Olası değerler: `Aes256` (varsayılan), `Aes192` , `Sha512` , veya ` Aes128` . |
| KeyEncryptionMethod| No | Azure AD B2C, verileri şifrelemek için kullanılan anahtarın kopyasını şifrelemek için kullandığı yöntemi gösterir. Meta veri,  `<EncryptedKey>` SAML yanıtında öğesinin değerini denetler. Olası değerler: ` Rsa15` (varsayılan)-RSA ortak anahtar şifreleme standardı (PKCS) sürüm 1,5 algoritması, ` RsaOaep` -RSA En Iyi asimetrik şifreleme doldurma (OAEP) şifreleme algoritması. |
| UseDetachedKeys | No |  Olası değerler: `true` , veya `false` (varsayılan). Değer olarak ayarlandığında `true` Azure AD B2C şifrelenmiş onayların biçimini değiştirir. Ayrılmış anahtarların kullanılması, şifreli onaylama listesini Encryptedrytedassertion 'nin bir alt öğesi olarak, EncryptedData yerine ekler. |
| Wantssignedyanıtları| No | SAML yanıtının bölümünü Azure AD B2C işaretetmeyeceğini belirtir `Response` . Olası değerler: `true` (varsayılan) veya `false` .  |
| Removemilimetre Secondsfromdatetime| No | (Bu, IssueInstant, NotBefore, NotOnOrAfter ve Authnınstant) içindeki tarih saat değerlerinden milisaniyeye kaldırılıp kaldırılmadığını belirtir. Olası değerler: `false` (varsayılan) veya `true` .  |


### <a name="outputclaims"></a>Outputclaim

**Outputclaim** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 0: n | Bağlı olan tarafın abone olduğu ilke için desteklenen listedeki beklenen talep türünün adı. Bu talep teknik profil için bir çıktı görevi görür. |

**Outputclaim** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | İlke dosyasında **Claimsschema** bölümünde zaten tanımlanmış bir **ClaimType** başvurusu. |
| Değerinin | No | Talep değeri boş ise kullanılabilecek varsayılan değer. |
| PartnerClaimType | No | Talebi, ClaimType tanımında yapılandırıldığı şekilde farklı bir adla gönderir. |

### <a name="subjectnaminginfo"></a>Subjectnamingınfo

**Subjectnameingınfo** öğesiyle, belirteç konusunun değerini denetlersiniz:

- **JWT belirteci** - `sub` talep. Bu, belirtecin, uygulamanın kullanıcısı gibi bilgileri onaylama konusunda bir sorumluyla ilgilidir. Bu değer sabittir ve yeniden atanamaz veya tekrar kullanılamaz. Bu, belirtecin bir kaynağa erişmek için ne zaman kullanıldığı gibi güvenli yetkilendirme denetimleri gerçekleştirmek için de kullanılabilir. Varsayılan olarak, konu talebi, dizindeki kullanıcının nesne KIMLIĞIYLE doldurulur. Daha fazla bilgi için bkz. [belirteç, oturum ve çoklu oturum açma yapılandırması](session-behavior.md).
- **SAML belirteci** - `<Subject><NameID>` Konu öğesini tanımlayan öğesi. NameID biçimi değiştirilebilir.

**Subjectnamingınfo** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ClaimType | Yes | Çıkış talebinin **Partnerclaimtype** öğesine bir başvuru. Çıkış taleplerinin bağlı olan taraf ilkesi **Outputclaim** koleksiyonunda tanımlanması gerekir. |
| Biçimlendir | No | SAML onay kutusunda döndürülen **NameID biçimini** ayarlamak Için SAML bağlı olan taraflar için kullanılır. |

Aşağıdaki örnekte, bir OpenID Connect bağlı olan tarafın nasıl tanımlanacağı gösterilmektedir. Konu adı bilgisi şu şekilde yapılandırılır `objectId` :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
JWT belirteci `sub` Kullanıcı ObjectID ile olan talebi içerir:

```json
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```

Aşağıdaki örnek, bir SAML bağlı olan tarafın nasıl tanımlanacağını göstermektedir. Konu adı bilgisi olarak yapılandırılır `objectId` ve NameID `format` sağlanmış olur:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient"/>
  </TechnicalProfile>
</RelyingParty>
```
