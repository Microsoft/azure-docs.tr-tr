---
title: Azure sanal makineleri için ortak PowerShell komutları
description: Azure 'da VM oluşturmaya ve yönetmeye başlamanızı sağlamak için ortak PowerShell komutları.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: ecc3f5934e4dd37cc27a1715ea39f86619d581ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730215"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Azure Sanal Makineler’i oluşturmak ve yönetmek için genel PowerShell komutları

Bu makalede, Azure aboneliğinizdeki sanal makineleri oluşturmak ve yönetmek için kullanabileceğiniz Azure PowerShell komutlarının bazıları ele alınmaktadır.  Belirli komut satırı anahtarları ve seçenekleriyle ilgili daha ayrıntılı yardım için **Get-Help** *komutunu* kullanabilirsiniz.

 

Bu değişkenler, bu makaledeki komutlardan birden fazlasını çalıştırdığınızda sizin için yararlı olabilir:

- $location-sanal makinenin konumu. Sizin için uygun bir [coğrafi bölge](https://azure.microsoft.com/regions/) bulmak için [Get-azlocation](/powershell/module/az.resources/get-azlocation) ' i kullanabilirsiniz.
- $myResourceGroup-sanal makineyi içeren kaynak grubunun adı.
- $myVM-sanal makinenin adı.

## <a name="create-a-vm---simplified"></a>VM oluşturma-Basitleştirilmiş

| Görev | Komut |
| ---- | ------- |
| Basit VM oluşturma | [New-AzVM](/powershell/module/az.compute/new-azvm) -adı $myVM <BR></BR><BR></BR> New-AzVM, tüm gerekli tek bir ad olduğu *Basitleştirilmiş* parametreler kümesine sahiptir. -Name değeri, yeni bir VM oluşturmak için gereken tüm kaynakların adı olarak kullanılacaktır. Daha fazlasını belirtebilirsiniz, ancak bu gereklidir.|
| Özel görüntüden VM oluşturma | New-AzVm-ResourceGroupName $myResourceGroup-adı $myVM Gename "MyImage"-Location $location  <BR></BR><BR></BR>Kendi [yönetilen görüntünüzü](capture-image-resource.md)zaten oluşturmuş olmanız gerekir. Birden çok ve özdeş VM oluşturmak için bir görüntü kullanabilirsiniz. |



## <a name="create-a-vm-configuration"></a>VM yapılandırması oluşturma

| Görev | Komut |
| ---- | ------- |
| VM yapılandırması oluşturma |$vm = [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) -vmname $MyVM-vmsize "Standard_D1_v1"<BR></BR><BR></BR>VM yapılandırması VM ayarlarını tanımlamak veya güncelleştirmek için kullanılır. Yapılandırma, VM adı ve [boyutu](../sizes.md)ile başlatılır. |
| Yapılandırma ayarları ekle |$vm = [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) -VM $VM-Windows-ComputerName $MyVM-Credential $cred-ProvisionVMAgent-enableotomatik güncelleştirme<BR></BR><BR></BR>Yeni-AzVMConfig kullanarak daha önce oluşturduğunuz yapılandırma nesnesine [kimlik bilgileri](/powershell/module/microsoft.powershell.security/get-credential) de dahil olmak üzere işletim sistemi ayarları eklenir. |
| Ağ arabirimi ekleme |$vm = [Add-Azvmnetworkınterface](/powershell/module/az.compute/add-azvmnetworkinterface) -VM $vm kimliği $Nic. Numarasını<BR></BR><BR></BR>Bir VM 'nin bir sanal ağda iletişim kurmak için bir [ağ arabirimi](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json) olmalıdır. Ayrıca, var olan bir ağ arabirimi nesnesini almak için [Get-Aznetworkınterface](/powershell/module/az.compute/add-azvmnetworkinterface) ' i de kullanabilirsiniz. |
| Platform görüntüsü belirtme |$vm = [set-Azvmsourceımage](/powershell/module/az.compute/set-azvmsourceimage) -VM $VM-publishername "publisher_name"-teklif "publisher_offer"-sku "product_sku"-sürüm "en son"<BR></BR><BR></BR>Yeni-AzVMConfig kullanarak daha önce oluşturduğunuz yapılandırma nesnesine [görüntü bilgileri](cli-ps-findimage.md) eklenir. Bu komuttan döndürülen nesne yalnızca bir platform görüntüsü kullanmak için işletim sistemi diski ayarladığınızda kullanılır. |
| VM oluşturma |[New-AzVM](/powershell/module/az.compute/new-azvm) -resourcegroupname $MyResourceGroup-location $LOCATION-VM $VM<BR></BR><BR></BR>Tüm kaynaklar bir [kaynak grubunda](../../azure-resource-manager/management/manage-resource-groups-powershell.md)oluşturulur. Bu komutu çalıştırmadan önce New-AzVMConfig, set-AzVMOperatingSystem, set-Azvmsourceımage, Add-Azvmnetworkınterface ve set-AzVMOSDisk komutunu çalıştırın. |
| VM güncelleştirme |[Update-AzVM](/powershell/module/az.compute/update-azvm) -resourcegroupname $MYRESOURCEGROUP-VM $VM<BR></BR><BR></BR>Get-AzVM kullanarak geçerli VM yapılandırmasını alın, VM nesnesindeki yapılandırma ayarlarını değiştirin ve ardından bu komutu çalıştırın. |

## <a name="get-information-about-vms"></a>VM 'Ler hakkında bilgi alın

| Görev | Komut |
| ---- | ------- |
| Bir abonelikteki VM 'Leri listeleme |[Get-AzVM](/powershell/module/az.compute/get-azvm) |
| Bir kaynak grubundaki VM 'Leri listeleme |Get-AzVM-ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Aboneliğinizdeki kaynak gruplarının bir listesini almak için [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)komutunu kullanın. |
| VM hakkında bilgi alma |Get-AzVM-ResourceGroupName $myResourceGroup-adı $myVM |

## <a name="manage-vms"></a>VM’leri yönetme
| Görev | Komut |
| --- | --- |
| VM başlatma |[Start-AzVM](/powershell/module/az.compute/start-azvm) -resourcegroupname $MyResourceGroup-adı $myVM |
| VM durdurma |[Stop-AzVM](/powershell/module/az.compute/stop-azvm) -resourcegroupname $MyResourceGroup-adı $myVM |
| Çalışan bir VM 'yi yeniden başlatma |[Restart-AzVM](/powershell/module/az.compute/restart-azvm) -resourcegroupname $MyResourceGroup-adı $myVM |
| VM silme |[Remove-AzVM](/powershell/module/az.compute/remove-azvm) -resourcegroupname $MyResourceGroup-adı $myVM |


## <a name="next-steps"></a>Sonraki adımlar
* [Kaynak Yöneticisi ve PowerShell kullanarak WINDOWS VM oluşturma](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json)bölümünde sanal makine oluşturmaya yönelik temel adımlara bakın.
