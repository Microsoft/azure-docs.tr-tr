---
title: Azure 'da SAP HANA için ekleme gereksinimleri (büyük örnekler) | Microsoft Docs
description: Azure 'daki SAP HANA için ekleme gereksinimleri (büyük örnekler).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99ad334a526b269879034dcc0e1cd0b1b22f1f7f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101188"
---
# <a name="onboarding-requirements"></a>Ekleme gereksinimleri

Bu liste Azure üzerinde SAP HANA çalıştırmaya yönelik gereksinimleri (daha büyük örnekler) ayrıştırır.

**Microsoft Azure**

- Azure 'da SAP HANA bağlantılı olabilecek bir Azure aboneliği (büyük örnekler).
- Microsoft Premier destek sözleşmesi. Azure 'da SAP çalıştırmaya ilişkin belirli bilgiler için, bkz [. sap destek notuna #2015553 – SAP Microsoft Azure: Destek önkoşulları](https://launchpad.support.sap.com/#/notes/2015553). 384 ve daha fazla CPU ile HANA büyük örnek birimleri kullanıyorsanız, Premier destek sözleşmesini Azure Rapid Response dahil etmek için de genişletmeniz gerekir.
- SAP ile boyutlandırma alıştırması gerçekleştirdikten sonra ihtiyacınız olan HANA büyük örnek SKU 'Larının farkında.

**Ağ bağlantısı**

- Şirket içi ile Azure arasında ExpressRoute: Şirket içi veri merkezinizi Azure 'a bağlamak için, ISS 'nizden en az 1 Gbps bir bağlantı sipariş ettiğinizden emin olun. HANA büyük örnek birimleri ve Azure arasında bağlantı, ExpressRoute teknolojisini de kullanıyor. HANA büyük örnek birimleri ve Azure arasındaki bu ExpressRoute bağlantısı, bu belirli ExpressRoute devresi için tüm veri giriş ve çıkış ücretleri dahil olmak üzere HANA büyük örnek birimlerinin fiyatına dahildir. Bu nedenle, müşteri olarak, şirket içi ve Azure arasında ExpressRoute bağlantılarınızın ötesinde ek maliyetlerle karşılaşmazsınız.

**İşletim sistemi**

- SAP uygulamaları için SUSE Linux Enterprise Server 12 ' ye yönelik lisanslar.

   > [!NOTE] 
   > Microsoft tarafından sunulan işletim sistemi SUSE 'e kayıtlı değildir. Bir abonelik yönetim aracı örneğine bağlı değil.

- Azure 'da bir VM 'de dağıtılan SUSE Linux abonelik yönetim aracı. Bu araç, Azure 'daki SAP HANA (büyük örnekler) SUSE tarafından Kaydolmakta ve sırasıyla güncelleştirilmesini sağlar. (HANA büyük örnek veri merkezi 'nde internet erişimi yoktur.) 
- SAP HANA için Red Hat Enterprise Linux 6,7 veya 7. x lisansları.

   > [!NOTE]
   > Microsoft tarafından sunulan işletim sistemi Red Hat ile kayıtlı değil. Red Hat abonelik Yöneticisi örneğine bağlı değildir.

- Red Hat abonelik Yöneticisi bir VM 'de Azure 'da dağıtılır. Red Hat abonelik Yöneticisi, Azure 'daki SAP HANA (büyük örnekler), Red Hat tarafından kaydedilmesini ve sırasıyla güncelleştirilmesini sağlar. (Azure büyük örnek damgasında dağıtılan kiracının içinden doğrudan internet erişimi yoktur.)
- SAP, Linux sağlayıcınızda de bir destek sözleşmesi olmasını gerektirir. Bu gereksinim, HANA büyük örnek çözümü veya Linux 'u Azure 'da çalıştırdığınız olguyu tarafından kaldırılmaz. Linux Azure Galeri görüntülerinin bazılarından farklı olarak hizmet ücreti, HANA büyük örnek çözüm teklifine dahil *değildir* . Linux dağıtıcısına sahip destek sözleşmeleri ile ilgili SAP gereksinimlerini karşılamak sizin sorumluluğunuzdadır. 
   - SUSE Linux için SAP Note #1984787-SUSE Linux Enterprise Server 12 ' de [destek sözleşmeleri gereksinimlerini arayın: Yükleme notları](https://launchpad.support.sap.com/#/notes/1984787) ve [SAP Not #1056161-SAP uygulamaları için SUSE öncelik desteği](https://launchpad.support.sap.com/#/notes/1056161).
   - Red Hat Linux için, HANA büyük örnek işletim sistemlerine yönelik destek ve hizmet güncelleştirmelerini içeren doğru abonelik düzeylerine sahip olmanız gerekir. Red hat, SAP çözümü için Red Hat Enterprise Linux aboneliğini önerir. Başvurun https://access.redhat.com/solutions/3082481. 

Farklı Linux sürümlerindeki farklı SAP HANA sürümlerinin destek matrisi için bkz. [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).

İşletim sisteminin ve HLı üretici yazılımının/sürücü sürümlerinin uyumluluk matrisi için, [hLi Için Işletim sistemi yükseltmesine](os-upgrade-hana-large-instance.md)başvurun.


> [!IMPORTANT] 
> Tür II birimleri için, bu noktada yalnızca SLES 12 SP2 işletim sistemi sürümü destekleniyor. 


**Veritabanı**

- SAP HANA için lisanslar ve yazılım yükleme bileşenleri (platform veya Enterprise Edition).

**Uygulamalar**

- SAP HANA ve ilgili SAP destek sözleşmelerine bağlanan tüm SAP uygulamaları için lisanslar ve yazılım yükleme bileşenleri.
- Azure (büyük örnekler) ortamlarında ve ilgili destek sözleşmelerinde SAP HANA birlikte kullanılan tüm SAP olmayan uygulamalar için lisanslar ve yazılım yükleme bileşenleri.

**Becerilere**

- Azure IaaS ve bileşenleri hakkında deneyim ve bilgi.
- Azure 'da SAP iş yükünün nasıl dağıtılacağı hakkında bilgi ile deneyim.
- SAP HANA yükleme sertifikalı kişisel.
- SAP HANA etrafında yüksek kullanılabilirlik ve olağanüstü durum kurtarma tasarlamak için SAP mimari becerileri.

**SAP**

- Beklenki, SAP müşterisi ve SAP ile destek sözleşmeniz olur.
- Özellikle, HANA büyük örnek SKU 'Larının tür II sınıfının uygulamaları için, SAP HANA sürümlerindeki SAP ve büyük ölçekli ölçekli bir donanımla ilgili son yapılandırma uygulamalarına başvurun.

**Sonraki adımlar**
- [Azure 'da SAP HANA (büyük örnekler) mimarisine](hana-architecture.md) başvurun