---
title: Azure SQL veritabanı güvenlik özellikleri
description: Bu makalede, Azure SQL veritabanı 'nın Azure 'da müşteri verilerini nasıl koruduğu hakkında genel bir açıklama sunulmaktadır.
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
ms.openlocfilehash: 74b0fa4643907493904e77ce333d1ec1dba01f49
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727097"
---
# <a name="azure-sql-database-security-features"></a>Azure SQL veritabanı güvenlik özellikleri    
Azure SQL veritabanı, Azure 'da bir ilişkisel veritabanı hizmeti sağlar. Müşteri verilerini korumak ve müşterilerin ilişkisel bir veritabanı hizmetinden beklediği güçlü güvenlik özellikleri sağlamak için, SQL veritabanı 'nın kendi güvenlik özellikleri kümesi vardır. Bu yetenekler, Azure 'dan devralınan denetimleri oluşturur.

## <a name="security-capabilities"></a>Güvenlik özellikleri

### <a name="usage-of-the-tds-protocol"></a>TDS protokolünün kullanımı
Azure SQL veritabanı yalnızca varsayılan TCP/1433 bağlantı noktası üzerinden erişilebilir olmasını gerektiren tablo veri akışı (TDS) protokolünü destekler.

### <a name="azure-sql-database-firewall"></a>Azure SQL veritabanı güvenlik duvarı
Azure SQL veritabanı, müşteri verilerini korumaya yardımcı olmak için, aşağıda gösterildiği gibi, varsayılan olarak SQL veritabanı sunucusuna tüm erişimi engeller.

![Azure SQL veritabanı güvenlik duvarı](./media/infrastructure-sql/sql-database-firewall.png)

Ağ geçidi güvenlik duvarı, müşterilerin ayrıntılı IP adresi aralıklarını belirtmesini sağlayan adresleri sınırlayabilir. Güvenlik Duvarı, her isteğin kaynak IP adresine göre erişime izin verir.

Müşteriler bir yönetim portalını kullanarak veya Azure SQL veritabanı yönetim REST API kullanarak program aracılığıyla güvenlik duvarı yapılandırması elde edebilir. Azure SQL veritabanı ağ geçidi güvenlik duvarı varsayılan olarak Azure SQL veritabanı örneklerine tüm müşteri TDS erişimini engeller. Müşteriler, Azure SQL veritabanı bağlantılarına kaynak ve hedef Internet adresleri, protokoller ve bağlantı noktası numaralarıyla izin vermek için erişim denetimi listelerini (ACL 'Ler) kullanarak erişimi yapılandırmalıdır.

### <a name="dosguard"></a>DoSGuard
Hizmet reddi (DoS) saldırıları, DoSGuard adlı bir SQL veritabanı ağ geçidi hizmeti tarafından azaltılır. DoSGuard IP adreslerinden gelen başarısız oturum açma işlemlerini etkin bir şekilde izler. Belirli bir IP adresinden belirli bir süre içinde birden çok başarısız oturum açma işlemi varsa, IP adresinin önceden tanımlanmış bir dönem için hizmetteki kaynaklara erişimi engellenir.

Ayrıca, Azure SQL veritabanı ağ geçidi şunları yapar:

- TDS FIPS 140-2 tarafından veritabanı sunucularına bağlanırken doğrulanan şifreli bağlantıları uygulamaya yönelik güvenli kanal yetenek anlaşmaları.
- İstemcilerden gelen bağlantıları kabul ederken durum bilgisi olan TDS paket incelemesi. Ağ Geçidi bağlantı bilgilerini doğrular ve TDS paketlerinde bağlantı dizesinde belirtilen veritabanı adına göre uygun fiziksel sunucuya geçirilir.

Azure SQL veritabanı sunumunun ağ güvenliği için aşırı kullanılabilir ilke, yalnızca hizmetin çalışmasına izin vermek için gerekli olan bağlantı ve iletişime izin verdir. Diğer tüm bağlantı noktaları, protokoller ve bağlantılar varsayılan olarak engellenir. Sanal yerel alan ağları (VLAN 'Lar) ve ACL 'Ler, ağ iletişimini kaynak ve hedef ağlar, protokoller ve bağlantı noktası numaralarıyla kısıtlamak için kullanılır.

Ağ tabanlı ACL 'Ler uygulamak için onaylanan mekanizmalar, yönlendiricilerde ve yük dengeleyiciler üzerinde ACL 'Ler içerir. Bu mekanizmalar Azure ağı, Konuk VM Güvenlik Duvarı ve müşteri tarafından yapılandırılan Azure SQL veritabanı ağ geçidi güvenlik kuralları tarafından yönetilir.

## <a name="data-segregation-and-customer-isolation"></a>Veri ayrımı ve müşteri yalıtımı
Azure üretim ağı, genel olarak erişilebilen sistem bileşenlerinin iç kaynaklardan ayrılmasını sağlayan bir şekilde yapılandırılmıştır. Genel kullanıma yönelik Azure portal ve temel alınan Azure sanal altyapısına erişim sağlayan, müşteri uygulaması örneklerinin ve müşteri verilerinin bulunduğu Web sunucuları arasında fiziksel ve mantıksal sınırlar vardır.

Herkese açık olarak erişilebilen tüm bilgiler, Azure üretim ağı 'nda yönetilir. Üretim ağı iki öğeli kimlik doğrulama ve sınır koruma mekanizmalarına tabidir, önceki bölümde açıklanan güvenlik duvarı ve güvenlik özelliği kümesini kullanır ve sonraki bölümlerde belirtildiği gibi veri yalıtımı işlevlerini kullanır.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>FC yetkisiz sistemleri ve yalıtımına
Yapı denetleyicisi (FC), Azure dokusunun merkezi Orchestrator 'ı olduğundan, özellikle de müşteri uygulamalarındaki potansiyel olarak güvenliği aşılmış bir mssunucudan tehditleri hafifletmek için önemli denetimler vardır. FC, cihaz bilgileri (örneğin, MAC adresi) FC içinde önceden yüklenmemiş herhangi bir donanımı tanımıyor. FC üzerindeki DHCP sunucuları, önyükleme yapmak istedikleri düğümlerin MAC adresi listesini yapılandırdı. Yetkisiz sistemler bağlı olsa da, yapı envanterine dahil değildir ve bu nedenle bağlı değildir veya doku envanterindeki herhangi bir sistemle iletişim kurmaya yetkili değildir. Bu, yetkisiz sistemlerin FC ile iletişim kurma riskini azaltır ve VLAN ve Azure 'a erişim elde edebilirler.

### <a name="vlan-isolation"></a>VLAN yalıtımı
Azure üretim ağı üç birincil VLAN 'a mantıksal olarak ayrılabilir:

- Ana VLAN: Güvenilmeyen Müşteri düğümlerini birbirine bağlar.
- FC VLAN: Güvenilen FCs ve destekleyici sistemleri içerir.
- Cihaz VLAN: Güvenilen ağ ve diğer altyapı cihazlarını içerir.

### <a name="packet-filtering"></a>Paket filtreleme
Düğümlerin kök işletim sistemi ve konuk işletim sistemi üzerinde uygulanan IPFilter ve yazılım güvenlik duvarları bağlantı kısıtlamalarını zorlar ve VM 'Ler arasında yetkisiz trafiği engeller.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hiper yönetici, kök işletim sistemi ve konuk VM 'Ler
Konuk VM 'lerden ve konuk VM 'lerden bir diğerinin yalıtımı, hiper yönetici ve kök işletim sistemi tarafından yönetilir.

### <a name="types-of-rules-on-firewalls"></a>Güvenlik duvarlarındaki kural türleri
Bir kural şu şekilde tanımlanır:

{Güvenlik Yanıt Merkezi (src) IP, src bağlantı noktası, hedef IP, hedef bağlantı noktası, hedef protokolü, gelen/giden, durum bilgisi olmayan/durumsuz, durum bilgisi olan Flow zaman aşımı.

Yalnızca kuralların herhangi biri izin veriyorsa, zaman uyumlu boşta karakter (SYN) paketlerine yalnızca içinde ve dışarı izin verilir. TCP için Azure, ilkenin yalnızca tüm SYN olmayan paketleri VM 'ye veya dışına izin verdiği durum bilgisiz kurallar kullanır. Güvenlik Merkezi, daha önce bir SYN paketi görmeyen bir ana bilgisayar yığınının bir SYN olmayan yok saymakla dayanıklı olması olabilir. TCP protokolünün durumu durum bilgisiz ve durum bilgisi olmayan SYN tabanlı kuralıyla birlikte durum bilgisi olan bir uygulamanın genel davranışına ulaşır.

Azure, Kullanıcı Datagram Protokolü (UDP) için durum bilgisi olan bir kural kullanır. Her bir UDP paketi bir kuralla eşleştiğinde, bir ters akış diğer yönde oluşturulur. Bu akışta yerleşik bir zaman aşımı vardır.

Müşteriler, Azure 'un sağladığı en üst düzey güvenlik duvarlarını ayarlamaktan sorumludur. Burada müşteriler gelen ve giden trafik için kuralları tanımlayabilir.

### <a name="production-configuration-management"></a>Üretim yapılandırması yönetimi
Standart güvenli yapılandırma işlemleri Azure ve Azure SQL veritabanı 'nda ilgili işlemler ekipleri tarafından korunur. Üretim sistemlerine yapılan tüm yapılandırma değişiklikleri merkezi bir izleme sistemi aracılığıyla belgelenmiştir ve izlenir. Yazılım ve donanım değişiklikleri merkezi izleme sistemi aracılığıyla izlenir. ACL ile ilgili ağ değişiklikleri bir ACL yönetim hizmeti kullanılarak izlenir.

Azure 'daki tüm yapılandırma değişiklikleri, hazırlama ortamında geliştirilir ve test edilir ve daha sonra üretim ortamında dağıtılır. Yazılım derlemeleri testin bir parçası olarak gözden geçirilir. Güvenlik ve gizlilik denetimleri, giriş denetim listesi ölçütlerinin bir parçası olarak gözden geçirilir. Değişiklikler, ilgili dağıtım ekibi tarafından zamanlanan aralıklarda dağıtılır. Yayınlar, üretime dağıtılmadan önce ilgili dağıtım ekibi personeli tarafından incelenip imzalanırlar.

Başarı için değişiklikler izlenir. Bir hata senaryosunda, değişiklik önceki durumuna geri alınır veya belirtilen personele onay vererek hatayı gidermek için bir düzeltme dağıtılır. Kaynak deposu, git, TFS, Ana Veri Hizmetleri (MDS), çalıştırma, Azure Güvenlik izleme, FC ve WinFabric platformu, Azure sanal ortamındaki yapılandırma ayarlarını merkezi olarak yönetmek, uygulamak ve doğrulamak için kullanılır.

Benzer şekilde, donanım ve ağ değişiklikleri derleme gereksinimlerine uygunluğunu değerlendirmek için doğrulama adımları oluşturmuştur. Yayınlar, yığın genelinde ilgili grupların bir eşgüdümlü değişiklik Danışma panosu (CAB) üzerinden incelenir ve yetkilendirilir.

## <a name="next-steps"></a>Sonraki adımlar
Microsoft 'un Azure altyapısını güvenli hale getirmeye yönelik daha fazla bilgi edinmek için bkz.:

- [Azure tesisler, şirket içi ve fiziksel güvenlik](physical-security.md)
- [Azure altyapı kullanılabilirliği](infrastructure-availability.md)
- [Azure Information System bileşenleri ve sınırları](infrastructure-components.md)
- [Azure ağ mimarisi](infrastructure-network.md)
- [Azure üretim ağı](production-network.md)
- [Azure üretim işlemleri ve yönetimi](infrastructure-operations.md)
- [Azure altyapı izleme](infrastructure-monitoring.md)
- [Azure altyapı bütünlüğü](infrastructure-integrity.md)
- [Azure müşteri verileri koruması](protection-customer-data.md)


