---
title: Sunucusuz SQL havuzu kullanarak Parquet dosyalarını sorgulama
description: Bu makalede, bir sunucusuz SQL havuzu kullanarak Parquet dosyalarını sorgulamayı öğreneceksiniz.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 72bf8e76217e8a3bcecd381d8d61815c539dd196
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209596"
---
# <a name="query-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu kullanarak Parquet dosyalarını sorgulama

Bu makalede, Parquet dosyalarını okuyacak sunucusuz SQL havuzu kullanarak bir sorgu yazmayı öğreneceksiniz.

## <a name="quickstart-example"></a>Hızlı başlangıç örneği

`OPENROWSET` işlevi, dosyanızın URL 'sini sağlayarak Parquet dosyasının içeriğini okumanızı sağlar.

### <a name="read-parquet-file"></a>Parquet dosyasını okuyun

Dosyanızın içeriğini görmenin en kolay yolu, `PARQUET` işlev için dosya URL 'si sağlamaktır `OPENROWSET` ve parquet ' i belirtmektir `FORMAT` . Dosya herkese açık ise veya Azure AD kimliğiniz bu dosyaya erişebilirse, aşağıdaki örnekte gösterildiği gibi sorguyu kullanarak dosyanın içeriğini görmeniz gerekir:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

Bu dosyaya erişebildiğinizden emin olun. Dosyanız SAS anahtarı veya özel Azure kimliğiyle korunuyorsa [SQL oturum açma için sunucu düzeyi kimlik bilgilerini](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)oluşturmanız gerekir.

> [!IMPORTANT]
> `Latin1_General_100_BIN2_UTF8`PARQUET dosyalarındaki dize DEĞERLERI UTF-8 kodlaması kullanılarak kodlandığından, UTF-8 veritabanı harmanlaması (örneğin) kullandığınızdan emin olun.
> PARQUET dosyasındaki metin kodlaması arasında uyuşmazlık var ve harmanlama beklenmeyen dönüştürme hatalarına neden olabilir.
> Aşağıdaki T-SQL ifadesini kullanarak geçerli veritabanının varsayılan harmanlamasını kolayca değiştirebilirsiniz: `alter database current collate Latin1_General_100_BIN2_UTF8`

### <a name="data-source-usage"></a>Veri kaynağı kullanımı

Önceki örnek, dosyanın tam yolunu kullanır. Alternatif olarak, depolama alanının kök klasörünü işaret eden konum ile bir dış veri kaynağı oluşturabilir ve bu veri kaynağını ve işlevindeki dosyanın göreli yolunu kullanabilirsiniz `OPENROWSET` :

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

Bir veri kaynağı SAS anahtarı veya özel kimlikle korunuyorsa, [veri kaynağını veritabanı kapsamlı kimlik](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)bilgileriyle yapılandırabilirsiniz.

### <a name="explicitly-specify-schema"></a>Açıkça şema belirt

`OPENROWSET` dosya kullanarak dosyadan okumak istediğiniz sütunları açıkça belirtmenize olanak sağlar `WITH` :

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

> [!IMPORTANT]
> `Latin1_General_100_BIN2_UTF8`Yan tümce içindeki tüm dize sütunları için BIR UTF-8 harmanlaması (örneğin) `WITH` veya veritabanı DÜZEYINDE bir UTF-8 harmanlamasını belirtdiğinizden emin olun.
> Dosya ve dize sütunu harmanlamasında metin kodlama arasında uyuşmazlık beklenmeyen dönüştürme hatalarına neden olabilir.
> Aşağıdaki T-SQL ifadesini kullanarak geçerli veritabanının varsayılan harmanlamasını kolayca değiştirebilirsiniz: `alter database current collate Latin1_General_100_BIN2_UTF8`
> Aşağıdaki tanımı kullanarak Sütu türlerinde harmanlamayı kolayca ayarlayabilirsiniz: `geo_id varchar(6) collate Latin1_General_100_BIN2_UTF8`

Aşağıdaki bölümlerde, çeşitli türlerdeki PARQUET dosyalarını sorgulama hakkında bilgi alabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

İlk adımınız, bir veri kaynağı ile [NYC sarı TAXI](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) depolama hesabına başvuran **bir veritabanı oluşturmaktır** . Sonra bu veritabanında [kurulum betiğini](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) yürüterek nesneleri başlatın. Bu kurulum betiği, veri kaynaklarını, veritabanı kapsamlı kimlik bilgilerini ve bu örneklerde kullanılan harici dosya biçimlerini oluşturacaktır.

## <a name="dataset"></a>Veri kümesi

Bu örnekte [NYC sarı TAXI](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) veri kümesi kullanılıyor. Parquet dosyalarını [CSV dosyalarını okurken](query-parquet-files.md)de aynı şekilde sorgulayabilirsiniz. Tek fark `FILEFORMAT` parametresi olarak ayarlanmalıdır `PARQUET` . Bu makaledeki örneklerde, Parquet dosyalarını okuma özellikleri gösterilmektedir.

## <a name="query-set-of-parquet-files"></a>Parquet dosyaları sorgu kümesi

Parquet dosyalarını Sorgulayabileceğiniz zaman yalnızca ilgilendiğiniz sütunları belirtebilirsiniz.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>Otomatik Şema çıkarımı

Parquet dosyalarını okurken OPENROWSET WıTH yan tümcesini kullanmanız gerekmez. Sütun adları ve veri türleri, Parquet dosyalarından otomatik olarak okur.

Aşağıdaki örnekte, Parquet dosyaları için otomatik Şema çıkarımı özellikleri gösterilmektedir. Bir şema belirtmeden 2018 Eylül ayının satır sayısını döndürür.

> [!NOTE]
> Parquet dosyalarını okurken OPENROWSET WıTH yan tümcesinde sütunlar belirtmeniz gerekmez. Bu durumda, sunucusuz SQL havuzu sorgu hizmeti, Parquet dosyasındaki meta verileri kullanır ve sütunları ada göre bağlar.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=9/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Bölümlenmiş verileri sorgulama

Bu örnekte belirtilen veri kümesi ayrı alt klasörlere bölünür (bölümlenmiş). FilePath işlevini kullanarak belirli bölümleri hedefleyebilirsiniz. Bu örnekte, 2017 ayının ilk üç ayı için yıl, ay ve payment_type göre tarifeli havayolu miktarları gösterilmektedir.

> [!NOTE]
> Sunucusuz SQL havuzu sorgusu Hive/Hadoop bölümlendirme şeması ile uyumludur.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>Tür eşleme

Parquet için SQL Native Type denetim [türü eşlemesine](develop-openrowset.md#type-mapping-for-parquet)Parquet tür eşlemesi için.

## <a name="next-steps"></a>Sonraki adımlar

[Parquet iç içe türlerin nasıl sorgulanalınacağını](query-parquet-nested-types.md)öğrenmek için sonraki makaleye ilerleyin.
