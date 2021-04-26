---
title: Azure 'daki SAP HANA koşullarını öğrenin (büyük örnekler) | Microsoft Docs
description: Azure 'daki SAP HANA koşullarını (büyük örnekler) öğrenin.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 4/16/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a243b348c01e6d1297a6a1fe016e3b6bc8d98d47
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719088"
---
# <a name="know-the-terms"></a>Terimleri öğrenme

Çeşitli yaygın tanımlar mimari ve teknik dağıtım kılavuzunda yaygın olarak kullanılır. Aşağıdaki terimleri ve bunların anlamlarını aklınızda edin:

- **IaaS**: hizmet olarak altyapı.
- **PaaS**: hizmet olarak platform.
- **SaaS**: hizmet olarak yazılım.
- **Sap bileşeni**: ERP merkezi BILEŞENI (ECC), iş AMBARı (beyaz), çözüm yöneticisi veya ENTERPRISE Portal (EP) gibi tek bir SAP uygulaması. SAP bileşenleri geleneksel ABAP veya Java teknolojilerine veya Iş nesneleri gibi NetWeaver tabanlı olmayan bir uygulamaya dayalı olabilir.
- **SAP ortamı**: bir veya daha fazla sap bileşeni, geliştirme, kalite güvencesi, eğitim, olağanüstü durum kurtarma veya üretim gibi bir iş işlevi gerçekleştirmek üzere mantıksal olarak gruplandırılır.
- **SAP yatay**: BT ORTAMıNıZDAKI tüm sap varlıklarını gösterir. SAP yatay, tüm üretim ve üretim dışı ortamları içerir.
- **SAP System**: Örneğin, BIR SAP ERP geliştirme sistemi, bir SAP BW test SISTEMI ve SAP CRM üretim SISTEMI gibi DBMS katmanının ve uygulama katmanının birleşimi. Azure dağıtımları, bu iki katmanın şirket içi ve Azure arasında bölünme desteğini desteklemez. SAP sistemi şirket içinde dağıtılır ya da Azure 'da dağıtılır. SAP yatay 'ın farklı sistemlerini Azure veya şirket içi olarak dağıtabilirsiniz. Örneğin, şirket içi SAP CRM üretim sistemini dağıtırken Azure 'da SAP CRM geliştirme ve test sistemlerini dağıtabilirsiniz. Azure 'daki SAP HANA için (büyük örnekler), sanal makinelerde SAP sistemlerinin SAP uygulama katmanını ve ilgili SAP HANA örneğini Azure 'da (büyük örnekler) SAP HANA bir birimde barındırmanıza yöneliktir.
- **Büyük örnek damgası**: tdı sertifikalı SAP HANA ve Azure 'da SAP HANA örnekleri çalıştırmak için adanmış bir donanım altyapı yığını.
- **Azure 'da SAP HANA (büyük örnekler):** Azure 'daki teklifin, farklı Azure bölgelerinde büyük örnek Damgalarında dağıtılan SAP HANA TDı sertifikalı donanımlarda HANA örnekleri çalıştırmasına yönelik resmi ad. İlgili *Hana büyük örnek* terimi, *Azure 'Daki SAP HANA (büyük örnekler)* için kısaysa ve bu teknik dağıtım kılavuzunda yaygın olarak kullanılır.
- **Şirketler arası**: VM 'lerin şirket içi veri merkezleri ile Azure arasında siteden siteye, çok siteli veya Azure ExpressRoute bağlantısı olan bir Azure aboneliğine dağıtıldığı bir senaryoyu açıklar. Yaygın Azure belgelerinde, bu tür dağıtımlar şirketler arası senaryolar olarak da açıklanmaktadır. Bağlantının nedeni şirket içi etki alanlarını, şirket içi Azure Active Directory/OpenLDAP ve şirket içi DNS 'yi Azure 'a genişletmenin nedenidir. Şirket içi yatay, Azure aboneliklerinin Azure varlıklarına genişletilir. Bu uzantıyla birlikte VM 'Ler, şirket içi etki alanının bir parçası olabilir. 

   Şirket içi etki alanının etki alanı kullanıcıları sunuculara erişebilir ve bu VM 'lerde (DBMS hizmetleri gibi) Hizmetleri çalıştırabilir. Şirket içinde dağıtılan ve Azure tarafından dağıtılan VM 'Ler arasındaki iletişim ve ad çözümlemesi mümkündür. Bu senaryo, çoğu SAP varlıklarının dağıtılmasının tipik bir yoludur. Daha fazla bilgi için, bkz. [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) ve [Azure Portal kullanarak siteden siteye bağlantı Ile sanal ağ oluşturma](../../../vpn-gateway/tutorial-site-to-site-portal.md).
- **Kiracı**: Hana büyük örnek damgasında dağıtılan bir müşteri bir *kiracıya yalıtılmış.* Bir kiracı, diğer kiracılardan ağ, depolama ve işlem katmanında yalıtılmıştır. Farklı kiracılara atanan depolama ve işlem birimleri, her bir diğerini göremez veya HANA büyük örnek damgası düzeyinde birbirleriyle iletişim kurabilir. Müşteri, farklı kiracılarda dağıtımları olmasını seçebilir. Daha sonra bile, HANA büyük örnek damgası düzeyinde kiracılar arasında iletişim yoktur.
- **SKU kategorisi**: Hana büyük örneği için aşağıdaki iki SKU kategorisi sunulur:
    - **Tür ı sınıfı**: s72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 ve S224m
    - **Tür II Class**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm ve S960m
- **Damga**: Hana büyük örneklerin Microsoft iç dağıtım boyutunu tanımlar. HANA büyük örnek birimlerinin dağıtılmasından önce, bilgi işlem, ağ ve depolama raflarından oluşan bir HANA büyük örnek damgasında bir veri merkezi konumuna dağıtılması gerekir. Bu tür bir dağıtıma HANA büyük örnek damgası veya düzeltme 4 ' ten (aşağıya bakın) **büyük örnek satırı** teriminin bir kısmını kullanıyoruz.
- **Düzeltme**: Hana büyük örnek damgaları için iki farklı damga düzeltmesi vardır. Bunlar, mimaride ve Azure sanal makine konaklarına yakınlığa göre farklılık gösterir.
    - "Düzeltme 3" (Rev 3), 2016 ortasından dağıtılan orijinal tasarımdır.
    - "Düzeltme 4,2" (Rev 4,2), Azure sanal makine konaklarına daha yakından yakınlık sağlayan yeni bir tasarımdır. Rev 4,2, Azure VM 'Leri ve HANA büyük örnek birimleri arasında Ultra düşük ağ gecikme süresi sunar. Azure portal kaynaklar BareMetal altyapısı olarak adlandırılır. Müşteriler, kaynaklarına Azure portal BareMetal örnekleri olarak erişebilir. 

Bulutta SAP iş yükünün nasıl dağıtılacağı konusunda çeşitli ek kaynaklar mevcuttur. SAP HANA dağıtımını Azure 'da yürütmeyi planlıyorsanız Azure IaaS ilkelerine ve Azure IaaS 'de SAP iş yüklerinin dağıtımına sahip olmanız ve bunların farkında olmanız gerekir. Devam etmeden önce daha fazla bilgi için bkz. [Azure sanal makineler 'de SAP çözümlerini kullanma](get-started.md) . 

## <a name="next-steps"></a>Sonraki adımlar
- [HLi sertifikası](hana-certification.md)bölümüne bakın.