---
title: OpenBSD VM görüntüsünü oluşturma ve Azure 'a yükleme
description: Azure CLı aracılığıyla bir Azure sanal makinesi oluşturmak için OpenBSD işletim sistemini içeren bir sanal sabit disk (VHD) oluşturmayı ve yüklemeyi öğrenin
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: huishao
ms.openlocfilehash: ee15836906eef0b9205691f9a6003cea0b9fae80
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036461"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>OpenBSD disk görüntüsünü Azure 'a oluşturma ve yükleme
Bu makalede, OpenBSD işletim sistemini içeren bir sanal sabit disk (VHD) oluşturma ve karşıya yükleme işleminin nasıl yapılacağı gösterilir. Karşıya yükledikten sonra, Azure CLı aracılığıyla Azure 'da bir sanal makine (VM) oluşturmak için kendi görüntünüz olarak kullanabilirsiniz.


## <a name="prerequisites"></a>Önkoşullar
Bu makalede, aşağıdaki öğeler olduğunu varsaymaktadır:

* **Azure aboneliği** -hesabınız yoksa yalnızca birkaç dakika içinde bir tane oluşturabilirsiniz. Bir MSDN aboneliğiniz varsa bkz. [Visual Studio aboneleri Için aylık Azure kredisi](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Aksi takdirde, [ücretsiz bir deneme hesabı oluşturmayı](https://azure.microsoft.com/pricing/free-trial/)öğrenin.  
* **Azure CLI** -en son [Azure CLI](/cli/azure/install-azure-cli) 'Nın yüklü olduğundan ve Azure hesabınızda [az Login](/cli/azure/reference-index)ile oturum açtığınızdan emin olun.
* **Bir. vhd dosyasında yüklü OpenBSD işletim sistemi** -sanal bir sabit diske desteklenen bir OpenBSD işletim sistemi ([6,2 sürüm AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.2/amd64/)) yüklenmelidir. . Vhd dosyaları oluşturmak için birden çok araç vardır. Örneğin,. vhd dosyasını oluşturmak ve işletim sistemini yüklemek için Hyper-V gibi bir sanallaştırma çözümünü kullanabilirsiniz. Hyper-V ' y i yüklemek ve kullanmak hakkında yönergeler için bkz. [Hyper-v ' y i yüklemek ve bir sanal makine oluşturmak](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Azure için OpenBSD görüntüsünü hazırlama
Hyper-V desteği eklenen OpenBSD işletim sistemi 6,1 ' i yüklediğiniz VM 'de aşağıdaki yordamları izleyin:

1. Yükleme sırasında DHCP etkinleştirilmemişse, hizmeti aşağıdaki gibi etkinleştirin:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Seri konsolunu aşağıdaki şekilde ayarlayın:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Paket yüklemesini aşağıdaki şekilde yapılandırın:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Varsayılan olarak, `root` Kullanıcı Azure 'daki sanal makinelerde devre dışıdır. Kullanıcılar, OpenBSD VM 'deki `doas` komutunu kullanarak yükseltilmiş ayrıcalıklarla komutlar çalıştırabilir. Doas varsayılan olarak etkindir. Daha fazla bilgi için bkz. [doas. conf](https://man.openbsd.org/doas.conf.5). 

5. Azure Aracısı için önkoşulları aşağıdaki gibi yükleyip yapılandırın:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Azure aracısının en son sürümü her zaman [GitHub](https://github.com/Azure/WALinuxAgent/releases)'da bulunabilir. Aracıyı aşağıdaki gibi yüklemelisiniz:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Azure Agent 'ı yükledikten sonra aşağıdaki gibi çalıştığını doğrulamanız iyi bir fikirdir:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Sistemin temizlenmesi için sağlamayı kaldırma ve yeniden sağlama için uygun hale getirme. Aşağıdaki komut, sağlanan son kullanıcı hesabını ve ilişkili verileri de siler:

    ```sh
    waagent -deprovision+user -force
    ```

Artık sanal makinenizin kapatılmasını sağlayabilirsiniz.


## <a name="prepare-the-vhd"></a>VHD 'YI hazırlama
VHDX biçimi Azure 'da desteklenmiyor, yalnızca **sabıt VHD**. Hyper-V Yöneticisi 'Ni veya PowerShell [Convert-VHD](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) cmdlet 'ini kullanarak DISKI sabit vhd biçimine dönüştürebilirsiniz. Aşağıda bir örnek verilmiştir.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Depolama kaynakları oluşturma ve karşıya yükleme
Öncelikle [az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur:

```azurecli
az group create --name myResourceGroup --location eastus
```

VHD 'nizi karşıya yüklemek için [az Storage Account Create](/cli/azure/storage/account)komutuyla bir depolama hesabı oluşturun. Depolama hesabı adları benzersiz olmalıdır, bu nedenle kendi adınızı sağlayın. Aşağıdaki örnek, *mystorageaccount*adlı bir depolama hesabı oluşturur:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Depolama hesabına erişimi denetlemek için [az Storage Account Keys List](/cli/azure/storage/account/keys) ile depolama anahtarını şu şekilde edinin:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Karşıya yüklediğiniz VHD 'leri mantıksal olarak ayırmak için [az Storage Container Create](/cli/azure/storage/container)ile depolama hesabı içinde bir kapsayıcı oluşturun:

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Son olarak, VHD 'nizi [az Storage blob upload](/cli/azure/storage/blob) ile aşağıdaki gibi karşıya yükleyin:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>VHD 'nizden VM oluşturma
[Örnek bir betikle](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) veya doğrudan [az VM Create](/cli/azure/vm)ile bir VM oluşturabilirsiniz. Karşıya yüklediğiniz OpenBSD VHD 'sini belirtmek için `--image` parametresini aşağıdaki gibi kullanın:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

OpenBSD sanal makinenizin IP adresini [az VM List-ip-addresses](/cli/azure/vm) ile aşağıdaki gibi alın:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Artık OpenBSD sanal makinenize normal olarak SSH ekleyebilirsiniz:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Sonraki adımlar
OpenBSD 6.1 üzerinde Hyper-V desteği hakkında daha fazla bilgi edinmek istiyorsanız [openbsd 6,1](https://www.openbsd.org/61.html) ve [hyperv. 4](https://man.openbsd.org/hyperv.4)makalesini okuyun.

Yönetilen diskten bir VM oluşturmak istiyorsanız [az disk](/cli/azure/disk)' i okuyun. 
