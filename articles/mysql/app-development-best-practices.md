---
title: Uygulama geliştirme en iyi uygulamaları-MySQL için Azure veritabanı
description: MySQL için Azure veritabanı 'nı kullanarak uygulama oluşturmaya yönelik en iyi yöntemler hakkında bilgi edinin.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 7f98e14c2eae133941f3fc87247a427198ebcdc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102611972"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-mysql"></a>MySQL için Azure Veritabanı ile uygulama oluşturmaya yönelik en iyi yöntemler 

MySQL için Azure veritabanı 'nı kullanarak buluta hazır bir uygulama oluşturmanıza yardımcı olacak bazı en iyi yöntemler aşağıda verilmiştir. Bu en iyi uygulamalar, uygulamanız için geliştirme süresini azaltabilir. 

## <a name="configuration-of-application-and-database-resources"></a>Uygulama ve veritabanı kaynaklarının yapılandırması

### <a name="keep-the-application-and-database-in-the-same-region"></a>Uygulamayı ve veritabanını aynı bölgede tut
Uygulamanızı Azure 'da dağıttığınızda tüm bağımlılıklarınızın aynı bölgede bulunduğundan emin olun. Bölgeler veya kullanılabilirlik alanları arasında örnekleri yayma, uygulamanızın genel performansını etkileyebilecek ağ gecikmesi oluşturur. 

### <a name="keep-your-mysql-server-secure"></a>MySQL sunucunuzu güvende tutun
MySQL sunucunuzu [güvenli](./concepts-security.md) ve erişilebilir değil herkese açık olacak şekilde yapılandırın. Sunucunuzun güvenliğini sağlamak için şu seçeneklerden birini kullanın: 
- [Güvenlik duvarı kuralları](./concepts-firewall-rules.md)
- [Sanal ağlar](./concepts-data-access-and-security-vnet.md) 
- [Azure Özel Bağlantı](./concepts-data-access-security-private-link.md)

Güvenlik için, her zaman SSL üzerinden MySQL sunucunuza bağlanmanız ve MySQL sunucunuzu ve uygulamanızı TLS 1,2 kullanacak şekilde yapılandırmanız gerekir. Bkz. [SSL/TLS Yapılandırma](./concepts-ssl-connection-security.md). 

### <a name="tune-your-server-parameters"></a>Sunucu parametrelerinizi ayarlayın
Okuma ağır iş yükleri ayarlama sunucu parametreleri için `tmp_table_size` ve `max_heap_table_size` daha iyi performans için iyileştirilmesine yardımcı olabilir. Bu değişkenler için gereken değerleri hesaplamak için, toplam bağlantı başına bellek değerlerine ve temel belleğe bakın. `tmp_table_size`Sunucudaki toplam bellek için temel bellek hesaplarıyla birlikte, hariç olmak üzere, bağlantı başına bellek parametrelerinin toplamı.

En büyük olası boyutunu hesaplamak için `tmp_table_size` `max_heap_table_size` aşağıdaki formülü kullanın:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> Toplam bellek, sunucunun sağlanan sanal çekirdekler üzerinde sahip olduğu toplam bellek miktarını gösterir.  Örneğin, MySQL sunucusu için Genel Amaçlı iki sanal çekirdek Azure veritabanı 'nda toplam bellek 5 GB * 2 olacaktır. [Fiyatlandırma katmanı](./concepts-pricing-tiers.md) belgelerindeki her bir katman için bellek hakkında daha fazla ayrıntı bulabilirsiniz.
>
> Temel bellek, ve gibi, `query_cache_size` `innodb_buffer_pool_size` MySQL 'in sunucu başlangıcında başlatılacağı ve ayıracağını belirten bellek değişkenlerini belirtir. Ve gibi bağlantı başına bellek, `sort_buffer_size` `join_buffer_size` yalnızca bir sorgu için ihtiyaç duyduğunda ayrılan bellektir.

### <a name="create-non-admin-users"></a>Yönetici olmayan kullanıcılar oluşturma 
Her veritabanı için [yönetici olmayan kullanıcılar oluşturun](./howto-create-users.md) . Genellikle, Kullanıcı adları veritabanı adları olarak tanımlanır.

### <a name="reset-your-password"></a>Parolanızı sıfırlama
Azure portal kullanarak MySQL sunucunuzun [parolasını sıfırlayabilirsiniz](./howto-create-manage-server-portal.md#update-admin-password) . 

Bir üretim veritabanı için sunucu parolanızı sıfırlama, uygulamanızı getirebilir. Uygulamanızın kullanıcılarına etkisini en aza indirmek için, tüm üretim iş yükleri için parolayı yoğun olmayan saatlerde sıfırlamak iyi bir uygulamadır.

## <a name="performance-and-resiliency"></a>Performans ve dayanıklılık 
İşte performans sorunlarını ayıklamanıza yardımcı olmak için kullanabileceğiniz birkaç araç ve uygulama aşağıda verilmiştir.

### <a name="enable-slow-query-logs-to-identify-performance-issues"></a>Performans sorunlarını belirlemek için yavaş sorgu günlüklerini etkinleştir
Sunucunuzdaki [yavaş sorgu günlüklerini](./concepts-server-logs.md) ve [Denetim günlüklerini](./concepts-audit-logs.md) etkinleştirebilirsiniz. Yavaş sorgu günlüklerinin çözümlenmesi, sorun giderme için performans sorunlarını belirlemenize yardımcı olabilir. 

Denetim günlükleri, Azure Izleyici günlükleri, Azure Event Hubs ve depolama hesaplarında Azure Tanılama Günlükler aracılığıyla da kullanılabilir. Bkz. [sorgu performans sorunlarını giderme](./howto-troubleshoot-query-performance.md).

### <a name="use-connection-pooling"></a>Bağlantı havuzu kullanma
Veritabanı bağlantılarını yönetmek, uygulamanın performansı üzerinde önemli bir etkiye sahip olabilir. Performansı iyileştirmek için, bağlantıların kaç kez kurulabileceğinizi ve anahtar kod yollarında bağlantı kurma süresini azaltmanız gerekir. Esneklik ve performansı geliştirmek için MySQL için Azure veritabanı 'na bağlanmak üzere [bağlantı havuzunu](./concepts-connectivity.md#access-databases-by-using-connection-pooling-recommended) kullanın. 

Bağlantıları etkin bir şekilde yönetmek için [Proxysql](https://proxysql.com/) bağlantı havuzlayıcı ' i kullanabilirsiniz. Bir bağlantı havuzlayıcı kullanılması, boştaki bağlantıları azaltabilir ve var olan bağlantıları yeniden kullanabilir ve bu da sorunları önlemeye yardımcı olur. Daha fazla bilgi için bkz. [ProxySQL 'i ayarlama](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) . 

### <a name="retry-logic-to-handle-transient-errors"></a>Geçici hataları işlemek için yeniden deneme mantığı
Uygulamanız, veritabanı bağlantılarının aralıklı olarak bırakılmakta veya kaybolduğu [geçici hatalarla](./concepts-connectivity.md#handling-transient-errors) karşılaşabilir. Bu gibi durumlarda, sunucu, 5 ila 10 saniye içinde bir ila iki yeniden denemeden sonra çalışır. 

İlk yeniden denemeden önce 5 saniye beklemek iyi bir uygulamadır. Sonra, 60 saniyeye kadar yavaş bekleme süresini artırarak her yeniden denemeye uyun. Uygulamanızın işlemi kabul eden en fazla yeniden deneme sayısını sınırlayın, böylece daha fazla araştırma yapabilirsiniz. Daha fazla bilgi için bkz. [bağlantı hatalarıyla ilgili sorunları giderme](./howto-troubleshoot-common-connection-issues.md) . 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Yük devretmeleri azaltmak için okuma çoğaltmasını etkinleştirin
Yük devretme senaryolarında [gelen verileri çoğaltma](./howto-data-in-replication.md) kullanabilirsiniz. Okuma çoğaltmaları kullanırken, kaynak ve çoğaltma sunucuları arasında otomatik yük devretme gerçekleşmez. 

Çoğaltma zaman uyumsuz olduğundan kaynak ve çoğaltma arasında bir gecikme fark edeceksiniz. Ağ gecikmesi, kaynak sunucuda çalışan iş yükünün boyutu ve veri merkezleri arasındaki gecikme süresi gibi birçok faktörden etkilenebilir. Çoğu durumda, çoğaltma gecikme süresi birkaç saniyeden birkaç dakikaya kadar değişir.

## <a name="database-deployment"></a>Veritabanı dağıtımı 

### <a name="configure-an-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>CI/CD dağıtım işlem hattınızda MySQL için Azure veritabanı görevini yapılandırma
Bazen değişiklikleri veritabanınıza dağıtmanız gerekir. Bu gibi durumlarda, [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) aracılığıyla sürekli TÜMLEŞTIRME (CI) ve sürekli teslım (CD) kullanabilir ve bir özel betik çalıştırarak veritabanını güncelleştirmek için [MySQL sunucunuzun](/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment) bir görevini kullanabilirsiniz.

### <a name="use-an-effective-process-for-manual-database-deployment"></a>El ile veritabanı dağıtımı için etkili bir işlem kullanın 
El ile veritabanı dağıtımı sırasında, kapalı kalma süresini en aza indirmek veya başarısız dağıtım riskini azaltmak için aşağıdaki adımları izleyin: 

1. [Mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) veya [MySQL çalışma ekranı](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html)kullanarak yeni bir veritabanında üretim veritabanının bir kopyasını oluşturun. 
2. Yeni veritabanını yeni şema değişiklikleri veya veritabanınız için gereken güncelleştirmeler ile güncelleştirin. 
3. Üretim veritabanını salt bir okuma durumuna getirin. Dağıtım tamamlanana kadar üretim veritabanında yazma işlemlerine sahip olmanız gerekir. 
4. 1. adımdaki yeni güncelleştirilmiş veritabanıyla uygulamanızı test edin.
5. Uygulama değişikliklerinizi dağıtın ve uygulamanın Şu anda en son güncelleştirmelere sahip olan yeni veritabanını kullanmakta olduğundan emin olun. 
6. Değişiklikleri geri almak için eski üretim veritabanını koruyun. Daha sonra, eski üretim veritabanını silmeyi ya da gerekirse Azure Storage 'a dışarı aktarmayı değerlendirebilirsiniz. 

>[!NOTE]
>Uygulama bir e-ticaret uygulaması gibi olduğunda ve salt okuma durumunda yer alıyorsa, bir yedekleme yaptıktan sonra değişiklikleri doğrudan üretim veritabanına dağıtın. Bazı kullanıcılar başarısız isteklerle karşılaşabileceğinden, etkiyi en aza indirmek için uygulamanın düşük trafiğiyle yoğun olmayan saatlerde gerçekleşmelidir. 
>
>Uygulama kodunuzun başarısız istekleri de işlediğinizden emin olun.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>İş yükünüzün bellek içi geçici tablo boyutlarını aşıp aşmadığından bakmak için MySQL yerel ölçümlerini kullanın
Okuma ağır iş yüküyle, MySQL sunucunuza karşı çalışan sorgular bellek içi geçici tablo boyutlarını aşabilir. Okuma ağır iş yükü, sunucunuzun, uygulamanızın performansını etkileyen geçici tabloları diske yazma anahtarına geçmesine neden olabilir. Sunucunuzun geçici tablo boyutunu aşarak diske yazmadığını öğrenmek için aşağıdaki ölçümlere bakın:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
`created_tmp_disk_tables`Ölçüm diskte kaç tablo oluşturulduğunu gösterir. `created_tmp_table`Ölçüm, iş yükünüz verildiğinde, bellekte kaç tane geçici tablonun biçimlendirilmiş olduğunu söyler. Belirli bir sorgu çalıştırmanın geçici tablolar kullanıp kullanmadığını anlamak için, sorgu üzerinde [açıkla](https://dev.mysql.com/doc/refman/8.0/en/explain.html) ifadesini çalıştırın. Sütunundaki ayrıntı, `extra` `Using temporary` sorgunun geçici tablolar kullanılarak çalışacağını gösterir.

Sorgular disklere atıştırdığı iş yükünüzün yüzdesini hesaplamak için aşağıdaki formüldeki ölçüm değerlerinizi kullanın:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

İdeal olarak, bu yüzde 25 ' in az olması gerekir. Yüzdenin %25 veya daha büyük olduğunu görürseniz, tmp_table_size ve max_heap_table_size iki sunucu parametresini değiştirmenizi öneririz.

## <a name="database-schema-and-queries"></a>Veritabanı şeması ve sorguları

Veritabanı şemanızı ve sorgularınızı oluştururken aklınızda bulundurmanız gereken birkaç ipucu aşağıda verilmiştir.

### <a name="use-the-right-datatype-for-your-table-columns"></a>Tablo sütunlarınız için doğru veri türünü kullanın
Depolamak istediğiniz verilerin türüne göre doğru veri türünün kullanılması, depolamayı iyileştirebilirler ve hatalı veri türleri nedeniyle oluşabilecek hataları azaltabilir.

### <a name="use-indexes"></a>Dizinleri kullanma
Yavaş sorgulardan kaçınmak için dizinleri kullanabilirsiniz. Dizinler, belirli sütunlara hızlıca sahip satırları bulmaya yardımcı olabilir. Bkz. [MySQL 'de dizinleri kullanma](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="use-explain-for-your-select-queries"></a>Seçme sorgularınız için AÇıKLA kullanın
`EXPLAIN`Sorgunuzu çalıştırmak Için MySQL ile ilgili Öngörüler elde etmek için, ifadesini kullanın. Sorgunuzun performans sorunlarını veya sorunları tespit etmenize yardımcı olabilir. Bkz. [Açıklama kullanarak sorgu performansını profil](./howto-troubleshoot-query-performance.md)oluşturma.