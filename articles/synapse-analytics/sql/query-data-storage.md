---
title: Sunucusuz SQL havuzu ile veri depolamayı sorgulama
description: Bu makalede, Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu kaynağını kullanarak Azure Storage 'ın nasıl sorgulanyapılacağı açıklanır.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d299afca0bd8070a1da738e02812b64c41a7101c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675043"
---
# <a name="query-storage-files-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu ile depolama dosyalarını sorgulama

Sunucusuz SQL havuzu, Data Lake 'unuzdaki verileri sorgulamanızı sağlar. Yarı yapılandırılmış ve yapılandırılmamış veri sorgularına uyum sağlayan bir T-SQL sorgu yüzeyi alanı sunar. Sorgulamak için aşağıdaki T-SQL yönleri desteklenir:

- [SQL işlevlerinin ve işleçlerin](overview-features.md)büyük çoğunluğu dahil olmak üzere tam [seçim](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) yüzeyi alanı.
- SELECT ([Cetas](develop-tables-cetas.md)) olarak dış tablo oluşturma bir [dış tablo](develop-tables-external-tables.md) oluşturur ve ardından Transact-SQL SELECT ifadesinin sonuçlarını Azure Storage 'a aktarır.

Vs. ile ilgili daha fazla bilgi için, şu anda desteklenmeyen [sunucusuz SQL havuzuna genel bakış](on-demand-workspace-overview.md) makalesini veya aşağıdaki makaleleri okuyun:
- Depolama alanından veri okumak için [dış tablo](develop-tables-external-tables.md) ve [OPENROWSET](develop-openrowset.md) işlevinin nasıl kullanılacağını öğrenilebileceğiniz [depolama erişimi geliştirin](develop-storage-files-overview.md) .
- SYNAPSE SQL 'in SAS kimlik doğrulaması veya çalışma alanının yönetilen kimliğini kullanarak depolamaya erişmesini sağlayabileceğiniz, [depolama erişimini denetleyin](develop-storage-files-storage-access-control.md) .

## <a name="overview"></a>Genel Bakış

Azure depolama dosyalarında bulunan verilerin yerinde sorgulanmasında sorunsuz bir deneyim sağlamak için, sunucusuz SQL havuzu ek yetenekler ile [OPENROWSET](develop-openrowset.md) işlevini kullanır:

- [Birden çok dosyayı veya klasörü sorgulama](#query-multiple-files-or-folders)
- [PARQUET dosya biçimi](#query-parquet-files)
- [CSV ve sınırlandırılmış metni sorgulama (alan Sonlandırıcı, satır Sonlandırıcı, kaçış karakteri)](#query-csv-files)
- [Seçili sütunların bir alt kümesini okuyun](#read-a-chosen-subset-of-columns)
- [Şema çıkarımı](#schema-inference)
- [filename işlevi](#filename-function)
- [FilePath işlevi](#filepath-function)
- [Karmaşık türlerle ve iç içe veya yinelenen veri yapıları ile çalışma](#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="query-parquet-files"></a>Sorgu PARQUET dosyaları

Parquet kaynak verilerini sorgulamak için FORMAT = ' PARQUET ' kullanın

```syntaxsql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net//mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

Kullanım örnekleri için [sorgu Parquet dosyaları](query-parquet-files.md) makalesini gözden geçirin.

## <a name="query-csv-files"></a>CSV dosyalarını sorgulama

CSV kaynak verilerini sorgulamak için FORMAT = ' CSV ' kullanın. CSV dosyalarını Sorgulayabileceğiniz zaman, CSV dosyasının şemasını işlevin parçası olarak belirtebilirsiniz `OPENROWSET` :

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.csv', FORMAT = 'CSV', PARSER_VERSION='2.0') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

Özel CSv biçimine ayrıştırma kuralları ayarlamak için kullanılabilen bazı ek seçenekler vardır:
- ESCAPE_CHAR = ' Char ', dosyanın kendisini ve tüm sınırlayıcı değerlerini kaçış için kullanılan dosyadaki karakteri belirtir. Kaçış karakterinin kendisi dışında bir değer veya sınırlayıcı değerlerinden herhangi biri gelmesi durumunda, değer okunurken kaçış karakteri bırakılır.
ESCAPE_CHAR parametresi, FIELDQUOTE 'un etkin olup olmadığı veya etkinleştirilmediği için uygulanır. Tırnak işareti karakterini atlamak için kullanılmaz. Tırnak işareti karakteri başka bir tırnak işareti karakteriyle atlanmalıdır. Tırnak içine alma karakteri sütun değeri içinde, yalnızca değer tırnak içine alma karakterleriyle kapsüllense görünebilir.
- FIELDSONLANDıRıCı = ' field_terminator ' kullanılacak alan sonlandırıcıyı belirtir. Varsayılan alan Sonlandırıcı bir virgül ("**,**")
- ROWSONLANDıRıCı = ' row_terminator ' kullanılacak satır sonlandırıcıyı belirtir. Varsayılan satır Sonlandırıcı bir yeni satır karakteri: **\r\n**.

## <a name="file-schema"></a>Dosya şeması

SYNAPSE SQL 'de SQL Language, işlevin parçası olarak dosyanın şemasını tanımlamanızı `OPENROWSET` ve sütunların tümünü veya alt kümesini okuyabilmenizi ya da şema çıkarımı kullanarak dosyadan sütun türlerini otomatik olarak belirlemeyi dener.

### <a name="read-a-chosen-subset-of-columns"></a>Seçili sütunların bir alt kümesini okuyun

Okumak istediğiniz sütunları belirtmek için deyiminiz içinde isteğe bağlı bir WıTH yan tümcesi sağlayabilirsiniz `OPENROWSET` .

- CSV veri dosyaları varsa tüm sütunları okumak için sütun adlarını ve bunların veri türlerini belirtin. Sütunların bir alt kümesini isterseniz, kaynak veri dosyalarından sütunları sıralı olarak seçmek için sıralı numaraları kullanın. Sütunlar sıra atamağına göre bağlanacaktır.
- Parquet veri dosyaları varsa, kaynak veri dosyalarındaki sütun adlarıyla eşleşen sütun adları sağlayın. Sütunlar ada göre bağlanacaktır.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (
      C1 int, 
      C2 varchar(20),
      C3 varchar(max)
) as rows
```

Her sütun için, sütun adı ve Type ın yan tümcesini belirtmeniz gerekir `WITH` .
Örnekler için, [tüm sütunları BELIRTMEDEN CSV dosyalarını oku](query-single-csv-file.md#return-a-subset-of-columns)bölümüne bakın.

## <a name="schema-inference"></a>Şema çıkarımı

Deyimden WıTH yan tümcesini atlayarak `OPENROWSET` , hizmeti temel alınan dosyalardaki şemayı otomatik olarak algılamaya (çıkarması) söyleyebilirsiniz.

> [!NOTE]
> Bu şu anda yalnızca PARQUET dosya biçimi için geçerlidir.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
```

En iyi performans için, [uygun gösterilen veri türlerinin](best-practices-sql-on-demand.md#check-inferred-data-types) kullanıldığından emin olun. 

## <a name="query-multiple-files-or-folders"></a>Birden çok dosyayı veya klasörü sorgulama

Tek bir varlık veya satır kümesi olarak davranırken bir klasör veya klasör kümesi içindeki bir dosya kümesi üzerinde bir T-SQL sorgusu çalıştırmak için bir dosya veya klasör kümesi üzerinde bir klasöre veya bir modele (joker karakter kullanarak) bir yol sağlayın.

Aşağıdaki kurallar geçerlidir:

- Desenler, bir dizin yolunun veya dosya adının bir kısmında görünebilir.
- Aynı dizin adımında veya dosya adında çeşitli desenler görünebilir.
- Birden çok joker karakter varsa, tüm eşleşen yolların içindeki dosyalar elde edilen dosya kümesine dahil edilir.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/myroot/*/mysubfolder/*.parquet', FORMAT = 'PARQUET' ) as rows
```

Kullanım örnekleri için [sorgu klasörlerine ve birden çok dosyaya](query-folders-multiple-csv-files.md) bakın.

## <a name="file-metadata-functions"></a>Dosya meta verileri işlevleri

### <a name="filename-function"></a>Filename işlevi

Bu işlev, satırın kaynaklandığı dosya adını döndürür. 

Belirli dosyaları sorgulamak için, [dosyaya özel dosyaları sorgula](query-specific-files.md#filename) makalesindeki dosya adı bölümünü okuyun.

Dönüş veri türü nvarchar (1024). En iyi performans için dosya adı işlevinin sonucunu her zaman uygun veri türüne atayın. Karakter veri türü kullanırsanız, uygun uzunluğun kullanıldığından emin olun.

### <a name="filepath-function"></a>FilePath işlevi

Bu işlev, yolun tam yolunu veya bir parçasını döndürür:

- Parametresi olmadan çağrıldığında, bir satırın kaynaklandığı tam dosya yolunu döndürür.
- Parametresi ile çağrıldığında, parametresinde belirtilen konumda joker karakterle eşleşen yolun bir bölümünü döndürür. Örneğin, parametre değeri 1 ilk joker karakterle eşleşen yolun bir bölümünü döndürür.

Daha fazla bilgi için, [özel dosyaları sorgula](query-specific-files.md#filepath) makalesindeki FilePath bölümünü okuyun.

Dönüş veri türü nvarchar (1024). En iyi performans için, her zaman FilePath işlevinin sonucunu uygun veri türüne atayın. Karakter veri türü kullanırsanız, uygun uzunluğun kullanıldığından emin olun.

## <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Karmaşık türlerle ve iç içe veya yinelenen veri yapıları ile çalışma

İç içe geçmiş veya yinelenen veri türlerinde depolanan verilerle (örneğin, [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) dosyalarında) sorunsuz bir deneyim sağlamak için SUNUCUSUZ SQL havuzu, izleyen uzantıları eklemiştir.

#### <a name="project-nested-or-repeated-data"></a>Proje iç içe veya yinelenen veriler

Verileri proje için, iç içe geçmiş veri türlerinin sütunlarını içeren Parquet dosyası üzerinde bir SELECT ifadesini çalıştırın. Çıkışta, iç içe geçmiş değerler JSON 'a serileştirilir ve varchar (8000) SQL veri türü olarak döndürülür.

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Daha ayrıntılı bilgi için, [sorgu Parquet iç içe türler](query-parquet-nested-types.md#project-nested-or-repeated-data) makalesinin iç içe veya yinelenen veri bölümüne bakın.

#### <a name="access-elements-from-nested-columns"></a>İç içe geçmiş sütunlardan öğelere erişin

Yuvalanmış bir sütundan iç içe geçmiş öğelere (struct gibi) erişmek için, alan adlarını yola birleştirmek için "nokta gösterimi" kullanın. İşlevin WıTH yan tümcesinde column_name yolunu belirtin `OPENROWSET` .

Sözdizimi parçası örneği aşağıdaki gibidir:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Varsayılan olarak, `OPENROWSET` IşLEVI WITH yan tümcesinde belirtilen sütun adlarıyla kaynak alan adı ve yoluyla eşleşir. Aynı kaynak Parquet dosyası içindeki farklı iç içe düzeylerde bulunan öğelere WıTH yan tümcesi aracılığıyla erişilebilir.

**Dönüş değerleri**

- İşlev, Iç Içe geçmiş tür grubunda olmayan tüm Parquet türleri için, belirtilen öğeden ve belirtilen yoldaki int, Decimal ve varchar gibi bir skaler değer döndürür.
- Yol, Iç Içe geçmiş bir türün bir öğesini işaret ediyorsa, işlev belirtilen yoldaki en üstteki öğeden başlayarak bir JSON parçası döndürür. JSON parçası varchar (8000) türündedir.
- Özellik belirtilen column_name bulunamazsa, işlev bir hata döndürür.
- Özellik, [yol moduna](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE)bağlı olarak belirtilen column_path bulunamazsa, işlev katı modda veya LAX modundayken null olduğunda bir hata döndürür.

Sorgu örnekleri için, [sorgu Parquet iç içe türler](query-parquet-nested-types.md#read-properties-from-nested-object-columns) makalesindeki iç içe geçmiş sütunlarda bulunan erişim öğeleri bölümünü gözden geçirin.

#### <a name="access-elements-from-repeated-columns"></a>Yinelenen sütunlardan öğelerine erişin

Bir dizi veya haritanın bir öğesi gibi yinelenen bir sütundan öğelere erişmek için, proje yapmanız ve sağlamanız gereken her skalar öğe için [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) işlevini kullanın:

- İlk parametre olarak iç içe veya yinelenen sütun
- İkinci bir parametre olarak, erişim için öğe veya özellik belirten bir [JSON yolu](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true)

Yinelenen bir sütundan skalar olmayan öğelere erişmek için, proje yapmanız ve sağlamanız gereken skalar olmayan her öğe için [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest&preserve-view=true) işlevini kullanın:

- İlk parametre olarak iç içe veya yinelenen sütun
- İkinci bir parametre olarak, erişim için öğe veya özellik belirten bir [JSON yolu](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true)

Aşağıdaki sözdizimi parçasını inceleyin:

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

[Sorgu Parquet iç içe türler](query-parquet-nested-types.md#access-elements-from-repeated-columns) makalesindeki yinelenen sütunlardan öğelere erişmek için sorgu örnekleri bulabilirsiniz.

## <a name="query-samples"></a>Sorgu örnekleri

Örnek sorguları kullanarak çeşitli veri türlerini sorgulama hakkında daha fazla bilgi edinebilirsiniz.

### <a name="tools"></a>Araçlar

Sorgu vermek için gereken araçlar:
    - Azure Synapse Studio 
    - Azure Data Studio
    - SQL Server Management Studio

### <a name="demo-setup"></a>Tanıtım kurulumu

İlk adımınız sorguları yürütebileceğiniz **bir veritabanı oluşturmaktır** . Daha sonra bu veritabanında [kurulum betiğini](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) yürüterek nesneleri başlatacaksınız. 

Bu kurulum betiği veri kaynaklarını, veritabanı kapsamlı kimlik bilgilerini ve bu örneklerde verileri okumak için kullanılan harici dosya biçimlerini oluşturacaktır.

> [!NOTE]
> Veritabanları, gerçek veriler için değil yalnızca meta verileri görüntülemek için kullanılır.  Kullandığınız veritabanı adını, daha sonra ihtiyacınız olacak şekilde yazın.

```sql
CREATE DATABASE mydbname;
```

### <a name="provided-demo-data"></a>Sunulan tanıtım verileri

Demo verileri aşağıdaki veri kümelerini içerir:

- NYC TAXI-sarı TAXI kayıtları-CSV ve Parquet biçiminde ayarlanmış genel NYC veri kümesinin parçası
- CSV biçiminde doldurma verisi kümesi
- İç içe geçmiş sütunlara sahip örnek Parquet dosyaları
- JSON biçimindeki kitaplar

| Klasör yolu                                                  | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| CSV                                                        | CSV biçimindeki veriler için üst klasör                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Farklı CSV biçimlerinde popülasyon veri dosyaları içeren klasörler. |
| /CSV/Taxi/                                                   | CSV biçiminde NYC ortak veri dosyaları içeren klasör              |
| Parquet                                                    | Parquet biçimindeki verilerin üst klasörü                     |
| /Parquet/TAXI                                                | Iquet biçimindeki ıFC ortak veri dosyaları, Hive/Hadoop bölümlendirme şeması kullanılarak yıl ve aya göre bölümlendirilir. |
| /Parquet/Nested/                                             | İç içe geçmiş sütunlara sahip örnek Parquet dosyaları                     |
| nesnesinde                                                       | JSON biçimindeki veriler için üst klasör                        |
| /JSON/Books/                                                 | Kitap verileri içeren JSON dosyaları                                   |


## <a name="next-steps"></a>Sonraki adımlar

Farklı dosya türlerini sorgulama ve görünümleri oluşturma ve kullanma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [CSV dosyalarını sorgulama](query-single-csv-file.md)
- [Parquet dosyalarını sorgulama](query-parquet-files.md)
- [JSON dosyalarını sorgulama](query-json-files.md)
- [İç içe değerleri sorgulama](query-parquet-nested-types.md)
- [Sorgu klasörleri ve birden çok CSV dosyası](query-folders-multiple-csv-files.md)
- [Sorgularda dosya meta verilerini kullan](query-specific-files.md)
- [Görünümleri oluşturma ve kullanma](create-use-views.md)
