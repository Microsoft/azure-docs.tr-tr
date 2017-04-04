---
title: "SQL Server&quot;dan Azure SQL Veri Ambarı&quot;na (PolyBase) veri yükleme | Microsoft Belgeleri"
description: "SQL Server&quot;dan düz dosyalara veri aktarmak için bcp&quot;yi, Azure blob depolama alanına veri aktarmak için AZCopy&quot;yi ve verileri Azure SQL Data Warehouse&quot;a almak için PolyBase&quot;i kullanın."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4d42786a-fb28-43c9-9c3b-72d19c0ecc11
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 21b4cc704e271ac220fd606305f8f97c9b2593bb
ms.lasthandoff: 03/22/2017



---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>SQL Server'dan Azure SQL Data Warehouse'a veri yükleme (AZCopy)
SQL Server'dan Azure blob depolama alanına veri yüklemek için bcp ve AZCopy komut satırı yardımcı programlarını kullanın. Ardından verileri Azure SQL Data Warehouse'a yüklemek için PolyBase'i veya Azure Data Factory'yi kullanın. 

## <a name="prerequisites"></a>Önkoşullar
Bu öğreticide ilerleyebilmeniz için şunlar gereklidir:

* SQL Data Warehouse veritabanı
* bcp komut satırı yardımcı programının yüklü olması
* SQLCMD komut satırı yardımcı programının yüklü olması

> [!NOTE]
> bcp ve sqlcmd yardımcı programlarını [Microsoft İndirme Merkezi][Microsoft Download Center]'nden indirebilirsiniz.
> 
> 

## <a name="import-data-into-sql-data-warehouse"></a>SQL Data Warehouse'a veri aktarma
Bu öğreticide, Azure SQL Data Warehouse'da bir tablo oluşturup tabloya veri aktaracaksınız.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>1. Adım: Azure SQL Data Warehouse'da tablo oluşturma
Örneğinizde bir tablo oluşturmak için aşağıdaki sorguyu çalıştırmak üzere bir komut isteminden sqlcmd yardımcı programını kullanın:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

> [!NOTE]
> SQL Veri Ambarı'nda tablo oluşturma ve WITH yan tümcesinde bulunan seçenekler hakkında daha fazla bilgi için bkz. [Tabloya Genel Bakış][Table Overview] veya [CREATE TABLE söz dizimi][CREATE TABLE syntax].
> 
> 

### <a name="step-2-create-a-source-data-file"></a>2. Adım: Kaynak veri dosyası oluşturma
Not Defteri'ni açın ve yeni bir metin dosyasına aşağıdaki veri satırlarını kopyalayıp dosyayı yerel geçici dizininize (C:\Temp\DimDate2.txt) kaydedin.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> bcp.exe dosyasının UTF-8 dosya kodlamasını desteklemediğini unutmayın. bcp.exe dosyasını kullanırken lütfen ASCII dosyalarını veya UTF-16 kodlu dosyaları kullanın.
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>3. Adım: Bağlanma ve verileri içeri aktarma
bcp ile aşağıdaki komutu kullanıp değerleri uygun şekilde değiştirerek bağlanabilir ve verileri içeri aktarabilirsiniz.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

sqlcmd ile aşağıdaki sorguyu çalıştırarak verilerin yüklendiğini doğrulayabilirsiniz:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Bu işlem sonrasında şu sonuçların döndürülmesi gerekir:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>4. Adım: Yeni yüklenmiş verilerinize ilişkin İstatistikler oluşturma
Azure SQL Data Warehouse henüz istatistiklerin otomatik olarak oluşturulup güncelleştirilmesini desteklemiyor. Sorgularınızdan en iyi performansı elde edebilmeniz için ilk yüklemeden veya verilerdeki önemli değişikliklerden sonra her tablonun her sütununa ilişkin istatistiklerin oluşturulması önemlidir. İstatistikler hakkında ayrıntılı bir açıklama için Geliştirme ile ilgili konu başlığı grubunda yer alan [İstatistikler][Statistics] bölümüne göz atın. Aşağıda bu örnekte yüklenen tablolar için nasıl istatistik oluşturacağınıza yönelik kısa bir örnek verilmiştir.

Bir sqlcmd isteminden şu CREATE STATISTICS deyimlerini yürütün:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>SQL Data Warehouse'dan veri aktarma
Bu öğreticide SQL Data Warehouse'da bulunan bir tablodan veri dosyası oluşturacaksınız. Yukarıda oluşturduğumuz verileri DimDate2_export.txt adlı dosyaya aktaracağız.

### <a name="step-1-export-the-data"></a>1. Adım: Verileri dışarı aktarma
bcp yardımcı programı ile aşağıdaki komutu kullanıp değerleri uygun şekilde değiştirerek bağlanabilir ve verileri içeri aktarabilirsiniz.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Yeni dosyayı açarak verilerin düzgün bir şekilde dışarı aktarıldığını doğrulayabilirsiniz. Dosyadaki verilerin aşağıdaki metinle eşleşmesi gerekir:

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> Dağıtılmış sistemlerin yapısı gereği, veri sırası SQL Data Warehouse veritabanlarında aynı olmayabilir. Tüm tabloyu dışarı aktarmak yerine, bcp yardımcı programının **queryout** işlevini kullanarak bir sorgu ayıklaması da yazabilirsiniz.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
Yüklemeye genel bakış için bkz. [SQL Veri Ambarı'na veri yükleme][Load data into SQL Data Warehouse].
Geliştirme ile ilgili daha fazla ipucu için bkz. [SQL Veri Ambarı’nda geliştirmeye genel bakış][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->

[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Table Overview]: ./sql-data-warehouse-tables-overview.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433

