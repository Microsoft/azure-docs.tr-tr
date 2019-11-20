---
title: Azure 'daki SAP HANA genel bakış (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA dağıtmaya genel bakış (büyük örnekler).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ea337101a5fe44e42ce85c17fec32028c75d3b85
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101176"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Azure üzerinde SAP HANA (Büyük Örnekler) nedir?

Azure 'da SAP HANA (büyük örnekler), Azure için benzersiz bir çözümdür. Azure, SAP HANA dağıtmak ve çalıştırmak için sanal makineler sağlamaya ek olarak size adanmış çıplak sunucularda SAP HANA çalıştırma ve dağıtma olanağı sunar. Azure 'da SAP HANA (büyük örnekler) çözüm, size atanan paylaşılmayan konak/sunucu çıplak donanımlarında oluşturulur. Sunucu donanımı, işlem/sunucu, ağ ve depolama altyapısını içeren daha büyük damgalara katıştırılır. Buna bir bileşim olarak, HANA 'ya özel veri merkezi tümleştirmesi (TDı) sertifikalandırilmiştir. Azure 'daki SAP HANA (büyük örnekler) farklı sunucu SKU 'Ları veya boyutları sunar. Birimlerde 36 Intel CPU çekirdeği ve 768 GB bellek bulunabilir ve en fazla 480 Intel CPU çekirdeği ve 24 TB 'a kadar bellek bulunan birimlere gidebilirsiniz.

Altyapı damgası içindeki müşteri yalıtımı, kiracılar içinde gerçekleştirilir ve şöyle görünür:

- **Ağ iletişimi**: Altyapı yığınındaki müşterilerin, müşteri tarafından atanan kiracı başına sanal ağlar aracılığıyla yalıtımı. Tek bir müşteriye bir kiracı atanır. Müşterinin birden çok kiracının olması olabilir. Kiracıların ağ yalıtımı, kiracılar aynı müşteriye ait olsa bile, altyapı damgası düzeyindeki kiracılar arasındaki ağ iletişimini yasaklar.
- **Depolama bileşenleri**: Depolama birimleri atanmış depolama sanal makineler aracılığıyla yalıtımı. Depolama birimleri, yalnızca bir depolama sanal makinesine atanabilir. Bir depolama sanal makinesi, SAP HANA TDı sertifikalı altyapı yığınında yalnızca tek bir kiracıya atanır. Sonuç olarak, bir depolama sanal makinesine atanan depolama birimlerine yalnızca belirli bir ve ilgili kiracıdan erişilebilir. Bunlar farklı dağıtılan kiracılar arasında görünür değildir.
- **Sunucu veya konak**: Bir sunucu veya konak birimi, müşteriler veya kiracılar arasında paylaşılmaz. Bir müşteriye dağıtılan bir sunucu veya konak, tek bir kiracıya atanan atomik çıplak bir işlem birimidir. Bir konak veya sunucuyu başka bir müşteriyle paylaştırmanıza neden olabilecek bir donanım bölümlendirme veya yumuşak bölümlendirme kullanılmaz. Belirli bir kiracının depolama sanal makinesine atanan depolama birimleri böyle bir sunucuya bağlanır. Bir kiracı, özel olarak atanmış farklı SKU 'Lara ait bir veya birden çok sunucu birimine sahip olabilir.
- Azure 'daki bir SAP HANA (büyük örnekler) altyapı damgasında, ağ, depolama ve işlem düzeyindeki kiracı kavramları aracılığıyla birçok farklı kiracı dağıtılır ve birbirlerine karşı yalıtılır. 


Bu çıplak sunucu birimleri yalnızca SAP HANA çalıştırmak için desteklenir. SAP uygulama katmanı veya iş yükü orta-Ware katmanı sanal makinelerde çalışır. Azure 'da SAP HANA çalıştıran altyapı damgaları (büyük örnekler), Azure ağ hizmetleri geri kemikleri 'ne bağlıdır. Bu şekilde, Azure 'daki SAP HANA (büyük örnekler) birimler ve sanal makineler arasında düşük gecikmeli bağlantı sağlanır.

2019 Temmuz itibariyle, HANA büyük örnek damgalarının ve dağıtımların konumunun iki farklı düzeltmesi arasında ayrım yaptık:

- "Düzeltme 3" (Rev 3): Müşterinin, 2019 Temmuz 'dan önce dağıtılması için kullanılabilir hale getirilen damgalar
- "Düzeltme 4" (Rev 4): Azure VM konaklarına yakın bir yerde dağıtılan ve şu ana kadar Azure bölgelerinde yayınlanan yeni damga tasarımı:
    -  Batı ABD 2 
    -  East US 
    -  Batı Avrupa
    -  Kuzey Avrupa


Bu belge, Azure 'daki SAP HANA (büyük örnekler) kapsayan çeşitli belgelerden biridir. Bu belgede çözüm tarafından sunulan temel mimari, sorumluluklar ve hizmetler tanıtılmaktadır. Çözümün üst düzey özellikleri de ele alınmıştır. Ağ ve bağlantı gibi diğer birçok alan için, diğer dört belge ayrıntıları ve detaya gitme bilgilerini kapsar. Azure 'daki SAP HANA belgeleri (büyük örnekler), VM 'lerde SAP NetWeaver yüklemesinin veya SAP NetWeaver dağıtımlarının yönlerini kapsamaz. Azure üzerinde SAP NetWeaver, aynı Azure belge kapsayıcısında bulunan ayrı belgelerde ele alınmıştır. 


HANA büyük örnek kılavuzunun farklı belgeleri aşağıdaki alanlara sahiptir:

- [Azure 'da SAP HANA (büyük örnekler) genel bakış ve mimari](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 'da SAP HANA (büyük örnekler) altyapı ve bağlantı](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 'da SAP HANA (büyük örnekler) yükleyip yapılandırma](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 'da yüksek kullanılabilirlik ve olağanüstü durum kurtarma SAP HANA (büyük örnekler)](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 'da sorun giderme ve izleme SAP HANA (büyük örnekler)](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [STONITH kullanarak SUSE 'de yüksek kullanılabilirlik kurulumu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Düzeltme 3 damgalarının tür II SKU 'Ları için işletim sistemi yedekleme ve geri yükleme](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

**Sonraki adımlar**
- [Koşulları öğrenin](hana-know-terms.md)