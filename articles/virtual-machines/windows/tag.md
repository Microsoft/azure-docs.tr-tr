---
title: Azure 'da bir Windows VM kaynağını etiketleme
description: Kaynak Yöneticisi dağıtım modelini kullanarak Azure 'da oluşturulan bir Windows sanal makinesini etiketleme hakkında bilgi edinin
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 52f8dbfc8665fce832eaa60e204273368ae7485d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74068194"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Azure 'da bir Windows sanal makinesini etiketleme
Bu makalede, Azure 'daki bir Windows sanal makinesini Kaynak Yöneticisi dağıtım modeliyle etiketlemek için farklı yollar açıklanmaktadır. Etiketler, doğrudan bir kaynağa veya bir kaynak grubuna yerleştirilebilecek Kullanıcı tanımlı anahtar/değer çiftleridir. Azure Şu anda kaynak ve kaynak grubu başına en fazla 15 etiket desteklemektedir. Etiketler, oluşturma sırasında veya var olan bir kaynağa eklenen bir kaynağa yerleştirilebilir. Lütfen etiketlerin yalnızca Kaynak Yöneticisi dağıtım modeli aracılığıyla oluşturulan kaynaklar için desteklendiğini unutmayın. Bir Linux sanal makinesini etiketlemek istiyorsanız bkz. [Azure 'Da Linux sanal makinesini etiketleme](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>PowerShell ile etiketleme
PowerShell aracılığıyla etiket oluşturmak, eklemek ve silmek için önce [PowerShell ortamınızı Azure Resource Manager][PowerShell environment with Azure Resource Manager]ayarlamanız gerekir. Kurulumu tamamladıktan sonra, oluşturma sırasında veya kaynak PowerShell aracılığıyla oluşturulduktan sonra Işlem, ağ ve depolama kaynaklarına Etiketler yerleştirebilirsiniz. Bu makale, sanal makinelere yerleştirilmiş etiketleri görüntüleme/düzenlemeyle ilgili olarak yoğunlaşacaktır.

 

İlk olarak, `Get-AzVM` cmdlet 'i aracılığıyla bir sanal makineye gidin.

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Sanal makineniz zaten etiketler içeriyorsa, kaynağınızın tüm etiketlerini görürsünüz:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

PowerShell aracılığıyla Etiketler eklemek istiyorsanız, `Set-AzResource` komutunu kullanabilirsiniz. Bkz. Etiketler PowerShell aracılığıyla güncelleştirilirken, Etiketler bir bütün olarak güncelleştirilir. Bu nedenle, zaten etiketlere sahip olan bir kaynağa bir etiket ekliyorsanız, kaynağa yerleştirilmesini istediğiniz tüm etiketleri dahil etmeniz gerekir. Aşağıda, PowerShell cmdlet 'Leri aracılığıyla bir kaynağa ek etiketlerin nasıl ekleneceği gösterilmektedir.

Bu ilk cmdlet, *Mytestvm* üzerinde yer alan tüm etiketleri `Get-AzResource` ve `Tags` özelliğini kullanarak *$Tags* değişkenine ayarlar.

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

İkinci komut, verilen değişkenin etiketlerini görüntüler.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

Üçüncü komut *$Tags* değişkenine ek bir etiket ekler. Yeni anahtar/değer çiftini *$Tags* listesine eklemek için **+=** kullanımını dikkate alın.

        PS C:\> $tags += @{Location="MyLocation"}

Dördüncü komut *$Tags* değişkeninde tanımlanan tüm etiketleri verilen kaynağa ayarlar. Bu durumda, test sanal makinedir.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Beşinci komut kaynaktaki tüm etiketleri görüntüler. Gördüğünüz gibi, *konum* artık değer olarak *MyLocation* içeren bir etiket olarak tanımlanmıştır.

```
    PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

PowerShell aracılığıyla etiketleme hakkında daha fazla bilgi edinmek için [Azure Kaynak cmdlet 'lerine][Azure Resource Cmdlets]göz atın.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Sonraki adımlar
* Azure kaynaklarınızın etiketlenmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Resource Manager genel bakış][Azure Resource Manager Overview] ve [etiketleri kullanarak Azure kaynaklarınızı düzenleme][Using Tags to organize your Azure Resources].
* Etiketlerin Azure kaynaklarını kullanımınızı yönetmenize nasıl yardımcı olduğunu görmek için bkz. [Azure Faturanızı Anlama][Understanding your Azure Bill] ve [Microsoft Azure Kaynak tüketiminiz hakkında öngörüler elde][Gain insights into your Microsoft Azure resource consumption]etme.

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
