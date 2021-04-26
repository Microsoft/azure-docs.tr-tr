---
title: "VNet 'e birden çok VPN Gateway siteden siteye bağlantı ekleme: Azure portal"
description: Mevcut bağlantısı olan bir VPN ağ geçidine çok siteli S2S bağlantıları ekleme
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: 92d39b0d39511571701fd092f641cb8ca3ae42c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890177"
---
# <a name="add-additional-s2s-connections-to-a-vnet-azure-portal"></a>VNet 'e ek S2S bağlantısı ekleme: Azure portal

> [!div class="op_single_selector"]
> * [Azure portalı](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasik)](vpn-gateway-multi-site.md)
>

Bu makale, var olan bağlantısı olan bir VPN ağ geçidine siteden siteye (S2S) bağlantılar eklemenize yardımcı olur. Bu mimari genellikle "çok siteli" bir yapılandırma olarak adlandırılır. Zaten bir S2S bağlantısı, Noktadan siteye bağlantısı veya VNet-VNet bağlantısı olan bir VNet 'e S2S bağlantısı ekleyebilirsiniz. Bağlantı eklerken bazı sınırlamalar vardır. Yapılandırmanızı başlatmadan önce doğrulamak için bu makaledeki [önkoşulları](#before) bölümüne bakın.

Bu makale, RouteBased VPN ağ geçidi olan Kaynak Yöneticisi sanal ağlar için geçerlidir. Bu adımlar, yeni ExpressRoute/siteden siteye birlikte var olan bağlantı yapılandırmalarına yönelik olarak geçerli değildir. Ancak, zaten var olan bir birlikte bulunan yapılandırmaya yalnızca yeni bir VPN bağlantısı ekliyorsanız, bu adımları kullanabilirsiniz. Birlikte var olan bağlantılar hakkında bilgi için bkz. [ExpressRoute/S2S birlikte var bağlantıları](../expressroute/expressroute-howto-coexist-resource-manager.md) .

## <a name="prerequisites"></a><a name="before"></a>Önkoşullar

Aşağıdaki öğeleri doğrulayın:

* Yeni bir birlikte var olan ExpressRoute ve VPN Gateway yapılandırması yapılandırmadığınızda.
* Mevcut bir bağlantıyla Kaynak Yöneticisi dağıtım modeli kullanılarak oluşturulmuş bir sanal ağınız var.
* VNet 'iniz için sanal ağ geçidi, RouteBased ' dir. PolicyBased VPN ağ geçidiniz varsa, sanal ağ geçidini silmeniz ve RouteBased olarak yeni bir VPN ağ geçidi oluşturmanız gerekir.
* Bu sanal ağın bağlandığı sanal ağlardan herhangi biri için adres aralıklarının hiçbiri çakışmıyor.
* Uyumlu VPN cihazınız ve onu yapılandırabilecek birisi var. Bkz. [VPN Cihazları Hakkında](vpn-gateway-about-vpn-devices.md). VPN cihazınızı yapılandırma konusuyla veya şirket içi ağ yapılandırmanızdaki IP adresi aralıklarıyla ilgili fazla bilginiz yoksa size bu ayrıntıları sağlayabilecek biriyle çalışmanız gerekir.
* VPN cihazınız için dışarıdan kullanıma açık bir genel IP adresiniz vardır.

## <a name="configure-a-connection"></a><a name="configure"></a>Bağlantı yapılandırma

1. Tarayıcıdan [Azure portalına](https://portal.azure.com) gidin ve gerekiyorsa Azure hesabınızda oturum açın.
1. **Tüm kaynaklar** ' ı seçin ve kaynak listesinden **sanal ağ geçidinizi** bulun ve seçin.
1. **Sanal ağ geçidi** sayfasında **Bağlantılar**' ı seçin.

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connections.png" alt-text="VPN Gateway bağlantıları":::
1. **Bağlantılar** sayfasında **+ Ekle**' yi seçin.
1. Bu, **bağlantı ekle** sayfasını açar.

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/add-connection.png" alt-text="Bağlantı sayfası ekle":::
1. **Bağlantı ekle** sayfasında, aşağıdaki alanları doldurun:

   * **Ad:** Bağlantısını oluşturmakta olduğunuz siteye vermek istediğiniz ad.
   * **Bağlantı türü:** **Siteden siteye (IPSec)** seçeneğini belirleyin.

## <a name="add-a-local-network-gateway"></a><a name="local"></a>Yerel ağ geçidi ekleme

1. **Yerel ağ geçidi** alanı için **_yerel ağ geçidi seçin_ _ ' i seçin *. Bu, _* yerel ağ geçidi Seç sayfasını açar** .
1. **+ Yeni oluştur** ' u seçerek **yerel ağ geçidi oluştur** sayfasını açın.

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/create-local-network-gateway.png" alt-text="Yerel ağ geçidi Oluştur sayfası":::
1. **Yerel ağ geçidi oluştur** sayfasında, aşağıdaki alanları doldurun:

   * **Ad:** Yerel ağ geçidi kaynağına vermek istediğiniz ad.
   * **Uç nokta:** Bağlanmak istediğiniz sitedeki VPN cihazının genel IP adresi veya uç noktanın FQDN 'SI.
   * **Adres alanı:** Yeni yerel ağ sitesine yönlendirilmesini istediğiniz adres alanı.
1. Değişiklikleri kaydetmek için **yerel ağ geçidi oluştur** sayfasında **Tamam ' ı** seçin.

## <a name="add-the-shared-key"></a><a name="part3"></a>Paylaşılan anahtarı ekleme

1. Yerel ağ geçidini oluşturduktan sonra **bağlantı ekle** sayfasına dönün.
1. Kalan alanları doldurun. **Paylaşılan anahtar (PSK)** için, PAYLAŞıLAN anahtarı VPN cihazınızdan alabilir veya buradan bir tane oluşturun ve ardından VPN cihazınızı aynı paylaşılan anahtarı kullanacak şekilde yapılandırabilirsiniz. Önemli olan şey, anahtarların tamamen aynı olmasıdır.

## <a name="create-the-connection"></a><a name="create"></a>Bağlantıyı oluşturma

1. Sayfanın alt kısmındaki bağlantıyı oluşturmak için **Tamam** ' ı seçin. Bağlantı hemen oluşturmaya başlar.
1. Bağlantı tamamlandıktan sonra, onu görüntüleyebilir ve doğrulayabilirsiniz.

## <a name="view-and-verify-the-vpn-connection"></a><a name="verify"></a>VPN bağlantısını görüntüleme ve doğrulama

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Daha fazla bilgi için bkz. [sanal makinelerin öğrenme yolları](/learn/paths/deploy-a-website-with-azure-virtual-machines/).
