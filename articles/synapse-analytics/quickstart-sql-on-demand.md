---
title: 'Hızlı başlangıç: sunucusuz SQL havuzu kullanma'
description: Bu hızlı başlangıçta, sunucusuz SQL havuzu kullanarak çeşitli dosya türlerini nasıl sorgulayacağınızı görürsünüz ve öğrenirsiniz.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 8607355069bbae5983239ddbd3e8752143f31497
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676349"
---
# <a name="quickstart-use-serverless-sql-pool"></a>Hızlı başlangıç: sunucusuz SQL havuzu kullanma

SYNAPSE sunucusuz SQL havuzu, Azure depolama 'ya yerleştirilmiş dosyalarda SQL sorguları çalıştırmanızı sağlayan sunucusuz bir sorgu hizmetidir. Bu hızlı başlangıçta, sunucusuz SQL havuzu kullanarak çeşitli dosya türlerini sorgulamayı öğreneceksiniz. Desteklenen biçimler [OPENROWSET](sql/develop-openrowset.md)'de listelenmiştir.

Bu hızlı başlangıçta sorgulama: CSV, Apache Parquet ve JSON dosyaları gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Sorguların gönderileceği SQL istemcisini seçin:

- [Azure SYNAPSE Studio](./get-started-create-workspace.md) , depolama alanındaki dosyalara GÖZATıP SQL sorguları oluşturmayı kullanabileceğiniz bir web aracıdır.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) , isteğe bağlı veritabanınızda SQL sorguları ve Not defterleri çalıştırmanızı sağlayan bir istemci aracıdır.
- [SQL Server Management Studio](sql/get-started-ssms.md) , isteğe bağlı veritabanınızda SQL sorguları çalıştırmanızı sağlayan bir istemci aracıdır.

Bu hızlı başlangıç için Parametreler:

| Parametre                                 | Açıklama                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Sunucusuz SQL havuzu hizmeti uç noktası adresi    | Sunucu adı olarak kullanılır                                   |
| Sunucusuz SQL havuzu hizmet uç noktası bölgesi     | Örneklerde hangi depolamanın kullanılacağını belirlemek için kullanılır |
| Uç nokta erişimi için Kullanıcı adı ve parola | Uç noktaya erişmek için kullanılır                               |
| Görünümler oluşturmak için kullanılan veritabanı         | Örneklerde başlangıç noktası olarak kullanılan veritabanı       |

## <a name="first-time-setup"></a>İlk kez kurulum

Örnekleri kullanmadan önce:

- Görünümleriniz için veritabanı oluşturma (görünümleri kullanmak istediğiniz durumlarda)
- Depolama alanındaki dosyalara erişmek için sunucusuz SQL havuzu tarafından kullanılacak kimlik bilgilerini oluşturma

### <a name="create-database"></a>Veritabanı oluşturma

Tanıtım amacıyla kendi veritabanınızı oluşturun. Bu veritabanını, bu makaledeki görünümlerinizi ve örnek sorguları oluşturmak için kullanacaksınız.

> [!NOTE]
> Veritabanları, gerçek veriler için değil yalnızca görünüm meta verileri için kullanılır.
>Daha sonra hızlı başlangıçta kullanmak üzere kullandığınız veritabanı adını yazın.

`mydbname`Seçtiğiniz bir ada geçiş yapmak için aşağıdaki sorguyu kullanın:

```sql
CREATE DATABASE mydbname
```

### <a name="create-data-source"></a>Veri kaynağı oluştur

Sunucusuz SQL havuzu kullanarak sorguları çalıştırmak için, sunucusuz SQL havuzunun depolama alanındaki dosyalara erişmek için kullanabileceği veri kaynağı oluşturun.
Bu bölümdeki örneklerde kullanılan veri kaynağını oluşturmak için aşağıdaki kod parçacığını yürütün:

```sql
-- create master key that will protect the credentials:
CREATE MASTER KEY ENCRYPTION BY PASSWORD = <enter very strong password here>

-- create credentials for containers in our demo storage account
CREATE DATABASE SCOPED CREDENTIAL sqlondemand
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

## <a name="query-csv-files"></a>CSV dosyalarını sorgulama

Aşağıdaki görüntüde Sorgulanacak dosyanın önizlemesi verilmiştir:

![Üst bilgi içermeyen CSV dosyasının ilk 10 satırı, Windows stili yeni satır.](./sql/media/query-single-csv-file/population.png)

Aşağıdaki sorgu, üst bilgi satırı içermeyen bir CSV dosyasının nasıl okunacağını, Windows stili yeni satırla ve virgülle ayrılmış sütunlarla nasıl okunacağını gösterir:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'csv/population/*.csv',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT = 'CSV', PARSER_VERSION = '2.0'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

Şemayı, sorgu derleme zamanında belirtebilirsiniz.
Daha fazla örnek için bkz. [CSV dosyasını sorgulama](sql/query-single-csv-file.md).

## <a name="query-parquet-files"></a>Parquet dosyalarını sorgulama

Aşağıdaki örnek, Parquet dosyalarını sorgulamak için otomatik Şema çıkarımı özelliklerini gösterir. Şemayı belirtmeden, Eylül 2017 ' deki satır sayısını döndürür.

> [!NOTE]
> `OPENROWSET WITH`Parquet dosyalarını okurken yan tümce içinde sütunlar belirtmeniz gerekmez. Bu durumda, sunucusuz SQL havuzu, verileri Parquet dosyasında kullanır ve sütunları ada göre bağlar.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'parquet/taxi/year=2017/month=9/*.parquet',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT='PARQUET'
  ) AS nyc
```

[Parquet dosyalarını sorgulama](sql/query-parquet-files.md)hakkında daha fazla bilgi edinin.

## <a name="query-json-files"></a>JSON dosyalarını sorgulama

### <a name="json-sample-file"></a>JSON örnek dosyası

Dosyalar *JSON* kapsayıcısına, klasör *kitaplarına* depolanır ve aşağıdaki yapıyla tek defter girişi içerir:

```json
{  
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[  
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

### <a name="query-json-files"></a>JSON dosyalarını sorgulama

Aşağıdaki sorgu, *Cryptology 'de dayalı ve Istatistiksel Yöntemler* başlığına sahip bir kitapta skalar değerler (başlık, yayımcı) almak için [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) kullanmayı gösterir, seçilen makalelere giriş:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'json/books/*.json',
      DATA_SOURCE = 'SqlOnDemandDemo'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> Tüm JSON dosyasını tek satır/sütun olarak okuyoruz. Bu nedenle, dosya içinde bulmayı beklemediğimiz için FIELDSONLANDıRıCı, FIELDQUOTE ve ROWSONLANDıRıCı, 0x0B olarak ayarlanmıştır.

## <a name="next-steps"></a>Sonraki adımlar

Şimdi aşağıdaki makalelerle devam etmeye hazırsınız:

- [Tek CSV dosyasını sorgula](sql/query-single-csv-file.md)
- [Sorgu klasörleri ve birden çok CSV dosyası](sql/query-folders-multiple-csv-files.md)
- [Belirli dosyaları sorgula](sql/query-specific-files.md)
- [Parquet dosyalarını sorgulama](sql/query-parquet-files.md)
- [Parquet iç içe türlerini sorgulama](sql/query-parquet-nested-types.md)
- [JSON dosyalarını sorgulama](sql/query-json-files.md)
- [Görünümleri oluşturma ve kullanma](sql/create-use-views.md)
- [Dış tablolar oluşturma ve kullanma](sql/create-use-external-tables.md)
- [Sorgu sonucunu Azure depolama 'ya kalıcı yap](sql/create-external-table-as-select.md)
- [Tek CSV dosyasını sorgula](sql/query-single-csv-file.md)