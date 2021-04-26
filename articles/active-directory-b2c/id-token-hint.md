---
title: Özel ilkede bir KIMLIK belirteci ipucu teknik profili tanımlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C bir özel ilkede bir KIMLIK belirteci ipucu teknik profili tanımlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d77e145cabcef2931d5fe6e76599da7931e576e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97669168"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C özel ilkesinde bir KIMLIK belirteci ipucu teknik profili tanımlama

Azure AD B2C, bağlı olan taraf uygulamalarının OAuth2 yetkilendirme isteğinin bir parçası olarak bir gelen JWT göndermesini sağlar. JWT belirteci bir bağlı olan taraf uygulaması veya bir kimlik sağlayıcısı tarafından verilebilir ve Kullanıcı veya yetkilendirme isteği hakkında bir ipucu geçirebilir. Azure AD B2C imza, verenin adı ve belirteç kitini doğrular ve gelen belirteçten talebi ayıklar.

## <a name="use-cases"></a>Uygulama alanları

Bu çözümü, tek bir JWT belirtecinde kapsüllenmiş Azure AD B2C veri göndermek için kullanabilirsiniz. Sistem yöneticinizin kullanıcılara imzalı bir davet gönderebildiği [e-posta daveti çözümüne kaydolma çözümü](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md), id_token_hint tabanlıdır. Yalnızca davet e-postasına erişimi olan kullanıcılar hesabı dizinde oluşturabilir.

## <a name="token-signing-approach"></a>Belirteç imzalama yaklaşımı

İd_token_hint, belirteç veren (bağlı olan taraf uygulaması veya bir kimlik sağlayıcısı) belirteci oluşturur ve sonra belirteci güvenilir bir kaynaktan geldiğini kanıtlamak için bir imzalama anahtarı kullanarak imzalar. İmzalama anahtarı simetrik veya asimetrik olabilir. Simetrik şifreleme veya özel anahtar şifrelemesi, imzayı imzalamak ve doğrulamak için paylaşılan bir gizli dizi kullanır. Asimetrik şifreleme veya ortak anahtar şifrelemesi, hem özel anahtar hem de ortak anahtar kullanan bir şifreleme sistemidir. Özel anahtar yalnızca belirteç veren tarafından bilinir ve belirteci imzalamak için kullanılır. Ortak anahtar, belirtecin imzasını doğrulamak için Azure AD B2C ilkesiyle paylaşılır.

## <a name="token-format"></a>Belirteç biçimi

İd_token_hint geçerli bir JWT belirteci olmalıdır. Aşağıdaki tabloda zorunlu olan talepler listelenmektedir. Ek talepler isteğe bağlıdır.

| Name | İste | Örnek değer | Description |
| ---- | ----- | ------------- | ----------- |
| Hedef kitle | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | Belirtecin amaçlanan alıcısını tanımlar. Hedef kitle, belirteç veren tarafından tanımlanan rastgele bir dizedir. Azure AD B2C, bu değeri doğrular ve eşleşmezse belirteci reddeder.  |
| Veren | `iss` |`https://localhost` | Güvenlik belirteci hizmetini (belirteç veren) tanımlar. Veren, belirteç veren tarafından tanımlanan rastgele bir URI 'dir. Azure AD B2C, bu değeri doğrular ve eşleşmezse belirteci reddeder.  |
| Sona erme saati | `exp` | `1600087315` | Belirtecin, dönem zamanında temsil edilen geçersiz hale geldiği zaman. Azure AD B2C, bu değeri doğrular ve belirtecin geçerliliği dolmuşsa belirteci reddeder.|
| Önce değil | `nbf` | `1599482515` | Belirtecin geçerli hale geldiği zaman, dönem süresi cinsinden temsil edilir. Bu zaman, genellikle belirtecin verildiği zaman ile aynıdır. Azure AD B2C bu değeri doğrular ve belirteç ömrü geçerli değilse belirteci reddeder. |

 Aşağıdaki belirteç geçerli bir KIMLIK belirteci örneğidir:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>Protokol

**Protokol** öğesinin **Name** özniteliğinin olarak ayarlanması gerekir `None` . Örneğin, **IdTokenHint_ExtractClaims** teknik profilinin Protokolü `None` :

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

Teknik profil, türü olan bir düzenleme adımından çağırılır `GetClaims` .

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>Çıkış talepleri

**Outputclaim** Öğesı, JWT belirtecinden Ayıklanacak taleplerin bir listesini içerir. İlkenizde tanımlanan talebin adını JWT belirtecinde tanımlanan adla eşlemeniz gerekebilir. Özniteliği ayarladığınız sürece JWT belirteci tarafından döndürülmeyen talepleri de ekleyebilirsiniz `DefaultValue` .

## <a name="metadata"></a>Meta veri

Simetrik anahtar kullanılırken aşağıdaki meta veriler geçerlidir. 

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| yayınlayan | Yes | Güvenlik belirteci hizmetini (belirteç veren) tanımlar. Bu değer `iss` , JWT belirteci talep kapsamındaki talebe özdeş olmalıdır. | 
| Idtokenaudience | Yes | Belirtecin amaçlanan alıcısını tanımlar. `aud`JWT belirteci talep kapsamındaki talebe özdeş olmalıdır. | 

Asimetrik anahtar kullanılırken aşağıdaki meta veriler geçerlidir. 

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| VERIYI| Yes | Bir OpenID iyi bilinen yapılandırma uç noktası olarak da bilinen, belirteç verenin yapılandırma belgesine işaret eden bir URL.   |
| yayınlayan | No | Güvenlik belirteci hizmetini (belirteç veren) tanımlar. Bu değer, meta verilerde yapılandırılan değerin üzerine yazmak için kullanılabilir ve `iss` JWT belirteci talep kapsamındaki talebe özdeş olmalıdır. |  
| Idtokenaudience | No | Belirtecin amaçlanan alıcısını tanımlar. `aud`JWT belirteci talep kapsamındaki talebe özdeş olmalıdır. |  

## <a name="cryptographic-keys"></a>Şifreleme anahtarları

Simetrik anahtar kullanırken, **Cryptographickeys** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| client_secret | Yes | JWT belirteci imzasını doğrulamak için kullanılan şifreleme anahtarı.|


## <a name="how-to-guide"></a>Nasıl yapılır kılavuzu

### <a name="issue-a-token-with-symmetric-keys"></a>Simetrik Anahtarlarla belirteç verme

#### <a name="step-1-create-a-shared-key"></a>Adım 1. Paylaşılan anahtar oluştur 

Belirteci imzalamak için kullanılabilecek bir anahtar oluşturun. Örneğin, bir anahtar oluşturmak için aşağıdaki PowerShell kodunu kullanın.

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

Bu kod, gibi bir gizli dize oluşturur `VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=` .

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>Adım 2. İmzalama anahtarını Azure AD B2C ekleyin

Belirteç Verenin kullandığı anahtarın Azure AD B2C ilke anahtarlarınızın içinde oluşturulması gerekir.  

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. Genel Bakış sayfasında, **ilkeler** altında **kimlik deneyimi çerçevesi**' ni seçin.
1. **Ilke anahtarlarını** seçin 
1. **El ile**' yi seçin.
1. **Ad** için öğesini kullanın `IdTokenHintKey` .  
   Ön ek `B2C_1A_` otomatik olarak eklenebilir.
1. **Gizli** kutusunda, daha önce oluşturduğunuz oturum açma anahtarını girin.
1. **Anahtar kullanımı** için **şifreleme** kullanın.
1. **Oluştur**’u seçin.
1. Anahtarı oluşturduğunuzdan emin olun `B2C_1A_IdTokenHintKey` .


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>3. Adım KIMLIK belirteci ipucu teknik profilini ekleyin

Aşağıdaki teknik profil belirteci doğrular ve talepleri ayıklar. 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>4. Adım: İlkenizi hazırlama

[Ilkenizi yapılandırın](#configure-your-policy) adımını doldurun.

#### <a name="step-5-prepare-the-code"></a>5. Adım. Kodu hazırlama  

[GitHub örneği](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key) , simetrik anahtar kullanılarak IMZALANMıŞ bir kimlik belirteci üreten bir ASP.NET Web uygulaması ve konsol uygulamasıdır. 


### <a name="issue-a-token-with-asymmetric-keys"></a>Asimetrik Anahtarlarla belirteç verme

Asimetrik anahtarla, belirteç RSA sertifikaları kullanılarak imzalanır. Bu uygulama, KIMLIK belirtecinin imzasını doğrulamak için Azure AD B2C tarafından kullanılan bir açık KIMLIK Connect meta veri uç noktası ve JSON Web anahtarları (JWKs) uç noktasını barındırır.

Belirteç Verenin aşağıdaki uç noktaları sağlaması gerekir:

* `/.well-known/openid-configuration` -Belirteç Verenin adı ve JWK uç noktasının bağlantısı gibi ilgili bilgileri içeren iyi bilinen bir yapılandırma uç noktası. 
* `/.well-known/keys` -JSON Web anahtarı (JWK) bitiş noktası anahtarı imzalamak için kullanılan ortak anahtara sahiptir (sertifikanın özel anahtar bölümüyle birlikte).

Bkz. [Tokenmetadatacontroller. cs](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) .NET MVC denetleyici örneği.

#### <a name="step-1-prepare-a-self-signed-certificate"></a>Adım 1. Otomatik olarak imzalanan sertifika hazırlama

Henüz bir sertifikanız yoksa, bu nasıl yapılır kılavuzunda otomatik olarak imzalanan bir sertifika kullanabilirsiniz. Windows 'da, bir sertifika oluşturmak için PowerShell 'in [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet 'ini kullanabilirsiniz.

Otomatik olarak imzalanan bir sertifika oluşturmak için bu PowerShell komutunu çalıştırın. `-Subject`Bağımsız değişkeni, uygulamanız için uygun şekilde değiştirin ve kiracı adı Azure AD B2C. Ayrıca, `-NotAfter` sertifika için farklı bir süre sonu belirtmek üzere tarihi de ayarlayabilirsiniz.

```PowerShell
New-SelfSignedCertificate `
    -KeyExportPolicy Exportable `
    -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -KeyUsage DigitalSignature `
    -NotAfter (Get-Date).AddMonths(12) `
    -CertStoreLocation "Cert:\CurrentUser\My"
```


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>Adım 2. KIMLIK belirteci ipucu teknik profilini ekleyin 

Aşağıdaki teknik profil belirteci doğrular ve talepleri ayıklar. Meta veri URI 'sini, belirteç verenin iyi bilinen yapılandırma uç noktası ile değiştirin.  

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <Item Key="IdTokenAudience">your_optional_audience</Item> -->
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>3. Adım İlkenizi hazırlama

[Ilkenizi yapılandırın](#configure-your-policy) adımını doldurun.

#### <a name="step-4-prepare-the-code"></a>4. Adım: Kodu hazırlama 

Bu [GitHub örnek](https://github.com/azure-ad-b2c/id-token-builder) ASP.NET Web UYGULAMASı, kimlik belirteçleri oluşturur ve Azure AD B2C ' id_token_hint "parametresini kullanmak için gereken meta veri uç noktalarını barındırır.


### <a name="configure-your-policy"></a>İlkenizi yapılandırma

Hem simetrik hem de asimetrik yaklaşımlar için `id_token_hint` Teknik profil, türü olan bir düzenleme adımından çağrılır `GetClaims` ve bağlı olan taraf ilkesinin giriş taleplerini belirtmesi gerekir.

1. Uzantı ilkenize IdTokenHint_ExtractClaims teknik profilini ekleyin.
1. Aşağıdaki düzenleme adımını Kullanıcı yolculuğuna ilk öğe olarak ekleyin.  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. Bağlı olan taraf ilkenizde, IdTokenHint_ExtractClaims teknik profilinde yapılandırdığınız giriş taleplerini yineleyin. Örnek:
    ```xml
   <RelyingParty>
     <DefaultUserJourney ReferenceId="SignUp" />
     <TechnicalProfile Id="PolicyProfile">
       <DisplayName>PolicyProfile</DisplayName>
       <Protocol Name="OpenIdConnect" />
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
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

İş gereksinimlerinize bağlı olarak, belirteç doğrulamaları eklemeniz gerekebilir, örneğin e-posta adresinin biçimini kontrol edebilirsiniz. Bunu yapmak için, bir [talep dönüştürme teknik profili](claims-transformation-technical-profile.md)çağıran düzenleme adımları ekleyin. Ayrıca bir hata iletisi sunmak için [kendi kendine onaylanan bir teknik profil](self-asserted-technical-profile.md) ekleyin. 

### <a name="create-and-sign-a-token"></a>Belirteç oluşturma ve imzalama

GitHub örnekleri, daha sonra sorgu dizesi parametresi olarak gönderilen bir JWT sorunu için bu tür bir belirteç oluşturmayı gösterir `id_token_hint` . İd_token_hint parametresine sahip yetkilendirme isteğine bir örnek aşağıda verilmiştir
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>Sonraki adımlar

- Azure AD B2C Community GitHub deposunda, [davet e-postası çözümüne kaydolma](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md) ' yı denetleyin.
