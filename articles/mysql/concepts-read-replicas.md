---
title: MySQL için Azure veritabanı 'nda çoğaltmaları okuyun.
description: "MySQL için Azure veritabanı 'nda çoğaltmaları okuma hakkında bilgi edinin: bölge seçme, çoğaltmalar oluşturma, çoğaltmalara bağlanma, çoğaltmayı izleme ve çoğaltmayı durdurma."
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/17/2019
ms.openlocfilehash: 66864870f29729e54ad06aef1208641f673c0612
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158321"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>MySQL için Azure Veritabanı’nda okuma amaçlı çoğaltmalar

Okuma amaçlı çoğaltma özelliği, MySQL için Azure Veritabanı’ndan salt okunur bir sunucuya verileri çoğaltmanıza olanak sağlar. Ana sunucudan en fazla beş çoğaltmaya çoğaltabilirsiniz. Çoğaltmalar, MySQL altyapısının yerel ikili günlük (binlog) dosya konumu tabanlı çoğaltma teknolojisini kullanılarak zaman uyumsuz olarak güncelleştirilir. Binlog çoğaltma hakkında daha fazla bilgi edinmek için [MySQL binlog çoğaltmasına genel bakış](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)bölümüne bakın.

Çoğaltmalar, yönettiğiniz yeni sunuculardan MySQL için normal Azure veritabanı sunucularına benzer. Her okuma çoğaltması için, sanal çekirdekler ve depolama biriminde GB/ay içinde sağlanan işlem için faturalandırılırsınız.

MySQL çoğaltma özellikleri ve sorunları hakkında daha fazla bilgi edinmek için [MySQL Çoğaltma belgelerine](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)bakın.

## <a name="when-to-use-a-read-replica"></a>Okuma çoğaltması ne zaman kullanılır?

Okuma çoğaltması özelliği, okuma yoğunluklu iş yüklerinin performansını ve ölçeğini artırmaya yardımcı olur. Okuma iş yükleri çoğaltmalar için yalıtılabilir, yazma iş yükleri ana ağa yönlendirilebilir.

Yaygın bir senaryo, bı ve analitik iş yüklerinin raporlama için veri kaynağı olarak okuma çoğaltmasını kullanmasını sağlar.

Çoğaltmalar salt okunurdur, ana bilgisayardaki yazma kapasitesini doğrudan azaltmazlar. Bu özellik, yazma yoğunluklu iş yükleri için hedeflenmez.

Okuma çoğaltması özelliği MySQL zaman uyumsuz çoğaltma kullanır. Özelliği, zaman uyumlu çoğaltma senaryolarına yönelik değildir. Ana ve çoğaltma arasında ölçülebilir bir gecikme olacaktır. Çoğaltılan veriler, sonunda, ana sunucudaki verilerle tutarlı hale gelir. Bu gecikmeyi barındırabilecek iş yükleri için bu özelliği kullanın.

## <a name="cross-region-replication"></a>Çapraz bölge çoğaltma
Ana sunucunuzdaki farklı bir bölgede bir okuma çoğaltması oluşturabilirsiniz. Çapraz bölge çoğaltma, olağanüstü durum kurtarma planlaması veya kullanıcılarınıza daha yakın veri getirme gibi senaryolar için yararlı olabilir.

[MySQL Için Azure veritabanı bölgesinde](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)bir ana sunucunuz olabilir.  Ana sunucu, eşleştirilmiş bölge veya evrensel çoğaltma bölgelerinde bir çoğaltmaya sahip olabilir. Aşağıdaki resimde, ana bölgenize göre hangi çoğaltma bölgelerinin kullanılabildiği gösterilmektedir.

[![okuma çoğaltması bölgeleri](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Evrensel çoğaltma bölgeleri
Ana sunucunuzun bulunduğu yere bakılmaksızın, aşağıdaki bölgelerin herhangi birinde bir okuma çoğaltması oluşturabilirsiniz. Desteklenen evrensel çoğaltma bölgeleri şunları içerir:

Avustralya Doğu, Avustralya Güneydoğu, Orta ABD, Doğu Asya, Doğu ABD, Doğu ABD 2, Japonya Doğu, Japonya Batı, Kore Orta, Kore Güney, Orta Kuzey ABD, Kuzey Avrupa, Orta Güney ABD, Güneydoğu Asya, UK Güney, UK Batı, Batı Avrupa, Batı ABD, Batı ABD 2.


### <a name="paired-regions"></a>Eşleştirilmiş bölgeler
Evrensel çoğaltma bölgelerine ek olarak, ana sunucunuzun Azure eşlenmiş bölgesinde bir okuma çoğaltması oluşturabilirsiniz. Bölgenizin çiftini bilmiyorsanız [Azure eşlenmiş bölgeler makalesinden](../best-practices-availability-paired-regions.md)daha fazla bilgi edinebilirsiniz.

Olağanüstü durum kurtarma planlaması için çapraz bölge çoğaltmaları kullanıyorsanız, çoğaltmayı diğer bölgelerden biri yerine eşleştirilmiş bölgede oluşturmanızı öneririz. Eşleştirilmiş bölgeler eşzamanlı güncelleştirmeleri önleyin ve fiziksel yalıtımın ve veri fazlalığını önceliklendirir.  

Ancak göz önünde bulundurulması gereken sınırlamalar vardır: 

* Bölgesel kullanılabilirlik: MySQL için Azure veritabanı Batı ABD 2, Fransa Orta, BAE Kuzey ve Almanya Orta kullanılabilir. Ancak, eşleştirilmiş bölgeleri kullanılamaz.
    
* Tek yönlü çiftler: bazı Azure bölgeleri yalnızca bir yönde eşleştirilmelidir. Bu bölgeler Batı Hindistan, Brezilya Güney ve US Gov Virginia içerir. 
   Bu, Batı Hindistan 'deki bir ana sunucunun Güney Hindistan bir çoğaltma oluşturmasıdır. Ancak, Güney Hindistan bir ana sunucu Batı Hindistan bir çoğaltma oluşturamaz. Bunun nedeni, Batı Hindistan ikincil bölgesinin Güney Hindistan, ancak Güney Hindistan ikincil bölgesi Batı Hindistan değildir.


## <a name="create-a-replica"></a>Çoğaltma oluşturma

Ana sunucuda var olan bir çoğaltma sunucusu yoksa, önce yönetici kendisini çoğaltma için hazırlamak üzere ilk olarak yeniden başlatılır.

Çoğaltma oluşturma iş akışı 'nı başlattığınızda, MySQL için boş bir Azure veritabanı sunucusu oluşturulur. Yeni sunucu, ana sunucuda bulunan verilerle doldurulur. Oluşturma süresi, ana bilgisayardaki veri miktarına ve son haftalık tam yedeklemeden bu yana geçen zamana bağlıdır. Süre, birkaç dakika ile birkaç saat arasında değişebilir.

Her çoğaltma, depolama [otomatik büyüme](concepts-pricing-tiers.md#storage-auto-grow)için etkinleştirilir. Otomatik büyüme özelliği, çoğaltmanın kendisine çoğaltılan verilerle kalmasına izin verir ve çoğaltma dışı hatalardan kaynaklanan bir kesintiye uğramasını önler.

[Azure Portal bir okuma çoğaltması oluşturmayı](howto-read-replicas-portal.md)öğrenin.

## <a name="connect-to-a-replica"></a>Bir çoğaltmaya bağlanma

Oluşturma sırasında bir çoğaltma, ana sunucunun güvenlik duvarı kurallarını veya VNet hizmet uç noktasını devralır. Daha sonra, bu kurallar ana sunucudan bağımsızdır.

Çoğaltma, yönetici hesabını ana sunucudan devralır. Ana sunucudaki tüm Kullanıcı hesapları, okuma çoğaltmalarına çoğaltılır. Bir okuma çoğaltmasına yalnızca ana sunucuda bulunan Kullanıcı hesaplarını kullanarak bağlanabilirsiniz.

Bir MySQL için Azure veritabanı sunucusunda yaptığınız gibi, ana bilgisayar adını ve geçerli bir kullanıcı hesabını kullanarak çoğaltmaya bağlanabilirsiniz. Yönetici Kullanıcı adı **myadmin**olan **myreplica** adlı BIR sunucu için MySQL CLI kullanarak çoğaltmaya bağlanabilirsiniz:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

İstemde, Kullanıcı hesabının parolasını girin.

## <a name="monitor-replication"></a>Çoğaltmayı izleme

MySQL için Azure veritabanı, Azure Izleyici 'de **saniye cinsinden yineleme gecikmesi** sağlar. Bu ölçüm yalnızca çoğaltmalar için kullanılabilir.

Bu ölçüm, MySQL 'in `SHOW SLAVE STATUS` komutunda bulunan `seconds_behind_master` ölçümü kullanılarak hesaplanır.

Çoğaltma gecikmesi iş yükünüz için kabul edilebilir bir değere ulaştığında sizi bilgilendirmek için bir uyarı ayarlayın.

## <a name="stop-replication"></a>Çoğaltmayı durdurma

Ana ve çoğaltma arasında çoğaltmayı durdurabilirsiniz. Bir ana sunucu ve bir okuma çoğaltması arasında çoğaltma durdurulduktan sonra çoğaltma tek başına bir sunucu haline gelir. Tek başına sunucusundaki veriler, çoğaltma durdurma komutunun başlatıldığı zamanda çoğaltma üzerinde kullanılabilir olan veri. Tek başına sunucu, ana sunucu ile birlikte yakalamaz.

Çoğaltma çoğaltmasını durdurmayı seçtiğinizde, önceki ana ve diğer çoğaltmalara ait tüm bağlantıları kaybeder. Ana ve onun çoğaltması arasında otomatik yük devretme yoktur.

> [!IMPORTANT]
> Tek başına sunucu tekrar bir çoğaltmaya yapılamaz.
> Bir okuma çoğaltmasında çoğaltmayı durdurmadan önce, çoğaltmanın gerekli tüm verilere sahip olduğundan emin olun.

[Bir çoğaltmaya çoğaltmayı durdurmayı](howto-read-replicas-portal.md)öğrenin.

## <a name="considerations-and-limitations"></a>Önemli noktalar ve sınırlamalar

### <a name="pricing-tiers"></a>Fiyatlandırma katmanları

Okuma çoğaltmaları Şu anda yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş fiyatlandırma katmanlarında kullanılabilir.

### <a name="master-server-restart"></a>Ana sunucu yeniden başlatması

Var olan çoğaltmaları olmayan bir ana öğe için bir çoğaltma oluşturduğunuzda, ana, önce kendisini çoğaltma için hazırlamak üzere yeniden başlatılır. Bunu dikkate alın ve yoğun bir süre boyunca bu işlemleri gerçekleştirin.

### <a name="new-replicas"></a>Yeni çoğaltmalar

Bir okuma çoğaltması, MySQL için yeni bir Azure veritabanı sunucusu olarak oluşturulur. Var olan bir sunucu bir çoğaltmaya yapılamaz. Başka bir okuma çoğaltmasının çoğaltmasını oluşturamazsınız.

### <a name="replica-configuration"></a>Çoğaltma yapılandırması

Bir çoğaltma, ana öğe ile aynı sunucu yapılandırması kullanılarak oluşturulur. Bir çoğaltma oluşturulduktan sonra, birden fazla ayar ana sunucudan bağımsız olarak değiştirilebilir: işlem oluşturma, sanal çekirdek, depolama ve yedekleme saklama süresi. Fiyatlandırma Katmanı, temel katmandan veya dışında bağımsız olarak da değiştirilebilir.

> [!IMPORTANT]
> Ana sunucu yapılandırması yeni değerlere güncelleştirilmeden önce, çoğaltma yapılandırmasını eşit veya daha büyük bir değere güncelleştirin. Bu eylem, çoğaltmanın ana kopya üzerinde yapılan değişiklikleri yansıtmasını sağlar.

Çoğaltma oluşturulduğunda güvenlik duvarı kuralları, sanal ağ kuralları ve parametre ayarları ana sunucudan çoğaltmaya devralınır. Daha sonra, çoğaltmanın kuralları bağımsızdır.

### <a name="stopped-replicas"></a>Durdurulan çoğaltmalar

Bir ana sunucu ve bir okuma çoğaltması arasında çoğaltmayı durdurursanız, durdurulan çoğaltma hem okuma hem de yazma işlemlerini kabul eden tek başına bir sunucu haline gelir. Tek başına sunucu tekrar bir çoğaltmaya yapılamaz.

### <a name="deleted-master-and-standalone-servers"></a>Ana ve tek başına sunucular silindi

Bir ana sunucu silindiğinde, çoğaltma tüm okuma çoğaltmalarına durdurulur. Bu çoğaltmalar tek başına sunucular haline gelir. Ana sunucunun kendisi silinir.

### <a name="user-accounts"></a>Kullanıcı hesapları

Ana sunucudaki kullanıcılar okuma çoğaltmalarına çoğaltılır. Bir okuma çoğaltmasına yalnızca ana sunucuda bulunan Kullanıcı hesaplarını kullanarak bağlanabilirsiniz.

### <a name="server-parameters"></a>Sunucu parametreleri

Verilerin eşitlenmemiş duruma gelmesini ve olası veri kaybını önlemek için, okuma amaçlı çoğaltmaları kullanırken bazı sunucu parametreleri güncelleştirmeye karşı kilitlenir.

Aşağıdaki sunucu parametreleri hem ana hem de çoğaltma sunucularında kilitlidir:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

[`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) parametresi, çoğaltma sunucularında kilitlidir. 

### <a name="other"></a>Diğer

- Genel işlem tanımlayıcıları (GTıD) desteklenmez.
- Bir çoğaltmanın çoğaltmasını oluşturma desteklenmiyor.
- Bellek içi tablolar çoğaltmaların eşitlenmemiş hale gelmesine neden olabilir. Bu, MySQL Çoğaltma teknolojisinin bir sınırlamasıdır. Daha fazla bilgi için [MySQL Reference belgelerindeki](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) daha fazla bilgi edinin.
- Ana sunucu tablolarının birincil anahtarlara sahip olduğundan emin olun. Birincil anahtarların olmaması, ana ve çoğaltmalar arasında çoğaltma gecikmesine neden olabilir.
- [MySQL belgelerindeki](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) MySQL Çoğaltma sınırlamalarının tam listesini gözden geçirin

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Portal kullanarak okuma çoğaltmaları oluşturmayı ve yönetmeyi](howto-read-replicas-portal.md) öğrenin
- [Azure CLI ve REST API kullanarak okuma çoğaltmaları oluşturma ve yönetme](howto-read-replicas-cli.md) hakkında bilgi edinin
