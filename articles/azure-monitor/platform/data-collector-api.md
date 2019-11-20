---
title: Azure Izleyici HTTP Veri Toplayıcı API 'SI | Microsoft Docs
description: Azure Izleyici HTTP Veri Toplayıcı API 'sini, REST API çağırabileceğiniz herhangi bir istemciden bir Log Analytics çalışma alanına JSON verisi gönderme eklemek için kullanabilirsiniz. Bu makalede, API 'nin nasıl kullanılacağı açıklanır ve farklı programlama dilleri kullanılarak verilerin nasıl yayımlanacağı gösterilmektedir.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/01/2019
ms.openlocfilehash: 136644dbcfe9e2835f799b284d21263913bc67b4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932585"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>HTTP Veri Toplayıcı API 'SI ile günlük verilerini Azure Izleyici 'ye gönderme (Genel Önizleme)
Bu makalede, Azure Izleyici 'ye bir REST API istemcisinden günlük verileri göndermek için HTTP Veri Toplayıcı API 'sinin nasıl kullanılacağı gösterilmektedir.  Betik veya uygulamanız tarafından toplanan verilerin nasıl biçimlendirileceğini, bir isteğe dahil edileceğini ve bu isteğin Azure Izleyici tarafından yetkilendirildiğini açıklar.  PowerShell, C#ve Python için örnek verilmiştir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Azure Izleyici HTTP Veri Toplayıcı API 'SI genel önizlemede.

## <a name="concepts"></a>Kavramlar
Azure Izleyici 'deki bir Log Analytics çalışma alanına günlük verileri göndermek için HTTP Veri Toplayıcı API 'sini, bir REST API çağırabilirler.  Bu, Azure Otomasyonu 'nda Azure veya başka bir buluttan yönetim verileri toplayan bir runbook olabilir veya günlük verilerini birleştirmek ve analiz etmek için Azure Izleyici kullanan alternatif bir yönetim sistemi olabilir.

Log Analytics çalışma alanındaki tüm veriler, belirli bir kayıt türüne sahip bir kayıt olarak depolanır.  JSON 'daki birden çok kayıt olarak HTTP Veri Toplayıcı API 'sine göndermek için verilerinizi biçimlendirin.  Verileri gönderdiğinizde, istek yükünde her kayıt için depoda tek bir kayıt oluşturulur.


![HTTP veri toplayıcısına genel bakış](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>İstek oluştur
HTTP Veri Toplayıcı API 'sini kullanmak için, JavaScript Nesne Gösterimi (JSON) ' de gönderilecek verileri içeren bir POST isteği oluşturursunuz.  Sonraki üç tablo, her istek için gereken öznitelikleri listeler. Makalenin ilerleyen kısımlarında, her bir özniteliği daha ayrıntılı bir şekilde açıklıyoruz.

### <a name="request-uri"></a>İstek URI 'SI
| Öznitelik | Özellik |
|:--- |:--- |
| Yöntem |Yayınla |
| KULLANıLMAMıŞSA |https://\<CustomerID\>. ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| İçerik türü |uygulama/json |

### <a name="request-uri-parameters"></a>İstek URI parametreleri
| Parametre | Açıklama |
|:--- |:--- |
| Ister |Log Analytics çalışma alanı için benzersiz tanımlayıcı. |
| Kaynak |API kaynak adı:/api/logs. |
| API sürümü |Bu istekle birlikte kullanılacak API sürümü. Şu anda 2016-04-01. |

### <a name="request-headers"></a>İstek üst bilgileri
| Üst bilgi | Açıklama |
|:--- |:--- |
| Yetkilendirme |Yetkilendirme imzası. Makalenin ilerleyen kısımlarında, HMAC-SHA256 üst bilgisi oluşturma hakkında bilgi edinebilirsiniz. |
| Günlük türü |Gönderilen verilerin kayıt türünü belirtin. Yalnızca harf, rakam ve alt çizgi (_) içerebilir ve 100 karakterden uzun olamaz. |
| x-MS-Tarih |İsteğin işlendiği tarih, RFC 1123 biçiminde. |
| x-MS-Azureresourceıd | Verilerin ilişkilendirilmesi gereken Azure kaynağının kaynak KIMLIĞI. Bu, [_Resourceıd](log-standard-properties.md#_resourceid) özelliğini doldurur ve verilerin [kaynak bağlamı](design-logs-deployment.md#access-mode) sorgularına dahil edilmesini sağlar. Bu alan belirtilmemişse, veriler kaynak bağlamı sorgularına dahil edilmez. |
| zaman oluşturulan alan | Veri öğesinin zaman damgasını içeren verilerdeki bir alanın adı. Bir alan belirtirseniz, bu durumda içeriği **TimeGenerated**için kullanılır. Bu alan belirtilmemişse, **TimeGenerated** için varsayılan değer, iletinin alınmasının zamanındır. İleti alanının içeriği, ISO 8601 biçiminde YYYY-MM-DDThh: mm: ssZ ' i izlemelidir. |

## <a name="authorization"></a>Yetkilendirme
Azure Izleyici HTTP Veri Toplayıcı API 'sine yönelik tüm istekler bir yetkilendirme üst bilgisi içermelidir. Bir isteğin kimliğini doğrulamak için isteği, isteği yapan çalışma alanının birincil veya ikincil anahtarıyla imzalamanız gerekir. Ardından, bu imzayı isteğin bir parçası olarak geçirin.   

Yetkilendirme üstbilgisinin biçimi aşağıdadır:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

Workspace *ID* , Log Analytics çalışma alanının benzersiz tanımlayıcısıdır. *İmza* , istekten oluşturulan ve sonra [SHA256 algoritması](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx)kullanılarak hesaplanan, [karma tabanlı bir ileti kimlik doğrulama kodu (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) . Ardından, Base64 kodlaması kullanarak bunu kodlayabilirsiniz.

**Sharedkey** imza dizesini kodlamak için bu biçimi kullanın:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

İmza dizesi örneği aşağıda verilmiştir:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

İmza dizeniz varsa, UTF-8 kodlu dizedeki HMAC-SHA256 algoritmasını kullanarak bunu kodlayın ve sonra sonucu Base64 olarak kodlayın. Şu biçimi kullanın:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Sonraki bölümlerdeki örneklerde, yetkilendirme üst bilgisi oluşturmanıza yardımcı olacak örnek kod bulunur.

## <a name="request-body"></a>İstek gövdesi
İletinin gövdesi JSON içinde olmalıdır. Aşağıdaki biçimde Özellik adı ve değer çiftleri içeren bir veya daha fazla kayıt içermelidir. Özellik adı yalnızca harf, rakam ve alt çizgi (_) içerebilir.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

Aşağıdaki biçimi kullanarak, birden çok kaydı tek bir istekte toplu olarak bir araya getirebilirsiniz. Tüm kayıtların aynı kayıt türü olması gerekir.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Kayıt türü ve özellikleri
Azure Izleyici HTTP Veri Toplayıcı API 'SI aracılığıyla veri gönderdiğinizde özel bir kayıt türü tanımlarsınız. Şu anda, diğer veri türleri ve çözümleri tarafından oluşturulan mevcut kayıt türlerine veri yazamıyoruz. Azure Izleyici gelen verileri okur ve sonra girdiğiniz değerlerin veri türleriyle eşleşen özellikler oluşturur.

Veri Toplayıcı API 'sine yönelik her istek, kayıt türü için adı olan bir **log-Type** üst bilgisi içermelidir. **_Cl** soneki, diğer günlük türlerinden özel bir günlük olarak ayırmak için girdiğiniz ada otomatik olarak eklenir. Örneğin, **Mynewrecordtype**adını girerseniz Azure Izleyici, **MyNewRecordType_CL**türünde bir kayıt oluşturur. Bu, Kullanıcı tarafından oluşturulan tür adları ve geçerli veya gelecekteki Microsoft çözümlerinde sevk edilen çakışmalar arasında çakışma olmamasını sağlamaya yardımcı olur.

Bir özelliğin veri türünü tanımlamak için, Azure Izleyici özellik adına bir sonek ekler. Bir özellik null değeri içeriyorsa, özelliği bu kayda dahil edilmez. Bu tabloda, özellik veri türü ve karşılık gelen sonek listelenmektedir:

| Özellik veri türü | Önekini |
|:--- |:--- |
| Dize |_s |
| Boole |_b |
| Çift |_D |
| Tarih/saat |_T |
| GUID (dize olarak depolanır) |_G |

Azure Izleyicisinin her özellik için kullandığı veri türü, yeni kaydın kayıt türünün zaten var olup olmadığına bağlıdır.

* Kayıt türü yoksa, Azure Izleyici yeni kayıt için her bir özelliğin veri türünü belirlemekte JSON tür çıkarımı kullanarak yeni bir tane oluşturur.
* Kayıt türü varsa, Azure Izleyici mevcut özellikleri temel alarak yeni bir kayıt oluşturmaya çalışır. Yeni kayıttaki bir özelliğin veri türü eşleşmiyorsa ve var olan türe dönüştürülemiyorsa veya kayıt mevcut olmayan bir özellik içeriyorsa, Azure Izleyici ilgili sonekine sahip yeni bir özellik oluşturur.

Örneğin, bu gönderim girdisi üç özelliklere sahip bir kayıt oluşturur, **number_d**, **boolean_b**ve **string_s**:

![Örnek kayıt 1](media/data-collector-api/record-01.png)

Daha sonra bu sonraki girişi, dizeler olarak biçimlendirilen tüm değerlerle gönderdiyseniz Özellikler değişmez. Bu değerler, varolan veri türlerine dönüştürülebilir:

![Örnek kayıt 2](media/data-collector-api/record-02.png)

Ancak, daha sonra bu sonraki gönderimi yaptıysanız, Azure Izleyici yeni **boolean_d** ve **string_d**özelliklerini oluşturacaktır. Bu değerler dönüştürülemez:

![Örnek kayıt 3](media/data-collector-api/record-03.png)

Daha sonra aşağıdaki girişi gönderdikten sonra, kayıt türü oluşturulmadan önce Azure Izleyici, **başarı_sayısı**, **boolean_s**ve **string_s**olmak üzere üç özellik içeren bir kayıt oluşturur. Bu girişte, ilk değerlerin her biri bir dize olarak biçimlendirilir:

![Örnek kayıt 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Ayrılmış Özellikler
Aşağıdaki özellikler ayrılmıştır ve özel bir kayıt türünde kullanılmamalıdır. Yükünüzü bu özellik adlarından herhangi birini içeriyorsa bir hata alırsınız.

- Kiracı

## <a name="data-limits"></a>Veri sınırları
Azure Izleyici veri toplama API 'sine gönderilen verilerin etrafında bazı kısıtlamalar vardır.

* Azure Izleyici veri toplayıcı API 'sine gönderi başına en fazla 30 MB. Bu, tek bir gönderi için boyut limiti. 30 MB 'ı aşan tek bir postadaki veriler, verileri daha küçük ölçekli parçalara ayırmak ve aynı anda göndermeniz gerekir.
* Alan değerleri için en fazla 32 KB sınırı. Alan değeri 32 KB 'tan büyükse, veriler kesilir.
* Belirli bir tür için önerilen en fazla alan sayısı 50 ' dir. Bu, kullanışlılık ve arama deneyimi açısından pratik bir limit değildir.  
* Bir Log Analytics çalışma alanındaki tablo yalnızca en fazla 500 sütunu destekler (Bu makalede alan olarak adlandırılır). 
* Sütun adı için en fazla karakter sayısı 500 ' dir.

## <a name="return-codes"></a>Dönüş kodları
HTTP durum kodu 200, isteğin işlenmek üzere alındığı anlamına gelir. Bu işlemin başarıyla tamamlandığını gösterir.

Bu tabloda, hizmetin döndürebileceğini belirten tüm durum kodları listelenmektedir:

| Kodlayın | Durum | Hata kodu | Açıklama |
|:--- |:--- |:--- |:--- |
| 200 |TAMAM | |İstek başarıyla kabul edildi. |
| 400 |Hatalı istek |Inactivecustomer |Çalışma alanı kapatıldı. |
| 400 |Hatalı istek |Invalidapiversion |Belirttiğiniz API sürümü hizmet tarafından tanınmadı. |
| 400 |Hatalı istek |Invalidcustomerıd |Belirtilen çalışma alanı KIMLIĞI geçersiz. |
| 400 |Hatalı istek |Invaliddataformat |Geçersiz JSON gönderildi. Yanıt gövdesinde hatanın nasıl çözümleneceği hakkında daha fazla bilgi bulunabilir. |
| 400 |Hatalı istek |Invalidlogtype |Belirtilen günlük türü özel karakterler veya Numerics içeriyor. |
| 400 |Hatalı istek |MissingApiVersion |API sürümü belirtilmedi. |
| 400 |Hatalı istek |MissingContentType |İçerik türü belirtilmedi. |
| 400 |Hatalı istek |MissingLogType |Gerekli değer günlük türü belirtilmedi. |
| 400 |Hatalı istek |UnsupportedContentType |İçerik türü **Application/JSON**olarak ayarlanmadı. |
| 403 |Yasak |Invalidauthorleştirme |Hizmet, isteğin kimliğini doğrulayamadı. Çalışma alanı KIMLIĞI ve bağlantı anahtarının geçerli olduğunu doğrulayın. |
| 404 |Bulunamadı | | Girilen URL yanlış ya da istek çok büyük. |
| 429 |Çok fazla Istek | | Hizmet, hesabınızdaki yüksek miktarda veri ile karşılaşıyor. Lütfen isteği daha sonra yeniden deneyin. |
| 500 |İç sunucu hatası |UnspecifiedError |Hizmet bir iç hatayla karşılaştı. Lütfen isteği yeniden deneyin. |
| 503 |Hizmet kullanılamıyor |ServiceUnavailable |Hizmet şu anda istekleri almak için kullanılamıyor. Lütfen isteğinizi yeniden deneyin. |

## <a name="query-data"></a>Verileri sorgulama
Azure Izleyici HTTP Veri Toplayıcı API 'SI tarafından gönderilen verileri sorgulamak için, belirttiğiniz **LogType** değerine **eşit olan,** **_CL**ile eklenen kayıtları arayın. Örneğin, **Mycustomlog**kullandıysanız, `MyCustomLog_CL`tüm kayıtları döndürür.

## <a name="sample-requests"></a>Örnek istekler
Sonraki bölümlerde, farklı programlama dilleri kullanarak Azure Izleyici HTTP Veri Toplayıcı API 'sine nasıl veri göndertireceğiz örnekleri bulacaksınız.

Her örnek için, yetkilendirme üstbilgisinin değişkenlerini ayarlamak için şu adımları uygulayın:

1. Azure portal, Log Analytics çalışma alanınızı bulun.
2. **Gelişmiş ayarlar** ' ı ve ardından **bağlı kaynakları**seçin.
2. **Çalışma alanı kimliği**' nin sağında, Kopyala simgesini seçin ve ardından KIMLIĞI **müşteri kimliği** değişkeninin değeri olarak yapıştırın.
3. **Birincil anahtarın**sağında, Kopyala simgesini seçin ve sonra kimliği **paylaşılan anahtar** değişkeninin değeri olarak yapıştırın.

Alternatif olarak, günlük türü ve JSON verileri için değişkenleri değiştirebilirsiniz.

### <a name="powershell-sample"></a>PowerShell örneği
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>C# örneği
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Python 2 örneği
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```
## <a name="alternatives-and-considerations"></a>Alternatifler ve önemli noktalar
Veri Toplayıcı API 'SI, Azure günlüklerine serbest biçimli veriler toplamak için gereksinimlerinizin çoğunu kapsasa da, API 'nin bazı sınırlamalarını aşmak için alternatif gerekebilecek örnekler vardır. Tüm seçenekleriniz aşağıda verilmiştir:

| Yapıyı | Açıklama | En uygun |
|---|---|---|
| [Özel olaylar](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): APPLICATION INSIGHTS yerel SDK tabanlı alma | Genellikle uygulamanızdaki bir SDK aracılığıyla belgelenmiş olan Application Insights özel olaylar aracılığıyla özel veri göndermenize olanak sağlar. | <ul><li> Uygulamanızda oluşturulan, ancak varsayılan veri türlerinden biri (istekler, bağımlılıklar, özel durumlar vb.) aracılığıyla SDK tarafından çekilmemiş veriler.</li><li> Application Insights ' deki diğer uygulama verileriyle en sık bağıntılı veriler </li></ul> |
| Azure Izleyici günlüklerinde veri toplayıcı API 'SI | Azure Izleyici günlüklerinde veri toplayıcı API 'SI, verileri almak için tamamen açık uçlu bir yoldur. JSON nesnesinde biçimlendirilen tüm veriler buraya gönderilebilir. Gönderildikten sonra işlenir ve günlüklerde bulunan diğer verilerle bağıntılı veya diğer Application Insights verilerine yönelik Günlükler kullanılabilir. <br/><br/> Verileri bir Azure Blob blob 'una dosya olarak yüklemek oldukça kolaydır, bu dosyaların nerede işleneceğini ve Log Analytics karşıya yükleneceğini buradan yükleyebilirsiniz. Bu tür bir işlem hattının örnek bir uygulanması için lütfen [Bu](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) makaleye bakın. | <ul><li> Application Insights içinde belgelenmiş bir uygulama içinde oluşturulmayan veriler.</li><li> Arama ve olgu tablolarını, başvuru verilerini, ön toplanmış istatistikleri ve benzeri örnekleri içerir. </li><li> Diğer Azure Izleyici verileri (Application Insights, diğer günlük veri türleri, Güvenlik Merkezi, kapsayıcılar/VM 'Ler için Azure Izleyici vb.) için çapraz başvurulacak verilere yöneliktir. </li></ul> |
| [Azure Veri Gezgini](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview) | Azure Veri Gezgini (ADX), Application Insights Analytics ve Azure Izleyici günlüklerini destekleyen veri platformudur. Genel kullanıma sunuldu ("GA"), veri platformunu ham biçimde kullanmak küme üzerinde (RBAC, bekletme oranı, şema vb.) bir bütün esnekliği (yönetim yükünü gerektirir) sağlar. ADX [CSV, TSV ve JSON](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master) dosyaları gibi birçok alma [seçeneği](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview#ingestion-methods) sunar. | <ul><li> Application Insights veya günlükleri altındaki diğer verilerle bağıntılı olmayacak veriler. </li><li> Azure Izleyici günlüklerinde gelişmiş alma veya işleme özellikleri gerektiren veriler bugün kullanılamıyor. </li></ul> |


## <a name="next-steps"></a>Sonraki adımlar
- Log Analytics çalışma alanından verileri almak için [günlük ARAMASı API](../log-query/log-query-overview.md) 'sini kullanın.

- Azure Izleyici 'de Logic Apps iş akışını kullanarak [Veri Toplayıcı API 'si ile veri işlem hattı oluşturma](create-pipeline-datacollector-api.md) hakkında daha fazla bilgi edinin.
