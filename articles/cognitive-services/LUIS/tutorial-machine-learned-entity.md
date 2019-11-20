---
title: 'Öğretici: makine tarafından öğrenilen varlık-LUSıS ile yapılandırılmış verileri Ayıkla'
titleSuffix: Azure Cognitive Services
description: Makine tarafından öğrenilen varlığı kullanarak bir utterden yapılandırılmış verileri ayıklayın. Ayıklama doğruluğunu artırmak için, tanımlayıcılar ve kısıtlamalara sahip alt bileşenler ekleyin.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: diberry
ms.openlocfilehash: 36b75f33b4fc9062d09fbc670a509594142f09bd
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828530"
---
# <a name="tutorial-extract-structured-data-with-machine-learned-entities-in-language-understanding-luis"></a>Öğretici: Language Understanding (LUSıS) içinde makine tarafından öğrenilen varlıklar ile yapılandırılmış verileri ayıklama

Bu öğreticide, makine tarafından öğrenilen varlığı kullanarak bir utterden yapılandırılmış verileri ayıklayın. 

Makine tarafından öğrenilen varlık, kendi tanımlayıcılarını ve kısıtlamalarını içeren alt bileşen varlıkları sağlayarak [model ayrıştırma kavramını](luis-concept-model.md#v3-authoring-model-decomposition) destekler. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:**

> [!div class="checklist"]
> * Örnek uygulamayı içeri aktar
> * Makine tarafından öğrenilen varlık Ekle 
> * Alt bileşen Ekle
> * Alt bileşen tanımlayıcısını ekle
> * Alt bileşen kısıtlaması Ekle
> * Uygulamayı eğitme
> * Test uygulaması
> * Uygulama yayımlama
> * Uç noktadan varlık tahminini al

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Makine tarafından öğrenilen bir varlık neden kullanılmalıdır?

Bu öğretici, bir yerden veri ayıklamak için makine tarafından öğrenilen bir varlık ekler. 

Bir varlığın amacı Ayıklanacak verileri tanımlamaktır. Bu, verileri bir ad, bir tür (mümkünse), belirsizlik varsa verilerin herhangi bir çözümlemesini ve verileri oluşturan tam metni içerir. 

Varlığı tanımlamak için, varlığı oluşturmanız ve sonra örnekteki varlık temsil eden metni etiketetmeniz gerekir. Bu etiketlenmiş örnekler, LUID 'in varlığın ne olduğunu ve bir utterde nerede bulabileceklerini öğretin. 

## <a name="entity-decomposability-is-important"></a>Varlık ayırıcı, önemli

Varlık ayırıcı, hem amaç tahmini hem de veri ayıklama için önemlidir. 

Veri ayıklama için başlangıç ve en üst düzey varlık olan makine tarafından öğrenilen bir varlık ile başlayın. Sonra varlığı, istemci uygulaması için gereken bölümlere ayırın. 

Uygulamanıza başladığınızda varlığınızın ne kadar ayrıntılı olmasını bildiğinize karşın, en iyi uygulama makine tarafından öğrenilen bir varlıkla başlamak ve sonra uygulama malarınızın alt bileşenleri ile çıkarılması gerekir.

Pratik koşullarda, pizza uygulaması için bir siparişi temsil eden makine tarafından öğrenilen bir varlık oluşturacaksınız. Sıralamada sıra için gereken tüm parçalar olmalıdır. Başlamak için varlık, siparişle ilgili tüm metinleri ve özellikle çekme boyutunu ve miktarını içerir. 

`deliver one large cheese pizza` için bir söylenişi, sıra olarak tüm söylik 'i ayıklamalı, sonra da `1` ve `large`ayıklamalıdır. 

Toppings veya Crust gibi yapabileceğiniz daha fazla ayrıştırma daha vardır. Bu Öğreticiden sonra, bu alt bileşenleri mevcut `Order` varlığınıza eklememelisiniz.

## <a name="import-example-json-to-begin-app"></a>Uygulamayı başlatmak için example. JSON al

1.  [Uygulama json dosyasını](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)indirip kaydedin.

1. [ÖNIZLEME Luu portalında](https://preview.luis.ai), **uygulamalarım** sayfasında **içeri aktar**' ı seçin ve ardından **JSON olarak içeri aktarın**. Önceki adımdan kaydedilen JSON dosyasını bulun. Uygulamanın adını değiştirmeniz gerekmez. **Bitti** 'yi seçin

1. **Yönet** bölümünde, **sürümler** sekmesinde sürümü seçin, sonra sürümü klonlamak için **kopyala** ' yı seçin ve `mach-learn`adlandırın. Ardından, kopyalama işlemini tamamladıktan sonra **bitti** ' yi seçin. Sürüm adı URL rotasının bir parçası olarak kullanıldığından ad bir URL'de geçerli olmayan hiçbir karakter içeremez.

    > [!TIP] 
    > Uygulamanızı değiştirmeden önce kopyalama en iyi uygulamadır. Bir sürümle işiniz bittiğinde, bir sürümü. JSON veya. lu dosyası olarak dışarı aktarın ve kaynak denetimi kontrol edin.

1. Bir LUO uygulamasının temel yapı taşlarını ve amaçlarını görmek için **Oluştur** ve **amaçlar** ' ı seçin.

    ![Sürümler sayfasından amaçlar sayfasına geçin.](media/tutorial-machine-learned-entity/new-version-imported-app.png)

## <a name="label-text-as-entities-in-example-utterances"></a>Metni örnek söyleyde varlıklar olarak etiketle

Bir pizza sırası hakkındaki ayrıntıları ayıklamak için, en üst düzey, makine tarafından öğrenilen `Order` bir varlık oluşturun.

1. **Amaçlar** sayfasında **orderpizza** hedefini seçin. 

1. Örnek söyleyenlik listesinde, aşağıdaki utterance ' ı seçin. 

    |Sipariş örneği|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    En solundaki `pickup` (#1) hemen öncesinde seçmeye başlayın, ardından en sağdaki metnin hemen ötesinde `anchovies` (#2-bu etiketleme işlemini sonlandırır). Bir açılır menü görünür. Açılır kutuda varlığın adını `Order` (#3) olarak girin. Sonra listeden `Order - Create new entity` seçin (#4).

    ![Metnin başlangıç ve bitiş sırası için etiketi](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Bir varlık her zaman tüm söylik olmaz. Bu özel durumda, `pickup` siparişin nasıl alınacağını belirtir, böylece sıralama için etiketlenmiş varlığın bir parçası olmalıdır. 

1. **Varlık türü seçin** kutusunda, **Yapı Ekle** ' yi seçin ve ardından **İleri**' yi seçin. Boyut ve miktar gibi alt bileşenlere izin vermek için yapı gereklidir.

    ![Varlığa yapı Ekle](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Makine tarafından **öğrenilen varlık oluştur** kutusunda, **Yapı** kutusuna `Size` ekleyin ve ardından ENTER ' u seçin. 
1. Bir **tanımlayıcı**eklemek Için **boyut tanımlayıcılarında** `+` seçin ve ardından **Yeni tümcecik listesi oluştur**' u seçin.

1. **Yeni tümcecik liste tanımlayıcısı oluştur** kutusuna adı girin `SizeDescriptor`, ardından şu değerleri girin: `small`, `medium`ve `large`. **Öneriler** kutusu doldurulduğunda `extra large`ve `xl`' i seçin. Yeni tümcecik listesini oluşturmak için **bitti** ' yi seçin. 

    Bu tümcecik listesi tanımlayıcısı, `Size` alt bileşenin boyutla ilgili sözcükleri örnek sözcükle bulmasına yardımcı olur. Bu listenin her kelime içermesi gerekmez, ancak boyutu göstermek için beklenen sözcükleri içermesi gerekir. 

    ![Boyut alt bileşeni için bir tanımlayıcı oluşturma](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. Makine tarafından **öğrenilen varlık oluştur** penceresinde **Oluştur** ' u seçerek `Size` alt bileşenini oluşturmayı tamamlayın.  

    Bir `Size` bileşeniyle `Order` varlık oluşturulur ancak yalnızca `Order` varlığı, utterance 'e uygulandı. Örnek utde `Size` varlık metnini etiketetmeniz gerekir. 

1. Aynı örnek utterde, daha sonra açılan listeden **Boyut** varlığı ' nı seçerek, `large` **Boyut** alt bileşenini etiketleyerek. 

    ![Boyut varlığını utterance 'teki metin için etiketleyin.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    Metni açıkça etiketlediğiniz için hem etiketleme hem de tahmin eşleşmesi nedeniyle satır metnin altında kalındır.

1. `Order` varlığı, boyut varlığıyla birlikte kalan söyleyde etiketleyin. Metindeki köşeli ayraç etiketli `Order` varlığı ve içindeki `Size` varlığını gösterir.

    |Sipariş örneği|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Tüm kalan örnekler için varlık ve alt bileşenler oluşturun.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Tek bir pizza `a` harf gibi kapsanan verileri nasıl değerlendirirsiniz? Veya `pickup` olmaması ve pizza 'nin nerede beklendiğine işaret `delivery`. Ya da varsayılan boyutunu veya küçük veya büyük bir boyut olmadığını belirtmek için bir boyut eksikliği mı var? İstemci uygulamasındaki iş kurallarınızın bir parçası olarak örtük veri işleme yapmayı düşünün. 

1. Uygulamayı eğiteiçin **eğitme**' yi seçin. Eğitim, etkin modele yeni varlıklar ve etiketli utterler gibi değişiklikleri uygular.

1. Eğitimin ardından, makinenin makine tarafından öğrenilen varlığı ne kadar iyi anladığını anlamak için yeni bir örnek utance ekleyin. 

    |Sipariş örneği|
    |--|
    |`pickup XL meat lovers pizza`|

    Genel en üst varlık, `Order` etiketlidir ve `Size` alt bileşeni de noktalı çizgilerle etiketlidir. Bu başarılı bir tahmindir. 

    ![Varlıkla öngörülen yeni örnek söylenişi](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    Noktalı bağlantı, tahminini gösterir. 

1. Tahmine göre etiketlenmiş bir varlık olarak değişiklik yapın, satırı seçin ve ardından **varlık tahminlerini Onayla**' yı seçin.

    ![Varlık tahminini Onayla ' yı seçerek tahmin kabul edin.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    Bu noktada, varlığı yeni bir örnek içinde bulabildiğinden makine tarafından öğrenilen varlık çalışır. Örnek eklemelerinde, varlık doğru şekilde tahmin edilmez, varlığı ve alt bileşenleri etiketleyin. Varlık doğru şekilde tahmin edildiğinde, tahminleri onaylayın. 

## <a name="add-prebuilt-number-to-app-to-help-extract-data"></a>Verilerin ayıklanmasına yardımcı olmak için uygulamaya önceden oluşturulmuş sayı ekleyin

Sipariş bilgileri ayrıca, siparişte kaç tane Pizzas gibi bir öğe olduğunu de içermelidir. Bu verileri ayıklamak için, yeni bir makine tarafından öğrenilen alt bileşenin `Order` eklenmesi gerekir ve bu bileşenin önceden oluşturulmuş bir sayının kısıtlaması olması gerekir. Varlığı önceden oluşturulmuş bir sayıyla kısıtlayan bir sayı, metnin bir basamak, `2`veya metin `two`olup olmadığını bulur ve ayıklar.

Uygulamaya önceden oluşturulmuş numarayı ekleyerek başlayın. 

1. Sol menüden **varlıklar** ' ı seçin ve ardından **+ önceden oluşturulmuş varlık Ekle**' yi seçin. 

1. **Önceden oluşturulmuş varlıklar Ekle** kutusunda, **numarayı** arayıp seçin ve **bitti**' yi seçin. 

    ![Önceden oluşturulmuş varlık Ekle](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    Önceden oluşturulmuş varlık uygulamaya eklenir, ancak henüz bir kısıtlama değildir. 

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Veri ayıklamaya yardımcı olmak için kısıtlama ile alt bileşen varlığı oluşturma

`Order` varlık, bir öğenin kaç tane sırada olduğunu belirleyebilmek için bir `Quantity` alt bileşeni içermelidir. Ayıklanan verilerin istemci uygulama tarafından hemen kullanılabilmesi için miktarın bir sayıyla sınırlandırılması gerekir. 

Bir kısıtlama, tam eşleşme (bir liste varlığı gibi) veya normal ifadeler (örneğin, bir normal ifade varlığı veya önceden oluşturulmuş bir varlık gibi) ile bir metin eşleşmesi olarak uygulanır. 

1. **Varlıklar** ' ı seçin ve `Order` varlığı seçin. 
1. Yeni varlığı uygulamaya eklemek için **+ Bileşen Ekle** ' yi seçin `Quantity` ve ardından ENTER ' u seçin.
1. Başarı bildirimi sonrasında `Quantity` alt bileşenini seçip kısıtlama kurşun kalem ' i seçin.
1. Açılan listede, önceden oluşturulmuş numarayı seçin. 

    ![Önceden oluşturulmuş sayıyla kısıtlama olarak miktar varlığı oluşturun.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    Kısıtlaması olan varlık oluşturulur ancak örnek utetlerini henüz uygulanmaz.

    > [!NOTE]
    > Bir alt bileşen, 5 düzeye kadar bir alt bileşen içinde iç içe olabilir. Bu makalede gösterilmediğinde Portal ve API 'den erişilebilir.  

## <a name="label-example-utterance-with-subcomponent-for-quantity-to-teach-luis-about-the-entity"></a>Varlıkla ilgili olarak LUVE varlığına öğretmek için alt bileşen ile etiket örneği

1. Sol taraftaki gezinmede **amaçları** seçin, sonra **orderpizza** hedefini seçin. Aşağıdaki söyleyenlerdeki üç sayı etiketlidir ancak `Order` varlık hattının altında görsel olarak bulunur. Bu alt düzey, varlıkların bulunduğu ancak `Order` varlığı dışında kabul edildiği anlamına gelir.

    ![Önceden oluşturulmuş sayı bulundu ancak sipariş varlığı henüz kabul edilmez.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. `Quantity` varlık ile sayıları etiketleyerek `2`, sonra listeden `Quantity` seçeneğini belirleyerek. `6` ve `1` aynı örnekteki şekilde etiketleyin.

    ![Metin olarak miktar tüzel kişiliği etiketi.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)  

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Uygulamaya varlık değişikliklerini uygulamak için uygulamayı eğitme

Uygulamayı bu yeni dıklarla eğitmek için **eğitme** ' yi seçin.

![Uygulamayı eğitme örnek, örnekleri gözden geçirin.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

Bu noktada, sıralamada ayıklanabilen bazı ayrıntılar (boyut, miktar ve toplam sipariş metni) vardır. Pizza toppings, Crust türü ve yan siparişler gibi `Order` varlığın daha fazla iyileştirilmesi vardır. Bunların her biri `Order` varlığının alt bileşenleri olarak oluşturulmalıdır. 

## <a name="test-the-app-to-validate-the-changes"></a>Değişiklikleri doğrulamak için uygulamayı test etme

Etkileşimli **Test** panelini kullanarak uygulamayı test edin. Bu işlem, etkin ve eğitilen uygulamanın ne kadar iyi çalıştığını görmek için yeni bir söylik girmenize ve tahmin sonuçlarını görüntülemenize imkan tanır. Amaç tahmini oldukça emin olmalıdır (%70 üzerinde) ve varlık ayıklama en az `Order` varlığı almalıdır. 5 utlaslar her durumda işlenecek kadar, sipariş varlığının ayrıntıları eksik olabilir.

1. Üst gezinti bölmesinde **Test**'i seçin.
1. Söylenişi `deliver a medium veggie pizza` girin ve ENTER ' u seçin. Etkin Model %70 güvenilirlikle doğru amacı tahmin eden tahmini. 

    ![Amacı test etmek için yeni bir söylenişi girin.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Varlık tahminlerini görmek için **İnceleme** ' yi seçin.

    ![Etkileşimli test panelinde varlık tahminlerini görüntüleyin.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    Boyut doğru şekilde tanımlandı. `OrderPizza` hedefini gösteren örnek, boyut olarak `medium` bir örneği olmadığı, ancak orta içeren bir `SizeDescriptor` tümcecik listesinin tanımlayıcısını kullandıkınızı unutmayın.

    Miktar doğru şekilde tahmin edilmez. Bunu yapmak için, söz konusu kelimeyi kullanarak miktarları ve bu sözcüğü bir `Quantity` varlığı olarak etiketlemesini belirten daha fazla örnek ekleme ekleyebilirsiniz. 

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Uygulamayı HTTP uç noktasından erişmek üzere yayımlayın

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction--from-http-endpoint"></a>HTTP uç noktasından amacı ve varlık tahminini alın

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Adresteki URL 'nin sonuna gidin ve etkileşimli test paneline girdiğiniz sorguyu girin. 

    `deliver a medium veggie pizza`

    Son sorgu dizesi parametresi konuşma `query`s**orgusu olan**  öğesidir. 

    ```json
    {
        "query": "deliver a medium veggie pizza",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }    
    ```
    

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>İlgili bilgiler

* [Öğretici-amaçlar](luis-quickstart-intents-only.md)
* [Kavram varlıkları](luis-concept-entity-types.md) kavramsal bilgileri
* [Kavram-Özellikler](luis-concept-feature.md) kavramsal bilgileri
* [Eğitme](luis-how-to-train.md)
* [Yayımlama nasıl yapılır?](luis-how-to-publish-app.md)
* [LUSıS portalında test etme](luis-interactive-test.md)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, uygulama, bir kullanıcının utterliği amacını bulmak ve bu noktadan ayrıntıları ayıklamak için makine tarafından öğrenilen bir varlık kullanır. Makine tarafından öğrenilen varlığın kullanılması, varlığın ayrıntılarını parçalara ayırmayı sağlar.  

> [!div class="nextstepaction"]
> [Önceden oluşturulmuş keyphrase varlığı ekleme](luis-quickstart-intent-and-key-phrase.md)
