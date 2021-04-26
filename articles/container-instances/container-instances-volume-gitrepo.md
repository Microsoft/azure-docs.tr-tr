---
title: GitRepo birimini kapsayıcı grubuna bağlama
description: Bir Git deposunu kapsayıcı örneklerinizi kopyalamak için gitRepo birimi bağlama hakkında bilgi edinin
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 7c1249e3120dd680c52bf74fb045bedf5202b9f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763730"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Azure Container Instances bir gitRepo birimi bağlama

Bir Git deposunu kapsayıcı örneklerinizi kopyalamak için bir *gitRepo* birimi bağlama hakkında bilgi edinin.

> [!NOTE]
> *GitRepo* birimi bağlamak Şu anda Linux kapsayıcılarıyla kısıtlıdır. Tüm özellikleri Windows kapsayıcılarına getirmek için çalıştık, ancak geçerli platform farklarını [genel bakışta](container-instances-overview.md#linux-and-windows-containers)bulabilirsiniz.

## <a name="gitrepo-volume"></a>gitRepo birimi

*GitRepo* birimi bir dizini takar ve belirtilen Git deposunu kapsayıcı başlangıcında buna kopyalar. Container Instances içinde bir *gitRepo* birimi kullanarak, uygulamalarınızda bunu gerçekleştirmek için kod eklemekten kaçınabilirsiniz.

Bir *gitRepo* birimi bağladığınızda, birimi yapılandırmak için üç özellik belirleyebilirsiniz:

| Özellik | Gerekli | Açıklama |
| -------- | -------- | ----------- |
| `repository` | Evet | `http://` `https://` Klonlanacak git deposunun dahil olduğu tam URL.|
| `directory` | No | Deponun klonlanacak dizin. Yol, "" ile içermemelidir veya başlamamalıdır `..` .  " `.` " Belirtirseniz, depo birimin dizinine kopyalanır. Aksi takdirde, git deposu birim dizini içindeki verilen adın alt dizinine kopyalanır. |
| `revision` | No | Klonlanacak düzeltmenin COMMIT karması. Belirtilmemişse, `HEAD` Düzeltme klonlanır. |

## <a name="mount-gitrepo-volume-azure-cli"></a>GitRepo Volume bağlama: Azure CLı

[Azure CLI](/cli/azure)ile kapsayıcı örnekleri dağıtırken bir gitRepo birimi bağlamak için, `--gitrepo-url` `--gitrepo-mount-path` [az Container Create][az-container-create] komutuna ve parametrelerini girin. İsteğe bağlı olarak, kopyalanacak birimin içindeki dizini ( `--gitrepo-dir` ) ve klonlanacak düzeltmenin COMMIT karmasını ( `--gitrepo-revision` ) belirtebilirsiniz.

Bu örnek komut, Microsoft [aci-HelloWorld][aci-helloworld] örnek uygulamasını `/mnt/aci-helloworld` kapsayıcı örneğinde içine kopyalar:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

GitRepo biriminin takıldığını doğrulamak için [az Container exec][az-container-exec] ile kapsayıcıda bir Shell başlatın ve dizini listeleyin:

```azurecli
az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
```

```output
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Bağlama gitRepo birimi: Kaynak Yöneticisi

Bir [Azure Resource Manager şablonuyla](/azure/templates/microsoft.containerinstance/containergroups)kapsayıcı örnekleri dağıtırken bir gitRepo birimi bağlamak için, önce `volumes` dizinin kapsayıcı grubu bölümünde diziyi doldurun `properties` . Ardından, *gitRepo* birimini bağlamak istediğiniz kapsayıcı grubundaki her bir kapsayıcı için, `volumeMounts` diziyi `properties` kapsayıcı tanımının bölümünde doldurun.

Örneğin, aşağıdaki Kaynak Yöneticisi şablonu tek bir kapsayıcıdan oluşan bir kapsayıcı grubu oluşturur. Kapsayıcı, *gitRepo* birim blokları tarafından belirtilen iki GitHub deposu klonlar. İkinci birim, kopyalamak üzere bir dizin belirten ek özellikler ve kopyalamak için belirli bir düzeltmenin COMMIT karmasını içerir.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Önceki şablonda tanımlanan iki kopyalanmış deponun elde edilen dizin yapısı:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Azure Resource Manager şablonuyla kapsayıcı örneği dağıtımına bir örnek görmek için bkz. [Azure Container Instances birden çok Kapsayıcılı grupları dağıtma](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Özel Git deposu kimlik doğrulaması

Özel bir git deposu için bir gitRepo birimi bağlamak üzere depo URL 'sinde kimlik bilgilerini belirtin. Genellikle, kimlik bilgileri, depoya kapsamlı erişim veren bir Kullanıcı adı ve kişisel erişim belirteci (PAT) biçimindedir.

Örneğin, `--gitrepo-url` özel bir GitHub deposu Için Azure CLI parametresi aşağıdakine benzer şekilde görünür (burada "gituser" GitHub kullanıcı adıdır ve "abcdef1234fdsa4321abcdef" kullanıcının kişisel erişim belirtecidir):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Azure Repos git deposu için, herhangi bir Kullanıcı adı belirtin (aşağıdaki örnekte olduğu gibi "azurereposuser" öğesini) geçerli bir PAT ile birlikte kullanabilirsiniz:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

GitHub ve Azure Repos için kişisel erişim belirteçleri hakkında daha fazla bilgi için aşağıdakilere bakın:

GitHub: [komut satırı için bir kişisel erişim belirteci oluşturma][pat-github]

Azure Repos: [erişim kimliğini doğrulamak için kişisel erişim belirteçleri oluşturun][pat-repos]

## <a name="next-steps"></a>Sonraki adımlar

Azure Container Instances diğer birim türlerini nasıl bağlayacağınızı öğrenin:

* [Azure Container Instances'ta Azure dosya paylaşımı bağlama](container-instances-volume-azure-files.md)
* [Azure Container Instances bir emptyDir birimi bağlama](container-instances-volume-emptydir.md)
* [Azure Container Instances bir gizli birimi bağlama](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: /azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
