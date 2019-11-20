---
title: "VNet 'e birden çok VPN Gateway siteden siteye bağlantı ekleme: Azure portalı: Kaynak Yöneticisi | Microsoft Docs"
description: Mevcut bağlantısı olan bir VPN ağ geçidine çok siteli S2S bağlantıları ekleme
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: d2c32fd35bbc6de1f010013c40a06af69052d3f5
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244613"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Mevcut bir VPN Ağ Geçidi bağlantısı ile bir VNet 'e siteden siteye bağlantı ekleme

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasik)](vpn-gateway-multi-site.md)
>
> 

Bu makale, Azure portal kullanarak mevcut bağlantısı olan bir VPN ağ geçidine siteden siteye (S2S) bağlantıları eklemenize yardımcı olur. Bu bağlantı türü genellikle "çok siteli" bir yapılandırma olarak adlandırılır. Zaten bir S2S bağlantısı, Noktadan siteye bağlantısı veya VNet-VNet bağlantısı olan bir VNet 'e S2S bağlantısı ekleyebilirsiniz. Bağlantı eklerken bazı sınırlamalar vardır. Yapılandırmanızı başlatmadan önce doğrulamak için bu makaledeki [başlamadan önce](#before) bölümüne bakın. 

Bu makale, RouteBased VPN ağ geçidi olan Kaynak Yöneticisi sanal ağlar için geçerlidir. Bu adımlar, yeni ExpressRoute/siteden siteye birlikte var olan bağlantı yapılandırmalarına yönelik olarak geçerli değildir. Ancak, zaten var olan bir birlikte bulunan yapılandırmaya yalnızca yeni bir VPN bağlantısı ekliyorsanız, bu adımları kullanabilirsiniz. Birlikte var olan bağlantılar hakkında bilgi için bkz. [ExpressRoute/S2S birlikte var bağlantıları](../expressroute/expressroute-howto-coexist-resource-manager.md) .

### <a name="deployment-models-and-methods"></a>Dağıtım modelleri ve yöntemleri
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Bu tablo, bu yapılandırma için yeni makaleler ve ek araçlar kullanılabilir hale geldiğinde güncelleştiririz. Bir makale kullanılabildiğinde, bu tablodan doğrudan buna bağlantı veriyoruz.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Başlamadan önce
Aşağıdaki öğeleri doğrulayın:

* Yeni bir birlikte var olan ExpressRoute ve VPN Gateway yapılandırması yapılandırmadığınızda.
* Mevcut bir bağlantıyla Kaynak Yöneticisi dağıtım modeli kullanılarak oluşturulmuş bir sanal ağınız var.
* VNet 'iniz için sanal ağ geçidi, RouteBased ' dir. PolicyBased VPN ağ geçidiniz varsa, sanal ağ geçidini silmeniz ve RouteBased olarak yeni bir VPN ağ geçidi oluşturmanız gerekir.
* Bu sanal ağın bağlandığı sanal ağlardan herhangi biri için adres aralıklarının hiçbiri çakışmıyor.
* Uyumlu VPN cihazınız ve onu yapılandırabilecek birisi var. Bkz. [VPN cihazları hakkında](vpn-gateway-about-vpn-devices.md). VPN cihazınızı yapılandırma konusunda bilgi sahibi değilseniz veya şirket içi ağ yapılandırmanızda bulunan IP adresi aralıklarını bilmiyorsanız, sizin için bu ayrıntıları sağlayabilecek biriyle koordine etmeniz gerekir.
* VPN cihazınız için dışarıdan kullanıma açık bir genel IP adresiniz vardır. Bu IP adresi bir NAT 'ın arkasında bulunamıyor.

## <a name="part1"></a>1. kısım-bağlantı yapılandırma
1. Bir tarayıcıdan [Azure Portal](https://portal.azure.com) gidin ve gerekirse Azure hesabınızla oturum açın.
2. **Tüm kaynaklar** ' a tıklayın ve kaynak listesinden **sanal ağ geçidinizi** bulun ve tıklatın.
3. **Sanal ağ geçidi** sayfasında **Bağlantılar**' a tıklayın.
   
    ![Bağlantılar sayfası](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "bağlantı sayfası")<br>
4. **Bağlantılar** sayfasında **+ Ekle**' ye tıklayın.
   
    ![Bağlantı Ekle düğmesine](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Bağlantı Ekle düğmesi")<br>
5. **Bağlantı ekle** sayfasında, aşağıdaki alanları doldurun:
   
   * **Ad:** Bağlantısını oluşturmakta olduğunuz siteye vermek istediğiniz ad.
   * **Bağlantı türü:** **Siteden siteye (IPSec)** seçeneğini belirleyin.
     
     ![Bağlantı sayfası](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "bağlantı") Ekle sayfası<br>

## <a name="part2"></a>Bölüm 2-yerel ağ geçidi ekleme
1. **Yerel ağ geçidi** ' ne tıklayın ***yerel ağ geçidi seçin***. Bu, **yerel ağ geçidi Seç** sayfasını açar.
   
    ![Yerel ağ geçidi]seçin(./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "yerel ağ geçidi seçin")<br>
2. **Yeni oluştur** ' **a tıklayarak Yerel ağ geçidi oluştur** sayfasını açın.
   
    ![Yerel ağ geçidi Oluştur sayfası](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "yerel ağ geçidi oluştur")<br>
3. **Yerel ağ geçidi oluştur** sayfasında, aşağıdaki alanları doldurun:
   
   * **Ad:** Yerel ağ geçidi kaynağına vermek istediğiniz ad.
   * **IP adresi:** Bağlanmak istediğiniz sitedeki VPN cihazının genel IP adresi.
   * **Adres alanı:** Yeni yerel ağ sitesine yönlendirilmesini istediğiniz adres alanı.
4. Değişiklikleri kaydetmek için **yerel ağ geçidi oluştur** sayfasında **Tamam** ' a tıklayın.

## <a name="part3"></a>3. kısım-paylaşılan anahtarı ekleme ve bağlantıyı oluşturma
1. **Bağlantı ekle** sayfasında, bağlantınızı oluşturmak için kullanmak istediğiniz paylaşılan anahtarı ekleyin. VPN cihazınızdan paylaşılan anahtarı alabilir veya buradan bir tane oluşturun ve ardından VPN cihazınızı aynı paylaşılan anahtarı kullanacak şekilde yapılandırabilirsiniz. Önemli olan şey, anahtarların tamamen aynı olmasıdır.
   
    ![Paylaşılan anahtar](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "paylaşılan anahtarı")<br>
2. Sayfanın alt kısmındaki bağlantıyı oluşturmak için **Tamam** ' ı tıklatın.

## <a name="part4"></a>4. Bölüm-VPN bağlantısını doğrulama


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Daha fazla bilgi için bkz. [sanal makineler öğrenme yolu](/learn/paths/deploy-a-website-with-azure-virtual-machines/) .
