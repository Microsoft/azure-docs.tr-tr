---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 72050ff4887642ba16d52948c1c1253ca01443c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102473690"
---
* Dönüştürme VM 'yi yeniden başlatarak, önceden var olan bir bakım penceresi sırasında sanal makinelerinizin geçişini zamanlayın. 

* Bu dönüştürme geri alınamaz. 

* [Sanal makine katılımcısı](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolüne sahip tüm kullanıcıların VM boyutunu (ön dönüştürme yapabilecekleri gibi) değiştiremeyeceği göz önünde bulundurun. Bunun nedeni, yönetilen diskleri olan VM 'Lerin, kullanıcının işletim sistemi disklerinde Microsoft. COMPUTE/diskler/Write iznine sahip olmasını gerektirir.

* Dönüştürmeyi test ettiğinizden emin olun. Üretimde geçişi gerçekleştirmeden önce bir sınama sanal makinesini geçirin.

* Dönüştürme sırasında VM’yi serbest bırakın. VM, dönüştürmeden sonra başlatıldığında yeni bir IP adresi alır. Gerekirse VM’ye [statik bir IP adresi atayabilirsiniz](../articles/virtual-network/public-ip-addresses.md).

* Dönüştürme işlemini desteklemek için gereken en düşük Azure VM Aracısı sürümünü gözden geçirin. Aracı sürümünüzü denetleme ve güncelleştirme hakkında daha fazla bilgi için bkz. [Azure 'DA VM aracıları Için en düşük sürüm desteği](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)
