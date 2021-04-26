---
title: Sürekli video kaydı-Azure
description: Sürekli video kaydı (CVR), videoyu bir video kaynağından sürekli kaydetme sürecini ifade eder. Bu konu, CVR 'nin ne olduğunu anlatmaktadır.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: f8b25d74db279f8cfff68f08efeab2975484a0c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453759"
---
# <a name="continuous-video-recording"></a>Sürekli video kaydı  

## <a name="suggested-pre-reading"></a>Önerilen önceden okuma  

* [Medya grafiği kavramı](media-graph-concept.md)
* [Video kaydetme kavramı](video-recording-concept.md)

## <a name="overview"></a>Genel Bakış

Sürekli video kaydı (CVR), videoyu bir video kaynağından sürekli kaydetme sürecini ifade eder. IoT Edge canlı video analizi, bir RTSP kaynak düğümünden ve bir varlık havuzu düğümünden oluşan bir [medya Graf](media-graph-concept.md) aracılığıyla bir CCTV kamerasından videoyu sürekli olarak 7/24 bir şekilde kaydetmeyi destekler. Aşağıdaki diyagramda böyle bir medya grafiğinin grafik temsili gösterilmektedir. Bu tür bir medya grafiğinin [grafik TOPOLOJISININ](media-graph-concept.md#media-graph-topologies-and-instances) JSON temsili [burada](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset)bulunabilir.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="Sürekli video kaydı":::

Yukarıda gösterilen medya grafiği bir uç cihazda çalıştırılabilir [ve varlık havuzu bir Azure Media Services varlığına](terminology.md#asset)video kaydı yapıyor. Medya grafiği etkinleştirilmiş durumda olduğu sürece video kaydedilir. Video bir varlık olarak kaydedildiğinden, Media Services mevcut akış özellikleri kullanılarak kayıttan çalınabilir. Daha fazla ayrıntı için bkz. [kaydedilen Içeriği kayıttan yürütme](video-playback-concept.md) .

## <a name="resilient-recording"></a>Dayanıklı kayıt

IoT Edge üzerindeki canlı video analizi, sınır cihazının zaman zaman bulut bağlantısı kaybetmesine veya kullanılabilir bant genişliğinden bir bırakma deneyime neden olabileceği, daha az, mükemmel olmayan ağ koşulları altında işletim sistemlerini destekler. Bu hesabın hesabında, kaynaktan alınan video yerel olarak bir önbellekte kaydedilir ve belirli aralıklarla otomatik olarak varlıkla eşitlenir. [Grafik TOPOLOJISI JSON](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json)'u incelerseniz, aşağıdaki özelliklerin tanımlandığını görürsünüz:

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```

İkinci iki özellik dayanıklı kayıt ile ilgilidir (her ikisi de bir varlık havuz düğümü için gereken özelliklerdir). LocalMediaCachePath özelliği, varlık havuzuna, kıymete yüklemeden önce medya verilerini önbelleğe almak için bu klasör yolunu kullanmasını söyler. Edge modülünün cihazınızın yerel depolama alanını nasıl kullanabileceğini anlamak için [Bu](../../iot-edge/how-to-access-host-storage-from-module.md) makaleye bakabilirsiniz. LocalMediaCacheMaximumSizeMiB özelliği, varlık havuzunun ne kadar disk alanının önbellek olarak kullanılabileceğini tanımlar (1 MIB = 1024 * 1024 bayt). 

Edge modülünüzün bağlantısı çok uzun bir süredir kaybolursa ve önbellek klasöründe depolanan içerik localMediaCacheMaximumSizeMiB değerine ulaşırsa, varlık havuzu en eski verilerden başlayarak önbellekten veri atmaya başlayacaktır. Örneğin, cihaz 10:00 ' da bağlantı kesildiğinde ve önbellek, 00 ' da maksimum sınıra rastlısa, varlık havuzu 10:00 ' da kaydedilen verileri silmeye başlar. 

Ağ bağlantısı geri yüklendiğinde, varlık havuzu önbellekten karşıya yüklenmeye başlayacaktır, en eski verilerden başlayarak yeniden başlatılır. Yukarıdaki örnekte, 5 dakikalık bir videonun, bağlantının geri yüklendiği zaman önbellekte atılmak zorunda olduğunu varsayalım (6:02PM), ardından varlık havuzu 10:05PM işaretinden karşıya yüklenmeye başlayacaktır.

Varlığı daha sonra [Bu](playback-recordings-how-to.md) API 'leri kullanarak incelerseniz, varlık yaklaşık 10 ila 10:05:00:00 arasında bir boşluk olduğunu görürsünüz.

## <a name="segmented-recording"></a>Kesimli kayıt  

Yukarıda açıklanan şekilde, varlık havuzu düğümü videoyu yerel bir önbelleğe kaydeder ve videoyu buluta düzenli olarak yükler. SegmentLength özelliği (yukarıdaki bölümde gösterilmiştir), varlığın kaydedildiği depolama hesabınıza veri yazma ile ilişkili yazma işlemleri maliyetini denetlemenize yardımcı olur. Örneğin, karşıya yükleme süresini 30 saniyeden 5 dakikaya artırırsanız, depolama işlemlerinin sayısı 10 faktörüyle (5 * 60/30) bırakılır.

SegmentLength özelliği, sınır modülünün videoyu en çok her segmentLength saniyede karşıya yüklemesine de sağlar. Bu özellik en az 30 saniyelik bir değere sahiptir (Ayrıca varsayılan) ve en fazla 5 dakikalık artışlarla 30 saniyelik artış artırılabilir.

> [!NOTE]
> SegmentLength 'ın kayıttan yürütme üzerindeki etkisi için [kayıttan yürütme kayıtları](playback-recordings-how-to.md) makalesine bakın.

## <a name="see-also"></a>Ayrıca bkz.

* [Olay tabanlı video kaydı](event-based-video-recording-concept.md)
* [Kaydedilen içeriğin kayıttan yürütülmesi](video-playback-concept.md)

## <a name="next-steps"></a>Sonraki adımlar

[Öğretici: sürekli video kaydı](continuous-video-recording-tutorial.md)
