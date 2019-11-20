---
title: Bilgi Bankası-Soru-Cevap Oluşturma
titleSuffix: Azure Cognitive Services
description: Soru-Cevap Oluşturma Bilgi Bankası, her QnA çiftiyle ilişkili bir soru-cevap (QnA) çiftleri ve isteğe bağlı meta veri kümesi içerir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 355556e98300ecad6aa3141f0f4ab14b834cd91e
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794893"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Soru-Cevap Oluşturma Bilgi Bankası nedir?

Soru-Cevap Oluşturma Bilgi Bankası, her QnA çiftiyle ilişkili bir soru-cevap (QnA) çiftleri ve isteğe bağlı meta veri kümesi içerir.

## <a name="key-knowledge-base-concepts"></a>Önemli Bilgi Bankası kavramları

* **Sorular**: bir soru, Kullanıcı sorgusunu en iyi şekilde temsil eden metni içerir. 
* **Yanıtlar**: bir yanıt, bir Kullanıcı sorgusu ilişkili soru ile eşleştiğinde döndürülen yanıttır.  
* **Meta**veri: meta veriler bir QNA çiftiyle ilişkili etiketlerdir ve anahtar-değer çiftleri olarak temsil edilir. Meta veri etiketleri, QnA çiftlerini filtrelemek ve sorgu eşleştirmesinin gerçekleştirileceği kümeyi sınırlamak için kullanılır.

Sayısal bir QnA ID tarafından temsil edilen tek bir QnA, tek bir yanıta eşlenen bir soru (alternatif sorular) için birden çok çeşitle sahiptir. Buna ek olarak, her bir çiftin ilişkili birden fazla meta veri alanı olabilir: bir anahtar ve bir değer.

![Bilgi temellerini Soru-Cevap Oluşturma](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Bilgi Bankası içerik biçimi

Bilgi Bankası 'nda zengin içerik aldığınızda, Soru-Cevap Oluşturma içeriği markına dönüştürmeye çalışır. Çoğu sohbet istemcisi tarafından anlaşılabilen markın biçimleri hakkında bilgi edinmek için [Bu blogu](https://aka.ms/qnamaker-docs-markdown-support) okuyun.

Meta veri alanları, ürün: Shredder gibi iki noktayla ayrılmış anahtar-değer çiftlerinden oluşur. Hem anahtar hem de değer salt metin olmalıdır. Meta veri anahtarı boşluk içermemelidir. Meta veriler anahtar başına yalnızca bir değeri destekler.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Soru-Cevap Oluşturma en iyi yanıtı seçmek için Kullanıcı sorgusunu nasıl işler?

Eğitilen ve [yayınlanan](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) soru-cevap oluşturma Bilgi Bankası, bir bot veya başka bir Istemci uygulamasından [generateanswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)'sindeki bir Kullanıcı sorgusu alır. Aşağıdaki diyagramda, Kullanıcı sorgusu alındığında işlem gösterilmektedir.

![Bir Kullanıcı sorgusu için derecelendirme işlemi](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Ranker işlemi

İşlem aşağıdaki tabloda açıklanmıştır.

|Adım|Amaç|
|--|--|
|1|İstemci uygulaması, Kullanıcı sorgusunu [Generateanswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)'sine gönderir.|
|2|Soru-Cevap Oluşturma dil algılama, yazım ve sözcük ayırıcılarını kullanarak Kullanıcı sorgusunu önceden işler.|
|3|Bu ön işleme, en iyi arama sonuçları için Kullanıcı sorgusunu değiştirmek üzere alınır.|
|4|Bu değiştirilen sorgu bir Azure Bilişsel Arama dizinine gönderilir ve bu da sonuçların `top` sayısını alır. Bu sonuçlarda doğru yanıt yoksa `top` değerini biraz artırın. Genellikle, `top` için 10 değeri sorguların %90 ' de işe yarar.|
|5|Soru-Cevap Oluşturma, Kullanıcı sorgusunun getirilen arama sonuçlarının doğruluğunu belirlemede gelişmiş bir özellik uygular. |
|6|Eğitimli derecelendiricisini modeli, Azure bilişsel arama sonuçlarını derecelendirmek için 5. adımdaki Özellik Puanını kullanır.|
|7|Yeni sonuçlar, istemci uygulamasına derecelendirilir sırada döndürülür.|
|||

Kullanılan özellikler arasında şunlar yer alır, ancak sözcük düzeyi semantikleri, bir Corpus içindeki terim düzeyi önem derecesi ve derin öğrenilen anlam modelleri, iki metin dizesi arasında benzerlik ve ilgi belirleme açısından sınırlı değildir.

## <a name="http-request-and-response-with-endpoint"></a>Uç nokta ile HTTP isteği ve yanıtı
Bilgi bankanızı yayımladığınızda, hizmet, uygulama ile tümleştirilen bir sohbet bot ile tümleştirilebilen REST tabanlı bir HTTP uç noktası oluşturur. 

### <a name="the-user-query-request-to-generate-an-answer"></a>Bir yanıt oluşturmak için Kullanıcı sorgulama isteği

Kullanıcı sorgusu, son kullanıcının Bilgi Bankası 'nda `How do I add a collaborator to my app?`gibi sorduğu sorudır. Sorgu genellikle doğal bir dil biçiminde veya soruyu temsil eden birkaç anahtar kelimedir, örneğin `help with collaborators`. Sorgu, istemci uygulamanızdaki bir HTTP isteğinden bilgi tabanınızdan gönderilir.

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

[Scorethreshold](./confidence-score.md#choose-a-score-threshold), [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)ve [strictfilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)gibi özellikleri ayarlayarak yanıtı kontrol edersiniz.

Doğru ve nihai yanıtı bulmak için konuşmayı, soruları ve yanıtları belirginleştirebilmek için [Çoklu açma işleviyle](../how-to/multiturn-conversation.md) birlikte [konuşma bağlamını](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) kullanın.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Yanıt oluşturmak için bir çağrıdan yanıt

HTTP yanıtı, belirli bir Kullanıcı sorgusuna en iyi eşleşme temelinde Bilgi Bankası 'ndan alınan yanıttır. Yanıt, yanıtı ve tahmin Puanını içerir. `top` özelliği ile birden fazla en iyi yanıt sorulursa, her biri puanı olan birden fazla en iyi yanıt alırsınız. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

### <a name="test-and-production-knowledge-base"></a>Test ve üretim Bilgi Bankası
Bilgi Bankası, Soru-Cevap Oluşturma ile oluşturulan, tutulan ve kullanılan soruların ve yanıtların deposudur. Her Soru-Cevap Oluşturma katmanı, birden fazla bilgi tabanı için kullanılabilir.

Bilgi Bankası 'nda iki durum vardır: *Test* ve *yayımlandı*.

*Test bilgi tabanı* , doğruluk ve yanıtların eksiksiz olması için düzenlenen, kaydedilen ve sınanan sürümdür. Test Bilgi Bankası 'nda yapılan değişiklikler uygulamanızın son kullanıcısını veya sohbet bot 'ı etkilemez. Test bilgi tabanı, HTTP isteğinde `test` olarak bilinir. 

*Yayımlanan bilgi tabanı* , sohbet bot veya uygulamanızda kullanılan sürümdür. Bilgi Bankası yayımlama eylemi, bilgi bankasındaki yayımlanmış sürüme test bilgi tabanı içeriğini koyar. Yayımlanan bilgi tabanı uygulamanın uç nokta aracılığıyla kullandığı sürüm olduğundan, içeriğin doğru ve iyi test edildiğinden emin olun. Yayımlanan Bilgi Bankası, HTTP isteğinde `prod` olarak bilinir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bilgi bankasındaki geliştirme yaşam döngüsü](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Ayrıca bkz.

[Soru-Cevap Oluşturma’ya genel bakış](../Overview/overview.md)

İle bir Bilgi Bankası oluşturun ve düzenleyin: 
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Şunu kullanarak bir yanıt oluşturun: 
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
