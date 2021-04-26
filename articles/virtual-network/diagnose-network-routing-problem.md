---
title: Azure sanal makine yönlendirme sorununu tanılama | Microsoft Docs
description: Bir sanal makine için geçerli yolları görüntüleyerek bir sanal makine yönlendirme sorununu tanılamayı öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: f84b74b054a073f2c1ae5ba2ac7d0d0a968367c6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767682"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Bir sanal makine yönlendirme sorununu tanılama

Bu makalede, bir sanal makinede (VM) bir ağ arabirimi için geçerli olan yolları görüntüleyerek bir yönlendirme sorununu tanılamayı öğreneceksiniz. Azure, her sanal ağ alt ağı için birkaç varsayılan yol oluşturur. Rota tablosunda yollar tanımlayarak Azure 'un varsayılan yollarını geçersiz kılabilir ve sonra yol tablosunu bir alt ağ ile ilişkilendirmenize olanak sağlayabilirsiniz. Oluşturduğunuz yolların birleşimi, Azure 'un varsayılan yolları ve şirket içi ağınızdan bir Azure VPN ağ geçidi üzerinden (sanal ağınız şirket içi ağınıza bağlıysa), bir alt ağdaki tüm ağ arabirimleri için etkili yollardır). Sanal ağ, ağ arabirimi veya yönlendirme kavramlarıyla ilgili bilgi sahibi değilseniz bkz. [sanal ağa genel bakış](virtual-networks-overview.md), [ağ arabirimi](virtual-network-network-interface.md)ve [yönlendirmeye genel bakış](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Senaryo

Bir VM 'ye bağlanmaya çalışırsanız, ancak bağlantı başarısız olur. SANAL makineye neden bağlanamamanızın nedenini öğrenmek için, Azure [Portal](#diagnose-using-azure-portal), [POWERSHELL](#diagnose-using-powershell)veya [Azure CLI](#diagnose-using-azure-cli)kullanarak bir ağ arabirimine yönelik etkili yolları görüntüleyebilirsiniz.

Aşağıdaki adımlarda, için geçerli yolları görüntülemek için mevcut bir sanal makinenizin olduğu varsayılır. Mevcut bir sanal makinenizin yoksa, bu makaledeki görevleri gerçekleştirmek için önce bir [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM dağıtın. Bu makaledeki örnekler, *myVMNic1* adlı bir ağ arabirimine sahip *myvm* adlı bir sanal makine içindir. VM ve ağ arabirimi, *Myresourcegroup* adlı bir kaynak grubunda yer alan ve *Doğu ABD* bölgedeyse. Adımdaki değerleri, sorunu tanılarken kullandığınız VM için uygun şekilde değiştirin.

## <a name="diagnose-using-azure-portal"></a>Azure portal kullanarak tanılama

1. [Gerekli izinlere](virtual-network-network-interface.md#permissions)sahip bir Azure hesabıyla Azure [portalında](https://portal.azure.com) oturum açın.
2. Azure portal üst kısmında, arama kutusuna çalışır durumda olan bir VM 'nin adını girin. VM 'nin adı arama sonuçlarında göründüğünde, bunu seçin.
3. Sol taraftaki **Ayarlar** **altında ağ ' ı seçin ve** adı ' nı seçerek ağ arabirimi kaynağına gidin.
     ![Ağ arabirimlerini görüntüle](./media/diagnose-network-routing-problem/view-nics.png)
4. Sol tarafta, **etkin rotalar**' ı seçin. Aşağıdaki resimde, **myVMNic1** adlı bir ağ arabirimi için geçerli rotalar gösterilmektedir: ![ geçerli rotaları görüntüleme](./media/diagnose-network-routing-problem/view-effective-routes.png)

    VM 'ye bağlı birden fazla ağ arabirimi varsa, herhangi bir ağ arabirimi için geçerli yolları seçerek görüntüleyebilirsiniz. Her ağ arabirimi farklı bir alt ağda yer aldığı için, her bir ağ arabirimi farklı bir etkin yollara sahip olabilir.

    Önceki resimde gösterilen örnekte, listelenen yollar Azure 'un her alt ağ için oluşturduğu varsayılan yollardır. Listenizde en az bu yollar bulunur, ancak başka bir sanal ağla eşlenmekte veya bir Azure VPN ağ geçidi üzerinden şirket içi ağınıza bağlı olması gibi sanal ağınız için etkinleştirilmiş olabilecek yeteneklere bağlı olarak ek rotalar olabilir. Yolların her biri ve ağ arabiriminiz için görebileceğiniz diğer yollar hakkında daha fazla bilgi edinmek için bkz. [sanal ağ trafiği yönlendirme](virtual-networks-udr-overview.md). Listenizde çok sayıda yol varsa, yolların listesiyle bir. csv dosyası indirmek için **İndir**' i seçmeniz daha kolay olabilir.

Etkin yollar önceki adımlarda VM üzerinden görüntülenmekle birlikte, geçerli rotaları bir ile de görebilirsiniz:
- **Tek bir ağ arabirimi**: [bir ağ arabirimini görüntülemeyi](virtual-network-network-interface.md#view-network-interface-settings)öğrenin.
- **Tek tek yol tablosu**: [bir yol tablosunu görüntülemeyi](manage-route-table.md#view-details-of-a-route-table)öğrenin.

## <a name="diagnose-using-powershell"></a>PowerShell kullanarak tanılama

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure Cloud Shell](https://shell.azure.com/powershell)izleyen komutları veya bilgisayarınızdan PowerShell 'i çalıştırarak çalıştırabilirsiniz. Azure Cloud Shell, ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. PowerShell 'i bilgisayarınızdan çalıştırırsanız, Azure PowerShell Module, sürüm 1.0.0 veya sonraki bir sürümü gerekir. `Get-Module -ListAvailable Az`Yüklü sürümü bulmak için bilgisayarınızda çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız, `Connect-AzAccount` [gerekli izinlere](virtual-network-network-interface.md#permissions)sahip bir hesapla Azure 'da oturum açmak için de çalıştırmanız gerekir.

[Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)ile bir ağ arabirimi için geçerli yolları alın. Aşağıdaki örnek, *Myresourcegroup* adlı bir kaynak grubunda bulunan *myVMNic1* adlı bir ağ arabirimi için geçerli yolları alır:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Çıktıda döndürülen bilgileri anlamak için bkz. [yönlendirmeye genel bakış](virtual-networks-udr-overview.md). Çıkış yalnızca VM çalışır durumdaysa döndürülür. VM 'ye bağlı birden çok ağ arabirimi varsa, her bir ağ arabirimi için geçerli yolları gözden geçirebilirsiniz. Her ağ arabirimi farklı bir alt ağda yer aldığı için, her bir ağ arabirimi farklı bir etkin yollara sahip olabilir. Hala bir iletişim sorununuz varsa bkz. [ek tanılama](#additional-diagnosis) ve [önemli noktalar](#considerations).

Bir ağ arabiriminin adını bilmiyorsanız, ancak ağ arabiriminin eklendiği VM 'nin adını biliyorsanız, aşağıdaki komutlar bir VM 'ye bağlı tüm ağ arabirimlerinin kimliklerini döndürür:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Aşağıdaki örneğe benzer bir çıktı alırsınız:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

Önceki çıktıda, ağ arabirimi adı *myVMNic1*' dir.

## <a name="diagnose-using-azure-cli"></a>Azure CLı kullanarak tanılama

[Azure Cloud Shell](https://shell.azure.com/bash)izleyen komutları veya CLI 'yi bilgisayarınızdan çalıştırarak çalıştırabilirsiniz. Bu makale, Azure CLı sürüm 2.0.32 veya üstünü gerektirir. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli). Azure CLı 'yi yerel olarak çalıştırıyorsanız, Ayrıca, `az login` [gerekli izinlere](virtual-network-network-interface.md#permissions)sahip bir hesapla Azure 'da çalıştırmanız ve oturum açmanız gerekir.

[Az Network Nic Show-geçerli-Route-Table](/cli/azure/network/nic#az_network_nic_show_effective_route_table)ile bir ağ arabirimi için geçerli yolları alın. Aşağıdaki örnek, *Myresourcegroup* adlı bir kaynak grubunda bulunan *myVMNic1* adlı bir ağ arabirimi için geçerli yolları alır:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

Çıktıda döndürülen bilgileri anlamak için bkz. [yönlendirmeye genel bakış](virtual-networks-udr-overview.md). Çıkış yalnızca VM çalışır durumdaysa döndürülür. VM 'ye bağlı birden çok ağ arabirimi varsa, her bir ağ arabirimi için geçerli yolları gözden geçirebilirsiniz. Her ağ arabirimi farklı bir alt ağda yer aldığı için, her bir ağ arabirimi farklı bir etkin yollara sahip olabilir. Hala bir iletişim sorununuz varsa bkz. [ek tanılama](#additional-diagnosis) ve [önemli noktalar](#considerations).

Bir ağ arabiriminin adını bilmiyorsanız, ancak ağ arabiriminin eklendiği VM 'nin adını biliyorsanız, aşağıdaki komutlar bir VM 'ye bağlı tüm ağ arabirimlerinin kimliklerini döndürür:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Bir sorunu çözün

Yönlendirme sorunlarının çözümlenmesi genellikle aşağıdakilerden oluşur:

- Azure 'un varsayılan yollarından birini geçersiz kılmak için özel bir yol ekleme. [Özel bir yol eklemeyi](manage-route-table.md#create-a-route)öğrenin.
- İstenmeyen bir konuma yönlendirmeye neden olabilecek özel bir yolu değiştirin veya kaldırın. Özel bir yolu [değiştirme](manage-route-table.md#change-a-route) veya [silme](manage-route-table.md#delete-a-route) hakkında bilgi edinin.
- Tanımladığınız özel yolları içeren yol tablosunun, ağ arabiriminin içinde bulunduğu alt ağ ile ilişkili olduğundan emin olma. [Bir rota tablosunu bir alt ağla ilişkilendirmeyi](manage-route-table.md#associate-a-route-table-to-a-subnet)öğrenin.
- Dağıttığınız Azure VPN Gateway veya ağ sanal aygıtları gibi cihazların çalıştırılabilir olduğundan emin olma. Bir Azure VPN ağ geçidiyle ilgili sorunları öğrenmek için ağ Izleyicisi 'nin [VPN tanılama](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) özelliğini kullanın.

Hala iletişim sorunlarınız varsa bkz. [hususlar](#considerations) ve ek tanılama.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

İletişim sorunlarını giderirken aşağıdaki noktaları göz önünde bulundurun:

- Yönlendirme, tanımladığınız yollar, Sınır Ağ Geçidi Protokolü (BGP) ve sistem yolları arasındaki en uzun ön ek eşleşmesi (LPM) temel alınarak hesaplanır. Aynı LPM eşleşmesine sahip birden fazla yol varsa, [yönlendirmeye genel bakış](virtual-networks-udr-overview.md#how-azure-selects-a-route)bölümünde listelenen sıra kaynağına göre bir rota seçilir. Etkin rotalarla, yalnızca bir LPM eşleşmesi olan geçerli yolları, tüm kullanılabilir yollara göre görebilirsiniz. Yolların bir ağ arabirimi için nasıl değerlendirildiğini görmek, sanal makinenizin iletişimini etkileyebilecek belirli yolların giderilmesi çok daha kolay hale getirir.
- Bir ağ sanal gereci (NVA) için bir sonraki atlama türü olarak *Sanal Gereç* ile özel yollar tanımladıysanız, trafiği alan NVA 'da IP iletmenin etkinleştirildiğinden emin olun veya paketler bırakılmıştı. [Ağ arabirimi IÇIN IP iletmeyi etkinleştirme](virtual-network-network-interface.md#enable-or-disable-ip-forwarding)hakkında daha fazla bilgi edinin. Ayrıca, NVA içindeki işletim sistemi veya uygulama de ağ trafiğini iletebilmelidir ve bunu yapmak için yapılandırılmış olmalıdır.
- 0.0.0.0/0 için bir yol oluşturduysanız, tüm giden internet trafiği, bir NVA veya VPN ağ geçidi gibi belirttiğiniz bir sonraki atlamaya yönlendirilir. Böyle bir yolun oluşturulması, genellikle Zorlamalı tünel olarak adlandırılır. İnternet 'ten sanal makinenize yönelik RDP veya SSH protokollerini kullanan uzak bağlantılar, sonraki atlamanın trafiği nasıl işleydiğine bağlı olarak bu yol ile çalışmayabilir. Zorlamalı tünel etkinleştirilebilir:
    - Siteden siteye VPN kullanırken, *VPN Gateway* sonraki atlama türü ile bir yol oluşturun. [Zorlamalı tüneli yapılandırma](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)hakkında daha fazla bilgi edinin.
    - Bir 0.0.0.0/0 (varsayılan yol), siteden siteye VPN veya ExpressRoute bağlantı hattı kullanılırken sanal ağ geçidi üzerinden BGP üzerinden tanıtıldığında. [Siteden sıteye VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [EXPRESSROUTE](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering)ile BGP kullanma hakkında daha fazla bilgi edinin.
- Sanal ağ eşleme trafiğinin düzgün çalışması için, eşlenen sanal ağın önek aralığı için sonraki atlama türüne sahip bir sistem *yolu bulunmalıdır.* Böyle bir yol yoksa ve sanal ağ eşleme bağlantısı **bağlı** ise:
    - Birkaç saniye bekleyip yeniden deneyin. Yeni oluşturulan bir eşleme bağlantısıdır, bazen yolların bir alt ağdaki tüm ağ arabirimlerine yayılması daha uzun sürer. Sanal ağ eşlemesi hakkında daha fazla bilgi edinmek için bkz. [sanal ağ eşlemesine genel bakış](virtual-network-peering-overview.md) ve [sanal ağ eşlemesini yönetme](virtual-network-manage-peering.md).
    - Ağ güvenlik grubu kuralları iletişimi etkiliyor olabilir. Daha fazla bilgi için bkz. [sanal makine ağ trafiği filtresini tanılama sorunu](diagnose-network-traffic-filter-problem.md).
- Azure, her bir Azure ağ arabirimine varsayılan yollar atabilse de, VM 'ye bağlı birden çok ağ arabiriminiz varsa, VM 'nin işletim sistemi içinde yalnızca birincil ağ arabirimine varsayılan bir yol (0.0.0.0/0) veya ağ geçidi atanır. Bir [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) veya [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) VM 'sine bağlı ikincil ağ arabirimleri için varsayılan bir yol oluşturmayı öğrenin. [Birincil ve ikincil ağ arabirimleri](virtual-network-network-interface-vm.md#constraints)hakkında daha fazla bilgi edinin.

## <a name="additional-diagnosis"></a>Ek tanılama

* Bir konuma giden trafik için sonraki atlama türünü tespit etmek üzere hızlı bir test çalıştırmak için, Azure ağ Izleyicisi 'nin [sonraki atlama](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) özelliğini kullanın. Sonraki atlama, bir sonraki atlama türünün belirtilen konuma giden trafik için ne olduğunu söyler.
* Bir VM 'nin ağ iletişiminin başarısız olmasına neden olan bir yol yoksa, bu sorun VM 'nin işletim sistemi içinde çalışan güvenlik duvarı yazılımının nedeni olabilir
* VPN Gateway veya NVA aracılığıyla şirket içi bir cihaza [tünel trafiğini zorlarsanız](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , cihazlar için yönlendirmeyi nasıl yapılandırdığınıza bağlı olarak internet 'TEN bir VM 'ye bağlanamadıysanız. Cihaz için yapılandırdığınız yönlendirmenin, trafiği VM için ortak veya özel bir IP adresine yönlendirdiğini doğrulayın.
* Ağ Izleyicisi özelliğini kullanarak, giden iletişim sorunlarının yönlendirme, filtreleme ve işletim sistemi nedenlerini belirleme konusunda [sorun giderin](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .

## <a name="next-steps"></a>Sonraki adımlar

- Bir [yol tablosu ve rotasıyla](manage-route-table.md)ilgili tüm görevler, Özellikler ve ayarlar hakkında bilgi edinin.
- [Sonraki atlama türleri, sistem yolları ve Azure 'un bir yolu nasıl seçtiği](virtual-networks-udr-overview.md)hakkında bilgi edinin.
