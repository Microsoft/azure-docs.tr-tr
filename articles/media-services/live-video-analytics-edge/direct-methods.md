---
title: IoT Edge-Azure 'da canlı video Analizte doğrudan Yöntemler kullanma
description: IoT Edge canlı video analizi, çeşitli doğrudan yöntemler sunar. Doğrudan Yöntemler, bu konuda açıklanan kuralları temel alır.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 56fbf565af45a9b3877ff8d6c48a56713ddc3da6
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076998"
---
# <a name="direct-methods"></a>Doğrudan yöntemler

IoT Edge canlı video analizi, IoT Hub çağrılabilen çeşitli doğrudan yöntemler sunar. Doğrudan Yöntemler, başarılı veya başarısız olmaları durumunda (Kullanıcı tarafından belirtilen zaman aşımından sonra) bir HTTP çağrısına benzer bir cihazla bir istek-yanıt etkileşimini temsil eder. Bu yaklaşım, cihazın yanıt verip verememesine bağlı olarak farklılık gösteren acil eylem senaryoları için yararlıdır. Daha fazla bilgi için bkz. [IoT Hub doğrudan yöntemleri anlama ve çağırma](../../iot-hub/iot-hub-devguide-direct-methods.md).

Bu konu, bu yöntemleri ve kuralları açıklamaktadır.

## <a name="conventions"></a>Kurallar

Doğrudan yöntemler aşağıdaki kurallara dayalıdır:

1. Doğrudan metotlarda, BIRINCIL olarak belirtilen bir sürüm vardır. IKINCIL biçim (aşağıdaki örnekte gösterildiği gibi):

    ```
    {
        "methodName": "GraphTopologySet",
        "payload": {
            // API version matches matches module MAJOR.MINOR version
            "@apiVersion": "1.0"
            //payload here…
        }
    }
    ```

2. IoT Edge modülündeki canlı video analizinin belirli bir sürümü, geçerli sürümüne kadar olan tüm doğrudan yöntemleri destekleyecektir. Örneğin, modül sürümü 1,3, 1,3, 1,2, 1,1 ve 1,0 sürümleriyle doğrudan metotları destekleyecektir.
3. Tüm doğrudan Yöntemler zaman uyumludur.
4. Hata sonuçları OData hata şemasını izler.
5. Adlar aşağıdaki kısıtlamaları gözlemlemelidir:
    
    * Yalnızca alfasayısal karakterler ve kısa çizgi ile başlamaz ve bitmez
    * Boşluk yok
    * En fazla 32 karakter

### <a name="example"></a>Örnek

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "Graph instance is not found."
    }
  }
}
```    

### <a name="top-level-error-codes"></a>Üst düzey hata kodları     

|Durum |Kod   |İleti|
|---|---|---|
|400|   Işlemindeki hatalı istek| İstek geçerli değil|
|400|   Invalidresource|    Kaynak geçerli değil|
|400|   Invalidversion| API sürümü geçerli değil|
|402|   Connectivitygerekli    |Edge modülünün düzgün çalışması için bulut bağlantısı gerekir.|
|404|   NotFound    |Kaynak bulunamadı|
|409|   Çakışma|   İşlem çakışması|
|500|   InternalServerError|    İç sunucu hatası|
|503|   Sunucu meşgul| Sunucu meşgul|

### <a name="detailed-error-codes"></a>Ayrıntılı hata kodları

Grafik modülü doğrulamaları gibi ayrıntılı doğrulama hataları hata ayrıntıları olarak eklenir:

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "InvalidResource",
      "message": "The resource format is invalid.",
      "details": [
        {
          "code": "RtspEndpointUrlInvalidScheme",
          "target": "$.Properties.Sources[0]",
          "message": "Uri scheme 'http' is not valid for an RTSP source endpoint. Valid values are: rtsp"
        },
        {
          "code": "PropertyValueInvalidPattern",
          "target": "$.Properties.Sinks[0].AssetNamePattern",
          "message": "The value must match the regular expression '^[^<>%&:\\\\\\/?*+.']{1,260}$'"
        }
      ]
    }
  }
}
}
```

|Durum|    Ayrıntılı kod   |Description|
|---|---|---|
|400|   GraphValidationError|   Döngüler veya bölümlendirme gibi genel grafik hataları.|
|400|   ModuleValidationError|  Modüle özgü doğrulama hataları.|
|409|   Graphtopologyuse| Grafik topolojisine bir veya daha fazla grafik örneği tarafından hala başvuruluyor.|
|409|   OperationNotAllowedInState| İstenen işlem geçerli durumda gerçekleştirilemiyor.|
|409|   ResourceValidationError|    Başvurulan kaynak (örnek: varlık) geçerli bir durumda değil.|

## <a name="details"></a>Ayrıntılar  

### <a name="graphtopologyget"></a>Graphtopologyıget

Bu doğrudan yöntem tek bir grafik topolojisi alır.

#### <a name="request"></a>İstek

```
{
    "methodName": "GraphTopologyGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Yanıt

```
{
    "status": 200,
    "payload": {
        "name": "CaptureAndRecord",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Durum kodları

| Koşul | Durum kodu | Ayrıntılı hata kodu |
|--|--|--|
| Varlık bulundu | 200 | Yok |
| Genel Kullanıcı hataları | 400 aralığı |  |
| Varlık bulunamadı | 404 |  |
| Genel sunucu hataları | 500 aralığı |  |

### <a name="graphtopologyset"></a>Graphtopologyıset

Verilen ad veya güncelleştirmeler ile aynı ada sahip mevcut bir tane varsa, tek bir topoloji oluşturur.

Anahtar yönleri:

* Topoloji, kendisine başvuran bir grafik olmadığından, serbestçe güncelleştirilmemiş olabilir.
* Tüm başvuruda bulunan grafikler devre dışı bırakıldığında topoloji serbestçe yüklenebilir:

    * Yeni eklenen parametreler varsayılan değerlere sahip
    * Kaldırılan parametrelere hiçbir grafik tarafından başvurulmuyor
* Etkin grafikler varsa topoloji güncelleştirmelerine izin verilmez

#### <a name="request"></a>İstek

```
{
    "methodName": "GraphTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord",
        "properties": {
            // Desired graph topology properties
        }
    }
}
```

#### <a name="response"></a>Yanıt

```
{
    "status": 201,
    "payload": {
        "name": " CaptureAndRecord ",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Durum kodları

|Koşul  |Durum kodu    |Ayrıntılı hata kodu|
|---|---|---|
Var olan varlık güncelleştirildi |200|   Yok|
Yeni varlık oluşturuldu  |201|   Yok|
Genel Kullanıcı hataları |400 aralığı  ||
Grafik doğrulama hataları |400    |GraphValidationError|
Modül doğrulama hataları|   400 |ModuleValidationError|
Genel sunucu hataları   |500 aralığı  ||

### <a name="graphtopologydelete"></a>Graphtopologyıdelete

Tek bir grafik topolojisini siler.

#### <a name="request"></a>İstek

```
{
    "methodName": "GraphTopologyDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Yanıt

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Durum kodları

| Koşul | Durum kodu | Ayrıntılı hata kodu |
|--|--|--|
| Varlık silindi | 200 | Yok |
| Varlık bulunamadı | 204 | Yok |
| Genel Kullanıcı hataları | 400 aralığı |  |
| Grafik topolojisine bir veya daha fazla grafik örneği tarafından başvuruluyor | 409 | Graphtopologyuse |
| Genel sunucu hataları | 500 aralığı |  |

### <a name="graphtopologylist"></a>Graphtopologyılist

Filtre ölçütleriyle eşleşen tüm grafik topolojilerinin bir listesini alır.

#### <a name="request"></a>İstek

```
{
    "methodName": "GraphTopologyList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Yanıt

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Filtre desteği

|İşlem |Alan (lar)    |İşleçler|
|---|---|---|
|$orderby|name  |ASC|


#### <a name="status-codes"></a>Durum kodları

| Koşul | Durum kodu | Ayrıntılı hata kodu |
|--|--|--|
| Başarılı | 200 | Yok |
| Genel Kullanıcı hataları | 400 aralığı |  |
| Genel sunucu hataları | 500 aralığı |  |

### <a name="graphinstanceget"></a>Graphınstanceget

Tek bir grafik örneği alır:

#### <a name="request"></a>İstek

```
{
    "methodName": "GraphInstanceGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Yanıt

```
{
    "status": 200,
    "payload": {
        "name": "Camera001",
        "properties": {
            // Complete graph instance
        }
    }
}
```

#### <a name="status-codes"></a>Durum kodları

| Koşul | Durum kodu | Ayrıntılı hata kodu |
|--|--|--|
| Varlık bulundu | 200 | Yok |
| Genel Kullanıcı hataları | 400 aralığı |  |
| Varlık bulunamadı | 404 |  |
| Genel sunucu hataları | 500 aralığı |  |

### <a name="graphinstanceset"></a>Graphınstanceset

Verilen ad veya güncelleştirmeler ve aynı ada sahip mevcut bir tane varsa, tek bir grafik örneği oluşturur.

Anahtar yönleri:

* Grafik örneği, "devre dışı" durumundayken serbestçe güncelleştirilemeyebilir.

    * Grafik her güncelleştirildiği zamanda yeniden onaylanır.
* Grafik "etkin olmayan" durumda olmadığında grafik örneği güncelleştirmeleri kısmen kısıtlıdır.
* Etkin grafiklerde grafik örneği güncelleştirmelerine izin verilmez.

#### <a name="request"></a>İstek

```
{
"methodName": "GraphInstanceSet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001",
        "properties": {
            // Desired graph instance properties
        }
    }
}
```

#### <a name="response"></a>Yanıt

````
{
    "status": 201,
    "payload": {
        "name": " Camera001",
        "properties": {
            // Complete graph instance
        }   
    }
}
````

#### <a name="status-codes"></a>Durum kodları

| Koşul | Durum kodu | Ayrıntılı hata kodu |
|--|--|--|
| Var olan varlık güncelleştirildi | 200 | Yok |
| Yeni varlık oluşturuldu | 201 | Yok |
| Genel Kullanıcı hataları | 400 aralığı |  |
| Grafik doğrulama hataları | 400 | GraphValidationError |
| Modül doğrulama hataları | 400 | ModuleValidationError |
| Kaynak doğrulama hataları | 409 | ResourceValidationError |
| Genel sunucu hataları | 500 aralığı |  | 

### <a name="graphinstancedelete"></a>Graphınstancedelete

Tek bir grafik örneğini siler.

Anahtar yönleri:

* Yalnızca devre dışı bırakılmış grafikler silinebilir.

#### <a name="request"></a>İstek

```
{
    "methodName": "GraphInstanceDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Yanıt

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Durum kodları

| Koşul | Durum kodu | Ayrıntılı hata kodu |
|--|--|--|
| Grafik başarıyla silindi | 200 | Yok |
| Grafik bulunamadı | 204 | Yok |
| Genel Kullanıcı hataları | 400 aralığı |  |
| Grafik "durduruldu" durumunda değil | 409 | OperationNotAllowedInState |
| Genel sunucu hataları | 500 aralığı |  |

### <a name="graphinstancelist"></a>Graphınstancelist

Bu, Graphtopologyılist öğesine benzerdir. Grafik örneklerinin numaralandırılması için kullanılmasına olanak sağlar.
Filtre ölçütleriyle eşleşen tüm grafik örneklerinin listesini alır.

#### <a name="request"></a>İstek

```
{
    "methodName": "GraphInstanceList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Yanıt

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Filtre desteği

|İşlem  |   Alan (lar)|   İşleçler|
|---|---|---|
|$orderby|  name|   ASC|

#### <a name="status-codes"></a>Durum kodları

| Koşul | Durum kodu | Ayrıntılı hata kodu |
|--|--|--|
| Başarılı | 200 | Yok |
| Genel Kullanıcı hataları | 400 aralığı |  |
| Genel sunucu hataları | 500 aralığı |  |

### <a name="graphinstanceactivate"></a>Graphınstanceactivate

Tek bir grafik örneğini etkinleştirir. 

Anahtar yönleri

* Yöntem yalnızca Graph etkinleştirildiğinde döndürülür 
* Graph, etkinleştirilirken "etkinleştirme" durumunu varsayar.

    * Grafikteki bir liste/get işlemi, grafiği uygun duruma döndürür.
* Teklik

    * "Etkinleştiriliyor" durumundaki bir grafiğin başlatılması, grafiğin devre dışı bırakıldığın (yani, Graph etkinleştirilinceye kadar çağrı blokları) aynı şekilde davranır.
    * "Etkin" durumdaki bir grafiğin etkinleştirilmesi anında başarıyla döndürülür.

#### <a name="request"></a>İstek

```
{
    "methodName": "GraphInstanceActivate",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Yanıt

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Durum kodları

| Koşul | Durum kodu | Ayrıntılı hata kodu |
|--|--|--|
| Grafik başarıyla etkinleştirildi | 200 | Yok |
| Yeni varlık oluşturuldu | 201 | Yok |
| Genel Kullanıcı hataları | 400 aralığı |  |
| Modül doğrulama hataları | 400 | ModuleValidationError |
| Kaynak doğrulama hataları | 409 | ResourceValidationError |
| Grafik devre dışı bırakılıyor durumunda | 409 | OperationNotAllowedInState |
| Genel sunucu hataları | 500 aralığı |  |

### <a name="graphinstancedeactivate"></a>Graphınstancedeactivate

Tek bir grafik örneğini devre dışı bırakır. Bir grafiğin devre dışı bırakılması, medya işlemeyi düzgün bir şekilde devre dışı bırakır ve uygun olduğunda, Edge üzerinde depolanan tüm olayların ve medya geçişli işlemleri buluta teslim edilmesini sağlar.

Anahtar yönleri:

* Yöntem yalnızca grafik devre dışı bırakıldığında döndürülür
* Grafik devre dışı bırakılırken "devre dışı bırakma" durumunu varsayar.

    * Grafikteki bir liste/get işlemi, grafiği uygun duruma döndürür.
    * Durdur yalnızca tüm medya buluta yüklendiğinde tamamlanır.
* Teklik

    * "Devre dışı bırakma" durumundaki bir grafiğin devre dışı bırakılması, grafiğin devre dışı bırakıldığın (yani, Graph devre dışı bırakılana kadar çağrı blokları) aynı şekilde davranır.
    * "Etkin olmayan" durumdaki bir grafiğin devre dışı bırakılması hemen başarıyla geri döndürülür.

#### <a name="request"></a>İstek

```
{
    "methodName": "GraphInstanceDeactivate",
    // A high response timeout is recommended here since there might be data movement    // being executed as part of this operation
    "responseTimeoutInSeconds": 300,
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Yanıt

```
{
    "status": 200,
    "payload": null
}
```

| Koşul | Durum kodu | Ayrıntılı hata kodu |
|--|--|--|
| Grafik başarıyla etkinleştirildi | 200 | Yok |
| Yeni varlık oluşturuldu | 201 | Yok |
| Genel Kullanıcı hataları | 400 aralığı |  |
| Grafik etkinleştiriliyor durumunda | 409 | OperationNotAllowedInState |
| Genel sunucu hataları | 500 aralığı |  |

## <a name="next-steps"></a>Sonraki adımlar

[Modül İkizi yapılandırma şeması](module-twin-configuration-schema.md)
