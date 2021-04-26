---
title: Azure CLı kullanarak Azure VM 'de yönetilen kimlikleri Yapılandırma-Azure AD
description: Azure CLı kullanarak bir Azure VM 'de sistem ve Kullanıcı tarafından atanan yönetilen kimlikleri yapılandırmaya yönelik adım adım yönergeler.
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
ms.openlocfilehash: a8dbe9665612e888b7e7afe95a472ba6b0de8d48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762524"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Azure CLı kullanarak Azure VM 'de Azure kaynakları için Yönetilen kimlikler yapılandırma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory ' de otomatik olarak yönetilen kimlik ile Azure hizmetleri sağlar. Bu kimliği, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz. 

Bu makalede, Azure CLı 'yı kullanarak Azure VM 'de Azure kaynakları işlemleri için aşağıdaki yönetilen kimliklerin nasıl gerçekleştirileceğini öğreneceksiniz:

- Azure VM 'de sistem tarafından atanan yönetilen kimliği etkinleştirme ve devre dışı bırakma
- Azure VM 'ye Kullanıcı tarafından atanan yönetilen kimlik ekleme ve kaldırma

Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için Yönetilen kimlikler hakkında bilgi sahibi değilseniz bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](overview.md). Sistem tarafından atanan ve Kullanıcı tarafından atanan yönetilen kimlik türleri hakkında bilgi edinmek için bkz. [yönetilen kimlik türleri](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimlik

Bu bölümde, Azure CLı kullanarak bir Azure VM 'de sistem tarafından atanan yönetilen kimliği etkinleştirmeyi ve devre dışı bırakmayı öğreneceksiniz.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Azure VM oluşturma sırasında sistem tarafından atanan yönetilen kimliği etkinleştirme

Sistem tarafından atanan yönetilen kimlik etkin bir Azure VM 'si oluşturmak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

1. VM'nizin ve onunla ilgili kaynakların kapsaması ve dağıtımı için, [az group create](/cli/azure/group/#az_group_create) komutunu kullanarak bir [kaynak grubu](../../azure-resource-manager/management/overview.md#terminology) oluşturun. Bunun yerine kullanmak istediğiniz bir kaynak grubunuz varsa, bu adımı atlayabilirsiniz:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. [az vm create](/cli/azure/vm/#az_vm_create) kullanarak VM oluşturun. Aşağıdaki örnek, parametresi tarafından istenen şekilde, sistem tarafından atanan yönetilen kimlik ile *Myvm* ADLı bir VM oluşturur `--assign-identity` . `--admin-username` ve `--admin-password` parametreleri, sanal makinede oturum açmak için yönetici hesabının kullanıcı adı ve parolasını belirtir. Bu değerleri ortamınıza uyacak şekilde güncelleştirin: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Mevcut bir Azure VM 'de sistem tarafından atanan yönetilen kimliği etkinleştirme

Bir VM 'de sistem tarafından atanan yönetilen kimliği etkinleştirmek için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

1. Azure CLI'yi yerel bir konsolda kullanıyorsanız, önce [az login](/cli/azure/reference-index#az_login) kullanarak Azure'da oturum açın. VM 'yi içeren Azure aboneliğiyle ilişkili bir hesap kullanın.

   ```azurecli-interactive
   az login
   ```

2. [](/cli/azure/vm/identity/) `identity assign` Sistem tarafından atanan KIMLIĞI mevcut bir VM 'ye etkinleştirmek için az VM Identity Assign komutunu kullanın:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Azure VM 'den sistem tarafından atanan kimliği devre dışı bırakma

Bir VM 'de sistem tarafından atanan yönetilen kimliği devre dışı bırakmak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

Artık sistem tarafından atanan kimliğe ihtiyacı olmayan ancak hala Kullanıcı tarafından atanan kimliklere ihtiyaç duyulmayan bir sanal makineniz varsa, aşağıdaki komutu kullanın:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Artık sistem tarafından atanan kimliğe ihtiyacı olmayan ve Kullanıcı tarafından atanan kimlikleri olmayan bir sanal makineniz varsa, aşağıdaki komutu kullanın:

> [!NOTE]
> Değer `none` büyük/küçük harfe duyarlıdır. Küçük harfle yazılmalıdır. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik

Bu bölümde, Azure CLı kullanarak bir Azure VM 'sinden Kullanıcı tarafından atanan yönetilen kimlik ekleme ve kaldırma hakkında bilgi edineceksiniz. Kullanıcı tarafından atanan yönetilen kimliğinizi VM 'nizin farklı bir RG olarak oluşturursanız. Sanal makinenize atamak için yönetilen kimliğinizin URL 'sini kullanmanız gerekir.
Yani--kimlikler "/Subscriptions/ <SUBID> /ResourceGroups/ <RESROURCEGROUP> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER_ASSIGNED_ID_NAME>"

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Azure VM oluşturma sırasında Kullanıcı tarafından atanan yönetilen kimlik atama

Oluşturma sırasında bir VM 'ye Kullanıcı tarafından atanan bir kimlik atamak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ve [yönetilen kimlik işleci](../../role-based-access-control/built-in-roles.md#managed-identity-operator) rol atamalarına ihtiyacı vardır. Ek Azure AD dizin rolü ataması gerekli değildir.

1. Kullanmak istediğiniz bir kaynak grubunuz zaten varsa, bu adımı atlayabilirsiniz. [Az Group Create](/cli/azure/group/#az_group_create)kullanarak Kullanıcı tarafından atanan yönetilen kimliğinizin kapsama ve dağıtımına yönelik bir [kaynak grubu](~/articles/azure-resource-manager/management/overview.md#terminology) oluşturun. `<RESOURCE GROUP>` ve `<LOCATION>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Kullanıcı tarafından atanan yönetilen kimliği oluşturmak için [az identity create](/cli/azure/identity#az_identity_create) kullanın.  `-g` parametresi kullanıcı tarafından atanan yönetilen kimliğin oluşturulduğu kaynak grubunu belirtirken, `-n` parametresi de bunun adını belirtir.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Yanıt, aşağıdaki gibi oluşturulan kullanıcı tarafından atanan yönetilen kimliğin ayrıntılarını içerir. Kullanıcı tarafından atanan yönetilen kimliğe atanan kaynak KIMLIĞI değeri aşağıdaki adımda kullanılır.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
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

3. [az vm create](/cli/azure/vm/#az_vm_create) kullanarak VM oluşturun. Aşağıdaki örnek, parametresi tarafından belirtilen yeni kullanıcı tarafından atanan kimlikle ilişkili bir VM oluşturur `--assign-identity` . `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` ve `<USER ASSIGNED IDENTITY NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Mevcut bir Azure VM 'sine Kullanıcı tarafından atanan bir yönetilen kimlik atama

Bir VM 'ye Kullanıcı tarafından atanan bir kimlik atamak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ve [yönetilen kimlik işleci](../../role-based-access-control/built-in-roles.md#managed-identity-operator) rol atamalarına ihtiyacı vardır. Ek Azure AD dizin rolü ataması gerekli değildir.

1. Kullanıcı tarafından atanan kimliği oluşturmak için [az identity create](/cli/azure/identity#az_identity_create) kullanın.  `-g`Parametresi, Kullanıcı tarafından atanan kimliğin oluşturulduğu kaynak grubunu belirtir ve `-n` parametresi adını belirtir. `<RESOURCE GROUP>` ve `<USER ASSIGNED IDENTITY NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

    > [!IMPORTANT]
    > Adında özel karakterler (alt çizgi) ile Kullanıcı tarafından atanan Yönetilen kimlikler oluşturma işlemi şu anda desteklenmiyor. Lütfen alfasayısal karakterler kullanın. Güncelleştirmeler için sonra yeniden denetleyin.  Daha fazla bilgi için bkz. [SSS ve bilinen sorunlar](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Yanıt, aşağıdaki gibi oluşturulan kullanıcı tarafından atanan yönetilen kimliğin ayrıntılarını içerir. 

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

2. [Az VM Identity Assign](/cli/azure/vm)kullanarak VM 'nize Kullanıcı tarafından atanan kimlik atayın. `<RESOURCE GROUP>` ve `<VM NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın. , `<USER ASSIGNED IDENTITY NAME>` Önceki adımda oluşturulduğu gibi kullanıcı tarafından atanan yönetilen kimliğin kaynak `name` özelliğidir. Kullanıcı tarafından atanan yönetilen kimliğinizi VM 'nizin farklı bir RG ile oluşturduysanız. Yönetilen kimliğinizin URL 'sini kullanmanız gerekir.

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Kullanıcı tarafından atanan yönetilen kimliği bir Azure VM 'den kaldırma

Kullanıcı tarafından atanan kimliği bir VM 'ye kaldırmak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir. 

Sanal makineye atanan tek kullanıcı tarafından atanan yönetilen kimlik ise, `UserAssigned` kimlik türü değerinden kaldırılır.  `<RESOURCE GROUP>` ve `<VM NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın. , `<USER ASSIGNED IDENTITY>` Kullanıcı tarafından atanan kimliğin özelliği olur ve bu, şu `name` kullanılarak sanal makinenin kimlik bölümünde bulunabilir `az vm identity show` :

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

SANAL makinenizin sistem tarafından atanan bir yönetilen kimliği yoksa ve bundan sonra Kullanıcı tarafından atanan tüm kimlikleri kaldırmak istiyorsanız aşağıdaki komutu kullanın:

> [!NOTE]
> Değer `none` büyük/küçük harfe duyarlıdır. Küçük harfle yazılmalıdır.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

SANAL makinenizin hem sistem tarafından atanan hem de Kullanıcı tarafından atanan kimlikleri varsa, yalnızca sistem tarafından atanan ' i kullanarak Kullanıcı tarafından atanan tüm kimlikleri kaldırabilirsiniz. Aşağıdaki komutu kullanın:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Sonraki adımlar
- [Azure kaynaklarına genel bakış için Yönetilen kimlikler](overview.md)
- Azure VM oluşturma hızlı başlangıçlarını tam olarak görmek için bkz.: 
  - [CLı ile Windows sanal makinesi oluşturma](../../virtual-machines/windows/quick-create-cli.md)  
  - [CLı ile Linux sanal makinesi oluşturma](../../virtual-machines/linux/quick-create-cli.md)
