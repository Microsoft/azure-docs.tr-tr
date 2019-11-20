---
title: Azure Data Factory eşleme veri akışında dönüştürmeyi birleştirin
description: Azure Data Factory eşleme veri akışındaki birleştirme dönüşümünü kullanarak iki veri kaynağından verileri birleştirme
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 1e9315195ceae435447739055105a66ee81e2a6a
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122931"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Eşleme veri akışında dönüştürmeyi Birleştir

İki kaynaktan veya bir eşleme veri akışındaki akışlardan verileri birleştirmek için JOIN dönüşümünü kullanın. Çıkış akışı, her iki kaynaktan da bir JOIN koşuluna göre eşleşen tüm sütunları içerecektir. 

## <a name="join-types"></a>JOIN türleri

Eşleme veri akışları Şu anda beş farklı JOIN türünü desteklemektedir.

### <a name="inner-join"></a>İç birleşim

İç birleşim yalnızca her iki tabloda eşleşen değerleri olan satırları çıktı olarak verir.

### <a name="left-outer"></a>Sol dış

Sol dış birleşim, sol akıştaki tüm satırları ve doğru akıştan eşleşen kayıtları döndürür. Sol akıştaki bir satırda eşleşme yoksa, doğru akıştaki çıkış sütunları NULL olarak ayarlanır. Çıktı, bir iç birleşim ile döndürülen satırlar ve sol akıştaki eşleşmeyen satırlar olacaktır.

### <a name="right-outer"></a>Sağ dış

Sağ dış birleşim, sol akıştaki doğru akıştaki ve eşleşen kayıtlardan tüm satırları döndürür. Doğru akıştaki bir satırda eşleşme yoksa, sol akıştaki çıkış sütunları NULL olarak ayarlanır. Çıktı, bir iç birleşim ile döndürülen satırlar ve doğru akıştan eşleşmeyen satırlar olacaktır.

### <a name="full-outer"></a>Tam dış

Tam dış birleşim, eşleşmeyen sütunlarda NULL değerler ile her iki taraftan da tüm sütunları ve satırları çıktı.

### <a name="cross-join"></a>Çapraz ekleme

Çapraz birle, iki akışın çapraz çarpımını bir koşula göre çıktı. Eşitlik olmayan bir koşul kullanıyorsanız, çapraz ekleme koşulunuz olarak bir özel ifade belirtin. Çıkış akışı, JOIN koşulunu karşılayan tüm satırlar olacaktır. Her satır bileşimini çıkaran bir Kartezyen ürün oluşturmak için, birleşim koşulunuz olarak `true()` belirtin.

## <a name="configuration"></a>Yapılandırma

1. **Sağ Akış** açılan menüsünde hangi veri akışını katdığınızı seçin.
1. **JOIN türünü** seçin
1. JOIN Koşulunuz için hangi anahtar sütunlarını eşlemek istediğinizi seçin. Varsayılan olarak, veri akışı her akıştaki bir sütun arasında eşitlik arar. Hesaplanan bir değer ile karşılaştırmak için, sütun açılan listesinin üzerine gelin ve **hesaplanan sütun**' u seçin.

![Dönüşümü Birleştir](media/data-flow/join.png "Birleştir")

## <a name="optimizing-join-performance"></a>JOIN performansını iyileştirme

SSIS gibi araçlarla birleştirme birleştirmesinin aksine, JOIN dönüşümü zorunlu bir birleştirme birleştirme işlemi değildir. JOIN anahtarları sıralama gerektirmez. JOIN işlemi, Spark içindeki en iyi JOIN işlemine bağlı olarak, yayın veya harita tarafı birleşimi üzerinden gerçekleşir.

![Dönüştürme iyileştirmelerine Birleştir](media/data-flow/joinoptimize.png "Birleştirmeyi En Iyi duruma getirme")

Veri akışlarının bir veya her ikisi de çalışan düğümü belleğine sığması durumunda, en iyi hale getirilmiş sekmesinden **yayını** etkinleştirerek performansınızı daha iyi hale getirin. Ayrıca, verileri, çalışan başına belleğe daha iyi uyacak şekilde, JOIN işleminde yeniden bölümlendirebilirsiniz.

## <a name="self-join"></a>Kendi kendine Birleştir

Bir veri akışını kendisiyle birleştirmek için, bir seçme dönüşümü olan mevcut bir akışa diğer ad ekleyin. Bir dönüşümün yanındaki artı simgesine tıklayıp **yeni dal**' ı seçerek yeni bir dal oluşturun. Özgün akışa diğer ad için bir seçme dönüşümü ekleyin. Bir JOIN dönüşümü ekleyin ve **sol** akış olarak orijinal akışı ve seçme dönüşümünü **doğru akış**olarak seçin.

![Kendi kendine Birleştir](media/data-flow/selfjoin.png "Kendi kendine Birleştir")

## <a name="testing-join-conditions"></a>Test ekleme koşulları

Hata ayıklama modundaki veri önizlemesiyle JOIN dönüştürmelerinin test edilirken, küçük bir bilinen veri kümesi kullanın. Büyük bir veri kümesinden satırlar örneklenirken, hangi satırların ve anahtarların test için okunacağını tahmin edemeyecektir. Sonuç belirleyici değildir, yani JOIN koşullarınızın herhangi bir eşleşme döndürmeyebilir.

## <a name="data-flow-script"></a>Veri akışı betiği

### <a name="syntax"></a>Sözdizimi

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>İç birleşim örneği

Aşağıdaki örnek, `JoinMatchedData` adlı bir JOIN dönüştürmedir `TripData` ve sağ Akış `TripFare`.  JOIN koşulu, her akıştaki `hack_license`, `medallion`, `vendor_id`ve `pickup_datetime` sütunları eşleşiyorsa true döndüren ifadedir `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}`. `joinType` `'inner'`. `broadcast` `'left'`değer olduğundan, yalnızca sol akışta yayını etkinleştiriyoruz.

Data Factory UX 'de, bu dönüşüm aşağıdaki görüntüye benzer şekilde görünür:

![JOIN örneği](media/data-flow/join-script1.png "JOIN örneği")

Bu dönüşüm için veri akışı betiği aşağıdaki kod parçacığında verilmiştir:

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="cross-join-example"></a>Çapraz ekleme örneği

Aşağıdaki örnek, `CartesianProduct` adlı bir JOIN dönüştürmedir `TripData` ve sağ Akış `TripFare`. Bu dönüşüm iki akış alır ve satırlarının Kartezyen bir ürününü döndürür. Tam bir Kartezyen ürün çıkış yaptığından, JOIN koşulu `true()`. `joinType` `cross`. `broadcast` `'left'`değer olduğundan, yalnızca sol akışta yayını etkinleştiriyoruz.

Data Factory UX 'de, bu dönüşüm aşağıdaki görüntüye benzer şekilde görünür:

![JOIN örneği](media/data-flow/join-script2.png "JOIN örneği")

Bu dönüşüm için veri akışı betiği aşağıdaki kod parçacığında verilmiştir:

```
TripData, TripFare
    join(
        true(),
        joinType:'cross',
        broadcast: 'left'
    )~> CartesianProduct
```

## <a name="next-steps"></a>Sonraki adımlar

Verileri katıldıktan sonra, türetilmiş bir [sütun](data-flow-derived-column.md) oluşturun ve verilerinizi bir hedef veri deposuna [havuzın](data-flow-sink.md) .
