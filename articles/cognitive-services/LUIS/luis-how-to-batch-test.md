---
title: Batch testi gerçekleştirme-LUSıS
titleSuffix: Azure Cognitive Services
description: Yanlış amaçlar ve varlıklara sahip olan uttersları bulmak için Language Understanding (LUSıS) toplu işlem testi kümelerini kullanın.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: 9fe4f21a5c9e9e26a2f94b8a60cba47916842fe3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501799"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Bir dizi örnek ile toplu test

Toplu işlem testi, etkin eğitilen sürümünüzü tahmin doğruluğunu ölçecek şekilde doğrular. Batch testi, etkin sürümünüzde her bir amaç ve varlığın doğruluğunu görüntülemenize yardımcı olur. Uygulamanızın bir amaca göre doğru amacı belirleyemediği veya etiketlenmesini sağlamak için bir amaca daha fazla örnek ekleme gibi bir amaca yönelik daha fazla örnek ekleme gibi işlem testi sonuçlarını gözden geçirin.

## <a name="group-data-for-batch-test"></a>Batch testi için grup verileri

Toplu test için kullanılan utterslar, LUIN için yeni bir öneme sahiptir. Bir dizi veri kümesi varsa, bu noktaları üç küme içine ayırın: bir amaca eklenen örnek, yayımlanan uç noktasından alınan utterler ve eğitilen işlem testi için kullanılan utterslar.

Kullandığınız Batch JSON dosyası, başlangıç ve bitiş konumu dahil olmak üzere en üst düzey makine öğrenimi varlıklarıyla birlikte yer almalıdır. Söyleyler uygulamada zaten bulunan örneklerin bir parçası olmamalıdır. Bu, amaç ve varlıklar için olumlu tahmin etmek istediğiniz söyler olmalıdır.

Testleri amaç ve/veya varlığa göre ayırabilir veya aynı dosyadaki tüm testlere (1000 ' e kadar) sahip olabilirsiniz. 

### <a name="common-errors-importing-a-batch"></a>Toplu iş içeri aktarma yaygın hataları

Toplu iş dosyanızı LUO 'ya yükleme hatalarıyla karşılaşırsanız, aşağıdaki yaygın sorunları kontrol edin:

* Toplu iş dosyasında 1.000 ' den fazla Aralık
* Bir varlık özelliği olmayan bir söylenişi JSON nesnesi. Özelliği boş bir dizi olabilir.
* Birden çok varlıkta etiketlenmiş sözcük (ler)
* Bir alana başlayan veya biten varlık etiketleri.

## <a name="fixing-batch-errors"></a>Toplu iş hatalarını düzeltme

Toplu iş testinde hatalar varsa, bir amaca daha fazla bilgi ekleyebilir ve/veya, LUTO 'lar arasında ayırt edici hale getirmenize yardımcı olmak için varlıkla birlikte daha fazla bilgi etiketi ekleyebilirsiniz. Uttersları eklediyseniz ve bunları etiketlendirmeye devam ediyorsa ve toplu iş testinde tahmin hataları almaya devam ediyorsanız, LUL 'nın daha hızlı öğrendiğine yardımcı olması için alana özgü sözlük içeren bir [ifade listesi](luis-concept-feature.md) özelliği eklemeyi göz önünde bulundurun.


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>Lua portalını kullanarak Batch test etme 

### <a name="import-and-train-an-example-app"></a>Örnek bir uygulamayı içeri aktarma ve eğitme

Gibi bir pizza sırası alan bir uygulamayı içeri aktarın `1 pepperoni pizza on thin crust` .

1.  [Uygulama JSON dosyasını](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true) indirip kaydedin.

1. [Luo portalında](https://www.luis.ai)oturum açın ve bu yazma kaynağına atanmış uygulamaları görmek için **aboneliğinizi** ve **yazma kaynağını** seçin.
1. **Yeni uygulama** ' nın yanındaki oku SEÇIN ve JSON 'ı yeni bir uygulamaya aktarmak için **JSON olarak içeri aktar** ' a tıklayın. Uygulamayı adlandırın `Pizza app` .


1. Uygulamayı eğitebilmeniz için, gezinmede sağ üst köşedeki **eğitme** ' yi seçin.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>Toplu işlem test dosyası

JSON örneği, bir test dosyasının nasıl göründüğünü göstermek için etiketlenmiş bir varlığa sahip bir söylenişi içerir. Kendi testlerinizde, etiketli doğru amaç ve makine öğrenimi varlığı ile birçok söyleyün olması gerekir.

1. `pizza-with-machine-learned-entity-test.json`Bir metin düzenleyicisinde oluşturun veya [indirin](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) .

2. JSON biçimli toplu iş dosyasında, testte tahmin etmek istediğiniz **amacı** içeren bir söylenişi ekleyin.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Batch 'i çalıştırma

1. Üst gezinti çubuğunda **Test** ' i seçin.

2. Sağ taraftaki panelde **Batch test paneli** ' ni seçin.

    ![Batch test bağlantısı](./media/luis-how-to-batch-test/batch-testing-link.png)

3. **İçeri aktar**'ı seçin. Görüntülenen iletişim kutusunda **Dosya Seç** ' i seçin ve test etmek için *1.000 ' den fazla* yer içermeyen doğru JSON biçimine sahip bir JSON dosyası bulun.

    İçeri aktarma hataları tarayıcının üst kısmındaki kırmızı bir bildirim çubuğunda raporlanır. İçeri aktarma hataları olduğunda, hiçbir veri kümesi oluşturulmaz. Daha fazla bilgi için bkz. [genel hatalar](#common-errors-importing-a-batch).

4. Dosyanın dosya konumunu seçin `pizza-with-machine-learned-entity-test.json` .

5. Veri kümesini adlandırın `pizza test` ve **bitti**' yi seçin.

6. **Çalıştır** düğmesini seçin. Batch testi çalıştıktan sonra **sonuçları göster**' i seçin. 

    > [!TIP]
    > * **Karşıdan yükleme** ' nin seçilmesi, karşıya yüklediğiniz aynı dosyayı indirir.
    > * Batch test başarısız olursa, en az bir söylenişi hedefi tahmine uymuyor.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>Amaçlar için Batch sonuçlarını gözden geçirme

Batch test sonuçlarını gözden geçirmek için **sonuçları göster**' i seçin. Test sonuçları, test dıksları 'nın etkin sürüme göre nasıl tahmin edildiği hakkında grafiksel olarak gösterilir.

Batch grafiğinde dört çeyrek daire görüntülenir. Grafiğin sağında bir filtre vardır. Filtre, amaçları ve varlıkları içerir. Grafiğin bir [bölümünü](#review-batch-results-for-intents) veya grafik içindeki bir noktayı seçtiğinizde, ilişkili söyleyler grafiğin altında görüntülenir.

Grafiğin üzerine gelindiğinde fare tekerleği grafikteki görüntüyü büyütebilir veya azaltabilir. Bu, grafikte sıkı bir şekilde kümelenmiş çok sayıda noktaya sahip olduğunda faydalıdır.

Grafik, kırmızı renkte iki bölümden oluşan dört Quadrants içinde yer alır.

1. Filtre listesinden **Modifyorder** hedefini seçin. Söylenişi, toplu iş dosyasında listelenen pozitif tahmine başarıyla eşleşeceğinden, **gerçek bir pozitif** anlamı olarak tahmin edilir.

    > [!div class="mx-imgBorder"]
    > ![Utterance, pozitif tahminiyle başarıyla eşleşti](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    Filtreler listesindeki yeşil onay işaretleri de her bir amaç için testin başarısını gösterir. Diğer tüm amaçlar, toplu iş testinde listelenmeyen herhangi bir amaç için negatif bir test olarak her bir amaca karşı test edildiğinden, tüm diğer amaçlar 1/1 pozitif bir puana göre listelenir.

1. **Onay** hedefini seçin. Bu amaç Batch testinde listelenmez, bu nedenle Batch testinde listelenen söylenişi 'in negatif bir testi olur.

    > [!div class="mx-imgBorder"]
    > ![Toplu iş dosyasında listelenmemiş amaç için utterance başarıyla negatif olarak tahmin edildi](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Negatif test, filtredeki yeşil metin ve kılavuz ile belirtilen şekilde başarılı oldu.

### <a name="review-batch-test-results-for-entities"></a>Varlıklar için Batch test sonuçlarını gözden geçirme

ModifyOrder varlığı, alt varlıklara sahip bir makine varlığı olarak, üst düzey varlığın eşleştiğini ve alt varlıkların nasıl tahmin edilir olduğunu gösterir.

1. Filtre listesinden **Modifyorder** varlığını seçin ve ardından kılavuzdaki daireyi seçin.

1. Varlık tahmini grafiğin altında görüntülenir. Ekranda, beklenmeler ile eşleşmeyen tahminler için beklenmeler ve noktalı çizgiler ile eşleşen tahminler için düz çizgiler bulunur.

    > [!div class="mx-imgBorder"]
    > ![Varlık üst öğesi toplu iş dosyasında başarıyla tahmin edildi](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>Grafik sonuçlarını filtrele

Grafiği belirli bir amaç veya varlığa göre filtrelemek için, sağ taraftaki filtreleme panelinde amacı veya varlığı seçin. Veri noktaları ve kendi dağıtım güncelleştirmeleri seçiminize göre grafiğe göre yapılır.

![Görselleştirilen Batch test sonucu](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>Grafik sonucu örnekleri

LUSıS portalındaki grafik, aşağıdaki işlemleri gerçekleştirebilirsiniz:
 
#### <a name="view-single-point-utterance-data"></a>Tek noktalı söylenişi verilerini görüntüle

Grafikte, tahminiyle ilgili belirsizlik Puanını görmek için bir veri noktasının üzerine gelin. Sayfanın alt kısmındaki utterlik listesine karşılık gelen utterlik listesini almak için bir veri noktası seçin.

![Seçili söylenişi](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>Bölüm verilerini görüntüle

Dört bölümden oluşan grafikte, grafiğin sağ üst köşesinde **yanlış pozitif** gibi bölüm adını seçin. Grafiğin altında, bu bölümdeki tüm söyleymalar bir listede grafiğin altında görüntülenir.

![Bölüme göre seçilen söyleyleri](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Bu önceki görüntüde, söylenişi, `switch on` turnallon amacına göre etiketlidir, ancak None hedefinin tahminini almış olur. Bu, beklenen tahmini yapmak için, en çok daha fazla örnek elde edilmesi gerektiğini belirten bir göstergesidir.

Grafiğin kırmızı renkte iki bölümü, beklenen tahminiyle eşleşmeyen utterlere işaret ediyor. Bu, LUIN daha fazla eğitim ihtiyacı olan SÖYLERE işaret ediyor.

Yeşil renkte grafiğin iki bölümü beklenen tahminiyle eşleşiyor.

## <a name="batch-testing-using-the-rest-api"></a>REST API kullanarak toplu işlem testi 

LUSıS, Lu, portalını ve REST API kullanarak toplu test yapmanızı sağlar. REST API uç noktaları aşağıda listelenmiştir. LUU portalını kullanarak Batch test hakkında bilgi için bkz. [öğretici: Batch test veri kümeleri](). Yer tutucu değerlerini kendi LUSıS tahmin anahtarınızla ve uç noktanızla değiştirerek aşağıdaki URL 'Leri kullanın. 

Üst bilgide HALSıS anahtarınızı eklemeyi `Ocp-Apim-Subscription-Key` ve öğesini olarak ayarlamayı unutmayın `Content-Type` `application/json` .

### <a name="start-a-batch-test"></a>Batch testi başlatma

Bir uygulama sürümü KIMLIĞINI veya bir yayımlama yuvasını kullanarak bir Batch testi başlatın. Aşağıdaki uç nokta biçimlerinden birine bir **Post** isteği gönderin. Batch dosyanızı isteğin gövdesine ekleyin.

Yayımlama yuvası
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

Uygulama sürümü KIMLIĞI
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

Bu uç noktalar, durumu denetlemek için kullanacağınız bir işlem KIMLIĞI döndürür ve sonuçları elde eder. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>Devam eden toplu iş testinin durumunu al

Aşağıdaki uç nokta biçimlerinden durumunu almak için başlattığınız Batch testinde işlem KIMLIĞI ' ni kullanın: 

Yayımlama yuvası
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

Uygulama sürümü KIMLIĞI
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>Toplu iş testinizden sonuçları al

Aşağıdaki uç nokta biçimlerinden sonuçlarını almak için başlattığınız Batch testinde işlem KIMLIĞI ' ni kullanın: 

Yayımlama yuvası
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

Uygulama sürümü KIMLIĞI
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>Utterslar toplu iş dosyası

Toplu işlem testi için *veri kümesi* olarak bilinen uttersünlerdeki toplu iş dosyasını gönder. Veri kümesi, en fazla 1.000 etiketli bir JSON biçimli dosyadır. Bir uygulamada en fazla 10 veri kümesi test edebilirsiniz. Daha fazla test etmeniz gerekiyorsa, bir veri kümesini silip yeni bir tane ekleyin. Toplu iş dosyası verilerinde karşılık gelen bir varlık olmasa bile, modeldeki tüm özel varlıklar, Batch test varlıkları filtresinde görüntülenir.

Toplu iş dosyası, utbotlardan oluşur. Her bir söylemeye, algılanmak üzere bekleyen [makine öğrenimi varlıklarıyla](luis-concept-entity-types.md#machine-learned-ml-entity) birlikte beklenen bir amaç tahmini olmalıdır.

### <a name="batch-syntax-template-for-intents-with-entities"></a>Varlıklar içeren amaçlar için Batch sözdizimi şablonu

Batch dosyanızı başlatmak için aşağıdaki şablonu kullanın:

```JSON
{
    "LabeledTestSetUtterances": [
        {
            "text": "play a song",
            "intent": "play_music",
            "entities": [
                {
                    "entity": "song_parent",
                    "startPos": 0,
                    "endPos": 15,
                    "children": [
                        {
                            "entity": "pre_song",
                            "startPos": 0,
                            "endPos": 3
                        },
                        {
                            "entity": "song_info",
                            "startPos": 5,
                            "endPos": 15
                        }
                    ]
                }
            ]
        }
    ]
}

```

Toplu iş dosyası, bir varlığın başlangıcını ve sonunu not etmek için **startPos** ve **endPos** özelliklerini kullanır. Değerler sıfır tabanlıdır ve bir boşluk üzerinde başlamamalı veya bitmemelidir. Bu, startIndex ve endIndex özelliklerini kullanan sorgu günlüklerinden farklıdır.

Varlıkları test etmek istemiyorsanız, `entities` özelliği ekleyin ve değeri boş bir dizi olarak ayarlayın `[]` .

### <a name="rest-api-batch-test-results"></a>REST API Batch test sonuçları

API tarafından döndürülen birkaç nesne vardır:

* Kesinlik, geri çekme ve F puanı gibi amaçlar ve varlık modelleriyle ilgili bilgiler.
* Her varlık için duyarlık, geri çekme ve F puanı gibi varlık modelleriyle ilgili bilgiler 
  * Bayrağını kullanarak, `verbose` ve gibi varlık hakkında daha fazla bilgi edinebilirsiniz `entityTextFScore` `entityTypeFScore` .
* Öngörülen ve etiketli amaç adlarıyla birlikte sunulan bildirimler
* Yanlış pozitif varlıkların listesi ve yanlış negatif varlıkların listesi.

## <a name="next-steps"></a>Sonraki adımlar

Test, LUP uygulamanızın doğru amaçları ve varlıkları algıladığını gösteriyorsa, daha fazla yer etiketleyerek veya özellik ekleyerek LUSıS uygulamanızın performansını iyileştirebilmek için çalışabilirsiniz.

* [LUSıS ile önerilen bir şekilde etiketleme](luis-how-to-review-endpoint-utterances.md)
* [LUSıS uygulamanızın performansını artırmak için özellikleri kullanın](luis-how-to-add-features.md)
