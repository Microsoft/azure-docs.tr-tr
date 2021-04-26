---
title: Anomali algılayıcı çok değişkenli JavaScript istemci kitaplığı hızlı başlangıç
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 656270c80e8da0ece83bb04190fa7e5710a0203e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879444"
---
JavaScript için anomali algılayıcısının çok sayıda istemci kitaplığını kullanmaya başlayın. Paketi yüklemek ve hizmet tarafından sunulan algoritmaları kullanmaya başlamak için bu adımları izleyin. Yeni multivarianomali algılama API 'Leri, Machine Learning bilgisine veya etiketli verilere gerek kalmadan, gelişmiş AI 'nin ölçüm gruplarından aykırları tespit etmek üzere kolayca tümleştirilmesine olanak tanır. Farklı sinyaller arasındaki bağımlılıklar ve eş düzeyli ilişkiler otomatik olarak anahtar faktörleri olarak sayılır. Bu, karmaşık sistemlerinizi hatalara karşı korumanıza yardımcı olur.

JavaScript için anomali algılayıcısının çok değişkenli istemci kitaplığını kullanarak şunları yapın:

* Bir zaman serisi grubundan sistem düzeyi bozuklulıkları algılayın.
* Herhangi bir bireysel zaman serisi size çok bilgi vermez ve bir sorunu tespit etmek için tüm sinyallere bakmanız gerekir.
* Sistem durumunun çeşitli yönlerini ölçen, yüzlerce fiziksel varlık için onlarca yüzlerce farklı algılayıcı türü ile tahmine dayalı bakım.

[Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/anomalydetector/ai-anomaly-detector)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/ai-anomaly-detector)  |  [Örnek kod](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* [Node.js](https://nodejs.org/) geçerli sürümü
* Azure aboneliğiniz olduktan sonra <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" "  target="_blank"> </a> anahtar ve uç noktanıza ulaşmak için Azure Portal bir anomali algılayıcı kaynağı oluşturun. Dağıtım için bekleyin ve **Kaynağa Git** düğmesine tıklayın.
    * Uygulamanızı anomali algılayıcı API 'sine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-nodejs-application"></a>Yeni bir Node.js uygulaması oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin. 

```console
mkdir myapp && cd myapp
```

`npm init`Bir dosya ile bir düğüm uygulaması oluşturmak için komutunu çalıştırın `package.json` . 

```console
npm init
```

Adlı bir dosya oluşturun `index.js` ve şu kitaplıkları içeri aktarın: '
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

Kaynağınızın Azure uç noktası ve anahtarı için değişken oluşturun. Örnek veri dosyası için başka bir değişken oluşturun.

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Anomali algılayıcı çok değişkenli API 'Leri kullanmak için, algılamayı kullanmadan önce kendi modelimizi eğitmemiz gerekir. Eğitim için kullanılan veriler, her zaman serisinin iki sütunlu, zaman damgası ve değer içeren CSV biçiminde olması gereken bir zaman dizisidir. Tüm zaman serileri tek bir zip dosyasında sıkıştırılmalıdır ve [Azure Blob depolama](../../../../storage/blobs/storage-blobs-introduction.md)alanına yüklenmelidir. Varsayılan olarak, zaman serisi değişkenini temsil etmek için dosya adı kullanılacaktır. Alternatif olarak, değişkenin adını. zip dosya adından farklı olacak şekilde isterseniz, dosyanın bir ek meta.jsZIP dosyasına dahil edilebilir. [BLOB SAS (paylaşılan erişim imzaları) URL 'si](../../../../storage/common/storage-sas-overview.md)oluşturduğumuzdan, eğitim için zip dosyasının URL 'sini kullanabiliriz.

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

`ms-rest-azure`Ve `azure-ai-anomalydetector` NPM paketlerini yükler. Bu hızlı başlangıçta CSV ayrıştırma kitaplığı da kullanılır:

```console
npm install @azure/ai-anomaly-detector csv-parse
```

Uygulamanızın `package.json` dosyası bağımlılıklarla güncelleştirilir.

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Node.js için anomali algılayıcı istemci kitaplığı ile aşağıdakilerin nasıl yapılacağını göstermektedir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Modeli eğitme](#train-a-model)
* [Anormallikleri Algıla](#detect-anomalies)
* [Modeli dışarı aktar](#export-model)
* [Modeli Sil](#delete-model)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Uç noktanız `AnomalyDetectorClient` ve kimlik bilgilerinizle bir nesne oluşturun.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey));
```

## <a name="train-a-model"></a>Modeli eğitme

### <a name="construct-a-model-result"></a>Model sonucu oluşturma

İlk olarak bir model isteği oluşturuyoruz. Başlangıç ve bitiş zamanının veri kaynağınıza göre hizalanmasına dikkat edin.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>Yeni bir modeli eğitme

Model isteğinizi anomali algılayıcı istemci metoduna geçirmeniz gerekir `trainMultivariateModel` .

```javascript
console.log("Training a new model...")
const train_response = await client.trainMultivariateModel(Modelrequest)
const model_id = train_response.location?.split("/").pop() ?? ""
console.log("New model ID: " + model_id)
```

Modelinizin eğitiminin tamamlanıp tamamlanmamasını denetlemek için modelin durumunu izleyebilirsiniz:

```javascript
let model_response = await client.getMultivariateModel(model_id)
let model_status = model_response.modelInfo?.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    model_response = await client.getMultivariateModel(model_id)
    model_status = model_response.modelInfo?.status
}

console.log("TRAINING FINISHED.")
```

## <a name="detect-anomalies"></a>Anormallikleri Algıla

`detectAnomaly` `getDectectionResult` Veri kaynağınız dahilinde herhangi bir sorun olup olmadığını öğrenmek için ve işlevlerini kullanın.

```javascript
console.log("Start detecting...")
const detect_request = {
    source: data_source,
    startTime: new Date(2021,0,2,12,0,0),
    endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request)
const result_id = result_header.location?.split("/").pop() ?? ""
let result = await client.getDetectionResult(result_id)
let result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    result = await client.getDetectionResult(result_id)
    result_status = result.summary.status
}
```

## <a name="export-model"></a>Modeli dışarı aktar

Eğitilen modelinize dışarı aktarmak için `exportModel` işlevini kullanın.

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody?.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>Modeli Sil

Geçerli kaynak için kullanılabilir olan mevcut bir modeli silmek için `deleteMultivariateModel` işlevini kullanın.

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı çalıştırmadan önce, kodunuzun [tam örnek koda](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js) karşı denetlenmesi yararlı olabilir

Uygulamayı `node` hızlı başlangıç dosyanızdaki komutla çalıştırın.

```console
node index.js
```

## <a name="next-steps"></a>Sonraki adımlar

* [Anomali algılayıcısı çok değişen en iyi uygulamalar](../../concepts/best-practices-multivariate.md)
