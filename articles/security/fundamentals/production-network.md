---
title: Azure üretim ağı
description: Bu makalede, Azure üretim ağının genel bir açıklaması sağlanmaktadır.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 7c0748e4ff1531649274834cb1e602c228f102e8
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726700"
---
# <a name="the-azure-production-network"></a>Azure üretim ağı
Azure üretim ağı kullanıcıları, kendi Azure uygulamalarına ve üretim ağını yöneten dahili Azure destek personeline erişen harici müşteriler içerir. Bu makalede, Azure üretim ağına bağlantı kurmaya yönelik güvenlik erişimi yöntemleri ve koruma mekanizmaları ele alınmaktadır.

## <a name="internet-routing-and-fault-tolerance"></a>Internet Yönlendirme ve hata toleransı
Küresel olarak yedekli iç ve dış Azure etki alanı adı hizmeti (DNS) altyapısı, birden fazla birincil ve ikincil DNS sunucusu kümeleriyle birlikte, hata toleransı sağlar. Aynı zamanda, dağıtılmış hizmet reddi (DDoS) saldırılarını engellemek ve Azure DNS hizmetlerinin bütünlüğünü korumak için NetScaler gibi ek Azure ağ güvenliği denetimleri kullanılır.

Azure DNS sunucuları birden çok veri merkezi tesisindedir. Azure DNS uygulama, Azure müşteri etki alanı adlarını genel olarak çözümlemek için ikincil ve birincil DNS sunucuları hiyerarşisini içerir. Etki alanı adları genellikle müşterinin hizmeti için sanal IP (VIP) adresini sarmalayan bir CloudApp.net adresine çözümlenir. Azure 'da benzersiz olan kiracı çevirisi 'nin iç adanmış IP (DIP) adresine karşılık gelen VIP, bu VIP 'den sorumlu Microsoft yük dengeleyiciler tarafından yapılır.

Azure, ABD içindeki coğrafi olarak dağıtılmış Azure veri merkezlerinde barındırılır ve güçlü, ölçeklenebilir mimari standartları uygulayan, son teknoloji ürünü yönlendirme platformları üzerine kurulmuştur. Önemli özellikleri arasında:

- Çok protokollü etiket anahtarlama (MPLS) tabanlı trafik mühendisliği, bir kesinti olması durumunda verimli bağlantı kullanımı ve hizmetin düzgün azalmasına sağlar.
- Ağlar "gereksinim ve bir" (N + 1) artıklık mimarisi veya daha iyi bir şekilde uygulanır.
- Dışarıdan, veri merkezleri, Redundantly 'in 1.200 ' den fazla internet hizmet sağlayıcısı ile birden çok eşleme noktasında bağlanmasını sağlayan adanmış, yüksek bant genişliğine sahip ağ devreleri tarafından sunulur. Bu bağlantı, sınır kapasitesinin 2.000 gigabayttan (GB/sn) fazla.

Microsoft, veri merkezleri arasında kendi ağ devrelerine sahip olduğundan, bu öznitelikler, geleneksel üçüncü taraf internet servis sağlayıcılarına gerek kalmadan Azure sunumunun 99,9 ve yüzde bir ağ kullanılabilirliği elde etmenize yardımcı olur.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Üretim ağı ve ilişkili güvenlik duvarları ile bağlantı
Azure ağ internet trafiği akış ilkesi, trafiği ABD içindeki en yakın bölgesel veri merkezinde bulunan Azure üretim ağına yönlendirir. Azure üretim veri merkezleri tutarlı ağ mimarisi ve donanım koruduğundan, aşağıdaki trafik akışı açıklaması tüm veri merkezlerine tutarlı bir şekilde uygulanır.

Azure için internet trafiği en yakın veri merkezine yönlendirildikten sonra, erişim yönlendiricilerine bir bağlantı oluşturulur. Bu erişim yönlendiricileri, Azure düğümleri ile müşteri tarafından oluşturulan VM 'Ler arasındaki trafiği yalıtmak için de görev yapar. Erişim ve kenar konumlarındaki ağ altyapısı cihazları, giriş ve Çıkış filtrelerinin uygulandığı sınır noktalarıdır. Bu yönlendiriciler, istenmeyen ağ trafiğini filtrelemek ve gerekirse trafik hızı sınırlarını uygulamak için katmanlı erişim denetimi listesi (ACL) aracılığıyla yapılandırılır. ACL tarafından izin verilen trafik yük dengeleyicileri yönlendirilir. Dağıtım yönlendiricileri yalnızca Microsoft tarafından onaylanan IP adreslerine izin vermek, kimlik sahtekarlığına karşı koruma sağlamak ve ACL 'Leri kullanan TCP bağlantıları kurmak için tasarlanmıştır.

Dış Yük Dengeleme cihazları, internet 'ten yönlendirilebilir IP 'lerden Azure iç IP 'lerine ağ adresi çevirisi (NAT) gerçekleştirmek için erişim yönlendiricilerinin arkasında bulunur. Cihazlar ayrıca paketleri geçerli üretim iç IP 'lerine ve bağlantı noktalarına yönlendirebilir ve iç üretim ağ adresi alanını açığa çıkarmak için bir koruma mekanizması işlevi görür.

Varsayılan olarak, Microsoft, daha sonra oturum açma ve tüm trafik dahil müşterilerin web tarayıcılarına aktarılan tüm trafik için Hypertext Transfer Protocol Secure (HTTPS) uygular. TLS v 1.2 kullanımı trafiğin akışı için güvenli bir tünel sağlar. Erişim ve çekirdek yönlendiricilerde ACL 'Ler, trafiğin kaynağının beklendiklerle tutarlı olmasını güvence altına alır.

Bu mimaride, geleneksel güvenlik mimarisine kıyasla önemli bir ayrım olması, adanmış donanım güvenlik duvarları, özelleştirilmiş yetkisiz giriş algılama veya önleme cihazları ya da normal şekilde olan diğer güvenlik gereçlerinin olmaması olabilir. Azure üretim ortamına bağlantılar yapılmadan önce bekleniyor. Müşteriler genellikle bu donanım güvenlik duvarı cihazlarını Azure ağında bekler; Ancak, Azure 'da hiçbiri işe alınır. Neredeyse özel olarak, bu güvenlik özellikleri, güvenlik duvarı özellikleri de dahil olmak üzere sağlam, çok katmanlı güvenlik mekanizmaları sağlamak için Azure ortamını çalıştıran yazılımda yerleşik olarak bulunur. Ayrıca, sınırın ve ilişkili kritik güvenlik cihazlarının genişlemesine kapsamı, önceki çizimde gösterildiği gibi, Azure çalıştıran yazılımlar tarafından yönetildiğinden daha kolay yönetilmesi ve stoklanmasını kolaylaştırır.

## <a name="core-security-and-firewall-features"></a>Çekirdek güvenlik ve güvenlik duvarı özellikleri
Azure, temel güvenlik yetkilendirme sınırını korumak üzere geleneksel bir ortamda genellikle beklenen güvenlik özelliklerini zorlamak için güçlü yazılım güvenliği ve güvenlik duvarı özelliklerini çeşitli düzeylerde uygular.

### <a name="azure-security-features"></a>Azure Güvenlik özellikleri
Azure, üretim ağı içinde ana bilgisayar tabanlı yazılım güvenlik duvarları uygular. Çekirdek Azure ortamında birçok çekirdek güvenlik ve güvenlik duvarı özelliği bulunur. Bu güvenlik özellikleri, Azure ortamında derinlemesine savunma stratejisini yansıtır. Azure 'daki müşteri verileri aşağıdaki güvenlik duvarları tarafından korunmaktadır:

**Hiper yönetici güvenlik duvarı (paket filtresi)** : Bu güvenlik duvarı Hiper yöneticide uygulanır ve yapı denetleyicisi (FC) Aracısı tarafından yapılandırılır. Bu güvenlik duvarı, VM içinde çalışan kiracıyı yetkisiz erişime karşı korur. Varsayılan olarak, bir VM oluşturulduğunda, tüm trafik engellenir ve sonra FC Aracısı, yetkili trafiğe izin vermek için filtreye kuralları ve özel durumları ekler.

Kuralların iki kategorisi burada programlanır:

- **Makine yapılandırması veya altyapı kuralları**: Varsayılan olarak, tüm iletişimler engellenir. Bir VM 'nin dinamik ana bilgisayar Yapılandırma Protokolü (DHCP) iletişimleri ve DNS bilgilerini göndermesini ve almasını sağlayan özel durumlar ve bu durum, FC kümesi ve işletim sistemi etkinleştirme sunucusu içindeki diğer VM 'lere giden "genel" internet 'e trafik gönderebilir. VM 'Lerin izin verilen giden hedefler listesi Azure yönlendirici alt ağları ve diğer Microsoft özelliklerini içermediğinden, kurallar onlar için bir savunma katmanı işlevi görür.
- **Rol yapılandırma dosyası kuralları**: Kiracıların hizmet modeline göre gelen ACL 'Leri tanımlar. Örneğin, bir kiracının belirli bir sanal makinede 80 numaralı bağlantı noktası üzerinde Web ön ucu varsa, bağlantı noktası 80 tüm IP adreslerine açılır. VM 'nin çalışan bir çalışan rolü varsa, çalışan rolü yalnızca aynı Kiracıdaki VM 'ye açılır.

**Yerel ana bilgisayar güvenlik duvarı**: Azure Service Fabric ve Azure depolama, hiper yönetici olmayan yerel bir işletim sistemi üzerinde çalışır ve bu nedenle, Windows Güvenlik Duvarı önceki iki kural kümesiyle yapılandırılır.

**Ana bilgisayar güvenlik duvarı**: Ana bilgisayar güvenlik duvarı, hiper yöneticiyi çalıştıran konak bölümünü korur. Kurallar yalnızca, belirli bir bağlantı noktasındaki konak bölümüyle iletişim kurmasına izin vermek üzere programlanır. Diğer özel durumlar, DHCP yanıt ve DNS yanıtları sağlamak için kullanılır. Azure, konak bölümü için bir güvenlik duvarı kuralları şablonu içeren bir makine yapılandırma dosyası kullanır. VM 'Lerin belirli protokol/bağlantı noktalarıyla ana bilgisayar bileşenleri, hat sunucusu ve meta veri sunucusuyla iletişim kurmasına olanak sağlayan bir ana bilgisayar güvenlik duvarı özel durumu da mevcuttur.

**Konuk güvenlik duvarı**: Konuk işletim sisteminin, müşteri VM 'Leri ve depolamadaki müşteriler tarafından yapılandırılabilen Windows Güvenlik Duvarı parçası.

Azure özellikleriyle oluşturulan ek güvenlik özellikleri şunlardır:

- DIP 'lerden gelen IP adresleri atanan altyapı bileşenleri. Internet 'teki bir saldırgan, Microsoft 'a ulaşamadığı için bu adreslere ait trafiği ele alamaz. Internet ağ geçidi yönlendiricileri yalnızca iç adreslere yönelik olan paketleri filtreleyerek üretim ağını girmemelidir. VIP 'lere yönlendirilmiş trafiği kabul eden tek bileşenler yük dengeleyiciler.
- Tüm iç düğümlerde uygulanan güvenlik duvarları, verilen herhangi bir senaryo için üç temel güvenlik mimarisi öğesine sahiptir:

   - Güvenlik duvarları yük dengeleyicinin arkasına yerleştirilir ve paketleri her yerden kabul eder. Bu paketlerin dışarıdan açığa çıkarılması amaçlanmıştır ve geleneksel bir çevre güvenlik duvarında açık bağlantı noktalarına karşılık gelir.
   - Güvenlik duvarları yalnızca sınırlı sayıda adres kümesinden paketleri kabul eder. Bu konu, DDoS saldırılarına karşı savunma kapsamındaki derinlemesine savunma stratejisinin bir parçasıdır. Bu tür bağlantılar, şifreli olarak doğrulanır.
   - Güvenlik duvarlarına yalnızca select iç düğümlerinden erişilebilir. Bunlar, yalnızca Azure ağı içinde bir dizi kaynak IP adresi olan listeleyen kaynak IP adresleri listesinden gelen paketleri kabul eder. Örneğin, şirket ağındaki bir saldırı istekleri bu adreslere yönlendirebilir, ancak paketin kaynak adresi Azure ağı içindeki numaralandırılmış listede değilse, saldırıları engellenir.
     - Çevre üzerindeki erişim yönlendiricisi, yapılandırılmış statik yolları nedeniyle Azure ağı içindeki bir adrese yönelik giden paketleri engelliyor.

## <a name="next-steps"></a>Sonraki adımlar
Microsoft 'un Azure altyapısını güvenli hale getirmeye yönelik daha fazla bilgi edinmek için bkz.:

- [Azure tesisler, şirket içi ve fiziksel güvenlik](physical-security.md)
- [Azure altyapı kullanılabilirliği](infrastructure-availability.md)
- [Azure Information System bileşenleri ve sınırları](infrastructure-components.md)
- [Azure ağ mimarisi](infrastructure-network.md)
- [Azure SQL veritabanı güvenlik özellikleri](infrastructure-sql.md)
- [Azure üretim işlemleri ve yönetimi](infrastructure-operations.md)
- [Azure altyapı izleme](infrastructure-monitoring.md)
- [Azure altyapı bütünlüğü](infrastructure-integrity.md)
- [Azure müşteri verileri koruması](protection-customer-data.md)
