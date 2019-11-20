---
title: 'Hızlı Başlangıç: Python kullanarak Bing Özel Arama uç noktanızı çağırma | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Python kullanarak Bing Özel Arama örneğinizi arama sonuçları istemeye başlamak için bu hızlı başlangıcı kullanın
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: 24416bc2259fdd35581699b11f599ea48e349d5a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564633"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Hızlı Başlangıç: Python kullanarak Bing Özel Arama uç noktanızı çağırma

Bing Özel Arama örneğinden arama sonuçları istemeye başlamak için bu hızlı başlangıcı kullanın. Bu uygulama Python 'da yazıldığı sırada, Bing Özel Arama API'si çoğu programlama dili ile uyumlu olan yeniden yazılmış bir Web hizmetidir. Bu örneğin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py)’da mevcuttur.

## <a name="prerequisites"></a>Önkoşullar

- Bir Bing Özel Arama örneği. Bkz [. hızlı başlangıç: Daha fazla bilgi için ilk](quick-start.md) Bing özel arama örneğinizi oluşturun.
- [Python](https://www.python.org/) 2. x veya 3. x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Uygulamayı oluşturma ve başlatma

1. En sevdiğiniz IDE veya düzenleyicide yeni bir Python dosyası oluşturun ve aşağıdaki içeri aktarma deyimlerini ekleyin. Abonelik anahtarınız, özel yapılandırma KIMLIĞINIZ ve bir arama terimi için değişkenler oluşturun. 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Arama isteği gönderme ve alma 

1. Arama teriminizi `q=` sorgu parametresine ekleyerek istek URL 'sini oluşturun ve arama örneğinizin özel yapılandırma kimliği ' ni olarak `customconfig=`yapın. parametreleri bir `&` karakterle ayırın. 

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. İsteği Bing Özel Arama örneğinize gönderin ve döndürülen arama sonuçlarını yazdırın.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel bir arama Web uygulaması oluşturma](./tutorials/custom-search-web-page.md)
