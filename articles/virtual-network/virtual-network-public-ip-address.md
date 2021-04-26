---
title: Genel IP adreslerini yönetme | Microsoft Docs
titleSuffix: Azure Virtual Network
description: Genel IP adreslerini yönetin.  Ayrıca genel IP adresinin, kendi yapılandırılabilir ayarlarına sahip bir kaynak olduğunu da öğrenin.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: 3277d5836d85f1071b7aa169cc83896934a2f63d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872452"
---
# <a name="manage-public-ip-addresses"></a>Genel IP adreslerini yönetme

Genel IP adresi ve oluşturma, değiştirme ve silme hakkında bilgi edinin. Genel IP adresi, kendi yapılandırılabilir ayarlarını içeren bir kaynaktır. Genel IP adreslerini destekleyen bir Azure kaynağına genel IP adresi atamak şunları sağlar:
- Azure sanal makineler (VM), Azure uygulama ağ geçitleri, Azure yük dengeleyiciler, Azure VPN ağ geçitleri ve diğerleri gibi Internet 'ten kaynağa gelen iletişim. VM, bir yük dengeleyici arka uç havuzunun parçası olduğu ve Yük Dengeleyiciye genel bir IP adresi atandığında, bir VM 'nin kendisine atanmış bir genel IP adresi yoksa, Internet 'ten de sanal makineler gibi bazı kaynaklarla iletişim kurabilirsiniz. Belirli bir Azure hizmetine yönelik bir kaynağa genel bir IP adresi atanıp atanamayacağını veya farklı bir Azure kaynağının genel IP adresi ile iletişim kuramayacağını öğrenmek için, hizmetin belgelerine bakın.
- Öngörülebilir bir IP adresi kullanarak Internet 'e giden bağlantı. Örneğin, bir sanal makine, kendisine atanmış bir genel IP adresi olmadan giden Internet ile iletişim kurabilir, ancak adresi varsayılan olarak Azure tarafından öngörülemeyen bir genel adrese çevrilmiş ağ adresidir. Bir kaynağa genel IP adresi atamak, giden bağlantı için hangi IP adresinin kullanıldığını bilmenizi sağlar. Tahmin edilebilir olsa da, seçilen atama yöntemine bağlı olarak adres değişebilir. Daha fazla bilgi için bkz. [genel IP adresi oluşturma](#create-a-public-ip-address). Azure kaynaklarından giden bağlantılar hakkında daha fazla bilgi edinmek için bkz. [giden bağlantıları anlama](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu makalenin herhangi bir bölümündeki adımları tamamlamadan önce aşağıdaki görevleri doldurun:

- Henüz bir Azure hesabınız yoksa [ücretsiz deneme hesabı](https://azure.microsoft.com/free)için kaydolun.
- Portalı kullanıyorsanız, açın https://portal.azure.com ve Azure hesabınızla oturum açın.
- Bu makaledeki görevleri tamamlamaya yönelik PowerShell komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/powershell)komutları çalıştırın veya PowerShell 'i bilgisayarınızdan çalıştırarak çalıştırın. Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Bu öğretici, Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.
- Bu makaledeki görevleri gerçekleştirmek için Azure komut satırı arabirimi (CLı) komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da bilgisayarınızdan CLI 'yı çalıştırarak. Bu öğretici, Azure CLı sürüm 2.0.31 veya üstünü gerektirir. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli). Azure CLı 'yi yerel olarak çalıştırıyorsanız, `az login` Azure ile bir bağlantı oluşturmak için çalıştırmanız da gerekir.

Oturum açtığınızda veya Azure 'a bağlanmak için kullandığınız hesap, [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya [izinlerde](#permissions)listelenen uygun eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır.

Genel IP adreslerinin nominal bir ücreti vardır. Fiyatlandırmayı görüntülemek için [IP adresi fiyatlandırma](https://azure.microsoft.com/pricing/details/ip-addresses) sayfasını okuyun.

## <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma

Portal, PowerShell veya CLı kullanarak genel IP adresleri oluşturma yönergeleri için lütfen aşağıdaki sayfalara bakın:

 * [Genel IP adresi oluşturma - portal](./create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [Genel IP adresi oluşturma - PowerShell](./create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [Genel IP adresi oluşturma - Azure CLI](./create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)

>[!NOTE]
>Portal iki genel IP adresi kaynağı (bir IPv4 ve bir IPv6) oluşturma seçeneğini sunsa da, PowerShell ve CLı komutları tek bir IP sürümü veya diğeri için adresi olan bir kaynak oluşturur. Her IP sürümü için bir tane olmak üzere iki genel IP adresi kaynağı istiyorsanız, genel IP adresi kaynakları için farklı adlar ve IP sürümleri belirterek komutu iki kez çalıştırmanız gerekir.

Oluşturma sırasında genel IP adresinin belirli öznitelikleri hakkında daha fazla ayrıntı için aşağıdaki tabloya bakın.

   |Ayar|Gerekli mi?|Ayrıntılar|
   |---|---|---|
   |IP sürümü|Yes| IPv4 veya IPv6 veya her Ikisini birden seçin. Her Ikisinin de belirlenmesi, 2 genel IP adresi oluşturma-1 IPv4 adresi ve 1 IPv6 adresi oluşmasına neden olur. [Azure sanal ağları 'Nda IPv6](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)hakkında daha fazla bilgi edinin.|
   |SKU|Yes|SKU 'ların kullanıma sunulmasından önce oluşturulan tüm genel IP adresleri **temel** SKU ortak IP adresleridir. Genel IP adresi oluşturulduktan sonra SKU 'YU değiştiremezsiniz. Tek başına bir sanal makine, bir kullanılabilirlik kümesindeki sanal makineler veya sanal makine ölçek kümeleri, temel veya standart SKU 'Ları kullanabilir. Kullanılabilirlik kümeleri veya ölçek kümelerinde veya tek başına VM 'Lerde bulunan sanal makineler arasında SKU 'Ların karıştırılmasına izin verilmez. **Temel** SKU: kullanılabilirlik alanlarını destekleyen bir bölgede genel IP adresi oluşturuyorsanız, **kullanılabilirlik alanı** ayarı varsayılan olarak *hiçbiri* olarak ayarlanır. Temel genel IP 'Ler kullanılabilirlik bölgelerini desteklemez. **Standart** SKU: Standart SKU genel IP 'si bir sanal makine veya yük dengeleyici ön ucuna ilişkilendirilebilir. Kullanılabilirlik alanlarını destekleyen bir bölgede genel IP adresi oluşturuyorsanız, **kullanılabilirlik alanı** ayarı varsayılan olarak bölgesel olarak *yedekli* olarak ayarlanır. Kullanılabilirlik bölgeleri hakkında daha fazla bilgi için bkz. **kullanılabilirlik alanı** ayarı. Adresi standart yük dengeleyiciye ilişkilendirirseniz standart SKU gereklidir. Standart yük dengeleyiciler hakkında daha fazla bilgi edinmek için bkz. [Azure yük dengeleyici standart SKU](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Standart bir SKU genel IP adresini bir sanal makinenin ağ arabirimine atadığınızda amaçlanan trafiğe bir [ağ güvenlik grubuyla](./network-security-groups-overview.md#network-security-groups) açıkça izin vermeniz gerekir. Bir ağ güvenlik grubu oluşturup ilişkilendirene ve istenen trafiğe açıkça izin verene kadar kaynakla erişim kurma girişimleri başarısız olur.|
   |Katman|Yes|IP adresinin bir bölge (**Bölgesel**) ile ilişkili olduğunu veya birden çok bölgeden "her noktaya" (**genel**) olduğunu gösterir. *"Genel katman" IP 'Si standart IP 'ler için Önizleme işlevselliği olduğunu ve şu anda yalnızca çapraz bölge Load Balancer için kullanıldığını unutmayın*.|
   |Name|Yes|Ad, seçtiğiniz kaynak grubu içinde benzersiz olmalıdır.|
   |IP adresi ataması|Yes|**Dinamik:** Dinamik adresler yalnızca genel bir IP adresi bir Azure kaynağıyla ilişkilendirildikten sonra atanır ve kaynak ilk kez başlatılır. Dinamik adresler, sanal makine gibi bir kaynağa atanırsa, sanal makinenin durdurulması (serbest bırakıldığında) ve sonra yeniden başlatıldığında değişebilir. Bir sanal makine yeniden başlatıldığında veya durdurulmuşsa (ancak serbest bırakılmamışsa) adres aynı kalır. Ortak bir IP adresi kaynağı ilişkilendirildiği bir kaynaktan ilişkisi kesildiğinde dinamik adresler serbest bırakılır. **Statik:** Statik adresler, genel bir IP adresi oluşturulduğunda atanır. Statik adresler genel bir IP adresi kaynağı silinene kadar serbest bırakılır. Adres bir kaynakla ilişkili değilse, adres oluşturulduktan sonra atama yöntemini değiştirebilirsiniz. Adres bir kaynakla ilişkiliyse, atama metodunu değiştiremeyebilirsiniz. **IP sürümü** için *IPv6* ' yı seçerseniz, atama yöntemi temel SKU için *dinamik* olmalıdır.  Standart SKU adresleri hem IPv4 hem de IPv6 için *statiktir* . |
   |Boşta kalma zaman aşımı (dakika)|No|Bir TCP veya HTTP bağlantısının istemcilere bağlı kalmadan açık tutulması için, etkin tut iletileri göndermek için kaç dakika sürer. **IP sürümü** için IPv6 ' yı seçerseniz bu değer değiştirilemez. |
   |DNS ad etiketi|No|Ad içinde oluşturduğunuz Azure konumunda benzersiz olmalıdır (tüm abonelikler ve tüm müşteriler genelinde). Azure adı ve IP adresini DNS 'ye otomatik olarak kaydeder, böylece ada sahip bir kaynağa bağlanabilirsiniz. Azure, tam DNS adını oluşturmak için *Location.cloudapp.Azure.com* gibi bir varsayılan alt ağ (seçtiğiniz konum sizin seçtiğiniz konumdur), sağladığınız ada ekler. Her iki adres sürümünü de oluşturmayı seçerseniz, aynı DNS adı hem IPv4 hem de IPv6 adreslerine atanır. Azure 'un varsayılan DNS 'i hem IPv4 A hem de IPv6 AAAA ad kayıtlarını içerir ve DNS adı arandığında her iki kayıtla da yanıt verir. İstemci, iletişim kurmak için kullanılacak adresi (IPv4 veya IPv6) seçer. Varsayılan son ek ile DNS ad etiketini kullanmak yerine veya buna ek olarak, genel IP adresine çözümlenen bir özel son ek ile DNS adını yapılandırmak için Azure DNS hizmetini kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure genel IP adresiyle Azure DNS kullanma](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Ad (yalnızca **her Ikisinin de** IP sürümünü seçerseniz görünür)|Evet, **her Ikisinin de** IP sürümünü seçerseniz|Ad, bu listedeki ilk **ad** için girdiğiniz adla farklı olmalıdır. Hem IPv4 hem de IPv6 adresi oluşturmayı seçerseniz, Portal kendisine atanan her IP adresi sürümüne sahip olan iki ayrı genel IP adresi kaynağı oluşturur.|
   |IP adresi ataması (yalnızca **IP sürümü '** nü seçerseniz görünür)|Evet, **her Ikisinin de** IP sürümünü seçerseniz|Yukarıdaki IP adresi atamasıyla aynı kısıtlamalar|
   |Abonelik|Yes|Ortak IP 'yi ilişkilendirebileceğiniz kaynakla aynı [abonelikte](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) bulunmalıdır.|
   |Kaynak grubu|Yes|, Genel IP 'yi ilişkilendirebileceğiniz kaynakla aynı veya farklı bir [kaynak grubunda](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) bulunabilir.|
   |Konum|Evet|Genel IP 'yi ilişkilendirebileceğiniz kaynak olarak, bölge olarak da adlandırılan aynı [konumda](https://azure.microsoft.com/regions)bulunmalıdır.|
   |Kullanılabilirlik alanı| No | Bu ayar yalnızca desteklenen bir konum seçtiğinizde görüntülenir. Desteklenen konumların bir listesi için bkz. [Kullanılabilirlik alanlarına genel bakış](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). **Temel** SKU 'yu seçtiyseniz, sizin için *hiçbiri* otomatik olarak seçili değildir. Belirli bir bölgeyi garanti etmek isterseniz belirli bir bölgeyi seçebilirsiniz. Seçim bölge yedekli değil. **Standart** SKU 'yu seçtiyseniz: bölge yedekli, sizin için otomatik olarak seçilir ve veri yolunuzu bölge hatasına dayanıklı hale getirir. Bölge hatasına dayanıklı olmayan belirli bir bölgenin garantisi yapmayı tercih ediyorsanız, belirli bir bölgeyi seçebilirsiniz.

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>Genel IP adresini görüntüleme, değiştirme veya silme

   - **Görünüm/listeleme**: SKU, adres, uygulanabilir ilişki (örn. sanal makine nıc, Load Balancer ön uç) dahil olmak üzere genel IP 'nin ayarlarını gözden geçirmek için.
   - **Değiştir**: boş zaman AŞıMı, DNS ad etiketi veya atama yöntemi gıbı [genel IP adresi oluşturma](#create-a-public-ip-address)adım 4 ' teki bilgileri kullanarak ayarları değiştirmek için.  (Genel bir IP SKU 'sunu temel sunucudan standart sürümüne yükseltmenin tam işlemi için bkz. [Azure genel IP adreslerini yükseltme](./virtual-network-public-ip-address-upgrade.md).)
   >[!WARNING]
   >Statik bir genel IP adresi atamasını dinamik olarak değiştirmek için, önce geçerli herhangi bir IP yapılandırmasından adresin ilişkilendirmesini kaldırmanız gerekir (bkz. **Delete** bölümü).  Ayrıca, atama yöntemini statik iken dinamik olarak değiştirdiğinizde, genel IP adresine atanan IP adresini kaybedersiniz. Azure genel DNS sunucuları, statik veya dinamik adresler ile herhangi bir DNS ad etiketi (Eğer tanımlıysa) arasında bir eşleme koruurken, sanal makine durdurulmuş (serbest bırakıldı) durumda olduktan sonra başlatıldığında dinamik bir IP adresi değişebilir. Adresin değiştirilmesini engellemek için statik bir IP adresi atayın.
   
|İşlem|Azure portalı|Azure PowerShell|Azure CLI|
|---|---|---|---|
|Görünüm | Genel IP 'nin **genel bakış** bölümünde |Genel IP adresi nesnesini almak ve ayarlarını görüntülemek için [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress)| [az Network public-ip](/cli/azure/network/public-ip#az_network_public_ip_show) ayarları göstermek için show|
|Liste | **Genel IP adresleri** kategorisi altında |Bir veya daha fazla genel IP adresi nesnesini almak ve ayarlarını görüntülemek için [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress)|Genel IP adreslerini listelemek için [az Network public-ip List](/cli/azure/network/public-ip#az_network_public_ip_list)|
|Değiştir | İlişkisi kesilen bir IP için, boşta kalma zaman aşımını, DNS adı etiketini değiştirmek veya temel IP 'nin statik ve dinamik olarak atamasını değiştirmek için **yapılandırma** ' yı seçin  |Ayarları güncelleştirmek için [set-Azpublicıpaddress](/powershell/module/az.network/set-azpublicipaddress) |güncelleştirmek için [az Network public-ip Update](/cli/azure/network/public-ip#az_network_public_ip_update) |

   - **Sil**: genel IP 'leri silme Işlemi, genel IP nesnesinin HERHANGI bir IP yapılandırmasıyla veya sanal makine NIC ile ilişkilendirilmemiş olmasını gerektirir. Daha fazla ayrıntı için aşağıdaki tabloya bakın.

|Kaynak|Azure portalı|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[Sanal Makine](./remove-public-ip-address-vm.md)|IP adresinin NIC yapılandırmasından ilişkilendirmesini **kaldırmak için ilişkiyi kaldırın '** ı seçin ve **Sil**' i seçin.|IP adresinin NIC yapılandırmasından ilişkilendirmesini kaldırmak için [set-Azpublicıpaddress](/powershell/module/az.network/set-azpublicipaddress) ; [Remove-Azpublicıpaddress](/powershell/module/az.network/remove-azpublicipaddress) öğesini Sil|[az Network public-ip Update--](/cli/azure/network/public-ip#az_network_public_ip_update) IP adresinin NIC yapılandırmasından ilişkilendirmesini kaldırmak için kaldırın; [az Network PUBLIC-](/cli/azure/network/public-ip#az_network_public_ip_delete) silmek için Delete |
|Load Balancer ön uç | Kullanılmayan bir genel IP adresine gidip **ilişkilendir** ' i seçin ve bunu değiştirmek Için Ilgili ön uç IP yapılandırması ile Load Balancer seçin (eski IP, VM için aynı yöntem kullanılarak silinebilir)  | Yeni ön uç IP yapılandırmasını genel Load Balancer ilişkilendirmek için [set-Azloadbalancerfrontendıpconfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) ; Silinecek [-Azpublicıpaddress](/powershell/module/az.network/remove-azpublicipaddress) ; , birden fazla varsa ön uç IP yapılandırmasını kaldırmak için [Remove-Azloadbalancerfrontendıpconfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) komutunu da kullanabilir |[az Network lb ön uç-IP Update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) Ile yeni ön uç IP yapılandırmasını genel Load Balancer ilişkilendirebilirsiniz; Silinecek [-Azpublicıpaddress](/powershell/module/az.network/remove-azpublicipaddress) ; , birden fazla varsa ön uç IP yapılandırmasını kaldırmak için [az Network lb ön uç-IP Delete](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) komutunu da kullanabilirsiniz|
|Güvenlik Duvarı|Yok| Güvenlik duvarını serbest bırakma ve tüm IP yapılandırmasını kaldırma [()](../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) | [az Network Firewall IP-Config Delete](/cli/azure/network/firewall/ip-config#az_network_firewall_ip_config_delete) , IP 'yi kaldırmak için (ancak önce PowerShell kullanılmalıdır)|

## <a name="virtual-machine-scale-sets"></a>Sanal Makine Ölçek Kümeleri

Ortak IP 'Ler içeren bir sanal makine ölçek kümesi kullanırken, tek tek sanal makine örnekleriyle ilişkili ayrı genel IP nesneleri yoktur. Ancak, bir genel IP öneki nesnesi, [IP örneğini oluşturmak için kullanılabilir](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).

Bir sanal makine ölçek kümesindeki genel IP 'Leri listelemek için PowerShell ([Get-Azpublicıpaddress-VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) veya CLI ([az VMSS List-instance-public-IP](/cli/azure/vmss#az_vmss_list_instance_public_ips)) kullanabilirsiniz.

Daha fazla bilgi için bkz. [Azure sanal makine ölçek kümeleri Için ağ](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine).

## <a name="assign-a-public-ip-address"></a>Genel IP adresi atama

Aşağıdaki kaynaklara genel IP adresi atamayı öğrenin:

- Bir [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makinesi (oluştururken) veya [var olan bir sanal makineye](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Ortak Load Balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway](../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN Gateway kullanarak siteden siteye bağlantı](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Sanal makine ölçek kümesi](../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>İzinler

Genel IP adreslerinde görevler gerçekleştirmek için, hesabınız [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya aşağıdaki tabloda listelenen uygun eylemlere atanmış [özel](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) bir role atanmalıdır:

| Eylem                                                             | Name                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft. Network/publicIPAddresses/Read                           | Genel IP adresini oku                                          |
| Microsoft. Network/Publicıpaddresses/Write                          | Genel IP adresi oluşturma veya güncelleştirme                           |
| Microsoft. Network/publicIPAddresses/Delete                         | Genel IP adresini Sil                                     |
| Microsoft. Network/publicIPAddresses/JOIN/Action                    | Genel IP adresini bir kaynakla ilişkilendir                    |

## <a name="next-steps"></a>Sonraki adımlar

- [PowerShell](powershell-samples.md) veya [Azure CLI](cli-samples.md) örnek betikleri kullanarak veya Azure [Kaynak Yöneticisi şablonlarını](template-samples.md) kullanarak genel IP adresi oluşturma
- Genel IP adresleri için [Azure ilke tanımları](./policy-reference.md) oluşturma ve atama
