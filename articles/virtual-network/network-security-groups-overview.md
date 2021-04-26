---
title: Azure ağ güvenlik gruplarına genel bakış
titlesuffix: Azure Virtual Network
description: Ağ güvenlik grupları hakkında bilgi edinin. Ağ güvenlik grupları, Azure kaynakları arasında ağ trafiğini filtrelemenize yardımcı olur.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: kumud
ms.reviewer: kumud
ms.custom: contperf-fy21q1
ms.openlocfilehash: d10c10d602dcfa8f83ee56c2755d800d76516ff1
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058553"
---
# <a name="network-security-groups"></a>Ağ güvenlik grupları
<a name="network-security-groups"></a>

Bir Azure sanal ağındaki Azure kaynaklarından gelen ve giden ağ trafiğini filtrelemek için bir Azure ağ güvenlik grubu kullanabilirsiniz. Bir ağ güvenlik grubu, Azure kaynaklarından oluşan çeşitli türlerden gelen ağ trafiğine veya giden ağ trafiğine izin veren veya reddeden [güvenlik kuralları](#security-rules) içerir. Her kural için kaynak, hedef, bağlantı noktası ve protokol belirtebilirsiniz.

Bu makalede bir ağ güvenlik grubu kuralının özellikleri, uygulanan [varsayılan güvenlik kuralları](#default-security-rules) ve [genişletilmiş bir güvenlik kuralı](#augmented-security-rules)oluşturmak için değiştirebileceğiniz kural özellikleri açıklanmaktadır.

## <a name="security-rules"></a><a name="security-rules"></a> Güvenlik kuralları

Bir ağ güvenlik grubunda Azure abonelik [limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) dahilinde sıfır veya istenen sayıda kural bulunabilir. Her bir kural aşağıdaki özellikleri belirtir:

|Özellik  |Açıklama  |
|---------|---------|
|Ad|Ağ güvenlik grubu içinde benzersiz bir ad.|
|Öncelik | 100 ile 4096 arasında bir rakam. Kurallar öncelik sırasına göre işleme alınır ve düşük rakamlı kurallar daha yüksek önceliğe sahip olduğundan yüksek rakamlı kurallardan önce uygulanır. Trafik bir kuralla eşleştiğinde işlem durur. Bunun sonucunda yüksek önceliğe sahip olan kurallarla aynı özniteliklere sahip olan önceliği daha düşük olan (yüksek rakamlı) kurallar işleme alınmaz.|
|Kaynak veya hedef| Herhangi biri veya tek bir IP adresi, sınıfsız etki alanı içi yönlendirme (CIDR) bloğu (örneğin, 10.0.0.0/24), hizmet etiketi veya uygulama güvenlik grubu. Bir Azure kaynağı için adres belirtirken kaynağa atanmış olan özel IP adresini belirtmeniz gerekir. Ağ güvenlik grupları, Azure gelen trafik için genel IP adresini özel IP adresine çevirdikten sonra ve giden trafik için özel IP adresini genel IP adreslerine çevirmeden önce işleme alınır. . Aralık, hizmet etiketi veya uygulama güvenlik grubu belirterek daha az sayıda güvenlik kuralı oluşturabilirsiniz. Bir kuralda birden fazla IP adresi veya aralığı belirtme özelliği (birden fazla hizmet etiketi veya uygulama grubu belirtemezsiniz) [genişletilmiş güvenlik kuralı](#augmented-security-rules) olarak adlandırılır. Genişletilmiş güvenlik kuralları yalnızca Resource Manager dağıtım modeliyle oluşturulmuş olan ağ güvenlik gruplarında oluşturulabilir. Klasik dağıtım modeliyle oluşturulmuş olan ağ güvenlik gruplarında birden fazla IP adresi ve IP adresi aralığı belirtemezsiniz.|
|Protokol     | TCP, UDP, ıCMP, ESP, AH veya any.|
|Yön| Kuralın gelen veya giden trafiğe uygulanma seçeneği.|
|Bağlantı noktası aralığı     |Tek bir bağlantı noktası veya aralık belirtebilirsiniz. Örneğin 80 veya 10000-10005 değerini kullanabilirsiniz. Aralık belirterek oluşturmanız gereken güvenlik kuralı sayısını azaltabilirsiniz. Genişletilmiş güvenlik kuralları yalnızca Resource Manager dağıtım modeliyle oluşturulmuş olan ağ güvenlik gruplarında oluşturulabilir. Klasik dağıtım modeliyle oluşturulmuş olan ağ güvenlik gruplarında aynı güvenlik kuralı içinde birden fazla bağlantı noktası ve bağlantı noktası aralığı belirtemezsiniz.   |
|Eylem     | İzin ver veya reddet        |

Ağ güvenlik grubu güvenlik kuralları, trafiğe izin verilmesi veya trafiğin reddedilmesi için 5 tanımlama grubu bilgisi (kaynak, kaynak bağlantı noktası, hedef, hedef bağlantı noktası ve protokol) ile önceliğe göre değerlendirilir. Aynı önceliğe ve yöne sahip iki güvenlik kuralı oluşturmeyebilirsiniz. Var olan bağlantılar için bir akış kaydı oluşturulur. Akış kaydının bağlantı durumuna göre iletişime izin verilir veya iletişim reddedilir. Akış kaydı bir ağ güvenlik grubunun durum bilgisine sahip olmasını sağlar. Örneğin 80 numaralı bağlantı noktasından tüm adreslere doğru giden bir güvenlik kuralı belirtirseniz giden trafiğe yanıt olarak bir gelen güvenlik kuralı belirtmeniz gerekli değildir. Yalnızca iletişimin dışarıdan başlatılması halinde bir gelen güvenlik kuralı belirtmeniz gerekir. Bunun tersi de geçerlidir. Gelen trafiğe bir bağlantı noktası üzerinden izin verilmesi halinde bağlantı noktasından geçen trafiğe yanıt olarak bir giden güvenlik belirtmeniz gerekli değildir.

Akışı etkinleştiren bir güvenlik kuralını kaldırdığınızda mevcut bağlantılar kesintiye uğramayabilir. Bağlantılar durdurulduğunda trafik akışları kesintiye uğrar ve en azından birkaç dakika boyunca hiçbir yönde trafik akışı gerçekleşmez.

Bir ağ güvenlik grubu içinde sınırlı sayıda güvenlik kuralı oluşturabilirsiniz. Ayrıntılar için [Azure limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) makalesini inceleyin.

### <a name="default-security-rules"></a><a name="default-security-rules"></a> Varsayılan güvenlik kuralları

Azure, oluşturduğunuz tüm ağ güvenlik gruplarına aşağıdaki varsayılan kuralları ekler:

#### <a name="inbound"></a>Gelen

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|Öncelik|Kaynak|Kaynak bağlantı noktaları|Hedef|Hedef bağlantı noktaları|Protokol|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Herhangi biri|İzin Ver|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Öncelik|Kaynak|Kaynak bağlantı noktaları|Hedef|Hedef bağlantı noktaları|Protokol|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Herhangi biri|İzin Ver|

##### <a name="denyallinbound"></a>DenyAllInbound

|Öncelik|Kaynak|Kaynak bağlantı noktaları|Hedef|Hedef bağlantı noktaları|Protokol|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Herhangi biri|Reddet|

#### <a name="outbound"></a>Giden

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Öncelik|Kaynak|Kaynak bağlantı noktaları| Hedef | Hedef bağlantı noktaları | Protokol | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Herhangi biri | İzin Ver |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Öncelik|Kaynak|Kaynak bağlantı noktaları| Hedef | Hedef bağlantı noktaları | Protokol | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | İnternet | 0-65535 | Herhangi biri | İzin Ver |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|Öncelik|Kaynak|Kaynak bağlantı noktaları| Hedef | Hedef bağlantı noktaları | Protokol | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Herhangi biri | Reddet |

**Kaynak** ve **Hedef** sütunlarında *VirtualNetwork*, *AzureLoadBalancer* ve *Internet*, için IP adresi yerine [hizmet etiketi](service-tags-overview.md) belirtilir. Protokol sütununda, TCP, UDP ve ıCMP **'yi kapsar.** Bir kural oluştururken TCP, UDP, ıCMP veya any belirtebilirsiniz. **Kaynak** ve **Hedef** sütunlarında yer alan *0.0.0.0/0* ifadesi tüm adresleri temsil eder. Azure portal, Azure CLı veya PowerShell gibi istemciler bu ifade için * veya herhangi birini kullanabilir.
 
Varsayılan kuralları kaldıramazsınız ancak daha yüksek önceliğe sahip kurallar oluşturarak onları geçersiz kılabilirsiniz.

### <a name="augmented-security-rules"></a><a name="augmented-security-rules"></a> Genişletilmiş güvenlik kuralları

Genişletilmiş güvenlik kuralları, sanal ağlar için güvenlik tanımını daha basit hale getirerek daha az sayıda kuralla daha geniş çaplı ve karmaşık ağ güvenlik ilkesi tanımlamanızı sağlar. Birden fazla bağlantı noktası ile birden fazla açık IP adresini ve aralığını bir araya getirerek tek ve anlaşılması kolay bir güvenlik kuralı oluşturabilirsiniz. Genişletilmiş kuralları bir kuralın kaynak, hedef ve bağlantı noktası alanlarında kullanabilirsiniz. Güvenlik kuralı tanımınızın bakımını kolaylaştırmak için genişletilmiş güvenlik kurallarını [hizmet etiketleri](service-tags-overview.md) veya [uygulama güvenlik gruplarıyla](#application-security-groups) bir arada kullanabilirsiniz. Bir kuralda belirtebileceğiniz adres, Aralık ve bağlantı noktası sayısı için sınırlar vardır. Ayrıntılar için [Azure limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) makalesini inceleyin.

#### <a name="service-tags"></a>Hizmet etiketleri

Hizmet etiketi, belirli bir Azure hizmetinden bir IP adresi önekleri grubunu temsil eder. Ağ güvenlik kurallarında sık sık güncelleştirmelerin karmaşıklığını en aza indirmenize yardımcı olur.

Daha fazla bilgi için bkz. [Azure hizmet etiketleri](service-tags-overview.md). Ağ erişimini kısıtlamak için depolama hizmeti etiketinin nasıl kullanılacağına ilişkin bir örnek için bkz. [PaaS kaynaklarına ağ erişimini kısıtlama](tutorial-restrict-network-access-to-resources.md).

#### <a name="application-security-groups"></a>Uygulama güvenliği grupları

Uygulama güvenlik grupları ağ güvenliğini uygulamanın yapısının doğal bir uzantısı olarak yapılandırmanıza imkan vererek sanal makineleri gruplamanızı ve ağ güvenlik ilkelerini bu gruplara göre tanımlamanızı sağlar. Açık IP adreslerinin bakımını el ile yapmanıza gerek kalmadan güvenlik ilkesini farklı ölçeklerde yeniden kullanabilirsiniz. Daha fazla bilgi için bkz. [uygulama güvenlik grupları](application-security-groups.md).

## <a name="azure-platform-considerations"></a>Azure platformunda dikkat edilmesi gerekenler

- **Konak düğümünün sanal IP 'si**: DHCP, DNS, imds ve sistem durumu izleme gibi temel altyapı hizmetleri, 168.63.129.16 ve 169.254.169.254 sanallaştırılmış ana bilgisayar IP adresleri aracılığıyla sağlanır. Bu IP adresleri Microsoft 'a aittir ve tüm bölgelerde bu amaç için kullanılan tek sanallaştırılmış IP adresleridir. Etkin güvenlik kuralları ve geçerli rotalar, bu platform kurallarını içermez. Bu temel altyapı iletişimini geçersiz kılmak için, ağ güvenlik grubu kuralınızda aşağıdaki [hizmet etiketlerini](service-tags-overview.md) kullanarak trafiği reddetmek için bir güvenlik kuralı oluşturabilirsiniz: AzurePlatformDNS, AzurePlatformIMDS, AzurePlatformLKM. [Ağ trafiği filtrelemeyi tanılamayı](diagnose-network-traffic-filter-problem.md) ve [ağ yönlendirmeyi tanılamayı](diagnose-network-routing-problem.md)öğrenin.
- **Lisanslama (Anahtar Yönetimi Hizmeti):** Sanal makinelerde çalışan Windows görüntülerinin lisanslanması gerekir. Lisanslama için, lisans isteği sorgularını işleyen Anahtar Yönetimi Hizmeti ana bilgisayar sunucularına bir lisans isteği gönderilir. İstek, bağlantı noktası 1688 üzerinden gönderilir. [default route 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) yapılandırması kullanan dağıtmalar için bu platform kuralı devre dışı bırakılır.
- **Yük dengelenmiş havuzlardaki sanal makineler**: Uygulanan kaynak bağlantı noktası ve adres aralığı, yük dengeleyiciye değil kaynak bilgisayara aittir. Hedef bağlantı noktası ve adres aralığı, yük dengeleyici değil, hedef bilgisayar içindir.
- **Azure hizmet örnekleri**: HDInsight, Uygulama Servisi Ortamları ve Sanal Makine Ölçek Kümeleri gibi sanal ağ alt ağlarına dağıtılmış olan farklı Azure hizmeti örnekleri. Sanal ağlara dağıtabileceğiniz hizmetlerin tam listesi için bkz. [Azure hizmetleri için sanal ağ](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Bir ağ güvenlik grubunu kaynağın dağıtılmış olduğu alt ağa uygulamadan önce hizmetlerle ilgili olan bağlantı noktası gereksinimlerini öğrendiğinizden emin olun. Gerekli bağlantı noktalarını reddetmeniz halinde hizmet düzgün çalışmaz.
- **Giden e-posta gönderme**: Microsoft, Azure sanal makineler 'den e-posta göndermek için KIMLIĞI doğrulanmış SMTP geçiş hizmetlerini (genellikle TCP bağlantı noktası 587 üzerinden bağlanır, ancak diğerleri de diğerleri) kullanmanızı önerir. SMTP geçiş hizmetleri, üçüncü taraf e-posta sağlayıcılarının iletileri reddetme olasılığını en aza indirmek için gönderen saygınlığı konusunda uzmanlaşmıştır. Bu tür SMTP geçiş hizmetleri Exchange Online Protection ve SendGrid'i içerir ancak bunlarla sınırlı değildir. Abonelik türünüz ne olursa olsun, Azure'da SMTP geçiş hizmetlerinin kullanımına hiçbir kısıtlama getirilmez. 

  Azure aboneliğinizi 15 Kasım 2017'den önce oluşturduysanız, SMTP geçiş hizmetlerini kullanabileceğiniz gibi, doğrudan TCP bağlantı noktası 25 üzerinden de e-posta gönderebilirsiniz. Aboneliğinizi 15 Kasım 2017'den sonra oluşturduysanız, doğrudan bağlantı noktası 25 üzerinden e-posta gönderemeyebilirsiniz. Bağlantı noktası 25 üzerinden giden iletişimin davranışı, sahip olduğunuz aboneliğe bağlıdır:

     - **Kurumsal Anlaşma**: Giden bağlantı noktası 25 iletişimine izin verilir. Azure platformunda hiçbir kısıtlama olmadan doğrudan sanal makinelerden dış e-posta sağlayıcılarına giden bir e-posta gönderebilirsiniz. 
     - **Kullandıkça öde:** Tüm kaynaklardan giden bağlantı noktası 25 iletişimi engellenir. Sanal makineden doğrudan dış e-posta sağlayıcılarına (kimliği doğrulanmış SMTP geçişi kullanmadan) e-posta göndermeniz gerekirse, kısıtlamanın kaldırılması için istekte bulunabilirsiniz. İstekler gözden geçirilip Microsoft'un takdirine bağlı olarak onaylanır ve yalnızca dolandırıcılık önleme denetimleri yapıldıktan sonra kabul edilir. İstekte bulunmak için, sorun türü *Teknik*, *Sanal Ağ Bağlantısı*, *E-posta gönderilemiyor (SMTP/Bağlantı Noktası 25)* olan bir destek olayı açın. Destek olayınıza, aboneliğinizin neden kimliği doğrulanmış SMTP geçişi üzerinden değil de doğrudan posta sağlayıcılarına e-posta göndermesi gerektiği konusundaki ayrıntıları da ekleyin. Aboneliğiniz muaf tutulursa, yalnızca muafiyet tarihinden sonra oluşturulmuş sanal makineler bağlantı noktası 25 üzerinden giden iletişimi kurabilir.
     - **MSDN, Azure Pass, Azure in Open, Education, BizSpark ve Ücretsiz deneme**: Tüm kaynaklardan giden bağlantı noktası 25 iletişimi engellenir. Kısıtlamayı kaldırmaya yönelik istekte bulunulamaz çünkü istekler kabul edilmez. Sanal makinenizden e-posta göndermeniz gerekirse, SMTP geçiş hizmetini kullanmanız gerekir.
     - **Bulut hizmeti sağlayıcısı**: Bulut hizmeti sağlayıcısı aracılığıyla Azure kaynakları kullanan müşteriler bulut hizmeti sağlayıcılarıyla bir destek talebi oluşturarak, güvenli SMTP geçişi kullanılamıyorsa sağlayıcıların onlar adına bir engelleme kaldırma talebi oluşturmalarını isteyebilir.

  Azure bağlantı noktası 25 üzerinde e-posta göndermenize izin verirse, Microsoft e-posta sağlayıcılarının sanal makinenizden gelen e-postayı kabul edeceğini garanti edemez. Belirli bir sağlayıcı sanal makinenizden gelen postayı reddederse, tüm ileti teslimi veya istenmeyen posta filtreleme sorunlarını çözmek için doğrudan sağlayıcıyla çalışmanız veya kimliği doğrulanmış SMTP geçiş hizmeti kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* Hangi Azure kaynaklarının bir sanal ağa dağıtılabileceği ve bunlarla ilişkili ağ güvenlik gruplarının olduğu hakkında bilgi edinmek için bkz. [Azure hizmetleri Için sanal ağ tümleştirmesi](virtual-network-for-azure-services.md)
* Trafiğin ağ güvenlik grupları ile nasıl değerlendirildiğini öğrenmek için bkz. [ağ güvenlik grupları nasıl çalışır](network-security-group-how-it-works.md).
* Daha önce bir ağ güvenlik grubu oluşturmadıysanız deneyim edinmek için hızlı [öğreticiyi](tutorial-filter-network-traffic.md) tamamlayabilirsiniz.
* Ağ güvenlik grupları ve yönetimi hakkında bilginiz varsa bkz. [Ağ güvenlik gruplarını yönetme](manage-network-security-group.md). 
* İletişim sorunları yaşıyorsanız ve ağ güvenlik gruplarıyla ilgili sorunları gidermeniz gerekiyorsa bkz. [Sanal makine ağ trafiği filtresi sorunlarını tanılama](diagnose-network-traffic-filter-problem.md). 
* Ağ [güvenlik grubu akış günlüklerinin](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , ilişkili bir ağ güvenlik grubu olan kaynaklardan gelen ve giden ağ trafiğini analiz etmek üzere nasıl etkinleştirileceğini öğrenin.
