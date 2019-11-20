---
title: Azure Haritalar Web SDK 'sında veri odaklı stil Ifadeleri | Microsoft Docs
description: Azure Maps web SDK 'sında veri tabanlı stil ifadeleri kullanma.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 507af54b8b4c2e7c67538a1a25a040c7ee5fdfd5
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976310"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Veri tabanlı stil Ifadeleri (Web SDK)

İfadeler, bir veri kaynağındaki her bir şekil için tanımlanan özellikleri gözlemleyecek stil seçeneklerine iş mantığı uygulamanızı sağlar. İfadeler, bir veri kaynağındaki veya katmandaki verileri filtrelemek için de kullanılabilir. İfadeler, if-deyimleri gibi koşullu mantığdan oluşabilir ve ile verileri işlemek için de kullanılabilir; dize, mantıksal ve matematik işleçleri. 

Veri odaklı stiller, Stillendirme etrafında iş mantığını uygulamak için gereken kod miktarını azaltabilir. Katmanlarla kullanıldığında, deyimler, Kullanıcı arabirimi iş parçacığı üzerinde iş mantığını değerlendirmeye kıyasla daha yüksek performans sağlayan ayrı bir iş parçacığında işleme zamanında değerlendirilir.

Aşağıdaki videoda, Azure Maps web SDK 'sında veri odaklı stillendirme hakkında bir genel bakış sunulmaktadır.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

İfadeler JSON dizileri olarak temsil edilir. Dizideki bir ifadenin ilk öğesi, ifade işlecinin adını belirten bir dizedir. Örneğin, "+" veya "Case". Sonraki öğeler (varsa), ifadenin bağımsız değişkenlerdir. Her bağımsız değişken bir değişmez değer (bir dize, sayı, Boolean veya `null`) ya da başka bir ifade dizisi. Aşağıdaki sözde kod, bir ifadenin temel yapısını tanımlar. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Haritalar Web SDK 'Sı, kendi başına veya diğer ifadelerle birlikte kullanılabilecek birçok türü destekler.

| İfade türü | Açıklama |
|---------------------|-------------|
| [Toplama ifadesi](#aggregate-expression) | Bir veri kümesi üzerinde işlenen ve ' a `clusterProperties` `DataSource`' seçeneği ile kullanılabilen bir hesaplamayı tanımlayan bir ifade. |
| [Boole ifadeleri](#boolean-expressions) | Boolean ifadeleri, Boole karşılaştırmaları değerlendirmek için bir dizi Boole işleci sağlar. |
| [Renk ifadeleri](#color-expressions) | Renk ifadeleri renk değerleri oluşturmayı ve işlemeyi kolaylaştırır. |
| [Koşullu ifadeler](#conditional-expressions) | Koşullu ifadeler, IF-deyimleri gibi mantıksal işlemler sağlar. |
| [Veri ifadeleri](#data-expressions) | Bir özelliğindeki Özellik verilerine erişim sağlar. |
| [Ara ve adım ifadelerini enterpolala](#interpolate-and-step-expressions) | Enterpolasyonlu ve step ifadeleri, bir ara değerli eğri veya adım işlevi üzerinde değerleri hesaplamak için kullanılabilir. |
| [Katmana özgü ifadeler](#layer-specific-expressions) | Yalnızca tek bir katman için geçerli olan özel ifadeler. |
| [Matematik ifadeleri](#math-expressions) | Expression Framework içinde veri odaklı hesaplamalar gerçekleştirmek için matematik işleçleri sağlar. |
| [Dize işleci ifadeleri](#string-operator-expressions) | Dize operatörü ifadeleri, büyük/küçük harfe dönüştürme ve dönüştürme gibi dizeler üzerinde dönüştürme işlemleri gerçekleştirir. |
| [Tür ifadeleri](#type-expressions) | Tür ifadeleri, dizeler, sayılar ve Boole değerleri gibi farklı veri türlerini test etmek ve dönüştürmek için araçlar sağlar. |
| [Değişken bağlama ifadeleri](#variable-binding-expressions) | Değişken bağlama ifadeleri, bir hesaplamanın sonuçlarının bir değişkende depolanmasını sağlar ve depolanan değeri yeniden hesaplamak zorunda kalmadan bir ifadenin başka bir yerinde bir yerde Başvurulmuş. |
| [Yakınlaştırma ifadesi](#zoom-expression) | İşleme zamanında haritanın geçerli yakınlaştırma düzeyini alır. |

Bu belgedeki tüm örnekler, farklı ifade türlerinin kullanılabileceği farklı yolları göstermek için aşağıdaki özelliği kullanır. 

```javascript
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
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Veri ifadeleri

Veri ifadeleri bir özelliğin özellik verilerine erişim sağlar. 

| İfade | Dönüş türü | Açıklama |
|------------|-------------|-------------|
| `['at', number, array]` | object | Diziden bir öğe alır. |
| `['geometry-type']` | dize | Özelliğin geometri türünü alır: Point, MultiPoint, LineString, MultiLineString, Çokgen, MultiPolygon. |
| `['get', string]` | value | Geçerli özelliğin özelliklerinden özellik değerini alır. İstenen özellik eksikse null değerini döndürür. |
| `['get', string, object]` | value | Belirtilen nesnenin özelliklerinden özellik değerini alır. İstenen özellik eksikse null değerini döndürür. |
| `['has', string]` | boolean | Özelliğin özelliklerinin belirtilen özelliğe sahip olup olmadığını belirler. |
| `['has', string, object]` | boolean | Nesnenin özelliklerinin belirtilen özelliğe sahip olup olmadığını belirler. |
| `['id']` | value | Özelliği varsa özelliğin KIMLIĞINI alır. |
| `['length', string | array]` | numarası | Bir dizenin veya dizinin uzunluğunu alır. |

**Örnekler**

Bir özelliğin özelliklerine, bir `get` ifade kullanarak doğrudan bir ifadede erişilebilir. Aşağıdaki örnek, bir kabarcık katmanının Color özelliğini belirtmek için özelliğinin "zoneColor" değerini kullanır. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Yukarıdaki örnek, tüm nokta özelliklerinin `zoneColor` özelliği varsa ince çalışacaktır, ancak yoksa, renk "siyah" a geri dönecektir. Geri dönüş rengini değiştirmek için bir `case` ifade, özelliğin mevcut olup olmadığını denetlemek için `has` ifadesiyle birlikte kullanılabilir ve bunun yerine bir geri dönüş rengi döndürmez.

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

Balon ve sembol katmanları, varsayılan olarak bir veri kaynağındaki tüm şekillerin koordinatlarını işler. Bu, bir çokgen veya çizginin köşelerini vurgulamak için yapılabilir. Katman seçeneği, bir Boolean ifadesinde `['geometry-type']` ifade kullanarak oluşturduğu özelliklerin geometri türünü sınırlamak için kullanılabilir. `filter` Aşağıdaki örnek, yalnızca `Point` özelliklerin işlenmesi için bir kabarcık katmanını sınırlandırır.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

Aşağıdaki örnek hem hem de `Point` `MultiPoint` özelliklerinin işlenmesine imkan sağlayacak. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Benzer şekilde, çokgenler ana hattı çizgi katmanlarında işlenir. Bir çizgi katmanında bu davranışı devre dışı bırakmak için yalnızca ve `LineString` `MultiLineString` özelliklerine izin veren bir filtre ekleyin.  

## <a name="math-expressions"></a>Matematik ifadeleri

Matematik ifadeleri, ifade çerçevesi içinde veri odaklı hesaplamalar gerçekleştirmek için matematik işleçleri sağlar.

| İfade | Dönüş türü | Açıklama |
|------------|-------------|-------------|
| `['+', number, number, …]` | numarası | Belirtilen sayıların toplamını hesaplar. |
| `['-', number]` | numarası | Belirtilen sayı ile 0 çıkartır. |
| `['-', number, number]` | numarası | İkinci sayıya göre ilk sayıları çıkartır. |
| `['*', number, number, …]` | numarası | Belirtilen sayıları birlikte çarpar. |
| `['/', number, number]` | numarası | İlk sayıyı ikinci sayıya böler. |
| `['%', number, number]` | numarası | İlk sayıyı ikinci sayıya bölerken kalanı hesaplar. |
| `['^', number, number]` | numarası | İkinci sayının gücünden çıkarılan ilk değerin değerini hesaplar. |
| `['abs', number]` | numarası | Belirtilen sayının mutlak değerini hesaplar. |
| `['acos', number]` | numarası | Belirtilen sayının arkkosinüsünü hesaplar. |
| `['asin', number]` | numarası | Belirtilen sayının arksinüsünü hesaplar. |
| `['atan', number]` | numarası | Belirtilen sayının ark tanjantını hesaplar. |
| `['ceil', number]` | numarası | Sayıyı bir sonraki tam tamsayıya yuvarlar. |
| `['cos', number]` | numarası | Belirtilen sayının cos sayısını hesaplar. |
| `['e']` | numarası | Matematik sabitini `e`döndürür. |
| `['floor', number]` | numarası | Sayıyı, önceki tam tamsayıya yuvarlar. |
| `['ln', number]` | numarası | Belirtilen sayının doğal logaritmasını hesaplar. |
| `['ln2']` | numarası | Matematik sabitini `ln(2)`döndürür. |
| `['log10', number]` | numarası | Belirtilen sayının 10 tabanında logaritmasını hesaplar. |
| `['log2', number]` | numarası | Belirtilen sayının temel iki logaritmasını hesaplar. |
| `['max', number, number, …]` | numarası | Belirtilen sayı kümesindeki en büyük sayıyı hesaplar. |
| `['min', number, number, …]` | numarası | Belirtilen sayı kümesindeki minimum sayıyı hesaplar. |
| `['pi']` | numarası | Matematik sabitini `PI`döndürür. |
| `['round', number]` | numarası | Sayıyı en yakın tamsayıya yuvarlar. Yarı-değerler sıfırdan uzağa yuvarlanır. Örneğin, `['round', -1.5]` -2 olarak değerlendirilir. |
| `['sin', number]` | numarası | Belirtilen sayının sinüsünü hesaplar. |
| `['sqrt', number]` | numarası | Belirtilen sayının kare kökünü hesaplar. |
| `['tan', number]` | numarası | Belirtilen sayının tanjantını hesaplar. |

## <a name="aggregate-expression"></a>Toplama ifadesi

Toplama ifadesi bir veri kümesi üzerinde işlenen ve ' a `clusterProperties` `DataSource`' seçeneği ile kullanılabilen bir hesaplamayı tanımlar. Bu ifadelerin çıktısı bir sayı veya Boole olmalıdır. 

Toplama ifadesi üç değer alır; bir işleç değeri ve başlangıç değeri ve toplama işleminin uygulanması için bir veri içindeki her özellikten bir özelliği almak için bir ifade. Bu ifade aşağıdaki biçimdedir:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- işlecinde Daha sonra, `mapExpression` kümedeki her bir nokta için hesaplanan tüm değerlere karşı uygulanan bir ifade işlevi. Desteklenen işleçler; 
    - Sayılar için: `+` `*`,,, `max``min`
    - Boole değerleri için `all`:,`any`
- InitialValue: İlk hesaplanan değerin oluşturulduğu başlangıç değeri.
- mapExpression: Veri kümesindeki her bir noktaya göre uygulanan bir ifade.

**Örnekler**

Bir veri kümesindeki tüm özelliklerin bir sayı olan bir `revenue` özelliği varsa. Veri kümesinden oluşturulan kümedeki tüm noktaların toplam geliri, aşağıdaki toplama ifadesi kullanılarak hesaplanabilir:`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Mantıksal ifadeler

Boolean ifadeleri, Boole karşılaştırmaları değerlendirmek için bir dizi Boole işleci sağlar.

Değerler karşılaştırılırken karşılaştırma kesin olarak yazılır. Farklı türlerin değerleri her zaman eşit kabul edilir. Türlerin ayrıştırma zamanında farklı olduğu bilinen durumlar geçersiz olarak değerlendirilir ve bir ayrıştırma hatası oluşturur. 

| İfade | Dönüş türü | Açıklama |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | Mantıksal değilleme. Girişin `true` olup `false`olmadığını ve`false`girişin olup olmadığını döndürür. `true` |
| `['!= ', value, value]` | boolean | Giriş `true` değerleri eşit değilse döndürür, `false` Aksi takdirde. |
| `['<', value, value]` | boolean | İlk `true` giriş ikinciden kesinlikle küçükse döndürür, `false` Aksi takdirde. Bağımsız değişkenlerin her iki dize veya iki sayı olması gerekir. |
| `['<=', value, value]` | boolean | İlk `true` girişin ikinciden küçük veya ona eşit olup olmadığını döndürür, `false` Aksi takdirde. Bağımsız değişkenlerin her iki dize veya iki sayı olması gerekir. |
| `['==', value, value]` | boolean | Giriş `true` değerlerinin eşitse, `false` Aksi takdirde döndürür. Bağımsız değişkenlerin her iki dize veya iki sayı olması gerekir. |
| `['>', value, value]` | boolean | İlk `true` girişin ikinciden kesinlikle büyük olup olmadığını döndürür, `false` Aksi takdirde. Bağımsız değişkenlerin her iki dize veya iki sayı olması gerekir. |
| `['>=' value, value]` | boolean | İlk `true` girişin ikinciden büyük veya ona eşit olup olmadığını döndürür, `false` Aksi takdirde. Bağımsız değişkenlerin her iki dize veya iki sayı olması gerekir. |
| `['all', boolean, boolean, …]` | boolean | Tüm `true` `false` girdilerin ,yoksa,döndürür.`true` |
| `['any', boolean, boolean, …]` | boolean | Girdilerden `true` herhangi `false` biri ise `true`, aksi takdirde döndürür. |

## <a name="conditional-expressions"></a>Koşullu ifadeler

Koşullu ifadeler, IF-deyimleri gibi mantıksal işlemler sağlar.

Aşağıdaki ifadeler giriş verilerinde koşullu mantık işlemleri gerçekleştirir. Örneğin, `case` ifade "if/then/else" mantığını `match` , ifade ise "switch-deyimi" gibi sağlar. 

### <a name="case-expression"></a>Case ifadesi

`case` İfade, Logic LIKE (if/then/else) gibi if deyimi sağlayan bir koşullu ifade türüdür. Bu tür ifade adımları bir Boole koşulları listesi aracılığıyla ve true olan ilk Boole koşulunun çıkış değerini döndürür.

Aşağıdaki sözde kod, `case` ifadenin yapısını tanımlar. 

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

Aşağıdaki örnek, olarak değerlendirilen bir tane bulana kadar farklı Boolean koşullarında adımlar sağlar `true`ve ardından ilgili değeri döndürür. Hiçbir Boole koşulu değerlendirilirse `true`, bir geri dönüş değeri döndürülür. 

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

`match` İfade, Logic gibi switch deyimi sağlayan bir koşullu ifade türüdür. Giriş, bir dize veya sayı döndüren gibi `['get', 'entityType']` herhangi bir ifade olabilir. Her etiket tek bir sabit değer değeri ya da değerlerinin tüm dizeler veya tüm sayılar olması gereken sabit değerler dizisi olmalıdır. Dizideki herhangi bir değer eşleşiyorsa, giriş eşleşir. Her etiket benzersiz olmalıdır. Giriş türü etiketlerin türüyle eşleşmiyorsa, sonuç geri dönüş değeri olacaktır.

Aşağıdaki sözde kod, `match` ifadenin yapısını tanımlar. 

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

Aşağıdaki örnek, bir kabarcık katmanındaki `entityType` bir nokta özelliğinin özelliğine bir eşleşme arayacağını bakar. Bir eşleşme bulursa, belirtilen değer döndürülür veya geri dönüş değerini döndürür.

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

Aşağıdaki örnek, hepsi aynı değeri döndürmesi gereken bir etiket kümesini listelemek için bir diziyi kullanır. Bu, her etiketin ayrı ayrı listelemesine göre çok daha etkilidir. Bu durumda `entityType` , özellik "Restoran" veya "grocery_store" ise, "kırmızı" rengi döndürülür.

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

Aşağıdaki örnek, bir "Array" veya "Array Contains" tür filtresi gerçekleştirmek için bir Match ifadesi kullanır. Bu durumda, izin verilen kimlikler listesinde bir ID değeri olan verileri filtreleme. Filtreler içeren ifadeler kullanılırken, sonucun bir Boole değeri olması gerekir.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>Birleşim ifadesi

İlk null olmayan değer alınana ve bu değeri döndürene kadar bir ifadekümesiaracılığıylaifadeadımları.`coalesce` 

Aşağıdaki sözde kod, `coalesce` ifadenin yapısını tanımlar. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Örnek**

Aşağıdaki örnek bir sembol katmanının `coalesce` `textField` seçeneğini ayarlamak için bir ifade kullanır. Özelliği özellikte yoksa veya olarak `null`ayarlandıysa, ifade `subtitle` daha sonra özelliği aramaya çalışır, veya `null`eksikse, daha sonra boş bir dizeye geri dönecektir. `title` 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

## <a name="type-expressions"></a>Tür ifadeleri

Tür ifadeleri, dizeler, sayılar ve Boole değerleri gibi farklı veri türlerini test etmek ve dönüştürmek için araçlar sağlar.

| İfade | Dönüş türü | Açıklama |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | dizi \| nesnesi | Sabit bir dizi veya nesne değeri döndürür. Bir dizi ya da nesnenin bir ifade olarak değerlendirilmesini engellemek için bu ifadeyi kullanın. Bir dizi ya da nesnenin bir ifade tarafından döndürülmesi gerektiğinde bu gereklidir. |
| `['to-boolean', value]` | boolean | Giriş değerini bir Boole değerine dönüştürür. `false` Sonuç, girişin boş bir dize `null` `false`, `0`,, veya `NaN`, aksi durumda `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | renk | Giriş değerini bir renge dönüştürür. Birden çok değer sağlanmışsa, ilk başarılı dönüştürme alınana kadar her biri sırayla değerlendirilir. Girdilerden hiçbiri dönüştürülemiyorsa, ifade bir hatadır. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | numarası | Mümkünse, giriş değerini bir sayıya dönüştürür. Giriş veya `null` `false`ise, sonuç 0 ' dır. Giriş ise `true`, sonuç 1 ' dir. Giriş bir dizeyse, ECMAScript dil belirtiminin [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) dize işlevini kullanarak bir sayıya dönüştürülür. Birden çok değer sağlanmışsa, ilk başarılı dönüştürme alınana kadar her biri sırayla değerlendirilir. Girdilerden hiçbiri dönüştürülemiyorsa, ifade bir hatadır. |
| `['to-string', value]` | dize | Giriş değerini bir dizeye dönüştürür. Giriş ise `null`, sonuç olur `""`. Giriş bir Boole ise, sonuç veya `"true"` `"false"`olur. Giriş bir sayı ise, ECMAScript dil belirtiminin [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) Number işlevi kullanılarak bir dizeye dönüştürülür. Giriş bir renkeyse CSS RGBA Color dizesine `"rgba(r,g,b,a)"`dönüştürülür. Aksi takdirde, giriş, ECMAScript dil belirtiminin [JSON. stringbelirt](https://tc39.github.io/ecma262/#sec-json.stringify) işlevi kullanılarak bir dizeye dönüştürülür. |
| `['typeof', value]` | dize | Verilen değerin türünü tanımlayan bir dize döndürür. |

> [!TIP]
> Tarayıcı konsolunda aşağıdakine benzer `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` bir hata iletisi varsa, kodunuzda bir yerde, ilk değeri için bir dize olmayan bir ifade olduğu anlamına gelir. İfadenin bir dizi döndürmesini istiyorsanız, diziyi `literal` ifadesiyle sarın. Aşağıdaki örnek, iki sayı içeren `offset` bir dize olması gereken bir sembol katmanının simge seçeneğini belirler, bu, noktanın `entityType` özelliğinin değerine göre iki adet `match` fark değeri arasında seçim yapmak için bir ifade kullanmaktır Özellik.
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
>             //If there is no title, try getting the subtitle. 
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

| İfade | Dönüş türü | Açıklama |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | renk | Ve `1` arasında`255` aralığa`0` gereken kırmızı, yeşil ve mavi bileşenlerden bir renk değeri oluşturur ve bir alfa bileşeni. Herhangi bir bileşen Aralık dışında olursa ifade bir hatadır. |
| `['rgba', number, number, number, number]` | renk | Ve `0` `1`arasında aralığagereken`0` *kırmızı*, *yeşil*, mavi bileşenlerden ve bir ve aralığı içindeki bir Alfa bileşeninden bir renk değeri oluşturur. `255` Herhangi bir bileşen Aralık dışında olursa ifade bir hatadır. |
| `['to-rgba']` | \[sayı, sayı, sayı, sayı\] | Giriş renginin *kırmızı*, *yeşil*, *mavi*ve *Alfa* bileşenlerini içeren dört öğeli bir diziyi bu sırayla döndürür. |

**Örnek**

Aşağıdaki örnek `255`, *kırmızı* bir değeri olan ve `temperature` özelliğinin değeri ile çarpılarak `2.5` hesaplanan *yeşil* ve *mavi* değerler içeren RGB renk değeri oluşturur. Sıcaklık değiştiği için renk, farklı *kırmızı*gölgelerle değişecektir.

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

| İfade | Dönüş türü | Açıklama |
|------------|-------------|-------------|
| `['concat', string, string, …]` | dize | Birden çok dizeyi birlikte birleştirir. Her değer bir dize olmalıdır. Gerekirse, diğer değer türlerini dizeye dönüştürmek için türifadesinikullanın.`to-string` |
| `['downcase', string]` | dize | Belirtilen dizeyi küçük harfe dönüştürür. |
| `['upcase', string]` | dize | Belirtilen dizeyi büyük harfe dönüştürür. |

**Örnek**

Aşağıdaki örnek, nokta özelliğinin `temperature` özelliğini bir dizeye dönüştürür ve sonra "°f" öğesini sonuna ekler.

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

![Dize işleci ifade örneği](media/how-to-expressions/string-operator-expression.png)</center>

## <a name="interpolate-and-step-expressions"></a>Ara ve adım ifadelerini enterpolala

Enterpolasyonlu ve step ifadeleri, bir ara değerli eğri veya adım işlevi üzerinde değerleri hesaplamak için kullanılabilir. Bu ifadeler, giriş olarak sayısal bir değer döndüren bir ifadeyi alır (örneğin `['get',  'temperature']`,). Giriş değeri, enterpolasyona veya Step işlevine en uygun değeri belirleyebilmek için, "Durdur" olarak adlandırılan giriş ve çıkış değerlerinin çiftlerine göre değerlendirilir. Her durun giriş değerleri bir sayı olmalı ve artan düzende olmalıdır. Çıkış değerleri bir sayı, sayı dizisi veya bir renk olmalıdır.

### <a name="interpolate-expression"></a>Enterpolageç ifadesi

Bir `interpolate` ifade, durdurma değerleri arasında ilişkilendirme yaparak sürekli, düzgün bir değer kümesini hesaplamak için kullanılabilir. Renk `interpolate` değerleri döndüren bir ifade, sonuç değerlerinin seçildiği bir renk gradyanı üretir.

Bir `interpolate` ifadede kullanılabilecek üç tür ilişkilendirme yöntemi vardır:
 
* `['linear']`-Durak çifti arasında doğrusal bir şekilde enterpolasyonlar.
* `['exponential', base]`-Duraklar arasında üstel olarak katlanarak enterpolasyonlar. `base` Değer, çıktının arttığı hızı denetler. Daha yüksek değerler, çıktıyı aralığın üst ucunda daha fazla artar. 1 `base` ' e yakın bir değer, daha fazla doğrusal bir şekilde artan bir çıktı üretir.
* `['cubic-bezier', x1, y1, x2, y2]`-Verilen denetim noktaları tarafından tanımlanan [üçüncü dereceden Bezier eğrisini](https://developer.mozilla.org/docs/Web/CSS/timing-function) kullanarak enterpolasyonlar.

İşte bu farklı türlerde ara nesnelerin nasıl görüneceğine ilişkin bir örnek. 

| Doğrusal  | Üstel | Üçüncü dereceden Bezier |
|---------|-------------|--------------|
| ![Doğrusal ilişkilendirme grafiği](media/how-to-expressions/linear-interpolation.png) | ![Üstel enterpolasyon grafiği](media/how-to-expressions/exponential-interpolation.png) | ![Üçüncü dereceden Bezier enterpolasyon grafiği](media/how-to-expressions/bezier-curve-interpolation.png) |

Aşağıdaki sözde kod, `interpolate` ifadenin yapısını tanımlar. 

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

Aşağıdaki örnek, nokta özelliğinin `linear interpolate` `temperature` özelliğine dayalı bir kabarcık `color` katmanının özelliğini ayarlamak için bir ifade kullanır. `temperature` Değer 60 ' den küçükse, "mavi" değeri, 60 ve 70 ' den az olursa sarı döndürülür, 70 ve daha az "kırmızı" döndürülürse, "turuncu 80" döndürülür.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

Aşağıdaki görüntüde, yukarıdaki ifade için renklerin nasıl seçildiği gösterilmektedir.
 
<center>

![Enterpolageç ifade](media/how-to-expressions/interpolate-expression-example.png) örneği</center>

### <a name="step-expression"></a>Adım ifadesi

Bir `step` ifade, duraklar tarafından tanımlanan bir [piecewise-sabit işlevi](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) değerlendirerek ayrık ve basamaklı sonuç değerlerini hesaplamak için kullanılabilir. 

Aşağıdaki sözde kod, `step` ifadenin yapısını tanımlar. 

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

Aşağıdaki örnek, nokta özelliğinin `step` `temperature` özelliğine dayalı bir kabarcık `color` katmanının özelliğini ayarlamak için bir ifade kullanır. `temperature` Değer 60 ' den küçükse, "mavi" değeri, 60 ve 70 ' den az olursa "sarı" döndürülür, 70 ve daha az "kırmızı" döndürülürse "turuncu 80" döndürülür.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
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

Isı haritası yoğunluğu ifadesi, ısı haritası katmanındaki her bir piksel için ısı haritası yoğunluğu değerini alır ve olarak `['heatmap-density']`tanımlanır. Bu `0` değer ile `1` arasında bir sayıdır ve, ısı haritasını renklendirmek için kullanılan renk `interpolation` degradesini tanımlamak için or `step` ifadesiyle birlikte kullanılır. Bu ifade yalnızca ısı haritası katmanının [Color seçeneğinde](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) kullanılabilir.

> [!TIP]
> Bir ilişkilendirme ifadesinde dizin 0 ' daki veya bir adım renginin varsayılan rengi olan renk, verilerin olmadığı alanın rengini tanımlar ve bir arka plan rengi tanımlamak için kullanılabilir. Birçok, bu değeri saydam veya yarı saydam bir siyah olarak ayarlamayı tercih eder. 

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

Bir ısı haritasını renklendirmeye yönelik düz bir gradyan kullanmanın yanı sıra, renkler bir `step` ifade kullanılarak bir aralıklar kümesi içinde belirlenebilir. Isı haritasının `step` renklendirilebilmesini için bir ifade kullanılması, yoğunluğu, bir dağılım veya radar stil eşlemesine benzer aralıklara görsel aralıklarla ayırır.  

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

Bir satır ilerleme ifadesi, ilerleme durumunu çizgi katmanında bir gradyan çizgisi üzerinde alır ve olarak `['line-progress']`tanımlanır. Bu değer 0 ile 1 arasında bir sayıdır ve `interpolation` or `step` ifadesi ile birlikte kullanılır. Bu ifade yalnızca çizgi katmanının [Strokegradient seçeneğiyle]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) birlikte kullanılabilir. 

> [!NOTE]
> Çizgi katmanının `lineMetrics` seçeneği,`true`veri kaynağı seçeneğinin olarak ayarlanmasını gerektirir. `strokeGradient`

**Örnek**

Aşağıdaki örnek, bir satırın `['line-progress']` konturuna bir renk gradyanı uygulamak için ifadesini kullanır.

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

Metin alanı biçimi ifadesi, karışık metin biçimlendirmesi sağlamak üzere `textField` sembol katmanları `textOptions` özelliğinin seçeneğiyle birlikte kullanılabilir. Bu ifade, bir giriş dizesi ve biçimlendirme seçenekleri kümesinin belirtilmesini sağlar. Bu ifadedeki her giriş dizesi için aşağıdaki seçenekler belirlenebilir.

 * `'font-scale'`-Yazı tipi boyutu için ölçekleme faktörünü belirtir. Belirtilmişse, bu değer bağımsız dize `size` `textOptions` için öğesinin özelliğini geçersiz kılar.
 * `'text-font'`-Bu dize için kullanılması gereken bir veya daha fazla yazı tipi ailesini belirtir. Belirtilmişse, bu değer bağımsız dize `font` `textOptions` için öğesinin özelliğini geçersiz kılar.

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

Aşağıdaki örnek, bir kalın yazı tipi ekleyerek ve özelliğin `title` özelliğinin yazı tipi boyutunu ölçeklendirerek metin alanını biçimlendirir. Bu örnek ayrıca, ölçeklenmiş `subtitle` bir yazı tipi boyutuyla bir yeni satır üzerinde özelliğin özelliğini de ekler.

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

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 'font-scale': 0.75 }
        ]
    }
});
```

Bu katman, aşağıdaki görüntüde gösterildiği gibi nokta özelliğini oluşturacak:
 
<center>

![Biçimli metin alanı](media/how-to-expressions/text-field-format-expression.png) olan nokta özelliğinin görüntüsü</center>

### <a name="number-format-expression"></a>Sayı biçimi ifadesi

İfade yalnızca bir sembol katmanının `textField` seçeneğiyle birlikte kullanılabilir. `number-format` Bu ifade, belirtilen sayıyı biçimli bir dizeye dönüştürür. Bu ifade, JavaScript [Number. toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) işlevini sarmalayan ve aşağıdaki seçenek kümesini destekler.

 * `locale`-Sayıları, belirtilen dille hizalanan bir şekilde dizelere dönüştürmek için bu seçeneği belirtin. Bu seçeneğe bir [BCP 47 Language etiketi](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) geçirin.
 * `currency`-Sayıyı bir para birimini temsil eden bir dizeye dönüştürmek için. Olası değerler [ıso 4217 para birimi kodlarıdır](https://en.wikipedia.org/wiki/ISO_4217), örneğin ABD Doları IÇIN "USD", Euro IÇIN "EUR" veya Çince RMB IÇIN "CNY" gibi.
 * `'min-fraction-digits'`-Sayının dize sürümüne dahil edilecek en az ondalık basamak sayısını belirtir.
 * `'max-fraction-digits'`-Sayının dize sürümüne dahil edilecek en fazla ondalık basamak sayısını belirtir.

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

Aşağıdaki örnek, nokta `number-format` `revenue` özelliğinin özelliğinin bir ABD Doları değeri gibi bir sembol katmanı seçeneğindenasılişleneceğinideğiştirmekiçinbirifadekullanır.`textField`

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Bu katman, aşağıdaki görüntüde gösterildiği gibi nokta özelliğini oluşturacak:

<center>

![Sayı biçimi ifade örneği](media/how-to-expressions/number-format-expression.png)</center>

## <a name="zoom-expression"></a>Yakınlaştırma ifadesi

Bir `zoom` ifade, işleme zamanında haritanın geçerli yakınlaştırma düzeyini almak için kullanılır ve olarak `['zoom']`tanımlanır. Bu ifade haritanın en düşük ve en yüksek yakınlaştırma düzeyi aralığı arasında bir sayı döndürür. Bu ifadenin kullanılması, haritanın yakınlaştırma düzeyi değiştiği için stillerin dinamik olarak değiştirilmesini sağlar. İfade yalnızca `interpolate` ve`step`ifadeleriylebirliktekullanılabilir. `zoom`

**Örnek**

Varsayılan olarak, ısı haritası katmanında işlenen veri noktalarının yarıçapının tüm yakınlaştırma düzeyleri için sabit bir piksel yarıçapı vardır. Eşleme, veri toplamlarını birlikte yakınlaştırırken ve ısı haritası katmanı farklı görünüyor. Her `zoom` bir yakınlaştırma düzeyi için yarıçapı ölçeklendirmek için, her bir veri noktasının haritanın aynı fiziksel alanını kapsaması gibi bir ifade kullanılabilir. Bu, ısı haritası katmanının daha statik ve tutarlı görünmesini sağlayacak. Haritanın her yakınlaştırma düzeyinin, önceki yakınlaştırma düzeyiyle dikey ve yatay olarak iki piksel daha vardır. Her yakınlaştırma düzeyiyle birlikte bulunan yarıçapı ölçeklemek, tüm yakınlaştırma düzeylerinde tutarlı görünen bir ısı haritası oluşturur. Bu, `zoom` ifadesi aşağıda gösterildiği gibi `base 2 exponential interpolation` ifadesiyle birlikte kullanılarak gerçekleştirilebilir. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[Bkz. canlı örnek](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Değişken bağlama ifadeleri

Değişken bağlama ifadeleri, bir hesaplamanın sonuçlarını, bir ifadenin başka bir yerinde yeniden hesaplanması gerekmeden birden çok kez başvurulabilmeleri için bir değişkende depolar. Bu, birçok hesaplamayı içeren ifadeler için yararlı bir iyileştirmedir

| İfade | Dönüş türü | Açıklama |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' Let ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: String,<br/>&nbsp;&nbsp;&nbsp;&nbsp;Değer1: Any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;AD2: dize,<br/>&nbsp;&nbsp;&nbsp;&nbsp;değer2: Any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;...<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Bir veya daha fazla değeri, sonucu döndüren alt ifadede `var` ifadesi tarafından kullanılmak üzere değişkenler olarak depolar. |
| `['var', name: string]` | herhangi biri | `let` İfadesi kullanılarak oluşturulan bir değişkene başvurur. |

**Örnek**

Bu örnek, sıcaklığa göre geliri hesaplayan bir ifade kullanır ve bu değer üzerinde farklı Boole `case` işlemlerini değerlendirmek için bir ifade kullanır. Bu ifade, geliri sıcaklık oranına göre depolamak için kullanılır, böylece yalnızca bir kez hesaplanmaları gerekir ve ifade, `var` bu değişkene yeniden hesaplama yapmak zorunda kalmadan gereken sıklıkta başvurur. `let`

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
> [Kabarcık katmanı ekleme](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Çizgi katmanı Ekle](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Çokgen katmanı Ekle](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Isı haritası katmanı ekleme](map-add-heat-map-layer.md)

İfadeleri destekleyen katman seçenekleri hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
