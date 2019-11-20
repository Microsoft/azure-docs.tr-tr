---
title: İfadelerdeki işlevler için başvuru kılavuzu-Azure Logic Apps
description: Azure Logic Apps ve güç otomatikleştirme için ifadelerde işlevlere yönelik başvuru kılavuzu
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 08/23/2019
ms.openlocfilehash: db33708fb96ff3e1982579c11a3928750a076f65
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582681"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Azure Logic Apps ve güç otomatikleştirme için ifadelerde işlevleri kullanmaya yönelik başvuru kılavuzu

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [Güç otomatikleştirmede](https://docs.microsoft.com/flow/getting-started)iş akışı tanımları için bazı [ifadeler](../logic-apps/logic-apps-workflow-definition-language.md#expressions) , iş akışınız çalışmaya başladığında henüz mevcut olmayan çalışma zamanı eylemlerinden değerlerini alır. Bu değerlere başvurmak veya bu deyimlerdeki değerleri işlemek için, [Iş akışı Tanım Dili](../logic-apps/logic-apps-workflow-definition-language.md)tarafından sunulan *işlevleri* kullanabilirsiniz. 

> [!NOTE]
> Bu başvuru sayfası hem Azure Logic Apps hem de güç otomatikleştirme için geçerlidir, ancak Azure Logic Apps belgelerinde görüntülenir. Bu sayfa özellikle Logic Apps 'e başvuruda bulunmasına rağmen, bu işlevler hem akışlar hem de Logic Apps için çalışır. Güç otomatikleştirmede işlevler ve ifadeler hakkında daha fazla bilgi için bkz. [koşullarda Ifadeleri kullanma](https://docs.microsoft.com/flow/use-expressions-in-conditions).

Örneğin, topla veya float değerlerinin toplamını istediğinizde, [Add () işlevi](../logic-apps/workflow-definition-language-functions-reference.md#add)gibi matematik işlevlerini kullanarak değerleri hesaplayabilirsiniz. İşlevlerle gerçekleştirebileceğiniz diğer örnek görevler şunlardır:

| Görev | İşlev sözdizimi | Sonuç |
| ---- | --------------- | ------ |
| Küçük harfli bir dize döndürür. | toLower (' <*metin*> ') <p>Örneğin: toLower (' Merhaba ') | herkese |
| Bir genel benzersiz tanımlayıcı (GUID) döndürür. | GUID () |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

İşlevleri [genel amaçlarına göre](#ordered-by-purpose)bulmak için aşağıdaki tabloları gözden geçirin. Ya da her işlev hakkında ayrıntılı bilgi için [alfabetik listeye](#alphabetical-list)bakın.

> [!NOTE]
> Parametre tanımlarının sözdiziminde, bir parametre sonrasında görünen bir soru işareti (?), parametrenin isteğe bağlı olduğu anlamına gelir.
> Örneğin bkz. [Getfuturetime ()](#getFutureTime).

## <a name="functions-in-expressions"></a>Deyimlerdeki işlevler

Bir ifadede bir işlevin nasıl kullanılacağını göstermek için bu örnek, `customerName` parametresinden değeri nasıl alabilirim ve bir ifadede [Parameters ()](#parameters) işlevini kullanarak bu değeri `accountName` özelliğine atayabilmenizi gösterir:

```json
"accountName": "@parameters('customerName')"
```

Deyimlerde işlevleri kullanabileceğiniz bazı genel yollar şunlardır:

| Görev | İfadede işlev sözdizimi |
| ---- | -------------------------------- |
| Öğeyi bir işleve geçirerek bir öğeyle çalışmayı gerçekleştirin. | "\@<*fonksiyonadı*> (<*öğesi*>)" |
| 1. iç içe `parameters()` işlevini kullanarak *ParameterName*'in değerini alın. </br>2. bu değeri *fonksiyonadı*değerine geçirerek sonuçla birlikte iş gerçekleştirin. | "\@<*fonksiyonadı*> (Parametreler (' <*ParameterName*> '))" |
| 1. iç içe geçmiş iç işlev *işlevbir*sonucu elde edin. </br>2. sonucu *functionName2*dış işlevine geçirin. | "\@<*functionName2*> (<*fonksiyonadı*> (<*öğe*>))" |
| 1. Sonuç olarak *fonksiyonadı*'dan elde edin. </br>2. sonucun *PropertyName*özelliğine sahip bir nesne olduğu için, bu özelliğin değerini alın. | "\@<*fonksiyonadı*> (<*öğesi*>). <*PropertyName*>" |
|||

Örneğin, `concat()` işlevi iki veya daha fazla dize değerini parametre olarak alabilir. Bu işlev bu dizeleri tek bir dize olarak birleştirir.
"Sophia" ve "Owen" gibi dize değişmez değerlerini geçirebilirsiniz, böylece "SophiaOwen" birleştirilmiş bir dize alırsınız:

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Ya da parametrelerden dize değerleri alabilirsiniz. Bu örnek her `concat()` parametresindeki `parameters()` işlevini ve `firstName` ve `lastName` parametrelerini kullanır. Daha sonra, "SophiaOwen" gibi Birleşik bir dize almanız için ortaya çıkan dizeleri `concat()` işleve geçirirsiniz:

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

Her iki durumda da örneklerin her ikisi de `customerName` özelliğine atanır.

Genel amaçlarına göre sıralanan kullanılabilir işlevler aşağıda verilmiştir veya [alfabetik sıraya](#alphabetical-list)göre işlevlere gidebilirsiniz.

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Dize işlevleri

Dizelerle çalışmak için, bu dize işlevlerini ve ayrıca bazı [koleksiyon işlevlerini](#collection-functions)kullanabilirsiniz.
Dize işlevleri yalnızca dizeler üzerinde çalışır.

| String işlevi | Görev |
| --------------- | ---- |
| [Concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | İki veya daha fazla dizeyi birleştirin ve Birleşik dizeyi döndürün. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Bir dizenin belirtilen alt dizeyle bitip bitmediğini denetleyin. |
| ['ini](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Bir dize olarak bir genel benzersiz tanımlayıcı (GUID) oluşturun. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Bir alt dize için başlangıç konumunu döndürür. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Alt dizenin son geçtiği başlangıç konumunu döndürür. |
| [değiştirin](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Bir alt dizeyi belirtilen dizeyle değiştirin ve güncelleştirilmiş dizeyi döndürün. |
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Özgün dizedeki belirtilen sınırlayıcı karakteri temel alan daha büyük bir dizeden, virgülle ayrılmış alt dizeler içeren bir dizi döndürür. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Bir dizenin belirli bir alt dizeyle başlatılıp başlatılmayacağını denetleyin. |
| [dizeden](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Belirtilen konumdan başlayarak bir dizeden karakter döndürün. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Küçük harfli bir dize döndürür. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Büyük harfle bir dize döndürür. |
| [kırpma](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Baştaki ve sondaki boşlukları bir dizeden kaldırın ve güncelleştirilmiş dizeyi döndürün. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Koleksiyon işlevleri

Koleksiyonlar, genellikle diziler, dizeler ve bazen sözlüklerle çalışmak için bu koleksiyon işlevlerini kullanabilirsiniz.

| Koleksiyon işlevi | Görev |
| ------------------- | ---- |
| [vardır](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Bir koleksiyonun belirli bir öğeye sahip olup olmadığını denetleyin. |
| [olmamalıdır](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Bir koleksiyonun boş olup olmadığını kontrol edin. |
| [adı](../logic-apps/workflow-definition-language-functions-reference.md#first) | Bir koleksiyondaki ilk öğeyi döndürür. |
| [imin](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Belirtilen koleksiyonlar genelinde *yalnızca* ortak öğeler içeren bir koleksiyon döndürür. |
| [maddesinin](../logic-apps/workflow-definition-language-functions-reference.md#item) | Bir dizi üzerinde yinelenen bir eylem içindeyken, eylemin geçerli yinelemesi sırasında dizideki geçerli öğeyi döndürün. |
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Bir diziden, belirtilen karakterle ayrılmış olan *Tüm* öğeleri içeren bir dize döndürür. |
| [soyadına](../logic-apps/workflow-definition-language-functions-reference.md#last) | Bir koleksiyondaki son öğeyi döndürür. |
| [uzunluklu](../logic-apps/workflow-definition-language-functions-reference.md#length) | Bir dize veya dizideki öğelerin sayısını döndürün. |
| [Şimdilik](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Öğeleri bir koleksiyonun önünden kaldırın ve *diğer tüm* öğeleri döndürün. |
| [almanız](../logic-apps/workflow-definition-language-functions-reference.md#take) | Bir koleksiyonun önünden öğe döndürün. |
| [birleşim](../logic-apps/workflow-definition-language-functions-reference.md#union) | Belirtilen koleksiyonlardaki *Tüm* öğeleri içeren bir koleksiyon döndürür. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Mantıksal karşılaştırma işlevleri

Koşullara göre çalışmak, değerleri ve ifade sonuçlarını karşılaştırmak veya çeşitli mantık türlerini değerlendirmek için bu mantıksal karşılaştırma işlevlerini kullanabilirsiniz.
Her işlev hakkında tam başvuru için [alfabetik listeye](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)bakın.

| Mantıksal karşılaştırma işlevi | Görev |
| --------------------------- | ---- |
| ['](../logic-apps/workflow-definition-language-functions-reference.md#and) | Tüm ifadelerin doğru olup olmadığını denetleyin. |
| [eşittir](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Her iki değerin de eşdeğer olup olmadığını denetleyin. |
| [ilerisi](../logic-apps/workflow-definition-language-functions-reference.md#greater) | İlk değerin ikinci değerden büyük olup olmadığını kontrol edin. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | İlk değerin ikinci değere eşit veya ondan büyük olup olmadığını kontrol edin. |
| [kullandıysanız](../logic-apps/workflow-definition-language-functions-reference.md#if) | İfadenin true veya false olduğunu denetleyin. Sonuca göre belirtilen değeri döndürün. |
| [büyüktür](../logic-apps/workflow-definition-language-functions-reference.md#less) | İlk değerin ikinci değerden küçük olup olmadığını kontrol edin. |
| [Lessotalals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | İlk değerin ikinci değere eşit veya ondan küçük olup olmadığını kontrol edin. |
| [başlatılmadı](../logic-apps/workflow-definition-language-functions-reference.md#not) | İfadenin yanlış olup olmadığını denetleyin. |
| [veya](../logic-apps/workflow-definition-language-functions-reference.md#or) | En az bir ifadenin doğru olup olmadığını denetleyin. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Dönüştürme işlevleri

Bir değerin türünü veya biçimini değiştirmek için bu dönüştürme işlevlerini kullanabilirsiniz.
Örneğin, Boole değerinden bir değeri tamsayı olarak değiştirebilirsiniz.
Dönüştürme sırasında Logic Apps içerik türlerini nasıl işleyeceğinden ilgili daha fazla bilgi için bkz. [tanıtıcı içerik türleri](../logic-apps/logic-apps-content-type.md).
Her işlev hakkında tam başvuru için [alfabetik listeye](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)bakın.

| Dönüştürme işlevi | Görev |
| ------------------- | ---- |
| [dizide](../logic-apps/workflow-definition-language-functions-reference.md#array) | Belirtilen tek bir girdiden bir dizi döndürür. Birden çok giriş için bkz. [Createarray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [biçiminde](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Bir dize için Base64 kodlamalı sürüm döndürün. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Base64 ile kodlanmış bir dize için ikili sürümü döndürün. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Base64 ile kodlanmış bir dize için dize sürümünü döndürün. |
| [ý](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Bir giriş değeri için ikili sürümü döndürün. |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Bir giriş değeri için Boole sürümü döndürün. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Birden çok girişe bir dizi döndürün. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Giriş değeri için veri URI 'sini döndürün. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Bir veri URI 'SI için ikili sürümü döndürün. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Bir veri URI 'SI için dize sürümünü döndürün. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Base64 ile kodlanmış bir dize için dize sürümünü döndürün. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Bir veri URI 'SI için ikili sürümü döndürün. |
| [decodeURIComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Kaçış karakterlerinin kodunu çözülmüş sürümlerle değiştiren bir dize döndürür. |
| [encodeURIComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | URL-güvenli olmayan karakterlerin yerine kaçış karakterleri içeren bir dize döndürür. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Giriş değeri için bir kayan nokta numarası döndürür. |
| ['tir](../logic-apps/workflow-definition-language-functions-reference.md#int) | Bir dize için tamsayı sürümünü döndürün. |
| [nesnesinde](../logic-apps/workflow-definition-language-functions-reference.md#json) | Bir dize veya XML için JavaScript Nesne Gösterimi (JSON) türü değerini veya nesnesini döndürün. |
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | Bir giriş değeri için dize sürümünü döndürün. |
| [URIComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | URL-güvenli olmayan karakterleri kaçış karakterleriyle değiştirerek, bir giriş değeri için URI kodlu sürümü döndürün. |
| [Urıonenttobinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | URI kodlamalı dize için ikili sürümü döndürün. |
| [Urıonenttostring](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | URI kodlamalı dize için dize sürümünü döndürün. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Bir dize için XML sürümünü döndürün. |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>Matematik işlevleri

Tamsayılar ve float ile çalışmak için, bu matematik işlevlerini kullanabilirsiniz.
Her işlev hakkında tam başvuru için [alfabetik listeye](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)bakın.

| Math işlevi | Görev |
| ------------- | ---- |
| [ekleyemiyorum](../logic-apps/workflow-definition-language-functions-reference.md#add) | İki sayı eklemenin sonucunu döndürür. |
| [DIV](../logic-apps/workflow-definition-language-functions-reference.md#div) | İki sayının bölünme sonucunu döndürür. |
| [Biçimlendir](../logic-apps/workflow-definition-language-functions-reference.md#max) | Sayı veya dizi kümesinden en yüksek değeri döndürün. |
| [Min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Sayı veya dizi kümesinden en düşük değeri döndürün. |
| [alma](../logic-apps/workflow-definition-language-functions-reference.md#mod) | İki sayıdan ayırarak kalanı döndürün. |
| [MUL](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Ürünü iki sayıdan çarpmadan döndürün. |
| [ran](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Belirtilen aralıktan rastgele bir tamsayı döndürür. |
| [aralığı](../logic-apps/workflow-definition-language-functions-reference.md#range) | Belirtilen tamsayıdan başlayan bir tamsayı dizisi döndürür. |
| [alt](../logic-apps/workflow-definition-language-functions-reference.md#sub) | İkinci sayının ilk sayıdan çıkarılmadan elde edilen sonucu döndürür. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Tarih ve saat işlevleri

Tarih ve saatlerle çalışmak için bu tarih ve saat işlevlerini kullanabilirsiniz.
Her işlev hakkında tam başvuru için [alfabetik listeye](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)bakın.

| Date veya Time işlevi | Görev |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Zaman damgasına bir gün sayısı ekleyin. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Zaman damgasına bir saat sayısı ekleyin. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Zaman damgasına bir dakika sayısı ekleyin. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Zaman damgasına bir saniye sayısı ekleyin. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Zaman damgasına bir dizi zaman birimi ekleyin. Ayrıca bkz. [Getfuturetime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [Convertfromulc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Evrensel Saat (UTC) zaman damgasını hedef saat dilimine dönüştürür. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Kaynak saat dilimindeki bir zaman damgasını hedef saat dilimine dönüştürür. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Kaynak saat diliminden bir zaman damgasını Eşgüdümlü Evrensel Saat (UTC) olarak dönüştürür. |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Zaman damgasından ay bileşeninin gününü döndürün. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Zaman damgasından hafta bileşeninin gününü döndürün. |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Zaman damgasından yıl bileşeninin gününü döndürün. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Zaman damgasından tarihi döndürün. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Geçerli zaman damgasını ve belirtilen zaman birimlerini döndürür. Ayrıca bkz. [Addtotime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Geçerli zaman damgasını belirtilen zaman birimleriyle döndürün. Ayrıca bkz. [alt Tractfromtime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Zaman damgası için günün başlangıcını döndürür. |
| [Saat başı](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Zaman damgası için saatin başlangıcını döndürür. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Zaman damgası için ayın başlangıcını döndürür. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Zaman damgasından birkaç zaman birimi çıkarın. Ayrıca bkz. [Getpasttime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [onay](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Belirtilen bir zaman damgası için `ticks` özellik değerini döndürün. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Geçerli zaman damgasını bir dize olarak döndürür. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>İş akışı işlevleri

Bu iş akışı işlevleri şunları yapmanıza yardımcı olabilir:

* Çalışma zamanında bir iş akışı örneği hakkındaki ayrıntıları alın.
* Logic Apps veya akışlar oluşturmak için kullanılan girişlerle çalışın.
* Tetikleyiciler ve eylemlerin çıktılarına başvurun.

Örneğin, bir eylemden çıkışlara başvurabilir ve bu verileri sonraki bir eylemde kullanabilirsiniz.
Her işlev hakkında tam başvuru için [alfabetik listeye](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)bakın.

| Workflow işlevi | Görev |
| ----------------- | ---- |
| [ön](../logic-apps/workflow-definition-language-functions-reference.md#action) | Çalışma zamanında geçerli eylemin çıkışını veya diğer JSON ad ve değer çiftleriyle değerleri döndürün. Ayrıca bkz. [Eylemler](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Çalışma zamanında bir eylemin `body` çıkışını döndürün. Ayrıca bkz. [gövde](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [Actionçıktılar](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Çalışma zamanında bir eylemin çıktısını döndürün. Bkz. [çıktılar](../logic-apps/workflow-definition-language-functions-reference.md#outputs) ve [Eylemler](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [eylem](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Çalışma zamanında bir eylemin çıkışını veya diğer JSON ad ve-değer çiftleriyle değerleri döndürün. Ayrıca bkz. [eylem](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [bölümü](#body) | Çalışma zamanında bir eylemin `body` çıkışını döndürün. Ayrıca bkz. [Actionbody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | *Form-Data* veya *form kodlu* eylem çıktılarında anahtar adı ile eşleşen değerlerle bir dizi oluşturun. |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Bir eylemin *form verileri* veya *form kodlu çıktısındaki*anahtar adı ile eşleşen tek bir değer döndürür. |
| [maddesinin](../logic-apps/workflow-definition-language-functions-reference.md#item) | Bir dizi üzerinde yinelenen bir eylem içindeyken, eylemin geçerli yinelemesi sırasında dizideki geçerli öğeyi döndürün. |
| [öğeler](../logic-apps/workflow-definition-language-functions-reference.md#items) | Bir foreach veya Until döngüsü içindeyken, belirtilen döngüden geçerli öğeyi döndürün.|
| [Iterationındexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Bir Until döngüsü içindeyken, geçerli yineleme için dizin değerini döndürür. Bu işlevi iç içe kadar, iç içe kadar kullanabilirsiniz. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Tetikleyici veya eylem çağıran "geri çağırma URL 'sini" döndürür. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Bir eylemin birden çok parçaya sahip olan çıkışında belirli bir bölümün gövdesini döndürün. |
| [outputs](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Çalışma zamanında bir eylemin çıktısını döndürün. |
| [parametreler](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | İş akışı tanımınızda açıklanan parametre için değeri döndürün. |
| [kaynaklanan](../logic-apps/workflow-definition-language-functions-reference.md#result) | `For_each`, `Until`ve `Scope`gibi belirtilen kapsamlı eylem içindeki tüm eylemlerin giriş ve çıkışlarını geri döndürün. |
| [Tetikleyicinin](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Çalışma zamanında bir tetikleyicinin çıkışını veya diğer JSON ad ve değer çiftlerinden döndürün. Ayrıca bkz. [Triggerçıktılar](#triggerOutputs) ve [triggerbody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Çalışma zamanında tetikleyicinin `body` çıkışını döndürün. Bkz. [tetikleyici](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | *Form-Data* veya *form kodlu* tetikleyici çıktılarında anahtar adı ile eşleşen tek bir değer döndürür. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Tetikleyicinin çok parçalı çıkışında belirli bir bölümün gövdesini döndürün. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | *Form-Data* veya *form kodlu* tetikleyici çıktılarında değerleri anahtar adıyla eşleşen bir dizi oluşturun. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Çalışma zamanında tetikleyicinin çıkışını veya diğer JSON ad ve-değer çiftleriyle değerleri döndürün. Bkz. [tetikleyici](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [değişkenlerinin](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Belirtilen değişken için değeri döndürün. |
| [akışıyla](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Çalışma zamanı sırasında iş akışı ile ilgili tüm ayrıntıları döndürür. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>URI ayrıştırma işlevleri

Tekdüzen Kaynak tanımlayıcıları (URI 'Ler) ile çalışmak ve bu URI 'Ler için çeşitli özellik değerleri almak için, bu URI ayrıştırma işlevlerini kullanabilirsiniz.
Her işlev hakkında tam başvuru için [alfabetik listeye](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)bakın.

| URI ayrıştırma işlevi | Görev |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Tekdüzen Kaynak tanımlayıcısı (URI) için `host` değeri döndürün. |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Tekdüzen Kaynak tanımlayıcısı (URI) için `path` değeri döndürün. |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Tekdüzen Kaynak tanımlayıcısı (URI) için `path` ve `query` değerlerini döndürün. |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Tekdüzen Kaynak tanımlayıcısı (URI) için `port` değeri döndürün. |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Tekdüzen Kaynak tanımlayıcısı (URI) için `query` değeri döndürün. |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Tekdüzen Kaynak tanımlayıcısı (URI) için `scheme` değeri döndürün. |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>İşleme işlevleri: JSON & XML

JSON nesneleri ve XML düğümleri ile çalışmak için bu işleme işlevlerini kullanabilirsiniz.
Her işlev hakkında tam başvuru için [alfabetik listeye](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)bakın.

| İşleme işlevi | Görev |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Bir özelliği ve değerini ya da ad-değer çiftini bir JSON nesnesine ekleyin ve güncelleştirilmiş nesneyi döndürün. |
| [Coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Bir veya daha fazla parametreden null olmayan ilk değeri döndürün. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | JSON nesnesinden bir özelliği kaldırın ve güncelleştirilmiş nesneyi döndürün. |
| [Özellik](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | JSON nesnesinin özelliği için değeri ayarlayın ve güncelleştirilmiş nesneyi döndürün. |
| [XPath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | XML 'yi bir XPath (XML Path Language) ifadesiyle eşleşen düğümler veya değerler için denetleyin ve eşleşen düğümleri veya değerleri döndürün. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Tüm işlevler-alfabetik liste

Bu bölüm, tüm kullanılabilir işlevleri alfabetik sırayla listeler.

<a name="action"></a>

### <a name="action"></a>action

Çalışma zamanında *geçerli* eylemin çıkışını veya diğer JSON ad ve-değer çiftleriyle bulunan değerleri döndürün, bu da ifadeye atayabilirsiniz.
Varsayılan olarak, bu işlev tüm eylem nesnesine başvurur, ancak isteğe bağlı olarak değerini istediğiniz bir özelliği belirtebilirsiniz.
Ayrıca bkz. [Eylemler ()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

`action()` işlevini yalnızca şu konumlarda kullanabilirsiniz:

* Bir Web kancası eyleminin `unsubscribe` özelliği, sonucu özgün `subscribe` isteğinden erişebilirsiniz
* Bir eylem için `trackedProperties` özelliği
* Bir eylem için `do-until` döngüsü koşulu

```
action()
action().outputs.body.<property>
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*özellik*> | Hayır | Dize | Eylem nesnesinin özelliğinin değeri istediğiniz adı: **Name**, **StartTime**, **bitişsaati**, **girişler**, **çıkışlar**, **durum**, **kod**, **trackingıd**ve **clienttrackingıd**. Azure portal, belirli bir çalıştırma geçmişinin ayrıntılarını inceleyerek bu özellikleri bulabilirsiniz. Daha fazla bilgi için bkz. [REST API-Iş akışı çalıştırma eylemleri](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | -----| ----------- |
| <*eylem-çıkış*> | Dize | Geçerli eylem veya özelliğin çıktısı |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Çalışma zamanında bir eylemin `body` çıkışını döndürün.
`actions('<actionName>').outputs.body`için toplu değer.
Bkz. [body ()](#body) ve [Actions ()](#actions).

```
actionBody('<actionName>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*ActionName*> | Evet | Dize | Eylemin istediğiniz `body` çıkışının adı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | -----| ----------- |
| <*eylem-gövde-çıkış*> | Dize | Belirtilen eylemden `body` çıktısı |
||||

*Örnek*

Bu örnek, Twitter eyleminden `body` çıkışını alır `Get user`:

```
actionBody('Get_user')
```

Ve şu sonucu döndürür:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>Actionçıktılar

Çalışma zamanında bir eylemin çıktısını döndürün.  `actions('<actionName>').outputs`için de toplu olur. Bkz. [Eylemler ()](#actions). `actionOutputs()` işlevi, Logic App Designer 'daki `outputs()` çözümleniyor, bu nedenle `actionOutputs()`yerine [çıktılar ()](#outputs)kullanmayı düşünün. Her iki işlev de aynı şekilde çalışır, ancak `outputs()` tercih edilir.

```
actionOutputs('<actionName>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*ActionName*> | Evet | Dize | Eylem için istediğiniz çıkışın adı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | -----| ----------- |
| *çıkış*> < | Dize | Belirtilen eylemden alınan çıkış |
||||

*Örnek*

Bu örnek, Twitter eyleminin `Get user`çıktısını alır:

```
actionOutputs('Get_user')
```

Ve şu sonucu döndürür:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>eylem

Çalışma zamanında bir eylemin çıktısını veya diğer JSON ad ve değer çiftlerinde bulunan değerleri bir ifadeye atayabilirsiniz. Varsayılan olarak, işlev tüm eylem nesnesine başvurur, ancak isteğe bağlı olarak değerini istediğiniz bir özelliği belirtebilirsiniz.
Toplu sürümler için bkz. [Actionbody ()](#actionBody), [actionçıktılar ()](#actionOutputs)ve [body ()](#body).
Geçerli eylem için bkz. [eylem ()](#action).

> [!NOTE]
> Daha önce, bir eylemin başka bir eylemden gelen çıktıya göre çalıştığını belirtirken `actions()` işlevini veya `conditions` öğesini kullanabilirsiniz. Ancak, eylemler arasında açık bağımlılıkları bildirmek için, artık bağımlı eylemin `runAfter` özelliğini kullanmanız gerekir.
> `runAfter` özelliği hakkında daha fazla bilgi edinmek için bkz. [runAfter özelliği ile sorunları yakalama ve işleme](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*ActionName*> | Evet | Dize | Çıktısı istediğiniz eylem nesnesinin adı  |
| <*özellik*> | Hayır | Dize | Eylem nesnesinin özelliğinin değeri istediğiniz adı: **Name**, **StartTime**, **bitişsaati**, **girişler**, **çıkışlar**, **durum**, **kod**, **trackingıd**ve **clienttrackingıd**. Azure portal, belirli bir çalıştırma geçmişinin ayrıntılarını inceleyerek bu özellikleri bulabilirsiniz. Daha fazla bilgi için bkz. [REST API-Iş akışı çalıştırma eylemleri](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | -----| ----------- |
| <*eylem-çıkış*> | Dize | Belirtilen eylem veya özelliğin çıktısı |
||||

*Örnek*

Bu örnek, çalışma zamanında `Get user` Twitter eyleminden `status` özellik değerini alır:

```
actions('Get_user').outputs.body.status
```

Ve şu sonucu döndürür: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

İki sayı eklemenin sonucunu döndürür.

```
add(<summand_1>, <summand_2>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Evet | Integer, float veya Mixed | Eklenecek numaralar |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | -----| ----------- |
| <*sonucu-sum*> | Tamsayı veya kayan | Belirtilen sayıları eklemenin sonucu |
||||

*Örnek*

Bu örnek, belirtilen sayıları ekler:

```
add(1, 1.5)
```

Ve şu sonucu döndürür: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Zaman damgasına bir gün sayısı ekleyin.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
| <*gün*> | Evet | Tamsayı | Eklenecek gün sayısının pozitif veya negatif sayısı |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-zaman damgası*> | Dize | Zaman damgası artı belirtilen gün sayısı  |
||||

*Örnek 1*

Bu örnek, belirtilen zaman damgasına 10 gün ekler:

```
addDays('2018-03-15T13:00:00Z', 10)
```

Ve şu sonucu döndürür: `"2018-03-25T00:00:0000000Z"`

*Örnek 2*

Bu örnek, belirtilen zaman damgasından beş gün çıkartır:

```
addDays('2018-03-15T00:00:00Z', -5)
```

Ve şu sonucu döndürür: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Zaman damgasına bir saat sayısı ekleyin.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
| <*saat*> | Evet | Tamsayı | Eklenecek saatlerin pozitif veya negatif sayısı |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-zaman damgası*> | Dize | Zaman damgası artı belirtilen saat sayısı  |
||||

*Örnek 1*

Bu örnek, belirtilen zaman damgasına 10 saat ekler:

```
addHours('2018-03-15T00:00:00Z', 10)
```

Ve şu sonucu döndürür: `"2018-03-15T10:00:0000000Z"`

*Örnek 2*

Bu örnek, belirtilen zaman damgasından beş saati çıkarır:

```
addHours('2018-03-15T15:00:00Z', -5)
```

Ve şu sonucu döndürür: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Zaman damgasına bir dakika sayısı ekleyin.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
| <*dakika*> | Evet | Tamsayı | Eklenecek pozitif veya negatif dakika sayısı |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-zaman damgası*> | Dize | Zaman damgası artı belirtilen dakika sayısı |
||||

*Örnek 1*

Bu örnek, belirtilen zaman damgasına 10 dakika ekler:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

Ve şu sonucu döndürür: `"2018-03-15T00:20:00.0000000Z"`

*Örnek 2*

Bu örnek, belirtilen zaman damgasından beş dakika çıkarır:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

Ve şu sonucu döndürür: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Bir özelliği ve değerini ya da ad-değer çiftini bir JSON nesnesine ekleyin ve güncelleştirilmiş nesneyi döndürün. Özellik çalışma zamanında zaten varsa, işlev başarısız olur ve bir hata oluşturur.

```
addProperty(<object>, '<property>', <value>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| *nesne*> < | Evet | Nesne | Özellik eklemek istediğiniz JSON nesnesi |
| <*özellik*> | Evet | Dize | Eklenecek özelliğin adı |
| <*değer*> | Evet | Herhangi biri | Özelliğin değeri |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-nesne*> | Nesne | Belirtilen özelliğe sahip güncelleştirilmiş JSON nesnesi |
||||

Varolan bir özelliğe bir alt özellik eklemek için şu sözdizimini kullanın:

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| *nesne*> < | Evet | Nesne | Özellik eklemek istediğiniz JSON nesnesi |
| <*üst özellik*> | Evet | Dize | Alt özellik eklemek istediğiniz üst özelliğin adı |
| <*alt özellik*> | Evet | Dize | Eklenecek alt özelliğin adı |
| <*değer*> | Evet | Herhangi biri | Belirtilen özellik için ayarlanacak değer |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-nesne*> | Nesne | Özelliği ayarladığınız güncelleştirilmiş JSON nesnesi |
||||

*Örnek 1*

Bu örnek, [JSON ()](#json) işlevi kullanılarak BIR dizeden JSON 'a dönüştürülen bir json nesnesine `middleName` özelliğini ekler. Nesne zaten `firstName` ve `surName` özelliklerini içeriyor. İşlevi belirtilen değeri yeni özelliğe atar ve güncelleştirilmiş nesneyi döndürür:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Şu anki JSON nesnesi aşağıdadır:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Güncelleştirilmiş JSON nesnesi şu şekildedir:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Örnek 2*

Bu örnek, [JSON ()](#json) işlevi kullanılarak BIR dizeden JSON 'a dönüştürülen bir JSON nesnesi içindeki var olan `customerName` özelliğine `middleName` alt özelliğini ekler. İşlevi belirtilen değeri yeni özelliğe atar ve güncelleştirilmiş nesneyi döndürür:

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

Şu anki JSON nesnesi aşağıdadır:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Güncelleştirilmiş JSON nesnesi şu şekildedir:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Zaman damgasına bir saniye sayısı ekleyin.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
| <*saniye*> | Evet | Tamsayı | Eklenecek pozitif veya negatif saniye sayısı |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-zaman damgası*> | Dize | Zaman damgası artı belirtilen saniye sayısı  |
||||

*Örnek 1*

Bu örnek, belirtilen zaman damgasına 10 saniye ekler:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

Ve şu sonucu döndürür: `"2018-03-15T00:00:10.0000000Z"`

*Örnek 2*

Bu örnek, beş saniyeyi belirtilen zaman damgasına çıkartır:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

Ve şu sonucu döndürür: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Zaman damgasına bir dizi zaman birimi ekleyin.
Ayrıca bkz. [Getfuturetime ()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
| <*aralık*> | Evet | Tamsayı | Eklenecek belirtilen zaman birimi sayısı |
| <*Timeunit*> | Evet | Dize | *Aralık*ile kullanılacak zaman birimi: "saniye", "dakika", "saat", "gün", "hafta", "ay", "yıl" |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-zaman damgası*> | Dize | Zaman damgası artı belirtilen zaman birimi sayısı  |
||||

*Örnek 1*

Bu örnek, belirtilen zaman damgasına bir gün ekler:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

Ve şu sonucu döndürür: `"2018-01-02T00:00:00.0000000Z"`

*Örnek 2*

Bu örnek, belirtilen zaman damgasına bir gün ekler:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

Ve isteğe bağlı "D" biçimini kullanarak sonucu döndürür: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>ve

Tüm ifadelerin doğru olup olmadığını denetleyin.
Tüm ifadeler doğru olduğunda true, en az bir ifade false olduğunda false döndürün.

```
and(<expression1>, <expression2>, ...)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*ifade1*>, <*İfade2*>,... | Evet | Boole | Denetlenecek ifadeler |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | -----| ----------- |
| true veya false | Boole | Tüm ifadeler true olduğunda true döndürün. En az bir ifade false olduğunda false döndürün. |
||||

*Örnek 1*

Bu örnekler, belirtilen Boolean değerlerinin tümünün doğru olup olmadığını denetler:

```
and(true, true)
and(false, true)
and(false, false)
```

Ve şu sonuçları döndürür:

* İlk örnek: her Iki ifade de doğrudur, bu nedenle `true`döndürür.
* İkinci örnek: bir ifade yanlış, bu nedenle `false`döndürür.
* Üçüncü örnek: her Iki ifade de false 'dur, bu nedenle `false`döndürür.

*Örnek 2*

Bu örnekler, belirtilen ifadelerin tümünün doğru olup olmadığını denetler:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

Ve şu sonuçları döndürür:

* İlk örnek: her Iki ifade de doğrudur, bu nedenle `true`döndürür.
* İkinci örnek: bir ifade yanlış, bu nedenle `false`döndürür.
* Üçüncü örnek: her Iki ifade de false 'dur, bu nedenle `false`döndürür.

<a name="array"></a>

### <a name="array"></a>array

Belirtilen tek bir girdiden bir dizi döndürür.
Birden çok giriş için bkz. [Createarray ()](#createArray).

```
array('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Dizi oluşturmak için dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| [<*değeri*>] | Dizi | Belirtilen tek girişi içeren bir dizi |
||||

*Örnek*

Bu örnek, "Hello" dizesinden bir dizi oluşturur:

```
array('hello')
```

Ve şu sonucu döndürür: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>biçiminde

Bir dize için Base64 kodlamalı sürüm döndürün.

```
base64('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Giriş dizesi |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*Base64 dize*> | Dize | Giriş dizesi için Base64 kodlamalı sürüm |
||||

*Örnek*

Bu örnek, "Hello" dizesini Base64 kodlamalı bir dizeye dönüştürür:

```
base64('hello')
```

Ve şu sonucu döndürür: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Base64 ile kodlanmış bir dize için ikili sürümü döndürün.

```
base64ToBinary('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Dönüştürülecek Base64 kodlamalı dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*binary-for-Base64 dize*> | Dize | Base64 ile kodlanmış dizenin ikili sürümü |
||||

*Örnek*

Bu örnek, "aGVsbG8 =" Base64 kodlamalı dizeyi ikili dizeye dönüştürür:

```
base64ToBinary('aGVsbG8=')
```

Ve şu sonucu döndürür:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Base64 ile kodlanmış bir dize için dize sürümünü döndürün ve Base64 dizesinin etkin bir şekilde kodunu çözerek.
Bu işlevi [decodeBase64 ()](#decodeBase64)yerine kullanın.
Her iki işlev de aynı şekilde çalışır, ancak `base64ToString()` tercih edilir.

```
base64ToString('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Kod çözme için Base64 kodlamalı dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*kodu çözülmüş-Base64 dizesi*> | Dize | Base64 ile kodlanmış bir dize için dize sürümü |
||||

*Örnek*

Bu örnek, "aGVsbG8 =" Base64 kodlamalı dizeyi yalnızca bir dizeye dönüştürür:

```
base64ToString('aGVsbG8=')
```

Ve şu sonucu döndürür: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>ý

Dize için ikili sürümü döndürün.

```
binary('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Dönüştürülecek dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*ikili-değer*> | Dize | Belirtilen dizenin ikili sürümü |
||||

*Örnek*

Bu örnek, "Hello" dizesini ikili dizeye dönüştürür:

```
binary('hello')
```

Ve şu sonucu döndürür:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>bölümü

Çalışma zamanında bir eylemin `body` çıkışını döndürün.
`actions('<actionName>').outputs.body`için toplu değer.
Bkz. [Actionbody ()](#actionBody) ve [Actions ()](#actions).

```
body('<actionName>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*ActionName*> | Evet | Dize | Eylemin istediğiniz `body` çıkışının adı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | -----| ----------- |
| <*eylem-gövde-çıkış*> | Dize | Belirtilen eylemden `body` çıktısı |
||||

*Örnek*

Bu örnek, `Get user` Twitter eyleminden `body` çıkışını alır:

```
body('Get_user')
```

Ve şu sonucu döndürür:

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>bool

Bir değer için Boolean sürümünü döndürün.

```
bool(<value>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Herhangi biri | Dönüştürülecek değer |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| true veya false | Boole | Belirtilen değer için Boolean sürümü |
||||

*Örnek*

Bu örnekler, belirtilen değerleri Boole değerlerine dönüştürür:

```
bool(1)
bool(0)
```

Ve şu sonuçları döndürür:

* İlk örnek: `true`
* İkinci örnek: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>Coalesce

Bir veya daha fazla parametreden null olmayan ilk değeri döndürün.
Boş dizeler, boş diziler ve boş nesneler null değil.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>,... | Evet | Herhangi biri, türleri karıştırabilirler | Null denetlenecek bir veya daha fazla öğe |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*ilk-null olmayan öğe*> | Herhangi biri | Null olmayan ilk öğe veya değer. Tüm parametreler null ise, bu işlev null değerini döndürür. |
||||

*Örnek*

Bu örnekler, belirtilen değerlerden null olmayan ilk değeri veya tüm değerler null olduğunda null değerini döndürür:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

Ve şu sonuçları döndürür:

* İlk örnek: `true`
* İkinci örnek: `"hello"`
* Üçüncü örnek: `null`

<a name="concat"></a>

### <a name="concat"></a>Concat

İki veya daha fazla dizeyi birleştirin ve Birleşik dizeyi döndürün.

```
concat('<text1>', '<text2>', ...)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*metin1*>, <*Metin2*>,... | Evet | Dize | Birleştirilecek en az iki dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*text1text2...* > | Dize | Birleşik giriş dizelerinden oluşturulan dize |
||||

*Örnek*

Bu örnek, "Hello" ve "World" dizelerini birleştirir:

```
concat('Hello', 'World')
```

Ve şu sonucu döndürür: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>vardır

Bir koleksiyonun belirli bir öğeye sahip olup olmadığını denetleyin.
Öğe bulunduğunda true, bulunamazsa false döndürün.
Bu işlev, büyük/küçük harfe duyarlıdır.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Özellikle, bu işlev bu koleksiyon türleri üzerinde çalışır:

* Alt *dize* bulmak için bir *dize*
* Bir *değer* bulmak için bir *dizi*
* *Anahtar* bulmak için bir *Sözlük*

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| *koleksiyon*> < | Evet | Dize, dizi veya sözlük | Denetlenecek koleksiyon |
| <*değer*> | Evet | Sırasıyla dize, dizi veya sözlük | Bulunacak öğe |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| true veya false | Boole | Öğe bulunduğunda true döndürün. Bulunamadığında false döndürün. |
||||

*Örnek 1*

Bu örnek, "World" alt dizesi için "Hello World" dizesini denetler ve true değerini döndürür:

```
contains('hello world', 'world')
```

*Örnek 2*

Bu örnek, "Universe" alt dizesi için "Hello World" dizesini denetler ve false döndürür:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>Convertfromulc

Evrensel Saat (UTC) zaman damgasını hedef saat dilimine dönüştürür.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
| <*destinationTimeZone*> | Evet | Dize | Hedef saat diliminin adı. Saat dilimi adları için bkz. [Microsoft saat dilimi Dizin değerleri](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ancak saat dilimi adından noktalama işaretlerini kaldırmanız gerekebilir. |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*dönüştürüldü-zaman damgası*> | Dize | Zaman damgası hedef saat dilimine dönüştürüldü |
||||

*Örnek 1*

Bu örnek, bir zaman damgasını belirtilen saat dilimine dönüştürür:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

Ve şu sonucu döndürür: `"2018-01-01T00:00:00.0000000"`

*Örnek 2*

Bu örnek, bir zaman damgasını belirtilen saat dilimine ve biçime dönüştürür:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

Ve şu sonucu döndürür: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Kaynak saat dilimindeki bir zaman damgasını hedef saat dilimine dönüştürür.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
| <*sourceTimeZone*> | Evet | Dize | Kaynak saat diliminin adı. Saat dilimi adları için bkz. [Microsoft saat dilimi Dizin değerleri](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ancak saat dilimi adından noktalama işaretlerini kaldırmanız gerekebilir. |
| <*destinationTimeZone*> | Evet | Dize | Hedef saat diliminin adı. Saat dilimi adları için bkz. [Microsoft saat dilimi Dizin değerleri](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ancak saat dilimi adından noktalama işaretlerini kaldırmanız gerekebilir. |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*dönüştürüldü-zaman damgası*> | Dize | Zaman damgası hedef saat dilimine dönüştürüldü |
||||

*Örnek 1*

Bu örnek, kaynak saat dilimini hedef saat dilimine dönüştürür:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

Ve şu sonucu döndürür: `"2018-01-01T00:00:00.0000000"`

*Örnek 2*

Bu örnek, saat dilimini belirtilen saat dilimine ve biçime dönüştürür:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

Ve şu sonucu döndürür: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Kaynak saat diliminden bir zaman damgasını Eşgüdümlü Evrensel Saat (UTC) olarak dönüştürür.

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
| <*sourceTimeZone*> | Evet | Dize | Kaynak saat diliminin adı. Saat dilimi adları için bkz. [Microsoft saat dilimi Dizin değerleri](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ancak saat dilimi adından noktalama işaretlerini kaldırmanız gerekebilir. |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*dönüştürüldü-zaman damgası*> | Dize | Zaman damgası UTC 'ye dönüştürüldü |
||||

*Örnek 1*

Bu örnek, bir zaman damgasını UTC 'ye dönüştürür:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

Ve şu sonucu döndürür: `"2018-01-01T08:00:00.0000000Z"`

*Örnek 2*

Bu örnek, bir zaman damgasını UTC 'ye dönüştürür:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

Ve şu sonucu döndürür: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Birden çok girişe bir dizi döndürün.
Tek giriş dizileri için bkz. [Array ()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>,... | Evet | Any, ancak karışık değil | Diziyi oluşturmak için en az iki öğe |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>,...] | Dizi | Tüm giriş öğelerinden oluşturulan dizi |
||||

*Örnek*

Bu örnek, bu girişlerden bir dizi oluşturur:

```
createArray('h', 'e', 'l', 'l', 'o')
```

Ve şu sonucu döndürür: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Bir dize için bir veri Tekdüzen Kaynak tanımlayıcısı (URI) döndürün.

```
dataUri('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Dönüştürülecek dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*veri-urı*> | Dize | Giriş dizesi için veri URI 'SI |
||||

*Örnek*

Bu örnek, "Hello" dizesi için bir veri URI 'SI oluşturur:

```
dataUri('hello')
```

Ve şu sonucu döndürür: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Bir veri Tekdüzen Kaynak tanımlayıcısı (URI) için ikili sürüm döndürün.
[Decodedatauri ()](#decodeDataUri)yerine bu işlevi kullanın.
Her iki işlev de aynı şekilde çalışır, ancak `dataUriBinary()` tercih edilir.

```
dataUriToBinary('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Dönüştürülecek veri URI 'SI |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*binary-for-Data-uri*> | Dize | Veri URI 'sinin ikili sürümü |
||||

*Örnek*

Bu örnek, bu veri URI 'SI için bir ikili sürüm oluşturur:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Ve şu sonucu döndürür:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Veri Tekdüzen Kaynak tanımlayıcısı (URI) için dize sürümünü döndürün.

```
dataUriToString('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Dönüştürülecek veri URI 'SI |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <- *for-Data-uri> String* | Dize | Veri URI 'SI için dize sürümü |
||||

*Örnek*

Bu örnek, bu veri URI 'SI için bir dize oluşturur:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Ve şu sonucu döndürür: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Zaman damgasından ayın gününü döndürür.

```
dayOfMonth('<timestamp>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*ayın günü*> | Tamsayı | Belirtilen zaman damgasından ayın günü |
||||

*Örnek*

Bu örnek, bu zaman damgasından ayın gününün numarasını döndürür:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

Ve şu sonucu döndürür: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Zaman damgasından haftanın gününü döndürün.

```
dayOfWeek('<timestamp>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*haftanın günü*> | Tamsayı | Pazar 0, Pazartesi 1, vb. belirtilen zaman damgasından haftanın günü |
||||

*Örnek*

Bu örnek, bu zaman damgasından Haftanın gününün numarasını döndürür:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

Ve şu sonucu döndürür: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Zaman damgasından yılın gününü döndürür.

```
dayOfYear('<timestamp>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*yılın günü*> | Tamsayı | Belirtilen zaman damgasından yılın günü |
||||

*Örnek*

Bu örnek, bu zaman damgasından yılın gün sayısını döndürür:

```
dayOfYear('2018-03-15T13:27:36Z')
```

Ve şu sonucu döndürür: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Base64 ile kodlanmış bir dize için dize sürümünü döndürün ve Base64 dizesinin etkin bir şekilde kodunu çözerek.
`decodeBase64()`yerine [base64ToString ()](#base64ToString) kullanmayı düşünün.
Her iki işlev de aynı şekilde çalışır, ancak `base64ToString()` tercih edilir.

```
decodeBase64('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Kod çözme için Base64 kodlamalı dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*kodu çözülmüş-Base64 dizesi*> | Dize | Base64 ile kodlanmış bir dize için dize sürümü |
||||

*Örnek*

Bu örnek Base64 kodlamalı dize için bir dize oluşturur:

```
decodeBase64('aGVsbG8=')
```

Ve şu sonucu döndürür: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Bir veri Tekdüzen Kaynak tanımlayıcısı (URI) için ikili sürüm döndürün.
`decodeDataUri()`yerine [Datauritobinary ()](#dataUriToBinary)kullanmayı düşünün.
Her iki işlev de aynı şekilde çalışır, ancak `dataUriToBinary()` tercih edilir.

```
decodeDataUri('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Kodu çözülecek veri URI dizesi |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*binary-for-Data-uri*> | Dize | Bir veri URI dizesinin ikili sürümü |
||||

*Örnek*

Bu örnek, bu veri URI 'SI için ikili sürümü döndürür:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Ve şu sonucu döndürür:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeURIComponent

Kaçış karakterlerinin kodunu çözülmüş sürümlerle değiştiren bir dize döndürür.

```
decodeUriComponent('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Kodu çözülecek kaçış karakterlerine sahip dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*kodu çözülmüş-urı*> | Dize | Kodu çözülen kaçış karakterlerine sahip güncelleştirilmiş dize |
||||

*Örnek*

Bu örnek, bu dizedeki kaçış karakterlerinin kodunu kodu çözülmüş sürümleriyle değiştirir:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

Ve şu sonucu döndürür: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>DIV

İki sayıdan ayırarak tamsayı sonucunu döndürür.
Kalan sonucu almak için bkz. [mod ()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*bölünen*> | Evet | Tamsayı veya kayan | *Bölen* tarafından bölünecek sayı |
| *bölen*> < | Evet | Tamsayı veya kayan | *Bölünen bölünen*sayı, ancak 0 olamaz |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*bölüm-sonuç*> | Tamsayı | İlk sayının ikinci sayıya bölünme sonucu tamsayı |
||||

*Örnek*

Her iki örnek de ilk sayıyı ikinci sayıya böler:

```
div(10, 5)
div(11, 5)
```

Ve şu sonucu döndürür: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeURIComponent

URL-güvenli olmayan karakterleri kaçış karakterleriyle değiştirerek dize için Tekdüzen Kaynak tanımlayıcısı (URI) kodlu bir sürüm döndürün.
`encodeUriComponent()`yerine [URIComponent ()](#uriComponent)kullanmayı düşünün.
Her iki işlev de aynı şekilde çalışır, ancak `uriComponent()` tercih edilir.

```
encodeUriComponent('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | URI kodlu biçime dönüştürülecek dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*kodlanmış urı*> | Dize | Kaçış karakterleri içeren URI kodlamalı dize |
||||

*Örnek*

Bu örnek, bu dize için URI kodlu bir sürüm oluşturur:

```
encodeUriComponent('https://contoso.com')
```

Ve şu sonucu döndürür: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>Olmamalıdır

Bir koleksiyonun boş olup olmadığını kontrol edin.
Koleksiyon boş olduğunda true döndürün ya da boş olmadığında false döndürün.

```
empty('<collection>')
empty([<collection>])
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| *koleksiyon*> < | Evet | String, array veya Object | Denetlenecek koleksiyon |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| true veya false | Boole | Koleksiyon boş olduğunda true döndürün. Boş olmadığında false döndürün. |
||||

*Örnek*

Bu örnekler, belirtilen koleksiyonların boş olup olmadığını denetler:

```
empty('')
empty('abc')
```

Ve şu sonuçları döndürür:

* İlk örnek: boş bir dizeyi geçirir, bu nedenle işlev `true`döndürür.
* İkinci örnek: "abc" dizesini geçirir, bu nedenle işlev `false`döndürür.

<a name="endswith"></a>

### <a name="endswith"></a>EndsWith

Bir dizenin belirli bir alt dizeyle bitip bitmediğini denetleyin.
Alt dize bulunduğunda true, bulunamazsa false döndürün.
Bu işlev, büyük/küçük harfe duyarlı değildir.

```
endsWith('<text>', '<searchText>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*metin*> | Evet | Dize | Denetlenecek dize |
| <*searchtext*> | Evet | Dize | Bulunacak bitiş alt dizesi |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| true veya false  | Boole | Bitiş alt dizesi bulunduğunda true döndürün. Bulunamadığında false döndürün. |
||||

*Örnek 1*

Bu örnek, "Hello World" dizesinin "World" dizesiyle bitip bitmediğini denetler:

```
endsWith('hello world', 'world')
```

Ve şu sonucu döndürür: `true`

*Örnek 2*

Bu örnek, "Hello World" dizesinin "Universe" dizesiyle bitip bitmediğini denetler:

```
endsWith('hello world', 'universe')
```

Ve şu sonucu döndürür: `false`

<a name="equals"></a>

### <a name="equals"></a>eşittir

Değerlerin, ifadelerin veya nesnelerin eşit olup olmadığını denetleyin.
Her ikisi de eşdeğer olduğunda true, eşdeğer olmadığında false döndürün.

```
equals('<object1>', '<object2>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Evet | Türlerini | Karşılaştırılacak değerler, ifadeler veya nesneler |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| true veya false | Boole | Her ikisi de eşdeğer olduğunda true döndürün. Eşdeğer olmadığında false döndürün. |
||||

*Örnek*

Bu örnekler, belirtilen girişlerin eşdeğer olup olmadığını denetler.

```
equals(true, 1)
equals('abc', 'abcd')
```

Ve şu sonuçları döndürür:

* İlk örnek: her Iki değer de eşdeğerdir, bu nedenle işlev `true`döndürür.
* İkinci örnek: her Iki değer de eşdeğer değildir, bu nedenle işlev `false`döndürür.

<a name="first"></a>

### <a name="first"></a>Adı

Bir dizeden veya diziden ilk öğeyi döndürür.

```
first('<collection>')
first([<collection>])
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| *koleksiyon*> < | Evet | Dize veya dizi | İlk öğenin bulunacağı koleksiyon |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*ilk koleksiyon-öğe*> | Herhangi biri | Koleksiyondaki ilk öğe |
||||

*Örnek*

Bu örneklerde, bu koleksiyonlardaki ilk öğe bulunur:

```
first('hello')
first(createArray(0, 1, 2))
```

Ve şu sonuçları döndürür:

* İlk örnek: `"h"`
* İkinci örnek: `0`

<a name="float"></a>

### <a name="float"></a>float

Kayan noktalı bir sayının dize sürümünü gerçek kayan noktalı sayıya dönüştürür.
Bu işlevi yalnızca bir uygulamaya özel parametreler geçirirken (örneğin, bir mantıksal uygulama veya Flow) kullanabilirsiniz.

```
float('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Dönüştürülecek geçerli bir kayan noktalı sayı olan dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*kayan değer*> | Float | Belirtilen dize için kayan noktalı sayı |
||||

*Örnek*

Bu örnek, bu kayan nokta numarası için bir dize sürümü oluşturur:

```
float('10.333')
```

Ve şu sonucu döndürür: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Belirtilen biçimde bir zaman damgası döndürün.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*yeniden biçimlendirilmiş-zaman damgası*> | Dize | Belirtilen biçimde güncelleştirilmiş zaman damgası |
||||

*Örnek*

Bu örnek, bir zaman damgasını belirtilen biçime dönüştürür:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

Ve şu sonucu döndürür: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Bir eylemin *form verileri* veya *form kodlu* çıkışındaki anahtar adı ile eşleşen değerler içeren bir dizi döndürür.

```
formDataMultiValues('<actionName>', '<key>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*ActionName*> | Evet | Dize | Çıktısı istediğiniz anahtar değere sahip olan eylem |
| <*anahtar*> | Evet | Dize | Değerini istediğiniz anahtarın adı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| [<*dizi-anahtar-değerleri*>] | Dizi | Belirtilen anahtarla eşleşen tüm değerleri içeren bir dizi |
||||

*Örnek*

Bu örnek, belirtilen işlemin form verileri veya form kodlu çıktısındaki "konu" anahtarının değerinden bir dizi oluşturur:

```
formDataMultiValues('Send_an_email', 'Subject')
```

Ve bir dizideki konu metnini döndürür, örneğin: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Bir eylemin *form verileri* veya *form kodlu* çıktısındaki anahtar adı ile eşleşen tek bir değer döndürür.
İşlev birden fazla eşleşme bulursa, işlev bir hata oluşturur.

```
formDataValue('<actionName>', '<key>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*ActionName*> | Evet | Dize | Çıktısı istediğiniz anahtar değere sahip olan eylem |
| <*anahtar*> | Evet | Dize | Değerini istediğiniz anahtarın adı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| *anahtar-değer*> < | Dize | Belirtilen anahtardaki değer  |
||||

*Örnek*

Bu örnek, belirtilen işlemin form verileri veya form kodlu çıktısındaki "konu" anahtarının değerinden bir dize oluşturur:

```
formDataValue('Send_an_email', 'Subject')
```

Ve konu metnini bir dize olarak döndürür, örneğin: `"Hello world"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Geçerli zaman damgasını ve belirtilen zaman birimlerini döndürür.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*aralık*> | Evet | Tamsayı | Eklenecek belirtilen zaman birimi sayısı |
| <*Timeunit*> | Evet | Dize | *Aralık*ile kullanılacak zaman birimi: "saniye", "dakika", "saat", "gün", "hafta", "ay", "yıl" |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-zaman damgası*> | Dize | Geçerli zaman damgası ve belirtilen zaman birimi sayısı |
||||

*Örnek 1*

Geçerli zaman damgasının "2018-03-01T00:00:00.0000000 Z" olduğunu varsayalım.
Bu örnek, bu zaman damgasına beş gün ekler:

```
getFutureTime(5, 'Day')
```

Ve şu sonucu döndürür: `"2018-03-06T00:00:00.0000000Z"`

*Örnek 2*

Geçerli zaman damgasının "2018-03-01T00:00:00.0000000 Z" olduğunu varsayalım.
Bu örnek beş gün ekler ve sonucu "D" biçimine dönüştürür:

```
getFutureTime(5, 'Day', 'D')
```

Ve şu sonucu döndürür: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Geçerli zaman damgasını belirtilen zaman birimleriyle döndürün.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*aralık*> | Evet | Tamsayı | Çıkarılacak belirtilen zaman birimi sayısı |
| <*Timeunit*> | Evet | Dize | *Aralık*ile kullanılacak zaman birimi: "saniye", "dakika", "saat", "gün", "hafta", "ay", "yıl" |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-zaman damgası*> | Dize | Geçerli zaman damgası, belirtilen zaman birimi sayısını eksi |
||||

*Örnek 1*

Geçerli zaman damgasının "2018-02-01T00:00:00.0000000 Z" olduğunu varsayalım.
Bu örnek, zaman damgasından beş gün çıkartır:

```
getPastTime(5, 'Day')
```

Ve şu sonucu döndürür: `"2018-01-27T00:00:00.0000000Z"`

*Örnek 2*

Geçerli zaman damgasının "2018-02-01T00:00:00.0000000 Z" olduğunu varsayalım.
Bu örnek beş günü çıkarır ve sonucu "D" biçimine dönüştürür:

```
getPastTime(5, 'Day', 'D')
```

Ve şu sonucu döndürür: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>ilerisi

İlk değerin ikinci değerden büyük olup olmadığını kontrol edin.
İlk değer daha fazla olduğunda true, küçükse false döndürün.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Integer, float veya String | İkinci değerden daha büyük olup olmadığını kontrol etmek için ilk değer |
| <*CompareTo*> | Evet | Sırasıyla tamsayı, float veya dize | Karşılaştırma değeri |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| true veya false | Boole | İlk değer ikinci değerden büyük olduğunda true döndürün. İlk değer ikinci değere eşit veya ondan küçük olduğunda false döndürün. |
||||

*Örnek*

Bu örnekler, ilk değerin ikinci değerden büyük olup olmadığını denetler:

```
greater(10, 5)
greater('apple', 'banana')
```

Ve şu sonuçları döndürür:

* İlk örnek: `true`
* İkinci örnek: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

İlk değerin ikinci değere eşit veya ondan büyük olup olmadığını kontrol edin.
İlk değer daha büyükse veya eşitse true, ilk değer ise false değerini döndürür.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Integer, float veya String | İkinci değerden büyük veya ona eşit olup olmadığını kontrol etmek için ilk değer |
| <*CompareTo*> | Evet | Sırasıyla tamsayı, float veya dize | Karşılaştırma değeri |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| true veya false | Boole | İlk değer ikinci değerden büyük veya bu değere eşit olduğunda true döndürün. İlk değer ikinci değerden küçük olduğunda false döndürün. |
||||

*Örnek*

Bu örnekler, ilk değerin ikinci değerden büyük veya eşit olup olmadığını denetler:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

Ve şu sonuçları döndürür:

* İlk örnek: `true`
* İkinci örnek: `false`

<a name="guid"></a>

### <a name="guid"></a>'ini

Dize olarak bir genel benzersiz tanımlayıcı (GUID) oluşturun, örneğin, "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Ayrıca, GUID için "D" varsayılan biçiminden farklı, kısa çizgi ile ayrılmış 32 basamaklı bir biçim belirleyebilirsiniz.

```
guid('<format>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*biçim*> | Hayır | Dize | Döndürülen GUID için tek bir [Biçim belirleyicisi](https://msdn.microsoft.com/library/97af8hh4) . Varsayılan olarak, "D" biçimindedir, ancak "N", "D", "B", "P" veya "X" kullanabilirsiniz. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*GUID-değer*> | Dize | Rastgele oluşturulmuş bir GUID |
||||

*Örnek*

Bu örnek, aynı GUID 'i, kısa çizgi ile ayrılmış ve parantez içine alınmış, 32 basamaklı olarak oluşturur:

```
guid('P')
```

Ve şu sonucu döndürür: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>kullandıysanız

İfadenin true veya false olduğunu denetleyin.
Sonuca göre belirtilen değeri döndürün.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*ifade*> | Evet | Boole | Denetlenecek ifade |
| <*Valueiftrue*> | Evet | Herhangi biri | İfade true olduğunda döndürülecek değer |
| <*Valueiffalse*> | Evet | Herhangi biri | İfade false olduğunda döndürülecek değer |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*belirtilen-Return-value*> | Herhangi biri | İfadenin true veya false olup olmadığına göre döndürülen belirtilen değer |
||||

*Örnek*

Belirtilen ifade true döndürdüğünden, bu örnek `"yes"` döndürür.
Aksi takdirde, örnek `"no"`döndürür:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Bir alt dize için başlangıç konumu veya dizin değeri döndürün.
Bu işlev, büyük/küçük harfe duyarlı değildir ve dizinler 0 sayısıyla başlar.

```
indexOf('<text>', '<searchText>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*metin*> | Evet | Dize | Bulunacak alt dizenin bulunduğu dize |
| <*searchtext*> | Evet | Dize | Bulunacak alt dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*Dizin-değer*>| Tamsayı | Belirtilen alt dizenin başlangıç konumu veya dizin değeri. <p>Dize bulunamazsa,-1 sayısını döndürün. |
||||

*Örnek*

Bu örnek, "Hello World" dizesindeki "World" alt dizesi için başlangıç dizini değerini bulur:

```
indexOf('hello world', 'world')
```

Ve şu sonucu döndürür: `6`

<a name="int"></a>

### <a name="int"></a>int

Bir dize için tamsayı sürümünü döndürün.

```
int('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Dönüştürülecek dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*tamsayı-sonuç*> | Tamsayı | Belirtilen dize için tamsayı sürümü |
||||

*Örnek*

Bu örnek, "10" dizesi için bir tamsayı sürümü oluşturur:

```
int('10')
```

Ve şu sonucu döndürür: `10`

<a name="item"></a>

### <a name="item"></a>Maddesinin

Bir dizi üzerinde yinelenen bir eylem içinde kullanıldığında, eylemin geçerli yinelemesi sırasında dizideki geçerli öğeyi döndürün.
Ayrıca, bu öğenin özelliklerindeki değerleri de alabilirsiniz.

```
item()
```

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*geçerli dizi öğesi*> | Herhangi biri | Eylemin geçerli yinelemesi için dizideki geçerli öğe |
||||

*Örnek*

Bu örnek, for-each döngüsünün geçerli yinelemesi içindeki "Send_an_email" eylemi için geçerli iletiden `body` öğesini alır:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>öğeler

Her bir döngüden geçerli öğeyi her bir döngüde döndürün.
Bu işlevi For-Each döngüsü içinde kullanın.

```
items('<loopName>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*Loopname*> | Evet | Dize | For-each döngüsünün adı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| *öğe*> < | Herhangi biri | Belirtilen for-each döngüsünde geçerli döngüdeki öğe |
||||

*Örnek*

Bu örnek, geçerli öğeyi belirtilen for-each döngüsünden alır:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>Iterationındexes

Geçerli yinelemenin dizin değerini bir Until döngüsü içinde döndürün. Bu işlevi iç içe kadar, iç içe kadar kullanabilirsiniz. 

```
iterationIndexes('<loopName>')
```

| Parametre | Gerekli | Tür | Açıklama | 
| --------- | -------- | ---- | ----------- | 
| <*Loopname*> | Evet | Dize | Until döngüsünün adı | 
||||| 

| Dönüş değeri | Tür | Açıklama | 
| ------------ | ---- | ----------- | 
| *dizin*> < | Tamsayı | Geçerli yinelemenin belirtilen Until döngüsü içindeki dizin değeri | 
|||| 

*Örnek* 

Bu örnek, bir sayaç değişkeni oluşturur ve sayaç değeri beş olana kadar bir Until döngüsü içindeki her yineleme sırasında bu değişkeni bir artırır. Örnek ayrıca her yineleme için geçerli dizini izleyen bir değişken oluşturur. Until döngüsünde, her yineleme sırasında, örnek sayacı artırır ve ardından sayaç değerini geçerli dizin değerine atar ve sonra sayacı artırır. İstediğiniz zaman, geçerli dizin değerini alarak geçerli yineleme numarasını belirleyebilirsiniz.

```
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5),
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>nesnesinde

Bir dize veya XML için JavaScript Nesne Gösterimi (JSON) türü değerini veya nesnesini döndürün.

```
json('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | String veya XML | Dönüştürülecek dize veya XML |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*JSON-sonuç*> | JSON yerel türü veya nesnesi | Belirtilen dize veya XML için JSON yerel tür değeri veya nesnesi. Dize null ise, işlev boş bir nesne döndürür. |
||||

*Örnek 1*

Bu örnek, bu dizeyi JSON değerine dönüştürür:

```
json('[1, 2, 3]')
```

Ve şu sonucu döndürür: `[1, 2, 3]`

*Örnek 2*

Bu örnek, bu dizeyi JSON öğesine dönüştürür:

```
json('{"fullName": "Sophia Owen"}')
```

Ve şu sonucu döndürür:

```
{
  "fullName": "Sophia Owen"
}
```

*Örnek 3*

Bu örnek, bu XML 'i JSON olarak dönüştürür:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

Ve şu sonucu döndürür:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>İmin

Belirtilen koleksiyonlar genelinde *yalnızca* ortak öğeler içeren bir koleksiyon döndürür.
Sonuçta görünmesi için, bu işleve geçirilen tüm koleksiyonlara bir öğe gelmelidir.
Bir veya daha fazla öğe aynı ada sahip ise, bu adı taşıyan son öğe sonuçta görüntülenir.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*Collection2*>,... | Evet | Dizi veya nesne, ancak her ikisi birden değil | *Yalnızca* ortak öğelerin olmasını istediğiniz Koleksiyonlar |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| *ortak öğeleri* <> | Sırasıyla dizi veya nesne | Belirtilen koleksiyonlar genelinde yalnızca ortak öğelere sahip olan bir koleksiyon |
||||

*Örnek*

Bu örnek, bu diziler genelinde ortak öğeleri bulur:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

Ve *yalnızca* şu öğeler içeren bir dizi döndürür: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Bir dizideki tüm öğeleri içeren ve her karakteri *sınırlayıcı*ile ayrılmış bir dize döndürür.

```
join([<collection>], '<delimiter>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| *koleksiyon*> < | Evet | Dizi | Katılacak öğelere sahip dizi |
| <*sınırlayıcı*> | Evet | Dize | Elde edilen dizedeki her karakter arasında görünen ayırıcı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*char1*><*sınırlayıcı*><*CHAR2*><*sınırlayıcı*>... | Dize | Belirtilen dizideki tüm öğelerden oluşturulan elde edilen dize |
||||

*Örnek*

Bu örnek, bu dizideki tüm öğelerden belirtilen karakteri sınırlayıcı olarak bir dize oluşturur:

```
join(createArray('a', 'b', 'c'), '.')
```

Ve şu sonucu döndürür: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>soyadına

Bir koleksiyondaki son öğeyi döndürür.

```
last('<collection>')
last([<collection>])
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| *koleksiyon*> < | Evet | Dize veya dizi | Son öğenin bulunacağı koleksiyon |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*Son koleksiyon öğesi*> | Sırasıyla dize veya dizi | Koleksiyondaki son öğe |
||||

*Örnek*

Bu örneklerde, bu koleksiyonlardaki son öğe bulunur:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

Ve şu sonuçları döndürür:

* İlk örnek: `"d"`
* İkinci örnek: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Bir alt dizenin son oluşumu için başlangıç konumunu veya dizin değerini döndürür.
Bu işlev, büyük/küçük harfe duyarlı değildir ve dizinler 0 sayısıyla başlar.

```
lastIndexOf('<text>', '<searchText>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*metin*> | Evet | Dize | Bulunacak alt dizenin bulunduğu dize |
| <*searchtext*> | Evet | Dize | Bulunacak alt dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*son dizin-değer*> | Tamsayı | Belirtilen alt dizenin son geçtiği konumun başlangıç konumu veya dizin değeri. <p>Dize bulunamazsa,-1 sayısını döndürün. |
||||

*Örnek*

Bu örnek, "Hello World" dizesinde "World" alt dizesinin son geçtiği başlangıç dizin değerini bulur:

```
lastIndexOf('hello world', 'world')
```

Ve şu sonucu döndürür: `6`

<a name="length"></a>

### <a name="length"></a>Uzunluklu

Bir koleksiyondaki öğelerin sayısını döndürün.

```
length('<collection>')
length([<collection>])
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| *koleksiyon*> < | Evet | Dize veya dizi | Sayılacak öğelerin bulunduğu koleksiyon |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*uzunluğu veya-say*> | Tamsayı | Koleksiyondaki öğelerin sayısı |
||||

*Örnek*

Bu örnekler, bu koleksiyonlardaki öğelerin sayısını sayar:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

Ve şu sonucu döndürür: `4`

<a name="less"></a>

### <a name="less"></a>büyüktür

İlk değerin ikinci değerden küçük olup olmadığını kontrol edin.
İlk değer daha az olduğunda true, ilk değer ise false değerini döndürür.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Integer, float veya String | İkinci değerden daha az olup olmadığını kontrol etmek için ilk değer |
| <*CompareTo*> | Evet | Sırasıyla tamsayı, float veya dize | Karşılaştırma öğesi |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| true veya false | Boole | İlk değer ikinci değerden küçük olduğunda true döndürün. İlk değer ikinci değerden daha büyük veya bu değere eşitse false döndürün. |
||||

*Örnek*

Bu örnekler, ilk değerin ikinci değerden küçük olup olmadığını denetler.

```
less(5, 10)
less('banana', 'apple')
```

Ve şu sonuçları döndürür:

* İlk örnek: `true`
* İkinci örnek: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>Lessotalals

İlk değerin ikinci değere eşit veya ondan küçük olup olmadığını kontrol edin.
İlk değer küçüktür veya eşitse true, ilk değer ise false değerini döndürür.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Integer, float veya String | İkinci değere eşit veya ondan küçük olup olmadığını kontrol etmek için ilk değer |
| <*CompareTo*> | Evet | Sırasıyla tamsayı, float veya dize | Karşılaştırma öğesi |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| true veya false  | Boole | İlk değer ikinci değerden küçük veya bu değere eşit olduğunda true döndürün. İlk değer ikinci değerden büyük olduğunda false döndürün. |
||||

*Örnek*

Bu örnekler, ilk değerin ikinci değerden küçük veya eşit olup olmadığını denetler.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

Ve şu sonuçları döndürür:

* İlk örnek: `true`
* İkinci örnek: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Tetikleyici veya eylem çağıran "geri çağırma URL 'sini" döndürür.
Bu işlev, yalnızca **Httpweb kancası** ve **Apiconnectionweb kancası** bağlayıcı türleri için Tetikleyiciler ve eylemlerle çalışır, ancak **Manual**, **yinelenme**, **http**ve **apiconnection** türlerini etkilemez.

```
listCallbackUrl()
```

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*geri arama-URL*> | Dize | Tetikleyici veya eylem için geri çağırma URL 'SI |
||||

*Örnek*

Bu örnekte, bu işlevin döndürebileceğini örnek bir geri çağırma URL 'SI gösterilmektedir:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>Biçimlendir

Bir liste veya diziden en yüksek değeri, her iki uçta da dahil olmak üzere sayı ile döndürün.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*sayı1*>, <*sayı2*>,... | Evet | Integer, float veya both | En yüksek değeri istediğiniz sayı kümesi |
| [<*sayı1*>, <*sayı2*>,...] | Evet | Dizi-tamsayı, kayan veya her ikisi | En yüksek değeri istediğiniz sayı dizisi |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*en yüksek değer*> | Tamsayı veya kayan | Belirtilen dizideki veya sayı kümesindeki en yüksek değer |
||||

*Örnek*

Bu örnekler, sayı ve dizi kümesinden en yüksek değeri alır:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

Ve şu sonucu döndürür: `3`

<a name="min"></a>

### <a name="min"></a>dk

Sayı veya dizi kümesinden en düşük değeri döndürün.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*sayı1*>, <*sayı2*>,... | Evet | Integer, float veya both | En düşük değeri istediğiniz sayı kümesi |
| [<*sayı1*>, <*sayı2*>,...] | Evet | Dizi-tamsayı, kayan veya her ikisi | En düşük değeri istediğiniz sayı dizisi |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*Minimum değer*> | Tamsayı veya kayan | Belirtilen sayı kümesindeki veya belirtilen dizi üzerinde en düşük değer |
||||

*Örnek*

Bu örnekler, sayı ve dizi kümesindeki en düşük değeri alır:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

Ve şu sonucu döndürür: `1`

<a name="mod"></a>

### <a name="mod"></a>alma

İki sayıdan ayırarak kalanı döndürün.
Tamsayı sonucunu almak için bkz. [div ()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*bölünen*> | Evet | Tamsayı veya kayan | *Bölen* tarafından bölünecek sayı |
| *bölen*> < | Evet | Tamsayı veya kayan | *Bölünen bölünen*sayı, ancak 0 olamaz. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*mod-sonuç*> | Tamsayı veya kayan | İlk sayının ikinci sayıya bölünmesiyle kalanı |
||||

*Örnek*

Bu örnek, ilk sayıyı ikinci sayıya böler:

```
mod(3, 2)
```

Ve şu sonucu döndürür: `1`

<a name="mul"></a>

### <a name="mul"></a>MUL

Ürünü iki sayıdan çarpmadan döndürün.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Evet | Tamsayı veya kayan | *Multiplicand2* ile çarpılacak sayı |
| <*multiplicand2*> | Evet | Tamsayı veya kayan | *Multiplicand1* Katalan sayı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*ürün-sonuç*> | Tamsayı veya kayan | Ürünün ilk sayıyı ikinci sayı ile çarpılmasıyla |
||||

*Örnek*

Bu örnekler ikinci sayının ilk numarasını birden çok kez göstermektedir:

```
mul(1, 2)
mul(1.5, 2)
```

Ve şu sonuçları döndürür:

* İlk örnek: `2`
* İkinci örnek `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Bir eylemin birden çok parçaya sahip olan çıkışında belirli bir bölümün gövdesini döndürün.

```
multipartBody('<actionName>', <index>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*ActionName*> | Evet | Dize | Birden çok parçaya çıkış içeren eylemin adı |
| *dizin*> < | Evet | Tamsayı | İstediğiniz bölümün dizin değeri |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*gövde*> | Dize | Belirtilen bölüm için gövde |
||||

<a name="not"></a>

### <a name="not"></a>Başlatılmadı

İfadenin yanlış olup olmadığını denetleyin.
İfade false olduğunda true, true olduğunda false döndürün.

```json
not(<expression>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*ifade*> | Evet | Boole | Denetlenecek ifade |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| true veya false | Boole | İfade false olduğunda true döndürün. İfade true olduğunda false döndürün. |
||||

*Örnek 1*

Bu örnekler, belirtilen ifadelerin yanlış olup olmadığını denetler:

```json
not(false)
not(true)
```

Ve şu sonuçları döndürür:

* İlk örnek: ifade false 'dur, bu nedenle işlev `true`döndürür.
* İkinci örnek: ifade true 'dur, bu nedenle işlev `false`döndürür.

*Örnek 2*

Bu örnekler, belirtilen ifadelerin yanlış olup olmadığını denetler:

```json
not(equals(1, 2))
not(equals(1, 1))
```

Ve şu sonuçları döndürür:

* İlk örnek: ifade false 'dur, bu nedenle işlev `true`döndürür.
* İkinci örnek: ifade true 'dur, bu nedenle işlev `false`döndürür.

<a name="or"></a>

### <a name="or"></a>or

En az bir ifadenin doğru olup olmadığını denetleyin.
En az bir ifade true olduğunda true, All false ise false döndürün.

```
or(<expression1>, <expression2>, ...)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*ifade1*>, <*İfade2*>,... | Evet | Boole | Denetlenecek ifadeler |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| true veya false | Boole | En az bir ifade true olduğunda true değerini döndürür. Tüm ifadeler false olduğunda false döndürün. |
||||

*Örnek 1*

Bu örnekler en az bir ifadenin doğru olup olmadığını denetler:

```json
or(true, false)
or(false, false)
```

Ve şu sonuçları döndürür:

* İlk örnek: en az bir ifade true, bu nedenle işlev `true`döndürüyor.
* İkinci örnek: her Iki ifade de false 'dur, bu nedenle işlev `false`döndürür.

*Örnek 2*

Bu örnekler en az bir ifadenin doğru olup olmadığını denetler:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

Ve şu sonuçları döndürür:

* İlk örnek: en az bir ifade true, bu nedenle işlev `true`döndürüyor.
* İkinci örnek: her Iki ifade de false 'dur, bu nedenle işlev `false`döndürür.

<a name="outputs"></a>

### <a name="outputs"></a>çıkışı

Çalışma zamanında bir eylemin çıkışlarını döndürün. Mantıksal uygulama tasarımcısında `outputs()` olarak çözümlenen `actionOutputs()`yerine bu işlevi kullanın. Her iki işlev de aynı şekilde çalışır, ancak `outputs()` tercih edilir.

```
outputs('<actionName>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*ActionName*> | Evet | Dize | Eylem için istediğiniz çıkışın adı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | -----| ----------- |
| *çıkış*> < | Dize | Belirtilen eylemden alınan çıkış |
||||

*Örnek*

Bu örnek, Twitter eyleminin `Get user`çıktısını alır:

```
outputs('Get_user')
```

Ve şu sonucu döndürür:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="parameters"></a>

### <a name="parameters"></a>parametreler

İş akışı tanımınızda açıklanan parametre için değeri döndürün.

```
parameters('<parameterName>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | Evet | Dize | Değerini istediğiniz parametre için ad |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*parametresi-değer*> | Herhangi biri | Belirtilen parametrenin değeri |
||||

*Örnek*

Bu JSON değerinin olduğunu varsayalım:

```json
{
  "fullName": "Sophia Owen"
}
```

Bu örnek, belirtilen parametre için değeri alır:

```
parameters('fullName')
```

Ve şu sonucu döndürür: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>ran

Yalnızca başlangıç ucunda olan belirtilen aralıktan rastgele bir tamsayı döndürür.

```
rand(<minValue>, <maxValue>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Evet | Tamsayı | Aralıktaki en küçük tamsayı |
| <*maxValue*> | Evet | Tamsayı | İşlevin döndürebilecekleri aralıktaki en yüksek tamsayıyı izleyen tamsayı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*rastgele sonuç*> | Tamsayı | Belirtilen aralıktan döndürülen rastgele tamsayı |
||||

*Örnek*

Bu örnek, en büyük değer hariç, belirtilen aralıktan rastgele bir tamsayı alır:

```
rand(1, 5)
```

Ve sonuç olarak şu numaralardan birini döndürür: `1`, `2`, `3`veya `4`

<a name="range"></a>

### <a name="range"></a>aralığı

Belirtilen tamsayıdan başlayan bir tamsayı dizisi döndürür.

```
range(<startIndex>, <count>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*startındex*> | Evet | Tamsayı | İlk öğe olarak diziyi Başlatan bir tamsayı değeri |
| <*sayı*> | Evet | Tamsayı | Dizideki tamsayıların sayısı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| [<*Range-sonuç*>] | Dizi | Belirtilen dizinden başlayan tamsayılar içeren dizi |
||||

*Örnek*

Bu örnek, belirtilen dizinden başlayan ve belirtilen sayıda tamsayı içeren bir tamsayı dizisi oluşturur:

```
range(1, 4)
```

Ve şu sonucu döndürür: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>değiştirin

Bir alt dizeyi belirtilen dizeyle değiştirin ve sonuç dizesini döndürün. Bu işlev, büyük/küçük harfe duyarlıdır.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*metin*> | Evet | Dize | Değiştirilecek alt dizenin bulunduğu dize |
| <*oldtext*> | Evet | Dize | Değiştirilecek alt dize |
| *newText*> < | Evet | Dize | Değiştirme dizesi |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirildi-metin*> | Dize | Alt dize değiştirildikten sonra güncelleştirilmiş dize <p>Alt dize bulunamazsa, özgün dizeyi döndürün. |
||||

*Örnek*

Bu örnek "eski" dizesinde "eski" alt dizesini bulur ve "Old" öğesini "New" ile değiştirir:

```
replace('the old string', 'old', 'new')
```

Ve şu sonucu döndürür: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Nesnesinden bir özelliği kaldırın ve güncelleştirilmiş nesneyi döndürün. Kaldırmaya çalıştığınız özellik yoksa, işlev orijinal nesneyi döndürür.

```
removeProperty(<object>, '<property>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| *nesne*> < | Evet | Nesne | Bir özelliği kaldırmak istediğiniz JSON nesnesi |
| <*özellik*> | Evet | Dize | Kaldırılacak özelliğin adı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-nesne*> | Nesne | Belirtilen özellik olmadan güncelleştirilmiş JSON nesnesi |
||||

Varolan bir özellikten bir alt özelliği kaldırmak için şu sözdizimini kullanın:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| *nesne*> < | Evet | Nesne | Özelliğini kaldırmak istediğiniz JSON nesnesi |
| <*üst özellik*> | Evet | Dize | Kaldırmak istediğiniz alt özelliği olan üst özelliğin adı |
| <*alt özellik*> | Evet | Dize | Kaldırılacak alt özelliğin adı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-nesne*> | Nesne | Alt özelliği kaldırdığınız güncelleştirilmiş JSON nesnesi |
||||

*Örnek 1*

Bu örnek, [JSON ()](#json) işlevini kullanarak BIR dizeden JSON 'a dönüştürülen ve güncelleştirilmiş nesneyi döndüren bir json nesnesinden `middleName` özelliğini kaldırır:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Şu anki JSON nesnesi aşağıdadır:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Güncelleştirilmiş JSON nesnesi şu şekildedir:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Örnek 2*

Bu örnek, JSON [()](#json) işlevini kullanarak BIR dizeden JSON 'a dönüştürülen ve güncelleştirilmiş nesneyi döndüren bir json nesnesindeki `customerName` parent özelliğinden `middleName` alt özelliğini kaldırır:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Şu anki JSON nesnesi aşağıdadır:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Güncelleştirilmiş JSON nesnesi şu şekildedir:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>kaynaklanan

`For_each`, `Until`veya `Scope` eylemi gibi, belirtilen kapsamlı eylem içindeki tüm eylemlerden gelen giriş ve çıkışları geri döndürün. Bu işlev, özel durumları tanılamanıza ve işleyebilmeniz için başarısız bir eylemden sonuçları döndürmektir. Daha fazla bilgi için bkz. [Içerik al ve hatalara yönelik sonuçlar](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures).

```
result('<scopedActionName>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*Scopedadctionname*> | Evet | Dize | Tüm iç eylemlerdeki girişlerin ve çıktıların döndürüleceği kapsamlı eylemin adı |
||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*dizi nesnesi*> | Dizi nesnesi | Belirtilen kapsamlı eylem içinde görünen her bir eylemden giriş ve çıkış dizilerini içeren bir dizi |
||||

*Örnek*

Bu örnek, `Compose` eyleminde `result()` işlevini kullanarak bir `For_each` döngüsünde içindeki bir HTTP eyleminin her yinelemesinden gelen giriş ve çıkışları döndürür:

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

Örneğin, Array döndürülen örneği, dış `outputs` nesnesinin, `For_each` eylemi içindeki eylemlerin her yinelemesinden gelen giriş ve çıkışları içermesi gibi görünebilir.

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>Özellik

JSON nesnesinin özelliği için değeri ayarlayın ve güncelleştirilmiş nesneyi döndürün. Ayarlamayı denerseniz özelliği yoksa, özelliği nesnesine eklenir. Yeni bir özellik eklemek için [AddProperty ()](#addProperty) işlevini kullanın.

```
setProperty(<object>, '<property>', <value>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| *nesne*> < | Evet | Nesne | Özelliğini ayarlamak istediğiniz JSON nesnesi |
| <*özellik*> | Evet | Dize | Ayarlanacak mevcut veya yeni özelliğin adı |
| <*değer*> | Evet | Herhangi biri | Belirtilen özellik için ayarlanacak değer |
|||||

Alt nesne içindeki alt özelliği ayarlamak için, bunun yerine iç içe `setProperty()` çağrısını kullanın. Aksi takdirde, işlev yalnızca çıkış olarak alt nesneyi döndürür.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| *nesne*> < | Evet | Nesne | Özelliğini ayarlamak istediğiniz JSON nesnesi |
| <*üst özellik*> | Evet | Dize | Ayarlamak istediğiniz alt özelliği olan üst özelliğin adı |
| <*alt özellik*> | Evet | Dize | Ayarlanacak alt özelliğin adı |
| <*değer*> | Evet | Herhangi biri | Belirtilen özellik için ayarlanacak değer |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-nesne*> | Nesne | Özelliği ayarladığınız güncelleştirilmiş JSON nesnesi |
||||

*Örnek 1*

Bu örnek, [JSON ()](#json) işlevi kullanılarak BIR dizeden JSON 'a dönüştürülen bir json nesnesinde `surName` özelliğini ayarlar. İşlevi belirtilen değeri özelliğine atar ve güncelleştirilmiş nesneyi döndürür:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Şu anki JSON nesnesi aşağıdadır:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Güncelleştirilmiş JSON nesnesi şu şekildedir:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Örnek 2*

Bu örnek, [JSON ()](#json) işlevi kullanılarak BIR dizeden JSON 'a dönüştürülen bir json nesnesinde `customerName` Parent özelliği için `surName` alt özelliğini ayarlar. İşlevi belirtilen değeri özelliğine atar ve güncelleştirilmiş nesneyi döndürür:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Şu anki JSON nesnesi aşağıdadır:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Güncelleştirilmiş JSON nesnesi şu şekildedir:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>Şimdilik

Öğeleri bir koleksiyonun önünden kaldırın ve *diğer tüm* öğeleri döndürün.

```
skip([<collection>], <count>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| *koleksiyon*> < | Evet | Dizi | Öğelerini kaldırmak istediğiniz koleksiyon |
| <*sayı*> | Evet | Tamsayı | En önünde kaldırılacak öğe sayısı için pozitif bir tamsayı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| [<*güncelleştirilmiş-koleksiyon*>] | Dizi | Belirtilen öğeler kaldırıldıktan sonra güncelleştirilmiş koleksiyon |
||||

*Örnek*

Bu örnek, belirtilen dizinin önüne 0 olan bir öğeyi, 0 sayısını kaldırır:

```
skip(createArray(0, 1, 2, 3), 1)
```

Ve bu diziyi kalan öğelerle birlikte döndürür: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Özgün dizedeki belirtilen sınırlayıcı karaktere göre virgülle ayırarak alt dizeler içeren bir dizi döndürür.

```
split('<text>', '<delimiter>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*metin*> | Evet | Dize | Özgün dizedeki belirtilen sınırlayıcıya göre alt dizelerdeki ayrı olacak dize |
| <*sınırlayıcı*> | Evet | Dize | Ayırıcı olarak kullanılacak özgün dizedeki karakter |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| [<*substring1*>, <*substring2*>,...] | Dizi | Virgülle ayırarak orijinal dizeden alt dizeler içeren bir dizi |
||||

*Örnek*

Bu örnek, belirtilen karakteri sınırlayıcı olarak belirtilen dizeden alt dizeler içeren bir dizi oluşturur:

```
split('a_b_c', '_')
```

Ve sonuç olarak bu diziyi döndürür: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Zaman damgası için günün başlangıcını döndürür.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-zaman damgası*> | Dize | Belirtilen zaman damgası, ancak günün sıfır saat işaretiyle başlıyor |
||||

*Örnek*

Bu örnek, bu zaman damgası için günün başlangıcını bulur:

```
startOfDay('2018-03-15T13:30:30Z')
```

Ve şu sonucu döndürür: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>Saat başı

Zaman damgası için saatin başlangıcını döndürür.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-zaman damgası*> | Dize | Belirtilen zaman damgası, ancak saat için sıfır dakikalık işaretten başlayarak |
||||

*Örnek*

Bu örnek, bu zaman damgası için saatin başlangıcını bulur:

```
startOfHour('2018-03-15T13:30:30Z')
```

Ve şu sonucu döndürür: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Zaman damgası için ayın başlangıcını döndürür.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-zaman damgası*> | Dize | Belirtilen zaman damgası, ancak ayın ilk günü sıfır saat işaretiyle başlıyor |
||||

*Örnek*

Bu örnek, bu zaman damgası için ayın başlangıcını döndürür:

```
startOfMonth('2018-03-15T13:30:30Z')
```

Ve şu sonucu döndürür: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Bir dizenin belirli bir alt dizeyle başlatılıp başlatılmayacağını denetleyin.
Alt dize bulunduğunda true, bulunamazsa false döndürün.
Bu işlev, büyük/küçük harfe duyarlı değildir.

```
startsWith('<text>', '<searchText>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*metin*> | Evet | Dize | Denetlenecek dize |
| <*searchtext*> | Evet | Dize | Bulunacak başlangıç dizesi |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| true veya false  | Boole | Başlangıç alt dizesi bulunduğunda true döndürün. Bulunamadığında false döndürün. |
||||

*Örnek 1*

Bu örnek, "Hello World" dizesinin "Hello" alt dizesiyle başlatılıp başlatılmayacağını denetler:

```
startsWith('hello world', 'hello')
```

Ve şu sonucu döndürür: `true`

*Örnek 2*

Bu örnek, "Hello World" dizesinin "Greetings" alt dizesiyle başlatılıp başlatılmayacağını denetler:

```
startsWith('hello world', 'greetings')
```

Ve şu sonucu döndürür: `false`

<a name="string"></a>

### <a name="string"></a>string

Bir değer için dize sürümünü döndürün.

```
string(<value>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Herhangi biri | Dönüştürülecek değer |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*dize-değer*> | Dize | Belirtilen değer için dize sürümü |
||||

*Örnek 1*

Bu örnek, bu sayının dize sürümünü oluşturur:

```
string(10)
```

Ve şu sonucu döndürür: `"10"`

*Örnek 2*

Bu örnek, belirtilen JSON nesnesi için bir dize oluşturur ve çift tırnak işareti (") için bir çıkış karakteri olarak ters eğik çizgi karakterini (\\) kullanır.

```
string( { "name": "Sophie Owen" } )
```

Ve şu sonucu döndürür: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>alt

İkinci sayının ilk sayıdan çıkarılmadan elde edilen sonucu döndürür.

```
sub(<minuend>, <subtrahend>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*eksilen*> | Evet | Tamsayı veya kayan | *Çıkarılan* çıkarmak için gereken sayı |
| <*çıkarılan*> | Evet | Tamsayı veya kayan | *Eksilen* 'ten çıkarılacak sayı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*sonucu*> | Tamsayı veya kayan | İkinci sayının ilk sayıdan çıkarılmadan elde edilen sonuç |
||||

*Örnek*

Bu örnek, ikinci sayıyı ilk sayıdan çıkartır:

```
sub(10.3, .3)
```

Ve şu sonucu döndürür: `10`

<a name="substring"></a>

### <a name="substring"></a>dizeden

Belirtilen konumdan veya dizinden başlayarak bir dizeden karakter döndürün.
Dizin değerleri 0 sayısıyla başlar.

```
substring('<text>', <startIndex>, <length>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*metin*> | Evet | Dize | Karakterleri istediğiniz dize |
| <*startındex*> | Evet | Tamsayı | Başlangıç konumu veya dizin değeri olarak kullanmak istediğiniz pozitif bir sayı eşittir veya 0 ' dan büyük bir değer |
| <*uzunluğu*> | Evet | Tamsayı | Alt dizede istediğiniz pozitif karakter sayısı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <alt *dize-sonuç*> | Dize | Kaynak dizedeki belirtilen dizin konumunda başlayan, belirtilen sayıda karakter içeren bir alt dize |
||||

*Örnek*

Bu örnek, 6 dizininden başlayarak belirtilen dizeden beş karakterlik bir alt dize oluşturur:

```
substring('hello world', 6, 5)
```

Ve şu sonucu döndürür: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Zaman damgasından birkaç zaman birimi çıkarın.
Ayrıca bkz. [Getpasttime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgasını içeren dize |
| <*aralık*> | Evet | Tamsayı | Çıkarılacak belirtilen zaman birimi sayısı |
| <*Timeunit*> | Evet | Dize | *Aralık*ile kullanılacak zaman birimi: "saniye", "dakika", "saat", "gün", "hafta", "ay", "yıl" |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-zaman damgası*> | Dize | Zaman damgası, belirtilen zaman birimi sayısını eksi |
||||

*Örnek 1*

Bu örnek, bir günü bu zaman damgasından çıkarır:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

Ve şu sonucu döndürür: `"2018-01-01T00:00:00:0000000Z"`

*Örnek 2*

Bu örnek, bir günü bu zaman damgasından çıkarır:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

Ve isteğe bağlı "D" biçimini kullanarak bu sonucu döndürür: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>Almanız

Bir koleksiyonun önünden öğe döndürün.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| *koleksiyon*> < | Evet | Dize veya dizi | Öğelerini istediğiniz koleksiyon |
| <*sayı*> | Evet | Tamsayı | Önünden istediğiniz öğe sayısı için pozitif bir tamsayı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*alt küme*> veya [<*alt kümesi*>] | Sırasıyla dize veya dizi | Özgün koleksiyonun önünden belirtilen sayıda öğe içeren bir dize veya dizi |
||||

*Örnek*

Bu örneklerde, bu koleksiyonların önüne belirtilen sayıda öğe alınır:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

Ve şu sonuçları döndürür:

* İlk örnek: `"abc"`
* İkinci örnek: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>onay

Belirtilen bir zaman damgası için `ticks` özellik değerini döndürün.
*Değer* bir 100-nanosaniyelik aralığıdır.

```
ticks('<timestamp>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*zaman damgası*> | Evet | Dize | Zaman damgası için dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*ticks-sayı*> | Tamsayı | Belirtilen zaman damgasından bu yana geçen onay işareti sayısı |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Küçük harfli bir dize döndürür. Dizedeki bir karakter küçük harfli bir sürüme sahip değilse, bu karakter döndürülen dizede değişmeden kalır.

```
toLower('<text>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*metin*> | Evet | Dize | Küçük harfle döndürülecek dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*küçük harfli metin*> | Dize | Özgün dize küçük harfli biçimde |
||||

*Örnek*

Bu örnek, bu dizeyi küçük harfe dönüştürür:

```
toLower('Hello World')
```

Ve şu sonucu döndürür: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Büyük harfle bir dize döndürür. Dizedeki bir karakter büyük bir sürüme sahip değilse, bu karakter döndürülen dizede değişmeden kalır.

```
toUpper('<text>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*metin*> | Evet | Dize | Büyük harfle döndürülecek dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| *büyük harf metin*> < | Dize | Büyük harfli özgün dize |
||||

*Örnek*

Bu örnek, bu dizeyi büyük harfe dönüştürür:

```
toUpper('Hello World')
```

Ve şu sonucu döndürür: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>Tetikleyicinin

Çalışma zamanında bir tetikleyicinin çıkışını veya diğer JSON ad ve-değer çiftleriyle değerleri bir ifadeye atayabilirsiniz.

* Tetikleyicinin girişlerinin içinde, bu işlev önceki yürütmenin çıktısını döndürür.

* Tetikleyicinin koşulunun içinde, bu işlev geçerli yürütmenin çıktısını döndürür.

Varsayılan olarak, işlevi tüm tetikleyici nesnesine başvurur, ancak isteğe bağlı olarak değerini istediğiniz bir özelliği belirtebilirsiniz.
Ayrıca, bu işlevde toplu sürümler mevcuttur, bkz. [Triggerçıktılar ()](#triggerOutputs) ve [triggerbody ()](#triggerBody).

```
trigger()
```

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*tetikleyicisi-çıkış*> | Dize | Çalışma zamanında bir tetikleyiciden alınan çıkış |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Çalışma zamanında tetikleyicinin `body` çıkışını döndürün.
`trigger().outputs.body`için toplu değer.
Bkz. [Tetikleyici ()](#trigger).

```
triggerBody()
```

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*tetikleyicisi-gövde-çıkış*> | Dize | Tetikleyiciden alınan `body` çıktısı |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Tetikleyicisinin *form verileri* veya *form kodlu* çıktıdaki anahtar adı ile eşleşen değerler içeren bir dizi döndürür.

```
triggerFormDataMultiValues('<key>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*anahtar*> | Evet | Dize | Değerini istediğiniz anahtarın adı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| [<*dizi-anahtar-değerleri*>] | Dizi | Belirtilen anahtarla eşleşen tüm değerleri içeren bir dizi |
||||

*Örnek*

Bu örnek bir RSS tetikleyicisinin form verileri veya form kodlu çıktıda "feedUrl" anahtar değerinden bir dizi oluşturur:

```
triggerFormDataMultiValues('feedUrl')
```

Ve bu diziyi örnek sonuç olarak döndürür: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Tetikleyicinin *form verileri* veya *form kodlu* çıktıda anahtar adı ile eşleşen tek bir değere sahip bir dize döndürür.
İşlev birden fazla eşleşme bulursa, işlev bir hata oluşturur.

```
triggerFormDataValue('<key>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*anahtar*> | Evet | Dize | Değerini istediğiniz anahtarın adı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| *anahtar-değer*> < | Dize | Belirtilen anahtardaki değer |
||||

*Örnek*

Bu örnek bir RSS tetikleyicisinin form verileri veya form kodlu çıktıda "feedUrl" anahtar değerinden bir dize oluşturur:

```
triggerFormDataValue('feedUrl')
```

Ve bu dizeyi örnek sonuç olarak döndürür: `"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Tetikleyicinin çıktısında, birden çok parçaya sahip belirli bir bölümün gövdesini döndürün.

```
triggerMultipartBody(<index>)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| *dizin*> < | Evet | Tamsayı | İstediğiniz bölümün dizin değeri |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*gövde*> | Dize | Tetikleyicinin çok parçalı çıkışında belirtilen bölümün gövdesi |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Çalışma zamanında tetikleyicinin çıkışını veya diğer JSON ad ve-değer çiftleriyle değerleri döndürün.
`trigger().outputs`için toplu değer.
Bkz. [Tetikleyici ()](#trigger).

```
triggerOutputs()
```

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*tetikleyicisi-çıkış*> | Dize | Çalışma zamanında bir tetikleyiciden alınan çıkış  |
||||

<a name="trim"></a>

### <a name="trim"></a>Kırpma

Baştaki ve sondaki boşlukları bir dizeden kaldırın ve güncelleştirilmiş dizeyi döndürün.

```
trim('<text>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*metin*> | Evet | Dize | Kaldırılacak öndeki ve sondaki boşluğu olan dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*Updatedtext*> | Dize | Baştaki veya sondaki boşluk olmadan özgün dize için güncelleştirilmiş bir sürüm |
||||

*Örnek*

Bu örnek, "Merhaba Dünya" dizesinden öndeki ve sondaki boşlukları kaldırır:

```
trim(' Hello World  ')
```

Ve şu sonucu döndürür: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>birleşim

Belirtilen koleksiyonlardaki *Tüm* öğeleri içeren bir koleksiyon döndürür.
Sonuçta görünmesi için, bu işleve geçirilen herhangi bir koleksiyonda bir öğe görünebilir. Bir veya daha fazla öğe aynı ada sahip ise, bu adı taşıyan son öğe sonuçta görüntülenir.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*Collection2*>,...  | Evet | Dizi veya nesne, ancak her ikisi birden değil | *Tüm* öğeleri istediğiniz yerdeki Koleksiyonlar |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*Updatedcollection*> | Sırasıyla dizi veya nesne | Belirtilen koleksiyonlardaki tüm öğeleri içeren bir koleksiyon-yinelenen yok |
||||

*Örnek*

Bu örnek, bu koleksiyonlardaki *Tüm* öğeleri alır:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

Ve şu sonucu döndürür: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>URIComponent

URL-güvenli olmayan karakterleri kaçış karakterleriyle değiştirerek dize için Tekdüzen Kaynak tanımlayıcısı (URI) kodlu bir sürüm döndürün.
[EncodeURIComponent ()](#encodeUriComponent)yerine bu işlevi kullanın.
Her iki işlev de aynı şekilde çalışır, ancak `uriComponent()` tercih edilir.

```
uriComponent('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | URI kodlu biçime dönüştürülecek dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*kodlanmış urı*> | Dize | Kaçış karakterleri içeren URI kodlamalı dize |
||||

*Örnek*

Bu örnek, bu dize için URI kodlu bir sürüm oluşturur:

```
uriComponent('https://contoso.com')
```

Ve şu sonucu döndürür: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>Urıonenttobinary

Tekdüzen Kaynak tanımlayıcısı (URI) bileşeni için ikili sürüm döndürün.

```
uriComponentToBinary('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Dönüştürülecek URI kodlamalı dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*binary-kodlamalı urı*> | Dize | URI kodlamalı dize için ikili sürüm. İkili içerik Base64 kodlandı ve `$content`tarafından temsil edilir. |
||||

*Örnek*

Bu örnek, URI kodlamalı bu dize için ikili sürüm oluşturur:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

Ve şu sonucu döndürür:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>Urıonenttostring

Tekdüzen Kaynak tanımlayıcısı (URI) kodlu bir dize için dize sürümünü döndürün, URI kodlu dizeyi etkin bir şekilde çözerek.

```
uriComponentToString('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Kodu çözülecek URI kodlamalı dize |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*kodu çözülmüş-urı*> | Dize | URI kodlamalı dize için kodu çözülmüş sürüm |
||||

*Örnek*

Bu örnek, bu URI kodlu dize için kodu çözülmüş dize sürümünü oluşturur:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

Ve şu sonucu döndürür: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Tekdüzen Kaynak tanımlayıcısı (URI) için `host` değeri döndürün.

```
uriHost('<uri>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*urı*> | Evet | Dize | `host` değerini istediğiniz URI |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*ana bilgisayar-değer*> | Dize | Belirtilen URI için `host` değeri |
||||

*Örnek*

Bu örnek, bu URI için `host` değerini bulur:

```
uriHost('https://www.localhost.com:8080')
```

Ve şu sonucu döndürür: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Tekdüzen Kaynak tanımlayıcısı (URI) için `path` değeri döndürün.

```
uriPath('<uri>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*urı*> | Evet | Dize | `path` değerini istediğiniz URI |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*Path-değer*> | Dize | Belirtilen URI için `path` değeri. `path` bir değere sahip değilse, "/" karakterini döndürün. |
||||

*Örnek*

Bu örnek, bu URI için `path` değerini bulur:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Ve şu sonucu döndürür: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Tekdüzen Kaynak tanımlayıcısı (URI) için `path` ve `query` değerlerini döndürün.

```
uriPathAndQuery('<uri>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*urı*> | Evet | Dize | `path` ve `query` değerlerini istediğiniz URI |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*Path-Query-value*> | Dize | Belirtilen URI için `path` ve `query` değerleri. `path` bir değer belirtmezse, "/" karakterini döndürün. |
||||

*Örnek*

Bu örnek, bu URI için `path` ve `query` değerlerini bulur:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Ve şu sonucu döndürür: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Tekdüzen Kaynak tanımlayıcısı (URI) için `port` değeri döndürün.

```
uriPort('<uri>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*urı*> | Evet | Dize | `port` değerini istediğiniz URI |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*bağlantı noktası-değer*> | Tamsayı | Belirtilen URI için `port` değeri. `port` bir değer belirtmezse, protokol için varsayılan bağlantı noktasını döndürün. |
||||

*Örnek*

Bu örnek, bu URI için `port` değerini döndürür:

```
uriPort('http://www.localhost:8080')
```

Ve şu sonucu döndürür: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Tekdüzen Kaynak tanımlayıcısı (URI) için `query` değeri döndürün.

```
uriQuery('<uri>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*urı*> | Evet | Dize | `query` değerini istediğiniz URI |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| *sorgu-değer*> < | Dize | Belirtilen URI için `query` değeri |
||||

*Örnek*

Bu örnek, bu URI için `query` değerini döndürür:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Ve şu sonucu döndürür: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Tekdüzen Kaynak tanımlayıcısı (URI) için `scheme` değeri döndürün.

```
uriScheme('<uri>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*urı*> | Evet | Dize | `scheme` değerini istediğiniz URI |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*düzeni-değer*> | Dize | Belirtilen URI için `scheme` değeri |
||||

*Örnek*

Bu örnek, bu URI için `scheme` değerini döndürür:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

Ve şu sonucu döndürür: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>UtcNow

Geçerli zaman damgasını döndürür.

```
utcNow('<format>')
```

İsteğe bağlı olarak, <*format*> parametresiyle farklı bir biçim belirtebilirsiniz.


| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*biçim*> | Hayır | Dize | Tek bir [biçim belirticisi](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*geçerli zaman damgası*> | Dize | Geçerli tarih ve saat |
||||

*Örnek 1*

Bugünün 15 Nisan 2018, 1:00:00 PM 'de olduğunu varsayalım.
Bu örnekte geçerli zaman damgası alınır:

```
utcNow()
```

Ve şu sonucu döndürür: `"2018-04-15T13:00:00.0000000Z"`

*Örnek 2*

Bugünün 15 Nisan 2018, 1:00:00 PM 'de olduğunu varsayalım.
Bu örnekte, isteğe bağlı "D" biçimini kullanarak geçerli zaman damgası alınır:

```
utcNow('D')
```

Ve şu sonucu döndürür: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>değişkenlerinin

Belirtilen değişken için değeri döndürün.

```
variables('<variableName>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Evet | Dize | Değerini istediğiniz değişkenin adı |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*değişken değer*> | Herhangi biri | Belirtilen değişkenin değeri |
||||

*Örnek*

"NumItems" değişkeni için geçerli değerin 20 olduğunu varsayalım.
Bu örnek, bu değişken için tamsayı değerini alır:

```
variables('numItems')
```

Ve şu sonucu döndürür: `20`

<a name="workflow"></a>

### <a name="workflow"></a>iş akışı

Çalışma zamanı sırasında iş akışı ile ilgili tüm ayrıntıları döndürür.

```
workflow().<property>
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*özellik*> | Hayır | Dize | Değerini istediğiniz iş akışı özelliğinin adı <p>Bir iş akışı nesnesi şu özelliklere sahiptir: **ad**, **tür**, **kimlik**, **konum**ve **Çalıştır**. **Run** özelliği değeri aynı zamanda şu özelliklere sahip bir nesnedir: **ad**, **tür**ve **kimlik**. |
|||||

*Örnek*

Bu örnek, bir iş akışının geçerli çalıştırması için ad döndürür:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

JSON nesnesi içeren bir dize için XML sürümünü döndürün.

```
xml('<value>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*değer*> | Evet | Dize | Dönüştürülecek JSON nesnesine sahip dize <p>JSON nesnesinin yalnızca bir root özelliği olmalıdır ve bu bir dizi olamaz. <br>Çift tırnak işareti (") için bir çıkış karakteri olarak ters eğik çizgi karakterini (\\) kullanın. |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*XML-sürümü*> | Nesne | Belirtilen dize veya JSON nesnesi için kodlanmış XML |
||||

*Örnek 1*

Bu örnek, bu dize için bir JSON nesnesi içeren XML sürümünü oluşturur:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

Ve bu sonuç XML 'sini döndürür:

```xml
<name>Sophia Owen</name>
```

*Örnek 2*

Bu JSON nesnesine sahip olduğunuzu varsayalım:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Bu örnek, bu JSON nesnesini içeren bir dize için XML oluşturur:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

Ve bu sonuç XML 'sini döndürür:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>XPath

XML 'yi bir XPath (XML Path Language) ifadesiyle eşleşen düğümler veya değerler için denetleyin ve eşleşen düğümleri veya değerleri döndürün. Bir XPath ifadesi veya yalnızca "XPath", XML içeriğindeki düğümleri veya işlem değerlerini seçebilmeniz için bir XML belge yapısında gezinmenize yardımcı olur.

```
xpath('<xml>', '<xpath>')
```

| Parametre | Gerekli | Tür | Açıklama |
| --------- | -------- | ---- | ----------- |
| <*xml*> | Evet | Herhangi biri | Bir XPath ifadesi değeriyle eşleşen düğümleri veya değerleri aramak için XML dizesi |
| <*xpath*> | Evet | Herhangi biri | Eşleşen XML düğümlerini veya değerlerini bulmak için kullanılan XPath ifadesi |
|||||

| Dönüş değeri | Tür | Açıklama |
| ------------ | ---- | ----------- |
| <*XML düğümü*> | 'SINI | Belirtilen XPath ifadesiyle yalnızca tek bir düğüm eşleştiğinde bir XML düğümü |
| <*değer*> | Herhangi biri | Belirtilen XPath ifadesiyle yalnızca tek bir değer eşleştiğinde bir XML düğümündeki değer |
| [<*XML-düğüm1*>, <*xml-Düğüm2*>,...] </br>-veya- </br>[<*değer1*>, <*değer2*>,...] | Dizi | XML düğümleri veya belirtilen XPath ifadesiyle eşleşen değerler içeren bir dizi |
||||

*Örnek 1*

Bu örnek, belirtilen bağımsız değişkenlerde `<name></name>` düğümle eşleşen düğümleri bulur ve bu düğüm değerlerini içeren bir dizi döndürür:

`xpath(xml(parameters('items')), '/produce/item/name')`

Bağımsız değişkenler şunlardır:

* Bu XML içeren "Items" dizesi:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  Örnek, "Items" bağımsız değişkeninden XML dizesini almak için [Parameters ()](#parameters) işlevini kullanır, ancak [XML ()](#xml) işlevini kullanarak dizeyi XML biçimine de dönüştürmelidir.

* Bu XPath ifadesi bir dize olarak geçirilir:

  `"/produce/item/name"`

`<name></name`eşleşen düğümleri içeren sonuç dizisi aşağıda verilmiştir:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Örnek 2*

Örnek 1 ' den sonra bu örnek, `<count></count>` düğümle eşleşen düğümleri bulur ve `sum()` işlevi ile bu düğüm değerlerini ekler:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Ve şu sonucu döndürür: `30`

*Örnek 3*

Bu örnekte her iki ifade de, bir ad alanı olan XML içeren belirtilen bağımsız değişkenlerde `<location></location>` düğümle eşleşen düğümleri bulur. İfadeler, çift tırnak işareti (") için bir çıkış karakteri olarak ters eğik çizgi karakterini (\\) kullanır.

* *İfade 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *İfade 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Bağımsız değişkenler şunlardır:

* XML belgesi ad alanını da içeren bu XML, `xmlns="http://contoso.com"`:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Burada XPath ifadesi:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

`<location></location>` düğümle eşleşen sonuç düğümü aşağıda verilmiştir:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Örnek 4*

Örnek 3 ' te aşağıdaki örnek, `<location></location>` düğümündeki değeri bulur:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

Ve şu sonucu döndürür: `"Paris"`

## <a name="next-steps"></a>Sonraki adımlar

[Iş akışı tanımlama dili](../logic-apps/logic-apps-workflow-definition-language.md) hakkında bilgi edinin
