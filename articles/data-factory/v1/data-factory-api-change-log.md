---
title: Data Factory-.NET API değişiklik günlüğü
description: Son değişiklikleri, özellik eklemelerini, hata düzeltmelerini açıklar vb. Azure Data Factory için .NET API 'nin belirli bir sürümünde.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: e1902db6bc3a127262f5e37f36e82b07ab15c7c9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683187"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory-.NET API değişiklik günlüğü
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. 

Bu makalede, belirli bir sürümdeki Azure Data Factory SDK ile ilgili değişiklikler hakkında bilgi sağlanır. Azure Data Factory için en son NuGet paketini [buradan](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) bulabilirsiniz

## <a name="version-4110"></a>Sürüm 4.11.0
Özellik eklemeleri:

* Aşağıdaki bağlı hizmet türleri eklendi:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Aşağıdaki veri kümesi türleri eklendi:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Aşağıdaki kopyalama kaynağı türleri eklendi:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Sürüm 4.10.0
* Aşağıdaki isteğe bağlı özellikler TextFormat öğesine eklenmiştir:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Aşağıdaki bağlı hizmet türleri eklendi:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [Salesforcelınkedservice](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Aşağıdaki veri kümesi türleri eklendi:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Aşağıdaki kopyalama kaynağı türleri eklendi:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* AzureMLBatchExecutionActivity 'e [webservicegirdilerle](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) özelliği ekleyin
  * Azure Machine Learning denemenize birden çok Web hizmeti girişi geçirmeyi etkinleştir

## <a name="version-491"></a>Sürüm 4.9.1
### <a name="bug-fix"></a>Hata onarımı
* [Weblinkedservice](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx)için WebApi tabanlı kimlik doğrulaması kullanımdan kaldırıldı.

## <a name="version-490"></a>Sürüm 4.9.0
### <a name="feature-additions"></a>Özellik eklemeleri
* CopyActivity 'ye [enablehazırlama](https://msdn.microsoft.com/library/mt767916.aspx) ve [stagingsettings](https://msdn.microsoft.com/library/mt767918.aspx) özellikleri ekleyin. Özelliği hakkında ayrıntılı bilgi için bkz. [aşamalı kopya](data-factory-copy-activity-performance.md#staged-copy) .

### <a name="bug-fix"></a>Hata onarımı
* [Activitywindowsbyactivitylistparameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) örneğini alan [Activitywindowoperationextensions. List](https://msdn.microsoft.com/library/mt767915.aspx) metodunun aşırı yüklemesini ortaya çıkarabilir.
* [Writebatchsize](https://msdn.microsoft.com/library/dn884293.aspx) ve [Writebatchtimeout](https://msdn.microsoft.com/library/dn884245.aspx) öğesini copysink içinde isteğe bağlı olarak işaretleyin.

## <a name="version-480"></a>Sürüm 4.8.0
### <a name="feature-additions"></a>Özellik eklemeleri
* Kopyalama performansını ayarlamayı etkinleştirmek için, kopyalama etkinliği türüne aşağıdaki isteğe bağlı özellikler eklenmiştir:
  * [Paralellkopyalar](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Sürüm 4.7.0
### <a name="feature-additions"></a>Özellik eklemeleri
* Dosyaları iyileştirilmiş satır sütunlu (ORC) biçimde kopyalamak için yeni StorageFormat türü [Orcformat](https://msdn.microsoft.com/library/mt723391.aspx) türü eklendi.
* [Allowpolybase](https://msdn.microsoft.com/library/mt723396.aspx) ve PolyBaseSettings özelliklerini SqlDWSink öğesine ekleyin.
  * Verileri SQL veri ambarı 'na kopyalamak için PolyBase kullanımını sağlar.

## <a name="version-461"></a>Sürüm 4.6.1
### <a name="bug-fixes"></a>Hata düzeltmeleri
* Etkinlik pencerelerini listelemek için HTTP isteğini düzeltir.
  * Kaynak grubu adını ve Veri Fabrikası adını istek yükünden kaldırır.

## <a name="version-460"></a>Sürüm 4.6.0
### <a name="feature-additions"></a>Özellik eklemeleri
* Şu özellikler, [pipelineproperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)'e eklenmiştir:
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Veri kümeleri](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* [Ardışık düzen eylemsizlik geri bilgisine](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)şu özellikler eklendi:
  * [Ardışık düzen Inestate](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Verileri JSON biçiminde olan veri kümelerini tanımlamak için yeni [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) türü [jsonformat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) türü eklendi.

## <a name="version-450"></a>Sürüm 4.5.0
### <a name="feature-additions"></a>Özellik eklemeleri
* [Etkinlik penceresi için liste işlemleri](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx)eklendi.
  * Varlık türlerine (veri fabrikaları, veri kümeleri, işlem hatları ve Etkinlikler) göre filtrelerle etkinlik pencerelerini almak için yöntemler eklenmiştir.
* Aşağıdaki bağlı hizmet türleri eklendi:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [weblinkedservice](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Aşağıdaki veri kümesi türleri eklendi:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [webtabledataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Aşağıdaki kopyalama kaynağı türleri eklendi:     
  * [Web kaynağı](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Sürüm 4.4.0
### <a name="feature-additions"></a>Özellik eklemeleri
* Aşağıdaki bağlı hizmet türü, kopyalama etkinlikleri için veri kaynakları ve havuzlar olarak eklenmiştir:
  * [Azurestooygesaslinkedservice](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Kavramsal bilgi ve örnekler için bkz. [Azure Storage SAS bağlı hizmeti](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) .

## <a name="version-430"></a>Sürüm 4.3.0
### <a name="feature-additions"></a>Özellik eklemeleri
* Aşağıdaki bağlı hizmet türleri, kopyalama etkinlikleri için veri kaynakları olarak eklenmiştir:
  * [Hdfslinkedservice](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Kavramsal bilgi ve örnekler için bkz. [Data Factory kullanarak verileri](data-factory-hdfs-connector.md) .
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Kavramsal bilgi ve örnekler için [Azure Data Factory kullanarak ODBC veri mağazalarından verileri taşıma](data-factory-odbc-connector.md) bölümüne bakın.

## <a name="version-420"></a>Sürüm 4.2.0
### <a name="feature-additions"></a>Özellik eklemeleri
* Şu yeni etkinlik türü eklendi: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Etkinlik hakkındaki ayrıntılar için bkz. [kaynak güncelleştirme etkinliğini kullanarak Azure ML modellerini güncelleştirme](data-factory-azure-ml-batch-execution-activity.md).
* [AzureMLLinkedService sınıfına](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx)yeni bir Isteğe bağlı [Updateresourceendpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) özelliği eklenmiştir.
* [Datafactorymanagementclient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) sınıfına [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) ve [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) özellikleri eklendi.
* Data Factory hizmetine istemci çağrıları için zaman aşımları yapılandırmasına izin verin.

## <a name="version-410"></a>Sürüm 4.1.0
### <a name="feature-additions"></a>Özellik eklemeleri
* Aşağıdaki bağlı hizmet türleri eklendi:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Aşağıdaki etkinlik türleri eklendi:
  * [Datalakeanaliz Ticsusqlactivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Aşağıdaki veri kümesi türleri eklendi:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Kopyalama etkinliği için aşağıdaki kaynak ve havuz türleri eklendi:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Sürüm 4.0.1
### <a name="breaking-changes"></a>Yeni değişiklikler
Aşağıdaki sınıflar yeniden adlandırıldı. Yeni adlar 4.0.0 sürümünden önceki özgün sınıfların adlarıdır.

| 4\.0.0 içindeki ad | 4\.0.1 içindeki ad |
|:--- |:--- |
| Azuressqldatawarehousedataset |[Azuressqldatawarehousetabledataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| Azuressqldataset |[Azuressqltabledataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Sürüm 4.0.0
### <a name="breaking-changes"></a>Yeni değişiklikler
* Aşağıdaki sınıflar/arabirimler yeniden adlandırıldı.

| Eski ad | Yeni ad |
|:--- |:--- |
| Itableoperations |[Idatasetoperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tablo |[Veri kümesi](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| Tablecreateorupdateresbir |[Datasetcreateorupdateres,](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* **Liste** yöntemleri artık disk belleğine alınmış sonuçları geri döndürüyor. Yanıt boş olmayan bir **NextLink** özelliği içeriyorsa, tüm sayfalar döndürülünceye kadar istemci uygulamanın sonraki sayfayı getirmeye devam etmesi gerekir.  Örnek aşağıda verilmiştir:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **Liste** işlem hattı API 'si, tam ayrıntılar yerine yalnızca bir işlem hattının özetini döndürür. Örneğin, bir işlem hattı özetindeki etkinlikler yalnızca ad ve tür içerir.

### <a name="feature-additions"></a>Özellik eklemeleri
* [Sqldwsink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) **sınıfı, Azure** SQL veri ambarı 'na ıdempotent kopyalamayı desteklemek için, iki yeni özelliği de kanal ve **sqlwritercleanupscript**' i destekler. Bu özellikler hakkındaki ayrıntılar için bkz. [Azure SQL veri ambarı](data-factory-azure-sql-data-warehouse-connector.md) makalesi.
* Artık kopyalama etkinliğinin bir parçası olarak Azure SQL veritabanı ve Azure SQL veri ambarı kaynakları için saklı yordam çalıştırmayı destekliyoruz. [SQLSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) ve [sqldwsource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) sınıfları şu özelliklere sahiptir: **SqlReaderStoredProcedureName** ve **StoredProcedureParameters**. Bu özellikler hakkında daha fazla bilgi için bkz. Azure.com üzerinde [Azure SQL veritabanı](data-factory-azure-sql-connector.md#sqlsource) ve [Azure SQL veri ambarı](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) makaleleri.  
