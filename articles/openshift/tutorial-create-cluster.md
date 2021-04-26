---
title: Öğretici-Azure Red Hat OpenShift 4 kümesi oluşturma
description: Azure CLı kullanarak Microsoft Azure Red Hat OpenShift kümesi oluşturmayı öğrenin
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 10/26/2020
ms.openlocfilehash: dda4fc6a80bbe07977f8d2a5ffcbea895a4e1fe6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771848"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Öğretici: Azure Red Hat OpenShift 4 kümesi oluşturma

Bu öğreticide, üç bölümden biri olmak üzere, ortamınızı OpenShift 4 çalıştıran bir Azure Red Hat Openshıft kümesi oluşturacak ve bir küme oluşturacak şekilde hazırlarsınız. Şunları öğrenirsiniz:
> [!div class="checklist"]
> * Önkoşulları ayarlayın 
> * Gerekli sanal ağı ve alt ağları oluşturma
> * Küme dağıtma

## <a name="before-you-begin"></a>Başlamadan önce

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLı sürüm 2.6.0 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

Azure Red Hat OpenShift, bir OpenShift kümesi oluşturmak ve çalıştırmak için en az 40 çekirdek gerektirir. Yeni bir Azure aboneliği için varsayılan Azure Kaynak kotası bu gereksinimi karşılamıyor. Kaynak sınırınıza bir artış istemek için bkz. [Standart kota: VM serisine göre limitleri artırma](../azure-portal/supportability/per-vm-quota-requests.md).

ARO çekme gizli anahtarı, ARO için RH OpenShift lisansının maliyetini değiştirmez.

### <a name="verify-your-permissions"></a>İzinlerinizi doğrulama

Bu öğreticide, kümenin sanal ağını içeren bir kaynak grubu oluşturacaksınız. Katkıda bulunan ve Kullanıcı erişimi yönetici izinlerinizin veya sahip izninizin olması gerekir; doğrudan sanal ağ üzerinde veya onu içeren kaynak grubunda ya da abonelikte sahip olmalısınız.

Ayrıca, küme için sizin adınıza bir uygulama ve hizmet sorumlusu oluşturmak üzere araç için yeterli sayıda Azure Active Directory izninizin olması gerekir.

### <a name="register-the-resource-providers"></a>Kaynak sağlayıcılarını kaydetme

1. Birden çok Azure aboneliğiniz varsa ilgili abonelik KIMLIĞINI belirtin:

    ```azurecli-interactive
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. `Microsoft.RedHatOpenShift`Kaynak sağlayıcısını Kaydet:

    ```azurecli-interactive
    az provider register -n Microsoft.RedHatOpenShift --wait
    ```
    
1. `Microsoft.Compute`Kaynak sağlayıcısını Kaydet:

    ```azurecli-interactive
    az provider register -n Microsoft.Compute --wait
    ```
    
1. `Microsoft.Storage`Kaynak sağlayıcısını Kaydet:

    ```azurecli-interactive
    az provider register -n Microsoft.Storage --wait
    ```
    
1. `Microsoft.Authorization`Kaynak sağlayıcısını Kaydet:

    ```azurecli-interactive
    az provider register -n Microsoft.Authorization --wait
    ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Red hat çekme gizli anahtarı alma (isteğe bağlı)

Red hat çekme gizli dizisi, kümenizin ek içerikle birlikte Red Hat kapsayıcısı kayıt defterlerine erişmesine olanak sağlar. Bu adım isteğe bağlıdır, ancak önerilir.

1. [Red Hat Openshıft Küme Yöneticisi portalınıza gidin](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) ve oturum açın.

   Red Hat hesabınızda oturum açmanız veya iş e-postanızı kullanarak yeni bir Red Hat hesabı oluşturmanız ve hüküm ve koşulları kabul etmeniz gerekir.

1. **Gizli anahtar yükle** ' ye tıklayın ve Aro kümenizde kullanılacak bir çekme gizli anahtarı indirin.

    Kaydedilen `pull-secret.txt` dosyayı güvenli bir yerde saklayın. Red hat veya sertifikalı iş ortakları için örnekler veya işleçler içeren bir küme oluşturmanız gerekiyorsa dosya her küme oluşturmada kullanılacaktır.

    `az aro create`Komutunu çalıştırırken, parametresini kullanarak çekme gizli dizinizi başvurabilirsiniz `--pull-secret @pull-secret.txt` . `az aro create`Dosyanızı depoladığınız dizinden yürütün `pull-secret.txt` . Aksi takdirde, `@pull-secret.txt` ile değiştirin `@/path/to/my/pull-secret.txt` .

    Çekme sırlarınızı kopyalıyorsunuz veya başka betiklerin içine başvuruyorsa, çekme gizli anahtarı geçerli bir JSON dizesi olarak biçimlendirilmelidir.

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>Kümeniz için özel bir etki alanı hazırlama (isteğe bağlı)

`az aro create`Komutunu çalıştırırken, parametresini kullanarak kümeniz için özel bir etki alanı belirtebilirsiniz `--domain foo.example.com` .

Kümeniz için özel bir etki alanı sağlarsanız aşağıdaki noktaları dikkate alın:

* Kümenizi oluşturduktan sonra, DNS sunucunuzda belirtilen 2 DNS A kayıt oluşturmanız gerekir `--domain` :
    * **API** -API sunucusu IP adresine işaret eder
    * **\* . Apps** -giriş IP adresine işaret ediyor
    * Küme oluşturulduktan sonra aşağıdaki komutu yürüterek bu değerleri alın: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'` .

* OpenShift konsolu, `https://console-openshift-console.apps.example.com` yerleşik etki alanı yerine, gibi BIR URL 'de kullanılabilir olacaktır `https://console-openshift-console.apps.<random>.<location>.aroapp.io` .

* Varsayılan olarak, Openshıft özel etki alanlarında oluşturulan tüm yollar için otomatik olarak imzalanan sertifikalar kullanır `*.apps.example.com` .  Kümeye bağlandıktan sonra özel DNS kullanmayı seçerseniz, giriş [denetleyiciniz için özel bır CA](https://docs.openshift.com/container-platform/4.6/security/certificates/replacing-default-ingress-certificate.html) ve [API sunucunuz IÇIN özel bir CA](https://docs.openshift.com/container-platform/4.6/security/certificates/api-server.html)yapılandırmak üzere OpenShift belgelerini izlemeniz gerekir.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>İki boş alt ağ içeren bir sanal ağ oluşturun

Ardından, iki boş alt ağ içeren bir sanal ağ oluşturacaksınız. Gereksinimlerinizi karşılayan mevcut bir sanal ağınız varsa, bu adımı atlayabilirsiniz.

1. **Komut yürütmek istediğiniz kabuk ortamında aşağıdaki değişkenleri ayarlayın `az` .**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

2. **Bir kaynak grubu oluşturun.**

   Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği mantıksal bir gruptur. Bir kaynak grubu oluştururken konum belirtmeniz istenir. Bu konum, kaynak grubu meta verilerinin depolandığı ve kaynak oluşturma sırasında başka bir bölge belirtmezseniz kaynaklarınızın Azure 'da çalıştığı yerdir. [Az Group Create](/cli/azure/group#az_group_create) komutunu kullanarak bir kaynak grubu oluşturun.
    
   > [!NOTE] 
   > Azure Red Hat OpenShift, bir Azure Kaynak grubunun oluşturulabildiği tüm bölgelerde kullanılamaz. Azure Red Hat OpenShift 'in desteklendiği konum hakkında bilgi için bkz. [kullanılabilir bölgeler](https://azure.microsoft.com/en-gb/global-infrastructure/services/?products=openshift) .

   ```azurecli-interactive
   az group create \
     --name $RESOURCEGROUP \
     --location $LOCATION
   ```

   Aşağıdaki örnek çıktıda başarıyla oluşturulan kaynak grubu gösterilmektedir:

   ```json
   {
     "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
     "location": "eastus",
     "name": "aro-rg",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "type": "Microsoft.Resources/resourceGroups"
   }
   ```

2. **Bir sanal ağ oluşturun.**

   OpenShift 4 çalıştıran Azure Red Hat OpenShift kümeleri, ana ve çalışan düğümleri için iki boş alt ağa sahip bir sanal ağ gerektirir. Bunun için yeni bir sanal ağ oluşturabilir ya da var olan bir sanal ağı kullanabilirsiniz.

   Daha önce oluşturduğunuz kaynak grubunda yeni bir sanal ağ oluşturun:

   ```azurecli-interactive
   az network vnet create \
      --resource-group $RESOURCEGROUP \
      --name aro-vnet \
      --address-prefixes 10.0.0.0/22
   ```

   Aşağıdaki örnek çıktıda başarıyla oluşturulan sanal ağ gösterilmektedir:

   ```json
   {
     "newVNet": {
       "addressSpace": {
         "addressPrefixes": [
           "10.0.0.0/22"
         ]
       },
       "dhcpOptions": {
         "dnsServers": []
       },
       "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
       "location": "eastus",
       "name": "aro-vnet",
       "provisioningState": "Succeeded",
       "resourceGroup": "aro-rg",
       "type": "Microsoft.Network/virtualNetworks"
     }
   }
   ```

3. **Ana düğümler için boş bir alt ağ ekleyin.**

   ```azurecli-interactive
   az network vnet subnet create \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --name master-subnet \
     --address-prefixes 10.0.0.0/23 \
     --service-endpoints Microsoft.ContainerRegistry
   ```

4. **Çalışan düğümleri için boş bir alt ağ ekleyin.**

   ```azurecli-interactive
   az network vnet subnet create \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --name worker-subnet \
     --address-prefixes 10.0.2.0/23 \
     --service-endpoints Microsoft.ContainerRegistry
   ```

5. **Ana alt ağda [alt ağ özel uç nokta Ilkelerini devre dışı bırakın](../private-link/disable-private-link-service-network-policy.md) .** Bu, hizmetin kümeye bağlanıp yönetmesine izin alabilmesi için gereklidir.

   ```azurecli-interactive
   az network vnet subnet update \
     --name master-subnet \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --disable-private-link-service-network-policies true
   ```

## <a name="create-the-cluster"></a>Kümeyi oluşturma

Bir küme oluşturmak için aşağıdaki komutu çalıştırın. Aşağıdaki seçeneklerden birini kullanmayı seçerseniz, komutu uygun şekilde değiştirin:
* İsteğe bağlı olarak, kümenizin Red Hat kapsayıcısı kayıt defterlerine ek içerikle birlikte erişmesine olanak tanıyan [Red hat çekme gizli anahtarını geçirebilirsiniz](#get-a-red-hat-pull-secret-optional) . `--pull-secret @pull-secret.txt`Komutunuz için bağımsız değişkenini ekleyin.
* İsteğe bağlı olarak, [özel bir etki alanı kullanabilirsiniz](#prepare-a-custom-domain-for-your-cluster-optional). `--domain foo.example.com` `foo.example.com` Kendi özel etki alanınız ile değiştirerek, Komutunuz için bağımsız değişkeni ekleyin.

> [!NOTE]
> Komutunuz için herhangi bir isteğe bağlı bağımsız değişken ekliyorsanız, sonundaki ters eğik çizgiyle komutun önceki satırındaki bağımsız değişkenini kapatmayı unutmayın.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

Komutu yürüttükten sonra `az aro create` , normalde bir küme oluşturmak yaklaşık 35 dakika sürer.

## <a name="next-steps"></a>Sonraki adımlar

Öğreticinin bu bölümünde, şunların nasıl yapıldığını öğrendiniz:
> [!div class="checklist"]
> * Önkoşulları ayarlama ve gerekli sanal ağı ve alt ağları oluşturma
> * Küme dağıtma

Sonraki öğreticiye ilerleyin:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift kümesine bağlanma](tutorial-connect-cluster.md)
