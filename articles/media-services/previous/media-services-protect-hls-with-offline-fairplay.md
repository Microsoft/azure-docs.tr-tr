---
title: Çevrimdışı Apple FairPlay-Azure ile HLS içeriğini koruma | Microsoft Docs
description: Bu konu, genel bir bakış sağlar ve Apple FairPlay ile HTTP Canlı Akışı (HLS) içeriğinizi çevrimdışı modda dinamik olarak şifrelemek üzere Azure Media Services nasıl kullanacağınızı gösterir.
services: media-services
keywords: HLS, DRM, FairPlay Akışı (FPS), çevrimdışı, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: 228b00a19bac9c773fce8e502d302314821fbf39
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871648"
---
# <a name="offline-fairplay-streaming-for-ios"></a>İOS için çevrimdışı FairPlay Akışı 

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Media Services sürümünü seçin:"]
> * [Sürüm 3](../latest/offline-fairplay-for-ios.md)
> * [Sürüm 2](media-services-protect-hls-with-offline-fairplay.md)

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>En son sürüm olan [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)’ü inceleyin. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-from-v2-to-v3.md)

Azure Media Services, aşağıdakileri kapsayan iyi tasarlanmış bir [içerik koruma hizmetleri](https://azure.microsoft.com/services/media-services/content-protection/) kümesi sağlar:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- AES-128 şifrelemesi

Dijital hak yönetimi (DRM)/Gelişmiş Şifreleme Standardı (AES) içerik şifrelemesi, çeşitli akış protokolleri için istek üzerine dinamik olarak gerçekleştirilir. DRM lisansı/AES şifre çözme anahtar teslim hizmetleri de Media Services tarafından sağlanır.

Çeşitli akış protokollerinde çevrimiçi akış için içeriğin korunmasının yanı sıra, korumalı içerik için çevrimdışı mod da çoğunlukla istenen bir özelliktir. Çevrimdışı mod desteği aşağıdaki senaryolar için gereklidir:

* Seyahat sırasında olduğu gibi Internet bağlantısı kullanılamadığında kayıttan yürütme.
* Bazı içerik sağlayıcıları, bir ülke/bölge kenarlığının ötesinde DRM lisans teslimine izin verebilir. Kullanıcılar, ülke/bölge dışına yolculukta içerik izlemek istiyorsam, çevrimdışı indirme gerekir.
* Bazı ülkelerde/bölgelerde internet kullanılabilirliği ve/veya bant genişliği hala sınırlı olur. Kullanıcılar, tatmin edici bir görüntüleme deneyimi için yeterince yüksek bir çözünürlükte içerik izleyebilmek üzere önce indirmeyi seçebilir. Bu durumda, sorun genellikle ağ kullanılabilirliği ancak sınırlı ağ bant genişliği değildir. Üst düzey (OTT)/çevrimiçi video platformu (OVP) sağlayıcıları, çevrimdışı mod desteği ister.

Bu makalede iOS 10 veya üstünü çalıştıran cihazları hedefleyen FairPlay streaming (FPS) çevrimdışı mod desteği ele alınmaktadır. Bu özellik, macOS 'ta watchOS, tvOS veya Safari gibi diğer Apple platformları için desteklenmez.

## <a name="preliminary-steps"></a>Ön adımlar
İOS 10 + cihazında FairPlay için çevrimdışı DRM uygulamadan önce:

* FairPlay için çevrimiçi içerik koruması hakkında bilgi sahibi olun. Daha fazla bilgi için aşağıdaki makalelere ve örneklere bakın:

    - [Azure Media Services için Apple FairPlay streaming genel kullanıma sunuldu](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Apple FairPlay veya Microsoft PlayReady ile HLS içeriğinizi koruma](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [Çevrimiçi FPS akışı için bir örnek](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Apple geliştirici ağından FPS SDK 'sını edinin. FPS SDK 'Sı iki bileşen içerir:

    - Anahtar güvenlik modülünü (KSM), istemci örneklerini, bir belirtimi ve bir test vektörü kümesini içeren FPS sunucu SDK 'Sı.
    - G işlevi belirtimini içeren FPS dağıtım paketi, FPS sertifikası, müşteriye özgü özel anahtar ve uygulama gizli anahtarı oluşturma yönergeleriyle birlikte. Apple, FPS dağıtım paketini yalnızca lisanslı içerik sağlayıcılarına yayınlar.

## <a name="configuration-in-media-services"></a>Media Services yapılandırma
[Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices)aracılığıyla kare dışı çevrimdışı mod yapılandırması için, MEDIA SERVICES .NET SDK sürümü 4.0.0.4 veya üstünü kullanın. Bu, FPS çevrimdışı modunu yapılandırmak IÇIN gerekli API 'yi sağlar.
Ayrıca, çevrimiçi modda FPS içerik korumasını yapılandırmak için çalışma kodu gerekir. FPS için çevrimiçi mod içerik korumasını yapılandırmak üzere kodu aldıktan sonra, yalnızca aşağıdaki iki değişikliğe ihtiyacınız vardır.

## <a name="code-change-in-the-fairplay-configuration"></a>FairPlay yapılandırmasındaki kod değişikliği
İlk değişiklik, çevrimdışı DRM senaryosuna izin veren true olan objDRMSettings. EnableOfflineMode adlı bir "çevrimdışı modunu etkinleştir" Boole değeri tanımlamaktır. Bu göstergeye bağlı olarak, FairPlay yapılandırmasında aşağıdaki değişikliği yapın:

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Varlık teslim ilkesi yapılandırmasındaki kod değişikliği
İkinci değişiklik, < AssetDeliveryPolicyConfigurationKey, String > sözlüğüne üçüncü anahtarı eklemektir.
Aşağıda gösterildiği gibi AssetDeliveryPolicyConfigurationKey ekleyin:
 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

Bu adımdan sonra, FPS varlık teslim ilkesindeki < Dictionary_AssetDeliveryPolicyConfigurationKey > dizesi aşağıdaki üç girişi içerir:

* AssetDeliveryPolicyConfigurationKey. FairPlayBaseLicenseAcquisitionUrl veya AssetDeliveryPolicyConfigurationKey. FairPlayLicenseAcquisitionUrl, kullanılan FPS KSM/Key sunucusu ve aynı varlık teslimini yeniden kullanma gibi etkenlere bağlı olarak birden çok varlık genelinde ilke
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey. AllowPersistentLicense

Artık Media Services hesabınız, çevrimdışı FairPlay lisansları sunacak şekilde yapılandırılmıştır.

## <a name="sample-ios-player"></a>Örnek iOS oynatıcı
FPS 'nin çevrimdışı mod desteği yalnızca iOS 10 ve üzeri sürümlerde kullanılabilir. FPS sunucu SDK 'Sı (sürüm 3,0 veya üzeri), belge ve FPS çevrimdışı modu için örnek içerir. Özellikle, FPS sunucu SDK 'Sı (sürüm 3,0 veya üzeri), çevrimdışı modla ilgili aşağıdaki iki öğeyi içerir:

* Belgedeki "FairPlay streaming ve HTTP Canlı Akışı ile çevrimdışı oynatma." 14 Eylül 2016, elma. FPS, sunucu SDK 'Sı 4,0 sürümünde bu belge ana FPS belgesi ile birleştirilmiştir.
* Örnek kod: \FairPlay Streaming Server SDK sürümü 3.1 \ Development\client\hlsaçlation_with_fps\hldağılmış Alog\. HLSCatalog örnek uygulamasında, çevrimdışı mod özelliklerini uygulamak için aşağıdaki kod dosyaları kullanılır:

    - AssetPersistenceManager. Swift kod dosyası: AssetPersistenceManager, bu örnekte nasıl yapılacağını gösteren ana sınıftır:

        - İndirme işlemini başlatmak ve iptal etmek ve mevcut varlıkları cihazları silmek için kullanılan API 'Ler gibi HLS akışlarını indirmeyi yönetin.
        - İndirme ilerlemesini izleyin.
    - AssetListTableViewController. Swift ve AssetListTableViewCell. Swift kod dosyaları: AssetListTableViewController, bu örneğin ana arabirimidir. Örneğin, bir indirmeyi yürütmek, indirmek, silmek veya iptal etmek için kullanabileceği varlıkların bir listesini sağlar. 

Bu adımlarda çalışan bir iOS yürütücüsünün nasıl ayarlanacağı gösterilmektedir. FPS sunucu SDK 'Sı 4.0.1 sürümündeki HLSCatalog örneğinden başlattığınız varsayılarak aşağıdaki kod değişikliklerini yapın:

Hldağık\shared\managers\contentkeydelegate.exe içinde, aşağıdaki kodu kullanarak yöntemini `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` uygulayın. "DrmUr", HLS URL 'sine atanmış bir değişken olmasına izin verir.

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

Hldağık\shared\managers\contentkeydelegate.exe içinde, yöntemini `requestApplicationCertificate()`uygulayın. Bu uygulama, sertifikayı aygıtla (yalnızca ortak anahtar) veya sertifikayı Web üzerinde barındırmanıza bağlı olarak değişir. Aşağıdaki uygulama, test örneklerinde kullanılan barındırılan uygulama sertifikasını kullanır. "CertUrl" ın uygulama sertifikasının URL 'sini içeren bir değişken olmasına izin verin.

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

![Çevrimdışı FairPlay iOS uygulama akışları](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Kendi test video URL 'nizi, FairPlay lisans alma URL 'sini ve bunları ayarladıysanız uygulama sertifikası URL 'nizi kullanın. Ya da test örneklerini içeren bir sonraki bölüme devam edebilirsiniz.

## <a name="integrated-test"></a>Tümleşik test
Media Services üç test örneği aşağıdaki üç senaryoyu kapsar:

* FPS korumalı, video, ses ve alternatif ses izi ile
* FPS korumalı, video ve ses ile, ancak alternatif ses parçası yok
* FPS korumalı, yalnızca video ile ve ses olmadan

Bu örnek, bir Azure Web uygulamasında barındırılan ilgili uygulama sertifikasıyla [Bu tanıtım sitesinde](https://aka.ms/poc#22)bulabilirsiniz.
FPS sunucu SDK 'sının sürüm 3 veya sürüm 4 örneğinde, ana çalma listesi alternatif ses içeriyorsa, çevrimdışı modda yalnızca ses çalar. Bu nedenle, alternatif sesi çıkarmanız gerekir. Diğer bir deyişle, daha önce listelenen ikinci ve üçüncü örnekler çevrimiçi ve çevrimdışı modda çalışır. İlk olarak listelenen örnek, çevrimiçi akış düzgün şekilde çalışırken yalnızca çevrimdışı modda ses çalar.

## <a name="faq"></a>SSS
Aşağıdaki sık sorulan sorular, sorun giderme konusunda yardım sağlar:

- **Çevrimdışı mod sırasında yalnızca ses oynayabilir ancak video değil mi?** Bu davranış, örnek uygulamanın tasarımıyla aynı şekilde görünür. Çevrimdışı modda alternatif bir ses izi varsa (HLS için bu durum söz konusu olduğunda), alternatif ses kanalında hem iOS 10 hem de iOS 11 varsayılandır. Bu davranışı, FPS çevrimdışı modu için dengelemek üzere akıştan alternatif ses parçasını kaldırın. Bunu Media Services yapmak için, "yalnızca ses = false" dinamik bildirim filtresini ekleyin. Diğer bir deyişle, HLS URL 'SI. ISM/manifest ile biter (format = M3U8-AAPL, Audio-Only = false). 
- **Yalnızca ses ekle = yanlış olarak çevrimdışı modda video olmadan hala ses çalsın.** İçerik teslim ağı (CDN) önbellek anahtarı tasarımına bağlı olarak, içerik önbelleğe alınmış olabilir. Önbelleği temizleme.
- **/SN, iOS 11 ' e ek olarak iOS 11 ' de de desteklenir.** Evet. FPS çevrimdışı modu, iOS 10 ve iOS 11 için desteklenir.
- **"FairPlay streaming ile çevrimdışı kayıttan yürütme ve HTTP Canlı Akışı" belgelerini FPS sunucu SDK 'sında neden bulamıyorum?** Bu belge, FPS sunucu SDK 'Sı sürüm 4 ' te birleştirildiğinden "FairPlay streaming Programlama Kılavuzu" ile birleştirilmiştir.
- **Son parametre, FPS çevrimdışı modu için aşağıdaki API 'de ne için stand?** 
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Bu API için belgeler için bkz. [FairPlayConfiguration. CreateSerializedFairPlayOptionConfiguration yöntemi](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). Parametresi, birim olarak saat ile çevrimdışı kiralama süresini temsil eder.
- **İOS cihazlarında indirilen/OFFLINE dosya yapısı nedir?** Bir iOS cihazında indirilen dosya yapısı aşağıdaki ekran görüntüsüne benzer şekilde görünür. `_keys` Klasör, indirilen fps lisanslarını, her bir lisans hizmeti ana bilgisayarı için bir depolama dosyası ile depolar. Klasör `.movpkg` , ses ve video içeriğini depolar. Bir tire ile biten ve ardından sayısal olan bir ada sahip ilk klasörün video içeriği vardır. Sayısal değer, video yorumlamaları 'nın en yüksek bant genişliğidir. Bir kısa çizgi ile biten bir ada sahip ikinci klasör, izleyen 0 ile ses içeriği içerir. "Data" adlı üçüncü klasör, FPS içeriğinin ana çalma listesini içerir. Son olarak, Boot. xml, `.movpkg` klasör içeriğinin tamamının bir açıklamasını sağlar. 

![Çevrimdışı FairPlay iOS örnek uygulama dosyası yapısı](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Örnek bir Boot. xml dosyası:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="summary"></a>Özet
Bu belge, FPS çevrimdışı modunu uygulamak için kullanabileceğiniz aşağıdaki adımları ve bilgileri içerir:

* Media Services .NET API 'SI aracılığıyla Media Services Content Protection yapılandırması, Media Services dinamik FairPlay şifrelemesi ve FairPlay lisans teslimini yapılandırır.
* FPS sunucu SDK 'sının örneğine dayalı bir iOS oynatıcı, çevrimiçi akış modunda veya çevrimdışı modda FPS içeriğini oynatacak bir iOS oynatıcı ayarlıyor.
* Örnek FPS videoları, çevrimdışı modu ve çevrimiçi akışı test etmek için kullanılır.
* Bir SSS, FPS çevrimdışı modu hakkında soruları yanıtlar.
