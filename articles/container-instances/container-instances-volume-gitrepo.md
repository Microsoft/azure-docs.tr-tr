---
title: GitRepo birimi bağlama Azure Container Instances
description: Bir Git deposunu kapsayıcı örneklerinizi kopyalamak için gitRepo birimi bağlama hakkında bilgi edinin
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 06/15/2018
ms.author: danlep
ms.openlocfilehash: e8afa9e14941920cdcfb984e6660bdc666240716
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325435"
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
| `repository` | Evet | Klonlanacak git deposunun dahil `http://` olduğu `https://`tam URL.|
| `directory` | Hayır | Deponun klonlanacak dizin. Yol, "`..`" ile içermemelidir veya başlamamalıdır.  "`.`" Belirtirseniz, depo birimin dizinine kopyalanır. Aksi takdirde, git deposu birim dizini içindeki verilen adın alt dizinine kopyalanır. |
| `revision` | Hayır | Klonlanacak düzeltmenin COMMIT karması. Belirtilmemişse, `HEAD` düzeltme klonlanır. |

## <a name="mount-gitrepo-volume-azure-cli"></a>GitRepo birimi bağlama: Azure CLI

[Azure CLI](/cli/azure)ile kapsayıcı örnekleri dağıtırken bir gitRepo birimi bağlamak için, `--gitrepo-url` [az Container Create][az-container-create] komutuna ve `--gitrepo-mount-path` parametrelerini girin. İsteğe bağlı olarak, kopyalanacak birimin içindeki dizini (`--gitrepo-dir`) ve klonlanacak düzeltmenin COMMIT karmasını (`--gitrepo-revision`) belirtebilirsiniz.

Bu örnek komut, Microsoft [aci-HelloWorld][aci-helloworld] örnek uygulamasını kapsayıcı örneğinde `/mnt/aci-helloworld` içine kopyalar:

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

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>GitRepo birimi bağlama: Resource Manager

Bir [Azure Resource Manager şablonuyla](/azure/templates/microsoft.containerinstance/containergroups)kapsayıcı örnekleri dağıtırken bir gitRepo birimi bağlamak için, önce `volumes` dizinin kapsayıcı grubu `properties` bölümünde diziyi doldurun. Ardından, *gitRepo* birimini bağlamak istediğiniz kapsayıcı grubundaki her bir kapsayıcı için, `volumeMounts` diziyi `properties` kapsayıcı tanımının bölümünde doldurun.

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

Örneğin, özel bir GitHub deposu `--gitrepo-url` için Azure CLI parametresi aşağıdakine benzer şekilde görünür (burada "gituser" GitHub kullanıcı adıdır ve "abcdef1234fdsa4321abcdef" kullanıcının kişisel erişim belirtecidir):

```azurecli
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Azure Repos git deposu için, herhangi bir Kullanıcı adı belirtin (aşağıdaki örnekte olduğu gibi "azurereposuser" öğesini) geçerli bir PAT ile birlikte kullanabilirsiniz:

```azurecli
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

GitHub ve Azure Repos için kişisel erişim belirteçleri hakkında daha fazla bilgi için aşağıdakilere bakın:

GitHub [Komut satırı için bir kişisel erişim belirteci oluşturma][pat-github]

Azure Repos: [Erişim kimliğini doğrulamak için kişisel erişim belirteçleri oluşturma][pat-repos]

## <a name="next-steps"></a>Sonraki adımlar

Azure Container Instances diğer birim türlerini nasıl bağlayacağınızı öğrenin:

* [Azure kapsayıcı durumlarda bir Azure dosya paylaşımını bağlama](container-instances-volume-azure-files.md)
* [Azure kapsayıcı durumlarda emptyDir birim](container-instances-volume-emptydir.md)
* [Azure Container Instances bir gizli birimi bağlama](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
