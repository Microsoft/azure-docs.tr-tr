---
title: Öğretici-coğrafi olarak çoğaltılan kayıt defteri oluşturma
description: Bir Azure Container Registry oluşturun, coğrafi çoğaltma yapılandırın, bir Docker görüntüsü hazırlayın ve bunu kayıt defterine dağıtın. Üç bölümden oluşan bir serinin birinci bölümü.
ms.topic: tutorial
ms.date: 06/30/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: c5f5627462f016c857d956be46c276dda78e9b2b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780668"
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>Öğretici: Coğrafi çoğaltmalı Azure Container Registry’yi hazırlama

Azure Container Registry, ağı dağıtımlarınıza yakın tutabileceğiniz, Azure’da dağıtılan özel bir Docker kayıt defteridir. Bu üç öğretici makalesinde, Linux kapsayıcısında çalıştırılan bir ASP.NET Core web uygulamasını iki [Kapsayıcılar için Web Apps](../app-service/index.yml) örneğine dağıtmak için coğrafi çoğaltmanın nasıl kullanılacağını öğreneceksiniz. Azure’ın görüntüyü otomatik olarak en yakın coğrafi çoğaltmalı depodan her bir Web Uygulaması örneğine nasıl dağıttığını göreceksiniz.

Bu öğreticide, üç bölümden oluşan bir serinin birinci bölümü:

> [!div class="checklist"]
> * Coğrafi çoğaltmalı Azure Container Registry oluşturma
> * Uygulama kaynak kodunu GitHub’dan kopyalama
> * Uygulama kaynağından bir Docker kapsayıcısı görüntüsü oluşturma
> * Kapsayıcı görüntüsünü kayıt defterinize gönderme

Sonraki öğreticilerde, kapsayıcıyı özel kayıt defterinizden iki Azure bölgesinde çalıştırılan bir web uygulamasına dağıtacaksınız. Daha sonra uygulamadaki kodu güncelleştirecek ve her iki Web Uygulaması örneğini kayıt defterinize yönelik tek bir `docker push` ile güncelleştireceksiniz.

## <a name="before-you-begin"></a>Başlamadan önce

Bu öğretici, Azure CLI’nın (sürüm 2.0.31 veya sonraki bir sürüm) yerel bir yüklemesini gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme]( /cli/azure/install-azure-cli).

Kapsayıcılar, kapsayıcı görüntüleri ve temel Docker CLI komutları gibi temel Docker kavramları hakkında bilgi sahibi olmanız gerekir. Kapsayıcı temelleri hakkında bilgi için bkz. [Docker ile çalışmaya başlama]( https://docs.docker.com/get-started/).

Bu öğreticiyi tamamlamak için yerel bir Docker yüklemesi gerekir. Docker, [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ve [Linux](https://docs.docker.com/engine/installation/#supported-platforms) sistemleri için yükleme yönergeleri sağlar.

Azure Cloud Shell, bu öğreticideki her adımı tamamlamak için gerekli olan Docker bileşenlerini içermez. Bu nedenle, Azure CLI ve Docker geliştirme ortamının yerel bir yüklemesini öneririz.

## <a name="create-a-container-registry"></a>Kapsayıcı kayıt defteri oluşturma

Bu öğreticide, Premium hizmet katmanında bir Azure Kapsayıcı kayıt defteri gerekir. Yeni bir Azure Container Registry oluşturmak için bu bölümdeki adımları izleyin.

> [!TIP]
> Daha önce bir kayıt defteri oluşturduysanız ve yükseltmeniz gerekiyorsa bkz. [katmanları değiştirme](container-registry-skus.md#changing-tiers). 

[Azure portalında](https://portal.azure.com) oturum açın.

Azure Container Registry **kaynak kapsayıcıları oluştur**' u seçin  >    >  .

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-01.png" alt-text="Azure portalında kapsayıcı kayıt defteri oluşturma":::

Yeni kayıt defterinizi aşağıdaki ayarlarla yapılandırın. **Temel bilgiler** sekmesinde:

* **Kayıt defteri adı**: Azure’da genel olarak benzersiz olan ve 5-50 alfasayısal karakterden oluşan bir kayıt defteri adı oluşturun
* **Kaynak grubu**: **Yeni oluştur** > `myResourceGroup`
* **Konum**: `West US`
* **SKU**: `Premium` (coğrafi çoğaltma için gereklidir)

Kayıt defteri örneğini oluşturmak için **gözden geçir + oluştur** seçeneğini belirleyip **Oluştur** ' u seçin.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-02.png" alt-text="Azure portal bir kapsayıcı kayıt defteri yapılandırma":::

Bu öğreticinin geri kalan aşamalarında, seçtiğiniz kapsayıcı **Kayıt defteri adı** için yer tutucu olarak `<acrName>` kullanacağız.

> [!TIP]
> Azure Container Registry genellikle birden fazla kapsayıcı konağında kullanılan uzun ömürlü kaynaklar olduğundan, kayıt defterinizi kendi kaynak grubunda oluşturmanızı öneririz. Coğrafi çoğaltmalı kayıt defterleri ve web kancaları yapılandırılırken bu ek kaynaklar aynı kaynak grubuna yerleştirilir.

## <a name="configure-geo-replication"></a>Coğrafi çoğaltmayı yapılandırma

Premium kayıt defterine sahip olduğunuza göre coğrafi çoğaltmayı yapılandırabilirsiniz. Sonraki öğreticide iki bölgede çalıştırılacak şekilde yapılandıracağınız web uygulamanız, en yakın kayıt defterinden kapsayıcı görüntülerini çeker.

Azure portal yeni kapsayıcı Kayıt defterinize gidin ve **Hizmetler** bölümünden **çoğaltmalar** ' ı seçin:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-03.png" alt-text="Azure portalı kapsayıcı kayıt defteri kullanıcı arabirimindeki Çoğaltmalar":::

Coğrafi çoğaltma için uygun Azure bölgelerinin yeşil altıgenlerle temsil edildiği bir harita görüntülenir:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-map-01.png" alt-text="Azure portalındaki bölge haritası":::

Yeşil altıgeni seçip ardından **Çoğaltma oluştur** bölümünden **Oluştur**’u seçerek Doğu ABD bölgesine kayıt defterinizi çoğaltın:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-04.png" alt-text="Azure portalında çoğaltma oluşturmaya yönelik kullanıcı arabirimi":::

Çoğaltma tamamlandığında portal her iki bölge için de *Hazır* durumunu gösterir. Çoğaltma durumunu yenilemek için **Yenile** düğmesini kullanın. Çoğaltmaların oluşturulması ve eşitlenmesi bir dakika kadar sürebilir.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-05.png" alt-text="Azure portalında çoğaltma durumuna yönelik kullanıcı arabirimi":::


## <a name="enable-admin-account"></a>Yönetici hesabını etkinleştir

Sonraki öğreticilerde, kayıt defterinden doğrudan Kapsayıcılar için Web App bir kapsayıcı görüntüsü dağıtırsınız. Bu özelliği etkinleştirmek için kayıt defterinin [yönetici hesabını](container-registry-authentication.md#admin-account)da etkinleştirmeniz gerekir.

Azure portal yeni kapsayıcı Kayıt defterinize gidin ve **Ayarlar** altında **erişim anahtarları** ' nı seçin. **Yönetici kullanıcı** altında **Etkinleştir**’i seçin.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-06.png" alt-text="Yönetici hesabını Azure portal etkinleştirme":::


## <a name="container-registry-login"></a>Kapsayıcı kayıt defterinde oturum açma

Coğrafi çoğaltmayı yapılandırdığınıza göre, bir kapsayıcı görüntüsü oluşturun ve kayıt defterinize gönderin. Görüntüleri göndermeden önce kayıt defterinizde oturum açmalısınız.

Kimlik doğrulaması yapmak ve kayıt defterinize yönelik kimlik bilgilerini önbelleğe almak için [az acr login](/cli/azure/acr#az_acr_login) komutunu kullanın. `<acrName>` değerini, önceden oluşturduğunuz kayıt defterinin adıyla değiştirin.

```azurecli
az acr login --name <acrName>
```

Bu komut tamamlandığında `Login Succeeded` iletisini döndürür.

## <a name="get-application-code"></a>Uygulama kodunu alma

Bu öğreticideki örnek, [ASP.NET Core][aspnet-core] ile oluşturulmuş küçük bir web uygulamasını içerir. Uygulama, Azure Container Registry tarafından görüntünün dağıtıldığı kaynak bölgeyi görüntüleyen bir HTML sayfası görevi görür.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-app-01.png" alt-text="Tarayıcıda gösterilen öğretici uygulama":::

Örneği yerel bir dizine ve `cd` öğesini şu dizine indirmek için git kullanın:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

`git` yüklenmemişse, doğrudan GitHub’dan [ZIP arşivini indirebilirsiniz][acr-helloworld-zip].

## <a name="update-dockerfile"></a>Dockerfile’ı güncelleştirme

Örnekte bulunan Dockerfile, kapsayıcının nasıl yapılandırıldığını gösterir. Resmi bir ASP.NET Core çalışma zamanı görüntüsünden başlar, uygulama dosyalarını kapsayıcıya kopyalar, bağımlılıkları kurar, resmi .NET Core SDK görüntüsünü kullanarak çıktıyı derler ve son olarak en iyi duruma getirilmiş bir aspnetcore görüntüsü oluşturur.

[Dockerfile][dockerfile] , `./AcrHelloworld/Dockerfile` klonlanan kaynakta bulunur.

```Dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
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

Çıkış:

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
Step 1/18 : FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS base
2.2: Pulling from mcr.microsoft.com/dotnet/core/aspnet
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

Daha sonra, kayıt defterinize *acr-helloworld* görüntüsünü göndermek için `docker push` komutunu kullanın. `<acrName>` değerini kayıt defterinizin adıyla değiştirin.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Coğrafi çoğaltma için kayıt defterinizi yapılandırdığınızdan, bu tek `docker push` komutuyla görüntünüz hem *Batı ABD* hem de *Doğu ABD* bölgesine otomatik olarak çoğaltılır.

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

<!-- LINKS - External -->
[acr-helloworld-zip]: https://github.com/Azure-Samples/acr-helloworld/archive/master.zip
[aspnet-core]: https://dot.net
[dockerfile]: https://github.com/Azure-Samples/acr-helloworld/blob/master/AcrHelloworld/Dockerfile
