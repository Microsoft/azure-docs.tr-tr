---
title: Azure CLı kullanarak Azure Image Builder hizmet izinlerini yapılandırma
description: Azure CLı kullanarak izinler ve ayrıcalıklar dahil olmak üzere Azure VM Image Builder hizmeti gereksinimlerini yapılandırın
author: cynthn
ms.author: danis
ms.date: 04/02/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: eb4fe102407bf519c9253ac7da39178ad8cacb0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607543"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>Azure CLı kullanarak Azure Image Builder hizmet izinlerini yapılandırma

(AıB) için kaydolmanız durumunda bu, AıB hizmetine bir hazırlama kaynak grubu (IT_ *) oluşturma, yönetme ve silme izni verir ve bu, görüntü derlemesi için gerekli olan kaynak ekleme haklarına sahip olur. Bu işlem, başarılı bir kayıt sırasında aboneliğinizde kullanılabilir hale getirilen bir AıB hizmet sorumlusu adı (SPN) tarafından yapılır.

Azure VM Image Builder 'ın görüntüleri yönetilen görüntülere veya paylaşılan bir görüntü galerisine dağıtmasını sağlamak için, görüntüleri okuma ve yazma izinlerine sahip Azure Kullanıcı tarafından atanan bir kimlik oluşturmanız gerekir. Azure depolama 'ya erişiyorsanız, bu durumda özel veya ortak kapsayıcıları okumak için izinler gerekir.

Bir görüntü oluşturmadan önce izinleri ve ayrıcalıkları ayarlamalısınız. Aşağıdaki bölümlerde, Azure CLı kullanarak olası senaryoların nasıl yapılandırılacağı anlatılmaktadır.

> [!IMPORTANT]
> Azure görüntü Oluşturucu Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>Özellikleri kaydetme

İlk olarak, Azure Image Builder hizmetine kaydolmanız gerekir. Kayıt, hizmet iznini hazırlama kaynak grubu oluşturma, yönetme ve silme izni verir. Ayrıca hizmet, görüntü derlemesi için gerekli olan grubu kaynak ekleme haklarına sahiptir.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Azure Kullanıcı tarafından atanan yönetilen kimlik oluşturma

Azure Image Builder, [Azure Kullanıcı tarafından atanan bir yönetilen kimlik](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)oluşturmanızı gerektirir. Azure Image Builder, görüntüleri okumak, görüntüleri yazmak ve Azure depolama hesaplarına erişmek için Kullanıcı tarafından atanan yönetilen kimliği kullanır. Aboneliğinizde belirli işlemleri yapmak için kimlik iznini verirsiniz.

> [!NOTE]
> Daha önce Azure Image Builder, görüntü kaynak gruplarına izin vermek için Azure görüntü Oluşturucu hizmet asıl adı 'nı (SPN) kullandı. SPN 'nin kullanılması kullanım dışı olacaktır. Bunun yerine Kullanıcı tarafından atanan bir yönetilen kimlik kullanın.

Aşağıdaki örnekte, Azure Kullanıcı tarafından atanan bir yönetilen kimliğin nasıl oluşturulacağı gösterilmektedir. Değişkenlerinizi ayarlamak için yer tutucu ayarlarını değiştirin.

| Ayar | Açıklama |
|---------|-------------|
| \<Resource group\> | Kullanıcı tarafından atanan yönetilen kimliğin oluşturulacağı kaynak grubu. |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
```

Azure Kullanıcı tarafından atanan kimlikler hakkında daha fazla bilgi için bkz. kimlik oluşturma hakkında [Azure Kullanıcı tarafından atanan yönetilen kimlik](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) belgeleri.

## <a name="allow-image-builder-to-distribute-images"></a>Görüntü oluşturucunun görüntüleri dağıtmasını sağlar

Görüntüleri (yönetilen görüntüler/paylaşılan görüntü Galerisi) dağıtmak için Azure Image Builder 'da, Azure görüntü Oluşturucu hizmeti 'nin bu kaynak gruplarına görüntüleri eklemesine izin verilmelidir. Gerekli izinleri vermek için, Kullanıcı tarafından atanan bir yönetilen kimlik oluşturmanız ve görüntünün oluşturulduğu kaynak grubunda bu haklara izin vermeniz gerekir. Azure görüntü oluşturucunun, abonelikteki diğer kaynak gruplarındaki kaynaklara erişme **izni yok.** Derlemelerinizin başarısız olmasına engel olmak için erişime izin vermek üzere açık eylemler gerçekleştirmeniz gerekir.

Görüntü dağıtmak için, kaynak grubunda Kullanıcı tarafından atanan yönetilen kimliğe katkıda bulunan haklara izin vermeniz gerekmez. Ancak, Kullanıcı tarafından atanan yönetilen kimliğin `Actions` dağıtım kaynak grubunda aşağıdaki Azure izinleri olması gerekir:

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Paylaşılan bir görüntü galerisine dağıtıyorsanız şunları da yapmanız gerekir:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>Mevcut görüntüleri özelleştirme izni

Kaynak özel görüntülerden (yönetilen görüntüler/paylaşılan görüntü Galerisi) görüntü oluşturmak için Azure Image Builder 'ın, bu kaynak grupları içindeki görüntüleri okumasına izin verilmelidir. Gerekli izinleri vermek için, Kullanıcı tarafından atanan bir yönetilen kimlik oluşturmanız ve görüntünün bulunduğu kaynak grubunda bu haklara izin vermeniz gerekir.

Var olan bir özel görüntüden oluştur:

```Actions
Microsoft.Compute/galleries/read
```

Var olan bir paylaşılan görüntü Galerisi sürümünden oluştur:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>Sanal ağlarınızdaki görüntüleri özelleştirme izni

Azure Image Builder, aboneliğinizde mevcut bir VNET 'i dağıtma ve kullanma özelliğine sahiptir ve bu sayede, bağlı kaynaklara özelleştirmelerin erişmesine izin verir.

Mevcut bir VNET 'e bir VM dağıtmak için, kaynak grubunda Kullanıcı tarafından atanan yönetilen kimliğe katkıda bulunan haklara izin vermeniz gerekmez. Ancak, Kullanıcı tarafından atanan yönetilen kimliğin `Actions` VNET kaynak grubunda aşağıdaki Azure izinlerine ihtiyacı vardır:

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Azure rol tanımı oluşturma

Aşağıdaki örneklerde, önceki bölümlerde açıklanan eylemlerden bir Azure rol tanımı oluşturulur. Örnekler, kaynak grubu düzeyinde uygulanır. Örneklerin gereksinimleriniz için yeterince ayrıntılı olup olmadığını değerlendirin ve test edin. Senaryonuz için, belirli bir paylaşılan görüntü galerisine iyileştirmeniz gerekebilir.

Görüntü eylemleri okuma ve yazma işlemlerine izin verir. Ortamınız için nelerin uygun olduğuna karar verin. Örneğin, Azure Image Builder 'ın, *örnek-RG-1* kaynak grubundaki görüntüleri okumasına izin veren bir rol oluşturun ve kaynak grubuna görüntüleri yazın *-RG-2*.

### <a name="custom-image-azure-role-example"></a>Özel görüntü Azure rolü örneği

Aşağıdaki örnek, bir kaynak özel görüntüsünü kullanmak ve dağıtmak için bir Azure rolü oluşturur. Daha sonra özel rolü Azure Image Builder için Kullanıcı tarafından atanan yönetilen kimliğe verirsiniz.

Örnekteki değerlerin değiştirilmesini basitleştirmek için, önce aşağıdaki değişkenleri ayarlayın. Değişkenlerinizi ayarlamak için yer tutucu ayarlarını değiştirin.

| Ayar | Açıklama |
|---------|-------------|
| \<Subscription ID\> | Azure abonelik KIMLIĞINIZ |
| \<Resource group\> | Özel görüntü için kaynak grubu |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>Mevcut VNET Azure rolü örneği

Aşağıdaki örnek, mevcut bir VNET görüntüsünü kullanmak ve dağıtmak için bir Azure rolü oluşturur. Daha sonra özel rolü Azure Image Builder için Kullanıcı tarafından atanan yönetilen kimliğe verirsiniz.

Örnekteki değerlerin değiştirilmesini basitleştirmek için, önce aşağıdaki değişkenleri ayarlayın. Değişkenlerinizi ayarlamak için yer tutucu ayarlarını değiştirin.

| Ayar | Açıklama |
|---------|-------------|
| \<Subscription ID\> | Azure abonelik KIMLIĞINIZ |
| \<Resource group\> | VNET kaynak grubu |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>Azure depolama erişimi için yönetilen kimlik kullanma

Azure depolama ile kimlik doğrulaması yapmak ve özel kapsayıcılar kullanmak istiyorsanız, Azure görüntü Oluşturucu bir Azure 'un Kullanıcı tarafından atanan yönetilen kimliğe ihtiyacı vardır. Azure görüntü Oluşturucu Kimliği, Azure depolama ile kimlik doğrulamak için kullanır.

> [!NOTE]
> Azure Image Builder yalnızca görüntü şablonu gönderme zamanında kimliği kullanır. Derleme VM 'sinin görüntü derlemesi sırasında kimliğe erişimi yok.

Kullanıcı tarafından atanan yönetilen kimliği oluşturmak için Azure CLı 'yi kullanın.

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

Görüntü Oluşturucu şablonunda, Kullanıcı tarafından atanan yönetilen kimliği sağlamanız gerekir.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

Aşağıdaki yer tutucu ayarlarını değiştirin:

| Ayar | Açıklama |
|---------|-------------|
| \<Region\> | Şablon Bölgesi |
| \<Resource group\> | Kaynak grubu |
| \<Storage account container\> | Depolama hesabı kapsayıcı adı |
| \<Subscription ID\> | Azure aboneliği |

Kullanıcı tarafından atanan yönetilen kimlik kullanarak daha fazla bilgi edinmek için Azure [User-Assigned yönetilen kimliği kullanacak özel görüntü oluşturma](./image-builder-user-assigned-identity.md)' ya bakın. Hızlı başlangıç, bir depolama hesabına erişmek için Kullanıcı tarafından atanan yönetilen kimliğin nasıl oluşturulacağını ve yapılandırılacağını gösterir.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Azure Image Builder 'a genel bakış](../image-builder-overview.md).