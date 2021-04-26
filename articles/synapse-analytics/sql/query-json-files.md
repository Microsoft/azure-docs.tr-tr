---
title: Sunucusuz SQL havuzu kullanarak JSON dosyalarını sorgulama
description: Bu bölümde, Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu kullanılarak JSON dosyalarının nasıl okunacağı açıklanmaktadır.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 5fcf688bbe8a5be2fc10b70950990b7b6ca71df8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225600"
---
# <a name="query-json-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te sunucusuz SQL havuzunu kullanarak JSON dosyalarını sorgulama

Bu makalede, Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu kullanarak bir sorgu yazmayı öğreneceksiniz. Sorgunun hedefi, [OPENROWSET](develop-openrowset.md)kullanarak JSON dosyalarını okumalıdır. 
- Birden çok JSON belgelerinin JSON dizisi olarak depolandığı standart JSON dosyaları.
- JSON belgelerinin yeni satır karakteriyle ayrıldığı, satır ile ayrılmış JSON dosyaları. Bu dosya türleri için ortak uzantılar `jsonl` , ve ' dir `ldjson` `ndjson` .

## <a name="read-json-documents"></a>JSON belgelerini oku

JSON dosyanızın içeriğini görmenin en kolay yolu, işleve dosya URL 'sini sağlamak `OPENROWSET` , CSV `FORMAT` 'yi belirtmek ve ve değerlerini ayarlamak içindir `0x0b` `fieldterminator` `fieldquote` . Satır için ayrılmış JSON dosyalarını okumanız gerekiyorsa, bu yeterli olur. Klasik JSON dosyanız varsa, değerlerini ayarlamanız gerekir `0x0b` `rowterminator` . `OPENROWSET` işlev, JSON 'ı ayrıştırır ve her belgeyi aşağıdaki biçimde döndürür:

| Belg |
| --- |
|{"date_rep": "2020-07-24", "gün": 24, "ay": 7, "Year": 2020, "durumlar": 3, "deecon": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-25", "gün": 25, "ay": 7, "Year": 2020, "durumlar": 7, "deaaltı": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-26", "gün": 26, "ay": 7, "Year": 2020, "durumlar": 4, "deecon": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-27", "gün": 27, "ay": 7, "Year": 2020, "durumlar": 8, "deecon": 0, "geo_id": "AF"}|

Dosya herkese açık ise veya Azure AD kimliğiniz bu dosyaya erişebilirse, aşağıdaki örneklerde gösterildiği gibi sorguyu kullanarak dosyanın içeriğini görmeniz gerekir.

### <a name="read-json-files"></a>JSON dosyalarını okuma

Aşağıdaki örnek sorgu JSON ve satır ile ayrılmış JSON dosyalarını okur ve her belgeyi ayrı bir satır olarak döndürür.

```sql
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Önceki örnek sorgudaki JSON belgesi bir nesne dizisi içerir. Sorgu, her nesneyi sonuç kümesinde ayrı bir satır olarak döndürür. Bu dosyaya erişebildiğinizden emin olun. Dosyanız SAS anahtarı veya özel kimlikle korunuyorsa [SQL oturum açma için sunucu düzeyi kimlik bilgilerini](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)ayarlamanız gerekir. 

### <a name="data-source-usage"></a>Veri kaynağı kullanımı

Önceki örnek, dosyanın tam yolunu kullanır. Alternatif olarak, depolama alanının kök klasörünü işaret eden konum ile bir dış veri kaynağı oluşturabilir ve bu veri kaynağını ve işlevdeki dosyanın göreli yolunu kullanabilirsiniz `OPENROWSET` :

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.json',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Bir veri kaynağı SAS anahtarı veya özel kimlikle korunuyorsa, [veri kaynağını veritabanı kapsamlı kimlik](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)bilgileriyle yapılandırabilirsiniz.

Aşağıdaki bölümlerde, çeşitli türlerdeki JSON dosyalarını sorgulama hakkında bilgi alabilirsiniz.

## <a name="parse-json-documents"></a>JSON belgelerini ayrıştırma

Önceki örneklerde bulunan sorgular, her JSON belgesini, sonuç kümesinin ayrı bir satırına tek bir dize olarak döndürür. `JSON_VALUE` `OPENJSON` Aşağıdaki örnekte gösterildiği gibi, IŞLEVLERI kullanarak JSON belgelerindeki değerleri ayrıştırır ve ilişkisel değerler olarak döndürebilirsiniz:

| Tarih \_ temsilcisi | çalışmaların | coğrafi bölge \_ kimliği |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>Örnek JSON belgesi

Sorgu örnekleri aşağıdaki yapıyla belge içeren *JSON* dosyalarını okur:

```json
{
    "date_rep":"2020-07-24",
    "day":24,"month":7,"year":2020,
    "cases":13,"deaths":0,
    "countries_and_territories":"Afghanistan",
    "geo_id":"AF",
    "country_territory_code":"AFG",
    "continent_exp":"Asia",
    "load_date":"2020-07-25 00:05:14",
    "iso_country":"AF"
}
```

> [!NOTE]
> Bu belgeler satır sınırlı JSON olarak depolanıyorsa, `FIELDTERMINATOR` ve 0x0B 'yi ayarlamanız gerekir `FIELDQUOTE` . Standart JSON formata sahipseniz, `ROWTERMINATOR` 0x0B olarak ayarlamanız gerekir.

### <a name="query-json-files-using-json_value"></a>JSON_VALUE kullanarak JSON dosyalarını sorgulama

Aşağıdaki sorgu, bir JSON belgelerinden skaler değerler [](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) ( `date_rep` ,,) almak için JSON_VALUE nasıl kullanacağınızı gösterir `countries_and_territories` `cases` :

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    CAST(JSON_VALUE(doc, '$.deaths') AS INT) as fatal,
    JSON_VALUE(doc, '$.cases') as cases,
    doc
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
order by JSON_VALUE(doc, '$.geo_id') desc
```

JSON belgesinden JSON özelliklerini ayıkladıktan sonra, sütun diğer adlarını tanımlayabilir ve isteğe bağlı olarak metinsel değerini bir tür olarak çevirebilirsiniz.

### <a name="query-json-files-using-openjson"></a>OPENJSON kullanarak JSON dosyalarını sorgulama

Aşağıdaki sorgu [Openjson](/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest&preserve-view=true)kullanır. Sırbistan tarafından bildirilen COVıD istatistiklerini alır:

```sql
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
where country = 'Serbia'
order by country, date_rep desc;
```
Sonuçlar işlev kullanılarak döndürülen sonuçlarla aynıdır `JSON_VALUE` . Bazı durumlarda, bundan `OPENJSON` faydalanabilir `JSON_VALUE` :
- `WITH`Yan tümcesinde, her özellik için sütun diğer adlarını ve türlerini açıkça ayarlayabilirsiniz. `CAST`İşlevi, listedeki her sütuna yerleştirmeniz gerekmez `SELECT` .
- `OPENJSON` çok sayıda özellik döndürüuyorsanız daha hızlı olabilir. Yalnızca 1-2 özellikleri `OPENJSON` döndürülülüğünüz, işlev ek yük olabilir.
- `OPENJSON`Her belgeden diziyi ayrıştırabilmeniz ve üst satırla birleştirmek istiyorsanız işlevini kullanmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Bu serinin sonraki makalelerinde nasıl yapılacağı gösterilmektedir:

- [Klasörleri ve birden çok dosyayı sorgulama](query-folders-multiple-csv-files.md)
- [Görünümleri oluşturma ve kullanma](create-use-views.md)
