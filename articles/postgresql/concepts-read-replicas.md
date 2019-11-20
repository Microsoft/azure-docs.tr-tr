---
title: PostgreSQL için Azure veritabanı 'nda çoğaltmaları okuma-tek sunucu
description: Bu makalede PostgreSQL için Azure veritabanı 'nda çoğaltmayı oku özelliği (tek sunucu) açıklanmaktadır.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/17/2019
ms.openlocfilehash: 5d3d752f549fe336f584fa3534b61cb5a009c3bd
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158812"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'nda çoğaltmaları okuma-tek sunucu

Çoğaltma oku özelliği, bir PostgreSQL için Azure veritabanı sunucusunun bir salt okunurdur sunucusuna veri çoğaltmanıza olanak sağlar. Ana sunucudan en fazla beş çoğaltmaya çoğaltabilirsiniz. Çoğaltmalar, PostgreSQL altyapısı yerel çoğaltma teknolojisiyle zaman uyumsuz olarak güncelleştirilir.

Çoğaltmalar, PostgreSQL için normal Azure veritabanı sunucularına benzer şekilde yönettiğiniz yeni sunuculardır. Her okuma çoğaltması için, sanal çekirdekler ve depolama biriminde GB/ay içinde sağlanan işlem için faturalandırılırsınız.

[Çoğaltmaları oluşturma ve yönetme](howto-read-replicas-portal.md)hakkında bilgi edinin.

## <a name="when-to-use-a-read-replica"></a>Okuma çoğaltması ne zaman kullanılır?
Okuma çoğaltması özelliği, okuma yoğunluklu iş yüklerinin performansını ve ölçeğini artırmaya yardımcı olur. Okuma iş yükleri çoğaltmalar için yalıtılabilir, yazma iş yükleri ana ağa yönlendirilebilir.

Yaygın bir senaryo, bı ve analitik iş yüklerinin raporlama için veri kaynağı olarak okuma çoğaltmasını kullanmasını sağlar.

Çoğaltmalar salt okunurdur, ana bilgisayardaki yazma kapasitesini doğrudan azaltmazlar. Bu özellik, yazma yoğunluklu iş yükleri için hedeflenmez.

Okuma çoğaltması özelliği PostgreSQL zaman uyumsuz çoğaltmasını kullanır. Özelliği, zaman uyumlu çoğaltma senaryolarına yönelik değildir. Ana ve çoğaltma arasında ölçülebilir bir gecikme olacaktır. Çoğaltılan veriler, sonunda, ana sunucudaki verilerle tutarlı hale gelir. Bu gecikmeyi barındırabilecek iş yükleri için bu özelliği kullanın.

## <a name="cross-region-replication"></a>Çapraz bölge çoğaltma
Ana sunucunuzdaki farklı bir bölgede bir okuma çoğaltması oluşturabilirsiniz. Çapraz bölge çoğaltma, olağanüstü durum kurtarma planlaması veya kullanıcılarınıza daha yakın veri getirme gibi senaryolar için yararlı olabilir.

[PostgreSQL Için Azure veritabanı bölgesine](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql)bir ana sunucu ekleyebilirsiniz. Ana sunucu, eşleştirilmiş bölge veya evrensel çoğaltma bölgelerinde bir çoğaltmaya sahip olabilir. Aşağıdaki resimde, ana bölgenize göre hangi çoğaltma bölgelerinin kullanılabildiği gösterilmektedir.

[![okuma çoğaltması bölgeleri](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Evrensel çoğaltma bölgeleri
Ana sunucunuzun bulunduğu yere bakılmaksızın aşağıdaki bölgelerin herhangi birinde her zaman bir okuma çoğaltması oluşturabilirsiniz. Evrensel çoğaltma bölgeleri şunlardır:

Avustralya Doğu, Avustralya Güneydoğu, Orta ABD, Doğu Asya, Doğu ABD, Doğu ABD 2, Japonya Doğu, Japonya Batı, Kore Orta, Kore Güney, Orta Kuzey ABD, Kuzey Avrupa, Orta Güney ABD, Güneydoğu Asya, UK Güney, UK Batı, Batı Avrupa, Batı ABD, Batı ABD 2.


### <a name="paired-regions"></a>Eşleştirilmiş bölgeler
Evrensel çoğaltma bölgelerine ek olarak, ana sunucunuzun Azure eşlenmiş bölgesinde bir okuma çoğaltması oluşturabilirsiniz. Bölgenizin çiftini bilmiyorsanız [Azure eşlenmiş bölgeler makalesinden](../best-practices-availability-paired-regions.md)daha fazla bilgi edinebilirsiniz.

Olağanüstü durum kurtarma planlaması için çapraz bölge çoğaltmaları kullanıyorsanız, çoğaltmayı diğer bölgelerden biri yerine eşleştirilmiş bölgede oluşturmanızı öneririz. Eşleştirilmiş bölgeler eşzamanlı güncelleştirmeleri önleyin ve fiziksel yalıtımın ve veri fazlalığını önceliklendirir.  

Göz önünde bulundurulması gereken sınırlamalar vardır: 

* Bölgesel kullanılabilirlik: PostgreSQL için Azure veritabanı Batı ABD 2, Fransa Orta, BAE Kuzey ve Almanya Orta bulunabilir. Ancak, eşleştirilmiş bölgeleri kullanılamaz.
    
* Tek yönlü çiftler: bazı Azure bölgeleri yalnızca bir yönde eşleştirilmelidir. Bu bölgeler Batı Hindistan Brezilya Güney içerir. 
   Bu, Batı Hindistan 'deki bir ana sunucunun Güney Hindistan bir çoğaltma oluşturmasıdır. Ancak, Güney Hindistan bir ana sunucu Batı Hindistan bir çoğaltma oluşturamaz. Bunun nedeni, Batı Hindistan ikincil bölgesinin Güney Hindistan, ancak Güney Hindistan ikincil bölgesi Batı Hindistan değildir.


## <a name="create-a-replica"></a>Çoğaltma oluşturma
Ana sunucuda `azure.replication_support` parametresi **çoğaltma**olarak ayarlanmış olmalıdır. Bu parametre değiştirildiğinde, değişikliğin etkili olması için sunucu yeniden başlatması gerekir. (`azure.replication_support` parametresi yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş katmanlar için geçerlidir).

Çoğaltma oluşturma iş akışını başlattığınızda, PostgreSQL için boş bir Azure veritabanı sunucusu oluşturulur. Yeni sunucu, ana sunucuda bulunan verilerle doldurulur. Oluşturma süresi, ana bilgisayardaki veri miktarına ve son haftalık tam yedeklemeden bu yana geçen zamana bağlıdır. Süre, birkaç dakika ile birkaç saat arasında değişebilir.

Her çoğaltma, depolama [otomatik büyüme](concepts-pricing-tiers.md#storage-auto-grow)için etkinleştirilir. Otomatik büyüme özelliği, çoğaltmanın kendisine çoğaltılan verilerle kalmasına izin verir ve çoğaltma sırasında depolama hatalarından kaynaklanan bir kesmeyi önler.

Okuma çoğaltması özelliği, mantıksal çoğaltma değil PostgreSQL fiziksel çoğaltmasını kullanır. Çoğaltma yuvaları kullanılarak akış çoğaltma varsayılan işlem modudur. Gerektiğinde, günlük aktarma işlemi yakalamak için kullanılır.

[Azure Portal bir okuma çoğaltması oluşturmayı](howto-read-replicas-portal.md)öğrenin.

## <a name="connect-to-a-replica"></a>Bir çoğaltmaya bağlanma
Bir çoğaltma oluşturduğunuzda, ana sunucunun güvenlik duvarı kuralları veya VNet hizmeti uç noktası aktarılmaz. Bu kuralların çoğaltma için bağımsız olarak ayarlanması gerekir.

Çoğaltma, yönetici hesabını ana sunucudan devralır. Ana sunucudaki tüm Kullanıcı hesapları, okuma çoğaltmalarına çoğaltılır. Bir okuma çoğaltmasına yalnızca ana sunucuda bulunan Kullanıcı hesaplarını kullanarak bağlanabilirsiniz.

Bir PostgreSQL için normal Azure veritabanı sunucusunda yaptığınız gibi, konak adını ve geçerli bir kullanıcı hesabını kullanarak çoğaltmaya bağlanabilirsiniz. Yönetici Kullanıcı adı **myadmin**olan **çoğaltmam** adlı bir sunucu için, psql kullanarak çoğaltmaya bağlanabilirsiniz:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

İstemde, Kullanıcı hesabının parolasını girin.

## <a name="monitor-replication"></a>Çoğaltmayı izleme
PostgreSQL için Azure veritabanı, çoğaltmayı izlemek için iki ölçüm sağlar. İki ölçüm çoğaltmalar ve **çoğaltma gecikmesi** **genelinde en fazla gecikme** . Bu ölçümleri görüntülemeyi öğrenmek için, [okuma çoğaltması nasıl yapılır makalesinin](howto-read-replicas-portal.md) **çoğaltma izleme** bölümüne bakın.

**Çoğaltmalar genelinde en fazla gecikme** ölçümü, ana ve en fazla çoğaltma çoğaltması arasındaki gecikme sayısını bayt cinsinden gösterir. Bu ölçüm yalnızca ana sunucuda kullanılabilir.

**Çoğaltma gecikmesi** ölçümü, son yeniden yürütülmüş işlemden bu yana geçen süreyi gösterir. Ana sunucunuzda gerçekleşen işlem yoksa, ölçüm bu zaman gecikmesini yansıtır. Bu ölçüm yalnızca çoğaltma sunucuları için kullanılabilir. Çoğaltma gecikmesi `pg_stat_wal_receiver` görünümünden hesaplanır:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Çoğaltma gecikmesi iş yükünüz için kabul edilebilir bir değere ulaştığında sizi bilgilendirmek için bir uyarı ayarlayın. 

Ek Öngörüler için, tüm çoğaltmalarda bayt olarak çoğaltma gecikmesini almak üzere ana sunucuyu doğrudan sorgulayın.

PostgreSQL sürüm 10 ' da:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

PostgreSQL sürüm 9,6 ve önceki sürümlerde:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Ana sunucu veya okuma çoğaltması yeniden başlatılırsa, yeniden başlatma ve yakalama için geçen süre çoğaltma gecikmesi ölçüsüne yansıtılır.

## <a name="stop-replication"></a>Çoğaltmayı durdurma
Ana ve çoğaltma arasında çoğaltmayı durdurabilirsiniz. Durdur eylemi, çoğaltmanın yeniden başlatılmasına ve çoğaltma ayarlarını kaldırmasına neden olur. Bir ana sunucu ve bir okuma çoğaltması arasında çoğaltma durdurulduktan sonra çoğaltma tek başına bir sunucu haline gelir. Tek başına sunucusundaki veriler, çoğaltma durdurma komutunun başlatıldığı zamanda çoğaltma üzerinde kullanılabilir olan veri. Tek başına sunucu, ana sunucu ile birlikte yakalamaz.

> [!IMPORTANT]
> Tek başına sunucu tekrar bir çoğaltmaya yapılamaz.
> Bir okuma çoğaltmasında çoğaltmayı durdurmadan önce, çoğaltmanın gerekli tüm verilere sahip olduğundan emin olun.

Çoğaltmayı durdurduğunuzda, çoğaltma önceki ana ve diğer çoğaltmalara ait tüm bağlantıları kaybeder.

[Bir çoğaltmaya çoğaltmayı durdurmayı](howto-read-replicas-portal.md)öğrenin.

## <a name="failover"></a>Yük devretme
Ana ve çoğaltma sunucuları arasında otomatik yük devretme yoktur. 

Çoğaltma zaman uyumsuz olduğundan, ana ve çoğaltma arasında bir gecikme vardır. Gecikme miktarı, ana sunucu üzerinde çalışan iş yükünün ne kadar ağır ve veri merkezleri arasındaki gecikme süresi gibi bir dizi faktörden etkilenebilir. Çoğu durumda, çoğaltma gecikmesi birkaç saniye ile birkaç dakika arasında değişir. Her bir çoğaltma için kullanılabilen ölçüm *çoğaltması*gecikmesini kullanarak gerçek çoğaltma gecikmelerinizi izleyebilirsiniz. Bu ölçüm, son yeniden yürütülmüş işlemden bu yana geçen süreyi gösterir. Yineleme gecikmesini bir süre içinde gözlemleyerek ortalama gecikmenizin ne olduğunu tanımlamanızı öneririz. Çoğaltma gecikmesi üzerinde bir uyarı ayarlayabilirsiniz, böylece beklenen aralığın dışında olursa işlem yapabilirsiniz.

> [!Tip]
> Çoğaltmaya yük devretmek, çoğaltmayı ana bilgisayardan geri bağladığınızda oluşan gecikme, ne kadar veri kaybedildiğine işaret eder.

Bir çoğaltmaya yük devretmek istediğinizde, 

1. Çoğaltmaya çoğaltmayı durdur<br/>
   Bu adım, çoğaltma sunucusunun yazmaları kabul edebilmesini sağlamak için gereklidir. Bu işlemin bir parçası olarak, çoğaltma sunucusu yeniden başlatılır ve ana bilgisayardan kaldırılır. Çoğaltmayı Durdur ' u başlattığınızda, arka uç işleminin genellikle yaklaşık 2 dakika sürer. Bu eylemin etkilerini anlamak için bu makalenin [çoğaltmayı durdur](#stop-replication) bölümüne bakın.
    
2. Uygulamanızı (eski) çoğaltmaya işaret edin<br/>
   Her sunucunun benzersiz bir bağlantı dizesi vardır. Uygulamanızı ana şablon yerine (eski) çoğaltmaya işaret etmek üzere güncelleştirin.
    
Uygulamanız okuma ve yazma işlemlerini başarıyla tamamladıktan sonra, yük devretmeyi tamamladınız. Bir sorunu saptadığınızda ve yukarıdaki 1. ve 2. adımları tamamladıktan sonra uygulama deneyimlerinizin ne kadar süre açık olacağını gösterir.


## <a name="considerations"></a>Dikkat edilmesi gerekenler

Bu bölümde çoğaltma oku özelliği hakkında dikkat edilecek noktalar özetlenmektedir.

### <a name="prerequisites"></a>Önkoşullar
Bir okuma çoğaltması oluşturmadan önce, `azure.replication_support` parametresi ana sunucuda **çoğaltma** olarak ayarlanmalıdır. Bu parametre değiştirildiğinde, değişikliğin etkili olması için sunucu yeniden başlatması gerekir. `azure.replication_support` parametresi yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş katmanlara uygulanır.

### <a name="new-replicas"></a>Yeni çoğaltmalar
Bir okuma çoğaltması, PostgreSQL için yeni bir Azure veritabanı sunucusu olarak oluşturulur. Var olan bir sunucu bir çoğaltmaya yapılamaz. Başka bir okuma çoğaltmasının çoğaltmasını oluşturamazsınız.

### <a name="replica-configuration"></a>Çoğaltma yapılandırması
Bir çoğaltma, ana öğe ile aynı işlem ve depolama ayarları kullanılarak oluşturulur. Bir çoğaltma oluşturulduktan sonra, birden fazla ayar ana sunucudan bağımsız olarak değiştirilebilir: işlem oluşturma, sanal çekirdek, depolama ve yedekleme saklama süresi. Fiyatlandırma Katmanı, temel katmandan veya dışında bağımsız olarak da değiştirilebilir.

> [!IMPORTANT]
> Ana ayar yeni bir değere güncellenmadan önce, çoğaltma yapılandırmasını eşit veya daha büyük bir değere güncelleştirin. Bu eylem, çoğaltmanın ana kopya üzerinde yapılan değişiklikleri yansıtmasını sağlar.

PostgreSQL, okuma çoğaltmasında `max_connections` parametresinin değerini ana değerden büyük veya ona eşit olacak şekilde gerektirir; Aksi takdirde, çoğaltma başlatılmaz. PostgreSQL için Azure veritabanı 'nda `max_connections` parametresi değeri SKU 'YU temel alır. Daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı 'Nda sınırlamalar](concepts-limits.md). 

Yukarıda açıklanan sunucu değerlerini güncelleştirmeye çalışırsanız, ancak sınırlara bağlı kalmazsanız bir hata alırsınız.

Çoğaltma oluşturulduğunda veya daha sonra güvenlik duvarı kuralları, sanal ağ kuralları ve parametre ayarları ana sunucudan çoğaltmaya devralınmaz.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) , okuma çoğaltmasında `max_prepared_transactions` parametresinin değerini ana değerden büyük veya ona eşit olacak şekilde gerektirir; Aksi takdirde, çoğaltma başlatılmaz. Ana `max_prepared_transactions` değiştirmek istiyorsanız, önce çoğaltmalarda değiştirin.

### <a name="stopped-replicas"></a>Durdurulan çoğaltmalar
Bir ana sunucu ve bir okuma çoğaltması arasında çoğaltmayı durdurursanız, çoğaltma değişikliği uygulamak için yeniden başlatılır. Durdurulan çoğaltma, hem okuma hem de yazma işlemlerini kabul eden tek başına bir sunucu haline gelir. Tek başına sunucu tekrar bir çoğaltmaya yapılamaz.

### <a name="deleted-master-and-standalone-servers"></a>Ana ve tek başına sunucular silindi
Bir ana sunucu silindiğinde, tüm okuma çoğaltmaları tek başına sunucular haline gelir. Çoğaltmalar bu değişikliği yansıtacak şekilde yeniden başlatılır.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Portal okuma çoğaltmaları oluşturmayı ve yönetmeyi](howto-read-replicas-portal.md)öğrenin.
* [Azure CLI ve REST API okuma çoğaltmaları oluşturma ve yönetme](howto-read-replicas-cli.md)hakkında bilgi edinin.
