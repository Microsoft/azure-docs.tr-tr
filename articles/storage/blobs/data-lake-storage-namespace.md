---
title: Azure Data Lake Storage 2. hiyerarşik ad alanı
description: Azure Data Lake Storage 2. için hiyerarşik ad alanı kavramını açıklar
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: de7f62dd4d4205e819278e6dc90d905e825580f2
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220513"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Azure Data Lake Storage 2. hiyerarşik ad alanı

Azure Data Lake Storage 2., nesne depolama ölçeğinde dosya sistemi performansı sağlamasına izin veren bir anahtar mekanizması ve fiyatlar **hiyerarşik bir ad alanı** eklidir. Bu, bir hesabın içindeki nesne/dosya koleksiyonunun, bilgisayarınızdaki dosya sistemi ile aynı şekilde bir dizin ve iç içe geçmiş alt dizinler hiyerarşisi halinde düzenlenmesine izin verir. Hiyerarşik bir ad alanı etkinken, bir depolama hesabı, analiz motorları ve çerçeveler hakkında tanıdık dosya sistemi semantiği ile, nesne depolamanın ölçeklenebilirlik ve maliyet verimliliğini sağlamalarına sahip olur.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>Hiyerarşik bir ad alanının avantajları

Aşağıdaki avantajlar, blob verileri üzerinde hiyerarşik bir ad alanı uygulayan dosya sistemleriyle ilişkilendirilir:

- **Atomik dizin düzenlemesi:** Nesne, yol parçalarını belirtmek için nesne adında eğik çizgi (/) ekleme kuralı benimseerek yaklaşık bir dizin hiyerarşisini depolar. Bu kural nesneleri düzenlemek için çalışırken, bu kural, dizinleri taşıma, yeniden adlandırma veya silme gibi eylemler için Yardım sağlamaz. Gerçek dizinler olmadan, uygulamalar, dizin düzeyindeki görevleri başarmak için milyonlarca ayrı blob 'ları işlemelidir. Buna karşılık, hiyerarşik bir ad alanı tek bir girişi (üst dizin) güncelleştirerek bu görevleri işler.

    Bu ciddi iyileştirme özellikle çok büyük veri analizi çerçeveleri için önemlidir. Hive, Spark, vb. gibi araçlar genellikle geçici konumlara çıkış yazar ve ardından işin sonunda konumu yeniden adlandırır. Hiyerarşik bir ad alanı olmadan, bu yeniden adlandırma genellikle analiz sürecinin kendisinden daha uzun sürebilir. Daha düşük iş gecikmesi, analiz iş yükleri için toplam sahip olma maliyeti (TCO) değerine eşit.

- **Tanıdık arabirim stili:** Dosya sistemleri, geliştiriciler ve kullanıcılar tarafından da benzer şekilde anlaşılacaktır. Data Lake Storage 2. tarafından açığa çıkarılan dosya sistemi arabirimi, büyük ve küçük bilgisayarlar tarafından kullanılan paradigmadır, buluta geçtiğinizde yeni bir depolama paradigması gerekmez.

Nesne mağazalarının depolamamasının bir hiyerarşik ad alanı, hiyerarşik bir ad alanı tarafından sınırlanıyor. Ancak, Data Lake Storage 2. hiyerarşik ad alanı doğrusal bir şekilde ölçeklendirilir ve veri kapasitesini veya performansını düşürür.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Hiyerarşik bir ad alanını etkinleştirip etkinleştirmeyeceğine karar verme

Hesabınızda hiyerarşik bir ad alanını etkinleştirdikten sonra, onu düz bir ad alanına geri döndüremezsiniz. Bu nedenle, nesne Mağazası iş yüklerinizin yapısını temel alan hiyerarşik bir ad alanını etkinleştirmek için mantıklı olup olmadığını göz önünde bulundurun.

Bazı iş yükleri hiyerarşik bir ad alanını etkinleştirerek herhangi bir avantaj kazanmayabilir. Yedeklemeler, görüntü depolaması ve nesne kuruluşunun nesnelerden ayrı olarak depolandığı diğer uygulamalar (örneğin, ayrı bir veritabanında) sayılabilir. 

Ayrıca, BLOB depolama özellikleri için destek ve Azure Service ekosistemi de büyümeye devam ederken, hiyerarşik bir ad alanı olan hesaplarda henüz desteklenmeyen bazı özellikler ve Azure hizmetleri vardır. [Bilinen sorunlara](data-lake-storage-known-issues.md)bakın. 

Genel olarak, dizinleri işleyen dosya sistemleri için tasarlanan depolama iş yükleri için hiyerarşik bir ad alanı etkinleştirmenizi öneririz. Bu, öncelikli olarak analiz işleme için olan tüm iş yüklerini içerir. Yüksek derecede kuruluş gerektiren veri kümeleri, hiyerarşik bir ad alanını etkinleştirerek de avantajdan yararlanabilir.

Hiyerarşik ad alanını etkinleştirme nedenleri bir TCO Analizi tarafından belirlenir. Genel olarak, depolama hızlandırmasının nedeniyle iş yükü gecikmede yapılan iyileştirmeler, daha az zaman için işlem kaynakları gerektirir. Hiyerarşik bir ad alanı tarafından etkinleştirilen atomik dizin düzenlemesi nedeniyle birçok iş yükü gecikmesi artırılabilir. Birçok iş yükünde, işlem kaynağı toplam maliyetinin %85 ' ünü temsil > eder ve bu nedenle iş yükü gecikmede bir azalma azalmasıyla önemli miktarda TCO tasarrufu elde edilir. Hiyerarşik bir ad alanının etkinleştirilmesinde depolama maliyetlerini artıran durumlarda bile, daha az işlem maliyeti nedeniyle TCO düşürüldü.

Veri depolama fiyatlarındaki farkları, işlem fiyatlarını ve düz bir hiyerarşik ad alanı olan hesaplar arasında hiyerarşik bir ad alanına karşı depolama kapasitesi ayırma fiyatlandırmasını çözümlemek için bkz. [Azure Data Lake Storage 2. fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="enabling-a-hierarchical-namespace"></a>Hiyerarşik ad alanını etkinleştirme

Var olan bir depolama hesabında hiyerarşik bir ad alanını etkinleştiremezsiniz. Hiyerarşik bir ad alanını yalnızca bir depolama hesabı oluşturduğunuzda etkinleştirebilirsiniz. Rehberlik için bkz. [Azure Data Lake Storage 2. ile kullanılacak depolama hesabı oluşturma](create-data-lake-storage-account.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Depolama hesabı oluşturma](../common/storage-account-create.md)