---
title: Azure ön kapısının Standart/Premium kural kümesi eylemlerini yapılandırma
description: Bu makale, Azure ön kapısının ayarlandığı çeşitli eylemlerin bir listesini sağlar.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: e4698a1c1576d15042dd050e0123b83dba39a3e3
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064800"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-actions"></a>Azure ön kapı Standart/Premium (Önizleme) kural kümesi eylemleri

> [!Note]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Burada](../front-door-overview.md)görüntüleyin.

Bir Azure ön kapısının Standart/Premium [kural kümesi](concept-rule-set.md) , eşleşen koşulların ve eylemlerin birleşiminden oluşan kurallardan oluşur. Bu makalede, Azure ön kapısı Standart/Premium kural kümesinde kullanabileceğiniz eylemlerin ayrıntılı bir açıklaması sağlanmaktadır. Eylem, eşleşme koşulunun tanımladığı bir istek türüne uygulanan davranışı tanımlar. Bir Azure ön kapısı (Standart/Premium) kural kümesinde, bir kural en fazla beş eylem içerebilir.

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Aşağıdaki eylemler Azure ön kapı kural kümesi 'nde kullanılabilir.  

## <a name="cache-expiration"></a><a name="CacheExpiration"></a> Önbellek süre sonu

Kuralların eşleştiği isteklere yönelik isteklerin yaşam süresi (TTL) değerini üzerine yazmak için **önbellek süre sonu** eylemini kullanın.

> [!NOTE]
> Kaynaklar `Cache-Control` ,, veya değeri olan üstbilgiyi kullanarak belirli yanıtları önbelleğe almak için belirtmeyebilir `no-cache` `private` `no-store` . Bu koşullarda, ön kapı içeriği hiçbir şekilde önbelleğe alınmaz ve bu eylemin hiçbir etkisi olmayacaktır.

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-------|------------------|
| Önbellek davranışı | <ul><li>**Atlama önbelleği:** İçerik önbelleğe alınmamalıdır. ARM şablonlarında, `cacheBehavior` özelliğini olarak ayarlayın `BypassCache` .</li><li>**Geçersiz kıl:** Kaynaktan döndürülen TTL değeri, eylemde belirtilen değerle üzerine yazılır. Bu davranış yalnızca yanıtın önbelleklenebilir olması durumunda uygulanır. ARM şablonlarında, `cacheBehavior` özelliğini olarak ayarlayın `Override` .</li><li>**Eksikse ayarla:** Kaynağından bir TTL değeri döndürülmezse, kural TTL 'yi eylemde belirtilen değere ayarlar. Bu davranış yalnızca yanıtın önbelleklenebilir olması durumunda uygulanır. ARM şablonlarında, `cacheBehavior` özelliğini olarak ayarlayın `SetIfMissing` .</li></ul> |
| Önbellek süresi | _Önbellek davranışı_ veya olarak ayarlandığında `Override` `Set if missing` , bu alanların kullanılacak önbellek süresini belirtmesi gerekir. En uzun süre 366 gündür.<ul><li>Azure portal: gün, saat, dakika ve saniye sayısını belirtin.</li><li>ARM şablonlarında: süreyi biçimde belirtin `d.hh:mm:ss` . |

### <a name="example"></a>Örnek

Bu örnekte, zaten bir önbellek süresi belirtmeyen eşleşen istekler için önbellek süre sonunu 6 saat olarak geçersiz kılıyoruz.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-expiration.png" alt-text="Önbellek süre sonu eylemini gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheExpiration",
  "parameters": {
    "cacheBehavior": "SetIfMissing",
    "cacheType": "All",
    "cacheDuration": "0.06:00:00",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheExpiration'
  parameters: {
    cacheBehavior: 'SetIfMissing'
    cacheType: All
    cacheDuration: '0.06:00:00'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters'
  }
}
```

---

## <a name="cache-key-query-string"></a><a name="CacheKeyQueryString"></a> Önbellek anahtarı sorgu dizesi

Sorgu dizelerine göre önbellek anahtarını değiştirmek için **önbellek anahtarı sorgu dizesi** eylemini kullanın. Önbellek anahtarı, ön kapısının önbelleğe yönelik benzersiz istekleri tanımladığı yoldur.

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-------|------------------|
| Davranış | <ul><li>**Şunları içerir:** Parametrelerde belirtilen sorgu dizeleri, önbellek anahtarı oluşturulduğunda dahil edilir. ARM şablonlarında, `queryStringBehavior` özelliğini olarak ayarlayın `Include` .</li><li>**Her benzersiz URL 'Yi önbelleğe al:** Her benzersiz URL 'nin kendi önbellek anahtarı vardır. ARM şablonlarında, ' nı kullanın `queryStringBehavior` `IncludeAll` .</li><li>**Hariç tut:** Parametrelerde belirtilen sorgu dizeleri, önbellek anahtarı oluşturulduğunda hariç tutulur. ARM şablonlarında, `queryStringBehavior` özelliğini olarak ayarlayın `Exclude` .</li><li>**Sorgu dizelerini yoksay:** Önbellek anahtarı oluşturulduğunda sorgu dizeleri dikkate alınmazlar. ARM şablonlarında, `queryStringBehavior` özelliğini olarak ayarlayın `ExcludeAll` .</li></ul>  |
| Parametreler | Virgülle ayırarak sorgu dizesi parametre adlarının listesi. |

### <a name="example"></a>Örnek

Bu örnekte, önbellek anahtarını adlı bir sorgu dizesi parametresi içerecek şekilde değiştirirsiniz `customerId` .

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-key-query-string.png" alt-text="Önbellek anahtarı sorgu dizesi eylemini gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheKeyQueryString",
  "parameters": {
    "queryStringBehavior": "Include",
    "queryParameters": "customerId",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheKeyQueryString'
  parameters: {
    queryStringBehavior: 'Include'
    queryParameters: 'customerId'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters'
  }
}
```

---

## <a name="modify-request-header"></a><a name="ModifyRequestHeader"></a> İstek üst bilgisini Değiştir

İstekteki üst bilgileri kaynağına gönderildiğinde değiştirmek için **istek başlığını Değiştir** eylemini kullanın.

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-------|------------------|
| Operatör | <ul><li>**Ekle:** Belirtilen üst bilgi, belirtilen değere sahip isteğe eklenmiş. Üst bilgi zaten mevcutsa, bu değer dize birleştirme kullanılarak mevcut üst bilgi değerine eklenir. Sınırlayıcı eklenmez. ARM şablonlarında, ' nı kullanın `headerAction` `Append` .</li><li>**Üzerine yaz:** Belirtilen üst bilgi, belirtilen değere sahip isteğe eklenmiş. Üst bilgi zaten mevcutsa, belirtilen değer varolan değerin üzerine yazar. ARM şablonlarında, ' nı kullanın `headerAction` `Overwrite` .</li><li>**Sil:** Kuralda belirtilen üst bilgi varsa, üst bilgi istekten silinir. ARM şablonlarında, ' nı kullanın `headerAction` `Delete` .</li></ul> |
| Üst bilgi adı | Değiştirilecek üstbilginin adı. |
| Üst bilgi değeri | Eklenecek veya üzerine yazılacak değer. |

### <a name="example"></a>Örnek

Bu örnekte, değeri `AdditionalValue` `MyRequestHeader` istek üstbilgisine ekler. Kaynak, yanıt üst bilgisini bir değerine ayarlarsanız `ValueSetByClient` , bu eylem uygulandıktan sonra, istek üstbilgisinin değeri olur `ValueSetByClientAdditionalValue` .

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-request-header.png" alt-text="İstek üst bilgisini değiştirme eylemini gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyRequestHeader",
  "parameters": {
    "headerAction": "Append",
    "headerName": "MyRequestHeader",
    "value": "AdditionalValue",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyRequestHeader'
  parameters: {
    headerAction: 'Append'
    headerName: 'MyRequestHeader'
    value: 'AdditionalValue'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="modify-response-header"></a><a name="ModifyResponseHeader"></a> Yanıt üst bilgisini Değiştir

Yanıtlarınızda bulunan üst bilgileri, istemcilerinize döndürülmeden önce değiştirmek için **yanıt başlığını Değiştir** eylemini kullanın.

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-------|------------------|
| Operatör | <ul><li>**Ekle:** Belirtilen üst bilgi, belirtilen değere sahip yanıta eklenmiş. Üst bilgi zaten mevcutsa, bu değer dize birleştirme kullanılarak mevcut üst bilgi değerine eklenir. Sınırlayıcı eklenmez. ARM şablonlarında, ' nı kullanın `headerAction` `Append` .</li><li>**Üzerine yaz:** Belirtilen üst bilgi, belirtilen değere sahip yanıta eklenmiş. Üst bilgi zaten mevcutsa, belirtilen değer varolan değerin üzerine yazar. ARM şablonlarında, ' nı kullanın `headerAction` `Overwrite` .</li><li>**Sil:** Kuralda belirtilen üst bilgi varsa, üst bilgi yanıttan silinir.  ARM şablonlarında, ' nı kullanın `headerAction` `Delete` .</li></ul> |
| Üst bilgi adı | Değiştirilecek üstbilginin adı. |
| Üst bilgi değeri | Eklenecek veya üzerine yazılacak değer. |

### <a name="example"></a>Örnek

Bu örnekte, `X-Powered-By` istemciye döndürülmeden önce yanıtlardan adı ile üst bilgi silinir.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-response-header.png" alt-text="Yanıt başlığını değiştir eylemini gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyResponseHeader",
  "parameters": {
    "headerAction": "Delete",
    "headerName": "X-Powered-By",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyResponseHeader'
  parameters: {
    headerAction: 'Delete'
    headerName: 'X-Powered-By'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="url-redirect"></a><a name="UrlRedirect"></a> URL yeniden yönlendirme

İstemcileri yeni bir URL 'ye yönlendirmek için **URL yeniden yönlendirme** eylemini kullanın. İstemciler, ön kapıdan bir yeniden yönlendirme yanıtı gönderilir.

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|----------|------------------|
| Yeniden yönlendirme türü | İstek sahibine döndürülecek yanıt türü. <ul><li>Azure portal: bulunan (302), taşınan (301), geçici yeniden yönlendirme (307), kalıcı yeniden yönlendirme (308).</li><li>ARM şablonlarında: `Found` , `Moved` , `TemporaryRedirect` , `PermanentRedirect`</li></ul> |
| Yeniden yönlendirme protokolü | <ul><li>Azure portal: `Match Request` , `HTTP` , `HTTPS`</li><li>ARM şablonlarında: `MatchRequest` , `Http` , `Https`</li></ul> |
| Hedef konak | İsteğin yeniden yönlendirilmesini istediğiniz ana bilgisayar adı. Gelen ana bilgisayarı korumak için boş bırakın. |
| Hedef yol | Yeniden Yönlendirmede kullanılacak yol. Önde gelen öğesini ekleyin `/` . Gelen yolu korumak için boş bırakın. |
| Sorgu dizesi | Yeniden Yönlendirmede kullanılan sorgu dizesi. Önde gelen ' i eklemeyin `?` . Gelen sorgu dizesini korumak için boş bırakın. |
| Hedef parça | Yeniden Yönlendirmede kullanılacak parça. Gelen parçayı korumak için boş bırakın. |

### <a name="example"></a>Örnek

Bu örnekte, parçayı korurken isteği yeniden yönlendiririz `https://contoso.com/exampleredirection?clientIp={client_ip}` . HTTP geçici yeniden yönlendirme (307) kullanılır. İstemcinin IP adresi, `{client_ip}` `client_ip` [sunucu değişkeni](#server-variables)kullanılarak URL içindeki belirtecin yerine kullanılır.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-redirect.png" alt-text="URL yeniden yönlendirme eylemini gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRedirect",
  "parameters": {
    "redirectType": "TemporaryRedirect",
    "destinationProtocol": "Https",
    "customHostname": "contoso.com",
    "customPath": "/exampleredirection",
    "customQueryString": "clientIp={client_ip}",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRedirect'
  parameters: {
    redirectType: 'TemporaryRedirect'
    destinationProtocol: 'Https'
    customHostname: 'contoso.com'
    customPath: '/exampleredirection'
    customQueryString: 'clientIp={client_ip}'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters'
  }
}
```

---

## <a name="url-rewrite"></a><a name="UrlRewrite"></a> URL yeniden yazma

**URL yeniden yazma** eylemini kullanarak, kaynağına en çok yönlendiren bir isteğin yolunu yazın.

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|----------|------------------|
| Kaynak stili | Değiştirilecek URL yolundaki kaynak modelini tanımlayın. Şu anda, kaynak stili önek tabanlı eşleşme kullanıyor. Tüm URL yollarını eşleştirmek için, `/` kaynak model değeri olarak bir eğik çizgi () kullanın. |
| Hedef | Yeniden yazma sırasında kullanılacak hedef yolu tanımlayın. Hedef yol, kaynak deseninin üzerine yazar. |
| Eşleşmeyen yolu koru | _Evet_ olarak ayarlanırsa, kaynak örüntüden sonraki kalan yol yeni hedef yoluna eklenir. |

### <a name="example"></a>Örnek

Bu örnekte, tüm istekleri yola yeniden yazdık `/redirection` ve yolun geri kalanını korumuyoruz.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-rewrite.png" alt-text="URL yeniden yazma eylemini gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRewrite",
  "parameters": {
    "sourcePattern": "/",
    "destination": "/redirection",
    "preserveUnmatchedPath": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRewrite'
  parameters: {
    sourcePattern: '/'
    destination: '/redirection'
    preserveUnmatchedPath: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters'
  }
}
```

---

## <a name="server-variables"></a>Sunucu değişkenleri

Kural kümesi sunucu değişkenleri, istek hakkındaki yapılandırılmış bilgilere erişim sağlar. İstek/yanıt üstbilgilerini veya URL yeniden yazma yollarını/Sorgu dizelerini dinamik olarak değiştirmek için sunucu değişkenlerini kullanabilirsiniz; Örneğin, yeni bir sayfa yüklenirken veya bir form gönderildiğinde.

### <a name="supported-variables"></a>Desteklenen değişkenler

| Değişken adı    | Description                                                                                                                                                                                                                                                                               |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `socket_ip`      | Azure ön kapısının doğrudan bağlantısının IP adresi. İstemci, isteği göndermek için bir HTTP proxy veya bir yük dengeleyici kullandıysanız değeri, `socket_ip` proxy 'nin veya yük DENGELEYICININ IP adresidir.                                                                      |
| `client_ip`      | Özgün isteği yapan istemcinin IP adresi. `X-Forwarded-For`İstekte bir üst bilgi varsa, bu durumda ISTEMCI IP adresi başlığından çekilir.                                                                                                               |
| `client_port`    | İsteği yapan istemcinin IP bağlantı noktası.                                                                                                                                                                                                                                          |
| `hostname`       | İstemciden gelen istekteki ana bilgisayar adı.                                                                                                                                                                                                                                             |
| `geo_country`    | İstek sahibinin ülke/bölge kodu üzerinden kaynak ülkesini/bölgesini gösterir.                                                                                                                                                                                                       |
| `http_method`    | Veya gibi URL isteğini yapmak için kullanılan yöntem `GET` `POST` .                                                                                                                                                                                                                         |
| `http_version`   | İstek Protokolü. Genellikle `HTTP/1.0` , `HTTP/1.1` veya `HTTP/2.0` .                                                                                                                                                                                                                      |
| `query_string`   | İstenen URL 'de "?" öğesini izleyen değişken/değer çiftleri listesi.<br />Örneğin, istekte, değer olacaktır `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` `query_string` `id=123&title=fabrikam` .                                                      |
| `request_scheme` | İstek düzeni: `http` veya `https` .                                                                                                                                                                                                                                                    |
| `request_uri`    | Tüm özgün istek URI 'SI (bağımsız değişkenlerle).<br />Örneğin, istekte, değer olacaktır `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` `request_uri` `/article.aspx?id=123&title=fabrikam` .                                                                     |
| `ssl_protocol`   | Kurulan bir TLS bağlantısının protokolü.                                                                                                                                                                                                                                            |
| `server_port`    | Bir isteği kabul eden sunucunun bağlantı noktası.                                                                                                                                                                                                                                           |
| `url_path`       | Web istemcisinin erişmek istediği konaktaki belirli kaynağı tanımlar. Bu, istek URI 'sinin bağımsız değişkenler olmadan bölümüdür.<br />Örneğin, istekte, değer olacaktır `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` `uri_path` `/article.aspx` . |

### <a name="server-variable-format"></a>Sunucu değişken biçimi    

Sunucu değişkenleri aşağıdaki biçimler kullanılarak belirtilebilir:

* `{variable}`: Tüm sunucu değişkenini dahil et. Örneğin, istemci IP adresi bu durumda, `111.222.333.444` `{client_ip}` belirteç olarak değerlendirilir `111.222.333.444` .
* `{variable:offset}`: Değişkenin sonuna kadar, belirli bir uzaklığa sonra sunucu değişkenini dahil edin. Fark sıfır tabanlıdır. Örneğin, istemci IP adresi bu durumda, `111.222.333.444` `{client_ip:3}` belirteç olarak değerlendirilir `.222.333.444` .
* `{variable:offset:length}`: Belirtilen uzunluğa kadar, belirli bir uzaklığa sonra sunucu değişkenini dahil edin. Fark sıfır tabanlıdır. Örneğin, istemci IP adresi bu durumda, `111.222.333.444` `{client_ip:4:3}` belirteç olarak değerlendirilir `222` .

### <a name="supported-actions"></a>Desteklenen işlemler

Sunucu değişkenleri aşağıdaki eylemlerde desteklenir:

* Önbellek anahtarı sorgu dizesi
* İstek üst bilgisini Değiştir
* Yanıt üst bilgisini Değiştir
* URL yeniden yönlendirme
* URL yeniden yazma

## <a name="next-steps"></a>Sonraki adımlar

* [Azure ön kapısının Standart/Premium kural kümesi](concept-rule-set.md)hakkında daha fazla bilgi edinin.
* [Kural kümesi eşleştirme koşulları](concept-rule-set-match-conditions.md)hakkında daha fazla bilgi edinin.
