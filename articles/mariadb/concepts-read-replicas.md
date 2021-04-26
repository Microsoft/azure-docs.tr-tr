---
title: Çoğaltmaları oku-MariaDB için Azure veritabanı
description: "MariaDB için Azure veritabanı 'nda çoğaltmaları okuma hakkında bilgi edinin: bölge seçme, çoğaltmalar oluşturma, çoğaltmalara bağlanma, çoğaltmayı izleme ve çoğaltmayı durdurma."
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.custom: references_regions
ms.openlocfilehash: 39547e3156a684293a0624f974a8b0930f656485
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664223"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>MariaDB için Azure Veritabanı’nda okuma amaçlı çoğaltmalar

Okuma amaçlı çoğaltma özelliği, MariaDB için Azure Veritabanı’ndan salt okunur bir sunucuya verileri çoğaltmanıza olanak sağlar. Kaynak sunucudan en fazla beş çoğaltmaya çoğaltabilirsiniz. Çoğaltmalar, küresel işlem KIMLIĞI (GTıD) ile MariaDB altyapısının ikili günlük (binlog) dosya konumu tabanlı çoğaltma teknolojisi kullanılarak zaman uyumsuz olarak güncelleştirilir. Binlog çoğaltma hakkında daha fazla bilgi edinmek için bkz. [binlog Çoğaltmaya genel bakış](https://mariadb.com/kb/en/library/replication-overview/).

Çoğaltmalar, yönettiğiniz yeni sunuculardır ve bu sunucular, MariaDB sunucuları için normal Azure veritabanı ile benzerdir. Her okuma çoğaltması için, sanal çekirdekler ve depolama biriminde GB/ay içinde sağlanan işlem için faturalandırılırsınız.

GTıD çoğaltma hakkında daha fazla bilgi için bkz. [MariaDB çoğaltma belgeleri](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Bu makale, Microsoft 'un artık kullandığı bir terim olan _bağımlı_ dönem başvuruları içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.

## <a name="when-to-use-a-read-replica"></a>Okuma çoğaltması ne zaman kullanılır?

Okuma çoğaltması özelliği, okuma yoğunluklu iş yüklerinin performansını ve ölçeğini artırmaya yardımcı olur. Okuma iş yükleri çoğaltmalar için yalıtılabilir, ancak yazma iş yükleri birincilde yönlendirilebilir.

Yaygın bir senaryo, bı ve analitik iş yüklerinin raporlama için veri kaynağı olarak okuma çoğaltmasını kullanmasını sağlar.

Çoğaltmalar salt okunurdur, birincil üzerindeki yazma kapasitesini doğrudan azaltmazlar. Bu özellik, yazma açısından yoğun iş yükleri için uygun değildir.

Okuma çoğaltması özelliği zaman uyumsuz çoğaltma kullanır. Özelliği, zaman uyumlu çoğaltma senaryolarına yönelik değildir. Kaynak ve çoğaltma arasında ölçülebilir bir gecikme olacaktır. Çoğaltılan veriler, sonunda birincil verilerle tutarlı hale gelir. Bu gecikmeyi barındırabilecek iş yükleri için bu özelliği kullanın.

## <a name="cross-region-replication"></a>Bölgeler arası çoğaltma

Kaynak sunucunuzdaki farklı bir bölgede bir okuma çoğaltması oluşturabilirsiniz. Çapraz bölge çoğaltma, olağanüstü durum kurtarma planlaması veya kullanıcılarınıza daha yakın veri getirme gibi senaryolar için yararlı olabilir.

[MariaDB bölgesinde herhangi bir Azure veritabanı için](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb)kaynak sunucunuz olabilir.  Kaynak sunucu, eşleştirilmiş bölgede veya evrensel çoğaltma bölgelerinde bir çoğaltmaya sahip olabilir. Aşağıdaki resimde, kaynak bölgenize göre hangi çoğaltma bölgelerinin kullanılabildiği gösterilmektedir.

[![Çoğaltma bölgelerini oku](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Evrensel çoğaltma bölgeleri

Kaynak sunucunuzun bulunduğu yere bakılmaksızın, aşağıdaki bölgelerin herhangi birinde bir okuma çoğaltması oluşturabilirsiniz. Desteklenen evrensel çoğaltma bölgeleri şunları içerir:

Avustralya Doğu, Avustralya Güneydoğu, Brezilya Güney, Kanada Orta, Kanada Doğu, Orta ABD, Doğu Asya, Doğu ABD, Doğu ABD 2, Japonya Doğu, Japonya Batı, Kore Orta, Kore Güney, Orta Kuzey ABD, Kuzey Avrupa, Orta Güney ABD, Güneydoğu Asya, UK Güney, UK Batı, Batı Avrupa, Batı ABD, Batı ABD 2, Orta Batı ABD.

### <a name="paired-regions"></a>Eşleştirilmiş bölgeler

Evrensel çoğaltma bölgelerine ek olarak, kaynak sunucunuzun Azure eşlenmiş bölgesinde bir okuma çoğaltması oluşturabilirsiniz. Bölgenizin çiftini bilmiyorsanız [Azure eşlenmiş bölgeler makalesinden](../best-practices-availability-paired-regions.md)daha fazla bilgi edinebilirsiniz.

Olağanüstü durum kurtarma planlaması için çapraz bölge çoğaltmaları kullanıyorsanız, çoğaltmayı diğer bölgelerden biri yerine eşleştirilmiş bölgede oluşturmanızı öneririz. Eşleştirilmiş bölgeler eşzamanlı güncelleştirmeleri önleyin ve fiziksel yalıtımın ve veri fazlalığını önceliklendirir.  

Ancak göz önünde bulundurulması gereken sınırlamalar vardır: 

* Bölgesel kullanılabilirlik: MariaDB için Azure veritabanı Fransa Orta, BAE Kuzey ve Almanya Orta sunulmaktadır. Ancak, eşleştirilmiş bölgeleri kullanılamaz.

* Tek yönlü çiftler: bazı Azure bölgeleri yalnızca bir yönde eşleştirilmelidir. Bu bölgeler Batı Hindistan, Brezilya Güney ve US Gov Virginia içerir. 
   Bu, Batı Hindistan içindeki bir kaynak sunucunun Güney Hindistan bir çoğaltma oluşturmasıdır. Ancak, Güney Hindistan bir kaynak sunucu Batı Hindistan bir çoğaltma oluşturamaz. Bunun nedeni, Batı Hindistan ikincil bölgesinin Güney Hindistan, ancak Güney Hindistan ikincil bölgesi Batı Hindistan değildir.

## <a name="create-a-replica"></a>Çoğaltma oluşturma

> [!IMPORTANT]
> Çoğaltma oku özelliği yalnızca Genel Amaçlı veya bellek için Iyileştirilmiş fiyatlandırma katmanlarında bulunan MariaDB sunucuları için Azure veritabanı 'nda kullanılabilir. Kaynak sunucunun bu fiyatlandırma katmanlarından birinde olduğundan emin olun.

Kaynak sunucuda var olan bir çoğaltma sunucusu yoksa, kaynak ilk olarak çoğaltma için hazırlamak üzere yeniden başlatılır.

Çoğaltma oluşturma iş akışını başlattığınızda, MariaDB sunucusu için boş bir Azure veritabanı oluşturulur. Yeni sunucu, kaynak sunucuda bulunan verilerle doldurulur. Oluşturma süresi, kaynaktaki veri miktarına ve son haftalık tam yedeklemeden bu yana geçen zamana bağlıdır. Süre, birkaç dakika ile birkaç saat arasında değişebilir.

> [!NOTE]
> Sunucularınızda bir depolama uyarısı ayarlanmamışsa, bunu yapmanızı öneririz. Bu uyarı, bir sunucu depolama sınırına yaklaştığı zaman, çoğaltmayı etkileyecek şekilde size bildirir.

[Azure Portal bir okuma çoğaltması oluşturmayı](howto-read-replicas-portal.md)öğrenin.

## <a name="connect-to-a-replica"></a>Bir çoğaltmaya bağlanma

Oluşturma sırasında bir çoğaltma, kaynak sunucunun güvenlik duvarı kurallarını devralır. Daha sonra, bu kurallar kaynak sunucudan bağımsızdır.

Çoğaltma, yönetim hesabını kaynak sunucudan devralır. Kaynak sunucudaki tüm Kullanıcı hesapları, okuma çoğaltmalarına çoğaltılır. Bir okuma çoğaltmasına yalnızca kaynak sunucuda bulunan Kullanıcı hesaplarını kullanarak bağlanabilirsiniz.

Bir bilgisayar ana bilgisayar adını ve geçerli bir kullanıcı hesabını kullanarak, MariaDB sunucusu için normal bir Azure veritabanında yaptığınız gibi çoğaltmaya bağlanabilirsiniz. Yönetici Kullanıcı adı **myadmin** olan **myreplica** adlı BIR sunucu için MySQL CLI kullanarak çoğaltmaya bağlanabilirsiniz:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

İstemde, Kullanıcı hesabının parolasını girin.

## <a name="monitor-replication"></a>Çoğaltmayı izleme

MariaDB için Azure veritabanı, Azure Izleyici 'de **saniye cinsinden çoğaltma gecikmesi** sağlar. Bu ölçüm yalnızca çoğaltmalar için kullanılabilir.

Bu ölçüm, `seconds_behind_master` MariaDB 'nin komutunda kullanılabilir olan ölçüm kullanılarak hesaplanır `SHOW SLAVE STATUS` .

Çoğaltma gecikmesi iş yükünüz için kabul edilebilir bir değere ulaştığında sizi bilgilendirmek için bir uyarı ayarlayın.

## <a name="stop-replication"></a>Çoğaltmayı durdurma

Kaynak ve çoğaltma arasında çoğaltmayı durdurabilirsiniz. Kaynak sunucu ve okuma çoğaltması arasında çoğaltma durdurulduktan sonra, çoğaltma tek başına bir sunucu haline gelir. Tek başına sunucusundaki veriler, çoğaltma durdurma komutunun başlatıldığı zamanda çoğaltma üzerinde kullanılabilir olan veri. Tek başına sunucu, kaynak sunucuyla ilgili değildir.

Çoğaltma çoğaltmasını durdurmayı seçtiğinizde, önceki kaynak ve diğer çoğaltmaların tüm bağlantılarını kaybeder. Kaynak ve çoğaltma arasında otomatik yük devretme yoktur.

> [!IMPORTANT]
> Tek başına sunucu tekrar bir çoğaltmaya yapılamaz.
> Bir okuma çoğaltmasında çoğaltmayı durdurmadan önce, çoğaltmanın gerekli tüm verilere sahip olduğundan emin olun.

[Bir çoğaltmaya çoğaltmayı durdurmayı](howto-read-replicas-portal.md)öğrenin.

## <a name="failover"></a>Yük devretme

Kaynak ve çoğaltma sunucuları arasında otomatik yük devretme yoktur.

Çoğaltma zaman uyumsuz olduğundan, kaynak ve çoğaltma arasında bir gecikme vardır. Gecikme miktarı, kaynak sunucuda çalışan iş yükünün ne kadar ağır ve veri merkezleri arasındaki gecikme süresi gibi bir dizi faktörden etkilenebilir. Çoğu durumda, çoğaltma gecikmesi birkaç saniyeyle birkaç dakika arasında değişir. Her bir çoğaltma için kullanılabilen ölçüm *çoğaltması* gecikmesini kullanarak gerçek çoğaltma gecikmelerinizi izleyebilirsiniz. Bu ölçüm, son yeniden yürütülmüş işlemden bu yana geçen süreyi gösterir. Yineleme gecikmesini bir süre içinde gözlemleyerek ortalama gecikmenizin ne olduğunu tanımlamanızı öneririz. Çoğaltma gecikmesi üzerinde bir uyarı ayarlayabilirsiniz, böylece beklenen aralığın dışında olursa işlem yapabilirsiniz.

> [!Tip]
> Çoğaltmaya yük devretmek, çoğaltmayı kaynaktan geri bağladığınızda oluşan gecikme süresi, ne kadar veri kaybı olduğunu gösterir.

Bir çoğaltmaya yük devretmek istediğinizde,

1. Çoğaltmaya çoğaltmayı durdurun.

   Bu adım, çoğaltma sunucusunun yazmaları kabul edebilmesini sağlamak için gereklidir. Bu işlemin bir parçası olarak, çoğaltma sunucusu birincil sunucudan kaldırılacak. Çoğaltmayı durdur işlemini başlattıktan sonra, arka uç işleminin genellikle yaklaşık 2 dakika sürer. Bu eylemin etkilerini anlamak için bu makalenin [çoğaltmayı durdur](#stop-replication) bölümüne bakın.

2. Uygulamanızı (eski) çoğaltmaya işaret edin.

   Her sunucunun benzersiz bir bağlantı dizesi vardır. Uygulamanızı birincil yerine (eski) çoğaltmaya işaret etmek üzere güncelleştirin.

Uygulamanız okuma ve yazma işlemlerini başarıyla tamamladıktan sonra, yük devretmeyi tamamladınız. Bir sorunu saptadığınızda ve yukarıdaki 1. ve 2. adımları tamamladıktan sonra uygulama deneyimlerinizin ne kadar süre açık olacağını gösterir.

## <a name="considerations-and-limitations"></a>Önemli noktalar ve sınırlamalar

### <a name="pricing-tiers"></a>Fiyatlandırma katmanları

Okuma çoğaltmaları Şu anda yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş fiyatlandırma katmanlarında kullanılabilir.

> [!NOTE]
> Çoğaltma sunucusunu çalıştırmanın maliyeti, çoğaltma sunucusunun çalıştığı bölgeyi temel alır.

### <a name="source-server-restart"></a>Kaynak sunucu yeniden başlatması

Mevcut çoğaltmaları olmayan bir kaynak için çoğaltma oluşturduğunuzda, kaynak ilk olarak çoğaltma için hazırlamak üzere yeniden başlatılır. Bunu dikkate alın ve yoğun bir süre boyunca bu işlemleri gerçekleştirin.

### <a name="new-replicas"></a>Yeni çoğaltmalar

Bir okuma çoğaltması, MariaDB sunucusu için yeni bir Azure veritabanı olarak oluşturulur. Var olan bir sunucu bir çoğaltmaya yapılamaz. Başka bir okuma çoğaltmasının çoğaltmasını oluşturamazsınız.

### <a name="replica-configuration"></a>Çoğaltma yapılandırması

Çoğaltma, birincil ile aynı sunucu yapılandırması kullanılarak oluşturulur. Bir çoğaltma oluşturulduktan sonra, çeşitli ayarlar kaynak sunucudan bağımsız olarak değiştirilebilir: işlem oluşturma, sanal çekirdek, depolama, yedekleme saklama süresi ve MariaDB altyapı sürümü. Fiyatlandırma Katmanı, temel katmandan veya dışında bağımsız olarak da değiştirilebilir.

> [!IMPORTANT]
> Kaynak sunucu yapılandırması yeni değerlere güncelleştirilmeden önce, çoğaltma yapılandırmasını eşit veya daha büyük değerlerle güncelleştirin. Bu eylem, çoğaltmanın birincil üzerinde yapılan değişikliklerle devam etmesini sağlar.

Çoğaltma oluşturulduğunda, güvenlik duvarı kuralları ve parametre ayarları kaynak sunucudan çoğaltmaya devralınır. Daha sonra, çoğaltmanın kuralları bağımsızdır.

### <a name="stopped-replicas"></a>Durdurulan çoğaltmalar

Kaynak sunucuyla bir okuma çoğaltması arasında çoğaltmayı durdurursanız, durdurulan çoğaltma hem okuma hem de yazma işlemlerini kabul eden tek başına bir sunucu haline gelir. Tek başına sunucu tekrar bir çoğaltmaya yapılamaz.

### <a name="deleted-source-and-standalone-servers"></a>Kaynak ve tek başına sunucular silindi

Bir kaynak sunucu silindiğinde, çoğaltma tüm okuma çoğaltmalarına durdurulur. Bu çoğaltmalar otomatik olarak tek başına sunucular olur ve hem okuma hem de yazma işlemlerini kabul edebilir. Kaynak sunucunun kendisi silinir.

### <a name="user-accounts"></a>Kullanıcı hesapları

Kaynak sunucudaki kullanıcılar okuma çoğaltmalarına çoğaltılır. Bir okuma çoğaltmasına yalnızca kaynak sunucuda bulunan Kullanıcı hesaplarını kullanarak bağlanabilirsiniz.

### <a name="server-parameters"></a>Sunucu parametreleri

Verilerin eşitlenmemiş duruma gelmesini ve olası veri kaybını önlemek için, okuma amaçlı çoğaltmaları kullanırken bazı sunucu parametreleri güncelleştirmeye karşı kilitlenir.

Aşağıdaki sunucu parametreleri hem kaynak hem de çoğaltma sunucularında kilitlidir:

* [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
* [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

[`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler)Parametresi, çoğaltma sunucularında kilitlidir.

Kaynak sunucuda yukarıdaki parametrelerden birini güncelleştirmek için lütfen çoğaltma sunucularını silin, birincil kaynaktaki parametre değerini güncelleştirin ve çoğaltmaları yeniden oluşturun.

### <a name="other"></a>Diğer

* Bir çoğaltmanın çoğaltmasını oluşturma desteklenmiyor.
* Bellek içi tablolar çoğaltmaların eşitlenmemiş hale gelmesine neden olabilir. Bu, MariaDB çoğaltma teknolojisinin bir sınırlamasıdır.
* Kaynak sunucu tablolarının birincil anahtarlara sahip olduğundan emin olun. Birincil anahtarların olmaması, kaynak ve çoğaltmalar arasında çoğaltma gecikmesine neden olabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Portal kullanarak okuma çoğaltmaları oluşturmayı ve yönetmeyi](howto-read-replicas-portal.md) öğrenin
* [Azure CLI ve REST API kullanarak okuma çoğaltmaları oluşturma ve yönetme](howto-read-replicas-cli.md) hakkında bilgi edinin
