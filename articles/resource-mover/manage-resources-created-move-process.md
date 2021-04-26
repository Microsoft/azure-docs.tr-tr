---
title: Azure Kaynak taşıyıcısı 'nde VM taşıma işlemi sırasında oluşturulan kaynakları yönetme
description: Azure Kaynak taşıyıcısı 'nde VM taşıma işlemi sırasında oluşturulan kaynakları yönetmeyi öğrenin
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: d3c4c4e86e2461ea1d05af284e724a5a2991f040
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727048"
---
# <a name="manage-resources-created-for-the-vm-move"></a>VM taşıma için oluşturulan kaynakları yönetme

Bu makalede, VM taşıma işlemini kolaylaştırmak için [Azure Kaynak taşıyıcısı](overview.md) tarafından açıkça oluşturulan kaynakların nasıl yönetileceği açıklanır. 

VM 'Leri bölgeler arasında taşıdıktan sonra, kaynak taşıyıcısı tarafından el ile temizlenmesi gereken birkaç kaynak vardır.

## <a name="delete-resources-created-for-vm-move"></a>VM taşıma için oluşturulan kaynakları silme

Taşıma koleksiyonunu el ile silin ve VM taşıma için oluşturulan kaynakları Site Recovery.

1. Kaynak grubundaki kaynakları gözden geçirin ```ResourceMoverRG-<sourceregion>-<target-region>-<metadataRegionShortName>``` .
2. Taşıma koleksiyonundaki VM 'nin ve diğer tüm kaynak kaynaklarının taşındığını/silindiğini kontrol edin. Bu, bunları kullanan beklemedeki kaynaklar olmamasını sağlar.
2. Bu kaynakları silin.

    - Taşıma koleksiyonu adı ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` .
    - Önbellek depolama hesabı adı ```resmovecache<guid>```
    - Kasa adı ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Sonraki adımlar

Kaynak taşıyıcısı ile [BIR VM](tutorial-move-region-virtual-machines.md) 'yi başka bir bölgeye taşımayı deneyin.
