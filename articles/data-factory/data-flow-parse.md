---
title: Eşleme veri akışında veri dönüşümünü ayrıştırma
description: Katıştırılmış sütun belgelerini ayrıştırma
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 4db9503ea84ae13148a89a03048c73399413e5cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101710201"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>Eşleme veri akışında dönüşüm ayrıştırma

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Verilerinizde belge biçiminde olan sütunları ayrıştırmak için ayrıştırma dönüşümünü kullanın. Ayrıştırılabilen gömülü belgelerin geçerli desteklenen türleri JSON ve sınırlandırılmış metinlerdir.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>Yapılandırma

Dönüştürme yapılandırma panelinde, ilk olarak satır içi ayrıştırmak istediğiniz sütunlarda yer alan veri türünü seçmeniz gerekir. Ayrıştırma dönüştürmesi aşağıdaki yapılandırma ayarlarını da içerir.

![Ayrıştırma ayarları](media/data-flow/data-flow-parse-1.png "Ayrıştır")

### <a name="column"></a>Sütun

Türetilmiş sütunlara ve toplamalara benzer şekilde, bir çıkış sütununu aşağı açılan seçicisinden seçerek değiştirirsiniz. Veya buraya yeni bir sütunun adını yazabilirsiniz. ADF, ayrıştırılmış kaynak verilerini bu sütunda depolar.

### <a name="expression"></a>Expression

Ayrıştırmanın kaynağını ayarlamak için ifade oluşturucusunu kullanın. Bu, ayrıştırmak istediğiniz kendi içinde bulunan verilerle kaynak sütunu seçmek kadar basit olabilir veya ayrıştırılacak karmaşık ifadeler oluşturabilirsiniz.

### <a name="output-column-type"></a>Çıkış sütunu türü

Burada, hedef çıktı şemasını tek bir sütuna yazılacak ayrıştırmadan yapılandıracaksınız.

![Parse örneği](media/data-flow/data-flow-parse-2.png "Parse örneği")

Bu örnekte, düz metin olan, ancak JSON yapısı olarak biçimlendirilen "jsonString" gelen alanını ayrıştırmayı tanımlıyoruz. Ayrıştırılmış sonuçları, bu şemaya sahip "JSON" adlı yeni bir sütunda JSON olarak depolayacağız:

```(trade as boolean, customers as string[])```

Çıktlarınızın doğru şekilde eşlendiğini doğrulamak için İnceleme sekmesine ve veri önizlemesine bakın.

## <a name="examples"></a>Örnekler

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    documentForm: 'documentPerLine') ~> JsonSource
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> CsvSource
JsonSource derive(jsonString = toString(goods)) ~> StringifyJson
StringifyJson parse(json = jsonString ? (trade as boolean,
        customers as string[]),
    format: 'json',
    documentForm: 'arrayOfDocuments') ~> ParseJson
CsvSource derive(csvString = 'Id|name|year\n\'1\'|\'test1\'|\'1999\'') ~> CsvString
CsvString parse(csv = csvString ? (id as integer,
        name as string,
        year as string),
    format: 'delimited',
    columnNamesAsHeader: true,
    columnDelimiter: '|',
    nullValue: '',
    documentForm: 'documentPerLine') ~> ParseCsv
ParseJson select(mapColumn(
        jsonString,
        json
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedJson
ParseCsv select(mapColumn(
        csvString,
        csv
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedCsv
```

## <a name="data-flow-script"></a>Veri akışı betiği

### <a name="syntax"></a>Syntax

### <a name="examples"></a>Örnekler

```
parse(json = jsonString ? (trade as boolean,
                                customers as string[]),
                format: 'json',
                documentForm: 'singleDocument') ~> ParseJson

parse(csv = csvString ? (id as integer,
                                name as string,
                                year as string),
                format: 'delimited',
                columnNamesAsHeader: true,
                columnDelimiter: '|',
                nullValue: '',
                documentForm: 'documentPerLine') ~> ParseCsv
```    

## <a name="next-steps"></a>Sonraki adımlar

* Satırları sütunlara eklemek için [Düzleştir dönüşümünü](data-flow-flatten.md) kullanın.
* Sütunları satırlara pivot olarak eklemek için [türetilmiş sütun dönüşümünü](data-flow-derived-column.md) kullanın.
