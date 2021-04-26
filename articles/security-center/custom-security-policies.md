---
title: Azure Güvenlik Merkezi 'nde özel güvenlik ilkeleri oluşturma | Microsoft Docs
description: Azure Güvenlik Merkezi tarafından izlenen Azure özel ilke tanımları.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.author: memildin
zone_pivot_groups: manage-asc-initiatives
ms.openlocfilehash: a41696ba92757550f9cbaa08ccf78d9a5da528d2
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718908"
---
# <a name="create-custom-security-initiatives-and-policies"></a>Özel güvenlik girişimleri ve ilkeleri oluşturma

Azure Güvenlik Merkezi, sistem ve ortamınızın güvenliğini sağlamaya yardımcı olmak için güvenlik önerileri oluşturur. Bu öneriler, tüm müşterilere sağlanan genel, varsayılan güvenlik ilkesine dahil olan sektör en iyi uygulamalarına dayalıdır. Ayrıca, güvenlik merkezi 'nin sektör ve mevzuat standartları hakkında bilgi edinmeleri de gelebilir.

Bu özellikle kendi *özel* girişimlerinizi ekleyebilirsiniz. Daha sonra ortamınız oluşturduğunuz ilkeleri izmazsa öneriler alacaksınız. Oluşturmakta olduğunuz özel girişimler, mevzuata uygunluk panosundaki yerleşik girişimlerle birlikte görünür ve bu da [yasal uyumluluğun geliştirilmesi](security-center-compliance-dashboard.md)öğreticide açıklanmıştır.

[Azure ilkesi belgelerinde](../governance/policy/concepts/definition-structure.md#definition-location)açıklandığı gibi, özel girişimleriniz için bir konum belirttiğinizde, bir yönetim grubu veya abonelik olması gerekir. 

> [!TIP]
> Bu sayfadaki temel kavramlara genel bakış için bkz. [güvenlik ilkeleri, girişimler ve öneriler nelerdir?](security-policy-concept.md).

::: zone pivot="azure-portal"

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Aboneliğinize özel bir girişim eklemek için 

1. Güvenlik Merkezi 'nin kenar çubuğundan **güvenlik ilkesi** sayfasını açın.

1. Özel bir girişim eklemek istediğiniz bir abonelik veya yönetim grubu seçin.

    [![Özel ilkenizi oluşturacağınız bir abonelik seçme](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Güvenlik Merkezi 'nde değerlendirilmek ve gösterilmesi için, abonelik düzeyinde (veya üzeri) özel standartlar eklemeniz gerekir. 
    >
    > Özel bir standart eklediğinizde, bu kapsama bir *girişim* atar. Bu nedenle, bu atama için gereken en geniş kapsamı seçmenizi öneririz.

1. Güvenlik İlkesi sayfasında, özel girişimlerinizin altında **özel girişim Ekle**' ye tıklayın.

    [![Özel girişim Ekle 'ye tıklayın](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Aşağıdaki sayfa görünür:

    ![İlke oluşturma veya ekleme](media/custom-security-policies/create-or-add-custom-policy.png)

1. Özel girişimler Ekle sayfasında, kuruluşunuzda önceden oluşturulmuş özel ilkelerin listesini gözden geçirin. Aboneliğinize atamak istediğiniz bir tane görürseniz **Ekle**' ye tıklayın. Listede gereksinimlerinizi karşılayan bir girişim yoksa, bu adımı atlayın.

1. Yeni bir özel girişim oluşturmak için:

    1. **Yeni oluştur**' a tıklayın.
    1. Tanımın konumunu ve adını girin.
    1. Dahil edilecek ilkeleri seçin ve **Ekle**' ye tıklayın.
    1. İstediğiniz parametreleri girin.
    1. **Kaydet**’e tıklayın.
    1. Özel girişimler Ekle sayfasında Yenile ' ye tıklayın. Yeni girişim kullanılabilir olarak gösterilir.
    1. **Ekle** ' ye tıklayın ve aboneliğinize atayın.

    > [!NOTE]
    > Yeni girişimler oluşturmak için abonelik sahibi kimlik bilgileri gerekir. Azure rolleri hakkında daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'Nde izinler](security-center-permissions.md).

    Yeni girişim devreye girer ve etkiyi aşağıdaki iki yolla görebilirsiniz:

    * Güvenlik Merkezi kenar çubuğundan Ilke & uyumluluğu altında, **mevzuat uyumluluğu**' nu seçin. Uyumluluk panosu, yeni özel girişiminizi yerleşik girişimlerle birlikte göstermek için açılır.
    
    * Ortamınız tanımladığınız ilkeleri izmazsa öneriler almaya başlar.

1. İlkenize yönelik sonuç önerilerini görmek için kenar çubuğundan **öneriler** ' e tıklayarak öneriler sayfasını açın. Öneriler "özel" bir etiketle görüntülenir ve yaklaşık bir saat içinde kullanılabilir.

    [![Özel öneriler](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

::: zone-end

::: zone pivot="rest-api"

## <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Azure Ilkesinde REST API kullanarak bir güvenlik ilkesi yapılandırma

Azure Güvenlik Merkezi, Azure Ilkesi ile yerel tümleştirmenin bir parçası olarak, ilke atamaları oluşturmak için Azure Ilkesinin REST API avantajlarından yararlanmanızı sağlar. Aşağıdaki yönergelerde, ilke atamalarının oluşturulması ve var olan atamaların özelleştirilmesi adım adım açıklanmaktadır. 

Azure Ilkesinde önemli kavramlar: 

- **İlke tanımı** bir kuraldır 

- **Girişim** , ilke tanımlarının koleksiyonudur (kurallar) 

- **Atama** , bir girişim veya ilkenin belirli bir kapsama (Yönetim grubu, abonelik vb.) bir uygulamasıdır. 

Güvenlik Merkezi 'nin tüm güvenlik ilkelerini içeren yerleşik bir girişimi olan [Azure Güvenlik kıyaslaması](https://docs.microsoft.com/security/benchmark/azure/introduction)vardır. Güvenlik Merkezi 'nin Azure kaynaklarınızın ilkelerini değerlendirmek için, yönetim grubunda veya değerlendirmek istediğiniz abonelikte bir atama oluşturmanız gerekir.

Yerleşik girişim, tüm güvenlik merkezi ilkelerinin varsayılan olarak etkinleştirilmiş olduğunu içerir. Yerleşik girişimden belirli ilkeleri devre dışı bırakmayı seçebilirsiniz. Örneğin, **Web uygulaması güvenlik duvarı** hariç tüm güvenlik merkezi ilkelerini uygulamak için, ilkenin efekt parametresinin değerini **devre dışı** olarak değiştirin.

## <a name="api-examples"></a>API örnekleri

Aşağıdaki örneklerde şu değişkenleri değiştirin:

- **{scope}** ilkeyi uyguladığınız yönetim grubunun veya aboneliğin adını girin
- **{Policyassignmentname}** ilgili ilke atamasının adını girin
- **{Name}** adınızı veya ilke değişikliğini onaylayan yöneticinin adını girin

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

::: zone-end


## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>Ayrıntılı bilgilerle özel önerilerinizi geliştirin

Azure Güvenlik Merkezi ile sağlanan yerleşik öneriler, önem düzeyleri ve Düzeltme yönergeleri gibi ayrıntıları içerir. Bu tür bilgileri özel önerilerinizi eklemek istiyorsanız Azure portal görünmesini veya önerilerinizi her yerden eriştiğinizde, REST API kullanmanız gerekir. 

Ekleyebileceğiniz iki bilgi türü şunlardır:

- **Düzeltici açıklama** – dize
- **Önem derecesi** – enum [düşük, orta, yüksek]

Meta veriler, özel girişim 'nin parçası olan bir ilkenin ilke tanımına eklenmelidir. Aşağıda gösterildiği gibi ' securityCenter ' özelliğinde olması gerekir:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

Meta veri/securityCenter özelliği de dahil olmak üzere özel ilkeye bir örnek aşağıda verilmiştir:

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

SecurityCenter özelliğini kullanmanın başka bir örneği için [REST API belgelerinin bu bölümüne](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples)bakın.


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, özel güvenlik ilkeleri oluşturmayı öğrendiniz. 

Diğer ilgili malzemeler için aşağıdaki makalelere bakın: 

- [Güvenlik ilkelerine genel bakış](tutorial-security-policy.md)
- [Yerleşik güvenlik ilkelerinin listesi](./policy-reference.md)