---
title: Hızlı başlangıç-portalda kayıt defteri oluşturma
description: Azure portal kullanarak özel bir Azure Container Registry oluşturmayı hızlı bir şekilde öğrenin.
ms.date: 08/04/2020
ms.topic: quickstart
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: 4618a83a43d9c27f44432d5d346fdb053cee7054
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773594"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak bir Azure Kapsayıcı kayıt defteri oluşturma

Azure Container Registry, Azure 'da özel Docker kapsayıcı görüntülerini ve ilgili yapıtları depolayabileceğiniz ve yönetebileceğiniz özel bir Docker kayıt defteridir. Bu hızlı başlangıçta, Azure portalıyla bir kapsayıcı kayıt defteri oluşturursunuz. Ardından, Docker komutlarını kullanarak kayıt defterine bir kapsayıcı görüntüsü gönderin ve son olarak görüntüyü Kayıt defterinizden çekin ve çalıştırın.

Bu hızlı başlangıç, kapsayıcı görüntülerle çalışmak üzere kayıt defterinde oturum açmak için Azure CLı 'yi (sürüm 2.0.55 veya üzeri önerilir) çalıştırıyor olmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli].

Ayrıca sisteminizde yerel olarak Docker yüklü olması gerekir. Docker [Mac][docker-mac], [Windows][docker-windows] veya [Linux][docker-linux]'ta Docker'ı kolayca yapılandırmanızı sağlayan paketler sağlar.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-container-registry"></a>Kapsayıcı kayıt defteri oluşturma

Container Registry **kaynak kapsayıcıları oluştur**' u seçin  >    >  .

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="Portalda kapsayıcı kayıt defterine gitme":::

**Temel bilgiler** sekmesinde, **kaynak grubu** ve **kayıt defteri adı** değerlerini girin. Kaynak defteri adı Azure’da benzersiz olmalı ve 5-50 arası alfasayısal karakter içermelidir. Bu hızlı başlangıçta `West US` konumunda `myResourceGroup` adlı yeni bir kaynak grubu oluşturun ve **SKU** olarak ‘Temel’ seçeneğini belirleyin.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="Portalda kapsayıcı kayıt defteri oluşturma":::

Geri kalan ayarlar için varsayılan değerleri kabul edin. Ardından **gözden geçir + oluştur**' u seçin. Ayarları inceledikten sonra **Oluştur**' u seçin.

Bu hızlı başlangıçta, Azure Container Registry hakkında bilgi edinmek için uygun maliyetli bir seçenek olan *temel* bir kayıt defteri oluşturursunuz. Kullanılabilir hizmet katmanları (SKU 'Lar) hakkında daha fazla bilgi için bkz. [kapsayıcı kayıt defteri hizmet katmanları][container-registry-skus].

**Dağıtım başarılı** iletisi göründüğünde, portalda kapsayıcı kayıt defteri ' ni seçin. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="Portalda kapsayıcı kayıt defterine genel bakış":::

Kayıt defteri adını ve **oturum açma sunucusunun** değerini bir yere göz atın. Bu değerleri, Docker ile görüntü gönderdiğinizde ve çektiğinizde aşağıdaki adımlarda kullanırsınız.

## <a name="log-in-to-registry"></a>Kayıt defterinde oturum açma

Kapsayıcı görüntülerini göndermeden ve çekmeden önce, kayıt defteri örneğinde oturum açmalısınız. Yerel makinenizde [Azure CLI oturumunu açın][get-started-with-azure-cli] , sonra [az ACR Login][az-acr-login] komutunu çalıştırın. Azure CLı ile oturum açarken yalnızca kayıt defteri adını belirtin. Gibi bir etki alanı soneki içeren oturum açma sunucusu adını kullanmayın `azurecr.io` .

```azurecli
az acr login --name <registry-name>
```

Örnek:

```azurecli
az acr login --name mycontainerregistry
```

Bu komut tamamlandığında `Login Succeeded` döndürülür. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Kapsayıcı görüntülerini listeleme

Kayıt defterinizin görüntülerini listelemek için portalda Kayıt defterinize gidin ve **depolar**' ı seçin ve ardından ile oluşturduğunuz  **Hello-World** deposunu seçin `docker push` .

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="Portalda kapsayıcı görüntülerini listeleme":::

**Merhaba-Dünya** deposunu seçerek `v1` etiketli görüntüyü **Etiketler** altında görürsünüz.

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynaklarınızı temizlemek için portalda **Myresourcegroup** kaynak grubuna gidin. Kaynak grubu yüklendikten sonra kaynak grubunu **Sil** ' e tıklayarak kaynak grubunu, kapsayıcı kayıt defterini ve orada depolanan kapsayıcı görüntülerini kaldırın.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="Portalda kaynak grubunu silme":::


## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure portal bir Azure Container Registry oluşturdunuz, kapsayıcı görüntüsünü gönderdi ve görüntüyü kayıt defterinden çekmiş ve çalıştırdınız. ACR 'ye daha ayrıntılı bir bakış için Azure Container Registry öğreticilerine geçin.

> [!div class="nextstepaction"]
> [Azure Container Registry öğreticileri][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry görevleri öğreticileri][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az_acr_login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
