---
title: Azure API Management Yönetilen kimlikler kullanma | Microsoft Docs
description: Azure portal, PowerShell ve bir Kaynak Yöneticisi şablonunu kullanarak API Management sistem tarafından atanan ve Kullanıcı tarafından atanan kimlikler oluşturmayı öğrenin.
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 40ee196f53af040e4099fb344de5488109ce001b
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812255"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Azure API Management Yönetilen kimlikler kullanma

Bu makalede, Azure API Management örneği için yönetilen kimlik oluşturma ve diğer kaynaklara erişme gösterilmektedir. Azure Active Directory tarafından oluşturulan yönetilen bir kimlik (Azure AD), API Management örneğinizin Azure Key Vault gibi diğer Azure AD korumalı kaynaklarına kolayca ve güvenli bir şekilde erişmesini sağlar. Azure bu kimliği yönetir, bu nedenle herhangi bir gizli dizi sağlamanız veya döndürmeniz gerekmez. Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../active-directory/managed-identities-azure-resources/overview.md).

API Management örneğine iki tür kimlik verebilirsiniz:

- *Sistem tarafından atanan bir kimlik* hizmetinize bağlanır ve hizmetiniz silinirse silinir. Hizmet yalnızca bir sistem tarafından atanmış kimliğe sahip olabilir.
- *Kullanıcı tarafından atanan bir kimlik* , hizmetinize atanabilecek tek başına bir Azure kaynağıdır. Hizmette birden çok kullanıcı tarafından atanan kimlik olabilir.

## <a name="create-a-system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimlik oluşturma

### <a name="azure-portal"></a>Azure portalı

Azure portal yönetilen bir kimlik ayarlamak için, önce bir API Management örneği oluşturup sonra özelliği etkinleştirmeniz gerekir.

1. Portalda genellikle yaptığınız gibi bir API Management örneği oluşturun. Portalda buna gidin.
2. **Yönetilen kimlikler**' i seçin.
3. **Sistem atandı** sekmesinde **durumu** **Açık** olarak değiştirin. **Kaydet**’i seçin.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Sistem tarafından atanan yönetilen kimliği etkinleştirme seçimleri" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıdaki adımlar, bir API Management örneği oluşturma ve Azure PowerShell kullanarak bir kimlik atama işleminde size yol gösterir. 

1. Gerekirse, [Azure PowerShell kılavuzundaki](/powershell/azure/install-az-ps)yönergeleri kullanarak Azure PowerShell ' yi yüklemelisiniz. Ardından `Connect-AzAccount` Azure ile bağlantı oluşturmak için öğesini çalıştırın.

2. Örneği oluşturmak için aşağıdaki kodu kullanın. API Management örneği ile Azure PowerShell kullanma hakkında daha fazla örnek için bkz. [API Management PowerShell örnekleri](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Kimliği oluşturmak için var olan bir örneği güncelleştirin:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager şablonu

Kaynak tanımına aşağıdaki özelliği ekleyerek bir kimlik ile API Management örneği oluşturabilirsiniz:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Bu özellik Azure 'un API Management örneğiniz için kimlik oluşturmasını ve yönetmesini söyler.

Örneğin, Azure Resource Manager bir şablon aşağıdaki gibi görünebilir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```

Örnek oluşturulduğunda, aşağıdaki ek özelliklere sahiptir:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`tenantId`Özelliği, kimliğin hangi Azure AD kiracıya ait olduğunu tanımlar. `principalId`Özelliği, örneğin yeni kimliği için benzersiz bir tanımlayıcıdır. Azure AD 'de hizmet sorumlusu, API Management örneğinize verdiğiniz aynı ada sahiptir.

> [!NOTE]
> Bir API Management örneği aynı anda hem sistem tarafından hem de Kullanıcı tarafından atanan kimliklere sahip olabilir. Bu durumda, `type` özelliği olur `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-system-assigned-identity"></a>Sistem tarafından atanan kimlik kullanılarak desteklenen senaryolar

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Azure Key Vault API Management örneği için özel bir TLS/SSL sertifikası alın
Azure Key Vault depolanan özel TLS/SSL sertifikalarını almak için bir API Management örneğinin sistem tarafından atanan kimliğini kullanabilirsiniz. Daha sonra bu sertifikaları API Management örneğindeki özel etki alanlarına atayabilirsiniz. Şu noktaları göz önünde bulundurun:

- Gizli dizi içerik türü *Application/x-PKCS12* olmalıdır.
- Parolayı içeren Key Vault sertifika gizli uç noktasını kullanın.

> [!Important]
> Sertifikanın nesne sürümünü sağlamazsanız, API Management Key Vault güncelleştirildikten sonra sertifikanın dört saat içinde otomatik olarak yeni sürümünü elde eder.

Aşağıdaki örnekte aşağıdaki adımları içeren bir Azure Resource Manager şablonu gösterilmektedir:

1. Yönetilen kimliğe sahip bir API Management örneği oluşturun.
2. Bir Azure Key Vault örneğinin erişim ilkelerini güncelleştirin ve API Management örneğinin bundan gizli dizileri almasına izin verin.
3. Key Vault örneğinden bir sertifika aracılığıyla özel bir etki alanı adı ayarlayarak API Management örneğini güncelleştirin.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>API Management kimliği kullanarak arka uçta kimlik doğrulama

[Kimlik doğrulaması ile yönetilen](api-management-authentication-policies.md#ManagedIdentity) kimlik ilkesi aracılığıyla arka uçta kimlik doğrulaması yapmak için sistem tarafından atanan kimliğini kullanabilirsiniz.

### <a name="connect-to-azure-resources-behind-ip-firewall-using-system-assigned-managed-identity"></a><a name="apim-as-trusted-service"></a>Sistem tarafından atanan yönetilen kimliği kullanarak IP güvenlik duvarının arkasındaki Azure kaynaklarına bağlanma


API Management, aşağıdaki kaynaklara güvenilir bir Microsoft hizmetidir. Bu, hizmetin bir güvenlik duvarının arkasındaki aşağıdaki kaynaklara bağlanmasına izin verir. Uygun Azure rolünü bu kaynak örneği için [sistem tarafından atanan yönetilen kimliğe](../active-directory/managed-identities-azure-resources/overview.md) açık bir şekilde atadıktan sonra, örneğin erişim kapsamı yönetilen kimliğe atanan Azure rolüne karşılık gelir.


|Azure hizmeti | Bağlantı|
|---|---|
|Azure Depolama | [Güvenilen-Azure-Azure-depolama](../storage/common/storage-network-security.md?tabs=azure-portal#trusted-access-based-on-system-assigned-managed-identity)|
|Azure Service Bus | [Güvenilen-Azure-Service-Bus](../service-bus-messaging/service-bus-ip-filtering.md#trusted-microsoft-services)|
|Azure Event Hub | [Popüler-Azure-Event-hub 'a erişme](../event-hubs/event-hubs-ip-filtering.md#trusted-microsoft-services)|


## <a name="create-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik oluşturma

> [!NOTE]
> API Management örneği, en fazla 10 Kullanıcı tarafından atanan yönetilen kimlik ile ilişkilendirebilirsiniz.

### <a name="azure-portal"></a>Azure portalı

Portalda yönetilen bir kimlik ayarlamak için önce bir API Management örneği oluşturup sonra özelliği etkinleştirmeniz gerekir.

1. Portalda genellikle yaptığınız gibi bir API Management örneği oluşturun. Portalda buna gidin.
2. **Yönetilen kimlikler**' i seçin.
3. **Kullanıcı atandı** sekmesinde **Ekle**' yi seçin.
4. Daha önce oluşturduğunuz kimliği arayın ve seçin. **Add (Ekle)** seçeneğini belirleyin.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Kullanıcı tarafından atanan yönetilen kimliği etkinleştirme seçimleri" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıdaki adımlar, bir API Management örneği oluşturma ve Azure PowerShell kullanarak bir kimlik atama işleminde size yol gösterir. 

1. Gerekirse, [Azure PowerShell kılavuzundaki](/powershell/azure/install-az-ps)yönergeleri kullanarak Azure PowerShell ' yi yüklemelisiniz. Ardından `Connect-AzAccount` Azure ile bağlantı oluşturmak için öğesini çalıştırın.

2. Örneği oluşturmak için aşağıdaki kodu kullanın. API Management örneği ile Azure PowerShell kullanma hakkında daha fazla örnek için bkz. [API Management PowerShell örnekleri](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Hizmete bir kimlik atamak için mevcut bir hizmeti güncelleştirin:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager şablonu

Kaynak tanımına aşağıdaki özelliği ekleyerek bir kimlik ile API Management örneği oluşturabilirsiniz:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

Kullanıcı tarafından atanan türü eklemek, Azure 'un örneğiniz için belirtilen kullanıcı tarafından atanan kimliğini kullanmasını söyler.

Örneğin, Azure Resource Manager bir şablon aşağıdaki gibi görünebilir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
         "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

Hizmet oluşturulduğunda, aşağıdaki ek özelliklere sahiptir:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

`principalId`Özelliği, Azure AD yönetimi için kullanılan kimlik için benzersiz bir tanımlayıcıdır. `clientId`Özelliği, uygulamanın çalışma zamanı çağrıları sırasında hangi kimliğin kullanılacağını belirtmek için kullanılan yeni kimliği için benzersiz bir tanımlayıcıdır.

> [!NOTE]
> Bir API Management örneği aynı anda hem sistem tarafından hem de Kullanıcı tarafından atanan kimliklere sahip olabilir. Bu durumda, `type` özelliği olur `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik kullanılarak desteklenen senaryolar

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Azure Key Vault API Management örneği için özel bir TLS/SSL sertifikası alın
Bir API Management örneği ile Anahtar Kasası arasında güven oluşturmak için Kullanıcı tarafından atanan herhangi bir kimliği kullanabilirsiniz. Bu güven daha sonra, Azure Key Vault depolanan özel TLS/SSL sertifikalarını almak için kullanılabilir. Daha sonra bu sertifikaları API Management örneğindeki özel etki alanlarına atayabilirsiniz. 

Şu noktaları göz önünde bulundurun:

- Gizli dizi içerik türü *Application/x-PKCS12* olmalıdır.
- Parolayı içeren Key Vault sertifika gizli uç noktasını kullanın.

> [!Important]
> Sertifikanın nesne sürümünü sağlamazsanız, API Management Key Vault güncelleştirildikten sonra sertifikanın dört saat içinde otomatik olarak yeni sürümünü elde eder.

Tüm şablon için, bkz. [Kullanıcı tarafından atanan kimlik kullanarak Anahtar Kasası tabanlı SSL ile API Management](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json).

Bu şablonda şunları dağıtacaksınız:

* Azure API Management
* Azure yönetilen Kullanıcı tarafından atanan kimlik
* SSL/TLS sertifikasını depolamak için Azure Anahtar Kasası

Dağıtımı otomatik olarak çalıştırmak için aşağıdaki düğmeye tıklayın:

[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Kullanıcı tarafından atanan bir kimlik kullanarak arka uçta kimlik doğrulama

[Kimlik doğrulaması ile yönetilen](api-management-authentication-policies.md#ManagedIdentity) kimlik ilkesi aracılığıyla arka uçta kimlik doğrulaması yapmak için Kullanıcı tarafından atanan kimlik ' i kullanabilirsiniz.

## <a name="remove-an-identity"></a><a name="remove"></a>Kimlik kaldırma

Özelliği portal veya Azure Resource Manager şablonu aracılığıyla oluşturulduğu şekilde devre dışı bırakarak sistem tarafından atanan bir kimliği kaldırabilirsiniz. Kullanıcı tarafından atanan kimlikler tek tek kaldırılabilir. Tüm kimlikleri kaldırmak için kimlik türünü olarak ayarlayın `"None"` .

Sistem tarafından atanan bir kimliğin bu şekilde kaldırılması, Azure AD 'den de silinecek. API Management örneği silindiğinde, sistem tarafından atanan kimlikler de Azure AD 'den otomatik olarak kaldırılır.

Azure Resource Manager şablonunu kullanarak tüm kimlikleri kaldırmak için bu bölümü güncelleştirin:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> API Management bir örnek Key Vault özel bir SSL sertifikasıyla yapılandırıldıysa ve yönetilen bir kimliği devre dışı bırakmayı denerseniz, istek başarısız olur.
>
> Bir Azure Key Vault sertifikasından satır içi kodlanmış sertifikaya geçerek ve sonra yönetilen kimliği devre dışı bırakarak kendi engellemesini kaldırabilirsiniz. Daha fazla bilgi için bkz. [özel bir etki alanı adı yapılandırma](configure-custom-domain.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure kaynakları için Yönetilen kimlikler hakkında daha fazla bilgi edinin:

* [Azure kaynakları için Yönetilen kimlikler nelerdir?](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager şablonları](https://github.com/Azure/azure-quickstart-templates)
* [İlkede yönetilen kimlikle kimlik doğrulama](./api-management-authentication-policies.md#ManagedIdentity)
