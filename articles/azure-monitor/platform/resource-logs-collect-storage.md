---
title: Azure Kaynak günlüklerini depolama hesabına arşivleme | Microsoft Docs
description: Depolama hesabında uzun süreli saklama için Azure Kaynak günlüklerinizi arşivlemeyi öğrenin.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 306f6cb0b50b7befcbf51e6164a5da887d35616e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74030885"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Azure Kaynak günlüklerini depolama hesabına arşivleme
Azure 'da [kaynak günlükleri](resource-logs-overview.md) , bir Azure kaynağının iç işlemi hakkında zengin, sık veriler sağlar. Bu makalede, verilerin arşivlenmek üzere tutulması için bir Azure depolama hesabına kaynak günlüklerinin toplanması açıklanır.

## <a name="prerequisites"></a>Önkoşullar
Henüz yoksa bir [Azure depolama hesabı oluşturmanız](../../storage/common/storage-quickstart-create-account.md) gerekir. Ayarı yapılandıran kullanıcının her iki aboneliğe de uygun RBAC erişimi olması koşuluyla, depolama hesabının, kaynak gönderme günlükleri ile aynı abonelikte olması gerekmez.


> [!IMPORTANT]
> Azure Data Lake Storage 2. hesapları, Azure portal geçerli bir seçenek olarak listelenseler bile, şu anda Tanılama ayarları için hedef olarak desteklenmemektedir.


İzleme verilerine erişimi daha iyi denetleyebilmeniz için, içinde depolanan diğer, izleme olmayan verileri olan mevcut bir depolama hesabını kullanmamalısınız. Ayrıca, [etkinlik günlüğünü](activity-logs-overview.md) de bir depolama hesabına arşivlerken, tüm izleme verilerini merkezi bir konumda tutmak için aynı depolama hesabını kullanmayı tercih edebilirsiniz.

## <a name="create-a-diagnostic-setting"></a>Tanılama ayarı oluştur
Kaynak günlükleri varsayılan olarak toplanmaz. Azure kaynağı için bir tanılama ayarı oluşturarak bunları bir Azure depolama hesabında ve diğer hedeflere toplayın. Ayrıntılar için bkz. [Azure 'da günlükleri ve ölçümleri toplamak için tanılama ayarı oluşturma](diagnostic-settings.md) .


## <a name="data-retention"></a>Veri saklama
Bekletme ilkesi, her günlük kategorisinden ve bir depolama hesabında depolanan ölçüm verilerinden verilerin saklanacağı gün sayısını tanımlar. Bekletme ilkesi, 0 ile 365 arasında herhangi bir sayıda gün olabilir. Sıfır bekletme ilkesi, bu günlük kategorisi için olayların süresiz olarak depolandığını belirtir.

Bekletme ilkeleri günde bir uygulanır. bu nedenle günün sonunda, bekletme ilkesinin ötesinde gün içindeki Günlükler silinir. Örneğin, bir günlük bir bekletme ilkesi olsaydı, bugün günün başında dünden önceki gün kayıtları silinir. Gece yarısı UTC, ancak bu günlükleri depolama hesabınızdan silinecek 24 saate kadar sürebilir not silme işlemi başlar. 


## <a name="schema-of-resource-logs-in-storage-account"></a>Depolama hesabındaki kaynak günlüklerinin şeması

Tanılama ayarını oluşturduktan sonra, etkin günlük kategorilerinden birinde bir olay meydana geldiğinde depolama hesabında bir depolama kapsayıcısı oluşturulur. Kapsayıcı içindeki Bloblar aşağıdaki adlandırma kuralını kullanır:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Örneğin, bir ağ güvenlik grubunun blobu aşağıdakine benzer bir ada sahip olabilir:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Her PT1H.json blobu, blob URL’sinde belirtilen saat (örneğin, h=12) içinde gerçekleşen bir JSON olay blobu içerir. Mevcut saat boyunca, olaylar meydana geldikçe PT1H.json dosyasına eklenir. Kaynak günlüğü olayları saat başına ayrı blob 'lara bölündüğü için, dakika değeri (e = 00) her zaman 00 ' dır.

PT1H. json dosyasında, her olay aşağıdaki biçimde depolanır. Bu, ortak bir en üst düzey şemayı kullanır, ancak [kaynak günlükleri şemasında](resource-logs-overview.md#resource-logs-schema)açıklandığı gibi her bir Azure hizmeti için benzersizdir.

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Platform günlükleri, her olayın bir satır olduğu ve yeni bir olayı gösterdiği [JSON satırları](http://jsonlines.org/)kullanılarak BLOB depolama alanına yazılır. Bu biçim Kasım 2018 ' de uygulandı. Bu tarihten önce, [Azure izleyici platformu günlüklerine yapılan biçim değişikliği Için hazırlama](resource-logs-blob-format.md)bölümünde açıklandığı gibi bir JSON dizisi olarak blob depolamaya yazılan günlükler bir depolama hesabına arşivlendi.

## <a name="next-steps"></a>Sonraki adımlar

* [Analiz için Blobları indirin](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Azure izleyici ile Azure Active Directory günlüklerini arşivleyin](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
* [Kaynak günlükleri hakkında daha fazla bilgi edinin](../../azure-monitor/platform/resource-logs-overview.md).

