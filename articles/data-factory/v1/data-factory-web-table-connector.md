---
title: Azure Data Factory kullanarak Web tablosundan veri taşıma
description: Azure Data Factory kullanarak bir Web sayfasındaki tablodaki verileri taşıma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 957b47244744f161ad9cc8019a411e2e59c29418
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682316"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Azure Data Factory kullanarak bir Web tablosu kaynağından veri taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-web-table-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-web-table.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de Web tablosu Bağlayıcısı](../connector-web-table.md).

Bu makalede, bir Web sayfasındaki tablodaki verileri desteklenen bir havuz veri deposuna taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Bu makalede, kopyalama etkinliğine sahip veri hareketine ve kaynak/havuz olarak desteklenen veri depolarının listesine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesi oluşturulur.

Data Factory Şu anda yalnızca bir Web tablosundan diğer veri depolarına veri taşımayı destekliyor, ancak diğer veri depolarından verileri bir Web tablosu hedefine taşımıyor.

> [!IMPORTANT]
> Bu Web Bağlayıcısı Şu anda yalnızca bir HTML sayfasından tablo içeriğini ayıklamayı desteklemektedir. HTTP/s uç noktasından veri almak için bunun yerine [http bağlayıcısını](data-factory-http-connector.md) kullanın.

## <a name="prerequisites"></a>Ön koşullar

Bu Web tablosu bağlayıcısını kullanmak için, şirket içinde barındırılan bir Integration Runtime (aka Veri Yönetimi Gateway) ayarlamanız ve havuz bağlantılı hizmetindeki `gatewayName` özelliğini yapılandırmanız gerekir. Örneğin, Web tablosundan Azure Blob depolamaya kopyalamak için, Azure Storage bağlı hizmetini aşağıdaki gibi yapılandırın:

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API 'Ler kullanarak şirket içi Cassandra veri deposundan veri taşıyan kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz. 

- İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir yol için bkz. [öğretici: kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) . 
- İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun. 
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun. 

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız.  Bir Web tablosundan veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımlarına sahip bir örnek için, bu makalenin [JSON örneği: Web tablosundan verileri Azure Blob 'A kopyalama](#json-example-copy-data-from-web-table-to-azure-blob) bölümüne bakın. 

Aşağıdaki bölümler, bir Web tablosuna özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Aşağıdaki tabloda, web bağlantılı hizmetine özgü JSON öğeleri için açıklama verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği: **Web** olarak ayarlanmalıdır |Evet |
| Url |Web kaynağının URL 'SI |Evet |
| authenticationType |Deðeri. |Evet |

### <a name="using-anonymous-authentication"></a>Anonim kimlik doğrulaması kullanma

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik özellikler & bölümlerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablosu vb.) için benzerdir.

**Typeproperties** bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **Webtable** türündeki veri kümesinin typeproperties bölümü aşağıdaki özelliklere sahiptir

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type |Veri kümesinin türü. **Webtable** olarak ayarlanmalıdır |Evet |
| Yolun |Tabloyu içeren kaynağın göreli URL 'SI. |Hayır. Yol belirtilmediğinde, yalnızca bağlı hizmet tanımında belirtilen URL kullanılır. |
| indeks |Kaynaktaki tablonun dizini. HTML sayfasındaki bir tablonun dizinini alma adımları için bkz. [HTML sayfasındaki tablonun dizinini alma](#get-index-of-a-table-in-an-html-page) bölümü. |Evet |

**Örnek:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölüm & özelliklerinin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları ve ilke gibi özellikler, tüm etkinlik türleri için kullanılabilir.

Ancak, etkinliğin typeProperties bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

Şu anda, Copy etkinliğinin kaynağı **Websource**türünde olduğunda ek özellikler desteklenmez.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>JSON örneği: Web tablosundan Azure Blob 'a veri kopyalama
Aşağıdaki örnek şunu gösterir:

1. [Web](#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [Webtable](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Websource](#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, verileri bir Web tablosundan her saat bir Azure blobuna kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

Aşağıdaki örnek, bir Web tablosundan Azure blobuna nasıl veri kopyalanacağını gösterir. Ancak, veriler Azure Data Factory içindeki kopyalama etkinliği kullanılarak [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde belirtilen herhangi bir havuza doğrudan kopyalanabilir.

**Web bağlantılı hizmet** Bu örnek, anonim kimlik doğrulamasıyla web bağlantılı hizmetini kullanır. Kullanabileceğiniz farklı kimlik doğrulama türleri için [Web bağlantılı hizmet](#linked-service-properties) bölümüne bakın.

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

**Azure Storage bağlı hizmeti**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

**Webtable giriş veri kümesi** **External** to **true** olarak ayarlamak, Data Factory hizmetine veri kümesinin veri fabrikasının dışında olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini bildirir.

> [!NOTE]
> HTML sayfasındaki bir tablonun dizinini alma adımları için bkz. [HTML sayfasındaki tablonun dizinini alma](#get-index-of-a-table-in-an-html-page) bölümü.  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Azure Blob çıktı veri kümesi**

Veriler her saat yeni bir bloba yazılır (sıklık: saat, Aralık: 1).

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



**Kopyalama etkinliği içeren işlem hattı**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. İşlem hattı JSON tanımında, **kaynak** türü **websource** olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır.

WebSource tarafından desteklenen özelliklerin listesi için bkz. WebSource türü özellikleri.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
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

## <a name="get-index-of-a-table-in-an-html-page"></a>HTML sayfasındaki bir tablonun dizinini al
1. **Excel 2016** ' i başlatın ve **veri** sekmesine geçin.  
2. Araç çubuğunda **Yeni sorgu** ' ya tıklayın, **diğer kaynaklardan** üzerine gelin ve **Web 'den**' ye tıklayın.

    ![Power Query menüsü](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. **Web 'den** , bağlantılı hizmet JSON 'Da kullanacağınız **URL 'yi** girin (örneğin, veri kümesi için belirttiğiniz yol ile birlikte https://en.wikipedia.org/wiki/) (örneğin: AFI% 27S_100_yıllar... 100 _Filmler) ve ardından **Tamam**' a tıklayın.

    ![Web iletişim kutusundan](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    Bu örnekte kullanılan URL: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. **Web Içeriği erişimi** iletişim kutusu ' nu görürseniz, doğru **URL**'yi, **kimlik doğrulamasını**seçin ve **Bağlan**' a tıklayın.

   ![Web içeriğine eriş iletişim kutusu](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Tablodaki içeriği görmek için ağaç görünümündeki bir **tablo** öğesine tıklayın ve ardından alt kısımdaki **Düzenle** düğmesine tıklayın.  

   ![Gezgin iletişim kutusu](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. **Sorgu Düzenleyicisi** penceresinde, araç çubuğunda **Gelişmiş Düzenleyici** düğme ' ye tıklayın.

    ![Gelişmiş Düzenleyici düğmesi](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. Gelişmiş Düzenleyici iletişim kutusunda, "kaynak" nın yanındaki sayı dizindir.

    ![Gelişmiş Düzenleyici-Dizin](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Excel 2013 kullanıyorsanız, dizini almak için [Excel için Microsoft Power Query](https://www.microsoft.com/download/details.aspx?id=39379) kullanın. Ayrıntılar için bkz. [Web sayfasına bağlanma](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) makalesi. [Masaüstü Için Microsoft Power BI](https://powerbi.microsoft.com/desktop/)kullanıyorsanız, bu adımlar benzerdir.

> [!NOTE]
> Kaynak veri kümesindeki sütunları havuz veri kümesinden sütunlara eşlemek için, bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .
