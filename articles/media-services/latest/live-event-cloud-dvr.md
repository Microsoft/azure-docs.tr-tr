---
title: Azure Media Services saat değiştirme ve canlı-VOD kullanma (isteğe bağlı video) | Microsoft Docs
description: Bu makalede, canlı çıktının ne olduğu ve bulut DVR 'ın nasıl kullanılacağı açıklanmaktadır.
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
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: ffcd279830cb49b64ddbb58a888ad7d653918b1b
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338835"
---
# <a name="using-time-shifting-and-live-to-vod-video-on-demand"></a>Zaman kaydırma ve canlı-VOD kullanma (isteğe bağlı video)

Azure Media Services, [canlı çıkış](https://docs.microsoft.com/rest/api/media/liveoutputs) nesnesi canlı akışınızı yakalayıp Media Services hesabınızdaki bir varlığa kaydeden dijital video kaydedicisi gibidir. Kaydedilen içerik, [varlık](https://docs.microsoft.com/rest/api/media/assets) kaynağı tarafından tanımlanan kapsayıcıda kalıcı hale getirilir (kapsayıcı, hesabınıza bağlı olan Azure Storage hesabıdır). Canlı çıktı Ayrıca, akışın ne kadarının arşiv kaydında (örneğin, bulut DVR 'ın kapasitesi) tutulması ve görüntüleyicilerin canlı akışı izlemeye başlayabileceği gibi, giden canlı akışın bazı özelliklerini denetlemenize olanak tanır. Diskteki arşiv, yalnızca canlı çıktının **archiveWindowLength** özelliğinde belirtilen içerik miktarını tutan bir dairesel arşiv "penceresidir". Bu pencerenin dışında kalan içerikler, depolama kapsayıcısından otomatik olarak atılır ve kurtarılamaz. ArchiveWindowLength değeri, DVR 'ın kapasitesini belirten ve en az 3 dakikadan en fazla 25 saate ayarlanabilir bir ISO-8601 TimeSpan süresi (örneğin, PTHH: MM: SS) temsil eder.

Canlı bir olay ile canlı çıkışları arasındaki ilişki, bir kanalın (canlı olay) sabit bir video akışını ve bir kaydın (canlı çıkış) belirli bir zaman segmentine (örneğin akşam) dahil olduğu geleneksel televizyon yayınına benzer. 6:12:00 ' dan haberleri: 00PM). Akışın canlı olayına akışını tamamladıktan sonra bir varlık, canlı çıkış ve akış Bulucu oluşturarak akış olayını başlatabilirsiniz. Canlı çıktı akışı Arşivle ve [akış uç noktası](https://docs.microsoft.com/rest/api/media/streamingendpoints)aracılığıyla görüntüleyiciler için kullanılabilir hale getirir. Farklı arşiv uzunlukları ve ayarları ile canlı bir olayda birden fazla canlı çıkış (en fazla üç maksimum) oluşturabilirsiniz. Canlı akış iş akışı hakkında daha fazla bilgi için, [genel adımlar](live-streaming-overview.md#general-steps) bölümüne bakın.

## <a name="using-a-dvr-during-an-event"></a>Bir olay sırasında DVR kullanma 

Bu bölümde, ' geri sarma ' için akışın hangi bölümlerinin kullanılabileceğini denetlemek üzere bir olay sırasında bir DVR 'ın nasıl kullanılacağı açıklanmaktadır.

ArchiveWindowLength değeri, bir görüntüleyicinin geçerli canlı konumdan ne zaman geri gidebileceğini belirler. ArchiveWindowLength değeri, istemci bildirimlerinin ne kadar büyüyeceğini de belirler.

Futbol oyunu akışını ve yalnızca 30 dakikalık bir ArchiveWindowLength olduğunu varsayalım. Oyun başladıktan sonra olayınızı 45 dakika izlemeye başlayan bir görüntüleyici, 15 dakikalık bir işaret üzerinden geri arama yapabilir. Canlı olay durduruluncaya kadar oyun için canlı çıkışlarınız devam eder, ancak archiveWindowLength dışında kalan içerikler sürekli olarak depolamadan atılır ve kurtarılabilir değildir. Bu örnekte, olayın başlangıcı ve 15 dakikalık işaret arasındaki video, DVR 'ınızdan ve varlık için BLOB depolama alanındaki kapsayıcıdan temizlenir. Arşiv kurtarılabilir değildir ve Azure Blob depolama alanındaki kapsayıcıdan kaldırılır.

Canlı bir olay, en fazla üç eşzamanlı çalışan canlı çıkışı destekler (aynı anda bir Canlı akıştan en fazla 3 kayıt/arşiv oluşturabilirsiniz). Bu özellik, gerektiğinde bir olayın farklı kısımlarını yayımlamanıza ve arşivlemenize olanak tanır. 7/24 canlı bir doğrusal akış yayınlamalı ve müşterilere, yakalama için isteğe bağlı içerik olarak sunmak üzere gün boyunca farklı programların "kayıtlarını" oluşturmanız gerektiğini varsayalım. Bu senaryoda, ilk olarak 1 saat veya daha az bir arşiv penceresi ile bir birincil canlı çıktı oluşturursunuz; bu, görüntüleyicilerinizin ayarlayacağından asıl canlı akışdır. Bu canlı çıktı için bir akış Bulucu oluşturur ve bunu uygulamanızda veya Web sitenizde "canlı" akış olarak yayımlayabilirsiniz. Canlı olay çalışırken programın başlangıcında (veya daha sonra kırpmak için bazı tutamaçlar sağlamak için 5 dakika erken), programlı olarak ikinci bir eşzamanlı canlı çıktı oluşturabilirsiniz. Bu ikinci canlı çıktı, program bittikten 5 dakika sonra silinebilir. Bu ikinci varlıkla, bu programı uygulamanızın kataloğunda isteğe bağlı bir varlık olarak yayımlamak üzere yeni bir akış Bulucu oluşturabilirsiniz. Bu işlemi, isteğe bağlı videolar olarak paylaşmak istediğiniz diğer program sınırları veya vurguları için birden çok kez yineleyebilirsiniz, ancak ilk canlı çıktının "canlı" akışı, doğrusal akışın yayınlanmaya devam eder. 

## <a name="creating-an-archive-for-on-demand-playback"></a>İsteğe bağlı kayıttan yürütme için Arşiv oluşturma

Canlı çıktının Arşivlenmesi gereken varlık, canlı çıktı silindiğinde otomatik olarak isteğe bağlı bir varlık haline gelir. Canlı bir olay durdurulmadan önce tüm canlı çıktıları silmeniz gerekir. Durdurulduğunda canlı çıktıları otomatik olarak kaldırmak için, bir [Removeoutputsonstop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) isteğe bağlı bayrağını kullanabilirsiniz. 

Olayı durdurup sildikten sonra bile, varlığı silmemiş olduğu sürece kullanıcılar arşivlenmiş içeriğinizi isteğe bağlı bir video olarak akışla verebilir. Bir olay tarafından kullanılıyorsa bir varlık silinmemelidir; önce olayın silinmesi gerekir.

Canlı çıkışınızın varlığını bir akış Bulucuyu kullanarak yayımladıysanız, canlı olay (DVR pencere uzunluğuna kadar), akış bulucunun bitiş tarihi veya silme işlemi, hangisi önce geldiği sürece görüntülenmeye devam eder.

Daha fazla bilgi için bkz.

- [Canlı akışa genel bakış](live-streaming-overview.md)
- [Canlı akış öğreticisi](stream-live-tutorial-with-api.md)

> [!NOTE]
> Canlı çıktıyı sildiğinizde, ilgili varlık ve varlığın içeriğini silmezsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

* [Videolarınızın alt klibini yapın](subclip-video-rest-howto.md).
* [Varlıklarınız için filtreleri tanımlayın](filters-dynamic-manifest-rest-howto.md).
