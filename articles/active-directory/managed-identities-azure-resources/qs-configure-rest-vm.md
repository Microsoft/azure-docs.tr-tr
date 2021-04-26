---
title: REST kullanarak Azure VM 'de yönetilen kimlikleri Yapılandırma-Azure AD
description: REST API çağrısı yapmak için KıVRıMLı kullanarak bir Azure VM üzerinde sistem ve Kullanıcı tarafından atanan Yönetilen kimlikler yapılandırmaya yönelik adım adım yönergeler.
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
ms.openlocfilehash: 9b74e7d312133c24daad448e029a3c3d4cbdce79
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773090"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>REST API çağrılarını kullanarak Azure VM 'de Azure kaynakları için Yönetilen kimlikler yapılandırma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory ' de otomatik olarak yönetilen bir sistem kimliğiyle Azure hizmetleri sağlar. Bu kimliği, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz. 

Bu makalede, Azure Resource Manager REST uç noktasına çağrı yapmak için KıVRıMLı kullanarak, Azure VM 'de Azure kaynakları işlemleri için aşağıdaki yönetilen kimlikleri nasıl gerçekleştireceğinizi öğreneceksiniz:

- Azure VM 'de sistem tarafından atanan yönetilen kimliği etkinleştirme ve devre dışı bırakma
- Azure VM 'ye Kullanıcı tarafından atanan yönetilen kimlik ekleme ve kaldırma

Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için Yönetilen kimlikler hakkında bilgi sahibi değilseniz bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](overview.md). Sistem tarafından atanan ve Kullanıcı tarafından atanan yönetilen kimlik türleri hakkında bilgi edinmek için bkz. [yönetilen kimlik türleri](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimlik

Bu bölümde, Azure Resource Manager REST uç noktasına çağrı yapmak için KıVRıMLı kullanarak bir Azure VM 'de sistem tarafından atanan yönetilen kimliğin nasıl etkinleştirileceğini ve devre dışı bırakılacağını öğreneceksiniz.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Azure VM oluşturma sırasında sistem tarafından atanan yönetilen kimliği etkinleştirme

Sistem tarafından atanan yönetilen kimlik etkin bir Azure VM 'si oluşturmak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

1. VM'nizin ve onunla ilgili kaynakların kapsaması ve dağıtımı için, [az group create](/cli/azure/group/#az_group_create) komutunu kullanarak bir [kaynak grubu](../../azure-resource-manager/management/overview.md#terminology) oluşturun. Bunun yerine kullanmak istediğiniz bir kaynak grubunuz varsa, bu adımı atlayabilirsiniz:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. SANAL ağınız için bir [ağ arabirimi](/cli/azure/network/nic#az_network_nic_create) oluşturun:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. VM 'nizi sistem tarafından atanan bir yönetilen kimlikle oluşturmak için yetkilendirme üstbilgisindeki bir sonraki adımda kullanacağınız bir taşıyıcı erişim belirteci alın.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Azure Cloud Shell kullanarak, Azure Resource Manager REST uç noktasını çağırmak için KıVRıMLı kullanarak bir VM oluşturun. Aşağıdaki örnek, bir sistem tarafından atanan yönetilen kimlik ile *Myvm* ADLı bir VM oluşturur ve bu değer tarafından istek gövdesinde tanımlanmıştır `"identity":{"type":"SystemAssigned"}` . `<ACCESS TOKEN>`Bir taşıyıcı erişim belirteci ve ortamınız için uygun bir değer istediğinizde, önceki adımda aldığınız değerle değiştirin `<SUBSCRIPTION ID>` .

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **İstek üst bilgileri**
   
   |İstek üst bilgisi  |Açıklama  |
   |---------|---------|
   |*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
   |*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        | 
   
   **İstek gövdesi**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Mevcut bir Azure VM 'de sistem tarafından atanan kimliği etkinleştirme

Başlangıçta sağlanan bir VM 'de sistem tarafından atanan yönetilen kimliği etkinleştirmek için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

1. VM 'nizi sistem tarafından atanan bir yönetilen kimlikle oluşturmak için yetkilendirme üstbilgisindeki bir sonraki adımda kullanacağınız bir taşıyıcı erişim belirteci alın.

   ```azurecli-interactive
   az account get-access-token
   ```

2. SANAL makinenizde, `{"identity":{"type":"SystemAssigned"}` *myvm* adlı bir VM için olan değere göre istek gövdesinde tanımlandığı şekilde sistem tarafından atanan yönetilen kimliği ETKINLEŞTIRMEK üzere Azure Resource Manager REST uç noktasını ÇAĞıRMAK için aşağıdaki kıvrımlı komutunu kullanın.  `<ACCESS TOKEN>`Bir taşıyıcı erişim belirteci ve ortamınız için uygun bir değer istediğinizde, önceki adımda aldığınız değerle değiştirin `<SUBSCRIPTION ID>` .
   
   > [!IMPORTANT]
   > VM 'ye atanmış olan, Kullanıcı tarafından atanan yönetilen kimliklerin hiçbirini silmemenizi sağlamak için, bu KıVRıMLı komutunu kullanarak Kullanıcı tarafından atanan yönetilen kimlikleri listeetmeniz gerekir: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Yanıttaki değerde tanımlandığı şekilde VM 'ye atanan kullanıcı tarafından atanan yönetilen kimlikleriniz varsa `identity` , VM 'niz üzerinde sistem tarafından atanan yönetilen kimliği etkinleştirirken Kullanıcı tarafından atanan yönetilen kimliklerin nasıl tutulacağını gösteren 3. adıma atlayın.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **İstek üst bilgileri**

   |İstek üst bilgisi  |Açıklama  |
   |---------|---------|
   |*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
   |*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        | 
   
   **İstek gövdesi**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Kullanıcı tarafından atanan yönetilen kimlikleri olan bir VM 'de sistem tarafından atanan yönetilen kimliği etkinleştirmek için, değere eklemeniz gerekir `SystemAssigned` `type` .  
   
   Örneğin, sanal makinenizin Kullanıcı tarafından atanan yönetilen kimlikleri varsa `ID1` ve bu makineye atanmış BIR sanal makineye, `ID2` sistem tarafından atanan yönetilen kimlik eklemek ISTIYORSANıZ aşağıdaki kıvrımlı çağrısını kullanın. `<ACCESS TOKEN>`Ve `<SUBSCRIPTION ID>` değerlerini ortamınıza uygun değerlerle değiştirin.

   API sürümü `2018-06-01` , Kullanıcı tarafından atanan yönetilen kimlikleri, `userAssignedIdentities` `identityIds` API sürümünde kullanılan bir dizi biçimindeki değerin aksine bir sözlük biçiminde depolar `2017-12-01` .
   
   **APı SÜRÜMÜ 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **İstek üst bilgileri**

   |İstek üst bilgisi  |Açıklama  |
   |---------|---------|
   |*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
   |*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        | 

   **İstek gövdesi**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **APı SÜRÜMÜ 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **İstek üst bilgileri**

   |İstek üst bilgisi  |Açıklama  |
   |---------|---------|
   |*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
   |*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        | 

   **İstek gövdesi**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Azure VM 'den sistem tarafından atanan yönetilen kimliği devre dışı bırakma

Bir VM 'de sistem tarafından atanan yönetilen kimliği devre dışı bırakmak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

1. VM 'nizi sistem tarafından atanan bir yönetilen kimlikle oluşturmak için yetkilendirme üstbilgisindeki bir sonraki adımda kullanacağınız bir taşıyıcı erişim belirteci alın.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Sistem tarafından atanan yönetilen kimliği devre dışı bırakmak için Azure Resource Manager REST uç noktasını çağırmak üzere sanal makineyi KıVRıMLı kullanarak güncelleştirin.  Aşağıdaki örnek, sistem tarafından atanan yönetilen kimliği, `{"identity":{"type":"None"}}` *myvm* adlı bir VM 'deki değere göre istek gövdesinde tanımlanan şekilde devre dışı bırakır.  `<ACCESS TOKEN>`Bir taşıyıcı erişim belirteci ve ortamınız için uygun bir değer istediğinizde, önceki adımda aldığınız değerle değiştirin `<SUBSCRIPTION ID>` .

   > [!IMPORTANT]
   > VM 'ye atanmış olan, Kullanıcı tarafından atanan yönetilen kimliklerin hiçbirini silmemenizi sağlamak için, bu KıVRıMLı komutunu kullanarak Kullanıcı tarafından atanan yönetilen kimlikleri listeetmeniz gerekir: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Yanıttaki değerde tanımlandığı şekilde VM 'ye atanan kullanıcı tarafından atanan yönetilen kimlikleriniz varsa `identity` , sanal makinenizde sistem tarafından atanan yönetilen kimliği devre dışı bırakırken Kullanıcı tarafından atanan yönetilen kimliklerin nasıl tutulacağını gösteren 3. adıma atlayın.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **İstek üst bilgileri**

   |İstek üst bilgisi  |Açıklama  |
   |---------|---------|
   |*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
   |*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        | 

   **İstek gövdesi**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Kullanıcı tarafından atanan yönetilen kimlikleri olan bir sanal makineden sistem tarafından atanan yönetilen kimliği kaldırmak için `SystemAssigned` `{"identity":{"type:" "}}` `UserAssigned` `userAssignedIdentities` **API sürüm 2018-06-01** kullanıyorsanız değeri ve sözlük değerlerini tutarken değeri kaldırın. **API sürüm 2017-12-01** veya önceki bir sürümünü kullanıyorsanız, `identityIds` diziyi saklayın.

## <a name="user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik

Bu bölümde, Azure Resource Manager REST uç noktasına çağrı yapmak için bir Azure VM üzerinde kullanıcı tarafından atanan yönetilen kimliğin nasıl ekleneceğini ve kaldırılacağını öğrenirsiniz.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Azure VM oluşturma sırasında Kullanıcı tarafından atanan yönetilen kimlik atama

Bir VM 'ye Kullanıcı tarafından atanan bir kimlik atamak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ve [yönetilen kimlik işleci](../../role-based-access-control/built-in-roles.md#managed-identity-operator) rol atamalarına ihtiyacı vardır. Ek Azure AD dizin rolü ataması gerekli değildir.

1. VM 'nizi sistem tarafından atanan bir yönetilen kimlikle oluşturmak için yetkilendirme üstbilgisindeki bir sonraki adımda kullanacağınız bir taşıyıcı erişim belirteci alın.

   ```azurecli-interactive
   az account get-access-token
   ```

2. SANAL ağınız için bir [ağ arabirimi](/cli/azure/network/nic#az_network_nic_create) oluşturun:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. VM 'nizi sistem tarafından atanan bir yönetilen kimlikle oluşturmak için yetkilendirme üstbilgisindeki bir sonraki adımda kullanacağınız bir taşıyıcı erişim belirteci alın.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Burada bulunan yönergeleri kullanarak Kullanıcı tarafından atanan bir yönetilen kimlik oluşturun: [Kullanıcı tarafından atanan yönetilen kimlik oluşturma](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Azure Resource Manager REST uç noktasını çağırmak için KıVRıMLı kullanarak bir VM oluşturun. Aşağıdaki örnek, *Myresourcegroup* kaynak grubunda *MYVM* adlı bir VM 'yi `ID1` , istek gövdesinde belirtilen şekilde Kullanıcı tarafından atanan yönetilen kimlikle birlikte oluşturur `"identity":{"type":"UserAssigned"}` . `<ACCESS TOKEN>`Bir taşıyıcı erişim belirteci ve ortamınız için uygun bir değer istediğinizde, önceki adımda aldığınız değerle değiştirin `<SUBSCRIPTION ID>` .
 
   **APı SÜRÜMÜ 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **İstek üst bilgileri**

   |İstek üst bilgisi  |Açıklama  |
   |---------|---------|
   |*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
   |*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        | 

   **İstek gövdesi**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{  
             "imageReference":{  
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{  
                "caching":"ReadWrite",
                "managedDisk":{  
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **APı SÜRÜMÜ 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **İstek üst bilgileri**

   |İstek üst bilgisi  |Açıklama  |
   |---------|---------|
   |*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
   |*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        | 

   **İstek gövdesi**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Mevcut bir Azure VM 'sine Kullanıcı tarafından atanan bir yönetilen kimlik atama

Bir VM 'ye Kullanıcı tarafından atanan bir kimlik atamak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ve [yönetilen kimlik işleci](../../role-based-access-control/built-in-roles.md#managed-identity-operator) rol atamalarına ihtiyacı vardır. Ek Azure AD dizin rolü ataması gerekli değildir.

1. VM 'nizi sistem tarafından atanan bir yönetilen kimlikle oluşturmak için yetkilendirme üstbilgisindeki bir sonraki adımda kullanacağınız bir taşıyıcı erişim belirteci alın.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Burada bulunan yönergeleri kullanarak Kullanıcı tarafından atanan bir yönetilen kimlik oluşturun, [Kullanıcı tarafından atanan bir yönetilen kimlik oluşturun](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. VM 'ye atanan mevcut kullanıcı veya sistem tarafından atanan yönetilen kimlikleri silmemenizi sağlamak için aşağıdaki KıVRıMLı komutunu kullanarak VM 'ye atanan kimlik türlerini listeetmeniz gerekir. Sanal makine ölçek kümesine atanmış Yönetilen kimlikler varsa, bu değerler altında listelenmiştir `identity` .

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **İstek üst bilgileri**

   |İstek üst bilgisi  |Açıklama  |
   |---------|---------|
   |*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.

    Yanıttaki değerde tanımlandığı şekilde VM 'ye atanan kullanıcı veya sistem tarafından atanan yönetilen kimlikleriniz varsa `identity` , VM 'nize Kullanıcı tarafından atanan yönetilen kimlik eklerken, sistem tarafından atanan yönetilen kimliğin nasıl tutulacağını gösteren 5. adıma atlayın.

4. Sanal makinenize atanan kullanıcı tarafından atanan yönetilen kimliğiniz yoksa, VM 'ye ilk Kullanıcı tarafından atanan yönetilen kimliği atamak için Azure Resource Manager REST uç noktasını çağırmak üzere aşağıdaki KıVRıMLı komutunu kullanın.

   Aşağıdaki örnek, Kullanıcı tarafından atanan bir yönetilen kimliği `ID1` *myresourcegroup* kaynak grubundaki *MYVM* adlı bir VM 'ye atar.  `<ACCESS TOKEN>`Bir taşıyıcı erişim belirteci ve ortamınız için uygun bir değer istediğinizde, önceki adımda aldığınız değerle değiştirin `<SUBSCRIPTION ID>` .

   **APı SÜRÜMÜ 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **İstek üst bilgileri**

   |İstek üst bilgisi  |Açıklama  |
   |---------|---------|
   |*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
   |*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        |
 
   **İstek gövdesi**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **APı SÜRÜMÜ 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **İstek üst bilgileri**

   |İstek üst bilgisi  |Açıklama  |
   |---------|---------|
   |*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
   |*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        | 

   **İstek gövdesi**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

5. VM 'nize atanan, Kullanıcı tarafından atanan veya sistem tarafından atanan bir yönetilen Kimliğiniz varsa:
   
   **APı SÜRÜMÜ 2018-06-01**

   Kullanıcı tarafından atanan yönetilen kimliği `userAssignedIdentities` Sözlük değerine ekleyin.
    
   Örneğin, sistem tarafından atanan yönetilen kimliğiniz ve `ID1` Sanal makinenize Şu anda atanmış olan kullanıcı tarafından atanan yönetilen kimlik varsa ve Kullanıcı tarafından atanan yönetilen kimliği eklemek istiyorsanız `ID2` :

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **İstek üst bilgileri**

   |İstek üst bilgisi  |Açıklama  |
   |---------|---------|
   |*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
   |*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        | 

   **İstek gövdesi**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **APı SÜRÜMÜ 2017-12-01**

   Kullanıcı tarafından `identityIds` atanan yeni yönetilen kimliği eklerken dizi değerinde tutmak istediğiniz kullanıcı tarafından atanan yönetilen kimlikleri koruyun.

   Örneğin, sistem tarafından atanan yönetilen kimliğiniz ve `ID1` Sanal makinenize Şu anda atanmış olan kullanıcı tarafından atanan yönetilen kimlik varsa ve Kullanıcı tarafından atanan yönetilen kimliği eklemek istiyorsanız `ID2` : 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **İstek üst bilgileri**

   |İstek üst bilgisi  |Açıklama  |
   |---------|---------|
   |*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
   |*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        | 

   **İstek gövdesi**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Kullanıcı tarafından atanan yönetilen kimliği bir Azure VM 'den kaldırma

Kullanıcı tarafından atanan kimliği bir VM 'ye kaldırmak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.

1. VM 'nizi sistem tarafından atanan bir yönetilen kimlikle oluşturmak için yetkilendirme üstbilgisindeki bir sonraki adımda kullanacağınız bir taşıyıcı erişim belirteci alın.

   ```azurecli-interactive
   az account get-access-token
   ```

2. VM 'ye atanmasını veya sistem tarafından atanan yönetilen kimliği kaldırmayı istediğiniz var olan kullanıcı tarafından atanan yönetilen kimlikleri silmemenizi sağlamak için aşağıdaki KıVRıMLı komutunu kullanarak yönetilen kimlikleri listeetmeniz gerekir: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **İstek üst bilgileri**

   |İstek üst bilgisi  |Açıklama  |
   |---------|---------|
   |*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
   |*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.
 
   VM 'ye atanmış Yönetilen kimlikler varsa, bu `identity` değerler değerindeki yanıtta listelenir.

   Örneğin, Kullanıcı tarafından atanan yönetilen kimliklere sahipseniz `ID1` ve `ID2` Sanal makinenize atanırsınız ve yalnızca `ID1` atanmış ve sistem tarafından atanan kimliği tutmak istiyorsanız:
   
   **APı SÜRÜMÜ 2018-06-01**

   `null`Kaldırmak istediğiniz kullanıcı tarafından atanan yönetilen kimliğe ekleyin:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **İstek üst bilgileri**

   |İstek üst bilgisi  |Açıklama  |
   |---------|---------|
   |*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
   |*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        | 

   **İstek gövdesi**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **APı SÜRÜMÜ 2017-12-01**

   Dizide tutmak istediğiniz yalnızca Kullanıcı tarafından atanan yönetilen kimlikleri koru `identityIds` :

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **İstek üst bilgileri**

   |İstek üst bilgisi  |Açıklama  |
   |---------|---------|
   |*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
   |*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        | 

   **İstek gövdesi**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

SANAL makinenizde hem sistem tarafından atanan hem de Kullanıcı tarafından atanan Yönetilen kimlikler varsa, aşağıdaki komutu kullanarak yalnızca sistem tarafından atanan yönetilen kimliği kullan ' a geçerek Kullanıcı tarafından atanan tüm yönetilen kimlikleri kaldırabilirsiniz:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**İstek üst bilgileri**

|İstek üst bilgisi  |Açıklama  |
|---------|---------|
|*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
|*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın. | 

**İstek gövdesi**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
SANAL makinenizin yalnızca Kullanıcı tarafından atanan yönetilen kimlikleri varsa ve bunların tümünü kaldırmak istiyorsanız aşağıdaki komutu kullanın:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**İstek üst bilgileri**

|İstek üst bilgisi  |Açıklama  |
|---------|---------|
|*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
|*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.| 

**İstek gövdesi**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Sonraki adımlar

REST kullanarak Kullanıcı tarafından atanan Yönetilen kimlikler oluşturma, listeleme veya silme hakkında bilgi için bkz.:

- [REST API çağrılarını kullanarak Kullanıcı tarafından atanan Yönetilen kimlikler oluşturma, listeleme veya silme](how-to-manage-ua-identity-rest.md)
