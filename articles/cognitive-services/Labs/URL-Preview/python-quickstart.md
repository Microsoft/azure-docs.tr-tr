---
title: "Hızlı Başlangıç: Proje URL 'SI önizlemesi, Python"
titlesuffix: Azure Cognitive Services
description: Python ile URL Önizleme Projesini kullanmaya hızlıca başlamak için örnek betik.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: e9b145b0786b2ab59552e5dfda6f0db63677443e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706873"
---
# <a name="quickstart-url-preview-with-python"></a>Hızlı Başlangıç: Python ile URL önizlemesi

Aşağıdaki Python örneği SwiftKey Web sitesi için bir URL Önizlemesi oluşturur: https://swiftkey.com/en.

## <a name="prerequisites"></a>Önkoşullar

[Bilişsel Hizmetler Laboratuvarları](https://labs.cognitive.microsoft.com/en-us/project-answer-search) ücretsiz denemesi için erişim anahtarı alın

Bu örnekte Python 3.6 kullanılmıştır.

## <a name="code-scenario"></a>Kod senaryosu 

Aşağıdaki kod URL Önizlemesi oluşturur.
Aşağıdaki adımları izler:
1. Ana bilgisayar ve yol ile uç noktayı belirtmek için değişkenleri bildirme.
2. Önizleme için sorgu URL'sini belirtme ve sorgu parametresini ekleme.  
3. Sorgu parametresini ayarlama.
4. İsteği oluşturan ve *Ocp-Apim-Subscription-Key* üst bilgisini ekleyen Search işlevini tanımlama.
5. *Ocp-Apim-Subscription-Key* üst bilgisini ayarlama. 
6. Bağlantı kurma ve isteği gönderme.
7. JSON sonuçlarını yazdırma.

Bu tanıtımda kullanılan kodun tamamı aşağıda verilmiştir:

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
```
## <a name="next-steps"></a>Sonraki adımlar
- [C# hızlı başlangıcı](csharp.md)
- [Java hızlı başlangıcı](java-quickstart.md)
- [JavaScript hızlı başlangıcı](javascript.md)
- [Node URL hızlı başlangıcı](node-quickstart.md)
