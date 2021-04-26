---
title: Gizli birimi kapsayıcı grubuna bağla
description: Gizli bilgileri kapsayıcı örneklerinizin erişimine yönelik olarak depolamak üzere nasıl bağlayacağınızı öğrenin
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: cd8bd4d59b5e53a0db2455bdfbaf56c05c93d65f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771009"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Azure Container Instances bir gizli birimi bağlama

Bir kapsayıcı grubundaki kapsayıcılara hassas bilgiler sağlamak için *gizli* bir birim kullanın. *Gizli* birim, saklama grubundaki kapsayıcılar tarafından erişilebilen, gizli dizilerinizi birim içindeki dosyalarda depolar. *Gizli dizileri gizli* bir birimde depolayarak, uygulama kodunuza SSH anahtarları veya veritabanı kimlik bilgileri gibi hassas veriler eklemekten kaçınabilirsiniz.

* Bir kapsayıcı grubundaki gizli anahtarlarla dağıtıldıktan sonra gizli bir birim *salt okunurdur*.
* Tüm gizli birimler, RAM ile desteklenen bir dosya sistemi olan [tmpfs][tmpfs]tarafından desteklenir; içerikleri hiçbir şekilde geçici olmayan depolamaya yazılmaz.

> [!NOTE]
> *Gizli* birimler Şu anda Linux kapsayıcılarıyla kısıtlıdır. [Ortam değişkenlerini ayarla](container-instances-environment-variables.md)bölümünde hem Windows hem de Linux kapsayıcıları için güvenli ortam değişkenlerini nasıl geçirebileceğinizi öğrenin. Tüm özellikleri Windows kapsayıcılarına getirmek için çalışıyoruz, ancak geçerli platform farklarını [genel bakışta](container-instances-overview.md#linux-and-windows-containers)bulabilirsiniz.

## <a name="mount-secret-volume---azure-cli"></a>Bağlama gizli birimi-Azure CLı

Azure CLı kullanarak bir veya daha fazla gizli dizi ile bir kapsayıcı dağıtmak için `--secrets` `--secrets-mount-path` [az Container Create][az-container-create] komutuna ve parametrelerini dahil edin. Bu örnek, "mysecret1" ve "mysecret2" gibi gizli dizileri içeren iki dosyadan oluşan *gizli* bir birimi bağlar `/mnt/secrets` :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

Aşağıdaki [az Container exec][az-container-exec] çıkışı, çalışan kapsayıcıda bir kabuğu açmayı, gizli birimde dosyaları listelemeyi ve sonra içeriklerini görüntülemeyi gösterir:

```azurecli
az container exec \
  --resource-group myResourceGroup \
  --name secret-volume-demo --exec-command "/bin/sh"
```

```output
/usr/src/app # ls /mnt/secrets
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

Aşağıdaki YAML şablonu, üzerinde *gizli* bir birim bağlayan bir kapsayıcı grubunu tanımlar `/mnt/secrets` . Gizli birimde, "mysecret1" ve "mysecret2" gizli dizileri içeren iki dosya vardır.

```yaml
apiVersion: '2019-12-01'
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

YAML şablonuyla dağıtmak için, önceki YAML 'yi adlı bir dosyaya kaydedin `deploy-aci.yaml` ve ardından parametresiyle [az Container Create][az-container-create] komutunu yürütün `--file` :

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Bağlama gizli birimi-Kaynak Yöneticisi

CLı ve YAML dağıtımına ek olarak, bir Azure [Kaynak Yöneticisi şablonu](/azure/templates/microsoft.containerinstance/containergroups)kullanarak bir kapsayıcı grubu dağıtabilirsiniz.

Önce, `volumes` diziyi şablonun kapsayıcı grubu `properties` bölümünde doldurun. Bir Kaynak Yöneticisi şablonuyla dağıtırken, gizli değerler şablonda **Base64 kodlamalı** olmalıdır. Ancak, gizli değerler kapsayıcıdaki dosyalar içinde düz metin olarak görünür.

Ardından, kapsayıcı grubundaki, *gizli* birimi bağlamak istediğiniz her bir kapsayıcı için, `volumeMounts` diziyi `properties` kapsayıcı tanımının bölümünde doldurun.

Aşağıdaki Kaynak Yöneticisi şablonu, üzerinde *gizli* bir birim bağlayan bir kapsayıcı grubunu tanımlar `/mnt/secrets` . Gizli birimin iki parolası vardır, "mysecret1" ve "mysecret2."

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Kaynak Yöneticisi şablonuyla dağıtmak için, önceki JSON 'ı adlı bir dosyaya kaydedin `deploy-aci.json` , ardından şu parametreyle [az Deployment Group Create][az-deployment-group-create] komutunu yürütün `--template-file` :

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>Sonraki adımlar

### <a name="volumes"></a>Birimler

Azure Container Instances diğer birim türlerini nasıl bağlayacağınızı öğrenin:

* [Azure Container Instances'ta Azure dosya paylaşımı bağlama](container-instances-volume-azure-files.md)
* [Azure Container Instances bir emptyDir birimi bağlama](container-instances-volume-emptydir.md)
* [Azure Container Instances bir gitRepo birimi bağlama](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Güvenli ortam değişkenleri

Kapsayıcılara hassas bilgiler sağlamaya yönelik başka bir Yöntem (Windows kapsayıcıları dahil), [Güvenli ortam değişkenlerinin](container-instances-environment-variables.md#secure-values)kullanımı üzerinden yapılır.

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
