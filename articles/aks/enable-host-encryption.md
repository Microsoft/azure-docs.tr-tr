---
title: Azure Kubernetes Service (AKS) üzerinde ana bilgisayar tabanlı şifrelemeyi etkinleştirme
description: Azure Kubernetes Service (AKS) kümesinde konak tabanlı şifrelemeyi yapılandırma hakkında bilgi edinin
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7eb3215aeb1f7c6508092d18fbebd90f852efe63
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772928"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Azure Kubernetes hizmeti (AKS) üzerinde ana bilgisayar tabanlı şifreleme (Önizleme)

Ana bilgisayar tabanlı şifreleme ile, AKS aracı düğümlerinizin VM konağında depolanan veriler, REST ve depolama hizmetine şifrelenmiş akışlarda şifrelenir. Diğer bir deyişle, geçici diskler platform tarafından yönetilen anahtarlarla birlikte şifrelenir. İşletim sistemi ve veri disklerinin önbelleği, bu disklerde ayarlanan şifreleme türüne bağlı olarak, platform tarafından yönetilen anahtarlarla veya müşteri tarafından yönetilen anahtarlarla birlikte Rest 'de şifrelenir. Varsayılan olarak, AKS kullanırken, işletim sistemi ve veri diskleri, platformda yönetilen anahtarlarla birlikte şifrelenir, yani bu disklere yönelik önbellekler, platform tarafından yönetilen anahtarlarla aynı zamanda varsayılan olarak şifrelenir.  [Azure Kubernetes hizmetindeki Azure diskleriyle kendi anahtarlarınızı (BYOK)](azure-disk-customer-managed-keys.md)izleyerek kendi yönetilen anahtarlarınızı belirtebilirsiniz. Bu disklerin önbelleği daha sonra da bu adımda belirttiğiniz anahtar kullanılarak şifrelenir.


## <a name="before-you-begin"></a>Başlamadan önce

Bu özellik yalnızca küme oluşturma veya düğüm havuzu oluşturma zamanında ayarlanabilir.

> [!NOTE]
> Ana bilgisayar tabanlı şifreleme, Azure yönetilen disklerin sunucu tarafı şifrelemesini ve yalnızca belirli [desteklenen VM boyutlarını][supported-sizes]destekleyen [Azure bölgelerinde][supported-regions] kullanılabilir.

### <a name="prerequisites"></a>Önkoşullar

- `aks-preview`CLI uzantısının v 0.4.73 veya üzeri sürümünün yüklü olduğundan emin olun.
- `EnableEncryptionAtHostPreview`Özellik bayrağının etkin ' in altında olduğundan emin olun `Microsoft.ContainerService` .

Azure Kubernetes hizmet kümeniz için EncryptionAtHost özelliğini kullanmadan önce aboneliğiniz için özelliği etkinleştirmeniz gerekir. Aboneliğiniz için özelliği etkinleştirmek üzere lütfen aşağıdaki adımları izleyin:

1. Aboneliğinizi için özelliği kaydetmek üzere aşağıdaki komutu yürütün

```azurecli-interactive
Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```
2. Özelliği denemeden önce aşağıdaki komutu kullanarak kayıt durumunun kayıtlı olduğundan (birkaç dakika sürer) emin olun.

```azurecli-interactive
Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI uzantısını yükleme

Ana bilgisayar tabanlı şifrelemeyi bir AKS kümesi oluşturmak için en son *aks-Preview* CLI uzantısına ihtiyacınız vardır. [Az Extension Add][az-extension-add] komutunu kullanarak *aks-Preview* Azure CLI uzantısını yükler veya [az Extension Update][az-extension-update] komutunu kullanarak kullanılabilir güncelleştirmeleri denetleyin:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Sınırlamalar

- Yalnızca yeni düğüm havuzlarında etkinleştirilebilir.
- Yalnızca Azure yönetilen disklerinin sunucu tarafı şifrelemesini ve yalnızca belirli [desteklenen VM boyutlarını][supported-sizes]destekleyen [Azure bölgelerinde][supported-regions] etkinleştirilebilir.
- *VM kümesi türü* olarak sanal makine ölçek kümeleri (VMSS) tabanlı bir aks kümesi ve düğüm havuzu gerektirir.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Yeni kümelerde ana bilgisayar tabanlı şifrelemeyi kullan (Önizleme)

Küme oluşturulduğunda küme Aracısı düğümlerini konak tabanlı şifrelemeyi kullanacak şekilde yapılandırın. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Konak tabanlı şifreleme olmadan kümeler oluşturmak istiyorsanız, parametreyi hariç bırakarak bunu yapabilirsiniz `--enable-encryption-at-host` .

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Mevcut kümeler üzerinde ana bilgisayar tabanlı şifrelemeyi kullanma (Önizleme)

Kümenize yeni bir düğüm havuzu ekleyerek, var olan kümelerde konak tabanlı şifrelemeyi etkinleştirebilirsiniz. Parametresini kullanarak ana bilgisayar tabanlı şifrelemeyi kullanmak için yeni bir düğüm havuzu yapılandırın `--enable-encryption-at-host` .

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Konak tabanlı şifreleme özelliği olmadan yeni düğüm havuzları oluşturmak istiyorsanız, parametresini atlayarak bunu yapabilirsiniz `--enable-encryption-at-host` .

## <a name="next-steps"></a>Sonraki adımlar

[AKS kümesi güvenliğine yönelik en iyi uygulamaları][best-practices-security] gözden geçirin [ana bilgisayar tabanlı şifreleme](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)hakkında daha fazla bilgi edinin.


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
