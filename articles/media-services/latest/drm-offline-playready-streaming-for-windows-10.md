---
title: Çevrimdışı PlayReady akışını yapılandırma
description: Bu makalede, Azure Media Services v3 hesabınızı Windows 10 ' un çevrimdışı için nasıl yapılandıracağınız açıklanmaktadır.
services: media-services
keywords: DASH, DRM, Widevine çevrimdışı modu, Exooynatıcı, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: f720549d0e9edba0865254d6427c19995d70fd8f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106069125"
---
# <a name="offline-playready-streaming-for-windows-10-with-media-services-v3"></a>Media Services v3 ile Windows 10 için çevrimdışı PlayReady akışı

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services, DRM koruması ile çevrimdışı indirmeyi/kayıttan yürütmeyi destekler. Bu makalede, Windows 10/PlayReady istemcileri için Azure Media Services çevrimdışı desteği ele alınmaktadır. Aşağıdaki makalelerde iOS/FairPlay ve Android/Widevine cihazları için çevrimdışı mod desteği hakkında bilgi edinebilirsiniz:

- [iOS için Çevrimdışı FairPlay Akışı](drm-offline-fairplay-for-ios-concept.md)
- [Android için çevrimdışı Widevine akışı](drm-offline-widevine-for-android.md)

> [!NOTE]
> Çevrimdışı DRM yalnızca içeriği indirdiğinizde lisans için tek bir istek yapmak üzere faturalandırılır. Tüm hatalar faturalandırılmaz.

## <a name="background-on-offline-mode-playback"></a>Çevrimdışı modda kayıttan yürütme arka planı

Bu bölüm, çevrimdışı modda Kayıttan yürütmede bazı arka plan sağlar, özellikle neden:

* Bazı ülkelerde/bölgelerde Internet kullanılabilirliği ve/veya bant genişliği hala sınırlı olur. Kullanıcılar, tatmin edici görüntüleme deneyimi için yeterince yüksek çözünürlükte içerik izleyebilmek için önce indirmeyi seçebilir. Bu durumda, genellikle ağ bant genişliği sınırlı olduğundan, sorun ağ kullanılabilirliği değildir. OTT/OVP sağlayıcıları çevrimdışı mod desteği istiyor.
* Netflix 2016 Q3 shareş konferansında, içerik indirme "oft-istenen bir özelliktir" ve "açık olan", Netflix CEO Hastings tarafından söylenen
* Bazı içerik sağlayıcıları, bir ülke/bölge kenarlığının ötesinde DRM lisans teslimine izin verebilir. Bir kullanıcının kuruluşunun seyahat yapması ve yine de içerik izlemek istiyorsa, çevrimdışı indirme gerekir.
 
Çevrimdışı modu uygulamamız zor olan zorluk aşağıda verilmiştir:

* MP4 birçok oyuncu, kodlayıcı araçları tarafından desteklenir, ancak MP4 kapsayıcısı ile DRM arasında bağlama yoktur;
* Uzun dönemde CENC ile CFF, gitmenin yoludur. Ancak, günümüzde araçlar/yürütücü ekosistemi henüz desteklemez. Bugün bir çözüme ihtiyacımız var.
 
Fikir: H264/AAC ile kesintisiz akış ([pff](/iis/media/smooth-streaming/protected-interoperable-file-format)) dosya biçimi PLAYREADY (AES-128 Mrk) ile bir bağlamaya sahiptir. Tek bir kesintisiz akış. IMV dosyası (videonun videoda zaman içinde olduğu varsayılarak), bir fMP4 ve kayıttan yürütme için kullanılabilir. Kesintisiz bir akış içeriği PlayReady şifrelemesi aracılığıyla gelirse her. ismv dosyası PlayReady korumalı parçalanmış bir MP4 haline gelir. Tercih edilen bit hızında bir. ISMV dosyası seçebiliriz ve bunu indirmek üzere. mp4 olarak yeniden adlandırabilirsiniz.

Aşamalı indirme için PlayReady korumalı MP4 barındırmak için iki seçenek vardır:

* Bunlardan biri, bu MP4 aynı kapsayıcı/medya hizmeti varlığına yerleştirebilir ve aşamalı indirme için Azure Media Services akış uç noktasından yararlanabilir;
* Bir tane, doğrudan Azure Storage 'dan aşamalı indirme için SAS Konumlandırıcı 'yı kullanabilir, Azure Media Services atlayarak.
 
İki tür PlayReady lisans teslimi kullanabilirsiniz:

* Azure Media Services 'de PlayReady lisans teslim hizmeti;
* Her yerde barındırılan PlayReady lisans sunucuları.

Aşağıda, birinci bir Azure VM 'de barındırılan PlayReady lisans sunucusu kullanılırken AMS 'de PlayReady lisans teslimini kullanan iki test varlığı kümesi verilmiştir:

## <a name="asset-1"></a>Varlık #1

* Aşamalı indirme URL 'SI: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (AMS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/`

## <a name="asset-2"></a>Varlık #2

* Aşamalı indirme URL 'SI: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (Şirket içi): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx`

Kayıttan yürütme testi için Windows 10 ' da bir Evrensel Windows uygulaması kullandık. [Windows 10 Universal örneklerinde](https://github.com/Microsoft/Windows-universal-samples), [uyarlamalı akış örneği](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming)adında bir temel oynatıcı örneği vardır. Her şey, indirilen videoyu seçmek ve bunu uyarlamalı akış kaynağı yerine kaynak olarak kullanmak üzere bizimle ilgili kod eklemektir. Değişiklikler düğme Click olay işleyicisi:

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

![PlayReady Protected fMP4 çevrimdışı modda kayıttan yürütme](./media/offline-playready-for-windows/offline-playready1.jpg)

Video PlayReady koruması altında olduğundan, ekran görüntüsü videoyu dahil edemeyecektir.

Özet bölümünde, Azure Media Services çevrimdışı modu elde ediyoruz:

* İçerik dönüştürme ve PlayReady şifrelemesi, Azure Media Services veya diğer araçlarda yapılabilir;
* İçerik, aşamalı indirme için Azure Media Services veya Azure Storage 'da barındırılabilir;
* PlayReady lisans teslimi Azure Media Services veya başka bir yerde olabilir;
* Hazırlanan kesintisiz akış içeriği, DRM olarak PlayReady ile DASH veya pürüzsüz aracılığıyla çevrimiçi akış için de kullanılabilir.
