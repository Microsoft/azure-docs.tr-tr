---
title: Tam modda silme
description: Azure Resource Manager şablonlarda kaynak türlerinin tamamlanma modu silme işlemini nasıl işleyeceğini gösterir.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 6986f600274beaaa67f2f6ce64cbc3d0ceaf322e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576082"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Tüm mod dağıtımları için Azure kaynaklarını silme

Bu makalede, kaynak türlerinin, tamamlanmış modda dağıtılan bir şablonda olmadığında silinme işleminin nasıl işleneceği açıklanır.

Türü, tamamlanmış modla dağıtılan şablonda olmadığında, **Evet** ile işaretlenen kaynak türleri silinir.

**Hayır** ile işaretlenen kaynak türleri, şablonda olmadığında otomatik olarak silinmez; Ancak, üst kaynak silinirse bunlar silinir. Davranışın tam açıklaması için bkz. [Azure Resource Manager Dağıtım modları](deployment-modes.md).

[Bir şablonda birden fazla kaynak grubuna](./deploy-to-resource-group.md)dağıtırsanız, dağıtım işleminde belirtilen kaynak grubundaki kaynaklar silinebilir. İkincil kaynak gruplarındaki kaynaklar silinmez.

Kaynaklar, kaynak sağlayıcısı ad alanı tarafından listelenir. Kaynak sağlayıcısı ad alanını Azure hizmet adı ile eşleştirmek için bkz. [Azure hizmetleri Için kaynak sağlayıcıları](../management/azure-services-resource-providers.md).

> [!NOTE]
> Bir şablonu tam modda dağıtmadan önce her zaman [ne yapılır işlemini](template-deploy-what-if.md) kullanın. Ne yapılır, hangi kaynakların oluşturulacağını, silineceğini veya değiştirildiğini gösterir. Kaynakları istenmeden silmeyi önlemek için ne yapılacağını kullanın.

Kaynak sağlayıcısı ad alanına atlayın:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AG, Dplatform](#microsoftagfoodplatform)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. AnyBuild](#microsoftanybuild)
> - [Microsoft. Apimanane](#microsoftapimanagement)
> - [Microsoft. AppAssessment](#microsoftappassessment)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. kanıtlama](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. oto Yönet](#microsoftautomanage)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AVS](#microsoftavs)
> - [Microsoft. Azure. Genfiliz](#microsoftazuregeneva)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureArcData](#microsoftazurearcdata)
> - [Microsoft. Azurecdir](#microsoftazurecis)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureSphere](#microsoftazuresphere)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. Azurestackhcı](#microsoftazurestackhci)
> - [Microsoft. BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. Faturalandırma](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. Blockzinciri](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. Cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. Cascade](#microsoftcascade)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. ClusterStor](#microsoftclusterstor)
> - [Microsoft. Codespaces](#microsoftcodespaces)
> - [Microsoft. Biliveservices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. ConnectedCache](#microsoftconnectedcache)
> - [Microsoft. Connectedaraç](#microsoftconnectedvehicle)
> - [Microsoft. ConnectedVMwarevSphere](#microsoftconnectedvmwarevsphere)
> - [Microsoft. tüketim](#microsoftconsumption)
> - [Microsoft. Containerınstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. Customerkasası](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. Databricks](#microsoftdatabricks)
> - [Microsoft. DataCatalog](#microsoftdatacatalog)
> - [Microsoft. DataFactory](#microsoftdatafactory)
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
> - [Microsoft. DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft. EdgeOrder](#microsoftedgeorder)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. deneme](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. Features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft. Genomiks](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthBot](#microsofthealthbot)
> - [Microsoft. Healthgelişme API 'leri](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ımportexport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft. ıotcentral](#microsoftiotcentral)
> - [Microsoft. ıotsecurity](#microsoftiotsecurity)
> - [Microsoft. ıotspaces](#microsoftiotspaces)
> - [Microsoft. Keykasası](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. Machinöğrenim](#microsoftmachinelearning)
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
> - [Microsoft. MobileNetwork](#microsoftmobilenetwork)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. Not defterleri](#microsoftnotebooks)
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
> - [Microsoft. PowerPlatform](#microsoftpowerplatform)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft. purview](#microsoftpurview)
> - [Microsoft. hisse](#microsoftquantum)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceConnector](#microsoftresourceconnector)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. ScVmm](#microsoftscvmm)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. Securityınsights](#microsoftsecurityinsights)
> - [Microsoft. SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. Servicefabrickafesi](#microsoftservicefabricmesh)
> - [Microsoft. ServiceLinker](#microsoftservicelinker)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. Singular](#microsoftsingularity)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. Storagerepce](#microsoftstoragereplication)
> - [Microsoft. Storagessync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. Storagesyncınt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. SYNAPSE](#microsoftsynapse)
> - [Microsoft. Timeseriesınsights](#microsofttimeseriesinsights)
> - [Microsoft. Token](#microsofttoken)
> - [Microsoft. Virtualmachineımages](#microsoftvirtualmachineimages)
> - [Microsoft. VMware](#microsoftvmware)
> - [Microsoft. Vmwarechoparlör basit](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. Windowssavunma Deratp](#microsoftwindowsdefenderatp)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. Windowsıot](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | DomainServices | Yes |
> | DomainServices/oucontainer | No |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Destek sağlayıcıları | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | aadsupportcases | No |
> | addsservices | No |
> | aracısını | No |
> | anonymousapiusers | No |
> | yapılandırma | No |
> | günlükler | No |
> | reports | No |
> | servicehealthölçümleri | No |
> | services | No |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Danışmanlaştırıp | No |
> | konfigürasyonları | No |
> | Generatereyorumgeçişleri | No |
> | meta veriler | No |
> | Öneriler | No |
> | gizlemeleri | No |

## <a name="microsoftagfoodplatform"></a>Microsoft. AG, Dplatform

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Farmtları | Yes |
> | Farmtts/eventGridFilters | No |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | actionRules | Yes |
> | alerts | No |
> | alertsList | No |
> | alertsMetaData | No |
> | alertsSummary | No |
> | alertsSummaryList | No |
> | migrateFromSmartDetection | No |
> | resourceHealthAlertRules | Yes |
> | smartDetectorAlertRules | Yes |
> | smartGroups | No |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | larý | Yes |

## <a name="microsoftanybuild"></a>Microsoft. AnyBuild

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | leriniz | Yes |

## <a name="microsoftapimanagement"></a>Microsoft. Apimanane

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Silinmi Hizmetleri | No |
> | Getdomainownershipıdentifier | No |
> | reportFeedback | No |
> | hizmet | Yes |
> | validateServiceName | No |

## <a name="microsoftappassessment"></a>Microsoft. AppAssessment

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | migrateProjects | Yes |
> | migrateProjects/değerlendirmeler | No |
> | migrateProjects/değerlendirmeler/assessedApplications | No |
> | migrateProjects/değerlendirmeler/assessedApplications/makineler | No |
> | migrateProjects/değerlendirmeler/assessedMachines | No |
> | migrateProjects/değerlendirmeler/assessedMachines/uygulamalar | No |
> | migrateProjects/değerlendirmeler/, Inestodeğerlendir | No |
> | migrateProjects/Sites | No |
> | migrateProjects/Sites/applianceConfigurations | No |
> | Migrateprojeler/siteler/makineler | No |
> | osVersions | No |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Configurationmağazaların | Yes |
> | Configurationmağazaların/eventGridFilters | No |
> | Configurationmağazaların/keyValues | No |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Spring | Yes |
> | Yay/uygulamalar | No |
> | Yay/uygulamalar/dağıtımlar | No |

## <a name="microsoftattestation"></a>Microsoft. kanıtlama

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | attestationProviders | Yes |
> | defaultProviders | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Accessbelgeno Scheduledefinitions | No |
> | Accessbelgeayarlarý Schedulesettings | No |
> | classicAdministrators | No |
> | Datatakma adlar | No |
> | Datapolicybildirimleri | No |
> | Denyasatamaları | No |
> | Erişimi yükseltme | No |
> | Findorphanroleatamalar | No |
> | kaynaktaki | No |
> | izinler | No |
> | Poliyasatamaları | No |
> | policyDefinitions | No |
> | Policymuafiyet | No |
> | policySetDefinitions | No |
> | privateLinkAssociations | No |
> | providerOperations | No |
> | resourceManagementPrivateLinks | Yes |
> | Roleatamamentapları | No |
> | roleAssignments | No |
> | Roleatamazamanlama örnekleri | No |
> | roleAssignmentScheduleRequests | No |
> | Roleatamazamanlamaları | No |
> | Roleatamasussusageölçümleri | No |
> | roleDefinitions | No |
> | Roleeligibilityscheduleınstances | No |
> | roleEligibilityScheduleRequests | No |
> | Roleeligibilityzamanlamalar | No |
> | roleManagementPolicies | No |
> | Rolemanagementpolicyasatamaları | No |

## <a name="microsoftautomanage"></a>Microsoft. oto Yönet

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |
> | Configurationprofileatamalar | No |
> | configurationProfilePreferences | Yes |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | automationAccounts | Yes |
> | automationAccounts/Configurations | Yes |
> | automationAccounts/Jobs | No |
> | automationAccounts/Privateendpointconnectionproxy 'Leri | No |
> | automationAccounts/privateEndpointConnections | No |
> | automationAccounts/privateLinkResources | No |
> | automationAccounts/runbook 'lar | Yes |
> | automationAccounts/softwareUpdateConfigurations | No |
> | automationAccounts/Web kancaları | No |

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Privatebulutlar | Yes |
> | Privatebulutlar/eklentiler | No |
> | Privatebulutlar/yetkilendirmeler | No |
> | Privatebulutlar/cloudLinks | No |
> | Privatebulutlar/kümeler | No |
> | Privatebulutlar/kümeler/veri depoları | No |
> | Privatebulutlar/globalReachConnections | No |
> | Privatebulutlar/hcxEnterpriseSites | No |
> | Privatebulutlar/scriptExecutions | No |
> | Privatebulutlar/scriptPackages | No |
> | Privatebulutlar/scriptPackages/Scriptcmdlet 'leri | No |
> | Privatebulutlar/workloadNetworks | No |
> | Privatebulutlar/workloadNetworks/dhcpConfigurations | No |
> | Privatebulutlar/workloadNetworks/dnsServices | No |
> | Privatebulutlar/workloadNetworks/dnsZones | No |
> | Privatebulutlar/workloadNetworks/Gateway | No |
> | Privatebulutlar/workloadNetworks/portMirroringProfiles | No |
> | Privatebulutlar/workloadNetworks/segment | No |
> | Privatebulutlar/workloadNetworks/virtualMachines | No |
> | Privatebulutlar/workloadNetworks/vmGroups | No |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Genfiliz

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | lý | No |
> | ortamlar/hesaplar | No |
> | ortamlar/hesaplar/ad alanları | No |
> | ortamlar/hesaplar/ad alanları/yapılandırma | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | b2cDirectories | Yes |
> | b2ctenants | No |
> | Guestkullanımlar | Yes |

## <a name="microsoftazurearcdata"></a>Microsoft. AzureArcData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Veri denetleyicileri | Yes |
> | Datawarehouseınstances | Yes |
> | Postgresınstances | Yes |
> | Sqlmanagedınstances | Yes |
> | Sqlserverınstances | Yes |

## <a name="microsoftazurecis"></a>Microsoft. Azurecdir

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | autopilotEnvironments | Yes |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Sqlserverkayıtları | Yes |
> | Sqlserverkayıtları/sqlServers | No |

## <a name="microsoftazuresphere"></a>Microsoft. AzureSphere

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | larına | Yes |
> | kataloglar/ürünler | Yes |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | cloudManifestFiles | No |
> | edgeSubscriptions | Yes |
> | Linkedabonelikleri | Yes |
> | kayıtlarında | Yes |
> | kayıt/müşteri abonelikleri | No |
> | kayıtlar/ürünler | No |

## <a name="microsoftazurestackhci"></a>Microsoft. Azurestackhcı

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | leriniz | Yes |
> | Gallerımages | Yes |
> | NetworkInterfaces | Yes |
> | virtualHardDisks | Yes |
> | virtualMachines | Yes |
> | virtualNetworks | Yes |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft. BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | bareMetalInstances | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | batchAccounts | Yes |
> | batchAccounts/sertifikalar | No |
> | batchAccounts/havuzlar | No |

## <a name="microsoftbilling"></a>Microsoft. Faturalandırma

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | billingAccounts | No |
> | billingAccounts/anlaşmalar | No |
> | billingAccounts/billingPermissions | No |
> | billingAccounts/billingProfiles | No |
> | billingAccounts/Billingprofiller/billingPermissions | No |
> | billingAccounts/billingProfiles/Billingroleatamaları | No |
> | billingAccounts/billingProfiles/billingRoleDefinitions | No |
> | billingAccounts/billingProfiles/Billingabonelikleri | No |
> | billingAccounts/billingProfiles/Createbillingroleatama | No |
> | billingAccounts/billingProfiles/müşteriler | No |
> | billingAccounts/billingProfiles/yönergeler | No |
> | billingAccounts/billingProfiles/faturalar | No |
> | billingAccounts/billingProfiles/faturalar/fiyat listesi | No |
> | billingAccounts/billingProfiles/faturalar/işlemler | No |
> | billingAccounts/billingProfiles/ınvoicesections | No |
> | billingAccounts/billingProfiles/ınvoicesections/billingPermissions | No |
> | billingAccounts/billingProfiles/ınvoicesections/Billingroleatamaları | No |
> | billingAccounts/billingProfiles/ınvoicesections/billingRoleDefinitions | No |
> | billingAccounts/billingProfiles/ınvoicesections/Billingabonelikleri | No |
> | billingAccounts/billingProfiles/ınvoicesections/Createbillingroleatama | No |
> | billingAccounts/billingProfiles/ınvoicesections/ınitiatetransfer | No |
> | billingAccounts/billingProfiles/ınvoicesections/ürünler | No |
> | billingAccounts/billingProfiles/ınvoicesections/ürünler/aktarım | No |
> | billingAccounts/billingProfiles/ınvoicesections/ürünler/updateAutoRenew | No |
> | billingAccounts/billingProfiles/ınvoicesections/işlemler | No |
> | billingAccounts/billingProfiles/ınvoicesections/aktarımlar | No |
> | billingAccounts/billingProfiles/ınvoicesections/Validatedeleteınvoicesectionuygunluğu | No |
> | billingAccounts/BillingProfiles/patchOperations | No |
> | billingAccounts/billingProfiles/paymentMethods | No |
> | billingAccounts/billingProfiles/ilkeler | No |
> | billingAccounts/billingProfiles/fiyat listesi | No |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | No |
> | billingAccounts/billingProfiles/ürünler | No |
> | billingAccounts/billingProfiles/rezervasyonlar | No |
> | billingAccounts/billingProfiles/işlemler | No |
> | billingAccounts/billingProfiles/Validatedeletebillingprofileuygunluğu | No |
> | billingAccounts/billingProfiles/validateDetachPaymentMethodEligibility | No |
> | billingAccounts/Billingroleatamaları | No |
> | billingAccounts/billingRoleDefinitions | No |
> | billingAccounts/Billingabonelikleri | No |
> | billingAccounts/Billingabonelikleri/yükseltme Terole | No |
> | billingAccounts/Billingabonelikleri/faturalar | No |
> | billingAccounts/Createbillingroleatama | No |
> | billingAccounts/Createınvoicesectionoperations | No |
> | billingAccounts/müşteriler | No |
> | billingAccounts/müşteriler/billingPermissions | No |
> | billingAccounts/müşteriler/Billingabonelikleri | No |
> | billingAccounts/müşteriler/ınitiatetransfer | No |
> | billingAccounts/müşteriler/ilkeler | No |
> | billingAccounts/müşteriler/ürünler | No |
> | billingAccounts/müşteriler/işlemler | No |
> | billingAccounts/müşteriler/aktarımlar | No |
> | billingAccounts/departmanlar | No |
> | billingAccounts/departmanlar/billingPermissions | No |
> | billingAccounts/departmanlar/Billingroleatamaları | No |
> | billingAccounts/departmanlar/billingRoleDefinitions | No |
> | billingAccounts/departmanlar/Billingabonelikleri | No |
> | billingAccounts/KayıtSayısı | No |
> | billingAccounts/Kayıthesapsayısı/billingPermissions | No |
> | billingAccounts/KayıtSayısı/Billingroleatamaları | No |
> | billingAccounts/KayıtSayısı/billingRoleDefinitions | No |
> | billingAccounts/Kayıthesapsayısı/Billingabonelikleri | No |
> | billingAccounts/faturalar | No |
> | billingAccounts/faturalar/işlemler | No |
> | billingAccounts/faturalar/transactionSummary | No |
> | billingAccounts/ınvoicesections | No |
> | billingAccounts/ınvoicesections/billingSubscriptionMoveOperations | No |
> | billingAccounts/ınvoicesections/Billingabonelikleri | No |
> | billingAccounts/ınvoicesections/Billingabonelikleri/aktarımı | No |
> | billingAccounts/ınvoicesections/yükselt | No |
> | billingAccounts/ınvoicesections/ınitiatetransfer | No |
> | billingAccounts/ınvoicesections/patchOperations | No |
> | billingAccounts/ınvoicesections/productMoveOperations | No |
> | billingAccounts/Ürünler/Ürünler | No |
> | billingAccounts/ınvoicesections/ürünler/transfer | No |
> | billingAccounts/ınvoicesections/ürünler/updateAutoRenew | No |
> | billingAccounts/ınvoicesections/işlemler | No |
> | billingAccounts/ınvoicesections/aktarımlar | No |
> | billingAccounts/Lineofkredisi | No |
> | billingAccounts/patchOperations | No |
> | billingAccounts/payableOverage | No |
> | billingAccounts/paymentMethods | No |
> | billingAccounts/payan | No |
> | billingAccounts/ürünler | No |
> | billingAccounts/rezervasyonlar | No |
> | billingAccounts/işlemler | No |
> | Billingdönemler | No |
> | billingPermissions | No |
> | billingProperty | No |
> | Billingroleatamaları | No |
> | billingRoleDefinitions | No |
> | Createbillingroleatama | No |
> | bölümlerinin | No |
> | kayıt sayısı | No |
> | faturalardan | No |
> | ter | No |
> | girişinde | No |
> | aktarımlar/acceptTransfer | No |
> | aktarımlar/declineTransfer | No |
> | aktarımlar/operationStatus | No |
> | aktarımlar/validateTransfer | No |
> | validateAddress | No |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Mapapsıs | Yes |
> | updateCommunicationPreference | No |

## <a name="microsoftblockchain"></a>Microsoft. Blockzinciri

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | blockchainMembers | Yes |
> | Cordadmembers | Yes |
> | izleyicileri | Yes |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | TokenServices | Yes |
> | TokenServices/BlockchainNetworks | No |
> | TokenServices/Groups | No |
> | TokenServices/gruplar/hesaplar | No |
> | TokenServices/TokenTemplates | No |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Şema tasmi | No |
> | Blueprintasbir/Atamaperations | No |
> | Blueprintasbir/işlemleri | No |
> | Blueprint | No |
> | planlar/yapıtlar | No |
> | planlar/sürümler | No |
> | planlar/sürümler/yapılar | No |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | botServices | Yes |
> | botServices/kanallar | No |
> | botServices/Connections | No |
> | hostSettings | No |
> | diller | No |
> | templates | No |

## <a name="microsoftcache"></a>Microsoft. Cache

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Redis | Yes |
> | Redsıs/EventGridFilters | No |
> | Redsıs/Privateendpointconnectionproxy 'Leri | No |
> | Redsıs/Privateendpointconnectionproxy/doğrulama | No |
> | Redsıs/privateEndpointConnections | No |
> | Redsıs/privateLinkResources | No |
> | redisEnterprise | Yes |
> | redisEnterprise/veritabanları | No |
> | RedisEnterprise/Privateendpointconnectionproxy 'Leri | No |
> | RedisEnterprise/Privateendpointconnectionproxy 'Leri/doğrulama | No |
> | RedisEnterprise/privateEndpointConnections | No |
> | RedisEnterprise/privateLinkResources | No |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | appliedReservations | No |
> | Oto Kotaartışı | No |
> | calculateExchange | No |
> | calculatePrice | No |
> | calculatePurchasePrice | No |
> | larına | No |
> | Ticari Vaalrezervler | No |
> | değişimi | No |
> | Ownrezervasyonlarını | No |
> | placePurchaseOrder | No |
> | Rezervler | No |
> | Rezervler/Hesaplaizterefund | No |
> | Rezervler/Birleştir | No |
> | Rezervler/rezervasyonlar | No |
> | Rezervler/rezervasyonlar/düzeltmeler | No |
> | Rezervler/geri dönüş | No |
> | Rezervler/Böl | No |
> | Rezervler/takas | No |
> | oluşturamaz | No |
> | resourceProviders | No |
> | kaynaklar | No |
> | validateReservationOrder | No |

## <a name="microsoftcascade"></a>Microsoft. Cascade

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Siteler | Yes |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |
> | CdnWebApplicationFirewallPolicies | Yes |
> | edgenodes | No |
> | lerinize | Yes |
> | Profiller/afırdpoints | Yes |
> | Profiller/Affe/rotalar | No |
> | Profiller/customdomains | No |
> | Profiller/uç noktalar | Yes |
> | Profiller/uç noktalar/customdomains | No |
> | Profiller/uç noktalar/origingroups | No |
> | Profiller/uç noktalar/kaynaklar | No |
> | Profiller/origingroups | No |
> | Profiller/origingroups/kaynaklar | No |
> | Profiller/RuleSets | No |
> | Profiller/RuleSets/Rules | No |
> | Profiller/gizlilikler | No |
> | Profiller/securitypolicies | No |
> | Validatearaştırması | No |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Sertifikadüzenleri | Yes |
> | certificateOrders/Certificates | No |
> | Validatecertificateregistrationınformation | No |

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | değişiklikler | No |
> | profil | No |
> | resourceChanges | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | özellikler | No |
> | domainNames | Yes |
> | domainNames/yetenekleri | No |
> | domainNames/internalLoadBalancers | No |
> | domainNames/serviceCertificates | No |
> | domainNames/Yuvaları | No |
> | domainNames/yuvalar/roller | No |
> | domainNames/yuvalar/roller/metricDefinitions | No |
> | domainNames/yuvalar/roller/ölçümler | No |
> | moveSubscriptionResources | No |
> | operatingSystemFamilies | No |
> | operatingSystems | No |
> | quotas | No |
> | resourceTypes | No |
> | Validatesubscriptionmoveavaılabılıty | No |
> | virtualMachines | Yes |
> | virtualMachines/diagnosticSettings | No |
> | virtualMachines/metricDefinitions | No |
> | virtualMachines/ölçümler | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | classicInfrastructureResources | No |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | özellikler | No |
> | expressRouteCrossConnections | No |
> | expressRouteCrossConnections/peerler | No |
> | gatewaySupportedDevices | No |
> | networkSecurityGroups | Yes |
> | quotas | No |
> | Rezervler | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/Remotevirtualnetworkpeeringproxy 'Leri | No |
> | virtualNetworks/Virtualnetworkpeerler | No |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | özellikler | No |
> | disklerinden | No |
> | images | No |
> | osImages | No |
> | Osplatformımages | No |
> | Publicımages | No |
> | quotas | No |
> | storageAccounts | Yes |
> | storageAccounts/blobServices | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/metricDefinitions | No |
> | storageAccounts/ölçümler | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/Services | No |
> | storageAccounts/Services/diagnosticSettings | No |
> | storageAccounts/Services/metricDefinitions | No |
> | storageAccounts/Services/ölçümler | No |
> | storageAccounts/tableServices | No |
> | storageAccounts/Vmımages | No |
> | Vmımages | No |

## <a name="microsoftclusterstor"></a>Microsoft. ClusterStor

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | düğümlerini | Yes |

## <a name="microsoftcodespaces"></a>Microsoft. Codespaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Planlama | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftcognitiveservices"></a>Microsoft. Biliveservices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |
> | hesaplar/Privateendpointconnectionproxy 'Leri | No |
> | hesaplar/privateEndpointConnections | No |
> | hesaplar/privateLinkResources | No |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | RateCard | No |
> | Usagetoplamaları | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | availabilitySets | Yes |
> | cloudServices | Yes |
> | cloudServices/NetworkInterfaces | No |
> | cloudServices/Publicıpaddresses | No |
> | cloudServices/Roleınstances | No |
> | cloudServices/Roleınstances/NetworkInterfaces | No |
> | cloudServices/roller | No |
> | Diskeriþler | Yes |
> | diskEncryptionSets | Yes |
> | disklerinden | Yes |
> | Galeriler | Yes |
> | Galeriler/uygulamalar | No |
> | Galeriler/uygulamalar/sürümler | No |
> | Galeriler/görüntüler | Yes |
> | Galeriler/resimler/sürümler | Yes |
> | hostGroups | Yes |
> | hostGroups/konaklar | Yes |
> | images | Yes |
> | proximityPlacementGroups | Yes |
> | restorePointCollections | Yes |
> | restorePointCollections/restorePoints | No |
> | sharedVMExtensions | Yes |
> | sharedVMExtensions/sürümler | No |
> | Sharedvmımages | Yes |
> | Sharedvmımages/sürümler | No |
> | anlık görüntüler | Yes |
> | sshPublicKeys | Yes |
> | virtualMachines | Yes |
> | virtualMachines/uzantıları | Yes |
> | virtualMachines/metricDefinitions | No |
> | virtualMachines/runCommands | Yes |
> | virtualMachineScaleSets | Yes |
> | virtualMachineScaleSets/uzantılar | No |
> | virtualMachineScaleSets/NetworkInterfaces | No |
> | virtualMachineScaleSets/Publicıpaddresses | No |
> | virtualMachineScaleSets/virtualMachines | No |
> | virtualMachineScaleSets/virtualMachines/NetworkInterfaces | No |

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Önbellekenodes | Yes |

## <a name="microsoftconnectedvehicle"></a>Microsoft. Connectedaraç

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | platformAccounts | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft. ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | ResourcePools | Yes |
> | VCenter | Yes |
> | VCenters/ınventoryıtems | No |
> | VirtualMachines | Yes |
> | VirtualMachines/uzantıları | Yes |
> | VirtualMachines/GuestAgents | No |
> | VirtualMachines/Hybridıdentitymetadata | No |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |

## <a name="microsoftconsumption"></a>Microsoft. tüketim

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Aggregmalyt maliyeti | No |
> | Bakiyeler | No |
> | Bütçeler | No |
> | Ücretler | No |
> | CostTags | No |
> | iler | No |
> | etkinlikler | No |
> | Tahminler | No |
> | oluş | No |
> | Marketlerinden | No |
> | Fiyat listeleri | No |
> | ürün | No |
> | Rezervde ayrıntıları | No |
> | ReservationRecommendationDetails | No |
> | Rezervationönerilere | No |
> | Rezervlerin Özeti | No |
> | Rezervlik Işlemleri | Hayır |
> | Etiketler | Hayır |
> | Kira | No |
> | Terimler | No |
> | UsageDetails | No |

## <a name="microsoftcontainerinstance"></a>Microsoft. Containerınstance

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Kapsayıcı grupları | Yes |
> | serviceAssociationLinks | No |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | kayıt | Yes |
> | kayıt defterleri/agentPools | Yes |
> | kayıt defterleri/derlemeler | No |
> | kayıt defterleri/derlemeler/iptal | No |
> | kayıt defterleri/derlemeler/getLogLink | No |
> | kayıt defterleri/buildTasks | Yes |
> | kayıt defterleri/buildTasks/Steps | No |
> | kayıt defterleri/Connectedregistry | No |
> | kayıt defterleri/Connectedkayıt defterleri/devre dışı bırakma | No |
> | kayıt defterleri/eventGridFilters | No |
> | kayıt defterleri/Exporthatlarının | No |
> | kayıt defterleri/generateCredentials | No |
> | kayıt defterleri/getBuildSourceUploadUrl 'Si | No |
> | kayıt defterleri/GetCredentials | No |
> | kayıt defterleri/ımportımage | No |
> | kayıt defterleri/ımporthatlarının | No |
> | kayıt defterleri/ardışık düzen eylemsizlik | No |
> | kayıt defterleri/Privateendpointconnectionproxy 'Leri | No |
> | kayıt defterleri/Privateendpointconnectionproxy/doğrulama | No |
> | kayıt defterleri/privateEndpointConnections | No |
> | kayıt defterleri/privateLinkResources | No |
> | kayıt defterleri/queueBuild | No |
> | kayıt defterleri/regenerateCredential | No |
> | kayıt defterleri/regenerateCredentials | No |
> | kayıt defterleri/çoğaltmalar | Yes |
> | kayıt defterleri/çalıştırmalar | No |
> | kayıt defterleri/çalıştırmalar/iptal | No |
> | kayıt defterleri/scheduleRun | No |
> | kayıt defterleri/Kapsameşlemler | No |
> | kayıt defterleri/Taskçalıştırmaları | No |
> | kayıt defterleri/görevler | Yes |
> | kayıt defterleri/belirteçler | No |
> | kayıt defterleri/updatePolicies | No |
> | kayıt defterleri/Web kancaları | Yes |
> | kayıt defterleri/Web kancaları/getCallbackConfig | No |
> | kayıt defterleri/Web kancaları/ping | No |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | containerServices | Yes |
> | Managedkümeler | Yes |
> | Managedkümeler/eventGridFilters | No |
> | openShiftManagedClusters | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Uyarılar | No |
> | BillingAccounts | No |
> | Bütçeler | No |
> | Cloudbağlayıcıları | No |
> | Bağlayıcılar | Yes |
> | costAllocationRules | No |
> | Departmanlar | No |
> | Boyutlar | No |
> | Kayıt sayısı | No |
> | Dışarı Aktarmalar | No |
> | ExternalBillingAccounts | No |
> | ExternalBillingAccounts/uyarılar | No |
> | ExternalBillingAccounts/Boyutlar | No |
> | ExternalBillingAccounts/tahmin | No |
> | ExternalBillingAccounts/sorgu | No |
> | Externalabonelikleri | No |
> | Externalabonelikleri/uyarıları | No |
> | Externalabonelikler/Boyutlar | No |
> | Externalabonelikler/tahmin | No |
> | Externalabonelikler/sorgu | No |
> | Fetchfiyatlarını | No |
> | Tahmin | No |
> | GenerateDetailedCostReport | No |
> | Generatereservation\ayrıntılar raporu | No |
> | Insights | No |
> | Sorgu | No |
> | register | No |
> | Reportconfigs | No |
> | Raporlar | No |
> | Zamanlama kısımları | No |
> | Ayarlar | No |
> | showbackRules | No |
> | Görünümler | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft. Customerkasası

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Disablekasa | No |
> | Enablekasa | No |
> | istekleri | No |
> | Tenantoptedın | No |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | içermektedir | No |
> | resourceProviders | Yes |

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | larında | Yes |

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
> | çalışma alanı | Yes |
> | çalışma alanları/dbWorkspaces | No |
> | çalışma alanları/Virtualnetworkpeerler | No |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | larına | Yes |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Veri fabrikaları | Yes |
> | DataFactory/diagnosticSettings | No |
> | DataFactory/metricDefinitions | No |
> | dataFactorySchema | No |
> | larının | Yes |
> | Fabrika/tümleştirme çalışma zamanları | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |
> | hesaplar/dataLakeStoreAccounts | No |
> | hesaplar/storageAccounts | No |
> | hesaplar/storageAccounts/kapsayıcılar | No |
> | hesaplar/Transferanaliz tici | No |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |
> | hesaplar/eventGridFilters | No |
> | hesaplar/firewallRules | No |

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Databasegeçişleri | No |
> | services | Yes |
> | Hizmetler/Projeler | Yes |
> | SqlMigrationServices | Yes |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Backupkasaları | Yes |
> | Resourcekorumalara | Yes |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |
> | hesaplar/paylaşımlar | No |
> | hesaplar/paylaşımlar/veri kümeleri | No |
> | hesaplar/paylaşımlar/davetler | No |
> | hesaplar/paylaşımlar/providersharesubscriptions | No |
> | hesaplar/paylaşımlar/synchronizationSettings | No |
> | hesaplar/parçalar esubscriptions | No |
> | hesaplar/parçalar esubscriptions/Consumersourcedataset 'ler | No |
> | hesaplar/parçalar esubscriptions/datasetmappings | No |
> | hesaplar/parçalar esubscriptions/Tetikleyiciler | No |

## <a name="microsoftdbformariadb"></a>Microsoft. Dbformarıdb

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | larý | Yes |
> | sunucular/danışmanları | No |
> | sunucular/anahtarlar | No |
> | sunucular/Privateendpointconnectionproxy 'Leri | No |
> | sunucular/privateEndpointConnections | No |
> | sunucular/privateLinkResources | No |
> | sunucular/Querymetinmetinleri | No |
> | sunucular/recoverableServers | No |
> | Servers/Resetqueryperformanceınsi, Data | No |
> | sunucular/başlangıç | No |
> | sunucular/durdur | No |
> | sunucular/topQueryStatistics | No |
> | sunucular/virtualNetworkRules | No |
> | sunucular/waitStatistics | No |

## <a name="microsoftdbformysql"></a>Microsoft. Dbformyısql

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Flexibtaservers | Yes |
> | larý | Yes |
> | sunucular/danışmanları | No |
> | sunucular/anahtarlar | No |
> | sunucular/Privateendpointconnectionproxy 'Leri | No |
> | sunucular/privateEndpointConnections | No |
> | sunucular/privateLinkResources | No |
> | sunucular/Querymetinmetinleri | No |
> | sunucular/recoverableServers | No |
> | Servers/Resetqueryperformanceınsi, Data | No |
> | sunucular/başlangıç | No |
> | sunucular/durdur | No |
> | sunucular/topQueryStatistics | No |
> | sunucular/yükseltme | No |
> | sunucular/virtualNetworkRules | No |
> | sunucular/waitStatistics | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Flexibtaservers | Yes |
> | Sunucu grupları | Yes |
> | serverGroupsv2 | Yes |
> | larý | Yes |
> | sunucular/danışmanları | No |
> | sunucular/anahtarlar | No |
> | sunucular/Privateendpointconnectionproxy 'Leri | No |
> | sunucular/privateEndpointConnections | No |
> | sunucular/privateLinkResources | No |
> | sunucular/Querymetinmetinleri | No |
> | sunucular/recoverableServers | No |
> | Servers/Resetqueryperformanceınsi, Data | No |
> | sunucular/topQueryStatistics | No |
> | sunucular/virtualNetworkRules | No |
> | sunucular/waitStatistics | No |
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
> | applicationgroups/uygulamalar | No |
> | applicationgroups/masaüstleri | No |
> | applicationgroups/startmenuıtems | No |
> | Ana bilgisayar havuzları | Yes |
> | hostpools/msıxpackages | No |
> | hostpools/oturumkonakları | No |
> | hostpools/sessionkonakları/usersessions | No |
> | hosthavuzlar/usersessions | No |
> | Scalingplanlar | Yes |
> | çalışma alanı | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Elaun havuzları | Yes |
> | Elaun havuzları/ıothubkiracılar | Yes |
> | Elaor havuzları/ıothubkiracılar/securitySettings | No |
> | Iothubs | Yes |
> | IotHubs/eventGridFilters | No |
> | IotHubs/securitySettings | No |
> | ProvisioningServices | Yes |
> | vardır | No |

## <a name="microsoftdeviceupdate"></a>Microsoft. DeviceUpdate

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |
> | hesaplar/örnekler | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | düzenler | Yes |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | denetleyiciler | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | labcenters | Yes |
> | larda | Yes |
> | Laboratuvarlar/ortamlar | Yes |
> | Labs/Servicerunanlar | Yes |
> | Labs/virtualMachines | Yes |
> | cağını | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Digitaltwınsınstances | Yes |
> | Digitaltwınsınstances/endpoints | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Cassandrakümeler | Yes |
> | databaseAccountNames | No |
> | Veritabanı hesapları | Yes |
> | Restokıbledatabaseaccounts | No |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | etki alanları | Yes |
> | Domains/Domainownershiptanýmlayýcýlarý | No |
> | generateSsoRequest | No |
> | topLevelDomains | No |
> | Validatedomainregistrationınformation | No |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | lcsprojects | No |
> | lcsprojects/clouddağıtımları | No |
> | lcsprojects/bağlayıcılar | No |

## <a name="microsoftedgeorder"></a>Microsoft. EdgeOrder

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | gideren | Yes |
> | orderCollections | Yes |
> | siparişler | Yes |
> | productFamiliesMetadata | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | services | Yes |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | etki alanları | Yes |
> | etki alanları/konular | No |
> | Eventabonelikleri | No |
> | Extensionkonuları | No |
> | partnerNamespaces | Yes |
> | partnerNamespaces/eventChannels | No |
> | iş ortağı kayıtları | Yes |
> | iş ortağı konuları | Yes |
> | iş ortağı konuları/Eventabonelikleri | No |
> | Sistem konuları | Yes |
> | Sistem konuları/Eventabonelikler | No |
> | konuları | Yes |
> | topicTypes | No |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | leriniz | Yes |
> | öznitelikleri | Yes |
> | ad alanları/authorizationrules | No |
> | ad alanları/diskalrecoveryconfigs | No |
> | ad alanları/eventhubs | No |
> | ad alanları/eventhubs/authorizationrules | No |
> | ad alanları/eventhubs/consumergroups | No |
> | ad alanları/networkrulesets | No |
> | ad alanları/privateEndpointConnections | No |

## <a name="microsoftexperimentation"></a>Microsoft. deneme

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | experimentWorkspaces | Yes |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | öznitelikleri | Yes |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | featureConfigurations | No |
> | featureProviderNamespaces | No |
> | Özellik sağlayıcıları | No |
> | özellikler | No |
> | sağlayıcılarla | No |
> | subscriptionFeatureRegistrations | No |

## <a name="microsoftgallery"></a>Microsoft. Gallery

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | kaydedemez | No |
> | gallergıtems | No |
> | generateartifactaccessuri | No |
> | myarea | No |
> | myarea/alan | No |
> | myarea/alan/alan | No |
> | myareas/Areas/Areas/gallergıtems | No |
> | myareas/Areas/gallergıtems | No |
> | myarea/gallergıtems | No |
> | register | No |
> | kaynaklar | No |
> | elde edilecek esourcesbyıd | No |

## <a name="microsoftgenomics"></a>Microsoft. Genomiks

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Oto Managedaccounts | Yes |
> | Oto Managedvmconfigurationprofiles | Yes |
> | Configurationprofileatamalar | No |
> | Guestconfigurationatamaları | No |
> | yazılımıdır | No |
> | softwareUpdateProfile | No |
> | softwareUpdates | No |

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

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Kümehavuzları | Yes |
> | Kümehavuzları/kümeler | Yes |
> | leriniz | Yes |
> | kümeler/uygulamalar | No |

## <a name="microsofthealthbot"></a>Microsoft. HealthBot

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Healthbotlar | Yes |

## <a name="microsofthealthcareapis"></a>Microsoft. Healthgelişme API 'leri

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | services | Yes |
> | Hizmetler/ıomtbağlayıcılar | No |
> | Hizmetler/ıomtbağlayıcılar/bağlantılar | No |
> | Hizmetler/ıomtbağlayıcılar/eşlemeler | No |
> | Hizmetler/Privateendpointconnectionproxy 'Leri | No |
> | Hizmetler/privateEndpointConnections | No |
> | Hizmetler/privateLinkResources | No |
> | çalışma alanı | Yes |
> | çalışma alanları/dicomservices | Yes |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | larla | Yes |
> | makineler/assessPatches | No |
> | makineler/uzantılar | Yes |
> | makineler/ınstallpatches | No |
> | makineler/privateLinkScopes | No |
> | privateLinkScopes | Yes |
> | privateLinkScopes/Privateendpointconnectionproxy 'Leri | No |
> | privateLinkScopes/privateEndpointConnections | No |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Veri yöneticileri | Yes |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | cihazlar | Yes |
> | networkfunctions işlevleri | Yes |
> | Networkfunctionsatıcıları | No |
> | registeredSubscriptions | No |
> | Larını | No |
> | Satıcılar/vendorsku 'ları | No |
> | Satıcılar/vendorsku 'ları/önizleme abonelikleri | No |
> | virtualNetworkFunctions | Yes |
> | Virtualnetworkfunctionsatıcılarını | No |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | bileşenleri | Yes |
> | networkScopes | Yes |

## <a name="microsoftimportexport"></a>Microsoft. ımportexport

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Çizelge | Yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftiotcentral"></a>Microsoft. ıotcentral

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | appTemplates | No |
> | Iotapps | Yes |

## <a name="microsoftiotsecurity"></a>Microsoft. ıotsecurity

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Savunma Derayarları | No |

## <a name="microsoftiotspaces"></a>Microsoft. ıotspaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Graf | Yes |

## <a name="microsoftkeyvault"></a>Microsoft. Keykasası

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | deletedManagedHSMs | No |
> | Silinkaults | No |
> | hsmPools | Yes |
> | managedHSMs | Yes |
> | kasaları | Yes |
> | kasa/erişim Ilkeleri | No |
> | kasa/eventGridFilters | No |
> | kasa/anahtarlar | No |
> | kasa/anahtarlar/sürümler | No |
> | kasa/gizlilikler | No |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Connectedkümeler | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | uzantılardan | No |
> | sourceControlConfigurations | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | leriniz | Yes |
> | kümeler/attacheddatabaseconfigurations | No |
> | kümeler/veritabanları | No |
> | kümeler/veritabanları/veri bağlantıları | No |
> | kümeler/veritabanları/eventhubconnections | No |
> | kümeler/veritabanları/princıpalasbir | No |
> | kümeler/veritabanları/betikler | No |
> | kümeler/veri bağlantıları | No |
> | kümeler/princıpalasbir | No |
> | kümeler/parçalar | No |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | labaccounts | Yes |
> | labplanlar | Yes |
> | larda | Yes |
> | kullanıcılar | No |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | hostingEnvironments | Yes |
> | Tümleştirme hesapları | Yes |
> | ıntegrationserviceortamortamları | Yes |
> | ıntegrationserviceortamortamları/managedap | Yes |
> | ısotedenvironments | Yes |
> | sürdürülen | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft. Machinöğrenim

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
> | modelinventories | Yes |
> | virtualkümeler | Yes |
> | çalışma alanı | Yes |
> | çalışma alanları/batchEndpoints | Yes |
> | çalışma alanları/batchEndpoints/dağıtımlar | Yes |
> | çalışma alanları/batchEndpoints/dağıtımlar/işler | No |
> | çalışma alanları/batchEndpoints/işler | No |
> | çalışma alanları/kodlar | No |
> | çalışma alanları/kodlar/sürümler | No |
> | çalışma alanları/hesaplar | No |
> | çalışma alanları/veriler | No |
> | çalışma alanları/veri depoları | No |
> | çalışma alanları/ortamlar | No |
> | çalışma alanları/eventGridFilters | No |
> | çalışma alanları/işler | No |
> | çalışma alanları/labelingJobs | No |
> | çalışma alanları/linkedServices | No |
> | çalışma alanları/modeller | No |
> | çalışma alanları/modeller/sürümler | No |
> | çalışma alanları/onlineEndpoints | Yes |
> | çalışma alanları/onlineEndpoints/dağıtımlar | Yes |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | applyUpdates | No |
> | Configurationatamalar | No |
> | maintenanceConfigurations | Yes |
> | publicMaintenanceConfigurations | No |
> | güncelleştirmeler | No |

## <a name="microsoftmanagedidentity"></a>Microsoft. Managedıdentity

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Kimlikler | No |
> | Userassignedıdentities | Yes |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | managedNetworks | Yes |
> | managedNetworks/managedNetworkGroups | Yes |
> | managedNetworks/managedNetworkPeeringPolicies | Yes |
> | bildirim | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Pazar Placeryumuristrationdefinitions | No |
> | Registrationatamaları | No |
> | registrationDefinitions | No |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | getEntities | No |
> | Yönetim grupları | No |
> | managementGroups/Settings | No |
> | kaynaklar | No |
> | startTenantBackfill | No |
> | tenantBackfillStatus | No |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |
> | hesaplar/oluşturucular | Yes |
> | hesaplar/eventGridFilters | No |
> | hesaplar/privateAtlases | Yes |

## <a name="microsoftmarketplace"></a>Microsoft. Market

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Macc | No |
> | sunar | No |
> | offerTypes | No |
> | offerTypes/yayımcılar | No |
> | offerTypes/yayımcılar/teklifler | No |
> | offerTypes/yayımcılar/teklifler/planlar | No |
> | offerTypes/yayımcılar/teklifler/planlar/anlaşmalar | No |
> | offertypes/yayımcılar/teklifler/planlar/yapılandırmalarını | No |
> | offertypes/yayımcılar/teklifler/planlar/yapılandırmalarını/ımportımage | No |
> | privategallergıtems | No |
> | privateStoreClient | No |
> | Privatemağazaların | No |
> | Privatemağazaları/Adminrequestapprove | No |
> | Privatemağazaların/tekliflerin | No |
> | Privatemağazaların/tekliflerin/acknowledgeNotification | No |
> | Privatemağazaların/queryNotificationsState | No |
> | Privatemağazaları/Requestapproışları | No |
> | Privatemağazaları/Requestapproışları/sorgu | No |
> | Privatemağazaları/Requestapproışları/çekme planı | No |
> | ürün | No |
> | Publishers | No |
> | Yayımcılar/teklifler | No |
> | Yayımcılar/teklifler/Düzeltme | No |
> | register | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | classicDevServices | Yes |
> | updateCommunicationPreference | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft. Marketplacesıralaması

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | anlaşmalar | No |
> | offertypes | No |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | mediaservices | Yes |
> | mediaservices/accountFilters | No |
> | mediaservices/varlıklar | No |
> | mediaservices/varlıklar/assetFilters | No |
> | mediaservices/contentKeyPolicies | No |
> | mediaservices/eventGridFilters | No |
> | mediaservices/Graphınstances | No |
> | mediaservices/Graphtopolojileri | No |
> | mediaservices/liveEventOperations | No |
> | mediaservices/liveEvents | Yes |
> | mediaservices/liveEvents/Liveçıktılar | No |
> | mediaservices/liveOutputOperations | No |
> | mediaservices/Mediagraf | No |
> | mediaservices/privateEndpointConnectionOperations | No |
> | mediaservices/Privateendpointconnectionproxy 'Leri | No |
> | mediaservices/privateEndpointConnections | No |
> | mediaservices/streamingEndpointOperations | No |
> | mediaservices/streamingEndpoints | Yes |
> | mediaservices/Streamingkonumlandırıcı | No |
> | mediaservices/streamingPolicies | No |
> | mediaservices/dönüşümler | No |
> | mediaservices/dönüşümler/işler | No |
> | Video Çözümleyicileri | Yes |
> | Video Çözümleyicileri/edgeModules | No |

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
> | moveCollections | Yes |
> | projeyle | Yes |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Yes |
> | objectAnchorsAccounts | Yes |
> | objectUnderstandingAccounts | Yes |
> | remoteRenderingAccounts | Yes |
> | spatialAnchorsAccounts | Yes |

## <a name="microsoftmobilenetwork"></a>Microsoft. MobileNetwork

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Mamak | Yes |
> | ağlar/siteler | Yes |
> | Paket çekirdekleri | Yes |
> | SIMS 'ler | Yes |
> | SIMS/simProfiles | Yes |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | netAppAccounts | Yes |
> | netAppAccounts/accountBackups | No |
> | netAppAccounts/Capacityhavuzları | Yes |
> | netAppAccounts/Capacityhavuzları/birimleri | Yes |
> | netAppAccounts/Capacityhavuzlar/birimler/anlık görüntüler | No |
> | netAppAccounts/Birimgrupları | No |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Applicationgateway 'ler | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Yes |
> | applicationSecurityGroups | Yes |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | Yes |
> | Savunma Konakları | Yes |
> | bgpServiceCommunities | No |
> | bağlantının | Yes |
> | ddosCustomPolicies | Yes |
> | Ddosprotectionplanlar | Yes |
> | Dnsoperationdurumları | No |
> | dnszones | Yes |
> | dnszones/A | No |
> | dnszones/AAAA | No |
> | dnszones/tümü | No |
> | dnszones/CAA | No |
> | dnszones/CNAME | No |
> | dnszones/MX | No |
> | dnszones/NS | No |
> | dnszones/PTR | No |
> | dnszones/kayıt kümeleri | No |
> | dnszones/SOA | No |
> | dnszones/SRV | No |
> | dnszones/TXT | No |
> | Expressroutedevreleri | Yes |
> | expressRouteCrossConnections | Yes |
> | Expressroutegateway 'ler | Yes |
> | expressRoutePorts | Yes |
> | expressRouteServiceProviders | No |
> | firewallPolicies | Yes |
> | frontkapıların | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | Yes |
> | getDnsResourceReference | No |
> | ınternalnotify | No |
> | ipGroups | Yes |
> | loadBalancers | Yes |
> | Localnetworkgateway 'ler | Yes |
> | Natgateway 'ler | Yes |
> | Networkıntpolicies Ilkeleri | Yes |
> | NetworkInterfaces | Yes |
> | networkProfiles | Yes |
> | networkSecurityGroups | Yes |
> | networkWatchers | Yes |
> | networkWatchers/Connectionmonitörleri | Yes |
> | networkWatchers/flowLogs | Yes |
> | networkWatchers/uzunluler | Yes |
> | networkWatchers/Pingkafesler | Yes |
> | p2sVpnGateways | Yes |
> | privateDnsOperationStatuses | No |
> | privateDnsZones | Yes |
> | privateDnsZones/A | No |
> | privateDnsZones/AAAA | No |
> | privateDnsZones/tümü | No |
> | privateDnsZones/CNAME | No |
> | privateDnsZones/MX | No |
> | privateDnsZones/PTR | No |
> | privateDnsZones/SOA | No |
> | privateDnsZones/SRV | No |
> | privateDnsZones/TXT | No |
> | privateDnsZones/virtualNetworkLinks | Yes |
> | privateEndpoints | Yes |
> | privateLinkServices | Yes |
> | Publicıpaddresses | Yes |
> | Publicıpöneklerini | Yes |
> | routeFilters | Yes |
> | routeTables | Yes |
> | serviceEndpointPolicies | Yes |
> | trafficManagerGeographicHierarchies | No |
> | trafficmanagerprofiles | Yes |
> | trafficmanagerprofiles/heatMaps | No |
> | trafficManagerUserMetricsKeys | No |
> | Virtualhub 'Lar | Yes |
> | virtualNetworkGateways | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/alt ağları | No |
> | virtualNetworkTaps | Yes |
> | Virtualwan | Yes |
> | Vpngateway 'ler | Yes |
> | vpnSites | Yes |
> | webApplicationFirewallPolicies | Yes |

## <a name="microsoftnotebooks"></a>Microsoft. Not defterleri

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Not defteri proxy 'leri | No |

## <a name="microsoftnotificationhubs"></a>Microsoft. Notificationhub 'Lar

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
> | MasterSites | Yes |
> | Sunucusiteleri | Yes |
> | VMwareSites | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft. Operationalınsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | leriniz | Yes |
> | deletedWorkspaces | No |
> | Bağlantı hedefleri | No |
> | querypack | Yes |
> | Storageınsii configs | No |
> | çalışma alanı | Yes |
> | çalışma alanları/veri dışarı aktarmaları | No |
> | çalışma alanları/veri kaynakları | No |
> | çalışma alanları/linkedServices | No |
> | çalışma alanları/linkedStorageAccounts | No |
> | çalışma alanları/meta veriler | No |
> | çalışma alanları/sorgu | No |
> | çalışma alanları/Scopedprivatelinkproxy 'Leri | No |
> | çalışma alanları/Storageınsii configs | No |
> | çalışma alanları/tablolar | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | managementassociations | No |
> | managementconfigurations | Yes |
> | çözümler | Yes |
> | görünümler | Yes |

## <a name="microsoftpeering"></a>Microsoft. eşleme

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Cdnpeeringöneklerini | No |
> | Yasallıklar | No |
> | peerAsns | No |
> | eşlemeleri | Yes |
> | Peeringserviceülkeleriyle | No |
> | peeringServiceProviders | No |
> | peeringServices | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft. Poliyelei

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | belirlediğimizi karşıladığımızı | No |
> | eventGridFilters | No |
> | Poliyevents | No |
> | policyMetadata | No |
> | policyStates | No |
> | policyTrackedResources | No |
> | düzeltmeler | No |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> |  konsolları | No |
> | panolar | Yes |
> | tenantconfigurations | No |
> | userSettings | No |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Privatelinkservicesforpowerbı | Yes |
> | Kira | Yes |
> | Kiracılar/çalışma alanları | No |
> | workspaceCollections | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft. Powerbiadanmış

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Oto Islevi çekirdekleri | Yes |
> | kapasiteler | Yes |

## <a name="microsoftpowerplatform"></a>Microsoft. PowerPlatform

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | enterprisePolicies | Yes |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |
> | deletedAccounts | No |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Providerkayıtları | No |
> | Providerkayıtları/customRollouts | No |
> | Providerkayıtları/Defaultrollout | No |
> | Providerkayıtlarıyla/resourceTypeRegistrations | No |

## <a name="microsoftpurview"></a>Microsoft. purview

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |
> | deletedAccounts | No |
> | getDefaultAccount | No |
> | removeDefaultAccount | No |
> | setDefaultAccount | No |

## <a name="microsoftquantum"></a>Microsoft. hisse

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Çalışma Alanları | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Backupkorunabilir | No |
> | kasaları | Yes |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | OpenShiftClusters | Yes |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | öznitelikleri | Yes |
> | ad alanları/authorizationrules | No |
> | ad alanları/hybridconnections | No |
> | ad alanları/hybridconnections/authorizationrules | No |
> | ad alanları/privateEndpointConnections | No |
> | ad alanları/wcfreyerleştiri | No |
> | ad alanları/wcfreyerleştirme/authorizationrules | No |

## <a name="microsoftresourceconnector"></a>Microsoft. ResourceConnector

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | yaptı | Yes |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | lardır | Yes |
> | resourceChangeDetails | No |
> | resourceChanges | No |
> | kaynaklar | No |
> | resourcesHistory | No |
> | subscriptionsStatus | No |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Kullanılabilirlik durumları | No |
> | Childadvailabilitydurumlar | No |
> | childResources | No |
> | acil sorun sorunları | No |
> | etkinlikler | No |
> | ımpactedresources | No |
> | meta veriler | No |

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | dağıtımlar | No |
> | dağıtımlar/işlemler | No |
> | deploymentScripts | Yes |
> | deploymentScripts/Günlükler | No |
> | Köprü | No |
> | sağlayıcılarla | No |
> | resourceGroups | No |
> | Aboneliklerin | No |
> | Templatespec | Yes |
> | Templatespec/sürümler | Yes |
> | Kira | No |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | uygulamalar | Yes |
> | kaynaklar | Yes |
> | saasresources | No |

## <a name="microsoftscvmm"></a>Microsoft. ScVmm

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | larının | Yes |
> | VirtualMachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |
> | vmmservers | Yes |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | resourceHealthMetadata | No |
> | searchServices | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | No |
> | advancedThreatProtectionSettings | No |
> | alerts | No |
> | alertsSuppressionRules | No |
> | allowedConnections | No |
> | Applicationwhitedökümler | No |
> | assessmentMetadata | No |
> | değerlendirme | No |
> | Oto Dissalertsrules | No |
> | akışlarını otomatikleştirin | Yes |
> | Oto Provisioningsettings | No |
> | Uyumluluklarına | No |
> | bağlayıcılar | No |
> | dataCollectionAgents | No |
> | cihazlar | No |
> | deviceSecurityGroups | No |
> | discoveredSecuritySolutions | No |
> | externalSecuritySolutions | No |
> | Informationprotectionpolicies | No |
> | ınestionsettings | No |
> | eklentisini | No |
> | ıotalerts | No |
> | ıotalerttypes | No |
> | ıotsavunma Dersettings | No |
> | ıotönerileri | No |
> | iotRecommendationTypes | No |
> | ıotsecuritysolutions | Yes |
> | ıotsecuritysolutions/analiz Ticsmodeller | No |
> | ıotsecuritysolutions/Analticsmodeller/Aggreggıt uyarıları | No |
> | ıotsecuritysolutions/analiz Ticsmodeller/Aggreg, öneriler | No |
> | ıotsecuritysolutions/ıotalerts | No |
> | ıotsecuritysolutions/ıotalerttypes | No |
> | ıotsecuritysolutions/ıotönerilere | No |
> | ıotsecuritysolutions/iotRecommendationTypes | No |
> | Iotsensörler | No |
> | ıotsites | No |
> | Jağaccesspolicies | No |
> | Jyer Ilkeleri | No |
> | onPremiseIotSensors | No |
> | ilkeler | No |
> | fiyatlandırmalar | No |
> | Reve daha karmaşık bakım standartları | No |
> | Rekontrol ve Re, | No |
> | Rekontrol ve Re, Re, Re, | No |
> | secureScoreControlDefinitions | No |
> | secureScoreControls | No |
> | Securesçekirdekler | No |
> | Securesçekirdekler/secureScoreControls | No |
> | securityContacts | No |
> | securitySolutions | No |
> | securitySolutionsReferenceData | No |
> | Securitydurumlardan | No |
> | securityStatusesSummaries | No |
> | Sunucukullanılabilirliği | No |
> | ayarlar | No |
> | SQL, Açıbir | No |
> | subAssessments | No |
> | Görevler | No |
> | anlatır | No |
> | çalışma alanı ayarları | No |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftsecurityinsights"></a>Microsoft. Securityınsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | toplamaları | No |
> | alertRules | No |
> | Alertrutatemplates | No |
> | automationRules | No |
> | yer işaretleri | No |
> | çalışmaların | No |
> | Veri bağlayıcıları | No |
> | dataConnectorsCheckRequirements | No |
> | zenginleştirme | No |
> | varlıklar | No |
> | entityQueries | No |
> | entityQueryTemplates | No |
> | olaylar | No |
> | officeConsents | No |
> | ayarlar | No |
> | Threatıntelligence | No |
> | watchlists | No |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | consoleServices | No |
> | Seri bağlantı noktaları | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | öznitelikleri | Yes |
> | ad alanları/authorizationrules | No |
> | ad alanları/diskalrecoveryconfigs | No |
> | ad alanları/eventgridfilters | No |
> | ad alanları/networkrulesets | No |
> | ad alanları/privateEndpointConnections | No |
> | ad alanları/kuyruklar | No |
> | ad alanları/kuyruklar/authorizationrules | No |
> | ad alanları/konular | No |
> | ad alanları/konular/authorizationrules | No |
> | ad alanları/konular/abonelikler | No |
> | ad alanları/konular/abonelikler/kurallar | No |
> | premiumMessagingRegions | No |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | uygulamalar | Yes |
> | leriniz | Yes |
> | kümeler/uygulamalar | No |
> | Kapsayıcı grupları | Yes |
> | containerGroupSets | Yes |
> | edgeclusters | Yes |
> | edgeclusters/uygulamalar | No |
> | managedkümeler | Yes |
> | managedkümeler/uygulamalar | No |
> | managedkümeler/uygulamalar/hizmetler | No |
> | managedkümeler/applicationTypes | No |
> | managedkümeler/applicationTypes/sürümleri | No |
> | managedkümeler/nodetypes | No |
> | Mamak | Yes |
> | secretmağazaları | Yes |
> | secretmağazaları/sertifikaları | No |
> | secretmağazaları/gizli dizileri | No |
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

## <a name="microsoftservicelinker"></a>Microsoft. ServiceLinker

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | okuyamaz | No |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Providerkayıtları | No |
> | Providerkayıtlarıyla/resourceTypeRegistrations | No |
> | piyasaya çıkarma | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | SignalR | Yes |
> | SignalR/eventGridFilters | No |
> | WebPubSub | Yes |

## <a name="microsoftsingularity"></a>Microsoft. Singular

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |
> | hesaplar/accountQuotaPolicies | No |
> | hesaplar/groupPolicies | No |
> | hesaplar/işler | No |
> | hesaplar/storageContainers | No |
> | images | No |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Hybriduseavantajlar | No |

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
> | ManagedInstances/veritabanları/backupShortTermRetentionPolicies | No |
> | ManagedInstances/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels | No |
> | ManagedInstances/veritabanları/ | No |
> | ManagedInstances/veritabanları/ek | No |
> | ManagedInstances/encryptionProtector | No |
> | ManagedInstances/anahtarlar | No |
> | ManagedInstances/Restokbledroppeddatabases/backupShortTermRetentionPolicies | No |
> | ManagedInstances/ | No |
> | larý | Yes |
> | sunucular/Yöneticiler | No |
> | sunucular/communicationLinks | No |
> | sunucular/veritabanları | Yes |
> | sunucular/encryptionProtector | No |
> | sunucular/firewallRules | No |
> | sunucular/anahtarlar | No |
> | sunucular/Restokbledroppeddatabases | No |
> | Sunucu/hizmet hedefleri | No |
> | sunucular/tdeCertificates | No |
> | Virtualkümeler | No |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Yes |
> | SqlVirtualMachineGroups/kullanılabilirliği Bilitygrouplisteners | No |
> | SqlVirtualMachines | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | deletedAccounts | No |
> | storageAccounts | Yes |
> | storageAccounts/blobServices | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/Services | No |
> | storageAccounts/Services/metricDefinitions | No |
> | storageAccounts/tableServices | No |
> | vardır | No |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | amlFilesystems | Yes |
> | önbelleklerinde | Yes |
> | önbellekler/Storagetaral | No |
> | Usagemodeller | No |

## <a name="microsoftstoragereplication"></a>Microsoft. Storagerepce

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | replicationGroups | No |

## <a name="microsoftstoragesync"></a>Microsoft. Storagessync

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/iş akışları | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/iş akışları | No |

## <a name="microsoftstoragesyncint"></a>Microsoft. Storagesyncınt

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/iş akışları | No |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | ilerinde | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | leriniz | Yes |
> | kümeler/privateEndpoints | No |
> | streammingjobs | Yes |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | acceptChangeTenant | No |
> | acceptOwnership | No |
> | acceptOwnershipStatus | No |
> | deyim | No |
> | iptal | No |
> | changeTenantRequest | No |
> | changeTenantStatus | No |
> | CreateSubscription | No |
> | seçin | No |
> | ilkeler | No |
> | yeniden adlandır | No |
> | SubscriptionDefinitions | No |
> | SubscriptionOperations | No |
> | Aboneliklerin | No |

## <a name="microsoftsynapse"></a>Microsoft. SYNAPSE

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Privatelinkhub 'Ları | Yes |
> | çalışma alanı | Yes |
> | çalışma alanları/bigDataPools | Yes |
> | çalışma alanları/Operationdurumlar | No |
> | çalışma alanları/sqlDatabases | Yes |
> | çalışma alanları/sqlPools | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft. Timeseriesınsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | lý | Yes |
> | ortamlar/accessPolicies | No |
> | ortamlar/EventSources | Yes |
> | ortamlar/Privateendpointconnectionproxy 'Leri | No |
> | ortamlar/privateEndpointConnections | No |
> | ortamlar/privateLinkResources | No |
> | ortamlar/Referencedataset 'ler | Yes |

## <a name="microsofttoken"></a>Microsoft. Token

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | depolaya | Yes |
> | depolar/accessPolicies | No |
> | depolar/hizmetler | No |
> | depolar/hizmetler/belirteçler | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. Virtualmachineımages

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | ımagetemplates | Yes |
> | ımagetemplates/Runçıktılar | No |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | ArcZones | Yes |
> | ResourcePools | Yes |
> | VCenter | Yes |
> | VCenters/ınventoryıtems | No |
> | virtualmachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Vmwarechoparlör basit

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | ayrılmış Cloudnodes | Yes |
> | ayrılmış CloudService | Yes |
> | virtualMachines | Yes |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | cihazlar | Yes |
> | registeredSubscriptions | No |
> | larını | No |
> | satıcılar/SKU 'lar | No |
> | satıcılar/vnfs | No |
> | Virtualnetworkfunctionsku 'Ları | No |
> | vnfs | Yes |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | accounts | Yes |
> | Planlama | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | apiManagementAccounts | No |
> | apiManagementAccounts/Apiacl 'Ler | No |
> | apiManagementAccounts/API 'ler | No |
> | apiManagementAccounts/API/Apiacl 'Ler | No |
> | apiManagementAccounts/API/Connectionacl 'Ler | No |
> | apiManagementAccounts/API/bağlantı | No |
> | apiManagementAccounts/API/Connections/Connectionacl 'Ler | No |
> | apiManagementAccounts/API/localizedDefinitions | No |
> | apiManagementAccounts/Connectionacl 'Ler | No |
> | apiManagementAccounts/bağlantılar | No |
> | billingMeters | No |
> | sertifikalar | Yes |
> | Connectiongateway 'ler | Yes |
> | bağlantının | Yes |
> | Customapsıs | Yes |
> | Silinmi siteleri | No |
> | Functionappyığınları | No |
> | generateGithubAccessTokenForAppserviceCLI | No |
> | hostingEnvironments | Yes |
> | hostingEnvironments/eventGridFilters | No |
> | hostingEnvironments/multiRolePools | No |
> | hostingEnvironments/workerPools | No |
> | Kubeortamları | Yes |
> | publishingUsers | No |
> | Öneriler | No |
> | resourceHealthMetadata | No |
> | zamanları | No |
> | serverFarms | Yes |
> | Sunucugrupları/eventGridFilters | No |
> | Sunucugrupları/firstPartyApps | No |
> | Sunucugrupları/firstPartyApps/keyVaultSettings | No |
> | Siteler | Yes |
> | siteler/yapılandırma  | No |
> | siteler/eventGridFilters | No |
> | siteler/hostNameBindings | No |
> | Sites/networkConfig | No |
> | siteler/premieraddons | Yes |
> | siteler/yuvalar | Yes |
> | siteler/yuvalar/eventGridFilters | No |
> | siteler/yuvalar/hostNameBindings | No |
> | siteler/yuvalar/networkConfig | No |
> | sourceControls | No |
> | staticSites | Yes |
> | doğrulamalısınız | No |
> | verifyHostingEnvironmentVnet | No |
> | Webappyığınları | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. Windowssavunma Deratp

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | Çoğulactivationkeys | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft. Windowsıot

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | DeviceServices | Yes |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | migrationAgents | Yes |
> | lerine | Yes |
> | iş yükleri/örnekler | No |
> | iş yükleri/sürümler | No |
> | iş yükleri/sürümler/yapılar | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Kaynak türü | Tam modda silme |
> | ------------- | ----------- |
> | monitörün | Hayır |

## <a name="next-steps"></a>Sonraki adımlar

Aynı verileri bir virgülle ayrılmış değerler dosyası ile almak için [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)indirin.
