---
title: REST API kullanarak Azure sanal makine kullanım verilerini alın
description: Azure REST API 'Lerini kullanarak bir sanal makine için kullanım ölçümleri toplayın.
services: virtual-machines
author: rloutlaw
ms.reviewer: routlaw
manager: gwallace
ms.service: load-balancer
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 523b81e53f2b0622b237993dbd88fb9492079c86
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035819"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>REST API kullanarak sanal makine kullanım ölçümlerini alın

Bu örnek, [Azure REST API](/rest/api/azure/)kullanarak bir [Linux sanal makinesi](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) için CPU kullanımının nasıl alınacağını gösterir.

Tüm başvuru belgelerini ve REST API için ek örnekleri [Azure IZLEYICI Rest başvurusunda](/rest/api/monitor)bulabilirsiniz. 

## <a name="build-the-request"></a>İsteği oluşturma

Bir sanal makineden alınan [CPU ölçüsünü yüzde](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) olarak toplamak IÇIN aşağıdaki get isteğini kullanın

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>İstek üst bilgileri

Aşağıdaki üstbilgiler gereklidir: 

|İstek üst bilgisi|Açıklama|  
|--------------------|-----------------|  
|*Content-Type:*|Gereklidir. `application/json` olarak ayarlayın.|  
|*Yetkilendirme:*|Gereklidir. Geçerli bir `Bearer` [erişim belirtecine](/rest/api/azure/#authorization-code-grant-interactive-clients) ayarlayın. |  

### <a name="uri-parameters"></a>URI parametreleri

| Ad | Açıklama |
| :--- | :---------- |
| subscriptionId | Bir Azure aboneliğini tanımlayan abonelik KIMLIĞI. Birden çok aboneliğiniz varsa bkz. [birden çok abonelikle çalışma](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Kaynakla ilişkili Azure Kaynak grubunun adı. Bu değeri Azure Resource Manager API, CLı veya portaldan edinebilirsiniz. |
| VMName | Azure sanal makinesinin adı. |
| metricnames | Geçerli [Load Balancer ölçümlerinin](/azure/load-balancer/load-balancer-standard-diagnostics)virgülle ayrılmış listesi. |
| api-version | İstek için kullanılacak API sürümü.<br /><br /> Bu belgede, yukarıdaki URL 'ye dahil edilen api-Version `2018-01-01`yer almaktadır.  |
| TimeSpan | Döndürülen ölçümlerin zaman aralığını tanımlayan aşağıdaki biçimdeki `startDateTime_ISO/endDateTime_ISO` dize. Bu isteğe bağlı parametre, örnekteki verilerin bir gününü döndürecek şekilde ayarlanır. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>İstek gövdesi

Bu işlem için istek gövdesi gerekli değil.

## <a name="handle-the-response"></a>Yanıtı işleme

200 durum kodu, ölçüm değerleri listesi başarıyla döndürüldüğünde döndürülür. [Başvuru belgelerinde](/rest/api/monitor/metrics/list#errorresponse)hata kodlarının tam bir listesi bulunur.

## <a name="example-response"></a>Örnek yanıt 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```
