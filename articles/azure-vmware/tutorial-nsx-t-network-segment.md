---
title: Öğretici-Azure VMware çözümünde NSX-T ağ kesimi ekleme
description: VCenter 'daki sanal makineler (VM) için kullanmak üzere NSX-T ağ segmenti oluşturmayı öğrenin.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 9125e552f9641a2d26b9584b66a4447f9c152161
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462141"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>Öğretici: Azure VMware çözümünde bir ağ kesimi ekleme 

VCenter 'da oluşturulan sanal makineler (VM) NSX-T içinde oluşturulan ağ kesimlerine yerleştirilir ve vCenter 'da görünür.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Ağ kesimleri eklemek için NSX-T Yöneticisi 'nde gezin
> * Yeni bir ağ kesimi Ekle
> * VCenter 'daki yeni ağ kesimini gözlemleyin

## <a name="prerequisites"></a>Önkoşullar

VCenter ve NSX-T Yöneticisi arabirimlerine erişimi olan bir Azure VMware çözümü özel bulutu. Daha fazla bilgi için bkz. [ağ Iletişimini yapılandırma](tutorial-configure-networking.md) öğreticisi.

## <a name="add-a-network-segment"></a>Ağ kesimi ekleme

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, vCenter 'daki VM 'Ler için kullanmak üzere bir NSX-T ağ kesimi oluşturdunuz. 

Artık şunları yapabilirsiniz: 

- [Azure VMware çözümü için DHCP oluşturma ve yönetme](manage-dhcp.md)
- [Azure VMware çözümünde VM dağıtmak için bir içerik kitaplığı oluşturma](deploy-vm-content-library.md) 
- [Şirket içi ortamlar için özel bir buluta](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
