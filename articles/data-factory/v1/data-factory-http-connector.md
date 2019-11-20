---
title: Verileri bir HTTP kaynağından taşıma-Azure
description: Azure Data Factory kullanarak şirket içi veya bulut HTTP kaynağından veri taşımayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4133393b7b21394ea397598a5e1651ee370f92f0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682505"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Azure Data Factory kullanarak bir HTTP kaynağından veri taşıma

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-http-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-http.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Azure Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de http Bağlayıcısı](../connector-http.md).


Bu makalede, verileri şirket içi veya bulut HTTP uç noktasından desteklenen bir havuz veri deposuna taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Bu makalede, kopyalama etkinliğini kullanarak veri hareketine genel bir bakış sunan [kopyalama etkinliğini kullanarak verileri taşıma](data-factory-data-movement-activities.md)hakkında bilgi oluşturulur. Makalede ayrıca kopyalama etkinliğinin kaynak ve havuz olarak desteklediği veri depoları listelenmektedir.

Data Factory Şu anda yalnızca bir HTTP kaynağından diğer veri depolarına veri taşımayı desteklemektedir. Diğer veri depolarından bir HTTP hedefine veri taşımayı desteklemez.

## <a name="supported-scenarios-and-authentication-types"></a>Desteklenen senaryolar ve kimlik doğrulama türleri

HTTP **Get** veya **Post** yöntemlerini kullanarak *hem buluttan hem de şirket içi http/S uç NOKTASıNDAN* veri almak için bu http bağlayıcısını kullanabilirsiniz. Aşağıdaki kimlik doğrulama türleri desteklenir: **Anonymous**, **Basic**, **Digest**, **Windows**ve **ClientCertificate**. Bu bağlayıcı ile [Web tablosu Bağlayıcısı](data-factory-web-table-connector.md)arasındaki farkı aklınızda edin. Web tablosu Bağlayıcısı bir HTML Web sayfasından tablo içeriğini ayıklar.

Şirket içi HTTP uç noktasından veri kopyaladığınızda, şirket içi ortama veya bir Azure VM 'ye Veri Yönetimi ağ geçidi yüklemelisiniz. Veri Yönetimi ağ geçidi hakkında bilgi edinmek ve ağ geçidini ayarlama hakkında adım adım yönergeler için bkz. [Şirket içi konumlar ve bulut arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Başlarken

Farklı araçları veya API 'Leri kullanarak bir HTTP kaynağından veri taşımak için kopyalama etkinliğine sahip bir işlem hattı oluşturabilirsiniz:

- İşlem hattı oluşturmanın en kolay yolu Veri Kopyalama Sihirbazı ' nı kullanmaktır. Veri Kopyalama Sihirbazı 'nı kullanarak bir işlem hattı oluşturmaya yönelik hızlı bir anlatım için bkz. [öğretici: kopyalama sihirbazını kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md).

- Bir işlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**veya **REST API**. Kopyalama etkinliğine sahip bir işlem hattının nasıl oluşturulacağı hakkında adım adım yönergeler için, [kopyalama etkinliği öğreticisine](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)bakın. Bir HTTP kaynağından Azure Blob depolama alanına veri kopyalamanın JSON örnekleri için bkz. [JSON örnekleri](#json-examples).

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki tabloda, HTTP bağlantılı hizmetine özgü JSON öğeleri açıklanmaktadır:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type | **Type** özelliği **http**olarak ayarlanmalıdır. | Evet |
| url | Web sunucusunun temel URL 'SI. | Evet |
| authenticationType | Kimlik doğrulama türünü belirtir. İzin verilen değerler **anonim**, **temel**, **Özet**, **Windows**ve **ClientCertificate**'tir. <br><br> Daha fazla özellik ve bu kimlik doğrulama türleri için JSON örnekleri için bu makaledeki sonraki bölümlere bakın. | Evet |
| enableServerCertificateValidation | Kaynak bir HTTPS Web sunucusu ise, sunucu SSL sertifika doğrulamasının etkinleştirilip etkinleştirilmeyeceğini belirtir. HTTPS sunucunuz otomatik olarak imzalanan bir sertifika kullandığında, bu **değeri false**olarak ayarlayın. | Hayır<br /> (varsayılan değer **true**'dur) |
| gatewayName | Şirket içi HTTP kaynağına bağlanmak için kullanılacak Veri Yönetimi ağ geçidi örneğinin adı. | Evet, şirket içi bir HTTP kaynağından veri kopyalıyorsanız |
| encryptedCredential | HTTP uç noktasına erişmek için şifrelenmiş kimlik bilgileri. Değer, kopyalama sihirbazında veya **ClickOnce** iletişim kutusunu kullanarak kimlik doğrulama bilgilerini yapılandırdığınızda otomatik olarak oluşturulur. | Hayır<br /> (yalnızca şirket içi HTTP sunucusundan veri kopyaladığınızda geçerlidir) |

Şirket içi HTTP Bağlayıcısı veri kaynağı için kimlik bilgilerini ayarlama hakkında daha fazla bilgi için bkz. [veri yönetimi ağ geçidi kullanarak şirket içi kaynaklar ve bulut arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Temel, Özet veya Windows kimlik doğrulamasını kullanma

**AuthenticationType** 'ı **Basic**, **Digest**veya **Windows**olarak ayarlayın. Önceki bölümlerde açıklanan genel HTTP Bağlayıcısı özelliklerine ek olarak, aşağıdaki özellikleri ayarlayın:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| Nitelen | HTTP uç noktasına erişmek için kullanılacak Kullanıcı adı. | Evet |
| password | Kullanıcının parolası (Kullanıcı**adı**). | Evet |

**Örnek: Basic, Digest veya Windows kimlik doğrulamasını kullanma**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>ClientCertificate kimlik doğrulamasını kullanma

Temel kimlik doğrulamasını kullanmak için **AuthenticationType** ' i **ClientCertificate**olarak ayarlayın. Önceki bölümlerde açıklanan genel HTTP Bağlayıcısı özelliklerine ek olarak, aşağıdaki özellikleri ayarlayın:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| embeddedCertData | PFX dosyasının ikili verilerinin Base64 ile kodlanmış içeriği. | **Embeddedcertdata** veya **certparmak izi** belirtin |
| Certparmak Izi | Ağ Geçidi makinenizin sertifika deposunda yüklü olan sertifikanın parmak izi. Yalnızca şirket içi HTTP kaynağından veri kopyaladığınızda geçerlidir. | **Embeddedcertdata** veya **certparmak izi** belirtin |
| password | Sertifikayla ilişkili parola. | Hayır |

Kimlik doğrulaması için **Certparmak izi** kullanırsanız ve sertifika yerel bilgisayarın Kişisel deposunda yüklüyse, ağ geçidi hizmetine okuma izinleri verin:

1. Microsoft Yönetim Konsolu 'nu (MMC) açın. **Yerel bilgisayarı**hedefleyen **Sertifikalar** ek bileşenini ekleyin.
2. **Sertifikalar** > **Kişisel**' i genişletin ve ardından **Sertifikalar**' ı seçin.
3. Kişisel mağazadan sertifikaya sağ tıklayın ve sonra **özel anahtarları yönet** >**Tüm görevler** ' i seçin.
3. **Güvenlik** sekmesinde, veri yönetimi ağ geçidi konak hizmetinin altında çalıştığı kullanıcı hesabını, sertifikaya okuma erişimi ile ekleyin.  

**Örnek: istemci sertifikası kullanma**

Bu bağlı hizmet, veri fabrikasını şirket içi HTTP Web sunucusuna bağlar. Veri Yönetimi ağ geçidi yüklü olan makinede yüklü bir istemci sertifikası kullanır.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

**Örnek: bir dosyada istemci sertifikası kullanma**

Bu bağlı hizmet, veri fabrikasını şirket içi HTTP Web sunucusuna bağlar. Veri Yönetimi ağ geçidi yüklü olan makinede bir istemci sertifika dosyası kullanır.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Yapı, kullanılabilirlik ve ilke gibi bir veri kümesi JSON dosyasının bazı bölümleri tüm veri kümesi türleri (Azure SQL veritabanı, Azure Blob depolama, Azure Tablo Depolaması) için benzerdir.

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md).

**Typeproperties** bölümü her bir veri kümesi türü için farklıdır. **Typeproperties** bölümü, veri deposundaki verilerin konumu hakkında bilgi sağlar. **Http** türünün bir veri kümesi Için **typeproperties** bölümü aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Veri kümesinin **türü** **http**olarak ayarlanmalıdır. | Evet |
| relativeUrl 'Si | Verileri içeren kaynağın göreli URL 'SI. Yol belirtilmediğinde, yalnızca bağlı hizmet tanımında belirtilen URL kullanılır. <br><br> Dinamik bir URL oluşturmak için [Data Factory işlevleri ve sistem değişkenleri](data-factory-functions-variables.md)kullanabilirsiniz. Örnek: **relativeUrl**: **$ $Text. Format ('/My/Report? month = {0: yyyy}-{0: mm} & fmt = CSV ',, daBaşlat)** . | Hayır |
| requestMethod | HTTP yöntemi. İzin verilen değerler **Al** ve **Postala**. | Hayır <br />(varsayılan değer **Al**) |
| additionalHeaders | Ek HTTP istek üstbilgileri. | Hayır |
| Istek gövdesi | HTTP isteğinin gövdesi. | Hayır |
| formatını | Verileri ayrıştırmadan olduğu *gibi BIR HTTP uç noktasından almak* isterseniz, **Biçim** ayarını atlayın. <br><br> Kopyalama sırasında HTTP yanıtı içeriğini ayrıştırmak istiyorsanız, şu biçim türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **Orcformat**ve **parquetformat**. Daha fazla bilgi için bkz. [metin biçimi](data-factory-supported-file-and-compression-formats.md#text-format), [JSON biçimi](data-factory-supported-file-and-compression-formats.md#json-format), [avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [orc biçimi](data-factory-supported-file-and-compression-formats.md#orc-format)ve [Parquet biçimi](data-factory-supported-file-and-compression-formats.md#parquet-format). |Hayır |
| masıyla | Verilerin türünü ve sıkıştırma düzeyini belirtin. Desteklenen türler: **gzip**, **söndür**, **bzip2**ve **zipsöndür**. Desteklenen düzeyler: **en iyi** ve **en hızlı**. Daha fazla bilgi için bkz. [Azure Data Factory dosya ve sıkıştırma biçimleri](data-factory-supported-file-and-compression-formats.md#compression-support). |Hayır |

**Örnek: GET (varsayılan) yöntemini kullanma**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Örnek: POST yöntemini kullanma**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliği özellikleri

Ad, açıklama, giriş ve çıkış tabloları ve ilke gibi özellikler tüm etkinlik türleri için kullanılabilir.

Etkinlikleri tanımlamak için kullanılabilen bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları oluşturma](data-factory-create-pipelines.md). 

Etkinliğin **typeproperties** bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için özellikler, kaynak ve havuz türlerine göre farklılık gösterir.

Şu anda, Copy etkinliğinin kaynağı **Httpsource** türünde olduğunda aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
| -------- | ----------- | -------- |
| httpRequestTimeout | HTTP isteğinin yanıt almak için zaman aşımı ( **TimeSpan** değeri). Yanıt verilerini okumak için zaman aşımı değil, yanıt almak için zaman aşımı. | Hayır<br />(varsayılan değer: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Desteklenen dosya ve sıkıştırma biçimleri

Daha fazla bilgi için [Azure Data Factory dosya ve sıkıştırma biçimlerine](data-factory-supported-file-and-compression-formats.md) bakın.

## <a name="json-examples"></a>JSON örnekleri

Aşağıdaki örnekler, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Örneklerde, verileri bir HTTP kaynağından Azure Blob depolama alanına kopyalama gösterilmektedir. Ancak, veriler, Azure Data Factory ' de kopyalama etkinliği kullanılarak [desteklenen](data-factory-data-movement-activities.md#supported-data-stores-and-formats) herhangi bir havuza *doğrudan* kaynak kopyalayabilir.

**Örnek: bir HTTP kaynağından Azure Blob depolama alanına veri kopyalama**

Bu örnek için Data Factory çözümü aşağıdaki Data Factory varlıklarını içerir:

*   [Http](#linked-service-properties)türünde bağlı bir hizmet.
*   [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
*   [Http](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
*   [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
*   [Httpsource](#copy-activity-properties) ve [blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, verileri bir HTTP kaynağından her saat bir Azure blobuna kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri izleyen bölümlerde açıklanmıştır.

### <a name="http-linked-service"></a>HTTP bağlı hizmeti

Bu örnek, anonim kimlik doğrulamasıyla HTTP bağlantılı hizmetini kullanır. Kullanabileceğiniz farklı kimlik doğrulama türleri için bkz. [http bağlı hizmeti](#linked-service-properties) .

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure depolama bağlı hizmeti

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>HTTP giriş veri kümesi

**External** to **true** olarak ayarlamak, Data Factory hizmetine veri kümesinin veri fabrikasının dışında olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini bildirir.

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Azure blob çıktı veri kümesi

Veriler her saat yeni bir bloba yazılır (**Sıklık**: **saat**, **Aralık**: **1**).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-that-uses-a-copy-activity"></a>Kopyalama etkinliği kullanan işlem hattı

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir. Kopyalama etkinliği her saat çalışacak şekilde zamanlanır. Ardışık düzen JSON tanımında **kaynak** türü **httpsource** olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır.

**Httpsource** tarafından desteklenen özelliklerin listesi için bkz. [httpsource](#copy-activity-properties).

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
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
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

> [!NOTE]
> Bir kaynak veri kümesindeki sütunları bir havuz veri kümesinden sütunlara eşlemek için, bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Performans ve ayar

Azure Data Factory ve en iyileştirmek için çeşitli yollarla veri taşıma (kopyalama etkinliği) performansını etkileyen anahtar faktörleri hakkında bilgi edinmek için bkz. [kopyalama etkinliği performansı ve ayarlama Kılavuzu](data-factory-copy-activity-performance.md).
