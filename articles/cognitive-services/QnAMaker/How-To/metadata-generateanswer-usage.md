---
title: GenerateAnswer API 'SI ile meta veriler-Soru-Cevap Oluşturma
titleSuffix: Azure Cognitive Services
description: Soru-Cevap Oluşturma, anahtar/değer çiftleri biçiminde meta verileri soru/yanıt çiftlerine eklemenizi sağlar. Sonuçları Kullanıcı sorgularıyla filtreleyebilir ve izleme konuşmalarında kullanılabilecek ek bilgileri saklayabilirsiniz.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 9d2100dbc2c5f24742a949778a1b7450bf303c5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232214"
---
# <a name="get-an-answer-with-the-generateanswer-api"></a>GenerateAnswer API 'SI ile bir yanıt alın

Kullanıcının sorusunun tahmin edilen yanıtını almak için GenerateAnswer API 'sini kullanın. Bir Bilgi Bankası yayımladığınızda, **Yayımlama** SAYFASıNDA bu API 'nin nasıl kullanılacağına ilişkin bilgileri görebilirsiniz. API 'yi, meta veri etiketlerine göre yanıtları filtrelemek için de yapılandırabilir ve test sorgu dizesi parametresiyle uç noktadan Bilgi Bankası ' nı test edebilirsiniz.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>GenerateAnswer API 'SI ile yanıt tahminlerini alın

Soru ve yanıt çiftleriyle en iyi eşleşmeyi elde etmek için, bot veya uygulamanızdaki [Generateanswer API](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) 'sini bir Kullanıcı sorusu ile sorgulamak için kullanırsınız.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>GenerateAnswer uç noktasını almak için Yayımla

Bilgi bankanızı [soru-cevap oluşturma portalından](https://www.qnamaker.ai)yayımladığınızda veya [API](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)'Yi kullanarak, generateanswer uç noktanızın ayrıntılarını alabilirsiniz.

Uç nokta ayrıntılarınızı almak için:
1. [https://www.qnamaker.ai](https://www.qnamaker.ai) adresinde oturum açın.
1. **Bilgi tabanlarım**' da bilgi tabanınız Için **kodu görüntüle** ' yi seçin.
    ![Bilgi temellerimin ekran görüntüsü](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. GenerateAnswer uç noktası ayrıntılarınızı alın.

    # <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

    ![Uç nokta ayrıntılarının ekran görüntüsü](../media/qnamaker-how-to-metadata-usage/view-code.png)

    # <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)

    ![Yönetilen uç nokta ayrıntılarının ekran görüntüsü](../media/qnamaker-how-to-metadata-usage/view-code-managed.png)

    ---

Ayrıca, bilgi Bankalarınızın **Ayarlar** sekmesinden uç nokta ayrıntılarınızı alabilirsiniz.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer istek yapılandırması

HTTP POST isteğiyle GenerateAnswer öğesini çağırın. GenerateAnswer çağrısının nasıl çağrılacağını gösteren örnek kod için bkz. [hızlı](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)başlangıçlara bakın.

POST isteği şunu kullanır:

* Gerekli [URI parametreleri](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Güvenlik için gerekli üst bilgi özelliği `Authorization`
* Gerekli [gövde özellikleri](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto).

GenerateAnswer URL 'SI aşağıdaki biçime sahiptir:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Öğesinin HTTP üst bilgisi özelliğini, `Authorization` `EndpointKey` sonundaki boşluk ve sonra **Ayarlar** sayfasında bulunan uç nokta anahtarı ile birlikte dizenin bir değeriyle ayarlamayı unutmayın.

Örnek bir JSON gövdesi şöyle görünür:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

[Rankertype](../concepts/best-practices.md#choosing-ranker-type)hakkında daha fazla bilgi edinin.

Önceki JSON yalnızca %30 ' da veya eşik puanı üzerinde olan yanıtları istedi.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer yanıt özellikleri

[Yanıt](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) , varsa yanıtı göstermek için ihtiyacınız olan tüm bilgileri ve bir sonraki konuşmayı IÇEREN bir JSON nesnesidir.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

Önceki JSON% 38,5 puanı ile yanıt verdi.

## <a name="match-questions-only-by-text"></a>Yalnızca soruları Eşleştir, metne göre

Varsayılan olarak, Soru-Cevap Oluşturma sorular ve yanıtlar arasında arama yapar. Yalnızca sorulardan arama yapmak istiyorsanız, yanıt oluşturmak için `RankerType=QuestionOnly` GenerateAnswer ISTEĞININ gönderi gövdesinde öğesini kullanın.

`isTest=false`Kullanarak, veya kullanarak, ile yayımlanan KB veya test KB içinde arama yapabilirsiniz `isTest=true` .

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}

```
## <a name="use-qna-maker-with-a-bot-in-c"></a>C 'de bir bot ile Soru-Cevap Oluşturma kullanma #

Bot Framework, [Getanswer API 'si](/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)ile soru-cevap oluşturma özelliklerine erişim sağlar:

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Önceki JSON yalnızca %30 ' da veya eşik puanı üzerinde olan yanıtları istedi.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Node.js bir bot ile Soru-Cevap Oluşturma kullanma

Bot Framework, [Getanswer API 'si](/javascript/api/botbuilder-ai/qnamaker?preserve-view=true&view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)ile soru-cevap oluşturma özelliklerine erişim sağlar:

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Önceki JSON yalnızca %30 ' da veya eşik puanı üzerinde olan yanıtları istedi.

## <a name="get-precise-answers-with-generateanswer-api"></a>GenerateAnswer API 'SI ile kesin yanıtlar alın

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

Yalnızca Soru-Cevap Oluşturma yönetilen sürümü ile kesin yanıt özelliği sunuyoruz.

# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)

Kullanıcı Soru-Cevap Oluşturma yönetilen kaynağı kullanırken [kesin yanıtları](../reference-precise-answering.md) etkinleştirebilir. AnswerSpanRequest parametresi aynı şekilde güncellenmelidir.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3,
    "answerSpanRequest": {
        "enable": true,
        "topAnswersWithSpan": 1
    }
}
```

Benzer şekilde, kullanıcılar answerSpanRequest parametresini ayarlamayana kesin yanıtları devre dışı bırakmayı tercih edebilir.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3
}
```
### <a name="bot-settings"></a>Bot ayarları

Bot hizmetiniz için kesin yanıt ayarlarını yapılandırmak istiyorsanız, bot için App Service kaynağına gidin. Ardından, aşağıdaki ayarı ekleyerek konfigürasyonları güncelleştirmeniz gerekir.

- Enableıiseanswer
- DisplayPreciseAnswerOnly

|Ekran yapılandırması|Enableıiseanswer|DisplayPreciseAnswerOnly|
|:--|--|--|
|Yalnızca kesin yanıtlar|true|true|
|Yalnızca uzun yanıtlar|yanlış|yanlış|
|Hem uzun hem de kesin yanıtlar|true|yanlış|

---

## <a name="common-http-errors"></a>Ortak HTTP hataları

|Kod|Açıklama|
|:--|--|
|2xx|Başarılı|
|400|İsteğin parametreleri yanlış, gerekli parametreler eksik, hatalı biçimlendirilmiş veya çok büyük|
|400|İsteğin gövdesi yanlış anlamı, JSON eksik, hatalı biçimlendirilmiş veya çok büyük|
|401|Geçersiz anahtar|
|403|Yasak-doğru izinleriniz yok|
|404|KB yok|
|410|Bu API kullanım dışı ve artık kullanılamıyor|

## <a name="next-steps"></a>Sonraki adımlar

**Yayımla** sayfası, Postman veya kıvrık [bir yanıt oluşturmak](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) için bilgi de sağlar.

> [!div class="nextstepaction"]
> [Bilgi bankanız hakkında analizler alma](../how-to/get-analytics-knowledge-base.md)
> [!div class="nextstepaction"]
> [Güvenirlik puanı](../Concepts/confidence-score.md)
