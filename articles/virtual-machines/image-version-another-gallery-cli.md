---
title: CLı kullanarak başka bir galerinin görüntü sürümünü kopyalama
description: Azure CLı ile başka bir galerinin görüntü sürümünü kopyalama.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e2cd885d886a0f13783e61a04c7243efdf12967e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784992"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Azure CLı kullanarak başka bir galerinin görüntüsünü kopyalama

Kuruluşunuzda birden çok Galeri varsa, diğer galerilere depolanmış varolan görüntü sürümlerinden görüntü sürümleri de oluşturabilirsiniz. Örneğin, yeni görüntüleri oluşturmak ve test etmek için bir geliştirme ve test galeriniz olabilir. Üretimde kullanılmak üzere hazırsanız, bu örneği kullanarak bunları bir üretim galerisine kopyalayabilirsiniz. Ayrıca, [Azure PowerShell](image-version-another-gallery-powershell.md)kullanarak başka bir galerideki görüntüden görüntü oluşturabilirsiniz.



## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için, var olan bir kaynak galerisine, görüntü tanımına ve görüntü sürümüne sahip olmanız gerekir. Ayrıca bir hedef galeriniz olmalıdır. 

Kaynak görüntü sürümü hedef galerinizin bulunduğu bölgeye çoğaltılmalıdır. 

Bu makalede çalışırken, kaynak adlarını gereken yerde değiştirin.



## <a name="get-information-from-the-source-gallery"></a>Kaynak galerisinden bilgi al

Yeni Galerinizdeki bir kopyasını oluşturabilmeniz için kaynak görüntü tanımından bilgilere ihtiyacınız olacak.

Kaynak galerisiyle ilgili bilgileri bulmak için [az SIG List](/cli/azure/sig#az_sig_list) kullanarak kullanılabilir görüntü galerileriyle ilgili bilgileri listeleyin.

```azurecli-interactive 
az sig list -o table
```

Görüntü tanımlarını [az Sig Image-Definition List](/cli/azure/sig/image-definition#az_sig_image_definition_list)kullanarak bir galeride listeleyin. Bu örnekte, *Mygallerrg* kaynak grubunda *MyGallery* adlı galerideki görüntü tanımlarını arıyoruz.

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

Yeni galerinize kopyalamak istediğiniz görüntü sürümünü bulmak için [az Sig Image-Version List](/cli/azure/sig/image-version#az_sig_image_version_list) kullanarak galerideki bir görüntünün sürümlerini listeleyin. Bu örnekte, *Myımagedefinition* görüntü tanımının parçası olan tüm görüntü sürümlerini arıyoruz.

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

İhtiyacınız olan tüm bilgilere sahip olduktan sonra, kaynak görüntü sürümünün KIMLIĞINI [az Sig Image-Version Show](/cli/azure/sig/image-version#az_sig_image_version_show)kullanarak alabilirsiniz.

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>Görüntü tanımını oluşturma 

Kaynak görüntü sürümünüzün görüntü tanımıyla eşleşen bir görüntü tanımı oluşturmanız gerekir. Daha [az Sig Image-Definition Show](/cli/azure/sig/image-definition#az_sig_image_definition_show)kullanarak görüntü tanımını yeni galerinize yeniden oluşturmak için gereken tüm bilgileri görebilirsiniz.

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```

Çıkış şuna benzer olacaktır:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Linux",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Yukarıdaki çıktıdaki bilgileri kullanarak yeni Galerinizdeki yeni bir görüntü tanımı oluşturun.


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```


## <a name="create-the-image-version"></a>Görüntü sürümü oluşturma

[Az Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az_sig_image_version_create)kullanarak sürümler oluşturun. Görüntü sürümü oluşturmak için taban çizgisi olarak kullanılacak yönetilen görüntünün KIMLIĞINI geçirmeniz gerekir. Kaynak grubundaki görüntüler hakkında bilgi almak için [az Image List](/cli/azure/image?view#az_image_list) kullanabilirsiniz. 

Görüntü sürümü için izin verilen karakterler rakamlardan ve dönemlerdir. Sayılar 32 bitlik bir tamsayı aralığında olmalıdır. Biçim: *MajorVersion*. *MinorVersion*. *Düzeltme Eki*.

Bu örnekte, görüntüimizin sürümü *1.0.0* ve bölgesel olarak yedekli depolama alanı kullanarak *Orta Güney ABD* bölgesinde 1 çoğaltma ve *Doğu ABD* bölgesinde 1 çoğaltma oluşturacağız.


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> Farklı bir görüntü sürümü oluşturmak için aynı yönetilen görüntüyü kullanabilmeniz için görüntü sürümünün oluşturulması ve çoğaltılması tamamen bitmesini beklemeniz gerekir.
>
> Görüntünüzü `--storage-account-type  premium_lrs` , görüntü sürümünü oluştururken ekleyerek ekleyebilir veya coğrafi olarak [yedekli depolama](../storage/common/storage-redundancy.md) alanı ekleyerek, Premium depolamada da saklayabilirsiniz `--storage-account-type  standard_zrs` .
>

## <a name="next-steps"></a>Sonraki adımlar

[Genelleştirilmiş](vm-generalized-image-version-cli.md) veya [özel](vm-specialized-image-version-cli.md) BIR görüntü sürümünden bir VM oluşturun.

Ayrıca, [Azure görüntü Oluşturucu (Önizleme)](./image-builder-overview.md) , görüntü sürümü oluşturma işlemini otomatik hale getirmeye yardımcı olabilir. bu sürümü, [var olan bir görüntü sürümünden yeni bir görüntü sürümünü](./linux/image-builder-gallery-update-image-version.md)güncelleştirmek ve oluşturmak için de kullanabilirsiniz. 

Satın alma planı bilgilerini sağlama hakkında daha fazla bilgi için bkz. [görüntü oluştururken Azure Marketi satın alma planı bilgilerini sağlama](marketplace-images.md).