---
title: "Şirket içi ağınızı bir Azure sanal ağına bağlama: Siteden Siteye VPN: Portal | Microsoft Docs"
description: "Şirket içi ağınız ile bir Azure sanal ağı arasında genel İnternet üzerinden bir IPSec bağlantısı oluşturma adımları. Bu adımlar portalı kullanarak Siteden Siteye şirket içi ve dışı karışık VPN Gateway bağlantısı oluşturmanıza yardımcı olur."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 80939bb48c29ba39e2d347cb80d6169d79329cfc
ms.lasthandoff: 03/25/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Azure portalında Siteden Siteye bağlantı oluşturma
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Klasik - Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Klasik - Klasik Portal](vpn-gateway-site-to-site-create.md)
>
>


Siteden Siteye (S2S) VPN ağ geçidi bağlantısı, IPSec/IKE (IKEv1 veya IKEv2) VPN tüneli üzerinden kurulan bir bağlantıdır. Bu bağlantı türü için, şirket içinde ortak IP adresi atanmış olan ve NAT'nin arkasında bulunmayan bir VPN cihazı gerekir. Siteden Siteye bağlantılar, şirket içi ve dışı karışık yapılandırmalar ve karma yapılandırmalar için kullanılabilir.

Bu makale, Azure Resource Manager dağıtım modelini ve Azure portal’ı kullanarak sanal ağ ve şirket içi ağınıza yönelik Konumdan Konuma VPN ağ geçidi bağlantısı oluşturma işleminde size yol gösterir. 

![Siteden Siteye şirket içi ve dışı karışık VPN Gateway bağlantısı diyagramı](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Konumdan Konuma bağlantılar için dağıtım modelleri ve yöntemleri
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Aşağıdaki tabloda, şu anda Siteden Siteye yapılandırmalar için kullanılabilen dağıtım modelleri ve yöntemleri gösterilmektedir. Yapılandırma adımlarını içeren bir makale olduğunda, bu tablodan makaleye yönelik doğrudan bağlantı oluştururuz.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Ek yapılandırmalar
Sanal ağları birbirine bağlamak istiyor ancak şirket içi bir konuma bağlantı oluşturmuyorsanız, bkz. [VNet’ten VNet’e bağlantıyı yapılandırma](vpn-gateway-vnet-vnet-rm-ps.md). Zaten bağlantısı bulunan bir sanal ağa Siteden Siteye bağlantı eklemek istiyorsanız bkz. [VPN ağ geçidi bağlantısı bulunan bir sanal ağa S2S bağlantısı ekleme](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Başlamadan önce
Yapılandırmanıza başlamadan önce aşağıdaki öğelerin bulunduğunu doğrulayın:

* Uyumlu bir VPN cihazı ve bu cihazı yapılandırabilecek biri. Bkz. [VPN Cihazları Hakkında](vpn-gateway-about-vpn-devices.md).
* Şirket içi ağınızda bulunan IP adresi alanlarıyla ilgili fazla bilginiz yoksa size bu ayrıntıları sağlayabilecek biriyle çalışmanız gerekir. Siteden Siteye bağlantıların çakışan adres alanları olamaz.
* VPN cihazınız için dışarıya yönelik genel bir IP adresi. Bu IP adresi bir NAT’nin arkasında olamaz.
* Azure aboneliği. Henüz Azure aboneliğiniz yoksa [MSDN abonelik avantajlarınızı](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) etkinleştirebilir veya [ücretsiz bir hesap](http://azure.microsoft.com/pricing/free-trial) için kaydolabilirsiniz.

### <a name="values"></a>Örnek değerler
Bu adımları bir alıştırma olarak kullanırken, şu örnek değerleri kullanabilirsiniz:

* **VNet Name:** TestVNet1
* **Adres Alanı:** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (bu alıştırma için isteğe bağlı)
* **Alt ağlar:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (bu alıştırma için isteğe bağlı)
  * GatewaySubnet: 10.11.255.0/27
* **Kaynak Grubu:** TestRG1
* **Konum:** Doğu ABD
* **DNS Sunucusu:** DNS sunucunuzun IP adresi
* **Sanal Ağ Geçidi Adı: VNet1GW**
* **Genel IP:** VNet1GWIP
* **VPN Türü:** Yol tabanlı
* **Bağlantı Türü:** Siteden siteye (IPsec)
* **Ağ Geçidi Türü:** VPN
* **Yerel Ağ Geçidi Adı:** Site2
* **Bağlantı Adı:** VNet1toSite2

## <a name="CreatVNet"></a>1. Sanal ağ oluşturma

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-s2s-rm-portal-include.md)]

## <a name="dns"></a>2. DNS sunucusu belirleme
Siteden Siteye bağlantılar için DNS gerekli değildir. Ancak, sanal ağınıza dağıtılmış olan kaynaklarınız için ad çözümleme istiyorsanız bir DNS sunucusu belirtmeniz gerekir. Bu ayar, bu sanal ağ için ad çözümlemede kullanmak istediğiniz DNS sunucusunu belirtmenizi sağlar. Bir DNS sunucusu oluşturmaz.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>3. Ağ geçidi alt ağı oluşturma
VPN ağ geçidiniz için bir ağ geçidi alt ağı oluşturmanız gerekir. Ağ geçidi alt ağı, VPN ağ geçidi hizmetlerinin kullanacağı IP adreslerini içerir. Mümkünse, /28 veya /27 CIDR bloğu kullanarak bir ağ geçidi alt ağı oluşturun. Bunun yapılması, gelecekteki ağ geçidi yapılandırma gereksinimlerini karşılamaya yetecek sayıda IP adresine sahip olmanızı sağlar.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]

## <a name="VNetGateway"></a>4. Sanal ağ geçidi oluşturma

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5. Yerel ağ geçidi oluşturma
Yerel ağ geçidi, şirket içi konumunuz anlamına gelir. Yerel ağ geçidi için belirttiğiniz ayarlar, şirket içi VPN cihazınıza yönlendirilen adres alanlarını belirler.

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6. VPN cihazınızı yapılandırma
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>7. Siteden Siteye bağlantı kurma

Bu adımda, sanal ağ geçidiniz ile şirket içi VPN cihazınız arasında Siteden Siteye VPN bağlantısı oluşturacaksınız. Bu bölüme başlamadan önce sanal ağa ait ağ geçidi ve yerel ağa ait ağ geçidinizin oluşturulmasının tamamlandığından emin olun.

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8. VPN bağlantısını doğrulama

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
*  Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Daha fazla bilgi için bkz. [Sanal Makineler](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
*  BGP hakkında bilgi edinmek için [BGP’ye Genel Bakış](vpn-gateway-bgp-overview.md) ve [BGP’yi yapılandırma](vpn-gateway-bgp-resource-manager-ps.md) makalelerine bakın.


