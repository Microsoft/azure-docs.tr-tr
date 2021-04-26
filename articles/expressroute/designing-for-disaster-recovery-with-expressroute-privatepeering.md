---
title: 'Azure ExpressRoute: olağanüstü durum kurtarma için tasarlama'
description: Bu sayfa, Azure ExpressRoute kullanılırken olağanüstü durum kurtarma için mimari öneriler sağlar.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2021
ms.author: duau
ms.openlocfilehash: d0aa9e8bfd565eeb7599d52adc0ac5b854e750bb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937235"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>ExpressRoute özel eşlemesi ile olağanüstü durum kurtarma için tasarlama

ExpressRoute, Microsoft kaynaklarına yönelik taşıyıcı sınıfı özel ağ bağlantısı sağlamak için yüksek kullanılabilirlik için tasarlanmıştır. Diğer bir deyişle, Microsoft ağı içindeki ExpressRoute yolunda tek bir hata noktası yoktur. ExpressRoute devresinin kullanılabilirliğini en üst düzeye çıkarmak için tasarım konuları için bkz. [ExpressRoute ile yüksek kullanılabilirlik Için tasarlama][HA].

Bununla birlikte, Murphy 'in popüler AdAge 'yi almak *için bir şey yanlış gidecekse*, bu makalede tek bir ExpressRoute bağlantı hattı kullanılarak ele alınan hatalardan çok daha fazla geçen çözümlere odaklanalım. Coğrafi olarak yedekli ExpressRoute devreleri kullanarak olağanüstü durum kurtarma için sağlam arka uç ağ bağlantısı oluşturmaya yönelik ağ mimarisi konularına bakacağız.

>[!NOTE]
>Bu makalede açıklanan kavramlar, sanal WAN veya bunun dışında bir ExpressRoute bağlantı hattı oluşturulduğunda geçerlidir.
>

## <a name="need-for-redundant-connectivity-solution"></a>Yedekli bağlantı çözümü gerekiyor

Bölgesel bir hizmetin tamamının (Microsoft, ağ hizmeti sağlayıcıları, müşteri veya diğer bulut hizmeti sağlayıcılarının) düştüğü durumlar ve örnekler vardır. Bu bölgesel çapta hizmet etkisinin temel nedeni, doğal Calamity içerir. İşte bu nedenle, iş sürekliliği ve görev açısından kritik uygulamalar için olağanüstü durum kurtarma planlamanız önemlidir.   

Görev açısından kritik uygulamalarınızı bir Azure bölgesinde veya şirket içinde ya da başka bir yerde çalıştırıp çalıştırmadığına bakılmaksızın, yük devretme siteniz olarak başka bir Azure bölgesi kullanabilirsiniz. Aşağıdaki makalelerde uygulamalardan ve ön uç erişim perspektiflerinden olağanüstü durum kurtarma ele alınmaktadır:

- [Kurumsal ölçekte olağanüstü durum kurtarma][Enterprise DR]
- [Azure Site Recovery ile SMB olağanüstü durum kurtarma][SMB DR]

Şirket içi ağınız ile Microsoft iş açısından kritik işlemler için ExpressRoute bağlantısına sahipseniz, olağanüstü durum kurtarma planınız de coğrafi olarak yedekli ağ bağlantısı içermelidir. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Birden çok ExpressRoute devreleri kullanma sorunları

Birden fazla bağlantı kullanarak aynı ağ kümesini birbirine gönderdiğinizde, ağlar arasında paralel yollar ortaya çıkarabilir. Paralel yollar düzgün şekilde uygun olmadığında, asymmetrik yönlendirmeye yol açabilir. Yolda durum bilgisi olan varlıklara (örneğin NAT, güvenlik duvarı) sahipseniz, asymmetrik yönlendirme trafik akışını engelleyebilir.  Genellikle, ExpressRoute özel eşleme yolu üzerinden NAT veya güvenlik duvarları gibi durum bilgisi olan varlıkların üzerinden gelmezsiniz. Bu nedenle, ExpressRoute özel eşlemesi üzerinden asimetrik yönlendirmenin trafik akışını engellemesi gerekmez.
 
Bununla birlikte, coğrafi olarak yedekli paralel yollar arasında trafiği dengeleyebiliyorsanız, durum bilgisi olan varlıklara sahip olup olmamasına bakılmaksızın tutarsız ağ performansı yaşayabilirsiniz. Bu coğrafi olarak yedekli paralel yollar, [konuma göre sağlayıcılar](expressroute-locations-providers.md#partners) sayfasında bulunan aynı metro veya farklı metro ile olabilir. 

### <a name="same-metro"></a>Aynı metro

[Birçok Metros](expressroute-locations-providers.md#global-commercial-azure) Iki ExpressRoute konumu vardır. Örneğin, *Amsterdam* ve *Amsterdam2* bir örnektir. Artıklığı tasarlarken, aynı metro 'daki her iki konumdan Azure 'a iki paralel yol oluşturabilirsiniz. Bu tasarımın avantajı, uygulama yük devretmesinin ne zaman, şirket içi uygulamalarınız arasındaki uçtan uca gecikme süresinin ve Microsoft 'un yaklaşık olarak aynı kalmasını sağlar. Ancak, deprem gibi doğal bir olağanüstü durum varsa her iki yol için de bağlantı kullanılamaz.

### <a name="different-metros"></a>Farklı Metros

Artıklık için farklı Metros kullanırken, aynı [coğrafi politik bölgede](expressroute-locations-providers.md#locations)ikincil konumu seçmeniz gerekir. Coğrafi politik bölgenin dışında bir konum seçmek için, paralel yollarda her iki devrede Premium SKU kullanmanız gerekir. Bu yapılandırmanın avantajı doğal bir olağanüstü durum olma ihtimaline neden olur, ancak her iki bağlantı da kesintiye yol açabilir ancak gecikme süresi uçtan uca artar.

Bu makalede, coğrafi olarak yedekli yolları yapılandırırken karşılaşabileceğiniz sorunları nasıl ele vertiğimizden bahsedelim.

## <a name="small-to-medium-on-premises-network-considerations"></a>Küçük ve orta ölçekli şirket içi ağ konuları

Aşağıdaki diyagramda gösterilen örnek ağı ele alalım. Örnekte, bir Azure bölgesindeki contoso şirket içi konumu ve contoso VNet arasında coğrafi olarak yedekli ExpressRoute bağlantısı oluşturulur. Diyagramda, düz yeşil çizgi tercih edilen yolu (ExpressRoute 1 aracılığıyla) gösterir ve noktalı bir tek yolu (ExpressRoute 2 aracılığıyla) temsil eder.

[![1]][1]

Olağanüstü durum kurtarma için ExpressRoute bağlantısı tasarlarken şunları göz önünde bulundurmanız gerekir:

- coğrafi olarak yedekli ExpressRoute devreleri kullanma
- farklı ExpressRoute devresi için farklı hizmet sağlayıcı ağı kullanma
- [yüksek kullanılabilirlik][HA] Için ExpressRoute devresinin her birini tasarlama
- farklı ExpressRoute devresini müşteri ağında farklı bir yerde sonlandırma

Varsayılan olarak, yolları tüm ExpressRoute yollarında aynı şekilde tanıdıysanız, Azure, eşit maliyetli çoklu yol (ECMP) yönlendirmesi kullanarak tüm ExpressRoute yollarında şirket içi bağlı trafiği yük dengelemeye çalışır.

Ancak, coğrafi olarak yedekli ExpressRoute devreleri, farklı ağ yollarına sahip farklı ağ performanslarını (özellikle ağ gecikmesi için) dikkate almamız gerekir. Normal işlem sırasında daha tutarlı ağ performansı almak için, en düşük gecikme süresini sunan ExpressRoute devresini tercih etmek isteyebilirsiniz.

Aşağıdaki tekniklerden birini kullanarak bir ExpressRoute devresini tercih etmek üzere Azure 'u etkileyebilir (verimlilik sırasıyla listelenmiştir):

- diğer ExpressRoute bağlantı hatlarına kıyasla tercih edilen ExpressRoute bağlantı hattı üzerinden daha fazla özel yol duyuruyor
- sanal ağı tercih edilen ExpressRoute devresine bağlayan bağlantıda daha yüksek bağlantı ağırlığı yapılandırma
- Yol olarak daha uzun tercih edilen ExpressRoute bağlantı hattı üzerinden yolları bildirme (yol Prepend olarak)

### <a name="more-specific-route"></a>Daha belirgin yol

Aşağıdaki diyagramda, daha belirgin yol tanıtımı kullanılarak ExpressRoute yol seçiminin etkili bir şekilde kullanılması gösterilmektedir. Gösterilen örnekte, contoso şirket içi/24 IP aralığı, tercih edilen yol (ExpressRoute 1) aracılığıyla iki/25 adres aralığı ve tek yönlü yol (ExpressRoute 2) aracılığıyla/24 olarak tanıtıldığında.

[![iki]][2]

/25,/24 ile karşılaştırıldığında daha belirgin olduğundan, Azure, 10.1.11.0/24 ' e gidecek trafiği normal durumda ExpressRoute 1 üzerinden gönderir. ExpressRoute 1 bağlantılarının her ikisi de kapalıysa, VNet 10.1.11.0/24 yol tanıtımını yalnızca ExpressRoute 2 aracılığıyla görebilir; Bu nedenle, bu hata durumunda bekleme devresi kullanılır.

### <a name="connection-weight"></a>Bağlantı ağırlığı

Aşağıdaki ekran görüntüsünde, Azure portal aracılığıyla bir ExpressRoute bağlantısının ağırlığını yapılandırma gösterilmektedir.

[![03]][3]

Aşağıdaki diyagramda bağlantı ağırlığı kullanılarak ExpressRoute yol seçiminin etkili bir şekilde kullanılması gösterilmektedir. Varsayılan bağlantı ağırlığı 0 ' dır. Aşağıdaki örnekte, ExpressRoute 1 bağlantısının ağırlığı 100 olarak yapılandırılır. VNet birden fazla ExpressRoute bağlantı hattı üzerinden tanıtılan bir rota önekini aldığında, VNet en yüksek ağırlığa sahip bağlantıyı tercih eder.

[![4]][4]

ExpressRoute 1 bağlantılarının her ikisi de kapalıysa, VNet 10.1.11.0/24 yol tanıtımını yalnızca ExpressRoute 2 aracılığıyla görebilir; Bu nedenle, bu hata durumunda bekleme devresi kullanılır.

### <a name="as-path-prepend"></a>AS yolu önüne

Aşağıdaki diyagramda yol Prepend 'i kullanarak ExpressRoute yol seçiminin etkili bir şekılde kullanılması gösterilmektedir. Diyagramda, ExpressRoute 1 üzerinden yönlendirme tanıtımı, eBGP 'nin varsayılan davranışını gösterir. ExpressRoute 2 üzerinden rota tanıtımına, şirket içi ağın ASN 'si de yolun AS yolunda ek olarak sona erer. Aynı yol birden çok ExpressRoute bağlantı hattı aracılığıyla alındığında, eBGP yol seçimi işlemi başına, VNet en kısa AS yolunu içeren yolu tercih eder. 

[![e]][5]

ExpressRoute 1 bağlantılarının her ikisi de kapalıysa, VNet 10.1.11.0/24 yol tanıtımını yalnızca ExpressRoute 2 aracılığıyla görebilir. Yani, daha uzun olan yol, ilgisiz hale gelir. Bu nedenle, bekleme devresi bu hata durumunda kullanılır.

Azure 'u başka bir şekilde kullanarak ExpressRoute 'tan birini tercih edebilirsiniz. Ayrıca, asimetrik akışlardan kaçınmak için şirket içi ağın aynı ExpressRoute yolunu da tercih etmeniz gerekir. Genellikle, yerel tercih değeri, şirket içi ağı diğer bir ExpressRoute devresini tercih etmek üzere etkilemek için kullanılır. Yerel tercih bir iç BGP (iBGP) ölçümdür. En yüksek yerel tercih değerine sahip BGP rotası tercih edilir.

> [!IMPORTANT]
> Belirli ExpressRoute devrelerini tek başına kullandığınızda, bunları etkin bir şekilde yönetmeniz ve düzenli aralıklarla yük devretme işlemini test etmeniz gerekir. 
> 

## <a name="large-distributed-enterprise-network"></a>Büyük dağıtılmış kurumsal ağ

Büyük bir dağıtılmış kurumsal ağınız olduğunda, muhtemelen birden fazla ExpressRoute devreniz vardır. Bu bölümde, etkin-etkin ExpressRoute devreleri kullanarak, başka bir bağımsız devrelere gerek duymadan olağanüstü durum kurtarma tasarlamayı görelim. 

Aşağıdaki diyagramda gösterilen örneği ele alalım. Örnekte, contoso iki farklı eşleme konumunda ExpressRoute devreleri aracılığıyla iki farklı Azure bölgesinde iki adet contoso IaaS dağıtımına bağlı iki şirket içi konum vardır. 

[![inç]][6]

Olağanüstü durum kurtarma 'nın nasıl mimarilerimiz, çapraz-bölgesel konuma (region1/Region2 to Location2/location1) trafiğinin nasıl yönlendirildiğini etkiler. Çapraz bölge konumu trafiğini farklı yollarla yönlendiren iki farklı olağanüstü durum mimarilerine göz atalım.

### <a name="scenario-1"></a>1\. Senaryo

İlk senaryoda, bir Azure bölgesi ve şirket içi ağ arasındaki tüm trafiğin, sabit durumdaki yerel ExpressRoute bağlantı hattı üzerinden akışı gibi olağanüstü durum kurtarma tasarlayalim. Yerel ExpressRoute bağlantı hattı başarısız olursa, Azure ile şirket içi ağ arasındaki tüm trafik akışları için uzak ExpressRoute devresi kullanılır.

Aşağıdaki diyagramda Senaryo 1 gösterilmektedir. Diyagramda yeşil çizgiler, VNet1 ve şirket içi ağlar arasındaki trafik akışı için yolları gösterir. Mavi çizgiler, VNet2 ve şirket içi ağlar arasındaki trafik akışı için yolları gösterir. Kesintisiz satırlarda, istenen yol kararlı durumda ve kesik çizgili satırlarda, sabit durum trafik akışını taşıyan ilgili ExpressRoute bağlantı hattının başarısız olduğu trafik yolunu gösterir. 

[![7@@]][7]

Şirket içi ağa bağlı trafik için yerel eşleme konumu ExpressRoute 'a bağlantıyı tercih etmek üzere sanal ağları etkilemek için bağlantı ağırlığı kullanarak senaryoyu mimARDA dağıtabilirsiniz. Çözümü tamamlayabilmeniz için, simetrik trafik akışının tersine çevrilmiş olduğundan emin olmanız gerekir. Bir ExpressRoute devresini tercih etmek için BGP yönlendiricileriniz (Şirket içi tarafında ExpressRoute devrelerinin sonlandırıldığı) arasında IGP oturumunda yerel tercihi kullanabilirsiniz. Çözüm aşağıdaki diyagramda gösterilmiştir. 

[![8]][8]

### <a name="scenario-2"></a>2\. Senaryo

Senaryo 2 aşağıdaki diyagramda gösterilmiştir. Diyagramda yeşil çizgiler, VNet1 ve şirket içi ağlar arasındaki trafik akışı için yolları gösterir. Mavi çizgiler, VNet2 ve şirket içi ağlar arasındaki trafik akışı için yolları gösterir. Düzenli durumda (diyagramdaki düz satırlarda), sanal ağlar ve şirket içi konumlar arasındaki tüm trafik, en bölüm için Microsoft omurga aracılığıyla akar ve şirket içi konumlar arasındaki bağlantı, yalnızca hata durumunda (diyagramdaki noktalı çizgiler) bir ExpressRoute üzerinden akar.

[![tuşlarına]][9]

Çözüm aşağıdaki diyagramda gösterilmiştir. Gösterildiği gibi, VNET yol seçimini etkilemek için, daha belirgin yol (seçenek 1) veya as-Path önüne (seçenek 2) kullanarak senaryoyu mimARDA dağıtabilirsiniz. Azure bağlı trafiği için şirket içi ağ yolu seçimini etkilemek için, şirket içi konum arasındaki iç yönü daha az tercih edilen şekilde yapılandırmanız gerekir. Bağlı olan bağlantıyı, şirket içi ağ içinde kullanılan yönlendirme protokolüne bağlı olarak tercih ettiğiniz şekilde yapılandırırsınız. Yerel tercihi IOP veya ölçümle (OSPF veya SIS) ile birlikte kullanabilirsiniz.

[![(]][10]


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir ExpressRoute devresi özel eşleme bağlantısının olağanüstü durum kurtarma için nasıl tasarlanacağını tartıştık. Aşağıdaki makalelerde uygulamalardan ve ön uç erişim perspektiflerinden olağanüstü durum kurtarma ele alınmaktadır:

- [Kurumsal ölçekte olağanüstü durum kurtarma][Enterprise DR]
- [Azure Site Recovery ile SMB olağanüstü durum kurtarma][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "küçük ila orta büyüklükteki şirket içi ağ konuları"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "daha belirgin yollar kullanarak yol seçimini etkili bir şekilde seçme"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "Azure Portal aracılığıyla bağlantı ağırlığını yapılandırma"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "bağlantı ağırlığı kullanarak yol seçimini seçme"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "yol sonuna kadar kullanılan yol seçimini seçme"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "büyük dağıtılan şirket içi ağ konuları"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "Senaryo 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "etkin-etkin ExpressRoute bağlantı hattı çözümü 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "Senaryo 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "etkin-etkin ExpressRoute devre çözümü 2"

<!--Link References-->
[HA]: ./designing-for-high-availability-with-expressroute.md
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: ./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: ./expressroute-optimize-routing.md#solution-use-as-path-prepending
