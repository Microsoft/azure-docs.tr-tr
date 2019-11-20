---
title: T-SQL bildirimleri
description: Azure SQL veri ambarı 'nda desteklenen T-SQL deyimlerine yönelik belgelere bağlantılar.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 861675941693697a20da189edebe33d91918782c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685524"
---
# <a name="t-sql-statements-supported-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda desteklenen T-SQL deyimleri
Azure SQL veri ambarı 'nda desteklenen T-SQL deyimlerine yönelik belgelere bağlantılar.

## <a name="data-definition-language-ddl-statements"></a>Veri tanımlama dili (DDL) deyimleri
* [ALTER DATABASE](https://msdn.microsoft.com/library/mt204042.aspx)
* [ALTER ıNDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [DEĞIŞIKLIK GERÇEKLEŞTIRILMIŞ görünüm](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) (Önizleme) 
* [DEĞIŞTIRME YORDAMı](https://msdn.microsoft.com/library/ms189762.aspx)
* [ŞEMAYı DEĞIŞTIR](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [COLUMNSTORE DIZINI OLUŞTUR](https://msdn.microsoft.com/library/gg492153.aspx)
* [CREATE DATABASE](https://msdn.microsoft.com/library/mt204021.aspx)
* [VERITABANı KAPSAMLı KIMLIK BILGISI OLUŞTUR](https://msdn.microsoft.com/library/mt270260.aspx)
* [DıŞ VERI KAYNAĞı OLUŞTUR](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [DıŞ TABLO OLUŞTUR](https://msdn.microsoft.com/library/dn935021.aspx)
* [IŞLEV OLUŞTUR](https://msdn.microsoft.com/library/mt203952.aspx)
* [DIZIN OLUŞTUR](https://msdn.microsoft.com/library/ms188783.aspx)
* SELECT (Önizleme) [olarak GERÇEKLEŞTIRILMIŞ görünüm oluştur](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) 
* [YORDAM OLUŞTUR](https://msdn.microsoft.com/library/ms187926.aspx)
* [ŞEMA OLUŞTUR](https://msdn.microsoft.com/library/ms189462.aspx)
* [CREATE STATISTICS](https://msdn.microsoft.com/library/ms188038.aspx)
* [CREATE TABLE](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [GÖRÜNÜM OLUŞTUR](https://msdn.microsoft.com/library/ms187956.aspx)
* [IŞ YÜKÜ SıNıFLANDıRıCıSı OLUŞTURMA](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [DıŞ VERI KAYNAĞıNı BıRAK](https://msdn.microsoft.com/library/mt146367.aspx)
* [DıŞ DOSYA BIÇIMINI BıRAK](https://msdn.microsoft.com/library/mt146379.aspx)
* [DıŞ TABLO BıRAK](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [PROSEDÜRÜ BıRAK](https://msdn.microsoft.com/library/ms174969.aspx)
* [BıRAKMA ISTATISTIKLERI](https://msdn.microsoft.com/library/ms175075.aspx)
* [TABLOYU BıRAK](https://msdn.microsoft.com/library/ms173790.aspx)
* [ŞEMAYı BıRAK](https://msdn.microsoft.com/library/ms186751.aspx)
* [GÖRÜNÜMÜ BıRAK](https://msdn.microsoft.com/library/ms173492.aspx)
* [BıRAKMA IŞ YÜKÜ SıNıFLANDıRıCıSı](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [YENIDEN ADLANDıR](https://msdn.microsoft.com/library/mt631611.aspx)
* [RESULT_SET_CACHING AYARLA](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [TRUNCATE TABLE](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>Veri Işleme dili (DML) deyimleri
* [DELETE](https://msdn.microsoft.com/library/ms189835.aspx)
* [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)
* [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Veritabanı Konsolu komutları
* [DBCC DROPCLEANARABELLEKLERI](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC DROPRESULTSETCACHE](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) (Önizleme)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRıNKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC SHOWRESULTCACHESPACEKULLANDıNıZ](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) (Önizleme)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Sorgu deyimleri
* [SEÇIN](https://msdn.microsoft.com/library/ms189499.aspx)
* [Common_table_expression Ile](https://msdn.microsoft.com/library/ms175972.aspx)
* [EXCEPT ve ıNTERSECT](https://msdn.microsoft.com/library/ms188055.aspx)
* [AÇıKLAMAK](https://msdn.microsoft.com/library/mt631615.aspx)
* [Kaynak](https://msdn.microsoft.com/library/ms177634.aspx)
* [PIVOT ve UNPıVOT kullanma](https://msdn.microsoft.com/library/ms177410.aspx)
* [GRUPLANDıRMA ÖLÇÜTÜ](https://msdn.microsoft.com/library/ms177673.aspx)
* [AÇMADAN](https://msdn.microsoft.com/library/ms180199.aspx)
* [SıRALAMA ÖLÇÜTÜ](https://msdn.microsoft.com/library/ms188385.aspx)
* [OPTION](https://msdn.microsoft.com/library/ms190322.aspx)
* [BIRLEŞIM](https://msdn.microsoft.com/library/ms180026.aspx)
* [OLMADıĞı](https://msdn.microsoft.com/library/ms188047.aspx)
* [Sayfanın Üstü](https://msdn.microsoft.com/library/ms189463.aspx)
* [Masını](https://msdn.microsoft.com/library/mt631614.aspx)
* [Arama koşulu](https://msdn.microsoft.com/library/ms173545.aspx)
* [Alt](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Güvenlik deyimleri
* İzinler: [verme](https://msdn.microsoft.com/library/ms187965.aspx), [reddetme](https://msdn.microsoft.com/library/ms188338.aspx), [iptal etme](https://msdn.microsoft.com/library/ms187728.aspx)
* [DEĞIŞTIRME YETKILENDIRMESI](https://msdn.microsoft.com/library/ms187359.aspx)
* [SERTIFIKAYı DEĞIŞTIR](https://msdn.microsoft.com/library/ms189511.aspx)
* [ALTER DATABASE ŞIFRELEME ANAHTARı](https://msdn.microsoft.com/library/bb630389.aspx)
* [DEĞIŞTIRME OTURUMU AÇMA](https://msdn.microsoft.com/library/ms189828.aspx)
* [DEĞIŞIKLIK ANA ANAHTARı](https://msdn.microsoft.com/library/ms186937.aspx)
* [ROLÜ DEĞIŞTIR](https://msdn.microsoft.com/library/ms189775.aspx)
* [KULLANıCı DEĞIŞTIR](https://msdn.microsoft.com/library/ms176060.aspx)
* [YEDEKLEME SERTIFIKASı](https://msdn.microsoft.com/library/ms178578.aspx)
* [ANA ANAHTARı KAPAT](https://msdn.microsoft.com/library/ms188387.aspx)
* [SERTIFIKA OLUŞTUR](https://msdn.microsoft.com/library/ms187798.aspx)
* [VERITABANı ŞIFRELEME ANAHTARı OLUŞTUR](https://msdn.microsoft.com/library/bb677241.aspx)
* [OTURUM AÇMA OLUŞTUR](https://msdn.microsoft.com/library/ms189751.aspx)
* [ANA ANAHTAR OLUŞTUR](https://msdn.microsoft.com/library/ms174382.aspx)
* [ROL OLUŞTUR](https://msdn.microsoft.com/library/ms187936.aspx)
* [KULLANıCı OLUŞTUR](https://msdn.microsoft.com/library/ms173463.aspx)
* [SERTIFIKAYı BıRAK](https://msdn.microsoft.com/library/ms179906.aspx)
* [BıRAKMA VERITABANı ŞIFRELEME ANAHTARı](https://msdn.microsoft.com/library/bb630256.aspx)
* [OTURUMU BıRAK](https://msdn.microsoft.com/library/ms188012.aspx)
* [ANA ANAHTAR BıRAK](https://msdn.microsoft.com/library/ms180071.aspx)
* [ROLÜ BıRAK](https://msdn.microsoft.com/library/ms174988.aspx)
* [KULLANıCıYı BıRAK](https://msdn.microsoft.com/library/ms189438.aspx)
* [ANA ANAHTARı AÇ](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla başvuru bilgisi için bkz. [Azure SQL veri ambarı 'Nda T-SQL dil öğeleri](sql-data-warehouse-reference-tsql-language-elements.md)ve [Azure SQL veri ambarı 'nda sistem görünümleri](sql-data-warehouse-reference-tsql-system-views.md).
