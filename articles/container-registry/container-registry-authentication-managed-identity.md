---
title: Yönetilen kimlikle Azure Container Registry kimlik doğrulaması
description: Kullanıcı tarafından atanan veya sistem tarafından atanan yönetilen bir Azure kimliği kullanarak özel kapsayıcı kayıt defterinizde görüntülere erişim sağlama.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/16/2019
ms.author: danlep
ms.openlocfilehash: 0672fb71ba4f56d0faf332df029100cb48741c8b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309878"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Azure Container Registry 'de kimlik doğrulamak için Azure yönetilen kimliği kullanma 

Kayıt defteri kimlik bilgilerini sağlamaya veya yönetmeye gerek kalmadan, başka bir Azure kaynağından Azure Container Registry 'de kimlik doğrulaması yapmak için [Azure kaynakları için yönetilen bir kimlik](../active-directory/managed-identities-azure-resources/overview.md) kullanın. Örneğin, ortak bir kayıt defteri kullandığınız kadar kolay bir şekilde, kapsayıcı Kayıt defterinizden kapsayıcı görüntülerine erişmek için bir Linux sanal makinesinde Kullanıcı tarafından atanan veya sistem tarafından atanan bir yönetilen kimlik ayarlayın.

Bu makalede, Yönetilen kimlikler ve nasıl yapılır hakkında daha fazla bilgi edinebilirsiniz:

> [!div class="checklist"]
> * Azure VM 'de Kullanıcı tarafından atanan veya sistem tarafından atanan bir kimliği etkinleştirme
> * Azure Container Registry 'ye kimlik erişimi verme
> * Yönetilen kimliği kullanarak kayıt defterine erişin ve bir kapsayıcı görüntüsü çekin 

Azure kaynaklarını oluşturmak için bu makale, Azure CLı sürüm 2.0.55 veya üstünü çalıştırmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli].

Bir kapsayıcı kayıt defteri ayarlamak ve buna bir kapsayıcı görüntüsü göndermek için Ayrıca, Docker 'ın yerel olarak yüklü olması gerekir. Docker, tüm [MacOS][docker-mac], [Windows][docker-windows]veya [Linux][docker-linux] sistemlerinde Docker 'ı kolayca yapılandıran paketler sağlar.

## <a name="why-use-a-managed-identity"></a>Yönetilen kimlik neden kullanılmalıdır?

Azure kaynakları için yönetilen bir kimlik, Azure hizmetlerini Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen kimlik sağlar. Yönetilen bir kimlikle, sanal makineler de dahil olmak üzere [belirli Azure kaynaklarını](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)yapılandırabilirsiniz. Daha sonra, kodu veya betiklerdeki kimlik bilgilerini geçirmeden diğer Azure kaynaklarına erişmek için bu kimliği kullanın.

Yönetilen kimlikler iki türtür:

* Birden fazla kaynağa atayabileceğiniz ve istediğiniz sürece devam eden *Kullanıcı tarafından atanan kimlikler*. Kullanıcı tarafından atanan kimlikler Şu anda önizlemededir.

* Tek bir sanal makine gibi belirli bir kaynak için benzersiz olan ve söz konusu kaynağın kullanım ömrü boyunca kullanılan *sistem tarafından yönetilen bir kimlik*.

Yönetilen kimliğe sahip bir Azure kaynağı ayarladıktan sonra, herhangi bir güvenlik sorumlusu gibi, kimliğe başka bir kaynağa istediğiniz erişimi verin. Örneğin, Azure 'da özel bir kayıt defterine çekme, gönderme ve çekme veya diğer izinleri içeren bir yönetilen kimlik rolü atayın. (Kayıt defteri rollerinin tüm listesi için bkz. [Azure Container Registry roller ve izinler](container-registry-roles.md).) Bir veya daha fazla kaynağa bir kimlik erişimi verebilirsiniz.

Daha sonra, kodunuzda kimlik bilgileri olmadan [Azure AD kimlik doğrulamasını destekleyen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)herhangi bir hizmette kimlik doğrulaması yapmak için bu kimliği kullanın. Bir sanal makineden bir Azure Container Registry 'ye erişmek üzere kimliği kullanmak için Azure Resource Manager ile kimlik doğrulaması yapabilirsiniz. Senaryonuza bağlı olarak yönetilen kimliği kullanarak nasıl kimlik doğrulaması yapılacağını seçin:

* HTTP veya REST çağrılarını kullanarak programlı [bir şekilde Azure AD erişim belirteci alma](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

* [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md) 'larını kullanma

* Kimlik ile [Azure CLI veya PowerShell 'de oturum açın](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) . 

## <a name="create-a-container-registry"></a>Kapsayıcı kayıt defteri oluşturma

Zaten bir Azure Container kayıt defteriniz yoksa bir kayıt defteri oluşturun ve buna örnek bir kapsayıcı görüntüsü gönderin. Adımlar için bkz [. hızlı başlangıç: Azure CLı](container-registry-get-started-azure-cli.md)kullanarak özel bir kapsayıcı kayıt defteri oluşturun.

Bu makalede, `aci-helloworld:v1` kapsayıcı resminin kayıt defterinizde depolandığı varsayılmaktadır. Örneklerde, *Mycontainerregistry*'nin kayıt defteri adı kullanılır. Sonraki adımlarda kendi kayıt defteriniz ve görüntü adlarınızla değiştirin.

## <a name="create-a-docker-enabled-vm"></a>Docker özellikli bir VM oluşturma

Docker özellikli bir Ubuntu sanal makinesi oluşturun. Ayrıca, [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 'yı sanal makineye yüklemeniz gerekir. Zaten bir Azure sanal makineniz varsa, sanal makineyi oluşturmak için bu adımı atlayın.

[Az VM Create][az-vm-create]ile varsayılan bir Ubuntu Azure sanal makinesi dağıtın. Aşağıdaki örnek, *Myresourcegroup*adlı mevcut bir kaynak grubunda *mydockervm* adlı bir sanal makine oluşturur:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM’nin oluşturulması birkaç dakika sürer. Komut tamamlandığında, Azure CLI tarafından `publicIpAddress` görüntülendiğine göz atın. VM 'ye SSH bağlantısı oluşturmak için bu adresi kullanın.

### <a name="install-docker-on-the-vm"></a>SANAL makineye Docker 'ı yükler

VM çalıştırıldıktan sonra VM ile bir SSH bağlantısı oluşturun. *Publicıpaddress* değerini sanal MAKINENIZIN genel IP adresiyle değiştirin.

```bash
ssh azureuser@publicIpAddress
```

SANAL makineye Docker yüklemek için aşağıdaki komutu çalıştırın:

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

Azure CLI 'yi, Ubuntu sanal makinenize yüklemek için [apt Ile Azure CLI 'Yı kurma](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) bölümündeki adımları izleyin. Bu makalede, sürüm 2.0.55 veya üstünü yüklediğinizden emin olun.

SSH oturumundan çıkın.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Örnek 1: Kullanıcı tarafından atanan bir kimlikle erişim

### <a name="create-an-identity"></a>Kimlik oluşturma

[Az Identity Create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) komutunu kullanarak aboneliğinizde bir kimlik oluşturun. Daha önce kapsayıcı kayıt defteri veya sanal makine ya da farklı bir tane oluşturmak için kullandığınız kaynak grubunu kullanabilirsiniz.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Aşağıdaki adımlarda kimliği yapılandırmak için [az Identity Show][az-identity-show] komutunu kullanarak KIMLIğIN kaynak kimliğini ve hızmet sorumlusu kimliğini değişkenlere depolayın.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Sanal makinenizde CLı 'da oturum açarken kimliğin KIMLIĞI daha sonraki bir adımda gerektiğinden, şu değeri gösterin:

```bash
echo $userID
```

KIMLIK şu biçimdedir:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>VM 'yi kimlikle yapılandırma

Aşağıdaki [az VM Identity Assign][az-vm-identity-assign] komutu, DOCKER VM 'nizi Kullanıcı tarafından atanan kimlikle yapılandırır:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Kapsayıcı kayıt defterine kimlik erişimi verme

Şimdi, kapsayıcıyı kapsayıcı Kayıt defterinize erişecek şekilde yapılandırın. Önce, kayıt defterinin kaynak KIMLIĞINI almak için [az ACR Show][az-acr-show] komutunu kullanın:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

AcrPull rolünü kayıt defterine atamak için [az role atama Create][az-role-assignment-create] komutunu kullanın. Bu rol, kayıt defterine [çekme izinleri](container-registry-roles.md) sağlar. Çekme ve anında iletme izinlerini sağlamak için ACRPush rolünü atayın.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Kimliği kullanarak kayıt defterine erişin

Kimliğiyle yapılandırılmış Docker sanal makinesine SSH. VM 'de yüklü olan Azure CLı 'yı kullanarak aşağıdaki Azure CLı komutlarını çalıştırın.

İlk olarak, VM 'de yapılandırdığınız kimliği kullanarak [az Login][az-login]Ile Azure CLI kimlik doğrulamasını yapın. İçin `<userID>`, önceki bir adımda aldığınız kimliğin kimliğini değiştirin. 

```azurecli
az login --identity --username <userID>
```

Ardından, [az ACR oturum açma][az-acr-login]ile kayıt defterine kimlik doğrulaması yapın. Bu komutu kullandığınızda, CLI, oturumunuzu kapsayıcı kayıt defteriyle sorunsuzca kimlik doğrulamaya çalışırken `az login` oluşturulan Active Directory belirtecini kullanır. (Sanal makinenizin kurulumuna bağlı olarak, bu komutu ve Docker komutlarını ile `sudo`çalıştırmanız gerekebilir.)

```azurecli
az acr login --name myContainerRegistry
```

Bir `Login succeeded` ileti görmeniz gerekir. Ardından, kimlik bilgileri `docker` sağlamadan komutları çalıştırabilirsiniz. Örneğin, kayıt defterinizin [][docker-pull] oturum açma sunucusu adını `aci-helloworld:v1` belirterek görüntüyü çekmek için Docker Pull çalıştırın. Oturum açma sunucusu adı, kapsayıcının kayıt defteri adınızın (tümü küçük harflerinden) ve `.azurecr.io` ardından gelen ' `mycontainerregistry.azurecr.io`den oluşur.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Örnek 2: Sistem tarafından atanan bir kimlikle erişim

### <a name="configure-the-vm-with-a-system-managed-identity"></a>VM 'yi sistem tarafından yönetilen bir kimlikle yapılandırma

Aşağıdaki [az VM Identity Assign][az-vm-identity-assign] komutu, DOCKER VM 'nizi sistem tarafından atanan bir kimlikle yapılandırır:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Sonraki adımlarda kullanmak üzere VM 'nin kimliğinin değerine `principalId` (hizmet asıl kimliği) bir değişken ayarlamak için [az VM Show][az-vm-show] komutunu kullanın.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Kapsayıcı kayıt defterine kimlik erişimi verme

Şimdi, kapsayıcıyı kapsayıcı Kayıt defterinize erişecek şekilde yapılandırın. Önce, kayıt defterinin kaynak KIMLIĞINI almak için [az ACR Show][az-acr-show] komutunu kullanın:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Kimliğe AcrPull rolünü atamak için [az role atama Create][az-role-assignment-create] komutunu kullanın. Bu rol, kayıt defterine [çekme izinleri](container-registry-roles.md) sağlar. Çekme ve anında iletme izinlerini sağlamak için ACRPush rolünü atayın.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Kimliği kullanarak kayıt defterine erişin

Kimliğiyle yapılandırılmış Docker sanal makinesine SSH. VM 'de yüklü olan Azure CLı 'yı kullanarak aşağıdaki Azure CLı komutlarını çalıştırın.

İlk olarak, VM 'deki sistem tarafından atanan kimliği kullanarak [az oturum açma][az-login]Ile Azure CLI kimlik doğrulamasını yapın.

```azurecli
az login --identity
```

Ardından, [az ACR oturum açma][az-acr-login]ile kayıt defterine kimlik doğrulaması yapın. Bu komutu kullandığınızda, CLI, oturumunuzu kapsayıcı kayıt defteriyle sorunsuzca kimlik doğrulamaya çalışırken `az login` oluşturulan Active Directory belirtecini kullanır. (Sanal makinenizin kurulumuna bağlı olarak, bu komutu ve Docker komutlarını ile `sudo`çalıştırmanız gerekebilir.)

```azurecli
az acr login --name myContainerRegistry
```

Bir `Login succeeded` ileti görmeniz gerekir. Ardından, kimlik bilgileri `docker` sağlamadan komutları çalıştırabilirsiniz. Örneğin, kayıt defterinizin [][docker-pull] oturum açma sunucusu adını `aci-helloworld:v1` belirterek görüntüyü çekmek için Docker Pull çalıştırın. Oturum açma sunucusu adı, kapsayıcının kayıt defteri adınızın (tümü küçük harflerinden) ve `.azurecr.io` ardından gelen ' `mycontainerregistry.azurecr.io`den oluşur.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Container Registry ile yönetilen kimlikler kullanma hakkında bilgi edindiniz ve şunları yapabilirsiniz:

> [!div class="checklist"]
> * Azure VM 'de Kullanıcı tarafından atanan veya sistem tarafından atanan bir kimliği etkinleştirme
> * Azure Container Registry 'ye kimlik erişimi verme
> * Yönetilen kimliği kullanarak kayıt defterine erişin ve bir kapsayıcı görüntüsü çekin

* [Azure kaynakları için Yönetilen kimlikler](/azure/active-directory/managed-identities-azure-resources/)hakkında daha fazla bilgi edinin.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
