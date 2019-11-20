---
title: Azure Data Lake Storage 1. çevrimdışı yöntemlere büyük veri kümesi yükleme
description: Içeri/dışarı aktarma hizmetini kullanarak Azure Blob depolamadan veri kopyalama Azure Data Lake Storage 1.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: aa3eb0bcd9ddd2a094563efe326f7af7e9e8708a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839302"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>Azure Içeri/dışarı aktarma hizmetini kullanarak verilerin çevrimdışı kopyası Data Lake Storage 1.

Bu makalede, [Azure içeri/dışarı aktarma hizmeti](../storage/common/storage-import-export-service.md)gibi çevrimdışı kopyalama yöntemlerini kullanarak çok büyük veri kümelerini (> 200 GB) Data Lake Storage 1. nasıl kopyalayacağınızı öğreneceksiniz. Özellikle, bu makalede örnek olarak kullanılan dosya 339.420.860.416 bayttır veya diskte 319 GB olur. Bu dosyayı 319GB. tsv dosyasına arayalım.

Azure Içeri/dışarı aktarma hizmeti, sabit disk sürücülerinizi bir Azure veri merkezine aktararak Azure Blob depolama alanına büyük miktarlarda veri aktarmanıza yardımcı olur.

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilere sahip olmanız gerekir:

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/pricing/free-trial/).
* **Bir Azure depolama hesabı**.
* **Azure Data Lake Storage 1. hesabı**. Bir oluşturma hakkında yönergeler için bkz. Azure Data Lake Storage 1. kullanmaya [başlama](data-lake-store-get-started-portal.md).

## <a name="prepare-the-data"></a>Verileri hazırlama

Içeri/dışarı aktarma hizmetini kullanmadan önce, boyutu **200 GB 'tan az olan kopyalara** aktarılacak veri dosyasını kesin. İçeri aktarma aracı 200 GB 'tan büyük dosyalarla çalışmaz. Bu makalede, dosyayı her biri 100 GB öbeklere böleceğiz. Bunu, [Cygwin](https://cygwin.com/install.html)kullanarak yapabilirsiniz. Cygwin, Linux komutlarını destekler. Bu durumda, aşağıdaki komutu kullanın:

    split -b 100m 319GB.tsv

Bölünen işlem, aşağıdaki adlara sahip dosyaları oluşturur.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Verilerle çalışmaya yönelik diskleri Al

Sabit sürücülerinizi hazırlamak için [Azure içeri/dışarı aktarma hizmetini](../storage/common/storage-import-export-service.md) ( **sürücülerinizi hazırlama** bölümünde) kullanma bölümündeki yönergeleri izleyin. Genel sıra:

1. Azure Içeri/dışarı aktarma hizmeti için kullanılacak gereksinimi karşılayan bir sabit disk temin edin.
2. Verilerin Azure veri merkezine gönderildikten sonra kopyalanacağı bir Azure depolama hesabı belirler.
3. Komut satırı yardımcı programı olan [Azure içeri/dışarı aktarma aracını](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)kullanın. Aracın nasıl kullanılacağını gösteren örnek bir kod parçacığı aşağıda verilmiştir.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Daha fazla örnek kod parçacığı için bkz. [Azure içeri/dışarı aktarma hizmetini kullanma](../storage/common/storage-import-export-service.md) .
4. Yukarıdaki komut, belirtilen konumda bir günlük dosyası oluşturur. [Azure Portal](https://portal.azure.com)bir içeri aktarma işi oluşturmak için bu günlük dosyasını kullanın.

## <a name="create-an-import-job"></a>İçeri aktarma işi oluşturma

Artık [Azure içeri/dışarı aktarma hizmetini](../storage/common/storage-import-export-service.md) ( **Içeri aktarma işi oluşturma** bölümünde) kullanarak bir içeri aktarma işi oluşturabilirsiniz. Bu içeri aktarma işi için, diğer ayrıntılarla birlikte disk sürücüleri hazırlanırken oluşturulan günlük dosyasını da belirtin.

## <a name="physically-ship-the-disks"></a>Diskleri fiziksel olarak teslim edin

Artık diskleri bir Azure veri merkezine fiziksel olarak gönderebilirsiniz. Burada, veriler içeri aktarma işini oluştururken belirttiğiniz Azure depolama bloblarına kopyalanır. Ayrıca, işi oluştururken izleme bilgilerini daha sonra sağlamayı tercih ettiyseniz artık içeri aktarma işinize geri dönüp izleme numarasını güncelleştirebilirsiniz.

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>Bloblardan veri kopyalama Data Lake Storage 1.

İçeri aktarma işinin durumu tamamlandıysa, verilerin belirttiğiniz Azure Storage Bloblarında kullanılabilir olup olmadığını doğrulayabilirsiniz. Daha sonra, bu verileri bloblardan Azure Data Lake Storage 1. taşımak için çeşitli yöntemler kullanabilirsiniz. Veri yüklemeye yönelik tüm kullanılabilir seçenekler için bkz. [verileri Data Lake Storage 1. içine almak](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

Bu bölümde, verileri kopyalamak için bir Azure Data Factory işlem hattı oluşturmak üzere kullanabileceğiniz JSON tanımlarını sunuyoruz. Bu JSON tanımlarını [Azure Portal](../data-factory/tutorial-copy-data-portal.md) veya [Visual Studio](../data-factory/tutorial-copy-data-dot-net.md)'dan kullanabilirsiniz.

### <a name="source-linked-service-azure-storage-blob"></a>Kaynak bağlı hizmeti (Azure Depolama Blobu)

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="target-linked-service-data-lake-storage-gen1"></a>Hedef bağlı hizmet (Data Lake Storage 1.)

```JSON
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
```

### <a name="input-data-set"></a>Giriş veri kümesi

```JSON
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

### <a name="output-data-set"></a>Çıkış veri kümesi

```JSON
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
```

### <a name="pipeline-copy-activity"></a>İşlem hattı (kopyalama etkinliği)

```JSON
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

Daha fazla bilgi için bkz. [Azure Storage blob 'dan Azure Data Lake Storage 1. Azure Data Factory kullanarak verileri taşıma](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>Data Lake Storage 1. veri dosyalarını yeniden yapılandırma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

319 GB olan bir dosyayla başlıyoruz ve Azure Içeri/dışarı aktarma hizmeti kullanılarak aktarılabilmesi için daha küçük boyuttaki dosyalara kadar bir dosya haline gelir. Artık veriler Azure Data Lake Storage 1. olduğundan, dosyayı özgün boyutuna yeniden yapılandırabilirsiniz. Bunu yapmak için aşağıdaki Azure PowerShell cmdlet 'lerini kullanabilirsiniz.

```PowerShell
# Login to our account
Connect-AzAccount

# List your subscriptions
Get-AzSubscription

# Switch to the subscription you want to work with
Set-AzContext -SubscriptionId
Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>Sonraki adımlar

* [Data Lake Storage Gen1'de verilerin güvenliğini sağlama](data-lake-store-secure-data.md)
* [Data Lake Storage 1. ile Azure Data Lake Analytics kullanma](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight 'ı Data Lake Storage 1. ile kullanma](data-lake-store-hdinsight-hadoop-use-portal.md)
