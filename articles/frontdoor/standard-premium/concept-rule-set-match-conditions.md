---
title: Azure ön kapısının Standart/Premium kural kümesi eşleştirme koşullarını yapılandırma
description: Bu makalede, Azure ön kapısı Standart/Premium kural kümesi ile kullanılabilen çeşitli eşleşme koşullarının bir listesi sunulmaktadır.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: 9e8defa9e929d21f210c48ffbd3b22e44195c17d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061630"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Azure ön kapı Standart/Premium (Önizleme) kural kümesi eşleşme koşulları

> [!Note]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Burada](../front-door-overview.md)görüntüleyin.

Azure ön kapısının Standart/Premium [kural kümesinde](concept-rule-set.md), bir kural sıfır veya daha fazla eşleşme koşulu ve bir eylemden oluşur. Bu makalede, Azure ön kapısı Standart/Premium kural kümesinde kullanabileceğiniz eşleştirme koşullarına ilişkin ayrıntılı açıklamalar sağlanmaktadır.

Bir kuralın ilk bölümü eşleşme koşullarıdır veya eşleştirme koşulları kümesidir. Bir kural, en fazla 10 eşleşme koşullarından oluşabilir. Bir eşleşme koşulu, tanımlı eylemlerin yapıldığı belirli istek türlerini tanımlar. Birden çok eşleşme koşulu kullanırsanız, eşleşme koşulları ve Logic kullanılarak birlikte gruplandırılır. Birden çok değeri destekleyen tüm eşleşme koşulları veya mantık kullanılır.

Eşleştirme koşulunu şu şekilde kullanabilirsiniz:

* İstekleri belirli bir IP adresine, ülkeye veya bölgeye göre filtreleyin.
* İstekleri üst bilgi bilgisine göre filtreleyin.
* Mobil cihazlardan veya masaüstü cihazlardan gelen istekleri filtreleyin.
* İstek dosya adı ve dosya uzantısından gelen istekleri filtreleyin.
* İstek URL 'SI, protokol, yol, sorgu dizesi, post args, vb. için istekleri filtreleyin.

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="device-type"></a><a name="IsDevice"></a> Cihaz türü

Bir mobil cihazdan veya masaüstü cihazdan yapılmış istekleri belirlemek için **cihaz türü** eşleşme koşulunu kullanın.  

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-------|------------------|
| Operatör | <ul><li>Azure portal: `Equal` , `Not Equal`</li><li>ARM şablonlarında: `Equal` ; `negateCondition` _eşit değil_ belirtmek için özelliğini kullanın |
| Değer | `Mobile`, `Desktop` |

### <a name="example"></a>Örnek

Bu örnekte, bir mobil cihazdan geldiği algılanan tüm istekleri eşleştik.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/device-type.png" alt-text="Cihaz türü eşleşme koşulunu gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "IsDevice",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "Mobile"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'IsDevice'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'Mobile'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters'
  }
}
```

---

## <a name="post-args"></a><a name="PostArgs"></a> Post bağımsız değişkenleri

POST isteğinin gövdesinde belirtilen bağımsız değişkenlere göre istekleri tanımlamak için **Post args** Match koşulunu kullanın. Tek bir eşleştirme koşulu, POST isteğinin gövdesinden tek bir bağımsız değişkenle eşleşir. Eşlemek için birden çok değer belirtebilirsiniz, bu, OR mantığı kullanılarak birleştirilir.

> [!NOTE]
> **Post args** ile eşleşen durum, içerik türü ile birlikte kullanılabilir `application/x-www-form-urlencoded` .

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-|-|
| Post bağımsız değişkenleri | POST bağımsız değişkeninin adını temsil eden bir dize değeri. |
| Operatör | [Standart işleç listesinden](#operator-list)herhangi bir operatör. |
| Değer | Eşleştirilecek POST bağımsız değişkeninin değerini temsil eden bir veya daha fazla dize veya tamsayı değeri. Birden çok değer belirtilmişse, veya mantığı kullanılarak değerlendirilir. |
| Örnek dönüştürme | `Lowercase`, `Uppercase` |

### <a name="example"></a>Örnek

Bu örnekte, `customerName` istek gövdesinde bir bağımsız değişken sağlandığı ve değeri harfle başladığı tüm post istekleri eşleşeceğiz `customerName` `J` `K` . ,, Ve ile başlayan değerlerin `J` `j` `K` `k` Tümü eşleştirilmek üzere giriş değerlerini büyük harfe dönüştürmek için bir Case dönüştürmesi kullanıyoruz.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/post-args.png" alt-text="Post args eşleşme koşulunun gösterildiği Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "PostArgs",
  "parameters": {
    "selector": "customerName",
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
        "J",
        "K"
    ],
    "transforms": [
        "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'PostArgs'
  parameters: {
    selector: 'customerName'
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'J'
      'K'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters'
  }
}
```

---

## <a name="query-string"></a><a name="QueryString"></a> Sorgu dizesi

Belirli bir sorgu dizesi içeren istekleri tanımlamak için **sorgu dizesi** eşleştirme koşulunu kullanın. Eşlemek için birden çok değer belirtebilirsiniz, bu, OR mantığı kullanılarak birleştirilir.

> [!NOTE]
> Tüm sorgu dizesi, baştaki değer olmadan tek bir dize olarak eşleştirilir `?` .

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-|-|
| Operatör | [Standart işleç listesinden](#operator-list)herhangi bir operatör. |
| Sorgu dizesi | Eşleştirilecek sorgu dizesinin değerini temsil eden bir veya daha fazla dize veya tamsayı değeri. `?`Sorgu dizesinin başlangıcına dahil etmeyin. Birden çok değer belirtilmişse, veya mantığı kullanılarak değerlendirilir. |
| Örnek dönüştürme | `Lowercase`, `Uppercase` |

### <a name="example"></a>Örnek

Bu örnekte, sorgu dizesinin dizeyi içerdiği tüm istekleri eşleşeceğiz `language=en-US` . Eşleştirme koşulunun büyük/küçük harfe duyarlı olmasını istiyoruz, bu nedenle büyük/küçük harf dönüştürmemelidir.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/query-string.png" alt-text="Sorgu dizesi eşleşme koşulunu gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "QueryString",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "language=en-US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'QueryString'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'language=en-US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters'
  }
}
```

---

## <a name="remote-address"></a><a name="RemoteAddress"></a> Uzak adres

**Uzak adres** eşleştirme koşulu, istek sahibinin konumuna veya IP adresine göre istekleri tanımlar. Eşlemek için birden çok değer belirtebilirsiniz, bu, OR mantığı kullanılarak birleştirilir.

* IP adresi blokları belirtirken CıDR gösterimini kullanın. Bu, bir IP adresi bloğunun sözdiziminin, ardından bir eğik çizgi ve ön ek boyutu gelen taban IP adresi olduğu anlamına gelir. Örnek:
    * **IPv4 örneği**: `5.5.5.64/26` 5.5.5.64 adresinden 5.5.5.127 'e ulaşan tüm istekleri eşleştirir.
    * **IPv6 örneği**: `1:2:3:/48` 1:2:3:0:0:0:0:0 ile 1:2:3 arasındaki adresten gelen tüm istekleri eşleştirir: ffff: ffff: ffff: ffff: ffff.
* Birden çok IP adresi ve IP adresi bloğu belirttiğinizde, ' veya ' mantığı uygulanır.
    * **IPv4 örneği**: iki IP adresi eklerseniz `1.2.3.4` ve `10.20.30.40` Bu koşul, 1.2.3.4 veya 10.20.30.40 adresinden gelen istekler için eşleştirilir.
    * **IPv6 örneği**: iki IP adresi eklerseniz `1:2:3:4:5:6:7:8` ve `10:20:30:40:50:60:70:80` Bu koşul, 1:2:3:4:5:6:7:8 veya 10:20:30:40:50:60:70:80 adresinden gelen istekler için eşleştirilir.

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-|-|
| Operatör | <ul><li>Azure Portal:,, `Geo Match` `Geo Not Match` `IP Match` veya `IP Not Match`</li><li>ARM şablonlarında: `GeoMatch` , `IPMatch` ; coğrafi olarak `negateCondition` _eşleşmeyen_ veya _IP eşleşmeyen_ ' i belirtmek için özelliğini kullanın</li></ul> |
| Değer | <ul><li>`IP Match`Veya `IP Not Match` işleçleri: bir veya daha fazla IP adres aralığı belirtin. Birden çok IP adresi aralığı belirtilmişse, veya mantığı kullanılarak değerlendirilir.</li><li>`Geo Match`Veya `Geo Not Match` işleçleri: ülke kodlarını kullanarak bir veya daha fazla konum belirtin.</li></ul> |

### <a name="example"></a>Örnek

Bu örnekte, isteğin Birleşik Devletler kaynaklandığı tüm istekleri eşleştik.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/remote-address.png" alt-text="Uzak adres eşleşme koşulunu gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RemoteAddress",
  "parameters": {
    "operator": "GeoMatch",
    "negateCondition": true,
    "matchValues": [
      "US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RemoteAddress'
  parameters: {
    operator: 'GeoMatch'
    negateCondition: true
    matchValues: [
      'US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters'
  }
}
```

---

## <a name="request-body"></a><a name="RequestBody"></a> İstek gövdesi

**İstek gövdesi** eşleştirme koşulu, istek gövdesinde görüntülenen belirli bir metne göre istekleri tanımlar. Eşlemek için birden çok değer belirtebilirsiniz, bu, OR mantığı kullanılarak birleştirilir.

> [!NOTE]
> Bir istek gövdesi boyutu 64 KB 'yi aşarsa, **İstek gövdesi** eşleşme koşulu için yalnızca ilk 64 KB kabul edilir.

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-|-|
| Operatör | [Standart işleç listesinden](#operator-list)herhangi bir operatör. |
| Değer | Eşleştirilecek istek gövdesi metninin değerini temsil eden bir veya daha fazla dize veya tamsayı değeri. Birden çok değer belirtilmişse, veya mantığı kullanılarak değerlendirilir. |
| Örnek dönüştürme | `Lowercase`, `Uppercase` |

### <a name="example"></a>Örnek

Bu örnekte, istek gövdesinin dizeyi içerdiği tüm istekleri eşleştik `ERROR` . Eşleşmeyi değerlendirmeden önce istek gövdesini büyük harfe dönüştürüyoruz, bu nedenle `error` ve diğer örnek Çeşitlemeler de bu eşleşme koşulunu tetikler.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-body.png" alt-text="İstek gövdesi eşleşme koşulunu gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestBody",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "ERROR"
    ],
    "transforms": [
      "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestBody'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'ERROR'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters'
  }
}
```

---

## <a name="request-file-name"></a><a name="UrlFileName"></a> İstek dosyası adı

**İstek dosya adı** eşleştirme koşulu, istek URL 'sinde belirtilen dosya adını içeren istekleri tanımlar. Eşlemek için birden çok değer belirtebilirsiniz, bu, OR mantığı kullanılarak birleştirilir.

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-|-|
| Operatör | [Standart işleç listesinden](#operator-list)herhangi bir operatör. |
| Değer | Eşleştirilecek istek dosyası adının değerini temsil eden bir veya daha fazla dize veya tamsayı değeri. Birden çok değer belirtilmişse, veya mantığı kullanılarak değerlendirilir. |
| Örnek dönüştürme | `Lowercase`, `Uppercase` |

### <a name="example"></a>Örnek

Bu örnekte, istek dosyası adının olduğu tüm istekleri eşleşeceğiz `media.mp4` . Eşleşmeyi değerlendirmeden önce dosya adını küçük harfe dönüştürüyoruz, bu nedenle `MEDIA.MP4` ve diğer büyük/küçük harf çeşitlemeleri de bu eşleşme koşulunu tetikler.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-name.png" alt-text="İstek dosyası adı eşleştirme koşulunu gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileName",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "media.mp4"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileName'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'media.mp4'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters'
  }
}
```

---

## <a name="request-file-extension"></a><a name="UrlFileExtension"></a> İstek dosyası uzantısı

**İstek dosyası uzantısı** eşleşme koşulu, belirtilen dosya uzantısını içeren istekleri İstek URL 'sindeki dosya adında tanımlar. Eşlemek için birden çok değer belirtebilirsiniz, bu, OR mantığı kullanılarak birleştirilir.

> [!NOTE]
> Önde gelen bir nokta eklemeyin. Örneğin `.html` yerine `html` kullanın.

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-|-|
| Operatör | [Standart işleç listesinden](#operator-list)herhangi bir operatör. |
| Değer | Eşleştirilecek istek dosyası uzantısının değerini temsil eden bir veya daha fazla dize veya tamsayı değeri. Önde gelen bir nokta eklemeyin. Birden çok değer belirtilmişse, veya mantığı kullanılarak değerlendirilir. |
| Örnek dönüştürme | `Lowercase`, `Uppercase` |

### <a name="example"></a>Örnek

Bu örnekte, istek dosya uzantısının veya olduğu tüm istekleri eşleşeceğiz `pdf` `docx` . Eşleşmeyi değerlendirmeden önce istek dosyası uzantısını küçük harfe dönüştürüyoruz, bu nedenle `PDF` , `DocX` ve diğer örnek Çeşitlemeler de bu eşleşme koşulunu tetikleyecektir.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-extension.png" alt-text="İstek dosyası uzantısı eşleşme koşulunu gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileExtension",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "pdf",
      "docx"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters"
  }
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileExtension'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'pdf'
      'docx'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters'
  }
}
```

---

## <a name="request-header"></a><a name="RequestHeader"></a> İstek üst bilgisi

**İstek üst bilgisi** eşleştirme koşulu, istekte belirli bir üst bilgiyi içeren istekleri tanımlar. Bu eşleştirme koşulunu, bir üst bilginin değerinin ne olduğunu veya üstbilginin belirtilen bir değerle eşleşip eşleşmediğini denetlemek için kullanabilirsiniz. Eşlemek için birden çok değer belirtebilirsiniz, bu, OR mantığı kullanılarak birleştirilir.

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-|-|
| Üst bilgi adı | POST bağımsız değişkeninin adını temsil eden bir dize değeri. |
| Operatör | [Standart işleç listesinden](#operator-list)herhangi bir operatör. |
| Değer | Eşleştirilecek istek üst bilgisinin değerini temsil eden bir veya daha fazla dize veya tamsayı değeri. Birden çok değer belirtilmişse, veya mantığı kullanılarak değerlendirilir. |
| Örnek dönüştürme | `Lowercase`, `Uppercase` |

### <a name="example"></a>Örnek

Bu örnekte, isteğin, `MyCustomHeader` değeri ne olursa olsun adlı bir üst bilgi içerdiği tüm istekleri eşleştik.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-header.png" alt-text="İstek üst bilgisi eşleşme koşulunu gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestHeader",
  "parameters": {
    "selector": "MyCustomHeader",
    "operator": "Any",
    "negateCondition": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestHeader'
  parameters: {
    selector: 'MyCustomHeader',
    operator: 'Any'
    negateCondition: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters'
  }
}
```

---

## <a name="request-method"></a><a name="RequestMethod"></a> İstek yöntemi

**İstek yöntemi** eşleştirme koşulu, belirtilen http istek yöntemini kullanan istekleri tanımlar. Eşlemek için birden çok değer belirtebilirsiniz, bu, OR mantığı kullanılarak birleştirilir.

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-|-|
| Operatör | <ul><li>Azure portal: `Equal` , `Not Equal`</li><li>ARM şablonlarında: `Equal` ; `negateCondition` _eşit değil_ belirtmek için özelliğini kullanın |
| İstek yöntemi | Öğesinden bir veya daha fazla HTTP yöntemi: `GET` , `POST` ,, `PUT` `DELETE` , `HEAD` , `OPTIONS` , `TRACE` . Birden çok değer belirtilmişse, veya mantığı kullanılarak değerlendirilir. |

### <a name="example"></a>Örnek

Bu örnekte, isteğin yöntemini kullandığı tüm istekleri eşleştik `DELETE` .

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-method.png" alt-text="İstek yöntemi eşleştirme koşulunu gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestMethod",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "DELETE"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestMethod'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'DELETE
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters'
  }
}
```

---

## <a name="request-path"></a><a name="UrlPath"></a> İstek yolu

**İstek yolu** eşleştirme koşulu, istek URL 'sinde belirtilen yolu içeren istekleri tanımlar. Eşlemek için birden çok değer belirtebilirsiniz, bu, OR mantığı kullanılarak birleştirilir.

> [!NOTE]
> Yol, ana bilgisayar adının ve eğik çizgiden sonraki URL 'nin parçasıdır. Örneğin, URL 'de `https://www.contoso.com/files/secure/file1.pdf` yol olur `files/secure/file1.pdf` .

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-|-|
| Operatör | [Standart işleç listesinden](#operator-list)herhangi bir operatör. |
| Değer | Eşleştirilecek istek yolunun değerini temsil eden bir veya daha fazla dize veya tamsayı değeri. Baştaki eğik çizgiyi eklemeyin. Birden çok değer belirtilmişse, veya mantığı kullanılarak değerlendirilir. |
| Örnek dönüştürme | `Lowercase`, `Uppercase` |

### <a name="example"></a>Örnek

Bu örnekte, istek dosyası yolunun başladığı tüm istekleri eşleştik `files/secure/` . Eşleşmeyi değerlendirmeden önce istek dosyası uzantısını küçük harfe dönüştürüyoruz, bu nedenle istekler `files/SECURE/` ve diğer büyük/küçük harf çeşitlemeleri de bu eşleşme koşulunu tetikleyecektir.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-path.png" alt-text="İstek yolu eşleştirme koşulunu gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlPath",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "files/secure/"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlPath'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'files/secure/'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters'
  }
}
```

---

## <a name="request-protocol"></a><a name="RequestScheme"></a> İstek Protokolü

**İstek Protokolü** eşleştirme koşulu, belirtilen Protokolü (http veya https) kullanan istekleri tanımlar.

> [!NOTE]
> *Protokol* bazen de *Düzen* olarak adlandırılır.

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-|-|
| Operatör | <ul><li>Azure portal: `Equal` , `Not Equal`</li><li>ARM şablonlarında: `Equal` ; `negateCondition` _eşit değil_ belirtmek için özelliğini kullanın |
| İstek yöntemi | `HTTP`, `HTTPS` |

### <a name="example"></a>Örnek

Bu örnekte, isteğin Protokolü kullandığı tüm istekleri eşleştik `HTTP` .

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-protocol.png" alt-text="İstek Protokolü eşleşme koşulunu gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestScheme",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "HTTP"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestScheme'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'HTTP
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters'
  }
}
```

---

## <a name="request-url"></a><a name="RequestUrl"></a> İstek URL 'SI

Belirtilen URL ile eşleşen istekleri tanımlar. Protokol ve sorgu dizesi dahil olmak üzere tüm URL değerlendirilir, ancak parçadır. Eşlemek için birden çok değer belirtebilirsiniz, bu, OR mantığı kullanılarak birleştirilir.

> [!TIP]
> Bu kural koşulunu kullandığınızda, protokolü eklediğinizden emin olun. Örneğin, `https://www.contoso.com` yalnızca yerine kullanın `www.contoso.com` .

### <a name="properties"></a>Özellikler

| Özellik | Desteklenen değerler |
|-|-|
| Operatör | [Standart işleç listesinden](#operator-list)herhangi bir operatör. |
| Değer | Eşleştirilecek istek URL 'sinin değerini temsil eden bir veya daha fazla dize veya tamsayı değeri. Birden çok değer belirtilmişse, veya mantığı kullanılarak değerlendirilir. |
| Örnek dönüştürme | `Lowercase`, `Uppercase` |

### <a name="example"></a>Örnek

Bu örnekte, istek URL 'sinin ile başladığı tüm istekleri eşleştik `https://api.contoso.com/customers/123` . Eşleşmeyi değerlendirmeden önce istek dosyası uzantısını küçük harfe dönüştürüyoruz, bu nedenle istekler `https://api.contoso.com/Customers/123` ve diğer büyük/küçük harf çeşitlemeleri de bu eşleşme koşulunu tetikleyecektir.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-url.png" alt-text="İstek URL 'SI eşleştirme koşulunu gösteren Portal ekran görüntüsü.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestUri",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "https://api.contoso.com/customers/123"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestUri'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'https://api.contoso.com/customers/123'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters'
  }
}
```

---

## <a name="operator-list"></a><a name = "operator-list"></a> İşleç listesi

Standart işleç listesinden değerleri kabul eden kurallar için aşağıdaki işleçler geçerlidir:

| İşleç                   | Açıklama                                                                                                                    | ARM şablon desteği                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Herhangi biri                        | Ne olursa olsun, herhangi bir değer olduğunda eşleşir.                                                                     | `operator`: `Any`                                               |
| Eşittir                      | Değer belirtilen dizeyle tam olarak eşleştiğinde eşleşir.                                                                   | `operator`: `Equal`                                             |
| Contains                   | Değer belirtilen dizeyi içerdiğinde eşleşir.                                                                          | `operator`: `Contains`                                          |
| Küçüktür                  | Değerin uzunluğu belirtilen tamsayıdan daha az olduğunda eşleşir.                                                       | `operator`: `LessThan`                                          |
| Büyüktür               | Değerin uzunluğu belirtilen tamsayıdan daha büyükse eşleşir.                                                    | `operator`: `GreaterThan`                                       |
| Küçüktür veya eşittir         | Değerin uzunluğu belirtilen tamsayıdan küçük veya ona eşit olduğunda eşleşir.                                           | `operator`: `LessThanOrEqual`                                   |
| Büyük veya eşittir      | Değerin uzunluğu belirtilen tamsayıdan büyük veya buna eşitse eşleşir.                                        | `operator`: `GreaterThanOrEqual`                                |
| Şununla Başlar:                | Değer belirtilen dizeyle başlıyorsa eşleşir.                                                                       | `operator`: `BeginsWith`                                        |
| Şununla Biter:                  | Değer belirtilen dizeyle sona erdiğinde eşleşir.                                                                         | `operator`: `EndsWith`                                          |
| RegEx                      | Değer belirtilen normal ifadeyle eşleştiğinde eşleşir. [Daha fazla ayrıntı için aşağıya bakın.](#regular-expressions)        | `operator`: `RegEx`                                             |
| Hiçbirini değil                    | Değer olmadığında eşleşir.                                                                                                | `operator`: `Any` ve `negateCondition` : `true`                |
| Eşit değildir                  | Değer belirtilen dizeyle eşleşmezse eşleşir.                                                                    | `operator`: `Equal` ve `negateCondition` : `true`              |
| İçermez               | Değer belirtilen dizeyi içermiyorsa eşleşir.                                                                  | `operator`: `Contains` ve `negateCondition` : `true`           |
| Küçüktür              | Değerin uzunluğu belirtilen tamsayıdan daha az olmadığında eşleşir.                                                   | `operator`: `LessThan` ve `negateCondition` : `true`           |
| Şundan büyük değil           | Değerin uzunluğu belirtilen tamsayıdan büyük olmadığında eşleşir.                                                | `operator`: `GreaterThan` ve `negateCondition` : `true`        |
| Küçük veya eşit değil     | Değerin uzunluğu belirtilen tamsayıdan küçük veya ona eşit olmadığında eşleşir.                                       | `operator`: `LessThanOrEqual` ve `negateCondition` : `true`    |
| Büyük veya eşit değil | Değerin uzunluğu belirtilen tamsayıdan büyük veya ona eşit olmadığında eşleşir.                                    | `operator`: `GreaterThanOrEqual` ve `negateCondition` : `true` |
| Şununla başlamaktadır            | Değer belirtilen dizeyle başlamadığınızda eşleşir.                                                               | `operator`: `BeginsWith` ve `negateCondition` : `true`         |
| Şununla bitmiyor              | Değer belirtilen dizeyle bitmezse eşleşir.                                                                 | `operator`: `EndsWith` ve `negateCondition` : `true`           |
| RegEx değil                  | Değer, belirtilen normal ifadeyle eşleşmezse eşleşir. [Daha fazla ayrıntı için aşağıya bakın.](#regular-expressions) | `operator`: `RegEx` ve `negateCondition` : `true`              |

> [!TIP]
> *Küçüktür ve* *büyüktür* gibi sayısal işleçler için, kullanılan karşılaştırma uzunluğa göre belirlenir. Match koşulunun değeri, karşılaştırmak istediğiniz uzunluğu belirten bir tamsayı olmalıdır.

### <a name="regular-expressions"></a><a name="regular-expressions"></a> Normal ifadeler

Normal ifadeler aşağıdaki işlemleri desteklemez:

* Geri başvurular ve yakalama alt ifadeleri.
* Rastgele sıfır Genişlik onaylamaları.
* Alt yordam başvuruları ve özyinelemeli desenler.
* Koşullu desenler.
* Geri izleme denetim fiilleri.
* `\C`Tek baytlık yönerge.
* `\R`Yeni satır eşleştirme yönergesi.
* `\K`Eşleşme sıfırlama yönergesinin başlangıcı.
* Belirtme çizgileri ve gömülü kod.
* Atomik gruplandırma ve sahip nicelik belirteçleri.

## <a name="next-steps"></a>Sonraki adımlar

* [Kural kümesi](concept-rule-set.md)hakkında daha fazla bilgi edinin.
* [Ilk kural kümesini nasıl yapılandıracağınızı](how-to-configure-rule-set.md)öğrenin.
* [Kural kümesi eylemleri](concept-rule-set-actions.md)hakkında daha fazla bilgi edinin.
