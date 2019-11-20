---
title: Yönetilen kimliğe sahip Azure yönetilen uygulaması
description: Mevcut kaynaklara bağlanmak, Azure kaynaklarını yönetmek ve etkinlik günlüğü için işlemsel kimlik sağlamak üzere yönetilen kimlik ile yönetilen uygulamayı yapılandırın.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 9e1f5072921104c749a0acef95b7da09f1cbb662
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330218"
---
# <a name="azure-managed-application-with-managed-identity"></a>Yönetilen kimliğe sahip Azure yönetilen uygulaması

> [!NOTE]
> Yönetilen uygulamalar için yönetilen kimlik desteği şu anda önizleme aşamasındadır. Yönetilen kimlik kullanmak için lütfen 2018-09-01-Preview API sürümünü kullanın.

Yönetilen bir uygulamanın yönetilen bir kimlik içermesi için nasıl yapılandırılacağını öğrenin. Yönetilen kimlik, müşterinin yönetilen uygulamaya diğer mevcut kaynaklara erişmesini sağlamak için kullanılabilir. Kimlik, Azure platformu tarafından yönetilir ve herhangi bir gizli dizi sağlamanızı veya döndürmenizi gerektirmez. Azure Active Directory (AAD) içindeki yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md).

Uygulamanıza iki tür kimlik verilebilir:

- **Sistem tarafından atanan bir kimlik** uygulamanıza bağlanır ve uygulamanız silinirse silinir. Uygulamanın yalnızca bir sistem tarafından atanmış kimliği olabilir.
- **Kullanıcı tarafından atanan bir kimlik** , uygulamanıza atanabilecek tek başına bir Azure kaynağıdır. Bir uygulamada birden çok kullanıcı tarafından atanan kimlik olabilir.

## <a name="how-to-use-managed-identity"></a>Yönetilen kimliği kullanma

Yönetilen kimlik, yönetilen uygulamalar için birçok senaryoya izin vermez. Çözülebilinen bazı yaygın senaryolar şunlardır:

- Mevcut Azure kaynaklarına bağlı bir yönetilen uygulama dağıtma. Bir örnek, [mevcut bir ağ arabirimine](../virtual-network/virtual-network-network-interface-vm.md)bağlı yönetilen uygulama Içinde bir Azure sanal MAKINESI (VM) dağıtmakta bir örnektir.
- Yönetilen uygulama ve yayımcı **tarafından yönetilen kaynak grubu**dışındaki Azure kaynaklarına erişim izni veriliyor.
- Azure 'daki etkinlik günlüğü ve diğer hizmetler için yönetilen uygulamalar için işletimsel kimlik sağlama.

## <a name="adding-managed-identity"></a>Yönetilen kimlik ekleniyor

Yönetilen bir kimlikle yönetilen bir uygulama oluşturmak için Azure kaynağında ek bir özelliğin ayarlanması gerekir. Aşağıdaki örnek bir örnek **kimlik** özelliği göstermektedir:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Şu **kimliğe**sahip bir yönetilen uygulama oluşturmanın iki yaygın yolu vardır: [createuıdefinition. JSON](./create-uidefinition-overview.md) ve [Azure Resource Manager şablonları](../azure-resource-manager/resource-group-authoring-templates.md). Basit tek oluşturma senaryolarında, daha zengin bir deneyim sağladığından, yönetilen kimliği etkinleştirmek için Createuıdefinition kullanılmalıdır. Ancak, otomatik veya birden çok yönetilen uygulama dağıtımı gerektiren gelişmiş veya karmaşık sistemlerle ilgilenirken, şablonlar kullanılabilir.

### <a name="using-createuidefinition"></a>Createuıdefinition kullanma

Yönetilen bir uygulama, yönetilen kimlik ile [Createuıdefinition. JSON](./create-uidefinition-overview.md)aracılığıyla yapılandırılabilir. [Çıktılar bölümünde](./create-uidefinition-overview.md#outputs), `managedIdentity` anahtarı yönetilen uygulama şablonunun kimlik özelliğini geçersiz kılmak için kullanılabilir. Örnek olarak, yönetilen uygulama üzerinde **sistem tarafından atanan** kimlik etkinleştirilir. Daha karmaşık kimlik nesneleri, tüketiciden giriş istemek için Createuıdefinition öğeleri kullanılarak oluşturulabilir. Bu girişler, **Kullanıcı tarafından atanan kimlik**Ile yönetilen uygulamalar oluşturmak için kullanılabilir.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Yönetilen kimlik için Createuıdefinition ne zaman kullanılır?

Yönetilen uygulamalarda yönetilen kimliği etkinleştirmek için Createuıdefinition 'ın ne zaman kullanılacağı hakkında bazı öneriler aşağıda verilmiştir.

- Yönetilen uygulama oluşturma Azure portal veya Market aracılığıyla gider.
- Yönetilen kimlik, karmaşık tüketici girişi gerektirir.
- Yönetilen kimlik, yönetilen uygulama oluşturulurken gereklidir.

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned Createuıdefinition

Yönetilen uygulama için SystemAssigned kimliğini sağlayan temel bir Createuıdefinition.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned Createuıdefinition

**Kullanıcı tarafından atanan bir kimlik** kaynağını giriş olarak alan ve yönetilen uygulama Için userassigned Identity sağlayan temel bir Createuıdefinition.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

Yukarıdaki Createuıdefinition. JSON, bir tüketicinin **Kullanıcı tarafından atanan kimlik** Azure kaynak kimliği 'ni girmesi için bir metin kutusuna sahip bir Kullanıcı oluşturma deneyimi oluşturur. Oluşturulan deneyim şöyle görünür:

![Örnek Kullanıcı tarafından atanan kimlik Createuıdefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager şablonları kullanma

> [!NOTE]
> Market yönetilen uygulama şablonları, Azure portal oluşturma deneyiminden geçen müşteriler için otomatik olarak oluşturulur.
> Bu senaryolar için Createuıdefinition üzerindeki `managedIdentity` Çıkış anahtarı kimliği etkinleştirmek için kullanılmalıdır.

Yönetilen kimlik Azure Resource Manager şablonlar aracılığıyla da etkinleştirilebilir. Örnek olarak, yönetilen uygulama üzerinde **sistem tarafından atanan** kimlik etkinleştirilir. Daha karmaşık kimlik nesneleri, giriş sağlamak için Azure Resource Manager şablon parametreleri kullanılarak oluşturulabilir. Bu girişler, **Kullanıcı tarafından atanan kimlik**Ile yönetilen uygulamalar oluşturmak için kullanılabilir.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Yönetilen kimlik için Azure Resource Manager şablonları ne zaman kullanılır

Yönetilen uygulamalarda yönetilen kimliği etkinleştirmek için Azure Resource Manager şablonlarının ne zaman kullanılacağı hakkında bazı öneriler aşağıda verilmiştir.

- Yönetilen uygulamalar, bir şablona göre programlı bir şekilde dağıtılabilir.
- Yönetilen kimlik için özel rol atamaları yönetilen uygulamayı sağlamak için gereklidir.
- Yönetilen uygulamanın Azure portal ve Market oluşturma akışına ihtiyacı yoktur.

#### <a name="systemassigned-template"></a>SystemAssigned şablonu

Yönetilen bir uygulamayı **sistem tarafından atanan** kimliğe dağıtan temel bir Azure Resource Manager şablonu.

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>Kullanıcı tarafından atanan şablon

Yönetilen bir uygulamayı **Kullanıcı tarafından atanan bir kimlikle**dağıtan temel bir Azure Resource Manager şablonu.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Azure kaynaklarına erişim verme

Yönetilen uygulamaya bir kimlik verildiğinde, mevcut Azure kaynaklarına erişim izni verilebilir. Bu işlem, Azure portal erişim denetimi (ıAM) arabirimi aracılığıyla yapılabilir. Yönetilen uygulamanın adı veya **Kullanıcı tarafından atanan kimliğin** bir rol ataması eklemek için aranabilir olması olabilir.

![Yönetilen uygulama için rol ataması ekleme](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Mevcut Azure kaynaklarını bağlama

> [!NOTE]
> Yönetilen uygulama dağıtılmadan önce **Kullanıcı tarafından atanan bir kimliğin** [yapılandırılması](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) gerekir. Ayrıca, yönetilen uygulamaların bağlantılı kaynak dağıtımı yalnızca **Market** türü için desteklenir.

Yönetilen kimlik Ayrıca, dağıtımı sırasında var olan kaynaklara erişmesi gereken bir yönetilen uygulamayı dağıtmak için de kullanılabilir. Yönetilen uygulama müşteri tarafından sağlandığında, **Maintemplate** dağıtımına ek yetkilendirmeler sağlamak için **Kullanıcı tarafından atanan kimlikler** eklenebilir.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Createuıdefinition 'ı bağlantılı kaynakla yazma

Yönetilen uygulamanın dağıtımını mevcut kaynaklara bağlarken, hem mevcut Azure kaynağı hem de söz konusu kaynak üzerinde geçerli rol ataması ile **Kullanıcı tarafından atanan bir kimlik** sağlanmalıdır.

 İki giriş gerektiren örnek bir Createuıdefinition: bir ağ arabirimi kaynak KIMLIĞI ve Kullanıcı tarafından atanan kimlik kaynak kimliği.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Bu Createuıdefinition. JSON, iki alanı olan bir oluşturma kullanıcı deneyimi oluşturur. İlk alan, kullanıcının yönetilen uygulama dağıtımına bağlanan kaynak için Azure Kaynak KIMLIĞI 'ne girmesine izin verir. İkincisi, bir tüketicinin bağlantılı Azure kaynağına erişimi olan **Kullanıcı tarafından atanan kimlik** Azure kaynak kimliği 'ni girmesi içindir. Oluşturulan deneyim şöyle görünür:

![İki girişi olan örnek Createuıdefinition: bir ağ arabirimi kaynak KIMLIĞI ve Kullanıcı tarafından atanan kimlik kaynak KIMLIĞI](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Ana şablonu bağlantılı kaynakla yazma

Createuıdefinition güncellenmesinin yanı sıra, geçirilen bağlantılı kaynak KIMLIĞINI kabul etmek için ana şablonun da güncelleştirilmesi gerekir. Ana şablon yeni bir parametre ekleyerek yeni çıktıyı kabul edecek şekilde güncelleştirilemeyebilir. @No__t-0 çıktısı oluşturulan yönetilen uygulama şablonundaki değeri geçersiz kıldığından, ana şablona geçirilmez ve parametreler bölümüne eklenmemelidir.

Ağ profilini Createuıdefinition tarafından sağlanmış mevcut bir ağ arabirimine ayarlayan örnek bir ana şablon.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Yönetilen uygulamayı bağlantılı kaynakla kullanma

Yönetilen uygulama paketi oluşturulduktan sonra, yönetilen uygulama Azure portal aracılığıyla tüketilebilir. Tüketilebilmesi için, birkaç önkoşul adımı vardır.

- Gerekli bağlantılı Azure kaynağının bir örneği oluşturulmalıdır.
- **Kullanıcı tarafından atanan kimlik** [oluşturulmalı ve bağlı kaynağa rol atamaları verilmelidir](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) .
- Mevcut bağlantılı kaynak KIMLIĞI ve **Kullanıcı tarafından atanan kimlik** kimliği Createuıdefinition 'a sağlanır.

## <a name="accessing-the-managed-identity-token"></a>Yönetilen kimlik belirtecine erişme

Yönetilen uygulamanın belirtecine artık yayımcı kiracısından `listTokens` API 'si üzerinden erişilebilir. Örnek bir istek şöyle görünebilir:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

İstek gövdesi parametreleri:

Parametre | Gereklidir | Açıklama
---|---|---
authorizationAudience | *eşleşen* | Hedef kaynağın uygulama KIMLIĞI URI 'SI. Ayrıca verilen belirtecin `aud` (hedef kitle) talebi olur. Varsayılan değer "https://management.azure.com/"
Userassignedıdentities | *eşleşen* | Belirteci almak için Kullanıcı tarafından atanan yönetilen kimliklerin listesi. Belirtilmemişse, `listTokens`, sistem tarafından atanan yönetilen kimliğin belirtecini döndürür.


Örnek yanıt şöyle görünebilir:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

Yanıt, `value` özelliği altında bir belirteç dizisi içerir:

Parametre | Açıklama
---|---
access_token | İstenen erişim belirteci.
expires_in | Erişim belirtecinin geçerli olacağı saniye sayısı.
expires_on | Erişim belirtecinin süresi dolduğu zaman aralığı. Bu, dönem içindeki saniye sayısı olarak gösterilir.
not_before | Erişim belirteci yürürlüğe girer zaman aralığı. Bu, dönem içindeki saniye sayısı olarak gösterilir.
authorizationAudience | Erişim belirtecinin isteğiyle `aud` (hedef kitle). Bu, `listTokens` isteğinde sağlanmıştı.
resourceId | Verilen belirtecin Azure Kaynak KIMLIĞI. Bu, yönetilen uygulama KIMLIĞI ya da Kullanıcı tarafından atanan kimlik KIMLIĞIDIR.
token_type | Belirtecin türü.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yönetilen bir uygulamayı özel bir sağlayıcı ile yapılandırma](./custom-providers-overview.md)
