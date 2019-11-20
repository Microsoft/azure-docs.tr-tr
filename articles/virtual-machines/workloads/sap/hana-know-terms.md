---
title: Azure 'daki SAP HANA koşullarını öğrenin (büyük örnekler) | Microsoft Docs
description: Azure 'daki SAP HANA koşullarını (büyük örnekler) öğrenin.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fec9f18404fc45f4cf69cc13b1602f818dbddfaf
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099748"
---
# <a name="know-the-terms"></a>Terimleri öğrenme

Çeşitli yaygın tanımlar mimari ve teknik dağıtım kılavuzunda yaygın olarak kullanılır. Aşağıdaki terimleri ve bunların anlamlarını aklınızda edin:

- **IaaS**: Hizmet olarak altyapı.
- **PaaS**: Hizmet olarak platform.
- **SaaS**: Hizmet olarak yazılım.
- **Sap bileşeni**: ERP merkezi bileşeni (ECC), Iş ambarı (beyaz), çözüm Yöneticisi veya Enterprise Portal (EP) gibi tek bir SAP uygulaması. SAP bileşenleri geleneksel ABAP veya Java teknolojilerine veya Iş nesneleri gibi NetWeaver tabanlı olmayan bir uygulamaya dayalı olabilir.
- **SAP ortamı**: Bir veya daha fazla SAP bileşeni, geliştirme, kalite güvencesi, eğitim, olağanüstü durum kurtarma veya üretim gibi bir iş işlevi gerçekleştirmek üzere mantıksal olarak gruplandırılır.
- **SAP yatay**: BT ortamınızdaki tüm SAP varlıklarını gösterir. SAP yatay, tüm üretim ve üretim dışı ortamları içerir.
- **SAP sistemi**: Örneğin, bir SAP ERP geliştirme sistemi, bir SAP BW test sistemi ve SAP CRM üretim sistemi gibi DBMS katmanının ve uygulama katmanının birleşimi. Azure dağıtımları, bu iki katmanın şirket içi ve Azure arasında bölünme desteğini desteklemez. SAP sistemi şirket içinde dağıtılır ya da Azure 'da dağıtılır. SAP yatay 'ın farklı sistemlerini Azure veya şirket içi olarak dağıtabilirsiniz. Örneğin, şirket içi SAP CRM üretim sistemini dağıtırken Azure 'da SAP CRM geliştirme ve test sistemlerini dağıtabilirsiniz. Azure 'daki SAP HANA için (büyük örnekler), sanal makinelerde SAP sistemlerinin SAP uygulama katmanını ve ilgili SAP HANA örneğini Azure 'da (büyük örnekler) SAP HANA bir birimde barındırmanıza yöneliktir.
- **Büyük örnek damgası**: SAP HANA TDı sertifikalı ve Azure 'da SAP HANA örnekleri çalıştırmak için ayrılmış bir donanım altyapısı yığını.
- **Azure 'da SAP HANA (büyük örnekler):** Azure 'daki teklifin, farklı Azure bölgelerinde büyük örnek Damgalarında dağıtılan SAP HANA TDı sertifikalı donanımlarda HANA örnekleri çalıştırmasına yönelik resmi ad. İlgili *Hana büyük örnek* terimi, *Azure 'Daki SAP HANA (büyük örnekler)* için kısaysa ve bu teknik dağıtım kılavuzunda yaygın olarak kullanılır.
- **Şirketler arası**: VM 'Lerin şirket içi veri merkezleri ile Azure arasında siteden siteye, çok siteli veya Azure ExpressRoute bağlantısı olan bir Azure aboneliğine dağıtıldığı bir senaryoyu açıklar. Yaygın Azure belgelerinde, bu tür dağıtımlar şirketler arası senaryolar olarak da açıklanmaktadır. Bağlantının nedeni şirket içi etki alanlarını, şirket içi Azure Active Directory/OpenLDAP ve şirket içi DNS 'yi Azure 'a genişletmenin nedenidir. Şirket içi yatay, Azure aboneliklerinin Azure varlıklarına genişletilir. Bu uzantıyla birlikte VM 'Ler, şirket içi etki alanının bir parçası olabilir. 

   Şirket içi etki alanının etki alanı kullanıcıları sunuculara erişebilir ve bu VM 'lerde (DBMS hizmetleri gibi) Hizmetleri çalıştırabilir. Şirket içinde dağıtılan ve Azure tarafından dağıtılan VM 'Ler arasındaki iletişim ve ad çözümlemesi mümkündür. Bu senaryo, çoğu SAP varlıklarının dağıtılmasının tipik bir yoludur. Daha fazla bilgi için, bkz. [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ve [Azure Portal kullanarak siteden siteye bağlantı Ile sanal ağ oluşturma](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Kiracı**: HANA büyük örnek damgasında dağıtılan bir müşteri, *kiracıya yalıtılmış.* Bir kiracı, diğer kiracılardan ağ, depolama ve işlem katmanında yalıtılmıştır. Farklı kiracılara atanan depolama ve işlem birimleri, her bir diğerini göremez veya HANA büyük örnek damgası düzeyinde birbirleriyle iletişim kurabilir. Müşteri, farklı kiracılarda dağıtımları olmasını seçebilir. Daha sonra bile, HANA büyük örnek damgası düzeyinde kiracılar arasında iletişim yoktur.
- **SKU kategorisi**: HANA büyük örneği için aşağıdaki iki SKU kategorisi sunulur:
    - **Tür ı sınıfı**: S72, S72m, S96, S144, S144m, S192, S192m ve S192xm
    - **Tür II sınıfı**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm ve S960m
- **Damga**: HANA büyük örneklerinin Microsoft iç dağıtım boyutunu tanımlar. HANA büyük örnek birimlerinin dağıtılmasından önce, bilgi işlem, ağ ve depolama raflarından oluşan bir HANA büyük örnek damgasında bir veri merkezi konumuna dağıtılması gerekir. Bu tür bir dağıtıma HANA büyük örnek damgası veya düzeltme 4 ' ten (aşağıya bakın) **büyük örnek satırı** teriminin bir kısmını kullanıyoruz.
- **Düzeltme**: HANA büyük örnek damgaları için iki farklı damga düzeltmesi vardır. Bunlar mimaride ve Azure sanal makine konaklarına yakınlığa göre farklılık gösterir
    - "Düzeltme 3" (Rev 3): 2016 yılının ortalarından dağıtılan orijinal tasarımdır
    - "Düzeltme 4" (Rev 4): Azure sanal makine konaklarına daha yakından yakınlık sağlayabilen ve Azure VM 'Leri ile HANA büyük örnek birimleri arasında daha düşük ağ gecikmesi sunan yeni bir tasarımdır 

Bulutta SAP iş yükünün nasıl dağıtılacağı konusunda çeşitli ek kaynaklar mevcuttur. SAP HANA dağıtımını Azure 'da yürütmeyi planlıyorsanız Azure IaaS ilkelerine ve Azure IaaS 'de SAP iş yüklerinin dağıtımına sahip olmanız ve bunların farkında olmanız gerekir. Devam etmeden önce daha fazla bilgi için bkz. [Azure sanal makineler 'de SAP çözümlerini kullanma](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) . 

**Sonraki adımlar**
- [HLi sertifikasyon](hana-certification.md) 'ye başvurun