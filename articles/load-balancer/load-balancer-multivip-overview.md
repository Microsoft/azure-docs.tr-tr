---
title: Birden çok ön uçlar-Azure Load Balancer
description: Bu öğrenme yoluyla, Azure Load Balancer çoklu ön uçlara genel bakış ile çalışmaya başlayın
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 58309133a46e32f409a0414be71791de73db9bed
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075943"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Azure Load Balancer için birden çok ön uç

Azure Load Balancer, birden fazla bağlantı noktası, birden çok IP adresi veya her ikisine birden Yük Dengeleme hizmeti sağlar. Ortak ve iç yük dengeleyici tanımlarını, bir VM kümesinde Yük Dengeleme akışları için kullanabilirsiniz.

Bu makalede, bu özellik, önemli kavramlar ve kısıtlamaların temelleri açıklanmaktadır. Yalnızca hizmetleri tek bir IP adresinde açığa çıkarmak istiyorsanız, [genel](load-balancer-get-started-internet-portal.md) veya [iç](load-balancer-get-started-ilb-arm-portal.md) yük dengeleyici yapılandırmalarına yönelik Basitleştirilmiş yönergeler bulabilirsiniz. Birden çok ön uçların eklenmesi tek bir ön uç yapılandırmasına artımlı yapılır. Bu makaledeki kavramları kullanarak, Basitleştirilmiş bir yapılandırmayı dilediğiniz zaman genişletebilirsiniz.

Bir Azure Load Balancer tanımladığınızda, ön uç ve arka uç havuzu yapılandırması kurallarla bağlanır. Kural tarafından başvurulan sistem durumu araştırması, yeni akışların arka uç havuzundaki bir düğüme nasıl gönderileceğini belirlemekte kullanılır. Ön uç (diğer adıyla VIP), bir IP adresi (genel veya iç), bir Aktarım Protokolü (UDP veya TCP) ve yük dengeleme kuralındaki bir bağlantı noktası numarasından oluşan 3 tanımlama grubu tarafından tanımlanır. Arka uç havuzu, Load Balancer arka uç havuzuna başvuran sanal makine IP yapılandırmalarının (NIC kaynağının bir parçası) koleksiyonudur.

Aşağıdaki tabloda bazı örnek ön uç yapılandırması yer almaktadır:

| Uçta | IP adresi | protocol | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

Tabloda dört farklı ön uçlar gösterilmektedir. Ön uç #1, #2 ve #3 birden çok kurala sahip tek bir ön uçlardır. Aynı IP adresi kullanılır, ancak bağlantı noktası ya da protokol her ön uç için farklıdır. Ön uç protokolünün ve bağlantı noktasının birden çok ön uçlarda yeniden kullanıldığı, ön uçlar #1 ve #4 birden çok ön uçlara bir örnektir.

Azure Load Balancer Yük Dengeleme kurallarını tanımlama konusunda esneklik sağlar. Bir kural, Ön uçtaki bir adresin ve bağlantı noktasının arka uçtaki hedef adresle ve bağlantı noktasıyla nasıl eşleştirildiğini bildirir. Arka uç bağlantı noktalarının kurallar arasında tekrar kullanılıp kullanılmayacağını, kuralın türüne bağlıdır. Her kural türü, ana bilgisayar yapılandırma ve araştırma tasarımını etkileyebilecek özel gereksinimlere sahiptir. İki tür kural vardır:

1. Arka uç bağlantı noktası yeniden kullanımı olmayan varsayılan kural
2. Arka uç bağlantı noktalarının tekrar kullanıldığı kayan IP kuralı

Azure Load Balancer, her iki kural türünü de aynı yük dengeleyici yapılandırmasında karıştırabilmeniz için izin verir. Yük dengeleyici, kural kısıtlamalarına göre IDE olarak kullandığınız sürece, belirli bir VM veya herhangi bir bileşim için aynı anda kullanılabilir. Seçtiğiniz kural türü, uygulamanızın gereksinimlerine ve bu yapılandırmayı destekleme karmaşıklığına bağlıdır. Senaryonuza en uygun olan kural türlerini değerlendirmelisiniz.

Varsayılan davranışla başlayarak bu senaryoları daha ayrıntılı bir şekilde araştırıyoruz.

## <a name="rule-type-1-no-backend-port-reuse"></a>Kural türü #1: arka uç bağlantı noktası yeniden kullanım yok

![Yeşil ve mor ön uçta birden çok ön uç çizimi](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

Bu senaryoda ön uçlar aşağıdaki gibi yapılandırılır:

| Uçta | IP adresi | protocol | port |
| --- | --- | --- | --- |
| ![yeşil ön uç](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![Mor ön uç](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

DIP, gelen akışın hedefi. Arka uç havuzunda, her VM istenen hizmeti bir DIP üzerindeki benzersiz bir bağlantı noktasında kullanıma sunar. Bu hizmet, ön uçta bir kural tanımıyla ilişkilendirilir.

İki kural tanımlanıyoruz:

| Kural | Ön uç eşleme | Arka uç havuzuna |
| --- | --- | --- |
| 1 |![yeşil ön uç](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Önuç1:80 |![arka uç](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![arka uç](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![IP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Önuç2:80 |![arka uç](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![arka uç](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Azure Load Balancer ' deki tüm eşleme artık şu şekildedir:

| Kural | Ön uç IP adresi | protocol | port | Hedef | port |
| --- | --- | --- | --- | --- | --- |
| ![yeşil kural](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |DIP IP adresi |80 |
| ![Mor kural](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |DIP IP adresi |81 |

Her kural, hedef IP adresi ve hedef bağlantı noktasının benzersiz bir birleşimini içeren bir akış üretmelidir. Akışın hedef bağlantı noktasını değiştirerek birden çok kural, farklı bağlantı noktalarında aynı DIP 'e akış sunabilir.

Sistem durumu araştırmaları her zaman bir VM 'nin DIP 'sine yönlendirilir. Araştırmanın VM 'nin sistem durumunu yansıttığından emin olmanız gerekir.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Kural türü #2: kayan IP kullanarak arka uç bağlantı noktası yeniden kullanımı

Azure Load Balancer, kullanılan kural türünden bağımsız olarak birden çok ön uç bağlantı noktasını yeniden kullanma esnekliği sağlar. Ayrıca, bazı uygulama senaryoları, arka uç havuzundaki tek bir VM 'de birden çok uygulama örneği tarafından kullanılacak bağlantı noktasını tercih eder veya gerektirir. Bağlantı noktası yeniden kullanımının yaygın örnekleri, yüksek kullanılabilirlik, ağ sanal cihazları için kümeleme ve yeniden şifrelemeden birden çok TLS uç noktası kullanıma sunma içerir.

Arka uç bağlantı noktasını birden çok kural genelinde yeniden kullanmak istiyorsanız, kural tanımında kayan IP 'yi etkinleştirmeniz gerekir.

"Kayan IP", Azure 'un doğrudan sunucu dönüşü (DSR) olarak bilinen bir bölümü için kullanılan terminolojmedir. DSR iki bölümden oluşur: bir akış topolojisi ve bir IP adresi eşleme şeması. Bir platform düzeyinde Azure Load Balancer, kayan IP 'nin etkin olup olmamasına bakılmaksızın her zaman DSR akış topolojisinde çalışır. Bu, akışın giden bölümünün her zaman doğrudan başlangıca akışa doğru şekilde yeniden yazılması anlamına gelir.

Varsayılan kural türüyle Azure, kullanım kolaylığı için geleneksel bir yük dengeleme IP adresi eşleme düzeni sunar. Kayan IP 'nin etkinleştirilmesi, IP adresi eşleme şemasını aşağıda açıklandığı gibi ek esneklik sağlayacak şekilde değiştirir.

Aşağıdaki diyagramda bu yapılandırma gösterilmektedir:

![DSR ile yeşil ve mor ön uç içeren birden çok ön uç çizimi](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Bu senaryo için, arka uç havuzundaki her sanal makinenin üç ağ arabirimi vardır:

* DIP: VM ile ilişkili bir sanal NIC (Azure 'un NIC kaynağı için IP yapılandırması)
* Ön uç 1: Konuk işletim sisteminde, ön uç 1 IP adresiyle yapılandırılan bir geri döngü arabirimi
* Ön uç 2: Konuk işletim sisteminde, ön uç 2 IP adresiyle yapılandırılmış bir geri döngü arabirimi

> [!IMPORTANT]
> Geri döngü arabirimlerinin yapılandırması, Konuk işletim sistemi içinde gerçekleştirilir. Bu yapılandırma, Azure tarafından gerçekleştirilmez veya yönetilmez. Bu yapılandırma olmadan kurallar çalışmayacaktır. Durum araştırma tanımları, DSR ön ucu temsil eden geri döngü arabirimi yerine VM 'nin DIP 'sini kullanır. Bu nedenle, hizmetiniz DSR ön noktasını temsil eden geri döngü arabiriminde sunulan hizmetin durumunu yansıtan bir DIP bağlantı noktası üzerinde araştırma yanıtları sağlamalıdır.

Önceki senaryodaki ile aynı ön uç yapılandırmasını varsayalım:

| Uçta | IP adresi | protocol | port |
| --- | --- | --- | --- |
| ![yeşil ön uç](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![Mor ön uç](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

İki kural tanımlanıyoruz:

| Kural | Uçta | Arka uç havuzuna eşle |
| --- | --- | --- |
| 1 |![rule](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Önuç1:80 |![arka uç](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Önuç1:80 (VM1 ve VM2) |
| 2 |![rule](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Önuç2:80 |![arka uç](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Önuç2:80 (VM1 ve VM2) |

Aşağıdaki tabloda yük dengeleyicideki bütün eşleme gösterilmektedir:

| Kural | Ön uç IP adresi | protocol | port | Hedef | port |
| --- | --- | --- | --- | --- | --- |
| ![yeşil kural](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |ön uç ile aynı (65.52.0.1) |ön uç ile aynı (80) |
| ![Mor kural](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |ön uç ile aynı (65.52.0.2) |ön uç ile aynı (80) |

Gelen akışın hedefi, VM 'deki geri döngü arabirimindeki ön uç IP adresidir. Her kural, hedef IP adresi ve hedef bağlantı noktasının benzersiz bir birleşimini içeren bir akış üretmelidir. Akışın hedef IP adresini değiştirerek, aynı VM 'de bağlantı noktası yeniden kullanımı mümkündür. Hizmetiniz, ilgili geri döngü arabiriminin ön uç IP adresine ve bağlantı noktasına bağlayarak yük dengeleyiciye açıktır.

Bu örnekte hedef bağlantı noktasını değiştirmediğine dikkat edin. Bu kayan bir IP senaryosu olsa da Azure Load Balancer, arka uç hedef bağlantı noktasını yeniden yazmak ve ön uç hedef bağlantı noktasından farklı hale getirmek için bir kural tanımlamayı da destekler.

Kayan IP kuralı türü, çeşitli yük dengeleyici yapılandırma desenlerinin temelidir. Şu anda kullanılabilen bir örnek, [birden çok dinleyici yapılandırması olan SQL AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) ' dır. Zaman içinde, bu senaryoların daha fazlasını belgeliyoruz.

## <a name="limitations"></a>Sınırlamalar

* Birden çok ön uç yapılandırması yalnızca IaaS sanal makinelerinde desteklenir.
* Kayan IP kuralıyla, uygulamanızın giden SNAT akışları için birincil IP yapılandırmasını kullanması gerekir. Uygulamanız Konuk işletim sistemindeki geri döngü arabiriminde yapılandırılmış ön uç IP adresine bağlandığında, giden akışı yeniden yazmak için Azure 'un giden SNAT 'si kullanılabilir değildir ve akış başarısız olur.  [Giden senaryoları](load-balancer-outbound-connections.md)gözden geçirin.
* Genel IP adreslerinin faturalandırma üzerinde bir etkisi vardır. Daha fazla bilgi için bkz. [IP adresi fiyatlandırması](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Abonelik sınırları geçerlidir. Daha fazla bilgi için bkz. Ayrıntılar için [hizmet limitleri](../azure-subscription-service-limits.md#networking-limits) .

## <a name="next-steps"></a>Sonraki adımlar

- Giden bağlantı davranışında birden çok ön uçların etkisini anlamak için [giden bağlantıları](load-balancer-outbound-connections.md) gözden geçirin.
