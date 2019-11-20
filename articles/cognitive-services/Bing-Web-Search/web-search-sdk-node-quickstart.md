---
title: "Hızlı başlangıç: Node.js için Bing Web Araması SDK'sını kullanma"
titleSuffix: Azure Cognitive Services
description: Bing Web Araması SDK'sı, Bing Web Araması özelliklerini Node.js uygulamanızla tümleştirmeyi kolaylaştırır. Bu hızlı başlangıçta istemci başlatmayı, istek göndermeyi ve yanıtı yazdırmayı öğreneceksiniz.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 79f0630c65aa438ac5d831b03842cc74b609cd3b
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132743"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-nodejs"></a>Hızlı başlangıç: Node.js için Bing Web Araması SDK'sını kullanma

Bing Web Araması SDK'sı, Bing Web Araması özelliklerini Node.js uygulamanızla tümleştirmeyi kolaylaştırır. Bu hızlı başlangıçta istemci başlatmayı, istek göndermeyi ve yanıtı yazdırmayı öğreneceksiniz.

Kodu hemen görmek istiyor musunuz? GitHub'daki [Node.js için Bing Web Araması SDK'sı örneklerini](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) inceleyebilirsiniz.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

Ayrıca bkz: [Bilişsel hizmetler fiyatlandırması - Bing arama API'si](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="prerequisites"></a>Önkoşullar
Bu hızlı başlangıcı çalıştırmak için aşağıdakilere ihtiyacınız olacaktır:

* [Node.js 6](https://nodejs.org/en/download/) veya üzeri
* Abonelik anahtarı  

## <a name="set-up-your-development-environment"></a>Geliştirme ortamınızı kurma

Node.js projemiz için geliştirme ortamını ayarlayarak başlayalım.

1. Projeniz için yeni bir dizin oluşturun:

    ```console
    mkdir YOUR_PROJECT
    ```

1. Yeni bir paket dosyası oluşturun:

    ```console
    cd YOUR_PROJECT
    npm init
    ```

1. Şimdi birkaç Azure modülü yükleyip `package.json` içine ekleyelim:

    ```console
    npm install --save azure-cognitiveservices-websearch
    npm install --save ms-rest-azure
    ```

## <a name="create-a-project-and-declare-required-modules"></a>Bir proje oluşturun ve gerekli modülleri bildirin

`package.json` ile aynı dizinde favori IDE ortamınızı veya düzenleyiciyi kullanarak yeni bir Node.js projesi oluşturun. Örneğin: `sample.js`.

Şimdi bu kodu projenize kopyalayın. Önceki bölümde yüklenen modülleri yükler.

```javascript
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>İstemciyi başlatma

Bu kod `azure-cognitiveservices-websearch` modünü kullanarak bir istemci başlatır. Devam etmeden önce Azure hesabınız için geçerli bir abonelik anahtarı girdiğinizden emin olun.

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>İstekte bulunma ve sonuçları yazdırma

İstemciyi kullanarak Bing Web Araması'na bir arama sorgusu gönderin. Yanıt `properties` dizisindeki öğeler için sonuç içerirse konsolda `result.value` yazılır.

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>Programı çalıştırma

Son adım programınızı çalıştırmaktır!

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu projeyi tamamladıktan sonra abonelik anahtarınızı program kodundan kaldırmayı unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Cognitive Services Node.js SDK'sı örnekleri](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

## <a name="see-also"></a>Ayrıca bkz.

* [Azure Node SDK başvurusu](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-websearch/)
