---
title: Azure sanal makineleri için ortak PowerShell komutları
description: Azure 'da Windows VM 'lerinizi oluşturmaya ve yönetmeye başlamanızı sağlamak için ortak PowerShell komutları.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: e61d0840f66dad2e1bf91512281d9171771f7ca9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032901"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Azure sanal makineleri oluşturmak ve yönetmek için kullanılan genel PowerShell komutları

Bu makalede, Azure aboneliğinizdeki sanal makineleri oluşturmak ve yönetmek için kullanabileceğiniz Azure PowerShell komutlarının bazıları ele alınmaktadır.  Belirli komut satırı anahtarları ve seçenekleriyle ilgili daha ayrıntılı yardım için **Get-Help** *komutunu*kullanabilirsiniz.

 

Bu değişkenler, bu makaledeki komutlardan birden fazlasını çalıştırdığınızda sizin için yararlı olabilir:

- $location-sanal makinenin konumu. Sizin için uygun bir [coğrafi bölge](https://azure.microsoft.com/regions/) bulmak için [Get-azlocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) ' i kullanabilirsiniz.
- $myResourceGroup-sanal makineyi içeren kaynak grubunun adı.
- $myVM-sanal makinenin adı.

## <a name="create-a-vm---simplified"></a>VM oluşturma-Basitleştirilmiş

| Görev | Komut |
| ---- | ------- |
| Basit VM oluşturma | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -adı $myVM <BR></BR><BR></BR> New-AzVM, hepsi gereken tek bir ad olduğu *Basitleştirilmiş* parametreler kümesine sahiptir. -Name değeri, yeni bir VM oluşturmak için gereken tüm kaynakların adı olarak kullanılacaktır. Daha fazlasını belirtebilirsiniz, ancak bu gereklidir.|
| Özel görüntüden VM oluşturma | New-AzVm-ResourceGroupName $myResourceGroup-adı $myVM ImageName "MyImage"-Location $location  <BR></BR><BR></BR>Kendi [yönetilen görüntünüzü](capture-image-resource.md)zaten oluşturmuş olmanız gerekir. Birden çok ve özdeş VM oluşturmak için bir görüntü kullanabilirsiniz. |



## <a name="create-a-vm-configuration"></a>VM yapılandırması oluşturma

| Görev | Komut |
| ---- | ------- |
| VM yapılandırması oluşturma |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -vmname $MyVM-vmsize "Standard_D1_v1"<BR></BR><BR></BR>VM yapılandırması VM ayarlarını tanımlamak veya güncelleştirmek için kullanılır. Yapılandırma, VM adı ve [boyutu](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ile başlatılır. |
| Yapılandırma ayarları ekle |$vm = [set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $VM-Windows-ComputerName $MyVM-Credential $cred-ProvisionVMAgent-enableotomatik güncelleştirme<BR></BR><BR></BR>Yeni-AzVMConfig kullanarak daha önce oluşturduğunuz yapılandırma nesnesine [kimlik bilgileri](https://technet.microsoft.com/library/hh849815.aspx) de dahil olmak üzere işletim sistemi ayarları eklenir. |
| Ağ arabirimi ekleme |$vm = [Add-Azvmnetworkınterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $vm kimliği $Nic. Numarasını<BR></BR><BR></BR>Bir VM 'nin bir sanal ağda iletişim kurmak için bir [ağ arabirimi](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) olmalıdır. Ayrıca, var olan bir ağ arabirimi nesnesini almak için [Get-Aznetworkınterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) ' i de kullanabilirsiniz. |
| Platform görüntüsü belirtme |$vm = [set-Azvmsourceımage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $VM-publishername "publisher_name"-teklif "publisher_offer"-sku "product_sku"-sürüm "en son"<BR></BR><BR></BR>Yeni-AzVMConfig kullanarak daha önce oluşturduğunuz yapılandırma nesnesine [görüntü bilgileri](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eklenir. Bu komuttan döndürülen nesne yalnızca bir platform görüntüsü kullanmak için işletim sistemi diski ayarladığınızda kullanılır. |
| VM oluşturma |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -resourcegroupname $MyResourceGroup-location $LOCATION-VM $VM<BR></BR><BR></BR>Tüm kaynaklar bir [kaynak grubunda](../../azure-resource-manager/manage-resource-groups-powershell.md)oluşturulur. Bu komutu çalıştırmadan önce New-AzVMConfig, set-AzVMOperatingSystem, set-Azvmsourceımage, Add-Azvmnetworkınterface ve set-AzVMOSDisk komutunu çalıştırın. |
| VM güncelleştirme |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -resourcegroupname $MYRESOURCEGROUP-VM $VM<BR></BR><BR></BR>Get-AzVM kullanarak geçerli VM yapılandırmasını alın, VM nesnesindeki yapılandırma ayarlarını değiştirin ve ardından bu komutu çalıştırın. |

## <a name="get-information-about-vms"></a>VM 'Ler hakkında bilgi alın

| Görev | Komut |
| ---- | ------- |
| Bir abonelikteki VM 'Leri listeleme |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Bir kaynak grubundaki VM 'Leri listeleme |Get-AzVM-ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Aboneliğinizdeki kaynak gruplarının bir listesini almak için [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup)komutunu kullanın. |
| VM hakkında bilgi alma |Get-AzVM-ResourceGroupName $myResourceGroup-adı $myVM |

## <a name="manage-vms"></a>VM’leri yönetme
| Görev | Komut |
| --- | --- |
| VM başlatma |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -resourcegroupname $MyResourceGroup-adı $myVM |
| VM durdurma |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -resourcegroupname $MyResourceGroup-adı $myVM |
| Çalışan bir VM 'yi yeniden başlatma |[Restart-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -resourcegroupname $MyResourceGroup-adı $myVM |
| VM silme |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -resourcegroupname $MyResourceGroup-adı $myVM |


## <a name="next-steps"></a>Sonraki adımlar
* [Kaynak Yöneticisi ve PowerShell kullanarak WINDOWS VM oluşturma](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)bölümünde sanal makine oluşturmaya yönelik temel adımlara bakın.

