---
title: Kaynaklar için etiket desteği
description: Hangi Azure kaynak türlerinin etiketleri desteklediğini gösterir. Tüm Azure hizmetleri için ayrıntılar sağlar.
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: b196cae267a8d7dc878f055f6b2d70a3ff6f9313
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773972"
---
# <a name="tag-support-for-azure-resources"></a>Azure kaynakları için etiket desteği
Bu makalede, bir kaynak türünün [etiketleri](tag-resources.md)destekleyip desteklemediğini açıklanmaktadır. Etiketi **destekleyen** sütun, kaynak türünün etiket için bir özelliğe sahip olup olmadığını gösterir. **Maliyet raporundaki etiket** etiketli sütun, kaynak türünün etiketi maliyet raporuna geçirip geçirmediğini belirtir. [Maliyet yönetimi maliyet analizi](../../cost-management-billing/costs/group-filter.md) ve [Azure Faturalandırma faturasında ve günlük kullanım verilerinde](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)maliyeti etiketlere göre görüntüleyebilirsiniz.

Aynı verileri bir virgülle ayrılmış değerler dosyası ile almak için [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)indirin.

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
> - [Microsoft.Insights](#microsoftinsights)
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
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | DomainServices | Yes | Yes |
> | DomainServices/oucontainer | Hayır | Hayır |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Destek sağlayıcıları | Hayır | Hayır |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Hayır | Hayır |
> | addsservices | Hayır | Hayır |
> | aracısını | Hayır | Hayır |
> | anonymousapiusers | Hayır | Hayır |
> | yapılandırma | Hayır | Hayır |
> | günlükler | Hayır | Hayır |
> | reports | Hayır | Hayır |
> | servicehealthölçümleri | Hayır | Hayır |
> | services | Hayır | Hayır |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Danışmanlaştırıp | Hayır | Hayır |
> | konfigürasyonları | Hayır | Hayır |
> | Generatereyorumgeçişleri | Hayır | Hayır |
> | meta veriler | Hayır | Hayır |
> | Öneriler | Hayır | Hayır |
> | gizlemeleri | Hayır | Hayır |

## <a name="microsoftagfoodplatform"></a>Microsoft. AG, Dplatform

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Farmtları | Yes | Yes |
> | Farmtts/eventGridFilters | Hayır | Hayır |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | actionRules | Yes | Yes |
> | alerts | Hayır | Hayır |
> | alertsList | Hayır | Hayır |
> | alertsMetaData | Hayır | Hayır |
> | alertsSummary | Hayır | Hayır |
> | alertsSummaryList | Hayır | Hayır |
> | migrateFromSmartDetection | Hayır | Hayır |
> | resourceHealthAlertRules | Yes | Yes |
> | smartDetectorAlertRules | Yes | Yes |
> | smartGroups | Hayır | Hayır |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | larý | Yes | Yes |

## <a name="microsoftanybuild"></a>Microsoft. AnyBuild

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | leriniz | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft. Apimanane

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Silinmi Hizmetleri | Hayır | Hayır |
> | Getdomainownershipıdentifier | Hayır | Hayır |
> | reportFeedback | Hayır | Hayır |
> | hizmet | Yes | Yes |
> | validateServiceName | Hayır | Hayır |

> [!NOTE]
> Azure API Management, her hizmet için yalnızca en fazla 15 etiket adı/değer çifti oluşturulmasını destekler.

## <a name="microsoftappassessment"></a>Microsoft. AppAssessment

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | migrateProjects | Yes | Yes |
> | migrateProjects/değerlendirmeler | Hayır | Hayır |
> | migrateProjects/değerlendirmeler/assessedApplications | Hayır | Hayır |
> | migrateProjects/değerlendirmeler/assessedApplications/makineler | Hayır | Hayır |
> | migrateProjects/değerlendirmeler/assessedMachines | Hayır | Hayır |
> | migrateProjects/değerlendirmeler/assessedMachines/uygulamalar | Hayır | Hayır |
> | migrateProjects/değerlendirmeler/, Inestodeğerlendir | Hayır | Hayır |
> | migrateProjects/Sites | Hayır | Hayır |
> | migrateProjects/Sites/applianceConfigurations | Hayır | Hayır |
> | Migrateprojeler/siteler/makineler | Hayır | Hayır |
> | osVersions | Hayır | Hayır |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Configurationmağazaların | Yes | Hayır |
> | Configurationmağazaların/eventGridFilters | Hayır | Hayır |
> | Configurationmağazaların/keyValues | Hayır | Hayır |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Spring | Yes | Yes |
> | Yay/uygulamalar | Hayır | Hayır |
> | Yay/uygulamalar/dağıtımlar | Hayır | Hayır |

## <a name="microsoftattestation"></a>Microsoft. kanıtlama

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Yes | Yes |
> | defaultProviders | Hayır | Hayır |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Accessbelgeno Scheduledefinitions | Hayır | Hayır |
> | Accessbelgeayarlarý Schedulesettings | Hayır | Hayır |
> | classicAdministrators | Hayır | Hayır |
> | Datatakma adlar | Hayır | Hayır |
> | Datapolicybildirimleri | Hayır | Hayır |
> | Denyasatamaları | Hayır | Hayır |
> | Erişimi yükseltme | Hayır | Hayır |
> | Findorphanroleatamalar | Hayır | Hayır |
> | kaynaktaki | Hayır | Hayır |
> | izinler | Hayır | Hayır |
> | Poliyasatamaları | Hayır | Hayır |
> | policyDefinitions | Hayır | Hayır |
> | Policymuafiyet | Hayır | Hayır |
> | policySetDefinitions | Hayır | Hayır |
> | privateLinkAssociations | Hayır | Hayır |
> | providerOperations | Hayır | Hayır |
> | resourceManagementPrivateLinks | Yes | Yes |
> | Roleatamamentapları | Hayır | Hayır |
> | roleAssignments | Hayır | Hayır |
> | Roleatamazamanlama örnekleri | Hayır | Hayır |
> | roleAssignmentScheduleRequests | Hayır | Hayır |
> | Roleatamazamanlamaları | Hayır | Hayır |
> | Roleatamasussusageölçümleri | Hayır | Hayır |
> | roleDefinitions | Hayır | Hayır |
> | Roleeligibilityscheduleınstances | Hayır | Hayır |
> | roleEligibilityScheduleRequests | Hayır | Hayır |
> | Roleeligibilityzamanlamalar | Hayır | Hayır |
> | roleManagementPolicies | Hayır | Hayır |
> | Rolemanagementpolicyasatamaları | Hayır | Hayır |

## <a name="microsoftautomanage"></a>Microsoft. oto Yönet

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | Configurationprofileatamalar | Hayır | Hayır |
> | configurationProfilePreferences | Yes | Yes |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Yes | Yes |
> | automationAccounts/Configurations | Yes | Yes |
> | automationAccounts/Jobs | Hayır | Hayır |
> | automationAccounts/Privateendpointconnectionproxy 'Leri | Hayır | Hayır |
> | automationAccounts/privateEndpointConnections | Hayır | Hayır |
> | automationAccounts/privateLinkResources | Hayır | Hayır |
> | automationAccounts/runbook 'lar | Yes | Yes |
> | automationAccounts/softwareUpdateConfigurations | Hayır | Hayır |
> | automationAccounts/Web kancaları | Hayır | Hayır |

> [!NOTE]
> Azure Otomasyonu yalnızca her bir Otomasyon kaynağı için en fazla 15 etiket adı/değer çifti oluşturmayı destekler.

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Privatebulutlar | Yes | Yes |
> | Privatebulutlar/eklentiler | Hayır | Hayır |
> | Privatebulutlar/yetkilendirmeler | Hayır | Hayır |
> | Privatebulutlar/cloudLinks | Hayır | Hayır |
> | Privatebulutlar/kümeler | Hayır | Hayır |
> | Privatebulutlar/kümeler/veri depoları | Hayır | Hayır |
> | Privatebulutlar/globalReachConnections | Hayır | Hayır |
> | Privatebulutlar/hcxEnterpriseSites | Hayır | Hayır |
> | Privatebulutlar/scriptExecutions | Hayır | Hayır |
> | Privatebulutlar/scriptPackages | Hayır | Hayır |
> | Privatebulutlar/scriptPackages/Scriptcmdlet 'leri | Hayır | Hayır |
> | Privatebulutlar/workloadNetworks | Hayır | Hayır |
> | Privatebulutlar/workloadNetworks/dhcpConfigurations | Hayır | Hayır |
> | Privatebulutlar/workloadNetworks/dnsServices | Hayır | Hayır |
> | Privatebulutlar/workloadNetworks/dnsZones | Hayır | Hayır |
> | Privatebulutlar/workloadNetworks/Gateway | Hayır | Hayır |
> | Privatebulutlar/workloadNetworks/portMirroringProfiles | Hayır | Hayır |
> | Privatebulutlar/workloadNetworks/segment | Hayır | Hayır |
> | Privatebulutlar/workloadNetworks/virtualMachines | Hayır | Hayır |
> | Privatebulutlar/workloadNetworks/vmGroups | Hayır | Hayır |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Genfiliz

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | lý | Hayır | Hayır |
> | ortamlar/hesaplar | Hayır | Hayır |
> | ortamlar/hesaplar/ad alanları | Hayır | Hayır |
> | ortamlar/hesaplar/ad alanları/yapılandırma | Hayır | Hayır |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Yes | Hayır |
> | b2ctenants | Hayır | Hayır |
> | Guestkullanımlar | Yes | Yes |

## <a name="microsoftazurearcdata"></a>Microsoft. AzureArcData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Veri denetleyicileri | Yes | Yes |
> | Datawarehouseınstances | Yes | Yes |
> | Postgresınstances | Yes | Yes |
> | Sqlmanagedınstances | Yes | Yes |
> | Sqlserverınstances | Yes | Yes |

## <a name="microsoftazurecis"></a>Microsoft. Azurecdir

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | autopilotEnvironments | Yes | Yes |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Sqlserverkayıtları | Yes | Yes |
> | Sqlserverkayıtları/sqlServers | Hayır | Hayır |

## <a name="microsoftazuresphere"></a>Microsoft. AzureSphere

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | larına | Yes | Yes |
> | kataloglar/ürünler | Yes | Yes |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Hayır | Hayır |
> | edgeSubscriptions | Yes | Yes |
> | Linkedabonelikleri | Yes | Yes |
> | kayıtlarında | Yes | Yes |
> | kayıt/müşteri abonelikleri | Hayır | Hayır |
> | kayıtlar/ürünler | Hayır | Hayır |

## <a name="microsoftazurestackhci"></a>Microsoft. Azurestackhcı

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | leriniz | Yes | Yes |
> | Gallerımages | Yes | Yes |
> | NetworkInterfaces | Yes | Yes |
> | virtualHardDisks | Yes | Yes |
> | virtualMachines | Yes | Yes |
> | virtualNetworks | Yes | Yes |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft. BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | bareMetalInstances | Yes | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Yes | Yes |
> | batchAccounts/sertifikalar | Hayır | Hayır |
> | batchAccounts/havuzlar | Hayır | Hayır |

## <a name="microsoftbilling"></a>Microsoft. Faturalandırma

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
> | billingAccounts/billingProfiles/yönergeler | Hayır | Hayır |
> | billingAccounts/billingProfiles/faturalar | Hayır | Hayır |
> | billingAccounts/billingProfiles/faturalar/fiyat listesi | Hayır | Hayır |
> | billingAccounts/billingProfiles/faturalar/işlemler | Hayır | Hayır |
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
> | billingAccounts/billingProfiles/ınvoicesections/Validatedeleteınvoicesectionuygunluğu | Hayır | Hayır |
> | billingAccounts/BillingProfiles/patchOperations | Hayır | Hayır |
> | billingAccounts/billingProfiles/paymentMethods | Hayır | Hayır |
> | billingAccounts/billingProfiles/ilkeler | Hayır | Hayır |
> | billingAccounts/billingProfiles/fiyat listesi | Hayır | Hayır |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Hayır | Hayır |
> | billingAccounts/billingProfiles/ürünler | Hayır | Hayır |
> | billingAccounts/billingProfiles/rezervasyonlar | Hayır | Hayır |
> | billingAccounts/billingProfiles/işlemler | Hayır | Hayır |
> | billingAccounts/billingProfiles/Validatedeletebillingprofileuygunluğu | Hayır | Hayır |
> | billingAccounts/billingProfiles/validateDetachPaymentMethodEligibility | Hayır | Hayır |
> | billingAccounts/Billingroleatamaları | Hayır | Hayır |
> | billingAccounts/billingRoleDefinitions | Hayır | Hayır |
> | billingAccounts/Billingabonelikleri | Hayır | Hayır |
> | billingAccounts/Billingabonelikleri/yükseltme Terole | Hayır | Hayır |
> | billingAccounts/Billingabonelikleri/faturalar | Hayır | Hayır |
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
> | billingAccounts/departmanlar/billingPermissions | Hayır | Hayır |
> | billingAccounts/departmanlar/Billingroleatamaları | Hayır | Hayır |
> | billingAccounts/departmanlar/billingRoleDefinitions | Hayır | Hayır |
> | billingAccounts/departmanlar/Billingabonelikleri | Hayır | Hayır |
> | billingAccounts/KayıtSayısı | Hayır | Hayır |
> | billingAccounts/Kayıthesapsayısı/billingPermissions | Hayır | Hayır |
> | billingAccounts/KayıtSayısı/Billingroleatamaları | Hayır | Hayır |
> | billingAccounts/KayıtSayısı/billingRoleDefinitions | Hayır | Hayır |
> | billingAccounts/Kayıthesapsayısı/Billingabonelikleri | Hayır | Hayır |
> | billingAccounts/faturalar | Hayır | Hayır |
> | billingAccounts/faturalar/işlemler | Hayır | Hayır |
> | billingAccounts/faturalar/transactionSummary | Hayır | Hayır |
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
> | billingAccounts/payableOverage | Hayır | Hayır |
> | billingAccounts/paymentMethods | Hayır | Hayır |
> | billingAccounts/payan | Hayır | Hayır |
> | billingAccounts/ürünler | Hayır | Hayır |
> | billingAccounts/rezervasyonlar | Hayır | Hayır |
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
> | ter | Hayır | Hayır |
> | girişinde | Hayır | Hayır |
> | aktarımlar/acceptTransfer | Hayır | Hayır |
> | aktarımlar/declineTransfer | Hayır | Hayır |
> | aktarımlar/operationStatus | Hayır | Hayır |
> | aktarımlar/validateTransfer | Hayır | Hayır |
> | validateAddress | Hayır | Hayır |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

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
> | izleyicileri | Yes | Yes |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | TokenServices | Yes | Yes |
> | TokenServices/BlockchainNetworks | Hayır | Hayır |
> | TokenServices/Groups | Hayır | Hayır |
> | TokenServices/gruplar/hesaplar | Hayır | Hayır |
> | TokenServices/TokenTemplates | Hayır | Hayır |

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
> | hostSettings | Hayır | Hayır |
> | diller | Hayır | Hayır |
> | templates | Hayır | Hayır |

## <a name="microsoftcache"></a>Microsoft. Cache

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Redis | Yes | Yes |
> | Redsıs/EventGridFilters | Hayır | Hayır |
> | Redsıs/Privateendpointconnectionproxy 'Leri | Hayır | Hayır |
> | Redsıs/Privateendpointconnectionproxy/doğrulama | Hayır | Hayır |
> | Redsıs/privateEndpointConnections | Hayır | Hayır |
> | Redsıs/privateLinkResources | Hayır | Hayır |
> | redisEnterprise | Yes | Yes |
> | redisEnterprise/veritabanları | Hayır | Hayır |
> | RedisEnterprise/Privateendpointconnectionproxy 'Leri | Hayır | Hayır |
> | RedisEnterprise/Privateendpointconnectionproxy 'Leri/doğrulama | Hayır | Hayır |
> | RedisEnterprise/privateEndpointConnections | Hayır | Hayır |
> | RedisEnterprise/privateLinkResources | Hayır | Hayır |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Hayır | Hayır |
> | Oto Kotaartışı | Hayır | Hayır |
> | calculateExchange | Hayır | Hayır |
> | calculatePrice | Hayır | Hayır |
> | calculatePurchasePrice | Hayır | Hayır |
> | larına | Hayır | Hayır |
> | Ticari Vaalrezervler | Hayır | Hayır |
> | değişimi | Hayır | Hayır |
> | Ownrezervasyonlarını | Hayır | Hayır |
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
> | resourceProviders | Hayır | Hayır |
> | kaynaklar | Hayır | Hayır |
> | validateReservationOrder | Hayır | Hayır |

## <a name="microsoftcascade"></a>Microsoft. Cascade

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Siteler | Yes | Yes |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Hayır | Hayır |
> | CdnWebApplicationFirewallPolicies | Yes | Yes |
> | edgenodes | Hayır | Hayır |
> | lerinize | Yes | Yes |
> | Profiller/afırdpoints | Yes | Yes |
> | Profiller/Affe/rotalar | Hayır | Hayır |
> | Profiller/customdomains | Hayır | Hayır |
> | Profiller/uç noktalar | Yes | Yes |
> | Profiller/uç noktalar/customdomains | Hayır | Hayır |
> | Profiller/uç noktalar/origingroups | Hayır | Hayır |
> | Profiller/uç noktalar/kaynaklar | Hayır | Hayır |
> | Profiller/origingroups | Hayır | Hayır |
> | Profiller/origingroups/kaynaklar | Hayır | Hayır |
> | Profiller/RuleSets | Hayır | Hayır |
> | Profiller/RuleSets/Rules | Hayır | Hayır |
> | Profiller/gizlilikler | Hayır | Hayır |
> | Profiller/securitypolicies | Hayır | Hayır |
> | Validatearaştırması | Hayır | Hayır |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Sertifikadüzenleri | Yes | Yes |
> | certificateOrders/Certificates | Hayır | Hayır |
> | Validatecertificateregistrationınformation | Hayır | Hayır |

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | değişiklikler | Hayır | Hayır |
> | profil | Hayır | Hayır |
> | resourceChanges | Hayır | Hayır |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | özellikler | Hayır | Hayır |
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

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | özellikler | Hayır | Hayır |
> | expressRouteCrossConnections | Hayır | Hayır |
> | expressRouteCrossConnections/peerler | Hayır | Hayır |
> | gatewaySupportedDevices | Hayır | Hayır |
> | networkSecurityGroups | Hayır | Hayır |
> | quotas | Hayır | Hayır |
> | Rezervler | Hayır | Hayır |
> | virtualNetworks | Hayır | Hayır |
> | virtualNetworks/Remotevirtualnetworkpeeringproxy 'Leri | Hayır | Hayır |
> | virtualNetworks/Virtualnetworkpeerler | Hayır | Hayır |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | özellikler | Hayır | Hayır |
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

## <a name="microsoftclusterstor"></a>Microsoft. ClusterStor

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | düğümlerini | Yes | Yes |

## <a name="microsoftcodespaces"></a>Microsoft. Codespaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Planlama | Yes | Hayır |
> | registeredSubscriptions | Hayır | Hayır |

## <a name="microsoftcognitiveservices"></a>Microsoft. Biliveservices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | hesaplar/Privateendpointconnectionproxy 'Leri | Hayır | Hayır |
> | hesaplar/privateEndpointConnections | Hayır | Hayır |
> | hesaplar/privateLinkResources | Hayır | Hayır |

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
> | cloudServices | Yes | Yes |
> | cloudServices/NetworkInterfaces | Hayır | Hayır |
> | cloudServices/Publicıpaddresses | Hayır | Hayır |
> | cloudServices/Roleınstances | Hayır | Hayır |
> | cloudServices/Roleınstances/NetworkInterfaces | Hayır | Hayır |
> | cloudServices/roller | Hayır | Hayır |
> | Diskeriþler | Yes | Yes |
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
> | sshPublicKeys | Yes | Yes |
> | virtualMachines | Yes | Yes |
> | virtualMachines/uzantıları | Yes | Yes |
> | virtualMachines/metricDefinitions | Hayır | Hayır |
> | virtualMachines/runCommands | Yes | Yes |
> | virtualMachineScaleSets | Yes | Yes |
> | virtualMachineScaleSets/uzantılar | Hayır | Hayır |
> | virtualMachineScaleSets/NetworkInterfaces | Hayır | Hayır |
> | virtualMachineScaleSets/Publicıpaddresses | Yes | Hayır |
> | virtualMachineScaleSets/virtualMachines | Hayır | Hayır |
> | virtualMachineScaleSets/virtualMachines/NetworkInterfaces | Hayır | Hayır |

> [!NOTE]
> Genelleştirilmiş olarak işaretlenmiş bir sanal makineye etiket ekleyemezsiniz. [Set-AzVm-Genelleştirilmiş](/powershell/module/Az.Compute/Set-AzVM) veya [az VM generalize](/cli/azure/vm#az_vm_generalize)ile bir sanal makineyi genelleştirilemez.

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Önbellekenodes | Yes | Yes |

## <a name="microsoftconnectedvehicle"></a>Microsoft. Connectedaraç

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | platformAccounts | Yes | Yes |
> | registeredSubscriptions | Hayır | Hayır |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft. ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | ResourcePools | Yes | Yes |
> | VCenter | Yes | Yes |
> | VCenters/ınventoryıtems | Hayır | Hayır |
> | VirtualMachines | Yes | Yes |
> | VirtualMachines/uzantıları | Yes | Yes |
> | VirtualMachines/GuestAgents | Hayır | Hayır |
> | VirtualMachines/Hybridıdentitymetadata | Hayır | Hayır |
> | VirtualMachineTemplates | Yes | Yes |
> | VirtualNetworks | Yes | Yes |

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
> | Fiyat listeleri | Hayır | Hayır |
> | ürün | Hayır | Hayır |
> | Rezervde ayrıntıları | Hayır | Hayır |
> | ReservationRecommendationDetails | Hayır | Hayır |
> | Rezervationönerilere | Hayır | Hayır |
> | Rezervlerin Özeti | Hayır | Hayır |
> | Rezervlik Işlemleri | Hayır | Hayır |
> | Etiketler | Hayır | Hayır |
> | Kira | Hayır | Hayır |
> | Terimler | Hayır | Hayır |
> | UsageDetails | Hayır | Hayır |

## <a name="microsoftcontainerinstance"></a>Microsoft. Containerınstance

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Kapsayıcı grupları | Yes | Yes |
> | serviceAssociationLinks | Hayır | Hayır |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | kayıt | Yes | Yes |
> | kayıt defterleri/agentPools | Yes | Yes |
> | kayıt defterleri/derlemeler | Hayır | Hayır |
> | kayıt defterleri/derlemeler/iptal | Hayır | Hayır |
> | kayıt defterleri/derlemeler/getLogLink | Hayır | Hayır |
> | kayıt defterleri/buildTasks | Yes | Yes |
> | kayıt defterleri/buildTasks/Steps | Hayır | Hayır |
> | kayıt defterleri/Connectedregistry | Hayır | Hayır |
> | kayıt defterleri/Connectedkayıt defterleri/devre dışı bırakma | Hayır | Hayır |
> | kayıt defterleri/eventGridFilters | Hayır | Hayır |
> | kayıt defterleri/Exporthatlarının | Hayır | Hayır |
> | kayıt defterleri/generateCredentials | Hayır | Hayır |
> | kayıt defterleri/getBuildSourceUploadUrl 'Si | Hayır | Hayır |
> | kayıt defterleri/GetCredentials | Hayır | Hayır |
> | kayıt defterleri/ımportımage | Hayır | Hayır |
> | kayıt defterleri/ımporthatlarının | Hayır | Hayır |
> | kayıt defterleri/ardışık düzen eylemsizlik | Hayır | Hayır |
> | kayıt defterleri/Privateendpointconnectionproxy 'Leri | Hayır | Hayır |
> | kayıt defterleri/Privateendpointconnectionproxy/doğrulama | Hayır | Hayır |
> | kayıt defterleri/privateEndpointConnections | Hayır | Hayır |
> | kayıt defterleri/privateLinkResources | Hayır | Hayır |
> | kayıt defterleri/queueBuild | Hayır | Hayır |
> | kayıt defterleri/regenerateCredential | Hayır | Hayır |
> | kayıt defterleri/regenerateCredentials | Hayır | Hayır |
> | kayıt defterleri/çoğaltmalar | Yes | Yes |
> | kayıt defterleri/çalıştırmalar | Hayır | Hayır |
> | kayıt defterleri/çalıştırmalar/iptal | Hayır | Hayır |
> | kayıt defterleri/scheduleRun | Hayır | Hayır |
> | kayıt defterleri/Kapsameşlemler | Hayır | Hayır |
> | kayıt defterleri/Taskçalıştırmaları | Hayır | Hayır |
> | kayıt defterleri/görevler | Yes | Yes |
> | kayıt defterleri/belirteçler | Hayır | Hayır |
> | kayıt defterleri/updatePolicies | Hayır | Hayır |
> | kayıt defterleri/Web kancaları | Yes | Yes |
> | kayıt defterleri/Web kancaları/getCallbackConfig | Hayır | Hayır |
> | kayıt defterleri/Web kancaları/ping | Hayır | Hayır |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | containerServices | Yes | Yes |
> | Managedkümeler | Yes | Yes |
> | Managedkümeler/eventGridFilters | Hayır | Hayır |
> | openShiftManagedClusters | Yes | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Uyarılar | Hayır | Hayır |
> | BillingAccounts | Hayır | Hayır |
> | Bütçeler | Hayır | Hayır |
> | Cloudbağlayıcıları | Hayır | Hayır |
> | Bağlayıcılar | Yes | Yes |
> | costAllocationRules | Hayır | Hayır |
> | Departmanlar | Hayır | Hayır |
> | Boyutlar | Hayır | Hayır |
> | Kayıt sayısı | Hayır | Hayır |
> | Dışarı Aktarmalar | Hayır | Hayır |
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
> | Fetchfiyatlarını | Hayır | Hayır |
> | Tahmin | Hayır | Hayır |
> | GenerateDetailedCostReport | Hayır | Hayır |
> | Generatereservation\ayrıntılar raporu | Hayır | Hayır |
> | Insights | Hayır | Hayır |
> | Sorgu | Hayır | Hayır |
> | register | Hayır | Hayır |
> | Reportconfigs | Hayır | Hayır |
> | Raporlar | Hayır | Hayır |
> | Zamanlama kısımları | Hayır | Hayır |
> | Ayarlar | Hayır | Hayır |
> | showbackRules | Hayır | Hayır |
> | Görünümler | Hayır | Hayır |

## <a name="microsoftcustomerlockbox"></a>Microsoft. Customerkasası

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Disablekasa | Hayır | Hayır |
> | Enablekasa | Hayır | Hayır |
> | istekleri | Hayır | Hayır |
> | Tenantoptedın | Hayır | Hayır |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | içermektedir | Hayır | Hayır |
> | resourceProviders | Yes | Yes |

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | larında | Yes | Yes |

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
> | çalışma alanı | Yes | Yes |
> | çalışma alanları/dbWorkspaces | Hayır | Hayır |
> | çalışma alanları/Virtualnetworkpeerler | Hayır | Hayır |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | larına | Yes | Yes |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Veri fabrikaları | Yes | Yes |
> | DataFactory/diagnosticSettings | Hayır | Hayır |
> | DataFactory/metricDefinitions | Hayır | Hayır |
> | dataFactorySchema | Hayır | Hayır |
> | larının | Yes | Yes |
> | Fabrika/tümleştirme çalışma zamanları | Hayır | Hayır |

> [!NOTE]
> Veri fabrikanızdaki Azure-SSIS tümleştirme çalışma zamanları varsa, çalışan maliyeti Data Factory etiketleriyle etiketlenecek. Azure-SSIS tümleştirme çalışma zamanlarının çalıştırılması, yeni Data Factory etiketlerinin çalışan maliyetlerine uygulanması için durdurulmalıdır ve yeniden başlatılmalıdır.

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | hesaplar/dataLakeStoreAccounts | Hayır | Hayır |
> | hesaplar/storageAccounts | Hayır | Hayır |
> | hesaplar/storageAccounts/kapsayıcılar | Hayır | Hayır |
> | hesaplar/Transferanaliz tici | Hayır | Hayır |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

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
> | Databasegeçişleri | Hayır | Hayır |
> | services | Yes | Yes |
> | Hizmetler/Projeler | Yes | Yes |
> | SqlMigrationServices | Yes | Yes |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Backupkasaları | Yes | Yes |
> | Resourcekorumalara | Yes | Yes |

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
> | larý | Yes | Yes |
> | sunucular/danışmanları | Hayır | Hayır |
> | sunucular/anahtarlar | Hayır | Hayır |
> | sunucular/Privateendpointconnectionproxy 'Leri | Hayır | Hayır |
> | sunucular/privateEndpointConnections | Hayır | Hayır |
> | sunucular/privateLinkResources | Hayır | Hayır |
> | sunucular/Querymetinmetinleri | Hayır | Hayır |
> | sunucular/recoverableServers | Hayır | Hayır |
> | Servers/Resetqueryperformanceınsi, Data | Hayır | Hayır |
> | sunucular/başlangıç | Hayır | Hayır |
> | sunucular/durdur | Hayır | Hayır |
> | sunucular/topQueryStatistics | Hayır | Hayır |
> | sunucular/virtualNetworkRules | Hayır | Hayır |
> | sunucular/waitStatistics | Hayır | Hayır |

## <a name="microsoftdbformysql"></a>Microsoft. Dbformyısql

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Flexibtaservers | Yes | Yes |
> | larý | Yes | Yes |
> | sunucular/danışmanları | Hayır | Hayır |
> | sunucular/anahtarlar | Hayır | Hayır |
> | sunucular/Privateendpointconnectionproxy 'Leri | Hayır | Hayır |
> | sunucular/privateEndpointConnections | Hayır | Hayır |
> | sunucular/privateLinkResources | Hayır | Hayır |
> | sunucular/Querymetinmetinleri | Hayır | Hayır |
> | sunucular/recoverableServers | Hayır | Hayır |
> | Servers/Resetqueryperformanceınsi, Data | Hayır | Hayır |
> | sunucular/başlangıç | Hayır | Hayır |
> | sunucular/durdur | Hayır | Hayır |
> | sunucular/topQueryStatistics | Hayır | Hayır |
> | sunucular/yükseltme | Hayır | Hayır |
> | sunucular/virtualNetworkRules | Hayır | Hayır |
> | sunucular/waitStatistics | Hayır | Hayır |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Flexibtaservers | Yes | Yes |
> | Sunucu grupları | Yes | Yes |
> | serverGroupsv2 | Yes | Yes |
> | larý | Yes | Yes |
> | sunucular/danışmanları | Hayır | Hayır |
> | sunucular/anahtarlar | Hayır | Hayır |
> | sunucular/Privateendpointconnectionproxy 'Leri | Hayır | Hayır |
> | sunucular/privateEndpointConnections | Hayır | Hayır |
> | sunucular/privateLinkResources | Hayır | Hayır |
> | sunucular/Querymetinmetinleri | Hayır | Hayır |
> | sunucular/recoverableServers | Hayır | Hayır |
> | Servers/Resetqueryperformanceınsi, Data | Hayır | Hayır |
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
> | hostpools/msıxpackages | Hayır | Hayır |
> | hostpools/oturumkonakları | Hayır | Hayır |
> | hostpools/sessionkonakları/usersessions | Hayır | Hayır |
> | hosthavuzlar/usersessions | Hayır | Hayır |
> | Scalingplanlar | Yes | Yes |
> | çalışma alanı | Yes | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Elaun havuzları | Yes | Yes |
> | Elaun havuzları/ıothubkiracılar | Yes | Yes |
> | Elaor havuzları/ıothubkiracılar/securitySettings | Hayır | Hayır |
> | Iothubs | Yes | Yes |
> | IotHubs/eventGridFilters | Hayır | Hayır |
> | IotHubs/securitySettings | Hayır | Hayır |
> | ProvisioningServices | Yes | Yes |
> | vardır | Hayır | Hayır |

## <a name="microsoftdeviceupdate"></a>Microsoft. DeviceUpdate

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | hesaplar/örnekler | Yes | Yes |
> | registeredSubscriptions | Hayır | Hayır |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | düzenler | Yes | Yes |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | denetleyiciler | Yes | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | labcenters | Yes | Yes |
> | larda | Yes | Yes |
> | Laboratuvarlar/ortamlar | Yes | Yes |
> | Labs/Servicerunanlar | Yes | Yes |
> | Labs/virtualMachines | Yes | Yes |
> | cağını | Yes | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Digitaltwınsınstances | Yes | Yes |
> | Digitaltwınsınstances/endpoints | Hayır | Hayır |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Cassandrakümeler | Yes | Yes |
> | databaseAccountNames | Hayır | Hayır |
> | Veritabanı hesapları | Yes | Yes |
> | Restokıbledatabaseaccounts | Hayır | Hayır |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | etki alanları | Yes | Yes |
> | Domains/Domainownershiptanýmlayýcýlarý | Hayır | Hayır |
> | generateSsoRequest | Hayır | Hayır |
> | topLevelDomains | Hayır | Hayır |
> | Validatedomainregistrationınformation | Hayır | Hayır |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Hayır | Hayır |
> | lcsprojects/clouddağıtımları | Hayır | Hayır |
> | lcsprojects/bağlayıcılar | Hayır | Hayır |

## <a name="microsoftedgeorder"></a>Microsoft. EdgeOrder

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | gideren | Yes | Yes |
> | orderCollections | Yes | Yes |
> | siparişler | Yes | Yes |
> | productFamiliesMetadata | Hayır | Hayır |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | services | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | etki alanları | Yes | Yes |
> | etki alanları/konular | Hayır | Hayır |
> | Eventabonelikleri | Hayır | Hayır |
> | Extensionkonuları | Hayır | Hayır |
> | partnerNamespaces | Yes | Yes |
> | partnerNamespaces/eventChannels | Hayır | Hayır |
> | iş ortağı kayıtları | Yes | Yes |
> | iş ortağı konuları | Yes | Yes |
> | iş ortağı konuları/Eventabonelikleri | Hayır | Hayır |
> | Sistem konuları | Yes | Yes |
> | Sistem konuları/Eventabonelikler | Hayır | Hayır |
> | konuları | Yes | Yes |
> | topicTypes | Hayır | Hayır |

## <a name="microsofteventhub"></a>Microsoft. EventHub

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
> | ad alanları/privateEndpointConnections | Hayır | Hayır |

## <a name="microsoftexperimentation"></a>Microsoft. deneme

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | Yes | Yes |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | öznitelikleri | Yes | Yes |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | featureConfigurations | Hayır | Hayır |
> | featureProviderNamespaces | Hayır | Hayır |
> | Özellik sağlayıcıları | Hayır | Hayır |
> | özellikler | Hayır | Hayır |
> | sağlayıcılarla | Hayır | Hayır |
> | subscriptionFeatureRegistrations | Hayır | Hayır |

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
> | register | Hayır | Hayır |
> | kaynaklar | Hayır | Hayır |
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
> | Oto Managedaccounts | Yes | Yes |
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

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Kümehavuzları | Yes | Yes |
> | Kümehavuzları/kümeler | Yes | Yes |
> | leriniz | Yes | Yes |
> | kümeler/uygulamalar | Hayır | Hayır |

## <a name="microsofthealthbot"></a>Microsoft. HealthBot

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Healthbotlar | Yes | Yes |

## <a name="microsofthealthcareapis"></a>Microsoft. Healthgelişme API 'leri

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | services | Yes | Yes |
> | Hizmetler/ıomtbağlayıcılar | Hayır | Hayır |
> | Hizmetler/ıomtbağlayıcılar/bağlantılar | Hayır | Hayır |
> | Hizmetler/ıomtbağlayıcılar/eşlemeler | Hayır | Hayır |
> | Hizmetler/Privateendpointconnectionproxy 'Leri | Hayır | Hayır |
> | Hizmetler/privateEndpointConnections | Hayır | Hayır |
> | Hizmetler/privateLinkResources | Hayır | Hayır |
> | çalışma alanı | Yes | Yes |
> | çalışma alanları/dicomservices | Yes | Yes |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | larla | Yes | Yes |
> | makineler/assessPatches | Hayır | Hayır |
> | makineler/uzantılar | Yes | Yes |
> | makineler/ınstallpatches | Hayır | Hayır |
> | makineler/privateLinkScopes | Hayır | Hayır |
> | privateLinkScopes | Yes | Yes |
> | privateLinkScopes/Privateendpointconnectionproxy 'Leri | Hayır | Hayır |
> | privateLinkScopes/privateEndpointConnections | Hayır | Hayır |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Veri yöneticileri | Yes | Yes |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | cihazlar | Yes | Yes |
> | networkfunctions işlevleri | Yes | Yes |
> | Networkfunctionsatıcıları | Hayır | Hayır |
> | registeredSubscriptions | Hayır | Hayır |
> | Larını | Hayır | Hayır |
> | Satıcılar/vendorsku 'ları | Hayır | Hayır |
> | Satıcılar/vendorsku 'ları/önizleme abonelikleri | Hayır | Hayır |
> | virtualNetworkFunctions | Yes | Yes |
> | Virtualnetworkfunctionsatıcılarını | Hayır | Hayır |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | bileşenleri | Yes | Yes |
> | networkScopes | Yes | Yes |

## <a name="microsoftimportexport"></a>Microsoft. ımportexport

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Çizelge | Yes | Yes |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | actionGroups | Yes | Yes |
> | activityLogAlerts | Yes | Yes |
> | alertrules | Yes | Yes |
> | autoscalesettings | Yes | Yes |
> | bileşenleri | Yes | Yes |
> | bileşenler/linkedStorageAccounts | Hayır | Hayır |
> | bileşenler/ProactiveDetectionConfigs | Hayır | Hayır |
> | diagnosticSettings | Hayır | Hayır |
> | guestDiagnosticSettings | Yes | Yes |
> | guestDiagnosticSettingsAssociation | Yes | Yes |
> | günlüğe kaydetme profilleri | Yes | Yes |
> | metricAlerts | Yes | Yes |
> | privateLinkScopes | Yes | Yes |
> | privateLinkScopes/privateEndpointConnections | Hayır | Hayır |
> | privateLinkScopes/scopedResources | Hayır | Hayır |
> | Querypack | Yes | Yes |
> | Querypack/sorgular | Hayır | Hayır |
> | scheduledQueryRules | Yes | Yes |
> | Web testleri | Yes | Yes |
> | çalışma kitapları | Yes | Yes |
> | workbooktemplates | Yes | Yes |

## <a name="microsoftintune"></a>Microsoft.Intune

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

## <a name="microsoftiotsecurity"></a>Microsoft. ıotsecurity

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Savunma Derayarları | Hayır | Hayır |

## <a name="microsoftiotspaces"></a>Microsoft. ıotspaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Graf | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft. Keykasası

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | deletedManagedHSMs | Hayır | Hayır |
> | Silinkaults | Hayır | Hayır |
> | hsmPools | Yes | Yes |
> | managedHSMs | Yes | Yes |
> | kasaları | Yes | Yes |
> | kasa/erişim Ilkeleri | Hayır | Hayır |
> | kasa/eventGridFilters | Hayır | Hayır |
> | kasa/anahtarlar | Hayır | Hayır |
> | kasa/anahtarlar/sürümler | Hayır | Hayır |
> | kasa/gizlilikler | Hayır | Hayır |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Connectedkümeler | Yes | Yes |
> | registeredSubscriptions | Hayır | Hayır |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | uzantılardan | Hayır | Hayır |
> | sourceControlConfigurations | Hayır | Hayır |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | leriniz | Yes | Yes |
> | kümeler/attacheddatabaseconfigurations | Hayır | Hayır |
> | kümeler/veritabanları | Hayır | Hayır |
> | kümeler/veritabanları/veri bağlantıları | Hayır | Hayır |
> | kümeler/veritabanları/eventhubconnections | Hayır | Hayır |
> | kümeler/veritabanları/princıpalasbir | Hayır | Hayır |
> | kümeler/veritabanları/betikler | Hayır | Hayır |
> | kümeler/veri bağlantıları | Hayır | Hayır |
> | kümeler/princıpalasbir | Hayır | Hayır |
> | kümeler/parçalar | Hayır | Hayır |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | labaccounts | Yes | Hayır |
> | labplanlar | Yes | Yes |
> | larda | Yes | Yes |
> | kullanıcılar | Hayır | Hayır |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Yes | Yes |
> | Tümleştirme hesapları | Yes | Yes |
> | ıntegrationserviceortamortamları | Yes | Yes |
> | ıntegrationserviceortamortamları/managedap | Hayır | Hayır |
> | ısotedenvironments | Yes | Yes |
> | sürdürülen | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft. Machinöğrenim

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
> | modelinventories | Yes | Yes |
> | virtualkümeler | Yes | Yes |
> | çalışma alanı | Yes | Yes |
> | çalışma alanları/batchEndpoints | Yes | Yes |
> | çalışma alanları/batchEndpoints/dağıtımlar | Yes | Yes |
> | çalışma alanları/batchEndpoints/dağıtımlar/işler | Hayır | Hayır |
> | çalışma alanları/batchEndpoints/işler | Hayır | Hayır |
> | çalışma alanları/kodlar | Hayır | Hayır |
> | çalışma alanları/kodlar/sürümler | Hayır | Hayır |
> | çalışma alanları/hesaplar | Hayır | Hayır |
> | çalışma alanları/veriler | Hayır | Hayır |
> | çalışma alanları/veri depoları | Hayır | Hayır |
> | çalışma alanları/ortamlar | Hayır | Hayır |
> | çalışma alanları/eventGridFilters | Hayır | Hayır |
> | çalışma alanları/işler | Hayır | Hayır |
> | çalışma alanları/labelingJobs | Hayır | Hayır |
> | çalışma alanları/linkedServices | Hayır | Hayır |
> | çalışma alanları/modeller | Hayır | Hayır |
> | çalışma alanları/modeller/sürümler | Hayır | Hayır |
> | çalışma alanları/onlineEndpoints | Yes | Yes |
> | çalışma alanları/onlineEndpoints/dağıtımlar | Yes | Yes |

> [!NOTE]
> Çalışma alanı etiketleri, işlem kümelerine ve işlem örneklerine yayılmaz.

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | applyUpdates | Hayır | Hayır |
> | Configurationatamalar | Hayır | Hayır |
> | maintenanceConfigurations | Yes | Yes |
> | publicMaintenanceConfigurations | Hayır | Hayır |
> | güncelleştirmeler | Hayır | Hayır |

## <a name="microsoftmanagedidentity"></a>Microsoft. Managedıdentity

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Kimlikler | Hayır | Hayır |
> | Userassignedıdentities | Yes | Yes |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | managedNetworks | Yes | Yes |
> | managedNetworks/managedNetworkGroups | Yes | Yes |
> | managedNetworks/managedNetworkPeeringPolicies | Yes | Yes |
> | bildirim | Yes | Yes |

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
> | managementGroups/Settings | Hayır | Hayır |
> | kaynaklar | Hayır | Hayır |
> | startTenantBackfill | Hayır | Hayır |
> | tenantBackfillStatus | Hayır | Hayır |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | hesaplar/oluşturucular | Yes | Yes |
> | hesaplar/eventGridFilters | Hayır | Hayır |
> | hesaplar/privateAtlases | Yes | Yes |

## <a name="microsoftmarketplace"></a>Microsoft. Market

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Macc | Hayır | Hayır |
> | sunar | Hayır | Hayır |
> | offerTypes | Hayır | Hayır |
> | offerTypes/yayımcılar | Hayır | Hayır |
> | offerTypes/yayımcılar/teklifler | Hayır | Hayır |
> | offerTypes/yayımcılar/teklifler/planlar | Hayır | Hayır |
> | offerTypes/yayımcılar/teklifler/planlar/anlaşmalar | Hayır | Hayır |
> | offertypes/yayımcılar/teklifler/planlar/yapılandırmalarını | Hayır | Hayır |
> | offertypes/yayımcılar/teklifler/planlar/yapılandırmalarını/ımportımage | Hayır | Hayır |
> | privategallergıtems | Hayır | Hayır |
> | privateStoreClient | Hayır | Hayır |
> | Privatemağazaların | Hayır | Hayır |
> | Privatemağazaları/Adminrequestapprove | Hayır | Hayır |
> | Privatemağazaların/tekliflerin | Hayır | Hayır |
> | Privatemağazaların/tekliflerin/acknowledgeNotification | Hayır | Hayır |
> | Privatemağazaların/queryNotificationsState | Hayır | Hayır |
> | Privatemağazaları/Requestapproışları | Hayır | Hayır |
> | Privatemağazaları/Requestapproışları/sorgu | Hayır | Hayır |
> | Privatemağazaları/Requestapproışları/çekme planı | Hayır | Hayır |
> | ürün | Hayır | Hayır |
> | Publishers | Hayır | Hayır |
> | Yayımcılar/teklifler | Hayır | Hayır |
> | Yayımcılar/teklifler/Düzeltme | Hayır | Hayır |
> | register | Hayır | Hayır |

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
> | mediaservices/Graphınstances | Hayır | Hayır |
> | mediaservices/Graphtopolojileri | Hayır | Hayır |
> | mediaservices/liveEventOperations | Hayır | Hayır |
> | mediaservices/liveEvents | Yes | Yes |
> | mediaservices/liveEvents/Liveçıktılar | Hayır | Hayır |
> | mediaservices/liveOutputOperations | Hayır | Hayır |
> | mediaservices/Mediagraf | Hayır | Hayır |
> | mediaservices/privateEndpointConnectionOperations | Hayır | Hayır |
> | mediaservices/Privateendpointconnectionproxy 'Leri | Hayır | Hayır |
> | mediaservices/privateEndpointConnections | Hayır | Hayır |
> | mediaservices/streamingEndpointOperations | Hayır | Hayır |
> | mediaservices/streamingEndpoints | Yes | Yes |
> | mediaservices/Streamingkonumlandırıcı | Hayır | Hayır |
> | mediaservices/streamingPolicies | Hayır | Hayır |
> | mediaservices/dönüşümler | Hayır | Hayır |
> | mediaservices/dönüşümler/işler | Hayır | Hayır |
> | Video Çözümleyicileri | Yes | Yes |
> | Video Çözümleyicileri/edgeModules | Hayır | Hayır |

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
> | moveCollections | Yes | Yes |
> | projeyle | Yes | Yes |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Yes | Yes |
> | objectAnchorsAccounts | Yes | Yes |
> | objectUnderstandingAccounts | Yes | Yes |
> | remoteRenderingAccounts | Yes | Yes |
> | spatialAnchorsAccounts | Yes | Yes |

## <a name="microsoftmobilenetwork"></a>Microsoft. MobileNetwork

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Mamak | Yes | Yes |
> | ağlar/siteler | Yes | Yes |
> | Paket çekirdekleri | Yes | Yes |
> | SIMS 'ler | Yes | Yes |
> | SIMS/simProfiles | Yes | Yes |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Yes | Hayır |
> | netAppAccounts/accountBackups | Hayır | Hayır |
> | netAppAccounts/Capacityhavuzları | Yes | Hayır |
> | netAppAccounts/Capacityhavuzları/birimleri | Yes | Hayır |
> | netAppAccounts/Capacityhavuzlar/birimler/anlık görüntüler | Hayır | Hayır |
> | netAppAccounts/Birimgrupları | Hayır | Hayır |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Applicationgateway 'ler | Yes | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Yes | Yes |
> | applicationSecurityGroups | Yes | Yes |
> | azureFirewallFqdnTags | Hayır | Hayır |
> | azureFirewalls | Yes | Hayır |
> | Savunma Konakları | Yes | Hayır |
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
> | frontdoorWebApplicationFirewallManagedRuleSets | Evet, ancak sınırlı ( [aşağıdaki nota](#frontdoor)bakın) | No |
> | frontdoorWebApplicationFirewallPolicies | Evet, ancak sınırlı ( [aşağıdaki nota](#frontdoor)bakın) | Yes |
> | getDnsResourceReference | Hayır | Hayır |
> | ınternalnotify | Hayır | Hayır |
> | ipGroups | Yes | Yes |
> | loadBalancers | Yes | Yes |
> | Localnetworkgateway 'ler | Yes | Yes |
> | Natgateway 'ler | Yes | Yes |
> | Networkıntpolicies Ilkeleri | Yes | Yes |
> | NetworkInterfaces | Yes | Yes |
> | networkProfiles | Yes | Yes |
> | networkSecurityGroups | Yes | Yes |
> | networkWatchers | Yes | Yes |
> | networkWatchers/Connectionmonitörleri | Yes | Hayır |
> | networkWatchers/flowLogs | Yes | Hayır |
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
> | virtualNetworks/alt ağları | Hayır | Hayır |
> | virtualNetworkTaps | Yes | Yes |
> | Virtualwan | Yes | Hayır |
> | Vpngateway 'ler | Yes | Yes |
> | vpnSites | Yes | Yes |
> | webApplicationFirewallPolicies | Yes | Yes |

<a id="frontdoor"></a>

> [!NOTE]
> Azure ön kapı hizmeti için, kaynak oluştururken Etiketler uygulayabilirsiniz, ancak bu durumda etiketleri güncelleştirmek veya eklemek Şu anda desteklenmemektedir.


## <a name="microsoftnotebooks"></a>Microsoft. Not defterleri

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Not defteri proxy 'leri | Hayır | Hayır |

## <a name="microsoftnotificationhubs"></a>Microsoft. Notificationhub 'Lar

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
> | MasterSites | Yes | Yes |
> | Sunucusiteleri | Yes | Yes |
> | VMwareSites | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft. Operationalınsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | leriniz | Yes | Yes |
> | deletedWorkspaces | Hayır | Hayır |
> | Bağlantı hedefleri | Hayır | Hayır |
> | querypack | Yes | Yes |
> | Storageınsii configs | Hayır | Hayır |
> | çalışma alanı | Yes | Yes |
> | çalışma alanları/veri dışarı aktarmaları | Hayır | Hayır |
> | çalışma alanları/veri kaynakları | Hayır | Hayır |
> | çalışma alanları/linkedServices | Hayır | Hayır |
> | çalışma alanları/linkedStorageAccounts | Hayır | Hayır |
> | çalışma alanları/meta veriler | Hayır | Hayır |
> | çalışma alanları/sorgu | Hayır | Hayır |
> | çalışma alanları/Scopedprivatelinkproxy 'Leri | Hayır | Hayır |
> | çalışma alanları/Storageınsii configs | Hayır | Hayır |
> | çalışma alanları/tablolar | Hayır | Hayır |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | managementassociations | Hayır | Hayır |
> | managementconfigurations | Yes | Yes |
> | çözümler | Yes | Yes |
> | görünümler | Yes | Yes |

## <a name="microsoftpeering"></a>Microsoft. eşleme

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Cdnpeeringöneklerini | Hayır | Hayır |
> | Yasallıklar | Hayır | Hayır |
> | peerAsns | Hayır | Hayır |
> | eşlemeleri | Yes | Yes |
> | Peeringserviceülkeleriyle | Hayır | Hayır |
> | peeringServiceProviders | Hayır | Hayır |
> | peeringServices | Yes | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft. Poliyelei

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | belirlediğimizi karşıladığımızı | Hayır | Hayır |
> | eventGridFilters | Hayır | Hayır |
> | Poliyevents | Hayır | Hayır |
> | policyMetadata | Hayır | Hayır |
> | policyStates | Hayır | Hayır |
> | policyTrackedResources | Hayır | Hayır |
> | düzeltmeler | Hayır | Hayır |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> |  konsolları | Hayır | Hayır |
> | panolar | Yes | Yes |
> | tenantconfigurations | Hayır | Hayır |
> | userSettings | Hayır | Hayır |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Privatelinkservicesforpowerbı | Yes | Yes |
> | Kira | Yes | Yes |
> | Kiracılar/çalışma alanları | Hayır | Hayır |
> | workspaceCollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft. Powerbiadanmış

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Oto Islevi çekirdekleri | Yes | Yes |
> | kapasiteler | Yes | Yes |

## <a name="microsoftpowerplatform"></a>Microsoft. PowerPlatform

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | enterprisePolicies | Yes | Yes |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | deletedAccounts | Hayır | Hayır |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Providerkayıtları | Hayır | Hayır |
> | Providerkayıtları/customRollouts | Hayır | Hayır |
> | Providerkayıtları/Defaultrollout | Hayır | Hayır |
> | Providerkayıtlarıyla/resourceTypeRegistrations | Hayır | Hayır |

## <a name="microsoftpurview"></a>Microsoft. purview

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | deletedAccounts | Hayır | Hayır |
> | getDefaultAccount | Hayır | Hayır |
> | removeDefaultAccount | Hayır | Hayır |
> | setDefaultAccount | Hayır | Hayır |

## <a name="microsoftquantum"></a>Microsoft. hisse

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Çalışma Alanları | Yes | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Backupkorunabilir | Hayır | Hayır |
> | kasaları | Yes | Yes |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | Yes | Yes |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | öznitelikleri | Yes | Yes |
> | ad alanları/authorizationrules | Hayır | Hayır |
> | ad alanları/hybridconnections | Hayır | Hayır |
> | ad alanları/hybridconnections/authorizationrules | Hayır | Hayır |
> | ad alanları/privateEndpointConnections | Hayır | Hayır |
> | ad alanları/wcfreyerleştiri | Hayır | Hayır |
> | ad alanları/wcfreyerleştirme/authorizationrules | Hayır | Hayır |

## <a name="microsoftresourceconnector"></a>Microsoft. ResourceConnector

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | yaptı | Yes | Yes |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | lardır | Yes | Yes |
> | resourceChangeDetails | Hayır | Hayır |
> | resourceChanges | Hayır | Hayır |
> | kaynaklar | Hayır | Hayır |
> | resourcesHistory | Hayır | Hayır |
> | subscriptionsStatus | Hayır | Hayır |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Kullanılabilirlik durumları | Hayır | Hayır |
> | Childadvailabilitydurumlar | Hayır | Hayır |
> | childResources | Hayır | Hayır |
> | acil sorun sorunları | Hayır | Hayır |
> | etkinlikler | Hayır | Hayır |
> | ımpactedresources | Hayır | Hayır |
> | meta veriler | Hayır | Hayır |

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | dağıtımlar | Yes | Hayır |
> | dağıtımlar/işlemler | Hayır | Hayır |
> | deploymentScripts | Yes | Yes |
> | deploymentScripts/Günlükler | Hayır | Hayır |
> | Köprü | Hayır | Hayır |
> | sağlayıcılarla | Hayır | Hayır |
> | resourceGroups | Yes | Hayır |
> | Aboneliklerin | Yes | Hayır |
> | Templatespec | Yes | Yes |
> | Templatespec/sürümler | Yes | Yes |
> | Kira | Hayır | Hayır |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | uygulamalar | Yes | Yes |
> | kaynaklar | Yes | Yes |
> | saasresources | Hayır | Hayır |

## <a name="microsoftscvmm"></a>Microsoft. ScVmm

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | larının | Yes | Yes |
> | VirtualMachines | Yes | Yes |
> | VirtualMachineTemplates | Yes | Yes |
> | VirtualNetworks | Yes | Yes |
> | vmmservers | Yes | Yes |

## <a name="microsoftsearch"></a>Microsoft. Search

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
> | alertsSuppressionRules | Hayır | Hayır |
> | allowedConnections | Hayır | Hayır |
> | Applicationwhitedökümler | Hayır | Hayır |
> | assessmentMetadata | Hayır | Hayır |
> | değerlendirme | Hayır | Hayır |
> | Oto Dissalertsrules | Hayır | Hayır |
> | akışlarını otomatikleştirin | Yes | Yes |
> | Oto Provisioningsettings | Hayır | Hayır |
> | Uyumluluklarına | Hayır | Hayır |
> | bağlayıcılar | Hayır | Hayır |
> | dataCollectionAgents | Hayır | Hayır |
> | cihazlar | Hayır | Hayır |
> | deviceSecurityGroups | Hayır | Hayır |
> | discoveredSecuritySolutions | Hayır | Hayır |
> | externalSecuritySolutions | Hayır | Hayır |
> | Informationprotectionpolicies | Hayır | Hayır |
> | ınestionsettings | Hayır | Hayır |
> | eklentisini | Hayır | Hayır |
> | ıotalerts | Hayır | Hayır |
> | ıotalerttypes | Hayır | Hayır |
> | ıotsavunma Dersettings | Hayır | Hayır |
> | ıotönerileri | Hayır | Hayır |
> | iotRecommendationTypes | Hayır | Hayır |
> | ıotsecuritysolutions | Yes | Yes |
> | ıotsecuritysolutions/analiz Ticsmodeller | Hayır | Hayır |
> | ıotsecuritysolutions/Analticsmodeller/Aggreggıt uyarıları | Hayır | Hayır |
> | ıotsecuritysolutions/analiz Ticsmodeller/Aggreg, öneriler | Hayır | Hayır |
> | ıotsecuritysolutions/ıotalerts | Hayır | Hayır |
> | ıotsecuritysolutions/ıotalerttypes | Hayır | Hayır |
> | ıotsecuritysolutions/ıotönerilere | Hayır | Hayır |
> | ıotsecuritysolutions/iotRecommendationTypes | Hayır | Hayır |
> | Iotsensörler | Hayır | Hayır |
> | ıotsites | Hayır | Hayır |
> | Jağaccesspolicies | Hayır | Hayır |
> | Jyer Ilkeleri | Hayır | Hayır |
> | onPremiseIotSensors | Hayır | Hayır |
> | ilkeler | Hayır | Hayır |
> | fiyatlandırmalar | Hayır | Hayır |
> | Reve daha karmaşık bakım standartları | Hayır | Hayır |
> | Rekontrol ve Re, | Hayır | Hayır |
> | Rekontrol ve Re, Re, Re, | Hayır | Hayır |
> | secureScoreControlDefinitions | Hayır | Hayır |
> | secureScoreControls | Hayır | Hayır |
> | Securesçekirdekler | Hayır | Hayır |
> | Securesçekirdekler/secureScoreControls | Hayır | Hayır |
> | securityContacts | Hayır | Hayır |
> | securitySolutions | Hayır | Hayır |
> | securitySolutionsReferenceData | Hayır | Hayır |
> | Securitydurumlardan | Hayır | Hayır |
> | securityStatusesSummaries | Hayır | Hayır |
> | Sunucukullanılabilirliği | Hayır | Hayır |
> | ayarlar | Hayır | Hayır |
> | SQL, Açıbir | Hayır | Hayır |
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
> | automationRules | Hayır | Hayır |
> | yer işaretleri | Hayır | Hayır |
> | çalışmaların | Hayır | Hayır |
> | Veri bağlayıcıları | Hayır | Hayır |
> | dataConnectorsCheckRequirements | Hayır | Hayır |
> | zenginleştirme | Hayır | Hayır |
> | varlıklar | Hayır | Hayır |
> | entityQueries | Hayır | Hayır |
> | entityQueryTemplates | Hayır | Hayır |
> | olaylar | Hayır | Hayır |
> | officeConsents | Hayır | Hayır |
> | ayarlar | Hayır | Hayır |
> | Threatıntelligence | Hayır | Hayır |
> | watchlists | Hayır | Hayır |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | consoleServices | Hayır | Hayır |
> | Seri bağlantı noktaları | Hayır | Hayır |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | öznitelikleri | Yes | Yes |
> | ad alanları/authorizationrules | Hayır | Hayır |
> | ad alanları/diskalrecoveryconfigs | Hayır | Hayır |
> | ad alanları/eventgridfilters | Hayır | Hayır |
> | ad alanları/networkrulesets | Hayır | Hayır |
> | ad alanları/privateEndpointConnections | Hayır | Hayır |
> | ad alanları/kuyruklar | Hayır | Hayır |
> | ad alanları/kuyruklar/authorizationrules | Hayır | Hayır |
> | ad alanları/konular | Hayır | Hayır |
> | ad alanları/konular/authorizationrules | Hayır | Hayır |
> | ad alanları/konular/abonelikler | Hayır | Hayır |
> | ad alanları/konular/abonelikler/kurallar | Hayır | Hayır |
> | premiumMessagingRegions | Hayır | Hayır |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

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
> | managedkümeler | Yes | Yes |
> | managedkümeler/uygulamalar | Hayır | Hayır |
> | managedkümeler/uygulamalar/hizmetler | Hayır | Hayır |
> | managedkümeler/applicationTypes | Hayır | Hayır |
> | managedkümeler/applicationTypes/sürümleri | Hayır | Hayır |
> | managedkümeler/nodetypes | Hayır | Hayır |
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

## <a name="microsoftservicelinker"></a>Microsoft. ServiceLinker

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | okuyamaz | Hayır | Hayır |

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
> | WebPubSub | Yes | Yes |

## <a name="microsoftsingularity"></a>Microsoft. Singular

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | hesaplar/accountQuotaPolicies | Hayır | Hayır |
> | hesaplar/groupPolicies | Hayır | Hayır |
> | hesaplar/işler | Hayır | Hayır |
> | hesaplar/storageContainers | Hayır | Hayır |
> | images | Hayır | Hayır |

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
> | longtermRetentionManagedInstance/longtermRetentionDatabase/longtermRetentionBackup | Hayır | Hayır |
> | longtermRetentionServer/longtermRetentionDatabase/longtermRetentionBackup | Hayır | Hayır |
> | ManagedInstances | Yes | Yes |
> | ManagedInstances/veritabanları | Hayır | Hayır |
> | ManagedInstances/veritabanları/backupShortTermRetentionPolicies | Hayır | Hayır |
> | ManagedInstances/veritabanları/şemalar/tablolar/sütunlar/sensitivityLabels | Hayır | Hayır |
> | ManagedInstances/veritabanları/ | Hayır | Hayır |
> | ManagedInstances/veritabanları/ek | Hayır | Hayır |
> | ManagedInstances/encryptionProtector | Hayır | Hayır |
> | ManagedInstances/anahtarlar | Hayır | Hayır |
> | ManagedInstances/Restokbledroppeddatabases/backupShortTermRetentionPolicies | Hayır | Hayır |
> | ManagedInstances/ | Hayır | Hayır |
> | larý | Yes | Yes |
> | sunucular/Yöneticiler | Hayır | Hayır |
> | sunucular/communicationLinks | Hayır | Hayır |
> | sunucular/veritabanları | Evet ( [aşağıdaki nota](#sqlnote)bakın) | Yes |
> | sunucular/encryptionProtector | Hayır | Hayır |
> | sunucular/firewallRules | Hayır | Hayır |
> | sunucular/anahtarlar | Hayır | Hayır |
> | sunucular/Restokbledroppeddatabases | Hayır | Hayır |
> | Sunucu/hizmet hedefleri | Hayır | Hayır |
> | sunucular/tdeCertificates | Hayır | Hayır |
> | Virtualkümeler | Yes | Yes |

<a id="sqlnote"></a>

> [!NOTE]
> Ana veritabanı etiketleri desteklemez, ancak Azure SYNAPSE Analytics veritabanları da dahil olmak üzere diğer veritabanları, Etiketler ' i destekler. Azure SYNAPSE Analytics veritabanları etkin (duraklatılmış değil) durumda olmalıdır.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Yes | Yes |
> | SqlVirtualMachineGroups/kullanılabilirliği Bilitygrouplisteners | Hayır | Hayır |
> | SqlVirtualMachines | Yes | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | deletedAccounts | Hayır | Hayır |
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
> | amlFilesystems | Yes | Yes |
> | önbelleklerinde | Yes | Yes |
> | önbellekler/Storagetaral | Hayır | Hayır |
> | Usagemodeller | Hayır | Hayır |

## <a name="microsoftstoragereplication"></a>Microsoft. Storagerepce

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Hayır | Hayır |

## <a name="microsoftstoragesync"></a>Microsoft. Storagessync

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

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | ilerinde | Yes | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | leriniz | Yes | Yes |
> | kümeler/privateEndpoints | Hayır | Hayır |
> | streammingjobs | Evet (aşağıdaki nota bakın) | Yes |

> [!NOTE]
> Streamingjobs çalışırken bir etiket ekleyemezsiniz. Etiketi eklemek için kaynağı durdurun.

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | Hayır | Hayır |
> | acceptOwnership | Hayır | Hayır |
> | acceptOwnershipStatus | Hayır | Hayır |
> | deyim | Hayır | Hayır |
> | iptal | Hayır | Hayır |
> | changeTenantRequest | Hayır | Hayır |
> | changeTenantStatus | Hayır | Hayır |
> | CreateSubscription | Hayır | Hayır |
> | seçin | Hayır | Hayır |
> | ilkeler | Hayır | Hayır |
> | yeniden adlandır | Hayır | Hayır |
> | SubscriptionDefinitions | Hayır | Hayır |
> | SubscriptionOperations | Hayır | Hayır |
> | Aboneliklerin | Hayır | Hayır |

## <a name="microsoftsynapse"></a>Microsoft. SYNAPSE

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Privatelinkhub 'Ları | Yes | Yes |
> | çalışma alanı | Yes | Yes |
> | çalışma alanları/bigDataPools | Yes | Yes |
> | çalışma alanları/Operationdurumlar | Hayır | Hayır |
> | çalışma alanları/sqlDatabases | Yes | Yes |
> | çalışma alanları/sqlPools | Yes | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft. Timeseriesınsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | lý | Yes | Hayır |
> | ortamlar/accessPolicies | Hayır | Hayır |
> | ortamlar/EventSources | Yes | Hayır |
> | ortamlar/Privateendpointconnectionproxy 'Leri | Hayır | Hayır |
> | ortamlar/privateEndpointConnections | Hayır | Hayır |
> | ortamlar/privateLinkResources | Hayır | Hayır |
> | ortamlar/Referencedataset 'ler | Yes | Hayır |

## <a name="microsofttoken"></a>Microsoft. Token

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | depolaya | Yes | Yes |
> | depolar/accessPolicies | Hayır | Hayır |
> | depolar/hizmetler | Hayır | Hayır |
> | depolar/hizmetler/belirteçler | Hayır | Hayır |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. Virtualmachineımages

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | ımagetemplates | Yes | Yes |
> | ımagetemplates/Runçıktılar | Hayır | Hayır |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | ArcZones | Yes | Yes |
> | ResourcePools | Yes | Yes |
> | VCenter | Yes | Yes |
> | VCenters/ınventoryıtems | Hayır | Hayır |
> | virtualmachines | Yes | Yes |
> | VirtualMachineTemplates | Yes | Yes |
> | VirtualNetworks | Yes | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Vmwarechoparlör basit

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | ayrılmış Cloudnodes | Yes | Yes |
> | ayrılmış CloudService | Yes | Yes |
> | virtualMachines | Yes | Yes |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | cihazlar | Yes | Yes |
> | registeredSubscriptions | Hayır | Hayır |
> | larını | Hayır | Hayır |
> | satıcılar/SKU 'lar | Hayır | Hayır |
> | satıcılar/vnfs | Hayır | Hayır |
> | Virtualnetworkfunctionsku 'Ları | Hayır | Hayır |
> | vnfs | Yes | Yes |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Hayır |
> | Planlama | Yes | Hayır |
> | registeredSubscriptions | Hayır | Hayır |

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
> | sertifikalar | Yes | Yes |
> | Connectiongateway 'ler | Yes | Yes |
> | bağlantının | Yes | Yes |
> | Customapsıs | Yes | Yes |
> | Silinmi siteleri | Hayır | Hayır |
> | Functionappyığınları | Hayır | Hayır |
> | generateGithubAccessTokenForAppserviceCLI | Hayır | Hayır |
> | hostingEnvironments | Yes | Yes |
> | hostingEnvironments/eventGridFilters | Hayır | Hayır |
> | hostingEnvironments/multiRolePools | Hayır | Hayır |
> | hostingEnvironments/workerPools | Hayır | Hayır |
> | Kubeortamları | Yes | Yes |
> | publishingUsers | Hayır | Hayır |
> | Öneriler | Hayır | Hayır |
> | resourceHealthMetadata | Hayır | Hayır |
> | zamanları | Hayır | Hayır |
> | serverFarms | Yes | Yes |
> | Sunucugrupları/eventGridFilters | Hayır | Hayır |
> | Sunucugrupları/firstPartyApps | Hayır | Hayır |
> | Sunucugrupları/firstPartyApps/keyVaultSettings | Hayır | Hayır |
> | Siteler | Yes | Yes |
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
> | staticSites | Yes | Yes |
> | doğrulamalısınız | Hayır | Hayır |
> | verifyHostingEnvironmentVnet | Hayır | Hayır |
> | Webappyığınları | Hayır | Hayır |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. Windowssavunma Deratp

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Hayır | Hayır |
> | diagnosticSettingsCategories | Hayır | Hayır |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | Çoğulactivationkeys | Yes | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft. Windowsıot

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Yes | Yes |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | migrationAgents | Yes | Yes |
> | lerine | Yes | Yes |
> | iş yükleri/örnekler | Hayır | Hayır |
> | iş yükleri/sürümler | Hayır | Hayır |
> | iş yükleri/sürümler/yapılar | Hayır | Hayır |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Kaynak türü | Etiketleri destekler | Maliyet raporundaki etiket |
> | ------------- | ----------- | ----------- |
> | monitörün | Hayır | Hayır |

## <a name="next-steps"></a>Sonraki adımlar

Kaynaklara etiketlerin nasıl uygulanacağını öğrenmek için bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](tag-resources.md).
