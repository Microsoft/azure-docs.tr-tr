---
title: Azure Işlevleri HTTP çıkış bağlamaları
description: Azure Işlevleri 'nde HTTP yanıtlarını döndürme hakkında bilgi edinin.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 6caf3847971c2dd39f1eea0a921891e4440373a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727476"
---
# <a name="azure-functions-http-output-bindings"></a>Azure Işlevleri HTTP çıkış bağlamaları

Http istek göndericisine yanıt vermek için HTTP çıkış bağlamasını kullanın. Bu bağlama bir HTTP tetikleyici gerektirir ve tetikleyicinin isteğiyle ilişkilendirilen yanıtı özelleştirme imkanı sunar.

HTTP ile tetiklenen bir işlev için varsayılan dönüş değeri:

- `HTTP 204 No Content` 2. x ve üzeri Işlevlerde boş bir gövdele
- `HTTP 200 OK` 1. x Işlevlerinde boş bir gövdesiyle

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, dosyasında *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır. C# sınıf kitaplıkları için, özelliklerde bu *function.js* karşılık gelen öznitelik özellikleri yoktur.

|Özellik  |Açıklama  |
|---------|---------|
| **türüyle** |Olarak ayarlanmalıdır `http` . |
| **Görünüm** | Olarak ayarlanmalıdır `out` . |
| **ada** | Yanıt için işlev kodunda kullanılan değişken adı veya `$return` dönüş değerini kullanmak. |

## <a name="usage"></a>Kullanım

HTTP yanıtı göndermek için, dil standardı yanıt düzenlerini kullanın. C# veya C# komut dosyasında, işlev dönüş türü veya ' nu yapın `IActionResult` `Task<IActionResult>` . C# ' de, dönüş değeri özniteliği gerekli değildir.

Örneğin, bkz. [tetikleyici örneği](./functions-bindings-http-webhook-trigger.md#example).

## <a name="hostjson-settings"></a>Ayarlar üzerinde host.js

Bu bölümde, 2. x ve üzeri sürümlerde bu bağlama için kullanılabilen genel yapılandırma ayarları açıklanmaktadır. Aşağıdaki dosyada örnek host.js, bu bağlamanın yalnızca sürüm 2. x + ayarlarını içerir. 2. x ve daha ötesi sürümlerindeki genel yapılandırma ayarları hakkında daha fazla bilgi için bkz. [ Azure işlevleri için başvuruhost.js](functions-host-json.md).

> [!NOTE]
> 1. x Işlevleri içindeki host.jsbaşvurusu için bkz. [ Azure işlevleri için başvuru üzerindehost.js, 1. x](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Özellik  |Varsayılan | Description |
|---------|---------|---------| 
| customHeaders|yok|HTTP yanıtında özel üstbilgiler ayarlamanıza olanak sağlar. Önceki örnek, `X-Content-Type-Options` içerik türü algılaması olmaması için üst bilgiyi yanıta ekler. |
|dynamicThrottlesEnabled|değeri<sup>\*</sup>|Bu ayar etkinleştirildiğinde, istek işleme işlem hattının benzer bir şekilde sistem performans sayaçlarını denetlemesini sağlar `connections/threads/processes/memory/cpu/etc` ve bu sayaçlardan herhangi biri yerleşik yüksek eşikten (%80%) olursa, sayaçlar `429 "Too Busy"` normal düzeylere dönene kadar istekler yanıt vererek reddedilir.<br/><sup>\*</sup>Bir tüketim planında varsayılan değer `true` . Adanmış bir planda varsayılan değer `false` .|
|HSTS|etkin değil|, `isEnabled` Olarak ayarlandığında `true` , [ `HstsOptions` sınıfında](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions)tanımlandığı şekilde [.NET Core 'un http katı taşıma güvenliği (HSTS) davranışı](/aspnet/core/security/enforcing-ssl?tabs=visual-studio#hsts) zorlanır. Yukarıdaki örnek ayrıca [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) özelliği 10 gün olarak ayarlar. Desteklenen özellikleri `hsts` şunlardır: <table><tr><th>Özellik</th><th>Açıklama</th></tr><tr><td>Excludedkonakları</td><td>HSTS üstbilgisinin eklendiği ana bilgisayar adlarının dize dizisi.</td></tr><tr><td>includeSubDomains</td><td>Strict-Transport-Security üstbilgisinin ıncludealt etki alanı parametresinin etkinleştirilip etkinleştirilmeyeceğini gösteren Boolean değer.</td></tr><tr><td>maxAge</td><td>Strict-Transport-Security üstbilgisinin Max-Age parametresini tanımlayan dize.</td></tr><tr><td>preload</td><td>Strict-Transport-Security üstbilgisinin preload parametresinin etkin olup olmadığını gösteren Boolean.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|Paralel olarak yürütülen HTTP işlevlerinin maksimum sayısı. Bu değer, kaynak kullanımının yönetilmesine yardımcı olabilecek eşzamanlılık denetlemenize olanak tanır. Örneğin, eşzamanlılık çok yüksek olduğunda sorunlara yol açacağından çok sayıda sistem kaynağı (bellek/CPU/yuva) kullanan bir HTTP işleviniz olabilir. Ya da bir üçüncü taraf hizmetine giden istekleri yapan bir işleviniz olabilir ve bu çağrıların hız sınırlı olması gerekir. Bu durumlarda, burada bir kısıtlama uygulanması yardımcı olabilir. <br/><sup>*</sup>Tüketim planı için varsayılan değer 100 ' dir. Adanmış bir plan için varsayılan değer sınırsız ( `-1` ) olur.|
|maxOutstandingRequests|200<sup>\*</sup>|Belirli bir zamanda tutulan bekleyen istek sayısı üst sınırı. Bu sınır, kuyruğa alınmış ancak yürütmeyi başlatmayan isteklerin yanı sıra devam eden yürütmeler içerir. Bu sınırın üzerindeki tüm gelen istekler, 429 "çok meşgul" yanıtıyla reddedilir. Bu, çağıranların zamana dayalı yeniden deneme stratejileri kullanmasına izin verir ve ayrıca en fazla istek gecikme sürelerini denetlemenize yardımcı olur. Bu, yalnızca betik ana bilgisayar yürütme yolu içinde oluşan kuyruğu denetler. ASP.NET istek kuyruğu gibi diğer kuyruklar da etkin olmaya devam eder ve bu ayardan etkilenmez. <br/><sup>\*</sup>Tüketim planı için varsayılan değer 200 ' dir. Adanmış bir plan için varsayılan değer sınırsız ( `-1` ) olur.|
|routePrefix|api|Tüm yollar için geçerli olan rota öneki. Varsayılan ön eki kaldırmak için boş bir dize kullanın. |

## <a name="next-steps"></a>Sonraki adımlar

- [HTTP isteğinden bir işlev çalıştırma](./functions-bindings-http-webhook-trigger.md)