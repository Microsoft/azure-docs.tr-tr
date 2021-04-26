---
title: Azure ağ Izleyicisi 'nde IP akışına giriş doğrulama | Microsoft Docs
description: Bu sayfa, ağ Izleyicisi IP akışı doğrulama özelliğine genel bakış sağlar
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: d6f6fe81c121f547f79fa34be77aab1fb6c0875a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899363"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Azure ağ Izleyicisi 'nde IP akışına giriş doğrulama

IP akışı doğrulama, bir paketin bir sanal makineye veya bir sanal makineye izin verildiğini veya reddedildiğini denetler. Bilgiler yön, protokol, yerel IP, uzak IP, yerel bağlantı noktası ve uzak bağlantı noktasından oluşur. Paket bir güvenlik grubu tarafından reddedilirse, paketi reddeden kuralın adı döndürülür. Herhangi bir kaynak veya hedef IP 'si seçilebilir, ancak IP akışı doğrulama, yöneticilerin internet 'ten veya şirket içi ortamdan gelen bağlantı sorunlarını hızla tanılamasına yardımcı olur.

IP akışı doğrulama, ağ arabirimine uygulanan alt ağ veya sanal makine NIC gibi tüm ağ güvenlik grupları (NSG 'ler) için kurallara bakar. Trafik akışı daha sonra bu ağ arabiriminden veya bu arabirimden yapılandırılan ayarlara göre doğrulanır. IP akışı doğrulama, bir ağ güvenlik grubundaki bir kuralın bir sanal makineye giriş veya çıkış trafiğini engelleyip engellemediğini onaylamadığında yararlıdır.

IP akışı doğrulamasını çalıştırmayı planladığınız tüm bölgelerde ağ Izleyicisi örneğinin oluşturulması gerekir. Ağ Izleyicisi bölgesel bir hizmettir ve yalnızca aynı bölgedeki kaynaklara karşı çalıştırılabilir. Kullanılan örnek, NIC veya alt ağ ile ilişkili tüm yollar hala döndürüldüğünden, IP akışı doğrulama sonuçlarını etkilemez.

![1][1]

## <a name="next-steps"></a>Sonraki adımlar

Portal üzerinden belirli bir sanal makine için bir pakete izin verildiğini veya reddedildiğini öğrenmek için aşağıdaki makaleyi ziyaret edin. [Portalı kullanarak IP akışı doğrulama ile bir VM 'de trafiğe izin verilip verilmediğini denetleyin](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

