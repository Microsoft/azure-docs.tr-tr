---
title: Kaynak türüne göre işlem desteğini taşıma
description: Yeni bir kaynak grubuna veya aboneliğe taşınabilecek Azure Kaynak türlerini listeler.
ms.topic: reference
ms.date: 10/24/2019
ms.openlocfilehash: 182e5fdd081126500c04f44776c9e9b8fad61843
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149774"
---
# <a name="move-operation-support-for-resources"></a>Kaynaklar için taşıma işlemi desteği
Bu makalede, bir Azure Kaynak türünün taşıma işlemini destekleyip desteklemediğini listelenmiştir. Ayrıca bir kaynağı taşırken göz önünde bulundurmanız gereken özel koşullar hakkında bilgi sağlar.

Kaynak sağlayıcısı ad alanına atlayın:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadihar](#microsoftaadiam)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. Apimanane](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. Batch](#microsoftbatch)
> - [Microsoft. Batchaı](#microsoftbatchai)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. Blockzinciri](#microsoftblockchain)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. Cache](#microsoftcache)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. Biliveservices](#microsoftcognitiveservices)
> - [Microsoft. COMPUTE](#microsoftcompute)
> - [Microsoft. Container](#microsoftcontainer)
> - [Microsoft. Containerınstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. Contentmoderatör](#microsoftcontentmoderator)
> - [Microsoft. Cortanaanalizi](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. Customerınsights](#microsoftcustomerinsights)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. Databricks](#microsoftdatabricks)
> - [Microsoft. DataCatalog](#microsoftdatacatalog)
> - [Microsoft. DataConnect](#microsoftdataconnect)
> - [Microsoft. DataExchange](#microsoftdataexchange)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. DataLake](#microsoftdatalake)
> - [Microsoft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft. DataLakeStore](#microsoftdatalakestore)
> - [Microsoft. DataMigration](#microsoftdatamigration)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft. Dbformarıdb](#microsoftdbformariadb)
> - [Microsoft. Dbformyısql](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. Devices](#microsoftdevices)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. Genomiks](#microsoftgenomics)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. Healthgelişme API 'leri](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. ımportexport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. ıotcentral](#microsoftiotcentral)
> - [Microsoft. ıotspaces](#microsoftiotspaces)
> - [Microsoft. Keykasası](#microsoftkeyvault)
> - [Microsoft. kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. Machinöğrenim](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft. MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. Managedıdentity](#microsoftmanagedidentity)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrate](#microsoftmigrate)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft. Network](#microsoftnetwork)
> - [Microsoft. Notificationhub 'Lar](#microsoftnotificationhubs)
> - [Microsoft. Operationalınsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. eşleme](#microsoftpeering)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PortalSdk](#microsoftportalsdk)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. Powerbiadanmış](#microsoftpowerbidedicated)
> - [Microsoft. ProjectOxford](#microsoftprojectoxford)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Scheduler](#microsoftscheduler)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. Servicefabrickafesi](#microsoftservicefabricmesh)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. SqlVM](#microsoftsqlvm)
> - [Microsoft. Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. Storagessync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. Storagesyncınt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. Teroyformoss](#microsoftterraformoss)
> - [Microsoft. Timeseriesınsights](#microsofttimeseriesinsights)
> - [Microsoft. Token](#microsofttoken)
> - [Microsoft. Virtualmachineımages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft. Vmwarechoparlör basit](#microsoftvmwarecloudsimple)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. Windowsıot](#microsoftwindowsiot)
> - [Microsoft. WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | DomainServices | Hayır | Hayır |
> | DomainServices/replicasets | Hayır | Hayır |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Kira | Hayır | Hayır |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | actionrules | Yes | Yes |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | sunucular | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | hizmet | Yes | Yes |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | configurationmağazaların | Yes | Yes |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | apiapps | Hayır | Hayır |
> | appdentities | Hayır | Hayır |
> | geçidinin | Hayır | Hayır |

> [!IMPORTANT]
> Bkz. [App Service taşıma Kılavuzu](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | poliyasatamaları | Hayır | Hayır |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Yes | Yes |
> | automationaccounts/Configurations | Yes | Yes |
> | automationaccounts/runbook 'lar | Yes | Yes |

> [!IMPORTANT]
> Runbook 'lar Otomasyon hesabıyla aynı kaynak grubunda bulunmalıdır.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Yes | Yes |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | sqlserverkayıtları | Hayır | Hayır |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | kayıtlarında | Yes | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Yes | Yes |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | leriniz | Hayır | Hayır |
> | dosya sunucuları | Hayır | Hayır |
> | Çizelge | Hayır | Hayır |
> | Onlarla | Hayır | Hayır |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | mapapsıs | Hayır | Hayır |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | biztalk | Yes | Yes |

## <a name="microsoftblockchain"></a>Microsoft. Blockzinciri

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Yes | Yes |
> | izleyici | Hayır | Hayır |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | şema tasmi | Hayır | Hayır |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | botservices | Yes | Yes |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Redis | Yes | Yes |

> [!IMPORTANT]
> Redsıs örneği için Azure önbelleği bir sanal ağla yapılandırıldıysa, örnek farklı bir aboneliğe taşınamaz. Bkz. [ağ taşıma sınırlamaları](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Hayır | Hayır |
> | profiles | Yes | Yes |
> | Profiller/uç noktalar | Yes | Yes |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | sertifikadüzenleri | Yes | Yes |

> [!IMPORTANT]
> Bkz. [App Service taşıma Kılavuzu](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | DomainNames | Yes | Hayır |
> | virtualmachines | Yes | Hayır |

> [!IMPORTANT]
> Bkz. [klasik dağıtım taşıma Kılavuzu](./move-limitations/classic-model-move-limitations.md). Klasik dağıtım kaynakları, bu senaryoya özel bir işlemle abonelikler arasında taşınabilir.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Hayır | Hayır |
> | rezervler | Hayır | Hayır |
> | virtualnetworks | Hayır | Hayır |

> [!IMPORTANT]
> Bkz. [klasik dağıtım taşıma Kılavuzu](./move-limitations/classic-model-move-limitations.md). Klasik dağıtım kaynakları, bu senaryoya özel bir işlemle abonelikler arasında taşınabilir.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Yes | Hayır |

> [!IMPORTANT]
> Bkz. [klasik dağıtım taşıma Kılavuzu](./move-limitations/classic-model-move-limitations.md). Klasik dağıtım kaynakları, bu senaryoya özel bir işlemle abonelikler arasında taşınabilir.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Yes | Yes |
> | diskencryptionsets | Hayır | Hayır |
> | disklerinden | Yes | Yes |
> | Galeriler | Hayır | Hayır |
> | Galeriler/görüntüler | Hayır | Hayır |
> | Galeriler/resimler/sürümler | Hayır | Hayır |
> | hostgroups | Hayır | Hayır |
> | hostgroups/konaklar | Hayır | Hayır |
> | images | Yes | Yes |
> | proximityplacementgroups | Hayır | Hayır |
> | restorepointcollections | Hayır | Hayır |
> | sharedvmımages | Hayır | Hayır |
> | sharedvmımages/sürümler | Hayır | Hayır |
> | anlık görüntüler | Yes | Yes |
> | virtualmachines | Yes | Yes |
> | virtualmachines/uzantıları | Yes | Yes |
> | virtualmachinescalesets | Yes | Yes |

> [!IMPORTANT]
> Bkz. [sanal makine taşıma Kılavuzu](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft. Container

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | kapsayıcı grupları | Hayır | Hayır |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | kapsayıcı grupları | Hayır | Hayır |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | kayıt | Yes | Yes |
> | kayıt defterleri/BuildTasks | Yes | Yes |
> | kayıt defterleri/çoğaltmalar | Yes | Yes |
> | kayıt defterleri/görevler | Yes | Yes |
> | kayıt defterleri/Web kancaları | Yes | Yes |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | containerservices | Hayır | Hayır |
> | managedkümeler | Hayır | Hayır |
> | openshiftmanagedclusters | Hayır | Hayır |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | uygulamalar | Yes | Yes |

## <a name="microsoftcortanaanalytics"></a>Microsoft. Cortanaanalizi

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | bağlayıcılar | Yes | Yes |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Lara | Yes | Yes |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | resourceproviders | Yes | Yes |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Çizelge | Hayır | Hayır |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Hayır | Hayır |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Onlarla | Hayır | Hayır |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | larına | Yes | Yes |
> | veri katalogları | Hayır | Hayır |

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | connectionyöneticileri | Hayır | Hayır |

## <a name="microsoftdataexchange"></a>Microsoft. DataExchange

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | paketlerle | Hayır | Hayır |
> | Planlama | Hayır | Hayır |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | veri fabrikaları | Yes | Yes |
> | larının | Yes | Yes |

## <a name="microsoftdatalake"></a>Microsoft. DataLake

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Hayır | Hayır |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | services | Hayır | Hayır |
> | Hizmetler/Projeler | Hayır | Hayır |
> | Lara | Hayır | Hayır |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |

## <a name="microsoftdbformariadb"></a>Microsoft. Dbformarıdb

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | sunucular | Yes | Yes |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | sunucular | Yes | Yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | sunucu grupları | Hayır | Hayır |
> | sunucular | Yes | Yes |
> | serversv2 | Yes | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | artifactsources | Yes | Yes |
> | piyasaya çıkarma | Yes | Yes |
> | servicetopolojileri | Yes | Yes |
> | servicetopolojileri/hizmetler | Yes | Yes |
> | servicetopolojileri/hizmetler/serviceunits | Yes | Yes |
> | adımlar | Yes | Yes |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | elaun havuzları | Hayır | Hayır |
> | elaun havuzları/ıothubkiracılar | Hayır | Hayır |
> | ıothubs | Yes | Yes |
> | provisioningservices | Yes | Yes |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | örleri | Yes | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | labcenters | Hayır | Hayır |
> | larda | Yes | Hayır |
> | Laboratuvarlar/ortamlar | Yes | Yes |
> | Labs/servicerunanlar | Yes | Yes |
> | Labs/virtualmachines | Yes | Hayır |
> | cağını | Yes | Yes |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | veritabanı hesapları | Yes | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | etki alanları | Yes | Yes |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | services | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | etki alanları | Yes | Yes |
> | konularıyla | Yes | Yes |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | leriniz | Yes | Yes |
> | öznitelikleri | Yes | Yes |

## <a name="microsoftgenomics"></a>Microsoft. Genomiks

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | hanaınstances | Hayır | Hayır |
> | sapizleyicileri | Yes | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | leriniz | Yes | Yes |

> [!IMPORTANT]
> HDInsight kümeleri, yeni bir abonelik veya kaynak grubuna taşıyabilirsiniz. Ancak, ağ kaynaklarını (örneğin, sanal ağ, NIC veya yük dengeleyici) HDInsight kümesine bağlı abonelikler arasında taşınamaz. Ayrıca, küme için bir sanal makineye ekli NIC yeni bir kaynak grubuna taşınamıyor.
>
> Bir HDInsight kümesi için yeni bir abonelik taşırken, önce diğer kaynakları (örneğin, depolama hesabı) taşıyın. Ardından, HDInsight kümesi, tek başına taşıyın.

## <a name="microsofthealthcareapis"></a>Microsoft. Healthgelişme API 'leri

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | services | Yes | Yes |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | larla | Hayır | Hayır |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | veri yöneticileri | Yes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Çizelge | Yes | Yes |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |
> | actiongroups | Yes | Yes |
> | activitylogalerts | Hayır | Hayır |
> | alertrules | Yes | Yes |
> | autoscalesettings | Yes | Yes |
> | bileşenleri | Yes | Yes |
> | guestdiagnosticsettings | Hayır | Hayır |
> | metricalerts | Hayır | Hayır |
> | notificationgroups | Hayır | Hayır |
> | notificationrules | Hayır | Hayır |
> | scheduledqueryrules | Yes | Yes |
> | Web testleri | Yes | Yes |
> | kitabı | Yes | Yes |

> [!IMPORTANT]
> Yeni aboneliğe taşınmasının [abonelik kotalarını](../azure-subscription-service-limits.md#azure-monitor-limits)aşmadığından emin olun.

## <a name="microsoftiotcentral"></a>Microsoft. ıotcentral

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | ıotapps | Yes | Yes |

## <a name="microsoftiotspaces"></a>Microsoft. ıotspaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | checknameavaılabılıty | Yes | Yes |
> | graph | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | hsmpools | Hayır | Hayır |
> | kasaları | Yes | Yes |

> [!IMPORTANT]
> Disk şifrelemesi için kullanılan anahtar kasaları aynı abonelikte veya abonelikler arasında bir kaynak grubuna taşınamaz.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | leriniz | Yes | Yes |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | labaccounts | Hayır | Hayır |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Hayır | Hayır |
> | Tümleştirme hesapları | Yes | Yes |
> | ıntegrationserviceortamortamları | Hayır | Hayır |
> | ısotedenvironments | Hayır | Hayır |
> | sürdürülen | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | commitmentplanlar | Yes | Yes |
> | Hizmetleri | Yes | Hayır |
> | Onlarla | Yes | Yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | operationalizationkümeleri | Yes | Yes |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |
> | hesaplar/çalışma alanları | Hayır | Hayır |
> | hesaplar/çalışma alanları/projeler | Hayır | Hayır |
> | teamaccounts | Hayır | Hayır |
> | teamaccounts/çalışma alanları | Hayır | Hayır |
> | teamaccounts/çalışma alanları/projeler | Hayır | Hayır |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | hostinghesapları | Hayır | Hayır |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Onlarla | Hayır | Hayır |

## <a name="microsoftmanagedidentity"></a>Microsoft. Managedıdentity

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | userassignedıdentities | Hayır | Hayır |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Hayır | Hayır |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | mediaservices | Yes | Yes |
> | mediaservices/liveevents | Yes | Yes |
> | mediaservices/streamingendpoints | Yes | Yes |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | appkümeler | Hayır | Hayır |

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Hayır | Hayır |
> | migrateprojects | Hayır | Hayır |
> | projeyle | Hayır | Hayır |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Hayır | Hayır |
> | netappaccounts/capacityhavuzları | Hayır | Hayır |
> | netappaccounts/capacityhavuzları/birimleri | Hayır | Hayır |
> | netappaccounts/capacityhavuzlar/Volumes/Mount hedefleri | Hayır | Hayır |
> | netappaccounts/capacityhavuzlar/birimler/anlık görüntüler | Hayır | Hayır |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | applicationgateway 'ler | Hayır | Hayır |
> | applicationgatewaywebapplicationfirewallpolicies | Hayır | Hayır |
> | applicationsecuritygroups | Yes | Yes |
> | azurefirewalls | Yes | Yes |
> | Savunma Konakları | Hayır | Hayır |
> | bağlantının | Yes | Yes |
> | ddoscustompolicies | Yes | Yes |
> | ddosprotectionplanlar | Hayır | Hayır |
> | dnszones | Yes | Yes |
> | expressroutedevreleri | Hayır | Hayır |
> | expressroutecrossconnections | Hayır | Hayır |
> | expressroutegateway 'ler | Hayır | Hayır |
> | expressrouteports | Hayır | Hayır |
> | frontkapıların | Hayır | Hayır |
> | frontdoorwebapplicationfirewallpolicies | Hayır | Hayır |
> | loadbalancers | Evet-temel SKU<br>Standart SKU yok | Evet-temel SKU<br>Standart SKU yok |
> | localnetworkgateway 'ler | Yes | Yes |
> | natgateway 'ler | Yes | Yes |
> | networkıntpolicies ilkeleri | Yes | Yes |
> | NetworkInterfaces | Yes | Yes |
> | networkprofiles | Hayır | Hayır |
> | networksecuritygroups | Yes | Yes |
> | networkwatchers | Yes | Yes |
> | networkwatchers/connectionmonitörleri | Yes | Yes |
> | networkwatchers/uzunluler | Yes | Yes |
> | networkwatchers/pingkafesler | Yes | Yes |
> | p2svpngateways | Hayır | Hayır |
> | privatednszones | Yes | Yes |
> | privatednszones/virtualnetworklinks | Yes | Yes |
> | privateendpoints | Hayır | Hayır |
> | privatelinkservices | Hayır | Hayır |
> | publicıpaddresses | Evet-temel SKU<br>Standart SKU yok | Evet-temel SKU<br>Standart SKU yok |
> | publicıpöneklerini | Yes | Yes |
> | routefilters | Hayır | Hayır |
> | routetables | Yes | Yes |
> | serviceendpointpolicies | Yes | Yes |
> | trafficmanagerprofiles | Yes | Yes |
> | virtualhub 'lar | Hayır | Hayır |
> | virtualnetworkgateways | Yes | Yes |
> | virtualnetworks | Yes | Yes |
> | virtualnetworktaps | Hayır | Hayır |
> | virtualwan | Hayır | Hayır |
> | vpngateway (sanal WAN) | Hayır | Hayır |
> | vpnsites (sanal WAN) | Hayır | Hayır |
> | webapplicationfirewallpolicies | Yes | Yes |

> [!IMPORTANT]
> Bkz. [ağ taşıma Kılavuzu](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | öznitelikleri | Yes | Yes |
> | ad alanları/notificationhub 'lar | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Onlarla | Yes | Yes |

> [!IMPORTANT]
> Yeni aboneliğe taşınmasının [abonelik kotalarını](../azure-subscription-service-limits.md#azure-monitor-limits)aşmadığından emin olun.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | Yes | Yes |
> | çözümler | Yes | Yes |
> | Görünümler | Yes | Yes |

## <a name="microsoftpeering"></a>Microsoft. eşleme

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | eşlemeleri | Hayır | Hayır |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | panoların | Yes | Yes |

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | rootresources | Hayır | Hayır |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | kapasiteler | Yes | Yes |

## <a name="microsoftprojectoxford"></a>Microsoft. ProjectOxford

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | kasaları | Yes | Yes |

> [!IMPORTANT]
> Bkz. [Kurtarma Hizmetleri taşıma Kılavuzu](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | öznitelikleri | Yes | Yes |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | sorgu | Yes | Yes |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | uygulamalar | Yes | Hayır |

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | var | Yes | Yes |
> | işlere | Yes | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | searchservices | Yes | Yes |

> [!IMPORTANT]
> Farklı bölgelerde çeşitli arama kaynaklarını tek bir işlemde taşıyamazsınız. Bunun yerine, bunları ayrı işlemlerde taşıyın.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | ıotsecuritysolutions | Yes | Yes |
> | playbookconfigurations | Hayır | Hayır |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | geçidinin | Hayır | Hayır |
> | düğüm | Hayır | Hayır |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | öznitelikleri | Yes | Yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | uygulamalar | Hayır | Hayır |
> | leriniz | Yes | Yes |
> | kümeler/uygulamalar | Hayır | Hayır |
> | kapsayıcı grupları | Hayır | Hayır |
> | containergroupsets | Hayır | Hayır |
> | edgeclusters | Hayır | Hayır |
> | Mamak | Hayır | Hayır |
> | secretmağazaları | Hayır | Hayır |
> | volumes | Hayır | Hayır |

## <a name="microsoftservicefabricmesh"></a>Microsoft. Servicefabrickafesi

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | uygulamalar | Yes | Yes |
> | kapsayıcı grupları | Hayır | Hayır |
> | geçidinin | Yes | Yes |
> | Mamak | Yes | Yes |
> | kaynaklanır | Yes | Yes |
> | volumes | Yes | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | SignalR | Yes | Yes |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | Hayır | Hayır |
> | yaptı | Hayır | Hayır |
> | applicationdefinitions | Hayır | Hayır |
> | uygulamalar | Hayır | Hayır |
> | jistekleri | Hayır | Hayır |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | ınstancepools | Hayır | Hayır |
> | ManagedInstances | Hayır | Hayır |
> | ManagedInstances/veritabanları | Hayır | Hayır |
> | sunucular | Yes | Yes |
> | sunucular/veritabanları | Yes | Yes |
> | sunucular/elaun havuzları | Yes | Yes |
> | virtualkümeler | Yes | Yes |

> [!IMPORTANT]
> Bir veritabanı ve sunucu aynı kaynak grubunda olmalıdır. Bir SQL server taşıdığınızda, tüm veritabanlarını da taşınır. Bu davranış, Azure SQL veritabanı ve Azure SQL veri ambarı veritabanları için geçerlidir.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Yes | Yes |
> | sqlvirtualmachines | Yes | Yes |

## <a name="microsoftsqlvm"></a>Microsoft. SqlVM

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | dwvm | Hayır | Hayır |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Yes | Yes |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | önbelleklerinde | Hayır | Hayır |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Yes | Yes |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Hayır | Hayır |

## <a name="microsoftstoragesyncint"></a>Microsoft. Storagesyncınt

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Hayır | Hayır |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | ilerinde | Hayır | Hayır |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | streammingjobs | Yes | Yes |

> [!IMPORTANT]
> Stream Analytics işleri çalışır durumda olduğunda taşınamaz.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Lý | Hayır | Hayır |
> | ortamlar/EventSources | Hayır | Hayır |
> | örnekler | Hayır | Hayır |
> | örnekler/ortamlar | Hayır | Hayır |
> | örnekler/ortamlar/EventSources | Hayır | Hayır |

## <a name="microsoftterraformoss"></a>Microsoft. Teroyformoss

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | providerkayıtları | Hayır | Hayır |
> | resources | Hayır | Hayır |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Lý | Yes | Yes |
> | ortamlar/EventSources | Yes | Yes |
> | ortamlar/referencedataset 'ler | Yes | Yes |

## <a name="microsofttoken"></a>Microsoft. Token

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | depolaya | Hayır | Hayır |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. Virtualmachineımages

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | ımagetemplates | Hayır | Hayır |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | account | Yes | Yes |
> | Hesap/uzantı | Yes | Yes |
> | hesap/proje | Yes | Yes |

> [!IMPORTANT]
> Azure DevOps aboneliğini değiştirmek için, bkz. [faturalandırma için kullanılan Azure aboneliğini değiştirme](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Vmwarechoparlör basit

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | ayrılmış cloudnodes | Hayır | Hayır |
> | ayrılmış CloudService | Hayır | Hayır |
> | virtualmachines | Hayır | Hayır |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Sertifika | Hayır | Yes |
> | connectiongateway 'ler | Yes | Yes |
> | bağlantının | Yes | Yes |
> | customapsıs | Yes | Yes |
> | hostingenvironments | Hayır | Hayır |
> | serverfarms | Yes | Yes |
> | barındıra | Yes | Yes |
> | siteler/premieraddons | Yes | Yes |
> | siteler/yuvalar | Yes | Yes |

> [!IMPORTANT]
> Bkz. [App Service taşıma Kılavuzu](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft. Windowsıot

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | deviceservices | Hayır | Hayır |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Hayır | Hayır |
> | Ana bilgisayar havuzları | Hayır | Hayır |
> | Onlarla | Hayır | Hayır |

## <a name="third-party-services"></a>Üçüncü taraf hizmetleri

Üçüncü taraf hizmetler şu anda taşıma işlemini desteklemiyor.

## <a name="next-steps"></a>Sonraki adımlar
Kaynakları taşıma komutları için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](resource-group-move-resources.md).

Aynı verileri bir virgülle ayrılmış değerler dosyası ile almak için, [Move-support-resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)dosyasını indirin.
