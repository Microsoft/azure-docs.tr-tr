---
title: Azure PowerShell ölçek kümesi oluşturmak için paylaşılan VM görüntülerini kullanma
description: Azure 'da sanal makine ölçek kümelerini dağıtmak için kullanılacak paylaşılan VM görüntülerini oluşturmak üzere Azure PowerShell nasıl kullanacağınızı öğrenin.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: c6e8eaafa8d6e8b1f68873130b5ff56484fda049
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444029"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Azure PowerShell ile sanal makine ölçek kümeleri için paylaşılan görüntüler oluşturun ve kullanın

Ölçek kümesi oluşturduğunuzda, sanal makine örnekleri dağıtılırken kullanılacak bir görüntü belirtirsiniz. Paylaşılan görüntü Galerisi hizmeti, kuruluşunuz genelinde özel görüntü paylaşımını büyük ölçüde basitleştirir. Özel görüntüler market görüntüleri gibidir, ancak bunları kendiniz oluşturursunuz. Özel görüntüler, uygulamaları, uygulama yapılandırmalarını ve diğer işletim sistemi yapılandırmalarını önceden yükleme gibi yapılandırmaları önyüklemek için kullanılabilir. 

Paylaşılan görüntü Galerisi, özel VM görüntülerinizi kuruluşunuzdaki diğer kişilerle, bir AAD kiracısı içinde veya bölgeler arasında paylaşmanızı sağlar. Hangi görüntüleri paylaşmak istediğinizi, içinde hangi bölgelerin kullanılabilir olmasını istediğinizi ve bunları ile paylaşmak istediğinizi seçin. Paylaşılan görüntüleri mantıksal olarak gruplandırabilmeniz için birden çok Galeri oluşturabilirsiniz. 

Galeri, tam Azure rol tabanlı erişim denetimi (Azure RBAC) sağlayan en üst düzey bir kaynaktır. Görüntülerin sürümü oluşturulabilir ve her görüntü sürümünü farklı bir Azure bölgesi kümesine çoğaltmayı tercih edebilirsiniz. Galeri yalnızca yönetilen görüntülerle birlikte kullanılabilir. 

Paylaşılan görüntü Galerisi özelliğinin birden çok kaynak türü vardır. 


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="before-you-begin"></a>Başlamadan önce

Aşağıdaki adımlarda, mevcut bir VM'yi alıp yeni VM örnekleri oluşturmak için kullanabileceğiniz yeniden kullanılabilir bir özel görüntüye dönüştürme işlemi ayrıntılı olarak açıklanmıştır.

Bu makaledeki örneği tamamlayabilmeniz için, mevcut bir yönetilen görüntünüz olmalıdır. [Öğreticiyi izleyebilirsiniz: gerekirse, sanal makine ölçek kümeleri için Azure PowerShell ile özel bir görüntü oluşturun ve kullanın](tutorial-use-custom-image-powershell.md) . Yönetilen görüntü bir veri diski içeriyorsa, veri diski boyutu 1 TB 'den fazla olamaz.

Makale üzerinden çalışırken, kaynak grubu ve VM adlarını gerektiği yerde değiştirin.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]




## <a name="next-steps"></a>Sonraki adımlar

Ayrıca, şablonlar kullanarak paylaşılan görüntü Galerisi kaynağı da oluşturabilirsiniz. Çeşitli Azure hızlı başlangıç şablonları mevcuttur: 

- [Paylaşılan Görüntü Galerisi Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Tanımı Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Sürümü Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Görüntü Sürümünden Sanal Makine Oluşturma](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Paylaşılan görüntü galerileri hakkında daha fazla bilgi için bkz. [genel bakış](../virtual-machines/shared-image-galleries.md). Sorunlarla karşılaşırsanız bkz. [paylaşılan görüntü galerilerine sorun giderme](../virtual-machines/troubleshooting-shared-images.md).
