---
title: Azure Active Directory B2C bir özel ilkede daha fazla teknik profil tanımlayın | Microsoft Docs
description: Azure Active Directory B2C bir özel ilkede, bir yeniden teknik profil tanımlayın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 13eedeb66d826d212b814fac321f920e78758cb8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063730"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C özel ilkesinde, yeniden teknik bir teknik profil tanımlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C), kendi Reststeğiniz hizmetiniz için destek sağlar. Azure AD B2C, verileri bir giriş talep koleksiyonundaki yeniden takip eden hizmete gönderir ve verileri bir çıkış talep koleksiyonunda geri alır. Yeniden hizmet tümleştirmesiyle şunları yapabilirsiniz:

- **Kullanıcı giriş verilerini doğrulama** -hatalı oluşturulmuş verilerin Azure AD B2C kalıcı olmasını önler. Kullanıcının değeri geçerli değilse, yeniden deneme hizmetiniz, kullanıcıdan bir giriş sağlamasını bildiren bir hata mesajı döndürür. Örneğin, Kullanıcı tarafından girilen e-posta adresinin müşterinizin veritabanında mevcut olduğunu doğrulayabilirsiniz.
- **Giriş taleplerinin üzerine yaz** -giriş taleplerdeki değerleri yeniden biçimlendirmenizi sağlar. Örneğin, bir Kullanıcı ilk adı küçük harfle veya tüm büyük harflerde girerse, adı yalnızca ilk harfi büyük harfle biçimlendirebilirsiniz.
- **Zenginleştirme Kullanıcı verileri** -kurumsal iş kolu uygulamalarıyla daha fazla tümleştirme yapmanızı sağlar. Örneğin, yeniden deneme hizmetiniz kullanıcının e-posta adresini alabilir, müşterinin veritabanını sorgulayabilir ve Azure AD B2C için kullanıcının bağlılık programı numarasını döndürebilir. Dönüş talepleri depolanabilir, sonraki düzenleme adımlarında değerlendirilebilir veya erişim belirtecine dahil edilebilir.
- **Özel iş mantığını Çalıştır** -anında iletme bildirimleri göndermenize, kurumsal veritabanlarını güncelleştirmenize, Kullanıcı geçiş işlemi çalıştırmanıza, izinleri yönetmenize, veritabanlarını denetlemeye ve diğer eylemleri gerçekleştirmenize olanak sağlar.

İlkeniz, REST API giriş talepleri gönderebilir. REST API, ilkenizde daha sonra kullanabileceğiniz çıkış taleplerini da döndürebilir veya bir hata iletisi oluşturabilir. Aşağıdaki yollarla, yeniden kullanılabilen hizmetlerle tümleştirmeyi tasarlayabilirsiniz:

- **Doğrulama teknik profili** -bir doğrulama teknik profili, restsize hizmetini çağırır. Doğrulama teknik profili, Kullanıcı yolculuğu devam etmeden önce Kullanıcı tarafından belirtilen verileri doğrular. Doğrulama teknik profiliyle birlikte, otomatik olarak onaylanan bir sayfada bir hata iletisi görüntülenir ve çıkış taleplerinde döndürülür.
- **Talep değişimi** -bir düzenleme adımı aracılığıyla Restity hizmetine bir çağrı yapılır. Bu senaryoda, hata iletisini işlemek için Kullanıcı arabirimi yoktur. REST API bir hata döndürürse, Kullanıcı, hata iletisiyle bağlı olan taraf uygulamasına yeniden yönlendirilir.

## <a name="protocol"></a>Protocol

**Protokol** öğesinin `Proprietary` **Name** özniteliğinin olarak ayarlanması gerekir. **Handler** özniteliği, Azure AD B2C tarafından kullanılan protokol işleyici derlemesinin tam adını içermelidir: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Aşağıdaki örnek, bir Restsize teknik profil göstermektedir:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Giriş talepleri

**Inputclaim** öğesi REST API göndermek için talepler listesi içerir. Ayrıca, talebin adını REST API tanımlanan adla eşleyebilirsiniz. Aşağıdaki örnekte, ilkeniz ve REST API arasındaki eşleme gösterilmektedir. REST API **,** **Soyadı** , **Soyadı olarak gönderilirken** **adı**olarak gönderilir. **E-posta** talebi olduğu gibi ayarlanır.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

**Inputclaimstransformations** öğesi, giriş taleplerini değiştirmek veya REST API göndermeden önce yenilerini oluşturmak Için kullanılan **inputclaimstransreference** öğelerinin bir koleksiyonunu içerebilir.

## <a name="output-claims"></a>Çıkış talepleri

**Outputclaim** öğesi, REST API tarafından döndürülen taleplerin bir listesini içerir. İlkenizde tanımlanan talebin adını REST API tanımlı adla eşlemeniz gerekebilir. `DefaultValue` Özniteliği ayarladığınız sürece, REST API kimlik sağlayıcısı tarafından döndürülmeyen talepleri de ekleyebilirsiniz.

**Outputclaimstransformations** öğesi, çıkış taleplerini değiştirmek veya yenilerini oluşturmak için kullanılan bir **outputclaimstransreference** öğeleri koleksiyonu içerebilir.

Aşağıdaki örnek, REST API tarafından döndürülen talebi gösterir:

- **Loyaltynumber** talep adıyla eşlenen **membershipID** talebi.

Teknik profil, kimlik sağlayıcısı tarafından döndürülmeyen talepler de döndürür:

- Varsayılan değeri olarak `true`ayarlanmış **Loyaltynumberisnew** talebi.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Meta Veriler

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| serviceUrl | Evet | REST API uç noktasının URL 'SI. |
| AuthenticationType | Evet | Yeniden izlenen talep sağlayıcısı tarafından gerçekleştirilen kimlik doğrulaması türü. Olası değerler: `None`, `Basic`, veya `ClientCertificate`. `None` Değer REST API anonim olmadığını gösterir. `Basic` Değer, REST API http temel kimlik doğrulamasıyla güvenli hale getirildiğini gösterir. Yalnızca doğrulanmış kullanıcılar, Azure AD B2C dahil, API 'nize erişebilir. `ClientCertificate` (Önerilen) değeri, REST API istemci sertifikası kimlik doğrulaması kullanarak erişimi kısıtladığını gösterir. Yalnızca Azure AD B2C gibi uygun sertifikalara sahip hizmetler hizmetinize erişebilir. |
| SendClaimsIn | Hayır | Giriş taleplerinin, Restity talep sağlayıcısına nasıl gönderileceğini belirtir. Olası değerler: `Body` (varsayılan), `Form`, `Header`, veya `QueryString`. `Body` Değer, JSON biçiminde istek gövdesinde gönderilen giriş talebinde bulunur. `Form` Değer, istek gövdesinde ve ' & ' ayrılmış anahtar değeri biçiminde gönderilen giriş talebinde bulunur. `Header` Değer, istek üstbilgisinde gönderilen giriş talebinde bulunur. `QueryString` Değer, istek sorgu dizesinde gönderilen giriş talebinde bulunur. |
| ClaimsFormat | Hayır | Çıkış taleplerinin biçimini belirtir. Olası değerler: `Body` (varsayılan), `Form`, `Header`, veya `QueryString`. `Body` Değer, JSON biçiminde istek gövdesinde gönderilen çıkış talebinde bulunur. `Form` Değer, istek gövdesinde ve ' & ' ayrılmış anahtar değeri biçiminde gönderilen çıkış talebinde bulunur. `Header` Değer, istek üstbilgisinde gönderilen çıkış talebinde bulunur. `QueryString` Değer, istek sorgu dizesinde gönderilen çıkış talebinde bulunur. |
| DebugMode | Hayır | Teknik profili hata ayıklama modunda çalıştırır. Hata ayıklama modunda REST API daha fazla bilgi döndürebilir. Hata iletisi döndüren bölümüne bakın. |

## <a name="cryptographic-keys"></a>Şifreleme anahtarları

Kimlik doğrulaması türü olarak `None`ayarlandıysa, **cryptographickeys** öğesi kullanılmaz.

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Kimlik doğrulaması türü olarak `Basic`ayarlandıysa, **cryptographickeys** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Evet | Kimlik doğrulaması için kullanılan Kullanıcı adı. |
| BasicAuthenticationPassword | Evet | Kimlik doğrulaması için kullanılan parola. |

Aşağıdaki örnek, temel kimlik doğrulaması ile bir teknik profil gösterir:

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Kimlik doğrulaması türü olarak `ClientCertificate`ayarlandıysa, **cryptographickeys** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ClientCertificate | Evet | Kimlik doğrulaması için kullanılacak x509 sertifikası (RSA anahtar kümesi). |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>Hata iletisi döndürülüyor

REST API, ' Kullanıcı CRM sisteminde bulunamadı ' gibi bir hata iletisi döndürmesi gerekebilir. Bir hata oluştuğunda, REST API aşağıdaki özniteliklere sahip bir HTTP 409 hata iletisi (çakışma yanıtı durum kodu) döndürmelidir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| version | Evet | 1.0.0 |
| status | Evet | 409 |
| code | Hayır | Etkin olduğunda `DebugMode` görünen, RESTAN uç nokta sağlayıcısından bir hata kodu. |
| requestId | Hayır | Etkinleştirildiğinde görünen `DebugMode` , restlen uç nokta sağlayıcısından gelen istek tanımlayıcısı. |
| userMessage | Evet | Kullanıcıya gösterilen bir hata iletisi. |
| developerMessage | Hayır | Sorunun ayrıntılı açıklaması ve nasıl düzeltileceğini, etkinleştirildiğinde görüntülenir `DebugMode` . |
| daha fazla bilgi | Hayır | Etkinleştirildiğinde görüntülenen `DebugMode` ek bilgilere işaret eden bir URI. |

Aşağıdaki örnek, JSON içinde biçimlendirilen bir hata iletisi döndüren REST API gösterir:

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

Aşağıdaki örnekte bir hata iletisi C# döndüren bir sınıf gösterilmektedir:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="examples"></a>Örnekler:
- [Kullanıcı girişinin doğrulanması sırasında Azure AD B2C Kullanıcı yolculuğunda REST API talep alışverişlerinde tümleştirin](active-directory-b2c-custom-rest-api-netfw.md)
- [HTTP temel kimlik doğrulaması kullanarak yeniden takip eden hizmetlerinizin güvenliğini sağlama](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
- [İstemci sertifikalarını kullanarak yeniden takip eden hizmetinizi güvenli hale getirin](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
- [İzlenecek yol: Kullanıcı girişinde Azure AD B2C Kullanıcı yolculuğunda REST API talep alışverişlerinde tümleştirme yapın](active-directory-b2c-rest-api-validation-custom.md)

 














