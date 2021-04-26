---
title: Azure spot sanal makineleri dağıtmak için PowerShell 'i kullanma
description: Azure PowerShell kullanarak, maliyetlerde tasarruf etmek üzere Azure spot sanal makinelerini dağıtma hakkında bilgi edinin.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 9a2ad2eb197af613919efa4414da1759cd47e2e7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802752"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Azure PowerShell kullanarak Azure spot sanal makineleri dağıtma


[Azure spot sanal makinelerinin](../spot-vms.md) kullanılması, önemli bir maliyet tasarruflarından kullanılmamış kapasitemizin avantajlarından yararlanmanızı sağlar. Azure 'un kapasiteyi yeniden sağlaması gerektiğinde Azure altyapısı, Azure spot sanal makinelerini çıkarır. Bu nedenle, Azure spot sanal makineleri toplu işleme işleri, geliştirme/test ortamları, büyük işlem iş yükleri ve daha fazlası gibi kesintileri işleyebilen iş yükleri için mükemmeldir.

Azure spot sanal makineleri için fiyatlandırma, bölge ve SKU temel alınarak değişkendir. Daha fazla bilgi için bkz. [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ve [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)için VM fiyatlandırması. En yüksek fiyatı ayarlama hakkında daha fazla bilgi için bkz. [Azure spot sanal makineler-fiyatlandırma](../spot-vms.md#pricing).

VM için saat başına ödeme yapmak istediğiniz maksimum fiyatı ayarlama seçeneğiniz vardır. En fazla 5 ondalık basamak kullanarak bir Azure spot sanal makinesi için maksimum fiyat ABD Doları (USD) olarak ayarlanabilir. Örneğin, değer, `0.98765` saat başına $0,98765 ABD Doları olan en yüksek fiyat olacaktır. En yüksek fiyatı olacak şekilde ayarlarsanız `-1` , VM fiyata göre çıkarılmaz. Kapasite ve kota kullanılabilir olduğu sürece, sanal makine fiyatı, nokta için geçerli fiyat veya standart bir sanal makine fiyatı olacaktır.


## <a name="create-the-vm"></a>Sanal makineyi oluşturma

Yapılandırmayı oluşturmak için [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) kullanarak bir spotvm oluşturun. Dahil et `-Priority Spot` ve ayarla `-MaxPrice` :
- `-1` Bu nedenle VM, fiyata göre çıkarılmaz.
- en fazla 5 basamaklı bir dolar miktarı. Örneğin, `-MaxPrice .98765` bir spotVM 'nin fiyatı Saat başına $. 98765 ' e GEÇTIĞINDE VM 'nin serbest bırakılacak olması anlamına gelir.


Bu örnek, fiyatlandırma temelinde serbest bırakılmayacak bir spotVM oluşturur (yalnızca Azure 'un kapasiteyi geri sağlaması gerektiğinde). Çıkarma İlkesi VM 'yi serbest bırakmak üzere ayarlanır, böylece daha sonra yeniden başlatılabilir. VM çıkartılan sanal makineyi ve temel diski silmek istiyorsanız, `-EvictionPolicy` içinde olarak ayarlanır `Delete` `New-AzVMConfig` .


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

VM oluşturulduktan sonra, kaynak grubundaki tüm VM 'Lerin en yüksek fiyatını görmek için sorgulama yapabilirsiniz.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>Çıkargı benzetimi yap

Uygulamanızın ani bir çıkarmasına ne kadar iyi yanıt vereceğini test etmek için REST, PowerShell veya CLı kullanarak bir Azure spot sanal makinesi çıkarması benzetimi yapabilirsiniz.

Çoğu durumda, REST API sanal makineleri kullanmak isteyeceksiniz-uygulamaların otomatikleştirilmiş test edilmesine yardımcı olmak için [çıkarmayı benzetim](/rest/api/compute/virtualmachines/simulateeviction) . REST için, `Response Code: 204` benzetimi yapılan çıkargı başarılı oldu demektir. Sanal makine çıkarıldığında uygulamanızın nasıl yanıt vereceğini otomatik hale getirmek için [planlı olay hizmeti](scheduled-events.md)ile benzetilen çıkarmaları birleştirebilirsiniz.

Zamanlanan olayları işlem içinde görmek için [Azure Cuma 'yı izleyin-VM bakımına hazırlanmak Için azure zamanlanan olaylar kullanma](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Hızlı test

Bir sanal makinenin nasıl çalıştığını göstermek için hızlı bir test için, PowerShell kullanarak bir çıkarma benzetimi yaparken nasıl göründüğünü görmek üzere zamanlanmış olay hizmetini sorgulama adımlarını inceleyelim.

Olay için ilk kez istek yaptığınızda zamanlanan olay hizmeti hizmetiniz için etkinleştirilir. 

Sanal makinenize uzaktan ekleyin ve ardından bir komut istemi açın. 

SANAL makinenizin komut isteminden şunu yazın:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Bu ilk yanıt 2 dakikaya kadar sürebilir. Bundan sonra çıktıyı neredeyse hemen görüntülemesi gerekir.

Az PowerShell modülünün yüklü olduğu bir bilgisayardan (yerel makineniz gibi) [set-AzVM](https://docs.microsoft.com/powershell/module/az.compute/set-azvm)kullanarak bir çıkarma benzetimi yapın. Kaynak grubu adını ve VM adını kendi adınızla değiştirin. 

```azurepowershell-interactive
Set-AzVM -ResourceGroupName "mySpotRG" -Name "mySpotVM" -SimulateEviction
```

Yanıt çıkışı, `Status: Succeeded` isteğin başarılı bir şekilde yapılmış olması durumunda olacaktır.

Spot sanal makinenize hızlı bir şekilde uzak bağlantınıza dönün ve Zamanlanan Olaylar uç noktasını tekrar sorgulayın. Daha fazla bilgi içeren bir çıkış yapana kadar aşağıdaki komutu yineleyin:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Zamanlanan olay hizmeti çıkarma bildirimini aldığında şuna benzer bir yanıt alacaksınız:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Bunu `"EventType":"Preempt"` ve KAYNAĞıN VM kaynağı olduğunu görebilirsiniz `"Resources":["myspotvm"]` . 

Ayrıca, değeri denetleyerek VM 'nin ne zaman çıkartıyor olduğunu da görebilirsiniz `"NotBefore"` . VM, ' de verilen süreden önce çıkarılmayacak `NotBefore` , bu sayede uygulamanızın uygulamanız düzgün bir şekilde kapatılabilir.


## <a name="next-steps"></a>Sonraki adımlar

Azure [CLI](../linux/spot-cli.md), [Portal](../spot-portal.md) veya [şablon](../linux/spot-template.md)kullanarak Azure spot sanal makinesi de oluşturabilirsiniz.

Azure spot sanal makine fiyatlandırması hakkında bilgi için [Azure perakende fiyatları API 'sini](/rest/api/cost-management/retail-prices/azure-retail-prices) kullanarak geçerli fiyatlandırma bilgilerini sorgulayın. `meterName`Ve `skuName` her ikisi de içerecektir `Spot` .

Bir hatayla karşılaşırsanız bkz. [hata kodları](../error-codes-spot.md).
