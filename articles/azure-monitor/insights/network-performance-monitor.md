---
title: Azure 'da Ağ Performansı İzleyicisi çözümü | Microsoft Docs
description: Azure 'daki Ağ Performansı İzleyicisi, ağların performansını neredeyse gerçek zamanlı olarak izlemenize ve ağ performansı performans sorunlarını tespit etmenize yardımcı olur.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: 943f6cee70367d8a1ff0dc003d06e46b487e3a48
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898802"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Azure 'da Ağ Performansı İzleyicisi çözümü

![Ağ Performansı İzleyicisi simgesi](./media/network-performance-monitor/npm-symbol.png)


Ağ Performansı İzleyicisi, ağ altyapınızın çeşitli noktaları arasındaki ağ performansını izlemenize yardımcı olan bulut tabanlı bir karma ağ izleme çözümüdür. Ayrıca hizmet ve uygulama uç noktalarına yapılan ağ bağlantısını izlemenize ve Azure ExpressRoute’un performansını izlemenize yardımcı olur. 

Ağ Performansı İzleyicisi trafik görüntüleme, yönlendirme hataları ve geleneksel ağ izleme yöntemlerinin algılayamadığı sorunlar gibi ağ sorunlarını algılar. Bir ağ bağlantısı için bir eşik ihlal edildiğinde, çözüm uyarılar oluşturur ve size bildirir. Ayrıca ağ performans sorunlarının zamanında tespit edilmesini sağlar ve sorunun kaynağını belirli bir ağ kesimine veya cihazına yerelleştirir. 

Ağ Performansı İzleyicisi üç geniş özellik sunar: 

* [Performans İzleyicisi](network-performance-monitor-performance-monitor.md): bulut dağıtımları ve şirket içi konumlar, birden çok veri merkezi ve şube ofisleri ve iş açısından kritik çok katmanlı uygulamalar veya mikro hizmetler arasında ağ bağlantısını izleyebilirsiniz. Performans Izleyicisinde, kullanıcılar şikayetden önce ağ sorunlarını tespit edebilirsiniz.

* [Hizmet bağlantısı İzleyicisi](network-performance-monitor-service-connectivity.md): kullanıcılarınızın bağlantısını ilgilendiğiniz hizmetlere izleyebilir, yolda hangi altyapının olduğunu belirleyebilir ve ağ performans sorunlarının nerede gerçekleşeceğini belirleyebilirsiniz. Kullanıcılarınız için kesintiler hakkında bilgi alabilir ve ağ yolunuzda sorunların tam konumunu görebilirsiniz. 

    Bu özellik, neredeyse gerçek zamanlı olarak izlemek üzere HTTP, HTTPS, TCP ve ıCMP 'yi temel alan testler gerçekleştirmenize yardımcı olur ve hizmetinizin kullanılabilirlik ve yanıt süresini de izlersiniz. Ayrıca, ağ payını paket kaybı ve gecikme süresi içinde izleyebilirsiniz. Ağ topolojisi eşlemesiyle, ağ yavaşlamaları yalıtabilirsiniz. Her bir atlamada gecikme süresiyle, düğümdeki ağ yolu üzerinde gerçekleşen sorun noktaları ' nı tanımlayabilirsiniz. Yerleşik testlerle, herhangi bir ön yapılandırma olmadan Office 365 ve Dynamics CRM 'ye ağ bağlantısını izleyebilirsiniz. Bu özellik sayesinde, ağ bağlantısını Web siteleri, SaaS uygulamaları, PaaS uygulamaları ve SQL veritabanları gibi herhangi bir TCP özellikli uç noktaya izleyebilirsiniz.

* [ExpressRoute İzleyicisi](network-performance-monitor-expressroute.md): Azure ExpressRoute üzerinden şubeleriniz ve Azure arasındaki uçtan uca bağlantıyı ve performansı izleyin.  

[Ağ performansı İzleyicisi](https://docs.microsoft.com/azure/networking/network-monitoring-overview) tarafından desteklenen çeşitli yetenekler hakkında daha fazla bilgi çevrimiçi olarak sunulmaktadır.
 
## <a name="supported-regions"></a>Desteklenen bölgeler
NPM, dünyanın herhangi bir bölümündeki ağlar ve uygulamalar arasındaki bağlantıyı, aşağıdaki bölgelerden birinde barındırılan bir çalışma alanından izleyebilir:
* Batı Avrupa
* Orta Batı ABD
* Doğu ABD
* Doğu Japonya
* Güneydoğu Asya
* Güney Doğu Avustralya
* Güney UK
* Orta Hindistan
* ABD devleti Virginia
* Çin Doğu 2


ExpressRoute Izleyicisi için desteklenen bölgelerin listesi [belgelerde](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117)bulunur.


## <a name="set-up-and-configure"></a>Ayarlama ve yapılandırma

### <a name="install-and-configure-agents"></a>Aracıları yükleme ve yapılandırma 

[Windows bilgisayarlarını Azure izleyici 'ye bağlama](../platform/agent-windows.md) ve [azure Izleyici 'ye Operations Manager bağlama](../platform/om-agents.md)sırasında aracıları yüklemek için temel süreçler kullanın.

### <a name="where-to-install-the-agents"></a>Aracıların yükleneceği konum 

* **Performans İzleyicisi**: diğer alt ağlara yönelik ağ bağlantılarını izlemek istediğiniz her alt ağa bağlı en az bir düğüme bağlı Log Analytics aracılarını yükler.

    Bir ağ bağlantısını izlemek için, bu bağlantının her iki uç noktasına aracı yükler. Ağınızın topolojisi hakkında emin değilseniz, aracıları, ağ performansını izlemek istediğiniz kritik iş yükleri olan sunuculara yükleyebilirsiniz. Örneğin, bir Web sunucusu ve SQL çalıştıran bir sunucu arasındaki ağ bağlantısını izlemek istiyorsanız, her iki sunucuya da bir aracı yükler. Aracılar, ana bilgisayarları değil, konaklar arasında ağ bağlantısını (bağlantıları) izler. 

* **Hizmet bağlantısı İzleyicisi**: hizmet uç noktasına ağ bağlantısını izlemek istediğiniz her düğüme bir Log Analytics Aracısı yükler. O1, O2 ve O3 etiketli Office sitelerinizin Office 365 'e ağ bağlantısını izlemek istiyorsanız örnek bir örnektir. Log Analytics aracısını O1, O2 ve O3 içinde her biri en az bir düğüme yükler. 

* **ExpressRoute İzleyicisi**: Azure sanal ağınıza en az bir Log Analytics Aracısı yükler. Ayrıca, ExpressRoute özel eşlemesi aracılığıyla bağlanan şirket içi alt ağa en az bir aracı da yükler.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>İzleme için Log Analytics aracılarını yapılandırma 

Ağ Performansı İzleyicisi, kaynak ve hedef aracılar arasındaki ağ performansını izlemek için yapay işlemler kullanır. Performans Izleyicisi ve hizmet bağlantısı Izleyicisi yeteneklerini izlemek için protokol olarak TCP ve ıCMP arasında seçim yapabilirsiniz. ExpressRoute Izleyicisi için izleme protokolü olarak yalnızca TCP kullanılabilir. Güvenlik duvarının, seçtiğiniz protokolde izleme için kullanılan Log Analytics aracıları arasında iletişime izin verdiğinden emin olun. 

* **TCP protokolü**: izleme için protokol olarak TCP ' yi seçerseniz, aracıların birbirlerine bağlanabildiğinizden emin olmak için ağ performansı İzleyicisi ve ExpressRoute İzleyicisi için kullanılan aracılarda güvenlik duvarı bağlantı noktasını açın. Bağlantı noktasını açmak için, yönetim ayrıcalıklarına sahip bir PowerShell penceresinde herhangi bir parametre olmadan [Enablerules. ps1](https://aka.ms/npmpowershellscript) PowerShell betiğini çalıştırın.

    Betik, çözüm için gereken kayıt defteri anahtarlarını oluşturur. Ayrıca aracıların birbirleriyle TCP bağlantıları oluşturmalarına izin vermek için Windows güvenlik duvarı kuralları oluşturur. Betiği tarafından oluşturulan kayıt defteri anahtarları, hata ayıklama günlüklerinin kaydedilip edilmeyeceğini ve Günlükler dosyasının yolunu belirtir. Betik Ayrıca iletişim için kullanılan aracı TCP bağlantı noktasını tanımlar. Bu anahtarların değerleri otomatik olarak komut dosyası tarafından ayarlanır. Bu anahtarları el ile değiştirmeyin. Varsayılan olarak açılan bağlantı noktası 8084 ' dir. Komut dosyasına portNumber parametresini girerek özel bir bağlantı noktası kullanabilirsiniz. Betiğin çalıştırıldığı tüm bilgisayarlarda aynı bağlantı noktasını kullanın. 

    >[!NOTE]
    > Betik yalnızca Windows güvenlik duvarını yerel olarak yapılandırır. Bir ağ güvenlik duvarınız varsa, Ağ Performansı İzleyicisi tarafından kullanılan TCP bağlantı noktasına giden trafiğe izin verdiğinden emin olun.

    >[!NOTE]
    > Hizmet bağlantısı Izleyicisi için [Enablerules. ps1](https://aka.ms/npmpowershellscript ) PowerShell betiğini çalıştırmanız gerekmez.

    

* **ICMP protokolü**: ICMP 'yi izlemeye yönelik protokol olarak SEÇERSENIZ, ICMP 'yi güvenilir bir şekilde kullanmak için aşağıdaki güvenlik duvarı kurallarını etkinleştirin:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Çözümü yapılandırma 

1. Ağ Performansı İzleyicisi çözümünü [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)' nden çalışma alanınıza ekleyin. Ayrıca, [Çözüm Galerisi Azure izleyici çözümlerini ekleme](../../azure-monitor/insights/solutions.md)bölümünde açıklanan süreci de kullanabilirsiniz. 
2. Log Analytics çalışma alanınızı açın ve **genel bakış** kutucuğunu seçin. 
3. İleti *çözümünün ek yapılandırma gerektirdiğinden* **ağ performansı İzleyicisi** kutucuğunu seçin.

   ![Ağ Performansı İzleyicisi kutucuğu](media/network-performance-monitor/npm-config.png)

4. **Kurulum** sayfasında, Log Analytics aracılarını yükleme ve aracıları **ortak ayarlar** görünümünde izlemeye yönelik olarak yapılandırma seçeneğini görürsünüz. Daha önce açıklandığı gibi, Log Analytics aracılarını yükleyip yapılandırdıysanız, kullanmak istediğiniz özelliği yapılandırmak için **Kurulum** görünümünü seçin. 

   **Performans İzleyicisi**: **varsayılan** performans izleyicisi kuralında yapay işlemler için kullanılacak protokolü seçin ve **Kaydet & devam et**' i seçin. Bu protokol seçimi yalnızca sistem tarafından oluşturulan varsayılan kural için geçerlidir. Her bir performans Izleyici kuralını açıkça her oluşturduğunuzda Protokolü seçmeniz gerekir. **Performans İzleyicisi** sekmesindeki **varsayılan** kural ayarlarına her zaman geçebilirsiniz (günlük-0 yapılandırmanızı tamamladıktan sonra görünür) ve protokolü daha sonra değiştirmelisiniz. Performans Izleyicisi özelliğini istemiyorsanız, varsayılan kuralı **Performans İzleyicisi** sekmesindeki **varsayılan** kural ayarlarından devre dışı bırakabilirsiniz.

   ![Performans Izleyicisi görünümü](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Hizmet bağlantısı İzleyicisi**: Bu özellik, aracılarınızın Office 365 ve Dynamics 365 ile ağ bağlantısını izlemeye yönelik yerleşik önceden yapılandırılmış testler sağlar. İzlemek istediğiniz Office 365 ve Dynamics 365 hizmetlerini, yanındaki onay kutularını seçerek seçin. İzlemek istediğiniz aracıları seçmek için, **aracıları Ekle**' yi seçin. Bu özelliği kullanmak istemiyorsanız veya daha sonra ayarlamak istemiyorsanız, hiçbir şey seçmeyin ve **kaydet & kaydet**' i seçin.

   ![Hizmet bağlantısı Izleyicisi görünümü](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute İzleyicisi**: Bu Log Analytics çalışma alanıyla bağlantılı Azure aboneliğindeki sanal ağlara bağlı olan tüm ExpressRoute özel eşayarlarını bulmaya yönelik **Şimdi bul** ' u seçin. 

   ![ExpressRoute Izleyicisi görünümü](media/network-performance-monitor/npm-express-route.png)

   Bulma işlemi tamamlandıktan sonra keşfedilen devreler ve eşlemeler bir tabloda listelenir. 

   ![Ağ Performansı İzleyicisi yapılandırma sayfası](media/network-performance-monitor/npm-private-peerings.png)
    
Bu devreler ve eşlemeler için izleme, başlangıçta devre dışı durumda. İzlemek istediğiniz her kaynağı seçin ve sağdaki ayrıntılar görünümünden bunları izlemeyi yapılandırın. Yapılandırmayı kaydetmek için **Kaydet** ' i seçin. Daha fazla bilgi için "ExpressRoute izlemesini yapılandırma" makalesini inceleyin. 

Kurulum bittikten sonra, verilerin doldurulması 30 dakika boyunca saat sürer. Çözüm, ağınızdan verileri toplalarken, ileti çözümünün Ağ Performansı İzleyicisi **genel bakış** kutucuğunda *ek yapılandırma gerektirdiğini* görürsünüz. Veriler toplandıktan ve dizinlendikten sonra, **genel bakış** kutucuğu değişir ve ağ durumlarınızın bir Özet olduğunu bildirir. Daha sonra, Log Analytics aracılarının yüklendiği düğümlerin izlenmesini, ayrıca ortamınızda bulunan alt ağları da düzenleyebilirsiniz.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Alt ağlar ve düğümler için izleme ayarlarını Düzenle 

En az bir aracı yüklü olan tüm alt ağlar yapılandırma sayfasındaki **alt ağlar** sekmesinde listelenir. 


Belirli alt ağların izlenmesini etkinleştirmek veya devre dışı bırakmak için:

1. Alt **Ağ kimliği**' nin yanındaki onay kutusunu seçin veya temizleyin. Ardından, uygun şekilde **izleme Için kullan** ' ın seçili veya temizlenmiş olduğundan emin olun. Birden çok alt ağ seçebilir veya temizleyebilirsiniz. Devre dışı bırakıldığında, alt ağlar izlenmez ve aracılar diğer aracıların ping komutunu durduracak şekilde güncelleştirilir. 
2. Belirli bir alt ağ içinde izlemek istediğiniz düğümleri seçin. Listeden alt ağı seçin ve izlenmeyen ve izlenen düğümleri içeren listeler arasında gerekli düğümleri taşıyın. Alt ağ için özel bir açıklama ekleyebilirsiniz.
3. Yapılandırmayı kaydetmek için **Kaydet** ' i seçin. 

#### <a name="choose-nodes-to-monitor"></a>İzlenecek düğümleri seçin

Bir aracısının yüklü olduğu tüm düğümler **düğümler** sekmesinde listelenir. 

1. İzlemek veya izlemeyi durdurmak istediğiniz düğümleri seçin veya temizleyin. 
2. **İzleme Için kullan**' ı seçin veya uygun şekilde temizleyin. 
3. **Kaydet**’i seçin. 


İstediğiniz özellikleri yapılandırın:

- [Performans Izleyicisi](network-performance-monitor-performance-monitor.md#configuration)
- [Hizmet bağlantısı Izleyicisi](network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute Izleyicisi](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Veri toplama ayrıntıları
Protokol olarak TCP ' yi seçtiğinizde, kayıp ve gecikme bilgilerini toplamak için Ağ Performansı İzleyicisi TCP SYN-SYNACK-ACK Handshake paketlerini kullanır. Ağ Performansı İzleyicisi, protokol olarak ıCMP 'yi seçtiğinizde ıCMP YANKı ıCMP YANKı yanıtı kullanır. İzleme yolu, topoloji bilgilerini almak için de kullanılır.

Aşağıdaki tabloda, verilerin Ağ Performansı İzleyicisi için nasıl toplandığı hakkında veri toplama yöntemleri ve diğer ayrıntılar gösterilmektedir.

| Platform | Doğrudan aracı | System Center Operations Manager Aracısı | Azure Depolama | Operations Manager gerekli mi? | Yönetim grubu aracılığıyla gönderilen aracı verileri Operations Manager | Toplama sıklığı |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP el sıkışmaları/ıCMP YANKı iletileri her 5 saniyede bir, 3 dakikada bir gönderilen veriler |
 

 
Çözüm, ağın sistem durumunu değerlendirmek için yapay işlemler kullanır. Log Analytics aracıları, ağ Exchange TCP paketlerindeki veya bir diğeri ile ıCMP Echo çeşitli noktalarında yüklü. Aracıların TCP paketleri veya ıCMP yankı kullanmasına bakılmaksızın, izleme için seçtiğiniz protokole bağlıdır. İşlem sırasında aracılar, varsa gidiş dönüş süresini ve paket kaybını öğreniyordur. Her aracı düzenli aralıklarla, ağda test olması gereken çeşitli yolları bulmak için diğer aracılara bir izleme yolu da gerçekleştirir. Aracılar, bu verileri kullanarak ağ gecikmesi ve paket kayıp rakamlarını verebilir. Sınamalar beş saniyede bir yinelenir. Veriler, Azure Izleyici 'de Log Analytics çalışma alanına yüklenmeden önce aracılardan üç dakikalık bir araya getirilir.



>[!NOTE]
> Aracılar birbirleriyle sık iletişim kurmakla birlikte, testleri yürütülürken önemli bir ağ trafiği oluşturmaz. Aracılar, kaybı ve gecikmeyi belirlemede yalnızca TCP SYN-SYNACK-ACK Handshake paketlerine bağımlıdır. Hiçbir veri paketi değiş tokuş alınmaz. Bu işlem sırasında, aracılar yalnızca gerektiğinde iletişim kurar. Aracı iletişim topolojisi, ağ trafiğini azaltmak için iyileştirilmiştir.

## <a name="use-the-solution"></a>Çözümü kullanma 

### <a name="network-performance-monitor-overview-tile"></a>Ağ Performansı İzleyicisi genel bakış kutucuğu 

Ağ Performansı İzleyicisi çözümünü etkinleştirdikten sonra, **genel bakış** sayfasındaki çözüm kutucuğu ağ durumuna hızlı bir genel bakış sağlar. 

 ![Ağ Performansı İzleyicisi genel bakış kutucuğu](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Ağ Performansı İzleyicisi panosu 

* **En Iyi ağ durumu olayları**: Bu sayfa, sistemdeki en son sistem durumu olaylarının ve uyarıların bir listesini ve olayları etkin olduğundan geçen süreyi sağlar. İzleme kuralı için seçili ölçüm değeri (kayıp, gecikme süresi, yanıt süresi veya bant genişliği kullanımı) eşiği aştığında bir sistem durumu olayı veya uyarısı oluşturulur. 

* **ExpressRoute İzleyicisi**: Bu sayfa, çözüm izlemelerinin çeşitli ExpressRoute eşleme bağlantıları için sistem durumu özetleri sağlar. **Topoloji** kutucuğu, ağınızda Izlenen ExpressRoute devreleri aracılığıyla ağ yolunun sayısını gösterir. **Topoloji** görünümüne gitmek için bu kutucuğu seçin.

* **Hizmet bağlantısı İzleyicisi**: Bu sayfa, oluşturduğunuz farklı testler için sistem durumu özetleri sağlar. **Topoloji** kutucuğu izlenen uç nokta sayısını gösterir. **Topoloji** görünümüne gitmek için bu kutucuğu seçin.

* **Performans İzleyicisi**: Bu sayfa, çözümün izlediği **ağ** **bağlantıları ve alt ağ bağlantıları** için sistem durumu özetleri sağlar. **Topoloji** kutucuğu, ağınızda izlenen ağ yollarının sayısını gösterir. **Topoloji** görünümüne gitmek için bu kutucuğu seçin. 

* **Ortak sorgular**: Bu sayfa, ham ağ izleme verilerini doğrudan getiren bir arama sorguları kümesi içerir. Bu sorguları, özelleştirilmiş raporlama için kendi sorgularınızı oluşturmak üzere bir başlangıç noktası olarak kullanabilirsiniz. 

   ![Ağ Performansı İzleyicisi panosu](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Derinlemesine detaya gitme 

Herhangi bir ilgilendiğiniz alanı daha ayrıntılı bir şekilde incelemek için çözüm panosunda çeşitli bağlantılar seçebilirsiniz. Örneğin, panoda bir uyarı veya sağlıksız bir ağ bağlantısı görüntülendiğinde, daha fazla araştırma yapmak için seçin. Bir sayfa, belirli ağ bağlantısı için tüm alt ağ bağlantılarını listeler. Her alt ağ bağlantısının kayıp, gecikme süresi ve sistem durumunu görebilirsiniz. Hangi alt ağ bağlantısının soruna neden olduğunu hızlıca bulabilirsiniz. Sağlıksız alt ağ bağlantısının tüm düğüm bağlantılarını görmek için **düğüm bağlantılarını görüntüle** ' yi seçin. Sonra, düğümden düğüme tek bağlantıları görebilir ve sağlıksız düğüm bağlantılarını bulabilirsiniz. 

Kaynak ve hedef düğümler arasındaki yolların atlama topolojisini görüntülemek için **topolojiyi görüntüle** ' yi seçin. Sağlıksız yollar kırmızı renkte görünür. Sorunu ağın belirli bir bölümünde hızlı bir şekilde tanımlayabilmeniz için her bir atlama tarafından katkıda bulunulan gecikme süresini görüntüleyebilirsiniz.

 

### <a name="network-state-recorder-control"></a>Ağ durumu kaydedici denetimi

Her görünüm, belirli bir noktada ağ durumlarınızın anlık görüntüsünü görüntüler. Varsayılan olarak, en son durum gösterilir. Sayfanın üst kısmındaki çubuk, durumun görüntülendiği zaman noktasını gösterir. Ağ durumlarınızın bir anlık görüntüsünü önceki bir zamanda görüntülemek için **Eylemler**' i seçin. Ayrıca, en son durumu görüntülerken herhangi bir sayfa için otomatik yenilemeyi etkinleştirebilir veya devre dışı bırakabilirsiniz. 

 ![Ağ durumu Kaydedicisi](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Eğilim grafikleri 

Detaydan en her düzeyde, ilgili ölçümün eğilimini görebilirsiniz. Bu, kayıp, gecikme süresi, yanıt süresi veya bant genişliği kullanımı olabilir. Eğilimin zaman aralığını değiştirmek için grafiğin en üstündeki zaman denetimini kullanın. 

Eğilim grafikleri, performans ölçümünün performansının geçmiş bir perspektifini gösterir. Bazı ağ sorunları geçici olarak geçicidir ve ağın yalnızca geçerli durumuna bakılarak kalıcı olarak sonuçlanır. Sorunlar, herkesin uyarı vermeden önce daha sonra zaman içinde yeniden görünür hale getirebilirler. Bu tür geçici sorunlar, uygulama yöneticileri için de zor olabilir. Sorunlar genellikle uygulama yanıt süresi içinde açıklanamayan artışlara kadar tüm uygulama bileşenleri düzgün şekilde çalışmaya görünse bile görünür. 

Bir eğilim grafiğine bakarak bu tür sorunları kolayca tespit edebilirsiniz. Sorun ağ gecikmesi veya paket kaybı durumunda ani bir ani artış olarak görüntülenir. Sorunu araştırmak için, ağ anlık görüntüsünü ve sorun oluştuğunda o zamana yönelik topolojiyi görüntülemek için ağ durumu kaydedici denetimini kullanın.

 
![Eğilim grafikleri](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Topoloji eşlemesi 

Ağ Performansı İzleyicisi, etkileşimli bir topoloji eşlemesindeki kaynak ve hedef uç nokta arasındaki yolların atlama topolojisini gösterir. Topoloji haritasını görüntülemek için, çözüm panosundaki **topoloji** kutucuğunu seçin. Ayrıca detaya gitme sayfasında **topoloji görüntüle** bağlantısını da seçebilirsiniz. 

Topoloji eşlemesi, kaynak ve hedef arasında kaç yolun olduğunu ve veri paketlerinin hangi yollarla yapılacağını gösterir. Her ağ atlaması tarafından katkıda bulunulan gecikme da görünür. Toplam yol gecikmesi eşiğin üstünde olan tüm yollar (karşılık gelen izleme kuralında ayarlanır) kırmızı renkte gösterilir. 

Bir düğüm seçip topoloji haritasında üzerine geldiğinizde, FQDN ve IP adresi gibi düğüm özelliklerini görürsünüz. IP adresini görmek için bir atlama seçin. Sorunlu ağ atlayağını, katkıda bulunan gecikme süresini yaşıyorsanız ile tanımlayabilirsiniz. Belirli yolları filtrelemek için, daraltılabilir eylem bölmesindeki filtreleri kullanın. Ağ topolojilerini basitleştirmek için, eylem bölmesindeki kaydırıcıyı kullanarak ara atlamaları gizleyin. Fare tekerleğini kullanarak topoloji haritasını yakınlaştırıp uzaklaştırabilirsiniz. 

Haritada gösterilen topoloji katman 3 topolojidir ve katman 2 cihazlarını ve bağlantılarını içermez. 

 
![Topoloji eşlemesi](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Azure Izleyici 'de günlük sorguları

Ağ Performansı İzleyicisi panosu ve detaya gitme sayfaları aracılığıyla grafiksel olarak sunulan tüm veriler, [günlük sorgularında](../log-query/log-query-overview.md)yerel olarak da kullanılabilir. Depodaki verilerin etkileşimli analizini yapabilir ve farklı kaynaklardaki verileri ilişkilendirebilir. Ayrıca, özel uyarılar ve görünümler oluşturabilir ve verileri Excel, Power BI veya paylaşılabilir bir bağlantıya aktarabilirsiniz. Panodaki **ortak sorgular** alanında, kendi sorgularınızı ve raporlarınızı oluşturmak için başlangıç noktası olarak kullanabileceğiniz bazı yararlı sorgular bulunur. 

## <a name="alerts"></a>Uyarılar

Ağ Performansı İzleyicisi, [Azure izleyici](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)'nin uyarı yeteneklerini kullanır.

Bu, tüm bildirimlerin [eylem grupları](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)kullanılarak yönetildiği anlamına gelir.  

Log Analytics aracılığıyla bir uyarı oluşturan NPM kullanıcısı varsa: 
1. Sizi Azure portal yönlendirmenizi sağlayacak bir bağlantı görürsünüz. Portala erişmek için tıklayın.
2. Ağ Performansı İzleyicisi çözüm kutucuğuna tıklayın. 
3. Yapılandır ' a gidin.  
4. Uyarı oluşturmak istediğiniz testi seçin ve yukarıdaki adımları izleyin.

Azure portal aracılığıyla bir uyarı oluşturan NPM kullanıcısı varsa:  
1. E-postanızı doğrudan girmeyi veya eylem grupları aracılığıyla uyarı oluşturmayı seçebilirsiniz.
2. E-postanızı doğrudan girmeyi seçerseniz, **NPM e-posta ActionGroup** adlı bir eylem grubu oluşturulur ve e-posta kimliği bu eylem grubuna eklenir.
3. Eylem gruplarını kullanmayı seçerseniz, önceden oluşturulmuş bir eylem grubu seçmeniz gerekir. Burada bir eylem grubu oluşturmayı öğrenebilirsiniz [.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal) 
4. Uyarı başarıyla oluşturulduktan sonra uyarılarınızı yönetmek için Uyarıları Yönet bağlantısını kullanabilirsiniz. 

Her uyarı oluşturduğunuzda, NPM Azure Izleyici 'de sorgu tabanlı bir günlük uyarı kuralı oluşturur. Bu sorgu varsayılan olarak her 5 dakikada bir tetiklenir. Azure izleyici, oluşturulan ilk 250 günlük uyarı kuralı için ücret almaz ve 250 günlük uyarı kuralları sınırının üzerindeki tüm uyarı kuralları, [Azure izleyici fiyatlandırma sayfasında uyarı başına fiyatlandırmaya](https://azure.microsoft.com/pricing/details/monitor/)göre faturalandırılır.
Bildirimler, [Azure izleyici fiyatlandırma sayfasında her bildirim fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/)için ayrı olarak ücretlendirilir.


## <a name="pricing"></a>Fiyatlandırma

Fiyatlandırma hakkında bilgi [çevrimiçi](network-performance-monitor-pricing-faq.md)olarak sunulmaktadır.

## <a name="provide-feedback"></a>Geri bildirim sağlayın 

* **UserVoice:** Üzerinde çalışmamızı istediğiniz Ağ Performansı İzleyicisi özellikler için fikirlerinizi gönderebilirsiniz. [UserVoice sayfasını](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring)ziyaret edin. 

* **Kohortu Imize katılarak:** Her zaman yeni müşterilerin kohortu 'e katılıyoruz. Bunun bir parçası olarak, yeni özelliklere erken erişim ve Ağ Performansı İzleyicisi iyileştirmemize yardımcı olacak bir fırsat edinirsiniz. Katılımını ilgileniyorsanız, bu [hızlı anketi](https://aka.ms/npmcohort)doldurun. 

## <a name="next-steps"></a>Sonraki adımlar 
[Performans İzleyicisi](network-performance-monitor-performance-monitor.md), [hizmet bağlantı Izleyicisi](network-performance-monitor-performance-monitor.md)ve [ExpressRoute İzleyicisi](network-performance-monitor-expressroute.md)hakkında daha fazla bilgi edinin. 
