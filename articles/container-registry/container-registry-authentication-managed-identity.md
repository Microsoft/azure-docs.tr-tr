---
title: Yönetilen kimlikle kimlik doğrulaması
description: Kullanıcı tarafından atanan veya sistem tarafından atanan yönetilen bir Azure kimliği kullanarak özel kapsayıcı kayıt defterinizde görüntülere erişim sağlama.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 213f49356fdc2444f8bc2cb4635e96015aff0a61
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781550"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Azure Container Registry 'de kimlik doğrulamak için Azure yönetilen kimliği kullanma 

Kayıt defteri kimlik bilgilerini sağlamaya veya yönetmeye gerek kalmadan, başka bir Azure kaynağından Azure Container Registry 'de kimlik doğrulaması yapmak için [Azure kaynakları için yönetilen bir kimlik](../active-directory/managed-identities-azure-resources/overview.md) kullanın. Örneğin, ortak bir kayıt defteri kullandığınız kadar kolay bir şekilde, kapsayıcı Kayıt defterinizden kapsayıcı görüntülerine erişmek için bir Linux sanal makinesinde Kullanıcı tarafından atanan veya sistem tarafından atanan bir yönetilen kimlik ayarlayın. Ya da, bir Azure Kubernetes hizmet kümesini, Pod dağıtımı için Azure Container Registry kapsayıcı görüntülerini çekmek üzere [yönetilen kimliğini](../aks/use-managed-identity.md) kullanacak şekilde ayarlayın.

Bu makalede, Yönetilen kimlikler ve nasıl yapılır hakkında daha fazla bilgi edinebilirsiniz:

> [!div class="checklist"]
> * Azure VM 'de Kullanıcı tarafından atanan veya sistem tarafından atanan bir kimliği etkinleştirme
> * Azure Container Registry 'ye kimlik erişimi verme
> * Yönetilen kimliği kullanarak kayıt defterine erişin ve bir kapsayıcı görüntüsü çekin 

Azure kaynaklarını oluşturmak için bu makale, Azure CLı sürüm 2.0.55 veya üstünü çalıştırmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli].

Bir kapsayıcı kayıt defteri ayarlamak ve buna bir kapsayıcı görüntüsü göndermek için Ayrıca, Docker 'ın yerel olarak yüklü olması gerekir. Docker [macOS][docker-mac], [Windows][docker-windows] veya [Linux][docker-linux]'ta Docker'ı kolayca yapılandırmanızı sağlayan paketler sağlar.

## <a name="why-use-a-managed-identity"></a>Yönetilen kimlik neden kullanılmalıdır?

Azure kaynakları için yönetilen kimlikler özelliği hakkında bilgi sahibi değilseniz bu [genel bakışı](../active-directory/managed-identities-azure-resources/overview.md) inceleyin.

Seçili Azure kaynaklarını yönetilen bir kimlikle ayarladıktan sonra, herhangi bir güvenlik sorumlusu gibi, kimliğe başka bir kaynağa istediğiniz erişimi verin. Örneğin, Azure 'da özel bir kayıt defterine çekme, gönderme ve çekme veya diğer izinleri içeren bir yönetilen kimlik rolü atayın. (Kayıt defteri rollerinin tüm listesi için bkz. [Azure Container Registry roller ve izinler](container-registry-roles.md).) Bir veya daha fazla kaynağa bir kimlik erişimi verebilirsiniz.

Daha sonra, kodunuzda kimlik bilgileri olmadan [Azure AD kimlik doğrulamasını destekleyen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)herhangi bir hizmette kimlik doğrulaması yapmak için bu kimliği kullanın. Senaryonuza bağlı olarak, yönetilen kimliği kullanarak nasıl kimlik doğrulaması yapılacağını seçin. Bir sanal makineden bir Azure Container Registry 'ye erişmek üzere kimliği kullanmak için Azure Resource Manager ile kimlik doğrulaması yapabilirsiniz. 

> [!NOTE]
> Şu anda Azure Kapsayıcılar için Web App veya Azure Container Instances gibi hizmetler, kapsayıcı kaynağının kendisini dağıtmak üzere bir kapsayıcı görüntüsü çekmekte olan Azure Container Registry kimlik doğrulaması yapmak için yönetilen kimliğini kullanamaz. Kimlik yalnızca kapsayıcı çalışmaya başladıktan sonra kullanılabilir. Azure Container Registry görüntülerini kullanarak bu kaynakları dağıtmak için, [hizmet sorumlusu](container-registry-auth-service-principal.md) gibi farklı bir kimlik doğrulama yöntemi önerilir.

## <a name="create-a-container-registry"></a>Kapsayıcı kayıt defteri oluşturma

Zaten bir Azure Container kayıt defteriniz yoksa bir kayıt defteri oluşturun ve buna örnek bir kapsayıcı görüntüsü gönderin. Adımlar için bkz. [hızlı başlangıç: Azure CLI kullanarak özel kapsayıcı kayıt defteri oluşturma](container-registry-get-started-azure-cli.md).

Bu makalede, `aci-helloworld:v1` kapsayıcı resminin kayıt defterinizde depolandığı varsayılmaktadır. Örneklerde, *Mycontainerregistry*'nin kayıt defteri adı kullanılır. Sonraki adımlarda kendi kayıt defteriniz ve görüntü adlarınızla değiştirin.

## <a name="create-a-docker-enabled-vm"></a>Docker özellikli bir VM oluşturma

Docker özellikli bir Ubuntu sanal makinesi oluşturun. Ayrıca, [Azure CLI](/cli/azure/install-azure-cli) 'yı sanal makineye yüklemeniz gerekir. Zaten bir Azure sanal makineniz varsa, sanal makineyi oluşturmak için bu adımı atlayın.

[Az VM Create][az-vm-create]ile varsayılan bir Ubuntu Azure sanal makinesi dağıtın. Aşağıdaki örnek, *Myresourcegroup* adlı mevcut bir kaynak grubunda *mydockervm* adlı bir sanal makine oluşturur:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM’nin oluşturulması birkaç dakika sürer. Komut tamamlandığında, `publicIpAddress` Azure CLI tarafından görüntülendiğine göz atın. VM 'ye SSH bağlantısı oluşturmak için bu adresi kullanın.

### <a name="install-docker-on-the-vm"></a>SANAL makineye Docker 'ı yükler

VM çalıştırıldıktan sonra VM ile bir SSH bağlantısı oluşturun. *Publicıpaddress* değerini sanal MAKINENIZIN genel IP adresiyle değiştirin.

```bash
ssh azureuser@publicIpAddress
```

SANAL makineye Docker yüklemek için aşağıdaki komutu çalıştırın:

```bash
sudo apt update
sudo apt install docker.io -y
```

Yükleme sonrasında, Docker 'ın sanal makinede düzgün çalıştığını doğrulamak için aşağıdaki komutu çalıştırın:

```bash
sudo docker run -it mcr.microsoft.com/hello-world
```

Çıkış:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure CLI'yi yükleme

Azure CLI 'yi, Ubuntu sanal makinenize yüklemek için [apt Ile Azure CLI 'Yı kurma](/cli/azure/install-azure-cli-apt) bölümündeki adımları izleyin. Bu makalede, sürüm 2.0.55 veya üstünü yüklediğinizden emin olun.

SSH oturumundan çıkın.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Örnek 1: Kullanıcı tarafından atanan bir kimlikle erişim

### <a name="create-an-identity"></a>Kimlik oluşturma

[Az Identity Create](/cli/azure/identity#az_identity_create) komutunu kullanarak aboneliğinizde bir kimlik oluşturun. Daha önce kapsayıcı kayıt defteri veya sanal makine ya da farklı bir tane oluşturmak için kullandığınız kaynak grubunu kullanabilirsiniz.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Aşağıdaki adımlarda kimliği yapılandırmak için, kimliğin kaynak KIMLIĞI ve hizmet sorumlusu KIMLIĞINI değişkenlerde depolamak üzere [az Identity Show] [az_identity_show] komutunu kullanın.

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

İlk olarak, VM 'de yapılandırdığınız kimliği kullanarak [az Login][az-login]Ile Azure CLI kimlik doğrulamasını yapın. İçin `<userID>` , önceki bir adımda aldığınız KIMLIğIN kimliğini değiştirin. 

```azurecli
az login --identity --username <userID>
```

Ardından, [az ACR oturum açma][az-acr-login]ile kayıt defterine kimlik doğrulaması yapın. Bu komutu kullandığınızda, CLı, `az login` oturumunuzu kapsayıcı kayıt defteriyle sorunsuzca kimlik doğrulamaya çalışırken oluşturulan Active Directory belirtecini kullanır. (Sanal makinenizin kurulumuna bağlı olarak, bu komutu ve Docker komutlarını ile çalıştırmanız gerekebilir `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Bir ileti görmeniz gerekir `Login succeeded` . Ardından, `docker` kimlik bilgileri sağlamadan komutları çalıştırabilirsiniz. Örneğin, [][docker-pull] `aci-helloworld:v1` kayıt defterinizin oturum açma sunucusu adını belirterek görüntüyü çekmek için Docker Pull çalıştırın. Oturum açma sunucusu adı, kapsayıcının kayıt defteri adınızın (tümü küçük harflerinden) ve ardından gelen ' den oluşur `.azurecr.io` `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Örnek 2: sistem tarafından atanan bir kimlikle erişim

### <a name="configure-the-vm-with-a-system-managed-identity"></a>VM 'yi sistem tarafından yönetilen bir kimlikle yapılandırma

Aşağıdaki [az VM Identity Assign][az-vm-identity-assign] komutu, DOCKER VM 'nizi sistem tarafından atanan bir kimlikle yapılandırır:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Sonraki adımlarda kullanmak üzere VM 'nin kimliğinin değerine (hizmet asıl KIMLIĞI) bir değişken ayarlamak için [az VM Show][az-vm-show] komutunu kullanın `principalId` .

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

Ardından, [az ACR oturum açma][az-acr-login]ile kayıt defterine kimlik doğrulaması yapın. Bu komutu kullandığınızda, CLı, `az login` oturumunuzu kapsayıcı kayıt defteriyle sorunsuzca kimlik doğrulamaya çalışırken oluşturulan Active Directory belirtecini kullanır. (Sanal makinenizin kurulumuna bağlı olarak, bu komutu ve Docker komutlarını ile çalıştırmanız gerekebilir `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Bir ileti görmeniz gerekir `Login succeeded` . Ardından, `docker` kimlik bilgileri sağlamadan komutları çalıştırabilirsiniz. Örneğin, [][docker-pull] `aci-helloworld:v1` kayıt defterinizin oturum açma sunucusu adını belirterek görüntüyü çekmek için Docker Pull çalıştırın. Oturum açma sunucusu adı, kapsayıcının kayıt defteri adınızın (tümü küçük harflerinden) ve ardından gelen ' den oluşur `.azurecr.io` `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Container Registry ile yönetilen kimlikler kullanma hakkında bilgi edindiniz ve şunları yapabilirsiniz:

> [!div class="checklist"]
> * Azure VM 'de Kullanıcı tarafından atanan veya sistem tarafından atanan bir kimliği etkinleştirme
> * Azure Container Registry 'ye kimlik erişimi verme
> * Yönetilen kimliği kullanarak kayıt defterine erişin ve bir kapsayıcı görüntüsü çekin

* [Azure kaynakları için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/index.yml)hakkında daha fazla bilgi edinin.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-vm-show]: /cli/azure/vm#az_vm_show
[az-vm-identity-assign]: /cli/azure/vm/identity#az_vm_identity_assign
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-identity-show]: /cli/azure/identity#az_identity_show
[azure-cli]: /cli/azure/install-azure-cli
