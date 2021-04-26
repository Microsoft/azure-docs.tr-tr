---
title: Adanmış SQL havuzunda sorun giderme (eski adıyla SQL DW)
description: Azure SYNAPSE Analytics 'te adanmış SQL Havuzu (eski adıyla SQL DW) sorunlarını giderme.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: af653585ec1b57b5fd697dc755e495a96e04e677
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565415"
---
# <a name="troubleshooting-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL Havuzu (eski adıyla SQL DW) sorunlarını giderme

Bu makalede, Azure SYNAPSE Analytics 'te adanmış SQL havuzunda (eski adıyla SQL DW) genel sorun giderme sorunları listelenmektedir.

## <a name="connecting"></a>Bağlanmada

| Sorun                                                        | Çözüm                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 'NT AUTHORITY\ANONYMOUS LOGON' kullanıcısı için oturum açma başarısız oldu. (Microsoft SQL Server, hata: 18456) | Bu hata, bir Azure AD kullanıcısı ana veritabanına bağlanmaya çalıştığında ancak ana veritabanında bir kullanıcı olmadığında oluşur.  Bu sorunu düzeltmek için bağlantı zamanında bağlanmak istediğiniz adanmış SQL havuzunu (eski adıyla SQL DW) belirtin ya da kullanıcıyı ana veritabanına ekleyin.  Daha fazla bilgi için [güvenlik genel bakış](sql-data-warehouse-overview-manage-security.md) makalesine bakın. |
| Sunucu sorumlusu "MyUserName" geçerli güvenlik bağlamında "asıl" veritabanına erişemiyor. Kullanıcının varsayılan veritabanı açılamıyor. Oturum açılamadı. 'MyUserName' kullanıcısı için oturum açma başarısız oldu. (Microsoft SQL Server, hata: 916) | Bu hata, bir Azure AD kullanıcısı ana veritabanına bağlanmaya çalıştığında ancak ana veritabanında bir kullanıcı olmadığında oluşur.  Bu sorunu düzeltmek için bağlantı zamanında bağlanmak istediğiniz adanmış SQL havuzunu (eski adıyla SQL DW) belirtin ya da kullanıcıyı ana veritabanına ekleyin.  Daha fazla bilgi için [güvenlik genel bakış](sql-data-warehouse-overview-manage-security.md) makalesine bakın. |
| CTAıP hatası                                                  | Bu hata, SQL veritabanı ana veritabanında bir oturum oluşturulduğunda, ancak belirli SQL veritabanında olmadığında ortaya çıkabilir.  Bu hatayla karşılaşırsanız, [güvenlik genel bakış](sql-data-warehouse-overview-manage-security.md) makalesine göz atın.  Bu makalede, ana veritabanında bir oturum açma ve Kullanıcı oluşturma ve ardından bir SQL veritabanında Kullanıcı oluşturma işlemleri açıklanmaktadır. |
| Güvenlik Duvarı Tarafından Engellendi                                          | Adanmış SQL Havuzu (eski adıyla SQL DW), yalnızca bilinen IP adreslerinin bir veritabanına erişimi olduğundan emin olmak için güvenlik duvarları tarafından korunur. Güvenlik duvarları varsayılan olarak güvenli hale gelir. Bu, bağlanmadan önce açıkça etkinleştirmeniz ve IP adresi veya adres aralığı yapmanız gerekir.  Güvenlik duvarınızı erişim için yapılandırmak üzere, [sağlama yönergelerindeki](create-data-warehouse-portal.md) [istemci IP 'niz için sunucu güvenlik duvarı erişimini yapılandırma](create-data-warehouse-portal.md) bölümündeki adımları izleyin. |
| Araçla veya sürücüyle bağlantı yapılamaz                           | Adanmış SQL Havuzu (eski adıyla SQL DW), [SMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)'Yi, [Visual Studio için SSDT](sql-data-warehouse-install-visual-studio.md)'yi veya verilerinizi sorgulamak için [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) kullanılmasını önerir. Sürücüler hakkında daha fazla bilgi ve Azure SYNAPSE 'a bağlanma hakkında daha fazla bilgi için bkz. [Azure SYNAPSE Için sürücüler](sql-data-warehouse-connection-strings.md) ve [Azure SYNAPSE makalelerine bağlanma](sql-data-warehouse-connect-overview.md) . |

## <a name="tools"></a>Araçlar

| Sorun                                                        | Çözüm                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio Nesne Gezgini 'nde Azure AD kullanıcıları eksik           | Bu bilinen bir sorundur.  Geçici bir çözüm olarak, [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)kullanıcıları görüntüleyin.  Adanmış SQL Havuzu (eski adıyla SQL DW) ile Azure Active Directory kullanma hakkında daha fazla bilgi edinmek için bkz. [Azure SYNAPSE kimlik doğrulaması](sql-data-warehouse-authentication.md) . |
| El ile betik oluşturma, betik Sihirbazı 'nı kullanma veya SSMS aracılığıyla bağlanma yavaş, yanıt vermiyor veya hata üretmiyor | Ana veritabanında kullanıcıların oluşturulduğundan emin olun. Komut dosyası seçeneklerinde Ayrıca, Engine sürümünün "Microsoft Azure SYNAPSE Analytics Edition" olarak ayarlandığından ve altyapı türünün "Microsoft Azure SQL Veritabanı" olduğundan emin olun. |
| SSMS 'de betik oluşturma başarısız oluyor                               | "Bağımlı nesneler için betik oluştur" seçeneği "true" olarak ayarlanmışsa adanmış SQL Havuzu (eski adıyla SQL DW) için betik oluşturma işlemi başarısız olur. Geçici bir çözüm olarak, kullanıcıların **Araçlar-> seçenekler->SQL Server Nesne Gezgini-> bağımlı seçenekler için betik oluştur ve yanlış olarak ayarla** seçeneğine el ile gitmesi gerekir |

## <a name="data-ingestion-and-preparation"></a>Veri alımı ve hazırlığı

| Sorun                                                        | Çözüm                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| CETAS kullanarak boş dizeleri dışarı aktarmak, Parquet ve ORC dosyalarında NULL değerler oluşmasına neden olur. Not NULL kısıtlamaları olmayan sütunlardan boş dizeler dışarı aktarıyorsanız, CETAS reddedilen kayıtlarla sonuçlanır ve dışa aktarma potansiyel olarak başarısız olabilir. | CETAS 'ın SELECT deyimindeki boş dizeleri veya sorunlu sütunu kaldırın. |
| Parquet ve ORC dosya biçimi için 0-127 aralığının dışında bir değerin bir mini sütuna yüklenmesi desteklenmez. | Hedef sütun için daha büyük bir veri türü belirtin.           |

## <a name="performance"></a>Performans

| Sorun                                                        | Çözüm                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Sorgu performansı sorunlarını giderme                            | Belirli bir sorgu sorunlarını gidermeye çalışıyorsanız, [sorgularınızı nasıl izleyebileceğiniz hakkında bilgi edinmeye](sql-data-warehouse-manage-monitor.md#monitor-query-execution)başlayın. |
| TempDB alanı sorunları | [Tempdb](sql-data-warehouse-manage-monitor.md#monitor-tempdb) alan kullanımını izleyin.  TempDB alanını çalıştırmanın yaygın nedenleri şunlardır:<br>-Sorguya ayrılan yeterli kaynak yok, verilerin TempDB 'ye taşımasına neden olur.  Bkz. [Iş yükü yönetimi](resource-classes-for-workload-management.md) <br>-Çok fazla veri hareketine neden olan istatistikler eksik veya güncel değil.  İstatistik oluşturma hakkında ayrıntılı bilgi için bkz. [tablo Istatistiklerini koruma](sql-data-warehouse-tables-statistics.md)<br>-TempDB alanı hizmet düzeyi başına ayrılır.  [ADANMıŞ SQL havuzunuzu (eskıden SQL DW)](sql-data-warehouse-manage-compute-overview.md#scaling-compute) daha yüksek bir DWU ayarına ölçeklendirin daha fazla tempdb alanı ayırır.|
| Kötü sorgu performansı ve planları genellikle eksik istatistiklerin bir sonucudur | Düşük performansın en yaygın nedeni, tablolarınızdaki istatistiklerin olmamasıdır.  İstatistiklerin nasıl oluşturulacağı ve performanstan ne kadar önemli olduğunu gösteren Ayrıntılar için bkz. [tablo Istatistiklerini koruma](sql-data-warehouse-tables-statistics.md) . |
| Sıraya alınan düşük eşzamanlılık/sorgu sayısı                             | Bellek ayırmayı eşzamanlılık ile dengelemeye anlamak için [Iş yükü yönetimini](resource-classes-for-workload-management.md) anlamak önemlidir. |
| En iyi yöntemleri uygulama                              | Sorgu performansını artırmanın yollarını öğrenmek için başlamak için en iyi yer, [ADANMıŞ SQL Havuzu (eski ADıYLA SQL DW) en iyi yöntemler](sql-data-warehouse-best-practices.md) makaleleridir. |
| Ölçeklendirmeyle performansı artırma                      | Bazen, performansı iyileştirmeye yönelik çözüm, [Özel SQL havuzunuzu (eski ADıYLA SQL DW) ölçeklendirerek](sql-data-warehouse-manage-compute-overview.md)sorgulara daha fazla işlem gücü eklemektir. |
| Yetersiz Dizin kalitesinin sonucu olarak sorgu performansı yetersiz     | Bazı süreler sorguları, [düşük columnstore dizin kalitesi](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)nedeniyle yavaşlayabilir.  Daha fazla bilgi ve [bölüm kalitesini artırmak için dizinleri yeniden oluşturma](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)hakkında daha fazla bilgi için bu makaleye bakın. |

## <a name="system-management"></a>Sistem Yönetimi

| Sorun                                                        | Çözüm                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: sunucu, izin verilen veritabanı Işlem birimi kotasını (45000) aşacağından işlem gerçekleştirilemedi. | Oluşturmaya çalıştığınız veritabanının [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) değerini azaltın ya da [bir kota artışı isteyin](sql-data-warehouse-get-started-create-support-ticket.md). |
| Alan kullanımını araştırma                              | Sisteminizin alan kullanımını anlamak için bkz. [Tablo boyutları](sql-data-warehouse-tables-overview.md#table-size-queries) . |
| Tabloları yönetmeyle ilgili yardım                                    | Tablolarınızı yönetme hakkında yardım almak için [tabloya genel bakış](sql-data-warehouse-tables-overview.md) makalesine bakın.  Bu makale ayrıca [tablo veri türleri](sql-data-warehouse-tables-data-types.md), tablo [dağıtma](sql-data-warehouse-tables-distribute.md), tablo [dizini oluşturma](sql-data-warehouse-tables-index.md) [, tablo](sql-data-warehouse-tables-partition.md)istatistikleri ve [geçici tabloları](sql-data-warehouse-tables-temporary.md) [koruma](sql-data-warehouse-tables-statistics.md) gibi daha ayrıntılı konulara bağlantılar içerir. |
| Saydam veri şifrelemesi (TDE) ilerleme çubuğu Azure portal güncelleştirilmiyor | TDE [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)ile durumunu görüntüleyebilirsiniz. |

## <a name="differences-from-sql-database"></a>SQL veritabanı farklılıkları

| Sorun                                 | Çözüm                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Desteklenmeyen SQL veritabanı özellikleri     | [Desteklenmeyen tablo özelliklerine](sql-data-warehouse-tables-overview.md#unsupported-table-features)bakın. |
| Desteklenmeyen SQL veritabanı veri türleri   | [Desteklenmeyen veri türlerine](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types)bakın.        |
| Saklı yordam sınırlamaları          | Saklı yordamların bazı sınırlamalarını anlamak için [saklı yordam sınırlamaları](sql-data-warehouse-develop-stored-procedures.md#limitations) bölümüne bakın. |
| UDF 'ler SELECT deyimlerini desteklemez | Bu, UDF 'lerimizin geçerli bir sınırlamasıdır.  Destekliyoruz sözdizimi için bkz. [oluşturma işlevi](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) . |
| sütunlar için sp_rename (Önizleme), *dbo* dışında şemalar üzerinde çalışmıyor | Bu, SYNAPSE [sp_rename (Önizleme) sütunlarının](/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)geçerli bir sınırlamasıdır.  *Dbo* şemasının bir parçası olmayan nesnelerdeki sütunlar, BIR CTAS aracılığıyla yeni bir tabloya yeniden adlandırılabilir. |

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzla ilgili çözüm bulma konusunda daha fazla yardım için, deneyebileceğiniz bazı diğer kaynaklar aşağıda verilmiştir.

* [Bloglar](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Özellik istekleri](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Videolar](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Destek bileti oluşturun](sql-data-warehouse-get-started-create-support-ticket.md)
* [Soru sayfası Microsoft Q&](/answers/topics/azure-synapse-analytics.html)
* [Stack Overflow Forumu](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)