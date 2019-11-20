---
title: Sorun giderme
description: Azure SQL veri ambarı sorunlarını giderme.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 7/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2aa7926286be277c7ad0aa7054b4bd6fceb8229f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685407"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Azure SQL veri ambarı sorunlarını giderme
Bu makalede, yaygın sorun giderme sorusu listelenmektedir.

## <a name="connecting"></a>Bağlanmada
| Sorun                                                        | Çözünürlük                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 'NT AUTHORITY\ANONYMOUS LOGON' kullanıcısı için oturum açma başarısız oldu. (Microsoft SQL Server, hata: 18456) | Bu hata, bir AAD kullanıcısı ana veritabanına bağlanmaya çalıştığında ancak ana veritabanında bir kullanıcıya sahip olmadığında oluşur.  Bu sorunu düzeltmek için, bağlantı zamanında bağlanmak istediğiniz SQL Veri Ambarı’nı belirtin veya kullanıcıyı ana veritabanına ekleyin.  Daha fazla bilgi için [güvenlik genel bakış][Security overview] makalesine bakın. |
| Sunucu sorumlusu "MyUserName" geçerli güvenlik bağlamında "asıl" veritabanına erişemiyor. Kullanıcının varsayılan veritabanı açılamıyor. Oturum açılamadı. 'MyUserName' kullanıcısı için oturum açma başarısız oldu. (Microsoft SQL Server, hata: 916) | Bu hata, bir AAD kullanıcısı ana veritabanına bağlanmaya çalıştığında ancak ana veritabanında bir kullanıcıya sahip olmadığında oluşur.  Bu sorunu düzeltmek için, bağlantı zamanında bağlanmak istediğiniz SQL Veri Ambarı’nı belirtin veya kullanıcıyı ana veritabanına ekleyin.  Daha fazla bilgi için [güvenlik genel bakış][Security overview] makalesine bakın. |
| CTAıP hatası                                                  | Bu hata, SQL Server ana veritabanında bir oturum oluşturulduğunda, ancak SQL veri ambarı veritabanında olmadığında ortaya çıkabilir.  Bu hatayla karşılaşırsanız, [güvenlik genel bakış][Security overview] makalesine göz atın.  Bu makalede, ana bilgisayarda bir oturum açma ve Kullanıcı oluşturma ve ardından SQL veri ambarı veritabanında bir Kullanıcı oluşturma işlemleri açıklanmaktadır. |
| Güvenlik duvarı tarafından engellendi                                          | Azure SQL veritabanları, yalnızca bilinen IP adreslerinin bir veritabanına erişimi olduğundan emin olmak için sunucu ve veritabanı düzeyi güvenlik duvarları tarafından korunur. Güvenlik duvarları varsayılan olarak güvenli hale gelir. Bu, bağlanmadan önce açıkça etkinleştirmeniz ve IP adresi veya adres aralığı yapmanız gerekir.  Güvenlik duvarınızı erişim için yapılandırmak üzere, [sağlama yönergelerindeki][Provisioning instructions] [istemci IP 'niz için sunucu güvenlik duvarı erişimini yapılandırma][Configure server firewall access for your client IP] bölümündeki adımları izleyin. |
| Araçla veya sürücüyle bağlantı yapılamaz                           | SQL veri ambarı, [SMS][SSMS]'Yi, [Visual Studio için SSDT][SSDT for Visual Studio]'yi veya verilerinizi sorgulamak için [sqlcmd][sqlcmd] kullanılmasını önerir. Sürücüler hakkında daha fazla bilgi ve SQL veri ambarı 'na bağlanma hakkında daha fazla bilgi için bkz. [Azure SQL veri ambarı Için sürücüler][Drivers for Azure SQL Data Warehouse] ve [Azure SQL veri ambarı makalelerine bağlanma][Connect to Azure SQL Data Warehouse] . |

## <a name="tools"></a>Araçlar
| Sorun                                                        | Çözünürlük                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio Nesne Gezgini 'nde AAD kullanıcıları eksik           | Bu bilinen bir sorundur.  Geçici bir çözüm olarak, [sys. database_principals][sys.database_principals]içindeki kullanıcıları görüntüleyin.  SQL veri ambarı ile Azure Active Directory kullanma hakkında daha fazla bilgi edinmek için bkz. [Azure SQL veri ambarı 'Nda kimlik doğrulama][Authentication to Azure SQL Data Warehouse] . |
| El ile betik oluşturma, betik Sihirbazı 'nı kullanma veya SSMS aracılığıyla bağlanma yavaş, yanıt vermiyor veya hata üretmiyor | Ana veritabanında kullanıcıların oluşturulduğundan emin olun. Komut dosyası seçeneklerinde Ayrıca, Engine sürümünün "Microsoft Azure SQL Veri Ambarı Edition" olarak ayarlandığından ve altyapı türünün "Microsoft Azure SQL Veritabanı" olduğundan emin olun. |
| SSMS 'de betik oluşturma başarısız oluyor                               | "Bağımlı nesneler için betik oluştur" seçeneği "true" olarak ayarlandıysa SQL veri ambarı için betik oluşturma işlemi başarısız olur. Geçici bir çözüm olarak, kullanıcıların Araçlar-> Seçenekler-> SQL Server Nesne Gezgini-> bağımlı seçenekler için betik oluştur ve yanlış olarak ayarla seçeneğine el ile gitmesi gerekir |

## <a name="performance"></a>Performans
| Sorun                                                        | Çözünürlük                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Sorgu performansı sorunlarını giderme                            | Belirli bir sorgu sorunlarını gidermeye çalışıyorsanız, [sorgularınızı nasıl izleyebileceğiniz hakkında bilgi edinmeye][Learning how to monitor your queries]başlayın. |
| Kötü sorgu performansı ve planları genellikle eksik istatistiklerin bir sonucudur | Düşük performansın en yaygın nedeni, tablolarınızdaki istatistiklerin olmamasıdır.  İstatistiklerin nasıl oluşturulacağı ve performanstan ne kadar önemli olduğunu gösteren Ayrıntılar için bkz. [tablo Istatistiklerini koruma][Statistics] . |
| Sıraya alınan düşük eşzamanlılık/sorgu sayısı                             | Bellek ayırmayı eşzamanlılık ile dengelemeye anlamak için [Iş yükü yönetimini][Workload management] anlamak önemlidir. |
| En iyi yöntemleri uygulama                              | Sorgu performansını artırmak için en iyi başlangıç [SQL veri ambarı en iyi yöntemler][SQL Data Warehouse best practices] makaleleridir. |
| Ölçeklendirmeyle performansı artırma                      | Bazen, performansı iyileştirmeye yönelik çözüm, [SQL veri Ambarınızı ölçeklendirerek][Scaling your SQL Data Warehouse]sorgularınıza daha fazla işlem gücü eklemektir. |
| Yetersiz Dizin kalitesinin sonucu olarak sorgu performansı yetersiz     | Bazı süreler sorguları, [düşük columnstore dizin kalitesi][Poor columnstore index quality]nedeniyle yavaşlayabilir.  Daha fazla bilgi ve [bölüm kalitesini artırmak için dizinleri yeniden oluşturma][Rebuild indexes to improve segment quality]hakkında daha fazla bilgi için bu makaleye bakın. |

## <a name="system-management"></a>Sistem Yönetimi
| Sorun                                                        | Çözünürlük                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: sunucu, izin verilen veritabanı Işlem birimi kotasını (45000) aşacağından işlem gerçekleştirilemedi. | Oluşturmaya çalıştığınız veritabanının [DWU][DWU] değerini azaltın ya da [bir kota artışı isteyin][request a quota increase]. |
| Alan kullanımını araştırma                              | Sisteminizin alan kullanımını anlamak için bkz. [Tablo boyutları][Table sizes] . |
| Tabloları yönetmeyle ilgili yardım                                    | Tablolarınızı yönetme hakkında yardım almak için [tabloya genel bakış][Overview] makalesine bakın.  Bu makale ayrıca [tablo veri türleri][Data types], tablo [dağıtma][Distribute], tablo [dizini oluşturma][Index] [, tablo][Partition]istatistikleri ve [geçici tabloları][Temporary] [koruma][Statistics] gibi daha ayrıntılı konulara bağlantılar içerir. |
| Saydam veri şifrelemesi (TDE) ilerleme çubuğu Azure portal güncelleştirilmiyor | TDE [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption)ile durumunu görüntüleyebilirsiniz. |


## <a name="differences-from-sql-database"></a>SQL veritabanı farklılıkları
| Sorun                                 | Çözünürlük                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Desteklenmeyen SQL veritabanı özellikleri     | [Desteklenmeyen tablo özelliklerine][Unsupported table features]bakın. |
| Desteklenmeyen SQL veritabanı veri türleri   | [Desteklenmeyen veri türlerine][Unsupported data types]bakın.        |
| SILME ve GÜNCELLEŞTIRME sınırlamaları         | [Desteklenmeyen güncelleştirme ve silme sözdizimini çözmek için][Using CTAS to work around unsupported UPDATE and DELETE syntax]bkz. [geçici çözümleri güncelleştirme][UPDATE workarounds], [geçici çözümleri silme][DELETE workarounds] ve CTAS kullanma. |
| MERGE deyimleri desteklenmiyor      | Bkz. [birleştirme geçici çözümleri][MERGE workarounds].                  |
| Saklı yordam sınırlamaları          | Saklı yordamların bazı sınırlamalarını anlamak için [saklı yordam sınırlamaları][Stored procedure limitations] bölümüne bakın. |
| UDF 'ler SELECT deyimlerini desteklemez | Bu, UDF 'lerimizin geçerli bir sınırlamasıdır.  Destekliyoruz sözdizimi için bkz. [oluşturma işlevi][CREATE FUNCTION] . |

## <a name="next-steps"></a>Sonraki adımlar
Sorununuzla ilgili çözüm bulma konusunda daha fazla yardım için, deneyebileceğiniz bazı diğer kaynaklar aşağıda verilmiştir.

* [Bloglar]
* [Özellik istekleri]
* [Videolar]
* [KEDI ekibi blogları]
* [Destek bileti oluşturma]
* [MSDN forumu]
* [Stack Overflow forumu]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SSMS]: /sql/ssms/download-sql-server-management-studio-ssms
[SSDT for Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Destek bileti oluşturma]: sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: sql-data-warehouse-manage-compute-overview.md
[DWU]: sql-data-warehouse-overview-what-is.md
[request a quota increase]: sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: sql-data-warehouse-best-practices.md
[Table sizes]: sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: sql-data-warehouse-tables-overview.md
[Data types]: sql-data-warehouse-tables-data-types.md
[Distribute]: sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md
[UPDATE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md


<!--MSDN references-->
[sys.database_principals]: /sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql
[CREATE FUNCTION]: /sql/t-sql/statements/create-function-sql-data-warehouse
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Bloglar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[KEDI ekibi blogları]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Özellik istekleri]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN forumu]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stack Overflow forumu]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videolar]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Databricks]: https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse
