---
title: 'Hızlı başlangıç: Python için anomali algılayıcı istemci kitaplığını kullanarak veri anormalilerini algılama'
titleSuffix: Azure Cognitive Services
description: Veri serinizdeki tüm verileri toplu olarak veya akış verilerinde saptamak için anomali algılayıcı API 'sini kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: aahi
ms.openlocfilehash: 926d7043ea6f56c1ba6d13fd4586fc6f6d59cfa8
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554722"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Hızlı başlangıç: Python için anomali algılayıcı istemci kitaplığı

Python için anomali algılayıcı istemci kitaplığını kullanmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin. Anomali algılayıcı hizmeti, sektör, senaryo veya veri hacminin ne olursa olsun, üzerinde en iyi şekilde sığdırma modellerini kullanarak zaman serisi verilerinizde yer alan anormallikleri bulmanıza olanak sağlar.

Python için anomali algılayıcı istemci kitaplığını kullanarak şunları yapın:

* Toplu istek olarak zaman serisi veri kümeniz genelinde anomali algılama
* Zaman serinizdeki en son veri noktasının anomali durumunu Algıla

Kitaplık [başvuru belgeleri](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python)  | [kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector)  | [paketi (Pypı)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/)  | [kod örnekleri](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* [Python 3. x](https://www.python.org/)
* [Pandas veri analizi kitaplığı](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-an-anomaly-detector-resource"></a>Anomali algılayıcısı kaynağı oluşturma

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-python-application"></a>Yeni bir Python uygulaması oluşturma

 Yeni bir Python dosyası oluşturun ve aşağıdaki kitaplıkları içeri aktarın.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Anahtarınız için bir ortam değişkeni, zaman serisi veri dosyası yolu ve aboneliğinizin Azure konumu olarak değişkenler oluşturun. Örneğin, `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

Python yükledikten sonra, ile istemci kitaplığını yükleyebilirsiniz:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Nesne modeli

Anomali algılayıcı istemcisi, anahtarınızı kullanarak Azure 'da kimlik doğrulaması yapan bir [Anokidetectorclient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) nesnesidir. İstemci iki anomali algılama yöntemi sağlar: [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)ve [Last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-)kullanarak en son veri noktasındaki bir veri kümesinin tamamı üzerinde. 

Zaman serisi verileri, [istek](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) nesnesinde bir dizi [işaret](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) olarak gönderilir. @No__t_0 nesnesi, verileri (örneğin,[ayrıntı düzeyi](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) ) ve anomali algılama parametrelerini tanımlamaya yönelik özellikler içerir. 

Anomali algılayıcısı yanıtı, kullanılan yönteme bağlı olarak bir [Lastdetectresponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) veya [entiredetectresponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) nesnesidir. 

## <a name="code-examples"></a>Kod örnekleri 

Bu kod parçacıkları, Python için anomali algılayıcı istemci kitaplığı ile aşağıdakilerin nasıl yapılacağını göstermektedir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Bir dosyadan zaman serisi veri kümesi yükleme](#load-time-series-data-from-a-file)
* [Tüm veri kümesindeki anormallikleri Algıla](#detect-anomalies-in-the-entire-data-set) 
* [En son veri noktasının anomali durumunu Algıla](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Azure location değişkeninizi uç noktaya ekleyin ve anahtarınızla istemcinin kimliğini doğrulayın.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Bir dosyadan zaman serisi verilerini yükle

Bu hızlı başlangıçta [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)'dan örnek verileri indirin:
1. Tarayıcınızda, **RAW**' a sağ tıklayın.
2. **Bağlantıyı farklı kaydet**' e tıklayın.
3. Dosyayı uygulama dizininize bir. csv dosyası olarak kaydedin.

Bu zaman serisi verileri. csv dosyası olarak biçimlendirilir ve anomali algılayıcısı API 'sine gönderilir.

Veri dosyanızı Pandas kitaplığının `read_csv()` yöntemiyle yükleyin ve veri serinizi depolamak için boş bir liste değişkeni oluşturun. Dosya üzerinde yineleme yapın ve verileri bir [nokta](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) nesnesi olarak ekleyin. Bu nesne,. csv veri dosyanızdaki satırlardan zaman damgası ve sayısal değer içerir. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Zaman seriniz ve veri noktalarının [ayrıntı düzeyi](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (veya dönemsellik) Ile bir [istek](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) nesnesi oluşturun. Örneğin, `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Tüm veri kümesindeki anormallikleri Algıla 

İstemcinin [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) yöntemini kullanarak tüm zaman serisi verilerinin bozuklukilerini algılamak için API 'yi çağırın. Döndürülen [Entiredetectresponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) nesnesini depolayın. Yanıtın `is_anomaly` listesini yineleyin ve `true` değerlerinin dizinini yazdırın. Bu değerler, varsa anormal veri noktalarının dizinine karşılık gelir.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>En son veri noktasının anomali durumunu Algıla

En son veri noktanağınızın, istemcinin [last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) yöntemini kullanarak bir anomali olup olmadığını ve döndürülen [lastdetectresponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) nesnesini depolayıp Depomadığını öğrenmek IÇIN anomali algılayıcısı API 'sini çağırın. Yanıtın `is_anomaly` değeri, bu noktanın anomali durumunu belirten bir Boole değerdir.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı `python` komutuyla ve dosya adınızla çalıştırın.
 
## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, kaynak grubuyla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
>[Azure Databricks ile akış anomali algılama](../tutorials/anomaly-detection-streaming-databricks.md)

* [Anomali ALGıLAYıCı API nedir?](../overview.md)
* Anomali algılayıcı API 'sini kullanırken [en iyi uygulamalar](../concepts/anomaly-detection-best-practices.md) .
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)' da bulunabilir.
