---
title: Azure sanal makinelerine ağ arabirimleri ekleme veya bu makinelere kaldırma | Microsoft Docs
description: Sanal makinelerden ağ arabirimlerini ekleme veya bunları kaldırma hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: kumud
ms.openlocfilehash: 24f4b7435c2736527d033aa5ca7a65ad35a3a705
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022177"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Sanal makinelerden ağ arabirimleri ekleme veya ağ arabirimlerini kaldırma

Bir Azure sanal makinesi (VM) oluştururken veya durdurulmuş (serbest bırakıldı) durumda var olan bir VM 'den ağ arabirimleri eklemek veya kaldırmak için mevcut bir ağ arabirimini nasıl ekleyeceğinizi öğrenin. Bir ağ arabirimi, bir Azure sanal makinesinin internet, Azure ve şirket içi kaynaklarla iletişim kurmasını sağlar. Bir VM 'nin bir veya daha fazla ağ arabirimi olabilir. 

Bir ağ arabirimi için IP adresi eklemeniz, değiştirmeniz veya kaldırmanız gerekirse bkz. [ağ ARABIRIMI IP adreslerini yönetme](virtual-network-network-interface-addresses.md). Ağ arabirimlerini oluşturmanız, değiştirmeniz veya silmeniz gerekiyorsa bkz. [ağ arabirimlerini yönetme](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu makalenin herhangi bir bölümündeki adımları tamamlamadan önce aşağıdaki görevleri doldurun:

- Henüz bir Azure hesabınız yoksa [ücretsiz deneme hesabı](https://azure.microsoft.com/free)için kaydolun.
- Portalı kullanıyorsanız, https://portal.azure.com ' ı açın ve Azure hesabınızla oturum açın.
- Bu makaledeki görevleri tamamlamaya yönelik PowerShell komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/powershell)komutları çalıştırın veya PowerShell 'i bilgisayarınızdan çalıştırarak çalıştırın. Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Bu öğretici, Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.
- Bu makaledeki görevleri gerçekleştirmek için Azure komut satırı arabirimi (CLı) komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da bilgisayarınızdan CLI 'yı çalıştırarak. Bu öğretici, Azure CLı sürüm 2.0.26 veya üstünü gerektirir. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme](/cli/azure/install-azure-cli). Azure CLı 'yi yerel olarak çalıştırıyorsanız, Azure ile bağlantı oluşturmak için `az login` ' ı çalıştırmanız gerekir.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Mevcut ağ arabirimlerini yeni bir VM 'ye Ekle

Portal üzerinden bir sanal makine oluşturduğunuzda, Portal varsayılan ayarlarla bir ağ arabirimi oluşturur ve bunu sizin için VM 'ye ekler. Mevcut ağ arabirimlerini yeni bir sanal makineye ekleyemez ve Azure portal kullanarak birden çok ağ arabirimine sahip bir VM oluşturabilirsiniz. CLı veya PowerShell kullanarak her ikisini de yapabilirsiniz, ancak [kısıtlamaları](#constraints)öğrendiğinizden emin olun. Birden çok ağ arabirimi içeren bir VM oluşturursanız, sanal makineyi oluşturduktan sonra işletim sistemini düzgün şekilde kullanmak için de yapılandırmanız gerekir. [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) veya [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 'u birden çok ağ arabirimi için yapılandırmayı öğrenin.

### <a name="commands"></a>Komutlar

VM oluşturmadan önce, [ağ arabirimi oluşturma](virtual-network-network-interface.md#create-a-network-interface)' daki adımları kullanarak bir ağ arabirimi oluşturun.

|Araç|Komut|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Mevcut bir VM 'ye ağ arabirimi ekleme

1. Azure Portal’da oturum açın.
2. Portalın üst kısmındaki arama kutusuna, ağ arabirimini eklemek istediğiniz VM 'nin adını yazın veya **tüm hizmetler**' i ve ardından **sanal makineler**' i seçerek VM 'ye gidin. VM 'yi bulduktan sonra seçin. VM, eklemek istediğiniz ağ arabirimlerinin sayısını desteklemelidir. Her VM boyutunun kaç ağ arabirimine desteklediğini öğrenmek için bkz. Azure 'da [Linux sanal makineleri Için boyutlar](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Azure 'da Windows sanal makineleri için Boyutlar](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. **Ayarlar**' ın altında **genel bakış**' ı seçin. **Durdur**' u seçin ve ardından VM 'Nin **durumu** **durduruldu (serbest bırakıldı)** olarak değişene kadar bekleyin.
4. **Ayarlar**altında **ağ**' ı seçin.
5. **Ağ arabirimi Ekle**' yi seçin. Şu anda başka bir VM 'ye bağlı olmayan ağ arabirimleri listesinden eklemek istediğiniz birini seçin.

   >[!NOTE]
   >Seçtiğiniz ağ arabiriminde hızlandırılmış ağ etkin olamaz, kendisine atanmış bir IPv6 adresi olamaz ve VM 'ye bağlı olan ağ arabirimini içeren sanal ağ ile aynı sanal ağda bulunmalıdır.

   Mevcut bir ağ arabiriminiz yoksa, önce bir tane oluşturmanız gerekir. Bunu yapmak için **ağ arabirimi oluştur**' u seçin. Ağ arabirimi oluşturma hakkında daha fazla bilgi edinmek için bkz. [ağ arabirimi oluşturma](virtual-network-network-interface.md#create-a-network-interface). Sanal makinelere ağ arabirimleri eklenirken ek kısıtlamalar hakkında daha fazla bilgi edinmek için bkz. [kısıtlamalar](#constraints).

6. **Tamam**’ı seçin.
7. **Ayarlar**' ın altında **genel bakış**' ı seçin ve sonra sanal makineyi başlatmak için **başlatın** .
8. VM işletim sistemini, birden çok ağ arabirimini düzgün şekilde kullanacak şekilde yapılandırın. [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) veya [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 'u birden çok ağ arabirimi için yapılandırmayı öğrenin.

### <a name="commands"></a>Komutlar
|Araç|Komut|
|---|---|
|CLI|[az VM NIC Add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (başvuru) veya [ayrıntılı adımlar](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-Azvmnetworkınterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (başvuru) veya [ayrıntılı adımlar](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Bir VM için ağ arabirimlerini görüntüleme

Her bir ağ arabiriminin yapılandırması ve her bir ağ arabirimine atanan IP adresleri hakkında bilgi edinmek için şu anda bir VM 'ye bağlı olan ağ arabirimlerini görüntüleyebilirsiniz. 

1. Aboneliğiniz için sahip, katkıda bulunan veya ağ katılımcısı rolüne atanan bir hesapla [Azure Portal](https://portal.azure.com) oturum açın. Hesaplara rol atama hakkında daha fazla bilgi edinmek için bkz. [Azure rol tabanlı erişim denetimi Için yerleşik roller](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Azure portal en üstünde bulunan metin **arama kaynaklarını** içeren kutuya **sanal makineler**yazın. Arama sonuçlarında **sanal makineler** göründüğünde seçin.
3. Ağ arabirimlerini görüntülemek istediğiniz VM 'nin adını seçin.
4. Seçtiğiniz VM 'nin **Ayarlar** bölümünde **ağ iletişimi**' ni seçin. Ağ arabirimi ayarları ve bunların nasıl değiştirileceği hakkında bilgi edinmek için bkz. [ağ arabirimlerini yönetme](virtual-network-network-interface.md). Bir ağ arabirimine atanan IP adreslerini ekleme, değiştirme veya kaldırma hakkında bilgi edinmek için bkz. [ağ ARABIRIMI IP adreslerini yönetme](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Komutlar

|Araç|Komut|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Bir VM 'den ağ arabirimini kaldırma

1. Azure Portal’da oturum açın.
2. Portalın üst kısmındaki arama kutusunda, ağ arabirimini kaldırmak istediğiniz sanal makinenin adını arayın veya **tüm hizmetler**' i ve ardından **sanal makineler**' i seçerek VM 'ye gidin. VM 'yi bulduktan sonra seçin.
3. **Genel bakış**' ı ve **Ayarlar**' ı seçin ve ardından **durdurun**. VM 'nin **durumu** **durduruldu (serbest bırakıldı)** olarak değişene kadar bekleyin.
4. **Ayarlar**altında **ağ**' ı seçin.
5. **Ağ arabirimini ayır**' ı seçin. Sanal makineye Şu anda bağlı olan ağ arabirimleri listesinden, ayırmak istediğiniz ağ arabirimini seçin.

   >[!NOTE]
   >Yalnızca bir ağ arabirimi listeleniyorsa, bir sanal makineye bağlı en az bir ağ arabirimine sahip olması gerektiğinden, bu ilişkiyi ayıramazsınız.
6. **Tamam**’ı seçin.

### <a name="commands"></a>Komutlar

|Araç|Komut|
|---|---|
|CLI|[az VM NIC Remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (başvuru) veya [ayrıntılı adımlar](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-Azvmnetworkınterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (başvuru) veya [ayrıntılı adımlar](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Kısıtlamalar

- Bir VM 'ye iliştirilmiş en az bir ağ arabirimi olmalıdır.
- VM 'nin desteklediği bir VM 'ye bağlı çok sayıda ağ arabirimi olabilir. Her VM boyutunun desteklediği ağ arabirimlerinin sayısı hakkında daha fazla bilgi edinmek için bkz. Azure 'da [Linux sanal makineleri Için boyutlar](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Azure 'da Windows sanal makineleri için Boyutlar](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tüm boyutlar en az iki ağ arabirimini destekler.
- Bir VM 'ye eklediğiniz ağ arabirimleri Şu anda başka bir sanal makineye iliştirilemiyor. Ağ arabirimleri oluşturma hakkında daha fazla bilgi edinmek için bkz. [ağ arabirimi oluşturma](virtual-network-network-interface.md#create-a-network-interface).
- Geçmişte, ağ arabirimleri yalnızca birden çok ağ arabirimi destekleyen ve en az iki ağ arabirimi ile oluşturulan VM 'lere eklenebilir. VM boyutu birden çok ağ arabirimi desteklendiğinden bile, tek bir ağ arabirimiyle oluşturulmuş bir VM 'ye ağ arabirimi eklemeyin. Buna karşılık, en az iki ağ arabirimi ile oluşturulan VM 'Lerin her zaman en az iki ağ arabirimine sahip olması gerektiğinden, ağ arabirimlerini yalnızca en az üç ağ arabirimine sahip bir VM 'den kaldırabilirsiniz. Bu kısıtlamaların hiçbiri artık uygulanmaz. Artık, herhangi bir sayıda ağ arabirimine sahip bir VM oluşturabilirsiniz (VM boyutu tarafından desteklenen sayıya kadar).
- Varsayılan olarak, bir VM 'ye bağlı ilk ağ arabirimi, *birincil* ağ arabirimi olarak tanımlanır. VM 'deki diğer tüm ağ arabirimleri *İkincil* ağ arabirimlerdir.
- Giden trafiği hangi ağ arabirimine gönderdiniz denetleyebilir, ancak varsayılan olarak, VM 'den gelen tüm giden trafik birincil ağ arabiriminin birincil IP yapılandırmasına atanan IP adresine gönderilir.
- Geçmişte, aynı Kullanılabilirlik kümesi içindeki tüm VM 'Lerin tek veya birden çok ağ arabirimine sahip olması gerekiyordu. Herhangi bir sayıda ağ arabirimine sahip VM 'Ler artık aynı Kullanılabilirlik kümesinde bulunabilir ve bu, VM boyutu tarafından desteklenen sayıya kadar olabilir. Bir VM 'yi yalnızca oluşturulduğu zaman bir kullanılabilirlik kümesine ekleyebilirsiniz. Kullanılabilirlik kümeleri hakkında daha fazla bilgi edinmek için bkz. [Azure 'Da VM 'lerin kullanılabilirliğini yönetme](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Aynı VM 'deki ağ arabirimleri bir sanal ağ içindeki farklı alt ağlara bağlanabilir olsa da, ağ arabirimlerinin hepsi aynı sanal ağa bağlı olmalıdır.
- Herhangi bir birincil veya ikincil ağ arabiriminin herhangi bir IP yapılandırması için herhangi bir IP adresini Azure Load Balancer bir arka uç havuzuna ekleyebilirsiniz. Geçmişte, bir arka uç havuzuna yalnızca birincil ağ arabiriminin birincil IP adresi eklenebilir. IP adresleri ve yapılandırma hakkında daha fazla bilgi için bkz. [IP adreslerini ekleme, değiştirme veya kaldırma](virtual-network-network-interface-addresses.md).
- Bir VM 'nin silinmesi, kendisine bağlı olan ağ arabirimlerini silmez. Bir VM 'yi sildiğinizde, ağ arabirimleri VM 'den ayrılır. Ağ arabirimlerini farklı sanal makinelere ekleyebilir veya silebilirsiniz.
- IPv6 'da olduğu gibi, bir VM 'yi oluşturduktan sonra bir sanal ağa hızlandırılmış ağ arabirimine sahip bir ağ arabirimi iliştiremezsiniz. Ayrıca, hızlandırılmış ağ iletişimi avantajlarından yararlanmak için VM işletim sistemi içindeki adımları da tamamlamalısınız. [Windows](create-vm-accelerated-networking-powershell.md) veya [Linux](create-vm-accelerated-networking-cli.md) sanal makineleri için hızlandırılmış ağ ve bunu kullanırken diğer kısıtlamalar hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar
Birden çok ağ arabirimi veya IP adresi olan bir VM oluşturmak için aşağıdaki makalelere bakın:

|Görev|Araç|
|---|---|
|Birden çok NIC ile VM oluşturma|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Birden çok IPv4 adresi olan tek bir NIC VM oluşturma|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Özel bir IPv6 adresine sahip tek bir NIC sanal makinesi oluşturma (Azure Load Balancer arka plan)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager şablonu](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
