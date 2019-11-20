---
title: REST çağrısı olan modeli alC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: 8cefd8357893657d94959cb853004b34b0ec9d8d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503755"
---
## <a name="prerequisites"></a>Önkoşullar

* Başlangıç anahtarı.
* Bilişsel hizmetler-dil düzeyi GitHub deposundan [Travelagent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) uygulamasını içeri aktarın.
* Alınan TravelAgent uygulaması için LUSıS uygulama KIMLIĞI. Uygulama kimliği, uygulama panosunda gösterilir.
* Uygulama içindeki, uttersliği alan sürüm KIMLIĞI. Varsayılan kimlik: "0.1".
* [Python 3.6](https://www.python.org/downloads/) veya üzeri.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Örnek konuşmalar JSON dosyası

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="get-luis-key"></a>LUIS anahtarını alma

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]


## <a name="change-model-programmatically"></a>Modeli program aracılığıyla değiştirme

Uygulamaya makine tarafından öğrenilen bir varlık [API 'si](https://aka.ms/luis-apim-v3-authoring) eklemek için git ' i kullanın. 

1. `model.py` adlı yeni bir dosya oluşturun. Aşağıdaki kodu ekleyin:

    ```python
    ########### Python 3.6 #############
    import requests
    
    # Starter key
    LUIS_authoringKey  = "YOUR-KEY"
    
    LUIS_APP_ID = "YOUR-APP-ID"
    
    # Authoring endpoint, example: westus.api.cognitive.microsoft.com
    LUIS_ENDPOINT = "YOUR-ENDPOINT"

    # The version number of your LUIS app
    LUIS_APP_VERSION = "0.1"
    
    URI_AddUtterances = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_ID}/examples'
    URI_Train = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_ID}/train'
    
    HEADERS = {'Ocp-Apim-Subscription-Key': LUIS_authoringKey}
    
    def addUtterances():
        r = requests.post(URI_AddUtterances,headers=HEADERS)
        print(r.json())
    
    def train():
        r = requests.post(URI_Train,headers=HEADERS)
        print(r.json())
    
    def trainStatus():
        r = requests.get(URI_Train,headers=HEADERS)
        print(r.json())
    
    addUtterances()
    train()
    trainStatus()
    ```
1. Aşağıdaki değerleri değiştirin:

    * başlangıç anahtarınızla `YOUR-KEY`
    * uç noktanızla `YOUR-ENDPOINT`, örneğin, `westus2.api.cognitive.microsoft.com`
    * Uygulamanızın KIMLIĞIYLE `YOUR-APP-ID`

1. Dosyayı oluşturduğunuz dizindeki aynı dizinde bir komut istemiyle, dosyayı çalıştırmak için aşağıdaki komutu girin:

    ```console
    python model.py
    ```  

## <a name="luis-keys"></a>LUIS anahtarları

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıcı tamamladığınızda dosyayı dosya sisteminden silin. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bir uygulama için en iyi uygulamalar](../luis-concept-best-practices.md)