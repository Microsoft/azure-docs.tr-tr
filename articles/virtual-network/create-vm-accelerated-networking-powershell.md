---
title: Hızlandırılmış ağ ile Azure VM oluşturma-Azure PowerShell
description: Hızlandırılmış ağ ile Linux sanal makinesi oluşturmayı öğrenin.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: 16837782af2f08e27363091dc21587a100194cd8
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083697"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking-using-azure-powershell"></a>Azure PowerShell kullanarak hızlandırılmış ağlarla Windows sanal makinesi oluşturma

Bu öğreticide, hızlandırılmış ağ ile bir Windows sanal makinesi (VM) oluşturmayı öğreneceksiniz. Hızlandırılmış ağ ile bir Linux VM oluşturmak için bkz. [hızlandırılmış ağ Ile LINUX VM oluşturma](create-vm-accelerated-networking-cli.md). Hızlandırılmış ağ, bir VM 'ye tek köklü g/ç Sanallaştırması (SR-ıOV) sağlar ve ağ performansını büyük ölçüde geliştirir. Bu yüksek performanslı yol, desteklenen VM türlerinde en zorlu ağ iş yükleri ile kullanım için, gecikme süresi, değişim ve CPU kullanımını azaltan ana bilgisayarı veri yolundan atlar. Aşağıdaki resimde, hızlandırılmış ağ ile ve olmadan iki VM arasındaki iletişim gösterilmektedir:

![Karşılaştırma](./media/create-vm-accelerated-networking/accelerated-networking.png)

Hızlandırılmış ağ olmadan, VM 'deki ve olmayan tüm ağ trafiği ana bilgisayar ve sanal anahtar arasında gezinmelidir. Sanal anahtar ağ güvenlik grupları, erişim denetim listeleri, yalıtım ve ağ trafiğine diğer ağ sanallaştırılmış hizmetler gibi tüm ilke zorlamasına olanak sağlar. Sanal anahtarlar hakkında daha fazla bilgi edinmek için bkz. [Hyper-V ağ sanallaştırma ve sanal anahtar](https://technet.microsoft.com/library/jj945275.aspx).

Hızlandırılmış ağ ile ağ trafiği VM 'nin ağ arabirimine (NIC) ulaşır ve ardından VM 'ye iletilir. Sanal anahtarın geçerli olduğu tüm ağ ilkeleri artık boşaltılmış ve donanımda uygulandı. İlkeyi donanıma uygulamak, NIC 'nin ağ trafiğini konak ve sanal anahtarı atlayarak, ana bilgisayara uyguladığı tüm ilkeyi koruyarak doğrudan VM 'ye iletmesini sağlar.

Hızlandırılmış ağ avantajları yalnızca üzerinde etkin olduğu VM için geçerlidir. En iyi sonuçlar için, bu özelliğin aynı Azure sanal ağına (VNet) bağlı en az iki VM üzerinde etkinleştirilmesi idealdir. VNET 'lerde iletişim kurarken veya şirket içinde bağlantı kurarken, bu özelliğin genel gecikme süresine en az etkisi vardır.

## <a name="benefits"></a>Avantajlar
* **Saniye başına düşük gecikme süresi/daha yüksek paketler (PPS):** Sanal anahtarın veri yolundan kaldırılması, ilke işleme için konakta harcadıkları zaman paketlerini kaldırır ve sanal makıne içinde işlenebilecek paketlerin sayısını artırır.
* **Azaltılan değişim:** Sanal anahtar işleme, uygulanması gereken ilke miktarına ve işleme yapan CPU 'nun iş yüküne bağlıdır. İlke zorlamayı donanıma devreetmek, paketleri doğrudan VM 'ye teslim ederek bu değişkenliği, Konağı VM iletişimine ve tüm yazılım kesintileri ve bağlam anahtarlarına kaldırarak bu değişkenliği kaldırır.
* **AZALTıLMıŞ CPU kullanımı:** Konaktaki sanal anahtarı atlamak, ağ trafiğini işlemeye yönelik daha az CPU kullanımına neden oluyor.

## <a name="limitations-and-constraints"></a>Sınırlamalar ve kısıtlamalar

### <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri
Aşağıdaki dağıtımlar Azure galerisindeki kutudan çıkar:
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**
* **Windows Server 2019 Datacenter**

### <a name="supported-vm-instances"></a>Desteklenen VM örnekleri
Hızlandırılmış ağ, 2 veya daha fazla vCPU ile en genel amaçlı ve işlem için iyileştirilmiş örnek boyutlarında desteklenir.  Desteklenen bu seriler şunlardır: D/DSv2 ve F/FS

Hiper iş parçacığı destekleyen örneklerde, hızlandırılmış ağ, 4 veya daha fazla vCPU içeren VM örneklerinde desteklenir. Desteklenen Seriler: D/Dsv3, E/Esv3, Fsv2, Lsv2, MS/MMS ve MS/Mmsv2.

VM örnekleri hakkında daha fazla bilgi için bkz. [WINDOWS VM boyutları](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Bölgeler
Tüm genel Azure bölgelerinde ve Azure Kamu bulutunda kullanılabilir.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Çalışan bir VM 'de hızlandırılmış ağı etkinleştirme
Hızlandırılmış ağ etkin olmayan desteklenen bir VM boyutu, yalnızca durdurulduğunda ve serbest bırakıldığında özelliği etkin hale getirebilirsiniz.

### <a name="deployment-through-azure-resource-manager"></a>Azure Resource Manager üzerinden dağıtım
Sanal makineler (klasik) hızlandırılmış ağlarla dağıtılamaz.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Azure hızlandırılmış ağ ile Windows VM oluşturma
## <a name="portal-creation"></a>Portal oluşturma
Bu makalede, Azure PowerShell kullanarak hızlandırılmış ağ içeren bir sanal makine oluşturma adımları sunulmaktadır, ancak [Azure Portal kullanarak hızlandırılmış ağ ile bir sanal makine da oluşturabilirsiniz](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Portalda bir sanal makine oluştururken, **sanal makine oluştur** dikey penceresinde **ağ** sekmesini seçin.  Bu sekmede, **hızlandırılmış ağ**için bir seçenek vardır.  [Desteklenen bir işletim sistemi](#supported-operating-systems) ve [VM boyutu](#supported-vm-instances)seçtiyseniz, bu seçenek otomatik olarak "açık" olarak doldurulur.  Aksi takdirde, hızlandırılmış ağ için "kapalı" seçeneğini doldurur ve kullanıcıya neden etkinleştirilmemiş bir neden olur.   
* *Note:* Portal aracılığıyla yalnızca desteklenen işletim sistemleri etkinleştirilebilir.  Özel bir görüntü kullanıyorsanız ve görüntünüz hızlandırılmış ağı destekliyorsa, lütfen CLı veya PowerShell kullanarak VM 'nizi oluşturun. 

Sanal makine oluşturulduktan sonra, hızlandırılmış ağ oluşturma özelliğinin etkinleştirildiğini onaylama ' daki yönergeleri izleyerek hızlandırılmış ağın etkinleştirildiğini doğrulayabilirsiniz.

## <a name="powershell-creation"></a>PowerShell oluşturma
## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/install-az-ps) sürüm 1.0.0 veya üstünü yükler. Yüklü olan sürümünüzü bulmak için `Get-Module -ListAvailable Az`çalıştırın. Yüklemeniz veya yükseltmeniz gerekiyorsa, [PowerShell Galerisi](https://www.powershellgallery.com/packages/Az)az modülünün en son sürümünü yüklemeniz gerekir. PowerShell oturumunda [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)komutunu kullanarak bir Azure hesabında oturum açın.

Aşağıdaki örneklerde, örnek parametre adlarını kendi değerlerinizle değiştirin. *Myresourcegroup*, *MYNIC*ve *myvm*dahil olmak üzere örnek parametre adları.

[New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)ile bir kaynak grubu oluşturun. Aşağıdaki örnek, *merkezileştirme* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

İlk olarak, [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig)ile bir alt ağ yapılandırması oluşturun. Aşağıdaki örnek, *mysubnet*adlı bir alt ağ oluşturur:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

*Mysubnet* alt ağıyla [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)ile bir sanal ağ oluşturun.

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

İlk olarak, [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig)ile bir ağ güvenlik grubu kuralı oluşturun.

```powershell
$rdp = New-AzNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

[New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) ile bir ağ güvenlik grubu oluşturun ve buna *ızın ver-RDP-All* Güvenlik kuralını atayın. Ağ güvenlik grubu, *Izin ver-RDP-All* kuralına ek olarak çeşitli varsayılan kurallar içerir. Tek bir varsayılan kural, Internet 'ten gelen tüm erişimi devre dışı bırakır. bu nedenle, sanal makineye, oluşturulduktan sonra uzaktan bağlanabilmek için *Izin ver-RDP-All* kuralının ağ güvenlik grubuna atanmasına neden olur.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Ağ güvenlik grubunu [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig)Ile *mysubnet* alt ağıyla ilişkilendirin. Ağ güvenlik grubundaki kural, alt ağda dağıtılan tüm kaynaklar için geçerlidir.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Hızlandırılmış ağ ile bir ağ arabirimi oluşturma
[New-Azpublicıpaddress](/powershell/module/az.Network/New-azPublicIpAddress)ile genel bir IP adresi oluşturun. Sanal makineye Internet 'ten erişmeyi planlamıyorsanız genel IP adresi gerekli değildir, ancak bu makaledeki adımları tamamlayabilmeniz için gereklidir.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Hızlandırılmış ağ özellikli [New-Aznetworkınterface](/powershell/module/az.Network/New-azNetworkInterface) ile bir ağ arabirimi oluşturun ve genel IP adresini ağ arabirimine atayın. Aşağıdaki örnek, *Myvnet* sanal ağının *mysubnet* alt ağında *MYNIC* adlı bir ağ arabirimi oluşturur ve *mypublicıp* genel IP adresini buna atar:

```powershell
$nic = New-AzNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Sanal makineyi oluşturma

VM kimlik bilgilerinizi, [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)kullanarak `$cred` değişkenine ayarlayın:

```powershell
$cred = Get-Credential
```

İlk olarak, [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)ile VM 'nizi tanımlayın. Aşağıdaki örnek, hızlandırılmış ağı destekleyen bir VM boyutu ile *Myvm* adlı bir VM 'yi tanımlar (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Tüm VM boyutları ve özelliklerinin bir listesi için bkz. [WINDOWS VM boyutları](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

[Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) ve [set-Azvmsourceımage](/powershell/module/az.compute/set-azvmsourceimage)ile VM yapılandırmanızın geri kalanını oluşturun. Aşağıdaki örnek bir Windows Server 2016 VM oluşturur:

```powershell
$vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

Daha önce [Add-Azvmnetworkınterface](/powershell/module/az.compute/add-azvmnetworkinterface)ile oluşturduğunuz ağ arabirimini ekleyin:

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Son olarak, [Yeni-azvm](/powershell/module/az.compute/new-azvm)ile VM 'nizi oluşturun:

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Sürücünün işletim sisteminde yüklü olduğunu onaylayın

VM 'yi Azure 'da oluşturduktan sonra VM 'ye bağlanın ve sürücünün Windows 'da yüklü olduğunu doğrulayın.

1. Bir Internet tarayıcısından Azure [portalını](https://portal.azure.com) açın ve Azure hesabınızla oturum açın.
2. Azure portal en üstünde bulunan metin *arama kaynaklarını* Içeren kutuya *myvm*yazın. **Myvm** , arama sonuçlarında göründüğünde, öğesine tıklayın. **Oluşturma** , **Bağlan** düğmesi altında görünür durumdaysa, Azure henüz VM 'yi oluşturmayı tamamlamadı. Genel Bakış **' ın sol** üst köşesinde bulunan **Bağlan** ' a tıklayın.
3. [Sanal makineyi oluştur](#create-the-virtual-machine)alanına girdiğiniz kullanıcı adını ve parolayı girin. Azure 'da bir Windows sanal makinesine hiç bağlanmadıysanız, bkz. [sanal makineye bağlanma](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Windows Başlat düğmesine sağ tıklayın ve **Aygıt Yöneticisi**' ye tıklayın. **Ağ bağdaştırıcıları** düğümünü genişletin. Aşağıdaki resimde gösterildiği gibi, **Mellanox ConnectX-3 sanal Işlevi Ethernet bağdaştırıcısının** göründüğünü onaylayın:

    ![Aygıt Yöneticisi](./media/create-vm-accelerated-networking/device-manager.png)

Hızlandırılmış ağ, VM 'niz için artık etkinleştirilmiştir.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Mevcut VM 'lerde hızlandırılmış ağı etkinleştir
Hızlandırılmış ağ olmadan bir VM oluşturduysanız, bu özelliği var olan bir VM 'de etkinleştirmek mümkündür.  Yukarıda da özetlenen aşağıdaki önkoşulları izleyerek VM 'nin hızlandırılmış ağı desteklemesi gerekir:

* Sanal makine hızlandırılmış ağ için desteklenen bir boyut olmalıdır
* VM, desteklenen bir Azure Galeri görüntüsü (ve Linux için çekirdek sürümü) olmalıdır
* Herhangi bir NIC üzerinde hızlandırılmış ağ etkinleştirilmeden önce bir kullanılabilirlik kümesindeki veya VMSS 'deki tüm sanal makinelerin durdurulması/serbest bırakılmasının gerekir

### <a name="individual-vms--vms-in-an-availability-set"></a>Bir kullanılabilirlik kümesindeki VM 'Ler & bireysel VM 'Ler
İlk olarak VM 'yi durdurun/serbest bırakın veya bir kullanılabilirlik kümesi ise, kümesindeki tüm VM 'Ler:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Önemli bir deyişle, VM 'niz tek tek oluşturulduysa, bir kullanılabilirlik kümesi olmadan, yalnızca hızlandırılmış ağı etkinleştirmek için tek bir VM 'yi durdurmanız/serbest getirmeniz gerektiğini lütfen unutmayın.  VM 'niz bir kullanılabilirlik kümesi ile oluşturulduysa, NIC 'lerden hiçbirinde hızlandırılmış ağı etkinleştirmeden önce kullanılabilirlik kümesinde bulunan tüm VM 'Lerin durdurulması/serbest bırakılmasının gerekir. 

Durdurulduktan sonra, sanal makinenizin NIC 'inde hızlandırılmış ağı etkinleştirin:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

VM 'nizi veya bir kullanılabilirlik kümesinde, küme içindeki tüm VM 'Leri yeniden başlatın ve hızlandırılmış ağın etkin olduğunu onaylayın:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS biraz farklıdır, ancak aynı iş akışına uyar.  İlk olarak, VM 'Leri durdurun:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

VM 'Ler durdurulduğunda, ağ arabirimi altındaki hızlandırılmış ağ özelliğini güncelleştirin:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Lütfen bir VMSS 'nin üç farklı ayar, otomatik, sıralı ve el ile güncelleştirme uygulayan VM yükseltmeleri olduğunu unutmayın.  Bu yönergelerde, VMSS 'nin değişiklikleri yeniden başlattıktan hemen sonra alması için ilke otomatik olarak ayarlanır.  Değişikliklerin hemen çekilmesi için otomatik olarak ayarlamak için:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Son olarak, VMSS 'yi yeniden başlatın:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Yeniden başlattıktan sonra, yükseltmelerin bitmesini bekleyin, ancak tamamlandığında, sanal makine içinde VF görüntülenir.  (Lütfen desteklenen bir işletim sistemi ve VM boyutu kullandığınızdan emin olun)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Hızlandırılmış ağ ile mevcut VM 'Leri yeniden boyutlandırma

Hızlandırılmış ağ etkin olan sanal makineler yalnızca hızlandırılmış ağı destekleyen VM 'lere yeniden boyutlandırılabilir.  

Hızlandırılmış ağ özellikli bir VM, yeniden boyutlandırma işlemi kullanılarak hızlandırılmış ağı desteklemeyen bir VM örneğine yeniden boyutlandırılamaz.  Bunun yerine, bu VM 'lerden birini yeniden boyutlandırmak için:

* VM 'yi durdurma/serbest bırakma veya bir kullanılabilirlik kümesinde/VMSS 'de, set/VMSS içindeki tüm VM 'Leri durdur/serbest bırak.
* Hızlandırılmış ağ, VM 'nin NIC 'inde veya bir kullanılabilirlik kümesinde/VMSS 'de, küme/VMSS 'de bulunan tüm VM 'lerde devre dışı bırakılmalıdır.
* Hızlandırılmış ağ devre dışı bırakıldığında, VM/kullanılabilirlik kümesi/VMSS, hızlandırılmış ağı desteklemeyen ve yeniden başlatılan yeni bir boyuta taşınabilir.
