---
title: Belge çevirisi ile çalışmaya başlama
description: C#, Go, Java, Node.js veya Python programlama dillerini ve platformlarını kullanarak belge çevirisi hizmeti oluşturma
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 03/05/2021
ms.openlocfilehash: 780e6defe4f7d09e2d136c080525447ffd29bbb4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612390"
---
# <a name="get-started-with-document-translation-preview"></a>Belge çevirisi 'ni kullanmaya başlama (Önizleme)

 Bu makalede, belge çevirisini HTTP REST API yöntemleriyle kullanmayı öğreneceksiniz. Belge çevirisi, [Azure Translator](../translator-info-overview.md) hizmetinin bulut tabanlı bir özelliğidir.  Belge çevirisi API 'SI, kaynak belge yapısını ve metin biçimlendirmesini korurken tüm belgelerin çevrilmesini mümkün bir şekilde sunar.

## <a name="prerequisites"></a>Önkoşullar

> [!NOTE]
>
> 1. Genellikle, Azure portal bir bilişsel hizmet kaynağı oluşturduğunuzda, çok sunuculu bir abonelik anahtarı veya tek servis abonelik anahtarı oluşturma seçeneğiniz vardır. Ancak, belge çevirisi şu anda yalnızca Translator (tek hizmet) kaynağında desteklenir ve **bilişsel** hizmetler (çok hizmet) kaynağına dahil değildir.
> 2. Belge çevirisi şu anda **S1 standart hizmet planında** kullanılabilir. _Bkz_ . bilişsel [Hizmetler fiyatlandırması — çevirici](https://azure.microsoft.com/pricing/details/cognitive-services/translator/).
>

Başlamak için şunlar gerekir:

* Etkin bir [**Azure hesabı**](https://azure.microsoft.com/free/cognitive-services/).  Bir [**hesabınız yoksa ücretsiz bir hesap oluşturabilirsiniz**](https://azure.microsoft.com/free/).

* Bir [**Translator**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) hizmet kaynağı (**bilişsel** hizmetler kaynağı değil).

* [**Azure Blob depolama hesabı**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). BLOB verilerinizi depolama hesabınızda depolamak ve düzenlemek için kapsayıcılar oluşturacaksınız.

## <a name="get-your-custom-domain-name-and-subscription-key"></a>Özel etki alanı adınızı ve abonelik anahtarınızı alın

> [!IMPORTANT]
>
> * **Belge çevirisi hizmetine yönelik tüm API istekleri özel bir etki alanı uç noktası gerektirir**.
> * Belge çevirisi için HTTP istekleri yapmak üzere Azure portal kaynak _anahtarlarınız ve uç_ nokta sayfanızda veya küresel çevirmen uç noktasında bulunan uç noktayı kullanamazsınız `api.cognitive.microsofttranslator.com` .

### <a name="what-is-the-custom-domain-endpoint"></a>Özel etki alanı uç noktası nedir?

Özel etki alanı uç noktası, kaynak adınız, ana bilgisayar adı ve çevirmen alt dizinleriyle biçimlendirilen bir URL 'dir:

```http
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1
```

### <a name="find-your-custom-domain-name"></a>Özel etki alanı adınızı bulun

**Kaynak adı** ( *özel etki alanı adı* da denir) parametresi, çevirmen kaynağını oluştururken **ad** alanına girdiğiniz değerdir.

:::image type="content" source="../media/instance-details.png" alt-text="Azure portal görüntüsü, kaynak oluştur, anlık ayrıntılar, ad alanı.":::

### <a name="get-your-subscription-key"></a>Abonelik anahtarınızı alın

Çeviri hizmetine gönderilen istekler, erişim için kimlik doğrulaması için salt okunurdur bir anahtar gerektirir.

1. Yeni bir kaynak oluşturduysanız, dağıtıldıktan sonra **Kaynağa Git**' i seçin. Mevcut bir belge çevirisi kaynağınız varsa doğrudan kaynak sayfanıza gidin.
1. Sol Rampadaki *kaynak yönetimi* altında **anahtarlar ve uç nokta**' ı seçin.
1. Abonelik anahtarınızı kopyalayıp *Microsoft Notepad* gibi uygun bir konuma yapıştırın.
1. Belge çevirisi hizmetine isteğiniz için kimlik doğrulaması yapmak üzere aşağıdaki koda yapıştırabilirsiniz.

:::image type="content" source="../media/translator-keys.png" alt-text="Azure portal içindeki abonelik anahtarınızı alın alanının görüntüsü.":::

## <a name="create-your-azure-blob-storage-containers"></a>Azure Blob depolama Kapsayıcılarınızı oluşturma

Kaynak, hedef ve isteğe bağlı sözlük dosyaları için [**Azure Blob depolama hesabınızda**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) [**kapsayıcı oluşturmanız**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) gerekir.

* **Kaynak kapsayıcısı**. Bu kapsayıcı, dosyalarınızı çeviri için yüklediğiniz yerdir (gerekli).
* **Hedef kapsayıcı**. Bu kapsayıcı, çevrilmiş dosyalarınızın depolanacağı (gerekli).  
* **Sözlük kapsayıcısı**. Bu kapsayıcı, sözlük dosyalarınızı yüklediğiniz yerdir (isteğe bağlı).  

### <a name="create-sas-access-tokens-for-document-translation"></a>**Belge çevirisi için SAS erişim belirteçleri oluşturma**

`sourceUrl`, `targetUrl` Ve isteğe bağlı, `glossaryUrl` bir sorgu dizesi olarak eklenmiş bir paylaşılan ERIŞIM imzası (SAS) belirteci içermelidir. Belirteç, kapsayıcınıza veya belirli bloblarınıza atanabilir. *Bkz* . [**belge ÇEVIRISI işlemi için SAS belirteçleri oluşturma**](create-sas-tokens.md).

* **Kaynak** Kapsayıcınız veya Blobun, **okuma** ve **Listeleme** erişiminin belirlenmiş olması gerekir.
* **Hedef** Kapsayıcınız veya Blobun, **yazma** ve **Listeleme** erişiminin belirlenmiş olması gerekir.
* **Sözlük** Kapsayıcınız veya Blobun, **okuma** ve **Listeleme** erişiminin belirlenmiş olması gerekir.

> [!TIP]
>
> * Bir işlemde **birden çok** dosya (blob) çeviriyseniz, **kapsayıcı düzeyinde SAS erişimi atayın**.  
> * Bir işlemde **tek** bir dosya (blob) çeviriyseniz, **BLOB düzeyinde SAS erişimi atayın**.  
>

## <a name="set-up-your-coding-platform"></a>Kodlama platformunuzu ayarlama

### <a name="c"></a>[C#](#tab/csharp)

* Yeni bir proje oluşturma.
* Program. cs ' i aşağıda gösterilen C# kodu ile değiştirin.
* Program. cs ' de uç noktanızı, abonelik anahtarınızı ve kapsayıcı URL değerlerinizi ayarlayın.
* JSON verilerini işlemek için [ .net CLI kullanarak paketeNewtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json/)ekleyin.
* Programı proje dizininden çalıştırın.

### <a name="nodejs"></a>[Node.js](#tab/javascript)

* Yeni bir Node.js projesi oluşturun.
* Axios kitaplığını ile birlikte yükler `npm i axios` .
* Aşağıdaki kodu kopyalayıp projenize yapıştırın.
* Uç noktanızı, abonelik anahtarınızı ve kapsayıcı URL değerlerinizi ayarlayın.
* Programı çalıştırın.

### <a name="python"></a>[Python](#tab/python)  

* Yeni bir proje oluşturma.
* Örneklerden birindeki kodu kopyalayıp projenize yapıştırın.
* Uç noktanızı, abonelik anahtarınızı ve kapsayıcı URL değerlerinizi ayarlayın.
* Programı çalıştırın. Örneğin: `python translate.py`.

### <a name="java"></a>[Java](#tab/java)

* Projeniz için bir çalışma dizini oluşturun. Örnek:

```powershell
mkdir sample-project
```

* Proje dizininizde aşağıdaki alt dizin yapısını oluşturun:  

  src</br>
&emsp; └ Ana</br>
&emsp;&emsp;&emsp;└ Java

```powershell
mkdir -p src/main/java/
```

**Note**: Java kaynak dosyaları (örneğin, _Sample. Java_) src/Main/**Java** içinde canlı.

* Kök dizininizde (örneğin, *örnek proje*), projenizi Gradle ile başlatın:

```powershell
gradle init --type basic
```

* Bir **DSL** seçmeniz Istendiğinde, **Kotlin**' ı seçin.

* Dosyayı güncelleştirin `build.gradle.kts`  . Örneğe bağlı olarak güncelleştirmeniz gerektiğini aklınızda bulundurun `mainClassName` :

  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "{NAME OF YOUR CLASS}"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
  }
  ```

* **Java** dizininde bir Java dosyası oluşturun ve kodu belirtilen örnekten kopyalayıp yapıştırın. Abonelik anahtarınızı ve uç noktanızı eklemeyi unutmayın.

* **Örneği kök dizinden derleyin ve çalıştırın**:

```powershell
gradle build
gradle run
```

### <a name="go"></a>[Git](#tab/go)  

* Yeni bir go projesi oluşturun.
* Aşağıda sağlanan kodu ekleyin.
* Uç noktanızı, abonelik anahtarınızı ve kapsayıcı URL değerlerinizi ayarlayın.
* Dosyayı '.go' uzantısıyla kaydedin.
* Go yüklü bir bilgisayarda bir komut istemi açın.
* Dosyayı oluşturun. Örneğin: ' Go Build example-Code. go '.
* Dosyayı çalıştırın, örneğin: ' example-Code '.

 ---

## <a name="make-document-translation-requests"></a>Belge çevirisi istekleri yapın

Bir Batch belge çevirisi isteği, bir POST isteği aracılığıyla Translator hizmeti uç noktanıza gönderilir. Başarılı olursa POST yöntemi bir `202 Accepted`  yanıt kodu döndürür ve toplu istek hizmet tarafından oluşturulur.

### <a name="http-headers"></a>HTTP üstbilgileri

Her belge çevirisi API isteğine aşağıdaki üstbilgiler dahildir:

|HTTP üstbilgisi|Description|
|---|--|
|Ocp-Apim-Subscription-Key|**Gerekli**: değer, çevirmen veya bilişsel hizmetler kaynağınız için Azure abonelik anahtarıdır.|
|İçerik Türü|**Gerekli**: yükün içerik türünü belirtir. Kabul edilen değerler Application/JSON veya charset = UTF-8 ' dir.|
|İçerik Uzunluğu|**Gerekli**: istek gövdesinin uzunluğu.|

### <a name="post-request-body-properties"></a>POST isteği gövde özellikleri

* POST isteğinin gövdesi adlı bir JSON nesnesidir `inputs` .
* `inputs`Nesnesi, `sourceURL` `targetURL` kaynak ve hedef dil çiftleriniz için hem hem de kapsayıcı adreslerini içerir ve isteğe bağlı olarak bir `glossaryURL` kapsayıcı adresi içerebilir.
* `prefix`Ve `suffix` alanları (isteğe bağlı), kapsayıcıda klasörler dahil olmak üzere belge filtrelemek için kullanılır.
* `glossaries`Belge çevrildiğinde alan için bir değer (isteğe bağlı) uygulanır.
* `targetUrl`Her hedef dil için benzersiz olmalıdır.

>[!NOTE]
> Hedefte aynı ada sahip bir dosya zaten varsa, üzerine yazılır.

## <a name="post-a-translation-request"></a>Çeviri isteği gönder

<!-- markdownlint-disable MD024 -->
### <a name="post-request-body-to-translate-all-documents-in-a-container"></a>Bir kapsayıcıdaki tüm belgeleri çevirmek için istek gövdesini GÖNDERIN

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```


### <a name="post-request-body-to-translate-a-specific-document-in-a-container"></a>Kapsayıcıda belirli bir belgeyi çevirmek için istek gövdesini GÖNDERIN

* "StorageType" belirttiğinizden emin olun: "dosya"
* Belirli bir blob/belge (kapsayıcı için değil) için SAS belirteci & kaynak URL 'SI oluşturduğunuzdan emin olun 
* Hedef dosya adını hedef URL 'nin bir parçası olarak belirttiğinizden emin olun; ancak SAS belirteci hala kapsayıcı için olur.
* Aşağıdaki örnek istek, iki hedef dile çevrilen tek bir belgeyi göstermektedir

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "es"
                },
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "de"
                }
            ]
        }
    ]
}
```


> [!IMPORTANT]
>
> Aşağıdaki kod örnekleri için, belirttiğiniz yerlerde anahtarınızı ve uç noktanızı sabit kodlarız. İşiniz bittiğinde kodu koddan kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin.  Kimlik bilgilerinizi güvenli bir şekilde depolamanıza ve erişmenize yönelik yollar için bkz. Azure bilişsel [Hizmetler güvenliği](/azure/cognitive-services/cognitive-services-security?tabs=command-line%2Ccsharp) .
>
> İşleme bağlı olarak aşağıdaki alanları güncelleştirmeniz gerekebilir:
>>>
>> * `endpoint`
>> * `subscriptionKey`
>> * `sourceURL`
>> * `targetURL`
>> * `glossaryURL`
>> * `id`  (iş KIMLIĞI)
>>

#### <a name="locating--the-id-value"></a>Değer bulma `id`

* İşi `id`  POST yöntemi yanıt üst bilgisi `Operation-Location`  URL 'si değerinde bulabilirsiniz. URL 'nin son parametresi işlemin işdir **`id`** :

|**Yanıt üst bilgisi**|**Sonuç URL 'SI**|
|-----------------------|----------------|
Operation-Location   | https://<<span>kaynak adı>. cognitiveservices.Azure.com/Translator/Text/Batch/v1.0-Preview.1/Batches/9dce0aa9-78dc-41ba-8cae-2e2f3c2ff8ec</span>

* Bir belge çevirisi işi almak için bir **Işleri al** isteği de kullanabilirsiniz `id` .

>

## <a name="_post-document-translation_-request"></a>_Belge çevirisi Isteği gönder_

Çeviri hizmetine toplu belge çevirisi isteği gönderme.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Text;
    

    class Program
    {

        static readonly string route = "/batches";

        private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

        private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

        static readonly string json = ("{\"inputs\": [{\"source\": {\"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",\"storageSource\": \"AzureBlob\",\"language\": \"en\",\"filter\":{\"prefix\": \"Demo_1/\"} }, \"targets\": [{\"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"category\": \"general\",\"language\": \"es\"}]}]}");
        
        static async Task Main(string[] args)
        {
            using HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
            
                StringContent content = new StringContent(json, Encoding.UTF8, "application/json");

                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                request.Content = content;
                
                HttpResponseMessage  response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;
                if (response.IsSuccessStatusCode)
                {
                    Console.WriteLine($"Status code: {response.StatusCode}");
                    Console.WriteLine();
                    Console.WriteLine($"Response Headers:"); 
                    Console.WriteLine(response.Headers);
                }
                else
                    Console.Write("Error");

            }

        }

    }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios').default;

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let route = '/batches';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';

let data = JSON.stringify({"inputs": [
  {
      "source": {
          "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
          "storageSource": "AzureBlob",
          "language": "en",
          "filter":{
              "prefix": "Demo_1/"
          }
      },
      "targets": [
          {
              "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
              "storageSource": "AzureBlob",
              "category": "general",
              "language": "es"}]}]});

let config = {
  method: 'post',
  baseURL: endpoint,
  url: route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config)
.then(function (response) {
  let result = { statusText: response.statusText, statusCode: response.status, headers: response.headers };
  console.log()
  console.log(JSON.stringify(result));
})
.catch(function (error) {
  console.log(error);
});
```

### <a name="python"></a>[Python](#tab/python)

```python

import requests

endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
path = '/batches'
constructed_url = endpoint + path

payload= {
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
                "storageSource": "AzureBlob",
                "language": "en",
                "filter":{
                    "prefix": "Demo_1/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "es"
                }
            ]
        }
    ]
}
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey,
  'Content-Type': 'application/json'
}

response = requests.post(constructed_url, headers=headers, json=payload)

print(f'response status code: {response.status_code}\nresponse status: {response.reason}\nresponse headers: {response.headers}')
```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DocumentTranslation {
    String subscriptionKey = "'<YOUR-SUBSCRIPTION-KEY>'";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String path = endpoint + "/batches";

    OkHttpClient client = new OkHttpClient();

    public void post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,  "{\n \"inputs\": [\n {\n \"source\": {\n \"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\n \"filter\": {\n  \"prefix\": \"Demo_1\"\n  },\n  \"language\": \"en\",\n \"storageSource\": \"AzureBlob\"\n  },\n \"targets\": [\n {\n \"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\n \"category\": \"general\",\n\"language\": \"fr\",\n\"storageSource\": \"AzureBlob\"\n }\n ],\n \"storageType\": \"Folder\"\n }\n  ]\n}");
        Request request = new Request.Builder()
                .url(path).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        System.out.println(response.code());
        System.out.println(response.headers());
    }

  public static void main(String[] args) {
        try {
            DocumentTranslation sampleRequest = new DocumentTranslation();
            sampleRequest.post();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### <a name="go"></a>[Git](#tab/go)

```go

package main

import (
 "bytes"
 "fmt"
"net/http"
)

func main() {
endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
uri := endpoint + "/batches"
method := "POST"

var jsonStr = []byte(`{"inputs":[{"source":{"sourceUrl":"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS","storageSource":"AzureBlob","language":"en","filter":{"prefix":"Demo_1/"}},"targets":[{"targetUrl":"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS","storageSource":"AzureBlob","category":"general","language":"es"}]}]}`)

req, err := http.NewRequest(method, endpoint, bytes.NewBuffer(jsonStr))
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

client := &http.Client{}

if err != nil {
    fmt.Println(err)
    return
  }
  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()
  fmt.Println("response status:",  res.Status)
  fmt.Println("response headers",  res.Header)
}
```

---

## <a name="_get-file-formats_"></a>_Dosya biçimlerini al_ 

Desteklenen dosya biçimlerinin bir listesini alın. Başarılı olursa, bu yöntem bir `200 OK` yanıt kodu döndürür.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/documents/formats";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/documents/formats';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetFileFormats {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/documents/formats";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/documents/formats'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Git](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/documents/formats"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-job-status_"></a>_İş durumunu al_ 

Tek bir iş için geçerli durumu ve bir belge çevirisi isteğindeki tüm işlerin özetini alın. Başarılı olursa, bu yöntem bir `200 OK` yanıt kodu döndürür.
<!-- markdownlint-disable MD024 -->

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetJobStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Git](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-document-status_"></a>_Belge durumunu al_

### <a name="brief-overview"></a>Kısa genel bakış

Belge çevirisi isteğindeki belirli bir belgenin durumunu alır. Başarılı olursa, bu yöntem bir `200 OK` yanıt kodu döndürür.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/{id}/document/{documentId}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/{id}/document/{documentId}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetDocumentStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/{id}/document/{documentId}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/{id}/document/{documentId}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Git](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/{id}/document/{documentId}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_delete-job_"></a>_İşi SIL_ 

### <a name="brief-overview"></a>Kısa genel bakış

Şu anda işleme veya sıraya alınmış işi iptal et. Yalnızca çeviri başlatılmamış belgeler iptal edilecek.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'delete',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DeleteJob {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("DELETE", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("DELETE", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Git](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "DELETE"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="content-limits"></a>İçerik sınırları

Aşağıdaki tabloda belge çevirisi 'ne (Önizleme) göndereceğiniz verilerin sınırları listelenmektedir.

|Öznitelik | Sınır|
|---|---|
|Belge boyutu| ≤ 40 MB |
|Toplam dosya sayısı.|≤ 1000 |
|Toplu işteki toplam içerik boyutu | ≤ 250 MB|
|Toplu işteki hedef dillerin sayısı| ≤ 10 |
|Çeviri belleği dosyasının boyutu| ≤ 10 MB|

> [!NOTE]
> Yukarıdaki içerik sınırları, genel yayından önce değiştirilebilir.

## <a name="learn-more"></a>Daha fazla bilgi edinin

* [Translator v3 API başvurusu](../reference/v3-0-reference.md)
* [Dil desteği](../language-support.md)
* [Azure API Management abonelikler](../../../api-management/api-management-subscriptions.md).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel çevirici kullanarak özelleştirilmiş bir dil sistemi oluşturma](../custom-translator/overview.md)
>
>
