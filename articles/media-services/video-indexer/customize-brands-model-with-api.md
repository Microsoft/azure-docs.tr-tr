---
title: Video Indexer API ile bir markalar modeli özelleştirme
titleSuffix: Azure Media Services
description: Video Indexer API ile bir markalar modelini özelleştirmeyi öğrenin.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 03c74e9108b27856259eb4f17d7ba521007c4fa3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312969"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Video Indexer API ile bir markalar modeli özelleştirme

Video Indexer, video ve ses içeriğinin dizin oluşturma ve yeniden dizin oluşturma sırasında konuşma ve görsel metinden marka algılamayı destekler. Marka algılama özelliği, Bing 'ün markalar veritabanı tarafından önerilen ürünlerin, hizmetlerin ve şirketlerin bahsetmelerini belirler. Örneğin, Microsoft video veya ses içeriğiyle bahsedildiğinde veya videoda görsel metin gösteriyorsa, Video Indexer içeriği bir marka olarak algılar. Özel bir markalar modeli, belirli markaların algılanmadan dışlanmasını ve kuruluşunuzun markalar veritabanında bulunmayabilir, modelinizin bir parçası olması gereken markalar dahil etmenizi sağlar. Daha fazla bilgi için bkz. [Genel Bakış](customize-brands-model-overview.md).

> [!NOTE]
> Video, marka eklemeden önce dizinlendiğinden, onu yeniden eklemeniz gerekir.

Bu konuda açıklandığı gibi, videoda algılanan özel markalar modellerini oluşturmak, kullanmak ve düzenlemek için Video Indexer API 'Lerini kullanabilirsiniz. Ayrıca, [video Indexer Web sitesini kullanarak markalar modelini özelleştirme](customize-brands-model-with-api.md)bölümünde açıklandığı gibi video Indexer Web sitesini de kullanabilirsiniz.

## <a name="create-a-brand"></a>Marka oluşturma

Marka API 'si [oluşturma](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Brand) yeni bir özel marka oluşturur ve belirtilen hesap Için özel markalar modeline ekler.

> [!NOTE]
> `enabled`(Gövdesinde) ayarı true olarak belirlemek için marka listesini video Indexer Için *ekleme* listesine koyar. `enabled`False olarak ayarlamak, markayı *dışlama* listesine koyar, bu nedenle video Indexer algılamıyor.

Gövdede ayarlayabileceğiniz bazı diğer parametreler:

* `referenceUrl`Bu değer, vican sayfasının bağlantısı gibi, marka için herhangi bir başvuru Web sitesi olabilir.
* `tags`Değer, markala ilgili etiketlerin bir listesidir. Bu etiket, Video Indexer Web sitesindeki marka 'in *Kategori* alanında görüntülenir. Örneğin, "Azure" markası, "Cloud" olarak etiketlenebilir veya kategorilere ayrılmıştır.

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

Marka API 'si [silme](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Brand) , belirtilen hesap Için özel markalar modelinden bir marka kaldırır. Hesap, `accountId` parametresinde belirtilir. Başarılı bir şekilde çağrıldıktan sonra, marka artık markalar *ekleme* veya *hariç tutma* listelerinde yer alır.

### <a name="response"></a>Yanıt

Marka başarıyla silindiğinde döndürülen içerik yok.

## <a name="get-a-specific-brand"></a>Belirli bir marka al

[Marka al](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Brand) API 'si, marka kimliğini kullanarak belirtilen hesap Için özel markalar modelinde bir markaların ayrıntılarını aramanıza olanak tanır.

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
> `enabled``true`markasının algılaması video Indexer Için *dahil etme* listesinde olduğunu ve `enabled` yanlış olması Için, markasının *hariç tutma* listesinde olduğunu, bu yüzden video Indexer algılayamayacağı anlamına gelir.

## <a name="update-a-specific-brand"></a>Belirli bir marka güncelleştirme

Marka API 'si [güncelleştirme](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Brand) , marka kimliğini kullanarak belirtilen hesap Için özel markalar modelinde bir markaların ayrıntılarını aramanıza olanak tanır.

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

[Tüm markalar al](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Brands) API 'si, markaların *dahil etme* veya *hariç tutma* listesinde olup olmamasından bağımsız olarak, belirtilen hesap için özel markalar modelindeki tüm markalarını döndürür.

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
> Marka adlı *örnek* , algılamak Için video Indexer *Içerme* listesinde ve *example2* adlı marka, *hariç tutma* listesinde yer alır, bu nedenle video Indexer algılamamaktadır.

## <a name="get-brands-model-settings"></a>Markalar model ayarlarını al

[Marka ayarları al](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Brands) API 'si, belirtilen hesaptaki markalar model ayarlarını döndürür. Markalar model ayarları, Bing markalar veritabanı 'nın etkin olup olmadığını temsil eder. Bing markalar etkinleştirilmemişse, Video Indexer yalnızca belirtilen hesabın özel markalar modelinden markalar algılar.

### <a name="response"></a>Yanıt

Yanıt, Bing markaların aşağıdaki örnekte belirtilen biçimde etkinleştirilip etkinleştirilmediğini gösterir.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn` true olarak ayarlanmakta, Bing markaların etkinleştirildiğini temsil edilir. `useBuiltin`Yanlışsa, Bing markalar devre dışıdır. `state`Değer kullanım dışı bırakıldığı için yok sayılabilir.

## <a name="update-brands-model-settings"></a>Markalar model ayarlarını Güncelleştir

[Güncelleştirme markalar](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Brands-Model-Settings) API 'si, belirtilen hesaptaki markalar modeli ayarlarını güncelleştirir. Markalar model ayarları, Bing markalar veritabanı 'nın etkin olup olmadığını temsil eder. Bing markalar etkinleştirilmemişse, Video Indexer yalnızca belirtilen hesabın özel markalar modelinden markalar algılar.

`useBuiltIn`True olarak ayarlanan bayrak, Bing markalarının etkinleştirildiği anlamına gelir. `useBuiltin`Yanlışsa, Bing markalar devre dışıdır.

### <a name="response"></a>Yanıt

Markalar model ayarı başarıyla güncelleştirildiği sırada döndürülen içerik yok.

## <a name="next-steps"></a>Sonraki adımlar

[Web sitesi kullanarak markalar modelini özelleştirme](customize-brands-model-with-website.md)
