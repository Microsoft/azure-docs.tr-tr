---
title: Hızlandırılmış ağ ile Windows VM oluşturma-Azure PowerShell
description: Ağ performansını önemli ölçüde artırmak için hızlandırılmış ağ ile bir Windows sanal makinesi (VM) oluşturun.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: b0ebb75530858a589c3166e21261e2f737fff50d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919978"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>Azure PowerShell kullanarak hızlandırılmış ağ ile Windows sanal makinesi oluşturma

Bu öğreticide, hızlandırılmış ağ ile bir Windows sanal makinesi (VM) oluşturmayı öğreneceksiniz.

> [!NOTE]
> Bir Linux sanal makinesiyle hızlandırılmış ağ kullanmak için bkz. [hızlandırılmış ağ Ile LINUX VM oluşturma](create-vm-accelerated-networking-cli.md).

Hızlandırılmış ağ, bir VM'ye tek köklü G/Ç sanallaştırmasını (SR-IOV) etkinleştirerek VM'nin ağ performansını büyük ölçüde geliştirir. Bu yüksek performanslı yol, veri yolunda konağı atladığından, desteklenen sanal makine türlerinde üzerindeki en zorlu ağ iş yükleri için gecikme süresini ve CPU kullanımını azaltır. Aşağıdaki diyagramda, iki VM 'nin hızlandırılmış ağ iletişimi olmadan ve ile nasıl iletişim kurduğu gösterilmektedir:

![Hızlandırılmış ağ ile ve olmadan Azure sanal makineler arasında iletişim](./media/create-vm-accelerated-networking/accelerated-networking.png)

Hızlandırılmış ağ olmadan, VM 'deki ve olmayan tüm ağ trafiği ana bilgisayar ve sanal anahtar arasında gezinmelidir. Sanal anahtar ağ güvenlik grupları, erişim denetim listeleri, yalıtım ve ağ trafiğine diğer ağ sanallaştırılmış hizmetler gibi tüm ilke zorlamasına olanak sağlar.

> [!NOTE]
> Sanal anahtarlar hakkında daha fazla bilgi edinmek için bkz. [Hyper-V sanal anahtarı](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch).

Hızlandırılmış ağ ile ağ trafiği VM 'nin ağ arabirimine (NIC) ulaşır ve sonra VM 'ye iletilir. Sanal anahtarın geçerli olduğu tüm ağ ilkeleri artık boşaltılmış ve donanımda uygulandı. İlke donanımda uygulandığından, NIC ağ trafiğini doğrudan VM 'ye iletebilir. NIC konak ve sanal anahtarı atlar, ancak konakta uygulanan tüm ilkeyi korur.

Hızlandırılmış ağ avantajları yalnızca etkin olduğu VM için geçerlidir. En iyi sonuçlar için, bu özelliği aynı Azure sanal ağına bağlı en az iki VM üzerinde etkinleştirin. Sanal ağlar arasında iletişim kurarken veya şirket içinde bağlantı kurarken, bu özelliğin genel gecikme süresine en az etkisi vardır.

## <a name="benefits"></a>Avantajlar

- **Saniye başına düşük gecikme süresi/daha yüksek paketler (PPS)**: veri yolundan sanal anahtarı ortadan kaldırmak, ilke işleme için konakta harcadıkları zaman paketlerini kaldırır. Ayrıca sanal makine içinde işlenebilecek paketlerin sayısını artırır.

- **Azaltılan değişim**: sanal anahtar işleme uygulanması gereken ilke miktarına bağlıdır. Ayrıca, işleme yapan CPU iş yüküne bağlıdır. İlke zorlamayı donanıma devreetmek, paketleri doğrudan VM 'ye teslim ederek bu değişkenliği kaldırır. Yük boşaltma aynı zamanda konaktan VM iletişimini, tüm yazılım kesmelerini ve tüm bağlam anahtarlarını da kaldırır.

- **AZALTıLMıŞ CPU kullanımı**: konaktaki sanal anahtarı atlamak, ağ trafiğini işlemek için daha az CPU kullanımına neden oluyor.

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Aşağıdaki dağıtımlar doğrudan Azure galerisinden desteklenir:

- **Windows Server 2019 Datacenter**
- **Windows Server 2016 Datacenter** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>Sınırlamalar ve kısıtlamalar

### <a name="supported-vm-instances"></a>Desteklenen VM örnekleri

Hızlandırılmış ağ, 2 veya daha fazla vCPU ile en genel amaçlı ve işlem için iyileştirilmiş örnek boyutlarında desteklenir. Hiper iş parçacığı destekleyen örneklerde, hızlandırılmış ağ, 4 veya daha fazla vCPU içeren VM örneklerinde desteklenir. 

Hızlandırılmış ağ desteği ayrı [sanal makine boyutları](../virtual-machines/sizes.md) belgelerinde bulunabilir. 

### <a name="custom-images"></a>Özel görüntüler

Özel bir görüntü kullanıyorsanız ve görüntünüz hızlandırılmış ağı destekliyorsa, Azure 'da Mellanox ConnectX-3 ve ConnectX-4 LX NIC 'lerle çalışan gerekli sürücülere sahip olduğunuzdan emin olun.

### <a name="regions"></a>Bölgeler

Hızlandırılmış ağ, tüm genel Azure bölgelerinde ve Azure Kamu bulutunda kullanılabilir.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Çalışan bir VM 'de hızlandırılmış ağı etkinleştirme

Hızlandırılmış ağ etkin olmayan desteklenen bir VM boyutu, yalnızca durdurulduğunda ve serbest bırakıldığında özelliği etkin hale getirebilirsiniz.

### <a name="deployment-through-azure-resource-manager"></a>Azure Resource Manager üzerinden dağıtım

Sanal makineler (klasik) hızlandırılmış ağlarla dağıtılamaz.

## <a name="vm-creation-using-the-portal"></a>Portalı kullanarak VM oluşturma

Bu makalede, Azure PowerShell kullanarak hızlandırılmış ağ içeren bir VM oluşturma adımları sunulmaktadır; ancak, hızlandırılmış ağ 'ı sağlayan [bir sanal makine oluşturmak için Azure Portal de kullanabilirsiniz](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Portalda bir VM oluşturduğunuzda, **sanal makine oluştur** sayfasında **ağ** sekmesini seçin. Bu sekmede **hızlandırılmış ağ** için bir seçenek bulunur. [Desteklenen bir işletim sistemi](#supported-operating-systems) ve [VM boyutu](#supported-vm-instances)seçtiyseniz, bu seçenek otomatik olarak **Açık** olarak ayarlanır. Aksi takdirde, seçenek **kapalı** olarak ayarlanır ve Azure 'un etkinleştirilmesinin nedenini görüntüler.

> [!NOTE]
> Portal aracılığıyla yalnızca desteklenen işletim sistemleri etkinleştirilebilir. Özel bir görüntü kullanıyorsanız ve görüntünüz hızlandırılmış ağı destekliyorsa, lütfen CLı veya PowerShell kullanarak VM 'nizi oluşturun. 

VM 'yi oluşturduktan sonra hızlandırılmış ağ oluşturma özelliğinin etkinleştirilip etkinleştirilmediğini doğrulayabilirsiniz. Şu yönergeleri izleyin:

1. VM 'lerinizi yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Sanal makineleri** arayın ve seçin.

2. Sanal makine listesinde yeni VM 'nizi seçin.

3. VM menü çubuğunda **ağ iletişimi**' ni seçin.

Ağ arabirimi bilgilerinde, **hızlandırılmış ağ** etiketinin yanındaki Portal, hızlandırılmış ağ durumu Için **devre dışı** ya da **etkin** olarak görüntülenir.

## <a name="vm-creation-using-powershell"></a>PowerShell kullanarak VM oluşturma

Devam etmeden önce [Azure PowerShell](/powershell/azure/install-az-ps) Version 1.0.0 veya üstünü yükleyebilirsiniz. Yüklü olan sürümünüzü bulmak için çalıştırın `Get-Module -ListAvailable Az` . Yüklemeniz veya yükseltmeniz gerekiyorsa, [PowerShell Galerisi](https://www.powershellgallery.com/packages/Az)az modülünün en son sürümünü yüklemeniz gerekir. PowerShell oturumunda, [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)komutunu kullanarak bir Azure hesabında oturum açın.

Aşağıdaki örneklerde, örnek parametre adlarını kendi değerlerinizle değiştirin. *Myresourcegroup*, *MYNIC* ve *myvm* dahil olmak üzere örnek parametre adları.

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

1. [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)ile bir kaynak grubu oluşturun. Aşağıdaki komut, tek *merkezde ABD* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig)ile bir alt ağ yapılandırması oluşturun. Aşağıdaki komut *Mysubnet* adlı bir alt ağ oluşturur:

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. *Mysubnet* alt ağıyla [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)ile bir sanal ağ oluşturun.

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

1. [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig)ile bir ağ güvenlik grubu kuralı oluşturun.

    ```azurepowershell
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

2. [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) ile bir ağ güvenlik grubu oluşturun ve buna *ızın ver-RDP-All* Güvenlik kuralını atayın. *İzin-RDP-All* kuralıyla, ağ güvenlik grubu birçok varsayılan kural içerir. Bir varsayılan kural, internet 'ten gelen tüm erişimi devre dışı bırakır. Oluşturulduktan sonra, sanal makineye uzaktan bağlanabilmek için, ağ güvenlik grubuna *Izin ver-RDP-All* kuralı atanır.

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. Ağ güvenlik grubunu [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig)Ile *mysubnet* alt ağıyla ilişkilendirin. Ağ güvenlik grubundaki kural, alt ağda dağıtılan tüm kaynaklar için geçerlidir.

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Hızlandırılmış ağ ile bir ağ arabirimi oluşturma

1. [New-Azpublicıpaddress](/powershell/module/az.Network/New-azPublicIpAddress)ile genel bir IP adresi oluşturun. VM 'ye internet 'ten erişmeyi planlamıyorsanız genel IP adresi gereksizdir. Ancak, bu makaledeki adımları tamamlaması gerekir.

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. Hızlandırılmış ağ özellikli [New-Aznetworkınterface](/powershell/module/az.Network/New-azNetworkInterface) ile bir ağ arabirimi oluşturun ve genel IP adresini ağ arabirimine atayın. Aşağıdaki örnekte, *Myvnet* sanal ağının *mysubnet* alt ağında *MYNIC* adlı bir ağ arabirimi oluşturulur ve *mypublicıp* genel IP adresi buna atanıyor:

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>VM oluşturma ve ağ arabirimini iliştirme

1. VM kimlik bilgilerinizi `$cred` [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)kullanarak değişkene ayarlayın, bu, oturum açmanızı ister:

    ```azurepowershell
    $cred = Get-Credential
    ```

2. [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)ile VM 'nizi tanımlayın. Aşağıdaki komut, hızlandırılmış ağı destekleyen bir VM boyutu ile *Myvm* adlı bir VM 'yi tanımlar (*Standard_DS4_v2*):

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    Tüm VM boyutları ve özelliklerinin bir listesi için bkz. [WINDOWS VM boyutları](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) ve [set-Azvmsourceımage](/powershell/module/az.compute/set-azvmsourceimage)ile VM yapılandırmanızın geri kalanını oluşturun. Aşağıdaki komut bir Windows Server 2016 VM oluşturur:

    ```azurepowershell
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

4. Daha önce [Add-Azvmnetworkınterface](/powershell/module/az.compute/add-azvmnetworkinterface)ile oluşturduğunuz ağ arabirimini ekleyin:

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. [New-azvm](/powershell/module/az.compute/new-azvm)ile VM 'nizi oluşturun.

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>Windows VM 'de Ethernet Denetleyicisi 'nin yüklü olduğunu onaylayın

VM 'yi Azure 'da oluşturduktan sonra VM 'ye bağlanın ve Ethernet denetleyicisinin Windows 'a yüklendiğini onaylayın.

1. VM 'lerinizi yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Sanal makineleri** arayın ve seçin.

2. Sanal makine listesinde yeni VM 'nizi seçin.

3. VM 'ye Genel Bakış sayfasında, sanal makinenin **durumu** **oluşturma** olarak listeleniyorsa, Azure VM oluşturmayı bitirene kadar bekleyin. VM  oluşturma işlemi tamamlandıktan sonra durum **çalışıyor** olarak değiştirilir.

4. VM 'ye genel bakış araç çubuğundan **Bağlan**  >  **RDP**  >  **indirme RDP dosyasını** seçin.

5. . Rdp dosyasını açın ve ardından VM 'de [sanal makine oluşturma ve ağ arabirimi iliştirme](#create-a-vm-and-attach-the-network-interface) bölümüne girdiğiniz kimlik bilgileriyle oturum açın. Azure 'da bir Windows sanal makinesine hiç bağlanmadıysanız, bkz. [sanal makineye bağlanma](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).

6. VM 'niz için Uzak Masaüstü oturumu görüntülendikten sonra, Windows Başlat düğmesine sağ tıklayın ve **Device Manager**' yi seçin.

7. **Device Manager** penceresinde, **ağ bağdaştırıcıları** düğümünü genişletin.

8. Aşağıdaki görüntüde gösterildiği gibi, **Mellanox ConnectX-3 sanal Işlevi Ethernet bağdaştırıcısının** göründüğünü onaylayın:

    ![Mellanox ConnectX-3 sanal Işlevi Ethernet bağdaştırıcısı, hızlandırılmış ağ için yeni ağ bağdaştırıcısı, Device Manager](./media/create-vm-accelerated-networking/device-manager.png)

Hızlandırılmış ağ, VM 'niz için artık etkinleştirilmiştir.

> [!NOTE]
> Mellanox bağdaştırıcısı başlatılamazsa, Uzak Masaüstü oturumunda bir yönetici istemi açın ve aşağıdaki komutu girin:
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>Mevcut VM 'lerde hızlandırılmış ağı etkinleştir

Hızlandırılmış ağ olmadan bir VM oluşturduysanız, mevcut bir VM 'de bu özelliği etkinleştirebilirsiniz. VM, yukarıda da özetlenen aşağıdaki önkoşulları karşılaarak hızlandırılmış ağı desteklemelidir:

* Sanal makine, hızlandırılmış ağ için desteklenen bir boyut olmalıdır.
* VM, desteklenen bir Azure Galeri görüntüsü (ve Linux için çekirdek sürümü) olmalıdır.
* Herhangi bir NIC üzerinde hızlandırılmış ağı etkinleştirmeden önce bir kullanılabilirlik kümesindeki veya bir sanal makine ölçek kümesindeki tüm VM 'Ler durdurulmalı veya serbest bırakılmalıdır.

### <a name="individual-vms-and-vms-in-an-availability-set"></a>Bir kullanılabilirlik kümesindeki ayrı VM 'Ler ve VM 'Ler

1. VM 'yi durdurun veya serbest bırakın ya da bir kullanılabilirlik kümesi ise, kümesindeki tüm VM 'Ler:

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > Bir VM 'yi tek tek oluşturduğunuzda, kullanılabilirlik kümesi olmadan tek tek VM 'yi yalnızca hızlandırılmış ağı etkinleştirmek üzere durdurmanız veya serbest bırakmak yeterlidir. VM 'niz bir kullanılabilirlik kümesi ile oluşturulduysa, sanal makinelerin hızlandırılmış ağı destekleyen bir kümeye bitmesi için, NIC 'lerden hiçbirinde hızlandırılmış ağı etkinleştirmeden önce kullanılabilirlik kümesindeki tüm VM 'Leri durdurmanız veya serbest bırakmak gerekir. Hızlandırılmış ağ 'ı destekleyen kümeler, hızlandırılmış ağı destekleyen NIC 'ler de daha iyi çalışır olduğundan, hızlandırılmış ağı devre dışı bırakırsanız, durdurma veya serbest bırakma gereksinimi gereksizdir.

2. SANAL makinenizin NIC 'inde hızlandırılmış ağı etkinleştirin:

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. VM 'nizi veya bir kullanılabilirlik kümesinde, küme içindeki tüm VM 'Leri yeniden başlatın ve hızlandırılmış ağın etkin olduğunu onaylayın:

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>Sanal makine ölçek kümesi

Bir sanal makine ölçek kümesi biraz farklıdır, ancak aynı iş akışını izler.

1. VM 'Leri durdurun:

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. Ağ arabirimi altındaki hızlandırılmış ağ özelliğini güncelleştirin:

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. Değişikliklerin hemen çekilmesi için uygulanan güncelleştirmeleri otomatik olarak ayarlayın:

    ```azurepowershell
    $vmss.UpgradePolicy.Mode = "Automatic"
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > Ölçek kümesi, güncelleştirmeleri üç farklı ayar kullanarak uygulayan VM yükseltmeleri içerir: otomatik, sıralı ve el ile. Bu yönergelerde, ilke otomatik olarak ayarlanır, böylece ölçek kümesi yeniden başladıktan sonra değişiklikleri hemen alır.

4. Ölçek kümesini yeniden başlatın:

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

Yeniden başlattıktan sonra, yükseltmelerin bitmesini bekleyin. Yükseltmeler yapıldıktan sonra sanal işlev (VF) VM içinde görünür. Desteklenen bir işletim sistemi ve VM boyutu kullandığınızdan emin olun.

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Hızlandırılmış ağ ile mevcut VM 'Leri yeniden boyutlandırma

Bir sanal makinede hızlandırılmış ağ etkinse, yalnızca hızlandırılmış ağı destekleyen bir VM 'ye yeniden boyutlandırabilirsiniz.  

Hızlandırılmış ağ etkin bir VM, yeniden boyutlandırma işlemi kullanılarak hızlandırılmış ağı desteklemeyen bir VM örneğine yeniden boyutlandırılamaz. Bunun yerine, bu VM 'lerden birini yeniden boyutlandırmak için:

1. VM 'yi durdurun veya serbest bırakın. Kullanılabilirlik kümesi veya ölçek kümesi için kullanılabilirlik kümesi veya ölçek kümesindeki tüm VM 'Leri durdurun veya serbest bırakın.

2. VM 'nin NIC 'inde hızlandırılmış ağı devre dışı bırakın. Kullanılabilirlik kümesi veya ölçek kümesi için kullanılabilirlik kümesi veya ölçek kümesi içindeki tüm VM 'lerin NIC 'lerinde hızlandırılmış ağı devre dışı bırakın.

3. Hızlandırılmış ağı devre dışı bıraktıktan sonra, VM, kullanılabilirlik kümesi veya ölçek kümesini, hızlandırılmış ağı desteklemeyen yeni bir boyuta taşıyın ve yeniden başlatın.