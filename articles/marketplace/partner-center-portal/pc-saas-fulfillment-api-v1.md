---
title: SaaS API v1 Azure Marketi
description: İlişkili karşılama v1 API 'Lerini kullanarak Azure Marketi 'nde SaaS teklifinin nasıl oluşturulacağını ve yönetileceğini açıklar.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ROBOTS: NOINDEX
ms.openlocfilehash: 99dd6db7003e0358ddde2438f6897cd767932227
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816576"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>SaaS karşılama API 'si sürüm 1 (kullanım dışı)

Bu makalede API 'lerle bir SaaS teklifinin nasıl oluşturulacağı açıklanmaktadır. REST yöntemlerinden ve uç noktalardan oluşan API 'Ler, seçili Azure üzerinden satış yaptıysanız SaaS teklifinizin aboneliklerine izin vermek için gereklidir.  

> [!WARNING]
> SaaS karşılama API 'sinin bu ilk sürümü kullanım dışıdır; Bunun yerine, [SaaS karşılama API v2 'yi](./pc-saas-fulfillment-api-v2.md)kullanın.  API 'nin bu başlatma sürümü şu anda yalnızca mevcut yayımcıları sunacak şekilde sağlanıyor. 

SaaS hizmetinizi Azure ile tümleştirmenize yardımcı olmak için aşağıdaki API 'Ler verilmiştir:

-   Çözümleme
-   Abone olun
-   Dönüştür
-   kaldırmak


## <a name="api-methods-and-endpoints"></a>API yöntemleri ve uç noktaları

Aşağıdaki bölümlerde, SaaS teklifi için abonelikleri etkinleştirmek üzere kullanılabilecek API yöntemleri ve uç noktalar açıklanır.


### <a name="marketplace-api-endpoint-and-api-version"></a>Market API uç noktası ve API sürümü

Azure Marketi API 'sinin uç noktası `https://marketplaceapi.microsoft.com`.

Geçerli API sürümü `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Aboneliği çözümle

Bitiş noktasında son eylem, kullanıcıların Market belirtecini kalıcı bir kaynak KIMLIĞINE çözümlemesine izin verir.  Kaynak KIMLIĞI SAAS aboneliğinin benzersiz tanımlayıcısıdır. 

Bir Kullanıcı bir ISV Web sitesine yeniden yönlendirildiğinde, URL sorgu parametrelerinde bir belirteç içerir. ISV 'nin bu belirteci kullanması beklenir ve çözümü çözümlemek için bir istek oluşturun. Yanıt, kaynak için benzersiz SAAS abonelik KIMLIĞI, adı, teklif KIMLIĞI ve planı içerir. Bu belirteç yalnızca saat için geçerlidir.

*İstek*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Parametre adı** |     **Açıklama**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api sürümü        |  Bu istek için kullanılacak işlemin sürümü.   |
|  |  |


*Üst Bilgiler*

| **Üst bilgi anahtarı**     | **Gerekli** | **Açıklama**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-MS-RequestId     | Hayır           | İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır.  |
| x-MS-bağıntıkimliği | Hayır           | İstemcideki işlem için benzersiz bir dize değeri. Bu alan, istemci işlemindeki tüm olayları sunucu tarafındaki olaylarla ilişkilendirir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır. |
| içerik türü       | Evet          | `application/json`                                        |
| yetkisi      | Evet          | JSON Web belirteci (JWT) taşıyıcı belirteci.                    |
| x-MS-Market-belirteç| Evet| Kullanıcı, SaaS ISV 'nin Azure Web sitesine yönlendirildiğinde, URL 'deki belirteç sorgu parametresi. **Note:** Bu belirteç yalnızca 1 saat için geçerlidir. Ayrıca, URL 'YI kullanmadan önce tarayıcıdan belirteç değeri kodunu çözer.|
|  |  |  |
  

*Yanıt gövdesi*

``` json
{
    "id": "",
    "subscriptionName": "",
    "offerId": "",
    "planId": "",
}
```

| **Parametre adı** | **Veri türü** | **Açıklama**                       |
|--------------------|---------------|---------------------------------------|
| id                 | Dize        | SaaS aboneliğinin KIMLIĞI.          |
| subscriptionName| Dize| SaaS hizmetine abone olurken Azure 'da Kullanıcı tarafından ayarlanan SaaS aboneliğinin adı.|
| OfferId            | Dize        | Kullanıcının abone olduğu teklif KIMLIĞI. |
| PlanID             | Dize        | Kullanıcının abone olduğu plan KIMLIĞI.  |
|  |  |  |


*Yanıt kodları*

| **HTTP durum kodu** | **Hata kodu**     | **Açıklama**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Belirteç başarıyla çözüldü.                                                            |
| 400                  | `BadRequest`         | Gerekli üst bilgiler eksik ya da geçersiz bir api sürümü belirtildi. Belirteç hatalı biçimlendirilmiş ya da zaman aşımına uğradığından belirteç çözümlenemedi (belirteç yalnızca oluşturulduktan sonra 1 saat için geçerlidir). |
| 403                  | `Forbidden`          | Çağıranın bu işlemi gerçekleştirme yetkisi yok.                                 |
| 429                  | `RequestThrottleId`  | Hizmet istekleri işlemekle meşgul, daha sonra yeniden deneyin.                                |
| 503                  | `ServiceUnavailable` | Hizmet geçici olarak devre dışı, daha sonra yeniden deneyin.                                        |
|  |  |  |


*Yanıt üst bilgileri*

| **Üst bilgi anahtarı**     | **Gerekli** | **Açıklama**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Evet          | İstemciden alınan istek KIMLIĞI.                                                                   |
| x-MS-bağıntıkimliği | Evet          | Bağıntı KIMLIĞI istemci tarafından geçirilmemişse, bu değer sunucu bağıntı KIMLIĞIDIR.                   |
| x-MS-ActivityId    | Evet          | Hizmetten gelen isteği izlemek için benzersiz bir dize değeri. Bu KIMLIK, herhangi bir mutabakatları için kullanılır. |
| Yeniden deneme-sonrası        | Hayır           | Bu değer yalnızca 429 yanıtı için ayarlanır.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Abone olun

Abone uç noktası, kullanıcıların belirli bir plan için bir SaaS hizmetine abonelik başlatmasını ve ticaret sisteminde faturalandırmayı etkinleştirmesini sağlar.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{SubscriptionID}* ? api-Version = 2017-04-15**

| **Parametre adı**  | **Açıklama**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Belirteç Resolve API aracılığıyla çözümlendikten sonra elde edilen SaaS aboneliğinin benzersiz KIMLIĞI.                              |
| api sürümü         | Bu istek için kullanılacak işlemin sürümü. |
|  |  |

*Üst Bilgiler*

|  **Üst bilgi anahtarı**        | **Gerekli** |  **Açıklama**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-MS-RequestId         |   Hayır         | İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır. |
| x-MS-bağıntıkimliği     |   Hayır         | İstemcideki işlem için benzersiz bir dize değeri. Bu değer, istemci işlemindeki tüm olayları sunucu tarafında bulunan olaylar ile ilişkilendirmek içindir. Bu sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır. |
| IF-Match/If-None-Match |   Hayır         |   Güçlü Doğrulayıcı ETag değeri.                                                          |
| içerik türü           |   Evet        |    `application/json`                                                                   |
|  yetkisi         |   Evet        |    JSON Web belirteci (JWT) taşıyıcı belirteci.                                               |
| x-MS-Market-oturum modu| Hayır | SaaS teklifine abone olurken kuru çalıştırma modunu etkinleştirmek için bayrak. Ayarlanırsa, abonelik ücretlendirilmeyecektir. Bu, ISV test senaryoları için kullanışlıdır. Lütfen **' dryırun '** olarak ayarlayın|
|  |  |  |

*Gövde*

``` json
{
    "lanId": "",
}
```

| **Öğe adı** | **Veri türü** | **Açıklama**                      |
|------------------|---------------|--------------------------------------|
| PlanID           | Istenir Dizisinde        | Kullanıcının abone olduğu SaaS hizmeti için plan KIMLIĞI.  |
|  |  |  |

*Yanıt kodları*

| **HTTP durum kodu** | **Hata kodu**     | **Açıklama**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS aboneliği etkinleştirme, belirli bir plan için alındı.                   |
| 400                  | `BadRequest`         | Gerekli üstbilgiler eksik veya JSON gövdesi hatalı biçimlendirilmiş. |
| 403                  | `Forbidden`          | Çağıranın bu işlemi gerçekleştirme yetkisi yok.                   |
| 404                  | `NotFound`           | Belirtilen KIMLIĞE sahip abonelik bulunamadı                                  |
| 409                  | `Conflict`           | Abonelikte başka bir işlem devam ediyor.                     |
| 429                  | `RequestThrottleId`  | Hizmet istekleri işlemekle meşgul, daha sonra yeniden deneyin.                  |
| 503                  | `ServiceUnavailable` | Hizmet geçici olarak devre dışı, daha sonra yeniden deneyin.                          |
|  |  |  |

202 yanıtı için, ' Operation-Location ' üstbilgisindeki istek işleminin durumunu izleyin. Kimlik doğrulaması, diğer Market API 'Leri ile aynıdır.

*Yanıt üst bilgileri*

| **Üst bilgi anahtarı**     | **Gerekli** | **Açıklama**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Evet          | İstemciden alınan istek KIMLIĞI.                                                                   |
| x-MS-bağıntıkimliği | Evet          | Bağıntı KIMLIĞI istemci tarafından geçirilmemişse, bu değer sunucu bağıntı KIMLIĞIDIR.                   |
| x-MS-ActivityId    | Evet          | Hizmetten gelen isteği izlemek için benzersiz bir dize değeri. Bu değer, herhangi bir mutabakatları için kullanılır. |
| Yeniden deneme-sonrası        | Evet          | İstemcinin durumu kontrol edebilir aralığı.                                                       |
| İşlem-konum | Evet          | İşlem durumunu almak için bir kaynağa bağlayın.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Plan uç noktasını Değiştir

Değişiklik uç noktası, kullanıcının şu anda abone olunan planını yeni bir plana dönüştürmesini sağlar.

**DÜZELTMESI**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{SubscriptionID}* ? api-Version = 2017-04-15**

| **Parametre adı**  | **Açıklama**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS aboneliğinin KIMLIĞI.                              |
| api sürümü         | Bu istek için kullanılacak işlemin sürümü. |
|  |  |

*Üst Bilgiler*

| **Üst bilgi anahtarı**          | **Gerekli** | **Açıklama**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-MS-RequestId          | Hayır           | İstemciden gelen isteği izlemek için benzersiz bir dize değeri. Bir GUID önerilir. Bu sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır.   |
| x-MS-bağıntıkimliği      | Hayır           | İstemcideki işlem için benzersiz bir dize değeri. Bu değer, istemci işlemindeki tüm olayları sunucu tarafında bulunan olaylar ile ilişkilendirmek içindir. Bu sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır. |
| IF-Match/If-None-Match | Hayır           | Güçlü Doğrulayıcı ETag değeri.                              |
| içerik türü            | Evet          | `application/json`                                        |
| yetkisi           | Evet          | JSON Web belirteci (JWT) taşıyıcı belirteci.                    |
|  |  |  |

*Gövde*

```json
{
    "planId": ""
}
```

|  **Öğe adı** |  **Veri türü**  | **Açıklama**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  PlanID           |  Istenir Dizisinde         | Kullanıcının abone olduğu SaaS hizmeti için plan KIMLIĞI.          |
|  |  |  |

*Yanıt kodları*

| **HTTP durum kodu** | **Hata kodu**     | **Açıklama**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS aboneliği etkinleştirme, belirli bir plan için alındı.                   |
| 400                  | `BadRequest`         | Gerekli üstbilgiler eksik veya JSON gövdesi hatalı biçimlendirilmiş. |
| 403                  | `Forbidden`          | Çağıranın bu işlemi gerçekleştirme yetkisi yok.                   |
| 404                  | `NotFound`           | Belirtilen KIMLIĞE sahip abonelik bulunamadı                                  |
| 409                  | `Conflict`           | Abonelikte başka bir işlem devam ediyor.                     |
| 429                  | `RequestThrottleId`  | Hizmet istekleri işlemekle meşgul, daha sonra yeniden deneyin.                  |
| 503                  | `ServiceUnavailable` | Hizmet geçici olarak devre dışı, daha sonra yeniden deneyin.                          |
|  |  |  |

*Yanıt üst bilgileri*

| **Üst bilgi anahtarı**     | **Gerekli** | **Açıklama**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Evet          | İstemciden alınan istek KIMLIĞI.                                                                   |
| x-MS-bağıntıkimliği | Evet          | Bağıntı KIMLIĞI istemci tarafından geçirilmemişse, bu değer sunucu bağıntı KIMLIĞIDIR.                   |
| x-MS-ActivityId    | Evet          | Hizmetten gelen isteği izlemek için benzersiz bir dize değeri. Bu değer, herhangi bir mutabakatları için kullanılır. |
| Yeniden deneme-sonrası        | Evet          | İstemcinin durumu kontrol edebilir aralığı.                                                       |
| İşlem-konum | Evet          | İşlem durumunu almak için bir kaynağa bağlayın.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Aboneliği Sil

Abone uç noktasındaki silme eylemi, kullanıcının belirtilen KIMLIĞE sahip bir aboneliği silmesine izin verir.

*İstek*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{SubscriptionID}* ? api-Version = 2017-04-15**

| **Parametre adı**  | **Açıklama**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS aboneliğinin KIMLIĞI.                              |
| api sürümü         | Bu istek için kullanılacak işlemin sürümü. |
|  |  |

*Üst Bilgiler*

| **Üst bilgi anahtarı**     | **Gerekli** | **Açıklama**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-MS-RequestId     | Hayır           | İstemciden gelen isteği izlemek için benzersiz bir dize değeri. Bir GUID önerilir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır.                                                           |
| x-MS-bağıntıkimliği | Hayır           | İstemcideki işlem için benzersiz bir dize değeri. Bu değer, istemci işlemindeki tüm olayları sunucu tarafında bulunan olaylar ile ilişkilendirmek içindir. Bu sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır. |
| yetkisi      | Evet          | JSON Web belirteci (JWT) taşıyıcı belirteci.                    |
|  |  |  |

*Yanıt kodları*

| **HTTP durum kodu** | **Hata kodu**     | **Açıklama**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS aboneliği etkinleştirme, belirli bir plan için alındı.                   |
| 400                  | `BadRequest`         | Gerekli üstbilgiler eksik veya JSON gövdesi hatalı biçimlendirilmiş. |
| 403                  | `Forbidden`          | Çağıranın bu işlemi gerçekleştirme yetkisi yok.                   |
| 404                  | `NotFound`           | Belirtilen KIMLIĞE sahip abonelik bulunamadı                                  |
| 429                  | `RequestThrottleId`  | Hizmetin istekleri işlemesi meşgul, lütfen daha sonra yeniden deneyin.                  |
| 503                  | `ServiceUnavailable` | Hizmet geçici olarak devre dışı. Lütfen daha sonra yeniden deneyin.                          |
|  |  |  |

202 yanıtı için, ' Operation-Location ' üstbilgisindeki istek işleminin durumunu izleyin. Kimlik doğrulaması, diğer Market API 'Leri ile aynıdır.

*Yanıt üst bilgileri*

| **Üst bilgi anahtarı**     | **Gerekli** | **Açıklama**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Evet          | İstemciden alınan istek KIMLIĞI.                                                                   |
| x-MS-bağıntıkimliği | Evet          | İlişki KIMLIĞI, istemci tarafından geçirilmemişse, bu, sunucu bağıntı KIMLIĞIDIR.                   |
| x-MS-ActivityId    | Evet          | Hizmetten gelen isteği izlemek için benzersiz bir dize değeri. Bu, herhangi bir mutabakatları için kullanılır. |
| Yeniden deneme-sonrası        | Evet          | İstemcinin durumu kontrol edebilir aralığı.                                                       |
| İşlem-konum | Evet          | İşlem durumunu almak için bir kaynağa bağlayın.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Işlem durumunu al

Bu uç nokta, kullanıcının tetiklenen bir zaman uyumsuz işlemin durumunu izlemesine olanak tanır (abonelik/abonelik kaldırma/değişiklik planı).

*İstek*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/ *{operationId}* ? api-Version = 2017-04-15**

| **Parametre adı**  | **Açıklama**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Tetiklenen işlemin benzersiz KIMLIĞI.                |
| api sürümü         | Bu istek için kullanılacak işlemin sürümü. |
|  |  |

*Üst Bilgiler*

| **Üst bilgi anahtarı**     | **Gerekli** | **Açıklama**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Hayır           | İstemciden gelen isteği izlemek için benzersiz bir dize değeri. Bir GUID önerilir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır.   |
| x-MS-bağıntıkimliği | Hayır           | İstemcideki işlem için benzersiz bir dize değeri. Bu değer, istemci işlemindeki tüm olayları sunucu tarafında bulunan olaylar ile ilişkilendirmek içindir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır.  |
| yetkisi      | Evet          | JSON Web belirteci (JWT) taşıyıcı belirteci.                    |
|  |  |  | 

*Yanıt gövdesi*

```json
{
    "id": "",
    "status": "",
    "resourceLocation": "",
    "created": "",
    "lastModified": ""
}
```

| **Parametre adı** | **Veri türü** | **Açıklama**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | Dize        | İşlemin KIMLIĞI.                                                                      |
| status             | Sabit Listesi          | İşlem durumu, aşağıdakilerden biri: `In Progress`, `Succeeded`veya `Failed`.          |
| resourceLocation   | Dize        | Oluşturulan veya değiştirilen aboneliğin bağlantısı. Bu, istemcinin güncelleştirilmiş durum post işlemini almasını sağlar. Bu değer `Unsubscribe` işlemler için ayarlanmadı. |
| yaratıl            | DateTime      | UTC olarak işlem oluşturma süresi.                                                           |
| lastModified       | DateTime      | UTC olarak işlemdeki son güncelleştirme.                                                      |
|  |  |  |

*Yanıt kodları*

| **HTTP durum kodu** | **Hata kodu**     | **Açıklama**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Get isteği başarıyla çözümlendi ve gövde yanıtı içeriyor.    |
| 400                  | `BadRequest`         | Gerekli üstbilgiler eksik ya da geçersiz bir api sürümü belirtildi. |
| 403                  | `Forbidden`          | Çağıranın bu işlemi gerçekleştirme yetkisi yok.                      |
| 404                  | `NotFound`           | Verilen KIMLIĞE sahip abonelik bulunamadı.                                     |
| 429                  | `RequestThrottleId`  | Hizmet istekleri işlemekle meşgul, daha sonra yeniden deneyin.                     |
| 503                  | `ServiceUnavailable` | Hizmet geçici olarak devre dışı, daha sonra yeniden deneyin.                             |
|  |  |  |

*Yanıt üst bilgileri*

| **Üst bilgi anahtarı**     | **Gerekli** | **Açıklama**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Evet          | İstemciden alınan istek KIMLIĞI.                                                                   |
| x-MS-bağıntıkimliği | Evet          | İlişki KIMLIĞI, istemci tarafından geçirilmemişse, bu, sunucu bağıntı KIMLIĞIDIR.                   |
| x-MS-ActivityId    | Evet          | Hizmetten gelen isteği izlemek için benzersiz bir dize değeri. Bu, herhangi bir mutabakatları için kullanılır. |
| Yeniden deneme-sonrası        | Evet          | İstemcinin durumu kontrol edebilir aralığı.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Abonelik al

Abone ol uç noktasındaki alma eylemi, kullanıcının belirli bir kaynak tanımlayıcısına sahip bir abonelik almasına izin verir.

*İstek*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{SubscriptionID}* ? api-Version = 2017-04-15**

| **Parametre adı**  | **Açıklama**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS aboneliğinin KIMLIĞI.                              |
| api sürümü         | Bu istek için kullanılacak işlemin sürümü. |
|  |  |

*Üst Bilgiler*

| **Üst bilgi anahtarı**     | **Gerekli** | **Açıklama**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Hayır           | İstemciden gelen isteği izlemeye yönelik benzersiz bir dize değeri, tercihen bir GUID. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır.                                                           |
| x-MS-bağıntıkimliği | Hayır           | İstemcideki işlem için benzersiz bir dize değeri. Bu değer, istemci işlemindeki tüm olayları sunucu tarafında bulunan olaylar ile ilişkilendirmek içindir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır. |
| yetkisi      | Evet          | JSON Web belirteci (JWT) taşıyıcı belirteci.                                                                    |
|  |  |  |

*Yanıt gövdesi*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Parametre adı**     | **Veri türü** | **Açıklama**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Dize        | Azure 'da SaaS Abonelik kaynağının KIMLIĞI.    |
| OfferId                | Dize        | Kullanıcının abone olduğu teklif KIMLIĞI.         |
| PlanID                 | Dize        | Kullanıcının abone olduğu plan KIMLIĞI.          |
| saasSubscriptionName   | Dize        | SaaS aboneliğinin adı.                |
| saasSubscriptionStatus | Sabit Listesi          | İşlem durumu.  Aşağıdakilerden biri:  <br/> - `Subscribed`: Abonelik etkin.  <br/> - `Pending`: Kullanıcı kaynağı oluşturma, ancak ISV tarafından etkinleştirilmemiş.   <br/> - `Unsubscribed`: kullanıcının aboneliği kaldırılmış.   <br/> - `Suspended`: Kullanıcı aboneliği askıya aldı.   <br/> - `Deactivated`: Azure aboneliği askıya alındı.  |
| yaratıl                | DateTime      | UTC 'de abonelik oluşturma zaman damgası değeri. |
| lastModified           | DateTime      | Abonelik UTC 'de zaman damgası değeri değiştirdi. |
|  |  |  |

*Yanıt kodları*

| **HTTP durum kodu** | **Hata kodu**     | **Açıklama**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Get isteği başarıyla çözümlendi ve gövde yanıtı içeriyor.    |
| 400                  | `BadRequest`         | Gerekli üstbilgiler eksik ya da geçersiz bir api sürümü belirtildi. |
| 403                  | `Forbidden`          | Çağıranın bu işlemi gerçekleştirme yetkisi yok.                      |
| 404                  | `NotFound`           | Belirtilen KIMLIĞE sahip abonelik bulunamadı                                     |
| 429                  | `RequestThrottleId`  | Hizmet istekleri işlemekle meşgul, daha sonra yeniden deneyin.                     |
| 503                  | `ServiceUnavailable` | Hizmet geçici olarak devre dışı, daha sonra yeniden deneyin.                             |
|  |  |  |

*Yanıt üst bilgileri*

| **Üst bilgi anahtarı**     | **Gerekli** | **Açıklama**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Evet          | İstemciden alınan istek KIMLIĞI.                                                                   |
| x-MS-bağıntıkimliği | Evet          | İlişki KIMLIĞI, istemci tarafından geçirilmemişse, bu, sunucu bağıntı KIMLIĞIDIR.                   |
| x-MS-ActivityId    | Evet          | Hizmetten gelen isteği izlemek için benzersiz bir dize değeri. Bu, herhangi bir mutabakatları için kullanılır. |
| Yeniden deneme-sonrası        | Hayır           | İstemcinin durumu kontrol edebilir aralığı.                                                       |
| Özelliği               | Evet          | İşlem durumunu almak için bir kaynağa bağlayın.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Abonelikleri al

Abonelikler üzerinde eylem al uç noktası, bir kullanıcının ISV 'den Tüm tekliflere yönelik tüm abonelikleri almasına izin verir.

*İstek*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Parametre adı**  | **Açıklama**                                       |
|---------------------|-------------------------------------------------------|
| api sürümü         | Bu istek için kullanılacak işlemin sürümü. |
|  |  |

*Üst Bilgiler*

| **Üst bilgi anahtarı**     | **Gerekli** | **Açıklama**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-MS-RequestId     | Hayır           | İstemciden gelen isteği izlemek için benzersiz bir dize değeri. Bir GUID önerilir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır.             |
| x-MS-bağıntıkimliği | Hayır           | İstemcideki işlem için benzersiz bir dize değeri. Bu değer, istemci işlemindeki tüm olayları sunucu tarafında bulunan olaylar ile ilişkilendirmek içindir. Bu değer sağlanmazsa, bir tane oluşturulur ve yanıt üst bilgilerinde sağlanır. |
| yetkisi      | Evet          | JSON Web belirteci (JWT) taşıyıcı belirteci.                    |
|  |  |  |

*Yanıt gövdesi*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Parametre adı**     | **Veri türü** | **Açıklama**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Dize        | Azure 'da SaaS Abonelik kaynağının KIMLIĞI    |
| OfferId                | Dize        | Kullanıcının abone olduğu teklif KIMLIĞI         |
| PlanID                 | Dize        | Kullanıcının abone olduğu plan KIMLIĞI          |
| saasSubscriptionName   | Dize        | SaaS aboneliğinin adı                |
| saasSubscriptionStatus | Sabit Listesi          | İşlem durumu.  Aşağıdakilerden biri:  <br/> - `Subscribed`: Abonelik etkin.  <br/> - `Pending`: Kullanıcı kaynağı oluşturma, ancak ISV tarafından etkinleştirilmemiş.   <br/> - `Unsubscribed`: kullanıcının aboneliği kaldırılmış.   <br/> - `Suspended`: Kullanıcı aboneliği askıya aldı.   <br/> - `Deactivated`: Azure aboneliği askıya alındı.  |
| yaratıl                | DateTime      | UTC 'de abonelik oluşturma zaman damgası değeri |
| lastModified           | DateTime      | UTC 'de abonelik değiştirme zaman damgası değeri |
|  |  |  |

*Yanıt kodları*

| **HTTP durum kodu** | **Hata kodu**     | **Açıklama**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Get isteği başarıyla çözümlendi ve gövde yanıtı içeriyor.    |
| 400                  | `BadRequest`         | Gerekli üstbilgiler eksik ya da geçersiz bir api sürümü belirtildi. |
| 403                  | `Forbidden`          | Çağıranın bu işlemi gerçekleştirme yetkisi yok.                      |
| 404                  | `NotFound`           | Belirtilen KIMLIĞE sahip abonelik bulunamadı                                     |
| 429                  | `RequestThrottleId`  | Hizmetin istekleri işlemesi meşgul, lütfen daha sonra yeniden deneyin.                     |
| 503                  | `ServiceUnavailable` | Hizmet geçici olarak devre dışı. Lütfen daha sonra yeniden deneyin.                             |
|  |  |  |

*Yanıt üst bilgileri*

| **Üst bilgi anahtarı**     | **Gerekli** | **Açıklama**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Evet          | İstemciden alınan istek KIMLIĞI.                                                                   |
| x-MS-bağıntıkimliği | Evet          | İlişki KIMLIĞI, istemci tarafından geçirilmemişse, bu, sunucu bağıntı KIMLIĞIDIR.                   |
| x-MS-ActivityId    | Evet          | Hizmetten gelen isteği izlemek için benzersiz bir dize değeri. Bu, herhangi bir mutabakatları için kullanılır. |
| Yeniden deneme-sonrası        | Hayır           | İstemcinin durumu kontrol edebilir aralığı.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS Web kancası

SaaS Web kancası, SaaS hizmetine yönelik değişikliklere karşı bildirimde bulunmak için kullanılır. Bu POST API 'sinin kimliği doğrulanmamış olması beklenir ve Microsoft hizmeti tarafından çağrılacaktır. SaaS hizmetinin, Web kancası bildiriminde işlem yapmadan önce doğrulamak ve yetkilendirmek için Operations API 'sini çağırması beklenmektedir. 

*Gövde*

``` json
  {
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "action": "Subscribe", // Subscribe/Unsubscribe/ChangePlan
    "operationRequestSource":"Azure",
    "timeStamp":"2018-12-01T00:00:00"
  }
```

| **Parametre adı**     | **Veri türü** | **Açıklama**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | Dize       | Tetiklenen işlemin benzersiz KIMLIĞI.                |
| Etkinlik kimliği   | Dize        | Hizmetten gelen isteği izlemek için benzersiz bir dize değeri. Bu, herhangi bir mutabakatları için kullanılır.               |
| subscriptionId                     | Dize        | Azure 'da SaaS Abonelik kaynağının KIMLIĞI.    |
| OfferId                | Dize        | Kullanıcının abone olduğu teklif KIMLIĞI. Yalnızca "Güncelleştir" eylemiyle birlikte sunulur.        |
| PublisherId                | Dize        | SaaS teklifinin yayımcı KIMLIĞI         |
| PlanID                 | Dize        | Kullanıcının abone olduğu plan KIMLIĞI. Yalnızca "Güncelleştir" eylemiyle birlikte sunulur.          |
| action                 | Dize        | Bu bildirimi tetikleyen eylem. Olası değerler-etkinleştirme, silme, askıya alma, yeniden devreye alma, güncelleştirme          |
| Ilişkin                 | Dize        | Bu bildirimin tetiklendiği zaman zaman damgası değeri UTC.          |
|  |  |  |


## <a name="next-steps"></a>Sonraki adımlar

Geliştiriciler, [bulut iş ortağı PORTALı REST API 'lerini](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)kullanarak iş yüklerini, teklifleri ve yayımcı profillerini de programlamayla alabilir ve düzenleyebilir.
