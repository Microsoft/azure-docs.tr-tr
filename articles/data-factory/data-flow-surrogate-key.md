---
title: Eşleme veri akışında vekil anahtar dönüşümü
description: Azure Data Factory eşleme veri akışı yedek anahtar dönüşümü kullanarak sıralı anahtar değerleri oluşturma
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/30/2020
ms.openlocfilehash: d1f8993b1adc297b1bfadba114df76a66e59afa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93147193"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Eşleme veri akışında vekil anahtar dönüşümü 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Her bir veri satırına bir artırma anahtarı değeri eklemek için vekil anahtar dönüşümünü kullanın. Bu, bir yıldız şeması analitik veri modelinde boyut tabloları tasarlarken yararlı olur. Bir yıldız şemasında, boyut tablolarınızdaki her üye, iş dışı bir anahtar olan benzersiz bir anahtar gerektirir.

## <a name="configuration"></a>Yapılandırma

![Vekil anahtar dönüşümü](media/data-flow/surrogate.png "Vekil anahtar dönüşümü")

**Anahtar sütun:** Oluşturulan vekil anahtar sütununun adı.

**Başlangıç değeri:** Oluşturulacak en düşük anahtar değeri.

## <a name="increment-keys-from-existing-sources"></a>Mevcut kaynaklardan anahtarları artırma

Bir kaynakta bulunan bir değerden dizinizi başlatmak için, bu değeri kaydetmek için bir önbellek havuzu kullanmanız ve iki değeri birlikte eklemek için türetilmiş bir sütun dönüşümü kullanmanız önerilir. Çıktıyı almak ve oluşturulan anahtara eklemek için önbelleğe alınmış bir arama kullanın. Daha fazla bilgi için, [önbellek havuzları](data-flow-sink.md#cache-sink) ve [önbelleğe alınmış aramalar](concepts-data-flow-expression-builder.md#cached-lookup)hakkında bilgi edinin.

![Vekil anahtar arama](media/data-flow/cached-lookup-example.png "Vekil anahtar arama")

### <a name="increment-from-existing-maximum-value"></a>Varolan en büyük değerden artış

Anahtar değerini önceki maksimum ile temel almak için, kaynak verilerinizin bulunduğu yere göre kullanabileceğiniz iki teknik vardır.

#### <a name="database-sources"></a>Veritabanı kaynakları

Kaynağınızdan MAX () ' i seçmek için bir SQL sorgu seçeneği kullanın. Örneğin, `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`.

![Vekil anahtar sorgusu](media/data-flow/surrogate-key-max-database.png "Vekil anahtar dönüştürme sorgusu")

#### <a name="file-sources"></a>Dosya kaynakları

Önceki en büyük değer bir dosya içinde ise, `max()` önceki en büyük değeri almak için toplu dönüşümde işlevini kullanın:

![Vekil anahtar dosyası](media/data-flow/surrogate-key-max-file.png "Vekil anahtar dosyası")

Her iki durumda da bir önbellek havuzuna yazmanız ve değeri yapmanız gerekir. 


## <a name="data-flow-script"></a>Veri akışı betiği

### <a name="syntax"></a>Syntax

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Örnek

![Vekil anahtar dönüşümü](media/data-flow/surrogate.png "Vekil anahtar dönüşümü")

Yukarıdaki yedek anahtar yapılandırması için veri akışı betiği aşağıdaki kod parçacığında bulunur.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Sonraki adımlar

Bu örnekler, [JOIN](data-flow-join.md) ve [türetilmiş sütun](data-flow-derived-column.md) dönüşümlerini kullanır.
