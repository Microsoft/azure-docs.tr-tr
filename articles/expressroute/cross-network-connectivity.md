---
title: Azure çapraz ağ bağlantısı
description: Bu sayfada, Azure ağ özelliklerine dayalı çapraz ağ bağlantısı ve çözümü için bir uygulama senaryosu açıklanır.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: e503dc2b4ae8773ebfedc7a9b73bc5ea93dd9d5a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076743"
---
# <a name="cross-network-connectivity"></a>Ağlar arası bağlantı

Fabrikam Inc. Doğu ABD 'de büyük bir fiziksel varlık ve Azure dağıtımı vardır. Fabrikam, ExpressRoute aracılığıyla şirket içi ve Azure dağıtımları arasında arka uç bağlantısına sahiptir. Benzer şekilde, contoso Ltd. Batı ABD ' de bir varlık ve Azure dağıtımı vardır. Contoso, ExpressRoute aracılığıyla şirket içi ve Azure dağıtımları arasında arka uç bağlantısına sahiptir.  

Fabrikam Inc. edinme contoso Ltd. Merger 'u takip eden Fabrikam, ağları bağlamak istemektedir. Aşağıdaki şekilde senaryo gösterilmektedir:

 [![1]][1]

Yukarıdaki şeklin ortasındaki kesik çizgili oklar, istenen ağ bağlantılarını gösterir. Özellikle, iki tür çapraz bağlantı istenir: 1) Fabrikam ve contoso sanal ağları çapraz bağlantısı, 2) çapraz bölgesel şirket içi ve sanal ağlar çapraz bağlantı (yani, Fabrikam şirket içi ağı contoso VNet 'e bağlama ve contoso bağlama Şirket içi ağdan fabrikam VNet 'e) ve 3) Fabrikam ve contoso şirket içi ağ çapraz bağlantı. 

Aşağıdaki tabloda, Merger 'tan önce contoso Ltd. için ExpressRoute 'un özel eşlemesinin yol tablosu gösterilmektedir.

[![2]][2]

Aşağıdaki tabloda, mermeyen bir sanal makinenin contoso aboneliğindeki etkin yolları gösterilmektedir. Her tablo için, VNet 'teki VM, VNet adres alanını ve contoso şirket içi ağını, varsayılan olanlardan ayrı olarak algılar. 

[![4]][4]

Aşağıdaki tabloda, Merger 'tan önce fabrikam Inc. için ExpressRoute 'un özel eşlemesinin yol tablosu gösterilmektedir.

[![3]][3]

Aşağıdaki tabloda, bir VM 'nin, birleşme öncesinde fabrikam aboneliğindeki etkin yolları gösterilmektedir. Her tablo için, VNet 'teki VM, VNet adres alanını ve Fabrikam şirket içi ağını, varsayılan olanlardan ayrı olarak algılar.

[![5]][5]

Bu makalede, adım adım ilerleyin ve aşağıdaki Azure ağ özelliklerini kullanarak istenen çapraz bağlantıları nasıl elde ettiğiyle tartışalım:

* [Sanal ağ eşleme][Virtual network peering] 
* [Sanal ağ ExpressRoute bağlantısı][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>VNET 'leri çapraz bağlama

Sanal ağ eşlemesi (VNet eşleme) iki sanal ağı bağlarken en iyi ve en iyi ağ performansını sağlar. VNet eşlemesi iki sanal ağın ikisini de aynı Azure bölgesinde (genellikle VNet eşlemesi olarak adlandırılır) ve iki farklı Azure bölgesinde (genellikle genel VNet eşlemesi olarak adlandırılır) eşlemeyi destekler. 

Contoso ve Fabrikam Azure aboneliklerindeki sanal ağlar arasında küresel VNet eşlemesini yapılandıralim. İki sanal ağ arasında sanal ağ eşlemesi oluşturma hakkında daha fazla bilgi için bkz. [sanal ağ eşlemesi oluşturma][Configure VNet peering] makalesi.

Aşağıdaki resimde genel VNet eşlemesi yapılandırıldıktan sonra ağ mimarisi gösterilmektedir.

[![inç]][6]

Aşağıdaki tabloda, contoso aboneliği VM 'si tarafından bilinen rotalar gösterilmektedir. Tablonun son girişine dikkat edin. Bu giriş, sanal ağların çapraz bağlantı kurma sonucudur.

[![7@@]][7]

Aşağıdaki tabloda fabrikam abonelik VM 'si tarafından bilinen rotalar gösterilmektedir. Tablonun son girişine dikkat edin. Bu giriş, sanal ağların çapraz bağlantı kurma sonucudur.

[![240]][8]

VNet eşlemesi iki sanal ağı doğrudan bağlar (yukarıdaki iki tabloda *Vnetglobaleşleme* girişi için sonraki atlama yok başlığına bakın)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>VNET 'leri şirket içi ağlara çapraz bağlama

Birden çok sanal ağa ExpressRoute bağlantı hattı bağlayabiliriz. Bir ExpressRoute bağlantı hattına bağlı olabilecek en fazla sanal ağ sayısı için [abonelik ve hizmet sınırlarına][Subscription limits] bakın. 

Böylece, sanal ağlar ve şirket içi ağlar arasında çapraz bağlantıyı etkinleştirmek için fabrikam ExpressRoute bağlantı hattını contoso aboneliği VNet 'e ve benzer contoso ExpressRoute devresi ' i fabrikam abonelik VNet 'e bağlayalim. Farklı bir abonelikte bir ExpressRoute devresine bir sanal ağ bağlamak için bir yetkilendirme oluşturup kullandık.  Bkz: [sanal ağı bir ExpressRoute devresine bağlama][Connect-ER-VNet].

Aşağıdaki resimde, sanal ağlara yönelik ExpressRoute çapraz bağlantısı yapılandırıldıktan sonra ağ mimarisi gösterilmektedir.

[![tuşlarına]][9]

Aşağıdaki tabloda, contoso Ltd 'nin ExpressRoute 'un özel eşlemesinin yol tablosu gösterilmektedir. Bu, sanal ağları ExpressRoute aracılığıyla şirket içi ağlara bağladıktan sonra. Yol tablosunun her iki sanal ağa ait yollara sahip olduğunu görün.

[![(]][10]

Aşağıdaki tabloda, Azure 'un ExpressRoute aracılığıyla sanal ağları şirket içi ağlara bağladıktan sonra, Fabrikam Inc. ExpressRoute 'un özel eşlemesinin yol tablosu gösterilmektedir. Yol tablosunun her iki sanal ağa ait yollara sahip olduğunu görün.

[![üst]][11]

Aşağıdaki tabloda, contoso aboneliği VM 'si tarafından bilinen rotalar gösterilmektedir. Tablonun *sanal ağ geçidi* girişlerine dikkat edin. VM, hem şirket içi ağlar için rotalar görür.

[![+]][12]

Aşağıdaki tabloda fabrikam abonelik VM 'si tarafından bilinen rotalar gösterilmektedir. Tablonun *sanal ağ geçidi* girişlerine dikkat edin. VM, hem şirket içi ağlar için rotalar görür.

[![hatası]][13]

>[!NOTE]
>Fabrikam ve/veya contoso aboneliklerinde, ilgili hub VNet 'e bağlı olan sanal ağlara da sahip olabilirsiniz (bir hub ve bağlı bileşen tasarımı bu makaledeki mimari diyagramlarında gösterilmektedir). Hub VNet ağ geçitleri arasında ExpressRoute 'a çapraz bağlantılar, Doğu ve Batı hub 'ları ile bağlı bileşenleri arasında iletişime da izin verir.
>

## <a name="cross-connecting-on-premises-networks"></a>Şirket içi ağları çapraz bağlama

ExpressRoute Global Reach, farklı ExpressRoute devrelerine bağlı olan şirket içi ağlar arasında bağlantı sağlar. Contoso ve Fabrikam ExpressRoute devreleri arasında Global Reach yapılandıralim. ExpressRoute devreleri farklı aboneliklerde olduğundan, bir yetkilendirme oluşturup kullandık. Adım adım yönergeler için bkz. [ExpressRoute 'ı yapılandırma Global Reach][Configure Global Reach] makalesi.

Aşağıdaki resimde Global Reach yapılandırıldıktan sonra ağ mimarisi gösterilmektedir.

[![May]][14]

Aşağıdaki tabloda, Global Reach yapılandırıldıktan sonra contoso Ltd. için ExpressRoute 'un özel eşlemesinin yol tablosu gösterilmektedir. Yol tablosunun hem şirket içi ağlara ait yollara sahip olduğunu görün. 

[![aşamaz]][15]

Aşağıdaki tabloda, Global Reach yapılandırıldıktan sonra fabrikam Inc. ' nin ExpressRoute 'un özel eşlemesinin yol tablosu gösterilmektedir. Yol tablosunun hem şirket içi ağlara ait yollara sahip olduğunu görün.

[![k]][16]

## <a name="next-steps"></a>Sonraki adımlar

VNet ve VNet eşlemesi hakkında daha fazla soru için bkz. [sanal ağ hakkında SSS][VNet-FAQ]. ExpressRoute ve sanal ağ bağlantısı hakkında daha fazla soru için bkz. [EXPRESSROUTE SSS][ER-FAQ] .

Global Reach ülkeye/bölgeye göre ülke/bölge bazında alınır. Global Reach, istediğiniz ülkelerde/bölgelerde kullanılabilir olup olmadığını görmek için bkz. [ExpressRoute Global Reach][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "uygulama senaryosu"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "contoso ExpressRoute yol tablosu merkli"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "fabrikam ExpressRoute yol tablosu merkli"
[]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "birleşme 'tan önce 4 contoso VM rotaları"
[]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "birleşme 'tan önce 5 fabrikam VM yolu"
[6]: ./media/cross-network-connectivity/vnet-peering.png "VNET eşleme sonrası mimari"
[]: ./media/cross-network-connectivity/contosovm-routes-peering.png "VNET eşlemeden sonra 7 contoso VM yolları"
[]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "VNET eşlemeden sonra 8 fabrikam VM yolu"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "mimariden sonraki mimari çapraz bağlantı"
[]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "EXR ve VNET 'leri bağladıktan sonra 10 contoso ExpressRoute yol tablosu"
[]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "EXR ve sanal ağları çapraz bağladıktan sonra 11 fabrikam ExpressRoute yol tablosu"
[]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "EXR ve sanal ağları çapraz bağladıktan sonra 12 contoso VM yolları"
[]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "EXR ve sanal ağları çapraz bağladıktan sonra 13 fabrikam VM yolları"
[14]: ./media/cross-network-connectivity/globalreach.png "Global Reach yapılandırdıktan sonra mimari"
[]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Global Reach sonra 15 contoso ExpressRoute yol tablosu"
[]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Global Reach sonra 16 fabrikam ExpressRoute yol tablosu"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq