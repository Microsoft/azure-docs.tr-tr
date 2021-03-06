---
title: 'Hızlı Başlangıç: Python ile arama gerçekleştirme - Bing Web Araması API’si'
titleSuffix: Azure Cognitive Services
description: Python kullanarak istekleri Bing Web Araması REST API göndermek ve bir JSON yanıtı almak için bu hızlı başlangıcı kullanın
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 5a9eff1ef012e66f661ee99852a8d82f117a45c4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350526"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Hızlı Başlangıç: Bing Web Araması API’sini çağırmak için Python kullanma  

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Web Araması API'si ilk çağrısını yapmak için bu hızlı başlangıcı kullanın. Bu Python uygulaması, API 'ye bir arama isteği gönderir ve JSON yanıtını gösterir. Bu uygulama Python 'da yazılmış olsa da, API birçok programlama dili ile uyumlu olan bir yenilenmiş Web hizmetidir.

Bu örnek [MyBinder](https://mybinder.org) üzerinde bir Jupyter notebook olarak çalıştırılır. Çalıştırmak için cildi Başlat rozetini seçin:

[![Bağlayıcısı](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Önkoşullar

* [Python 2. x veya 3. x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Değişkenleri tanımlama

1. `subscription_key` değerini Azure hesabınızdan geçerli bir abonelik anahtarı ile değiştirin.

   ```python
   subscription_key = "YOUR_ACCESS_KEY"
   assert subscription_key
   ```

2. Bing Web Araması API’si uç noktasını tanımlayın. Aşağıdaki kodda genel uç noktasını kullanabilir veya kaynağınız için Azure portal görüntülenmiş [özel alt etki alanı](../../../cognitive-services/cognitive-services-custom-subdomains.md) uç noktasını kullanabilirsiniz.

   ```python
   search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
   ```

3. İsteğe bağlı olarak, için değerini değiştirerek arama sorgusunu özelleştirin `search_term` .

   ```python
   search_term = "Azure Cognitive Services"
   ```

## <a name="make-a-request"></a>İstekte bulunma

Bu kod, `requests` Bing Web araması API'si çağırmak ve sonuçları JSON nesnesi olarak döndürmek için kitaplığı kullanır. API anahtarı `headers` dizininde, arama terimi ile sorgu parametreleri de `params` dizininde iletilir. 

Seçeneklerin ve parametrelerin tüm listesi için bkz. [Bing Web araması API'si v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Yanıtı biçimlendirme ve görüntüleme

`search_results`Nesnesi, arama sonuçlarını ve ilgili sorgular ve sayfalar olarak bu meta verileri içerir. Bu kodda yanıtı biçimlendirmek ve tarayıcınızda görüntülemek için `IPython.display` kitaplığı kullanılır.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"], v["name"], v["snippet"])
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>GitHub 'da örnek kod

Bu kodu yerel olarak çalıştırmak için [GitHub 'da bulunan tüm örneğe](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py)bakın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bing Web Araması API'si tek sayfalı uygulama öğreticisi](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]