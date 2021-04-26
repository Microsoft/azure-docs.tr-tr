---
title: Azure Haritalar Web SDK 'sında veri odaklı stil Ifadeleri | Microsoft Azure haritaları
description: Veri odaklı stil ifadeleri hakkında bilgi edinin. Haritaların stillerini ayarlamak için Azure Maps web SDK 'sında bu ifadeleri nasıl kullanacağınızı öğrenin.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 41a117c9ea8b47afcedaa1714abc2031d3be6c21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680066"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Veri tabanlı stil Ifadeleri (Web SDK)

İfadeler, bir veri kaynağındaki her bir şekil için tanımlanan özellikleri gözlemleyecek stil seçeneklerine iş mantığı uygulamanızı sağlar. İfadeler, bir veri kaynağındaki veya katmandaki verileri filtreleyebilir. İfadeler, if-deyimleri gibi koşullu mantığdan oluşabilir. Ayrıca, bunları kullanarak verileri işlemek için kullanılabilir: dize işleçleri, mantıksal işleçler ve matematik işleçleri.

Veri odaklı stiller, Stillendirme etrafında iş mantığını uygulamak için gereken kod miktarını azaltır. Katmanlarla kullanıldığında, ifadeler ayrı bir iş parçacığında işleme zamanında değerlendirilir. Bu işlevsellik, UI iş parçacığında iş mantığını değerlendirmeye kıyasla daha yüksek performans sağlar.

Bu videoda, Azure Maps web SDK 'sında veri odaklı stillendirme hakkında genel bakış sunulmaktadır.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

İfadeler JSON dizileri olarak temsil edilir. Dizideki bir ifadenin ilk öğesi, ifade işlecinin adını belirten bir dizedir. Örneğin, "+" veya "Case". Sonraki öğeler (varsa), ifadenin bağımsız değişkenlerdir. Her bağımsız değişken bir değişmez değer (bir dize, sayı, Boolean veya `null` ) ya da başka bir ifade dizisi. Aşağıdaki sözde kod, bir ifadenin temel yapısını tanımlar. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Haritalar Web SDK 'Sı birçok tür ifadeyi destekler. İfadeler, kendi içinde veya diğer ifadelerle birlikte kullanılabilir.

| İfade türü | Description |
|---------------------|-------------|
| [Toplama ifadesi](#aggregate-expression) | Bir veri kümesi üzerinde işlenen ve ' a ' seçeneği ile kullanılabilen bir hesaplamayı tanımlayan bir ifade `clusterProperties` `DataSource` . |
| [Mantıksal ifadeler](#boolean-expressions) | Boolean ifadeleri, Boole karşılaştırmaları değerlendirmek için bir dizi Boole işleci sağlar. |
| [Renk ifadeleri](#color-expressions) | Renk ifadeleri renk değerleri oluşturmayı ve işlemeyi kolaylaştırır. |
| [Koşullu ifadeler](#conditional-expressions) | Koşullu ifadeler, IF-deyimleri gibi mantıksal işlemler sağlar. |
| [Veri ifadeleri](#data-expressions) | Bir özelliğindeki Özellik verilerine erişim sağlar. |
| [Ara ve adım ifadelerini enterpolala](#interpolate-and-step-expressions) | Enterpolasyonlu ve step ifadeleri, bir ara değerli eğri veya adım işlevi üzerinde değerleri hesaplamak için kullanılabilir. |
| [Katmana özgü ifadeler](#layer-specific-expressions) | Yalnızca tek bir katman için geçerli olan özel ifadeler. |
| [Matematik ifadeleri](#math-expressions) | Expression Framework içinde veri odaklı hesaplamalar gerçekleştirmek için matematik işleçleri sağlar. |
| [Dize işleci ifadeleri](#string-operator-expressions) | Dize operatörü ifadeleri, büyük/küçük harfe dönüştürme ve dönüştürme gibi dizeler üzerinde dönüştürme işlemleri gerçekleştirir. |
| [Tür ifadeleri](#type-expressions) | Tür ifadeleri, dizeler, sayılar ve Boole değerleri gibi farklı veri türlerini test etmek ve dönüştürmek için araçlar sağlar. |
| [Değişken bağlama ifadeleri](#variable-binding-expressions) | Değişken bağlama ifadeleri, bir hesaplamanın sonuçlarını bir değişkende depolar ve depolanan değeri yeniden hesaplamaya gerek kalmadan bir ifadenin başka bir yerinde birden çok kez başvurulur. |
| [Yakınlaştırma ifadesi](#zoom-expression) | İşleme zamanında haritanın geçerli yakınlaştırma düzeyini alır. |

Bu belgedeki tüm örnekler, farklı ifade türlerinin kullanılabileceği farklı yolları göstermek için aşağıdaki özelliği kullanır. 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

## <a name="data-expressions"></a>Veri ifadeleri

Veri ifadeleri bir özelliğin özellik verilerine erişim sağlar. 

| Expression | Dönüş türü | Description |
|------------|-------------|-------------|
| `['at', number, array]` | değer | Diziden bir öğe alır. |
| `['geometry-type']` | string | Özelliğin geometri türünü alır: Point, MultiPoint, LineString, MultiLineString, Çokgen, MultiPolygon. |
| `['get', string]` | değer | Geçerli özelliğin özelliklerinden özellik değerini alır. İstenen özellik eksikse null değerini döndürür. |
| `['get', string, object]` | değer | Belirtilen nesnenin özelliklerinden özellik değerini alır. İstenen özellik eksikse null değerini döndürür. |
| `['has', string]` | boolean | Özelliğin özelliklerinin belirtilen özelliğe sahip olup olmadığını belirler. |
| `['has', string, object]` | boolean | Nesnenin özelliklerinin belirtilen özelliğe sahip olup olmadığını belirler. |
| `['id']` | değer | Özelliği varsa özelliğin KIMLIĞINI alır. |
| `['in', boolean | string | number, array]` | boolean | Dizide bir öğe olup olmadığını belirler |
| `['in', substring, string]` | boolean | Bir dizedeki alt dizenin mevcut olup olmadığını belirler |
| `['index-of', boolean | string | number, array | string]`<br/><br/>`['index-of', boolean | string | number, array | string, number]` | sayı | Bir öğenin dizide bulunduğu ilk konumu veya bir dize içinde bir alt dize bulunabilir ya da `-1` giriş bulunamazsa. Aramanın başlatılacağı bir isteğe bağlı dizini kabul eder. |
| `['length', string | array]` | sayı | Bir dizenin veya dizinin uzunluğunu alır. |
| `['slice', array | string, number]`<br/><br/>`['slice', array | string, number, number]` | dize \| dizisi | Bir diziden veya belirtilen başlangıç dizininden bir dizeden bir alt dizeden veya ayarlandıysa bir başlangıç dizininden bir bitiş dizininden bir öğe döndürür. Dönüş değeri, Başlangıç dizininin dahil değildir ancak son dizinden değil. |

**Örnekler**

Bir özelliğin özelliklerine, bir ifade kullanarak doğrudan bir ifadede erişilebilir `get` . Bu örnek, `zoneColor` bir kabarcık katmanının Color özelliğini belirtmek için özelliğinin değerini kullanır. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Yukarıdaki örnek, tüm nokta özelliklerinin özelliği varsa düzgün çalışacaktır `zoneColor` . Aksi takdirde renk, büyük olasılıkla "siyah" a geri dönecektir. Geri dönüş rengini değiştirmek için, `case` `has` özelliğinin mevcut olup olmadığını denetlemek için ifadesiyle birlikte bir ifade kullanın. Özelliği yoksa, bir geri dönüş rengi döndürün.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

Balon ve sembol katmanları, varsayılan olarak bir veri kaynağındaki tüm şekillerin koordinatlarını işler. Bu davranış, bir çokgenin veya çizginin köşelerini vurgulayabilir. `filter`Katman seçeneği, Boolean ifadesinde bir ifade kullanarak, oluşturduğu özelliklerin geometri türünü sınırlamak için kullanılabilir `['geometry-type']` . Aşağıdaki örnek, yalnızca özelliklerin işlenmesi için bir kabarcık katmanını sınırlandırır `Point` .

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

Aşağıdaki örnek hem hem de `Point` `MultiPoint` özelliklerinin işlenmesine izin verir. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Benzer şekilde, çokgenler ana hattı çizgi katmanlarında işlenir. Bir çizgi katmanında bu davranışı devre dışı bırakmak için yalnızca ve özelliklerine izin veren bir filtre ekleyin `LineString` `MultiLineString` .  

Veri ifadelerinin nasıl kullanılacağına ilişkin bazı ek örnekler aşağıda verilmiştir:

```javascript
//Get item [2] from an array "properties.abcArray[1]" = "c"
['at', 2, ['get', 'abcArray']]

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
['at', 1, ['at', 0, ['get', 'array2d']]]

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
['in', 'a', ['get', 'abcArray']]

//Gets the index of the value 'b' in an array "properties.abcArray.indexOf('b')" = 1
['index-of', 'b', ['get', 'abcArray']]

//Get the length of an array "properties.abcArray.length" = 3
['length', ['get', 'abcArray']]

//Get the value of a subproperty "properties._style.fillColor" = "red"
['get', 'fillColor', ['get', '_style']]

//Check that "fillColor" exists as a subproperty of "_style".
['has', 'fillColor', ['get', '_style']]

//Slice an array starting at index 2 "properties.abcArray.slice(2)" = ['c']
['slice', ['get', 'abcArray'], 2]

//Slice a string from index 0 to index 4 "properties.entityType.slice(0, 4)" = 'rest'
['slice', ['get', 'entityType'], 0, 4]
```

## <a name="math-expressions"></a>Matematik ifadeleri

Matematik ifadeleri, ifade çerçevesi içinde veri odaklı hesaplamalar gerçekleştirmek için matematik işleçleri sağlar.

| Expression | Dönüş türü | Description |
|------------|-------------|-------------|
| `['+', number, number, …]` | sayı | Belirtilen sayıların toplamını hesaplar. |
| `['-', number]` | sayı | Belirtilen sayı ile 0 çıkartır. |
| `['-', number, number]` | sayı | İkinci sayıya göre ilk sayıları çıkartır. |
| `['*', number, number, …]` | sayı | Belirtilen sayıları birlikte çarpar. |
| `['/', number, number]` | sayı | İlk sayıyı ikinci sayıya böler. |
| `['%', number, number]` | sayı | İlk sayıyı ikinci sayıya bölerken kalanı hesaplar. |
| `['^', number, number]` | sayı | İkinci sayının gücünden çıkarılan ilk değerin değerini hesaplar. |
| `['abs', number]` | sayı | Belirtilen sayının mutlak değerini hesaplar. |
| `['acos', number]` | sayı | Belirtilen sayının arkkosinüsünü hesaplar. |
| `['asin', number]` | sayı | Belirtilen sayının arksinüsünü hesaplar. |
| `['atan', number]` | sayı | Belirtilen sayının ark tanjantını hesaplar. |
| `['ceil', number]` | sayı | Sayıyı bir sonraki tam tamsayıya yuvarlar. |
| `['cos', number]` | sayı | Belirtilen sayının cos sayısını hesaplar. |
| `['e']` | sayı | Matematik sabitini döndürür `e` . |
| `['floor', number]` | sayı | Sayıyı, önceki tam tamsayıya yuvarlar. |
| `['ln', number]` | sayı | Belirtilen sayının doğal logaritmasını hesaplar. |
| `['ln2']` | sayı | Matematik sabitini döndürür `ln(2)` . |
| `['log10', number]` | sayı | Belirtilen sayının 10 tabanında logaritmasını hesaplar. |
| `['log2', number]` | sayı | Belirtilen sayının temel iki logaritmasını hesaplar. |
| `['max', number, number, …]` | sayı | Belirtilen sayı kümesindeki en büyük sayıyı hesaplar. |
| `['min', number, number, …]` | sayı | Belirtilen sayı kümesindeki minimum sayıyı hesaplar. |
| `['pi']` | sayı | Matematik sabitini döndürür `PI` . |
| `['round', number]` | sayı | Sayıyı en yakın tamsayıya yuvarlar. Yarı-değerler sıfırdan uzağa yuvarlanır. Örneğin, `['round', -1.5]` olarak değerlendirilir `-2` . |
| `['sin', number]` | sayı | Belirtilen sayının sinüsünü hesaplar. |
| `['sqrt', number]` | sayı | Belirtilen sayının kare kökünü hesaplar. |
| `['tan', number]` | sayı | Belirtilen sayının tanjantını hesaplar. |

## <a name="aggregate-expression"></a>Toplama ifadesi

Toplama ifadesi, bir veri kümesi üzerinde işlenen ve ' nin seçeneğiyle kullanılabilen bir hesaplamayı tanımlar `clusterProperties` `DataSource` . Bu ifadelerin çıktısı bir sayı veya Boole değeri olmalıdır. 

Toplama ifadesi üç değer alır: bir işleç değeri ve başlangıç değeri ve toplama işlemini uygulamak için bir veri içindeki her özellikten bir özelliği almak için bir ifade. Bu ifade aşağıdaki biçimdedir:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- işleç: kümedeki her bir nokta için hesaplanan tüm değerlere karşı uygulanan bir ifade işlevi `mapExpression` . Desteklenen işleçler: 
    - Sayılar için:,, `+` `*` `max` , `min`
    - Boole değerleri için: `all` , `any`
- InitialValue: ilk hesaplanan değerin oluşturulduğu başlangıç değeri.
- mapExpression: veri kümesindeki her bir noktaya göre uygulanan bir ifade.

**Örnekler**

Bir veri kümesindeki tüm özellikler bir sayı olan bir `revenue` özelliğe sahiptir. Daha sonra, bir kümede bulunan ve veri kümesinden oluşturulan tüm noktaların toplam geliri hesaplanabilir. Bu hesaplama aşağıdaki toplama ifadesi kullanılarak yapılır: `['+', 0, ['get', 'revenue']]`

### <a name="accumulated-expression"></a>Birikmiş ifade

`accumulated`İfade şimdiye kadar biriktirilen bir küme özelliğinin değerini alır. Bu, yalnızca `clusterProperties` kümelenmiş bir kaynak seçeneğinde kullanılabilir `DataSource` .

**Kullanım**

```javascript
["accumulated"]
```

## <a name="boolean-expressions"></a>Mantıksal ifadeler

Boolean ifadeleri, Boole karşılaştırmaları değerlendirmek için bir dizi Boole işleci sağlar.

Değerler karşılaştırılırken karşılaştırma kesin olarak yazılır. Farklı türlerin değerleri her zaman eşit kabul edilir. Türlerin ayrıştırma zamanında farklı olduğu bilinen durumlar geçersiz olarak değerlendirilir ve bir ayrıştırma hatası oluşturur. 

| Expression | Dönüş türü | Description |
|------------|-------------|-------------|
| `['!', boolean]` | boolean | Mantıksal değilleme. `true`Girişin olup olmadığını ve girişin olup olmadığını döndürür `false` `false` `true` . |
| `['!=', value, value]` | boolean | `true`Giriş değerleri eşit değilse döndürür, `false` Aksi takdirde. |
| `['<', value, value]` | boolean | `true`İlk giriş ikinciden kesinlikle küçükse döndürür, `false` Aksi takdirde. Bağımsız değişkenlerin her iki dize veya iki sayı olması gerekir. |
| `['<=', value, value]` | boolean | `true`İlk girişin ikinciden küçük veya ona eşit olup olmadığını döndürür, `false` Aksi takdirde. Bağımsız değişkenlerin her iki dize veya iki sayı olması gerekir. |
| `['==', value, value]` | boolean | `true`Giriş değerlerinin eşitse, `false` Aksi takdirde döndürür. Bağımsız değişkenlerin her iki dize veya iki sayı olması gerekir. |
| `['>', value, value]` | boolean | `true`İlk girişin ikinciden kesinlikle büyük olup olmadığını döndürür, `false` Aksi takdirde. Bağımsız değişkenlerin her iki dize veya iki sayı olması gerekir. |
| `['>=' value, value]` | boolean | `true`İlk girişin ikinciden büyük veya ona eşit olup olmadığını döndürür, `false` Aksi takdirde. Bağımsız değişkenlerin her iki dize veya iki sayı olması gerekir. |
| `['all', boolean, boolean, …]` | boolean | `true`Tüm girdilerin `true` , yoksa, döndürür `false` . |
| `['any', boolean, boolean, …]` | boolean | Girdilerden `true` herhangi biri ise `true` , `false` Aksi takdirde döndürür. |
| `['within', Polygon | MultiPolygon | Feature<Polygon | MultiPolygon>]` | boolean | `true`Değerlendirilen özelliğin giriş geometrisi sınırının içinde tam olarak içerildiğini, aksi takdirde false değerini döndürür. Giriş değeri,, veya türünde geçerli bir geojson olabilir `Polygon` `MultiPolygon` `Feature` `FeatureCollection` . Değerlendirme için desteklenen özellikler:<br/><br/>-Point: `false` bir noktanın sınır üzerinde olması veya sınırın dışına düştüğünü döndürür.<br/>-LineString: `false` bir satırın herhangi bir bölümü sınırın dışında kalırsa, satır sınırın üzerine kesişiyor veya çizginin uç noktası sınırın üzerinde. |

## <a name="conditional-expressions"></a>Koşullu ifadeler

Koşullu ifadeler, IF-deyimleri gibi mantıksal işlemler sağlar.

Aşağıdaki ifadeler giriş verilerinde koşullu mantık işlemleri gerçekleştirir. Örneğin, ifade " `case` if/then/else" mantığını, ifade ise " `match` switch-deyimi" gibi sağlar. 

### <a name="case-expression"></a>Case ifadesi

`case`İfade, "if/then/else" mantığını sağlayan bir koşullu ifade türüdür. Boolean koşulların bir listesi aracılığıyla bu tür ifade adımları. True olarak değerlendirmek için ilk Boole koşulunun çıkış değerini döndürür.

Aşağıdaki sözde kod, ifadenin yapısını tanımlar `case` . 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Örnek**

Aşağıdaki örnek, olarak değerlendirilen bir tane bulana kadar farklı Boolean koşullarında adımlar sağlar `true` ve ardından ilgili değeri döndürür. Hiçbir Boole koşulu değerlendirilirse `true` , bir geri dönüş değeri döndürülür. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Match ifadesi

`match`İfade, Logic gibi switch deyimi sağlayan bir koşullu ifade türüdür. Giriş, `['get', 'entityType']` bir dize veya sayı döndüren gibi herhangi bir ifade olabilir. Her etiket tek bir sabit değer değeri ya da değerlerinin tüm dizeler veya tüm sayılar olması gereken sabit değerler dizisi olmalıdır. Dizideki herhangi bir değer eşleşiyorsa, giriş eşleşir. Her etiket benzersiz olmalıdır. Giriş türü etiketlerin türüyle eşleşmiyorsa, sonuç geri dönüş değeri olacaktır.

Aşağıdaki sözde kod, ifadenin yapısını tanımlar `match` . 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Örnekler**

Aşağıdaki örnek, bir `entityType` kabarcık katmanındaki bir nokta özelliğinin özelliğine bir eşleşme arayacağını bakar. Bir eşleşme bulursa, belirtilen değer döndürülür veya geri dönüş değerini döndürür.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

Aşağıdaki örnek, hepsi aynı değeri döndürmesi gereken bir etiket kümesini listelemek için bir diziyi kullanır. Bu yaklaşım, her bir etiketin ayrı ayrı listelenenden çok daha etkilidir. Bu durumda, `entityType` özellik "Restoran" veya "grocery_store" ise, "kırmızı" rengi döndürülür.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>Birleşim ifadesi

`coalesce`İlk null olmayan değer alınana ve bu değeri döndürene kadar bir ifade kümesi aracılığıyla ifade adımları. 

Aşağıdaki sözde kod, ifadenin yapısını tanımlar `coalesce` . 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Örnek**

Aşağıdaki örnek bir `coalesce` sembol katmanının seçeneğini ayarlamak için bir ifade kullanır `textField` . Özelliği özellikte yoksa `title` veya olarak ayarlandıysa `null` , ifade daha sonra özelliği aramaya çalışır `subTitle` , veya eksikse, `null` daha sonra boş bir dizeye geri dönecektir. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subTitle. 
            ['get', 'subTitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

Aşağıdaki örnek, `coalesce` belirtilen görüntü adları listesinden harita hareketli görüntüsündeki kullanılabilir ilk görüntü simgesini almak için bir ifade kullanır.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Tür ifadeleri

Tür ifadeleri, dizeler, sayılar ve Boole değerleri gibi farklı veri türlerini test etmek ve dönüştürmek için araçlar sağlar.

| Expression | Dönüş türü | Description |
|------------|-------------|-------------|
| `['array', value]` \| `['array', type: "string" | "number" | "boolean", value]` | Object [] | Girişin bir dizi olduğunu onaylar. |
| `['boolean', value]` \| `["boolean", value, fallback: value, fallback: value, ...]` | boolean | Giriş değerinin bir Boole olduğunu onaylar. Birden çok değer sağlanmışsa, her biri Boole elde edilene kadar sırayla değerlendirilir. Girdilerden hiçbiri Boole değilse, ifade bir hatadır. |
| `['collator', { 'case-sensitive': boolean, 'diacritic-sensitive': boolean, 'locale': string }]` | Collator | Yerel ayara bağlı karşılaştırma işlemlerinde kullanılmak üzere bir Harmanlayıcı döndürür. Büyük/küçük harfe duyarlı ve aksan duyarlı seçenekler varsayılan olarak false şeklindedir. Yerel ayar bağımsız değişkeni, kullanılacak yerel ayarın IETF dili etiketini belirtir. Hiçbiri sağlanmazsa, varsayılan yerel ayar kullanılır. İstenen yerel ayar kullanılamıyorsa, Harmanlayıcı sistem tarafından tanımlanan bir geri dönüş yerel ayarı kullanır. Yerel ayar geri dönüş davranışının sonuçlarını sınamak için çözümlenmiş-locale ' i kullanın. |
| `['literal', array]`<br/><br/>`['literal', object]` | dizi \| nesnesi | Sabit bir dizi veya nesne değeri döndürür. Bir dizi ya da nesnenin bir ifade olarak değerlendirilmesini engellemek için bu ifadeyi kullanın. Bir dizi ya da nesnenin bir ifade tarafından döndürülmesi gerektiğinde bu gereklidir. |
| `['image', string]` | string | Harita görüntüsü Sprite öğesine belirtilen görüntü KIMLIĞININ yüklenip yüklenmediğini denetler. Eğer ise, KIMLIK döndürülür, aksi takdirde null döndürülür. |
| `['number', value]` \| `["number", value, fallback: value, fallback: value, ...]` | sayı | Giriş değerinin bir sayı olduğunu onaylar. Birden çok değer sağlanmışsa, her biri bir sayı alınana kadar sırayla değerlendirilir. Girdilerden hiçbiri sayı değilse, ifade bir hatadır. |
| `['object', value]`  \| `["object", value, fallback: value, fallback: value, ...]` | Nesne | Giriş değerinin bir nesne olduğunu onaylar.  Birden çok değer sağlanmışsa, her biri bir nesne alınana kadar sırayla değerlendirilir. Girdilerden hiçbiri nesneler ise, ifade bir hatadır. |
| `['string', value]` \| `["string", value, fallback: value, fallback: value, ...]` | string | Giriş değerinin bir dize olduğunu onaylar. Birden çok değer sağlanmışsa, her biri bir dize alınana kadar sırayla değerlendirilir. Girdilerden hiçbiri dize değilse, ifade bir hatadır. |
| `['to-boolean', value]` | boolean | Giriş değerini bir Boole değerine dönüştürür. Sonuç, `false` girişin boş bir dize,,, veya, `0` `false` `null` `NaN` Aksi durumda `true` . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Giriş değerini bir renge dönüştürür. Birden çok değer sağlanmışsa, ilk başarılı dönüştürme alınana kadar her biri sırayla değerlendirilir. Girdilerden hiçbiri dönüştürülemiyorsa, ifade bir hatadır. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | sayı | Mümkünse, giriş değerini bir sayıya dönüştürür. Giriş `null` veya ise `false` , sonuç 0 ' dır. Giriş ise, `true` Sonuç 1 ' dir. Giriş bir dizeyse, ECMAScript dil belirtiminin [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) dize işlevini kullanarak bir sayıya dönüştürülür. Birden çok değer sağlanmışsa, ilk başarılı dönüştürme alınana kadar her biri sırayla değerlendirilir. Girdilerden hiçbiri dönüştürülemiyorsa, ifade bir hatadır. |
| `['to-string', value]` | string | Giriş değerini bir dizeye dönüştürür. Giriş ise, `null` sonuç olur `""` . Giriş bir Boole ise, sonuç `"true"` veya olur `"false"` . Giriş bir sayı ise, ECMAScript dil belirtiminin [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) Number işlevi kullanılarak bir dizeye dönüştürülür. Giriş bir renkeyse CSS RGBA Color dizesine dönüştürülür `"rgba(r,g,b,a)"` . Aksi takdirde, giriş, ECMAScript dil belirtiminin [JSON. stringbelirt](https://tc39.github.io/ecma262/#sec-json.stringify) işlevi kullanılarak bir dizeye dönüştürülür. |
| `['typeof', value]` | string | Verilen değerin türünü tanımlayan bir dize döndürür. |

> [!TIP]
> Tarayıcı konsolunda aşağıdakine benzer bir hata iletisi varsa `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` , kodunuzda ilk değeri için bir dize olmayan bir dizi içeren bir ifade olduğu anlamına gelir. İfadenin bir dizi döndürmesini istiyorsanız, diziyi ifadesiyle sarın `literal` . Aşağıdaki örnek, `offset` `match` nokta özelliğinin özelliğinin değerine bağlı olarak iki sayı değeri arasında seçim yapmak için bir ifade kullanarak bir sembol katmanının simge seçeneğini ayarlar  `entityType` .
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Renk ifadeleri

Renk ifadeleri renk değerleri oluşturmayı ve işlemeyi kolaylaştırır.

| Expression | Dönüş türü | Description |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Ve arasında aralığa gereken *kırmızı*, *yeşil* ve *mavi* bileşenlerden bir renk değeri oluşturur `0` `255` ve bir alfa bileşeni `1` . Herhangi bir bileşen Aralık dışında olursa ifade bir hatadır. |
| `['rgba', number, number, number, number]` | color | Ve arasında aralığa gereken *kırmızı*, *yeşil*, *mavi* bileşenlerden `0` `255` ve bir ve aralığı içindeki bir Alfa bileşeninden bir renk değeri oluşturur `0` `1` . Herhangi bir bileşen Aralık dışında olursa ifade bir hatadır. |
| `['to-rgba']` | \[sayı, sayı, sayı, sayı\] | Giriş renginin *kırmızı*, *yeşil*, *mavi* ve *Alfa* bileşenlerini içeren dört öğeli bir diziyi bu sırayla döndürür. |

**Örnek**

Aşağıdaki örnek, *kırmızı* değeri olan bir RGB renk değeri `255` ve özelliğinin değeri ile çarpılarak hesaplanan *yeşil* ve *mavi* değerler oluşturur `2.5` `temperature` . Sıcaklık değiştiğinde renk, farklı *kırmızı* gölgelerle değişecektir.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Dize işleci ifadeleri

Dize operatörü ifadeleri, büyük/küçük harfe dönüştürme ve dönüştürme gibi dizeler üzerinde dönüştürme işlemleri gerçekleştirir. 

| Expression | Dönüş türü | Description |
|------------|-------------|-------------|
| `['concat', string, string, …]` | dize | Birden çok dizeyi birlikte birleştirir. Her değer bir dize olmalıdır. `to-string`Gerekirse, diğer değer türlerini dizeye dönüştürmek için tür ifadesini kullanın. |
| `['downcase', string]` | string | Belirtilen dizeyi küçük harfe dönüştürür. |
| `['is-supported-script', string]` \| `['is-supported-script', Expression]`| boolean | Giriş dizesinin geçerli yazı tipi yığını tarafından desteklenen bir karakter kümesi kullanıp kullanmadığını belirler. Örnek: `['is-supported-script', 'ಗೌರವಾರ್ಥವಾಗಿ']` |
| `['resolved-locale', string]` | string | Belirtilen Harmanlayıcı tarafından kullanılan yerel ayarın IETF dil etiketini döndürür. Bu, varsayılan sistem yerel ayarını belirlemekte veya istenen bir yerel ayarın başarıyla yüklenip yüklenmediğini belirleyebilmesi için kullanılabilir. |
| `['upcase', string]` | string | Belirtilen dizeyi büyük harfe dönüştürür. |

**Örnek**

Aşağıdaki örnek, `temperature` nokta özelliğinin özelliğini bir dizeye dönüştürür ve sonra "°f" öğesini sonuna ekler.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

Yukarıdaki ifade, "64 °F" metni ile haritada, aşağıdaki görüntüde gösterildiği gibi çakışan bir PIN oluşturur.

<center>

![Dize işleci ifade örneği ](media/how-to-expressions/string-operator-expression.png)</center>

## <a name="interpolate-and-step-expressions"></a>Ara ve adım ifadelerini enterpolala

Enterpolasyonlu ve step ifadeleri, bir ara değerli eğri veya adım işlevi üzerinde değerleri hesaplamak için kullanılabilir. Bu ifadeler, giriş olarak sayısal bir değer döndüren bir ifadeyi alır (örneğin,) `['get',  'temperature']` . Giriş değeri, enterpolasyona veya Step işlevine en uygun değeri belirleyecek giriş ve çıkış değerlerinin çiftlerine göre değerlendirilir. Çıkış değerleri "Durdur" olarak adlandırılır. Her durun giriş değerleri bir sayı olmalı ve artan düzende olmalıdır. Çıkış değerleri bir sayı, sayı dizisi veya bir renk olmalıdır.

### <a name="interpolate-expression"></a>Enterpolageç ifadesi

Bir `interpolate` ifade, durdurma değerleri arasında ilişkilendirme yaparak sürekli, düzgün bir değer kümesini hesaplamak için kullanılabilir. `interpolate`Renk değerleri döndüren bir ifade, sonuç değerlerinin seçildiği bir renk gradyanı üretir.

Bir ifadede kullanılabilecek üç tür ilişkilendirme yöntemi vardır `interpolate` :
 
* `['linear']` -Durak çifti arasında doğrusal bir şekilde enterpolasyonlar.
* `['exponential', base]` -Duraklar arasında üstel olarak katlanarak enterpolasyonlar. `base`Değer, çıktının arttığı hızı denetler. Daha yüksek değerler, çıktıyı aralığın üst ucunda daha fazla artar. `base`1 ' e yakın bir değer, daha fazla doğrusal bir şekilde artan bir çıktı üretir.
* `['cubic-bezier', x1, y1, x2, y2]` -Verilen denetim noktaları tarafından tanımlanan [üçüncü dereceden Bezier eğrisini](https://developer.mozilla.org/docs/Web/CSS/timing-function) kullanarak enterpolasyonlar.

İşte bu farklı türlerde ara nesnelerin nasıl görüneceğine ilişkin bir örnek. 

| Doğrusal  | Üstel | Üçüncü dereceden Bezier |
|---------|-------------|--------------|
| ![Doğrusal ilişkilendirme grafiği](media/how-to-expressions/linear-interpolation.png) | ![Üstel enterpolasyon grafiği](media/how-to-expressions/exponential-interpolation.png) | ![Üçüncü dereceden Bezier enterpolasyon grafiği](media/how-to-expressions/bezier-curve-interpolation.png) |

Aşağıdaki sözde kod, ifadenin yapısını tanımlar `interpolate` . 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Örnek**

Aşağıdaki örnek, `linear interpolate` `color` nokta özelliğinin özelliğine dayalı bir kabarcık katmanının özelliğini ayarlamak için bir ifade kullanır `temperature` . `temperature`Değer 60 ' den küçükse, "mavi" döndürülür. 60 arasındaysa ve 70 ' den küçükse, sarı döndürülür. 70 arasındaysa ve 80 ' den küçükse, "turuncu" döndürülür. 80 veya daha büyükse, "Red" döndürülür.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50, 'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

Aşağıdaki görüntüde, yukarıdaki ifade için renklerin nasıl seçildiği gösterilmektedir.
 
<center>

![Enterpolageç ifade örneği ](media/how-to-expressions/interpolate-expression-example.png)</center>

### <a name="step-expression"></a>Adım ifadesi

Bir `step` ifade, duraklar tarafından tanımlanan bir [piecewise-sabit işlevi](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) değerlendirerek ayrık ve basamaklı sonuç değerlerini hesaplamak için kullanılabilir. 

Aşağıdaki sözde kod, ifadenin yapısını tanımlar `step` . 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

Adım ifadeleri, giriş değerinden hemen önce durun çıkış değerini ya da giriş ilk durmadan daha küçükse ilk giriş değerini döndürür. 

**Örnek**

Aşağıdaki örnek, `step` `color` nokta özelliğinin özelliğine dayalı bir kabarcık katmanının özelliğini ayarlamak için bir ifade kullanır `temperature` . `temperature`Değer 60 ' den küçükse, "mavi" döndürülür. 60 arasındaysa ve 70 ' den küçükse, "sarı" döndürülür. 70 arasındaysa ve 80 ' den küçükse, "turuncu" döndürülür. 80 veya daha büyükse, "Red" döndürülür.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

Aşağıdaki görüntüde, yukarıdaki ifade için renklerin nasıl seçildiği gösterilmektedir.
 
<center>

![Adım ifadesi örneği](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Katmana özgü ifadeler

Yalnızca belirli katmanlara uygulanan özel ifadeler.

### <a name="heat-map-density-expression"></a>Isı haritası yoğunluğu ifadesi

Isı haritası yoğunluğu ifadesi, ısı haritası katmanındaki her bir piksel için ısı haritası yoğunluğu değerini alır ve olarak tanımlanır `['heatmap-density']` . Bu değer ile arasında bir sayıdır `0` `1` . `interpolation` `step` Isı haritasını renklendirmek için kullanılan renk degradesini tanımlamak için veya ifadesiyle birlikte kullanılır. Bu ifade yalnızca ısı haritası katmanının [Color seçeneğinde](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions#color) kullanılabilir.

> [!TIP]
> Dizin 0 ' daki, bir enterpolasyon ifadesinde veya bir adım renginin varsayılan renginden renk, veri bulunmayan alanın rengini tanımlar. 0 dizinindeki renk, bir arka plan rengi tanımlamak için kullanılabilir. Birçok, bu değeri saydam veya yarı saydam bir siyah olarak ayarlamayı tercih eder.

**Örnek**

Bu örnek, ısı haritasını işlemek için yumuşak bir renk gradyanı oluşturmak üzere bir Oluşturucu ilişkilendirme ifadesi kullanır. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Bir ısı haritasını renklendirmeye yönelik düz bir gradyan kullanmanın yanı sıra, renkler bir ifade kullanılarak bir aralıklar kümesi içinde belirlenebilir `step` . `step`Isı haritasının renklendirilebilmesini için bir ifade kullanılması, yoğunluğu bir dağılım veya radar stil eşlemesine benzer aralıklar halinde ayırır.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Daha fazla bilgi için bkz. [ısı haritası katmanı ekleme](map-add-heat-map-layer.md) belgeleri.

### <a name="line-progress-expression"></a>Satır ilerleme ifadesi

Bir satır ilerleme ifadesi, ilerleme durumunu çizgi katmanında bir gradyan çizgisi üzerinde alır ve olarak tanımlanır `['line-progress']` . Bu değer 0 ile 1 arasında bir sayıdır. Or ifadesi ile birlikte kullanılır `interpolation` `step` . Bu ifade yalnızca çizgi katmanının [Strokegradient seçeneğiyle]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions#strokegradient) birlikte kullanılabilir. 

> [!NOTE]
> `strokeGradient`Çizgi katmanının seçeneği, `lineMetrics` veri kaynağı seçeneğinin olarak ayarlanmasını gerektirir `true` .

**Örnek**

Bu örnek, `['line-progress']` bir satırın konturuna bir renk gradyanı uygulamak için ifadesini kullanır.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Bkz. canlı örnek](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Metin alanı biçim ifadesi

Metin alanı biçimi ifadesi, `textField` `textOptions` karışık metin biçimlendirmesi sağlamak üzere sembol katmanları özelliğinin seçeneğiyle birlikte kullanılabilir. Bu ifade, bir giriş dizesi ve biçimlendirme seçenekleri kümesinin belirtilmesini sağlar. Bu ifadedeki her giriş dizesi için aşağıdaki seçenekler belirlenebilir.

 * `'font-scale'` -Yazı tipi boyutu için ölçekleme faktörünü belirtir. Belirtilmişse, bu değer `size` bağımsız dize için öğesinin özelliğini geçersiz kılar `textOptions` .
 * `'text-font'` -Bu dize için kullanılması gereken bir veya daha fazla yazı tipi ailesini belirtir. Belirtilmişse, bu değer `font` bağımsız dize için öğesinin özelliğini geçersiz kılar `textOptions` .

Aşağıdaki sözde kod, metin alanı biçim ifadesinin yapısını tanımlar. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[]
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[]
    },
    …
]
```

**Örnek**

Aşağıdaki örnek, bir kalın yazı tipi ekleyerek ve özelliğin özelliğinin yazı tipi boyutunu ölçeklendirerek metin alanını biçimlendirir `title` . Bu örnek ayrıca `subTitle` , ölçeklenmiş bir yazı tipi boyutuyla bir yeni satır üzerinde özelliğin özelliğini de ekler.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subTitle property. 
            ['get', 'subTitle'],
            { 
                'font-scale': 0.75
            }
        ]
    }
});
```

Bu katman, aşağıdaki görüntüde gösterildiği gibi nokta özelliğini oluşturacak:
 
<center>

![Biçimli metin alanı ](media/how-to-expressions/text-field-format-expression.png) olan nokta özelliğinin görüntüsü </center>

### <a name="number-format-expression"></a>Sayı biçimi ifadesi

`number-format`İfade yalnızca `textField` bir sembol katmanının seçeneğiyle birlikte kullanılabilir. Bu ifade, belirtilen sayıyı biçimli bir dizeye dönüştürür. Bu ifade, JavaScript [Number. toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) işlevini sarmalayan ve aşağıdaki seçenek kümesini destekler.

 * `locale` -Sayıları, belirtilen dille hizalanan bir şekilde dizelere dönüştürmek için bu seçeneği belirtin. Bu seçeneğe bir [BCP 47 Language etiketi](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) geçirin.
 * `currency` -Sayıyı bir para birimini temsil eden bir dizeye dönüştürmek için. Olası değerler [ıso 4217 para birimi kodlarıdır](https://en.wikipedia.org/wiki/ISO_4217), örneğin ABD Doları IÇIN "USD", Euro IÇIN "EUR" veya Çince RMB IÇIN "CNY" gibi.
 * `'min-fraction-digits'` -Sayının dize sürümüne dahil edilecek en az ondalık basamak sayısını belirtir.
 * `'max-fraction-digits'` -Sayının dize sürümüne dahil edilecek en fazla ondalık basamak sayısını belirtir.

Aşağıdaki sözde kod, metin alanı biçim ifadesinin yapısını tanımlar. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Örnek**

Aşağıdaki örnek, `number-format` `revenue` nokta özelliğinin ÖZELLIĞININ bir `textField` ABD Doları değeri gibi bir sembol katmanı seçeneğinde nasıl işleneceğini değiştirmek için bir ifade kullanır.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { 'currency': 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Bu katman, aşağıdaki görüntüde gösterildiği gibi nokta özelliğini oluşturacak:

<center>

![Sayı biçimi ifade örneği ](media/how-to-expressions/number-format-expression.png)</center>

### <a name="image-expression"></a>Görüntü ifadesi

Bir görüntü ifadesi, `image` `textField` bir sembol katmanının ve seçenekleriyle ve `fillPattern` Çokgen katmanının seçeneği ile kullanılabilir. Bu ifade, istenen görüntünün stilde var olduğunu denetler ve `null` görüntünün Şu anda stilde olup olmadığına bağlı olarak çözümlenen görüntü adını ya da geri dönecektir. Bu doğrulama işlemi zaman uyumludur ve görüntü bağımsız değişkeninde istenmeden önce görüntünün stile eklenmesini gerektirir.

**Örnek**

Aşağıdaki örnek, bir simge `image` katmanında metinle birlikte satır içi bir simge eklemek için bir ifade kullanır. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Bu katman, aşağıdaki görüntüde gösterildiği gibi sembol katmanındaki metin alanını oluşturacak:

<center>

![Görüntü ifadesi örneği ](media/how-to-expressions/image-expression.png)</center>

## <a name="zoom-expression"></a>Yakınlaştırma ifadesi

Bir `zoom` ifade, işleme zamanında haritanın geçerli yakınlaştırma düzeyini almak için kullanılır ve olarak tanımlanır `['zoom']` . Bu ifade haritanın en düşük ve en yüksek yakınlaştırma düzeyi aralığı arasında bir sayı döndürür. Azure, Web ve Android için etkileşimli harita denetimlerini, 0 ile 24 arasında numaralandırılmış 25 yakınlaştırma düzeyi destekler. İfadenin kullanılması, `zoom` haritanın yakınlaştırma düzeyi değiştiği için stillerin dinamik olarak değiştirilmesini sağlar. `zoom`İfade yalnızca `interpolate` ve `step` ifadeleriyle birlikte kullanılabilir.

**Örnek**

Varsayılan olarak, ısı haritası katmanında işlenen veri noktalarının yarıçapının tüm yakınlaştırma düzeyleri için sabit bir piksel yarıçapı vardır. Eşleme yakınlaştırıldığında, veri toplar ve ısı haritası katmanı farklı görünüyor. Her bir `zoom` yakınlaştırma düzeyi için yarıçapı ölçeklendirmek için, her bir veri noktasının haritanın aynı fiziksel alanını kapsaması gibi bir ifade kullanılabilir. Bu, ısı haritası katmanının daha statik ve tutarlı görünmesini sağlayacak. Haritanın her yakınlaştırma düzeyinin, önceki yakınlaştırma düzeyiyle dikey ve yatay olarak iki piksel daha vardır. Her yakınlaştırma düzeyiyle iki katına çıkar, yarıçapı ölçeklendirin, tüm yakınlaştırma düzeylerinde tutarlı görünen bir ısı haritası oluşturur. Bu, `zoom` `base 2 exponential interpolation` En düşük yakınlaştırma düzeyi için piksel yarıçapı ve aşağıda gösterildiği gibi hesaplanan en yüksek yakınlaştırma düzeyi için ölçeklendirilmiş bir yarıçap ile ifade kullanılarak gerçekleştirilebilir `2 * Math.pow(2, minZoom - maxZoom)` .

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        1, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[Bkz. canlı örnek](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Değişken bağlama ifadeleri

Değişken bağlama ifadeleri, bir hesaplamanın sonuçlarını bir değişkende depolar. Bu nedenle, hesaplama sonuçlarının bir ifadenin başka bir yerinde birden çok kez başvurulabilmesini sağlayabilirsiniz. Birçok hesaplamayı içeren ifadeler için yararlı bir iyileştirmedir.

| Expression | Dönüş türü | Description |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' Let ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: String,<br/>&nbsp;&nbsp;&nbsp;&nbsp;Değer1: Any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;AD2: dize,<br/>&nbsp;&nbsp;&nbsp;&nbsp;değer2: Any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Bir veya daha fazla değeri `var` , sonucu döndüren alt ifadede ifadesi tarafından kullanılmak üzere değişkenler olarak depolar. |
| `['var', name: string]` | herhangi biri | İfadesi kullanılarak oluşturulan bir değişkene başvurur `let` . |

**Örnek**

Bu örnek, sıcaklığa göre geliri hesaplayan bir ifade kullanır ve `case` Bu değer üzerinde farklı Boole işlemlerini değerlendirmek için bir ifade kullanır. Bu `let` ifade, geliri sıcaklık oranına göre depolamak için kullanılır, böylece yalnızca bir kez hesaplanmaları gerekir. `var`İfade, bu değişkene, yeniden hesaplamayı gerektirmeden gereken sıklıkta başvurur.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Sonraki adımlar

İfadeler uygulayan daha fazla kod örneği için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"] 
> [Sembol katmanı ekleme](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Baloncuk katmanı ekleme](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Çizgi katmanı ekleme](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Çokgen katmanı ekleme](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Isı haritası katmanı ekleme](map-add-heat-map-layer.md)

İfadeleri destekleyen katman seçenekleri hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

> [!div class="nextstepaction"] 
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)