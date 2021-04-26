---
title: Azure Data Factory kullanarak Oracle 'a ve veri kopyalama
description: Data Factory kullanarak, desteklenen kaynak depolardaki verileri bir Oracle veritabanına veya Oracle 'dan desteklenen havuz depolarına kopyalamayı öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: jingwang
ms.openlocfilehash: 9e6be88af13d5dd7ddceba32ec08cab54ca5e3a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587296"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Azure Data Factory kullanarak verileri ve Oracle 'a kopyalama

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-onprem-oracle-connector.md)
> * [Güncel sürüm](connector-oracle.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, verileri bir Oracle veritabanından ve ' den kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)sayfasında oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Oracle Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Bir Oracle veritabanından desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Ayrıca, desteklenen herhangi bir kaynak veri deposundan verileri bir Oracle veritabanına kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak veya havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu Oracle Bağlayıcısı şunları destekler:

- Bir Oracle veritabanının aşağıdaki sürümleri:
    - Oracle 19c R1 (19,1) ve üzeri
    - Oracle 18c R1 (18,1) ve üzeri
    - Oracle 12c R1 (12,1) ve üzeri
    - Oracle 11g R1 (11,1) ve üzeri
    - Oracle 10G R1 (10,1) ve üzeri
    - Oracle 9i R2 (9,2) ve üzeri
    - Oracle 8i R3 (8.1.7) ve üzeri
    - Oracle Database Cloud Sınavveri hizmeti
- Oracle kaynağından paralel kopyalama. Ayrıntılar için [Oracle 'Dan paralel kopyalama](#parallel-copy-from-oracle) bölümüne bakın.

> [!Note]
> Oracle proxy sunucusu desteklenmez.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Integration Runtime, yerleşik bir Oracle sürücüsü sağlar. Bu nedenle, ve Oracle 'dan veri kopyaladığınızda sürücüyü el ile yüklemeniz gerekmez.

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, Oracle bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılar sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Oracle bağlı hizmeti aşağıdaki özellikleri destekler:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği **Oracle** olarak ayarlanmalıdır. | Yes |
| Dizisi | Oracle Database örneğine bağlanmak için gereken bilgileri belirtir. <br/>Ayrıca Azure Key Vault bir parola yerleştirebilir ve `password` yapılandırmayı bağlantı dizesinin dışına çekebilirsiniz. Daha ayrıntılı bilgi için aşağıdaki örneklere bakın ve [kimlik bilgilerini Azure Key Vault depolayın](store-credentials-in-key-vault.md) . <br><br>**Desteklenen bağlantı türü**: veritabanınızı tanımlamak IÇIN **Oracle SID** veya **Oracle hizmet adını** kullanabilirsiniz:<br>-SID kullanıyorsanız: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>-Hizmet adı kullanıyorsanız: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Gelişmiş Oracle yerel bağlantı seçenekleri için, tnsnames ' de bir giriş eklemeyi seçebilirsiniz [. ](http://www.orafaq.com/wiki/Tnsnames.ora) , Oracle sunucusunda ora dosyası ve ADF Oracle bağlantılı hizmetinde, Oracle hizmet adı bağlantı türünü kullanmayı ve ilgili hizmet adını yapılandırmayı seçin. | Yes |
| connectVia | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanılır. |No |

>[!TIP]
>Bir hata alırsanız, "ORA-01025: UPı parametresi aralık dışında" ve Oracle sürümünüz 8i ise, `WireProtocolMode=1` Bağlantı dizenizi ekleyin. Sonra yeniden deneyin.

Yük devretme senaryosu için birden çok Oracle örneğinize sahipseniz, Oracle bağlı hizmeti oluşturabilir, birincil ana bilgisayar, bağlantı noktası, Kullanıcı adı, parola vb. gibi yeni bir "**ek bağlantı özellikleri**" ekleyebilirsiniz ve bu `AlternateServers` değer, `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)` ayraçları kaçırmayın ve iki nokta üst üste ( `:` ) ayırıcı olarak dikkat edin. Örnek olarak, diğer sunucuların aşağıdaki değeri bağlantı yük devretmesi için iki alternatif veritabanı sunucusu tanımlar: `(HostName=AccountingOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)` .

Büyük/küçük harf olarak bağlantı dizesinde ayarlayabileceğiniz daha fazla bağlantı özelliği:

| Özellik | Açıklama | İzin verilen değerler |
|:--- |:--- |:--- |
| Dizi boyutu |Bağlayıcının tek bir ağ gidiş yolculuğuna getirileceği bayt sayısı. Ör., `ArraySize=‭10485760‬` .<br/><br/>Daha büyük değerler, ağ üzerinden veri getirme sayısını azaltarak aktarım hızını artırır. Daha küçük değerler yanıt süresini arttırır, çünkü sunucunun veri aktarmasını bekleyen bir gecikme vardır. | 1 ila 4294967296 (4 GB) arasında bir tamsayı. Varsayılan değer `60000` olarak belirlenmiştir. 1 değeri bayt sayısını tanımlamaz, ancak tam olarak bir veri satırı için alan ayırmayı gösterir. |

Oracle bağlantısı üzerinde şifrelemeyi etkinleştirmek için iki seçeneğiniz vardır:

-   **Üçlü DES şifreleme (3DES) ve Gelişmiş Şifreleme Standardı (AES)** kullanmak için Oracle sunucu tarafında, Oracle gelişmiş güvenlik (OAS) bölümüne gidin ve şifreleme ayarlarını yapılandırın. Ayrıntılar için bkz. [Oracle belgeleri](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Oracle uygulama geliştirme çerçevesi (ADF) Bağlayıcısı, bir Oracle bağlantısı kurarken OAS 'de yapılandırdığınız birini kullanmak üzere şifreleme yöntemini otomatik olarak görüşür.

-   **TLS** kullanmak için:

    1.  TLS/SSL sertifika bilgilerini alın. TLS/SSL sertifikalarınızın Distinguished Encoding Rules (DER) ile kodlanmış sertifika bilgilerini alın ve çıktıyı kaydedin (-----sertifikayı Başlat... -----) Bir metin dosyası olarak son sertifika.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Örnek:** DERcert. cer dosyasından sertifika bilgilerini ayıklayın ve ardından çıktıyı cert.txt kaydedin.

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
    
    2.  Veya oluşturun `keystore` `truststore` . Aşağıdaki komut, `truststore` PKCS-12 biçiminde bir parola ile veya parolasız dosya oluşturur.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Örnek:** `truststore` Parola Ile MyTrustStoreFile adlı BIR PKCS12 dosyası oluşturun.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  `truststore`Dosyayı kendi kendine BARıNDıRıLAN IR makinesine yerleştirin. Örneğin, dosyayı C:\mytruststorefilekonumundaki yere yerleştirin.
    4.  Azure Data Factory ' de Oracle bağlantı dizesini `EncryptionMethod=1` ve karşılık gelen `TrustStore` / `TrustStorePassword` değeri yapılandırın. Örneğin, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Örnek:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Ve Oracle 'dan verileri kopyalamak için, veri kümesinin Type özelliğini olarak ayarlayın `OracleTable` . Aşağıdaki özellikler desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Veri kümesinin Type özelliği olarak ayarlanmalıdır `OracleTable` . | Yes |
| schema | Şemanın adı. |Kaynak için Hayır, havuz için Evet  |
| tablo | Tablo/görünüm adı. |Kaynak için Hayır, havuz için Evet  |
| tableName | Şema ile tablonun/görünümün adı. Bu özellik geriye dönük uyumluluk için desteklenir. Yeni iş yükü için `schema` ve kullanın `table` . | Kaynak için Hayır, havuz için Evet |

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

Oracle 'dan veri kopyalamak için kopyalama etkinliğindeki kaynak türünü olarak ayarlayın `OracleSource` . Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği olarak ayarlanmalıdır `OracleSource` . | Yes |
| oracleReaderQuery | Verileri okumak için özel SQL sorgusunu kullanın. `"SELECT * FROM MyTable"` bunun bir örneğidir.<br>Bölümlenmiş yükü etkinleştirdiğinizde, sorgunuza karşılık gelen yerleşik bölüm parametrelerini de eklemeniz gerekir. Örnekler için [Oracle 'Dan paralel kopyalama](#parallel-copy-from-oracle) bölümüne bakın. | No |
| partitionOptions | Oracle 'dan veri yüklemek için kullanılan veri bölümleme seçeneklerini belirtir. <br>İzin verilen değerler: **none** (default), **Physicalpartitionsoftable** ve **DynamicRange**.<br>Bir bölüm seçeneği etkinleştirildiğinde (yani, `None` ), bir Oracle veritabanından eşzamanlı olarak veri yükleme derecesi, [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayarla birlikte denetlenir. | No |
| partitionSettings | Veri bölümleme için ayarların grubunu belirtin. <br>Bölüm seçeneği olmadığında uygulayın `None` . | No |
| partitionNames | Kopyalanması gereken fiziksel bölümlerin listesi. <br>Bölüm seçeneği olduğunda uygulayın `PhysicalPartitionsOfTable` . Kaynak verileri almak için bir sorgu kullanırsanız, `?AdfTabularPartitionName` WHERE yan tümcesinde kanca. Örnek için [Oracle 'Dan paralel kopyalama](#parallel-copy-from-oracle) bölümüne bakın. | No |
| partitionColumnName | Paralel kopya için Aralık bölümleme tarafından kullanılacak, **tamsayı türünde** kaynak sütunun adını belirtin. Belirtilmemişse, tablonun birincil anahtarı otomatik olarak algılanır ve bölüm sütunu olarak kullanılır. <br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Kaynak verileri almak için bir sorgu kullanırsanız,  `?AdfRangePartitionColumnName` WHERE yan tümcesinde kanca. Örnek için [Oracle 'Dan paralel kopyalama](#parallel-copy-from-oracle) bölümüne bakın. | No |
| Partitionüstsınırı | Verilerin kopyalanacağı bölüm sütununun en büyük değeri. <br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Kaynak verileri almak için bir sorgu kullanırsanız, `?AdfRangePartitionUpbound` WHERE yan tümcesinde kanca. Örnek için [Oracle 'Dan paralel kopyalama](#parallel-copy-from-oracle) bölümüne bakın. | No |
| Partitionalme sınırı | Verilerin kopyalanacağı bölüm sütununun en küçük değeri. <br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Kaynak verileri almak için bir sorgu kullanırsanız, `?AdfRangePartitionLowbound` WHERE yan tümcesinde kanca. Örnek için [Oracle 'Dan paralel kopyalama](#parallel-copy-from-oracle) bölümüne bakın. | No |

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

Verileri Oracle 'a kopyalamak için kopyalama etkinliğindeki havuz türünü olarak ayarlayın `OracleSink` . Aşağıdaki özellikler, etkinlik **havuzunu** Kopyala bölümünde desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği havuzunun Type özelliği olarak ayarlanmalıdır `OracleSink` . | Yes |
| writeBatchSize | Arabellek boyutu ulaştığında SQL tablosuna veri ekler `writeBatchSize` .<br/>İzin verilen değerler Integer (satır sayısı). |Hayır (varsayılan değer 10.000) |
| writeBatchTimeout | Toplu iş ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi.<br/>İzin verilen değerler TimeSpan. Örnek olarak 00:30:00 (30 dakika). | No |
| Ön Copyscrıpt | Kopyalama etkinliği için, her çalıştırmada verileri Oracle 'a yazmadan önce çalıştırılacak bir SQL sorgusu belirtin. Bu özelliği, önceden yüklenmiş verileri temizlemek için kullanabilirsiniz. | No |
| maxConcurrentConnections |Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No |

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

Bölümlenmiş kopyayı etkinleştirdiğinizde Data Factory verileri bölümlere göre yüklemek için Oracle kaynağınızdan paralel sorgular çalıştırır. Paralel derece [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayar tarafından denetlenir. Örneğin, `parallelCopies` dört olarak ayarlarsanız, Data Factory aynı anda, belirtilen bölüm seçeneğiniz ve ayarlarınıza göre dört sorgu üretir ve çalışır ve her sorgu Oracle veritabanınızdaki verilerin bir kısmını alır.

Oracle veritabanınızdan büyük miktarda veri yüklediğinizde özellikle veri bölümleme ile paralel kopyayı etkinleştirmeniz önerilir. Farklı senaryolar için önerilen yapılandırma aşağıda verilmiştir. Dosya tabanlı veri deposuna veri kopyalarken, bir klasöre birden çok dosya (yalnızca klasör adını belirt) olarak yazma işlemi geri çağrılır, bu durumda performans tek bir dosyaya yazılmasından daha iyidir.

| Senaryo                                                     | Önerilen ayarlar                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fiziksel bölümlerle büyük tablodan tam yük.          | **Bölüm seçeneği**: tablonun fiziksel bölümleri. <br><br/>Yürütme sırasında Data Factory fiziksel bölümleri otomatik olarak algılar ve verileri bölümlere göre kopyalar. |
| Fiziksel bölümler olmadan, veri bölümlendirme için bir tamsayı sütunuyla birlikte büyük tablodan tam yük. | **Bölüm seçenekleri**: Dinamik Aralık bölümü.<br>**Bölüm sütunu**: verileri bölümlemek için kullanılan sütunu belirtin. Belirtilmemişse, birincil anahtar sütunu kullanılır. |
| Fiziksel bölümlerle özel bir sorgu kullanarak büyük miktarda veri yükleyin. | **Bölüm seçeneği**: tablonun fiziksel bölümleri.<br>**Sorgu**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>` .<br>**Bölüm adı**: Verilerin kopyalanacağı bölüm adlarını belirtin. Belirtilmezse, Data Factory Oracle veri kümesinde belirttiğiniz tablodaki fiziksel bölümleri otomatik olarak algılar.<br><br>Yürütme sırasında Data Factory, `?AdfTabularPartitionName` gerçek bölüm adıyla değiştirilir ve Oracle 'a gönderilir. |
| Veri bölümlendirme için bir tamsayı sütunu ile, fiziksel bölümler olmadan, özel bir sorgu kullanarak büyük miktarda veri yükleyin. | **Bölüm seçenekleri**: Dinamik Aralık bölümü.<br>**Sorgu**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Bölüm sütunu**: verileri bölümlemek için kullanılan sütunu belirtin. Tamsayı veri türünde bir sütuna göre bölümleyebilirsiniz.<br>**Bölüm üst sınırı** ve **bölüm alt sınırı**: yalnızca alt ve üst Aralık arasında veri almak için bölüm sütununa karşı filtrelemek istediğinizi belirtin.<br><br>Yürütme sırasında Data Factory, `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound` , ve `?AdfRangePartitionLowbound` değerlerini her bölüm için gerçek sütun adı ve değer aralıklarıyla değiştirir ve Oracle 'a gönderir. <br>Örneğin, "ID" adlı bölüm sütununuzu alt sınır 1 ve üst sınır 80 olarak ayarlandıysa, paralel kopyalama 4 olarak ayarlanır Data Factory verileri 4 bölümden alır. Kimlikleri sırasıyla [1, 20], [21, 40], [41, 60] ve [61, 80] arasındadır. |

> [!TIP]
> Verileri bölümlendirilmemiş bir tablodan kopyalarken, bir tamsayı sütununa göre bölümlemek için "Dinamik Aralık" Bölüm seçeneğini kullanabilirsiniz. Kaynak verilerinizde böyle bir sütun türü yoksa, bir sütun oluşturmak ve bölüm sütunu olarak kullanmak için kaynak sorgudaki [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm) işlevden yararlanabilirsiniz.

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
| Bun |Byte []<br/>(yalnızca Oracle 10G ve üzeri sürümlerde desteklenir) |
| CHAR |Dize |
| CLOB |Dize |
| DATE |DateTime |
| FLOAT |Ondalık, dize (duyarlık > 28) |
| TAMSAYI |Ondalık, dize (duyarlık > 28) |
| KALACAĞıNı |Dize |
| UZUN HAM |Byte [] |
| NCHAR |Dize |
| NCLOB |Dize |
| SAYı (p, s) |Decimal, String (Eğer p > 28) |
| Duyarlık ve ölçek olmadan sayı |Çift |
| NVARCHAR2 |Dize |
| Madde |Byte [] |
| ROWıD |Dize |
| TIMESTAMP |DateTime |
| YEREL SAAT DILIMIYLE ZAMAN DAMGASı |Dize |
| SAAT DILIMI ILE ZAMAN DAMGASı |Dize |
| IŞARETSIZ TAMSAYı |Sayı |
| VARCHAR2 |Dize |
| XML |Dize |

> [!NOTE]
> Veri türleri zaman ARALıĞı ve Aralık gün/sanıye desteklenmez.

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar
Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
