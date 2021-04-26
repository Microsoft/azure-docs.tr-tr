---
title: "Mimari: Azure sanal WAN 'a geçiş"
description: Azure sanal WAN 'a geçiş yapmayı öğrenin.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: cherylmc
ms.openlocfilehash: e602905b461e370189cefed706ddc3a47e0199fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91839648"
---
# <a name="migrate-to-azure-virtual-wan"></a>Azure Sanal WAN’a geçiş

Azure sanal WAN, şirketlerin Microsoft Global ağı ölçeğinden faydalanmak için genel bağlantılarını basitleştirmesini sağlar. Bu makalede, mevcut bir müşteri tarafından yönetilen hub ve bağlı olmayan topolojiden, Microsoft tarafından yönetilen sanal WAN hub 'larını kullanan bir tasarıma geçiş yapmak isteyen şirketler için teknik ayrıntılar sağlanmaktadır.

Azure sanal WAN 'ın bulut merkezli modern kurumsal küresel ağı benimseme sağladığı avantajlar hakkında daha fazla bilgi için bkz. [küresel aktarım ağı mimarisi ve sanal WAN](virtual-wan-global-transit-network-architecture.md).

:::image type="content" source="./media/migrate-from-hub-spoke-topology/hub-spoke.png" alt-text="Hub ve bağlı bileşen":::
**Şekil: Azure sanal WAN**

Azure hub ve-kol bağlantı modeli, Azure ağ 'ın basit ve ölçeklenebilir bulut ağları oluşturmak için varsayılan geçişli yönlendirme davranışından yararlanmak üzere binlerce müşterimiz tarafından benimsenmiştir. Azure sanal WAN derlemeleri bu kavramlarda, yalnızca şirket içi konumlar ve Azure arasında değil, genel bağlantı Topolojilerine izin veren yeni yetenekler sunar, ancak müşterilerin mevcut küresel ağlarını artırmak için Microsoft ağı ölçeğinden yararlanmasını sağlar.

Bu makalede, mevcut bir müşteri tarafından yönetilen hub ve bağlı olan ortamın Azure sanal WAN 'ına dayalı bir topolojiye geçirilmesi gösterilmektedir.

## <a name="scenario"></a>Senaryo

Contoso, Avrupa ve Asya 'daki ofislerle küresel bir mali kuruluştur. Bu uygulamalar, içindeki şirket içi veri merkezinden Azure 'a taşımak için planlarlar ve karma bağlantı için bölgesel hub sanal ağları dahil olmak üzere müşteri tarafından yönetilen hub ve bağlı bileşen mimarisine dayalı bir temel tasarım sağlar. Bulut tabanlı teknolojilerde taşıma kapsamında, ağ ekibi, bağlantısının iş için iyileştirildiğinden emin olmayı sağlar.

Aşağıdaki şekilde, birden çok Azure bölgesine bağlantı dahil olmak üzere var olan küresel ağın üst düzey bir görünümü gösterilmektedir.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png" alt-text="Contoso var olan ağ topolojisi":::
**Şekil: contoso var olan ağ topolojisi**

Mevcut ağ topolojisinden aşağıdaki noktaları anlamış olabilir:

* Hub ve bağlı bileşen topolojisi, bir ortak özel geniş alan ağına (WAN) geri bağlantı için ExpressRoute devreleri dahil olmak üzere birden çok bölgede kullanılır.

* Bu sitelerden bazılarının, bulutta barındırılan uygulamalara ulaşmak için doğrudan Azure 'da VPN tünellerini de vardır.

## <a name="requirements"></a>Gereksinimler

Ağ ekibi, contoso geçişini destekleyebilen ve maliyet, ölçek ve performans alanlarında en uygun hale getirebilen bir genel ağ modeli sunmaya eklenmiştir. Özet bölümünde aşağıdaki gereksinimlerin karşılanması gerekir:

* Bulut barındırılan uygulamalar için iyileştirilmiş yola sahip hem baş çeyrek (HQ) hem de şube ofislerini sağlayın.
* Aşağıdaki bağlantı yollarını korurken, mevcut şirket içi veri merkezlerinden (DC) VPN sonlandırmasına ilişkin rahatlığı kaldırın:
  * **Daldan VNET**: VPN bağlantılı ofislerde, yerel Azure bölgesindeki buluta geçirilen uygulamalara erişim sağlayabilmelidir.
  * **Hub 'Dan VNET 'e daldan hub**'a: VPN bağlantılı ofislerde, uzak Azure bölgesindeki buluta geçirilen uygulamalara erişim sağlayabilmelidir.
  * **Daldan dala**: bölgesel VPN bağlı ofisleri, birbirleriyle ve ExpressRoute bağlantılı HQ/DC siteleriyle iletişim kurabilmelidir.
  * **Hub 'dan Dala Dala**: genel olarak ayrılmış VPN bağlı ofisleri birbirleriyle ve tüm ExpressRoute ile bağlantılı HQ/DC siteleriyle iletişim kurabilmelidir.
  * **Daldan Internet**: bağlı sitelerin, internet ile iletişim kurabilmesi gerekir. Bu trafiğin filtrelenmiş ve günlüğe kaydedilmiş olması gerekir.
  * **VNET-VNET**: aynı bölgedeki bağlı bileşen sanal ağlarının birbirleriyle iletişim kurabilmesi gerekir.
  * Sanal ağdan hub 'a- **VNET**'e: farklı bölgelerdeki bağlı bileşen sanal ağlarının birbirleriyle iletişim kurabilmesi gerekir.
* Contoso dolaşım kullanıcılarının (dizüstü ve telefon) şirket ağında bulunmayan şirket kaynaklarına erişmesine olanak sağlar.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Azure sanal WAN mimarisi

Aşağıdaki şekilde, önceki bölümde açıklanan gereksinimleri karşılamak üzere Azure sanal WAN kullanılarak güncelleştirilmiş hedef topolojinin üst düzey bir görünümü gösterilmektedir.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/vwan-architecture.png" alt-text="Contoso sanal WAN mimarisi":::
**Şekil: Azure sanal WAN mimarisi**

Özet:

* Avrupa 'daki HQ ExpressRoute 'a bağlı, Avrupa Şirket içi DC, Azure 'a tamamen geçirilir ve kullanımdan çıkarıldı.
* Asya DC ve HQ, Özel WAN 'a bağlı kalır. Azure sanal WAN artık yerel taşıyıcı ağı artırmak ve genel bağlantı sağlamak için kullanılmaktadır.
* ExpressRoute ve VPN bağlantılı cihazlara yönelik bağlantı hub 'ı sağlamak için hem Batı Avrupa hem de Güney Doğu Asya Azure bölgelerinde dağıtılan Azure sanal WAN hub 'ları.
* Hub 'lar aynı zamanda küresel ağ ağına yönelik OpenVPN bağlantısını kullanan birden çok istemci türündeki dolaşım kullanıcıları için VPN sonlandırmasını sağlar, ancak yalnızca Azure 'a geçirilen uygulamalara değil, şirket içinde kalan tüm kaynakları da erişime izin verir.
* Azure sanal WAN tarafından sunulan bir sanal ağ içindeki kaynaklar için Internet bağlantısı.

Azure sanal WAN tarafından da sunulan uzak siteler için Internet bağlantısı. Microsoft 365 gibi SaaS hizmetlerine iyileştirilmiş erişim için iş ortağı tümleştirmesiyle desteklenen yerel internet kırılımı.

## <a name="migrate-to-virtual-wan"></a>Sanal WAN'a geçme

Bu bölümde, Azure sanal WAN 'a geçiş için çeşitli adımlar gösterilmektedir.

### <a name="step-1-single-region-customer-managed-hub-and-spoke"></a>1. Adım: tek bölge müşterinin yönettiği hub-ve-kol

Aşağıdaki şekilde, Azure sanal WAN 'ın piyasaya sürülmesinden önce Contoso için tek bir bölge topolojisi gösterilmektedir:

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure1.png" alt-text="Tek bölge topolojisi":::
**Şekil 1: tek bölge el ile merkezi-ve-bağlı**

Hub ve bağlı bileşen yaklaşımına sahip olmak üzere, müşteri tarafından yönetilen hub sanal ağı çeşitli işlev blokları içerir:

* Paylaşılan hizmetler (birden çok bağlı bileşen için gereken tüm ortak işlevleri). Örnek: contoso, hizmet olarak altyapı (IaaS) sanal makinelerinde Windows Server etki alanı denetleyicileri kullanır.
* IP/yönlendirme güvenlik duvarı hizmetleri, bir üçüncü taraf ağ sanal gereci tarafından sağlanır ve bağlı bileşen 2. Katman 3 IP yönlendirmesi etkinleştiriliyor.
* Gelen HTTPS istekleri için Azure Application Gateway ve internet kaynaklarına filtrelenmiş giden erişim için sanal makinelerde çalışan üçüncü taraf ara sunucu hizmetleri dahil Internet giriş/çıkış Hizmetleri.
* Şirket içi ağlara bağlantı için ExpressRoute ve VPN sanal ağ geçidi.

### <a name="step-2-deploy-virtual-wan-hubs"></a>2. Adım: sanal WAN hub 'larını dağıtma

Her bölgeye bir sanal WAN hub 'ı dağıtın. Sanal WAN hub 'ını VPN ve ExpressRoute işlevselliğiyle birlikte aşağıdaki makalelerde açıklandığı gibi ayarlayın:

* [Öğretici: Azure Sanal WAN kullanarak siteden siteye bağlantı oluşturma](virtual-wan-site-to-site-portal.md)
* [Öğretici: Azure sanal WAN kullanarak bir ExpressRoute ilişkilendirmesi oluşturma](virtual-wan-expressroute-portal.md)

> [!NOTE]
> Azure sanal WAN, bu makalede gösterilen bazı trafik yollarını etkinleştirmek için standart SKU 'YU kullanıyor olmalıdır.
>

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure2.png" alt-text="Sanal WAN hub 'larını dağıtma":::
**Şekil 2: müşteri tarafından yönetilen hub ve sanal WAN geçişine bağlı bileşen**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>3. Adım: uzak siteleri (ExpressRoute ve VPN) sanal WAN 'a bağlama

Sanal WAN hub 'ını mevcut ExpressRoute devrelerine bağlayın ve Internet üzerinden siteden siteye VPN 'Leri uzak dallara ayarlayın.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure3.png" alt-text="Uzak siteleri sanal WAN 'a bağlama":::
**Şekil 3: müşteri tarafından yönetilen hub ve sanal WAN geçişine bağlı bileşen**

Bu noktada, şirket içi ağ ekipmanı, sanal WAN ile yönetilen hub VNet 'e atanan IP adresi alanını yansıtan yollar almaya başlar. Uzak VPN bağlantılı dallar bu aşamada, bağlı olan sanal ağlardaki mevcut uygulamaların iki yolunu görürsünüz. Bu cihazlar, geçiş aşamasında simetrik yönlendirmeyi sağlamak için müşteri tarafından yönetilen hub 'a tüneli kullanmaya devam edecek şekilde yapılandırılmalıdır.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>4. Adım: sanal WAN aracılığıyla karma bağlantıyı test etme

Üretim bağlantısı için yönetilen sanal WAN hub 'ını kullanmadan önce, bir test bağlı ağ sanal ağı ve sanal WAN VNet bağlantısı ayarlamanızı öneririz. Sonraki adımlarla devam etmeden önce bu test ortamına yapılan bağlantıların ExpressRoute ve siteden siteye VPN aracılığıyla çalıştığını doğrulayın.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure4.png" alt-text="Sanal WAN aracılığıyla karma bağlantıyı test etme":::
**Şekil 4: müşteri tarafından yönetilen hub ve sanal WAN geçişine bağlı bileşen**

Bu aşamada, hem orijinal müşteri tarafından yönetilen hub sanal ağının hem de yeni sanal WAN hub 'ının aynı ExpressRoute devresine bağlı olduğunu bilmek önemlidir. Bu nedenle, iletişim kurmak için her iki ortamda da bağlı olan ışınsal 'yi etkinleştirmek üzere kullanılabilecek bir trafik yoludur. Örneğin, müşteri tarafından yönetilen hub sanal ağına bağlı bir alt ağdan gelen trafik, yeni sanal WAN hub 'ına VNet bağlantısı aracılığıyla bağlı olan herhangi bir bağlı ağa ulaşmak üzere ExpressRoute bağlantı hattı için kullanılan MSEE cihazlarını gezmez. Bu, 5. adımdaki geçiş uçların aşamalı geçişini sağlar.

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>5. Adım: sanal WAN hub 'ına geçiş bağlantısı

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure5.png" alt-text="Sanal WAN hub 'ına geçiş bağlantısı":::
**Şekil 5: müşteri tarafından yönetilen hub-ve-sanal WAN geçişine bağlı**

**a**. Bağlı olan sanal ağlardan gelen mevcut eşleme bağlantılarını, müşteri tarafından yönetilen eski hub 'a silin. A-c adımları tamamlanana kadar, bağlı olan sanal ağlardaki uygulamalara erişim kullanılamaz.

**b**. VNet bağlantıları üzerinden sanal WAN hub 'ına bağlı olan sanal ağları bağlayın.

**c**. Bağlı olan sanal ağlarda daha önce kullanılan Kullanıcı tanımlı yolları (UDR), bağlı bileşen iletişimleri için kaldırın. Bu yol artık sanal WAN hub 'ında bulunan dinamik yönlendirme tarafından etkinleştirilir.

**d**. Müşteri tarafından yönetilen hub 'daki mevcut ExpressRoute ve VPN ağ geçitleri artık sonraki adıma (e) izin vermek için kullanımdan çıkarıldı.

**e**. Eski müşteri tarafından yönetilen hub 'ı (hub sanal ağı) yeni bir VNet bağlantısı aracılığıyla sanal WAN hub 'ına bağlayın.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>6. Adım: eski hub, paylaşılan hizmetlere bağlı olur

Artık Azure ağımızı, sanal WAN hub 'ını yeni topolojimize merkezi bir noktaya getirmek için yeniden tasarladık.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure6.png" alt-text="Eski hub, paylaşılan hizmetlere bağlı olur":::
**Şekil 6: müşteri tarafından yönetilen hub ve sanal WAN geçişine bağlı bileşen**

Sanal WAN hub 'ı yönetilen bir varlık olduğundan ve sanal makineler gibi özel kaynakların dağıtımına izin vermediğinden, paylaşılan hizmetler bloğu artık bağlı bir sanal ağ olarak bulunur ve Azure Application Gateway veya ağ sanallaştırılmış gereci aracılığıyla internet girişi gibi işlevleri barındırır. Paylaşılan hizmetler ortamı ve arka uç sanal makineleri arasındaki trafik artık sanal WAN ile yönetilen hub 'ı geçişli.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>7. Adım: sanal WAN 'ı tam olarak kullanmak için şirket içi bağlantıyı Iyileştirin

Contoso, bu aşamada genellikle şirket içi DC içinde kalan bazı eski uygulamalarla Microsoft Bulut içinde iş uygulamalarının geçişlerini tamamlamıştır.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure7.png" alt-text="Sanal WAN 'ı tam olarak kullanmak için şirket içi bağlantıyı iyileştirin":::
**Şekil 7: müşteri tarafından yönetilen hub ve sanal WAN geçişine bağlı bileşen**

Contoso, Azure sanal WAN 'ın tüm işlevselliğinden yararlanmak için eski şirket içi VPN bağlantılarının yetkisini almaya karar verir. HQ veya DC ağlarına erişmeye devam eden dallar, Azure sanal WAN ' ın yerleşik aktarma yönlendirmesini kullanarak Microsoft Global ağını iletiyor.

> [!NOTE]
> ExpressRoute Global Reach ExpressRoute ulaşım 'e ExpressRoute (gösterilmez Şekil 7) sağlamak için Microsoft omurga 'tan yararlanmak isteyen müşteriler için ExpressRoute gereklidir.
>

## <a name="end-state-architecture-and-traffic-paths"></a>Son durum mimarisi ve trafik yolları

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure8.png" alt-text="Son durum mimarisi ve trafik yolları":::
**Şekil: Çift bölge sanal WAN**

Bu bölüm, bazı örnek trafik akışlarına bakarak bu topolojinin özgün gereksinimleri nasıl karşıladığına ilişkin bir Özet sağlar.

### <a name="path-1"></a>Yol 1

Yol 1, Asya 'daki S2S VPN bağlantılı dalından Güney Doğu Asya bölgesindeki bir Azure VNet 'e trafik akışını gösterir.

Trafik şu şekilde yönlendirilir:

* Asya dalı, esnek S2S BGP özellikli tüneller aracılığıyla Güney Doğu Asya sanal WAN hub 'ına bağlanır.

* Asya sanal WAN hub trafiği bağlı VNet 'e yerel olarak yönlendirir.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow1.png" alt-text="Flow 1":::

### <a name="path-2"></a>Yol 2

Yol 2, Avrupa HQ 'ya bağlı olan ExpressRoute 'daki trafik akışını Güney Doğu Asya bölgesindeki bir Azure VNet 'e gösterir.

Trafik şu şekilde yönlendirilir:

* Avrupa HQ, Batı Avrupa sanal WAN hub 'ına ExpressRoute bağlantı hattı aracılığıyla bağlanır.

* Sanal WAN hub 'dan hub 'a genel bağlantı, uzak bölgede bağlı olan VNet 'e trafik aktarımına olanak sağlar.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow2.png" alt-text="Akış 2":::

### <a name="path-3"></a>Yol 3

Yol 3, bir Avrupa S2S bağlı dalına Özel WAN 'a bağlı olan Asya şirket içi DC 'den trafik akışını gösterir.

Trafik şu şekilde yönlendirilir:

* Asya DC, yerel özel WAN taşıyıcısı ile bağlanır.

* ExpressRoute bağlantı hattı, Güney Doğu Asya sanal WAN hub 'ına Özel WAN bağlantıları üzerinden yerel olarak sonlandırılır.

* Sanal WAN hub 'dan hub 'a genel bağlantı trafik geçişi sağlar.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow3.png" alt-text="Flow 3":::

### <a name="path-4"></a>Yol 4

4. yol, Güney Doğu Asya bölgesindeki bir Azure VNet 'ten gelen trafik akışını Batı Avrupa bölgesindeki bir Azure VNet 'e gösterir.

Trafik şu şekilde yönlendirilir:

* Sanal WAN hub 'dan hub 'a genel bağlantı, bağlantılı tüm Azure sanal ağlarına ek kullanıcı yapılandırması olmadan yerel aktarım sağlar.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow4.png" alt-text="Flow 4":::

### <a name="path-5"></a>Yol 5

Yol 5, gezici VPN (P2S) kullanıcılarının Batı Avrupa bölgesindeki bir Azure VNet 'e trafik akışını gösterir.

Trafik şu şekilde yönlendirilir:

* Dizüstü ve mobil cihaz kullanıcıları, ' deki P2S VPN ağ Batı Avrupa geçidinde bulunan ' de saydam bağlantı için OpenVPN istemcisini kullanır.

* Sanal WAN hub Batı Avrupa trafiği bağlı VNet 'e yerel olarak yönlendirir.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow5.png" alt-text="Flow 5":::

## <a name="security-and-policy-control-via-azure-firewall"></a>Azure Güvenlik Duvarı aracılığıyla güvenlik ve ilke denetimi

Contoso artık, bu makalede daha önce bahsedilen gereksinimlere sahip tüm dallar ve sanal ağlar arasındaki bağlantıyı bir arada doğruladı. Güvenlik denetimi ve ağ yalıtımının gereksinimlerini karşılamak için, hub ağı üzerinden trafiği ayırmaya ve günlüğe kaydetmeye devam etmesi gerekir. Bu işlev daha önce bir ağ sanal gereci (NVA) tarafından gerçekleştirildi. Contoso Ayrıca, mevcut proxy hizmetlerini kullanımdan kaldırmak ve giden Internet filtreleme için yerel Azure hizmetlerini kullanmak istemektedir.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/security-policy.png" alt-text="Azure Güvenlik Duvarı aracılığıyla güvenlik ve ilke denetimi":::
**Şekil: sanal WAN 'da Azure Güvenlik Duvarı (güvenli sanal hub)**

Birleşik bir ilke denetimi noktasını etkinleştirmek üzere sanal WAN hub 'larına Azure Güvenlik Duvarı tanıtmak için aşağıdaki üst düzey adımlar gereklidir. Bu süreç ve güvenli sanal hub 'Lar kavramı hakkında daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Yöneticisi](../firewall-manager/index.yml).

1. Azure Güvenlik duvarı ilkesi oluşturun.
2. Güvenlik Duvarı ilkesini Azure sanal WAN hub 'ına bağlama. Bu adım, var olan sanal WAN hub 'ının güvenli bir sanal hub olarak çalışmasını sağlar ve gerekli Azure Güvenlik Duvarı kaynaklarını dağıtır.

> [!NOTE]
> Bölgeler arası trafik dahil olmak üzere, güvenli sanal hub 'ların kullanımıyla ilgili kısıtlamalar vardır. Daha fazla bilgi için bkz. [güvenlik duvarı yöneticisi-bilinen sorunlar](../firewall-manager/overview.md#known-issues).
>

Aşağıdaki yollarda, Azure güvenli sanal hub 'ları kullanılarak etkinleştirilen bağlantı yolları gösterilmektedir:

### <a name="path-6"></a>Yol 6

Yol 6 aynı bölgedeki VNET 'ler arasındaki güvenli trafik akışını gösterir.

Trafik şu şekilde yönlendirilir:

* Aynı güvenli sanal hub 'a bağlı sanal ağlar artık trafiği Azure Güvenlik Duvarı aracılığıyla yönlendirmektedir.

* Azure Güvenlik Duvarı, bu akışlara ilke uygulayabilir.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow6.png" alt-text="Flow 6":::

### <a name="path-7"></a>Yol 7

Yol 7, bir Azure VNet 'ten Internet 'e veya üçüncü taraf güvenlik hizmetine trafik akışını gösterir.

Trafik şu şekilde yönlendirilir:

* Güvenli sanal hub 'a bağlı sanal ağlar, internet 'teki merkezi bir Internet erişimi noktası olarak güvenli hub 'ı kullanarak herkese genel, hedeflere trafik gönderebilir.

* Bu trafik Azure Güvenlik Duvarı FQDN kuralları kullanılarak yerel olarak filtrelenebilir veya inceleme için bir üçüncü taraf güvenlik hizmetine gönderilebilir.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow7.png" alt-text="Flow 7":::

### <a name="path-8"></a>Yol 8

Yol 8 ' den Internete veya üçüncü taraf güvenlik hizmetinden trafik akışı gösterilmektedir.

Trafik şu şekilde yönlendirilir:

* Güvenli sanal hub 'a bağlı dallar, güvenli hub 'ı Internet erişimi merkezi olarak kullanarak Internet 'teki genel hedeflere trafik gönderebilir.

* Bu trafik Azure Güvenlik Duvarı FQDN kuralları kullanılarak yerel olarak filtrelenebilir veya inceleme için bir üçüncü taraf güvenlik hizmetine gönderilebilir.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow8.png" alt-text="Akış 8":::

## <a name="next-steps"></a>Sonraki adımlar

[Azure sanal WAN](virtual-wan-about.md)hakkında daha fazla bilgi edinin.
