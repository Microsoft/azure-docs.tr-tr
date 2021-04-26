---
title: Öğretici-Azure PowerShell ile Windows VM 'Leri oluşturma ve yönetme
description: Bu öğreticide, Azure PowerShell kullanarak Azure’da Windows VM’leri oluşturup yönetmeyi öğrenirsiniz
author: cynthn
ms.service: virtual-machines
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: ae298562b5d3306cf6c20b4c2fb7b378ba99c986
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561628"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>Öğretici: Azure PowerShell ile Windows VM’leri Oluşturma ve Yönetme

Azure sanal makineleri tam olarak yapılandırılabilir ve esnek bir bilgi işlem ortamı sağlar. Bu öğretici sanal makine (VM) boyutu seçme, VM görüntüsü seçme ve VM dağıtma gibi temel Azure sanal makine dağıtımı görevlerini kapsar. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * VM oluşturma ve bir VM’ye bağlanma
> * VM görüntülerini seçme ve kullanma
> * Belirli VM boyutlarını görüntüleme ve kullanma
> * VM’yi yeniden boyutlandırma
> * VM durumunu görüntüleme ve anlama

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

## <a name="create-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutuyla bir kaynak grubu oluşturun.

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Bir sanal makineden önce bir kaynak grubu oluşturulmalıdır. Bu örnekte, *EastUS* bölgesinde *myResourceGroupVM* adlı bir kaynak grubu oluşturulur:

```azurepowershell-interactive
New-AzResourceGroup `
   -ResourceGroupName "myResourceGroupVM" `
   -Location "EastUS"
```

Kaynak grubu, bu öğretici boyunca görülebileceği gibi bir VM oluşturulurken veya değiştirilirken belirtilir.

## <a name="create-a-vm"></a>VM oluşturma

Bir VM oluştururken, işletim sistemi görüntüsü, ağ yapılandırması ve yönetici kimlik bilgileri gibi çeşitli seçenekler bulunur. Bu örnekte Windows Server 2016 Datacenter'ın varsayılan sürümünü çalıştıran *myVM* adlı bir VM oluşturulur.

[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) komutuyla VM’de yönetici hesabı için gereken kullanıcı adı ve parolasını ayarlayın:

```azurepowershell-interactive
$cred = Get-Credential
```

[New-azvm](/powershell/module/az.compute/new-azvm)ile VM 'yi oluşturun.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>VM’ye bağlanma

Dağıtım tamamlandıktan sonra VM ile bir uzak masaüstü bağlantısı oluşturun.

VM'nin genel IP adresini döndürmek için aşağıdaki komutları çalıştırın. Sonraki bir adımda web bağlantısını test etmek üzere tarayıcınızla bağlanabilmek için bu IP Adresini not alın.

```azurepowershell-interactive
Get-AzPublicIpAddress `
   -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

VM ile bir uzak masaüstü oturumu oluşturmak için yerel makinenizde aşağıdaki komutu kullanın. IP adresini, sanal makinenizin *publicIPAddress* değeriyle değiştirin. İstendiğinde, VM oluşturulurken kullanılan kimlik bilgilerini girin.

```powershell
mstsc /v:<publicIpAddress>
```

**Windows Güvenliği** penceresinde **Diğer seçenekler**'i ve ardından **Başka bir hesap kullanın**'ı seçin. VM için oluşturduğunuz kullanıcı adı ve parolayı yazıp **Tamam**’a tıklayın.

## <a name="understand-marketplace-images"></a>Market resimlerini anlama

Azure Market, yeni VM oluşturmak için kullanılabilecek çok sayıda görüntü içerir. Önceki adımlarda, Windows Server 2016 Datacenter görüntüsü kullanılarak bir VM oluşturuldu. Bu adımda, PowerShell modülü markette diğer Windows görüntülerini aramak için kullanılır, bu da yeni sanal makinelerin toplandığı yer olarak ayrıca kullanılabilir. Bu işlem, görüntüyü [tanımlamak](cli-ps-findimage.md#terminology) için yayımcının, teklifin, SKU’nun ve isteğe bağlı olarak bir sürüm numarasının bulunmasını kapsar.

Görüntü yayımcıları listesini döndürmek için [Get-Azvmımagepublisher](/powershell/module/az.compute/get-azvmimagepublisher) komutunu kullanın:

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

Görüntü tekliflerinin bir listesini döndürmek için [Get-Azvmımageteklifini](/powershell/module/az.compute/get-azvmimageoffer) kullanın. Bu komutla, döndürülen liste belirtilen `MicrosoftWindowsServer` adlı yayımcı üzerinde filtrelenir:

```azurepowershell-interactive
Get-AzVMImageOffer `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer"
```

Sonuçlar şu örneğe benzer olacaktır: 

```powershell
Offer             PublisherName          Location
-----             -------------          --------
Windows-HUB       MicrosoftWindowsServer EastUS
WindowsServer     MicrosoftWindowsServer EastUS
WindowsServer-HUB MicrosoftWindowsServer EastUS
```

[Get-Azvmımagesku](/powershell/module/az.compute/get-azvmimagesku) komutu, görüntü adlarının bir listesini döndürmek için yayımcı ve teklif adı üzerine filtreleyecek.

```azurepowershell-interactive
Get-AzVMImageSku `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer" `
   -Offer "WindowsServer"
```

Sonuçlar şu örneğe benzer olacaktır: 

```powershell
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Bu bilgiler, belirli bir görüntüye sahip bir VM’yi dağıtmak için kullanılabilir. Bu örnek, Kapsayıcılar’ı içeren Windows Server 2016 görüntüsünün en son sürümünü kullanarak VM'yi dağıtır.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

PowerShell komut istemlerinin size döndürülmesi için `-AsJob` parametresi VM’yi arka plan görevi olarak oluşturur. Arka plan işlerinin ayrıntılarını `Get-Job` cmdlet'i ile görüntüleyebilirsiniz.

## <a name="understand-vm-sizes"></a>VM boyutlarını anlama

VM boyutu, sanal makine için kullanılabilir hale getirilen CPU, GPU ve bellek gibi işlem kaynaklarının miktarını belirler. Sanal makinelerin iş yükü için uygun bir VM boyutu kullanılarak oluşturulması gerekir. İş yükü artarsa, mevcut sanal makine de yeniden boyutlandırılabilir.

### <a name="vm-sizes"></a>VM Boyutları

Aşağıdaki tabloda boyutlar kullanım durumlarına göre kategorilere ayrılmaktadır.  

| Tür                     | Ortak boyutlar           |    Açıklama       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Genel amaçlı](../sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| Dengeli CPU/bellek. Küçük ve orta ölçekli uygulama ve veri çözümlerini geliştirmek/test etmek için idealdir.  |
| [İşlem için iyileştirilmiş](../sizes-compute.md)   | Fsv2          | Yüksek CPU/bellek. Orta düzey trafiğe sahip uygulamalar, ağ gereçleri ve toplu işlemler için idealdir.        |
| [Bellek için iyileştirilmiş](../sizes-memory.md)    | Esv3, Ev3, d, DSv2, Dv2  | Yüksek bellek/çekirdek. İlişkisel veritabanı, orta veya büyük boyutlu önbellekler ve bellek içi analiz için idealdir.                 |
| [Depolama için iyileştirilmiş](../sizes-storage.md)      | Lsv2, ls              | Yüksek disk aktarım hızı ve GÇ. Büyük Veri, SQL ve NoSQL veritabanları için ideal.                                                         |
| [GPU](../sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | Ağır grafik işlemleri ile video düzenleme işlemleri için özel olarak hedeflenen VM’ler.       |
| [Yüksek performans](../sizes-hpc.md) | H        | İşleme düzeyi yüksek olan isteğe bağlı ağ arabirimleri (RDMA) içeren VM’lerimiz, şimdiye kadarki en güçlü CPU ile sunuluyor. |

### <a name="find-available-vm-sizes"></a>Kullanılabilir VM boyutlarını bulma

Belirli bir bölgede kullanılabilen VM boyutlarının listesini görmek için [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) komutunu kullanın.

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>VM’yi yeniden boyutlandırma

VM dağıtıldıktan sonra, kaynak ayırmayı artırmak veya azaltmak için yeniden boyutlandırılabilir.

Bir VM 'yi yeniden boyutlandırmadan önce, istediğiniz boyutun geçerli VM kümesinde kullanılabilir olup olmadığını denetleyin. [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) komutu boyutların bir listesini döndürür.

```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Boyut kullanılabiliyorsa, sanal makine, bir açık durumdan yeniden boyutlandırılabilir, ancak işlem sırasında yeniden başlatılır.

```azurepowershell-interactive
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_DS3_v2"
Update-AzVM `
   -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
```

İstediğiniz boyut geçerli kümede yoksa, yeniden boyutlandırma işleminin gerçekleşebilmesi için önce VM 'nin serbest olması gerekir. VM'nin serbest bırakılmasıyla geçici diskteki tüm veriler kaldırılır ve statik IP adresi kullanılmadığı sürece genel IP adresi değişir.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_E2s_v3"
Update-AzVM -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -Name $vm.name
```

## <a name="vm-power-states"></a>VM güç durumları

Bir Azure VM’si birçok güç durumuna sahip olabilir. 


| Güç Durumu | Description
|----|----|
| Başlatılıyor | Sanal makine başlatılıyor. |
| Çalışma | Sanal makine çalışıyor. |
| Durduruluyor | Sanal makine durduruluyor. |
| Durduruldu | VM durduruldu. Durduruldu durumundaki sanal makinelere bilgi işlem ücretleri uygulanmaya devam eder.  |
| Serbest bırakılıyor | VM serbest bırakılıyor. |
| Serbest bırakıldı | VM 'nin Hiper yöneticide kaldırıldığını ancak denetim düzleminde hala kullanılabilir olduğunu gösterir. `Deallocated` durumundaki sanal makinelere bilgi işlem ücretleri uygulanmaz. |
| - | VM 'nin güç durumu bilinmiyor. |


Belirli bir VM 'nin durumunu almak için [Get-AzVM](/powershell/module/az.compute/get-azvm) komutunu kullanın. VM ve kaynak grubu için geçerli bir ad belirttiğinizden emin olun.

```azurepowershell-interactive
Get-AzVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Çıkış şu örneğe benzer olacaktır:

```powershell
Status
------
PowerState/running
```

Aboneliğinizdeki tüm VM 'lerin güç durumunu almak için, [sanal makineler-tüm API](/rest/api/compute/virtualmachines/listall) 'Leri parametresi **statusonly** *true* olarak ayarlanmış şekilde kullanın.

## <a name="management-tasks"></a>Yönetim görevleri

Bir sanal makinenin yaşam döngüsü boyunca, bir sanal makineyi başlatma, durdurma veya silme gibi yönetim görevlerini çalıştırmak isteyebilirsiniz. Ayrıca, yinelenen veya karmaşık görevleri otomatikleştirmek için betikler oluşturmak isteyebilirsiniz. Azure PowerShell kullanarak, birçok ortak yönetim görevi komut satırından veya betikler içinde çalıştırılabilir.

### <a name="stop-a-vm"></a>VM durdurma

[Stop-azvm](/powershell/module/az.compute/stop-azvm)ile VM 'yi durdurun ve serbest bırakın:

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
```

VM'yi sağlanan bir durumda tutmak istiyorsanız, - StayProvisioned parametresini kullanın.

### <a name="start-a-vm"></a>VM başlatma

```azurepowershell-interactive
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM"
```

### <a name="delete-resource-group"></a>Kaynak grubunu silme

Kaynak grubunu sildiğinizde, kaynak grubunun içindeki her şey silinir.

```azurepowershell-interactive
Remove-AzResourceGroup `
   -Name "myResourceGroupVM" `
   -Force
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdakiler gibi temel VM oluşturma ve yönetim görevlerini öğrendiniz:

> [!div class="checklist"]
> * VM oluşturma ve bir VM’ye bağlanma
> * VM görüntülerini seçme ve kullanma
> * Belirli VM boyutlarını görüntüleme ve kullanma
> * VM’yi yeniden boyutlandırma
> * VM durumunu görüntüleme ve anlama

VM diskleri hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.  

> [!div class="nextstepaction"]
> [VM diskleri oluşturma ve yönetme](./tutorial-manage-data-disk.md)