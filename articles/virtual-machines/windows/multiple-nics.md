---
title: Azure 'da birden çok NIC kullanan Windows VM 'Leri oluşturma ve yönetme
description: Azure PowerShell veya Kaynak Yöneticisi şablonlarını kullanarak, birden fazla NIC 'e bağlı bir Windows sanal makinesi oluşturmayı ve yönetmeyi öğrenin.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 0e826442c816f83c875b907bbf3054793ebb382a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033141"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Birden çok NIC içeren bir Windows sanal makinesi oluşturma ve yönetme
Azure 'daki sanal makinelere (VM 'Ler), birden çok sanal ağ arabirim kartı (NIC) eklenmiş olabilir. Yaygın bir senaryo, ön uç ve arka uç bağlantısı için farklı alt ağlara sahip olur. Bir VM 'de birden çok NIC 'yi birden çok alt ağa ilişkilendirebilirsiniz, ancak bu alt ağların hepsi aynı sanal ağda (vNet) bulunmalıdır. Bu makalede, birden fazla NIC 'ye eklenmiş bir VM oluşturma işlemi açıklanır. Ayrıca, mevcut bir VM 'ye NIC ekleme veya kaldırma hakkında bilgi edinebilirsiniz. Farklı [VM boyutları](sizes.md) değişen sayıda NIC destekler, bu nedenle VM 'nizi uygun şekilde boyutlandırın.

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki örneklerde, örnek parametre adlarını kendi değerlerinizle değiştirin. Örnek parametre adları *Myresourcegroup*, *Myvnet*ve *myvm*' i içerir.

 

## <a name="create-a-vm-with-multiple-nics"></a>Birden çok NIC ile VM oluşturma
İlk olarak bir kaynak grubu oluşturun. Aşağıdaki örnek *EastUs* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Sanal ağ ve alt ağlar oluşturma
Yaygın bir senaryo, sanal ağın iki veya daha fazla alt ağa sahip olması içindir. Diğeri arka uç trafiği için ön uç trafiği için bir alt ağ olabilir. Her iki alt ağa bağlanmak için VM 'niz üzerinde birden çok NIC kullanırsınız.

1. [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig)ile iki sanal ağ alt ağı tanımlayın. Aşağıdaki örnek, *Mysubnetön uç* ve *mysubnetarka ucu*için alt ağları tanımlar:

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork)ile Sanal ağınızı ve alt ağlarınızı oluşturun. Aşağıdaki örnek, *Myvnet*adlı bir sanal ağ oluşturur:

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Birden çok NIC oluşturma
[New-Aznetworkınterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface)Ile iki NIC oluşturun. Ön uç alt ağına bir NIC ve arka uç alt ağına bir NIC ekleyin. Aşağıdaki örnek *myNic1* ve *MyNic2*adlı NIC 'leri oluşturur:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Genellikle, ağ trafiğini VM 'ye filtreleyecek bir [ağ güvenlik grubu](../../virtual-network/security-overview.md) ve trafiği birden çok VM arasında dağıtmak için bir [yük dengeleyici](../../load-balancer/load-balancer-overview.md) de oluşturursunuz.

### <a name="create-the-virtual-machine"></a>Sanal makineyi oluşturma
Şimdi VM yapılandırmanızı oluşturmaya başlayın. Her VM boyutunun, bir VM 'ye ekleyebileceğiniz toplam NIC sayısı sınırı vardır. Daha fazla bilgi için bkz. [WINDOWS VM boyutları](sizes.md).

1. VM kimlik bilgilerinizi `$cred` değişkenine aşağıda gösterildiği gibi ayarlayın:

    ```powershell
    $cred = Get-Credential
    ```

2. [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig)ile VM 'nizi tanımlayın. Aşağıdaki örnek, *myvm* ADLı bir VM 'yi tanımlar ve Ikiden fazla NIC 'yi destekleyen bir VM boyutu kullanır (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) ve [set-Azvmsourceımage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage)ile VM yapılandırmanızın geri kalanını oluşturun. Aşağıdaki örnek bir Windows Server 2016 VM oluşturur:

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

4. [Add-Azvmnetworkınterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface)ile daha önce oluşturduğunuz iki NIC 'i bağlayın:

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. [New-azvm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)ile VM 'nizi oluşturun:

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. [İşletim sistemini birden çok NIC Için yapılandırma](#configure-guest-os-for-multiple-nics)bölümündeki adımları tamamlayarak, ikincil NIC 'ler için yönlendirme ekleyin.

## <a name="add-a-nic-to-an-existing-vm"></a>Mevcut bir VM 'ye bir NIC ekleme
Var olan bir VM 'ye sanal bir NIC eklemek için VM 'yi serbest bırakın, sanal NIC 'yi ekleyin ve ardından VM 'yi başlatın. Farklı [VM boyutları](sizes.md) değişen sayıda NIC destekler, bu nedenle VM 'nizi uygun şekilde boyutlandırın. Gerekirse, [bir VM 'yi yeniden boyutlandırabilirsiniz](resize-vm.md).

1. [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm)ile VM 'yi serbest bırakın. Aşağıdaki örnek *Myresourcegroup*Içindeki *MYVM* adlı VM 'yi kaldırır:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. [Get-azvm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)ile VM 'nin var olan yapılandırmasını alın. Aşağıdaki örnek, *Myresourcegroup*Içinde *MYVM* adlı VM 'nin bilgilerini alır:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Aşağıdaki örnek, *Mysubnetarka uca*iliştirilmiş *myNic3* adlı [New-aznetworkınterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) ile bir sanal NIC oluşturur. Sanal NIC daha sonra *Myresourcegroup* ' de [Add-Azvmnetworkınterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface)Ile *myvm* adlı VM 'ye eklenir:

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzVMNetworkInterface -VM $vm -Id $nicId | Update-AzVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Birincil sanal NIC 'ler
    Birden çok NIC VM üzerindeki NIC 'lerden birinin birincil olması gerekir. VM 'deki mevcut sanal NIC 'lerden biri zaten birincil olarak ayarlandıysa, bu adımı atlayabilirsiniz. Aşağıdaki örnek, bir VM 'de Şu anda iki sanal NIC 'nin bulunduğunu ve ilk NIC 'yi (`[0]`) birincil olarak eklemek istediğinizi varsayar:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. [Start-AzVm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm)ile VM 'yi başlatın:

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. [İşletim sistemini birden çok NIC Için yapılandırma](#configure-guest-os-for-multiple-nics)bölümündeki adımları tamamlayarak, ikincil NIC 'ler için yönlendirme ekleyin.

## <a name="remove-a-nic-from-an-existing-vm"></a>Mevcut bir VM 'den bir NIC 'yi kaldırma
Bir sanal NIC 'yi var olan bir VM 'den kaldırmak için VM 'yi serbest bırakın, sanal NIC 'yi kaldırın ve ardından VM 'yi başlatın.

1. [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm)ile VM 'yi serbest bırakın. Aşağıdaki örnek *Myresourcegroup*Içindeki *MYVM* adlı VM 'yi kaldırır:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. [Get-azvm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)ile VM 'nin var olan yapılandırmasını alın. Aşağıdaki örnek, *Myresourcegroup*Içinde *MYVM* adlı VM 'nin bilgilerini alır:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. [Get-Aznetworkınterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface)ile NIC kaldırma hakkında bilgi alın. Aşağıdaki örnek *myNic3*hakkında bilgi alır:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. [Remove-Azvmnetworkınterface](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmnetworkinterface) ile NIC 'i kaldırın ve ardından VM 'yi [Update-azvm](https://docs.microsoft.com/powershell/module/az.compute/update-azvm)ile güncelleştirin. Aşağıdaki örnek, önceki adımda `$nicId` tarafından alınan *myNic3* kaldırır:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. [Start-AzVm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm)ile VM 'yi başlatın:

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Şablonlarla birden çok NIC oluşturma
Azure Resource Manager şablonlar, dağıtım sırasında birden çok NIC oluşturma gibi bir kaynağın birden çok örneğini oluşturmak için bir yol sağlar. Kaynak Yöneticisi şablonlar, ortamınızı tanımlamak için bildirim temelli JSON dosyalarını kullanır. Daha fazla bilgi için bkz. [Azure Resource Manager genel bakış](../../azure-resource-manager/resource-group-overview.md). Oluşturulacak örnek sayısını belirtmek için *Kopyala* ' yı kullanabilirsiniz:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Daha fazla bilgi için, bkz. [ *Copy*kullanarak birden çok örnek oluşturma](../../resource-group-create-multiple.md). 

Bir kaynak adına bir sayı eklemek için `copyIndex()` de kullanabilirsiniz. Daha sonra *myNic1*, *MyNic2* vb. oluşturabilirsiniz. Aşağıdaki kodda dizin değeri ekleme örneği gösterilmektedir:

```json
"name": "[concat('myNic', copyIndex())]", 
```

[Kaynak Yöneticisi şablonları kullanarak birden çok NIC oluşturmaya](../../virtual-network/template-samples.md)ilişkin bir örnek bulabilirsiniz.

[İşletim sistemini birden çok NIC Için yapılandırma](#configure-guest-os-for-multiple-nics)bölümündeki adımları tamamlayarak, ikincil NIC 'ler için yönlendirme ekleyin.

## <a name="configure-guest-os-for-multiple-nics"></a>Konuk işletim sistemini birden çok NIC için yapılandırma

Azure, sanal makineye bağlı ilk (birincil) ağ arabirimine bir varsayılan ağ geçidi atar. Azure, bir sanal makineye bağlı ek (ikincil) ağ arabirimlerine varsayılan ağ geçidi atamaz. Bu nedenle varsayılan olarak, alt ağın dışında kalan ve ikincil bir ağ arabirimi içeren kaynaklarla iletişim kurulamaz. Ancak, ağ alt ağı dışındaki kaynaklarla iletişim kurabilir, ancak iletişim etkinleştirme adımları farklı işletim sistemleri için farklı olabilir.

1. Bir Windows komut isteminden, iki bağlı ağ arabirimine sahip bir sanal makine için aşağıdaki çıktıya benzer bir çıktı döndüren `route print` komutunu çalıştırın:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    Bu örnekte, **Microsoft Hyper-V ağ bağdaştırıcısı #4** (arabirim 7), kendisine atanmış bir varsayılan ağ geçidine sahip olmayan ikincil ağ arabirimidir.

2. Bir komut isteminden, ikincil ağ arabirimine hangi IP adresinin atandığını görmek için `ipconfig` komutunu çalıştırın. Bu örnekte, 192.168.2.4 Interface 7 ' ye atanır. İkincil ağ arabirimi için varsayılan ağ geçidi adresi döndürülmez.

3. İkincil ağ arabiriminin alt ağı dışındaki adreslere giden tüm trafiği alt ağ için ağ geçidine yönlendirmek üzere aşağıdaki komutu çalıştırın:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Alt ağ için ağ geçidi adresi, alt ağ için tanımlanan adres aralığındaki ilk IP adresidir (1 ' de sona eriyor). Tüm trafiği alt ağ dışında yönlendirmek istemiyorsanız, bunun yerine belirli hedeflere tek tek yollar ekleyebilirsiniz. Örneğin, yalnızca ikincil ağ arabiriminden gelen trafiği 192.168.3.0 ağına yönlendirmek istiyorsanız şu komutu girin:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Örneğin, 192.168.3.0 ağındaki bir kaynakla başarılı iletişimi onaylamak için, "interface 7 (192.168.2.4) kullanarak 192.168.3.4 ping için aşağıdaki komutu girin:

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Aşağıdaki komutla, ping yaptığınız cihazın Windows Güvenlik Duvarı üzerinden ıCMP 'yi açmanız gerekebilir:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Eklenen yolun yol tablosunda olduğunu doğrulamak için, aşağıdaki metne benzer olan çıktıyı döndüren `route print` komutunu girin:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    **Ağ geçidi**altında *192.168.1.1* ile listelenen yol, birincil ağ arabirimi için varsayılan olarak bulunan yoldur. **Ağ geçidi**altında *192.168.2.1* içeren yol, eklediğiniz yoldur.

## <a name="next-steps"></a>Sonraki adımlar
Birden çok NIC içeren bir VM oluşturmaya çalışırken [WINDOWS VM boyutlarını](sizes.md) gözden geçirin. Her VM boyutunun desteklediği en fazla NIC sayısına dikkat edin. 


