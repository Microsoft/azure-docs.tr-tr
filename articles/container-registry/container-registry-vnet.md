---
title: Bir sanal ağla Azure Container Registry erişimi kısıtla
description: Azure Container Registry 'ye yalnızca bir Azure sanal ağındaki veya genel IP adresi aralıklarından gelen kaynaklardan erişime izin verin.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: 5ba5c180def9539c486fb8727a0a78b4f98fa185
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931327"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Azure sanal ağını veya güvenlik duvarı kurallarını kullanarak bir Azure Container Registry 'ye erişimi kısıtlama

[Azure sanal ağ](../virtual-network/virtual-networks-overview.md) , Azure ve şirket içi kaynaklarınız için güvenli, özel ağ sağlar. Azure sanal ağından özel Azure Container Registry 'nize erişimi sınırlayarak, yalnızca sanal ağdaki kaynakların kayıt defterine erişebildiğinden emin olursunuz. Şirketler arası senaryolar için, yalnızca belirli IP adreslerinden kayıt defteri erişimine izin vermek üzere güvenlik duvarı kurallarını da yapılandırabilirsiniz.

Bu makalede bir kapsayıcı kayıt defterinde gelen ağ erişim kurallarını yapılandırmak için iki senaryo gösterilmektedir: sanal bir ağda dağıtılan bir sanal makineden veya bir VM 'nin genel IP adresinden.

> [!IMPORTANT]
> Bu özellik şu anda önizleme aşamasındadır ve bazı [sınırlamalar geçerlidir](#preview-limitations). Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.
>

Bunun yerine, bir güvenlik duvarının arkasındaki bir kapsayıcı kayıt defterine ulaşmak için kaynakların erişim kurallarını ayarlamanız gerekiyorsa, bkz. [güvenlik duvarı arkasındaki bir Azure Container Registry 'ye erişmek için kuralları yapılandırma](container-registry-firewall-access-rules.md).


## <a name="preview-limitations"></a>Önizleme sınırlamaları

* Yalnızca bir **Premium** kapsayıcı kayıt defteri, ağ erişim kurallarıyla yapılandırılabilir. Kayıt defteri hizmeti katmanları hakkında bilgi için bkz. [Azure Container Registry SKU 'lar](container-registry-skus.md). 

* Bir sanal ağdaki kapsayıcı kayıt defterine erişmek için bir konak olarak yalnızca bir [Azure Kubernetes hizmet](../aks/intro-kubernetes.md) kümesi veya Azure [sanal makinesi](../virtual-machines/linux/overview.md) kullanılabilir. *Azure Container Instances dahil diğer Azure hizmetleri şu anda desteklenmemektedir.*

* [ACR görev](container-registry-tasks-overview.md) işlemleri şu anda bir sanal ağda erişilen bir kapsayıcı kayıt defterinde desteklenmiyor.

* Her kayıt defteri en fazla 100 sanal ağ kuralını destekler.

## <a name="prerequisites"></a>Önkoşullar

* Bu makalede Azure CLı adımlarını kullanmak için Azure CLı sürüm 2.0.58 veya üzeri gereklidir. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli].

* Zaten bir kapsayıcı kayıt defteriniz yoksa, bir tane oluşturun (Premium SKU gerekir) ve Docker Hub 'ından `hello-world` gibi örnek bir görüntü gönderin. Örneğin, [Azure Portal][quickstart-portal] veya [Azure CLI][quickstart-cli] kullanarak bir kayıt defteri oluşturun. 

* Farklı bir Azure aboneliğindeki bir sanal ağ kullanarak kayıt defteri erişimini kısıtlamak istiyorsanız, bu abonelikte Azure Container Registry kaynak sağlayıcısını kaydetmeniz gerekir. Örneğin:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>Bir kapsayıcı kayıt defteri için ağ kuralları hakkında

Azure Container Registry, varsayılan olarak herhangi bir ağdaki konaklardan internet üzerinden bağlantıları kabul eder. Bir sanal ağ ile, bir ağ sınırını taşımadan, kayıt defterine güvenli bir şekilde erişmek için yalnızca bir AKS kümesi veya Azure VM gibi Azure kaynaklarına izin verebilirsiniz. Ayrıca, ağ güvenlik duvarı kurallarını yalnızca belirli genel İnternet IP adresi aralıklarına izin verecek şekilde yapılandırabilirsiniz. 

Bir kayıt defterine erişimi sınırlandırmak için, önce tüm ağ bağlantılarını reddetmeye yönelik kayıt defteri varsayılan eylemini değiştirin. Ardından, ağ erişim kuralları ekleyin. Ağ kuralları aracılığıyla erişim izni verilen istemciler, [kapsayıcı kayıt defterinde kimlik doğrulamaya](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) ve verilere erişme yetkisine sahip olmaya devam etmelidir.

### <a name="service-endpoint-for-subnets"></a>Alt ağlar için hizmet uç noktası

Sanal ağdaki bir alt ağdan erişime izin vermek için Azure Container Registry hizmeti için bir [hizmet uç noktası](../virtual-network/virtual-network-service-endpoints-overview.md) eklemeniz gerekir. 

Azure Container Registry gibi çok kiracılı hizmetler, tüm müşteriler için tek bir IP adresleri kümesi kullanır. Hizmet uç noktası bir kayıt defterine erişmek için bir uç nokta atar. Bu uç nokta, trafiğe Azure omurga ağı üzerinden en uygun yolu verir. Sanal ağın ve alt ağın kimlikleri de her istekle birlikte iletilir.

### <a name="firewall-rules"></a>Güvenlik duvarı kuralları

IP ağ kuralları için, *16.17.18.0/24* gibi CIDR gösterimini kullanarak izin verilen internet adresi aralıklarını veya *16.17.18.19*gibi tek bir IP adresini belirtin. IP ağ kurallarına yalnızca *genel* internet IP adresleri için izin verilir. Özel ağlar için ayrılan IP adresi aralıklarına (RFC 1918 ' de tanımlandığı gibi) IP kurallarında izin verilmez.

## <a name="create-a-docker-enabled-virtual-machine"></a>Docker özellikli bir sanal makine oluşturma

Bu makalede, bir Azure Container Registry 'ye erişmek için Docker özellikli bir Ubuntu VM kullanın. Kayıt defterine Azure Active Directory kimlik doğrulaması kullanmak için [Azure CLI][azure-cli] 'yı sanal makineye de yüklersiniz. Zaten bir Azure sanal makineniz varsa, bu oluşturma adımını atlayın.

Sanal makineniz ve kapsayıcı kayıt defteriniz için aynı kaynak grubunu kullanabilirsiniz. Bu kurulum, sonda temizleme işlemini basitleştirir, ancak gerekli değildir. Sanal makine ve sanal ağ için ayrı bir kaynak grubu oluşturmayı seçerseniz, [az Group Create][az-group-create]komutunu çalıştırın. Aşağıdaki örnek *westcentralus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli
az group create --name myResourceGroup --location westus
```

Şimdi [az VM Create][az-vm-create]ile varsayılan bir Ubuntu Azure sanal makinesini dağıtın. Aşağıdaki örnek, *Mydockervm*adlı bir sanal makine oluşturur:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM’nin oluşturulması birkaç dakika sürer. Komut tamamlandığında, Azure CLı tarafından gösterilecek `publicIpAddress` göz atın. Bu adresi VM 'ye SSH bağlantısı kurmak ve isteğe bağlı olarak daha sonra güvenlik duvarı kuralları kurmak için kullanın.

### <a name="install-docker-on-the-vm"></a>SANAL makineye Docker 'ı yükler

VM çalıştırıldıktan sonra VM ile bir SSH bağlantısı oluşturun. *Publicıpaddress* değerini sanal MAKINENIZIN genel IP adresiyle değiştirin.

```bash
ssh azureuser@publicIpAddress
```

Ubuntu VM 'ye Docker yüklemek için aşağıdaki komutu çalıştırın:

```bash
sudo apt install docker.io -y
```

Yükleme sonrasında, Docker 'ın sanal makinede düzgün çalıştığını doğrulamak için aşağıdaki komutu çalıştırın:

```bash
sudo docker run -it hello-world
```

Çıktı:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure CLI'yı yükleme

Azure CLI 'yi, Ubuntu sanal makinenize yüklemek için [apt Ile Azure CLI 'Yı kurma](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) bölümündeki adımları izleyin. Bu makalede, sürüm 2.0.58 veya üstünü yüklediğinizden emin olun.

SSH bağlantısından çıkın.

## <a name="allow-access-from-a-virtual-network"></a>Bir sanal ağdan erişime izin ver

Bu bölümde, kapsayıcı kayıt defterinizi Azure sanal ağındaki bir alt ağdan erişime izin verecek şekilde yapılandırın. Azure CLı ve Azure portal ile eşdeğer adımlar sağlanır.

### <a name="allow-access-from-a-virtual-network---cli"></a>Bir sanal ağ üzerinden erişime izin ver-CLı

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Bir alt ağa hizmet uç noktası ekleme

Bir VM oluşturduğunuzda, Azure varsayılan olarak aynı kaynak grubunda bir sanal ağ oluşturur. Sanal ağın adı, sanal makinenin adını temel alır. Örneğin, *Mydockervm*sanal makinenizi adlandırdıysanız, varsayılan sanal ağ adı Mydockervmvnet adlı bir alt ağ ile Mydockervmvnet *olur.* Bunu Azure portal veya [az Network VNET List][az-network-vnet-list] komutunu kullanarak doğrulayın:

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

Çıktı:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Alt ağınıza bir **Microsoft. ContainerRegistry** hizmeti uç noktası eklemek için [az Network VNET subnet Update][az-network-vnet-subnet-update] komutunu kullanın. Aşağıdaki komutta, sanal ağınızın ve alt ağınızın adlarını değiştirin:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Alt ağın kaynak KIMLIĞINI almak için [az Network VNET subnet Show][az-network-vnet-subnet-show] komutunu kullanın. Bu, bir ağ erişim kuralını yapılandırmak için sonraki bir adımda gerekli olacaktır.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Çıktı:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>Kayıt defteri için varsayılan ağ erişimini değiştirme

Varsayılan olarak, bir Azure Container Registry, herhangi bir ağdaki konaklardan gelen bağlantılara izin verir. Seçilen bir ağa erişimi sınırlandırmak için, varsayılan eylemi erişimi Reddet olarak değiştirin. Kayıt defterinizin adını şu [az ACR Update][az-acr-update] komutunda değiştirin:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Kayıt defterine ağ kuralı ekle

Kayıt defterinize VM 'nin alt ağından erişime izin veren bir ağ kuralı eklemek için [az ACR Network-Rule Add][az-acr-network-rule-add] komutunu kullanın. Aşağıdaki komutta, kapsayıcı kayıt defterinin adını ve alt ağın kaynak KIMLIĞINI değiştirin: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

[Kayıt defterine erişimi doğrulamaya](#verify-access-to-the-registry)devam edin.

### <a name="allow-access-from-a-virtual-network---portal"></a>Bir sanal ağ-Portal ' dan erişime izin ver

#### <a name="add-service-endpoint-to-subnet"></a>Alt ağa hizmet uç noktası ekle

Bir VM oluşturduğunuzda, Azure varsayılan olarak aynı kaynak grubunda bir sanal ağ oluşturur. Sanal ağın adı, sanal makinenin adını temel alır. Örneğin, *Mydockervm*sanal makinenizi adlandırdıysanız, varsayılan sanal ağ adı Mydockervmvnet adlı bir alt ağ ile Mydockervmvnet *olur.*

Bir alt ağa Azure Container Registry için bir hizmet uç noktası eklemek için:

1. [Azure Portal][azure-portal]üst kısmındaki arama kutusuna *sanal ağlar*girin. Arama sonuçlarında **sanal ağlar** görüntülendiğinde, bunu seçin.
1. Sanal ağlar listesinden, sanal makinenizin dağıtıldığı sanal ağı ( *Mydockervmvnet*gibi) seçin.
1. **Ayarlar**altında **alt ağlar**' ı seçin.
1. Sanal makinenizin dağıtıldığı alt ağı seçin; örneğin, *Mydockervmsubnet*.
1. **Hizmet uç noktaları**altında **Microsoft. containerregistry**' yi seçin.
1. **Kaydet**’i seçin.

![Alt ağa hizmet uç noktası ekle][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Kayıt defteri için ağ erişimini yapılandırma

Varsayılan olarak, bir Azure Container Registry, herhangi bir ağdaki konaklardan gelen bağlantılara izin verir. Sanal ağa erişimi sınırlandırmak için:

1. Portalda kapsayıcı Kayıt defterinize gidin.
1. **Ayarlar**altında **güvenlik duvarı ve sanal ağlar**' ı seçin.
1. Erişimi varsayılan olarak reddetmek için, **Seçili ağlardan**erişime izin ver ' i seçin. 
1. **Var olan sanal ağı ekle**' yi seçin ve bir hizmet uç noktası ile yapılandırdığınız sanal ağı ve alt ağı seçin. **Add (Ekle)** seçeneğini belirleyin.
1. **Kaydet**’i seçin.

![Kapsayıcı kayıt defteri için sanal ağı yapılandırma][acr-vnet-portal]

[Kayıt defterine erişimi doğrulamaya](#verify-access-to-the-registry)devam edin.

## <a name="allow-access-from-an-ip-address"></a>IP adresinden erişime izin ver

Bu bölümde, kapsayıcı kayıt defterinizi belirli bir IP adresinden veya aralıktan erişime izin verecek şekilde yapılandırın. Azure CLı ve Azure portal ile eşdeğer adımlar sağlanır.

### <a name="allow-access-from-an-ip-address---cli"></a>IP adresinden erişime izin ver-CLı

#### <a name="change-default-network-access-to-registry"></a>Kayıt defteri için varsayılan ağ erişimini değiştirme

Daha önce yapmadıysanız, kayıt defteri yapılandırmasını varsayılan olarak erişimi reddedecek şekilde güncelleştirin. Kayıt defterinizin adını şu [az ACR Update][az-acr-update] komutunda değiştirin:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Ağ kuralını kayıt defterinden kaldır

VM 'nin alt ağından erişime izin vermek için daha önce bir ağ kuralı eklediyseniz, alt ağın hizmet uç noktasını ve ağ kuralını kaldırın. Daha önceki bir adımda aldığınız alt ağın kapsayıcı kayıt defteri adını ve kaynak KIMLIĞINI [az ACR Network Rule Remove][az-acr-network-rule-remove] komutunda değiştirin: 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>Kayıt defterine ağ kuralı ekle

Kayıt defterinize VM 'nin IP adresinden erişime izin veren bir ağ kuralı eklemek için [az ACR Network-Rule Add][az-acr-network-rule-add] komutunu kullanın. Aşağıdaki komutta, kapsayıcı kayıt defterinin adını ve VM 'nin genel IP adresini değiştirin.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

[Kayıt defterine erişimi doğrulamaya](#verify-access-to-the-registry)devam edin.

### <a name="allow-access-from-an-ip-address---portal"></a>Bir IP adresinden erişime izin ver-Portal

#### <a name="remove-existing-network-rule-from-registry"></a>Mevcut ağ kuralını kayıt defterinden kaldır

VM 'nin alt ağından erişime izin vermek için daha önce bir ağ kuralı eklediyseniz, mevcut kuralı kaldırın. Kayıt defterine farklı bir VM 'den erişmek istiyorsanız bu bölümü atlayın.

* Alt ağın hizmet uç noktasını Azure Container Registry kaldırmak için alt ağ ayarlarını güncelleştirin. 

  1. [Azure Portal][azure-portal], sanal makinenizin dağıtıldığı sanal ağa gidin.
  1. **Ayarlar**altında **alt ağlar**' ı seçin.
  1. Sanal makinenizin dağıtıldığı alt ağı seçin.
  1. **Hizmet uç noktaları**altında **Microsoft. containerregistry**onay kutusunu kaldırın. 
  1. **Kaydet**’i seçin.

* Alt ağın kayıt defterine erişmesine izin veren ağ kuralını kaldırın.

  1. Portalda kapsayıcı Kayıt defterinize gidin.
  1. **Ayarlar**altında **güvenlik duvarı ve sanal ağlar**' ı seçin.
  1. **Sanal ağlar**altında sanal ağın adını seçip **Kaldır**' ı seçin.
  1. **Kaydet**’i seçin.

#### <a name="add-network-rule-to-registry"></a>Kayıt defterine ağ kuralı ekle

1. Portalda kapsayıcı Kayıt defterinize gidin.
1. **Ayarlar**altında **güvenlik duvarı ve sanal ağlar**' ı seçin.
1. Daha önce yapmadıysanız, **Seçili ağlardan**erişime izin vermeyi seçin. 
1. **Sanal ağlar**altında ağın seçili olmadığından emin olun.
1. **Güvenlik duvarı**altında, bir VM 'nın genel IP adresini girin. Ya da VM 'nin IP adresini içeren CıDR gösteriminde bir adres aralığı girin.
1. **Kaydet**’i seçin.

![Kapsayıcı kayıt defteri için güvenlik duvarı kuralını yapılandırma][acr-vnet-firewall-portal]

[Kayıt defterine erişimi doğrulamaya](#verify-access-to-the-registry)devam edin.

## <a name="verify-access-to-the-registry"></a>Kayıt defterine erişimi doğrulama

Yapılandırmanın güncelleştirilmesi birkaç dakika bekledikten sonra, sanal makinenin kapsayıcı kayıt defterine erişebildiğini doğrulayın. Sanal makinenize bir SSH bağlantısı oluşturun ve [az ACR Login][az-acr-login] komutunu çalıştırarak kayıt defterinizde oturum açın. 

```bash
az acr login --name mycontainerregistry
```

Kayıt defterinden örnek bir görüntü çekmek için `docker pull` Çalıştır gibi kayıt defteri işlemleri gerçekleştirebilirsiniz. Kayıt defteri oturum açma sunucusu adı (tümü küçük harf) önekini kullanarak kayıt defteriniz için uygun bir görüntü ve etiket değeri koyun:

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker görüntüyü sanal makineye başarıyla çeker.

Bu örnekte, özel kapsayıcı kayıt defterine ağ erişim kuralıyla erişebileceğiniz gösterilmektedir. Ancak, kayıt defterine, yapılandırılmış bir ağ erişim kuralına sahip olmayan farklı bir oturum açma konağından erişilemez. `az acr login` komutunu veya `docker login` komutunu kullanarak başka bir konaktan oturum açmaya çalışırsanız, çıkış aşağıdakine benzer:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Varsayılan kayıt defteri erişimini geri yükle

Kayıt defterini varsayılan olarak erişime izin verecek şekilde geri yüklemek için, yapılandırılmış tüm ağ kurallarını kaldırın. Ardından, varsayılan eylemi erişime izin verecek şekilde ayarlayın. Azure CLı ve Azure portal ile eşdeğer adımlar sağlanır.

### <a name="restore-default-registry-access---cli"></a>Varsayılan kayıt defteri erişimini geri yükleme-CLı

#### <a name="remove-network-rules"></a>Ağ kurallarını kaldır

Kayıt defteriniz için yapılandırılmış ağ kurallarının bir listesini görmek için şu [az ACR Network-Rule List][az-acr-network-rule-list] komutunu çalıştırın:

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Yapılandırılan her kural için [az ACR Network-Rule Remove][az-acr-network-rule-remove] komutunu çalıştırarak kaldırın. Örneğin:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>Erişime izin ver

Kayıt defterinizin adını şu [az ACR Update][az-acr-update] komutunda değiştirin:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Varsayılan kayıt defteri erişimini geri yükleme-Portal


1. Portalda kapsayıcı Kayıt defterinize gidin ve **güvenlik duvarı ve sanal ağlar '** ı seçin.
1. **Sanal ağlar**altında, her bir sanal ağı seçin ve ardından **Kaldır**' ı seçin.
1. **Güvenlik duvarı**altında, her bir adres aralığını seçin ve ardından Sil simgesini seçin.
1. **Erişime Izin ver**' ın altında **tüm ağlar**' ı seçin. 
1. **Kaydet**’i seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Tüm Azure kaynaklarını aynı kaynak grubunda oluşturduysanız ve artık gerekmiyorsa, tek bir [az Group Delete](/cli/azure/group) komutu kullanarak kaynakları isteğe bağlı olarak silebilirsiniz:

```azurecli
az group delete --name myResourceGroup
```

Portalda kaynaklarınızı temizlemek için myResourceGroup kaynak grubuna gidin. Kaynak grubu yüklendikten sonra kaynak grubunu **Sil** ' e tıklayarak kaynak grubunu ve burada depolanan kaynakları kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kısaca çok sayıda sanal ağ kaynağı ve özelliği ele alınmıştır. Azure sanal ağ belgeleri bu konuları kapsamlı bir şekilde ele almaktadır:

* [Sanal ağ](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Alt ağ](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Hizmet uç noktaları](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
