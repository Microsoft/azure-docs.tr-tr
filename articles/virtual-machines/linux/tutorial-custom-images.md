---
title: Öğretici-Azure CLı ile özel VM görüntüleri oluşturma
description: Bu öğreticide, Azure CLI kullanarak Azure’da özel sanal makine görüntüsü oluşturmayı öğrenirsiniz
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e629c605c0ffd3a7e0e1e53c3d661642b9dd01b7
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034508"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Öğretici: Azure CLI ile bir Azure VM'nin özel görüntüsünü oluşturma

Özel görüntüler market görüntüleri gibidir, ancak bunları kendiniz oluşturursunuz. Özel görüntüler, uygulamaları, uygulama yapılandırmalarını ve diğer işletim sistemi yapılandırmalarını önceden yükleme gibi yapılandırmaları önyüklemek için kullanılabilir. Bu öğreticide, bir Azure sanal makinesine ait kendi özel görüntünüzü oluşturursunuz. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * VM’lerin sağlamasını kaldırma ve VM’leri genelleştirme
> * Özel görüntü oluşturma
> * Özel görüntüden VM oluşturma
> * Aboneliğinizdeki tüm görüntüleri listeleme
> * Görüntü silme

Bu öğretici, en son sürüme sürekli olarak güncellenen [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)içindeki CLI 'yi kullanır. Cloud Shell açmak için herhangi bir kod bloğunun en üstünden **deneyin** ' i seçin.

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.30 veya sonraki bir sürümünü çalıştırmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Başlamadan önce

Aşağıdaki adımlarda, mevcut bir VM'yi alıp yeni VM örnekleri oluşturmak için kullanabileceğiniz yeniden kullanılabilir bir özel görüntüye dönüştürme işlemi ayrıntılı olarak açıklanmıştır.

Bu öğreticideki örneği tamamlamak için, mevcut bir sanal makinenizin olması gerekir. Gerekirse, bu [betik örneği](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) sizin için bir tane oluşturabilir. Bu öğreticide çalışırken, gerektiğinde kaynak grubu ve VM adlarını değiştirin.

## <a name="create-a-custom-image"></a>Özel görüntü oluşturma

Sanal makinenin bir görüntüsünü oluşturmak için, kaynak VM’nin sağlamasını kaldırarak, kaynak VM’yi serbest bırakarak ve ardından genelleştirilmiş olarak işaretleyerek VM’yi hazırlamanız gerekir. VM hazırlandıktan sonra, görüntü oluşturabilirsiniz.

### <a name="deprovision-the-vm"></a>VM’nin sağlamasını kaldırma 

Sağlamayı kaldırma işlemi, makineye özgü bilgileri kaldırarak VM’yi genelleştirir. Bu genelleştirme, tek bir görüntüden birçok VM dağıtmayı mümkün kılar. Sağlamayı kaldırma sırasında, ana bilgisayar adı sıfırlanarak *localhost.localdomain* olur. SSH ana bilgisayar anahtarları, ad sunucusu yapılandırmaları, kök parolası ve önbelleğe alınan DHCP kiraları da ayrıca silinir.

> [!WARNING]
> VM 'nin genelleştirilmesi ve kaldırılması, kaynak VM 'nin kullanılamamasına neden olur ve yeniden başlatılamaz. 

VM’nin sağlamasını kaldırmak için, Azure VM aracısını (waagent) kullanın. Azure VM aracısı, VM’de yüklüdür ve sağlamayı ve Azure Yapı Denetleyicisi ile etkileşimi yönetir. Daha fazla bilgi için bkz. [Azure Linux Aracısı kullanıcı kılavuzu](../extensions/agent-linux.md).

SSH kullanarak VM'nize bağlanın ve VM’nin sağlamasını kaldırmak için komutu çalıştırın. `+user` bağımsız değişkeniyle, son sağlanan kullanıcı hesabı ve ilişkili tüm veriler de ayrıca silinir. Örnek IP adresini, sanal makinenizin genel IP adresi ile değiştirin.

Sanal Makineye SSH uygulayın.
```bash
ssh azureuser@52.174.34.95
```
VM’nin sağlamasını kaldırın.

```bash
sudo waagent -deprovision+user -force
```
SSH oturumu kapatın.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>VM’yi serbest bırakma ve genelleştirilmiş olarak işaretleme

Bir görüntü oluşturmak için VM’nin serbest bırakılması gerekir. [az vm deallocate](/cli//azure/vm) komutunu kullanarak VM’yi serbest bırakın. 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Son olarak da, Azure platformunun VM’nin genelleştirilmiş olduğunu bilmesi için [az vm generalize](/cli//azure/vm) komutunu kullanarak VM’nin durumunu genelleştirilmiş olarak ayarlayın. Yalnızca genelleştirilmiş bir VM’den görüntü oluşturabilirsiniz.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>Görüntü oluşturma

Artık [az image create](/cli//azure/image) komutunu kullanarak VM’nin görüntüsünü oluşturabilirsiniz. Aşağıdaki örnek, *myVM* adlı bir VM’den *myImage* adlı bir görüntü oluşturur.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Görüntüden VM oluşturma

Artık bir görüntünüz olduğuna göre, [az vm create](/cli/azure/vm) komutunu kullanarak görüntüden bir veya daha fazla yeni VM oluşturabilirsiniz. Aşağıdaki örnek, *myImage* adlı görüntüden *myVMfromImage* adlı bir VM oluşturur.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Tek bir görüntüden 20 VM 'ye eş zamanlı dağıtım sayısını kısıtlamanızı öneririz. Aynı özel görüntüden 20 ' den fazla VM 'ye ait büyük ölçekli, eşzamanlı dağıtımlar planlarken, birden çok görüntü çoğaltmasıyla paylaşılan bir [görüntü Galerisi](shared-image-galleries.md) kullanmanız gerekir. 

## <a name="image-management"></a>Görüntü yönetimi 

Burada, ortak görüntü yönetimi görevleri ve Azure CLI kullanarak bunların nasıl tamamlanacağını gösteren bazı örnekler verilmiştir.

Tüm görüntüleri adlara göre tablo biçiminde listeleyin.

```azurecli-interactive 
az image list \
    --resource-group myResourceGroup
```

Görüntüyü silin. Bu örnek, *myResourceGroup* kaynak grubundan *myOldImage* adlı görüntüyü siler.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, özel bir VM görüntüsü oluşturdunuz. Şunları öğrendiniz:

> [!div class="checklist"]
> * VM’lerin sağlamasını kaldırma ve VM’leri genelleştirme
> * Özel görüntü oluşturma
> * Özel görüntüden VM oluşturma
> * Aboneliğinizdeki tüm görüntüleri listeleme
> * Görüntü silme

Yüksek oranda kullanılabilir sanal makineler hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Yüksek oranda kullanılabilir VM’ler oluşturun](tutorial-availability-sets.md).

