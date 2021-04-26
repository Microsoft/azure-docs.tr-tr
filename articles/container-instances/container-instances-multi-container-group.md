---
title: Öğretici-çok Kapsayıcılı grup dağıtma-şablon
description: Bu öğreticide, Azure CLı ile Azure Resource Manager şablonu kullanarak Azure Container Instances birden çok kapsayıcılı bir kapsayıcı grubunu dağıtmayı öğreneceksiniz.
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 93ab139342795634a968cd538672e0e1b9bb08ae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763910"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Öğretici: Kaynak Yöneticisi şablonu kullanarak çok kapsayıcılı bir grup dağıtma

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Azure Container Instances, [kapsayıcı grubu](container-instances-container-groups.md)kullanılarak tek bir konakta birden fazla kapsayıcının dağıtımını destekler. Bir kapsayıcı grubu, günlüğe kaydetme, izleme veya bir hizmetin ikinci bağlı bir işleme ihtiyacı olan başka herhangi bir yapılandırma için bir uygulama arabası oluşturulması durumunda faydalıdır.

Bu öğreticide, Azure CLı kullanarak bir Azure Resource Manager şablonu dağıtarak basit iki kapsayıcılı bir sepet yapılandırması çalıştırmak için adımları takip edersiniz. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Çok kapsayıcılı bir grup şablonu yapılandırma
> * Kapsayıcı grubunu dağıtma
> * Kapsayıcıların günlüklerini görüntüleme

Bir Kaynak Yöneticisi şablonu, kapsayıcı grubuyla ek Azure hizmet kaynakları (örneğin, bir Azure dosya paylaşımında veya sanal ağ) dağıtmanız gerektiğinde senaryolar için kolay bir şekilde uyarlanmıştır. 

> [!NOTE]
> Çok Kapsayıcılı gruplar Şu anda Linux kapsayıcılarıyla kısıtlıdır. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-template"></a>Şablon yapılandırma

Aşağıdaki JSON öğesini adlı yeni bir dosyaya kopyalayarak başlayın `azuredeploy.json` . Azure Cloud Shell, çalışma dizininizde dosyayı oluşturmak için Visual Studio Code kullanabilirsiniz:

```
code azuredeploy.json
```

Bu Kaynak Yöneticisi şablonu, iki kapsayıcı, genel IP adresi ve iki açığa çıkarılan bağlantı noktası içeren bir kapsayıcı grubunu tanımlar. Gruptaki ilk kapsayıcı, internet 'e yönelik bir Web uygulaması çalıştırır. İkinci kapsayıcı olan sepet, ana Web uygulamasına grubun yerel ağı aracılığıyla bir HTTP isteği oluşturur.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": 80
            },
            {
                "protocol": "tcp",
                "port": 8080
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

Özel bir kapsayıcı görüntüsü kayıt defteri kullanmak için, JSON belgesine aşağıdaki biçimde bir nesne ekleyin. Bu yapılandırmanın örnek bir uygulama için bkz. [aci Kaynak Yöneticisi şablonu başvuru][template-reference] belgeleri.

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>Şablonu dağıtma

[az group create][az-group-create] komutuyla bir kaynak grubu oluşturun.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[Az Deployment Group Create][az-deployment-group-create] komutuyla şablonu dağıtın.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json
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

Bu öğreticide, Azure Container Instances içinde çok kapsayıcılı bir grubu dağıtmak için bir Azure Resource Manager şablonu kullandınız. Şunları öğrendiniz:

> [!div class="checklist"]
> * Çok kapsayıcılı bir grup şablonu yapılandırma
> * Kapsayıcı grubunu dağıtma
> * Kapsayıcıların günlüklerini görüntüleme

Ek şablon örnekleri için bkz. [Azure Container Instances için Azure Resource Manager şablonları](container-instances-samples-rm.md).

Ayrıca, [YAML dosyası](container-instances-multi-container-yaml.md)kullanarak çok kapsayıcılı bir grup belirtebilirsiniz. YAML biçiminin daha kısa olmasından dolayı, bir YAML dosyası ile dağıtım, dağıtımınız yalnızca kapsayıcı örnekleri içerdiğinde iyi bir seçimdir.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
