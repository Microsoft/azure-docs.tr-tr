---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: ff8bfbeea8bd22619375e88081da0cf9c0770fc9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511426"
---
<!-- ### Create a storage account -->

Media Services hesabı oluştururken, bir Azure Depolama hesabı kaynağının adını sağlamanız gerekir. Belirtilen depolama hesabı, Media Services hesabınıza eklenir. Depolama hesaplarının Media Services’ta nasıl kullanıldığı hakkında daha fazla bilgi için bkz. [Depolama hesapları](../storage-account-concept.md).

Tek bir **Birincil** depolama hesabınız olması gerekir, ancak Media Services hesabınızla ilişkili istediğiniz sayıda **İkincil** depolama hesabınız olabilir. Media Services, **General-purpose v2** (GPv2) veya **General-purpose v1** (GPv1) hesaplarını destekler. Yalnızca blob hesaplarının **Birincil** olmasına izin verilmez. Depolama hesapları hakkında daha fazla bilgi edinmek istiyorsanız bkz. [Azure Depolama hesap seçenekleri](../../../storage/common/storage-account-overview.md). 

Bu örnekte, bir Genel Amaçlı v2, standart LRS hesabı oluşturacağız. Depolama hesaplarıyla denemek istiyorsanız kullanın `--sku Standard_LRS` . Ancak, üretim için bir SKU seçerken `--sku Standard_RAGRS` iş sürekliliği için coğrafi çoğaltma sağlayan göz önünde bulundurmanız gerekir. Daha fazla bilgi için bkz. [depolama hesapları](/cli/azure/storage/account).

Aşağıdaki komut, Media Services Hesabı ile ilişkilendirilecek Depolama hesabını oluşturur. Aşağıdaki betikte, `storageaccountforams` kendi benzersiz adresler adınızı 24 karakterden daha uzun bir süre ile değiştirin. `amsResourceGroup` önceki adımda kaynak grubu için verdiğiniz değerle aynı olmalıdır.

```azurecli
az storage account create --name storageaccountforams --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```
