---
title: Öğretici-Azure 'da Windows VM 'ye uygulama yüklemesi
description: Bu öğreticide, Azure’da betikler çalıştırmak ve Windows sanal makinelerine uygulamalar dağıtmak için Özel Betik Uzantısı’nı kullanmayı öğreneceksiniz
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c7c0fc8aaa3779390060ae283f1427f4955ed421
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555984"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Öğretici - Azure’da Özel Betik Uzantısı ile bir Windows sanal makinesine uygulama dağıtma

Sanal makineleri (VM 'Ler) hızlı ve tutarlı bir şekilde yapılandırmak için [Windows Için özel Betik uzantısı](../extensions/custom-script-windows.md)'nı kullanabilirsiniz. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * IIS yüklemek için Özel Betik Uzantısı kullanma
> * Özel Betik Uzantısı kullanan bir VM oluşturma
> * Uzantı uygulandıktan sonra çalışan bir IIS sitesi görüntüleme

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

## <a name="custom-script-extension-overview"></a>Özel betik uzantısına genel bakış
Özel Betik Uzantısı, Azure VM’lerinde betik indirir ve yürütür. Bu uzantı dağıtım sonrası yapılandırma, yazılım yükleme veya diğer yapılandırma/yönetim görevleri için kullanışlıdır. Betikler Azure depolama veya GitHub konumlarından indirilebilir ya da Azure portalına uzantı çalışma zamanında iletilebilir.

Özel Betik uzantısı, Azure Resource Manager şablonları ile tümleşir ve Azure CLI, PowerShell, Azure portalı veya Azure Sanal Makine REST API'si kullanılarak da çalıştırılabilir.

Özel Betik Uzantısı’nı, Windows ve Linux VM'ler ile kullanabilirsiniz.


## <a name="create-virtual-machine"></a>Sanal makine oluşturma
VM için yönetici kullanıcı adını ve parolasını [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)ile ayarlayın:

```azurepowershell-interactive
$cred = Get-Credential
```

Artık [New-azvm](/powershell/module/az.compute/new-azvm)ile VM oluşturabilirsiniz. Aşağıdaki örnekte *EastUS* konumunda *myVM* adlı bir VM oluşturulur. Zaten mevcut değilse, *myResourceGroupAutomate* kaynak grubu ve destekleyici ağ kaynakları oluşturulur. Web trafiğine izin vermek için, cmdlet ayrıca *80* numaralı bağlantı noktasını açar.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Kaynakların ve sanal makinenin oluşturulması birkaç dakika sürer.


## <a name="automate-iis-install"></a>IIS yüklemeyi otomatikleştirme
Özel Betik uzantısını yüklemek için [set-Azvmexgerkomutunu](/powershell/module/az.compute/set-azvmextension) kullanın. Uzantı, IIS web sunucusunu yüklemek için `powershell Add-WindowsFeature Web-Server` komutunu çalıştırır ve ardından VM’nin ana bilgisayar adını göstermek için *Default.htm* sayfasını güncelleştirir:

```azurepowershell-interactive
Set-AzVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Web sitesini test etme
[Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress)ile yük dengeleyicinizin genel IP adresini alın. Aşağıdaki örnek, daha önce oluşturulan *myPublicIPAddress* için IP adresini alır:

```azurepowershell-interactive
Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

Daha sonra genel IP adresini bir web tarayıcısına girebilirsiniz. Aşağıdaki örnekteki gibi yük dengeleyicinin trafiği dağıttığı VM’nin ana bilgisayar adının dahil olduğu web sitesi görüntülenir:

![Çalışan IIS web sitesi](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir VM’de IIS yüklemesini otomatikleştirdiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * IIS yüklemek için Özel Betik Uzantısı kullanma
> * Özel Betik Uzantısı kullanan bir VM oluşturma
> * Uzantı uygulandıktan sonra çalışan bir IIS sitesi görüntüleme

Özel VM görüntülerinin nasıl oluşturulacağını öğrenmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Özel VM görüntüleri oluşturma](./tutorial-custom-images.md)
