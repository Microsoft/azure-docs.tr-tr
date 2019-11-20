---
title: 'Hızlı başlangıç: REST API ve Node. js ile uzak görüntüyü analiz etme'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, Node. js ile Görüntü İşleme API'si kullanarak bir görüntüyü analiz edersiniz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18, seo-javascript-september2018, seo-javascript-october2019
ms.openlocfilehash: b00c9ccca3c07ad1cfb6ca1f949f1f2c8c33655d
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176486"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-with-nodejs"></a>Hızlı başlangıç: node. js ile Görüntü İşleme REST API kullanarak uzak görüntüyü çözümleme

Bu hızlı başlangıçta, Node. js ile Görüntü İşleme REST API kullanarak görsel özellikleri ayıklamak için uzaktan depolanan bir görüntüyü analiz edersiniz. [Görüntüyü çözümle](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) yöntemiyle, görüntü içeriğine göre görsel özellikleri ayıklayabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- [Node. js](https://nodejs.org) 4. x veya daha yeni bir sürümü yüklü olmalıdır.
- [NPM](https://www.npmjs.com/) 'nin yüklü olması gerekir.
- Görüntü İşleme için bir abonelik anahtarınız olması gerekir. Deneme bilişsel [Hizmetler](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)'den ücretsiz bir deneme anahtarı edinebilirsiniz. Ya da Görüntü İşleme abone olmak ve anahtarınızı almak için bilişsel [Hizmetler oluşturma](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ' daki yönergeleri izleyin. Ardından, sırasıyla `COMPUTER_VISION_SUBSCRIPTION_KEY` ve `COMPUTER_VISION_ENDPOINT` adlı anahtar ve hizmet uç noktası dizesi için [ortam değişkenleri oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) .

## <a name="create-and-run-the-sample"></a>Örnek oluşturma ve çalıştırma

Örneği oluşturmak ve çalıştırmak için aşağıdaki adımları uygulayın:

1. NPM [`request`](https://www.npmjs.com/package/request) paketini yükler.
   1. Yönetici olarak bir komut istemi penceresi açın.
   1. Şu komutu çalıştırın:

      ```console
      npm install request
      ```

   1. Paket başarıyla yüklendikten sonra, komut istemi penceresini kapatın.

1. Aşağıdaki kodu bir metin düzenleyicisine kopyalayın.
1. İsteğe bağlı olarak, `imageUrl` değerini, çözümlemek istediğiniz farklı bir görüntünün URL 'siyle değiştirin.
1. İsteğe bağlı olarak, `language` istek parametresinin değerini farklı bir dille değiştirin.
1. Kodu `.js` uzantılı bir dosya olarak kaydedin. Örneğin, `analyze-image.js`.
1. Bir komut istemi penceresi açın.
1. İstemde, dosyayı çalıştırmak için `node` komutunu kullanın. Örneğin, `node analyze-image.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v2.1/analyze';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

// Request parameters.
const params = {
    'visualFeatures': 'Categories,Description,Color',
    'details': '',
    'language': 'en'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="examine-the-response"></a>Yanıtı inceleyin

JSON 'da başarılı bir yanıt döndürülür. Örnek, aşağıdaki örneğe benzer şekilde, komut istemi penceresinde başarılı bir yanıtı ayrıştırır ve görüntüler:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Grey",
    "dominantColorBackground": "Green",
    "dominantColors": [
      "Grey",
      "Green"
    ],
    "accentColor": "4D5E2F",
    "isBwImg": false
  },
  "requestId": "81b4e400-e3c1-41f1-9020-e6871ad9f0ed",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında dosyayı silin ve NPM `request` paketini kaldırın. Paketi kaldırmak için aşağıdaki adımları uygulayın:

1. Yönetici olarak bir komut istemi penceresi açın.
2. Şu komutu çalıştırın:

   ```console
   npm uninstall request
   ```

3. Paket başarıyla kaldırıldıktan sonra, komut istemi penceresini kapatın.

## <a name="next-steps"></a>Sonraki adımlar

Bir görüntüyü analiz etmek, ünlüler ve köklikleri algılamak, küçük resim oluşturmak ve yazdırılmış ve el yazısı metin ayıklamak için kullanılan Görüntü İşleme API 'Leri keşfeder. Görüntü İşleme API'si hızlı bir şekilde denemek için, [Açık API test konsolunu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)deneyin.

> [!div class="nextstepaction"]
> [Görüntü İşleme API'si keşfet](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
