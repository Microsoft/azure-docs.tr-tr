---
title: Öğretici-çok Kapsayıcılı grup dağıtmak için Docker Compose kullanma
description: Çok kapsayıcılı bir uygulama derlemek ve çalıştırmak için Docker Compose kullanın ve ardından uygulamayı ' de ' de açın Azure Container Instances
ms.topic: tutorial
ms.date: 10/28/2020
ms.custom: ''
ms.openlocfilehash: ef08b9f9e0f596f1d94c0e6edfd46f735fe78053
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786929"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>Öğretici: Docker Compose kullanarak çok kapsayıcılı bir grup dağıtma 

Bu öğreticide, bir çok Kapsayıcılı uygulamayı yerel olarak tanımlayıp çalıştırmak ve sonra Azure Container Instances bir [kapsayıcı grubu](container-instances-container-groups.md) olarak dağıtmak için [Docker Compose](https://docs.docker.com/compose/) kullanırsınız. 

Docker ile bulutta yerel uygulamalar geliştirirken ve yerel geliştirmeden bulut dağıtımına sorunsuz bir şekilde geçmek istediğinizde, kapsayıcıları isteğe bağlı Azure Container Instances olarak çalıştırın. Bu özellik, [Docker ve Azure arasındaki tümleştirmeyle](https://docs.docker.com/engine/context/aci-integration/)etkinleştirilir. Yerel Docker komutlarını, Azure 'da [tek bir kapsayıcı örneği](quickstart-docker-cli.md) veya çok kapsayıcılı bir grup çalıştırmak için kullanabilirsiniz.

> [!IMPORTANT]
> Azure Container Instances özelliklerinin hepsi desteklenmez. [Docker ACI tümleştirmesi](https://github.com/docker/aci-integration-beta) GitHub deposunda bir sorun oluşturarak Docker-Azure tümleştirme hakkında geri bildirim sağlayın.

> [!TIP]
> Kapsayıcıları, görüntüleri ve bağlamlarını geliştirmek, çalıştırmak ve yönetmek için tümleşik bir deneyim için [Visual Studio Code Için Docker uzantısını](https://aka.ms/VSCodeDocker) kullanabilirsiniz.

Bu makalede şunları yapacaksınız:

> [!div class="checklist"]
> * Azure kapsayıcı kayıt defteri oluşturma
> * Uygulama kaynak kodunu GitHub’dan kopyalama
> * Bir görüntü derlemek ve çok kapsayıcılı bir uygulamayı yerel olarak çalıştırmak için Docker Compose kullanma
> * Uygulama görüntüsünü kapsayıcı Kayıt defterinize gönderme
> * Docker için Azure bağlamı oluşturma
> * Azure Container Instances içinde uygulamayı getir

## <a name="prerequisites"></a>Önkoşullar

* **Azure CLI** -Azure CLI 'nın yerel bilgisayarınızda yüklü olması gerekir. Sürüm 2.10.1 veya üzeri önerilir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

* **Docker Desktop** - [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) veya [MacOS](https://desktop.docker.com/mac/edge/Docker.dmg)için kullanılabilen Docker Desktop sürüm 2.3.0.5 veya üstünü kullanmanız gerekir. Veya [Linux Için Docker acı TÜMLEŞTIRMESI CLI](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux)'sını yükler.

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="get-application-code"></a>Uygulama kodunu alma

Bu öğreticide kullanılan örnek uygulama, temel oylama uygulamasıdır. Bu uygulama, ön uç bileşen ile arka uç Redis örneğinden oluşur. Web bileşeni, özel kapsayıcı görüntüsüne paketlenmiştir. Redis örneği, Docker Hub’dan alınan değiştirilmemiş bir görüntü kullanır.

Örnek uygulamayı geliştirme ortamınıza kopyalamak için [git](https://git-scm.com/downloads) komutunu kullanın:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Kopyalanmış dizine geçin.

```console
cd azure-voting-app-redis
```

Dizin içinde uygulama kaynak kodu ve önceden oluşturulmuş bir Docker Compose dosyası, Docker-Compose. YAML olur.

## <a name="modify-docker-compose-file"></a>Docker Compose dosyasını Değiştir

Docker-Compose. YAML 'yi bir metin düzenleyicisinde açın. Dosya `azure-vote-back` ve `azure-vote-front` hizmetlerini yapılandırır.

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "8080:80"
```

`azure-vote-front`Yapılandırmada aşağıdaki iki değişikliği yapın:

1. `image`Hizmette özelliği güncelleştirin `azure-vote-front` . Görüntü adını Azure Container Registry 'nizin oturum açma sunucusu adıyla (. azurecr.io) ön ekler \<acrName\> . Örneğin, kayıt defteriniz *myregistry* olarak adlandırılmışsa, oturum açma sunucusu adı *myregistry.azurecr.io* (tümü küçük) olur ve görüntü özelliği daha sonra olur `myregistry.azurecr.io/azure-vote-front` .
1. `ports`Eşlemesini ile değiştirin `80:80` . Dosyayı kaydedin.

Güncelleştirilmiş dosya şuna benzer olmalıdır:

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: myregistry.azurecr.io/azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Bu alternatifleri yaparak, bir `azure-vote-front` sonraki adımda oluşturduğunuz görüntü, Azure Container kayıt defteriniz için etiketlenebilir ve görüntü Azure Container Instances çalıştırılabilir.

> [!TIP]
> Bu senaryo için bir Azure Container Registry kullanmak zorunda değilsiniz. Örneğin, Docker Hub 'da uygulama görüntünüzü barındırmak için bir özel depo seçebilirsiniz. Farklı bir kayıt defteri seçerseniz, görüntü özelliğini uygun şekilde güncelleştirin.

## <a name="run-multi-container-application-locally"></a>Çok Kapsayıcılı uygulamayı yerel olarak çalıştırma

Kapsayıcı görüntüsünü oluşturmak için örnek dosyayı kullanan [Docker-Compose](https://docs.docker.com/compose/reference/up/)öğesini çalıştırın `docker-compose.yaml` , redsıs görüntüsünü indirin ve uygulamayı başlatın:

```console
docker-compose up --build -d
```

Tamamlandığında, oluşturulan görüntüleri görmek için [docker images](https://docs.docker.com/engine/reference/commandline/images/) komutunu kullanın. Üç görüntü indirilir veya oluşturulur. `azure-vote-front`Görüntü, görüntüyü temel olarak kullanan ön uç uygulamasını içerir `uwsgi-nginx-flask` . `redis` görüntüsü bir Redis örneği başlatmak için kullanılır.

```
$ docker images

REPOSITORY                                TAG        IMAGE ID            CREATED             SIZE
myregistry.azurecr.io/azure-vote-front    latest     9cc914e25834        40 seconds ago      944MB
mcr.microsoft.com/oss/bitnami/redis       6.0.8      3a54a920bb6c        4 weeks ago          103MB
tiangolo/uwsgi-nginx-flask                python3.6  788ca94b2313        9 months ago        9444MB
```

Çalışan kapsayıcıları görmek için [Docker PS](https://docs.docker.com/engine/reference/commandline/ps/) komutunu çalıştırın:

```
$ docker ps

CONTAINER ID        IMAGE                                      COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        myregistry.azurecr.io/azure-vote-front     "/entrypoint.sh /sta…"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:80->80/tcp   azure-vote-front
b62b47a7d313        mcr.microsoft.com/oss/bitnami/redis:6.0.8  "/opt/bitnami/script…"   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

Çalışan uygulamayı görmek için yerel web tarayıcısına `http://localhost:80` yazın. Örnek uygulama aşağıdaki örnekte gösterilen şekilde yüklenir:

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="Oylama uygulamasının görüntüsü":::

Yerel uygulamayı denemeden sonra, uygulamayı durdurmak ve kapsayıcıları kaldırmak için [Docker-Compose](https://docs.docker.com/compose/reference/down/) ' u çalıştırın.

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>Görüntüyü kapsayıcı kayıt defterine gönder

Uygulamayı Azure Container Instances dağıtmak için, `azure-vote-front` görüntüyü kapsayıcı Kayıt defterinize göndermeniz gerekir. Görüntüyü göndermek için [Docker-Compose Push](https://docs.docker.com/compose/reference/push) Çalıştır:

```console
docker-compose push
```

Kayıt defterine gönderim birkaç dakika sürebilir.

Görüntünün kayıt defterinizde depolandığını doğrulamak için [az ACR Repository Show](/cli/azure/acr/repository#az_acr_repository_show) komutunu çalıştırın:

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>Azure Container Instances 'a uygulama dağıtma

Sonra ACI bağlamına geçin. Sonraki Docker komutları bu bağlamda çalışır.

```console
docker context use myacicontext
```

`docker compose up`Azure Container Instances içinde uygulamayı başlatmak için öğesini çalıştırın. `azure-vote-front`Görüntü, kapsayıcı Kayıt defterinizden çekilir ve kapsayıcı grubu Azure Container Instances oluşturulur.

```console
docker compose up
```

> [!NOTE]
> Bir ACI bağlamında Şu anda kullanılabilir olan Docker Compose komutları `docker compose up` ve ' dir `docker compose down` . `docker`Bu komutlarda ve arasında tire yok `compose` .

Kısa bir süre içinde, kapsayıcı grubu dağıtılır. Örnek çıktı:

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

`docker ps`Çalışan kapsayıcıları ve kapsayıcı grubuna atanan IP adresini görmek için ' i çalıştırın.

```console
docker ps
```

Örnek çıktı:

```
CONTAINER ID                           IMAGE                                         COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8                         Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                            Running             52.179.23.131:80->80/tcp
```

Çalışan uygulamayı bulutta görmek için, görüntülenecek IP adresini yerel bir Web tarayıcısına girin. Bu örnekte, girin `52.179.23.131` . Örnek uygulama aşağıdaki örnekte gösterilen şekilde yüklenir:

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="ACI 'de oylama uygulaması görüntüsü":::

Ön uç kapsayıcısının günlüklerini görmek için [Docker logs](https://docs.docker.com/engine/reference/commandline/logs) komutunu çalıştırın. Örnek:

```console
docker logs azurevotingappredis_azure-vote-front
```

Dağıttığınız kapsayıcı grubunun özelliklerini ve durumunu görmek için Azure portal veya diğer Azure araçlarını da kullanabilirsiniz.

Uygulamayı denemeyi bitirdiğinizde uygulamayı ve kapsayıcıları şu ile durdurun `docker compose down` :

```console
docker compose down
```

Bu komut Azure Container Instances kapsayıcı grubunu siler.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, çok kapsayıcılı bir uygulamayı Azure Container Instances ' de çalıştırmak üzere yerel olarak çalıştırmaya geçiş yapmak için Docker Compose kullandınız. Şunları öğrendiniz:

> [!div class="checklist"]
> * Azure kapsayıcı kayıt defteri oluşturma
> * Uygulama kaynak kodunu GitHub’dan kopyalama
> * Bir görüntü derlemek ve çok kapsayıcılı bir uygulamayı yerel olarak çalıştırmak için Docker Compose kullanma
> * Uygulama görüntüsünü kapsayıcı Kayıt defterinize gönderme
> * Docker için Azure bağlamı oluşturma
> * Azure Container Instances içinde uygulamayı getir

Ayrıca, kapsayıcıları, görüntüleri ve bağlamlarını geliştirmek, çalıştırmak ve yönetmek için tümleşik bir deneyim için [Visual Studio Code Için Docker uzantısını](https://aka.ms/VSCodeDocker) da kullanabilirsiniz.

Azure Container Instances daha fazla özellikten yararlanmak istiyorsanız, Azure Araçları 'nı kullanarak çok kapsayıcılı bir grup belirtin. Örneğin, bir [YAML dosyası](container-instances-multi-container-yaml.md)Ile Azure CLI kullanarak bir kapsayıcı grubu dağıtmak veya [Azure Resource Manager şablonu](container-instances-multi-container-group.md)kullanarak dağıtmak için öğreticilere bakın. 