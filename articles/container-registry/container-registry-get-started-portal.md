---
title: Hızlı başlangıç-portalda kayıt defteri oluşturma-Azure Container Registry
description: Azure portal Azure Container Registry içinde özel bir Docker kayıt defteri oluşturmayı hızlı bir şekilde öğrenin.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: bcf0874266aa83a4b4b932670557854c4688d400
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931641"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak özel kapsayıcı kayıt defteri oluşturma

Azure kapsayıcı kayıt defteri, Azure’da özel Docker kapsayıcısı görüntülerinizi depolayıp yönetebileceğiniz özel bir Docker kayıt defteridir. Bu hızlı başlangıçta, Azure portalıyla bir kapsayıcı kayıt defteri oluşturursunuz. Ardından, Docker komutlarını kullanarak kayıt defterine bir kapsayıcı görüntüsü gönderin ve son olarak görüntüyü Kayıt defterinizden çekin ve çalıştırın.

Bu hızlı başlangıç, kapsayıcı görüntülerle çalışmak üzere kayıt defterinde oturum açmak için Azure CLı 'yi (sürüm 2.0.55 veya üzeri önerilir) çalıştırıyor olmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli].

Ayrıca sisteminizde yerel olarak Docker yüklü olması gerekir. Docker, [Mac][docker-mac], [Windows][docker-windows] veya [Linux][docker-linux]’ta Docker’ı kolayca yapılandırmanızı sağlayan paketler sağlar.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-container-registry"></a>Kapsayıcı kayıt defteri oluşturma

**Kaynak oluştur** > **Kapsayıcılar** > **Container Registry**'yi seçin.

![Azure portalında kapsayıcı kayıt defteri oluşturma][qs-portal-01]

**Kaynak Defteri Adı** ve **Kaynak grubu** değerlerini girin. Kaynak defteri adı Azure’da benzersiz olmalı ve 5-50 arası alfasayısal karakter içermelidir. Bu hızlı başlangıçta `West US` konumunda `myResourceGroup` adlı yeni bir kaynak grubu oluşturun ve **SKU** olarak ‘Temel’ seçeneğini belirleyin. **Oluştur**’u seçerek ACR örneğini dağıtın.

![Azure portal kapsayıcı kayıt defteri oluşturma][qs-portal-03]

Bu hızlı başlangıçta, Azure Container Registry hakkında bilgi edinmek için uygun maliyetli bir seçenek olan *temel* bir kayıt defteri oluşturursunuz. Kullanılabilir hizmet katmanları hakkında daha fazla bilgi için bkz. [kapsayıcı kayıt defteri SKU 'ları][container-registry-skus].

**Dağıtım başarılı** iletisi göründüğünde, portalda kapsayıcı kayıt defteri ' ni seçin. 

![Azure portal kapsayıcı kayıt defterine genel bakış][qs-portal-05]

**Oturum açma sunucusunun**değerini bir yere göz atın. Bu değeri, Azure CLı ve Docker ile kayıt defteriyle çalışırken aşağıdaki adımlarda kullanırsınız.

## <a name="log-in-to-registry"></a>Kayıt defterinde oturum açma

Kapsayıcı görüntülerini gönderip çekmeden önce ACR örneğinde oturum açmalısınız. İşletim sisteminizde bir komut kabuğu açın ve Azure CLı 'de [az ACR Login][az-acr-login] komutunu kullanın.

```azurecli
az acr login --name <acrName>
```

Bu komut tamamlandığında `Login Succeeded` döndürülür. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Kapsayıcı görüntülerini listeleme

Kayıt defterinizin görüntülerini listelemek için portalda Kayıt defterinize gidin ve **depolar**' ı seçin ve ardından `docker push`oluşturduğunuz depoyu seçin.

Bu örnekte, **Merhaba-Dünya** deposunu seçtik ve **Etiketler**altında `v1`etiketli görüntüyü görebiliriz.

![Azure portal kapsayıcı görüntülerini listeleme][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynaklarınızı temizlemek için portalda **Myresourcegroup** kaynak grubuna gidin. Kaynak grubu yüklendikten sonra, kaynak grubunu, kapsayıcı kayıt defterini ve orada depolanan kapsayıcı görüntülerini kaldırmak için **kaynak grubunu sil** ' e tıklayın.

![Azure portalında kaynak grubunu silme][qs-portal-08]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure portal bir Azure Container Registry oluşturdunuz, kapsayıcı görüntüsünü gönderdi ve görüntüyü kayıt defterinden çekmiş ve çalıştırdınız. ACR 'ye daha ayrıntılı bir bakış için Azure Container Registry öğreticilerine geçin.

> [!div class="nextstepaction"]
> [Azure Container Registry öğreticileri][container-registry-tutorial-quick-task]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

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
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
