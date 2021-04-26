---
title: Belirtilen genel IP adreslerine sahip bir havuz oluşturun
description: Kendi genel IP adreslerinizi kullanan bir Batch havuzu oluşturmayı öğrenin.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: 82a37f96a91bdad37c1a7828ef0cf71b3581ca82
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768404"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>Belirtilen genel IP adreslerine sahip bir Azure Batch havuzu oluşturun

Bir Azure Batch havuzu oluşturduğunuzda, [havuzu belirttiğiniz bir Azure sanal ağının](batch-virtual-network.md) (VNet) alt ağında sağlayabilirsiniz. Batch havuzundaki sanal makinelere, Batch tarafından oluşturulan genel IP adresleri üzerinden erişilir. Bu genel IP adresleri havuzun ömrü boyunca değişebilir, bu da IP adresleri yenilenmemişse ağ ayarlarınızın güncelliğini yitirmiş hale gelebileceği anlamına gelir.

Havuzunuzdaki sanal makinelerle birlikte kullanmak üzere statik genel IP adreslerinin bir listesini oluşturabilirsiniz. Bu, genel IP adreslerinin listesini denetlemenizi ve bunların beklenmedik şekilde değişmemesini sağlar. Bu, özellikle belirli IP adreslerine erişimi kısıtlayan bir veritabanı gibi herhangi bir dış hizmetle çalışıyorsanız yararlı olabilir.

Genel IP adresleri olmayan havuzlar oluşturma hakkında bilgi için, [genel IP adresleri olmadan Azure Batch havuzu oluştur](./batch-pool-no-public-ip-address.md)' u okuyun.

## <a name="prerequisites"></a>Önkoşullar

- **Kimlik doğrulaması**. Genel bir IP adresi kullanmak için Batch istemci API 'sinin [Azure Active Directory (ad) kimlik doğrulamasını](batch-aad-auth.md)kullanması gerekir.

- **Bir Azure sanal ağı**. Havuzunuzu ve IP adreslerinizi oluşturduğunuz aynı Azure aboneliğinden bir [sanal ağ](batch-virtual-network.md) kullanmanız gerekir. Yalnızca Azure Resource Manager tabanlı VNET 'ler kullanılabilir. VNet 'in tüm [genel gereksinimleri](batch-virtual-network.md#vnet-requirements)karşıladığından emin olun.

- **En az bir Azure genel IP adresi**. Bir veya daha fazla genel IP adresi oluşturmak için [Azure Portal](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address), [Azure Command-Line arabirimi (clı)](/cli/azure/network/public-ip#az_network_public_ip_create)veya [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress)kullanabilirsiniz. Aşağıda listelenen gereksinimleri izlediğinizden emin olun.

> [!NOTE]
> Batch, genel IP adreslerini içeren kaynak grubundaki ek ağ kaynaklarını otomatik olarak ayırır. Batch, her 100 ayrılmış düğüm için genellikle bir ağ güvenlik grubu (NSG) ve bir yük dengeleyici ayırır. Bu kaynaklar, aboneliğin kaynak kotalarıyla sınırlıdır. Daha büyük havuzlar kullanılırken, bir veya daha fazla kaynağın bir veya daha fazlası için [bir kota artışı istemeniz](batch-quota-limit.md#increase-a-quota) gerekebilir.

## <a name="public-ip-address-requirements"></a>Genel IP adresi gereksinimleri

Genel IP adreslerinizi oluştururken aşağıdaki gereksinimleri göz önünde bulundurun:

- Genel IP adresleri, havuzunuzu oluşturmak için kullandığınız Batch hesabıyla aynı abonelikte ve bölgede olmalıdır.
- **IP adresi ataması** **statik** olarak ayarlanmalıdır.
- **SKU** **Standart** olarak ayarlanmalıdır.
- DNS adı belirtilmelidir.
- Genel IP adreslerinin yalnızca sanal makine yapılandırma havuzları için kullanılması gerekir. Başka hiçbir kaynak bu IP adreslerini kullanmamalıdır veya havuz ayırma hatalarıyla karşılaşabilir.
- Hiçbir güvenlik ilkesi veya kaynak kilidi, bir kullanıcının genel IP adresine erişimini kısıtlayamaz.
- Havuz için belirtilen genel IP adresi sayısı, havuzun hedeflediği VM sayısına uyum sağlayacak kadar büyük olmalıdır. Bu, havuzun en azından hedeflenmiş olarak bulunan **düğümlerin**   ve **Targetlowprioritynodes**   özelliklerinin toplamı olmalıdır. Yeterli IP adresi yoksa, havuz işlem düğümlerini kısmen ayırır ve bir yeniden boyutlandırma hatası oluşur. Şu anda Batch, her 100 VM için bir genel IP adresi kullanır.
- Her zaman ek bir genel IP adresi arabelleği olmalıdır. Bir havuza eklediğiniz en az bir genel IP adresi veya bir havuza eklediğiniz toplam genel IP adreslerinin yaklaşık %10 ' u daha büyük bir değer eklemenizi öneririz. Bu ek arabellek, ölçeklendirilirken iç iyileştirmesine ve başarısız bir ölçeğe göre daha hızlı ölçeklendirilmesine ve ölçeği ölçeklendirilmesine yardımcı olur.
- Havuz oluşturulduktan sonra, havuz tarafından kullanılan genel IP adresleri listesini ekleyemez veya değiştiremezsiniz. Listeyi değiştirmeniz gerekiyorsa, havuzu silip yeniden oluşturmanız gerekir.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>Genel IP adresleriyle bir Batch havuzu oluşturma

Aşağıdaki örnekte, genel IP adresleri kullanan bir havuz oluşturmak için [Azure Batch hizmeti REST API](/rest/api/batchservice/pool/add) nasıl kullanılacağı gösterilmektedir.

### <a name="batch-service-rest-api"></a>Batch Hizmeti REST API’si

REST API URI'si

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

İstek Gövdesi

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "taskSlotsPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      },
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>Sonraki adımlar

- [Batch hizmeti iş akışı ve](batch-service-workflow-features.md) havuzlar, düğümler, işler ve görevler gibi birincil kaynaklar hakkında bilgi edinin.
- Bir [Azure sanal ağının alt ağında havuz oluşturma](batch-virtual-network.md)hakkında bilgi edinin.
- [Genel IP adresleri olmayan Azure Batch havuzu oluşturma](./batch-pool-no-public-ip-address.md)hakkında bilgi edinin.
