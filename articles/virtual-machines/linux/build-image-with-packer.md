---
title: Packer ile Linux Azure VM görüntüleri oluşturma
description: Azure 'da Linux sanal makinelerinin görüntülerini oluşturmak için Packer 'ı nasıl kullanacağınızı öğrenin
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.openlocfilehash: a9f0750908123c236596683ec2ad6de505c46213
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036958"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Azure 'da Linux sanal makine görüntüleri oluşturmak için Packer kullanma
Azure 'daki her sanal makine (VM), Linux dağıtımını ve işletim sistemi sürümünü tanımlayan bir görüntüden oluşturulur. Görüntüler, önceden yüklenmiş uygulamaları ve konfigürasyonları içerebilir. Azure Marketi, en yaygın dağıtımlar ve uygulama ortamları için birçok birinci ve üçüncü taraf görüntü sağlar veya gereksinimlerinize uygun kendi özel görüntülerinizi oluşturabilirsiniz. Bu makalede, Azure 'da özel görüntüler tanımlamak ve derlemek için açık kaynak aracı [Packer](https://www.packer.io/) 'ın nasıl kullanılacağı açıklanır.

> [!NOTE]
> Azure 'da, kendi özel görüntülerinizi tanımlamak ve oluşturmak için Azure görüntü Oluşturucu (Önizleme) hizmeti artık vardır. Azure Image Builder, Packer üzerine kurulmuştur, bu nedenle mevcut Packer kabuğu hazırlayıcı betikleri ile birlikte kullanabilirsiniz. Azure Image Builder 'ı kullanmaya başlamak için bkz. [Azure Image Builder Ile LINUX VM oluşturma](image-builder.md).


## <a name="create-azure-resource-group"></a>Azure Kaynak grubu oluştur
Yapı işlemi sırasında Packer, kaynak VM 'yi oluşturduğunda geçici Azure kaynakları oluşturur. Bu kaynak VM 'yi bir görüntü olarak kullanılacak şekilde yakalamak için bir kaynak grubu tanımlamanız gerekir. Packer Build işleminin çıktısı bu kaynak grubunda saklanır.

[az group create](/cli/azure/group) ile bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Azure kimlik bilgilerini oluşturma
Packer hizmet sorumlusu kullanarak Azure ile kimlik doğrular. Azure hizmet sorumlusu, Packer gibi uygulamalar, hizmetler ve otomasyon araçlarıyla kullanabileceğiniz bir güvenlik kimliğidir. İzinleri, hizmet sorumlusunun Azure 'da gerçekleştirebileceği işlemlere göre kontrol edersiniz ve tanımlar.

[Az ad SP Create-for-RBAC](/cli/azure/ad/sp) ile bir hizmet sorumlusu oluşturun ve Packer 'ın ihtiyacı olan kimlik bilgilerini çıktı:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Yukarıdaki komutlardan çıkış örneği aşağıdaki gibidir:

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Azure 'da kimlik doğrulaması yapmak için [az Account Show](/cli/azure/account)komutuyla Azure abonelik kimliğinizi edinmeniz de gerekir:

```azurecli
az account show --query "{ subscription_id: id }"
```

Sonraki adımda bu iki komuttan gelen çıktıyı kullanırsınız.


## <a name="define-packer-template"></a>Packer şablonunu tanımla
Görüntü oluşturmak için JSON dosyası olarak bir şablon oluşturursunuz. Şablonda, gerçek yapı işlemini yürüten oluşturucular ve hazırlayıcılar tanımlarsınız. Packer, Azure için, önceki adımda oluşturulan hizmet sorumlusu kimlik bilgileri gibi Azure kaynaklarını tanımlamanızı sağlayan bir [hazırlayıcı](https://www.packer.io/docs/builders/azure.html) içerir.

*Ubuntu. JSON* adlı bir dosya oluşturun ve aşağıdaki içeriği yapıştırın. Aşağıdaki değerleri girin:

| Parametre                           | Nereden alınır |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | `az ad sp` Create komutu için çıktının ilk satırı- *AppID* |
| *client_secret*                     | `az ad sp` Create komutu- *Password* öğesinden ikinci çıkış satırı |
| *tenant_id*                         | `az ad sp` oluşturma komutu *kiracısı* için üçüncü çıkış satırı |
| *subscription_id*                   | `az account show` komutundan çıkış |
| *managed_image_resource_group_name* | İlk adımda oluşturduğunuz kaynak grubunun adı |
| *managed_image_name*                | Oluşturulan yönetilen disk görüntüsünün adı |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Bu şablon Ubuntu 16,04 LTS görüntüsünü oluşturur, NGıNX 'i yükleyip VM 'yi kaldırır.

> [!NOTE]
> Kullanıcı kimlik bilgilerini sağlamak için bu şablonu genişlettikten sonra `deprovision+user`yerine `-deprovision` okumak üzere Azure aracısını sağlayan sağlayıcısı Oluştur komutunu ayarlayın.
> `+user` bayrağı, kaynak VM 'den tüm Kullanıcı hesaplarını kaldırır.


## <a name="build-packer-image"></a>Packer görüntüsü oluştur
Yerel makinenizde zaten Packer yüklü değilse, [Packer yükleme yönergelerini izleyin](https://www.packer.io/docs/install/index.html).

Packer şablonu dosyanızı aşağıdaki şekilde belirterek görüntüyü oluşturun:

```bash
./packer build ubuntu.json
```

Yukarıdaki komutlardan çıkış örneği aşağıdaki gibidir:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Packer 'ın VM 'yi oluşturması, hazırlayıcılar çalıştırması ve dağıtımı temizlemesi birkaç dakika sürer.


## <a name="create-vm-from-azure-image"></a>Azure görüntüsünden VM oluşturma
Artık, [az VM Create](/cli/azure/vm)komutuyla görüntinizden bir VM oluşturabilirsiniz. `--image` parametresiyle oluşturduğunuz görüntüyü belirtin. Aşağıdaki örnek *Mypackerımage* öğesinden *myvm* adlı bir VM oluşturur ve henüz yoksa SSH anahtarlarını oluşturur:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Sanal makineleri, Packer görüntüsünden farklı bir kaynak grubunda veya bölgede oluşturmak istiyorsanız, görüntü adı yerine görüntü KIMLIĞINI belirtin. Görüntü KIMLIĞINI [az Image Show](/cli/azure/image#az-image-show)ile elde edebilirsiniz.

VM 'nin oluşturulması birkaç dakika sürer. VM oluşturulduktan sonra Azure CLı tarafından gösterilecek `publicIpAddress` göz atın. Bu adres, NGıNX sitesine bir Web tarayıcısı aracılığıyla erişmek için kullanılır.

Web trafiğinin VM’nize erişmesine izin vermek için, [az vm open-port](/cli/azure/vm) komutuyla İnternet’te 80 numaralı bağlantı noktasını açın:

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Test sanal makinesi ve NGıNX
Artık bir web tarayıcısı açıp adres çubuğuna `http://publicIpAddress` ifadesini girebilirsiniz. VM oluşturma işleminden kendi herkese açık IP adresinizi sağlayın. Varsayılan NGıNX sayfası aşağıdaki örnekte olduğu gibi görüntülenir:

![Varsayılan NGINX sitesi](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Sonraki adımlar
Ayrıca, mevcut Packer sağlayıcısı oluştur betiklerini [Azure Image Builder](image-builder.md)ile de kullanabilirsiniz.
