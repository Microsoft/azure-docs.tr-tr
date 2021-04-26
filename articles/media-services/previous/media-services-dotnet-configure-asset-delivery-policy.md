---
title: .NET SDK ile varlık teslim ilkelerini yapılandırma | Microsoft Docs
description: Bu konuda, Azure Media Services .NET SDK ile farklı varlık teslim ilkelerinin nasıl yapılandırılacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 82e152f257e867dfdbf97c92b041ce57c5a1bdbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014273"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>.NET SDK ile varlık teslim ilkelerini yapılandırma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Genel Bakış
Şifrelenmiş varlıkları teslim etmek istiyorsanız, Media Services içerik teslim iş akışındaki adımlardan biri varlıklar için teslim ilkelerini yapılandırıyorsunuz. Varlık teslim ilkesi, varlığınızın nasıl sunulmasını istediğinizi Media Services söyler: varlığınızı dinamik olarak paketlemeniz gereken (örneğin, MPEG DASH, HLS, Kesintisiz Akış veya All), varlığınızı ve (zarf veya ortak şifreleme) nasıl (zarf veya ortak şifrelemeyi) bir şekilde şifrelemek isteyip istemediğiniz.

Bu makalede, varlık teslim ilkelerinin neden ve nasıl oluşturulacağı ve yapılandırılacağı açıklanmaktadır.

>[!NOTE]
>AMS hesabınız oluşturulduğunda, hesabınıza **Durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçerik akışını başlatmak ve dinamik paketleme ile dinamik şifrelemeden yararlanmak için içerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumda olması gerekir. 
>
>Ayrıca, dinamik paketleme ve dinamik şifrelemeyi kullanabilmeniz için varlığınız bir uyarlamalı bit hızı MP4 'leri veya Uyarlamalı bit hızı Kesintisiz Akış dosyaları içermelidir.

Aynı kıymete farklı ilkeler uygulayabilirsiniz. Örneğin, MPEG DASH ve HLS 'e Kesintisiz Akış ve AES zarf şifrelemeye PlayReady şifrelemesi uygulayabilirsiniz. Herhangi bir teslim ilkesinde tanımlanmayan tüm protokollerin (örneğin, protokol olarak yalnızca HLS‘yi belirten tek bir ilke ekliyorsunuz) akışla aktarılması engellenir. Bunun tek istisnası, hiçbir varlık teslim ilkesinin tanımlanmadığı durumdur. Bu halde tüm protokollere açık bir şekilde izin verilir.

Depolama ile şifrelenmiş bir varlık sağlamak istiyorsanız, varlığın teslim ilkesini yapılandırmanız gerekir. Varlığınızın akışı için, akış sunucusu depolama şifrelemesini kaldırır ve belirtilen teslim ilkesini kullanarak içeriğinizi akışa çıkarır. Örneğin, varlığınızı Gelişmiş Şifreleme Standardı (AES) zarf şifreleme anahtarıyla şifreli olarak sunmak için, ilke türünü **DynamicEnvelopeEncryption** olarak ayarlayın. Depolama şifrelemesini kaldırmak ve varlığı açık olarak akışa almak için, ilke türünü **Nodynamicencryption** olarak ayarlayın. Bu ilke türlerinin nasıl yapılandırılacağını gösteren örnekler aşağıda verilmiştir.

Varlık teslim ilkesini nasıl yapılandırdığınıza bağlı olarak, aşağıdaki akış protokollerini dinamik olarak paketleyebilir, şifreleyebilir ve akışla aktarabilirsiniz: Kesintisiz Akış, HLS ve MPEG DASH.

Aşağıdaki listede, pürüzsüz, HLS ve DASH akışını sağlamak için kullandığınız biçimler gösterilmektedir.

Kesintisiz Akış:

{akış uç noktası adı-media services hesabı adı}.streaming.mediaservices.windows.net/{konum kimliği}/{dosya adı}.ism/Manifest

HLS

{akış uç noktası adı-media services hesabı adı}.streaming.mediaservices.windows.net/{konum kimliği}/{dosya adı}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{akış uç noktası adı-media services hesabı adı}.streaming.mediaservices.windows.net/{konum kimliği}/{dosya adı}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Dikkat edilmesi gerekenler
* AssetDeliveryPolicy öğesini silmeden önce varlıkla ilişkili tüm akış bulıcıları silmelisiniz. Daha sonra isterseniz yeni bir AssetDeliveryPolicy ile yeni akış Konumlandırıcı oluşturabilirsiniz.
* Bir varlık dağıtım ilkesi ayarlanmamışsa, depolama ile şifrelenmiş bir varlık üzerinde bir akış Bulucu oluşturulamaz.  Varlık depolama şifreli değilse, sistem bir bulucu oluşturmanıza ve varlığı bir varlık teslim ilkesi olmadan net bir şekilde akışla oluşturmanıza izin verir.
* Tek bir varlıkla ilişkili birden fazla varlık teslim ilkesi olabilir, ancak belirli bir AssetDeliveryProtocol öğesini işlemek için yalnızca bir yol belirtebilirsiniz.  Bu durum, sistem bir Kesintisiz Akış isteği yaptığında bir hata ile sonuçlanmayacak olan AssetDeliveryProtocol. Yumuşakan akış protokolünü belirten iki teslim ilkesini bağlamayı denerseniz ortaya bir hataya neden olur.
* Mevcut bir akış Bulucu olan bir varlığınız varsa, varlığa yeni bir ilke bağlayamazsınız (mevcut bir ilkenin varlıkla bağlantısını kaldırabilir ya da varlıkla ilişkili bir teslim ilkesini güncelleştirebilirsiniz).  İlk olarak, akış bulucuyu kaldırmanız, ilkeleri ayarlamanız ve ardından akış bulucuyu yeniden oluşturmanız gerekir.  Akış bulucuyu yeniden oluşturduğunuzda aynı Locatorıd 'yi kullanabilirsiniz, ancak içerik kaynak veya aşağı akış CDN tarafından önbelleğe alınmasından bu yana istemci sorunlarına yol açmayabileceğinizden emin olmanız gerekir.

## <a name="clear-asset-delivery-policy"></a>Varlık teslim ilkesini temizle

Aşağıdaki **Configureclearassetdeliverypolicy** yöntemi, dinamik şifrelemeyi uygulamayacağını ve akışı şu protokollerden birine teslim etmeyi BELIRTIR: MPEG Dash, hls ve kesintisiz akış protokolleri. Bu ilkeyi, depolama şifreli varlıklarınıza uygulamak isteyebilirsiniz.

Bir AssetDeliveryPolicy oluştururken belirtebileceğiniz değerler hakkında daha fazla bilgi için, [assetdeliverypolicy tanımlanırken kullanılan türlere](#types) bakın.

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Dynamiccommonencryptıon varlık teslim ilkesi

Aşağıdaki **Createassetdeliverypolicy** yöntemi, kesintisiz bir akış protokolüne dinamik ortak şifreleme (**dynamiccommonencryption**) uygulamak Için yapılandırılmış olan **assetdeliverypolicy** 'yi oluşturur (diğer protokollerin akış akışı engellenir). Yöntem iki parametre alır: **varlık** (teslim ilkesini uygulamak istediğiniz varlık) ve **ıditentkey** ( **commonencryption** türünün içerik anahtarı), daha fazla bilgi Için bkz.: [içerik anahtarı oluşturma](media-services-dotnet-create-contentkey.md#common_contentkey).

Bir AssetDeliveryPolicy oluştururken belirtebileceğiniz değerler hakkında daha fazla bilgi için, [assetdeliverypolicy tanımlanırken kullanılan türlere](#types) bakın.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

Azure Media Services Ayrıca Widevine şifreleme eklemenize olanak sağlar. Aşağıdaki örnek, varlık teslim ilkesine hem PlayReady hem de Widevine eklendiğini gösterir.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> Widevine ile şifrelerken yalnızca DASH kullanarak teslim edebilirsiniz. Varlık teslim protokolünde DASH belirttiğinizden emin olun.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption varlık teslim ilkesi
Aşağıdaki **Createassetdeliverypolicy** yöntemi, kesintisiz akış, HLS ve Dash protokollerine dinamik zarf şifrelemesi (**DynamicEnvelopeEncryption**) uygulamak Için yapılandırılmış olan **assetdeliverypolicy** 'yi oluşturur (bazı protokoller Belirtmemeye karar verirseniz, bunlar akış tarafından engellenirler). Yöntem iki parametre alır: **varlık** (teslim ilkesini uygulamak istediğiniz varlık) ve **ıditentkey** ( **EnvelopeEncryption** türünün içerik anahtarı), daha fazla bilgi Için bkz.: [içerik anahtarı oluşturma](media-services-dotnet-create-contentkey.md#envelope_contentkey).

Bir AssetDeliveryPolicy oluştururken belirtebileceğiniz değerler hakkında daha fazla bilgi için, [assetdeliverypolicy tanımlanırken kullanılan türlere](#types) bakın.   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamic Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>AssetDeliveryPolicy tanımlanırken kullanılan türler

### <a name="assetdeliveryprotocol"></a><a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

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
### <a name="assetdeliverypolicytype"></a><a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

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
### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

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
### <a name="assetdeliverypolicyconfigurationkey"></a><a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

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

