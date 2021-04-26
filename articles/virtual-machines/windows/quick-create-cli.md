---
title: Hızlı başlangıç-Azure CLı kullanarak Windows VM oluşturma
description: Bu hızlı başlangıçta, Azure CLı kullanarak bir Windows sanal makinesi oluşturma hakkında bilgi edineceksiniz.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 769b42133fb894c916ecaa3c42dd7de85206c765
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556256"
---
# <a name="quickstart-create-a-windows-virtual-machine-with-the-azure-cli"></a>Hızlı Başlangıç: Azure CLI ile Windows sanal makinesi oluşturma

Azure CLI, komut satırından veya betik içindeki Azure kaynaklarını oluşturmak ve yönetmek için kullanılır. Bu hızlı başlangıçta Azure CLI kullanarak Azure’da Windows Server 2016 çalıştıran bir sanal makinenin (VM) nasıl dağıtılacağı gösterilir. VM’ye RDP oluşturup IIS web sunucusunu yükleyerek VM’nizin çalıştığını görebilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kod bloklarını kopyalamak için **Kopyala** ' yı seçin, Cloud Shell yapıştırın ve **ENTER** tuşuna basarak çalıştırın.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Sanal makine oluşturma

[az vm create](/cli/azure/vm) ile bir VM oluşturun. Aşağıdaki örnek, *myvm* ADLı bir VM oluşturur. Bu örnek, bir Yönetici Kullanıcı adı için *azureuser* kullanır. 

[Azure VM 'lerinin parola gereksinimlerini](./faq.md#what-are-the-password-requirements-when-creating-a-vm
)karşılayan bir parola sağlamanız gerekir. Aşağıdaki örneği kullanarak komut satırına bir parola girmeniz istenir. `--admin-password`Parolanız için bir değer ile parametresini de ekleyebilirsiniz. SANAL makineye bağlandığınızda Kullanıcı adı ve parola daha sonra kullanılacaktır.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser 
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
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

VM’nizdeki çıktıda `publicIpAddress` değerini not alın. Sonraki adımlarda bu adres, VM’ye erişmek için kullanılır.

## <a name="open-port-80-for-web-traffic"></a>Web trafiği için 80 numaralı bağlantı noktasını açın

Azure üzerinde bir Windows VM oluşturduğunuzda varsayılan olarak, yalnızca RDP bağlantıları açılır. IIS web sunucusuyla kullanacağınız TCP bağlantı noktası 80’i açmak için [az vm open-port](/cli/azure/vm) komutunu kullanın:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Sanal makineye bağlanma

Yerel bilgisayarınızdan bir uzak masaüstü oturumu oluşturmak için aşağıdaki komutu kullanın. IP adresini, sanal makinenizin genel IP adresi ile değiştirin. İstendiğinde, VM’yi oluştururken kullandığınız kimlik bilgilerini girin:

```powershell
mstsc /v:publicIpAddress
```

## <a name="install-web-server"></a>Web sunucusunu yükleme

Sanal makinenizin çalıştığını görmek için IIS web sunucusunu yükleyin. VM’de bir PowerShell istemi açın ve şu komutu çalıştırın:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

İşiniz bittiğinde, RDP’nin sanal makine bağlantısını kapatın.

## <a name="view-the-web-server-in-action"></a>Web sunucusunun çalıştığını görme

Sanal makinenizde İnternet’ten IIS yüklenmiş ve 80 numaralı bağlantı noktası açık olduğunda, varsayılan IIS karşılama sayfasını görüntülemek için tercih ettiğiniz bir web tarayıcısını kullanın. VM’nizin önceki bir adımda edinilen genel IP adresini kullanın. Aşağıdaki örnekte varsayılan IIS web sitesi gösterilir:

![Varsayılan IIS sitesi](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az Group Delete](/cli/azure/group) komutunu kullanarak kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırabilirsiniz:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, basit bir sanal makine dağıttınız, web trafiği için bir ağ bağlantı noktası açtınız ve temel bir web sunucusu yüklediniz. Azure sanal makineleri hakkında daha fazla bilgi için Windows VM’lerine yönelik öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Windows sanal makine öğreticileri](./tutorial-manage-vm.md)
