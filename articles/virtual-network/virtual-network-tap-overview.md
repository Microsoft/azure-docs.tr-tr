---
title: Azure sanal ağ 'ya genel bakış | Microsoft Docs
description: Sanal ağ dokunma hakkında bilgi edinin. Sanal ağ dokunma, sanal makine ağ trafiğinin bir paket toplayıcısına akışı olabilecek ayrıntılı bir kopyasını sağlar.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 99cd9fc1da009660023a246c5210e7f54bdebcfd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177427"
---
# <a name="virtual-network-tap"></a>Sanal ağ TAP

Azure sanal ağ TAP (Terminal erişim noktası), sanal makine ağ trafiğinizi bir ağ paketi toplayıcısına veya analiz aracına sürekli olarak akışla kullanmanıza olanak sağlar. Toplayıcı veya Analiz Aracı bir [ağ sanal gereç](https://azure.microsoft.com/solutions/network-appliances/) ortağı tarafından sağlanır. Sanal ağ dokunarak çalışmak üzere doğrulanan iş ortağı çözümlerinin listesi için bkz. [iş ortağı çözümleri](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Sanal ağ dokunma Şu anda tüm Azure bölgelerinde önizleme aşamasındadır. Sanal ağ TAP 'ı kullanmak için, abonelik KIMLIĞINIZLE <azurevnettap@microsoft.com> bir e-posta göndererek önizlemeye kaydolmalısınız. Aboneliğiniz kaydedildiğinde siz de bir e-posta alırsınız. Onay e-postası alınana kadar özelliği kullanamazsınız. Bu önizleme, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için kullanılmamalıdır. Belirli özellikler desteklenmiyor olabilir, kısıtlı yeteneklere sahip olabilir veya tüm Azure konumlarında mevcut olmayabilir. Ayrıntılar için [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ' na bakın.

## <a name="virtual-network-tap-partner-solutions"></a>Sanal ağ TAP iş ortağı çözümleri

### <a name="network-packet-brokers"></a>Ağ paketi aracıları

- [Büyük anahtar büyük Izleme yapısı](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigaon, GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva PRMS](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Güvenlik analizi, ağ/uygulama performansı yönetimi

- [Uyanık güvenlik](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch bulutu](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Koyu izleme](https://www.darktrace.com/en/azure/)
- [ExtraHop açığa çıkar (x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis siber güvenliği](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Riveryatak Steelmerkezi AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RSA Nettanık® platformu](https://www.rsa.com/azure)
- [Vectra Bilito](https://vectra.ai/microsoftazure)

Aşağıdaki resimde, sanal ağ TAP 'ın nasıl çalıştığı gösterilmektedir. Sanal ağınızda dağıtılan bir sanal makineye bağlı bir [ağ arabirimine](virtual-network-network-interface.md) bir dokunma yapılandırması ekleyebilirsiniz. Hedef, izlenen ağ arabirimiyle veya eşlenmiş bir [sanal](virtual-network-peering-overview.md) ağla aynı sanal ağdaki bir sanal ağ IP adresidir. Sanal ağ TAP 'ın toplayıcı çözümü, yüksek kullanılabilirlik için bir [Azure Iç yük dengeleyicinin](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#concepts) arkasına dağıtılabilir. Tek bir çözüme yönelik dağıtım seçeneklerini değerlendirmek için bkz. [iş ortağı çözümleri](#virtual-network-tap-partner-solutions).

![Sanal ağ dokunma çalışma şekli](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Önkoşullar

Bir sanal ağ TAP 'ı oluşturmadan önce, önizlemeye kaydettiğiniz bir onay e-postası almış ve [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) dağıtım modeli kullanılarak oluşturulmuş bir veya daha fazla sanal makineniz ve bunu toplamak için bir iş ortağı çözümü almış olmanız gerekir. Aynı Azure bölgesindeki trafiğe dokunun. Sanal ağınızda bir iş ortağı çözümünüz yoksa, bir tane dağıtmak için [iş ortağı çözümlerine](#virtual-network-tap-partner-solutions) bakın. Aynı veya farklı aboneliklerdeki birden çok ağ arabiriminden gelen trafiği toplamak için aynı sanal ağ TAP kaynağını kullanabilirsiniz. İzlenen ağ arabirimleri farklı aboneliklerdeyse, aboneliklerin aynı Azure Active Directory kiracısıyla ilişkilendirilmesi gerekir. Ayrıca, dokunma trafiğini toplamak için izlenen ağ arabirimleri ve hedef uç noktası aynı bölgedeki eşlenmiş sanal ağlarda bulunabilir. Bu dağıtım modelini kullanıyorsanız, sanal ağ [dokuyla yapılandırmadan önce sanal ağ](virtual-network-peering-overview.md) eşlemesinin etkinleştirildiğinden emin olun.

## <a name="permissions"></a>İzinler

Ağ arabirimlerinde yapılandırma uygulamak için kullandığınız hesapların [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya aşağıdaki tablodan gerekli eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanması gerekir:

| Eylem | Adı |
|---|---|
| Microsoft. Network/virtualNetworkTaps/* | Bir sanal ağ dokunma kaynağı oluşturmak, güncelleştirmek, okumak ve silmek için gereklidir |
| Microsoft. Network/NetworkInterfaces/Read | TAP 'ın yapılandırıldığı ağ arabirimi kaynağını okumak için gereklidir |
| Microsoft. Network/tapConfigurations/* | Bir ağ arabiriminde dokunma yapılandırması oluşturmak, güncelleştirmek, okumak ve silmek için gereklidir |

## <a name="next-steps"></a>Sonraki adımlar

- [Bir sanal ağa dokunma oluşturmayı](tutorial-tap-virtual-network-cli.md)öğrenin.
