---
title: Azure Standart Load Balancer nedir?
titlesuffix: Azure Load Balancer
description: Bu öğrenme yoluyla Azure Standart Load Balancer özelliklerine genel bir bakış ile çalışmaya başlayın.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2019
ms.author: allensu
ms.openlocfilehash: c14cf572410d02892aa8a2b3e9f0f42fce46d411
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74068774"
---
# <a name="azure-standard-load-balancer-overview"></a>Azure Standart Load Balancer genel bakış

Azure Load Balancer, uygulamalarınızı ölçeklendirmenize ve hizmetleriniz için yüksek kullanılabilirlik oluşturmanıza olanak sağlar. Load Balancer, gelen ve giden senaryolar için kullanılabilir ve düşük gecikme süresi, yüksek aktarım hızı ve tüm TCP ve UDP uygulamaları için milyonlarca akışa kadar ölçeği ölçeklendirir. 

Bu makale Standart Load Balancer odaklanılmıştır.  Azure Load Balancer daha genel bir genel bakış için [Load Balancer genel bakış](load-balancer-overview.md) konusunu gözden geçirin.

## <a name="what-is-standard-load-balancer"></a>Standart Load Balancer nedir?

Standart Load Balancer, temel Load Balancer üzerinde genişletilmiş ve daha ayrıntılı bir özellik kümesine sahip tüm TCP ve UDP uygulamalarına yönelik yeni bir Load Balancer ürünüdür.  Birçok benzerlikler olsa da, bu makalede özetlenen farklılığı öğrenmek önemlidir.

Standart Load Balancer, genel veya iç Load Balancer olarak kullanabilirsiniz. Ve bir sanal makine, bir genel ve bir iç Load Balancer kaynağına bağlanabilir.

Load Balancer kaynağının işlevleri her zaman ön uç, bir kural, sistem durumu araştırması ve arka uç havuzu tanımı olarak ifade edilir.  Bir kaynak, birden çok kural içerebilir. Sanal makinenin NIC kaynağından arka uç havuzunu belirterek, sanal makineleri arka uç havuzuna yerleştirebilirsiniz.  Bu parametre ağ profili aracılığıyla geçirilir ve sanal makine ölçek kümeleri kullanılırken genişletilir.

Bir anahtar yönü, kaynak için sanal ağın kapsamıdır.  Temel Load Balancer bir kullanılabilirlik kümesi kapsamında mevcut olsa da, bir Standart Load Balancer sanal ağın kapsamıyla tamamen tümleşiktir ve tüm sanal ağ kavramları geçerlidir.

Load Balancer kaynaklar, Azure 'un, oluşturmak istediğiniz senaryoya ulaşmak için çok kiracılı altyapısını nasıl programtabileceği hakkında ifade ettiğiniz nesnelerdir.  Load Balancer kaynakları ile gerçek altyapı arasında doğrudan ilişki yoktur; Load Balancer oluşturmak bir örnek oluşturmaz, kapasite her zaman kullanılabilir olur ve göz önünde bulundurulması gereken başlangıç veya ölçeklendirme gecikmeleri yoktur. 

>[!NOTE]
> Azure, senaryolarınız için tam olarak yönetilen bir Yük Dengeleme çözümleri paketi sağlar.  TLS sonlandırmasını ("SSL boşaltması") veya HTTP/HTTPS isteği uygulama katmanı işleme için arıyorsanız [Application Gateway](../application-gateway/application-gateway-introduction.md)gözden geçirin.  Genel DNS yük dengeleme özelliği arıyorsanız [Traffic Manager](../traffic-manager/traffic-manager-overview.md)'ı inceleyin.  Uçtan uca senaryolarınız, bu çözümlerin gerektiğinde birleştirilmesinin avantajlarından yararlanabilir.

## <a name="why-use-standard-load-balancer"></a>Neden Standart Load Balancer kullanmalıyım?

Standart Load Balancer uygulamalarınızı ölçeklendirmenizi ve küçük ölçekli dağıtımlardan büyük ve karmaşık çok bölgeli mimarilere kadar yüksek düzeyde kullanılabilirlik oluşturmanızı sağlar.

Standart Load Balancer ve temel Load Balancer arasındaki farklılıklara genel bakış için aşağıdaki tabloyu gözden geçirin:

>[!NOTE]
> Yeni tasarımlarda Standart Load Balancer kullanılmalıdır. 

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Load Balancer, [fiyatlandırma](https://aka.ms/lbpricing)ve [SLA](https://aka.ms/lbsla) [için hizmet sınırlarını](https://aka.ms/lblimits)gözden geçirin.


### <a name="backend"></a>Arka uç havuzu

Standart Load Balancer arka uç havuzları, sanal bir ağdaki herhangi bir sanal makine kaynağına genişletilir.  Bu, en fazla 1000 arka uç örneği içerebilir.  Bir arka uç örneği, NIC kaynağının bir özelliği olan bir IP yapılandırmadır.

Arka uç havuzunda tek başına sanal makineler, kullanılabilirlik kümeleri veya sanal makine ölçek kümeleri bulunabilir.  Ayrıca, arka uç havuzundaki kaynakları karıştırabilirsiniz. Load Balancer kaynak başına arka uç havuzunda en fazla 150 kaynak birleştirebilirsiniz.

Arka uç havuzunuzu nasıl tasarlayabileceğinizi düşünürken, yönetim işlemlerinin süresini daha iyi iyileştirmek için en az sayıda ayrı arka uç havuzu kaynağı tasarlayabilirsiniz.  Veri düzlemi performansı veya ölçeğinde farklılık yoktur.

### <a name="probes"></a>Durum araştırmaları
  
Standart Load Balancer, https uygulamalarınızı doğru şekilde izlemek için [https durum araştırmaları](load-balancer-custom-probe-overview.md#httpprobe) (Aktarım katmanı GÜVENLIĞI (TLS) sarmalayıcısı ile http araştırması) için destek ekler.  

Ayrıca, arka uç havuzunun tamamı [araştırdığınızda](load-balancer-custom-probe-overview.md#probedown), standart Load Balancer tüm kurulu TCP bağlantılarının devam etmesine izin verir. (Temel Load Balancer, tüm örneklere tüm TCP bağlantılarını sonlandırır).

Ayrıntılar için [Load Balancer sistem durumu araştırmalarını](load-balancer-custom-probe-overview.md) gözden geçirin.

### <a name="az"></a>Kullanılabilirlik Alanları

>[!IMPORTANT]
>Bölgeye özgü bilgiler de dahil olmak üzere ilgili konular için [kullanılabilirlik alanları](../availability-zones/az-overview.md) gözden geçirin.

Standart Load Balancer, Kullanılabilirlik Alanları kullanılabildiği bölgelerde ek yetkileri destekler.  Bu özellikler, tüm Standart Load Balancer için artımlı olarak yapılır.  Kullanılabilirlik Alanları yapılandırma ortak ve iç Standart Load Balancer için kullanılabilir.

ZGen olmayan ön uçlar, Kullanılabilirlik Alanları bir bölgede dağıtıldığında varsayılan olarak bölge yedekli hale gelir.   Bölgesel olarak yedekli bir ön uç, bölge hatası ve tüm bölgelerde aynı anda adanmış altyapı tarafından sunulur. 

Ayrıca, belirli bir bölgenin ön uç olduğunu garanti edebilirsiniz. İlgili bölgeyi içeren ve yalnızca tek bir bölgedeki adanmış altyapı tarafından sunulan bir ön uç ön uç paylaşır.

Platformlar arası yük dengeleme, arka uç havuzu için kullanılabilir ve VNET 'teki herhangi bir sanal makine kaynağı bir arka uç havuzunun parçası olabilir.

[Kullanılabilirlik alanları ilgili yetenekler hakkında ayrıntılı tartışmayı](load-balancer-standard-availability-zones.md)gözden geçirin.

### <a name="diagnostics"></a>'Ya

Standart Load Balancer, Azure Izleyici aracılığıyla çok boyutlu ölçümler sağlar.  Bu ölçümler, belirli bir boyut için filtrelenebilir, gruplandırılabilir ve parçalanılabilir.  Bu kişiler, hizmetinizin performansı ve durumuyla ilgili güncel ve geçmiş öngörüler sağlar.  Kaynak Durumu de desteklenir.  Aşağıda, desteklenen Tanılamalar hakkında kısa bir genel bakış verilmiştir:

| Ölçüm | Açıklama |
| --- | --- |
| VIP kullanılabilirliği | Standart Load Balancer, bir bölgedeki veri yolunu, VM 'nizi destekleyen SDN yığınına kadar her zaman Load Balancer ön uca bir şekilde alıştırın. Sağlıklı örnekler kaldığı sürece ölçüm, uygulamanızın yük dengeli trafiğiyle aynı yolu izler. Müşterileriniz tarafından kullanılan veri yolu da onaylanır. Ölçüm, uygulamanız için görünmez ve diğer işlemleri engellemez.|
| DIP kullanılabilirliği | Standart Load Balancer, yapılandırma ayarlarınıza göre uygulama uç noktanızın sistem durumunu izleyen dağıtılmış bir sistem durumu yoklama hizmeti kullanır. Bu ölçüm, Load Balancer havuzundaki her bir örnek uç noktasının bir toplama veya bitiş noktası başına filtrelenmiş görünümünü sağlar.  Durum araştırma yapılandırmanızla gösterildiği gibi Load Balancer uygulamanızın durumunu nasıl görüntüleyerek görebilirsiniz.
| SYN paketleri | Standart Load Balancer TCP bağlantılarını sonlandıramaz veya TCP veya UDP paket akışlarıyla etkileşime almaz. Akışlar ve bunların el sıkışmaları her zaman kaynak ve sanal makine örneği arasındadır. TCP protokol senaryolarınızı daha iyi gidermek için, kaç TCP bağlantı denemesi yapıldığını anlamak üzere SYN paketleri sayaçlarını kullanabilirsiniz. Ölçüm, alınan TCP SYN paketlerinin sayısını bildirir.|
| SNAT bağlantıları | Standart Load Balancer, genel IP adresi ön ucuna bağlı olan giden akış sayısını raporlar. SNAT bağlantı noktaları, tüketilülmüş bir kaynaktır. Bu ölçüm, uygulamanızın giden kaynaklı akışlar için SNAT 'ye ne kadar yoğun bir şekilde bağlı olduğunu belirten bir gösterge verebilir.  Başarılı ve başarısız giden SNAT akışları için sayaçlar raporlanır ve giden akışlarınızın durumunu gidermek ve anlamak için kullanılabilir.|
| Bayt sayaçları | Standart Load Balancer, ön uç başına işlenen verileri raporlar.|
| Paket sayaçları | Standart Load Balancer, ön uç başına işlenen paketleri raporlar.|

[Standart Load Balancer Tanılamanın ayrıntılı tartışmasını](load-balancer-standard-diagnostics.md)gözden geçirin.

### <a name="haports"></a>HA bağlantı noktaları

Standart Load Balancer yeni bir kural türünü destekler.  

Uygulamanızı ölçeklendirmenizi ve son derece güvenilir olmasını sağlamak için Yük Dengeleme kurallarını yapılandırabilirsiniz. HA bağlantı noktaları Yük Dengeleme kuralı kullandığınızda Standart Load Balancer, bir iç Standart Load Balancer ön uç IP adresinin her kısa ömürlü bağlantı noktasında akış başına yük dengelemesi sağlar.  Özelliği, tek tek bağlantı noktalarını belirtmek pratik veya istenmeyen olan diğer senaryolar için yararlıdır.

Bir HA bağlantı noktaları Yük Dengeleme kuralı, ağ sanal gereçleri ve tüm uygulamalar için etkin-Pasif veya etkin-etkin n + 1 senaryoları oluşturmanıza izin verir ve bu da büyük sayıda gelen bağlantı noktası gerektirir.  Bir sistem durumu araştırması, hangi arka uçları yeni akışlar almak gerektiğini tespit etmek için kullanılabilir.  Bir bağlantı noktası aralığı senaryosuna öykünmek için bir ağ güvenlik grubu kullanabilirsiniz.

>[!IMPORTANT]
> Bir ağ sanal gereci kullanmayı planlıyorsanız, ürünün HA bağlantı noktalarıyla test edilip edilmeyeceğini ve uygulama için kendi özel kılavuzlarını izlemesini sağlamak için satıcınıza başvurun. 

[Ha bağlantı noktalarının ayrıntılı tartışmasını](load-balancer-ha-ports-overview.md)gözden geçirin.

### <a name="securebydefault"></a>Varsayılan olarak güvenli

Standart Load Balancer sanal ağa tamamen eklendi.  Sanal ağ, özel, kapalı bir ağ.  Standart yük dengeleyiciler ve standart genel IP adresleri, sanal ağın dışından bu sanal ağa erişilmesine izin verecek şekilde tasarlandığından, bu kaynaklar artık açılmadığınız takdirde varsayılan olarak kapalı olur. Bu, artık izin verilen trafiğe açıkça izin vermek ve bunları yeniden listelemek için ağ güvenlik grupları (NSG 'ler) kullanıldığı anlamına gelir.  Tüm sanal veri merkezinizi oluşturabilir ve ne zaman kullanılabilir olması gerektiği konusunda NSG 'ye karar verebilirsiniz.  Sanal makine kaynağınızın bir alt ağda veya NIC 'inde bir NSG 'niz yoksa, trafiğin bu kaynağa erişmesine izin verilmez.

NSG 'ler ve senaryonuz için nasıl uygulanacağı hakkında daha fazla bilgi edinmek için bkz. [ağ güvenlik grupları](../virtual-network/security-overview.md).

### <a name="outbound"></a>Giden bağlantılar

Load Balancer gelen ve giden senaryoları destekler.  Standart Load Balancer, giden bağlantılara göre temel Load Balancer önemli ölçüde farklıdır.

Kaynak ağ adresi çevirisi (SNAT), sanal ağınızdaki iç, özel IP adreslerini Load Balancer ön uçların genel IP adreslerine eşlemek için kullanılır.

Standart Load Balancer [daha sağlam, ölçeklenebilir ve öngörülebilir BIR SNAT algoritması](load-balancer-outbound-connections.md#snat) için yeni bir algoritma sunar ve yeni yetenekler sağlar, belirsizlik kaldırır ve yan etkileri yerine açık yapılandırmalara zorlar. Bu değişiklikler, yeni özelliklerin ortaya çıkmasına izin vermek için gereklidir. 

Bunlar, Standart Load Balancer çalışırken hatırlayamamanız gereken anahtarlardır:

- bir kuralın tamamlanması Load Balancer kaynağını sürücüler.  Tüm Azure programlamasında yapılandırmadan türetiliyor.
- birden çok ön uç kullanılabilir olduğunda, tüm ön uçlar kullanılır ve her ön uç, kullanılabilir SNAT bağlantı noktası sayısını çarpar
- giden bağlantılar için belirli bir ön uç için kullanmak istemiyorsanız, öğesini seçebilir ve kontrol edebilirsiniz.
- giden senaryolar açık ve giden bağlantı belirtilene kadar yok.
- Yük Dengeleme kuralları, SNAT 'nin nasıl programlandığı hakkında bir görüntü çıkarır. Yük Dengeleme kuralları protokole özeldir. SNAT protokolüne özgüdür ve yapılandırma, yan bir efekt oluşturmak yerine bunu yansıtmalıdır.

#### <a name="multiple-frontends"></a>Birden çok ön uç
Zaten giden bağlantılar için yüksek taleple karşılaştığınızdan veya daha önce büyük bir talep yaşıyor olduğunuzdan, daha fazla SNAT bağlantı noktası isterseniz, aynı sanal makineye ek ön uçlar, kurallar ve arka uç havuzları yapılandırarak artımlı SNAT bağlantı noktası envanterini de ekleyebilirsiniz kaynakların.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Giden için hangi ön uç kullanıldığını denetleme
Giden bağlantıları yalnızca belirli bir ön uç IP adresinden kaynaklanacak şekilde kısıtlamak istiyorsanız, isteğe bağlı olarak giden eşlemeyi ifade eden kuralda giden SNAT 'yi devre dışı bırakabilirsiniz.

#### <a name="control-outbound-connectivity"></a>Giden bağlantıyı denetleme
Standart Load Balancer sanal ağın bağlamı içinde var.  Bir sanal ağ yalıtılmış, özel bir ağ.  Genel IP adresi ile bir ilişki mevcut değilse, genel bağlantıya izin verilmez.  Sanal ağınızda ve yerelde olduklarından [VNET hizmet uç noktalarına](../virtual-network/virtual-network-service-endpoints-overview.md) ulaşabilirsiniz.  Sanal ağınızın dışında bir hedefe giden bağlantı kurmak istiyorsanız iki seçeneğiniz vardır:
- Standart SKU genel IP adresini, sanal makine kaynağına örnek düzeyi genel IP adresi olarak atayın veya
- sanal makine kaynağını ortak bir Standart Load Balancer arka uç havuzuna yerleştirin.

Her ikisi de sanal ağın dışından sanal ağın dışına giden bağlantıya izin verir. 

_Yalnızca_ sanal makine kaynağınızın bulunduğu arka uç havuzuyla ilişkili bir dahili standart Load Balancer varsa, sanal makineniz yalnızca sanal ağ kaynaklarına ve [VNET hizmet uç noktalarına](../virtual-network/virtual-network-service-endpoints-overview.md)ulaşabilir.  Giden bağlantı oluşturmak için önceki paragrafta açıklanan adımları izleyebilirsiniz.

Standart SKU 'Lar ile ilişkilendirilmemiş bir sanal makine kaynağının giden bağlantısı, daha önce olduğu gibi kalır.

[Giden bağlantıların ayrıntılı tartışmasını](load-balancer-outbound-connections.md)gözden geçirin.

### <a name="multife"></a>Birden çok ön uç
Load Balancer birden çok ön uç içeren birden çok kuralı destekler.  Standart Load Balancer bunu giden senaryolara genişletir.  Giden senaryolar temelde bir gelen yük dengeleme kuralının tersidir.  Gelen yük dengeleme kuralı, giden bağlantılar için bir ilişkilendirme de oluşturur. Standart Load Balancer, bir yük dengeleme kuralı aracılığıyla bir sanal makine kaynağıyla ilişkili tüm ön uçları kullanır.  Ayrıca, Yük Dengeleme kuralındaki bir parametre ve bir yük dengeleme kuralını giden bağlantı amacıyla bastırarak, yok dahil belirli ön uçlar seçimine izin verir.

Karşılaştırma için temel Load Balancer rastgele tek bir ön uç seçer ve hangisinin seçili olduğunu denetleyebilme özelliği yoktur.

[Giden bağlantıların ayrıntılı tartışmasını](load-balancer-outbound-connections.md)gözden geçirin.

### <a name="operations"></a>Yönetim Işlemleri

Standart Load Balancer kaynaklar tamamen yeni bir altyapı platformunda mevcuttur.  Bu, standart SKU 'Lar ve tamamlanma süreleri için daha hızlı yönetim işlemlerini standart SKU kaynağı başına 30 saniyeden daha kısa bir süre sağlar.  Arka uç havuzlarının boyutu artdıkça, arka uç havuzu değişiklikleri için gereken süre de artar.

Standart Load Balancer kaynaklarını değiştirebilir ve standart bir genel IP adresini bir sanal makineden diğerine daha hızlı taşıyabilirsiniz.

## <a name="migration-between-skus"></a>SKU 'Lar arasında geçiş

SKU 'Lar değişebilir değildir. Bir kaynak SKU 'sundan diğerine geçmek için bu bölümdeki adımları izleyin.

>[!IMPORTANT]
>SKU 'Lar arasındaki farkları anlamak için bu belgeyi tamamen gözden geçirin ve senaryonuzu dikkatle inceledi.  Senaryonuzu hizalamak için ek değişiklikler yapmanız gerekebilir.

### <a name="migrate-from-basic-to-standard-sku"></a>Temel bilgisayardan standart SKU 'ya geçiş

1. Yeni bir standart kaynak (Load Balancer ve gerektiğinde genel IP 'Ler) oluşturun. Kurallarınızı ve araştırma tanımlarınızı yeniden oluşturun.  Daha önce 443/TCP için TCP araştırması kullanıyorsanız, bu araştırma protokolünü bir HTTPS araştırması olarak değiştirmeyi ve bir yol eklemeyi düşünün.

2. Yük dengeli trafiği, araştırmayı ve izin vermek istediğiniz diğer trafiği beyaz listeye almak için NIC veya alt ağ üzerinde yeni bir NSG oluşturun veya var olan NSG 'yi güncelleştirin.

3. Tüm sanal makine örneklerinden temel SKU kaynaklarını (uygun olduğu gibi Load Balancer ve genel IP 'Leri) kaldırın. Bir kullanılabilirlik kümesinin tüm sanal makine örneklerini de kaldırdığınızdan emin olun.

4. Tüm sanal makine örneklerini yeni standart SKU kaynaklarına ekleyin.

### <a name="migrate-from-standard-to-basic-sku"></a>Standart 'ten temel SKU 'ya geçiş

1. Yeni bir temel kaynak (Load Balancer ve genel IP 'Leri gerektiği şekilde) oluşturun. Kurallarınızı ve araştırma tanımlarınızı yeniden oluşturun.  HTTPS araştırmasını TCP araştırmasına, 443/TCP 'ye değiştirin. 

2. Tüm sanal makine örneklerinden standart SKU kaynaklarını (geçerli olduğu gibi Load Balancer ve genel IP 'Leri) kaldırın. Bir kullanılabilirlik kümesinin tüm sanal makine örneklerini de kaldırdığınızdan emin olun.

3. Tüm sanal makine örneklerini yeni temel SKU kaynaklarına ekleyin.

>[!IMPORTANT]
>
>Temel ve standart SKU 'ların kullanımıyla ilgili sınırlamalar vardır.
>
>Standart SKU 'nun HA bağlantı noktaları ve tanılama özellikleri yalnızca standart SKU 'da kullanılabilir. Standart SKU 'dan temel SKU 'ya geçiş yapamazsınız ve ayrıca bu özellikleri de koruyabilirsiniz.
>
>Hem temel hem de standart SKU, bu makalede özetlenen sayıda farklılık vardır.  Bunları anladığınızdan ve hazırlandığınızdan emin olun.
>
>Eşleşen SKU 'Ların Load Balancer ve genel IP kaynakları için kullanılması gerekir. Temel SKU kaynakları ve standart SKU kaynaklarının bir karışımı olamaz. Tek başına sanal makineleri, bir kullanılabilirlik kümesi kaynağındaki sanal makineleri veya sanal makine ölçek kümesi kaynaklarını aynı anda iki SKU’ya iliştiremezsiniz.

## <a name="region-availability"></a>Bölge kullanılabilirliği

Standart Load Balancer Şu anda tüm genel bulut bölgelerinde kullanılabilir.

## <a name="sla"></a>SLA

Standart yük dengeleyiciler% 99,99 SLA ile kullanılabilir.  Ayrıntılar için [Standart Load Balancer SLA 'sını](https://aka.ms/lbsla) gözden geçirin.

## <a name="pricing"></a>Fiyatlandırma

Standart Load Balancer kullanım ücretlendirilir.

- Yapılandırılmış yük dengeleme ve giden kural sayısı (gelen NAT kuralları toplam kural sayısına göre sayılmaz)
- Kural ne olursa olsun gelen ve giden işlenen veri miktarı. 

Standart Load Balancer fiyatlandırma bilgileri için [Load Balancer fiyatlandırması](https://azure.microsoft.com/pricing/details/load-balancer/) sayfasını inceleyin.

## <a name="limitations"></a>Sınırlamalar

- SKU 'Lar değişebilir değildir. Mevcut bir kaynağın SKU 'sunu değiştiremeyebilirsiniz.
- Tek başına bir sanal makine kaynağı, kullanılabilirlik kümesi kaynağı veya sanal makine ölçek kümesi kaynağı, her ikisi de tek bir SKU 'ya başvurabilir.
- Load Balancer kuralı iki sanal ağı yayamaz.  Ön uçlar ve ilgili arka uç örnekleri aynı sanal ağda bulunmalıdır.  
- [Abonelik taşıma Işlemleri](../azure-resource-manager/resource-group-move-resources.md) standart SKU lb ve PIP kaynakları için desteklenmez.
- VNet ve diğer Microsoft Platformu Hizmetleri olmayan Web çalışanı rolleri, ön VNet Hizmetleri ve diğer platform hizmetleri işlevinin yan etkisi nedeniyle yalnızca bir iç Standart Load Balancer kullanıldığında erişilebilir. İlgili hizmetin kendisi veya temeldeki platformun hiçbir bildirimde bulunulmadan değişiklik yapabilmediği için bu bunu temel almalısınız. Yalnızca dahili Standart Load Balancer kullandığınızda istenirse, açıkça [giden bağlantı](load-balancer-outbound-connections.md) oluşturmanız gerektiğini varsaymanız gerekir.
- Load Balancer, TCP veya UDP IP protokolleri için yük dengeleme ve bağlantı noktası iletme özellikleri sunan bir üründür.  Yük dengeleme kuralları ve gelen NAT kuralları, TCP ve UDP için desteklenir ancak ICMP dahil olmak üzere diğer IP protokolleri için desteklenmez. Load Balancer, UDP veya TCP akışlarının yüklerini sonlandırmaz, yanıtlamaz veya başka bir şekilde etkileşim kurmaz. Bir ara sunucu değildir. Ön uca bağlantının başarıyla doğrulanması, Yük Dengeleme veya gelen NAT kuralında (TCP veya UDP) aynı protokolle aynı protokol ile bant içinde yer almalıdır _ve_ sanal makinelerinizden en az birinin bir istemcinin ön ucundan yanıt görmesini sağlamak için bir yanıt oluşturması gerekir.  Load Balancer ön uca bir bant içi yanıt alınamayacak sanal makinelerin yanıt veremediğini belirtir.  Bir sanal makine yanıt veremeden bir Load Balancer ön ucuna etkileşimde bulunmak mümkün değildir.  Bu durum [port masquerade SNAT](load-balancer-outbound-connections.md#snat) öğesinin yalnızca TCP ve UDP için desteklendiği giden bağlantılar için de geçerlidir. ICMP de dahil olmak üzere diğer bağlantılar başarısız olacaktır.  Azaltmak için bir örnek düzeyinde Genel IP adresi atayın.
- Sanal ağ içindeki özel IP adreslerinden ortak IP adreslerine geçiş yaparken [giden bağlantılar](load-balancer-outbound-connections.md) sağlayan genel yük dengeleyiciler farklı olarak, Iç yük dengeleyiciler giden kaynaklı bağlantıları, her ikisi de özel IP adresi alanında olan bir iç Load Balancer ön ucuna çevirmez.  Bu, çevirinin gerekli olmadığı benzersiz iç IP adresi alanı içinde SNAT tükenmesi olasılığını ortadan kaldırır.  Yan etkisi, arka uç havuzundaki bir VM 'den giden bir akışın, bulunduğu _ve_ kendisine geri eşlendiği iç Load Balancer ön uca akmasını denediğinde akışın her iki tarafının da eşleşmediğine ve akışın başarısız olmasına neden olur.  Akış ön uca akışı oluşturan arka uç havuzundaki aynı VM 'ye geri eşlenmediyseniz, akış başarılı olur.   Akış kendisine geri eşleniyorsa giden akış VM 'den ön uca, buna karşılık gelen gelen akış ise VM 'den kendisine doğru şekilde görünür. Konuk işletim sisteminin açısından bakıldığında aynı akışın gelen ve giden bölümleri sanal makine ile eşleşmez. Kaynak ve hedef eşleşmediğinden TCP yığını aynı akışın iki yarısını aynı akışa ait gibi görmez.  Akış, arka uç havuzundaki diğer herhangi bir sanal makineye eşleniyorsa, akışın kilitlenme eşleşir ve VM akışa başarıyla yanıt verebilir.  Bu senaryonun belirtisi aralıklı bağlantı zaman aşımları. Bu senaryoyu güvenilir bir şekilde sağlamak için birkaç yaygın geçici çözüm vardır (bir arka uç havuzundan, iç Load Balancer veya [DSR stil kurallarını kullanarak](load-balancer-multivip-overview.md)bir üçüncü taraf proxy 'nin eklenmesini içeren arka uç Load Balancer havuzlarına giden akışlar).  Karmaşıklığı azaltmak için genel Load Balancer kullanabilirsiniz ancak elde edeceğiniz senaryo [SNAT tükenmesi](load-balancer-outbound-connections.md#snat) olasılığına sahip olur ve dikkatli bir şekilde yönetilmediği sürece kaçınılmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

- [Standart Load Balancer ve kullanılabilirlik alanları](load-balancer-standard-availability-zones.md)kullanma hakkında bilgi edinin.
- [Sistem durumu araştırmaları](load-balancer-custom-probe-overview.md)hakkında bilgi edinin.
- [Kullanılabilirlik alanları](../availability-zones/az-overview.md)hakkında daha fazla bilgi edinin.
- [Standart Load Balancer tanılama](load-balancer-standard-diagnostics.md)hakkında bilgi edinin.
- [Azure izleyici](../monitoring-and-diagnostics/monitoring-overview.md)'de Tanılama için [desteklenen çok boyutlu ölçümler](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) hakkında bilgi edinin.
- Kullanma hakkında bilgi edinin [giden bağlantılar için yük dengeleyici](load-balancer-outbound-connections.md).
- [Giden kuralları](load-balancer-outbound-rules-overview.md)hakkında bilgi edinin.
- [Boşta durumunda TCP sıfırlaması](load-balancer-tcp-reset.md)hakkında bilgi edinin.
- [Ha bağlantı noktaları Yük Dengeleme kurallarıyla standart Load Balancer](load-balancer-ha-ports-overview.md)hakkında bilgi edinin.
- [Birden çok ön uç ile Load Balancer](load-balancer-multivip-overview.md)kullanma hakkında bilgi edinin.
- [Sanal ağlar](../virtual-network/virtual-networks-overview.md)hakkında bilgi edinin.
- [Ağ güvenlik grupları](../virtual-network/security-overview.md)hakkında daha fazla bilgi edinin.
- Sanal ağ [hizmeti uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md)hakkında bilgi edinin.
- Azure 'daki diğer bazı anahtar [ağ özellikleri](../networking/networking-overview.md) hakkında bilgi edinin.
- [Load Balancer](load-balancer-overview.md)hakkında daha fazla bilgi edinin.
