---
title: Azure 'da Linux Konakları oluşturmak için Docker makinesi kullanma
description: Docker makinesinin Azure 'da Docker konakları oluşturmak için nasıl kullanılacağını açıklar.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 6b7f26d4554cc5ab1450ac3a8cf927b2338f8ea2
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036259"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Azure 'da ana bilgisayar oluşturmak için Docker makinesi kullanma
Bu makalede, Azure 'da ana bilgisayar oluşturmak için [Docker makinesi](https://docs.docker.com/machine/) 'nin nasıl kullanılacağı açıklanır. `docker-machine` komutu Azure 'da bir Linux sanal makinesi (VM) oluşturur ve Docker 'ı kurar. Daha sonra, aynı yerel araçları ve iş akışlarını kullanarak Docker konaklarınızı Azure 'da yönetebilirsiniz. Windows 10 ' da Docker-Machine ' i kullanmak için Linux Bash kullanmanız gerekir.

## <a name="create-vms-with-docker-machine"></a>Docker makinesiyle VM oluşturma
İlk olarak, [az Account](/cli/azure/account) komutuyla Azure abonelik kimliğinizi aşağıda gösterildiği gibi edinin:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Azure 'da, *Azure* 'da sürücü olarak belirterek Docker ana bilgisayar vm 'leri `docker-machine create` oluşturursunuz. Daha fazla bilgi için bkz. [Docker Azure sürücüsü belgeleri](https://docs.docker.com/machine/drivers/azure/)

Aşağıdaki örnek, "standart D2 v2" planına bağlı olarak *Myvm*ADLı bir VM oluşturur, *azureuser*adlı bir kullanıcı hesabı oluşturur ve ana makine sanal makinesinde *80* numaralı bağlantı noktasını açar. Azure hesabınızda oturum açmak ve kaynak oluşturmak ve yönetmek için Docker makinesi izinleri vermek için tüm istemleri izleyin.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_DS2_v2" \
    myvm
```

Çıktı aşağıdaki örneğe benzer şekilde görünür:

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvm) Completed machine pre-create checks.
Creating machine...
(myvm) Querying existing resource group.  name="docker-machine"
(myvm) Creating resource group.  name="docker-machine" location="westus"
(myvm) Configuring availability set.  name="docker-machine"
(myvm) Configuring network security group.  name="myvm-firewall" location="westus"
(myvm) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvm) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvm) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvm) Creating public IP address.  name="myvm-ip" static=false
(myvm) Creating network interface.  name="myvm-nic"
(myvm) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvm) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvm
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvm
```

## <a name="configure-your-docker-shell"></a>Docker kabuğu 'nu yapılandırma
Azure 'da Docker ana bilgisayarınıza bağlanmak için uygun bağlantı ayarlarını tanımlayın. Çıktının sonunda belirtildiği gibi, Docker konağınız için bağlantı bilgilerini aşağıdaki gibi görüntüleyin: 

```bash
docker-machine env myvm
```

Çıktı aşağıdaki örneğe benzer:

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvm)
```

Bağlantı ayarlarını tanımlamak için, önerilen yapılandırma komutunu (`eval $(docker-machine env myvm)`) çalıştırabilir ya da ortam değişkenlerini el ile ayarlayabilirsiniz. 

## <a name="run-a-container"></a>Kapsayıcı çalıştırma
Bir kapsayıcıyı eylemde görmek için temel NGıNX Web sunucusu çalışmasına izin verir. `docker run` bir kapsayıcı oluşturun ve web trafiği için 80 numaralı bağlantı noktasını aşağıdaki gibi kullanıma sunun:

```bash
docker run -d -p 80:80 --restart=always nginx
```

Çıktı aşağıdaki örneğe benzer:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

Çalışan kapsayıcıları `docker ps`ile görüntüleyin. Aşağıdaki örnek çıktı, 80 numaralı bağlantı noktası ile çalışan NGıNX kapsayıcısını göstermektedir:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Kapsayıcıyı test etme
Docker konağının genel IP adresini şu şekilde edinin:


```bash
docker-machine ip myvm
```

Kapsayıcıyı eylemde görmek için bir Web tarayıcısı açın ve önceki komutun çıktısında belirtilen genel IP adresini girin:

![Ngnx kapsayıcısını çalıştırma](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Sonraki adımlar
Docker Compose kullanma örnekleri için bkz. [Azure 'Da Docker ile çalışmaya başlama ve oluşturma](docker-compose-quickstart.md).
