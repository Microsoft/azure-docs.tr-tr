---
title: Azure 'da iç içe Traffic Manager profilleri
titlesuffix: Azure Traffic Manager
description: Bu makalede, Azure Traffic Manager ' Iç Içe profil ' özelliği açıklanmaktadır
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: allensu
ms.openlocfilehash: 8815d852ad9f8a1823e1c21cc2d233409518da33
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333797"
---
# <a name="nested-traffic-manager-profiles"></a>İç İçe Traffic Manager profilleri

Traffic Manager, her bir son kullanıcıdan gelen trafiği hangi uç noktanın alacağını Traffic Manager denetlemenize olanak tanıyan bir dizi trafik yönlendirme yöntemini içerir. Daha fazla bilgi için bkz. [Traffic Manager trafik-yönlendirme yöntemleri](traffic-manager-routing-methods.md).

Her Traffic Manager profili tek bir trafik yönlendirme yöntemini belirtir. Ancak, tek bir Traffic Manager profili tarafından sağlanmış olan yönlendirmeden daha karmaşık trafik yönlendirmesi gerektiren senaryolar vardır. Birden fazla trafik yönlendirme yönteminin avantajlarını birleştirmek için Traffic Manager profillerini iç içe geçirebilirsiniz. İç içe geçmiş profiller, daha büyük ve daha karmaşık uygulama dağıtımlarını desteklemek için varsayılan Traffic Manager davranışını geçersiz kılmanızı sağlar.

Aşağıdaki örneklerde, çeşitli senaryolarda iç içe geçmiş Traffic Manager profillerinin nasıl kullanılacağı gösterilmektedir.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Örnek 1: ' Performance ' ve ' ağırlıklı ' trafik yönlendirmeyi birleştirme

Aşağıdaki Azure bölgelerinde bir uygulama dağıttığınızı varsayalım: Batı ABD, Batı Avrupa ve Doğu Asya. Trafiği kullanıcıya en yakın bölgeye dağıtmak için Traffic Manager ' Performance ' trafik yönlendirme yöntemini kullanırsınız.

![Tek Traffic Manager profili][4]

Şimdi, daha kapsamlı bir şekilde kullanıma sunulmadan önce hizmetinize bir güncelleştirmeyi test etmek istediğinizi varsayalım. Test dağıtımınıza küçük bir trafik yüzdesini yönlendirmek için ' ağırlıklı ' trafik-yönlendirme yöntemini kullanmak istiyorsunuz. Test dağıtımını, Batı Avrupa içindeki mevcut üretim dağıtımıyla birlikte ayarlarsınız.

Tek bir profilde hem ' ağırlıklı ' hem de ' performans trafiği-yönlendirmeyi birleştiremezsiniz. Bu senaryoyu desteklemek için, iki Batı Avrupa uç noktasını ve ' ağırlıklı ' trafik-yönlendirme yöntemini kullanarak bir Traffic Manager profili oluşturursunuz. Sonra, bu ' alt ' profilini ' üst ' profiline bir uç nokta olarak eklersiniz. Üst profil hala performans trafiği-yönlendirme yöntemini kullanır ve diğer genel dağıtımları uç noktalar olarak içerir.

Aşağıdaki diyagramda Bu örnek gösterilmektedir:

![İç İçe Traffic Manager profilleri][2]

Bu yapılandırmada, üst profille yönlendirilen trafik, trafiği normal şekilde bölgeler arasında dağıtır. Batı Avrupa içinde, iç içe profil, atanan ağırlığa göre trafiği üretim ve test uç noktalarına dağıtır.

Üst profil ' Performance ' trafik-yönlendirme yöntemini kullandığında, her bir uç noktaya bir konum atanması gerekir. Uç noktasını yapılandırdığınızda konum atanır. Dağıtımınıza en yakın Azure bölgesini seçin. Azure bölgeleri, Internet gecikme süresi tablosu tarafından desteklenen konum değerleridir. Daha fazla bilgi için bkz. [' Performance ' trafik-yönlendirme yöntemi Traffic Manager](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Örnek 2: Iç Içe profillerde uç nokta izleme

Traffic Manager her bir hizmet uç noktasının durumunu etkin bir şekilde izler. Bir uç nokta sağlıksız ise, Traffic Manager hizmetinizin kullanılabilirliğini korumak için kullanıcıları alternatif uç noktalara yönlendirir. Bu uç nokta izleme ve yük devretme davranışı tüm trafik yönlendirme yöntemleri için geçerlidir. Daha fazla bilgi için bkz. [Traffic Manager uç nokta izleme](traffic-manager-monitoring.md). Uç nokta izleme, iç içe profiller için farklı şekilde çalışır. İç içe profiller sayesinde, üst profil doğrudan alt üzerinde sistem durumu denetimleri gerçekleştirmez. Bunun yerine, alt profilin genel durumunu hesaplamak için alt profilin uç noktalarının sistem durumu kullanılır. Bu durum bilgileri, iç içe geçmiş profil hiyerarşisinin yayılmıştır. Üst profil, trafiğin alt profile yönlendirip yönlendirmeyeceğini anlamak için bu toplanmış sistem durumunu kullanır. İç içe geçmiş profillerin sistem durumu izlemeyle ilgili ayrıntılı bilgi için [SSS](traffic-manager-FAQs.md#traffic-manager-nested-profiles) bölümüne bakın.

Önceki örneğe dönerek Batı Avrupa içindeki üretim dağıtımının başarısız olduğunu varsayalım. Varsayılan olarak, ' alt ' profili tüm trafiği test dağıtımına yönlendirir. Test dağıtımı da başarısız olursa, üst profil tüm alt uç noktaları sağlıksız olduğundan alt profilin trafik almamalı olduğunu belirler. Daha sonra, üst profil trafiği diğer bölgelere dağıtır.

![İç içe profil yük devretme (varsayılan davranış)][3]

Bu düzenleme ile memnun olabilirsiniz. Ya da Batı Avrupa için tüm trafiğin artık sınırlı bir alt küme trafiği yerine test dağıtımına gittiğinin endişeniz olabilir. Test dağıtımının sistem durumu ne olursa olsun, Batı Avrupa ' deki üretim dağıtımı başarısız olduğunda diğer bölgelere yük devretmek istersiniz. Bu yük devretmeyi etkinleştirmek için alt profili üst profilde bir uç nokta olarak yapılandırırken ' MinChildEndpoints ' parametresini belirtebilirsiniz. Parametresi, alt profildeki kullanılabilir uç noktaların minimum sayısını belirler. Varsayılan değer ' 1 '. Bu senaryo için MinChildEndpoints değerini 2 olarak ayarlarsınız. Bu eşiğin altında, üst profil tüm alt profilin kullanılamaz olduğunu varsayar ve trafiği diğer uç noktalara yönlendirir.

Aşağıdaki şekilde bu yapılandırma gösterilmektedir:

![' MinChildEndpoints ' ile iç içe profil yük devretmesi = 2][4]

> [!NOTE]
> ' Priority ' trafik-yönlendirme yöntemi tüm trafiği tek bir uç noktaya dağıtır. Bu nedenle, alt profil için ' 1 ' dışındaki bir MinChildEndpoints ayarında çok az amaç vardır.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Örnek 3: ' Performans ' trafiği yönlendirmesinde öncelikli yük devretme bölgeleri

' Performance ' trafik-yönlendirme yönteminin varsayılan davranışı, farklı coğrafi konumlarda uç noktalarınız olduğunda, son kullanıcıların en düşük ağ gecikmesi açısından "en yakın" uç noktaya yönlendirilmesidir.

Ancak, Batı ABD için Batı Avrupa trafiği yük devretmesini tercih ettiğinizi ve her iki uç nokta de kullanılamadığı zaman yalnızca diğer bölgelere doğrudan trafik yönlendirdiğini varsayalım. Bu çözümü, ' Priority ' trafik yönlendirme yöntemiyle bir alt profil kullanarak oluşturabilirsiniz.

![Tercihe bağlı yük devretme ile ' performans ' trafiği yönlendirme][6]

Batı Avrupa uç noktası Batı ABD uç noktasından daha yüksek önceliğe sahip olduğundan, her iki uç nokta de çevrimiçi olduğunda tüm trafik Batı Avrupa uç noktasına gönderilir. Batı Avrupa başarısız olursa, trafiği Batı ABD yönlendirilir. İç içe geçmiş profille, trafik yalnızca Batı Avrupa ve Batı ABD başarısız olduğunda Doğu Asya yönlendirilir.

Bu kalıbı tüm bölgeler için tekrarlayabilirsiniz. Üst profildeki üç bitiş noktasını, her biri öncelikli bir yük devretme sırası sağlayan üç alt profille değiştirin.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Örnek 4: Aynı bölgedeki birden fazla uç nokta arasında ' performans ' trafiği yönlendirmeyi denetleme

Belirli bir bölgede birden fazla uç noktaya sahip bir profilde ' Performance ' trafik-yönlendirme yönteminin kullanıldığını varsayın. Varsayılan olarak, bu bölgeye yöneltilen trafik bu bölgedeki tüm kullanılabilir uç noktalar arasında eşit olarak dağıtılır.

![' Performans ' trafik yönlendirme-bölge içi trafik dağıtımı (varsayılan davranış)][7]

Batı Avrupa birden fazla uç nokta eklemek yerine, bu uç noktalar ayrı bir alt profile alınır. Alt profil, Batı Avrupa ' deki tek uç nokta olarak üst öğeye eklenir. Alt profildeki ayarlar, bu bölge içinde öncelik tabanlı veya ağırlıklı trafik yönlendirmeyi etkinleştirerek Batı Avrupa ile trafik dağılımını denetleyebilir.

![Özel bölge içi trafik dağıtımı ile ' performans ' trafiği yönlendirme][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Örnek 5: Uç nokta başına izleme ayarları

Eski şirket içi bir Web sitesinden gelen trafiği Azure 'da barındırılan yeni bir bulut tabanlı sürüme sorunsuz bir şekilde geçirmek için Traffic Manager kullandığınızı varsayalım. Eski site için, site durumunu izlemek üzere giriş sayfası URI 'sini kullanmak istersiniz. Ancak bulut tabanlı yeni sürüm için, ek denetimler içeren özel bir izleme sayfası (yol '/Monitor.aspx ') uyguız.

![Uç nokta izleme Traffic Manager (varsayılan davranış)][9]

Bir Traffic Manager profilindeki izleme ayarları, tek bir profildeki tüm uç noktalar için geçerlidir. İç içe profiller sayesinde, farklı izleme ayarlarını tanımlamak için her site için farklı bir alt profil kullanırsınız.

![Uç nokta başına ayarlarla uç nokta izleme Traffic Manager][10]

## <a name="faqs"></a>SSS

* [İç içe geçmiş profilleri yapılandırmak Nasıl yaparım??](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#traffic-manager-endpoint-monitoring)

* [Trafik Yöneticisi kaç tane iç içe geçme katmansın destekler?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-layers-of-nesting-does-traffic-manger-support)

* [Aynı Traffic Manager profilinde, iç içe geçmiş alt profillerle diğer uç nokta türlerini karışık yapabilir miyim?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [Fatura modeli, Iç Içe geçmiş profiller için nasıl uygulanır?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-billing-model-apply-for-nested-profiles)

* [İç içe geçmiş profiller için bir performans etkisi var mı?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-performance-impact-for-nested-profiles)

* [Traffic Manager bir üst profilde iç içe geçmiş bir uç noktanın durumunu nasıl hesaplamıştır?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>Sonraki adımlar

[Traffic Manager profilleri](traffic-manager-overview.md) hakkında daha fazla bilgi edinin

[Traffic Manager profili oluşturmayı](traffic-manager-create-profile.md) öğrenin

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png
