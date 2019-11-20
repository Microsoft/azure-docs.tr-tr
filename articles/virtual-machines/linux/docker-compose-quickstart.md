---
title: Azure 'da bir Linux sanal makinesinde Docker Compose kullanma
description: Azure CLı ile Docker 'ı yüklemek ve kullanmak ve Linux sanal makinelerinde oluşturma
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 4f2f12e0124743ad31e083cf4ece83bcb608bce0
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036267"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Azure 'da çok kapsayıcılı bir uygulama tanımlamak ve çalıştırmak için Docker ve Compose kullanmaya başlama
[Oluşturma](https://github.com/docker/compose)ile birden çok Docker kapsayıcılarından oluşan bir uygulamayı tanımlamak için basit bir metin dosyası kullanın. Sonra uygulamanızı, tanımlı ortamınızı dağıtmak üzere her şeyi yapan tek bir komutta çalıştırabilirsiniz. Örnek olarak, bu makalede bir Ubuntu VM üzerinde bir arka uç MariaDB SQL veritabanı ile bir WordPress blogunu hızlı bir şekilde nasıl ayarlayabileceğinizi gösterir. Ayrıca, daha karmaşık uygulamalar oluşturmak için Oluştur ' a da yararlanabilirsiniz.

Bu makale, [Azure Cloud Shell](https://shell.azure.com/bash) ve [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) sürümü 2.0.58 kullanılarak 2/14/2019 ' de son test edilmiştir.

## <a name="create-docker-host-with-azure-cli"></a>Azure CLı ile Docker Konağı oluşturma
En son [Azure CLI](/cli/azure/install-az-cli2) 'yı yükleyip [az Login](/cli/azure/reference-index)kullanarak bir Azure hesabında oturum açın.

İlk olarak, [az Group Create](/cli/azure/group)komutuyla Docker ortamınız için bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

*Kabuğunuzda Cloud-init. txt* adlı bir dosya oluşturun ve aşağıdaki yapılandırmayı yapıştırın. Dosyayı oluşturmak ve kullanılabilir düzenleyicilerin listesini görmek için `sensible-editor cloud-init.txt` adını girin. 

```yaml
#include https://get.docker.com
```

Şimdi [az vm create](/cli/azure/vm#az-vm-create) ile bir VM oluşturun. `--custom-data` parametresini kullanarak cloud-init yapılandırma dosyanızı geçirin. Dosyayı mevcut çalışma dizininizin dışına kaydettiyseniz *cloud-init.txt* yapılandırmasının tam yolunu belirtin. Aşağıdaki örnek, *Mydockervm* adlı bir sanal makine oluşturur ve 80 numaralı bağlantı noktasını Web trafiğine açar.

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

VM’nin oluşturulması, paketlerin yüklenmesi ve uygulamanın başlatılması birkaç dakika sürebilir. Azure CLI sizi isteme geri döndürdükten sonra çalışmaya devam eden arka plan görevleri vardır. VM oluşturulduktan sonra, Azure CLI tarafından görüntülenen `publicIpAddress` değerini not edin. 

                 

## <a name="install-compose"></a>Yüklemeyi oluştur


Yeni Docker Konağı Sanal makinenize SSH. Kendi IP adresinizi sağlayın.

```bash
ssh azureuser@10.10.111.11
```

VM 'ye oluşturma ' yı yükler.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Docker-Compose. yıml yapılandırma dosyası oluşturma
VM 'de çalıştırılacak Docker kapsayıcılarını tanımlamak için bir `docker-compose.yml` yapılandırma dosyası oluşturun. Dosya, her kapsayıcıda çalıştırılacak görüntüyü, gerekli ortam değişkenlerini ve bağımlılıklarını, bağlantı noktalarını ve kapsayıcılar arasındaki bağlantıları belirtir. YML dosya sözdizimi hakkında daha fazla bilgi için bkz. [dosya başvurusu oluşturma](https://docs.docker.com/compose/compose-file/).

Bir *Docker-Compose. yıml* dosyası oluşturun. Dosyaya veri eklemek için en sevdiğiniz metin düzenleyiciyi kullanın. Aşağıdaki örnek, kullanmak istediğiniz bir düzenleyiciyi seçmek `sensible-editor` için bir komut istemi ile dosyayı oluşturur.

```bash
sensible-editor docker-compose.yml
```

Aşağıdaki örneği Docker Compose dosyanıza yapıştırın. Bu yapılandırma, WordPress (açık kaynak blog ve içerik yönetim sistemi) ve bağlı bir arka uç MariaDB SQL veritabanı 'nı yüklemek için [Dockerhub kayıt defterindeki](https://registry.hub.docker.com/_/wordpress/) görüntüleri kullanır. Kendi *MYSQL_ROOT_PASSWORD*girin.

```yml
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Oluşturma ile kapsayıcıları başlatma
*Docker-Compose. yml* dosyanızdaki aynı dizinde aşağıdaki komutu çalıştırın (ortamınıza bağlı olarak, `sudo`kullanarak `docker-compose` çalıştırmanız gerekebilir):

```bash
sudo docker-compose up -d
```

Bu komut, *Docker-Compose. yıml*Içinde belirtilen Docker kapsayıcılarını başlatır. Bu adımın tamamlanabilmesi için bir veya iki dakika sürer. Aşağıdakine benzer bir çıktı görürsünüz:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Kapsayıcıların yukarı doğru olduğunu doğrulamak için `sudo docker-compose ps`yazın. Şöyle bir şey görmeniz gerekir:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Artık, 80 numaralı bağlantı noktasındaki VM 'ye doğrudan WordPress 'e bağlanabilirsiniz. Bir Web tarayıcısı açın ve sanal makinenizin IP adresi adını girin. Şimdi yüklemeyi tamamlayabileceğiniz ve uygulamayı kullanmaya başlayabileceğiniz WordPress başlangıç ekranını görmeniz gerekir.

![WordPress başlangıç ekranı](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Sonraki adımlar
* Çok Kapsayıcılı uygulamalar oluşturma ve dağıtma hakkında daha fazla örnek için [oluşturma komut satırı başvurusunu](https://docs.docker.com/compose/reference/) ve [Kullanıcı kılavuzunu](https://docs.docker.com/compose/) inceleyin.
* Docker ile bir Azure VM ve oluşturma ile ayarlanmış bir uygulama dağıtmak için kendi veya [topluluğınızdan](https://azure.microsoft.com/documentation/templates/)katkıdan bir Azure Resource Manager şablonu kullanın. Örneğin, [Docker şablonuyla bir WordPress blogu dağıtma](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) , bir Ubuntu VM üzerinde bir MySQL arka ucuna sahip WordPress 'yi hızlıca dağıtmak Için Docker ve Compose kullanır.
* Docker Compose bir Docker Sısınma kümesiyle tümleştirmeyi deneyin. Senaryolar için [Sısınma Ile oluşturma kullanma](https://docs.docker.com/compose/swarm/) konusuna bakın.

