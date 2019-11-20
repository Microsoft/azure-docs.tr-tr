---
title: Azure adanmış Konakları Azure PowerShell kullanarak dağıtma
description: Azure PowerShell kullanarak VM 'Leri adanmış ana bilgisayarlara dağıtın.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.openlocfilehash: 92dca6f4f41ff426aebcb8e580653afaa71afff8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033364"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Önizleme: Azure PowerShell kullanarak VM 'Leri adanmış konaklara dağıtma

Bu makalede, sanal makinelerinizi (VM 'Ler) barındırmak için Azure [adanmış ana bilgisayar](dedicated-hosts.md) oluşturma konusunda size kılavuzluk eder. 

Azure PowerShell sürüm 2.4.2 sections veya sonraki bir sürümü yüklediğinizden emin olun ve içinde `Connect-AzAccount`bir Azure hesabında oturum açtığınızdan emin olun. Sürüm 2.4.2 sections yüklemek için bir PowerShell komut istemi açın ve şunu yazın:

```powershell
Install-Module -Name Az.Compute -Repository PSGallery -RequiredVersion 2.4.2-preview -AllowPrerelease
```

PowerShell 'de önizleme modülü işlevselliğini etkinleştirmek için PowerShellGet modülünün en az sürüm 1.6.0 gerekir. PowerShell Core 'un en son sürümleri bu otomatik olarak yerleşiktir, ancak PowerShell 'in eski sürümleri için, en son sürüme güncelleştirmek üzere aşağıdaki komutu çalıştırabilirsiniz:

```powershell
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```


> [!IMPORTANT]
> Azure ayrılmış Konakları Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Bilinen önizleme sınırlamaları**
> - Sanal Makine Ölçek Kümeleri Şu anda adanmış konaklarda desteklenmiyor.
> - Önizleme ilk sürümü şu VM serisini destekler: DSv3 ve ESv3. 



## <a name="create-a-host-group"></a>Konak grubu oluştur

**Konak grubu** , adanmış Konakları koleksiyonunu temsil eden bir kaynaktır. Bir bölge ve kullanılabilirlik bölgesinde bir konak grubu oluşturur ve bu gruba ana bilgisayar ekleyebilirsiniz. Yüksek kullanılabilirlik için planlama yaparken ek seçenekler vardır. Aşağıdaki seçeneklerden birini veya her ikisini de adanmış konaklarınız ile kullanabilirsiniz: 
- Birden çok kullanılabilirlik alanı arasında yayılır. Bu durumda, kullanmak istediğiniz her bölgede bir konak grubunuz olması gerekir.
- Fiziksel raflarla eşlenen birden çok hata etki alanı arasında yayılma. 
 
Her iki durumda da, konak grubunuz için hata etki alanı sayısını sağlamanız gerekir. Grubunuzdaki hata etki alanlarına yayılması istemiyorsanız, hata etki alanı sayısı 1 ' i kullanın. 

Hem kullanılabilirlik bölgelerini hem de hata etki alanlarını kullanmaya karar verebilirsiniz. Bu örnek, 2 hata etki alanı ile bölge 1 ' de bir konak grubu oluşturur. 


```powershell
$rgName = "myDHResourceGroup"
$location = "East US"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>Konak Oluşturma

Şimdi konak grubunda adanmış bir konak oluşturalım. Konak için bir ada ek olarak, ana bilgisayar için SKU sağlamanız gerekir. Ana bilgisayar SKU 'SU, desteklenen VM serisini ve adanmış ana bilgisayarınız için donanım oluşturmayı yakalar.  Önizleme sırasında, aşağıdaki konak SKU değerlerini destekliyoruz: DSv3_Type1 ve ESv3_Type1.


Konak SKU 'Ları ve fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure ayrılmış ana bilgisayar fiyatlandırması](https://aka.ms/ADHPricing).

Konak grubunuz için bir hata etki alanı sayısı ayarlarsanız, ana bilgisayarınız için hata etki alanını belirtmeniz istenir. Bu örnekte, ana bilgisayar için hata etki alanını 1 olarak ayarlayacağız.


```powershell
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>VM oluşturma

Adanmış konakta bir sanal makine oluşturun. 

Konak grubunuzu oluştururken bir kullanılabilirlik alanı belirttiyseniz, sanal makineyi oluştururken aynı bölgeyi kullanmanız gerekir. Bu örnekte, ana bilgisayar grubumuz bölge 1 ' de olduğundan, VM 'yi bölge 1 ' de oluşturmamız gerekiyor.  


```powershell
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Yeterli kaynağı olmayan bir konakta sanal makine oluşturursanız, sanal makine başarısız durumda oluşturulur. 

## <a name="check-the-status-of-the-host"></a>Konağın durumunu denetleme

Ana bilgisayar sistem durumunu ve `-InstanceView` parametresi ile [GetAzHost](/powershell/module/az.compute/get-azhost) kullanarak konağa ne kadar sanal makine dağıtacağınızı kontrol edebilirsiniz.

```
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

Çıktı şuna benzer olacaktır:

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="clean-up"></a>Temizleme

Hiçbir sanal makine dağıtılmamışsa bile adanmış ana bilgisayarlar için ücret ödersiniz. Maliyetleri kaydetmek için şu anda kullanmadığınız tüm Konakları silmelisiniz.  

Bir konağı yalnızca, onu kullanan daha fazla sanal makine olmadığında silebilirsiniz. [Remove-AzVM](/powershell/module/az.compute/remove-azvm)' i kullanarak VM 'leri silin.

```powershell
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

VM 'Leri sildikten sonra [Remove-AzHost](/powershell/module/az.compute/remove-azhost)komutunu kullanarak Konağı silebilirsiniz.

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Tüm konaklarınızı sildikten sonra [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup)komutunu kullanarak konak grubunu silebilirsiniz. 

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Ayrıca, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)komutunu kullanarak tüm kaynak grubunu tek bir komutta silebilirsiniz. Bu işlem, tüm VM 'Ler, konaklar ve konak grupları dahil olmak üzere grupta oluşturulan tüm kaynakları siler.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Sonraki adımlar

- [Burada](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), bir bölgedeki maksimum dayanıklılık için hem bölge hem de hata etki alanı kullanan örnek şablon vardır.

- Ayrıca, [Azure Portal](dedicated-hosts-portal.md)kullanarak adanmış konaklar da dağıtabilirsiniz.
