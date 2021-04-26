---
title: 'Hızlı başlangıç: Azure CLı kullanarak bir Linux VM oluşturma'
description: Bu hızlı başlangıçta Azure CLI’yi kullanarak Linux sanal makinesi oluşturmayı öğrenirsiniz
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 03/30/2021
ms.author: cynthn
ms.custom:
- mvc
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
ms.openlocfilehash: 3e8c1a06244c46f5789506e8a77d410f5493bbf3
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058587"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>Hızlı Başlangıç: Azure CLI ile Linux sanal makinesi oluşturma

Bu hızlı başlangıç, Azure 'da bir Linux sanal makinesini (VM) dağıtmak için Azure komut satırı arabirimi 'ni (CLı) nasıl kullanacağınızı gösterir. Azure CLI, komut satırından veya betik içindeki Azure kaynaklarını oluşturmak ve yönetmek için kullanılır.

Bu öğreticide, en son Ubuntu LTS görüntüsünü yükleyeceğiz. VM'nin çalıştığını görmek için SSH kullanarak bağlanacak ve NGINX web sunucusunu yükleyeceksiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de açabilirsiniz [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kod bloklarını kopyalamak için **Kopyala** ' yı seçin, Cloud Shell yapıştırın ve çalıştırmak için **ENTER** ' u seçin.

CLI'yi yerel olarak yükleyip kullanmayı tercih ediyorsanız bu hızlı başlangıç için Azure CLI 2.0.30 veya sonraki bir sürümü gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Sanal makine oluşturma

[az vm create](/cli/azure/vm) komutuyla bir sanal makine oluşturun.

Aşağıdaki örnek, *myVM* adlı bir VM oluşturur ve *azureuser* adlı bir kullanıcı hesabı ekler. `--generate-ssh-keys`Parametresi, otomatik olarak BIR SSH anahtarı oluşturmak ve varsayılan anahtar konumuna (*~/PST*) koymak için kullanılır. Bunun yerine belirli bir anahtar kümesini kullanmak için `--ssh-key-value` seçeneğini kullanın.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM’yi ve destekleyici kaynakları oluşturmak birkaç dakika sürer. Aşağıdaki örnekte VM oluşturma işleminin başarılı olduğu gösterilmektedir.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

VM’nizdeki çıktıda `publicIpAddress` değerini not alın. Sonraki adımlarda bu adres, VM’ye erişmek için kullanılır.

## <a name="open-port-80-for-web-traffic"></a>Web trafiği için 80 numaralı bağlantı noktasını açın

Azure üzerinde bir Linux VM oluşturduğunuzda varsayılan olarak, yalnızca SSH bağlantıları açılır. NGINX web sunucusuyla kullanacağınız TCP bağlantı noktası 80’i açmak için [az vm open-port](/cli/azure/vm) komutunu kullanın:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Sanal makineye bağlanma

Normal şekilde VM’nize SSH’leyin. Örnekteki IP adresini, önceki çıktıda belirtildiği gibi, sanal makinenizin genel IP adresiyle değiştirin:

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-web-server"></a>Web sunucusunu yükleme

Sanal makinenizin çalıştığını görmek için NGINX web sunucusunu yükleyin. Paket kaynaklarınızı güncelleştirip en son NGINX paketini yükleyin.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

İşlemi tamamladığınızda `exit` yazarak SSH oturumunu kapatın.

## <a name="view-the-web-server-in-action"></a>Web sunucusunun çalıştığını görme

İstediğiniz web tarayıcısını kullanarak varsayılan NGINX karşılama sayfasını görüntüleyin. Web adresi olarak VM'nizin genel IP adresini kullanın. Aşağıdaki örnekte varsayılan NGINX web sitesi gösterilir:

![NGINX karşılama sayfasını görüntüleme](./media/quick-create-cli/view-the-nginx-welcome-page.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az group delete](/cli/azure/group) komutunu kullanarak kaynak grubunu, VM’yi ve tüm ilgili kaynakları kaldırabilirsiniz. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, basit bir sanal makine dağıttınız, web trafiği için bir ağ bağlantı noktası açtınız ve temel bir web sunucusu yüklediniz. Azure sanal makineleri hakkında daha fazla bilgi için Linux VM’lerine yönelik öğreticiye geçin.


> [!div class="nextstepaction"]
> [Azure Linux sanal makine öğreticileri](./tutorial-manage-vm.md)
