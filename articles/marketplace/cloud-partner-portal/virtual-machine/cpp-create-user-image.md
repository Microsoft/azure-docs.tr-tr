---
title: Azure Marketi için bir Kullanıcı VM görüntüsü oluşturma
description: Bir Kullanıcı VM görüntüsü oluşturmak için gereken adımları ve başvuruları listeler.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: e63f09dc538c5e66b244826cf3b5f92ac388b6a9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818690"
---
# <a name="create-a-user-vm-image"></a>Kullanıcı VM görüntüsü oluşturma

Bu makalede, genelleştirilmiş bir VHD 'den yönetilmeyen bir görüntü oluşturmak için gereken iki genel adım açıklanmaktadır.  Her adımda size rehberlik etmek için başvurular sağlanır: görüntüyü yakalama ve görüntüyü Genelleştirme.


## <a name="capture-the-vm-image"></a>VM görüntüsünü yakala

Erişim yaklaşımınıza karşılık gelen VM 'yi yakalamak için aşağıdaki makaledeki yönergeleri kullanın:

-  PowerShell: [bir Azure VM 'den YÖNETILMEYEN VM görüntüsü oluşturma](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLı: [bir sanal makinenin veya VHD 'nin görüntüsünü oluşturma](../../../virtual-machines/linux/capture-image.md)
-  API: [sanal makineler-yakala](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>VM görüntüsünü genelleştirin

Kullanıcı görüntüsünü önceden genelleştirilmiş bir VHD 'den oluşturmuş olduğunuzdan, genelleştirilmiş de genelleştirilmelidir.  Yine, erişim mekanizmanıza karşılık gelen aşağıdaki makaleyi seçin.  (Diski yakalandığında zaten genelleştirilmiş bir durum olabilir.)

-  PowerShell: [VM 'Yi Genelleştirme](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLı: [2. Adım: VM görüntüsü oluşturma](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [sanal makineler-Genelleştir](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Sonraki adımlar

Daha sonra [bir sertifika oluşturup](cpp-create-key-vault-cert.md) yeni bir Azure Key Vault depolayacaksınız.  Bu sertifika, sanal makineye güvenli bir WinRM bağlantısı kurmak için gereklidir.
