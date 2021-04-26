---
title: Anomali algılayıcı Python çok değişkenli istemci kitaplığı hızlı başlangıç
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: b0eba6d0be1d6a65b911f05dabf3cdbecc9c666d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880088"
---
Python için anomali algılayıcısının çok sayıda istemci kitaplığını kullanmaya başlayın. Hizmet tarafından sunulan algoritmaları kullanarak paketi başlatmak için bu adımları izleyin. Yeni multivarianomali algılama API 'Leri, Machine Learning bilgisine veya etiketli verilere gerek kalmadan, gelişmiş AI 'nin ölçüm gruplarından aykırları tespit etmek üzere kolayca tümleştirilmesine olanak tanır. Farklı sinyaller arasındaki bağımlılıklar ve eş düzeyli ilişkiler otomatik olarak anahtar faktörleri olarak sayılır. Bu, karmaşık sistemlerinizi hatalara karşı korumanıza yardımcı olur.

Python için anomali algılayıcı çok değişkenli istemci kitaplığını kullanarak şunları yapın:

* Bir zaman serisi grubundan sistem düzeyi bozuklulıkları algılayın.
* Herhangi bir bireysel zaman serisi size çok bilgi vermez ve bir sorunu tespit etmek için tüm sinyallere bakmanız gerekir.
* Sistem durumunun çeşitli yönlerini ölçen, yüzlerce fiziksel varlık için onlarca yüzlerce farklı algılayıcı türü ile tahmine dayalı bakım.

[Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Paket (Pypı)](https://pypi.org/project/azure-ai-anomalydetector/3.0.0b3/)  |  [Örnek kod](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/samples/sample_multivariate_detect.py)  |  [Jupyter Notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb)

## <a name="prerequisites"></a>Önkoşullar

* [Python 3.x](https://www.python.org/)
* [Pandas veri analizi kitaplığı](https://pandas.pydata.org/)
* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* Azure aboneliğiniz olduktan sonra <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" "  target="_blank"> </a> anahtar ve uç noktanıza ulaşmak için Azure Portal bir anomali algılayıcı kaynağı oluşturun. Dağıtım için bekleyin ve **Kaynağa Git** düğmesine tıklayın.
    * Uygulamanızı anomali algılayıcı API 'sine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.


## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-python-application"></a>Yeni bir Python uygulaması oluşturma

 Yeni bir Python dosyası oluşturun ve aşağıdaki kitaplıkları içeri aktarın.

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

Anahtarınız için bir ortam değişkeni, zaman serisi veri dosyası yolu ve aboneliğinizin Azure konumu olarak değişkenler oluşturun. Örneğin, `westus2`.

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

Python yükledikten sonra, ile istemci kitaplığını yükleyebilirsiniz:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Python için anomali algılayıcı istemci kitaplığı ile aşağıdakilerin nasıl yapılacağını göstermektedir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Modeli eğitme](#train-the-model)
* [Anormallikleri Algıla](#detect-anomalies)
* [Modeli dışarı aktar](#export-model)
* [Modeli Sil](#delete-model)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Yeni bir anomali algılayıcı istemcisi oluşturmak için anomali algılayıcısı abonelik anahtarını ve ilişkili uç noktayı geçirmeniz gerekir. Ayrıca bir veri kaynağı da oluşturacağız.  

Anomali algılayıcı çok değişkenli API 'Leri kullanmak için, algılamayı kullanmadan önce kendi modelimizi eğitmemiz gerekir. Eğitim için kullanılan veriler, her zaman serisinin iki sütunlu, zaman damgası ve değer içeren CSV biçiminde olması gereken bir zaman dizisidir. Tüm zaman serileri tek bir zip dosyasında sıkıştırılmalıdır ve [Azure Blob depolama](../../../../storage/blobs/storage-blobs-introduction.md#blobs)alanına yüklenmelidir. Varsayılan olarak, zaman serisi değişkenini temsil etmek için dosya adı kullanılacaktır. Alternatif olarak, değişkenin adını. zip dosya adından farklı olacak şekilde isterseniz, dosyanın bir ek meta.jsZIP dosyasına dahil edilebilir. [BLOB SAS (paylaşılan erişim imzaları) URL 'si](../../../../storage/common/storage-sas-overview.md)oluşturduğumuzdan, eğitim için zip dosyasının URL 'sini kullanabiliriz.

```python
def __init__(self, subscription_key, anomaly_detector_endpoint, data_source=None):
    self.sub_key = subscription_key
    self.end_point = anomaly_detector_endpoint

    # Create an Anomaly Detector client

    # <client>
    self.ad_client = AnomalyDetectorClient(AzureKeyCredential(self.sub_key), self.end_point)
    # </client>

    if not data_source:
        # Datafeed for test only
        self.data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS"
    else:
        self.data_source = data_source
```

## <a name="train-the-model"></a>Modeli eğitme

İlk olarak modeli eğtireceğiz, eğitimin ne zaman tamamlandığını saptamak için eğitim sırasında modelin durumunu kontrol edeceğiz ve sonra algılama aşamasına geçtiğimiz zaman ihtiyaç duyduğumuz en son model KIMLIĞINI alın.

```python
def train(self, start_time, end_time, max_tryout=500):

    # Number of models available now
    model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models before training.".format(len(model_list)))
    
    # Use sample data to train the model
    print("Training new model...")
    data_feed = ModelInfo(start_time=start_time, end_time=end_time, source=self.data_source)
    response_header = \
    self.ad_client.train_multivariate_model(data_feed, cls=lambda *args: [args[i] for i in range(len(args))])[-1]
    trained_model_id = response_header['Location'].split("/")[-1]
    
    # Model list after training
    new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    
    # Wait until the model is ready. It usually takes several minutes
    model_status = None
    tryout_count = 0
    while (tryout_count < max_tryout and model_status != "READY"):
        model_status = self.ad_client.get_multivariate_model(trained_model_id).additional_properties["summary"][
            "status"]
        tryout_count += 1
        time.sleep(2)
    
    assert model_status == "READY"
    
    print("Done.", "\n--------------------")
    print("{:d} available models after training.".format(len(new_model_list)))
    
    # Return the latest model id
    return trained_model_id

```

## <a name="detect-anomalies"></a>Anormallikleri Algıla

`detect_anomaly` `get_dectection_result` Veri kaynağınız içinde herhangi bir sorun olup olmadığını öğrenmek için ve kullanın. Az önce eğitilen modelin model KIMLIĞINI geçirmeniz gerekecektir.

```python
def detect(self, model_id, start_time, end_time, max_tryout=500):
    
    # Detect anomaly in the same data source (but a different interval)
    try:
        detection_req = DetectionRequest(source=self.data_source, start_time=start_time, end_time=end_time)
        response_header = self.ad_client.detect_anomaly(model_id, detection_req,
                                                        cls=lambda *args: [args[i] for i in range(len(args))])[-1]
        result_id = response_header['Location'].split("/")[-1]
    
        # Get results (may need a few seconds)
        r = self.ad_client.get_detection_result(result_id)
        tryout_count = 0
        while r.summary.status != "READY" and tryout_count < max_tryout:
            time.sleep(1)
            r = self.ad_client.get_detection_result(result_id)
            tryout_count += 1
    
        if r.summary.status != "READY":
            print("Request timeout after %d tryouts.".format(max_tryout))
            return None
    
    except HttpResponseError as e:
        print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
    except Exception as e:
        raise e
    
    return r
```

## <a name="export-model"></a>Modeli dışarı aktar

Bir model kullanımını dışa aktarmak `export_model` ve dışarı aktarmak istediğiniz modelin model kimliğini geçirmek istiyorsanız:

```python
def export_model(self, model_id, model_path="model.zip"):

    # Export the model
    model_stream_generator = self.ad_client.export_model(model_id)
    with open(model_path, "wb") as f_obj:
        while True:
            try:
                f_obj.write(next(model_stream_generator))
            except StopIteration:
                break
            except Exception as e:
                raise e
```

## <a name="delete-model"></a>Modeli Sil

Bir model kullanımını silmek `delete_multivariate_model` ve silmek istediğiniz modelin model kimliğini geçirmek için:

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı çalıştırmadan önce, yeni oluşturulan işlevlerimizi çağırmak için bazı kodlar eklememiz gerekiyor.

```python
if __name__ == '__main__':
    subscription_key = "ANOMALY_DETECTOR_KEY"
    anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"

    # Create a new sample and client
    sample = MultivariateSample(subscription_key, anomaly_detector_endpoint, data_source=None)

    # Train a new model
    model_id = sample.train(datetime(2021, 1, 1, 0, 0, 0), datetime(2021, 1, 2, 12, 0, 0))

    # Reference
    result = sample.detect(model_id, datetime(2021, 1, 2, 12, 0, 0), datetime(2021, 1, 3, 0, 0, 0))
    print("Result ID:\t", result.result_id)
    print("Result summary:\t", result.summary)
    print("Result length:\t", len(result.results))

    # Export model
    sample.export_model(model_id, "model.zip")

    # Delete model
    sample.delete_model(model_id)

```

Bu uygulamayı çalıştırmadan önce, bu hızlı başlangıç 'nin türetildiği [tam örnek koda](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb) göre projenizi denetlemek yararlı olabilir.

Başlamanıza yardımcı olmak için [ayrıntılı bir Jupyter Notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb) de sunuyoruz.

Uygulamayı `python` komutuyla ve dosya adınızla çalıştırın.


[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
