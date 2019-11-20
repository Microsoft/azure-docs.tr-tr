---
title: Azure Container Instances bir gizli birimi bağlama
description: Gizli bilgileri kapsayıcı örneklerinizin erişimine yönelik olarak depolamak üzere nasıl bağlayacağınızı öğrenin
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: danlep
ms.openlocfilehash: 2e96ef73c3ff89fd7941fa14a8a1e53e6d4d8593
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68325421"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Azure Container Instances bir gizli birimi bağlama

Bir kapsayıcı grubundaki kapsayıcılara hassas bilgiler sağlamak için *gizli* bir birim kullanın. *Gizli* birim, saklama grubundaki kapsayıcılar tarafından erişilebilen, gizli dizilerinizi birim içindeki dosyalarda depolar. *Gizli dizileri gizli* bir birimde depolayarak, uygulama kodunuza SSH anahtarları veya veritabanı kimlik bilgileri gibi hassas veriler eklemekten kaçınabilirsiniz.

Tüm *gizli* BIRIMLER, RAM ile desteklenen bir dosya sistemi olan [tmpfs][tmpfs]tarafından desteklenir; içerikleri hiçbir şekilde geçici olmayan depolamaya yazılmaz.

> [!NOTE]
> *Gizli* birimler Şu anda Linux kapsayıcılarıyla kısıtlıdır. [Ortam değişkenlerini ayarla](container-instances-environment-variables.md)bölümünde hem Windows hem de Linux kapsayıcıları için güvenli ortam değişkenlerini nasıl geçirebileceğinizi öğrenin. Tüm özellikleri Windows kapsayıcılarına getirmek için çalışıyoruz, ancak geçerli platform farklarını [genel bakışta](container-instances-overview.md#linux-and-windows-containers)bulabilirsiniz.

## <a name="mount-secret-volume---azure-cli"></a>Bağlama gizli birimi-Azure CLı

Azure CLI kullanarak bir veya daha fazla gizli dizi ile bir kapsayıcı dağıtmak için `--secrets` [az Container Create][az-container-create] komutuna `--secrets-mount-path` ve parametrelerini dahil edin. Bu örnek, " mysecret1" ve "mysecret2 `/mnt/secrets`" gibi iki gizli dizi içeren gizli bir birimi bağlar:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

Aşağıdaki [az Container exec][az-container-exec] çıkışı, çalışan kapsayıcıda bir kabuğu açmayı, gizli birimde dosyaları listelemeyi ve sonra içeriklerini görüntülemeyi gösterir:

```console
$ az container exec --resource-group myResourceGroup --name secret-volume-demo --exec-command "/bin/sh"
/usr/src/app # ls -1 /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Bağlama gizli birimi-YAML

Ayrıca, Azure CLı ve bir [YAML şablonuyla](container-instances-multi-container-yaml.md)kapsayıcı grupları da dağıtabilirsiniz. YAML şablonuna göre dağıtmak, birden çok kapsayıcıdan oluşan kapsayıcı grupları dağıtıldığında tercih edilen yöntemdir.

Bir YAML şablonuyla dağıtırken, gizli değerlerin şablonda **Base64 kodlamalı** olması gerekir. Ancak, gizli değerler kapsayıcıdaki dosyalar içinde düz metin olarak görünür.

Aşağıdaki YAML şablonu, üzerinde `/mnt/secrets` *gizli* bir birim bağlayan bir kapsayıcı grubunu tanımlar. Gizli birimin iki parolası vardır, "mysecret1" ve "mysecret2."

```yaml
apiVersion: '2018-10-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

YAML şablonuyla dağıtmak için, önceki YAML 'yi adlı `deploy-aci.yaml`bir dosyaya kaydedin ve ardından `--file` parametresiyle [az Container Create][az-container-create] komutunu yürütün:

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Bağlama gizli birimi-Kaynak Yöneticisi

CLı ve YAML dağıtımına ek olarak, bir Azure [Kaynak Yöneticisi şablonu](/azure/templates/microsoft.containerinstance/containergroups)kullanarak bir kapsayıcı grubu dağıtabilirsiniz.

Önce, `volumes` diziyi şablonun kapsayıcı grubu `properties` bölümünde doldurun. Bir Kaynak Yöneticisi şablonuyla dağıtırken, gizli değerler şablonda **Base64 kodlamalı** olmalıdır. Ancak, gizli değerler kapsayıcıdaki dosyalar içinde düz metin olarak görünür.

Ardından, kapsayıcı grubundaki, *gizli* birimi bağlamak istediğiniz her bir kapsayıcı için, `volumeMounts` diziyi `properties` kapsayıcı tanımının bölümünde doldurun.

Aşağıdaki Kaynak Yöneticisi şablonu, üzerinde `/mnt/secrets` *gizli* bir birim bağlayan bir kapsayıcı grubunu tanımlar. Gizli birimin iki parolası vardır, "mysecret1" ve "mysecret2."

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Kaynak Yöneticisi şablonuyla dağıtmak için, önceki JSON 'ı adlı `deploy-aci.json`bir dosyaya kaydedin, sonra [az Group Deployment Create][az-group-deployment-create] komutunu `--template-file` parametresiyle yürütün:

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>Sonraki adımlar

### <a name="volumes"></a>Birimler

Azure Container Instances diğer birim türlerini nasıl bağlayacağınızı öğrenin:

* [Azure kapsayıcı durumlarda bir Azure dosya paylaşımını bağlama](container-instances-volume-azure-files.md)
* [Azure kapsayıcı durumlarda emptyDir birim](container-instances-volume-emptydir.md)
* [Azure Container Instances bir gitRepo birimi bağlama](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Güvenli ortam değişkenleri

Kapsayıcılara hassas bilgiler sağlamaya yönelik başka bir Yöntem (Windows kapsayıcıları dahil), [Güvenli ortam değişkenlerinin](container-instances-environment-variables.md#secure-values)kullanımı üzerinden yapılır.

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
