---
title: HTTP istekleri oluşturmak için API Management hizmetini kullanma
description: API 'nizden dış Hizmetleri çağırmak için API Management istek ve yanıt ilkelerini kullanmayı öğrenin
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 2f4bd040d7e5858fd561444f56dbce7b3f940d9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92742393"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Azure API Management Service 'ten dış hizmetler kullanma
Azure API Management hizmetinde kullanılabilen ilkeler, yalnızca gelen istek, giden yanıt ve temel yapılandırma bilgilerini temel alan çok sayıda kullanışlı iş gerçekleştirebilir. Ancak API Management ilkelerden, dış hizmetlerle etkileşime girebilmek birçok fırsat daha açar.

[Azure Event hub hizmeti ile günlüğe kaydetme, izleme ve analiz için](api-management-log-to-eventhub-sample.md)nasıl etkileşim kuracağınızı daha önce gördünüz. Bu makalede, herhangi bir dış HTTP tabanlı hizmetle etkileşime girebilmeniz için ilkeler gösterilmektedir. Bu ilkeler, uzak olayları tetiklemek veya özgün isteği ve yanıtı bir şekilde işlemek için kullanılan bilgileri almak için kullanılabilir.

## <a name="send-one-way-request"></a>Tek yönlü Istek gönder
Büyük olasılıkla en basit dış etkileşim, bir dış hizmetin bazı önemli olay türlerini bilgilendirilmesine olanak tanıyan bir istek olan yangın ve unutma stilidir. Denetim akışı ilkesi, `choose` ilgilendiğiniz her türlü koşulu algılamak için kullanılabilir.  Koşul karşılanmıyorsa, [tek yönlü istek temelli istek](./api-management-advanced-policies.md#SendOneWayRequest) ilkesini kullanarak dış http isteği yapabilirsiniz. Bu, HipChat veya bolluk gibi bir mesajlaşma sistemine veya SendGrid ya da MailChimp gibi bir posta API 'sine ya da Pagerharcı gibi kritik destek olaylarına yönelik bir istek olabilir. Bu mesajlaşma sistemlerinin tümünde çağrılabilecek basit HTTP API 'Leri vardır.

### <a name="alerting-with-slack"></a>Bolluk ile uyarma
Aşağıdaki örnek, HTTP yanıt durum kodu 500 ' den büyük veya buna eşitse bir bolluk sohbet odasına nasıl ileti gönderileceğini gösterir. 500 Aralık hatası, API 'nin istemcisinin kendini çözemediğini arka uç API 'SI ile ilgili bir sorun olduğunu gösterir. Genellikle API Management parça üzerinde bazı tür bir müdahale gerektirir.  

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

Bolluk, gelen Web kancaları kavramını içerir. Bir gelen Web kancası yapılandırırken, bolluk basit bir POST isteği yapmanıza ve bir iletiyi bolluk kanalına iletmenize olanak tanıyan özel bir URL oluşturur. Oluşturduğunuz JSON gövdesi, bolluk tarafından tanımlanan bir biçime dayanır.

![Bolluk Web kancası](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Ateştir ve yeterince iyi unutur.
İstek için bir ateş ve unutma stili kullanılırken belirli bir denge vardır. Bir nedenden dolayı istek başarısız olursa, hata bildirilmeyecektir. Bu durumda, ikincil hata raporlama sistemine sahip olmanın karmaşıklığı ve yanıt için bekleyen ek performans maliyeti garanti edilmez. Yanıtın denetlenmesi gereken senaryolarda, [gönderme isteği](./api-management-advanced-policies.md#SendRequest) ilkesi daha iyi bir seçenektir.

## <a name="send-request"></a>Send-Request
`send-request`İlke, karmaşık işleme işlevleri gerçekleştirmek ve daha fazla ilke işleme için KULLANıLABILECEK API Yönetimi hizmetine veri döndürmek için bir dış hizmet kullanılmasını sağlar.

### <a name="authorizing-reference-tokens"></a>Başvuru belirteçlerini yetkilendirme
API Management büyük bir işlevi arka uç kaynaklarını koruuyor. API 'niz tarafından kullanılan yetkilendirme sunucusu, [Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) olduğu gibi, OAuth2 akışının bir parçası olarak [JWT belirteçleri](https://jwt.io/) oluşturursa, `validate-jwt` belirtecin geçerliliğini doğrulamak için ilkeyi kullanabilirsiniz. Bazı yetkilendirme sunucuları, yetkilendirme sunucusuna geri çağırma yapılmadan doğrulanamayan [başvuru belirteçleri](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) olarak adlandırılır.

### <a name="standardized-introspection"></a>Standartlaştırılmış iç denetim
Geçmişte, bir yetkilendirme sunucusuyla bir başvuru belirtecini doğrulamak için standartlaştırılmış bir yöntem yoktu. Ancak, son öneren standart [RFC 7662](https://tools.ietf.org/html/rfc7662) , bir kaynak sunucunun bir belirtecin geçerliliğini nasıl DOĞRULAYABILECEĞINI tanımlayan IETF tarafından yayımlanmıştır.

### <a name="extracting-the-token"></a>Belirteç ayıklanıyor
İlk adım, yetkilendirme başlığından belirteci ayıklamaya yönelik olur. Üst bilgi değeri, `Bearer` yetkilendirme şeması, tek bir boşluk ve sonra, [RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1)başına yetkilendirme belirteci ile biçimlendirilmelidir. Ne yazık ki yetkilendirme şemasının atlanamadığı durumlar vardır. Ayrıştırılırken bu için hesap için, API Management bir boşluk üzerinde üst bilgi değerini böler ve döndürülen dize dizisinden son dizeyi seçer. Bu, hatalı biçimlendirilen Yetkilendirme üstbilgileri için geçici bir çözüm sağlar.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Doğrulama isteği yapılıyor
API Management, yetkilendirme belirtecine sahip olduktan sonra, API Management belirteci doğrulama isteğinde bulunabilir. RFC 7662 bu işlemi iç denetim çağırır ve `POST` iç denetim kaynağına BIR HTML formu kullanmanızı gerektirir. HTML formu en azından anahtarla bir anahtar/değer çifti içermelidir `token` . Kötü amaçlı istemcilerin geçerli belirteçler için gidemediğinden emin olmak için, yetkilendirme sunucusuna yönelik bu isteğin de kimliği doğrulanmalıdır.

```xml
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
```

### <a name="checking-the-response"></a>Yanıt denetleniyor
`response-variable-name`Öznitelik, döndürülen yanıta erişim vermek için kullanılır. Bu özellikte tanımlanan ad, `context.Variables` nesneye erişmek için sözlükte bir anahtar olarak kullanılabilir `IResponse` .

Yanıt nesnesinden gövdeyi alabilir ve RFC 7622, yanıtın bir JSON nesnesi olması gerektiğini API Management ve en az bir Boole değeri olan adlı bir özellik içermesi gerektiğini belirtir `active` . `active`True olduğunda, belirteç geçerli kabul edilir.

Alternatif olarak, belirtecin geçerli olup olmadığını belirtmek için yetkilendirme sunucusu "etkin" alanını içermiyorsa, geçerli bir belirteçte hangi özelliklerin ayarlandığını belirlemek için Postman gibi bir araç kullanın. Örneğin, geçerli bir belirteç yanıtı "expires_in" adlı bir özellik içeriyorsa, bu özellik adının yetkilendirme sunucusu yanıtında bu şekilde mevcut olup olmadığını denetleyin:

Koşul = "@ (((IResponse) bağlamında <. Değişkenler ["tokenstate"]). Body.As <JObject> (). Özellik ("expires_in") = = null) ">

### <a name="reporting-failure"></a>Raporlama hatası
`<choose>`Belirtecin geçersiz olup olmadığını algılamak için bir ilke kullanabilir ve bu durumda 401 yanıtı döndürün.

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

Belirteçlerin nasıl kullanılması gerektiğini açıklayan, [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) `bearer` ' de, API Management da `WWW-Authenticate` 401 yanıtıyla bir üst bilgi döndürür. WWW-Authenticate, bir istemciye düzgün bir şekilde yetkilendirilmiş bir istek oluşturmayı bildirmek üzere tasarlanmıştır. OAuth2 çerçevesiyle mümkün olan çok çeşitli yaklaşımlar nedeniyle, gereken tüm bilgileri iletmek zordur. Neyse ki [İstemcilerin bir kaynak sunucusuna istekleri düzgün bir şekilde nasıl yetkilendirebileceği](https://tools.ietf.org/html/draft-jones-oauth-discovery-00)konusunda yardımcı olmaya yönelik çalışmalar vardır.

### <a name="final-solution"></a>Son çözüm
Sonunda aşağıdaki ilkeyi alırsınız:

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
              <return-response response-variable-name="existing response variable">
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

Bu, `send-request` ilke, yararlı dış hizmetleri API Management hizmeti aracılığıyla akan istek ve yanıt sürecinde bütünleştirmek için nasıl kullanılabileceğinizi gösteren birçok örnekten biridir.

## <a name="response-composition"></a>Yanıt kompozisyonu
`send-request`İlke, önceki örnekte gördüğünüz gibi bir arka uç sistemine birincil isteği geliştirmek için kullanılabilir veya arka uç çağrısının tamamen yerini alır olarak kullanılabilir. Bu tekniği kullanarak, birden çok farklı sistemden toplanmış bileşik kaynakları kolayca oluşturabilirsiniz.

### <a name="building-a-dashboard"></a>Pano oluşturma
Bazen birden fazla arka uç sisteminde bulunan bilgileri (örneğin, bir panoyu) açığa çıkarmak isteyebilirsiniz. KPI 'ler tüm farklı arka uçlardan gelir, ancak bunlara doğrudan erişim sağlamamayı tercih edersiniz, ancak tüm bilgiler tek bir istekte alınacaksa bu iyi bir durum olabilir. Arka uç bilgilerden bazılarının bazı Dilimleme ve sözlük ve ilk olarak küçük bir temizleme yapması gerekir! Kullanıcıların, düşük performanslı ölçümlerinin değişebilir olup olmadığını görmek için, kullanıcının, F5 tuşunu aşağı bir şekilde değiştirip değiştiremediğini bildiğiniz için, arka uç yükünü azaltmak yararlı olacaktır.    

### <a name="faking-the-resource"></a>Kaynağa göz yorma
Pano kaynağını oluşturmanın ilk adımı Azure portal yeni bir işlem yapılandırmaktır. Bu, dinamik kaynağı oluşturmak üzere bir bileşim ilkesi yapılandırmak için kullanılan bir yer tutucu işlemidir.

![Pano işlemi](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>İstekleri yapma
İşlem oluşturulduktan sonra, bu işlem için özel olarak bir ilke yapılandırabilirsiniz. 

![Ilke kapsamı ekranını gösteren ekran görüntüsü.](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

İlk adım, gelen istekten herhangi bir sorgu parametresi ayıklayarak bunları arka uca iletebilmeniz için kullanılır. Bu örnekte, Pano bir zaman dilimine göre bilgileri gösteriyor ve bu nedenle `fromDate` ve `toDate` parametresi vardır. `set-variable`İlkeyi, Istek URL 'sindeki bilgileri ayıklamak için kullanabilirsiniz.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Bu bilgileri aldıktan sonra, tüm arka uç sistemlerine istek yapabilirsiniz. Her istek, parametre bilgilerine sahip yeni bir URL oluşturur ve ilgili sunucusunu çağırır ve yanıtı bir bağlam değişkenine depolar.

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Bu istekler, ideal olmayan sırayla yürütülür. 

### <a name="responding"></a>Başlamasına
Bileşik yanıtı oluşturmak için, [Return-Response](./api-management-advanced-policies.md#ReturnResponse) ilkesini kullanabilirsiniz. `set-body`Öğesi, `JObject` Özellikler olarak gömülü tüm bileşen temsillerine sahip yeni bir oluşturmak için bir ifade kullanabilir.

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

Tüm ilke aşağıdaki gibi görünür:

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

Yer tutucu işleminin yapılandırmasında, pano kaynağını en az bir saat için önbelleğe alınacak şekilde yapılandırabilirsiniz. 

## <a name="summary"></a>Özet
Azure API Management hizmeti, HTTP trafiğine seçmeli olarak uygulanabilen ve arka uç hizmetleri oluşturmayı sağlayan esnek ilkeler sağlar. API ağ geçidinizi uyarı işlevleri, doğrulama, doğrulama özellikleri ile geliştirmek veya birden fazla arka uç hizmetine göre yeni bileşik kaynaklar oluşturmak isteyip istemediğiniz, `send-request` ve ilgili ilkeler bir olanaklar dünyasını açar.
