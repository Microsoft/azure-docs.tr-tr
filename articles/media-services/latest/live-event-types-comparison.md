---
title: Azure Media Services LiveEvent Types | Microsoft Docs
description: Bu makalede, LiveEvent türlerini karşılaştıran ayrıntılı bir tablo gösterilir.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 884cf8d913cec038df3b38c8af2ed0a67bd8060d
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802235"
---
# <a name="live-event-types-comparison"></a>Canlı olay türleri karşılaştırması

Azure Media Services, canlı bir [olay](https://docs.microsoft.com/rest/api/media/liveevents) iki türden biri olabilir: canlı kodlama ve doğrudan geçiş. 

## <a name="types-comparison"></a>Tür karşılaştırması 

Aşağıdaki tablo, canlı olay türlerinin özelliklerini karşılaştırır. Türler, [Liveeventencodingtype](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)kullanılarak oluşturma sırasında ayarlanır:

* **Liveeventencodingtype. None** -şirket içi bir Live Encoder çoklu bit hızı akışı gönderir. Alınan akışlar, daha fazla işlem yapılmadan canlı olaydan geçer. 
* **Liveeventencodingtype. Standard** -şirket içi bir Live Encoder canlı olaya tek bit hızlı bir akış gönderir ve Media Services çoklu bit hızı akışları oluşturur. Katkı akışı 720p veya daha yüksek çözünürlükte ise, **Default720p** önayar bir dizi 6 çözünürlük/bit hızı çifti kodlayacaktır (Ayrıntılar makalede daha sonra takip edilir).
* **Liveeventencodingtype. Premium1080p** -şirket içi bir Live Encoder canlı olaya tek bit hızlı bir akış gönderir ve Media Services çoklu bit hızı akışları oluşturur. Default1080p önayar, çözümleme/bit hızı çiftleri çıkış kümesini belirtir (Ayrıntılar makalede daha sonra takip edilecek). 

| Özellik | Geçişli canlı etkinlik | Standart veya Premium1080p canlı etkinliği |
| --- | --- | --- |
| Tek bit hızı girişi, bulutta birden çok bit hızında kodlanır |Hayır |Evet |
| Katkı akışı için maksimum video çözünürlüğü |4K (4096x2160/60 kare/sn) |1080p (1920x1088 üzerinde 30 kare/sn)|
| Katkı akışında önerilen maksimum katman sayısı|12 ' ye kadar|Bir ses|
| Çıktıda maksimum katman sayısı| Giriş ile aynı|6 ' ya kadar (aşağıdaki sistem önayarlarına bakın)|
| Katkı akışı en fazla toplam bant genişliği|60 Mbps|Yok|
| Katkıdaki tek bir katman için maksimum bit hızı |20 Mbps|20 Mbps|
| Birden çok dil sesi parçası desteği|Evet|Hayır|
| Desteklenen giriş video codec bileşenleri |H., ve AVC ve H. 265/HEVC|H. BIR/DAHA FAZLA/AVC|
| Desteklenen çıkış video codec bileşenleri|Giriş ile aynı|H. BIR/DAHA FAZLA/AVC|
| Desteklenen video bit derinliği, giriş ve çıkış|HDR 10/HLG dahil olmak üzere en fazla 10 bit|8 bit|
| Desteklenen giriş sesi codec bileşenleri|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Desteklenen çıkış ses codec bileşenleri|Giriş ile aynı|AAC-LC|
| Çıkış videosunun maksimum video çözünürlüğü|Giriş ile aynı|Standart-720p, Premium1080p-1080p|
| Giriş videosunun maksimum kare oranı|60 çerçeve/saniye|Standart veya Premium1080p-30 kare/saniye|
| Giriş protokolleri|RTMP, parçalanmış-MP4 (Kesintisiz Akış)|RTMP, parçalanmış-MP4 (Kesintisiz Akış)|
| Fiyat|[Fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/media-services/) bakın ve "canlı video" sekmesine tıklayın|[Fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/media-services/) bakın ve "canlı video" sekmesine tıklayın|
| En fazla çalışma süresi| 24 saat x 365 gün, canlı doğrusal | 24 saat x 365 gün, canlı doğrusal (Önizleme)|
| Gömülü CEA 608/708 açıklamalı alt yazı verilerini geçirebilme|Evet|Evet|
| SLA ekleme desteği|Hayır|Hayır|
| API aracılığıyla ad sinyali için destek| Hayır|Hayır|
| Yerleşik olarak SCTE-35 iletileri aracılığıyla ad sinyali desteği|Evet|Evet|
| Katkı akışındaki kısa yedeklerden kurtarma olanağı|Evet|Kısmi|
| Tekdüzen olmayan giriş GOPs desteği|Evet|Hayır – girişte sabit GOP süresi olmalıdır|
| Değişken çerçeve hızı girişi desteği|Evet|Hayır – giriş sabit kare oranı olmalıdır. Küçük çeşitlemeler, örneğin, yüksek hareket sahneleri sırasında toleranslı olarak dağıtılır. Ancak katkı akışı kare hızını (örneğin, 15 kare/saniye) bırakamıyor.|
| Giriş akışı kaybolduğunda canlı etkinliğin otomatik olarak kaybolması|Hayır|12 saat sonra, çalışan bir canlı çıkış yoksa|

## <a name="system-presets"></a>Sistem önayarları

Live Encoder 'daki çıktıda bulunan çözünürlükler ve bitoranlar, [ön kümeleyici](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding)tarafından belirlenir. **Standart** bir Live Encoder (LiveEventEncodingType. Standard) kullanılıyorsa, *Default720p* önayarı aşağıda açıklanan 6 çözünürlük/bit hızı çiftinin bir kümesini belirtir. Aksi halde, bir **Premium1080p** Live Encoder (LiveEventEncodingType. Premium1080p) kullanılıyorsa, *Default1080p* önayar, çözümleme/bit hızı çiftlerinin çıkış kümesini belirtir.

> [!NOTE]
> Standart canlı kodlama için kurulum yaptıysanız, Default1080p ön ayarını canlı bir olaya uygulayamazsınız. bir hata alırsınız. Ayrıca, Premium1080p Live Encoder 'a Default720p ön ayarını uygulamaya çalıştığınızda da bir hata alacaksınız.

### <a name="output-video-streams-for-default720p"></a>Default720p için çıkış video akışları

Katkı akışı 720p veya daha yüksek çözünürlükte ise, **Default720p** ön sürümü akışı aşağıdaki 6 katmana kodlayacaktır. Aşağıdaki tabloda, bit hızı KB/sn 'dir, MaxFPS izin verilen maksimum kare hızını (çerçeveler/saniye cinsinden) temsil eder. profil, kullanılan H. lenebilir profilini temsil eder.

| Bit hızı | Genişlik | Yükseklik | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Yüksek |
| 2200 |960 |540 |30 |Yüksek |
| 1350 |704 |396 |30 |Yüksek |
| 850 |512 |288 |30 |Yüksek |
| 550 |384 |216 |30 |Yüksek |
| 200 |340 |192 |30 |Yüksek |

> [!NOTE]
> Canlı kodlama ön ayarını özelleştirmeniz gerekiyorsa lütfen Azure Portal aracılığıyla bir destek bileti açın. İstediğiniz çözünürlüklerin ve bit hızlarının yer aldığı tabloyu paylaşmanız gerekir. 720p çözünürlükte yalnızca bir katman ve toplamda en fazla 6 katman bulunduğundan emin olun. Ayrıca, standart bir Live Encoder için önceden ayarlanmış bir kod isteğinde bulunduğunuzu da belirtin.
> Bitoranlar ve çözümlerin belirli değerleri zaman içinde ayarlanabilir

### <a name="output-video-streams-for-default1080p"></a>Default1080p için çıkış video akışları

Katkı akışı 1080p çözünürlükte ise, **Default1080p** ön sürümü akışı aşağıdaki 6 katmana kodlayabilir.

| Bit hızı | Genişlik | Yükseklik | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Yüksek |
| 3000 |1280 |720 |30 |Yüksek |
| 1600 |960 |540 |30 |Yüksek |
| 800 |640 |360 |30 |Yüksek |
| 400 |480 |270 |30 |Yüksek |
| 200 |320 |180 |30 |Yüksek |

> [!NOTE]
> Canlı kodlama ön ayarını özelleştirmeniz gerekiyorsa lütfen Azure Portal aracılığıyla bir destek bileti açın. İstediğiniz çözünürlüklerin ve bit hızlarının yer aldığı tabloyu paylaşmanız gerekir. 1080p ve en fazla 6 katmanda yalnızca bir katman olduğunu doğrulayın. Ayrıca, Premium1080p Live Encoder için önceden ayarlanmış bir kod isteğinde bulunduğunuzu da belirtin.
> Bitücretler ve çözümlerin belirli değerleri zaman içinde ayarlanabilir.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Default720p ve Default1080p için çıkış ses akışı

Hem *Default720p* hem de *Default1080p* önayarları için ses 128 kbps 'de stereo AAC-LC olarak kodlanır. Örnekleme hızı, katkı akışındaki ses izlemesının izler.

## <a name="implicit-properties-of-the-live-encoder"></a>Live Encoder 'ın örtük özellikleri

Önceki bölümde, açık bir şekilde denetlenebileceği, katman sayısı, çözünürlükler ve bit hızları gibi önceden ayarlanmış olarak, canlı kodlayıcının özellikleri açıklanmaktadır. Bu bölüm örtük özellikleri açıklığa kavuşturulur.

### <a name="group-of-pictures-gop-duration"></a>Resim grubu (GOP) süresi

Live Encoder, katkı akışı 'nın [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) yapısını izler. Bu, çıkış katmanlarının aynı GOP süresine sahip olacağı anlamına gelir. Bu nedenle, şirket içi kodlayıcı 'yı, sabit GOP süresi (genellikle 2 saniye) olan bir katkı akışı oluşturmak üzere yapılandırmanız önerilir. Bu, hizmetten giden HLS ve MPEG DASH akışlarının de sabit GOP sürelerine sahip olmasını sağlayacaktır. GOP sürelerinin küçük değişimlerinin çoğu cihaz tarafından toleranslı olma olasılığı yüksektir.

### <a name="frame-rate"></a>Kare hızı

Live Encoder Ayrıca katkı akışındaki tek video çerçevelerinin sürelerini izler. Bu, çıkış katmanlarının aynı süreleri kapsayan çerçevelere sahip olacağı anlamına gelir. Bu nedenle, şirket içi kodlayıcı 'yı sabit kare oranı (en fazla 30 kare/saniye) olan bir katkı akışı oluşturmak üzere yapılandırmanız önerilir. Bu, hizmetin giden HLS ve MPEG DASH akışlarının de sabit kare fiyatları sürelerine sahip olmasını güvence altına alır. Çerçeve ücretlerine ait küçük çeşitlemeler birçok cihaz tarafından toleranslı olabilir, ancak canlı kodlayıcının doğru şekilde oynatacak bir çıktı üretmesi garanti etmez. Şirket içi Live Encoder 'ın çerçeveleri bırakmamalıdır (örn. düşük pil koşullarında) veya kare hızını herhangi bir şekilde değiştirerek.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Katkı akışı ve çıkış katmanlarının çözümlenmesi

Canlı kodlayıcı, katkı akışını kullanmaktan kaçınmak için yapılandırılır. Sonuç olarak, çıkış katmanlarının en yüksek çözünürlüğü, katkı beslemesinden daha fazla olmaz.

Örneğin, Default1080p Live Encoding için yapılandırılmış canlı bir olaya 720p adresinde bir katkı akışı gönderirseniz, çıktı yalnızca 5 katmana sahip olur ve 3Mbps 'de 720p, 200 Kbps hızında en fazla 1080p 'e giderek çalışır. Ya da 360p 'de standart canlı kodlama için yapılandırılmış canlı bir olaya bir katkı akışı gönderirseniz, çıktı 3 katman (288p, 216p ve 192p çözünürlüğünde) içerecektir. Bozuk durumunda, standart bir Live Encoder için bir katkı akışı, örneğin, 160x90 piksel olarak gönderirseniz, çıkış, katkı akışındaki aynı bit hızında 16 0x90 çözünürlükte bir katman içerecektir.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Katkı akışı ve çıkış katmanlarının bit hızı

Live Encoder, katkı akışındaki bit hızına bakılmaksızın, ön ayarlı bit hızı ayarlarını kabul etmek üzere yapılandırılmıştır. Sonuç olarak, çıkış katmanlarının bit hızı, katkı beslemenin boyutunu aşabilir. Örneğin, 1 MB/sn 'de bir 720p çözünürlükte bir katkı akışında gönderirseniz, çıkış katmanları Yukarıdaki [tabloda](live-event-types-comparison.md#output-video-streams-for-default720p) olduğu gibi kalır.

## <a name="next-steps"></a>Sonraki adımlar

[Canlı akışa genel bakış](live-streaming-overview.md)
