---
title: Azure CLı ile bir Linux sanal makinesine MongoDB 'yi yüklemeyin
description: Azure CLı kullanarak MongoDB 'yi Linux sanal makinesine yüklemeyi ve yapılandırmayı öğrenin
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 7f5fdd625eb49bfcac0bd58bca7a8415ac877517
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036003"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Linux VM 'de MongoDB 'yi yüklemek ve yapılandırmak

[MongoDB](https://www.mongodb.org) , popüler bir açık kaynaklı ve yüksek performanslı bir NoSQL veritabanıdır. Bu makalede, Azure CLı ile bir Linux sanal makinesine MongoDB 'nin nasıl yükleneceği ve yapılandırılacağı gösterilmektedir. Aşağıda aşağıdakiler hakkında ayrıntılı bilgiler verilmiştir:

* [Temel bir MongoDB örneğini el ile yükleyip yapılandırma](#manually-install-and-configure-mongodb-on-a-vm)
* [Kaynak Yöneticisi şablonu kullanarak temel bir MongoDB örneği oluşturma](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Kaynak Yöneticisi şablonu kullanarak çoğaltma kümeleriyle karmaşık bir MongoDB parçalı kümesi oluşturma](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>MongoDB 'yi bir VM 'ye el ile yükleyip yapılandırma
MongoDB, Red Hat/CentOS, SUSE, Ubuntu ve detik dahil olmak üzere Linux Distro 'lara için [yükleme yönergeleri sağlar](https://docs.mongodb.com/manual/administration/install-on-linux/) . Aşağıdaki örnek bir *CentOS* sanal makinesi oluşturur. Bu ortamı oluşturmak için [az Login](/cli/azure/reference-index)kullanarak bir Azure hesabında en son [Azure CLI](/cli/azure/install-az-cli2) 'nın yüklü ve oturum açmış olması gerekir.

[az group create](/cli/azure/group) ile bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur:

```azurecli
az group create --name myResourceGroup --location eastus
```

[az vm create](/cli/azure/vm) ile bir VM oluşturun. Aşağıdaki örnek, SSH ortak anahtar kimlik doğrulamasını kullanarak *azureuser* adlı bir kullanıcıyla *MYVM* adlı bir VM oluşturur

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Kendi Kullanıcı adınızı ve önceki adımdan alınan çıktıda listelenen `publicIpAddress` sanal makineye SSH.

```bash
ssh azureuser@<publicIpAddress>
```

MongoDB için yükleme kaynakları eklemek için, aşağıdaki gibi bir **yum** depo dosyası oluşturun:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

`vi` veya `nano`gibi, düzenlenmek üzere MongoDB depo dosyasını açın. Aşağıdaki satırları ekleyin:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Şu şekilde **yum** kullanarak MongoDB 'yi oluşturun:

```bash
sudo yum install -y mongodb-org
```

Varsayılan olarak SELinux, MongoDB 'ye erişiminizi önleyen CentOS görüntülerinde zorlanır. İlke yönetim araçları 'nı yükleyip SELinux 'u, MongoDB 'nin varsayılan TCP bağlantı noktası 27017 ' de şu şekilde çalışmasına izin verecek şekilde yapılandırın:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

MongoDB hizmetini şu şekilde başlatın:

```bash
sudo service mongod start
```

Yerel `mongo` istemcisini kullanarak bağlantı kurarak MongoDB yüklemesini doğrulayın:

```bash
mongo
```

Şimdi bazı verileri ekleyerek ve ardından arayarak MongoDB örneğini test edin:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

İsterseniz, MongoDB 'yi sistem yeniden başlatma sırasında otomatik olarak başlayacak şekilde yapılandırın:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Bir şablon kullanarak CentOS üzerinde temel MongoDB örneği oluşturma
GitHub 'dan aşağıdaki Azure hızlı başlangıç şablonunu kullanarak tek bir CentOS sanal makinesinde temel bir MongoDB örneği oluşturabilirsiniz. Bu şablon, yeni oluşturulan CentOS sanal makinesine bir **IUM** deposu eklemek ve ardından MongoDB 'yi yüklemek için Linux Için özel Betik uzantısı 'nı kullanır.

* [CentOS - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json temel MongoDB örneği](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos)

Bu ortamı oluşturmak için [az Login](/cli/azure/reference-index)kullanarak bir Azure hesabında en son [Azure CLI](/cli/azure/install-az-cli2) 'nın yüklü ve oturum açmış olması gerekir. Öncelikle [az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur:

```azurecli
az group create --name myResourceGroup --location eastus
```

Daha sonra, MongoDB şablonunu [az Group Deployment Create](/cli/azure/group/deployment)komutuyla dağıtın. İstendiğinde, *Newstorageaccountname*, *dnsnameforpublicıp*ve Yönetici Kullanıcı adı ve parolası için kendi benzersiz değerlerinizi girin:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

VM 'nizin genel DNS adresini kullanarak VM 'de oturum açın. Genel DNS adresini [az VM Show](/cli/azure/vm)ile görüntüleyebilirsiniz:

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

Kendi Kullanıcı adınızı ve genel DNS adresinizi kullanarak sanal makinenize SSH:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Yerel `mongo` istemcisini kullanarak şu şekilde bağlantı kurarak MongoDB yüklemesini doğrulayın:

```bash
mongo
```

Şimdi aşağıdaki gibi bazı veri ve arama ekleyerek örneği test edin:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Bir şablon kullanarak CentOS üzerinde karmaşık bir MongoDB parçalı kümesi oluşturma
GitHub 'dan aşağıdaki Azure hızlı başlangıç şablonunu kullanarak karmaşık bir MongoDB parçalı küme oluşturabilirsiniz. Bu şablon, artıklık ve yüksek kullanılabilirlik sağlamak için [MongoDB parçalı küme en iyi uygulamalarını](https://docs.mongodb.com/manual/core/sharded-cluster-components/) izler. Şablon, her bir çoğaltma kümesinde üç düğüm ile iki parça oluşturur. Aynı zamanda üç düğüm içeren bir yapılandırma sunucusu çoğaltma kümesi oluşturulur ve parçaların tamamında uygulamalara tutarlılık sağlamak için iki **mongos** yönlendirici sunucusu vardır.

* [CentOS - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json MongoDB parçalama kümesi](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos)

> [!WARNING]
> Bu karmaşık MongoDB parçalı kümenin dağıtımı, genellikle bir abonelik için bölge başına varsayılan çekirdek sayısı olan 20 ' den fazla çekirdek gerektirir. Çekirdek saymanızı artırmak için bir Azure destek isteği açın.

Bu ortamı oluşturmak için [az Login](/cli/azure/reference-index)kullanarak bir Azure hesabında en son [Azure CLI](/cli/azure/install-az-cli2) 'nın yüklü ve oturum açmış olması gerekir. Öncelikle [az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur:

```azurecli
az group create --name myResourceGroup --location eastus
```

Daha sonra, MongoDB şablonunu [az Group Deployment Create](/cli/azure/group/deployment)komutuyla dağıtın. *Mongoadminusername*, *sizeofdatadiskingb*ve *confignodevmsize*gibi gerektiğinde kendi kaynak adlarınızı ve boyutlarınızı tanımlayın:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

Bu dağıtım, tüm sanal makine örneklerini dağıtmak ve yapılandırmak için bir saatten fazla sürebilir. `--no-wait` bayrağı, önceki komutun sonunda, şablon dağıtımı Azure platformu tarafından kabul edildikten sonra denetimi komut istemine döndürmek için kullanılır. Daha sonra dağıtım durumunu [az Group Deployment Show](/cli/azure/group/deployment)ile görüntüleyebilirsiniz. Aşağıdaki örnek *Myresourcegroup* kaynak grubundaki *Mymongodbcluster* dağıtımının durumunu görüntüler:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Sonraki adımlar
Bu örneklerde, sanal makinede bulunan MongoDB örneğine yerel olarak bağlanırsınız. Başka bir VM 'den veya ağdan MongoDB örneğine bağlanmak istiyorsanız, uygun [ağ güvenlik grubu kurallarının oluşturulduğundan](nsg-quickstart.md)emin olun.

Bu örnekler, geliştirme amacıyla çekirdek MongoDB ortamını dağıtır. Ortamınız için gerekli güvenlik yapılandırması seçeneklerini uygulayın. Daha fazla bilgi için bkz. [MongoDB güvenlik belgeleri](https://docs.mongodb.com/manual/security/).

Şablonları kullanarak oluşturma hakkında daha fazla bilgi için bkz. [Azure Resource Manager genel bakış](../../azure-resource-manager/resource-group-overview.md).

Azure Resource Manager şablonlar, VM 'lerinizde betikleri indirmek ve yürütmek için özel betik uzantısını kullanır. Daha fazla bilgi için bkz. [Azure Özel Betik uzantısı 'nı Linux sanal makineleri Ile kullanma](extensions-customscript.md).

