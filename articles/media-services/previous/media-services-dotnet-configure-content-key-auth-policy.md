---
title: Media Services .NET SDK 'sını kullanarak bir içerik anahtarı yetkilendirme ilkesi yapılandırma | Microsoft Docs
description: Media Services .NET SDK kullanarak bir içerik anahtarı için yetkilendirme ilkesi yapılandırmayı öğrenin.
services: media-services
documentationcenter: ''
author: mingfeiy
manager: femila
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: a2d978a68f6f654e3bdeea07c931cd7103f5850c
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2019
ms.locfileid: "69015524"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Dinamik şifreleme: İçerik anahtarı yetkilendirme ilkesini yapılandırma
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Genel Bakış
 128-bit şifreleme anahtarları veya [PlayReady Digital Rights Management (DRM)](https://www.microsoft.com/playready/overview/)kullanarak GELIŞMIŞ ŞIFRELEME standardı (AES) Ile korunan MPEG-DASH, Kesintisiz Akış ve HTTP canlı akışı (HLS) akışları sunmak için Azure Media Services kullanabilirsiniz. Media Services ile Widevine DRM ile şifrelenmiş DASH akışları da sunabilirsiniz. PlayReady ve Widevine, ortak şifreleme (ISO/IEC 23001-7 CENC) belirtimi uyarınca şifrelenir.

Media Services Ayrıca, istemcilerin şifreli içeriği oynatmak için AES anahtarları veya PlayReady/Widevine lisansları alabileceği bir anahtar/lisans teslim hizmeti sağlar.

Bir varlığı şifrelemek Media Services isterseniz, bir şifreleme anahtarını (CommonEncryption veya EnvelopeEncryption) varlıkla ilişkilendirmeniz gerekir. Daha fazla bilgi için bkz. [.net Ile ContentKeys oluşturma](media-services-dotnet-create-contentkey.md). Ayrıca, anahtar için yetkilendirme ilkeleri yapılandırmanız gerekir (Bu makalede açıklandığı gibi).

Bir Player tarafından bir akış istendiğinde, Media Services,, AES veya DRM şifrelemesini kullanarak içeriğinizi dinamik olarak şifrelemek için belirtilen anahtarı kullanır. Oynatıcı, akışın şifresini çözmek için anahtar teslim hizmetinden anahtarı ister. Kullanıcının anahtarı almak için yetkilendirilip yetkilendirilmediğini belirleme hizmeti, anahtar için belirttiğiniz yetkilendirme ilkelerini değerlendirir.

Media Services, anahtar isteğinde bulunan kullanıcıların kimlik doğrulamasını yapmanın birden çok yöntemini destekler. İçerik anahtarı Yetkilendirme ilkesinde bir veya daha fazla yetkilendirme kısıtlaması olabilir. Seçenekler açık veya belirteç kısıtlamasıdır. Belirteç kısıtlamalı ilkenin beraberinde bir güvenlik belirteci hizmeti (STS) tarafından verilmiş bir belirteç bulunmalıdır. Media Services basit Web belirteci ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) biçimindeki belirteçleri ve JSON Web token ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) biçimini destekler.

Media Services STS sağlamıyor. Belirteçleri vermek için özel bir STS oluşturabilir veya Azure Access Control Service kullanabilirsiniz. STS, belirtilen anahtarla imzalanmış bir belirteç oluşturacak ve belirteç kısıtlama yapılandırmasında (Bu makalede açıklandığı gibi) belirttiğiniz talepler verecek şekilde yapılandırılmalıdır. Belirteç geçerliyse ve belirteçteki talepler içerik anahtarı için yapılandırılananlarla eşleşiyorsa, Media Services anahtar teslim hizmeti, istemciye şifreleme anahtarını döndürür.

Daha fazla bilgi için aşağıdaki makalelere bakın:

- [JWT belirteci kimlik doğrulaması](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Azure Media Services OWIN MVC tabanlı uygulama Azure Active Directory ve JWT taleplerine göre içerik anahtarı teslimini tümleştirin](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Bazı konular geçerlidir
* Media Services hesabınız oluşturulduğunda hesabınıza “Durdurulmuş” durumda bir varsayılan akış uç noktası eklenir. İçeriğinizi akışa başlamak ve dinamik paketleme ve dinamik şifreleme avantajlarından yararlanmak için, akış uç noktanızın "çalışıyor" durumunda olması gerekir. 
* Varlığınız bir uyarlamalı bit hızı MP4 'leri veya Uyarlamalı bit hızı Kesintisiz Akış dosyaları içermelidir. Daha fazla bilgi için bkz. [varlık kodlama](media-services-encode-asset.md).
* Varlıklarınızı AssetCreationOptions. Storageşifrelenen seçeneğini kullanarak karşıya yükleyin ve kodlayın.
* Aynı ilke yapılandırmasını gerektiren birden çok içerik anahtarınız varsa, tek bir yetkilendirme ilkesi oluşturmanızı ve birden çok içerik anahtarı ile yeniden kullanmanızı öneririz.
* Anahtar teslim hizmeti, ContentKeyAuthorizationPolicy ve ilgili nesneleri (ilke seçenekleri ve kısıtlamalar) 15 dakika boyunca önbelleğe alır. ContentKeyAuthorizationPolicy oluşturup bir belirteç kısıtlaması kullanmayı belirtebilir, test edebilir ve ardından ilkeyi açık kısıtlama olarak güncelleştirebilirsiniz. Bu işlem, ilkenin, ilkenin açık sürümüne geçiş yapmadan yaklaşık 15 dakika sürer.
* Varlığınızın teslim ilkesini ekler veya güncelleştirirseniz, varsa mevcut bulucuyu silip yeni bir bulucu oluşturmanız gerekir.
* Şu anda aşamalı İndirmeleri şifreleyemez.
* Media Services akış uç noktası, '\*' joker karakteri olarak ön kontrol yanıtında CORS ' Access-Control-Allow-Origin ' üst bilgisinin değerini ayarlar. Bu değer, Azure Media Player, Roku ve JWPlayer gibi birçok oyuncunun yanı sıra diğerleri için de geçerlidir. Bununla birlikte,, "içerme" olarak ayarlanmış kimlik bilgileri ile, bu arada, kendi çizgi js ' deki XMLHttpRequest, ' Access-Control-Allow-Origin '\*değeri olarak "" joker karakterine izin vermediğinden, WITH, Tek bir etki alanından istemcinizi barındırdıysanız, bu sınırlamaya yönelik bir geçici çözüm olarak, Media Services ön kontrol yanıt üstbilgisinde bu etki alanını belirtebilir. Yardım için Azure portal aracılığıyla bir destek bileti açın.

## <a name="aes-128-dynamic-encryption"></a>AES-128 dinamik şifreleme
### <a name="open-restriction"></a>Açık kısıtlama
Açık kısıtlama, sistemin anahtarı bir anahtar isteği yapan herkese teslim eder anlamına gelir. Bu kısıtlama, test amacıyla yararlı olabilir.

Aşağıdaki örnek, açık bir yetkilendirme ilkesi oluşturur ve onu içerik anahtarına ekler:
```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>Belirteç kısıtlaması
Bu bölümde, içerik anahtarı yetkilendirme ilkesi oluşturma ve içerik anahtarıyla ilişkilendirme açıklanır. Yetkilendirme ilkesi, kullanıcının anahtarı alma yetkisine sahip olup olmadığını belirlemek için hangi yetkilendirme gereksinimlerinin karşılanacağını açıklar. Örneğin, doğrulama anahtarı listesi belirtecin imzalandığı anahtarı içeriyor mu?

Belirteç kısıtlama seçeneğini yapılandırmak için, belirtecin yetkilendirme gereksinimlerini betimleyen bir XML kullanmanız gerekir. Belirteç kısıtlama yapılandırması XML 'si aşağıdaki XML şemasıyla uyumlu olmalıdır:
```csharp
#### Token restriction schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>
```
Belirteç kısıtlı ilkesini yapılandırırken, birincil doğrulama anahtarını, verenin ve hedef kitle parametrelerini belirtmeniz gerekir. Birincil doğrulama anahtarı belirteç birlikte imzalandığı anahtarını içerir. Veren, belirteci veren STS 'dir. Hedef kitle (bazen kapsam olarak adlandırılır) belirtecin amacını veya belirtecin erişim yetkisi aldığı kaynağı açıklar. Media Services anahtar dağıtımı hizmetiyle belirtecindeki bu değerleri şablon değerleri eşleştiğini doğrular.

.NET için Media Services SDK kullandığınızda, kısıtlama belirtecini oluşturmak için TokenRestrictionTemplate sınıfını kullanabilirsiniz.
Aşağıdaki örnek, bir belirteç kısıtlaması ile bir yetkilendirme ilkesi oluşturur. Bu örnekte, istemci, bir imzalama anahtarı (doğrulamaları Icationkey), bir belirteç veren ve gerekli talepler içeren bir belirteç sunmalıdır.
```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    }
```
#### <a name="test-token"></a>Test belirteci
Anahtar yetkilendirme ilkesi için kullanılan belirteç kısıtlamasına dayalı bir test belirteci almak için aşağıdakileri yapın:
```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();
```

## <a name="playready-dynamic-encryption"></a>PlayReady dinamik şifrelemesi
Bir kullanıcı tarafından korunan içeriği yürütmeye çalıştığında, PlayReady DRM çalışma zamanının uygulanmasını istediğiniz hakları ve kısıtlamaları yapılandırmak için Media Services kullanabilirsiniz. 

İçeriğinizi PlayReady ile koruduğunuzda, yetkilendirme ilkenizde belirtmeniz gereken işlemlerden biri [PlayReady lisans şablonunu](media-services-playready-license-template-overview.md)TANıMLAYAN bir XML dizesidir. .NET için Media Services SDK 'da, PlayReadyLicenseResponseTemplate ve PlayReadyLicenseTemplate sınıfları PlayReady lisans şablonunu tanımlamanıza yardımcı olur.

İçeriğinizi PlayReady ve Widevine ile şifrelemeyi öğrenmek için bkz. [PlayReady ve/veya Widevine dinamik ortak şifrelemeyi kullanma](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Açık kısıtlama
Açık kısıtlama, sistemin anahtarı bir anahtar isteği yapan herkese teslim eder anlamına gelir. Bu kısıtlama, test amacıyla yararlı olabilir.

Aşağıdaki örnek, açık bir yetkilendirme ilkesi oluşturur ve onu içerik anahtarına ekler:

```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {

        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }
```

### <a name="token-restriction"></a>Belirteç kısıtlaması
Belirteç kısıtlama seçeneğini yapılandırmak için, belirtecin yetkilendirme gereksinimlerini betimleyen bir XML kullanmanız gerekir. Belirteç kısıtlama yapılandırması XML, "belirteç kısıtlama şeması" bölümünde gösterilen XML şemasıyla uyumlu olmalıdır.

```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {

        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();


        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 

    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }
```

Anahtar yetkilendirme ilkesi için kullanılan belirteç kısıtlamasına dayalı bir test belirteci almak için, "[Test belirteci](#test-token)" bölümüne bakın. 

## <a id="types"></a>ContentKeyAuthorizationPolicy tanımladığınızda kullanılan türler
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a id="TokenType"></a>Belirteç

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
İçerik anahtarının yetkilendirme ilkesini yapılandırdığınıza göre, bkz. [varlık teslim Ilkesini yapılandırma](media-services-dotnet-configure-asset-delivery-policy.md).

