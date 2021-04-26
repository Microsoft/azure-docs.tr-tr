---
title: PostgreSQL için Azure veritabanı-esnek sunucu
description: PostgreSQL için Azure veritabanı 'na genel bakış sağlar-esnek sunucu.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 04/05/2021
ms.openlocfilehash: 090d38a92da126cd4fd8423231f856d22ea9a64c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106845"
---
# <a name="azure-database-for-postgresql---flexible-server"></a>PostgreSQL için Azure veritabanı-esnek sunucu

PostgreSQL Community Edition tarafından desteklenen [PostgreSQL Için Azure veritabanı](../overview.md) , üç dağıtım modunda sunulmaktadır:

- [Tek sunucu](../overview-single-server.md)
- Esnek Sunucu (Önizleme)
- Hiper Ölçek (Citus)

Bu makalede, esnek sunucu dağıtım modelinin temel kavramlarına genel bakış ve giriş sağlayacağız.

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

## <a name="overview"></a>Genel Bakış

PostgreSQL için Azure veritabanı-esnek sunucu, veritabanı yönetim işlevleri ve yapılandırma ayarları üzerinde daha ayrıntılı denetim ve esneklik sağlamak için tasarlanmış, tam olarak yönetilen bir veritabanı hizmetidir. Genel olarak, hizmet kullanıcı gereksinimlerine göre daha fazla esneklik ve sunucu yapılandırma özelleştirmeleri sağlar. Esnek sunucu mimarisi, kullanıcıların, daha düşük gecikme süresi için istemci katmanı ile veritabanı altyapısını birlikte bulmasını sağlar, tek bir kullanılabilirlik alanı içinde ve birden çok kullanılabilirlik alanında yüksek kullanılabilirlik ' i seçin. Esnek sunucular Ayrıca, her zaman sürekli olarak tam işlem kapasitesi gerektirmeyen iş yükleri için ideal olan sunucunuzu durdurup başlatabilir ve daha iyi bir işlem katmanı sağlar. Hizmet şu anda PostgreSQL 11 ve 12 ' nin topluluk sürümünü desteklemektedir. Hizmet şu anda çok çeşitli  [Azure bölgelerinde](https://azure.microsoft.com/global-infrastructure/services/)kullanıma sunuldu.

![Esnek sunucu-genel bakış](./media/overview/overview-flexible-server.png)


Esnek sunucular için en uygun

- Daha iyi denetim ve özelleştirmeler gerektiren uygulama geliştirmeleri.
- Bölge yedekli yüksek kullanılabilirlik
- Yönetilen bakım pencereleri
  
## <a name="high-availability"></a>Yüksek kullanılabilirlik

Esnek sunucu dağıtım modeli, tek kullanılabilirlik alanı genelinde ve birden çok kullanılabilirlik alanında yüksek kullanılabilirliği destekleyecek şekilde tasarlanmıştır. Mimari, işlem ve depolamayı ayırır. Veritabanı altyapısı bir Linux sanal makinesinde çalışarak veri dosyaları Azure depolama üzerinde bulunur. Depolama, veri dayanıklılığı sağlayan veritabanı dosyalarının yerel olarak yedekli zaman uyumlu kopyasını tutar.

Planlı veya planlanmamış yük devretme olayları sırasında, sunucu kapalıysa, hizmet aşağıdaki otomatik prosedürü kullanarak sunucuların yüksek oranda kullanılabilir olduğunu korur:

1. Yeni bir işlem Linux VM 'si sağlandı.
2. Veri dosyaları içeren depolama alanı, yeni sanal makineyle eşlenir
3. PostgreSQL veritabanı altyapısı, yeni sanal makinede çevrimiçi hale getirilir.

Aşağıdaki resimde VM ve depolama hatası geçişi gösterilmektedir.

 :::image type="content" source="./media/overview/overview-azure-postgres-flex-virtualmachine.png" alt-text="Esnek sunucu-VM ve depolama sorunları":::

Bölgesel olarak yedekli yüksek kullanılabilirlik yapılandırılmışsa, hizmet aynı Azure bölgesindeki kullanılabilirlik bölgesi genelinde etkin bir bekleme sunucusu sağlar ve bakımını yapar. Kaynak sunucudaki veri değişiklikleri, sıfır veri kaybı sağlamak için zaman uyumlu sunucuya eşzamanlı olarak çoğaltılır. Bölgesel olarak yedekli yüksek kullanılabilirlik sayesinde, planlı veya planlanmamış yük devretme olayı tetiklendiğinde, bekleme sunucusu hemen çevrimiçi olur ve gelen işlemleri işleyebilir. Bu, aşağıdaki resimde gösterildiği gibi birden çok kullanılabilirlik bölgesini destekleyen bir Azure bölgesindeki kullanılabilirlik bölgesi arızasından hizmet dayanıklılığı sağlar.

 :::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="Bölge yedekli yüksek kullanılabilirlik":::

 Daha fazla ayrıntı için bkz. [yüksek kullanılabilirlik belgesi](./concepts-high-availability.md) .

## <a name="automated-patching-with-managed-maintenance-window"></a>Yönetilen bakım penceresiyle otomatik düzeltme eki uygulama

Hizmet, temel alınan donanım, işletim sistemi ve veritabanı altyapısının otomatik düzeltme eki uygular. Düzeltme eki uygulama, güvenlik ve yazılım güncelleştirmelerini içerir. PostgreSQL altyapısı için, ikincil sürüm yükseltmeleri planlı bakım sürümünün bir parçası olarak da dahil edilmiştir. Kullanıcılar, düzeltme eki uygulama zamanlamasını sistem tarafından yönetilmek üzere yapılandırabilir veya özel zamanlamalarını tanımlayabilir. Bakım zamanlaması sırasında, düzeltme eki uygulanır ve güncelleştirmeyi tamamlamaya yönelik düzeltme eki uygulama işleminin bir parçası olarak sunucunun yeniden başlatılması gerekebilir. Özel zamanlama sayesinde, kullanıcılar düzeltme eki uygulama döngüsünü öngörülebilir hale getirebilir ve iş üzerinde en az etkiyle bir bakım penceresi seçebilirler. Genel olarak, hizmet sürekli tümleştirme ve yayının bir parçası olarak aylık yayın zamanlamasını izler.

## <a name="automatic-backups"></a>Otomatik yedeklemeler

Esnek sunucu hizmeti otomatik olarak sunucu yedeklemeleri oluşturur ve bunları Kullanıcı tarafından yedekli (ZRS) yerel olarak yapılandırılmış şekilde depolar. Yedeklemeler, Sunucunuzu yedekleme saklama süresi içinde herhangi bir zaman noktasına geri yüklemek için kullanılabilir. Varsayılan yedekleme saklama süresi yedi gündür. Saklama, isteğe bağlı olarak 35 güne kadar yapılandırılabilir. Tüm yedeklemeler AES 256 bit şifreleme kullanılarak şifrelenir. Daha fazla ayrıntı için bkz. [yedeklemeler](./concepts-backup-restore.md) .

## <a name="adjust-performance-and-scale-within-seconds"></a>Saniyeler içinde performansı ve ölçeği ayarlama

Esnek sunucu hizmeti üç işlem katmanında kullanılabilir: Burstable, Genel Amaçlı ve bellek için Iyileştirilmiş. Burstable katmanı, her zaman sürekli olarak tam işlem kapasitesi gerektirmeyen düşük maliyetli geliştirme ve düşük eşzamanlılık iş yükleri için idealdir. Genel Amaçlı ve bellek için Iyileştirilmiş, yüksek eşzamanlılık, ölçek ve öngörülebilir performans gerektiren üretim iş yükleri için daha uygundur. İlk uygulamanızı ayda birkaç ABD Doları için küçük bir veritabanında oluşturabilir ve ardından ölçeklendirmeyi gereksinimlerinize uygun şekilde ayarlayabilirsiniz.

## <a name="stopstart-server-to-lower-tco"></a>TCO 'yu düşürmek için sunucuyu Durdur/Başlat

Esnek sunucu hizmeti, TCO 'nizi düşürmek için isteğe bağlı sunucuyu durdurup başlatabilmenizi sağlar. İşlem Katmanı faturalandırması, sunucu durdurulduğunda hemen durdurulur. Bu, geliştirme, test etme ve zamana göre öngörülebilir üretim iş yükleri için önemli maliyet tasarrufları elde etmenizi sağlar. Sunucu daha önce yeniden başlatılana kadar yedi gün boyunca durdurulmuş durumda kalır.

## <a name="enterprise-grade-security"></a>Kurumsal sınıf güvenlik

Esnek sunucu hizmeti, bekleyen verilerin depolama şifrelemesi için FIPS 140-2 tarafından doğrulanan şifreleme modülünü kullanır. Yedeklemeler ve sorguları çalıştırırken oluşturulan geçici dosyalar da dahil olmak üzere veriler şifrelenir. Hizmet, Azure depolama şifrelemesi 'ne dahil olan AES 256 bit şifrelemeyi kullanır ve anahtarlar sistem tarafından yönetilebilir (varsayılan). Hizmet, varsayılan olarak uygulanan Aktarım Katmanı Güvenliği (SSL/TLS) ile verileri hareket halinde şifreler. Hizmet, yalnızca TLS sürümleri 1,2 ' i zorlar ve destekler.

Esnek sunucular, Azure sanal ağı (VNet tümleştirmesi) kullanan sunuculara tam özel erişim sağlar. Azure sanal ağındaki sunuculara yalnızca özel IP adresleri üzerinden ulaşılırsa ve bu adreslere bağlanabilir. VNet tümleştirmeyle, genel erişim reddedilir ve sunuculara genel uç noktalar kullanılarak ulaşılamıyor.

## <a name="monitoring-and-alerting"></a>İzleme ve uyarı

Esnek sunucu hizmeti yerleşik performans izleme ve uyarı özellikleriyle donatılmıştır. Tüm Azure ölçümlerinin bir dakikalık sıklığı vardır ve her ölçüm 30 gün geçmiş sağlar. Ölçümler üzerinde uyarılar yapılandırabilirsiniz. Hizmet, kaynak kullanımını izlemek için ana bilgisayar sunucusu ölçümleri sunar ve yavaş sorgu günlüklerinin yapılandırılmasına izin verir. Bu araçları kullanarak, iş yüklerinizi hızlıca iyileştirebilmenizi ve sunucunuzu en iyi performansa göre yapılandırmanızı sağlayabilirsiniz.

## <a name="azure-regions"></a>Azure bölgeleri

Azure 'da iş yükünüzü çalıştırmanın avantajlarından biri küresel erişime sahip. Esnek sunucu şu Azure bölgelerinde sunulmaktadır:

| Bölge | Kullanılabilirlik | Bölge yedekli HA | 
| --- | --- | --- |
| West Europe | :heavy_check_mark: | :heavy_check_mark: |
| Kuzey Avrupa | :heavy_check_mark: | :heavy_check_mark: |
| Güney Birleşik Krallık | :heavy_check_mark: | :heavy_check_mark: | 
| Doğu ABD 2 | :heavy_check_mark: | :heavy_check_mark: |
| Batı ABD 2 | :heavy_check_mark: | :heavy_check_mark: |
| Central US | :heavy_check_mark: | :heavy_check_mark: | 
| Doğu ABD | :heavy_check_mark: | :heavy_check_mark: | 
| Güneydoğu Asya | :heavy_check_mark: | :heavy_check_mark: |
| Doğu Japonya | :heavy_check_mark: | :heavy_check_mark: | 
| Doğu Avustralya | :heavy_check_mark: | :heavy_check_mark: | 
| Orta Kanada | :heavy_check_mark: | :heavy_check_mark: | 
| Orta Fransa | :heavy_check_mark: | :heavy_check_mark: | 

Esnek sunucu için daha fazla bölge eklemeye devam ediyoruz.

## <a name="migration"></a>Geçiş

Hizmet, PostgreSQL 'in topluluk sürümünü çalıştırır. Bu, tam uygulama uyumluluğuna izin verir ve PostgreSQL altyapısında geliştirilen mevcut bir uygulamayı esnek sunucuya geçirmek için en az bir yeniden düzenleme maliyeti gerektirir. 

- **Döküm ve geri yükleme** – kullanıcıların bazı kapalı kalma süresini karşılayabileceği, pg_restore pg_dump gibi topluluk araçlarını kullanarak döküm ve geri yükleme işlemleri için en hızlı geçiş yolu sağlayabildiği çevrimdışı geçişler için. Ayrıntılar için bkz. [döküm kullanarak geçiş ve geri yükleme](../howto-migrate-using-dump-and-restore.md) .
- **Azure veritabanı geçiş hizmeti** : en az kapalı kalma süresiyle esnek sunucuya sorunsuz ve Basitleştirilmiş geçişler Için Azure veritabanı geçiş hizmeti yararlanılabilir olabilir. [CLI aracılığıyla](../../dms/tutorial-postgresql-azure-postgresql-online.md)Portal ve DMS [aracılığıyla DMS](../../dms/tutorial-postgresql-azure-postgresql-online-portal.md) 'e bakın. PostgreSQL için Azure veritabanı-tek sunucudan esnek sunucuya geçiş yapabilirsiniz. Ayrıntılar için bu [DMS makalesine](../../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) bakın.

## <a name="contacts"></a>Kişiler
PostgreSQL için Azure veritabanı esnek sunucusuna sahip olabileceğiniz tüm sorular veya öneriler için, PostgreSQL için Azure veritabanı ekibine bir e-posta gönderin ([ @Ask PostgreSQL IÇIN Azure DB](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Bu e-posta adresinin bir teknik destek diğer adı olmadığına lütfen unutmayın.

Buna ek olarak, aşağıdaki iletişim noktalarını uygun şekilde göz önünde bulundurun:

- Azure Desteği ile iletişim kurmak için [Azure portaldan bir bilet oluşturun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Hesabınızla ilgili bir sorun gidermek için Azure portalda bir [destek isteği](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) oluşturun.
- Görüş bildirmek veya yeni özellikler istemek için [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql) aracılığıyla bir giriş oluşturun.
  

## <a name="next-steps"></a>Sonraki adımlar

Artık PostgreSQL için Azure veritabanı esnek sunucu dağıtım moduna bir giriş okuduğunuzdan, ilk sunucunuzu oluşturmaya hazırsınız: [Azure Portal kullanarak PostgreSQL Için Azure veritabanı-esnek sunucu oluşturma](./quickstart-create-server-portal.md)