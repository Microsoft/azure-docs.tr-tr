---
title: Ölçümler, uyarılar ve kaynak durumu ile tanılama-Azure Standart Load Balancer
description: Azure Standart Load Balancer tanımak için kullanılabilir ölçümleri, uyarıları ve kaynak durumu bilgilerini kullanın.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: allensu
ms.openlocfilehash: ff42c6e9bd3c25721d2b77e49c2dd98a3eebdb43
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048738"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Ölçümler, uyarılar ve kaynak durumu ile Standart Load Balancer

Azure Standart Load Balancer aşağıdaki tanılama yeteneklerini kullanıma sunar:

* **Çok boyutlu ölçümler ve uyarılar**: Standart yük dengeleyici yapılandırmalarına yönelik [Azure izleyici](https://docs.microsoft.com/azure/azure-monitor/overview) aracılığıyla yeni çok boyutlu tanılama özellikleri sağlar. Standart yük dengeleyici kaynaklarınızı izleyebilir, yönetebilir ve sorun giderebilirsiniz.

* **Kaynak sistem durumu**: Azure portal ve Kaynak Durumu sayfasındaki Load Balancer sayfası (izleyici altında) Standart Load Balancer için kaynak durumu bölümünü kullanıma sunar. 

Bu makalede, bu yetenekler için hızlı bir tura yer verilmiştir ve bunları Standart Load Balancer için kullanmanın yolları sunulmaktadır.

## <a name = "MultiDimensionalMetrics"></a>Çok boyutlu ölçümler

Azure Load Balancer, Azure portal yeni Azure ölçümleri aracılığıyla yeni çok boyutlu ölçümler sağlar ve yük dengeleyici kaynaklarınıza gerçek zamanlı tanılama öngörüleri almanıza yardımcı olur. 

Çeşitli Standart Load Balancer yapılandırmalarında aşağıdaki ölçümler sağlanır:

| Ölçüm | Kaynak türü | Açıklama | Önerilen toplama |
| --- | --- | --- | --- |
| Veri yolu kullanılabilirliği (VIP kullanılabilirliği)| Ortak ve iç yük dengeleyici | Standart Load Balancer, bir bölgedeki veri yolunu yük dengeleyici ön ucuna, VM 'nizi destekleyen SDN yığınına kadar her zaman bir şekilde alıştırın. Sağlıklı örnekler kaldığı sürece ölçüm, uygulamanızın yük dengeli trafiğiyle aynı yolu izler. Müşterilerinizin kullandığı veri yolu da onaylanır. Ölçüm, uygulamanız için görünmez ve diğer işlemleri engellemez.| Ortalama |
| Durum araştırma durumu (DIP kullanılabilirliği) | Ortak ve iç yük dengeleyici | Standart Load Balancer, yapılandırma ayarlarınıza göre uygulama uç noktanızın sistem durumunu izleyen dağıtılmış bir sistem durumu algılama hizmeti kullanır. Bu ölçüm, yük dengeleyici havuzundaki her bir örnek uç noktasının toplam veya uç nokta başına filtrelenmiş bir görünümünü sağlar. Durum araştırma yapılandırmanızla gösterildiği gibi, uygulamanızın sistem durumunu nasıl görüntülemelerini Load Balancer görebilirsiniz. |  Ortalama |
| SYN (Synchronize) paketleri | Ortak ve iç yük dengeleyici | Standart Load Balancer, Iletim Denetim Protokolü (TCP) bağlantılarını sonlandırır veya TCP veya UDP paket akışlarıyla etkileşime girmez. Akışlar ve bunların el sıkışmaları her zaman kaynak ve sanal makine örneği arasındadır. TCP protokol senaryolarınızı daha iyi gidermek için, kaç TCP bağlantı denemesi yapıldığını anlamak üzere SYN paketleri sayaçlarını kullanabilirsiniz. Ölçüm, alınan TCP SYN paketlerinin sayısını bildirir.| Ortalama |
| SNAT bağlantıları | Ortak yük dengeleyici |Standart Load Balancer, genel IP adresi ön ucuna bağlı olan giden akış sayısını raporlar. Kaynak ağ adresi çevirisi (SNAT) bağlantı noktaları, tüketilülmüş bir kaynaktır. Bu ölçüm, uygulamanızın giden kaynaklı akışlar için SNAT 'ye ne kadar yoğun bir şekilde bağlı olduğunu belirten bir gösterge verebilir. Başarılı ve başarısız giden SNAT akışları için sayaçlar raporlanır ve giden akışlarınızın durumunu gidermek ve anlamak için kullanılabilir.| Ortalama |
| Bayt sayaçları |  Ortak ve iç yük dengeleyici | Standart Load Balancer, ön uç başına işlenen verileri raporlar.| Ortalama |
| Paket sayaçları |  Ortak ve iç yük dengeleyici | Standart Load Balancer, ön uç başına işlenen paketleri raporlar.| Ortalama |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Yük dengeleyici ölçülerinizi Azure portal görüntüleyin

Azure portal, yük dengeleyici ölçümlerini, belirli bir kaynak için hem yük dengeleyici kaynak sayfasında hem de Azure Izleyici sayfasında bulunan ölçümler sayfası aracılığıyla kullanıma sunar. 

Standart Load Balancer kaynaklarınızın ölçümlerini görüntülemek için:
1. Ölçümler sayfasına gidin ve aşağıdakilerden birini yapın:
   * Yük dengeleyici kaynağı sayfasında, açılan listeden ölçüm türünü seçin.
   * Azure Izleyici sayfasında, yük dengeleyici kaynağını seçin.
2. Uygun toplama türünü ayarlayın.
3. İsteğe bağlı olarak, gerekli filtreleme ve gruplandırmayı yapılandırın.

    ![Standart Load Balancer ölçümleri](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

    *Şekil: Standart Load Balancer için veri yolu kullanılabilirliği ölçümü*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>API 'Ler aracılığıyla çok boyutlu ölçümleri program aracılığıyla alma

Çok boyutlu ölçüm tanımlarını ve değerlerini almaya yönelik API Kılavuzu için bkz. [Azure izleme REST API izlenecek yol](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Yaygın tanılama senaryoları ve önerilen görünümler

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Veri yolu, yük dengeleyici VIP 'im için hazır ve kullanılabilir mi?

VIP kullanılabilirlik ölçümü, sanal makinelerinizin bulunduğu işlem konağının bölge içindeki veri yolunun sistem durumunu açıklar. Ölçüm, Azure altyapısının sistem durumunun bir yansımasıyla aynıdır. Ölçüyü şu şekilde kullanabilirsiniz:
- Hizmetinizin dış kullanılabilirliğini izleyin
- Daha ayrıntılı bilgi edinin ve hizmetinizin dağıtıldığı platformun sağlıklı olup olmadığını veya Konuk işletim sistemi ya da uygulama örneğinizin sağlıklı olup olmadığını anlayın.
- Bir olayın hizmetinize mı yoksa temel alınan veri düzlemine mi ilgili olduğunu yalıtın. Bu ölçüyü durum araştırma durumu ("DIP kullanılabilirliği") ile karıştırmayın.

Standart Load Balancer kaynaklarınız için veri yolu kullanılabilirliğini almak için:
1. Doğru yük dengeleyici kaynağının seçildiğinden emin olun. 
2. **Ölçüm** açılan listesinde, **veri yolu kullanılabilirliği**' ni seçin. 
3. **Toplama** açılan listesinde, **Ort**' ı seçin. 
4. Ayrıca, ön uç IP adresi veya ön uç bağlantı noktası için gerekli ön uç IP adresi veya ön uç bağlantı noktasıyla boyut olarak bir filtre ekleyin ve ardından bunları seçilen boyuta göre gruplayın.

![VIP yoklama](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Şekil: Load Balancer ön uç araştırma ayrıntıları*

Ölçüm, etkin ve bant içi ölçüm tarafından oluşturulur. Bölge içindeki bir yoklama hizmeti, ölçüm için trafik kaynağı. Hizmet, genel ön uç ile bir dağıtım oluşturduktan hemen sonra etkinleştirilir ve ön ucu kaldırana kadar devam eder. 

Dağıtımınızın ön ucu ve kuralıyla eşleşen bir paket düzenli aralıklarla oluşturulur. Kaynak bölgeden, arka uç havuzundaki bir VM 'nin bulunduğu konağa geçer. Yük dengeleyici altyapısı, diğer tüm trafikle aynı yük dengelemeyi ve çeviri işlemlerini gerçekleştirir. Bu araştırma, yük dengeli uç noktanıza bant içinde yer alır. Arka uç havuzunda iyi durumda olan bir VM 'nin bulunduğu işlem konağına araştırma yapıldıktan sonra, işlem Konağı yoklama hizmetine bir yanıt üretir. VM 'niz bu trafiği görmez.

VIP kullanılabilirliği aşağıdaki nedenlerden dolayı başarısız olur:
- Dağıtımınızda arka uç havuzunda kalan sağlıklı VM yok. 
- Bir altyapı kesintisi oluştu.

Tanılama amacıyla, [veri yolu kullanılabilirlik ölçüsünü sistem durumu araştırma durumuyla birlikte](#vipavailabilityandhealthprobes)kullanabilirsiniz.

Çoğu senaryo için toplama olarak **Ortalama** kullanın.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>VIP 'lerimin yoklamalara yanıt vermesini sağlamak için arka uç örnekleri var mı?

Durum araştırma durumu ölçümü, yük dengeleyicinizin sistem durumu araştırmasını yapılandırırken sizin tarafınızdan yapılandırılan uygulama dağıtımınızın sistem durumunu açıklar. Yük dengeleyici, yeni akışların nereye gönderileceğini öğrenmek için sistem durumu araştırmasının durumunu kullanır. Sistem durumu araştırmaları bir Azure altyapı adresinden kaynaklanmakta ve VM 'nin Konuk işletim sistemi içinde görülebilir.

Standart Load Balancer kaynaklarınızın durum araştırma durumunu almak için:
1. **Ortalama** toplama türü Ile **sistem durumu araştırma durumu** ölçümünü seçin. 
2. Gerekli ön uç IP adresine veya bağlantı noktasına (veya her ikisine) bir filtre uygulayın.

Durum araştırmaları aşağıdaki nedenlerden dolayı başarısız olur:
- Bir sistem durumu araştırması, dinlemede olmayan veya yanıt vermeyen bir bağlantı noktasına veya yanlış protokol kullanılarak yapılandırılır. Hizmetiniz doğrudan sunucu dönüşü (DSR veya kayan IP) kuralları kullanıyorsa, hizmetin yalnızca ön uç IP adresiyle yapılandırılmış geri döngüyle değil, NIC 'nin IP yapılandırmasının IP adresini dinlediğinden emin olun.
- Ağ güvenlik grubu, sanal makinenin Konuk işletim sistemi güvenlik duvarı veya uygulama katmanı filtreleri tarafından araştırmanız buna izin verilmez.

Çoğu senaryo için toplama olarak **Ortalama** kullanın.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Giden bağlantı istatistiklerimi denetlemek Nasıl yaparım? mı? 

SNAT bağlantı ölçümü, [giden akışlar](https://aka.ms/lboutbound)için başarılı ve başarısız bağlantıların hacmini açıklar.

Sıfırdan büyük bir hatalı bağlantı birimi, SNAT bağlantı noktası tükenmesi olduğunu gösterir. Bu hatalara neyin neden olabileceğini belirlemek için daha fazla araştırma yapmanız gerekir. SNAT bağlantı noktası Tükenme bildirimleri, [giden akış](https://aka.ms/lboutbound)oluşturma hatası olarak. İş üzerindeki senaryoları ve mekanizmaları anlamak için giden bağlantılar hakkındaki makaleyi gözden geçirin ve SNAT bağlantı noktası tükenmesi olmaması için nasıl azaltacağınızı ve tasarlayacağınızı öğrenin. 

SNAT bağlantı istatistiklerini almak için:
1. **SNAT bağlantıları** ölçüm türünü ve toplama olarak **Sum** ' ı seçin. 
2. Farklı satırlarla temsil edilen başarılı ve başarısız SNAT bağlantı sayıları için **bağlantı durumuna** göre gruplandırın. 

![SNAT bağlantısı](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Şekil: Load Balancer SNAT bağlantı sayısı*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Hizmetme gelen/giden bağlantı girişimlerini kontrol Nasıl yaparım? mı?

Bir SYN paketleri ölçümü, belirli bir ön uçla ilişkili olan veya gönderilen ( [giden akışlar](https://aka.ms/lboutbound)IÇIN) TCP SYN paketlerinin hacmini açıklar. Bu ölçümü, hizmetinize yönelik TCP bağlantısı girişimlerini anlamak için kullanabilirsiniz.

Çoğu senaryo için toplama olarak **Toplam** ' i kullanın.

![SYN bağlantısı](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Şekil: Load Balancer SYN sayısı*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Nasıl yaparım? ağ bant genişliği tüketimi kontrol edilsin mi? 

Bayt ve paket sayaçları ölçümü, hizmetiniz tarafından ön uç başına gönderilen veya alınan bayt ve paketlerin hacmini açıklar.

Çoğu senaryo için toplama olarak **Toplam** ' i kullanın.

Byte veya Packet Count istatistiklerini almak için:
1. Toplam **bayt sayısını** ve/veya **paket sayısı** ölçüm türünü seçin. 
2. Aşağıdakilerden birini yapın:
   * Belirli bir ön uç IP, ön uç bağlantı noktası, arka uç IP veya arka uç bağlantı noktası üzerine filtre uygulayın.
   * Herhangi bir filtreleme yapmadan yük dengeleyici kaynağınızın genel istatistiklerini alın.

![Bayt sayısı](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Şekil: Load Balancer bayt sayısı*

#### <a name = "vipavailabilityandhealthprobes"></a>Yük dengeleyici dağıtımımı Tanıla Nasıl yaparım??

Tek bir grafik üzerinde VIP kullanılabilirliği ve durum araştırma ölçümlerinin bir birleşimini kullanarak, sorunun nerede arandığını tanımlayabilir ve sorunu çözebilirsiniz. Azure 'un doğru şekilde çalıştığını güvence altına alabilir ve yaratacağı için bu bilgiyi kullanarak yapılandırmanın veya uygulamanın kök nedenin olduğunu belirleyebilirsiniz.

Azure 'un dağıtımınızın sistem durumunu, verdiğiniz yapılandırma uyarınca nasıl görüntülemelerini anlamak için sistem durumu araştırma ölçümlerini kullanabilirsiniz. Sistem durumu araştırmalarının, her zaman izlemenin veya bir nedeni belirlemede harika bir ilk adımdır.

Bu adımları daha fazla alabilir ve VIP kullanılabilirlik ölçümlerini kullanarak Azure 'un, belirli dağıtımınızdan sorumlu olan temel alınan veri düzleminin sistem durumunu nasıl görebildiğiyle ilgili Öngörüler elde edebilirsiniz. Her iki ölçümü de birleştirdiğinizde, bu örnekte gösterildiği gibi hatanın nerede olabileceğini yalıtabilirsiniz:

![Veri yolu kullanılabilirliği ve durum araştırma durumu ölçümlerini birleştirme](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Şekil: veri yolu kullanılabilirliği ve durum araştırma durumu ölçümlerini birleştirme*

Grafik aşağıdaki bilgileri görüntüler:
- VM 'lerinizi barındıran altyapı, grafiğin başlangıcında kullanım dışı ve yüzde 0 ' dan fazla. Daha sonra, altyapı sağlıklı ve VM 'Ler erişilebilir ve arka uca birden fazla VM yerleştirildi. Bu bilgiler, daha sonra yüzde 100 ' de olan veri yolu kullanılabilirliği (VIP kullanılabilirliği) için mavi izleme tarafından gösterilir. 
- Mor izleme tarafından belirtilen durum araştırma durumu (DIP kullanılabilirliği), grafiğin başlangıcında yüzde 0 ' dır. Yeşil daire içinde, durum araştırma durumunun (DIP kullanılabilirliği) sağlıklı hale geldiğini ve müşterinin dağıtımının yeni akışları kabul edebildiği noktada vurgulanmıştır.

Grafik, müşterilerin, diğer sorunların oluşup oluşmadığını tahmin etmek veya destek istemek zorunda kalmadan, kendi üzerinde dağıtım sorunlarını gidermelerini sağlar. Yanlış yapılandırma veya hatalı uygulama nedeniyle sistem durumu araştırmaları başarısız olduğu için hizmet kullanılamıyor.

## <a name = "ResourceHealth"></a>Kaynak sistem durumu

Standart Load Balancer kaynaklarının sistem durumu, **izleme > hizmeti sistem durumu**altında mevcut **kaynak sistem durumu** aracılığıyla gösterilir.

Genel Standart Load Balancer kaynaklarınızın durumunu görüntülemek için:
1. **İzleme** > **hizmeti sistem durumunu**seçin.

   ![Sayfayı izle](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Şekil: Azure Izleyici 'de hizmet durumu bağlantısı*

2. **Kaynak durumu**' yi seçin ve ardından **abonelik kimliği** ve **kaynak türü = Load Balancer** ' nın seçildiğinden emin olun.

   ![Kaynak sistem durumu](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Şekil: sistem durumu görünümü için kaynak seçin*

3. Listede, geçmiş sistem durumunu görüntülemek için Load Balancer kaynağını seçin.

    ![Load Balancer sistem durumu](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Şekil: Load Balancer kaynak durumu görünümü*
 
Çeşitli kaynak sistem durumu durumları ve bunların açıklamaları aşağıdaki tabloda listelenmiştir: 

| Kaynak sistem durumu | Açıklama |
| --- | --- |
| Kullanılabilir | Standart yük dengeleyici kaynağınız sağlıklı ve kullanılabilir durumda. |
| Kullanılamaz | Standart yük dengeleyici kaynağınız sağlıklı değil. **Azure izleyici** > **ölçümleri**' ni seçerek sistem durumunu tanılayın.<br>(*Kullanılamayan* durum, kaynağın standart yük dengeleyicinizle bağlantılı olmadığı anlamına da gelebilir.) |
| Bilinmiyor | Standart yük dengeleyici kaynağınız için kaynak sistem durumu henüz güncelleştirilmemiş.<br>(*Bilinmeyen* durum da kaynağın standart yük dengeleyicinizle bağlantılı olmadığı anlamına gelebilir.)  |

## <a name="next-steps"></a>Sonraki adımlar

- [Standart Yük Dengeleyici](load-balancer-standard-overview.md) hakkında daha fazla bilgi edinin.
- [Yük dengeleyici giden bağlantınız](https://aka.ms/lboutbound)hakkında daha fazla bilgi edinin.
- [Azure izleyici](https://docs.microsoft.com/azure/azure-monitor/overview)hakkında bilgi edinin.
- [Azure izleyici REST API](https://docs.microsoft.com/rest/api/monitor/) ve [ölçümleri REST API aracılığıyla alma](/rest/api/monitor/metrics/list)hakkında bilgi edinin.
