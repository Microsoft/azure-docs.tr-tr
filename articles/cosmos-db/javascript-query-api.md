---
title: Azure Cosmos DB saklı yordamlar ve Tetikleyiciler 'de JavaScript tümleşik sorgu API 'SI ile çalışma
description: Bu makalede, Azure Cosmos DB ' de saklı yordamlar ve Tetikleyiciler oluşturmak için JavaScript dil ile tümleşik sorgu API 'SI kavramları tanıtılmaktadır.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: tisande
ms.reviewer: sngun
ms.custom: devx-track-js
ms.openlocfilehash: b2563a9af0e0ca6943059698e29d139143780d93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341000"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Azure Cosmos DB’de JavaScript sorgu API’si
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB SQL API 'sini kullanarak sorgu vermekten ek olarak, [Cosmos DB sunucu tarafı SDK 'sı](https://github.com/Azure/azure-cosmosdb-js-server/) Cosmos DB saklı yordamlar ve tetikleyicilerinde iyileştirilmiş sorgular gerçekleştirmeye yönelik bir JavaScript arabirimi sağlar. Bu JavaScript arabirimini kullanmak için SQL dilini bilmeniz gerekmez. JavaScript sorgu API 'SI, koşul işlevlerini, ECMAScript5's dizisi yerleşik ve Lodash gibi popüler JavaScript kitaplıklarını bilen bir sözdizimi ile program aracılığıyla sorgu oluşturmanızı sağlar. Sorgular JavaScript çalışma zamanı tarafından ayrıştırılır ve Azure Cosmos DB dizinleri kullanılarak verimli bir şekilde yürütülür.

## <a name="supported-javascript-functions"></a>Desteklenen JavaScript işlevleri

| **İşlev** | **Açıklama** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Değeri () ile sonlandırılması gereken bir zincir çağrısını başlatır.|
|`filter(predicateFunction [, options] [, callback])`|Giriş belgelerini sonuç kümesine filtrelemek için doğru/yanlış döndüren bir koşul işlevi kullanarak girişi filtreler. Bu işlev SQL 'de WHERE yan tümcesine benzer şekilde davranır.|
|`flatten([isShallow] [, options] [, callback])`|Her giriş öğesindeki dizileri tek bir dizide birleştirir ve düzleştirir. Bu işlev, LINQ içinde SelectMany ile benzer şekilde davranır.|
|`map(transformationFunction [, options] [, callback])`|Her giriş öğesini bir JavaScript nesnesine veya değerine eşleyen bir dönüştürme işlevi verilen bir projeksiyon uygular. Bu işlev, SQL 'deki bir SELECT yan tümcesine benzer şekilde davranır.|
|`pluck([propertyName] [, options] [, callback])`|Bu işlev, her giriş öğesinden tek bir özelliğin değerini çıkaran bir haritanın kısayoludur.|
|`sortBy([predicate] [, options] [, callback])`|Giriş belge akışındaki belgeleri verilen koşulu kullanarak artan düzende sıralayarak yeni bir belge kümesi oluşturur. Bu işlev, SQL 'deki ORDER BY yan tümcesine benzer şekilde davranır.|
|`sortByDescending([predicate] [, options] [, callback])`|Verilen koşulu kullanarak, giriş belgesi akışındaki belgeleri azalan sırada sıralayarak yeni bir belge kümesi oluşturur. Bu işlev, SQL 'deki ORDER BY x DESC yan tümcesine benzer şekilde davranır.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|İç diziye bir kendi kendine birleşim gerçekleştirir ve sonuç projeksiyonunda, her iki taraftan da sonuçları tanımlama grubu olarak ekler. Örneğin, bir kişi belgesine kişi. pets ile katılmak, [Person, Evcil hayvan] tanımlama gruplarını üretecektir. Bu, .NET BAĞLANTıSıNıN SelectMany ile benzerdir.|

Koşul ve/veya seçici işlevlerinin içine dahil edildiğinde, aşağıdaki JavaScript yapıları doğrudan Azure Cosmos DB dizinleri üzerinde çalışacak şekilde otomatik olarak iyileştirilir:

- Basit işleçler `=` `+` `-` `*` : `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!``~`
- Nesne değişmez değeri dahil değişmez değerler: {}
- var, return

Aşağıdaki JavaScript yapıları Azure Cosmos DB dizinleri için iyileştirilmez:

- Denetim akışı (örneğin, for, IF)
- İşlev çağrıları

Daha fazla bilgi için bkz. [sunucu tarafı JavaScript belgeleri Cosmos DB](https://github.com/Azure/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>SQL 'den JavaScript 'e başvuru sayfası

Aşağıdaki tabloda çeşitli SQL sorguları ve ilgili JavaScript sorguları sunulmaktadır. SQL sorgularında olduğu gibi Özellikler (örneğin, item.id) büyük/küçük harfe duyarlıdır.

> [!NOTE]
> `__` (çift alt çizgi), `getContext().getCollection()` JavaScript sorgu API 'si kullanılırken bir diğer addır.

|**SQL**|**JavaScript sorgu API 'SI**|**Açıklama**|
|---|---|---|
|SEÇIN<br>Docs 'TAN| __. map (işlev (doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;belgeyi döndür;<br>});|Tüm belgelere (devamlılık belirteci ile sayfalandırılmış olarak) sahip olur.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;Msg olarak docs. Message,<br>&nbsp;&nbsp;&nbsp;docs. Actions <br>Docs 'TAN|__. map (işlev (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;döndürülmesini<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Kimlik: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Msg: Doc. Message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Eylemler: Doc. Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Kimliği, iletiyi (diğer adı msg) ve tüm belgelerden yapılacak işlemleri projeler.|
|SEÇIN<br>Docs 'TAN<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs. ID = "X998_Y998"|__. Filter (işlev (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Return doc.id = = = "X998_Y998";<br>});|Koşul: ID = "X998_Y998" olan belgeler için sorgular.|
|SEÇIN<br>Docs 'TAN<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (docs. Etiketler, 123)|__. Filter (işlev (x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;x. Tags. IndexOf (123) && x. Tags döndürün >-1;<br>});|Etiketler özelliği ve etiketleri olan belge sorguları 123 değerini içeren bir dizidir.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;Msg olarak docs. Message<br>Docs 'TAN<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs. ID = "X998_Y998"|__. zinciri ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. Filter (işlev (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Return doc.id = = = "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. map (işlev (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;döndürülmesini<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Kimlik: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Msg: Doc. Message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>. Value ();|Bir koşula sahip belge için sorgular, ID = "X998_Y998" ve ardından kimliği ve iletiyi (diğer adıyla msg) projeler.|
|DEĞER etiketini Seç<br>Docs 'TAN<br>Docs 'TA etiketi BIrLEŞTIr. Lerimi<br>SıRALAMA ölçütü docs._ts|__. zinciri ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. Filter (işlev (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;belgeyi döndür. Array. IsArray (doc && etiketleri. Etiketler);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. sortBy (Function (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dönüş doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. pluck ("Etiketler")<br>&nbsp;&nbsp;&nbsp;&nbsp;. Düzleştir ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. Value ()|Bir dizi özelliğine, etikete sahip ve sonuç belgelerini _ts zaman damgası sistemi özelliğine göre sıralayan belgeler ve ardından projeler + Etiketler dizisini düzleştirir.|

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB ' de saklı yordamları, Tetikleyicileri ve Kullanıcı tanımlı işlevleri yazma ve kullanma hakkında daha fazla bilgi edinin:

- [JavaScript sorgu API 'sini kullanarak saklı yordamları ve Tetikleyicileri yazma](how-to-write-javascript-query-api.md)
- [Azure Cosmos DB saklı yordamlar, Tetikleyiciler ve Kullanıcı tanımlı işlevlerle çalışma](stored-procedures-triggers-udfs.md)
- [Saklı yordamları, Tetikleyicileri, Kullanıcı tanımlı işlevleri Azure Cosmos DB kullanma](how-to-use-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB JavaScript sunucu tarafı API başvurusu](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
