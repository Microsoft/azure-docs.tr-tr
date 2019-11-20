---
title: Azure ExpressRoute ile Azure VM olağanüstü durum kurtarma Azure Site Recovery tümleştirme
description: Azure Site Recovery ve Azure ExpressRoute kullanarak Azure VM 'Leri için olağanüstü durum kurtarmanın nasıl ayarlanacağını açıklar.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: bf12a5b7850a56d945e1082be6c522c31738669c
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954094"
---
# <a name="integrate-expressroute-with-disaster-recovery-for-azure-vms"></a>Azure VM 'lerinde ExpressRoute 'ı olağanüstü durum kurtarma ile tümleştirme


Bu makalede, Azure sanal makineleri için ikincil bir Azure bölgesine olağanüstü durum kurtarmayı ayarlarken Azure ExpressRoute 'u [Azure Site Recovery](site-recovery-overview.md)ile tümleştirme açıklanır.

Site Recovery Azure VM verilerini Azure 'a çoğaltarak Azure VM 'Leri olağanüstü durumdan kurtarmaya olanak sağlar.

- Azure VM 'Leri [Azure yönetilen diskleri](../virtual-machines/windows/managed-disks-overview.md)kullanıyorsa, VM verileri ikincil bölgedeki çoğaltılan bir yönetilen diske çoğaltılır.
- Azure VM 'Leri yönetilen diskler kullanmamışsa, VM verileri bir Azure depolama hesabına çoğaltılır.
- Çoğaltma uç noktaları geneldir, ancak Azure VM 'Leri için çoğaltma trafiği internet üzerinden geçmez.

ExpressRoute, şirket içi ağları bir bağlantı sağlayıcısı tarafından kolaylaştırarak özel bir bağlantı üzerinden Microsoft Azure bulutuna genişletmenizi sağlar. ExpressRoute yapılandırdıysanız, Site Recovery aşağıdaki gibi tümleştirilir:

- **Azure bölgeleri arasında çoğaltma sırasında**: Azure VM olağanüstü durum kurtarma için çoğaltma trafiği yalnızca Azure 'da bulunur ve ExpressRoute gerekli değildir veya çoğaltma için kullanılmaz. Ancak, şirket içi bir siteden birincil Azure sitesindeki Azure VM 'lerine bağlanıyorsanız, bu Azure VM 'Leri için olağanüstü durum kurtarmayı ayarlarken dikkat etmeniz gerekenler bir dizi sorun vardır.
- **Azure bölgeleri arasında yük devretme**: kesintiler gerçekleştiğinde, birincil ve ikincil Azure bölgesindeki Azure VM 'lerinden yük devreolursunuz. İkincil bir bölgeye yük devrettikten sonra, ExpressRoute kullanarak ikincil bölgedeki Azure VM 'lerine erişmek için gereken birkaç adım vardır.


## <a name="before-you-begin"></a>Başlamadan önce

Başlamadan önce, aşağıdaki kavramları anladığınızdan emin olun:

- ExpressRoute [devreleri](../expressroute/expressroute-circuit-peerings.md)
- ExpressRoute [yönlendirme etki alanları](../expressroute/expressroute-circuit-peerings.md#routingdomains)
- ExpressRoute [konumları](../expressroute/expressroute-locations.md).
- Azure VM [çoğaltma mimarisi](azure-to-azure-architecture.md)
- Azure VM 'Leri için [çoğaltmayı ayarlama](azure-to-azure-tutorial-enable-replication.md) .
- Azure VM 'Leri yük [devretme](azure-to-azure-tutorial-failover-failback.md) .


## <a name="general-recommendations"></a>Genel öneriler

En iyi uygulama için ve olağanüstü durum kurtarma için etkili kurtarma süresi hedeflerini (RTOs) sağlamak üzere, ExpressRoute ile tümleştirilecek Site Recovery ayarlarken aşağıdakileri yapmanızı öneririz:

- İkincil bir bölgeye yük devretmeden önce ağ bileşenlerini sağla:
    - Azure VM 'Ler için çoğaltmayı etkinleştirdiğinizde Site Recovery, kaynak ağ ayarlarına bağlı olarak, hedef Azure bölgesindeki ağlar, alt ağlar ve ağ geçitleri gibi ağ kaynaklarını otomatik olarak dağıtabilir.
    - Site Recovery, VNet ağ geçitleri gibi ağ kaynaklarını otomatik olarak ayarlayamıyorum.
    - Yük devretmeden önce bu ek ağ kaynaklarını sağlamanızı öneririz. Bu dağıtımla ilişkili küçük bir kesinti olması ve dağıtım planlaması sırasında kendisi için hesap yapmadıysanız, genel kurtarma süresini etkileyebilir.
- Düzenli olağanüstü durum kurtarma detaylarını çalıştırın:
    - Tatbikat, çoğaltma stratejinizi veri kaybı veya kesinti süresi olmadan doğrular ve üretim ortamınızı etkilemez. RTO olumsuz yönde etkileyebilecek son dakikalık yapılandırma sorunlarından kaçınmaya yardımcı olur.
    - Detaya gitme için bir yük devretme testi çalıştırdığınızda, çoğaltmayı etkinleştirdiğinizde ayarlanmış olan varsayılan ağ yerine ayrı bir Azure VM ağı kullanmanızı öneririz.
- Tek bir ExpressRoute devreniz varsa farklı IP adresi alanları kullanın.
    - Hedef sanal ağ için farklı bir IP adresi alanı kullanmanızı öneririz. Bu, bölgesel kesintiler sırasında bağlantı kurarken sorunları önler.
    - Ayrı bir adres alanı kullanamıyoruz, olağanüstü durum kurtarma ayrıntısına yük devretmeyi farklı IP adresleriyle ayrı bir test ağında çalıştırdığınızdan emin olun. Çakışan IP adresi alanı ile iki sanal ağı aynı ExpressRoute devresine bağlanamazsınız.

## <a name="replicate-azure-vms-when-using-expressroute"></a>ExpressRoute kullanırken Azure VM 'lerini çoğaltma


Birincil sitede Azure VM 'Ler için çoğaltma ayarlamak istiyorsanız ve bu VM 'lere ExpressRoute üzerinden şirket içi sitenizden bağlanıyorsanız, yapmanız gerekenler şunlardır:

1. Her bir Azure VM için [çoğaltmayı etkinleştirin](azure-to-azure-tutorial-enable-replication.md) .
2. İsteğe bağlı olarak, ağ Site Recovery ayarlamış izin ver:
    - Çoğaltmayı yapılandırıp etkinleştirdiğinizde Site Recovery, hedef Azure bölgesindeki ağları, alt ağları ve ağ geçidi alt ağlarını kaynak bölgedeki olanlarla eşleşecek şekilde ayarlar. Site Recovery Ayrıca kaynak ve hedef sanal ağlar arasında da eşleşir.
    - Site Recovery bunu otomatik olarak yapmak istemiyorsanız, çoğaltmayı etkinleştirmeden önce hedef tarafı ağ kaynaklarını oluşturun.
3. Diğer ağ öğelerini oluşturun:
    - Site Recovery, rota tabloları, VNet ağ geçitleri, VNet ağ geçidi bağlantıları, VNet eşlemesi veya ikincil bölgedeki diğer ağ kaynakları ve bağlantılar oluşturmaz.
    - Bu ek ağ öğelerini, birincil bölgeden yük devretmeyi çalıştırmadan önce, ikincil bölgede oluşturmanız gerekir.
    - Bu ağ kaynaklarını ayarlamak ve bağlamak için [Kurtarma planlarını](site-recovery-create-recovery-plans.md) ve otomasyon betikleri ' ni kullanabilirsiniz.
1. Ağ trafiğinin akışını denetlemek için dağıtılan bir ağ sanal gereci (NVA) varsa Şunlara göz önünde olabilirsiniz:
    - Azure VM çoğaltma için Azure 'un varsayılan sistem rotası 0.0.0.0/0 ' dır.
    - Genellikle, NVA dağıtımları giden Internet trafiğini NVA üzerinden akışa zorlayan varsayılan bir yol (0.0.0.0/0) tanımlar. Varsayılan yol, başka bir belirli yol yapılandırması bulunamadığında kullanılır.
    - Bu durumda, tüm çoğaltma trafiği NVA üzerinden geçerse NVA aşırı yüklenmiş olabilir.
    - Ayrıca, tüm Azure VM trafiğini şirket içi dağıtımlara yönlendirmek için varsayılan rotalar kullanılırken de aynı sınırlama geçerlidir.
    - Bu senaryoda, çoğaltma trafiğinin Azure sınırından çıkmaması için, Microsoft. Storage hizmeti için sanal ağınızda [bir ağ hizmeti uç noktası oluşturmanızı](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) öneririz.

## <a name="replication-example"></a>Çoğaltma örneği

Genellikle kurumsal dağıtımlar, internet ve şirket içi sitelere harici bağlantı için merkezi bir bağlantı hub 'ı ile birden fazla Azure sanal ağı arasında bölünen iş yükleri sağlar. Hub ve bağlı bileşen topolojisi genellikle ExpressRoute ile birlikte kullanılır.

![Yük devretmeden önce ExpressRoute ile şirket içi-Azure arası](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Bölge**. Uygulamalar Azure Doğu Asya bölgesinde dağıtılır.
- **Bağlı ağ sanal**ağları. Uygulamalar iki bağlı ağ sanal ağlarına dağıtılır:
    - **Kaynak vNet1**: 10.1.0.0/24.
    - **Kaynak vNet2**: 10.2.0.0/24.
    - Her bir bağlı ağ sanal ağı **hub vNet**'e bağlanır.
- **Hub vNet**. Hub vNet **kaynak hub VNET**: 10.10.10.0/24.
  - Bu merkez sanal ağı ağ geçidi denetleyicisi gibi davranır.
  - Tüm alt ağlar arasındaki tüm iletişimler bu hub aracılığıyla gider.
    - **Hub vNet alt ağları**. Hub vNet 'in iki alt ağı vardır:
    - **NVA alt ağı**: 10.10.10.0/25. Bu alt ağ bir NVA (10.10.10.10) içerir.
    - **Ağ geçidi alt ağı**: 10.10.10.128/25. Bu alt ağ, bir ExpressRoute bağlantısına bağlı bir ExpressRoute ağ geçidi içerir ve şirket içi siteye özel bir eşleme yönlendirme etki alanı aracılığıyla yönlendirir.
- Şirket içi veri merkezinde, bir ExpressRoute bağlantı hattı bağlantısı, bir iş ortağı kenarı, Hong Kong ' de bulunur.
- Tüm yönlendirme Azure yol tabloları (UDR) ile denetlenir.
- VNET 'ler veya şirket içi veri merkezine arasındaki tüm giden trafik NVA aracılığıyla yönlendirilir.

### <a name="hub-and-spoke-peering-settings"></a>Hub ve bağlı bileşen eşleme ayarları

#### <a name="spoke-to-hub"></a>Uçtan merkeze

**Yön** | **Ayar** | **State**
--- | --- | ---
Uçtan merkeze | Sanal ağ adresine izin ver | Etkin
Uçtan merkeze | İletilen trafiğe izin ver | Etkin
Uçtan merkeze | Ağ Geçidi aktarımına izin ver | Devre dışı
Uçtan merkeze | Ağ geçitlerini kaldır 'ı kullan | Etkin

 ![Hub eşleme yapılandırmasına bağlı](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Merkezden uca

**Yön** | **Ayar** | **State**
--- | --- | ---
Merkezden uca | Sanal ağ adresine izin ver | Etkin
Merkezden uca | İletilen trafiğe izin ver | Etkin
Merkezden uca | Ağ Geçidi aktarımına izin ver | Etkin
Merkezden uca | Ağ geçitlerini kaldır 'ı kullan | Devre dışı

 ![Hub 'dan bağlı bileşen eşleme yapılandırması](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Örnek adımlar

Örneğimizde, kaynak ağdaki Azure VM 'Leri için çoğaltmayı etkinleştirirken aşağıdakiler gerçekleşmelidir:

1. VM için [çoğaltmayı etkinleştirirsiniz](azure-to-azure-tutorial-enable-replication.md) .
2. Site Recovery, hedef bölgede çoğaltma sanal ağları, alt ağlar ve ağ geçidi alt ağları oluşturacak.
3. Site Recovery, kaynak ağlar ve oluşturduğu çoğaltma hedef ağları arasında eşlemeler oluşturur.
4. Sanal ağ geçitleri, sanal ağ geçidi bağlantıları, sanal ağ eşlemesi veya diğer ağ kaynaklarını ya da bağlantıları el ile oluşturursunuz.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>ExpressRoute kullanılırken Azure VM 'lerinden yük devretme

Site Recovery kullanarak Azure VM 'Leri hedef Azure bölgesine devretmek için, ExpressRoute [özel eşlemesini](../expressroute/expressroute-circuit-peerings.md#privatepeering)kullanarak bunlara erişebilirsiniz.

- ExpressRoute 'ı hedef vNet 'e yeni bir bağlantıyla bağlamanız gerekir. Mevcut ExpressRoute bağlantısı otomatik olarak aktarılmaz.
- ExpressRoute bağlantınızı hedef vNet 'e ayarlama yöntemi ExpressRoute topolojinize göre değişir.


### <a name="access-with-two-circuits"></a>İki devrele erişin

#### <a name="two-circuits-with-two-peering-locations"></a>İki eşleme konumuna sahip iki bağlantı

Bu yapılandırma, ExpressRoute bağlantı hatlarına karşı koruma sağlamaya yardımcı olur. Birincil eşleme konumunuz kapalıysa, bağlantılar diğer konumdan devam edebilir.

- Üretim ortamına bağlı bağlantı hattı genellikle birincildir. İkincil devre genellikle daha düşük bant genişliğine sahiptir ve bu, bir olağanüstü durum oluşursa artırılabilir.
- Yük devretmeden sonra, ikincil ExpressRoute bağlantı hattından hedef vNet 'e bağlantılar kurabilirsiniz. Alternatif olarak, genel kurtarma süresini azaltmak için, olağanüstü bir durum oluşması durumunda bağlantıların ayarlanmış ve çalışır durumda olmasını sağlayabilirsiniz.
- Hem birincil hem de hedef VNET 'lere eşzamanlı bağlantılarla, şirket içi yönlendirmelerinizin yalnızca yük devretmeden sonra ikincil bağlantı hattını ve bağlantıyı kullandığından emin olun.
- Kaynak ve hedef sanal ağlar, yük devretmeden sonra yeni IP adresleri alabilir veya aynı olanları koruyabilir. Her iki durumda da, yük devretmeden önce ikincil bağlantılar kurulabilir.


#### <a name="two-circuits-with-single-peering-location"></a>Tek eşleme konumuna sahip iki bağlantı

Bu yapılandırma, birincil ExpressRoute bağlantı hattı arızalarına karşı korunmaya yardımcı olur, ancak tek ExpressRoute eşleme konumu devre dışı kaldığında, her iki devreyi da etkilemez.

- Şirket içi veri merkezinden, birincil bağlantı hattı ile kaynak vNEt 'e ve ikincil devrenle hedef vNet 'e eş zamanlı bağlantılar ekleyebilirsiniz.
- Birincil ve hedef için eşzamanlı bağlantı ile şirket içi yönlendirmenin yalnızca, yük devretmeden sonra ikincil devre ve bağlantı kullandığından emin olun.
-   Aynı eşleme konumunda devreler oluşturulduğunda her iki devreleri aynı vNet 'e bağlanamazsınız.

### <a name="access-with-a-single-circuit"></a>Tek bir devreye sahip erişim

Bu yapılandırmada yalnızca bir ExpressRoute devresi vardır. Devre dışı bırakılmış olması durumunda bağlantı hattı yedekli bir bağlantıya sahip olsa da, eşleme bölgeniz kapalıysa tek bir rota devresi esnekliği sağlamacaktır. Aşağıdakilere dikkat edin:

- Azure VM 'lerini [aynı coğrafi konumdaki](azure-to-azure-support-matrix.md#region-support)herhangi bir Azure bölgesine çoğaltabilirsiniz. Hedef Azure bölgesi kaynakla aynı konumda değilse, tek bir ExpressRoute bağlantı hattı kullanıyorsanız ExpressRoute Premium 'u etkinleştirmeniz gerekir. [ExpressRoute konumları](../expressroute/expressroute-locations.md) ve [ExpressRoute fiyatlandırması](https://azure.microsoft.com/pricing/details/expressroute/)hakkında bilgi edinin.
- Hedef bölgede aynı IP adresi alanı kullanılıyorsa, kaynak ve hedef VNET 'leri aynı anda devre dışı olarak bağlanamazsınız. Bu senaryoda:    
    -  Kaynak tarafı bağlantısını kesin ve ardından hedef tarafı bağlantısını oluşturun. Bu bağlantı değişikliği, Site Recovery kurtarma planının bir parçası olarak başlatılabilir. Aşağıdakilere dikkat edin:
        - Bölgesel bir hatada, birincil bölgeye erişilemezse, bağlantı kesme işlemi başarısız olabilir. Bu, hedef bölgeye bağlantı oluşturmayı etkileyebilir.
        - Bağlantıyı hedef bölgede oluşturduysanız ve birincil bölge daha sonra kurtarılırsa, iki eşzamanlı bağlantı aynı adres alanına bağlanmayı denerseniz paket bırakmaları yaşayabilirsiniz.
        - Bunu engellemek için, birincil bağlantıyı hemen sonlandırın.
        - VM birincil bölgeye yeniden çalıştıktan sonra, ikincil bağlantının bağlantısını kestikten sonra birincil bağlantı yeniden kurulabilir.
-   Hedef sanal ağda farklı bir adres alanı kullanılıyorsa aynı ExpressRoute bağlantı hattından kaynak ve hedef sanal ağlara aynı anda bağlanabilirsiniz.


## <a name="failover-example"></a>Yük devretme örneği

Örneğimizde aşağıdaki topolojiyi kullanıyoruz:

- İki farklı eşleme konumunda iki farklı ExpressRoute devreleri.
- Yük devretmeden sonra Azure VM 'Leri için özel IP adreslerini koruyun.
- Hedef kurtarma bölgesi Azure Güneydoğu Asya ' dır.
- Bir ikincil ExpressRoute bağlantı hattı bağlantısı, Singapur içindeki bir iş ortağı kenarıyla oluşturulur.

Yük devretmeden sonra aynı IP adresine sahip tek bir ExpressRoute bağlantı hattı kullanan basit bir topoloji için [Bu makaleyi gözden geçirin](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover).

### <a name="example-steps"></a>Örnek adımlar
Bu örnekte kurtarmayı otomatikleştirmek için yapmanız gerekenler şunlardır:

1. Çoğaltmayı ayarlamak için adımları izleyin.
2. Yük devretme sırasında veya sonrasında bu ek adımlarla [Azure VM 'Lerinin yükünü devreder](azure-to-azure-tutorial-failover-failback.md).

    a. Hedef bölge hub VNet 'te Azure ExpressRoute ağ geçidini oluşturun. Bu, hedef hub vNet 'i ExpressRoute devresine bağlamanız gerekir.

    b. Hedef hub vNet 'ten hedef ExpressRoute devresine bağlantı oluşturun.

    c. Hedef bölgenin hub 'ı ve bağlı bileşen sanal ağları arasındaki VNet eşayarlarını ayarlayın. Hedef bölgedeki eşleme özellikleri, kaynak bölgeyle aynı olacaktır.

    d. Hub VNet 'te UDRs 'yi ve iki bağlı ağ VNET 'i ayarlayın.

    - Hedef tarafı UDRs 'nin özellikleri, aynı IP adreslerini kullanırken kaynak taraftaki olanlarla aynıdır.
    - Farklı hedef IP adresleriyle UDRs 'nin uygun şekilde değiştirilmesi gerekir.


Yukarıdaki adımlar, [Kurtarma planının](site-recovery-create-recovery-plans.md)bir parçası olarak komut dosyası oluşturulabilir. Uygulama bağlantısı ve kurtarma süresi gereksinimlerine bağlı olarak yukarıdaki adımlar, yük devretmeye başlamadan önce da tamamlanabilir.

#### <a name="after-recovery"></a>Kurtarma sonrasında

VM 'Leri kurtardıktan ve bağlantıyı tamamladıktan sonra kurtarma ortamı aşağıdaki gibidir.

![Yük devretmeden sonra ExpressRoute ile şirket içi-Azure arası](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Sonraki adımlar

Uygulama yük devretmesini otomatikleştirmek için [Kurtarma planlarını](site-recovery-create-recovery-plans.md) kullanma hakkında daha fazla bilgi edinin.
