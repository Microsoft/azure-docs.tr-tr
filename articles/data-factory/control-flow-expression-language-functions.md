---
title: Azure Data Factory ifade ve işlevler
description: Bu makalede, Data Factory varlıkları oluştururken kullanabileceğiniz ifadeler ve işlevler hakkında bilgi sağlanır.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 75441a398e654893601cb7375ad3674d2b8aff29
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679909"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Azure Data Factory’deki ifadeler ve işlevler
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-functions-variables.md)
> * [Geçerli sürüm](control-flow-expression-language-functions.md)

Bu makale, Azure Data Factory tarafından desteklenen ifadeler ve işlevlerle ilgili ayrıntıları sağlar. 

## <a name="introduction"></a>Giriş
Tanımdaki JSON değerleri, çalışma zamanında değerlendirilen bir sabit değer veya ifadeler olabilir. Örneğin:  
  
```json
"name": "value"
```

 veya  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>İfadeler  
İfadeler JSON dize değerinde herhangi bir yerde görünebilir ve her zaman başka bir JSON değerine neden olabilir. JSON değeri bir ifadesiyse, ifadenin gövdesi at-Sign (\@) kaldırılarak ayıklanır. \@ile başlayan bir hazır değer dizesi gerekiyorsa, \@\@kullanılarak kaçışmalıdır. Aşağıdaki örneklerde ifadelerin nasıl değerlendirildiği gösterilmektedir.  
  
|JSON değeri|Sonuç|  
|----------------|------------|  
|parametrelere|' Parameters ' karakterleri döndürülür.|  
|"parametreler [1]"|' Parameters [1] ' karakterleri döndürülür.|  
|"\@\@"|'\@' içeren bir karakter dizesi döndürüldü.|  
|"\@"|' \@' içeren 2 karakterlik bir dize döndürüldü.|  
  
 İfadeler, ifadelerin `@{ ... }`Sarmalanan *dize ilişkilendirme* adlı bir özellik kullanılarak dizeler içinde de görünebilir. Örneğin, `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Dize ilişkilendirmeyi kullanarak, sonuç her zaman bir dizedir. `myNumber` `42` olarak tanımladım ve `foo`olarak `myString`:  
  
|JSON değeri|Sonuç|  
|----------------|------------|  
|"\@işlem hattı (). Parameters. myString"| `foo` dize olarak döndürür.|  
|"\@{Pipeline (). Parameters. myString}"| `foo` dize olarak döndürür.|  
|"\@işlem hattı (). Parameters. myNumber"| `42` *sayı*olarak döndürür.|  
|"\@{Pipeline (). Parameters. myNumber}"| `42` *dize*olarak döndürür.|  
|"Yanıt: @ {Pipeline (). Parameters. myNumber}"| `Answer is: 42`dize döndürür.|  
|"\@Concat (' yanıt: ', dize (ardışık düzen (). Parameters. myNumber))"| Dizeyi döndürür `Answer is: 42`|  
|"Yanıt: \@\@{Pipeline (). Parameters. myNumber}"| `Answer is: @{pipeline().parameters.myNumber}`dize döndürür.|  
  
### <a name="examples"></a>Örnekler

#### <a name="a-dataset-with-a-parameter"></a>Parametresi olan bir veri kümesi
Aşağıdaki örnekte, BlobDataset, **Path**adlı bir parametre alır. Değeri, `dataset().path`ifadesini kullanarak **FolderPath** özelliği için bir değer ayarlamak için kullanılır. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

#### <a name="a-pipeline-with-a-parameter"></a>Parametresi olan bir işlem hattı
Aşağıdaki örnekte, işlem hattı **inputPath** ve **OutputPath** parametrelerini alır. Parametreli blob veri kümesinin **yolu** , bu parametrelerin değerleri kullanılarak ayarlanır. Burada kullanılan söz dizimi: `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
#### <a name="tutorial"></a>Öğretici
Bu [öğreticide](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) , bir işlem hattı ve etkinlik arasında ve Etkinlikler arasında parametrelerin nasıl geçirileceğini adım adım gösterilmektedir.

  
## <a name="functions"></a>İşlevler  
 İfadeler içindeki işlevleri çağırabilirsiniz. Aşağıdaki bölümler, bir ifadede kullanılabilecek işlevlerle ilgili bilgiler sağlar.  

## <a name="string-functions"></a>Dize işlevleri  
 Aşağıdaki işlevler yalnızca dizeler için geçerlidir. Ayrıca, dizeler üzerinde bir dizi koleksiyon işlevini de kullanabilirsiniz.  
  
|İşlev adı|Açıklama|  
|-------------------|-----------------|  
|Concat|Herhangi bir sayıda dizeyi birlikte birleştirir. Örneğin, parametre1 ise `foo,` aşağıdaki ifade `somevalue-foo-somevalue`döndürür: `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Parametre numarası**: 1... *n*<br /><br /> **Ad**: dize *n*<br /><br /> **Açıklama**: gereklidir. Tek bir dize içinde Birleştirilecek dizeler.|  
|dizeden|Dizeden bir karakter alt kümesini döndürür. Örneğin, aşağıdaki ifade:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> döndürdüğü<br /><br /> `foo`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Alt dizenin alındığı dize.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: başlangıç dizini<br /><br /> **Açıklama**: gereklidir. Alt dizenin 1. parametrede başladığı dizin. Başlangıç dizini sıfır tabanlıdır. <br /><br /> **Parametre numarası**: 3<br /><br /> **Ad**: Uzunluk<br /><br /> **Açıklama**: gereklidir. Alt dizenin uzunluğu.|  
|değiştirin|Bir dizeyi verilen dizeyle değiştirir. Örneğin, ifadesi:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> döndürdüğü<br /><br /> `the new string`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir.  Parametre 1 ' de parametre 2 ' de bulunursa, 2 parametresi için aranan ve parametre 3 ile güncellenen dize.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: eski dize<br /><br /> **Açıklama**: gereklidir. Parametre 1 ' de eşleşme bulunduğunda parametre 3 ile değiştirilecek dize<br /><br /> **Parametre numarası**: 3<br /><br /> **Ad**: yeni dize<br /><br /> **Açıklama**: gereklidir. Parametre 1 ' de eşleşme bulunduğunda parametre 2 ' deki dizeyi değiştirmek için kullanılan dize.|  
|'ini| Genel olarak benzersiz bir dize (aka) oluşturur. GUID). Örneğin, aşağıdaki çıktı `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`oluşturulabilir:<br /><br /> `guid()`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: biçim<br /><br /> **Açıklama**: isteğe bağlı. [Bu GUID 'in değerinin nasıl biçimlendirileceğini](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx)belirten tek bir Biçim belirleyicisi. Biçim parametresi "N", "D", "B", "P" veya "X" olabilir. Biçim sağlanmazsa, "D" kullanılır.|  
|toLower|Dizeyi küçük harfe dönüştürür. Örneğin, aşağıdaki `two by two is four`döndürür: `toLower('Two by Two is Four')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Küçük harfe dönüştürülecek dize. Dizedeki bir karakterin küçük harfli bir eşdeğeri yoksa, döndürülen dizede değişmeden bulunur.|  
|toUpper|Dizeyi büyük harfe dönüştürür. Örneğin, aşağıdaki ifade `TWO BY TWO IS FOUR`döndürür: `toUpper('Two by Two is Four')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Büyük harfe dönüştürülecek dize. Dizedeki bir karakterin büyük harfli bir eşdeğeri yoksa, döndürülen dizede değişmeden bulunur.|  
|IndexOf|Bir dize insensitively dizesi içindeki bir değerin dizinini bulur. Dizin sıfır tabanlıdır. Örneğin, aşağıdaki ifade `7`döndürür: `indexof('hello, world.', 'world')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Değeri içerebilen dize.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Dizininde arama yapılacak değer.|  
|LastIndexOf|Bir dize insensitively dize içinde bir değerin son dizinini bulur. Dizin sıfır tabanlıdır. Örneğin, aşağıdaki ifade `3`döndürür: `lastindexof('foofoo', 'foo')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Değeri içerebilen dize.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Dizininde arama yapılacak değer.|  
|StartsWith|Dizenin insensitively bir değer durumuyla başlıyorsa denetler. Örneğin, aşağıdaki ifade `true`döndürür: `startswith('hello, world', 'hello')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Değeri içerebilen dize.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Dize ile başlayabiliriz değeri.|  
|EndsWith|Dizenin insensitively bir değer durumuyla bitmediğini denetler. Örneğin, aşağıdaki ifade `true`döndürür: `endswith('hello, world', 'world')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Değeri içerebilen dize.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Dizenin ile biteme değeri.|  
|split|Dizeyi ayırıcı kullanarak böler. Örneğin, aşağıdaki ifade `["a", "b", "c"]`döndürür: `split('a;b;c',';')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Bölünen dize.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Ayırıcı.|  
  
  
## <a name="collection-functions"></a>Koleksiyon işlevleri  
 Bu işlevler diziler, dizeler ve bazen sözlükler gibi koleksiyonlar üzerinde çalışır.  
  
|İşlev adı|Açıklama|  
|-------------------|-----------------|  
|vardır|Sözlük bir anahtar içeriyorsa, liste değeri içeriyorsa veya dize alt dize içeriyorsa true döndürür. Örneğin, aşağıdaki ifade `true:``contains('abacaba','aca')` döndürür<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: koleksiyon içinde<br /><br /> **Açıklama**: gereklidir. İçinde arama yapılacak koleksiyon.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: nesne bul<br /><br /> **Açıklama**: gereklidir. **İçindeki koleksiyon**içinde bulunacak nesne.|  
|Uzunluklu|Bir dizideki veya dizedeki öğelerin sayısını döndürür. Örneğin, aşağıdaki ifade `3`döndürür: `length('abc')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: koleksiyon<br /><br /> **Açıklama**: gereklidir. Uzunluğunu almak için koleksiyon.|  
|Olmamalıdır|Nesne, dizi veya dize boşsa true döndürür. Örneğin, aşağıdaki ifade `true`döndürür:<br /><br /> `empty('')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: koleksiyon<br /><br /> **Açıklama**: gereklidir. Boş olup olmadığını denetlemek için koleksiyon.|  
|İmin|Kendisine geçirilen diziler veya nesneler arasında ortak öğelerle tek bir dizi veya nesne döndürür. Örneğin, bu işlev `[1, 2]`döndürür:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> İşlevin parametreleri bir dizi nesne veya dizi kümesi olabilir (bir karışımı değil). Aynı ada sahip iki nesne varsa, bu adı taşıyan son nesne son nesnede görüntülenir.<br /><br /> **Parametre numarası**: 1... *n*<br /><br /> **Ad**: koleksiyon *n*<br /><br /> **Açıklama**: gereklidir. Değerlendirilecek Koleksiyonlar. Bir nesne, sonuçta görünmesi için geçirilen tüm koleksiyonlar içinde olmalıdır.|  
|birleşim|Bir dizi ya da nesne içindeki tüm öğeleri içeren tek bir dizi veya nesne döndürür. Örneğin, bu işlev `[1, 2, 3, 10, 101]:` döndürür<br /><br /> : `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> İşlevin parametreleri bir dizi nesne veya dizi kümesi olabilir (bir karışımı değil). Son çıktıda aynı ada sahip iki nesne varsa, bu adı taşıyan son nesne son nesnede görüntülenir.<br /><br /> **Parametre numarası**: 1... *n*<br /><br /> **Ad**: koleksiyon *n*<br /><br /> **Açıklama**: gereklidir. Değerlendirilecek Koleksiyonlar. Koleksiyonlardan herhangi birinde görüntülenen bir nesne, sonuçta görünür.|  
|Adı|Geçirilen dizideki veya dizedeki ilk öğeyi döndürür. Örneğin, bu işlev `0`döndürür:<br /><br /> `first([0,2,3])`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: koleksiyon<br /><br /> **Açıklama**: gereklidir. İlk nesneyi almak için koleksiyon.|  
|soyadına|Geçirilen dizideki veya dizedeki son öğeyi döndürür. Örneğin, bu işlev `3`döndürür:<br /><br /> `last('0123')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: koleksiyon<br /><br /> **Açıklama**: gereklidir. Son nesneyi almak için koleksiyon.|  
|Almanız|Geçirilen dizi veya dizeden ilk **sayı** öğelerini döndürür, örneğin bu işlev `[1, 2]`döndürür: `take([1, 2, 3, 4], 2)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: koleksiyon<br /><br /> **Açıklama**: gereklidir. İlk **sayı** nesnesini almak için koleksiyon.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: sayı<br /><br /> **Açıklama**: gereklidir. **Koleksiyondan**gerçekleştirilecek nesne sayısı. Pozitif bir tamsayı olmalıdır.|  
|Şimdilik|Dizin **sayısıyla**başlayan dizideki öğeleri döndürür, örneğin bu işlev `[3, 4]`döndürür:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: koleksiyon<br /><br /> **Açıklama**: gereklidir. İlk **Count** nesnesini atlanacak koleksiyon.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: sayı<br /><br /> **Açıklama**: gereklidir. **Koleksiyonun**önünden kaldırılacak nesne sayısı. Pozitif bir tamsayı olmalıdır.|  
  
## <a name="logical-functions"></a>Mantıksal işlevler  
 Bu işlevler, koşullar içinde yararlı olduğundan, her türlü mantığı değerlendirmek için kullanılabilirler.  
  
|İşlev adı|Açıklama|  
|-------------------|-----------------|  
|eşittir|İki değer eşitse true değerini döndürür. Örneğin, parametre1 foo ise, aşağıdaki ifade `true`döndürür: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: nesne 1<br /><br /> **Açıklama**: gereklidir. **Nesne 2**ile Karşılaştırılacak nesne.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: nesne 2<br /><br /> **Açıklama**: gereklidir. **Nesne 1**ile Karşılaştırılacak nesne.|  
|büyüktür|İlk bağımsız değişken ikinciden küçükse true döndürür. Değer yalnızca Integer, float veya String türünde olabilir. Örneğin, aşağıdaki ifade `true`döndürür: `less(10,100)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: nesne 1<br /><br /> **Açıklama**: gereklidir. **Nesne 2**' den daha az olup olmadığını Denetlenecek nesne.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: nesne 2<br /><br /> **Açıklama**: gereklidir. **Nesne 1**' den büyük olup olmadığını Denetlenecek nesne.|  
|Lessotalals|İlk bağımsız değişken ikinciden küçükse true döndürür. Değer yalnızca Integer, float veya String türünde olabilir. Örneğin, aşağıdaki ifade `true`döndürür: `lessOrEquals(10,10)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: nesne 1<br /><br /> **Açıklama**: gereklidir. **Nesne 2**' ye eşit veya ondan daha küçükse Denetlenecek nesne.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: nesne 2<br /><br /> **Açıklama**: gereklidir. **Nesne 1**' den büyük veya ona eşit olup olmadığını Denetlenecek nesne.|  
|ilerisi|İlk bağımsız değişken ikinciden büyükse true değerini döndürür. Değer yalnızca Integer, float veya String türünde olabilir. Örneğin, aşağıdaki ifade `false`döndürür: `greater(10,10)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: nesne 1<br /><br /> **Açıklama**: gereklidir. **Nesne 2**' den büyükse Denetlenecek nesne.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: nesne 2<br /><br /> **Açıklama**: gereklidir. **Nesne 1**' den daha az olup olmadığını kontrol etmek için nesne.|  
|greaterOrEquals|İlk bağımsız değişken ikinciden büyükse ya da eşitse true değerini döndürür. Değer yalnızca Integer, float veya String türünde olabilir. Örneğin, aşağıdaki ifade `false`döndürür: `greaterOrEquals(10,100)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: nesne 1<br /><br /> **Açıklama**: gereklidir. **Nesne 2**' den büyük veya ona eşit olup olmadığını Denetlenecek nesne.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: nesne 2<br /><br /> **Açıklama**: gereklidir. **Nesne 1**' den küçük veya ona eşit olup olmadığını Denetlenecek nesne.|  
|ve|Parametrelerin her ikisi de true olduğunda true döndürür. Her iki bağımsız değişkenin de Boole olması gerekir. Aşağıdaki `false`döndürür: `and(greater(1,10),equals(0,0))`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: Boolean 1<br /><br /> **Açıklama**: gereklidir. `true`olması gereken ilk bağımsız değişken.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: Boolean 2<br /><br /> **Açıklama**: gereklidir. İkinci bağımsız değişken `true`olmalıdır.|  
|or|Parametrelerden biri true olduğunda true döndürür. Her iki bağımsız değişkenin de Boole olması gerekir. Aşağıdaki `true`döndürür: `or(greater(1,10),equals(0,0))`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: Boolean 1<br /><br /> **Açıklama**: gereklidir. `true`olabilecek ilk bağımsız değişken.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: Boolean 2<br /><br /> **Açıklama**: gereklidir. İkinci bağımsız değişken `true`olabilir.|  
|Başlatılmadı|Parametre `false`true değerini döndürür. Her iki bağımsız değişkenin de Boole olması gerekir. Aşağıdaki `true`döndürür: `not(contains('200 Success','Fail'))`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: Boolean<br /><br /> **Açıklama**: parametre `false`, true döndürür. Her iki bağımsız değişkenin de Boole olması gerekir. Aşağıdaki `true`döndürür: `not(contains('200 Success','Fail'))`|  
|kullandıysanız|Belirtilen ifadenin `true` veya `false`sonuçları ile sonuçlanmasına göre belirtilen bir değeri döndürür.  Örneğin, aşağıdaki `"yes"`döndürür: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: ifade<br /><br /> **Açıklama**: gereklidir. İfadenin hangi değerin döndürüleceğini belirleyen bir Boolean değer.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: true<br /><br /> **Açıklama**: gereklidir. İfade `true`döndürülecek değer.<br /><br /> **Parametre numarası**: 3<br /><br /> **Ad**: false<br /><br /> **Açıklama**: gereklidir. İfade `false`döndürülecek değer.|  
  
## <a name="conversion-functions"></a>Dönüştürme işlevleri  
 Bu işlevler, dildeki yerel türlerin her biri arasında dönüştürme yapmak için kullanılır:  
  
-   string  
  
-   integer  
  
-   float  
  
-   boole  
  
-   Dizi  
  
-   sözlüğü  
  
|İşlev adı|Açıklama|  
|-------------------|-----------------|  
|int|Parametreyi bir tamsayıya dönüştürün. Örneğin, aşağıdaki ifade bir dize yerine 100 olarak bir sayı döndürür: `int('100')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: değer<br /><br /> **Açıklama**: gereklidir. Tamsayıya dönüştürülen değer.|  
|string|Parametreyi bir dizeye Dönüştür. Örneğin, aşağıdaki ifade `'10'`döndürür: `string(10)` bir nesneyi dizeye dönüştürebilirsiniz. Örneğin, **foo** parametresi bir özellik `bar : baz`olan bir nesnesiyse, aşağıdakiler `{"bar" : "baz"}` döndürebilir `string(pipeline().parameters.foo)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: değer<br /><br /> **Açıklama**: gereklidir. Bir dizeye dönüştürülen değer.|  
|nesnesinde|Parametreyi bir JSON türü değerine dönüştürür. Dizenin tersidir (). Örneğin, aşağıdaki ifade, bir dize yerine `[1,2,3]` bir dizi olarak döndürür:<br /><br /> `json('[1,2,3]')`<br /><br /> Benzer şekilde, bir dizeyi nesnesine dönüştürebilirsiniz. Örneğin, `json('{"bar" : "baz"}')` şunu döndürür:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Yerel bir tür değerine dönüştürülen dize.<br /><br /> JSON işlevi xml girişini de destekler. Örneğin, öğesinin parametre değeri:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> Şu JSON 'a dönüştürüldü:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Parametre bağımsız değişkenini bir kayan noktalı sayıya dönüştürün. Örneğin, aşağıdaki ifade `10.333`döndürür: `float('10.333')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: değer<br /><br /> **Açıklama**: gereklidir. Kayan noktalı sayıya dönüştürülen değer.|  
|bool|Parametreyi Boole değerine dönüştürür. Örneğin, aşağıdaki ifade `false`döndürür: `bool(0)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: değer<br /><br /> **Açıklama**: gereklidir. Boole değerine dönüştürülen değer.|  
|Coalesce|Geçirilen bağımsız değişkenlerde ilk null olmayan nesneyi döndürür. Not: boş bir dize null değil. Örneğin, 1 ve 2. parametreler tanımlanmamışsa, bu `fallback`döndürür: `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Parametre numarası**: 1... *n*<br /><br /> **Ad**: nesne*n*<br /><br /> **Açıklama**: gereklidir. `null`denetlenecek nesneler.|  
|biçiminde|Giriş dizesinin Base64 gösterimini döndürür. Örneğin, aşağıdaki ifade `c29tZSBzdHJpbmc=`döndürür: `base64('some string')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize 1<br /><br /> **Açıklama**: gereklidir. Base64 gösterimine Kodlanacak dize.|  
|base64ToBinary|Base64 ile kodlanmış bir dizenin ikili gösterimini döndürür. Örneğin, aşağıdaki ifade bazı bir dizenin ikili gösterimini döndürür: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Base64 kodlamalı dize.|  
|base64ToString|Based64 kodlamalı bir dizenin dize gösterimini döndürür. Örneğin, aşağıdaki ifade bir dize döndürür: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Base64 kodlamalı dize.|  
|ý|Bir değerin ikili gösterimini döndürür.  Örneğin, aşağıdaki ifade bir dizenin ikili gösterimini döndürür: `binary(‘some string’).`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: değer<br /><br /> **Açıklama**: gereklidir. İkiliye dönüştürülmüş değer.|  
|dataUriToBinary|Bir veri URI 'sinin ikili gösterimini döndürür. Örneğin, aşağıdaki ifade bir dizenin ikili gösterimini döndürür: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. İkili gösterimine dönüştürülecek veri URI 'SI.|  
|dataUriToString|Bir veri URI 'sinin dize gösterimini döndürür. Örneğin, aşağıdaki ifade bir dize döndürür: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br />**Açıklama**: gereklidir. Dize gösterimine dönüştürülecek veri URI 'SI.|  
|dataUri|Bir değerin veri URI 'sini döndürür. Örneğin, aşağıdaki ifade veri döndürüyor: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Parametre numarası**: 1<br /><br />**Ad**: değer<br /><br />**Açıklama**: gereklidir. Veri URI 'sine dönüştürülecek değer.|  
|decodeBase64|Giriş based64 dizesinin dize gösterimini döndürür. Örneğin, aşağıdaki ifade `some string`döndürür: `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: bir input based64 dizesinin dize gösterimini döndürür.|  
|encodeURIComponent|URL-geçirilen dizeyi çıkar. Örneğin, aşağıdaki ifade `You+Are%3ACool%2FAwesome`döndürür: `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. URL 'den çıkmak için dize-güvenli olmayan karakterler.|  
|decodeURIComponent|URL kaldır-geçilen dizeyi çıkar. Örneğin, aşağıdaki ifade `You Are:Cool/Awesome`döndürür: `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. URL kodunun çözülmesi için dize-güvenli olmayan karakterler.|  
|decodeDataUri|Giriş verisi URI dizesinin ikili gösterimini döndürür. Örneğin, aşağıdaki ifade `some string`ikili gösterimini döndürür: `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br /> **Açıklama**: gereklidir. Bir ikili gösterimde çözülecek dataURI.|  
|URIComponent|Bir değerin URI kodlamalı gösterimini döndürür. Örneğin, aşağıdaki ifade `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')` döndürür<br /><br /> Parametre ayrıntıları: sayı: 1, ad: dize, Açıklama: gereklidir. URI kodlamalı olan dize.|  
|Urıonenttobinary|URI kodlamalı bir dizenin ikili gösterimini döndürür. Örneğin, aşağıdaki ifade `You Are:Cool/Awesome`ikili gösterimini döndürür: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: dize<br /><br />**Açıklama**: gereklidir. URI kodlamalı dize.|  
|Urıonenttostring|URI kodlamalı bir dizenin dize gösterimini döndürür. Örneğin, aşağıdaki ifade `You Are:Cool/Awesome`döndürür: `uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Parametre numarası**: 1<br /><br />**Ad**: dize<br /><br />**Açıklama**: gereklidir. URI kodlamalı dize.|  
|xml|Değerin bir XML gösterimini döndürür. Örneğin, aşağıdaki ifade `'\<name>Alan\</name>'`tarafından temsil edilen bir XML içeriği döndürüyor: `xml('\<name>Alan\</name>')`. XML işlevi JSON nesne girişini de destekler. Örneğin, `{ "abc": "xyz" }` parametresi bir XML içeriğine dönüştürülür `\<abc>xyz\</abc>`<br /><br /> **Parametre numarası**: 1<br /><br />**Ad**: değer<br /><br />**Açıklama**: gereklidir. XML 'e dönüştürülecek değer.|  
|XPath|XPath ifadesinin değerlendirdiği bir değerin XPath ifadesiyle eşleşen bir XML düğümleri dizisi döndürür.<br /><br />  **Örnek 1**<br /><br /> ' P1 ' parametresinin değerinin aşağıdaki XML 'in bir dize temsili olduğunu varsayın:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Bu kod: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> döndürür<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> leri<br /><br /> 2. Bu kod: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> döndürür<br /><br /> `13`<br /><br /> <br /><br /> **Örnek 2**<br /><br /> Aşağıdaki XML içeriği veriliyor:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1. Bu kod: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> or<br /><br /> 2. Bu kod: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> Döndürdüğü<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> ve<br /><br /> 3. Bu kod: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> Döndürdüğü<br /><br /> ``bar``<br /><br /> **Parametre numarası**: 1<br /><br />**Ad**: XML<br /><br />**Açıklama**: gereklidir. XPath ifadesinin değerlendirileceği XML.<br /><br /> **Parametre numarası**: 2<br /><br />**Ad**: XPath<br /><br />**Açıklama**: gereklidir. Değerlendirilecek XPath ifadesi.|  
|array|Parametreyi bir diziye dönüştürür.  Örneğin, aşağıdaki ifade `["abc"]`döndürür: `array('abc')`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: değer<br /><br /> **Açıklama**: gereklidir. Bir diziye dönüştürülen değer.|
|createArray|Parametrelerden bir dizi oluşturur.  Örneğin, aşağıdaki ifade `["a", "c"]`döndürür: `createArray('a', 'c')`<br /><br /> **Parametre numarası**: 1... No<br /><br /> **Ad**: herhangi bir n<br /><br /> **Açıklama**: gereklidir. Bir dizide birleştirilecek değerler.|

## <a name="math-functions"></a>Matematik işlevleri  
 Bu işlevler, iki tür numara için kullanılabilir: **tamsayılar** ve **float**.  
  
|İşlev adı|Açıklama|  
|-------------------|-----------------|  
|add|İki sayının eklemenin sonucunu döndürür. Örneğin, bu işlev `20.333`döndürür: `add(10,10.333)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: summve 1<br /><br /> **Açıklama**: gereklidir. **Summand 2 '** ye eklenecek sayı.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: summve 2<br /><br /> **Açıklama**: gereklidir. **Summand 1 '** e eklenecek sayı.|  
|alt|İki sayının çıkarma sonucunu döndürür. Örneğin, bu işlev şunu döndürür: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: minuend<br /><br /> **Açıklama**: gereklidir. **Çıkarılan** kaldırılan sayı.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: çıkarılan<br /><br /> **Açıklama**: gereklidir. **Minuend**'ten kaldırılacak sayı.|  
|MUL|İki sayının çarpma sonucu sonucunu döndürür. Örneğin, aşağıdaki `103.33`döndürür:<br /><br /> `mul(10,10.333)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: çoğullıve 1<br /><br /> **Açıklama**: gereklidir. **Çoğullıve 2 '** nin çarpılacağı sayı.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: çoğullıve 2<br /><br /> **Açıklama**: gereklidir. **Çoğullıve 1 ' i** çarpılacak sayı.|  
|DIV|İki sayının bölümünün sonucunu döndürür. Örneğin, aşağıdaki `1.0333`döndürür:<br /><br /> `div(10.333,10)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: bölünen<br /><br /> **Açıklama**: gereklidir. **Bölen**tarafından bölünecek sayı.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: bölen<br /><br /> **Açıklama**: gereklidir. **Bölünen** bölüneceği sayı.|  
|alma|İki sayının (modül) bölümünün arkasına kalanı döndürür. Örneğin, aşağıdaki ifade `2`döndürür:<br /><br /> `mod(10,4)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: bölünen<br /><br /> **Açıklama**: gereklidir. **Bölen**tarafından bölünecek sayı.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: bölen<br /><br /> **Açıklama**: gereklidir. **Bölünen** bölüneceği sayı. Bölme işleminden sonra geri kalanı alınır.|  
|dk|Bu işlevi çağırmak için iki farklı desen vardır: burada `min([0,1,2])` en az bir dizi alır. Bu ifade `0`döndürür. Alternatif olarak, bu işlev virgülle ayrılmış bir değerler listesi alabilir: `min(0,1,2)` bu işlev Ayrıca 0 değerini döndürür. Tüm değerlerin sayı olması gerekir, bu nedenle parametre yalnızca içinde numaralara sahip olması gereken bir dizidir.<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: koleksiyon veya değer<br /><br /> **Açıklama**: gereklidir. En küçük değeri bulmak için bir değer dizisi ya da bir küme ilk değeri olabilir.<br /><br /> **Parametre numarası**: 2... *n*<br /><br /> **Ad**: değer *n*<br /><br /> **Açıklama**: isteğe bağlı. İlk parametre bir değer ise, ek değerler geçirebilirsiniz ve geçirilen tüm değerlerin en az biri döndürülür.|  
|Biçimlendir|Bu işlevi çağırmak için iki farklı desen vardır: `max([0,1,2])`<br /><br /> Burada en fazla bir dizi alır. Bu ifade `2`döndürür. Alternatif olarak, bu işlev virgülle ayrılmış bir değerler listesi alabilir: `max(0,1,2)` bu işlev 2 değerini döndürür. Tüm değerlerin sayı olması gerekir, bu nedenle parametre yalnızca içinde numaralara sahip olması gereken bir dizidir.<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: koleksiyon veya değer<br /><br /> **Açıklama**: gereklidir. En büyük değeri bulmak için bir değer dizisi ya da bir küme ilk değeri olabilir.<br /><br /> **Parametre numarası**: 2... *n*<br /><br /> **Ad**: değer *n*<br /><br /> **Açıklama**: isteğe bağlı. İlk parametre bir değer ise, ek değerler geçirebilirsiniz ve geçirilen tüm değerlerin en fazla değeri döndürülür.|  
|aralığı| Belirli bir sayıdan başlayan tamsayılar dizisi oluşturur ve döndürülen dizinin uzunluğunu tanımlarsınız. Örneğin, bu işlev `[3,4,5,6]`döndürür:<br /><br /> `range(3,4)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: başlangıç dizini<br /><br /> **Açıklama**: gereklidir. Dizideki ilk tamsayıdır.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: sayı<br /><br /> **Açıklama**: gereklidir. Dizide bulunan tamsayılar sayısı.|  
|ran| Belirtilen Aralık içinde (her iki uçta da) rastgele bir tamsayı üretir. Örneğin, bu `42`döndürebilir:<br /><br /> `rand(-1000,1000)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: en az<br /><br /> **Açıklama**: gereklidir. Döndürülebilecek en küçük tamsayı.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: en fazla<br /><br /> **Açıklama**: gereklidir. Döndürülebilecek en yüksek tamsayı.|  
  
## <a name="date-functions"></a>Tarih işlevleri  
  
|İşlev adı|Açıklama|  
|-------------------|-----------------|  
|UtcNow|Geçerli zaman damgasını bir dize olarak döndürür. Örneğin `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: biçim<br /><br /> **Açıklama**: isteğe bağlı. Tek bir [biçim belirticisi karakteri](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) veya bu zaman damgasının değerinin nasıl biçimlendirileceğini gösteren bir [özel biçim deseninin](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) olması. Biçim sağlanmazsa ISO 8601 biçimi ("o") kullanılır.|  
|AddSeconds|Geçirilen dize zaman damgasına tamsayı cinsinden saniye sayısı ekler. Saniye sayısı pozitif veya negatif olabilir. Bir biçim belirticisi sağlanmamışsa, sonuç ISO 8601 biçiminde ("o") varsayılan olarak bir dizedir. Örneğin `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: Timestamp<br /><br /> **Açıklama**: gereklidir. Saati içeren bir dize.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: saniye<br /><br /> **Açıklama**: gereklidir. Eklenecek saniye sayısı. Saniye çıkarmak için negatif olabilir.<br /><br /> **Parametre numarası**: 3<br /><br /> **Ad**: biçim<br /><br /> **Açıklama**: isteğe bağlı. Tek bir [biçim belirticisi karakteri](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) veya bu zaman damgasının değerinin nasıl biçimlendirileceğini gösteren bir [özel biçim deseninin](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) olması. Biçim sağlanmazsa ISO 8601 biçimi ("o") kullanılır.|  
|AddMinutes|Geçirilen bir dize zaman damgasına tamsayı cinsinden bir sayı ekler. Dakika sayısı pozitif veya negatif olabilir. Bir biçim belirticisi sağlanmamışsa, sonuç ISO 8601 biçiminde ("o") varsayılan olarak bir dizedir. Örneğin, `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: Timestamp<br /><br /> **Açıklama**: gereklidir. Saati içeren bir dize.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: dakika<br /><br /> **Açıklama**: gereklidir. Eklenecek dakika sayısı. Dakika çıkarmak için negatif olabilir.<br /><br /> **Parametre numarası**: 3<br /><br /> **Ad**: biçim<br /><br /> **Açıklama**: isteğe bağlı. Tek bir [biçim belirticisi karakteri](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) veya bu zaman damgasının değerinin nasıl biçimlendirileceğini gösteren bir [özel biçim deseninin](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) olması. Biçim sağlanmazsa ISO 8601 biçimi ("o") kullanılır.|  
|AddHours|Geçirilen bir dize zaman damgasına tamsayı cinsinden bir saat ekler. Saat sayısı pozitif veya negatif olabilir. Bir biçim belirticisi sağlanmamışsa, sonuç ISO 8601 biçiminde ("o") varsayılan olarak bir dizedir. Örneğin `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: Timestamp<br /><br /> **Açıklama**: gereklidir. Saati içeren bir dize.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: Saat<br /><br /> **Açıklama**: gereklidir. Eklenecek saat sayısı. Saatleri çıkarmak için negatif olabilir.<br /><br /> **Parametre numarası**: 3<br /><br /> **Ad**: biçim<br /><br /> **Açıklama**: isteğe bağlı. Tek bir [biçim belirticisi karakteri](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) veya bu zaman damgasının değerinin nasıl biçimlendirileceğini gösteren bir [özel biçim deseninin](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) olması. Biçim sağlanmazsa ISO 8601 biçimi ("o") kullanılır.|  
|AddDays|Geçirilen bir dize zaman damgasına bir tamsayı gün sayısı ekler. Gün sayısı pozitif veya negatif olabilir. Bir biçim belirticisi sağlanmamışsa, sonuç ISO 8601 biçiminde ("o") varsayılan olarak bir dizedir. Örneğin `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Parametre numarası**: 1<br /><br /> **Ad**: Timestamp<br /><br /> **Açıklama**: gereklidir. Saati içeren bir dize.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: gün<br /><br /> **Açıklama**: gereklidir. Eklenecek gün sayısı. Gün çıkarmak için negatif olabilir.<br /><br /> **Parametre numarası**: 3<br /><br /> **Ad**: biçim<br /><br /> **Açıklama**: isteğe bağlı. Tek bir [biçim belirticisi karakteri](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) veya bu zaman damgasının değerinin nasıl biçimlendirileceğini gösteren bir [özel biçim deseninin](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) olması. Biçim sağlanmazsa ISO 8601 biçimi ("o") kullanılır.|  
|formatDateTime|Tarih biçiminde bir dize döndürür. Bir biçim belirticisi sağlanmamışsa, sonuç ISO 8601 biçiminde ("o") varsayılan olarak bir dizedir. Örneğin `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br />' Yyyy/aa/gg ' içindeki bir tarihi biçimlendirmek için, formatDateTime (UtcNow (), ' yyyy/MM/dd ') kullanın.</br>Tarihe bir ad eklemek için @concat(' foo-', '/', formatDateTime (UtcNow (), ' yyyy/aa/gg ') kullanın.<br><br> **Parametre numarası**: 1<br /><br /> **Ad**: Tarih<br /><br /> **Açıklama**: gereklidir. Tarihi içeren bir dize.<br /><br /> **Parametre numarası**: 2<br /><br /> **Ad**: biçim<br /><br /> **Açıklama**: isteğe bağlı. Tek bir [biçim belirticisi karakteri](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) veya bu zaman damgasının değerinin nasıl biçimlendirileceğini gösteren bir [özel biçim deseninin](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) olması. Biçim sağlanmazsa ISO 8601 biçimi ("o") kullanılır. |  

## <a name="next-steps"></a>Sonraki adımlar
İfadelerde kullanabileceğiniz sistem değişkenlerinin listesi için bkz. [Sistem değişkenleri](control-flow-system-variables.md).
