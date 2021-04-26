---
title: Azure Data Factory ifade ve işlevler
description: Bu makalede, Data Factory varlıkları oluştururken kullanabileceğiniz ifadeler ve işlevler hakkında bilgi sağlanır.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: af365ef9b94702fa6634235a95a91297d6b7ae50
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107139"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Azure Data Factory’deki ifadeler ve işlevler

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-functions-variables.md)
> * [Güncel sürüm](control-flow-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makale, Azure Data Factory tarafından desteklenen ifadeler ve işlevlerle ilgili ayrıntıları sağlar. 

## <a name="expressions"></a>İfadeler

Tanımdaki JSON değerleri, çalışma zamanında değerlendirilen bir sabit değer veya ifadeler olabilir. Örnek:  
  
```json
"name": "value"
```

 veya  
  
```json
"name": "@pipeline().parameters.password"
```

İfadeler JSON dize değerinde herhangi bir yerde görünebilir ve her zaman başka bir JSON değerine neden olabilir. JSON değeri bir ifadesiyse, ifadenin gövdesi at-Sign () kaldırılarak ayıklanır \@ . İle başlayan bir sabit değer dizesi gerekliyse \@ , kullanılarak kaçışlı olması gerekir \@ \@ . Aşağıdaki örneklerde ifadelerin nasıl değerlendirildiği gösterilmektedir.  
  
|JSON değeri|Sonuç|  
|----------------|------------|  
|parametrelere|' Parameters ' karakterleri döndürülür.|  
|"parametreler [1]"|' Parameters [1] ' karakterleri döndürülür.|  
|"\@\@"|' ' İçeren 1 karakterlik bir dize \@ döndürüldü.|  
|" \@"|' ' İçeren 2 karakterlik bir dize \@ döndürüldü.|  
  
 İfadeler, ifadelerin Sarmalanan *dize ilişkilendirme* adlı bir özellik kullanılarak dizeler içinde de görünebilir `@{ ... }` . Örnek: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Dize ilişkilendirmeyi kullanarak, sonuç her zaman bir dizedir. Şu şekilde tanımladım `myNumber` `42`  `myString`  `foo` :  
  
|JSON değeri|Sonuç|  
|----------------|------------|  
|" \@ Pipeline (). Parameters. myString"| `foo`Bir dize olarak döndürür.|  
|" \@ {Pipeline (). Parameters. myString}"| `foo`Bir dize olarak döndürür.|  
|"işlem \@ hattı (). Parameters. myNumber"| `42` *Sayı* olarak döndürür.|  
|" \@ {Pipeline (). Parameters. myNumber}"| `42`Bir *dize* olarak döndürür.|  
|"Yanıt: @ {Pipeline (). Parameters. myNumber}"| Dizeyi döndürür `Answer is: 42` .|  
|" \@ Concat (' yanıt: ', dize (ardışık düzen (). Parameters. myNumber))"| Dizeyi döndürür `Answer is: 42`|  
|"Yanıt: \@ \@ {Pipeline (). Parameters. MyNumber}"| Dizeyi döndürür `Answer is: @{pipeline().parameters.myNumber}` .|  

## <a name="examples"></a>Örnekler

### <a name="complex-expression-example"></a>Karmaşık ifade örneği
Aşağıdaki örnek, etkinlik çıktısının derin alt alanına başvuran karmaşık bir örnek gösterir. Bir alt alan olarak değerlendirilen bir işlem hattı parametresine başvurmak için, nokta (.) işleci yerine [] sözdizimini kullanın (subfield1 ve subfield2 durumunda olduğu gibi)

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

### <a name="dynamic-content-editor"></a>Dinamik İçerik Düzenleyicisi

Düzenleme işlemi tamamlandığında dinamik içerik Düzenleyicisi içeriğinizdeki karakterleri otomatik olarak çıkar. Örneğin, İçerik Düzenleyicisi 'ndeki aşağıdaki içerik iki ifade fonksiyoniyle bir dize ilişkilendirimiyle. 

```json
{ 
  "type": "@{if(equals(1, 2), 'Blob', 'Table' )}",
  "name": "@{toUpper('myData')}"
}
```

Dinamik İçerik Düzenleyicisi içerikleri ifadeye dönüştürür `"{ \n  \"type\": \"@{if(equals(1, 2), 'Blob', 'Table' )}\",\n  \"name\": \"@{toUpper('myData')}\"\n}"` . Bu ifadenin sonucu aşağıda gösterilen bir JSON biçim dizesidir.

```json
{
  "type": "Table",
  "name": "MYDATA"
}
```

### <a name="a-dataset-with-a-parameter"></a>Parametresi olan bir veri kümesi
Aşağıdaki örnekte, BlobDataset, **Path** adlı bir parametre alır. Değeri, şu ifadeyi kullanarak **FolderPath** özelliği için bir değer ayarlamak için kullanılır: `dataset().path` . 

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

### <a name="a-pipeline-with-a-parameter"></a>Parametresi olan bir işlem hattı
Aşağıdaki örnekte, işlem hattı **inputPath** ve **OutputPath** parametrelerini alır. Parametreli blob veri kümesinin **yolu** , bu parametrelerin değerleri kullanılarak ayarlanır. Burada kullanılan sözdizimi: `pipeline().parameters.parametername` . 

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

### <a name="replacing-special-characters"></a>Özel karakterleri değiştirme

Dinamik İçerik Düzenleyicisi, düzenleme işlemi bitince içeriklerde çift tırnak işareti ve ters eğik çizgi gibi karakterleri otomatik olarak çıkar. Bu **, Replace**() işlevinde, **\t, \t** kullanarak satır beslemesini veya sekmeyi değiştirmek istediğinizde sorun oluşmasına neden olur. Deyimi ek \ ' i kaldırmak için kod görünümündeki dinamik içeriğinizi düzenleyebilir veya ifade dilini kullanarak özel karakterleri değiştirmek için aşağıdaki adımları izleyebilirsiniz:

1. Özgün dize değerine karşılık gelen URL kodlaması
1. URL kodlamalı dizeyi Değiştir, örneğin, satır besleme (% 0A), satır başı (% 0D), yatay sekme (%09).
1. URL kod çözme

Örneğin, değerinde bir yeni satır karakteri olan *CompanyName* , ifadesi `@uriComponentToString(replace(uriComponent(variables('companyName')), '%0A', ''))` yeni satır karakterini kaldırabilir. 

```json
Contoso-
Corporation
```

### <a name="escaping-single-quote-character"></a>Kaçış tek tırnak karakteri

İfade işlevleri dize değeri parametreleri için tek tırnak kullanır. Dize işlevlerinde bir ' karakteri kaçış için iki tek tırnak kullanın. Örneğin, ifade `@concat('Baba', ''' ', 'book store')` aşağıdaki sonucu döndürür.

```
Baba's book store
```

### <a name="tutorial"></a>Öğretici
Bu [öğreticide](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) , bir işlem hattı ve etkinlik arasında ve Etkinlikler arasında parametrelerin nasıl geçirileceğini adım adım gösterilmektedir.

  
## <a name="functions"></a>İşlevler

İfadeler içindeki işlevleri çağırabilirsiniz. Aşağıdaki bölümler, bir ifadede kullanılabilecek işlevlerle ilgili bilgiler sağlar.  

## <a name="string-functions"></a>Dize işlevleri  

Dizelerle çalışmak için, bu dize işlevlerini ve ayrıca bazı [koleksiyon işlevlerini](#collection-functions)kullanabilirsiniz.
Dize işlevleri yalnızca dizeler üzerinde çalışır.

| String işlevi | Görev |
| --------------- | ---- |
| [Concat](control-flow-expression-language-functions.md#concat) | İki veya daha fazla dizeyi birleştirin ve Birleşik dizeyi döndürün. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | Bir dizenin belirtilen alt dizeyle bitip bitmediğini denetleyin. |
| [guid](control-flow-expression-language-functions.md#guid) | Bir dize olarak bir genel benzersiz tanımlayıcı (GUID) oluşturun. |
| [IndexOf](control-flow-expression-language-functions.md#indexof) | Bir alt dize için başlangıç konumunu döndürür. |
| [LastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Alt dizenin son geçtiği başlangıç konumunu döndürür. |
| [değiştirin](control-flow-expression-language-functions.md#replace) | Bir alt dizeyi belirtilen dizeyle değiştirin ve güncelleştirilmiş dizeyi döndürün. |
| [ayırmayı](control-flow-expression-language-functions.md#split) | Özgün dizedeki belirtilen sınırlayıcı karakteri temel alan daha büyük bir dizeden, virgülle ayrılmış alt dizeler içeren bir dizi döndürür. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | Bir dizenin belirli bir alt dizeyle başlatılıp başlatılmayacağını denetleyin. |
| [dizeden](control-flow-expression-language-functions.md#substring) | Belirtilen konumdan başlayarak bir dizeden karakter döndürün. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Küçük harfli bir dize döndürür. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | Büyük harfle bir dize döndürür. |
| [kırpma](control-flow-expression-language-functions.md#trim) | Baştaki ve sondaki boşlukları bir dizeden kaldırın ve güncelleştirilmiş dizeyi döndürün. |

## <a name="collection-functions"></a>Koleksiyon işlevleri

Koleksiyonlar, genellikle diziler, dizeler ve bazen sözlüklerle çalışmak için bu koleksiyon işlevlerini kullanabilirsiniz.

| Koleksiyon işlevi | Görev |
| ------------------- | ---- |
| [vardır](control-flow-expression-language-functions.md#contains) | Bir koleksiyonun belirli bir öğeye sahip olup olmadığını denetleyin. |
| [empty](control-flow-expression-language-functions.md#empty) | Bir koleksiyonun boş olup olmadığını kontrol edin. |
| [adı](control-flow-expression-language-functions.md#first) | Bir koleksiyondaki ilk öğeyi döndürür. |
| [imin](control-flow-expression-language-functions.md#intersection) | Belirtilen koleksiyonlar genelinde *yalnızca* ortak öğeler içeren bir koleksiyon döndürür. |
| [ayrılma](control-flow-expression-language-functions.md#join) | Bir diziden, belirtilen karakterle ayrılmış olan *Tüm* öğeleri içeren bir dize döndürür. |
| [soyadına](control-flow-expression-language-functions.md#last) | Bir koleksiyondaki son öğeyi döndürür. |
| [length](control-flow-expression-language-functions.md#length) | Bir dize veya dizideki öğelerin sayısını döndürün. |
| [Şimdilik](control-flow-expression-language-functions.md#skip) | Öğeleri bir koleksiyonun önünden kaldırın ve *diğer tüm* öğeleri döndürün. |
| [almanız](control-flow-expression-language-functions.md#take) | Bir koleksiyonun önünden öğe döndürün. |
| [birleşim](control-flow-expression-language-functions.md#union) | Belirtilen koleksiyonlardaki *Tüm* öğeleri içeren bir koleksiyon döndürür. | 

## <a name="logical-functions"></a>Mantıksal işlevler  

Bu işlevler, koşullar içinde yararlı olduğundan, her türlü mantığı değerlendirmek için kullanılabilirler.  
  
| Mantıksal karşılaştırma işlevi | Görev |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Tüm ifadelerin doğru olup olmadığını denetleyin. |
| [eşittir](control-flow-expression-language-functions.md#equals) | Her iki değerin de eşdeğer olup olmadığını denetleyin. |
| [büyüktür](control-flow-expression-language-functions.md#greater) | İlk değerin ikinci değerden büyük olup olmadığını kontrol edin. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | İlk değerin ikinci değere eşit veya ondan büyük olup olmadığını kontrol edin. |
| [kullandıysanız](control-flow-expression-language-functions.md#if) | İfadenin true veya false olduğunu denetleyin. Sonuca göre belirtilen değeri döndürün. |
| [büyüktür](control-flow-expression-language-functions.md#less) | İlk değerin ikinci değerden küçük olup olmadığını kontrol edin. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | İlk değerin ikinci değere eşit veya ondan küçük olup olmadığını kontrol edin. |
| [başlatılmadı](control-flow-expression-language-functions.md#not) | İfadenin yanlış olup olmadığını denetleyin. |
| [veya](control-flow-expression-language-functions.md#or) | En az bir ifadenin doğru olup olmadığını denetleyin. |
  
## <a name="conversion-functions"></a>Dönüşüm işlevleri  

 Bu işlevler, dildeki yerel türlerin her biri arasında dönüştürme yapmak için kullanılır:  
-   string
-   tamsayı
-   float
-   boolean
-   dizi
-   sözlüğü

| Dönüştürme işlevi | Görev |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | Belirtilen tek bir girdiden bir dizi döndürür. Birden çok giriş için bkz. [Createarray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Bir dize için Base64 kodlamalı sürüm döndürün. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Base64 ile kodlanmış bir dize için ikili sürümü döndürün. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Base64 ile kodlanmış bir dize için dize sürümünü döndürün. |
| [ý](control-flow-expression-language-functions.md#binary) | Bir giriş değeri için ikili sürümü döndürün. |
| [bool](control-flow-expression-language-functions.md#bool) | Bir giriş değeri için Boole sürümü döndürün. |
| [Coalesce](control-flow-expression-language-functions.md#coalesce) | Bir veya daha fazla parametreden null olmayan ilk değeri döndürün. |
| [createArray](control-flow-expression-language-functions.md#createArray) | Birden çok girişe bir dizi döndürün. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Giriş değeri için veri URI 'sini döndürün. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Bir veri URI 'SI için ikili sürümü döndürün. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Bir veri URI 'SI için dize sürümünü döndürün. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Base64 ile kodlanmış bir dize için dize sürümünü döndürün. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Bir veri URI 'SI için ikili sürümü döndürün. |
| [decodeURIComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Kaçış karakterlerinin kodunu çözülmüş sürümlerle değiştiren bir dize döndürür. |
| [encodeURIComponent](control-flow-expression-language-functions.md#encodeUriComponent) | URL-güvenli olmayan karakterlerin yerine kaçış karakterleri içeren bir dize döndürür. |
| [float](control-flow-expression-language-functions.md#float) | Giriş değeri için bir kayan nokta numarası döndürür. |
| [int](control-flow-expression-language-functions.md#int) | Bir dize için tamsayı sürümünü döndürün. |
| [nesnesinde](control-flow-expression-language-functions.md#json) | Bir dize veya XML için JavaScript Nesne Gösterimi (JSON) türü değerini veya nesnesini döndürün. |
| [string](control-flow-expression-language-functions.md#string) | Bir giriş değeri için dize sürümünü döndürün. |
| [URIComponent](control-flow-expression-language-functions.md#uriComponent) | URL-güvenli olmayan karakterleri kaçış karakterleriyle değiştirerek, bir giriş değeri için URI kodlu sürümü döndürün. |
| [Urıonenttobinary](control-flow-expression-language-functions.md#uriComponentToBinary) | URI kodlamalı dize için ikili sürümü döndürün. |
| [Urıonenttostring](control-flow-expression-language-functions.md#uriComponentToString) | URI kodlamalı dize için dize sürümünü döndürün. |
| ['sini](control-flow-expression-language-functions.md#xml) | Bir dize için XML sürümünü döndürün. |
| [XPath](control-flow-expression-language-functions.md#xpath) | XML 'yi bir XPath (XML Path Language) ifadesiyle eşleşen düğümler veya değerler için denetleyin ve eşleşen düğümleri veya değerleri döndürün. |

## <a name="math-functions"></a>Matematik işlevleri  
 Bu işlevler, iki tür numara için kullanılabilir: **tamsayılar** ve **float**.  

| Math işlevi | Görev |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | İki sayı eklemenin sonucunu döndürür. |
| [div](control-flow-expression-language-functions.md#div) | İki sayının bölünme sonucunu döndürür. |
| [Biçimlendir](control-flow-expression-language-functions.md#max) | Sayı veya dizi kümesinden en yüksek değeri döndürün. |
| [dk](control-flow-expression-language-functions.md#min) | Sayı veya dizi kümesinden en düşük değeri döndürün. |
| [alma](control-flow-expression-language-functions.md#mod) | İki sayıdan ayırarak kalanı döndürün. |
| [MUL](control-flow-expression-language-functions.md#mul) | Ürünü iki sayıdan çarpmadan döndürün. |
| [ran](control-flow-expression-language-functions.md#rand) | Belirtilen aralıktan rastgele bir tamsayı döndürür. |
| [aralığı](control-flow-expression-language-functions.md#range) | Belirtilen tamsayıdan başlayan bir tamsayı dizisi döndürür. |
| [alt](control-flow-expression-language-functions.md#sub) | İkinci sayının ilk sayıdan çıkarılmadan elde edilen sonucu döndürür. |
  
## <a name="date-functions"></a>Tarih işlevleri  

| Date veya Time işlevi | Görev |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Zaman damgasına bir gün sayısı ekleyin. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Zaman damgasına bir saat sayısı ekleyin. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | Zaman damgasına bir dakika sayısı ekleyin. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | Zaman damgasına bir saniye sayısı ekleyin. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Zaman damgasına bir dizi zaman birimi ekleyin. Ayrıca bkz. [Getfuturetime](control-flow-expression-language-functions.md#getFutureTime). |
| [Convertfromulc](control-flow-expression-language-functions.md#convertFromUtc) | Evrensel Saat (UTC) zaman damgasını hedef saat dilimine dönüştürür. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Kaynak saat dilimindeki bir zaman damgasını hedef saat dilimine dönüştürür. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | Kaynak saat diliminden bir zaman damgasını Eşgüdümlü Evrensel Saat (UTC) olarak dönüştürür. |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Zaman damgasından ay bileşeninin gününü döndürün. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Zaman damgasından hafta bileşeninin gününü döndürün. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | Zaman damgasından yıl bileşeninin gününü döndürün. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Zaman damgasını isteğe bağlı biçimde bir dize olarak döndürür. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Geçerli zaman damgasını ve belirtilen zaman birimlerini döndürür. Ayrıca bkz. [Addtotime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Geçerli zaman damgasını belirtilen zaman birimleriyle döndürün. Ayrıca bkz. [alt Tractfromtime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Zaman damgası için günün başlangıcını döndürür. |
| [Saat başı](control-flow-expression-language-functions.md#startOfHour) | Zaman damgası için saatin başlangıcını döndürür. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Zaman damgası için ayın başlangıcını döndürür. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Zaman damgasından birkaç zaman birimi çıkarın. Ayrıca bkz. [Getpasttime](control-flow-expression-language-functions.md#getPastTime). |
| [onay](control-flow-expression-language-functions.md#ticks) | `ticks`Belirtilen zaman damgası için özellik değerini döndürün. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Geçerli zaman damgasını bir dize olarak döndürür. |

## <a name="function-reference"></a>İşlev başvurusu

Bu bölüm, tüm kullanılabilir işlevleri alfabetik sırayla listeler.

<a name="add"></a>

### <a name="add"></a>add

İki sayı eklemenin sonucunu döndürür.

```
add(<summand_1>, <summand_2>)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Yes | Integer, float veya Mixed | Eklenecek numaralar |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | -----| ----------- |
| <*Sonuç-toplam*> | Tamsayı veya kayan | Belirtilen sayıları eklemenin sonucu |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
| <*miş*> | Yes | Tamsayı | Eklenecek gün sayısının pozitif veya negatif sayısı |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
| <*saatlerinin*> | Yes | Tamsayı | Eklenecek saatlerin pozitif veya negatif sayısı |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
| <*dakika*> | Yes | Tamsayı | Eklenecek pozitif veya negatif dakika sayısı |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
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

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Zaman damgasına bir saniye sayısı ekleyin.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
| <*saniyeden*> | Yes | Tamsayı | Eklenecek pozitif veya negatif saniye sayısı |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
| <*aralığında*> | Yes | Tamsayı | Eklenecek belirtilen zaman birimi sayısı |
| <*timeUnit*> | Evet | Dize | *Aralık* ile kullanılacak zaman birimi: "saniye", "dakika", "saat", "gün", "hafta", "ay", "yıl" |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
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

Her iki ifadenin de doğru olup olmadığını denetleyin.
Her iki ifade de true olduğunda true, en az bir ifade false olduğunda false döndürün.

```
and(<expression1>, <expression2>)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ifade1*>, <*İfade2*> | Yes | Boole | Denetlenecek ifadeler |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | -----| ----------- |
| true veya false | Boole | Her iki ifade de true olduğunda true döndürün. En az bir ifade false olduğunda false döndürün. |
||||

*Örnek 1*

Bu örnekler, belirtilen Boolean değerlerinin her ikisinin de doğru olup olmadığını denetler:

```
and(true, true)
and(false, true)
and(false, false)
```

Ve şu sonuçları döndürür:

* İlk örnek: her Iki ifade de true, bu nedenle döndürüyor `true` .
* İkinci örnek: bir ifade false 'dur, bu nedenle döndürür `false` .
* Üçüncü örnek: her Iki ifade de false 'dur, bu nedenle döndürür `false` .

*Örnek 2*

Bu örnekler, belirtilen ifadelerin her ikisinin de doğru olup olmadığını denetler:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

Ve şu sonuçları döndürür:

* İlk örnek: her Iki ifade de true, bu nedenle döndürüyor `true` .
* İkinci örnek: bir ifade false 'dur, bu nedenle döndürür `false` .
* Üçüncü örnek: her Iki ifade de false 'dur, bu nedenle döndürür `false` .

<a name="array"></a>

### <a name="array"></a>array

Belirtilen tek bir girdiden bir dizi döndürür.
Birden çok giriş için bkz. [Createarray ()](#createArray).

```
array('<value>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Dizi oluşturmak için dize |
|||||

| Döndürülen değer | Tür | Description |
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

### <a name="base64"></a>base64

Bir dize için Base64 kodlamalı sürüm döndürün.

```
base64('<value>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Giriş dizesi |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*Base64 dizesi*> | Dize | Giriş dizesi için Base64 kodlamalı sürüm |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Dönüştürülecek Base64 kodlamalı dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*ikili-for-Base64-dize*> | Dize | Base64 ile kodlanmış dizenin ikili sürümü |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Kod çözme için Base64 kodlamalı dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*kodu çözülmüş-Base64-dize*> | Dize | Base64 ile kodlanmış bir dize için dize sürümü |
||||

*Örnek*

Bu örnek, "aGVsbG8 =" Base64 kodlamalı dizeyi yalnızca bir dizeye dönüştürür:

```
base64ToString('aGVsbG8=')
```

Ve şu sonucu döndürür: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>ikili

Dize için ikili sürümü döndürün.

```
binary('<value>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Dönüştürülecek dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*-for-Input-Value*> | Dize | Belirtilen dizenin ikili sürümü |
||||

*Örnek*

Bu örnek, "Hello" dizesini ikili dizeye dönüştürür:

```
binary('hello')
```

Ve şu sonucu döndürür:

`"0110100001100101011011000110110001101111"`

<a name="bool"></a>

### <a name="bool"></a>bool

Bir değer için Boolean sürümünü döndürün.

```
bool(<value>)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Yes | Herhangi biri | Dönüştürülecek değer |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>,... | Yes | Herhangi biri, türleri karıştırabilirler | Null denetlenecek bir veya daha fazla öğe |
|||||

| Döndürülen değer | Tür | Description |
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

### <a name="concat"></a>concat

İki veya daha fazla dizeyi birleştirin ve Birleşik dizeyi döndürün.

```
concat('<text1>', '<text2>', ...)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*metin1*>, <*Metin2*>,... | Evet | Dize | Birleştirilecek en az iki dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Dize | Birleşik giriş dizelerinden oluşturulan dize |
||||

*Örnek*

Bu örnek, "Hello" ve "World" dizelerini birleştirir:

```
concat('Hello', 'World')
```

Ve şu sonucu döndürür: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*koleksiyon*> | Yes | Dize, dizi veya sözlük | Denetlenecek koleksiyon |
| <*deeri*> | Yes | Sırasıyla dize, dizi veya sözlük | Bulunacak öğe |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
| <*destinationTimeZone*> | Evet | Dize | Hedef saat diliminin adı. Saat dilimi adları için bkz. [Microsoft saat dilimi Dizin değerleri](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ancak saat dilimi adından noktalama işaretlerini kaldırmanız gerekebilir. |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*dönüştürülmüş-zaman damgası*> | Dize | Zaman damgası hedef saat dilimine dönüştürüldü |
||||

*Örnek 1*

Bu örnek, bir zaman damgasını belirtilen saat dilimine dönüştürür:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

Ve şu sonucu döndürür: `"2018-01-01T00:00:00Z"`

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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
| <*sourceTimeZone*> | Evet | Dize | Kaynak saat diliminin adı. Saat dilimi adları için bkz. [Microsoft saat dilimi Dizin değerleri](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ancak saat dilimi adından noktalama işaretlerini kaldırmanız gerekebilir. |
| <*destinationTimeZone*> | Evet | Dize | Hedef saat diliminin adı. Saat dilimi adları için bkz. [Microsoft saat dilimi Dizin değerleri](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ancak saat dilimi adından noktalama işaretlerini kaldırmanız gerekebilir. |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*dönüştürülmüş-zaman damgası*> | Dize | Zaman damgası hedef saat dilimine dönüştürüldü |
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
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

Ve şu sonucu döndürür: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Kaynak saat diliminden bir zaman damgasını Eşgüdümlü Evrensel Saat (UTC) olarak dönüştürür.

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
| <*sourceTimeZone*> | Evet | Dize | Kaynak saat diliminin adı. Saat dilimi adları için bkz. [Microsoft saat dilimi Dizin değerleri](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ancak saat dilimi adından noktalama işaretlerini kaldırmanız gerekebilir. |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*dönüştürülmüş-zaman damgası*> | Dize | Zaman damgası UTC 'ye dönüştürüldü |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>,... | Yes | Any, ancak karışık değil | Diziyi oluşturmak için en az iki öğe |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Dönüştürülecek dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*veri URI 'si*> | Dize | Giriş dizesi için veri URI 'SI |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Dönüştürülecek veri URI 'SI |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*-for-Data-Uri*> | Dize | Veri URI 'sinin ikili sürümü |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Dönüştürülecek veri URI 'SI |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*-for-Data-Uri için dize*> | Dize | Veri URI 'SI için dize sürümü |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*Haftanın günü*> | Tamsayı | Pazar 0, Pazartesi 1, vb. belirtilen zaman damgasından haftanın günü |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*Yılın günü*> | Tamsayı | Belirtilen zaman damgasından yılın günü |
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
Yerine [base64ToString ()](#base64ToString) kullanmayı düşünün `decodeBase64()` .
Her iki işlev de aynı şekilde çalışır, ancak `base64ToString()` tercih edilir.

```
decodeBase64('<value>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Kod çözme için Base64 kodlamalı dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*kodu çözülmüş-Base64-dize*> | Dize | Base64 ile kodlanmış bir dize için dize sürümü |
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
Yerine [Datauritobinary ()](#dataUriToBinary)kullanmayı düşünün `decodeDataUri()` .
Her iki işlev de aynı şekilde çalışır, ancak `dataUriToBinary()` tercih edilir.

```
decodeDataUri('<value>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Kodu çözülecek veri URI dizesi |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*-for-Data-Uri*> | Dize | Bir veri URI dizesinin ikili sürümü |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Kodu çözülecek kaçış karakterlerine sahip dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*kodu çözülmüş-URI*> | Dize | Kodu çözülen kaçış karakterlerine sahip güncelleştirilmiş dize |
||||

*Örnek*

Bu örnek, bu dizedeki kaçış karakterlerinin kodunu kodu çözülmüş sürümleriyle değiştirir:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

Ve şu sonucu döndürür: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

İki sayıdan ayırarak tamsayı sonucunu döndürür.
Kalan sonucu almak için bkz. [mod ()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*eni*> | Yes | Tamsayı veya kayan | *Bölen* tarafından bölünecek sayı |
| <*Lene*> | Yes | Tamsayı veya kayan | *Bölünen bölünen* sayı, ancak 0 olamaz |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*Bölüm-sonuç*> | Tamsayı | İlk sayının ikinci sayıya bölünme sonucu tamsayı |
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
Yerine [URIComponent ()](#uriComponent)kullanmayı düşünün `encodeUriComponent()` .
Her iki işlev de aynı şekilde çalışır, ancak `uriComponent()` tercih edilir.

```
encodeUriComponent('<value>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | URI kodlu biçime dönüştürülecek dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*kodlanmış URI*> | Dize | Kaçış karakterleri içeren URI kodlamalı dize |
||||

*Örnek*

Bu örnek, bu dize için URI kodlu bir sürüm oluşturur:

```
encodeUriComponent('https://contoso.com')
```

Ve şu sonucu döndürür: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

Bir koleksiyonun boş olup olmadığını kontrol edin.
Koleksiyon boş olduğunda true döndürün ya da boş olmadığında false döndürün.

```
empty('<collection>')
empty([<collection>])
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*koleksiyon*> | Yes | String, array veya Object | Denetlenecek koleksiyon |
|||||

| Döndürülen değer | Tür | Description |
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

* İlk örnek: boş bir dizeyi geçirir, bu nedenle işlev döndürür `true` .
* İkinci örnek: "abc" dizesini geçirir, bu nedenle işlev döndürülür `false` .

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Bir dizenin belirli bir alt dizeyle bitip bitmediğini denetleyin.
Alt dize bulunduğunda true, bulunamazsa false döndürün.
Bu işlev, büyük/küçük harfe duyarlı değildir.

```
endsWith('<text>', '<searchText>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*metinleri*> | Evet | Dize | Denetlenecek dize |
| <*aramametni*> | Evet | Dize | Bulunacak bitiş alt dizesi |
|||||

| Döndürülen değer | Tür | Description |
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

### <a name="equals"></a>equals

Değerlerin, ifadelerin veya nesnelerin eşit olup olmadığını denetleyin.
Her ikisi de eşdeğer olduğunda true, eşdeğer olmadığında false döndürün.

```
equals('<object1>', '<object2>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Yes | Türlerini | Karşılaştırılacak değerler, ifadeler veya nesneler |
|||||

| Döndürülen değer | Tür | Description |
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

* İlk örnek: her Iki değer de eşdeğerdir, bu nedenle işlev döndürülür `true` .
* İkinci örnek: her Iki değer de eşdeğer değildir, bu nedenle işlev döndürür `false` .

<a name="first"></a>

### <a name="first"></a>adı

Bir dizeden veya diziden ilk öğeyi döndürür.

```
first('<collection>')
first([<collection>])
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*koleksiyon*> | Yes | Dize veya dizi | İlk öğenin bulunacağı koleksiyon |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*Birinci koleksiyon-öğe*> | Herhangi biri | Koleksiyondaki ilk öğe |
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

```
float('<value>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Dönüştürülecek geçerli bir kayan noktalı sayı olan dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*float değeri*> | Float | Belirtilen dize için kayan noktalı sayı |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*yeniden biçimlendirilmiş-zaman damgası*> | Dize | Belirtilen biçimde güncelleştirilmiş zaman damgası |
||||

*Örnek*

Bu örnek, bir zaman damgasını belirtilen biçime dönüştürür:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

Ve şu sonucu döndürür: `"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Geçerli zaman damgasını ve belirtilen zaman birimlerini döndürür.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*aralığında*> | Yes | Tamsayı | Eklenecek belirtilen zaman birimi sayısı |
| <*timeUnit*> | Evet | Dize | *Aralık* ile kullanılacak zaman birimi: "saniye", "dakika", "saat", "gün", "hafta", "ay", "yıl" |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*aralığında*> | Yes | Tamsayı | Çıkarılacak belirtilen zaman birimi sayısı |
| <*timeUnit*> | Evet | Dize | *Aralık* ile kullanılacak zaman birimi: "saniye", "dakika", "saat", "gün", "hafta", "ay", "yıl" |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
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

### <a name="greater"></a>büyüktür

İlk değerin ikinci değerden büyük olup olmadığını kontrol edin.
İlk değer daha fazla olduğunda true, küçükse false döndürün.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Yes | Integer, float veya String | İkinci değerden daha büyük olup olmadığını kontrol etmek için ilk değer |
| <*compareTo*> | Yes | Sırasıyla tamsayı, float veya dize | Karşılaştırma değeri |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Yes | Integer, float veya String | İkinci değerden büyük veya ona eşit olup olmadığını kontrol etmek için ilk değer |
| <*compareTo*> | Yes | Sırasıyla tamsayı, float veya dize | Karşılaştırma değeri |
|||||

| Döndürülen değer | Tür | Description |
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

### <a name="guid"></a>guid

Dize olarak bir genel benzersiz tanımlayıcı (GUID) oluşturun, örneğin, "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Ayrıca, GUID için "D" varsayılan biçiminden farklı, kısa çizgi ile ayrılmış 32 basamaklı bir biçim belirleyebilirsiniz.

```
guid('<format>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*formatını*> | Hayır | Dize | Döndürülen GUID için tek bir [Biçim belirleyicisi](/dotnet/api/system.guid.tostring) . Varsayılan olarak, "D" biçimindedir, ancak "N", "D", "B", "P" veya "X" kullanabilirsiniz. |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*GUID değeri*> | Dize | Rastgele oluşturulmuş bir GUID |
||||

*Örnek*

Bu örnek, aynı GUID 'i, kısa çizgi ile ayrılmış ve parantez içine alınmış, 32 basamaklı olarak oluşturur:

```
guid('P')
```

Ve şu sonucu döndürür: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

İfadenin true veya false olduğunu denetleyin.
Sonuca göre belirtilen değeri döndürün.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ifadesini*> | Yes | Boole | Denetlenecek ifade |
| <*valueIfTrue*> | Yes | Herhangi biri | İfade true olduğunda döndürülecek değer |
| <*valueIfFalse*> | Yes | Herhangi biri | İfade false olduğunda döndürülecek değer |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*Belirtilen-Return-Value*> | Herhangi biri | İfadenin true veya false olup olmadığına göre döndürülen belirtilen değer |
||||

*Örnek*

Bu örnek `"yes"` , belirtilen ifade true döndürdüğünden döndürür.
Aksi takdirde, örnek şunu döndürür `"no"` :

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>IndexOf

Bir alt dize için başlangıç konumu veya dizin değeri döndürün.
Bu işlev, büyük/küçük harfe duyarlı değildir ve dizinler 0 sayısıyla başlar.

```
indexOf('<text>', '<searchText>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*metinleri*> | Evet | Dize | Bulunacak alt dizenin bulunduğu dize |
| <*aramametni*> | Evet | Dize | Bulunacak alt dize |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Dönüştürülecek dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*tamsayı-sonuç*> | Tamsayı | Belirtilen dize için tamsayı sürümü |
||||

*Örnek*

Bu örnek, "10" dizesi için bir tamsayı sürümü oluşturur:

```
int('10')
```

Ve şu sonucu döndürür: `10`

<a name="json"></a>

### <a name="json"></a>json

Bir dize veya XML için JavaScript Nesne Gösterimi (JSON) türü değerini veya nesnesini döndürün.

```
json('<value>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Yes | String veya XML | Dönüştürülecek dize veya XML |
|||||

| Döndürülen değer | Tür | Description |
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

### <a name="intersection"></a>imin

Belirtilen koleksiyonlar genelinde *yalnızca* ortak öğeler içeren bir koleksiyon döndürür.
Sonuçta görünmesi için, bu işleve geçirilen tüm koleksiyonlara bir öğe gelmelidir.
Bir veya daha fazla öğe aynı ada sahip ise, bu adı taşıyan son öğe sonuçta görüntülenir.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*Collection2*>,... | Yes | Dizi veya nesne, ancak her ikisi birden değil | *Yalnızca* ortak öğelerin olmasını istediğiniz Koleksiyonlar |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*ortak öğeler*> | Sırasıyla dizi veya nesne | Belirtilen koleksiyonlar genelinde yalnızca ortak öğelere sahip olan bir koleksiyon |
||||

*Örnek*

Bu örnek, bu diziler genelinde ortak öğeleri bulur:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

Ve *yalnızca* şu öğeler içeren bir dizi döndürür: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Bir dizideki tüm öğeleri içeren ve her karakteri *sınırlayıcı* ile ayrılmış bir dize döndürür.

```
join([<collection>], '<delimiter>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*koleksiyon*> | Yes | Dizi | Katılacak öğelere sahip dizi |
| <*ayırıcı*> | Evet | Dize | Elde edilen dizedeki her karakter arasında görünen ayırıcı |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*CHAR1* >< *sınırlayıcı* >< *CHAR2* >< *sınırlayıcı*>... | Dize | Belirtilen dizideki tüm öğelerden oluşturulan elde edilen dize |
||||

*Örnek*

Bu örnek, bu dizideki tüm öğelerden belirtilen karakteri sınırlayıcı olarak bir dize oluşturur:

```
join(createArray('a', 'b', 'c'), '.')
```

Ve şu sonucu döndürür: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>Son

Bir koleksiyondaki son öğeyi döndürür.

```
last('<collection>')
last([<collection>])
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*koleksiyon*> | Yes | Dize veya dizi | Son öğenin bulunacağı koleksiyon |
|||||

| Döndürülen değer | Tür | Description |
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

### <a name="lastindexof"></a>LastIndexOf

Bir alt dizenin son oluşumu için başlangıç konumunu veya dizin değerini döndürür.
Bu işlev, büyük/küçük harfe duyarlı değildir ve dizinler 0 sayısıyla başlar.

```
lastIndexOf('<text>', '<searchText>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*metinleri*> | Evet | Dize | Bulunacak alt dizenin bulunduğu dize |
| <*aramametni*> | Evet | Dize | Bulunacak alt dize |
|||||

| Döndürülen değer | Tür | Description |
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

### <a name="length"></a>length

Bir koleksiyondaki öğelerin sayısını döndürün.

```
length('<collection>')
length([<collection>])
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*koleksiyon*> | Yes | Dize veya dizi | Sayılacak öğelerin bulunduğu koleksiyon |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*Uzunluk veya sayı*> | Tamsayı | Koleksiyondaki öğelerin sayısı |
||||

*Örnek*

Bu örnekler, bu koleksiyonlardaki öğelerin sayısını sayar:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

Ve şu sonucu döndürür: `4`

<a name="less"></a>

### <a name="less"></a>daha az

İlk değerin ikinci değerden küçük olup olmadığını kontrol edin.
İlk değer daha az olduğunda true, ilk değer ise false değerini döndürür.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Yes | Integer, float veya String | İkinci değerden daha az olup olmadığını kontrol etmek için ilk değer |
| <*compareTo*> | Yes | Sırasıyla tamsayı, float veya dize | Karşılaştırma öğesi |
|||||

| Döndürülen değer | Tür | Description |
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

### <a name="lessorequals"></a>lessOrEquals

İlk değerin ikinci değere eşit veya ondan küçük olup olmadığını kontrol edin.
İlk değer küçüktür veya eşitse true, ilk değer ise false değerini döndürür.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Yes | Integer, float veya String | İkinci değere eşit veya ondan küçük olup olmadığını kontrol etmek için ilk değer |
| <*compareTo*> | Yes | Sırasıyla tamsayı, float veya dize | Karşılaştırma öğesi |
|||||

| Döndürülen değer | Tür | Description |
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

<a name="max"></a>

### <a name="max"></a>max

Bir liste veya diziden en yüksek değeri, her iki uçta da dahil olmak üzere sayı ile döndürün.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*sayı1*>, <*sayı2*>,... | Yes | Integer, float veya both | En yüksek değeri istediğiniz sayı kümesi |
| [<*sayı1*>, <*sayı2*>,...] | Yes | Dizi-tamsayı, kayan veya her ikisi | En yüksek değeri istediğiniz sayı dizisi |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*maksimum değer*> | Tamsayı veya kayan | Belirtilen dizideki veya sayı kümesindeki en yüksek değer |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*sayı1*>, <*sayı2*>,... | Yes | Integer, float veya both | En düşük değeri istediğiniz sayı kümesi |
| [<*sayı1*>, <*sayı2*>,...] | Yes | Dizi-tamsayı, kayan veya her ikisi | En düşük değeri istediğiniz sayı dizisi |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*En düşük değer*> | Tamsayı veya kayan | Belirtilen sayı kümesindeki veya belirtilen dizi üzerinde en düşük değer |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*eni*> | Yes | Tamsayı veya kayan | *Bölen* tarafından bölünecek sayı |
| <*Lene*> | Yes | Tamsayı veya kayan | *Bölünen bölünen* sayı, ancak 0 olamaz. |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*Modül-sonuç*> | Tamsayı veya kayan | İlk sayının ikinci sayıya bölünmesiyle kalanı |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Yes | Tamsayı veya kayan | *Multiplicand2* ile çarpılacak sayı |
| <*multiplicand2*> | Yes | Tamsayı veya kayan | *Multiplicand1* Katalan sayı |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*Ürün-sonuç*> | Tamsayı veya kayan | Ürünün ilk sayıyı ikinci sayı ile çarpılmasıyla |
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

<a name="not"></a>

### <a name="not"></a>not

İfadenin yanlış olup olmadığını denetleyin.
İfade false olduğunda true, true olduğunda false döndürün.

```json
not(<expression>)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ifadesini*> | Yes | Boole | Denetlenecek ifade |
|||||

| Döndürülen değer | Tür | Description |
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

* İlk örnek: ifade false 'dur, bu nedenle işlev döndürür `true` .
* İkinci örnek: ifadesi true 'dur, bu nedenle işlev döndürür `false` .

*Örnek 2*

Bu örnekler, belirtilen ifadelerin yanlış olup olmadığını denetler:

```json
not(equals(1, 2))
not(equals(1, 1))
```

Ve şu sonuçları döndürür:

* İlk örnek: ifade false 'dur, bu nedenle işlev döndürür `true` .
* İkinci örnek: ifadesi true 'dur, bu nedenle işlev döndürür `false` .

<a name="or"></a>

### <a name="or"></a>veya

En az bir ifadenin doğru olup olmadığını denetleyin.
En az bir ifade true olduğunda true, her ikisi de false olduğunda false döndürün.

```
or(<expression1>, <expression2>)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ifade1*>, <*İfade2*> | Yes | Boole | Denetlenecek ifadeler |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| true veya false | Boole | En az bir ifade true olduğunda true değerini döndürür. Her iki ifade de false olduğunda false döndürün. |
||||

*Örnek 1*

Bu örnekler en az bir ifadenin doğru olup olmadığını denetler:

```json
or(true, false)
or(false, false)
```

Ve şu sonuçları döndürür:

* İlk örnek: en az bir ifade true, bu nedenle işlev döndürür `true` .
* İkinci örnek: her Iki ifade de false 'dur, bu nedenle işlev döndürür `false` .

*Örnek 2*

Bu örnekler en az bir ifadenin doğru olup olmadığını denetler:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

Ve şu sonuçları döndürür:

* İlk örnek: en az bir ifade true, bu nedenle işlev döndürür `true` .
* İkinci örnek: her Iki ifade de false 'dur, bu nedenle işlev döndürür `false` .

<a name="rand"></a>

### <a name="rand"></a>rand

Yalnızca başlangıç ucunda olan belirtilen aralıktan rastgele bir tamsayı döndürür.

```
rand(<minValue>, <maxValue>)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Yes | Tamsayı | Aralıktaki en küçük tamsayı |
| <*Değerini*> | Yes | Tamsayı | İşlevin döndürebilecekleri aralıktaki en yüksek tamsayıyı izleyen tamsayı |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*Rastgele sonuç*> | Tamsayı | Belirtilen aralıktan döndürülen rastgele tamsayı |
||||

*Örnek*

Bu örnek, en büyük değer hariç, belirtilen aralıktan rastgele bir tamsayı alır:

```
rand(1, 5)
```

Ve sonuç olarak şu numaralardan birini döndürür: `1` , `2` , `3` veya `4`

<a name="range"></a>

### <a name="range"></a>aralık

Belirtilen tamsayıdan başlayan bir tamsayı dizisi döndürür.

```
range(<startIndex>, <count>)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Yes | Tamsayı | İlk öğe olarak diziyi Başlatan bir tamsayı değeri |
| <*biriktirme*> | Yes | Tamsayı | Dizideki tamsayıların sayısı |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*metinleri*> | Evet | Dize | Değiştirilecek alt dizenin bulunduğu dize |
| <*EskiMetin*> | Evet | Dize | Değiştirilecek alt dize |
| <*newText*> | Evet | Dize | Değiştirme dizesi |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*güncelleştirilmiş-metin*> | Dize | Alt dize değiştirildikten sonra güncelleştirilmiş dize <p>Alt dize bulunamazsa, özgün dizeyi döndürün. |
||||

*Örnek*

Bu örnek "eski" dizesinde "eski" alt dizesini bulur ve "Old" öğesini "New" ile değiştirir:

```
replace('the old string', 'old', 'new')
```

Ve şu sonucu döndürür: `"the new string"`

<a name="skip"></a>

### <a name="skip"></a>Atla

Öğeleri bir koleksiyonun önünden kaldırın ve *diğer tüm* öğeleri döndürün.

```
skip([<collection>], <count>)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*koleksiyon*> | Yes | Dizi | Öğelerini kaldırmak istediğiniz koleksiyon |
| <*biriktirme*> | Yes | Tamsayı | En önünde kaldırılacak öğe sayısı için pozitif bir tamsayı |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*metinleri*> | Evet | Dize | Özgün dizedeki belirtilen sınırlayıcıya göre alt dizelerdeki ayrı olacak dize |
| <*ayırıcı*> | Evet | Dize | Ayırıcı olarak kullanılacak özgün dizedeki karakter |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*metinleri*> | Evet | Dize | Denetlenecek dize |
| <*aramametni*> | Evet | Dize | Bulunacak başlangıç dizesi |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Yes | Herhangi biri | Dönüştürülecek değer |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*dize değeri*> | Dize | Belirtilen değer için dize sürümü |
||||

*Örnek 1*

Bu örnek, bu sayının dize sürümünü oluşturur:

```
string(10)
```

Ve şu sonucu döndürür: `"10"`

*Örnek 2*

Bu örnek, belirtilen JSON nesnesi için bir dize oluşturur ve \\ çift tırnak işareti (") için kaçış karakteri olarak ters eğik çizgi karakterini () kullanır.

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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*eksilen*> | Yes | Tamsayı veya kayan | *Çıkarılan* çıkarmak için gereken sayı |
| <*çıkarılan*> | Yes | Tamsayı veya kayan | *Eksilen* 'ten çıkarılacak sayı |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*kaynaklanan*> | Tamsayı veya kayan | İkinci sayının ilk sayıdan çıkarılmadan elde edilen sonuç |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*metinleri*> | Evet | Dize | Karakterleri istediğiniz dize |
| <*startIndex*> | Yes | Tamsayı | Başlangıç konumu veya dizin değeri olarak kullanmak istediğiniz pozitif bir sayı eşittir veya 0 ' dan büyük bir değer |
| <*uzunluklu*> | Yes | Tamsayı | Alt dizede istediğiniz pozitif karakter sayısı |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*alt dize-sonuç*> | Dize | Kaynak dizedeki belirtilen dizin konumunda başlayan, belirtilen sayıda karakter içeren bir alt dize |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgasını içeren dize |
| <*aralığında*> | Yes | Tamsayı | Çıkarılacak belirtilen zaman birimi sayısı |
| <*timeUnit*> | Evet | Dize | *Aralık* ile kullanılacak zaman birimi: "saniye", "dakika", "saat", "gün", "hafta", "ay", "yıl" |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
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

### <a name="take"></a>take

Bir koleksiyonun önünden öğe döndürün.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*koleksiyon*> | Yes | Dize veya dizi | Öğelerini istediğiniz koleksiyon |
| <*biriktirme*> | Yes | Tamsayı | Önünden istediğiniz öğe sayısı için pozitif bir tamsayı |
|||||

| Döndürülen değer | Tür | Description |
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

`ticks`Belirtilen zaman damgası için özellik değerini döndürün.
*Değer* bir 100-nanosaniyelik aralığıdır.

```
ticks('<timestamp>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*ilişkin*> | Evet | Dize | Zaman damgası için dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*Ticks-sayı*> | Tamsayı | Belirtilen zaman damgasından bu yana geçen onay işareti sayısı |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Küçük harfli bir dize döndürür. Dizedeki bir karakter küçük harfli bir sürüme sahip değilse, bu karakter döndürülen dizede değişmeden kalır.

```
toLower('<text>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*metinleri*> | Evet | Dize | Küçük harfle döndürülecek dize |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*metinleri*> | Evet | Dize | Büyük harfle döndürülecek dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*büyük harfli metin*> | Dize | Büyük harfli özgün dize |
||||

*Örnek*

Bu örnek, bu dizeyi büyük harfe dönüştürür:

```
toUpper('Hello World')
```

Ve şu sonucu döndürür: `"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>kırpma

Baştaki ve sondaki boşlukları bir dizeden kaldırın ve güncelleştirilmiş dizeyi döndürün.

```
trim('<text>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*metinleri*> | Evet | Dize | Kaldırılacak öndeki ve sondaki boşluğu olan dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*updatedText*> | Dize | Baştaki veya sondaki boşluk olmadan özgün dize için güncelleştirilmiş bir sürüm |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*Collection2*>,...  | Yes | Dizi veya nesne, ancak her ikisi birden değil | *Tüm* öğeleri istediğiniz yerdeki Koleksiyonlar |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Sırasıyla dizi veya nesne | Belirtilen koleksiyonlardaki tüm öğeleri içeren bir koleksiyon-yinelenen yok |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | URI kodlu biçime dönüştürülecek dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*kodlanmış URI*> | Dize | Kaçış karakterleri içeren URI kodlamalı dize |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Dönüştürülecek URI kodlamalı dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*ikili-for-Encoded-URI*> | Dize | URI kodlamalı dize için ikili sürüm. İkili içerik Base64 kodlandı ve tarafından temsil edilir `$content` . |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Kodu çözülecek URI kodlamalı dize |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*kodu çözülmüş-URI*> | Dize | URI kodlamalı dize için kodu çözülmüş sürüm |
||||

*Örnek*

Bu örnek, bu URI kodlu dize için kodu çözülmüş dize sürümünü oluşturur:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

Ve şu sonucu döndürür: `"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Geçerli zaman damgasını döndürür.

```
utcNow('<format>')
```

İsteğe bağlı olarak, <*format*> parametresiyle farklı bir biçim belirtebilirsiniz.

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*formatını*> | Hayır | Dize | Tek bir [biçim belirticisi](/dotnet/standard/base-types/standard-date-and-time-format-strings) veya bir [özel biçim deseninin](/dotnet/standard/base-types/custom-date-and-time-format-strings)olması. Zaman damgası için varsayılan biçim, [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601) ile uyumlu olan ve saat dilimi bilgilerini koruyan ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-mm-ddTHH: mm: ss: gönderildiğinde fffffffK biçiminde) şeklindedir. |
|||||

| Döndürülen değer | Tür | Description |
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

<a name="xml"></a>

### <a name="xml"></a>xml

JSON nesnesi içeren bir dize için XML sürümünü döndürün.

```
xml('<value>')
```

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*deeri*> | Evet | Dize | Dönüştürülecek JSON nesnesine sahip dize <p>JSON nesnesinin yalnızca bir root özelliği olmalıdır ve bu bir dizi olamaz. <br>\\Çift tırnak işareti (") için kaçış karakteri olarak ters eğik çizgi karakterini () kullanın. |
|||||

| Döndürülen değer | Tür | Description |
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

| Parametre | Gerekli | Tür | Description |
| --------- | -------- | ---- | ----------- |
| <*'sini*> | Yes | Herhangi biri | Bir XPath ifadesi değeriyle eşleşen düğümleri veya değerleri aramak için XML dizesi |
| <*XPath*> | Yes | Herhangi biri | Eşleşen XML düğümlerini veya değerlerini bulmak için kullanılan XPath ifadesi |
|||||

| Döndürülen değer | Tür | Description |
| ------------ | ---- | ----------- |
| <*XML düğümü*> | XML | Belirtilen XPath ifadesiyle yalnızca tek bir düğüm eşleştiğinde bir XML düğümü |
| <*deeri*> | Herhangi biri | Belirtilen XPath ifadesiyle yalnızca tek bir değer eşleştiğinde bir XML düğümündeki değer |
| [<*XML-düğüm1*>, <*xml-Düğüm2*>,...] </br>-veya- </br>[<*değer1*>, <*değer2*>,...] | Dizi | XML düğümleri veya belirtilen XPath ifadesiyle eşleşen değerler içeren bir dizi |
||||

*Örnek 1*

Örnek 1 ' den sonra bu örnek, düğüm ile eşleşen düğümleri bulur `<count></count>` ve bu düğüm değerlerini `sum()` işlevle ekler:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Ve şu sonucu döndürür: `30`

*Örnek 2*

Bu örnekte her iki ifade de `<location></location>` , bir ad alanı Ile xml içeren belirtilen bağımsız değişkenlerde düğümle eşleşen düğümleri bulur. İfadeler, \\ çift tırnak işareti (") için kaçış karakteri olarak ters eğik çizgi karakterini () kullanır.

* *İfade 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *İfade 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Bağımsız değişkenler şunlardır:

* XML belgesi ad alanını içeren bu XML `xmlns="http://contoso.com"` :

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Burada XPath ifadesi:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Düğüm ile eşleşen sonuç düğümü aşağıdadır `<location></location>` :

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Örnek 3*

Örnek 3 ' te aşağıdaki örnekte, bu örnek düğümündeki değeri bulur `<location></location>` :

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

Ve şu sonucu döndürür: `"Paris"`

## <a name="next-steps"></a>Sonraki adımlar
İfadelerde kullanabileceğiniz sistem değişkenlerinin listesi için bkz. [Sistem değişkenleri](control-flow-system-variables.md).
