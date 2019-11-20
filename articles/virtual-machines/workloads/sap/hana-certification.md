---
title: Azure 'da SAP HANA sertifikası (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA sertifikası (büyük örnekler).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c2c919d6e85a2b708dc247b1642e7e8987df81b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099890"
---
# <a name="certification"></a>Sertifika

NetWeaver sertifikalarının yanı sıra SAP, Azure IaaS gibi belirli altyapılarda SAP HANA desteklemek için SAP HANA özel bir sertifika gerektirir.

NetWeaver üzerinde çekirdek SAP Note ve bir derece SAP HANA sertifikası, [SAP Note #1928533 – Azure 'da SAP uygulamaları: Desteklenen Ürünler ve Azure VM türleri](https://launchpad.support.sap.com/#/notes/1928533).

Azure (büyük örnekler) birimlerindeki SAP HANA sertifika kayıtları, [SAP HANA sertifikalı IaaS platformları](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) sitesinde bulunabilir. 

SAP HANA sertifikalı IaaS platformları sitesinde başvurulan Azure (büyük örnekler) türlerindeki SAP HANA, Microsoft ve SAP müşterilerine Azure 'da büyük SAP Business Suite, SAP BW, S/4 HANA, siyah beyaz/4HANA veya diğer SAP HANA iş yüklerini dağıtma yeteneği sağlar. Çözüm, SAP-HANA sertifikalı adanmış donanım damgasına ([SAP HANA özel veri merkezi tümleştirmesi – TDI](https://scn.sap.com/docs/DOC-63140)) dayanır. SAP HANA TDı ile yapılandırılmış bir çözüm çalıştırırsanız, SAP HANA tabanlı tüm uygulamalar (SAP HANA üzerindeki SAP Business Suite SAP BW, SAP HANA, S4/HANA ve BW4/HANA) donanım altyapısında çalışır.

VM 'lerdeki SAP HANA çalıştırmaya kıyasla bu çözüm avantajına sahiptir. Daha büyük bellek birimleri sağlar. Bu çözümü etkinleştirmek için aşağıdaki temel yönleri anlamanız gerekir:

- SAP uygulama katmanı ve SAP olmayan uygulamalar, normal Azure donanım damgaları 'nda barındırılan VM 'lerde çalışır.
- Müşteri şirket içi altyapısı, veri merkezleri ve uygulama dağıtımları, ExpressRoute (önerilen) veya bir sanal özel ağ (VPN) üzerinden bulut platformuna bağlanır. Active Directory ve DNS, Azure 'da da genişletilir.
- HANA iş yükünün SAP HANA veritabanı örneği Azure üzerinde SAP HANA çalışır (büyük örnekler). Büyük örnek damgası Azure ağa bağlı olduğundan, VM 'lerde çalışan yazılımlar, HANA büyük örneğinde çalışan HANA örneğiyle etkileşime geçebilir.
- Azure 'daki SAP HANA donanımı (büyük örnekler), SUSE Linux Enterprise Server veya Red Hat Enterprise Linux önceden yüklenmiş olarak bir IaaS içinde sunulan ayrılmış donanımdır. Sanal makinelerde olduğu gibi, işletim sistemiyle ilgili diğer güncelleştirmeler ve bakım de sizin sorumluluğunuzdadır.
- Hana büyük örnek birimlerinde SAP HANA çalıştırmak için gerekli olan HANA veya ek bileşenlerin yüklenmesi sorumluluğu sizin sorumluluğunuzdadır. Azure 'daki SAP HANA tüm devam eden işlemler ve yönetimi de sizin sorumluluğunuzdadır.
- Burada açıklanan çözümlere ek olarak, Azure aboneliğinize Azure 'daki SAP HANA bağlanan diğer bileşenleri (büyük örnekler) yükleyebilirsiniz. Örneğin, geçiş sunucuları, RDP sunucuları, SAP HANA Studio, SAP BI senaryoları için SAP veri Hizmetleri veya ağ izleme çözümleri gibi SAP HANA veritabanıyla iletişim sağlayan bileşenlerdir.
- Azure 'da olduğu gibi, HANA büyük örnek yüksek kullanılabilirlik ve olağanüstü durum kurtarma işlevselliği için destek sağlar.

**Sonraki adımlar**
- [HLI Için kullanılabilir SKU 'lara](hana-available-skus.md) başvurun 