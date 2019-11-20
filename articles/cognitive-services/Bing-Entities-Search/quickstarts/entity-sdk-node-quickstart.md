---
title: 'Hızlı Başlangıç: Node. js için Bing Varlık Arama SDK ile bir arama isteği gönderme'
titleSuffix: Azure Cognitive Services
description: Node. js için Bing Varlık Arama SDK ile varlıkları aramak için bu hızlı başlangıcı kullanın
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 40c9062dba5eb3bbed6ee90bfdb0a74c1d6c11d5
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479025"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Hızlı Başlangıç: Node. js için Bing Varlık Arama SDK ile bir arama isteği gönderme

Node. js için Bing Varlık Arama SDK ile varlıkları aramaya başlamak için bu hızlı başlangıcı kullanın. Bing Varlık Arama birçok programlama dili ile uyumlu bir REST API sahip olsa da SDK, hizmeti uygulamalarınızla tümleştirmenin kolay bir yolunu sunar. Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js)' da bulunabilir.

## <a name="prerequisites"></a>Önkoşullar

* [Node.js](https://nodejs.org/en/download/)’in en son sürümü.

* [Node. js için Bing varlık arama SDK 'sı](https://www.npmjs.com/package/azure-cognitiveservices-entitysearch)

Bing Varlık Arama SDK 'sını yüklemek için:

1. Geliştirme `npm install ms-rest-azure` ortamınızda çalıştırın.
2. Geliştirme `npm install azure-cognitiveservices-entitysearch` ortamınızda çalıştırın.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Uygulamayı oluşturma ve başlatma

1. En sevdiğiniz IDE veya düzenleyicide yeni bir JavaScript dosyası oluşturun ve aşağıdaki gereksinimleri ekleyin. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');
    ```

2. Abonelik anahtarınızı `CognitiveServicesCredentials` kullanarak bir örnek oluşturun. Ardından, bununla birlikte arama istemcisinin bir örneğini oluşturun.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>İstek gönderme ve yanıt alma

1. İle `entitiesOperations.search()`bir varlık arama isteği gönderin. Bir yanıt aldıktan sonra, döndürülen sonuç sayısını `queryContext`ve ilk sonucun açıklamasını yazdırın.
      
    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Tek sayfalı web uygulaması oluşturma](../tutorial-bing-entities-search-single-page-app.md)

* [Bing Varlık Arama API'si nedir?](../overview.md )