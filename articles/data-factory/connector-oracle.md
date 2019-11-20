---
title: Azure Data Factory kullanarak Oracle 'a ve veri kopyalama
description: Data Factory kullanarak, desteklenen kaynak depolardaki verileri bir Oracle veritabanına veya Oracle 'dan desteklenen havuz depolarına kopyalamayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: d8cbc7410f2b2bd525148cee9dc5b8ddbb756dff
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680498"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Azure Data Factory kullanarak verileri ve Oracle 'a kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-onprem-oracle-connector.md)
> * [Geçerli sürüm](connector-oracle.md)

Bu makalede, verileri bir Oracle veritabanından ve ' den kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)sayfasında oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu Oracle Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Bir Oracle veritabanından desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Ayrıca, desteklenen herhangi bir kaynak veri deposundan verileri bir Oracle veritabanına kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak veya havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu Oracle Bağlayıcısı şunları destekler:

- Bir Oracle veritabanının aşağıdaki sürümleri:
    - Oracle 18c R1 (18,1) ve üzeri
    - Oracle 12c R1 (12,1) ve üzeri
    - Oracle 11g R1 (11,1) ve üzeri
    - Oracle 10G R1 (10,1) ve üzeri
    - Oracle 9i R2 (9,2) ve üzeri
    - Oracle 8i R3 (8.1.7) ve üzeri
    - Oracle Database Cloud Sınavveri hizmeti
- Temel veya OID kimlik doğrulamaları kullanarak verileri kopyalama.
- Oracle kaynağından paralel kopyalama. Ayrıntılar için [Oracle 'Dan paralel kopyalama](#parallel-copy-from-oracle) bölümüne bakın.

> [!Note]
> Oracle proxy sunucusu desteklenmez.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Integration Runtime, yerleşik bir Oracle sürücüsü sağlar. Bu nedenle, ve Oracle 'dan veri kopyaladığınızda sürücüyü el ile yüklemeniz gerekmez.

## <a name="get-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, Oracle bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılar sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Oracle bağlı hizmeti aşağıdaki özellikleri destekler:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği **Oracle**olarak ayarlanmalıdır. | Evet |
| connectionString | Oracle Database örneğine bağlanmak için gereken bilgileri belirtir. <br/>Bu alanı, Data Factory güvenli bir şekilde depolamak için `SecureString` olarak işaretleyin. Ayrıca Azure Key Vault bir parola yerleştirebilir ve `password` yapılandırmasını bağlantı dizesinden dışarı çekebilirsiniz. Daha ayrıntılı bilgi için aşağıdaki örneklere bakın ve [kimlik bilgilerini Azure Key Vault depolayın](store-credentials-in-key-vault.md) . <br><br>**Desteklenen bağlantı türü**: veritabanınızı tanımlamak IÇIN **Oracle SID** veya **Oracle hizmet adını** kullanabilirsiniz:<br>-SID kullanıyorsanız: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>-Hizmet adı 'nı kullanıyorsanız: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Evet |
| connectVia | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanılır. |Hayır |

>[!TIP]
>Bir hata alırsanız, "ORA-01025: UPı parametresi aralık dışında" ve Oracle sürümünüz 8i ise, Bağlantı dizenizi `WireProtocolMode=1` ekleyin. Sonra yeniden deneyin.

Büyük/küçük harf olarak bağlantı dizesinde ayarlayabileceğiniz daha fazla bağlantı özelliği:

| Özellik | Açıklama | İzin verilen değerler |
|:--- |:--- |:--- |
| Dizi boyutu |Bağlayıcının tek bir ağ gidiş yolculuğuna getirileceği bayt sayısı. Ör. `ArraySize=‭10485760‬`.<br/><br/>Daha büyük değerler, ağ üzerinden veri getirme sayısını azaltarak aktarım hızını artırır. Daha küçük değerler yanıt süresini arttırır, çünkü sunucunun veri aktarmasını bekleyen bir gecikme vardır. | 1 ila 4294967296 (4 GB) arasında bir tamsayı. Varsayılan değer `60000`. 1 değeri bayt sayısını tanımlamaz, ancak tam olarak bir veri satırı için alan ayırmayı gösterir. |

Oracle bağlantısı üzerinde şifrelemeyi etkinleştirmek için iki seçeneğiniz vardır:

-   **Üçlü DES şifreleme (3DES) ve Gelişmiş Şifreleme Standardı (AES)** kullanmak için Oracle sunucu tarafında, Oracle gelişmiş güvenlik (OAS) bölümüne gidin ve şifreleme ayarlarını yapılandırın. Ayrıntılar için bkz. [Oracle belgeleri](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Oracle uygulama geliştirme çerçevesi (ADF) Bağlayıcısı, bir Oracle bağlantısı kurarken OAS 'de yapılandırdığınız birini kullanmak üzere şifreleme yöntemini otomatik olarak görüşür.

-   **SSL**kullanmak için:

    1.  SSL sertifika bilgilerini alın. SSL sertifikalarınızın Distinguished Encoding Rules (DER) ile kodlanmış sertifika bilgilerini alın ve çıktıyı kaydedin (-----sertifikayı Başlat... -----) Bir metin dosyası olarak son sertifika.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Örnek:** DERcert. cer dosyasından sertifika bilgilerini ayıklayın ve çıktıyı CERT. txt dosyasına kaydedin.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  `keystore` veya `truststore`oluşturun. Aşağıdaki komut, PKCS-12 biçiminde bir parola ile veya parolasız `truststore` dosyası oluşturur.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Örnek:** Parola ile MyTrustStoreFile adlı bir PKCS12 `truststore` dosyası oluşturun.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  `truststore` dosyasını şirket içinde barındırılan IR makinesine yerleştirin. Örneğin, dosyayı C:\mytruststorefilekonumundaki yere yerleştirin.
    4.  Azure Data Factory, Oracle bağlantı dizesini `EncryptionMethod=1` ve karşılık gelen `TrustStore`/`TrustStorePassword`değer ile yapılandırın. Örneğin, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Örnek:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek: Azure Key Vault parola depola**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
## <a name="dataset-properties"></a>Veri kümesi özellikleri

Bu bölüm, Oracle veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar. Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md). 

Ve Oracle 'dan verileri kopyalamak için, veri kümesinin Type özelliğini `OracleTable`olarak ayarlayın. Aşağıdaki özellikler desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Veri kümesinin Type özelliği `OracleTable`olarak ayarlanmalıdır. | Evet |
| manızı | Şemanın adı. |Kaynak için Hayır, havuz için Evet  |
| tablosundan | Tablo/görünüm adı. |Kaynak için Hayır, havuz için Evet  |
| tableName | Şema ile tablonun/görünümün adı. Bu özellik geriye dönük uyumluluk için desteklenir. Yeni iş yükü için `schema` ve `table`kullanın. | Kaynak için Hayır, havuz için Evet |

**Örnek:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Bu bölüm, Oracle kaynağı ve havuzu tarafından desteklenen özelliklerin bir listesini sağlar. Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Kaynak olarak Oracle

>[!TIP]
>Veri bölümlemesini kullanarak Oracle 'dan verileri verimli bir şekilde yüklemek için [Oracle 'Dan paralel kopyadan](#parallel-copy-from-oracle)daha fazla bilgi edinin.

Oracle 'dan veri kopyalamak için kopyalama etkinliğindeki kaynak türünü `OracleSource`olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının Type özelliği `OracleSource`olarak ayarlanmalıdır. | Evet |
| oracleReaderQuery | Verileri okumak için özel SQL sorgusunu kullanın. `"SELECT * FROM MyTable"` bunun bir örneğidir.<br>Bölümlenmiş yükü etkinleştirdiğinizde, sorgunuza karşılık gelen yerleşik bölüm parametrelerini de eklemeniz gerekir. Örnekler için [Oracle 'Dan paralel kopyalama](#parallel-copy-from-oracle) bölümüne bakın. | Hayır |
| partitionOptions | Oracle 'dan veri yüklemek için kullanılan veri bölümleme seçeneklerini belirtir. <br>İzin verilen değerler: **none** (default), **Physicalpartitionsoftable** ve **DynamicRange**.<br>Bir bölüm seçeneği etkinleştirildiğinde (`None`değil), bir Oracle veritabanından eşzamanlı olarak veri yükleme için paralellik derecesi kopyalama etkinliğinde [`parallelCopies`](copy-activity-performance.md#parallel-copy) ayarı tarafından denetlenir. | Hayır |
| partitionSettings | Veri bölümleme için ayarların grubunu belirtin. <br>Bölüm seçeneği `None`olmadığında geçerlidir. | Hayır |
| partitionNames | Kopyalanması gereken fiziksel bölümlerin listesi. <br>Bölüm seçeneği `PhysicalPartitionsOfTable`olduğunda geçerlidir. Kaynak verileri almak için bir sorgu kullanırsanız, ' ın WHERE yan tümcesinde `?AdfTabularPartitionName` kanca. Örnek için [Oracle 'Dan paralel kopyalama](#parallel-copy-from-oracle) bölümüne bakın. | Hayır |
| partitionColumnName | Paralel kopya için Aralık bölümleme tarafından kullanılacak, **tamsayı türünde** kaynak sütunun adını belirtin. Belirtilmemişse, tablonun birincil anahtarı otomatik olarak algılanır ve bölüm sütunu olarak kullanılır. <br>Bölüm seçeneği `DynamicRange`olduğunda geçerlidir. Kaynak verileri almak için bir sorgu kullanırsanız, ' ın WHERE yan tümcesinde `?AdfRangePartitionColumnName` kanca. Örnek için [Oracle 'Dan paralel kopyalama](#parallel-copy-from-oracle) bölümüne bakın. | Hayır |
| partitionüstsınırı | Verilerin kopyalanacağı bölüm sütununun en büyük değeri. <br>Bölüm seçeneği `DynamicRange`olduğunda geçerlidir. Kaynak verileri almak için bir sorgu kullanırsanız, ' ın WHERE yan tümcesinde `?AdfRangePartitionUpbound` kanca. Örnek için [Oracle 'Dan paralel kopyalama](#parallel-copy-from-oracle) bölümüne bakın. | Hayır |
| Partitionalme sınırı | Verilerin kopyalanacağı bölüm sütununun en küçük değeri. <br>Bölüm seçeneği `DynamicRange`olduğunda geçerlidir. Kaynak verileri almak için bir sorgu kullanırsanız, ' ın WHERE yan tümcesinde `?AdfRangePartitionLowbound` kanca. Örnek için [Oracle 'Dan paralel kopyalama](#parallel-copy-from-oracle) bölümüne bakın. | Hayır |

**Örnek: bölüm olmadan temel sorgu kullanarak veri kopyalama**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Havuz olarak Oracle

Verileri Oracle 'a kopyalamak için kopyalama etkinliğindeki havuz türünü `OracleSink`olarak ayarlayın. Aşağıdaki özellikler, etkinlik **havuzunu** Kopyala bölümünde desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği havuzunun Type özelliği `OracleSink`olarak ayarlanmalıdır. | Evet |
| writeBatchSize | Arabellek boyutu `writeBatchSize`ulaştığında SQL tablosuna veri ekler.<br/>İzin verilen değerler Integer (satır sayısı). |Hayır (varsayılan değer 10.000) |
| writeBatchTimeout | Toplu iş ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi.<br/>İzin verilen değerler TimeSpan. Örnek olarak 00:30:00 (30 dakika). | Hayır |
| Ön Copyscrıpt | Kopyalama etkinliği için, her çalıştırmada verileri Oracle 'a yazmadan önce çalıştırılacak bir SQL sorgusu belirtin. Bu özelliği, önceden yüklenmiş verileri temizlemek için kullanabilirsiniz. | Hayır |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Oracle 'dan paralel kopyalama

Data Factory Oracle Bağlayıcısı, verileri Oracle 'dan paralel olarak kopyalamak için yerleşik veri bölümlemesini sağlar. Kopyalama etkinliğinin **kaynak** sekmesinde veri bölümleme seçeneklerini bulabilirsiniz.

![Bölüm seçeneklerinin ekran görüntüsü](./media/connector-oracle/connector-oracle-partition-options.png)

Bölümlenmiş kopyayı etkinleştirdiğinizde Data Factory verileri bölümlere göre yüklemek için Oracle kaynağınızdan paralel sorgular çalıştırır. Paralel derece kopyalama etkinliğinde [`parallelCopies`](copy-activity-performance.md#parallel-copy) ayarıyla denetlenir. Örneğin, `parallelCopies` dört olarak ayarlarsanız, Data Factory aynı anda, belirtilen bölüm seçeneğiniz ve ayarlarınıza göre dört sorgu üretir ve çalışır ve her sorgu Oracle veritabanınızdan verilerin bir kısmını alır.

Oracle veritabanınızdan büyük miktarda veri yüklediğinizde özellikle veri bölümleme ile paralel kopyayı etkinleştirmeniz önerilir. Farklı senaryolar için önerilen yapılandırma aşağıda verilmiştir. Dosya tabanlı veri deposuna veri kopyalarken, bir klasöre birden çok dosya (yalnızca klasör adını belirt) olarak yazma işlemi geri çağrılır, bu durumda performans tek bir dosyaya yazılmasından daha iyidir.

| Senaryo                                                     | Önerilen ayarlar                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fiziksel bölümlerle büyük tablodan tam yük.          | **Bölüm seçeneği**: tablonun fiziksel bölümleri. <br><br/>Yürütme sırasında Data Factory fiziksel bölümleri otomatik olarak algılar ve verileri bölümlere göre kopyalar. |
| Fiziksel bölümler olmadan, veri bölümlendirme için bir tamsayı sütunuyla birlikte büyük tablodan tam yük. | **Bölüm seçenekleri**: Dinamik Aralık bölümü.<br>**Bölüm sütunu**: verileri bölümlemek için kullanılan sütunu belirtin. Belirtilmemişse, birincil anahtar sütunu kullanılır. |
| Fiziksel bölümlerle özel bir sorgu kullanarak büyük miktarda veri yükleyin. | **Bölüm seçeneği**: tablonun fiziksel bölümleri.<br>**Sorgu**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Bölüm adı**: Verilerin kopyalanacağı bölüm adlarını belirtin. Belirtilmezse, Data Factory Oracle veri kümesinde belirttiğiniz tablodaki fiziksel bölümleri otomatik olarak algılar.<br><br>Yürütme sırasında, Data Factory `?AdfTabularPartitionName` gerçek bölüm adıyla değiştirir ve Oracle 'a gönderir. |
| Veri bölümlendirme için bir tamsayı sütunu ile, fiziksel bölümler olmadan, özel bir sorgu kullanarak büyük miktarda veri yükleyin. | **Bölüm seçenekleri**: Dinamik Aralık bölümü.<br>**Sorgu**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Bölüm sütunu**: verileri bölümlemek için kullanılan sütunu belirtin. Tamsayı veri türünde bir sütuna göre bölümleyebilirsiniz.<br>**Bölüm üst sınırı** ve **bölüm alt sınırı**: yalnızca alt ve üst Aralık arasında veri almak için bölüm sütununa karşı filtrelemek istediğinizi belirtin.<br><br>Yürütme sırasında, Data Factory `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`ve `?AdfRangePartitionLowbound` her bölüm için gerçek sütun adı ve değer aralıklarıyla değiştirir ve Oracle 'a gönderir. <br>Örneğin, "ID" adlı bölüm sütununuzu alt sınır 1 ve üst sınır 80 olarak ayarlandıysa, paralel kopyalama 4 olarak ayarlanır Data Factory verileri 4 bölümden alır. Kimlikleri sırasıyla [1, 20], [21, 40], [41, 60] ve [61, 80] arasındadır. |

**Örnek: fiziksel bölüm ile sorgulama**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Örnek: Dinamik Aralık bölümü ile sorgulama**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Oracle için veri türü eşlemesi

Ve Oracle 'a veri kopyaladığınızda aşağıdaki eşlemeler geçerlidir. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md).

| Oracle veri türü | Data Factory geçici veri türü |
|:--- |:--- |
| BDosya |Byte [] |
| BLOB |Byte []<br/>(yalnızca Oracle 10G ve üzeri sürümlerde desteklenir) |
| CHAR |Dize |
| CLOB |Dize |
| DATE |DateTime |
| FLOAT |Ondalık, dize (duyarlık > 28) |
| INTEGER |Ondalık, dize (duyarlık > 28) |
| KALACAĞıNı |Dize |
| UZUN HAM |Byte [] |
| NCHAR |Dize |
| NCLOB |Dize |
| SAYıSıNDAN |Ondalık, dize (duyarlık > 28) |
| NVARCHAR2 |Dize |
| Madde |Byte [] |
| ROWıD |Dize |
| ILIŞKIN |DateTime |
| YEREL SAAT DILIMIYLE ZAMAN DAMGASı |Dize |
| SAAT DILIMI ILE ZAMAN DAMGASı |Dize |
| IŞARETSIZ TAMSAYı |Sayı |
| VARCHAR2 |Dize |
| 'SINI |Dize |

> [!NOTE]
> Veri türleri zaman ARALıĞı ve Aralık gün/sanıye desteklenmez.

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar
Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md##supported-data-stores-and-formats).
