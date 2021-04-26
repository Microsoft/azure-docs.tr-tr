---
title: Azure SYNAPSE link 'te sunucusuz SQL havuzu kullanarak verileri Azure Cosmos DB sorgulama
description: Bu makalede, Azure SYNAPSE link 'te sunucusuz bir SQL havuzu kullanarak Azure Cosmos DB sorgulama hakkında bilgi edineceksiniz.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 03/02/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 64a112fd29ee9e3fbb82d9b54322415569b3ff85
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209545"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link"></a>Azure SYNAPSE link 'te sunucusuz SQL havuzu ile Azure Cosmos DB verileri sorgulama

Sunucusuz bir SQL havuzu, işlem iş yüklerinizin performansını etkilemeden neredeyse gerçek zamanlı olarak [Azure SYNAPSE bağlantısı](../../cosmos-db/synapse-link.md) ile etkinleştirilen Azure Cosmos DB kapsayıcılarınızdaki verileri analiz etmenize olanak tanır. [Analitik depodan](../../cosmos-db/analytical-store-introduction.md) verileri sorgulamak ve t-SQL arabirimi aracılığıyla çok çeşitli iş ZEKASı (BI) ve geçici sorgulama araçlarıyla tümleşik bağlantıyı sorgulamak için tanıdık bir T-SQL söz dizimi sunar.

Azure Cosmos DB sorgulamak için, tam [seçim](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) yüzeyi alanı [SQL işlevlerinin ve işleçlerin](overview-features.md)büyük çoğunluğunu içeren [OPENROWSET](develop-openrowset.md) işlevi aracılığıyla desteklenir. Ayrıca, Azure Blob Azure Data Lake Storage depolama alanındaki verilerle birlikte Azure Cosmos DB verileri okuyan sorgunun sonuçlarını, Select (Cetas) [olarak dış tablo oluştur](develop-tables-cetas.md#cetas-in-serverless-sql-pool) ' u kullanarak da saklayabilirsiniz. Şu anda, CETAS kullanarak Azure Cosmos DB sunucusuz SQL havuzu sorgu sonuçlarını depolayamaz.

Bu makalede, Azure SYNAPSE bağlantısı ile etkinleştirilen Azure Cosmos DB kapsayıcılardan veri sorgulayan sunucusuz bir SQL havuzuyla bir sorgu yazmayı öğreneceksiniz. Daha sonra, Azure Cosmos DB kapsayıcıları üzerinde sunucusuz SQL havuzu görünümleri oluşturma ve bunları [Bu öğreticideki](./tutorial-data-analyst.md)Power BI modellerine bağlama hakkında daha fazla bilgi edinebilirsiniz. Bu öğretici, [Azure Cosmos DB iyi tanımlanmış bir şemaya](../../cosmos-db/analytical-store-introduction.md#schema-representation)sahip bir kapsayıcı kullanır.

## <a name="overview"></a>Genel Bakış

Sunucusuz SQL havuzu, işlevi kullanarak analitik depolamayı Azure Cosmos DB sorgulamanızı sağlar `OPENROWSET` . 
- `OPENROWSET` satır içi anahtar ile. Bu sözdizimi, kimlik bilgileri hazırlanmasına gerek kalmadan Azure Cosmos DB koleksiyonlarını sorgulamak için kullanılabilir.
- `OPENROWSET` Cosmos DB hesap anahtarını içeren bu kimlik bilgilerine başvuruluyor. Bu söz dizimi, Azure Cosmos DB koleksiyonlarında görünümler oluşturmak için kullanılabilir.

### <a name="openrowset-with-key"></a>[Anahtarla OPENROWSET](#tab/openrowset-key)

Azure Cosmos DB analitik depodaki verileri sorgulamayı ve çözümlemeyi desteklemek için sunucusuz bir SQL havuzu kullanılır. Sunucusuz SQL havuzu `OPENROWSET` SQL sözdizimini kullanır, bu nedenle öncelikle Azure Cosmos DB Bağlantı dizenizi bu biçime dönüştürmeniz gerekir:

```sql
OPENROWSET( 
       'CosmosDB',
       '<SQL connection string for Azure Cosmos DB>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Azure Cosmos DB için SQL bağlantı dizesi Azure Cosmos DB hesap adı, veritabanı adı, veritabanı hesabı ana anahtarı ve işleve isteğe bağlı bir bölge adı belirtir `OPENROWSET` . Bu bilgilerden bazıları standart Azure Cosmos DB bağlantı dizesinden alınabilir.

Standart Azure Cosmos DB bağlantı dizesi biçiminden dönüştürme:

```
AccountEndpoint=https://<database account name>.documents.azure.com:443/;AccountKey=<database account master key>;
```

SQL bağlantı dizesi aşağıdaki biçimdedir:

```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Bölge isteğe bağlıdır. Atlanırsa, kapsayıcının birincil bölgesi kullanılır.

Azure Cosmos DB kapsayıcı adı, sözdiziminde tırnak işareti olmadan belirtilir `OPENROWSET` . Kapsayıcı adında özel karakterler varsa (örneğin, bir tire (-) varsa, ad sözdiziminde köşeli ayraç () içine sarılanmış olmalıdır `[]` `OPENROWSET` .

### <a name="openrowset-with-credential"></a>[Kimlik bilgisiyle OPENROWSET](#tab/openrowset-credential)

`OPENROWSET`Kimlik bilgisine başvuran sözdizimi kullanabilirsiniz:

```sql
OPENROWSET( 
       PROVIDER = 'CosmosDB',
       CONNECTION = '<SQL connection string for Azure Cosmos DB without account key>',
       OBJECT = '<Container name>',
       [ CREDENTIAL | SERVER_CREDENTIAL ] = '<credential name>'
    )  [ < with clause > ] AS alias
```

Azure Cosmos DB için SQL bağlantı dizesi bu durumda bir anahtar içermiyor. Bağlantı dizesi aşağıdaki biçimdedir:

```sql
'account=<database account name>;database=<database name>;region=<region name>'
```

Veritabanı hesabı ana anahtarı sunucu düzeyi kimlik bilgisine veya veritabanı kapsamlı kimlik bilgisine yerleştirildi. 

---

> [!IMPORTANT]
> `Latin1_General_100_CI_AS_SC_UTF8`Bir Azure Cosmos DB analitik deposundaki dize DEĞERLERI UTF-8 metin olarak kodlandığından, örneğin, bazı UTF-8 veritabanı harmanlaması kullandığınızdan emin olun.
> Dosyadaki metin kodlaması ve harmanlama arasında uyuşmazlık, beklenmeyen metin dönüştürme hatalarına neden olabilir.
> T-SQL ifadesini kullanarak geçerli veritabanının varsayılan harmanlamasını kolayca değiştirebilirsiniz `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` .

> [!NOTE]
> Sunucusuz bir SQL havuzu, Azure Cosmos DB bir işlem deposunun sorgulanmasını desteklemez.

## <a name="sample-dataset"></a>Örnek veri kümesi

Bu makaledeki örneklerde, merk [önleme ve denetim (ECDC) COVı-19 örnekleri](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) ve [Covı-19 açık araştırma VERI kümesi (kablo-19), DOI: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)için Avrupa Centre 'daki veriler temel alınır.

Bu sayfalardaki verilerin lisansını ve yapısını görebilirsiniz. Ayrıca, [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) ve [kablo-19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) veri kümeleri için örnek verileri indirebilirsiniz.

Sunucusuz bir SQL havuzuyla Azure Cosmos DB verilerinin nasıl sorgulanalınacağını gösteren bu makaledeki adımları takip etmek için aşağıdaki kaynakları oluşturduğunuzdan emin olun:

* [Azure SYNAPSE bağlantısı etkin](../../cosmos-db/configure-synapse-link.md)olan bir Azure Cosmos DB veritabanı hesabı.
* Adlı bir Azure Cosmos DB veritabanı `covid` .
* Adlı `Ecdc` ve `Cord19` önceki örnek veri kümeleri ile yüklenen iki Azure Cosmos DB kapsayıcısı.

Test amaçlı olarak aşağıdaki bağlantı dizesini kullanabilirsiniz: `Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==` . Bu bağlantı, SYNAPSE SQL uç noktanıza kıyasla uzak bölgede yer alabileceğinden bu bağlantının performans garantisi vermediğini unutmayın.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Otomatik Şema çıkarımı ile Azure Cosmos DB verileri keşfet

Azure Cosmos DB verileri keşfetmenin en kolay yolu otomatik Şema çıkarımı özelliğini kullanmaktır. `WITH`Deyimden yan tümcesini atlayarak `OPENROWSET` , SUNUCUSUZ SQL havuzunun Azure Cosmos DB kapsayıcısının analitik deposunun şemasını otomatik olarak (çıkarması) otomatik olarak oluşturmasını sağlayabilirsiniz.

### <a name="openrowset-with-key"></a>[Anahtarla OPENROWSET](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc) as documents
```

### <a name="openrowset-with-credential"></a>[Kimlik bilgisiyle OPENROWSET](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

---

Yukarıdaki örnekte, sunucusuz SQL havuzunun, `covid` `MyCosmosDbAccount` Azure Cosmos DB anahtarı (önceki örnekte kukla) kullanılarak kimliği doğrulanmış Azure Cosmos DB hesabındaki veritabanına bağlanmasını sağladık. Daha sonra `Ecdc` bölgede bulunan analitik depoya eriştik `West US 2` . Belirli özelliklerin projeksiyonu olmadığından, `OPENROWSET` işlev Azure Cosmos DB öğelerinden tüm özellikleri döndürür.

Azure Cosmos DB kapsayıcısındaki öğelerin, ve özelliklerinin olduğunu varsayarsak, `date_rep` `cases` `geo_id` Bu sorgunun sonuçları aşağıdaki tabloda gösterilmiştir:

| date_rep | çalışmaların | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Aynı Azure Cosmos DB veritabanındaki diğer kapsayıcıdan verileri araştırmanıza gerek varsa, aynı bağlantı dizesini kullanabilir ve üçüncü parametre olarak gerekli kapsayıcıya başvurabilirsiniz:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Açıkça şema belirt

' De otomatik Şema çıkarımı özelliği `OPENROWSET` basit ve kullanımı kolay bir querience sağlarken, iş senaryolarınız Azure Cosmos DB verilerinden yalnızca ilgili özellikleri okumak için şemayı açıkça belirtmenizi gerektirebilir.

`OPENROWSET`İşlevi, kapsayıcıdaki verilerden hangi özellikleri okumak istediğinizi açıkça belirtmenizi ve veri türlerini belirtmenizi sağlar.

Aşağıdaki yapıyla [ECDC COVıD veri kümesinden](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) bazı verileri içeri aktardığımızda Azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Azure Cosmos DB içindeki bu düz JSON belgeleri, SYNAPSE SQL 'de bir dizi satır ve sütun olarak gösterilebilir. `OPENROWSET`İşlevi, okumak istediğiniz özelliklerin bir alt kümesini ve yan tümcesindeki tam sütun türlerini belirtmenize olanak sağlar `WITH` :

### <a name="openrowset-with-key"></a>[Anahtarla OPENROWSET](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

### <a name="openrowset-with-credential"></a>[Kimlik bilgisiyle OPENROWSET](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
   
```

---
Bu sorgunun sonucu aşağıdaki tablo gibi görünebilir:

| date_rep | çalışmaların | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Azure Cosmos DB değerleri için kullanılması gereken SQL türleri hakkında daha fazla bilgi için makalenin sonundaki [SQL tür eşlemeleri kuralları](#azure-cosmos-db-to-sql-type-mappings) bölümüne bakın.

## <a name="create-view"></a>Görünüm Oluştur

Ana veya varsayılan veritabanlarında görünümlerin oluşturulması önerilmez veya desteklenmez. Bu nedenle, Görünümleriniz için bir kullanıcı veritabanı oluşturmanız gerekir.

Şemayı tanımladıktan sonra, Azure Cosmos DB verilerinizin üzerine bir görünüm hazırlayabilirsiniz. Azure Cosmos DB hesap anahtarınızı ayrı bir kimlik bilgisine yerleştirmeniz ve bu kimlik bilgisine işlevle başvurmalısınız `OPENROWSET` . Hesap anahtarınızı görünüm tanımında değiştirmeyin.

```sql
CREATE CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

`OPENROWSET`Performansınızı etkileyebileceğinden, açıkça tanımlanmış şema olmadan kullanmayın. Sütunlarınız için mümkün olan en küçük boyutları (örneğin, VARCHAR (100) varsayılan VARCHAR (8000) yerine) kullandığınızdan emin olun. UTF [-8 dönüştürme sorununu](../troubleshoot/reading-utf8-text.md)önlemek için, bazı UTF-8 harmanlamasını varsayılan veritabanı harmanlaması olarak veya açık sütun harmanlama olarak ayarlamanız gerekir. Harmanlama `Latin1_General_100_BIN2_UTF8` , bazı dize sütunlarını kullanarak verileri filtreleyerek en iyi performansı sağlar.

## <a name="query-nested-objects-and-arrays"></a>İç içe nesneleri ve dizileri sorgulama

Azure Cosmos DB, bunları iç içe geçmiş nesneler veya diziler olarak oluşturarak daha karmaşık veri modellerini temsil edebilirsiniz. Azure Cosmos DB için Azure SYNAPSE link 'in oto Sync özelliği, diğer bir deyişle, sunucusuz SQL havuzundan zengin sorgu için izin veren iç içe geçmiş veri türlerini işlemeyi da kapsayan, analitik depodaki şema gösterimini yönetir.

Örneğin, [kablo-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) veri kümesinin bu YAPıYı izleyen JSON belgeleri vardır:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Azure Cosmos DB iç içe geçmiş nesneler ve diziler, `OPENROWSET` işlev onları okuduğunda sorgu sonucunda json dizeleri olarak gösterilir. Yan tümcesini kullanırken, nesnelerdeki iç içe geçmiş değerlerin yollarını belirtebilirsiniz `WITH` :

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19)
WITH (  paper_id    varchar(8000),
        title        varchar(1000) '$.metadata.title',
        metadata     varchar(max),
        authors      varchar(max) '$.metadata.authors'
) AS docs;
```

Bu sorgunun sonucu aşağıdaki tablo gibi görünebilir:

| paper_id | başlık | meta veriler | düzenliyor |
| --- | --- | --- | --- |
| bb11206963e831f... | Tamamlayıcı bilgiler ekonomik-epidemi... | `{"title":"Supplementary Informati…` | `[{"first":"Julien","last":"Mélade","suffix":"","af…`| 
| bb1206963e831f1... | Iconyascent sera 'nın Immune-E kullanımı... | `{"title":"The Use of Convalescent…` | `[{"first":"Antonio","last":"Lavazza","suffix":"", …` |
| bb378eca9aac649... | Tylosema esculentum (marama) tuber ve B... | `{"title":"Tylosema esculentum (Ma…` | `[{"first":"Walter","last":"Chingwaru","suffix":"",…` | 

[Azure SYNAPSE bağlantısı 'nda karmaşık veri türlerini](../how-to-analyze-complex-schema.md) çözümleme ve [SUNUCUSUZ bir SQL havuzundaki iç içe yapılar](query-parquet-nested-types.md)hakkında daha fazla bilgi edinin.

> [!IMPORTANT]
> Metinde yerine beklenmedik karakterler görürseniz `MÃƒÂ©lade` `Mélade` , veritabanı harmanlamalarınız [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) harmanlamasına ayarlı değildir.
> Gibi bir SQL ifadesini kullanarak [veritabanının HARMANLAMASıNı](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) UTF-8 harmanlamasına çevirin `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="flatten-nested-arrays"></a>İç içe dizileri Düzleştir

Azure Cosmos DB veriler, bir [kablo-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) veri kümesinden yazarın dizisi gibi iç içe geçmiş alt dizilere sahip olabilir:

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

Bazı durumlarda, üst öğeden (meta veriler) özellikleri dizinin (yazarlar) tüm öğeleriyle "katılmanız" gerekebilir. Sunucusuz bir SQL havuzu, iç içe geçmiş diziye işlevi uygulayarak iç içe yapıları düzleştirmenize olanak sağlar `OPENJSON` :

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

Bu sorgunun sonucu aşağıdaki tablo gibi görünebilir:

| başlık | düzenliyor | adı | Son | ilişkisi |
| --- | --- | --- | --- | --- |
| Tamamlayıcı bilgiler ekonomik-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Tamamlayıcı bilgiler ekonomik-epidemi... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4 # |`{"laboratory":"","institution":"U…` | 
| Tamamlayıcı bilgiler ekonomik-epidemi... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Tamamlayıcı bilgiler ekonomik-epidemi... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Zeytin yeşili | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Metinde yerine beklenmedik karakterler görürseniz `MÃƒÂ©lade` `Mélade` , veritabanı harmanlamalarınız [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) harmanlamasına ayarlı değildir. Gibi bir SQL ifadesini kullanarak [veritabanının HARMANLAMASıNı](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) UTF-8 harmanlamasına çevirin `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>SQL tür eşlemelerine Azure Cosmos DB

Azure Cosmos DB işlemsel depo şema belirsiz olsa da, analitik depo analitik sorgu performansını iyileştirmek için şemadır. Azure SYNAPSE link 'in oto Sync özelliği sayesinde, analitik depodaki şema gösterimini, iç içe geçmiş veri türlerini işlemeyi da kapsayan kutudan Azure Cosmos DB yönetir. Sunucusuz bir SQL havuzu analitik depoyu sorguladığı için, Azure Cosmos DB giriş veri türlerini SQL veri türlerine nasıl eşleneceğini anlamak önemlidir.

Azure Cosmos DB SQL (Core) API 'SI, JSON özelliği türlerini sayı, dize, Boole, null, iç içe nesne veya dizi olarak destekler. İçinde yan tümcesini kullanıyorsanız, bu JSON türleriyle eşleşen SQL türlerini seçmeniz gerekir `WITH` `OPENROWSET` . Aşağıdaki tabloda, Azure Cosmos DB farklı özellik türleri için kullanılması gereken SQL sütun türleri gösterilmektedir.

| Azure Cosmos DB Özellik türü | SQL sütun türü |
| --- | --- |
| Boole | bit |
| Tamsayı | bigint |
| Ondalık | float |
| Dize | varchar (UTF-8 veritabanı harmanlaması) |
| Tarih saat (ISO biçimli dize) | varchar (30) |
| Tarih saat (UNIX zaman damgası) | bigint |
| Null | `any SQL type` 
| İç içe nesne veya dizi | JSON metni olarak seri hale getirilmiş varchar (max) (UTF-8 veritabanı harmanlaması) |

## <a name="full-fidelity-schema"></a>Tam uygunluk şeması

Azure Cosmos DB tam uygunluk şeması, bir kapsayıcıdaki her özellik için hem değerleri hem de en iyi eşleşme türlerini kaydeder. `OPENROWSET`Tam aslına uygunluk şeması olan bir kapsayıcıdaki işlev hem türü hem de her hücrede gerçek değeri sağlar. Aşağıdaki sorgunun öğeleri tam uygunluk şemasına sahip bir kapsayıcıdan okuduğunu varsayalım:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) as rows
```

Bu sorgunun sonucu, JSON metni olarak biçimlendirilen türleri ve değerleri döndürür:

| date_rep | çalışmaların | geo_id |
| --- | --- | --- |
| {"Date": "2020-08-13"} | {"Int32": "254"} | {"String": "RS"} |
| {"Date": "2020-08-12"} | {"Int32": "235"}| {"String": "RS"} |
| {"Date": "2020-08-11"} | {"Int32": "316"} | {"String": "RS"} |
| {"Date": "2020-08-10"} | {"Int32": "281"} | {"String": "RS"} |
| {"Date": "2020-08-09"} | {"Int32": "295"} | {"String": "RS"} |
| {"String": "2020/08/08"} | {"Int32": ","} | {"String": "RS"} |
| {"Date": "2020-08-07"} | {"float64": "339.0"} | {"String": "RS"} |

Her değer için, Azure Cosmos DB kapsayıcı öğesinde tanımlanan türü görebilirsiniz. Özelliği için değerlerin çoğu `date_rep` `date` değer içerir, ancak bazıları Azure Cosmos DB dize olarak yanlış depolanır. Tam uygunluk şeması, hem doğru yazılmış değerleri hem de `date` Hatalı biçimlendirilmiş `string` değerleri döndürür.
Durum sayısı, bir değer olarak depolanan bilgiler `int32` , ancak ondalık sayı olarak girilen bir değer vardır. Bu değer türü vardır `float64` . En büyük sayıyı aşan bazı değerler varsa `int32` , bunlar tür olarak depolanır `int64` . `geo_id`Bu örnekteki tüm değerler türler olarak depolanır `string` .

> [!IMPORTANT]
> `OPENROWSET`Yan tümcesi olmayan işlev `WITH` hem beklenen türlerle hem de hatalı olarak girilen türlere sahip değerleri gösterir. Bu işlev, raporlama için değil veri araştırması için tasarlanmıştır. Raporları derlemek için bu işlevden döndürülen JSON değerlerini ayrıştırmayın. Raporlarınızı oluşturmak için açık bir [WITH yan tümcesi](#query-items-with-full-fidelity-schema) kullanın. Tam uygunluk analitik deposunda düzeltmeleri uygulamak için Azure Cosmos DB kapsayıcısında yanlış türlere sahip değerleri temizlemeniz gerekir.

Mongo DB API türünün Azure Cosmos DB hesaplarını sorgulamanızı istiyorsanız, analitik depoda tam uygunluk şeması gösterimi ve [Azure Cosmos DB analitik deposunda](../../cosmos-db/analytical-store-introduction.md#analytical-schema)kullanılacak genişletilmiş özellik adları hakkında daha fazla bilgi edinebilirsiniz.

### <a name="query-items-with-full-fidelity-schema"></a>Tam uygunluk şeması olan öğeleri sorgula

Tam uygunluk şeması sorgulanırken, yan tümcesinde SQL türünü ve beklenen Azure Cosmos DB Özellik türünü açıkça belirtmeniz gerekir `WITH` .

Aşağıdaki örnekte, `string` özelliği için doğru tür olduğunu `geo_id` ve `int32` özelliği için doğru tür olduğunu varsayacağız `cases` :

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

`geo_id`Ve `cases` diğer türlerine sahip olan değerler değer olarak döndürülür `NULL` . Bu sorgu, `cases` () ifadesinde belirtilen türle yalnızca öğesine başvuracaktır `cases.int32` .

`cases.int64`Bir Azure Cosmos DB kapsayıcısında temizlenemeyen diğer türler (,) ile değerleriniz varsa `cases.float64` , bunlara açıkça bir yan tümce içinde başvurulmanız `WITH` ve sonuçları birleştirmeniz gerekir. Aşağıdaki sorgu `int32` ,, `int64` ve `float64` `cases` sütununda depolanır:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

Bu örnekte, servis talebi sayısı, ya da değerleri olarak depolanır `int32` `int64` `float64` . Ülke başına servis talebi sayısını hesaplamak için tüm değerler ayıklanmalıdır.

## <a name="known-issues"></a>Bilinen sorunlar

- `OPENROWSET`Sütun harmanlamasının UTF-8 kodlaması yoksa sunucusuz BIR SQL havuzu bir derleme zamanı uyarısı döndürür. `OPENROWSET`T-SQL ifadesini kullanarak geçerli veritabanında çalışan tüm işlevler için Varsayılan harmanlamayı kolayca değiştirebilirsiniz `alter database current collate Latin1_General_100_CI_AS_SC_UTF8` .

Olası hatalar ve sorun giderme eylemleri aşağıdaki tabloda listelenmiştir.

| Hata | Kök neden |
| --- | --- |
| Sözdizimi hataları:<br/> -Yakınında yanlış söz dizimi `Openrowset`<br/> - `...` tanınan bir `BULK OPENROWSET` sağlayıcı seçeneği değil.<br/> -Yakınında yanlış söz dizimi `...` | Olası kök nedenler:<br/> -İlk parametre olarak CosmosDB kullanmıyor.<br/> -Üçüncü parametresindeki bir tanımlayıcı yerine dize sabit değeri kullanılıyor.<br/> -Üçüncü parametreyi (kapsayıcı adı) belirtmiyor. |
| CosmosDB bağlantı dizesinde bir hata oluştu. | -Hesap, veritabanı veya anahtar belirtilmedi. <br/> -Bir bağlantı dizesinde tanınmayan bir seçenek vardır.<br/> -Noktalı virgül ( `;` ), bağlantı dizesinin sonuna yerleştirilir. |
| CosmosDB yolunu çözümleme, "yanlış hesap adı" veya "yanlış veritabanı adı" hatasıyla başarısız oldu. | Belirtilen hesap adı, veritabanı adı veya kapsayıcı bulunamıyor ya da analiz depolama alanı belirtilen koleksiyon için etkinleştirilmemiş.|
| CosmosDB yolu çözümlenirken "yanlış gizli değer" veya "gizli dizi null ya da boş" hatası ile başarısız oldu. | Hesap anahtarı geçerli değil veya eksik. |
| `column name`Tür sütunu `type name` dış veri türüyle uyumlu değil `type name` . | Yan tümcesindeki belirtilen sütun türü `WITH` Azure Cosmos DB kapsayıcısının türüyle eşleşmiyor. Sütun türünü [SQL tür eşlemeleriyle Azure Cosmos DB](#azure-cosmos-db-to-sql-type-mappings)bölümünde açıklandığı gibi değiştirmeyi deneyin veya `VARCHAR` türü kullanın. |
| Sütun `NULL` , tüm hücrelerdeki değerleri içerir. | Yan tümcesindeki yanlış sütun adı veya yol ifadesi olabilir `WITH` . Yan tümcesindeki sütun adı (veya sütun türünden sonraki yol ifadesi) `WITH` Azure Cosmos DB koleksiyonundaki bazı özellik adları ile eşleşmelidir. Karşılaştırma *büyük/küçük harfe duyarlıdır*. Örneğin, `productCode` ve `ProductCode` farklı özelliklerdir. |

[Azure SYNAPSE Analytics geri bildirim sayfasında](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862)öneriler ve sorunlar rapor edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Azure SYNAPSE bağlantısıyla Power BI ve sunucusuz SQL havuzu kullanma](../../cosmos-db/synapse-link-power-bi.md)
- [Sunucusuz bir SQL havuzunda görünüm oluşturma ve kullanma](create-use-views.md)
- [Azure Cosmos DB üzerinde sunucusuz SQL havuzu görünümleri oluşturma ve bunları DirectQuery aracılığıyla Power BI modellerine bağlama hakkında öğretici](./tutorial-data-analyst.md)
