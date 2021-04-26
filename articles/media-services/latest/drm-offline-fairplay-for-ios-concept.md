---
title: İOS için Media Services v3 offline FairPlay Akışı
description: Bu konu, genel bir bakış sağlar ve Apple FairPlay ile HTTP Canlı Akışı (HLS) içeriğinizi çevrimdışı modda dinamik olarak şifrelemek üzere Azure Media Services v3 'yi nasıl kullanacağınızı gösterir.
services: media-services
author: willzhan
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: acff3e77b92110c818ea0ab0f8281aef87e9db81
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106069165"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>İOS için Media Services v3 ile çevrimdışı FairPlay Akışı

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

 Azure Media Services, aşağıdakileri kapsayan iyi tasarlanmış bir [içerik koruma hizmetleri](https://azure.microsoft.com/services/media-services/content-protection/) kümesi sağlar:

- Microsoft PlayReady
- Google Widevine
    
    Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.
- Apple FairPlay
- AES-128 şifrelemesi

Dijital hak yönetimi (DRM)/Gelişmiş Şifreleme Standardı (AES) içerik şifrelemesi, çeşitli akış protokolleri için istek üzerine dinamik olarak gerçekleştirilir. DRM lisansı/AES şifre çözme anahtar teslim hizmetleri de Media Services tarafından sağlanır.

Çeşitli akış protokollerinde çevrimiçi akış için içeriğin korunmasının yanı sıra, korumalı içerik için çevrimdışı mod da çoğunlukla istenen bir özelliktir. Çevrimdışı mod desteği aşağıdaki senaryolar için gereklidir:

* Seyahat sırasında olduğu gibi Internet bağlantısı kullanılamadığında kayıttan yürütme.
* Bazı içerik sağlayıcıları, bir ülke/bölge kenarlığının ötesinde DRM lisans teslimine izin verebilir. Kullanıcılar, ülke/bölge dışına yolculukta içerik izlemek istiyorsam, çevrimdışı indirme gerekir.
* Bazı ülkelerde/bölgelerde internet kullanılabilirliği ve/veya bant genişliği hala sınırlı olur. Kullanıcılar, tatmin edici bir görüntüleme deneyimi için yeterince yüksek bir çözünürlükte içerik izleyebilmek üzere önce indirmeyi seçebilir. Bu durumda, sorun genellikle ağ kullanılabilirliği ancak sınırlı ağ bant genişliği değildir. Üst düzey (OTT)/çevrimiçi video platformu (OVP) sağlayıcıları, çevrimdışı mod desteği ister.

Bu makalede iOS 10 veya üstünü çalıştıran cihazları hedefleyen FairPlay streaming (FPS) çevrimdışı mod desteği ele alınmaktadır. Bu özellik, macOS 'ta watchOS, tvOS veya Safari gibi diğer Apple platformları için desteklenmez.

> [!NOTE]
> Çevrimdışı DRM yalnızca içeriği indirdiğinizde lisans için tek bir istek yapmak üzere faturalandırılır. Tüm hatalar faturalandırılmaz.

## <a name="prerequisites"></a>Önkoşullar

İOS 10 + cihazında FairPlay için çevrimdışı DRM uygulamadan önce:

* FairPlay için çevrimiçi içerik korumasını gözden geçirin: 

    - [Apple FairPlay lisansı gereksinimleri ve yapılandırması](drm-fairplay-license-overview.md)
    - [DRM dinamik şifreleme ve lisans teslim hizmetini kullanma](drm-protect-with-drm-tutorial.md)
    - Çevrimiçi FPS akışı yapılandırmasını içeren bir .NET örneği: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Apple geliştirici ağından FPS SDK 'sını edinin. FPS SDK 'Sı iki bileşen içerir:

    - Anahtar güvenlik modülünü (KSM), istemci örneklerini, bir belirtimi ve bir test vektörü kümesini içeren FPS sunucu SDK 'Sı.
    - G işlevi belirtimini içeren FPS dağıtım paketi, FPS sertifikası, müşteriye özgü özel anahtar ve uygulama gizli anahtarı oluşturma yönergeleriyle birlikte. Apple, FPS dağıtım paketini yalnızca lisanslı içerik sağlayıcılarına yayınlar.
* Kopyala https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git . 

    FairPlay yapılandırması eklemek için [.NET kullanarak DRM Ile şifrelemeden](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) kodu değiştirmeniz gerekir.  

## <a name="configure-content-protection-in-azure-media-services"></a>Azure Media Services 'da içerik korumasını yapılandırma

[Getorcreatecontentkeypolicyasync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) yönteminde şunları yapın:

FairPlay ilkesi seçeneğini yapılandıran kodun açıklamasını kaldırın:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Ayrıca, CIBH ContentKeyPolicyOption öğesini ContentKeyPolicyOptions listesine ekleyen kodun açıklamasını kaldırın

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Çevrimdışı modunu etkinleştir

Çevrimdışı modu etkinleştirmek için özel bir StreamingPolicy oluşturun ve [Createstreaminglocatokısync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L561)Içinde StreamingLocator oluştururken adını kullanın.
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true // This enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true // Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        // Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }
}

```

Artık Media Services hesabınız, çevrimdışı FairPlay lisansları sunacak şekilde yapılandırılmıştır.

## <a name="sample-ios-player"></a>Örnek iOS oynatıcı

FPS 'nin çevrimdışı mod desteği yalnızca iOS 10 ve üzeri sürümlerde kullanılabilir. FPS sunucu SDK 'Sı (sürüm 3,0 veya üzeri), belge ve FPS çevrimdışı modu için örnek içerir. Özellikle, FPS sunucu SDK 'Sı (sürüm 3,0 veya üzeri), çevrimdışı modla ilgili aşağıdaki iki öğeyi içerir:

* Belge: "FairPlay streaming ve HTTP Canlı Akışı ile çevrimdışı oynatma." 14 Eylül 2016, elma. FPS, sunucu SDK 'Sı 4,0 sürümünde bu belge ana FPS belgesi ile birleştirilmiştir.
* Örnek kod: HLSCatalog örneği (Apple 'ın FPS sunucu SDK 'sının parçası), \FairPlay Streaming Server SDK sürümü 3.1 \ geliştirme Ment\client\ HLSCatalog_With_FPS \Hldağılmış Alog\. HLSCatalog örnek uygulamasında, çevrimdışı mod özelliklerini uygulamak için aşağıdaki kod dosyaları kullanılır:

    - AssetPersistenceManager. Swift kod dosyası: AssetPersistenceManager, bu örnekte nasıl yapılacağını gösteren ana sınıftır:

        - İndirme işlemini başlatmak ve iptal etmek ve mevcut varlıkları cihazları silmek için kullanılan API 'Ler gibi HLS akışlarını indirmeyi yönetin.
        - İndirme ilerlemesini izleyin.
    - AssetListTableViewController. Swift ve AssetListTableViewCell. Swift kod dosyaları: AssetListTableViewController bu örneğin ana arabirimidir. Örneğin, bir indirmeyi yürütmek, indirmek, silmek veya iptal etmek için kullanabileceği varlıkların bir listesini sağlar. 

Bu adımlarda çalışan bir iOS yürütücüsünün nasıl ayarlanacağı gösterilmektedir. FPS sunucu SDK 'Sı 4.0.1 sürümündeki HLSCatalog örneğinden başlattığınız varsayılarak aşağıdaki kod değişikliklerini yapın:

Hldağık\shared\managers\contentkeydelegate.exe içinde, `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` aşağıdaki kodu kullanarak yöntemini uygulayın. "DrmUr", HLS URL 'sine atanmış bir değişken olmasına izin verir.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

Hldağık\shared\managers\contentkeydelegate.exe içinde, yöntemini uygulayın `requestApplicationCertificate()` . Bu uygulama, sertifikayı aygıtla (yalnızca ortak anahtar) veya sertifikayı Web üzerinde barındırmanıza bağlı olarak değişir. Aşağıdaki uygulama, test örneklerinde kullanılan barındırılan uygulama sertifikasını kullanır. "CertUrl" ın uygulama sertifikasının URL 'sini içeren bir değişken olmasına izin verin.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

Son tümleşik test için hem video URL 'SI hem de uygulama sertifikası URL 'SI "tümleşik test" bölümünde verilmiştir.

Hlstreaalog\shared\resources\streams.exe içinde, test video URL 'nizi ekleyin. İçerik anahtarı KIMLIĞI için, FairPlay lisans alımı URL 'sini, SKD protokolüyle benzersiz değer olarak kullanın.

![Çevrimdışı FairPlay iOS uygulama akışları](media/drm-offline-fairplay-for-ios-concept/offline-fairplay-ios-app-streams.png)

Kendi test video URL 'nizi, FairPlay lisans alma URL 'sini ve bunları ayarladıysanız uygulama sertifikası URL 'nizi kullanın. Ya da test örneklerini içeren bir sonraki bölüme devam edebilirsiniz.

## <a name="integrated-test"></a>Tümleşik test

Media Services üç test örneği aşağıdaki üç senaryoyu kapsar:

* FPS korumalı, video, ses ve alternatif ses izi ile
* FPS korumalı, video ve ses ile, ancak alternatif ses parçası yok
* FPS korumalı, yalnızca video ile ve ses olmadan

Bu örnek, bir Azure Web uygulamasında barındırılan ilgili uygulama sertifikasıyla [Bu tanıtım sitesinde](https://aka.ms/poc#22)bulabilirsiniz.
FPS sunucu SDK 'sının sürüm 3 veya sürüm 4 örneğinde, ana çalma listesi alternatif ses içeriyorsa, çevrimdışı modda yalnızca ses çalar. Bu nedenle, alternatif sesi çıkarmanız gerekir. Diğer bir deyişle, daha önce listelenen ikinci ve üçüncü örnekler çevrimiçi ve çevrimdışı modda çalışır. İlk olarak listelenen örnek, çevrimiçi akış düzgün şekilde çalışırken yalnızca çevrimdışı modda ses çalar.

## <a name="offline-fairplay-questions"></a>Çevrimdışı Fairplay soruları

Bkz. [çevrimdışı Fairplay soruları](questions-collection.md#why-does-only-audio-play-but-not-video-during-offline-mode).
