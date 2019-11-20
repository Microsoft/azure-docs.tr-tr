---
title: Azure Data Factory eşleme veri akışında dönüştürme var
description: Azure Data Factory eşleme veri akışında var olan dönüştürmeyi kullanarak mevcut satırları denetleyin
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: a477eba246c4ebcfbd32e92f1fd30c301ea1cc5b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676724"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Eşleme veri akışında dönüştürme var

Var olan dönüştürme, verilerinizin başka bir kaynakta veya akışta bulunup bulunmadığını denetleyen bir satır filtreleme dönüşümünüze sahiptir. Çıkış akışı, sol akıştaki mevcut veya doğru akışta bulunmayan tüm satırları içerir. Var olan dönüştürme ```SQL WHERE EXISTS``` ve ```SQL WHERE NOT EXISTS```benzerdir.

## <a name="configuration"></a>Yapılandırma

1. **Sağ Akış** açılan menüsünde var olan veri akışını kontrol ettiğiniz verileri seçin.
1. Mevcut **tür** ayarında verilerin mevcut olup olmadığını mı yoksa mevcut mi olduğunu belirtin.
1. **Özel bir ifade**isteyip istemediğinizi seçin.
1. Mevcut koşullarınız olarak karşılaştırmak istediğiniz anahtar sütunları seçin. Varsayılan olarak, veri akışı her akıştaki bir sütun arasında eşitlik arar. Hesaplanan bir değer ile karşılaştırmak için, sütun açılan listesinin üzerine gelin ve **hesaplanan sütun**' u seçin.

![Mevcut ayarlar](media/data-flow/exists.png "1 var")

### <a name="multiple-exists-conditions"></a>Birden çok mevcut koşul

Her akıştaki birden çok sütunu karşılaştırmak için var olan bir satırın yanındaki artı simgesine tıklayarak yeni bir mevcut koşul ekleyin. Her ek koşul bir "and" ifadesiyle birleştirilir. İki sütunu karşılaştırmak aşağıdaki ifadeyle aynıdır:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Özel ifade

"Ve" ve "eşittir" dışındaki işleçleri içeren serbest biçimli bir ifade oluşturmak için **özel ifade** alanını seçin. Mavi kutuya tıklayarak veri akışı ifade Oluşturucusu aracılığıyla özel bir ifade girin.

![Özel ayarları var](media/data-flow/exists1.png "Özel var")

## <a name="data-flow-script"></a>Veri akışı betiği

### <a name="syntax"></a>Sözdizimi

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Örnek

Aşağıdaki örnek, `checkForChanges` adlı, sol akış `NameNorm2` ve sağ Akış `TypeConversions`alan, var olan bir dönüşümdir.  EXISTS koşulu, her bir akışta `EMPID` ve `Region` sütunları eşleşiyorsa true döndüren ifadedir `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region`. Varlığını denetliyoruz, `negate` false 'tur. En iyileştirme sekmesinde herhangi bir yayını etkinleştirmedik, `broadcast` değer `'none'`.

Data Factory UX 'de, bu dönüşüm aşağıdaki görüntüye benzer şekilde görünür:

![Mevcut örnek](media/data-flow/exists-script.png "Mevcut örnek")

Bu dönüşüm için veri akışı betiği aşağıdaki kod parçacığında verilmiştir:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Sonraki adımlar

Benzer dönüşümler [arama](data-flow-lookup.md) ve [birleşimdir](data-flow-join.md).
