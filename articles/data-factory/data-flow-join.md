---
title: Eşleme veri akışında dönüştürmeyi Birleştir
description: Azure Data Factory eşleme veri akışındaki birleştirme dönüşümünü kullanarak iki veri kaynağından verileri birleştirme
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: ac84ce17f53145ffd85ffa31b6633d8b4b184962
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93042651"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Eşleme veri akışında dönüştürmeyi Birleştir

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

İki kaynaktan veya bir eşleme veri akışındaki akışlardan verileri birleştirmek için JOIN dönüşümünü kullanın. Çıkış akışı, her iki kaynaktan da bir JOIN koşuluna göre eşleşen tüm sütunları içerecektir. 

## <a name="join-types"></a>JOIN türleri

Eşleme veri akışları Şu anda beş farklı JOIN türünü desteklemektedir.

### <a name="inner-join"></a>İç birleşim

İç birleşim yalnızca her iki tabloda eşleşen değerleri olan satırları çıktı.

### <a name="left-outer"></a>Sol dış

Sol dış birleşim, sol akıştaki tüm satırları ve doğru akıştan eşleşen kayıtları döndürür. Sol akıştaki bir satırda eşleşme yoksa, doğru akıştaki çıkış sütunları NULL olarak ayarlanır. Çıktı, bir iç birleşim ile döndürülen satırlar ve sol akıştaki eşleşmeyen satırlar olacaktır.

> [!NOTE]
> Veri akışları tarafından kullanılan Spark altyapısı, bazı durumlarda, JOIN koşullarınızda olası Kartezyen ürünler nedeniyle başarısız olur. Bu durumda, özel bir çapraz birleşime geçebilir ve JOIN koşulunuz el ile girebilirsiniz. Bu durum, yürütme altyapısının ilişkinin her iki tarafından tüm satırları hesaplaması ve sonra satırları filtrelemeniz gerekeceğinden, veri akışlarınızda daha yavaş performans oluşmasına neden olabilir.

### <a name="right-outer"></a>Sağ dış

Sağ dış birleşim, sol akıştaki doğru akıştaki ve eşleşen kayıtlardan tüm satırları döndürür. Doğru akıştaki bir satırda eşleşme yoksa, sol akıştaki çıkış sütunları NULL olarak ayarlanır. Çıktı, bir iç birleşim ile döndürülen satırlar ve doğru akıştan eşleşmeyen satırlar olacaktır.

### <a name="full-outer"></a>Tam dış

Tam dış birleşim, eşleşmeyen sütunlarda NULL değerler ile her iki taraftan da tüm sütunları ve satırları çıktı.

### <a name="custom-cross-join"></a>Özel çapraz ekleme

Çapraz birle, iki akışın çapraz çarpımını bir koşula göre çıktı. Eşitlik olmayan bir koşul kullanıyorsanız, çapraz ekleme koşulunuz olarak bir özel ifade belirtin. Çıkış akışı, JOIN koşulunu karşılayan tüm satırlar olacaktır.

Bu birleştirme türünü, eşlenmemiş birleşimler ve koşullar için kullanabilirsiniz ```OR``` .

Açıkça tam bir Kartezyen ürün oluşturmak isterseniz, eşleştirilecek yapay bir anahtar oluşturmak için, birleştirmenin önüne iki bağımsız akışın her birinde türetilmiş sütun dönüşümünü kullanın. Örneğin, adlı her akıştaki türetilmiş sütunda yeni bir sütun oluşturun ```SyntheticKey``` ve eşit olarak ayarlayın ```1``` . Ardından ```a.SyntheticKey == b.SyntheticKey``` özel JOIN ifadeniz olarak kullanın.

> [!NOTE]
> Özel bir çapraz birleşimde sol ve sağ İlişkinizdeki her bir taraftan en az bir sütun eklediğinizden emin olun. Her bir taraftaki sütunlar yerine statik değerlerle çapraz birleştirmeleri yürütmek, veri akışlarınızın düzgün bir şekilde gerçekleştirmesini sağlamak için tüm veri kümesinin tam taramasına neden olur.

## <a name="configuration"></a>Yapılandırma

1. **Sağ Akış** açılan menüsünde hangi veri akışını katdığınızı seçin.
1. **JOIN türünü** seçin
1. JOIN Koşulunuz için hangi anahtar sütunlarını eşlemek istediğinizi seçin. Varsayılan olarak, veri akışı her akıştaki bir sütun arasında eşitlik arar. Hesaplanan bir değer ile karşılaştırmak için, sütun açılan listesinin üzerine gelin ve **hesaplanan sütun**' u seçin.

![Dönüşümü Birleştir](media/data-flow/join.png "Katılın")

### <a name="non-equi-joins"></a>Eşit olmayan birleşimler

JOIN koşullarınıza eşit (! =) veya büyüktür (>) gibi koşullu bir işleç kullanmak için iki sütun arasındaki operatör açılan listesini değiştirin. Eşitlenmiş olmayan birleşimler, **en iyileştirme** sekmesinde **sabit** yayınlama kullanılarak yayınlanamayacak iki akışdan en az birini gerektirir.

![Eşlenmemiş birleşim](media/data-flow/non-equi-join.png "Eşlenmemiş birleşim")

## <a name="optimizing-join-performance"></a>JOIN performansını iyileştirme

SSIS gibi araçlarla birleştirme birleştirmesinin aksine, JOIN dönüşümü zorunlu bir birleştirme birleştirme işlemi değildir. JOIN anahtarları sıralama gerektirmez. JOIN işlemi, Spark içindeki en iyi JOIN işlemine bağlı olarak, yayın veya harita tarafı birleşimi üzerinden gerçekleşir.

![Dönüştürme iyileştirmelerine Birleştir](media/data-flow/joinoptimize.png "Birleştirmeyi En Iyi duruma getirme")

Birleşimler, aramalar ve mevcut dönüşümde, bir veya her iki veri akışı çalışan düğümü belleğine sığması halinde **yayını** etkinleştirerek performansı iyileştirebilirsiniz. Spark altyapısı, varsayılan olarak bir kenar yayınlanıp yayınlanmayacağını otomatik olarak karar verir. Hangi tarafın yayınlanmak üzere el ile seçmek için, **sabit**' i seçin.

Birleşimlerinizin zaman aşımı hatalarıyla çalışmadığı **durumlar dışında yayınlamayı** devre dışı bırakmanız önerilmez.

## <a name="self-join"></a>Self-Join

Bir veri akışını kendisiyle birleştirmek için, bir seçme dönüşümü olan mevcut bir akışa diğer ad ekleyin. Bir dönüşümün yanındaki artı simgesine tıklayıp **yeni dal**' ı seçerek yeni bir dal oluşturun. Özgün akışa diğer ad için bir seçme dönüşümü ekleyin. Bir JOIN dönüşümü ekleyin ve **sol** akış olarak orijinal akışı ve seçme dönüşümünü **doğru akış** olarak seçin.

![Kendi kendine Birleştir](media/data-flow/selfjoin.png "Kendi kendine Birleştir")

## <a name="testing-join-conditions"></a>Test ekleme koşulları

Hata ayıklama modundaki veri önizlemesiyle JOIN dönüştürmelerinin test edilirken, küçük bir bilinen veri kümesi kullanın. Büyük bir veri kümesinden satırlar örneklenirken, hangi satırların ve anahtarların test için okunacağını tahmin edemeyecektir. Sonuç belirleyici değildir, yani JOIN koşullarınızın herhangi bir eşleşme döndürmeyebilir.

## <a name="data-flow-script"></a>Veri akışı betiği

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>İç birleşim örneği

Aşağıdaki örnek, `JoinMatchedData` sol akış `TripData` ve sağ Akış alan adlı bir JOIN dönüştürmedir `TripFare` .  JOIN koşulu, `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` `hack_license` `medallion` her akıştaki,, `vendor_id` , ve `pickup_datetime` sütunlarının eşleşmesi durumunda true döndüren ifadedir. `joinType` `'inner'` . Yayını yalnızca sol akışta etkinleştiriyoruz, bu yüzden `broadcast` değer vardır `'left'` .

Data Factory UX 'de, bu dönüşüm aşağıdaki görüntüye benzer şekilde görünür:

![Ekran görüntüsü, birleşim ayarları sekmesi seçili olan dönüşümü ve Iç birleşim türünü gösterir.](media/data-flow/join-script1.png "JOIN örneği")

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

### <a name="custom-cross-join-example"></a>Özel çapraz ekleme örneği

Aşağıdaki örnek, `JoiningColumns` sol akış `LeftStream` ve sağ Akış alan adlı bir JOIN dönüştürmedir `RightStream` . Bu dönüşüm iki akış alır ve sütunun sütundan büyük olduğu tüm satırları birleştirir `leftstreamcolumn` `rightstreamcolumn` . `joinType` `cross` . Yayın etkin değil `broadcast` değeri `'none'` .

Data Factory UX 'de, bu dönüşüm aşağıdaki görüntüye benzer şekilde görünür:

![Ekran görüntüsü, JOIN ayarları sekmesi seçili olan dönüşümü ve özel (çapraz) bir JOIN türü gösterir.](media/data-flow/join-script2.png "JOIN örneği")

Bu dönüşüm için veri akışı betiği aşağıdaki kod parçacığında verilmiştir:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Sonraki adımlar

Verileri katıldıktan sonra, türetilmiş bir [sütun](data-flow-derived-column.md) oluşturun ve verilerinizi bir hedef veri deposuna [havuzın](data-flow-sink.md) .
