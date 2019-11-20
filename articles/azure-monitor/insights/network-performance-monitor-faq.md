---
title: SSS-Azure 'da Ağ Performansı İzleyicisi çözüm | Microsoft Docs
description: Bu makale, Azure 'daki Ağ Performansı İzleyicisi hakkında sık sorulan soruları yakalar. Ağ Performansı İzleyicisi (NPM), ağların performansını neredeyse gerçek zamanlı olarak izlemenize ve ağ performansı performans sorunlarını saptamanıza ve bulmanıza yardımcı olur.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: ce0b917f34cab31227e721e119c72cd5d1f99bff
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832010"
---
# <a name="network-performance-monitor-solution-faq"></a>Ağ Performansı İzleyicisi çözümü SSS

![Ağ Performansı İzleyicisi simgesi](media/network-performance-monitor-faq/npm-symbol.png)

Bu makale, Azure 'da Ağ Performansı İzleyicisi (NPM) hakkında sık sorulan soruları (SSS) yakalar

[Ağ performansı İzleyicisi](/azure/networking/network-monitoring-overview) , ağ altyapınızın çeşitli noktaları arasındaki ağ performansını izlemenize yardımcı olan bulut tabanlı bir [karma ağ izleme](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md) çözümüdür. Ayrıca [hizmet ve uygulama uç noktalarına](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md) ağ bağlantısını Izlemenize ve [Azure ExpressRoute 'un performansını izlemenize](../../azure-monitor/insights/network-performance-monitor-expressroute.md)yardımcı olur. 

Ağ Performansı İzleyicisi trafik görüntüleme, yönlendirme hataları ve geleneksel ağ izleme yöntemlerinin algılayamadığı sorunlar gibi ağ sorunlarını algılar. Bir ağ bağlantısı için bir eşik ihlal edildiğinde, çözüm uyarılar oluşturur ve size bildirir. Ayrıca ağ performans sorunlarının zamanında tespit edilmesini sağlar ve sorunun kaynağını belirli bir ağ kesimine veya cihazına yerelleştirir. 

[Ağ performansı İzleyicisi](https://docs.microsoft.com/azure/networking/network-monitoring-overview) tarafından desteklenen çeşitli yetenekler hakkında daha fazla bilgi çevrimiçi olarak sunulmaktadır.

## <a name="set-up-and-configure-agents"></a>Aracıları ayarlama ve yapılandırma

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>NPM tarafından izleme için kullanılacak düğümlerin Platform gereksinimleri nelerdir?
Aşağıda, NPM 'nin çeşitli özellikleri için Platform gereksinimleri verilmiştir:

- NPM 'nin performans Izleyicisi ve hizmet bağlantısı Izleyicisi özellikleri hem Windows Server hem de Windows masaüstleri/istemci işletim sistemlerini destekler. Desteklenen Windows Server işletim sistemi sürümleri 2008 SP1 veya sonraki bir sürümü. Desteklenen Windows masaüstleri/istemci sürümleri Windows 10, Windows 8.1, Windows 8 ve Windows 7 ' dir. 
- NPM 'nin ExpressRoute Izleyici özelliği yalnızca Windows Server (2008 SP1 veya üzeri) işletim sistemini destekler.

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Linux makinelerini NPM 'de izleme düğümleri olarak kullanabilir miyim?
Linux tabanlı düğümleri kullanarak ağları izleme özelliği şu anda önizlemededir. Daha fazla bilgi edinmek için hesap yöneticinize ulaşın. Linux Agents yalnızca NPM 'nin performans Izleyicisi özelliği için izleme özelliği sağlar ve hizmet bağlantısı Izleyicisi ile ExpressRoute Izleyici özellikleri için kullanılamaz

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>NPM tarafından izleme için kullanılacak düğümlerin boyut gereksinimleri nelerdir?
Ağları izlemek üzere düğüm VM 'lerinde NPM çözümünü çalıştırmak için düğümlerin en az 500 MB ve bir çekirdeğe sahip olması gerekir. NPM 'yi çalıştırmak için ayrı düğümler kullanmanız gerekmez. Çözüm, üzerinde çalışan diğer iş yükleri olan düğümlerde çalıştırılabilir. Çözüm, %5 ' ten fazla CPU kullanıyorsa izleme işlemini durdurma özelliğine sahiptir.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>NPM 'yi kullanmak için, düğümlerimi doğrudan aracı olarak veya System Center Operations Manager aracılığıyla bağlamanız gerekir mi?
Hem performans Izleyicisi hem de hizmet bağlantısı Izleyicisi özellikleri [doğrudan aracılar olarak bağlanan](../../azure-monitor/platform/agent-windows.md) ve [Operations Manager aracılığıyla bağlanan](../../azure-monitor/platform/om-agents.md)düğümleri destekler.

ExpressRoute Izleyici özelliği için Azure düğümlerinin yalnızca doğrudan aracı olarak bağlanması gerekir. Operations Manager ile bağlantılı Azure düğümleri desteklenmez. Şirket içi düğümlerde, bir ExpressRoute devresini izlemek için doğrudan aracılar ve Operations Manager aracılığıyla bağlanan düğümler desteklenir.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>İzleme için TCP ve ıCMP arasındaki protokol seçilmelidir mi?
Ağınızı Windows Server tabanlı düğümleri kullanarak izliyorsanız, daha iyi doğruluk sağladığından izleme protokolü olarak TCP kullanmanızı öneririz. 

Windows masaüstleri/istemci işletim sistemi tabanlı düğümler için ıCMP önerilir. Bu platform, TCP verilerinin, NPM 'nin ağ topolojisini bulması için kullandığı ham yuvalar üzerinden gönderilmesine izin vermez.

[Burada](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol)her bir protokolün göreli avantajları hakkında daha fazla bilgi edinebilirsiniz.

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Bir düğümü TCP protokolünü kullanarak izlemeyi destekleyecek şekilde nasıl yapılandırabilirim?
Düğümün TCP protokolünü kullanarak izlemeyi desteklemesi için: 
* Düğüm platformunun Windows Server (2008 SP1 veya üzeri) olduğundan emin olun.
* Düğümde [Enablerules. ps1](https://aka.ms/npmpowershellscript) PowerShell betiğini çalıştırın. Daha fazla ayrıntı için [yönergelere](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) bakın.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>NPM tarafından izleme için kullanılan TCP bağlantı noktasını nasıl değiştirebilirim?
İzleme için, [Enablerules. ps1](https://aka.ms/npmpowershellscript) betiğini çalıştırarak, NPM tarafından kullanılan TCP bağlantı noktasını değiştirebilirsiniz. Parametre olarak kullanmayı düşündüğünüz bağlantı noktası numarasını girmeniz gerekir. Örneğin, 8060 numaralı bağlantı noktasında TCP 'yi etkinleştirmek için `EnableRules.ps1 8060`çalıştırın. İzleme için kullanılan tüm düğümlerde aynı TCP bağlantı noktasını kullandığınızdan emin olun.

Betik yalnızca Windows güvenlik duvarını yerel olarak yapılandırır. Ağ güvenlik duvarı veya ağ güvenlik grubu (NSG) kurallarınız varsa, NPM tarafından kullanılan TCP bağlantı noktasına giden trafiğe izin verdiklerinden emin olun.

### <a name="how-many-agents-should-i-use"></a>Kaç aracı kullanmalıyım?
İzlemek istediğiniz her alt ağ için en az bir aracı kullanmanız gerekir.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Kullanılabilecek en fazla aracı sayısı nedir veya hata alıyorum ".... Yapılandırma sınırınıza ulaştınız "?
NPM, her çalışma alanı için IP sayısını 5000 IP 'ye sınırlandırır. Bir düğümde hem IPv4 hem de IPv6 adresi varsa, bu düğüm için 2 IP olarak sayılır. Bu nedenle, 5000 IP 'nin bu sınırı, aracı sayısının üst sınırına karar verebilir. > > NPM 'deki düğümler sekmesinden etkin olmayan aracıları silebilirsiniz. NPM Ayrıca aracıyı barındıran VM 'ye atanan tüm IP 'lerin geçmişini tutar ve her biri 5000 IP 'nin bu üst sınırına katkıda bulunan ayrı IP olarak sayılır. Çalışma alanınız için IP 'Leri boşaltmak için düğümler sayfasını kullanarak kullanımda olmayan IP 'Leri silebilirsiniz.

## <a name="monitoring"></a>İzleme

### <a name="how-are-loss-and-latency-calculated"></a>Kayıp ve gecikme nasıl hesaplanır
Kaynak aracıları, kaynak-hedef IP arasındaki tüm yolların emin olmak için düzenli aralıklarla hedef IP 'ye TCP SYN istekleri (izleme için protokol olarak seçilirse) ya da ıCMP YANKı isteklerini (izleme protokolü olarak seçilirse) gönderir. birleşim ele alınmıştır. Alınan paketlerin ve paket gidiş dönüş süresinin yüzdesi, her yolun kaybını ve gecikme süresini hesaplamak için ölçülür. Bu veriler, belirli bir yoklama aralığı için IP birleşimi için, kayıp ve gecikme süresinin toplam değerlerini almak üzere yoklama aralığı ve tüm yollar üzerinden toplanır.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Kaynak aracısının, izleme için paketleri hedefe ne sıklıkta gönderdikleridir?
Performans Izleyicisi ve ExpressRoute Izleyici özellikleri için, kaynak paketleri her 5 saniyede bir gönderir ve ağ ölçümlerini kaydeder. Bu veriler, kayıp ve gecikme süresinin ortalama ve en yüksek değerlerini hesaplamak için 3 dakikalık bir yoklama aralığı içinde toplanır. Hizmet bağlantısı Izleyici özelliği için, ağ ölçümü için paketlerin gönderilmesi sıklığı, testi yapılandırırken belirli bir test için Kullanıcı tarafından girilen sıklık tarafından belirlenir.

### <a name="how-many-packets-are-sent-for-monitoring"></a>İzleme için kaç paket gönderildi?
Kaynak aracısının bir yoklamada hedefe gönderdiği paketlerin sayısı Uyarlamalı olur ve farklı ağ topolojileri için farklı olabilen özel algoritmamız tarafından kararlanır. Kaynak-hedef IP birleşimi arasındaki ağ yollarının sayısı daha fazla, gönderilen paketlerin sayısıdır. Sistem, kaynak-hedef IP birleşimi arasındaki tüm yolların kapsanmasını sağlar.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>NPM, kaynak ve hedef arasındaki ağ topolojisini nasıl bulur?
NPM, kaynak ve hedef arasındaki tüm yolları ve atlamaları bulmaya yönelik bir özel algoritma kullanır.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>NPM, Yönlendirme ve anahtarlama düzeyi bilgisi sağlar 
NPM, kaynak Aracısı ve hedef arasındaki olası tüm yolları algılayabilse de, belirli iş yükleriniz tarafından gönderilen paketler tarafından hangi yolun alındığını tespit etmez. Çözüm, beklediğinizden daha fazla gecikme süresi ekleyen yolları ve temel alınan ağ atlamalarını belirlemenize yardımcı olabilir.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Yolların bir kısmı neden sağlıksız?
Kaynak ve hedef IP 'Ler arasında farklı ağ yolları olabilir ve her yol farklı bir kayıp ve gecikme süresine sahip olabilir. NPM, kayıp ve/veya gecikme değerlerinin, izleme yapılandırmasındaki ilgili eşikten büyük olduğu, bu yolları sağlıksız (kırmızı renkle gösterilir) olarak işaretler.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Kırmızı renkte atlama, ağ topolojisi eşlemesinde işaret ediyor mu?
Bir atlama kırmızıysa, en az bir sağlıksız yolun parçası olduğunu belirtir. NPM, yolları yalnızca sağlıksız olarak işaretlediğinde, her yolun sistem durumunu ayırt etmez. Sorunlu atlamalarını belirlemek için, atlama gecikmesini ve beklenen gecikme süresini daha fazla ekleyerek ayırt edebilirsiniz.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Performans Izleyicisinde hata yerelleştirmesi nasıl çalışır?
NPM her ağ yoluna, ağ kesimine ve bileşen ağ atlıklarına, bir parçası oldukları sağlıksız yolların sayısına göre hata olasılıkların atanmasını sağlamak için bir dayalı mekanizması kullanır. Ağ kesimleri ve atlamaları daha fazla sağlıksız yolun parçası haline geldiği için, bunlarla ilişkili hata olasılığı artar. Bu algoritma en iyi şekilde çalışarak, hata olasılıkların hesaplanması için veri noktalarını arttığı için NPM Aracısı birbirlerine bağlı olan çok sayıda düğümünüz olduğunda bu algoritma en iyi şekilde çalışacaktır.

### <a name="how-can-i-create-alerts-in-npm"></a>NPM 'de nasıl uyarı oluşturabilirim?
Adım adım yönergeler için [belgelerde Uyarılar bölümüne](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts) bakın.

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>Uyarılar için varsayılan Log Analytics sorguları nelerdir?
Performans İzleyicisi sorgusu

    NetworkMonitoring 
     | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
    
Hizmet bağlantısı İzleyicisi sorgusu

    NetworkMonitoring                 
     | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
    
ExpressRoute İzleyicisi sorguları: devreler sorgusu

    NetworkMonitoring
    | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"

Özel eşleme

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"

Microsoft eşlemesi

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"

Ortak sorgu   

    NetworkMonitoring
    | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") 

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Yönlendiriciler ve sunucuları tek tek cihazlar olarak izleyebilir miyim?
NPM yalnızca kaynak ve hedef IP 'Ler arasındaki temel ağ atlamalarının (anahtarlar, yönlendiriciler, sunucular vb.) IP ve ana bilgisayar adını tanımlar. Bu, tanımlanan atlamaları arasındaki gecikmeyi de belirler. Bu temel atlamaları tek tek izlemez.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>Azure ile AWS arasındaki ağ bağlantısını izlemek için NPM kullanılabilir mi?
Evet. Ayrıntılar için [NPM kullanarak Azure, AWS ve şirket içi ağları izleme](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/) makalesine bakın.

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>ExpressRoute bant genişliği kullanımı gelen veya giden
Bant genişliği kullanımı, gelen ve giden bant genişliğinin toplamıdır. Bit/sn cinsinden ifade edilir.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>ExpressRoute için gelen ve giden bant genişliği bilgilerini alabilir miyim?
Hem birincil hem de Ikincil bant genişliği için gelen ve giden değerler yakalanabilir.

MS eşleme düzeyi bilgileri için, günlük aramasında aşağıdaki belirtilen sorguyu kullanın

    NetworkMonitoring 
     | where SubType == "ERMSPeeringUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
    
Özel eşleme düzeyi bilgileri için, günlük aramasında aşağıdaki belirtilen sorguyu kullanın

    NetworkMonitoring 
     | where SubType == "ERVNetConnectionUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
Devre düzeyi bilgileri için, günlük aramasında aşağıdaki belirtilen sorguyu kullanın

    NetworkMonitoring 
        | where SubType == "ERCircuitTotalUtilization"
        | project CircuitName, PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>NPM 'nin performans Izleyicisi için hangi bölgeler desteklenir?
NPM, dünyanın herhangi bir bölümündeki ağlar arasındaki bağlantıyı, [desteklenen bölgelerden](../../azure-monitor/insights/network-performance-monitor.md#supported-regions) birinde barındırılan bir çalışma alanından izleyebilir

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>NPM 'nin hizmet bağlantısı Izleyicisi için hangi bölgeler desteklenir?
NPM, [desteklenen bölgelerden](../../azure-monitor/insights/network-performance-monitor.md#supported-regions) birinde barındırılan bir çalışma alanından dünyanın herhangi bir bölümündeki hizmetlere bağlantıyı izleyebilir

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>NPM 'nin ExpressRoute Izleyicisi için hangi bölgeler desteklenir?
NPM, herhangi bir Azure bölgesinde bulunan ExpressRoute devrelerinizi izleyebilir. NPM 'ye eklemek için [desteklenen bölgelerden](/azure/expressroute/how-to-npm) birinde barındırılması gereken bir Log Analytics çalışma alanı gerekir

## <a name="troubleshoot"></a>Sorun giderme

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Bazı atlamalarda ağ topolojisi görünümünde tanımlanamayan olarak işaretlenmiş mı?
NPM, kaynak aracıdan hedefe yönelik topolojiyi bulacak bir izleme yolu değiştirme sürümü kullanıyor. Tanımlanamayan bir atlama, ağ atlamanın kaynak aracısının izleme yolu isteğine yanıt vermediği temsil eder. Art arda üç ağ atlaması aracının izleme Oute öğesine yanıt vermiyorsa, çözüm yanıt vermeyen atlamaları tanımlanamayan olarak işaretler ve daha fazla atlama bulmayı denemez.

Bir atlama aşağıdaki senaryolardan birinde veya daha fazla bir izleme Oute 'e yanıt vermeyebilir:

* Yönlendiriciler kimliğini açığa çıkarmayan bir şekilde yapılandırılmıştır.
* Ağ cihazları ICMP_TTL_EXCEEDED trafiğe izin vermiyor.
* Bir güvenlik duvarı, ağ cihazından gelen ICMP_TTL_EXCEEDED yanıtını engelliyor.

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>Sağlıksız testler için uyarı alıyorum, ancak NPM 'nin kayıp ve gecikme grafiğinde yüksek değerleri görmüyorum. Sağlıksız olup olmadığını kontrol Nasıl yaparım? mı?
Kaynak ve hedef arasındaki uçtan uca gecikme süresi, aralarında herhangi bir yol için eşikten kesişirse NPM bir uyarı oluşturur. Bazı ağların aynı kaynak ve hedefi bağlayan birden çok yolu vardır. NPM bir uyarı harekete geçirirse herhangi bir yol sağlıksız olur. Grafiklerde görülen kayıp ve gecikme, tüm yolların ortalama değeridir, bu nedenle tek bir yolun tam değerini gösteremeyebilir. Eşiğin ihlal edildiği yeri anlamak için, uyarıdaki "alt tür" sütununu arayın. Sorun bir yol nedeniyle gerçekleşirse, alt tür değeri NetworkPath olacaktır (performans Izleme testleri için), EndpointPath (hizmet bağlantısı Izleyicisi testleri için) ve ExpressRoutePath (ExpressRotue Izleyici testleri için). 

Yolu bulmak için örnek sorgu uygun değil:

    NetworkMonitoring 
    | where ( SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and          CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
    | project SubType, LossHealthState, LatencyHealthState, MedianLatency 

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Test neden sağlıksız bir şekilde gösteriliyor, ancak topoloji değil 
NPM, uçtan uca kaybı, gecikme süresi ve topolojiyi farklı aralıklarla izler. Kayıp ve gecikme süresi, her 5 saniyede bir ölçülerek her üç dakikada bir (performans Izleyicisi ve hızlı rota Izleyicisi için) toplanır. Bu topoloji her 10 dakikada bir Izleme Örneğin, 3:44 ve 4:04 arasındaki topoloji üç kez (3:44, 3:54, 4:04) güncelleştirilemeyebilir, ancak kayıp ve gecikme süresi yedi kez güncelleştirilir (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). 3:54 konumunda oluşturulan topoloji, 3:56, 3:59 ve 4:02 ' de hesaplanan kayıp ve gecikme süresi boyunca işlenir. ER devrenizi 3:59 ' de sağlıksız olduğunu bildiren bir uyarı aldığınızı varsayalım. NPM 'de oturum açıp topoloji süresini 3:59 olarak ayarlamaya çalışırsınız. NPM 3:54 konumunda oluşturulan topolojiyi işleyecek. Ağınızın son bilinen topolojisini anlamak için, Timeişlenmiş alanları (kayıp ve gecikme süresinin hesaplandığı süre) ve izleme (topolojinin hesaplandığı süre) ile karşılaştırın. 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>NetworkMonitoring tablosundaki E2EMedianLatency ve AvgHopLatencyList alanları arasındaki fark nedir?
E2EMedianLatency, TCP ping testlerinin sonuçları alındıktan sonra her üç dakikada bir güncelleştirilir, ancak AvgHopLatencyList, izleme Oute temelinde her 10 dakikada bir güncelleştirilir. E2EMedianLatency hesaplanma süresini tam olarak anlamak için, Timeişlenen alanını kullanın. İzleme/kapatma AvgHopLatencyList ve güncelleştirilmiş zamanı tam olarak anlamak için, izleme alanı ' nı kullanın.

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Atlama gecikmesi sayıları neden, Hoplatnby değerlerinden farklı 
Hoplatşifreli değerler bitiş noktası kaynağıdır.
Örneğin: atlamaları-A, B, C. AvgHopLatency-10, 15, 20. Bu, kaynağın bir gecikme süresi = 10, kaynak-B gecikme süresi = 15 ve kaynak-C gecikme süresinin 20 olduğu anlamına gelir. Kullanıcı arabirimi, topolojide 5 olarak A-B atlama gecikmesini hesaplar

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>Çözüm %100 kayıp olduğunu ancak kaynak ile hedef arasında bağlantı olduğunu gösteriyor
Bu durum, ana bilgisayar güvenlik duvarı veya ara güvenlik duvarı (ağ güvenlik duvarı ya da Azure NSG), kaynak Aracısı ve NPM tarafından izleme için kullanılan bağlantı noktası üzerinden hedef arasındaki iletişimi engelliyorsa (varsayılan olarak, bağlantı noktası 8084, yoksa, Müşteri bunu değiştirdi).

* Ana bilgisayar güvenlik duvarının gerekli bağlantı noktasındaki iletişimi engellemediğinden emin olmak için şu görünümden kaynak ve hedef düğümlerin sistem durumunu görüntüleyin: Ağ Performansı İzleyicisi-> Yapılandırma-> düğümleri. 
  Sağlıksız olmaları durumunda yönergeleri görüntüleyin ve düzeltici eylem gerçekleştirin. Düğümler sağlıklı ise, b adımına geçin. öğrenin.
* Bir ara ağ güvenlik duvarının veya Azure NSG 'nin gerekli bağlantı noktasındaki iletişimi engellemediğinden emin olmak için aşağıdaki yönergeleri kullanarak üçüncü taraf PsPing yardımcı programını kullanın:
  * psping yardımcı programı [buradan](https://technet.microsoft.com/sysinternals/psping.aspx) indirilebilir 
  * Kaynak düğümden aşağıdaki komutu çalıştırın.
    * psping-n 15 \<hedef düğüm IPAddress:p\>, varsayılan NPM 8084 bağlantı noktasını kullanır. Bunu, EnableRules. ps1 betiğini kullanarak açıkça değiştirdiyseniz, kullanmakta olduğunuz özel bağlantı noktası numarasını girin. Bu, Azure makinesinden şirket içine bir ping işlemi sağlar
* Pingler 'in başarılı olup olmadığını denetleyin. Aksi takdirde, bir ara ağ güvenlik duvarının veya Azure NSG 'nin Bu bağlantı noktasındaki trafiği engellediğini gösterir.
* Şimdi, komutu hedef düğümden kaynak düğüm IP 'si olarak çalıştırın.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>A düğümünden B 'ye kadar bir kayıp vardır ancak B düğümünden bir arasında değildir. Kaydol?
A ile B arasındaki ağ yolları B ile arasında ağ yollarından farklı olabilir, kayıp ve gecikme süresinin farklı değerleri gözlemlenebilir.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Tüm ExpressRoute devreleri ve eşleme bağlantılarım neden bulunamadı?
NPM artık kullanıcının erişimi olan tüm aboneliklerde ExpressRoute devreleri ve eşleme bağlantılarını bulur. Hızlı rota kaynaklarınızın bağlandığı tüm abonelikleri seçin ve bulunan her kaynak için izlemeyi etkinleştirin. NPM özel bir eşleme bulunurken bağlantı nesnelerine bakar, bu nedenle lütfen bir VNET 'in eşlemeden ilişkilendirildiğinden emin olun.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>ER Monitor özelliğinin bir tanılama iletisi vardır "trafik HERHANGI bir devrele geçirilmiyor". Bu ne demektir?

Şirket içi ve Azure düğümleri arasında sağlıklı bir bağlantının olduğu, ancak trafiğin NPM tarafından izlenmesi için yapılandırılmış ExpressRoute bağlantı hattı üzerinden gitmediği bir senaryo olabilir. 

Bu durum şu durumlarda oluşabilir:

* ER devresi devre dışı.
* Yol filtreleri, istenen ExpressRoute bağlantı hattı üzerinden diğer yollara (bir VPN bağlantısı veya başka bir ExpressRoute bağlantı hattı gibi) öncelik vertikleri şekilde yapılandırılır. 
* İzleme yapılandırmasında ExpressRoute devresini izlemek için seçilen Şirket içi ve Azure düğümleri, hedeflenen ExpressRoute bağlantı hattı üzerinden birbirleriyle bağlantıya sahip değildir. İzlemek istediğiniz ExpressRoute bağlantı hattı üzerinden birbirlerine bağlantısı olan doğru düğümleri seçtiğinizden emin olun.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>ExpressRoute bağlantı hattını izleme yapılandırılırken Azure düğümleri algılanmaz.
Azure düğümleri Operations Manager aracılığıyla bağlandığında bu durum oluşabilir. ExpressRoute Izleyici özelliği, yalnızca doğrudan aracılar olarak bağlı olan Azure düğümlerini destekler.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>OMS portalında ExpressRoute devrelerine göre Keşfedemiyorum
NPM hem Azure portal hem de OMS portalından kullanılabilir, ancak ExpressRoute Izleyici özelliği içindeki devre bulma işlemi yalnızca Azure portal üzerinden çalışabilir. Devreler Azure portal aracılığıyla keşfedildiğinde, özelliği iki portaldan birinde kullanabilirsiniz. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>Hizmet bağlantısı Izleyicisi özelliği içinde hizmet yanıt süresi, ağ kaybı ve gecikme süresi de yok olarak gösterilir
Bir veya daha fazla doğruysa bu durum oluşabilir:

* Hizmet çalışmıyor.
* Hizmete ağ bağlantısını denetlemek için kullanılan düğüm çalışmıyor.
* Test yapılandırmasına girilen hedef yanlış.
* Düğümün hiçbir ağ bağlantısı yok.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>Hizmet bağlantısı Izleyicisi özelliği 'nde geçerli bir hizmet yanıt süresi gösterilir, ancak gecikme süresi de ağ kaybı olarak gösterilir
 Bir veya daha fazla doğruysa bu durum oluşabilir:

* Hizmete ağ bağlantısını denetlemek için kullanılan düğüm bir Windows istemci makinese, hedef hizmet ıCMP isteklerini engelliyor veya bir ağ güvenlik duvarı düğümden kaynaklanan ıCMP isteklerini engelliyor.
* Ağ ölçümlerini Uygula onay kutusu, test yapılandırmasında boştur.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>Hizmet bağlantısı Izleyicisi özelliği ' nde, hizmet yanıt süresi yok ancak ağ kaybı ve gecikme süresi geçerli
Hedef hizmet bir Web uygulaması değilse ancak test bir Web testi olarak yapılandırıldığında bu durum oluşabilir. Test yapılandırmasını düzenleyin ve test türünü web yerine ağ olarak seçin.

## <a name="miscellaneous"></a>Muhtelif Hükümler

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>İzleme için kullanılan düğüm üzerinde bir performans etkisi var mı?
NPM işlemi, ana bilgisayar CPU kaynaklarının %5 ' inden fazlasını kullanıyorsa durdurulacak şekilde yapılandırıldı. Bu, performansı etkilemeden olağan iş yükleri için düğümleri kullanmaya devam etmek zorunda kalmasından emin olmak içindir.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>İzleme için güvenlik duvarı kuralları düzenlensin mi?
NPM yalnızca, aracıların belirtilen bağlantı noktasında birbirleriyle TCP bağlantıları oluşturmalarına izin vermek için EnableRules. ps1 PowerShell betiğinin çalıştırıldığı düğümlerde yerel bir Windows güvenlik duvarı kuralı oluşturur. Çözüm herhangi bir ağ güvenlik duvarı veya ağ güvenlik grubu (NSG) kuralını değiştirmez.

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>İzleme için kullanılan düğümlerin sistem durumunu nasıl kontrol edebilirim?
İzleme için kullanılan düğümlerin sistem durumunu şu görünümden görebilirsiniz: Ağ Performansı İzleyicisi-> Yapılandırma-> düğümleri. Bir düğüm sağlıksız ise, hata ayrıntılarını görüntüleyebilir ve önerilen eylemi gerçekleştirebilirsiniz.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>Gecikme sayısını mikrosaniye cinsinden bildirebilirler mi?
NPM, Kullanıcı arabirimindeki ve milisaniye cinsinden gecikme sayılarını yuvarlar. Aynı veriler daha yüksek bir ayrıntı düzeyinde (bazen dört ondalık basamağa kadar) depolanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure 'daki ağ performansı İzleyicisi çözümüne](../../azure-monitor/insights/network-performance-monitor.md)başvurarak ağ performansı İzleyicisi hakkında daha fazla bilgi edinin.
