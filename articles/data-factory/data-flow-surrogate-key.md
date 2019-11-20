---
title: Azure Data Factory eşleme veri akışı yedek anahtar dönüştürmesi
description: Azure Data Factory eşleme veri akışı yedek anahtar dönüşümü kullanarak sıralı anahtar değerleri oluşturma
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 144d6298a13d35d94a68b35c443a3a47cefcfc2a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387173"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Veri akışı yedek anahtar dönüşümünü eşleme



Veri akışı satır kümesine artan iş dışı bir anahtar değeri eklemek için vekil anahtar dönüşümünü kullanın. Bu, boyut tablolarınızdaki her üyenin, Kıbol DW metodolojisinin bir parçası olan iş dışı bir anahtar olan benzersiz bir anahtara sahip olması gereken bir yıldız şeması analitik veri modelinde, boyut tabloları tasarlarken yararlıdır.

![Vekil anahtar dönüşümü](media/data-flow/surrogate.png "Vekil anahtar dönüşümü")

"Anahtar sütun", yeni yedek anahtar sütununuzu verdiğiniz addır.

"Başlangıç değeri", artımlı değerin başlangıç noktasıdır.

## <a name="increment-keys-from-existing-sources"></a>Mevcut kaynaklardan anahtarları artırma

Dizinizi bir kaynakta bulunan bir değerden başlatmak isterseniz, yedek anahtar dönüşümünüzü hemen izleyerek türetilmiş bir sütun dönüştürmesi kullanabilir ve iki değeri birlikte ekleyebilirsiniz:

![SK en fazla Ekle](media/data-flow/sk006.png "Vekil anahtar dönüştürme en fazla Ekle")

Anahtar değerini önceki üst sınır ile temel almak için kullanabileceğiniz iki teknik vardır:

### <a name="database-sources"></a>Veritabanı kaynakları

Kaynak dönüşümünü kullanarak kaynağınızdan MAX () ' i seçmek için "sorgu" seçeneğini kullanın:

![Vekil anahtar sorgusu](media/data-flow/sk002.png "Vekil anahtar dönüştürme sorgusu")

### <a name="file-sources"></a>Dosya kaynakları

Önceki en büyük değer bir dosya içinde ise, kaynak dönüşümünüzü bir toplam dönüşümle birlikte kullanabilir ve en büyük () ifade işlevini kullanarak önceki en büyük değeri alabilirsiniz:

![Vekil anahtar dosyası](media/data-flow/sk008.png "Vekil anahtar dosyası")

Her iki durumda da, gelen yeni verilerinizi önceki en büyük değeri içeren kaynakla birlikte katılmanız gerekir:

![Vekil anahtar birleşimi](media/data-flow/sk004.png "Vekil anahtar birleşimi")

## <a name="next-steps"></a>Sonraki adımlar

Bu örnekler, [JOIN](data-flow-join.md) ve [türetilmiş sütun](data-flow-derived-column.md) dönüşümlerini kullanır.
