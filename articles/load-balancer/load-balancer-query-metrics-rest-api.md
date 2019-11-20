---
title: REST API ölçümleri alma
titlesuffix: Azure Load Balancer
description: Belirli bir zaman ve tarih aralığı için Load Balancer sistem durumu ve kullanım ölçümleri toplamak üzere Azure REST API 'Lerini kullanın.
services: sql-database
author: asudbring
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 06/06/2017
ms.author: allensu
ms.openlocfilehash: 0d12dc04aff58dd6273d8d29d422bdbd9e7c886b
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274537"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>REST API kullanarak Load Balancer kullanım ölçümleri alın

Bu nasıl yapılır, [Azure REST API](/rest/api/azure/)kullanarak bir zaman aralığı için [Standart Load Balancer](/azure/load-balancer/load-balancer-standard-overview) işlenen bayt sayısını nasıl toplayacağınızı gösterir.

Tüm başvuru belgelerini ve REST API için ek örnekleri [Azure IZLEYICI Rest başvurusunda](/rest/api/monitor)bulabilirsiniz. 

## <a name="build-the-request"></a>Derleme isteği

Bir Standart Load Balancer [ByteCount ölçümünü](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) toplamak IÇIN aşağıdaki get isteğini kullanın. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>İstek üst bilgileri

Aşağıdaki üstbilgiler gereklidir: 

|İstek üst bilgisi|Açıklama|  
|--------------------|-----------------|  
|*Content-Type:*|Gerekli. Olarak `application/json`ayarlayın.|  
|*Authorization:*|Gerekli. Geçerli `Bearer` bir [erişim belirtecine](/rest/api/azure/#authorization-code-grant-interactive-clients)ayarlayın. |  

### <a name="uri-parameters"></a>URI parametreleri

| Ad | Açıklama |
| :--- | :---------- |
| subscriptionId | Bir Azure aboneliğini tanımlayan abonelik KIMLIĞI. Birden çok aboneliğiniz varsa bkz. [birden çok abonelikle çalışma](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Kaynağı içeren kaynak grubunun adı. Bu değeri Azure Resource Manager API, CLı veya portaldan elde edebilirsiniz. |
| loadBalancerName | Azure Load Balancer adı. |
| metricnames | Geçerli [Load Balancer ölçümlerinin](/azure/load-balancer/load-balancer-standard-diagnostics)virgülle ayrılmış listesi. |
| API sürümü | İstek için kullanılacak API sürümü.<br /><br /> Bu belge, yukarıdaki URL 'ye `2018-01-01`dahil edilen api sürümünü içerir.  |
| TimeSpan | Sorgunun TimeSpan değeri. Aşağıdaki biçimde `startDateTime_ISO/endDateTime_ISO`bir dizedir. Bu isteğe bağlı parametre, örnekteki verilerin bir gününü döndürecek şekilde ayarlanır. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>İstek gövdesi

Bu işlem için istek gövdesi gerekli değil.

## <a name="handle-the-response"></a>Yanıtı işleme

200 durum kodu, ölçüm değerleri listesi başarıyla döndürüldüğünde döndürülür. [Başvuru belgelerinde](/rest/api/monitor/metrics/list#errorresponse)hata kodlarının tam bir listesi bulunur.

## <a name="example-response"></a>Örnek yanıt 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```
