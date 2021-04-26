---
title: Uyumsuz satırları atlayarak Azure Data Factory kopyalama etkinliğine hata toleransı ekleyin
description: Kopyalama sırasında uyumsuz satırları atlayarak Azure Data Factory kopyalama etkinliğine hata toleransı eklemeyi öğrenin
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 10e4bedae5b7c429152a3503fff2cb2769d66eb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377185"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Uyumsuz satırları atlayarak kopyalama etkinliğine hata toleransı ekleyin

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-copy-activity-fault-tolerance.md)
> * [Sürüm 2 (geçerli sürüm)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, [Data Factory kopyalama etkinliğinde hata toleransı](../copy-activity-fault-tolerance.md)' ne bakın.

Azure Data Factory [kopyalama etkinliği](data-factory-data-movement-activities.md) , verileri kaynak ve havuz veri depoları arasında kopyalarken uyumsuz satırları işlemek için kullanabileceğiniz iki yol sunar:

- Uyumsuz verilerle karşılaşıldığında kopyalama etkinliğini durdurabilir ve başarısız yapabilirsiniz (varsayılan davranış).
- Hata toleransı ekleyerek ve uyumsuz veri satırlarını atlayarak tüm verileri kopyalamaya devam edebilirsiniz. Ayrıca, Azure Blob depolamada uyumsuz satırları günlüğe kaydedebilirsiniz. Sonra hatanın nedenini öğrenmek, veri kaynağındaki verileri onarmak ve kopyalama etkinliğini yeniden denemek için günlüğü inceleyebilirsiniz.

## <a name="supported-scenarios"></a>Desteklenen senaryolar
Kopyalama etkinliği, uyumsuz verileri saptamak, atlamak ve günlüğe kaydetmek için üç senaryoyu destekler:

- **Kaynak veri türü ile havuz yerel türü arasında uyumsuzluk**

    Örneğin: BLOB depolama alanındaki bir CSV dosyasından, üç **Int** tür sütunu içeren bir şema tanımına sahıp bir SQL veritabanına veri kopyalama. Gibi sayısal veriler içeren CSV dosyası satırları `123,456,789` Havuz deposuna başarıyla kopyalanır. Ancak, gibi sayısal olmayan değerler içeren satırlar `123,456,abc` uyumsuz olarak algılanır ve atlanır.

- **Kaynak ile havuz arasında sütun sayısı uyuşmazlığı**

    Örneğin: BLOB depolama alanındaki bir CSV dosyasından, altı sütun içeren bir şema tanımına sahip bir SQL veritabanına veri kopyalama. Altı sütun içeren CSV dosyası satırları havuz deposuna başarıyla kopyalanır. Altıdan fazla veya daha az sütun içeren CSV dosyası satırları uyumsuz olarak algılanır ve atlanır.

- **SQL Server/Azure SQL Veritabanı/Azure Cosmos DB’ye yazarken birincil anahtar ihlali**

    Örneğin: bir SQL Server 'dan SQL veritabanı 'na veri kopyalama. Birincil anahtar, havuz SQL veritabanında tanımlanmıştır, ancak kaynak SQL Server 'da böyle bir birincil anahtar tanımlanmamıştır. Kaynakta bulunan yinelenen satırlar havuza kopyalanamıyor. Kopyalama etkinliği yalnızca kaynak verilerin ilk satırını havuza kopyalar. Yinelenen birincil anahtar değerini içeren sonraki kaynak satırlar uyumsuz olarak algılanır ve atlanır.

>[!NOTE]
>Kopyalama etkinliği, [Azure SYNAPSE Analytics PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) veya [Amazon Redshift Unload](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift)dahil olmak üzere dış veri yükleme mekanizmasını çağırmak üzere yapılandırıldığında bu özellik uygulanmaz. PolyBase kullanarak Azure SYNAPSE Analytics 'e veri yüklemek için, kopyalama etkinliğinde "[Polybasesettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" belirterek PolyBase 'in yerel hata toleransı desteğini kullanın.

## <a name="configuration"></a>Yapılandırma
Aşağıdaki örnek, kopyalama etkinliğinde uyumsuz satırları atlamayı yapılandırmak için bir JSON tanımı sağlar:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| **Enableskipıncompatiblerow** | Kopya sırasında uyumsuz satırları atlamayı etkinleştir. | Doğru<br/>False (varsayılan) | No |
| **Redirectıncompatiblerowsettings** | Uyumsuz satırları günlüğe kaydetmek istediğinizde belirtilenebilir bir özellik grubu. | &nbsp; | No |
| **linkedServiceName** | Atlanan satırları içeren günlüğü depolamak için Azure Storage bağlı hizmeti. | Günlük dosyasını depolamak için kullanmak istediğiniz depolama örneğine başvuran bir [Azurestorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) veya [Azurestokıgesas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) bağlı hizmetinin adı. | No |
| **Yolun** | Atlanan satırları içeren günlük dosyasının yolu. | Uyumsuz verileri günlüğe kaydetmek için kullanmak istediğiniz BLOB depolama yolunu belirtin. Bir yol sağlamazsanız, hizmet sizin için bir kapsayıcı oluşturur. | No |

## <a name="monitoring"></a>İzleme
Kopyalama etkinliği çalıştırıldıktan sonra, izleme bölümünde atlanan satır sayısını görebilirsiniz:

![İzleyici, uyumsuz satırları atladı](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

' I uyumsuz satırları günlüğe kaydetmek üzere yapılandırırsanız, günlük dosyasını şu yolda bulabilirsiniz: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` günlük dosyasında, atlanan satırları ve uyumsuzluğun kök nedenini görebilirsiniz.

Hem özgün veriler hem de karşılık gelen hata dosyada günlüğe kaydedilir. Günlük dosyası içeriğine bir örnek aşağıdaki gibidir:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory kopyalama etkinliği hakkında daha fazla bilgi için bkz. [kopyalama etkinliğini kullanarak verileri taşıma](data-factory-data-movement-activities.md).
