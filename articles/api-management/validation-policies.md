---
title: Azure API Management doğrulama ilkeleri | Microsoft Docs
description: İstekleri ve yanıtları doğrulamak için Azure API Management 'da kullanabileceğiniz ilkeler hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/12/2021
ms.author: apimpm
ms.openlocfilehash: 1a835d26b4c41c92b9849856a2f31b3550947bd8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801902"
---
# <a name="api-management-policies-to-validate-requests-and-responses"></a>İstekleri ve yanıtları doğrulamak için ilkeleri API Management

Bu makalede, aşağıdaki API Management ilkeleri için bir başvuru sağlanmaktadır. İlke ekleme ve yapılandırma hakkında daha fazla bilgi için bkz. [API Management ilkeleri](./api-management-policies.md).

Bir Openapı şemasında API isteklerini ve yanıtlarını doğrulamak ve üstbilgiler veya yük ekleme gibi güvenlik açıklarına karşı korumak için doğrulama ilkelerini kullanın. Bir Web uygulaması güvenlik duvarının yerini almayan bir doğrulama ilkesi, statik, önceden tanımlanmış kurallara dayanan güvenlik ürünlerinin kapsamadığı ek bir tehdit sınıfına yanıt verme esnekliği sağlar.

## <a name="validation-policies"></a>Doğrulama ilkeleri

- [Içeriği doğrula](#validate-content) -API şemasında bir isteğin veya Yanıt gövdesinin boyutunu veya JSON şemasını doğrular. 
- [Parametreleri doğrula](#validate-parameters) -istek üst bilgisini, sorguyu veya yol parametrelerini API şemasına göre doğrular.
- [Üstbilgileri doğrula](#validate-headers) -yanıt üstbilgilerini API şemasına göre doğrular.
- [Durum kodunu doğrula](#validate-status-code) -API şemasına karşı yanıt olarak http durum kodlarını doğrular.

> [!NOTE]
> Bir doğrulama ilkesi tarafından kullanılabilen en büyük API şeması boyutu 4 MB 'tır. Şema bu sınırı aşarsa, doğrulama ilkeleri çalışma zamanında hata döndürür. Bunu artırmak için lütfen [desteğe](https://azure.microsoft.com/support/options/)başvurun. 

## <a name="actions"></a>Eylemler

Her doğrulama ilkesi, API isteğinde bir varlık doğrulanırken veya API şemasına karşı yanıtta API Management bir eylemi belirten bir özniteliği içerir. API şemasında temsil edilmeyen öğeler için bir eylem belirtilebilir ve bu, ilkeye bağlı olarak API şemasında temsil edilmeyen öğeler içindir. Bir ilkenin alt öğesinde belirtilen bir eylem, üst öğesi için belirtilen bir eylemi geçersiz kılar.

Kullanılabilir eylemler:

| Eylem         | Açıklama          |                                                                                                                         
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| yoksayma | Doğrulamayı atlayın. |
| önlemek | İstek veya Yanıt işlemeyi engelleyin, ayrıntılı doğrulama hatasını günlüğe kaydedin ve bir hata döndürün. İlk hata kümesi algılandığında işleme kesintiye uğrar. |
| detect | İstek veya Yanıt işlemeyi kesintiye uğramadan doğrulama hatalarını günlüğe kaydedin. |

## <a name="logs"></a>Günlükler

İlke yürütme sırasında doğrulama hatalarıyla ilgili ayrıntılar, `context.Variables` `errors-variable-name` ilkenin kök öğesindeki özniteliğinde belirtilen değişkende günlüğe kaydedilir. Bir `prevent` eylemde yapılandırıldığında, doğrulama hatası daha fazla istek veya Yanıt işlemesini engeller ve ayrıca özelliğine de yayılır `context.LastError` . 

Hataları araştırmak için, bağlam değişkenlerinden [Application Insights](api-management-howto-app-insights.md)hataları günlüğe kaydetmek için bir [izleme](api-management-advanced-policies.md#Trace) İlkesi kullanın.

## <a name="performance-implications"></a>Performans üzerindeki etkileri

Doğrulama ilkeleri eklemek, API aktarım hızını etkileyebilir. Aşağıdaki genel ilkeler geçerlidir:
* API şeması boyutunun daha büyük olması, düşük aktarım hızı olacaktır. 
* Bir istek veya Yanıt içindeki yükün ne kadar büyük olması, aktarım hızı düşük olacaktır. 
* API şemasının boyutunun performansı, yükün boyutundan daha büyük bir etkiye sahiptir. 
* Boyutu birkaç megabayt olan bir API şemasına karşı doğrulama, bazı koşullarda istek veya yanıt zaman aşımları oluşmasına neden olabilir. Bu efekt, hizmetin  **Tüketim** ve **Geliştirici** katmanlarında daha fazla önemlidir. 

API aktarım hızı üzerinde doğrulama ilkelerinin etkilerini değerlendirmek için beklenen üretim iş yüklerinizle yük testlerini gerçekleştirmenizi öneririz.

## <a name="validate-content"></a>İçeriği doğrula

`validate-content`İlke, bir isteğin veya Yanıt gövdesinin boyut veya JSON ŞEMASıNı API şemasına göre doğrular. JSON dışındaki biçimler desteklenmez.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<validate-content unspecified-content-type-action="ignore|prevent|detect" max-size="size in bytes" size-exceeded-action="ignore|prevent|detect" errors-variable-name="variable name">
    <content type="content type string, for example: application/json, application/hal+json" validate-as="json" action="ignore|prevent|detect" />
</validate-content>
```

### <a name="example"></a>Örnek

Aşağıdaki örnekte, isteklerde ve yanıtlarındaki JSON yükü algılama modunda onaylanır. 100 KB 'den büyük yükleri olan iletiler engellenir. 

```xml
<validate-content unspecified-content-type-action="prevent" max-size="102400" size-exceeded-action="prevent" errors-variable-name="requestBodyValidation">
    <content type="application/json" validate-as="json" action="detect" />
    <content type="application/hal+json" validate-as="json" action="detect" />
</validate-content>

```

### <a name="elements"></a>Öğeler

| Ad         | Açıklama                                                                                                                                   | Gerekli |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Validate-içerik | Kök öğe.                                                                                                                               | Yes      |
| içerik | İstek veya yanıtta içerik türünü doğrulamak için bu öğelerden bir veya daha fazlasını ekleyin ve belirtilen eylemi gerçekleştirin.  | No |

### <a name="attributes"></a>Öznitelikler

| Ad                       | Açıklama                                                                                                                                                            | Gerekli | Varsayılan |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| belirtilmemiş-içerik-tür-eylem | API şemasında belirtilmeyen bir içerik türüne sahip istekler veya yanıtlar için gerçekleştirilecek [eylem](#actions) . |  Yes     | Yok   |
| en büyük boyut | Üst bilgiye göre denetlenen istek veya Yanıt gövdesinin bayt olarak en fazla uzunluğu `Content-Length` . İstek gövdesi veya yanıt gövdesi sıkıştırılmışsa, bu değer sıkıştırması açılmış uzunluktadır. İzin verilen maksimum değer: 102.400 bayt (100 KB).  | Yes       | Yok   |
| Boyut aşıldı-eylem | Gövdesi ' de belirtilen boyutu aşan istekler veya yanıtlar için gerçekleştirilecek [eylem](#actions) `max-size` . |  Yes     | Yok   |
| hatalar-değişken adı | `context.Variables`Doğrulama hatalarını günlüğe kaydetmek için içindeki değişkenin adı.  |   Yes    | Yok   |
| tür | Üst bilgiye karşı denetlenen, için gövde doğrulamasını yürütmek için içerik türü `Content-Type` . Bu değer büyük/küçük harfe duyarlıdır. Boşsa, API şemasında belirtilen her içerik türü için geçerlidir. |   No    |  Yok  |
| farklı doğrula | Eşleşen bir içerik türüne sahip bir istek veya Yanıt gövdesinin doğrulanması için kullanılacak doğrulama altyapısı. Şu anda yalnızca "JSON" değeri desteklenir.   |  Yes     |  Yok  |
| eylem | Gövdesi belirtilen içerik türüyle eşleşmeyen istekler veya yanıtlar için gerçekleştirilecek [eylem](#actions) .  |  Yes      | Yok   |

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen, giden, hata üzerine

-   **İlke kapsamları:** tüm kapsamlar

## <a name="validate-parameters"></a>Parametreleri doğrula

`validate-parameters`İlke, API şemasına karşı isteklerdeki üst bilgi, sorgu veya yol parametrelerini doğrular.

> [!IMPORTANT]
> ' Den önceki bir yönetim API sürümünü kullanarak bir API içeri aktardıysanız `2021-01-01-preview` , `validate-parameters` ilke çalışmayabilir. API 'nizi yönetim API 'SI sürümünü veya üstünü kullanarak [yeniden içe aktarmanız](/rest/api/apimanagement/2021-01-01-preview/apis/createorupdate) gerekebilir `2021-01-01-preview` .


### <a name="policy-statement"></a>İlke ekstresi

```xml
<validate-parameters specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect" errors-variable-name="variable name"> 
    <headers specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </headers>
    <query specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </query>
    <path specified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </path>
</validate-parameters>
```

### <a name="example"></a>Örnek

Bu örnekte, tüm sorgu ve yol parametreleri engelleme modunda ve algılama modundaki üst bilgilerde onaylanır. Birkaç üstbilgi parametresi için doğrulama geçersiz kılındı:

```xml
<validate-parameters specified-parameter-action="prevent" unspecified-parameter-action="prevent" errors-variable-name="requestParametersValidation"> 
    <headers specified-parameter-action="detect" unspecified-parameter-action="detect">
        <parameter name="Authorization" action="prevent" />
        <parameter name="User-Agent" action="ignore" />
        <parameter name="Host" action="ignore" />
        <parameter name="Referrer" action="ignore" />
    </headers>   
</validate-parameters>
```

### <a name="elements"></a>Öğeler

| Ad         | Açıklama                                                                                                                                   | Gerekli |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Validate parametreleri | Kök öğe. İsteklerde tüm parametrelerin varsayılan doğrulama eylemlerini belirtir.                                                                                                                              | Yes      |
| bilgisinde | İsteklerdeki üst bilgi parametrelerine yönelik varsayılan doğrulama eylemlerini geçersiz kılmak için bu öğeyi ekleyin.   | No |
| sorgu | İsteklerdeki sorgu parametreleri için varsayılan doğrulama eylemlerini geçersiz kılmak için bu öğeyi ekleyin.  | No |
| path | İsteklerde URL yolu parametreleri için varsayılan doğrulama eylemlerini geçersiz kılmak için bu öğeyi ekleyin.  | No |
| parametre | Doğrulama eylemlerinin daha üst düzey yapılandırmasını geçersiz kılmak için adlandırılmış parametreler için bir veya daha fazla öğe ekleyin. | No |

### <a name="attributes"></a>Öznitelikler

| Ad                       | Açıklama                                                                                                                                                            | Gerekli | Varsayılan |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| Belirtilen parametre-eylem | API şemasında belirtilen istek parametreleri için gerçekleştirilecek [eylem](#actions) . <br/><br/> Bir `headers` , `query` veya öğesinde sağlandığında, `path` değeri öğesindeki değerini geçersiz kılar `specified-parameter-action` `validate-parameters` .  |  Yes     | Yok   |
| belirtilmemiş-parametre-eylem | API şemasında belirtilmeyen istek parametreleri için gerçekleştirilecek [eylem](#actions) . <br/><br/>Bir `headers` veya `query` öğesinde sağlandığında, değeri öğesindeki değerini geçersiz kılar `unspecified-parameter-action` `validate-parameters` . |  Yes     | Yok   |
| hatalar-değişken adı | `context.Variables`Doğrulama hatalarını günlüğe kaydetmek için içindeki değişkenin adı.  |   Yes    | Yok   |
| name | Doğrulama eyleminin geçersiz kılınması için parametrenin adı. Bu değer büyük/küçük harfe duyarlıdır.  | Yes | Yok |
| eylem | Eşleşen ada sahip parametre için gerçekleştirilecek [eylem](#actions) . Parametresi, API şemasında belirtilmişse, bu değer üst düzey yapılandırmayı geçersiz kılar `specified-parameter-action` . Parametre API şemasında belirtilmemişse, bu değer üst düzey yapılandırmayı geçersiz kılar `unspecified-parameter-action` .| Yes | Yok | 

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen

-   **İlke kapsamları:** tüm kapsamlar

## <a name="validate-headers"></a>Üstbilgileri doğrula

`validate-headers`İlke, yanıt ÜSTBILGILERINI API şemasına göre doğrular.

> [!IMPORTANT]
> ' Den önceki bir yönetim API sürümünü kullanarak bir API içeri aktardıysanız `2021-01-01-preview` , `validate-headers` ilke çalışmayabilir. API 'nizi yönetim API 'SI sürümünü veya üstünü kullanarak yeniden içe aktarmanız gerekebilir `2021-01-01-preview` .

### <a name="policy-statement"></a>İlke ekstresi

```xml
<validate-headers specified-header-action="ignore|prevent|detect" unspecified-header-action="ignore|prevent|detect" errors-variable-name="variable name">
    <header name="header name" action="ignore|prevent|detect" />
</validate-headers>
```

### <a name="example"></a>Örnek

```xml
<validate-headers specified-header-action="ignore" unspecified-header-action="prevent" errors-variable-name="responseHeadersValidation" />
```
### <a name="elements"></a>Öğeler

| Ad         | Açıklama                                                                                                                                   | Gerekli |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| üstbilgileri doğrula | Kök öğe. Yanıtlarındaki tüm üst bilgiler için varsayılan doğrulama eylemlerini belirtir.                                                                                                                              | Yes      |
| üst bilgi | Yanıtlarındaki üst bilgiler için varsayılan doğrulama eylemlerini geçersiz kılmak üzere adlandırılmış üst bilgiler için bir veya daha fazla öğe ekleyin. | No |

### <a name="attributes"></a>Öznitelikler

| Ad                       | Açıklama                                                                                                                                                            | Gerekli | Varsayılan |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| belirtilen-üst bilgi-eylem | API şemasında belirtilen yanıt üst bilgileri için gerçekleştirilecek [eylem](#actions) .  |  Yes     | Yok   |
| belirtilmemiş-üst bilgi-eylem | API şemasında belirtilmeyen yanıt üst bilgileri için gerçekleştirilecek [eylem](#actions) .  |  Yes     | Yok   |
| hatalar-değişken adı | `context.Variables`Doğrulama hatalarını günlüğe kaydetmek için içindeki değişkenin adı.  |   Yes    | Yok   |
| name | Doğrulama eyleminin geçersiz kılınması için üst bilginin adı. Bu değer büyük/küçük harfe duyarlıdır. | Yes | Yok |
| eylem | Eşleşen ada sahip üst bilgi için gerçekleştirilecek [eylem](#actions) . Üst bilgi API şemasında belirtilmişse, bu değer öğesindeki değerini geçersiz kılar `specified-header-action` `validate-headers` . Aksi takdirde, `unspecified-header-action` Validate-Headers öğesindeki değerini geçersiz kılar. | Yes | Yok | 

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** giden, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar

## <a name="validate-status-code"></a>Durum kodunu doğrula

`validate-status-code`İlke, API şemasına karşı yanıt olarak http durum kodlarını doğrular. Bu ilke yığın izlemeleri içerebilen arka uç hatalarının sızıntılarını engellemek için kullanılabilir. 

### <a name="policy-statement"></a>İlke ekstresi

```xml
<validate-status-code unspecified-status-code-action="ignore|prevent|detect" errors-variable-name="variable name">
    <status-code code="HTTP status code number" action="ignore|prevent|detect" />
</validate-status-code>
```

### <a name="example"></a>Örnek

```xml
<validate-status-code unspecified-status-code-action="prevent" errors-variable-name="responseStatusCodeValidation" />
```

### <a name="elements"></a>Öğeler

| Ad         | Açıklama                                                                                                                                   | Gerekli |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| doğrulama-durum kodu | Kök öğe.                                                                                                | Yes      |
| durum kodu | Yanıtlarındaki durum kodları için varsayılan doğrulama eylemini geçersiz kılmak üzere HTTP durum kodları için bir veya daha fazla öğe ekleyin. | No |

### <a name="attributes"></a>Öznitelikler

| Ad                       | Açıklama                                                                                                                                                            | Gerekli | Varsayılan |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| belirtilmemiş-durum-kod-eylem | API şemasında belirtilmeyen yanıtlara yönelik HTTP durum kodları için gerçekleştirilecek [eylem](#actions) .  |  Yes     | Yok   |
| hatalar-değişken adı | `context.Variables`Doğrulama hatalarını günlüğe kaydetmek için içindeki değişkenin adı.  |   Yes    | Yok   |
| kod | Doğrulama eyleminin geçersiz kılınması için HTTP durum kodu. | Yes | Yok |
| eylem | API şemasında belirtilmeyen, eşleşen durum kodu için gerçekleştirilecek [eylem](#actions) . Durum kodu API şemasında belirtilmişse, bu geçersiz kılma etkili olmaz. | Yes | Yok | 

### <a name="usage"></a>Kullanım

Bu ilke, aşağıdaki ilke [bölümlerinde](./api-management-howto-policies.md#sections) ve [kapsamlarda](./api-management-howto-policies.md#scopes)kullanılabilir.

-   **İlke bölümleri:** giden, hata durumunda

-   **İlke kapsamları:** tüm kapsamlar


## <a name="validation-errors"></a>Doğrulama hataları
Aşağıdaki tabloda, doğrulama ilkelerinin olası tüm hataları listelenmektedir. 

* **Ayrıntılar** -hataları araştırmak için kullanılabilir. Herkese açık şekilde paylaşılmamıştır.
* **Genel Yanıt** -istemciye hata döndürüldü. Uygulama ayrıntılarını sızıntısına neden olmaz.

| **Ad**                             | **Tür**                                                        | **Doğrulama kuralı** | **Ayrıntılar**                                                                                                                                       | **Genel Yanıt**                                                                                                                       | **Eylem**           |
|----|----|---|---|---|----|
| **Validate-içerik** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| |Istek gövdesi                                                     | SizeLimit           | İsteğin gövdesi {size} bayt uzunluğundadır ve yapılandırılan {maxSize} baytlık sınırı aşıyor.                                                       | İsteğin gövdesi {size} bayt uzunluğundadır ve {maxSize} baytlık sınırı aşıyor.                                                          | Algıla/önle |
||Yanıt gövdesi                                                    | SizeLimit           | Yanıtın gövdesi {size} bayt uzunluğundadır ve yapılandırılan {maxSize} baytlık sınırı aşıyor.                                                      | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
| {messageContentType}                 | Istek gövdesi                                                     | Belirtilmemiş         | Belirtilmeyen [messageContentType} içerik türüne izin verilmiyor.                                                                                     | Belirtilmeyen [messageContentType} içerik türüne izin verilmiyor.                                                                             | Algıla/önle |
| {messageContentType}                 | Yanıt gövdesi                                                    | Belirtilmemiş         | Belirtilmeyen [messageContentType} içerik türüne izin verilmiyor.                                                                                     | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
| | ApiSchema                                                       |                     | API 'nin şeması yok veya çözümlenemedi.                                                                                          | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
|                                      | ApiSchema                                                       |                     | API 'nin şeması tanımlar belirtmiyor.                                                                                                        | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
| {messageContentType}                 | Istek gövdesi/yanıt gövdesi                                      | MissingDefinition   | API 'nin şeması, {messageContentType} içerik türüyle ilişkili {definitionName} tanımını içermiyor.                        | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
| {messageContentType}                 | Istek gövdesi                                                     | IncorrectMessage    | İsteğin gövdesi {messageContentType} içerik türüyle ilişkili {definitionName} tanımına uymuyor.<br/><br/>{valError. Message} satır: {valError. LineNumber}, konum: {valError. LinePosition}                  | İsteğin gövdesi {messageContentType} içerik türüyle ilişkili {definitionName} tanımına uymuyor.<br/><br/>{valError. Message} satır: {valError. LineNumber}, konum: {valError. LinePosition}            | Algıla/önle |
| {messageContentType}                 | Yanıt gövdesi                                                    | IncorrectMessage    | Yanıtın gövdesi {messageContentType} içerik türüyle ilişkili {definitionName} tanımına uymuyor.<br/><br/>{valError. Message} satır: {valError. LineNumber}, konum: {valError. LinePosition}                                       | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
|                                      | Istek gövdesi                                                     | ValidationException | İsteğin gövdesi {messageContentType} içerik türü için doğrulanamıyor.<br/><br/>{özel durum ayrıntıları}                                                                | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
|                                      | Yanıt gövdesi                                                    | ValidationException | Yanıt gövdesi {messageContentType} içerik türü için doğrulanamıyor.<br/><br/>{özel durum ayrıntıları}                                                                | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
| **Validate-parametre/Validate-üstbilgiler** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {paramName}/{headerName}           | QueryParameter/PathParameter/RequestHeader                  | Belirtilmemiş         | Belirtilmemiş {yol parametresi/sorgu parametresi/üst bilgi} {paramName} öğesine izin verilmiyor.                                                               | Belirtilmemiş {yol parametresi/sorgu parametresi/üst bilgi} {paramName} öğesine izin verilmiyor.                                                       | Algıla/önle |
| HeaderName                         | ResponseHeader                                                  | Belirtilmemiş         | Belirtilmeyen {headerName} başlığına izin verilmiyor.                                                                                                   | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
|                                      |ApiSchema                                                       |                     | API 'nin şeması yok veya çözümlenemedi.                                                                                            | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
|                                       | ApiSchema                                                       |                     | API şeması tanımlar belirtmiyor.                                                                                                          | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
| paramName                          | QueryParameter/PathParameter/RequestHeader/ResponseHeader | MissingDefinition   | API 'nin şeması {sorgu parametresi/yol parametresi/üstbilgisi} {paramName} ile ilişkili {definitionName} tanımını içermiyor.  | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
| paramName                          | QueryParameter/PathParameter/RequestHeader                  | IncorrectMessage    | İstek {sorgu parametresi/yol parametresi/üstbilgisi} {paramName} için birden çok değer içeremez.                                           | İstek {sorgu parametresi/yol parametresi/üstbilgisi} {paramName} için birden çok değer içeremez.                                   | Algıla/önle |
| HeaderName                         | ResponseHeader                                                  | IncorrectMessage    | Yanıt, {headerName} üst bilgisi için birden çok değer içeremez.                                                                              | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
| paramName                          | QueryParameter/PathParameter/RequestHeader                  | IncorrectMessage    | {ParamName} {sorgu parametresi/yol parametresinin/üstbilgisinin değeri tanıma uymuyor.<br/><br/>{valError. Message} satır: {valError. LineNumber}, konum: {valError. LinePosition}                                          | {Sorgu parametresi/yol parametresi/üstbilgi} {paramName} değeri tanıma uymuyor.<br/><br/>{valError. Message} satır: {valError. LineNumber}, konum: {valError. LinePosition}                              | Algıla/önle |
| HeaderName                         | ResponseHeader                                                  | IncorrectMessage    | {HeaderName} üstbilgisinin değeri tanıma uymuyor.<br/><br/>{valError. Message} satır: {valError. LineNumber}, konum: {valError. LinePosition}                                                                              | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
| paramName                          | QueryParameter/PathParameter/RequestHeader                  | IncorrectMessage    | {ParamName} {sorgu parametresi/yol parametresinin/üstbilgisinin değeri tanıma göre ayrıştırılamıyor. <br/><br/>değerine. Mesaj                                | {Sorgu parametresi/yol parametresi/üstbilgisi} {paramName} değeri tanıma göre ayrıştırılamadı. <br/><br/>değerine. Mesaj                      | Algıla/önle |
| HeaderName                         | ResponseHeader                                                  | IncorrectMessage    | {HeaderName} üstbilgisinin değeri tanıma göre ayrıştırılamadı.                                                                  | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
| paramName                          | QueryParameter/PathParameter/RequestHeader                  | Doğrulama hatası     | {Sorgu parametresi/yol parametresi/üstbilgisi} {paramName} doğrulanamıyor.<br/><br/>{özel durum ayrıntıları}                                                                      | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
| HeaderName                         | ResponseHeader                                                  | Doğrulama hatası     | {HeaderName} üst bilgisi doğrulanamıyor.<br/><br/>{özel durum ayrıntıları}                                                                                                          | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |
| **doğrulama-durum kodu**             |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {Status-Code}                        | Durum                                                      | Belirtilmemiş         | {Status-Code} yanıt durum koduna izin verilmez.                                                                                                | Bir iç hata nedeniyle istek işlenemedi. API sahibine başvurun.                                                       | Algıla/önle |


Aşağıdaki tabloda, olası Ileti değerleriyle birlikte doğrulama hatasının tüm olası neden değerleri listelenmektedir:

|  **Nedeni**         |    **İleti** |
|---|---|  
| Hatalı istek       |     {Details} bağlam değişkeni için, {Public Response} for Client|
| Yanıta izin verilmiyor  | {Details} bağlam değişkeni için, {Public Response} for Client |






## <a name="next-steps"></a>Sonraki adımlar

İlkelerle çalışma hakkında daha fazla bilgi için bkz.:

-   [API Management ilkeler](api-management-howto-policies.md)
-   [API dönüştürme](transform-api.md)
-   İlke deyimlerinin ve ayarlarının tam listesi için [ilke başvurusu](./api-management-policies.md)
-   [İlke örnekleri](./policy-reference.md)
-   [Hata işleme](./api-management-error-handling-policies.md)
