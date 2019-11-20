---
title: Azure Data Lake Azure Veri Gezgini kullanarak verileri sorgulama
description: Azure Data Lake Azure Veri Gezgini kullanarak verileri sorgulamayı öğrenin.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: b0056df16dccaf1dc7e94aad1a2c6c262ffd89ee
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383371"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Azure Data Lake Azure Veri Gezgini kullanarak verileri sorgulama (Önizleme)

Azure Data Lake Storage, büyük veri analizi için yüksek düzeyde ölçeklenebilir ve ekonomik bir Data Lake çözümüdür. İçgörülere daha hızlı bir şekilde ulaşabilmeniz için yüksek performanslı dosya sisteminin gücünü büyük ölçek ve tasarrufla sunar. Analiz iş yükleri için iyileştirilmiş olan Data Lake Storage Gen2, Azure Blob Depolama özelliklerini geliştirir.
 
Azure Veri Gezgini, Azure Blob depolama ve Azure Data Lake Storage 2. tümleştirilerek, Gölü verilere hızlı, önbelleğe alınmış ve dizinli erişim sağlar. Azure Veri Gezgini 'a girmeden önce Gölü verileri analiz edebilir ve sorgulayabilirsiniz. Ayrıca, alınan ve toplanan yerel Gölü verileri aynı anda sorgulayabilirsiniz.  

> [!TIP]
> En iyi sorgu performansı, verileri Azure Veri Gezgini 'e göre gerekli hale getiriliyor. Önceki giriş yapılmadan Azure Data Lake Storage 2. verileri sorgulama özelliği yalnızca geçmiş veriler veya nadiren sorgulanan veriler için kullanılmalıdır.
 
## <a name="optimize-query-performance-in-the-lake"></a>Gölü sorgu performansını iyileştirme 

* İyileştirilmiş performans ve iyileştirilmiş sorgu süresi için verileri bölümleme.
* İyileştirilmiş performans (en iyi performans için lz4) için verileri sıkıştırın.
* Azure Blob depolama veya Azure Data Lake Storage 2. Azure Veri Gezgini kümenizle aynı bölge ile kullanın. 

## <a name="create-an-external-table"></a>Dış tablo oluşturma

 > [!NOTE]
 > Şu anda desteklenen depolama hesapları Azure Blob depolama veya Azure Data Lake Storage 2. Şu anda desteklenen veri biçimleri JSON, CSV, TSV ve txt ' dir.

1. Azure Veri Gezgini 'de dış tablo oluşturmak için komutunukullanın.`.create external table` `.show` ,`.drop`Ve gibi`.alter` ek dış tablo komutları [dış tablo komutlarında](/azure/kusto/management/externaltables)belgelenmiştir.

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * Daha ayrıntılı bölümlendirme ile artan performans beklenir. Örneğin, günlük bölümleri olan dış tablolar üzerinde sorgular, aylık bölümlenmiş tablolarla bu sorgulardan daha iyi performansa sahip olacaktır.
    > * Bölümler içeren bir dış tablo tanımladığınızda, depolama yapısının aynı olması beklenir.
Örneğin, tablo YYYY/AA/GG biçiminde bir tarih saat bölümüyle tanımlanmışsa (varsayılan), URI depolama dosya yolu *kapsayıcı1/yyyy/aa/gg/all_exported_blobs*olmalıdır. 
    > * Dış tablo bir tarih saat sütunuyla bölümlense, sorgunuza kapalı bir Aralık için her zaman bir zaman filtresi ekleyin (örneğin, sorgu- `ArchivedProducts | where Timestamp between (ago(1h) . 10m)` -bundan daha iyi (açılan Aralık) bir- `ArchivedProducts | where Timestamp > ago(1h)` ) gerçekleştirmelidir. 

1. Dış tablo, Web Kullanıcı arabiriminin sol bölmesinde görünür

    ![Web Kullanıcı arabiriminde dış tablo](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>JSON biçiminde bir dış tablo oluşturma

JSON biçiminde bir dış tablo oluşturabilirsiniz. Daha fazla bilgi için bkz. [dış tablo komutları](/azure/kusto/management/externaltables)

1. *Externaltablejson*adlı bir tablo oluşturmak için komutunukullanın:`.create external table`

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. JSON biçimi, aşağıda gösterildiği gibi sütunlara eşleme oluşturma konusunda ikinci bir adım gerektirir. Aşağıdaki sorguda, *MappingName*adlı belirli bir JSON eşlemesi oluşturun:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Dış tablo izinleri
 
* Veritabanı kullanıcısı bir dış tablo oluşturabilir. Tablo Oluşturucu otomatik olarak tablo Yöneticisi olur.
* Küme, veritabanı veya tablo Yöneticisi mevcut bir tabloyu düzenleyebilir.
* Herhangi bir veritabanı kullanıcısı veya okuyucu, bir dış tabloyu sorgulayabilir.
 
## <a name="query-an-external-table"></a>Dış tabloyu sorgulama
 
Bir dış tabloyu sorgulamak için `external_table()` işlevini kullanın ve işlev bağımsız değişkeni olarak tablo adını sağlayın. Sorgunun geri kalanı standart kusto sorgu dilidir.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> IntelliSense, dış tablo sorgularında Şu anda desteklenmiyor.

### <a name="query-an-external-table-with-json-format"></a>JSON biçimiyle bir dış tablo sorgula

JSON biçimine sahip bir dış tabloyu sorgulamak için, `external_table()` işlevini kullanın ve işlev bağımsız değişkenleri olarak hem tablo adını hem de eşleme adını sağlayın. Aşağıdaki sorguda, *MappingName* belirtilmemişse, daha önce oluşturduğunuz bir eşleme kullanılacaktır.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Dış ve alınan verileri birlikte sorgula

Aynı sorgu içinde hem harici tabloları hem de alınan veri tablolarını sorgulayabilirsiniz. Siz [`join`](/azure/kusto/query/joinoperator) [veya`union`](/azure/kusto/query/unionoperator) dış tabloyu Azure Veri Gezgini, SQL Server veya diğer kaynaklardan ek verilerle birlikte kullanabilirsiniz. Bir dış [`let( ) statement`](/azure/kusto/query/letstatement) Tablo başvurusuna bir Özet adı atamak için bir kullanın.

Aşağıdaki örnekte, *Ürünler* bir veri tablosu ve *ArchivedProducts* Azure Data Lake Storage 2. veri içeren bir dış tablodur:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Yardım kümesindeki *Taxırides* dış tablosunu sorgula

*Taxırides* örnek veri kümesi, [NYC TAXI ve Limousine Komisyonu](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)'ndan yeni York City TAXI verileri içerir.

### <a name="create-external-table-taxirides"></a>Dış tablo *Vergilenirides* oluşturma 

> [!NOTE]
> Bu bölümde, *Yardım* kümesinde *Taxırides* dış tablosunu oluşturmak için kullanılan sorgu gösterilmektedir. Bu tablo zaten oluşturulduğundan, bu bölümü atlayabilir ve [sorgu *Taxırides* dış tablo verilerini](#query-taxirides-external-table-data)gerçekleştirebilirsiniz. 

1. Aşağıdaki sorgu, yardım kümesindeki dış tablo *Vergilenirilerini* oluşturmak için kullanılmıştır. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. Sonuç tablosu, *Yardım* kümesinde oluşturulmuştur:

    ![Taxırides dış tablo](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>*Taxırides* dış tablo verilerini sorgula 

[https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) *Taxırides* dış tablosunu sorgulamak için ' de oturum açın. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Bölümlendirme olmadan *Taxırides* dış tablosunu sorgula

[Bu sorguyu](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) , haftanın her günü için veri kümesinin tamamına göre değer kümesi olarak belirleyen *Taxırides* üzerinde çalıştırın. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Bu sorgu, haftanın en yoğun gününü gösterir. Veriler bölümlenmemiş olduğundan, bu sorgunun sonuçları döndürmesi uzun sürebilir (birkaç dakikaya kadar).

![bölümlenmemiş sorguyu işle](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Bölümlendirme ile Taxırides dış tablosunu sorgula 

[Bu sorguyu](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) , Ocak 2017 ' de kullanılan TAXI cab türlerini (sarı veya yeşil) gösteren dış tabloda *vergi Irides* üzerinde çalıştırın. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Bu sorgu, sorgu süresini ve performansını en iyi duruma getirmek için bölümleme kullanır. Sorgu bölümlenmiş bir sütunda (pickup_datetime) filtreler ve sonuçları birkaç saniye döndürür.

![bölümlenmiş sorguyu işle](media/data-lake-query-data/taxirides-with-partition.png)
  
Dış tablo *Vergilenides* üzerinde çalışacak ek sorgular yazabilir ve veriler hakkında daha fazla bilgi edinebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Azure Veri Gezgini kullanarak Azure Data Lake verilerinizi sorgulayın. [Sorguları yazmayı](write-queries.md) ve verilerinizden ek Öngörüler türetireceğinizi öğrenin.
