---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67188769"
---
| Sınır tanımlayıcı | Sınır | Açıklamalar |
| --- | --- | --- |
| En fazla depolama hesabı kimlik bilgisi sayısı |64 | |
| En fazla birim kapsayıcısı sayısı |64 | |
| En fazla birim sayısı |255 | |
| Bant genişliği şablonu başına en fazla zamanlama sayısı |168 |Haftanın her gününde her saat için bir zamanlama. |
| Fiziksel cihazlardaki katmanlı birimin en büyük boyutu |StorSimple 8100 ve StorSimple 8600 için 64 TB |StorSimple 8100 ve StorSimple 8600 fiziksel cihazlardır. |
| Azure 'daki sanal cihazlarda en fazla katmanlı birim boyutu |StorSimple 8010 için 30 TB <br></br> StorSimple 8020 için 64 TB |StorSimple 8010 ve StorSimple 8020, Azure 'da, sırasıyla standart depolama ve Premium depolama kullanan sanal cihazlardır. |
| Fiziksel cihazlarda yerel olarak sabitlenmiş bir birimin en büyük boyutu |StorSimple 8100 için 9 TB <br></br> StorSimple 8600 için 24 TB |StorSimple 8100 ve StorSimple 8600 fiziksel cihazlardır. |
| En fazla Iscsı bağlantısı sayısı |512 | |
| Başlatıcılardan en fazla Iscsı bağlantısı sayısı |512 | |
| Cihaz başına en fazla erişim denetimi kaydı sayısı |64 | |
| Yedekleme ilkesi başına en fazla birim sayısı |24 | |
| Yedekleme ilkesi başına tutulan en fazla yedekleme sayısı |64 | |
| Yedekleme ilkesi başına en fazla zamanlama sayısı |10 | |
| Birim başına tutulabilirler, her türden en fazla anlık görüntü sayısı |256 |Bu miktar yerel anlık görüntüleri ve bulut anlık görüntülerini içerir. |
| Herhangi bir cihazda bulunabilecek en fazla anlık görüntü sayısı |10,000 | |
| Yedekleme, geri yükleme veya kopyalama için paralel olarak işlenelebilecek en fazla birim sayısı |16 |<ul><li>16 ' dan fazla birim varsa, işlem yuvaları kullanılabilir hale geldiğinde bunlar sırayla işlenir.</li><li>Kopyalanmış veya geri yüklenen katmanlı bir birimin yeni yedeklemeleri işlem tamamlanana kadar gerçekleşemez. Yerel bir birimde, birim çevrimiçi olduktan sonra yedeklemelere izin verilir.</li></ul> |
| Katmanlı birimler için kurtarma süresini geri yükleme ve kopyalama |< 2 dakika |<ul><li>Birim boyutu ne olursa olsun, bir geri yükleme veya kopyalama işleminin 2 dakika içinde kullanılabilir hale getirilir.</li><li>Verilerin çoğu ve meta verilerin büyük bir bölümü de bulutta bulunduğundan, birim performansı başlangıçta normalden daha yavaş olabilir. Performans, buluttan StorSimple cihazına veri akışı kadar artabilir.</li><li>Meta verilerin indirileceği toplam süre, ayrılan birim boyutuna bağlıdır. Meta veriler otomatik olarak cihaza, ayrılmış birim verileri başına 5 dakikalık bir hızda arka planda getirilir. Bu fiyat, Internet bant genişliğinden buluta etkilenebilir.</li><li>Geri yükleme veya kopyalama işlemi, tüm meta veriler cihazdan olduğunda tamamlanır.</li><li>Geri yükleme veya kopyalama işlemi tam olarak tamamlanana kadar yedekleme işlemleri gerçekleştirilemez. |
| Yerel olarak sabitlenmiş birimler için kurtarma süresini geri yükle |< 2 dakika |<ul><li>Birim boyutu ne olursa olsun, geri yükleme işleminin 2 dakika içinde kullanılabilir hale getirilir.</li><li>Verilerin çoğu ve meta verilerin büyük bir bölümü de bulutta bulunduğundan, birim performansı başlangıçta normalden daha yavaş olabilir. Performans, buluttan StorSimple cihazına veri akışı kadar artabilir.</li><li>Meta verilerin indirileceği toplam süre, ayrılan birim boyutuna bağlıdır. Meta veriler otomatik olarak cihaza, ayrılmış birim verileri başına 5 dakikalık bir hızda arka planda getirilir. Bu fiyat, Internet bant genişliğinden buluta etkilenebilir.</li><li>Katmanlı birimlerden farklı olarak, yerel olarak sabitlenmiş birimler varsa, birim verileri de cihaza yerel olarak indirilir. Tüm birim verileri cihaza getirildiğinde geri yükleme işlemi tamamlanır.</li><li>Geri yükleme işlemleri uzun olabilir ve geri yüklemeyi tamamlamaya yönelik toplam süre, sağlanan yerel birimin, Internet bant genişliğinizin ve cihazdaki mevcut verilerin boyutuna bağlı olarak değişir. Geri yükleme işlemi devam ederken, yerel olarak sabitlenmiş birimdeki yedekleme işlemlerine izin verilir. |
| İnce-geri yükleme kullanılabilirliği |Son yük devretme | |
| SSD katmanından sunulduğunda istemci okuma/yazma aktarım hızı üst sınırı * |tek bir 10 Gigabit Ethernet ağ arabirimiyle 920/720 MB/sn |MPIO ve iki ağ arabirimi ile en fazla iki kez. |
| HDD katmanından sunulduğunda istemci okuma/yazma aktarım hızı üst sınırı * |120/250 MB/sn | |
| Bulut katmanından sunulduğunda istemci okuma/yazma aktarım hızı üst sınırı * |11/41 MB/sn |Okuma aktarım hızı, yeterli g/ç sırası derinliğini oluşturan ve bu istemcilere göre değişir. |

&#42;G/ç türü başına en fazla aktarım hızı% 100 okuma ve% 100 yazma senaryolarıyla ölçülür. Gerçek aktarım hızı daha düşük olabilir ve g/ç karışımı ve ağ koşullarına bağlı olabilir.

