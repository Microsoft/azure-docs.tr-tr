---
title: Azure API Management gelişmiş ilkeleri | Microsoft Docs
description: Azure API Management 'de kullanıma sunulan gelişmiş ilkeler hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 225f26ac2133f45fe7eba9e39d64d0cfe9e20766
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885296"
---
# <a name="api-management-advanced-policies"></a>Gelişmiş ilkeleri API Management

Bu konu, aşağıdaki API Management ilkelerine yönelik bir başvuru sağlar. İlke ekleme ve yapılandırma hakkında daha fazla bilgi için bkz. [API Management ilkeleri](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AdvancedPolicies"></a>Gelişmiş İlkeler

-   [Denetim akışı](api-management-advanced-policies.md#choose) -koşullu [ifadeler](api-management-policy-expressions.md)değerlendirmesinin sonuçlarına göre koşullu olarak ilke deyimlerini uygular.
-   [İlet isteği](#ForwardRequest) -isteği arka uç hizmetine iletir.
-   [Eşzamanlılık sınırı](#LimitConcurrency) -geçen ilkelerin aynı anda belirtilen sayıda istekten daha fazla tarafından yürütülmesini önler.
-   [Olay Hub 'ında günlüğe kaydet](#log-to-eventhub) -belirtilen biçimdeki Iletileri bir günlükçü varlığı tarafından tanımlanan bir olay hub 'ına gönderir.
-   [Sahte yanıt](#mock-response) -işlem hattı yürütmesini iptal eder ve doğrudan çağırana bir yanıt döndürür.
-   [Yeniden dene](#Retry) -koşul karşılanana kadar, ekteki ilke deyimlerinin yürütülmesini yeniden dener. Yürütme belirtilen zaman aralıklarıyla ve belirtilen yeniden deneme sayısına kadar yinelenir.
-   [Dönüş yanıtı](#ReturnResponse) -işlem hattı yürütmesini iptal eder ve belirtilen yanıtı doğrudan çağırana döndürür.
-   [Tek yönlü gönderme isteği](#SendOneWayRequest) -bir yanıtı beklemeden belirtilen URL 'ye bir istek gönderir.
-   [Istek gönder](#SendRequest) -belirtilen URL 'ye bir istek gönderir.
-   [Http proxy 'Yi ayarlama](#SetHttpProxy) -iletilen ISTEKLERI bir http proxy 'si aracılığıyla yönlendirmenize olanak tanır.
-   [Set Request Yöntemi](#SetRequestMethod) -bir Istek için http yöntemini değiştirmenize izin verir.
-   [Durum kodu ayarla](#SetStatus) -http durum kodunu belirtilen değere dönüştürür.
-   [Değişken ayarla](api-management-advanced-policies.md#set-variable) -daha sonra erişim için adlandırılmış [bağlam](api-management-policy-expressions.md#ContextVariables) değişkeninde bir değeri devam ettirir.
-   [Trace](#Trace) - [API denetçisi](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) çıktısına, Application Insights Telemetriler ve tanılama günlüklerine özel izlemeler ekler.
-   [Bekleme](#Wait) -devam etmeden önce, kapalı [gönderme isteği](api-management-advanced-policies.md#SendRequest), [önbellekten değer alma](api-management-caching-policies.md#GetFromCacheByKey)veya [Denetim akışı](api-management-advanced-policies.md#choose) ilkelerinin tamamlanmasını bekler.

## <a name="choose"></a>Denetim akışı

`choose` ilkesi, bir bir if-then-else ya da bir programlama dilinde Switch yapısına benzer şekilde, Boolean ifadelerin değerlendirmesinin sonucuna bağlı olarak, kapalı ilke deyimlerini uygular.

### <a name="ChoosePolicyStatement"></a>İlke ekstresi

```xml
<choose>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <otherwise>
        <!— one or more policy statements to be applied if none of the above conditions are true  -->
</otherwise>
</choose>
```

Denetim akışı ilkesi en az bir `<when/>` öğesi içermelidir. `<otherwise/>` öğesi isteğe bağlıdır. `<when/>` öğelerdeki koşullar, ilke içindeki görünümleri sırayla değerlendirilir. Koşul özniteliği eşittir `true` olan ilk `<when/>` öğesi içine eklenen ilke deyimleri uygulanacak. Varsa `<otherwise/>` öğesi içindeki ilkeler, tüm `<when/>` öğesi koşul özniteliklerinin `false`olması durumunda uygulanır.

### <a name="examples"></a>Örnekler

#### <a name="ChooseExample"></a>Örneğinde

Aşağıdaki örnek, bir [set değişkenli](api-management-advanced-policies.md#set-variable) ilke ve iki denetim akışı ilkesini gösterir.

Değişken ayarla ilkesi gelen bölümdür ve `User-Agent` istek üst bilgisi, `iPad` veya `iPhone`metin içeriyorsa true olarak ayarlanmış bir `isMobile` Boole [bağlam](api-management-policy-expressions.md#ContextVariables) değişkeni oluşturur.

İlk denetim akışı ilkesi de gelen bölümünde bulunur ve `isMobile` bağlam değişkeninin değerine bağlı olarak iki [set Query String parametre](api-management-transformation-policies.md#SetQueryStringParameter) ilkesinden birini koşullu olarak uygular.

İkinci denetim akışı ilkesi giden bölümdür ve `isMobile` `true`olarak ayarlandığında, XML 'i [JSON 'A Dönüştür](api-management-transformation-policies.md#ConvertXMLtoJSON) ilkesini koşullu olarak uygular.

```xml
<policies>
    <inbound>
        <set-variable name="isMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <set-query-parameter name="mobile" exists-action="override">
                    <value>true</value>
                </set-query-parameter>
            </when>
            <otherwise>
                <set-query-parameter name="mobile" exists-action="override">
                    <value>false</value>
                </set-query-parameter>
            </otherwise>
        </choose>
    </inbound>
    <outbound>
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>
            </when>
        </choose>
    </outbound>
</policies>
```

#### <a name="example"></a>Örnek

Bu örnek, `Starter` ürünü kullanılırken arka uç hizmetinden alınan yanıttan veri öğelerini kaldırarak içerik filtrelemenin nasıl gerçekleştirileceğini gösterir. Bu ilkeyi yapılandırma ve kullanma gösterimi için bkz. [Cloud Cover bölüm 177: Vlad Vinogradsky Ile daha fazla API Management özelliği](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) ve 34:30 ile ileri sarma. Bu demo için kullanılan [koyu gök tahmini API 'sine](https://developer.forecast.io/) ilişkin bir genel bakış görmek için 31:50 adresinden başlayın.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="elements"></a>Öğeler

| Öğe   | Açıklama                                                                                                                                                                                                                                                               | Gerekli |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| 'yu    | Kök öğe.                                                                                                                                                                                                                                                             | Evet      |
| Oluşturulurken      | `choose` ilkesinin `if` veya `ifelse` bölümleri için kullanılacak koşul. `choose` ilkesinde birden çok `when` bölümü varsa, bunlar sırayla değerlendirilir. Bir zaman öğesinin `condition` `true`olarak değerlendirildikten sonra, başka bir `when` koşulu değerlendirilmez. | Evet      |
| güvenmiyorsanız | `when` koşullardan hiçbiri `true`olarak değerlendiriliyorsa kullanılacak ilke kod parçacığını içerir.                                                                                                                                                                               | Hayır       |

### <a name="attributes"></a>Öznitelikler

| Öznitelik                                              | Açıklama                                                                                               | Gerekli |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| Condition = "Boole ifadesi &#124; Boolean sabiti" | İçeren `when` ilkesi deyimi değerlendirildiğinde değerlendirilen Boole ifadesi veya sabiti. | Evet      |

### <a name="ChooseUsage"></a>Kullanımıyla

Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="ForwardRequest"></a>İletme isteği

`forward-request` ilkesi gelen isteği istek [bağlamında](api-management-policy-expressions.md#ContextVariables)belirtilen arka uç hizmetine iletir. Arka uç hizmeti URL 'SI, API [ayarlarında](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) belirtilir ve [arka uç hizmet ilkesi ayarlama](api-management-transformation-policies.md) kullanılarak değiştirilebilir.

> [!NOTE]
> Bu ilkeyi kaldırmak, isteğin arka uç hizmetine iletilmemesi ve giden bölümündeki ilkeler gelen bölümündeki ilkelerin başarıyla tamamlanmasıyla hemen değerlendirilir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<forward-request timeout="time in seconds" follow-redirects="true | false" buffer-request-body="true | false" />
```

### <a name="examples"></a>Örnekler

#### <a name="example"></a>Örnek

Aşağıdaki API düzeyi ilkesi, tüm API isteklerini 60 saniyelik bir zaman aşımı aralığı ile arka uç hizmetine iletir.

```xml
<!-- api level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="60"/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Örnek

Bu işlem düzeyi ilkesi, üst API düzeyi kapsamından arka uç ilkesini devralması için `base` öğesini kullanır.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <base/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Örnek

Bu işlem düzeyi ilkesi, tüm istekleri 120 zaman aşımı ile arka uç hizmetine açıkça iletir ve üst API düzeyi arka uç ilkesini almaz.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120"/>
        <!-- effective policy. note the absence of <base/> -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Örnek

Bu işlem düzeyi ilke, istekleri arka uç hizmetine iletmiyor.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <!-- no forwarding to backend -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

### <a name="elements"></a>Öğeler

| Öğe         | Açıklama   | Gerekli |
| --------------- | ------------- | -------- |
| ileri-istek | Kök öğe. | Evet      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik                               | Açıklama                                                                                                      | Gerekli | Varsayılan     |
| --------------------------------------- | ---------------------------------------------------------------------------------------------------------------- | -------- | ----------- |
| timeout = "Integer"                       | Bir zaman aşımı hatası oluşturulmadan önce, arka uç hizmeti tarafından HTTP yanıt üst bilgilerinin döndürülmesi için beklenecek saniye cinsinden süre. Minimum değer 0 saniyedir. Temeldeki ağ altyapısı bu süreden sonra boştaki bağlantıları düşürüebileceğinden, 240 saniyeden büyük değerler gerçekleştirilemez. | Hayır       | None |
| İzle-yeniden yönlendirmeler = " &#124; true false"    | Arka uç hizmetinden gelen yönlendirmelerin, ağ geçidi tarafından izlenen veya çağırana döndürülmeyeceğini belirtir.      | Hayır       | false       |
| buffer-Request-Body = "true &#124; false" | "True" olarak ayarlandığında istek arabelleğe alınır ve [yeniden denenmek](api-management-advanced-policies.md#Retry)üzere tekrar kullanılır. | Hayır       | false       |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.

-   **İlke bölümleri:** arka uç
-   **İlke kapsamları:** tüm kapsamlar

## <a name="LimitConcurrency"></a>Eşzamanlılık sınırı

`limit-concurrency` ilkesi, herhangi bir zamanda belirtilen sayıda istekten daha fazlasını gerçekleştirerek, eklenen ilkelerin yürütülmesini önler. Bu sayıyı aştıktan sonra, yeni istekler 429 çok fazla Istek durum koduyla hemen başarısız olur.

### <a name="LimitConcurrencyStatement"></a>İlke ekstresi

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Örnekler

#### <a name="example"></a>Örnek

Aşağıdaki örnek, bir arka uca iletilen isteklerin sayısının bir bağlam değişkeninin değerine göre nasıl sınırlandırılacağını gösterir.

```xml
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3">
      <forward-request timeout="120"/>
    <limit-concurrency/>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Öğeler

| Öğe           | Açıklama   | Gerekli |
| ----------------- | ------------- | -------- |
| limit-eşzamanlılık | Kök öğe. | Evet      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik | Açıklama                                                                                        | Gerekli | Varsayılan |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| anahtar       | Bir dize. İzin verilen ifade. Eşzamanlılık kapsamını belirtir. Birden çok ilke tarafından paylaşılabilir. | Evet      | Yok     |
| en yüksek sayı | Bir tamsayı. İlkeye girmesine izin verilen en fazla istek sayısını belirtir.           | Evet      | Yok     |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="log-to-eventhub"></a>Olay Hub 'ında günlüğe kaydet

`log-to-eventhub` ilkesi, belirtilen biçimdeki iletileri bir günlükçü varlığı tarafından tanımlanan bir olay hub 'ına gönderir. Adından da anlaşılacağı gibi, ilke, çevrimiçi veya çevrimdışı analize yönelik seçili istek veya Yanıt bağlamı bilgilerini kaydetmek için kullanılır.

> [!NOTE]
> Bir olay hub 'ı ve günlüğe kaydetme olaylarını yapılandırmaya yönelik adım adım kılavuz için bkz. [Azure Event Hubs ile API Management olaylarını günlüğe](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/)kaydetme.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Örnek

Herhangi bir dize Event Hubs oturum açma değeri olarak kullanılabilir. Bu örnekte, tüm gelen çağrılar için tarih ve saat, dağıtım hizmeti adı, istek kimliği, IP adresi ve işlem adı `contoso-logger` kimliği ile kayıtlı olan Event hub günlükçüsü günlüğüne kaydedilir.

```xml
<policies>
  <inbound>
    <log-to-eventhub logger-id ='contoso-logger'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )
    </log-to-eventhub>
  </inbound>
  <outbound>
  </outbound>
</policies>
```

### <a name="elements"></a>Öğeler

| Öğe         | Açıklama                                                                     | Gerekli |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| -eventhub arası | Kök öğe. Bu öğenin değeri, Olay Hub 'ınıza kaydedilecek dizedir. | Evet      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik     | Açıklama                                                               | Gerekli                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| günlükçü kimliği     | API Management hizmetinize kayıtlı günlükçü kimliği.         | Evet                                                                  |
| bölüm kimliği  | İletilerin gönderildiği bölümün dizinini belirtir.             | İsteğe bağlı. `partition-key` kullanılırsa bu öznitelik kullanılamayabilir. |
| Bölüm-anahtar | İletiler gönderilirken bölüm ataması için kullanılan değeri belirtir. | İsteğe bağlı. `partition-id` kullanılırsa bu öznitelik kullanılamayabilir.  |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="mock-response"></a>Sahte yanıt

Adın gösterdiği gibi `mock-response`, API 'Leri ve işlemleri sahte şekilde kullanılır. Normal işlem hattı yürütmesini iptal eder ve çağırana bir yanıt döndürür. İlke, her zaman en yüksek uygunlukta yanıtları döndürmeye çalışır. Her kullanılabilir olduğunda yanıt içerik örneklerini tercih eder. Şemalar sağlandığında ve örnekler olmadığında, şemalardan örnek yanıtlar oluşturur. Hiçbir örnek veya şema bulunmazsa, içerik olmayan yanıtlar döndürülmez.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<mock-response status-code="code" content-type="media type"/>

```

### <a name="examples"></a>Örnekler

```xml
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>
```

### <a name="elements"></a>Öğeler

| Öğe       | Açıklama   | Gerekli |
| ------------- | ------------- | -------- |
| sahte yanıt | Kök öğe. | Evet      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik    | Açıklama                                                                                           | Gerekli | Varsayılan |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| durum kodu  | Yanıt durum kodunu belirtir ve karşılık gelen örneği veya şemayı seçmek için kullanılır.                 | Hayır       | 200     |
| içerik türü | `Content-Type` yanıt üst bilgisi değerini belirtir ve karşılık gelen örnek veya şemayı seçmek için kullanılır. | Hayır       | None    |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, hata üzerine

-   **İlke kapsamları:** tüm kapsamlar

## <a name="Retry"></a>Retry

`retry` ilkesi alt ilkelerini bir kez yürütür ve sonra yeniden deneme `condition` `false` hale gelinceye kadar yürütmeyi yeniden dener `count` tükenmiş olur.

### <a name="policy-statement"></a>İlke ekstresi

```xml

<retry
    condition="boolean expression or literal"
    count="number of retry attempts"
    interval="retry interval in seconds"
    max-interval="maximum retry interval in seconds"
    delta="retry interval delta in seconds"
    first-fast-retry="boolean expression or literal">
        <!-- One or more child policies. No restrictions -->
</retry>

```

### <a name="example"></a>Örnek

Aşağıdaki örnekte, istek iletimi bir üstel yeniden deneme algoritması kullanılarak on kata kadar yeniden denenir. `first-fast-retry` false olarak ayarlandığından, tüm yeniden deneme girişimleri üstel yeniden deneme algoritmasına tabidir.

```xml

<retry
    condition="@(context.Response.StatusCode == 500)"
    count="10"
    interval="10"
    max-interval="100"
    delta="10"
    first-fast-retry="false">
        <forward-request buffer-request-body="true" />
</retry>

```

### <a name="elements"></a>Öğeler

| Öğe | Açıklama                                                         | Gerekli |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | Kök öğe. , Alt öğeleri olarak başka herhangi bir ilke içerebilir. | Evet      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik        | Açıklama                                                                                                                                           | Gerekli | Varsayılan |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| Koşul        | Yeniden denemelerin durdurulup durdurulmadığını belirten bir Boolean sabit değer veya [ifade](api-management-policy-expressions.md) (`false`) veya devamı (`true`).      | Evet      | Yok     |
| count            | Denemek için en fazla yeniden deneme sayısını belirten pozitif bir sayı.                                                                                | Evet      | Yok     |
| interval         | Yeniden deneme girişimleri arasındaki bekleme aralığını belirten saniye cinsinden pozitif bir sayı.                                                                 | Evet      | Yok     |
| Maksimum Aralık     | Yeniden deneme girişimleri arasındaki en fazla bekleme aralığını belirten saniye cinsinden pozitif bir sayı. Üstel yeniden deneme algoritması uygulamak için kullanılır. | Hayır       | Yok     |
| tamamlanması            | Bekleme aralığı artışını belirten saniye cinsinden pozitif bir sayı. Doğrusal ve üstel yeniden deneme algoritmalarının uygulanması için kullanılır.             | Hayır       | Yok     |
| ilk hızlı yeniden deneme | `true` olarak ayarlanırsa, ilk yeniden deneme denemesi hemen gerçekleştirilir.                                                                                  | Hayır       | `false` |

> [!NOTE]
> Yalnızca `interval` belirtildiğinde, **sabit** Aralık yeniden denemeleri gerçekleştirilir.
> Yalnızca `interval` ve `delta` belirtildiğinde, yeniden denemeler arasındaki bekleme süresinin aşağıdaki formül `interval + (count - 1)*delta`göre hesaplandığı bir **Doğrusal** Aralık yeniden deneme algoritması kullanılır.
> `interval`, `max-interval` ve `delta` belirtildiğinde, yeniden denemeler arasındaki bekleme süresinin, aşağıdaki formüle göre `interval` değerinden değer `max-interval` üstel olarak artmasının gerektiği **üstel** aralığı yeniden deneme algoritması uygulanır.`min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) kullanılabilir. Alt ilke kullanım kısıtlamalarının Bu ilke tarafından devralındığını unutmayın.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="ReturnResponse"></a>Dönüş yanıtı

`return-response` ilkesi, işlem hattı yürütmesini iptal eder ve çağırana varsayılan ya da özel bir yanıt döndürür. Varsayılan yanıt, gövde olmadan `200 OK`. Özel yanıt, bir bağlam değişkeni veya ilke deyimleri ile belirtilebilir. Her ikisi de sağlandığında, bağlam değişkeni içinde yer alan yanıt, çağırana döndürülmeden önce ilke deyimleri tarafından değiştirilir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<return-response response-variable-name="existing context variable">
  <set-header/>
  <set-body/>
  <set-status/>
</return-response>

```

### <a name="example"></a>Örnek

```xml
<return-response>
   <set-status code="401" reason="Unauthorized"/>
   <set-header name="WWW-Authenticate" exists-action="override">
      <value>Bearer error="invalid_token"</value>
   </set-header>
</return-response>

```

### <a name="elements"></a>Öğeler

| Öğe         | Açıklama                                                                               | Gerekli |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| dönüş-yanıt | Kök öğe.                                                                             | Evet      |
| Set-Header      | Bir [Set-Header](api-management-transformation-policies.md#SetHTTPheader) ilke deyimidir. | Hayır       |
| gövde kümesi        | Bir [Set-Body](api-management-transformation-policies.md#SetBody) ilke deyimidir.         | Hayır       |
| durum ayarla      | Bir [set-Status](api-management-advanced-policies.md#SetStatus) ilke deyimidir.           | Hayır       |

### <a name="attributes"></a>Öznitelikler

| Öznitelik              | Açıklama                                                                                                                                                                          | Gerekli  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| Yanıt değişkeni-adı | ' Den başvurulan bağlam değişkeninin adı, örneğin bir yukarı akış [gönderme isteği](api-management-advanced-policies.md#SendRequest) ilkesi ve bir `Response` nesnesi içerir | İsteğe bağlı. |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="SendOneWayRequest"></a>Tek yönlü istek gönder

`send-one-way-request` ilkesi, sağlanmış isteği, yanıt beklemeden belirtilen URL 'ye gönderir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<send-one-way-request mode="new | copy">
  <url>...</url>
  <method>...</method>
  <header name="" exists-action="override | skip | append | delete">...</header>
  <body>...</body>
  <authentication-certificate thumbprint="thumbprint" />
</send-one-way-request>

```

### <a name="example"></a>Örnek

Bu örnek ilke, HTTP yanıt kodu 500 değerinden büyük veya bu değere eşitse bir bolluk sohbet odasına ileti göndermek için `send-one-way-request` ilkesi kullanma örneğini gösterir. Bu örnek hakkında daha fazla bilgi için bkz. [Azure API Management Service 'ten dış hizmetler kullanma](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Öğeler

| Öğe                    | Açıklama                                                                                                 | Gerekli                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| tek yönlü istek gönder       | Kök öğe.                                                                                               | Evet                             |
| url                        | İsteğin URL 'SI.                                                                                     | If modu = Copy; Aksi halde evet. |
| method                     | İstek için HTTP yöntemi.                                                                            | If modu = Copy; Aksi halde evet. |
| üst bilgi                     | İstek üst bilgisi. Birden çok istek üst bilgisi için birden çok üstbilgi öğesi kullanın.                                  | Hayır                              |
| bölümü                       | İstek gövdesi.                                                                                           | Hayır                              |
| kimlik doğrulama-sertifika | [İstemci kimlik doğrulaması için kullanılacak sertifika](api-management-authentication-policies.md#ClientCertificate) | Hayır                              |

### <a name="attributes"></a>Öznitelikler

| Öznitelik     | Açıklama                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Gerekli | Varsayılan  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| Mode = "String" | Bunun yeni bir istek mi yoksa geçerli isteğin bir kopyası mı olduğunu belirler. Giden modda Mode = Copy, istek gövdesini başlatmaz.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Hayır       | Yeni      |
| ad          | Ayarlanacak üst bilginin adını belirtir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Evet      | Yok      |
| var-eylem | Üstbilgi zaten belirtildiğinde gerçekleştirilecek eylemi belirtir. Bu öznitelik aşağıdaki değerlerden birine sahip olmalıdır.<br /><br /> -override-mevcut üstbilginin değerini değiştirir.<br />-Skip-mevcut üst bilgi değerini değiştirmez.<br />-Append-değeri varolan üst bilgi değerine ekler.<br />-Delete-üstbilgiyi istekten kaldırır.<br /><br /> Aynı ada sahip birden çok girişi `override` için ayarlandığında, üst bilgi tüm girişlere göre ayarlanmakta (birden çok kez listelenecektir); Sonuç olarak yalnızca listelenen değerler ayarlanır. | Hayır       | Manızı |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="SendRequest"></a>İstek Gönder

`send-request` ilkesi belirtilen URL 'ye belirtilen URL 'yi gönderiyor, ancak ayarlanan zaman aşımı değerinden daha uzun süre beklemekte.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error
="false|true">
  <set-url>...</set-url>
  <set-method>...</set-method>
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>
  <set-body>...</set-body>
  <authentication-certificate thumbprint="thumbprint" />
</send-request>

```

### <a name="example"></a>Örnek

Bu örnekte, bir yetkilendirme sunucusuyla bir başvuru belirtecini doğrulamak için bir yol gösterilmektedir. Bu örnek hakkında daha fazla bilgi için bkz. [Azure API Management Service 'ten dış hizmetler kullanma](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
        <!-- Check active property in response -->
        <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
            <!-- Return 401 Unauthorized with http-problem payload -->
            <return-response>
                <set-status code="401" reason="Unauthorized" />
                <set-header name="WWW-Authenticate" exists-action="override">
                    <value>Bearer error="invalid_token"</value>
                </set-header>
            </return-response>
        </when>
    </choose>
  <base />
</inbound>

```

### <a name="elements"></a>Öğeler

| Öğe                    | Açıklama                                                                                                 | Gerekli                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| gönderme isteği               | Kök öğe.                                                                                               | Evet                             |
| url                        | İsteğin URL 'SI.                                                                                     | If modu = Copy; Aksi halde evet. |
| method                     | İstek için HTTP yöntemi.                                                                            | If modu = Copy; Aksi halde evet. |
| üst bilgi                     | İstek üst bilgisi. Birden çok istek üst bilgisi için birden çok üstbilgi öğesi kullanın.                                  | Hayır                              |
| bölümü                       | İstek gövdesi.                                                                                           | Hayır                              |
| kimlik doğrulama-sertifika | [İstemci kimlik doğrulaması için kullanılacak sertifika](api-management-authentication-policies.md#ClientCertificate) | Hayır                              |

### <a name="attributes"></a>Öznitelikler

| Öznitelik                       | Açıklama                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Gerekli | Varsayılan  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| Mode = "String"                   | Bunun yeni bir istek mi yoksa geçerli isteğin bir kopyası mı olduğunu belirler. Giden modda Mode = Copy, istek gövdesini başlatmaz.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Hayır       | Yeni      |
| Response-variable-name = "String" | Bir yanıt nesnesi alacak bağlam değişkeninin adı. Değişken yoksa, ilke başarıyla yürütüldüğünde oluşturulur ve [`context.Variable`](api-management-policy-expressions.md#ContextVariables) koleksiyonu aracılığıyla erişilebilir hale gelir.                                                                                                                                                                                                                                                                                                                          | Evet      | Yok      |
| timeout = "Integer"               | URL çağrısının başarısız olması için geçmesi gereken saniye cinsinden zaman aşımı aralığı.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Hayır       | 60       |
| yoksayma-hata                    | True ise ve istek bir hatayla sonuçlanır:<br /><br /> -Response-değişken adı belirtilmişse null değer içerecektir.<br />-Response değişkeni adı belirtilmemişse, bağlam. İstek güncelleştirilmeyecek.                                                                                                                                                                                                                                                                                                                                                                                   | Hayır       | false    |
| ad                            | Ayarlanacak üst bilginin adını belirtir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Evet      | Yok      |
| var-eylem                   | Üstbilgi zaten belirtildiğinde gerçekleştirilecek eylemi belirtir. Bu öznitelik aşağıdaki değerlerden birine sahip olmalıdır.<br /><br /> -override-mevcut üstbilginin değerini değiştirir.<br />-Skip-mevcut üst bilgi değerini değiştirmez.<br />-Append-değeri varolan üst bilgi değerine ekler.<br />-Delete-üstbilgiyi istekten kaldırır.<br /><br /> Aynı ada sahip birden çok girişi `override` için ayarlandığında, üst bilgi tüm girişlere göre ayarlanmakta (birden çok kez listelenecektir); Sonuç olarak yalnızca listelenen değerler ayarlanır. | Hayır       | Manızı |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="SetHttpProxy"></a>HTTP proxy 'yi ayarla

`proxy` ilkesi, arka uçlara iletilen istekleri bir HTTP proxy 'si aracılığıyla yönlendirmenize olanak tanır. Ağ geçidi ve proxy arasında yalnızca HTTP (HTTPS değil) desteklenir. Yalnızca temel ve NTLM kimlik doğrulaması.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Örnek

Gizli bilgilerin ilke belgesinde depolanmasını önlemek için, [özelliklerin](api-management-howto-properties.md) Kullanıcı adı ve parola değerlerini kullanın.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Öğeler

| Öğe | Açıklama  | Gerekli |
| ------- | ------------ | -------- |
| Proxy   | Kök öğe | Evet      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik         | Açıklama                                            | Gerekli | Varsayılan |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| URL = "String"      | http://host:portbiçimindeki proxy URL 'SI.             | Evet      | Yok     |
| username = "String" | Proxy ile kimlik doğrulaması için kullanılacak Kullanıcı adı. | Hayır       | Yok     |
| Password = "String" | Proxy ile kimlik doğrulaması için kullanılacak parola. | Hayır       | Yok     |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen

-   **İlke kapsamları:** tüm kapsamlar

## <a name="SetRequestMethod"></a>İstek yöntemini ayarla

`set-method` ilkesi, istek için HTTP istek yöntemini değiştirmenize izin verir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Örnek

`set-method` İlkesi kullanan Bu örnek ilke, HTTP yanıt kodu 500 değerinden büyük veya bu değere eşitse bir bolluk sohbet odasına bir ileti göndermeye yönelik bir örnek gösterir. Bu örnek hakkında daha fazla bilgi için bkz. [Azure API Management Service 'ten dış hizmetler kullanma](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Öğeler

| Öğe    | Açıklama                                                       | Gerekli |
| ---------- | ----------------------------------------------------------------- | -------- |
| Set-yöntemi | Kök öğe. Öğesinin değeri HTTP yöntemini belirtir. | Evet      |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="SetStatus"></a>Durum kodunu ayarla

`set-status` ilkesi, HTTP durum kodunu belirtilen değere ayarlar.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Örnek

Bu örnekte, yetkilendirme belirtecinin geçersiz olması durumunda 401 yanıtının nasıl döndürülayarlanacağı gösterilmektedir. Daha fazla bilgi için bkz [. Azure API Management Service 'ten dış hizmetler kullanma](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>

```

### <a name="elements"></a>Öğeler

| Öğe    | Açıklama   | Gerekli |
| ---------- | ------------- | -------- |
| durum ayarla | Kök öğe. | Evet      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik       | Açıklama                                                | Gerekli | Varsayılan |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| Code = "integer"  | Döndürülecek HTTP durum kodu.                            | Evet      | Yok     |
| Reason = "String" | Durum kodunu döndürme nedeninin açıklaması. | Evet      | Yok     |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.

-   **İlke bölümleri:** giden, arka uç, hata durumunda
-   **İlke kapsamları:** tüm kapsamlar

## <a name="set-variable"></a>Değişken ayarla

`set-variable` ilkesi bir [bağlam](api-management-policy-expressions.md#ContextVariables) değişkeni bildirir ve bu değere bir [ifade](api-management-policy-expressions.md) veya dize sabiti ile belirtilen bir değer atar. ifade bir değişmez değer içeriyorsa, bir dizeye dönüştürülür ve değerin türü `System.String`olacaktır.

### <a name="set-variablePolicyStatement"></a>İlke ekstresi

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="set-variableExample"></a>Örneğinde

Aşağıdaki örnek, gelen bölümündeki bir değişken kümesi ilkesini gösterir. Bu set değişken ilkesi, `User-Agent` istek üst bilgisi, `iPad` veya `iPhone`metin içeriyorsa true olarak ayarlanmış bir `isMobile` Boole [bağlam](api-management-policy-expressions.md#ContextVariables) değişkeni oluşturur.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Öğeler

| Öğe      | Açıklama   | Gerekli |
| ------------ | ------------- | -------- |
| Set değişkeni | Kök öğe. | Evet      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik | Açıklama                                                              | Gerekli |
| --------- | ------------------------------------------------------------------------ | -------- |
| ad      | Değişkenin adı.                                                | Evet      |
| değer     | Değişkenin değeri. Bu bir ifade veya sabit değer olabilir. | Evet      |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda
-   **İlke kapsamları:** tüm kapsamlar

### <a name="set-variableAllowedTypes"></a>İzin verilen türler

`set-variable` ilkesinde kullanılan ifadelerin aşağıdaki temel türlerden birini döndürmesi gerekir.

-   System. Boolean
-   System. SByte
-   System. Byte
-   System. UInt16
-   System. UInt32
-   System. UInt64
-   System. Int16
-   System. Int32
-   System. Int64
-   System. Decimal
-   System. Single
-   System. Double
-   System. Guid
-   System. String
-   System. Char
-   System. DateTime
-   System. TimeSpan
-   System. Byte?
-   System. UInt16?
-   System. UInt32?
-   System. UInt64?
-   System. Int16?
-   System. Int32?
-   System. Int64?
-   System. Decimal?
-   System. Single?
-   System. Double?
-   System. Guid?
-   System. String?
-   System. Char?
-   System. DateTime

## <a name="Trace"></a>İzlemesinin

`trace` ilkesi, API denetçisi çıktısına, Application Insights Telemetriler ve/veya tanılama günlüklerine özel bir izleme ekler. 

* İlke, izleme tetiklendiğinde [API denetçisi](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) çıktısına özel bir izleme ekler, yani `Ocp-Apim-Trace` istek üst bilgisi var ve true olarak ayarlanır ve `Ocp-Apim-Subscription-Key` istek üst bilgisi bulunur ve izlemeye izin veren geçerli bir anahtar barındırır. 
* İlke, [Application Insights tümleştirme](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights) etkinleştirildiğinde ve ilkede belirtilen `severity` düzeyi tanılama ayarında belirtilen `verbosity` düzeyinden daha yüksek olduğunda Application Insights ' de bir [izleme](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) telemetrisi oluşturur. 
* İlke, [tanılama günlükleri](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs) etkinken ve ilkede belirtilen önem düzeyi tanılama ayarında belirtilen ayrıntı düzeyinden daha yüksek olduğunda günlük girişine bir özellik ekler.  


### <a name="policy-statement"></a>İlke ekstresi

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="traceExample"></a>Örneğinde

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>Öğeler

| Öğe | Açıklama   | Gerekli |
| ------- | ------------- | -------- |
| izlemesinin   | Kök öğe. | Evet      |
| message | Günlüğe kaydedilecek bir dize veya ifade. | Evet |
| meta veriler | Application Insights [izleme](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) telemetrisine özel bir özellik ekler. | Hayır |

### <a name="attributes"></a>Öznitelikler

| Öznitelik | Açıklama                                                                             | Gerekli | Varsayılan |
| --------- | --------------------------------------------------------------------------------------- | -------- | ------- |
| source    | Dize sabit değeri, izleme görüntüleyicisine anlamlı ve iletinin kaynağını belirtmektir. | Evet      | Yok     |
| inin    | İzlemenin önem derecesini belirtir. İzin verilen değerler `verbose`, `information``error` (en küçükten en büyüğe). | Hayır      | Seçeneini     |
| ad    | Özelliğin adı. | Evet      | Yok     |
| değer    | Özelliğin değeri. | Evet      | Yok     |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="Wait"></a>Bekleneceğini

`wait` ilkesi, anlık alt ilkelerini paralel olarak yürütür ve tamamlanmadan önce onun veya ilk alt ilkelerinden birinin tamamlanmasını bekler. Bekleme ilkesi, anlık alt ilkelerine [Istek gönderme](api-management-advanced-policies.md#SendRequest), [önbellekten değer alma](api-management-caching-policies.md#GetFromCacheByKey)ve [akış ilkelerini denetim](api-management-advanced-policies.md#choose) altına alabilir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Örnek

Aşağıdaki örnekte, `wait` ilkesinin hemen alt ilkelerine sahip iki `choose` ilkesi vardır. Bu `choose` ilkelerinin her biri paralel olarak yürütülür. Her `choose` ilkesi, önbelleğe alınmış bir değer almayı dener. Önbellek isabetsizliği varsa, değeri sağlamak için bir arka uç hizmeti çağırılır. Bu örnekte, `wait` ilkesi, tüm ilk alt ilkeleri tamamlanana kadar tamamlanmaz, çünkü `for` özniteliği `all`olarak ayarlanmıştır. Bu örnekte, bağlam değişkenleri (`execute-branch-one`, `value-one`, `execute-branch-two`ve `value-two`) Bu örnek ilkenin kapsamı dışında bildirilmiştir.

```xml
<wait for="all">
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-one="])">
      <cache-lookup-value key="key-one" variable-name="value-one" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-one="))">
          <send-request mode="new" response-variable-name="value-one">
            <set-url>https://backend-one</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-two="])">
      <cache-lookup-value key="key-two" variable-name="value-two" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-two="))">
          <send-request mode="new" response-variable-name="value-two">
            <set-url>https://backend-two</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
</wait>

```

### <a name="elements"></a>Öğeler

| Öğe | Açıklama                                                                                                   | Gerekli |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| Bekleneceğini    | Kök öğe. Yalnızca `send-request`, `cache-lookup-value`ve `choose` ilkeleri alt öğe olarak bulunabilir. | Evet      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik | Açıklama                                                                                                                                                                                                                                                                                                                                                                                                            | Gerekli | Varsayılan |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| bekleniyor       | `wait` ilkesinin tüm hızlı alt ilkelerin tamamlanmasını mi yoksa yalnızca bir tane mi bekleyeceğini belirler. İzin verilen değerler şunlardır:<br /><br /> - `all`-tüm ilk alt ilkelerin tamamlanmasını bekle<br />-herhangi bir anlık alt ilkenin tamamlanmasını bekleyin. İlk acil alt ilke tamamlandıktan sonra, `wait` ilkesi tamamlanır ve diğer tüm anlık alt ilkelerin yürütülmesi sonlandırılır. | Hayır       | tümü     |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç
-   **İlke kapsamları:** tüm kapsamlar

## <a name="next-steps"></a>Sonraki adımlar

İlkelerle çalışma hakkında daha fazla bilgi için bkz.

-   [API Management ilkeler](api-management-howto-policies.md)
-   [İlke ifadeleri](api-management-policy-expressions.md)
-   İlke deyimlerinin ve ayarlarının tam listesi için [Ilke başvurusu](api-management-policy-reference.md)
-   [İlke örnekleri](policy-samples.md)
