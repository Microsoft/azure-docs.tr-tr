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
ms.openlocfilehash: cbabfc1ae0c54c4ef20d3c689506e486bf4b6a66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94386500"
---
Bu kıvrımlı tabanlı hızlı başlangıç, bilgi tabanınızdan yanıt alma konusunda size yol gösterir.

## <a name="prerequisites"></a>Önkoşullar

* En son [**kıvır**](https://curl.haxx.se/).
* Sahip olmanız gerekir
    * Bir [soru-cevap oluşturma hizmeti](../How-To/set-up-qnamaker-service-azure.md)
    * Önceki [hızlı](../Quickstarts/add-question-metadata-portal.md)başlangıçta, meta veriler ve tek BT sohbeti ile yapılandırılan, sorular ve yanıtlarla ilgili eğitim ve yanıt verilen bir bilgi tabanı.

> [!NOTE]
> Bilgi tabanınızdan bir soruya yanıt oluşturmaya hazırsanız, bilgi bankasını [eğitmeniz](../Quickstarts/create-publish-knowledge-base.md#save-and-train) ve [yayımlamanız](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) gerekir. Bilgi Bankası 'niz yayımlandığında **Yayımla** sayfası, yanıt oluşturmak için http isteği ayarlarını görüntüler. **Kıvrımlı** sekmesi, komut satırı aracından bir yanıt oluşturmak için gereken ayarları gösterir.

## <a name="use-metadata-to-filter-answer"></a>Yanıtı filtrelemek için meta verileri kullanma

Meta verilere dayalı bir yanıt için önceki hızlı bir şekilde sorgu Bilgi Bankası 'nı kullanın.

1. Bilgi Bankası 'nın **Ayarlar** sayfasında, bilgi bankasından bir yanıt oluşturmak için kullanılan örnek bir kıvrımlı komutu görmek için **kıvrımlı** sekmesini seçin.
1. Komutu düzenleyebilmeniz için komutu düzenlenebilir bir ortama (örneğin, bir metin dosyası) kopyalayın. Meta verilerinin `service:qna_maker` QnA çiftleri için bir filtre olarak kullanılması için soru değerini aşağıdaki gibi düzenleyin.

   # <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)
    
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H   "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```
    ---

    Bu soru, `size` Iki QNA çiftinin birini döndürebilen yalnızca tek bir sözcüktür. `strictFilters`Dizi, yanıta yalnızca yanıtları azaltmasını söyler `qna_maker` .

1. Yanıt yalnızca filtre ölçütlerini karşılayan yanıtı içerir. Aşağıdaki kıvrımlı yanıtı okunabilirlik için biçimlendirildi:

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

```json
Debug: {Enable:true}
```

1. Daha fazla bilgi görmek için, hata ayıklama özelliğini içerecek şekilde kıvrımlı komutunu düzenleyin.

   # <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)
    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```
    ---

1. Yanıt, Yanıtla ilgili bilgileri içerir. Aşağıdaki JSON çıkışında bazı hata ayıklama ayrıntıları, kısaltma için üç nokta ile değiştirilmiştir.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) to learn how to collaborate on a knowledge base.",
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

Özelliği bir Boole değeridir.

```json
isTest:true
```

Kıvrımlı komutu şöyle görünür:
# <a name="qna-maker-ga"></a>[Soru-Cevap Oluşturma GA](#tab/v1)
```bash
curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```
# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)
```bash
curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```

---
JSON yanıtı yayımlanan Bilgi Bankası sorgusuyla aynı şemayı kullanır.

> [!NOTE]
> Test ve yayımlanan bilgi tabanı tam olarak aynı ise, test dizini kaynaktaki tüm bilgi tabanları arasında paylaşıldığından bazı hafif değişimler de olabilir.

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Bir GIT-chat yanıtı sorgulamak için kıvrımlı kullanın

1. Kıvrımlı özellikli terminalde, kullanıcıdan, soru gibi bir bot konuşma bitiş ekstresi kullanın `Thank you` . Ayarlanacak başka hiçbir özellik yok.
    
   # <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```
    ---

1. Kıvrımlı komutunu çalıştırın ve puan ve yanıt dahil olmak üzere JSON yanıtını alın.

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

1. `threshold` `size` %80 veya daha iyi bir eşikle yanıt vermesini istemek için özelliğini ekleyin. Sorunun puanı %71 olduğundan Bilgi Bankası bu yanıtı bulamamalıdır. Sonuç, Bilgi Bankası oluşturduğunuzda verdiğiniz varsayılan yanıtı döndürür.

   # <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)
    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```
    ---

1. Kıvrımlı komutunu çalıştırın ve JSON yanıtını alın.

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
    
   # <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)
    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
    ```
    ---

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