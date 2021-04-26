---
title: Eşleme veri akışında sıralama dönüşümü
description: Azure Data Factory eşleme veri sıralama dönüşümü
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 4a6567f8576e2507704956233bc593b203b48239
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588743"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Eşleme veri akışında sıralama dönüşümü

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Sıralama dönüştürmesi, geçerli veri akışındaki gelen satırları sıralamanıza olanak tanır. Sütunları tek tek seçebilir ve artan veya azalan sırada sıralayabilirsiniz.

> [!NOTE]
> Eşleme veri akışları, verileri birden çok düğüm ve bölüm arasında dağıtan Spark kümelerinde yürütülür. Verilerinizi sonraki bir dönüşümde yeniden bölümlendirmayı seçerseniz, reshuffling veri nedeniyle sıralıyorsanız kaybedebilirsiniz. Veri akışınızda sıralama sırasını tutmanın en iyi yolu, dönüştürmenin En Iyi hale getirilmiş sekmesinde tek bir bölüm ayarlamak ve sıralama dönüşümünü mümkün olduğunca geri doğru tutacaktır.

## <a name="configuration"></a>Yapılandırma

![Sıralama ayarları](media/data-flow/sort.png "Sırala")

**Büyük/küçük harf duyarsız:** Dize veya metin alanlarını sıralarken büyük/küçük harf yoksaymak isteyip istemediğiniz

**Yalnızca bölümler Içinde Sırala:** Spark üzerinde veri akışları çalıştırıldığında, her veri akışı bölümlere ayrılır. Bu ayar veri akışının tamamını sıralamak yerine yalnızca gelen bölümlerin içindeki verileri sıralar. 

**Sıralama koşulları:** Sıralama yaptığınız sütunları ve sıralamanın ne sırada olacağını seçin. Sıra sıralama önceliğini belirler. Null değerleri veri akışının başlangıcında veya sonunda görünüp görünmeyeceğini seçin.

### <a name="computed-columns"></a>Hesaplanan sütunlar

Sıralamayı uygulamadan önce bir sütun değerini değiştirmek veya ayıklamak için sütunun üzerine gelin ve "hesaplanan sütun" seçeneğini belirleyin. Bu, bir sütun değeri kullanmak yerine sıralama işlemi için bir ifade oluşturmak üzere ifade oluşturucuyu açar.

## <a name="data-flow-script"></a>Veri akışı betiği

### <a name="syntax"></a>Syntax

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Örnek

![Sıralama ayarları](media/data-flow/sort.png "Sırala")

Yukarıdaki sıralama yapılandırması için veri akışı betiği aşağıdaki kod parçacığında bulunur.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Sonraki adımlar

Sıralama sonrasında, [Toplam dönüştürmeyi](data-flow-aggregate.md) kullanmak isteyebilirsiniz
