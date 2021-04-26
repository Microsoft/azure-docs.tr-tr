---
title: Azure PowerShell paylaşılan bir görüntü galerisi oluşturma
description: Azure 'da paylaşılan bir görüntü galerisi oluşturmak için Azure PowerShell kullanmayı öğrenin
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 877439ddba6ffaf2fd060c29f28c25c18694605d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555576"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Azure PowerShell paylaşılan bir görüntü galerisi oluşturma 

[Paylaşılan görüntü Galerisi](./shared-image-galleries.md) , kuruluşunuz genelinde özel görüntü paylaşımını basitleştirir. Özel görüntüler market görüntüleri gibidir, ancak bunları kendiniz oluşturursunuz. Özel görüntüler, uygulamalar, uygulama yapılandırması ve diğer işletim sistemi yapılandırmalarının dağıtım görevlerinin önyüklemesi için kullanılabilir. 

Paylaşılan görüntü Galerisi, özel VM görüntülerinizi kuruluşunuzdaki diğer kişilerle, bir AAD kiracısı içinde veya bölgeler arasında paylaşmanızı sağlar. Hangi görüntüleri paylaşmak istediğinizi, içinde hangi bölgelerin kullanılabilir olmasını istediğinizi ve bunları ile paylaşmak istediğinizi seçin. Paylaşılan görüntüleri mantıksal olarak gruplandırabilmeniz için birden çok Galeri oluşturabilirsiniz. 

Galeri, tam Azure rol tabanlı erişim denetimi (Azure RBAC) sağlayan en üst düzey bir kaynaktır. Görüntülerin sürümü oluşturulabilir ve her görüntü sürümünü farklı bir Azure bölgesi kümesine çoğaltmayı tercih edebilirsiniz. Galeri yalnızca yönetilen görüntülerle birlikte kullanılabilir.

Paylaşılan görüntü Galerisi özelliğinin birden çok kaynak türü vardır. 

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


[!INCLUDE [virtual-machines-common-shared-images-powershell](../../includes/virtual-machines-common-shared-images-powershell.md)]


## <a name="next-steps"></a>Sonraki adımlar

Bir [VM](image-version-vm-powershell.md)'den, [yönetilen görüntüden](image-version-managed-image-powershell.md)veya [başka bir galerideki görüntüden](image-version-another-gallery-powershell.md)görüntü oluşturun.

[Azure görüntü Oluşturucu (Önizleme)](./image-builder-overview.md) , görüntü sürümü oluşturmayı otomatikleştirmenize yardımcı olabilir, hatta [mevcut bir görüntü sürümünden yeni bir görüntü sürümünü](./windows/image-builder-gallery-update-image-version.md)güncelleştirmek ve oluşturmak için de kullanabilirsiniz. 

Ayrıca, şablonlar kullanarak paylaşılan görüntü Galerisi kaynağı da oluşturabilirsiniz. Çeşitli Azure hızlı başlangıç şablonları mevcuttur: 

- [Paylaşılan Görüntü Galerisi Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Tanımı Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Sürümü Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Görüntü Sürümünden Sanal Makine Oluşturma](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)