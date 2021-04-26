---
title: Azure Data Factory içindeki parametreleri ve ifadeleri kullanma
description: Bu nasıl yapılır makalesi, Data Factory varlıkları oluştururken kullanabileceğiniz ifadeler ve işlevler hakkında bilgi sağlar.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/08/2020
ms.openlocfilehash: c9e1abc5fb6f66981f56bc262319587d9fc4265e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566668"
---
# <a name="how-to-use-parameters-expressions-and-functions-in-azure-data-factory"></a>Azure Data Factory içindeki parametreleri, ifadeleri ve işlevleri kullanma

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-functions-variables.md)
> * [Güncel sürüm](how-to-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu belgede, öncelikle Azure Data Factory içinde parametreli veri işlem hatları oluşturma özelliğini keşfetmeye yönelik çeşitli örneklerle temel kavramları öğrenmenize odaklanacağız. Parametreleştirme ve dinamik ifadeler, çok fazla sayıda esnek ayıklama, dönüştürme, yükleme (ETL) veya ayıklama, yükleme, dönüştürme (ELT) çözümüne izin verdiklerinden, çözüm bakımının maliyetini önemli ölçüde azaltır ve yeni özelliklerin mevcut işlem hatlarına uygulanmasını hızlandırmaya olanak tanıyan ADF 'ye yönelik önemli eklemelerdir. Bu kazançlar, Parametreleştirme işlevinin sabit kodlama miktarını en aza indirecek ve bir çözümdeki yeniden kullanılabilir nesne ve işlemlerin sayısını arttığı için yapılır.

## <a name="azure-data-factory-ui-and-parameters"></a>Azure Data Factory Kullanıcı arabirimi ve parametreleri

ADF Kullanıcı arabiriminde Azure Data Factory parametre kullanımı için yeni bir bağlantı kullanıyorsanız, Visual Açıklama [parametreleri ile meta veri odaklı işlem hattı için parametrelere ve Data Factory Kullanıcı](./how-to-use-trigger-parameterization.md#data-factory-ui) arabirimine [sahip bağlı hizmetler IÇIN Data Factory Kullanıcı](./parameterize-linked-services.md#data-factory-ui) arabirimini gözden geçirin.

## <a name="parameter-and-expression-concepts"></a>Parametre ve ifade kavramları 

Dış değerleri işlem hatları, veri kümeleri, bağlı hizmetler ve veri akışları içine geçirmek için parametreleri kullanabilirsiniz. Parametre kaynağa geçirildikten sonra değiştirilemez. Parametreleme kaynakları, her seferinde farklı değerlerle yeniden kullanabilirsiniz. Parametreler tek tek veya ifadelerin bir parçası olarak kullanılabilir. Tanımdaki JSON değerleri, çalışma zamanında değerlendirilen bir sabit değer veya ifadeler olabilir.

Örnek:  
  
```json
"name": "value"
```

 veya  
  
```json
"name": "@pipeline().parameters.password"
```

İfadeler JSON dize değerinde herhangi bir yerde görünebilir ve her zaman başka bir JSON değerine neden olabilir. Burada *parola* , ifadedeki bir işlem hattı parametresidir. JSON değeri bir ifadesiyse, ifadenin gövdesi at-Sign () kaldırılarak ayıklanır \@ . İle başlayan bir sabit değer dizesi gerekliyse \@ , kullanılarak kaçışlı olması gerekir \@ \@ . Aşağıdaki örneklerde ifadelerin nasıl değerlendirildiği gösterilmektedir.  
  
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

## <a name="examples-of-using-parameters-in-expressions"></a>İfadelerde parametre kullanma örnekleri 

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

### <a name="a-dataset-with--parameters"></a>Parametreleri olan bir veri kümesi

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

### <a name="a-pipeline-with--parameters"></a>Parametreleri olan bir işlem hattı

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

  
## <a name="calling-functions-within-expressions"></a>İfadeler içinde çağırma işlevleri 

İfadeler içindeki işlevleri çağırabilirsiniz. Aşağıdaki bölümler, bir ifadede kullanılabilecek işlevlerle ilgili bilgiler sağlar.  

### <a name="string-functions"></a>Dize işlevleri  

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

### <a name="collection-functions"></a>Koleksiyon işlevleri

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

### <a name="logical-functions"></a>Mantıksal işlevler  

Bu işlevler, koşullar içinde yararlı olduğundan, her türlü mantığı değerlendirmek için kullanılabilirler.  
  
| Mantıksal karşılaştırma işlevi | Görev |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Tüm ifadelerin doğru olup olmadığını denetleyin. |
| [eşittir](control-flow-expression-language-functions.md#equals) | Her iki değerin de eşdeğer olup olmadığını denetleyin. |
| [büyüktür](control-flow-expression-language-functions.md#greater) | İlk değerin ikinci değerden büyük olup olmadığını kontrol edin. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | İlk değerin ikinci değere eşit veya ondan büyük olup olmadığını kontrol edin. |
| [if](control-flow-expression-language-functions.md#if) | İfadenin true veya false olduğunu denetleyin. Sonuca göre belirtilen değeri döndürün. |
| [büyüktür](control-flow-expression-language-functions.md#less) | İlk değerin ikinci değerden küçük olup olmadığını kontrol edin. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | İlk değerin ikinci değere eşit veya ondan küçük olup olmadığını kontrol edin. |
| [başlatılmadı](control-flow-expression-language-functions.md#not) | İfadenin yanlış olup olmadığını denetleyin. |
| [veya](control-flow-expression-language-functions.md#or) | En az bir ifadenin doğru olup olmadığını denetleyin. |
  
### <a name="conversion-functions"></a>Dönüşüm işlevleri  

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

### <a name="math-functions"></a>Matematik işlevleri  
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
  
### <a name="date-functions"></a>Tarih işlevleri  

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

## <a name="detailed-examples-for-practice"></a>Uygulama için ayrıntılı örnekler

### <a name="detailed-azure-data-factory-copy-pipeline-with-parameters"></a>Parametrelerle ilgili ayrıntılı Azure Data Factory kopyalama işlem hattı 

Bu [Azure Data Factory kopya ardışık düzen parametresi geçirme öğreticisi](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) , parametreleri bir işlem hattı ve etkinlik arasında ve Etkinlikler arasında nasıl geçiyüde size yol gösterir.

### <a name="detailed--mapping-data-flow-pipeline-with-parameters"></a>Parametrelerle ilgili ayrıntılı eşleme veri akışı işlem hattı 

Veri akışında parametrelerin nasıl kullanılacağına ilişkin kapsamlı bir örnek için [, lütfen veri akışını parametrelerle eşlemeyi](./parameters-data-flow.md) izleyin.

### <a name="detailed-metadata-driven-pipeline-with-parameters"></a>Parametrelerle ayrıntılı meta veri odaklı işlem hattı

Meta veri odaklı işlem hatlarını tasarlamak için parametrelerin nasıl kullanılacağı hakkında daha fazla bilgi edinmek için lütfen [parametrelerle birlikte metaveri temelli](./how-to-use-trigger-parameterization.md) işlem hattını izleyin. Bu, parametreler için popüler bir kullanım durumdur.


## <a name="next-steps"></a>Sonraki adımlar
İfadelerde kullanabileceğiniz sistem değişkenlerinin listesi için bkz. [Sistem değişkenleri](control-flow-system-variables.md).