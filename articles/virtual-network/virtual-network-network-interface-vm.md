---
title: Azure VM 'lerine ağ arabirimleri ekleme veya bu sanal makinelere kaldırma
description: Sanal makinelerden ağ arabirimlerini ekleme veya bunları kaldırma hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 847f8dbd2d8f4064f12333348a4f03e5c5fcc611
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774278"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Sanal makinelerde ağ arabirimi ekleme veya kaldırma işlemlerini gerçekleştirme

Bir Azure sanal makinesi (VM) oluştururken Mevcut bir ağ arabirimini nasıl ekleyeceğinizi öğrenin. Ayrıca, durdurulmuş (serbest bırakıldı) durumda mevcut bir VM 'den ağ arabirimleri eklemeyi veya kaldırmayı öğrenin. Bir ağ arabirimi, Azure VM 'nin internet, Azure ve şirket içi kaynaklarla iletişim kurmasını sağlar. Bir VM 'nin bir veya daha fazla ağ arabirimi vardır. 

Bir ağ arabirimi için IP adresi eklemeniz, değiştirmeniz veya kaldırmanız gerekirse bkz. [ağ ARABIRIMI IP adreslerini yönetme](virtual-network-network-interface-addresses.md). Ağ arabirimlerini oluşturmak, değiştirmek veya silmek için bkz. [ağ arabirimlerini yönetme](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hesabınız yoksa, etkin abonelikle bir Azure hesabı ayarlayın. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Bu makalenin geri kalanını başlatmadan önce bu görevlerden birini doldurun:

- **Portal kullanıcıları**: [Azure Portal](https://portal.azure.com) Azure hesabınızla oturum açın.

- **PowerShell kullanıcıları**: [Azure Cloud Shell](https://shell.azure.com/powershell)komutları çalıştırın ya da bilgisayarınızdan PowerShell 'i çalıştırın. Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Azure Cloud Shell tarayıcısı sekmesinde **ortam Seç** açılan listesini bulun, ardından henüz seçili değilse **PowerShell** ' i seçin.

    PowerShell 'i yerel olarak çalıştırıyorsanız, Azure PowerShell Module sürüm 1.0.0 veya üstünü kullanın. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az.Network` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). Azure ile bağlantı oluşturmak için `Connect-AzAccount` komutunu çalıştırın.

- **Azure komut satırı arabirimi (CLI) kullanıcıları**: [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da CLI 'yı bilgisayarınızdan çalıştırın. Azure CLı 'yi yerel olarak çalıştırıyorsanız, Azure CLı sürüm 2.0.26 veya üstünü kullanın. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli). Azure ile bağlantı oluşturmak için `az login` komutunu çalıştırın.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Mevcut ağ arabirimlerini yeni bir VM 'ye Ekle

Portal üzerinden bir sanal makine oluşturduğunuzda, Portal varsayılan ayarlarla bir ağ arabirimi oluşturur ve ağ arabirimini sizin için VM 'ye ekler. Mevcut ağ arabirimlerini yeni bir VM 'ye eklemek veya birden çok ağ arabirimi içeren bir VM oluşturmak için portalını kullanamazsınız. CLı veya PowerShell kullanarak her ikisini de yapabilirsiniz. [Kısıtlamaları](#constraints)öğrenmek için kendinizi öğrendiğinizden emin olun. Birden çok ağ arabirimi içeren bir VM oluşturursanız, sanal makineyi oluşturduktan sonra işletim sistemini düzgün şekilde kullanmak için de yapılandırmanız gerekir. [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) veya [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 'u birden çok ağ arabirimi için yapılandırmayı öğrenin.

### <a name="commands"></a>Komutlar

VM oluşturmadan önce [bir ağ arabirimi oluşturun](virtual-network-network-interface.md#create-a-network-interface).

|Araç|Komut|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_create)|
|PowerShell|[New-Aznetworkınterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Mevcut bir VM 'ye ağ arabirimi ekleme

Sanal makinenize bir ağ arabirimi eklemek için:

1. Var olan bir sanal makineyi bulmak için [Azure Portal](https://portal.azure.com) gidin. **Sanal makineleri** arayın ve seçin.

2. SANAL makinenizin adını seçin. VM, eklemek istediğiniz ağ arabirimlerinin sayısını desteklemelidir. Her VM boyutunun kaç ağ arabirimine desteklediğini öğrenmek için, [Linux VM 'leri](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Windows VM 'leri](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)için Azure 'daki boyutlara bakın.

3. VM komut çubuğunda **Durdur**' u ve ardından onay Iletişim kutusunda **Tamam** ' ı seçin. Ardından, VM 'nin **durumu** **durduruldu (serbest bırakıldı)** olarak değişene kadar bekleyin.

4. VM menü **çubuğundan ağ**  >  **Ekle ağ arabirimi**' ni seçin. Ardından, **var olan ağ arabirimini Ekle**' de, eklemek istediğiniz ağ arabirimini seçin ve **Tamam**' ı seçin.

    >[!NOTE]
    >Seçtiğiniz ağ arabiriminde hızlandırılmış ağ etkin olamaz, kendisine atanmış bir IPv6 adresi olamaz ve VM 'ye bağlı olan ağ arabirimi ile aynı sanal ağda bulunmalıdır.

    Mevcut bir ağ arabiriminiz yoksa, önce bir tane oluşturmanız gerekir. Bunu yapmak için **ağ arabirimi oluştur**' u seçin. Ağ arabirimi oluşturma hakkında daha fazla bilgi edinmek için bkz. [ağ arabirimi oluşturma](virtual-network-network-interface.md#create-a-network-interface). Sanal makinelere ağ arabirimleri eklenirken ek kısıtlamalar hakkında daha fazla bilgi edinmek için bkz. [kısıtlamalar](#constraints).

5.   >  Sanal makineyi yeniden başlatmak için VM menü çubuğundan genel bakış **Başlat** ' ı seçin.

Artık, sanal makine işletim sistemini birden çok ağ arabirimini düzgün şekilde kullanacak şekilde yapılandırabilirsiniz. [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) veya [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 'u birden çok ağ arabirimi için yapılandırmayı öğrenin.

### <a name="commands"></a>Komutlar

|Araç|Komut|
|---|---|
|CLI|[az VM NIC Add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_add) (başvuru); [ayrıntılı adımlar](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-Azvmnetworkınterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (başvuru); [ayrıntılı adımlar](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Bir VM için ağ arabirimlerini görüntüleme

Her bir ağ arabiriminin yapılandırması ve her bir ağ arabirimine atanan IP adresleri hakkında bilgi edinmek için şu anda bir VM 'ye bağlı olan ağ arabirimlerini görüntüleyebilirsiniz. 

1. Var olan bir sanal makineyi bulmak için [Azure Portal](https://portal.azure.com) gidin. **Sanal makineleri** arayın ve seçin.

    >[!NOTE]
    >Aboneliğiniz için sahip, katkıda bulunan veya ağ katılımcısı rolüne atanan bir hesabı kullanarak oturum açın. Hesaplara rol atama hakkında daha fazla bilgi edinmek için bkz. [Azure rol tabanlı erişim denetimi Için yerleşik roller](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).

2. Bağlı ağ arabirimlerini görüntülemek istediğiniz VM 'nin adını seçin.

3. VM menü çubuğunda **ağ**' ı seçin.

Ağ arabirimi ayarları ve bunların nasıl değiştirileceği hakkında bilgi edinmek için bkz. [ağ arabirimlerini yönetme](virtual-network-network-interface.md). Bir ağ arabirimine atanan IP adreslerini ekleme, değiştirme veya kaldırma hakkında bilgi edinmek için bkz. [ağ ARABIRIMI IP adreslerini yönetme](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Komutlar

|Araç|Komut|
|---|---|
|CLI|[az VM NIC List](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Bir VM 'den ağ arabirimini kaldırma

1. Var olan bir sanal makineyi bulmak için [Azure Portal](https://portal.azure.com) gidin. **Sanal makineleri** arayın ve seçin.

2. Bağlı ağ arabirimlerini görüntülemek istediğiniz VM 'nin adını seçin.

3. VM araç çubuğunda **Durdur**' u seçin.

4. VM 'nin **durumu** **durduruldu (serbest bırakıldı)** olarak değişene kadar bekleyin.

5. VM menü çubuğundan   >  **ağ bağlantısını ayır**' ı seçin.

6. **Ağ arabirimini ayır** iletişim kutusunda, ayırmak istediğiniz ağ arabirimini seçin. Ardından **Tamam**’ı seçin.

    >[!NOTE]
    >Yalnızca bir ağ arabirimi listeleniyorsa, bir sanal makineye bağlı en az bir ağ arabirimine sahip olması gerektiğinden, bunu ayırabilirsiniz.

### <a name="commands"></a>Komutlar

|Araç|Komut|
|---|---|
|CLI|[az VM NIC Remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_remove) (başvuru); [ayrıntılı adımlar](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-Azvmnetworkınterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (başvuru); [ayrıntılı adımlar](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Kısıtlamalar

- Bir VM 'ye iliştirilmiş en az bir ağ arabirimi olmalıdır.

- VM 'nin desteklediği bir VM 'ye bağlı çok sayıda ağ arabirimi olabilir. Her VM boyutunun desteklediği ağ arabirimlerinin sayısı hakkında daha fazla bilgi edinmek için bkz. [Linux VM 'leri](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Windows VM 'leri](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)için Azure 'da boyutlar. Tüm boyutlar en az iki ağ arabirimini destekler.

- Bir VM 'ye eklediğiniz ağ arabirimleri Şu anda başka bir VM 'ye iliştirilemiyor. Ağ arabirimleri oluşturma hakkında daha fazla bilgi edinmek için bkz. [ağ arabirimi oluşturma](virtual-network-network-interface.md#create-a-network-interface).

- Geçmişte, ağ arabirimlerini yalnızca birden çok ağ arabirimi destekleyen ve en az iki ağ arabirimi ile oluşturulan VM 'lere ekleyebilirsiniz. VM boyutu birden fazla ağ arabirimi desteklenenden bile, tek bir ağ arabirimiyle oluşturulmuş bir VM 'ye ağ arabirimi eklemeyin. Buna karşılık, en az iki ağ arabirimi ile oluşturulan VM 'Lerin her zaman en az iki ağ arabirimine sahip olması gerektiğinden, ağ arabirimlerini yalnızca en az üç ağ arabirimine sahip bir VM 'den kaldırabilirsiniz. Bu kısıtlamalar artık uygulanmaz. Artık, herhangi bir sayıda ağ arabirimine sahip bir VM oluşturabilirsiniz (VM boyutu tarafından desteklenen sayıya kadar).

- Varsayılan olarak, bir VM 'ye bağlı ilk ağ arabirimi, *birincil* ağ arabirimidir. VM 'deki diğer tüm ağ arabirimleri *İkincil* ağ arabirimlerdir.

- Giden trafiği hangi ağ arabirimine göndereğinizi kontrol edebilirsiniz. Bununla birlikte, varsayılan olarak bir VM, tüm giden trafiği birincil ağ arabiriminin birincil IP yapılandırmasına atanan IP adresine gönderir.

- Geçmişte, aynı Kullanılabilirlik kümesi içindeki tüm VM 'Lerin tek veya birden çok ağ arabirimine sahip olması gerekiyordu. Herhangi bir sayıda ağ arabirimine sahip VM 'Ler artık aynı Kullanılabilirlik kümesinde bulunabilir ve bu, VM boyutu tarafından desteklenen sayıya kadar olabilir. Bir VM 'yi yalnızca oluşturulduğu zaman bir kullanılabilirlik kümesine ekleyebilirsiniz. Kullanılabilirlik kümeleri hakkında daha fazla bilgi edinmek için bkz. [Azure 'Da VM 'lerin kullanılabilirliğini yönetme](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

- Aynı VM 'deki ağ arabirimlerini bir sanal ağ içindeki farklı alt ağlara bağlayabilirsiniz. Ancak, ağ arabirimlerinin tümünün aynı sanal ağa bağlanması gerekir.

- Herhangi bir birincil veya ikincil ağ arabiriminin herhangi bir IP yapılandırması için herhangi bir IP adresini Azure Load Balancer bir arka uç havuzuna ekleyebilirsiniz. Geçmişte, bir arka uç havuzuna yalnızca birincil ağ arabiriminin birincil IP adresi eklenebilir. IP adresleri ve yapılandırma hakkında daha fazla bilgi için bkz. [IP adreslerini ekleme, değiştirme veya kaldırma](virtual-network-network-interface-addresses.md).

- Bir VM 'nin silinmesi, kendisine bağlı olan ağ arabirimlerini silmez. Bir VM 'yi sildiğinizde, ağ arabirimleri VM 'den ayrılır. Bu ağ arabirimlerini farklı VM 'lere ekleyebilir veya silebilirsiniz.

- En iyi performansı elde etmek için hızlandırılmış ağ gerekir. Bazı durumlarda, [Windows](create-vm-accelerated-networking-powershell.md) veya [Linux](create-vm-accelerated-networking-cli.md) sanal makineleri için hızlandırılmış ağı açıkça etkinleştirmeniz gerekir.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="next-steps"></a>Sonraki adımlar

Birden çok ağ arabirimi veya IP adresi olan bir VM oluşturmak için bkz.:

|Görev|Araç|
|---|---|
|Birden çok NIC ile VM oluşturma|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Birden çok IPv4 adresi olan tek bir NIC VM oluşturma|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Özel bir IPv6 adresine sahip tek bir NIC sanal makinesi oluşturma (Azure Load Balancer arka plan)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager şablonu](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|