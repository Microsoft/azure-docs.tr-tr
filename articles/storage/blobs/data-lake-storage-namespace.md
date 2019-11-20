---
title: Azure Data Lake Storage 2. hiyerarşik ad alanı
description: Azure Data Lake Storage 2. için hiyerarşik ad alanı kavramını açıklar
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 0b98892bd31b097e3dc217d54f52f12550599d32
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847136"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Azure Data Lake Storage 2. hiyerarşik ad alanı

Azure Data Lake Storage 2., nesne depolama ölçeğinde dosya sistemi performansı sağlamasına izin veren bir anahtar mekanizması ve fiyatlar **hiyerarşik bir ad alanı**eklidir. Bu, bir hesabın içindeki nesne/dosya koleksiyonunun, bilgisayarınızdaki dosya sistemi ile aynı şekilde bir dizin ve iç içe geçmiş alt dizinler hiyerarşisi halinde düzenlenmesine izin verir. Hiyerarşik ad alanı etkinken, bir depolama hesabı, analiz motorları ve çerçeveler hakkında tanıdık dosya sistemi semantiği ile, nesne depolamanın ölçeklenebilirlik ve maliyet verimliliğini sağlamalarına sahip olur.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Hiyerarşik ad alanının avantajları

Aşağıdaki avantajlar, blob verileri üzerinde hiyerarşik bir ad alanı uygulayan dosya sistemleriyle ilişkilendirilir:

- **Atomik dizin düzenlemesi:** Nesne, yol parçalarını belirtmek için nesne adında eğik çizgi (/) ekleme kuralı benimseerek yaklaşık bir dizin hiyerarşisini depolar. Bu kural nesneleri düzenlemek için çalışırken, bu kural, dizinleri taşıma, yeniden adlandırma veya silme gibi eylemler için Yardım sağlamaz. Gerçek dizinler olmadan, uygulamalar, dizin düzeyindeki görevleri başarmak için milyonlarca ayrı blob 'ları işlemelidir. Bunun aksine, hiyerarşik ad alanı tek bir girişi (üst dizin) güncelleştirerek bu görevleri işler.

    Bu ciddi iyileştirme özellikle çok büyük veri analizi çerçeveleri için önemlidir. Hive, Spark, vb. gibi araçlar genellikle geçici konumlara çıkış yazar ve ardından işin sonunda konumu yeniden adlandırır. Hiyerarşik ad alanı olmadan, bu yeniden adlandırma genellikle analiz sürecinin kendisinden daha uzun sürebilir. Daha düşük iş gecikmesi, analiz iş yükleri için toplam sahip olma maliyeti (TCO) değerine eşit.

- **Tanıdık arabirim stili:** Dosya sistemleri, geliştiriciler ve kullanıcılar tarafından da benzer şekilde anlaşılacaktır. Data Lake Storage 2. tarafından açığa çıkarılan dosya sistemi arabirimi, büyük ve küçük bilgisayarlar tarafından kullanılan paradigmadır, buluta geçtiğinizde yeni bir depolama paradigması gerekmez.

Nesne mağazalarının depolamamasının bir hiyerarşik ad alanı, hiyerarşik bir ad alanı tarafından sınırlanıyor. Ancak, Data Lake Storage 2. hiyerarşik ad alanı doğrusal bir şekilde ölçeklendirilir ve veri kapasitesini veya performansını düşürür.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Hiyerarşik ad alanı ne zaman etkinleştirilecek

Dizinleri işleyen dosya sistemleri için tasarlanan depolama iş yükleri için hiyerarşik ad alanını etkinleştirmenizi öneririz. Bu, öncelikli olarak analiz işleme için olan tüm iş yüklerini içerir. Yüksek derecede kuruluş gerektiren veri kümeleri, hiyerarşik ad alanını etkinleştirerek de avantajlı olur.

Hiyerarşik ad alanını etkinleştirme nedenleri bir TCO Analizi tarafından belirlenir. Genel olarak, depolama hızlandırmasının nedeniyle iş yükü gecikmede yapılan iyileştirmeler, daha az zaman için işlem kaynakları gerektirir. Hiyerarşik ad alanı tarafından etkinleştirilen atomik dizin düzenlemesi nedeniyle birçok iş yükü için gecikme artırılabilir. Birçok iş yükünde, işlem kaynağı toplam maliyetinin% 85 ' ünü temsil > eder ve bu nedenle iş yükü gecikmede bir azalma azalmasıyla önemli miktarda TCO tasarrufu elde edilir. Hiyerarşik ad alanının etkinleştirilmesinde depolama maliyetlerini artıran durumlarda bile, daha az işlem maliyeti nedeniyle TCO düşürüldü.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Hiyerarşik ad alanını devre dışı bırakma

Bazı nesne Mağazası iş yükleri, hiyerarşik ad alanını etkinleştirerek herhangi bir avantaj kazanmayabilir. Yedeklemeler, görüntü depolaması ve nesne kuruluşunun nesnelerden ayrı olarak depolandığı diğer uygulamalar (örneğin, ayrı bir veritabanında) sayılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Depolama hesabı oluşturma](./data-lake-storage-quickstart-create-account.md)
