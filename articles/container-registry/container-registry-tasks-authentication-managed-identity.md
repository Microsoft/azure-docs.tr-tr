---
title: ACR görevinde yönetilen kimlik
description: Görevin diğer özel kapsayıcı kayıt defterleri dahil diğer Azure kaynaklarına erişmesine izin vermek için bir Azure Container Registry görevindeki Azure kaynakları için yönetilen bir kimliği etkinleştirin.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: 19d63861a98884ff2f5103946c19e2226c4b14b7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781172"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>ACR görevlerinde Azure tarafından yönetilen bir kimlik kullanma 

Bir [ACR görevinde](container-registry-tasks-overview.md) [Azure kaynakları için yönetilen bir kimliği](../active-directory/managed-identities-azure-resources/overview.md) etkinleştirin, bu nedenle görev, kimlik bilgilerini sağlamaya veya yönetmeye gerek kalmadan diğer Azure kaynaklarına erişebilir. Örneğin, bir görev adımının kapsayıcı görüntülerini başka bir kayıt defterine çekme veya gönderme için bir yönetilen kimlik kullanın.

Bu makalede, bir ACR görevinde Kullanıcı tarafından atanan veya sistem tarafından atanan bir yönetilen kimliği etkinleştirmek için Azure CLı 'nın nasıl kullanılacağını öğrenirsiniz. Azure Cloud Shell veya yerel bir Azure CLı yüklemesi kullanabilirsiniz. Yerel olarak kullanmak isterseniz, 2.0.68 veya üzeri sürümü gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

Çizim amacıyla, bu makaledeki örnek komutlar, yönetilen bir kimlik sağlayan temel bir görüntü oluşturma görevi oluşturmak için [az ACR Task Create][az-acr-task-create] komutunu kullanır. Yönetilen bir kimlik kullanarak ACR görevinden güvenli kaynaklara erişmek için örnek senaryolar için bkz.:

* [Kayıt defterleri arasında kimlik doğrulaması](container-registry-tasks-cross-registry-authentication.md)
* [Azure Key Vault depolanan gizli dizileri içeren dış kaynaklara erişin](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Yönetilen kimlik neden kullanılmalıdır?

Azure kaynakları için yönetilen bir kimlik, Azure Active Directory ' de otomatik olarak yönetilen bir kimlikle seçili Azure hizmetlerini sağlar. Görevin, görev adımlarında kimlik bilgilerini geçirmeden diğer güvenli Azure kaynaklarına erişebilmesi için, yönetilen bir kimlikle bir ACR görevi yapılandırabilirsiniz.

Yönetilen kimlikler iki türtür:

* Birden fazla kaynağa atayabileceğiniz ve istediğiniz sürece kalıcı hale getirebilecek *Kullanıcı tarafından atanan kimlikler*. Kullanıcı tarafından atanan kimlikler Şu anda önizlemededir.

* ACR görevi gibi belirli bir kaynak için benzersiz olan ve söz konusu kaynağın kullanım ömrü boyunca bir *sistem tarafından atanan kimlik*.

Bir ACR görevinde kimlik türlerinin birini veya her ikisini de etkinleştirebilirsiniz. Her türlü güvenlik sorumlusu gibi başka bir kaynağa kimlik erişimi verin. Görev çalıştığında, erişim gerektiren herhangi bir görev adımdaki kaynağa erişmek için kimliğini kullanır.

## <a name="steps-to-use-a-managed-identity"></a>Yönetilen kimlik kullanma adımları

Bir ACR göreviyle yönetilen kimlik kullanmak için bu üst düzey adımları izleyin.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (isteğe bağlı) Kullanıcı tarafından atanan kimlik oluşturma

Kullanıcı tarafından atanan bir kimlik kullanmayı planlıyorsanız, var olan bir kimliği kullanın veya Azure CLı veya diğer Azure araçlarını kullanarak kimlik oluşturun. Örneğin, [az Identity Create][az-identity-create] komutunu kullanın. 

Yalnızca sistem tarafından atanan bir kimlik kullanmayı planlıyorsanız, bu adımı atlayın. ACR görevini oluştururken sistem tarafından atanan bir kimlik oluşturursunuz.

### <a name="2-enable-identity-on-an-acr-task"></a>2. bir ACR görevinde kimliği etkinleştirin

Bir ACR görevi oluşturduğunuzda, isteğe bağlı olarak Kullanıcı tarafından atanan bir kimlik, sistem tarafından atanan bir kimlik veya her ikisini de etkinleştirin. Örneğin, `--assign-identity` Azure CLI 'de [az ACR Task Create][az-acr-task-create] komutunu çalıştırdığınızda parametresini geçirin.

Sistem tarafından atanan bir kimliği etkinleştirmek için `--assign-identity` değeri veya olmadan geçirin `assign-identity [system]` . Aşağıdaki örnek komut, `hello-world` görüntüyü oluşturan ve sistem tarafından atanan bir yönetilen kimlik sağlayan ortak bir GitHub deposundan bir Linux görevi oluşturur:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Kullanıcı tarafından atanan bir kimliği etkinleştirmek için, `--assign-identity` kimliğin *kaynak kimliği* değeriyle geçiş yapın. Aşağıdaki örnek komut, `hello-world` görüntüyü oluşturan ve Kullanıcı tarafından atanan yönetilen bir kimlik sağlayan ortak bir GitHub deposundan bir Linux görevi oluşturur:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

Daha [az Identity Show][az-identity-show] komutunu çalıştırarak KIMLIğIN kaynak kimliğini alabilirsiniz. *Myresourcegroup* kaynak grubundaki *Myuseratandıdentity* kimliği için kaynak kimliği şu biçimdedir: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. diğer Azure kaynaklarına erişmek için kimlik izinleri verin

Görevin gereksinimlerine bağlı olarak, diğer Azure kaynaklarına erişmek için kimlik izinlerini verin. Örnekler arasında şunlar yer almaktadır:

* Azure 'da bir hedef kapsayıcı kayıt defterine çekme, gönderme ve çekme veya diğer izinlerle yönetilen kimliğe bir rol atayın. Kayıt defteri rollerinin tüm listesi için bkz. [Azure Container Registry rolleri ve izinleri](container-registry-roles.md). 
* Bir Azure anahtar kasasındaki gizli dizileri okumak için yönetilen kimliğe bir rol atayın.

Kaynaklara rol tabanlı erişimi yönetmek için [Azure CLI](../role-based-access-control/role-assignments-cli.md) veya diğer Azure araçlarını kullanın. Örneğin, kaynağa bir rolün kimliğini atamak için [az role atama Create][az-role-assignment-create] komutunu çalıştırın. 

Aşağıdaki örnek, bir kapsayıcı kayıt defterinden çekme izinleri için yönetilen bir kimlik atar. Komut, görev kimliği ve hedef kayıt defterinin *kaynak kimliği* IÇIN *sorumlu kimliği* belirtir.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (isteğe bağlı) göreve kimlik bilgileri ekleyin

Görevin başka bir özel kayıt defterine görüntü çekmek veya göndermek için kimlik bilgileri gerekiyorsa veya diğer kaynaklara erişmek için, göreve kimlik bilgileri ekleyin. Kimlik bilgilerini eklemek için [az ACR Task Credential Add][az-acr-task-credential-add] komutunu çalıştırın ve `--use-identity` kimliğin kimlik bilgilerine erişemeyeceğini belirtmek için parametresini geçirin. 

Örneğin, Azure Container Registry *targetregistry* ile kimlik doğrulaması yapmak için sistem tarafından atanan bir kimliğin kimlik bilgilerini eklemek için şunu geçirin `use-identity [system]` :

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Kayıt defteri *targetregistry* ile kimlik doğrulaması yapmak üzere Kullanıcı tarafından atanan bir kimliğin kimlik bilgilerini eklemek için, `use-identity` kimliğin *istemci kimliği* değeriyle geçirin. Örnek:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Daha [az Identity Show][az-identity-show] komutunu çalıştırarak KIMLIğIN istemci kimliğini alabilirsiniz. İstemci KIMLIĞI, formun bir GUID 'sidir `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` .

### <a name="5-run-the-task"></a>5. görevi çalıştırın

Yönetilen kimliğe sahip bir görevi yapılandırdıktan sonra, görevi çalıştırın. Örneğin, bu makalede oluşturulan görevlerden birini test etmek için [az ACR Task Run][az-acr-task-run] komutunu kullanarak el ile tetikleyin. Ek, otomatik görev Tetikleyicileri yapılandırdıysanız, görev otomatik olarak tetiklendiğinde çalışır.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir ACR görevinde Kullanıcı tarafından atanan veya sistem tarafından atanan bir yönetilen kimliğin nasıl etkinleştirileceğini ve kullanılacağını öğrendiniz. Yönetilen bir kimlik kullanarak ACR görevinden güvenli kaynaklara erişim senaryoları için, bkz.:

* [Kayıt defterleri arasında kimlik doğrulaması](container-registry-tasks-cross-registry-authentication.md)
* [Azure Key Vault depolanan gizli dizileri içeren dış kaynaklara erişin](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[azure-cli-install]: /cli/azure/install-azure-cli
