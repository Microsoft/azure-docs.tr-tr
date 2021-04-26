---
title: Azure API Management erişim kısıtlama ilkeleri | Microsoft Docs
description: Azure API Management 'de kullanıma sunulan erişim kısıtlama ilkeleri hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: vladvino
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.topic: article
ms.date: 02/26/2021
ms.author: apimpm
ms.openlocfilehash: 882d96271b6976db1ffc0dde181d5699c5cc27de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688255"
---
# <a name="api-management-access-restriction-policies"></a>API Management erişim kısıtlama ilkeleri

Bu konu, aşağıdaki API Management ilkelerine yönelik bir başvuru sağlar. İlke ekleme ve yapılandırma hakkında daha fazla bilgi için bkz. [API Management ilkeleri](./api-management-policies.md).

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a> Erişim kısıtlama ilkeleri

-   [Http üstbilgisini denetle](#CheckHTTPHeader) -http üstbilgisinin varlığını ve/veya değerini uygular.
-   [Abonelik ile çağrı hızını sınırla](#LimitCallRate) -çağrı hızını her abonelik IÇIN sınırlayarak API kullanım artışlarını engeller.
-   [Anahtara göre çağrı hızını sınırla](#LimitCallRateByKey) -çağrı hızını anahtar TEMELINDE sınırlayarak API kullanım artışlarını engeller.
-   Belirli IP adreslerinden ve/veya adres aralıklarından çağrı [IP 'leri filtrelerini kısıtla](#RestrictCallerIPs) (izin verir/reddeder).
-   [Kullanım kotasını aboneliğe göre ayarla](#SetUsageQuota) -yenilenebilir bir veya yaşam süresi araması hacmi ve/veya bant genişliği kotasını abonelik başına temelinde zorlamanıza olanak sağlar.
-   [Anahtar temelinde kullanım kotasını ayarlama](#SetUsageQuotaByKey) -bir yenilenebilir veya yaşam süresi çağrı hacmi ve/veya bant genişliği kotasını anahtar başına temelinde zorlamanıza olanak sağlar.
-   [JWT 'Yi doğrula](#ValidateJWT) -BELIRTILEN bir http üst bilgisinden veya belirtilen sorgu parametresinden AYıKLANAN bir JWT 'ın varlığını ve geçerliliğini zorlar.

> [!TIP]
> Farklı amaçlar için farklı kapsamlarda erişim kısıtlama ilkeleri kullanabilirsiniz. Örneğin, ilkeyi API düzeyine uygulayarak tüm API 'yi AAD kimlik doğrulamasıyla güvenli hale getirebilirsiniz `validate-jwt` veya API işlem düzeyine uygulayabilir ve `claims` daha ayrıntılı denetim için kullanabilirsiniz.

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a> HTTP üstbilgisini denetle

`check-header`Bir isteğin belirtilen BIR http üst bilgisine sahip olmasını zorlamak için ilkeyi kullanın. İsteğe bağlı olarak, başlığın belirli bir değere sahip olup olmadığını veya bir dizi izin verilen değer olup olmadığını kontrol edebilirsiniz. Denetim başarısız olursa, ilke istek işlemeyi sonlandırır ve ilke tarafından belirtilen HTTP durum kodunu ve hata iletisini döndürür.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>Örnek

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>Öğeler

| Ad         | Açıklama                                                                                                                                   | Gerekli |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| onay üst bilgisi | Kök öğe.                                                                                                                                 | Yes      |
| değer        | İzin verilen HTTP üst bilgisi değeri. Birden çok değer öğesi belirtildiğinde, değerlerden herhangi biri bir eşleşme olduğunda denetim başarı olarak kabul edilir. | No       |

### <a name="attributes"></a>Öznitelikler

| Ad                       | Açıklama                                                                                                                                                            | Gerekli | Varsayılan |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| başarısız-denetim-hata-ileti | Üst bilgi yoksa veya geçersiz bir değere sahip değilse, HTTP yanıt gövdesinde döndürülecek hata iletisi. Bu ileti, doğru bir şekilde kaçış karakteri içermelidir. | Yes      | Yok     |
| başarısız-Check-httpcode      | Üst bilgi yoksa veya geçersiz bir değere sahip değilse döndürülecek HTTP durum kodu.                                                                                        | Yes      | Yok     |
| üst bilgi adı                | Denetlenecek HTTP üstbilgisinin adı.                                                                                                                                  | Yes      | Yok     |
| Yoksay-büyük harf                | True veya false olarak ayarlanabilir. Üst bilgi değeri, kabul edilebilir değerler kümesiyle karşılaştırıldığı zaman true olarak ayarlanırsa, bu durum yoksayılır.                                    | Yes      | Yok     |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden

-   **İlke kapsamları:** tüm kapsamlar

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a> Çağrı hızını aboneliğe göre sınırla

`rate-limit`İlke, belirli bir süre için çağrı oranını belirtilen bir sayı ile sınırlayarak, her abonelik IÇIN API kullanım artışlarını engeller. Çağrı hızı aşıldığında, çağıran bir `429 Too Many Requests` yanıt durum kodu alır.

> [!IMPORTANT]
> Bu ilke, her ilke belgesi için yalnızca bir kez kullanılabilir.
>
> [İlke ifadeleri](api-management-policy-expressions.md) bu ilkenin ilke özniteliklerinin hiçbirinde kullanılamaz.

> [!CAUTION]
> Daraltma mimarisinin dağıtılmış doğası nedeniyle, hız sınırlaması hiçbir şekilde tamamen doğru değildir. Yapılandırılan ve gerçek izin verilen istek sayısı arasındaki fark, istek hacmi ve hızı, arka uç gecikmesi ve diğer faktörlere göre değişiklik gösterir.

> [!NOTE]
> Hız sınırları ve Kotalar arasındaki farkı anlamak için [bkz. oran limitleri ve kotaları.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>İlke ekstresi

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" 
        retry-after-header-name="header name" 
        retry-after-variable-name="policy expression variable name"
        remaining-calls-header-name="header name"  
        remaining-calls-variable-name="policy expression variable name"
        total-calls-header-name="header name"/>
    </api>
</rate-limit>
```

### <a name="example"></a>Örnek

Aşağıdaki örnekte, abonelik başına hız sınırı 90 saniye başına 20 çağrı olur. Her ilke yürütmeden sonra, zaman döneminde izin verilen kalan çağrılar değişkende depolanır `remainingCallsPerSubscription` .

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" remaining-calls-variable-name="remainingCallsPerSubscription"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Öğeler

| Ad       | Açıklama                                                                                                                                                                                                                                                                                              | Gerekli |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| hız limiti | Kök öğe.                                                                                                                                                                                                                                                                                            | Yes      |
| api        | Ürünün içindeki API 'lerde çağrı hızı sınırı uygulamak için bu öğelerden bir veya daha fazlasını ekleyin. Ürün ve API çağrısı hız limitleri bağımsız olarak uygulanır. Ya da ile API 'ye başvurulabilir `name` `id` . Her iki öznitelik de sağlanmışsa, `id` kullanılır ve `name` yok sayılır.                    | No       |
| operation  | Bir API içindeki işlemlerde çağrı hızı sınırı uygulamak için bu öğelerden bir veya daha fazlasını ekleyin. Ürün, API ve işlem çağrısı hız limitleri bağımsız olarak uygulanır. İşlem ya da ile veya arasında başvuru yapılabilir `name` `id` . Her iki öznitelik de sağlanmışsa, `id` kullanılır ve `name` yok sayılır. | No       |

### <a name="attributes"></a>Öznitelikler

| Ad           | Açıklama                                                                                           | Gerekli | Varsayılan |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | Hız sınırının uygulanacağı API 'nin adı.                                                | Yes      | Yok     |
| aramalarda          | İçinde belirtilen zaman aralığında izin verilen en fazla toplam çağrı sayısı `renewal-period` . | Yes      | Yok     |
| yenileme süresi | Kayan pencerenin, izin verilen istek sayısının ' de belirtilen değeri aşması veya aşmaması için saniye cinsinden uzunluğu `calls` .                                              | Yes      | Yok     |
| retry-After-Header-Name    | Belirtilen çağrı hızı aşıldıktan sonra, değeri önerilen yeniden deneme aralığı olan bir yanıt üstbilgisinin adı. |  No | Yok  |
| retry-After-variable-name    | Belirtilen çağrı hızı aşıldıktan sonra önerilen yeniden deneme aralığını saniye cinsinden depolayan bir ilke ifadesi değişkeninin adı. |  No | Yok  |
| kalan-aramalar-üst bilgi-adı    | Her ilke yürütmeden sonra değeri belirtilen yanıt üstbilgisinin adı, içinde belirtilen zaman aralığı için izin verilen kalan çağrı sayısıdır `renewal-period` . |  No | Yok  |
| kalan-çağrı-değişken adı    | Her ilke yürütmesi sonrasında bir ilke ifadesi değişkeninin adı, içinde belirtilen zaman aralığı için izin verilen kalan çağrı sayısını saklar `renewal-period` . |  No | Yok  |
| Toplam-çağrı-üstbilgi-adı    | Değeri ' de belirtilen değer olan bir yanıt üstbilgisinin adı `calls` . |  No | Yok  |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen

-   **İlke kapsamları:** ürün, API, işlem

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a> Çağrı hızını anahtarla sınırla

> [!IMPORTANT]
> Bu özellik API Management **Tüketim** katmanında kullanılamaz.

`rate-limit-by-key`İlke, belirli bir süre için çağrı hızını belirtilen bir sayı ile sınırlayarak, her anahtar IÇIN API kullanım artışlarını engeller. Anahtar rastgele bir dize değerine sahip olabilir ve genellikle bir ilke ifadesi kullanılarak sağlanır. Hangi isteklerin sınıra doğru sayılması gerektiğini belirtmek için isteğe bağlı artış koşulu eklenebilir. Bu çağrı hızı aşıldığında, çağıran bir `429 Too Many Requests` yanıt durum kodu alır.

Bu ilkenin daha fazla bilgi ve örnekleri için bkz. [Azure API Management Ile Gelişmiş istek azaltma](./api-management-sample-flexible-throttling.md).

> [!CAUTION]
> Daraltma mimarisinin dağıtılmış doğası nedeniyle, hız sınırlaması hiçbir şekilde tamamen doğru değildir. Yapılandırılan ve gerçek izin verilen istek sayısı arasındaki fark, istek hacmi ve hızı, arka uç gecikmesi ve diğer faktörlere göre değişiklik gösterir.

> [!NOTE]
> Hız sınırları ve Kotalar arasındaki farkı anlamak için [bkz. oran limitleri ve kotaları.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>İlke ekstresi

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" 
                   retry-after-header-name="header name" retry-after-variable-name="policy expression variable name"
                   remaining-calls-header-name="header name"  remaining-calls-variable-name="policy expression variable name"
                   total-calls-header-name="header name"/> 

```

### <a name="example"></a>Örnek

Aşağıdaki örnekte, 60 saniye başına 10 çağrının hız limiti arayan IP adresi tarafından anahtarlanır. Her ilke yürütmeden sonra, zaman döneminde izin verilen kalan çağrılar değişkende depolanır `remainingCallsPerIP` .

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"
              remaining-calls-variable-name="remainingCallsPerIP"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Öğeler

| Ad              | Açıklama   | Gerekli |
| ----------------- | ------------- | -------- |
| oran-anahtarla sınırla | Kök öğe. | Yes      |

### <a name="attributes"></a>Öznitelikler

| Ad                | Açıklama                                                                                           | Gerekli | Varsayılan |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| aramalarda               | İçinde belirtilen zaman aralığında izin verilen en fazla toplam çağrı sayısı `renewal-period` . | Yes      | Yok     |
| sayaç anahtarı         | Hız limiti ilkesi için kullanılacak anahtar.                                                             | Yes      | Yok     |
| artış koşulu | İsteğin oran () içinde sayılmasını belirten Boole ifadesi `true` .        | No       | Yok     |
| yenileme süresi      | Kayan pencerenin, izin verilen istek sayısının ' de belirtilen değeri aşması veya aşmaması için saniye cinsinden uzunluğu `calls` .                                           | Yes      | Yok     |
| retry-After-Header-Name    | Belirtilen çağrı hızı aşıldıktan sonra, değeri önerilen yeniden deneme aralığı olan bir yanıt üstbilgisinin adı. |  No | Yok  |
| retry-After-variable-name    | Belirtilen çağrı hızı aşıldıktan sonra önerilen yeniden deneme aralığını saniye cinsinden depolayan bir ilke ifadesi değişkeninin adı. |  No | Yok  |
| kalan-aramalar-üst bilgi-adı    | Her ilke yürütmeden sonra değeri belirtilen yanıt üstbilgisinin adı, içinde belirtilen zaman aralığı için izin verilen kalan çağrı sayısıdır `renewal-period` . |  No | Yok  |
| kalan-çağrı-değişken adı    | Her ilke yürütmesi sonrasında bir ilke ifadesi değişkeninin adı, içinde belirtilen zaman aralığı için izin verilen kalan çağrı sayısını saklar `renewal-period` . |  No | Yok  |
| Toplam-çağrı-üstbilgi-adı    | Değeri ' de belirtilen değer olan bir yanıt üstbilgisinin adı `calls` . |  No | Yok  |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen

-   **İlke kapsamları:** tüm kapsamlar

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a> Çağıran IP 'Leri kısıtla

`ip-filter`İlke, belırlı IP adreslerinden ve/veya adres aralıklarından gelen çağrılara filtre uygular (izin verir/reddeder).

### <a name="policy-statement"></a>İlke ekstresi

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Örnek

Aşağıdaki örnekte, ilke yalnızca tek IP adresinden veya belirtilen IP adresi aralığından gelen isteklere izin verir

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Öğeler

| Ad                                      | Açıklama                                         | Gerekli                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| IP filtresi                                 | Kök öğe.                                       | Yes                                                            |
| adres                                   | Üzerinde filtreleneceği tek bir IP adresi belirtir.   | En az bir `address` veya `address-range` öğe gereklidir. |
| Adres aralığı = "Address" to = "Address" | Üzerinde süzülecek bir IP adresi aralığı belirtir. | En az bir `address` veya `address-range` öğe gereklidir. |

### <a name="attributes"></a>Öznitelikler

| Ad                                      | Açıklama                                                                                 | Gerekli                                           | Varsayılan |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| Adres aralığı = "Address" to = "Address" | Erişimine izin vermek veya erişimi reddetmek için bir IP adresleri aralığı.                                        | `address-range`Öğe kullanıldığında gereklidir. | Yok     |
| IP-filtre eylemi = "izin ver &#124; yasaklaın"    | Belirtilen IP adresleri ve aralıkları için çağrılara izin verilip verilmeyeceğini belirtir. | Yes                                                | Yok     |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen
-   **İlke kapsamları:** tüm kapsamlar

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a> Kullanım kotasını aboneliğe göre ayarla

`quota`İlke, abonelik başına yenilenebilir veya yaşam süresi çağrı hacmi ve/veya bant genişliği kotasını zorlar.

> [!IMPORTANT]
> Bu ilke, her ilke belgesi için yalnızca bir kez kullanılabilir.
>
> [İlke ifadeleri](api-management-policy-expressions.md) bu ilkenin ilke özniteliklerinin hiçbirinde kullanılamaz.

> [!NOTE]
> Hız sınırları ve Kotalar arasındaki farkı anlamak için [bkz. oran limitleri ve kotaları.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>İlke ekstresi

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>Örnek

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Öğeler

| Ad      | Açıklama                                                                                                                                                                                                                                                                                  | Gerekli |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| kota     | Kök öğe.                                                                                                                                                                                                                                                                                | Yes      |
| api       | Ürün içindeki API 'lerde çağrı kotası uygulamak için bu öğelerden bir veya daha fazlasını ekleyin. Ürün ve API çağrı kotaları bağımsız olarak uygulanır. Ya da ile API 'ye başvurulabilir `name` `id` . Her iki öznitelik de sağlanmışsa, `id` kullanılır ve `name` yok sayılır.                    | No       |
| operation | API içindeki işlemlere çağrı kotası koymak için bu öğelerden bir veya daha fazlasını ekleyin. Ürün, API ve işlem çağrısı kotaları bağımsız olarak uygulanır. İşlem ya da ile veya arasında başvuru yapılabilir `name` `id` . Her iki öznitelik de sağlanmışsa, `id` kullanılır ve `name` yok sayılır. | No      |

### <a name="attributes"></a>Öznitelikler

| Ad           | Açıklama                                                                                               | Gerekli                                                         | Varsayılan |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | Kotanın uygulandığı API veya işlemin adı.                                             | Yes                                                              | Yok     |
| bant genişliği      | İçinde belirtilen zaman aralığında izin verilen en büyük toplam kilobayt sayısı `renewal-period` . | Ya `calls` da `bandwidth` her ikisi de belirtilmelidir. | Yok     |
| aramalarda          | İçinde belirtilen zaman aralığında izin verilen en fazla toplam çağrı sayısı `renewal-period` .     | Ya `calls` da `bandwidth` her ikisi de belirtilmelidir. | Yok     |
| yenileme süresi | Kotanın sıfırlandıktan sonraki saniye cinsinden süre. `0`Period ayarlandığında, sonsuz olarak ayarlanır. | Yes                                                              | Yok     |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen
-   **İlke kapsamları:** ürün

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a> Anahtara göre kullanım kotasını ayarla

> [!IMPORTANT]
> Bu özellik API Management **Tüketim** katmanında kullanılamaz.

`quota-by-key`İlke, her anahtar temelinde yenilenebilir veya yaşam süresi çağrı hacmi ve/veya bant genişliği kotası uygular. Anahtar rastgele bir dize değerine sahip olabilir ve genellikle bir ilke ifadesi kullanılarak sağlanır. Kotaya doğru hangi isteklerin sayıldığını belirtmek için isteğe bağlı artış koşulu eklenebilir. Birden çok ilke aynı anahtar değerini artırılabiliyorsa, istek başına yalnızca bir kez artırılır. Çağrı hızı aşıldığında, çağıran bir `403 Forbidden` yanıt durum kodu alır.

Bu ilkenin daha fazla bilgi ve örnekleri için bkz. [Azure API Management Ile Gelişmiş istek azaltma](./api-management-sample-flexible-throttling.md).

> [!NOTE]
> Hız sınırları ve Kotalar arasındaki farkı anlamak için [bkz. oran limitleri ve kotaları.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>İlke ekstresi

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Örnek

Aşağıdaki örnekte, kota çağıran IP adresine göre anahtarlanır.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Öğeler

| Ad  | Açıklama   | Gerekli |
| ----- | ------------- | -------- |
| kota | Kök öğe. | Yes      |

### <a name="attributes"></a>Öznitelikler

| Ad                | Açıklama                                                                                               | Gerekli                                                         | Varsayılan |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| bant genişliği           | İçinde belirtilen zaman aralığında izin verilen en büyük toplam kilobayt sayısı `renewal-period` . | Ya `calls` da `bandwidth` her ikisi de belirtilmelidir. | Yok     |
| aramalarda               | İçinde belirtilen zaman aralığında izin verilen en fazla toplam çağrı sayısı `renewal-period` .     | Ya `calls` da `bandwidth` her ikisi de belirtilmelidir. | Yok     |
| sayaç anahtarı         | Kota ilkesi için kullanılacak anahtar.                                                                      | Yes                                                              | Yok     |
| artış koşulu | İsteğin kotanın () içinde sayılmasını belirten Boole ifadesi `true`             | No                                                               | Yok     |
| yenileme süresi      | Kotanın sıfırlandıktan sonraki saniye cinsinden süre. `0`Period ayarlandığında, sonsuz olarak ayarlanır.                                                   | Yes                                                              | Yok     |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen
-   **İlke kapsamları:** tüm kapsamlar

## <a name="validate-jwt"></a><a name="ValidateJWT"></a> JWT 'yi doğrula

`validate-jwt`İlke, belirtilen BIR http üstbilgisinden veya belirtilen sorgu parametresinden AYıKLANAN JSON Web belirtecinin (JWT) varlığını ve geçerliliğini zorlar.

> [!IMPORTANT]
> `validate-jwt`İlke, `exp` `require-expiration-time` öznitelik belirtilmediği ve olarak ayarlanmadığı müddetçe, kayıtlı talebin JWT belirtecine dahil edilmesini gerektirir `false` .
> `validate-jwt`İlke, HS256 ve RS256 imzalama algoritmalarını destekler. HS256 için anahtarın, Base64 kodlamalı biçimde ilke içinde satır içi olarak sağlanması gerekir. RS256 için anahtar, bir açık KIMLIK yapılandırma uç noktası aracılığıyla ya da ortak anahtarın ortak anahtarını veya mod-üs çiftini içeren karşıya yüklenen bir sertifikanın KIMLIĞINI sağlayarak sağlanabilir.
> `validate-jwt`İlke, simetrik anahtarlarla şifrelenmiş belirteçleri şu şifreleme algoritmaları kullanılarak destekler: A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
</validate-jwt>

```

### <a name="examples"></a>Örnekler

#### <a name="simple-token-validation"></a>Basit belirteç doğrulama

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="token-validation-with-rsa-certificate"></a>RSA sertifikası ile belirteç doğrulama

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key certificate-id="my-rsa-cert" />  <!-- signing key specified as certificate ID, enclosed in double-quotes -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory belirteci doğrulaması

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C belirteci doğrulaması

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Belirteç taleplerine göre işlemlere erişim yetkisi verme

Bu örnek, belirteç talep değerine göre işlemlere erişim yetkisi vermek için [JWT Ilkesini doğrula](api-management-access-restriction-policies.md#ValidateJWT) ' nın nasıl kullanılacağını gösterir.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

### <a name="elements"></a>Öğeler

| Öğe             | Açıklama                                                                                                                                                                                                                                                                                                                                           | Gerekli |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Validate-JWT        | Kök öğe.                                                                                                                                                                                                                                                                                                                                         | Yes      |
| kitle           | Belirteç üzerinde bulunabilecek kabul edilebilir hedef kitle taleplerinin bir listesini içerir. Birden fazla hedef kitle değeri varsa, her bir değer tümü tükenene kadar (Bu durumda doğrulamanın başarısız olması) veya bir başarılı olana kadar denenir. En az bir hedef kitlesi belirtilmelidir.                                                                     | No       |
| veren-İmzalama anahtarları | İmzalı belirteçleri doğrulamak için kullanılan Base64 kodlamalı güvenlik anahtarlarının bir listesi. Birden çok güvenlik anahtarı varsa, her anahtar tümü tükenene kadar (Bu durumda doğrulamanın başarısız olması) ya da bir başarılı olur (belirteç geçişi için yararlıdır). Anahtar öğeleri, `id` talebe göre eşleştirmek için kullanılan isteğe bağlı bir özniteliğe sahiptir `kid` . <br/><br/>Alternatif olarak, kullanarak bir veren imzalama anahtarı sağlayın:<br/><br/> - `certificate-id``<key certificate-id="mycertificate" />`API Management [yüklendi](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add) bir sertifika varlığının tanımlayıcısını belirtmek için biçiminde<br/>-RSA `n` `e` `<key n="<modulus>" e="<exponent>" />` parametrelerini base64url-kodlamalı biçimde belirtmek için-RSA mod ve üs çifti biçimde               | No       |
| şifre çözme-anahtarlar     | Belirteçlerin şifresini çözmek için kullanılan Base64 kodlamalı anahtarların listesi. Birden çok güvenlik anahtarı varsa, her anahtar tüm anahtarlar tükenene kadar (Bu durumda doğrulamanın başarısız olması halinde) veya anahtar başarılı olana kadar denenir. Anahtar öğeleri, `id` talebe göre eşleştirmek için kullanılan isteğe bağlı bir özniteliğe sahiptir `kid` .<br/><br/>Alternatif olarak, kullanarak bir şifre çözme anahtarı sağlayın:<br/><br/> - `certificate-id``<key certificate-id="mycertificate" />`API Management [yüklendi](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add) bir sertifika varlığının tanımlayıcısını belirtmek için biçiminde                                                 | No       |
| verenler             | Belirteci veren kabul edilebilir sorumluların listesi. Birden çok veren değeri varsa, her bir değer tümü tükenene kadar (Bu durumda doğrulamanın başarısız olması) veya bir başarılı olana kadar denenir.                                                                                                                                         | No       |
| OpenID-config       | İmzalama anahtarlarının ve veren 'in elde ettiği uyumlu bir açık KIMLIK yapılandırma uç noktası belirtmek için kullanılan öğe.                                                                                                                                                                                                                        | No       |
| gerekli talepler     | Belirtecin geçerli kabul edilmesi için belirteçte bulunması beklenen taleplerin listesini içerir. Öznitelik, `match` `all` ilkenin başarılı olması için belirteçte her talep değerine ayarlandığında, doğrulamanın başarılı olması gerekir. Öznitelik, `match` `any` doğrulamanın başarılı olması için belirteçte en az bir talep olarak ayarlandığında belirtilmelidir. | No       |

### <a name="attributes"></a>Öznitelikler

| Ad                            | Açıklama                                                                                                                                                                                                                                                                                                                                                                                                                                            | Gerekli                                                                         | Varsayılan                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| Saat eğ                      | TimeSpan. Belirteç Verenin ve API Management örneğinin sistem saatleri arasında beklenen en uzun süreyi belirtmek için kullanın.                                                                                                                                                                                                                                                                                                               | No                                                                               | 0 saniye                                                                         |
| başarısız-doğrulama-hata-ileti | JWT doğrulamadan geçemezse HTTP yanıt gövdesine döndürülecek hata iletisi. Bu ileti, doğru bir şekilde kaçış karakteri içermelidir.                                                                                                                                                                                                                                                                                                 | No                                                                               | Varsayılan hata iletisi, "JWT yok" gibi doğrulama sorununa bağlıdır. |
| başarısız-doğrulama-httpcode      | JWT doğrulamadan geçemezse döndürülen HTTP durum kodu.                                                                                                                                                                                                                                                                                                                                                                                         | No                                                                               | 401                                                                               |
| üst bilgi adı                     | Belirteci tutan HTTP üstbilgisinin adı.                                                                                                                                                                                                                                                                                                                                                                                                         | Biri `header-name` `query-parameter-name` veya `token-value` belirtilmelidir. | Yok                                                                               |
| sorgu parametresi-adı            | Belirteci tutan sorgu parametresinin adı.                                                                                                                                                                                                                                                                                                                                                                                                     | Biri `header-name` `query-parameter-name` veya `token-value` belirtilmelidir. | Yok                                                                               |
| belirteç değeri                     | JWT belirteci içeren bir dize döndüren ifade                                                                                                                                                                                                                                                                                                                                                                                                     | Biri `header-name` `query-parameter-name` veya `token-value` belirtilmelidir. | Yok                                                                               |
| kimlik                              | `id`Öğesindeki özniteliği, `key` `kid` imza doğrulaması için kullanılacak uygun anahtarı bulmak için belirteçteki taleple eşleştirilecek dizeyi (varsa) belirtmenize olanak tanır.                                                                                                                                                                                                                                           | No                                                                               | Yok                                                                               |
| match                           | `match`Öğesindeki özniteliği, `claim` doğrulamanın başarılı olması için, ilkedeki her talep değerinin belirteçte olması gerekip gerekmediğini belirtir. Olası değerler şunlardır:<br /><br /> - `all` -doğrulamanın başarılı olması için, ilkedeki her talep değerinin belirteçte mevcut olması gerekir.<br /><br /> - `any` -doğrulamanın başarılı olması için belirteçte en az bir talep değeri bulunmalıdır.                                                       | No                                                                               | tümü                                                                               |
| gerektir-süre sonu         | Boolean. Belirteçte bir süre sonu talebinin gerekli olup olmadığını belirtir.                                                                                                                                                                                                                                                                                                                                                                               | No                                                                               | true                                                                              |
| gerekli-düzen                  | Belirteç şemasının adı, örn. "taşıyıcı". Bu öznitelik ayarlandığında, ilke belirtilen düzenin yetkilendirme üst bilgi değerinde mevcut olduğundan emin olur.                                                                                                                                                                                                                                                                                    | No                                                                               | Yok                                                                               |
| imzalı belirteçleri gerektir           | Boolean. Bir belirtecin imzalanıp imzalanmayacağını belirtir.                                                                                                                                                                                                                                                                                                                                                                                           | No                                                                               | true                                                                              |
| ayırıcı                       | Dize. Birden çok değerli talepten bir değer kümesini ayıklamak için kullanılacak bir ayırıcı (ör. ",") belirtir.                                                                                                                                                                                                                                                                                                                                          | No                                                                               | Yok                                                                               |
| url                             | Açık KIMLIK yapılandırma meta verilerinin alınabilmesi için KIMLIK yapılandırma uç noktası URL 'SI açık olmalıdır. Yanıt, URL 'de tanımlanan özelliklere göre olmalıdır: `https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata` . Azure Active Directory için aşağıdaki URL 'YI kullanın: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` Dizin kiracı adınızı değiştirme, ör `contoso.onmicrosoft.com` . | Yes                                                                              | Yok                                                                               |
| çıkış-belirteç-değişken-adı      | Dize. Başarılı belirteç doğrulamasından sonra türünden bir nesne olarak belirteç değeri alacak bağlam değişkeninin adı [`Jwt`](api-management-policy-expressions.md)                                                                                                                                                                                                                                                                                     | No                                                                               | Yok                                                                               |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen
-   **İlke kapsamları:** tüm kapsamlar

## <a name="next-steps"></a>Sonraki adımlar

İlkelerle çalışma hakkında daha fazla bilgi için bkz.

-   [API Management ilkeler](api-management-howto-policies.md)
-   [API dönüştürme](transform-api.md)
-   İlke deyimlerinin ve ayarlarının tam listesi için [Ilke başvurusu](./api-management-policies.md)
-   [İlke örnekleri](./policy-reference.md)
