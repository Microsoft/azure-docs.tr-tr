---
title: 'Azure VPN Gateway: P2S yönlendirme hakkında'
description: Farklı işletim sistemleri, uzaktan erişim protokolleri ve sanal ağ yapılandırmalarına yönelik Azure Noktadan siteye VPN yönlendirmesi hakkında bilgi edinin.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 0b9b8ba555cddd56c49c750709e69ec180291c95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91827169"
---
# <a name="about-point-to-site-vpn-routing"></a>Noktadan Siteye VPN yönlendirme hakkında

Bu makale, Azure Noktadan siteye VPN yönlendirmesinin nasıl davranacağını anlamanıza yardımcı olur. P2S VPN yönlendirme davranışı, istemci işletim sistemine, VPN bağlantısı için kullanılan protokole ve sanal ağların (VNet 'ler) birbirlerine nasıl bağlı olduğunu bağımlıdır.

Azure Şu anda uzaktan erişim, Ikev2 ve SSTP için iki protokolü desteklemektedir. Ikev2, Windows, Linux, macOS, Android ve iOS gibi birçok istemci işletim sisteminde desteklenir. SSTP yalnızca Windows 'ta desteklenir. Ağınızın topolojisine bir değişiklik yaparsanız ve Windows VPN istemcileriniz varsa, değişikliklerin istemciye uygulanması için Windows istemcileri için VPN istemci paketinin indirilip yeniden yüklenmesi gerekir.

> [!NOTE]
> Bu makale yalnızca Ikev2 için geçerlidir.
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>Diyagramlar hakkında

Bu makalede birçok farklı diyagram vardır. Her bölümde farklı bir topoloji veya yapılandırma gösterilmektedir. Bu makalenin amaçları doğrultusunda, siteden siteye (S2S) ve VNet 'ten VNet 'e bağlantılar, her ikisi de IPSec tünellerinin yanı sıra aynı şekilde çalışır. Bu makaledeki tüm VPN ağ geçitleri rota tabanlıdır.

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>Bir yalıtılmış VNet

Bu örnekteki Noktadan siteye VPN Ağ Geçidi bağlantısı, başka bir sanal ağla bağlantılı veya eşlenmiş olmayan bir VNet için (VNet1). Bu örnekte, istemciler VNet1 'e erişebilir.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg" alt-text="Yalıtılmış VNet yönlendirmesi" lightbox="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg":::

### <a name="address-space"></a>Adres alanı

* VNet1:10.1.0.0/16

### <a name="routes-added"></a>Eklenen yollar

* Windows istemcilerine eklenen yollar: 10.1.0.0/16, 192.168.0.0/24

* Windows olmayan istemcilere eklenen yollar: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows istemcileri VNet1 erişebilir

* Windows dışı istemciler VNet1 erişebilir

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>Birden çok eşlenen sanal ağlar

Bu örnekte, Noktadan siteye VPN Ağ Geçidi bağlantısı VNet1 içindir. VNet1, VNet2 ile eşlenmez. VNet 2, VNet3 ile eşlenmez. VNet1, Ile vnet4 arasında ile eşlenmez. VNet1 ve VNet3 arasında doğrudan eşleme yoktur. VNet1 "Gateway aktarımına Izin ver" ve VNet2 ve Ile vnet4 arasında "uzak ağ geçitlerini kullan" özelliği etkinleştirilmiş.

Windows kullanan istemciler doğrudan eşlenmiş sanal ağlara erişebilir, ancak VNet eşlemesi veya ağ topolojisi üzerinde herhangi bir değişiklik yapılırsa VPN istemcisinin yeniden indirilmesi gerekir. Windows dışı istemciler, doğrudan eşlenmiş sanal ağlara erişebilir. Erişim geçişli değildir ve yalnızca doğrudan eşlenmiş VNET 'ler ile sınırlıdır.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg" alt-text="Birden çok eşlenen sanal ağlar" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg":::

### <a name="address-space"></a>Adres alanı:

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* Ile vnet4 arasında: 10.4.0.0/16

### <a name="routes-added"></a>Eklenen yollar

* Windows istemcilerine eklenen yollar: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Windows olmayan istemcilere eklenen yollar: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows istemcileri VNet1, VNet2 ve Ile vnet4 arasında erişebilir, ancak herhangi bir topoloji değişikliklerinin etkili olması için VPN istemcisinin yeniden indirilmesi gerekir.

* Windows olmayan istemciler VNet1, VNet2 ve Ile vnet4 arasında 'ye erişebilir

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>S2S VPN kullanılarak bağlı birden çok VNET

Bu örnekte, Noktadan siteye VPN Ağ Geçidi bağlantısı VNet1 içindir. VNet1, siteden siteye VPN bağlantısı kullanılarak VNet2 'e bağlanır. VNet2, siteden siteye VPN bağlantısı kullanılarak VNet3 'e bağlanır. VNet1 ve VNet3 arasında doğrudan eşleme veya siteden siteye VPN bağlantısı yoktur. Tüm siteden siteye bağlantılar yönlendirme için BGP 'yi çalıştırmıyor.

Windows veya desteklenen başka bir işletim sistemi kullanan istemciler yalnızca VNet1 erişebilir. Ek VNET 'lere erişmek için BGP kullanılmalıdır.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg" alt-text="Birden çok VNET ve S2S" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg":::

### <a name="address-space"></a>Adres alanı

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

### <a name="routes-added"></a>Eklenen yollar

* Windows istemcilerine eklenen yollar: 10.1.0.0/16, 192.168.0.0/24

* Windows olmayan istemcilere eklenen yollar: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows istemcileri yalnızca VNet1 erişebilir

* Windows olmayan istemciler yalnızca VNet1 erişebilir

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>S2S VPN (BGP) kullanılarak bağlı birden fazla sanal ağ

Bu örnekte, Noktadan siteye VPN Ağ Geçidi bağlantısı VNet1 içindir. VNet1, siteden siteye VPN bağlantısı kullanılarak VNet2 'e bağlanır. VNet2, siteden siteye VPN bağlantısı kullanılarak VNet3 'e bağlanır. VNet1 ve VNet3 arasında doğrudan eşleme veya siteden siteye VPN bağlantısı yoktur. Tüm siteden siteye bağlantılar yönlendirme için BGP çalıştırıyor.

Windows veya desteklenen başka bir işletim sistemi kullanan istemciler, siteden siteye VPN bağlantısı kullanılarak bağlı olan tüm sanal ağlara erişebilir, ancak bağlı VNET 'lere olan yolların Windows istemcilerine el ile eklenmesi gerekir.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg" alt-text="Birden çok VNET ve S2S (BGP)" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg":::

### <a name="address-space"></a>Adres alanı

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

### <a name="routes-added"></a>Eklenen yollar

* Windows istemcilerine eklenen yollar: 10.1.0.0/16

* Windows olmayan istemcilere eklenen yollar: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows istemcileri VNet1, VNet2 ve VNet3 erişebilir, ancak VNet2 ve VNet3 yollarının el ile eklenmesi gerekir.

* Windows olmayan istemciler VNet1, VNet2 ve VNet3 'ye erişebilir

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>Bir sanal ağ ve şube ofisi

Bu örnekte, Noktadan siteye VPN Ağ Geçidi bağlantısı VNet1 içindir. VNet1, başka bir sanal ağla bağlantılı değildir/eşlenmez, ancak BGP çalıştırmayan siteden siteye VPN bağlantısı aracılığıyla şirket içi siteye bağlanır.

Windows ve Windows dışı istemciler yalnızca VNet1 'e erişebilir.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg" alt-text="VNet ve şube ofisi ile yönlendirme" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg":::

### <a name="address-space"></a>Adres alanı

* VNet1:10.1.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Eklenen yollar

* Windows istemcilerine eklenen yollar: 10.1.0.0/16, 192.168.0.0/24

* Windows olmayan istemcilere eklenen yollar: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows istemcileri yalnızca VNet1 erişebilir

* Windows olmayan istemciler yalnızca VNet1 erişebilir

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>Bir sanal ağ ve şube ofisi (BGP)

Bu örnekte, Noktadan siteye VPN Ağ Geçidi bağlantısı VNet1 içindir. VNet1 bağlı değildir veya başka bir sanal ağla eşlenmez, ancak BGP çalıştıran siteden siteye VPN bağlantısı aracılığıyla şirket içi siteye (site1) bağlanır.

Windows istemcileri VNet 'e ve şube ofise (site1) erişebilir, ancak site1 'e giden yolların el ile istemciye eklenmesi gerekir. Windows dışı istemciler, VNet 'e ve şirket içi şube ofisine erişebilir.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg" alt-text="VNet ve şube ofisi ile yönlendirme" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg":::

### <a name="address-space"></a>Adres alanı

* VNet1:10.1.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Eklenen yollar

* Windows istemcilerine eklenen yollar: 10.1.0.0/16, 192.168.0.0/24

* Windows olmayan istemcilere eklenen yollar: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows istemcileri VNet1 ve site1 erişebilir, ancak site1 yollarının el ile eklenmesi gerekir.

* Windows dışı istemciler, VNet1 ve site1 'e erişebilir.


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>S2S ve şube ofisi kullanılarak bağlanan birden çok VNET

Bu örnekte, Noktadan siteye VPN Ağ Geçidi bağlantısı VNet1 içindir. VNet1, siteden siteye VPN bağlantısı kullanılarak VNet2 'e bağlanır. VNet2, siteden siteye VPN bağlantısı kullanılarak VNet3 'e bağlanır. VNet1 ve VNet3 ağları arasında doğrudan eşleme veya siteden siteye VPN tüneli yoktur. VNet3, siteden siteye VPN bağlantısı kullanarak bir şube ofisine (site1) bağlanır. Tüm VPN bağlantıları BGP çalıştırmıyor.

Tüm istemciler yalnızca VNet1 erişim sağlayabilir.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg" alt-text="Çoklu VNet S2S ve şube ofisi gösteren diyagram" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg":::

### <a name="address-space"></a>Adres alanı

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Eklenen yollar

* Windows istemcilerine eklenen yollar: 10.1.0.0/16, 192.168.0.0/24

* Windows olmayan istemcilere eklenen yollar: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows istemcileri yalnızca VNet1 erişebilir

* Windows olmayan istemciler yalnızca VNet1 erişebilir

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>S2S ve şube ofisi (BGP) kullanılarak bağlanan birden çok VNET

Bu örnekte, Noktadan siteye VPN Ağ Geçidi bağlantısı VNet1 içindir. VNet1, siteden siteye VPN bağlantısı kullanılarak VNet2 'e bağlanır. VNet2, siteden siteye VPN bağlantısı kullanılarak VNet3 'e bağlanır. VNet1 ve VNet3 ağları arasında doğrudan eşleme veya siteden siteye VPN tüneli yoktur. VNet3, siteden siteye VPN bağlantısı kullanarak bir şube ofisine (site1) bağlanır. Tüm VPN bağlantıları BGP çalıştırıyor.

Windows kullanan istemciler, siteden siteye VPN bağlantısı kullanılarak bağlanan sanal ağlara ve sitelere erişebilir, ancak VNet2, VNet3 ve site1 yollarının istemciye el ile eklenmesi gerekir. Windows dışı istemcileri, bir siteden siteye VPN bağlantısı kullanılarak bağlı olan sanal ağlara ve sitelere el ile müdahale olmadan erişebilirler. Erişim geçişlidir ve istemciler tüm bağlı sanal ağlarda ve sitelerde (Şirket içi) kaynaklara erişebilir.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg" alt-text="Çoklu VNet S2S ve şube ofisi" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg":::

### <a name="address-space"></a>Adres alanı

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Eklenen yollar

* Windows istemcilerine eklenen yollar: 10.1.0.0/16, 192.168.0.0/24

* Windows olmayan istemcilere eklenen yollar: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows istemcileri VNet1, VNet2, VNet3 ve site1 erişebilir, ancak VNet2, VNet3 ve site1 yollarının istemciye el ile eklenmesi gerekir.

* Windows olmayan istemciler VNet1, Vnet2, VNet3 ve site1 'e erişebilir.

## <a name="next-steps"></a>Sonraki adımlar

P2S VPN 'nizi oluşturmaya başlamak için [Azure Portal kullanarak P2S VPN oluşturma](vpn-gateway-howto-point-to-site-resource-manager-portal.md) konusuna bakın.
