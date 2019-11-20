---
title: Azure Cosmos DB 'de JavaScript dil ile tümleşik sorgu API 'SI ile çalışma
description: Bu makalede, Azure Cosmos DB ' de saklı yordamlar ve Tetikleyiciler oluşturmak için JavaScript dil ile tümleşik sorgu API 'SI kavramları tanıtılmaktadır.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 01e5e95da3c19c03d07c7f3c1d716f5f1e97de98
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717586"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Azure Cosmos DB 'de JavaScript sorgu API 'SI

Azure Cosmos DB SQL API 'sini kullanarak sorgu vermekten ek olarak, [Cosmos DB sunucu tarafı SDK 'sı](https://azure.github.io/azure-cosmosdb-js-server/) , JavaScript arabirimini kullanarak iyileştirilmiş sorgular gerçekleştirmenize olanak tanır. Bu JavaScript arabirimini kullanmak için SQL dilini bilmeniz gerekmez. JavaScript sorgu API 'SI, koşul işlevlerini, ECMAScript5's dizisi yerleşik ve Lodash gibi popüler JavaScript kitaplıklarını bilen bir sözdizimi ile program aracılığıyla sorgu oluşturmanızı sağlar. Sorgular JavaScript çalışma zamanı tarafından ayrıştırılır ve Azure Cosmos DB dizinleri kullanılarak verimli bir şekilde yürütülür.

## <a name="supported-javascript-functions"></a>Desteklenen JavaScript işlevleri

| **Çalışmayacaktır** | **Açıklama** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Value()) ile bitirilmelidir zincirleme bir arama başlatır.|
|`filter(predicateFunction [, options] [, callback])`|Sonuç kümesine girdi belgelerini içeri/dışarı filtrelemek için true/false döndüren bir koşul işlevini kullanarak giriş filtreler. Bu işlev bir WHERE yan tümcesinde SQL benzer şekilde davranır.|
|`flatten([isShallow] [, options] [, callback])`|Her giriş öğesindeki dizileri tek bir dizide birleştirir ve düzleştirir. Bu işlev SelectMany LINQ benzer şekilde davranır.|
|`map(transformationFunction [, options] [, callback])`|Her giriş öğesini bir JavaScript nesnesi veya değer eşleyen bir dönüştürme işlevi verilen bir projeksiyon geçerlidir. Bu işlev bir SELECT yan tümcesi SQL benzer şekilde davranır.|
|`pluck([propertyName] [, options] [, callback])`|Bu işlev, her giriş öğesinden tek bir özelliğinin değerini ayıklar bir eşleme için bir kısayoldur.|
|`sortBy([predicate] [, options] [, callback])`|Giriş belge akışındaki belgeleri verilen koşulu kullanarak artan düzende sıralayarak yeni bir belge kümesi oluşturur. Bu işlev bir ORDER BY yan tümcesi içinde SQL benzer şekilde davranır.|
|`sortByDescending([predicate] [, options] [, callback])`|Verilen koşulu kullanarak, giriş belgesi akışındaki belgeleri azalan sırada sıralayarak yeni bir belge kümesi oluşturur. Bu işlev bir x DESC ORDER BY yan tümcesinde SQL benzer şekilde davranır.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|İç diziye bir kendi kendine birleşim gerçekleştirir ve sonuç projeksiyonunda, her iki taraftan da sonuçları tanımlama grubu olarak ekler. Örneğin, bir kişi belgesine kişi. pets ile katılmak, [Person, Evcil hayvan] tanımlama gruplarını üretecektir. Bu, .NET BAĞLANTıSıNıN SelectMany ile benzerdir.|

Koşul ve/veya Seçici işlevlerinin içine dahil edilirse, aşağıdaki JavaScript yapıları otomatik olarak doğrudan Azure Cosmos DB dizinlerini çalıştırılmak üzere iyileştirilen:

- Basit işleçler: `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!``~`
- Nesne sabit değeri de dahil olmak üzere hazır: {}
- dönüş var

Aşağıdaki JavaScript yapıları için Azure Cosmos DB dizinleri en iyi duruma getirilmiş değil:

- Denetim akışı (örneğin, varsa, ancak)
- İşlev çağrıları

Daha fazla bilgi için bkz. [sunucu tarafı JavaScript belgeleri Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>SQL 'den JavaScript 'e başvuru sayfası

Aşağıdaki tabloda, çeşitli SQL sorguları ve karşılık gelen JavaScript sorguları sunulmaktadır. SQL sorgularında olduğu gibi Özellikler (örneğin, item.id) büyük/küçük harfe duyarlıdır.

> [!NOTE]
> `__`(çift alt çizgi), JavaScript sorgu API `getContext().getCollection()` 'si kullanılırken bir diğer addır.

|**SQL**|**JavaScript sorgu API 'SI**|**Açıklama**|
|---|---|---|
|SEÇİN *<br>Belgelerinden| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;Belge döndürür;<br>});|Sonuç tüm belgelerde (devamlılık belirteci ile sayfalandırılmış) olur.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;Msg olarak docs. Message,<br>&nbsp;&nbsp;&nbsp;docs. Actions <br>Belgelerinden|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;{döndürür<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Kimliği, ileti (diğer adlı msg için) ve tüm belgeleri eylemden projeleri.|
|SEÇİN *<br>Belgelerinden<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs. ID = "X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;doc.id dönüş === "X998_Y998";<br>});|Sorgular için koşul belgelerle: kimlik = "X998_Y998".|
|SEÇİN *<br>Belgelerinden<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (docs. Etiketler, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;x.Tags dönüş & & x.Tags.indexOf(123) > -1;<br>});|Etiketler özelliği ve etiketlere sahip olan belgeler için sorgulardır 123 değerini içeren bir dizi.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;Msg olarak docs. Message<br>Belgelerinden<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs. ID = "X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;doc.id dönüş === "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{döndürür<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.Value();|Bir koşul ile belgeler için sorgu kimliği = "X998_Y998" ve ardından kodu ve iletinin (ileti için diğer adlı) projeleri.|
|DEĞER etiketi<br>Belgelerinden<br>Etiket IN docs katılın. Etiketleri<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Belge döndürür. Etiketler & & Array.isArray (doc. Etiketler);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;doc._ts döndürür;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Value()|Etiketler, bir dizi özelliği ve elde edilen belgeler _ts zaman damgası sistem özelliği sıralar ve ardından projeleri + etiketler dizisi düzleştirir belgeler için filtreler.|

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB ' de saklı yordamları, Tetikleyicileri ve Kullanıcı tanımlı işlevleri yazma ve kullanma hakkında daha fazla bilgi edinin:

- [JavaScript sorgu API 'sini kullanarak saklı yordamları ve Tetikleyicileri yazma](how-to-write-javascript-query-api.md)
- [Azure Cosmos DB saklı yordamlar, Tetikleyiciler ve Kullanıcı tanımlı işlevlerle çalışma](stored-procedures-triggers-udfs.md)
- [Saklı yordamları, Tetikleyicileri, Kullanıcı tanımlı işlevleri Azure Cosmos DB kullanma](how-to-use-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB JavaScript sunucu tarafı API Başvurusu](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
