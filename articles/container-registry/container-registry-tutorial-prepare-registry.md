---
title: Öğretici-coğrafi olarak çoğaltılan Azure Container Registry oluşturma
description: Bir Azure Container Registry oluşturun, coğrafi çoğaltma yapılandırın, bir Docker görüntüsü hazırlayın ve bunu kayıt defterine dağıtın. Üç bölümden oluşan bir serinin birinci bölümü.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 04/30/2017
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 5a2aedfe93aa27f839c416c27ac028db1e650295
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931366"
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>Öğretici: Coğrafi çoğaltmalı Azure Container Registry’yi hazırlama

Azure Container Registry, ağı dağıtımlarınıza yakın tutabileceğiniz, Azure’da dağıtılan özel bir Docker kayıt defteridir. Bu üç öğretici makalesinde, Linux kapsayıcısında çalıştırılan bir ASP.NET Core web uygulamasını iki [Kapsayıcılar için Web Apps](../app-service/containers/index.yml) örneğine dağıtmak için coğrafi çoğaltmanın nasıl kullanılacağını öğreneceksiniz. Azure’ın görüntüyü otomatik olarak en yakın coğrafi çoğaltmalı depodan her bir Web Uygulaması örneğine nasıl dağıttığını göreceksiniz.

Bu öğreticide, üç bölümden oluşan bir serinin birinci bölümü:

> [!div class="checklist"]
> * Coğrafi çoğaltmalı Azure Container Registry oluşturma
> * Uygulama kaynak kodunu GitHub’dan kopyalama
> * Uygulama kaynağından bir Docker kapsayıcısı görüntüsü oluşturma
> * Kapsayıcı görüntüsünü kayıt defterinize gönderme

Sonraki öğreticilerde, kapsayıcıyı özel kayıt defterinizden iki Azure bölgesinde çalıştırılan bir web uygulamasına dağıtacaksınız. Daha sonra uygulamadaki kodu güncelleştirecek ve her iki Web Uygulaması örneğini kayıt defterinize yönelik tek bir `docker push` ile güncelleştireceksiniz.

## <a name="before-you-begin"></a>Başlamadan önce

Bu öğretici, Azure CLI’nın (sürüm 2.0.31 veya sonraki bir sürüm) yerel bir yüklemesini gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).

Kapsayıcılar, kapsayıcı görüntüleri ve temel Docker CLI komutları gibi temel Docker kavramları hakkında bilgi sahibi olmanız gerekir. Kapsayıcı temelleri hakkında bilgi için bkz. [Docker ile çalışmaya başlama]( https://docs.docker.com/get-started/).

Bu öğreticiyi tamamlamak için yerel bir Docker yüklemesi gerekir. Docker, [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ve [Linux](https://docs.docker.com/engine/installation/#supported-platforms) sistemleri için yükleme yönergeleri sağlar.

Azure Cloud Shell, bu öğreticideki her adımı tamamlamak için gerekli olan Docker bileşenlerini içermez. Bu nedenle, Azure CLI ve Docker geliştirme ortamının yerel bir yüklemesini öneririz.

## <a name="create-a-container-registry"></a>Kapsayıcı kayıt defteri oluşturma

[Azure portalında](https://portal.azure.com) oturum açın.

**Kaynak oluştur** > **Kapsayıcılar** > **Azure Container Registry** seçeneklerini belirleyin.

![Azure portalında kapsayıcı kayıt defteri oluşturma][tut-portal-01]

Aşağıdaki ayarlarla yeni kayıt defterinizi yapılandırın:

* **Kayıt defteri adı**: Azure’da genel olarak benzersiz olan ve 5-50 alfasayısal karakterden oluşan bir kayıt defteri adı oluşturun
* **Kaynak Grubu**: **Yeni oluştur** > `myResourceGroup`
* **Konum**: `West US`
* **Yönetici kullanıcı**: `Enable` (Kapsayıcılar için Web App’in görüntüleri çekmesi için gereklidir)
* **SKU**: `Premium` (coğrafi çoğaltma için gereklidir)

**Oluştur**’u seçerek ACR örneğini dağıtın.

![Azure portalında kapsayıcı kayıt defteri oluşturma][tut-portal-02]

Bu öğreticinin geri kalan aşamalarında, seçtiğiniz kapsayıcı `<acrName>`Kayıt defteri adı**için yer tutucu olarak** kullanacağız.

> [!TIP]
> Azure Container Registry genellikle birden fazla kapsayıcı konağında kullanılan uzun ömürlü kaynaklar olduğundan, kayıt defterinizi kendi kaynak grubunda oluşturmanızı öneririz. Coğrafi çoğaltmalı kayıt defterleri ve web kancaları yapılandırılırken bu ek kaynaklar aynı kaynak grubuna yerleştirilir.
>

## <a name="configure-geo-replication"></a>Coğrafi çoğaltmayı yapılandırma

Premium kayıt defterine sahip olduğunuza göre coğrafi çoğaltmayı yapılandırabilirsiniz. Sonraki öğreticide iki bölgede çalıştırılacak şekilde yapılandıracağınız web uygulamanız, en yakın kayıt defterinden kapsayıcı görüntülerini çeker.

Azure portalında yeni kapsayıcı kayıt defterinize gidin ve **HİZMETLER** bölümünden **Çoğaltmalar**’ı seçin:

![Azure portalı kapsayıcı kayıt defteri kullanıcı arabirimindeki Çoğaltmalar][tut-portal-03]

Coğrafi çoğaltma için uygun Azure bölgelerinin yeşil altıgenlerle temsil edildiği bir harita görüntülenir:

 ![Azure portalındaki bölge haritası][tut-map-01]

Yeşil altıgeni seçip ardından **Çoğaltma oluştur** bölümünden **Oluştur**’u seçerek Doğu ABD bölgesine kayıt defterinizi çoğaltın:

 ![Azure portalında çoğaltma oluşturmaya yönelik kullanıcı arabirimi][tut-portal-04]

Çoğaltma tamamlandığında portal her iki bölge için de *Hazır* durumunu gösterir. Çoğaltma durumunu yenilemek için **Yenile** düğmesini kullanın. Çoğaltmaların oluşturulması ve eşitlenmesi bir dakika kadar sürebilir.

![Azure portalında çoğaltma durumuna yönelik kullanıcı arabirimi][tut-portal-05]

## <a name="container-registry-login"></a>Kapsayıcı kayıt defterinde oturum açma

Coğrafi çoğaltmayı yapılandırdığınıza göre, bir kapsayıcı görüntüsü oluşturun ve kayıt defterinize gönderin. Görüntüleri göndermeden önce ACR örneğinizde oturum açmanız gerekir.

Kimlik doğrulaması yapmak ve kayıt defterinize yönelik kimlik bilgilerini önbelleğe almak için [az acr login](https://docs.microsoft.com/cli/azure/acr#az-acr-login) komutunu kullanın. `<acrName>` değerini, önceden oluşturduğunuz kayıt defterinin adıyla değiştirin.

```azurecli
az acr login --name <acrName>
```

Bu komut tamamlandığında `Login Succeeded` iletisini döndürür.

## <a name="get-application-code"></a>Uygulama kodunu alma

Bu öğreticideki örnek, [ASP.NET Core][aspnet-core] ile oluşturulmuş küçük bir web uygulamasını içerir. Uygulama, Azure Container Registry tarafından görüntünün dağıtıldığı kaynak bölgeyi görüntüleyen bir HTML sayfası görevi görür.

![Tarayıcıda gösterilen öğretici uygulama][tut-app-01]

Örneği yerel bir dizine ve `cd` öğesini şu dizine indirmek için git kullanın:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

Yüklü `git` yoksa, [ZIP arşivini][acr-helloworld-zip] doğrudan GitHub 'dan indirebilirsiniz.

## <a name="update-dockerfile"></a>Dockerfile’ı güncelleştirme

Örnekte bulunan Dockerfile, kapsayıcının nasıl yapılandırıldığını gösterir. Resmi bir [aspnetcore][dockerhub-aspnetcore] görüntüsünden başlatılır, uygulama dosyalarını kapsayıcıya kopyalar, bağımlılıkları yükler, resmi [aspnetcore-build][dockerhub-aspnetcore-build] görüntüsünü kullanarak çıktıyı derler ve son olarak iyileştirilmiş bir aspnetcore görüntüsü oluşturur.

[Dockerfile][dockerfile] , kopyalanmış kaynaktaki `./AcrHelloworld/Dockerfile` konumunda bulunur.

```Dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

*acr-helloworld* görüntüsündeki uygulama, kayıt defterinin oturum açma sunucusuyla ilgili bilgiler için DNS’yi sorgulayarak kapsayıcısının dağıtıldığı kaynak bölgeyi belirlemeye çalışır. Dockerfile’daki `DOCKER_REGISTRY` ortam değişkeninde kayıt defterinizin oturum açma sunucusunun tam etki alanı adını (FQDN) belirtmeniz gerekir.

İlk olarak, `az acr show` komutuyla kayıt defterinin oturum açma sunucusunu alın. `<acrName>` değerini, önceki adımlarda oluşturduğunuz kayıt defterinin adıyla değiştirin.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Çıktı:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Sonra, `ENV DOCKER_REGISTRY` satırını kayıt defterinizin oturum açma sunucusu FQDN’si ile güncelleştirin. Bu örnek, *uniqueregistryname* olan örnek kayıt defteri adını yansıtır:

```Dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Kapsayıcı görüntüsü oluşturma

Dockerfile’ı kayıt defteri oturum açma sunucunuzun FQDN’si ile güncelleştirdiğinize göre, kapsayıcı görüntüsünü oluşturmak için `docker build` kullanabilirsiniz. Tekrar `<acrName>` değerini, kayıt defterinizin adıyla değiştirerek görüntüyü oluşturmak ve özel kayıt defterinizin URL’si ile etiketlemek için aşağıdaki komutu çalıştırın:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Docker görüntüsü oluşturulurken birçok çıktı satırı görüntülenir (burada kesilmiş şekilde gösterilmektedir):

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer

[...]

Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

Derlenen ve etiketlenen görüntüyü görmek için `docker images` kullanın:

```console
$ docker images
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
[...]
```

## <a name="push-image-to-azure-container-registry"></a>Azure Container Registry’ye görüntü gönderme

Daha sonra, kayıt defterinize `docker push`acr-helloworld*görüntüsünü göndermek için* komutunu kullanın. `<acrName>` değerini kayıt defterinizin adıyla değiştirin.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Coğrafi çoğaltma için kayıt defterinizi yapılandırdığınızdan, bu tek *komutuyla görüntünüz hem*Batı ABD*hem de*Doğu ABD`docker push` bölgesine otomatik olarak çoğaltılır.

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, özel, coğrafi çoğaltmalı bir kayıt defteri oluşturdunuz, bir kapsayıcı görüntüsü oluşturdunuz ve sonra bu görüntüyü kayıt defterinize gönderdiniz.

Görüntülere yerel olarak hizmet vermek için coğrafi çoğaltma kullanarak kapsayıcınızı birden fazla Kapsayıcılar için Web Apps’e dağıtmak için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Azure Container Registry’den web uygulaması dağıtma](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png

<!-- LINKS - External -->
[acr-helloworld-zip]: https://github.com/Azure-Samples/acr-helloworld/archive/master.zip
[aspnet-core]: https://dot.net
[dockerhub-aspnetcore]: https://hub.docker.com/r/microsoft/aspnetcore/
[dockerhub-aspnetcore-build]: https://store.docker.com/community/images/microsoft/aspnetcore-build
[dockerfile]: https://github.com/Azure-Samples/acr-helloworld/blob/master/AcrHelloworld/Dockerfile