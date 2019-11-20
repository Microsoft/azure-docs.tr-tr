---
title: CloudSimple sanal makinelerine göre Azure VMware çözümü genel bakış
description: CloudSimple sanal makineleri ve bunların avantajları hakkında bilgi edinin.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 213ab51dae20d281a1a0e0f8ea18f4bde888e64d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877907"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple sanal makinelerine genel bakış

CloudSimple, Azure portal VMware sanal makinelerini (VM) yönetmenizi sağlar.  VSphere kümenizdeki bir küme veya kaynak havuzu, aboneliğiniz ile eşleyerek Azure üzerinden yönetilir.

Azure 'dan CloudSimple VM oluşturmak için özel bulut vCenter 'unuzda bir VM şablonu bulunmalıdır.  Şablon, işletim sistemini ve uygulamaları özelleştirmek için kullanılır.  Kurumsal güvenlik ilkelerini karşılamak için şablon sanal makinesi sağlamlaştırılmış olabilir.  Sanal makine oluşturmak ve ardından bir self servis modeli kullanarak Azure portal kullanmak için şablonu kullanabilirsiniz.

## <a name="benefits"></a>Avantajlar

Azure portal CloudSimple sanal makineleri, kullanıcıların VMware sanal makinelerini oluşturmalarına ve yönetmesine yönelik bir self servis mekanizması sağlar.

* Özel bulut vCenter 'unuzda CloudSimple VM oluşturma
* VM özelliklerini yönetme
  * Disk Ekle/Kaldır
  * NIC 'Leri Ekle/Kaldır
* CloudSimple VM 'nizin güç işlemleri
  * Açma ve kapatma
  * VM'yi Sıfırla
* VM 'yi silme

## <a name="next-steps"></a>Sonraki adımlar

* [Azure 'Da VMware VM](quickstart-create-vmware-virtual-machine.md) 'lerini kullanmayı öğrenin
* [Azure aboneliğinizi](azure-subscription-mapping.md) nasıl eşleyeceğinizi öğrenin
