---
title: Varlıkların filtrelenmesi, sıralanması ve sayfalama
description: Azure Media Services v3 varlıklarının filtrelenmesini, sıralanmasını ve sayfalamayı öğrenin.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: overview
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: c2748bb879c016ce7d13e73602e9bc0604738dff
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121192"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Media Services varlıkların filtrelenmesi, sıralanması ve sayfalama

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu konu, Azure Media Services v3 varlıklarını listelerken kullanılabilen OData sorgu seçeneklerini ve sayfalandırma desteğini ele alır.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

* Türündeki varlıkların özellikleri `Datetime` her zaman UTC biçimindedir.
* İstek gönderilmeden önce sorgu dizesindeki boşluk, URL kodlamalı olmalıdır.

## <a name="comparison-operators"></a>Karşılaştırma işleçleri

Bir alanı sabit değerle karşılaştırmak için aşağıdaki işleçleri kullanabilirsiniz:

Eşitlik işleçleri:

- `eq`: Bir alanın sabit değere *eşit* olup olmadığını test edin.
- `ne`: Bir alanın sabit değere *eşit* olup olmadığını test edin.

Aralık işleçleri:

- `gt`: Bir alanın sabit değerden *büyük* olup olmadığını test edin.
- `lt`: Bir alanın sabit değerden *küçük* olup olmadığını test edin.
- `ge`: Bir alanın sabit değere *eşit veya ondan büyük* olup olmadığını test edin.
- `le`: Bir alanın sabit değere *eşit veya ondan küçük* olup olmadığını test edin.

## <a name="filter"></a>Filtre

`$filter`Yalnızca ilgilendiğiniz nesneleri bulmak için bir OData filtre parametresi sağlamak üzere kullanın.

Aşağıdaki REST örneği `alternateId` bir varlığın değerine göre filtreliyor:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

Aşağıdaki C# örneği varlığın oluşturulma tarihinde filtreliyor:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Sıralama ölçütü

`$orderby`Döndürülen nesneleri belirtilen parametreye göre sıralamak için kullanın. Örnek:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Sonuçları artan veya azalan düzende sıralamak için, `asc` `desc` alan adına veya alana ayırarak bir boşluk ekleyin. Örneğin: `$orderby properties/created desc`.

## <a name="skip-token"></a>Belirteci atla

Bir sorgu yanıtı çok sayıda öğe içeriyorsa, hizmet bir `$skiptoken` `@odata.nextLink` sonraki sonuç sayfasını almak için kullandığınız bir () değeri döndürür. Tüm sonuç kümesi üzerinden sayfa eklemek için kullanın.

V3 Media Services, sayfa boyutunu yapılandıramazsınız. Sayfa boyutu varlık türüne göre değişir. Ayrıntılar için aşağıdaki ayrı bölümleri okuyun.

Koleksiyon üzerinde sayfalama yaparken varlıklar oluşturulur veya silinirse, değişiklikler döndürülen sonuçlara yansıtılır (Bu değişiklikler henüz indirilmemiş koleksiyonun parçasıysa).

> [!TIP]
> `nextLink`Koleksiyonu numaralandırmak için her zaman kullanın ve belirli bir sayfa boyutuna bağlı değildir.
>
> `nextLink`Değer yalnızca birden fazla varlık sayfası varsa mevcut olacaktır.

Öğesinin kullanıldığı aşağıdaki örneği göz önünde bulundurun `$skiptoken` . *Amstestaccount* değerini hesap adınızla değiştirdiğinizden ve *api sürümü* değerini en son sürüme ayarladığınızdan emin olun.

Aşağıdaki gibi varlıkların bir listesini istemeniz durumunda:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Aşağıdakine benzer bir yanıt geri alacaksınız:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Ardından, için bir get isteği göndererek sonraki sayfayı isteyebilirsiniz:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Aşağıdaki C# örneği, hesaptaki tüm akış konum belirleyicilerinin nasıl numaralandırılacağını gösterir.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Sorgu seçeneklerini birleştirmek için mantıksal işleçler kullanma

Media Services **v3** , **ve ve mantıksal işleçleri destekler.** 

Aşağıdaki REST örneği işin durumunu denetler:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

C# ' de aynı sorguyu şöyle oluşturursunuz: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Varlıkların filtreleme ve sıralama seçenekleri

Aşağıdaki tabloda, filtreleme ve sıralama seçeneklerinin farklı varlıklara nasıl uygulanacağı gösterilmektedir:

|Varlık adı|Özellik adı|Filtre|Sipariş|
|---|---|---|---|
|[Varlıklar](/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` ve `desc`|
||Properties. AlternateId |`eq`||
||Properties. assetId |`eq`||
||Özellikler. oluşturuldu| `eq`, `gt`, `lt`| `asc` ve `desc`|
|[İçerik anahtarı ilkeleri](/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` ve `desc`|
||Özellikler. oluşturuldu    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` ve `desc`|
||Properties. Description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||Properties. lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` ve `desc`|
||Properties. PolicyId|`eq`, `ne`||
|[İşler](/rest/api/media/jobs)| name  | `eq`            | `asc` ve `desc`|
||Properties. State        | `eq`, `ne`        |                         |
||Özellikler. oluşturuldu      | `gt`, `ge`, `lt`, `le`| `asc` ve `desc`|
||Properties. lastModified | `gt`, `ge`, `lt`, `le` | `asc` ve `desc`| 
|[Akış bulucuları](/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` ve `desc`|
||Özellikler. oluşturuldu    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` ve `desc`|
||Properties. BitişZamanı    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` ve `desc`|
|[Akış ilkeleri](/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` ve `desc`|
||Özellikler. oluşturuldu    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` ve `desc`|
|[Dönüştürmeler](/rest/api/media/transforms)| name | `eq`            | `asc` ve `desc`|
|| Özellikler. oluşturuldu      | `gt`, `ge`, `lt`, `le`| `asc` ve `desc`|
|| Properties. lastModified | `gt`, `ge`, `lt`, `le`| `asc` ve `desc`|

## <a name="next-steps"></a>Sonraki adımlar

* [Varlıkları Listele](/rest/api/media/assets/list)
* [Içerik anahtarı Ilkelerini Listele](/rest/api/media/contentkeypolicies/list)
* [Işleri listeleme](/rest/api/media/jobs/list)
* [Akış Ilkelerini Listele](/rest/api/media/streamingpolicies/list)
* [Akış Konumlandırıcı listesini Listele](/rest/api/media/streaminglocators/list)
* [Bir dosyayı akışa alma](stream-files-dotnet-quickstart.md)
* [Kotalar ve sınırlar](limits-quotas-constraints-reference.md)
