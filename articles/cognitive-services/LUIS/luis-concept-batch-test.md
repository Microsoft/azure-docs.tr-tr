---
title: Batch testi-LUSıS
titleSuffix: Azure Cognitive Services
description: Uygulamanızı iyileştirmek ve dilini anlamak için, uygulamanızda sürekli olarak çalışmak üzere toplu test kullanın.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: e9ad7c52af20762633c710b39a64fbebf0cf6213
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487736"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>LUSıS portalındaki 1000 ile Batch testleri

Toplu işlem testi, etkin eğitilen sürümünüzü tahmin doğruluğunu ölçecek şekilde doğrular. Batch testi, etkin sürümünüzde her bir amaç ve varlığın doğruluğunu görüntülemenize yardımcı olur ve sonuçları grafik ile görüntüler. Uygulamanızın bir amaca göre doğru amacı belirleyemediği veya etiketlenmesini sağlamak için bir amaca daha fazla örnek ekleme gibi bir amaca yönelik daha fazla örnek ekleme gibi işlem testi sonuçlarını gözden geçirin.

## <a name="group-data-for-batch-test"></a>Batch testi için grup verileri

Toplu test için kullanılan utterslar, LUIN için yeni bir öneme sahiptir. Bir dizi veri kümesi varsa, bu noktaları üç küme içine ayırın: bir amaca eklenen örnek, yayımlanan uç noktasından alınan utterler ve eğitilen işlem testi için kullanılan utterslar. 

## <a name="a-data-set-of-utterances"></a>Bir dizi veri kümesi

Toplu işlem testi için *veri kümesi*olarak bilinen uttersünlerdeki toplu iş dosyasını gönder. Veri kümesi, **mükerrer olmayan** bir şekilde etiketlenmiş en fazla 1.000 içeren JSON biçimli bir dosyadır. Bir uygulamada en fazla 10 veri kümesi test edebilirsiniz. Daha fazla test etmeniz gerekiyorsa, bir veri kümesini silip yeni bir tane ekleyin.

|**Kuralın**|
|--|
|\* Yinelenen söylenme yok|
|1000 utterya veya daha az|

\* Yinelemeler, ilk başta simgeleştirilmiş eşleştirmelere değil tam dize eşleşmeleri olarak değerlendirilir. 

## <a name="entities-allowed-in-batch-tests"></a>Batch testlerinde izin verilen varlıklar

Toplu iş dosyası verilerinde karşılık gelen bir varlık olmasa bile, modeldeki tüm özel varlıklar, Batch test varlıkları filtresinde görüntülenir.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Toplu işlem dosyası biçimi

Toplu iş dosyası, utbotlardan oluşur. Her bir söylemeye, algılanmak istediğiniz [makine tarafından öğrenilen varlıkların](luis-concept-entity-types.md#types-of-entities) yanında beklenen bir amaç tahmini olmalıdır. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Varlıklar içeren amaçlar için Batch sözdizimi şablonu

Batch dosyanızı başlatmak için aşağıdaki şablonu kullanın:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Toplu iş dosyası, bir varlığın başlangıcını ve sonunu not etmek için **startPos** ve **endPos** özelliklerini kullanır. Değerler sıfır tabanlıdır ve bir boşluk üzerinde başlamamalı veya bitmemelidir. Bu, startIndex ve endIndex özelliklerini kullanan sorgu günlüklerinden farklıdır. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Varlıklar olmadan amaçlar için Batch sözdizimi şablonu

Toplu iş dosyanızı varlıklar olmadan başlatmak için aşağıdaki şablonu kullanın:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Varlıkları test etmek istemiyorsanız, `entities` özelliğini dahil edin ve değeri boş bir dizi olarak ayarlayın `[]`.


## <a name="common-errors-importing-a-batch"></a>Toplu iş içeri aktarma yaygın hataları

Yaygın hatalar şunlardır: 

> * 1\.000 'den fazla Aralık
> * Bir varlık özelliği olmayan bir söylenişi JSON nesnesi. Özelliği boş bir dizi olabilir.
> * Birden çok varlıkta etiketlenmiş sözcük (ler)
> * Bir alanda başlangıç veya bitiş varlık etiketi.

## <a name="batch-test-state"></a>Batch test durumu

LUO, her bir veri kümesinin son testinin durumunu izler. Bu boyut (toplu iş içindeki deterlik sayısı), son çalıştırma tarihi ve en son sonuç (başarıyla tahmin edilen sayı) içerir.

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Batch test sonuçları

Batch test sonucu, hata matrisi olarak bilinen bir dağılım grafiktir. Bu grafik, toplu iş dosyasındaki ve geçerli modelin öngörülen amacını ve varlıklarını, 4 yönlü bir karşılaştırmaktır. 

Hatalı **pozitif** ve **yanlış negatif** bölümlerdeki veri noktaları Araştırılması gereken hataları gösterir. Tüm veri noktaları **doğru pozitif** ve **gerçek negatif** bölümlerdir, bu veri kümesinde uygulamanızın doğruluğu mükemmeldir.

![Grafik dört bölümü](./media/luis-concept-batch-test/chart-sections.png)

Bu grafik, lubunun geçerli eğitimi temelinde yanlış tahmin eden bir değer bulmanıza yardımcı olur. Sonuçlar, grafiğin her bir bölgesi için görüntülenir. Bu bölgedeki söylenişi sonuçlarını gözden geçirmek için, bu bilgileri gözden geçirmek üzere grafikteki ayrı noktaları seçin veya bölge adı ' nı seçin.

![Toplu işe testi](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Sonuçlardaki hatalar

Batch testinde hatalar, toplu iş dosyasında belirtildiği şekilde tahmin edilemeyen amaçları gösterir. Hatalar grafiğin iki kırmızı bölümünde belirtilmiştir. 

False pozitif bölümü, bir utin sahip olmaması durumunda bir amaç veya varlıkla eşleştiğini gösterir. Yanlış negatif değeri, bir tanüance 'in olması gereken bir amaç veya varlıkla eşleşmedi olduğunu gösterir. 

## <a name="fixing-batch-errors"></a>Toplu iş hatalarını düzeltme

Toplu iş testinde hatalar varsa, bir amaca daha fazla bilgi ekleyebilir ve/veya, LUTO 'lar arasında ayırt edici hale getirmenize yardımcı olmak için varlıkla birlikte daha fazla bilgi etiketi ekleyebilirsiniz. Uttersları eklediyseniz ve bunları etiketlendirmeye devam ediyorsa ve toplu iş testinde tahmin hataları almaya devam ediyorsanız, LUL 'nın daha hızlı öğrendiğine yardımcı olması için alana özgü sözlük içeren bir [ifade listesi](luis-concept-feature.md) özelliği eklemeyi göz önünde bulundurun. 

## <a name="next-steps"></a>Sonraki adımlar

* [Toplu işi test](luis-how-to-batch-test.md) etme hakkında bilgi edinin
