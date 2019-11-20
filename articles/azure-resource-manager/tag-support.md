---
title: Kaynaklar için etiket desteği
description: Hangi Azure kaynak türlerinin etiketleri desteklediğini gösterir. Tüm Azure hizmetleri için ayrıntılar sağlar.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: aa8736310a5c18a1f94d3920a4f88632212c7af4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173544"
---
# <a name="tag-support-for-azure-resources"></a>Azure kaynakları için etiket desteği
Bu makalede, bir kaynak türünün [etiketleri](resource-group-using-tags.md)destekleyip desteklemediğini açıklanmaktadır. Etiketi **destekleyen** sütun, kaynak türünün etiket için bir özelliğe sahip olup olmadığını gösterir. **Maliyet raporundaki etiket** etiketli sütun, kaynak türünün etiketi maliyet raporuna geçirip geçirmediğini belirtir.

Aynı verileri bir virgülle ayrılmış değerler dosyası ile almak için, [tag-support. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)dosyasını indirin.

Kaynak sağlayıcısı ad alanına atlayın:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. Apimanane](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. kanıtlama](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. Azconfig](#microsoftazconfig)
> - [Microsoft. Azure. Genfiliz](#microsoftazuregeneva)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. Batch](#microsoftbatch)
> - [Microsoft. Faturalandırma](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. Blockzinciri](#microsoftblockchain)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. Cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. Biliveservices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft. COMPUTE](#microsoftcompute)
> - [Microsoft. tüketim](#microsoftconsumption)
> - [Microsoft. Containerınstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. Cortanaanalizi](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. Customerkasası](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. Databricks](#microsoftdatabricks)
> - [Microsoft. DataCatalog](#microsoftdatacatalog)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft. DataLakeStore](#microsoftdatalakestore)
> - [Microsoft. DataMigration](#microsoftdatamigration)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft. Dbformarıdb](#microsoftdbformariadb)
> - [Microsoft. Dbformyısql](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft. Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. Features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft. Genomiks](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. Healthgelişme API 'leri](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ımportexport](#microsoftimportexport)
> - [Microsoft. Intune](#microsoftintune)
> - [Microsoft. ıotcentral](#microsoftiotcentral)
> - [Microsoft. ıotspaces](#microsoftiotspaces)
> - [Microsoft. Keykasası](#microsoftkeyvault)
> - [Microsoft. kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. Machinöğrenim](#microsoftmachinelearning)
> - [Microsoft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. Managedıdentity](#microsoftmanagedidentity)
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
> - [Microsoft. Network](#microsoftnetwork)
> - [Microsoft. Notificationhub 'Lar](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. Operationalınsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. eşleme](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. Powerbiadanmış](#microsoftpowerbidedicated)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. RemoteApp](#microsoftremoteapp)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Scheduler](#microsoftscheduler)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. Securityınsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. Servicefabrickafesi](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. Sıterecovery](#microsoftsiterecovery)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. Storagerepce](#microsoftstoragereplication)
> - [Microsoft. Storagessync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. Storagesyncınt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. Timeseriesınsights](#microsofttimeseriesinsights)
> - [Microsoft. Vmwarechoparlör basit](#microsoftvmwarecloudsimple)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. Windowssavunma Deratp](#microsoftwindowsdefenderatp)
> - [Microsoft. Windowsıot](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | DomainServices | Yes | Yes |
> | DomainServices/oucontainer | Hayır | Hayır |
> | DomainServices/ReplicaSets | Yes | Yes |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Destek sağlayıcıları | Hayır | Hayır |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Hayır | Hayır |
> | addsservices | Hayır | Hayır |
> | aracısını | Hayır | Hayır |
> | anonymousapiusers | Hayır | Hayır |
> | yapılandırma | Hayır | Hayır |
> | logs | Hayır | Hayır |
> | raporlar | Hayır | Hayır |
> | servicehealthölçümleri | Hayır | Hayır |
> | services | Hayır | Hayır |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | konfigürasyonları | Hayır | Hayır |
> | Generatereyorumgeçişleri | Hayır | Hayır |
> | meta veriler | Hayır | Hayır |
> | Önerileri | Hayır | Hayır |
> | gizlemeleri | Hayır | Hayır |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | actionRules | Yes | Yes |
> | alerts | Hayır | Hayır |
> | alertsList | Hayır | Hayır |
> | alertsMetaData | Hayır | Hayır |
> | alertsSummary | Hayır | Hayır |
> | alertsSummaryList | Hayır | Hayır |
> | Lerimi | Hayır | Hayır |
> | smartDetectorAlertRules | Yes | Yes |
> | Smartdetectorruntimeortamortamları | Hayır | Hayır |
> | smartGroups | Hayır | Hayır |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | sunucular | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Hayır | Hayır |
> | hizmet | Yes | Yes |
> | validateServiceName | Hayır | Hayır |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Configurationmağazaların | Yes | Yes |
> | Configurationmağazaların/eventGridFilters | Hayır | Hayır |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Spring | Yes | Yes |

## <a name="microsoftattestation"></a>Microsoft. kanıtlama

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Hayır | Hayır |

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Hayır | Hayır |
> | Datatakma adlar | Hayır | Hayır |
> | Denyasatamaları | Hayır | Hayır |
> | Erişimi yükseltme | Hayır | Hayır |
> | Findorphanroleatamalar | Hayır | Hayır |
> | kaynaktaki | Hayır | Hayır |
> | izinler | Hayır | Hayır |
> | Poliyasatamaları | Hayır | Hayır |
> | policyDefinitions | Hayır | Hayır |
> | policySetDefinitions | Hayır | Hayır |
> | providerOperations | Hayır | Hayır |
> | roleAssignments | Hayır | Hayır |
> | roleDefinitions | Hayır | Hayır |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Yes | Yes |
> | automationAccounts/Configurations | Yes | Yes |
> | automationAccounts/Jobs | Hayır | Hayır |
> | automationAccounts/runbook 'lar | Yes | Yes |
> | automationAccounts/softwareUpdateConfigurations | Hayır | Hayır |
> | automationAccounts/Web kancaları | Hayır | Hayır |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Configurationmağazaların | Yes | Yes |
> | Configurationmağazaların/eventGridFilters | Hayır | Hayır |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Genfiliz

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Lý | Hayır | Hayır |
> | ortamlar/hesaplar | Hayır | Hayır |
> | ortamlar/hesaplar/ad alanları | Hayır | Hayır |
> | ortamlar/hesaplar/ad alanları/yapılandırma | Hayır | Hayır |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Yes | Hayır |
> | b2ctenants | Hayır | Hayır |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Hybriddatayöneticileri | Yes | Yes |
> | Postgresınstances | Yes | Yes |
> | Sqlbıgdatakümeler | Yes | Yes |
> | SQLInstances | Yes | Yes |
> | Sqlserverkayıtları | Yes | Yes |
> | Sqlserverkayıtları/sqlServers | Hayır | Hayır |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | kayıtlarında | Yes | Yes |
> | kayıt/müşteri abonelikleri | Hayır | Hayır |
> | kayıtlar/ürünler | Hayır | Hayır |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Yes | Yes |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Hayır | Hayır |
> | billingAccounts/anlaşmalar | Hayır | Hayır |
> | billingAccounts/billingPermissions | Hayır | Hayır |
> | billingAccounts/billingProfiles | Hayır | Hayır |
> | billingAccounts/Billingprofiller/billingPermissions | Hayır | Hayır |
> | billingAccounts/billingProfiles/Billingroleatamaları | Hayır | Hayır |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Hayır | Hayır |
> | billingAccounts/billingProfiles/Billingabonelikleri | Hayır | Hayır |
> | billingAccounts/billingProfiles/Createbillingroleatama | Hayır | Hayır |
> | billingAccounts/billingProfiles/müşteriler | Hayır | Hayır |
> | billingAccounts/billingProfiles/faturalar | Hayır | Hayır |
> | billingAccounts/billingProfiles/faturalar/fiyat listesi | Hayır | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections | Hayır | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/billingPermissions | Hayır | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/Billingroleatamaları | Hayır | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/billingRoleDefinitions | Hayır | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/Billingabonelikleri | Hayır | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/Createbillingroleatama | Hayır | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/ınitiatetransfer | Hayır | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/ürünler | Hayır | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/ürünler/aktarım | Hayır | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/ürünler/updateAutoRenew | Hayır | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/işlemler | Hayır | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/aktarımlar | Hayır | Hayır |
> | billingAccounts/BillingProfiles/patchOperations | Hayır | Hayır |
> | billingAccounts/billingProfiles/paymentMethods | Hayır | Hayır |
> | billingAccounts/billingProfiles/ilkeler | Hayır | Hayır |
> | billingAccounts/billingProfiles/fiyat listesi | Hayır | Hayır |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Hayır | Hayır |
> | billingAccounts/billingProfiles/ürünler | Hayır | Hayır |
> | billingAccounts/billingProfiles/işlemler | Hayır | Hayır |
> | billingAccounts/Billingroleatamaları | Hayır | Hayır |
> | billingAccounts/billingRoleDefinitions | Hayır | Hayır |
> | billingAccounts/Billingabonelikleri | Hayır | Hayır |
> | billingAccounts/Createbillingroleatama | Hayır | Hayır |
> | billingAccounts/Createınvoicesectionoperations | Hayır | Hayır |
> | billingAccounts/müşteriler | Hayır | Hayır |
> | billingAccounts/müşteriler/billingPermissions | Hayır | Hayır |
> | billingAccounts/müşteriler/Billingabonelikleri | Hayır | Hayır |
> | billingAccounts/müşteriler/ınitiatetransfer | Hayır | Hayır |
> | billingAccounts/müşteriler/ilkeler | Hayır | Hayır |
> | billingAccounts/müşteriler/ürünler | Hayır | Hayır |
> | billingAccounts/müşteriler/işlemler | Hayır | Hayır |
> | billingAccounts/müşteriler/aktarımlar | Hayır | Hayır |
> | billingAccounts/departmanlar | Hayır | Hayır |
> | billingAccounts/KayıtSayısı | Hayır | Hayır |
> | billingAccounts/faturalar | Hayır | Hayır |
> | billingAccounts/ınvoicesections | Hayır | Hayır |
> | billingAccounts/ınvoicesections/billingSubscriptionMoveOperations | Hayır | Hayır |
> | billingAccounts/ınvoicesections/Billingabonelikleri | Hayır | Hayır |
> | billingAccounts/ınvoicesections/Billingabonelikleri/aktarımı | Hayır | Hayır |
> | billingAccounts/ınvoicesections/yükselt | Hayır | Hayır |
> | billingAccounts/ınvoicesections/ınitiatetransfer | Hayır | Hayır |
> | billingAccounts/ınvoicesections/patchOperations | Hayır | Hayır |
> | billingAccounts/ınvoicesections/productMoveOperations | Hayır | Hayır |
> | billingAccounts/Ürünler/Ürünler | Hayır | Hayır |
> | billingAccounts/ınvoicesections/ürünler/transfer | Hayır | Hayır |
> | billingAccounts/ınvoicesections/ürünler/updateAutoRenew | Hayır | Hayır |
> | billingAccounts/ınvoicesections/işlemler | Hayır | Hayır |
> | billingAccounts/ınvoicesections/aktarımlar | Hayır | Hayır |
> | billingAccounts/Lineofkredisi | Hayır | Hayır |
> | billingAccounts/patchOperations | Hayır | Hayır |
> | billingAccounts/paymentMethods | Hayır | Hayır |
> | billingAccounts/ürünler | Hayır | Hayır |
> | billingAccounts/işlemler | Hayır | Hayır |
> | Billingdönemler | Hayır | Hayır |
> | billingPermissions | Hayır | Hayır |
> | billingProperty | Hayır | Hayır |
> | Billingroleatamaları | Hayır | Hayır |
> | billingRoleDefinitions | Hayır | Hayır |
> | Createbillingroleatama | Hayır | Hayır |
> | bölümlerinin | Hayır | Hayır |
> | kayıt sayısı | Hayır | Hayır |
> | faturalardan | Hayır | Hayır |
> | girişinde | Hayır | Hayır |
> | aktarımlar/acceptTransfer | Hayır | Hayır |
> | aktarımlar/declineTransfer | Hayır | Hayır |
> | aktarımlar/operationStatus | Hayır | Hayır |
> | aktarımlar/validateTransfer | Hayır | Hayır |
> | validateAddress | Hayır | Hayır |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Mapapsıs | Yes | Yes |
> | updateCommunicationPreference | Hayır | Hayır |

## <a name="microsoftblockchain"></a>Microsoft. Blockzinciri

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Yes | Yes |
> | Cordadmembers | Yes | Yes |
> | izleyici | Yes | Yes |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Şema tasmi | Hayır | Hayır |
> | Blueprintasbir/Atamaperations | Hayır | Hayır |
> | Blueprintasbir/işlemleri | Hayır | Hayır |
> | Blueprint | Hayır | Hayır |
> | planlar/yapıtlar | Hayır | Hayır |
> | planlar/sürümler | Hayır | Hayır |
> | planlar/sürümler/yapılar | Hayır | Hayır |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | botServices | Yes | Yes |
> | botServices/kanallar | Hayır | Hayır |
> | botServices/Connections | Hayır | Hayır |
> | Diller | Hayır | Hayır |
> | templates | Hayır | Hayır |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Redis | Yes | Yes |
> | RedisConfigDefinition | Hayır | Hayır |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Hayır | Hayır |
> | calculateExchange | Hayır | Hayır |
> | calculatePrice | Hayır | Hayır |
> | calculatePurchasePrice | Hayır | Hayır |
> | larına | Hayır | Hayır |
> | Ticari Vaalrezervler | Hayır | Hayır |
> | değişimi | Hayır | Hayır |
> | placePurchaseOrder | Hayır | Hayır |
> | Rezervler | Hayır | Hayır |
> | Rezervler/Hesaplaizterefund | Hayır | Hayır |
> | Rezervler/Birleştir | Hayır | Hayır |
> | Rezervler/rezervasyonlar | Hayır | Hayır |
> | Rezervler/rezervasyonlar/düzeltmeler | Hayır | Hayır |
> | Rezervler/geri dönüş | Hayır | Hayır |
> | Rezervler/Böl | Hayır | Hayır |
> | Rezervler/takas | Hayır | Hayır |
> | oluşturamaz | Hayır | Hayır |
> | resources | Hayır | Hayır |
> | validateReservationOrder | Hayır | Hayır |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Hayır | Hayır |
> | CdnWebApplicationFirewallPolicies | Yes | Yes |
> | edgenodes | Hayır | Hayır |
> | profiles | Yes | Yes |
> | Profiller/uç noktalar | Yes | Yes |
> | Profiller/uç noktalar/customdomains | Hayır | Hayır |
> | Profiller/uç noktalar/kaynaklar | Hayır | Hayır |
> | Validatearaştırması | Hayır | Hayır |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Sertifikadüzenleri | Yes | Yes |
> | certificateOrders/Certificates | Hayır | Hayır |
> | Validatecertificateregistrationınformation | Hayır | Hayır |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Yetenek | Hayır | Hayır |
> | domainNames | Hayır | Hayır |
> | domainNames/yetenekleri | Hayır | Hayır |
> | domainNames/internalLoadBalancers | Hayır | Hayır |
> | domainNames/serviceCertificates | Hayır | Hayır |
> | domainNames/Yuvaları | Hayır | Hayır |
> | domainNames/yuvalar/roller | Hayır | Hayır |
> | domainNames/yuvalar/roller/metricDefinitions | Hayır | Hayır |
> | domainNames/yuvalar/roller/ölçümler | Hayır | Hayır |
> | moveSubscriptionResources | Hayır | Hayır |
> | operatingSystemFamilies | Hayır | Hayır |
> | operatingSystems | Hayır | Hayır |
> | quotas | Hayır | Hayır |
> | resourceTypes | Hayır | Hayır |
> | Validatesubscriptionmoveavaılabılıty | Hayır | Hayır |
> | virtualMachines | Hayır | Hayır |
> | virtualMachines/diagnosticSettings | Hayır | Hayır |
> | virtualMachines/metricDefinitions | Hayır | Hayır |
> | virtualMachines/ölçümler | Hayır | Hayır |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Hayır | Hayır |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Yetenek | Hayır | Hayır |
> | expressRouteCrossConnections | Hayır | Hayır |
> | expressRouteCrossConnections/peerler | Hayır | Hayır |
> | gatewaySupportedDevices | Hayır | Hayır |
> | networkSecurityGroups | Hayır | Hayır |
> | quotas | Hayır | Hayır |
> | Rezervler | Hayır | Hayır |
> | virtualNetworks | Hayır | Hayır |
> | virtualNetworks/Remotevirtualnetworkpeeringproxy 'Leri | Hayır | Hayır |
> | virtualNetworks/Virtualnetworkpeerler | Hayır | Hayır |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Yetenek | Hayır | Hayır |
> | disklerinden | Hayır | Hayır |
> | images | Hayır | Hayır |
> | osImages | Hayır | Hayır |
> | Osplatformımages | Hayır | Hayır |
> | Publicımages | Hayır | Hayır |
> | quotas | Hayır | Hayır |
> | storageAccounts | Hayır | Hayır |
> | storageAccounts/blobServices | Hayır | Hayır |
> | storageAccounts/fileServices | Hayır | Hayır |
> | storageAccounts/metricDefinitions | Hayır | Hayır |
> | storageAccounts/ölçümler | Hayır | Hayır |
> | storageAccounts/queueServices | Hayır | Hayır |
> | storageAccounts/Services | Hayır | Hayır |
> | storageAccounts/Services/diagnosticSettings | Hayır | Hayır |
> | storageAccounts/Services/metricDefinitions | Hayır | Hayır |
> | storageAccounts/Services/ölçümler | Hayır | Hayır |
> | storageAccounts/tableServices | Hayır | Hayır |
> | storageAccounts/Vmımages | Hayır | Hayır |
> | Vmımages | Hayır | Hayır |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | RateCard | Hayır | Hayır |
> | Usagetoplamaları | Hayır | Hayır |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Yes | Yes |
> | diskEncryptionSets | Yes | Yes |
> | disklerinden | Yes | Yes |
> | Galeriler | Yes | Yes |
> | Galeriler/uygulamalar | Hayır | Hayır |
> | Galeriler/uygulamalar/sürümler | Hayır | Hayır |
> | Galeriler/görüntüler | Hayır | Hayır |
> | Galeriler/resimler/sürümler | Hayır | Hayır |
> | hostGroups | Yes | Yes |
> | hostGroups/konaklar | Yes | Yes |
> | images | Yes | Yes |
> | proximityPlacementGroups | Yes | Yes |
> | restorePointCollections | Yes | Yes |
> | restorePointCollections/restorePoints | Hayır | Hayır |
> | sharedVMExtensions | Yes | Yes |
> | sharedVMExtensions/sürümler | Hayır | Hayır |
> | Sharedvmımages | Yes | Yes |
> | Sharedvmımages/sürümler | Hayır | Hayır |
> | anlık görüntüler | Yes | Yes |
> | virtualMachines | Yes | Yes |
> | virtualMachines/uzantıları | Yes | Yes |
> | virtualMachines/metricDefinitions | Hayır | Hayır |
> | virtualMachineScaleSets | Yes | Yes |
> | virtualMachineScaleSets/uzantılar | Hayır | Hayır |
> | virtualMachineScaleSets/NetworkInterfaces | Hayır | Hayır |
> | virtualMachineScaleSets/Publicıpaddresses | Hayır | Hayır |
> | virtualMachineScaleSets/virtualMachines | Hayır | Hayır |
> | virtualMachineScaleSets/virtualMachines/NetworkInterfaces | Hayır | Hayır |

## <a name="microsoftconsumption"></a>Microsoft. tüketim

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Aggregmalyt maliyeti | Hayır | Hayır |
> | Bakiyeler | Hayır | Hayır |
> | Bütçeler | Hayır | Hayır |
> | Ücretler | Hayır | Hayır |
> | CostTags | Hayır | Hayır |
> | iler | Hayır | Hayır |
> | etkinlikler | Hayır | Hayır |
> | Tahminler | Hayır | Hayır |
> | oluş | Hayır | Hayır |
> | Marketlerinden | Hayır | Hayır |
> | Pricesheets | Hayır | Hayır |
> | ürün | Hayır | Hayır |
> | Rezervde ayrıntıları | Hayır | Hayır |
> | Rezervationönerilere | Hayır | Hayır |
> | Rezervlerin Özeti | Hayır | Hayır |
> | Rezervlik Işlemleri | Hayır | Hayır |
> | Etiketler | Hayır | Hayır |
> | Kira | Hayır | Hayır |
> | Koşullar | Hayır | Hayır |
> | UsageDetails | Hayır | Hayır |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Kapsayıcı grupları | Yes | Yes |
> | serviceAssociationLinks | Hayır | Hayır |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | kayıt | Yes | Yes |
> | kayıt defterleri/derlemeler | Hayır | Hayır |
> | kayıt defterleri/derlemeler/iptal | Hayır | Hayır |
> | kayıt defterleri/derlemeler/getLogLink | Hayır | Hayır |
> | kayıt defterleri/buildTasks | Yes | Yes |
> | kayıt defterleri/buildTasks/Steps | Hayır | Hayır |
> | kayıt defterleri/eventGridFilters | Hayır | Hayır |
> | kayıt defterleri/generateCredentials | Hayır | Hayır |
> | kayıt defterleri/getBuildSourceUploadUrl 'Si | Hayır | Hayır |
> | kayıt defterleri/GetCredentials | Hayır | Hayır |
> | kayıt defterleri/ımportımage | Hayır | Hayır |
> | kayıt defterleri/queueBuild | Hayır | Hayır |
> | kayıt defterleri/regenerateCredential | Hayır | Hayır |
> | kayıt defterleri/regenerateCredentials | Hayır | Hayır |
> | kayıt defterleri/çoğaltmalar | Yes | Yes |
> | kayıt defterleri/çalıştırmalar | Hayır | Hayır |
> | kayıt defterleri/çalıştırmalar/iptal | Hayır | Hayır |
> | kayıt defterleri/scheduleRun | Hayır | Hayır |
> | kayıt defterleri/Kapsameşlemler | Hayır | Hayır |
> | kayıt defterleri/görevler | Yes | Yes |
> | kayıt defterleri/belirteçler | Hayır | Hayır |
> | kayıt defterleri/updatePolicies | Hayır | Hayır |
> | kayıt defterleri/Web kancaları | Yes | Yes |
> | kayıt defterleri/Web kancaları/getCallbackConfig | Hayır | Hayır |
> | kayıt defterleri/Web kancaları/ping | Hayır | Hayır |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | containerServices | Yes | Yes |
> | Managedkümeler | Yes | Yes |
> | openShiftManagedClusters | Yes | Yes |

## <a name="microsoftcortanaanalytics"></a>Microsoft. Cortanaanalizi

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Uyarılar | Hayır | Hayır |
> | billingAccounts | Hayır | Hayır |
> | Bütçeler | Hayır | Hayır |
> | Cloudbağlayıcıları | Hayır | Hayır |
> | Bağlayıcılar | Yes | Yes |
> | bölümlerinin | Hayır | Hayır |
> | Boyutlar | Hayır | Hayır |
> | kayıt sayısı | Hayır | Hayır |
> | Dışarı aktarmalar | Hayır | Hayır |
> | ExternalBillingAccounts | Hayır | Hayır |
> | ExternalBillingAccounts/uyarılar | Hayır | Hayır |
> | ExternalBillingAccounts/Boyutlar | Hayır | Hayır |
> | ExternalBillingAccounts/tahmin | Hayır | Hayır |
> | ExternalBillingAccounts/sorgu | Hayır | Hayır |
> | Externalabonelikleri | Hayır | Hayır |
> | Externalabonelikleri/uyarıları | Hayır | Hayır |
> | Externalabonelikler/Boyutlar | Hayır | Hayır |
> | Externalabonelikler/tahmin | Hayır | Hayır |
> | Externalabonelikler/sorgu | Hayır | Hayır |
> | T | Hayır | Hayır |
> | Sorgu | Hayır | Hayır |
> | kaydolunamadı | Hayır | Hayır |
> | Reportconfigs | Hayır | Hayır |
> | Raporlar | Hayır | Hayır |
> | Ayarlar | Hayır | Hayır |
> | showbackRules | Hayır | Hayır |
> | Görünümler | Hayır | Hayır |

## <a name="microsoftcustomerlockbox"></a>Microsoft. Customerkasası

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | istekler | Hayır | Hayır |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | içermektedir | Hayır | Hayır |
> | resourceProviders | Yes | Yes |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Çizelge | Yes | Yes |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Yes | Yes |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Onlarla | Yes | Hayır |
> | çalışma alanları/Virtualnetworkpeerler | Hayır | Hayır |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | larına | Yes | Yes |
> | veri katalogları | Yes | Yes |
> | veri katalogları/veri kaynakları | Hayır | Hayır |
> | veri katalogları/veri kaynakları/taramalar | Hayır | Hayır |
> | veri katalogları/veri kaynakları/taramalar/veri kümeleri | Hayır | Hayır |
> | veri katalogları/veri kaynakları/taramalar/Tetikleyiciler | Hayır | Hayır |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Veri fabrikaları | Yes | Hayır |
> | DataFactory/diagnosticSettings | Hayır | Hayır |
> | DataFactory/metricDefinitions | Hayır | Hayır |
> | dataFactorySchema | Hayır | Hayır |
> | larının | Yes | Hayır |
> | Fabrika/tümleştirme çalışma zamanları | Hayır | Hayır |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | hesaplar/dataLakeStoreAccounts | Hayır | Hayır |
> | hesaplar/storageAccounts | Hayır | Hayır |
> | hesaplar/storageAccounts/kapsayıcılar | Hayır | Hayır |
> | hesaplar/Transferanaliz tici | Hayır | Hayır |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | hesaplar/eventGridFilters | Hayır | Hayır |
> | hesaplar/firewallRules | Hayır | Hayır |

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | services | Hayır | Hayır |
> | Hizmetler/Projeler | Hayır | Hayır |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | hesaplar/paylaşımlar | Hayır | Hayır |
> | hesaplar/paylaşımlar/veri kümeleri | Hayır | Hayır |
> | hesaplar/paylaşımlar/davetler | Hayır | Hayır |
> | hesaplar/paylaşımlar/providersharesubscriptions | Hayır | Hayır |
> | hesaplar/paylaşımlar/synchronizationSettings | Hayır | Hayır |
> | hesaplar/parçalar esubscriptions | Hayır | Hayır |
> | hesaplar/parçalar esubscriptions/Consumersourcedataset 'ler | Hayır | Hayır |
> | hesaplar/parçalar esubscriptions/datasetmappings | Hayır | Hayır |
> | hesaplar/parçalar esubscriptions/Tetikleyiciler | Hayır | Hayır |

## <a name="microsoftdbformariadb"></a>Microsoft. Dbformarıdb

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | sunucular | Yes | Yes |
> | sunucular/danışmanları | Hayır | Hayır |
> | sunucular/Privateendpointconnectionproxy 'Leri | Hayır | Hayır |
> | sunucular/privateEndpointConnections | Hayır | Hayır |
> | sunucular/privateLinkResources | Hayır | Hayır |
> | sunucular/Querymetinmetinleri | Hayır | Hayır |
> | sunucular/recoverableServers | Hayır | Hayır |
> | sunucular/topQueryStatistics | Hayır | Hayır |
> | sunucular/virtualNetworkRules | Hayır | Hayır |
> | sunucular/waitStatistics | Hayır | Hayır |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | sunucular | Yes | Yes |
> | sunucular/danışmanları | Hayır | Hayır |
> | sunucular/Privateendpointconnectionproxy 'Leri | Hayır | Hayır |
> | sunucular/privateEndpointConnections | Hayır | Hayır |
> | sunucular/privateLinkResources | Hayır | Hayır |
> | sunucular/Querymetinmetinleri | Hayır | Hayır |
> | sunucular/recoverableServers | Hayır | Hayır |
> | sunucular/topQueryStatistics | Hayır | Hayır |
> | sunucular/virtualNetworkRules | Hayır | Hayır |
> | sunucular/waitStatistics | Hayır | Hayır |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Sunucu grupları | Yes | Yes |
> | sunucular | Yes | Yes |
> | sunucular/danışmanları | Hayır | Hayır |
> | sunucular/anahtarlar | Hayır | Hayır |
> | sunucular/Privateendpointconnectionproxy 'Leri | Hayır | Hayır |
> | sunucular/privateEndpointConnections | Hayır | Hayır |
> | sunucular/privateLinkResources | Hayır | Hayır |
> | sunucular/Querymetinmetinleri | Hayır | Hayır |
> | sunucular/recoverableServers | Hayır | Hayır |
> | sunucular/topQueryStatistics | Hayır | Hayır |
> | sunucular/virtualNetworkRules | Hayır | Hayır |
> | sunucular/waitStatistics | Hayır | Hayır |
> | serversv2 | Yes | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | artifactSources | Yes | Yes |
> | piyasaya çıkarma | Yes | Yes |
> | Servicetopolojileri | Yes | Yes |
> | Servicetopolojileri/hizmetler | Yes | Yes |
> | Servicetopolojileri/hizmetler/serviceUnits | Yes | Yes |
> | adımlar | Yes | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Yes | Yes |
> | applicationgroups/uygulamalar | Hayır | Hayır |
> | applicationgroups/masaüstleri | Hayır | Hayır |
> | applicationgroups/startmenuıtems | Hayır | Hayır |
> | Ana bilgisayar havuzları | Yes | Yes |
> | hostpools/oturumkonakları | Hayır | Hayır |
> | hostpools/sessionkonakları/usersessions | Hayır | Hayır |
> | hosthavuzlar/usersessions | Hayır | Hayır |
> | Onlarla | Yes | Yes |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Elaun havuzları | Yes | Yes |
> | Elaun havuzları/ıothubkiracılar | Yes | Yes |
> | Iothubs | Yes | Yes |
> | IotHubs/eventGridFilters | Hayır | Hayır |
> | ProvisioningServices | Yes | Yes |
> | vardır | Hayır | Hayır |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | düzenler | Yes | Yes |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | örleri | Yes | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | labcenters | Yes | Yes |
> | larda | Yes | Yes |
> | Laboratuvarlar/ortamlar | Yes | Yes |
> | Labs/Servicerunanlar | Yes | Yes |
> | Labs/virtualMachines | Yes | Yes |
> | cağını | Yes | Yes |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Hayır | Hayır |
> | Veritabanı hesapları | Yes | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | etki alanları | Yes | Yes |
> | Domains/Domainownershiptanýmlayýcýlarý | Hayır | Hayır |
> | generateSsoRequest | Hayır | Hayır |
> | topLevelDomains | Hayır | Hayır |
> | Validatedomainregistrationınformation | Hayır | Hayır |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Hayır | Hayır |
> | lcsprojects/clouddağıtımları | Hayır | Hayır |
> | lcsprojects/bağlayıcılar | Hayır | Hayır |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | services | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | etki alanları | Yes | Yes |
> | etki alanları/konular | Hayır | Hayır |
> | Eventabonelikleri | Hayır | Hayır |
> | Extensionkonuları | Hayır | Hayır |
> | konularıyla | Yes | Yes |
> | topicTypes | Hayır | Hayır |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | leriniz | Yes | Yes |
> | öznitelikleri | Yes | Yes |
> | ad alanları/authorizationrules | Hayır | Hayır |
> | ad alanları/diskalrecoveryconfigs | Hayır | Hayır |
> | ad alanları/eventhubs | Hayır | Hayır |
> | ad alanları/eventhubs/authorizationrules | Hayır | Hayır |
> | ad alanları/eventhubs/consumergroups | Hayır | Hayır |
> | ad alanları/networkrulesets | Hayır | Hayır |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | SaaS Uygulamaları Geliştirme | Hayır | Hayır |
> | sağlayıcıları | Hayır | Hayır |

## <a name="microsoftgallery"></a>Microsoft. Gallery

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | kaydedemez | Hayır | Hayır |
> | gallergıtems | Hayır | Hayır |
> | generateartifactaccessuri | Hayır | Hayır |
> | myarea | Hayır | Hayır |
> | myarea/alan | Hayır | Hayır |
> | myarea/alan/alan | Hayır | Hayır |
> | myareas/Areas/Areas/gallergıtems | Hayır | Hayır |
> | myareas/Areas/gallergıtems | Hayır | Hayır |
> | myarea/gallergıtems | Hayır | Hayır |
> | kaydolunamadı | Hayır | Hayır |
> | resources | Hayır | Hayır |
> | elde edilecek esourcesbyıd | Hayır | Hayır |

## <a name="microsoftgenomics"></a>Microsoft. Genomiks

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Oto Managedvmconfigurationprofiles | Yes | Yes |
> | Configurationprofileatamalar | Hayır | Hayır |
> | Guestconfigurationatamaları | Hayır | Hayır |
> | yazılımıdır | Hayır | Hayır |
> | softwareUpdateProfile | Hayır | Hayır |
> | softwareUpdates | Hayır | Hayır |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Hanaınstances | Yes | Yes |
> | Sapizleyicileri | Yes | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | ayrılmış Atedhsms | Yes | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | leriniz | Yes | Yes |
> | kümeler/uygulamalar | Hayır | Hayır |

## <a name="microsofthealthcareapis"></a>Microsoft. Healthgelişme API 'leri

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | services | Yes | Yes |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | larla | Yes | Yes |
> | makineler/uzantılar | Yes | Yes |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Veri yöneticileri | Yes | Yes |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | bileşenleri | Yes | Yes |
> | networkScopes | Yes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Çizelge | Yes | Yes |

## <a name="microsoftintune"></a>Microsoft. Intune

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Hayır | Hayır |
> | diagnosticSettingsCategories | Hayır | Hayır |

## <a name="microsoftiotcentral"></a>Microsoft. ıotcentral

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | appTemplates | Hayır | Hayır |
> | Iotapps | Yes | Yes |

## <a name="microsoftiotspaces"></a>Microsoft. ıotspaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Graf | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Silinkaults | Hayır | Hayır |
> | hsmPools | Yes | Yes |
> | kasaları | Yes | Yes |
> | kasa/erişim Ilkeleri | Hayır | Hayır |
> | kasa/eventGridFilters | Hayır | Hayır |
> | kasa/gizlilikler | Hayır | Hayır |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | leriniz | Yes | Yes |
> | kümeler/attacheddatabaseconfigurations | Hayır | Hayır |
> | kümeler/veritabanları | Hayır | Hayır |
> | kümeler/veritabanları/veri bağlantıları | Hayır | Hayır |
> | kümeler/veritabanları/eventhubconnections | Hayır | Hayır |
> | kümeler/parçalar | Hayır | Hayır |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | labaccounts | Yes | Yes |
> | kullanıcılar | Hayır | Hayır |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Yes | Yes |
> | Tümleştirme hesapları | Yes | Yes |
> | ıntegrationserviceortamortamları | Yes | Yes |
> | ıntegrationserviceortamortamları/managedap | Yes | Yes |
> | ısotedenvironments | Yes | Yes |
> | sürdürülen | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Commitmentplanlar | Yes | Yes |
> | Hizmetleri | Yes | Yes |
> | Çalışma Alanları | Yes | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Onlarla | Yes | Yes |
> | çalışma alanları/hesaplar | Hayır | Hayır |
> | çalışma alanları/eventGridFilters | Hayır | Hayır |

## <a name="microsoftmanagedidentity"></a>Microsoft. Managedıdentity

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Lerinizde | Hayır | Hayır |
> | Userassignedıdentities | Yes | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Pazar Placeryumuristrationdefinitions | Hayır | Hayır |
> | Registrationatamaları | Hayır | Hayır |
> | registrationDefinitions | Hayır | Hayır |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | getEntities | Hayır | Hayır |
> | Yönetim grupları | Hayır | Hayır |
> | resources | Hayır | Hayır |
> | startTenantBackfill | Hayır | Hayır |
> | tenantBackfillStatus | Hayır | Hayır |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | hesaplar/eventGridFilters | Hayır | Hayır |

## <a name="microsoftmarketplace"></a>Microsoft. Market

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | sunar | Hayır | Hayır |
> | offerTypes | Hayır | Hayır |
> | offerTypes/yayımcılar | Hayır | Hayır |
> | offerTypes/yayımcılar/teklifler | Hayır | Hayır |
> | offerTypes/yayımcılar/teklifler/planlar | Hayır | Hayır |
> | offerTypes/yayımcılar/teklifler/planlar/anlaşmalar | Hayır | Hayır |
> | offerTypes/yayımcılar/teklifler/planlar/configs | Hayır | Hayır |
> | offerTypes/yayımcılar/teklifler/planlar/configs/ımportımage | Hayır | Hayır |
> | privategallergıtems | Hayır | Hayır |
> | ürün | Hayır | Hayır |
> | Publishers | Hayır | Hayır |
> | Yayımcılar/teklifler | Hayır | Hayır |
> | Yayımcılar/teklifler/Düzeltme | Hayır | Hayır |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Yes | Yes |
> | updateCommunicationPreference | Hayır | Hayır |

## <a name="microsoftmarketplaceordering"></a>Microsoft. Marketplacesıralaması

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | anlaşmalar | Hayır | Hayır |
> | offertypes | Hayır | Hayır |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | mediaservices | Yes | Yes |
> | mediaservices/accountFilters | Hayır | Hayır |
> | mediaservices/varlıklar | Hayır | Hayır |
> | mediaservices/varlıklar/assetFilters | Hayır | Hayır |
> | mediaservices/contentKeyPolicies | Hayır | Hayır |
> | mediaservices/eventGridFilters | Hayır | Hayır |
> | mediaservices/liveEventOperations | Hayır | Hayır |
> | mediaservices/liveEvents | Yes | Yes |
> | mediaservices/liveEvents/Liveçıktılar | Hayır | Hayır |
> | mediaservices/liveOutputOperations | Hayır | Hayır |
> | mediaservices/Mediagraf | Hayır | Hayır |
> | mediaservices/streamingEndpointOperations | Hayır | Hayır |
> | mediaservices/streamingEndpoints | Yes | Yes |
> | mediaservices/Streamingkonumlandırıcı | Hayır | Hayır |
> | mediaservices/streamingPolicies | Hayır | Hayır |
> | mediaservices/dönüşümler | Hayır | Hayır |
> | mediaservices/dönüşümler/işler | Hayır | Hayır |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Appkümeler | Yes | Yes |

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Yes | Yes |
> | migrateprojects | Yes | Yes |
> | projeyle | Yes | Yes |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Yes | Yes |
> | objectUnderstandingAccounts | Yes | Yes |
> | remoteRenderingAccounts | Yes | Yes |
> | spatialAnchorsAccounts | Yes | Yes |
> | surfaceReconstructionAccounts | Yes | Yes |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Yes | Hayır |
> | netAppAccounts/Capacityhavuzları | Yes | Hayır |
> | netAppAccounts/Capacityhavuzları/birimleri | Yes | Hayır |
> | netAppAccounts/Capacityhavuzlar/Volumes/Mount hedefleri | Yes | Hayır |
> | netAppAccounts/Capacityhavuzlar/birimler/anlık görüntüler | Yes | Hayır |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Applicationgateway 'ler | Yes | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Yes | Yes |
> | applicationSecurityGroups | Yes | Yes |
> | azureFirewallFqdnTags | Hayır | Hayır |
> | azureFirewalls | Yes | Hayır |
> | Savunma Konakları | Yes | Yes |
> | bgpServiceCommunities | Hayır | Hayır |
> | bağlantının | Yes | Yes |
> | ddosCustomPolicies | Yes | Yes |
> | Ddosprotectionplanlar | Yes | Yes |
> | Dnsoperationdurumları | Hayır | Hayır |
> | dnszones | Yes | Yes |
> | dnszones/A | Hayır | Hayır |
> | dnszones/AAAA | Hayır | Hayır |
> | dnszones/tümü | Hayır | Hayır |
> | dnszones/CAA | Hayır | Hayır |
> | dnszones/CNAME | Hayır | Hayır |
> | dnszones/MX | Hayır | Hayır |
> | dnszones/NS | Hayır | Hayır |
> | dnszones/PTR | Hayır | Hayır |
> | dnszones/kayıt kümeleri | Hayır | Hayır |
> | dnszones/SOA | Hayır | Hayır |
> | dnszones/SRV | Hayır | Hayır |
> | dnszones/TXT | Hayır | Hayır |
> | Expressroutedevreleri | Yes | Yes |
> | expressRouteCrossConnections | Yes | Yes |
> | Expressroutegateway 'ler | Yes | Yes |
> | expressRoutePorts | Yes | Yes |
> | expressRouteServiceProviders | Hayır | Hayır |
> | firewallPolicies | Yes | Yes |
> | frontkapıların | Evet, ancak sınırlı ( [aşağıdaki nota](#frontdoor)bakın) | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | Evet, ancak sınırlı ( [aşağıdaki nota](#frontdoor)bakın) | Hayır |
> | frontdoorWebApplicationFirewallPolicies | Evet, ancak sınırlı ( [aşağıdaki nota](#frontdoor)bakın) | Yes |
> | getDnsResourceReference | Hayır | Hayır |
> | ınternalnotify | Hayır | Hayır |
> | loadBalancers | Yes | Hayır |
> | Localnetworkgateway 'ler | Yes | Yes |
> | Natgateway 'ler | Yes | Yes |
> | Networkıntpolicies Ilkeleri | Yes | Yes |
> | NetworkInterfaces | Yes | Yes |
> | networkProfiles | Yes | Yes |
> | networkSecurityGroups | Yes | Yes |
> | networkWatchers | Yes | Hayır |
> | networkWatchers/Connectionmonitörleri | Yes | Hayır |
> | networkWatchers/uzunluler | Yes | Hayır |
> | networkWatchers/Pingkafesler | Yes | Hayır |
> | p2sVpnGateways | Yes | Yes |
> | privateDnsOperationStatuses | Hayır | Hayır |
> | privateDnsZones | Yes | Yes |
> | privateDnsZones/A | Hayır | Hayır |
> | privateDnsZones/AAAA | Hayır | Hayır |
> | privateDnsZones/tümü | Hayır | Hayır |
> | privateDnsZones/CNAME | Hayır | Hayır |
> | privateDnsZones/MX | Hayır | Hayır |
> | privateDnsZones/PTR | Hayır | Hayır |
> | privateDnsZones/SOA | Hayır | Hayır |
> | privateDnsZones/SRV | Hayır | Hayır |
> | privateDnsZones/TXT | Hayır | Hayır |
> | privateDnsZones/virtualNetworkLinks | Yes | Yes |
> | privateEndpoints | Yes | Yes |
> | privateLinkServices | Yes | Yes |
> | Publicıpaddresses | Yes | Yes |
> | Publicıpöneklerini | Yes | Yes |
> | routeFilters | Yes | Yes |
> | routeTables | Yes | Yes |
> | serviceEndpointPolicies | Yes | Yes |
> | trafficManagerGeographicHierarchies | Hayır | Hayır |
> | trafficmanagerprofiles | Yes | Yes |
> | trafficmanagerprofiles/heatMaps | Hayır | Hayır |
> | trafficManagerUserMetricsKeys | Hayır | Hayır |
> | Virtualhub 'Lar | Yes | Yes |
> | virtualNetworkGateways | Yes | Yes |
> | virtualNetworks | Yes | Yes |
> | virtualNetworkTaps | Yes | Yes |
> | Virtualwan | Yes | Yes |
> | Vpngateway 'ler | Yes | Hayır |
> | vpnSites | Yes | Yes |
> | webApplicationFirewallPolicies | Yes | Yes |

<a id="frontdoor" />

> [!NOTE]
> Azure ön kapı hizmeti için, kaynak oluştururken Etiketler uygulayabilirsiniz, ancak bu durumda etiketleri güncelleştirmek veya eklemek Şu anda desteklenmemektedir.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | öznitelikleri | Yes | Hayır |
> | ad alanları/Notificationhub 'Lar | Yes | Hayır |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Yes | Yes |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Hiper sanal siteler | Yes | Yes |
> | Importsites | Yes | Yes |
> | Sunucusiteleri | Yes | Yes |
> | VMwareSites | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | leriniz | Yes | Yes |
> | cihazlar | Hayır | Hayır |
> | Bağlantı hedefleri | Hayır | Hayır |
> | Storageınsii configs | Hayır | Hayır |
> | Onlarla | Yes | Yes |
> | çalışma alanları/veri kaynakları | Hayır | Hayır |
> | çalışma alanları/linkedServices | Hayır | Hayır |
> | çalışma alanları/sorgu | Hayır | Hayır |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | managementassociations | Hayır | Hayır |
> | managementconfigurations | Yes | Yes |
> | çözümler | Yes | Yes |
> | Görünümler | Yes | Yes |

## <a name="microsoftpeering"></a>Microsoft. eşleme

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Yasallıklar | Hayır | Hayır |
> | peerAsns | Hayır | Hayır |
> | eşlemeleri | Yes | Yes |
> | peeringServiceProviders | Hayır | Hayır |
> | peeringServices | Yes | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft. Poliyelei

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Poliyevents | Hayır | Hayır |
> | policyMetadata | Hayır | Hayır |
> | policyStates | Hayır | Hayır |
> | policyTrackedResources | Hayır | Hayır |
> | düzeltmeler | Hayır | Hayır |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | konsolları | Hayır | Hayır |
> | panoların | Yes | Yes |
> | userSettings | Hayır | Hayır |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | kapasiteler | Yes | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Backupkorunabilir | Hayır | Hayır |
> | kasaları | Yes | Yes |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | öznitelikleri | Yes | Yes |
> | ad alanları/authorizationrules | Hayır | Hayır |
> | ad alanları/hybridconnections | Hayır | Hayır |
> | ad alanları/hybridconnections/authorizationrules | Hayır | Hayır |
> | ad alanları/wcfreyerleştiri | Hayır | Hayır |
> | ad alanları/wcfreyerleştirme/authorizationrules | Hayır | Hayır |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Hayır | Hayır |
> | koleksiyonlarıyla | Yes | Yes |
> | Koleksiyonlar/uygulamalar | Hayır | Hayır |
> | Koleksiyonlar/SecurityPrincipals | Hayır | Hayır |
> | Templateımages | Hayır | Hayır |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | sorgu | Yes | Yes |
> | resourceChangeDetails | Hayır | Hayır |
> | resourceChanges | Hayır | Hayır |
> | resources | Hayır | Hayır |
> | resourcesHistory | Hayır | Hayır |
> | subscriptionsStatus | Hayır | Hayır |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Kullanılabilirlik durumları | Hayır | Hayır |
> | Childadvailabilitydurumlar | Hayır | Hayır |
> | childResources | Hayır | Hayır |
> | etkinlikler | Hayır | Hayır |
> | ımpactedresources | Hayır | Hayır |
> | meta veriler | Hayır | Hayır |
> | bildirimi | Hayır | Hayır |

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | dağıtımlar | Yes | Hayır |
> | dağıtımlar/işlemler | Hayır | Hayır |
> | deploymentScripts | Yes | Yes |
> | deploymentScripts/Günlükler | Hayır | Hayır |
> | Köprü | Hayır | Hayır |
> | notifyResourceJobs | Hayır | Hayır |
> | sağlayıcıları | Hayır | Hayır |
> | resourceGroups | Yes | Hayır |
> | resources | Hayır | Hayır |
> | Aboneliklerin | Hayır | Hayır |
> | Abonelikler/sağlayıcılar | Hayır | Hayır |
> | Abonelikler/resourceGroups | Hayır | Hayır |
> | Abonelikler/ResourceGroups/kaynaklar | Hayır | Hayır |
> | Abonelikler/kaynaklar | Hayır | Hayır |
> | Abonelikler/etiket adları | Hayır | Hayır |
> | Abonelikler/etiket adları/tagValues | Hayır | Hayır |
> | Kira | Hayır | Hayır |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | uygulamalar | Yes | Yes |
> | saasresources | Hayır | Hayır |

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | işlere | Yes | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Hayır | Hayır |
> | searchServices | Yes | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Hayır | Hayır |
> | advancedThreatProtectionSettings | Hayır | Hayır |
> | alerts | Hayır | Hayır |
> | allowedConnections | Hayır | Hayır |
> | Applicationwhitedökümler | Hayır | Hayır |
> | assessmentMetadata | Hayır | Hayır |
> | kopyalan | Hayır | Hayır |
> | akışlarını otomatikleştirin | Yes | Yes |
> | Oto Provisioningsettings | Hayır | Hayır |
> | Uyumluluklarına | Hayır | Hayır |
> | dataCollectionAgents | Hayır | Hayır |
> | deviceSecurityGroups | Hayır | Hayır |
> | discoveredSecuritySolutions | Hayır | Hayır |
> | externalSecuritySolutions | Hayır | Hayır |
> | Informationprotectionpolicies | Hayır | Hayır |
> | ıotsecuritysolutions | Yes | Yes |
> | ıotsecuritysolutions/analiz Ticsmodeller | Hayır | Hayır |
> | ıotsecuritysolutions/Analticsmodeller/Aggreggıt uyarıları | Hayır | Hayır |
> | ıotsecuritysolutions/analiz Ticsmodeller/Aggreg, öneriler | Hayır | Hayır |
> | Jağaccesspolicies | Hayır | Hayır |
> | networkData | Hayır | Hayır |
> | playbookConfigurations | Yes | Yes |
> | Elerindeki | Hayır | Hayır |
> | fiyatlandırmalar | Hayır | Hayır |
> | Reve daha karmaşık bakım standartları | Hayır | Hayır |
> | Rekontrol ve Re, | Hayır | Hayır |
> | Rekontrol ve Re, Re, Re, | Hayır | Hayır |
> | securityContacts | Hayır | Hayır |
> | securitySolutions | Hayır | Hayır |
> | securitySolutionsReferenceData | Hayır | Hayır |
> | Securitydurumlardan | Hayır | Hayır |
> | securityStatusesSummaries | Hayır | Hayır |
> | Sunucukullanılabilirliği | Hayır | Hayır |
> | ayarlar | Hayır | Hayır |
> | subAssessments | Hayır | Hayır |
> | Görevler | Hayır | Hayır |
> | anlatır | Hayır | Hayır |
> | çalışma alanı ayarları | Hayır | Hayır |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Hayır | Hayır |
> | diagnosticSettingsCategories | Hayır | Hayır |

## <a name="microsoftsecurityinsights"></a>Microsoft. Securityınsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | toplamaları | Hayır | Hayır |
> | alertRules | Hayır | Hayır |
> | Alertrutatemplates | Hayır | Hayır |
> | leriniz | Hayır | Hayır |
> | çalışmaların | Hayır | Hayır |
> | Veri bağlayıcıları | Hayır | Hayır |
> | varlıklar | Hayır | Hayır |
> | entityQueries | Hayır | Hayır |
> | officeConsents | Hayır | Hayır |
> | ayarlar | Hayır | Hayır |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | öznitelikleri | Yes | Hayır |
> | ad alanları/authorizationrules | Hayır | Hayır |
> | ad alanları/diskalrecoveryconfigs | Hayır | Hayır |
> | ad alanları/eventgridfilters | Hayır | Hayır |
> | ad alanları/networkrulesets | Hayır | Hayır |
> | ad alanları/kuyruklar | Hayır | Hayır |
> | ad alanları/kuyruklar/authorizationrules | Hayır | Hayır |
> | ad alanları/konular | Hayır | Hayır |
> | ad alanları/konular/authorizationrules | Hayır | Hayır |
> | ad alanları/konular/abonelikler | Hayır | Hayır |
> | ad alanları/konular/abonelikler/kurallar | Hayır | Hayır |
> | premiumMessagingRegions | Hayır | Hayır |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | uygulamalar | Yes | Yes |
> | leriniz | Yes | Yes |
> | kümeler/uygulamalar | Hayır | Hayır |
> | Kapsayıcı grupları | Yes | Yes |
> | containerGroupSets | Yes | Yes |
> | edgeclusters | Yes | Yes |
> | edgeclusters/uygulamalar | Hayır | Hayır |
> | Mamak | Yes | Yes |
> | secretmağazaları | Yes | Yes |
> | secretmağazaları/sertifikaları | Hayır | Hayır |
> | secretmağazaları/gizli dizileri | Hayır | Hayır |
> | volumes | Yes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft. Servicefabrickafesi

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | uygulamalar | Yes | Yes |
> | Kapsayıcı grupları | Yes | Yes |
> | geçidinin | Yes | Yes |
> | Mamak | Yes | Yes |
> | kaynaklanır | Yes | Yes |
> | volumes | Yes | Yes |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Providerkayıtları | Hayır | Hayır |
> | Providerkayıtlarıyla/resourceTypeRegistrations | Hayır | Hayır |
> | piyasaya çıkarma | Yes | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | SignalR | Yes | Yes |
> | SignalR/eventGridFilters | Hayır | Hayır |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Yes | Yes |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Hybriduseavantajlar | Hayır | Hayır |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Yes | Yes |
> | uygulamalar | Yes | Yes |
> | Jistekleri | Yes | Yes |

## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | ManagedInstances | Yes | Yes |
> | ManagedInstances/veritabanları | Evet ( [aşağıdaki nota](#sqlnote)bakın) | Yes |
> | ManagedInstances/veritabanları/backupShortTermRetentionPolicies | Hayır | Hayır |
> | ManagedInstances/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels | Hayır | Hayır |
> | ManagedInstances/veritabanları/ | Hayır | Hayır |
> | ManagedInstances/veritabanları/ek | Hayır | Hayır |
> | ManagedInstances/encryptionProtector | Hayır | Hayır |
> | ManagedInstances/anahtarlar | Hayır | Hayır |
> | ManagedInstances/Restokbledroppeddatabases/backupShortTermRetentionPolicies | Hayır | Hayır |
> | ManagedInstances/ | Hayır | Hayır |
> | sunucular | Yes | Yes |
> | sunucular/Yöneticiler | Hayır | Hayır |
> | sunucular/communicationLinks | Hayır | Hayır |
> | sunucular/veritabanları | Evet ( [aşağıdaki nota](#sqlnote)bakın) | Yes |
> | sunucular/encryptionProtector | Hayır | Hayır |
> | sunucular/firewallRules | Hayır | Hayır |
> | sunucular/anahtarlar | Hayır | Hayır |
> | sunucular/Restokbledroppeddatabases | Hayır | Hayır |
> | Sunucu/hizmet hedefleri | Hayır | Hayır |
> | sunucular/tdeCertificates | Hayır | Hayır |
> | Virtualkümeler | Hayır | Hayır |

<a id="sqlnote" />

> [!NOTE]
> Ana veritabanı etiketleri desteklemez, ancak Azure SQL veri ambarı veritabanları da dahil olmak üzere diğer veritabanları, Etiketler ' i destekler. Azure SQL veri ambarı veritabanlarının etkin (duraklatılmış değil) durumda olması gerekir.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Yes | Yes |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Hayır | Hayır |
> | SqlVirtualMachines | Yes | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Yes | Yes |
> | storageAccounts/blobServices | Hayır | Hayır |
> | storageAccounts/fileServices | Hayır | Hayır |
> | storageAccounts/queueServices | Hayır | Hayır |
> | storageAccounts/Services | Hayır | Hayır |
> | storageAccounts/Services/metricDefinitions | Hayır | Hayır |
> | storageAccounts/tableServices | Hayır | Hayır |
> | vardır | Hayır | Hayır |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | önbelleklerinde | Yes | Yes |
> | önbellekler/Storagetaral | Hayır | Hayır |
> | Usagemodeller | Hayır | Hayır |

## <a name="microsoftstoragereplication"></a>Microsoft. Storagerepce

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Hayır | Hayır |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Yes | Yes |
> | storageSyncServices/registeredServers | Hayır | Hayır |
> | storageSyncServices/syncGroups | Hayır | Hayır |
> | storageSyncServices/syncGroups/cloudEndpoints | Hayır | Hayır |
> | storageSyncServices/syncGroups/serverEndpoints | Hayır | Hayır |
> | storageSyncServices/iş akışları | Hayır | Hayır |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Yes | Yes |
> | storageSyncServices/registeredServers | Hayır | Hayır |
> | storageSyncServices/syncGroups | Hayır | Hayır |
> | storageSyncServices/syncGroups/cloudEndpoints | Hayır | Hayır |
> | storageSyncServices/syncGroups/serverEndpoints | Hayır | Hayır |
> | storageSyncServices/iş akışları | Hayır | Hayır |

## <a name="microsoftstoragesyncint"></a>Microsoft. Storagesyncınt

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Yes | Yes |
> | storageSyncServices/registeredServers | Hayır | Hayır |
> | storageSyncServices/syncGroups | Hayır | Hayır |
> | storageSyncServices/syncGroups/cloudEndpoints | Hayır | Hayır |
> | storageSyncServices/syncGroups/serverEndpoints | Hayır | Hayır |
> | storageSyncServices/iş akışları | Hayır | Hayır |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | ilerinde | Yes | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | streammingjobs | Evet (aşağıdaki nota bakın) | Yes |

> [!NOTE]
> Streamingjobs çalışırken bir etiket ekleyemezsiniz. Etiketi eklemek için kaynağı durdurun.

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | İptal | Hayır | Hayır |
> | CreateSubscription | Hayır | Hayır |
> | etkinleştirebilir | Hayır | Hayır |
> | Yeniden Adlandır | Hayır | Hayır |
> | SubscriptionDefinitions | Hayır | Hayır |
> | SubscriptionOperations | Hayır | Hayır |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Lý | Yes | Hayır |
> | ortamlar/accessPolicies | Hayır | Hayır |
> | ortamlar/EventSources | Yes | Hayır |
> | ortamlar/Referencedataset 'ler | Yes | Hayır |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Vmwarechoparlör basit

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | ayrılmış Cloudnodes | Yes | Yes |
> | ayrılmış CloudService | Yes | Yes |
> | virtualMachines | Yes | Yes |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Hayır | Hayır |
> | apiManagementAccounts/Apiacl 'Ler | Hayır | Hayır |
> | apiManagementAccounts/API 'ler | Hayır | Hayır |
> | apiManagementAccounts/API/Apiacl 'Ler | Hayır | Hayır |
> | apiManagementAccounts/API/Connectionacl 'Ler | Hayır | Hayır |
> | apiManagementAccounts/API/bağlantı | Hayır | Hayır |
> | apiManagementAccounts/API/Connections/Connectionacl 'Ler | Hayır | Hayır |
> | apiManagementAccounts/API/localizedDefinitions | Hayır | Hayır |
> | apiManagementAccounts/Connectionacl 'Ler | Hayır | Hayır |
> | apiManagementAccounts/bağlantılar | Hayır | Hayır |
> | billingMeters | Hayır | Hayır |
> | Sertifika | Yes | Yes |
> | Connectiongateway 'ler | Yes | Yes |
> | bağlantının | Yes | Yes |
> | Customapsıs | Yes | Yes |
> | Silinmi siteleri | Hayır | Hayır |
> | işlevleri | Hayır | Hayır |
> | hostingEnvironments | Yes | Yes |
> | hostingEnvironments/multiRolePools | Hayır | Hayır |
> | hostingEnvironments/workerPools | Hayır | Hayır |
> | publishingUsers | Hayır | Hayır |
> | Önerileri | Hayır | Hayır |
> | resourceHealthMetadata | Hayır | Hayır |
> | zamanları | Hayır | Hayır |
> | serverFarms | Yes | Yes |
> | Sunucugrupları/eventGridFilters | Hayır | Hayır |
> | barındıra | Yes | Yes |
> | siteler/yapılandırma  | Hayır | Hayır |
> | siteler/eventGridFilters | Hayır | Hayır |
> | siteler/hostNameBindings | Hayır | Hayır |
> | Sites/networkConfig | Hayır | Hayır |
> | siteler/premieraddons | Yes | Yes |
> | siteler/yuvalar | Yes | Yes |
> | siteler/yuvalar/eventGridFilters | Hayır | Hayır |
> | siteler/yuvalar/hostNameBindings | Hayır | Hayır |
> | siteler/yuvalar/networkConfig | Hayır | Hayır |
> | sourceControls | Hayır | Hayır |
> | doğrulamalısınız | Hayır | Hayır |
> | verifyHostingEnvironmentVnet | Hayır | Hayır |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. Windowssavunma Deratp

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Hayır | Hayır |
> | diagnosticSettingsCategories | Hayır | Hayır |

## <a name="microsoftwindowsiot"></a>Microsoft. Windowsıot

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Yes | Yes |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | bileşenleri | Hayır | Hayır |
> | componentsSummary | Hayır | Hayır |
> | Izleme örnekleri | Hayır | Hayır |
> | Izleme ınstancessummary | Hayır | Hayır |
> | monitörün | Hayır | Hayır |
> | notificationSettings | Hayır | Hayır |

## <a name="next-steps"></a>Sonraki adımlar

Kaynaklara etiketlerin nasıl uygulanacağını öğrenmek için bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](resource-group-using-tags.md).
