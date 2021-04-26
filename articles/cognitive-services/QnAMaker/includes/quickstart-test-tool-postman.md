---
title: include dosyası
description: include dosyası
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 11/09/2020
ms.openlocfilehash: fa497b69b067d5556f11effdb52505895ecc3bdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94386623"
---
Bu Postman tabanlı hızlı başlangıç, bilgi tabanınızdan yanıt alma konusunda size yol gösterir.

## <a name="prerequisites"></a>Önkoşullar

* En son [**Postman**](https://www.getpostman.com/).
* Sahip olmanız gerekir
    * Bir [soru-cevap oluşturma hizmeti](../How-To/set-up-qnamaker-service-azure.md)
    * Hızlı başlangıçtan oluşturulan [sorular ve yanıtlar ile](../Quickstarts/add-question-metadata-portal.md) eğitilen ve yayınlanmış bir bilgi tabanı, meta veriler ve BT sohbeti ile yapılandırılır.

> [!NOTE]
> Bilgi tabanınızdan bir soruya yanıt oluşturmaya hazırsanız, bilgi bankasını [eğitmeniz](../Quickstarts/create-publish-knowledge-base.md#save-and-train) ve [yayımlamanız](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) gerekir. Bilgi Bankası 'niz yayımlandığında **Yayımla** sayfası, yanıt oluşturmak için http isteği ayarlarını görüntüler. **Postman** sekmesi, yanıt oluşturmak için gereken ayarları gösterir.

## <a name="set-up-postman-for-requests"></a>İstekler için Postman ayarla

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

Bu hızlı başlangıç, Postman **Post** isteği için aynı ayarları kullanır ve daha sonra, sorgulanılmaya çalıştığınız öğesine göre Service 'e gönderilen JSON gövde gönderisini yapılandırır.

Postman 'ı yapılandırmak için bu yordamı kullanın, sonra JSON gövdesini yapılandırmak için sonraki her bölümü okuyun.

1. Bilgi Bankası 'nın **Ayarlar** sayfasında, bilgi bankasından yanıt oluşturmak için kullanılan yapılandırmayı görmek Için **Postman** sekmesini seçin. Postman 'da kullanmak için aşağıdaki bilgileri kopyalayın.

    |Name|Ayar|Amaç ve değer|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Bu, URL için HTTP yöntemidir ve yoldur.|
    |`Host`|`https://YOUR-RESOURCE_NAME.azurewebsites.net/qnamaker`|Bu, URL 'nin ana bilgisayarı. Tüm generateAnswer URL 'sini almak için Konağı ve gönderi değerlerini birleştirin.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|Azure 'a isteğinizi yetkilendirmek için üst bilgi değeri. |
    |`Content-type`|`application/json`|İçeriğiniz için üst bilgi değeri.|
    ||`{"question":"<Your question>"}`|POST isteğinin gövdesi JSON nesnesi olarak. Bu değer, sorgunun ne işe hazırlandığına bağlı olarak, aşağıdaki her bir bölümde değişecektir.|

1. Postman 'ı açın ve yayımlanan Bilgi Bankası ayarlarınıza sahip yeni bir temel **gönderi** isteği oluşturun. Aşağıdaki bölümlerde, POSTAYı Bilgi Bankası ile değiştirmek için JSON gövdesini değiştirin.

# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)

Bu hızlı başlangıç, Postman **Post** isteği için aynı ayarları kullanır ve daha sonra, sorgulanılmaya çalıştığınız öğesine göre Service 'e gönderilen JSON gövde gönderisini yapılandırır.

Postman 'ı yapılandırmak için bu yordamı kullanın, sonra JSON gövdesini yapılandırmak için sonraki her bölümü okuyun.

1. Bilgi Bankası 'nın **Ayarlar** sayfasında, bilgi bankasından yanıt oluşturmak için kullanılan yapılandırmayı görmek Için **Postman** sekmesini seçin. Postman 'da kullanmak için aşağıdaki bilgileri kopyalayın.

    |Name|Ayar|Amaç ve değer|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Bu, URL için HTTP yöntemidir ve yoldur.|
    |`Host`|`https://YOUR-RESOURCE_NAME.cognitiveservices.azure.com/qnamaker`|Bu, URL 'nin ana bilgisayarı. Tüm generateAnswer URL 'sini almak için Konağı ve gönderi değerlerini birleştirin.|
    |`Ocp-Apim-Subscription-Key`|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|İsteğinizi yetkilendirmek için üst bilgi değeri. |
    |`Content-type`|`application/json`|İçeriğiniz için üst bilgi değeri.|
    ||`{"question":"<Your question>"}`|POST isteğinin gövdesi JSON nesnesi olarak. Bu değer, sorgunun ne işe hazırlandığına bağlı olarak, aşağıdaki her bir bölümde değişecektir.|

1. Postman 'ı açın ve yayımlanan Bilgi Bankası ayarlarınıza sahip yeni bir temel **gönderi** isteği oluşturun. Aşağıdaki bölümlerde, POSTAYı Bilgi Bankası ile değiştirmek için JSON gövdesini değiştirin.
---

## <a name="use-metadata-to-filter-answer"></a>Yanıtı filtrelemek için meta verileri kullanma

Önceki bir hızlı başlangıçta, iki farklı soruyu ayırt etmek için meta veriler iki QnA çiftlerine eklenmiştir. Filtreyi yalnızca ilgili QnA çiftiyle kısıtlamak için sorguya meta verileri ekleyin.

1. Postman 'da, ' `strictFilters` ın ad/değer çiftine sahip özelliğini ekleyerek yalnızca sorgu json ' ı değiştirin `service:qna_maker` . JSON gövdesi şu olmalıdır:

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    Bu soru, `size` iki soru ve yanıt çiftinin her birini döndürebilen tek bir sözcüktür. `strictFilters`Dizi, yanıta yalnızca yanıtları azaltmasını söyler `qna_maker` .

1. Yanıt yalnızca filtre ölçütlerini karşılayan yanıtı içerir.

    Aşağıdaki yanıt okunabilirlik için biçimlendirildi:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Arama terimini karşılamayan ancak filtreye uyan bir soru ve yanıt çifti varsa döndürülmüyor. Bunun yerine, genel yanıt `No good match found in KB.` döndürülür.

## <a name="use-debug-query-property"></a>Hata ayıklama sorgu özelliğini kullan

> [!NOTE]
>Herhangi bir bağımlılık için hata ayıklama özelliğini kullanmanızı önermiyoruz. Bu özellik, ürün ekibine sorun gidermeye yardımcı olmak için eklenmiştir.

Hata ayıklama bilgileri döndürülen yanıtın nasıl belirlendiğini anlamanıza yardımcı olur. Faydalı olsa da gerekli değildir. Hata ayıklama bilgileriyle bir yanıt oluşturmak için, özelliği ekleyin `debug` :

1. Postman 'da, özelliği ekleyerek yalnızca JSON gövdesini değiştirin `debug` . JSON şu olmalıdır:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. Yanıt, Yanıtla ilgili bilgileri içerir. Aşağıdaki JSON çıktısında, bazı hata ayıklama ayrıntıları üç nokta ile değiştirilmiştir.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>Sınama Bilgi Bankası 'nı kullanın

Sınama Bilgi Bankası 'ndan bir yanıt almak istiyorsanız `isTest` Body özelliğini kullanın.

Postman 'da, özelliği ekleyerek yalnızca JSON gövdesini değiştirin `isTest` . JSON şu olmalıdır:

```json
{
    'question':'size',
    'isTest': true
}
```

JSON yanıtı yayımlanan Bilgi Bankası sorgusuyla aynı şemayı kullanır.

> [!NOTE]
> Test ve yayımlanan bilgi tabanı tam olarak aynı ise, test dizini kaynaktaki tüm bilgi tabanları arasında paylaşıldığından bazı hafif değişimler de olabilir.

## <a name="query-for-a-chit-chat-answer"></a>Bir CHIT-chat yanıtı sorgusu

1. Postman 'da, kullanıcıdan yalnızca JSON gövdesini bir konuşma bitiş bildirimiyle değiştirin. JSON şu olmalıdır:

    ```json
    {
        'question':'thank you'
    }
    ```

1. Yanıt puanı ve yanıtı içerir.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    `Thank you` sorusu bir genel konuşma sorusuyla tam olarak eşleştiği için Soru-Cevap Oluşturma tam olarak emindir ve 100 puan döndürür. Soru-Cevap Oluşturma Ayrıca, tüm ilgili soruların yanı sıra CHIT-chat meta veri etiketi bilgilerini içeren meta veri özelliği de döndürülür.

## <a name="use-threshold-and-default-answer"></a>Eşik ve varsayılan yanıtı kullan

Yanıt için en az bir eşik isteyebilirsiniz. Eşik karşılanmazsa, varsayılan yanıt döndürülür.

1. Postman 'da, kullanıcıdan yalnızca JSON gövdesini bir konuşma bitiş bildirimiyle değiştirin. JSON şu olmalıdır:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    Sorunun puanı %71 olduğundan ve bunun yerine Bilgi Bankası oluşturduğunuzda verdiğiniz varsayılan yanıtı geri döndürtiğinden Bilgi Bankası bu yanıtı bulamaz.

    Puan ve yanıt dahil döndürülen JSON yanıtı:

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    Soru-Cevap Oluşturma bir puan döndürdü `0` , bu, güven anlamına gelir. Ayrıca varsayılan yanıtı da döndürür.

1. Eşik değerini %60 olarak değiştirip sorguyu yeniden isteyin:

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    Döndürülen JSON yanıtı buldu.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```