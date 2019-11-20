---
title: Öğretici-Azure Container Instances-YAML 'de çok kapsayıcılı bir grup dağıtma
description: Bu öğreticide, Azure CLı ile bir YAML dosyası kullanarak Azure Container Instances birden çok kapsayıcılı bir kapsayıcı grubunu dağıtmayı öğreneceksiniz.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.openlocfilehash: a38b0cfe7072975e4bcaf61b65ab7733694f714c
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178558"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Öğretici: YAML dosyası kullanarak çok kapsayıcılı bir grup dağıtma

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Azure Container Instances, [kapsayıcı grubu](container-instances-container-groups.md)kullanılarak tek bir konakta birden fazla kapsayıcının dağıtımını destekler. Bir kapsayıcı grubu, günlüğe kaydetme, izleme veya bir hizmetin ikinci bağlı bir işleme ihtiyacı olan başka herhangi bir yapılandırma için bir uygulama arabası oluşturulması durumunda faydalıdır.

Bu öğreticide, Azure CLı kullanarak bir [YAML dosyası](container-instances-reference-yaml.md) dağıtarak basit bir iki Kapsayıcılı sepet yapılandırması çalıştırmanın adımlarını takip edersiniz. YAML dosyası, örnek ayarlarını belirtmek için kısa bir biçim sağlar. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * YAML dosyası yapılandırma
> * Kapsayıcı grubunu dağıtma
> * Kapsayıcıların günlüklerini görüntüleme

> [!NOTE]
> Çok Kapsayıcılı gruplar Şu anda Linux kapsayıcılarıyla kısıtlıdır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>YAML dosyası yapılandırma

Azure CLı 'de [az Container Create][az-container-create] komutuyla çok kapsayıcılı bir grup dağıtmak için, BIR YAML dosyasında kapsayıcı grubu yapılandırmasını belirtmeniz gerekir. Ardından, YAML dosyasını komuta bir parametre olarak geçirin.

Aşağıdaki YAML 'yi **Deploy-aci. YAML**adlı yeni bir dosyaya kopyalayarak başlayın. Azure Cloud Shell, çalışma dizininizde dosyayı oluşturmak için Visual Studio Code kullanabilirsiniz:

```
code deploy-aci.yaml
```

Bu YAML dosyası, iki kapsayıcı, genel IP adresi ve iki açığa çıkarılan bağlantı noktası içeren "myContainerGroup" adlı bir kapsayıcı grubunu tanımlar. Kapsayıcılar ortak Microsoft görüntülerinden dağıtılır. Gruptaki ilk kapsayıcı, internet 'e yönelik bir Web uygulaması çalıştırır. İkinci kapsayıcı olan sepet, düzenli aralıklarla, kapsayıcı grubunun yerel ağı aracılığıyla ilk kapsayıcıda çalışan Web uygulamasına HTTP istekleri sağlar.

```YAML
apiVersion: 2018-10-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Özel bir kapsayıcı görüntüsü kayıt defteri kullanmak için, `imageRegistryCredentials` özelliği, ortamınız için değiştirilen değerlerle birlikte kapsayıcı grubuna ekleyin:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Kapsayıcı grubunu dağıtma

[Az Group Create][az-group-create] komutuyla bir kaynak grubu oluşturun:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Bir bağımsız değişken olarak YAML dosyasını geçirerek, [az Container Create][az-container-create] komutuyla kapsayıcı grubunu dağıtın:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

Birkaç saniye içinde Azure’dan bir ilk yanıt almanız gerekir.

## <a name="view-deployment-state"></a>Dağıtım durumunu görüntüle

Dağıtımın durumunu görüntülemek için, aşağıdaki [az Container Show][az-container-show] komutunu kullanın:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Çalışan uygulamayı görüntülemek isterseniz, tarayıcınızda IP adresine gidin. Örneğin, IP Bu örnek çıktıdır `52.168.26.124` :

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Kapsayıcı günlüklerini görüntüle

[Az Container logs][az-container-logs] komutunu kullanarak bir kapsayıcının günlük çıktısını görüntüleyin. `--container-name` Bağımsız değişkeni, günlüklerin alınacağı kapsayıcıyı belirtir. Bu örnekte, `aci-tutorial-app` kapsayıcı belirtilir.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Çıktı:

```console
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Sepet kapsayıcısının günlüklerini görmek için `aci-tutorial-sidecar` kapsayıcıyı belirten benzer bir komut çalıştırın.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Çıktı:

```console
Every 3s: curl -I http://localhost                          2019-03-21 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 21 Mar 2019 20:36:41 GMT
Connection: keep-alive
```

Gördüğünüz gibi, sepet, çalıştığından emin olmak için grubun yerel ağı aracılığıyla ana Web uygulamasına bir HTTP isteği düzenli olarak yapar. Bu sepet örneği, dışında bir http yanıt kodu `200 OK`aldıysa bir uyarı tetiklemek için genişletilebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Container Instances içinde çok kapsayıcılı bir grubu dağıtmak için bir YAML dosyası kullandınız. Şunları öğrendiniz:

> [!div class="checklist"]
> * Çok kapsayıcılı bir grup için bir YAML dosyası yapılandırma
> * Kapsayıcı grubunu dağıtma
> * Kapsayıcıların günlüklerini görüntüleme

Ayrıca, [Kaynak Yöneticisi şablonu](container-instances-multi-container-group.md)kullanarak çok kapsayıcılı bir grup belirtebilirsiniz. Bir Kaynak Yöneticisi şablonu, kapsayıcı grubuyla ek Azure hizmet kaynakları dağıtmanız gerektiğinde senaryolar için kolay bir şekilde uyarlanmıştır.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
