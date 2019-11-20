---
title: Azure Media Services kullanarak DRM alt sisteminin hibrit tasarımı | Microsoft Docs
description: Bu konu, Azure Media Services kullanılarak DRM alt sisteminin karma tasarımını ele alır.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: juliako
ms.openlocfilehash: d15bfcfbae3b24e1a9b29dc74f9b41a979e63ae9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "69014662"
---
# <a name="hybrid-design-of-drm-subsystems"></a>DRM alt sistemlerinin karma tasarımı 

Bu konu, Azure Media Services kullanılarak DRM alt sisteminin karma tasarımını ele alır.

## <a name="overview"></a>Genel Bakış

Azure Media Services aşağıdaki üç DRM sistemi için destek sağlar:

* PlayReady
* Widevine (modüler)
* FairPlay

DRM desteği, tüm 3 DRMs 'Leri bir tarayıcı oynatıcı SDK 'Sı olarak desteklemek Azure Media Player, DRM şifrelemesini (dinamik şifrelemeyi) ve lisans teslimini içerir.

DRM/CENC alt sistemi tasarımı ve uygulamasını ayrıntılı bir şekilde görmek için lütfen bkz. [birden çok DRM ve Access Control CENC](media-services-cenc-with-multidrm-access-control.md)adlı belge.

Üç DRM sistemine yönelik kapsamlı destek sunuyoruz, ancak bazen müşterilerin karma bir DRM alt sistemi oluşturmak için Azure Media Services ek olarak kendi altyapısının/alt sistemlerinin çeşitli parçalarını kullanması gerekir.

Müşteriler tarafından istenen bazı yaygın sorular aşağıda verilmiştir:

* "Kendi DRM lisans sunucularımı kullanabilir miyim?" (Bu durumda, müşteriler, gömülü iş mantığı ile DRM lisans sunucusu grubuna yatırım yapmış demektir).
* "AMS 'de içerik barındırmadan yalnızca Azure Media Services için DRM lisans teslimatını kullanabilir miyim?"

## <a name="modularity-of-the-ams-drm-platform"></a>AMS DRM platformunun modülerliği

Azure Media Services DRM, kapsamlı bir bulut video platformunun bir parçası olarak esneklik ve modülerliği göz önünde bulundurularak bir tasarıma sahiptir. Aşağıdaki tabloda açıklanan aşağıdaki farklı birleşimlerden herhangi biriyle Azure Media Services kullanabilirsiniz (aşağıdaki tabloda kullanılan Gösterimin açıklaması). 

|**& Kaynağı barındıran içerik**|**İçerik şifreleme**|**DRM lisansı teslimi**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Üçüncü taraf|
|AMS|Üçüncü taraf|AMS|
|AMS|Üçüncü taraf|Üçüncü taraf|
|Üçüncü taraf|Üçüncü taraf|AMS|

### <a name="content-hosting--origin"></a>& Kaynağı barındıran içerik

* AMS: video varlığı AMS 'de barındırılır ve akış, AMS akış uç noktaları üzerinden (dinamik paketleme olması gerekmez).
* Üçüncü taraf: video, AMS dışında bir üçüncü taraf akış platformunda barındırılır ve teslim edilir.

### <a name="content-encryption"></a>İçerik şifreleme

* AMS: içerik şifreleme, AMS dinamik şifrelemesi tarafından dinamik olarak/isteğe bağlı olarak gerçekleştirilir.
* Üçüncü taraf: içerik şifreleme, bir ön işleme iş akışı kullanılarak AMS dışında gerçekleştirilir.

### <a name="drm-license-delivery"></a>DRM lisansı verme

* AMS DRM lisansı, AMS lisans teslim hizmeti tarafından dağıtılır.
* Üçüncü taraf: DRM lisansı, AMS dışında bir üçüncü taraf DRM lisans sunucusu tarafından dağıtılır.

## <a name="configure-based-on-your-hybrid-scenario"></a>Karma senaryonuz temelinde yapılandırma

### <a name="content-key"></a>İçerik anahtarı

Bir içerik anahtarı yapılandırması sayesinde, hem AMS dinamik şifreleme hem de AMS lisans teslimat hizmeti 'nin aşağıdaki özniteliklerini denetleyebilirsiniz:

* Dinamik DRM şifrelemesi için kullanılan içerik anahtarı.
* Lisans Teslim Hizmetleri tarafından teslim edilecek DRM lisans içeriği: haklar, içerik anahtarı ve kısıtlamalar.
* **İçerik anahtarı yetkilendirme ilkesi kısıtlamasının**türü: açık, IP veya belirteç kısıtlaması.
* **İçerik anahtarı yetkilendirme ilkesi kısıtlamasının** **belirteç** türü kullanılırsa, bir lisans verilmeden önce **içerik anahtarı yetkilendirme ilkesi kısıtlamasının** karşılanması gerekir.

### <a name="asset-delivery-policy"></a>Varlık teslim ilkesi

Bir varlık teslim ilkesinin yapılandırması sayesinde, AMS dinamik paketleyicisi tarafından kullanılan aşağıdaki öznitelikleri ve bir AMS akış uç noktasının dinamik şifrelemesini kontrol edebilirsiniz:

* CENC (PlayReady ve Widevine) altındaki DASH gibi akış protokolü ve DRM şifreleme kombinasyonu, Widevine veya PlayReady altında PlayReady, HLS altında kesintisiz akış.
* Dahil edilen her bir DRMs için varsayılan/katıştırılmış lisans teslimi URL 'Leri.
* DASH MPD veya HLS çalma listesindeki lisans alma URL 'Lerinin (LA_URLs) Widevine ve FairPlay için sırasıyla anahtar KIMLIĞI (KıD) sorgu dizesi içerip içermediğini belirtir.

## <a name="scenarios-and-samples"></a>Senaryolar ve örnekler

Önceki bölümde yer alan açıklamaları temel alarak, aşağıdaki beş karma senaryo ilgili **içerik anahtarı**-**varlık teslim ilkesi** yapılandırma kombinasyonlarını kullanır (son sütunda bahsedilen örnekler tablosunu izler):

|**& Kaynağı barındıran içerik**|**DRM şifrelemesi**|**DRM lisansı teslimi**|**İçerik anahtarını yapılandır**|**Varlık teslim ilkesini yapılandırma**|**Örnek**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Evet|Evet|Örnek 1|
|AMS|AMS|Üçüncü taraf|Evet|Evet|Örnek 2|
|AMS|Üçüncü taraf|AMS|Evet|Hayır|Örnek 3|
|AMS|Üçüncü taraf|Dışarıya|Hayır|Hayır|Örnek 4|
|Üçüncü taraf|Üçüncü taraf|AMS|Evet|Hayır|    

Örneklerde, PlayReady koruması hem DASH hem de kesintisiz akış için geçerlidir. Aşağıdaki video URL 'Leri kesintisiz akış URL 'lardır. Karşılık gelen DASH URL 'Lerini almak için, yalnızca "(format = MPD-Time-CSF)" ekleyin. [Azure Medya test yürütücüyü](https://aka.ms/amtest) bir tarayıcıda test etmek için kullanabilirsiniz. Hangi akış protokolünü kullanacağınızı, hangi teknoloji altında kullanacağınızı yapılandırmanıza olanak tanır. IE11 ve Windows 10 ' da Microsoft Edge, EME aracılığıyla PlayReady 'yi destekler. Daha fazla bilgi için, bkz. [test aracı hakkında ayrıntılar](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Örnek 1

* Kaynak (temel) URL 'SI: https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL (DASH & Düzgünleştir): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (DASH): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* FairPlay LA_URL (HLS): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Örnek 2

* Kaynak (temel) URL 'SI: https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (DASH & Düzgünleştir): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Örnek 3

* Kaynak URL 'SI: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & Düzgünleştir): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Örnek 4

* Kaynak URL 'SI: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & Düzgünleştir): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>Özet

Özet olarak, DRM bileşenleri Azure Media Services, bu konuda açıklandığı gibi, içerik anahtarını ve varlık teslim ilkesini düzgün şekilde yapılandırarak bunları karma bir senaryoda kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Media Services öğrenme yollarını görüntüleyin.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

