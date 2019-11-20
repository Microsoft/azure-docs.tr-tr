---
title: Tam modda silme
description: Azure Resource Manager şablonlarda kaynak türlerinin tamamlanma modu silme işlemini nasıl işleyeceğini gösterir.
ms.topic: conceptual
ms.date: 10/27/2019
ms.openlocfilehash: e8e473ba697dd64d2562a2e1efc03f1ba5b384e6
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149931"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Tüm mod dağıtımları için Azure kaynaklarını silme

Bu makalede, kaynak türlerinin, tamamlanmış modda dağıtılan bir şablonda olmadığında silinme işleminin nasıl işleneceği açıklanır.

Türü, tamamlanmış modla dağıtılan şablonda olmadığında, **Evet** ile işaretlenen kaynak türleri silinir.

**Hayır** ile işaretlenen kaynak türleri, şablonda olmadığında otomatik olarak silinmez; Ancak, üst kaynak silinirse bunlar silinir. Davranışın tam açıklaması için bkz. [Azure Resource Manager Dağıtım modları](deployment-modes.md).

[Bir şablonda birden fazla kaynak grubuna](resource-manager-cross-resource-group-deployment.md)dağıtırsanız, dağıtım işleminde belirtilen kaynak grubundaki kaynaklar silinebilir. İkincil kaynak gruplarındaki kaynaklar silinmez.

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
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | DomainServices | Yes |
> | DomainServices/oucontainer | Hayır |
> | DomainServices/ReplicaSets | Yes |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Destek sağlayıcıları | Hayır |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | aadsupportcases | Hayır |
> | addsservices | Hayır |
> | aracısını | Hayır |
> | anonymousapiusers | Hayır |
> | yapılandırma | Hayır |
> | logs | Hayır |
> | raporlar | Hayır |
> | servicehealthölçümleri | Hayır |
> | services | Hayır |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | konfigürasyonları | Hayır |
> | Generatereyorumgeçişleri | Hayır |
> | meta veriler | Hayır |
> | Önerileri | Hayır |
> | gizlemeleri | Hayır |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | actionRules | Yes |
> | alerts | Hayır |
> | alertsList | Hayır |
> | alertsMetaData | Hayır |
> | alertsSummary | Hayır |
> | alertsSummaryList | Hayır |
> | Lerimi | Hayır |
> | smartDetectorAlertRules | Yes |
> | Smartdetectorruntimeortamortamları | Hayır |
> | smartGroups | Hayır |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | sunucular | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | reportFeedback | Hayır |
> | hizmet | Yes |
> | validateServiceName | Hayır |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Configurationmağazaların | Yes |
> | Configurationmağazaların/eventGridFilters | Hayır |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Spring | Yes |

## <a name="microsoftattestation"></a>Microsoft. kanıtlama

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | attestationProviders | Hayır |

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | classicAdministrators | Hayır |
> | Datatakma adlar | Hayır |
> | Denyasatamaları | Hayır |
> | Erişimi yükseltme | Hayır |
> | Findorphanroleatamalar | Hayır |
> | kaynaktaki | Hayır |
> | izinler | Hayır |
> | Poliyasatamaları | Hayır |
> | policyDefinitions | Hayır |
> | policySetDefinitions | Hayır |
> | providerOperations | Hayır |
> | roleAssignments | Hayır |
> | roleDefinitions | Hayır |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | automationAccounts | Yes |
> | automationAccounts/Configurations | Yes |
> | automationAccounts/Jobs | Hayır |
> | automationAccounts/runbook 'lar | Yes |
> | automationAccounts/softwareUpdateConfigurations | Hayır |
> | automationAccounts/Web kancaları | Hayır |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Configurationmağazaların | Yes |
> | Configurationmağazaların/eventGridFilters | Hayır |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Genfiliz

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Lý | Hayır |
> | ortamlar/hesaplar | Hayır |
> | ortamlar/hesaplar/ad alanları | Hayır |
> | ortamlar/hesaplar/ad alanları/yapılandırma | Hayır |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | b2cDirectories | Yes |
> | b2ctenants | Hayır |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Hybriddatayöneticileri | Yes |
> | Postgresınstances | Yes |
> | Sqlbıgdatakümeler | Yes |
> | SQLInstances | Yes |
> | Sqlserverkayıtları | Yes |
> | Sqlserverkayıtları/sqlServers | Hayır |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | kayıtlarında | Yes |
> | kayıt/müşteri abonelikleri | Hayır |
> | kayıtlar/ürünler | Hayır |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | batchAccounts | Yes |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | billingAccounts | Hayır |
> | billingAccounts/anlaşmalar | Hayır |
> | billingAccounts/billingPermissions | Hayır |
> | billingAccounts/billingProfiles | Hayır |
> | billingAccounts/Billingprofiller/billingPermissions | Hayır |
> | billingAccounts/billingProfiles/Billingroleatamaları | Hayır |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Hayır |
> | billingAccounts/billingProfiles/Billingabonelikleri | Hayır |
> | billingAccounts/billingProfiles/Createbillingroleatama | Hayır |
> | billingAccounts/billingProfiles/müşteriler | Hayır |
> | billingAccounts/billingProfiles/faturalar | Hayır |
> | billingAccounts/billingProfiles/faturalar/fiyat listesi | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/billingPermissions | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/Billingroleatamaları | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/billingRoleDefinitions | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/Billingabonelikleri | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/Createbillingroleatama | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/ınitiatetransfer | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/ürünler | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/ürünler/aktarım | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/ürünler/updateAutoRenew | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/işlemler | Hayır |
> | billingAccounts/billingProfiles/ınvoicesections/aktarımlar | Hayır |
> | billingAccounts/BillingProfiles/patchOperations | Hayır |
> | billingAccounts/billingProfiles/paymentMethods | Hayır |
> | billingAccounts/billingProfiles/ilkeler | Hayır |
> | billingAccounts/billingProfiles/fiyat listesi | Hayır |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Hayır |
> | billingAccounts/billingProfiles/ürünler | Hayır |
> | billingAccounts/billingProfiles/işlemler | Hayır |
> | billingAccounts/Billingroleatamaları | Hayır |
> | billingAccounts/billingRoleDefinitions | Hayır |
> | billingAccounts/Billingabonelikleri | Hayır |
> | billingAccounts/Createbillingroleatama | Hayır |
> | billingAccounts/Createınvoicesectionoperations | Hayır |
> | billingAccounts/müşteriler | Hayır |
> | billingAccounts/müşteriler/billingPermissions | Hayır |
> | billingAccounts/müşteriler/Billingabonelikleri | Hayır |
> | billingAccounts/müşteriler/ınitiatetransfer | Hayır |
> | billingAccounts/müşteriler/ilkeler | Hayır |
> | billingAccounts/müşteriler/ürünler | Hayır |
> | billingAccounts/müşteriler/işlemler | Hayır |
> | billingAccounts/müşteriler/aktarımlar | Hayır |
> | billingAccounts/departmanlar | Hayır |
> | billingAccounts/KayıtSayısı | Hayır |
> | billingAccounts/faturalar | Hayır |
> | billingAccounts/ınvoicesections | Hayır |
> | billingAccounts/ınvoicesections/billingSubscriptionMoveOperations | Hayır |
> | billingAccounts/ınvoicesections/Billingabonelikleri | Hayır |
> | billingAccounts/ınvoicesections/Billingabonelikleri/aktarımı | Hayır |
> | billingAccounts/ınvoicesections/yükselt | Hayır |
> | billingAccounts/ınvoicesections/ınitiatetransfer | Hayır |
> | billingAccounts/ınvoicesections/patchOperations | Hayır |
> | billingAccounts/ınvoicesections/productMoveOperations | Hayır |
> | billingAccounts/Ürünler/Ürünler | Hayır |
> | billingAccounts/ınvoicesections/ürünler/transfer | Hayır |
> | billingAccounts/ınvoicesections/ürünler/updateAutoRenew | Hayır |
> | billingAccounts/ınvoicesections/işlemler | Hayır |
> | billingAccounts/ınvoicesections/aktarımlar | Hayır |
> | billingAccounts/Lineofkredisi | Hayır |
> | billingAccounts/patchOperations | Hayır |
> | billingAccounts/paymentMethods | Hayır |
> | billingAccounts/ürünler | Hayır |
> | billingAccounts/işlemler | Hayır |
> | Billingdönemler | Hayır |
> | billingPermissions | Hayır |
> | billingProperty | Hayır |
> | Billingroleatamaları | Hayır |
> | billingRoleDefinitions | Hayır |
> | Createbillingroleatama | Hayır |
> | bölümlerinin | Hayır |
> | kayıt sayısı | Hayır |
> | faturalardan | Hayır |
> | girişinde | Hayır |
> | aktarımlar/acceptTransfer | Hayır |
> | aktarımlar/declineTransfer | Hayır |
> | aktarımlar/operationStatus | Hayır |
> | aktarımlar/validateTransfer | Hayır |
> | validateAddress | Hayır |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Mapapsıs | Yes |
> | updateCommunicationPreference | Hayır |

## <a name="microsoftblockchain"></a>Microsoft. Blockzinciri

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | blockchainMembers | Yes |
> | Cordadmembers | Yes |
> | izleyici | Yes |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Şema tasmi | Hayır |
> | Blueprintasbir/Atamaperations | Hayır |
> | Blueprintasbir/işlemleri | Hayır |
> | Blueprint | Hayır |
> | planlar/yapıtlar | Hayır |
> | planlar/sürümler | Hayır |
> | planlar/sürümler/yapılar | Hayır |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | botServices | Yes |
> | botServices/kanallar | Hayır |
> | botServices/Connections | Hayır |
> | Diller | Hayır |
> | templates | Hayır |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Redis | Yes |
> | RedisConfigDefinition | Hayır |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | appliedReservations | Hayır |
> | calculateExchange | Hayır |
> | calculatePrice | Hayır |
> | calculatePurchasePrice | Hayır |
> | larına | Hayır |
> | Ticari Vaalrezervler | Hayır |
> | değişimi | Hayır |
> | placePurchaseOrder | Hayır |
> | Rezervler | Hayır |
> | Rezervler/Hesaplaizterefund | Hayır |
> | Rezervler/Birleştir | Hayır |
> | Rezervler/rezervasyonlar | Hayır |
> | Rezervler/rezervasyonlar/düzeltmeler | Hayır |
> | Rezervler/geri dönüş | Hayır |
> | Rezervler/Böl | Hayır |
> | Rezervler/takas | Hayır |
> | oluşturamaz | Hayır |
> | resources | Hayır |
> | validateReservationOrder | Hayır |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Hayır |
> | CdnWebApplicationFirewallPolicies | Yes |
> | edgenodes | Hayır |
> | profiles | Yes |
> | Profiller/uç noktalar | Yes |
> | Profiller/uç noktalar/customdomains | Hayır |
> | Profiller/uç noktalar/kaynaklar | Hayır |
> | Validatearaştırması | Hayır |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Sertifikadüzenleri | Yes |
> | certificateOrders/Certificates | Hayır |
> | Validatecertificateregistrationınformation | Hayır |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Yetenek | Hayır |
> | domainNames | Yes |
> | domainNames/yetenekleri | Hayır |
> | domainNames/internalLoadBalancers | Hayır |
> | domainNames/serviceCertificates | Hayır |
> | domainNames/Yuvaları | Hayır |
> | domainNames/yuvalar/roller | Hayır |
> | domainNames/yuvalar/roller/metricDefinitions | Hayır |
> | domainNames/yuvalar/roller/ölçümler | Hayır |
> | moveSubscriptionResources | Hayır |
> | operatingSystemFamilies | Hayır |
> | operatingSystems | Hayır |
> | quotas | Hayır |
> | resourceTypes | Hayır |
> | Validatesubscriptionmoveavaılabılıty | Hayır |
> | virtualMachines | Yes |
> | virtualMachines/diagnosticSettings | Hayır |
> | virtualMachines/metricDefinitions | Hayır |
> | virtualMachines/ölçümler | Hayır |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | classicInfrastructureResources | Hayır |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Yetenek | Hayır |
> | expressRouteCrossConnections | Hayır |
> | expressRouteCrossConnections/peerler | Hayır |
> | gatewaySupportedDevices | Hayır |
> | networkSecurityGroups | Yes |
> | quotas | Hayır |
> | Rezervler | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/Remotevirtualnetworkpeeringproxy 'Leri | Hayır |
> | virtualNetworks/Virtualnetworkpeerler | Hayır |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Yetenek | Hayır |
> | disklerinden | Hayır |
> | images | Hayır |
> | osImages | Hayır |
> | Osplatformımages | Hayır |
> | Publicımages | Hayır |
> | quotas | Hayır |
> | storageAccounts | Yes |
> | storageAccounts/blobServices | Hayır |
> | storageAccounts/fileServices | Hayır |
> | storageAccounts/metricDefinitions | Hayır |
> | storageAccounts/ölçümler | Hayır |
> | storageAccounts/queueServices | Hayır |
> | storageAccounts/Services | Hayır |
> | storageAccounts/Services/diagnosticSettings | Hayır |
> | storageAccounts/Services/metricDefinitions | Hayır |
> | storageAccounts/Services/ölçümler | Hayır |
> | storageAccounts/tableServices | Hayır |
> | storageAccounts/Vmımages | Hayır |
> | Vmımages | Hayır |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | RateCard | Hayır |
> | Usagetoplamaları | Hayır |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | availabilitySets | Yes |
> | diskEncryptionSets | Yes |
> | disklerinden | Yes |
> | Galeriler | Yes |
> | Galeriler/uygulamalar | Hayır |
> | Galeriler/uygulamalar/sürümler | Hayır |
> | Galeriler/görüntüler | Hayır |
> | Galeriler/resimler/sürümler | Hayır |
> | hostGroups | Yes |
> | hostGroups/konaklar | Yes |
> | images | Yes |
> | proximityPlacementGroups | Yes |
> | restorePointCollections | Yes |
> | restorePointCollections/restorePoints | Hayır |
> | sharedVMExtensions | Yes |
> | sharedVMExtensions/sürümler | Hayır |
> | Sharedvmımages | Yes |
> | Sharedvmımages/sürümler | Hayır |
> | anlık görüntüler | Yes |
> | virtualMachines | Yes |
> | virtualMachines/uzantıları | Yes |
> | virtualMachines/metricDefinitions | Hayır |
> | virtualMachineScaleSets | Yes |
> | virtualMachineScaleSets/uzantılar | Hayır |
> | virtualMachineScaleSets/NetworkInterfaces | Hayır |
> | virtualMachineScaleSets/Publicıpaddresses | Hayır |
> | virtualMachineScaleSets/virtualMachines | Hayır |
> | virtualMachineScaleSets/virtualMachines/NetworkInterfaces | Hayır |

## <a name="microsoftconsumption"></a>Microsoft. tüketim

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Aggregmalyt maliyeti | Hayır |
> | Bakiyeler | Hayır |
> | Bütçeler | Hayır |
> | Ücretler | Hayır |
> | CostTags | Hayır |
> | iler | Hayır |
> | etkinlikler | Hayır |
> | Tahminler | Hayır |
> | oluş | Hayır |
> | Marketlerinden | Hayır |
> | Pricesheets | Hayır |
> | ürün | Hayır |
> | Rezervde ayrıntıları | Hayır |
> | Rezervationönerilere | Hayır |
> | Rezervlerin Özeti | Hayır |
> | Rezervlik Işlemleri | Hayır |
> | Etiketler | Hayır |
> | Kira | Hayır |
> | Koşullar | Hayır |
> | UsageDetails | Hayır |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Kapsayıcı grupları | Yes |
> | serviceAssociationLinks | Hayır |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | kayıt | Yes |
> | kayıt defterleri/derlemeler | Hayır |
> | kayıt defterleri/derlemeler/iptal | Hayır |
> | kayıt defterleri/derlemeler/getLogLink | Hayır |
> | kayıt defterleri/buildTasks | Yes |
> | kayıt defterleri/buildTasks/Steps | Hayır |
> | kayıt defterleri/eventGridFilters | Hayır |
> | kayıt defterleri/generateCredentials | Hayır |
> | kayıt defterleri/getBuildSourceUploadUrl 'Si | Hayır |
> | kayıt defterleri/GetCredentials | Hayır |
> | kayıt defterleri/ımportımage | Hayır |
> | kayıt defterleri/queueBuild | Hayır |
> | kayıt defterleri/regenerateCredential | Hayır |
> | kayıt defterleri/regenerateCredentials | Hayır |
> | kayıt defterleri/çoğaltmalar | Yes |
> | kayıt defterleri/çalıştırmalar | Hayır |
> | kayıt defterleri/çalıştırmalar/iptal | Hayır |
> | kayıt defterleri/scheduleRun | Hayır |
> | kayıt defterleri/Kapsameşlemler | Hayır |
> | kayıt defterleri/görevler | Yes |
> | kayıt defterleri/belirteçler | Hayır |
> | kayıt defterleri/updatePolicies | Hayır |
> | kayıt defterleri/Web kancaları | Yes |
> | kayıt defterleri/Web kancaları/getCallbackConfig | Hayır |
> | kayıt defterleri/Web kancaları/ping | Hayır |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | containerServices | Yes |
> | Managedkümeler | Yes |
> | openShiftManagedClusters | Yes |

## <a name="microsoftcortanaanalytics"></a>Microsoft. Cortanaanalizi

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Uyarılar | Hayır |
> | billingAccounts | Hayır |
> | Bütçeler | Hayır |
> | Cloudbağlayıcıları | Hayır |
> | Bağlayıcılar | Yes |
> | bölümlerinin | Hayır |
> | Boyutlar | Hayır |
> | kayıt sayısı | Hayır |
> | Dışarı aktarmalar | Hayır |
> | ExternalBillingAccounts | Hayır |
> | ExternalBillingAccounts/uyarılar | Hayır |
> | ExternalBillingAccounts/Boyutlar | Hayır |
> | ExternalBillingAccounts/tahmin | Hayır |
> | ExternalBillingAccounts/sorgu | Hayır |
> | Externalabonelikleri | Hayır |
> | Externalabonelikleri/uyarıları | Hayır |
> | Externalabonelikler/Boyutlar | Hayır |
> | Externalabonelikler/tahmin | Hayır |
> | Externalabonelikler/sorgu | Hayır |
> | T | Hayır |
> | Sorgu | Hayır |
> | kaydolunamadı | Hayır |
> | Reportconfigs | Hayır |
> | Raporlar | Hayır |
> | Ayarlar | Hayır |
> | showbackRules | Hayır |
> | Görünümler | Hayır |

## <a name="microsoftcustomerlockbox"></a>Microsoft. Customerkasası

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | istekler | Hayır |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | içermektedir | Hayır |
> | resourceProviders | Yes |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Çizelge | Yes |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Yes |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Onlarla | Yes |
> | çalışma alanları/Virtualnetworkpeerler | Hayır |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | larına | Yes |
> | veri katalogları | Yes |
> | veri katalogları/veri kaynakları | Hayır |
> | veri katalogları/veri kaynakları/taramalar | Hayır |
> | veri katalogları/veri kaynakları/taramalar/veri kümeleri | Hayır |
> | veri katalogları/veri kaynakları/taramalar/Tetikleyiciler | Hayır |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Veri fabrikaları | Yes |
> | DataFactory/diagnosticSettings | Hayır |
> | DataFactory/metricDefinitions | Hayır |
> | dataFactorySchema | Hayır |
> | larının | Yes |
> | Fabrika/tümleştirme çalışma zamanları | Hayır |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |
> | hesaplar/dataLakeStoreAccounts | Hayır |
> | hesaplar/storageAccounts | Hayır |
> | hesaplar/storageAccounts/kapsayıcılar | Hayır |
> | hesaplar/Transferanaliz tici | Hayır |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |
> | hesaplar/eventGridFilters | Hayır |
> | hesaplar/firewallRules | Hayır |

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | services | Yes |
> | Hizmetler/Projeler | Yes |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |
> | hesaplar/paylaşımlar | Hayır |
> | hesaplar/paylaşımlar/veri kümeleri | Hayır |
> | hesaplar/paylaşımlar/davetler | Hayır |
> | hesaplar/paylaşımlar/providersharesubscriptions | Hayır |
> | hesaplar/paylaşımlar/synchronizationSettings | Hayır |
> | hesaplar/parçalar esubscriptions | Hayır |
> | hesaplar/parçalar esubscriptions/Consumersourcedataset 'ler | Hayır |
> | hesaplar/parçalar esubscriptions/datasetmappings | Hayır |
> | hesaplar/parçalar esubscriptions/Tetikleyiciler | Hayır |

## <a name="microsoftdbformariadb"></a>Microsoft. Dbformarıdb

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | sunucular | Yes |
> | sunucular/danışmanları | Hayır |
> | sunucular/Privateendpointconnectionproxy 'Leri | Hayır |
> | sunucular/privateEndpointConnections | Hayır |
> | sunucular/privateLinkResources | Hayır |
> | sunucular/Querymetinmetinleri | Hayır |
> | sunucular/recoverableServers | Hayır |
> | sunucular/topQueryStatistics | Hayır |
> | sunucular/virtualNetworkRules | Hayır |
> | sunucular/waitStatistics | Hayır |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | sunucular | Yes |
> | sunucular/danışmanları | Hayır |
> | sunucular/Privateendpointconnectionproxy 'Leri | Hayır |
> | sunucular/privateEndpointConnections | Hayır |
> | sunucular/privateLinkResources | Hayır |
> | sunucular/Querymetinmetinleri | Hayır |
> | sunucular/recoverableServers | Hayır |
> | sunucular/topQueryStatistics | Hayır |
> | sunucular/virtualNetworkRules | Hayır |
> | sunucular/waitStatistics | Hayır |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Sunucu grupları | Yes |
> | sunucular | Yes |
> | sunucular/danışmanları | Hayır |
> | sunucular/anahtarlar | Hayır |
> | sunucular/Privateendpointconnectionproxy 'Leri | Hayır |
> | sunucular/privateEndpointConnections | Hayır |
> | sunucular/privateLinkResources | Hayır |
> | sunucular/Querymetinmetinleri | Hayır |
> | sunucular/recoverableServers | Hayır |
> | sunucular/topQueryStatistics | Hayır |
> | sunucular/virtualNetworkRules | Hayır |
> | sunucular/waitStatistics | Hayır |
> | serversv2 | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | artifactSources | Yes |
> | piyasaya çıkarma | Yes |
> | Servicetopolojileri | Yes |
> | Servicetopolojileri/hizmetler | Yes |
> | Servicetopolojileri/hizmetler/serviceUnits | Yes |
> | adımlar | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | applicationgroups | Yes |
> | applicationgroups/uygulamalar | Hayır |
> | applicationgroups/masaüstleri | Hayır |
> | applicationgroups/startmenuıtems | Hayır |
> | Ana bilgisayar havuzları | Yes |
> | hostpools/oturumkonakları | Hayır |
> | hostpools/sessionkonakları/usersessions | Hayır |
> | hosthavuzlar/usersessions | Hayır |
> | Onlarla | Yes |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Elaun havuzları | Yes |
> | Elaun havuzları/ıothubkiracılar | Yes |
> | Iothubs | Yes |
> | IotHubs/eventGridFilters | Hayır |
> | ProvisioningServices | Yes |
> | vardır | Hayır |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | düzenler | Yes |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | örleri | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | labcenters | Yes |
> | larda | Yes |
> | Laboratuvarlar/ortamlar | Yes |
> | Labs/Servicerunanlar | Yes |
> | Labs/virtualMachines | Yes |
> | cağını | Yes |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | databaseAccountNames | Hayır |
> | Veritabanı hesapları | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | etki alanları | Yes |
> | Domains/Domainownershiptanýmlayýcýlarý | Hayır |
> | generateSsoRequest | Hayır |
> | topLevelDomains | Hayır |
> | Validatedomainregistrationınformation | Hayır |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | lcsprojects | Hayır |
> | lcsprojects/clouddağıtımları | Hayır |
> | lcsprojects/bağlayıcılar | Hayır |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | services | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | etki alanları | Yes |
> | etki alanları/konular | Hayır |
> | Eventabonelikleri | Hayır |
> | Extensionkonuları | Hayır |
> | konularıyla | Yes |
> | topicTypes | Hayır |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | leriniz | Yes |
> | öznitelikleri | Yes |
> | ad alanları/authorizationrules | Hayır |
> | ad alanları/diskalrecoveryconfigs | Hayır |
> | ad alanları/eventhubs | Hayır |
> | ad alanları/eventhubs/authorizationrules | Hayır |
> | ad alanları/eventhubs/consumergroups | Hayır |
> | ad alanları/networkrulesets | Hayır |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | SaaS Uygulamaları Geliştirme | Hayır |
> | sağlayıcıları | Hayır |

## <a name="microsoftgallery"></a>Microsoft. Gallery

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | kaydedemez | Hayır |
> | gallergıtems | Hayır |
> | generateartifactaccessuri | Hayır |
> | myarea | Hayır |
> | myarea/alan | Hayır |
> | myarea/alan/alan | Hayır |
> | myareas/Areas/Areas/gallergıtems | Hayır |
> | myareas/Areas/gallergıtems | Hayır |
> | myarea/gallergıtems | Hayır |
> | kaydolunamadı | Hayır |
> | resources | Hayır |
> | elde edilecek esourcesbyıd | Hayır |

## <a name="microsoftgenomics"></a>Microsoft. Genomiks

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Oto Managedvmconfigurationprofiles | Yes |
> | Configurationprofileatamalar | Hayır |
> | Guestconfigurationatamaları | Hayır |
> | yazılımıdır | Hayır |
> | softwareUpdateProfile | Hayır |
> | softwareUpdates | Hayır |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Hanaınstances | Yes |
> | Sapizleyicileri | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | ayrılmış Atedhsms | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | leriniz | Yes |
> | kümeler/uygulamalar | Hayır |

## <a name="microsofthealthcareapis"></a>Microsoft. Healthgelişme API 'leri

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | services | Yes |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | larla | Yes |
> | makineler/uzantılar | Yes |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Veri yöneticileri | Yes |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | bileşenleri | Yes |
> | networkScopes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Çizelge | Yes |

## <a name="microsoftintune"></a>Microsoft. Intune

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | diagnosticSettings | Hayır |
> | diagnosticSettingsCategories | Hayır |

## <a name="microsoftiotcentral"></a>Microsoft. ıotcentral

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | appTemplates | Hayır |
> | Iotapps | Yes |

## <a name="microsoftiotspaces"></a>Microsoft. ıotspaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Graf | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Silinkaults | Hayır |
> | hsmPools | Yes |
> | kasaları | Yes |
> | kasa/erişim Ilkeleri | Hayır |
> | kasa/eventGridFilters | Hayır |
> | kasa/gizlilikler | Hayır |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | leriniz | Yes |
> | kümeler/attacheddatabaseconfigurations | Hayır |
> | kümeler/veritabanları | Hayır |
> | kümeler/veritabanları/veri bağlantıları | Hayır |
> | kümeler/veritabanları/eventhubconnections | Hayır |
> | kümeler/parçalar | Hayır |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | labaccounts | Yes |
> | kullanıcılar | Hayır |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | hostingEnvironments | Yes |
> | Tümleştirme hesapları | Yes |
> | ıntegrationserviceortamortamları | Yes |
> | ıntegrationserviceortamortamları/managedap | Yes |
> | ısotedenvironments | Yes |
> | sürdürülen | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Commitmentplanlar | Yes |
> | Hizmetleri | Yes |
> | Çalışma Alanları | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Onlarla | Yes |
> | çalışma alanları/hesaplar | Hayır |
> | çalışma alanları/eventGridFilters | Hayır |

## <a name="microsoftmanagedidentity"></a>Microsoft. Managedıdentity

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Lerinizde | Hayır |
> | Userassignedıdentities | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Pazar Placeryumuristrationdefinitions | Hayır |
> | Registrationatamaları | Hayır |
> | registrationDefinitions | Hayır |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | getEntities | Hayır |
> | Yönetim grupları | Hayır |
> | resources | Hayır |
> | startTenantBackfill | Hayır |
> | tenantBackfillStatus | Hayır |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |
> | hesaplar/eventGridFilters | Hayır |

## <a name="microsoftmarketplace"></a>Microsoft. Market

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | sunar | Hayır |
> | offerTypes | Hayır |
> | offerTypes/yayımcılar | Hayır |
> | offerTypes/yayımcılar/teklifler | Hayır |
> | offerTypes/yayımcılar/teklifler/planlar | Hayır |
> | offerTypes/yayımcılar/teklifler/planlar/anlaşmalar | Hayır |
> | offerTypes/yayımcılar/teklifler/planlar/configs | Hayır |
> | offerTypes/yayımcılar/teklifler/planlar/configs/ımportımage | Hayır |
> | privategallergıtems | Hayır |
> | ürün | Hayır |
> | Publishers | Hayır |
> | Yayımcılar/teklifler | Hayır |
> | Yayımcılar/teklifler/Düzeltme | Hayır |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | classicDevServices | Yes |
> | updateCommunicationPreference | Hayır |

## <a name="microsoftmarketplaceordering"></a>Microsoft. Marketplacesıralaması

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | anlaşmalar | Hayır |
> | offertypes | Hayır |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | mediaservices | Yes |
> | mediaservices/accountFilters | Hayır |
> | mediaservices/varlıklar | Hayır |
> | mediaservices/varlıklar/assetFilters | Hayır |
> | mediaservices/contentKeyPolicies | Hayır |
> | mediaservices/eventGridFilters | Hayır |
> | mediaservices/liveEventOperations | Hayır |
> | mediaservices/liveEvents | Yes |
> | mediaservices/liveEvents/Liveçıktılar | Hayır |
> | mediaservices/liveOutputOperations | Hayır |
> | mediaservices/Mediagraf | Hayır |
> | mediaservices/streamingEndpointOperations | Hayır |
> | mediaservices/streamingEndpoints | Yes |
> | mediaservices/Streamingkonumlandırıcı | Hayır |
> | mediaservices/streamingPolicies | Hayır |
> | mediaservices/dönüşümler | Hayır |
> | mediaservices/dönüşümler/işler | Hayır |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Appkümeler | Yes |

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | assessmentProjects | Yes |
> | migrateprojects | Yes |
> | projeyle | Yes |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Yes |
> | objectUnderstandingAccounts | Yes |
> | remoteRenderingAccounts | Yes |
> | spatialAnchorsAccounts | Yes |
> | surfaceReconstructionAccounts | Yes |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | netAppAccounts | Yes |
> | netAppAccounts/backupPolicies | Yes |
> | netAppAccounts/Capacityhavuzları | Yes |
> | netAppAccounts/Capacityhavuzları/birimleri | Yes |
> | netAppAccounts/Capacityhavuzları/birimleri/yedeklemeleri | Hayır |
> | netAppAccounts/Capacityhavuzlar/Volumes/Mount hedefleri | Yes |
> | netAppAccounts/Capacityhavuzlar/birimler/anlık görüntüler | Yes |
> | netAppAccounts/Vaults | Hayır |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Applicationgateway 'ler | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Yes |
> | applicationSecurityGroups | Yes |
> | azureFirewallFqdnTags | Hayır |
> | azureFirewalls | Yes |
> | Savunma Konakları | Yes |
> | bgpServiceCommunities | Hayır |
> | bağlantının | Yes |
> | ddosCustomPolicies | Yes |
> | Ddosprotectionplanlar | Yes |
> | Dnsoperationdurumları | Hayır |
> | dnszones | Yes |
> | dnszones/A | Hayır |
> | dnszones/AAAA | Hayır |
> | dnszones/tümü | Hayır |
> | dnszones/CAA | Hayır |
> | dnszones/CNAME | Hayır |
> | dnszones/MX | Hayır |
> | dnszones/NS | Hayır |
> | dnszones/PTR | Hayır |
> | dnszones/kayıt kümeleri | Hayır |
> | dnszones/SOA | Hayır |
> | dnszones/SRV | Hayır |
> | dnszones/TXT | Hayır |
> | Expressroutedevreleri | Yes |
> | expressRouteCrossConnections | Yes |
> | Expressroutegateway 'ler | Yes |
> | expressRoutePorts | Yes |
> | expressRouteServiceProviders | Hayır |
> | firewallPolicies | Yes |
> | frontkapıların | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | Hayır |
> | frontdoorWebApplicationFirewallPolicies | Yes |
> | getDnsResourceReference | Hayır |
> | ınternalnotify | Hayır |
> | loadBalancers | Yes |
> | Localnetworkgateway 'ler | Yes |
> | Natgateway 'ler | Yes |
> | Networkıntpolicies Ilkeleri | Yes |
> | NetworkInterfaces | Yes |
> | networkProfiles | Yes |
> | networkSecurityGroups | Yes |
> | networkWatchers | Yes |
> | networkWatchers/Connectionmonitörleri | Yes |
> | networkWatchers/uzunluler | Yes |
> | networkWatchers/Pingkafesler | Yes |
> | p2sVpnGateways | Yes |
> | privateDnsOperationStatuses | Hayır |
> | privateDnsZones | Yes |
> | privateDnsZones/A | Hayır |
> | privateDnsZones/AAAA | Hayır |
> | privateDnsZones/tümü | Hayır |
> | privateDnsZones/CNAME | Hayır |
> | privateDnsZones/MX | Hayır |
> | privateDnsZones/PTR | Hayır |
> | privateDnsZones/SOA | Hayır |
> | privateDnsZones/SRV | Hayır |
> | privateDnsZones/TXT | Hayır |
> | privateDnsZones/virtualNetworkLinks | Yes |
> | privateEndpoints | Yes |
> | privateLinkServices | Yes |
> | Publicıpaddresses | Yes |
> | Publicıpöneklerini | Yes |
> | routeFilters | Yes |
> | routeTables | Yes |
> | serviceEndpointPolicies | Yes |
> | trafficManagerGeographicHierarchies | Hayır |
> | trafficmanagerprofiles | Yes |
> | trafficmanagerprofiles/heatMaps | Hayır |
> | trafficManagerUserMetricsKeys | Hayır |
> | Virtualhub 'Lar | Yes |
> | virtualNetworkGateways | Yes |
> | virtualNetworks | Yes |
> | virtualNetworkTaps | Yes |
> | Virtualwan | Yes |
> | Vpngateway 'ler | Yes |
> | vpnSites | Yes |
> | webApplicationFirewallPolicies | Yes |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | öznitelikleri | Yes |
> | ad alanları/Notificationhub 'Lar | Yes |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | osNamespaces | Yes |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Hiper sanal siteler | Yes |
> | Importsites | Yes |
> | Sunucusiteleri | Yes |
> | VMwareSites | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | leriniz | Yes |
> | cihazlar | Hayır |
> | Bağlantı hedefleri | Hayır |
> | Storageınsii configs | Hayır |
> | Onlarla | Yes |
> | çalışma alanları/veri kaynakları | Hayır |
> | çalışma alanları/linkedServices | Hayır |
> | çalışma alanları/sorgu | Hayır |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | managementassociations | Hayır |
> | managementconfigurations | Yes |
> | çözümler | Yes |
> | Görünümler | Yes |

## <a name="microsoftpeering"></a>Microsoft. eşleme

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Yasallıklar | Hayır |
> | peerAsns | Hayır |
> | eşlemeleri | Yes |
> | peeringServiceProviders | Hayır |
> | peeringServices | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft. Poliyelei

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Poliyevents | Hayır |
> | policyMetadata | Hayır |
> | policyStates | Hayır |
> | policyTrackedResources | Hayır |
> | düzeltmeler | Hayır |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | konsolları | Hayır |
> | panoların | Yes |
> | userSettings | Hayır |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | workspaceCollections | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | kapasiteler | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Backupkorunabilir | Hayır |
> | kasaları | Yes |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | öznitelikleri | Yes |
> | ad alanları/authorizationrules | Hayır |
> | ad alanları/hybridconnections | Hayır |
> | ad alanları/hybridconnections/authorizationrules | Hayır |
> | ad alanları/wcfreyerleştiri | Hayır |
> | ad alanları/wcfreyerleştirme/authorizationrules | Hayır |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Hayır |
> | koleksiyonlarıyla | Yes |
> | Koleksiyonlar/uygulamalar | Hayır |
> | Koleksiyonlar/SecurityPrincipals | Hayır |
> | Templateımages | Hayır |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | sorgu | Yes |
> | resourceChangeDetails | Hayır |
> | resourceChanges | Hayır |
> | resources | Hayır |
> | resourcesHistory | Hayır |
> | subscriptionsStatus | Hayır |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Kullanılabilirlik durumları | Hayır |
> | Childadvailabilitydurumlar | Hayır |
> | childResources | Hayır |
> | etkinlikler | Hayır |
> | ımpactedresources | Hayır |
> | meta veriler | Hayır |
> | bildirimi | Hayır |

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | dağıtımlar | Hayır |
> | dağıtımlar/işlemler | Hayır |
> | deploymentScripts | Yes |
> | deploymentScripts/Günlükler | Hayır |
> | Köprü | Hayır |
> | notifyResourceJobs | Hayır |
> | sağlayıcıları | Hayır |
> | resourceGroups | Hayır |
> | resources | Hayır |
> | Aboneliklerin | Hayır |
> | Abonelikler/sağlayıcılar | Hayır |
> | Abonelikler/resourceGroups | Hayır |
> | Abonelikler/ResourceGroups/kaynaklar | Hayır |
> | Abonelikler/kaynaklar | Hayır |
> | Abonelikler/etiket adları | Hayır |
> | Abonelikler/etiket adları/tagValues | Hayır |
> | Kira | Hayır |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | uygulamalar | Yes |
> | saasresources | Hayır |

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | işlere | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | resourceHealthMetadata | Hayır |
> | searchServices | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Hayır |
> | advancedThreatProtectionSettings | Hayır |
> | alerts | Hayır |
> | allowedConnections | Hayır |
> | Applicationwhitedökümler | Hayır |
> | assessmentMetadata | Hayır |
> | kopyalan | Hayır |
> | akışlarını otomatikleştirin | Yes |
> | Oto Provisioningsettings | Hayır |
> | Uyumluluklarına | Hayır |
> | dataCollectionAgents | Hayır |
> | deviceSecurityGroups | Hayır |
> | discoveredSecuritySolutions | Hayır |
> | externalSecuritySolutions | Hayır |
> | Informationprotectionpolicies | Hayır |
> | ıotsecuritysolutions | Yes |
> | ıotsecuritysolutions/analiz Ticsmodeller | Hayır |
> | ıotsecuritysolutions/Analticsmodeller/Aggreggıt uyarıları | Hayır |
> | ıotsecuritysolutions/analiz Ticsmodeller/Aggreg, öneriler | Hayır |
> | Jağaccesspolicies | Hayır |
> | networkData | Hayır |
> | playbookConfigurations | Yes |
> | Elerindeki | Hayır |
> | fiyatlandırmalar | Hayır |
> | Reve daha karmaşık bakım standartları | Hayır |
> | Rekontrol ve Re, | Hayır |
> | Rekontrol ve Re, Re, Re, | Hayır |
> | securityContacts | Hayır |
> | securitySolutions | Hayır |
> | securitySolutionsReferenceData | Hayır |
> | Securitydurumlardan | Hayır |
> | securityStatusesSummaries | Hayır |
> | Sunucukullanılabilirliği | Hayır |
> | ayarlar | Hayır |
> | subAssessments | Hayır |
> | Görevler | Hayır |
> | anlatır | Hayır |
> | çalışma alanı ayarları | Hayır |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | diagnosticSettings | Hayır |
> | diagnosticSettingsCategories | Hayır |

## <a name="microsoftsecurityinsights"></a>Microsoft. Securityınsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | toplamaları | Hayır |
> | alertRules | Hayır |
> | Alertrutatemplates | Hayır |
> | leriniz | Hayır |
> | çalışmaların | Hayır |
> | Veri bağlayıcıları | Hayır |
> | varlıklar | Hayır |
> | entityQueries | Hayır |
> | officeConsents | Hayır |
> | ayarlar | Hayır |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | öznitelikleri | Yes |
> | ad alanları/authorizationrules | Hayır |
> | ad alanları/diskalrecoveryconfigs | Hayır |
> | ad alanları/eventgridfilters | Hayır |
> | ad alanları/networkrulesets | Hayır |
> | ad alanları/kuyruklar | Hayır |
> | ad alanları/kuyruklar/authorizationrules | Hayır |
> | ad alanları/konular | Hayır |
> | ad alanları/konular/authorizationrules | Hayır |
> | ad alanları/konular/abonelikler | Hayır |
> | ad alanları/konular/abonelikler/kurallar | Hayır |
> | premiumMessagingRegions | Hayır |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | uygulamalar | Yes |
> | leriniz | Yes |
> | kümeler/uygulamalar | Hayır |
> | Kapsayıcı grupları | Yes |
> | containerGroupSets | Yes |
> | edgeclusters | Yes |
> | edgeclusters/uygulamalar | Hayır |
> | Mamak | Yes |
> | secretmağazaları | Yes |
> | secretmağazaları/sertifikaları | Hayır |
> | secretmağazaları/gizli dizileri | Hayır |
> | volumes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft. Servicefabrickafesi

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | uygulamalar | Yes |
> | Kapsayıcı grupları | Yes |
> | geçidinin | Yes |
> | Mamak | Yes |
> | kaynaklanır | Yes |
> | volumes | Yes |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Providerkayıtları | Hayır |
> | Providerkayıtlarıyla/resourceTypeRegistrations | Hayır |
> | piyasaya çıkarma | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | SignalR | Yes |
> | SignalR/eventGridFilters | Hayır |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | SiteRecoveryVault | Yes |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Hybriduseavantajlar | Hayır |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | applicationDefinitions | Yes |
> | uygulamalar | Yes |
> | Jistekleri | Yes |

## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | ManagedInstances | Yes |
> | ManagedInstances/veritabanları | Yes |
> | ManagedInstances/veritabanları/backupShortTermRetentionPolicies | Hayır |
> | ManagedInstances/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels | Hayır |
> | ManagedInstances/veritabanları/ | Hayır |
> | ManagedInstances/veritabanları/ek | Hayır |
> | ManagedInstances/encryptionProtector | Hayır |
> | ManagedInstances/anahtarlar | Hayır |
> | ManagedInstances/Restokbledroppeddatabases/backupShortTermRetentionPolicies | Hayır |
> | ManagedInstances/ | Hayır |
> | sunucular | Yes |
> | sunucular/Yöneticiler | Hayır |
> | sunucular/communicationLinks | Hayır |
> | sunucular/veritabanları | Yes |
> | sunucular/encryptionProtector | Hayır |
> | sunucular/firewallRules | Hayır |
> | sunucular/anahtarlar | Hayır |
> | sunucular/Restokbledroppeddatabases | Hayır |
> | Sunucu/hizmet hedefleri | Hayır |
> | sunucular/tdeCertificates | Hayır |
> | Virtualkümeler | Hayır |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Yes |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Hayır |
> | SqlVirtualMachines | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | storageAccounts | Yes |
> | storageAccounts/blobServices | Hayır |
> | storageAccounts/fileServices | Hayır |
> | storageAccounts/queueServices | Hayır |
> | storageAccounts/Services | Hayır |
> | storageAccounts/Services/metricDefinitions | Hayır |
> | storageAccounts/tableServices | Hayır |
> | vardır | Hayır |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | önbelleklerinde | Yes |
> | önbellekler/Storagetaral | Hayır |
> | Usagemodeller | Hayır |

## <a name="microsoftstoragereplication"></a>Microsoft. Storagerepce

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | replicationGroups | Hayır |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | Hayır |
> | storageSyncServices/syncGroups | Hayır |
> | storageSyncServices/syncGroups/cloudEndpoints | Hayır |
> | storageSyncServices/syncGroups/serverEndpoints | Hayır |
> | storageSyncServices/iş akışları | Hayır |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | Hayır |
> | storageSyncServices/syncGroups | Hayır |
> | storageSyncServices/syncGroups/cloudEndpoints | Hayır |
> | storageSyncServices/syncGroups/serverEndpoints | Hayır |
> | storageSyncServices/iş akışları | Hayır |

## <a name="microsoftstoragesyncint"></a>Microsoft. Storagesyncınt

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | Hayır |
> | storageSyncServices/syncGroups | Hayır |
> | storageSyncServices/syncGroups/cloudEndpoints | Hayır |
> | storageSyncServices/syncGroups/serverEndpoints | Hayır |
> | storageSyncServices/iş akışları | Hayır |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | ilerinde | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | streammingjobs | Yes |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | İptal | Hayır |
> | CreateSubscription | Hayır |
> | etkinleştirebilir | Hayır |
> | Yeniden Adlandır | Hayır |
> | SubscriptionDefinitions | Hayır |
> | SubscriptionOperations | Hayır |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Lý | Yes |
> | ortamlar/accessPolicies | Hayır |
> | ortamlar/EventSources | Yes |
> | ortamlar/Referencedataset 'ler | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Vmwarechoparlör basit

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | ayrılmış Cloudnodes | Yes |
> | ayrılmış CloudService | Yes |
> | virtualMachines | Yes |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | apiManagementAccounts | Hayır |
> | apiManagementAccounts/Apiacl 'Ler | Hayır |
> | apiManagementAccounts/API 'ler | Hayır |
> | apiManagementAccounts/API/Apiacl 'Ler | Hayır |
> | apiManagementAccounts/API/Connectionacl 'Ler | Hayır |
> | apiManagementAccounts/API/bağlantı | Hayır |
> | apiManagementAccounts/API/Connections/Connectionacl 'Ler | Hayır |
> | apiManagementAccounts/API/localizedDefinitions | Hayır |
> | apiManagementAccounts/Connectionacl 'Ler | Hayır |
> | apiManagementAccounts/bağlantılar | Hayır |
> | billingMeters | Hayır |
> | Sertifika | Yes |
> | Connectiongateway 'ler | Yes |
> | bağlantının | Yes |
> | Customapsıs | Yes |
> | Silinmi siteleri | Hayır |
> | işlevleri | Hayır |
> | hostingEnvironments | Yes |
> | hostingEnvironments/multiRolePools | Hayır |
> | hostingEnvironments/workerPools | Hayır |
> | publishingUsers | Hayır |
> | Önerileri | Hayır |
> | resourceHealthMetadata | Hayır |
> | zamanları | Hayır |
> | serverFarms | Yes |
> | Sunucugrupları/eventGridFilters | Hayır |
> | barındıra | Yes |
> | siteler/yapılandırma  | Hayır |
> | siteler/eventGridFilters | Hayır |
> | siteler/hostNameBindings | Hayır |
> | Sites/networkConfig | Hayır |
> | siteler/premieraddons | Yes |
> | siteler/yuvalar | Yes |
> | siteler/yuvalar/eventGridFilters | Hayır |
> | siteler/yuvalar/hostNameBindings | Hayır |
> | siteler/yuvalar/networkConfig | Hayır |
> | sourceControls | Hayır |
> | doğrulamalısınız | Hayır |
> | verifyHostingEnvironmentVnet | Hayır |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. Windowssavunma Deratp

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | diagnosticSettings | Hayır |
> | diagnosticSettingsCategories | Hayır |

## <a name="microsoftwindowsiot"></a>Microsoft. Windowsıot

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | DeviceServices | Yes |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | bileşenleri | Hayır |
> | componentsSummary | Hayır |
> | Izleme örnekleri | Hayır |
> | Izleme ınstancessummary | Hayır |
> | monitörün | Hayır |
> | notificationSettings | Hayır |

## <a name="next-steps"></a>Sonraki adımlar

Aynı verileri bir virgülle ayrılmış değerler dosyası ile almak için, [Complete-Mode-Deletion. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)dosyasını indirin.
