---
title: REST API kullanarak Azure Sentinel 'de hunme ve canlı akış sorgularını yönetme | Microsoft Docs
description: Bu makalede, Azure Sentinel özelliklerinin, arama ve canlı akış sorgularını yönetmek için Log Analytics ' REST API avantajına sahip olduğu açıklanır.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: 64f05e18ff757d9f086cf06d74109bf64e32a05c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98795688"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>Azure Sentinel'de REST API kullanarak avlanma ve canlı akış sorgularını yönetme

Azure 'da yerleşik olarak oluşturulan Azure Sentinel Log Analytics, arama ve canlı akışı sorgularını yönetmek için Log Analytics ' REST API kullanmanıza olanak sağlar. Bu belgede, REST API kullanarak sorguları nasıl oluşturacağınız ve yöneteceğiniz gösterilmektedir.  Bu şekilde oluşturulan sorgular, Azure Sentinel Kullanıcı arabiriminde görüntülenir.

[Kayıtlı aramalar API 'si](/rest/api/loganalytics/savedsearches)hakkında daha fazla bilgi için kesin REST API başvurusuna bakın.

## <a name="api-examples"></a>API örnekleri

Aşağıdaki örneklerde, bu yer tutucuları aşağıdaki tabloda önceden tanımlanmış yenisiyle değiştirin:

| Yer tutucu | Şununla değiştir |
|-|-|
| **SubscriptionID** | hunme veya canlı akışı sorgusunu uyguladığınız aboneliğin adı. |
| **ResourceGroupName** | Arama veya canlı akışı sorgusunu uyguladığınız kaynak grubunun adı. |
| **{Savedsearchıd}** | Her bir arama sorgusu için benzersiz bir kimlik (GUID). |
| **WorkspaceName** | sorgunun hedefi olan Log Analytics çalışma alanının adı. |
| **DisplayName** | sorgu için tercih ettiğiniz bir görünen ad. |
| **Açıklaması** | hunme veya canlı akışı sorgusunun açıklaması. |
| **Taktiklerini** | sorguya uygulanan ilgili MITRE ATT&CK tactika. |
| **Sorgulayamadı** | Sorgunuzun sorgu ifadesi. |
|  

### <a name="example-1"></a>Örnek 1

Bu örnekte, belirli bir Azure Sentinel çalışma alanı için bir arama sorgusu oluşturma veya güncelleştirme işlemlerinin nasıl yapılacağı gösterilmektedir.  Bir canlı akışı sorgusu için, *"Category": "sorguları ara"* öğesini **istek gövdesinde**" *Category": "canlı akışı Queries* " ile değiştirin: 

#### <a name="request-header"></a>İstek üst bilgisi

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>İstek gövdesi

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>Örnek 2

Bu örnekte, belirli bir Azure Sentinel çalışma alanı için bir arama veya canlı akışı sorgusunun nasıl silineceği gösterilmektedir:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>Örnek 3

Bu örnek, belirli bir çalışma alanı için bir ara veya canlı akış sorgusu almanızı gösterir:

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Sentinel 'de Log Analytics API 'sini kullanarak hunme ve canlı akış sorgularını yönetmeyi öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Tehditler için proaktif araya](hunting.md)
- [Otomatik arama kampanyalarını çalıştırmak için not defterlerini kullanın](notebooks.md)