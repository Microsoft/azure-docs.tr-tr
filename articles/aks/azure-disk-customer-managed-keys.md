---
title: Azure Kubernetes Service (AKS) ' de Azure disklerini şifrelemek için müşteri tarafından yönetilen bir anahtar kullanma
description: AKS işletim sistemini ve veri disklerini şifrelemek için kendi anahtarlarınızı getirin (BYOK).
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: c5c555d7eb5142f5f41f65b24f754c65450a2713
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776200"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içindeki Azure diskleriyle kendi anahtarlarınızı (BYOK) getirin

Azure depolama, bekleyen bir depolama hesabındaki tüm verileri şifreler. Varsayılan olarak, veriler Microsoft tarafından yönetilen anahtarlarla şifrelenir. Şifreleme anahtarları üzerinde ek denetim için, AKS kümeleriniz için hem işletim sistemi hem de veri diskleri için bekleyen şifreleme için kullanılmak üzere müşteri tarafından yönetilen anahtarlar sağlayabilirsiniz. [Linux][customer-managed-keys-linux] ve [Windows][customer-managed-keys-windows]'ta müşteri tarafından yönetilen anahtarlar hakkında daha fazla bilgi edinin.

## <a name="limitations"></a>Sınırlamalar
* Veri diski şifreleme desteği, Kubernetes sürüm 1,17 ve üstünü çalıştıran AKS kümeleriyle sınırlıdır.
* Müşteri tarafından yönetilen anahtarlarla işletim sistemi ve veri diski şifrelemesi, yalnızca bir AKS kümesi oluşturulurken etkinleştirilebilir.

## <a name="prerequisites"></a>Önkoşullar
* Yönetilen diskleri şifrelemek için Key Vault kullanırken *Azure Key Vault* için geçici silme ve Temizleme korumasını etkinleştirmeniz gerekir.
* Azure CLı sürüm 2.11.1 veya sonraki bir sürümü gereklidir.

## <a name="create-an-azure-key-vault-instance"></a>Azure Key Vault örneği oluşturma

Anahtarlarınızı depolamak için bir Azure Key Vault örneği kullanın.  Azure portal, [müşteri tarafından yönetilen anahtarları Azure Key Vault Ile yapılandırmak][byok-azure-portal] için isteğe bağlı olarak kullanabilirsiniz

Yeni bir *kaynak grubu* oluşturun ve ardından yeni bir *Key Vault* örneği oluşturun ve geçici silme ve Temizleme korumasını etkinleştirin.  Her komut için aynı bölgeyi ve kaynak grubu adlarını kullandığınızdan emin olun.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>DiskEncryptionSet örneği oluşturma

*Mykeyvaultname* değerini anahtar kasanızın adıyla değiştirin.  Ayrıca, aşağıdaki adımları tamamlayabilmeniz için Azure Key Vault depolanan bir *anahtara* ihtiyacınız olacaktır.  Mevcut anahtarınızı önceki adımlarda oluşturduğunuz Key Vault depolayın veya [Yeni bir anahtar oluşturun][key-vault-generate] ve aşağıdaki *mykeyname* değerini anahtarınızın adıyla değiştirin.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query "[id]" -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query "[key.kid]" -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>DiskEncryptionSet erişimini Anahtar Kasası 'na verme

Önceki adımlarda oluşturduğunuz DiskEncryptionSet ve Resource gruplarını kullanın ve Azure Key Vault DiskEncryptionSet kaynağına erişimi verin.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query "[identity.principalId]" -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Yeni bir AKS kümesi oluşturma ve işletim sistemi diskini şifreleme

Yeni bir **kaynak grubu** ve aks kümesi oluşturun ve ardından anahtarınızı kullanarak işletim sistemi diskini şifreleyin. Müşteri tarafından yönetilen anahtarlar yalnızca 1,17 'den büyük Kubernetes sürümlerinde desteklenir. 

> [!IMPORTANT]
> Aks kümeniz için yeni bir eklendi grubu oluşturduğunuzdan emin olun

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query "[id]" -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

Yukarıda oluşturulan kümeye yeni düğüm havuzları eklendiğinde, oluşturma sırasında belirtilen müşteri tarafından yönetilen anahtar, işletim sistemi diskini şifrelemek için kullanılır.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>AKS kümesi veri diskinizi şifreleyin (isteğe bağlı)
V 1.17.2 veri diski için anahtar sağlanmazsa ve ayrıca AKS veri disklerini diğer anahtarlarla şifreleyebiliyorsanız, işletim sistemi disk şifreleme anahtarı veri diskini şifrelemek için kullanılacaktır.

> [!IMPORTANT]
> Uygun AKS kimlik bilgilerine sahip olduğunuzdan emin olun. Yönetilen kimliğin, diskencryptionset 'in dağıtıldığı kaynak grubuna katkıda bulunan erişiminin olması gerekir. Aksi takdirde, yönetilen kimliğin izinlere sahip olmadığından emin olarak bir hata alırsınız.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Aşağıdaki bilgileri içeren **bYok-Azure-disk. YAML** adlı bir dosya oluşturun.  Myazuyeniden gönderme Scriptionıd, myResourceGroup ve myDiskEncrptionSetName değerlerini değerlerinizle değiştirin ve YAML 'yi uygulayın.  DiskEncryptionSet dosyanızın dağıtıldığı kaynak grubunu kullandığınızdan emin olun.  Azure Cloud Shell kullanırsanız, bu dosya bir sanal veya fiziksel sistemde çalışırken VI veya nano kullanılarak oluşturulabilir:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Ardından, bu dağıtımı AKS kümenizde çalıştırın:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>Sonraki adımlar

[AKS küme güvenliği için en iyi uygulamaları][best-practices-security] gözden geçirme

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions