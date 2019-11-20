---
title: 'Öğretici: desenler-LUSıS'
titleSuffix: Azure Cognitive Services
description: Daha az örnek konuşma sağlayıp amaç ve varlık tahminini artırmak için desenleri kullanın. Desen, varlıkları ve yok sayılabilir metni tanımlama söz dizimini içeren şablon konuşma örneğiyle sağlanır.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 4e4f1787db86378eaeff9df196cc061c42d0ab1e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499000"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats"></a>Öğretici: ortak desen şablonu söylenişi biçimleri ekleme

Bu öğreticide daha az örnek konuşma sağlayıp amaç ve varlık tahminini artırmak için desenleri kullanacaksınız. Desen, varlıkları ve yok sayılabilir metni tanımlama söz dizimini içeren şablon konuşma örneğiyle sağlanır. Desen, ifade eşleme ve makine öğrenimi işlemlerinin birleşimidir.  Şablon konuşma örneği amaç konuşmalarıyla birlikte LUIS hizmetinin amaca uygun konuşmaları anlamasını kolaylaştırır. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:**

> [!div class="checklist"]
> * Örnek uygulamayı içeri aktar 
> * Amaç oluşturma
> * Eğitim
> * Yayımlama
> * Uç noktasındaki amaçları ve varlıkları alma
> * Desen oluşturma
> * Desen tahmin geliştirmelerini onaylama
> * Metni yok sayılabilir olarak işaretleme ve desen içine yerleştirme
> * Test panelini kullanarak desen erişimini doğrulama

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Örnek uygulamayı içeri aktar

Son öğreticide oluşturulan **HumanResources** adlı uygulamayla devam edin. 

Aşağıdaki adımları kullanın:

1.  [Uygulama JSON dosyasını](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json) indirip kaydedin.

2. JSON'ı yeni bir uygulamaya içeri aktarın.

3. **Yönet** bölümünde **Sürümler** sekmesinde sürümü kopyalayın ve `patterns` olarak adlandırın. Kopyalama, özgün sürümünüzü etkilemeden farklı LUIS özelliklerini deneyebileceğiniz ideal bir yol sunar. Sürüm adı URL rotasının bir parçası olarak kullanıldığından ad bir URL'de geçerli olmayan hiçbir karakter içeremez.

## <a name="create-new-intents-and-their-utterances"></a>Yeni amaçları ve konuşmalarını oluşturma

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **Intents** (Amaçlar) sayfasında **Create new intent** (Yeni amaç oluştur) öğesini seçin. 

3. Açılan iletişim kutusuna `OrgChart-Manager` girip **Done** (Bitti) öğesini seçin.

    ![Yeni ileti oluşturma açılır penceresi](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Amaca örnek konuşmalar ekleyin.

    |Örnek konuşmalar|
    |--|
    |Who is John W. Smith the subordinate of? (John W. Smith kimin astı?)|
    |Who does John W. Smith report to? (John W. Smith kime rapor veriyor?)|
    |Who is John W. Smith's manager? (John W. Smith'in yöneticisi kim?)|
    |Who does Jill Jones directly report to? (Jill Jones kime bağlı?)|
    |Who is Jill Jones supervisor? (Jill Jones'un süpervizörü kim?)|

    [![LU, hedefe yeni söyleyeni ekleme gibi bir ekran görüntüsü](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "LU, hedefe yeni söyleyeni ekleme gibi bir ekran görüntüsü")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Amacın konuşmalarında employee varlığı yerine keyPhrase varlığı etiketlenmişse endişelenmeyin. İkisi de uç noktanın Test bölmesinde doğru şekilde tahmin edilir. 

5. Sol gezinti bölmesinden **Intents** (Amaçlar) öğesini seçin.

6. **Create new intent** (Yeni amaç oluştur) öğesini seçin. 

7. Açılan iletişim kutusuna `OrgChart-Reports` girip **Done** (Bitti) öğesini seçin.

8. Amaca örnek konuşmalar ekleyin.

    |Örnek konuşmalar|
    |--|
    |Who are John W. Smith's subordinates? (John W. Smith'in astları kimler?)|
    |John W. Smith? (Kimler John W. Smith'e rapor veriyor?)|
    |Who does John W. Smith manage? (John W. Smith kimleri yönetiyor?)|
    |Who are Jill Jones direct reports? (Jill Jones'a bağlı çalışanlar kimler?)|
    |Who does Jill Jones supervise? (Jill Jones kime süpervizörlük yapıyor?)|

## <a name="caution-about-example-utterance-quantity"></a>Örnek konuşma miktarıyla ilgili uyarı

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>Eğitim

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Yayımlama

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Uç noktadan amacı ve varlıkları alma

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Adres çubuğundaki URL'nin sonuna gidip `Who is the boss of Jill Jones?` yazın. Son sorgu dizesi parametresi konuşma `q`s**orgusu olan**  öğesidir. 

    ```json
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Bu sorgu başarılı oldu mu? Bu eğitim döngüsü için başarılı oldu. İlk iki amacın puanları birbirine yakın. LUIS eğitimi her seferinde aynı olmadığından biraz fark vardır. Bu iki puan bir sonraki eğitim döngüsünde tersine çevrilebilir. Sonuç olarak yanlış amaç döndürülebilir. 

Doğru amacın puan yüzdesini bir sonraki en yüksek puandan bir miktar daha yüksek ve uzak hale getirmek için desenleri kullanın. 

Bu ikinci tarayıcı penceresini açık bırakın. Öğreticinin sonraki bölümlerinde kullanacaksınız. 

## <a name="template-utterances"></a>Konuşma şablonları
İnsan Kaynakları etki alanının doğası gereği kuruluşlardaki çalışan ilişkileri hakkında sorulabilecek sorular farklı şekillerde yöneltilebilir. Örneğin:

|Konuşmalar|
|--|
|Who does Jill Jones report to? (Jill Jones kime rapor veriyor?)|
|Who reports to Jill Jones? (Jill Jones'a kim rapor veriyor?)|

Bu konuşmalar birbirine çok yakın olduğundan bağlam açısından farklarını belirlemek için birden fazla konuşma örneği sağlamanız gerekir. Amaç için bir desen eklediğinizde LUIS, bir amaç için sık kullanılan konuşma desenlerini çok sayıda konuşma örneği eklemeden öğrenir. 

Bu amaç için bazı konuşma şablonu örnekleri şunlardır:

|Konuşma şablonu örnekleri|söz dizimini anlamı|
|--|--|
|Who does {Employee} report to[?] ({Çalışan} kime rapor veriyor[?])|{Çalışan} değişebilir, [?] öğesini yoksay}|
|Who reports to {Employee}[?] ({Çalışana} kim rapor veriyor[?])|{Çalışan} değişebilir, [?] öğesini yoksay}|

`{Employee}` söz dizimi, varlığın konuşma şablonu içindeki konumunu ve hangi varlık olduğunu belirtir. İsteğe bağlı `[?]` söz dizimi isteğe bağlı kelimeleri veya noktalama işaretlerini belirtir. LUIS konuşmayı eşleştirir ve parantez içindeki isteğe bağlı metni yoksayar.

Söz dizimi normal ifade gibi görünür ancak normal ifade değildir. Yalnızca küme ayracı `{}` ve köşeli ayraç `[]` söz dizimi desteklenir. İki düzeye kadar iç içe yerleştirme yapılabilir.

Bir desenin bir konuşmayla eşleştirilebilmesi için ilk konuşmadaki varlıkların öncelikle konuşma şablonundaki varlıklarla eşleşmesi gerekir. Ancak şablon varlıkların değil yalnızca amaçların tahmin edilmesine yardımcı olur. 

**Desenler daha az örnek konuşma sağlamanızı mümkün kılsa da varlıkların algılanmaması durumunda desen eşleşmez.**

## <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Kuruluş Yöneticisi amacı için desenler ekleme

1. Üst menüden **Build** (Derle) öğesini seçin.

2. Sol gezinti bölmesinin **Improve app performance** (Uygulama performansını geliştir) bölümünde **Patterns** (Desenler) öğesini seçin.

3. **OrgChart-Manager** amacını seçip aşağıdaki konuşma şablonlarını girin:

    |Konuşma şablonları|
    |:--|
    |Who is {Employee} the subordinate of[?] ({Çalışan} kimin astı[?])|
    |Who does {Employee} report to[?] ({Çalışan} kime rapor veriyor[?])|
    |Who is {Employee}['s] manager[?] ({Çalışan}['ın] yöneticisi kim[?]|
    |Who does {Employee} directly report to[?] ({Çalışan} kime bağlı[?])|
    |Who is {Employee}['s] supervisor[?] ({Çalışan}['ın] süpervizörü kim[?]|
    |Who is the boss of {Employee}[?] ({Çalışanın} patronu kim[?])|

    Rollere sahip varlıklar rol adının da bulunduğu söz dizimini kullanır ve [ayrı bir rol öğreticisinde](luis-tutorial-pattern-roles.md) ele alınmaktadır. 

    Konuşma şablonunu yazdığınızda LUIS sol küme ayracını `{` girdiğinizde varlığı doldurmanıza yardımcı olur.

    [![Amaç için konuşma şablonu girme ekran görüntüsü](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. Desenler sayfasında, **kuruluş şeması-raporlar** hedefini seçin, sonra aşağıdaki şablon utslerini girin:

    |Konuşma şablonları|
    |:--|
    |Who are {Employee}['s] subordinates[?] ({Çalışan}['ın] astları kimler[?]|
    |Who reports to {Employee}[?] ({Çalışana} kim rapor veriyor[?])|
    |Who does {Employee} manage[?] ({Çalışan} kimi yönetiyor[?])|
    |Who are {Employee} direct reports[?] ({Çalışana} bağlı çalışanlar kimler[?])|
    |Who does {Employee} supervise[?] ({Çalışan} kime süpervizörlük yapıyor[?])|
    |Who does {Employee} boss[?] ({Çalışan} kime patronluk yapıyor[?])|

## <a name="query-endpoint-when-patterns-are-used"></a>Desenler kullanıldığında uç noktayı sorgulama

Artık desenler uygulamaya eklendiğine göre, uygulamayı tahmin çalışma zamanı uç noktasında eğitin, yayımlar ve sorgular.

1. Uygulamayı yeniden eğitin ve yayımlayın.

1. Tarayıcıda uç nokta URL'si sekmesine geçin.

1. Adres çubuğundaki URL'nin sonuna gidip konuşma olarak `Who is the boss of Jill Jones?` yazın. Son sorgu dizesi parametresi konuşma `q`s**orgusu olan**  öğesidir. 

    ```json
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Amaç tahmini artık önemli ölçüde daha önemlidir.

## <a name="working-with-optional-text-and-prebuilt-entities"></a>İsteğe bağlı metin ve önceden oluşturulmuş varlıklarla çalışma

Bu öğreticinin önceki bölümlerinde kullanılan desen konuşma şablonları s harfinin iyelik olarak kullanılması `'s` ve soru işaretinin kullanılması `?` gibi birkaç isteğe bağlı metin örneğine sahiptir. Söylenişi metninde geçerli ve gelecek tarihlere izin vermeniz gerektiğini varsayalım.

Örnek konuşmalar şunlardır:

|Amaç|İsteğe bağlı metin ve önceden oluşturulmuş varlıklara sahip örnek konuşmalar|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Bu örneklerin her birinde LUIS'in doğru tahmin yapabilmesi için gerekli olan bir fiil çekimi (`was`, `is`, `will be`) ve tarih (`March 3`, `now` ve `in a month`) bulunmaktadır. Son iki örnekte `in` ve `on` haricinde neredeyse aynı metnin kullanıldığına dikkat edin.

Bu isteğe bağlı bilgilere izin veren örnek şablon örnekleri: 

|Amaç|İsteğe bağlı metin ve önceden oluşturulmuş varlıklara sahip örnek konuşmalar|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


Söz diziminde isteğe bağlı köşeli parantez `[]` kullanılması isteğe bağlı metnin konuşma şablonuna eklenmesini kolaylaştırır, ikinci düzeye kadar iç içe yerleştirilebilir `[[]]` ve varlık ya da metin içerebilir.


**Soru: her bir şablon için ilk harf olan her bir `w` harf, küçük harfle neden. İsteğe bağlı olarak büyük veya küçük harf olmaması gerekir mi?** İstemci uygulaması tarafından sorgu uç noktasına gönderilen konuşma küçük harfe dönüştürülür. Konuşma şablonu ve uç nokta konuşmasında büyük harf veya küçük harf kullanılabilir. Karşılaştırma her zaman küçük harfe dönüştürme sonrasında gerçekleştirilir.

**Soru: March 3 (Mart 3) hem sayı `3` hem de tarih `March 3` olarak tahmin ediliyorsa konuşma şablonunun sayı bölümü neden önceden oluşturulmuş durumda değil?** Konuşma şablonu tahmini `March 3` olarak doğrudan veya `in a month` çıkarımıyla bağlamsal olarak kullanmaktadır. Tarih, sayı içerebilir ancak her sayı tarih olmayabilir. Her zaman tahmin JSON sonuçlarında döndürülmesini istediğiniz türü en iyi temsil eden varlığını kullanın.  

**Soru: `Who will {Employee}['s] manager be on March 3?` gibi zayıf ifadeler nasıl işlenir?** `will` ve `be` ifadelerinin ayrılması gereken bunun gibi dilbilgisi açısından farklı fiil çekimlerinin yeni bir konuşma şablonu halinde ayrılması gerekir. Var olan konuşma şablonu bununla eşleşmez. Konuşmanın amacı değişmiş olmasına rağmen konuşmadaki kelime yerleşimleri değişmemiştir. Bu değişiklik LUIS tahminini etkiler. Bu söyleyleri birleştirmek için [ve veya](#use-the-or-operator-and-groups) fiil-tenslerini gruplandırabilirsiniz. 

**Unutmayın: Önce varlıklar bulunur, ardından desen eşleştirilir.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Var olan konuşma şablonu desenini düzenleme

1. LUIS web sitesinin üst menüsünden **Build** (Derle) öğesini seçip sol taraftaki menüden **Patterns** (Desenler) öğesini belirleyin. 

1. Mevcut şablon için arama yapın, `Who is {Employee}['s] manager[?]`ve sağ taraftaki üç nokta (***...***) simgesini seçin ve ardından açılır menüden **Düzenle** ' yi seçin. 

1. Konuşma şablonunu şu şekilde değiştirin: `who is {Employee}['s] manager [[on]{datetimeV2}?]`

## <a name="add-new-pattern-template-utterances"></a>Yeni konuşma şablonu deseni ekleme

1. **Build** (Derle) menüsünün **Patterns** (Desenler) bölümünde birden fazla yeni konuşma şablonu deseni ekleyebilirsiniz. Intent (Amaç) açılan menüsünden **OrgChart-Manager** öğesini seçin ve aşağıdaki konuşma şablonlarını girin:

    |Amaç|İsteğe bağlı metin ve önceden oluşturulmuş varlıklara sahip örnek konuşmalar|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Uygulamayı eğitin.

3. Panelin en üstündeki **Test** öğesini seçerek test panelini açın. 

4. Desenin eşleştirildiğini ve amaç puanının oldukça yüksek olduğunu doğrulamak için birkaç test konuşması girin. 

    İlk konuşmayı girdikten sonra tüm tahmin sonuçlarını görebilmek için sonucun altındaki **Inspect** (İncele) öğesini seçin. Her bir söylemeye, **kuruluş şeması Yöneticisi** amacına sahip olmalıdır ve çalışanların ve datetimeV2 varlıkların değerlerini ayıklamalıdır.

    |İfade|
    |--|
    |Who will be Jill Jones manager (Jill Jones yöneticisi kim olacak)|
    |who will be jill jones's manager (jill jones'un yöneticisi kim olacak)|
    |Who will be Jill Jones's manager? (Jill Jones'un yöneticisi kim olacak?)|
    |who will be Jill jones manager on March 3 (3 Mart'ta Jill jones yöneticisi kim olacak)|
    |Who will be Jill Jones manager next Month (Önümüzdeki ay Jill Jones yöneticisi kim olacak)|
    |Who will be Jill Jones manager in a month? (Bir ay içinde Jill Jones yöneticisi kim olacak?)|

Bu konuşmaların tümü varlık bulduğundan aynı desenle eşleşir ve yüksek tahmin puanına sahiptir.

## <a name="use-the-or-operator-and-groups"></a>OR işlecini ve gruplarını kullanın

Önceki şablon dıklıları çok yakın. Şablon uttaslarını azaltmak için **grup** `()` ve **veya** `|` söz dizimini kullanın. 

Aşağıdaki 2 desenleri, Grup `()` ve veya `|` söz dizimini kullanarak tek bir düzende birleştirebilir.

|Amaç|İsteğe bağlı metin ve önceden oluşturulmuş varlıklara sahip örnek konuşmalar|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Yeni şablon söylenişi şu şekilde olacaktır: 

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`. 

Bu, gerekli fiil zaman hali etrafında bir **Grup** kullanır ve isteğe bağlı `in` ve aralarında bir **veya** dikey çizgi ile `on`. 

1. **Desenler** sayfasında, **kuruluş şeması-yönetici** filtresini seçin. `manager`arayarak listeyi daraltın. 

    ![' Manager ' terimiyle ilgili kuruluş yöneticisi amaç desenlerinde ara](./media/luis-tutorial-pattern/search-patterns.png)

1. Şablonun tek bir sürümünü saklayın (bir sonraki adımda düzenlemek için) ve diğer çeşitlemeleri silin. 

1. Şablon utterliğini şu şekilde değiştirin: 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

1. Uygulamayı eğitin.

1. Utterance 'in sürümlerini test etmek için test bölmesini kullanın:

    |Test bölmesine girilecek söylenme|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|    
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|


## <a name="use-the-utterance-beginning-and-ending-anchors"></a>Söylenişi başlangıç ve bitiş bağlayıcılarını kullanma

Desen sözdizimi, `^`bir giriş işaretinin başlangıç ve bitiş noktası sözdizimini sağlar. Başlangıç ve bitiş söylenişi bağlayıcıları, tek özel ve büyük olasılıkla sabit değerli bir şekilde hedeflemek veya hedef amaçlar için ayrı olarak kullanılmak üzere birlikte kullanılabilir. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide çok sayıda örnek konuşma olmadan tahmin edilmesi zor olan konuşmalar için iki amaç eklendi. Bunlar için desen eklemek, LUIS'in amacı önemli ölçüde daha yüksek bir puanla amacı daha iyi tahmin etmesini sağladı. Varlıkları ve yok sayılabilir metinleri işaretlemek LUIS'in deseni daha fazla konuşmaya uygulamasını mümkün hale getirdi.

> [!div class="nextstepaction"]
> [Desen ile rol kullanmayı öğrenin](luis-tutorial-pattern-roles.md)
