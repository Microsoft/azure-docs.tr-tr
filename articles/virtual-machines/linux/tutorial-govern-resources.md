---
title: Öğretici-Azure CLı ile Azure sanal makinelerini yönetme
description: Bu öğreticide, RBAC, ilkeler, kilitler ve etiketler uygulayarak Azure sanal makinelerini yönetmek üzere Azure CLI kullanmayı öğrenirsiniz
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: gwallace
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: e9475f4226a65ad06d45170e0ab802c6cdb79d8f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034535"
---
# <a name="tutorial-learn-about-linux-virtual-machine-governance-with-azure-cli"></a>Öğretici: Azure CLI ile Linux sanal makine yönetimi hakkında bilgi edinin

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Azure CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.30 veya sonraki bir sürümünü çalıştırmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="understand-scope"></a>Kapsamı anlama

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

Bu öğreticide, işiniz bittiğinde kolayca silebilmeniz için tüm yönetim ayarlarını bir kaynak grubuna uygulayacaksınız.

Şimdi o kaynak grubunu oluşturalım.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

Kaynak grubu şu anda boştur.

## <a name="role-based-access-control"></a>Rol tabanlı erişim denetimi

Kuruluşunuzdaki kullanıcıların bu kaynaklara erişmek için doğru düzeyde erişime sahip olduğundan emin olmak istersiniz. Kullanıcılara sınırsız erişim vermek istemezsiniz ancak işlerini halledebildiklerinden de emin olmanız gerekir. [Rol tabanlı erişim denetimi](../../role-based-access-control/overview.md), bir kapsamdaki belirli eylemleri tamamlamak için izinli olan kullanıcıları yönetmenizi sağlar.

Rol atamaları oluşturmak ve kaldırmak için kullanıcıların `Microsoft.Authorization/roleAssignments/*` erişimi olması gerekmektedir. Bu erişim, Sahip veya Kullanıcı Erişimi Yöneticisi rolleriyle verilir.

Sanal makine çözümlerini yönetmek için yaygın olarak gereken erişimi sağlayan üç adet kaynağa özgü rol vardır:

* [Sanal Makine Katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Ağ Katılımcısı](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Depolama Hesabı Katılımcısı](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Kullanıcılara rolleri tek tek atamak yerine, benzer eylemlerde bulunması gereken kullanıcılar için bir Azure Active Directory grubu kullanmak genellikle daha kolaydır. Ardından, bu grubu uygun role atayabilirsiniz. Bu makalede sanal makineyi yönetmek için var olan bir grubu kullanın veya portalı kullanarak [bir Azure Active Directory grubu oluşturun](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Yeni grup oluşturduktan veya var olan bir grup belirledikten sonra [az role assignment create](https://docs.microsoft.com/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) komutunu kullanarak yeni Azure Active Directory grubunu kaynak grubu için Sanal Makine Katılımcısı rolüne atayabilirsiniz.

```azurecli-interactive
adgroupId=$(az ad group show --group <your-group-name> --query objectId --output tsv)

az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

**Asıl \<guıd > dizinde mevcut olmadığı**belirten bir hata alırsanız, yeni grup Azure Active Directory genelinde yayılmaz. Komutu tekrar çalıştırmayı deneyin.

Genellikle, kullanıcıların dağıtılmış kaynakları yönetmek için atandığından emin olmak üzere *Ağ Katılımcısı* ve *Depolama Hesabı Katılımcısı* için işlemi yinelemeniz gerekir. Bu makalede, söz konusu adımları atlayabilirsiniz.

## <a name="azure-policy"></a>Azure İlkesi

[Azure İlkesi](../../governance/policy/overview.md) abonelikteki tüm kaynakların şirket standartlarına uyduğundan emin olmanıza yardımcı olur. Aboneliğinizde zaten birkaç ilke tanımı mevcuttur. Kullanılabilir ilke tanımlarını görmek için [az policy definition list](https://docs.microsoft.com/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-list) komutunu kullanın:

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Mevcut ilke tanımlarını göreceksiniz. İlke türü **Yerleşik** veya **Özel**’dir. Atamak istediğiniz bir koşulu açıklayan ilke türlerinin tanımlarına bakın. Bu makalede, aşağıdakileri gerçekleştiren ilkeler atayacaksınız:

* Tüm kaynaklar için konumları sınırlama.
* Sanal makineler için SKU'ları sınırlama.
* Yönetilen diskler kullanmayan sanal makineleri denetleme.

Aşağıdaki örnekte, görünen ada göre üç ilke tanımı alırsınız. Bu tanımları kaynak grubuna atamak için [az policy assignment create](https://docs.microsoft.com/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) komutunu kullanın. Bazı ilkeler için, izin verilen değerleri belirtmek üzere parametre değerleri sağlayın.

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

Önceki örnekte ilke parametrelerini bildiğiniz varsayılmaktadır. Parametreleri görüntülemeniz gerekiyorsa şunu kullanın:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>Sanal makineyi dağıtma

Rol ve ilkeler atadıktan sonra çözümünüzü dağıtmaya hazırsınız. Varsayılan boyut, izin verilen SKU’larınızdan biri olan Standard_DS1_v2’dir. Varsayılan konumda mevcut değilse, komut SSH anahtarlarını oluşturur.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Dağıtımınız tamamlandıktan sonra çözüme daha fazla yönetim ayarı uygulayabilirsiniz.

## <a name="lock-resources"></a>Kaynakları kilitleme

[Kaynak kilitleri](../../azure-resource-manager/resource-group-lock-resources.md), kuruluşunuzdaki kullanıcıların kritik kaynakları yanlışlıkla silmesini veya değiştirmesini önler. Rol tabanlı erişim denetiminin aksine, kaynak kilitleri tüm kullanıcılar ve roller için bir kısıtlama uygular. Kilit düzeyini *CanNotDelete* veya *ReadOnly* olarak ayarlayabilirsiniz.

Yönetim kilitlerini oluşturmak veya silmek için `Microsoft.Authorization/locks/*` eylemlerine erişiminiz olması gerekmektedir. Yerleşik rollerden yalnızca **Sahip** ve **Kullanııcı Erişiimi Yöneticisi** bu eylemleri kullanabilir.

Sanal makineyi ve ağ güvenlik grubunu kilitlemek için [az lock create](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest#az-resource-lock-create) komutunu kullanın:

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

Kilitleri test etmek için aşağıdaki komutu çalıştırmayı deneyin:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Silme işleminin bir kilit nedeniyle tamamlanamadığını belirten bir hata görürsünüz. Kaynak grubu yalnızca kilitleri spesifik olarak kaldırırsanız silinebilir. Bu adım [Kaynakları temizle](#clean-up-resources) bölümünde gösterilmektedir.

## <a name="tag-resources"></a>Kaynakları etiketleme

Azure kaynaklarınızı mantıksal olarak kategorilere ayırmak için [etiketler](../../azure-resource-manager/resource-group-using-tags.md) uygulayabilirsiniz. Her etiket bir ad ve değerden oluşur. Örneğin, "Ortam" adını ve "Üretim" değerini üretimdeki tüm kaynaklara uygulayabilirsiniz.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Etiketleri bir sanal makineye uygulamak için [az resource tag](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list) komutunu kullanın. Kaynaktaki mevcut tüm etiketler korunmaz.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Kaynakları etikete göre bulma

Kaynakları etiket adı ve değeriyle bulmak için [az resource list](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list) komutunu kullanın:

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

Tüm sanal makineleri bir etiket değeriyle durdurmak gibi yönetim görevleri için döndürülen değerleri kullanabilirsiniz.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Maliyetleri etiket değerlerine göre görüntüleme

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kilit kaldırılana kadar kilitli ağ güvenlik grubu silinemez. Kilidi kaldırmak için kilitlerin kimliklerini alın ve bunları [az lock delete](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest#az-resource-lock-delete) komutuna ekleyin:

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

Artık gerekli değilse, [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) komutunu kullanarak kaynak grubunu, VM’yi ve tüm ilgili kaynakları kaldırabilirsiniz. SSH oturumundan sanal makinenize çıkış yapın, ardından kaynakları aşağıda belirtildiği gibi silin:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, özel bir VM görüntüsü oluşturdunuz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Kullanıcıları bir role atama
> * Standartları uygulamaya zorlayan ilkeler uygulama
> * Kilitlerle kritik kaynakları koruma
> * Fatura ve yönetim için kaynakları etiketleme

Bir sanal makinede yapılan değişiklikleri belirleme ve paket güncelleştirmelerini yönetme hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Sanal makineleri yönetme](tutorial-config-management.md)

