---
title: Kullanılabilirlik alanları arasında havuz oluşturma
description: Hatalara karşı korumaya yardımcı olmak için, bölgesel ilkesiyle bir Batch havuzu oluşturmayı öğrenin.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 56e718bedf504b8e69598c2d99ab8b889a470b89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101725297"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>Kullanılabilirlik Alanları genelinde Azure Batch havuzu oluşturma

[Kullanılabilirlik alanları](https://azure.microsoft.com/global-infrastructure/availability-zones/) destekleyen Azure bölgeleri, her biri kendi bağımsız güç kaynağına, ağına ve soğutma sistemine sahip olan en az üç ayrı bölgeye sahiptir. Sanal makine yapılandırmasını kullanarak bir Azure Batch havuzu oluşturduğunuzda, Batch havuzunuzu Kullanılabilirlik Alanları arasında sağlamayı tercih edebilirsiniz. Bu bölgesel ilkesiyle havuzunuzu oluşturmak, Batch işlem düğümlerinizi Azure veri merkezi düzeyindeki hatalardan korumanıza yardımcı olur.

Örneğin, havuzunuzu üç kullanılabilirlik alanları destekleyen bir Azure bölgesinde bölgesel ilkesiyle oluşturabilirsiniz. Tek kullanılabilirlik bölgesindeki bir Azure veri merkezinde altyapı arızası varsa, Batch havuzunuzun diğer iki Kullanılabilirlik Alanları sağlıklı düğümleri olmaya devam eder, bu nedenle havuz görev zamanlaması için kullanılabilir kalır.

## <a name="regional-support-and-other-requirements"></a>Bölgesel destek ve diğer gereksinimler

Batch, Kullanılabilirlik Alanları destekleyici Azure ile eşlik sağlar. Bölgesel seçeneğini kullanmak için, havuzunuzun [desteklenen bir Azure bölgesinde](../availability-zones/az-region.md)oluşturulması gerekir.

Toplu Iş havuzunuzun kullanılabilirlik alanları arasında ayrılması için, havuzun oluşturulduğu Azure bölgesinin, istenen VM SKU 'sunu birden fazla bölgede desteklemesi gerekir. [Kaynak SKU 'Ları LISTE API](/rest/api/compute/resourceskus/list) 'sini çağırarak ve [Resourcesku](/rest/api/compute/resourceskus/list#resourcesku)'ın **LocationInfo** alanını denetleyerek bunu doğrulayabilirsiniz. İstenen VM SKU 'SU için birden fazla bölgenin desteklendiğinden emin olun.

[Kullanıcı aboneliği modu Batch hesapları](accounts.md#batch-accounts)için, havuzunuzu oluşturmakta olduğunuz aboneliğin, istenen VM SKU 'sunda bir bölge teklif kısıtlaması olmadığından emin olun. Bunu onaylamak için [kaynak SKU 'Ları LISTE API](/rest/api/compute/resourceskus/list) 'sini çağırın ve [resourceskurestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions)kontrol edin. Bir bölge kısıtlaması varsa, bölge kısıtlamasını kaldırmak için bir [destek bileti](/troubleshoot/azure/general/region-access-request-process) gönderebilirsiniz.

Ayrıca, düğüm içi iletişimin etkin olması ve [InfiniBand 'yi destekleyen bir VM SKU 'su](../virtual-machines/workloads/hpc/enable-infiniband.md)kullanması durumunda bölgesel ilkesiyle bir havuz oluşturmayacağınızı unutmayın.

## <a name="create-a-batch-pool-across-availability-zones"></a>Kullanılabilirlik Alanları arasında bir Batch havuzu oluşturma

Aşağıdaki örneklerde Kullanılabilirlik Alanları arasında bir toplu Iş havuzunun nasıl oluşturulacağı gösterilmektedir.

> [!NOTE]
> Havuzunuzu bir ilke ile oluştururken, Batch hizmeti, havuzunuzu seçili bölgedeki tüm Kullanılabilirlik Alanları ayırmaya çalışır; bölgeler genelinde belirli bir ayırma belirtemezsiniz.

### <a name="batch-management-client-net-sdk"></a>Batch Management Istemci .NET SDK 'Sı

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>Batch REST API

REST API URL 'SI

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

İstek gövdesi

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Batch hizmeti iş akışı ve](batch-service-workflow-features.md) havuzlar, düğümler, işler ve görevler gibi birincil kaynaklar hakkında bilgi edinin.
- Bir [Azure sanal ağının alt ağında havuz oluşturma](batch-virtual-network.md)hakkında bilgi edinin.
- [Genel IP adresleri olmayan Azure Batch havuzu oluşturma](./batch-pool-no-public-ip-address.md)hakkında bilgi edinin.