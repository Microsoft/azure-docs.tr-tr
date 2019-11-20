---
title: Linux VM 'Ler için yönetilen diske genel bakış Azure Disk Depolama
description: Linux VM 'lerini kullanırken depolama hesaplarını işleyen Azure yönetilen disklere genel bakış
author: roygara
ms.service: virtual-machines-linux
ms.topic: overview
ms.date: 11/06/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7e9a5e1b1e1b0c879a1264573e7073e70a1e95fc
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035857"
---
# <a name="introduction-to-azure-managed-disks"></a>Azure yönetilen disklere giriş

Azure yönetilen disk, bir sanal sabit disktir (VHD). Bunu, şirket içi sunucuda bir fiziksel disk gibi düşünebilirsiniz, ancak sanallaştırılabilir. Azure yönetilen diskler, Azure 'daki bir rastgele GÇ depolama nesnesi olan sayfa Blobları olarak depolanır. Sayfa Blobları, blob kapsayıcıları ve Azure depolama hesapları üzerinde bir soyutlama olduğundan, yönetilen bir disk ' Managed ' çağrısı yaptık. Yönetilen disklerle, tüm yapmanız gereken diski temin etmek ve Azure 'un geri kalanını yapması gerekir.

Azure yönetilen diskleri iş yükleriniz ile kullanmayı seçtiğinizde, Azure bu diski sizin için oluşturur ve yönetir. Kullanılabilir disk türleri arasında Ultra disk, Premium katı hal sürücüsü (SSD), Standart SSD ve standart sabit disk sürücüsü (HDD) bulunur. Her bir disk türü hakkında daha fazla bilgi için bkz. [IaaS VM 'leri için disk türü seçme](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [IaaS VM 'Leri için bir disk türü seçin](disks-types.md)
