---
title: Zaman uyumsuz işlemlerin durumu
description: Azure'da zaman uyumsuz işlemleri izleme açıklar. Bu, uzun süre çalışan işlemin durumunu almak için kullandığınız değerleri gösterir.
ms.topic: conceptual
ms.date: 12/09/2018
ms.custom: seodec18
ms.openlocfilehash: 39174010e5d5c71e1b9f09827f92a2c540d98e78
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149544"
---
# <a name="track-asynchronous-azure-operations"></a>Azure zaman uyumsuz işlemleri izleme
Bazı Azure REST işlemlerini zaman uyumsuz olarak çalışır, çünkü işlem hızlı bir şekilde tamamlanamıyor. Bu makalede, yanıtta döndürülen değerleri arasında zaman uyumsuz işlemlerin durumunu izlemek açıklar.  

## <a name="status-codes-for-asynchronous-operations"></a>Zaman uyumsuz işlemler için durum kodları
Zaman uyumsuz bir işlem başlangıçta herhangi birinin bir HTTP durum kodu döndürür:

* 201 (oluşturuldu)
* 202 (kabul edildi) 

İşlem başarıyla tamamlandığında, ya da döndürür:

* 200 (TAMAM)
* 204 (içerik yok) 

Başvurmak [REST API belgelerini](/rest/api/) yürütme işlemi için yanıtlar görmek için.

## <a name="monitor-status-of-operation"></a>İşlemin durumu İzleyicisi
REST işlemlerini zaman uyumsuz işlemin durumunu belirlemek için kullanılan üstbilgi değerlerini döndürür. İncelemek için büyük olasılıkla üç üstbilgi değerleri vardır:

* `Azure-AsyncOperation` -Devam eden işlemin durumunu denetleme URL. (İşlemi, bu değer döndürürse, her zaman bu konumu yerine) işlemin durumunu izlemek için kullanın.
* `Location` -Bir işlem tamamlandığında belirlemek için URL. Azure-AsyncOperation yalnızca döndürülmezse, bu değeri kullanın.
* `Retry-After` -Zaman uyumsuz işlemin durumu denetlemeden önce beklenecek saniye sayısı.

Ancak, bu değerlerin her zaman uyumsuz işlem döndürür. Örneğin, bir işlem için Azure-AsyncOperation üstbilgi değeri ve başka bir işlem için konum üst bilgisi değeri değerlendirmek gerekebilir. 

Bir istek üst bilgisi değeri alması gibi üstbilgi değerlerini alın. Örneğin, C# ' ta, üstbilgi değerini almak bir `HttpWebResponse` adlı nesne `response` aşağıdaki kod ile:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation istek ve yanıt

Zaman uyumsuz işlemin durumunu almak için Azure-AsyncOperation üstbilgi değeri URL'SİNDE bir GET isteği gönderin.

Bu işlem yanıt gövdesi işlemiyle ilgili bilgi içerir. Aşağıdaki örnek, işleminden döndürülen değerlerden gösterir:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

Yalnızca `status` tüm yanıtları döndürülür. Durum başarısız oldu veya iptal edildi ise hata nesnesi döndürülür. Diğer tüm değerler isteğe bağlıdır; Bu nedenle, aldığınız yanıt örneği farklı görünebilir.

## <a name="provisioningstate-values"></a>provisioningState değerleri

Bir kaynak oluşturmak, güncelleştirmek veya silmek (PUT, PATCH, DELETE) işlemlerini döndürür, genellikle bir `provisioningState` değeri. Bir işlem tamamlandığında, aşağıdaki üç değerden birini döndürülür: 

* Başarılı oldu
* Başarısız
* İptal edildi

Diğer tüm değerler, işlem hala çalışıyor gösterir. Kaynak sağlayıcısı durumunu gösteren özelleştirilmiş bir değer döndürebilir. Örneğin, alabileceğiniz **kabul edilen** alınan ve çalışan istek olduğunda.

## <a name="example-requests-and-responses"></a>Örnek istekleri ve yanıtları

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>(Azure-AsyncOperation 202) sanal makineyi Başlat
Bu örnekte, durumu belirlemek gösterilmektedir **Başlat** sanal makineler için işlem. İlk istek aşağıdaki biçimdedir:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Durum kodu: 202 döndürür. Üstbilgi değerleri arasında görürsünüz:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Bu URL için başka bir isteği gönderilirken zaman uyumsuz işlemin durumunu denetlemek için.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Yanıt gövdesi, işlemin durumunu içerir:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Kaynakları (Azure-AsyncOperation ile 201) dağıtma

Bu örnekte, durumu belirlemek gösterilmektedir **dağıtımları** kaynakları Azure'a dağıtma işlemi. İlk istek aşağıdaki biçimdedir:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

201 durum kodunu döndürür. Yanıt gövdesinin içerir:

```json
"provisioningState":"Accepted",
```

Üstbilgi değerleri arasında görürsünüz:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Bu URL için başka bir isteği gönderilirken zaman uyumsuz işlemin durumunu denetlemek için.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Yanıt gövdesi, işlemin durumunu içerir:

```json
{"status":"Running"}
```

Dağıtım tamamlandığında yanıt içerir:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Depolama hesabı (konum ve Retry-After 202) oluşturma

Bu örnekte, durumu belirlemek gösterilmektedir **oluşturma** işlemi depolama hesapları için. İlk istek aşağıdaki biçimdedir:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

Ve istek gövdesi depolama hesabına yönelik özellikler içerir:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Durum kodu: 202 döndürür. Üstbilgi değerleri arasında aşağıdaki iki değer görürsünüz:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Bekleyen sayısı için belirtilen yeniden deneme sonrasında onay zaman uyumsuz işlemin durumu bu URL için başka bir istek göndererek saniye sonra.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

İstek hala çalışıyorsa, durum kodu 202 alırsınız. İstek tamamlandıysa, durum kodu 200 alabilir ve yanıt gövdesinin oluşturulmuş depolama hesabının özelliklerini içerir.

## <a name="next-steps"></a>Sonraki adımlar

* Her REST işlemi hakkında daha fazla bilgi için bkz [REST API belgelerini](/rest/api/).
* Resource Manager REST API aracılığıyla şablonları dağıtma hakkında daha fazla bilgi için bkz: [kaynakları Resource Manager şablonları ve Resource Manager REST API'si ile dağıtma](resource-group-template-deploy-rest.md).