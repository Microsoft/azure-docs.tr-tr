---
title: 'Hızlı başlangıç: anomali algılayıcısının REST API ve Java kullanarak zaman serisi verilerinizde bozukluklar algılama'
titleSuffix: Azure Cognitive Services
description: Veri serinizdeki tüm verileri toplu olarak veya akış verilerinde saptamak için anomali algılayıcı API 'sini kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: 6d54ec8df08e7c3d76a97c2531c21b1fd4d130b9
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554757"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Hızlı başlangıç: anomali algılayıcısının REST API ve Java kullanarak zaman serisi verilerinizde bozukluklar algılama

Bu hızlı başlangıcı kullanarak, zaman serisi verilerinizde bozukluklar olup olmadığı konusunda anomali algılayıcı API 'sinin iki algılama modunu kullanmaya başlayın. Bu Java uygulaması, JSON biçimli zaman serisi verilerini içeren iki API isteği gönderir ve yanıtları alır.

| API isteği                                        | Uygulama çıkışı                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Bir toplu iş olarak anomali algılama                        | Zaman serisi verilerinde her bir veri noktasının anomali durumunu (ve diğer verileri) ve algılanan tüm anormalilerin konumlarını içeren JSON yanıtı. |
| En son veri noktasının anomali durumunu Algıla | Zaman serisi verilerinde en son veri noktası için anomali durumunu (ve diğer verileri) içeren JSON yanıtı.                                                                                                                                         |

 Bu uygulama Java 'da yazıldığı sırada, API birçok programlama dili ile uyumlu olan bir yeniden sorun Web hizmetidir.

## <a name="prerequisites"></a>Önkoşullar

- [Java &trade; geliştirme seti (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) veya üzeri.

- Bu kitaplıkları Maven deposundan içeri aktar
    - [Java paketindeki JSON](https://mvnrepository.com/artifact/org.json/json)
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) paketi

- Zaman serisi veri noktalarını içeren bir JSON dosyası. Bu hızlı başlangıçta örnek veriler [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)'da bulunabilir.

### <a name="create-an-anomaly-detector-resource"></a>Anomali algılayıcısı kaynağı oluşturma

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Yeni uygulama oluşturma

1. Yeni bir Java projesi oluşturun ve aşağıdaki kitaplıkları içeri aktarın.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Abonelik anahtarınız ve uç noktanız için değişkenler oluşturun. Anomali algılama için kullanabileceğiniz URI 'Ler aşağıda verilmiştir. Bunlar, daha sonra API isteği URL 'Leri oluşturmak için hizmet uç noktanıza eklenecektir.

    |Algılama yöntemi  |KULLANıLMAMıŞSA  |
    |---------|---------|
    |Toplu iş algılama    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |En son veri noktasında algılama     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>İstek göndermek için bir işlev oluşturma

1. Yukarıda oluşturulan değişkenleri alan `sendRequest()` adlı yeni bir işlev oluşturun. Ardından aşağıdaki adımları gerçekleştirin.

2. API 'ye istek gönderebilen `CloseableHttpClient` nesnesi oluşturun. Uç noktanızı ve anomali algılayıcı URL 'sini birleştirerek isteği bir `HttpPost` istek nesnesine gönderin.

3. @No__t_1 üst bilgisini `application/json` olarak ayarlamak için isteğin `setHeader()` işlevini kullanın ve `Ocp-Apim-Subscription-Key` üstbilgisine abonelik anahtarınızı ekleyin.

4. Gönderilecek verilere isteğin `setEntity()` işlevini kullanın.

5. İsteği göndermek ve bir `CloseableHttpResponse` nesnesine kaydetmek için istemcinin `execute()` işlevini kullanın.

6. Yanıt içeriğini depolamak için `HttpEntity` nesnesi oluşturun. @No__t_0 içeriği alın. Yanıt boş değilse, döndürün.

[!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Bir toplu iş olarak anomali algılama

1. Veri genelinde bir toplu iş olarak bozukluklar algılamak için `detectAnomaliesBatch()` adlı bir yöntem oluşturun. Uç nokta, URL, abonelik anahtarı ve JSON verileriniz ile yukarıda oluşturulan `sendRequest()` yöntemini çağırın. Sonucu alın ve konsola yazdırın.

2. Yanıt `code` alanı içeriyorsa, hata kodunu ve hata iletisini yazdırın.

3. Aksi takdirde, veri kümesindeki anormalilerin konumlarını bulabilirsiniz. Yanıtın `isAnomaly` alanı, belirli bir veri noktasının bir anomali olup olmadığı ile ilgili bir Boole değeri içerir. JSON dizisini alın ve `true` değerlerinin dizinini yazdırarak bu diziyi yineleyin. Bu değerler, varsa anormal veri noktalarının dizinine karşılık gelir.

[!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>En son veri noktasının anomali durumunu Algıla

* Veri kümesindeki son veri noktasının anomali durumunu algılamak için `detectAnomaliesLatest()` adlı bir yöntem oluşturun. Uç nokta, URL, abonelik anahtarı ve JSON verileriniz ile yukarıda oluşturulan `sendRequest()` yöntemini çağırın. Sonucu alın ve konsola yazdırın.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Zaman serisi verilerinizi yükleyin ve isteği gönderin

1. Uygulamanızın ana yönteminde, isteklere eklenecek verileri içeren JSON dosyasını okuyun.

2. Yukarıda oluşturulan iki anomali algılama işlevini çağırın.

[!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Örnek yanıt

JSON biçiminde başarılı bir yanıt döndürülür. GitHub 'da JSON yanıtını görüntülemek için aşağıdaki bağlantılara tıklayın:
* [Örnek toplu iş algılama yanıtı](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Örnek en son nokta algılama yanıtı](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
>[Azure Databricks ile akış anomali algılama](../tutorials/anomaly-detection-streaming-databricks.md)

* [Anomali ALGıLAYıCı API nedir?](../overview.md)
* Anomali algılayıcı API 'sini kullanırken [en iyi uygulamalar](../concepts/anomaly-detection-best-practices.md) .
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)' da bulunabilir.
