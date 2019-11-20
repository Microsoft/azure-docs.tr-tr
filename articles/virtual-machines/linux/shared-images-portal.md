---
title: Portalı kullanarak Linux için paylaşılan Azure sanal makine görüntülerini oluşturma
description: Azure portal kullanarak sanal makine görüntülerini oluşturma ve paylaşma hakkında bilgi edinin.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 89c98379a8e79d1b00db47021ae737ae471f79b3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035006"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Azure portal kullanarak paylaşılan bir görüntü galerisi oluşturun

[Paylaşılan görüntü Galerisi](shared-image-galleries.md) , kuruluşunuz genelinde özel görüntü paylaşımını basitleştirir. Özel görüntüler market görüntüleri gibidir, ancak bunları kendiniz oluşturursunuz. Özel görüntüler, uygulamalar, uygulama yapılandırması ve diğer işletim sistemi yapılandırmalarının dağıtım görevlerinin önyüklemesi için kullanılabilir. 

Paylaşılan görüntü Galerisi, özel VM görüntülerinizi kuruluşunuzdaki diğer kişilerle, bir AAD kiracısı içinde veya bölgeler arasında paylaşmanızı sağlar. Hangi görüntüleri paylaşmak istediğinizi, içinde hangi bölgelerin kullanılabilir olmasını istediğinizi ve bunları ile paylaşmak istediğinizi seçin. Paylaşılan görüntüleri mantıksal olarak gruplandırabilmeniz için birden çok Galeri oluşturabilirsiniz. 

Galeri, tam rol tabanlı erişim denetimi (RBAC) sağlayan en üst düzey bir kaynaktır. Görüntülerin sürümü oluşturulabilir ve her görüntü sürümünü farklı bir Azure bölgesi kümesine çoğaltmayı tercih edebilirsiniz. Galeri yalnızca yönetilen görüntülerle birlikte kullanılabilir.

Paylaşılan görüntü Galerisi özelliğinin birden çok kaynak türü vardır. Bunları bu makalede kullanacağız veya oluşturacağız:

| Kaynak | Açıklama|
|----------|------------|
| **Yönetilen görüntü** | Tek başına kullanılabilen veya bir görüntü galerisinde **görüntü sürümü** oluşturmak için kullanılan temel bir görüntü. Yönetilen görüntüler [Genelleştirilmiş](shared-image-galleries.md#generalized-and-specialized-images) VM 'lerden oluşturulur. Yönetilen görüntü, birden çok VM oluşturmak için kullanılabilen ve artık paylaşılan görüntü sürümleri oluşturmak için kullanılabilen özel bir VHD türüdür. |
| **Görüntüye** | Bir VHD 'nin **görüntü sürümü**oluşturmak için kullanılabilecek bir kopyası. Anlık görüntüler [özelleştirilmiş](shared-image-galleries.md#generalized-and-specialized-images) bir VM 'den alınabilir (Genelleştirilmiş olmayan bir şekilde) ve özel bir görüntü sürümü oluşturmak için tek başına veya veri disklerinin anlık görüntüleriyle birlikte kullanılır.
| **Görüntü Galerisi** | Azure Marketi gibi bir **görüntü Galerisi** , görüntüleri yönetmek ve paylaşmak için bir depodur, ancak kimlerin erişimi olduğunu kontrol edersiniz. |
| **Görüntü tanımı** | Görüntüler, bir galeri içinde tanımlanır ve bu görüntüyü kuruluşunuzda kullanmaya yönelik gereksinimler hakkında bilgi taşır. Görüntünün Genelleştirilmiş veya özel, işletim sistemi, minimum ve maksimum bellek gereksinimleri ve sürüm notları gibi bilgileri ekleyebilirsiniz. Bu, bir görüntü türünün tanımıdır. |
| **Görüntü sürümü** | Bir **görüntü sürümü** , galerı kullanılırken VM oluşturmak için kullandığınız şeydir. Ortamınız için gerektiğinde bir görüntünün birden fazla sürümüne sahip olabilirsiniz. Yönetilen bir görüntü gibi, bir sanal makine oluşturmak için bir **görüntü sürümü** kullandığınızda, sanal makine için yeni diskler oluşturmak üzere görüntü sürümü kullanılır. Görüntü sürümleri birden çok kez kullanılabilir. |

<br>

> [!IMPORTANT]
> Özel görüntüler şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Bilinen önizleme sınırlamaları** VM 'Ler yalnızca portal veya API kullanılarak özelleştirilmiş görüntülerden oluşturulabilir. , Önizleme için CLı veya PowerShell desteği yoktur.


## <a name="before-you-begin"></a>Başlamadan önce

Bu makaledeki örneği tamamlayabilmeniz için, genelleştirilmiş bir VM 'nin ya da özelleştirilmiş bir sanal makinenin anlık görüntüsünün mevcut bir yönetilen görüntüsüne sahip olmanız gerekir. Öğreticiyi izleyebilirsiniz: yönetilen bir görüntü oluşturmak için [Azure PowerShell Ile Azure VM 'nin özel bir görüntüsünü oluşturma](tutorial-custom-images.md) veya özel bir VM için [anlık görüntü](../windows/snapshot-copy-managed-disk.md) oluşturma. Hem yönetilen hem de anlık görüntülerde, veri diski boyutu 1 TB 'den fazla olamaz.

Bu makalede çalışırken, kaynak grubu ve VM adlarını gerektiği yerde değiştirin.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>VM oluşturma 

Artık bir veya daha fazla yeni VM oluşturabilirsiniz. Bu örnek, *Doğu ABD* veri merkezinde *Myresourcegroup* Içinde *myvmfromımage*adlı bir VM oluşturur.

1. Görüntü tanımınıza gidin. Kullanılabilir tüm görüntü tanımlarını göstermek için kaynak filtresini kullanabilirsiniz.
1. Görüntü tanımınızın sayfasında, sayfanın üst kısmındaki menüden **VM oluştur** ' u seçin.
1. **Kaynak grubu**Için **Yeni oluştur** ' u seçin ve ad için *myresourcegroup* yazın.
1. **Sanal makine adı**alanına *myvm*yazın.
1. **Bölge**için *Doğu ABD*' yi seçin.
1. **Kullanılabilirlik seçenekleri**için, varsayılan *altyapı yedekliliği gerekmez*' ı gerekli olarak bırakın.
1. Görüntü tanımı için sayfadan başladıysanız **görüntünün değeri** `latest` görüntü sürümü ile otomatik olarak doldurulur.
1. **Boyut**için kullanılabilir boyutlar LISTESINDEN bir VM boyutu seçin ve ardından **Seç**' i seçin.
1. **Yönetici hesabı**altında, kaynak VM genelleştiriliydi, **Kullanıcı adı** ve **SSH ortak anahtarınızı**girin. Kaynak VM özelleştirilise, kaynak VM 'deki bilgiler kullanıldığından bu seçenekler gri kalır.
1. VM 'ye uzaktan erişime izin vermek istiyorsanız, **ortak gelen bağlantı noktaları**altında **Seçili bağlantı noktalarına izin ver** ' i seçin ve ardından açılır listeden **SSH (22)** öğesini seçin. VM 'ye uzaktan erişime izin vermek istemiyorsanız, **Genel gelen bağlantı noktaları**için **hiçbirini** seçilmemiş bırakın.
1. İşiniz bittiğinde sayfanın altındaki **gözden geçir + oluştur** düğmesini seçin.
1. VM doğrulamayı geçtikten sonra, dağıtımı başlatmak için sayfanın alt kısmındaki **Oluştur** ' u seçin.


## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, sanal makineyi ve tüm ilişkili kaynakları silebilirsiniz. Bunu yapmak için sanal makinenin kaynak grubunu ve **Sil**’i seçip silinecek kaynak grubunun adını onaylayın.

Tek tek kaynakları silmek istiyorsanız, bunları ters sırada silmeniz gerekir. Örneğin, bir görüntü tanımını silmek için, bu görüntüden oluşturulan tüm görüntü sürümlerini silmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Ayrıca, şablonlar kullanarak paylaşılan görüntü Galerisi kaynağı da oluşturabilirsiniz. Çeşitli Azure hızlı başlangıç şablonları mevcuttur: 

- [Paylaşılan görüntü galerisi oluşturma](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Paylaşılan görüntü galerisinde görüntü tanımı oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Paylaşılan görüntü galerisinde görüntü sürümü oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Görüntü sürümünden VM oluşturma](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Paylaşılan görüntü galerileri hakkında daha fazla bilgi için bkz. [genel bakış](shared-image-galleries.md). Sorunlarla karşılaşırsanız bkz. [paylaşılan görüntü galerilerine sorun giderme](troubleshooting-shared-images.md).

