---
title: REST API kullanarak Azure Ilke güvenlik ilkeleri oluşturun ve düzenleyin | Microsoft Docs
description: REST API aracılığıyla Azure Ilke ilkesi yönetimi hakkında bilgi edinin.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 56c5ca8c4e1d70e002a338c753f9ab1f0b1aa411
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522027"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Azure Ilkesinde REST API kullanarak bir güvenlik ilkesi yapılandırma

Azure Güvenlik Merkezi, Azure Ilkesi ile yerel tümleştirmenin bir parçası olarak, ilke atamaları oluşturmak için Azure Ilkesinin REST API avantajlarından yararlanmanızı sağlar. Aşağıdaki yönergelerde, ilke atamalarının oluşturulması ve var olan atamaların özelleştirilmesi adım adım açıklanmaktadır. 

Azure Ilkesinde önemli kavramlar: 

- **İlke tanımı** bir kuraldır 

- **Girişim** , ilke tanımlarının koleksiyonudur (kurallar) 

- **Atama** , bir girişim veya ilkenin belirli bir kapsama (Yönetim grubu, abonelik vb.) bir uygulamasıdır. 

Güvenlik Merkezi 'nin tüm güvenlik ilkelerini içeren yerleşik bir girişimi vardır. Güvenlik Merkezi 'nin Azure kaynaklarınızın ilkelerini değerlendirmek için, yönetim grubunda veya değerlendirmek istediğiniz abonelikte bir atama oluşturmanız gerekir.

Yerleşik girişim, tüm güvenlik merkezi ilkelerinin varsayılan olarak etkinleştirilmiş olduğunu içerir. Yerleşik girişimden belirli ilkeleri devre dışı bırakmayı seçebilirsiniz. Örneğin, **Web uygulaması güvenlik duvarı**hariç tüm güvenlik merkezi ilkelerini uygulamak için, ilkenin efekt parametresinin değerini **devre dışı**olarak değiştirin. 

## <a name="api-examples"></a>API örnekleri

Aşağıdaki örneklerde şu değişkenleri değiştirin:

- **{scope}** ilkeyi uyguladığınız yönetim grubunun veya aboneliğin adını girin.
- **{Policyassignmentname}** [ilgili ilke atamasının adını](#policy-names)girin.
- **{Name}** adınızı veya ilke değişikliğini onaylayan yöneticinin adını girin.

Bu örnekte, yerleşik güvenlik merkezi girişiminin bir abonelikte veya yönetim grubunda nasıl atanacağı gösterilmektedir
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

Bu örnekte, aşağıdaki ilkeler devre dışı bırakıldığında yerleşik güvenlik merkezi girişiminin bir abonelikte nasıl atanacağı gösterilmektedir: 

- Sistem güncelleştirmeleri ("systemUpdatesMonitoringEffect") 

- Güvenlik Yapılandırması ("systemConfigurationsMonitoringEffect") 

- Endpoint Protection ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
Bu örnek, bir atamanın nasıl kaldırılacağını gösterir:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## İlke adları başvurusu<a name="policy-names"></a>

|Güvenlik Merkezi 'nde ilke adı|Azure Ilkesinde görünen ilke adı |İlke etkisi parametre adı|
|----|----|----|
|SQL Şifrelemesi |Azure Güvenlik Merkezi 'nde şifrelenmemiş SQL veritabanını izleme |sqlEncryptionMonitoringEffect| 
|SQL Denetimi |Azure Güvenlik Merkezi 'nde denetlenen SQL veritabanını izleme |sqlAuditingMonitoringEffect|
|Sistem güncelleştirmeleri |Azure Güvenlik Merkezi 'nde eksik sistem güncelleştirmelerini izleme |systemUpdatesMonitoringEffect|
|Depolama şifrelemesi |Depolama hesapları için eksik blob şifrelemesini denetle |storageEncryptionMonitoringEffect|
|JıT ağ erişimi |Azure Güvenlik Merkezi 'nde olası ağ tam zamanında (JıT) erişimi izleme |jitNetworkAccessMonitoringEffect |
|Uyarlamalı uygulama denetimleri |Azure Güvenlik Merkezi 'nde olası uygulama beyaz listesini izleme |adaptiveApplicationControlsMonitoringEffect|
|Ağ güvenlik grupları |Azure Güvenlik Merkezi 'nde izin veren ağ erişimini izleme |networkSecurityGroupsMonitoringEffect| 
|Güvenlik yapılandırmaları |Azure Güvenlik Merkezi 'nde işletim sistemi güvenlik açıklarını izleme |systemConfigurationsMonitoringEffect| 
|Uç nokta koruması |Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme |endpointProtectionMonitoringEffect |
|Disk şifrelemesi |Azure Güvenlik Merkezi 'nde şifrelenmemiş VM disklerini izleme |diskEncryptionMonitoringEffect|
|Güvenlik açığı değerlendirmesi |Azure Güvenlik Merkezi 'nde VM güvenlik açıklarını izleme |vulnerabilityAssessmentMonitoringEffect|
|Web uygulaması güvenlik duvarı |Azure Güvenlik Merkezi 'nde korumasız Web uygulamasını izleme |webApplicationFirewallMonitoringEffect |
|Yeni nesil güvenlik duvarı |Azure Güvenlik Merkezi 'nde korumasız ağ uç noktalarını izleme| |


## <a name="next-steps"></a>Sonraki adımlar

Diğer ilgili malzemeler için aşağıdaki makalelere bakın: 

- [Özel güvenlik ilkeleri](custom-security-policies.md)
- [Güvenlik ilkesine genel bakış](tutorial-security-policy.md)