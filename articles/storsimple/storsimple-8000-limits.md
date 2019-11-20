---
title: StorSimple 8000 serisi sistem sınırları | Microsoft Docs
description: StorSimple 8000 serisi bileşenleri ve bağlantıları için sistem sınırlarını ve önerilen boyutları açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/28/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70f2d9542082ddf7ecf1d1e7361b0ecdb14c5ef8
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963382"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>StorSimple 8000 serisi sistem sınırları nelerdir?

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Genel Bakış

StorSimple, veri merkeziniz için ölçeklenebilir ve esnek depolama sağlar. Ancak, StorSimple çözümünüzü planlarken, dağıtırken ve işletirken göz önünde bulundurmanız gereken bazı sınırlar vardır. Aşağıdaki tabloda bu sınırlar açıklanmakta ve StorSimple çözümünüzden en iyi şekilde yararlanmak için bazı öneriler sağlanmıştır.

| Sınır tanımlayıcı | Sınır | Açıklamalar |
| --- | --- | --- |
| En fazla depolama hesabı kimlik bilgisi sayısı |64 | |
| En fazla birim kapsayıcısı sayısı |64 | |
| En fazla birim sayısı |255 | |
| Yerel olarak sabitlenmiş birim sayısı üst sınırı |32 | |
| Bant genişliği şablonu başına en fazla zamanlama sayısı |168 |Haftanın her gününde her saat için bir zamanlama (24 * 7). |
| Fiziksel cihazlardaki katmanlı birimin en büyük boyutu |8100 ve 8600 için 64 TB |8100 ve 8600 fiziksel cihazlardır. |
| Azure 'daki sanal cihazlarda en fazla katmanlı birim boyutu |8010 için 30 TB <br></br> 8020 için 64 TB |8010 ve 8020, Azure 'da standart depolama ve Premium depolama kullanan sanal cihazlardır. |
| Fiziksel cihazlarda yerel olarak sabitlenmiş bir birimin en büyük boyutu |8100 için 8,5 TB <br></br> 8600 için 22,5 TB |8100 ve 8600 fiziksel cihazlardır. |
| En fazla Iscsı bağlantısı sayısı |512 | |
| Başlatıcılardan en fazla Iscsı bağlantısı sayısı |512 | |
| Cihaz başına en fazla erişim denetimi kaydı sayısı |64 | |
| Yedekleme ilkesi başına en fazla birim sayısı |20 | |
| Zamanlama başına tutulan en fazla yedekleme sayısı (bir yedekleme ilkesinde) |64 | |
| Yedekleme ilkesi başına en fazla zamanlama sayısı |10 | |
| Birim başına tutulabilirler, her türden en fazla anlık görüntü sayısı |256 |Bu sayı yerel anlık görüntüleri ve bulut anlık görüntülerini içerir. |
| Herhangi bir cihazda bulunabilecek en fazla anlık görüntü sayısı |10,000 | |
| Yedekleme, geri yükleme veya kopyalama için paralel olarak işlenelebilecek en fazla birim sayısı |16 |<ul><li>16 ' dan fazla birim varsa, işlem yuvaları kullanılabilir hale geldiğinde bunlar sırayla işlenir.</li><li>Kopyalanmış veya geri yüklenen katmanlı bir birimin yeni yedeklemeleri işlem tamamlanana kadar gerçekleşemez. Ancak, yerel bir birim için, birim çevrimiçi olduktan sonra yedeklemelere izin verilir.</li></ul> |
| Katmanlı birimler için kurtarma süresini geri yükleme ve kopyalama |< 2 dakika |<ul><li>Birimin boyutu ne olursa olsun, birim, geri yükleme veya kopyalama işleminde 2 dakika içinde kullanılabilir hale getirilir.</li><li>Verilerin çoğu ve meta verilerin büyük bir olasılıkla bulutta yer aldığı için birim performansı başlangıçta normalden daha yavaş olabilir. Performans, buluttan StorSimple cihazına veri akışı kadar artabilir.</li><li>Meta verilerin indirileceği toplam süre, ayrılan birim boyutuna bağlıdır. Meta veriler otomatik olarak cihaza, ayrılmış birim verileri başına 5 dakikalık bir hızda arka planda getirilir. Bu fiyat, Internet bant genişliğinden buluta etkilenebilir.</li><li>Geri yükleme veya kopyalama işlemi, tüm meta veriler cihazdan olduğunda tamamlanır.</li><li>Geri yükleme veya kopyalama işlemi tam olarak tamamlanana kadar yedekleme işlemleri gerçekleştirilemez. |
| Yerel olarak sabitlenmiş birimler için kurtarma süresini geri yükle |< 2 dakika |<ul><li>Birim boyutu ne olursa olsun, geri yükleme işleminin 2 dakika içinde kullanılabilir hale getirilir.</li><li>Verilerin çoğu ve meta verilerin büyük bir olasılıkla bulutta yer aldığı için birim performansı başlangıçta normalden daha yavaş olabilir. Performans, buluttan StorSimple cihazına veri akışı kadar artabilir.</li><li>Meta verilerin indirileceği toplam süre, ayrılan birim boyutuna bağlıdır. Meta veriler otomatik olarak cihaza, ayrılmış birim verileri başına 5 dakikalık bir hızda arka planda getirilir. Bu fiyat, Internet bant genişliğinden buluta etkilenebilir.</li><li>Katmanlı birimlerin aksine, yerel olarak sabitlenmiş birimler için, birim verileri de cihaza yerel olarak indirilir. Tüm birim verileri cihaza getirildiğinde geri yükleme işlemi tamamlanır.</li><li>Geri yükleme işlemleri uzun olabilir. Geri yüklemeyi tamamlamaya yönelik toplam süre, sağlanan yerel birimin, Internet bant genişliğinizin ve cihazdaki mevcut verilerin boyutuna bağlıdır. Geri yükleme işlemi devam ederken, yerel olarak sabitlenmiş birimdeki yedekleme işlemlerine izin verilir. |
| Bulut anlık görüntüleri için işleme hızı |15 dakika/TB |<ul><li>Bulut anlık görüntüsünün karşıya yükleme için, yedekleme sırasında her TB 'ye ayrılan birim veri başına hazırlık yapması için gereken en kısa süre. </li><li> Toplam bulut anlık görüntü süresi, bu kez, Internet bant genişliğinden buluta etkilenen anlık görüntü karşıya yükleme zamanına eklenerek hesaplanır. |
| İstemci okuma/yazma performansı üst sınırı (SSD katmanından sunulduğunda) * |tek 10 GbE ağ arabirimiyle 920/720 MB/s |MPIO ve iki ağ arabirimi ile en fazla 2x. |
| İstemci okuma/yazma performansı üst sınırı (HDD katmanından sunulduğunda) * |120/250 MB/s | |
| Güncelleştirme 3 ve sonrası için en fazla istemci okuma/yazma işleme (bulut katmanından sunulduğunda) * * * |katmanlı birimler için 40/60 MB/s<br><br>birim oluşturma sırasında arşiv seçeneği belirlenmiş katmanlı birimler için 60/80 MB/s |Okuma aktarım hızı, yeterli g/ç sırası derinliğini oluşturan ve bu istemcilere göre değişir. <br><br>Elde edilen hız, kullanılan temel depolama hesabının hızına bağlıdır. |

&#42;G/ç türü başına en fazla aktarım hızı% 100 okuma ve% 100 yazma senaryolarıyla ölçülür. Gerçek aktarım hızı daha düşük olabilir ve g/ç karışımı ve ağ koşullarına bağlı olabilir.

&#42;&#42;Güncelleştirme 3 ' ten önceki performans numaraları daha düşük olabilir.

## <a name="next-steps"></a>Sonraki adımlar
[StorSimple sistem gereksinimlerini](storsimple-8000-system-requirements.md)gözden geçirin.

