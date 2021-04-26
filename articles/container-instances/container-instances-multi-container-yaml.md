---
title: Öğretici-çok Kapsayıcılı grup dağıtma-YAML
description: Bu öğreticide, Azure CLı ile bir YAML dosyası kullanarak Azure Container Instances birden çok kapsayıcılı bir kapsayıcı grubunu dağıtmayı öğreneceksiniz.
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 74269440357ee2d7ae36661618a31293346fa712
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771272"
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

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-yaml-file"></a>YAML dosyası yapılandırma

Azure CLı 'de [az Container Create][az-container-create] komutuyla çok kapsayıcılı bir grup dağıtmak için, BIR YAML dosyasında kapsayıcı grubu yapılandırmasını belirtmeniz gerekir. Ardından, YAML dosyasını komuta bir parametre olarak geçirin.

Aşağıdaki YAML 'yi **Deploy-aci. YAML** adlı yeni bir dosyaya kopyalayarak başlayın. Azure Cloud Shell, çalışma dizininizde dosyayı oluşturmak için Visual Studio Code kullanabilirsiniz:

```
code deploy-aci.yaml
```

Bu YAML dosyası, iki kapsayıcı, genel IP adresi ve iki açığa çıkarılan bağlantı noktası içeren "myContainerGroup" adlı bir kapsayıcı grubunu tanımlar. Kapsayıcılar ortak Microsoft görüntülerinden dağıtılır. Gruptaki ilk kapsayıcı, internet 'e yönelik bir Web uygulaması çalıştırır. İkinci kapsayıcı olan sepet, düzenli aralıklarla, kapsayıcı grubunun yerel ağı aracılığıyla ilk kapsayıcıda çalışan Web uygulamasına HTTP istekleri sağlar.

```YAML
apiVersion: 2019-12-01
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
      port: 80
    - protocol: tcp
      port: 8080
tags: {exampleTag: tutorial}
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

Çalışan uygulamayı görüntülemek isterseniz, tarayıcınızda IP adresine gidin. Örneğin, IP `52.168.26.124` Bu örnek çıktıdır:

```console
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Kapsayıcı günlüklerini görüntüleme

[Az Container logs][az-container-logs] komutunu kullanarak bir kapsayıcının günlük çıktısını görüntüleyin. `--container-name`Bağımsız değişkeni, günlüklerin alınacağı kapsayıcıyı belirtir. Bu örnekte, `aci-tutorial-app` kapsayıcı belirtilir.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Çıkış:

```console
listening on port 80
::1 - - [02/Jul/2020:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Sepet kapsayıcısının günlüklerini görmek için kapsayıcıyı belirten benzer bir komut çalıştırın `aci-tutorial-sidecar` .

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Çıkış:

```console
Every 3s: curl -I http://localhost                          2020-07-02 20:36:41

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
Date: Thu, 02 Jul 2020 20:36:41 GMT
Connection: keep-alive
```

Gördüğünüz gibi, sepet, çalıştığından emin olmak için grubun yerel ağı aracılığıyla ana Web uygulamasına bir HTTP isteği düzenli olarak yapar. Bu sepet örneği, dışında bir http yanıt kodu aldıysa bir uyarı tetiklemek için Genişletilebilir `200 OK` .

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
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
