---
title: PaaS kaynaklarına ağ erişimini kısıtlama-Azure CLı
description: Bu makalede, Azure depolama ve Azure SQL veritabanı gibi Azure kaynaklarına ağ erişimini, Azure CLı kullanan sanal ağ hizmet uç noktaları ile sınırlama ve kısıtlama hakkında bilgi edineceksiniz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 88c7b210e5fde7faafaf99af3fafab7b1cfd026a
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057329"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Azure CLı kullanarak sanal ağ hizmet uç noktaları ile PaaS kaynaklarına ağ erişimini kısıtlama

Sanal ağ hizmet uç noktaları bazı Azure hizmet uç noktalarına ağ erişimini bir sanal ağ alt ağı ile sınırlamanıza olanak tanır. Ayrıca, kaynaklara internet erişimini de kaldırabilirsiniz. Hizmet uç noktaları, sanal ağınızdan desteklenen Azure hizmetlerine doğrudan bağlantı sağlar, böylece Azure hizmetlerine erişmek için sanal ağınızın özel adres alanını kullanabilirsiniz. Hizmet uç noktaları aracılığıyla Azure kaynaklarına gönderilen trafik her zaman Microsoft Azure omurga ağı üzerinde kalır. Bu makalede şunları öğreneceksiniz:

* Alt ağ ile sanal ağ oluşturma
* Alt ağ ekleme ve hizmet uç noktasını etkinleştirme
* Azure kaynağı oluşturma ve yalnızca bir alt ağdan ağ erişimine izin verme
* Her alt ağa bir sanal makine (VM) dağıtma
* Bir alt ağdan kaynağa erişimi onaylama
* Bir alt ağdan ve internetten kaynağa erişimin reddedildiğini onaylama

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.28 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

Bir sanal ağ oluşturmadan önce, sanal ağ ve bu makalede oluşturulan tüm diğer kaynaklar için bir kaynak grubu oluşturmanız gerekir. [az group create](/cli/azure/group) ile bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

[Az Network VNET Create](/cli/azure/network/vnet)komutuyla bir alt ağa sahip bir sanal ağ oluşturun.

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Hizmet uç noktasını girin 

Hizmet uç noktalarını yalnızca hizmet uç noktalarını destekleyen hizmetler için etkinleştirebilirsiniz. [Az Network VNET List-Endpoint-Services](/cli/azure/network/vnet)Ile bir Azure konumunda bulunan hizmet uç noktası etkin hizmetlerini görüntüleyin. Aşağıdaki örnek, *eastus* bölgesinde kullanılabilir olan hizmet uç noktası etkin hizmetleri listesini döndürür. Daha fazla Azure hizmeti uç noktası etkinleştirildiğinden, döndürülen hizmetlerin listesi zaman içinde büyüyerek artar.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

[Az Network VNET subnet Create](/cli/azure/network/vnet/subnet)komutuyla sanal ağda ek bir alt ağ oluşturun. Bu örnekte, alt ağ için *Microsoft. Storage* için bir hizmet uç noktası oluşturulur: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Bir kaynak için ağ erişimini kısıtlama

[Az Network NSG Create](/cli/azure/network/nsg)komutuyla bir ağ güvenlik grubu oluşturun. Aşağıdaki örnek, *Mynsgprivate* adlı bir ağ güvenlik grubu oluşturur.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Ağ güvenlik grubunu, [az Network VNET subnet Update](/cli/azure/network/vnet/subnet)ile *özel* alt ağ ile ilişkilendirin. Aşağıdaki örnek, *Mynsgprivate* ağ güvenlik grubunu *özel* alt ağla ilişkilendirir:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

[Az Network NSG Rule Create](/cli/azure/network/nsg/rule)ile güvenlik kuralları oluşturun. Aşağıdaki kural, Azure depolama hizmetine atanan genel IP adreslerine giden erişime izin verir: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

Her ağ güvenlik grubu, çeşitli [varsayılan güvenlik kuralları](./network-security-groups-overview.md#default-security-rules)içerir. Aşağıdaki kural, tüm genel IP adreslerine giden erişime izin veren bir varsayılan güvenlik kuralını geçersiz kılar. `destination-address-prefix "Internet"`Seçeneği tüm genel IP adreslerine giden erişimi reddeder. Önceki kural, Azure depolama 'nın genel IP adreslerine erişim sağlayan daha yüksek öncelikli bir nedenle bu kuralı geçersiz kılar.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

Aşağıdaki kural, alt ağa gelen SSH trafiğinin her yerden çalışmasına izin verir. Kural, internetten gelen tüm trafiği engelleyen bir varsayılan güvenlik kuralını geçersiz kılar. Daha sonraki bir adımda bağlantının test edileceği şekilde, SSH alt ağa izin verilir.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>Bir kaynağa ağ erişimini kısıtlama

Hizmet uç noktaları için etkinleştirilmiş Azure hizmetleri aracılığıyla oluşturulan kaynaklara ağ erişimini kısıtlamak için gereken adımlar, hizmetler arasında farklılık gösterir. Bir hizmete yönelik belirli adımlar için ilgili hizmetin belgelerine bakın. Bu makalenin geri kalanında bir Azure depolama hesabı için ağ erişimini bir örnek olarak kısıtlama adımları yer alır.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

[Az Storage Account Create](/cli/azure/storage/account)komutuyla bir Azure depolama hesabı oluşturun. `<replace-with-your-unique-storage-account-name>`Yalnızca rakamlar ve küçük harfler kullanarak, 3-24 karakter uzunluğunda tüm Azure konumlarında benzersiz olan bir adla değiştirin.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Depolama hesabı oluşturulduktan sonra, [az Storage Account Show-Connection-String](/cli/azure/storage/account)ile depolama hesabının bağlantı dizesini bir değişkene alın. Bağlantı dizesi, sonraki bir adımda dosya paylaşma oluşturmak için kullanılır.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Değişkenin içeriğini görüntüleyin ve sonraki bir adımda kullanıldığından çıktıda döndürülen **Accountkey** değerini aklınızda edin.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Depolama hesabında dosya paylaşımı oluşturma

Depolama hesabında [az Storage Share Create](/cli/azure/storage/share)komutuyla bir dosya paylaşma oluşturun. Sonraki bir adımda, bu dosya paylaşımının ağa erişimi onaylamak için bağlanmış olması gerekir.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Bir depolama hesabına tüm ağ erişimini reddet

Varsayılan olarak, depolama hesapları herhangi bir ağdaki istemcilerden gelen ağ bağlantılarını kabul eder. Seçilen ağlara erişimi sınırlandırmak için, varsayılan eylemi [az Storage Account Update](/cli/azure/storage/account)ile *Reddet* olarak değiştirin. Ağ erişimi reddedildiğinde depolama hesabı hiçbir ağdan erişilebilir olmaz.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Bir alt ağdan ağ erişimini etkinleştirme

[Az Storage Account Network-Rule Add](/cli/azure/storage/account/network-rule)ile *özel* alt ağdan depolama hesabına ağ erişimine izin verin.

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Bir depolama hesabına ağ erişimini test etmek için her alt ağa bir VM dağıtın.

### <a name="create-the-first-virtual-machine"></a>İlk sanal makineyi oluşturma

[Az VM Create](/cli/azure/vm)komutuyla *ortak* alt ağda bir VM oluşturun. SSH anahtarları, varsayılan anahtar konumunda zaten mevcut değilse komut bunları oluşturur. Belirli bir anahtar kümesini kullanmak için `--ssh-key-value` seçeneğini kullanın.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

Sanal makinenin oluşturulması birkaç dakika sürer. VM oluşturulduktan sonra Azure CLı, aşağıdaki örneğe benzer bilgiler gösterir: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Döndürülen çıktıda **Publicıpaddress** ' i bir yere göz atın. Bu adres, sonraki bir adımda sanal makineye internet 'ten erişmek için kullanılır.

### <a name="create-the-second-virtual-machine"></a>İkinci sanal makineyi oluşturma

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

Sanal makinenin oluşturulması birkaç dakika sürer. Oluşturulduktan sonra, döndürülen çıktıda **Publicıpaddress** ' i göz önünde atın. Bu adres, sonraki bir adımda sanal makineye internet 'ten erişmek için kullanılır.

## <a name="confirm-access-to-storage-account"></a>Depolama hesabına erişimi onaylama

*MyVmPrivate* VM 'ye SSH. *\<publicIpAddress>* *MyVmPrivate* sanal makinenizin genel IP adresi ile değiştirin.

```bash 
ssh <publicIpAddress>
```

Bağlama noktası için bir klasör oluşturun:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Azure dosya paylaşımından oluşturduğunuz dizine bağlayın. Aşağıdaki komutu çalıştırmadan önce, `<storage-account-name>` hesap adıyla ve `<storage-account-key>` [depolama hesabı oluşturma](#create-a-storage-account)bölümünde aldığınız anahtarla değiştirin.

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

`user@myVmPrivate:~$`İstemi alırsınız. Azure dosya paylaşımı, */mnt/myazurefileshare* öğesine başarıyla bağlandı.

VM 'nin herhangi bir genel IP adresine giden bağlantısı olmadığını doğrulayın:

```bash
ping bing.com -c 4
```

*Özel* alt ağ ile ilişkili ağ güvenlik grubu Azure Depolama hizmetine atanan adreslerden başka genel IP adreslerine giden erişime izin vermediği için bir yanıt almazsınız.

SSH oturumundan *myVmPrivate* VM 'sine çıkın.

## <a name="confirm-access-is-denied-to-storage-account"></a>Depolama hesabına erişimin reddedildiğini onaylama

*MyVmPublic* VM Ile bir SSH oturumu oluşturmak için aşağıdaki komutu kullanın. `<publicIpAddress>` *MyVmPublic* sanal makinenizin genel IP adresiyle değiştirin: 

```bash 
ssh <publicIpAddress>
```

Bağlama noktası için dizin oluşturma:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Azure dosya paylaşımından oluşturduğunuz dizine bağlama girişimi. Bu makalede Ubuntu 'ın en son sürümünü dağıttığınız varsayılmaktadır. Ubuntu 'nun önceki sürümlerini kullanıyorsanız, dosya paylaşımları bağlama hakkında ek yönergeler için bkz. [Linux üzerinde bağlama](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Aşağıdaki komutu çalıştırmadan önce, `<storage-account-name>` hesap adıyla ve `<storage-account-key>` [depolama hesabı oluşturma](#create-a-storage-account)bölümünde aldığınız anahtarla değiştirin:

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Erişim reddedilir ve `mount error(13): Permission denied` *MyVmPublic* VM *ortak* alt ağ içinde dağıtıldığından bir hata alırsınız. *Genel* alt ağında Azure Depolama için etkinleştirilmiş bir hizmet uç noktası bulunmaz ve depolama hesabı *Genel* alt ağından değil, yalnızca *Özel* alt ağından ağ erişimine izin verir.

SSH oturumundan *myVmPublic* VM 'sine çıkın.

Bilgisayarınızdan, [az Storage Share List](/cli/azure/storage/share)komutuyla Depolama hesabınızdaki paylaşımları görüntülemeyi deneyin. Ve depolama hesabı `<account-name>` `<account-key>` [Oluştur](#create-a-storage-account)' dan depolama hesabı adını ve anahtarını değiştirin:

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Erişim engellendi ve bu istek, *MyVirtualNetwork* sanal ağının *özel* alt ağında olmadığından, bu *işlemi gerçekleştirme yetkisi yok* .

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az Group Delete](/cli/azure) ' i kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırın.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir sanal ağ alt ağı için bir hizmet uç noktası etkinleştirdiniz. Hizmet uç noktalarının birden fazla Azure hizmeti ile dağıtılmış kaynaklar için etkinleştirilebildiğini öğrendiniz. Bir Azure Depolama hesabı oluşturdunuz ve depolama hesabına ağ erişimini yalnızca bir sanal ağ alt ağındaki kaynaklarla sınırladınız. Hizmet uç noktaları hakkında daha fazla bilgi için bkz. [Hizmet uç noktalarına genel bakış](virtual-network-service-endpoints-overview.md) ve [Alt ağları yönetme](virtual-network-manage-subnet.md).

Hesabınızda birden fazla sanal ağ varsa, her bir sanal ağın içindeki kaynakların birbiriyle iletişim kurabilmesi iki sanal ağı birbirine bağlamak isteyebilirsiniz. Nasıl yapılacağını öğrenmek için bkz. [sanal ağları bağlama](tutorial-connect-virtual-networks-cli.md).
