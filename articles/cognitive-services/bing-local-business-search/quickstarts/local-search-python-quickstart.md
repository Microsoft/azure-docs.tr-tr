---
title: Hızlı başlangıç-Python 'da API 'ye sorgu gönderme-Bing yerel Iş arama
titleSuffix: Azure Cognitive Services
description: Python 'da Bing yerel Iş Arama API 'sini kullanmaya başlamak için bu hızlı başlangıcı kullanın.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.date: 05/12/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: adbf3d9abddf01ba67046cfa433ffd46f713ff83
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536665"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Hızlı başlangıç: Python 'da Bing yerel Iş Arama API 'sine bir sorgu gönderme

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Azure bilişsel hizmeti olan Bing yerel Iş Arama API 'sine istek gönderme hakkında bilgi edinmek için bu hızlı başlangıcı kullanın. Bu basit uygulama Python 'da yazılmış olsa da, API, HTTP istekleri yapma ve JSON 'u ayrıştırma yeteneğine sahip olan herhangi bir programlama diliyle uyumlu olan bir yeniden takip eden Web hizmetidir.

Bu örnek uygulama, bir arama sorgusu için API 'den gelen yerel yanıt verilerini alır.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/) 2. x veya 3. x.
* Azure aboneliğiniz olduktan sonra, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Bing Arama kaynağı oluşturun Bing arama bir kaynak oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.

## <a name="run-the-complete-application"></a>Uygulamanın tamamını Çalıştır

Aşağıdaki örnekte, aşağıdaki adımlarda uygulanan yerelleştirilmiş sonuçlar alınır:
1. Ana bilgisayar ve yol ile uç noktayı belirtmek için değişkenleri bildirme.
2. Sorgu parametresini belirtin. 
3. İsteği oluşturan ve üstbilgiyi ekleyen arama işlevini tanımlayın `Ocp-Apim-Subscription-Key` .
4. Üstbilgiyi ayarlayın `Ocp-Apim-Subscription-Key` . 
5. Bağlantıyı yapın ve isteği gönderin.
6. JSON sonuçlarını yazdırma.

Bu demo için kodun tamamı aşağıdaki gibidir:

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>Sonraki adımlar
- [Yerel Iş arama Java hızlı başlangıç](local-search-java-quickstart.md)
- [Yerel Iş arama C# hızlı başlangıç](local-quickstart.md)
- [Yerel Iş arama Node.js hızlı başlangıç](local-search-node-quickstart.md)
