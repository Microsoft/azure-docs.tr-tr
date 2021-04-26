---
title: Anomali algılayıcı çok değişkenli Java istemci kitaplığı hızlı başlangıç
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: e85f54beb9a3d4203e527ed9c8ce5582b6a2f5b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879705"
---
Java için anomali algılayıcısının çok sayıda istemci kitaplığını kullanmaya başlayın. Hizmet tarafından sunulan algoritmaları kullanarak paketi başlatmak için bu adımları izleyin. Yeni multivarianomali algılama API 'Leri, Machine Learning bilgisine veya etiketli verilere gerek kalmadan, gelişmiş AI 'nin ölçüm gruplarından aykırları tespit etmek üzere kolayca tümleştirilmesine olanak tanır. Farklı sinyaller arasındaki bağımlılıklar ve eş düzeyli ilişkiler otomatik olarak anahtar faktörleri olarak sayılır. Bu, karmaşık sistemlerinizi hatalara karşı korumanıza yardımcı olur.

Java için anomali algılayıcısının çok değişkenli istemci kitaplığını kullanarak şunları yapın:

* Bir zaman serisi grubundan sistem düzeyi bozuklulıkları algılayın.
* Herhangi bir bireysel zaman serisi size çok bilgi vermez ve bir sorunu tespit etmek için tüm sinyallere bakmanız gerekir.
* Sistem durumunun çeşitli yönlerini ölçen, yüzlerce fiziksel varlık için onlarca yüzlerce farklı algılayıcı türü ile tahmine dayalı bakım.

[Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Paket (Maven)](https://repo1.maven.org/maven2/com/azure/azure-ai-anomalydetector/3.0.0-beta.2/)  |  [Örnek kod](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* [Java Development Kit 'in (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) geçerli sürümü
* [Gradle yapı aracı](https://gradle.org/install/)veya başka bir bağımlılık Yöneticisi.
* Azure aboneliğiniz olduktan sonra <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" "  target="_blank"> </a> anahtar ve uç noktanıza ulaşmak için Azure Portal bir anomali algılayıcı kaynağı oluşturun. Dağıtım için bekleyin ve **Kaynağa Git** düğmesine tıklayın.
    * Uygulamanızı anomali algılayıcı API 'sine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-gradle-project"></a>Yeni bir Gradle projesi oluşturma

Bu hızlı başlangıç, Gradle bağımlılık yöneticisini kullanır. [Maven merkezi deposunda](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor)daha fazla istemci kitaplığı bilgisi bulabilirsiniz.

Konsol penceresinde (cmd, PowerShell veya Bash gibi), uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin. 

```console
mkdir myapp && cd myapp
```

`gradle init`Çalışma dizininizden komutunu çalıştırın. Bu komut, uygulamanızı oluşturmak ve yapılandırmak için çalışma zamanında kullanılan *Build. Gradle. kts* de dahil olmak üzere Gradle için gerekli derleme dosyaları oluşturur.

```console
gradle init --type basic
```

Bir **DSL** seçmeniz Istendiğinde, **Kotlin**' ı seçin.

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

*Build. Gradle. kts* ' i bulun ve tercıh ettiğiniz IDE veya metin düzenleyicinizle açın. Sonra bu derleme yapılandırmasında kopyalayın. Proje bağımlılıklarını eklediğinizden emin olun.

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Java dosyası oluşturma

Örnek uygulamanız için bir klasör oluşturun. Çalışma dizininizden aşağıdaki komutu çalıştırın:

```console
mkdir -p src/main/java
```

Yeni klasöre gidin ve *Metricsadvisorquickbaşlangıç. Java* adlı bir dosya oluşturun. Bunu tercih ettiğiniz düzenleyicide veya IDE 'de açın ve aşağıdaki deyimleri ekleyin `import` :

```java
package com.azure.ai.anomalydetector;

import com.azure.ai.anomalydetector.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.*;
import com.azure.core.http.policy.*;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.PagedResponse;
import com.azure.core.http.rest.Response;
import com.azure.core.http.rest.StreamResponse;
import com.azure.core.util.Context;
import reactor.core.publisher.Flux;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.nio.ByteBuffer;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.util.Iterator;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;
```

Kaynağınızın Azure uç noktası ve anahtarı için değişken oluşturun. Örnek veri dosyası için başka bir değişken oluşturun.

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

 Anomali algılayıcı çok değişkenli API 'Leri kullanmak için, algılamayı kullanmadan önce kendi modelimizi eğitmemiz gerekir. Eğitim için kullanılan veriler, her zaman serisinin iki sütunlu, zaman damgası ve değer içeren CSV biçiminde olması gereken bir zaman dizisidir. Tüm zaman serileri tek bir zip dosyasında sıkıştırılmalıdır ve [Azure Blob depolama](../../../../storage/blobs/storage-blobs-introduction.md)alanına yüklenmelidir. Varsayılan olarak, zaman serisi değişkenini temsil etmek için dosya adı kullanılacaktır. Alternatif olarak, değişkenin adını. zip dosya adından farklı olacak şekilde isterseniz, dosyanın bir ek meta.jsZIP dosyasına dahil edilebilir. [BLOB SAS (paylaşılan erişim imzaları) URL 'si](../../../../storage/common/storage-sas-overview.md)oluşturduğumuzdan, eğitim için zip dosyasının URL 'sini kullanabiliriz.

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Node.js için anomali algılayıcı istemci kitaplığı ile aşağıdakilerin nasıl yapılacağını göstermektedir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Modeli eğitme](#train-a-model)
* [Anormallikleri Algıla](#detect-anomalies)
* [Modeli dışarı aktar](#export-model)
* [Modeli Sil](#delete-model)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Uç noktanız `anomalyDetectorClient` ve kimlik bilgilerinizle bir nesne oluşturun.

```java
HttpHeaders headers = new HttpHeaders()
    .put("Accept", ContentType.APPLICATION_JSON);

HttpPipelinePolicy authPolicy = new AzureKeyCredentialPolicy(key,
    new AzureKeyCredential(key));
AddHeadersPolicy addHeadersPolicy = new AddHeadersPolicy(headers);

HttpPipeline httpPipeline = new HttpPipelineBuilder().httpClient(HttpClient.createDefault())
    .policies(authPolicy, addHeadersPolicy).build();
// Instantiate a client that will be used to call the service.
HttpLogOptions httpLogOptions = new HttpLogOptions();
httpLogOptions.setLogLevel(HttpLogDetailLevel.BODY_AND_HEADERS);

AnomalyDetectorClient anomalyDetectorClient = new AnomalyDetectorClientBuilder()
    .pipeline(httpPipeline)
    .endpoint(endpoint)
    .httpLogOptions(httpLogOptions)
    .buildClient();
```

## <a name="train-a-model"></a>Modeli eğitme

### <a name="construct-a-model-result-and-train-model"></a>Model sonucu oluşturun ve modeli eğitme

İlk olarak bir model isteği oluşturuyoruz. Başlangıç ve bitiş zamanının veri kaynağınıza göre hizalanmasına dikkat edin.

 Anomali algılayıcı çok değişkenli API 'Leri kullanmak için, algılamayı kullanmadan önce kendi modelimizi eğitmemiz gerekir. Eğitim için kullanılan veriler, her zaman serisinin iki sütunlu, zaman damgası ve değer içeren CSV biçiminde olması gereken bir zaman dizisidir. Tüm zaman serileri tek bir zip dosyasında sıkıştırılmalıdır ve [Azure Blob depolama](../../../../storage/blobs/storage-blobs-introduction.md#blobs)alanına yüklenmelidir. Varsayılan olarak, zaman serisi değişkenini temsil etmek için dosya adı kullanılacaktır. Alternatif olarak, değişkenin adını. zip dosya adından farklı olacak şekilde isterseniz, dosyanın bir ek meta.jsZIP dosyasına dahil edilebilir. [BLOB SAS (paylaşılan erişim imzaları) URL 'si](../../../../storage/common/storage-sas-overview.md)oluşturduğumuzdan, eğitim için zip dosyasının URL 'sini kullanabiliriz.

```java
Path path = Paths.get("test-data.csv");
List<String> requestData = Files.readAllLines(path);
List<TimeSeriesPoint> series = requestData.stream()
    .map(line -> line.trim())
    .filter(line -> line.length() > 0)
    .map(line -> line.split(",", 2))
    .filter(splits -> splits.length == 2)
    .map(splits -> {
        TimeSeriesPoint timeSeriesPoint = new TimeSeriesPoint();
        timeSeriesPoint.setTimestamp(OffsetDateTime.parse(splits[0]));
        timeSeriesPoint.setValue(Float.parseFloat(splits[1]));
        return timeSeriesPoint;
    })
    .collect(Collectors.toList());

Integer window = 28;
AlignMode alignMode = AlignMode.OUTER;
FillNAMethod fillNAMethod = FillNAMethod.LINEAR;
Integer paddingValue = 0;
AlignPolicy alignPolicy = new AlignPolicy().setAlignMode(alignMode).setFillNAMethod(fillNAMethod).setPaddingValue(paddingValue);
String source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
OffsetDateTime startTime = OffsetDateTime.of(2021, 1, 2, 0, 0, 0, 0, ZoneOffset.UTC);
;
OffsetDateTime endTime = OffsetDateTime.of(2021, 1, 3, 0, 0, 0, 0, ZoneOffset.UTC);
;
String displayName = "Devops-MultiAD";

ModelInfo request = new ModelInfo().setSlidingWindow(window).setAlignPolicy(alignPolicy).setSource(source).setStartTime(startTime).setEndTime(endTime).setDisplayName(displayName);
TrainMultivariateModelResponse trainMultivariateModelResponse = anomalyDetectorClient.trainMultivariateModelWithResponse(request, Context.NONE);
String header = trainMultivariateModelResponse.getDeserializedHeaders().getLocation();
String[] model_ids = header.split("/");
UUID model_id = UUID.fromString(model_ids[model_ids.length - 1]);
System.out.println(model_id);

Integer skip = 0;
Integer top = 5;
PagedIterable<ModelSnapshot> response = anomalyDetectorClient.listMultivariateModel(skip, top);
Iterator<PagedResponse<ModelSnapshot>> ite = response.iterableByPage().iterator();

while (true) {
    Response<Model> response_model = anomalyDetectorClient.getMultivariateModelWithResponse(model_id, Context.NONE);
    UUID model = response_model.getValue().getModelId();
    System.out.println(response_model.getStatusCode());
    System.out.println(response_model.getValue().getModelInfo().getStatus());
    System.out.println(model);
    if (response_model.getValue().getModelInfo().getStatus() == ModelStatus.READY) {
        break;
    }
}
```

## <a name="detect-anomalies"></a>Anormallikleri Algıla

```java
DetectionRequest detectionRequest = new DetectionRequest().setSource(source).setStartTime(startTime).setEndTime(endTime);
DetectAnomalyResponse detectAnomalyResponse = anomalyDetectorClient.detectAnomalyWithResponse(model_id, detectionRequest, Context.NONE);
String result = detectAnomalyResponse.getDeserializedHeaders().getLocation();

String[] result_list = result.split("/");
UUID result_id = UUID.fromString(result_list[result_list.length - 1]);

while (true) {
    DetectionResult response_result = anomalyDetectorClient.getDetectionResult(result_id);
    if (response_result.getSummary().getStatus() == DetectionStatus.READY) {
        break;
    }
    else if(response_result.getSummary().getStatus() == DetectionStatus.FAILED){

    }
}
```

## <a name="export-model"></a>Modeli dışarı aktar

Eğitilen modelinizi dışarı aktarmak için kullanın `exportModelWithResponse` .

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>Modeli Sil

Geçerli kaynak için kullanılabilir olan mevcut bir modeli silmek için `deleteMultivariateModelWithResponse` işlevini kullanın.

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı ile oluşturabilirsiniz:

```console
gradle build
```
### <a name="run-the-application"></a>Uygulamayı çalıştırma

Çalıştırmadan önce, kodunuzu [tam örnek koda](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java)karşı denetlemek yararlı olabilir.

Uygulamayı `run` hedefle çalıştırın:

```console
gradle run
```

## <a name="next-steps"></a>Sonraki adımlar

* [Anomali algılayıcısı çok değişen en iyi uygulamalar](../../concepts/best-practices-multivariate.md)
