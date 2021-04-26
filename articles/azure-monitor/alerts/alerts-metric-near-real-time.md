---
title: Azure Izleyici 'de ölçüm uyarıları için desteklenen kaynaklar
description: Azure Izleyici 'de ölçüm uyarıları için destek ölçümlerine ve günlüklerine yönelik başvuru
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 24298734a46b8339a2a8818692641b4c10812294
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104890"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Azure Izleyici 'de ölçüm uyarıları için desteklenen kaynaklar

Azure Izleyici artık [Klasik eski ölçüm uyarıları](./alerts-classic.overview.md)üzerinde önemli avantajlar içeren [Yeni bir ölçüm uyarı türünü](./alerts-overview.md) desteklemektedir. Ölçümler, [büyük Azure hizmetleri listesi](../essentials/metrics-supported.md)için kullanılabilir. Yeni uyarılar, kaynak türlerinin (büyüyen) bir alt kümesini destekler. Bu makalede bu alt küme listelenir.

Ölçüm olarak ayıklanmış bir Log Analytics çalışma alanında depolanan popüler günlük verilerinde daha yeni ölçüm uyarıları da kullanabilirsiniz. Daha fazla bilgi için, [Günlükler Için ölçüm uyarılarını](./alerts-metric-logs.md)görüntüleyin.

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLı, REST desteği
Şu anda yalnızca Azure portal, [REST API](/rest/api/monitor/metricalerts/)veya [Kaynak Yöneticisi şablonlarda](./alerts-metric-create-templates.md)daha yeni ölçüm uyarıları oluşturabilirsiniz. PowerShell ve Azure CLı sürümleri 2,0 ve üstünü kullanarak daha yeni uyarılar yapılandırmaya yönelik destek yakında kullanıma sunulacak.

## <a name="metrics-and-dimensions-supported"></a>Desteklenen ölçümler ve boyutlar
Daha yeni ölçüm uyarıları, boyutları kullanan ölçümler için uyarı vermeyi destekler. Ölçülerinizi doğru düzeye filtrelemek için boyutları kullanabilirsiniz. Geçerli boyutlarla birlikte desteklenen tüm ölçümler, [Azure izleyici-ölçüm Gezgini](../essentials/metrics-charts.md)tarafından araştırılabilir ve görselleştirilir.

Daha yeni uyarılar tarafından desteklenen Azure Izleyici ölçüm kaynaklarının tam listesi aşağıda verilmiştir:

|Kaynak türü  |Desteklenen boyutlar |Çoklu kaynak uyarıları| Kullanılabilir ölçümler|
|---------|---------|-----|----------|
|Microsoft. Aadihar/Azureadölçümlerini | Yes | Hayır | [Azure AD](../essentials/metrics-supported.md#microsoftaadiamazureadmetrics) |
|Microsoft.ApiManagement/service | Yes | Hayır | [API Management](../essentials/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft. AppConfiguration/Configurationmağazaları |Yes | Hayır | [Uygulama Yapılandırması](../essentials/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft. AppPlatform/yay | Yes | Hayır | [Azure Spring Cloud](../essentials/metrics-supported.md#microsoftappplatformspring) |
|Microsoft. Automation/automationAccounts | Yes| Hayır | [Otomasyon Hesapları](../essentials/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/Privatebulutlar | Hayır | Hayır | [Azure VMware Çözümü](../essentials/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.Batch/batchAccounts | Yes | Hayır | [Toplu Hesaplar](../essentials/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft. BotService/botServices | Yes | Hayır | [Bot Hizmetleri](../essentials/metrics-supported.md#microsoftbotservicebotservices) |
|Microsoft. Cache/redsıs | Yes | Yes | [Redis için Azure Önbelleği](../essentials/metrics-supported.md#microsoftcacheredis) |
|MICROSOFT. CDN/profiller | Yes | Hayır | [CDN Profilleri](../essentials/metrics-supported.md#microsoftcdnprofiles) |
|Microsoft. ClassicCompute/domainNames/yuvalar/roller | Hayır | Hayır | [Klasik Cloud Services](../essentials/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft. ClassicCompute/virtualMachines | Hayır | Hayır | [Klasik sanal makineler](../essentials/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft. ClassicStorage/storageAccounts | Yes | Hayır | [Depolama hesapları (klasik)](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft. ClassicStorage/storageAccounts/blobServices | Yes | Hayır | [Depolama hesapları (klasik)-blob 'Lar](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft. ClassicStorage/storageAccounts/fileServices | Yes | Hayır | [Depolama hesapları (klasik)-dosyalar](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft. ClassicStorage/storageAccounts/queueServices | Yes | Hayır | [Depolama hesapları (klasik)-kuyruklar](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft. ClassicStorage/storageAccounts/tableServices | Yes | Hayır | [Depolama hesapları (klasik)-tablolar](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft. Biliveservices/hesapları | Yes | Hayır | [Bilişsel Hizmetler](../essentials/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft. COMPUTE/cloudServices | Yes | Hayır |  [Bulut Hizmetleri](../essentials/metrics-supported.md#microsoftcomputecloudservices) |
|Microsoft. COMPUTE/cloudServices/rolleri | Yes | Hayır |  [Bulut hizmeti rolleri](../essentials/metrics-supported.md#microsoftcomputecloudservicesroles) |
|Microsoft.Compute/virtualMachines | Yes | Evet<sup>1</sup> | [Sanal Makineler](../essentials/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Yes | Hayır |[Sanal Makine Ölçek Kümeleri](../essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft. Containerınstance/containerGroups | Yes| Hayır | [Kapsayıcı grupları](../essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft. ContainerRegistry/kayıt defterleri | Hayır | Hayır | [Kapsayıcı kayıt defterleri](../essentials/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft. ContainerService/Managedkümeler | Yes | Hayır | [Yönetilen Kümeler](../essentials/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Yes | Yes | [Data Box](../essentials/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft. DataFactory/DataFactory| Yes| Hayır | [Veri fabrikaları v1](../essentials/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft. DataFactory/Factory |Yes | Hayır | [Veri fabrikaları v2](../essentials/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft. DataShare/hesapları | Yes | Hayır | [Veri paylaşımları](../essentials/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft. Dbformarıdb/sunucular | Hayır | Hayır | [MariaDB için DB](../essentials/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft. Dbformyısql/sunucuları | Hayır | Hayır |[MySQL için DB](../essentials/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft. DBforPostgreSQL/Flexibtaservers | Yes | Hayır | [PostgreSQL için DB (esnek sunucular)](../essentials/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft. DBforPostgreSQL/serverGroupsv2 | Yes | Hayır | PostgreSQL için DB (hyperscale) |
|Microsoft. DBforPostgreSQL/sunucuları | Hayır | Hayır | [PostgreSQL için DB](../essentials/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft. DBforPostgreSQL/serversv2 | Hayır | Hayır | [PostgreSQL için DB v2](../essentials/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft. Devices/IotHubs | Yes | Hayır |[IoT Hub’ı](../essentials/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft. Devices/provisioningServices| Yes | Hayır | [Cihaz sağlama hizmetleri](../essentials/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/Digitaltwınsınstances | Yes | Hayır | [Digital Twins](../essentials/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|UmentDB/databaseAccounts Microsoft.Doc | Yes | Hayır | [Cosmos DB](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft. EventGrid/Domains | Yes | Hayır | [Event Grid Etki Alanları](../essentials/metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/Systemkonuları | Yes | Hayır | [Event Grid sistem konuları](../essentials/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft. EventGrid/konuları |Yes | Hayır | [Event Grid Konu Başlıkları](../essentials/metrics-supported.md#microsofteventgridtopics) |
|Microsoft. EventHub/kümeler |Yes| Hayır | [Event Hubs kümeleri](../essentials/metrics-supported.md#microsofteventhubclusters) |
|Microsoft. EventHub/ad alanları |Yes| Hayır | [Event Hubs](../essentials/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft. HDInsight/kümeler | Yes | Hayır | [HDInsight Kümeleri](../essentials/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft. Insights/bileşenler | Yes | Hayır | [Uygulama Bilgileri](../essentials/metrics-supported.md#microsoftinsightscomponents) |
|Microsoft. Keykasası/kasa | Yes |Yes |[Kasalar](../essentials/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft. kusto/kümeler | Yes |Hayır |[Veri Gezgini kümeleri](../essentials/metrics-supported.md#microsoftkustoclusters)|
|Microsoft. Logic/ıntegrationserviceortamortamları | Yes | Hayır |[Tümleştirme hizmeti ortamları](../essentials/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft. Logic/iş akışları | Hayır | Hayır |[Logic Apps](../essentials/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft. MachineLearningServices/çalışma alanları | Yes | Hayır | [Machine Learning](../essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft. MachineLearningServices/Workspaces/onlineEndpoints | Yes | Hayır | Machine Learning uç noktaları |
|Microsoft. MachineLearningServices/Workspaces/onlineEndpoints/dağıtımlar | Yes | Hayır | Machine Learning uç nokta dağıtımları |
|Microsoft. Maps/hesaplar | Yes | Hayır | [Hesapları eşleme](../essentials/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft. Media/mediaservices | Hayır | Hayır | [Media Services](../essentials/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft. Media/mediaservices/streamingEndpoints | Yes | Hayır | [Media Services akış uç noktaları](../essentials/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft. NetApp/netAppAccounts/Capacityhavuzları | Yes | Yes | [Azure NetApp kapasite havuzları](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/netAppAccounts/Capacityhavuzları/birimleri | Yes | Yes | [Azure NetApp birimleri](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft. Network/Applicationgateway 'ler | Yes | Hayır | [Uygulama Ağ Geçitleri](../essentials/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft. Network/azurefirewalls | Yes | Hayır | [Güvenlik duvarları](../essentials/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft. Network/dnsZones | Hayır | Hayır | [DNS Bölgeleri](../essentials/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft. Network/Expressroutedevreleri | Yes | Hayır |[ExpressRoute devreleri](../essentials/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/expressRoutePorts | Yes | Hayır |[ExpressRoute Direct](../essentials/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft. Network/loadBalancers (yalnızca standart SKU 'Lar için)| Yes| Hayır | [Yük dengeleyiciler](../essentials/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/Natgateway 'ler| Hayır | Hayır | [NAT ağ geçitleri](../essentials/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft. Network/privateEndpoints| Hayır | Hayır | [Özel uç noktalar](../essentials/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft. Network/privateLinkServices| Hayır | Hayır | [Özel bağlantı Hizmetleri](../essentials/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft. Network/publicıpaddresses | Hayır | Hayır | [Genel IP Adresleri](../essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft. Network/trafficManagerProfiles | Yes | Hayır | [Traffic Manager Profilleri](../essentials/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. Operationalınsights/çalışma alanları| Yes | Hayır | [Log Analytics çalışma alanları](../essentials/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. eşleme/eşlemeler | Yes | Hayır | [Eşlemeler](../essentials/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. eşleme/peeringServices | Yes | Hayır | [Eşleme Hizmetleri](../essentials/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft. Powerbiadanmış/kapasiteler | Hayır | Hayır | [Kapasiteler](../essentials/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft. Relay/Namespace | Yes | Hayır | [Geçişler](../essentials/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft. Search/searchServices | Hayır | Hayır | [Arama Hizmetleri](../essentials/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft. ServiceBus/ad alanları | Yes | Hayır | [Service Bus](../essentials/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft. SQL/ManagedInstances | Hayır | Yes | [SQL Yönetilen Örnekleri](../essentials/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft. SQL/Servers/veritabanları | Hayır | Yes | [SQL Veritabanları](../essentials/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft. SQL/Servers/Elaun havuzları | Hayır | Yes | [SQL elastik havuzları](../essentials/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft. Storage/storageAccounts |Yes | Hayır | [Depolama Hesapları](../essentials/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft. Storage/storageAccounts/blobServices | Yes| Hayır | [Depolama hesapları-Bloblar](../essentials/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft. Storage/storageAccounts/fileServices | Yes| Hayır | [Depolama hesapları-dosyalar](../essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft. Storage/storageAccounts/queueServices | Yes| Hayır | [Depolama hesapları-kuyruklar](../essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft. Storage/storageAccounts/tableServices | Yes| Hayır | [Depolama hesapları-tablolar](../essentials/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft. StorageCache/önbellekler | Yes | Hayır | [HPC önbellekleri](../essentials/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft. Storagessync/storageSyncServices | Yes | Hayır | [Depolama Eşitleme Hizmetleri](../essentials/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft. StreamAnalytics/streamingjobs | Yes | Hayır | [Stream Analytics](../essentials/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft. SYNAPSE/çalışma alanları | Yes | Hayır | [Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft. SYNAPSE/Workspaces/bigDataPools | Yes | Hayır | [SYNAPSE Analytics Apache Spark havuzları](../essentials/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft. SYNAPSE/Workspaces/Sqlhavuzlar | Yes | Hayır | [SYNAPSE Analytics SQL havuzları](../essentials/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft. Vmwarechoparlör basit/virtualMachines | Yes | Hayır | [CloudSimple Sanal Makineleri](../essentials/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft. Web/hostingEnvironments/multiRolePools | Yes | Hayır | [Çok rollü havuzlar App Service Ortamı](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft. Web/hostingEnvironments/workerPools | Yes | Hayır | [App Service Ortamı çalışan havuzları](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft. Web/sunucugrupları | Yes | Hayır | [App Service planları](../essentials/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft. Web/siteler | Yes | Hayır | [Uygulama hizmetleri ve Işlevleri](../essentials/metrics-supported.md#microsoftwebsites)|
|Microsoft. Web/Sites/Yuvaları | Yes | Hayır | [App Service Yuvaları](../essentials/metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> sanal makine ağ ölçümleri (Toplam ağ, ağ üzerinden toplam, gelen akış, giden akış, gelen akış sayısı üst sınırı, çıkış akışı en yüksek oluşturma oranı) ve özel ölçümler için desteklenmez.

## <a name="payload-schema"></a>Yük şeması

> [!NOTE]
> Web kancası tümleştirmelerinize yönelik olarak, Azure Izleyici 'deki tüm uyarı hizmetlerinde tek bir Genişletilebilir ve birleştirilmiş uyarı yüküne sahip olmanın avantajını sağlayan [ortak uyarı şemasını](./alerts-common-schema.md)da kullanabilirsiniz. [Ortak uyarı şeması tanımları hakkında bilgi edinin.](./alerts-common-schema-definitions.md)


POST işlemi, uygun şekilde yapılandırılmış bir [eylem grubu](./action-groups.md) kullanıldığında, neredeyse tüm yeni ölçüm uyarıları IÇIN aşağıdaki JSON yükünü ve şemayı içerir:

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

* Yeni [Uyarılar deneyimi](./alerts-overview.md)hakkında daha fazla bilgi edinin.
* [Azure 'da günlük uyarıları](./alerts-unified-log.md)hakkında bilgi edinin.
* [Azure 'daki uyarılar](./alerts-overview.md)hakkında bilgi edinin.
