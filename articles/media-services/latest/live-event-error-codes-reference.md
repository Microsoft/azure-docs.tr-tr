---
title: Azure Media Services canlı olay hata kodları
description: Bu makalede, canlı olay hata kodları listelenmektedir.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 09859a953b0127733cbf1b0876c1d2ffa6082096
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279485"
---
# <a name="media-services-live-event-error-codes"></a>Media Services canlı olay hata kodları

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Aşağıdaki tablolarda [canlı olay](live-event-outputs-concept.md) hata kodları listelenmektedir.

## <a name="liveeventconnectionrejected"></a>Liveeventconnectionreddedildi

Canlı bir olay için [Event Grid](../../event-grid/index.yml) olaylarına abone olduğunuzda, [Liveeventconnectionreddedildi](monitoring/media-services-event-schemas.md\#liveeventconnectionrejected) olayından aşağıdaki hatalardan birini görebilirsiniz.
> [!div class="mx-tdCol2BreakAll"]
>| Hata | Bilgi |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|Description | Hatalı alma URL 'SI |
>|Önerilen çözüm| APPıD, RTMP alma URL 'sindeki bir GUID belirtecidir. API 'nin alma URL 'siyle eşleştiğinden emin olun. |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| Description |Yapılandırılan IP izin verilenler listesinde kodlayıcı IP 'si yok |
>| Önerilen çözüm| Kodlayıcı IP Izin verilenler listesinde olduğundan emin olun. Doğru değeri ayarlamak için *whoismyıp* veya *CIDR Hesaplayıcı* gibi çevrimiçi bir araç kullanın.  Kodlayıcının gerçek canlı olaydan önce sunucuya ulaşabildiğinizden emin olun. |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| Description|RTMP Kodlayıcısı `setDataFrame` komutu göndermedi. |
>| Önerilen çözüm|Çoğu ticari kodlayıcılar akış meta verileri gönderir. Tek bit hızı alma işlemi yapan bir kodlayıcı için bu sorun olmayabilir. Akış meta verileri eksik olduğunda LiveEvent gelen bit hızını hesaplayabilir.  Geçiş kanalı veya Double Push senaryosu için çoklu bit hızı alma senaryosunda, alma URL 'sinde sorgu dizesini ' videopaudte ' ve ' audiodatarate ' ile eklemeyi deneyebilirsiniz. Yaklaşık değer işe başlayabilir. Birim kbit. Örneğin,  `rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| Description|Belirtilen codec desteklenmez.|
>| Önerilen çözüm| LiveEvent desteklenmeyen codec aldı. Örneğin, bir RTMP alma, LiveEvent, AVC olmayan video codec 'i aldı.  Kodlayıcı ön ayarını denetleyin. |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| Description |Medya açıklaması bilgileri, gerçek medya verileri teslim edilmeden önce alınmadı. |
>| Önerilen çözüm|LiveEvent, kodlayıcıdan Stream açıklaması (header veya FLV etiketi) almaz. Bu bir protokol ihlalidir. Kodlayıcı satıcısına başvurun. |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| Description|Ses veya video türü için kaliteleri sayısı izin verilen üst sınırı aşıyor. |
>| Önerilen çözüm|Canlı olay modu Live Encoding, kodlayıcının tek bir bit hızını video ve ses olarak iletmesi gerekir.  Aynı bit hızında yedekli bir gönderim yapılmasına izin verildiğini unutmayın. Tek bit hızlı bir akış çıktısı içermediğinden emin olmak için Kodlayıcı ön ayarını veya çıkış ayarlarını kontrol edin. |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| Description|Canlı bir olay veya kanal hizmetindeki toplam gelen bit hızı izin verilen maksimum sınırı aştı. |
>| Önerilen çözüm|Kodlayıcı, en yüksek gelen bit hızını aştı. Bu sınır, katkıda bulunan kodlayıcıdan gelen tüm verileri toplar. Bit hızı azaltmak için Kodlayıcı ön ayarını veya çıkış ayarlarını kontrol edin. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| Description|Video veya ses FLVTag zaman damgası, RTMP kodlayıcısından geçersizdir. |
>| Önerilen çözüm|Kullanım dışı. |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| Description|Çerçeve ücretleri olan gelen kodlayıcı alınan akışlar, canlı olayları/kanalları kodlamak için izin verilen en yüksek 30 fps sayısını aştı. |
>| Önerilen çözüm|36 fps altında kare hızına düşürmek için Kodlayıcı ön ayarını denetleyin. |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| Description|Gelen kodlayıcı alınan akışlar, şu izin verilen çözünürlükleri aştı: 1920x1088 for the Live Events/Channels ve 4096 x 2160 for pass-through canlı olaylar/kanallar. |
>| Önerilen çözüm|Video çözünürlüğünü azaltmak için Kodlayıcı ön ayarını kontrol edin, böylece sınır aşılamaz. |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| Description|Canlı olay, bir kerede büyük miktarda ses verisi veya anahtar çerçevesi olmayan büyük miktarda video verisi aldı. Doğru verilerle yeniden denenme şansı vermek için kodlayıcının bağlantısını kestik. |
>| Önerilen çözüm|Kodlayıcının her anahtar çerçeve aralığı (GOP) için bir anahtar çerçeve gönderdiğinden emin olun.  "Sabit bit hızı (CBR)" veya "anahtar çerçevelerini Hizala" gibi ayarları etkinleştirin. Bazen, katkıda bulunan kodlayıcının sıfırlanması yardımcı olabilir. Yardım yoksa, kodlayıcı satıcısına başvurun. |

## <a name="liveeventencoderdisconnected"></a>Liveeventencoderconnected bağlantısı kesildi

[Liveeventencoderconnected](monitoring/media-services-event-schemas.md\#liveeventencoderdisconnected) olayında aşağıdaki hatalardan birini görebilirsiniz.

> [!div class="mx-tdCol2BreakAll"]
>| Hata | Bilgi |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| Description|RTMP oturumu izin verilen süre sınırı boyunca boşta kaldıktan sonra zaman aşımına uğradı. |
>|Önerilen çözüm|Bu durum genellikle, bir kodlayıcı giriş akışını almayı sonlandırdığı zaman meydana gelir. böylece, gönderilecek veri yok. Kodlayıcı veya giriş akışı durumunun sağlıklı durumda olup olmadığını denetleyin. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|Description| Video veya ses FLVTag zaman damgası, RTMP kodlayıcısından geçersizdir. |
>| Önerilen çözüm| Kullanım dışı. |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| Description|Kodlayıcı verileri çok hızlı gönderiyor. |
>| Önerilen çözüm|Bu, kodlayıcı kısa bir süre içinde büyük bir parça kümesi patlayamadığında oluşur.  Bu, kodlayıcı bir ağ sorunundan kaynaklanan verileri veri gönderemeden ve ağ kullanılabilir olduğunda verileri dışarı gönderemeden bu da teorik olarak gerçekleşir. Kodlayıcı günlüğünden veya sistem günlüğünden nedeni bulun. |
>|**Bilinmeyen hata kodları** |
>| Description| Bu hata kodları, karma eşlemesindeki girdileri yinelemek için bellek hatasından değişebilir. Bu, kodlayıcı kısa bir dönemde büyük bir parçalar kümesi gönderdiğinde meydana gelebilir.  Bu durum, kodlayıcı bir ağ sorunundan dolayı sırasında verileri gönderemediği zaman da gerçekleşebilir ve ağ kullanılabilir hale geldiğinde tüm gecikmeli parçaları bir kerede gönderir. |
>|Önerilen çözüm| Kodlayıcı günlüklerini denetleyin.|

## <a name="other-error-codes"></a>Diğer hata kodları

> [!div class="mx-tdCol2BreakAll"]
>| Hata | Bilgi |Reddedilen/bağlantısı kesilen olay|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||Yes|
>| Description|Bu genel bir hatadır. ||
>|Önerilen çözüm| Yok.||
>|**MPI_SYSTEM_MAINTENANCE** ||Yes|
>| Description|Kodlayıcı, hizmet güncelleştirmesi veya sistem bakımı nedeniyle kesildi. ||
>|Önerilen çözüm|Kodlayıcının ' Auto Connect ' sağladığından emin olun. Kodlayıcının bakımda olmayan canlı etkinlik uç noktasına yeniden bağlanmasına olanak sağlar. ||
>|**MPE_BAD_URL_SYNTAX** ||Yes|
>| Description|Alma URL 'SI yanlış biçimlendirilmiş. ||
>|Önerilen çözüm|Alma URL 'sinin doğru biçimlendirildiğinden emin olun. RTMP için `rtmp[s]://hostname:port/live/GUID_APPID/streamname` ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||Yes|
>| Description|Kodlayıcı oturumu kesti.  ||
>|Önerilen çözüm|Bu hata değil. Kodlayıcı, düzgün bağlantı kesilmesi da dahil olmak üzere bağlantı kesmeyi başlattı. Bu beklenmeyen bir bağlantı kesildiğinde kodlayıcı günlüklerine bakın. |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||Hayır|
>| Açıklama|Gelen veri hızı beklenen bit hızına uymuyor. ||
>|Önerilen çözüm|Bu, gelen veri hızı çok yavaş veya hızlı olduğunda gerçekleşen bir uyarıdır. Kodlayıcı günlüğünü veya sistem günlüğünü kontrol edin.||
>|**MPE_INGEST_DISCONTINUITY** ||Hayır|
>| Açıklama| Gelen verilerde sürekli olarak bir sorun var.||
>|Önerilen çözüm| Bu, bir ağ sorunu veya bir sistem kaynağı sorunu nedeniyle kodlayıcının verileri bıraktığı bir uyarıdır. Kodlayıcı günlüğünü veya sistem günlüğünü kontrol edin. Sistem kaynağını (CPU, bellek veya ağ) de izleyin. Sistem CPU 'SU çok yüksekse, bit hızı azaltmayı deneyin veya sistem grafik kartından H/W kodlayıcı seçeneğini kullanın.||

## <a name="see-also"></a>Ayrıca bkz.

[Akış uç noktası (başlangıç) hata kodları](stream-streaming-endpoint-error-codes-reference.md)

## <a name="next-steps"></a>Sonraki adımlar

[Öğretici: Media Services ile canlı akış](stream-live-tutorial-with-api.md)
