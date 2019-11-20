---
title: Azure Izleyici 'de Azure depolama ölçümleri | Microsoft Docs
description: Azure Izleyici 'den sunulan yeni ölçümler hakkında bilgi edinin.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: c2f6847a286a9c106fc094e9f0aa315d6b1f337d
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257096"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Azure İzleyici’de Azure Depolama ölçümleri

Azure depolama ile ilgili ölçümler sayesinde kullanım eğilimlerini çözümleyebilir, istekleri izleyebilir ve depolama hesabınızla ilgili sorunları tanılayabilirsiniz.

Azure Izleyici, farklı Azure hizmetleri genelinde izleme için birleştirilmiş kullanıcı arabirimleri sağlar. Daha fazla bilgi için bkz. [Azure izleyici](../../monitoring-and-diagnostics/monitoring-overview.md). Azure depolama, Azure izleyici platformuna ölçüm verileri göndererek Azure Izleyicisini tümleştirir.

## <a name="access-metrics"></a>Erişim ölçümleri

Azure İzleyici ölçümlerine erişim birden çok yol sağlar. Bunlara [Azure Portal](https://portal.azure.com), Azure Izleyici API 'LERI (REST ve .net) ve Event Hubs gibi çözümleme çözümlerini de erişebilirsiniz. Daha fazla bilgi için bkz. [Azure Izleyici ölçümleri](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Ölçümler varsayılan olarak etkindir ve son 93 güne kadar veri erişimi sağlayabilirsiniz. Uzun bir süre saklamak istiyorsanız ölçüm verileri bir Azure depolama hesabına arşivleyebilir. Bu yapılandırılan [tanılama ayarları](../../azure-monitor/platform/resource-logs-overview.md) Azure İzleyici'de.

### <a name="access-metrics-in-the-azure-portal"></a>Azure portal ölçümler 'e erişin

Azure portal ölçümleri zaman içinde izleyebilirsiniz. Aşağıdaki örnek, **işlemlerin** hesap düzeyinde nasıl görüntüleneceğini gösterir.

![Azure portal ölçümlere erişme ekran görüntüsü](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Boyutları destekleyen ölçümler için, ölçüyü istenen boyut değeriyle filtreleyebilirsiniz. Aşağıdaki örnek, **API ad** boyutu için değerler seçilerek belirli bir işlemdeki hesap düzeyinde **işlemlerin** nasıl görüntüleneceğini gösterir.

![Azure portal Dimension ile ölçümlere erişme ekran görüntüsü](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>REST API ölçümler ile erişin

Azure Izleyici, ölçüm tanımını ve değerlerini okumak için [REST API 'ler](/rest/api/monitor/) sağlar. Bu bölümde, depolama ölçümlerini nasıl okuyabilmeniz gösterilmektedir. Kaynak KIMLIĞI, tüm REST API 'lerinde kullanılır. Daha fazla bilgi için lütfen depolama alanındaki hizmetler için kaynak KIMLIĞINI anlama konusunu okuyun.

Aşağıdaki örnek, REST API test etmeyi basitleştirmek için komut satırında [Armclient](https://github.com/projectkudu/ARMClient) 'ın nasıl kullanılacağını gösterir.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Hesap düzeyi ölçüm tanımını REST API listeleyin

Aşağıdaki örnek, ölçüm tanımının hesap düzeyinde nasıl ekleneceğini gösterir:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Blob, tablo, dosya veya kuyruğun ölçüm tanımlarını listelemek istiyorsanız, API ile her hizmet için farklı kaynak kimlikleri belirtmeniz gerekir.

Yanıt, JSON biçiminde ölçüm tanımını içerir:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Hesap düzeyindeki ölçüm değerlerini REST API ile okuyun

Aşağıdaki örnek, ölçüm verilerinin hesap düzeyinde nasıl okunacağını gösterir:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

Yukarıdaki örnekte, blob, tablo, dosya veya kuyrukla ilgili ölçüm değerlerini okumak istiyorsanız, API ile her hizmet için farklı kaynak kimlikleri belirtmeniz gerekir.

Aşağıdaki yanıt, JSON biçiminde ölçüm değerleri içerir:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>.NET SDK ile ölçümlere erişin

Azure Izleyici, ölçüm tanımını ve değerlerini okumak için [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) sağlar. [Örnek kod](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) , SDK 'nın farklı parametrelerle nasıl kullanılacağını gösterir. Depolama ölçümleri için veya `0.18.0-preview` sonraki sürümü kullanmanız gerekir. Kaynak KIMLIĞI .NET SDK 'da kullanılır. Daha fazla bilgi için lütfen depolama alanındaki hizmetler için kaynak KIMLIĞINI anlama konusunu okuyun.

Aşağıdaki örnek, depolama ölçümlerini okumak için Azure Izleyici .NET SDK 'sını nasıl kullanacağınızı gösterir.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>.NET SDK ile hesap düzeyi ölçüm tanımını listeleme

Aşağıdaki örnek, ölçüm tanımının hesap düzeyinde nasıl ekleneceğini gösterir:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Blob, tablo, dosya veya kuyruğun ölçüm tanımlarını listelemek istiyorsanız, API ile her hizmet için farklı kaynak kimlikleri belirtmeniz gerekir.

#### <a name="read-metric-values-with-the-net-sdk"></a>.NET SDK ile ölçüm değerlerini okuma

Aşağıdaki örnek, hesap düzeyinde verilerin nasıl `UsedCapacity` okunacağını gösterir:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

Yukarıdaki örnekte, blob, tablo, dosya veya kuyrukla ilgili ölçüm değerlerini okumak istiyorsanız, API ile her hizmet için farklı kaynak kimlikleri belirtmeniz gerekir.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>.NET SDK ile çok boyutlu ölçüm değerlerini okuma

Çok boyutlu ölçümler için, belirli bir boyut değerindeki ölçüm verilerini okumak istiyorsanız meta veri filtresini tanımlamanız gerekir.

Aşağıdaki örnek, çoklu boyut desteği ile ölçüm verilerinin nasıl okunacağını gösterir:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Azure depolama 'daki hizmetler için kaynak KIMLIĞINI anlama

Kaynak KIMLIĞI, Azure 'daki bir kaynağın benzersiz tanımlayıcısıdır. Ölçüm tanımlarını veya değerlerini okumak için Azure Izleyici REST API kullandığınızda, üzerinde çalışmak istediğiniz kaynak için kaynak KIMLIĞI ' ni kullanmanız gerekir. Kaynak KIMLIĞI şablonu şu biçimdedir:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Depolama, Azure Izleyici ile hem depolama hesabı düzeyinde hem de hizmet düzeyinde ölçümler sağlar. Örneğin, yalnızca BLOB depolama için ölçümleri alabilirsiniz. Her düzeyin kendi kaynak KIMLIĞI vardır. Bu, yalnızca o düzeydeki ölçümleri almak için kullanılır.

### <a name="resource-id-for-a-storage-account"></a>Depolama hesabı için kaynak KIMLIĞI

Aşağıda, bir depolama hesabı için kaynak KIMLIĞINI belirtme biçimi gösterilmektedir.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>Depolama Hizmetleri için kaynak KIMLIĞI

Aşağıda, depolama hizmetlerinin her biri için kaynak KIMLIĞINI belirtme biçimi gösterilmektedir.

* Blob hizmeti kaynak kimliği
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* Tablo hizmeti kaynak kimliği
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* Kuyruk hizmeti kaynak kimliği
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* Dosya hizmeti kaynak kimliği
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>Azure Izleyici REST API kaynak KIMLIĞI

Aşağıda, Azure Izleyici REST API çağrılırken kullanılan desenler gösterilmektedir.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Kapasite ölçümleri
Kapasite ölçümleri değerleri her saat Azure Izleyici 'ye gönderilir. Değerler günlük olarak yenilenir. Zaman dilimi, ölçüm değerlerinin sunulduğu zaman aralığını tanımlar. Tüm kapasite ölçümleri için desteklenen zaman dilimi bir saattir (PT1H).

Azure depolama, Azure Izleyici 'de aşağıdaki kapasite ölçümlerini sağlar.

### <a name="account-level"></a>Hesap düzeyi

| Ölçüm Adı | Açıklama |
| ------------------- | ----------------- |
| UsedCapacity | Depolama hesabı tarafından kullanılan depolama miktarı. Standart depolama hesapları için blob, tablo, dosya ve kuyruk tarafından kullanılan kapasitenin toplamıdır. Premium depolama ve Blob Depolama hesapları için BlobCapacity ile aynıdır. <br/><br/> Birim Bayt <br/> Toplama türü: Average <br/> Değer örneği: 1024 |

### <a name="blob-storage"></a>Blob depolama

| Ölçüm Adı | Açıklama |
| ------------------- | ----------------- |
| BlobCapacity | Depolama hesabında kullanılan BLOB depolama alanı toplamı. <br/><br/> Birim Bayt <br/> Toplama türü: Average <br/> Değer örneği: 1024 <br/> Boyutlar: **Blobtype**ve **blobtier** ([tanım](#metrics-dimensions)) |
| BLOB sayısı    | Depolama hesabında depolanan BLOB nesnelerinin sayısı. <br/><br/> Birim Count <br/> Toplama türü: Average <br/> Değer örneği: 1024 <br/> Boyutlar: **Blobtype**ve **blobtier** ([tanım](#metrics-dimensions)) |
| ContainerCount    | Depolama hesabındaki kapsayıcıların sayısı. <br/><br/> Birim Count <br/> Toplama türü: Average <br/> Değer örneği: 1024 |
| Dizin kapasitesi     | ADLS 2. hiyerarşik dizin tarafından kullanılan depolama miktarı <br/><br/> Birim Bayt <br/> Toplama türü: Average <br/> Değer örneği: 1024 |

### <a name="table-storage"></a>Tablo depolama alanı

| Ölçüm Adı | Açıklama |
| ------------------- | ----------------- |
| TableCapacity | Depolama hesabı tarafından kullanılan tablo depolaması miktarı. <br/><br/> Birim Bayt <br/> Toplama türü: Average <br/> Değer örneği: 1024 |
| TableCount   | Depolama hesabındaki tablo sayısı. <br/><br/> Birim Count <br/> Toplama türü: Average <br/> Değer örneği: 1024 |
| TableEntityCount | Depolama hesabındaki tablo varlıklarının sayısı. <br/><br/> Birim Count <br/> Toplama türü: Average <br/> Değer örneği: 1024 |

### <a name="queue-storage"></a>Kuyruk depolama

| Ölçüm Adı | Açıklama |
| ------------------- | ----------------- |
| QueueCapacity | Depolama hesabı tarafından kullanılan kuyruk depolama miktarı. <br/><br/> Birim Bayt <br/> Toplama türü: Average <br/> Değer örneği: 1024 |
| QueueCount   | Depolama hesabındaki sıraların sayısı. <br/><br/> Birim Count <br/> Toplama türü: Average <br/> Değer örneği: 1024 |
| QueueMessageCount | Depolama hesabındaki süre dolma edilmemiş sıra iletilerinin sayısı. <br/><br/>Birim Count <br/> Toplama türü: Average <br/> Değer örneği: 1024 |

### <a name="file-storage"></a>Dosya depolama

| Ölçüm Adı | Açıklama |
| ------------------- | ----------------- |
| Dosya kapasitesi | Depolama hesabı tarafından kullanılan dosya depolama alanı miktarı. <br/><br/> Birim Bayt <br/> Toplama türü: Average <br/> Değer örneği: 1024 |
| FileCount   | Depolama hesabındaki dosyaların sayısı. <br/><br/> Birim Count <br/> Toplama türü: Average <br/> Değer örneği: 1024 |
| Dosya ShareCount | Depolama hesabındaki dosya paylaşımlarının sayısı. <br/><br/> Birim Count <br/> Toplama türü: Average <br/> Değer örneği: 1024 |

## <a name="transaction-metrics"></a>İşlem ölçümleri

İşlem ölçümleri, Azure depolama 'daki bir depolama hesabına yönelik her istek için Azure Izleyici 'ye dağıtılır. Depolama hesabınızda hiçbir etkinlik olmaması durumunda, dönem içinde işlem ölçümlerinde hiç veri olmayacaktır. Tüm işlem ölçümleri hem hesap hem de hizmet düzeyinde (BLOB depolama, tablo depolama, Azure dosyaları ve kuyruk depolama) kullanılabilir. Zaman dilimi, ölçüm değerlerinin sunulduğu zaman aralığını tanımlar. Tüm işlem ölçümleri için desteklenen saat graıns PT1H ve PT1M.

Azure depolama, Azure Izleyici 'de aşağıdaki işlem ölçümlerini sağlar.

| Ölçüm Adı | Açıklama |
| ------------------- | ----------------- |
| İşlemler | Bir depolama hizmetine yapılan isteklerin veya belirtilen API işlemi sayısı. Bu sayı, başarılı ve başarısız istekleri ve hata üreten istekleri içerir. <br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Geçerli Boyutlar: ResponseType, GeoType, ApiName ve Authentication ([tanım](#metrics-dimensions))<br/> Değer örneği: 1024 |
| Giriş | Giriş verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya giren ve Azure içinde giren verileri içerir. <br/><br/> Birim Bayt <br/> Toplama türü: Toplam <br/> Geçerli Boyutlar: GeoType, ApiName ve Authentication ([tanım](#metrics-dimensions)) <br/> Değer örneği: 1024 |
| Çıkış | Çıkış verileri miktarı. Bu sayı, dış istemciden Azure Depolama'ya çıkan ve Azure içinde çıkan verileri içerir. Sonuç olarak bu sayı, faturalanabilir çıkışı yansıtmaz. <br/><br/> Birim Bayt <br/> Toplama türü: Toplam <br/> Geçerli Boyutlar: GeoType, ApiName ve Authentication ([tanım](#metrics-dimensions)) <br/> Değer örneği: 1024 |
| SuccessServerLatency | Azure Depolama tarafından gerçekleştirilen başarılı bir isteği işlemek için kullanılan ortalama süre. Bu değer, Başarı E2E Gecikme Süresi’nde belirtilen ağ gecikme süresini içermez. <br/><br/> Birim Milisaniye <br/> Toplama türü: Average <br/> Geçerli Boyutlar: GeoType, ApiName ve Authentication ([tanım](#metrics-dimensions)) <br/> Değer örneği: 1024 |
| Başarı E2e | Bir depolama hizmetine yapılan başarılı isteklerin veya belirtilen API işleminin ortalama uçtan uca gecikme süresi. Bu değer, isteği okumak, yanıtı göndermek ve yanıtın onayını almak için Azure Depolama içinde gerekli işleme süresini içerir. <br/><br/> Birim Milisaniye <br/> Toplama türü: Average <br/> Geçerli Boyutlar: GeoType, ApiName ve Authentication ([tanım](#metrics-dimensions)) <br/> Değer örneği: 1024 |
| Kullanılabilirlik | Depolama hizmetinin veya belirtilen API işleminin kullanılabilirlik yüzdesi. Kullanılabilirlik, toplam faturalandırılabilir istek değerinin beklenmeyen hata üreten istekler dahil olmak üzere ilgili istek sayısına bölünmesiyle hesaplanır. Azaltılmış kullanılabilirlik ve depolama hizmetine veya belirtilen API işlemi için beklenmeyen tüm hatalar sonuçlanır. <br/><br/> Birim Percent <br/> Toplama türü: Average <br/> Geçerli Boyutlar: GeoType, ApiName ve Authentication ([tanım](#metrics-dimensions)) <br/> Değer örneği: 99,99 |

## <a name="metrics-dimensions"></a>Ölçümleri boyutları

Azure depolama, Azure Izleyici 'de ölçümler için aşağıdaki boyutları destekler.

| Boyut Adı | Açıklama |
| ------------------- | ----------------- |
| **BlobType** | Yalnızca blob ölçümleri için blob türü. Desteklenen değerler **Blockblob**, **pageblob**ve **Azure Data Lake Storage**. Append blobu BlockBlob 'a dahildir. |
| **BlobTier** | Azure depolama, blob nesne verilerini en düşük maliyetli biçimde depolamanıza olanak sağlayan farklı erişim katmanları sunar. Bkz. [Azure Storage blob katmanında](../blobs/storage-blob-storage-tiers.md)daha fazla bilgi. Desteklenen değerler şunlardır: <br/> <li>**Sık**erişimli: Etkin katman</li> <li>Seyrek **erişimli:** Cool katmanı</li> <li>**Arşiv**: Arşiv katmanı</li> <li>**Premium**: Blok Blobu için Premium katman</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Premium Sayfa Blobu için katman türleri</li> <li>**Standart**: Standart Sayfa Blobu için katman türü</li> <li>**Katmanlı olmayan**: Genel amaçlı v1 depolama hesabı için katman türü</li> |
| **GeoType** | Birincil veya Ikincil kümeden işlem. Kullanılabilir değerler **PRIMARY** ve **Secondary**' i içerir. İkincil kiracının nesnelerini okurken Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) için geçerlidir. |
| **ResponseType** | İşlem yanıt türü. Kullanılabilir değerler şunlardır: <br/><br/> <li>**ServerOtherError**: Açıklananlar hariç diğer tüm sunucu tarafı hatalar </li> <li>**ServerBusyError**: HTTP 503 durum kodu döndüren, kimliği doğrulanmış istek. </li> <li>**ServerTimeoutError**: HTTP 500 durum kodu döndüren, zaman aşımına uğramış ve kimliği doğrulanmış istek. Zaman aşımı bir sunucu hatası nedeniyle gerçekleşti. </li> <li>**AuthorizationError**: Yetkisiz veri erişimi veya yetkilendirme hatası nedeniyle başarısız olmuş bir kimliği doğrulanmış istek. </li> <li>**NetworkError**: Ağ hataları nedeniyle başarısız olmuş bir kimliği doğrulanmış istek. Çoğunlukla bir istemci, zaman aşımı süre sonundan önce bağlantıyı erkenden kapattığında gerçekleşir. </li> <li>**ClientThrottlingError**: İstemci tarafı azaltma hatası. </li> <li>**ClientTimeoutError**: HTTP 500 durum kodu döndüren, zaman aşımına uğramış ve kimliği doğrulanmış istek. İstemcinin ağ zaman aşımı veya istek zaman aşımı depolama hizmetinin beklediğinden düşük bir değere ayarlanmışsa beklenen bir zaman aşımıdır. Aksi takdirde, bir ServerTimeoutError olarak bildirilir. </li> <li>**ClientOtherError**: Açıklananlar hariç diğer tüm istemci tarafı hatalar. </li> <li>**Success**: Başarılı istek</li> <li> **Başarılı bir kısıtlama**: Bir SMB istemcisi ilk denemede kısıtlandığında, ancak yeniden denemeler yapıldıktan sonra başarılı olan istek başarılı oldu.</li> |
| **ApiName** | İşlemin adı. Örneğin: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Tüm işlem adları için bkz. [belge](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Kimlik Doğrulaması** | İşlemlerde kullanılan kimlik doğrulaması türü. Kullanılabilir değerler şunlardır: <br/> <li>**Accountkey**: İşlem, depolama hesabı anahtarıyla doğrulanır.</li> <li>**SAS**: İşlem, paylaşılan erişim imzaları ile doğrulanır.</li> <li>**OAuth**: İşlem, OAuth erişim belirteçleriyle doğrulanır.</li> <li>**Anonim**: İşlem anonim olarak istendi. Bu, ön kontrol isteklerini içermez.</li> <li>**Anonymousön hazırlığı**: İşlem, ön kontrol isteği.</li> |

Boyutları destekleyen ölçümler için, karşılık gelen ölçüm değerlerini görmek için boyut değerini belirtmeniz gerekir. Örneğin, başarılı yanıtlar için **işlem** değerine bakarsanız, **responseType** boyutunu **başarılı**olarak filtrelemeniz gerekir. Ya da Blok Blobu için **Blobcount** değerine bakarsanız, **Blobtype** boyutunu **blockblob**ile filtrelemeniz gerekir.

## <a name="service-continuity-of-legacy-metrics"></a>Eski ölçümlerin hizmet sürekliliği

Eski ölçümler Azure Izleyici yönetilen ölçümleriyle paralel olarak kullanılabilir. Destek, Azure Storage hizmeti eski ölçümlerde sona erdirene kadar aynı kalır.

## <a name="faq"></a>SSS

**Yeni ölçümler klasik depolama hesabını destekliyor mu?**

Hayır, Azure Izleyici 'de yeni ölçümler yalnızca Azure Resource Manager depolama hesaplarını destekler. Depolama hesaplarında ölçümleri kullanmak istiyorsanız, Azure Resource Manager depolama hesabına geçiş yapmanız gerekir. Bkz. [Azure Resource Manager 'ye geçirme](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Azure depolama, yönetilen diskler veya yönetilmeyen diskler için ölçümleri destekliyor mu?**

Hayır, Azure Işlem disklerdeki ölçümleri destekler. Daha fazla bilgi için [makalesine](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) bakın.

**Klasik ölçümler yeni ölçümler ile nasıl eşlenir ve geçirilir?**

Klasik ölçümler ve yeni ölçümler arasında ayrıntılı eşlemeyi [Azure depolama ölçümleri geçişi](./storage-metrics-migration.md)'nde bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure İzleyici](../../monitoring-and-diagnostics/monitoring-overview.md)
