---
title: Markalar modelini özelleştirmek için Azure Video Indexer kullanma
titleSuffix: Azure Media Services
description: Bu makalede, Azure Video Indexer markaların modelini özelleştirmek için nasıl kullanılacağı gösterilmektedir.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 4289c592644d7570ff0dd9ce6aed0cd77f51f25e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838328"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Video Indexer API ile bir markalar modeli özelleştirme

Video Indexer, video ve ses içeriğinin dizin oluşturma ve yeniden dizin oluşturma sırasında konuşma ve görsel metinden marka algılamayı destekler. Marka algılama özelliği, Bing 'ün markalar veritabanı tarafından önerilen ürünlerin, hizmetlerin ve şirketlerin bahsetmelerini belirler. Örneğin, Microsoft bir video veya ses içeriğinde bahsedildiğinde veya videoda görsel metin gösteriyorsa, Video Indexer içeriği bir marka olarak algılar. Özel bir markalar modeli, belirli markaların algılanmadan dışlanmasını ve kuruluşunuzun markalar veritabanında bulunmayabilir, modelinizin bir parçası olması gereken markalar dahil etmenizi sağlar.

Ayrıntılı bir genel bakış için bkz. [genel bakış](customize-brands-model-overview.md).

Bu konuda açıklandığı gibi, videoda algılanan özel markalar modellerini oluşturmak, kullanmak ve düzenlemek için Video Indexer API 'Lerini kullanabilirsiniz. Ayrıca, [video Indexer Web sitesini kullanarak markalar modelini özelleştirme](customize-brands-model-with-api.md)bölümünde açıklandığı gibi video Indexer Web sitesini de kullanabilirsiniz.

## <a name="create-a-brand"></a>Marka oluşturma

Bu, yeni bir özel marka oluşturur ve belirtilen hesap için özel markalar modeline ekler.

### <a name="request-url"></a>İstek URL'si

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Video Indexer geliştirici portalını kullanarak gerekli parametrelere bakın ve test edin](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand).

### <a name="request-parameters"></a>İstek parametreleri

|**Ad**|**Tür**|**Gerekli**|**Açıklama**|
|---|---|---|---|
|location|string|Evet|Çağrının yönlendirileceği Azure bölgesi. Daha fazla bilgi için bkz. [Azure bölgeleri ve video Indexer](regions.md).|
|Accoun|string|Evet|Hesap için genel benzersiz tanımlayıcı|
|accessToken|string|Evet|Çağrıya göre kimlik doğrulaması için erişim belirteci (kapsam [hesabı erişim belirteci](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)olmalıdır). Erişim belirteçlerinin süresi 1 saat içinde doluyor.|

### <a name="request-body"></a>İstek gövdesi

Bu parametrelere ek olarak, aşağıdaki örnekte yer alan yeni marka hakkında bilgi sağlayan bir istek gövdesi JSON nesnesi sağlamanız gerekir.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

True **olarak ayarlandığında** , markası video Indexer için *ekleme* listesine koyar. **Enabled** özelliği false olarak ayarlandığında, markayı *dışlama* listesine koyar, bu nedenle video Indexer algılamamasını sağlar.

**Referenceurl** değeri, bir markasının visela sayfasına yönelik bağlantı gibi herhangi bir başvuru Web sitesi olabilir.

**Etiketler** değeri, markala ilgili etiketlerin bir listesidir. Bu, Video Indexer Web sitesindeki marka 'in *Kategori* alanında görüntülenir. Örneğin, "Azure" markası, "Cloud" olarak etiketlenebilir veya kategorilere ayrılmıştır.

### <a name="response"></a>Yanıt

Yanıt, yeni oluşturduğunuz markada aşağıdaki örnekte yer alan bilgileri sağlar.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Marka silme

Belirtilen hesap için özel markalar modelinden bir marka kaldırır. Hesap **AccountID** parametresinde belirtilir. Başarılı bir şekilde çağrıldıktan sonra, marka artık markalar *ekleme* veya *hariç tutma* listelerinde yer alır.

### <a name="request-url"></a>İstek URL'si

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Video Indexer geliştirici portalını kullanarak gerekli parametrelere bakın ve test edin](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?).

### <a name="request-parameters"></a>İstek parametreleri

|**Ad**|**Tür**|**Gerekli**|**Açıklama**|
|---|---|---|---|
|location|string|Evet|Çağrının yönlendirileceği Azure bölgesi. Daha fazla bilgi için bkz. [Azure bölgeleri ve video Indexer](regions.md).|
|Accoun|string|Evet|Hesap için genel benzersiz tanımlayıcı|
|id|integer|Evet|Marka kimliği (marka oluşturulduğunda oluşturulur)|
|accessToken|string|Evet|Çağrıya göre kimlik doğrulaması için erişim belirteci (kapsam [hesabı erişim belirteci](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)olmalıdır). Erişim belirteçlerinin süresi 1 saat içinde doluyor.|

### <a name="request-body"></a>İstek gövdesi

Bu çağrı için gereken başka bir istek gövdesi yok.

### <a name="response"></a>Yanıt

Marka başarıyla silindiğinde döndürülen içerik yok.

## <a name="get-a-specific-brand"></a>Belirli bir marka al

Bu, marka kimliğini kullanarak belirtilen hesap için özel markalar modelinde bir markaların ayrıntılarını aramanıza olanak tanır.

### <a name="request-url"></a>İstek URL'si

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Video Indexer geliştirici portalını kullanarak gerekli parametrelere bakın ve test edin](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?).

### <a name="request-parameters"></a>İstek parametreleri

|**Ad**|**Tür**|**Gerekli**|**Açıklama**|
|---|---|---|---|
|location|string|Evet|Çağrının yönlendirileceği Azure bölgesi. Daha fazla bilgi için bkz. [Azure bölgeleri ve video Indexer](regions.md).|
|Accoun|string|Evet|Hesap için genel benzersiz tanımlayıcı|
|id|integer|Evet|Marka KIMLIĞI (marka oluşturulduğunda oluşturulur)|
|accessToken|string|Evet|Çağrıya göre kimlik doğrulaması için erişim belirteci (kapsam [hesabı erişim belirteci](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)olmalıdır). Erişim belirteçlerinin süresi 1 saat içinde doluyor.|

### <a name="request-body"></a>İstek gövdesi

Bu çağrı için gereken başka bir istek gövdesi yok.

### <a name="response"></a>Yanıt

Yanıt, aşağıdaki örnekte belirtilen biçimde, aradığınız markada (marka KIMLIĞI kullanarak) ilgili bilgiler sağlar.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> **true** olarak **ayarlanmakta olan** marka, markasının tespit edilecek video Indexer için *dahil* olduğunu ve yanlış olarak **etkinleştirildiğini** , markasının *hariç tutma* listesinde olduğunu belirtir ve video Indexer bunu algılamaz.

## <a name="update-a-specific-brand"></a>Belirli bir marka güncelleştirme

Bu, marka KIMLIĞINI kullanarak belirtilen hesap için özel markalar modelinde bir markaların ayrıntılarını aramanıza olanak tanır.

### <a name="request-url"></a>İstek URL'si

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Video Indexer geliştirici portalını kullanarak gerekli parametrelere bakın ve test edin](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?).

### <a name="request-parameters"></a>İstek parametreleri

|**Ad**|**Tür**|**Gerekli**|**Açıklama**|
|---|---|---|---|
|location|string|Evet|Çağrının yönlendirileceği Azure bölgesi. Daha fazla bilgi için bkz. [Azure bölgeleri ve video Indexer](regions.md).|
|Accoun|string|Evet|Hesap için genel benzersiz tanımlayıcı|
|id|integer|Evet|Marka KIMLIĞI (marka oluşturulduğunda oluşturulur)|
|accessToken|string|Evet|Çağrıya göre kimlik doğrulaması için erişim belirteci (kapsam [hesabı erişim belirteci](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)olmalıdır). Erişim belirteçlerinin süresi 1 saat içinde doluyor.|

### <a name="request-body"></a>İstek gövdesi

Bu parametrelere ek olarak, aşağıdaki örnek biçimini izleyerek güncelleştirmek istediğiniz marka üzerinde güncelleştirilmiş bilgiler sağlayan bir istek gövdesi JSON nesnesi sağlamanız gerekir.

```json
{
  "name": "Example",
  "enabled": false,
  "tags": ["Tag1", "NewTag2"],
  "description": "This is an update example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-28T15:52:22.3413983",
}
```

> [!NOTE]
> Bu örnekte, **marka oluştur** bölümünde örnek istek gövdesinde oluşturulan marka, burada yeni bir etiket ve yeni açıklama ile güncelleştiriliyor. **Etkin** değer aynı zamanda, *hariç tutma* listesine koymak için false olarak değiştirilmiştir.

### <a name="response"></a>Yanıt

Yanıt, aşağıdaki örnek biçimini izleyerek güncelleştirdiğiniz marka hakkındaki güncelleştirilmiş bilgileri sağlar.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Tüm markalar alın

Bu, markaların *dahil etme* veya *hariç tutma* listesinde olup olmamasından bağımsız olarak, belirtilen hesap için özel markalar modelindeki tüm markalar döndürür.

### <a name="request-url"></a>İstek URL'si

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Video Indexer geliştirici portalını kullanarak gerekli parametrelere bakın ve test edin](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?).

### <a name="request-parameters"></a>İstek parametreleri

|**Ad**|**Tür**|**Gerekli**|**Açıklama**|
|---|---|---|---|
|location|string|Evet|Çağrının yönlendirileceği Azure bölgesi. Daha fazla bilgi için bkz. [Azure bölgeleri ve video Indexer](regions.md).|
|Accoun|string|Evet|Hesap için genel benzersiz tanımlayıcı|
|accessToken|string|Evet|Çağrıya göre kimlik doğrulaması için erişim belirteci (kapsam [hesabı erişim belirteci](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)olmalıdır). Erişim belirteçlerinin süresi 1 saat içinde doluyor.|

### <a name="request-body"></a>İstek gövdesi

Bu çağrı için gereken başka bir istek gövdesi yok.

### <a name="response"></a>Yanıt

Yanıt, hesabınızdaki tüm markaların bir listesini ve aşağıdaki örnekte belirtilen biçimdeki ayrıntıları sağlar.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> Marka adlı *örnek* , algılamak Için video Indexer *Içerme* listesinde ve *example2* adlı marka, *hariç tutma* listesinde yer alır, bu nedenle video Indexer bunu algılamaz.

## <a name="get-brands-model-settings"></a>Markalar model ayarlarını al

Bu, belirtilen hesaptaki markalar model ayarlarını döndürür. Markalar model ayarları, Bing markalar veritabanı 'nın etkin olup olmadığını temsil eder. Bing markalar etkinleştirilmemişse, Video Indexer yalnızca belirtilen hesabın özel markalar modelinden markalar algılar.

### <a name="request-url"></a>İstek URL'si

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Video Indexer geliştirici portalını kullanarak gerekli parametrelere bakın ve test edin](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands).

### <a name="request-parameters"></a>İstek parametreleri

|**Ad**|**Tür**|**Gerekli**|**Açıklama**|
|---|---|---|---|
|location|string|Evet|Çağrının yönlendirileceği Azure bölgesi. Daha fazla bilgi için bkz. [Azure bölgeleri ve video Indexer](regions.md).|
|Accoun|string|Evet|Hesap için genel benzersiz tanımlayıcı|
|accessToken|string|Evet|Çağrıya göre kimlik doğrulaması için erişim belirteci (kapsam [hesabı erişim belirteci](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)olmalıdır). Erişim belirteçlerinin süresi 1 saat içinde doluyor.|

### <a name="request-body"></a>İstek gövdesi

Bu çağrı için gereken başka bir istek gövdesi yok.

### <a name="response"></a>Yanıt

Yanıt, Bing markaların aşağıdaki örnekte belirtilen biçimde etkinleştirilip etkinleştirilmediğini gösterir.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> doğru olarak ayarlanan **Usebuiltin** , Bing markaların etkinleştirildiğini temsil eder. *Usebuiltin* false Ise, Bing markalar devre dışı bırakılır. **Durum** değeri kullanım dışı olduğu için yok sayılabilir.

## <a name="update-brands-model-settings"></a>Markalar model ayarlarını Güncelleştir

Bu, belirtilen hesaptaki markalar modeli ayarlarını güncelleştirir. Markalar model ayarları, Bing markalar veritabanı 'nın etkin olup olmadığını temsil eder. Bing markalar etkinleştirilmemişse, Video Indexer yalnızca belirtilen hesabın özel markalar modelinden markalar algılar.

### <a name="request-url"></a>İstek URL 'SI:
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```

[Video Indexer geliştirici portalını kullanarak gerekli parametrelere bakın ve test edin](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?).

### <a name="request-parameters"></a>İstek parametreleri

|**Ad**|**Tür**|**Gerekli**|**Açıklama**|
|---|---|---|---|
|location|string|Evet|Çağrının yönlendirileceği Azure bölgesi. Daha fazla bilgi için bkz. [Azure bölgeleri ve video Indexer](regions.md).|
|Accoun|string|Evet|Hesap için genel benzersiz tanımlayıcı|
|accessToken|string|Evet|Çağrıya göre kimlik doğrulaması için erişim belirteci (kapsam [hesabı erişim belirteci](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)olmalıdır). Erişim belirteçlerinin süresi 1 saat içinde doluyor.|

### <a name="request-body"></a>İstek gövdesi

Bu parametrelere ek olarak, aşağıdaki örnekte yer alan yeni marka hakkında bilgi sağlayan bir istek gövdesi JSON nesnesi sağlamanız gerekir.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> doğru olarak ayarlanan **Usebuiltin** , Bing markaların etkinleştirildiğini temsil eder. *Usebuiltin* false Ise, Bing markalar devre dışı bırakılır.

### <a name="response"></a>Yanıt

Markalar model ayarı başarıyla güncelleştirildiği zaman döndürülen içerik yok.

## <a name="next-steps"></a>Sonraki adımlar

[Web sitesi kullanarak markalar modelini özelleştirme](customize-brands-model-with-website.md)
