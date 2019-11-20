---
title: Koşullu Bilişsel Beceri
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama koşullu yetenek filtreleme, varsayılanlar oluşturma ve bir beceri tanımında değerleri birleştirme imkanı sunar.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792039"
---
# <a name="conditional-cognitive-skill"></a>Koşullu Bilişsel Beceri

**Koşullu** Beceri, bir Boole işleminin bir çıkışa atanacak verileri belirlemesini gerektiren Azure bilişsel arama senaryolarına olanak sağlar. Bu senaryolar filtreleme, varsayılan bir değer atama ve bir koşula göre verileri birleştirme işlemlerini içerir.

Aşağıdaki sözde kod, koşullu yeteneğin ne yaptığını göstermektedir:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Bu beceri, bir Azure bilişsel hizmetler API 'siyle bağlantılı değildir ve bunu kullanmak için ücretlendirilmez. Ancak, sizi gün başına az sayıda zenginleştirme ile sınırlayan "ücretsiz" Kaynak seçeneğini geçersiz kılmak için bilişsel [Hizmetler kaynağı iliştirmelisiniz](cognitive-search-attach-cognitive-services.md) .

## <a name="odatatype"></a>@odata.type  
Microsoft. yetenekler. util. Conditionalbeceri


## <a name="evaluated-fields"></a>Değerlendirilen alanlar

Bu beceri, girişleri değerlendirilen alanlar olduğundan özeldir.

Aşağıdaki öğeler bir ifadenin geçerli değerleridir:

-   Ek açıklama yolları (deyimlerdeki yollar "$ (" ve ")" ile ayrılmış olmalıdır)
 <br/>
    Örnekler:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Değişmez değerler (dizeler, sayılar, doğru, yanlış, null) <br/>
    Örnekler:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Karşılaştırma işleçleri kullanan ifadeler (= =,! =, > =, >, < =, <) <br/>
    Örnekler:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Boole işleçleri kullanan ifadeler (& &, | |,!, ^) <br/>
    Örnekler:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Sayısal işleçler kullanan ifadeler (+,-, \*,/,%) <br/>
    Örnekler: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Koşullu beceri değerlendirmeyi desteklediğinden, bunu küçük dönüştürme senaryolarında kullanabilirsiniz. Örneğin, bkz. [beceri tanımı 4](#transformation-example).

## <a name="skill-inputs"></a>Beceri girişleri
Girişler büyük/küçük harfe duyarlıdır.

| Girdi   | Açıklama |
|-------------|-------------|
| Koşul   | Bu giriş, değerlendirilecek koşulu temsil eden [değerlendirilmiş bir alandır](#evaluated-fields) . Bu koşul, bir Boole değeri (*true* veya *false*) olarak değerlendirilmelidir.   <br/>  Örnekler: <br/> "= true" <br/> "= $ (/Document/Language) = = ' fr '" <br/> "= $ (/Document/Pages/\*/Language) = = $ (/document/expectedLanguage)" <br/> |
| whenTrue    | Bu giriş, koşul *true*olarak değerlendirilirse döndürülecek değeri temsil eden [değerlendirilmiş bir alandır](#evaluated-fields) . Sabitler dizeleri tek tırnak işaretleri (' ve ') olarak döndürülmelidir. <br/>Örnek değerler: <br/> "=" Sözleşme ' "<br/>"= $ (/document/contractType)" <br/> "= $ (/Document/Entities/\*)" <br/> |
| whenFalse   | Bu giriş, koşul *false*olarak değerlendirilirse döndürülecek değeri temsil eden [değerlendirilmiş bir alandır](#evaluated-fields) . <br/>Örnek değerler: <br/> "=" Sözleşme ' "<br/>"= $ (/document/contractType)" <br/> "= $ (/Document/Entities/\*)" <br/>

## <a name="skill-outputs"></a>Yetenek çıkışları
Yalnızca "çıktı" olarak adlandırılan tek bir çıktı vardır. Koşul false ise *whenfalse* veya koşul true Ise *whentrue* ise value değerini döndürür.

## <a name="examples"></a>Örnekler

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Örnek beceri tanımı 1: belgeleri yalnızca Fransızca belge döndürecek şekilde filtreleyin

Aşağıdaki çıktı, belgenin dili Fransızca ise bir dizi cümle ("/Document/frenchcümleler") döndürür. Dil Fransızca değilse, değer *null*olarak ayarlanır.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
"/Document/frenchcümleler" başka bir beceri *bağlamı* olarak kullanılırsa, bu beceri yalnızca "/Document/frenchcümleler" *null*olarak ayarlanmamışsa çalışır.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Örnek beceri tanımı 2: var olmayan bir değer için varsayılan değer ayarlayın

Aşağıdaki çıktı, belgenin diline ayarlanmış bir ek açıklama ("/document/languageWithDefault") oluşturur ve dil ayarlanmamışsa "es" olarak ayarlanır.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Örnek beceri tanımı 3: iki alandan bir değer birleştirme

Bu örnekte, bazı Tümcelerin *Frenchsentiment* özelliği vardır. *Frenchsentiment* özelliği null olduğunda, *englisentiment* değerini kullanmak istiyoruz. Çıktıyı, *yaklaşım* ("/Document/Sentiment/*/Sentiment") adlı bir üyeye atacağız.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Dönüştürme örneği
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Örnek beceri tanımı 4: tek bir alanda veri dönüştürme

Bu örnekte, 0 ile 1 arasında *bir yaklaşım* alırız. Bunu-1 ile 1 arasında olacak şekilde dönüştürmek istiyoruz. Bu küçük dönüşümü yapmak için koşullu yetenek kullanabiliriz.

Bu örnekte, koşulun her zaman *doğru*olduğu için yeteneğin koşullu yönünü kullanmıyoruz.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Özel Konular
Bazı parametreler değerlendirilir, bu nedenle belgelenen kalıbı izlemeniz için özellikle dikkatli olmanız gerekir. İfadeler bir eşittir işareti ile başlamalıdır. Yolun "$ (" ve ")" ile ayrılmış olması gerekir. Dizeleri tek tırnak işaretlerine yerleştirdiğinizden emin olun. Bu, değerlendirici dizelerin ve gerçek yolların ve işleçlerin ayırt edilmesini sağlar. Ayrıca, boşluk (örneğin, bir yoldaki bir "*", çarpma dışında bir şey) yerleştirdiğinizden emin olun.


## <a name="next-steps"></a>Sonraki adımlar

+ [Yerleşik yetenekler](cognitive-search-predefined-skills.md)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
