---
title: Azure API Management gelişmiş ilkeleri | Microsoft Docs
description: Azure API Management 'de kullanıma sunulan gelişmiş ilkeler hakkında bilgi edinin. Örneklere bakın ve kullanılabilir ek kaynakları görüntüleyin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/13/2020
ms.author: apimpm
ms.openlocfilehash: 03529fd3c0231617c477f4f16773039a02386683
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103562493"
---
# <a name="api-management-advanced-policies"></a>API Management gelişmiş ilkeleri

Bu konu, aşağıdaki API Management ilkelerine yönelik bir başvuru sağlar. İlke ekleme ve yapılandırma hakkında daha fazla bilgi için bkz. [API Management ilkeleri](./api-management-policies.md).

## <a name="advanced-policies"></a><a name="AdvancedPolicies"></a> Gelişmiş İlkeler

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
-   [Trace](#Trace) - [API denetçisi](./api-management-howto-api-inspector.md) çıktısına, Application Insights Telemetriler ve kaynak günlüklerine özel izlemeler ekler.
-   [Bekleme](#Wait) -devam etmeden önce, kapalı [gönderme isteği](api-management-advanced-policies.md#SendRequest), [önbellekten değer alma](api-management-caching-policies.md#GetFromCacheByKey)veya [Denetim akışı](api-management-advanced-policies.md#choose) ilkelerinin tamamlanmasını bekler.

## <a name="control-flow"></a><a name="choose"></a> Denetim akışı

`choose`İlke, bir bir if-then-else veya bir programlama dilinde Switch yapısına benzer şekilde, Boolean ifadelerin değerlendirilme sonucuna bağlı olarak, kapalı ilke deyimlerini uygular.

### <a name="policy-statement"></a><a name="ChoosePolicyStatement"></a> İlke ekstresi

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

Denetim akışı ilkesi en az bir `<when/>` öğe içermelidir. `<otherwise/>`Öğesi isteğe bağlıdır. Öğelerin içindeki koşullar `<when/>` , ilke içindeki görünümleri sırayla değerlendirilir. `<when/>`Koşul özniteliği eşittir olan ilk öğe içine eklenen ilke deyimleri `true` uygulanacak. Varsa öğesi içindeki ilkeler, `<otherwise/>` tüm `<when/>` öğe koşulu öznitelikleri varsa uygulanır `false` .

### <a name="examples"></a>Örnekler

#### <a name="example"></a><a name="ChooseExample"></a> Örneğinde

Aşağıdaki örnek, bir [set değişkenli](api-management-advanced-policies.md#set-variable) ilke ve iki denetim akışı ilkesini gösterir.

Değişken ayarla ilkesi gelen bölümdür ve `isMobile` [](api-management-policy-expressions.md#ContextVariables) `User-Agent` istek üst bilgisi metin içeriyorsa true olarak ayarlanmış bir Boole bağlam değişkeni oluşturur `iPad` `iPhone` .

İlk denetim akışı ilkesi de gelen bölümünde bulunur ve bağlam değişkeninin değerine bağlı olarak iki [set sorgu dizesi parametre](api-management-transformation-policies.md#SetQueryStringParameter) ilkesinden birini koşullu olarak uygular `isMobile` .

İkinci denetim akışı ilkesi, çıkış bölümünde bulunur ve olarak ayarlandığında [XML 'ı JSON 'ye Dönüştür](api-management-transformation-policies.md#ConvertXMLtoJSON) ilkesini koşullu olarak uygular `isMobile` `true` .

```xml
<policies>
    <inbound>
        <set-variable name="isMobile" value="@(context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPad") || context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPhone"))" />
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

Bu örnek, ürünü kullanırken arka uç hizmetinden alınan yanıttan veri öğelerini kaldırarak içerik filtrelemenin nasıl gerçekleştirileceğini gösterir `Starter` . Bu ilkeyi yapılandırma ve kullanma gösterimi için bkz. [Cloud Cover bölüm 177: Vlad Vinogradsky Ile daha fazla API Management özelliği](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) ve 34:30 ile ileri sarma. Bu demo için kullanılan [koyu gök tahmini API 'sine](https://developer.forecast.io/) ilişkin bir genel bakış görmek için 31:50 adresinden başlayın.

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
| 'yu    | Kök öğe.                                                                                                                                                                                                                                                             | Yes      |
| oluşturulurken      | İlkenin veya bölümlerinin için kullanılacak koşul `if` `ifelse` `choose` . `choose`İlkede birden çok bölüm varsa `when` , bunlar sırayla değerlendirilir. `condition`Bir zaman öğesi olarak değerlendirildikten sonra `true` , başka hiçbir `when` koşul değerlendirilmez. | Yes      |
| güvenmiyorsanız | Koşulların hiçbiri olarak değerlendiriliyorsa kullanılacak ilke kod parçacığını içerir `when` `true` .                                                                                                                                                                               | No       |

### <a name="attributes"></a>Öznitelikler

| Öznitelik                                              | Açıklama                                                                                               | Gerekli |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| Condition = "Boole ifadesi &#124; Boolean sabiti" | İçeren ilke deyimi değerlendirildiğinde değerlendirilen Boole ifadesi veya sabiti `when` . | Yes      |

### <a name="usage"></a><a name="ChooseUsage"></a> Kullanımıyla

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="forward-request"></a><a name="ForwardRequest"></a> İletme isteği

`forward-request`İlke, gelen isteği istek [bağlamında](api-management-policy-expressions.md#ContextVariables)belirtilen arka uç hizmetine iletir. Arka uç hizmeti URL 'SI, API [ayarlarında](./import-and-publish.md) belirtilir ve [arka uç hizmet ilkesi ayarlama](api-management-transformation-policies.md) kullanılarak değiştirilebilir.

> [!NOTE]
> Bu ilkeyi kaldırmak, isteğin arka uç hizmetine iletilmemesi ve giden bölümündeki ilkeler gelen bölümündeki ilkelerin başarıyla tamamlanmasıyla hemen değerlendirilir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" buffer-response="true | false" fail-on-error-status-code="false | true"/>
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

Bu işlem düzeyi ilkesi, `base` üst API düzeyi kapsamından arka uç ilkesini devralması için öğesini kullanır.

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

Bu işlem düzeyi ilkesi, tüm istekleri 120 zaman aşımı ile arka uç hizmetine açıkça iletir ve üst API düzeyi arka uç ilkesini almaz. Arka uç hizmeti 400 ile 599 arasında bir hata durum kodu ile yanıt verirse, [hata durumunda](api-management-error-handling-policies.md) Bu bölüm tetiklenir.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120" fail-on-error-status-code="true" />
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
| ileri-istek | Kök öğe. | Yes      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik                                     | Açıklama                                                                                                                                                                                                                                                                                                    | Gerekli | Varsayılan |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| timeout = "Integer"                             | Bir zaman aşımı hatası oluşturulmadan önce, arka uç hizmeti tarafından HTTP yanıt üst bilgilerinin döndürülmesi için beklenecek saniye cinsinden süre. Minimum değer 0 saniyedir. Temeldeki ağ altyapısı bu süreden sonra boştaki bağlantıları düşürüebileceğinden, 240 saniyeden büyük değerler gerçekleştirilemez. | No       | Yok    |
| iz-yönlendirmeler = "false &#124; true"          | Arka uç hizmetinden gelen yönlendirmelerin, ağ geçidi tarafından izlenen veya çağırana döndürülmeyeceğini belirtir.                                                                                                                                                                                                    | No       | yanlış   |
| buffer-Request-Body = "false &#124; true"       | "True" olarak ayarlandığında istek arabelleğe alınır ve [yeniden denenmek](api-management-advanced-policies.md#Retry)üzere tekrar kullanılır.                                                                                                                                                                                               | No       | yanlış   |
| buffer-Response = "false &#124; true" | Öbekli yanıtların işlenmesini etkiler. "False" olarak ayarlandığında, arka uçtan alınan her bir öbek hemen çağırana döndürülür. "True" olarak ayarlandığında parçalar arabelleğe alınır (akış sonu algılanmamışsa 8 KB) ve yalnızca çağırana geri döndürülürsünüz. | No | true |
| hata-başarısız-Status-Code = "false &#124; true" | Doğru olarak ayarlandığında, 400 ile 599 arasında bir yanıt kodu için [hata üzerinde](api-management-error-handling-policies.md) Tetikleyiciler bölümü.                                                                                                                                                                      | No       | yanlış   |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** arka uç
-   **İlke kapsamları:** tüm kapsamlar

## <a name="limit-concurrency"></a><a name="LimitConcurrency"></a> Eşzamanlılık sınırı

`limit-concurrency`İlke, belirli bir zamanda belirtilen sayıda istekten daha fazlasını gerçekleştirerek, eklenen ilkelerin yürütülmesini önler. Bu sayıyı aştıktan sonra, yeni istekler 429 çok fazla Istek durum koduyla hemen başarısız olur.

### <a name="policy-statement"></a><a name="LimitConcurrencyStatement"></a> İlke ekstresi

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
    </limit-concurrency>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Öğeler

| Öğe           | Açıklama   | Gerekli |
| ----------------- | ------------- | -------- |
| limit-eşzamanlılık | Kök öğe. | Yes      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik | Açıklama                                                                                        | Gerekli | Varsayılan |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| anahtar       | Bir dize. İzin verilen ifade. Eşzamanlılık kapsamını belirtir. Birden çok ilke tarafından paylaşılabilir. | Yes      | Yok     |
| en yüksek sayı | Bir tamsayı. İlkeye girmesine izin verilen en fazla istek sayısını belirtir.           | Yes      | Yok     |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="log-to-event-hub"></a><a name="log-to-eventhub"></a> Olay Hub 'ında günlüğe kaydet

`log-to-eventhub`İlke, belirtilen biçimdeki iletileri bir günlükçü varlığı tarafından tanımlanan bir olay hub 'ına gönderir. Adından da anlaşılacağı gibi, ilke, çevrimiçi veya çevrimdışı analize yönelik seçili istek veya Yanıt bağlamı bilgilerini kaydetmek için kullanılır.

> [!NOTE]
> Bir olay hub 'ı ve günlüğe kaydetme olaylarını yapılandırmaya yönelik adım adım kılavuz için bkz. [Azure Event Hubs ile API Management olaylarını günlüğe](./api-management-howto-log-event-hubs.md)kaydetme.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Örnek

Herhangi bir dize Event Hubs oturum açma değeri olarak kullanılabilir. Bu örnekte, tüm gelen çağrılar için tarih ve saat, dağıtım hizmeti adı, istek KIMLIĞI, IP adresi ve işlem adı, kimliğe kayıtlı olan Event hub günlükçüsü günlüğüne kaydedilir `contoso-logger`

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
| -eventhub arası | Kök öğe. Bu öğenin değeri, Olay Hub 'ınıza kaydedilecek dizedir. | Yes      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik     | Açıklama                                                               | Gerekli                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| günlükçü kimliği     | API Management hizmetinize kayıtlı günlükçü KIMLIĞI.         | Yes                                                                  |
| bölüm kimliği  | İletilerin gönderildiği bölümün dizinini belirtir.             | İsteğe bağlı. Kullanılıyorsa, bu öznitelik kullanılamayabilir `partition-key` . |
| Bölüm-anahtar | İletiler gönderilirken bölüm ataması için kullanılan değeri belirtir. | İsteğe bağlı. Kullanılıyorsa, bu öznitelik kullanılamayabilir `partition-id` .  |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="mock-response"></a><a name="mock-response"></a> Sahte yanıt

, `mock-response` Adın gösterdiği gibi, API 'leri ve işlemleri de anladığı gibi kullanılır. Normal işlem hattı yürütmesini iptal eder ve çağırana bir yanıt döndürür. İlke, her zaman en yüksek uygunlukta yanıtları döndürmeye çalışır. Her kullanılabilir olduğunda yanıt içerik örneklerini tercih eder. Şemalar sağlandığında ve örnekler olmadığında, şemalardan örnek yanıtlar oluşturur. Hiçbir örnek veya şema bulunmazsa, içerik olmayan yanıtlar döndürülmez.

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
| sahte yanıt | Kök öğe. | Yes      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik    | Açıklama                                                                                           | Gerekli | Varsayılan |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| durum kodu  | Yanıt durum kodunu belirtir ve karşılık gelen örneği veya şemayı seçmek için kullanılır.                 | No       | 200     |
| içerik türü | `Content-Type`Yanıt üst bilgisi değerini belirtir ve karşılık gelen örneği veya şemayı seçmek için kullanılır. | No       | Yok    |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, hata üzerine

-   **İlke kapsamları:** tüm kapsamlar

## <a name="retry"></a><a name="Retry"></a> Retry

`retry`İlke, alt ilkelerini bir kez yürütür ve sonra yeniden deneme `condition` `false` veya yeniden deneme bitinceye kadar yürütmeyi yeniden dener `count` .

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

Aşağıdaki örnekte, istek iletimi bir üstel yeniden deneme algoritması kullanılarak on kata kadar yeniden denenir. `first-fast-retry`, False olarak ayarlandığı için tüm yeniden deneme girişimleri üstel yeniden deneme algoritmasına tabidir.

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
| retry   | Kök öğe. , Alt öğeleri olarak başka herhangi bir ilke içerebilir. | Yes      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik        | Açıklama                                                                                                                                           | Gerekli | Varsayılan |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| koşul        | Yeniden denemelerin durdurulup durdurulmadığını belirten bir Boole sabit değeri veya [ifadesi](api-management-policy-expressions.md) `false` () `true` .      | Yes      | Yok     |
| count            | Denemek için en fazla yeniden deneme sayısını belirten pozitif bir sayı.                                                                                | Yes      | Yok     |
| interval         | Yeniden deneme girişimleri arasındaki bekleme aralığını belirten saniye cinsinden pozitif bir sayı.                                                                 | Yes      | Yok     |
| Maksimum Aralık     | Yeniden deneme girişimleri arasındaki en fazla bekleme aralığını belirten saniye cinsinden pozitif bir sayı. Üstel yeniden deneme algoritması uygulamak için kullanılır. | No       | Yok     |
| delta            | Bekleme aralığı artışını belirten saniye cinsinden pozitif bir sayı. Doğrusal ve üstel yeniden deneme algoritmalarının uygulanması için kullanılır.             | No       | Yok     |
| ilk hızlı yeniden deneme | Olarak ayarlanırsa `true` , ilk yeniden deneme denemesi hemen gerçekleştirilir.                                                                                  | No       | `false` |

> [!NOTE]
> Yalnızca, `interval` belirtildiğinde, **sabit** Aralık yeniden denemeleri gerçekleştirilir.
> Yalnızca `interval` ve `delta` belirtildiğinde, yeniden denemeler arasındaki bekleme süresi aşağıdaki formül ile hesaplanıyorsa, **Doğrusal** bir Interval yeniden deneme algoritması kullanılır `interval + (count - 1)*delta` .
> Ve belirtildiğinde, her zaman `interval` `max-interval` `delta` yeniden denemeler  arasındaki bekleme süresinin `interval` değeri, `max-interval` aşağıdaki formüle göre değere katlanarak üstel olarak artmaktadır `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)` .

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes) kullanılabilir. Alt ilke kullanım kısıtlamalarının Bu ilke tarafından devralındığını unutmayın.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="return-response"></a><a name="ReturnResponse"></a> Dönüş yanıtı

İlke, işlem `return-response` hattı yürütmesini iptal eder ve çağırana varsayılan ya da özel bir yanıt döndürür. Varsayılan yanıt `200 OK` gövde yok. Özel yanıt, bir bağlam değişkeni veya ilke deyimleri ile belirtilebilir. Her ikisi de sağlandığında, bağlam değişkeni içinde yer alan yanıt, çağırana döndürülmeden önce ilke deyimleri tarafından değiştirilir.

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
| dönüş-yanıt | Kök öğe.                                                                             | Yes      |
| Set-Header      | Bir [Set-Header](api-management-transformation-policies.md#SetHTTPheader) ilke deyimidir. | No       |
| gövde kümesi        | Bir [Set-Body](api-management-transformation-policies.md#SetBody) ilke deyimidir.         | No       |
| durum ayarla      | Bir [set-Status](api-management-advanced-policies.md#SetStatus) ilke deyimidir.           | No       |

### <a name="attributes"></a>Öznitelikler

| Öznitelik              | Açıklama                                                                                                                                                                          | Gerekli  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| Yanıt değişkeni-adı | Öğesinden başvurulan bağlam değişkeninin adı (örneğin, bir yukarı akış [gönderme isteği](api-management-advanced-policies.md#SendRequest) ilkesi ve bir nesne içeren) `Response` | İsteğe bağlı. |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="send-one-way-request"></a><a name="SendOneWayRequest"></a> Tek yönlü istek gönder

`send-one-way-request`İlke, sağlanmış isteği, yanıt beklemeden BELIRTILEN URL 'ye gönderir.

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

Bu örnek ilke, `send-one-way-request` http yanıt kodu 500 değerinden büyük veya bu değere eşitse bir bolluk sohbet odasına bir ileti göndermek için ilkeyi kullanmanın bir örneğini gösterir. Bu örnek hakkında daha fazla bilgi için bkz. [Azure API Management Service 'ten dış hizmetler kullanma](./api-management-sample-send-request.md).

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
| tek yönlü istek gönder       | Kök öğe.                                                                                               | Yes                             |
| url                        | İsteğin URL 'SI.                                                                                     | If modu = Copy; Aksi halde evet. |
| method                     | İstek için HTTP yöntemi.                                                                            | If modu = Copy; Aksi halde evet. |
| üst bilgi                     | İstek üst bilgisi. Birden çok istek üst bilgisi için birden çok üstbilgi öğesi kullanın.                                  | No                              |
| body                       | İstek gövdesi.                                                                                           | No                              |
| kimlik doğrulama-sertifika | [İstemci kimlik doğrulaması için kullanılacak sertifika](api-management-authentication-policies.md#ClientCertificate) | No                              |

### <a name="attributes"></a>Öznitelikler

| Öznitelik     | Açıklama                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Gerekli | Varsayılan  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| Mode = "String" | Bunun yeni bir istek mi yoksa geçerli isteğin bir kopyası mı olduğunu belirler. Giden modda Mode = Copy, istek gövdesini başlatmaz.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | No       | Yeni      |
| name          | Ayarlanacak üst bilginin adını belirtir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Yes      | Yok      |
| var-eylem | Üstbilgi zaten belirtildiğinde gerçekleştirilecek eylemi belirtir. Bu öznitelik aşağıdaki değerlerden birine sahip olmalıdır.<br /><br /> -override-mevcut üstbilginin değerini değiştirir.<br />-Skip-mevcut üst bilgi değerini değiştirmez.<br />-Append-değeri varolan üst bilgi değerine ekler.<br />-Delete-üstbilgiyi istekten kaldırır.<br /><br /> `override`Aynı ada sahip birden çok girdiyi listelemek üzere ayarlandığında, üstbilgi tüm girişlere göre ayarlanmakta (birden çok kez listelenecektir), ancak sonuçlarda listelenen değerler ayarlanır. | No       | override |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="send-request"></a><a name="SendRequest"></a> İstek Gönder

`send-request`İlke BELIRTILEN URL 'ye belirtilen isteği gönderiyor, ancak ayarlanan zaman aşımı değerinden daha uzun süre beklemekte.

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

Bu örnekte, bir yetkilendirme sunucusuyla bir başvuru belirtecini doğrulamak için bir yol gösterilmektedir. Bu örnek hakkında daha fazla bilgi için bkz. [Azure API Management Service 'ten dış hizmetler kullanma](./api-management-sample-send-request.md).

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
| gönderme isteği               | Kök öğe.                                                                                               | Yes                             |
| url                        | İsteğin URL 'SI.                                                                                     | If modu = Copy; Aksi halde evet. |
| method                     | İstek için HTTP yöntemi.                                                                            | If modu = Copy; Aksi halde evet. |
| üst bilgi                     | İstek üst bilgisi. Birden çok istek üst bilgisi için birden çok üstbilgi öğesi kullanın.                                  | No                              |
| body                       | İstek gövdesi.                                                                                           | No                              |
| kimlik doğrulama-sertifika | [İstemci kimlik doğrulaması için kullanılacak sertifika](api-management-authentication-policies.md#ClientCertificate) | No                              |

### <a name="attributes"></a>Öznitelikler

| Öznitelik                       | Açıklama                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Gerekli | Varsayılan  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| Mode = "String"                   | Bunun yeni bir istek mi yoksa geçerli isteğin bir kopyası mı olduğunu belirler. Giden modda Mode = Copy, istek gövdesini başlatmaz.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | No       | Yeni      |
| Response-variable-name = "String" | Bir yanıt nesnesi alacak bağlam değişkeninin adı. Değişken yoksa, ilke başarıyla yürütüldüğünde oluşturulur ve koleksiyon aracılığıyla erişilebilir hale gelir [`context.Variable`](api-management-policy-expressions.md#ContextVariables) .                                                                                                                                                                                                                                                                                                                          | Yes      | Yok      |
| timeout = "Integer"               | URL çağrısının başarısız olması için geçmesi gereken saniye cinsinden zaman aşımı aralığı.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | No       | 60       |
| yoksayma-hata                    | True ise ve istek bir hatayla sonuçlanır:<br /><br /> -Response-değişken adı belirtilmişse null değer içerecektir.<br />-Response değişkeni adı belirtilmemişse, bağlam. İstek güncelleştirilmeyecek.                                                                                                                                                                                                                                                                                                                                                                                   | No       | yanlış    |
| name                            | Ayarlanacak üst bilginin adını belirtir.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Yes      | Yok      |
| var-eylem                   | Üstbilgi zaten belirtildiğinde gerçekleştirilecek eylemi belirtir. Bu öznitelik aşağıdaki değerlerden birine sahip olmalıdır.<br /><br /> -override-mevcut üstbilginin değerini değiştirir.<br />-Skip-mevcut üst bilgi değerini değiştirmez.<br />-Append-değeri varolan üst bilgi değerine ekler.<br />-Delete-üstbilgiyi istekten kaldırır.<br /><br /> `override`Aynı ada sahip birden çok girdiyi listelemek üzere ayarlandığında, üstbilgi tüm girişlere göre ayarlanmakta (birden çok kez listelenecektir), ancak sonuçlarda listelenen değerler ayarlanır. | No       | override |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="set-http-proxy"></a><a name="SetHttpProxy"></a> HTTP proxy 'yi ayarla

İlke, arka `proxy` uçlara iletilen istekleri BIR http proxy 'si aracılığıyla yönlendirmenize olanak tanır. Ağ geçidi ve proxy arasında yalnızca HTTP (HTTPS değil) desteklenir. Yalnızca temel ve NTLM kimlik doğrulaması.

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
| proxy   | Kök öğe | Yes      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik         | Açıklama                                            | Gerekli | Varsayılan |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| URL = "String"      | Biçiminde proxy URL 'SI http://host:port .             | Yes      | Yok     |
| username = "String" | Proxy ile kimlik doğrulaması için kullanılacak Kullanıcı adı. | No       | Yok     |
| Password = "String" | Proxy ile kimlik doğrulaması için kullanılacak parola. | No       | Yok     |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen

-   **İlke kapsamları:** tüm kapsamlar

## <a name="set-request-method"></a><a name="SetRequestMethod"></a> İstek yöntemini ayarla

`set-method`İlke, istek IÇIN http istek yöntemini değiştirmenize izin verir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Örnek

İlkeyi kullanan Bu örnek ilke, `set-method` http yanıt kodu 500 değerinden büyük veya bu değere eşitse bir bolluk sohbet odasına bir ileti göndermeye yönelik bir örnek gösterir. Bu örnek hakkında daha fazla bilgi için bkz. [Azure API Management Service 'ten dış hizmetler kullanma](./api-management-sample-send-request.md).

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
| Set-yöntemi | Kök öğe. Öğesinin değeri HTTP yöntemini belirtir. | Yes      |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="set-status-code"></a><a name="SetStatus"></a> Durum kodunu ayarla

`set-status`İlke, http durum kodunu belirtilen değere ayarlar.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Örnek

Bu örnekte, yetkilendirme belirtecinin geçersiz olması durumunda 401 yanıtının nasıl döndürülayarlanacağı gösterilmektedir. Daha fazla bilgi için bkz [. Azure API Management Service 'ten dış hizmetler kullanma](./api-management-sample-send-request.md)

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
| durum ayarla | Kök öğe. | Yes      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik       | Açıklama                                                | Gerekli | Varsayılan |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| Code = "integer"  | Döndürülecek HTTP durum kodu.                            | Yes      | Yok     |
| Reason = "String" | Durum kodunu döndürme nedeninin açıklaması. | Yes      | Yok     |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** giden, arka uç, hata durumunda
-   **İlke kapsamları:** tüm kapsamlar

## <a name="set-variable"></a><a name="set-variable"></a> Değişken ayarla

`set-variable`İlke bir [bağlam](api-management-policy-expressions.md#ContextVariables) değişkeni bildirir ve bu değere bir [ifade](api-management-policy-expressions.md) veya dize sabiti aracılığıyla belirtilen bir değer atar. ifade bir değişmez değer içeriyorsa, bir dizeye dönüştürülür ve değerin türü olacaktır `System.String` .

### <a name="policy-statement"></a><a name="set-variablePolicyStatement"></a> İlke ekstresi

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="example"></a><a name="set-variableExample"></a> Örneğinde

Aşağıdaki örnek, gelen bölümündeki bir değişken kümesi ilkesini gösterir. Bu set değişken ilkesi `isMobile` [](api-management-policy-expressions.md#ContextVariables) , `User-Agent` istek üst bilgisi metin içeriyorsa true olarak ayarlanmış bir Boole bağlam değişkeni oluşturur `iPad` `iPhone` .

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPad") || context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPhone"))" />
```

### <a name="elements"></a>Öğeler

| Öğe      | Açıklama   | Gerekli |
| ------------ | ------------- | -------- |
| Set değişkeni | Kök öğe. | Yes      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik | Açıklama                                                              | Gerekli |
| --------- | ------------------------------------------------------------------------ | -------- |
| name      | Değişkenin adı.                                                | Yes      |
| değer     | Değişkenin değeri. Bu bir ifade veya sabit değer olabilir. | Yes      |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda
-   **İlke kapsamları:** tüm kapsamlar

### <a name="allowed-types"></a><a name="set-variableAllowedTypes"></a> İzin verilen türler

İlkede kullanılan ifadelerin `set-variable` aşağıdaki temel türlerden birini döndürmesi gerekir.

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

## <a name="trace"></a><a name="Trace"></a> İzlemesinin

`trace`İlke, API denetçisi çıktısına, Application Insights Telemetriler ve/veya kaynak günlüklerine özel bir izleme ekler.

-   İlke, izleme tetiklendiğinde [API denetçisi](./api-management-howto-api-inspector.md) çıktısına özel bir izleme ekler, yani `Ocp-Apim-Trace` istek üst bilgisi var ve doğru olarak ayarlanır ve `Ocp-Apim-Subscription-Key` istek üst bilgisi bulunur ve izlemeye izin veren geçerli bir anahtar barındırır.
-   İlke, [Application Insights tümleştirme](./api-management-howto-app-insights.md) etkinleştirildiğinde ve [](../azure-monitor/app/data-model-trace-telemetry.md) `severity` ilkede belirtilen, tanılama ayarında belirtilen değere eşit veya ondan daha büyük olduğunda Application Insights bir izleme telemetrisi oluşturur `verbosity` .
-   İlke, [kaynak günlükleri](./api-management-howto-use-azure-monitor.md#activity-logs) etkinleştirildiğinde ve ilkede belirtilen önem düzeyi, tanılama ayarında belirtilen ayrıntı düzeyinden daha yüksek olduğunda günlük girişine bir özellik ekler.

### <a name="policy-statement"></a>İlke ekstresi

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="example"></a><a name="traceExample"></a> Örneğinde

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>Öğeler

| Öğe  | Açıklama                                                                                                                                          | Gerekli |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| izleme    | Kök öğe.                                                                                                                                        | Yes      |
| message  | Günlüğe kaydedilecek bir dize veya ifade.                                                                                                                 | Yes      |
| meta veriler | Application Insights [izleme](../azure-monitor/app/data-model-trace-telemetry.md) telemetrisine özel bir özellik ekler. | No       |

### <a name="attributes"></a>Öznitelikler

| Öznitelik | Açıklama                                                                                                               | Gerekli | Varsayılan |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| kaynak    | Dize sabit değeri, izleme görüntüleyicisine anlamlı ve iletinin kaynağını belirtmektir.                                   | Yes      | Yok     |
| önem derecesi  | İzlemenin önem derecesini belirtir. İzin verilen değerler `verbose` , `information` , `error` (en küçükten en büyüğe). | No       | Ayrıntılı |
| name      | Özelliğin adı.                                                                                                     | Yes      | Yok     |
| değer     | Özelliğin değeri.                                                                                                    | Yes      | Yok     |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes) kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="wait"></a><a name="Wait"></a> Bekleneceğini

`wait`İlke, anlık alt ilkelerini paralel olarak yürütür ve tamamlanmadan önce tüm veya ilk alt ilkelerinden birinin tamamlanmasını bekler. Bekleme ilkesi, anlık alt ilkelerine [Istek gönderme](api-management-advanced-policies.md#SendRequest), [önbellekten değer alma](api-management-caching-policies.md#GetFromCacheByKey)ve [akış ilkelerini denetim](api-management-advanced-policies.md#choose) altına alabilir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Örnek

Aşağıdaki örnekte, `choose` ilkenin en hızlı alt ilkelerine sahip iki ilke vardır `wait` . Bu ilkelerin her biri `choose` paralel olarak yürütülür. Her `choose` ilke, önbelleğe alınmış bir değer almayı dener. Önbellek isabetsizliği varsa, değeri sağlamak için bir arka uç hizmeti çağırılır. Bu örnekte, `wait` özniteliği olarak ayarlandığından, ilke tüm ilk alt ilkeleri tamamlanana kadar tamamlanmaz `for` `all` . Bu örnekte, bağlam değişkenleri ( `execute-branch-one` ,, `value-one` `execute-branch-two` ve `value-two` ) Bu örnek ilkenin kapsamı dışında bildirilmiştir.

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
| bekleneceğini    | Kök öğe. Yalnızca alt öğeler `send-request` , `cache-lookup-value` , ve ilkeler olarak bulunabilir `choose` . | Yes      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik | Açıklama                                                                                                                                                                                                                                                                                                                                                                                                            | Gerekli | Varsayılan |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| :       | `wait`İlkenin tüm hızlı alt ilkelerin tamamlanmasını mi yoksa yalnızca bir tane mi bekleyeceğini belirler. İzin verilen değerler şunlardır:<br /><br /> - `all` -Tüm ilk alt ilkelerin tamamlanmasını bekle<br />-herhangi bir anlık alt ilkenin tamamlanmasını bekleyin. İlk acil alt öğe ilkesi tamamlandıktan sonra, `wait` ilke tamamlanır ve diğer tüm anlık alt ilkelerin yürütülmesi sonlandırılır. | No       | tümü     |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, arka uç
-   **İlke kapsamları:** tüm kapsamlar

## <a name="next-steps"></a>Sonraki adımlar

İlkelerle çalışma hakkında daha fazla bilgi için bkz.

-   [API Management ilkeler](api-management-howto-policies.md)
-   [İlke ifadeleri](api-management-policy-expressions.md)
-   İlke deyimlerinin ve ayarlarının tam listesi için [Ilke başvurusu](./api-management-policies.md)
-   [İlke örnekleri](./policy-reference.md)
