---
title: REST API ile Azure hizmeti kaynak kullanımını gözden geçirme
description: Azure hizmeti kaynak kullanımını gözden geçirmek için Azure REST API’lerinin nasıl kullanılacağını öğrenin. Maliyet yönetimi raporu oluşturun ve belirli kaynak türleri için filtreleyin.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: ed1d360b5e816627bf161f479addcd6e0eb776a1
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058859"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>REST API kullanarak Azure kaynak kullanımını gözden geçirme

Azure Maliyet Yönetimi API’leri, Azure kaynaklarınızın tüketimini gözden geçirip yönetmenize yardımcı olur.

Bu makalede, saatlik kullanım bilgilerinizi içeren virgülle ayrılmış değer belgesi oluşturacak bir günlük raporun nasıl oluşturulacağını ve Azure kaynak grubundaki sanal makinelerin, veritabanlarının ve etiketlenmiş kaynakların kullanımını sorgulayabilmeniz için raporu özelleştirmek amacıyla filtrelerin nasıl kullanılacağını öğreneceksiniz.

>[!NOTE]
> Maliyet Yönetimi API’si şu anda özel önizleme aşamasındadır.

## <a name="create-a-basic-cost-management-report"></a>Temel maliyet yönetimi raporu oluşturma

Maliyet raporlamanın nasıl oluşturulduğunu ve raporların nereye yayımlanacağını tanımlamak için Maliyet Yönetimi API’sinde `reports` işlemini kullanın.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

`{subscriptionGuid}` parametresi gereklidir ve API belirtecinde sağlanan kimlik bilgileri kullanılarak okunabilen bir abonelik kimliğini içermelidir. 

`{reportName}` parametresi, raporun adını belirtir. Rapor adlarının listesini almak için Rapor_Listesi işlemini kullanabilirsiniz: `/subscriptions/{subscriptionId}/providers/Microsoft.CostManagement/reports`. [GitHub](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/cost-management/resource-manager/Microsoft.CostManagement/preview/2018-08-01-preview/examples/ReportList.json) üzerinde örnek çıktıyı görüntüleyin.

Aşağıdaki üstbilgiler gereklidir:

|İstek üst bilgisi|Açıklama|  
|--------------------|-----------------|  
|*Content-Type:*| Gereklidir. `application/json` olarak ayarlayın. |  
|*Yetkilendirme:*| Gereklidir. Geçerli bir `Bearer` belirtecine ayarlayın. |

HTTP istek gövdesinde raporun parametrelerini yapılandırın. Aşağıdaki örnekte rapor, etkin olduğunda her gün oluşturulacak şekilde ayarlanır, Azure Depolama blobu kapsayıcısına yazılan bir CSV dosyasıdır ve `westus` kaynak grubundaki tüm kaynaklar için saatlik maliyet bilgilerini içerir.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

Sanal Makineye (VM) bağlı bir veya birden çok işletim sistemi diski içerdiği için

## <a name="filtering-reports"></a>Raporları filtreleme

Rapor oluşturulurken istek gövdesinin `filter` ve `dimensions` bölümü, belirli kaynak türleri için maliyetlere odaklanmanıza olanak sağlar. Önceki istek gövdesinde, bir bölgedeki tüm kaynaklara göre nasıl filtreleme yapılacağı gösterilir.

### <a name="get-all-compute-usage"></a>Tüm işlem kullanım bilgilerini alma

Tüm bölgelerde aboneliğinizdeki Azure sanal makine maliyetlerini raporlamak için `ResourceType` boyutunu kullanın.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines",
                "Microsoft.Compute/virtualMachines"
        ]
    }
}
```

### <a name="get-all-database-usage"></a>Tüm veritabanı kullanım bilgilerini alma

Tüm bölgelerde aboneliğinizdeki Azure SQL Veritabanı maliyetlerini raporlamak için `ResourceType` boyutunu kullanın.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ]
    }
}
```

### <a name="report-on-specific-instances"></a>Belirli örneklerle ilgili raporlama

`Resource` boyutu, belirli kaynaklar için maliyetleri raporlamanıza olanak sağlar.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>Zaman çerçevelerini değiştirme

Hafta başından bugüne ve ay başından bugüne yerleşik seçeneklerinin dışında bir zaman çerçevesi ayarlamak için `timeframe` tanımını `Custom` olarak ayarlayın.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Sonraki adımlar
- [Azure REST API’yi kullanmaya başlayın](/rest/api/azure/)