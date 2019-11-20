---
title: 'Azure sanal WAN: ExpressRoute bağlantıları oluşturma'
description: Bu öğreticide Azure sanal WAN kullanarak Azure ve şirket içi ortamlara ExpressRoute bağlantıları oluşturma hakkında bilgi edinin.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 5a394f93c5cc606c26ffa1cc64bf1d61617b05b8
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014937"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Öğretici: Azure sanal WAN kullanarak bir ExpressRoute ilişkilendirmesi oluşturma

Bu öğreticide, Azure 'daki kaynaklarınıza bir ExpressRoute bağlantı hattı üzerinden bağlanmak için sanal WAN 'ın nasıl kullanılacağı gösterilmektedir. Sanal WAN ve sanal WAN kaynakları hakkında daha fazla bilgi için bkz. [sanal WAN 'A genel bakış](virtual-wan-about.md).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sanal WAN oluşturma
> * Hub ve ağ geçidi oluşturma
> * Bir sanal ağı bir hub'a bağlama
> * Bir bağlantı merkezini hub ağ geçidine bağlama
> * Bağlantıyı test etme
> * Ağ Geçidi boyutunu değiştirme
> * Varsayılan bir yol tanıtma

## <a name="before-you-begin"></a>Başlamadan önce

Yapılandırmanıza başlamadan önce aşağıdaki ölçütleri karşıladığınızı doğrulayın:

* Bağlanmak istediğiniz bir sanal ağınız var. Şirket içi ağlarınızın alt ağlarının hiçbirinin, bağlanmak istediğiniz sanal ağlarla çakışmadığından emin olun. Azure portal bir sanal ağ oluşturmak için [hızlı](../virtual-network/quick-create-portal.md)başlangıca bakın.

* Sanal ağınızda sanal ağ geçidi yok. Sanal ağınızda bir ağ geçidi (VPN veya ExpressRoute) varsa, tüm ağ geçitlerini kaldırmanız gerekir. Bu yapılandırma, sanal ağın bunun yerine sanal WAN hub ağ geçidine bağlanmasını gerektirir.

* Hub bölgenizden bir IP adresi aralığı edinin. Hub, sanal WAN tarafından oluşturulan ve kullanılan bir sanal ağ. Hub için belirttiğiniz adres aralığı, bağlandığınız mevcut sanal ağlarınızla çakışamaz. Ayrıca bağlandığınız şirket içi adres aralıklarıyla da çakışamaz. Şirket içi ağ yapılandırmanızda bulunan IP adresi aralıklarını tanımıyorsanız, sizin için bu ayrıntıları sağlayabilecek biriyle koordine edebilirsiniz.

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="openvwan"></a>Sanal WAN oluşturma

Bir tarayıcıdan [Azure portalına](https://portal.azure.com) gidin ve Azure hesabınızla oturum açın.

1. Sanal WAN sayfasına gidin. Portalda **+Kaynak oluştur**’a tıklayın. Arama kutusuna **sanal WAN** yazın ve ENTER ' u seçin.
2. Sonuçlardan **sanal WAN** ' ı seçin. Sanal WAN sayfasında, **Oluştur** ' a tıklayarak WAN sayfası oluştur sayfasını açın.
3. **WAN oluştur** sayfasında, **temel bilgiler** sekmesinde aşağıdaki alanları girin:

   ![WAN oluşturma](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Abonelik**: Kullanmak istediğiniz aboneliği seçin.
   * **Kaynak Grubu**: Yeni oluşturun veya var olanı kullanın.
   * **Kaynak grubu konumu** -açılan listeden bir kaynak konumu seçin. WAN, global bir kaynaktır ve belirli bir bölgeyle sınırlı değildir. Ancak oluşturduğunuz WAN kaynağını daha kolay yönetmek ve bulmak için bir bölge seçmeniz gerekir.
   * **Ad** -WAN 'nizi çağırmak istediğiniz adı yazın.
   * **Tür** seçin **Standart**. Temel SKU 'YU kullanarak bir ExpressRoute ağ geçidi oluşturamazsınız.
4. Alanları doldurmayı tamamladıktan sonra, **gözden geçir + oluştur**' u seçin.
5. Doğrulama başarılı olduktan sonra, sanal WAN oluşturmak için **Oluştur** ' u seçin.

## <a name="hub"></a>Sanal hub ve ağ geçidi oluşturma

Sanal hub, sanal WAN tarafından oluşturulan ve kullanılan sanal bir ağ. VPN ve ExpressRoute gibi çeşitli ağ geçitleri içerebilir. Bu bölümde, sanal hub 'ınız için bir ExpressRoute ağ geçidi oluşturacaksınız. [Yeni bir sanal hub oluştururken](#newhub)ağ geçidini oluşturabilir ya da onu düzenleyerek [mevcut bir hub](#existinghub) 'da ağ geçidini oluşturabilirsiniz. 

ExpressRoute ağ geçitleri 2 Gbps biriminde sağlanır. 1 ölçek birimi = 2 Gbps, en fazla 10 ölçek birimi destekler = 20 Gbps. Bir sanal hub ve ağ geçidinin tam olarak oluşturulması yaklaşık 30 dakika sürer.

### <a name="newhub"></a>Yeni bir sanal hub ve bir ağ geçidi oluşturmak için

Yeni bir sanal hub oluşturun. Bir hub oluşturulduktan sonra herhangi bir site iliştiremeseniz bile Hub için ücret ödersiniz.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="existinghub"></a>Mevcut bir hub 'da ağ geçidi oluşturmak için

Ayrıca, var olan bir hub 'da düzenleyerek bir ağ geçidi oluşturabilirsiniz.

1. Düzenlemek istediğiniz sanal hub 'a gidin ve seçin.
2. **Sanal hub 'ı Düzenle** sayfasında **ExpressRoute ağ geçidini dahil et**onay kutusunu işaretleyin.
3. Değişikliklerinizi onaylamak için **Onayla** ' yı seçin. Hub ve hub kaynaklarının tam olarak oluşturulması yaklaşık 30 dakika sürer.

   ![Mevcut hub](./media/virtual-wan-expressroute-portal/edithub.png "Hub 'ı düzenleme")

### <a name="to-view-a-gateway"></a>Bir ağ geçidini görüntülemek için

Bir ExpressRoute ağ geçidi oluşturduktan sonra, ağ geçidi ayrıntılarını görüntüleyebilirsiniz. Hub 'a gidin, **ExpressRoute**' ı seçin ve ağ geçidini görüntüleyin.

![Ağ geçidini görüntüle](./media/virtual-wan-expressroute-portal/viewgw.png "ağ geçidini görüntüle")

## <a name="connectvnet"></a>VNet 'iniz hub 'a bağlanır

Bu bölümde, hub 'ınız ile VNet arasında eşleme bağlantısı oluşturursunuz. Bu adımları bağlanmak istediğiniz tüm sanal ağlar için tekrarlayın.

1. Sanal WAN'ınızın sayfasında **Sanal ağ bağlantısı**'na tıklayın.
2. Sanal ağ bağlantısı sayfasında **+Bağlantı ekle**'ye tıklayın.
3. **Bağlantı ekle** sayfasında aşağıdaki alanları doldurun:

    * **Bağlantı adı**: Bağlantınıza bir ad verin.
    * **Hub'lar**: Bu bağlantıyla ilişkilendirmek istediğiniz hub'ı seçin.
    * **Abonelik**: Aboneliği doğrulayın.
    * **Sanal ağ**: Bu hub'a bağlamak istediğiniz sanal ağı seçin. Sanal ağda zaten var olan bir sanal ağ geçidi (VPN ya da ExpressRoute) olamaz.

## <a name="connectcircuit"></a>Devrenizi hub Gateway 'e bağlama

Ağ Geçidi oluşturulduktan sonra, bir [ExpressRoute devresini](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) buna bağlayabilirsiniz. ExpressRoute Global Reach tarafından desteklenen konumlardaki ExpressRoute Premium devrelerinin bir sanal WAN ExpressRoute Gateway 'e bağlanabildiğini unutmayın.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Devresine hub Gateway 'e bağlamak için

Portalda **sanal hub-> bağlantısı-> ExpressRoute** sayfasına gidin. Aboneliğinizde bir ExpressRoute devresine erişiminiz varsa, devre listesinde kullanmak istediğiniz devreyi görürsünüz. Herhangi bir bağlantı görmüyorsanız, ancak bir yetkilendirme anahtarı ve eş devre URI 'SI ile sağlanmışsa, bir bağlantı hattı kullanabilir ve bağlayabilirsiniz. [Yetkilendirme anahtarını benimsemek için](#authkey)bkz. bağlanma.

1. Devresini seçin.
2. **Bağlantı devresini**seçin.

   ![bağlantı devreleri](./media/virtual-wan-expressroute-portal/cktconnect.png "bağlantı devreleri")

### <a name="authkey"></a>Yetkilendirme anahtarını benimsemek yoluyla bağlanmak için

Bağlanmak için verdiğiniz yetkilendirme anahtarını ve devre URI 'sini kullanın.

1. ExpressRoute sayfasında **+ kullan yetkilendirme anahtarı** ' na tıklayın.

   ![dınız](./media/virtual-wan-expressroute-portal/redeem.png "dınız")
2. Kullanma yetkilendirme anahtarı sayfasında, değerleri girin.

   ![anahtar değerlerini kullanma](./media/virtual-wan-expressroute-portal/redeemkey2.png "anahtar değerlerini kullanma")
3. Anahtarı eklemek için **Ekle** ' yi seçin.
4. Devresini görüntüleyin. Kullanılan bir devre, kullanıcının adı (tür, sağlayıcı ve diğer bilgiler olmadan) yalnızca Kullanıcı tarafından farklı bir abonelikte olduğu için bu adı gösterir.

## <a name="to-test-connectivity"></a>Bağlantıyı sınamak için

Bağlantı hattı bağlantısı kurulduktan sonra, hub bağlantı durumu ' Bu hub ' olarak gösterilir. Bu, bağlantının Merkez ExpressRoute ağ geçidine göre kuruladığını gösterir. ExpressRoute bağlantı hattının arkasındaki bir istemciden bağlantıyı test etmeden önce yaklaşık 5 dakika bekleyin. Örneğin, daha önce oluşturduğunuz VNet 'teki bir VM.

ExpressRoute ağ geçidiyle aynı hub 'daki bir sanal WAN VPN Gateway 'e bağlı olan siteleriniz varsa VPN ve ExpressRoute uç noktaları arasında çift yönlü bağlantıya sahip olabilirsiniz. Dinamik yönlendirme (BGP) destekleniyor. Hub 'daki ağ geçitlerinin ASN 'si düzeltildi ve şu an düzenlenemiyor.

## <a name="to-change-the-size-of-a-gateway"></a>Bir ağ geçidinin boyutunu değiştirmek için

ExpressRoute ağ geçidinizin boyutunu değiştirmek istiyorsanız, hub 'ın içindeki ExpressRoute ağ geçidini bulun ve açılan listeden ölçek birimlerini seçin. Değişiklerinizi kaydedin. Hub Gateway 'in güncelleştirilmesi yaklaşık 30 dakika sürer.

![Ağ Geçidi boyutunu değiştir](./media/virtual-wan-expressroute-portal/changescale.png "Ağ Geçidi boyutunu değiştir")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Varsayılan 0.0.0.0/0 yolunu uç noktalara tanıtmak için

Azure sanal hub 'ının varsayılan yolu olan 0.0.0.0/0 yolunu ExpressRoute bitiş noktalarına tanıtmasını istiyorsanız, ' Varsayılan rotayı yay ' seçeneğini etkinleştirmeniz gerekir.

1. **Devre dışı >...-> Bağlantıyı Düzenle**' yi seçin.

   ![Bağlantıyı Düzenle](./media/virtual-wan-expressroute-portal/defaultroute1.png "Bağlantıyı Düzenle")

2. Varsayılan yolu yaymak için **Etkinleştir** ' i seçin.

   ![Varsayılan yolu yay](./media/virtual-wan-expressroute-portal/defaultroute2.png "Varsayılan yolu yay")

## <a name="next-steps"></a>Sonraki adımlar

Sanal WAN hakkında daha fazla bilgi için [Sanal WAN'a Genel Bakış](virtual-wan-about.md) sayfasına bakın.