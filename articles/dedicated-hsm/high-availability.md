---
title: Yüksek kullanılabilirlik-Azure ayrılmış HSM | Microsoft Docs
description: Azure adanmış HSM yüksek kullanılabilirliğe yönelik temel konular hakkında bilgi edinin. Bu makale bir örnek içerir.
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: c46ccbda35936ed94079a21e3e9c72405875b961
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608876"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure adanmış HSM yüksek kullanılabilirlik

Azure adanmış HSM, Microsoft 'un yüksek oranda kullanılabilir veri merkezleri tarafından sabitlenmiştir. Ancak, yüksek oranda kullanılabilir veri merkezi, yerelleştirilmiş arızalara ve çok büyük koşullarda bölgesel düzey hatalara karşı savunmasız kalır. Microsoft, birden çok cihazın tek bir rafı paylaşan cihazlara neden olmamasını sağlamak için bir bölgedeki farklı veri merkezlerinde HSM cihazları dağıtır. Bu HSM 'ler, Thales HA Grup özelliği kullanılarak bir bölgedeki veri merkezleri arasında eşlenilerek daha fazla yüksek kullanılabilirlik elde edilebilir. Ayrıca, bir olağanüstü durum kurtarma durumunda bölgesel yük devretmeyi ele almak için cihazları bölgeler arasında eşleştirmek mümkündür. Bu çok katmanlı yüksek kullanılabilirlik yapılandırmasıyla, uygulamaların çalışmasını sağlamak için herhangi bir cihaz hatası otomatik olarak karşılanır. Tüm veri merkezleri aynı zamanda site üzerinde yedek cihazlara ve bileşenlere sahiptir, bu nedenle başarısız olan tüm cihazlar zamanında değiştirilebilir.

## <a name="high-availability-example"></a>Yüksek kullanılabilirlik örneği

Yazılım düzeyinde yüksek kullanılabilirlik için HSM cihazlarının nasıl yapılandırılacağı hakkında bilgi ' Thales Luna 7 HSM Yönetim Kılavuzu ' nda bulunur. Bu belge  [Thales HSM sayfasında](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms)bulunur.

Aşağıdaki diyagramda, yüksek oranda kullanılabilir bir mimari gösterilmektedir. Bölgede birden çok cihazı ve ayrı bir bölgede eşleştirilmiş birden çok cihazı kullanır. Bu mimari en az dört HSM cihaz ve sanal ağ bileşeni kullanır.

![Yüksek kullanılabilirlik diyagramı](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Sonraki adımlar

Yüksek kullanılabilirlik ve güvenlik gibi hizmetin temel kavramlarının cihaz sağlama ve uygulama tasarımı ya da dağıtımdan önce iyi anlaşıldığından emin olmanız önerilir.
Daha fazla kavram düzeyi konuları:

* [Dağıtım mimarisi](deployment-architecture.md)
* [Fiziksel Güvenlik](physical-security.md)
* [Ağ](networking.md)
* [Desteklenebilirlik](supportability.md)
* [İzleme](monitoring.md)

Yüksek kullanılabilirlik için HSM cihazlarını yapılandırma hakkında ayrıntılı bilgi için, lütfen yönetici kılavuzlarına yönelik [Thales müşteri destek portalına](https://supportportal.thalesgroup.com/csm) başvurun ve Bölüm 6 ' yı inceleyin.
