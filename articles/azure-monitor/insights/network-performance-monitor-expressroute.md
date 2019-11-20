---
title: Azure Log Analytics Ağ Performansı İzleyicisi çözümü | Microsoft Docs
description: Azure ExpressRoute üzerinden şubeleriniz ve Azure arasındaki uçtan uca bağlantıyı ve performansı izlemek için Ağ Performansı İzleyicisi ExpressRoute Izleyici özelliğini kullanın.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 5383402816eddba4c631c240585723b7c7119cef
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898891"
---
# <a name="expressroute-monitor"></a>ExpressRoute Izleyicisi

Azure ExpressRoute üzerinden, şube ofisleriniz ve Azure arasındaki uçtan uca bağlantıyı ve performansı izlemek için [ağ performansı İzleyicisi](network-performance-monitor.md) Azure ExpressRoute izleyici özelliğini kullanabilirsiniz. Temel avantajlar şunlardır: 

- Aboneliğinizle ilişkili ExpressRoute devreleri için oto algılama.
- ExpressRoute için devre, eşleme ve Azure sanal ağ düzeyinde bant genişliği kullanımını, kaybı ve gecikme süresini izleme.
- ExpressRoute Devrelerinizin ağ topolojisini bulma.

![ExpressRoute Izleyicisi](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Yapılandırma 
Ağ Performansı İzleyicisi yapılandırmasını açmak için [ağ performansı İzleyicisi çözümünü](network-performance-monitor.md) açın ve **Yapılandır**' ı seçin.

### <a name="configure-network-security-group-rules"></a>Ağ güvenlik grubu kurallarını yapılandırma 
Azure 'daki Ağ Performansı İzleyicisi aracılığıyla izleme için kullanılan sunucular için, yapay işlemler için Ağ Performansı İzleyicisi tarafından kullanılan bağlantı noktasında TCP trafiğine izin vermek üzere ağ güvenlik grubu (NSG) kurallarını yapılandırın. Varsayılan bağlantı noktası 8084 ' dir. Bu yapılandırma, Azure VM 'lerinde yüklü Log Analytics aracısının şirket içi izleme aracısıyla iletişim kurmasına izin verir. 

NSG 'ler hakkında daha fazla bilgi için bkz. [ağ güvenlik grupları](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Bu adımla devam etmeden önce şirket içi sunucu aracısını ve Azure sunucu aracısını yükleyip EnableRules. ps1 PowerShell betiğini çalıştırın. 

 
### <a name="discover-expressroute-peering-connections"></a>ExpressRoute eşleme bağlantılarını bul 
 
1. **ExpressRoute eş** görünümü görünümünü seçin.
2. Bu Azure Log Analytics çalışma alanıyla bağlantılı Azure aboneliğindeki sanal ağlara bağlı olan tüm ExpressRoute özel eşayarlarını öğrenmek için **Şimdi bul** ' u seçin.

    >[!NOTE]
    > Çözüm şu anda yalnızca ExpressRoute özel eşayarlarını bulur. 

    >[!NOTE]
    > Yalnızca bu Log Analytics çalışma alanıyla bağlantılı abonelikle ilişkili sanal ağlara bağlı özel eşlemeler bulunur. ExpressRoute, bu çalışma alanına bağlı aboneliğin dışındaki sanal ağlara bağlıysa, bu aboneliklerde bir Log Analytics çalışma alanı oluşturun. Sonra bu eşlemeleri izlemek için Ağ Performansı İzleyicisi kullanın. 

    ![ExpressRoute Izleyicisi yapılandırması](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   Bulma işlemi tamamlandıktan sonra bulunan özel eşleme bağlantıları bir tabloda listelenir. Bu eşler için izleme başlangıçta devre dışı durumda. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>ExpressRoute eşleme bağlantılarının izlenmesini etkinleştir 

1. İzlemek istediğiniz özel eşleme bağlantısını seçin.
2. Sağdaki bölmede **Bu eşlemeyi izle** onay kutusunu seçin. 
3. Bu bağlantı için sistem durumu olayları oluşturmak istiyorsanız **Bu eşleme Için sistem durumu Izlemeyi etkinleştir**' i seçin. 
4. İzleme koşullarını seçin. Eşik değerlerini girerek sistem durumu olayı oluşturma için özel eşikler ayarlayabilirsiniz. Koşulun değeri, eşleme bağlantısı için seçili eşiğin üstünde olduğunda bir sistem durumu olayı oluşturulur. 
5. Bu eşleme bağlantısını izlemek için kullanmayı düşündüğünüz izleme aracılarını seçmek için **aracıları Ekle** ' yi seçin. Bağlantının her iki ucunun de aracılarını eklediğinizden emin olun. Bu eşlemeye bağlı sanal ağda en az bir aracı olması gerekir. Ayrıca, bu eşlemeye bağlı en az bir şirket içi aracının olması gerekir. 
6. Yapılandırmayı kaydetmek için **Kaydet** ' i seçin. 

   ![ExpressRoute izleme yapılandırması](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Kuralları etkinleştirdikten ve değerleri ve aracıları seçtikten sonra, değerlerin doldurulması ve **ExpressRoute izleme** kutucuklarının görünmesi için 30 ila 60 dakika bekleyin. İzleme kutucuklarını gördüğünüzde, ExpressRoute devrelerinizi ve bağlantı kaynaklarınızı artık Ağ Performansı İzleyicisi tarafından izlenir. 

>[!NOTE]
> Bu özellik, yeni sorgu diline yükseltilen çalışma alanları üzerinde güvenilir bir şekilde çalışabilir.

## <a name="walkthrough"></a>Kılavuz 

Ağ Performansı İzleyicisi panosu, ExpressRoute devreleri ve eşleme bağlantılarının sistem durumuna genel bir bakış gösterir. 

![Ağ Performansı İzleyicisi panosu](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Devreler listesi 

Tüm izlenen ExpressRoute devrelerinin listesini görmek için ExpressRoute devreleri kutucuğunu seçin. Bir devre seçebilir ve sistem durumunu, paket kaybı için eğilim grafiklerini, bant genişliği kullanımını ve gecikmeyi görüntüleyebilirsiniz. Grafikler etkileşimlidir. Grafikleri çizmek için özel bir zaman penceresi seçebilirsiniz. Farenizi yakınlaştırmak için grafikteki bir alanın üzerine sürükleyin ve hassas veri noktalarına bakın. 

![ExpressRoute devreleri listesi](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Kayıp, gecikme süresi ve aktarım hızı eğilimleri 

Bant genişliği kullanımı, gecikme süresi ve kayıp grafikleri etkileşimlidir. Fare denetimlerini kullanarak, bu grafiklerin herhangi bir bölümüne yakınlaştırabilirsiniz. Ayrıca, diğer aralıkların bant genişliği, gecikme süresi ve kayıp verilerini de görebilirsiniz. **Eylemler** düğmesinin altındaki sol üst köşedeki **Tarih/saat**' i seçin. 

![ExpressRoute gecikmesi](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Eşleme listesi 

Sanal ağlara yönelik tüm bağlantıların bir listesini özel eşleme üzerinden getirmek için panoda **özel eş** oluşturma kutucuğunu seçin. Burada, bir sanal ağ bağlantısı seçebilir ve sistem durumunu, paket kaybı için eğilim grafiklerini, bant genişliği kullanımını ve gecikme süresini görüntüleyebilirsiniz. 

![ExpressRoute eşlemeleri](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Devre topolojisi 

Devre topolojisini görüntülemek için **topoloji** kutucuğunu seçin. Bu eylem sizi seçili Devre veya eşlemenin topoloji görünümüne götürür. Topoloji diyagramı, ağdaki her segment için gecikme süresi sağlar ve her katman 3 atlama, diyagramın bir düğümü ile temsil edilir. Bir atlama seçildiğinde atlama hakkında daha fazla ayrıntı ortaya çıkarlar. Şirket içi atlamaları dahil etmek için görünürlük düzeyini artırmak üzere kaydırıcı çubuğunu **Filtreler**altına taşıyın. Kaydırıcı çubuğunu sola veya sağa taşımak, topoloji grafiğindeki atlama sayısını artırır veya azaltır. Her bir kesimde gecikme süresi görünür ve bu, ağınızda yüksek gecikmeli kesimleri daha hızlı yalıtımına olanak tanır.

![ExpressRoute topolojisi](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Devrenin ayrıntılı topoloji görünümü 

Bu görünüm, sanal ağ bağlantılarını gösterir. 

![ExpressRoute sanal ağ bağlantıları](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Tanılama 

Ağ Performansı İzleyicisi, birkaç devre bağlantı sorununu tanılamanıza yardımcı olur. Görebileceğiniz bazı sorunlar aşağıda listelenmiştir.

Bildirim kodlarını görebilir ve **günlüğe Loganalytics**aracılığıyla uyarılar ayarlayabilirsiniz. **NPM tanılama** sayfasında, tetiklenen her tanılama iletisi için açıklamaları görebilirsiniz.

| Bildirim kodu (Günlükler) | Açıklama |
| --- | --- |
| 5501 | ExpressRoute devresinin ikincil bağlantısı üzerinden geçiş yapılamıyor |
| 5502 | ExpressRoute devresinin birincil bağlantısı üzerinden geçiş yapılamıyor |
| 5503 | Çalışma alanına bağlı abonelik için devre bulunmadı | 
| 5508 | Trafiğin yol için herhangi bir devreden geçirilip geçirilmediğini belirleme |
| 5510 | Trafik, hedeflenen devre üzerinden geçirilmiyor | 
| 5511 | Trafik, hedeflenen sanal ağ üzerinden geçirilmiyor | 

**Devre dışı.** Ağ Performansı İzleyicisi, şirket içi kaynaklarınız ve Azure sanal ağlarınız arasındaki bağlantı kaybolduğu anda sizi uyarır. Bu bildirim, Kullanıcı ilerleme almadan ve kapalı kalma süresini azaltmadan önce proaktif işlem yapmanıza yardımcı olur.

![ExpressRoute bağlantı hattı devre dışı](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**Trafik amaçlanan bağlantı hattı üzerinden akar.** Ağ Performansı İzleyicisi, trafiği amaçlanan ExpressRoute bağlantı hattı üzerinden akan her durumda bilgilendirir. Bu sorun, devre dışı bırakılırsa ve trafik yedekleme yolu üzerinden akar meydana gelir. Ayrıca, bir yönlendirme sorunu olduğunda da gerçekleşebilir. Bu bilgiler, yönlendirme ilkelerinizin yapılandırma sorunlarını önceden yönetmenize ve en uygun ve güvenli yolun kullanıldığından emin olmanıza yardımcı olur. 

 

**Birincil bağlantı hattı üzerinden akan trafik.** Ağ Performansı İzleyicisi, trafiğin ikincil ExpressRoute bağlantı hattı üzerinden akışı yapıldığında size bildirir. Bu durumda herhangi bir bağlantı sorunlarıyla karşılaşmayasanız da, birincil bağlantı sorunlarını giderme konusunda sorunları daha iyi hazırlamanızı sağlar. 

 
![ExpressRoute trafik akışı](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Yoğun kullanım nedeniyle azalma.** Bant genişliği kullanımı eğilimini gecikme eğilimi ile ilişkilendirip, Azure iş yükü düşüşünün bant genişliği kullanımının en yoğun olup olmadığını belirleyebilirsiniz. Daha sonra eyleme uygun bir işlem yapabilirsiniz.

![ExpressRoute bant genişliği kullanımı](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Sonraki adımlar
Ayrıntılı ağ performansı veri kayıtlarını görüntülemek için [günlüklerde arama](../../azure-monitor/log-query/log-query-overview.md) yapın.
