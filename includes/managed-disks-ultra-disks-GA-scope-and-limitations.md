---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c847b110cb821ebe3ca7e706fcaec592dcc58fa8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73600969"
---
Şimdilik, Ultra diskler ek sınırlamalara sahiptir ve bunlar şu şekildedir:

- , Bölge başına değişen sayıda kullanılabilirlik bölgesi ile aşağıdaki bölgelerde desteklenir:
    - Doğu ABD 2
    - Doğu ABD
    - Batı ABD 2
    - Güneydoğu Asya
    - Kuzey Avrupa
    - Batı Avrupa
    - Birleşik Krallık Güney 
- Yalnızca kullanılabilirlik alanları ile kullanılabilir (bölge dışındaki kullanılabilirlik kümeleri ve tek VM dağıtımları, bir ultra disk iliştirebilme özelliğine sahip olmaz)
- Yalnızca şu VM serisinde desteklenir:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Her VM boyutu, Ultra disklerle desteklenen her bölgede kullanılabilir değildir.
- Yalnızca veri diskleri olarak kullanılabilir ve yalnızca 4k fiziksel sektör boyutunu destekler  
- Yalnızca boş disk olarak oluşturulabilir  
- Disk anlık görüntülerini, VM görüntülerini, kullanılabilirlik kümelerini ve Azure disk şifrelemesini henüz desteklemez
- Azure Backup veya Azure Site Recovery tümleştirmeyi henüz desteklemez
- GA VM 'lerde ıOPS için geçerli en büyük sınır 80.000 ' dir.