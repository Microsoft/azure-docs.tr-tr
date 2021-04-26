---
title: Zaman uyumsuz işlemlerin durumu
description: Azure 'da zaman uyumsuz işlemlerin nasıl izleneceğini açıklar. Uzun süre çalışan bir işlemin durumunu almak için kullandığınız değerleri gösterir.
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: f5823bc8f6c0d5b334a638d4ad350eb2f9ef2b28
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932752"
---
# <a name="track-asynchronous-azure-operations"></a>Zaman uyumsuz Azure işlemlerini izleme

İşlem hızlı bir şekilde tamamlanamadığından bazı Azure REST işlemleri zaman uyumsuz olarak çalışır. Bu makalede, yanıtta döndürülen değerler aracılığıyla zaman uyumsuz işlemlerin durumunun nasıl izleneceği açıklanır.  

## <a name="status-codes-for-asynchronous-operations"></a>Zaman uyumsuz işlemler için durum kodları

Zaman uyumsuz bir işlem başlangıçta bir HTTP durum kodu döndürür:

* 201 (oluşturuldu)
* 202 (kabul edildi)

İşlem başarıyla tamamlandığında, aşağıdakilerden birini döndürür:

* 200 (TAMAM)
* 204 (Içerik yok)

Yürütmekte olduğunuz işlemin yanıtlarını görmek için [REST API belgelerine](/rest/api/azure/) bakın.

201 veya 202 yanıt kodunu aldıktan sonra, işlemin durumunu izlemeye hazırsınız demektir.

## <a name="url-to-monitor-status"></a>Durumu izlemek için URL

Zaman uyumsuz işlemin durumunu izlemenin iki farklı yolu vardır. Özgün talebinizden döndürülen üst bilgi değerlerini inceleyerek doğru yaklaşımı belirlersiniz. İlk olarak şunu arayın:

* `Azure-AsyncOperation` -İşlemin devam eden durumunu denetlemek için URL. İşlem bu değeri döndürürse, işlemin durumunu izlemek için onu kullanın.
* `Retry-After` -Zaman uyumsuz işlemin durumu denetlenmeden önce beklenecek saniye sayısı.

`Azure-AsyncOperation`Üst bilgi değerlerinden biri değilse, şunu arayın:

* `Location` -Bir işlemin ne zaman tamamlandığını belirlemek için URL. Yalnızca Azure-AsyncOperation döndürülmediğinde bu değeri kullanın.
* `Retry-After` -Zaman uyumsuz işlemin durumu denetlenmeden önce beklenecek saniye sayısı.

## <a name="azure-asyncoperation-request-and-response"></a>İstek ve yanıt Azure-AsyncOperation

`Azure-AsyncOperation`Üst bilgi değerindeki BIR URL varsa, bu URL 'ye BIR get isteği gönderin. `Retry-After`Durumu ne sıklıkta kontrol etmek için değerini kullanın. İşlemin durumunu gösteren bir yanıt nesnesi alacaksınız. URL ile işlemin durumu denetlenirken farklı bir yanıt döndürülür `Location` . Konum URL 'sindeki yanıt hakkında daha fazla bilgi için bkz. [depolama hesabı oluşturma (konum ile 202 ve yeniden deneme-sonrası)](#create-storage-account-202-with-location-and-retry-after).

Yanıt özellikleri farklılık gösterebilir, ancak her zaman zaman uyumsuz işlemin durumunu içerir.

```json
{
    "status": "{status-value}"
}
```

Aşağıdaki örnek, işlemden döndürülebilecek diğer değerleri gösterir:

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

Hata nesnesi, durum başarısız olduğunda veya Iptal edildiğinde döndürülür. Diğer tüm değerler isteğe bağlıdır. Aldığınız yanıt, örneğinden farklı görünebilir.

## <a name="provisioningstate-values"></a>provisioningState değerleri

Kaynak oluşturan, güncelleştiren veya silen (PUT, PATCH, SIL) işlemler genellikle bir `provisioningState` değer döndürüyor. Bir işlem tamamlandığında, aşağıdaki üç değerden biri döndürülür:

* Başarılı
* Başarısız
* İptal edildi

Diğer tüm değerler işlemin hala çalıştığını gösterir. Kaynak sağlayıcı, durumunu gösteren özelleştirilmiş bir değer döndürebilir. Örneğin, istek alındığında ve çalıştırıldığında **kabul** edilebilir.

## <a name="example-requests-and-responses"></a>Örnek istekleri ve yanıtları

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Sanal makineyi başlatın (Azure-AsyncOperation ile 202)

Bu örnek, [sanal makineler için başlatma işleminin](/rest/api/compute/virtualmachines/start)durumunun nasıl belirleneceğini gösterir. İlk istek aşağıdaki biçimdedir:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2019-12-01
```

202 durum kodunu döndürür. Üst bilgi değerleri arasında şunu görürsünüz:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
```

Bu URL 'ye başka bir istek göndererek zaman uyumsuz işlemin durumunu denetlemek için.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
```

Yanıt gövdesi işlemin durumunu içerir:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Kaynakları dağıtma (Azure ile zaman uyumsuz coperation ile 201)

Bu örnekte, Azure 'a [kaynak dağıtmaya yönelik dağıtımlar işleminin](/rest/api/resources/resources/deployments/createorupdate) durumunun nasıl belirleneceği gösterilmektedir. İlk istek aşağıdaki biçimdedir:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2020-06-01
```

201 durum kodunu döndürür. Yanıtın gövdesi şunları içerir:

```json
"provisioningState":"Accepted",
```

Üst bilgi değerleri arasında şunu görürsünüz:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

Bu URL 'ye başka bir istek göndererek zaman uyumsuz işlemin durumunu denetlemek için.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

Yanıt gövdesi işlemin durumunu içerir:

```json
{
    "status": "Running"
}
```

Dağıtım tamamlandığında, yanıt şunları içerir:

```json
{
    "status": "Succeeded"
}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Depolama hesabı oluştur (konum ve yeniden dene ile 202)

Bu örnek, [depolama hesapları için oluşturma işleminin](/rest/api/storagerp/storageaccounts/create)durumunun nasıl belirleneceğini gösterir. İlk istek aşağıdaki biçimdedir:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2019-06-01
```

Ve istek gövdesi depolama hesabı için özellikler içerir:

```json
{
    "location": "South Central US",
    "properties": {},
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

202 durum kodunu döndürür. Üst bilgi değerleri arasında aşağıdaki iki değeri görürsünüz:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
Retry-After: 17
```

Yeniden deneme sırasında belirtilen saniye sayısını bekledikten sonra, bu URL 'ye başka bir istek göndererek zaman uyumsuz işlemin durumunu denetleyin.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
```

İstek hala çalışıyorsa 202 bir durum kodu alırsınız. İstek tamamlandıysa, 200 durum kodunu alır ve yanıtın gövdesi oluşturulan depolama hesabının özelliklerini içerir.

## <a name="next-steps"></a>Sonraki adımlar

* Her REST işlemi hakkındaki belgeler için [REST API belgelerine](/rest/api/azure/)bakın.
* Kaynak Yöneticisi REST API üzerinden şablon dağıtma hakkında daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonlarıyla kaynakları dağıtma ve Kaynak Yöneticisi REST API](../templates/deploy-rest.md).