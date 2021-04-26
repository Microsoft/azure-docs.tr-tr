---
title: Kaynak türüne göre işlem desteğini taşıma
description: Yeni bir kaynak grubuna, aboneliğe veya bölgeye taşınabilecek Azure Kaynak türlerini listeler.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: a56a9e6f04aa800e16bbab0190ce7b41d87da590
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740097"
---
# <a name="move-operation-support-for-resources"></a>Kaynaklar için taşıma işlemi desteği

Bu makalede, bir Azure Kaynak türünün taşıma işlemini destekleyip desteklemediğini listelenmiştir. Ayrıca bir kaynağı taşırken göz önünde bulundurmanız gereken özel koşullar hakkında bilgi sağlar.

> [!IMPORTANT]
> Çoğu durumda, bir alt kaynak kendi üst kaynağından bağımsız olarak taşınamaz. Alt kaynakların biçiminde bir kaynak türü vardır `<resource-provider-namespace>/<parent-resource>/<child-resource>` . Örneğin, `Microsoft.ServiceBus/namespaces/queues` bir alt kaynağıdır `Microsoft.ServiceBus/namespaces` . Üst kaynağı taşıdığınızda, alt kaynak otomatik olarak onunla birlikte taşınır. Bu makalede bir alt kaynak görmüyorsanız, ana kaynakla birlikte taşındığını varsayabilirsiniz. Üst kaynak taşımayı desteklemiyorsa, alt kaynak taşınamaz.

Kaynak sağlayıcısı ad alanına atlayın:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadihar](#microsoftaadiam)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. Apimanane](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft. kanıtlama](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AVS](#microsoftavs)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. Azurestackhcı](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. Faturalandırma](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. Blockzinciri](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. Cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft. Biliveservices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. tüketim](#microsoftconsumption)
> - [Microsoft. Containerınstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. Contentmoderatör](#microsoftcontentmoderator)
> - [Microsoft. Cortanaanalizi](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. Customerınsights](#microsoftcustomerinsights)
> - [Microsoft. Customerkasası](#microsoftcustomerlockbox)
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
> - [Microsoft. DataProtection](#microsoftdataprotection)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft. Dbformarıdb](#microsoftdbformariadb)
> - [Microsoft. Dbformyısql](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. deneme](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. Features](#microsoftfeatures)
> - [Microsoft. Genomiks](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. Healthgelişme API 'leri](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ımportexport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. ıotcentral](#microsoftiotcentral)
> - [Microsoft. ıotspaces](#microsoftiotspaces)
> - [Microsoft. Keykasası](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. Machinöğrenim](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. Maintenance](#microsoftmaintenance)
> - [Microsoft. Managedıdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Market](#microsoftmarketplace)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Marketplacesıralaması](#microsoftmarketplaceordering)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrate](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. Notificationhub 'Lar](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. Operationalınsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. eşleme](#microsoftpeering)
> - [Microsoft. Poliyelei](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. Powerbiadanmış](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft. hisse](#microsoftquantum)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. Securityınsights](#microsoftsecurityinsights)
> - [Microsoft. SerialConsole](#microsoftserialconsole)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. Servicefabrickafesi](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. Storagessync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. Storagesyncınt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. support](#microsoftsupport)
> - [Microsoft. SYNAPSE](#microsoftsynapse)
> - [Microsoft. Timeseriesınsights](#microsofttimeseriesinsights)
> - [Microsoft. Token](#microsofttoken)
> - [Microsoft. Virtualmachineımages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMware](#microsoftvmware)
> - [Microsoft. Vmwarechoparlör basit](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. Windowsıot](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | DomainServices | Hayır | Hayır |  Hayır |

## <a name="microsoftaadiam"></a>Microsoft. aadihar

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | diagnosticsettings | Hayır | Hayır | Hayır |
> | diagnosticsettingscategories | Hayır | Hayır | Hayır |
> | privatelinkforazuread | Yes | Yes | Hayır |
> | Kira | Yes | Yes | Hayır |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | destek sağlayıcıları | Hayır | Hayır | Hayır |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | aadsupportcases | Hayır | Hayır | Hayır |
> | addsservices | Hayır | Hayır | Hayır |
> | aracısını | Hayır | Hayır | Hayır |
> | anonymousapiusers | Hayır | Hayır | Hayır |
> | yapılandırma | Hayır | Hayır | Hayır |
> | günlükler | Hayır | Hayır | Hayır |
> | reports | Hayır | Hayır | Hayır |
> | servicehealthölçümleri | Hayır | Hayır | Hayır |
> | services | Hayır | Hayır | Hayır |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | konfigürasyonları | Hayır | Hayır | Hayır |
> | generatereyorumgeçişleri | Hayır | Hayır | Hayır |
> | meta veriler | Hayır | Hayır | Hayır |
> | Öneriler | Hayır | Hayır | Hayır |
> | gizlemeleri | Hayır | Hayır | Hayır |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | actionrules | Yes | Yes | Hayır |
> | alerts | Hayır | Hayır | Hayır |
> | alertslist | Hayır | Hayır | Hayır |
> | alertsmetadata | Hayır | Hayır | Hayır |
> | alertssummary | Hayır | Hayır | Hayır |
> | alertssummarylist | Hayır | Hayır | Hayır |
> | smartdetectoralertrules | Yes | Yes | Hayır |
> | smartgroups | Hayır | Hayır | Hayır |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | larý | Yes | Yes | Hayır |

## <a name="microsoftapimanagement"></a>Microsoft. Apimanane

> [!IMPORTANT]
> Tüketim SKU 'suna ayarlanmış bir API Management hizmeti taşınamaz.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | reportfeedback | Hayır | Hayır | Hayır |
> | hizmet | Yes | Yes | Evet (şablonu kullanarak) <br/><br/> [API Management bölgeler arasında taşıyın](../../api-management/api-management-howto-migrate.md). |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationmağazaların | Yes | Yes | Hayır |
> | configurationmağazaların/eventgridfilters | Hayır | Hayır | Hayır |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | yay | Yes | Yes | Hayır |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!IMPORTANT]
> Bkz. [App Service taşıma Kılavuzu](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | apiapps | Hayır | Hayır | Evet (şablonu kullanarak)<br/><br/> [App Service uygulamasını başka bir bölgeye taşıma](../../app-service/manage-move-across-regions.md) |
> | appdentities | Hayır | Hayır | Hayır |
> | geçidinin | Hayır | Hayır | Hayır |

## <a name="microsoftattestation"></a>Microsoft. kanıtlama

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | attestationproviders | Yes | Yes | Hayır |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | classicadministrators | Hayır | Hayır | Hayır |
> | datatakma adlar | Hayır | Hayır | Hayır |
> | denyasatamaları | Hayır | Hayır | Hayır |
> | erişimi yükseltme | Hayır | Hayır | Hayır |
> | findorphanroleatamalar | Hayır | Hayır | Hayır |
> | kaynaktaki | Hayır | Hayır | Hayır |
> | izinler | Hayır | Hayır | Hayır |
> | poliyasatamaları | Hayır | Hayır | Hayır |
> | PolicyDefinitions | Hayır | Hayır | Hayır |
> | policysetdefinitions | Hayır | Hayır | Hayır |
> | privatelinkassociations | Hayır | Hayır | Hayır |
> | resourcemanagementprivatelinks | Hayır | Hayır | Hayır |
> | roleassignments | Hayır | Hayır | Hayır |
> | roleatamasussusageölçümleri | Hayır | Hayır | Hayır |
> | roledefinitions | Hayır | Hayır | Hayır |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!IMPORTANT]
> Runbook 'lar Otomasyon hesabıyla aynı kaynak grubunda bulunmalıdır.
>
> Daha fazla bilgi için bkz. [Azure Otomasyonu hesabınızı başka bir aboneliğe taşıma](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | automationaccounts | Yes | Yes | Evet (şablonu kullanarak) <br/><br/> [Coğrafi çoğaltma kullanma](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |
> | automationaccounts/Configurations | Yes | Yes | Hayır |
> | automationaccounts/runbook 'lar | Yes | Yes | Hayır |

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | privatebulutlar | Yes | Yes | Hayır |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | b2cdirectories | Yes | Yes | Hayır |
> | b2ctenants | Hayır | Hayır | Hayır |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | veri denetleyicileri | Hayır | Hayır | Hayır |
> | hybriddatayöneticileri | Hayır | Hayır | Hayır |
> | postgresınstances | Hayır | Hayır | Hayır |
> | SQLInstances | Hayır | Hayır | Hayır |
> | sqlmanagedınstances | Hayır | Hayır | Hayır |
> | sqlserverınstances | Hayır | Hayır | Hayır |
> | sqlserverkayıtları | Yes | Yes | Hayır |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | cloudmanifestfiles | Hayır | Hayır | Hayır |
> | kayıtlarında | Yes | Yes | Hayır |

## <a name="microsoftazurestackhci"></a>Microsoft. Azurestackhcı

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | leriniz | Hayır | Hayır | Hayır |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | batchaccounts | Yes | Yes | Batch hesapları doğrudan bir bölgeden diğerine taşınamaz, ancak şablonu dışarı aktarmak, değiştirmek ve şablonu yeni bölgeye dağıtmak için bir şablon kullanabilirsiniz. <br/><br/> [Bir Batch hesabını bölgeler arasında taşıma](../../batch/best-practices.md#moving-batch-accounts-across-regions) hakkında bilgi edinin |

## <a name="microsoftbilling"></a>Microsoft. Faturalandırma

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | billingaccounts | Hayır | Hayır | Hayır |
> | billingdönemler | Hayır | Hayır | Hayır |
> | billingpermissions | Hayır | Hayır | Hayır |
> | billingproperty | Hayır | Hayır | Hayır |
> | billingroleatamaları | Hayır | Hayır | Hayır |
> | billingroledefinitions | Hayır | Hayır | Hayır |
> | bölümlerinin | Hayır | Hayır | Hayır |
> | kayıt sayısı | Hayır | Hayır | Hayır |
> | faturalardan | Hayır | Hayır | Hayır |
> | girişinde | Hayır | Hayır | Hayır |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | mapapsıs | Hayır | Hayır | Hayır |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | biztalk | Hayır | Hayır | Hayır |

## <a name="microsoftblockchain"></a>Microsoft. Blockzinciri

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | blockchainmembers | Hayır | Hayır | Hayır <br/><br/> Blok zinciri ağı farklı bölgelerde düğümlere sahip olamaz. |
> | cordadmembers | Hayır | Hayır | Hayır |
> | izleyicileri | Hayır | Hayır | Hayır |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | tokenservices | Hayır | Hayır | Hayır |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | şema tasmi | Hayır | Hayır | Hayır |
> | Blueprint | Hayır | Hayır | Hayır |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | botservices | Yes | Yes | Hayır |

## <a name="microsoftcache"></a>Microsoft. Cache

> [!IMPORTANT]
> Redsıs örneği için Azure önbelleği bir sanal ağla yapılandırıldıysa, örnek farklı bir aboneliğe taşınamaz. Bkz. [ağ taşıma sınırlamaları](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | Redis | Yes | Yes | Hayır |
> | redisenterprise | Hayır | Hayır | Hayır |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | appliedreservations | Hayır | Hayır | Hayır |
> | calculateexchange | Hayır | Hayır | Hayır |
> | calculateprice | Hayır | Hayır | Hayır |
> | calculatepurchaseprice | Hayır | Hayır | Hayır |
> | larına | Hayır | Hayır | Hayır |
> | ticari vaalrezervler | Hayır | Hayır | Hayır |
> | değişimi | Hayır | Hayır | Hayır |
> | rezervler | Hayır | Hayır | Hayır |
> | oluşturamaz | Hayır | Hayır | Hayır |
> | kaynaklar | Hayır | Hayır | Hayır |
> | validatereservationorder | Hayır | Hayır | Hayır |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | cdnwebapplicationfirewallmanagedrulesets | Hayır | Hayır | Hayır |
> | cdnwebapplicationfirewallpolicies | Yes | Yes | Hayır |
> | edgenodes | Hayır | Hayır | Hayır |
> | lerinize | Yes | Yes | Hayır |
> | Profiller/uç noktalar | Yes | Yes | Hayır |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!IMPORTANT]
> Bkz. [App Service taşıma Kılavuzu](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | sertifikadüzenleri | Yes | Yes | Hayır |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Bkz. [klasik dağıtım taşıma Kılavuzu](./move-limitations/classic-model-move-limitations.md). Klasik dağıtım kaynakları, bu senaryoya özel bir işlemle abonelikler arasında taşınabilir.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | özellikler | Hayır | Hayır | Hayır |
> | DomainNames | Yes | Hayır | Hayır |
> | quotas | Hayır | Hayır | Hayır |
> | resourcetypes | Hayır | Hayır | Hayır |
> | validatesubscriptionmoveavaılabılıty | Hayır | Hayır | Hayır |
> | virtualmachines | Yes | Yes | Hayır |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!IMPORTANT]
> Bkz. [klasik dağıtım taşıma Kılavuzu](./move-limitations/classic-model-move-limitations.md). Klasik dağıtım kaynakları, bu senaryoya özel bir işlemle abonelikler arasında taşınabilir.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | classicinfrastructureresources | Hayır | Hayır | Hayır |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!IMPORTANT]
> Bkz. [klasik dağıtım taşıma Kılavuzu](./move-limitations/classic-model-move-limitations.md). Klasik dağıtım kaynakları, bu senaryoya özel bir işlemle abonelikler arasında taşınabilir.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | özellikler | Hayır | Hayır | Hayır |
> | expressroutecrossconnections | Hayır | Hayır | Hayır |
> | expressroutecrossconnections/peerler | Hayır | Hayır | Hayır |
> | gatewaysupporteddevices | Hayır | Hayır | Hayır |
> | networksecuritygroups | Hayır | Hayır | Hayır |
> | quotas | Hayır | Hayır | Hayır |
> | rezervler | Hayır | Hayır | Hayır |
> | virtualnetworks | Hayır | Hayır | Hayır |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!IMPORTANT]
> Bkz. [klasik dağıtım taşıma Kılavuzu](./move-limitations/classic-model-move-limitations.md). Klasik dağıtım kaynakları, bu senaryoya özel bir işlemle abonelikler arasında taşınabilir.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | disklerinden | Hayır | Hayır | Hayır |
> | images | Hayır | Hayır | Hayır |
> | osimages | Hayır | Hayır | Hayır |
> | osplatformımages | Hayır | Hayır | Hayır |
> | publicımages | Hayır | Hayır | Hayır |
> | quotas | Hayır | Hayır | Hayır |
> | storageaccounts | Yes | Hayır | Yes |
> | vmımages | Hayır | Hayır | Hayır |

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!IMPORTANT]
> Bkz. [klasik dağıtım taşıma Kılavuzu](./move-limitations/classic-model-move-limitations.md). Klasik dağıtım kaynakları, bu senaryoya özel bir işlemle abonelikler arasında taşınabilir.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | işlemler | Hayır | Hayır | Hayır |

## <a name="microsoftcognitiveservices"></a>Microsoft. Biliveservices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Yes | Yes | Hayır |
> | Bilişsel Arama | **yayınlan** | **yayınlan** | El ile yapılan adımlarla desteklenir.<br/><br/> [Azure bilişsel arama hizmetinizi başka bir bölgeye taşıma](../../search/search-howto-move-across-regions.md) hakkında bilgi edinin |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | ratecard | Hayır | Hayır | Hayır |
> | usagetoplamaları | Hayır | Hayır | Hayır |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Bkz. [sanal makine taşıma Kılavuzu](./move-limitations/virtual-machines-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | availabilitysets | Yes | Yes |  Yes <br/><br/> Kullanılabilirlik kümelerini taşımak için [Azure Kaynak taşıyıcısı](../../resource-mover/tutorial-move-region-virtual-machines.md) kullanın. |
> | diskeriþler | Hayır | Hayır | Hayır |
> | diskencryptionsets | Hayır | Hayır | Hayır |
> | disklerinden | Yes | Yes | Yes <br/><br/> Azure [kaynak taşıyıcısı](../../resource-mover/tutorial-move-region-virtual-machines.md) 'Ni kullanarak Azure VM 'lerini ve ilgili diskleri taşıyın. |
> | Galeriler | Hayır | Hayır | Hayır |
> | Galeriler/görüntüler | Hayır | Hayır | Hayır |
> | Galeriler/resimler/sürümler | Hayır | Hayır | Hayır |
> | hostgroups | Hayır | Hayır | Hayır |
> | hostgroups/konaklar | Hayır | Hayır | Hayır |
> | images | Yes | Yes | Hayır |
> | proximityplacementgroups | Yes | Yes | Hayır |
> | restorepointcollections | Hayır | Hayır | Hayır |
> | restorepointcollections/restorepoints | Hayır | Hayır | Hayır |
> | sharedvmextensions | Hayır | Hayır | Hayır |
> | sharedvmımages | Hayır | Hayır | Hayır |
> | sharedvmımages/sürümler | Hayır | Hayır | Hayır |
> | anlık görüntüler | Yes | Yes | Hayır |
> | sshpublickeys | Hayır | Hayır | Hayır |
> | virtualmachines | Yes | Yes | Yes <br/><br/> Azure sanal makinelerini taşımak için [Azure Kaynak taşıyıcısı](../../resource-mover/tutorial-move-region-virtual-machines.md) kullanın. |
> | virtualmachines/uzantıları | Yes | Yes | Hayır |
> | virtualmachinescalesets | Yes | Yes | Hayır |

## <a name="microsoftconsumption"></a>Microsoft. tüketim

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregmalyt maliyeti | Hayır | Hayır | Hayır |
> | dengeler | Hayır | Hayır | Hayır |
> | bütçelerinin | Hayır | Hayır | Hayır |
> | ücretleriyle | Hayır | Hayır | Hayır |
> | costtags | Hayır | Hayır | Hayır |
> | iler | Hayır | Hayır | Hayır |
> | etkinlikler | Hayır | Hayır | Hayır |
> | hesaplansın | Hayır | Hayır | Hayır |
> | oluş | Hayır | Hayır | Hayır |
> | marketlerinden | Hayır | Hayır | Hayır |
> | pricesheets | Hayır | Hayır | Hayır |
> | ürün | Hayır | Hayır | Hayır |
> | rezervde ayrıntıları | Hayır | Hayır | Hayır |
> | reservationrecommendationdetails | Hayır | Hayır | Hayır |
> | rezervationönerilere | Hayır | Hayır | Hayır |
> | rezervlerin Özeti | Hayır | Hayır | Hayır |
> | rezervlik işlemleri | Hayır | Hayır | Hayır |
> | etiketler | Hayır | Hayır | Hayır |
> | Kira | Hayır | Hayır | Hayır |
> | larındaki | Hayır | Hayır | Hayır |
> | UsageDetails | Hayır | Hayır | Hayır |

## <a name="microsoftcontainerinstance"></a>Microsoft. Containerınstance

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | kapsayıcı grupları | Hayır | Hayır | Hayır |
> | serviceassociationlinks | Hayır | Hayır | Hayır |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | kayıt | Yes | Yes | Hayır |
> | kayıt defterleri/agentpools | Yes | Yes | Hayır |
> | kayıt defterleri/BuildTasks | Yes | Yes | Hayır |
> | kayıt defterleri/çoğaltmalar | Yes | Yes | Hayır |
> | kayıt defterleri/görevler | Yes | Yes | Hayır |
> | kayıt defterleri/Web kancaları | Yes | Yes | Hayır |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | containerservices | Hayır | Hayır | Hayır |
> | managedkümeler | Hayır | Hayır | Hayır |
> | openshiftmanagedclusters | Hayır | Hayır | Hayır |

## <a name="microsoftcontentmoderator"></a>Microsoft. Contentmoderatör

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | uygulamalar | Hayır | Hayır | Hayır |

## <a name="microsoftcortanaanalytics"></a>Microsoft. Cortanaanalizi

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Hayır | Hayır | Hayır |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | alerts | Hayır | Hayır | Hayır |
> | billingaccounts | Hayır | Hayır | Hayır |
> | bütçelerinin | Hayır | Hayır | Hayır |
> | cloudbağlayıcıları | Hayır | Hayır | Hayır |
> | bağlayıcılar | Yes | Yes | Hayır |
> | bölümlerinin | Hayır | Hayır | Hayır |
> | boyutlarına | Hayır | Hayır | Hayır |
> | kayıt sayısı | Hayır | Hayır | Hayır |
> | aktarımları | Hayır | Hayır | Hayır |
> | externalbillingaccounts | Hayır | Hayır | Hayır |
> | tahmin | Hayır | Hayır | Hayır |
> | sorgu | Hayır | Hayır | Hayır |
> | register | Hayır | Hayır | Hayır |
> | reportconfigs | Hayır | Hayır | Hayır |
> | reports | Hayır | Hayır | Hayır |
> | ayarlar | Hayır | Hayır | Hayır |
> | showbackrules | Hayır | Hayır | Hayır |
> | görünümler | Hayır | Hayır | Hayır |

## <a name="microsoftcustomerinsights"></a>Microsoft. Customerınsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | hub'lar | Hayır | Hayır | Hayır |

## <a name="microsoftcustomerlockbox"></a>Microsoft. Customerkasası

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | istekleri | Hayır | Hayır | Hayır |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | içermektedir | Hayır | Hayır | Hayır |
> | resourceproviders | Yes | Yes | Hayır |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | Çizelge | Hayır | Hayır | Hayır |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | availablesku 'lar | Hayır | Hayır | Hayır |
> | databoxedgedevices | Hayır | Hayır | Hayır |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | çalışma alanı | Hayır | Hayır | Hayır |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | larına | Yes | Yes | Hayır |
> | veri katalogları | Hayır | Hayır | Hayır |

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | connectionyöneticileri | Hayır | Hayır | Hayır |

## <a name="microsoftdataexchange"></a>Microsoft. DataExchange

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | paketlerle | Hayır | Hayır | Hayır |
> | Planlama | Hayır | Hayır | Hayır |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | veri fabrikaları | Yes | Yes | Hayır |
> | larının | Yes | Yes | Hayır |

## <a name="microsoftdatalake"></a>Microsoft. DataLake

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | datalakeaccounts | Hayır | Hayır | Hayır |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Yes | Yes | Hayır |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Yes | Yes | Hayır |

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Hayır | Hayır | Hayır |
> | Hizmetler/Projeler | Hayır | Hayır | Hayır |
> | Lara | Hayır | Hayır | Hayır |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ---------- |
> | backupkasaları | Hayır | Hayır | Hayır |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Yes | Yes | Hayır |

## <a name="microsoftdbformariadb"></a>Microsoft. Dbformarıdb

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | larý | Yes | Yes | Bir çapraz bölge okuma çoğaltmasını, var olan bir sunucuyu taşımak için kullanabilirsiniz. [Daha fazla bilgi edinin](../../postgresql/howto-move-regions-portal.md).<br/><br/> Hizmet, coğrafi olarak yedekli yedekleme depolaması ile sağlanmışsa, diğer bölgelerde geri yüklemek için coğrafi geri yükleme kullanabilirsiniz. [Daha fazla bilgi edinin](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft. Dbformyısql

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | Flexibtaservers | Hayır | Hayır | Hayır |
> | larý | Yes | Yes | Bir çapraz bölge okuma çoğaltmasını, var olan bir sunucuyu taşımak için kullanabilirsiniz. [Daha fazla bilgi edinin](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | Flexibtaservers | Hayır | Hayır | Hayır |
> | sunucu grupları | Hayır | Hayır | Hayır |
> | larý | Yes | Yes | Bir çapraz bölge okuma çoğaltmasını, var olan bir sunucuyu taşımak için kullanabilirsiniz. [Daha fazla bilgi edinin](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Yes | Yes | Hayır |
> | teksunucular | Yes | Yes | Hayır |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | artifactsources | Yes | Yes | Hayır |
> | piyasaya çıkarma | Yes | Yes | Hayır |
> | servicetopolojileri | Yes | Yes | Hayır |
> | servicetopolojileri/hizmetler | Yes | Yes | Hayır |
> | servicetopolojileri/hizmetler/serviceunits | Yes | Yes | Hayır |
> | adımlar | Yes | Yes | Hayır |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgroups | Yes | Yes | Hayır |
> | Ana bilgisayar havuzları | Yes | Yes | Hayır |
> | çalışma alanı | Yes | Yes | Hayır |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | elaun havuzları | Hayır | Hayır | Hayır. Kaynak gösterilmez. |
> | elaun havuzları/ıothubkiracılar | Hayır | Hayır | Hayır. Kaynak gösterilmez. |
> | ıothubs | Yes | Evet | Evet. [Daha fazla bilgi edinin](../../iot-hub/iot-hub-how-to-clone.md) |
> | provisioningservices | Yes | Yes | Hayır |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | düzenler | Yes | Yes | Hayır |
> | denetleyiciler | **yayınlan** | **yayınlan** | No |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | denetleyiciler | Yes | Yes | Hayır |
> | AKS kümesi | **yayınlan** | **yayınlan** | No<br/><br/> Başka bir bölgeye geçme hakkında [daha fazla bilgi edinin](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) .

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | labcenters | Hayır | Hayır | Hayır |
> | larda | Yes | Hayır | Hayır |
> | Laboratuvarlar/ortamlar | Yes | Yes | Hayır |
> | Labs/servicerunanlar | Yes | Yes | Hayır |
> | Labs/virtualmachines | Yes | Hayır | Hayır |
> | cağını | Yes | Yes | Hayır |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | digitaltwınsınstances | Hayır | Hayır | Evet, yeni bölgedeki kaynakları yeniden oluşturarak. [Daha fazla bilgi edinin](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | databaseaccountnames | Hayır | Hayır | Hayır |
> | veritabanı hesapları | Yes | Yes | Hayır |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | etki alanları | Yes | Yes | Hayır |
> | generatessorequest | Hayır | Hayır | Hayır |
> | topleveldomains | Hayır | Hayır | Hayır |
> | validatedomainregistrationınformation | Hayır | Hayır | Hayır |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Yes | Yes | Hayır |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | etki alanları | Yes | Yes | Hayır |
> | eventabonelikleri | Hayır-bağımsız olarak taşınamaz, ancak otomatik olarak abone olunan kaynakla taşınabilir. | Hayır-bağımsız olarak taşınamaz, ancak otomatik olarak abone olunan kaynakla taşınabilir. | No |
> | extensionkonuları | Hayır | Hayır | Hayır |
> | partnernamespaces | Yes | Yes | Hayır |
> | iş ortağı kayıtları | Hayır | Hayır | Hayır |
> | iş ortağı konuları | Yes | Yes | Hayır |
> | Sistem konuları | Yes | Yes | Hayır |
> | konuları | Yes | Yes | Hayır |
> | topictypes | Hayır | Hayır | Hayır |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | leriniz | Yes | Yes | Hayır |
> | öznitelikleri | Yes | Yes | Evet (şablon ile)<br/><br/> [Bir olay hub 'ı ad alanını başka bir bölgeye taşıma](../../event-hubs/move-across-regions.md) |
> | isteyin | Hayır | Hayır | Hayır |

## <a name="microsoftexperimentation"></a>Microsoft. deneme

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | experimentworkspaces | Hayır | Hayır | Hayır |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | öznitelikleri | Yes | Yes | Hayır |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | özellik sağlayıcıları | Hayır | Hayır | Hayır |
> | özellikler | Hayır | Hayır | Hayır |
> | sağlayıcılarla | Hayır | Hayır | Hayır |
> | subscriptionfeatureregistrations | Hayır | Hayır | Hayır |

## <a name="microsoftgenomics"></a>Microsoft. Genomiks

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Hayır | Hayır | Hayır |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | Oto managedaccounts | Hayır | Hayır | Hayır |
> | Oto managedvmconfigurationprofiles | Hayır | Hayır | Hayır |
> | guestconfigurationatamaları | Hayır | Hayır | Hayır |
> | yazılımıdır | Hayır | Hayır | Hayır |
> | softwareupdateprofile | Hayır | Hayır | Hayır |
> | softwareupdates | Hayır | Hayır | Hayır |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | hanaınstances | Hayır | Hayır | Hayır |
> | sapizleyicileri | Hayır | Hayır | Hayır |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | ayrılmış atedhsms | Hayır | Hayır | Hayır |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!IMPORTANT]
> HDInsight kümelerini yeni bir aboneliğe veya kaynak grubuna taşıyabilirsiniz. Bununla birlikte, HDInsight kümesiyle bağlantılı ağ kaynakları (sanal ağ, NIC veya yük dengeleyici gibi) için abonelikler arasında geçiş yapamazsınız. Ayrıca, küme için bir sanal makineye bağlı bir NIC 'ye yeni bir kaynak grubuna taşıyamazsınız.
>
> HDInsight kümesini yeni bir aboneliğe taşırken, önce diğer kaynakları (depolama hesabı gibi) taşıyın. Ardından, HDInsight kümesini kendi kendine taşıyın.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | leriniz | Yes | Yes | Hayır |

## <a name="microsofthealthcareapis"></a>Microsoft. Healthgelişme API 'leri

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Yes | Yes | Hayır |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | larla | Yes | Yes | Hayır |
> | makineler/uzantılar | Yes | Yes | Hayır |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | veri yöneticileri | Yes | Yes | Hayır |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | cihazlar | Hayır | Hayır | Hayır |
> | vnfs | Hayır | Hayır | Hayır |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | bileşenleri | Hayır | Hayır | Hayır |
> | networkscopes | Hayır | Hayır | Hayır |

## <a name="microsoftimportexport"></a>Microsoft. ımportexport

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | Çizelge | Yes | Yes | Hayır |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!IMPORTANT]
> Yeni aboneliğe taşınmasının [abonelik kotalarını](azure-subscription-service-limits.md#azure-monitor-limits)aşmadığından emin olun.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | **yayınlan** | **yayınlan** | Hayır. [Daha fazla bilgi edinin](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region). |
> | actiongroups | Yes | Yes | Hayır |
> | activitylogalerts | Hayır | Hayır | Hayır |
> | alertrules | Yes | Yes | Hayır |
> | autoscalesettings | Yes | Yes | Hayır |
> | temel | Hayır | Hayır | Hayır |
> | bileşenleri | Yes | Yes | Hayır |
> | datacollectionrules | Hayır | Hayır | Hayır |
> | diagnosticsettings | Hayır | Hayır | Hayır |
> | diagnosticsettingscategories | Hayır | Hayır | Hayır |
> | eventcategories | Hayır | Hayır | Hayır |
> | eventTypes | Hayır | Hayır | Hayır |
> | extendeddiagnosticsettings | Hayır | Hayır | Hayır |
> | guestdiagnosticsettings | Hayır | Hayır | Hayır |
> | listmigrationdate | Hayır | Hayır | Hayır |
> | logdefinitions | Hayır | Hayır | Hayır |
> | günlüğe kaydetme profilleri | Hayır | Hayır | Hayır |
> | günlükler | Hayır | Hayır | Hayır |
> | metricalerts | Hayır | Hayır | Hayır |
> | metrictemeller | Hayır | Hayır | Hayır |
> | metricbatch | Hayır | Hayır | Hayır |
> | MetricDefinitions | Hayır | Hayır | Hayır |
> | metricnamespaces | Hayır | Hayır | Hayır |
> | metrics | Hayır | Hayır | Hayır |
> | migratealertrules | Hayır | Hayır | Hayır |
> | migratetonewpricingmodel | Hayır | Hayır | Hayır |
> | çalışma kitapları | Hayır | Hayır | Hayır |
> | notificationgroups | Hayır | Hayır | Hayır |
> | privatelinkscopes | Hayır | Hayır | Hayır |
> | rollbacktolegacypricingmodel | Hayır | Hayır | Hayır |
> | scheduledqueryrules | Yes | Yes | Hayır |
> | topology | Hayır | Hayır | Hayır |
> | işlemler | Hayır | Hayır | Hayır |
> | vminsightsonboardingstatuses | Hayır | Hayır | Hayır |
> | Web testleri | Yes | Yes | Hayır |
> | Web testleri/gettestresultfile | Hayır | Hayır | Hayır |
> | çalışma kitapları | Yes | Yes | Hayır |
> | workbooktemplates | Yes | Yes | Hayır |

## <a name="microsoftiotcentral"></a>Microsoft. ıotcentral

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | apptemplates | Hayır | Hayır | Hayır |
> | ıotapps | Yes | Yes | Hayır |

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | ıothub | **yayınlan** | **yayınlan** | Evet (kopya hub 'ı) <br/><br/> [IoT Hub 'ını başka bir bölgeye kopyalama](../../iot-hub/iot-hub-how-to-clone.md) |

## <a name="microsoftiotspaces"></a>Microsoft. ıotspaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | çıkarılamıyor | Yes | Yes | Hayır |

## <a name="microsoftkeyvault"></a>Microsoft. Keykasası

> [!IMPORTANT]
> Disk şifrelemesi için kullanılan anahtar kasaları aynı abonelikte veya abonelikler arasında bir kaynak grubuna taşınamaz.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | silinkaults | Hayır | Hayır | Hayır |
> | hsmpools | Hayır | Hayır | Hayır |
> | managedhsms | Hayır | Hayır | Hayır |
> | kasaları | Yes | Yes | Hayır |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | connectedkümeler | Yes | Yes | Hayır |
> | registeredsubscriptions | Hayır | Hayır | Hayır |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | sourcecontrolconfigurations | Hayır | Hayır | Hayır |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | leriniz | Yes | Yes | Hayır |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | labaccounts | Hayır | Hayır | Hayır |
> | kullanıcılar | Hayır | Hayır | Hayır |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Hayır | Hayır | Hayır |

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Hayır | Hayır | Hayır, küresel bir hizmettir. |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | hostingenvironments | Hayır | Hayır | Hayır |
> | Tümleştirme hesapları | Yes | Yes | Hayır |
> | ıntegrationserviceortamortamları | Yes | Hayır | Hayır |
> | ıntegrationserviceortamortamları/managedap | Yes | Hayır | Hayır |
> | ısotedenvironments | Hayır | Hayır | Hayır |
> | sürdürülen | Yes | Yes | Hayır |

## <a name="microsoftmachinelearning"></a>Microsoft. Machinöğrenim

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | commitmentplanlar | Hayır | Hayır | Hayır |
> | Hizmetleri | Yes | Hayır | Hayır |
> | çalışma alanı | Yes | Yes | Hayır |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | operationalizationkümeleri | Hayır | Hayır | Hayır |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Hayır | Hayır | Hayır |
> | teamaccounts | Hayır | Hayır | Hayır |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Hayır | Hayır | Hayır |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | çalışma alanı | Hayır | Hayır | Hayır |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationatamalar | Hayır | Hayır | Evet. [Daha fazla bilgi edinin](../../virtual-machines/move-region-maintenance-configuration.md) |
> | maintenanceconfigurations | Yes | Evet | Evet. [Daha fazla bilgi edinin](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | güncelleştirmeler | Hayır | Hayır | Hayır |

## <a name="microsoftmanagedidentity"></a>Microsoft. Managedıdentity

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | lerinizde | Hayır | Hayır | Hayır |
> | userassignedıdentities | Hayır | Hayır | Hayır |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | managednetworks | Hayır | Hayır | Hayır |
> | managednetworks/managednetworkgroups | Hayır | Hayır | Hayır |
> | managednetworks/managednetworkpeeringpolicies | Hayır | Hayır | Hayır |
> | bildirim | Hayır | Hayır | Hayır |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | Pazar placeryumuristrationdefinitions | Hayır | Hayır | Hayır |
> | registrationatamaları | Hayır | Hayır | Hayır |
> | registrationdefinitions | Hayır | Hayır | Hayır |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | getentities | Hayır | Hayır | Hayır |
> | managementgroups | Hayır | Hayır | Hayır |
> | managementgroups/Settings | Hayır | Hayır | Hayır |
> | kaynaklar | Hayır | Hayır | Hayır |
> | starttenantbackfill | Hayır | Hayır | Hayır |
> | tenantbackfillstatus | Hayır | Hayır | Hayır |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Yes | Yes | Hayır, Azure Maps bir jeo-uzamsal hizmettir. |
> | hesaplar/privateatlases | Yes | Yes | Hayır |

## <a name="microsoftmarketplace"></a>Microsoft. Market

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | sunar | Hayır | Hayır | Hayır |
> | offertypes | Hayır | Hayır | Hayır |
> | privategallergıtems | Hayır | Hayır | Hayır |
> | privatestoreclient | Hayır | Hayır | Hayır |
> | privatemağazaların | Hayır | Hayır | Hayır |
> | ürün | Hayır | Hayır | Hayır |
> | Publishers | Hayır | Hayır | Hayır |
> | register | Hayır | Hayır | Hayır |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | classicdevservices | Hayır | Hayır | Hayır |

## <a name="microsoftmarketplaceordering"></a>Microsoft. Marketplacesıralaması

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | anlaşmalar | Hayır | Hayır | Hayır |
> | offertypes | Hayır | Hayır | Hayır |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | mediaservices | Yes | Yes | Hayır |
> | mediaservices/liveevents | Yes | Yes | Hayır |
> | mediaservices/streamingendpoints | Yes | Yes | Hayır |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | appkümeler | Hayır | Hayır | Hayır |

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | assessmentprojects | Hayır | Hayır | Hayır |
> | migrateprojects | Hayır | Hayır | Hayır |
> | movecollections | Hayır | Hayır | Hayır |
> | projeyle | Hayır | Hayır | Hayır |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ---------- |
> | holographicsbroadcastaccounts | Hayır | Hayır | Hayır |
> | objectunderstandingaccounts | Hayır | Hayır | Hayır |
> | remoterenderingaccounts | Yes | Yes | Hayır |
> | spatialanchorsaccounts | Yes | Yes | Hayır |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | netappaccounts | Hayır | Hayır | Hayır |
> | netappaccounts/capacityhavuzları | Hayır | Hayır | Hayır |
> | netappaccounts/capacityhavuzları/birimleri | Hayır | Hayır | Hayır |
> | netappaccounts/capacityhavuzlar/Volumes/Mount hedefleri | Hayır | Hayır | Hayır |
> | netappaccounts/capacityhavuzlar/birimler/anlık görüntüler | Hayır | Hayır | Hayır |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Bkz. [ağ taşıma Kılavuzu](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgateway 'ler | Hayır | Hayır | Hayır |
> | applicationgatewaywebapplicationfirewallpolicies | Hayır | Hayır | Hayır |
> | applicationsecuritygroups | Yes | Yes | Hayır |
> | azurefirewalls | Hayır | Hayır | Hayır |
> | Savunma Konakları | Hayır | Hayır | Hayır |
> | bgpservicecommunities | Hayır | Hayır | Hayır |
> | bağlantının | Yes | Yes | Hayır |
> | ddoscustompolicies | Yes | Yes | Hayır |
> | ddosprotectionplanlar | Hayır | Hayır | Hayır |
> | dnszones | Yes | Yes | Hayır |
> | expressroutedevreleri | Hayır | Hayır | Hayır |
> | expressroutegateway 'ler | Hayır | Hayır | Hayır |
> | expressrouteserviceproviders | Hayır | Hayır | Hayır |
> | firewallpolicies | Yes | Yes | Hayır |
> | frontkapıların | Hayır | Hayır | Hayır |
> | ipayırmalar | Yes | Yes | Hayır |
> | ipgroups | Yes | Yes | Hayır |
> | loadbalancers | Evet-temel SKU<br> Evet-standart SKU | Evet-temel SKU<br>Standart SKU yok | Yes <br/><br/> [Azure Kaynak taşıyıcısı](../../resource-mover/tutorial-move-region-virtual-machines.md) 'ni kullanarak iç ve dış yük dengeleyicileri taşıyın. |
> | localnetworkgateway 'ler | Yes | Yes | Hayır |
> | natgateway 'ler | Hayır | Hayır | Hayır |
> | networkexperimentprofiles | Hayır | Hayır | Hayır |
> | networkıntpolicies ilkeleri | Yes | Yes | Hayır |
> | NetworkInterfaces | Yes | Yes | Yes <br/><br/> NIC 'Leri taşımak için [Azure Kaynak taşıyıcısı](../../resource-mover/tutorial-move-region-virtual-machines.md) kullanın. |
> | networkprofiles | Hayır | Hayır | Hayır |
> | networksecuritygroups | Yes | Yes | Yes <br/><br/> Ağ güvenlik gruplarını (NGSs) taşımak için [Azure Kaynak taşıyıcısı](../../resource-mover/tutorial-move-region-virtual-machines.md) kullanın. |
> | networkwatchers | Hayır | Hayır | Hayır |
> | networkwatchers/connectionmonitörleri | Yes | Hayır | Hayır |
> | networkwatchers/flowlogs | Yes | Hayır | Hayır |
> | networkwatchers/pingkafesler | Yes | Hayır | Hayır |
> | p2svpngateways | Hayır | Hayır | Hayır |
> | privatednszones | Yes | Yes | Hayır |
> | privatednszones/virtualnetworklinks | Yes | Yes | Hayır |
> | privatednszonesinternal | Hayır | Hayır | Hayır |
> | privateendpointredirectmaps | Hayır | Hayır | Hayır |
> | privateendpoints | Hayır | Hayır | Hayır |
> | privatelinkservices | Hayır | Hayır | Hayır |
> | publicıpaddresses | Evet-temel SKU<br>Evet-standart SKU | Evet-temel SKU<br>Standart SKU yok | Yes<br/><br/> Genel IP adreslerini taşımak için [Azure Kaynak taşıyıcısı](../../resource-mover/tutorial-move-region-virtual-machines.md) kullanın. |
> | publicıpöneklerini | Yes | Yes | Hayır |
> | routefilters | Hayır | Hayır | Hayır |
> | routetables | Yes | Yes | Hayır |
> | securitypartnerproviders | Yes | Yes | Hayır |
> | serviceendpointpolicies | Yes | Yes | Hayır |
> | trafficmanagergeographichierarchies | Hayır | Hayır | Hayır |
> | trafficmanagerprofiles | Yes | Yes | Hayır |
> | trafficmanagerprofiles/ısı haritalarını | Hayır | Hayır | Hayır |
> | trafficmanagerusermetricskeys | Hayır | Hayır | Hayır |
> | virtualhub 'lar | Hayır | Hayır | Hayır |
> | virtualnetworkgateways | Yes | Yes | Hayır |
> | virtualnetworks | Yes | Yes | Hayır |
> | virtualnetworktaps | Hayır | Hayır | Hayır |
> | virtualyönlendiriciler | Yes | Yes | Hayır |
> | virtualwan | Hayır | Hayır |
> | vpngateway (sanal WAN) | Hayır | Hayır | Hayır |
> | vpnserverconfigurations | Hayır | Hayır | Hayır |
> | vpnsites (sanal WAN) | Hayır | Hayır | Hayır |

## <a name="microsoftnotificationhubs"></a>Microsoft. Notificationhub 'Lar

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | öznitelikleri | Yes | Yes | Hayır |
> | ad alanları/notificationhub 'lar | Yes | Yes | Hayır |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | osnamespaces | Yes | Yes | Hayır |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | Hiper sanal siteler | Hayır | Hayır | Hayır |
> | ımportsites | Hayır | Hayır | Hayır |
> | sunucusiteleri | Hayır | Hayır | Hayır |
> | vmwaresites | Hayır | Hayır | Hayır |

## <a name="microsoftoperationalinsights"></a>Microsoft. Operationalınsights

> [!IMPORTANT]
> Yeni bir aboneliğe geçiş için [abonelik kotalarını](azure-subscription-service-limits.md#azure-monitor-limits)aşmayacağından emin olun.
>
> Bağlı bir Otomasyon hesabına sahip olan çalışma alanları taşınamaz. Bir taşıma işlemine başlamadan önce, tüm otomasyon hesaplarının bağlantısını kaldırmayı unutmayın.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | leriniz | Hayır | Hayır | Hayır |
> | deletedworkspaces | Hayır | Hayır | Hayır |
> | bağlantı hedefleri | Hayır | Hayır | Hayır |
> | storageınsii configs | Hayır | Hayır | Hayır |
> | çalışma alanı | Yes | Yes | Hayır |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | managementassociations | Hayır | Hayır | Hayır |
> | managementconfigurations | Yes | Yes | Hayır |
> | çözümler | Yes | Yes | Hayır |
> | görünümler | Yes | Yes | Hayır |

## <a name="microsoftpeering"></a>Microsoft. eşleme

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | Yasallıklar | Hayır | Hayır | Hayır |
> | peerasns | Hayır | Hayır | Hayır |
> | peeringlocations | Hayır | Hayır | Hayır |
> | eşlemeleri | Hayır | Hayır | Hayır |
> | peeringserviceülkeleriyle | Hayır | Hayır | Hayır |
> | peeringserviceloing | Hayır | Hayır | Hayır |
> | peeringserviceproviders | Hayır | Hayır | Hayır |
> | peeringservices | Hayır | Hayır | Hayır |

## <a name="microsoftpolicyinsights"></a>Microsoft. Poliyelei

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | poliyevents | Hayır | Hayır | Hayır |
> | policystates | Hayır | Hayır | Hayır |
> | policytrackedresources | Hayır | Hayır | Hayır |
> | düzeltmeler | Hayır | Hayır | Hayır |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> |  konsolları | Hayır | Hayır | Hayır |
> | panolar | Yes | Yes | Hayır |
> | UserSettings | Hayır | Hayır | Hayır |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | workspacecollections | Yes | Yes | Hayır |

## <a name="microsoftpowerbidedicated"></a>Microsoft. Powerbiadanmış

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | kapasiteler | Yes | Yes | Hayır |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ---------- |
> | accounts | Hayır | Hayır | Hayır |

## <a name="microsoftpurview"></a>Microsoft. purview

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ---------- |
> | accounts | **yayınlan** | **yayınlan** | No |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | availableaccounts | Hayır | Hayır | Hayır |
> | providerkayıtları | Hayır | Hayır | Hayır |
> | piyasaya çıkarma | Hayır | Hayır | Hayır |

## <a name="microsoftquantum"></a>Microsoft. hisse

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | çalışma alanı | Hayır | Hayır | Hayır |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!IMPORTANT]
> Bkz. [Kurtarma Hizmetleri taşıma Kılavuzu](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | replicationeligibilityresults | Hayır | Hayır | Hayır |
> | kasaları | Yes | Evet | Hayır.<br/><br/> Azure bölgeleri arasında Azure Backup için kurtarma hizmetleri kasalarının taşınması desteklenmez.<br/><br/> Azure Site Recovery için kurtarma hizmetleri kasalarında, kasayı hedef bölgede [devre dışı bırakıp yeniden](../../site-recovery/move-vaults-across-regions.md) oluşturabilirsiniz. |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | openshiftclusters | Hayır | Hayır | Hayır |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | öznitelikleri | Yes | Yes | Hayır |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | lardır | Yes | Yes | Hayır |
> | resourcechangedetails | Hayır | Hayır | Hayır |
> | resourcechanges | Hayır | Hayır | Hayır |
> | kaynaklar | Hayır | Hayır | Hayır |
> | resourceshistory | Hayır | Hayır | Hayır |
> | subscriptionsstatus | Hayır | Hayır | Hayır |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | childresources | Hayır | Hayır | Hayır |
> | acil sorun sorunları | Hayır | Hayır | Hayır |
> | etkinlikler | Hayır | Hayır | Hayır |
> | meta veriler | Hayır | Hayır | Hayır |
> | bildirimler | Hayır | Hayır | Hayır |

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | dağıtımlar | Hayır | Hayır | Hayır |
> | deploymentscripts | Hayır | Hayır | Yes<br/><br/>[Microsoft. resources kaynaklarını yeni bölgeye taşıma](microsoft-resources-move-regions.md) |
> | deploymentscripts/Günlükler | Hayır | Hayır | Hayır |
> | Köprü | Hayır | Hayır | Hayır |
> | sağlayıcılarla | Hayır | Hayır | Hayır |
> | ResourceGroups | Hayır | Hayır | Hayır |
> | kaynaklar | Hayır | Hayır | Hayır |
> | Aboneliklerin | Hayır | Hayır | Hayır |
> | etiketler | Hayır | Hayır | Hayır |
> | templatespec | Hayır | Hayır | Yes<br/><br/>[Microsoft. resources kaynaklarını yeni bölgeye taşıma](microsoft-resources-move-regions.md) |
> | templatespec/sürümler | Hayır | Hayır | Hayır |
> | Kira | Hayır | Hayır | Hayır |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | uygulamalar | Yes | Hayır | Hayır |
> | saasresources | Hayır | Hayır | Hayır |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!IMPORTANT]
> Farklı bölgelerde çeşitli arama kaynaklarını tek bir işlemde taşıyamazsınız. Bunun yerine, bunları ayrı işlemlerde taşıyın.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | resourcehealthmetadata | Hayır | Hayır | Hayır |
> | searchservices | Yes | Yes | Hayır |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | adaptivenetworkhardenings | Hayır | Hayır | Hayır |
> | advancedthreatprotectionsettings | Hayır | Hayır | Hayır |
> | alerts | Hayır | Hayır | Hayır |
> | allowedconnections | Hayır | Hayır | Hayır |
> | applicationwhitedökümler | Hayır | Hayır | Hayır |
> | assessmentmetadata | Hayır | Hayır | Hayır |
> | değerlendirme | Hayır | Hayır | Hayır |
> | Oto dissalertsrules | Hayır | Hayır | Hayır |
> | akışlarını otomatikleştirin | Yes | Yes | Hayır |
> | Oto provisioningsettings | Hayır | Hayır | Hayır |
> | karmaşık anceresults | Hayır | Hayır | Hayır |
> | uyumluluklarına | Hayır | Hayır | Hayır |
> | datacollectionagents | Hayır | Hayır | Hayır |
> | devicesecuritygroups | Hayır | Hayır | Hayır |
> | discoveredsecuritysolutions | Hayır | Hayır | Hayır |
> | externalsecuritysolutions | Hayır | Hayır | Hayır |
> | ınformationprotectionpolicies | Hayır | Hayır | Hayır |
> | ıotsecuritysolutions | Yes | Yes | Hayır |
> | ıotsecuritysolutions/analiz ticsmodeller | Hayır | Hayır | Hayır |
> | ıotsecuritysolutions/analticsmodeller/aggreggıt uyarıları | Hayır | Hayır | Hayır |
> | ıotsecuritysolutions/analiz ticsmodeller/Aggreg, öneriler | Hayır | Hayır | Hayır |
> | jağaccesspolicies | Hayır | Hayır | Hayır |
> | ilkeler | Hayır | Hayır | Hayır |
> | fiyatlandırmalar | Hayır | Hayır | Hayır |
> | Reve daha karmaşık bakım standartları | Hayır | Hayır | Hayır |
> | rekontrol ve Re, | Hayır | Hayır | Hayır |
> | rekontrol ve Re, Re, Re, | Hayır | Hayır | Hayır |
> | securitycontacts | Hayır | Hayır | Hayır |
> | securitysolutions | Hayır | Hayır | Hayır |
> | securitysolutionsreferencedata | Hayır | Hayır | Hayır |
> | securitydurumlardan | Hayır | Hayır | Hayır |
> | securitystatusessummaries | Hayır | Hayır | Hayır |
> | sunucukullanılabilirliği | Hayır | Hayır | Hayır |
> | ayarlar | Hayır | Hayır | Hayır |
> | subassessments | Hayır | Hayır | Hayır |
> | Görevler | Hayır | Hayır | Hayır |
> | anlatır | Hayır | Hayır | Hayır |
> | çalışma alanı ayarları | Hayır | Hayır | Hayır |

## <a name="microsoftsecurityinsights"></a>Microsoft. Securityınsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | toplamaları | Hayır | Hayır | Hayır |
> | alertrules | Hayır | Hayır | Hayır |
> | alertrutatemplates | Hayır | Hayır | Hayır |
> | automationrules | Hayır | Hayır | Hayır |
> | yer işaretleri | Hayır | Hayır | Hayır |
> | çalışmaların | Hayır | Hayır | Hayır |
> | veri bağlayıcıları | Hayır | Hayır | Hayır |
> | varlıklar | Hayır | Hayır | Hayır |
> | entityqueries | Hayır | Hayır | Hayır |
> | olaylar | Hayır | Hayır | Hayır |
> | officeconsents | Hayır | Hayır | Hayır |
> | ayarlar | Hayır | Hayır | Hayır |
> | threatıntelligence | Hayır | Hayır | Hayır |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | consoleservices | Hayır | Hayır | Hayır |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | geçidinin | Hayır | Hayır | Hayır |
> | düğümlerini | Hayır | Hayır | Hayır |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | öznitelikleri | Yes | Yes | Hayır |
> | premiummessagingregions | Hayır | Hayır | Hayır |
> | isteyin | Hayır | Hayır | Hayır |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | uygulamalar | Hayır | Hayır | Hayır |
> | leriniz | Yes | Yes | Hayır |
> | kapsayıcı grupları | Hayır | Hayır | Hayır |
> | containergroupsets | Hayır | Hayır | Hayır |
> | edgeclusters | Hayır | Hayır | Hayır |
> | managedkümeler | Hayır | Hayır | Hayır |
> | Mamak | Hayır | Hayır | Hayır |
> | secretmağazaları | Hayır | Hayır | Hayır |
> | volumes | Hayır | Hayır | Hayır |

## <a name="microsoftservicefabricmesh"></a>Microsoft. Servicefabrickafesi

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | uygulamalar | Yes | Yes | Hayır |
> | kapsayıcı grupları | Hayır | Hayır | Hayır |
> | geçidinin | Yes | Yes | Hayır |
> | Mamak | Yes | Yes | Hayır |
> | kaynaklanır | Yes | Yes | Hayır |
> | volumes | Yes | Yes | Hayır |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | piyasaya çıkarma | Hayır | Hayır | Hayır |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | SignalR | Yes | Yes | Hayır |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | hybriduseavantajlar | Hayır | Hayır | Hayır |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationdefinitions | Hayır | Hayır | Hayır |
> | uygulamalar | Hayır | Hayır | Hayır |
> | jistekleri | Hayır | Hayır | Hayır |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Bir veritabanı ve sunucu aynı kaynak grubunda olmalıdır. Bir SQL sunucusunu taşıdığınızda, tüm veritabanları da taşınır. Bu davranış, Azure SQL veritabanı ve Azure SYNAPSE Analytics veritabanları için geçerlidir.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | ınstancepools | Hayır | Hayır | Hayır |
> | yerlerini | Yes | Yes | Hayır |
> | ManagedInstances | Hayır | Hayır | Yes <br/><br/> Yönetilen örnekleri bölgeler arasında taşıma hakkında [daha fazla bilgi edinin](../../azure-sql/database/move-resources-across-regions.md) . |
> | ManagedInstances/veritabanları | Hayır | Hayır | Yes |
> | larý | Yes | Yes |Yes |
> | sunucular/veritabanları | Yes | Yes | Yes <br/><br/> Veritabanlarını bölgeler arasında taşıma hakkında [daha fazla bilgi edinin](../../azure-sql/database/move-resources-across-regions.md) .<br/><br/> Azure SQL veritabanlarını taşımak için Azure Kaynak taşıyıcısı kullanma hakkında [daha fazla bilgi edinin](../../resource-mover/tutorial-move-region-sql.md) .  |
> | sunucular/veritabanları/backuplongtermretentionpolicies | Yes | Yes | Hayır |
> | sunucular/elaun havuzları | Yes | Yes | Yes <br/><br/> Elastik havuzları bölgeler arasında taşıma hakkında [daha fazla bilgi edinin](../../azure-sql/database/move-resources-across-regions.md) .<br/><br/> Azure SQL elastik havuzlarını taşımak için Azure Kaynak taşıyıcısı kullanma hakkında [daha fazla bilgi edinin](../../resource-mover/tutorial-move-region-sql.md) .  |
> | sunucular/jobaccounts | Yes | Yes | Hayır |
> | sunucular/jobagents | Yes | Yes | Hayır |
> | virtualkümeler | Yes | Yes | Yes |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | sqlvirtualmachinegroups | Yes | Yes | Hayır |
> | sqlvirtualmachines | Yes | Yes | Hayır |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | storageaccounts | Yes | Yes | Yes<br/><br/> [Bir Azure Depolama hesabını başka bir bölgeye taşıma](../../storage/common/storage-account-move.md) |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | önbelleklerinde | Hayır | Hayır | Hayır |

## <a name="microsoftstoragesync"></a>Microsoft. Storagessync

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Yes | Yes | Hayır |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Hayır | Hayır | Hayır |

## <a name="microsoftstoragesyncint"></a>Microsoft. Storagesyncınt

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Hayır | Hayır | Hayır |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | ilerinde | Hayır | Hayır | Hayır |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!IMPORTANT]
> Stream Analytics işleri çalışır durumda olduğunda taşınamaz.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | leriniz | Hayır | Hayır | Hayır |
> | streammingjobs | Yes | Yes | Hayır |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | lý | Hayır | Hayır | Hayır |
> | larında | Hayır | Hayır | Hayır |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | Aboneliklerin | Hayır | Hayır | Hayır |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Hayır | Hayır | Hayır |
> | destek biletleri | Hayır | Hayır | Hayır |

## <a name="microsoftsynapse"></a>Microsoft. SYNAPSE

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | çalışma alanı | Hayır | Hayır | Hayır |
> | çalışma alanları/bigdatapools | Hayır | Hayır | Hayır |
> | çalışma alanları/sqlpools | Hayır | Hayır | Hayır |

## <a name="microsofttimeseriesinsights"></a>Microsoft. Timeseriesınsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | lý | Yes | Yes | Hayır |
> | ortamlar/EventSources | Yes | Yes | Hayır |
> | ortamlar/referencedataset 'ler | Yes | Yes | Hayır |

## <a name="microsofttoken"></a>Microsoft. Token

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | depolaya | Yes | Yes | Hayır |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. Virtualmachineımages

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | ımagetemplates | Hayır | Hayır | Hayır |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!IMPORTANT]
> Azure DevOps aboneliğini değiştirmek için, bkz. [faturalandırma için kullanılan Azure aboneliğini değiştirme](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | account | Hayır | Hayır | Hayır |
> | Hesap/uzantı | Hayır | Hayır | Hayır |
> | hesap/proje | Hayır | Hayır | Hayır |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | arczones | Hayır | Hayır | Hayır |
> | ResourcePools | Hayır | Hayır | Hayır |
> | vCenter | Hayır | Hayır | Hayır |
> | virtualmachines | Hayır | Hayır | Hayır |
> | virtualmachinetemplates | Hayır | Hayır | Hayır |
> | virtualnetworks | Hayır | Hayır | Hayır |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Vmwarechoparlör basit

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | ayrılmış cloudnodes | Hayır | Hayır | Hayır |
> | ayrılmış CloudService | Hayır | Hayır | Hayır |
> | virtualmachines | Hayır | Hayır | Hayır |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | cihazlar | Hayır | Hayır | Hayır |
> | vnfs | Hayır | Hayır | Hayır |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Hayır | Hayır | Hayır |
> | Planlama | Hayır | Hayır | Hayır |
> | registeredsubscriptions | Hayır | Hayır | Hayır |

## <a name="microsoftweb"></a>Microsoft. Web

> [!IMPORTANT]
> Bkz. [App Service taşıma Kılavuzu](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | availableyığınları | Hayır | Hayır | Hayır |
> | billingmeters | Hayır | Hayır | Hayır |
> | sertifikalar | Hayır | Yes | Hayır |
> | connectiongateway 'ler | Yes | Yes | Hayır |
> | bağlantının | Yes | Yes | Hayır |
> | customapsıs | Yes | Yes | Hayır |
> | silinmi siteleri | Hayır | Hayır | Hayır |
> | deploymentlocations | Hayır | Hayır | Hayır |
> | coğrafi bölgeler | Hayır | Hayır | Hayır |
> | hostingenvironments | Hayır | Hayır | Hayır |
> | kubeortamları | Yes | Yes | Hayır |
> | publishingusers | Hayır | Hayır | Hayır |
> | Öneriler | Hayır | Hayır | Hayır |
> | resourcehealthmetadata | Hayır | Hayır | Hayır |
> | zamanları | Hayır | Hayır | Hayır |
> | serverfarms | Yes | Yes | Hayır |
> | sunucugrupları/eventgridfilters | Hayır | Hayır | Hayır |
> | Siteler | Yes | Yes | Hayır |
> | siteler/premieraddons | Yes | Yes | Hayır |
> | siteler/yuvalar | Yes | Yes | Hayır |
> | sourcecontrols | Hayır | Hayır | Hayır |
> | staticsites | Hayır | Hayır | Hayır |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | çoğulactivationkeys | Hayır | Hayır | Hayır |

## <a name="microsoftwindowsiot"></a>Microsoft. Windowsıot

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | deviceservices | Hayır | Hayır | Hayır |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | lerine | Hayır | Hayır | Hayır |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik | Bölge taşıma |
> | ------------- | ----------- | ---------- | ----------- |
> | bileşenleri | Hayır | Hayır | Hayır |
> | componentssummary | Hayır | Hayır | Hayır |
> | izleme örnekleri | Hayır | Hayır | Hayır |
> | izleme ınstancessummary | Hayır | Hayır | Hayır |
> | monitörün | Hayır | Hayır | Hayır |

## <a name="third-party-services"></a>Üçüncü taraf hizmetleri

Üçüncü taraf hizmetler şu anda taşıma işlemini desteklemiyor.

## <a name="next-steps"></a>Sonraki adımlar

- Kaynakları taşıma komutları için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](move-resource-group-and-subscription.md).
- Kaynak taşıyıcısı hizmeti hakkında [daha fazla bilgi edinin](../../resource-mover/overview.md) .
- Aynı verileri bir virgülle ayrılmış değerler dosyası ile almak için [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)indirin.
