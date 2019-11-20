---
title: 'Azure ExpressRoute: yüksek kullanılabilirlik için tasarlama'
description: Bu sayfa, Azure ExpressRoute kullanılırken yüksek kullanılabilirlik için mimari öneriler sağlar.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4c3c6ae5fbdd91e6e44438be7fef2a3a91564a34
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076674"
---
# <a name="designing-for-high-availability-with-expressroute"></a>ExpressRoute ile yüksek kullanılabilirlik için tasarlama

ExpressRoute, Microsoft kaynaklarına yönelik taşıyıcı sınıfı özel ağ bağlantısı sağlamak için yüksek kullanılabilirlik için tasarlanmıştır. Diğer bir deyişle, Microsoft ağı içindeki ExpressRoute yolunda tek bir hata noktası yoktur. Kullanılabilirliği en üst düzeye çıkarmak için, ExpressRoute devrenizin müşterisi ve hizmet sağlayıcı segmenti de yüksek kullanılabilirlik için de tasarlanmıştır. Bu makalede, ilk olarak bir ExpressRoute kullanarak sağlam ağ bağlantısı oluşturmaya yönelik ağ mimarisi konularına bakalım, ardından ExpressRoute bağlantı hattının yüksek oranda kullanılabilirliğini geliştirmeye yardımcı olacak ince ayar özelliklerine bakalım.


## <a name="architecture-considerations"></a>Mimari konuları

Aşağıdaki şekilde, bir ExpressRoute bağlantı hattının kullanılabilirliğini en üst düzeye çıkarmak için bir ExpressRoute devresi kullanarak bağlanmak için önerilen yol gösterilmektedir.

 [![1]][1]

Yüksek kullanılabilirlik için, ExpressRoute bağlantı hattının, uçtan uca ağ genelinde yedekliliğe karşı korunması önemlidir. Diğer bir deyişle, şirket içi ağınızda yedekliliğe sahip olmanız ve hizmet sağlayıcı ağınız içindeki yedekliliğe güvenmemeniz gerekir. En düşük düzeyde yedekliliğe sahip olmak, tek bir ağ arızasından kaçınarak. Ağ cihazları için yedekli güç ve soğutma olması, yüksek kullanılabilirliği daha da iyileştirir.

### <a name="first-mile-physical-layer-design-considerations"></a>İlk mil fiziksel katman tasarımı konuları

 Hem bir ExpressRoute devresine ait birincil ve ikincil bağlantıyı aynı müşteri şirket Ekipmanı (CPE) üzerinde sonlandırabilirsiniz, şirket içi ağınızda yüksek kullanılabilirliğe sahip olursunuz. Ayrıca, hem birincil hem de ikincil bağlantıyı aynı bir CPE bağlantı noktası üzerinden yapılandırırsanız (farklı alt arabirimler altında iki bağlantıyı sonlandırarak ya da iş ortağı ağı içindeki iki bağlantıyı birleştirerek), iş ortağını zorlıyoruz Ayrıca, ağ kesimlerinde yüksek kullanılabilirlik sağlamak için. Bu uzlaşma aşağıdaki şekilde gösterilmiştir.

[![2]][2]

Öte yandan, bir ExpressRoute devresine ait birincil ve ikincil bağlantıları farklı coğrafi konumlarda sona erdirdiğinizde, bağlantının ağ performansını tehlikeye atmanız gerekebilir. Trafik, birincil ve farklı coğrafi konumlarda sonlandırılan ikincil bağlantılar arasında etkin bir şekilde yük dengelemesi yapıldıysa, iki yol arasındaki ağ gecikmesi üzerinde potansiyel büyük farklılık, ağın büyük bir sonucudur mının. 

Coğrafi olarak yedekli Tasarım konuları için bkz. [ExpressRoute ile olağanüstü durum kurtarma Için tasarlama][DR].

### <a name="active-active-connections"></a>Etkin-etkin bağlantılar

Microsoft ağı, ExpressRoute devrelerinin birincil ve ikincil bağlantılarını etkin-etkin modda çalışacak şekilde yapılandırılmıştır. Ancak, rota reklamlarınız aracılığıyla bir ExpressRoute devresine ait gereksiz bağlantıları aktif-pasif modda çalışacak şekilde zorlayabilirsiniz. Daha özel yollar ve BGP 'yi önceden bekleyen olarak bildirme, bir yolu diğeri üzerinde tercih etmek için kullanılan yaygın tekniklerdir.

Yüksek kullanılabilirliği artırmak için, bir ExpressRoute bağlantı hattının her ikisini de etkin-etkin modda çalıştırmak önerilir. Bağlantıların etkin-etkin modda çalışmasına izin verirseniz, Microsoft ağ trafiği her akış temelinde bağlantılar üzerinden dengeleyebilir.

Aktif-pasif modda bir ExpressRoute devresine ait birincil ve ikincil bağlantıları çalıştırmak, her iki bağlantının da etkin yoldaki bir hatayı takip eden riskini olumsuz yönde çalıştırmıyor. Üzerinde geçiş sırasında oluşan yaygın nedenler, pasif bağlantının etkin yönetiminin ve eski yolların pasif bağlantısının olmamasından kaynaklanır.

Alternatif olarak, bir ExpressRoute devresine ait birincil ve ikincil bağlantıları etkin-etkin modda çalıştırmak, bir ExpressRoute bağlantı hatasından sonra, akışların yalnızca yarısı ve yeniden yönlendirilme hakkında bilgi elde edin. Bu nedenle, etkin-etkin mod, kurtarma süresini (MTTR) önemli ölçüde artırmaya yardımcı olur.

### <a name="nat-for-microsoft-peering"></a>Microsoft eşlemesi için NAT 

Microsoft eşlemesi, genel uç noktaları arasındaki iletişim için tasarlanmıştır. Yaygın olarak, şirket içi özel uç noktalar, Microsoft eşlemesi üzerinden iletişim kurmadan önce müşteri veya iş ortağı ağı üzerinde genel IP ile çevrilmiş (NATed) ağ adresidir. Etkin-etkin modda hem birincil hem de ikincil bağlantıları kullandığınız varsayılarak, burada ve NAT, ExpressRoute bağlantılarından birindeki bir başarısızlığı izleyen ne kadar hızlı bir şekilde kurtardığınız konusunda ne kadar hızlı bir şekilde etkilenirsiniz. İki farklı NAT seçeneği aşağıdaki şekilde gösterilmiştir:

[![03]][3]

1\. seçenekte, ExpressRoute 'ın birincil ve ikincil bağlantıları arasında trafik bölünmeden sonra NAT uygulanır. NAT 'nin durum bilgisi olan gereksinimlerini karşılamak için, birincil ve ikincil cihazlar arasında bağımsız NAT havuzları kullanılır. böylece, dönüş trafiği akışın altında bulunduğu uç cihaza ulaşır.

2\. seçenekte, ExpressRoute 'ın birincil ve ikincil bağlantıları arasında trafiği bölmek için ortak bir NAT havuzu kullanılır. Trafiği bölmeden önce ortak NAT havuzunun fark edilmesi, yüksek kullanılabilirliğe güvendiğinden, tek bir hata noktası geldiğini ifade etmez.

1 seçeneğiyle, bir ExpressRoute bağlantı hatasından sonra karşılık gelen NAT havuzuna ulaşma özelliği bozulur. Bu nedenle, tüm bozuk akışların, karşılık gelen pencere zaman aşımından sonra TCP veya uygulama katmanı tarafından yeniden oluşturulması gerekir. Şirket içi sunuculardan herhangi birinin ön eki için NAT havuzlarından herhangi biri kullanılırsa ve ilgili bağlantı başarısız olursa, bağlantı düzeltilene kadar şirket içi sunuculara Azure 'dan ulaşılamaz.

2\. seçenek ile, NAT, birincil veya ikincil bağlantı hatasından sonra bile erişilebilir. Bu nedenle, ağ katmanının kendisi paketleri yeniden yönlendirebilir ve hatanın ardından daha hızlı kurtarmaya yardımcı olabilir. 

> [!NOTE]
> NAT seçeneği 1 (birincil ve ikincil ExpressRoute bağlantıları için bağımsız NAT havuzları) kullanıyorsanız ve bir IP adresi bağlantı noktasını NAT havuzundan bir şirket içi sunucuya eşlediğinizde, karşılık gelen bağlantı başarısız olur.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Özel eşleme için ince ayar özellikleri

Bu bölümde, ExpressRoute devrenizin yüksek oranda kullanılabilir hale getirmenize yardımcı olacak isteğe bağlı (Azure dağıtımınıza ve ne kadar gizli olduğuna bağlı olarak). Özellikle, ExpressRoute sanal ağ geçitleri ve çift yönlü Iletme algılaması (BFD) için bölgeye duyarlı dağıtımı gözden geçirelim.

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Kullanılabilirlik alanına duyarlı ExpressRoute sanal ağ geçitleri

Bir Azure bölgesindeki kullanılabilirlik bölgesi bir hata etki alanının ve bir güncelleştirme etki alanının birleşimidir. Bölgesel olarak yedekli Azure IaaS dağıtımını kabul ediyorsanız, ExpressRoute özel eşlemesini sonlandıran, bölgesel olarak yedekli sanal ağ geçitlerini da yapılandırmak isteyebilirsiniz. Daha fazla bilgi için bkz. [Azure kullanılabilirlik alanları bölgesel olarak yedekli sanal ağ geçitleri hakkında][zone redundant vgw]. Bölgesel olarak yedekli sanal ağ geçidini yapılandırmak için, bkz. [Azure kullanılabilirlik alanları bölge yedekli sanal ağ geçidi oluşturma][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Hata algılama süresini iyileştirme

ExpressRoute, özel eşleme üzerinde BFD 'yi destekler. BFD, Microsoft Enterprise Edge (MSEE) ile şirket içi ve BGP komşuları arasındaki, bir saniyeden kısa bir süredir 3 dakikadan (varsayılan) arasında katman 2 ağ üzerinden hatanın algılanma süresini azaltır. Hızlı hata algılama süresi hastening hata kurtarmaya yardımcı olur. Daha fazla bilgi için bkz. [ExpressRoute üzerinde BFD 'Yi yapılandırma][BFD].

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir ExpressRoute bağlantı hattı bağlantısının yüksek kullanılabilirlik için nasıl tasarlanacağını tartıştık. Bir ExpressRoute devre eşleme noktası, coğrafi bir konuma sabitlenmiştir ve bu nedenle tüm konumu etkileyen çok zararlı bir hatadan etkilenebilir. 

Microsoft omurgasına coğrafi olarak yedekli ağ bağlantısı oluşturmaya yönelik tasarım konuları için, tüm bölgeyi etkileyen çok zararlı hatalara yol açabilir, bkz. [ExpressRoute özel eşlemesi ile olağanüstü durum kurtarma Için tasarlama][DR].

<!--Image References-->
[]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "ExpressRoute kullanarak bağlanmak için önerilen 1 yol"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "en son mil bağlantıyı" etkinleştir
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT seçeneği"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




