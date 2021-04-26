---
title: Hızlı başlangıç-Azure PowerShell bir Windows sanal makinesi oluşturma
description: Bu hızlı başlangıçta Azure PowerShell’i kullanarak Windows sanal makinesi oluşturmayı öğrenirsiniz
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 78b912dd649ff942e0187f9b3602d9213383b8c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560744"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>Hızlı Başlangıç: PowerShell ile Azure'da Windows sanal makinesi oluşturma

Azure PowerShell modülü, PowerShell komut satırından veya betik içinden Azure kaynakları oluşturmak ve yönetmek için kullanılır. Bu hızlı başlangıçta Azure PowerShell modülü kullanarak Azure’da Windows Server 2016 çalıştıran bir sanal makinenin (VM) nasıl dağıtılacağı gösterilir. Ayrıca VM 'ye yönelik RDP ve IIS Web sunucusunu yükleyerek VM 'nin eylemde görünmesini sağlayabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.


## <a name="create-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)Ile bir Azure Kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>Sanal makine oluşturma

[New-azvm](/powershell/module/az.compute/new-azvm)Ile bir VM oluşturun. Her bir kaynak için ad sağlayın ve `New-AzVM` cmdlet henüz yoksa, oluşturur.

İstendiğinde, sanal makine için oturum açma kimlik bilgileri olarak kullanılacak bir Kullanıcı adı ve parola girin:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389
```

## <a name="connect-to-virtual-machine"></a>Sanal makineye bağlanma

Dağıtım tamamlandıktan sonra RDP VM'ye bağlanır. VM'nizin çalışmasını görmek için, IIS web sunucusu yüklenir.

VM 'nin genel IP adresini görmek için [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) cmdlet 'ini kullanın:

```powershell
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Yerel bilgisayarınızdan bir uzak masaüstü oturumu oluşturmak için aşağıdaki komutu kullanın. IP adresini, sanal makinenizin genel IP adresi ile değiştirin. 

```powershell
mstsc /v:publicIpAddress
```

**Windows Güvenliği** penceresinde **Daha fazla seçenek** ve ardından **Farklı bir hesap kullanın** seçeneklerini belirleyin. Kullanıcı adını **localhost** \\ *Kullanıcı adı* olarak yazın, sanal makine için oluşturduğunuz parolayı girin ve ardından **Tamam**' a tıklayın.

Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. **Evet** ' e tıklayın veya bağlantıyı oluşturmak Için **devam edin**

## <a name="install-web-server"></a>Web sunucusunu yükleme

Sanal makinenizin çalıştığını görmek için IIS web sunucusunu yükleyin. VM’de bir PowerShell istemi açın ve şu komutu çalıştırın:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

İşiniz bittiğinde, RDP’nin sanal makine bağlantısını kapatın.

## <a name="view-the-web-server-in-action"></a>Web sunucusunun çalıştığını görme

Sanal makinenizde İnternet’ten IIS yüklenmiş ve 80 numaralı bağlantı noktası açık olduğunda, varsayılan IIS karşılama sayfasını görüntülemek için tercih ettiğiniz bir web tarayıcısını kullanın. VM’nizin önceki bir adımda edinilen genel IP adresini kullanın. Aşağıdaki örnekte varsayılan IIS web sitesi gösterilir:

![Varsayılan IIS sitesi](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet 'ini kullanarak kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırabilirsiniz:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, basit bir sanal makine dağıttınız, web trafiği için bir ağ bağlantı noktası açtınız ve temel bir web sunucusu yüklediniz. Azure sanal makineleri hakkında daha fazla bilgi için Windows VM’lerine yönelik öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Windows sanal makine öğreticileri](./tutorial-manage-vm.md)
