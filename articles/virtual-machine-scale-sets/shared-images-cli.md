---
title: Azure CLı 'da ölçek kümesi oluşturmak için paylaşılan VM görüntülerini kullanma
description: Azure 'da sanal makine ölçek kümeleri dağıtmak için kullanılacak paylaşılan VM görüntülerini oluşturmak üzere Azure CLı 'yı nasıl kullanacağınızı öğrenin.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: b5e9d5995e8173950db483c5c26a11830a62862e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444012"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Azure CLı 2,0 ile sanal makine ölçek kümeleri için paylaşılan görüntüler oluşturma ve kullanma

Ölçek kümesi oluşturduğunuzda, sanal makine örnekleri dağıtılırken kullanılacak bir görüntü belirtirsiniz. [Paylaşılan görüntü Galerisi](../virtual-machines/shared-image-galleries.md) , kuruluşunuz genelinde özel görüntü paylaşımını basitleştirir. Özel görüntüler market görüntüleri gibidir, ancak bunları kendiniz oluşturursunuz. Özel görüntüler, uygulamaları, uygulama yapılandırmalarını ve diğer işletim sistemi yapılandırmalarını önceden yükleme gibi yapılandırmaları önyüklemek için kullanılabilir. 

Paylaşılan görüntü Galerisi, görüntülerinizi başkalarıyla paylaşmanıza olanak sağlar. Hangi görüntüleri paylaşmak istediğinizi, içinde hangi bölgelerin kullanılabilir olmasını istediğinizi ve bunları ile paylaşmak istediğinizi seçin. 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Sonraki adımlar

Bir [VM](../virtual-machines/image-version-vm-cli.md)'den veya [yönetilen bir görüntüden](../virtual-machines/image-version-managed-image-cli.md)görüntü sürümü oluşturun.

Paylaşılan görüntü galerileri hakkında daha fazla bilgi için bkz. [genel bakış](../virtual-machines/shared-image-galleries.md). Sorunlarla karşılaşırsanız bkz. [paylaşılan görüntü galerilerine sorun giderme](../virtual-machines/troubleshooting-shared-images.md).
