---
title: Azure CLı kullanarak Azure 'da Linux VM 'nin bir görüntüsünü yakalama
description: Azure CLı kullanarak toplu dağıtımlar için kullanmak üzere Azure VM 'nin bir görüntüsünü yakalayın.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: ed7d45fb9148bd441a3798c48be8b25e1da2b8c1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036915"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Bir sanal makinenin veya VHD 'nin görüntüsünü oluşturma

<!-- generalize, image - extended version of the tutorial-->

Azure 'da kullanmak üzere bir sanal makinenin (VM) birden çok kopyasını oluşturmak için VM 'nin veya işletim sistemi VHD 'sinin bir görüntüsünü yakalayın. Dağıtım için bir görüntü oluşturmak üzere kişisel hesap bilgilerini kaldırmanız gerekir. Aşağıdaki adımlarda, var olan bir VM 'yi serbest bırakın, serbest bırakın ve bir görüntü oluşturun. Bu görüntüyü, aboneliğinizdeki tüm kaynak grupları arasında VM 'Ler oluşturmak için kullanabilirsiniz.

Yedekleme veya hata ayıklama için mevcut Linux sanal makinenizin bir kopyasını oluşturmak veya şirket içi bir VM 'den özelleştirilmiş bir Linux VHD 'yi yüklemek için, bkz. [özel disk görüntüsünden LINUX VM 'Yi karşıya yükleme ve oluşturma](upload-vhd.md).  

Özel görüntünüzü oluşturmak için **Azure VM görüntü Oluşturucu (Genel Önizleme)** hizmetini kullanabilirsiniz, herhangi bir araç öğrenmeniz gerekmez veya yapı işlem hatlarını kurulum, yalnızca bir görüntü yapılandırması sağlamaktan ve görüntü Oluşturucu görüntüyü oluşturacaktır. Daha fazla bilgi için bkz. [Azure VM görüntü Oluşturucu Ile çalışmaya](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview)başlama.

Görüntü oluşturmadan önce aşağıdaki öğelere sahip olmanız gerekir:

* Yönetilen diskler kullanan Kaynak Yöneticisi dağıtım modelinde oluşturulan bir Azure VM 'si. Henüz bir Linux sanal makinesi oluşturmadıysanız [Portal](quick-create-portal.md), [Azure CLI](quick-create-cli.md)veya [Kaynak Yöneticisi şablonlarını](create-ssh-secured-vm-from-template.md)kullanabilirsiniz. VM 'yi gerektiği şekilde yapılandırın. Örneğin, [veri diskleri ekleyin](add-disk.md), güncelleştirmeleri uygulayın ve uygulamaları yükler. 

* En son [Azure CLI](/cli/azure/install-az-cli2) yüklü ve [az oturum açma](/cli/azure/reference-index#az-login)ile bir Azure hesabında oturum açmış olmalıdır.

## <a name="prefer-a-tutorial-instead"></a>Bunun yerine bir öğretici tercih edilsin mi?

Bu makalenin basitleştirilmiş bir sürümü ve Azure 'da VM 'Ler hakkında sınama, değerlendirme veya öğrenme için bkz. [CLI kullanarak Azure VM 'nin özel bir görüntüsünü oluşturma](tutorial-custom-images.md).  Aksi takdirde, tam resmi almak için burada okumayı sürdürün.


## <a name="step-1-deprovision-the-vm"></a>1\. Adım: sanal makinenin sağlamasını kaldırma
İlk olarak, makineye özgü dosyaları ve verileri silmek için Azure VM Aracısı 'nı kullanarak VM 'yi parçalara ayırcaksınız. Kaynak Linux sanal makinenizde `-deprovision+user` parametresiyle `waagent` komutunu kullanın. Daha fazla bilgi için bkz. [Azure Linux Aracısı kullanıcı kılavuzu](../extensions/agent-linux.md).

1. Bir SSH istemcisiyle Linux sanal makinenize bağlanın.
2. SSH penceresinde, aşağıdaki komutu girin:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Bu komutu yalnızca görüntü olarak yakaladığınız bir VM üzerinde çalıştırın. Bu komut görüntünün tüm hassas bilgilerin temizlenme veya yeniden dağıtım için uygun olduğunu garanti etmez. `+user` parametresi, sağlanan son kullanıcı hesabını da kaldırır. Kullanıcı hesabı kimlik bilgilerini VM 'de tutmak için yalnızca `-deprovision`kullanın.
 
3. Devam etmek için **y** girin. Bu onay adımını önlemek için `-force` parametresini ekleyebilirsiniz.
4. Komut tamamlandıktan sonra SSH istemcisini kapatmak için **Çıkış** ' ı girin.  VM hala bu noktada çalışmaya devam edecektir.

## <a name="step-2-create-vm-image"></a>2\. Adım: VM görüntüsü oluşturma
VM 'yi Genelleştirilmiş olarak işaretlemek ve görüntüyü yakalamak için Azure CLı 'yi kullanın. Aşağıdaki örneklerde, örnek parametre adlarını kendi değerlerinizle değiştirin. Örnek parametre adları *Myresourcegroup*, *Myvnet*ve *myvm*' i içerir.

1. [Az VM serbest bırakma](/cli/azure/vm)ile sağladığınız VM 'yi serbest bırakın. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *MYVM* adlı VM 'yi kaldırır.  
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```
    
    Üzerinde geçiş yapmadan önce VM 'nin tamamen serbest olmasını bekleyin. Bu işlem birkaç dakika sürebilir.  VM, ayırmayı kaldırma sırasında kapatılır.

2. [Az VM generalize](/cli/azure/vm)komutunu kullanarak VM 'yi Genelleştirilmiş olarak işaretleyin. Aşağıdaki örnek, *Myresourcegroup* adlı kaynak grubunda *MYVM* adlı VM 'yi Genelleştirilmiş olarak işaretler.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

    Genelleştirilmiş bir VM artık yeniden başlatılamaz.

3. [Az Image Create](/cli/azure/image#az-image-create)komutuyla VM kaynağının bir görüntüsünü oluşturun. Aşağıdaki örnek *Myresourcegroup* adlı kaynak grubunda *MYVM*adlı VM kaynağını kullanarak *MyImage* adlı bir görüntü oluşturur.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Görüntü, kaynak VM 'niz ile aynı kaynak grubunda oluşturulur. Bu görüntüden, aboneliğinizdeki herhangi bir kaynak grubunda VM 'Ler oluşturabilirsiniz. Yönetim açısından, VM kaynaklarınız ve görüntüleriniz için belirli bir kaynak grubu oluşturmak isteyebilirsiniz.
   >
   > Görüntünüzü bölge dayanıklı depolamada depolamak isterseniz, bunu [kullanılabilirlik bölgelerini](../../availability-zones/az-overview.md) destekleyen bir bölgede oluşturmanız ve `--zone-resilient true` parametresini eklemeniz gerekir.
   
Bu komut, VM görüntüsünü açıklayan JSON döndürür. Bu çıktıyı daha sonra başvurmak üzere kaydedin.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>3\. Adım: yakalanan görüntüden VM oluşturma
[Az VM Create](/cli/azure/vm)ile oluşturduğunuz görüntüyü kullanarak bir VM oluşturun. Aşağıdaki örnek, *MyImage*adlı görüntüden *dağıtılan myvmadlı* bir VM oluşturur.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Başka bir kaynak grubunda VM oluşturma 

Aboneliğinizdeki herhangi bir kaynak grubunda bulunan bir görüntüden sanal makineler oluşturabilirsiniz. Görüntüden farklı bir kaynak grubunda bir VM oluşturmak için görüntsün tam kaynak KIMLIĞINI belirtin. Görüntülerin listesini görüntülemek için [az Image List](/cli/azure/image#az-image-list) kullanın. Çıktı aşağıdaki örneğe benzerdir.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

Aşağıdaki örnek, görüntü kaynak KIMLIĞI belirterek kaynak görüntü dışında bir kaynak grubunda bir VM oluşturmak için [az VM Create](/cli/azure/vm#az-vm-create) kullanır.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>4\. Adım: dağıtımı doğrulama

Dağıtımı doğrulamak ve yeni VM 'yi kullanmaya başlamak için oluşturduğunuz sanal makineye SSH. SSH aracılığıyla bağlanmak için [az VM Show](/cli/azure/vm#az-vm-show)komutuyla sanal makinenizin IP ADRESINI veya FQDN 'sini bulun.

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Sonraki adımlar
Kaynak VM görüntüsünden birden çok VM oluşturabilirsiniz. Resimde değişiklik yapmak için: 

- Görüntinizden bir VM oluşturun.
- Tüm güncelleştirmeleri veya yapılandırma değişikliklerini yapın.
- Bir görüntü sağlamayı, serbest ayırmayı, genelleştirmenizi ve bir görüntüyü oluşturmayı geri almak için adımları izleyin.
- Gelecekteki dağıtımlar için bu yeni görüntüyü kullanın. Özgün görüntüyü silebilirsiniz.

CLı ile VM 'lerinizi yönetme hakkında daha fazla bilgi için bkz. [Azure CLI](/cli/azure).
