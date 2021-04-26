---
title: Web hizmeti olarak dağıtılan model için istemci oluştur
titleSuffix: Azure Machine Learning
description: Azure Machine Learning bir model dağıtıldığında oluşturulan bir Web hizmeti uç noktasını çağırmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/12/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-csharp
ms.openlocfilehash: 8cab9331cdd4c15ab76b7c33f956be15ec2259ef
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861202"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Web hizmeti olarak dağıtılan bir Azure Machine Learning modelini kullanma


Web hizmeti olarak Azure Machine Learning modeli dağıtma işlemi bir REST API uç noktası oluşturur. Bu uç noktaya veri gönderebilir ve model tarafından döndürülen tahmini alabilirsiniz. Bu belgede, C#, Go, Java ve Python kullanarak Web hizmeti için istemci oluşturma hakkında bilgi edinin.

Yerel ortamınıza bir model dağıtırken, Azure Container Instances, Azure Kubernetes hizmeti veya alanı programlanabilir kapı dizileri (FPGA) için bir Web hizmeti oluşturursunuz. Web hizmetine erişmek için kullanılan URI 'yi [Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro)kullanarak alırsınız. Kimlik doğrulaması etkinleştirilirse, kimlik doğrulama anahtarlarını veya belirteçlerini almak için SDK 'Yı da kullanabilirsiniz.

Machine Learning Web hizmeti kullanan bir istemci oluşturmak için genel iş akışı:

1. Bağlantı bilgilerini almak için SDK 'Yı kullanın.
1. Model tarafından kullanılan istek verilerinin türünü belirleme.
1. Web hizmetini çağıran bir uygulama oluşturun.

> [!TIP]
> Bu belgedeki örnekler, Openapı (Swagger) belirtimlerinin kullanımı olmadan el ile oluşturulur. Dağıtımınız için bir Openapı belirtimini etkinleştirdiyseniz, hizmetinize yönelik istemci kitaplıkları oluşturmak için [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen) gibi araçları kullanabilirsiniz.

## <a name="connection-information"></a>Bağlantı bilgileri

> [!NOTE]
> Web hizmeti bilgilerini almak için Azure Machine Learning SDK 'sını kullanın. Bu bir Python SDK 'sına sahiptir. Hizmet için bir istemci oluşturmak üzere herhangi bir dili kullanabilirsiniz.

[Azureml. Core. WebService](/python/api/azureml-core/azureml.core.webservice%28class%29) sınıfı, istemci oluşturmak için gereken bilgileri sağlar. Aşağıdaki `Webservice` Özellikler bir istemci uygulaması oluşturmak için yararlıdır:

* `auth_enabled` -Anahtar kimlik doğrulaması etkinse `True` ; Aksi takdirde, `False` .
* `token_auth_enabled` -Belirteç kimlik doğrulaması etkinse `True` ; Aksi takdirde, `False` .
* `scoring_uri` -REST API adresi.
* `swagger_uri` -Openapı belirtiminin adresi. Otomatik şema oluşturmayı etkinleştirdiyseniz, bu URI kullanılabilir. Daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md).

Dağıtılan Web Hizmetleri için bu bilgileri almanın birkaç yolu vardır:

# <a name="python"></a>[Python](#tab/python)

* Bir modeli dağıtırken, `Webservice` hizmet hakkındaki bilgilerle bir nesne döndürülür:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* `Webservice.list`Çalışma alanınızdaki modeller için dağıtılan Web Hizmetleri listesini almak için kullanabilirsiniz. Döndürülen bilgi listesini daraltmak için filtre ekleyebilirsiniz. Ne filtrelenebilir hakkında daha fazla bilgi için, bkz. [WebService. List](/python/api/azureml-core/azureml.core.webservice.webservice.webservice) başvuru belgeleri.

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Dağıtılan hizmetin adını biliyorsanız, ' nin yeni bir örneğini oluşturabilir `Webservice` ve çalışma alanını ve hizmet adını parametreler olarak sağlayabilirsiniz. Yeni nesne dağıtılan hizmetle ilgili bilgiler içerir.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Dağıtılan hizmetin adını biliyorsanız [az ml Service Show](/cli/azure/ml/service#az_ml_service_show) komutunu kullanın:

```azurecli
az ml service show -n <service-name>
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure Machine Learning Studio 'dan __uç noktalar__, __gerçek zamanlı uç noktalar__ ve sonra uç nokta adı ' nı seçin. Uç nokta için Ayrıntılar bölümünde, __REST uç noktası__ alanı Puanlama URI 'sini içerir. __Swagger URI__ 'SI Swagger URI 'sini içerir.

---

Aşağıdaki tabloda bu URI 'Lerin nasıl göründüğünü gösterilmektedir:

| URI türü | Örnek |
| ----- | ----- |
| Puanlama URI 'SI | `http://104.214.29.152:80/api/v1/service/<service-name>/score` |
| Swagger URI 'SI | `http://104.214.29.152/api/v1/service/<service-name>/swagger.json` |

> [!TIP]
> Dağıtımınız için IP adresi farklı olacaktır. Her bir AKS kümesi, bu kümeye dağıtımlar tarafından paylaşılan kendi IP adresine sahip olacaktır.

### <a name="secured-web-service"></a>Güvenli Web hizmeti

Dağıtılan Web hizmetini bir TLS/SSL sertifikası kullanarak güvenli hale getirebilirsiniz, Puanlama veya Swagger URI 'sini kullanarak hizmete bağlanmak için [https](https://en.wikipedia.org/wiki/HTTPS) kullanabilirsiniz. HTTPS, iki arasındaki iletişimleri şifreleyerek bir istemci ve Web hizmeti arasındaki iletişimin güvenliğini sağlamaya yardımcı olur. Şifreleme, [Aktarım Katmanı Güvenliği (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)kullanır. TLS, bazen TLS 'nin öncülü olan *Güvenli Yuva Katmanı* (SSL) olarak adlandırılır.

> [!IMPORTANT]
> Azure Machine Learning tarafından dağıtılan Web Hizmetleri yalnızca TLS sürüm 1,2 ' i destekler. Bir istemci uygulaması oluştururken, bu sürümü desteklediğinden emin olun.

Daha fazla bilgi için bkz. [Azure Machine Learning aracılığıyla bir Web hizmetini güvenli hale getirmek IÇIN TLS kullanma](how-to-secure-web-service.md).

### <a name="authentication-for-services"></a>Hizmetler için kimlik doğrulaması

Azure Machine Learning, Web hizmetlerinizi erişimi denetlemek için iki yol sağlar.

|Kimlik Doğrulama Yöntemi|ACı|AKS|
|---|---|---|
|Anahtar|Varsayılan olarak devre dışı| Varsayılan olarak etkindir|
|Belirteç| Kullanılamaz| Varsayılan olarak devre dışı |

Bir anahtara veya belirteçle güvenli hale getirilmiş bir hizmete istek gönderilirken, anahtar veya belirteci geçirmek için __Yetkilendirme__ üst bilgisini kullanın. Anahtar veya belirtecin olarak biçimlendirilmesi gerekir `Bearer <key-or-token>` , burada `<key-or-token>` anahtar veya belirteç değeridir.

Anahtarlar ve belirteçler arasındaki başlıca fark, **anahtarların statik olduğu ve el ile yeniden üretilme** ve **belirteçlerin süre sonu üzerine yenilenmesi gerektiği**. Anahtar tabanlı kimlik doğrulaması, Azure Container Instance ve Azure Kubernetes hizmeti tarafından dağıtılan Web Hizmetleri için desteklenir ve belirteç tabanlı kimlik doğrulaması **yalnızca** Azure Kubernetes hizmet dağıtımları için kullanılabilir. Kimlik doğrulamasını yapılandırma hakkında daha fazla bilgi için bkz. [Web Hizmetleri olarak dağıtılan modeller için kimlik doğrulamasını yapılandırma](how-to-authenticate-web-service.md).


#### <a name="authentication-with-keys"></a>Anahtarlar ile kimlik doğrulama

Bir dağıtım için kimlik doğrulamasını etkinleştirdiğinizde, otomatik olarak kimlik doğrulama anahtarları oluşturursunuz.

* Azure Kubernetes hizmetine dağıtım yaparken, varsayılan olarak kimlik doğrulaması etkinleştirilir.
* Azure Container Instances dağıtım yaparken, varsayılan olarak kimlik doğrulaması devre dışıdır.

Kimlik doğrulamasını denetlemek için, `auth_enabled` bir dağıtım oluştururken veya güncelleştirirken parametresini kullanın.

Kimlik doğrulaması etkinleştirilirse, `get_keys` birincil ve ikincil kimlik doğrulama anahtarını almak için yöntemini kullanabilirsiniz:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Bir anahtarı yeniden oluşturmanız gerekiyorsa kullanın [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29) .

#### <a name="authentication-with-tokens"></a>Belirteçlerle kimlik doğrulama

Bir Web hizmeti için belirteç kimlik doğrulamasını etkinleştirdiğinizde, bir kullanıcının, Web hizmetine erişmek için bir Azure Machine Learning JWT belirteci sağlaması gerekir. 

* Azure Kubernetes hizmetine dağıtım yaparken belirteç kimlik doğrulaması varsayılan olarak devre dışıdır.
* Azure Container Instances dağıtım yaparken belirteç kimlik doğrulaması desteklenmez.

Belirteç kimlik doğrulamasını denetlemek için, `token_auth_enabled` bir dağıtım oluştururken veya güncelleştirirken parametresini kullanın.

Belirteç kimlik doğrulaması etkinleştirilirse, `get_token` bir taşıyıcı belirteci almak için yöntemini ve bu belirteçlerin süre sonu süresini kullanabilirsiniz:

```python
token, refresh_by = service.get_token()
print(token)
```

[Azure CLI ve Machine Learning uzantısı](reference-azure-machine-learning-cli.md)varsa, bir belirteç almak için aşağıdaki komutu kullanabilirsiniz:

```azurecli
az ml service get-access-token -n <service-name>
```

> [!IMPORTANT]
> Şu anda belirteci almanın tek yolu Azure Machine Learning SDK veya Azure CLı Machine Learning uzantısını kullanmaktır.

Belirtecin zamanından sonra yeni bir belirteç istemeniz gerekir `refresh_by` . 

## <a name="request-data"></a>İstek verileri

REST API, isteğin gövdesinin aşağıdaki yapıyla bir JSON belgesi olmasını bekliyor:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Verilerin yapısının, hizmette Puanlama betiğinin ve modelinin beklediği ile eşleşmesi gerekir. Puanlama betiği, verileri modele geçirmeden önce değiştirebilir.

### <a name="binary-data"></a>İkili veriler

Hizmetinize ikili veri desteğini etkinleştirme hakkında daha fazla bilgi için bkz. [ikili veriler](how-to-deploy-advanced-entry-script.md#binary-data).

> [!TIP]
> İkili veri desteğini etkinleştirme, dağıtılan model tarafından kullanılan score.py dosyasında olur. İstemciden, programlama dilinizin HTTP işlevini kullanın. Örneğin, aşağıdaki kod parçacığı bir JPG dosyasının içeriğini bir Web hizmetine gönderir:
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>Çıkış noktaları arası kaynak paylaşımı (CORS)

Hizmetinizde CORS desteğini etkinleştirme hakkında bilgi için bkz. çıkış noktaları [arası kaynak paylaşımı](how-to-deploy-advanced-entry-script.md#cors).

## <a name="call-the-service-c"></a>Hizmeti çağırma (C#)

Bu örnek, C# ' [ın not defteri Içinde tren](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) örneği kullanılarak oluşturulan Web hizmetini çağırmak için nasıl kullanılacağını gösterir:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

Döndürülen sonuçlar aşağıdaki JSON belgesine benzer:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Hizmeti çağırın (git)

Bu örnek, go 'nun [Not defteri Içinde tren](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) örneği kullanılarak oluşturulan Web hizmetini çağırmak için nasıl kullanılacağını gösterir:

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

Döndürülen sonuçlar aşağıdaki JSON belgesine benzer:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Hizmeti çağırma (Java)

Bu örnek, [Not defteri 'nin Içindeki eğten](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) oluşturulan Web hizmetini çağırmak için Java 'nın nasıl kullanılacağını gösterir:

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

Döndürülen sonuçlar aşağıdaki JSON belgesine benzer:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Hizmeti çağırma (Python)

Bu örnekte, Python kullanarak [Not defteri 'nin Içinden eğitim](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) örneği kullanılarak oluşturulan Web hizmetini nasıl çağıracağı gösterilmektedir:

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Döndürülen sonuçlar aşağıdaki JSON belgesine benzer:

```JSON
[217.67978776218715, 224.78937091757172]
```


## <a name="web-service-schema-openapi-specification"></a>Web hizmeti şeması (Openapı belirtimi)

Dağıtımınızla otomatik şema oluşturma kullandıysanız, [swagger_uri özelliğini](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri)kullanarak hizmetin openapı belirtiminin adresini alabilirsiniz. (Örneğin, `print(service.swagger_uri)` .) Belirtimi almak için bir GET isteği kullanın veya bir tarayıcıda URI 'yi açın.

Aşağıdaki JSON belgesi, bir dağıtım için oluşturulan bir şemanın (Openapı belirtimi) bir örneğidir:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Daha fazla bilgi için bkz. [Openapı belirtimi](https://swagger.io/specification/).

Belirtiden istemci kitaplıkları oluşturabileceğiniz bir yardımcı program için bkz. [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen).


> [!TIP]
> Hizmeti dağıttıktan sonra şema JSON belgesini alabilirsiniz. Yerel Web hizmetinin Swagger dosyasına URI 'yi almak için dağıtılan Web hizmetindeki [swagger_uri özelliğini](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri) kullanın (örneğin, `service.swagger_uri` ).

## <a name="consume-the-service-from-power-bi"></a>Hizmeti Power BI tüketme

Power BI, Power BI verileri tahmine dayalı olarak zenginleştirmek için Azure Machine Learning Web hizmetlerinin kullanımını destekler. 

Power BI, tüketim için desteklenen bir Web hizmeti oluşturmak için, şemanın Power BI gereken biçimi desteklemesi gerekir. [Power BI tarafından desteklenen bir şema oluşturmayı öğrenin](./how-to-deploy-advanced-entry-script.md#power-bi-compatible-endpoint).

Web hizmeti dağıtıldıktan sonra Power BI iş akışlarından kullanılabilir. [Azure Machine Learning web hizmetini Power BI'dan kullanmayı öğrenin](/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Sonraki adımlar

Python ve derin öğrenme modellerinin gerçek zamanlı Puanlama için başvuru mimarisini görüntülemek için [Azure mimari merkezi](/azure/architecture/reference-architectures/ai/realtime-scoring-python)' ne gidin.
