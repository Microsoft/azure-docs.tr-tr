---
title: Azure Data Factory eşleme veri akışında filtre dönüşümü
description: Azure Data Factory eşleme veri akışındaki filtre dönüşümünü kullanarak satırları filtreleme
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 1daff431fc217c08f3bc3c5aeb3b4711691909c0
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132529"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Eşleme veri akışında filtre dönüşümü

Filtre dönüştürmeleri bir koşula göre satır filtrelemesine izin verir. Çıkış akışı, filtreleme durumuyla eşleşen tüm satırları içerir. Filtre dönüşümü SQL 'deki WHERE yan tümcesine benzerdir.

## <a name="configuration"></a>Yapılandırma

Filtre koşulu için bir ifade girmek üzere veri akışı ifade oluşturucusunu kullanın. İfade oluşturucuyu açmak için mavi kutuya tıklayın. Filtre koşulu Boolean türünde olmalıdır. Bir ifade oluşturma hakkında daha fazla bilgi için bkz. [Expression Builder](concepts-data-flow-expression-builder.md) belgeleri.

![Filtre dönüşümü](media/data-flow/filter1.png "Filtre dönüşümü")

## <a name="data-flow-script"></a>Veri akışı betiği

### <a name="syntax"></a>Sözdizimi

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Örnek

Aşağıdaki örnek, gelen akış `CleanData`alan `FilterBefore1960` adlı bir filtre dönüştürmesinin bir örneğidir. Filtre koşulu `year <= 1960`ifadedir.

Data Factory UX 'de, bu dönüşüm aşağıdaki görüntüye benzer şekilde görünür:

![Filtre dönüşümü](media/data-flow/filter1.png "Filtre dönüşümü")

Bu dönüşüm için veri akışı betiği aşağıdaki kod parçacığında verilmiştir:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Sonraki adımlar

Sütunları [seçim dönüşümüne](data-flow-select.md) göre filtrele
