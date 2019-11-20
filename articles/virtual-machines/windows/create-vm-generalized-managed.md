---
title: Azure 'da yönetilen görüntüden VM oluşturma
description: Kaynak Yöneticisi dağıtım modelinde Azure PowerShell veya Azure portal kullanarak genelleştirilmiş bir yönetilen görüntüden Windows sanal makinesi oluşturun.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 17100769bac2e7af08e0bcba6ca2eb129b621013
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037520"
---
# <a name="create-a-vm-from-a-managed-image"></a>Yönetilen görüntüden VM oluşturma

Azure portal veya PowerShell 'i kullanarak Azure yönetilen bir VM görüntüsünden birden çok sanal makine (VM) oluşturabilirsiniz. Yönetilen bir VM görüntüsü, işletim sistemi ve veri diskleri dahil olmak üzere VM oluşturmak için gereken bilgileri içerir. Görüntüyü oluşturan sanal sabit diskler (VHD), hem işletim sistemi diskleri hem de tüm veri diskleri dahil, yönetilen diskler olarak depolanır. 

Yeni bir VM oluşturmadan önce, kaynak görüntü olarak kullanmak için [yönetilen BIR VM görüntüsü oluşturmanız](capture-image-resource.md) ve görüntüde erişimi olması gereken herhangi bir kullanıcıya görüntü üzerinde okuma erişimi vermeniz gerekir. 


## <a name="use-the-portal"></a>Portalı kullanma

1. [Azure portalı](https://portal.azure.com) açın.
2. Sol taraftaki menüden **tüm kaynaklar**' ı seçin. Görüntülerinizi kolayca bulmak için kaynakları **türe** göre sıralayabilirsiniz.
3. Listeden kullanmak istediğiniz görüntüyü seçin. Görüntüye **genel bakış** sayfası açılır.
4. Menüden **VM oluştur** ' u seçin.
5. Sanal makine bilgilerini girin. Buraya girilen Kullanıcı adı ve parola, sanal makinede oturum açmak için kullanılacaktır. Tamamlandığında **Tamam**' ı seçin. Yeni VM 'yi mevcut bir kaynak grubunda oluşturabilir veya yeni **Oluştur** ' u seçerek VM 'yi depolayacak yeni bir kaynak grubu oluşturabilirsiniz.
6. VM için bir boyut seçin. Daha fazla boyut görmek için **Tümünü görüntüle**’yi seçin veya **Desteklenen disk türü** filtresini değiştirin. 
7. **Ayarlar**' ın altında, gerekli değişiklikleri yapın ve **Tamam**' ı seçin. 
8. Özet sayfasında, görüntü adınızın **özel bir görüntü**olarak listelendiğini görmeniz gerekir. Sanal makine dağıtımını başlatmak için **Tamam ' ı** seçin.


## <a name="use-powershell"></a>PowerShell kullanma

[New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet 'i için Basitleştirilmiş parametre kümesini kullanarak BIR görüntüden VM oluşturmak için PowerShell 'i kullanabilirsiniz. Görüntünün, VM 'yi oluşturacağınız kaynak grubunda olması gerekir.

 

[New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) için ayarlanan Basitleştirilmiş parametre yalnızca BIR görüntüden VM oluşturmak için bir ad, kaynak grubu ve görüntü adı sağlamanızı gerektirir. New-AzVm, otomatik olarak oluşturduğu tüm kaynakların adı olarak **-Name** parametresinin değerini kullanacaktır. Bu örnekte, her bir kaynak için daha ayrıntılı adlar sağlıyoruz, ancak cmdlet 'in onları otomatik olarak oluşturmasına izin veririz. Ayrıca, daha önce sanal ağ gibi kaynaklar oluşturabilir ve kaynak adını cmdlet 'e geçirebilirsiniz. Yeni-AzVm, adları adlarıyla bulabilmeleri durumunda mevcut kaynakları kullanacaktır.

Aşağıdaki örnek, *Myresourcegroup* kaynak grubunda, *MyImage*adlı görüntüden *myvmfromımage*adlı bir VM oluşturur. 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```



## <a name="next-steps"></a>Sonraki adımlar
[Azure PowerShell modülü ile Windows VM 'Leri oluşturma ve yönetme](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

