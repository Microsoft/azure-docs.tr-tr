---
title: Sys_schema kullanarak, performansı ayarlama ve MySQL için Azure veritabanı 'nı sürdürme
description: Sys_schema kullanarak, performans sorunlarını nasıl bulacağınızı ve MySQL için Azure veritabanı 'nda veritabanını nasıl koruyacağınızı öğrenin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: 7dc6b4744c74c56803127f63a8a6f29ca5a15090
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972785"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'nda performans ayarlama ve veritabanı bakımı için sys_schema kullanma

MySQL 5,5 ' de ilk olarak bulunan MySQL performance_schema, bellek ayırma, saklı programlar, meta veri kilitleme gibi birçok önemli sunucu kaynağı için izleme sağlar. Ancak, performance_schema 80 'den fazla tablo içerir ve gerekli bilgilerin alınması genellikle performance_schema içindeki tabloların yanı sıra information_schema içindeki tabloların katılmasını gerektirir. Hem performance_schema hem de information_schema üzerinde derleme yapmak için sys_schema, salt okunur bir veritabanında kolay bir şekilde [Kullanıcı dostu görünümler](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) koleksiyonu sağlar ve MySQL Için Azure veritabanı sürüm 5,7 ' de tam olarak etkinleştirilmiştir.

![sys_schema görünümleri](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

Sys_schema 'de 52 görünüm vardır ve her görünüm aşağıdaki öneklerden birine sahiptir:

- Host_summary veya GÇ: g/ç ile ilgili gecikme süreleri.
- InnoDB: InnoDB buffer durum ve kilitler.
- Bellek: konak ve kullanıcılar tarafından bellek kullanımı.
- Şema: otomatik artış, dizinler vb. gibi şemaya ilişkin bilgiler
- Deyim: SQL deyimleriyle ilgili bilgiler; Bu, tam tablo taramasına veya uzun sorgu zamanına neden olan bir ifade olabilir.
- Kullanıcı: kullanıcılara göre tüketilen ve gruplandırılan kaynaklar. Dosya g/ç, bağlantı ve bellek örnekleri örnektir.
- Wait: bekleme olayları ana bilgisayara veya kullanıcıya göre gruplandırılır.

Şimdi sys_schema 'in bazı yaygın kullanım düzenlerine göz atalım. Kullanmaya başlamak için kullanım desenlerini iki kategoride gruplarız: **performans ayarlama** ve **Veritabanı Bakımı**.

## <a name="performance-tuning"></a>Performans ayarı

### <a name="sysuser_summary_by_file_io"></a>*sys. user_summary_by_file_io*

GÇ, veritabanında en pahalı bir işlemdir. *Sys. user_summary_by_file_io* görünümünü SORGULAYARAK ortalama GÇ gecikme süresini bulabiliriz. Varsayılan 125 GB sağlanmış depolama alanı ile GÇ gecikme süresi yaklaşık 15 saniyedir.

![GÇ gecikmesi: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

MySQL için Azure veritabanı, depolama 'ya göre GÇ 'yi ölçeklendirdikten sonra, sağlanan depolama alanını 1 TB olarak artırdıktan sonra, GÇ gecikmesi 571 MS olarak azalır.

![GÇ gecikmesi: 1TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschema_tables_with_full_table_scans"></a>*sys. schema_tables_with_full_table_scans*

Dikkatli bir planlamaya rağmen çok sayıda sorgu yine de tam tablo taramasına neden olabilir. Dizinlerin türleri ve bunların nasıl iyileştirileceği hakkında ek bilgiler için, bu makaleye başvurabilirsiniz: [sorgu performansı sorunlarını giderme](./howto-troubleshoot-query-performance.md). Tam tablo taramaları Kaynak yoğunluklu ve veritabanı performanslarını düşürür. Tam tablo taraması olan tabloları bulmanın en hızlı yolu, *sys. schema_tables_with_full_table_scans* görünümünü sorgulamanızı sağlar.

![tam tablo taramaları](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysuser_summary_by_statement_type"></a>*sys. user_summary_by_statement_type*

Veritabanı performans sorunlarını gidermek için, veritabanınızın içinde gerçekleşen olayları belirlemek yararlı olabilir ve *sys. user_summary_by_statement_type* görünümünün kullanılması yalnızca eli olabilir.

![deyime göre Özet](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

Bu örnekte, MySQL için Azure veritabanı, slog sorgu günlüğü 44579 kez temizlenme 53 dakika harcamıştır. Bu uzun süredir ve çok sayıda IOs. Yavaş sorgu günlüğliğinizi devre dışı bırakarak veya yavaş sorgu oturum açma Azure portal sıklığını azaltabilmeniz için bu etkinliği azaltabilirsiniz.

## <a name="database-maintenance"></a>Veritabanı Bakımı

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys. innodb_buffer_stats_by_table*

InnoDB arabellek havuzu bellekte bulunur ve DBMS ile depolama arasındaki ana önbellek mekanizmasıdır. InnoDB arabellek havuzunun boyutu performans katmanına bağlıdır ve farklı bir Ürün SKU 'SU seçilmediği takdirde değiştirilemez. İşletim Sisteminizdeki bellekte olduğu gibi, fresher verileri için yer açmak üzere eski sayfalar takas edilir. En fazla InnoDB arabellek havuzu belleği tükettiği tabloları bulmak için *sys. innodb_buffer_stats_by_table* görünümünü sorgulayabilirsiniz.

![InnoDB arabellek durumu](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

Yukarıdaki grafikte, sistem tabloları ve görünümleri dışında, WordPress Sitelerimin birini barındıran mysqldatabase033 veritabanındaki her tablo, bellekteki verilerin 16 KB veya 1 sayfa üzerinde kapladığı şekilde görünür.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys. schema_unused_indexes* & *sys. schema_redundant_indexes*

Dizinler, okuma performansını artırmaya yönelik harika araçlardır, ancak eklemeler ve depolama için ek maliyetler doğurur. *Sys. schema_unused_indexes* ve *sys. schema_redundant_indexes* , kullanılmayan veya yinelenen dizinler hakkında öngörüler sağlar.

![kullanılmayan dizinler](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![Yedekli dizinler](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Sonuç

Özet olarak sys_schema, hem performans ayarlaması hem de veritabanı bakımı için harika bir araçtır. MySQL için Azure veritabanı 'nda bu özellikten faydalantığınızdan emin olun. 

## <a name="next-steps"></a>Sonraki adımlar
- En çok ilgili sorularınızın eş yanıtlarını bulmak veya yeni bir soru/cevap göndermek için [MSDN Forumu](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) veya [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql)sitesini ziyaret edin.
