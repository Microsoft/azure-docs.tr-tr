---
title: 'ML Studio (klasik): Web hizmetlerini kullanma-Azure'
description: Machine Learning hizmeti Azure Machine Learning Studio (klasik) dağıtıldıktan sonra, tekrar eden Web hizmeti gerçek zamanlı istek-yanıt hizmeti ya da bir toplu yürütme hizmeti olarak tüketilebilir.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-python, devx-track-js, devx-track-csharp
ms.date: 05/29/2020
ms.openlocfilehash: 0dc49265c0ea799e194e4ac7004b558d8a9d4dd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519278"
---
# <a name="how-to-consume-a-machine-learning-studio-classic-web-service"></a>Machine Learning Studio (klasik) Web hizmetini kullanma

**Uygulama hedefi:** ![ Bu bir onay işaretidir ve bu makalenin Machine Learning Studio (klasik) için geçerli olduğu anlamına gelir.  ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasik)   ![ Bu bir X ' dir ve bu makalenin Azure Machine Learning için geçerli olduğu anlamına gelir.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Bir Web hizmeti olarak Azure Machine Learning Studio (klasik) tahmine dayalı bir model dağıttıktan sonra, verileri göndermek ve öngörüleri almak için bir REST API kullanabilirsiniz. Verileri gerçek zamanlı veya toplu işlem modunda gönderebilirsiniz.

Machine Learning Studio (klasik) kullanarak Machine Learning Web hizmeti oluşturma ve dağıtma hakkında daha fazla bilgi edinebilirsiniz:

* Machine Learning Studio (klasik) ' de deneme oluşturma hakkında bir öğretici için, bkz. [İlk denemenizi oluşturma](create-experiment.md).
* Web hizmeti dağıtma hakkında ayrıntılı bilgi için bkz. [Machine Learning Web hizmeti dağıtma](deploy-a-machine-learning-web-service.md).
* Genel olarak Machine Learning hakkında daha fazla bilgi için [Machine Learning belge merkezini](https://azure.microsoft.com/documentation/services/machine-learning/)ziyaret edin.



## <a name="overview"></a>Genel Bakış
Azure Machine Learning Web hizmeti ile bir dış uygulama, Machine Learning iş akışı Puanlama modeliyle gerçek zamanlı iletişim kurar. Bir Machine Learning Web hizmeti çağrısı, bir dış uygulamaya tahmin sonuçları döndürür. Bir Machine Learning Web hizmeti çağrısı yapmak için, bir tahmin dağıttığınızda oluşturulan bir API anahtarı geçirirsiniz. Machine Learning Web hizmeti, Web programlama projeleri için popüler bir mimari seçimi olan REST 'i temel alır.

Azure Machine Learning Studio (klasik) iki tür hizmete sahiptir:

* Request-Response Service (RR)-Machine Learning Studio (klasik) tarafından oluşturulan ve dağıtılan durum bilgisiz modellere yönelik bir arabirim sağlayan düşük gecikme süreli, yüksek oranda ölçeklenebilir bir hizmettir.
* Toplu yürütme hizmeti (BES): veri kayıtları için toplu iş puan veren bir zaman uyumsuz hizmettir.

Machine Learning Web Hizmetleri hakkında daha fazla bilgi için bkz. [Machine Learning Web hizmeti dağıtma](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Yetkilendirme anahtarı al
Denemenizi dağıtırken, Web hizmeti için API anahtarları oluşturulur. Anahtarları birkaç konumdan alabilirsiniz.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Microsoft Azure Machine Learning Web Hizmetleri portalından
[Microsoft Azure Machine Learning Web Hizmetleri](https://services.azureml.net) portalında oturum açın.

Yeni bir Machine Learning Web hizmeti için API anahtarını almak için:

1. Azure Machine Learning Web Hizmetleri portalında, **Web Hizmetleri** üst menüsü ' ne tıklayın.
2. Anahtarı almak istediğiniz Web hizmetine tıklayın.
3. Üstteki menüde **tüketme**' ye tıklayın.
4. **Birincil anahtarı** kopyalayın ve kaydedin.

Klasik bir Machine Learning Web hizmetine yönelik API anahtarını almak için:

1. Azure Machine Learning Web Hizmetleri portalında, **Klasik Web Hizmetleri** üst menüsü ' ne tıklayın.
2. Üzerinde çalıştığınız Web hizmeti ' ne tıklayın.
3. Anahtarı almak istediğiniz uç noktaya tıklayın.
4. Üstteki menüde **tüketme**' ye tıklayın.
5. **Birincil anahtarı** kopyalayın ve kaydedin.

### <a name="classic-web-service"></a>Klasik Web hizmeti
 Ayrıca, klasik Web hizmeti için Machine Learning Studio (klasik) için bir anahtar alabilirsiniz.

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (klasik)
1. Machine Learning Studio (klasik) bölümünde, sol taraftaki **Web Hizmetleri** ' ne tıklayın.
2. Bir Web hizmetine tıklayın. **API anahtarı** **Pano** sekmesindedir.

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>Machine Learning Web hizmetine bağlanma
HTTP isteğini ve yanıtını destekleyen herhangi bir programlama dilini kullanarak bir Machine Learning Web hizmetine bağlanabilirsiniz. Örnekleri bir Machine Learning Web hizmeti yardım sayfasından C#, Python ve R içinde görüntüleyebilirsiniz.

**MACHINE LEARNING API yardımı** Bir Web hizmeti dağıtırken API yardımı Machine Learning oluşturulur. Bkz. [öğretici 3: Kredi risk modelini dağıtma](tutorial-part3-credit-risk-deploy.md).
Machine Learning API yardımı, bir tahmin Web hizmeti hakkındaki ayrıntıları içerir.

1. Üzerinde çalıştığınız Web hizmeti ' ne tıklayın.
2. API yardım sayfasını görüntülemek istediğiniz uç noktaya tıklayın.
3. Üstteki menüde **tüketme**' ye tıklayın.
4. Request-Response veya toplu yürütme uç noktaları altında **API Yardım sayfası** ' na tıklayın.

**Yeni bir Web hizmeti için Machine Learning API yardımını görüntülemek için**

[Azure Machine Learning Web Hizmetleri portalında](https://services.azureml.net/):

1. Üst menüdeki **Web Hizmetleri** ' ne tıklayın.
2. Anahtarı almak istediğiniz Web hizmetine tıklayın.

Request-Response ve Batch yürütme Hizmetleri için URI 'Leri ve C#, R ve Python 'da örnek kodu almak için **Web hizmetini kullan** ' a tıklayın.

Sağlanan URI 'lerden çağrılan API 'Ler için Swagger tabanlı belgeler almak üzere **Swagger API** ' ye tıklayın.

### <a name="c-sample"></a>C# örneği
Bir Machine Learning Web hizmetine bağlanmak için, bir **HttpClient** kullanarak ScoreData kullanın. ScoreData, ScoreData ' ı temsil eden sayısal özelliklerin n boyutlu bir vektörü olan FeatureVector öğesini içerir. Machine Learning hizmetinde bir API anahtarı ile kimlik doğrulaması yapabilirsiniz.

Bir Machine Learning Web hizmetine bağlanmak için **Microsoft. Aspnet. WebApi. Client** NuGet paketinin yüklü olması gerekir.

**Visual Studio 'da Microsoft. AspNet. WebApi. Client NuGet 'i yükler**

1. Indirme veri kümesini UCI: Yetişkin 2 sınıfı veri kümesi Web hizmetinden yayımlayın.
2. **Araçlar**  >  **NuGet Paket Yöneticisi**  >  **Paket Yöneticisi konsolu**' na tıklayın.
3. **Install-Package Microsoft. Aspnet. WebApi. Client** öğesini seçin.

**Kod örneğini çalıştırmak için**

1. Yayımla "Örnek 1: veri kümesini UCI: Yetişkin 2 sınıf veri kümesinden Indir" deneme, Machine Learning örnek koleksiyonunun bir parçası.
2. Bir Web hizmetindeki anahtarla apiKey atayın. Yukarıdaki **Yetkilendirme anahtarını öğrenin** bölümüne bakın.
3. Istek URI 'SI ile serviceUri atayın.

**Bunun için bir bütün istek şöyle görünecektir.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.NET application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Python örneği
Bir Machine Learning Web hizmetine bağlanmak için Python 2. X için **urllib2** kitaplığını ve Python 3. x için **urllib. Request** kitaplığını kullanın. Bir FeatureVector içeren ve ScoreData 'yı temsil eden sayısal özelliklerin n boyutlu bir vektörünün bulunduğu ScoreData geçişi yapılır. Machine Learning hizmetinde bir API anahtarı ile kimlik doğrulaması yapabilirsiniz.

**Kod örneğini çalıştırmak için**

1. Dağıt "Örnek 1: veri kümesini UCI: Yetişkin 2 sınıf veri kümesinden Indirin" deneme, Machine Learning örnek koleksiyonunun bir parçası.
2. Bir Web hizmetindeki anahtarla apiKey atayın. Bu makalenin başındaki **bir yetkilendirme anahtarı al** bölümüne bakın.
3. Istek URI 'SI ile serviceUri atayın.

**Bunun için bir bütün istek şöyle görünecektir.**
```python
import urllib2 # urllib.request and urllib.error for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(url, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>R örneği

Bir Machine Learning Web hizmetine bağlanmak için, isteği yapmak ve döndürülen JSON yanıtını işlemek üzere **Rkıvrık** ve **rjson** kitaplıklarını kullanın. Bir FeatureVector içeren ve ScoreData 'yı temsil eden sayısal özelliklerin n boyutlu bir vektörünün bulunduğu ScoreData geçişi yapılır. Machine Learning hizmetinde bir API anahtarı ile kimlik doğrulaması yapabilirsiniz.

**Bunun için bir bütün istek şöyle görünecektir.**
```r
library("curl")
library("httr")
library("rjson")

requestFailed = function(response) {
    return (response$status_code >= 400)
}

printHttpResult = function(response, result) {
    if (requestFailed(response)) {
        print(paste("The request failed with status code:", response$status_code, sep=" "))
    
        # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
        print(response$headers)
    }
    
    print("Result:") 
    print(fromJSON(result))  
}

req = list(
        Inputs = list( 
            "input1" = list(
                "ColumnNames" = list("Col1", "Col2", "Col3"),
                "Values" = list( list( "0", "value", "0" ),  list( "0", "value", "0" )  )
            )                ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "abc123" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

response = POST(url= "<your-api-uri>",
        add_headers("Content-Type" = "application/json", "Authorization" = authz_hdr),
        body = body)

result = content(response, type="text", encoding="UTF-8")

printHttpResult(response, result)
```

### <a name="javascript-sample"></a>JavaScript örneği

Bir Machine Learning Web hizmetine bağlanmak için, projenizdeki **istek** NPM paketini kullanın. Ayrıca, `JSON` girişinizi biçimlendirmek ve sonucu ayrıştırmak için nesnesini de kullanacaksınız. `npm install request --save`' Yi kullanarak veya ' `"request": "*"` de package.jsekleyin veya `dependencies` çalıştırın `npm install` .

**Bunun için bir bütün istek şöyle görünecektir.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```