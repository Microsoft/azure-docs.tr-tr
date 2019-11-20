---
title: Azure CLı kullanarak bir Azure sanal makine ölçek kümesi üzerinde sistem ve Kullanıcı tarafından atanan Yönetilen kimlikler yapılandırma
description: Azure CLı kullanarak bir Azure sanal makine ölçek kümesinde sistem ve Kullanıcı tarafından atanan Yönetilen kimlikler yapılandırmaya yönelik adım adım yönergeler.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: MarkusVi
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895d914226014a0f43bc7f8ff24d3e7dff24ef37
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310042"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Azure CLı kullanarak bir sanal makine ölçek kümesindeki Azure kaynakları için Yönetilen kimlikler yapılandırma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory ' de otomatik olarak yönetilen bir kimlikle Azure hizmetleri sağlar. Bu kimliği, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz. 

Bu makalede, Azure CLı kullanarak Azure sanal makine ölçek kümesindeki Azure kaynakları işlemleri için aşağıdaki yönetilen kimliklerin nasıl gerçekleştirileceğini öğreneceksiniz:
- Azure sanal makine ölçek kümesi üzerinde sistem tarafından atanan yönetilen kimliği etkinleştirme ve devre dışı bırakma
- Azure sanal makine ölçek kümesine Kullanıcı tarafından atanan yönetilen kimlik ekleme ve kaldırma


## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için yönetilen kimliklerle bilmiyorsanız kullanıma [genel bakış bölümünde](overview.md). **Gözden geçirmeyi unutmayın [sistem tarafından atanan ve kullanıcı tarafından atanan bir yönetilen kimlik arasındaki farkı](overview.md#how-does-it-work)** .
- Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).
- Bu makaledeki yönetim işlemlerini gerçekleştirmek için, hesabınız aşağıdaki Azure rol tabanlı erişim denetimi atamalarına ihtiyaç duyuyor:

    > [!NOTE]
    > Ek Azure AD dizin rolü ataması gerekli değildir.

    - Sanal makine [katılımcısı](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) bir sanal makine ölçek kümesi oluşturmak ve sistem ve/veya Kullanıcı tarafından atanan yönetilen kimliği bir sanal makine ölçek kümesinden etkinleştirmek ve kaldırmak için.
    - Kullanıcı tarafından atanan yönetilen kimlik oluşturmak için [yönetilen kimlik katılımcısı](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) rolü.
    - Kullanıcı tarafından atanan yönetilen kimliği ve sanal makine ölçek kümesine atamak ve kaldırmak için [yönetilen kimlik operatörü](/azure/role-based-access-control/built-in-roles#managed-identity-operator) rolü.
- CLI betiği örnekleri çalıştırmak için üç seçeneğiniz vardır:
    - Kullanım [Azure Cloud Shell](../../cloud-shell/overview.md) Azure portalından (sonraki bölüme bakın).
    - Katıştırılmış Azure Cloud Shell aracılığıyla her kod bloğunun sağ üst köşesinde bulunan "Try It" düğmesini kullanın.
    - [Azure CLI 'nın en son sürümünü yükler](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 veya üzeri) yerel bir CLı konsolu kullanmayı tercih ediyorsanız. 
      
      > [!NOTE]
      > Komutlar, [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)'nın en son sürümünü yansıtacak şekilde güncelleştirilmiştir.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimlik

Bu bölümde, Azure CLı kullanarak bir Azure sanal makine ölçek kümesi için sistem tarafından atanan yönetilen kimliği etkinleştirmeyi ve devre dışı bırakmayı öğreneceksiniz.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Azure sanal makine ölçek kümesi oluşturma sırasında sistem tarafından atanan yönetilen kimliği etkinleştir

Sistem tarafından atanan yönetilen kimlik etkin bir sanal makine ölçek kümesi oluşturmak için:

1. Azure CLI'yi yerel bir konsolda kullanıyorsanız, önce [az login](/cli/azure/reference-index#az-login) kullanarak Azure'da oturum açın. Sanal makine ölçek kümesini dağıtmak istediğiniz Azure aboneliğiyle ilişkili bir hesabı kullanın:

   ```azurecli-interactive
   az login
   ```

2. [Az Group Create](/cli/azure/group/#az-group-create)kullanılarak sanal makine ölçek kümesinin ve ilgili kaynaklarının kapsama ve dağıtımı için bir [kaynak grubu](../../azure-resource-manager/resource-group-overview.md#terminology) oluşturun. Bunun yerine kullanmak istediğiniz bir kaynak grubunuz zaten varsa, bu adımı atlayabilirsiniz:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. [Az VMSS Create](/cli/azure/vmss/#az-vmss-create) komutunu kullanarak bir sanal makine ölçek kümesi oluşturun. Aşağıdaki örnek, `--assign-identity` parametresi tarafından istenen şekilde, sistem tarafından atanan yönetilen kimlik ile *myvmss* adlı bir sanal makine ölçek kümesi oluşturur. `--admin-username` ve `--admin-password` parametreleri, sanal makinede oturum açmak için yönetici hesabının kullanıcı adı ve parolasını belirtir. Bu değerleri ortamınıza uyacak şekilde güncelleştirin: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Mevcut bir Azure sanal makine ölçek kümesi üzerinde sistem tarafından atanan yönetilen kimliği etkinleştir

Mevcut bir Azure sanal makine ölçek kümesi üzerinde sistem tarafından atanan yönetilen kimliği etkinleştirmeniz gerekiyorsa:

1. Azure CLI'yi yerel bir konsolda kullanıyorsanız, önce [az login](/cli/azure/reference-index#az-login) kullanarak Azure'da oturum açın. Sanal makine ölçek kümesini içeren Azure aboneliğiyle ilişkili bir hesap kullanın.

   ```azurecli-interactive
   az login
   ```

2. Mevcut bir VM 'ye sistem tarafından atanan yönetilen kimliği etkinleştirmek için [az VMSS Identity Assign](/cli/azure/vmss/identity/#az-vmss-identity-assign) komutunu kullanın:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure sanal makine ölçek kümesinden sistem tarafından atanan yönetilen kimliği devre dışı bırak

Artık sistem tarafından atanan yönetilen kimliğe ihtiyacı olmayan ancak hala Kullanıcı tarafından atanan yönetilen kimliklere ihtiyaç duymayan bir sanal makine ölçek kümesine sahipseniz, aşağıdaki komutu kullanın:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Artık sistem tarafından atanan yönetilen kimliğe ihtiyacı olmayan ve Kullanıcı tarafından atanan yönetilen kimliklere sahip olmayan bir sanal makineniz varsa, aşağıdaki komutu kullanın:

> [!NOTE]
> Değer `none` büyük/küçük harfe duyarlıdır. Küçük harfle yazılmalıdır. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```



## <a name="user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik

Bu bölümde, Azure CLı kullanarak Kullanıcı tarafından atanan yönetilen bir kimliği etkinleştirmeyi ve kaldırmayı öğreneceksiniz.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Bir sanal makine ölçek kümesi oluşturulurken Kullanıcı tarafından atanan yönetilen kimlik atama

Bu bölümde, bir sanal makine ölçek kümesi oluşturma ve sanal makine ölçek kümesine Kullanıcı tarafından atanan yönetilen kimlik ataması adım adım açıklanmaktadır. Kullanmak istediğiniz bir sanal makine ölçek kümesi zaten varsa, bu bölümü atlayın ve sonraki adıma geçin.

1. Kullanmak istediğiniz bir kaynak grubunuz zaten varsa, bu adımı atlayabilirsiniz. [Az Group Create](/cli/azure/group/#az-group-create)kullanarak Kullanıcı tarafından atanan yönetilen kimliğinizin kapsama ve dağıtımına yönelik bir [kaynak grubu](~/articles/azure-resource-manager/resource-group-overview.md#terminology) oluşturun. `<RESOURCE GROUP>` ve `<LOCATION>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Kullanıcı tarafından atanan yönetilen kimliği oluşturmak için [az identity create](/cli/azure/identity#az-identity-create) kullanın.  `-g` parametresi kullanıcı tarafından atanan yönetilen kimliğin oluşturulduğu kaynak grubunu belirtirken, `-n` parametresi de bunun adını belirtir. `<RESOURCE GROUP>` ve `<USER ASSIGNED IDENTITY NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Yanıt, aşağıdaki gibi oluşturulan kullanıcı tarafından atanan yönetilen kimliğin ayrıntılarını içerir. Kullanıcı tarafından `id` atanan yönetilen kimliğe atanan kaynak değeri aşağıdaki adımda kullanılır.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. [Az VMSS Create](/cli/azure/vmss/#az-vmss-create)komutunu kullanarak bir sanal makine ölçek kümesi oluşturun. Aşağıdaki örnek, `--assign-identity` parametresi tarafından belirtilen yeni kullanıcı tarafından atanan yönetilen kimlikle ilişkili bir sanal makine ölçek kümesi oluşturur. `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` ve `<USER ASSIGNED IDENTITY>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Mevcut bir sanal makine ölçek kümesine Kullanıcı tarafından atanan bir yönetilen kimlik atama

1. Kullanıcı tarafından atanan yönetilen kimliği oluşturmak için [az identity create](/cli/azure/identity#az-identity-create) kullanın.  `-g` parametresi kullanıcı tarafından atanan yönetilen kimliğin oluşturulduğu kaynak grubunu belirtirken, `-n` parametresi de bunun adını belirtir. `<RESOURCE GROUP>` ve `<USER ASSIGNED IDENTITY NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Yanıt, aşağıdaki gibi oluşturulan kullanıcı tarafından atanan yönetilen kimliğin ayrıntılarını içerir.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. [Az VMSS Identity Assign](/cli/azure/vmss/identity)komutunu kullanarak sanal makine ölçek kümesine Kullanıcı tarafından atanan yönetilen kimliği atayın. `<RESOURCE GROUP>` ve `<VIRTUAL MACHINE SCALE SET NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın. , `<USER ASSIGNED IDENTITY>` Önceki adımda oluşturulan kullanıcı tarafından atanan kimliğin kaynak `name` özelliğidir:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Kullanıcı tarafından atanan yönetilen kimliği bir Azure sanal makine ölçek kümesinden kaldırma

Bir sanal makine ölçek kümesinden Kullanıcı tarafından atanan yönetilen kimliği kaldırmak için [az VMSS Identity Remove](/cli/azure/vmss/identity#az-vmss-identity-remove)komutunu kullanın. Bu, sanal makine ölçek kümesine atanan tek kullanıcı tarafından atanan yönetilen kimlik ise, `UserAssigned` kimlik türü değerinden kaldırılır.  `<RESOURCE GROUP>` ve `<VIRTUAL MACHINE SCALE SET NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın. , `<USER ASSIGNED IDENTITY>` Şu `name` kullanılarak`az vmss identity show`sanal makine ölçek kümesinin kimlik bölümünde bulunan, Kullanıcı tarafından atanan yönetilen kimliğin özelliği olacaktır:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Sanal makine ölçek kümesinde sistem tarafından atanan yönetilen bir kimlik yoksa ve Kullanıcı tarafından atanan tüm yönetilen kimlikleri bundan kaldırmak istiyorsanız aşağıdaki komutu kullanın:

> [!NOTE]
> Değer `none` büyük/küçük harfe duyarlıdır. Küçük harfle yazılmalıdır.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Sanal makine ölçek kümesinde hem sistem tarafından atanan hem de Kullanıcı tarafından atanan Yönetilen kimlikler varsa, yalnızca sistem tarafından atanan yönetilen kimliği kullan ' a geçerek Kullanıcı tarafından atanan tüm kimlikleri kaldırabilirsiniz. Aşağıdaki komutu kullanın:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynaklarına genel bakış için yönetilen kimlik](overview.md)
- Tam Azure sanal makine ölçek kümesi oluşturma hızlı başlangıcı için bkz.: 

  - [CLı ile sanal makine ölçek kümesi oluşturma](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















