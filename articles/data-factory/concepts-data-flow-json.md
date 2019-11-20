---
title: Azure Data Factory veri akışını eşlemede JSON kullanma
description: Azure Data Factory eşleme veri akışı, Hiyerarşilerle JSON belgelerini işlemeye yönelik yerleşik yeteneklere sahiptir
author: kromerm
ms.author: makromer
ms.review: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: fe412e9e682fb55f1664c546e6b6c5a347527adb
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72387356"
---
# <a name="mapping-data-flow-json-handling"></a>Veri akışı JSON işlemesini eşleme

## <a name="creating-json-structures-in-derived-column"></a>Türetilmiş sütunda JSON yapıları oluşturma

Türetilmiş sütun ifade Oluşturucusu aracılığıyla veri akışınıza karmaşık bir sütun ekleyebilirsiniz. Türetilmiş sütun dönüşümünde yeni bir sütun ekleyin ve mavi kutuya tıklayarak ifade oluşturucuyu açın. Bir sütunu karmaşık hale getirmek için JSON yapısını el ile girebilir veya UX kullanarak alt sütunları etkileşimli olarak ekleyebilirsiniz.

### <a name="using-the-expression-builder-ux"></a>İfade Oluşturucu UX 'i kullanma

Çıktı şeması yan bölmesinde bir sütunun üzerine gelin ve artı simgesine tıklayın. Sütunu karmaşık bir tür yapmak için **alt sütun Ekle** ' yi seçin.

![Alt sütun Ekle](media/data-flow/addsubcolumn.png "Alt sütun Ekle")

Aynı şekilde ek sütunlar ve alt sütunlar ekleyebilirsiniz. Karmaşık olmayan her bir alan için, ifade düzenleyicisine sağa doğru bir ifade eklenebilir.

![Karmaşık sütun](media/data-flow/complexcolumn.png "Karmaşık sütun")

### <a name="entering-the-json-structure-manually"></a>JSON yapısını el ile girme

JSON yapısını el ile eklemek için yeni bir sütun ekleyin ve düzenleyicide ifadeyi girin. İfade aşağıdaki genel biçimi izler:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Bu ifade "complexColumn" adlı bir sütun için girilmişse, havuza aşağıdaki JSON olarak yazılır:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Tüm hiyerarşik tanım için örnek el ile betik
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="source-format-options"></a>Kaynak Biçim seçenekleri

Veri akışınızda kaynak olarak JSON veri kümesi kullanmak, beş ek ayar ayarlamanıza olanak sağlar. Bu ayarlar, **kaynak seçenekleri** sekmesindeki **JSON ayarları** Accordion altında bulunabilir.  

![JSON ayarları](media/data-flow/json-settings.png "JSON ayarları")

### <a name="default"></a>Varsayılan

Varsayılan olarak, JSON verileri aşağıdaki biçimde okunurdur.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Tek belge

**Tek belge** seçilirse, eşleme veri akışları her dosyadan bir JSON belgesi okur. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

### <a name="unquoted-column-names"></a>Tırnak işaretleri olmayan sütun adları

**Tırnak Işaretleri olmayan sütun adları** seçiliyse, eşleme veri akışları tırnak IÇINE alınmış JSON sütunlarını okur. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Açıklamalar içeriyor

JSON verilerinde C veya C++ Style yorum ekleme varsa, Select **yorumu vardır** .

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Tek tırnaklı

JSON alanları ve değerleri çift tırnak yerine tek tırnakları kullanıyorsa, **tek tırnak** işareti ' ni seçin.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Ters eğik çizgi kaçışı

JSON verilerinde karakter kaçış için ters eğik çizgi kullanılırsa **tek tırnak** işareti seçin.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Daha yüksek sıralı işlevler

Daha yüksek sıralı bir işlev, bir veya daha fazla işlevi bağımsız değişken olarak alan bir işlevdir. Dizi işlemlerini etkinleştiren veri akışları eşlemesinde desteklenen daha yüksek sıralı işlevlerin listesi aşağıda verilmiştir.

### <a name="filter"></a>filtreyle
Öğeleri, belirtilen koşulu karşılamayan dizinin dışına filtreler. Filtre, koşul işlevindeki bir öğeye #item olarak bir başvuru bekliyor.

#### <a name="examples"></a>Örnekler
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

### <a name="map"></a>map
Dizideki her öğeyi, belirtilen ifadeyi kullanarak yeni bir öğeye eşler. Eşleme, ifade işlevindeki bir öğeye #item olarak bir başvuru bekliyor.

#### <a name="examples"></a>Örnekler
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

### <a name="reduce"></a>azal
Öğeleri bir dizide biriktirir. Azaltma, #acc ve #item olarak ilk ifade işlevindeki bir Biriktiricinin ve bir öğenin bir başvurusunu bekler ve ikinci ifade işlevinde kullanılacak #result olarak elde edilen değeri bekler.

#### <a name="examples"></a>Örnekler
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

### <a name="sort"></a>Düzenine
Belirtilen koşul işlevini kullanarak diziyi sıralar. Sıralama, #item1 ve #item2 olarak ifade işlevindeki birbirini izleyen iki öğeye bir başvuru bekliyor.

#### <a name="examples"></a>Örnekler
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

### <a name="contains"></a>vardır
Belirtilen dizideki herhangi bir öğe, belirtilen koşulda doğru olarak değerlendiriliyorsa true değerini döndürür. Contains, koşul işlevindeki bir öğeye #item olarak bir başvuru bekliyor.

#### <a name="examples"></a>Örnekler
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Sonraki adımlar

* [Hiyerarşik yapılarınızı derlemek için türetilmiş sütun dönüşümünü kullanın](data-flow-derived-column.md)
