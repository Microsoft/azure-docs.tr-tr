---
title: Azure Container Registry görevlerle yönetilen bir kimlik kullanma
description: Görevin diğer özel kapsayıcı kayıt defterleri dahil diğer Azure kaynaklarına erişmesine izin vermek için bir Azure Container Registry görevindeki Azure kaynakları için yönetilen bir kimliği etkinleştirin.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/11/2019
ms.author: danlep
ms.openlocfilehash: 9f7c083a079e42172a9e2865f90293fa4d6813d8
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640417"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>ACR görevlerinde Azure tarafından yönetilen bir kimlik kullanma 

Bir [ACR görevinde](container-registry-tasks-overview.md) [Azure kaynakları için yönetilen bir kimliği](../active-directory/managed-identities-azure-resources/overview.md) etkinleştirin, bu nedenle görev, kimlik bilgilerini sağlamaya veya yönetmeye gerek kalmadan diğer Azure kaynaklarına erişebilir. Örneğin, bir görev adımının kapsayıcı görüntülerini başka bir kayıt defterine çekme veya gönderme için bir yönetilen kimlik kullanın.

Bu makalede, bir ACR görevinde Kullanıcı tarafından atanan veya sistem tarafından atanan bir yönetilen kimliği etkinleştirmek için Azure CLı 'nın nasıl kullanılacağını öğrenirsiniz. Azure Cloud Shell veya yerel bir Azure CLı yüklemesi kullanabilirsiniz. Yerel olarak kullanmak isterseniz, 2.0.68 veya üzeri sürümü gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli-install].

Yönetilen bir kimlik kullanarak ACR görevinden güvenli kaynaklara erişim senaryoları için, bkz.:

* [Çapraz kayıt defteri kimlik doğrulaması](container-registry-tasks-cross-registry-authentication.md)
* [Azure Key Vault depolanan gizli dizileri içeren dış kaynaklara erişin](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Yönetilen kimlik neden kullanılmalıdır?

Azure kaynakları için yönetilen bir kimlik, Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimliğe sahip seçili Azure hizmetlerini sağlar. Görevin, görev adımlarında kimlik bilgilerini geçirmeden diğer güvenli Azure kaynaklarına erişebilmesi için, yönetilen bir kimlikle bir ACR görevi yapılandırabilirsiniz.

Yönetilen kimlikler iki türtür:

* Birden fazla kaynağa atayabileceğiniz ve istediğiniz sürece kalıcı hale getirebilecek *Kullanıcı tarafından atanan kimlikler*. Kullanıcı tarafından atanan kimlikler Şu anda önizlemededir.

* ACR görevi gibi belirli bir kaynak için benzersiz olan ve söz konusu kaynağın kullanım ömrü boyunca bir *sistem tarafından atanan kimlik*.

Bir ACR görevinde kimlik türlerinin birini veya her ikisini de etkinleştirebilirsiniz. Her türlü güvenlik sorumlusu gibi başka bir kaynağa kimlik erişimi verin. Görev çalıştığında, erişim gerektiren herhangi bir görev adımdaki kaynağa erişmek için kimliğini kullanır.

## <a name="steps-to-use-a-managed-identity"></a>Yönetilen kimlik kullanma adımları

Bir ACR göreviyle yönetilen kimlik kullanmak için bu üst düzey adımları izleyin.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. Seçim Kullanıcı tarafından atanan kimlik oluşturma

Kullanıcı tarafından atanan bir kimlik kullanmayı planlıyorsanız, var olan bir kimliği kullanabilirsiniz. Veya Azure CLı veya diğer Azure araçlarını kullanarak kimlik oluşturun. Örneğin, [az Identity Create][az-identity-create] komutunu kullanın. 

Yalnızca sistem tarafından atanan bir kimlik kullanmayı planlıyorsanız, bu adımı atlayın. ACR görevini oluştururken, sistem tarafından atanan bir kimlik oluşturabilirsiniz.

### <a name="2-enable-identity-on-an-acr-task"></a>2. ACR görevinde kimlik etkinleştirme

Bir ACR görevi oluşturduğunuzda, isteğe bağlı olarak Kullanıcı tarafından atanan bir kimlik, sistem tarafından atanan bir kimlik veya her ikisini de etkinleştirin. Örneğin, Azure CLI 'de `--assign-identity` [az ACR Task Create][az-acr-task-create] komutunu çalıştırdığınızda parametresini geçirin.

Sistem tarafından atanan bir kimliği etkinleştirmek için değeri veya `--assign-identity` `assign-identity [system]`olmadan geçirin. Aşağıdaki komut, bir git COMMIT tetikleyicisiyle ve sistem tarafından atanan yönetilen kimlikle `hello-world` görüntüyü oluşturan genel bir GitHub deposundan bir Linux görevi oluşturur:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --assign-identity
```

Kullanıcı tarafından atanan bir kimliği etkinleştirmek için, kimliğin `--assign-identity` *kaynak kimliği* değeriyle geçiş yapın. Aşağıdaki komut, bir git COMMIT tetikleyicisiyle ve Kullanıcı tarafından atanan yönetilen kimlikle `hello-world` görüntüyü oluşturan genel bir GitHub deposundan bir Linux görevi oluşturur:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --assign-identity <resourceID>
```

Daha [az Identity Show][az-identity-show] komutunu çalıştırarak KIMLIğIN kaynak kimliğini alabilirsiniz. *Myresourcegroup* kaynak grubundaki *Myuseratandıdentity* kimliği için kaynak kimliği, formundadır. 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Diğer Azure kaynaklarına erişim için kimlik izinleri verme

Görevin gereksinimlerine bağlı olarak, diğer Azure kaynaklarına erişmek için kimlik izinlerini verin. Örneklere şunlar dahildir:

* Azure 'da bir hedef kapsayıcı kayıt defterine çekme, gönderme ve çekme veya diğer izinlerle yönetilen kimliğe bir rol atayın. Kayıt defteri rollerinin tüm listesi için bkz. [Azure Container Registry rolleri ve izinleri](container-registry-roles.md). 
* Bir Azure anahtar kasasındaki gizli dizileri okumak için yönetilen kimliğe bir rol atayın.

Kaynaklara rol tabanlı erişimi yönetmek için [Azure CLI](../role-based-access-control/role-assignments-cli.md) veya diğer Azure araçlarını kullanın. Örneğin, kimliğe bir rolün kimliğini atamak için [az role atama Create][az-role-assignment-create] komutunu çalıştırın. 

Aşağıdaki örnek, bir kapsayıcı kayıt defterinden çekme izinleri için yönetilen bir kimlik atar. Komut, kimliğin *hizmet sorumlusu kimliğini* ve hedef kayıt DEFTERININ *kaynak kimliğini* belirtir.


```azurecli
az role assignment create --assignee <servicePrincipalID> --scope <registryID> --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. Seçim Göreve kimlik bilgileri ekleyin

Göreviniz başka bir Azure Container Registry 'ye görüntü çeker veya bu kayıt defterine gönderim yaparsanız kimlik doğrulaması için kimlik bilgilerini göreve ekleyin. Görevin kimlik bilgilerini göreve eklemek için [az ACR Task Credential Add][az-acr-task-credential-add] komutunu çalıştırın ve `--use-identity` parametresini geçirin. 

Örneğin, kayıt defteri *targetregistry*ile kimlik doğrulaması yapmak için sistem tarafından atanan bir kimliğin kimlik bilgilerini eklemek için şunu `use-identity [system]`geçirin:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Kayıt defteri *targetregistry*ile kimlik doğrulaması yapmak üzere Kullanıcı tarafından atanan bir kimliğin kimlik bilgilerini eklemek için `use-identity` , kimliğin *istemci kimliği* değeriyle geçirin. Örneğin:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Daha [az Identity Show][az-identity-show] komutunu çalıştırarak KIMLIğIN istemci kimliğini alabilirsiniz. İstemci KIMLIĞI, formun `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`bir GUID 'sidir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir ACR görevinde Kullanıcı tarafından atanan veya sistem tarafından atanan bir yönetilen kimliğin nasıl etkinleştirileceğini ve kullanılacağını öğrendiniz. Yönetilen bir kimlik kullanarak ACR görevinden güvenli kaynaklara erişim senaryoları için, bkz.:

* [Çapraz kayıt defteri kimlik doğrulaması](container-registry-tasks-cross-registry-authentication.md)
* [Azure Key Vault depolanan gizli dizileri içeren dış kaynaklara erişin](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
