---
title: 800 olmayan kaynaklar sayısı sınırı
description: Bir kaynak grubunda 800 taneden fazla örneğe sahip olabilir Azure Kaynak türlerini listeler.
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: d132773ff35d53dc373c759326efc8179f4993d6
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366547"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Kaynak grubu başına 800 örneğiyle sınırlı kaynak yok

Varsayılan olarak, her kaynak grubunda bir kaynak türünün en fazla 800 örneğini dağıtabilirsiniz. Ancak, bazı kaynak türleri 800 örnek sınırından muaf tutulur. Bu makalede, bir kaynak grubunda 800 ' den fazla örneğe sahip olan Azure Kaynak türleri listelenmektedir. Diğer tüm kaynak türleri 800 örnek ile sınırlıdır.

Bazı kaynak türleri için, 800 örnek sınırının kaldırılmasını sağlamak üzere desteğe başvurmanız gerekir. Bu kaynak türleri bu makalede belirtilmiştir.


## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

* resourceHealthAlertRules
* smartDetectorAlertRules

## <a name="microsoftautomation"></a>Microsoft. Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

* edgeSubscriptions
* Linkedabonelikleri
* kayıtlarında
* kayıt/müşteri abonelikleri
* kayıtlar/ürünler

## <a name="microsoftbotservice"></a>Microsoft. BotService

* botServices-varsayılan olarak 800 örnek ile sınırlıdır. Bu sınır, destek ile iletişim kurarak artırılabilir.

## <a name="microsoftcompute"></a>Microsoft.Compute

* disklerinden
* Galeriler
* Galeriler/görüntüler
* Galeriler/resimler/sürümler
* images
* anlık görüntüler
* virtualMachineScaleSets, varsayılan olarak 800 örnek ile sınırlıdır. Bu sınır, destek ile iletişim kurarak artırılabilir.
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft. Containerınstance

* Kapsayıcı grupları

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

* kayıt defterleri/buildTasks
* kayıt defterleri/buildTasks/listSourceRepositoryProperties
* kayıt defterleri/buildTasks/Steps
* kayıt defterleri/buildTasks/Steps/listBuildArguments
* kayıt defterleri/eventGridFilters
* kayıt defterleri/çoğaltmalar
* kayıt defterleri/görevler
* kayıt defterleri/Web kancaları

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

* larında

## <a name="microsoftdbformariadb"></a>Microsoft. Dbformarıdb

* larý

## <a name="microsoftdbformysql"></a>Microsoft. Dbformyısql

* Flexibtaservers
* larý

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

* Flexibtaservers
* Sunucu grupları
* serverGroupsv2
* larý
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

* cağını

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft. EventHub

* leriniz
* öznitelikleri

## <a name="microsoftexperimentation"></a>Microsoft. deneme

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

* Oto Managedvmconfigurationprofiles
* Configurationprofileatamalar
* Guestconfigurationatamaları
* yazılımıdır
* softwareUpdateProfile
* softwareUpdates

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

* makineler-en fazla 5.000 örneği destekler
* makineler/uzantılar-sınırsız sayıda VM Uzantısı örneğini destekler

## <a name="microsoftinsights"></a>Microsoft. Insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft. Logic

* Tümleştirme hesapları
* sürdürülen

## <a name="microsoftmedia"></a>Microsoft. Media

* mediaservices/liveEvents

## <a name="microsoftnetapp"></a>Microsoft. NetApp

* netAppAccounts
* netAppAccounts/Capacityhavuzları
* netAppAccounts/Capacityhavuzları/birimleri
* netAppAccounts/Capacityhavuzlar/Volumes/Mount hedefleri
* netAppAccounts/Capacityhavuzlar/birimler/anlık görüntüler
* netAppAccounts/Birimgrupları

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* Savunma Konakları
* Ddosprotectionplanlar
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/tümü
* dnszones/kayıt kümeleri
* Networkıntpolicies Ilkeleri
* NetworkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/tümü
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* Publicıpaddresses, varsayılan olarak 800 örnek Ile sınırlıdır. Bu sınır, destek ile iletişim kurarak artırılabilir.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

* workspaceCollections, varsayılan olarak 800 örnek ile sınırlıdır. Bu sınır, destek ile iletişim kurarak artırılabilir.

## <a name="microsoftpowerbidedicated"></a>Microsoft. Powerbiadanmış

* Varsayılan olarak, 800 örneğiyle sınırlı olan oto özellikli çekirdekler. Bu sınır, destek ile iletişim kurarak artırılabilir.
* kapasiteler, varsayılan olarak 800 örnek ile sınırlıdır. Bu sınır, destek ile iletişim kurarak artırılabilir.

## <a name="microsoftrelay"></a>Microsoft. Relay

* öznitelikleri

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

* işlere

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* öznitelikleri

## <a name="microsoftsingularity"></a>Microsoft. Singular

* accounts
* hesaplar/accountQuotaPolicies
* hesaplar/groupPolicies
* hesaplar/işler
* hesaplar/storageContainers

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftsql"></a>Microsoft.Sql

* sunucular/veritabanları

## <a name="microsoftweb"></a>Microsoft. Web

* apiManagementAccounts/API 'ler
* Siteler

## <a name="next-steps"></a>Sonraki adımlar

Tüm kotalar ve limitlerin listesi için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](azure-subscription-service-limits.md).
