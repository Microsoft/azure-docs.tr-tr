---
title: İyi örnek utterer-LUSıS
titleSuffix: Azure Cognitive Services
description: Söyleyceler, uygulamanızın yorumlamak için gereken kullanıcıdan gelen giriştir. Kullanıcıların girecağı tümcecikleri toplayın. Aynı şeyi gösteren, ancak sözcük uzunluğu ve sözcük yerleşimi içinde farklı şekilde oluşturulan utterleri dahil edin.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 8069b3b9c9a226e29a3eae3261948ee92291726d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486633"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>LUSıS uygulamanız için nelerin iyi olduğunu anlayın

**Söyleyceler** , uygulamanızın yorumlamak için gereken kullanıcıdan gelen giriştir. LUO 'dan amaçları ve varlıkları ayıklamak için, her bir amaç için çeşitli farklı örnek türlerini yakalamak önemlidir. Etkin öğrenimi veya yeni vavaslar üzerinde eğitim almaya devam etme süreci, sanal makine tarafından öğrenilen zeka 'nın sağladığı zeka açısından önemlidir.

Kullanıcıların girebileceği düşündüklerini toplayın. Aynı şeyi gösteren, ancak çeşitli yollarla oluşturulan utterleri dahil edin:

* Utterance uzunluğu-istemci uygulamanız için kısa, orta ve uzun
* Sözcük ve tümcecik uzunluğu 
* Sözcük yerleşimi-noktadan itibaren, ortadaki ve sonunda varlık
* Denetleyicilerinin 
* Çoğullaştırma
* Kesintilerinden kaynaklanan
* Ad ve fiil seçimi
* Noktalama-doğru, yanlış ve dilbilgisi kullanımı çok iyi

## <a name="how-to-choose-varied-utterances"></a>Değişen detersliği seçme

LUSıS modelinize [örnek eklemeler ekleyerek](luis-how-to-add-example-utterances.md) ilk kez başladıysanız göz önünde bulundurmanız gereken bazı ilkeler aşağıda verilmiştir.

### <a name="utterances-arent-always-well-formed"></a>Utterslar her zaman iyi biçimlendirilmemiş

"" Kayıt "veya" Paris uçuş "gibi bir cümle parçası olan" benim için Istanbul için bir bilet  Kullanıcılar genellikle yazım hataları yapar. Uygulamanızı planlarken, LUO 'ya geçirmeden önce Kullanıcı girişini düzeltmek için [Bing yazım denetimi](luis-tutorial-bing-spellcheck.md) kullanıp kullanmayacağınızı düşünün. 

Kullanıcı araslarını yazım denetimi yapmazsanız, LUSıS 'yi, yazım hataları ve yazım hataları içeren uttaslar üzerinde eğitmelisiniz.

### <a name="use-the-representative-language-of-the-user"></a>Kullanıcının temsili dilini kullan

Utterlere seçerken, yaygın bir terim veya tümcecik, istemci uygulamanızın tipik kullanıcısı için doğru olmayabilir. Etki alanı deneyimine sahip olmayabilir. Bir kullanıcının yalnızca uzman olmaları durumunda söyledikleri terimleri veya tümceleri kullanırken dikkatli olun.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Değişen terminolojiyi ve ifade ' i seçin

Fark eden tümce desenleri oluşturmaya yönelik çabalar oluştursanız bile, bazı sözlük tekrarlamaya devam edersiniz.

Bu örnek aşağıdaki adımları uygulayın:

|Örnek konuşmalar|
|--|
|bir bilgisayarı nasıl edinebilirim?|
|Bir bilgisayarı nereden alabilirim?|
|Bir bilgisayar almak istiyorum, nasıl gidebilirim?|
|Bir bilgisayar ne zaman olabilir?| 

Buradaki temel terim, "bilgisayar" olarak farklılık gösterir. Masaüstü bilgisayar, dizüstü bilgisayar, iş istasyonu veya hatta yalnızca makine gibi alternatifleri kullanın. LUSıS, bağlamdaki Eşanlamlı sözcükleri akıllıca algılar, ancak eğitim için söyler oluşturduğunuzda bunları değiştirmek daha iyidir.

## <a name="example-utterances-in-each-intent"></a>Her amaç için örnek söylenme

Her bir amaç, en az 15 örnek bir olmalıdır. Herhangi bir örnek elde gerektirmeyen bir amaç varsa, LUO 'yı eğitemeyeceksiniz. Bir veya çok az örnek ile ilgili bir amaç varsa, LUARA, amacı doğru tahmin etmez. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Her yazma yinelemesi için küçük sayıda 15 utterations ekleyin

Modelin her yinelemesinde, büyük miktarlarda sayı eklemeyin. Sayıları 15 ' te ekleyin. Yeniden [eğitin](luis-how-to-train.md), [yayımlayın](luis-how-to-publish-app.md)ve [Test](luis-interactive-test.md) edin.  

LUSıS, lular model yazarı tarafından dikkatle seçilmiş olan deterleri olan etkili modeller oluşturur. Çok fazla sayıda söyleyme eklemek karışıklık sunduğundan önemli değildir.  

Birkaç noktadır başlamak daha iyidir, ardından doğru amaç tahmini ve varlık ayıklama için [uç nokta utslerini gözden geçirin](luis-how-to-review-endpoint-utterances.md) .

## <a name="utterance-normalization"></a>Utterance normalleştirmesi

Utterance normalleştirme, eğitim ve tahmin sırasında noktalama ve aksanların etkilerini gözardı eden bir işlemdir.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>Aksanların ve noktalama işaretlerinin utterance normalleştirilmesi

Uygulama JSON dosyasında bir ayar olduğundan, uygulamayı oluşturduğunuzda veya içeri aktardığınızda utterance normalleştirmesi tanımlanmıştır. Söylenişi normalleştirme ayarları varsayılan olarak kapalıdır. 

Aksanlar, metin içindeki işaretler veya işaretlerdir, örneğin: 

```
İ ı Ş Ğ ş ğ ö ü
```

Uygulamanız üzerinde normalleştirmeyi kapatırsa, **Test** bölmesi, toplu iş testleri ve uç nokta sorguları, vurgu veya noktalama kullanan tüm söyler için değişecektir.

`settings` parametresindeki lusıs JSON uygulama dosyanıza aksanlar veya noktalama işaretleri için söylenişi normalleştirmesini açın.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

**Noktalama işareti** , modelleriniz eğitilen ve uç nokta sorgularınız tahmin etmeden önce, noktalama işaretlerinden kaldırılacak şekilde görünür. 

**Aksanların** normalleştirilmesi, karakterleri normal karakterlerle birlikte gelen aksan işaretleri ile değiştirir. Örneğin: `Je parle français` `Je parle francais`olur. 

Normalleştirme, örnek ifade veya tahmin yanıtlarınızda noktalama ve aksanlar görmeyecek ve yalnızca eğitim ve tahmin sırasında yoksayıladıklarından emin değildir.


### <a name="punctuation-marks"></a>Noktalama işaretleri

Noktalama, LUSıS 'de ayrı bir belirteçtir. Uçta nokta içermeyen bir nokta ile sonunda bir nokta içeren bir söylenişi iki ayrı tanüler ve iki farklı tahmin elde edebilir. 

Noktalama işareti normalleştirilmezse, bazı istemci uygulamalar bu işaretlere anlam yerleştirebilir, varsayılan olarak, Lu, noktalama işaretlerini yoksayar. Her iki stilin de aynı göreli puanları döndürmesi için, örnek uttlarınızın hem noktalama işaretlerini hem de noktalama işaretlerini kullantığınızdan emin olun. 

Modelin noktalama [işaretlerini (noktalama](luis-concept-utterance.md) işaretleri içeren ve olmayan) veya özel sözdizimi ile noktalama işaretlerini gözardı etmek daha kolay olan [desenlerdeki](luis-concept-patterns.md) noktalama işaretlerini işlediği emin olun: `I am applying for the {Job} position[.]`

Noktalama, istemci uygulamanızda belirli bir anlamı yoksa, noktalama işaretlerini normalleştirerek [noktalama işaretlerini yok saymayı](#utterance-normalization) düşünün. 

### <a name="ignoring-words-and-punctuation"></a>Sözcükler ve noktalama işaretleri yoksayılıyor

Desenlerde belirli sözcükleri veya noktalama işaretlerini yoksaymak isterseniz, `[]`köşeli ayraçın _Yoksay_ sözdizimini içeren bir [desen](luis-concept-patterns.md#pattern-syntax) kullanın. 

## <a name="training-utterances"></a>Eğitim konuşmaları

Eğitim genellikle belirleyici değildir: söylenişi tahmini sürümler veya uygulamalar arasında biraz farklılık gösterebilir. [Sürüm ayarları](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API 'sini, tüm eğitim verilerini kullanmak üzere `UseAllTrainingData` ad/değer çiftiyle güncelleştirerek, belirleyici olmayan eğitimi kaldırabilirsiniz.

## <a name="testing-utterances"></a>Söyleyceler test etme 

Geliştiriciler, bir [tahmin uç noktası](luis-how-to-azure-subscription.md) URL 'sine utser göndererek, lusıs uygulamasının gerçek trafikle test edilmesine başlamamalıdır. Bu [Söyleyime, gözden geçirme](luis-how-to-review-endpoint-utterances.md)ve varlıkların performansını geliştirmek için kullanılır. LUSıS Web sitesi test bölmesi ile gönderilen testler, uç nokta aracılığıyla gönderilmez ve bu nedenle etkin öğrenimine katkıda bulunun. 

## <a name="review-utterances"></a>Detersliği gözden geçirme

Modelinize eğitilen, yayımladım ve [uç nokta](luis-glossary.md#endpoint) sorgularını aldıktan sonra, Luo tarafından önerilen noktaları [gözden geçirin](luis-how-to-review-endpoint-utterances.md) . LUO, amaç veya varlık için düşük puanları olan uç nokta dıklarını seçer. 

## <a name="best-practices"></a>En iyi uygulamalar

[En iyi uygulamaları](luis-concept-best-practices.md) gözden geçirin ve bunları düzenli yazma döngünüzün bir parçası olarak uygulayın.

## <a name="label-for-word-meaning"></a>Sözcük anlamı etiketi

Sözcük seçimi veya sözcük düzenlemesi aynıysa, ancak aynı şeyi içermiyorsa, varlıkla etiketlemeyin. 

Aşağıdaki söyleyenlerdeki sözcük `fair` bir hograf. Aynı şekilde yazılmış ancak farklı bir anlamı vardır:

|İfade|
|--|
|Bu yaz Seattle alanında ne tür bir ilçe FAIRS oluyor?|
|Seattle incelemesi için geçerli derecelendirme mi?|

Tüm olay verilerini bulmak için bir olay varlığı istediyseniz, sözcüğü ilk utterde `fair` etiketleyin, ancak ikinciden değil.


## <a name="next-steps"></a>Sonraki adımlar
Kullanıcı araslarını anlamak için bir LUO uygulamasını eğitme hakkında daha fazla bilgi için bkz. [örnek ekleme](luis-how-to-add-example-utterances.md) .

