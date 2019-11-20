---
title: Öğretici - Azure PowerShell ile bir ölçek kümesine uygulama yükleme | Microsoft Docs
description: Azure PowerShell kullanarak Özel Betik Uzantısı ile sanal makine ölçek kümelerine uygulama yükleme işleminin nasıl yapılacağını öğrenin
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/08/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6877ba6240806f3213cadc66fdc74d89b2e9ba31
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877981"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-azure-powershell"></a>Öğretici: Azure PowerShell ile sanal makine ölçek kümelerine uygulama yüklemesi

Bir ölçek kümesindeki sanal makine (VM) örneklerinde uygulamaları çalıştırmak için önce uygulama bileşenlerini ve gerekli dosyaları yüklemeniz gerekir. Önceki bir öğreticide, sanal makine örneklerinizi dağıtmak için nasıl özel sanal makine görüntüsü oluşturulacağını ve kullanılacağını öğrendiniz. Bu özel görüntüde, el ile uygulama yüklemeleri ve yapılandırmaları yer alıyordu. Her sanal makine örneği dağıtıldıktan sonra bir ölçek kümesine uygulamaların yüklenmesini otomatikleştirebilir veya önceden ölçek kümesinde çalıştırılan bir uygulamayı güncelleştirebilirsiniz. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Ölçek kümenize otomatik olarak uygulama yükleme
> * Azure Özel Betik Uzantısı’nı kullanma
> * Ölçek kümesinde çalıştırılan bir uygulamayı güncelleştirme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="what-is-the-azure-custom-script-extension"></a>Azure Özel Betik Uzantısı nedir?
Özel Betik Uzantısı, Azure VM’lerinde betik indirir ve yürütür. Bu uzantı dağıtım sonrası yapılandırma, yazılım yükleme veya diğer yapılandırma/yönetim görevleri için kullanışlıdır. Betikler Azure depolama veya GitHub konumlarından indirilebilir ya da Azure portalına uzantı çalışma zamanında iletilebilir.

Özel Betik uzantısı Azure Resource Manager şablonlarıyla tümleştirilir. Azure CLı, Azure PowerShell, Azure portal veya REST API ile de kullanılabilir. Daha fazla bilgi için bkz. [Özel Betik Uzantısı'na genel bakış](../virtual-machines/windows/extensions-customscript.md).

Özel Betik Uzantısı’nı çalışır halde görmek için, IIS web sunucusunu yükleyen ve ölçek kümesi sanal makine örneğinin ana bilgisayar adını veren bir ölçek kümesi oluşturun. Özel Betik Uzantısı tanımı, GitHub’dan bir örnek betiği indirir, gerekli paketleri yükler, sonra sanal makine örneği ana bilgisayar adını bir temel HTML sayfasına yazar.


## <a name="create-a-scale-set"></a>Ölçek kümesi oluşturma
Şimdi [New-AzVmss](/powershell/module/az.compute/new-azvmss)ile bir sanal makine ölçek kümesi oluşturun. Tek tek sanal makine örneklerine trafiği dağıtmak için bir yük dengeleyici de oluşturulur. Yük dengeleyici, TCP bağlantı noktası 80 ' deki trafiği dağıtmaya yönelik kurallar içerir. Ayrıca TCP bağlantı noktası 3389 üzerinde Uzak Masaüstü trafiğine ve TCP bağlantı noktası 5985 ' de PowerShell uzaktan iletişimine izin verir. İstendiğinde, ölçek kümesindeki sanal makine örnekleri için kendi yönetici kimlik bilgilerinizi ayarlayabilirsiniz:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

Tüm ölçek kümesi kaynaklarının ve VM'lerin oluşturulup yapılandırılması birkaç dakika sürer.


## <a name="create-custom-script-extension-definition"></a>Özel Betik Uzantısı tanımı oluşturma
Azure PowerShell, indirilecek dosyayı ve yürütülecek komutu depolamak için bir hashtable kullanır. Aşağıdaki örnekte, GitHub’dan örnek bir betik kullanılır. İlk olarak, aşağıdaki adımları uygulayarak bu yapılandırma nesnesini oluşturun:

```azurepowershell-interactive
$customConfig = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}
```


Şimdi, [Add-AzVmssExtension](/powershell/module/az.Compute/Add-azVmssExtension)Ile özel betik uzantısını uygulayın. Önceden tanımlanan yapılandırma nesnesi, uzantıya geçirilir. Uzantıyı Update [-AzVmss](/powershell/module/az.compute/update-azvmss)ile sanal makine örneklerinde güncelleştirin ve çalıştırın.


```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Ölçek kümesindeki her sanal makine örneği, GitHub’dan betiği indirip çalıştırır. Daha karmaşık bir örnekte, birden fazla uygulama bileşeni ve dosya yüklenebilir. Ölçek kümesinin ölçeği genişletilirse yeni sanal makine örnekleri otomatik olarak aynı Özel Betik Uzantısı tanımını uygular ve gerekli uygulamayı yükler.


## <a name="allow-traffic-to-application"></a>Uygulamaya giden trafiğe izin verme

Temel Web uygulamasına erişime izin vermek için, [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) ve [New-aznetworksecuritygroup](/powershell/module/az.network/new-aznetworksecuritygroup)ile bir ağ güvenlik grubu oluşturun. Daha fazla bilgi için bkz. [Azure sanal makine ölçek kümeleri Için ağ](virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroup" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName  "myResourceGroup" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzVirtualNetwork -VirtualNetwork $vnet

```



## <a name="test-your-scale-set"></a>Ölçek kümenizi test etme
Web sunucunuzu işlem içinde görmek için [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress)ile yük dengeleyicinizin genel IP adresini alın. Aşağıdaki örnek, *Myresourcegroup* kaynak grubunda oluşturulan IP adresini görüntüler:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Yük dengeleyicinin genel IP adresini bir web tarayıcısına girin. Aşağıdaki örnekte gösterildiği gibi yük dengeleyici trafiği VM örneklerinizden birine dağıtır:

![IIS’deki temel web sayfası](media/tutorial-install-apps-powershell/running-iis.png)

Sonraki adımda güncelleştirilmiş bir sürümü görebilmeniz için web tarayıcısını açık bırakın.


## <a name="update-app-deployment"></a>Uygulama dağıtımını güncelleştirme
Bir ölçek kümesinin yaşam döngüsü boyunca, uygulamanızın güncelleştirilmiş bir sürümünü dağıtmanız gerekebilir. Özel Betik Uzantısı ile, güncelleştirilmiş bir dağıtım betiğine başvurabilir ve sonra uzantıyı ölçek kümenize yeniden uygulayabilirsiniz. Önceki bir adımda ölçek kümesi oluşturulduğunda `-UpgradePolicyMode`, *Automatic* olarak ayarlandı. Bu ayar, ölçek kümesindeki sanal makine örneklerinin otomatik olarak güncelleştirilmesini ve uygulamanızın en son sürümünü uygulamasını sağlar.

*customConfigv2* adlı yeni bir yapılandırma tanımı oluşturun. Bu tanım, uygulama yükleme betiğinin güncelleştirilmiş bir *v2* sürümünü çalıştırır:

```azurepowershell-interactive
$customConfigv2 = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis-v2.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis-v2.ps1"
}
```

Özel Betik uzantısı yapılandırmasını ölçek kümesindeki sanal makine örneklerine güncelleştirin. Uygulamanın güncelleştirilmiş sürümünü uygulamak için *customConfigv2* tanımı kullanılır:

```azurepowershell-interactive
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"
 
$vmss.VirtualMachineProfile.ExtensionProfile[0].Extensions[0].Settings = $customConfigv2
 
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Ölçek kümesindeki tüm sanal makine örnekleri otomatik olarak örnek web sayfasının en son sürümüyle güncelleştirilir. Güncelleştirilmiş sürümü görmek için tarayıcınızda web sitesini yenileyin:

![IIS’deki güncelleştirilmiş web sayfası](media/tutorial-install-apps-powershell/running-iis-updated.png)


## <a name="clean-up-resources"></a>Kaynakları temizleme
Ölçek kümesini ve ek kaynaklarınızı kaldırmak için, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)komutunu kullanarak kaynak grubunu ve tüm kaynaklarını silin. `-Force` parametresi kaynakları ek bir komut istemi olmadan silmek istediğinizi onaylar. `-AsJob` parametresi işlemin tamamlanmasını beklemeden denetimi komut istemine döndürür.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure PowerShell ile ölçek kümenizde otomatik olarak uygulama yükleme ve güncelleştirme işleminin nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Ölçek kümenize otomatik olarak uygulama yükleme
> * Azure Özel Betik Uzantısı’nı kullanma
> * Ölçek kümesinde çalıştırılan bir uygulamayı güncelleştirme

Ölçek kümenizi otomatik olarak ölçeklendirme hakkında bilgi almak için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Ölçek kümelerinizi otomatik olarak ölçeklendirme](tutorial-autoscale-powershell.md)
