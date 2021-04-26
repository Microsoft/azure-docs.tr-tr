---
title: Azure depolama 'ya veri alımını kısıtlama-Azure CLı
description: Bu makalede, Azure CLı kullanarak sanal ağ hizmeti uç noktası ilkeleriyle sanal ağ verilerini Azure depolama kaynaklarına sınırlamayı ve sınırlamayı öğreneceksiniz.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: fb48cbeb842fd9251067f9b8c4c83694e9749505
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063755"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Azure CLı kullanarak sanal ağ hizmeti uç noktası ilkeleriyle Azure depolama hesaplarına veri alımını yönetme

Sanal ağ hizmeti uç noktası ilkeleri, Azure depolama hesaplarında hizmet uç noktaları üzerinden bir sanal ağ içinden erişim denetimi uygulamanıza olanak tanır. Bu, iş yüklerinizi güvenli hale getirmenin, hangi depolama hesaplarına izin verileceğini ve veri ayıklanma izin verileceğini yönetme anahtarıdır.
Bu makalede şunları öğreneceksiniz:

* Bir sanal ağ oluşturun ve bir alt ağ ekleyin.
* Azure depolama için hizmet uç noktasını etkinleştirin.
* İki Azure depolama hesabı oluşturun ve yukarıda oluşturulan alt ağdan ağ erişimine izin verin.
* Yalnızca depolama hesaplarından birine erişime izin vermek için bir hizmet uç noktası ilkesi oluşturun.
* Alt ağa bir sanal makine (VM) dağıtın.
* Alt ağdan izin verilen depolama hesabına erişimi onaylayın.
* Alt ağdan izin verilmeyen depolama hesabına erişimin reddedildiğini doğrulayın.

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
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Hizmet uç noktasını girin 

Bu örnekte, alt ağ *özel* için *Microsoft. Storage* için bir hizmet uç noktası oluşturulur: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
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

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Azure depolama hesaplarına ağ erişimini kısıtlama

Bu bölümde, hizmet uç noktası aracılığıyla bir sanal ağ içindeki verilen alt ağdan bir Azure depolama hesabı için ağ erişimini kısıtlama adımları listelenmektedir.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

[Az Storage Account Create](/cli/azure/storage/account)komutuyla iki Azure depolama hesabı oluşturun.

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Depolama hesapları oluşturulduktan sonra, [az Storage Account Show-Connection-String](/cli/azure/storage/account)ile depolama hesaplarının bağlantı dizesini bir değişkene alın. Bağlantı dizesi, sonraki bir adımda dosya paylaşma oluşturmak için kullanılır.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Değişkenin içeriğini görüntüleyin ve sonraki bir adımda kullanıldığından çıktıda döndürülen **Accountkey** değerini aklınızda edin.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Depolama hesabında dosya paylaşımı oluşturma

Depolama hesabında [az Storage Share Create](/cli/azure/storage/share)komutuyla bir dosya paylaşma oluşturun. Sonraki bir adımda, bu dosya paylaşımının ağa erişimi onaylamak için bağlanmış olması gerekir.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>Depolama hesabına tüm ağ erişimini reddet

Varsayılan olarak, depolama hesapları herhangi bir ağdaki istemcilerden gelen ağ bağlantılarını kabul eder. Seçilen ağlara erişimi sınırlandırmak için, varsayılan eylemi [az Storage Account Update](/cli/azure/storage/account)ile *Reddet* olarak değiştirin. Ağ erişimi reddedildiğinde depolama hesabı hiçbir ağdan erişilebilir olmaz.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Sanal ağ alt ağından ağ erişimini etkinleştir

[Az Storage Account Network-Rule Add](/cli/azure/storage/account/network-rule)ile *özel* alt ağdan depolama hesabına ağ erişimine izin verin.

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Geçerli depolama hesabına erişime izin vermek için ilkeyi Uygula

Azure hizmet uç noktası ilkeleri yalnızca Azure Storage 'da kullanılabilir. Bu nedenle, bu alt ağda *Microsoft. Storage* Için hizmet uç noktası 'nı Bu örnek kurulumda etkinleştiriyoruz.

Hizmet uç noktası ilkeleri hizmet uç noktaları üzerinden uygulanır. Bir hizmet uç noktası ilkesi oluşturarak başlayacağız. Daha sonra bu alt ağ için onaylanacak Azure depolama hesaplarının bu ilkesinde ilke tanımlarını oluşturacağız

Hizmet uç noktası ilkesi oluşturma

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Bir değişkende izin verilen depolama hesabı için kaynak URI 'sini kaydedin. Aşağıdaki komutu yürütmeden önce, *\<your-subscription-id>* ABONELIK kimliğinizin gerçek değeriyle değiştirin.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Yukarıdaki Azure Storage hesabının hizmet uç noktası ilkesine izin vermek için bir ilke tanımı oluşturun & ekleyin

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

Ve sanal ağ alt ağını, önceki adımda oluşturulan hizmet uç noktası ilkesiyle ilişkilendirilecek şekilde güncelleştirin

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Azure depolama hesaplarına erişim kısıtlamasını doğrulama

### <a name="create-the-virtual-machine"></a>Sanal makineyi oluşturma

Bir depolama hesabına ağ erişimini test etmek için alt ağa bir VM dağıtın.

[Az VM Create](/cli/azure/vm)komutuyla *özel* alt ağda bir VM oluşturun. SSH anahtarları, varsayılan anahtar konumunda zaten mevcut değilse komut bunları oluşturur. Belirli bir anahtar kümesini kullanmak için `--ssh-key-value` seçeneğini kullanın.

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

### <a name="confirm-access-to-storage-account"></a>Depolama hesabına erişimi onaylama

*MyVmPrivate* VM 'ye SSH. *\<publicIpAddress>* *MyVmPrivate* sanal makinenizin genel IP adresi ile değiştirin.

```bash 
ssh <publicIpAddress>
```

Bağlama noktası için bir klasör oluşturun:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Azure dosya paylaşımından oluşturduğunuz dizine bağlayın. Aşağıdaki komutu yürütmeden önce, *\<storage-account-key>* **$saConnectionString 1**' den *accountkey* değeri ile değiştirin.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

`user@myVmPrivate:~$`İstemi alırsınız. Azure dosya paylaşımı, */mnt/myazurefileshare* öğesine başarıyla bağlandı.

### <a name="confirm-access-is-denied-to-storage-account"></a>Depolama hesabına erişimin reddedildiğini onaylama

Aynı VM *myVmPrivate*, bağlama noktası için bir dizin oluşturun:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Azure dosya paylaşımından *notallowedstorageacc* depolama hesabından oluşturduğunuz dizine bağlama girişimi. Bu makalede Ubuntu 'ın en son sürümünü dağıttığınız varsayılmaktadır. Ubuntu 'nun önceki sürümlerini kullanıyorsanız, dosya paylaşımları bağlama hakkında ek yönergeler için bkz. [Linux üzerinde bağlama](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . 

Aşağıdaki komutu yürütmeden önce, *\<storage-account-key>* **$saConnectionString 2**' den *accountkey* değeri ile değiştirin.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Erişim engellendi ve `mount error(13): Permission denied` Bu depolama hesabı, alt ağa uyguladığımız hizmet uç noktası ilkesinin izin verilenler listesinde olmadığından bir hata alıyorsunuz. 

SSH oturumundan *myVmPublic* VM 'sine çıkın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az Group Delete](/cli/azure) ' i kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırın.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure depolama 'ya bir Azure sanal ağ hizmeti uç noktası üzerinden bir hizmet uç noktası İlkesi uyguladınız. Azure depolama hesaplarını ve sınırlı ağ erişimini bir sanal ağ alt ağından yalnızca belirli depolama hesaplarına (ve bu nedenle reddedilir) oluşturdunuz. Hizmet uç noktası ilkeleri hakkında daha fazla bilgi için bkz. [hizmet uç noktası ilkelerine genel bakış](virtual-network-service-endpoint-policies-overview.md).