---
title: Şablonu, sanal makine ölçek kümelerinde yönetilen kimlikleri kullanacak şekilde Yapılandırma-Azure AD
description: Bir Azure Resource Manager şablonu kullanarak bir sanal makine ölçek kümesindeki Azure kaynakları için Yönetilen kimlikler yapılandırmaya yönelik adım adım yönergeler.
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
ms.date: 04/12/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e948b96022972dcf702ac5a4d8be85c9afe16e7
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365986"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Bir şablon kullanarak bir Azure sanal makine ölçeğinde Azure kaynakları için Yönetilen kimlikler yapılandırma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory ' de otomatik olarak yönetilen kimlik ile Azure hizmetleri sağlar. Bu kimliği, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz.

Bu makalede, Azure Resource Manager dağıtım şablonu kullanarak Azure sanal makine ölçek kümesindeki Azure kaynakları işlemleri için aşağıdaki yönetilen kimliklerin nasıl gerçekleştirileceğini öğreneceksiniz:

- Azure sanal makine ölçek kümesi üzerinde sistem tarafından atanan yönetilen kimliği etkinleştirme ve devre dışı bırakma
- Azure sanal makine ölçek kümesine Kullanıcı tarafından atanan yönetilen kimlik ekleme ve kaldırma

## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için Yönetilen kimlikler hakkında bilginiz varsa [genel bakış bölümüne](overview.md)bakın. **[Sistem tarafından atanan ve Kullanıcı tarafından atanan yönetilen kimlik arasındaki farkı](overview.md#managed-identity-types)gözden geçirdiğinizden emin** olun.
- Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).
- Bu makaledeki yönetim işlemlerini gerçekleştirmek için, hesabınız aşağıdaki Azure rol tabanlı erişim denetimi atamalarına ihtiyaç duyuyor:

    > [!NOTE]
    > Ek Azure AD dizin rolü ataması gerekli değildir.

    - Sanal makine [katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) bir sanal makine ölçek kümesi oluşturmak ve sistem ve/veya Kullanıcı tarafından atanan yönetilen kimliği bir sanal makine ölçek kümesinden etkinleştirmek ve kaldırmak için.
    - Kullanıcı tarafından atanan yönetilen kimlik oluşturmak için [yönetilen kimlik katılımcısı](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) rolü.
    - Kullanıcı tarafından atanan yönetilen kimliği ve sanal makine ölçek kümesine atamak ve kaldırmak için [yönetilen kimlik operatörü](../../role-based-access-control/built-in-roles.md#managed-identity-operator) rolü.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager şablonları

Azure portal ve betikte olduğu gibi [Azure Resource Manager](../../azure-resource-manager/management/overview.md) şablonlar, bir Azure Kaynak grubu tarafından tanımlanan yeni veya değiştirilmiş kaynakları dağıtma olanağı sağlar. Aşağıdakiler dahil olmak üzere hem yerel hem de portal tabanlı şablon düzenlemesi ve dağıtımı için çeşitli seçenekler mevcuttur:

   - Sıfırdan bir şablon oluşturmanıza veya mevcut bir ortak ya da [hızlı başlangıç şablonuna](https://azure.microsoft.com/documentation/templates/)temeletmenize olanak tanıyan [Azure Marketi 'nden özel bir şablon](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)kullanma.
   - Varolan bir kaynak grubundan türeterek, [orijinal dağıtımdan](../../azure-resource-manager/templates/export-template-portal.md)veya [dağıtımın geçerli durumundan](../../azure-resource-manager/templates/export-template-portal.md)bir şablonu dışarı aktararak.
   - Yerel bir [JSON Düzenleyicisi (vs Code gibi)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)kullanarak ve POWERSHELL veya CLI kullanarak karşıya yükleme ve dağıtma.
   - Bir şablon oluşturmak ve dağıtmak için Visual Studio [Azure Kaynak grubu projesini](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) kullanma.  

Seçtiğiniz seçenekten bağımsız olarak, şablon söz dizimi ilk dağıtım ve yeniden dağıtım sırasında aynıdır. Yeni veya mevcut bir VM 'de Azure kaynakları için yönetilen kimliklerin etkinleştirilmesi aynı şekilde yapılır. Ayrıca, varsayılan olarak Azure Resource Manager dağıtımlar için [artımlı bir güncelleştirme](../../azure-resource-manager/templates/deployment-modes.md) yapar.

## <a name="system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimlik

Bu bölümde, sistem tarafından atanan yönetilen kimliği bir Azure Resource Manager şablonu kullanarak etkinleştirip devre dışı bırakacaksınız.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Bir sanal makine ölçek kümesi veya var olan bir sanal makine ölçek kümesi oluşturulurken sistem tarafından atanan yönetilen kimliği etkinleştirin

1. Azure 'da yerel olarak veya Azure portal aracılığıyla oturum açıp etmeksizin, sanal makine ölçek kümesini içeren Azure aboneliğiyle ilişkili bir hesabı kullanın.
2. Sistem tarafından atanan yönetilen kimliği etkinleştirmek için, şablonu bir düzenleyiciye yükleyin, `Microsoft.Compute/virtualMachinesScaleSets` Kaynaklar bölümünde ilgilendiğiniz kaynağı bulun ve özelliği ile `identity` aynı düzeye ekleyin `"type": "Microsoft.Compute/virtualMachinesScaleSets"` . Aşağıdaki sözdizimini kullanın:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

4. İşiniz bittiğinde, şablonunuzun kaynak bölümüne aşağıdaki bölümler eklenmelidir ve şuna benzemelidir:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
        
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure sanal makine ölçek kümesinden sistem tarafından atanan yönetilen kimliği devre dışı bırakma

Artık sistem tarafından atanan yönetilen kimliğe ihtiyacı olmayan bir sanal makine ölçek kümesine sahipseniz:

1. Azure 'da yerel olarak veya Azure portal aracılığıyla oturum açıp etmeksizin, sanal makine ölçek kümesini içeren Azure aboneliğiyle ilişkili bir hesabı kullanın.

2. Şablonu bir [düzenleyiciye](#azure-resource-manager-templates) yükleyin ve `Microsoft.Compute/virtualMachineScaleSets` bölümün içindeki ilgilendiğiniz kaynağı bulun `resources` . Yalnızca sistem tarafından atanmış yönetilen kimliğe sahip bir VM varsa, kimlik türünü olarak değiştirerek devre dışı bırakabilirsiniz `None` .

   **Microsoft. COMPUTE/virtualMachineScaleSets API sürüm 2018-06-01**

   Apisürümünüz ise `2018-06-01` ve sanal makinenizde hem sistem hem de Kullanıcı tarafından atanan Yönetilen kimlikler varsa, `SystemAssigned` kimlik türünden kaldırın ve `UserAssigned` Useratandidentities sözlük değerleriyle birlikte tutun.

   **Microsoft. COMPUTE/virtualMachineScaleSets API sürüm 2018-06-01**

   Apisürümünüz ise `2017-12-01` ve sanal makine ölçek kümesinde hem sistem hem de Kullanıcı tarafından atanan Yönetilen kimlikler varsa, `SystemAssigned` kimlik türünden kaldırın ve `UserAssigned` `identityIds` Kullanıcı tarafından atanan yönetilen kimliklerin dizisiyle birlikte saklayın.



   Aşağıdaki örnek, bir sanal makine ölçek kümesinden, Kullanıcı tarafından atanan yönetilen kimlikleri olmayan bir sistem tarafından atanan yönetilen kimliği nasıl kaldırakullanacağınızı gösterir:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik

Bu bölümde, Azure Resource Manager şablonu kullanarak bir sanal makine ölçek kümesine Kullanıcı tarafından atanan bir yönetilen kimlik atarsınız.

> [!Note]
> Azure Resource Manager şablonu kullanarak Kullanıcı tarafından atanan yönetilen kimlik oluşturmak için, bkz. [Kullanıcı tarafından atanan yönetilen kimlik oluşturma](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Kullanıcı tarafından atanan yönetilen kimliği bir sanal makine ölçek kümesine atama

1. Öğesi altında `resources` , sanal makine ölçek kümesine Kullanıcı tarafından atanan bir yönetilen kimlik atamak için aşağıdaki girişi ekleyin.  Öğesini `<USERASSIGNEDIDENTITY>` , oluşturduğunuz Kullanıcı tarafından atanan yönetilen kimliğin adıyla değiştirdiğinizden emin olun.

   **Microsoft. COMPUTE/virtualMachineScaleSets API sürüm 2018-06-01**

   ApiVersion ise `2018-06-01` , Kullanıcı tarafından atanan yönetilen kimlikleriniz `userAssignedIdentities` sözlük biçiminde depolanır ve `<USERASSIGNEDIDENTITYNAME>` değer şablonunuzun bölümünde tanımlanan bir değişkende depolanmalıdır `variables` .

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }

   }
   ```   

   **Microsoft. COMPUTE/virtualMachineScaleSets API sürüm 2017-12-01**

   `apiVersion` `2017-12-01` Veya daha önceki bir sürümse, Kullanıcı tarafından atanan yönetilen kimlikleriniz `identityIds` dizide depolanır ve `<USERASSIGNEDIDENTITYNAME>` değer, şablonunuzun değişkenler bölümünde tanımlanan bir değişkende depolanmalıdır.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }

3. When you are done, your template should look similar to the following:

   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                }
            }
        }
    ]
   ```

   **Microsoft. COMPUTE/virtualMachines API sürümü 2017-12-01**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                }
            }
        }
    ]
   ```
### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Kullanıcı tarafından atanan yönetilen kimliği bir Azure sanal makine ölçek kümesinden kaldır

Artık Kullanıcı tarafından atanan yönetilen kimliğe ihtiyacı olmayan bir sanal makine ölçek kümesine sahipseniz:

1. Azure 'da yerel olarak veya Azure portal aracılığıyla oturum açıp etmeksizin, sanal makine ölçek kümesini içeren Azure aboneliğiyle ilişkili bir hesabı kullanın.

2. Şablonu bir [düzenleyiciye](#azure-resource-manager-templates) yükleyin ve `Microsoft.Compute/virtualMachineScaleSets` bölümün içindeki ilgilendiğiniz kaynağı bulun `resources` . Yalnızca Kullanıcı tarafından atanan yönetilen kimliğe sahip bir sanal makine ölçek kümesine sahipseniz, kimlik türünü olarak değiştirerek devre dışı bırakabilirsiniz `None` .

   Aşağıdaki örnek, sistem tarafından atanan yönetilen kimlikleri olmayan bir VM 'den Kullanıcı tarafından atanan tüm yönetilen kimliklerin nasıl kaldırılacağını gösterir:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```

   **Microsoft. COMPUTE/virtualMachineScaleSets API sürüm 2018-06-01**

   Bir sanal makine ölçek kümesinden Kullanıcı tarafından atanan tek bir yönetilen kimliği kaldırmak için `userAssignedIdentities` sözlükten kaldırın.

   Sistem tarafından atanan bir kimliğiniz varsa `type` değer altındaki değerde saklayın `identity` .

   **Microsoft. COMPUTE/virtualMachineScaleSets API sürüm 2017-12-01**

   Bir sanal makine ölçek kümesinden Kullanıcı tarafından atanan tek bir yönetilen kimliği kaldırmak için, `identityIds` diziden kaldırın.

   Sistem tarafından atanan bir yönetilen Kimliğiniz varsa `type` değer altındaki değerde saklayın `identity` .

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynaklarına genel bakış Için Yönetilen kimlikler](overview.md).