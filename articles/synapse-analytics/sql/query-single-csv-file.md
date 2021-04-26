---
title: Sunucusuz SQL havuzu kullanarak CSV dosyalarını sorgulama
description: Bu makalede, sunucusuz SQL havuzu kullanarak farklı dosya biçimlerine sahip tek CSV dosyalarını sorgulamayı öğreneceksiniz.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: f2f0cdf307e91fb40c55d4a98139bad1a5eca886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462592"
---
# <a name="query-csv-files"></a>CSV dosyalarını sorgulama

Bu makalede, Azure SYNAPSE Analytics 'te sunucusuz SQL havuzunu kullanarak tek bir CSV dosyasını sorgulamayı öğreneceksiniz. CSV dosyaları farklı biçimlere sahip olabilir: 

- Üst bilgi satırı ile ve olmadan
- Virgül ve sekmeyle ayrılmış değerler
- Windows ve UNIX stil satırı sonları
- Tırnak işaretleri olmayan ve tırnak içine alınmış değerler ve kaçış karakterleri

Yukarıdaki tüm Çeşitlemeler aşağıda ele alınacaktır.

## <a name="quickstart-example"></a>Hızlı başlangıç örneği

`OPENROWSET` işlevi, dosyanıza URL ekleyerek CSV dosyasının içeriğini okumanızı sağlar.

### <a name="read-a-csv-file"></a>CSV dosyası okuma

Dosyanızın içeriğini görmenin en kolay yolu, `CSV` işlev için dosya URL 'si sağlamaktır `OPENROWSET` , csv ve 2,0 ' i belirtmektir `FORMAT` `PARSER_VERSION` . Dosya herkese açık ise veya Azure AD kimliğiniz bu dosyaya erişebilirse, aşağıdaki örnekte gösterildiği gibi sorguyu kullanarak dosyanın içeriğini görmeniz gerekir:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    format = 'csv',
    parser_version = '2.0',
    firstrow = 2 ) as rows
```

`firstrow`Bu durumda üstbilgiyi temsil eden CSV dosyasındaki ilk satırı atlamak için seçeneği kullanılır. Bu dosyaya erişebildiğinizden emin olun. Dosyanız SAS anahtarı veya özel kimlikle korunuyorsa, [SQL oturum açma için sunucu düzeyi kimlik bilgisi](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)kurulumunu yapmanız gerekir.

> [!IMPORTANT]
> CSV dosyanız UTF-8 karakterleri içeriyorsa, UTF-8 veritabanı harmanlaması (örneğin) kullandığınızdan emin olun `Latin1_General_100_CI_AS_SC_UTF8` .
> Dosyadaki metin kodlaması arasında uyuşmazlık var ve harmanlama beklenmeyen dönüştürme hatalarına neden olabilir.
> Aşağıdaki T-SQL ifadesini kullanarak geçerli veritabanının varsayılan harmanlamasını kolayca değiştirebilirsiniz: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

### <a name="data-source-usage"></a>Veri kaynağı kullanımı

Önceki örnek, dosyanın tam yolunu kullanır. Alternatif olarak, depolama alanının kök klasörünü işaret eden konum ile bir dış veri kaynağı oluşturabilirsiniz:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
```

Bir veri kaynağı oluşturduktan sonra, bu veri kaynağını ve işlevindeki dosyanın göreli yolunu kullanabilirsiniz `OPENROWSET` :

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) as rows
```

Bir veri kaynağı SAS anahtarı veya özel kimlikle korunuyorsa, [veri kaynağını veritabanı kapsamlı kimlik](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)bilgileriyle yapılandırabilirsiniz.

### <a name="explicitly-specify-schema"></a>Açıkça şema belirt

`OPENROWSET` dosya kullanarak dosyadan okumak istediğiniz sütunları açıkça belirtmenize olanak sağlar `WITH` :

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) with (
        date_rep date 1,
        cases int 5,
        geo_id varchar(6) 8
    ) as rows
```

Tümce içindeki bir veri türünden sonraki sayılar `WITH` CSV dosyasındaki sütun dizinini temsil eder.

> [!IMPORTANT]
> CSV dosyanız UTF-8 karakterleri içeriyorsa, `Latin1_General_100_CI_AS_SC_UTF8` yan tümcedeki tüm sütunlar IÇIN UTF-8 harmanlaması (örneğin) `WITH` veya veritabanı DÜZEYINDE bazı UTF-8 harmanlamasını belirtdiğinizden emin olun.
> Dosyadaki metin kodlaması arasında uyuşmazlık var ve harmanlama beklenmeyen dönüştürme hatalarına neden olabilir.
> Aşağıdaki T-SQL ifadesini kullanarak geçerli veritabanının varsayılan harmanlamasını kolayca değiştirebilirsiniz: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`
> Aşağıdaki tanımı kullanarak Sütu türlerinde harmanlamayı kolayca ayarlayabilirsiniz: `geo_id varchar(6) collate Latin1_General_100_CI_AI_SC_UTF8 8`

Aşağıdaki bölümlerde, çeşitli CSV dosyası türlerini sorgulama hakkında bilgi alabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

İlk adımınız tabloların oluşturulacağı **bir veritabanı oluşturmaktır** . Sonra bu veritabanında [kurulum betiğini](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) yürüterek nesneleri başlatın. Bu kurulum betiği, veri kaynaklarını, veritabanı kapsamlı kimlik bilgilerini ve bu örneklerde kullanılan harici dosya biçimlerini oluşturacaktır.

## <a name="windows-style-new-line"></a>Windows stili yeni satır

Aşağıdaki sorgu, Windows stili yeni bir satırla ve virgülle ayrılmış sütunlarla bir başlık satırı olmadan bir CSV dosyasının nasıl okunacağını gösterir.

Dosya önizlemesi:

![Üst bilgi içermeyen CSV dosyasının ilk 10 satırı, Windows stili yeni satır.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>UNIX stili yeni satır

Aşağıdaki sorgu, bir üst bilgi satırı olmadan bir dosyanın nasıl okunacağını, UNIX stili yeni bir satırla ve virgülle sınırlandırılmış sütunlara gösterir. Diğer örneklerle karşılaştırıldığında dosyanın farklı konumunu aklınızda yapın.

Dosya önizlemesi:

![Üst bilgi satırı olmayan ve yeni Unix-Style satır içeren CSV dosyasının ilk 10 satırı.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Üst bilgi satırı

Aşağıdaki sorgu, bir üst bilgi satırı olan bir okuma dosyasının, UNIX stili yeni bir satırla ve virgülle ayrılmış sütunlarda nasıl yapılacağını gösterir. Diğer örneklerle karşılaştırıldığında dosyanın farklı konumunu aklınızda yapın.

Dosya önizlemesi:

![Üst bilgi satırı ve Unix-Style yeni satırla CSV dosyasının ilk 10 satırı.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Özel tırnak karakteri

Aşağıdaki sorgu, bir üst bilgi satırıyla bir dosyanın nasıl okunacağını, UNIX stili yeni satır, virgülle ayrılmış sütunlar ve tırnak içine alınmış değerlerle gösterir. Diğer örneklerle karşılaştırıldığında dosyanın farklı konumunu aklınızda yapın.

Dosya önizlemesi:

![Üst bilgi satırı ve Unix-Style yeni satır ve tırnak işaretli değerler içeren CSV dosyasının ilk 10 satırı.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> FIELDQUOTE için varsayılan değer bir çift tırnak olduğundan, bu sorgu, FIELDQUOTE parametresini atlarsanız aynı sonuçları döndürür.

## <a name="escape-characters"></a>Kaçış karakterleri

Aşağıdaki sorgu, bir üst bilgi satırıyla bir dosyanın nasıl okunacağını, UNIX stili yeni bir satır, virgülle ayrılmış sütunlar ve değer içinde alan sınırlayıcısı (virgül) için kullanılan kaçış karakteri gösterir. Diğer örneklerle karşılaştırıldığında dosyanın farklı konumunu aklınızda yapın.

Dosya önizlemesi:

![Üst bilgi satırı ile CSV dosyasının ilk 10 satırı ve alan sınırlayıcısı için kullanılan yeni satır ve çıkış karakteri Unix-Style.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> "Slov, enıa" içindeki virgül, ülke/bölge adının bir parçası yerine alan sınırlayıcısı olarak değerlendirildiğinden, bu sorgu, ESCAPECHAR belirtilmemişse başarısız olur. "Slov, Enia" iki sütun olarak değerlendirilir. Bu nedenle, belirli satırda diğer satırlardan daha fazla bir sütun ve WıTH yan tümcesinde tanımladığınız bir sütun daha vardır.

### <a name="escape-quoting-characters"></a>Tırnak işareti karakterlerini kaçış

Aşağıdaki sorgu, bir üst bilgi satırıyla bir dosyanın nasıl okunacağını, UNIX stili yeni bir satır, virgülle ayrılmış sütunlar ve değerler içindeki bir kaçışlı çift tırnak karakteri gösterir. Diğer örneklerle karşılaştırıldığında dosyanın farklı konumunu aklınızda yapın.

Dosya önizlemesi:

![Aşağıdaki sorgu, bir üst bilgi satırıyla bir dosyanın nasıl okunacağını, UNIX stili yeni bir satır, virgülle ayrılmış sütunlar ve değerler içindeki bir kaçışlı çift tırnak karakteri gösterir.](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Tırnak işareti karakteri başka bir tırnak işareti karakteriyle atlanmalıdır. Tırnak içine alma karakteri sütun değeri içinde, yalnızca değer tırnak içine alma karakterleriyle kapsüllense görünebilir.

## <a name="tab-delimited-files"></a>Sekmeyle ayrılmış dosyalar

Aşağıdaki sorgu, bir dosyanın bir başlık satırı ile, UNIX stili yeni bir satırla ve sekmeyle ayrılmış sütunlarla nasıl okunacağını gösterir. Diğer örneklerle karşılaştırıldığında dosyanın farklı konumunu aklınızda yapın.

Dosya önizlemesi:

![Üst bilgi satırı ve Unix-Style yeni çizgi ve sekme sınırlayıcısı ile CSV dosyasının ilk 10 satırı.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="return-a-subset-of-columns"></a>Sütunların bir alt kümesini döndürür

Şimdiye kadar, Ile kullanarak CSV dosya şemasını belirttiniz ve tüm sütunları listelediyseniz. Sorgunuz için gereken sütunları yalnızca gerekli her sütun için bir sıra numarası kullanarak belirtebilirsiniz. İlgilendiğiniz sütunları da atlayabilirsiniz.

Aşağıdaki sorgu, yalnızca gereken sütunları belirterek bir dosyadaki farklı ülke/bölge adlarının sayısını döndürür:

> [!NOTE]
> Aşağıdaki sorgudaki WıTH yan tümcesine göz atın ve *[country_name]* sütununu tanımladığınız satırın sonunda "2" (tırnak işareti olmadan) olduğunu unutmayın. Bu, *[country_name]* sütununun dosyadaki ikinci sütun olduğu anlamına gelir. Sorgu, ikinci tane hariç dosyadaki tüm sütunları yoksayar.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Sonraki adımlar

Sonraki makalelerde şunları nasıl kullanacağınız gösterilmektedir:

- [Parquet dosyaları sorgulanıyor](query-parquet-files.md)
- [Klasörleri ve birden çok dosyayı sorgulama](query-folders-multiple-csv-files.md)
