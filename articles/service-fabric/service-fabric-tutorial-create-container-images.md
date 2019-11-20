---
title: Azure'da Service Fabric üzerinde kapsayıcı görüntüleri oluşturma | Microsoft Docs
description: Bu öğreticide, çok kapsayıcılı bir Service Fabric uygulaması için kapsayıcı görüntülerini nasıl oluşturabileceğinizi öğrenirsiniz.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: chackdan
editor: suhuruli
tags: servicefabric
keywords: Docker, Kapsayıcılar, Mikro Hizmetler, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: b5dba1f7e71149c37edc909d2bb43d708192bdbb
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385218"
---
# <a name="tutorial-create-container-images-on-a-linux-service-fabric-cluster"></a>Öğretici: Linux Service Fabric kümesinde kapsayıcı görüntüleri oluşturma

Bu öğretici, Linux Service Fabric kümesinde kapsayıcıları kullanmayı gösteren öğretici serisinin ilk parçasıdır. Bu öğreticide, bir çoklu konteyner uygulaması Service Fabric ile kullanılmak üzere hazırlanmaktadır. Sonraki öğreticilerde, bu görüntüler Service Fabric uygulamasının bir parçası olarak kullanılır. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Uygulama kaynağını GitHub’dan kopyalama
> * Uygulama kaynağından kapsayıcı görüntüsü oluşturma
> * Azure Container Registry (ACR) örneği dağıtma
> * ACR için kapsayıcı görüntüsü etiketleme
> * Görüntüyü ACR’ye yükleme

Bu öğretici serisinde şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Service Fabric için kapsayıcı görüntüleri oluşturma
> * [Kapsayıcılara Sahip bir Service Fabric Uygulaması Derleme ve Çalıştırma](service-fabric-tutorial-package-containers.md)
> * [Service Fabric’de yük devretme ve ölçeklendirme nasıl işlenir?](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>Önkoşullar

* Service Fabric için ayarlanan Linux geliştirme ortamı. Linux ortamınızı ayarlamak için [buradaki](service-fabric-get-started-linux.md) yönergeleri izleyin.
* Bu öğretici için Azure CLI 2.0.4 veya sonraki bir sürümü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).
* Ayrıca, Azure aboneliğine sahip olmalısınız. Ücretsiz deneme sürümü hakkında daha fazla bilgi için [buraya](https://azure.microsoft.com/free/) göz atın.

## <a name="get-application-code"></a>Uygulama kodunu alma

Bu öğreticide kullanılan örnek uygulama, oylama uygulamasıdır. Bu uygulama, ön uç bileşen ile arka uç Redis örneğinden oluşur. Bileşenler, kapsayıcı görüntüleri olarak paketlenir.

Geliştirme ortamına uygulamanın bir kopyasını indirmek için Git kullanın.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

Çözüm, iki klasör ve bir ‘docker-compose.yml’ dosyası içerir. ‘azure-vote’ klasörü, görüntüyü derlemek için kullanılan Dockerfile dosyasının yanı sıra Python ön uç hizmetini de içerir. ‘Voting’ dizini ise kümeye dağıtılmış Service Fabric uygulama paketini içerir. Bu dizinler, bu öğreticiyi tamamlamak için gerekli varlıkları içerir.

## <a name="create-container-images"></a>Kapsayıcı görüntüleri oluşturma

**azure-vote** dizini içinde, ön uç web bileşeni için görüntü derlemek üzere aşağıdaki komutu çalıştırın. Bu komut, görüntüyü oluşturmak için bu dizindeki Docker dosyasını kullanır.

```bash
docker build -t azure-vote-front .
```
> [!Note]
> Erişim reddediliyorsa Docker'da sudo ile çalışmayı anlatan [bu](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user) belgeyi izleyin.

Gerekli tüm bağımlılıkların Docker Hub’dan çekilmesi gerektiğinden, bu komutun çalıştırılması uzun sürebilir. Tamamlandığında, oluşturulan görüntüleri görmek için [docker images](https://docs.docker.com/engine/reference/commandline/images/) komutunu kullanın.

```bash
docker images
```

İndirilen veya oluşturulan iki görüntü olduğunu göz önünde bulundurun. *azure-vote-front* görüntüsü, uygulamayı içerir. Bu görüntü, Docker Hub’daki bir *python* görüntüsünden türetilmiştir.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Azure Container Registry’yi dağıtma

İlk olarak, Azure hesabınızda oturum açmak için **az Login** komutunu çalıştırın.

```bash
az login
```

Sonra, Azure Container kayıt defterini oluşturmada kullanacağınız aboneliğinizi seçmenizi sağlayan **az account** komutunu kullanın. Azure aboneliğinizin abonelik kimliğini <subscription_id> alanına girmeniz gerekir.

```bash
az account set --subscription <subscription_id>
```

Bir Azure Container Registry dağıtırken önce bir kaynak grubuna ihtiyaç duyarsınız. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

**az group create** komutuyla bir kaynak grubu oluşturun. Bu örnekte, *westus* bölgesinde *myResourceGroup* adlı bir kaynak grubu oluşturulur.

```bash
az group create --name <myResourceGroup> --location westus
```

**az acr create** komutuyla Azure Container kayıt defteri oluşturun. \<acrName> değerini, aboneliğiniz altında oluşturmak istediğiniz kapsayıcı kayıt defteriyle değiştirin. Bu ad, alfasayısal karakterler içermeli ve benzersiz olmalıdır.

```bash
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

Bu öğreticinin geri kalan aşamalarında, seçtiğiniz kapsayıcı kayıt defteri adı için yer tutucu olarak “acrName” kullanacağız. Lütfen bu değeri not edin.

## <a name="sign-in-to-your-container-registry"></a>Kapsayıcı kayıt defterinizde oturum açın

Görüntüleri göndermeden önce ACR Örneğinizde oturum açın. İşlemi tamamlamak için **az acr login** komutunu kullanın. Kapsayıcı kayıt defterine oluşturulduğunda verilen benzersiz adı sağlayın.

```bash
az acr login --name <acrName>
```

Komut tamamlandığında bir “Oturum Başarıyla Açıldı” iletisi döndürür.

## <a name="tag-container-images"></a>Kapsayıcı görüntülerini etiketleme

Her kapsayıcı görüntüsünün, kayıt defterinin loginServer adıyla etiketlenmesi gerekir. Bu etiket, görüntü kayıt defterine kapsayıcı görüntüleri gönderilirken kullanılır.

Mevcut görüntülerin listesini görüntülemek için [docker images](https://docs.docker.com/engine/reference/commandline/images/) komutunu kullanın.

```bash
docker images
```

Çıktı:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

loginServer adını almak için aşağıdaki komutu çalıştırın:

```bash
az acr show --name <acrName> --query loginServer --output table
```

Bu çıkış, aşağıdaki sonuçları içeren bir tablo döndürür. Bu sonuç, sonraki adımda kapsayıcı kayıt defterine göndermeden önce **azure-vote-front** görüntünüzü etiketleyebilmeniz için gereklidir.

```bash
Result
------------------
<acrName>.azurecr.io
```

Şimdi, kapsayıcı kayıt defterinizin loginServer’ı için *azure-vote-front* görüntüsünü etiketleyin. Ayrıca, görüntü adının sonuna `:v1` ekleyin. Bu etiket, görüntü sürümünü belirtir.

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

Etiketledikten sonra, işlemi doğrulamak için ‘docker images’ komutunu çalıştırın.

Çıktı:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Kayıt defterine görüntü gönderme

*azure-vote-front* görüntüsünü kayıt defterine gönderin. 

Aşağıdaki örneği kullanarak, ortamınızda, ACR loginServer adını loginServer olarak değiştirin.

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

Docker gönderme komutlarının tamamlanması birkaç dakika sürebilir.

## <a name="list-images-in-registry"></a>Kayıt defterindeki görüntüleri listeleme

Azure Container Registry’nize gönderilen görüntülerin listesini döndürmek için [az acr repository list](/cli/azure/acr/repository) komutunu kullanın. Komutu ACR örneği adıyla güncelleştirin.

```bash
az acr repository list --name <acrName> --output table
```

Çıktı:

```bash
Result
----------------
azure-vote-front
```

Öğretici tamamlandığında, kapsayıcı görüntüsü özel bir Azure Container Registry örneğinde depolanır. Sonraki öğreticilerde, bu görüntüyü ACR’den bir Service Fabric kümesine dağıtma işlemi açıklanmıştır.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir uygulama GitHub 'dan çekilir ve kapsayıcı görüntüleri oluşturulup bir kayıt defterine gönderildi. Aşağıdaki adımlar tamamlandı:

> [!div class="checklist"]
> * Uygulama kaynağını GitHub’dan kopyalama
> * Uygulama kaynağından kapsayıcı görüntüsü oluşturma
> * Azure Container Registry (ACR) örneği dağıtma
> * ACR için kapsayıcı görüntüsü etiketleme
> * Görüntüyü ACR’ye yükleme

Yeoman kullanarak bir Service Fabric uygulamasına kapsayıcı paketleme hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Kapsayıcıları Service Fabric uygulaması olarak paketleme ve dağıtma](service-fabric-tutorial-package-containers.md)
