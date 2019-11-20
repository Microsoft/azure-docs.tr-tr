---
title: Azure genel IP adresi oluşturma, değiştirme veya silme | Microsoft Docs
description: Genel IP adresi oluşturma, değiştirme veya silme hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: 5e71f30723826a18f10d4c8e1b9d34e356c9962b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114629"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Genel IP adresi oluşturma, değiştirme veya silme

Genel IP adresi ve oluşturma, değiştirme ve silme hakkında bilgi edinin. Genel IP adresi, kendi yapılandırılabilir ayarlarını içeren bir kaynaktır. Genel IP adreslerini destekleyen bir Azure kaynağına genel IP adresi atamak şunları sağlar:
- Azure sanal makineler (VM), Azure uygulama ağ geçitleri, Azure yük dengeleyiciler, Azure VPN ağ geçitleri ve diğerleri gibi Internet 'ten kaynağa gelen iletişim. VM, bir yük dengeleyici arka uç havuzunun parçası olduğu ve Yük Dengeleyiciye genel bir IP adresi atandığında, bir VM 'nin kendisine atanmış bir genel IP adresi yoksa, Internet 'ten de sanal makineler gibi bazı kaynaklarla iletişim kurabilirsiniz. Belirli bir Azure hizmetine yönelik bir kaynağa genel bir IP adresi atanıp atanamayacağını veya farklı bir Azure kaynağının genel IP adresi ile iletişim kuramayacağını öğrenmek için, hizmetin belgelerine bakın.
- Öngörülebilir bir IP adresi kullanarak Internet 'e giden bağlantı. Örneğin, bir sanal makine, kendisine atanmış bir genel IP adresi olmadan giden Internet ile iletişim kurabilir, ancak adresi varsayılan olarak Azure tarafından öngörülemeyen bir genel adrese çevrilmiş ağ adresidir. Bir kaynağa genel IP adresi atamak, giden bağlantı için hangi IP adresinin kullanıldığını bilmenizi sağlar. Tahmin edilebilir olsa da, seçilen atama yöntemine bağlı olarak adres değişebilir. Daha fazla bilgi için bkz. [genel IP adresi oluşturma](#create-a-public-ip-address). Azure kaynaklarından giden bağlantılar hakkında daha fazla bilgi edinmek için bkz. [giden bağlantıları anlama](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu makalenin herhangi bir bölümündeki adımları tamamlamadan önce aşağıdaki görevleri doldurun:

- Henüz bir Azure hesabınız yoksa [ücretsiz deneme hesabı](https://azure.microsoft.com/free)için kaydolun.
- Portalı kullanıyorsanız, https://portal.azure.comaçın ve Azure hesabınızla oturum açın.
- Bu makaledeki görevleri tamamlamaya yönelik PowerShell komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/powershell)komutları çalıştırın veya PowerShell 'i bilgisayarınızdan çalıştırarak çalıştırın. Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Bu öğretici, Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.
- Bu makaledeki görevleri gerçekleştirmek için Azure komut satırı arabirimi (CLı) komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da bilgisayarınızdan CLI 'yı çalıştırarak. Bu öğretici, Azure CLı sürüm 2.0.31 veya üstünü gerektirir. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme](/cli/azure/install-azure-cli). Azure CLı 'yi yerel olarak çalıştırıyorsanız, Azure ile bağlantı oluşturmak için `az login` çalıştırmanız da gerekir.

Oturum açtığınızda veya Azure 'a bağlanmak için kullandığınız hesap, [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya [izinlerde](#permissions)listelenen uygun eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır.

Genel IP adreslerinin nominal bir ücreti vardır. Fiyatlandırmayı görüntülemek için [IP adresi fiyatlandırma](https://azure.microsoft.com/pricing/details/ip-addresses) sayfasını okuyun.

## <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma

1. Portalın sol üst köşesinde **+ kaynak oluştur**' u seçin.
2. *Market 'Te ara* kutusuna *genel IP adresini* girin. Arama sonuçlarında **genel IP adresi** göründüğünde seçin.
3. **Genel IP adresi**altında **Oluştur**' u seçin.
4. Aşağıdaki ayarlara ait değerleri girin veya seçin, **genel IP adresi oluştur**altında, **Oluştur**' u seçin.

   |Ayar|Gerekli mi?|Ayrıntılar|
   |---|---|---|
   |Ad|Yes|Ad, seçtiğiniz kaynak grubu içinde benzersiz olmalıdır.|
   |SKU|Yes|SKU 'ların kullanıma sunulmasından önce oluşturulan tüm genel IP adresleri **temel** SKU ortak IP adresleridir. Genel IP adresi oluşturulduktan sonra SKU 'YU değiştiremezsiniz. Tek başına bir sanal makine, bir kullanılabilirlik kümesindeki sanal makineler veya sanal makine ölçek kümeleri, temel veya standart SKU 'Ları kullanabilir. Kullanılabilirlik kümeleri veya ölçek kümeleri içindeki sanal makineler arasında SKU 'Ların karıştırılmasına izin verilmez. **Temel** SKU: kullanılabilirlik alanlarını destekleyen bir bölgede genel IP adresi oluşturuyorsanız, **kullanılabilirlik alanı** ayarı varsayılan olarak *hiçbiri* olarak ayarlanır. Temel genel IP 'Ler kullanılabilirlik bölgelerini desteklemez. **Standart** SKU: Standart SKU genel IP 'si bir sanal makine veya yük dengeleyici ön ucuna ilişkilendirilebilir. Kullanılabilirlik alanlarını destekleyen bir bölgede genel IP adresi oluşturuyorsanız, **kullanılabilirlik alanı** ayarı varsayılan olarak bölgesel olarak *yedekli* olarak ayarlanır. Kullanılabilirlik bölgeleri hakkında daha fazla bilgi için bkz. **kullanılabilirlik alanı** ayarı. Adresi standart yük dengeleyiciye ilişkilendirirseniz standart SKU gereklidir. Standart yük dengeleyiciler hakkında daha fazla bilgi edinmek için bkz. [Azure yük dengeleyici standart SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Standart bir SKU genel IP adresini bir sanal makinenin ağ arabirimine atadığınızda amaçlanan trafiğe bir [ağ güvenlik grubuyla](security-overview.md#network-security-groups) açıkça izin vermeniz gerekir. Bir ağ güvenlik grubu oluşturup ilişkilendirene ve istenen trafiğe açıkça izin verene kadar kaynakla erişim kurma girişimleri başarısız olur.|
   |IP sürümü|Yes| IPv4 veya IPv6 seçin. Genel IPv4 adresleri birkaç Azure kaynağına atanaken, IPv6 genel IP adresi yalnızca Internet 'e yönelik bir yük dengeleyiciye atanabilir. Yük dengeleyici, Azure sanal makinelerine IPv6 trafiği yükünü dengeleyebilir. [Sanal makinelere IPv6 trafiği yük dengelemesi](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)hakkında daha fazla bilgi edinin. **Standart SKU**'Yu seçtiyseniz *IPv6 'yı*seçme seçeneğiniz yoktur. **Standart SKU**kullanırken yalnızca bir IPv4 adresi oluşturabilirsiniz.|
   |IP adresi ataması|Yes|**Dinamik:** Dinamik adresler yalnızca genel bir IP adresi bir Azure kaynağıyla ilişkilendirildikten sonra atanır ve kaynak ilk kez başlatılır. Dinamik adresler, sanal makine gibi bir kaynağa atanırsa, sanal makinenin durdurulması (serbest bırakıldığında) ve sonra yeniden başlatıldığında değişebilir. Bir sanal makine yeniden başlatıldığında veya durdurulmuşsa (ancak serbest bırakılmamışsa) adres aynı kalır. Ortak bir IP adresi kaynağı ilişkilendirildiği bir kaynaktan ilişkisi kesildiğinde dinamik adresler serbest bırakılır. **Statik:** Statik adresler, genel bir IP adresi oluşturulduğunda atanır. Statik adresler genel bir IP adresi kaynağı silinene kadar serbest bırakılır. Adres bir kaynakla ilişkili değilse, adres oluşturulduktan sonra atama yöntemini değiştirebilirsiniz. Adres bir kaynakla ilişkiliyse, atama metodunu değiştiremeyebilirsiniz. **IP sürümü**için *IPv6* ' yı seçerseniz, atama yöntemi *dinamiktir*. **SKU**için *Standart* ' ı seçerseniz, atama yöntemi *statiktir*.|
   |Boşta kalma zaman aşımı (dakika)|Hayır|Bir TCP veya HTTP bağlantısının istemcilere bağlı kalmadan açık tutulması için, etkin tut iletileri göndermek için kaç dakika sürer. **IP sürümü**için IPv6 ' yı seçerseniz bu değer değiştirilemez. |
   |DNS ad etiketi|Hayır|Ad içinde oluşturduğunuz Azure konumunda benzersiz olmalıdır (tüm abonelikler ve tüm müşteriler genelinde). Azure adı ve IP adresini DNS 'ye otomatik olarak kaydeder, böylece ada sahip bir kaynağa bağlanabilirsiniz. Azure, tam DNS adını oluşturmak için *Location.cloudapp.Azure.com* gibi bir varsayılan alt ağ (seçtiğiniz konum sizin seçtiğiniz konumdur), sağladığınız ada ekler. Her iki adres sürümünü de oluşturmayı seçerseniz, aynı DNS adı hem IPv4 hem de IPv6 adreslerine atanır. Azure 'un varsayılan DNS 'i hem IPv4 A hem de IPv6 AAAA ad kayıtlarını içerir ve DNS adı arandığında her iki kayıtla da yanıt verir. İstemci, iletişim kurmak için kullanılacak adresi (IPv4 veya IPv6) seçer. Varsayılan son ek ile DNS ad etiketini kullanmak yerine veya buna ek olarak, genel IP adresine çözümlenen bir özel son ek ile DNS adını yapılandırmak için Azure DNS hizmetini kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure genel IP adresiyle Azure DNS kullanma](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |IPv6 (veya IPv4) adresi oluşturma|Hayır| IPv6 veya IPv4 'Ün görüntülenip görüntülenmeyeceğini, **IP sürümü**için seçtiğiniz öğesine bağlıdır. Örneğin, **IP sürümü**için **IPv4** ' ü seçerseniz, burada **IPv6** görüntülenir. **SKU**için *Standart* ' ı seçerseniz, bir IPv6 adresi oluşturma seçeneğiniz yoktur.
   |Ad (yalnızca **bir IPv6 (veya IPv4) adresi oluştur** onay kutusunu işaretlediğinizde görünür)|Evet, **IPv6 oluştur** (veya IPv4) onay kutusunu seçin.|Ad, bu listedeki ilk **ad** için girdiğiniz adla farklı olmalıdır. Hem IPv4 hem de IPv6 adresi oluşturmayı seçerseniz, Portal kendisine atanan her IP adresi sürümüne sahip olan iki ayrı genel IP adresi kaynağı oluşturur.|
   |IP adresi ataması (yalnızca **bir IPv6 (veya IPv4) adresi oluştur** onay kutusunu işaretlediğinizde görünür)|Evet, **IPv6 oluştur** (veya IPv4) onay kutusunu seçin.|Onay kutusu **bir IPv4 adresi oluştur**ifadesini içeriyorsa, bir atama yöntemi seçebilirsiniz. Onay kutusu **bir IPv6 adresi oluşturmazsa**, **dinamik**olması gerektiği için atama yöntemi seçemezsiniz.|
   |Abonelik|Yes|Genel IP adresini ilişkilendirmek istediğiniz kaynakla aynı [abonelikte](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) bulunmalıdır.|
   |Kaynak grubu|Yes|, Genel IP adresini ilişkilendirmek istediğiniz kaynakla aynı veya farklı bir [kaynak grubunda](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) bulunabilir.|
   |Konum|Yes|Genel IP adresini ilişkilendirmek istediğiniz kaynak olarak, bölge olarak da adlandırılan aynı [konumda](https://azure.microsoft.com/regions)bulunmalıdır.|
   |Kullanılabilirlik alanı| Hayır | Bu ayar yalnızca desteklenen bir konum seçtiğinizde görüntülenir. Desteklenen konumların bir listesi için bkz. [Kullanılabilirlik alanlarına genel bakış](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). **Temel** SKU 'yu seçtiyseniz, sizin için *hiçbiri* otomatik olarak seçili değildir. Belirli bir bölgeyi garanti etmek isterseniz belirli bir bölgeyi seçebilirsiniz. Seçim bölge yedekli değil. **Standart** SKU 'yu seçtiyseniz: bölge yedekli, sizin için otomatik olarak seçilir ve veri yolunuzu bölge hatasına dayanıklı hale getirir. Bölge hatasına dayanıklı olmayan belirli bir bölgenin garantisi yapmayı tercih ediyorsanız, belirli bir bölgeyi seçebilirsiniz.

**Komut**

Portal iki genel IP adresi kaynağı (bir IPv4 ve bir IPv6) oluşturma seçeneğini sunsa da, aşağıdaki CLı ve PowerShell komutları tek bir IP sürümü veya diğeri için adresi olan bir kaynak oluşturur. Her IP sürümü için bir tane olmak üzere iki genel IP adresi kaynağı istiyorsanız, genel IP adresi kaynakları için farklı adlar ve sürümler belirterek komutu iki kez çalıştırmanız gerekir.

|Araç|Komut|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Genel IP adresini görüntüleme, ayarlarını değiştirme veya silme

1. Azure portal üst kısmında bulunan metin *arama kaynaklarını* içeren kutuya *genel IP adresi*yazın. Arama sonuçlarında **genel IP adresleri** görüntülendiğinde, bunu seçin.
2. Görüntülemek istediğiniz genel IP adresinin adını seçin, ayarları değiştirin veya listeden silin.
3. Genel IP adresini görüntülemek, silmek veya değiştirmek istediğinize bağlı olarak aşağıdaki seçeneklerden birini doldurun.
   - **Görünüm**: **genel bakış** bölümü, genel IP adresi için ilişkilendirildiği ağ arabirimi gibi (adres bir ağ arabirimiyle ilişkiliyse) anahtar ayarlarını gösterir. Portal, adresin sürümünü (IPv4 veya IPv6) göstermez. Sürüm bilgilerini görüntülemek için PowerShell veya CLı komutunu kullanarak genel IP adresini görüntüleyin. IP adresi sürümü IPv6 ise, atanan adres Portal, PowerShell veya CLı tarafından gösterilmez.
   - **Sil**: genel IP adresini silmek Için **genel bakış** bölümünde **Sil** ' i seçin. Adres Şu anda bir IP yapılandırmasıyla ilişkilendiriliyorsa silinemez. Adres Şu anda bir yapılandırma ile ilişkili ise, IP yapılandırmasından adresin ilişkilendirmesini **kaldırmak için ilişkiyi kaldırın '** ı seçin.
   - **Değişiklik**: **yapılandırma**' yı seçin. [Genel IP adresi oluşturma](#create-a-public-ip-address)içindeki 4. adımdaki bilgileri kullanarak ayarları değiştirin. Statik olan bir IPv4 adresinin atamasını dinamik olarak değiştirmek için, önce genel IPv4 adresinin ilişkilendirildiği IP yapılandırmasından ilişkilendirmesini kaldırmanız gerekir. Daha sonra atama yöntemini dinamik olarak değiştirebilir ve IP adresini aynı IP yapılandırması, farklı bir yapılandırma ile ilişkilendirmek için **ilişkilendir** ' i seçebilir veya ilişkiyi kaldırın. Genel bir IP adresinin ilişkilendirmesini kaldırmak için **genel bakış** bölümünde **ilişkiyi kaldırın ' ı seçin.**

   >[!WARNING]
   >Atama yöntemini statik olarak dinamik olarak değiştirdiğinizde, genel IP adresine atanan IP adresini kaybedersiniz. Azure genel DNS sunucuları, statik veya dinamik adresler ile herhangi bir DNS ad etiketi (Eğer tanımlıysa) arasında bir eşleme koruurken, sanal makine durdurulmuş (serbest bırakıldı) durumda olduktan sonra başlatıldığında dinamik bir IP adresi değişebilir. Adresin değiştirilmesini engellemek için statik bir IP adresi atayın.

**Komut**

|Araç|Komut|
|---|---|
|CLI|Genel IP adreslerini listelemek için [az Network public-ip List](/cli/azure/network/public-ip#az-network-public-ip-list) , [az Network public-ip](/cli/azure/network/public-ip#az-network-public-ip-show) ayarları göstermek için show; güncelleştirmek için [az Network public-ip Update](/cli/azure/network/public-ip#az-network-public-ip-update) ; [az Network PUBLIC-](/cli/azure/network/public-ip#az-network-public-ip-delete) silmek için Delete|
|PowerShell|[Get-azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) , genel bir IP adresi nesnesi almak ve ayarlarını görüntülemek için [set-azpublicıpaddress](/powershell/module/az.network/set-azpublicipaddress) , ayarları güncelleştirme; [Remove-Azpublicıpaddress](/powershell/module/az.network/remove-azpublicipaddress) öğesini Sil|

## <a name="assign-a-public-ip-address"></a>Genel IP adresi atama

Aşağıdaki kaynaklara genel IP adresi atamayı öğrenin:

- Bir [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM (oluştururken) veya [var olan bir VM](virtual-network-network-interface-addresses.md#add-ip-addresses) 'ye
- [Internet 'e yönelik Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure VPN Gateway kullanarak siteden siteye bağlantı](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure sanal makine ölçek kümesi](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>İzinler

Genel IP adreslerinde görevler gerçekleştirmek için, hesabınız [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya aşağıdaki tabloda listelenen uygun eylemlere atanmış [özel](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) bir role atanmalıdır:

| Eylem                                                             | Ad                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Genel IP adresini oku                                          |
| Microsoft. Network/Publicıpaddresses/Write                          | Genel IP adresi oluşturma veya güncelleştirme                           |
| Microsoft. Network/publicIPAddresses/Delete                         | Genel IP adresini Sil                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Genel IP adresini bir kaynakla ilişkilendir                    |

## <a name="next-steps"></a>Sonraki adımlar

- [PowerShell](powershell-samples.md) veya [Azure CLI](cli-samples.md) örnek betikleri kullanarak veya Azure [Kaynak Yöneticisi şablonlarını](template-samples.md) kullanarak genel IP adresi oluşturma
- Genel IP adresleri için [Azure ilkesi](policy-samples.md) oluşturma ve uygulama
