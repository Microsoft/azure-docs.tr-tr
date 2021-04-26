---
title: Data Factory-.NET API değişiklik günlüğü
description: Azure Data Factory için belirli bir .NET API sürümünde değişiklik, özellik eklemeleri, hata düzeltmeleri ve benzeri işlemleri açıklar.
author: dcstwh
ms.author: weetok
ms.reviewer: jonburchel
ms.service: data-factory
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: 2d292bbc820574d4360be51e93588f8f908f468d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785606"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory-.NET API değişiklik günlüğü
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. 

Bu makalede, belirli bir sürümdeki Azure Data Factory SDK ile ilgili değişiklikler hakkında bilgi sağlanır. Azure Data Factory için en son NuGet paketini [buradan](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) bulabilirsiniz

## <a name="version-4110"></a>Sürüm 4.11.0
Özellik eklemeleri:

* Aşağıdaki bağlı hizmet türleri eklendi:
  * [OnPremisesMongoDbLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesmongodblinkedservice)
  * [AmazonRedshiftLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.amazonredshiftlinkedservice)
  * [AwsAccessKeyLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.awsaccesskeylinkedservice)
* Aşağıdaki veri kümesi türleri eklendi:
  * [MongoDbCollectionDataset](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbcollectiondataset)
  * [AmazonS3Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.amazons3dataset)
* Aşağıdaki kopyalama kaynağı türleri eklendi:
  * [MongoDbSource](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbsource)

## <a name="version-4100"></a>Sürüm 4.10.0
* Aşağıdaki isteğe bağlı özellikler TextFormat öğesine eklenmiştir:
  * [SkipLineCount](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [FirstRowAsHeader](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [TreatEmptyAsNull](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
* Aşağıdaki bağlı hizmet türleri eklendi:
  * [OnPremisesCassandraLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice)
  * [Salesforcelınkedservice](/dotnet/api/microsoft.azure.management.datafactories.models.salesforcelinkedservice)
* Aşağıdaki veri kümesi türleri eklendi:
  * [OnPremisesCassandraTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset)
* Aşağıdaki kopyalama kaynağı türleri eklendi:
  * [CassandraSource](/dotnet/api/microsoft.azure.management.datafactories.models.cassandrasource)
* AzureMLBatchExecutionActivity 'e [webservicegirdilerle](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity) özelliği ekleyin
  * Azure Machine Learning denemenize birden çok Web hizmeti girişi geçirmeyi etkinleştir

## <a name="version-491"></a>Sürüm 4.9.1
### <a name="bug-fix"></a>Hata onarımı
* [Weblinkedservice](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)için WebApi tabanlı kimlik doğrulaması kullanımdan kaldırıldı.

## <a name="version-490"></a>Sürüm 4.9.0
### <a name="feature-additions"></a>Özellik eklemeleri
* CopyActivity 'ye [enablehazırlama](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity) ve [stagingsettings](/dotnet/api/microsoft.azure.management.datafactories.models.stagingsettings) özellikleri ekleyin. Özelliği hakkında ayrıntılı bilgi için bkz. [aşamalı kopya](data-factory-copy-activity-performance.md#staged-copy) .

### <a name="bug-fix"></a>Hata onarımı
* [Activitywindowsbyactivitylistparameters](/dotnet/api/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters) örneğini alan [Activitywindowoperationextensions. List](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions) metodunun aşırı yüklemesini ortaya çıkarabilir.
* [Writebatchsize](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) ve [Writebatchtimeout](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) öğesini copysink içinde isteğe bağlı olarak işaretleyin.

## <a name="version-480"></a>Sürüm 4.8.0
### <a name="feature-additions"></a>Özellik eklemeleri
* Kopyalama performansını ayarlamayı etkinleştirmek için, kopyalama etkinliği türüne aşağıdaki isteğe bağlı özellikler eklenmiştir:
  * [Paralellkopyalar](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)
  * [CloudDataMovementUnits](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)

## <a name="version-470"></a>Sürüm 4.7.0
### <a name="feature-additions"></a>Özellik eklemeleri
* Dosyaları iyileştirilmiş satır sütunlu (ORC) biçimde kopyalamak için yeni StorageFormat türü [Orcformat](/dotnet/api/microsoft.azure.management.datafactories.models.orcformat) türü eklendi.
* [Allowpolybase](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) ve PolyBaseSettings özelliklerini SqlDWSink öğesine ekleyin.
  * Azure SYNAPSE Analytics 'e veri kopyalamak için PolyBase 'in kullanılmasını sağlar.

## <a name="version-461"></a>Sürüm 4.6.1
### <a name="bug-fixes"></a>Hata Düzeltmeleri
* Etkinlik pencerelerini listelemek için HTTP isteğini düzeltir.
  * Kaynak grubu adını ve Veri Fabrikası adını istek yükünden kaldırır.

## <a name="version-460"></a>Sürüm 4.6.0
### <a name="feature-additions"></a>Özellik eklemeleri
* Şu özellikler, [pipelineproperties](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)'e eklenmiştir:
  * [PipelineMode](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [ExpirationTime](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [Veri kümeleri](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
* [Ardışık düzen eylemsizlik geri bilgisine](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)şu özellikler eklendi:
  * [Ardışık düzen Inestate](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
* Verileri JSON biçiminde olan veri kümelerini tanımlamak için yeni [StorageFormat](/dotnet/api/microsoft.azure.management.datafactories.models.storageformat) türü [jsonformat](/dotnet/api/microsoft.azure.management.datafactories.models.jsonformat) türü eklendi.

## <a name="version-450"></a>Sürüm 4.5.0
### <a name="feature-additions"></a>Özellik eklemeleri
* [Etkinlik penceresi için liste işlemleri](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions)eklendi.
  * Varlık türlerine (veri fabrikaları, veri kümeleri, işlem hatları ve Etkinlikler) göre filtrelerle etkinlik pencerelerini almak için yöntemler eklenmiştir.
* Aşağıdaki bağlı hizmet türleri eklendi:
  * [ODataLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.odatalinkedservice), [weblinkedservice](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)
* Aşağıdaki veri kümesi türleri eklendi:
  * [ODataResourceDataset](/dotnet/api/microsoft.azure.management.datafactories.models.odataresourcedataset), [webtabledataset](/dotnet/api/microsoft.azure.management.datafactories.models.webtabledataset)
* Aşağıdaki kopyalama kaynağı türleri eklendi:     
  * [Web kaynağı](/dotnet/api/microsoft.azure.management.datafactories.models.websource)

## <a name="version-440"></a>Sürüm 4.4.0
### <a name="feature-additions"></a>Özellik eklemeleri
* Aşağıdaki bağlı hizmet türü, kopyalama etkinlikleri için veri kaynakları ve havuzlar olarak eklenmiştir:
  * [Azurestooygesaslinkedservice](/dotnet/api/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice). Kavramsal bilgi ve örnekler için bkz. [Azure Storage SAS bağlı hizmeti](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) .

## <a name="version-430"></a>Sürüm 4.3.0
### <a name="feature-additions"></a>Özellik eklemeleri
* Aşağıdaki bağlı hizmet türleri, kopyalama etkinlikleri için veri kaynakları olarak eklenmiştir:
  * [Hdfslinkedservice](/dotnet/api/microsoft.azure.management.datafactories.models.hdfslinkedservice). Kavramsal bilgi ve örnekler için bkz. [Data Factory kullanarak verileri](data-factory-hdfs-connector.md) .
  * [OnPremisesOdbcLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice). Kavramsal bilgi ve örnekler için [Azure Data Factory kullanarak ODBC veri mağazalarından verileri taşıma](data-factory-odbc-connector.md) bölümüne bakın.

## <a name="version-420"></a>Sürüm 4.2.0
### <a name="feature-additions"></a>Özellik eklemeleri
* Şu yeni etkinlik türü eklendi: [AzureMLUpdateResourceActivity](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity). Etkinlik hakkındaki ayrıntılar için bkz. [kaynak güncelleştirme etkinliğini kullanarak Azure ML modellerini güncelleştirme](data-factory-azure-ml-batch-execution-activity.md).
* [AzureMLLinkedService sınıfına](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice)yeni bir Isteğe bağlı [Updateresourceendpoint](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice) özelliği eklenmiştir.
* [Datafactorymanagementclient](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) sınıfına [LongRunningOperationInitialTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) ve [LongRunningOperationRetryTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) özellikleri eklendi.
* Data Factory hizmetine istemci çağrıları için zaman aşımları yapılandırmasına izin verin.

## <a name="version-410"></a>Sürüm 4.1.0
### <a name="feature-additions"></a>Özellik eklemeleri
* Aşağıdaki bağlı hizmet türleri eklendi:
  * [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
  * [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* Aşağıdaki etkinlik türleri eklendi:
  * [Datalakeanaliz Ticsusqlactivity](/dotnet/api/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity)
* Aşağıdaki veri kümesi türleri eklendi:
  * [AzureDataLakeStoreDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoredataset)
* Kopyalama etkinliği için aşağıdaki kaynak ve havuz türleri eklendi:
  * [AzureDataLakeStoreSource](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresource)
  * [AzureDataLakeStoreSink](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresink)

## <a name="version-401"></a>Sürüm 4.0.1
### <a name="breaking-changes"></a>Yeni değişiklikler
Aşağıdaki sınıflar yeniden adlandırıldı. Yeni adlar 4.0.0 sürümünden önceki özgün sınıfların adlarıdır.

| 4.0.0 içindeki ad | 4.0.1 içindeki ad |
|:--- |:--- |
| Azuressqldatawarehousedataset |[Azuressqldatawarehousetabledataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset) |
| Azuressqldataset |[Azuressqltabledataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqltabledataset) |
| AzureDataset |[AzureTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuretabledataset) |
| OracleDataset |[OracleTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.oracletabledataset) |
| RelationalDataset |[RelationalTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.relationaltabledataset) |
| SqlServerDataset |[SqlServerTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.sqlservertabledataset) |

## <a name="version-400"></a>Sürüm 4.0.0
### <a name="breaking-changes"></a>Yeni değişiklikler
* Aşağıdaki sınıflar/arabirimler yeniden adlandırıldı.

| Eski ad | Yeni ad |
|:--- |:--- |
| Itableoperations |[Idatasetoperations](/dotnet/api/microsoft.azure.management.datafactories.idatasetoperations) |
| Tablo |[Veri kümesi](/dotnet/api/microsoft.azure.management.datafactories.models.dataset) |
| TableProperties |[DatasetProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasetproperties) |
| TableTypeProprerties |[DatasetTypeProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasettypeproperties) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters) |
| Tablecreateorupdateresbir |[Datasetcreateorupdateres,](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse) |
| TableGetResponse |[DatasetGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetgetresponse) |
| TableListResponse |[DatasetListResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetlistresponse) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters) |

* **Liste** yöntemleri artık disk belleğine alınmış sonuçları geri döndürüyor. Yanıt boş olmayan bir **NextLink** özelliği içeriyorsa, tüm sayfalar döndürülünceye kadar istemci uygulamanın sonraki sayfayı getirmeye devam etmesi gerekir.  Aşağıda bir örnek verilmiştir:

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
* [Sqldwsink](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) sınıfı iki yeni özelliği **destekler, bu, Azure** Azure SYNAPSE Analytics 'e ıdempotent kopyalamayı desteklemek için. Bu özellikler hakkında daha fazla bilgi için bkz. [Azure SYNAPSE Analytics](data-factory-azure-sql-data-warehouse-connector.md) makalesi.
* Artık kopyalama etkinliğinin bir parçası olarak Azure SQL veritabanı ve Azure SYNAPSE Analytics kaynakları için saklı yordam çalıştırmayı destekliyoruz. [SQLSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqlsource) ve [sqldwsource](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsource) sınıfları şu özelliklere sahiptir: **SqlReaderStoredProcedureName** ve **StoredProcedureParameters**. Bu özellikler hakkındaki ayrıntılar için bkz. [Azure SQL veritabanı](data-factory-azure-sql-connector.md#sqlsource) ve [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) makaleleri Azure.com.