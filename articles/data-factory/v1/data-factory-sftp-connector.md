---
title: Azure Data Factory kullanarak SFTP sunucusundan verileri taşıma
description: Azure Data Factory kullanarak şirket içi veya bulut SFTP sunucusundan veri taşıma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1a75b3af46d79cc7a028fa5d36ef1653b1619e8d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682337"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Azure Data Factory kullanarak bir SFTP sunucusundan veri taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-sftp-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-sftp.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de Sftpconnector](../connector-sftp.md).

Bu makalede, verileri şirket içi/bulut SFTP sunucusundan desteklenen bir havuz veri deposuna taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Bu makalede, kopyalama etkinliğine sahip veri hareketine ve kaynak/havuz olarak desteklenen veri depolarının listesine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesi oluşturulur.

Data Factory Şu anda yalnızca bir SFTP sunucusundan diğer veri depolarına veri taşımayı destekler, ancak diğer veri depolarından verileri bir SFTP sunucusuna taşımamaktadır. Hem şirket içi hem de bulut SFTP sunucularını destekler.

> [!NOTE]
> Kopyalama etkinliği, kaynak dosyayı başarıyla hedefe kopyalandıktan sonra silmez. Başarılı bir kopyadan sonra kaynak dosyayı silmeniz gerekiyorsa, dosyayı silmek ve işlem hattındaki etkinliği kullanmak için özel bir etkinlik oluşturun.

## <a name="supported-scenarios-and-authentication-types"></a>Desteklenen senaryolar ve kimlik doğrulama türleri
Bu SFTP bağlayıcısını, **hem Cloud SFTP sunucularından hem de şirket ıçı SFTP sunucularından**veri kopyalamak için kullanabilirsiniz. SFTP sunucusuna bağlanırken **temel** ve **sshpublickey** kimlik doğrulama türleri desteklenir.

Şirket içi bir SFTP sunucusundan veri kopyalarken şirket içi ortama/Azure VM 'ye bir Veri Yönetimi ağ geçidi yüklemeniz gerekir. Ağ Geçidi hakkındaki ayrıntılar için bkz. [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md) . Ağ geçidini ayarlamaya ve kullanmaya ilişkin adım adım yönergeler için bkz. [Şirket içi konumlar ve bulut makalesi arasında verileri taşıma](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API 'Ler kullanarak bir SFTP kaynağından veri taşıyan kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz.

- İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir yol için bkz. [öğretici: kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) .

- İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . Verileri SFTP sunucusundan Azure Blob depolamaya kopyalamak için JSON örnekleri için, bu makaledeki [JSON örneği: SFTP sunucusundan Azure Blob 'a veri kopyalama](#json-example-copy-data-from-sftp-server-to-azure-blob) bölümüne bakın.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Aşağıdaki tabloda, FTP bağlantılı hizmetine özgü JSON öğeleri için açıklama verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type | Tür özelliği `Sftp`olarak ayarlanmalıdır. |Evet |
| Konağının | SFTP sunucusunun adı veya IP adresi. |Evet |
| port |SFTP sunucusunun dinlediği bağlantı noktası. Varsayılan değer: 21 |Hayır |
| authenticationType |Kimlik doğrulama türünü belirtin. İzin verilen değerler: **temel**, **sshpublickey**. <br><br> [Temel kimlik doğrulamasını kullanma](#using-basic-authentication) ve sırasıyla daha fazla ÖZELLIK ve JSON ÖRNEKLERI üzerinde [SSH ortak anahtar kimlik doğrulama bölümlerini kullanma](#using-ssh-public-key-authentication) bölümüne bakın. |Evet |
| skipHostKeyValidation | Konak anahtarı doğrulamanın atlanıp atlanmayacağını belirtin. | Hayır. Varsayılan değer: false |
| hostKeyFingerprint | Ana bilgisayar anahtarının parmak yazdırma türünü belirtin. | `skipHostKeyValidation` false olarak ayarlandıysa Evet.  |
| gatewayName |Şirket içi bir SFTP sunucusuna bağlanmak için Veri Yönetimi ağ geçidinin adı. | Şirket içi bir SFTP sunucusundan veri kopyalandıysanız Evet. |
| encryptedCredential | SFTP sunucusuna erişmek için şifrelenmiş kimlik bilgileri. Kopyalama sihirbazında veya ClickOnce açılan iletişim kutusunda temel kimlik doğrulaması (Kullanıcı adı + parola) veya SshPublicKey kimlik doğrulaması (Kullanıcı adı + özel anahtar yolu veya içerik) belirttiğinizde otomatik olarak üretilir. | Hayır. Yalnızca şirket içi bir SFTP sunucusundan veri kopyalarken geçerlidir. |

### <a name="using-basic-authentication"></a>Temel kimlik doğrulaması kullanma

Temel kimlik doğrulaması kullanmak için `authenticationType` ' ı `Basic` olarak ayarlayın ve en son bölümde tanıtılan SFTP Bağlayıcısı genel 'in yanı sıra aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| kullanıcı adı | SFTP sunucusuna erişimi olan kullanıcı. |Evet |
| password | Kullanıcı için parola (Kullanıcı adı). | Evet |

#### <a name="example-basic-authentication"></a>Örnek: temel kimlik doğrulaması
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Örnek: şifreli kimlik bilgileri ile temel kimlik doğrulaması

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>SSH ortak anahtar kimlik doğrulamasını kullanma

SSH ortak anahtar kimlik doğrulamasını kullanmak için, `authenticationType` `SshPublicKey`olarak ayarlayın ve en son bölümde tanıtılan SFTP Bağlayıcısı genel 'in yanı sıra aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| kullanıcı adı |SFTP sunucusuna erişimi olan Kullanıcı |Evet |
| privateKeyPath | Ağ geçidinin erişebileceği özel anahtar dosyasının mutlak yolunu belirtin. | `privateKeyPath` ya da `privateKeyContent`belirtin. <br><br> Yalnızca şirket içi bir SFTP sunucusundan veri kopyalarken geçerlidir. |
| privateKeyContent | Özel anahtar içeriğinin seri hale getirilmiş dizesi. Kopyalama Sihirbazı özel anahtar dosyasını okuyabilir ve özel anahtar içeriğini otomatik olarak ayıklayabilir. Başka bir aracı/SDK kullanıyorsanız, bunun yerine privateKeyPath özelliğini kullanın. | `privateKeyPath` ya da `privateKeyContent`belirtin. |
| Deyimi | Anahtar dosyası bir pass ifadesi tarafından korunuyorsa, özel anahtarın şifresini çözmek için geçiş tümceciğini/parolayı belirtin. | Özel anahtar dosyası bir pass ifadesi tarafından korunuyorsa Evet. |

> [!NOTE]
> SFTP Bağlayıcısı RSA/DSA OpenSSH anahtarını destekler. Anahtar dosyası içeriğinizin "-----BEGIN [RSA/DSA] özel anahtar-----" ile başlayıp başlamadığına emin olun. Özel anahtar dosyası bir PPK biçimli dosya ise, lütfen. PPK 'den OpenSSH biçimine dönüştürmek için Putty aracını kullanın.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Örnek: özel anahtar filePath kullanarak SshPublicKey kimlik doğrulaması

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Örnek: özel anahtar içeriğini kullanarak SshPublicKey kimlik doğrulaması

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik özellikler & bölümlerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri için benzerdir.

**Typeproperties** bölümü her bir veri kümesi türü için farklıdır. Veri kümesi türüne özgü bilgiler sağlar. **FileShare** veri kümesi türündeki bir veri kümesinin typeproperties bölümü aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| folderPath |Klasörün alt yolu. Dizedeki özel karakterler için ' \ ' kaçış karakterini kullanın. Örnekler için bkz. örnek bağlantılı hizmet ve veri kümesi tanımları.<br/><br/>Bu özelliği, dilim başlangıç/bitiş tarihi-saati temelinde klasör yolları sağlamak için **Partitionby** ile birleştirebilirsiniz. |Evet |
| fileName |Tablonun klasördeki belirli bir dosyaya başvurmasını istiyorsanız, **FolderPath** içindeki dosyanın adını belirtin. Bu özellik için herhangi bir değer belirtmezseniz tablo, klasördeki tüm dosyaları gösterir.<br/><br/>Bir çıkış veri kümesi için dosya adı belirtilmediğinde, oluşturulan dosyanın adı şu biçimde olacaktır: <br/><br/>`Data.<Guid>.txt` (örnek: Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt |Hayır |
| fileFilter |Tüm dosyalar yerine folderPath içindeki dosyaların bir alt kümesini seçmek için kullanılacak bir filtre belirtin.<br/><br/>İzin verilen değerler: `*` (birden fazla karakter) ve `?` (tek karakter).<br/><br/>Örnekler 1: `"fileFilter": "*.log"`<br/>Örnek 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter, bir giriş FileShare veri kümesi için geçerlidir. Bu özellik,. |Hayır |
| partitionedBy |partitionedBy, zaman serisi verilerine yönelik bir dinamik folderPath, filename belirtmek için kullanılabilir. Örneğin, her saat veri için folderPath parametreli parametrelenir. |Hayır |
| formatını | Şu biçim türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. Biçim ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](data-factory-supported-file-and-compression-formats.md#text-format), [JSON biçimi](data-factory-supported-file-and-compression-formats.md#json-format), [avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [orc biçimi](data-factory-supported-file-and-compression-formats.md#orc-format)ve [Parquet biçim](data-factory-supported-file-and-compression-formats.md#parquet-format) bölümleri. <br><br> Dosyaları dosya tabanlı mağazalar (ikili kopya) arasında **olduğu gibi kopyalamak** istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın. |Hayır |
| masıyla | Verilerin türünü ve sıkıştırma düzeyini belirtin. Desteklenen türler şunlardır: **gzip**, **söndür**, **bzip2**ve **zipsöndür**. Desteklenen düzeyler şunlardır: **en iyi** ve **en hızlı**. Daha fazla bilgi için bkz. [Azure Data Factory dosya ve sıkıştırma biçimleri](data-factory-supported-file-and-compression-formats.md#compression-support). |Hayır |
| useBinaryTransfer |Ikili aktarım modunu kullanıp kullanmayacağınızı belirtin. İkili mod ve yanlış ASCII için true. Varsayılan değer: true. Bu özellik yalnızca, ilişkili bağlı hizmet türü: FtpServer türünde olduğunda kullanılabilir. |Hayır |

> [!NOTE]
> filename ve fileFilter aynı anda kullanılamaz.

### <a name="using-partionedby-property"></a>PartionedBy özelliği kullanma
Önceki bölümde belirtildiği gibi, partitionedBy ile zaman serisi verileri için dinamik bir folderPath, filename belirtebilirsiniz. Bunu, belirli bir veri dilimi için mantıksal zaman dilimini belirten Data Factory makroları ve sistem değişkeni Dilimestart ' ı ile yapabilirsiniz.

Zaman serisi veri kümeleri, zamanlama ve dilimler hakkında bilgi edinmek için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md), [& yürütmeyi zamanlama](data-factory-scheduling-and-execution.md)ve işlem [hattı makaleleri oluşturma](data-factory-create-pipelines.md) .

#### <a name="sample-1"></a>Örnek 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Bu örnekte, {Slice}, belirtilen biçimde (YYYYMMDDHH) Data Factory sistem değişkeni değeri ile değiştirilmiştir. Dilimcstart, dilimin başlangıç zamanına başvurur. FolderPath her bir dilim için farklıdır. Örnek: wikidatagateway/wisvahili amptadataout/2014100103 veya wikidatagateway/wisvahili amptadataout/2014100104.

#### <a name="sample-2"></a>Örnek 2:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Bu örnekte, monthestart 'ın Year, month, Day ve Time, folderPath ve fileName özellikleri tarafından kullanılan ayrı değişkenlere ayıklanır.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölüm & özelliklerinin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları ve ilkeler gibi özellikler, tüm etkinlik türleri için kullanılabilir.

Ancak, etkinliğin typeProperties bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için tür özellikleri, kaynak ve havuz türlerine göre değişir.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Desteklenen dosya ve sıkıştırma biçimleri
Ayrıntılar hakkında [Azure Data Factory makalesinde dosya ve sıkıştırma biçimlerine](data-factory-supported-file-and-compression-formats.md) bakın.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>JSON örneği: SFTP sunucusundan Azure Blob 'a veri kopyalama
Aşağıdaki örnek, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Verilerin SFTP kaynağından Azure Blob depolama alanına nasıl kopyalanacağını gösterir. Ancak, veriler, Azure Data Factory ' deki kopyalama etkinliği kullanılarak, [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) belirtilen herhangi bir kaynaktan herhangi bir kaynağa **doğrudan** kopyalanabilir.

> [!IMPORTANT]
> Bu örnek, JSON parçacıkları sağlar. Veri Fabrikası oluşturmaya yönelik adım adım yönergeler içermez. Adım adım yönergeler için bkz. [Şirket içi konumlar ve bulut makalesi arasında verileri taşıma](data-factory-move-data-between-onprem-and-cloud.md) .

Örnek, aşağıdaki Data Factory varlıklarına sahiptir:

* [SFTP](#linked-service-properties)türünde bağlı bir hizmet.
* [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
* [FileShare](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
* [Filesystemsource](#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, verileri bir SFTP sunucusundan her saat bir Azure blobuna kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

**SFTP bağlı hizmeti**

Bu örnek, düz metin olarak Kullanıcı adı ve parolasıyla temel kimlik doğrulamasını kullanır. Aşağıdaki yollarla da kullanabilirsiniz:

* Şifrelenmiş kimlik bilgileriyle temel kimlik doğrulaması
* SSH ortak anahtar kimlik doğrulaması

Kullanabileceğiniz farklı kimlik doğrulama türleri için bkz. [FTP bağlı hizmeti](#linked-service-properties) bölümü.

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Azure Storage bağlı hizmeti**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**SFTP giriş veri kümesi**

Bu veri kümesi, `mysharedfolder` ve dosya `test.csv`SFTP klasörünü ifade eder. İşlem hattı, dosyayı hedefe kopyalar.

"External": "true" ayarı, veri kümesinin veri fabrikasında dış olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini Data Factory hizmetine bildirir.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Azure Blob çıktı veri kümesi**

Veriler her saat yeni bir bloba yazılır (sıklık: saat, Aralık: 1). Blob 'un klasör yolu, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısımlarını kullanır.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopyalama etkinliği içeren işlem hattı**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. İşlem hattı JSON tanımında **kaynak** türü, **filesystemsource** olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .

## <a name="next-steps"></a>Sonraki Adımlar
Aşağıdaki makalelere bakın:

* Kopyalama etkinliği ile işlem hattı oluşturmaya yönelik adım adım yönergeler için [etkinlik öğreticisini kopyalayın](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .
