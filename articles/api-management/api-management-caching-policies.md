---
title: Azure API Management önbelleğe alma ilkeleri | Microsoft Docs
description: Azure API Management 'de kullanıma sunulan önbelleğe alma ilkeleri hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 06c4ede12f939e48973d3e0b502d90b848d199bb
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072619"
---
# <a name="api-management-caching-policies"></a>API Management önbelleğe alma ilkeleri
Bu konu, aşağıdaki API Management ilkelerine yönelik bir başvuru sağlar. İlke ekleme ve yapılandırma hakkında daha fazla bilgi için bkz. [API Management ilkeleri](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CachingPolicies"></a>Önbelleğe alma ilkeleri

- Yanıt önbelleğe alma ilkeleri
    - [Önbellekten al](api-management-caching-policies.md#GetFromCache) -önbellek araması yapın ve kullanılabilir olduğunda geçerli bir önbelleğe alınmış yanıt döndürür.
    - [Önbellekte depola](api-management-caching-policies.md#StoreToCache) -belirtilen önbellek denetimi yapılandırmasına göre yanıtları önbelleğe alır.
- Değer önbelleğe alma ilkeleri
    - [Önbellekten değer al](#GetFromCacheByKey) -önbelleğe alınmış bir öğeyi anahtara göre alır.
    - [Önbellekte depolama değeri](#StoreToCacheByKey) -bir öğeyi bir anahtarla önbelleğe saklayın.
    - [Önbellekten değer kaldır](#RemoveCacheByKey) -önbellekteki bir öğeyi anahtarla kaldırın.

## <a name="GetFromCache"></a>Önbellekten al
`cache-lookup` İlkeyi kullanarak önbellek araması gerçekleştirin ve kullanılabilir olduğunda geçerli bir önbelleğe alınmış yanıt döndürün. Bu ilke, yanıt içeriğinin bir süre içinde statik kaldığı durumlarda uygulanabilir. Yanıt önbelleğe alma, arka uç Web sunucusunda uygulanan bant genişliğini ve işleme gereksinimlerini azaltır ve API tüketicileri tarafından algılanan gecikme süresini düşürür.

> [!NOTE]
> Bu ilkenin [önbellek ilkesi için](api-management-caching-policies.md#StoreToCache) karşılık gelen bir deposu olması gerekir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

### <a name="examples"></a>Örnekler

#### <a name="example"></a>Örnek

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>İlke ifadelerini kullanan örnek
Bu örnek, desteklenen hizmet `Cache-Control` yönergesi tarafından belirtilen şekilde arka uç hizmetinin yanıt önbelleği ile eşleşen API Management yanıtı önbelleğe alma süresinin nasıl yapılandırılacağını gösterir. Bu ilkeyi yapılandırma ve kullanma tanıtımı için bkz [. bulut kapak bölümü 177: Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) ile daha fazla API Management özellik ve 25:25 'e ileri sarma.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Daha fazla bilgi için bkz. [ilke ifadeleri](api-management-policy-expressions.md) ve [bağlam değişkeni](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Öğeler

|Name|Açıklama|Gerekli|
|----------|-----------------|--------------|
|Cache-arama|Kök öğe.|Evet|
|üst bilgiye göre değişiklik|Belirtilen üst bilgi başına, kabul etme, kabul-karakter, kabul-kodlama, kabul-dil, yetkilendirme, beklenen, kimden, ana bilgisayar, IF-Match gibi bir değer için önbelleğe alma yanıtı başlatın.|Hayır|
|sorguya göre değişir-parametre|Belirtilen sorgu parametrelerinin değeri başına önbelleğe alma işlemini başlat. Tek veya birden çok parametre girin. Ayırıcı olarak noktalı virgül kullanın. Hiçbiri belirtilmemişse, tüm sorgu parametreleri kullanılır.|Hayır|

### <a name="attributes"></a>Öznitelikler

| Name                           | Açıklama                                                                                                                                                                                                                                                                                                                                                 | Gerekli | Varsayılan           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| Allow-Private-Response-Caching | Olarak `true`ayarlandığında, yetkilendirme üst bilgisi içeren isteklerin önbelleğe alınmasına izin verir.                                                                                                                                                                                                                                                                        | Hayır       | false             |
| önbelleğe alma türü               | Özniteliğin aşağıdaki değerleri arasında seçim yapın:<br />- `internal`yerleşik API Management önbelleğini kullanmak için<br />- `external`Dış önbelleği [azure API Management redsıs için dış Azure önbelleği kullanma](api-management-howto-cache-external.md)bölümünde açıklanan şekilde kullanmak için,<br />- `prefer-external`yapılandırılmışsa veya iç önbellekte değilse dış önbelleği kullanmak için. | Hayır       | `prefer-external` |
| aşağı akış-önbelleğe alma türü        | Bu özniteliğin aşağıdaki değerlerden birine ayarlanması gerekir.<br /><br /> -None-aşağı akış önbelleğe almaya izin verilmez.<br />-Özel-aşağı akış özel önbelleğe almaya izin verilir.<br />-Genel-özel ve paylaşılan aşağı akış önbelleğe almaya izin verilir.                                                                                                          | Hayır       | yok              |
| yeniden doğrulama gerekir                | Aşağı akış önbelleğe alma etkinleştirildiğinde bu öznitelik, `must-revalidate` ağ geçidi yanıtlarındaki önbellek denetimi yönergesini açar veya kapatır.                                                                                                                                                                                                                      | Hayır       | true              |
| geliştirici göre farklılık gösterir              | Abonelik anahtarı başına önbelleğe yanıt olarak ayarlanır. [](https://docs.microsoft.com/azure/api-management/api-management-subscriptions) `true`                                                                                                                                                                                                                                                                                                         | Evet      |         False          |
| geliştirici gruplarına göre değişiklik       | Kullanıcı grubu başına önbelleğe yanıt olarak ayarlanır. [](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups) `true`                                                                                                                                                                                                                                                                                                             | Evet      |       False            |

### <a name="usage"></a>Kullanım
Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve kapsamlarda kullanılabilir. [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)

- **İlke bölümleri:** gelen
- **İlke kapsamları:** tüm kapsamlar

## <a name="StoreToCache"></a>Önbelleğe al
İlke `cache-store` , yanıtları belirtilen önbellek ayarlarına göre önbelleğe alır. Bu ilke, yanıt içeriğinin bir süre içinde statik kaldığı durumlarda uygulanabilir. Yanıt önbelleğe alma, arka uç Web sunucusunda uygulanan bant genişliğini ve işleme gereksinimlerini azaltır ve API tüketicileri tarafından algılanan gecikme süresini düşürür.

> [!NOTE]
> Bu ilkenin, karşılık gelen bir [önbellekten alma](api-management-caching-policies.md#GetFromCache) ilkesi olmalıdır.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<cache-store duration="seconds" />
```

### <a name="examples"></a>Örnekler

#### <a name="example"></a>Örnek

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>İlke ifadelerini kullanan örnek
Bu örnek, desteklenen hizmet `Cache-Control` yönergesi tarafından belirtilen şekilde arka uç hizmetinin yanıt önbelleği ile eşleşen API Management yanıtı önbelleğe alma süresinin nasıl yapılandırılacağını gösterir. Bu ilkeyi yapılandırma ve kullanma tanıtımı için bkz [. bulut kapak bölümü 177: Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) ile daha fazla API Management özellik ve 25:25 'e ileri sarma.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Daha fazla bilgi için bkz. [ilke ifadeleri](api-management-policy-expressions.md) ve [bağlam değişkeni](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Öğeler

|Name|Açıklama|Gerekli|
|----------|-----------------|--------------|
|önbellek-mağaza|Kök öğe.|Evet|

### <a name="attributes"></a>Öznitelikler

| Name             | Açıklama                                                                                                                                                                                                                                                                                                                                                 | Gerekli | Varsayılan           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duration         | Önbellekte belirtilen, önbelleğe alınmış girişlerin yaşam süresi (saniye cinsinden).                                                                                                                                                                                                                                                                                                   | Evet      | Yok               |

### <a name="usage"></a>Kullanım
Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve kapsamlarda kullanılabilir. [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)

- **İlke bölümleri:** giden
- **İlke kapsamları:** tüm kapsamlar

## <a name="GetFromCacheByKey"></a>Önbellekten değer al
Anahtarı kullanarak önbellek aramasını gerçekleştirmek ve önbelleğe alınmış bir değer döndürmek için ilkeyikullanın.`cache-lookup-value` Anahtar rastgele bir dize değerine sahip olabilir ve genellikle bir ilke ifadesi kullanılarak sağlanır.

> [!NOTE]
> Bu ilkenin önbellek ilkesinde karşılık gelen bir [Depo değeri](#StoreToCacheByKey) olması gerekir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Örnek
Bu ilkenin daha fazla bilgi ve örnekleri için bkz. [Azure API Management 'Da özel önbelleğe alma](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Öğeler

|Name|Açıklama|Gerekli|
|----------|-----------------|--------------|
|Önbellek-arama-değer|Kök öğe.|Evet|

### <a name="attributes"></a>Öznitelikler

| Name             | Açıklama                                                                                                                                                                                                                                                                                                                                                 | Gerekli | Varsayılan           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| önbelleğe alma türü | Özniteliğin aşağıdaki değerleri arasında seçim yapın:<br />- `internal`yerleşik API Management önbelleğini kullanmak için<br />- `external`Dış önbelleği [azure API Management redsıs için dış Azure önbelleği kullanma](api-management-howto-cache-external.md)bölümünde açıklanan şekilde kullanmak için,<br />- `prefer-external`yapılandırılmışsa veya iç önbellekte değilse dış önbelleği kullanmak için. | Hayır       | `prefer-external` |
| Varsayılan değer    | Önbellek anahtarı arama bir isabetsizlik ile sonuçlanacaksa değişkene atanacak bir değer. Bu öznitelik belirtilmemişse, `null` atanır.                                                                                                                                                                                                           | Hayır       | `null`            |
| key              | Aramada kullanılacak önbellek anahtarı değeri.                                                                                                                                                                                                                                                                                                                       | Evet      | Yok               |
| değişken adı    | Arama başarılı olursa, aranan değere atanacak [bağlam değişkeninin](api-management-policy-expressions.md#ContextVariables) adı. Arama, bir isabetsizlik ile sonuçlanırsa, değişkene `default-value` öznitelik değeri atanır veya `null` `default-value` öznitelik atlanırsa bu değere atanır.                                       | Evet      | Yok               |

### <a name="usage"></a>Kullanım
Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve kapsamlarda kullanılabilir. [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)

- **İlke bölümleri:** gelen, giden, arka uç, hata durumunda
- **İlke kapsamları:** tüm kapsamlar

## <a name="StoreToCacheByKey"></a>Değeri önbellekte depola
Anahtar tarafından önbellek depolaması gerçekleştirir.`cache-store-value` Anahtar rastgele bir dize değerine sahip olabilir ve genellikle bir ilke ifadesi kullanılarak sağlanır.

> [!NOTE]
> Bu ilke, önbellek ilkesindeki karşılık gelen bir [alma değerine](#GetFromCacheByKey) sahip olmalıdır.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Örnek
Bu ilkenin daha fazla bilgi ve örnekleri için bkz. [Azure API Management 'Da özel önbelleğe alma](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Öğeler

|Name|Açıklama|Gerekli|
|----------|-----------------|--------------|
|Cache-Store-değer|Kök öğe.|Evet|

### <a name="attributes"></a>Öznitelikler

| Name             | Açıklama                                                                                                                                                                                                                                                                                                                                                 | Gerekli | Varsayılan           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| önbelleğe alma türü | Özniteliğin aşağıdaki değerleri arasında seçim yapın:<br />- `internal`yerleşik API Management önbelleğini kullanmak için<br />- `external`Dış önbelleği [azure API Management redsıs için dış Azure önbelleği kullanma](api-management-howto-cache-external.md)bölümünde açıklanan şekilde kullanmak için,<br />- `prefer-external`yapılandırılmışsa veya iç önbellekte değilse dış önbelleği kullanmak için. | Hayır       | `prefer-external` |
| duration         | Değer, saniye cinsinden belirtilen Duration değeri için önbelleğe alınır.                                                                                                                                                                                                                                                                                 | Evet      | Yok               |
| key              | Önbellek anahtarı değer altında depolanır.                                                                                                                                                                                                                                                                                                                   | Evet      | Yok               |
| value            | Önbelleğe alınacak değer.                                                                                                                                                                                                                                                                                                                                     | Evet      | Yok               |
### <a name="usage"></a>Kullanım
Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve kapsamlarda kullanılabilir. [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)

- **İlke bölümleri:** gelen, giden, arka uç, hata durumunda
- **İlke kapsamları:** tüm kapsamlar

### <a name="RemoveCacheByKey"></a>Değeri önbellekten kaldır
, `cache-remove-value` Anahtarı tarafından tanımlanan önbelleğe alınmış bir öğeyi siler. Anahtar rastgele bir dize değerine sahip olabilir ve genellikle bir ilke ifadesi kullanılarak sağlanır.

#### <a name="policy-statement"></a>İlke ekstresi

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Örnek

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Öğeler

|Name|Açıklama|Gerekli|
|----------|-----------------|--------------|
|Cache-Remove-Value|Kök öğe.|Evet|

#### <a name="attributes"></a>Öznitelikler

| Name             | Açıklama                                                                                                                                                                                                                                                                                                                                                 | Gerekli | Varsayılan           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| önbelleğe alma türü | Özniteliğin aşağıdaki değerleri arasında seçim yapın:<br />- `internal`yerleşik API Management önbelleğini kullanmak için<br />- `external`Dış önbelleği [azure API Management redsıs için dış Azure önbelleği kullanma](api-management-howto-cache-external.md)bölümünde açıklanan şekilde kullanmak için,<br />- `prefer-external`yapılandırılmışsa veya iç önbellekte değilse dış önbelleği kullanmak için. | Hayır       | `prefer-external` |
| key              | Önbellekten kaldırılacak daha önce önbelleğe alınmış değerin anahtarı.                                                                                                                                                                                                                                                                                        | Evet      | Yok               |

#### <a name="usage"></a>Kullanım
Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve kapsamlarda kullanılabilir. [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)

- **İlke bölümleri:** gelen, giden, arka uç, hata durumunda
- **İlke kapsamları:** tüm kapsamlar

## <a name="next-steps"></a>Sonraki adımlar

İlkelerle çalışma hakkında daha fazla bilgi için bkz.

+ [API Management ilkeler](api-management-howto-policies.md)
+ [API dönüştürme](transform-api.md)
+ İlke deyimlerinin ve ayarlarının tam listesi için [Ilke başvurusu](api-management-policy-reference.md)
+ [İlke örnekleri](policy-samples.md)
