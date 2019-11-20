---
title: Azure sanal WAN 'a geçiş
description: Azure sanal WAN 'a geçiş hakkında bilgi edinin.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 584e1c7da70dd7b22969d8d19967fbbe2c52075f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502124"
---
# <a name="migrate-to-azure-virtual-wan"></a>Azure sanal WAN 'a geçiş
Azure sanal WAN, şirketlerin küresel bağlantılarını basitleşmesini ve Microsoft Global ağı ölçeğinden faydalamalarını sağlar. Bu Teknik İnceleme, mevcut bir müşteri tarafından yönetilen hub ve bağlı olmayan topolojiden Microsoft tarafından yönetilen sanal WAN hub 'larını kullanan bir tasarıma geçiş isteyen şirketler için teknik ayrıntılar sağlar.

[Küresel aktarım ağı mimarisi ve sanal WAN](virtual-wan-global-transit-network-architecture.md) makalesi, bulut merkezli modern kurumsal küresel bir ağı benimsemekte olan kuruluşlar Için Azure sanal WAN 'ın sağladığı avantajları vurgular.

![hub ve bağlı bileşen](./media/migrate-from-hub-spoke-topology/figure1.png)
**Şekil 1: Azure sanal WAN**

Azure sanal veri merkezi (VDC) hub ve-ışınsal bağlantısı modeli, müşterilerimizin 1000 ' inden yararlanarak basit ve ölçeklenebilir bulut ağları oluşturmak için Azure ağ 'ın varsayılan geçişli yönlendirme davranışından yararlanmasını sağlar. Azure sanal WAN, bu kavramlarda yer alan ve yalnızca şirket içi konumlar ve Azure arasında değil, genel bağlantı Topolojilerine izin veren yeni yetenekler sunar, ancak müşterilerin Microsoft ağ ölçeğinden yararlanmasını sağlar. mevcut küresel ağlar.

Bu makalede, mevcut bir karma ortamın sanal WAN 'a nasıl geçirileceği açıklanır.

## <a name="scenario"></a>Senaryo

Contoso, Avrupa ve Asya 'daki ofislerle küresel bir mali kuruluştur. Bunlar, içindeki şirket içi etki alanı denetleyiciden Azure 'a taşımak için planlarlar ve karma bağlantı için bölgesel müşteri tarafından yönetilen hub sanal ağları dahil olmak üzere VDC mimarisine dayalı bir temel tasarım sağlar. Bulut tabanlı teknolojiden taşıma kapsamında, ağ ekibi, bağlantısı olan iş için iyileştirildiğinden emin olmak üzere kaldırılmıştır.

Şekil 2 ' de, mevcut global ağın, birden çok Azure bölgesine bağlantı dahil olmak üzere üst düzey bir görünümü gösterilmektedir.

![contoso var olan ağ topolojisi](./media/migrate-from-hub-spoke-topology/figure2.png)
**Şekil 2: contoso var olan ağ topolojisi**

Mevcut ağ topolojisinden aşağıdaki noktaları anlamış olabilir:
 
- Birden çok bölgede kullanılan merkez ve bağlı bileşen modeli. Ortak bir özel WAN 'a geri bağlantı için ExpressRoute Premium devrelerinin kullanılmasıyla.
- Bu sitelerden bazılarının, Microsoft bulutunda barındırılan uygulamalara ulaşmak için doğrudan Azure 'da VPN tünellerini de vardır.

## <a name="requirements"></a>Gereksinimler
Ağ ekibi, contoso geçişini destekleyebilen ve maliyet, ölçek ve performans alanlarında en uygun hale getirebilen bir genel ağ modeli sunmaya eklenmiştir. Özet bölümünde aşağıdaki gereksinimlerin karşılanması gerekir:
- Bulut barındırılan uygulamalar için iyileştirilmiş yola sahip hem baş çeyrek (HQ) hem de şube ofislerini sağlayın. 
- Aşağıdaki bağlantı yollarını korurken VPN sonlandırma için mevcut şirket içi etki alanı denetleyicilerine (DC) güvenilmesini kaldırın:
    - **Daldan VNET**: VPN bağlantılı ofislerde, yerel Azure bölgesindeki buluta geçirilen uygulamalara erişim sağlayabilmelidir.
    - **Daldan hub 'Dan VNET**'e: VPN bağlantılı ofislerde, uzak Azure bölgesindeki buluta geçirilen uygulamalara erişim sağlayabilmelidir. 
    - **Daldan dala**: bölgesel VPN bağlı ofisleri, birbirleriyle ve ExpressRoute bağlantılı HQ/DC siteleriyle iletişim kurabilmelidir. 
    - **Daldan hub-hub**arası: genel olarak ayrılmış VPN bağlı ofisleri birbirleriyle ve tüm ExpressRoute ile bağlantılı HQ/DC siteleriyle iletişim kurabilmelidir.
    - **Daldan Internet**: bağlı sitelerin internet ile iletişim kurabilmesi gerekir ve bu trafiğin filtrelenmiş ve günlüğe kaydedilmiş olması gerekir.
    - **VNET-VNET**: aynı bölgedeki bağlı bileşen sanal ağlarının birbirleriyle iletişim kurabilmesi gerekir.
    - **VNET-hub**-hub-VNET: farklı bölgelerdeki bağlı olan sanal ağların birbirleriyle iletişim kurabilmesi gerekir.
- Contoso dolaşım kullanıcılarının (dizüstü ve telefon) şirket ağında değil şirket kaynaklarına erişmesine olanak sağlar.

## <a name="azure-virtual-wan-architecture"></a>Azure sanal WAN mimarisi

Şekil 3 ' te, önceki bölümde açıklanan gereksinimleri karşılamak için Azure sanal WAN kullanılarak güncelleştirilmiş hedef topolojinin yüksek düzey bir görünümü gösterilmektedir.

![contoso sanal WAN mimarisi](./media/migrate-from-hub-spoke-topology/figure3.png)
**Şekil 3: Azure sanal WAN mimarisi**

Özet: 
- Avrupa 'daki HQ ExpressRoute 'a bağlı, Avrupa Şirket içi DC, Azure 'a tamamen geçirilir ve kullanımdan çıkarıldı.
- Asya DC ve HQ, Özel WAN 'a bağlı kalır. Azure sanal WAN artık yerel taşıyıcı ağı artırmak ve genel bağlantı sağlamak için kullanıldı 
- ExpressRoute ve VPN bağlantılı cihazlara yönelik bağlantı hub 'ı sağlamak için hem Batı Avrupa hem de Güney Doğu Asya Azure bölgelerinde dağıtılan Azure sanal WAN hub 'Ları. 
- Hub 'lar aynı zamanda küresel ağ ağına yönelik OpenVPN bağlantısını kullanan birden çok istemci türündeki dolaşım kullanıcıları için VPN tarafı sağlar ve yalnızca Azure 'a geçirilen uygulamalara değil, şirket içinde kalan tüm kaynakları da erişime izin verir. 
- Azure sanal WAN tarafından sunulan bir sanal ağ içindeki kaynaklar için Internet bağlantısı. Azure sanal WAN tarafından sunulan uzak siteler için Internet bağlantısı. Office 365 gibi SaaS hizmetlerine iyileştirilmiş erişim için iş ortağı tümleştirmesiyle desteklenen yerel internet kırılımı.

## <a name="migrate-to-azure-virtual-wan"></a>Azure sanal WAN 'a geçiş

Bu bölümde, Azure sanal WAN 'a geçiş yapmak için çeşitli adımlar açıklanmaktadır.
 
### <a name="vdc-hub-and-spoke-single-region"></a>VDC hub ve-bağlı bileşen tek bölge

Aşağıdaki şekilde, Azure sanal WAN 'ın piyasaya sürülmesinden önce Contoso için tek bir bölge topolojisi gösterilmektedir.

![Sanal WAN hub 'larını dağıtma](./media/migrate-from-hub-spoke-topology/figure4.png)

 **Şekil 4: VDC hub ve-bağlı bileşen tek bölgesi – 1. adım**

Sanal veri merkezi (VDC) yaklaşımına sahip olan, müşteri tarafından yönetilen hub sanal ağı çeşitli işlev blokları içerir:
- Paylaşılan hizmetler (birden çok bağlı bileşen için gereken tüm ortak işlevleri), contoso 'nun kullandığı bir örnek, hizmet olarak altyapı (IaaS) sanal makinelerinde Windows Server etki alanı denetleyicileri.
- IP/yönlendirme güvenlik duvarı hizmetleri, bir üçüncü taraf ağ sanal gereci tarafından sağlanır ve bağlı bileşen 2. Katman 3 IP yönlendirmesi etkinleştiriliyor. 
- Gelen HTTPS istekleri için Azure Application Gateway ve internet kaynaklarına filtrelenmiş giden erişim için sanal makinelerde çalışan üçüncü taraf ara sunucu hizmetleri dahil Internet giriş/çıkış Hizmetleri.
- Şirket içi ağlara bağlantı için ExpressRoute ve VPN sanal ağ geçidi.

### <a name="deploy-virtual-wan-hubs"></a>Sanal WAN hub 'larını dağıtma

İlk adım, her bölgede bir sanal WAN hub 'ı dağıtmaya ilgilidir. Aşağıdaki makalelerde açıklandığı gibi, sanal WAN hub 'ını VPN Gateway ve Express Route Gateway ile dağıtın: 
- [Öğretici: Azure sanal WAN kullanarak siteden siteye bağlantı oluşturma](virtual-wan-site-to-site-portal.md)
- [Öğretici: Azure sanal WAN kullanarak bir ExpressRoute ilişkilendirmesi oluşturma](virtual-wan-expressroute-portal.md) 

> [!NOTE]
> Azure sanal WAN, bu makalede açıklanan bazı trafik yollarını etkinleştirmek için standart SKU 'YU kullanıyor olmalıdır.


Sanal WAN hub 'larını ![](./media/migrate-from-hub-spoke-topology/figure5.png)
**Şekil 5: VDC hub 'ı ve-sanal WAN geçişine bağlı bileşen-2. adım**

### <a name="connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Uzak siteleri (ExpressRoute ve VPN) sanal WAN 'a bağlama

Artık sanal WAN hub 'ını, Şirket içindeki uzak dallara ExpressRoute devrelerine bağlayıp siteden siteye VPN 'Leri kuruyor.

> [!NOTE]
> Express rotalar devrelerinin sanal WAN hub 'ına bağlanması için Premium SKU türüne yükseltilmesi gerekir.


uzak siteleri sanal WAN 'a bağlama ![](./media/migrate-from-hub-spoke-topology/figure6.png)
**Şekil 6: VDC hub 'ına ve-sanal WAN geçişine bağlı bileşen-3. adım**

Bu şirket içi ağ donatımı, sanal WAN ile yönetilen hub VNet 'e atanan IP adresi alanını yansıtan yollar almaya başlar. Uzak VPN bağlantılı dallar bu aşamada, bağlı olan sanal ağlardaki mevcut uygulamaların iki yolunu görürsünüz. Bu cihazlar, geçiş aşamasında simetrik yönlendirmeyi sağlamak için VDC hub 'ına tünel kullanmaya devam edecek şekilde yapılandırılmalıdır.

### <a name="test-hybrid-connectivity-via-virtual-wan"></a>Sanal WAN aracılığıyla karma bağlantıyı test etme

Üretim bağlantısı için yönetilen sanal WAN hub 'ını kullanmadan önce, bir test bağlı bileşen sanal ağını ve sanal WAN VNet bağlantısını ayarlamanız önerilir. Sonraki adımlarla devam etmeden önce bu test ortamına yapılan bağlantıların ExpressRoute ve siteden siteye VPN aracılığıyla çalıştığını doğrulayın.

sanal WAN](./media/migrate-from-hub-spoke-topology/figure7.png)
Şekil 7 aracılığıyla karma bağlantı ![test etme **: VDC hub 'ı ve-sanal WAN geçişine bağlı bileşen – 4. adım**

### <a name="transition-connectivity-to-virtual-wan-hub"></a>Sanal WAN hub 'ına geçiş bağlantısı


![sanal WAN hub 'ına geçiş bağlantısı](./media/migrate-from-hub-spoke-topology/figure8.png)
**Şekil 8: sanal WAN hub 'ına ve-sanal WAN geçişine bağlı-5. adım**

**a**. Mevcut eşleme bağlantılarını, bağlı olan sanal ağlardan eski VDC hub 'ına silin. A-c adımları tamamlanana kadar, bağlı olan sanal ağlardaki uygulamalara erişim kullanılamaz.

**b**. VNet bağlantıları üzerinden sanal WAN hub 'ına bağlı olan sanal ağları bağlayın.

**c**. Bağlı olan sanal ağlarda daha önce kullanılan Kullanıcı tanımlı yolları (UDR), bağlı bileşen iletişimleri için kaldırın. Bu yol artık sanal WAN hub 'ında bulunan dinamik yönlendirme tarafından etkinleştirilir.

**d**. VDC hub 'ındaki mevcut ExpressRoute ve VPN ağ geçitleri artık 5. adıma izin vermek için kullanımdan çıkarıldı.

**e**. Eski VDC hub 'ını (hub sanal ağı) yeni bir VNet bağlantısı aracılığıyla sanal WAN hub 'ına bağlayın.

### <a name="old-hub-becomes-shared-services-spoke"></a>Eski hub, paylaşılan hizmetlere bağlı olur

Artık Azure ağımızı, sanal WAN hub 'ını yeni topolojimize merkezi bir noktaya getirmek için yeniden tasarladık.

![eski hub, paylaşılan hizmetlere bağlı olur](./media/migrate-from-hub-spoke-topology/figure9.png)
**Şekil 9: VDC hub 'ı ve-sanal WAN geçişine bağlı bileşen-6. adım**

Sanal WAN hub 'ı yönetilen bir varlık olduğu ve sanal makineler gibi özel kaynakların dağıtımına izin vermediği için, paylaşılan hizmetler bloğu artık bağlı bir sanal ağ olarak bulunur ve Azure Application Gateway veya ağ üzerinden internet girişi gibi işlevleri barınıyor sanallaştırılmış gereç. Paylaşılan hizmetler ortamı ve arka uç sanal makineleri arasındaki trafik artık sanal WAN ile yönetilen hub 'ı geçişli.

### <a name="optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Sanal WAN 'ı tam olarak kullanmak için şirket içi bağlantıyı iyileştirin

Contoso, bu aşamada genellikle şirket içi DC içinde kalan bazı eski uygulamalarla Microsoft Bulut içinde iş uygulamalarının geçişlerini tamamlamıştır.

![sanal WAN](./media/migrate-from-hub-spoke-topology/figure10.png)
Şekil 10 ' a yönelik şirket içi bağlantıyı En Iyi hale getirmeyi **: VDC hub 'ı ve sanal WAN geçişine bağlı bileşen-adım 7**

 Contoso, Azure sanal WAN 'ın tüm işlevselliğinden yararlanmak için eski şirket içi VPN bağlantılarını kullanımdan çıkarmayı belirler. HQ veya DC ağlarına erişmeye devam eden dallar, Azure sanal WAN ' ın yerleşik aktarma yönlendirmesini kullanarak Microsoft Global ağını iletiyor. ExpressRoute Global Reach, mevcut özel WAN 'lerini tamamlamak için Microsoft omurga 'ten yararlanmak isteyen müşteriler için alternatif bir seçimdir.

## <a name="end-state-architecture-and-traffic-paths"></a>Son durum mimarisi ve trafik yolları


![uç durum mimarisi ve trafik yolları](./media/migrate-from-hub-spoke-topology/figure11.png)
**Şekil 11: Çift bölge sanal WAN**

Bu bölüm, bazı örnek trafik akışlarına bakarak bu topolojinin özgün gereksinimleri nasıl karşıladığına ilişkin bir Özet sağlar.

### <a name="path-1"></a>Yol 1

Yol 1, Asya S2S VPN dalından Güney Doğu Asya bölgesindeki Azure VNet 'e trafik akışını açıklar.

Trafik şu şekilde yönlendirilir:
- Asya dalı, esnek S2S BGP özellikli tüneller aracılığıyla Güney Doğu Asya sanal WAN hub 'ına bağlanır.
- Asya sanal WAN hub trafiği bağlı VNet 'e yerel olarak yönlendirir.

![Flow 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Yol 2
Yol 2, ExpressRoute bağlantılı Avrupa HQ ile Güney Doğu Asya bölgesindeki Azure VNet 'e giden trafik akışını açıklar.

Trafik şu şekilde yönlendirilir:
- Avrupa HQ, Batı Avrupa sanal WAN hub 'ına standart ExpressRoute bağlantı hattı üzerinden bağlanır.
- Sanal WAN hub 'dan hub 'a genel bağlantı, uzak bölgede bağlı olan VNet 'e kesintisiz trafik geçişi sağlar.

![Akış 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Yol 3
Yol 3, Özel WAN ile Avrupa S2S bağlı dalına bağlanan Asya şirket içi DC 'den trafik akışını açıklar.

Trafik şu şekilde yönlendirilir:
- Asya DC, yerel özel WAN taşıyıcısı ile bağlanır.
- ExpressRoute bağlantı hattı, Güney Doğu Asya sanal WAN hub 'ına Özel WAN bağlantıları üzerinden yerel olarak sonlandırılır.
- Sanal WAN hub 'dan hub 'a genel bağlantı, Avrupa 'daki uzak hub 'a bağlı trafik dalının sorunsuz bir şekilde aktarımına olanak sağlar.

![Flow 3](./media/migrate-from-hub-spoke-topology/flow3.png)


### <a name="path-4"></a>Yol 4
4\. yol, Güney Doğu Asya bölgesindeki Azure VNet 'ten Batı Avrupa bölgesindeki Azure VNet 'e trafik akışını açıklar.

Trafik şu şekilde yönlendirilir:
- Sanal WAN hub 'dan hub 'a genel bağlantı, bağlantılı tüm Azure sanal ağlarına ek kullanıcı yapılandırması olmadan yerel aktarım sağlar.

![Flow 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Yol 5
Yol 5, gezici VPN (P2S) kullanıcısının Batı Avrupa bölgesindeki Azure VNet 'e trafik akışını açıklar.

Trafik şu şekilde yönlendirilir:
- Dizüstü ve telefon kullanıcıları, Batı Avrupa 'de P2S VPN Gateway 'e saydam bağlantı için OpenVPN istemcisi kullanıyor.
- Sanal WAN hub Batı Avrupa trafiği bağlı VNet 'e yerel olarak yönlendirir.

![Flow 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Azure Güvenlik Duvarı aracılığıyla güvenlik ve ilke denetimi

Contoso artık, bu belgede daha önce bahsedilen gereksinimlere sahip tüm dallar ve sanal ağlar arasındaki bağlantıyı bir arada doğruladı. Güvenlik denetimi ve ağ yalıtımına yönelik gereksinimlerini karşılamak için, hub ağı üzerinden trafiği ayırmaya ve günlüğe kaydetmeye devam etmesi gerekir, bu işlev daha önce bir NVA tarafından gerçekleştirildi. Contoso Ayrıca, mevcut ara sunucu hizmetlerini kullanımdan kaldırmak ve giden Internet filtreleme için yerel Azure hizmetlerini kullanabilir. 

Azure Güvenlik Duvarı aracılığıyla güvenlik ve ilke denetimi ![](./media/migrate-from-hub-spoke-topology/figure12.png)
**Şekil 12: sanal WAN 'Da Azure Güvenlik Duvarı (güvenli sanal hub)**

Birleşik bir ilke denetimi noktasını etkinleştirmek üzere sanal WAN hub 'Larına Azure Güvenlik Duvarı tanıtmak için aşağıdaki üst düzey adımlar gereklidir. Bu süreç ve güvenli sanal hub 'Lar kavramı [burada](https://go.microsoft.com/fwlink/?linkid=2107683)tam ayrıntılı olarak açıklanmıştır.
- Azure Güvenlik duvarı ilkesi oluşturun.
- Güvenlik Duvarı ilkesini Azure sanal WAN hub 'ına bağlama.
Yukarıdaki adım, var olan sanal WAN hub 'ının güvenli bir sanal hub olarak çalışmasını sağlar ve gerekli Azure Güvenlik Duvarı kaynaklarını dağıtır.

> [!NOTE]
> Azure Güvenlik Duvarı standart bir sanal WAN hub 'ında (SKU: Standart) dağıtılmışsa: V2V, B2V, V2I ve B2I FW ilkeleri yalnızca Azure ILT 'ın dağıtıldığı belirli bir hub 'a bağlı olan sanal ağlar ve dallardan kaynaklanan trafiğe zorlanır (güvenli merkez). Aynı sanal WAN 'lara bağlı uzak VNET 'ler ve dallardan kaynaklanan trafik, bu uzak dallar ve VNet, hub bağlantılarına sanal WAN hub 'ı ile bağlı olsa da "güvenlik duvarı" olmaz. Platformlar arası güvenlik duvarı desteği, Azure sanal WAN ve güvenlik duvarı Yöneticisi yol haritasında bulunur.

Aşağıdaki yollar Azure güvenli sanal hub 'lardan yararlanarak etkinleştirilen bağlantı yollarını anlatmaktadır.

### <a name="path-6"></a>Yol 6
Yol 6 aynı bölgedeki VNet-VNet güvenli aktarım yolundan trafik akışını açıklar.

Trafik şu şekilde yönlendirilir:
- Aynı güvenli sanal hub 'a bağlı sanal ağlar artık trafiği Azure Güvenlik Duvarı aracılığıyla yönlendirmektedir.
- Azure Güvenlik Duvarı, bu akışlara ilke uygulayabilir.

![Flow 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Yol 7
Yol 7, VNET 'ten Internet 'e veya üçüncü taraf güvenlik hizmetinden trafik akışını açıklar.

Trafik şu şekilde yönlendirilir:
- Güvenli sanal hub 'a bağlı sanal ağlar, internet 'teki merkezi bir Internet erişimi noktası olarak güvenli hub 'ı kullanarak herkese genel, hedeflere trafik gönderebilir.
- Bu trafik, Azure Güvenlik Duvarı FQDN kuralları kullanılarak yerel olarak filtrelenebilir veya inceleme için bir üçüncü taraf güvenlik hizmetine gönderilebilir.

![Flow 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Yol 8
Yol 8, daldan Internet veya üçüncü taraf güvenlik hizmetinden trafik akışını açıklar.

Trafik şu şekilde yönlendirilir:
- Güvenli sanal hub 'a bağlı dallar, internet üzerindeki genel hedeflere trafik gönderebilir ve bu da merkezi bir Internet erişimi noktası olarak güvenli hub 'ı kullanabilir.
- Bu trafik, Azure Güvenlik Duvarı FQDN kuralları kullanılarak yerel olarak filtrelenebilir veya inceleme için bir üçüncü taraf güvenlik hizmetine gönderilebilir.

![Akış 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Sonraki adımlar
[Azure sanal WAN](virtual-wan-about.md) hakkında daha fazla bilgi
