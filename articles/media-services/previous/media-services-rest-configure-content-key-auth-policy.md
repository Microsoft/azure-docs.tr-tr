---
title: REST-Azure ile içerik anahtarı yetkilendirme ilkesi yapılandırma | Microsoft Docs
description: Media Services REST API kullanarak bir içerik anahtarı için yetkilendirme ilkesi yapılandırmayı öğrenin.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 7af5f9e2-8ed8-43f2-843b-580ce8759fd4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 66649eb6109339cd7723ab4a7e3a12c5c322973d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017299"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Dinamik şifreleme: içerik anahtarı yetkilendirme ilkesini yapılandırma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Genel Bakış
 Azure Media Services, içeriğinizi 128 bit şifreleme anahtarları ve PlayReady veya Widevine dijital hak yönetimi (DRM) kullanarak Gelişmiş Şifreleme Standardı (AES) ile şifreli (dinamik) teslim etmek için kullanabilirsiniz. Media Services Ayrıca yetkili istemcilere anahtar ve PlayReady/Widevine lisansları teslim etmek için bir hizmet sağlar.

Bir varlığı şifrelemek Media Services isterseniz, bir şifreleme anahtarını (CommonEncryption veya EnvelopeEncryption) varlıkla ilişkilendirmeniz gerekir. Daha fazla bilgi için bkz. [rest ile içerik anahtarları oluşturma](media-services-rest-create-contentkey.md). Ayrıca, anahtar için yetkilendirme ilkeleri yapılandırmanız gerekir (Bu makalede açıklandığı gibi).

Bir akış bir oyuncu tarafından istendiğinde, Media Services AES veya PlayReady şifrelemesini kullanarak içeriğinizi dinamik olarak şifrelemek için belirtilen anahtarı kullanır. Oynatıcı, akışın şifresini çözmek için anahtar teslim hizmetinden anahtarı ister. Kullanıcının anahtarı almak için yetkilendirilip yetkilendirilmediğini belirleme hizmeti, anahtar için belirttiğiniz yetkilendirme ilkelerini değerlendirir.

Media Services, anahtar isteğinde bulunan kullanıcıların kimlik doğrulamasını yapmanın birden çok yöntemini destekler. İçerik anahtarı yetkilendirme ilkesi, açık veya belirteç kısıtlaması kullanarak bir veya daha fazla yetkilendirme kısıtlamalarına sahip olabilir. Belirteç kısıtlamalı ilkenin beraberinde bir güvenlik belirteci hizmeti (STS) tarafından verilmiş bir belirteç bulunmalıdır. Media Services basit Web belirteci ([SWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2)) ve JSON Web token (JWT) biçimlerindeki belirteçleri destekler.

Media Services STS sağlamıyor. Belirteçleri vermek için özel bir STS oluşturabilir veya Azure Active Directory (Azure AD) kullanabilirsiniz. STS, belirtilen anahtarla imzalanmış bir belirteç oluşturacak ve belirteç kısıtlama yapılandırmasında (Bu makalede açıklandığı gibi) belirttiğiniz talepler verecek şekilde yapılandırılmalıdır. Belirteç geçerliyse ve belirteçteki talepler içerik anahtarı için yapılandırılananlarla eşleşiyorsa, Media Services anahtar teslim hizmeti, istemciye şifreleme anahtarını döndürür.

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:
- [JWT belirteci kimlik doğrulaması](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Azure Media Services, MVC tabanlı bir uygulamayı Azure Active Directory ile tümleştirin ve JWT taleplerine göre içerik anahtarı teslimini kısıtlayın](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Bazı konular geçerlidir
* Dinamik paketleme ve dinamik şifrelemeyi kullanmak için, içeriğinizi akışa almak istediğiniz akış uç noktasının "çalışıyor" durumunda olduğundan emin olun.
* Varlığınız bir uyarlamalı bit hızı MP4 'leri veya Uyarlamalı bit hızı Kesintisiz Akış dosyaları içermelidir. Daha fazla bilgi için bkz. [varlık kodlama](media-services-encode-asset.md).
* Varlıklarınızı AssetCreationOptions. Storageşifrelenen seçeneğini kullanarak karşıya yükleyin ve kodlayın.
* Aynı ilke yapılandırmasını gerektiren birden çok içerik anahtarınız varsa, tek bir yetkilendirme ilkesi oluşturmanızı ve birden çok içerik anahtarı ile yeniden kullanmanızı öneririz.
* Anahtar teslim hizmeti, ContentKeyAuthorizationPolicy ve ilgili nesneleri (ilke seçenekleri ve kısıtlamalar) 15 dakika boyunca önbelleğe alır. ContentKeyAuthorizationPolicy oluşturup bir belirteç kısıtlaması kullanmayı belirtebilir, test edebilir ve ardından ilkeyi açık kısıtlama olarak güncelleştirebilirsiniz. Bu işlem, ilkenin, ilkenin açık sürümüne geçiş yapmadan yaklaşık 15 dakika sürer.
* Varlığınızın teslim ilkesini ekler veya güncelleştirirseniz, varsa mevcut bulucuyu silip yeni bir bulucu oluşturmanız gerekir.
* Şu anda aşamalı İndirmeleri şifreleyemez.
* Media Services akış uç noktası, ön kontrol yanıtında CORS erişim-denetimi-Izin-kaynak üst bilgisinin değerini joker karakter " \* ." olarak ayarlar. Bu değer, Azure Media Player, Roku ve JWPlayer gibi birçok oyuncunun yanı sıra diğerleri için de geçerlidir. Ancak, kimlik bilgileri "içerme" olarak ayarlandığı için dash.js kullanan bazı oyuncular işe yaramazsa, dash.js XMLHttpRequest, "" joker karakterine \* erişim-Control-Allow-Origin değeri olarak izin vermez. dash.js, bu sınırlamaya yönelik bir geçici çözüm olarak, istemcinizi tek bir etki alanından barındırdıysanız Media Services, bu etki alanını ön kontrol yanıt üstbilgisinde belirtebilir. Yardım için Azure portal aracılığıyla bir destek bileti açın.

## <a name="aes-128-dynamic-encryption"></a>AES-128 dinamik şifreleme
> [!NOTE]
> Media Services REST API çalışırken aşağıdaki noktalar geçerlidir.
> 
> Media Services içindeki varlıklara eriştiğinizde, HTTP isteklerinizin belirli üstbilgi alanlarını ve değerlerini ayarlamanız gerekir. Daha fazla bilgi için bkz. [Media Services REST API Geliştirme Için kurulum](media-services-rest-how-to-use.md).
> 
> 
> 

### <a name="open-restriction"></a>Açık kısıtlama
Açık kısıtlama, sistemin anahtarı bir anahtar isteği yapan herkese teslim eder anlamına gelir. Bu kısıtlama, test amacıyla yararlı olabilir.

Aşağıdaki örnek, açık bir yetkilendirme ilkesi oluşturur ve onu içerik anahtarına ekler.

#### <a name="create-contentkeyauthorizationpolicies"></a><a id="ContentKeyAuthorizationPolicies"></a>ContentKeyAuthorizationPolicies oluşturma
İstek:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 36

{"Name":"Open Authorization Policy"}
```

Yanıt:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 211
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Adb4593da-f4d1-4cc5-a92a-d20eacbabee4')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
request-id: aabfa731-e884-4bf3-8314-492b04747ac4
x-ms-request-id: aabfa731-e884-4bf3-8314-492b04747ac4
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 08:25:56 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:db4593da-f4d1-4cc5-a92a-d20eacbabee4","Name":"Open Authorization Policy"}
```

#### <a name="create-contentkeyauthorizationpolicyoptions"></a><a id="ContentKeyAuthorizationPolicyOptions"></a>ContentKeyAuthorizationPolicyOptions oluştur
İstek:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 168

{"Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}
```

Yanıt:    

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 349
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
x-ms-request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 08:56:40 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:57829b17-1101-4797-919b-f816f4a007b7","Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a><a id="LinkContentKeyAuthorizationPoliciesWithOptions"></a>ContentKeyAuthorizationPolicies seçeneklerini seçeneklerle bağlayın
İstek:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3A0baa438b-8ac2-4c40-a53c-4d4722b78715')/$links/Options HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 9847f705-f2ca-4e95-a478-8f823dbbaa29
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 154

{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')"}
```

Yanıt:

```output
HTTP/1.1 204 No Content
```

#### <a name="add-an-authorization-policy-to-the-content-key"></a><a id="AddAuthorizationPolicyToKey"></a>İçerik anahtarına bir yetkilendirme ilkesi ekleyin
İstek:

```console
PUT https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A2e6d36a7-a17c-4e9a-830d-eca23ad1a6f9') HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: e613efff-cb6a-41b4-984a-f4f8fb6e76a4
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 78

{"AuthorizationPolicyId":"nb:ckpid:UUID:c06cebb8-c4f0-4d1a-ba00-3273fb2bc3ad"}
```

Yanıt:

```output
HTTP/1.1 204 No Content
```

### <a name="token-restriction"></a>Belirteç kısıtlaması
Bu bölümde, içerik anahtarı yetkilendirme ilkesi oluşturma ve içerik anahtarıyla ilişkilendirme açıklanır. Yetkilendirme ilkesi, kullanıcının anahtarı alma yetkisine sahip olup olmadığını belirlemek için hangi yetkilendirme gereksinimlerinin karşılanacağını açıklar. Örneğin, doğrulama anahtarı listesi belirtecin imzalandığı anahtarı içeriyor mu?

Belirteç kısıtlama seçeneğini yapılandırmak için, belirtecin yetkilendirme gereksinimlerini betimleyen bir XML kullanmanız gerekir. Belirteç kısıtlama yapılandırması XML 'si aşağıdaki XML şemasıyla uyumlu olmalıdır:


#### <a name="token-restriction-schema"></a><a id="schema"></a>Belirteç kısıtlama şeması
```xml
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

Belirteç kısıtlı ilkesini yapılandırırken, birincil doğrulama anahtarını, verenin ve hedef kitle parametrelerini belirtmeniz gerekir. Birincil doğrulama anahtarı, belirtecin imzalandığı anahtarı içerir. Veren, belirteci veren STS 'dir. Hedef kitle (bazen kapsam olarak adlandırılır) belirtecin amacını veya belirtecin erişim yetkisi aldığı kaynağı açıklar. Media Services anahtar teslim hizmeti, belirteçteki bu değerlerin şablondaki değerlerle eşleştiğini doğrular.

Aşağıdaki örnek, bir belirteç kısıtlaması ile bir yetkilendirme ilkesi oluşturur. Bu örnekte, istemci, imzalama anahtarını (doğrulamaları ıationkey), bir belirteç vereni ve gerekli talepleri içeren bir belirteç sunmalıdır.

### <a name="create-contentkeyauthorizationpolicies"></a>ContentKeyAuthorizationPolicies oluşturma
"[ContentKeyAuthorizationPolicies oluştur](#ContentKeyAuthorizationPolicies)" bölümünde gösterildiği gibi bir belirteç kısıtlama ilkesi oluşturun.

### <a name="create-contentkeyauthorizationpolicyoptions"></a>ContentKeyAuthorizationPolicyOptions oluştur
İstek:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 1079

{"Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

Yanıt:    

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1260
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae1ef6145-46e8-4ee6-9756-b1cf96328c23')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
x-ms-request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:10:37 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e1ef6145-46e8-4ee6-9756-b1cf96328c23","Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>ContentKeyAuthorizationPolicies seçeneklerini seçeneklerle bağlayın
ContentKeyAuthorizationPolicies ' ı, "[Contentkeyauthorizationpolicies oluşturma](#ContentKeyAuthorizationPolicies)" bölümünde gösterildiği gibi seçeneklerle bağlayın.

#### <a name="add-an-authorization-policy-to-the-content-key"></a>İçerik anahtarına bir yetkilendirme ilkesi ekleyin
"[İçerik anahtarına yetkilendirme Ilkesi Ekle](#AddAuthorizationPolicyToKey)" bölümünde gösterildiği gibi, ' nin Contentkey 'e authorizationpolicy ekleyin.

## <a name="playready-dynamic-encryption"></a>PlayReady dinamik şifrelemesi
Bir kullanıcı tarafından korunan içeriği yürütmeye çalıştığında, PlayReady DRM çalışma zamanının uygulanmasını istediğiniz hakları ve kısıtlamaları yapılandırmak için Media Services kullanabilirsiniz. 

İçeriğinizi PlayReady ile koruduğunuzda, yetkilendirme ilkenizde belirtmeniz gereken işlemlerden biri [PlayReady lisans şablonunu](media-services-playready-license-template-overview.md)TANıMLAYAN bir XML dizesidir. 

### <a name="open-restriction"></a>Açık kısıtlama
Açık kısıtlama, sistemin anahtarı bir anahtar isteği yapan herkese teslim eder anlamına gelir. Bu kısıtlama, test amacıyla yararlı olabilir.

Aşağıdaki örnek, açık bir yetkilendirme ilkesi oluşturur ve onu içerik anahtarına ekler.

#### <a name="create-contentkeyauthorizationpolicies"></a><a id="ContentKeyAuthorizationPolicies2"></a>ContentKeyAuthorizationPolicies oluşturma
İstek:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 58

{"Name":"Deliver Common Content Key"}
```

Yanıt:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 233
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Acc3c64a8-e2fc-4e09-bf60-ac954251a387')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
x-ms-request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:26:00 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:cc3c64a8-e2fc-4e09-bf60-ac954251a387","Name":"Deliver Common Content Key"}
```


#### <a name="create-contentkeyauthorizationpolicyoptions"></a>ContentKeyAuthorizationPolicyOptions oluştur
İstek:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 593

{"Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}
```

Yanıt:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 774
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A1052308c-4df7-4fdb-8d21-4d2141fc2be0')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
x-ms-request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:23:24 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:1052308c-4df7-4fdb-8d21-4d2141fc2be0","Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>ContentKeyAuthorizationPolicies seçeneklerini seçeneklerle bağlayın
ContentKeyAuthorizationPolicies ' ı, "[Contentkeyauthorizationpolicies oluşturma](#ContentKeyAuthorizationPolicies)" bölümünde gösterildiği gibi seçeneklerle bağlayın.

#### <a name="add-an-authorization-policy-to-the-content-key"></a>İçerik anahtarına bir yetkilendirme ilkesi ekleyin
"[İçerik anahtarına yetkilendirme Ilkesi Ekle](#AddAuthorizationPolicyToKey)" bölümünde gösterildiği gibi, ' nin Contentkey 'e authorizationpolicy ekleyin.

### <a name="token-restriction"></a>Belirteç kısıtlaması
Belirteç kısıtlama seçeneğini yapılandırmak için, belirtecin yetkilendirme gereksinimlerini betimleyen bir XML kullanmanız gerekir. Belirteç kısıtlama yapılandırması XML, "[belirteç kısıtlama şeması](#schema)" bölümünde gösterilen xml şemasıyla uyumlu olmalıdır.

#### <a name="create-contentkeyauthorizationpolicies"></a>ContentKeyAuthorizationPolicies oluşturma
"[ContentKeyAuthorizationPolicies oluştur](#ContentKeyAuthorizationPolicies2)" bölümünde gösterildiği gibi contentkeyauthorizationpolicies oluşturma.

#### <a name="create-contentkeyauthorizationpolicyoptions"></a>ContentKeyAuthorizationPolicyOptions oluştur
İstek:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 1525

{"Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

Yanıt:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1706
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae42bbeae-de42-4077-90e9-a844f297ef70')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
request-id: ccf8a4ba-731e-4124-8192-079592c251cc
x-ms-request-id: ccf8a4ba-731e-4124-8192-079592c251cc
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:58:47 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e42bbeae-de42-4077-90e9-a844f297ef70","Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>ContentKeyAuthorizationPolicies seçeneklerini seçeneklerle bağlayın
ContentKeyAuthorizationPolicies ' ı, "[Contentkeyauthorizationpolicies oluşturma](#ContentKeyAuthorizationPolicies)" bölümünde gösterildiği gibi seçeneklerle bağlayın.

#### <a name="add-an-authorization-policy-to-the-content-key"></a>İçerik anahtarına bir yetkilendirme ilkesi ekleyin
"[İçerik anahtarına yetkilendirme Ilkesi Ekle](#AddAuthorizationPolicyToKey)" bölümünde gösterildiği gibi, ' nin Contentkey 'e authorizationpolicy ekleyin.

## <a name="types-used-when-you-define-contentkeyauthorizationpolicy"></a><a id="types"></a>ContentKeyAuthorizationPolicy tanımladığınızda kullanılan türler
### <a name="contentkeyrestrictiontype"></a><a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
```csharp
public enum ContentKeyRestrictionType
{
    Open = 0,
    TokenRestricted = 1, 
    IPRestricted = 2, // IP restriction on content key is not currently supported, reserved for future.
}
```


> [!NOTE]
> İçerik anahtarı yetkilendirme ilkelerinde IP kısıtlaması henüz hizmette yok.


### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType
```csharp
public enum ContentKeyDeliveryType
{
    None = 0,
    PlayReadyLicense = 1,
    BaselineHttp = 2,
    Widevine = 3
}
```

## <a name="additional-notes"></a>Ek notlar

* Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
Bir içerik anahtarının yetkilendirme ilkesini yapılandırdığınıza göre, bkz. [varlık teslim Ilkesini yapılandırma](media-services-rest-configure-asset-delivery-policy.md).
