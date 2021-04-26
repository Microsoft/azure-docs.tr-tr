---
title: Öğretici-Azure CLı ile özel VM görüntüleri oluşturma
description: Bu öğreticide, Azure CLI kullanarak Azure’da özel sanal makine görüntüsü oluşturmayı öğrenirsiniz
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: 81bbf0b49919db68407a85b9ea2f731c5f8e1d91
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769886"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Öğretici: Azure CLI ile bir Azure VM'nin özel görüntüsünü oluşturma

Özel görüntüler market görüntüleri gibidir, ancak bunları kendiniz oluşturursunuz. Özel görüntüler, uygulamaları, uygulama yapılandırmalarını ve diğer işletim sistemi yapılandırmalarını önceden yükleme gibi yapılandırmaları önyüklemek için kullanılabilir. Bu öğreticide, bir Azure sanal makinesine ait kendi özel görüntünüzü oluşturursunuz. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Paylaşılan Görüntü Galerisi Oluşturma
> * Görüntü tanımı oluşturma
> * Görüntü sürümü oluşturma
> * Görüntüden VM oluşturma 
> * Resim galerisini paylaşma


Bu öğretici, en son sürüme sürekli olarak güncellenen [Azure Cloud Shell](../../cloud-shell/overview.md)içindeki CLI 'yi kullanır. Cloud Shell açmak için herhangi bir kod bloğunun en üstünden **deneyin** ' i seçin.

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğreticide, Azure CLı sürüm 2.4.0 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Genel Bakış

[Paylaşılan görüntü Galerisi](../shared-image-galleries.md) , kuruluşunuz genelinde özel görüntü paylaşımını basitleştirir. Özel görüntüler market görüntüleri gibidir, ancak bunları kendiniz oluşturursunuz. Özel görüntüler, uygulamaları, uygulama yapılandırmalarını ve diğer işletim sistemi yapılandırmalarını önceden yükleme gibi yapılandırmaları önyüklemek için kullanılabilir. 

Paylaşılan görüntü Galerisi, özel VM görüntülerinizi başkalarıyla paylaşmanıza olanak sağlar. Hangi görüntüleri paylaşmak istediğinizi, içinde hangi bölgelerin kullanılabilir olmasını istediğinizi ve bunları ile paylaşmak istediğinizi seçin. 

Paylaşılan görüntü Galerisi özelliği birden çok kaynak türüne sahiptir:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>Başlamadan önce

Aşağıdaki adımlarda, mevcut bir VM'yi alıp yeni VM örnekleri oluşturmak için kullanabileceğiniz yeniden kullanılabilir bir özel görüntüye dönüştürme işlemi ayrıntılı olarak açıklanmıştır.

Bu öğreticideki örneği tamamlamak için, mevcut bir sanal makinenizin olması gerekir. Gerekirse, bu öğreticide kullanmak üzere bir VM oluşturmak için [CLI hızlı](quick-create-cli.md) başlangıcını görebilirsiniz. Öğreticide çalışırken, kaynak adlarını gereken yerde değiştirin.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

## <a name="create-an-image-gallery"></a>Görüntü galerisi oluşturma 

Görüntü Galerisi, görüntü paylaşımını etkinleştirmek için kullanılan birincil kaynaktır. 

Galeri adı için izin verilen karakterler büyük veya küçük harflerden, rakamlardan, noktalardan ve noktalardan oluşur. Galeri adı tire içeremez.   Galeri adları, aboneliğiniz dahilinde benzersiz olmalıdır. 

[Az Sig Create](/cli/azure/sig#az_sig_create)kullanarak bir görüntü galerisi oluşturun. Aşağıdaki örnek, *Doğu ABD* Içinde *mygallerrg* adlı Galeri adlı bir kaynak grubu ve *MyGallery* adlı bir galeri oluşturur.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>VM hakkında bilgi edinme

[Az VM List](/cli/azure/vm#az_vm_list)kullanılarak kullanılabilir olan VM 'lerin bir listesini görebilirsiniz. 

```azurecli-interactive
az vm list --output table
```

VM adını ve içindeki kaynak grubunu öğrendikten sonra [az VM Get-instance-View](/cli/azure/vm#az_vm_get_instance_view)kullanarak VM 'nin kimliğini alın. 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

VM 'nizin KIMLIĞINI daha sonra kullanmak üzere kopyalayın.

## <a name="create-an-image-definition"></a>Görüntü tanımı oluşturma

Görüntü tanımları görüntüler için bir mantıksal gruplama oluşturur. Bunlar içinde oluşturulan görüntü sürümleri hakkındaki bilgileri yönetmek için kullanılır. 

Görüntü tanımı adları büyük veya küçük harflerden, rakamlardan, noktalardan, çizgilerden ve noktalardan oluşabilir. 

Bir görüntü tanımı için belirtebileceğiniz değerler hakkında daha fazla bilgi için bkz. [görüntü tanımları](../shared-image-galleries.md#image-definitions).

Galeride [az Sig Image-Definition Create](/cli/azure/sig/image-definition#az_sig_image_definition_create)kullanarak bir görüntü tanımı oluşturun. 

Bu örnekte, görüntü tanımı *Myımagedefinition* olarak adlandırılır ve [özelleştirilmiş](../shared-image-galleries.md#generalized-and-specialized-images) bir Linux işletim sistemi görüntüsü içindir. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

Daha sonra kullanmak için çıktıdan görüntü tanımının KIMLIĞINI kopyalayın.

## <a name="create-the-image-version"></a>Görüntü sürümü oluşturma

[Az Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az_sig_image_version_create)kullanarak VM 'den bir görüntü sürümü oluşturun.  

Görüntü sürümü için izin verilen karakterler rakamlardan ve dönemlerdir. Sayılar 32 bitlik bir tamsayı aralığında olmalıdır. Biçim: *MajorVersion*. *MinorVersion*. *Düzeltme Eki*.

Bu örnekte, görüntüimizin sürümü *1.0.0* , *Orta Batı ABD* bölgesinde 2 çoğaltma, *Orta Güney ABD* bölgesinde 1 çoğaltma ve bölgesel olarak yedekli depolama alanı kullanarak *Doğu ABD 2* bölgesindeki 1 çoğaltma oluşturacağız. Çoğaltma bölgeleri, kaynak VM 'nin bulunduğu bölgeyi içermelidir.

Bu örnekteki değerini, `--managed-image` önceki ADıMDA sanal MAKINENIZIN kimliğiyle değiştirin.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Farklı bir görüntü sürümü oluşturmak için aynı yönetilen görüntüyü kullanabilmeniz için görüntü sürümünün oluşturulması ve çoğaltılması tamamen bitmesini beklemeniz gerekir.
>
> Görüntünüzü `--storage-account-type  premium_lrs` , görüntü sürümünü oluştururken ekleyerek bir ekleme veya bölgesel olarak [yedekli depolama alanı](../../storage/common/storage-redundancy.md) tarafından Premiun depolamada da saklayabilirsiniz `--storage-account-type  standard_zrs` .
>

 
## <a name="create-the-vm"></a>Sanal makineyi oluşturma

Görüntünün özelleşmiş bir görüntü olduğunu göstermek için,--özelleşmiş parametresini kullanarak [az VM Create](/cli/azure/vm#az_vm_create) kullanarak VM oluşturun. 

`--image`Kullanılabilir görüntünün en son sürümünden VM oluşturmak için görüntü tanımı kimliği ' ni kullanın. Ayrıca, için görüntü sürüm KIMLIĞI sağlayarak VM 'yi belirli bir sürümden da oluşturabilirsiniz `--image` . 

Bu örnekte, *Myımagedefinition* görüntüsünün en son sürümünden bir VM oluşturacağız.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

## <a name="share-the-gallery"></a>Galeriyi paylaşma

Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak, abonelikler arasında görüntü paylaşabilirsiniz. Galeri, görüntü tanımı veya görüntü sürümü gibi resim paylaşabilirsiniz. Abonelikler arasında bile, görüntü sürümü üzerinde okuma izinlerine sahip olan tüm kullanıcılar, görüntü sürümünü kullanarak bir sanal makine dağıtacaktır.

Galeri düzeyindeki diğer kullanıcılarla paylaşmanızı öneririz. Galerinizin nesne KIMLIĞINI almak için [az SIG Show](/cli/azure/sig#az_sig_show)' ı kullanın.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Nesne KIMLIĞINI bir e-posta adresi ile birlikte kullanın ve bir kullanıcıya paylaşılan görüntü galerisine erişim sağlamak için [az role atama oluştur](/cli/azure/role/assignment#az_role_assignment_create) ' u kullanın. `<email-address>`Ve `<gallery iD>` bilgilerini kendi bilgileriniz ile değiştirin.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Azure RBAC kullanarak kaynakları paylaşma hakkında daha fazla bilgi için bkz. Azure [CLI kullanarak Azure rol atamaları ekleme veya kaldırma](../../role-based-access-control/role-assignments-cli.md).

## <a name="azure-image-builder"></a>Azure Görüntü Oluşturucusu

Azure Ayrıca, Packer, [Azure VM Image Builder](../image-builder-overview.md)üzerine inşa olan bir hizmet sunar. Özelleştirmeleri bir şablonda açıklamanız yeterlidir ve görüntü oluşturma işleme alınacaktır. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, özel bir VM görüntüsü oluşturdunuz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Paylaşılan Görüntü Galerisi Oluşturma
> * Görüntü tanımı oluşturma
> * Görüntü sürümü oluşturma
> * Görüntüden VM oluşturma 
> * Resim galerisini paylaşma

Yüksek oranda kullanılabilir sanal makineler hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Yüksek oranda kullanılabilir VM’ler oluşturma](tutorial-availability-sets.md)