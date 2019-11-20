---
title: Azure Data Factory’de kopyalama etkinliğinin hataya dayanıklılığı
description: Uyumsuz satırları atlayarak Azure Data Factory etkinliği kopyalamak için hataya dayanıklılık ekleme hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: a60cafd529db1c6726a15db2c442af8d097411cc
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678152"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Azure Data Factory’de kopyalama etkinliğinin hataya dayanıklılığı
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Geçerli sürüm](copy-activity-fault-tolerance.md)

Azure Data Factory kopyalama etkinliği, verileri kaynak ve havuz veri depoları arasında kopyalarken uyumsuz satırları işlemek için kullanabileceğiniz iki yol sunar:

- Uyumsuz verilerle karşılaşıldığında kopyalama etkinliğini durdurabilir ve başarısız yapabilirsiniz (varsayılan davranış).
- Hata toleransı ekleyerek ve uyumsuz veri satırlarını atlayarak tüm verileri kopyalamaya devam edebilirsiniz. Ayrıca, Azure Blob depolamada veya Azure Data Lake Store uyumsuz satırları günlüğe kaydedebilirsiniz. Sonra hatanın nedenini öğrenmek, veri kaynağındaki verileri onarmak ve kopyalama etkinliğini yeniden denemek için günlüğü inceleyebilirsiniz.

## <a name="supported-scenarios"></a>Desteklenen senaryolar
Kopyalama etkinliği, uyumsuz verileri saptamak, atlamak ve günlüğe kaydetmek için üç senaryoyu destekler:

- **Kaynak veri türü ile havuz yerel türü arasında uyumsuzluk**. 

    Örneğin: BLOB depolama alanındaki bir CSV dosyasından, üç INT tür sütunu içeren bir şema tanımına sahip bir SQL veritabanına veri kopyalama. 123.456.789 gibi sayısal veriler içeren CSV dosyası satırları havuz deposuna başarıyla kopyalanır. Ancak, 123.456, ABC gibi sayısal olmayan değerler içeren satırlar uyumsuz olarak algılanır ve atlanır.

- **Kaynak ve havuz arasındaki sütun sayısında uyuşmazlık var**.

    Örneğin: BLOB depolama alanındaki bir CSV dosyasından, altı sütun içeren bir şema tanımına sahip bir SQL veritabanına veri kopyalama. Altı sütun içeren CSV dosyası satırları havuz deposuna başarıyla kopyalanır. Altıdan fazla veya daha az sütun içeren CSV dosyası satırları uyumsuz olarak algılanır ve atlanır.

- **SQL Server/Azure SQL veritabanı/Azure Cosmos DB yazılırken birincil anahtar ihlali**.

    Örneğin: bir SQL Server 'dan SQL veritabanı 'na veri kopyalama. Birincil anahtar, havuz SQL veritabanında tanımlanmıştır, ancak kaynak SQL Server 'da böyle bir birincil anahtar tanımlanmamıştır. Kaynakta bulunan yinelenen satırlar havuza kopyalanamıyor. Kopyalama etkinliği yalnızca kaynak verilerin ilk satırını havuza kopyalar. Yinelenen birincil anahtar değerini içeren sonraki kaynak satırlar uyumsuz olarak algılanır ve atlanır.

>[!NOTE]
>- PolyBase kullanarak SQL veri ambarı 'na veri yüklemek için, kopyalama etkinliğinde "[Polybasesettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" aracılığıyla reddetme Ilkelerini belirterek PolyBase 'in yerel hata toleransı ayarlarını yapılandırın. Aynı zamanda, PolyBase uyumsuz satırları blob veya ADLS 'e aşağıda gösterildiği gibi normal şekilde yeniden yönlendirmeyi etkinleştirebilirsiniz.
>- Copy etkinliği [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)komutunu çağırmak üzere yapılandırıldığında bu özellik uygulanmaz.
>- Kopyalama etkinliği [BIR SQL havuzundan saklı yordam](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink)çağırmak üzere yapılandırıldığında bu özellik uygulanmaz.

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
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | -------- 
Enableskipıncompatiblerow | Kopya sırasında uyumsuz satırların atlanıp atlanmayacağını belirtir. | True<br/>False (varsayılan) | Hayır
Redirectıncompatiblerowsettings | Uyumsuz satırları günlüğe kaydetmek istediğinizde belirtilenebilir bir özellik grubu. | &nbsp; | Hayır
linkedServiceName | Atlanan satırları içeren günlüğü depolamak için [Azure depolama](connector-azure-blob-storage.md#linked-service-properties) 'nın bağlı hizmeti veya [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) . | Günlük dosyasını depolamak için kullanmak istediğiniz örneğe başvuran bir `AzureStorage` veya `AzureDataLakeStore` türü bağlı hizmetin adı. | Hayır
Yolun | Atlanan satırları içeren günlük dosyasının yolu. | Uyumsuz verileri günlüğe kaydetmek için kullanmak istediğiniz yolu belirtin. Bir yol sağlamazsanız, hizmet sizin için bir kapsayıcı oluşturur. | Hayır

## <a name="monitor-skipped-rows"></a>Atlanan satırları izle
Kopyalama etkinliği çalıştırıldıktan sonra kopyalama etkinliğinin çıkışında atlanan satır sayısını görebilirsiniz:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
' I uyumsuz satırları günlüğe kaydetmek üzere yapılandırırsanız, günlük dosyasını şu yolda bulabilirsiniz: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Günlük dosyaları yalnızca CSV dosyaları olabilir. Atlanan özgün veriler, gerekirse sütun sınırlayıcısı olarak virgülle kaydedilir. Günlük dosyasındaki özgün kaynak verilerine ek olarak "ErrorCode" ve "ErrorMessage" olmak üzere iki sütun daha ekler. Bu, uyumsuzluğun kök nedenini görebileceğiniz yerdir. ErrorCode ve ErrorMessage, çift tırnak ile alıntılanacaktır. 

Günlük dosyası içeriğine bir örnek aşağıdaki gibidir:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Sonraki adımlar
Diğer kopyalama etkinliği makalelerine bakın:

- [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
- [Etkinlik performansını Kopyala](copy-activity-performance.md)


