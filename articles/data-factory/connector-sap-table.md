---
title: SAP tablosundan veri kopyalama
description: Bir Azure Data Factory işlem hattındaki kopyalama etkinliğini kullanarak SAP tablosundan desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2021
ms.openlocfilehash: 4026d2f987ca37834231ac4d7e827ff543af9d2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232401"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Azure Data Factory kullanarak SAP tablosundan veri kopyalama

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, bir SAP tablosundan veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Daha fazla bilgi için bkz. [kopyalama etkinliğine genel bakış](copy-activity-overview.md).

>[!TIP]
>ADF 'nin SAP veri tümleştirme senaryosunda genel desteğini öğrenmek için, her SAP Bağlayıcısı, karşılaştırma ve kılavuza ayrıntılı giriş ile [Azure Data Factory Teknik İnceleme kullanarak SAP veri tümleştirme](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) konusuna bakın.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu SAP tablo Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Bir SAP tablosundan desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak veya havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu SAP tablo Bağlayıcısı şunları destekler:

- İçindeki bir SAP tablosundan veri kopyalama:

  - SAP ERP merkezi bileşeni (SAP ECC) sürüm 7,01 veya üzeri (2015 sonrasında yayınlanan son SAP desteği paket yığınında).
  - SAP Business Warehouse (SAP BW) sürüm 7,01 veya üzeri (2015 sonrasında yayınlanan son SAP desteği paket yığınında).
  - SAP S/4HANA.
  - SAP Business Suite sürüm 7,01 veya üzeri (2015 ' den sonra yayınlanan son SAP desteği paket yığınında) diğer ürünler.

- SAP saydam tablosundan, havuza alınmış bir tablodan, kümelenmiş bir tablodan ve bir görünümden veri kopyalama.
- SNC yapılandırıldıysa, temel kimlik doğrulaması veya güvenli ağ Iletişimleri (SNC) kullanarak verileri kopyalama.
- SAP uygulama sunucusuna veya SAP ileti sunucusuna bağlanma.
- Varsayılan veya özel RFC aracılığıyla veri alma.

Sürüm 7,01 veya üzeri, SAP ECC sürümü yerine SAP NetWeaver sürümüne başvurur. Örneğin, SAP ECC 6,0 EHP 7, genel olarak NetWeaver sürüm >= 7,4 ' dir. Ortamınız hakkında emin değilseniz SAP sisteminizden sürümü onaylamaya yönelik adımlar aşağıda verilmiştir:

1. SAP sistemine bağlanmak için SAP GUI 'yi kullanın. 
2. **Sistem**  ->  **durumuna** gidin. 
3. SAP_BASIS sürümünü denetleyin, 701 ' den büyük veya ona eşit olduğundan emin olun.  
      ![SAP_BASIS denetle](./media/connector-sap-table/sap-basis.png)

## <a name="prerequisites"></a>Önkoşullar

Bu SAP tablosu bağlayıcısını kullanmak için şunları yapmanız gerekir:

- Şirket içinde barındırılan bir tümleştirme çalışma zamanı ayarlayın (sürüm 3,17 veya üzeri). Daha fazla bilgi için, bkz. [Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma ve yapılandırma](create-self-hosted-integration-runtime.md).

- 64-bit SAP bağlayıcısını SAP web sitesinden [Microsoft .NET 3,0 için](https://support.sap.com/en/product/connectors/msnet.html) indirin ve şirket içinde barındırılan tümleştirme çalışma zamanı makinesine yükleyin. Yükleme sırasında, **Isteğe bağlı kurulum adımları** penceresindeki **GAC 'ye derlemeleri yükleme** seçeneğini seçtiğinizden emin olun.

  ![.NET için SAP bağlayıcısını yükler](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Data Factory SAP tablosu Bağlayıcısı 'nda kullanılmakta olan SAP kullanıcısının aşağıdaki izinlere sahip olması gerekir:

  - Uzak Işlev çağrısı (RFC) hedeflerini kullanma yetkilendirmesi.
  - S_SDSAUTH yetkilendirme nesnesinin yürütme etkinliğinin izinleri. SAP Note 40089 ' i temel yetkilendirme nesnelerinde bulabilirsiniz. Bazı RFC 'Ler, temel NCo Bağlayıcısı için gereklidir, örneğin RFC_FUNCTION_SEARCH. 

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, SAP tablo bağlayıcısına özgü Data Factory varlıklarını tanımlamak için kullanılan özellikler hakkında ayrıntılar sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki özellikler SAP BW açık hub bağlı hizmeti için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| `type` | `type`Özelliğin olarak ayarlanması gerekir `SapTable` . | Yes |
| `server` | SAP örneğinin bulunduğu sunucunun adı.<br/>SAP uygulama sunucusuna bağlanmak için kullanın. | No |
| `systemNumber` | SAP sisteminin sistem numarası.<br/>SAP uygulama sunucusuna bağlanmak için kullanın.<br/>İzin verilen değer: dize olarak temsil edilen iki basamaklı ondalık sayı. | No |
| `messageServer` | SAP ileti sunucusunun ana bilgisayar adı.<br/>Bir SAP ileti sunucusuna bağlanmak için kullanın. | No |
| `messageServerService` | İleti sunucusunun hizmet adı veya bağlantı noktası numarası.<br/>Bir SAP ileti sunucusuna bağlanmak için kullanın. | No |
| `systemId` | Tablonun bulunduğu SAP sisteminin KIMLIĞI.<br/>Bir SAP ileti sunucusuna bağlanmak için kullanın. | No |
| `logonGroup` | SAP sistemi için oturum açma grubu.<br/>Bir SAP ileti sunucusuna bağlanmak için kullanın. | No |
| `clientId` | SAP sistemindeki istemcinin KIMLIĞI.<br/>İzin verilen değer: dize olarak temsil edilen üç basamaklı ondalık sayı. | Yes |
| `language` | SAP sisteminin kullandığı dil.<br/>Varsayılan değer `EN` olarak belirlenmiştir.| No |
| `userName` | SAP sunucusuna erişimi olan kullanıcının adı. | Yes |
| `password` | Kullanıcının parolası. Bu alanı, `SecureString` Data Factory güvenli bir şekilde depolamak için yazın veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)için işaretleyin. | Yes |
| `sncMode` | Tablonun bulunduğu SAP sunucusuna erişmek için SNC etkinleştirme göstergesi.<br/>SAP sunucusuna bağlanmak için SNC kullanmak istiyorsanız kullanın.<br/>İzin verilen değerler `0` (kapalı, varsayılan) veya `1` (açık). | No |
| `sncMyName` | Tablonun bulunduğu SAP sunucusuna erişmek için başlatıcının SNC adı.<br/>Açık olduğunda geçerlidir `sncMode` . | No |
| `sncPartnerName` | Tablonun bulunduğu SAP sunucusuna erişmek için iletişim ortağının SNC adı.<br/>Açık olduğunda geçerlidir `sncMode` . | No |
| `sncLibraryPath` | Tablonun bulunduğu SAP sunucusuna erişmek için dış güvenlik ürününün kitaplığı.<br/>Açık olduğunda geçerlidir `sncMode` . | No |
| `sncQop` | Uygulanacak SNC koruma düzeyi kalitesi.<br/>Açık olduğunda geçerlidir `sncMode` . <br/>İzin verilen değerler `1` (kimlik doğrulaması), `2` (bütünlük), `3` (Gizlilik), `8` (varsayılan), `9` (en fazla). | No |
| `connectVia` | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . [Önkoşullardan](#prerequisites)daha önce belirtildiği gibi, şirket içinde barındırılan bir tümleştirme çalışma zamanı gereklidir. |Yes |

### <a name="example-1-connect-to-an-sap-application-server"></a>Örnek 1: SAP uygulama sunucusuna bağlanma

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>Örnek 2: SAP ileti sunucusuna bağlanma

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Örnek 3: SNC kullanarak bağlanma

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md). Aşağıdaki bölüm, SAP tablo veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

Ve SAP BW açık hub bağlantılı hizmetine veri kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| `type` | `type`Özelliğin olarak ayarlanması gerekir `SapTableResource` . | Yes |
| `tableName` | Verilerin kopyalanacağı SAP tablosunun adı. | Yes |

### <a name="example"></a>Örnek

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md). Aşağıdaki bölüm, SAP tablo kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="sap-table-as-source"></a>Kaynak olarak SAP tablosu

Bir SAP tablosundan veri kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik                         | Açıklama                                                  | Gerekli |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | `type`Özelliğin olarak ayarlanması gerekir `SapTableSource` .         | Yes      |
| `rowCount`                         | Alınacak satır sayısı.                              | No       |
| `rfcTableFields`                 | SAP tablosundan kopyalanacak alanlar (sütunlar). Örneğin, `column0, column1`. | No       |
| `rfcTableOptions`                | SAP tablosundaki satırları filtrelemeye yönelik seçenekler. Örneğin, `COLUMN0 EQ 'SOMEVALUE'`. Bu makalenin ilerleyen kısımlarında Ayrıca bkz. SAP Query operator tablosu. | No       |
| `customRfcReadTableFunctionModule` | Bir SAP tablosundan veri okumak için kullanılabilen özel bir RFC işlev modülü.<br>Verilerin SAP sisteminizden nasıl alındığını tanımlamak ve Data Factory ' a geri dönmek için özel bir RFC işlevi modülü kullanabilirsiniz. Özel işlev modülünün `/SAPDS/RFC_READ_TABLE2` , Data Factory tarafından kullanılan varsayılan arabirim olan öğesine benzer bir arabirimi (içeri aktarma, dışarı aktarma, tablolar) uygulanmış olması gerekir.<br>Data Factory | No       |
| `partitionOption`                  | Bir SAP tablosundan okunacak bölüm mekanizması. Desteklenen seçenekler şunlardır: <ul><li>`None`</li><li>`PartitionOnInt` (gibi, sol tarafta sıfır dolgusu olan normal tamsayı veya tamsayı değerleri `0000012345` )</li><li>`PartitionOnCalendarYear` ("YYYY" biçiminde 4 basamak)</li><li>`PartitionOnCalendarMonth` ("YYYYMM" biçiminde 6 basamak)</li><li>`PartitionOnCalendarDate` ("YYYYMMDD" biçiminde 8 basamak)</li><li>`PartitionOntime` (örneğin, "HHMMSS" biçiminde 6 basamak `235959` )</li></ul> | No       |
| `partitionColumnName`              | Verileri bölümlemek için kullanılan sütunun adı.                | No       |
| `partitionUpperBound`              | Bölümlendirmeye devam etmek için, içinde belirtilen sütunun en büyük değeri `partitionColumnName` . | No       |
| `partitionLowerBound`              | Bölümlendirmeye devam etmek için, içinde belirtilen sütunun en küçük değeri `partitionColumnName` . (Note: `partitionLowerBound` bölüm seçeneği olduğunda "0" olamaz `PartitionOnInt` ) | No       |
| `maxPartitionsNumber`              | Verilerin bölüneceği en fazla bölüm sayısı.     | No       |
| `sapDataColumnDelimiter` | Çıktı verilerini ayırmak için SAP RFC 'ye geçirilen sınırlayıcı olarak kullanılan tek karakter. | No |

>[!TIP]
>SAP tablonuzda birkaç milyar satır gibi büyük miktarda veri varsa, `partitionOption` `partitionSetting` verileri daha küçük bölümlere bölmek için ve kullanın. Bu durumda, veriler bölüm başına okunurdur ve her bir veri bölümü, tek bir RFC çağrısıyla SAP sunucusundan alınır.<br/>
<br/>
>`partitionOption` `partitionOnInt` Örnek olarak, her bölümdeki satır sayısı şu formül ile hesaplanır: (ve arasında kalan toplam satır `partitionUpperBound` `partitionLowerBound` )/ `maxPartitionsNumber` .<br/>
<br/>
>Kopyayı hızlandırmak için veri bölümlerini paralel olarak yüklemek için, paralel derece [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayarla denetlenir. Örneğin, `parallelCopies` dört olarak ayarlarsanız, Data Factory aynı anda, belirtilen bölüm seçeneğiniz ve ayarlarınıza göre dört sorgu üretir ve çalışır ve her sorgu, SAP tabloınızdan verilerin bir kısmını alır. `maxPartitionsNumber`Özelliğin değerinin birden çok değerini yapmanızı kesinlikle öneririz `parallelCopies` . Dosya tabanlı veri deposuna veri kopyalarken, bir klasöre birden çok dosya (yalnızca klasör adını belirt) olarak yazmak da daha da iyidir. Bu durumda, performans tek bir dosyaya yazılmasından daha iyidir.


>[!TIP]
> , `BASXML` Azure Data Factory tarafında bu SAP tablo Bağlayıcısı için varsayılan olarak etkindir.

İçinde `rfcTableOptions` , satırları filtrelemek için aşağıdaki genel SAP sorgu işleçlerini kullanabilirsiniz:

| İşleç | Açıklama |
| :------- | :------- |
| `EQ` | Eşittir |
| `NE` | Eşit değildir |
| `LT` | Küçüktür |
| `LE` | Küçük veya eşittir |
| `GT` | Büyüktür |
| `GE` | Büyük veya eşittir |
| `IN` | İtibariyle `TABCLASS IN ('TRANSP', 'INTTAB')` |
| `LIKE` | İtibariyle `LIKE 'Emma%'` |

### <a name="example"></a>Örnek

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="join-sap-tables"></a>SAP tablolarına katılarak

Şu anda SAP tablo Bağlayıcısı Varsayılan işlev modülünün bulunduğu tek bir tabloyu destekler. Birden çok tablonun birleştirilmiş verilerini almak için aşağıdaki adımları izleyerek SAP tablo Bağlayıcısı 'ndaki [Customrfcreadtablefunctionmodule](#copy-activity-properties) özelliğinden yararlanabilirsiniz:

- Bir sorgu SEÇENEĞI olarak bir sorgu alabilir ve verileri almak için kendi mantığınızı uygulayabilen [özel bir işlev modülü yazın](#create-custom-function-module).
- "Özel işlev modülü" için, özel işlev modülünüzün adını girin.
- "RFC tablo seçenekleri" için, işlev modülünüzü " `<TABLE1>` Iç BIRLEŞIM COLUMN0" gıbı seçenekler olarak akışa almak için tablo JOIN ifadesini belirtin `<TABLE2>` .

Aşağıda bir örnek verilmiştir:

![SAP tablosuna katılması](./media/connector-sap-table/sap-table-join.png) 

>[!TIP]
>Ayrıca, Birleşik verileri SAP tablo Bağlayıcısı tarafından desteklenen görünümde toplanmış olmaya da göz önüne alabilirsiniz.
>Ayrıca ilgili tabloları Azure 'a (ör. Azure depolama, Azure SQL veritabanı) eklemek için çıkarmayı ve daha sonra veri akışını kullanarak daha fazla JOIN veya Filter ile devam edebilirsiniz.

## <a name="create-custom-function-module"></a>Özel işlev modülü oluştur

SAP tablosu için şu anda kopyalama kaynağında [Customrfcreadtablefunctionmodule](#copy-activity-properties) özelliğini destekliyoruz, bu da kendi mantığınızı ve işlem verilerinizi kullanmanıza olanak sağlar.

Hızlı bir kılavuz olarak, "özel işlev modülü" ile çalışmaya başlamak için bazı gereksinimler aşağıda verilmiştir:

- Tanım:

    ![Tanım](./media/connector-sap-table/custom-function-module-definition.png) 

- Verileri aşağıdaki tablolardan birine dışarı aktarın:

    ![Tablo 1 dışarı aktarma](./media/connector-sap-table/export-table-1.png) 

    ![Dışarı aktarma tablosu 2](./media/connector-sap-table/export-table-2.png)
 
Aşağıda, SAP tablo bağlayıcısının özel işlev modülüyle nasıl çalıştığı gösterilmektedir:

1. SAP NCO aracılığıyla SAP sunucusuyla bağlantı oluşturun.

1. Aşağıdaki şekilde ayarlanan parametrelerle birlikte "özel işlev modülü" çağırın:

    - QUERY_TABLE: ADF SAP tablosu veri kümesinde ayarladığınız tablo adı; 
    - Sınırlayıcı: ADF SAP tablo kaynağında ayarladığınız sınırlayıcı; 
    - ROWCOUNT/seçenek/alanlar: ADF tablo kaynağında ayarladığınız satır sayısı/toplanmış seçenek/alanlar.

1. Sonucu alın ve verileri aşağıdaki yollarla ayrıştırın:

    1. Şemaları almak için Fields tablosundaki değeri ayrıştırın.

        ![Alanlardaki değerleri Ayrıştır](./media/connector-sap-table/parse-values.png)

    1. Hangi tablonun bu değerleri içerdiğini görmek için çıkış tablosunun değerlerini alın.

        ![Çıkış tablosundaki değerleri Al](./media/connector-sap-table/get-values.png)

    1. OUT_TABLE değerleri alın, verileri ayrıştırın ve sonra havuza yazın.

## <a name="data-type-mappings-for-an-sap-table"></a>SAP tablosu için veri türü eşlemeleri

Bir SAP tablosundan veri kopyalarken aşağıdaki eşlemeler, SAP tablosu veri türlerinden Azure Data Factory geçici veri türlerine kadar kullanılır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md).

| SAP ABAP türü | Data Factory geçici veri türü |
|:--- |:--- |
| `C` Dizisinde | `String` |
| `I` Gir | `Int32` |
| `F` Float | `Double` |
| `D` Güncel | `String` |
| `T` Işınızda | `String` |
| `P` (BCD paketlenmiş, para birimi, ondalık, miktar) | `Decimal` |
| `N` Rakamlardan | `String` |
| `X` (İkili ve ham) | `String` |

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.


## <a name="next-steps"></a>Sonraki adımlar

Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
