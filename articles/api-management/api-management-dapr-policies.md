---
title: Azure API Management Davpr tümleştirme ilkeleri | Microsoft Docs
description: Azure API Management ilkelerini, Davpr mikro hizmetleri uzantılarıyla etkileşimde bulunmak için öğrenin.
author: vladvino
ms.author: vlvinogr
ms.date: 02/18/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 051bf4398555f318f613c66d58ec65be1d30e215
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646818"
---
# <a name="api-management-dapr-integration-policies"></a>API Management Davpr tümleştirme ilkeleri

Bu konu, Davpr tümleştirmesi API Management ilkelerine yönelik bir başvuru sağlar. Davpr, herhangi bir dilde veya çerçevede durum bilgisiz ve durum bilgisi olmayan mikro hizmetler tabanlı uygulamalar oluşturmak için taşınabilir bir çalışma zamanı. Hizmet bulma ve çağırma gibi ortak mikro hizmet desenlerini, yerleşik yeniden deneme mantığı ile yayımlama ve yayınlama, en az bir kez teslimat semantiğinin yayımlanması, yayınlamayla abone olma ve dış hizmetler kullanılarak kolayca bileşim sağlamak için takılabilir bağlama kaynaklarıyla birlikte. [DAPR.io](https://dapr.io) adresine giderek, davpr ile çalışmaya başlama hakkında ayrıntılı bilgi ve yönergeler için bkz.. İlke ekleme ve yapılandırma hakkında daha fazla bilgi için bkz. [API Management ilkeleri](api-management-howto-policies.md).

> [!CAUTION]
> Bu konuda başvurulan ilkeler genel önizlemede yer almaktadır ve [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)'na tabidir.

> [!IMPORTANT]
> Bu konuda başvurulan ilkeler yalnızca, Davpr desteği etkinken [API Management ağ geçidinin şirket içinde barındırılan sürümünde](self-hosted-gateway-overview.md) çalışır.

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>Şirket içinde barındırılan ağ geçidinde Davpr desteğini etkinleştir

Şirket içinde barındırılan ağ geçidinde Davpr desteğini açmak için, "app-name" değerini istenen bir adla değiştirerek [Kubernetes Dağıtım şablonuna](how-to-deploy-self-hosted-gateway-kubernetes.md) aşağıda bulunan [davpr ek açıklamalarını](https://github.com/dapr/docs/blob/master/README.md) ekleyin. Bu API Management için ayarlama ve kullanma hakkında ayrıntılı izlenecek yol, [burada](https://aka.ms/apim/dapr/walkthru), davpr ile kullanılabilir.
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>Distributed Application Runtime (Dapr) tümleştirme ilkeleri

-  [Bir hizmete Istek gönder](api-management-dapr-policies.md#invoke): bir davpr mikro hizmetini bulmak ve güvenilir bir şekilde iletişim kurmak Için davpr çalışma zamanı 'nı kullanır. Davpr 'de hizmet çağrısı hakkında daha fazla bilgi edinmek için bu [Benioku](https://github.com/dapr/docs/blob/master/README.md#service-invocation) dosyasındaki açıklamaya bakın.
-  Yayın [/alt konuya Ileti gönder](api-management-dapr-policies.md#pubsub): bir yayımla/abone ol konusuna ileti yayımlamak Için davpr çalışma zamanı kullanır. Davpr 'de yayımla/abone ol iletileri hakkında daha fazla bilgi edinmek için bu [Benioku](https://github.com/dapr/docs/blob/master/README.md) dosyasındaki açıklamaya bakın.
-  [Çıkış bağlamasını Tetikle](api-management-dapr-policies.md#bind): çıkış bağlaması aracılığıyla bir dış sistem çağırmak Için davpr çalışma zamanı kullanır. Davpr 'deki bağlamalar hakkında daha fazla bilgi edinmek için bu [Benioku](https://github.com/dapr/docs/blob/master/README.md) dosyasındaki açıklamaya bakın.

## <a name="send-request-to-a-service"></a><a name="invoke"></a> İsteği bir hizmete gönder

Bu ilke, geçerli isteğin hedef URL 'sini `http://localhost:3500/v1.0/invoke/{app-id}[.{ns-name}]/method/{method-name}` ilke bildiriminde belirtilen değerlerle değiştirir.

İlke, Davpr 'nin ağ geçidiyle aynı Pod içindeki bir sepet kapsayıcısında çalıştığını varsayar. İstek alındıktan sonra, Davpr çalışma zamanı, HTTP ve gRPC arasında olası protokol çevirisi, yeniden denemeler, dağıtılmış izleme ve hata işleme dahil olmak üzere hizmet bulma ve gerçek çağırma işlemleri gerçekleştirir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" dapr-namespace="ns-name" />
```

### <a name="examples"></a>Örnekler

#### <a name="example"></a>Örnek

Aşağıdaki örnek, "Echo" adlı mikro hizmette "geri" adlı yöntemin çağrılması gösterilmektedir. `set-backend-service`İlke hedef URL 'yi olarak ayarlar `http://localhost:3500/v1.0/invoke/echo.echo-app/method/back` . `forward-request`İlke, isteği mikro hizmete teslim eden Davpr çalışma zamanına dağıtır.

`forward-request`İlke açıklık açısından burada gösterilir. İlke genellikle anahtar sözcüğü aracılığıyla genel kapsamdan "devralındı" `base` .

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" dapr-namespace="echo-app" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>Öğeler

| Öğe             | Açıklama  | Gerekli |
|---------------------|--------------|----------|
| küme arka uç hizmeti | Kök öğe | Yes      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik        | Açıklama                     | Gerekli | Varsayılan |
|------------------|---------------------------------|----------|---------|
| arka uç kimliği       | "Dadpr" olarak ayarlanmalıdır           | Yes      | Yok     |
| davpr-uygulama kimliği      | Hedef mikro hizmetin adı. Davpr içindeki [AppID](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) parametresini oluşturmak için kullanılır.| Yes | Yok |
| davpr-yöntemi      | Hedef mikro hizmette çağrılacak yöntemin veya URL 'nin adı. , Davpr 'de [Yöntem-adı](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) parametresiyle eşlenir.| Yes | Yok |
| davpr-ad alanı   | Hedef mikro hizmetin bulunduğu ad alanının adı. Davpr içindeki [AppID](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) parametresini oluşturmak için kullanılır.| No | Yok |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

- **İlke bölümleri:** gelen
- **İlke kapsamları:** tüm kapsamlar

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> Yayın/alt konuya ileti gönder

Bu ilke, API Management ağ geçidine bir Davpr yayımlama/abone olma konusuna ileti göndermesini söyler. İlke, `http://localhost:3500/v1.0/publish/{{pubsub-name}}/{{topic}}` şablon parametrelerini değiştirmek ve ilke bildiriminde belirtilen içerik eklemek için BIR http post isteği yaparak bunu gerçekleştirir.

İlke, davpr çalışma zamanının ağ geçidiyle aynı Pod içindeki bir sepet kapsayıcısında çalıştığını varsayar. Davpr çalışma zamanı, yayımlama/alt semantiğini uygular.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<publish-to-dapr pubsub-name="pubsub-name" topic="topic-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>Örnekler

#### <a name="example"></a>Örnek

Aşağıdaki örnekte, geçerli isteğin gövdesinin "Orders" pub/Sub [bileşeninin](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters)"yeni" [konusuna](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters) gönderilmesi gösterilmektedir. Davpr çalışma zamanından alınan yanıt, [bağlam](api-management-policy-expressions.md#ContextVariables) nesnesindeki Variables koleksiyonunun "dadpr-Response" girdisinde saklanır.

Eğer Davpr çalışma zamanı, hedef konuyu bulamıyorsa, örneğin ve bir hata ile yanıt verirse, "hata durumunda" bölümü tetiklenir. Davpr çalışma zamanından alınan yanıt, çağıran öğesine döndürülür. Aksi takdirde, varsayılan `200 OK` yanıt döndürülür.

"Arka uç" bölümü boş ve istek arka uca iletilmiyor.

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
           pubsub-name="orders"
               topic="new"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Öğeler

| Öğe             | Açıklama  | Gerekli |
|---------------------|--------------|----------|
| Yayınla     | Kök öğe | Yes      |

### <a name="attributes"></a>Öznitelikler

| Öznitelik        | Açıklama                     | Gerekli | Varsayılan |
|------------------|---------------------------------|----------|---------|
| PubSub-adı      | Hedef PubSub bileşeninin adı. , Davpr 'de [pubsubname](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) parametresine eşlenir. Yoksa, __Konu__ özniteliği değeri, biçiminde olmalıdır `pubsub-name/topic-name` .    | No       | Yok    |
| konu başlığı            | Konunun adı. , Davpr içindeki [Konu](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) parametresiyle eşlenir.               | Yes      | Yok     |
| yoksayma-hata     | Ayarlanırsa `true` , ilke, ["hata üzerinde](api-management-error-handling-policies.md) " bölümünü, davpr çalışma zamanından hata aldıktan sonra tetiklemez şekilde bildirir | No | `false` |
| Yanıt değişkeni-adı | Davpr çalışma zamanından yanıt depolamak için kullanılacak [değişkenlerin](api-management-policy-expressions.md#ContextVariables) koleksiyon girişinin adı | No | Yok |
| timeout | Davpr çalışma zamanının yanıt vermesi için beklenecek süre (saniye cinsinden). 1 ile 240 saniye arasında değişebilir. | No | 5 |
| şablon | İleti içeriğini dönüştürmek için kullanılacak şablon oluşturma altyapısı. "Sıvı" yalnızca desteklenen değerdir. | No | Yok |
| içerik türü | İleti içeriğinin türü. "Application/JSON" desteklenen tek değerdir. | No | Yok |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

- **İlke bölümleri:** gelen, giden, hata üzerine
- **İlke kapsamları:** tüm kapsamlar

## <a name="trigger-output-binding"></a><a name="bind"></a> Çıkış bağlamasını Tetikle

Bu ilke API Management ağ geçidine giden bir Davpr [bağlamasını](https://github.com/dapr/docs/blob/master/README.md)tetiklemesine bildirir. İlke, `http://localhost:3500/v1.0/bindings/{{bind-name}}` şablon parametresini değiştirmek ve ilke bildiriminde belirtilen içeriği eklemek için BIR http post isteği yaparak bunu gerçekleştirir.

İlke, davpr çalışma zamanının ağ geçidiyle aynı Pod içindeki bir sepet kapsayıcısında çalıştığını varsayar. Davpr çalışma zamanı, bağlama tarafından temsil edilen dış kaynağı çağırmaktan sorumludur.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<invoke-dapr-binding name="bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <metadata>
        <item key="item-name"><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>Örnekler

#### <a name="example"></a>Örnek

Aşağıdaki örnek, "Create" işlem adına sahip "dış sistemler" adlı giden bağlamanın tetiklenmesi, "kaynak" ve "istemci-IP" adlı iki anahtar/değer öğeden oluşan meta veriler ve özgün istekten gelen gövdeyi gösterir. Davpr çalışma zamanından alınan yanıt, [bağlam](api-management-policy-expressions.md#ContextVariables) nesnesindeki değişkenler koleksiyonunun "bind-Response" girdisinde yakalanır.

Eğer bir nedenden dolayı Davpr çalışma zamanı başarısız olursa ve hata vererek yanıt verirse, "hata durumunda" bölümü tetiklenir ve Davpr çalışma zamanından alınan yanıt, çağıran çağırana döndürülür. Aksi takdirde, varsayılan `200 OK` yanıt döndürülür.

"Arka uç" bölümü boş ve istek arka uca iletilmiyor.

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Öğeler

| Öğe             | Açıklama  | Gerekli |
|---------------------|--------------|----------|
| Invoke-dadpr-bağlama | Kök öğe | Yes      |
| meta veriler            | Belirli meta verileri anahtar/değer çiftleri biçiminde bağlama. Davpr içindeki [metadata](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) özelliği ile eşlenir. | No |
| veriler            | İletinin içeriği. Davpr içindeki [Data](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) özelliği ile eşlenir. | No |


### <a name="attributes"></a>Öznitelikler

| Öznitelik        | Açıklama                     | Gerekli | Varsayılan |
|------------------|---------------------------------|----------|---------|
| name            | Hedef bağlama adı. , Davpr içinde [tanımlanan](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#bindings-structure) bağlamaların adıyla eşleşmelidir.           | Yes      | Yok     |
| operation       | Hedef işlem adı (bağlamaya özgü). Davpr içindeki [Operation](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) özelliği ile eşlenir. | No | Yok |
| yoksayma-hata     | Ayarlanırsa `true` , ilke, ["hata üzerinde](api-management-error-handling-policies.md) " bölümünü, davpr çalışma zamanından hata aldıktan sonra tetiklemez şekilde bildirir | No | `false` |
| Yanıt değişkeni-adı | Davpr çalışma zamanından yanıt depolamak için kullanılacak [değişkenlerin](api-management-policy-expressions.md#ContextVariables) koleksiyon girişinin adı | No | Yok |
| timeout | Davpr çalışma zamanının yanıt vermesi için beklenecek süre (saniye cinsinden). 1 ile 240 saniye arasında değişebilir. | No | 5 |
| şablon | İleti içeriğini dönüştürmek için kullanılacak şablon oluşturma altyapısı. "Sıvı" yalnızca desteklenen değerdir. | No | Yok |
| içerik türü | İleti içeriğinin türü. "Application/JSON" desteklenen tek değerdir. | No | Yok |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

- **İlke bölümleri:** gelen, giden, hata üzerine
- **İlke kapsamları:** tüm kapsamlar
