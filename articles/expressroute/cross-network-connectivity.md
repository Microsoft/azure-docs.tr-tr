---
title: Azure çapraz ağ bağlantısı
description: Bu sayfada, Azure ağ özelliklerine dayalı çapraz ağ bağlantısı ve çözümü için bir uygulama senaryosu açıklanır.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: duau
ms.openlocfilehash: 018afa1b2a31ebd44925a3fc79cbdc729b2d4695
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202506"
---
# <a name="cross-network-connectivity"></a>Ağlar arası bağlantı

Fabrikam Inc. Doğu ABD 'de büyük bir fiziksel varlık ve Azure dağıtımı vardır. Fabrikam, ExpressRoute aracılığıyla şirket içi ve Azure dağıtımları arasında arka uç bağlantısına sahiptir. Benzer şekilde, contoso Ltd. Batı ABD ' de bir varlık ve Azure dağıtımı vardır. Contoso, ExpressRoute aracılığıyla şirket içi ve Azure dağıtımları arasında arka uç bağlantısına sahiptir.  

Fabrikam Inc. edinme contoso Ltd. Merger 'u takip eden Fabrikam, ağları bağlamak istemektedir. Aşağıdaki şekilde senaryo gösterilmektedir:

![Uygulama senaryosu](./media/cross-network-connectivity/premergerscenario.png)

Yukarıdaki şeklin ortasındaki kesik çizgili oklar, istenen ağ bağlantılarını gösterir. Özellikle, üç tür çapraz bağlantı istenir: 1) Fabrikam ve contoso VNET çapraz bağlantısı, 2) çapraz bölgesel şirket içi ve VNET çapraz bağlantı (yani fabrikam şirket içi ağı contoso VNet 'e bağlama ve contoso şirket içi ağını fabrikam VNet 'e bağlama) ve 3) Fabrikam ve contoso şirket içi ağ çapraz bağlantı. 

Aşağıdaki tabloda, Merger 'tan önce contoso Ltd. için ExpressRoute 'un özel eşlemesinin yol tablosu gösterilmektedir.

![Merkli olmadan önce contoso ExpressRoute yol tablosu](./media/cross-network-connectivity/contosoexr-rt-premerger.png)

Aşağıdaki tabloda, mermeyen bir sanal makinenin contoso aboneliğindeki etkin yolları gösterilmektedir. Her tablo için, VNet 'teki VM, VNet adres alanını ve contoso şirket içi ağını, varsayılan olanlardan ayrı olarak algılar.

![Merkli başlamadan önce contoso VM rotaları](./media/cross-network-connectivity/contosovm-routes-premerger.png)

Aşağıdaki tabloda, Merger 'tan önce fabrikam Inc. için ExpressRoute 'un özel eşlemesinin yol tablosu gösterilmektedir.

![Birleşme öncesinde fabrikam ExpressRoute yol tablosu](./media/cross-network-connectivity/fabrikamexr-rt-premerger.png)

Aşağıdaki tabloda, bir VM 'nin, birleşme öncesinde fabrikam aboneliğindeki etkin yolları gösterilmektedir. Her tablo için, VNet 'teki VM, VNet adres alanını ve Fabrikam şirket içi ağını, varsayılan olanlardan ayrı olarak algılar.

![Birleşme 'tan önce fabrikam VM yolları](./media/cross-network-connectivity/fabrikamvm-routes-premerger.png)

Bu makalede, adım adım ilerleyin ve aşağıdaki Azure ağ özelliklerini kullanarak istenen çapraz bağlantıları nasıl elde ettiğiyle tartışalım:

* [Sanal ağ eşleme][Virtual network peering] 
* [Sanal ağ ExpressRoute bağlantısı][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>VNET 'leri çapraz bağlama

Sanal ağ eşlemesi (VNet eşleme) iki sanal ağı bağlarken en iyi ve en iyi ağ performansını sağlar. VNet eşlemesi iki sanal ağın ikisini de aynı Azure bölgesinde (genellikle VNet eşlemesi olarak adlandırılır) ve iki farklı Azure bölgesinde (genellikle genel VNet eşlemesi olarak adlandırılır) eşlemeyi destekler. 

Contoso ve Fabrikam Azure aboneliklerindeki sanal ağlar arasında küresel VNet eşlemesini yapılandıralim. İki sanal ağ arasında sanal ağ eşlemesi oluşturma hakkında daha fazla bilgi için bkz. [sanal ağ eşlemesi oluşturma][Configure VNet peering] makalesi.

Aşağıdaki resimde genel VNet eşlemesi yapılandırıldıktan sonra ağ mimarisi gösterilmektedir.

![VNet eşleme sonrası mimari](./media/cross-network-connectivity/vnet-peering.png )

Aşağıdaki tabloda, contoso aboneliği VM 'si tarafından bilinen rotalar gösterilmektedir. Tablonun son girişine dikkat edin. Bu giriş, sanal ağların çapraz bağlantı kurma sonucudur.

![VNet eşlemesi sonrasında contoso VM rotaları](./media/cross-network-connectivity/contosovm-routes-peering.png)

Aşağıdaki tabloda fabrikam abonelik VM 'si tarafından bilinen rotalar gösterilmektedir. Tablonun son girişine dikkat edin. Bu giriş, sanal ağların çapraz bağlantı kurma sonucudur.

![Sanal ağ eşlemeden sonra fabrikam VM rotaları](./media/cross-network-connectivity/fabrikamvm-routes-peering.png)

VNet eşlemesi iki sanal ağı doğrudan bağlar (yukarıdaki iki tabloda *Vnetglobaleşleme* girişi için sonraki atlama yok başlığına bakın)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>VNET 'leri şirket içi ağlara çapraz bağlama

Birden çok sanal ağa ExpressRoute bağlantı hattı bağlayabiliriz. Bir ExpressRoute bağlantı hattına bağlı olabilecek en fazla sanal ağ sayısı için [abonelik ve hizmet sınırlarına][Subscription limits] bakın. 

Böylece, sanal ağlar ve şirket içi ağlar arasında çapraz bağlantıyı etkinleştirmek için fabrikam ExpressRoute bağlantı hattını contoso aboneliği VNet 'e ve benzer contoso ExpressRoute devresi ' i fabrikam abonelik VNet 'e bağlayalim. Farklı bir abonelikte bir ExpressRoute devresine bir sanal ağ bağlamak için bir yetkilendirme oluşturup kullandık.  Bkz: [sanal ağı bir ExpressRoute devresine bağlama][Connect-ER-VNet].

Aşağıdaki resimde, sanal ağlara yönelik ExpressRoute çapraz bağlantısı yapılandırıldıktan sonra ağ mimarisi gösterilmektedir.

![ExpressRoute ile çapraz bağlantı sonrası mimari](./media/cross-network-connectivity/exr-x-connect.png)

Aşağıdaki tabloda, contoso Ltd 'nin ExpressRoute 'un özel eşlemesinin yol tablosu gösterilmektedir. Bu, sanal ağları ExpressRoute aracılığıyla şirket içi ağlara bağladıktan sonra. Yol tablosunun her iki sanal ağa ait yollara sahip olduğunu görün.

![ExR ve sanal ağları çapraz bağladıktan sonra contoso ExpressRoute yol tablosu](./media/cross-network-connectivity/contosoexr-rt-xconnect.png)

Aşağıdaki tabloda, Azure 'un ExpressRoute aracılığıyla sanal ağları şirket içi ağlara bağladıktan sonra, Fabrikam Inc. ExpressRoute 'un özel eşlemesinin yol tablosu gösterilmektedir. Yol tablosunun her iki sanal ağa ait yollara sahip olduğunu görün.

![ExR ve sanal ağları çapraz bağladıktan sonra fabrikam ExpressRoute yol tablosu](./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png)

Aşağıdaki tabloda, contoso aboneliği VM 'si tarafından bilinen rotalar gösterilmektedir. Tablonun *sanal ağ geçidi* girişlerine dikkat edin. VM, hem şirket içi ağlar için rotalar görür.

![ExR ve sanal ağları çapraz bağladıktan sonra contoso VM rotaları](./media/cross-network-connectivity/contosovm-routes-xconnect.png)

Aşağıdaki tabloda fabrikam abonelik VM 'si tarafından bilinen rotalar gösterilmektedir. Tablonun *sanal ağ geçidi* girişlerine dikkat edin. VM, hem şirket içi ağlar için rotalar görür.

![ExR ve sanal ağları çapraz bağladıktan sonra fabrikam VM rotaları](./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png)

>[!NOTE]
>Fabrikam ve/veya contoso aboneliklerinde, ilgili hub VNet 'e bağlı olan sanal ağlara da sahip olabilirsiniz (bir hub ve bağlı bileşen tasarımı bu makaledeki mimari diyagramlarında gösterilmektedir). Hub VNet ağ geçitleri arasında ExpressRoute 'a çapraz bağlantılar, Doğu ve Batı hub 'ları ile bağlı bileşenleri arasında iletişime da izin verir.
>

## <a name="cross-connecting-on-premises-networks"></a>Şirket içi ağları çapraz bağlama

ExpressRoute Global Reach, farklı ExpressRoute devrelerine bağlı olan şirket içi ağlar arasında bağlantı sağlar. Contoso ve Fabrikam ExpressRoute devreleri arasında Global Reach yapılandıralim. ExpressRoute devreleri farklı aboneliklerde olduğundan, bir yetkilendirme oluşturup kullandık. Adım adım yönergeler için bkz. [ExpressRoute 'ı yapılandırma Global Reach][Configure Global Reach] makalesi.

Aşağıdaki resimde Global Reach yapılandırıldıktan sonra ağ mimarisi gösterilmektedir.

![Global Reach yapılandırdıktan sonra mimari](./media/cross-network-connectivity/globalreach.png)

Aşağıdaki tabloda, Global Reach yapılandırıldıktan sonra contoso Ltd. için ExpressRoute 'un özel eşlemesinin yol tablosu gösterilmektedir. Yol tablosunun hem şirket içi ağlara ait yollara sahip olduğunu görün. 

![Global Reach sonrasında contoso ExpressRoute yol tablosu](./media/cross-network-connectivity/contosoexr-rt-gr.png)

Aşağıdaki tabloda, Global Reach yapılandırıldıktan sonra fabrikam Inc. ' nin ExpressRoute 'un özel eşlemesinin yol tablosu gösterilmektedir. Yol tablosunun hem şirket içi ağlara ait yollara sahip olduğunu görün.

![Global Reach sonra fabrikam ExpressRoute yol tablosu]( ./media/cross-network-connectivity/fabrikamexr-rt-gr.png )

## <a name="next-steps"></a>Sonraki adımlar

VNet ve VNet eşlemesi hakkında daha fazla soru için bkz. [sanal ağ hakkında SSS][VNet-FAQ]. ExpressRoute ve sanal ağ bağlantısı hakkında daha fazla soru için bkz. [EXPRESSROUTE SSS][ER-FAQ] .

Global Reach ülkeye/bölgeye göre ülke/bölge bazında alınır. Global Reach, istediğiniz ülkelerde/bölgelerde kullanılabilir olup olmadığını görmek için bkz. [ExpressRoute Global Reach][Global Reach].

<!--Link References-->
[Virtual network peering]: ../virtual-network/virtual-network-peering-overview.md
[connection]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[Global Reach]: ./expressroute-global-reach.md
[Configure VNet peering]: ../virtual-network/create-peering-different-subscriptions.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[Subscription limits]: ../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits
[Connect-ER-VNet]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[ER-FAQ]: ./expressroute-faqs.md
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq