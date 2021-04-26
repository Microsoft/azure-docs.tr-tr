---
title: 'Öğretici: bir sanal ağı ExpressRoute devresine bağlama-Azure portal'
description: Bu öğreticide, Azure portal kullanarak bir sanal ağı Azure ExpressRoute bağlantı hattına bağlamak için nasıl bağlantı oluşturacağınız gösterilmektedir.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: a9bfb4beea2c2bec1b819d228215cfff65e37fe4
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110266"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Öğretici: portalı kullanarak bir ExpressRoute devresine sanal ağ bağlama

> [!div class="op_single_selector"]
> * [Azure portalı](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video-Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasik)](expressroute-howto-linkvnet-classic.md)
> 

Bu öğretici, Azure portal kullanarak bir sanal ağı Azure ExpressRoute bağlantı hattını bağlamak için bir bağlantı oluşturmanıza yardımcı olur. Azure ExpressRoute bağlantı hattına bağlandığınız sanal ağlar aynı abonelikte ya da başka bir aboneliğin parçası olabilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> - Sanal bir ağı aynı abonelikte bir devreye bağlama.
> - Sanal bir ağı farklı bir abonelikteki bir devreye bağlayın.
> - Sanal ağ ile ExpressRoute bağlantı hattı arasındaki bağlantıyı silin.

## <a name="prerequisites"></a>Önkoşullar

* Yapılandırmaya başlamadan önce [önkoşulları](expressroute-prerequisites.md), [yönlendirme gereksinimlerini](expressroute-routing.md)ve [iş akışlarını](expressroute-workflows.md) gözden geçirin.

* Etkin bir ExpressRoute bağlantı hattınızın olması gerekir.
  * [ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) bağlantı hattı oluşturmak için yönergeleri izleyin ve bağlantı sağlayıcınız tarafından devre dışı bırakıldı.
  * Devreniz için Azure özel eşlemesi 'nin yapılandırıldığından emin olun. Eşleme ve yönlendirme yönergeleri için [bir ExpressRoute bağlantı hattı oluşturma ve değiştirme](expressroute-howto-routing-portal-resource-manager.md) makalesine bakın.
  * Azure özel eşlemesinin yapılandırıldığından ve uçtan uca bağlantı için ağınız ile Microsoft arasında BGP eşlemesi oluşturduğunuzdan emin olun.
  * Oluşturulmuş ve tam olarak sağlanmış bir sanal ağa ve sanal ağ geçidine sahip olduğunuzdan emin olun. [ExpressRoute için sanal ağ geçidi oluşturma](expressroute-howto-add-gateway-resource-manager.md)yönergelerini izleyin. ExpressRoute için bir sanal ağ geçidi, VPN değil GatewayType ' ExpressRoute ' kullanır.

* 10 adede kadar sanal ağı standart bir ExpressRoute devresine bağlayabilirsiniz. Standart bir ExpressRoute bağlantı hattı kullanılırken tüm sanal ağların aynı geopolitik bölgede olması gerekir.

* Tek bir sanal ağ, en fazla 16 ExpressRoute devresine bağlanabilir. Bağlanmakta olduğunuz her ExpressRoute devresi için yeni bir bağlantı nesnesi oluşturmak üzere aşağıdaki işlemi kullanın. ExpressRoute devreleri aynı abonelikte, farklı aboneliklerde veya her ikisinin karışımı olabilir.

* ExpressRoute Premium eklentisini etkinleştirirseniz, ExpressRoute devresinin coğrafi bölgesinin dışındaki sanal ağları bağlayabilirsiniz. Premium eklenti Ayrıca, seçilen bant genişliğine bağlı olarak ExpressRoute bağlantı hattına 10 ' dan fazla sanal ağ bağlamanıza olanak tanır. Premium eklenti hakkında daha fazla bilgi için [SSS bölümüne](expressroute-faqs.md) bakın.

* Adımları daha iyi anlamak için başlamadan önce [bir video görüntüleyebilirsiniz](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) .

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>VNet 'i devre dışı bir aboneliğe bağlama

> [!NOTE]
> Katman 3 sağlayıcısı eşlerinizi yapılandırdıysa BGP yapılandırma bilgileri görünmez. Devreniz sağlanmış durumdaysa bağlantı oluşturabileceksiniz.
>

### <a name="to-create-a-connection"></a>Bağlantı oluşturmak için

1. ExpressRoute devreniz ve Azure özel eşlerinizin başarıyla yapılandırıldığından emin olun. ExpressRoute bağlantı hattı [oluşturma](expressroute-howto-circuit-arm.md) ve [bir ExpressRoute bağlantı hattı için eşleme oluşturma ve değiştirme](expressroute-howto-routing-arm.md)bölümündeki yönergeleri izleyin. ExpressRoute bağlantı hattınızı aşağıdaki görüntüde şöyle görünmelidir:

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="ExpressRoute devresi ekran görüntüsü":::

1. Artık sanal ağ geçidinizin ExpressRoute Devrenize bağlamak için bir bağlantı sağlamaya başlayabilirsiniz. Bağlantı **Ekle ' yi seçerek**  >   **bağlantı ekle** sayfasını açın.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="Bağlantı ekran görüntüsü ekleme":::

1. Bağlantı için bir ad girin ve ardından Ileri ' yi seçin **: ayarlar >**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-basic.png" alt-text="Bağlantı temel sayfası oluştur":::

1. Devreye bağlamak istediğiniz sanal ağa ait ağ geçidini seçin ve **gözden geçir + oluştur**' u seçin. Sonra doğrulama tamamlandıktan sonra **Oluştur** ' u seçin.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-settings.png" alt-text="Bağlantı ayarları oluşturma sayfası":::

1. Bağlantınız başarıyla yapılandırıldıktan sonra bağlantı nesneniz bağlantı bilgilerini gösterir.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="Bağlantı nesnesi ekran görüntüsü":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>VNet 'i devre dışı bir aboneliğe bağlama

Bir ExpressRoute devresini birden çok abonelik arasında paylaşabilirsiniz. Aşağıdaki şekilde, birden çok aboneliğin ExpressRoute devreleri için paylaşımın nasıl çalıştığına ilişkin basit bir şematik gösterilmektedir.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="Çapraz abonelik bağlantısı":::

Büyük buluttaki küçük bulutların her biri, bir kuruluştaki farklı departmanlara ait olan abonelikleri temsil etmek için kullanılır. Kuruluştaki bölümlerin her biri, hizmetlerini dağıtmak için kendi aboneliğini kullanır, ancak şirket içi ağınıza geri bağlanmak için tek bir ExpressRoute bağlantı hattı paylaşabilir. Tek bir departman (Bu örnekte:) ExpressRoute devresine sahip olabilir. Kuruluştaki diğer abonelikler ExpressRoute devresini kullanabilir.

  > [!NOTE]
  > Adanmış devre için bağlantı ve bant genişliği ücretleri ExpressRoute bağlantı hattı sahibine uygulanır. Tüm sanal ağlar aynı bant genişliğini paylaşır.
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Yönetim-devre sahipleri ve devre kullanıcıları hakkında

' Devowner ', ExpressRoute bağlantı hattı kaynağının yetkili bir uzman kullanıcısı. Devre sahibi, ' devre kullanıcıları ' tarafından kullanılabilecek yetkilendirmeler oluşturabilir. Devre kullanıcıları ExpressRoute bağlantı hattı ile aynı abonelikte olmayan sanal ağ geçitlerinin sahiplerinden oluşur. Devre kullanıcıları yetkilendirmeleri (sanal ağ başına bir yetkilendirme) kullanabilir.

Devre sahibinin, her zaman yetkilendirmeleri değiştirme ve iptal etme gücü vardır. Erişim iptal edilen abonelikten tüm bağlantı bağlantılarında silinen bir yetkilendirme sonuçları iptal ediliyor.

### <a name="circuit-owner-operations"></a>Devre sahibi işlemleri

**Bağlantı yetkilendirmesi oluşturmak için**

Devre sahibi, sanal ağ geçitlerini ExpressRoute devresine bağlamak için bir devre kullanıcısı tarafından kullanılacak bir yetkilendirme anahtarı oluşturan bir yetkilendirme oluşturur. Yetkilendirme yalnızca bir bağlantı için geçerlidir.

> [!NOTE]
> Her bağlantı için ayrı bir yetkilendirme gerekir.
>

1. ExpressRoute sayfasında **yetkilendirmeler** ' i seçin ve ardından yetkilendirme için bir **ad** yazın ve **Kaydet**' i seçin.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="Yetkilendirmeler":::

2. Yapılandırma kaydedildikten sonra **kaynak kimliği** ve **Yetkilendirme anahtarı**' nı kopyalayın.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="Yetkilendirme anahtarı":::

**Bir bağlantı yetkilendirmesini silmek için**

Bağlantınızın yetkilendirme anahtarının **Sil** simgesini seçerek bir bağlantıyı silebilirsiniz.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-authorization-key.png" alt-text="Yetkilendirme anahtarını Sil":::

Bağlantıyı silmek ve yetkilendirme anahtarını sürdürmek istiyorsanız, bağlantı devresinin bağlantı sayfasından bağlantıyı silebilirsiniz.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection-owning-circuit.png" alt-text="Bağlantı sahibi olan devresini Sil":::

### <a name="circuit-user-operations"></a>Devre Kullanıcı işlemleri

Devre kullanıcısının, devre sahibinden kaynak KIMLIĞI ve yetkilendirme anahtarı olması gerekir.

**Bir bağlantı yetkilendirmesini kullanma**

1. **+ Kaynak oluştur** düğmesini seçin. **Bağlantı** araması yapın ve **Oluştur**' u seçin.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="Yeni kaynaklar oluşturma":::

1. *Bağlantı türünün* **ExpressRoute** olarak ayarlandığından emin olun. *Kaynak grubunu* ve *konumu* seçin ve ardından temel bilgiler sayfasında **Tamam** ' ı seçin.

    > [!NOTE]
    > Konum, bağlantısını oluşturduğunuz sanal ağ geçidi konumuyla aynı *olmalıdır* .

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="Temel bilgiler sayfası":::

1. **Ayarlar** sayfasında, *sanal ağ geçidini* seçin ve **Yetkilendirmeyi** kullan onay kutusunu işaretleyin. *Yetkilendirme anahtarını* ve *eş bağlantı devre URI* 'sini girin ve bağlantıya bir ad verin. **Tamam**’ı seçin. 
 
    > [!NOTE]
    > *Eş devre URI 'si* , ExpressRoute bağlantı HATTıNıN kaynak kimliğidir (ExpressRoute devresinin Özellikler ayar bölmesinde bulabilirsiniz).

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="Ayarlar sayfası":::

1. **Özet** sayfasındaki bilgileri gözden geçirin ve **Tamam**' ı seçin.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-summary.png" alt-text="Özet sayfası":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bağlantınız için sayfadaki **Sil** simgesini seçerek bir bağlantıyı silebilir ve sanal ağınızın bir ExpressRoute bağlantı hattına bağlantısını kaldırabilirsiniz.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection.png" alt-text="Bağlantıyı Sil":::

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, sanal bir ağı aynı abonelikte ve farklı bir abonelikte bir devreye bağlamayı öğrendiniz. ExpressRoute ağ geçidi hakkında daha fazla bilgi için bkz.: 

> [!div class="nextstepaction"]
> [ExpressRoute sanal ağ geçitleri hakkında](expressroute-about-virtual-network-gateways.md)
