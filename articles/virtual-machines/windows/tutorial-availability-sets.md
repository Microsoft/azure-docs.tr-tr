---
title: Öğretici-Azure 'da Windows VM 'Leri için yüksek kullanılabilirlik
description: Bu öğreticide, Kullanılabilirlik Kümelerinde yüksek oranda kullanılabilir sanal makineler dağıtmak için Azure PowerShell kullanmayı öğreneceksiniz
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0f94f4d312cefec80a0f294e256ee1ad908b903c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74068127"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Öğretici: Azure PowerShell ile yüksek oranda kullanılabilir sanal makineler oluşturma ve dağıtma

Bu öğreticide, kullanılabilirlik kümelerini kullanarak sanal makinelerinizin (VM) kullanılabilirliğini ve güvenilirliğini nasıl artıracağınızı öğreneceksiniz. Kullanılabilirlik kümeleri, Azure 'da dağıttığınız VM 'Lerin bir kümede birden çok, yalıtılmış donanım düğümüne dağıtıldığından emin olun. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Kullanılabilirlik kümesi oluşturma
> * Kullanılabilirlik kümesinde sanal makine oluşturma
> * Kullanılabilir sanal makine boyutlarını denetleme
> * Azure Danışmanı’nı denetleme


## <a name="availability-set-overview"></a>Kullanılabilirlik kümesine genel bakış

Kullanılabilirlik kümesi, dağıtılan VM kaynaklarını birbirinden yalıtmak için bir mantıksal gruplama özelliğidir. Azure, bir kullanılabilirlik kümesi içinde yerleştirdiğiniz VM 'Lerin birden çok fiziksel sunucuda, bilgi işlem raflarında, depolama birimlerinde ve ağ anahtarlarında çalıştığından emin olmanızı sağlar. Bir donanım veya yazılım hatası oluşursa, sanal makinelerinizin yalnızca bir alt kümesi etkilenir ve genel çözümünüz çalışır durumda kalır. Kullanılabilirlik kümeleri, güvenilir bulut çözümleri oluşturmak için gereklidir.

Dört adet ön uç web sunucusuna ve iki adet arka uç sanal makineye sahip olabileceğiniz tipik bir sanal makine tabanlı çözümü düşünelim. Azure ile, sanal makinelerinizi dağıtmadan önce iki kullanılabilirlik kümesi tanımlamak istersiniz: bir Web katmanı ve diğeri arka katman için. Yeni bir VM oluşturduğunuzda, kullanılabilirlik kümesini parametre olarak belirtirsiniz. Azure, VM 'Lerin birden fazla fiziksel donanım kaynağı arasında yalıtılmasını sağlar. Sunucularınızdaki bir fiziksel donanımda bir sorun varsa, farklı donanımlarda olduklarından sunucularınızın diğer örneklerinin çalışmaya devam edecek olduğunu bilirsiniz.

Azure’da güvenilir sanal makine tabanlı çözümleri dağıtmak istediğinizde Kullanılabilirlik Kümelerini kullanın.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell'i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. İsterseniz [https://shell.azure.com/powershell](https://shell.azure.com/powershell) adresine giderek Cloud Shell'i ayrı bir tarayıcı sekmesinde de başlatabilirsiniz. **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

## <a name="create-an-availability-set"></a>Kullanılabilirlik kümesi oluşturma

Konumdaki donanım, birden çok etki alanına ve hata etki alanına bölünmüştür. **Güncelleştirme etki alanı**, aynı anda yeniden başlatılabilen bir VM grubu ve temel alınan fiziksel donanımdır. Aynı **hata etki alanında** bulunan VM’ler, ortak güç kaynağı ve ağ anahtarıyla birlikte ortak depolama alanını paylaşır.  

[New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset)kullanarak bir kullanılabilirlik kümesi oluşturabilirsiniz. Bu örnekte, hem güncelleştirme hem de hata etki alanlarının sayısı *2* ' dir ve kullanılabilirlik kümesi *myAvailabilitySet*olarak adlandırılır.

Bir kaynak grubu oluşturun.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

`-sku aligned` parametresiyle [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset) kullanarak yönetilen bir kullanılabilirlik kümesi oluşturun.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Kullanılabilirlik kümesi içinde sanal makineler oluşturma
Donanım genelinde doğru şekilde dağıtıldığından emin olmak için VM 'Lerin kullanılabilirlik kümesi içinde oluşturulması gerekir. Mevcut bir VM oluşturulduktan sonra bir kullanılabilirlik kümesine ekleyemezsiniz. 


[New-azvm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)Ile bir VM oluşturduğunuzda, kullanılabilirlik kümesinin adını belirtmek için `-AvailabilitySetName` parametresini kullanırsınız.

İlk olarak, VM için [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) ile bir yönetici kullanıcı adı ve parola ayarlayın:

```azurepowershell-interactive
$cred = Get-Credential
```

Şimdi kullanılabilirlik kümesinde [New-azvm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) Ile iki VM oluşturun.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

İki VM’yi de oluşturup yapılandırmak birkaç dakika sürer. Tamamlandığında, temel alınan donanım arasında dağıtılmış iki sanal makineye sahip olursunuz. 

**MyResourceGroupAvailability** > **MyAvailabilitySet** > **kaynak gruplarına** giderek portaldaki kullanılabilirlik kümesine bakarsanız, VM 'lerin iki hata ve güncelleştirme etki alanı arasında nasıl dağıtıldığını görmeniz gerekir.

![Portaldaki kullanılabilirlik kümesi](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Kullanılabilir sanal makine boyutlarını denetleme 

Daha sonra kullanılabilirlik kümesine daha fazla sanal makine ekleyebilirsiniz; ancak donanımda hangi sanal makine boyutlarının kullanılabilir olduğunu bilmeniz gerekir. Kullanılabilirlik kümesi için donanım kümesindeki tüm kullanılabilir boyutları listelemek için [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) komutunu kullanın.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Azure Danışmanı’nı denetleme 

Ayrıca, sanal makinelerinizin kullanılabilirliğini geliştirme hakkında daha fazla bilgi edinmek için Azure Danışmanı 'nı kullanabilirsiniz. Azure Advisor yapılandırma ve kullanım telemetrinizi analiz ederek Azure kaynaklarınızın maliyet verimliliğini, performansını, kullanılabilirliğini ve güvenliğini artırmanıza yardımcı olabilecek çözümler önerir.

[Azure portal](https://portal.azure.com)’ında oturum açın, **Tüm hizmetler**’i seçin ve **Danışman** yazın. Danışman panosu, seçili abonelik için kişiselleştirilmiş önerileri gösterir. Daha fazla bilgi için bkz. [Azure Danışmanı’nı kullanmaya başlayın](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Kullanılabilirlik kümesi oluşturma
> * Kullanılabilirlik kümesinde sanal makine oluşturma
> * Kullanılabilir sanal makine boyutlarını denetleme
> * Azure Danışmanı’nı denetleme

Sanal makine ölçek kümeleri hakkında daha fazla bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [VM ölçek kümesi oluşturma](tutorial-create-vmss.md)


