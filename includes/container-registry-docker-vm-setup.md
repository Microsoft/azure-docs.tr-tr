---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: 429377cd50e83195cb1c3a422416fdb35644a28e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773480"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Docker özellikli bir sanal makine oluşturma

Test amacıyla, bir Azure Container Registry 'ye erişmek için Docker özellikli bir Ubuntu VM kullanın. Kayıt defterine Azure Active Directory kimlik doğrulaması kullanmak için [Azure CLI][azure-cli] 'yı sanal makineye de yüklersiniz. Zaten bir Azure sanal makineniz varsa, bu oluşturma adımını atlayın.

Sanal makineniz ve kapsayıcı kayıt defteriniz için aynı kaynak grubunu kullanabilirsiniz. Bu kurulum, sonda temizleme işlemini basitleştirir, ancak gerekli değildir. Sanal makine ve sanal ağ için ayrı bir kaynak grubu oluşturmayı seçerseniz, [az Group Create][az-group-create]komutunu çalıştırın. Aşağıdaki örnek, kaynak grubu adı ve kayıt defteri konumu için ortam değişkenlerini ayarlamış olduğunuzu varsayar:

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

Şimdi [az VM Create][az-vm-create]ile varsayılan bir Ubuntu Azure sanal makinesini dağıtın. Aşağıdaki örnek, *Mydockervm* ADLı bir VM oluşturur.

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM’nin oluşturulması birkaç dakika sürer. Komut tamamlandığında, `publicIpAddress` Azure CLI tarafından görüntülendiğine göz atın. VM 'ye SSH bağlantısı oluşturmak için bu adresi kullanın.

### <a name="install-docker-on-the-vm"></a>SANAL makineye Docker 'ı yükler

VM çalıştırıldıktan sonra VM ile bir SSH bağlantısı oluşturun. *Publicıpaddress* değerini sanal MAKINENIZIN genel IP adresiyle değiştirin.

```bash
ssh azureuser@publicIpAddress
```

Ubuntu VM 'ye Docker yüklemek için aşağıdaki komutları çalıştırın:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Yükleme sonrasında, Docker 'ın sanal makinede düzgün çalıştığını doğrulamak için aşağıdaki komutu çalıştırın:

```bash
sudo docker run -it hello-world
```

Çıkış:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure CLI'yi yükleme

Azure CLI 'yi, Ubuntu sanal makinenize yüklemek için [apt Ile Azure CLI 'Yı kurma](/cli/azure/install-azure-cli-apt) bölümündeki adımları izleyin. Örnek:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

SSH bağlantısından çıkın.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-group-create]: /cli/azure/group