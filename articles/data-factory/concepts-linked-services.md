---
title: Azure Data Factory bağlı hizmetler
description: Data Factory 'de bağlı hizmetler hakkında bilgi edinin. Bağlı hizmetler işlem/veri depolarını Data Factory 'ye bağlar.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 9dd81484d8afab66fcb76f8fccdea348ef6a34c4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681493"
---
# <a name="linked-services-in-azure-data-factory"></a>Azure Data Factory bağlı hizmetler
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-create-datasets.md)
> * [Geçerli sürüm](concepts-datasets-linked-services.md)

Bu makalede, bağlı hizmetlerin ne olduğu, JSON biçiminde nasıl tanımlandığı ve Azure Data Factory işlem hatları 'nda nasıl kullanıldığı açıklanmaktadır.

Data Factory yeni bir deyişle, genel bakış için bkz. [Azure Data Factory giriş](introduction.md) .

## <a name="overview"></a>Genel Bakış
Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem **hattı** , birlikte bir görevi gerçekleştiren **etkinliklerin** mantıksal gruplandırmasıdır. Bir işlem hattındaki etkinlikler, verilerinizde gerçekleştirilecek eylemleri tanımlar. Örneğin, verileri şirket içi SQL Server Azure Blob depolamaya kopyalamak için bir kopyalama etkinliği kullanabilirsiniz. Daha sonra, çıktı verileri üretmek üzere blob depolamadan veri işlemek için bir Azure HDInsight kümesinde Hive betiği çalıştıran bir Hive etkinliği kullanabilirsiniz. Son olarak, çıkış verilerini Azure SQL veri ambarı 'na kopyalamak için ikinci bir kopyalama etkinliği kullanabilirsiniz. Bu işlem, en üst kısımdaki iş zekası (BI) raporlama çözümlerini oluşturulmuştur. İşlem hatları ve etkinlikler hakkında daha fazla bilgi için bkz. Azure Data Factory işlem [hatları ve etkinlikleri](concepts-pipelines-activities.md) .

Şimdi, veri **kümesi** , **etkinliklerde** giriş ve çıkış olarak kullanmak istediğiniz verilere işaret eden veya başvuruda bulunan verilerin adlandırılmış bir görünümüdür.

Bir veri kümesi oluşturmadan önce, veri deponuzu veri fabrikasına bağlamak için **bağlı bir hizmet** oluşturmanız gerekir. Bağlı hizmetler, dış kaynaklara bağlanmak için Data Factory’ye gereken bağlantı bilgilerini tanımlayan bağlantı dizelerine çok benzer. Bu şekilde düşünün; veri kümesi, bağlantılı veri depolarındaki verilerin yapısını temsil eder ve bağlı hizmet, veri kaynağıyla bağlantıyı tanımlar. Örneğin, Azure depolama bağlı hizmeti bir depolama hesabını veri fabrikasına bağlar. Azure blob veri kümesi, blob kapsayıcısını ve işlenecek giriş bloblarını içeren Azure depolama hesabı içindeki klasörü temsil eder.

Örnek senaryo aşağıda verilmiştir. Blob depolamadan SQL veritabanına veri kopyalamak için, iki bağlı hizmet oluşturursunuz: Azure depolama ve Azure SQL veritabanı. Daha sonra, iki veri kümesi oluşturun: Azure blob veri kümesi (Azure Storage bağlı hizmeti 'ne başvurur) ve Azure SQL tablo veri kümesi (Azure SQL veritabanı bağlı hizmetini ifade eder). Azure depolama ve Azure SQL veritabanı bağlı hizmetleri, Data Factory çalışma zamanında, sırasıyla Azure depolama ve Azure SQL veritabanınıza bağlanmak için kullandığı bağlantı dizelerini içerir. Azure blob veri kümesi blob depoınızda giriş bloblarını içeren blob kapsayıcısını ve BLOB klasörünü belirtir. Azure SQL tablo veri kümesi, verilerin kopyalanacağı SQL veritabanınızda SQL tablosunu belirtir.

Aşağıdaki diyagramda, Data Factory içinde işlem hattı, etkinlik, veri kümesi ve bağlı hizmet arasındaki ilişkiler gösterilmektedir:

![İşlem hattı, etkinlik, veri kümesi, bağlı hizmetler arasındaki ilişki](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Bağlı hizmet JSON 'ı
Data Factory bağlı bir hizmet, JSON biçiminde aşağıdaki gibi tanımlanır:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Aşağıdaki tabloda, yukarıdaki JSON 'daki özellikler açıklanmaktadır:

Özellik | Açıklama | Gerekli |
-------- | ----------- | -------- |
ad | Bağlı hizmetin adı. Bkz. [Azure Data Factory adlandırma kuralları](naming-rules.md). |  Evet |
type | Bağlı hizmetin türü. Örneğin: AzureStorage (veri deposu) veya AzureBatch (işlem). TypeProperties açıklamasına bakın. | Evet |
typeProperties | Tür özellikleri her bir veri deposu veya işlem için farklıdır. <br/><br/> Desteklenen veri deposu türleri ve bunların tür özellikleri için, bu makaledeki [veri kümesi türü](concepts-datasets-linked-services.md#dataset-type) tablosuna bakın. Bir veri deposuna özgü tür özellikleri hakkında bilgi edinmek için veri deposu Bağlayıcısı makalesine gidin. <br/><br/> Desteklenen işlem türleri ve bunların tür özellikleri için bkz. [işlem bağlantılı hizmetleri](compute-linked-services.md). | Evet |
connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime (veri depolduğunuz özel bir ağda yer alıyorsa) kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | Hayır

## <a name="linked-service-example"></a>Bağlı hizmet örneği
Aşağıdaki bağlı hizmet bir Azure Storage bağlı hizmetidir. Türün AzureStorage olarak ayarlandığını unutmayın. Azure depolama bağlı hizmetinin tür özellikleri bir bağlantı dizesi içerir. Data Factory hizmeti, çalışma zamanında veri deposuna bağlanmak için bu bağlantı dizesini kullanır.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma

Şu araçlardan veya SDK 'Lardan birini kullanarak bağlı hizmetler oluşturabilirsiniz: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager şablonu ve Azure Portal

## <a name="data-store-linked-services"></a>Veri deposu bağlı hizmetleri
[Bağlayıcı genel bakış](copy-activity-overview.md#supported-data-stores-and-formats) makalesindeki Data Factory tarafından desteklenen veri listesini bulabilirsiniz. Desteklenen bağlantı özelliklerini öğrenmek için bir veri deposuna tıklayın.

## <a name="compute-linked-services"></a>İşlem bağlantılı hizmetler
Farklı işlem ortamları hakkında ayrıntılı bilgi edinmek için [desteklenen başvuru işlem ortamları](compute-linked-services.md) , veri fabrikanınızdan ve farklı yapılandırmalardan bağlanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Bu araçlardan veya SDK 'Lardan birini kullanarak işlem hatları ve veri kümeleri oluşturmaya yönelik adım adım yönergeler için aşağıdaki öğreticiye bakın.

- [Hızlı başlangıç: .NET kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-dot-net.md)
- [Hızlı başlangıç: PowerShell kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-powershell.md)
- [Hızlı başlangıç: REST API kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-rest-api.md)
- [Hızlı başlangıç: Azure portal kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-portal.md)
