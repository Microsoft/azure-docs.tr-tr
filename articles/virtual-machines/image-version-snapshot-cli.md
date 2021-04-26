---
title: CLı-paylaşılan görüntü galerisinde bir anlık görüntü veya yönetilen diskten görüntü oluşturma
description: Azure CLı kullanarak paylaşılan görüntü galerisinde bir anlık görüntü veya yönetilen diskten görüntü oluşturmayı öğrenin.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 2dc6d99b8b1c913479fc584b52f6ff919dfac675
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792300"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Azure CLı kullanarak paylaşılan bir görüntü galerisinde yönetilen diskten veya anlık görüntüden görüntü oluşturma

Paylaşılan görüntü galerisine geçirmek istediğiniz mevcut bir anlık görüntü veya yönetilen diskiniz varsa, doğrudan yönetilen diskten veya anlık görüntüden paylaşılan bir görüntü Galerisi görüntüsü oluşturabilirsiniz. Yeni görüntünüzü sınadıktan sonra, kaynak yönetilen diski veya anlık görüntüyü silebilirsiniz. Ayrıca, [Azure PowerShell](image-version-snapshot-powershell.md)kullanarak paylaşılan bir görüntü galerisinde yönetilen bir diskten veya anlık görüntüden görüntü oluşturabilirsiniz.

Görüntü galerisindeki görüntülerin iki bileşeni vardır ve bu örnekte oluşturacağız:
- **Görüntü tanımı** , görüntü ve kullanma gereksinimleri hakkında bilgi taşır. Bu, görüntünün Windows veya Linux, özelleştirilmiş veya Genelleştirilmiş, sürüm notları ve en düşük ve en yüksek bellek gereksinimlerini içerir. Bu, bir görüntü türünün tanımıdır. 
- **Görüntü sürümü** , paylaşılan görüntü GALERISI kullanılırken VM oluşturmak için kullanılır. Ortamınız için gerektiğinde bir görüntünün birden fazla sürümüne sahip olabilirsiniz. Bir VM oluşturduğunuzda, sanal makine için yeni diskler oluşturmak üzere görüntü sürümü kullanılır. Görüntü sürümleri birden çok kez kullanılabilir.


## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için bir anlık görüntü veya yönetilen diskiniz olmalıdır. 

Veri diski eklemek istiyorsanız, veri diski boyutu 1 TB 'tan fazla olamaz.

Bu makalede çalışırken, kaynak adlarını gereken yerde değiştirin.

## <a name="find-the-snapshot-or-managed-disk"></a>Anlık görüntü veya yönetilen disk bulma 

[Az Snapshot List](/cli/azure/snapshot#az_snapshot_list)kullanılarak bir kaynak grubunda kullanılabilir olan anlık görüntülerin listesini görebilirsiniz. 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

Anlık görüntü yerine yönetilen disk de kullanabilirsiniz. Yönetilen bir disk almak için [az disk List](/cli/azure/disk#az_disk_list)kullanın. 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

Anlık görüntü veya yönetilen disk KIMLIĞI ve `$source` daha sonra kullanılmak üzere adlı bir değişkene atadıktan sonra.

Yansımanıza dahil etmek istediğiniz tüm veri disklerini almak için aynı işlemi kullanabilirsiniz. Bunları değişkenlere atayın ve sonra görüntü sürümünü oluştururken bu değişkenleri kullanın.


## <a name="find-the-gallery"></a>Galeriyi bulun

Görüntü tanımını oluşturmak için görüntü galerisi hakkında bilgilere ihtiyacınız olacaktır.

[Az SIG List](/cli/azure/sig#az_sig_list)kullanarak kullanılabilir görüntü galerileriyle ilgili bilgileri listeleyin. Galerinin daha sonra kullanmak için hangi kaynak grubunun içinde olduğunu göz önünde bir şekilde adlandırın.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>Görüntü tanımı oluşturma

Görüntü tanımları görüntüler için bir mantıksal gruplama oluşturur. Bunlar görüntüyle ilgili bilgileri yönetmek için kullanılır. Görüntü tanımı adları büyük veya küçük harflerden, rakamlardan, noktalardan, çizgilerden ve noktalardan oluşabilir. 

Görüntü tanımınızı yaparken, doğru bilgilerin tümünün bulunduğundan emin olun. Bu örnekte, anlık görüntü veya yönetilen diskin kullanımda olan ve genelleştirilmiş bir VM 'den olduğunu varsayıyoruz. Yönetilen disk veya anlık görüntü genelleştirilmiş bir işletim sistemi (Windows için [Sysprep veya](https://github.com/Azure/WALinuxAgent) `-deprovision` `-deprovision+user` Linux için veya Linux için) aldıysanız, öğesini `-OsState` olarak değiştirin `generalized` . 

Bir görüntü tanımı için belirtebileceğiniz değerler hakkında daha fazla bilgi için bkz. [görüntü tanımları](./shared-image-galleries.md#image-definitions).

Galeride [az Sig Image-Definition Create](/cli/azure/sig/image-definition#az_sig_image_definition_create)kullanarak bir görüntü tanımı oluşturun.

Bu örnekte, görüntü tanımı *Myımagedefinition* olarak adlandırılır ve [özelleştirilmiş](./shared-image-galleries.md#generalized-and-specialized-images) bir Linux işletim sistemi görüntüsü içindir. Windows işletim sistemi kullanan görüntülerin tanımını oluşturmak için kullanın `--os-type Windows` . 

Bu örnekte Galeri, *Mygallerrg* kaynak grubunda yer aldığı ve görüntü tanımı adı *mımagedefinition* *olarak adlandırılmaktadır*.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>Görüntü sürümü oluşturma

[Az Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az_sig_image_version_create)kullanarak bir görüntü sürümü oluşturun. 

Görüntü sürümü için izin verilen karakterler rakamlardan ve dönemlerdir. Sayılar 32 bitlik bir tamsayı aralığında olmalıdır. Biçim: *MajorVersion*. *MinorVersion*. *Düzeltme Eki*.

Bu örnekte, görüntüimizin sürümü *1.0.0* ve bölgesel olarak yedekli depolama alanı kullanarak *Orta Güney ABD* bölgesinde 1 çoğaltma ve *Doğu ABD 2* bölgesinde 1 çoğaltma oluşturacağız. Çoğaltma için hedef bölgeler seçerken, yönetilen diskin veya anlık görüntünün *kaynak* bölgesini çoğaltma için hedef olarak da eklemeniz gerektiğini unutmayın.

Anlık görüntü veya yönetilen diskin KIMLIĞINI `--os-snapshot` parametreye geçirin.


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

Görüntüye veri diskleri eklemek istiyorsanız, her iki `--data-snapshot-luns` parametreyi de LUN numarasına ve `--data-snapshots` veri diski ya da anlık görüntü kimliğine ayarlanmış olarak eklemeniz gerekir.

> [!NOTE]
> Farklı bir görüntü sürümü oluşturmak için aynı yönetilen görüntüyü kullanabilmeniz için görüntü sürümünün oluşturulması ve çoğaltılması tamamen bitmesini beklemeniz gerekir.
>
> Görüntü sürümünü oluştururken ekleyerek görüntü sürümü çoğaltmalarınızı, bölgesel olarak [yedekli depolama](../storage/common/storage-redundancy.md) alanında da saklayabilirsiniz `--storage-account-type standard_zrs` .
>

## <a name="next-steps"></a>Sonraki adımlar

[Özel görüntü sürümünden](vm-specialized-image-version-cli.md)bir VM oluşturun.

Satın alma planı bilgilerini sağlama hakkında daha fazla bilgi için bkz. [görüntü oluştururken Azure Marketi satın alma planı bilgilerini sağlama](marketplace-images.md).