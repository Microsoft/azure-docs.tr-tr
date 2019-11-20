---
title: 'Azure ExpressRoute: sanal ağa ağ geçidi ekleme: Portal'
description: Bu makalede, Azure portal kullanarak ExpressRoute için zaten oluşturulmuş bir Kaynak Yöneticisi VNet 'e bir sanal ağ geçidi ekleme işlemi adım adım açıklanmaktadır.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 87b656f0ef999b3b15a89476f5cba4c4fcfc2b1e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037390"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Azure portalını kullanarak ExpressRoute için sanal ağ geçidi yapılandırma
> [!div class="op_single_selector"]
> * [Resource Manager - Azure portalı](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasik - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure portalı](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Bu makalede önceden var olan bir VNet için sanal ağ geçidi eklemek için adımlarında size kılavuzluk eder. Bu makalede ekleme, yeniden boyutlandırma ve önceden var olan bir VNet için bir sanal ağ (VNet) ağ geçidini kaldırmak için adımlarında size kılavuzluk eder. Bu yapılandırma için özel bir ExpressRoute yapılandırmasında kullanılan Resource Manager dağıtım modeli kullanılarak oluşturulan sanal ağlar için aynıdır. Sanal ağ geçidi ve ExpressRoute için ağ geçidi yapılandırma ayarları hakkında daha fazla bilgi için bkz: [ExpressRoute için sanal ağ geçitleri hakkında](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Başlamadan önce

Bu görev için adımları aşağıdaki yapılandırma başvuru listesinde değerlere göre sanal ağ kullanın. Bizim örnek adımlarda bu listeyi kullanırız. Bir başvuru olarak kullanmak için listedeki değerleri kendi değerlerinizle değiştirerek kopyalayabilirsiniz.

**Yapılandırma başvuru listesi**

* Sanal ağ adı "TestVNet" =
* Sanal ağ adres alanı 192.168.0.0/16 =
* Alt ağ adı "FrontEnd" = 
    * Alt ağ adres alanı = "192.168.1.0/24"
* Kaynak grubu "TestRG" =
* Konum "Doğu ABD" =
* Ağ geçidi alt ağ adı: gereken her zaman adını bir ağ geçidi alt ağı "GatewaySubnet" *GatewaySubnet*.
    * Ağ geçidi alt ağ adres alanı = "192.168.200.0/26"
* Ağ geçidi adı "ERGW" =
* Ağ geçidi genel IP adı "MyERGWVIP" =
* Ağ geçidi türü = "ExpressRoute" Bu tür bir ExpressRoute yapılandırma için gereklidir.

Görüntüleyebileceğiniz bir [Video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) yapılandırmanıza başlamadan önce bu adımları.

## <a name="create-the-gateway-subnet"></a>Ağ geçidi alt ağını oluşturma

1. [Portal](https://portal.azure.com)’da, sanal ağ geçidini oluşturmak istediğiniz Resource Manager sanal ağına gidin.
2. VNet dikey pencerenizin **Ayarlar** bölümünde, Alt Ağlar dikey penceresini genişletmek için **Alt Ağlar**'a tıklayın.
3. **Alt ağlar** dikey penceresinde, **+Alt ağ**’a tıklayarak **Alt ağ ekle** dikey penceresini açın. 
   
    ![Ağ geçidi alt ağını ekleme](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Ağ geçidi alt ağı ekleme")


4. Alt ağınız için **Ad** alanı otomatik olarak ‘GatewaySubnet’ değeriyle doldurulur. Alt ağın Azure tarafından ağ geçidi alt ağı olarak tanınması için bu değer gereklidir. Otomatik olarak doldurulmuş **Adres aralığı** değerlerini, yapılandırma gereksinimlerinize uyacak şekilde ayarlayın. / 27 veya daha büyük bir ağ geçidi alt ağı oluşturmanızı öneririz (/ 26, / 25 vb..). ' A tıklayarak **Tamam** değerleri kaydedin ve ağ geçidi alt ağı oluşturun.

    ![Alt ağ ekleniyor](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Alt ağ ekleme")

## <a name="create-the-virtual-network-gateway"></a>Sanal ağ geçidini oluşturma

1. Portalda, sol taraftaki **+** simgesine tıklayın ve arama alanına ‘Sanal Ağ Geçidi’ yazın. Arama sonuçlarında **Sanal ağ geçidi** seçeneğini bulun ve girişe tıklayın. **Sanal ağ geçidi** dikey penceresinin alt kısmındaki **Oluştur**’a tıklayın. Bu işlem **Sanal ağ geçidi oluştur** dikey penceresini açar.
2. **Sanal ağ geçidi oluştur** dikey penceresinde, sanal ağ geçidinize ait değerleri girin.

    ![Sanal ağ geçidi dikey alanı oluştur](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Sanal ağ geçidi oluştur dikey penceresinin alanları")
3. **Ad**: Ağ geçidinizi adlandırın. Bir ağ geçidi alt ağını adlandırmayla aynı değildir. Bu ad, oluşturduğunuz ağ geçidi nesnesinin adıdır.
4. **Ağ geçidi türü**: seçin **ExpressRoute**.
5. **SKU**: Açılır listeden ağ geçidi SKU’sunu seçin.
6. **Konum**: **Konum** alanını, sanal ağınızın bulunduğu konumu işaret edecek şekilde ayarlayın. Konum, sanal ağınızın bulunduğu bölgeye işaret etmiyorsa, 'Sanal ağ seçin' açılır menüsünde sanal ağ görüntülenmez.
7. Bu ağ geçidini eklemek istediğiniz sanal ağı seçin. **Sanal ağ**'a tıklayarak **Sanal ağ seçin** dikey penceresini açın. VNet'i seçin. Sanal ağınızı görmüyorsanız **Konum** alanının, sanal ağınızın bulunduğu bölgeyi işaret ettiğinden emin olun.
9. Genel bir IP adresi seçin. **Genel IP adresi**'ne tıklayarak **Genel IP adresi seçin** dikey penceresini açın. **+Yeni Oluştur**'a tıklayarak **Genel IP adresi oluştur** dikey penceresini açın. Genel IP adresiniz için bir ad girin. Bu dikey pencere bir genel IP adresi nesnesi oluşturur ve daha sonra bu nesneye dinamik olarak bir genel IP adresi atanır. Bu dikey penceredeki değişiklikleri kaydetmek için **Tamam**’a tıklayın.
10. **Abonelik**: Doğru aboneliğin seçildiğini doğrulayın.
11. **Kaynak grubu**: Bu ayar, seçtiğiniz Sanal Ağ tarafından belirlenir.
12. Önceki ayarları belirttikten sonra **Konum**'u ayarlamayın.
13. Ayarları doğrulayın. Ağ geçidinizin panoda görünmesini istiyorsanız dikey pencerenin altında yer alan **Panoya sabitle** seçeneğini belirleyebilirsiniz.
14. Ağ geçidi oluşturmaya başlamak için **Oluştur**’a tıklayın. Ayarlar doğrulanır ve ağ geçidi dağıtılır. Sanal ağ geçidi oluşturma, tamamlanması 45 dakika sürebilir.

## <a name="next-steps"></a>Sonraki adımlar
VNet ağ geçidinin oluşturduktan sonra ağınız bir ExpressRoute bağlantı hattına bağlayabilirsiniz. Bkz: [bir sanal ağı ExpressRoute devresine bağlama](expressroute-howto-linkvnet-portal-resource-manager.md).
