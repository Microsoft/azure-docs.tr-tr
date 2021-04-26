---
title: Oluşturma veya güncelleştirme işlemlerine yönelik PUT çağrıları
description: İşlem kaynaklarında oluşturma veya güncelleştirme işlemleri için çağrılar koy
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: d6ee4179dce905d637e933743ade7452a2484077
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91978569"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>İşlem kaynaklarında oluşturma veya güncelleştirme için çağrılar koy

`Microsoft.Compute` kaynaklar, *http put* semantiğinin geleneksel tanımını desteklemez. Bunun yerine, bu kaynaklar hem PUT hem de PATCH fiilleri için PATCH semantiğini kullanır.

**Oluşturma** işlemleri uygun olduğunda varsayılan değerleri uygular. Ancak, PUT veya PATCH aracılığıyla yapılan kaynak **güncelleştirmeleri** , hiçbir varsayılan özelliği uygulamaz. **Güncelleştirme** işlemleri geçerlidir katı düzeltme eki semantiği uygulayın.

Örneğin, `caching` bir sanal makinenin disk özelliği, `ReadWrite` kaynağın bir işletim sistemi diski olması durumunda varsayılan olarak kullanılır.

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

Ancak, bir özellik ayrıldıklarında veya *null* değer geçirildiğinde **güncelleştirme** işlemleri için değiştirilmeden kalır ve değer değeri yok olur.

Bu, bir ilişkiyi kaldırma amacıyla bir kaynağa güncelleştirme işlemleri gönderilirken önemlidir. Bu kaynak bir `Microsoft.Compute` kaynaktır, kaldırmak istediğiniz ilgili özelliğin açıkça çağrılması ve atanan bir değer olması gerekir. Bunu başarmak için, kullanıcılar **""** gibi boş bir dize geçirebilir. Bu, platforma bu ilişkilendirmeyi kaldırmalarını sağlar.

> [!IMPORTANT]
> Dizi öğesi "Düzeltme eki uygulama" desteği yoktur. Bunun yerine, istemcinin, güncelleştirilmiş dizinin tüm içeriğiyle birlikte bir PUT veya PATCH isteği yapması gerekir. Örneğin, bir veri diskini bir VM 'den ayırmak için, geçerli VM modelini almak üzere bir GET isteği yapın, diski kullanımdan çıkarmak için kaldırın `properties.storageProfile.dataDisks` ve GÜNCELLEŞTIRILMIŞ VM varlığına sahip BIR PUT isteği yapın.

## <a name="examples"></a>Örnekler

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>Yakınlık yerleşimi grupları ilişkilendirmesini kaldırmak için doğru yük

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>Yakınlık yerleşimi grupları ilişkilendirmesini kaldırmak için yanlış yük

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>Sonraki Adımlar
[Sanal makineler](/rest/api/compute/virtualmachines/createorupdate) ve [Sanal Makine Ölçek Kümeleri](/rest/api/compute/virtualmachinescalesets/createorupdate) için oluşturma veya güncelleştirme çağrıları hakkında daha fazla bilgi edinin