---
title: Yanlış yazılmış kelimeleri Düzelt-LUSıS
titleSuffix: Azure Cognitive Services
description: Lug uç nokta sorgularını Bing Yazım Denetimi API'si v7 ekleyerek, yazılanlara yanlış yazılan sözcükleri düzeltin.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: 509d1dc0b94bdfa9be5185df0bad793f7702eb26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101731043"
---
# <a name="correct-misspelled-words-with-bing-resource"></a>Yanlış yazılmış kelimeleri Bing kaynağıyla düzeltin

V3 tahmin API 'si artık [Bing yazım denetımı API](/bing/search-apis/bing-spell-check/overview)'sini destekliyor. İsteklerinizin üst bilgisinde Bing arama kaynağına anahtar ekleyerek uygulamanıza yazım denetimi ekleyin. Zaten bir tane varsa var olan bir Bing kaynağını kullanabilir veya bu özelliği kullanmak için [Yeni bir tane oluşturabilirsiniz](https://portal.azure.com/#create/Microsoft.BingSearch) . 

Yanlış yazılmış bir sorgu için tahmin çıkış örneği:

```json
{
  "query": "bouk me a fliht to kayro",
  "prediction": {
    "alteredQuery": "book me a flight to cairo",
    "topIntent": "book a flight",
    "intents": {
      "book a flight": {
        "score": 0.9480589
      }
      "None": {
        "score": 0.0332136229
      }
    },
    "entities": {}
  }
}
```

, Azın Kullanıcı söylenişi tahminiyle önce yazım için düzeltmeler yapılır. Yanıtta yazım denetimi de dahil olmak üzere, özgün yazıdaki tüm değişiklikleri görebilirsiniz.

## <a name="create-bing-search-resource"></a>Bing Arama kaynağı oluşturma

Azure portal Bing Arama kaynak oluşturmak için aşağıdaki yönergeleri izleyin:

1. [Azure Portal](https://portal.azure.com)oturum açın.

2. Sol üst köşedeki **kaynak oluştur** ' u seçin.

3. Arama kutusuna `Bing Search V7` hizmeti girin ve hizmeti seçin.

4. Yasal bildirim dahil olmak üzere bilgiler içeren bir bilgi paneli görüntülenir. Abonelik oluşturma işlemini başlatmak için **Oluştur** ' u seçin.

> [!div class="mx-imgBorder"]
> ![Bing Yazım Denetimi API'si v7 kaynağı](./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png)

5. Sonraki panelde hizmet ayarlarınızı girin. Hizmet oluşturma işleminin bitmesini bekleyin.

6. Kaynak oluşturulduktan sonra, sol taraftaki **anahtarlar ve uç nokta** dikey penceresine gidin. 

7. Tahmin isteğiniz üstbilgisine eklenecek anahtarlardan birini kopyalayın. Yalnızca iki anahtardan birine ihtiyacınız olacak.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="enable-spell-check-from-ui"></a>Kullanıcı arabiriminden yazım denetimini etkinleştir 
[LUA portalını](https://www.luis.ai)kullanarak örnek sorgunuz için SpellCheck 'i etkinleştirebilirsiniz. Ekranın üst kısmında **Yönet** ' i ve sol gezinti bölmesinde **Azure kaynakları** ' nı seçin. Bir tahmin kaynağını uygulamanızla ilişkilendirdikten sonra, sayfanın altından **sorgu parametrelerini değiştir** ' i seçebilir ve **Yazım denetimini etkinleştir** alanına kaynak anahtarını yapıştırabilirsiniz.
    
   > [!div class="mx-imgBorder"]
   > ![Yazım denetimini etkinleştir](./media/luis-tutorial-bing-spellcheck/spellcheck-query-params.png)


## <a name="adding-the-key-to-the-endpoint-url"></a>Uç nokta URL 'sine anahtar ekleniyor
Yazım denetimi uygulamak istediğiniz her sorgu için, uç nokta sorgusunun sorgu üst bilgisi parametresinde bulunan Bing yazım denetimi kaynak anahtarına ihtiyacı vardır. Luya çağıran bir sohbet botu olabilir veya doğrudan LUO Endpoint API 'sini çağırabilirsiniz. Uç noktanın nasıl çağrıldığına bakılmaksızın, her bir çağrı, yazım düzeltmelerinin düzgün şekilde çalışması için üstbilginin isteğine gerekli bilgileri içermelidir. Değeri, **Mkt-Bing-yazım denetimi-anahtarı** ile anahtar değerine ayarlamanız gerekir.

|Üst bilgi anahtarı|Üst bilgi değeri|
|--|--|
|`mkt-bing-spell-check-key`|Kaynağınızın **anahtarlar ve uç nokta** dikey penceresinde bulunan anahtarlar|

## <a name="send-misspelled-utterance-to-luis"></a>Yanlış yazılmış söylenişi 'i lusıs 'e gönder
1. Tahmin sorgusunda, "dağın ne kadar uzakta?" gibi gönderilecek bir söylenişi ekleyin. Ingilizce olarak,, `mountain` ile `n` doğru bir yazıdır.

2. Lua, için bir JSON sonucuyla yanıt verir `How far is the mountain?` . Bing Yazım Denetimi API'si v7 yanlış bir sorun algılarsa, `query` lusıs UYGULAMASıNıN JSON yanıtında bulunan alan özgün sorguyu içerir ve `alteredQuery` alan, Luo 'ya gönderilen düzeltilen sorguyu içerir.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Yazım hatalarını yoksay

Bing Arama API v7 hizmetini kullanmak istemiyorsanız doğru ve yanlış yazım denetimi eklemeniz gerekir.

İki çözüm vardır:

* Her türlü farklı yazımlar içeren, LUTO 'ın doğru yazım ve yazım hataları öğrenmeleri için etiketlenmesi gereken her türlü etiket örneği. Bu seçenek, yazım denetleyicisi kullanmaktan daha fazla etiketleme çabası gerektirir.
* Sözcüğün tüm çeşitlemelerine sahip bir tümcecik listesi oluşturun. Bu çözümle, örnek çeşitlerdeki sözcük çeşitlemelerini etiketlemenize gerek yoktur.


> [!div class="nextstepaction"]
> [Örnek araslar hakkında daha fazla bilgi edinin](./luis-how-to-add-entities.md)