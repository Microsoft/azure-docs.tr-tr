---
title: Azure Data Factory kullanarak Web tablosundan veri kopyalama
description: Web tablosundan Data Factory tarafından desteklenen veri depolarına veri depolarına veri kopyalamanızı sağlayan Azure Data Factory Web tablosu Bağlayıcısı hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: a13f3c2d2bbebd2cd6fa95bd7aa144722447ac9d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680053"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Web tablosundan veri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-web-table-connector.md)
> * [Geçerli sürüm](connector-web-table.md)

Bu makalede, bir Web tablosu veritabanından veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

Bu Web tablosu Bağlayıcısı arasındaki fark, [rest Bağlayıcısı](connector-rest.md) ve [http Bağlayıcısı](connector-http.md) şunlardır:

- **Web tablosu Bağlayıcısı** , tablo IÇERIĞINI bir HTML Web sayfasından ayıklar.
- **Rest Bağlayıcısı** , verilerin yeniden oluşturulmuş API 'lerden kopyalanmasını özellikle destekler.
- **Http Bağlayıcısı** , örneğin dosyayı indirmek için HERHANGI bir HTTP uç noktasından veri almak için geneldir. 

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu Web tablosu Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Web tablosu veritabanından desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu Web tablosu Bağlayıcısı **HTML sayfasından tablo içeriğini ayıklamayı**destekler.

## <a name="prerequisites"></a>Ön koşullar

Bu Web tablosu bağlayıcısını kullanmak için, kendinden konak Integration Runtime ayarlamanız gerekir. Ayrıntılar için bkz. [Şirket içinde barındırılan Integration Runtime](create-self-hosted-integration-runtime.md) makalesi.

## <a name="getting-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler, Web tablosu bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Web tablosu bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği: **Web** olarak ayarlanmalıdır |Evet |
| url | Web kaynağının URL 'SI |Evet |
| authenticationType | İzin verilen değer: **Anonymous**. |Evet |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşul](#prerequisites)bölümünde belirtildiği gibi, kendinden konak Integration Runtime gereklidir. |Evet |

**Örnek:**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, Web tablosu veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

Web tablosundan veri kopyalamak için veri kümesinin Type özelliğini **Webtable**olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | DataSet 'in Type özelliği: **Webtable** olarak ayarlanmalıdır | Evet |
| Yolun |Tabloyu içeren kaynağın göreli URL 'SI. |Hayır. Yol belirtilmediğinde, yalnızca bağlı hizmet tanımında belirtilen URL kullanılır. |
| indeks |Kaynaktaki tablonun dizini. HTML sayfasındaki bir tablonun dizinini alma adımları için bkz. [HTML sayfasındaki tablonun dizinini alma](#get-index-of-a-table-in-an-html-page) bölümü. |Evet |

**Örnek:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Web tablosu kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="web-table-as-source"></a>Kaynak olarak Web tablosu

Web tablosundan veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **Websource**olarak ayarlayın, ek özellikler desteklenmez.

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
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
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>HTML sayfasındaki bir tablonun dizinini al

[Veri kümesi özelliklerinde](#dataset-properties)yapılandırmanız gereken bir tablonun dizinini almak için, aşağıdaki gibi bir araç olarak örneğin, Excel 2016 'yi kullanabilirsiniz:

1. **Excel 2016** ' i başlatın ve **veri** sekmesine geçin.
2. Araç çubuğunda **Yeni sorgu** ' ya tıklayın, **diğer kaynaklardan** üzerine gelin ve **Web 'den**' ye tıklayın.

    ![Power Query menüsü](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. **Web 'den** , bağlantılı hizmet JSON 'Da kullanacağınız **URL 'yi** girin (örneğin, veri kümesi için belirttiğiniz yol ile birlikte https://en.wikipedia.org/wiki/) (örneğin: AFI% 27S_100_yıllar... 100 _Filmler) ve ardından **Tamam**' a tıklayın.

    ![Web iletişim kutusundan](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    Bu örnekte kullanılan URL: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. **Web Içeriği erişimi** iletişim kutusu ' nu görürseniz, doğru **URL**'yi, **kimlik doğrulamasını**seçin ve **Bağlan**' a tıklayın.

   ![Web içeriğine eriş iletişim kutusu](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Tablodaki içeriği görmek için ağaç görünümündeki bir **tablo** öğesine tıklayın ve ardından alt kısımdaki **Düzenle** düğmesine tıklayın.  

   ![Gezgin iletişim kutusu](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. **Sorgu Düzenleyicisi** penceresinde, araç çubuğunda **Gelişmiş Düzenleyici** düğme ' ye tıklayın.

    ![Gelişmiş Düzenleyici düğmesi](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. Gelişmiş Düzenleyici iletişim kutusunda, "kaynak" nın yanındaki sayı dizindir.

    ![Gelişmiş Düzenleyici-Dizin](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Excel 2013 kullanıyorsanız, dizini almak için [Excel için Microsoft Power Query](https://www.microsoft.com/download/details.aspx?id=39379) kullanın. Ayrıntılar için bkz. [Web sayfasına bağlanma](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) makalesi. [Masaüstü Için Microsoft Power BI](https://powerbi.microsoft.com/desktop/)kullanıyorsanız, bu adımlar benzerdir.


## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
