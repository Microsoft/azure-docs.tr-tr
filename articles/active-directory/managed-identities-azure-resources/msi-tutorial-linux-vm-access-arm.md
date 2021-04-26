---
title: Azure Resource Manager’a erişmek için Linux VM kullanıcı tarafından atanan yönetilen kimliği kullanma
description: Linux VM üzerinde bir kullanıcı tarafından atanan yönetilen kimliği kullanarak Azure Resource Manager'a erişme işleminde size yol gösteren bir öğretici.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: barclayn
ROBOTS: NOINDEX,NOFOLLOW
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9c7555235283e892741234b74ddb80ce3a13051
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784722"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Öğretici: Azure Resource Manager’a erişmek için Linux VM’de kullanıcı tarafından atanan yönetilen kimliği kullanma

Bu öğreticide, kullanıcı tarafından atanan yönetilen kimliği oluşturma, bunu Linux Sanal Makinesine (VM) atama ve bu kimliği Azure Resource Manager API’sine erişmek için kullanma işlemleri açıklanır. Azure kaynakları için yönetilen kimlikler, Azure tarafından otomatik olarak yönetilir. Bunlar, kodunuza kimlik bilgileri girmenize gerek kalmadan Azure AD kimlik doğrulamasını destekleyen hizmetlerde kimlik doğrulaması yapmaya olanak tanır. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Kullanıcı tarafından atanan yönetilen kimlik oluşturma
> * Kullanıcı tarafından atanan yönetilen kimliği Linux VM'ye atama 
> * Azure Resource Manager’da Kaynak Grubuna kullanıcı tarafından atanan yönetilen kimlik için erişim verme 
> * Kullanıcı tarafından atanan yönetilen kimliği kullanarak erişim belirteci alma ve Azure Resource Manager çağrısı yapmak için bunu kullanma 

## <a name="prerequisites"></a>Önkoşullar

- Yönetilen kimliklerin anlaşılmasıdır. Azure kaynakları için yönetilen kimlikler özelliği hakkında bilgi sahibi değilseniz bu [genel bakışı](overview.md) inceleyin. 
- Azure hesabı, [ücretsiz bir hesap için kaydolun](https://azure.microsoft.com/free/).
- Ayrıca bir Linux sanal makinesine ihtiyacınız vardır. Bu öğretici için bir sanal makine oluşturmanız gerekiyorsa, [Azure Portal Linux sanal makinesi oluşturma](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine) başlıklı makaleyi takip edebilirsiniz.
- Örnek betikleri çalıştırmak için iki seçeneğiniz vardır:
    - Kod bloklarının sağ üst köşesindeki **It TRY** düğmesini kullanarak açabileceğiniz [Azure Cloud Shell](../../cloud-shell/overview.md)kullanın.
    - [Azure CLI](/cli/azure/install-azure-cli)'nın en son sürümünü yükleyerek betikleri yerel olarak çalıştırın, sonra [az Login](/cli/azure/reference-index#az_login)kullanarak Azure 'da oturum açın.

## <a name="create-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik oluşturma

Kullanıcı tarafından atanan yönetilen kimliği oluşturmak için [az identity create](/cli/azure/identity#az_identity_create) kullanın. `-g` parametresi kullanıcı tarafından atanan yönetilen kimliğin oluşturulduğu kaynak grubunu belirtirken, `-n` parametresi de bunun adını belirtir. `<RESOURCE GROUP>` ve `<UAMI NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın:
    
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <UAMI NAME>
```

Yanıt, aşağıdaki örneğe benzer biçimde, oluşturulmuş kullanıcı tarafından atanan yönetilen kimliğin ayrıntılarını içerir. Kullanıcı tarafından atanan yönetilen kimliğinizin `id` değerini not alın, çünkü bu değer sonraki adımda kullanılacaktır:

```json
{
"clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
"clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
"id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>",
"location": "westcentralus",
"name": "<UAMI NAME>",
"principalId": "9012",
"resourceGroup": "<RESOURCE GROUP>",
"tags": {},
"tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
"type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

## <a name="assign-an-identity-to-your-linux-vm"></a>Linux VM 'nize bir kimlik atama

Kullanıcı tarafından atanan yönetilen kimlik, istemciler tarafından birden çok Azure kaynağında kullanılabilir. Aşağıdaki komutları kullanarak kullanıcı tarafından atanan yönetilen kimliği tek bir VM'ye atayın. `-IdentityID` parametresi için önceki adımda döndürülen `Id` özelliğini kullanın.

[Az VM Identity Assign](/cli/azure/vm)kullanarak Linux sanal makinenize Kullanıcı tarafından atanan yönetilen kimliği atayın. `<RESOURCE GROUP>` ve `<VM NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın. `--identities` parametre değeri için önceki adımda döndürülen `id` özelliğini kullanın.

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>"
```

## <a name="grant-access-to-a-resource-group-in-azure-resource-manager"></a>Azure Resource Manager bir kaynak grubuna erişim izni verme

Azure kaynakları için yönetilen kimlikler, kodunuzun Azure AD kimlik doğrulamasını destekleyen kaynak API'lerinde kimlik doğrulaması yapmak amacıyla erişim belirteçleri istemek için kullanabileceği kimlikleri sağlar. Bu öğreticide, kodunuz Azure Resource Manager API’sine erişir.  

Kodunuzun API'ye erişebilmesi için önce Azure Resource Manager'da kaynağa kimlik erişimi vermeniz gerekir. Bu durumda, içinde VM'nin yer aldığı Kaynak Grubudur. `<SUBSCRIPTION ID>` ve `<RESOURCE GROUP>` değerini ortamınıza uyacak şekilde güncelleştirin. Buna ek olarak, `<UAMI PRINCIPALID>` özelliğini [Kullanıcı tarafından atanan yönetilen kimlik oluşturma](#create-a-user-assigned-managed-identity) bölümünde `az identity create` komutuyla döndürülen `principalId` özelliğiyle değiştirin:

```azurecli-interactive
az role assignment create --assignee <UAMI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

Yanıt, aşağıdaki örneğe benzer biçimde, oluşturulmuş atamasının ayrıntılarını içerir:

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>VM kimliğini kullanarak erişim belirteci alma ve Resource Manager çağrısı yapmak için bunu kullanma 

Bu öğreticinin kalan bölümünde, daha önce oluşturmuş olduğunuz VM'den çalışacağız.

Bu adımları tamamlamak bir SSH istemciniz olmalıdır. Windows kullanıyorsanız, [Linux için Windows Alt Sistemi](/windows/wsl/about)'ndeki SSH istemcisini kullanabilirsiniz. 

1. Azure [portalında](https://portal.azure.com)oturum açın.
2. Portalda, **Sanal Makineler**'e ve Linux sanal makinesine gidin, ardından **Genel Bakış**'ta **Bağlan**'a tıklayın. VM'nize bağlanma dizesini kopyalayın.
3. Tercih ettiğiniz SSH istemcisiyle VM'ye bağlanın. Windows kullanıyorsanız, [Linux için Windows Alt Sistemi](/windows/wsl/about)'ndeki SSH istemcisini kullanabilirsiniz. SSSH istemcinizin anahtarlarını yapılandırmak için yardıma ihtiyacınız olursa, bkz. [Azure'da Windows ile SSH anahtarlarını kullanma](~/articles/virtual-machines/linux/ssh-from-windows.md) veya [Azure’da Linux VM’ler için SSH ortak ve özel anahtar çifti oluşturma](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
4. Terminal penceresinde, Azure Resource Manager erişim belirtecini almak için CURL'yi kullanarak Azure Instance Metadata Service (IMDS) kimlik uç noktasına bir istek gönderin.  

   Erişim belirtecini almaya yönelik CURL isteği aşağıdaki örnekte gösterilmektedir. `<CLIENT ID>` özelliğinin [Kullanıcı tarafında atanan yönetilen kimlik oluşturma](#create-a-user-assigned-managed-identity) bölümünde `az identity create` komutuyla döndürülen `clientId` özelliğiyle değiştirildiğinden emin olun: 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<UAMI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > `resource` parametresinin değeri Azure AD'nin beklediği değerle tam olarak eşleşmelidir. Resource Manager kaynak kimliği kullanıldığında, URI'nin sonundaki eğik çizgiyi de eklemelisiniz. 
    
    Yanıtta, Azure Resource Manager’a erişmek için ihtiyacınız olan erişim belirteci vardır. 
    
    Yanıt örneği:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

5. Azure Resource Manager’a erişmek için erişim belirtecini kullanın ve önceden kullanıcı tarafından atanan yönetilen kimliğiniz için erişim verdiğiniz Kaynak Grubunun özelliklerini okuyun. `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` değerlerinin daha önce belirttiğiniz değerlerle ve `<ACCESS TOKEN>` öğesinin önceki adımda döndürülen belirteçle değiştirildiğinden emin olun.

    > [!NOTE]
    > URL büyük/küçük harfe duyarlıdır; dolayısıyla daha önce Kaynak Grubunu adlandırırken kullandığınız büyük/küçük harf düzenini ve `resourceGroups` içindeki büyük "G" harfini kullanmaya dikkat edin.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Yanıtta, aşağıdaki örneğe benzer belirli Kaynak Grubu bilgileri yer alır: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, kullanıcı tarafından atanan bir yönetilen kimlik oluşturmayı ve Azure Resource Manager API'sine erişmek için bu kimliği bir Linux sanal makinesine eklemeyi öğrendiniz.  Azure Resource Manager hakkında daha fazla bilgi edinmek için bkz:

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
