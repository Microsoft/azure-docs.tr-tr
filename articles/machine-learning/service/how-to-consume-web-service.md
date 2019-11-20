---
title: Web hizmeti olarak dağıtılan model için istemci oluştur
titleSuffix: Azure Machine Learning
description: Bir model Azure Machine Learning modeliyle dağıtıldığında oluşturulan bir Web hizmetini nasıl kullanacağınızı öğrenin. Web hizmeti bir REST API gösterir. Tercih ettiğiniz programlama dilini kullanarak bu API için istemcileri oluşturun.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/06/2019
ms.custom: seodec18
ms.openlocfilehash: 608f343166b528cacf3b1479d993466f6df7cb7e
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932153"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Web hizmeti olarak dağıtılan bir Azure Machine Learning modeli kullanma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bir Azure Machine Learning modelini Web hizmeti olarak dağıtmak bir REST API oluşturur. Bu API 'ye veri gönderebilir ve modelin döndürdüğü tahmini alabilirsiniz. Bu belgede, Web hizmeti için, Go, Java ve Python kullanarak C#istemci oluşturma hakkında bilgi edinin.

Azure Container Instances, Azure Kubernetes hizmeti veya alan-programlanabilir kapı dizileri (FPGA) için bir görüntü dağıtırken bir Web hizmeti oluşturursunuz. Kayıtlı modeller ve Puanlama dosyalarından görüntüler oluşturursunuz. [Azure MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)kullanarak bir Web hizmetine erişmek IÇIN kullanılan URI 'yi alırsınız. Kimlik doğrulaması etkinleştirilirse, kimlik doğrulama anahtarlarını veya belirteçlerini almak için SDK 'Yı da kullanabilirsiniz.

Machine Learning Web hizmeti kullanan bir istemci oluşturmak için genel iş akışı:

1. Bağlantı bilgilerini almak için SDK 'Yı kullanın.
1. Model tarafından kullanılan istek verilerinin türünü belirleme.
1. Web hizmetini çağıran bir uygulama oluşturun.

> [!TIP]
> Bu belgedeki örnekler, Openapı (Swagger) belirtimlerinin kullanımı olmadan el ile oluşturulur. Dağıtımınız için bir Openapı belirtimini etkinleştirdiyseniz, hizmetinize yönelik istemci kitaplıkları oluşturmak için [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen) gibi araçları kullanabilirsiniz.

## <a name="connection-information"></a>Bağlantı bilgileri

> [!NOTE]
> Web hizmeti bilgilerini almak için Azure Machine Learning SDK 'sını kullanın. Bu bir Python SDK 'sına sahiptir. Hizmet için bir istemci oluşturmak üzere herhangi bir dili kullanabilirsiniz.

[Azureml. Core. WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) sınıfı, istemci oluşturmak için gereken bilgileri sağlar. Aşağıdaki `Webservice` özellikleri bir istemci uygulaması oluşturmak için yararlıdır:

* `auth_enabled`-anahtar kimlik doğrulaması etkinse, `True`; Aksi takdirde, `False`.
* `token_auth_enabled`-belirteç kimlik doğrulaması etkinse, `True`; Aksi takdirde, `False`.
* `scoring_uri`-REST API adresi.
* `swagger_uri`-Openapı belirtiminin adresi. Otomatik şema oluşturmayı etkinleştirdiyseniz, bu URI kullanılabilir. Daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md#schema).

Dağıtılan Web Hizmetleri için bu bilgileri almanın üç yolu vardır:

* Bir modeli dağıtırken, hizmet hakkındaki bilgilerle bir `Webservice` nesnesi döndürülür:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* Çalışma alanınızdaki modeller için dağıtılan Web Hizmetleri listesini almak için `Webservice.list` kullanabilirsiniz. Döndürülen bilgi listesini daraltmak için filtre ekleyebilirsiniz. Ne filtrelenebilir hakkında daha fazla bilgi için, bkz. [WebService. List](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) başvuru belgeleri.

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Dağıtılan hizmetin adını biliyorsanız, `Webservice`yeni bir örneğini oluşturabilir ve çalışma alanını ve hizmet adını parametreler olarak sağlayabilirsiniz. Yeni nesne dağıtılan hizmetle ilgili bilgiler içerir.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="authentication-for-services"></a>Hizmetler için kimlik doğrulaması

Azure Machine Learning, Web hizmetlerinizi erişimi denetlemek için iki yol sağlar.

|Kimlik Doğrulama Yöntemi|ACı|AKS|
|---|---|---|
|Anahtar|Varsayılan olarak devre dışı| Varsayılan olarak etkin|
|Belirteç| Yok| Varsayılan olarak devre dışı |

Bir anahtara veya belirteçle güvenli hale getirilmiş bir hizmete istek gönderilirken, anahtar veya belirteci geçirmek için __Yetkilendirme__ üst bilgisini kullanın. Anahtar veya belirteç `Bearer <key-or-token>`olarak biçimlendirilmelidir; burada `<key-or-token>` anahtarınız veya belirteç değeridir.

#### <a name="authentication-with-keys"></a>Anahtarlar ile kimlik doğrulama

Bir dağıtım için kimlik doğrulamasını etkinleştirdiğinizde, otomatik olarak kimlik doğrulama anahtarları oluşturursunuz.

* Azure Kubernetes hizmetine dağıtım yaparken, varsayılan olarak kimlik doğrulaması etkinleştirilir.
* Azure Container Instances dağıtım yaparken, varsayılan olarak kimlik doğrulaması devre dışıdır.

Kimlik doğrulamasını denetlemek için, bir dağıtım oluştururken veya güncelleştirirken `auth_enabled` parametresini kullanın.

Kimlik doğrulaması etkinleştirilirse, birincil ve ikincil kimlik doğrulama anahtarını almak için `get_keys` yöntemini kullanabilirsiniz:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Bir anahtarı yeniden oluşturmanız gerekiyorsa [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)kullanın.

#### <a name="authentication-with-tokens"></a>Belirteçlerle kimlik doğrulama

Bir Web hizmeti için belirteç kimlik doğrulamasını etkinleştirdiğinizde, bir kullanıcının, Web hizmetine erişmek için bir Azure Machine Learning JWT belirteci sağlaması gerekir. 

* Azure Kubernetes hizmetine dağıtım yaparken belirteç kimlik doğrulaması varsayılan olarak devre dışıdır.
* Azure Container Instances dağıtım yaparken belirteç kimlik doğrulaması desteklenmez.

Belirteç kimlik doğrulamasını denetlemek için, bir dağıtım oluştururken veya güncelleştirirken `token_auth_enabled` parametresini kullanın.

Belirteç kimlik doğrulaması etkinleştirilirse, bir taşıyıcı belirtecini almak için `get_token` yöntemini kullanabilirsiniz ve belirteçleri sona erme zamanı zaman aşımı süresi:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Belirtecin `refresh_by` zamanından sonra yeni bir belirteç istemeniz gerekir. 

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

Örneğin, [Not defteri örneğinde eğitme](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) içindeki model 10 sayıdan oluşan bir dizi bekler. Bu örneğe ilişkin Puanlama betiği, istekten bir sayısal tuş bir y dizisi oluşturur ve modele geçirir. Aşağıdaki örnek bu hizmetin beklediği verileri gösterir:

```json
{
    "data": 
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
            ]
        ]
}
```

Web hizmeti, bir istekte birden çok veri kümesini kabul edebilir. Yanıt dizisi içeren bir JSON belgesi döndürür.

### <a name="binary-data"></a>İkili veriler

Hizmetinize ikili veri desteğini etkinleştirme hakkında daha fazla bilgi için bkz. [ikili veriler](how-to-deploy-and-where.md#binary).

### <a name="cross-origin-resource-sharing-cors"></a>Çıkış noktaları arası kaynak paylaşımı (CORS)

Hizmetinizde CORS desteğini etkinleştirme hakkında bilgi için bkz. çıkış noktaları [arası kaynak paylaşımı](how-to-deploy-and-where.md#cors).

## <a name="call-the-service-c"></a>Hizmeti (C#) çağırın

Bu örnek, C# [Not defteri 'nde tren](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) örneği kullanılarak oluşturulan Web hizmetini çağırmak için nasıl kullanılacağını gösterir:

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

Bu örnek, go 'nun [Not defteri Içinde tren](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) örneği kullanılarak oluşturulan Web hizmetini çağırmak için nasıl kullanılacağını gösterir:

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

Bu örnek, [Not defteri 'nin Içindeki eğten](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) oluşturulan Web hizmetini çağırmak için Java 'nın nasıl kullanılacağını gösterir:

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

Bu örnekte, Python kullanarak [Not defteri 'nin Içinden eğitim](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) örneği kullanılarak oluşturulan Web hizmetini nasıl çağıracağı gösterilmektedir:

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

## <a name="consume-the-service-from-power-bi"></a>Hizmeti Power BI tüketme

Power BI, Power BI verileri tahmine dayalı olarak zenginleştirmek için Azure Machine Learning Web hizmetlerinin kullanımını destekler. 

Power BI, tüketim için desteklenen bir Web hizmeti oluşturmak için, şemanın Power BI gereken biçimi desteklemesi gerekir. [Power BI tarafından desteklenen bir şema oluşturmayı öğrenin](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#example-entry-script).

Web hizmeti dağıtıldıktan sonra, Power BI veri akışlarından tüketilebilir. [Power BI bir Azure Machine Learning Web hizmeti kullanmayı öğrenin](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Sonraki adımlar

Python ve derin öğrenme modellerinin gerçek zamanlı Puanlama için başvuru mimarisini görüntülemek için [Azure mimari merkezi](/azure/architecture/reference-architectures/ai/realtime-scoring-python)' ne gidin.
