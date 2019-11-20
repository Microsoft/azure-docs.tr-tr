---
title: Uzantı kaynak türleri
description: Azure Kaynak türleri, diğer kaynak türlerinin yeteneklerini genişletmek için kullanılır.
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 16dcd4cccea4445b6839ac639094d18e29ed49a9
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147027"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Diğer kaynakların özelliklerini genişleten kaynak türleri

Uzantı kaynağı, başka bir kaynağın özelliklerine ekleyen bir kaynaktır. Örneğin, kaynak kilidi bir uzantı kaynağıdır. Başka bir kaynağa bir kaynak kilidi uygulayarak bunun silinmesini veya değiştirilmesini önleyebilirsiniz. Kendi başına bir kaynak kilidi oluşturmak mantıklı değildir. Uzantı kaynağı her zaman başka bir kaynağa uygulanır.

## <a name="extension-resource-types"></a>Uzantı kaynak türleri

- Microsoft. Advisor/Configurations
- Microsoft. Advisor/öneriler
- Microsoft. Advisor/suppressions
- Microsoft. AlertsManagement/uyarılar
- Microsoft. AlertsManagement/alertsSummary
- Microsoft. Authorization/checkAccess
- Microsoft. Authorization/Denyasatamaları
- Microsoft. Authorization/kilitleri
- Microsoft. Authorization/Permissions
- Microsoft. Authorization/Poliyasatamaları
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/Roleatamalar
- Microsoft. Authorization/roleDefinitions
- Microsoft. faturalandırma/Billingdönemler
- Microsoft. faturalandırma/billingPermissions
- Microsoft. faturalandırma/Billingroleatamaları
- Microsoft. faturalandırma/billingRoleDefinitions
- Microsoft. faturalandırma/Createbillingroleatama
- Microsoft. Blueprint/Blueprintasbir
- Microsoft. Blueprint/planlar
- Microsoft. tüketim/Aggregmalyt maliyeti
- Microsoft. tüketim/bakiyeleri
- Microsoft. tüketim/bütçeler
- Microsoft. tüketim/ücretler
- Microsoft. tüketim/CostTags
- Microsoft. tüketim/tahminler
- Microsoft. tüketim/pazar yerleri
- Microsoft. tüketim/OperationResults
- Microsoft. tüketim/OperationStatus
- Microsoft. tüketim/Pricesheets
- Microsoft. tüketim/Rezervationdetails
- Microsoft. tüketim/Rezervationönerilere
- Microsoft. tüketim/Rezervationözetler
- Microsoft. tüketim/Rezervationtransactions
- Microsoft. tüketim/Etiketler
- Microsoft. tüketim/koşullar
- Microsoft. tüketim/UsageDetails
- Microsoft. tüketim/krediler
- Microsoft. tüketim/olaylar
- Microsoft. tüketim/lotlar
- Microsoft. tüketim/ürünler
- Microsoft. tüketim/kiracılar
- Microsoft. Containerınstance/serviceAssociationLinks
- Microsoft. CostManagement/uyarılar
- Microsoft. CostManagement/bütçeleri
- Microsoft. CostManagement/Dimensions
- Microsoft. CostManagement/dışarı aktarmalar
- Microsoft. CostManagement/Externalabonelikleri
- Microsoft. CostManagement/tahmin
- Microsoft. CostManagement/Query
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/Reports
- Microsoft. CostManagement/views
- Microsoft. CostManagement/showbackRules
- Microsoft. CustomProviders/ilişkilendirmeleri
- Microsoft. EventGrid/Eventabonelikleri
- Microsoft. EventGrid/Extensionkonuları
- Microsoft. GuestConfiguration/Configurationprofileatamalar
- Microsoft. GuestConfiguration/Guestconfigurationatamaları
- Microsoft. GuestConfiguration/yazılım
- Microsoft. GuestConfiguration/softwareUpdateProfile
- Microsoft. GuestConfiguration/softwareUpdates
- Microsoft. Insights/automatedExportSettings
- Microsoft. Insights/Baseline
- Microsoft. Insights/calculatebaseline
- Microsoft. Insights/diagnosticSettings
- Microsoft. Insights/diagnosticSettingsCategories
- Microsoft. Insights/eventTypes
- Microsoft. Insights/extendedDiagnosticSettings
- Microsoft. Insights/guestDiagnosticSettingsAssociation
- Microsoft. Insights/logDefinitions
- Microsoft. Insights/Günlükler
- Microsoft. Insights/metricDefinitions
- Microsoft. Insights/metricNamespaces
- Microsoft. Insights/metrictemeller
- Microsoft. Insights/ölçümler
- Microsoft. Insights/Mykitaplarında
- Microsoft. Insights/vmInsightsOnboardingStatuses
- Microsoft. KubernetesConfiguration/sourceControlConfigurations
- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/Configurationatamalar
- Microsoft. Maintenance/Updates
- Microsoft. Managedıdentity/kimlikler
- Microsoft. ManagedServices/Registrationatamaları
- Microsoft. ManagedServices/registrationDefinitions
- Microsoft. Operationalınsights/Storageınsiizconfigs
- Microsoft. OperationsManagement/managementassociations
- Microsoft. Poliyeleghts/Poliyevents
- Microsoft. Poliyeleghts/policyStates
- Microsoft. Poliyeleghts/policyTrackedResources
- Microsoft. Poliyeleghts/düzeltmelere
- Microsoft. RecoveryServices/Backupkoruyucuteditems
- Microsoft. ResourceHealth/kullanılabilirliği Bilitydurumlar
- Microsoft. ResourceHealth/Childadvailabilitydurumlar
- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/olayları
- Microsoft. ResourceHealth/ımpactedresources
- Microsoft. ResourceHealth/bildirimleri
- Microsoft. resources/Links
- Microsoft. resources/etiketleri
- Microsoft. Security/Zorluslar
- Microsoft. Security/ınformationprotectionpolicies
- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/değerlendirmeleri
- Microsoft. Security/Karmaşıkanceresults
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/dataCollectionResults
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/networkData
- Microsoft. Security/serversecurity, açıklıkları
- Microsoft. Securityınsights/toplamalar
- Microsoft. Securityınsights/Alertrutatemplates
- Microsoft. Securityınsights/alertRules
- Microsoft. Securityınsights/yer işaretleri
- Microsoft. Securityınsights/durumlar
- Microsoft. Securityınsights/veri bağlayıcıları
- Microsoft. Securityınsights/varlıklar
- Microsoft. Securityınsights/entityQueries
- Microsoft. Securityınsights/officeConsents
- Microsoft. Securityınsights/ayarlar
- Microsoft. SoftwarePlan/Hybriduseavantajlar
- Microsoft. Subscription/CreateSubscription
- Microsoft. support/createsupportbilet
- Microsoft. support/supportbiletleri
- Microsoft. WorkloadMonitor/bileşenleri
- Microsoft. WorkloadMonitor/Monitorınstances
- Microsoft. WorkloadMonitor/izleyicileri
- Microsoft. WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Sonraki adımlar

- Bir Azure Resource Manager şablonundaki uzantı kaynağı için kaynak KIMLIĞINI almak üzere [Extensionresourceıd](resource-group-template-functions-resource.md#extensionresourceid)kullanın.
- Bir şablonda uzantı kaynağı oluşturma örneği için [Event Grid olay abonelikleri](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)' ne bakın.
