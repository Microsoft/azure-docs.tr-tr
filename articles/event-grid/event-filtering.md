---
title: Azure Event Grid için olay filtreleme
description: Azure Event Grid aboneliği oluştururken olayların nasıl filtreleneceğini açıklar.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: b5439b77b86d42d062cf9da66ce678f04f46f813
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256098"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Event Grid abonelikleri için olay filtrelemeyi anlayın

Bu makalede, hangi olayların uç noktanıza gönderileceğini filtrelemeye yönelik farklı yollar açıklanmaktadır. Bir olay aboneliği oluştururken filtreleme için üç seçeneğiniz vardır:

* Olay türleri
* Konu şununla başlıyor veya şununla bitiyor
* Gelişmiş alanlar ve işleçler

## <a name="event-type-filtering"></a>Olay türü filtreleme

Varsayılan olarak, olay kaynağı için tüm [olay türleri](event-schema.md) uç noktaya gönderilir. Uç noktanıza yalnızca belirli olay türlerini gönderilmeye karar verebilirsiniz. Örneğin, kaynaklarınız için güncelleştirmeler hakkında bildirim alabilir, ancak silme işlemleri gibi diğer işlemlere yönelik bilgilendirilirsiniz. Bu durumda, olay türüne göre filtreleyin `Microsoft.Resources.ResourceWriteSuccess` . Olay türlerine sahip bir dizi sağlayın veya `All` olay kaynağı için tüm olay türlerini almak üzere belirtin.

Olay türüne göre filtreleme için JSON sözdizimi şöyledir:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Konu filtreleme

Konuya göre basit filtreleme için, konu için bir başlangıç veya bitiş değeri belirtin. Örneğin, konunun bittiğini `.txt` yalnızca bir metin dosyasını depolama hesabına yüklemeyle ilgili olayları almak üzere belirtebilirsiniz. Ya da, `/blobServices/default/containers/testcontainer` o kapsayıcının tüm olaylarını almak için, depolama hesabındaki diğer kapsayıcıları değil, konunun ile başlayan bir filtre uygulayabilirsiniz.

Olayları özel konulara yayımlarken, etkinliklerinizin etkinlikleri oluşturun ve bu da abonelerin olayla ilgilenip ilgilenmediğini bilmesini kolaylaştırır. Aboneler olayları filtrelemek ve yönlendirmek için Subject özelliğini kullanır. Örneğin, abonelerin bu yolun segmentlerine göre filtreleyebilmesi için olayın gerçekleştiği yolu eklemeyi göz önünde bulundurun. Yol, abonelerin olayları en dar veya genel olarak filtrelemesine olanak sağlar. Konu içinde üç segment yolu sağlarsanız `/A/B/C` , aboneler `/A` çok sayıda olayı almak için ilk kesime göre filtreleyebilirsiniz. Bu aboneler, veya gibi konularla olayları `/A/B/C` alır `/A/D/E` . Diğer aboneler, daha `/A/B` dar bir olay kümesi almak için tarafından filtreleyebilirler.

Konuya göre filtrelemeye yönelik JSON sözdizimi şöyledir:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Gelişmiş filtreleme

Veri alanlarındaki değerlere göre filtrelemek ve karşılaştırma işlecini belirtmek için Gelişmiş filtreleme seçeneğini kullanın. Gelişmiş filtreleme ' de şunları belirtirsiniz:

* işleç türü-karşılaştırma türü.
* anahtar-filtreleme için kullanmakta olduğunuz olay verileri alanı. Bir sayı, Boolean, dize veya dizi olabilir.
* Values-anahtarla karşılaştırılacak değer veya değerler.

## <a name="key"></a>Anahtar
Anahtar, filtreleme için kullanmakta olduğunuz olay verilerinde yer alan alandır. Aşağıdaki türlerden biri olabilir:

- Sayı
- Boole
- Dize
- Dizide. `enableAdvancedFilteringOnArrays`Bu özelliği kullanmak için özelliği true olarak ayarlamanız gerekir. Şu anda Azure portal bu özelliğin etkinleştirilmesini desteklemez. 

    ```json
    "filter":
    {
        "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
        "subjectEndsWith": ".jpg",
        "enableAdvancedFilteringOnArrays": true
    }
    ```

**Event Grid şemasındaki** olaylar için, anahtar: `ID` ,,,, `Topic` `Subject` `EventType` `DataVersion` veya olay verileri (gibi `data.key1` ) için aşağıdaki değerleri kullanın.

**Bulut olayları şemasındaki** olaylar için, anahtar: `eventid` , `source` ,, `eventtype` `eventtypeversion` veya olay verileri (gibi `data.key1` ) için aşağıdaki değerleri kullanın.

**Özel giriş şeması** için, olay verileri alanlarını (gibi) kullanın `data.key1` . Veri bölümündeki alanlara erişmek için `.` (nokta) gösterimini kullanın. Örneğin, `data.sitename` ,, `data.appEventTypeDetail.action` `sitename` veya `action` Aşağıdaki örnek olaya erişmek için.

```json
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
```

## <a name="values"></a>Değerler
Değerler: sayı, dize, Boole veya dizi olabilir

## <a name="operators"></a>İşleçler

**Rakamlar** için kullanılabilir işleçler şunlardır:

## <a name="numberin"></a>Numberın
**Anahtar** değeri belirtilen **filtre** değerlerinden biri ise, numberin işleci true olarak değerlendirilir. Aşağıdaki örnekte, `counter` bölümündeki öznitelik değerinin `data` 5 veya 1 olup olmadığını denetler. 

```json
"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]
```


Anahtar bir diziyse, dizideki tüm değerler filtre değerlerinin dizisine karşı denetlenir. Anahtar: ve filtresi ile birlikte sahte kod aşağıda verilmiştir: `[v1, v2, v3]` `[a, b, c]` . Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="numbernotin"></a>Numbernotın
**Anahtar** değeri belirtilen **filtre** değerlerinden herhangi biri **değilse** numbernotın doğru olarak değerlendirilir. Aşağıdaki örnekte, `counter` bölümündeki öznitelik değerinin `data` 41 ve 0 olmadığını kontrol eder. 

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0
    ]
}]
```

Anahtar bir diziyse, dizideki tüm değerler filtre değerlerinin dizisine karşı denetlenir. Anahtar: ve filtresi ile birlikte sahte kod aşağıda verilmiştir: `[v1, v2, v3]` `[a, b, c]` . Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```

## <a name="numberlessthan"></a>NumberLessThan
**Anahtar** değeri belirtilen **filtre** değerinden **küçükse** numberlessthan işleci true olarak değerlendirilir. Aşağıdaki örnekte, `counter` bölümündeki öznitelik değerinin 100 ' den küçük olup olmadığını denetler `data` . 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

Anahtar bir diziyse, dizideki tüm değerler filtre değerine göre denetlenir. Şu anahtara sahip sözde kod aşağıda verilmiştir: `[v1, v2, v3]` . Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH key IN (v1, v2, v3)
    IF key < filter
        MATCH
```

## <a name="numbergreaterthan"></a>NumberGreaterThan
**Anahtar** değeri belirtilen **filtre** değerinden **daha büyükse** , NumberGreaterThan işleci true olarak değerlendirilir. Aşağıdaki örnekte, `counter` bölümündeki öznitelik değerinin 20 ' den büyük olup olmadığını denetler `data` . 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

Anahtar bir diziyse, dizideki tüm değerler filtre değerine göre denetlenir. Şu anahtara sahip sözde kod aşağıda verilmiştir: `[v1, v2, v3]` . Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH key IN (v1, v2, v3)
    IF key > filter
        MATCH
```

## <a name="numberlessthanorequals"></a>Numberlessals
**Anahtar** değeri belirtilen **filtre** değerinden **küçük veya** bu değere eşitse, numberlessals okarşılandığından işleci true olarak değerlendirilir. Aşağıdaki örnekte, `counter` bölümündeki öznitelik değerinin `data` 100 değerinden küçük veya ona eşit olup olmadığını denetler. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

Anahtar bir diziyse, dizideki tüm değerler filtre değerine göre denetlenir. Şu anahtara sahip sözde kod aşağıda verilmiştir: `[v1, v2, v3]` . Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH key IN (v1, v2, v3)
    IF key <= filter
        MATCH
```

## <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals
**Anahtar** değeri belirtilen **filtre** değerinden **büyük veya** bu değere eşitse, NumberGreaterThanOrEquals işleci true olarak değerlendirilir. Aşağıdaki örnekte, `counter` bölümündeki öznitelik değerinin `data` 30 ' a eşit veya daha büyük olup olmadığını denetler. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

Anahtar bir diziyse, dizideki tüm değerler filtre değerine göre denetlenir. Şu anahtara sahip sözde kod aşağıda verilmiştir: `[v1, v2, v3]` . Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH key IN (v1, v2, v3)
    IF key >= filter
        MATCH
```

## <a name="numberinrange"></a>Numberınrange
**Anahtar** değeri belirtilen **filtre aralıklarından** birinde ise numberınrange işleci true olarak değerlendirilir. Aşağıdaki örnekte, `key1` bölümündeki özniteliğin değerinin `data` iki aralıklardan birinde olup olmadığını denetler: 3,14159-999,95, 3000-4000. 

```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```

`values`Özelliği bir Aralık dizisidir. Önceki örnekte, bu iki aralığın bir dizisidir. Burada, denetlenecek tek aralıklı dizi örneği verilmiştir. 

**Tek aralıklı dizi:** 
```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Anahtar bir diziyse, dizideki tüm değerler filtre değerlerinin dizisine karşı denetlenir. Anahtar: `[v1, v2, v3]` ve filtre: bir Aralık dizisi olan sahte kod aşağıda verilmiştir. Bu sahte kodda, `a` `b` dizideki her bir aralığın düşük ve yüksek değerleri vardır. Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
       IF key >= a AND key <= b
           MATCH
```


## <a name="numbernotinrange"></a>NumberNotInRange
**Anahtar** değeri belirtilen **filtre aralıklarından** hiçbirinde **değilse** , NumberNotInRange işleci true olarak değerlendirilir. Aşağıdaki örnekte, `key1` bölümündeki özniteliğin değerinin `data` iki aralıklardan birinde olup olmadığını denetler: 3,14159-999,95, 3000-4000. Eğer ise, işleç false döndürür. 

```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```
`values`Özelliği bir Aralık dizisidir. Önceki örnekte, bu iki aralığın bir dizisidir. Burada, denetlenecek tek aralıklı dizi örneği verilmiştir.

**Tek aralıklı dizi:** 
```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Anahtar bir diziyse, dizideki tüm değerler filtre değerlerinin dizisine karşı denetlenir. Anahtar: `[v1, v2, v3]` ve filtre: bir Aralık dizisi olan sahte kod aşağıda verilmiştir. Bu sahte kodda, `a` `b` dizideki her bir aralığın düşük ve yüksek değerleri vardır. Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
        IF key >= a AND key <= b
            FAIL_MATCH
```


**Boole değerleri** için kullanılabilir işleç: 

## <a name="boolequals"></a>BoolEquals
**Anahtar** değeri belirtilen Boolean değer **filtresi** ise, BoolEquals işleci true olarak değerlendirilir. Aşağıdaki örnekte, bölümünde özniteliği değeri olup olmadığını denetler `isEnabled` `data` `true` . 

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```

Anahtar bir diziyse, dizideki tüm değerler filtre Boole değeri ile denetlenir. Şu anahtara sahip sözde kod aşağıda verilmiştir: `[v1, v2, v3]` . Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH key IN (v1, v2, v3)
    IF filter == key
        MATCH
```

**Dizeler** için kullanılabilir işleçler şunlardır:

## <a name="stringcontains"></a>StringContains
**Anahtar** değeri belirtilen **filtre** değerlerinden herhangi birini **içeriyorsa** (alt dizeler olarak) **stringcontains** true olarak değerlendirilir. Aşağıdaki örnekte, `key1` bölümündeki öznitelik değerinin `data` belirtilen alt dizelerin birini içerip içermediğini denetler: `microsoft` veya `azure` . Örneğin, `azure data factory` içinde bulunur `azure` . 

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

Anahtar bir diziyse, dizideki tüm değerler filtre değerlerinin dizisine karşı denetlenir. Anahtar: ve filtresi ile birlikte sahte kod aşağıda verilmiştir: `[v1, v2, v3]` `[a,b,c]` . Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            MATCH
```

## <a name="stringnotcontains"></a>StringNotContains
**Anahtar** , belirtilen **filtre** değerlerini alt dizeler olarak **içermiyorsa** **stringnotcontains** işleci true olarak değerlendirilir. Anahtar alt dize olarak belirtilen değerlerden birini içeriyorsa, işleç false olarak değerlendirilir. Aşağıdaki örnekte, işleci yalnızca bölümdeki özniteliğin değeri alt dizeler içermiyorsa true değerini döndürür `key1` `data` `contoso` `fabrikam` . 

```json
"advancedFilters": [{
    "operatorType": "StringNotContains",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam"
    ]
}]
```

Anahtar bir diziyse, dizideki tüm değerler filtre değerlerinin dizisine karşı denetlenir. Anahtar: ve filtresi ile birlikte sahte kod aşağıda verilmiştir: `[v1, v2, v3]` `[a,b,c]` . Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            FAIL_MATCH
```
Bu işlecin geçerli sınırlaması için [sınırlamalar](#limitations) bölümüne bakın.

## <a name="stringbeginswith"></a>StringBeginsWith
**Anahtar** değeri belirtilen **filtre** değerlerinden herhangi biriyle **başlıyorsa** , **stringbeginswith** işleci true olarak değerlendirilir. Aşağıdaki örnekte, `key1` bölümündeki öznitelik değerinin `data` veya ile başlayıp başlamamadığını denetler `event` `grid` . Örneğin, `event hubs` ile başlar `event` .  

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Anahtar bir diziyse, dizideki tüm değerler filtre değerlerinin dizisine karşı denetlenir. Anahtar: ve filtresi ile birlikte sahte kod aşağıda verilmiştir: `[v1, v2, v3]` `[a,b,c]` . Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            MATCH
```

## <a name="stringnotbeginswith"></a>StringNotBeginsWith
**Anahtar** değeri belirtilen **filtre** değerlerinden biriyle **başlamadıysanız** **stringnotbeginswith** işleci true olarak değerlendirilir. Aşağıdaki örnekte, `key1` bölümündeki öznitelik değerinin `data` veya ile başlayıp başlamamadığını denetler `event` `message` .

```json
"advancedFilters": [{
    "operatorType": "StringNotBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Anahtar bir diziyse, dizideki tüm değerler filtre değerlerinin dizisine karşı denetlenir. Anahtar: ve filtresi ile birlikte sahte kod aşağıda verilmiştir: `[v1, v2, v3]` `[a,b,c]` . Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            FAIL_MATCH
```

## <a name="stringendswith"></a>StringEndsWith
**Anahtar** değeri belirtilen **filtre** değerlerinden biriyle **biterse** **stringendswith** işleci true olarak değerlendirilir. Aşağıdaki örnekte, `key1` bölümündeki öznitelik değerinin `data` veya veya ile bitip bitmediğini denetler `jpg` `jpeg` `png` . Örneğin, `eventgrid.png` ile biter `png` .


```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Anahtar bir diziyse, dizideki tüm değerler filtre değerlerinin dizisine karşı denetlenir. Anahtar: ve filtresi ile birlikte sahte kod aşağıda verilmiştir: `[v1, v2, v3]` `[a,b,c]` . Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            MATCH
```

## <a name="stringnotendswith"></a>StringNotEndsWith
**Anahtar** değeri belirtilen **filtre** değerlerinden hiçbiriyle **bitmezse** **stringnotendswith** işleci true olarak değerlendirilir. Aşağıdaki örnekte, `key1` bölümündeki öznitelik değerinin `data` veya veya ile bitmediğini denetler `jpg` `jpeg` `png` . 


```json
"advancedFilters": [{
    "operatorType": "StringNotEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Anahtar bir diziyse, dizideki tüm değerler filtre değerlerinin dizisine karşı denetlenir. Anahtar: ve filtresi ile birlikte sahte kod aşağıda verilmiştir: `[v1, v2, v3]` `[a,b,c]` . Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            FAIL_MATCH
```

## <a name="stringin"></a>Stringın
**Stringın** işleci, **anahtar** değerinin belirtilen **filtre** değerlerinden biriyle **tam olarak eşleşip eşleşmediğini** denetler. Aşağıdaki örnekte, `key1` bölümündeki özniteliğinin değerinin `data` `contoso` veya `fabrikam` ya da olup olmadığını denetler `factory` . 

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam", 
        "factory"
    ]
}]
```

Anahtar bir diziyse, dizideki tüm değerler filtre değerlerinin dizisine karşı denetlenir. Anahtar: ve filtresi ile birlikte sahte kod aşağıda verilmiştir: `[v1, v2, v3]` `[a,b,c]` . Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="stringnotin"></a>Stringnotın
**Stringnotın** işleci, **anahtar** değerinin belirtilen **filtre** değerlerinden hiçbiriyle **eşleşip eşleşmediğini** denetler. Aşağıdaki örnekte, bölümünde özniteliği değeri olup olmadığını denetler `key1` `data` `aws` `bridge` . 

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

Anahtar bir diziyse, dizideki tüm değerler filtre değerlerinin dizisine karşı denetlenir. Anahtar: ve filtresi ile birlikte sahte kod aşağıda verilmiştir: `[v1, v2, v3]` `[a,b,c]` . Filtrenin veri türüyle eşleşmeyen veri türlerine sahip herhangi bir anahtar değer yok sayılır.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```


Tüm dize karşılaştırmaları büyük/küçük harfe duyarlı değildir.

> [!NOTE]
> Olay JSON 'SI gelişmiş filtre anahtarını içermiyorsa, filtre şu işleçler için **eşleşmeyen** evaulated olarak ayarlanır: NumberGreaterThan, NumberGreaterThanOrEquals, NumberLessThan, Numberlessmkoşulları, numberin, BoolEquals, stringcontains, StringNotContains, StringBeginsWith, StringNotBeginsWith, StringEndsWith, StringNotEndsWith, stringin.
> 
>Filtre şu işleçler için evaulated olarak **eşleşir** : Numbernotın, stringnotın.


## <a name="isnullorundefined"></a>Isnullorundefined
Anahtarın değeri NULL veya tanımsız ise, ısnullorundefined işleci true olarak değerlendirilir. 

```json
{
    "operatorType": "IsNullOrUndefined",
    "key": "data.key1"
}
```

Aşağıdaki örnekte, KEY1 eksik olduğundan, işleç doğru olarak değerlendirilir. 

```json
{ 
    "data": 
    { 
        "key2": 5 
    } 
}
```

Aşağıdaki örnekte, KEY1 null olarak ayarlanır; bu nedenle işleç doğru olarak değerlendirilir.

```json
{
    "data": 
    { 
        "key1": null
    }
}
```

Bu örneklerde KEY1 başka bir değer varsa, işleç yanlış olarak değerlendirilir. 

## <a name="isnotnull"></a>IsNotNull
Anahtarın değeri NULL veya tanımsız değilse IsNotNull işleci true olarak değerlendirilir. 

```json
{
    "operatorType": "IsNotNull",
    "key": "data.key1"
}
```

## <a name="or-and-and"></a>OR ve ve
Birden çok değer içeren tek bir filtre belirtirseniz, **ya** da bir işlem gerçekleştirilir, bu nedenle anahtar alanının değeri bu değerlerden biri olmalıdır. Aşağıda bir örnek verilmiştir:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Birden çok farklı filtre belirtirseniz **ve** bir işlem yapılır, bu nedenle her filtre koşulu karşılanmalıdır. Aşağıda bir örnek verilmiştir: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

## <a name="cloudevents"></a>CloudEvents 
**Cloudevents şemasındaki** olaylar için, anahtar: `eventid` , `source` ,, `eventtype` `eventtypeversion` veya olay verileri (gibi `data.key1` ) için aşağıdaki değerleri kullanın. 

[CloudEvents 1,0 ' de uzantı bağlamı özniteliklerini](https://github.com/cloudevents/spec/blob/v1.0.1/spec.md#extension-context-attributes)de kullanabilirsiniz. Aşağıdaki örnekte `comexampleextension1` ve `comexampleothervalue` uzantı bağlamı öznitelikleridir. 

```json
{
    "specversion" : "1.0",
    "type" : "com.example.someevent",
    "source" : "/mycontext",
    "id" : "C234-1234-1234",
    "time" : "2018-04-05T17:31:00Z",
    "subject": null,
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
        "appinfoA" : "abc",
        "appinfoB" : 123,
        "appinfoC" : true
    }
}
```

Bir filtrede uzantı bağlamı özniteliği kullanmanın bir örneği aşağıda verilmiştir.

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "comexampleothervalue",
    "values": [
        "5", 
        "1"
    ]
}]
```


## <a name="limitations"></a>Sınırlamalar

Gelişmiş filtreleme aşağıdaki sınırlamalara sahiptir:

* Her olay Kılavuzu aboneliği için tüm filtrelerdeki 5 Gelişmiş filtre ve 25 filtre değeri
* dize değeri başına 512 karakter
* **İçindeki** ve **Not** işleçleri için beş değer
* `StringNotContains`İşleç Şu anda portalda kullanılamıyor.
* **`.` (Nokta)** karakterleriyle anahtarlar. Örneğin: `http://schemas.microsoft.com/claims/authnclassreference` veya `john.doe@contoso.com` . Şu anda Anahtarlar içinde kaçış karakterleri için destek yoktur. 

Aynı anahtar birden fazla filtrede kullanılabilir.





## <a name="next-steps"></a>Sonraki adımlar

* PowerShell ve Azure CLı ile olayları filtreleme hakkında bilgi edinmek için bkz. [Event Grid olayları filtreleme](how-to-filter-events.md).
* Event Grid kullanmaya hızlıca başlamak için bkz. [özel olayları oluşturma ve Azure Event Grid ile yönlendirme](custom-event-quickstart.md).
