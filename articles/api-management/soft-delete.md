---
title: Azure API Management geçici silme (Önizleme) | Microsoft Docs
description: Geçici silme, silinen API Management örneklerinin kurtarılmasına olanak tanır.
ms.service: api-management
ms.topic: conceptual
author: vladvino
ms.author: apimpm
ms.date: 11/27/2020
ms.openlocfilehash: e2842f3e428abb4f0eb628dbb8e446f2714d5d89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652394"
---
# <a name="api-management-soft-delete-preview"></a>API Management geçici silme (Önizleme)

API Management geçici silme (Önizleme) ile, son silinen API Management (APıM) örneklerini kurtarabilir ve geri yükleyebilirsiniz.

> [!IMPORTANT]
> Yalnızca `2020-06-01-preview` ve sonrakı API sürümleri kullanılarak silinen API Management örnekleri, bu makalede açıklanan adımlar kullanılarak geçici olarak silinir ve kurtarılabilir. Önceki API sürümleri kullanılarak silinen APıM örnekleri, sabit olarak silinmeye devam edecektir. Azure PowerShell ve Azure CLı Şu anda `2020-06-01-preview` sürümü kullanmıyor ve ayrıca sabit silme davranışına neden olacak.

## <a name="supporting-interfaces"></a>Destekleyici arabirimler

Geçici silme özelliği [REST API](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/restore)aracılığıyla kullanılabilir.

> [!TIP]
> Azure REST API 'Leri çağırmaya yönelik ipuçları ve araçlar için [azure REST API başvurusu](/rest/api/azure/) ' na bakın.

| İşlem | Description | API Management ad alanı | En düşük API sürümü |
|--|--|--|--|
| [Oluştur veya güncelleştir](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) | Bir API Management hizmeti oluşturur veya güncelleştirir.  | API Management hizmeti | Herhangi biri |
| [](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) `restore` Özelliği **true** olarak ayarlanmış şekilde oluştur veya güncelleştir | Daha önce geçici olarak siliniyorsa API Management hizmeti siler. `restore`Belirtilmişse ve `true` diğer tüm özelliklere ayarlanırsa, yok sayılır.  | API Management hizmeti |  2020-06-01-Önizleme |
| [Silme](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/delete) | Mevcut bir API Management hizmetini siler. | API Management hizmeti | 2020-06-01-Önizleme|
| [Ada göre al](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) | Adına göre geçici olarak silinen API Yönetimi hizmetini alın. | Silinen hizmetler | 2020-06-01-Önizleme |
| [Aboneliğe göre Listele](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) | Verilen abonelik için geri alma için kullanılabilen tüm geçici silinen hizmetleri listeler. | Silinen hizmetler | 2020-06-01-Önizleme
| [Meye](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) | API Management hizmeti temizler (silmeyi geri alma seçeneği olmadan siler). | Silinen hizmetler | 2020-06-01-Önizleme

## <a name="soft-delete-behavior"></a>Geçici silme davranışı

API Management örneğinizi oluşturmak için herhangi bir API sürümünü kullanabilirsiniz, ancak `2020-06-01-preview` daha sonra, APıM örneğinizi geçici olarak silmek için veya sonraki sürümleri kullanmanız gerekir (ve bunu kurtarma seçeneğine sahip olursunuz).

API Management bir örneği sildikten sonra, hizmet silinmiş bir durumda olacak ve herhangi bir APIM işlemi için erişilemez hale gelir. Bu durumda, APıM örneği yalnızca listelenebilir, kurtarılabilir veya temizlenmiş (kalıcı olarak silinir).

Aynı zamanda, Azure, önceden belirlenmiş (48 saat) saklama aralığından sonra, APıM örneğine karşılık gelen temel verilerin silinmesini de zamanlamaya çalışır. Örneğe karşılık gelen DNS kaydı, bekletme aralığı süresince de korunur. Saklama süresi geçene kadar geçici olarak silinmiş bir API Management örneğinin adını yeniden kullanamazsınız.

APıM örneğiniz 48 saat içinde kurtarılmıyorsa, bu, (kurtarılamaz) kalıcı olarak silinir. Ayrıca, APıM örneğinizi [Temizleme](#purge-a-soft-deleted-apim-instance) (kalıcı olarak silme) seçeneğini de tercih edebilirsiniz. böylece, geçici silme Bekletme dönemi de vardır.

## <a name="list-deleted-apim-instances"></a>Silinen APıM örneklerini Listele

Silinen Hizmetleri [ada göre Al](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) veya [abonelik işlemlerine göre Listele](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) ' i kullanarak, geçici olarak silinen bir APIM örneğinin geri yükleme (silmeyi geri alma) için kullanılabilir olduğunu doğrulayabilirsiniz.

### <a name="get-a-soft-deleted-instance-by-name"></a>Ada göre geçici olarak silinen bir örnek alın

[](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) `{subscriptionId}` `{location}` `{serviceName}` Azure aboneliğiniz, kaynak konumunuz ve API Management örnek adınızla birlikte API Management adına göre Al işlemini kullanın:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Geri alma için varsa, Azure APıM örneğinin bir kaydını döndürür `deletionDate` `scheduledPurgeDate` , örneğin:

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>Belirli bir abonelik için tüm geçici silinen örnekleri listeleyin

Abonelik KIMLIĞINIZLE yerine API Management [listesini abonelik Işlemine göre](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) kullanın `{subscriptionId}` :

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

Bu işlem, belirli bir abonelik kapsamında, silme için kullanılabilen tüm geçici olarak silinen hizmetlerin bir listesini döndürür `deletionDate` `scheduledPurgeDate` .

## <a name="recover-a-deleted-apim-instance"></a>Silinen bir APıM örneğini kurtarma

[](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) `{subscriptionId}` `{resourceGroup}` `{apimServiceName}` Azure aboneliğiniz, kaynak grubu adı ve API Management adı yerine, ve kullanarak oluşturma veya güncelleştirme işlemi API Management kullanın:

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2020-06-01-preview
```

. . . ve `restore` özelliğini `true` istek gövdesinde olarak ayarlayın. (Bu bayrak belirtildiğinde ve *true* olarak ayarlandığında, diğer tüm özellikler yok sayılır.) Örneğin:

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>Geçici olarak silinen bir APıM örneğini Temizleme

[](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) `{subscriptionId}` `{location}` `{serviceName}` Azure aboneliğiniz, kaynak konumunuz ve API Management adıyla birlikte, ve yerine API Management temizleme işlemini kullanın:

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Bu işlem, Azure 'dan API Management örneğinizi kalıcı olarak siler.

## <a name="next-steps"></a>Sonraki adımlar

Uzun süreli API Management yedekleme ve kurtarma seçenekleri hakkında bilgi edinin:

- [Azure API Management'ta hizmet yedekleme ve geri yükleme işlevlerini kullanarak acil durumda kurtarma](api-management-howto-disaster-recovery-backup-restore.md)