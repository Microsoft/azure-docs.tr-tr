---
title: RelyingParty-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C özel bir ilkenin RelyingParty öğesini belirtin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b1bfa945843d185a46f1f1d79fd4dab0e991c769
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063803"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**RelyingParty** öğesi Azure Active Directory B2C (Azure AD B2C) için geçerli istek için zorlamak üzere Kullanıcı yolculuğunu belirtir. Ayrıca, bağlı olan taraf (RP) uygulamasının verilen belirtecin bir parçası olarak ihtiyacı olan taleplerin listesini de belirtir. Web, mobil veya masaüstü uygulaması gibi bir RP uygulaması, RP ilke dosyasını çağırır. RP ilke dosyası, oturum açma, parola sıfırlama veya bir profili düzenlemeyle ilgili belirli bir görevi yürütür. Birden çok uygulama aynı RP ilkesini kullanabilir ve tek bir uygulama birden çok ilke kullanabilir. Tüm RP uygulamaları taleplerle aynı belirteci alır ve Kullanıcı aynı kullanıcı yolculuğuna gider.

Aşağıdaki örnek *B2C_1A_signup_signin* ilke dosyasında bir **RelyingParty** öğesi gösterir:

```XML
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
      <SingleSignOn Scope="TrustFramework" KeepAliveInDays="7"/>
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

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| Defaultuseryolculuğu | 1:1 | RP uygulaması için varsayılan kullanıcı yolculuğu. |
| Kullanıcıbağlantısı Neyıdavranışları | 0:1 | Kullanıcı yolculuğu davranışlarının kapsamı. |
| Teknisyen | 1:1 | RP uygulaması tarafından desteklenen teknik bir profil. Teknik profil, RP uygulamasının Azure AD B2C ile iletişim kurabilmesi için bir sözleşme sağlar. |

## <a name="defaultuserjourney"></a>Defaultuseryolculuğu

`DefaultUserJourney` Öğesi, genellikle temel veya uzantılar ilkesinde tanımlanan Kullanıcı yolculuğu tanıtıcısına yönelik bir başvuru belirtir. Aşağıdaki örneklerde, **RelyingParty** öğesinde belirtilen kaydolma veya oturum açma Kullanıcı yolculuğu gösterilmektedir:

*B2C_1A_signup_signin* ilkesi:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* veya *B2C_1A_TrustFrameworkExtensionPolicy*:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

**Defaultuseryolculuney** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ReferenceId | Evet | İlkede Kullanıcı yolculuğu için bir tanımlayıcı. Daha fazla bilgi için bkz. [Kullanıcı yolculukları](userjourneys.md) |

## <a name="userjourneybehaviors"></a>Kullanıcıbağlantısı Neyıdavranışları

**Userınewydavranışlar** öğesi aşağıdaki öğeleri içerir:

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Kullanıcı yolculuğunun tek oturum açma (SSO) oturum davranışının kapsamı. |
| Ssosession |0:1 | Oturumun kimlik doğrulama davranışı. Olası değerler: `Rolling` veya `Absolute`. `Rolling` Değer (varsayılan), Kullanıcı uygulamada sürekli olarak etkin olduğu sürece kullanıcının oturum açmış olarak kaldığını gösterir. `Absolute` Değer, kullanıcının uygulama oturumu ömrü ile belirtilen zaman süresinden sonra yeniden kimlik doğrulaması zorlaması gerektiğini gösterir. |
| Sessionexpirınseconds | 0:1 | Başarılı kimlik doğrulamasından sonra kullanıcının tarayıcısında depolanan bir tamsayı olarak belirtilen Azure AD B2C's oturum tanımlama bilgisinin ömrü. |
| Bağlantı, Neyelik | 0:1 | Kullanılacak Azure Application Insights izleme anahtarı. |
| ContentDefinitionParameters | 0:1 | İçerik tanımı yük URI 'sine eklenecek anahtar değer çiftlerinin listesi. |

### <a name="singlesignon"></a>SingleSignOn

**SingleSignon** öğesi aşağıdaki öznitelikte yer alır:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| `Scope` | Evet | Çoklu oturum açma davranışının kapsamı. Olası değerler: `Suppressed`, `Tenant`, `Application`veya. `Policy` `Suppressed` Değer, davranışın bastırıldığını gösterir. Örneğin, çoklu oturum açma oturumunda Kullanıcı için oturum korunmaz ve kullanıcıdan her zaman bir kimlik sağlayıcısı seçimi istenir. `TrustFramework` Değer, güven çerçevesindeki tüm ilkeler için davranışın uygulanacağını gösterir. Örneğin, bir güven çerçevesinin iki ilkesiyle ilgili olarak gezindikleri bir Kullanıcı, bir kimlik sağlayıcı seçimi istenmez. `Tenant` Değer, davranışın Kiracıdaki tüm ilkelere uygulanacağını gösterir. Örneğin, bir kiracı için iki ilke ile gezinmekte olan bir kullanıcıya bir kimlik sağlayıcısı seçimi istenmez. `Application` Değer, davranışın istek yapan uygulamanın tüm ilkelerine uygulanacağını gösterir. Örneğin, bir uygulama için iki ilke ile gezinmekte olan bir kullanıcıya bir kimlik sağlayıcısı seçimi istenmez. `Policy` Değer, davranışın yalnızca bir ilke için geçerli olduğunu gösterir. Örneğin, bir güven çerçevesi için iki ilke ile gezinerek bir Kullanıcı, ilkeler arasında geçiş yaparken bir kimlik sağlayıcısı seçimine sorulur. |
| Keepaliveındays | Evet | Kullanıcının ne kadar süreyle oturum açdığına ilişkin denetim. Değerin 0 olarak ayarlanması, KMSI işlevini devre dışı bırakır. Daha fazla bilgi için bkz. Oturumumu [açık tut](active-directory-b2c-reference-kmsi-custom.md). |

## <a name="journeyinsights"></a>Bağlantı, Neyelik

I, **Newınghts** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| TelemetryEngine | Evet | Değer olmalıdır `ApplicationInsights`. |
| Instrumentationkey | Evet | Application Insights öğesi için izleme anahtarını içeren dize. |
| DeveloperMode | Evet | Olası değerler: `true` veya `false`. Application Insights `true`, işlem hattı aracılığıyla Telemetriyi bir şekilde yükseltir. Bu ayar geliştirme için iyidir, ancak yüksek birimlerde kısıtlıdır. ayrıntılı etkinlik günlükleri yalnızca özel ilkelerin geliştirilmesine yardımcı olmak için tasarlanmıştır. Üretim ortamında geliştirme modunu kullanmayın. Günlükler, geliştirme sırasında kimlik sağlayıcılardan gelen ve giden tüm talepleri toplar. Üretimde kullanılıyorsa, geliştirici, sahip oldukları App Insights günlüğünde toplanan PII (özel olarak tanımlanabilen bilgiler) için sorumluluğu kabul eder. Bu ayrıntılı Günlükler yalnızca bu değer olarak `true`ayarlandığında toplanır.|
| ClientEnabled | Evet | Olası değerler: `true` veya `false`. `true`, İzleme sayfası görünümü ve istemci tarafı hataları için Application Insights istemci tarafı betiği gönderir. |
| Sunucuetkin | Evet | Olası değerler: `true` veya `false`. Varsa `true`, Application Insights için var olan kullanıcıgünneyıkaydedicisi JSON 'sini özel bir olay olarak gönderir. |
| TelemetryVersion | Evet | Değer olmalıdır `1.0.0`. |

Daha fazla bilgi için bkz. [günlükleri toplama](active-directory-b2c-troubleshoot-custom.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Azure AD B2C özel ilkeleri kullanarak bir sorgu dizesinde bir parametre gönderebilirsiniz. Parametreyi HTML uç noktanıza ileterek sayfa içeriğini dinamik olarak değiştirebilirsiniz. Örneğin web veya mobil uygulamanızdan ilettiğiniz bir parametreye göre Azure AD B2C kaydolma veya oturum açma sayfanızdaki arka plan görüntüsünü değiştirebilirsiniz. Azure AD B2C, sorgu dizesi parametrelerini aspx dosyası gibi dinamik HTML dosyanıza geçirir.

Aşağıdaki örnek, sorgu dizesinde `campaignId` `hawaii` değeri olan adlı bir parametre geçirir:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

**Contentdefinitionparameters** öğesi aşağıdaki öğeyi içerir:

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | Bir içerik tanımı yük URI 'sinin sorgu dizesine eklenen anahtar değer çiftini içeren bir dize. |

**Contentdefinitionparameter** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Name | Evet | Anahtar değer çiftinin adı. |

Daha fazla bilgi için bkz [. özel ilkeler kullanarak dinamik içerikle Kullanıcı arabirimini yapılandırma](active-directory-b2c-ui-customization-custom-dynamic.md)

## <a name="technicalprofile"></a>Teknisyen

**Teknisyen** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Id | Evet | Değer olmalıdır `PolicyProfile`. |

**Teknisyen** aşağıdaki öğeleri içerir:

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| DisplayName | 0:1 | Kullanıcılara görüntülenen teknik profilin adını içeren dize. |
| Açıklama | 0:1 | Kullanıcılara görüntülenen teknik profilin açıklamasını içeren dize. |
| Protocol | 1:1 | Federasyon için kullanılan protokol. |
| Meta Veriler | 0:1 | Bağlı olan taraf ve diğer topluluk katılımcıları arasında etkileşimi yapılandırmak üzere bir işlem sırasında uç noktayla iletişim için kullanılan anahtar/değer çiftlerinin *öğe* koleksiyonu. |
| Outputclaim | 0:1 | Teknik profilde çıkış olarak gerçekleştirilen talep türlerinin listesi. Bu öğelerin her biri, **Claimsschema** bölümünde veya bu ilke dosyasının devraldığı bir ilkede zaten tanımlanmış olan bir **ClaimType** başvurusu içerir. |
| Subjectnamingınfo | 0:1 | Belirteçlerde kullanılan konu adı. |

**Protokol** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Name | Evet | Teknik profilin bir parçası olarak kullanılan Azure AD B2C tarafından desteklenen geçerli bir protokol adı. Olası değerler: `OpenIdConnect` veya `SAML2`. Değer `OpenIdConnect` , OpenID Foundation belirtimine göre OpenID Connect 1,0 Protokol standardını temsil eder. , `SAML2` Oassıs belirtimine göre SAML 2,0 Protokol standardını temsil eder. Üretimde SAML belirteci kullanmayın. |

## <a name="outputclaims"></a>Outputclaim

**Outputclaim** öğesi aşağıdaki öğeyi içerir:

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| outputClaim | 0: n | Bağlı olan tarafın abone olduğu ilke için desteklenen listedeki beklenen talep türünün adı. Bu talep teknik profil için bir çıktı görevi görür. |

**Outputclaim** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Evet | İlke dosyasında **Claimsschema** bölümünde zaten tanımlanmış bir **ClaimType** başvurusu. |
| Değerinin | Hayır | Talep değeri boş ise kullanılabilecek varsayılan değer. |
| PartnerClaimType | Hayır | Talebi, ClaimType tanımında yapılandırıldığı şekilde farklı bir adla gönderir. |

### <a name="subjectnaminginfo"></a>Subjectnamingınfo

**Subjectnameingınfo** öğesiyle, belirteç konusunun değerini denetlersiniz:
- **JWT belirteci** - `sub` talep. Bu, belirtecin, uygulamanın kullanıcısı gibi bilgileri onaylama konusunda bir sorumluyla ilgilidir. Bu değer sabittir ve yeniden atanamaz veya tekrar kullanılamaz. Bu, belirtecin bir kaynağa erişmek için ne zaman kullanıldığı gibi güvenli yetkilendirme denetimleri gerçekleştirmek için de kullanılabilir. Varsayılan olarak, konu talebi, dizindeki kullanıcının nesne KIMLIĞIYLE doldurulur. Daha fazla bilgi için bkz. [belirteç, oturum ve çoklu oturum açma yapılandırması](active-directory-b2c-token-session-sso.md).
- **SAML belirteci** - `<Subject><NameID>` konu öğesini tanımlayan öğe.

**Subjectnamingınfo** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ClaimType | Evet | Çıkış talebinin **Partnerclaimtype**öğesine bir başvuru. Çıkış taleplerinin bağlı olan taraf ilkesi **Outputclaim** koleksiyonunda tanımlanması gerekir. |

Aşağıdaki örnekte, bir OpenID Connect bağlı olan tarafın nasıl tanımlanacağı gösterilmektedir. Konu adı bilgisi şu şekilde `objectId`yapılandırılır:

```XML
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
JWT belirteci kullanıcı ObjectID ile `sub` olan talebi içerir:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```


