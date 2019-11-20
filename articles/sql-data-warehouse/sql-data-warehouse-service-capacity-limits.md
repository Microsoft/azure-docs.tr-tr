---
title: Kapasite sınırları-Azure SYNAPSE Analytics (eski adıyla SQL DW)
description: Azure SYNAPSE 'de çeşitli SQL Analytics bileşenleri için izin verilen en yüksek değer.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: e3661797ea408f219a67a1862901fee7c27a1d58
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123909"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-capacity-limits"></a>Azure SYNAPSE Analytics (eski adıyla SQL DW) kapasite sınırları

Çeşitli Azure SYNAPSE bileşenleri için izin verilen en yüksek değer.

## <a name="workload-management"></a>İş yükü yönetimi

| Kategori | Açıklama | Maksimum |
|:--- |:--- |:--- |
| [Veri ambarı birimleri (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Tek bir SQL Havuzu (veri ambarı) birimi için maksimum DWU | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Veri ambarı birimleri (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Sunucu başına varsayılan DTU |54,000<br></br>Varsayılan olarak, her SQL Server (örneğin, myserver.database.windows.net), DW5000c 'e kadar izin veren bir DTU kotasına sahiptir 54.000. Bu kota yalnızca bir güvenlik sınırıdır. [Bir destek bileti oluşturarak](sql-data-warehouse-get-started-create-support-ticket.md) ve istek türü olarak *Kota* ' yı seçerek kotanızı artırabilirsiniz.  DTU gereksinimlerinizi hesaplamak için gereken toplam DWU ile 7,5 ' i çarpıp, gereken toplam cDWU ile 9,5 ' i çarpın. Örneğin:<br></br>DW6000 x 7,5 = 45.000 DTU<br></br>DW5000c x 9,5 = 47.500 DTU.<br></br>Geçerli DTU tüketiminizi, portalda SQL Server seçeneğinden görüntüleyebilirsiniz. DTU kotasında hem duraklatılmış hem de duraklatılmamış veritabanları sayılır. |
| Veritabanı bağlantısı |Maksimum eş zamanlı açık oturum |1024<br/><br/>Eş zamanlı açık oturumların sayısı, seçilen DWU 'ya göre değişiklik gösterecektir. DWU600c ve üzeri, en fazla 1024 açık oturumu destekler. DWU500c ve altı, en fazla eşzamanlı açık oturum sınırı olan 512 ' i destekler. Aynı anda yürütebileceğini sorgu sayısı için sınırlamalar olduğunu unutmayın. Eşzamanlılık sınırı aşıldığında, istek işlenmek üzere beklediği bir iç sıraya gider. |
| Veritabanı bağlantısı |Hazırlanan deyimler için maksimum bellek |20 MB |
| [İş yükü yönetimi](resource-classes-for-workload-management.md) |En fazla eşzamanlı sorgu |128<br/><br/>  En fazla 128 eşzamanlı sorgu yürütülecektir ve kalan sorgular sıraya alınacaktır.<br/><br/>Kullanıcılar daha yüksek kaynak sınıflarına atandığında veya [veri ambarı birimi](memory-concurrency-limits.md) ayarı düşürülen zaman eşzamanlı sorguların sayısı azalabilir. DMV sorguları gibi bazı sorguların çalışmasına her zaman izin verilir ve eşzamanlı sorgu sınırını etkilemez. Eşzamanlı sorgu yürütme hakkında daha fazla bilgi için bkz. [eşzamanlılık üst sınırları](memory-concurrency-limits.md) makalesi. |
| ['nin](sql-data-warehouse-tables-temporary.md) |En fazla GB |DW100 başına 399 GB. Bu nedenle, DWU1000 adresinde tempdb 3,99 TB olarak boyutlandırılır. |

## <a name="database-objects"></a>Veritabanı nesneleri
| Kategori | Açıklama | Maksimum |
|:--- |:--- |:--- |
| Veritabanı |En büyük boyut | Gen1:240 TB diskte sıkıştırılmış. Bu alan tempdb veya günlük alanından bağımsızdır ve bu nedenle bu alan kalıcı tablolara ayrılmıştır.  Kümelenmiş columnstore sıkıştırması, 5X ile tahmin edilir.  Bu sıkıştırma, tüm tablolar kümelenmiş columnstore (varsayılan tablo türü) olduğunda veritabanının yaklaşık 1 PB 'e büyümesine izin verir. <br/><br/> Gen2: rowstore için 240TB ve columnstore tabloları için sınırsız depolama |
| Tablo |En büyük boyut |disk üzerinde 60 TB sıkıştırılmış |
| Tablo |Veritabanı başına tablo | 100.000 |
| Tablo |Tablo başına sütun |1024 sütun |
| Tablo |Sütun başına bayt |Sütun [veri türüne](sql-data-warehouse-tables-data-types.md)bağımlıdır. Sınır, karakter veri türleri için 8000, nvarchar için 4000 veya en fazla veri türü için 2 GB 'dir. |
| Tablo |Satır başına bayt, tanımlı boyut |8060 bayt<br/><br/>Satır başına bayt sayısı, sayfa sıkıştırması ile SQL Server için olduğu şekilde hesaplanır. SQL Server gibi, satır taşma alanı desteklenir ve bu da **değişken uzunluklu sütunların** satır dışı gönderilmesine olanak sağlar. Değişken uzunluk satırları satır dışı gönderildiğinde, ana kayıtta yalnızca 24 baytlık kök depolanır. Daha fazla bilgi için bkz. [8 kb aşan satır taşma verileri](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tablo |Tablo başına bölüm sayısı |15.000<br/><br/>Yüksek performans için, hala iş gereksinimlerinizi desteklerken ihtiyacınız olan bölüm sayısını en aza indirmenizi öneririz. Bölüm sayısı arttıkça, veri tanımlama dili (DDL) ve veri Işleme dili (DML) işlemleri için ek yükün büyümesi ve performansının yavaşlamasına neden olur. |
| Tablo |Bölüm sınırı değeri başına karakter. |4000 |
| Dizin oluşturma |Tablo başına kümelenmemiş dizinler. |50<br/><br/>Yalnızca rowstore tabloları için geçerlidir. |
| Dizin oluşturma |Tablo başına kümelenmiş dizinler. |1<br><br/>Hem rowstore hem de columnstore tabloları için geçerlidir. |
| Dizin oluşturma |Dizin anahtar boyutu. |900 bayt.<br/><br/>Yalnızca rowstore dizinleri için geçerlidir.<br/><br/>Sütundaki mevcut veriler, Dizin oluşturulduğunda 900 baytı aşmazsa, varchar sütunlarındaki en fazla 900 bayttan daha fazla boyutu olan dizinler oluşturulabilir. Ancak, daha sonra toplam boyutun 900 baytı aşmasına neden olan sütunlara ekleme veya GÜNCELLEŞTIRME eylemleri başarısız olur. |
| Dizin oluşturma |Dizin başına anahtar sütun sayısı. |16<br/><br/>Yalnızca rowstore dizinleri için geçerlidir. Kümelenmiş columnstore dizinleri tüm sütunları içerir. |
| İstatistikler |Birleşik sütun değerlerinin boyutu. |900 bayt. |
| İstatistikler |İstatistik nesnesi başına sütun. |32 |
| İstatistikler |Tablo başına sütunlarda oluşturulan istatistikler. |30,000 |
| Saklı Yordamlar |En fazla iç içe geçme düzeyi. |8 |
| Görünüm |Görünüm başına sütun |1,024 |

## <a name="loads"></a>Sayfam
| Kategori | Açıklama | Maksimum |
|:--- |:--- |:--- |
| PolyBase yükleri |Satır başına MB |1<br/><br/>PolyBase, 1 MB 'tan küçük satırları yükler. LOB veri türlerini kümelenmiş bir columnstore dizini (CCı) olan tablolara yüklemek desteklenmez.<br/><br/> |

## <a name="queries"></a>Sorgular
| Kategori | Açıklama | Maksimum |
|:--- |:--- |:--- |
| Sorgu |Kullanıcı tablolarında sıraya alınmış sorgular. |1000 |
| Sorgu |Sistem görünümlerinde eşzamanlı sorgular. |100 |
| Sorgu |Sistem görünümlerinde sıraya alınmış sorgular |1000 |
| Sorgu |En fazla parametre |2098 |
| Batch |En büyük boyut |65,536*4096 |
| Sonuçları SEÇIN |Satır başına sütun sayısı |4096<br/><br/>SEÇIM sonucunda satır başına 4096 ' den fazla sütuna sahip olabilirsiniz. Her zaman 4096 sahip olabilirsiniz garantisi yoktur. Sorgu planı geçici bir tablo gerektiriyorsa, tablo başına en fazla 1024 sütun uygulanabilir. |
| SELECT |İç içe geçmiş alt sorgular |32<br/><br/>SELECT ifadesinde en fazla 32 iç içe geçmiş alt sorgu olamaz. Her zaman 32 sahip olabilirsiniz garantisi yoktur. Örneğin, bir JOIN sorgu planına bir alt sorgu getirebilir. Alt sorgu sayısı da kullanılabilir bellek ile sınırlandırılabilir. |
| SELECT |JOIN başına sütun sayısı |1024 sütun<br/><br/>BIRLEŞIMDE hiç 1024 sütundan fazlasına sahip olabilirsiniz. Her zaman 1024 sahip olabilirsiniz garantisi yoktur. JOIN planı, JOIN sonucundan daha fazla sütun içeren geçici bir tablo gerektiriyorsa, 1024 sınırı geçici tablo için geçerlidir. |
| SELECT |Grup başına sütunlara göre bayt. |8060<br/><br/>GROUP BY yan tümcesindeki sütunlarda en fazla 8060 bayt olabilir. |
| SELECT |Sütunlara göre SıRALAMA başına bayt sayısı |8060 bayt<br/><br/>ORDER BY yan tümcesindeki sütunlarda en fazla 8060 bayt olabilir |
| İfade başına tanımlayıcılar |Başvurulan tanımlayıcıların sayısı |65,535<br/><br/> Sorgunun tek bir ifadesinde bulunabilecek tanımlayıcıların sayısı sınırlıdır. Bu sayının aşılması SQL Server hatası 8632 ile sonuçlanır. Daha fazla bilgi için bkz. [iç hata: bir ifade Hizmetleri sınırına ulaşıldı](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Dize sabit değerleri | Deyimdeki dize sabit değerlerinin sayısı | 20,000 <br/><br/>Bir sorgunun tek bir ifadesindeki dize sabitleri sayısı sınırlıdır. Bu sayının aşılması SQL Server hatası 8632 ile sonuçlanır.|

## <a name="metadata"></a>Meta Veriler
| Sistem görünümü | En fazla satır |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10,000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |En son 1000 SQL isteği için DMS çalışanlarının toplam sayısı. |
| sys.dm_pdw_errors |10,000 |
| sys.dm_pdw_exec_requests |10,000 |
| sys.dm_pdw_exec_sessions |10,000 |
| sys.dm_pdw_request_steps |Sys. dm_pdw_exec_requests içinde depolanan en son 1000 SQL isteği için toplam adım sayısı. |
| sys.dm_pdw_os_event_logs |10,000 |
| sys.dm_pdw_sql_requests |Sys. dm_pdw_exec_requests içinde depolanan en son 1000 SQL isteği. |

## <a name="next-steps"></a>Sonraki adımlar
Azure SYNAPSE kullanımı ile ilgili öneriler için, bkz. bilgi [sayfası](cheat-sheet.md).
