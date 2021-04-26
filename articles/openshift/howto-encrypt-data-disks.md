---
title: Azure Red Hat OpenShift (ARO) üzerinde müşteri tarafından yönetilen bir anahtarla (CMK) kalıcı birim taleplerini şifreleyin
description: Kendi anahtarını getir (BYOK)/müşteri tarafından yönetilen anahtar (CMK) Azure Red Hat OpenShift için dağıtım yönergeleri
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: şifreleme, bYok, Aro, CMK, OpenShift, Red Hat
ms.openlocfilehash: f6c80bab6f821dc7c85352bf57ebe255ae712d43
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783530"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Azure Red Hat OpenShift (ARO) ile kalıcı birim taleplerini, müşteri tarafından yönetilen anahtar (CMK) ile şifreleme (Önizleme)

Azure depolama, bulutta kalıcı olduğunda verilerinizi otomatik olarak [şifrelemek](../storage/common/storage-service-encryption.md) için sunucu tarafı ŞIFRELEME (SSE) kullanır. Varsayılan olarak, veriler Microsoft Platformu tarafından yönetilen anahtarlarla şifrelenir. Şifreleme anahtarları üzerinde ek denetim için, Azure Red Hat OpenShift kümelerinizdeki verileri şifrelemek üzere kendi müşteri tarafından yönetilen anahtarlarınızı sağlayabilirsiniz.

> [!NOTE]
> Bu aşamada, destek yalnızca, müşteri tarafından yönetilen anahtarlarla ARO kalıcı birimlerini şifrelemek için mevcuttur. Bu özellik şu anda ana veya çalışan düğümü işletim sistemi diskleri için kullanılamaz.

> [!IMPORTANT]
> ARO Önizleme özellikleri self servis, kabul etme esasına göre sunulmaktadır. Önizlemeler "olduğu gibi" ve "kullanılabilir olarak" verilmiştir ve hizmet düzeyi anlaşmalarından ve sınırlı garantiden çıkarılır. ARO önizlemeleri, müşteri desteği tarafından kısmen bir en iyi performans kapsamında ele alınmıştır. Bu nedenle, bu özellikler üretim kullanımı için tasarlanmamıştır.

## <a name="before-you-begin"></a>Başlamadan önce
Bu makalede şu şekilde varsayılmaktadır:

* OpenShift sürüm 4,4 (veya üzeri) üzerinde önceden var olan bir ARO kümeniz var.

* **OC** OpenShift komut satırı aracınız, Base64 (coreutils parçası) ve **az** Azure CLI yüklü.

* Bir genel küme Yönetici kullanıcısı (kubeadmin) olarak **OC** kullanarak Aro kümenizde oturum açarsınız.

* Azure CLı 'da, \ ' **i kullanarak, Aro kümesiyle aynı** abonelikte "katkıda bulunan" erişimine izin vermek için yetkilendirilmiş bir hesapla oturum açarsınız.

## <a name="limitations"></a>Sınırlamalar

* Müşteri tarafından yönetilen anahtar şifrelemesi için kullanılabilirlik bölgeye özeldir. Belirli bir Azure bölgesinin durumunu görmek için [Azure bölgelerini][supported-regions]inceleyin.
* Ultra diskler kullanmak istiyorsanız, başlamadan önce bunu aboneliğinizde etkinleştirmeniz gerekir.

## <a name="declare-cluster--encryption-variables"></a>Küme & şifreleme değişkenlerini bildirme
Aşağıdaki değişkenleri, müşteri tarafından yönetilen şifreleme anahtarlarını etkinleştirmek istediğiniz ARO kümeniz olduğunuz için uygun olabilecek her türlü şekilde yapılandırmanız gerekir:
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>Abonelik KIMLIĞINIZI alma
Azure abonelik KIMLIĞINIZ, CMK yapılandırmasında birden çok kez kullanılır. Edinin ve bir değişken olarak depolayın:
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Azure Key Vault örneği oluşturma
Anahtarlarınızı depolamak için bir Azure Key Vault örneği kullanılmalıdır. Temizleme koruması etkinken yeni bir Key Vault oluşturun. Ardından, kendi özel anahtarınızı depolamak için kasa içinde yeni bir anahtar oluşturun:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table

# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Azure disk şifreleme kümesi oluşturma

Azure disk şifrelemesi kümesi, ARO 'daki diskler için başvuru noktası olarak kullanılır. Bu, önceki adımda oluşturduğumuz Azure Key Vault bağlanır ve müşteri tarafından yönetilen anahtarları bu konumdan çeker.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Disk şifrelemeyi Key Vault için erişim izni ver
Önceki adımlarda oluşturduğumuz disk şifreleme kümesini kullanın ve disk şifrelemeyi Azure Key Vault için erişim izni verin:

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Rol atamaları için gereken diğer kimlikleri alma
ARO kümesinin, ARO kümesindeki kalıcı birim taleplerini (PVC) şifrelemek için disk şifreleme kümesini kullanmasına izin vermemiz gerekiyor. Bunu yapmak için yeni bir Yönetilen Hizmet Kimliği (MSI) oluşturacağız. Ayrıca, ARO MSI ve disk şifreleme kümesi için de diğer izinleri ayarlayacağız.

```azurecli-interactive
# First, get the Azure Application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $buildRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity Azure Application ID.
aroMSIAppId="$(az identity show -n $msiName -g $buildRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
buildRGResourceId="$(az group show -n $buildRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>CMK şifrelemesi için gereken diğer rol atamalarını uygulama
Önceki adımda elde edilen değişkenleri kullanarak gerekli rol atamalarını uygulayın:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>Şifrelenmiş Premium & Ultra diskler için k8s depolama sınıfı oluşturma
Premium_LRS ve UltraSSD_LRS diskleri için CMK için kullanılacak depolama sınıflarını oluşturun:

```azurecli-interactive
# Premium Disks
cat > managed-premium-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```

Daha sonra, depolama sınıfı yapılandırmasını uygulamak için bu dağıtımı ARO kümenizde çalıştırın:

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>Müşteri tarafından yönetilen anahtarlarla şifrelemeyi test etme (isteğe bağlı)
Kümenizin, PVC şifrelemesi için müşteri tarafından yönetilen bir anahtar kullanıp kullanmediğini denetlemek için yeni depolama sınıfını kullanarak kalıcı bir birim talebi oluşturacağız. Aşağıdaki kod parçacığı bir pod oluşturur ve Premium diskler kullanılarak kalıcı bir birim talebi bağlar.
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-cmk-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-cmk
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-cmk-encryption
spec:
  containers:
  - name: mypod-with-cmk-encryption
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: mypod-with-cmk-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file-optional"></a>Test Pod yapılandırma dosyasını Uygula (isteğe bağlı)
Test Pod yapılandırmasını uygulamak ve yeni kalıcı birim talebinin UID 'sini döndürmek için aşağıdaki komutları yürütün. UID, diskin CMK kullanılarak şifrelendiğini doğrulamak için kullanılacaktır.
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> Bazen Azure Active Directory içindeki rol atamaları uygulanırken küçük bir gecikme olabilir. Bu yönergelerin nasıl yürütüldüğü hızına bağlı olarak, "tam Azure disk adını belirleme" komutuna yönelik komut başarısız olabilir. Bu durumda, diskin başarıyla sağlandığından emin olmak için, OC ' ın **-CMK-Encryption-PVC** ' yi tanımlayan çıktıyı gözden geçirin. Rol atama yayılması tamamlanmadıysa, Pod & PVC YAML 'yi *silip* yeniden *uygulamanız* gerekebilir.

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>PVC diskinin "EncryptionAtRestWithCustomerKey" ile yapılandırıldığını doğrulama (Isteğe bağlı)
Pod, CMK depolama sınıfına başvuran kalıcı bir birim talebi oluşturmalı. Aşağıdaki komutun çalıştırılması, PVC 'nin beklendiği gibi dağıtıldığını doğrular:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ../aks/operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions