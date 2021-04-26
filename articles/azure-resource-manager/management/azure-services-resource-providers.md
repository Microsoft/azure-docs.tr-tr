---
title: Azure hizmetlerine göre kaynak sağlayıcıları
description: Azure Resource Manager için tüm kaynak sağlayıcısı ad alanlarını listeler ve bu ad alanı için Azure hizmetini gösterir.
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: f33017713d8154fb56472ad5f53b97b22d32e0e3
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167881"
---
# <a name="resource-providers-for-azure-services"></a>Azure hizmetleri için kaynak sağlayıcıları

Bu makalede, kaynak sağlayıcısı ad uzaylarının Azure hizmetleri ile nasıl eşleneceğini gösterir. Kaynak sağlayıcısını bilmiyorsanız bkz. [kaynak sağlayıcısını bulma](#find-resource-provider).

## <a name="match-resource-provider-to-service"></a>Kaynak sağlayıcısını hizmetle Eşleştir

**Kayıt** ile işaretlenen kaynak sağlayıcıları, aboneliğiniz için varsayılan olarak kaydedilir. Daha fazla bilgi için bkz. [kayıt](#registration).

| Kaynak sağlayıcısı ad alanı | Azure hizmeti |
| --------------------------- | ------------- |
| Microsoft. AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft. addons | çekirdeğin |
| Microsoft. ADHybridHealthService- [kayıtlı](#registration) | [Azure Active Directory](../../active-directory/index.yml) |
| Microsoft. Advisor | [Azure Danışmanı](../../advisor/index.yml) |
| Microsoft. AlertsManagement | [Azure İzleyici](../../azure-monitor/index.yml) |
| Microsoft. AnalysisServices | [Azure Analysis Services](../../analysis-services/index.yml) |
| Microsoft. Apimanane | [API Management](../../api-management/index.yml) |
| Microsoft. AppConfiguration | [Azure Uygulama Yapılandırması](../../azure-app-configuration/index.yml) |
| Microsoft. AppPlatform | [Azure Spring Cloud](../../spring-cloud/overview.md) |
| Microsoft. kanıtlama | Azure kanıtlama hizmeti |
| Microsoft. Authorization- [kayıtlı](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft. Automation | [Otomasyon](../../automation/index.yml) |
| Microsoft. AutonomousSystems | [Özerk sistemler](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft. AVS | [Azure VMware Çözümü](../../azure-vmware/index.yml) |
| Microsoft. AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft. AzureArcData | Azure Arc özellikli veri hizmetleri |
| Microsoft. AzureData | SQL Server kayıt defteri |
| Microsoft. AzureStack | çekirdeğin |
| Microsoft. Azurestackhcı | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft. faturalandırma- [kayıtlı](#registration) | [Maliyet Yönetimi ve Faturalandırma](/azure/billing/) |
| Microsoft. BingMaps | [Bing Haritalar](/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft. Blockzinciri | [Azure Blok Zinciri Hizmeti](../../blockchain/workbench/index.yml) |
| Microsoft. BlockchainTokens | [Azure Blok Zinciri Belirteçleri](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft. Blueprint | [Azure Blueprints](../../governance/blueprints/index.yml) |
| Microsoft. BotService | [Azure Bot Hizmeti](/azure/bot-service/) |
| Microsoft. Cache | [Redis için Azure Önbelleği](../../azure-cache-for-redis/index.yml) |
| Microsoft. Capacity | çekirdeğin |
| Microsoft. CDN | [Content Delivery Network](../../cdn/index.yml) |
| Microsoft. CertificateRegistration | [App Service sertifikaları](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft. ChangeAnalysis | [Azure İzleyici](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Klasik dağıtım modeli sanal makinesi |
| Microsoft. ClassicInfrastructureMigrate | Klasik dağıtım modeli geçişi |
| Microsoft. ClassicNetwork | Klasik dağıtım modeli sanal ağı |
| Microsoft. ClassicStorage | Klasik dağıtım modeli depolaması |
| Microsoft. ClassicSubscription- [kayıtlı](#registration) | Klasik dağıtım modeli |
| Microsoft. Biliveservices | [Bilişsel Hizmetler](../../cognitive-services/index.yml) |
| Microsoft. Commerce- [kayıtlı](#registration) | çekirdeğin |
| Microsoft.Compute | [Sanal Makineler](../../virtual-machines/index.yml)<br />[Sanal Makine Ölçek Kümeleri](../../virtual-machine-scale-sets/index.yml) |
| Microsoft. tüketim- [kayıtlı](#registration) | [Maliyet Yönetimi](/azure/cost-management/) |
| Microsoft. Containerınstance | [Container Instances](../../container-instances/index.yml) |
| Microsoft. ContainerRegistry | [Container Registry](../../container-registry/index.yml) |
| Microsoft. ContainerService | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft. CostManagement- [kayıtlı](#registration) | [Maliyet Yönetimi](/azure/cost-management/) |
| Microsoft. CostManagementExports | [Maliyet Yönetimi](/azure/cost-management/) |
| Microsoft. Customerkasası | [Microsoft Azure için Müşteri Kasası](../../security/fundamentals/customer-lockbox-overview.md) |
| Microsoft. CustomProviders | [Azure Özel Sağlayıcılar](../custom-providers/overview.md) |
| Microsoft. DataBox | [Azure Data Box](../../databox/index.yml) |
| Microsoft. DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Microsoft. Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft. DataCatalog | [Veri Kataloğu](../../data-catalog/index.yml) |
| Microsoft. DataFactory | [Data Factory](../../data-factory/index.yml) |
| Microsoft. DataLakeAnalytics | [Data Lake Analytics](../../data-lake-analytics/index.yml) |
| Microsoft. DataLakeStore | [Azure Data Lake Storage 2. Nesil](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft. DataMigration | [Azure Veritabanı Geçiş Hizmeti](../../dms/index.yml) |
| Microsoft. DataProtection | Veri Koruma |
| Microsoft. DataShare | [Azure Veri Paylaşımı](../../data-share/index.yml) |
| Microsoft. Dbformarıdb | [MariaDB için Azure Veritabanı](../../mariadb/index.yml) |
| Microsoft. Dbformyısql | [MySQL için Azure Veritabanı](../../mysql/index.yml) |
| Microsoft. DBforPostgreSQL | [PostgreSQL için Azure Veritabanı](../../postgresql/index.yml) |
| Microsoft. DeploymentManager | [Azure Deployment Manager](../templates/deployment-manager-overview.md) |
| Microsoft. DesktopVirtualization | [Windows Sanal Masaüstü](../../virtual-desktop/index.yml) |
| Microsoft.Devices | [Azure IoT Hub](../../iot-hub/index.yml)<br />[Azure IoT Hub Cihazı Sağlama Hizmeti](../../iot-dps/index.yml) |
| Microsoft. DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft. DevSpaces | [Azure Dev Spaces](../../dev-spaces/index.yml) |
| Microsoft. DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. DigitalTwins | [Azure Digital Twins](../../digital-twins/overview.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft. DomainRegistration | [App Service](../../app-service/index.yml) |
| Microsoft. DynamicsLcs | [Yaşam döngüsü Hizmetleri](https://lcs.dynamics.com/Logon/Index ) |
| Microsoft. EnterpriseKnowledgeGraph | Kurumsal bilgi grafiği |
| Microsoft. EventGrid | [Event Grid](../../event-grid/index.yml) |
| Microsoft. EventHub | [Event Hubs](../../event-hubs/index.yml) |
| Microsoft. Features- [kayıtlı](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft. GuestConfiguration | [Azure İlkesi](../../governance/policy/index.yml) |
| Microsoft. HanaOnAzure | [Azure üzerinde SAP HANA Büyük Örnekleri](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft. HardwareSecurityModules | [Azure Ayrılmış HSM](../../dedicated-hsm/index.yml) |
| Microsoft. HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft. Healthgelişme API 'leri | [FHIR için Azure API'si](../../healthcare-apis/fhir/index.yml) |
| Microsoft. HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft. HybridData | [StorSimple](../../storsimple/index.yml) |
| Microsoft. HybridNetwork  | [Özel kenar bölgeleri](../../networking/edge-zones-overview.md) |
| Microsoft. ımportexport | [Azure İçeri/Dışarı Aktarma](../../import-export/storage-import-export-service.md) |
| Microsoft. Insights | [Azure İzleyici](../../azure-monitor/index.yml) |
| Microsoft. ıotcentral | [Azure IoT Central](../../iot-central/index.yml) |
| Microsoft. ıotspaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft.Intune | [Azure İzleyici](../../azure-monitor/index.yml) |
| Microsoft. Keykasası | [Anahtar Kasası](../../key-vault/index.yml) |
| Microsoft. Kubernetes | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft. KubernetesConfiguration | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.Kusto | [Azure Veri Gezgini](/azure/data-explorer/) |
| Microsoft. LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft. Machinöğrenim | [Machine Learning Studio](../../machine-learning/classic/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft. Maintenance | [Azure Bakımı](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft. Managedıdentity | [Azure kaynakları için yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft. ManagedNetwork | PaaS hizmetleri tarafından yönetilen sanal ağlar |
| Microsoft. ManagedServices | [Azure Lighthouse](../../lighthouse/index.yml) |
| Microsoft. Management | [Yönetim Grupları](../../governance/management-groups/index.yml) |
| Microsoft. Maps | [Azure Haritalar](../../azure-maps/index.yml) |
| Microsoft. Market | çekirdeğin |
| Microsoft. MarketplaceApps | çekirdeğin |
| Microsoft. Marketplacesıralaması- [kayıtlı](#registration) | çekirdeğin |
| Microsoft. Media | [Media Services](../../media-services/index.yml) |
| Microsoft. Microservices4Spring | [Azure Spring Cloud](../../spring-cloud/overview.md) |
| Microsoft. Migrate | [Azure Geçişi](../../migrate/migrate-services-overview.md) |
| Microsoft. MixedReality | [Azure Spatial Anchors](../../spatial-anchors/index.yml) |
| Microsoft. NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Application Gateway](../../application-gateway/index.yml)<br />[Azure Bastion](../../bastion/index.yml)<br />[Azure DDoS Koruması](../../ddos-protection/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure Güvenlik Duvarı](../../firewall/index.yml)<br />[Azure Front Door Hizmeti](../../frontdoor/index.yml)<br />[Azure Özel Bağlantı](../../private-link/index.yml)<br />[Load Balancer](../../load-balancer/index.yml)<br />[Ağ İzleyicisi](../../network-watcher/index.yml)<br />[Traffic Manager](../../traffic-manager/index.yml)<br />[Sanal Ağ](../../virtual-network/index.yml)<br />[Sanal WAN](../../virtual-wan/index.yml)<br />[VPN Gateway](../../vpn-gateway/index.yml)<br /> |
| Microsoft. Not defterleri | [Azure Notebooks](https://notebooks.azure.com/help/introduction) |
| Microsoft. Notificationhub 'Lar | [Notification Hubs](../../notification-hubs/index.yml) |
| Microsoft. ObjectStore | Nesne deposu |
| Microsoft. OffAzure | [Azure Geçişi](../../migrate/migrate-services-overview.md) |
| Microsoft. Operationalınsights | [Azure İzleyici](../../azure-monitor/index.yml) |
| Microsoft. OperationsManagement | [Azure İzleyici](../../azure-monitor/index.yml) |
| Microsoft. eşleme | [Azure Eşleme Hizmeti](../../peering-service/index.yml) |
| Microsoft. Poliyelei | [Azure İlkesi](../../governance/policy/index.yml) |
| Microsoft. Portal- [kayıtlı](#registration) | [Azure portalı](../../azure-portal/index.yml) |
| Microsoft. PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft. Powerbiadanmış | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft. PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft. ProjectBabylon | [Azure Veri Kataloğu](../../data-catalog/overview.md) |
| Microsoft. hisse | [Azure hisse](https://azure.microsoft.com/services/quantum/) |
| Microsoft. RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft. RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft. Relay | [Azure Geçişi](../../azure-relay/relay-what-is-it.md) |
| Microsoft. ResourceGraph- [kayıtlı](#registration) | [Azure Kaynak Grafı](../../governance/resource-graph/index.yml) |
| Microsoft. ResourceHealth | [Azure Hizmet Durumu](../../service-health/index.yml) |
| Microsoft. Resources- [kayıtlı](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft. SaaS | çekirdeğin |
| Microsoft. Scheduler | [Scheduler](../../scheduler/index.yml) |
| Microsoft. Search | [Azure Bilişsel Arama](../../search/index.yml) |
| Microsoft.Security | [Güvenlik Merkezi](../../security-center/index.yml) |
| Microsoft. Securityınsights | [Azure Sentinel](../../sentinel/index.yml) |
| Microsoft. SerialConsole- [kayıtlı](#registration) | [Windows için Azure seri konsol](/troubleshoot/azure/virtual-machines/serial-console-windows) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Microsoft. ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft. Servicefabrickafesi | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft. Services | çekirdeğin |
| Microsoft. SignalRService | [Azure SignalR Hizmeti](../../azure-signalr/index.yml) |
| Microsoft. SoftwarePlan | Lisans |
| Microsoft. Solutions | [Azure Yönetilen Uygulamalar](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Veritabanı](../../azure-sql/database/index.yml)<br /> [Azure SQL Yönetilen Örnek](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. SqlVirtualMachine | [Azure Sanal Makineler'de SQL Server](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [Depolama](../../storage/index.yml) |
| Microsoft. StorageCache | [Azure HPC Önbelleği](../../hpc-cache/index.yml) |
| Microsoft. Storagessync | [Depolama](../../storage/index.yml) |
| Microsoft. StorSimple | [StorSimple](../../storsimple/index.yml) |
| Microsoft. StreamAnalytics | [Azure Akış Analizi](../../stream-analytics/index.yml) |
| Microsoft. Subscription | çekirdeğin |
| Microsoft. support- [kayıtlı](#registration) | çekirdeğin |
| Microsoft. SYNAPSE | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. Timeseriesınsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft. Token | Belirteç |
| Microsoft. Virtualmachineımages | [Azure Görüntü Oluşturucusu](../../virtual-machines/image-builder-overview.md) |
| Microsoft. VisualStudio | [Azure DevOps](/azure/devops/) |
| Microsoft. VMware | [Azure VMware Çözümü](../../azure-vmware/index.yml) |
| Microsoft. Vmwarechoparlör basit | [CloudSimple tarafından sunulan Azure VMware Çözümü](../../vmware-cloudsimple/index.md) |
| Microsoft. VSOnline | [Azure DevOps](/azure/devops/) |
| Microsoft. Web | [App Service](../../app-service/index.yml)<br />[Azure İşlevleri](../../azure-functions/index.yml) |
| Microsoft. Windowssavunma Deratp | [Microsoft Defender Gelişmiş Tehdit Koruması](../../security-center/security-center-wdatp.md) |
| Microsoft. WindowsESU | Genişletilmiş güvenlik güncelleştirmeleri |
| Microsoft. Windowsıot | [Windows 10 IoT Core Services](/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft. WorkloadMonitor | [Azure İzleyici](../../azure-monitor/index.yml) |

## <a name="registration"></a>Kayıt

Üzerinde **kaydedilmiş** olarak işaretlenen kaynak sağlayıcıları, aboneliğiniz için varsayılan olarak kaydedilir. Diğer kaynak sağlayıcılarını kullanmak için, [bunları kaydetmeniz](resource-providers-and-types.md)gerekir. Ancak, belirli eylemleri gerçekleştirdiğinizde birçok kaynak sağlayıcısı sizin için kaydedilir. Örneğin, Portal üzerinden bir kaynak oluşturursanız, Portal, gerekli olan tüm kaydedilmemiş kaynak sağlayıcılarını otomatik olarak kaydeder. Kaynakları bir [Azure Resource Manager şablonuyla](../templates/overview.md)dağıtırken, gerekli kaynak sağlayıcıları da kaydedilir.

> [!IMPORTANT]
> Yalnızca bir kaynak sağlayıcısını kullanmaya hazırsanız kaydolun. Kayıt adımı, aboneliğinizde en az ayrıcalıkları korumanıza olanak sağlar. Kötü amaçlı bir Kullanıcı kayıtlı olmayan kaynak sağlayıcılarını kullanamaz.

## <a name="find-resource-provider"></a>Kaynak sağlayıcısını bul

Azure 'da mevcut altyapıya sahipseniz, ancak hangi kaynak sağlayıcının kullanıldığından emin değilseniz, kaynak sağlayıcısını bulmak için Azure CLı veya PowerShell kullanabilirsiniz. Bulunacak kaynakları içeren kaynak grubunun adını belirtin.

Aşağıdaki örnek Azure CLı kullanır:

```azurecli-interactive
az resource list -g examplegroup
```

Sonuçlar kaynak türünü içerir. Kaynak sağlayıcısı ad alanı, kaynak türünün ilk kısmıdır. Aşağıdaki örnekte **Microsoft. Keykasası** kaynak sağlayıcısı gösterilmektedir.

```json
[
  {
    ...
    "type": "Microsoft.KeyVault/vaults"
  }
]
```

Aşağıdaki örnek PowerShell kullanır:

```azurepowershell-interactive
Get-AzResource -ResourceGroupName examplegroup
```

Sonuçlar kaynak türünü içerir. Kaynak sağlayıcısı ad alanı, kaynak türünün ilk kısmıdır. Aşağıdaki örnekte **Microsoft. Keykasası** kaynak sağlayıcısı gösterilmektedir.

```azurepowershell
Name              : examplekey
ResourceGroupName : examplegroup
ResourceType      : Microsoft.KeyVault/vaults
...
```

## <a name="next-steps"></a>Sonraki adımlar

Kaynak sağlayıcısını kaydetme dahil olmak üzere kaynak sağlayıcıları hakkında daha fazla bilgi için bkz. [Azure kaynak sağlayıcıları ve türleri](resource-providers-and-types.md).
