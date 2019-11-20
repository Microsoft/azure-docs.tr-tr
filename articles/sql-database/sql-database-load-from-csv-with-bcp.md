---
title: CSV dosyasından veritabanına veri yükleme (bcp)
description: Küçük veri boyutları için Azure SQL Veritabanına veri aktarırken bcp kullanır.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: b0df3d588f1d9b0a50c3ea7a583b0704e7e85c39
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827482"
---
# <a name="load-data-from-csv-into-azure-sql-database-flat-files"></a>CSV dosyasından Azure SQL Veritabanı'na veri yükleme (düz dosyalar)

Bir CSV dosyasından Azure SQL Veritabanı’na veri aktarmak için bcp komut satırı yardımcı programını kullanabilirsiniz.

## <a name="before-you-begin"></a>Başlamadan önce

### <a name="prerequisites"></a>Ön koşullar

Bu makaledeki adımları tamamlayabilmeniz için şunlar gerekir:

* Azure SQL veritabanı sunucusu ve veritabanı
* bcp komut satırı yardımcı programının yüklü olması
* sqlcmd komut satırı yardımcı programının yüklü olması

bcp ve sqlcmd yardımcı programlarını [Microsoft İndirme Merkezi][Microsoft Download Center]'nden indirebilirsiniz.

### <a name="data-in-ascii-or-utf-16-format"></a>ASCII veya UTF-16 biçimindeki veriler

UTF-8 biçimi bcp tarafından desteklenmediğinden, bu öğreticiyi kendi verilerinizle deniyorsanız verilerinizin ASCII veya UTF-16 kodlamasını kullanıyor olması gerekir. 

## <a name="1-create-a-destination-table"></a>1. bir hedef tablo oluşturun

SQL Veritabanı'nda bir tabloyu hedef tablo olarak tanımlayın. Tablodaki sütunlar, veri dosyanızın tüm satırlarındaki verilere karşılık gelmelidir.

Tablo oluşturmak için bir komut istemi açın ve sqlcmd.exe dosyasını kullanarak şu komutu çalıştırın:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```


## <a name="2-create-a-source-data-file"></a>2. kaynak veri dosyası oluşturma

Not Defteri'ni açın ve yeni bir metin dosyasına aşağıdaki veri satırlarını kopyalayıp dosyayı yerel geçici dizininize (C:\Temp\DimDate2.txt) kaydedin. Bu veri ASCII biçimindedir.

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

(İsteğe bağlı) SQL Server veritabanından kendi verilerinizi dışarı aktarmak için bir komut istemi açın ve aşağıdaki komutu çalıştırın. TableName (Tablo Adı), ServerName (Sunucu Adı), DatabaseName (Veritabanı Adı), Username (Kullanıcı Adı) ve Password (Parola) alanlarına kendi bilgilerinizi yazın.

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t , 
```

## <a name="3-load-the-data"></a>3. verileri yükleme

Verileri yüklemek için bir komut satırı açın; Sunucu Adı, Veritabanı Adı, Kullanıcı Adı ve Parola alanlarına kendi bilgilerinizi yazarak aşağıdaki komutu çalıştırın.

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

Bu komutu kullanarak verilerin düzgün şekilde yüklendiğini doğrulayın

```bcp
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Sonuçlar şu şekilde görünmelidir:

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

## <a name="next-steps"></a>Sonraki adımlar

Bir SQL Server veritabanına geçiş yapmak için bkz. [SQL Server veritabanı geçişi](sql-database-single-database-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
