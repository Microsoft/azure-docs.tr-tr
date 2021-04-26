---
title: Kapsayıcı grubunda yönetilen kimliği etkinleştir
description: Diğer Azure hizmetleriyle kimlik doğrulayabilecek Azure Container Instances yönetilen bir kimliği nasıl etkinleştirebileceğinizi öğrenin
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: f8f3c646487d86f4e1bce13ccbf28992b8b1497a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764018"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Azure Container Instances ile yönetilen kimlikleri kullanma

Kod içinde herhangi bir gizli dizi veya kimlik bilgisi olmadan diğer Azure hizmetleriyle etkileşim kuran Azure Container Instances kod çalıştırmak için [Azure kaynakları için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) kullanın. Özelliği, Azure Active Directory otomatik olarak yönetilen bir kimlikle Azure Container Instances dağıtımı sağlar.

Bu makalede, Azure Container Instances ve içindeki yönetilen kimlikler hakkında daha fazla bilgi edinebilirsiniz:

> [!div class="checklist"]
> * Kapsayıcı grubunda Kullanıcı tarafından atanan veya sistem tarafından atanan bir kimliği etkinleştirme
> * Azure Anahtar Kasası 'na kimlik erişimi verme
> * Çalışan bir kapsayıcıdan anahtar kasasına erişmek için yönetilen kimliği kullanma

Diğer Azure hizmetlerine erişmek için Azure Container Instances kimlikleri etkinleştirmek ve kullanmak üzere örnekleri uyarlayın. Bu örnekler etkileşimlidir. Ancak, kapsayıcıda kapsayıcı görüntüleriniz Azure hizmetlerine erişmek için kodu çalıştırır.
 
> [!IMPORTANT]
> Bu özellik şu anda önizleme sürümündedir. Önizlemeler, [ek kullanım koşullarını](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir. Şu anda Azure Container Instances Yönetilen kimlikler yalnızca Linux kapsayıcılarıyla desteklenir ve henüz Windows kapsayıcılarıyla desteklenmez.

## <a name="why-use-a-managed-identity"></a>Yönetilen kimlik neden kullanılmalıdır?

Kapsayıcı kodunuzda kimlik bilgilerini yönetmeksizin [Azure AD kimlik doğrulamasını destekleyen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) herhangi bir hizmette kimlik doğrulaması yapmak için çalışan bir kapsayıcıda yönetilen bir kimlik kullanın. AD kimlik doğrulamasını desteklemeyen hizmetler için, gizli dizileri bir Azure anahtar kasasında saklayabilir ve kimlik bilgilerini almak için anahtar kasasına erişmek üzere yönetilen kimliği kullanabilirsiniz. Yönetilen kimlik kullanma hakkında daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="enable-a-managed-identity"></a>Yönetilen bir kimliği etkinleştirme

 Bir kapsayıcı grubu oluşturduğunuzda bir [Containergroupıdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) özelliği ayarlayarak bir veya daha fazla yönetilen kimliği etkinleştirin. Ayrıca, bir kapsayıcı grubu çalıştıktan sonra yönetilen kimlikleri etkinleştirebilir veya güncelleştirebilirsiniz-her iki işlem de kapsayıcı grubunun yeniden başlatılmasına neden olur. Yeni veya mevcut bir kapsayıcı grubundaki kimlikleri ayarlamak için Azure CLı, Kaynak Yöneticisi şablonu, YAML dosyası veya başka bir Azure aracını kullanın. 

Azure Container Instances hem yönetilen Azure kimlik türlerini destekler: Kullanıcı tarafından atanan ve sistem tarafından atanan. Bir kapsayıcı grubunda, sistem tarafından atanan bir kimliği, bir veya daha fazla kullanıcı tarafından atanan kimliği veya her iki tür kimliği etkinleştirebilirsiniz. Azure kaynakları için Yönetilen kimlikler hakkında bilginiz varsa bkz. [genel bakış](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="use-a-managed-identity"></a>Yönetilen kimlik kullanma

Yönetilen bir kimlik kullanmak için, kimliğin abonelikte bir veya daha fazla Azure hizmet kaynağına (bir Web uygulaması, bir Anahtar Kasası veya bir depolama hesabı gibi) erişim verilmesi gerekir. Çalışan bir kapsayıcıda yönetilen bir kimlik kullanılması, bir Azure VM 'de kimlik kullanılmasına benzer. [Belirteç](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure POWERSHELL veya Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)veya [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)'larını kullanma için VM yönergelerine bakın.

### <a name="limitations"></a>Sınırlamalar

* Şu anda bir sanal ağa dağıtılan bir kapsayıcı grubunda yönetilen bir kimlik kullanamazsınız.
* Bir kapsayıcı grubu oluştururken Azure Container Registry bir görüntü çekmek için yönetilen bir kimlik kullanamazsınız. Kimlik yalnızca çalışan bir kapsayıcı içinde kullanılabilir.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.49 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-an-azure-key-vault"></a>Azure Anahtar Kasası oluşturma

Bu makaledeki örneklerde Azure Anahtar Kasası gizliliğine erişmek için Azure Container Instances yönetilen bir kimlik kullanılır. 

İlk olarak aşağıdaki [az group create](/cli/azure/group#az_group_create) komutunu kullanarak *eastus* bölgesinde *myResourceGroup* adlı bir kaynak grubu oluşturun:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Bir Anahtar Kasası oluşturmak için [az keykasacreate](/cli/azure/keyvault#az_keyvault_create) komutunu kullanın. Benzersiz bir Anahtar Kasası adı belirttiğinizden emin olun. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

[Az keykasasecret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) komutunu kullanarak anahtar kasasında örnek bir gizli dizi depolayın:

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Azure Container Instances içinde Kullanıcı tarafından atanan veya sistem tarafından atanan bir yönetilen kimlik kullanarak anahtar kasasına erişmek için aşağıdaki örneklerle devam edin.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Örnek 1: Azure anahtar kasasına erişmek için Kullanıcı tarafından atanan bir kimlik kullanma

### <a name="create-an-identity"></a>Kimlik oluşturma

İlk olarak [az Identity Create](/cli/azure/identity#az_identity_create) komutunu kullanarak aboneliğinizde bir kimlik oluşturun. Anahtar kasasını oluşturmak için kullanılan kaynak grubunu kullanabilir veya farklı bir tane kullanabilirsiniz.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Aşağıdaki adımlarda kimliği kullanmak için, kimliğin hizmet sorumlusu KIMLIĞINI ve kaynak KIMLIĞINI değişkenlerde depolamak üzere [az Identity Show](/cli/azure/identity#az_identity_show) komutunu kullanın.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query id --output tsv)
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Anahtar kasasına Kullanıcı tarafından atanan kimlik erişimi verme

Anahtar kasasında bir erişim ilkesi ayarlamak için aşağıdaki [az keykasası Set-Policy](/cli/azure/keyvault) komutunu çalıştırın. Aşağıdaki örnek, Kullanıcı tarafından atanan kimliğin anahtar kasasından gizli dizileri almasına izin verir:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="enable-user-assigned-identity-on-a-container-group"></a>Kapsayıcı grubunda Kullanıcı tarafından atanan kimliği etkinleştirme

Microsoft 'un görüntüsüne dayalı bir kapsayıcı örneği oluşturmak için aşağıdaki [az Container Create](/cli/azure/container#az_container_create) komutunu çalıştırın `azure-cli` . Bu örnek, diğer Azure hizmetlerine erişmek için Azure CLı 'yı çalıştırmak üzere etkileşimli olarak kullanabileceğiniz tek bir kapsayıcı grubu sağlar. Bu bölümde, yalnızca temel işletim sistemi kullanılır. Kapsayıcıda Azure CLı 'yı kullanma örneği için bkz. [bir kapsayıcı grubunda sistem tarafından atanan kimliği etkinleştirme](#enable-system-assigned-identity-on-a-container-group). 

`--assign-identity`Parametresi, Kullanıcı tarafından atanan yönetilen kimliğinizi gruba geçirir. Uzun süre çalışan komut kapsayıcıyı çalışır durumda tutar. Bu örnek, anahtar kasasını oluşturmak için kullanılan kaynak grubunu kullanır, ancak farklı bir tane belirtebilirsiniz.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Birkaç saniye içinde Azure CLI'den dağıtımın tamamlandığını belirten bir yanıt almanız gerekir. [Az Container Show](/cli/azure/container#az_container_show) komutuyla durumunu kontrol edin.

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

`identity`Çıktıda bulunan bölüm, kimliğin kapsayıcı grubunda ayarlandığını gösteren aşağıdakine benzer şekilde görünür. `principalID`Altında, `userAssignedIdentities` Azure Active Directory içinde oluşturduğunuz kimliğin hizmet sorumlusu bulunur:

```console
[...]
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
[...]
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Anahtar kasasından gizli dizi almak için Kullanıcı tarafından atanan kimlik kullan

Artık anahtar kasasına erişmek için çalışan kapsayıcı örneği içinde yönetilen kimliği kullanabilirsiniz. Önce kapsayıcıda bir bash kabuğu başlatın:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Kapsayıcıda bash kabuğu 'nda aşağıdaki komutları çalıştırın. Anahtar kasasında kimlik doğrulaması yapmak için Azure Active Directory kullanmak üzere bir erişim belirteci almak için aşağıdaki komutu çalıştırın:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Çıkış:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Erişim belirtecini, kimlik doğrulaması için sonraki komutlarda kullanılacak bir değişkende depolamak için aşağıdaki komutu çalıştırın:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Artık anahtar kasasında kimlik doğrulamak ve gizli dizi okumak için erişim belirtecini kullanın. Anahtar kasanızın adını URL 'de (*https: \/ /mykeyvault.Vault.Azure.net/..*.) değiştirdiğinizden emin olun:

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Yanıt, gizliliği gösteren aşağıdakine benzer şekilde görünür. Kodunuzda, parolayı elde etmek için bu çıktıyı ayrıştırırdınız. Daha sonra, başka bir Azure kaynağına erişmek için sonraki bir işlemde gizli dizi kullanın.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Örnek 2: Azure Anahtar Kasası 'na erişmek için sistem tarafından atanan bir kimlik kullanma

### <a name="enable-system-assigned-identity-on-a-container-group"></a>Bir kapsayıcı grubunda sistem tarafından atanan kimliği etkinleştirme

Microsoft 'un görüntüsüne dayalı bir kapsayıcı örneği oluşturmak için aşağıdaki [az Container Create](/cli/azure/container#az_container_create) komutunu çalıştırın `azure-cli` . Bu örnek, diğer Azure hizmetlerine erişmek için Azure CLı 'yı çalıştırmak üzere etkileşimli olarak kullanabileceğiniz tek bir kapsayıcı grubu sağlar. 

`--assign-identity`Ek değer içermeyen parametre, grupta sistem tarafından atanan yönetilen kimliği mümkün bir şekilde sunar. Kimlik, kapsayıcı grubunun kaynak grubunun kapsamına alınır. Uzun süre çalışan komut kapsayıcıyı çalışır durumda tutar. Bu örnek, kimlik kapsamındaki anahtar kasasını oluşturmak için kullanılan kaynak grubunu kullanır.

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

Birkaç saniye içinde Azure CLI'den dağıtımın tamamlandığını belirten bir yanıt almanız gerekir. [Az Container Show](/cli/azure/container#az_container_show) komutuyla durumunu kontrol edin.

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

`identity`Çıktıda bulunan bölüm, Azure Active Directory ' de sistem tarafından atanan bir kimliğin oluşturulduğunu gösteren aşağıdakine benzer şekilde görünür:

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

`principalId`Sonraki adımlarda kullanmak için, kimliğin değerine (hizmet asıl kimliği) bir değişken ayarlayın.

```azurecli-interactive
spID=$(az container show \
  --resource-group myResourceGroup \
  --name mycontainer \
  --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Anahtar kasasına kapsayıcı grubu erişimi verme

Anahtar kasasında bir erişim ilkesi ayarlamak için aşağıdaki [az keykasası Set-Policy](/cli/azure/keyvault) komutunu çalıştırın. Aşağıdaki örnek, sistem tarafından yönetilen kimliğin anahtar kasasından gizli dizileri almasına izin verir:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Anahtar kasasından gizli dizi almak için kapsayıcı grubu kimliğini kullanın

Artık yönetilen kimliği, çalışan kapsayıcı örneği içindeki anahtar kasasına erişmek için kullanabilirsiniz. Önce kapsayıcıda bir bash kabuğu başlatın:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Kapsayıcıda bash kabuğu 'nda aşağıdaki komutları çalıştırın. Önce yönetilen kimliği kullanarak Azure CLı 'da oturum açın:

```bash
az login --identity
```

Çalışan kapsayıcıdan, anahtar kasasından gizli dizi alın:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

Gizli dizinin değeri alınır:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak yönetilen kimliği etkinleştirme

Bir [Kaynak Yöneticisi şablonu](container-instances-multi-container-group.md)kullanarak bir kapsayıcı grubundaki yönetilen bir kimliği etkinleştirmek için `identity` `Microsoft.ContainerInstance/containerGroups` nesnesinin özelliğini bir nesne ile ayarlayın `ContainerGroupIdentity` . Aşağıdaki kod parçacıkları `identity` farklı senaryolar için yapılandırılmış özelliği gösterir. [Kaynak Yöneticisi şablonu başvurusuna](/azure/templates/microsoft.containerinstance/containergroups)bakın. En az bir `apiVersion` belirtin `2018-10-01` .

### <a name="user-assigned-identity"></a>Kullanıcı tarafından atanan kimlik

Kullanıcı tarafından atanan kimlik, formun bir kaynak KIMLIĞIDIR:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Kullanıcı tarafından atanan bir veya daha fazla kimliği etkinleştirebilirsiniz.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Sistem tarafından atanan kimlik

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Sistem ve Kullanıcı tarafından atanan kimlikler

Bir kapsayıcı grubunda, hem sistem tarafından atanan hem de bir veya daha fazla kullanıcı tarafından atanan kimlik sağlayabilirsiniz.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>YAML dosyasını kullanarak yönetilen kimliği etkinleştirme

Bir [YAML dosyası](container-instances-multi-container-yaml.md)kullanılarak dağıtılan bir kapsayıcı grubundaki yönetilen bir kimliği etkinleştirmek için aşağıdaki YAML 'yi ekleyin.
En az bir `apiVersion` belirtin `2018-10-01` .

### <a name="user-assigned-identity"></a>Kullanıcı tarafından atanan kimlik

Kullanıcı tarafından atanan kimlik, formun bir kaynak KIMLIĞIDIR 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Kullanıcı tarafından atanan bir veya daha fazla kimliği etkinleştirebilirsiniz.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Sistem tarafından atanan kimlik

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Sistem ve Kullanıcı tarafından atanan kimlikler

Bir kapsayıcı grubunda, hem sistem tarafından atanan hem de bir veya daha fazla kullanıcı tarafından atanan kimlik sağlayabilirsiniz.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Container Instances ' deki Yönetilen kimlikler hakkında bilgi edindiniz ve şunları yapabilirsiniz:

> [!div class="checklist"]
> * Kapsayıcı grubunda Kullanıcı tarafından atanan veya sistem tarafından atanan bir kimliği etkinleştirme
> * Azure Anahtar Kasası 'na kimlik erişimi verme
> * Çalışan bir kapsayıcıdan anahtar kasasına erişmek için yönetilen kimliği kullanma

* [Azure kaynakları için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/index.yml)hakkında daha fazla bilgi edinin.

* Azure Container Instances 'ten bir anahtar kasasına erişmek için yönetilen kimlik kullanma hakkında bir [Azure go SDK örneğine](https://medium.com/@samkreter/c98911206328) bakın.
