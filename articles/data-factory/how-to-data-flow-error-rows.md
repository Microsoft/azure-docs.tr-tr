---
title: Azure Data Factory veri akışlarını eşleme ile hata satırlarını işleme
description: Veri akışlarını eşleme kullanarak Azure Data Factory SQL kesme hatalarının nasıl işleneceğini öğrenin.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: makromer
ms.openlocfilehash: 3fe3403ad06d82ba5ccd33d2718bf0e5eff64490
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166544"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Data Factory eşleme veri akışlarında SQL kesme hatası satırlarını işle

Veri akışlarını eşleme kullanılırken Data Factory ortak bir senaryo, dönüştürülmüş verilerinizi bir Azure SQL veritabanına yazmaktır. Bu senaryoda, karşı engellemeniz gereken yaygın bir hata koşulunun olası sütun kesilmesi olabilir. Bir hedef dize sütununa sığmayan sütunların günlüğe kaydedilmesini sağlamak için bu adımları izleyin ve veri akışınız bu senaryolarda devam etmesine izin verir.

## <a name="scenario"></a>Senaryo

1. "Ad" adlı bir ```nvarchar(5)``` sütununa sahip bir hedef Azure SQL veritabanı tablosu sunuyoruz.

2. Veri akışımız içinde, havuzumuzdaki film başlıklarını bu hedef "ad" sütununa eşlemek istiyoruz.

    ![Film veri akışı 1](media/data-flow/error4.png)
    
3. Bu sorun, film başlığının hepsi yalnızca 5 karakter içerebilen bir havuz sütunu içine sığmayadır. Bu veri akışını yürüttüğünüzde aşağıdakine benzer bir hata alırsınız: ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

## <a name="how-to-design-around-this-condition"></a>Bu durumu nasıl tasarlayacağımızı

1. Bu senaryoda, "ad" sütununun en fazla uzunluğu beş karakterdir. Bu nedenle, satırları beş karakterden daha uzun olan "başlıklar" ile günlüğe yazmamızı sağlayacak bir koşullu bölünmüş dönüştürme ekleyelim ve ayrıca bu alana sığamayacak olan satırların veritabanına yazmak için bu alana sığması sağlanır.

    ![Koşullu bölme](media/data-flow/error1.png)

2. Bu koşullu bölünmüş dönüşüm, en fazla "başlık" uzunluğunu beş olacak şekilde tanımlar. Beşten küçük veya buna eşit olan herhangi bir satır ```GoodRows``` akışa gider. Beşten büyük olan herhangi bir satır ```BadRows``` akışa gider.

3. Şimdi başarısız olan satırları günlüğe kaydetmek istiyoruz. Günlüğe kaydetme için ```BadRows``` akışa bir havuz dönüştürmesi ekleyin. Burada, tam işlem kaydının günlüğe kaydedilmesini sağlamak için tüm alanları "otomatik eşleme" yapacağız. Bu, blob depolamada tek bir dosyaya metin ile ayrılmış bir CSV dosya çıktıdır. "BadRows. csv" günlük dosyasını çağıracağız.

    ![Hatalı satırlar](media/data-flow/error3.png)
    
4. Tamamlanan veri akışı aşağıda gösterilmiştir. Artık SQL kesme hatalarından kaçınmak ve bu girdileri bir günlük dosyasına yerleştirmek için hata satırlarını böyoruz. Bu arada, başarılı satırlar hedef veritabanımızı yazmaya devam edebilir.

    ![veri akışını Tamam](media/data-flow/error2.png)

## <a name="next-steps"></a>Sonraki adımlar

* Veri akışı [dönüştürmelerini](concepts-data-flow-overview.md)eşleme kullanarak veri akışı mantığınızın geri kalanını oluşturun.
