---
title: Ağınızı kurma
description: IoT gereçlerinde Azure Defender ile çalışacak şekilde ağınızı başarıyla ayarlamış olduğunuzdan emin olmak için çözüm mimarisi, ağ hazırlama, ön koşullar ve diğer bilgiler hakkında bilgi edinin.
ms.date: 02/18/2021
ms.topic: how-to
ms.openlocfilehash: ed75995b7cbc3b051d77942698a5d0d948e6eef0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781815"
---
# <a name="about-azure-defender-for-iot-network-setup"></a>IoT Ağ kurulumu için Azure Defender hakkında

IoT için Azure Defender, sürekli ICS tehdit izleme ve cihaz bulma özellikleri sunar. Platform aşağıdaki bileşenleri içerir:

**IoT sensörleri Için Defender:** Sensörler, pasif (aracısız) izleme kullanarak ICS ağ trafiğini toplar. Pasif ve kesintisiz bir şekilde, sensör, OT ve IoT ağları ve cihazları üzerinde sıfır performans etkisine sahiptir. Algılayıcı bir SPAN bağlantı noktasına veya ağa DOKUNMASıNA bağlanır ve ağınızı izlemeye hemen başlar. Algılamalar, algılayıcı konsolunda görüntülenir. Burada, bunları bir ağ eşlemesinde, bir cihaz envanterinde ve kapsamlı bir rapor yelpazğunda görüntüleyebilir, araştırabilir ve çözümleyebilirsiniz. Risk değerlendirmesi raporlarını, veri araştırma sorgularını ve saldırı vektörlerini örnek olarak içerir. 

**IoT şirket içi yönetim konsolu Için Defender**: şirket içi yönetim konsolu tüm ağ cihazlarının birleştirilmiş bir görünümünü sağlar. Tüm tesisleriniz genelinde anahtar OT ve IoT risk göstergelerinin ve uyarılarının gerçek zamanlı bir görünümünü sunar. SOC iş akışlarınızla ve PlayBook 'lar ile sıkı bir şekilde tümleştirilmiş, risk azaltma etkinliklerinin ve siteler arası tehditlerin kolay bir şekilde önceliklendirilmesini sunar. 

**IoT portalı Için Defender:** IoT için Defender uygulaması, çözüm gereçlerini satın almanıza, yazılım yüklemenize ve güncelleştirmenize ve TI paketlerini güncelleştirmenize yardımcı olabilir. 

Bu makalede, IoT gereçlerine yönelik Defender ile çalışacak şekilde ağınızı başarıyla ayarlamanıza yardımcı olacak çözüm mimarisi, ağ hazırlama, ön koşullar ve daha fazlası hakkında bilgi sağlanır. Bu makaledeki bilgilerle çalışan okuyucular, işletim sistemlerinde ve bu durumda OT ve IoT ağlarının yönetilmesi ile karşılaşılmalıdır. Otomasyon mühendisleri, tesis yöneticileri, OT ağ altyapısı hizmet sağlayıcıları, siber güvenlik ekipleri, Cıos veya Cıos örnekleri sayılabilir.

Yardım veya destek için [Microsoft desteği](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)başvurun.

## <a name="on-site-deployment-tasks"></a>Yerinde dağıtım görevleri

Site dağıtım görevleri şunları içerir:

- [Site bilgilerini topla](#collect-site-information)

- [Yapılandırma iş istasyonunu hazırlama](#prepare-a-configuration-workstation)

- [Raf yükleme planlaması](#planning-rack-installation)

### <a name="collect-site-information"></a>Site bilgilerini topla

Site bilgilerini aşağıda gösterildiği gibi kaydedin:

- Algılayıcı yönetimi ağ bilgileri.

- Site ağ mimarisi.

- Fiziksel ortam.

- Sistem tümleştirmeleri.

- Planlanmış Kullanıcı kimlik bilgileri.

- Yapılandırma iş istasyonu.

- SSL sertifikaları (isteğe bağlı ancak önerilir).

- SMTP kimlik doğrulaması (isteğe bağlı). SMTP sunucusunu kimlik doğrulamasıyla kullanmak için sunucunuz için gereken kimlik bilgilerini hazırlayın.

- DNS sunucuları (isteğe bağlı). DNS sunucunuzun IP ve ana bilgisayar adını hazırlayın.

Önemli site bilgilerinin ayrıntılı bir listesi ve açıklaması için bkz. [örnek site kitabı](#example-site-book).

#### <a name="successful-monitoring-guidelines"></a>Başarılı izleme yönergeleri

Her üretim ağlarınızdaki gereci bağlamaya en uygun yeri bulmak için şu yordamı izlemeniz önerilir:

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="Üretim ağı kurulumunun Diyagram örneği.":::

### <a name="prepare-a-configuration-workstation"></a>Yapılandırma iş istasyonunu hazırlama

Aşağıdakiler dahil olmak üzere bir Windows iş istasyonu hazırlayın:

- Algılayıcı yönetimi arabirimine bağlantı.

- Desteklenen bir tarayıcı

- PuTTY gibi terminal yazılımı.

Gerekli güvenlik duvarı kurallarının iş istasyonunda açık olduğundan emin olun. Ayrıntılar için bkz. [ağ erişim gereksinimleri](#network-access-requirements) .

#### <a name="supported-browsers"></a>Desteklenen tarayıcılar

Aşağıdaki tarayıcılar, sensörler ve şirket içi yönetim konsolu Web uygulamaları için desteklenir:

- Chrome 32 +

- Microsoft Edge 86 +

- Internet Explorer 10 +

### <a name="network-access-requirements"></a>Ağ erişim gereksinimleri

Kurumsal güvenlik ilkenizin aşağıdakilere erişime izin verdiğini doğrulayın:

| Protokol | Aktarım | Gelen/giden | Bağlantı noktası | Kullanılan | Amaç | Kaynak | Hedef |
|--|--|--|--|--|--|--|--|
| HTTPS | TCP | GELEN/GIDEN | 443 | Algılayıcı ve şirket Içi Yönetim Konsolu Web Konsolu | Web konsoluna erişim | İstemci | Algılayıcı ve şirket içi yönetim konsolu |
| SSH | TCP | GELEN/GIDEN | 22 | CLI | CLı erişimi | İstemci | Algılayıcı ve şirket içi yönetim konsolu |
| SSL | TCP | GELEN/GIDEN | 443 | Algılayıcı ve şirket içi yönetim konsolu | Six platformu ve merkezi yönetim platformu arasında bağlantı | algılayıcı | Şirket içi yönetim konsolu |
| NTP | UDP | IN | 123 | Zaman eşitleme | Şirket içi yönetim konsolu, algılayıcı için NTP olarak kullanın | algılayıcı | Şirket içi yönetim konsolu |
| NTP | UDP | GELEN/GIDEN | 123 | Zaman eşitleme | Şirket içi yönetim konsolunun yüklü olmadığı durumlarda, dış NTP sunucusuna bağlı algılayıcı | algılayıcı | NTP |
| SMTP | TCP | DıŞı | 25 | E-posta | Six platformu ile yönetim platformu ve posta sunucusu arasındaki bağlantı | Algılayıcı ve şirket içi yönetim konsolu | E-posta sunucusu |
| Syslog | UDP | DıŞı | 514 | LEEF | Şirket içi yönetim konsolundan Syslog sunucusuna gönderilen Günlükler | Şirket içi yönetim konsolu ve algılayıcı | Syslog sunucusu |
| DNS |  | GELEN/GIDEN | 53 | DNS | DNS sunucusu bağlantı noktası | Şirket içi yönetim konsolu ve algılayıcı | DNS sunucusu |
| LDAP | TCP | GELEN/GIDEN | 389 | Active Directory | Six platformu ile yönetim platformu arasındaki bağlantı Active Directory | Şirket içi yönetim konsolu ve algılayıcı | LDAP sunucusu |
| LDAPS | TCP | GELEN/GIDEN | 636 | Active Directory | Six platformu ile yönetim platformu arasındaki bağlantı Active Directory | Şirket içi yönetim konsolu ve algılayıcı | LDAPS sunucusu |
| SNMP | UDP | DıŞı | 161 | İzleme | Uzak SNMP toplayıcıları. | Şirket içi yönetim konsolu ve algılayıcı | SNMP sunucusu |
| WMI | UDP | DıŞı | 135 | izleme | Windows uç nokta Izleme | Algılayıcısı | İlgili ağ öğesi |
| Tünel | TCP | IN | 9000 <br /><br />-bağlantı noktası 443 ' nin üstünde <br /><br />Son kullanıcıdan şirket içi yönetim konsoluna. <br /><br />-Noktadan şirket içi yönetim konsoluna kadar olan bağlantı noktası 22  | izleme | Tünel | Algılayıcısı | Şirket içi yönetim konsolu |

### <a name="planning-rack-installation"></a>Raf yükleme planlaması

Raf yüklemenizi planlamak için:

1. Gereç ağı ayarlarınıza bir izleyici ve klavye hazırlayın.

1. Gereç için raf alanını ayırın.

1. Gereç için AC gücü kullanılabilir.
1. Yönetim bağlantısını ağ anahtarına bağlamak için LAN kablosunu hazırlayın.
1. Anahtar yayma (yansıtma) bağlantı noktalarını ve ya da ağ tap 'ları IoT gereci için Defender 'a bağlamak üzere LAN kablolarını hazırlayın. 
1. Mimari inceleme oturumunda açıklandığı gibi yansıtılan anahtarların YAYıLMA bağlantı noktalarını yapılandırın, bağlayın ve doğrulayın.
1. Yapılandırılmış YAYıLMA bağlantı noktasını Wireshark çalıştıran bir bilgisayara bağlayın ve bağlantı noktasının doğru şekilde yapılandırıldığını doğrulayın.
1. İlgili tüm güvenlik duvarı bağlantı noktalarını açın.

## <a name="about-passive-network-monitoring"></a>Pasif ağ izleme hakkında

Gereç, birden fazla kaynaktan gelen trafiği anahtar yansıtma bağlantı noktaları (SPAN bağlantı noktaları) ya da ağ dokunmasıyla alır. Yönetim bağlantı noktası, şirket içi yönetim konsoluna veya IoT portalı için Defender 'a bağlantı ile iş, kuruluş veya algılayıcı yönetim ağına bağlanır.

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="Bir yönetilen anahtarın bağlantı noktası yansıtmayla diyagramı.":::

### <a name="purdue-model"></a>Takip tarihi modeli

Aşağıdaki bölümlerde, takip eden düzeyler açıklanır.

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="Purdue modelinin diyagramı.":::

####  <a name="level-0-cell-and-area"></a>Düzey 0: hücre ve alan  

Düzey 0, temel üretim sürecinde yer alan çok sayıda sensörden, Atlayıcıları ve cihazdan oluşur. Bu cihazlar, endüstriyel otomasyon ve denetim sisteminin temel işlevlerini gerçekleştirir, örneğin:

- Motor sürüş.

- Değişkenleri ölçme.
- Çıkış ayarlama.
- Boyama, kaynak ve döndürme gibi önemli işlevleri gerçekleştirme.

#### <a name="level-1-process-control"></a>Düzey 1: Işlem denetimi

Düzey 1, anahtar işlevi düzey 0 cihazlarla iletişim kuran üretim işlemini denetleyen ve işleyen katıştırılmış denetleyicilerden oluşur. Ayrık üretimde, bu cihazlar Programlanabilir Logic Controllers (PLCs) veya uzak telemetri birimleridir (DTU 'lar). İşlem üretimi ' nde, temel denetleyiciye dağıtılmış denetim sistemi (DC) denir.

#### <a name="level-2-supervisory"></a>Düzey 2: denetim

Düzey 2, bir üretim tesis alanının çalışma zamanı gözetiminin ve işlemiyle ilişkili olan sistemleri ve işlevleri temsil eder. Bunlar genellikle aşağıdakileri içerir: 

- İşleç arabirimleri veya HMIS

- Alarmlar veya uyarı sistemleri

- İşlem histora ve Batch yönetim sistemleri

- Denetim odası iş istasyonları

Bu sistemler, düzey 1 ' deki PLCs ve RS 'Ler ile iletişim kurar. Bazı durumlarda, site veya kuruluş (düzey 4 ve düzey 5) sistemleri ve uygulamaları ile iletişim kurar veya veri paylaşır. Bu sistemler öncelikle standart bilgi işlem donanımını ve işletim sistemlerini (UNIX veya Microsoft Windows) temel alır.

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>Düzey 3 ve 3,5: Site düzeyi ve endüstriyel çevre ağı

Site düzeyi, en üst düzeyde endüstriyel otomasyon ve denetim sistemlerini temsil eder. Bu düzeyde bulunan sistemler ve uygulamalar, site genelinde endüstriyel otomasyon ve denetim işlevlerini yönetir. 0 ile 3 arası düzeyler site işlemleri için kritik olarak değerlendirilir. Bu düzeyde bulunan sistemler ve işlevler şunları içerebilir:

- Üretim raporlama (örneğin, Bisiklet süreleri, kalite dizini, tahmine dayalı bakım)

- Tesis histora

- Ayrıntılı üretim zamanlaması

- Site düzeyi işlem yönetimi

- Cihaz ve malzeme yönetimi

- Düzeltme Eki başlatma sunucusu

- Dosya sunucusu

- Endüstriyel etki alanı, Active Directory, Terminal sunucusu

Bu sistemler, üretim bölgesiyle iletişim kurar ve kurumsal (düzey 4 ve düzey 5) sistemlerle ve uygulamalarla verileri paylaşır.  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>Düzey 4 ve 5: Iş ve kurumsal ağlar

Düzey 4 ve düzey 5, merkezi BT sistemleri ve işlevlerinin mevcut olduğu siteyi veya kurumsal ağı temsil eder. BT kuruluşu Hizmetleri, sistemleri ve uygulamaları bu düzeylerde doğrudan yönetir.

## <a name="planning-for-network-monitoring"></a>Ağ izleme planlaması

Aşağıdaki örneklerde, endüstriyel denetim ağları için farklı türlerde topolojiler ve sensörler için en iyi izleme ve yerleştirme konuları bulunur.

### <a name="what-should-be-monitored"></a>Nelerin izlenmesi gerekir?

Katman 1 ve 2 ' den geçen trafik izlenmelidir.

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>IoT gerecinin Defender 'a bağlanması gerekenler nelerdir?

IoT gereci için Defender, 1 ve 2. Katmanlar arasındaki endüstriyel iletişimleri (bazı durumlarda katman 3) görmek için yönetilen anahtarlara bağlanmalıdır.

Aşağıdaki diyagram, genellikle bir SOC ve MSSP tarafından işletilen expansiber güvenlik ekosistemi olan çok katmanlı, çok kiracılı bir ağın genel bir soyutlamasıdır.

Genellikle, NTA algılayıcılar, OSı modelinin 3 ' e 3 ' e kadar olan katmanlarda dağıtılır.

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="OSı modelinin diyagramı.":::

#### <a name="example-ring-topology"></a>Örnek: halka topolojisi

Halka ağı, her bir anahtarın veya düğümün iki farklı anahtara bağlandığı bir topolojidir ve trafik için tek bir sürekli patika oluşturulur.

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="Halka topolojisi diyagramı.":::

#### <a name="example-linear-bus-and-star-topology"></a>Örnek: doğrusal veri yolu ve yıldız topolojisi

Bir yıldız ağında, her ana bilgisayar bir merkezi hub 'a bağlanır. En basit biçimde, bir merkezi hub, ileti ileten bir iletken görevi görür. Aşağıdaki örnekte, daha düşük anahtarlar izlenmiyor ve bu anahtarlar için yerelde kalan trafik görülmeyecektir. Cihazlar ARP iletileri temel alınarak tanımlanabilir, ancak bağlantı bilgileri eksik olur.

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="Doğrusal veri yolu ve yıldız topolojisi diyagramı.":::

#### <a name="multisensor-deployment"></a>Çoklu algılayıcı dağıtımı

Birden çok sensöri dağıtmaya yönelik bazı öneriler aşağıda verilmiştir:

| **Sayı** | **Gösterilmiştir** | **Bağımlılık** | **Algılayıcı sayısı** |
|--|--|--|--|
| Anahtarlar arasındaki en fazla uzaklık | 80 ölçü | Hazırlanmış Ethernet kablosu | 1 ' den fazla |
| OT ağı sayısı | 1 ' den fazla | Fiziksel bağlantı yok | 1 ' den fazla |
| Anahtar sayısı | RSPAN yapılandırması kullanılabilir | Yerel yayılmasına sahip en fazla sekiz anahtar, en çok kablo mesafeden algılayıcıya yakın | 1 ' den fazla |

#### <a name="traffic-mirroring"></a>Trafik yansıtma  

Trafik analizinde yalnızca ilgili bilgileri görmek için, IoT for IoT platformunu anahtar üzerindeki bir yansıtma bağlantı noktasına veya yalnızca endüstriyel ICS ve SCADA trafiğini içeren bir dokunarak bağlamanız gerekir. 

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="Kurulum için bu anahtarı kullanın.":::

Aşağıdaki yöntemleri kullanarak, anahtar trafiğini izleyebilirsiniz:

- [Anahtar YAYıLıMı bağlantı noktası](#switch-span-port)

- [Uzak Aralık (RSPAN)](#remote-span-rspan)

- [Etkin ve pasif toplama dokunma](#active-and-passive-aggregation-tap)

SPAN ve RSPAN, Cisco terimlerdir. Diğer anahtarların marka işlevleri benzer işlevlere sahiptir ancak farklı terminoloji kullanabilir.

#### <a name="switch-span-port"></a>Anahtar YAYıLıMı bağlantı noktası

Anahtar bağlantı noktası Çözümleyicisi, aynı anahtardaki arayüzdeki arayüzlerden gelen yerel trafiği yansıtır. Bazı konular aşağıda verilmiştir:

- İlgili anahtarın bağlantı noktası yansıtma işlevini desteklediğini doğrulayın.  

- Yansıtma seçeneği varsayılan olarak devre dışıdır.

- Hiçbir veri bağlanmasa bile, anahtarın tüm bağlantı noktalarını yapılandırmanızı öneririz. Aksi takdirde, standart dışı bir cihaz izlenmeyen bir bağlantı noktasına bağlı olabilir ve sensörde uyarı vermez.

- Yayın veya çok noktaya yayın mesajlaşma kullanan OT ağlarında, anahtarı yalnızca RX (Al) iletimini yansıtacak şekilde yapılandırın. Aksi takdirde, çok noktaya yayın iletileri çok sayıda etkin bağlantı noktası için yinelenir ve bant genişliği çarpılır.

Aşağıdaki yapılandırma örnekleri yalnızca başvuru amaçlıdır ve IOS çalıştıran bir Cisco 2960 anahtarını (24 bağlantı noktası) temel alır. Bunlar yalnızca tipik örneklerdir, bu nedenle bunları yönergeler olarak kullanmayın. Diğer Cisco işletim sistemlerindeki yansıtma bağlantı noktaları ve diğer anahtar markalar farklı şekilde yapılandırılır.

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="Bir SPAN bağlantı noktası yapılandırma terminalinin diyagramı.":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="YAYıLMA bağlantı noktası yapılandırma modunun diyagramı.":::

##### <a name="monitoring-multiple-vlans"></a>Birden çok VLAN izleme

IoT için Defender, ağınızda yapılandırılmış VLAN 'Ların izlenmesini sağlar. IoT sistemi için Defender yapılandırması gerekli değildir. Kullanıcının, ağınızdaki anahtarın IoT için Defender 'a VLAN etiketleri gönderecek şekilde yapılandırıldığından emin olması gerekir.

Aşağıdaki örnekte, IoT için Defender 'da VLAN 'Ları izlemeyi etkinleştirmek üzere Cisco anahtarında yapılandırılması gereken komutlar gösterilmektedir:

**İzleme oturumu**: Bu komut, yayılma bağlantı noktasına VLAN gönderme işleminden sorumludur.

- izleme oturumu 1 kaynak arabirimi Gi1/2

- izleme oturumu 1 filtre paket türü iyi RX

- izleme oturumu 1 hedef arabirimi fastEthernet1/1 kapsülleme dot1q

**Izleme santral bağlantı noktası F.E. Gi1/1**: VLAN bağlantı noktasında VLAN 'lar yapılandırılır.

- arabirim GigabitEthernet1/1

- switchport santral kapsülleme dot1q

- switchport modu santral

#### <a name="remote-span-rspan"></a>Uzak Aralık (RSPAN)

Uzak YAYıLMA oturumu, birden fazla dağıtılmış kaynak bağlantı noktasından gelen trafiği ayrılmış bir uzak VLAN 'a yansıtır.  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="Uzak YAYıLıMıN diyagramı.":::

Daha sonra VLAN 'daki veriler, fiziksel hedef bağlantı noktasını içeren belirli bir anahtara birden çok anahtar arasında santrale bağlantı noktaları üzerinden dağıtılır. Bu bağlantı noktası, IoT Platformu için Defender 'a bağlanır.  

##### <a name="more-about-rspan"></a>RSPAN hakkında daha fazla bilgi

- RSPAN, anahtarlar arasında izleyicileri kapsayan trafiği taşımak için özel bir VLAN gerektiren gelişmiş bir özelliktir. RSPAN tüm anahtarlarda desteklenmez. Anahtarın RSPAN işlevini desteklediğini doğrulayın.

- Yansıtma seçeneği varsayılan olarak devre dışıdır.

- Kaynak ve hedef anahtarlar arasındaki santrale bağlantı noktasında uzak VLAN 'a izin verilmelidir.

- Aynı RSPAN oturumunu bağlayan tüm anahtarlar aynı satıcıdan olmalıdır.

> [!NOTE]
> Anahtarlar arasındaki uzak VLAN 'ı paylaşan santral bağlantı noktasının bir yansıtma oturumu kaynak bağlantı noktası olarak tanımlanmadığından emin olun.
>
> Uzak VLAN, santrale bağlantı noktasındaki bant genişliğini yansıtılmış oturumun bant genişliğinin boyutuyla artırır. Anahtarın santral bağlantı noktasının bunu desteklediğini doğrulayın.  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="Uzak VLAN diyagramı.":::

#### <a name="rspan-configuration-examples"></a>RSPAN yapılandırma örnekleri

RSPAN: Cisco Catalyst 2960 (24 bağlantı noktası) tabanlı.

**Kaynak anahtarı yapılandırma örneği:**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="RSPAN yapılandırması ekran görüntüsü.":::

1. Genel yapılandırma modunu girin.

1. Adanmış bir VLAN oluşturun.

1. VLAN 'ı RSPAN VLAN olarak belirler.

1. "Terminal yapılandırma" moduna geri dönün.

1. Tüm 24 bağlantı noktalarını oturum kaynakları olarak yapılandırın.

1. RSPAN VLAN 'ı oturum hedefi olacak şekilde yapılandırın.

1. Ayrıcalıklı EXEC moduna geri dönün.

1. Bağlantı noktası yansıtma yapılandırmasını doğrulayın.

**Hedef anahtar yapılandırma örneği:**

1. Genel yapılandırma modunu girin.

1. RSPAN VLAN 'ı oturum kaynağı olacak şekilde yapılandırın.

1. Fiziksel bağlantı noktası 24 ' ü oturum hedefi olacak şekilde yapılandırın.

1. Ayrıcalıklı EXEC moduna geri dönün.

1. Bağlantı noktası yansıtma yapılandırmasını doğrulayın.

1. Yapılandırmayı kaydedin.

#### <a name="active-and-passive-aggregation-tap"></a>Etkin ve pasif toplama dokunma

Etkin veya pasif bir toplama dokunma, ağ kablosunu satır içi olarak yüklenir. Her iki RX ve TX ' i izleme sensörlerine çoğaltır.

Terminal erişim noktası (TAP), ağ trafiğinin bağlantı noktasından B 'ye ve bağlantı noktası B 'den bağlantı noktası A 'ya kesintisiz olarak akmasını sağlayan bir donanım aygıtıdır. Trafik akışının her iki tarafının da sürekli olarak ağ bütünlüğünden ödün vermeden tam bir kopyasını oluşturur. Bazı dokunmalar, istenirse anahtar ayarlarını kullanarak iletme ve trafik alma ' yı toplar. Toplama desteklenmiyorsa, her bir dokunma trafiği gönderme ve alma trafiğini izlemek için iki algılayıcı bağlantı noktası kullanır.

Dokunmalar çeşitli nedenlerle avantajlıdır. Bunlar donanım tabanlıdır ve tehlikeye girebilir. Tüm trafik, hatta hasar gören iletiler bile geçer ve bu anahtarlar genellikle düşürüktir. İşlemciye duyarlı değildir, bu nedenle paket zamanlaması, anahtarların yansıtma işlevini, yansıtılmış paketlerin zamanlamasını etkileyebilecek düşük öncelikli bir görev olarak işleyeceği bir tam değer. Adli amaçlar için en iyi cihaz bir dokunma olur.

Bağlantı noktası izleme için, toplayıcısını değiştirme 'lar de kullanılabilir. Bu cihazlar, işlemci tabanlıdır ve donanım dokunmasıyla doğası gereği güvenli değildir. Bunlar tam paket zamanlamasını yansıtmayabilir.

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="Etkin ve pasif dokunmalar diyagramı.":::

##### <a name="common-tap-models"></a>Ortak dokunma modelleri

Bu modeller uyumluluk için test edilmiştir. Diğer satıcılar ve modeller de uyumlu olabilir.

| Görüntü | Modelleme |
|--|--|
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Garland P1GCCAS ekran görüntüsü."::: | Garland P1GCCAS |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="IXIA TPA2-CU3 ekran görüntüsü."::: | IXIA TPA2-CU3 |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="US Robotics USR 4503 ekran görüntüsü."::: | US Robotics USR 4503 |

##### <a name="special-tap-configuration"></a>Özel dokunma yapılandırması

| Garland TAP | ABD Robotics TAP |
| ----------- | --------------- |
| Atlatıcı anahtarları aşağıdaki gibi ayarlandığından emin olun:<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="US Robotics anahtarının ekran görüntüsü.":::| Toplama modunun etkin olduğundan emin olun. |

## <a name="deployment-validation"></a>Dağıtım doğrulaması

### <a name="engineering-self-review"></a>Mühendislik kendi kendine gözden geçirme  

OT ve ICS ağ diyagramınızı gözden geçirmek, en iyi şekilde bağlantı kurmak için en verimli yoldur. Bu, izleme için en uygun trafiği elde edebilirsiniz.

Site mühendislerinin ağın nasıl göründüğünü öğrenmiş. Yerel ağ ve operasyonel ekiplerle bir inceleme oturumunun olması, genellikle beklentileri açıklığa kavuşturacak ve gereci bağlamak için en iyi yeri tanımlamalıdır.

İlgili bilgiler:

- Bilinen cihazların listesi (elektronik tablo)  

- Tahmini cihaz sayısı  

- Satıcılar ve endüstriyel protokoller

- Anahtar modeli, bağlantı noktası yansıtma seçeneğinin kullanılabilir olduğunu doğrulamak için

- Anahtarları kimin yönettiğini (örneğin,) ve dış kaynak olup olmadıkları hakkında bilgi

- Sitedeki OT ağlarının listesi

#### <a name="common-questions"></a>Sık sorulan sorular

- Uygulamanın genel hedefleri nelerdir? Eksiksiz bir sayım ve doğru ağ eşlemi önemli mi?

- ICS 'de birden fazla veya yedekli ağ var mı? Tüm ağlar izleniyor mu?

- ICS ile kurumsal (iş) ağı arasında iletişim var mı? Bu iletişimler izleniyor mu?

- Ağ tasarımında VLAN 'Lar yapılandırılmış mı?

- BT 'nin bakımı, düzeltilen veya geçici cihazlarla nasıl yapılır?

- İzlenen ağlarda güvenlik duvarları nerede yüklenir?

- İzlenen ağlarda herhangi bir yönlendirme var mı?

- İzlenen ağlarda hangi OT protokolleri etkin?

- Bu anahtara bağlandığımızda, HMı ve PLCs arasında iletişim görüyoruz mi?

- ICS anahtarları ile kurumsal güvenlik duvarı arasındaki fiziksel uzaklık nedir? 

- Yönetilmeyen anahtarlar yönetilen anahtarlarla değiştirilebilir veya ağ, bir seçeneğe dokunmalıdır mi?

- Ağda herhangi bir seri iletişimi var mı? Yanıt Evet ise, ağ diyagramında göster.

- IoT gereci için Defender bu anahtara bağlanmalıdır, bu dolapta fiziksel olarak kullanılabilir raf alanı var mı?

#### <a name="other-considerations"></a>Diğer önemli noktalar

IoT gereci için Defender 'ın amacı, 1 ve 2. katmanlardan trafiği izmektir.

Bazı mimarilerde, bu katmanda trafik varsa, IoT gereci için Defender, katman 3 ' ü de izler. Site mimarisini gözden geçirirken ve bir anahtarı izlemeye karar verirken aşağıdaki değişkenleri göz önünde bulundurun:

- Bu anahtarı izlemenin önem derecesine karşı maliyet/avantaj nedir?

- Bir anahtar yönetilmiyorsa, daha üst düzey bir anahtardan trafiği izlemek mümkün olacaktır mi?

  ICS mimarisi bir halka topolojisi ise, bu halkadaki yalnızca bir anahtarın izlenmesi gerekir.

- Bu ağda güvenlik veya operasyonel risk nedir?

- Anahtarın VLAN 'ı izlemek mümkün mü? Bu VLAN, izleyediğimiz başka bir anahtarda görünebilir mi?

#### <a name="technical-validation"></a>Teknik doğrulama

Anahtar yayma (veya yansıtma) bağlantı noktasından kaydedilmiş trafik örneği (PCAP dosyası) alınması şunları yapmanıza yardımcı olabilir:

- Anahtarın doğru şekilde yapılandırılıp yapılandırılmadığını doğrulayın.

- Anahtardan gelen trafiğin izleme için uygun olup olmadığını onaylayın (OT trafiği).

- Bu anahtardaki bant genişliğini ve tahmini cihaz sayısını belirler.

Wireshark uygulaması aracılığıyla bir dizüstü bilgisayarı zaten yapılandırılmış bir YAYıLMA bağlantı noktasına bağlayarak örnek bir PCAP dosyası (birkaç dakika) kaydedebilirsiniz.

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="Bir YAYıLMA bağlantı noktasına bağlı dizüstü bilgisayarın ekran görüntüsü.":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="Örnek bir PCAP dosyası kaydının ekran görüntüsü.":::

#### <a name="wireshark-validation"></a>Wireshark doğrulaması

- *Tek noktaya yayın paketlerinin* kayıt trafiğinden mevcut olup olmadığını denetleyin. Tek noktaya yayın bir adresten diğerine. Trafiğin büyük bir kısmının ARP iletileri varsa, anahtar kurulumu yanlış olur.

- **İstatistik**  >  **protokol hiyerarşisine** gidin. Endüstriyel OT protokollerinin mevcut olduğunu doğrulayın.

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Wireshark doğrulamasının ekran görüntüsü.":::

## <a name="troubleshooting"></a>Sorun giderme

Sorun giderme sorunları için şu bölümleri kullanın:

- [Bir Web arabirimi kullanılarak bağlanamaz](#cant-connect-by-using-a-web-interface)

- [Gereç yanıt vermiyor](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>Bir Web arabirimi kullanılarak bağlanamaz

1. Bağlanmaya çalıştığınız bilgisayarın gereç ile aynı ağ üzerinde olduğundan emin olun.

2. GUI ağının, sensördeki yönetim bağlantı noktasına bağlı olduğunu doğrulayın.

3. Gereç IP adresine ping gönderin. Ping yapılacak yanıt yoksa:

    1. Bir izleyiciyi ve klavyeyi gereç ile bağlayın.

    1. Oturum açmak için **destek** kullanıcısını ve parolayı kullanın.

    1. Geçerli IP adresini görmek için komut **ağı listesini** kullanın.

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="Ağ listesi komutunun ekran görüntüsü.":::

4. Ağ parametreleri yanlış yapılandırılmış ise, bunu değiştirmek için aşağıdaki yordamı kullanın:

    1. **Ağ düzenleme ayarları** komutunu kullanın.

    1. Yönetim ağı IP adresini değiştirmek için **Y**' yi seçin.

    1. Alt ağ maskesini değiştirmek için **Y**' yi seçin.

    1. DNS 'yi değiştirmek için **Y**'yi seçin.

    1. Varsayılan ağ geçidi IP adresini değiştirmek için **Y**' yi seçin.

    1. Giriş arabirimi değişikliği (yalnızca algılayıcı için) için **Y**' yi seçin.

    1. Köprü arabirimi için **N**' yi seçin.

    1. Ayarları uygulamak için **Y**' yi seçin.

5. Yeniden başlattıktan sonra, Kullanıcı desteğiyle bağlanın ve parametrelerin değiştirildiğini doğrulamak için **ağ listesi** komutunu kullanın.

6. PING ve GUI 'den yeniden bağlanmayı deneyin.

### <a name="appliance-is-not-responding"></a>Gereç yanıt vermiyor

1. Gerecle bir izleyici ve klavye ile bağlanın veya CLı 'ya uzaktan bağlanmak için PuTTY kullanın.

2. Oturum açmak için destek kimlik bilgilerini kullanın.

3. **Sistem sağlamlık** komutunu kullanın ve tüm işlemlerin çalıştığını denetleyin.

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="Sistem sağlamlık komutunun ekran görüntüsü.":::

Diğer herhangi bir sorun için [Microsoft desteği](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)başvurun.

## <a name="example-site-book"></a>Örnek site kitabı

Ağ kurulumu için ihtiyaç duyduğunuz önemli bilgileri almak ve gözden geçirmek için örnek site kitabı ' nı kullanın.

### <a name="site-checklist"></a>Site denetim listesi

Site dağıtımından önce bu listeyi gözden geçirin:

| **#** | **Görev veya etkinlik** | **Durum** | **Açıklamalar** |
|--|--|--|--|
| 1 | Cihazları sipariş edin. | ☐ |  |
| 2 | Ağdaki alt ağların bir listesini hazırlayın. | ☐ |  |
| 3 | Üretim ağlarının VLAN listesini sağlayın. | ☐ |  |
| 4 | Ağdaki anahtar modellerinin bir listesini sağlayın. | ☐ |  |
| 5 | Endüstriyel ekipmanın satıcıların ve protokollerinin bir listesini sağlayın. | ☐ |  |
| 6 | Sensörler için ağ ayrıntılarını sağlayın (IP adresi, alt ağ, D-GW, DNS). | ☐ |  |
| 7 | Gerekli güvenlik duvarı kuralları ve erişim listesi oluşturun. | ☐ |  |
| 8 | Bağlantı noktası izleme için anahtarlar üzerinde kapsayıcı bağlantı noktaları oluşturun veya ağ dokunmayı istediğiniz şekilde yapılandırın. | ☐ |  |
| 9 | Algılayıcı gereçlerinde raf alanı hazırlayın. | ☐ |  |
| 10 | Personel için bir iş istasyonu hazırlayın. | ☐ |  |
| 11 | IoT raf cihazları için Defender için bir klavye, izleyici ve fare sağlayın. | ☐ |  |
| 12 | Gereçlerden oluşan raf ve kablo. | ☐ |  |
| 13 | Dağıtımı desteklemek için site kaynaklarını ayırın. | ☐ |  |
| 14 | Active Directory grupları veya yerel kullanıcılar oluşturun. | ☐ |  |
| 15 | Kurulum Eğitimi (kendi kendine öğrenme). | ☐ |  |
| 16 | Git veya git. | ☐ |  |
| 17 | Dağıtım tarihini zamanlayın. | ☐ |  |


| **Date** | **Not** | **Dağıtım tarihi** | **Not** |
|--|--|--|--|
| IoT için Defender |  | Site adı * |  |
| Name |  | Name |  |
| Konum |  | Konum |  |

#### <a name="architecture-review"></a>Mimari incelemesi

Endüstriyel ağ diyagramına genel bir bakış için, bir Defender for IoT donatımı için uygun konumu tanımlamanızı sağlayacak.

1.  Endüstriyel OT ortamının genel bir ağ diyagramını görüntüleyin. Örnek:

    :::image type="content" source="media/how-to-set-up-your-network/ot-global-network-diagram.png" alt-text="Küresel ağ için endüstriyel OT ortamının diyagramı.":::

    > [!NOTE]
    > IoT gereci için Defender, anahtardaki bağlantı noktaları arasındaki trafiği gösteren alt düzey bir anahtara bağlanmalıdır.  

2. İzlenecek yaklaşık ağ aygıtı sayısını belirtin. Bu bilgilere IoT portalı için Azure Defender aboneliğinizi eklerken ihtiyacınız olacak. Ekleme işlemi sırasında cihaz sayısını 1000 artışlarla girmeniz istenir.

3. Üretim ağları ve açıklama (isteğe bağlı) için bir alt ağ listesi sağlayın. 

    |  **#**  | **Alt ağ adı** | **Açıklama** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

4. Üretim ağlarının VLAN listesini sağlayın.

    | **#** | **VLAN adı** | **Açıklama** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

5. Anahtarların bağlantı noktası yansıtma özelliğine sahip olduğunu doğrulamak için, IoT Platformu için Defender 'ın bağlanması gereken anahtar modeli numaralarını belirtin:

    | **#** | **Anahtar** | **Modelleme** | **Trafik yansıtma desteği (SPAN, RSPAN veya None)** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

    Üçüncü bir taraf anahtarları yönetebilir mi? Y veya N 

    Evet ise kim? __________________________________ 

    İlkeleri nelerdir? __________________________________ 

    Örnek:

    - Siemens

    - Rockwell Otomasyonu – Ethernet veya IP

    - Acil son – DeltaV, omalı
    
6. Ağdaki bir seri bağlantı aracılığıyla iletişim kuran cihazlar var mı? Evet veya Hayır 

    Yanıt Evet ise, hangi seri iletişim protokolünü belirtin: ________________ 

    Yanıt Evet ise, ağ diyagramında cihazların seri protokolleriyle iletişim kurduğu ve nerede olduğu konusunda işaret ettiği durumlar: 
 
    <Add your network diagram with marked serial connection> 

7. QoS için varsayılan algılayıcı ayarı 1,5 MB/sn 'dir. Değiştirmek istediğinizi belirtin: ________________ 

   İş birimi (BU): ________________ 

### <a name="specifications-for-site-equipment"></a>Site Ekipmanı belirtimleri

#### <a name="network"></a>Ağ  

Algılayıcı gereci, bir ağ bağdaştırıcısı aracılığıyla anahtar yayma bağlantı noktasına bağlanır. Bu, başka bir adanmış ağ bağdaştırıcısı aracılığıyla yönetim için müşterinin kurumsal ağına bağlanır.

Şirket ağına bağlanacak algılayıcı NIC 'in adres ayrıntılarını belirtin: 

|  Öğe               | Gereç 1 | Gereç 2 | Gereç 3 |
| --------------- | ------------- | ------------- | ------------- |
| Gereç IP adresi    |               |               |               |
| Alt ağ          |               |               |               |
| Varsayılan ağ geçidi |               |               |               |
| DNS             |               |               |               |
| Konak adı       |               |               |               |

#### <a name="idraciloserver-management"></a>Idrac/ILO/sunucu yönetimi

|       Öğe          | Gereç 1 | Gereç 2 | Gereç 3 |
| --------------- | ------------- | ------------- | ------------- |
| Gereç IP adresi     |               |               |               |
| Alt ağ          |               |               |               |
| Varsayılan ağ geçidi |               |               |               |
| DNS             |               |               |               |

#### <a name="on-premises-management-console"></a>Şirket içi yönetim konsolu  

|       Öğe          | Etkin | Pasif (HA kullanırken) |
| --------------- | ------ | ----------------------- |
| IP Adresi             |        |                         |
| Alt ağ          |        |                         |
| Varsayılan ağ geçidi |        |                         |
| DNS             |        |                         |

#### <a name="snmp"></a>SNMP  

|   Öğe              | Ayrıntılar |
| --------------- | ------ |
| IP              |        |
| IP Adresi | |
| Kullanıcı adı | |
| Parola | |
| Kimlik doğrulaması türü | MD5 veya SHA |
| Şifreleme | DES veya AES |
| Gizli anahtar | |
| SNMP v2 topluluk dizesi |

### <a name="on-premises-management-console-ssl-certificate"></a>Şirket içi yönetim konsolu SSL sertifikası

Bir SSL sertifikası kullanmayı planlıyor musunuz? Evet veya Hayır

Yanıt Evet ise, hangi hizmeti oluşturmak için kullanacaksınız? Sertifikaya hangi öznitelikleri dahil edersiniz (örneğin, etki alanı veya IP adresi)?

### <a name="smtp-authentication"></a>SMTP kimlik doğrulaması

Uyarıları bir e-posta sunucusuna iletmek için SMTP kullanmayı planlıyorsunuz musunuz? Evet veya Hayır

Yanıt Evet ise, hangi kimlik doğrulama yöntemini kullanacağınızı kullanacaksınız?  

### <a name="active-directory-or-local-users"></a>Active Directory veya yerel kullanıcılar

Bir Active Directory site Kullanıcı grubu oluşturmak veya yerel kullanıcılar oluşturmak için bir Active Directory yöneticisiyle iletişim kurun. Kullanıcılarınızın dağıtım günü için hazırlamış olduğunuzdan emin olun. 

### <a name="iot-device-types-in-the-network"></a>Ağdaki IoT cihaz türleri

| Cihaz Türü | Ağdaki cihaz sayısı | Ortalama bant genişliği |
| --------------- | ------ | ----------------------- |
| Kamera | |
| X-ray makinesi | |

## <a name="see-also"></a>Ayrıca bkz.

[IoT yüklemesi için Defender hakkında](how-to-install-software.md)
