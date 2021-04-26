---
title: Hizmetiniz için ölçeklendirme ve yüksek kullanılabilirlik sağlamaya yönelik sistem durumu yoklamaları
titleSuffix: Azure Load Balancer
description: Bu makalede, arka plandaki örnekleri izlemek için sistem durumu araştırmalarını nasıl kullanacağınızı öğrenin Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a008d7b26738b9552a7a43ab026391bd9afe0aa8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96780952"
---
# <a name="load-balancer-health-probes"></a>Load Balancer durum araştırmaları

Azure Load Balancer ile yük dengeleme kuralları kullanırken, arka uç uç noktası durumunu algılamaya Load Balancer izin vermek için sistem durumu araştırmalarını belirtmeniz gerekir.  Durum araştırması ve araştırma yanıtlarının yapılandırması, hangi arka uç havuzu örneklerinin yeni akışlar alacağını tespit eder. Bir arka uç uç noktasındaki uygulamanın başarısızlığını algılamak için sistem durumu araştırmalarını kullanabilirsiniz. Ayrıca, bir sistem durumu araştırmasına özel bir yanıt oluşturabilir ve yük veya planlanan kapalı kalma süresini yönetmek için akış denetimi için sistem durumu araştırması ' ni kullanabilirsiniz. Bir sistem durumu araştırması başarısız olduğunda, Load Balancer ilgili sağlıksız örneğe yeni akış göndermeyi durdurur. Giden bağlantı etkilenmez, yalnızca gelen bağlantı etkilendi.

Durum araştırmaları birden çok protokolü destekler. Belirli bir sistem durumu araştırma protokolünün kullanılabilirliği Load Balancer SKU 'ya göre değişir.  Ayrıca, hizmetin davranışı, bu tabloda gösterildiği gibi Load Balancer SKU 'ya göre farklılık gösterir:

| | Standart SKU | Temel SKU |
| --- | --- | --- |
| **[Araştırma türleri](#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Araştırma davranışı](#probedown)** | Tüm yoklamalar, tüm TCP akışları devam eder. | Tüm yoklamalar, tüm TCP akışları sona erer. | 


>[!IMPORTANT]
>Güvenilir bir hizmet oluşturmak için aşağıdaki önemli [Tasarım Kılavuzu](#design) dahil olmak üzere bu belgeyi tamamen gözden geçirin.

>[!IMPORTANT]
>Load Balancer sistem durumu araştırmaları, 168.63.129.16 IP adresinden kaynaklanacak ve örneğinizi işaretlemek için araştırmaların engellenmemelidir.  Ayrıntılar için [araştırma kaynağı IP adresini](#probesource) gözden geçirin.

>[!IMPORTANT]
>Yapılandırılmış zaman aşımı eşiğinden bağımsız olarak, sunucu HTTP 200 Tamam olmayan bir durum kodu döndürürse veya bağlantı TCP sıfırlaması aracılığıyla sonlandırılırsa Load Balancer, sistem durumu araştırmalarının otomatik olarak bir örneği araştırması engellenir.

## <a name="probe-configuration"></a><a name="probes"></a>Araştırma yapılandırması

Durum araştırma yapılandırması aşağıdaki öğelerden oluşur:

- Tek yoklamalar arasındaki Aralık süresi
- Araştırmanın farklı bir duruma geçmeden önce gözlenecek araştırma yanıtlarının sayısı
- Araştırmanın Protokolü
- Araştırmanın bağlantı noktası
- Http (S) yoklamaları kullanılırken HTTP GET için kullanılacak HTTP yolu

>[!NOTE]
>Azure PowerShell, Azure CLı, şablonlar veya API kullanılırken bir araştırma tanımı zorunlu değildir veya denetlenir. Araştırma doğrulama testleri yalnızca Azure portalı kullanılırken yapılır.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Uygulama sinyalini anlama, sinyalin algılanması ve platformun yeniden eylemi

Her ikisine de uygulanan araştırma yanıtlarının sayısı

- bir örneğin yukarı olarak işaretlenmesini sağlayan başarılı araştırmaların sayısı ve
- bir örneğin aşağı olarak işaretlenmesine neden olan zaman aşımına uğrayan yoklamaların sayısı.

Belirtilen zaman aşımı ve Aralık değerleri, bir örneğin yukarı veya aşağı olarak işaretlenip işaretlenmediğini belirtir.  Zaman aralığının yoklama yanıtlarının sayısıyla çarpıldığı süre, araştırma yanıtlarının algılanabilmesi için gereken süreyi belirler.  Ve gerekli yoklamaların elde edilmesi durumunda hizmet tepki verir.

Davranışı bir örnekle daha fazla gösteririz. Araştırma yanıtlarının sayısını 2 ' ye ve Interval değerini 5 saniyeye ayarladıysanız, bu 2 araştırma zaman aşımı hatalarının 10 saniyelik bir Aralık içinde gözlenecek olması anlamına gelir.  Bir araştırmanın gönderildiği zaman, uygulamanız durumu değiştirebiliyorsa, iki senaryoya göre tespit etmek için gereken süreyi de eşliyoruz:

1. Uygulamanız ilk araştırma ulaşmadan hemen önce bir zaman aşımı araştırma yanıtı oluşturmaya başlarsa, bu olayların algılanması 10 saniye (2 x 5 saniyelik Aralık) ve uygulamanın, ilk araştırmanın ulaştığı zaman bir zaman aşımını sinyalden başlayan süre kadar sürer.  Bu algılamanın 10 saniyeden biraz uzun sürmek üzere olduğunu varsayabilirsiniz.
2. Uygulamanız ilk araştırma alındıktan sonra yalnızca bir zaman aşımı araştırma yanıtı oluşturmaya başlarsa, bu olayların algılanması sonraki araştırma gelene kadar (ve zaman aşımına uğrayana kadar) ve 10 saniye (2 x 5 saniyelik aralıklar) kadar başlamaz.  Bu algılamanın yalnızca 15 saniye içinde geçmesi gerektiğini varsayabilirsiniz.

Bu örnekte, algılama gerçekleştiyse, platform bu değişikliğe tepki vermek için kısa bir süre sürer.  Bu, buna bağlı olarak 

1. uygulama durumu değiştirme başladığında ve
2. Bu değişiklik algılandığında ve gerekli ölçütlere (belirtilen aralıkta gönderilen yoklamaların sayısı) karşılandığında ve
3. algılama, platform genelinde iletildiğinde 

bir zaman aşımı araştırma yanıtı için yeniden eylemin, uygulamadan gelen bir değişikliğe yanıt vermek üzere en az 10 saniyelik ve en fazla 15 saniyeden fazla bir işlem olacağını varsayabilirsiniz.  Bu örnek, ne olduğunu göstermek için verilmiştir, ancak bu örnekte gösterilen yukarıdaki kaba kılavuzun ötesinde tam bir süre tahmin etmek mümkün değildir.

>[!NOTE]
>Sistem durumu araştırması, arka uç havuzundaki çalışan tüm örnekleri araştıracaktır. Bir örnek durdurulmuşsa, yeniden başlatılana kadar bu, araştırmaz.

## <a name="probe-types"></a><a name="types"></a>Araştırma türleri

Sistem durumu araştırması tarafından kullanılan protokol aşağıdakilerden birine yapılandırılabilir:

- [TCP dinleyicileri](#tcpprobe)
- [HTTP uç noktaları](#httpprobe)
- [HTTPS uç noktaları](#httpsprobe)

Kullanılabilir protokoller, kullanılan Load Balancer SKU 'suna bağımlıdır:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| **Standart SKU** |    &#9989; |   &#9989; |   &#9989; |
| **Temel SKU** |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a> TCP araştırması

TCP araştırmaları, tanımlı bağlantı noktasıyla üç yönlü bir açık TCP el sıkışması gerçekleştirerek bir bağlantı başlatır.  TCP araştırmaları bir bağlantıyı dört yönlü bir kapalı TCP el sıkışması ile sonlandırır.

En düşük araştırma aralığı 5 saniyedir ve en az sağlıksız yanıt sayısı 2 ' dir.  Tüm aralıkların toplam süresi 120 saniyeyi aşamaz.

Şu durumlarda bir TCP araştırması başarısız olur:
* Örnekteki TCP dinleyicisi, zaman aşımı süresi boyunca hiç yanıt vermez.  Araştırma, araştırmadan önce yanıtlanmak üzere yapılandırılan zaman aşımına uğramış araştırma istekleri sayısına göre aşağı işaretlenir.
* Araştırma, örnekten bir TCP sıfırlaması alır.

Aşağıda, Kaynak Yöneticisi şablonunda bu tür bir araştırma yapılandırmasını nasıl ifade ettiğiniz gösterilmektedir:

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="http--https-probe"></a><a name="httpprobe"></a><a name="httpsprobe"></a>Http/https araştırması

>[!NOTE]
>HTTPS araştırması yalnızca [Standart Load Balancer](./load-balancer-overview.md)için kullanılabilir.

HTTP ve HTTPS araştırmaları TCP araştırmasına dayanır ve belirtilen yola sahip bir HTTP GET oluşturur. Bu yoklamaların her ikisi de HTTP GET için göreli yolları destekler. HTTPS araştırmaları, bir Aktarım Katmanı Güvenliği (TLS, eskiden SSL olarak bilinen) sarmalayıcı ekleme ile HTTP araştırmaları ile aynıdır. Durum araştırması, örnek zaman aşımı süresi içinde bir HTTP durumu 200 ile yanıt verdiğinde yapılır.  Sistem durumu araştırması, varsayılan olarak her 15 saniyede bir yapılandırılan durum araştırma bağlantı noktasını denetlemeye çalışır. En düşük araştırma aralığı 5 saniyedir. Tüm aralıkların toplam süresi 120 saniyeyi aşamaz.

Araştırma bağlantı noktası Ayrıca hizmetin kendisi için de dinleyici olan yük dengeleyici rotasyondan örnekleri kaldırmak için kendi mantığınızı uygulamak üzere, HTTP/HTTPS araştırmaları da yararlı olabilir. Örneğin, %90 CPU üzerinde bir örneği kaldırmaya karar verebilir ve 200 olmayan bir HTTP durumu döndürebilir. 

> [!NOTE] 
> HTTPS araştırması, tüm zincirde en az bir SHA256 imza karması olan sertifikaların kullanılmasını gerektirir.

Cloud Services kullanırsanız ve w3wp.exe kullanan Web rollerine sahipseniz, Web sitenizin otomatik olarak izlenmesini de elde edersiniz. Web sitesi kodunuzda oluşan hatalarda yük dengeleyici araştırmasına 200 olmayan bir durum döndürülür.

Şu durumlarda bir HTTP/HTTPS araştırması başarısız olur:
* Araştırma uç noktası 200 dışında bir HTTP yanıt kodu döndürür (örneğin, 403, 404 veya 500). Bu, sistem durumu araştırmasını hemen işaretleyecek. 
* Araştırma bitiş noktası, en az yoklama aralığı ve 30 saniyelik zaman aşımı süresi boyunca yanıt vermez. Araştırma çalışmıyor olarak işaretlenmeden ve tüm zaman aşımı aralıklarının toplamına ulaşılıncaya kadar birden çok araştırma isteği yanıtlanmayabilir.
* Araştırma uç noktası, TCP sıfırlaması ile bağlantıyı kapatır.

Aşağıda, Kaynak Yöneticisi şablonunda bu tür bir araştırma yapılandırmasını nasıl ifade ettiğiniz gösterilmektedir:

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>Konuk Aracısı Araştırması (yalnızca klasik)

Bulut hizmeti rolleri (çalışan rolleri ve Web rolleri) varsayılan olarak araştırma izleme için bir Konuk Aracısı kullanır.  Konuk aracı araştırması, son çare bir yapılandırmadır.  Her zaman bir TCP veya HTTP araştırmasıyla bir sistem durumu araştırması kullanın. Konuk aracı araştırması, çoğu uygulama senaryosu için açıkça tanımlanmış yoklamalar kadar etkili değildir.

Konuk Aracısı araştırması, VM 'nin içindeki Konuk aracısının bir denetsıdır. Daha sonra bir HTTP 200 OK yanıtıyla dinler ve yanıt verir ancak örneğin, örnek zaten Ready durumundaysa. (Diğer durumlar meşgul, geri dönüşüm veya durduruluyor.)

Daha fazla bilgi için bkz. [sistem durumu araştırmaları için hizmet tanım dosyasını (csdef) yapılandırma](/previous-versions/azure/reference/ee758710(v=azure.100)) veya [bulut hizmetleri için bir genel yük dengeleyici oluşturma ile çalışmaya başlama](/previous-versions/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Konuk Aracısı HTTP 200 Tamam ile yanıt veremezse, yük dengeleyici örneği yanıt vermiyor olarak işaretler. Daha sonra bu örneğe akış göndermeyi bırakır. Yük dengeleyici örneği denetlemeye devam eder. 

Konuk Aracısı bir HTTP 200 ile yanıt verirse, yük dengeleyici yeni akışları bu örneğe yeniden gönderir.

Web rolü kullandığınızda, Web sitesi kodu genellikle Azure yapısı veya Konuk Aracısı tarafından izlenmeyen w3wp.exe çalışır. w3wp.exe hatalarda (örneğin, HTTP 500 yanıtları) Konuk aracısına bildirilmemiştir. Sonuç olarak, yük dengeleyici bu örneği döndürmeden almaz.

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>Araştırma davranışı

TCP, HTTP ve HTTPS sistem durumu araştırmaları sağlıklı olarak değerlendirilir ve arka uç uç noktasını şu durumlarda sağlıklı olarak işaretler:

* VM önyüklendikten sonra sistem durumu araştırması başarılı olur.
* Arka uç uç noktasını sağlıklı olarak işaretlemek için gereken yoklamaların belirtilen sayısı.

Sağlıklı bir durumu elde eden tüm arka uç uç noktaları, yeni akış almaya uygundur.  

> [!NOTE]
> Sistem durumu araştırması dalgalandıktan sonra, yük dengeleyici arka uç uç noktasını sağlıklı duruma getirmeden önce daha uzun süre bekler. Bu ek bekleme süresi, kullanıcıyı ve altyapıyı korur ve bilerek bir ilkedir.

## <a name="probe-down-behavior"></a><a name="probedown"></a>Araştırma davranışı

### <a name="tcp-connections"></a>TCP bağlantıları

Yeni TCP bağlantıları kalan sağlıklı arka uç uç noktası için başarılı olur.

Arka uç uç noktasının sistem durumu araştırması başarısız olursa, bu arka uç uç noktasına kurulan TCP bağlantıları devam eder.

Arka uç havuzundaki tüm örneklere yönelik tüm yoklamalar başarısız olursa, arka uç havuzuna yeni akış gönderilmez. Standart Load Balancer, belirlenen TCP akışlarının devam etmesine izin verir.  Temel Load Balancer, tüm mevcut TCP akışlarını arka uç havuzuna sonlandırır.
 
Load Balancer bir geçiş hizmetidir (TCP bağlantılarını sonlandıramaz) ve akış her zaman istemci ile sanal makinenin Konuk işletim sistemi ve uygulaması arasındadır. Tüm yoklamalar içeren bir havuz, akışı alacak ve bir SYN-ACK ile yanıt veren bir uç nokta olmadığından, ön uç, TCP bağlantı açma girişimlerini (SYN) yanıt vermemesine neden olur.

### <a name="udp-datagrams"></a>UDP veri birimleri

UDP veri birimleri sağlıklı arka uç uç noktalarına teslim edilecek.

UDP bağlantısız ve UDP için izlenen akış durumu yok. Arka uç uç noktasının sistem durumu araştırması başarısız olursa, mevcut UDP akışları arka uç havuzundaki başka bir sağlıklı örneğe taşınır.

Arka uç havuzundaki tüm örneklere yönelik tüm yoklamalar başarısız olursa, mevcut UDP akışları temel ve standart yük dengeleyiciler için sonlandırılır.

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>Araştırma kaynağı IP adresi

Load Balancer, iç sistem durumu modeli için dağıtılmış bir yoklama hizmeti kullanır. Yoklama hizmeti, sanal makinelerin bulunduğu her bir konakta bulunur ve müşterinin yapılandırmasına göre sistem durumu araştırmaları oluşturmak için isteğe bağlı olarak programlanabilir. Durum araştırma trafiği, sistem durumu araştırması ve müşteri VM 'si üreten araştırma hizmeti arasında doğrudan yapılır. Tüm Load Balancer sistem durumu araştırmaları, kaynak olarak 168.63.129.16 IP adresinden itibaren yapılır.  RFC1918 alanı olmayan bir VNet 'in içindeki IP adres alanını kullanabilirsiniz.  Küresel olarak ayrılmış, Microsoft 'un sahip olduğu IP adresi, VNet içinde kullandığınız IP adresi alanıyla bir IP adresi çakışması olasılığını azaltır.  Bu IP adresi tüm bölgelerde aynıdır ve değişmez ve yalnızca iç Azure platformu bileşeni bu IP adresinden bir paket kaynak sağladığından bir güvenlik riski değildir. 

AzureLoadBalancer Service etiketi, [ağ güvenlik gruplarınızı](../virtual-network/network-security-groups-overview.md) bu kaynak IP adresini tanımlar ve varsayılan olarak sistem durumu araştırma trafiğine izin verir.

Load Balancer sistem durumu araştırmalarının yanı sıra [aşağıdaki işlemler bu IP adresini kullanır](../virtual-network/what-is-ip-address-168-63-129-16.md):

- VM aracısının "Ready" durumunda olduğunu bildirmek için platformla iletişim kurmasını sağlar
- Özel DNS sunucuları tanımlamayan müşterilere filtrelenmiş ad çözümlemesi sağlamak için DNS sanal sunucusuyla iletişim sağlar.  Bu filtreleme, müşterilerin yalnızca dağıtımının ana bilgisayar adlarını çözümleyebilmesini sağlar.
- VM 'nin Azure 'daki DHCP hizmetinden dinamik bir IP adresi almasını sağlar.

## <a name="design-guidance"></a><a name="design"></a> Tasarım Kılavuzu

Sistem durumu araştırmaları, hizmetinizi dayanıklı hale getirmek ve ölçeklendirilmesine izin vermek için kullanılır. Yanlış yapılandırma veya hatalı tasarım deseninin kullanılabilmesi, hizmetinizin kullanılabilirliğini ve ölçeklenebilirliğini etkileyebilir. Tüm belgeyi gözden geçirin ve bu araştırma yanıtının ne kadar etkilendiğine veya ne zaman işaretleneceğini ve uygulama senaryonuzun kullanılabilirliğini nasıl etkilediğini düşünün.

Uygulamanız için sistem durumu modelini tasarlarken, bir arka uç uç noktasındaki bir bağlantı noktasını yoklamanız gerekir ve bu örnek sistem durumunu __ve__ sağladığınızda uygulama hizmetini yansıtır.  Uygulama bağlantı noktası ve yoklama bağlantı noktasının aynı olması gerekmez.  Bazı senaryolarda, araştırma bağlantı noktasının uygulamanızın hizmeti sağladığı bağlantı noktasından farklı olması istenebilir.  

Bazen uygulamanızın sistem durumunu algılamaması için bir sistem durumu araştırma yanıtı oluşturması faydalı olabilir, ancak aynı zamanda örneğinizin yeni akışlar alıp almamasının gerekip gerekmediğini Load Balancer doğrudan işaret edin.  Uygulamanızın, sistem durumu araştırmasını veya uygulamanızın bakımını hazırlamaya ve senaryonuzu boşaltmaya hazırlanarak, yeni akışların bir örneğe basıncını ve daha fazla teslimini oluşturmasına izin vermek için araştırma yanıtını değiştirebilirsiniz.  Standart Load Balancer kullanırken, [araştırma](#probedown) sinyali, boşta kalma zaman aşımı veya bağlantı kapanışına kadar her zaman TCP akışlarının devam etmesine izin verir. 

UDP yük dengelemesi için, arka uç uç noktasından özel bir sistem durumu araştırma sinyali oluşturmanız ve UDP uygulamanızın sistem durumunu yansıtmak için karşılık gelen dinleyiciyi hedefleyen bir TCP, HTTP veya HTTPS sistem durumu araştırması kullanmanız gerekir.

[Standart Load Balancer](./load-balancer-overview.md)Ile [ha bağlantı noktaları Yük Dengeleme kuralları](load-balancer-ha-ports-overview.md) kullanırken, tüm bağlantı noktaları yük dengedir ve tek bir sistem durumu araştırma yanıtı, tüm örneğin durumunu yansıtmalıdır.

Bu yapılandırma, senaryomunuzdan oluşan basamaklı hatalara yol açacağından, sistem durumu araştırmasını VNet 'teki başka bir örneğe alan örnek aracılığıyla bir sistem durumu araştırmasını veya proxy 'yi çevirmeyin.  Aşağıdaki senaryoyu göz önünde bulundurun: üçüncü taraf gereçlerin bir kümesi, gereçlere yönelik ölçek ve artıklık sağlamak üzere bir Load Balancer kaynağın arka uç havuzunda dağıtılır ve sistem durumu araştırması, üçüncü taraf gereç proxy 'lerinin bir bağlantı noktasını yoklamak veya gereç arkasındaki diğer sanal makinelere çevirilmesi için yapılandırılır.  Gerecin arkasındaki diğer sanal makinelere veya ara sunucu isteklerini çevirmek için kullandığınız bağlantı noktasını araştırdıysanız, gerecin arkasındaki tek bir sanal makineden gelen yoklama yanıtları, gerecin kendisini ölü olarak işaretler. Bu yapılandırma, gerecin arkasındaki tek bir arka uç uç noktasının sonucu olarak tüm uygulama senaryosunun basamaklı bir hatasına neden olabilir.  Tetikleyici, Load Balancer özgün hedefi (gereç örneği) işaretlemesine neden olacak ve sonra tüm uygulama senaryonuzu devre dışı bırakabilmesi için aralıklı bir araştırma hatası olabilir. Bunun yerine gereç durumunu araştırma. Sistem durumu sinyalinin belirlenmesi için araştırmanın seçimi, ağ sanal gereçleri (NVA) senaryolarında önemli bir noktadır ve bu senaryolar için uygun sistem durumu sinyalinin ne olduğunu öğrenmek için uygulama satıcınıza başvurmanız gerekir.

Güvenlik Duvarı ilkeleriniz için araştırmanın [kaynak IP 'si](#probesource) izin vermezseniz, örneğinize ulaşamadığından sistem durumu araştırması başarısız olur.  Sırasıyla, durum araştırma hatası nedeniyle Load Balancer örneğinizi işaretleyecek.  Bu yanlış yapılandırma, yük dengeli uygulama senaryonuzun başarısız olmasına neden olabilir.

Örneğinizi işaretlemek için Load Balancer sistem durumu araştırması için, tüm Azure [ağ güvenlik gruplarında](../virtual-network/network-security-groups-overview.md) ve yerel güvenlik duvarı ILKELERINDE bu IP adresine izin vermeniz **gerekir** .  Varsayılan olarak, her ağ güvenlik grubu, durum araştırma trafiğine izin vermek için AzureLoadBalancer [hizmet etiketini](../virtual-network/network-security-groups-overview.md#service-tags) içerir.

Bir sistem durumu araştırma hatasını test etmek veya tek bir örneği işaretlemek isterseniz, sistem durumu araştırmasını (hedef bağlantı noktası veya [kaynak IP](#probesource)) açıkça engellemek ve bir araştırmanın başarısızlığının benzetimini yapmak için bir [ağ güvenlik grubu](../virtual-network/network-security-groups-overview.md) kullanabilirsiniz.

VNet 'i 168.63.129.16 içeren, Microsoft 'un sahip olduğu IP adresi aralığıyla yapılandırmayın.  Bu tür yapılandırmalarda sistem durumu araştırmasının IP adresi ile çakışacaktır ve senaryonuzun sürmesine neden olabilir.

VM 'niz üzerinde birden fazla arabiriminiz varsa, bunu aldığınız arabirimdeki araştırmayı yanıtlamanıza gerek duyarsınız.  Bu adresi VM 'de her arabirim için kaynak ağ adresi çevirisi yapmanız gerekebilir.

[TCP zaman damgalarını](https://tools.ietf.org/html/rfc1323)etkinleştirmeyin.  TCP zaman damgalarının etkinleştirilmesi, sanal makinenin Konuk işletim sistemi TCP yığını tarafından bırakılan TCP paketlerinin, ilgili uç nokta ile Load Balancer sonuçlanmasına neden olacak şekilde sistem durumu araştırmalarının başarısız olmasına neden olabilir.  Güvenlik sağlamlaştırılmış VM görüntülerinde varsayılan olarak TCP zaman damgaları etkinleştirilir ve devre dışı bırakılmalıdır.

## <a name="monitoring"></a>İzleme

Hem genel hem de dahili [Standart Load Balancer](./load-balancer-overview.md) , Azure izleyici aracılığıyla çok boyutlu ölçümler olarak uç nokta ve arka uç uç nokta durumu araştırma durumunu kullanıma sunar. Bu ölçümler diğer Azure hizmetleri veya iş ortağı uygulamaları tarafından tüketilebilir. 

Temel genel Load Balancer, Azure Izleyici günlükleri aracılığıyla arka uç havuzu başına özetlenen durum araştırma durumunu gösterir.  Azure Izleyici günlükleri, iç temel yük dengeleyiciler için kullanılamaz.  [Azure izleyici günlüklerini](load-balancer-monitor-log.md) kullanarak genel yük dengeleyici araştırma sistem durumunu ve araştırma sayısını denetleyebilirsiniz. Günlüğe kaydetme, yük dengeleyici sistem durumu hakkında istatistikler sağlamak için Power BI veya Azure operasyonel içgörüler ile kullanılabilir.

## <a name="limitations"></a>Sınırlamalar

- HTTPS araştırmaları, istemci sertifikasıyla karşılıklı kimlik doğrulamayı desteklemez.
- TCP zaman damgaları etkinleştirildiğinde sistem durumu araştırmalarının başarısız olacağını varsayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Standart Load Balancer](./load-balancer-overview.md) hakkında daha fazla bilgi edinin
- [PowerShell kullanarak Kaynak Yöneticisi bir genel yük dengeleyici oluşturmaya başlama](quickstart-load-balancer-standard-public-powershell.md)
- [Sistem durumu araştırmaları için REST API](/rest/api/load-balancer/loadbalancerprobes/)
- [Load Balancer UserVoice](https://aka.ms/lbuservoice) ile yeni durum araştırma becerileri isteyin
