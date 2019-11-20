---
title: Azure Data Factory eşleme veri akışında toplama dönüştürmesi
description: Veri akışı toplama dönüştürmesiyle Azure Data Factory verileri ölçekte nasıl toplayacağınızı öğrenin.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 3f21367c36ae31aa2115c109933a581bef464baf
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676906"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Eşleme veri akışında toplama dönüştürmesi 

Toplam dönüşümü, veri akışlarınızdan sütunların toplamalarının tanımlar. Ifade oluşturucuyu kullanarak, var olan veya hesaplanan sütunlara göre gruplanmış SUM, MIN, MAX ve COUNT gibi farklı toplamalar türlerini tanımlayabilirsiniz.

## <a name="group-by"></a>Gruplandırma ölçütü

Toplama için bir Group by yan tümcesi olarak kullanılacak yeni bir hesaplanmış sütun oluşturun veya var olan bir sütunu seçin. Varolan bir sütunu kullanmak için açılan listeden seçin. Yeni bir hesaplanan sütun oluşturmak için, yan tümcesinin üzerine gelin ve **hesaplanan sütun**' a tıklayın. Bu, [veri akışı ifade Oluşturucusu](concepts-data-flow-expression-builder.md)'nu açar. Hesaplanan sütununuzu oluşturduktan sonra **ad** alanının altındaki çıkış sütunu adını girin. Ek bir Group by yan tümcesi eklemek istiyorsanız, var olan bir yan tümcenin üzerine gelin ve artı simgesine tıklayın.

![Ayarlara göre toplama dönüştürme grubu](media/data-flow/agg.png "Ayarlara göre toplama dönüştürme grubu")

Toplama dönüşümünde Group by yan tümcesi isteğe bağlıdır.

## <a name="aggregate-column"></a>Toplama sütunu 

Toplama ifadeleri oluşturmak için **Toplamlar** sekmesine gidin. Bir toplama ile var olan bir sütunun üzerine yazabilir ya da yeni bir adla yeni bir alan oluşturabilirsiniz. Toplama ifadesi, sütun adı seçicisinin yanındaki sağ kutuya girilir. İfadeyi düzenlemek için, metin kutusuna tıklayarak ifade oluşturucuyu açın. Ek toplamalar eklemek için mevcut bir ifadenin üzerine gelin ve artı simgesine tıklayarak yeni bir toplama sütunu veya [sütun stili](concepts-data-flow-column-pattern.md)oluşturun.

Her toplama ifadesi en az bir toplama işlevi içermelidir.

![Toplu dönüştürme toplama ayarları](media/data-flow/agg2.png "Toplu dönüştürme toplama ayarları")


> [!NOTE]
> Hata ayıklama modunda, ifade Oluşturucusu toplama işlevleriyle veri önizlemeleri üretemiyor. Toplama dönüştürmelerinin veri önizlemelerini görüntülemek için, ifade oluşturucuyu kapatın ve verileri ' veri önizleme ' sekmesi aracılığıyla görüntüleyin.

## <a name="reconnect-rows-and-columns"></a>Satırları ve sütunları yeniden bağla

Toplam dönüşümler SQL toplama seçme sorgularıyla benzerdir. Group by yan tümcesine veya toplama işlevlerine dahil olmayan sütunlar, toplu dönüşümünüzün çıkışına kadar akmaz. Toplanan çıkışındaki diğer sütunları eklemek istiyorsanız aşağıdaki yöntemlerden birini yapın:

* Bu ek sütunu eklemek için `last()` veya `first()` gibi bir toplama işlevi kullanın.
* [Kendi kendine JOIN modelini](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)kullanarak sütunları çıkış akışla yeniden katın.

## <a name="data-flow-script"></a>Veri akışı betiği

### <a name="syntax"></a>Sözdizimi

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Örnek

Aşağıdaki örnek, `MoviesYear` gelen bir akış alır ve satırları sütun `year`göre gruplandırır. Dönüştürme, sütun `Rating`ortalamasını değerlendiren bir toplama sütunu `avgrating` oluşturur. Bu toplama dönüştürmesi `AvgComedyRatingsByYear`olarak adlandırılır.

Data Factory UX 'de, bu dönüşüm aşağıdaki görüntüye benzer şekilde görünür:

![Örneğe göre Gruplandır](media/data-flow/agg-script1.png "Örneğe göre Gruplandır")

![Birleşik örnek](media/data-flow/agg-script2.png "Birleşik örnek")

Bu dönüşüm için veri akışı betiği aşağıdaki kod parçacığında bulunur.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Sonraki adımlar

* Pencere [dönüşümünü](data-flow-window.md) kullanarak pencere tabanlı toplamayı tanımlama
