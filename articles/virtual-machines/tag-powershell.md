---
title: PowerShell kullanarak VM etiketleme
description: PowerShell kullanarak bir sanal makineyi etiketleme hakkında bilgi edinin
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: bf13d5c0caeb0bf31a383cd23155a6856c81c53b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897400"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Azure 'da PowerShell kullanarak bir sanal makineyi etiketleme

Bu makalede, PowerShell kullanarak Azure 'da bir VM 'nin nasıl etiketleneceğini açıklanmaktadır. Etiketler, doğrudan bir kaynağa veya bir kaynak grubuna yerleştirilebilecek Kullanıcı tanımlı anahtar/değer çiftleridir. Azure Şu anda kaynak ve kaynak grubu başına en fazla 50 etiketi desteklemektedir. Etiketler, oluşturma sırasında veya var olan bir kaynağa eklenen bir kaynağa yerleştirilebilir. Azure CLı kullanarak bir sanal makineyi etiketlemek istiyorsanız bkz. Azure [CLI kullanarak Azure 'da bir sanal makineyi etiketleme](tag-cli.md).

`Get-AzVM`VM 'nizin geçerli etiket listesini görüntülemek için cmdlet 'ini kullanın.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

Sanal makineniz zaten etiketler içeriyorsa, tüm etiketleri liste biçiminde görürsünüz.

Etiket eklemek için `Set-AzResource` komutunu kullanın. Etiketler PowerShell aracılığıyla güncelleştirilirken Etiketler bir bütün olarak güncelleştirilir. Zaten etiketlere sahip olan bir kaynağa bir etiket ekliyorsanız, kaynağa yerleştirilmesini istediğiniz tüm etiketleri dahil etmeniz gerekir. Aşağıda, PowerShell cmdlet 'Leri aracılığıyla bir kaynağa ek etiketlerin nasıl ekleneceği gösterilmektedir.

Ve özelliğini kullanarak VM için tüm geçerli etiketleri `$tags` değişkenine atayın `Get-AzResource` `Tags` .

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

Geçerli etiketleri görmek için değişkeni yazın.

```azurepowershell-interactive
$tags
```

Çıktının şöyle görünebileceğini aşağıda bulabilirsiniz:

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

Aşağıdaki örnekte, değerine sahip adlı bir etiket ekleyeceğiz `Location` `myLocation` . `+=`Yeni anahtar/değer çiftini listeye eklemek için kullanın `$tags` .

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

`Set-AzResource`VM 'deki *$Tags* değişkeninde tanımlanmış tüm etiketleri ayarlamak için kullanın.

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

`Get-AzResource`Kaynaktaki tüm etiketleri göstermek için kullanın.

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

Çıktı aşağıdakine benzer şekilde görünmelidir. Bu, şu anda yeni etiketi içerir:

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```

### <a name="next-steps"></a>Sonraki adımlar

- Azure kaynaklarınızın etiketlenmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Resource Manager genel bakış](../azure-resource-manager/management/overview.md) ve [etiketleri kullanarak Azure kaynaklarınızı düzenleme](../azure-resource-manager/management/tag-resources.md).
- Etiketlerin Azure kaynakları kullanımını yönetmenize nasıl yardımcı olduğunu görmek için bkz. [Azure Faturanızı Anlama](../cost-management-billing/understand/review-individual-bill.md).
