---
title: Sanal makine ölçek kümesi 'nde yönetilen kimlikleri Yapılandırma-Azure CLı-Azure AD
description: Azure CLı kullanarak bir Azure sanal makine ölçek kümesi üzerinde sistem ve Kullanıcı tarafından atanan Yönetilen kimlikler yapılandırmaya yönelik adım adım yönergeler.
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
ms.openlocfilehash: 0e6e7e1724247c0e6d2b9db2fdf6980e8ef553c7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788196"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Azure CLı kullanarak bir sanal makine ölçek kümesindeki Azure kaynakları için Yönetilen kimlikler yapılandırma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory ' de otomatik olarak yönetilen kimlik ile Azure hizmetleri sağlar. Bu kimliği, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz. 

Bu makalede, Azure CLı kullanarak Azure sanal makine ölçek kümesindeki Azure kaynakları işlemleri için aşağıdaki yönetilen kimliklerin nasıl gerçekleştirileceğini öğreneceksiniz:
- Azure sanal makine ölçek kümesi üzerinde sistem tarafından atanan yönetilen kimliği etkinleştirme ve devre dışı bırakma
- Azure sanal makine ölçek kümesine Kullanıcı tarafından atanan yönetilen kimlik ekleme ve kaldırma

Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için Yönetilen kimlikler hakkında bilgi sahibi değilseniz bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](overview.md). Sistem tarafından atanan ve Kullanıcı tarafından atanan yönetilen kimlik türleri hakkında bilgi edinmek için bkz. [yönetilen kimlik türleri](overview.md#managed-identity-types).

- Bu makaledeki yönetim işlemlerini gerçekleştirmek için, hesabınız aşağıdaki Azure rol tabanlı erişim denetimi atamalarına ihtiyaç duyuyor:

  - Sanal makine [katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) bir sanal makine ölçek kümesi oluşturmak ve sistem ve/veya Kullanıcı tarafından atanan yönetilen kimliği bir sanal makine ölçek kümesinden etkinleştirmek ve kaldırmak için.

  - Kullanıcı tarafından atanan yönetilen kimlik oluşturmak için [yönetilen kimlik katılımcısı](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) rolü.

  - Kullanıcı tarafından atanan yönetilen kimliği ve sanal makine ölçek kümesine atamak ve kaldırmak için [yönetilen kimlik operatörü](../../role-based-access-control/built-in-roles.md#managed-identity-operator) rolü.

  > [!NOTE]
  > Ek Azure AD dizin rolü ataması gerekli değildir.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimlik

Bu bölümde, Azure CLı kullanarak bir Azure sanal makine ölçek kümesi için sistem tarafından atanan yönetilen kimliği etkinleştirmeyi ve devre dışı bırakmayı öğreneceksiniz.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Azure sanal makine ölçek kümesi oluşturma sırasında sistem tarafından atanan yönetilen kimliği etkinleştir

Sistem tarafından atanan yönetilen kimlik etkin bir sanal makine ölçek kümesi oluşturmak için:

1. [Az Group Create](/cli/azure/group/#az_group_create)kullanılarak sanal makine ölçek kümesinin ve ilgili kaynaklarının kapsama ve dağıtımı için bir [kaynak grubu](../../azure-resource-manager/management/overview.md#terminology) oluşturun. Bunun yerine kullanmak istediğiniz bir kaynak grubunuz zaten varsa, bu adımı atlayabilirsiniz:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. Bir sanal makine ölçek kümesi [oluşturun](/cli/azure/vmss/#az_vmss_create) . Aşağıdaki örnek, parametresi tarafından istenen şekilde, sistem tarafından atanan yönetilen kimlik ile *Myvmss* adlı bir sanal makine ölçek kümesi oluşturur `--assign-identity` . `--admin-username` ve `--admin-password` parametreleri, sanal makinede oturum açmak için yönetici hesabının kullanıcı adı ve parolasını belirtir. Bu değerleri ortamınıza uyacak şekilde güncelleştirin: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Mevcut bir Azure sanal makine ölçek kümesi üzerinde sistem tarafından atanan yönetilen kimliği etkinleştir

Mevcut bir Azure sanal makine ölçek kümesi üzerinde sistem tarafından atanan yönetilen kimliği [etkinleştirmeniz](/cli/azure/vmss/identity/#az_vmss_identity_assign) gerekiyorsa:

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

1. Kullanmak istediğiniz bir kaynak grubunuz zaten varsa, bu adımı atlayabilirsiniz. [Az Group Create](/cli/azure/group/#az_group_create)kullanarak Kullanıcı tarafından atanan yönetilen kimliğinizin kapsama ve dağıtımına yönelik bir [kaynak grubu](~/articles/azure-resource-manager/management/overview.md#terminology) oluşturun. `<RESOURCE GROUP>` ve `<LOCATION>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Kullanıcı tarafından atanan yönetilen kimliği oluşturmak için [az identity create](/cli/azure/identity#az_identity_create) kullanın.  `-g` parametresi kullanıcı tarafından atanan yönetilen kimliğin oluşturulduğu kaynak grubunu belirtirken, `-n` parametresi de bunun adını belirtir. `<RESOURCE GROUP>` ve `<USER ASSIGNED IDENTITY NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Yanıt, aşağıdaki gibi oluşturulan kullanıcı tarafından atanan yönetilen kimliğin ayrıntılarını içerir. `id`Kullanıcı tarafından atanan yönetilen kimliğe atanan kaynak değeri aşağıdaki adımda kullanılır.

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

3. Bir sanal makine ölçek kümesi [oluşturun](/cli/azure/vmss/#az_vmss_create) . Aşağıdaki örnek, parametresi tarafından belirtilen yeni kullanıcı tarafından atanan yönetilen kimlikle ilişkili bir sanal makine ölçek kümesi oluşturur `--assign-identity` . `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` ve `<USER ASSIGNED IDENTITY>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Mevcut bir sanal makine ölçek kümesine Kullanıcı tarafından atanan bir yönetilen kimlik atama

1. Kullanıcı tarafından atanan yönetilen kimliği oluşturmak için [az identity create](/cli/azure/identity#az_identity_create) kullanın.  `-g` parametresi kullanıcı tarafından atanan yönetilen kimliğin oluşturulduğu kaynak grubunu belirtirken, `-n` parametresi de bunun adını belirtir. `<RESOURCE GROUP>` ve `<USER ASSIGNED IDENTITY NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

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

2. Kullanıcı tarafından atanan yönetilen kimliği sanal makine ölçek kümesine [atayın](/cli/azure/vmss/identity) . `<RESOURCE GROUP>` ve `<VIRTUAL MACHINE SCALE SET NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın. , `<USER ASSIGNED IDENTITY>` Önceki adımda oluşturulan kullanıcı tarafından atanan kimliğin kaynak `name` özelliğidir:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Kullanıcı tarafından atanan yönetilen kimliği bir Azure sanal makine ölçek kümesinden kaldırma

Bir sanal makine ölçek kümesi kullanımıyla Kullanıcı tarafından atanan bir yönetilen kimliği [kaldırmak](/cli/azure/vmss/identity#az_vmss_identity_remove) için `az vmss identity remove` . Bu, sanal makine ölçek kümesine atanan tek kullanıcı tarafından atanan yönetilen kimlik ise, `UserAssigned` kimlik türü değerinden kaldırılır.  `<RESOURCE GROUP>` ve `<VIRTUAL MACHINE SCALE SET NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın. , `<USER ASSIGNED IDENTITY>` Şu `name` kullanılarak sanal makine ölçek kümesinin kimlik bölümünde bulunan, Kullanıcı tarafından atanan yönetilen kimliğin özelliği olacaktır `az vmss identity show` :

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

- [Azure kaynaklarına genel bakış için Yönetilen kimlikler](overview.md)
- Tam Azure sanal makine ölçek kümesi oluşturma hızlı başlangıcı için bkz. [CLI Ile sanal makine ölçek kümesi oluşturma](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)
