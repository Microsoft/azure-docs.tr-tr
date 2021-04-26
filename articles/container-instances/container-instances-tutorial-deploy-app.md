---
title: Öğretici-kapsayıcı uygulamasını kapsayıcı örneğine dağıtma
description: Azure Container Instances öğreticisi Bölüm 3/3-kapsayıcı uygulamayı Azure Container Instances dağıtma
ms.topic: tutorial
ms.date: 03/21/2018
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 2d12b0bc91cf844001b770c8a7e534ad6b1b9b2d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763892"
---
# <a name="tutorial-deploy-a-container-application-to-azure-container-instances"></a>Öğretici: Azure Container Instances bir kapsayıcı uygulaması dağıtma

Bu, üç kısımdan oluşan serinin son öğreticisidir. Serinin önceki kısımlarında [bir kapsayıcı görüntüsü oluşturuldu](container-instances-tutorial-prepare-app.md) ve [Azure Container Registry’ye gönderildi](container-instances-tutorial-prepare-acr.md). Bu makalede, Azure Container Instances’a kapsayıcı dağıtılarak seri tamamlanır.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Azure Container Registry’den Azure Container Instances’a kapsayıcıyı dağıtma
> * Tarayıcıda çalıştırılan uygulamayı görüntüleme
> * Kapsayıcının günlüklerini görüntüleme

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Azure CLI’yi kullanarak kapsayıcıyı dağıtma

Bu bölümde, [birinci öğreticide](container-instances-tutorial-prepare-app.md) derlenen ve [ikinci öğreticide](container-instances-tutorial-prepare-acr.md) Azure Container Registry’ye gönderilen görüntüyü dağıtmak için Azure CLI’yi kullanırsınız. Devam etmeden önce bu öğreticileri tamamladığınızdan emin olun.

### <a name="get-registry-credentials"></a>Kayıt defteri kimlik bilgilerini alma

[İkinci öğreticide](container-instances-tutorial-prepare-acr.md)oluşturulan gibi özel bir Azure Container Registry 'de barındırılan bir görüntüyü dağıtırken, kayıt defterine erişmek için kimlik bilgilerini sağlamanız gerekir. 

Birçok senaryo için en iyi yöntem, Kayıt defterinize yönelik *çekme* izinleriyle Azure Active Directory hizmet sorumlusu oluşturmak ve yapılandırmak olacaktır. Gerekli izinlere sahip bir hizmet sorumlusu oluşturmak için örnek betikler için [Azure Container Instances Azure Container Registry Ile kimlik doğrulama](../container-registry/container-registry-auth-aci.md) konusuna bakın. *Hizmet sorumlusu kimliği* ve *hizmet sorumlusu parolasını* göz önünde yararlanın. Bu kimlik bilgilerini, kapsayıcıyı dağıtırken kayıt defterine erişmek için kullanırsınız.

Ayrıca, kapsayıcı kayıt defteri oturum açma sunucusunun tam adına de ihtiyacınız vardır ( `<acrName>` kayıt defterinizin adıyla değiştirin):

```azurecli
az acr show --name <acrName> --query loginServer
```

### <a name="deploy-container"></a>Kapsayıcıyı dağıtma

Şimdi kapsayıcıyı dağıtmak için [az container create][az-container-create] komutunu kullanın. `<acrLoginServer>`Önceki komuttan elde ettiğiniz değerle değiştirin. `<service-principal-ID>`Ve `<service-principal-password>` kayıt defterine erişmek için oluşturduğunuz HIZMET sorumlusu kimliği ve parolasıyla değiştirin. `<aciDnsLabel>`İstenen BIR DNS adıyla değiştirin.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-login-server <acrLoginServer> --registry-username <service-principal-ID> --registry-password <service-principal-password> --dns-name-label <aciDnsLabel> --ports 80
```

Birkaç saniye içinde Azure’dan bir ilk yanıt almanız gerekir. `--dns-name-label` değeri, kapsayıcı örneğini oluşturduğunuz Azure bölgesi içinde benzersiz olmalıdır. Komutu yürütürken bir **DNS ad etiketi** hata iletisi alırsanız önceki komutta yer alan değeri değiştirin.

### <a name="verify-deployment-progress"></a>Dağıtım ilerleme durumunu doğrulama

Dağıtımın durumunu görüntülemek için [az container show][az-container-show] komutunu kullanın:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

*Beklemede* olan durum *Çalışıyor* olarak değişinceye kadar [az container show][az-container-show] komutunu yineleyin; bu bir dakikadan kısa sürecektir. Kapsayıcı *Çalışıyor* durumunda olduğunda sonraki adıma ilerleyin.

## <a name="view-the-application-and-container-logs"></a>Uygulama ve kapsayıcı günlüklerini görüntüleme

Dağıtım başarılı olduktan sonra, [az container show][az-container-show] komutuyla kapsayıcının tam etki alanı adını görüntüleyin:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Örnek:
```output
"aci-demo.eastus.azurecontainer.io"
```

Çalışan uygulamayı görmek için, sık kullandığınız tarayıcıda görüntülenen DNS adına gidin:

![Tarayıcıdaki Merhaba Dünya uygulaması][aci-app-browser]

Kapsayıcının günlük çıktısını da görüntüleyebilirsiniz:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Örnek çıktı:

```output
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık bu öğretici serisinde oluşturduğunuz kaynakların herhangi birine ihtiyacınız yoksa, kaynak grubunu ve içerdiği tüm kaynakları kaldırmak için [az group delete][az-group-delete] komutunu yürütebilirsiniz. Bu komut, oluşturduğunuz kapsayıcı kayıt defterinin yanı sıra çalışan kapsayıcıyı ve tüm ilişkili kaynakları siler.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Container Instances’a kapsayıcınızı dağıtma işlemini tamamladınız. Aşağıdaki adımlar tamamlandı:

> [!div class="checklist"]
> * Azure CLI kullanılarak Azure Container Registry’den kapsayıcı dağıtıldı
> * Tarayıcıda uygulama görüntülendi
> * Kapsayıcı günlükleri görüntülendi

Temel bilgileri edindiğinize göre şimdi kapsayıcı gruplarının nasıl çalıştığı gibi, Azure Container Instances hakkında daha fazla bilgi edinmeye geçebilirsiniz:

> [!div class="nextstepaction"]
> [Azure Container Instances’taki kapsayıcı grupları](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
