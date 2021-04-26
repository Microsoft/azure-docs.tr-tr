---
title: Hızlı başlangıç-Docker kapsayıcısını kapsayıcı örneğine dağıtma-Azure CLı
description: Bu hızlı başlangıçta, yalıtılmış bir Azure Container örneğinde çalışan kapsayıcılı bir Web uygulamasını hızla dağıtmak için Azure CLı 'yi kullanırsınız
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom:
- seo-python-october2019
- seodec18
- mvc
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 2027bdc4e77fefe2219b22671494b6d4f4b0ccb8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763991"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak Azure 'da kapsayıcı örneği dağıtma

Azure 'da sunucusuz Docker kapsayıcılarını basitlik ve hızla çalıştırmak için Azure Container Instances kullanın. Azure Kubernetes hizmeti gibi tam kapsayıcı düzenleme platformu gerekmiyorsa, bir uygulamayı isteğe bağlı olarak bir kapsayıcı örneğine dağıtın.

Bu hızlı başlangıçta, yalıtılmış bir Docker kapsayıcısını dağıtmak ve uygulamayı tam etki alanı adı (FQDN) ile kullanılabilir hale getirmek için Azure CLı 'yi kullanırsınız. Tek bir dağıtım komutunu yürütmeden birkaç saniye sonra, kapsayıcıda çalışan uygulamaya gidebilirsiniz:

![Azure Container Instances tarayıcıda dağıtılan bir uygulamayı görüntüleme][aci-app-browser]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu hızlı başlangıç, Azure CLı 'nin sürüm 2.0.55 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Tüm Azure kaynakları gibi Azure kapsayıcı örneklerinin de bir kaynak grubuna dağıtılması gerekir. Kaynak grupları, ilgili Azure kaynaklarını düzenlemenizi ve yönetmenizi sağlar.

İlk olarak aşağıdaki [az group create][az-group-create] komutunu kullanarak *eastus* bölgesinde *myResourceGroup* adlı bir kaynak grubu oluşturun:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Kapsayıcı oluşturma

Artık bir kaynak grubuna sahip olduğunuza göre Azure'da kapsayıcı çalıştırabilirsiniz. Azure CLI ile kapsayıcı örneği oluşturmak için [az container create][az-container-create] komutunda bir kaynak grubu adı, kapsayıcı örneği adı ve Docker kapsayıcı görüntüsü belirtin. Bu hızlı başlangıçta, ortak `mcr.microsoft.com/azuredocs/aci-helloworld` görüntüyü kullanırsınız. Bu görüntü, statik bir HTML sayfasına hizmet veren Node.js yazılmış küçük bir Web uygulamasını paketler.

Açılacak bir veya daha fazla bağlantı noktası, DNS ad etiketi ya da ikisini birden belirterek kapsayıcılarınızı internete açabilirsiniz. Bu hızlı başlangıçta, Web uygulamasının herkese açık bir şekilde erişilebilir olması için DNS adı etiketiyle bir kapsayıcı dağıtırsınız.

Bir kapsayıcı örneği başlatmak için aşağıdakine benzer bir komut yürütün. `--dns-name-label`Örneği oluşturduğunuz Azure bölgesi içinde benzersiz bir değer ayarlayın. "DNS ad etiketi kullanılamıyor" hata iletisiyle karşılaşırsanız farklı bir DNS ad etiketi deneyin.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

Birkaç saniye içinde Azure CLI'den dağıtımın tamamlandığını belirten bir yanıt almanız gerekir. Durumunu [az container show][az-container-show] komutuyla denetleyebilirsiniz:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

Komutu çalıştırdığınızda, kapsayıcının tam etki alanı adı (FQDN) ve sağlama durumu görüntülenir.

```output
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Kapsayıcı `ProvisioningState` **başarılı** olursa, tarayıcınızda FQDN 'sine gidin. Aşağıdakine benzer bir web sayfası görüyorsanız kendinizi tebrik edebilirsiniz! Docker kapsayıcısında çalışan bir uygulamayı başarıyla Azure'a dağıttınız.

![Azure Container Instances tarayıcıda dağıtılan bir uygulamayı görüntüleme][aci-app-browser]

İlk seferde uygulama görüntülenmezse DNS kayıtlarının yayılması için birkaç saniye bekleyip tarayıcınızı yenilemeyi deneyebilirsiniz.

## <a name="pull-the-container-logs"></a>Kapsayıcı günlüklerini çekme

Kapsayıcıdaki veya üzerinde çalışan uygulamalardaki sorunları gidermek (veya yalnızca çıkışını görmek) istediğinizde kapsayıcı örneğinin günlüklerinden başlayın.

[az container logs][az-container-logs] komutu ile kapsayıcı örneğinin günlüklerini çekin:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Çıkış, kapsayıcının günlüklerini görüntüler ve uygulamayı tarayıcınızda görüntülediğinizde oluşturulan HTTP GET isteklerini göstermelidir.

```output
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>Çıkış akışları ekleme

Günlükleri görüntülemeye ek olarak, yerel standart çıkış ve standart hata akışlarınızı kapsayıcınınkine ekleyebilirsiniz.

İlk olarak, yerel konsolunuzu kapsayıcının Çıkış akışlarına eklemek için [az Container Attach][az-container-attach] komutunu yürütün:

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

Bağlandıktan sonra, ek çıkışlar oluşturmak için tarayıcınızı birkaç defa yenileyin. İşlemi tamamladığınızda `Control+C` ile konsolunuzu ayırın. Aşağıdakine benzer bir çıktı görmeniz gerekir:

```output
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kapsayıcıyla işiniz bittiğinde [az container delete][az-container-delete] komutunu kullanarak kapsayıcıyı kaldırın:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Kapsayıcının silindiğini doğrulamak için, [az container list](/cli/azure/container#az_container_list) komutunu yürütün:

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

**mycontainer** kapsayıcısı komut çıkışında görünmemelidir. Kaynak grubunda başka kapsayıcınız yoksa, çıkış görüntülenmez.

*myResourceGroup* kaynak grubuyla ve içindeki kaynaklarla işiniz bittiyse [az group delete][az-group-delete] komutuyla silin:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, genel Microsoft görüntüsünü kullanarak bir Azure Kapsayıcı örneği oluşturdunuz. Kapsayıcı görüntüsünü oluşturup özel bir Azure kapsayıcı kayıt defterinden dağıtmak istiyorsanız Azure Container Instances öğreticisine geçin.

> [!div class="nextstepaction"]
> [Azure Container Instances öğreticisi](./container-instances-tutorial-prepare-app.md)

Azure 'da bir Orchestration sisteminde kapsayıcıları çalıştırmaya yönelik seçenekleri denemek için bkz. [Azure Kubernetes Service (AKS)][container-service] hızlı başlangıç.

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/view-an-application-running-in-an-azure-container-instance.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-create]: /cli/azure/container#az_container_create
[az-container-delete]: /cli/azure/container#az_container_delete
[az-container-list]: /cli/azure/container#az_container_list
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
