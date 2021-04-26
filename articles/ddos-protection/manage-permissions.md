---
title: Azure DDoS koruma planı izinleri
description: Koruma planında izin yönetme hakkında bilgi edinin.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: df53062c7c897493a47d88ea2873f9710b9825bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99806265"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>DDoS koruma planlarını yönetme: izinler ve kısıtlamalar

DDoS koruma planı bölgeler ve abonelikler arasında çalışmaktadır. Aynı plan, kiracınız genelinde farklı bölgelerdeki diğer aboneliklerdeki sanal ağlara bağlanabilir. Planın ilişkilendirildiği abonelik, plan için aylık yinelenen faturanızı ve korunan genel IP adreslerinin sayısının 100 ' i aşması durumunda fazla kullanım ücreti sağlar. DDoS fiyatlandırması hakkında daha fazla bilgi için bkz. [fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="prerequisites"></a>Önkoşullar

- Bu öğreticideki adımları tamamlayabilmeniz için önce bir [Azure DDoS standart koruma planı](manage-ddos-protection.md)oluşturmanız gerekir.

## <a name="permissions"></a>İzinler

DDoS koruma planlarıyla çalışmak için, hesabınız [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya aşağıdaki tabloda listelenen uygun eylemlere atanmış [özel](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) bir role atanmalıdır:

| Eylem                                            | Name                                     |
| ---------                                         | -------------                            |
| Microsoft. Network/Ddosprotectionplanlar/okuma        | DDoS koruma planını okuyun              |
| Microsoft. Network/Ddosprotectionplanlar/yazma       | DDoS koruma planı oluşturma veya güncelleştirme  |
| Microsoft. Network/Ddosprotectionplanlar/silme      | DDoS koruma planını silme            |
| Microsoft. Network/Ddosprotectionplanlar/JOIN/Action | DDoS koruma planına ekleme              |

Bir sanal ağ için DDoS korumasını etkinleştirmek üzere hesabınıza [sanal ağlar için uygun eylemler](../virtual-network/manage-virtual-network.md#permissions)de atanmalıdır.

## <a name="azure-policy"></a>Azure İlkesi

Çoğu kuruluş için birden fazla planın oluşturulması gerekli değildir. Bir plan abonelikler arasında taşınamaz. Bir planın bulunduğu aboneliği değiştirmek istiyorsanız, var olan planı silmeniz ve yeni bir tane oluşturmanız gerekir.

Çeşitli aboneliklerine sahip olan ve tek bir planın bir maliyet denetimi kiracısında dağıtılmasını sağlamak isteyen müşteriler için, Azure [DDoS koruması standart planlarının oluşturulmasını kısıtlamak](https://aka.ms/ddosrestrictplan)üzere Azure İlkesi ' ni kullanabilirsiniz. Bu ilke, abonelik daha önce özel bir durum olarak işaretlenmediği sürece herhangi bir DDoS planı oluşturulmasını engeller. Bu ilke, DDoS planının dağıtıldığı, ancak bunların uyumsuz olarak işaretlenmesinin gerektiği tüm aboneliklerinin bir listesini gösterir.


## <a name="next-steps"></a>Sonraki adımlar

DDoS koruma planınız için telemetri görüntüleme ve yapılandırma hakkında bilgi edinmek için öğreticilere devam edin.

> [!div class="nextstepaction"]
> [DDoS konuma telemetrisini görüntüleme ve yapılandırma](telemetry.md)
