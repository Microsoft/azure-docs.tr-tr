---
title: Media Services REST API kullanarak varlık teslim ilkelerini yapılandırma | Microsoft Docs
description: Bu konu, Media Services REST API kullanılarak farklı varlık teslim ilkelerinin nasıl yapılandırılacağını gösterir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 65a7b9479dae1826f478ca5e038ef5feb1caefb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017333"
---
# <a name="configuring-asset-delivery-policies"></a>Varlık teslim ilkelerini yapılandırma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Dinamik olarak şifrelenen varlıklar teslim etmeyi planlıyorsanız, Media Services içerik teslim iş akışındaki adımlardan biri varlıklar için teslim ilkelerini yapılandırıyorsunuz. Varlık teslim ilkesi, varlığınızın nasıl sunulmasını istediğinizi Media Services söyler: varlığınızı dinamik olarak paketlemeniz gereken (örneğin, MPEG DASH, HLS, Kesintisiz Akış veya All), varlığınızı ve (zarf veya ortak şifreleme) nasıl (zarf veya ortak şifrelemeyi) bir şekilde şifrelemek isteyip istemediğiniz.

Bu konuda, varlık teslim ilkelerinin neden ve nasıl oluşturulacağı ve yapılandırılacağı açıklanmaktadır.

> [!NOTE]
> AMS hesabınız oluşturulduğunda hesabınıza **durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçerik akışını başlatmak ve dinamik paketleme ile dinamik şifrelemeden yararlanmak için içerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumda olması gerekir. 
>
> Ayrıca, dinamik paketleme ve dinamik şifrelemeyi kullanabilmeniz için varlığınız bir uyarlamalı bit hızı MP4 'leri veya Uyarlamalı bit hızı Kesintisiz Akış dosyaları içermelidir.

Aynı kıymete farklı ilkeler uygulayabilirsiniz. Örneğin, MPEG DASH ve HLS 'e Kesintisiz Akış ve AES zarf şifrelemeye PlayReady şifrelemesi uygulayabilirsiniz. Herhangi bir teslim ilkesinde tanımlanmayan tüm protokollerin (örneğin, protokol olarak yalnızca HLS‘yi belirten tek bir ilke ekliyorsunuz) akışla aktarılması engellenir. Bunun tek istisnası, hiçbir varlık teslim ilkesinin tanımlanmadığı durumdur. Bu halde tüm protokollere açık bir şekilde izin verilir.

Depolama ile şifrelenmiş bir varlık sağlamak istiyorsanız, varlığın teslim ilkesini yapılandırmanız gerekir. Varlığınızın akışı için, akış sunucusu depolama şifrelemesini kaldırır ve belirtilen teslim ilkesini kullanarak içeriğinizi akışa çıkarır. Örneğin, varlığınızı Gelişmiş Şifreleme Standardı (AES) zarf şifreleme anahtarıyla şifreli olarak sunmak için, ilke türünü **DynamicEnvelopeEncryption** olarak ayarlayın. Depolama şifrelemesini kaldırmak ve varlığı açık olarak akışa almak için, ilke türünü **Nodynamicencryption** olarak ayarlayın. Bu ilke türlerinin nasıl yapılandırılacağını gösteren örnekler aşağıda verilmiştir.

Varlık teslim ilkesini nasıl yapılandırdığınıza bağlı olarak, aşağıdaki akış protokollerini dinamik olarak paketleyebilir, dinamik olarak şifreleyebilir ve akışlara alabilirsiniz: Kesintisiz Akış, HLS, MPEG DASH akışları.

Aşağıdaki listede kesintisiz, HLS, TIRE akışı için kullandığınız biçimler gösterilmektedir.

Kesintisiz Akış:

{akış uç noktası adı-media services hesabı adı}.streaming.mediaservices.windows.net/{konum kimliği}/{dosya adı}.ism/Manifest

HLS

{akış uç noktası adı-media services hesabı adı}.streaming.mediaservices.windows.net/{konum kimliği}/{dosya adı}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{akış uç noktası adı-media services hesabı adı}.streaming.mediaservices.windows.net/{konum kimliği}/{dosya adı}.ism/Manifest(format=mpd-time-csf)


Varlık yayımlama ve akış URL'si oluşturma yönergeleri için bkz. [Akış URL'si oluşturma](media-services-deliver-streaming-content.md).

## <a name="considerations"></a>Dikkat edilmesi gerekenler
* Bu varlık için bir OnDemand (akış) Bulucu varken bir varlıkla ilişkili bir AssetDeliveryPolicy 'yi silemezsiniz. İlkeyi silmeden önce ilkeyi varlıktan kaldırın önerisi.
* Bir varlık dağıtım ilkesi ayarlanmamışsa, depolama ile şifrelenmiş bir varlık üzerinde bir akış Bulucu oluşturulamaz.  Varlık depolama şifreli değilse, sistem bir bulucu oluşturmanıza ve varlığı bir varlık teslim ilkesi olmadan net bir şekilde akışla oluşturmanıza izin verir.
* Tek bir varlıkla ilişkili birden fazla varlık teslim ilkesi olabilir, ancak belirli bir AssetDeliveryProtocol öğesini işlemek için yalnızca bir yol belirtebilirsiniz.  Bu durum, sistem bir Kesintisiz Akış isteği yaptığında bir hata ile sonuçlanmayacak olan AssetDeliveryProtocol. Yumuşakan akış protokolünü belirten iki teslim ilkesini bağlamayı denerseniz ortaya bir hataya neden olur.
* Mevcut bir akış Bulucu olan bir varlığınız varsa, kıymete yeni bir ilke bağlayamazsınız, var olan bir ilkenin varlığını kesebilirsiniz veya varlıkla ilişkili bir teslim ilkesini güncelleştiremezsiniz.  İlk olarak, akış bulucuyu kaldırmanız, ilkeleri ayarlamanız ve ardından akış bulucuyu yeniden oluşturmanız gerekir.  Akış bulucuyu yeniden oluşturduğunuzda aynı Locatorıd 'yi kullanabilirsiniz, ancak içerik kaynak veya aşağı akış CDN tarafından önbelleğe alınmasından bu yana istemci sorunlarına yol açmayabileceğinizden emin olmanız gerekir.

> [!NOTE]
> 
> Media Services varlıklara erişirken, HTTP isteklerinizin belirli üstbilgi alanlarını ve değerlerini ayarlamanız gerekir. Daha fazla bilgi için bkz. [Media Services REST API Geliştirme Için kurulum](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Media Services’e bağlanmak

AMS API 'sine bağlanma hakkında daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasıyla Azure MEDIA SERVICES API 'Sine erişme](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Varlık teslim ilkesini temizle
### <a name="create-asset-delivery-policy"></a><a id="create_asset_delivery_policy"></a>Varlık teslim İlkesi Oluştur
Aşağıdaki HTTP isteği, dinamik şifrelemeyi uygulamamak ve akışı şu protokollerden birine teslim etmek için belirten bir varlık teslim ilkesi oluşturur: MPEG DASH, HLS ve Kesintisiz Akış protokolleri. 

Bir AssetDeliveryPolicy oluştururken belirtebileceğiniz değerler hakkında daha fazla bilgi için, [assetdeliverypolicy tanımlanırken kullanılan türlere](#types) bakın.   

İstek:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
Host: media.windows.net

{"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null}
```

Yanıt:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 363
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 06:21:27 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
"Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null,
"Created":"2015-02-08T06:21:27.6908329Z",
"LastModified":"2015-02-08T06:21:27.6908329Z"}
```

### <a name="link-asset-with-asset-delivery-policy"></a><a id="link_asset_with_asset_delivery_policy"></a>Varlığı varlık teslim ilkesiyle bağla
Aşağıdaki HTTP isteği, belirtilen varlığı için varlık teslim ilkesine bağlar.

İstek:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
Host: media.windows.net

{"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}
```

Yanıt:

```output
HTTP/1.1 204 No Content
```


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption varlık teslim ilkesi
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>EnvelopeEncryption türünün içerik anahtarını oluşturun ve varlığa bağlayın
DynamicEnvelopeEncryption teslim ilkesi belirtirken, varlığınızı EnvelopeEncryption türünün bir içerik anahtarına bağlamanız gerekir. Daha fazla bilgi için bkz. [içerik anahtarı oluşturma](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a><a id="get_delivery_url"></a>Teslim URL 'SI al
Önceki adımda oluşturulan içerik anahtarının belirtilen teslim yöntemi için teslim URL 'sini alın. İstemci, korunan içeriği kayıttan yürütmek için bir AES anahtarı veya PlayReady lisansı istemek üzere döndürülen URL 'YI kullanır.

HTTP isteğinin gövdesinde alınacak URL 'nin türünü belirtin. İçeriğinizi PlayReady ile koruyorsanız, keyDeliveryType: {"keyDeliveryType": 1} için 1 kullanarak Media Services PlayReady lisans alma URL 'SI isteyin. İçeriğinizi zarf şifrelemesiyle koruyorsanız keyDeliveryType: {"keyDeliveryType": 2} için 2 belirterek bir anahtar alma URL 'SI isteyin.

İstek:

```console
POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
Content-Type: application/json
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
Host: media.windows.net
Content-Length: 21

{"keyDeliveryType":2}
```

Yanıt:

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 198
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
request-id: d26f65d2-fe65-4136-8fcf-31545be68377
x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 21:42:30 GMT

{"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}
```


### <a name="create-asset-delivery-policy"></a>Varlık teslim İlkesi Oluştur
Aşağıdaki HTTP isteği, **HLS** protokolüne dinamik zarf şifrelemesi (**DynamicEnvelopeEncryption**) uygulamak Için yapılandırılmış olan **assetdeliverypolicy** 'yi oluşturur (Bu örnekte, diğer protokollerin akışa erişimi engellenir). 

Bir AssetDeliveryPolicy oluştururken belirtebileceğiniz değerler hakkında daha fazla bilgi için, [assetdeliverypolicy tanımlanırken kullanılan türlere](#types) bakın.   

İstek:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}
```


Yanıt:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 460
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 09 Feb 2015 05:24:38 GMT

{"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}
```


### <a name="link-asset-with-asset-delivery-policy"></a>Varlığı varlık teslim ilkesiyle bağla
Bkz. varlık [teslim ilkesiyle varlık bağlama](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Dynamiccommonencryptıon varlık teslim ilkesi
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>CommonEncryption türünün içerik anahtarını oluşturun ve varlığa bağlayın
DynamicCommonEncryption dağıtım ilkesi belirtirken, varlığınızı Commonencryptıon türünde bir içerik anahtarına bağlamanız gerekir. Daha fazla bilgi için bkz. [içerik anahtarı oluşturma](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Teslim URL 'SI al
Önceki adımda oluşturulan içerik anahtarının PlayReady Delivery yönteminin teslim URL 'sini alın. İstemci, korunan içeriği kayıttan yürütmek için bir PlayReady lisansı istemek üzere döndürülen URL 'YI kullanır. Daha fazla bilgi için bkz. [teslim URL 'Si al](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Varlık teslim İlkesi Oluştur
Aşağıdaki HTTP isteği, **kesintisiz akış** protokolüne dinamik ortak şifreleme (**dynamiccommonencryption**) uygulamak Için yapılandırılmış olan **assetdeliverypolicy** oluşturur (Bu örnekte, diğer protokollerin akış ile engellenmesi). 

Bir AssetDeliveryPolicy oluştururken belirtebileceğiniz değerler hakkında daha fazla bilgi için, [assetdeliverypolicy tanımlanırken kullanılan türlere](#types) bakın.   

İstek:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}
```


Widevine DRM kullanarak içeriğinizi korumak istiyorsanız, AssetDeliveryConfiguration değerlerini Widevınelicensetanışıtionurl (7 değerine sahiptir) kullanacak şekilde güncelleştirin ve lisans teslimi hizmetinin URL 'sini belirtin. Widevine lisansları teslim etmenize yardımcı olması için aşağıdaki AMS iş ortaklarını kullanabilirsiniz: [Axinom](https://www.axinom.com), [ezdrm](https://ezdrm.com/), [ROI Labs](https://castlabs.com/company/partners/azure/).

Örnek: 

```console
{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}
```

> [!NOTE]
> Widevine ile şifrelerken yalnızca DASH kullanarak teslim edebilirsiniz. Varlık teslim protokolünde DASH (2) belirttiğinizden emin olun.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Varlığı varlık teslim ilkesiyle bağla
Bkz. varlık [teslim ilkesiyle varlık bağlama](#link_asset_with_asset_delivery_policy)

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>AssetDeliveryPolicy tanımlanırken kullanılan türler

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

Aşağıdaki enum varlık teslim protokolü için ayarlayabileceğiniz değerleri açıklar.

```csharp
[Flags]
public enum AssetDeliveryProtocol
{
    /// <summary>
    /// No protocols.
    /// </summary>
    None = 0x0,

    /// <summary>
    /// Smooth streaming protocol.
    /// </summary>
    SmoothStreaming = 0x1,

    /// <summary>
    /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
    /// </summary>
    Dash = 0x2,

    /// <summary>
    /// Apple HTTP Live Streaming protocol.
    /// </summary>
    HLS = 0x4,

    ProgressiveDownload = 0x10, 

    /// <summary>
    /// Include all protocols.
    /// </summary>
    All = 0xFFFF
}
```

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

Aşağıdaki enum varlık teslim ilkesi türü için ayarlayabileceğiniz değerleri açıklar.  

```csharp
public enum AssetDeliveryPolicyType
{
    /// <summary>
    /// Delivery Policy Type not set.  An invalid value.
    /// </summary>
    None,

    /// <summary>
    /// The Asset should not be delivered via this AssetDeliveryProtocol. 
    /// </summary>
    Blocked, 

    /// <summary>
    /// Do not apply dynamic encryption to the asset.
    /// </summary>
    /// 
    NoDynamicEncryption,  

    /// <summary>
    /// Apply Dynamic Envelope encryption.
    /// </summary>
    DynamicEnvelopeEncryption,

    /// <summary>
    /// Apply Dynamic Common encryption.
    /// </summary>
    DynamicCommonEncryption
    }
```

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

Aşağıdaki Enum, içerik anahtarının teslim yöntemini istemciye yapılandırmak için kullanabileceğiniz değerleri açıklar.
    
```csharp
public enum ContentKeyDeliveryType
{
    /// <summary>
    /// None.
    ///
    </summary>
    None = 0,

    /// <summary>
    /// Use PlayReady License acquisition protocol
    ///
    </summary>
    PlayReadyLicense = 1,

    /// <summary>
    /// Use MPEG Baseline HTTP key protocol.
    ///
    </summary>
    BaselineHttp = 2,

    /// <summary>
    /// Use Widevine License acquisition protocol
    ///
    </summary>
    Widevine = 3

}
```


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

Aşağıdaki Enum, bir varlık teslim ilkesi için özel yapılandırma almak üzere kullanılan anahtarları yapılandırmak için ayarlayabileceğiniz değerleri açıklar.

```csharp
public enum AssetDeliveryPolicyConfigurationKey
{
    /// <summary>
    /// No policies.
    /// </summary>
    None,

    /// <summary>
    /// Exact Envelope key URL.
    /// </summary>
    EnvelopeKeyAcquisitionUrl,

    /// <summary>
    /// Base key url that will have KID=<Guid> appended for Envelope.
    /// </summary>
    EnvelopeBaseKeyAcquisitionUrl,

    /// <summary>
    /// The initialization vector to use for envelope encryption in Base64 format.
    /// </summary>
    EnvelopeEncryptionIVAsBase64,

    /// <summary>
    /// The PlayReady License Acquisition Url to use for common encryption.
    /// </summary>
    PlayReadyLicenseAcquisitionUrl,

    /// <summary>
    /// The PlayReady Custom Attributes to add to the PlayReady Content Header
    /// </summary>
    PlayReadyCustomAttributes,

    /// <summary>
    /// The initialization vector to use for envelope encryption.
    /// </summary>
    EnvelopeEncryptionIV,

    /// <summary>
    /// Widevine DRM acquisition url
    /// </summary>
    WidevineLicenseAcquisitionUrl
}
```

## <a name="additional-notes"></a>Ek notlar

* Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

