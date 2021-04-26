---
title: Genel Bakış-MySQL için Azure veritabanı-esnek sunucu
description: MySQL Community sürümünü temel alan Microsoft bulutundaki bir ilişkisel veritabanı hizmeti olan MySQL için Azure veritabanı esnek sunucusu hakkında bilgi edinin.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/21/2020
ms.openlocfilehash: e3259cddc3790f92f56d7f59783cd66a2502b704
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813335"
---
# <a name="azure-database-for-mysql---flexible-server-preview"></a>MySQL için Azure veritabanı-esnek sunucu (Önizleme)

MySQL Community Edition tarafından desteklenen MySQL için Azure veritabanı, iki dağıtım modunda sunulmaktadır:
- Tek sunucu 
- Esnek Sunucu (Önizleme)

Bu makalede, esnek sunucu dağıtım modelinin temel kavramlarına genel bakış ve giriş sağlayacağız. Hangi dağıtım seçeneğinin iş yükünüz için uygun olduğuna karar verme hakkında daha fazla bilgi için bkz. [Azure 'da doğru MySQL sunucu seçeneği seçme](./../select-right-deployment-type.md).

## <a name="overview"></a>Genel Bakış

MySQL için Azure veritabanı esnek sunucu, veritabanı yönetim işlevleri ve yapılandırma ayarları üzerinde daha ayrıntılı denetim ve esneklik sağlamak için tasarlanmış, tam olarak yönetilen bir veritabanı hizmetidir. Genel olarak, hizmet kullanıcı gereksinimlerine göre daha fazla esneklik ve sunucu yapılandırma özelleştirmeleri sağlar. Esnek sunucu mimarisi, kullanıcıların tek kullanılabilirlik bölgesinde ve birden çok kullanılabilirlik alanında yüksek kullanılabilirliği kabul etmesine olanak tanır. Esnek sunucular Ayrıca, sürekli olarak tam işlem kapasitesi gerektirmeyen iş yükleri için ideal olan sunucunuzu ve ücretsiz SKU 'ları durdurma/başlatma yeteneğine sahip daha iyi maliyet iyileştirme denetimleri sağlar. Hizmet şu anda MySQL 5,7 ve 8,0 topluluk sürümünü desteklemektedir. Hizmet şu anda çok çeşitli [Azure bölgelerinde](https://azure.microsoft.com/global-infrastructure/services/)kullanıma sunuldu.

Esnek sunucular için en uygun 
- Daha iyi denetim ve özelleştirmeler gerektiren uygulama geliştirmeleri.
- Bölge yedekli yüksek kullanılabilirlik
- Yönetilen bakım pencereleri

![Esnek sunucu kavramsal diyagramı](media/overview/1-flexible-server-conceptual-diagram.png) 

## <a name="high-availability-within-and-across-availability-zones"></a>Kullanılabilirlik alanları dahilinde ve genelinde yüksek kullanılabilirlik

Esnek sunucu dağıtım modeli, tek kullanılabilirlik alanı genelinde ve birden çok kullanılabilirlik alanında yüksek kullanılabilirliği destekleyecek şekilde tasarlanmıştır. Mimari, işlem ve depolamayı ayırır. Veritabanı altyapısı bir sanal makinede çalışır, veri dosyaları Azure depolama üzerinde bulunur. Depolama, veritabanı dosyalarının yerel olarak yedekli, her zaman veri dayanıklılığı sağlayan üç zaman uyumlu kopyasını tutar. 

Tek bir kullanılabilirlik alanı içinde, sunucu planlı veya planlanmamış olaylar nedeniyle kapalıysa, hizmet aşağıdaki otomatik prosedürü kullanarak sunucuların yüksek oranda kullanılabilir olduğunu korur:

1. Yeni bir işlem VM 'si sağlandı.
2. Veri dosyaları içeren depolama alanı, yeni sanal makineyle eşlenir
3. MySQL veritabanı altyapısı yeni sanal makinede çevrimiçi hale getirilir.
4. Sunucu bağlantıları kabul etmeye hazırsa istemci uygulamaları yeniden bağlanabilir.
   
:::image type="content" source="media/overview/2-flexible-server-architecture.png" alt-text="Tek bölge yüksek kullanılabilirlik kavramsal diyagramı"::: 

Bölgesel olarak yedekli yüksek kullanılabilirlik yapılandırılmışsa, hizmet aynı Azure bölgesindeki kullanılabilirlik bölgesi genelinde etkin bir bekleme sunucusu sağlar ve bakımını yapar. Kaynak sunucudaki veri değişiklikleri, sıfır veri kaybı sağlamak için zaman uyumlu sunucuya eşzamanlı olarak çoğaltılır. Bölgesel olarak yedekli yüksek kullanılabilirlik sayesinde, planlı veya planlanmamış yük devretme olayı tetiklendiğinde, bekleme sunucusu hemen çevrimiçi olur ve gelen işlemleri işleyebilir. 60-120 saniyeden oluşan tipik yük devretme süresi aralığı. Bu, hizmetin yüksek kullanılabilirliği desteklemesini ve belirli bir Azure bölgesindeki tek kullanılabilirlik bölgesi hatalarıyla ilgili tolerans ile iyileştirilmiş dayanıklılık sağlamasına olanak tanır. 

Daha fazla ayrıntı için bkz. [yüksek kullanılabilirlik kavramları](concepts-high-availability.md) .

:::image type="content" source="media/overview/3-flexible-server-overview-zone-redundant-ha.png" alt-text="Bölge yedekli yüksek kullanılabilirliği kavramsal diyagramı"::: 

## <a name="automated-patching-with-managed-maintenance-window"></a>Yönetilen bakım penceresiyle otomatik düzeltme eki uygulama

Hizmet, temel alınan donanım, işletim sistemi ve veritabanı altyapısının otomatik düzeltme eki uygular. Düzeltme eki uygulama, güvenlik ve yazılım güncelleştirmelerini içerir. MySQL altyapısı için, ikincil sürüm yükseltmeleri planlı bakım sürümünün bir parçası olarak da dahil edilmiştir. Kullanıcılar, düzeltme eki uygulama zamanlamasını sistem tarafından yönetilmek üzere yapılandırabilir veya özel zamanlamalarını tanımlayabilir. Bakım zamanlaması sırasında, düzeltme eki uygulanır ve sunucu, güncelleştirmeyi tamamlamaya yönelik düzeltme eki uygulama işleminin bir parçası olarak yeniden başlatma gerektirebilir. Özel zamanlama sayesinde, kullanıcılar düzeltme eki uygulama döngüsünü öngörülebilir hale getirebilir ve iş üzerinde en az etkiyle bir bakım penceresi seçebilirler. Genel olarak, hizmet sürekli tümleştirme ve yayının bir parçası olarak aylık yayın zamanlamasını izler.

Daha fazla ayrıntı için bkz. [zamanlanmış bakım](concepts-maintenance.md) . 

## <a name="automatic-backups"></a>Otomatik yedeklemeler

Esnek sunucu hizmeti otomatik olarak sunucu yedeklemeleri oluşturur ve bunları Kullanıcı tarafından yerel olarak yedekli veya coğrafi olarak yedekli depolama olarak yapılandırılmış şekilde depolar. Yedeklemeler, Sunucunuzu yedekleme saklama süresi içinde herhangi bir zaman noktasına geri yüklemek için kullanılabilir. Varsayılan yedekleme saklama süresi yedi gündür. Saklama, isteğe bağlı olarak 35 güne kadar yapılandırılabilir. Tüm yedeklemeler AES 256 bit şifreleme kullanılarak şifrelenir. 

Daha fazla bilgi için bkz. [yedekleme kavramları](concepts-backup-restore.md) .

## <a name="network-isolation"></a>Ağ Yalıtımı

MySQL için Azure veritabanı esnek sunucusuna bağlanmak için iki ağ seçeneğiniz vardır. Seçenekler **özel erişim (VNET tümleştirmesi)** ve **genel erişim (izin verilen IP adresleri)**. 

* **Özel erişim (VNET tümleştirmesi)** – esnek sunucunuzu [Azure sanal ağınıza](../../virtual-network/virtual-networks-overview.md)dağıtabilirsiniz. Azure sanal ağları özel ve güvenli ağ iletişimi sağlar. Bir sanal ağdaki kaynaklar özel IP adresleri üzerinden iletişim kurabilir.

   Aşağıdaki özellikleri istiyorsanız VNet tümleştirme seçeneğini belirleyin:
   * Aynı sanal ağdaki Azure kaynaklarından özel IP adresleri kullanarak esnek sunucunuza bağlanma
   * Azure olmayan kaynaklardan esnek sunucunuza bağlanmak için VPN veya ExpressRoute kullanın
   * Ortak uç nokta yok

* **Genel erişim (izin VERILEN IP adresleri)** – esnek sunucunuzu ortak bir uç nokta ile dağıtabilirsiniz. Genel uç nokta, genel olarak çözümlenebilen bir DNS adresidir. "İzin verilen IP adresleri" ifadesi sunucunuza erişim izni vermek için seçtiğiniz bir IP aralığı anlamına gelir. Bu izinler, **güvenlik duvarı kuralları** olarak adlandırılır.

Daha fazla bilgi için bkz. [ağ kavramları](concepts-networking.md) .

## <a name="adjust-performance-and-scale-within-seconds"></a>Saniyeler içinde performansı ve ölçeği ayarlama

Esnek sunucu hizmeti üç SKU katmanında mevcuttur: Burstable, Genel Amaçlı ve bellek için Iyileştirilmiş. Burstable katmanı, her zaman sürekli olarak tam işlem kapasitesi gerektirmeyen düşük maliyetli geliştirme ve düşük eşzamanlılık iş yükleri için idealdir. Genel Amaçlı ve bellek için Iyileştirilmiş, yüksek eşzamanlılık, ölçek ve öngörülebilir performans gerektiren üretim iş yükleri için daha uygundur. İlk uygulamanızı ayda birkaç ABD Doları için küçük bir veritabanında oluşturabilir ve ardından ölçeklendirmeyi gereksinimlerinize uygun şekilde ayarlayabilirsiniz. Depolama Ölçeklendirmesi çevrimiçi ve depolama otomatik büyümesini destekler. Dinamik ölçeklendirebilirlik, veritabanınızın hızla değişen kaynak gereksinimlerine saydam bir şekilde yanıt verebilmesini sağlar. Yalnızca kullandığınız kaynaklar için ödeme yaparsınız. 

Daha fazla bilgi için bkz. [işlem ve depolama kavramları](concepts-compute-storage.md) .

## <a name="scale-out-your-read-workload-with-up-to-10-read-replicas"></a>10 ' a kadar okuma çoğaltmalarıyla okuma iş yükünüzü ölçeklendirin

MySQL, İnternet ölçeğinde Web ve mobil uygulamaları çalıştırmaya yönelik popüler veritabanı altyapılarından biridir. Müşterilerimizin birçoğu, çevrimiçi eğitim hizmetleri, video akış Hizmetleri, dijital ödeme çözümleri, e-ticaret platformları, Oyun Hizmetleri, haber portalları, kamu ve sağlık Web siteleri için bunu kullanır. Web veya mobil uygulamadaki trafik arttıkça bu hizmetlerin hizmet vermesi ve ölçeklendirilmesi gerekir.

Uygulamalar tarafında, uygulama genellikle Java veya php 'de geliştirilmiştir ve [Azure sanal makine ölçek kümeleri](../../virtual-machine-scale-sets/overview.md)   veya [Azure Uygulama hizmetlerinde](../../app-service/overview.md)çalışacak şekilde geçirilir   ya da [Azure KUBERNETES hizmeti (aks)](../../aks/intro-kubernetes.md)üzerinde çalışmak üzere Kapsayıcılı hale getirilir. Arka plandaki altyapı olarak sanal makine ölçek kümesi, App Service veya AKS ile, uygulama ölçekleme, anında yeni VM 'Ler sunarak basitleştirilir ve isteklerin durum bilgisiz bileşenleri, isteklere geçmek için, ancak genellikle veritabanı, Merkezi durum bilgisi olan bir bileşen olarak performans sorunlarına neden olur.

Çoğaltma oku özelliği, MySQL için Azure veritabanı esnek sunucusu 'na ait verileri salt okunurdur bir sunucuya çoğaltmanıza olanak sağlar. Kaynak sunucudan 10 ' a **kadar** çoğaltma yapabilirsiniz. Çoğaltmalar, MySQL altyapısının yerel [ikili günlük (binlog) dosya konumu tabanlı çoğaltma teknolojisi](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)kullanılarak zaman uyumsuz olarak güncelleştirilir. Uygulama yeniden düzenleme maliyeti olmadan çoğaltmaları okumak için uygulama iş yükünüzü sorunsuz bir şekilde genişletmek için [Proxysql](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) gibi yük dengeleyici ara sunucu çözümünü kullanabilirsiniz. 

Daha fazla bilgi edinmek için bkz. [çoğaltma kavramlarını okuyun](concepts-read-replicas.md) . 


## <a name="stopstart-server-to-optimize-cost"></a>Maliyeti iyileştirmek için sunucuyu Durdur/Başlat

Esnek sunucu hizmeti, maliyeti iyileştirmek için isteğe bağlı sunucuyu durdurup başlatabilmenizi sağlar. İşlem Katmanı faturalandırması, sunucu durdurulduğunda hemen durdurulur. Bu, geliştirme, test etme ve zamana göre öngörülebilir üretim iş yükleri için önemli maliyet tasarrufları elde etmenizi sağlar. Daha önce yeniden başlatılmadığı sürece sunucu yedi gün boyunca durdurulmuş durumda kalır. 

Daha fazla bilgi için bkz. [sunucu kavramları](concept-servers.md) . 

## <a name="enterprise-grade-security-and-privacy"></a>Kurumsal düzeyde güvenlik ve Gizlilik

Esnek sunucu hizmeti, bekleyen verilerin depolama şifrelemesi için FIPS 140-2 tarafından doğrulanan şifreleme modülünü kullanır. Yedeklemeler ve sorguları çalıştırırken oluşturulan geçici dosyalar da dahil olmak üzere veriler şifrelenir. Hizmet, Azure depolama şifrelemesi 'ne dahil olan AES 256 bit şifrelemeyi kullanır ve anahtarlar sistem tarafından yönetilebilir (varsayılan). 

Hizmet, Aktarım Katmanı Güvenliği varsayılan olarak zorunlu kılınan verileri hareket halinde şifreler. Esnek sunucular yalnızca Aktarım Katmanı Güvenliği (TLS 1,2) kullanılarak şifrelenmiş bağlantıları destekler ve TLS 1,0 ve TLS 1,1 ile gelen tüm bağlantılar reddedilir. 

Daha fazla bilgi için bkz. [Esnek sunuculara şifreli bağlantıları kullanma](https://docs.mongodb.com/manual/tutorial/configure-ssl) .

Esnek sunucular, [Azure sanal ağ](../../virtual-network/virtual-networks-overview.md) (VNet) Tümleştirmesi kullanılarak sunuculara tam özel erişim sağlar. Azure sanal ağındaki sunuculara yalnızca özel IP adresleri üzerinden ulaşılırsa ve bu adreslere bağlanabilir. VNet tümleştirmeyle, genel erişim reddedilir ve sunuculara genel uç noktalar kullanılarak ulaşılamıyor. 

Daha fazla bilgi için bkz. [ağ kavramları](concepts-networking.md) .


## <a name="monitoring-and-alerting"></a>İzleme ve uyarı

Esnek sunucu hizmeti yerleşik performans izleme ve uyarı özellikleriyle donatılmıştır. Tüm Azure ölçümlerinin bir dakikalık sıklığı vardır ve her ölçüm 30 gün geçmiş sağlar. Ölçümler üzerinde uyarılar yapılandırabilirsiniz. Hizmet, kaynak kullanımını izlemek için ana bilgisayar sunucusu ölçümleri sunar ve yavaş sorgu günlüklerinin yapılandırılmasına izin verir. Bu araçları kullanarak, iş yüklerinizi hızlıca iyileştirebilmenizi ve sunucunuzu en iyi performansa göre yapılandırmanızı sağlayabilirsiniz. 

Daha fazla bilgi için bkz. [izleme kavramları](concepts-monitoring.md) .

## <a name="migration"></a>Geçiş

Hizmet MySQL 'in topluluk sürümünü çalıştırır. Bu, tam uygulama uyumluluğuna izin verir ve MySQL altyapısında geliştirilen mevcut uygulamayı tek sunucu hizmetine geçirmek için en az yeniden düzenleme maliyeti gerektirir. Tek sunucuya geçiş, aşağıdaki seçeneklerden biri kullanılarak gerçekleştirilebilir:

- **Dökümünü al ve geri yükle** – kullanıcıların bazı kapalı kalma süresini karşılayabileceği, döküm ve geri yükleme işlemleri, mysqldump/mydumper gibi topluluk araçlarını kullanarak geçirmek için en hızlı yolu sağlayabilir. Ayrıntılar için bkz. döküm kullanarak geçiş ve geri yükleme. 
- **Azure veritabanı geçiş hizmeti** : en az kapalı kalma süresiyle tek sunucuya sorunsuz ve Basitleştirilmiş geçişler Için [Azure veritabanı geçiş hizmeti](../../dms/tutorial-mysql-azure-mysql-online.md) yararlanılabilir olabilir. 

## <a name="azure-regions"></a>Azure bölgeleri

Azure 'da iş yükünüzü çalıştırmanın avantajlarından biri küresel erişime sahip. MySQL için Azure veritabanı esnek sunucusu şu Azure bölgelerinde bugün kullanılabilir:

| Region | Kullanılabilirlik | Bölge yedekli HA | 
| --- | --- | --- |
| West Europe | :heavy_check_mark: | :heavy_check_mark: |
| Kuzey Avrupa | :heavy_check_mark: | :heavy_check_mark: |
| Güney Birleşik Krallık | :heavy_check_mark: | :heavy_check_mark: | 
| Doğu ABD 2 | :heavy_check_mark: | :heavy_check_mark: |
| Batı ABD 2 | :heavy_check_mark: | :heavy_check_mark: |
| Central US | :heavy_check_mark: | sayı | 
| Doğu ABD | :heavy_check_mark: | :heavy_check_mark: |
| Orta Kanada | :heavy_check_mark: | sayı | 
| Güneydoğu Asya | :heavy_check_mark: | :heavy_check_mark: |
| Güney Kore - Orta | :heavy_check_mark: | sayı | 
| Doğu Japonya | :heavy_check_mark: | :heavy_check_mark: | 
| Doğu Avustralya | :heavy_check_mark: | :heavy_check_mark: |

Yakında yeni bölgeler eklemek için çalışıyoruz.

## <a name="contacts"></a>Kişiler
MySQL için Azure veritabanı esnek sunucusu 'nda olabilecek herhangi bir soru veya öneri için, MySQL için Azure veritabanı ekibine ([ @Ask MySQL IÇIN Azure DB](mailto:AskAzureDBforMySQL@service.microsoft.com)) bir e-posta gönderin. Bu e-posta adresi bir teknik destek diğer adı değil.

Buna ek olarak, aşağıdaki iletişim noktalarını uygun şekilde göz önünde bulundurun:

- Azure Desteği ile iletişim kurmak için [Azure portaldan bir bilet oluşturun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Hesabınızla ilgili bir sorun gidermek için Azure portalda bir [destek isteği](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) oluşturun.
- Görüş bildirmek veya yeni özellikler istemek için [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) aracılığıyla bir giriş oluşturun.

## <a name="next-steps"></a>Sonraki adımlar
Artık MySQL için Azure veritabanı tek sunucu dağıtım moduna giriş okudığınıza göre şunları yapabilirsiniz:

- İlk sunucunuzu oluşturun. 
  - [Azure portal kullanarak MySQL için Azure veritabanı esnek sunucusu oluşturma](quickstart-create-server-portal.md)
  - [Azure CLı kullanarak MySQL için Azure veritabanı esnek sunucusu oluşturma](quickstart-create-server-cli.md)
  - [Azure CLı kullanarak MySQL için Azure veritabanı esnek sunucusunu yönetme](how-to-manage-server-portal.md)

- Tercih ettiğiniz dili kullanarak ilk uygulamanızı oluşturun:
  - [Python](connect-python.md)
  - [Php](connect-php.md)
