---
title: İsteğe bağlı video kayıttan yürütmeyi oluşturmak için zaman kaydırma kullanma
description: Bu makalede, canlı akışları kaydetmek ve isteğe bağlı kayıttan yürütme oluşturmak için zaman değiştirme ve canlı çıkışların nasıl kullanılacağı açıklanır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: e98f45989e3ded1eb485c3f8f6eb95aa51592a9a
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278074"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>İsteğe bağlı video oynatma oluşturmak için zaman değiştirme ve canlı çıktıları kullanma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services, [canlı çıkış](/rest/api/media/liveoutputs) nesnesi canlı akışınızı yakalayıp Media Services hesabınızdaki bir varlığa kaydeden dijital video kaydedicisi gibidir. Kaydedilen içerik, [varlık](/rest/api/media/assets) kaynağı tarafından tanımlanan kapsayıcıda kalıcı hale getirilir (kapsayıcı, hesabınıza bağlı olan Azure Storage hesabıdır). Canlı çıktı ayrıca giden canlı akışın bazı özelliklerini denetlemenize olanak tanır (örneğin, bulut DVR 'ın kapasitesi gibi) veya görüntüleyiciler canlı akışı izlemeye başlayabileceği zaman. Diskteki arşiv, yalnızca canlı çıktının **archiveWindowLength** özelliğinde belirtilen içerik miktarını tutan bir dairesel arşiv "penceresidir". Bu pencerenin dışında kalan içerikler, depolama kapsayıcısından otomatik olarak atılır ve kurtarılamaz. ArchiveWindowLength değeri, DVR 'ın kapasitesini belirten bir ISO-8601 TimeSpan süresini (örneğin, PTHH: MM: SS) temsil eder. Değer en az bir dakika ile en fazla 25 saat arasında ayarlanabilir.

Canlı bir olay ve canlı çıkışları arasındaki ilişki geleneksel TV yayınına benzer, bu da bir kanalın (canlı olay) sabit bir video akışını ve bir kaydın (canlı çıktı) belirli bir zaman segmentine (örneğin, 6:12 ila 7:00PM) kapsamı vardır. Akışın canlı olayına akışını tamamladıktan sonra bir varlık, canlı çıkış ve akış Bulucu oluşturarak akış olayını başlatabilirsiniz. Canlı çıktı akışı Arşivle ve [akış uç noktası](/rest/api/media/streamingendpoints)aracılığıyla görüntüleyiciler için kullanılabilir hale getirir. Farklı arşiv uzunlukları ve ayarları ile canlı bir olayda birden fazla canlı çıkış (en fazla üç maksimum) oluşturabilirsiniz. Canlı akış iş akışı hakkında daha fazla bilgi için, [genel adımlar](stream-live-streaming-concept.md#general-steps) bölümüne bakın.

## <a name="using-a-dvr-during-an-event"></a>Bir olay sırasında DVR kullanma

Bu bölümde, ' geri sarma ' için akışın hangi bölümlerinin kullanılabileceğini denetlemek üzere bir olay sırasında bir DVR 'ın nasıl kullanılacağı açıklanmaktadır.

`archiveWindowLength`Değer, bir görüntüleyicinin geçerli canlı konumdan ne kadar geri gidebileceğini belirler. `archiveWindowLength`Değer Ayrıca, istemci bildirimlerinin ne kadar büyüyeceğini de belirler.

Bir futbol oyunu akışını ve yalnızca 30 dakikalık bir oyun olduğunu varsayalım `ArchiveWindowLength` . Oyun başladıktan sonra olayınızı 45 dakika izlemeye başlayan bir görüntüleyici, 15 dakikalık bir işaret üzerinden geri arama yapabilir. Canlı olay durduruluncaya kadar oyunun canlı çıktıları devam edecektir. ArchiveWindowLength dışında kalan içerikler sürekli olarak depolamadan atılır ve kurtarılamaz olur. Bu örnekte, olayın başlangıcı ve 15 dakikalık işaret arasındaki video, DVR 'ınızdan ve varlık için BLOB depolama alanındaki kapsayıcıdan temizlenir. Arşiv kurtarılabilir değildir ve Azure Blob depolama alanındaki kapsayıcıdan kaldırılır.

Canlı bir olay, en fazla üç eşzamanlı çalışan canlı çıkışı destekler (aynı anda bir Canlı akıştan en fazla 3 kayıt/arşiv oluşturabilirsiniz). Bu destek, gerektiğinde bir olayın farklı parçalarını yayımlamanıza ve arşivlemenize olanak tanır. 7/24 canlı bir doğrusal akış yayınlamalı ve müşterilere, yakalama için isteğe bağlı içerik olarak sunmak üzere gün boyunca farklı programların "kayıtlarını" oluşturmanız gerektiğini varsayalım. Bu senaryo için, ilk olarak 1 saat veya daha kısa bir arşiv penceresi ile birincil bir canlı çıktı oluşturursunuz; bu, görüntüleyicilerinizin ayarlayacağından asıl canlı akışdır. Bu canlı çıktı için bir akış Bulucu oluşturur ve bunu uygulamanızda veya Web sitenizde "canlı" akış olarak yayımlayabilirsiniz. Canlı olay çalışırken programın başlangıcında (veya daha sonra kırpmak için bazı tutamaçlar sağlamak için 5 dakika erken), programlı olarak ikinci bir eşzamanlı canlı çıktı oluşturabilirsiniz. Bu ikinci canlı çıktı, program bittikten 5 dakika sonra silinebilir. Bu ikinci varlıkla, bu programı uygulamanızın kataloğunda isteğe bağlı bir varlık olarak yayımlamak üzere yeni bir akış Bulucu oluşturabilirsiniz. Bu işlemi, isteğe bağlı videolar olarak paylaşmak istediğiniz diğer program sınırları veya vurguları için birden çok kez yineleyebilirsiniz, ancak ilk canlı çıktının "canlı" akışı, doğrusal akışın yayınlanmaya devam eder.

## <a name="creating-an-archive-for-on-demand-playback"></a>İsteğe bağlı kayıttan yürütme için Arşiv oluşturma

Canlı çıktının silindiği varlık, canlı çıktı silindiğinde otomatik olarak isteğe bağlı bir varlık haline gelir. Canlı bir olay durdurulmadan önce tüm canlı çıktıları silmeniz gerekir. Durdurulduğunda canlı çıktıları otomatik olarak kaldırmak için, bir [Removeoutputsonstop](/rest/api/media/liveevents/stop#request-body) isteğe bağlı bayrağını kullanabilirsiniz.

Olayı durdurup sildikten sonra bile, varlık silinmedikçe, kullanıcılar arşivlenmiş içeriğinizi isteğe bağlı bir video olarak akışa alabilir. Bir olay tarafından kullanılıyorsa bir varlık silinmemelidir; önce olayın silinmesi gerekir.

Canlı çıkışınızın varlığını bir akış Bulucu kullanarak yayımladıysanız, canlı olay (DVR pencere uzunluğuna kadar), akış bulucunun bitiş tarihi veya silme işlemi, hangisi önce geldiği sürece görüntülenmeye devam eder.

Daha fazla bilgi için bkz.

- [Canlı akışa genel bakış](stream-live-streaming-concept.md)
- [Canlı akış öğreticisi](stream-live-tutorial-with-api.md)

> [!NOTE]
> Canlı çıktıyı sildiğinizde, ilgili varlık ve varlığın içeriğini silmezsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Videolarınızın alt klibini yapın](transform-subclip-video-rest-how-to.md).
* [Varlıklarınız için filtreleri tanımlayın](filters-dynamic-manifest-rest-howto.md).
