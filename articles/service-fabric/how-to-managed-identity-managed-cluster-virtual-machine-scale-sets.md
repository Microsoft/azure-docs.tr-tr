---
title: Service Fabric yönetilen bir küme düğümü türüne yönetilen kimlik ekleme (Önizleme)
description: Bu makalede, yönetilen bir kimliği Service Fabric yönetilen bir küme düğümü türüne nasıl ekleyeceğiniz gösterilmektedir
ms.topic: how-to
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 3ff5d66160ddbb037469378634826fd9eeae0c54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651655"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type-preview"></a>Service Fabric yönetilen bir küme düğümü türüne yönetilen kimlik ekleme (Önizleme)

Service Fabric yönetilen bir kümedeki her düğüm türü bir sanal makine ölçek kümesi tarafından desteklenir. Yönetilen kimliklerin yönetilen küme düğüm türüyle kullanılmasına izin vermek için, `vmManagedIdentity` kullanılabilecek bir kimlik listesi içeren düğüm türü tanımlarına bir özellik eklenmiştir `userAssignedIdentities` . İşlevselliği, yönetilen kimliklerin [Azure Key Vault sanal makine ölçek kümesi uzantısıyla](../virtual-machines/extensions/key-vault-windows.md)yönetilen kimlik kullanma gibi yönetilmeyen kümelerde nasıl kullanılabileceğini yansıtır.


Bir düğüm türünde yönetilen kimlik kullanan Service Fabric yönetilen bir küme dağıtımına bir örnek için, [Bu şablona](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI)bakın. Desteklenen bölgelerin listesi için bkz. [yönetilen küme SSS](./faq-managed-cluster.md#what-regions-are-supported-in-the-preview).

> [!NOTE]
> Bu özellik için şu anda yalnızca Kullanıcı tarafından atanan kimlikler desteklenmektedir.

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce:

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.
* PowerShell kullanmayı planlıyorsanız, CLı başvuru komutlarını çalıştırmak için Azure CLı ' yi [yükleyebilirsiniz](/cli/azure/install-azure-cli) .

## <a name="create-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik oluşturma 

Kullanıcı tarafından atanan yönetilen kimlik, dağıtımdan sonra oluşturmak için bir Azure Resource Manager (ARM) şablonunun kaynaklar bölümünde tanımlanabilir:

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

veya PowerShell aracılığıyla oluşturuldu:

```powershell
az group create --name <resourceGroupName> --location <location>
az identity create --name <userAssignedIdentityName> --resource-group <resourceGroupName>
```

## <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Service Fabric kaynak sağlayıcısına rol ataması ekleme

Yönetilen kimliğe Service Fabric kaynak sağlayıcısı uygulamasıyla bir rol ataması ekleyin. Bu atama Service Fabric kaynak sağlayıcısının kimliği yönetilen kümenin sanal makine ölçek kümesine atamasını sağlar. 

Aşağıdaki değerler uygun olduğunda kullanılmalıdır:

|Name|Karşılık gelen Service Fabric kaynak sağlayıcısı değeri|
|----|-------------------------------------|
|Uygulama Kimliği|74cb6831-0dbb-4be1-8206-fd4df301cdc2|
|Nesne kimliği|fbc587f2-66f5-4459-a027-bcd908b9d278|


|Rol tanımı adı|Rol tanımı KIMLIĞI|
|----|-------------------------------------|
|Yönetilen kimlik Işleci|f1a07417-d97a-45cb-824c-7a7467783830
|



Bu rol ataması, kaynak bölümünde, nesne KIMLIĞI ve rol tanımı KIMLIĞI kullanılarak tanımlanabilir:

```JSON
{
    "type": "Microsoft.Authorization/roleAssignments", 
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('vmIdentityRoleNameGuid')]",
    "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
    "dependsOn": [ 
        "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
    ], 
    "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
        "principalId": "fbc587f2-66f5-4459-a027-bcd908b9d278" 
    } 
}, 
```

ya da uygulama KIMLIĞI ve rol tanımı KIMLIĞI kullanılarak PowerShell aracılığıyla oluşturulur:

```powershell
New-AzRoleAssignment -ApplicationId 74cb6831-0dbb-4be1-8206-fd4df301cdc2 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

ya da nesne KIMLIĞI ve rol tanımı KIMLIĞI:

```powershell
New-AzRoleAssignment -PrincipalId fbc587f2-66f5-4459-a027-bcd908b9d278 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

## <a name="add-managed-identity-properties-to-node-type-definition"></a>Yönetilen kimlik özelliklerini düğüm türü tanımına ekleyin

Son olarak, `vmManagedIdentity` ve `userAssignedIdentities` özelliklerini yönetilen kümenin düğüm türü tanımına ekleyin. İçin **2021-01-01-Preview** veya sonraki bir sürümünü kullandığınızdan emin olun `apiVersion` .

```json

 {
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "2021-01-01-preview",
    ...
    "properties": {
        "isPrimary" : true,
        "vmInstanceCount": 5,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2_v2",
        "vmImagePublisher" : "MicrosoftWindowsServer",
        "vmImageOffer" : "WindowsServer",
        "vmImageSku" : "2019-Datacenter",
        "vmImageVersion" : "latest",
        "vmManagedIdentity": {
            "userAssignedIdentities": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
            ]
        }
    }
}
```

Dağıtımdan sonra, oluşturulan yönetilen kimlik, belirtilen düğüm türünün sanal makine ölçek kümesine eklenmiştir ve herhangi bir yönetilmeyen kümede olduğu gibi beklendiği gibi kullanılabilir.

## <a name="troubleshooting"></a>Sorun giderme

Bir rol ataması düzgün bir şekilde eklenemedi, dağıtımda aşağıdaki hata ile karşılanır:

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="SFRP 'nin nesne/uygulama KIMLIĞINE sahip istemciyi gösteren Azure portal dağıtım hatası kimlik yönetimi etkinliğini gerçekleştirme iznine sahip değil":::

## <a name="next-steps"></a>Sonraki Adımlar

> [!div class="nextstepaction"]
> [Service Fabric yönetilen bir kümeye uygulama dağıtma](./tutorial-managed-cluster-deploy-app.md)