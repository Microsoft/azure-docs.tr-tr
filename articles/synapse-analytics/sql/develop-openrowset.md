---
title: Sunucusuz SQL havuzunda OPENROWSET kullanma
description: Bu makalede sunucusuz SQL havuzundaki OPENROWSET sözdizimi açıklanmakta ve bağımsız değişkenlerin nasıl kullanılacağı açıklanmaktadır.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 28c54865ab9c2876d998896f5f536a11088962f8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566435"
---
# <a name="how-to-use-openrowset-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu kullanarak OPENROWSET kullanma

`OPENROWSET(BULK...)`İşlevi, Azure Storage 'daki dosyalara erişmenizi sağlar. `OPENROWSET` işlev, uzak bir veri kaynağının (örneğin dosya) içeriğini okur ve içeriği bir dizi satır olarak döndürür. Sunucusuz SQL havuzu kaynağında, OPENROWSET işlevi çağırarak ve toplu seçenek belirtilerek OPENROWSET toplu satır kümesi sağlayıcısına erişilir.  

İşlevine, bir `OPENROWSET` `FROM` tablo adı gibi bir sorgunun yan tümcesinde başvurulabilir `OPENROWSET` . Bir dosyadaki verilerin bir satır kümesi olarak okunmasını ve döndürülmesini sağlayan yerleşik bir toplu sağlayıcı aracılığıyla toplu işlemleri destekler.

## <a name="data-source"></a>Veri kaynağı

SYNAPSE SQL içindeki OPENROWSET işlevi, bir veri kaynağından dosya içeriğini okur. Veri kaynağı bir Azure Storage hesabıdır ve işlevde açıkça başvurulabilir `OPENROWSET` ya da okumak istediğiniz dosyaların URL 'sinden dinamik olarak çıkarsanamıyor.
`OPENROWSET`İşlevi, isteğe bağlı olarak `DATA_SOURCE` , dosyaları içeren veri kaynağını belirtmek için bir parametre içerebilir.
- `OPENROWSET` olmadan `DATA_SOURCE` , bu dosya içeriğini seçenek olarak BELIRTILEN URL konumundan doğrudan okumak için kullanılabilir `BULK` :

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

Bu, önceden yapılandırma olmadan dosyaların içeriğini okumak için hızlı ve kolay bir yoldur. Bu seçenek, depolama alanına erişmek için temel kimlik doğrulama seçeneğini kullanmanıza olanak sağlar (Azure AD oturum açmaları için Azure AD geçişi ve SQL oturum açma için SAS belirteci). 

- `OPENROWSET``DATA_SOURCE`, ile belirtilen depolama hesabındaki dosyalara erişmek için kullanılabilir:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    Bu seçenek, veri kaynağındaki depolama hesabının konumunu yapılandırmanıza ve depolamaya erişmek için kullanılması gereken kimlik doğrulama yöntemini belirtmenize olanak sağlar. 
    
    > [!IMPORTANT]
    > `OPENROWSET` olmadan `DATA_SOURCE` , depolama dosyalarına erişmek için hızlı ve kolay bir yol sağlar, ancak sınırlı kimlik doğrulama seçenekleri sunar. Örnek olarak, Azure AD sorumluları yalnızca [Azure AD kimliklerini](develop-storage-files-storage-access-control.md?tabs=user-identity) veya genel kullanıma açık dosyaları kullanarak dosyalara erişebilir. Daha güçlü kimlik doğrulama seçeneklerine ihtiyacınız varsa, `DATA_SOURCE` seçeneğini kullanın ve depolama alanına erişmek için kullanmak istediğiniz kimlik bilgisini tanımlayın.


## <a name="security"></a>Güvenlik

Bir veritabanı kullanıcısının `ADMINISTER BULK OPERATIONS` işlevini kullanma izni olması gerekir `OPENROWSET` .

Depolama Yöneticisi ayrıca, bir kullanıcının geçerli SAS belirteci sağlayarak veya Azure AD sorumlusunu depolama dosyalarına erişmesine olanak tanıyarak dosyalara erişmesini sağlamalıdır. [Bu makaledeki](develop-storage-files-storage-access-control.md)depolama erişimi denetimi hakkında daha fazla bilgi edinin.

`OPENROWSET` depolama alanının kimliğini nasıl doğrulayacağınızı öğrenmek için aşağıdaki kuralları kullanın:
- `OPENROWSET` `DATA_SOURCE` Kimlik doğrulama mekanizması içinde, arayan türüne bağlıdır.
  - Herhangi bir Kullanıcı, `OPENROWSET` `DATA_SOURCE` Azure depolama 'da genel kullanıma açık dosyaları okumak zorunda kalmadan kullanabilir.
  - Azure depolama, Azure AD kullanıcısının temel dosyalara erişmesine izin veriyorsa (örneğin, çağıranın Azure Storage üzerinde izni varsa) Azure AD oturum açmaları, korunan dosyalara kendi [Azure AD kimliklerini](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) kullanarak erişebilirler `Storage Reader` .
  - SQL oturumları `OPENROWSET` `DATA_SOURCE` , genel kullanıma açık dosyalara erişmek için olmadan da KULLANABILIR, SAS belirteci kullanılarak korunan dosyalar veya SYNAPSE çalışma alanının yönetilen kimliği. Depolama dosyalarına erişim izni vermek için [sunucu kapsamlı kimlik bilgileri oluşturmanız](develop-storage-files-storage-access-control.md#examples) gerekir. 
- İçindeki `OPENROWSET` `DATA_SOURCE` kimlik doğrulama mekanizması, başvurulan veri kaynağına atanan veritabanı kapsamlı kimlik bilgileri içinde tanımlanmıştır. Bu seçenek, genel olarak kullanılabilir depolamaya erişmenizi veya SAS belirtecini, çalışma alanının yönetilen kimliğini veya [arayanın Azure AD kimliğini](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (arayan Azure AD sorumlusu ise) kullanarak depolamaya erişmenizi sağlar. `DATA_SOURCE`Ortak olmayan Azure Storage 'a başvuruyorsa, depolama dosyalarına erişime izin vermek için, [veritabanı kapsamlı kimlik bilgileri oluşturmanız](develop-storage-files-storage-access-control.md#examples) ve içinde başvurulmasına gerek duyarsınız `DATA SOURCE` .

Çağıranın, `REFERENCES` depolamada kimlik doğrulaması yapmak için kimlik bilgisi üzerinde izni olması gerekir.

## <a name="syntax"></a>Söz dizimi

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal' | 'json_path'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
[ , HEADER_ROW = { TRUE | FALSE } ]
[ , DATAFILETYPE = { 'char' | 'widechar' } ]
[ , CODEPAGE = { 'ACP' | 'OEM' | 'RAW' | 'code_page' } ]
```

## <a name="arguments"></a>Bağımsız değişkenler

Sorgulamak için hedef verileri içeren giriş dosyaları için iki seçeneğiniz vardır. Geçerli değerler:

- ' CSV '-satır/sütun ayırıcılarına sahip tüm sınırlandırılmış metin dosyalarını Içerir. Herhangi bir karakter, TSV: FIELDSONLANDıRıCı = sekmesi gibi bir alan ayırıcısı olarak kullanılabilir.

- ' PARQUET '-Parquet biçiminde Ikili dosya 

**' unstructured_data_path '**

Verilerin yolunu oluşturan unstructured_data_path mutlak veya göreli bir yol olabilir:
- ' \<prefix> ://' Biçimindeki mutlak yol, \<storage_account_path> / \<storage_path> bir kullanıcının dosyaları doğrudan okumasını sağlar.
- ' <storage_path> ' biçimindeki göreli yol, parametresiyle kullanılması gerekir `DATA_SOURCE` ve <storage_account_path> konum içindeki dosya modelini tanımlar `EXTERNAL DATA SOURCE` . 

Aşağıda, <storage account path> belirli dış veri kaynağınıza bağlanacak ilgili değerleri bulacaksınız. 

| Dış veri kaynağı       | Ön ek | Depolama hesabı yolu                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Depolama         | http [s]  | \<storage_account>. blob.core.windows.net/path/file   |
| Azure Blob Depolama         | fazla b [s]  | \<container>@\<storage_account>. blob.core.windows.net/path/file |
| Azure Data Lake Storage 1. Nesil | http [s]  | \<storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage 2. Nesil | http [s]  | \<storage_account>. dfs.core.windows.net/Path/File   |
| Azure Data Lake Storage 2. Nesil | ABFS [s]  | [\<file_system>@\<account_name>. dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

Depolama alanınızı, okumak istediğiniz klasörü veya dosyayı işaret eden bir yolu belirtir. Yol bir kapsayıcıya veya klasöre işaret ediyorsa, tüm dosyalar söz konusu kapsayıcı veya klasörden okunacaktır. Alt klasörlerdeki dosyalar dahil değildir. 

Birden çok dosya veya klasörü hedeflemek için joker karakterler kullanabilirsiniz. Ardışık olmayan birden çok joker karakter kullanımına izin verilir.
Aşağıda, */CSV/popülasyonu* ile başlayan tüm klasörlerden *popülasyon* ile başlayan tüm *CSV* dosyalarını okuyan bir örnek verilmiştir:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Unstructured_data_path bir klasör olacak şekilde belirtirseniz, sunucusuz bir SQL havuzu sorgusu bu klasörden dosyaları alacaktır. 

Yolun sonunda/* belirterek, daha az SQL havuzunun klasörlere geçiş yapmasını sağlayabilirsiniz: `https://sqlondemandstorage.blob.core.windows.net/csv/population/**`

> [!NOTE]
> Hadoop ve PolyBase 'in aksine, yolun sonunda/* * belirtmediğiniz müddetçe sunucusuz SQL havuzu alt klasör döndürmez. Yalnızca Hadoop ve PolyBase gibi, dosya adının altı çizili (_) veya nokta (.) ile başladığı dosyaları döndürmez.

Aşağıdaki örnekte, unstructured_data_path = ise `https://mystorageaccount.dfs.core.windows.net/webdata/` , sunucusuz BIR SQL havuzu sorgusu mydata.txt satırları döndürür. Bir alt klasörde bulunduğundan mydata2.txt ve mydata3.txt döndürmez.

![Dış tablolar için özyinelemeli veriler](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

WıTH yan tümcesi, dosyalardan okumak istediğiniz sütunları belirtmenize olanak tanır.

- CSV veri dosyaları için tüm sütunları okumak üzere sütun adlarını ve bunların veri türlerini belirtin. Sütunların bir alt kümesini isterseniz, kaynak veri dosyalarından sütunları sıralı olarak seçmek için sıralı numaraları kullanın. Sütunlar sıra atamağına göre bağlanacaktır. HEADER_ROW = TRUE kullanılırsa, sütun bağlama sıra konumu yerine sütun adı tarafından yapılır.
    > [!TIP]
    > CSV dosyaları için yan tümcesini de atlayabilirsiniz. Veri türleri, dosya içeriğinden otomatik olarak algılanır. HEADER_ROW bağımsız değişkenini kullanarak, sütun adlarının üst bilgi satırından okunacaktır. Ayrıntılar için [Otomatik şema bulmayı](#automatic-schema-discovery)denetleyin.
    
- Parquet veri dosyaları için, kaynak veri dosyalarındaki sütun adlarıyla eşleşen sütun adları sağlayın. Sütunlar ada göre bağlanacaktır ve büyük/küçük harfe duyarlıdır. WıTH yan tümcesi atlanırsa, Parquet dosyalarındaki tüm sütunlar döndürülür.
    > [!IMPORTANT]
    > Parquet dosyalarındaki sütun adları büyük/küçük harfe duyarlıdır. Parquet dosyasında sütun adından büyük küçük harfe sahip sütun adı belirtirseniz, bu sütun için NULL değerler döndürülür.


çıkış sütununun adı column_name. Sağlanmışsa, bu ad kaynak dosyada sütun adını ve varsa JSON yolunda belirtilen sütun adını geçersiz kılar. Json_path sağlanmazsa, otomatik olarak ' $ .column_name ' olarak eklenir. Davranış için json_path bağımsız değişkenini denetleyin.

column_type = çıkış sütunu için veri türü. Örtük veri türü dönüştürmesi burada gerçekleşmeyecektir.

column_ordinal = kaynak dosya (lar) daki sütunun sıra numarası. Bağlama ada göre yapıldığından, bu bağımsız değişken Parquet dosyaları için yok sayılır. Aşağıdaki örnek yalnızca bir CSV dosyasından ikinci bir sütun döndürür:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

json_path = sütuna veya iç içe özelliğe [JSON yol ifadesi](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) . Varsayılan [yol modu](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE) LAX 'tir.

> [!NOTE]
> Belirtilen yol yoksa, katı modda sorgu hata vererek başarısız olur. LAX modunda sorgu başarılı olur ve JSON yolu ifadesi NULL olarak değerlendirilir.

**\<bulk_options>**

FIELDSONLANDıRıCı = ' field_terminator '

Kullanılacak alan sonlandırıcıyı belirtir. Varsayılan alan Sonlandırıcı virgüldür ("**,**").

ROWSONLANDıRıCı = ' row_terminator ' '

Kullanılacak satır sonlandırıcıyı belirtir. Satır Sonlandırıcı belirtilmemişse, varsayılan Sonlandırıcıların biri kullanılacaktır. PARSER_VERSION = ' 1,0 ' için varsayılan sonlandırıcılar \r\n, \n ve \r. PARSER_VERSION = ' 2,0 ' için varsayılan sonlandırıcılar \r\n ve \n

ESCAPE_CHAR = ' Char '

Dosyanın kendisini ve dosyadaki tüm sınırlayıcı değerlerini kaçış için kullanılan karakteri belirtir. Kaçış karakterinin kendisi dışında bir değer veya sınırlayıcı değerlerinden herhangi biri gelmesi durumunda, değer okunurken kaçış karakteri bırakılır. 

ESCAPE_CHAR parametresi, FIELDQUOTE 'un etkin olup olmamasından bağımsız olarak uygulanır. Tırnak işareti karakterini atlamak için kullanılmaz. Tırnak işareti karakteri başka bir tırnak işareti karakteriyle atlanmalıdır. Tırnak içine alma karakteri sütun değeri içinde, yalnızca değer tırnak içine alma karakterleriyle kapsüllense görünebilir.

FIRSTROW = ' first_row ' 

Yüklenecek ilk satırın numarasını belirtir. Varsayılan değer 1 ' dir ve belirtilen veri dosyasındaki ilk satırı gösterir. Satır numaraları, satır sonlandırıcıları sayarak belirlenir. FIRSTROW 1 tabanlıdır.

FIELDQUOTE = ' field_quote ' 

CSV dosyasında tırnak karakteri olarak kullanılacak bir karakter belirtir. Belirtilmezse, tırnak karakteri (") kullanılır. 

DATA_COMPRESSION = ' data_compression_method '

Sıkıştırma yöntemini belirtir. Yalnızca PARSER_VERSION = ' 1.0 ' içinde desteklenir. Aşağıdaki sıkıştırma yöntemi destekleniyor:

- GZIP

PARSER_VERSION = ' parser_version '

Dosyalar okunurken kullanılacak ayrıştırıcı sürümünü belirtir. Şu anda desteklenen CSV ayrıştırıcı sürümleri 1,0 ve 2,0:

- PARSER_VERSION = ' 1,0 '
- PARSER_VERSION = ' 2,0 '

CSV Ayrıştırıcısı sürüm 1,0, varsayılan ve özellik açısından zengin bir özelliktir. Sürüm 2,0, performans için oluşturulmuştur ve tüm seçenekleri ve kodlamaları desteklemez. 

CSV Ayrıştırıcısı sürüm 1,0 özellikleri:

- Şu seçenekler desteklenmez: HEADER_ROW.

CSV Ayrıştırıcısı sürüm 2,0 özellikleri:

- Tüm veri türleri desteklenmez.
- Maksimum karakter sütunu uzunluğu 8000 ' dir.
- En büyük satır boyutu sınırı 8 MB 'tır.
- Şu seçenekler desteklenmez: DATA_COMPRESSION.
- Tırnak işareti boş dize ("") boş dize olarak yorumlanır.
- TARIH veri türü için desteklenen biçim: YYYY-AA-GG
- Saat veri türü için desteklenen biçim: ss: DD: SS [. kesirli saniye]
- DATETIME2 veri türü için desteklenen biçim: YYYY-AA-GG SS: DD: SS [. kesirli saniye]

HEADER_ROW = {TRUE | YANLÝÞ

CSV dosyasının üst bilgi satırını içerip içermediğini belirtir. Varsayılan değer FALSE 'dur. PARSER_VERSION = ' 2.0 ' içinde desteklenir. TRUE ise, ilk satırdan FIRSTROW bağımsız değişkenine göre sütun adları okunacaktır. TRUE ve Schema Ile kullanılarak belirtilirse, sütun adlarının bağlanması sıra konumlarına değil sütun adı tarafından yapılır.

DATAFILETYPE = {' Char ' | ' widechar '}

Kodlamayı belirtir: bir karakter UTF8 için kullanılır, UTF16 dosyaları için geniş karakter kullanılır.

CODEPAGE = {' ACP ' | ' OEM ' | ' RAW ' | ' code_page '}

Veri dosyasındaki verilerin kod sayfasını belirtir. Varsayılan değer 65001 ' dir (UTF-8 kodlaması). Bu seçenekle ilgili daha fazla ayrıntı için [buradaki](/sql/t-sql/functions/openrowset-transact-sql?view=sql-server-ver15&preserve-view=true#codepage)adımları inceleyin.

## <a name="fast-delimited-text-parsing"></a>Hızlı sınırlandırılmış metin ayrıştırma

Kullanabileceğiniz iki sınırlı metin ayrıştırıcısı sürümü vardır. CSV Ayrıştırıcısı sürüm 1,0, çözümleyici sürüm 2,0 performans için yapılandırıldığında varsayılan ve özellik bakımından zengin bir özelliktir. Ayrıştırıcı 2,0 ' de performans iyileştirmesi gelişmiş Ayrıştırma tekniklerinden ve çoklu iş parçacığından gelir. Dosya boyutu büyüdükçe hızdaki fark daha büyük olacaktır.

## <a name="automatic-schema-discovery"></a>Otomatik şema bulma

WıTH yan tümcesini atlayarak hem CSV hem de Parquet dosyalarını, şemayı bilmeden veya belirtmeden kolayca sorgulayabilirsiniz. Sütun adları ve veri türleri dosyalardan çıkarsdolacak.

Parquet dosyalarında okunacak sütun meta verileri, tür eşlemeleri de [Parquet için tür eşlemelerde](#type-mapping-for-parquet)bulunabilir. Örnekler için [şema belirtmeden Parquet dosyalarını okumayı](#read-parquet-files-without-specifying-schema) denetleyin.

CSV dosyaları için sütun adları, üst bilgi satırından okunabilir. Üst bilgi satırının HEADER_ROW bağımsız değişkenini kullanarak mevcut olup olmadığını belirtebilirsiniz. HEADER_ROW = FALSE ise, genel sütun adları kullanılacaktır: C1, C2,... CN burada n, dosyadaki sütun sayısıdır. Veri türleri ilk 100 veri satırlarından çıkarsedilir. Örnekler için [şema BELIRTMEDEN CSV dosyaları okumayı](#read-csv-files-without-specifying-schema) denetleyin.

> [!IMPORTANT]
> Bunun yerine uygun veri türü çıkarsanamıyor ve daha büyük veri türü kullanılacağı durumlar vardır. Bu performans yükünü taşır ve varchar (8000) olarak gösterilen karakter sütunları için özellikle önemlidir. En iyi performansı elde etmek için lütfen [gösterilen veri türlerini kontrol](best-practices-sql-on-demand.md#check-inferred-data-types) edin ve [uygun veri türlerini kullanın](best-practices-sql-on-demand.md#use-appropriate-data-types).

### <a name="type-mapping-for-parquet"></a>Parquet için tür eşleme

Parquet dosyaları her sütun için tür açıklamalarını içerir. Aşağıdaki tabloda, Parquet türlerinin SQL yerel türleriyle nasıl eşlendiği açıklanmaktadır.

| Parquet türü | Parquet mantıksal türü (ek açıklama) | SQL veri türü |
| --- | --- | --- |
| BOOLEAN | | bit |
| IKILI/BYTE_ARRAY | | ikili |
| ÇIFT | | float |
| FLOAT | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |ikili |
| Ý |UTF8 |varchar \* (UTF8 harmanlama) |
| Ý |DIZISINDE |varchar \* (UTF8 harmanlama) |
| Ý |YARDıMıNıN|varchar \* (UTF8 harmanlama) |
| FIXED_LEN_BYTE_ARRAY |EDIN |uniqueidentifier |
| Ý |KATEGORI |decimal |
| Ý |JSON |varchar (8000) \* (UTF8 harmanlama) |
| Ý |BSON | Desteklenmez |
| FIXED_LEN_BYTE_ARRAY |KATEGORI |decimal |
| BYTE_ARRAY |ARALıĞıNDA | Desteklenmez |
| INT32 |TAMSAYı (8, doğru) |smallint |
| INT32 |INT (16, doğru) |smallint |
| INT32 |INT (32, doğru) |int |
| INT32 |INT (8, false) |tinyint |
| INT32 |INT (16, false) |int |
| INT32 |INT (32, false) |bigint |
| INT32 |DATE |date |
| INT32 |KATEGORI |decimal |
| INT32 |SAAT (MILIMETRE)|time |
| INT64 |INT (64, true) |bigint |
| INT64 |INT (64, false) |ondalık (20, 0) |
| INT64 |KATEGORI |decimal |
| INT64 |SAAT (MIKRO SN) |zaman-saat (NANOS) desteklenmiyor |
|INT64 |ZAMAN DAMGASı (MILIMETRE/MIKRO SN) |datetime2-TIMESTAMP (NANOS) desteklenmiyor |
|[Karmaşık tür](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |LISTELE |JSON ile seri hale getirilmiş varchar (8000) |
|[Karmaşık tür](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|HARITA|JSON ile seri hale getirilmiş varchar (8000) |

## <a name="examples"></a>Örnekler

### <a name="read-csv-files-without-specifying-schema"></a>Şemayı belirtmeden CSV dosyalarını okuma

Aşağıdaki örnek, sütun adları ve veri türleri belirtmeden üst bilgi satırını içeren CSV dosyasını okur: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE) as [r]
```

Aşağıdaki örnek, sütun adları ve veri türleri belirtmeden üst bilgi satırı içermeyen CSV dosyasını okur: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0') as [r]
```

### <a name="read-parquet-files-without-specifying-schema"></a>Şema belirtmeden Parquet dosyalarını okuma

Aşağıdaki örnek, görselleştirmenizdeki veri kümesindeki ilk satırın tüm sütunlarını, Parquet biçiminde ve sütun adlarını ve veri türlerini belirtmeden döndürür: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

### <a name="read-specific-columns-from-csv-file"></a>CSV dosyasından belirli sütunları oku

Aşağıdaki örnek, population*. csv dosyalarından yalnızca 1 ve 4 sıralı numaralarına sahip iki sütun döndürür. Dosyalarda üstbilgi satırı olmadığından, ilk satırdan okumaya başlar:

```sql
SELECT 
    * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```

### <a name="read-specific-columns-from-parquet-file"></a>Parquet dosyasından belirli sütunları okuma

Aşağıdaki örnek, Parquet biçiminde görselleştirmenizdeki veri kümesindeki ilk satırın yalnızca iki sütununu döndürür: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    [stateName] VARCHAR (50),
    [population] bigint
) AS [r]
```

### <a name="specify-columns-using-json-paths"></a>JSON yollarını kullanarak sütunları belirtme

Aşağıdaki örnek, WıTH yan tümcesinde [JSON yol ifadelerini](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) nasıl kullanabileceğinizi gösterir ve katı ve LAX yol modları arasındaki farkı gösterir: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    --lax path mode samples
    [stateName] VARCHAR (50), -- this one works as column name casing is valid - it targets the same column as the next one
    [stateName_explicit_path] VARCHAR (50) '$.stateName', -- this one works as column name casing is valid
    [COUNTYNAME] VARCHAR (50), -- STATEname column will contain NULLs only because of wrong casing - it targets the same column as the next one
    [countyName_explicit_path] VARCHAR (50) '$.COUNTYNAME', -- STATEname column will contain NULLS only because of wrong casing and default path mode being lax

    --strict path mode samples
    [population] bigint 'strict $.population' -- this one works as column name casing is valid
    --,[population2] bigint 'strict $.POPULATION' -- this one fails because of wrong casing and strict path mode
)
AS [r]
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla örnek için bkz [](query-data-storage.md) `OPENROWSET` . [CSV](query-single-csv-file.md), [Parquet](query-parquet-files.md)ve [JSON](query-json-files.md) dosya biçimlerini okumak için nasıl kullanacağınızı öğrenmek için Query Data Storage hızlı başlangıcı. En iyi performansı elde etmek için [en iyi uygulamaları](best-practices-sql-on-demand.md) denetleyin. Ayrıca, [Cetas](develop-tables-cetas.md)kullanarak sorgunuzun sonuçlarını Azure depolama 'ya kaydetmeyi de öğrenebilirsiniz.