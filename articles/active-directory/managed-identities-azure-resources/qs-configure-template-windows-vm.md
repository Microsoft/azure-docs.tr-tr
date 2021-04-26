---
title: Şablon kullanarak Azure VM 'de Yönetilen kimlikler Yapılandırma-Azure AD
description: Azure Resource Manager şablonu kullanarak Azure VM 'de Azure kaynakları için Yönetilen kimlikler yapılandırmaya yönelik adım adım yönergeler.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4948e17d1e0e782a8fa18c3eb5a2185e816a459a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631413"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-templates"></a>Şablonları kullanarak Azure VM 'de Azure kaynakları için Yönetilen kimlikler yapılandırma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory ' de otomatik olarak yönetilen kimlik ile Azure hizmetleri sağlar. Bu kimliği, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz.

Bu makalede, Azure Resource Manager Dağıtım şablonunu kullanarak, Azure VM 'de Azure kaynakları işlemleri için aşağıdaki yönetilen kimlikleri nasıl gerçekleştireceğinizi öğreneceksiniz:

## <a name="prerequisites"></a>Önkoşullar

- Azure Resource Manager dağıtım şablonu kullanma konusunda bilgi sahibi değilseniz, [genel bakış bölümüne](overview.md)bakın. **[Sistem tarafından atanan ve Kullanıcı tarafından atanan yönetilen kimlik arasındaki farkı](overview.md#managed-identity-types)gözden geçirdiğinizden emin** olun.
- Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager şablonları

Azure portal ve betikte olduğu gibi [Azure Resource Manager](../../azure-resource-manager/management/overview.md) şablonlar, bir Azure Kaynak grubu tarafından tanımlanan yeni veya değiştirilmiş kaynakları dağıtma olanağı sağlar. Aşağıdakiler dahil olmak üzere hem yerel hem de portal tabanlı şablon düzenlemesi ve dağıtımı için çeşitli seçenekler mevcuttur:

   - Sıfırdan bir şablon oluşturmanıza veya mevcut bir ortak ya da [hızlı başlangıç şablonuna](https://azure.microsoft.com/documentation/templates/)temeletmenize olanak tanıyan [Azure Marketi 'nden özel bir şablon](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)kullanma.
   - Varolan bir kaynak grubundan türeterek, [orijinal dağıtımdan](../../azure-resource-manager/templates/export-template-portal.md)veya [dağıtımın geçerli durumundan](../../azure-resource-manager/templates/export-template-portal.md)bir şablonu dışarı aktararak.
   - Yerel bir [JSON Düzenleyicisi (vs Code gibi)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)kullanarak ve POWERSHELL veya CLI kullanarak karşıya yükleme ve dağıtma.
   - Bir şablon oluşturmak ve dağıtmak için Visual Studio [Azure Kaynak grubu projesini](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) kullanma.  

Seçtiğiniz seçenekten bağımsız olarak, şablon söz dizimi ilk dağıtım ve yeniden dağıtım sırasında aynıdır. Yeni veya mevcut bir VM 'de sistem veya Kullanıcı tarafından atanan yönetilen kimlik etkinleştirmek aynı şekilde yapılır. Ayrıca, varsayılan olarak Azure Resource Manager dağıtımlar için [artımlı bir güncelleştirme](../../azure-resource-manager/templates/deployment-modes.md) yapar.

## <a name="system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimlik

Bu bölümde, Azure Resource Manager şablonu kullanarak sistem tarafından atanan bir yönetilen kimliği etkinleştirip devre dışı bırakacaksınız.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Azure VM oluşturma sırasında veya var olan bir VM 'de sistem tarafından atanan yönetilen kimliği etkinleştirin

Bir VM 'de sistem tarafından atanan yönetilen kimliği etkinleştirmek için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

1. Azure 'da yerel olarak veya Azure portal aracılığıyla oturum açtığınızda, VM 'yi içeren Azure aboneliğiyle ilişkili bir hesabı kullanın.

2. Sistem tarafından atanan yönetilen kimliği etkinleştirmek için, şablonu bir düzenleyiciye yükleyin, `Microsoft.Compute/virtualMachines` bölüm içinde ilgilendiğiniz kaynağı bulun ve özelliği ile `resources` `"identity"` aynı düzeye ekleyin `"type": "Microsoft.Compute/virtualMachines"` . Aşağıdaki sözdizimini kullanın:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. İşiniz bittiğinde, şablonunuzun bölümüne aşağıdaki bölümler eklenmelidir `resource` ve şuna benzemelidir:

   ```JSON
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                }                        
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>VM 'nin sistem tarafından atanan yönetilen kimliğini bir rol atama

VM 'niz üzerinde sistem tarafından atanan yönetilen kimliği etkinleştirdikten sonra, bu gruba, oluşturulduğu kaynak grubuna **okuyucu** erişimi gibi bir rol vermek isteyebilirsiniz.

SANAL makinenizin sistem tarafından atanan kimliğine bir rol atamak için hesabınızın [Kullanıcı erişimi yönetici](../../role-based-access-control/built-in-roles.md#user-access-administrator) rolü ataması gerekir.

1. Azure 'da yerel olarak veya Azure portal aracılığıyla oturum açtığınızda, VM 'yi içeren Azure aboneliğiyle ilişkili bir hesabı kullanın.

2. Şablonu bir [düzenleyiciye](#azure-resource-manager-templates) YÜKLEYIN ve VM **okuyucunuzun** oluşturulduğu kaynak grubuna erişmesine izin vermek için aşağıdaki bilgileri ekleyin.  Şablon yapınız, düzenleyici ve seçtiğiniz dağıtım modeline bağlı olarak farklılık gösterebilir.

   Bölümünün altına `parameters` aşağıdakileri ekleyin:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    Bölümünün altına `variables` aşağıdakileri ekleyin:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Bölümünün altına `resources` aşağıdakileri ekleyin:

    ```JSON
    {
        "apiVersion": "2017-09-01",
        "type": "Microsoft.Authorization/roleAssignments",
        "name": "[parameters('rbacGuid')]",
        "properties": {
            "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
            "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
            "scope": "[resourceGroup().id]"
        },
         "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
        ]
    }
    ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Azure VM 'den sistem tarafından atanan yönetilen kimliği devre dışı bırakma

Bir VM 'den sistem tarafından atanan yönetilen kimliği kaldırmak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

1. Azure 'da yerel olarak veya Azure portal aracılığıyla oturum açtığınızda, VM 'yi içeren Azure aboneliğiyle ilişkili bir hesabı kullanın.

2. Şablonu bir [düzenleyiciye](#azure-resource-manager-templates) yükleyin ve `Microsoft.Compute/virtualMachines` bölümün içindeki ilgilendiğiniz kaynağı bulun `resources` . Yalnızca sistem tarafından atanmış yönetilen kimliğe sahip bir VM varsa, kimlik türünü olarak değiştirerek devre dışı bırakabilirsiniz `None` .  

   **Microsoft. COMPUTE/virtualMachines API sürümü 2018-06-01**

   SANAL makinenizde hem sistem hem de Kullanıcı tarafından atanan Yönetilen kimlikler varsa, `SystemAssigned` kimlik türünden kaldırın ve `UserAssigned` `userAssignedIdentities` Sözlük değerleriyle birlikte saklayın.

   **Microsoft. COMPUTE/virtualMachines API sürümü 2018-06-01**

   Eğer `apiVersion` `2017-12-01` ve sanal makinenizde hem sistem hem de Kullanıcı tarafından atanan Yönetilen kimlikler varsa, `SystemAssigned` kimlik türünden kaldırın ve `UserAssigned` `identityIds` Kullanıcı tarafından atanan yönetilen kimliklerin dizisiyle birlikte saklayın.  

Aşağıdaki örnek, Kullanıcı tarafından atanan yönetilen kimlikleri olmayan bir VM 'den sistem tarafından atanan yönetilen kimliği nasıl kaldırakullanacağınızı gösterir:

 ```JSON
 {
     "apiVersion": "2018-06-01",
     "type": "Microsoft.Compute/virtualMachines",
     "name": "[parameters('vmName')]",
     "location": "[resourceGroup().location]",
     "identity": {
         "type": "None"
     }
 }
 ```

## <a name="user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik

Bu bölümde, Azure Resource Manager şablonu kullanarak bir Azure VM 'sine Kullanıcı tarafından atanan bir yönetilen kimlik atarsınız.

> [!Note]
> Azure Resource Manager şablonu kullanarak Kullanıcı tarafından atanan yönetilen kimlik oluşturmak için, bkz. [Kullanıcı tarafından atanan yönetilen kimlik oluşturma](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Azure VM 'ye Kullanıcı tarafından atanan yönetilen kimlik atama

Bir VM 'ye Kullanıcı tarafından atanan bir kimlik atamak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ve [yönetilen kimlik işleci](../../role-based-access-control/built-in-roles.md#managed-identity-operator) rol atamalarına ihtiyacı vardır. Ek Azure AD dizin rolü ataması gerekli değildir.

1. Öğesi altında `resources` , sanal makinenize Kullanıcı tarafından atanan bir yönetilen kimlik atamak için aşağıdaki girişi ekleyin.  Öğesini `<USERASSIGNEDIDENTITY>` , oluşturduğunuz Kullanıcı tarafından atanan yönetilen kimliğin adıyla değiştirdiğinizden emin olun.

   **Microsoft. COMPUTE/virtualMachines API sürümü 2018-06-01**

   `apiVersion`İse `2018-06-01` , Kullanıcı tarafından atanan yönetilen kimlikleriniz `userAssignedIdentities` sözlük biçiminde depolanır ve `<USERASSIGNEDIDENTITYNAME>` değerin, şablonunuzun bölümünde tanımlanan bir değişkende depolanması gerekir `variables` .

   ```JSON
    {
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
            }
        }
    }
   ```

   **Microsoft. COMPUTE/virtualMachines API sürümü 2017-12-01**

   `apiVersion`İse `2017-12-01` , Kullanıcı tarafından atanan yönetilen kimlikleriniz `identityIds` dizide depolanır ve `<USERASSIGNEDIDENTITYNAME>` değer şablonunuzun bölümünde tanımlanan bir değişkende depolanmalıdır `variables` .

   ```JSON
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```

3. İşiniz bittiğinde, şablonunuzun bölümüne aşağıdaki bölümler eklenmelidir `resource` ve şuna benzemelidir:

   **Microsoft. COMPUTE/virtualMachines API sürümü 2018-06-01**    

   ```JSON
     "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        }
    ] 
   ```
   **Microsoft. COMPUTE/virtualMachines API sürümü 2017-12-01**

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        }
   ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Kullanıcı tarafından atanan yönetilen kimliği bir Azure VM 'den kaldırma

Kullanıcı tarafından atanan kimliği bir VM 'den kaldırmak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir. Ek Azure AD dizin rolü ataması gerekli değildir.

1. Azure 'da yerel olarak veya Azure portal aracılığıyla oturum açtığınızda, VM 'yi içeren Azure aboneliğiyle ilişkili bir hesabı kullanın.

2. Şablonu bir [düzenleyiciye](#azure-resource-manager-templates) yükleyin ve `Microsoft.Compute/virtualMachines` bölümün içindeki ilgilendiğiniz kaynağı bulun `resources` . Yalnızca Kullanıcı tarafından atanan yönetilen kimliğe sahip bir VM varsa, kimlik türünü olarak değiştirerek devre dışı bırakabilirsiniz `None` .

   Aşağıdaki örnek, sistem tarafından atanan yönetilen kimlikleri olmayan bir VM 'den Kullanıcı tarafından atanan tüm yönetilen kimliklerin nasıl kaldırılacağını gösterir:

   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": {
          "type": "None"
          },
    }
   ```

   **Microsoft. COMPUTE/virtualMachines API sürümü 2018-06-01**

   Tek bir kullanıcı tarafından atanan yönetilen kimliği bir VM 'den kaldırmak için `useraAssignedIdentities` sözlükten kaldırın.

   Sistem tarafından atanan bir yönetilen Kimliğiniz varsa `type` değer altındaki değerde saklayın `identity` .

   **Microsoft. COMPUTE/virtualMachines API sürümü 2017-12-01**

   Tek bir kullanıcı tarafından atanan yönetilen kimliği bir VM 'den kaldırmak için, `identityIds` diziden kaldırın.

   Sistem tarafından atanan bir yönetilen Kimliğiniz varsa `type` değer altındaki değerde saklayın `identity` .

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynaklarına genel bakış Için Yönetilen kimlikler](overview.md).
