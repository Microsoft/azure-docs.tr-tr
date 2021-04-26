---
title: "Öğretici: Azure ExpressRoute-VNet 'e ağ geçidi ekleme (Azure portal)"
description: Bu öğreticide, Azure portal kullanarak ExpressRoute için sanal ağ geçidi ekleme işlemi adım adım açıklanmaktadır.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: f81446111ab87b280d50caceb93beefc7aedef3b
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110334"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak ExpressRoute için sanal ağ geçidi yapılandırma
> [!div class="op_single_selector"]
> * [Kaynak Yöneticisi Azure portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasik-PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video-Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Bu öğretici, önceden var olan VNet için bir sanal ağ geçidi ekleme adımlarında size yol gösterir. Bu makalede, önceden var olan VNet için bir sanal ağ (VNet) ağ geçidi ekleme, yeniden boyutlandırma ve kaldırma adımlarında adım adım gösterilmektedir. Bu yapılandırmaya yönelik adımlar, bir ExpressRoute yapılandırmasında kullanılacak Kaynak Yöneticisi dağıtım modeli kullanılarak oluşturulan sanal ağlar içindir. ExpressRoute için sanal ağ geçitleri ve ağ geçidi yapılandırma ayarları hakkında daha fazla bilgi için bkz. [ExpressRoute için sanal ağ geçitleri hakkında](expressroute-about-virtual-network-gateways.md). 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> - Ağ geçidi alt ağı oluşturun.
> - Sanal Ağ Geçidi oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu görevin adımları aşağıdaki yapılandırma başvurusu listesindeki değerleri temel alarak bir sanal ağ kullanır. Bu listeyi örnek adımlarımızda kullanırız. Bir başvuru olarak kullanmak için listeyi kopyalayabilir ve değerleri kendi değerlerinizle değiştirin.

**Yapılandırma başvuru listesi**

* Sanal ağ adı = "TestVNet"
* Sanal ağ adres alanı = 192.168.0.0/16
* Alt ağ adı = "ön uç" 
    * Alt ağ adres alanı = "192.168.1.0/24"
* Kaynak grubu = "TestRG"
* Konum = "Doğu ABD"
* Ağ geçidi alt ağ adı: "GatewaySubnet" her zaman bir ağ geçidi alt ağı *gatewaysubnet* adını vermelisiniz.
    * Ağ geçidi alt ağ adres alanı = "192.168.200.0/26"
* Ağ geçidi adı = "ERGW"
* Ağ Geçidi genel IP adı = "Myergwvıp"
* Ağ Geçidi türü = "ExpressRoute" Bu tür bir ExpressRoute yapılandırması için gereklidir.

Yapılandırmanıza başlamadan önce bu adımların bir [videosunu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) görüntüleyebilirsiniz.

> [!IMPORTANT]
> Özel eşleme için IPv6 desteği şu anda **genel önizlemededir**. Sanal ağınızı IPv6 tabanlı özel eşleme ile bir ExpressRoute devresine bağlamak istiyorsanız, lütfen sanal ağınızın ikili bir yığın olduğundan emin olun ve [Azure VNET Için IPv6](../virtual-network/ipv6-overview.md)yönergelerini izleyin.
> 
> 

## <a name="create-the-gateway-subnet"></a>Ağ geçidi alt ağını oluşturma

1. [Portalda](https://portal.azure.com), sanal ağ geçidini oluşturmak istediğiniz kaynak yöneticisi sanal ağa gidin.
1. Sanal ağınızın **Ayarlar** bölümünde alt ağ ayarlarını genişletmek Için **alt ağlar** ' ı seçin.
1. Ağ geçidi alt ağı eklemek için **alt ağlar** ayarlarında **+ Gateway alt ağı** ' nı seçin. 
   
    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-gateway-subnet.png" alt-text="Ağ geçidi alt ağı ekleme":::

1. Alt ağınız için **Ad** alanı otomatik olarak ‘GatewaySubnet’ değeriyle doldurulur. Alt ağın Azure tarafından ağ geçidi alt ağı olarak tanınması için bu değer gereklidir. Yapılandırma gereksinimlerinize uyacak şekilde, oto doldurulmuş **adres aralığı** değerlerini ayarlayın. /27 veya daha büyük (/26,/25 vb.) bir ağ geçidi alt ağı oluşturmanızı öneririz. Ağ geçidinize 16 ExpressRoute devreleri bağlamayı planlıyorsanız,/26 veya daha büyük bir ağ geçidi alt ağı oluşturmanız **gerekir** .

    Çift yığın sanal ağı kullanıyorsanız ve ExpressRoute üzerinden IPv6 tabanlı özel eşleme kullanmayı planlıyorsanız, **IP6 adres alanı Ekle** ve **IPv6 adres aralığı** değerlerini gir ' e tıklayın.

    Ardından, değerleri kaydetmek ve ağ geçidi alt ağını oluşturmak için **Tamam** ' ı seçin.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-subnet-gateway.png" alt-text="Alt ağ ekleme":::

## <a name="create-the-virtual-network-gateway"></a>Sanal ağ geçidini oluşturma

1. Portalda, sol taraftaki **kaynak oluştur**' u seçin ve arama alanına ' sanal ağ geçidi ' yazın. Arama dönüşindeki **sanal ağ geçidini** bulun ve girişi seçin. **Sanal ağ geçidi** sayfasında **Oluştur**' u seçin.
1. **Sanal ağ geçidi oluştur** sayfasında, girin veya şu ayarları seçin:

    | Ayar | Değer |
    | --------| ----- |
    | Abonelik | Doğru aboneliğin seçildiğini doğrulayın. |
    | Kaynak Grubu | Sanal ağı seçtikten sonra kaynak grubu otomatik olarak seçilir. | 
    | Name | Ağ geçidinizi adlandırın. Bu, bir ağ geçidi alt ağını adlandırmayla aynı değildir. Bu, oluşturmakta olduğunuz ağ geçidi nesnesinin adıdır.|
    | Region | **Bölge** alanını, sanal ağınızın bulunduğu konuma işaret etmek üzere değiştirin. Konum, sanal ağınızın bulunduğu bölgeyi işaret ettikten sonra, sanal ağ ' sanal ağ seçin ' açılır listesinde görünmez. |
    | Ağ geçidi türü | **ExpressRoute** seçin|
    | SKU | Açılan listeden ağ geçidi SKU 'sunu seçin. |
    | Sanal ağ | *Testvnet*' i seçin. |
    | Genel IP adresi | **Yeni oluştur**’u seçin.|
    | Genel IP adresi adı | Genel IP adresi için bir ad girin. |

    > [!IMPORTANT]
    > ExpressRoute üzerinden IPv6 tabanlı özel eşleme kullanmayı planlıyorsanız, **SKU** IÇIN az SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) seçtiğinizden emin olun.
    > 
    > 

1. Ağ geçidini oluşturmaya başlamak için **gözden geçir + oluştur**' u ve ardından **Oluştur** ' u seçin. Ayarlar doğrulanır ve ağ geçidi dağıtılır. Sanal ağ geçidi oluşturma işleminin tamamlanması 45 dakika sürebilir.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/gateway.png" alt-text="Sanal ağ geçidi sayfası alanı oluştur":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

ExpressRoute ağ geçidine artık ihtiyacınız yoksa, sanal ağ kaynak grubundaki ağ geçidini bulun ve **Sil**' i seçin. Ağ geçidinin bir devreye bağlantı içermediğinden emin olun.

:::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/delete-gateway.png" alt-text="Sanal ağ geçidini Sil":::

## <a name="next-steps"></a>Sonraki adımlar
VNet ağ geçidini oluşturduktan sonra sanal ortamınızı bir ExpressRoute devresine bağlayabilirsiniz. 

> [!div class="nextstepaction"]
> [Sanal ağı bir ExpressRoute devresine bağlama](expressroute-howto-linkvnet-portal-resource-manager.md)
