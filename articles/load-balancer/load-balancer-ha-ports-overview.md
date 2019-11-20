---
title: Azure yüksek kullanılabilirlik bağlantı noktaları genel bakış
titlesuffix: Azure Load Balancer
description: İç yük dengeleyici üzerinde yüksek kullanılabilirlik bağlantı noktaları yük dengelemeyi hakkında bilgi edinin.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2019
ms.author: allensu
ms.openlocfilehash: 350c6ae2e62a88477ce67132b56d9253166d13ec
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130439"
---
# <a name="high-availability-ports-overview"></a>Yüksek kullanılabilirlik bağlantı noktaları genel bakış

İç yük dengeleyici kullanıldığında azure standart Load Balancer, tüm bağlantı noktalarındaki Yük Dengeleme TCP ve UDP akışlar aynı anda yardımcı olur. 

Yüksek kullanılabilirlik (HA) bağlantı noktaları Yük Dengeleme kuralı, iç Standart Load Balancer yapılandırılan bir yük dengeleme kuralının bir varyantıdır. Tek bir kural yük dengelemek için bir iç standart Load Balancer'ın tüm bağlantı noktalarında gelen tüm TCP ve UDP akışlar sağlayarak bir yük dengeleyicinin kullanılmasını kolaylaştırabilirsiniz. Yük Dengeleme karar akış yapılır. Bu eylem, şu beş demet bağlantısına dayanır: kaynak IP adresi, kaynak bağlantı noktası, hedef IP adresi, hedef bağlantı noktası ve protokol

HA bağlantı noktaları Yük Dengeleme kuralları, sanal ağların içindeki ağ sanal gereçleri (NVA 'lar) için yüksek kullanılabilirlik ve ölçek gibi kritik senaryolarda size yardımcı olur. Özellik bağlantı noktaları, çok sayıda yük dengeli olması gerektiğinde de yardımcı olabilir. 

HA bağlantı noktaları Yük Dengeleme kuralları, ön uç ve arka uç bağlantı noktalarını **0** ' a ve Protokolü **tümüne**ayarladığınızda yapılandırılır. İç yük dengeleyici kaynağı, bağlantı noktası numarasından bağımsız olarak tüm TCP ve UDP akışlarını dengeler

## <a name="why-use-ha-ports"></a>HA bağlantı noktaları neden kullanmalısınız?

### <a name="nva"></a>Ağ sanal Gereçleri

Azure İş yükünüzün güvenlik tehditlerini birden çok tür güvenliğini sağlamaya yardımcı olmak için nva'ları kullanabilirsiniz. Bu senaryolarda nva'ları kullandığınızda, güvenilir ve yüksek oranda kullanılabilir olması gerekir ve bunlar için isteğe bağlı ölçeği gerekir.

İç load balancer arka uç havuzuna yalnızca NVA örneklerinin ekleyerek bu hedeflere elde edebileceğiniz ve yük dengeleyici kuralı yapılandırma bir HA bağlantı noktaları.

NVA HA senaryoları için HA bağlantı noktaları aşağıdaki avantajları sağlar:
- Hızlı yük devretmeyi sağlıklı örnekleri için örnek başına sistem durumu araştırmaları ile sağlayın
- Ölçek genişletme ile daha yüksek performans sağlamak *n*-etkin örnekler
- Sağlamak *n*-etkin ve Aktif-Pasif senaryoları
- Cihazları izleme için Apache ZooKeeper düğümleri gibi karmaşık çözümleri gerekmemesi

Aşağıdaki diyagramda bir merkez ve uç sanal ağ dağıtımı sunar. Uçlar zorlamalı tünel hub sanal ağa ve güvenilen alanı çıkmadan önce NVA aracılığıyla trafiği. HA bağlantı noktaları yapılandırmaya sahip bir iç standart yük dengeleyici arkasında nva'ları var. Tüm trafiği, işlenen ve buna göre iletilir. Aşağıdaki diyagramda göster olarak yapılandırıldığında, bir HA bağlantı noktaları Yük Dengeleme kuralı ek olarak giriş ve çıkış trafiği için Flow simetri sağlar.

<a node="diagram"></a>
![NVA 'lar, HA modunda dağıtılan merkez ve bağlı sanal ağın diyagramı](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Nva'ları kullanıyorsanız sağlayıcıları ile HA bağlantı noktaları en iyi şekilde kullanmak ve hangi senaryolar desteklenir öğrenmek için onaylayın.

### <a name="load-balancing-large-numbers-of-ports"></a>Bağlantı noktalarını çok sayıda Yük Dengeleme

HA bağlantı noktalarını bağlantı noktalarının çok sayıda Yük Dengeleme gerektiren uygulamalar için de kullanabilirsiniz. Bir iç kullanarak bu senaryolar basit hale getirebileceğinizi [Standard Load Balancer](load-balancer-standard-overview.md) HA bağlantı noktaları. Tek bir yük dengeleyici kuralı, birden fazla bireysel Yük Dengeleme kuralları, her bağlantı noktası için bir tane yerine geçer.

## <a name="region-availability"></a>Bölge kullanılabilirliği

HA bağlantı noktaları özelliğini tüm genel Azure bölgelerinde kullanılabilir.

## <a name="supported-configurations"></a>Desteklenen yapılandırmalar

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Standart bir iç yük dengeleyici üzerindeki tek, değişken olmayan bir IP (olmayan - doğrudan sunucu dönüşü) HA bağlantı noktaları yapılandırma

Bu yapılandırma bir temel HA bağlantı noktaları yapılandırmadır. Yapılandırabileceğiniz bir HA bağlantı noktaları Yük Dengeleme kuralı üzerinde tek bir ön uç IP adresi aşağıdakileri yaparak:
1. Standard Load Balancer'ı yapılandırırken, seçin **HA bağlantı noktaları** onay kutusuna yük dengeleyici kuralı yapılandırması.
2. İçin **kayan IP**seçin **devre dışı bırakılmış**.

Bu yapılandırma diğer Yük Dengeleme kuralı yapılandırma geçerli yük dengeleyici kaynağına izin vermez. İç yük dengeleyici kaynağı için başka bir yapılandırma arka uç örneklerinin verilen kümesine izin verir.

Ancak, genel bir Standard Load Balancer arka uç örneklerinin HA bağlantı noktaları kuralın ek olarak yapılandırabilirsiniz.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Standart bir iç yük dengeleyici üzerindeki bir tek, kayan IP (doğrudan sunucu dönüşü) HA bağlantı noktaları yapılandırma

Benzer şekilde, bir Yük Dengeleme kuralı ile kullanmak için yük dengeleyici yapılandırabilirsiniz **HA bağlantı noktası** ayarlayarak, tek bir ön uç ile **kayan IP** için **etkin**. 

Bu yapılandırmayı kullanarak kayan IP yük dengeleyici kuralları ve/veya herkese açık yük dengeleyici ekleyebilirsiniz. Ancak, HA bağlantı noktaları bir değişken olmayan IP kullanamazsınız. Bu yapılandırma üzerinde Yük Dengeleme yapılandırma.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Standart bir iç yük dengeleyici birden çok yapılandırmada HA bağlantı noktaları

Birden fazla HA bağlantı noktası ön uç için aynı arka uç havuzunu yapılandırma senaryonuz gerektiriyorsa, bunu yapabilirsiniz: 
- Birden fazla ön uç özel IP adresi tek bir iç standart yük dengeleyici kaynağı için yapılandırın.
- Her kural seçilen tek benzersiz bir ön uç IP adresine sahip olduğu birden çok Yük Dengeleme kuralları yapılandırın.
- Seçin **HA bağlantı noktaları** seçeneğini ve ardından **kayan IP** için **etkin** tüm Yük Dengeleme kuralları.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>HA bağlantı noktaları ve aynı arka uç örneğinde bir genel yük dengeleyici ile iç yük dengeleyici

Yapılandırabileceğiniz *bir* tek iç standart yük dengeleyici HA bağlantı noktaları ile birlikte arka uç kaynakları için genel bir Standard Load Balancer kaynağı.

>[!NOTE]
>Bu özellik şu anda Azure Resource Manager şablonları kullanılabilir, ancak Azure portalı üzerinden kullanılabilir değil.

## <a name="limitations"></a>Sınırlamalar

- HA bağlantı noktaları Yük Dengeleme kuralları yalnızca iç Standart Load Balancer için kullanılabilir.
- Birleştirme, bir HA bağlantı noktaları Yük Dengeleme kuralı ve bir HA olmayan bağlantı noktaları Yük Dengeleme kuralı desteklenmiyor.
- Mevcut IP parçaları, ilk paket ile aynı hedefe HA bağlantı noktaları Yük Dengeleme kuralları tarafından iletilir.  IP fragmenting bir UDP veya TCP paketi desteklenmez.
- HA bağlantı noktaları Yük Dengeleme kuralları IPv6 için kullanılamaz.
- Akış simetrileri (öncelikli olarak NVA senaryolarında), yalnızca Yukarıdaki diyagramda gösterildiği gibi kullanıldığında ve HA bağlantı noktaları Yük Dengeleme kuralları kullanıldığında, arka uç örneği ve tek bir NIC (ve tek IP yapılandırması) ile desteklenir. Başka hiçbir senaryoda sağlanmaz. Bu, iki veya daha fazla Load Balancer kaynağı ve ilgili kuralları bağımsız kararlar verirken ve hiçbir şekilde koordine etmediği anlamına gelir. Açıklamasına bakın ve için diyagram [ağ sanal Gereçleri](#nva). Birden çok NIC kullandığınızda veya bir ortak ve dahili Load Balancer arasında NVA 'yı kullanarak, Flow simetri kullanılamıyor.  Yanıtların aynı NVA 'ya ulaşmasına izin vermek için, giriş akışını gereç IP 'si ile çözmek için kaynak tarafından geçici bir çözüm bulabilirsiniz.  Ancak, tek bir NIC kullanmanız ve Yukarıdaki diyagramda gösterilen başvuru mimarisini kullanmanız önemle önerilir.


## <a name="next-steps"></a>Sonraki adımlar

- [HA bağlantı noktaları üzerinde iç standart Load Balancer yapılandırma](load-balancer-configure-ha-ports.md)
- [Standart Load Balancer hakkında bilgi edinin](load-balancer-standard-overview.md)
