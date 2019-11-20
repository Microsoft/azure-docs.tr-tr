---
title: "Hızlı Başlangıç: Python SDK 'sını kullanarak Bing Özel Arama uç noktanızı çağırın | Microsoft Docs"
titleSuffix: Azure Cognitive Services
description: Python SDK 'sını kullanarak Bing Özel Arama örneğinizi arama sonuçları istemeye başlamak için bu hızlı başlangıcı kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: 7a9d0d6619878299768e1f17b4da3280ea1ec1fc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564609"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>Hızlı Başlangıç: Python SDK 'sını kullanarak Bing Özel Arama uç noktanızı çağırma 

Python SDK 'sını kullanarak Bing Özel Arama örneğinizden arama sonuçları istemeye başlamak için bu hızlı başlangıcı kullanın. Bing Özel Arama birçok programlama dili ile uyumlu bir REST API sahip olsa da Bing Özel Arama SDK, hizmeti uygulamalarınızla tümleştirmenin kolay bir yolunu sunar. Bu örneğe ilişkin kaynak kodu, [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) 'da ek hata işleme ve ek açıklamalarla bulunabilir.

## <a name="prerequisites"></a>Önkoşullar

- Bir Bing Özel Arama örneği. Bkz [. hızlı başlangıç: Daha fazla bilgi için ilk](quick-start.md) Bing özel arama örneğinizi oluşturun.
- Python [2. x veya 3. x](https://www.python.org/) 
- [Python için Bing özel arama SDK 'sı](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>Python SDK 'Yı yükler

Aşağıdaki komutla Bing Özel Arama SDK 'Yı yükler.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Yeni uygulama oluşturma

En sevdiğiniz düzenleyicide veya IDE 'de yeni bir Python dosyası oluşturun ve aşağıdaki içeri aktarmaları ekleyin.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Arama İstemcisi Oluşturma ve istek gönderme

1. Abonelik anahtarınız için bir değişken oluşturun.

    ```python
    subscription_key = 'your-subscription-key'
    ```

2. Abonelik anahtarıyla bir `CognitiveServicesCredentials` nesne `CustomSearchClient`kullanarak bir örneği oluşturun. 

    ```python
    client = CustomSearchClient(CognitiveServicesCredentials(subscription_key))
    ```

3. İle `client.custom_instance.search()`bir arama isteği gönderin. Arama teriminizi `query` parametreye ekleyin ve arama örneğinizi kullanmak için `custom_config` özel yapılandırma kimliğinize ayarlayın. **Üretim** sekmesine tıklayarak [Bing özel arama portalından](https://www.customsearch.ai/)kimliğiniz edinebilirsiniz.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Arama sonuçlarını görüntüleme

Herhangi bir Web sayfası arama sonucu bulunursa, birinciden birini alın ve adını, URL 'sini ve bulunan toplam Web sayfalarını yazdırın.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel bir arama Web uygulaması oluşturma](./tutorials/custom-search-web-page.md)
