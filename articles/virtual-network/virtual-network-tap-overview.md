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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 6160dd09edc57f2f52306d4dad0dde413fff0616
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617191"
---
# <a name="virtual-network-tap"></a>Sanal ağ TAP
> [!IMPORTANT]
> Sanal ağ dokunma önizlemesi Şu anda tüm Azure bölgelerinde tutuluyor. Abonelik KIMLIĞINIZLE bize e-posta gönderebilirsiniz <azurevnettap@microsoft.com> ve önizleme hakkında gelecekteki güncelleştirmeler hakkında sizi bilgilendireceğiz. Geçici olarak, [Azure Marketi tekliflerinizde](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances%3Ball&search=Network%20Traffic&filters=partners)bulunan [paket Aracısı iş ortağı ÇÖZÜMLERIMIZ](#virtual-network-tap-partner-solutions) aracılığıyla dokunma/ağ görünürlük işlevselliği sağlayan aracı tabanlı veya NVA çözümlerini kullanabilirsiniz.

Azure sanal ağ TAP (Terminal erişim noktası), sanal makine ağ trafiğinizi bir ağ paketi toplayıcısına veya analiz aracına sürekli olarak akışla kullanmanıza olanak sağlar. Toplayıcı veya Analiz Aracı bir [ağ sanal gereç](https://azure.microsoft.com/solutions/network-appliances/) ortağı tarafından sağlanır. Sanal ağ dokunarak çalışmak üzere doğrulanan iş ortağı çözümlerinin listesi için bkz. [iş ortağı çözümleri](#virtual-network-tap-partner-solutions).
Aşağıdaki resimde, sanal ağ TAP 'ın nasıl çalıştığı gösterilmektedir. Sanal ağınızda dağıtılan bir sanal makineye bağlı bir [ağ arabirimine](virtual-network-network-interface.md) bir dokunma yapılandırması ekleyebilirsiniz. Hedef, izlenen ağ arabirimiyle veya eşlenmiş bir [sanal](virtual-network-peering-overview.md) ağla aynı sanal ağdaki bir sanal ağ IP adresidir. Sanal ağ TAP 'ın toplayıcı çözümü, yüksek kullanılabilirlik için bir Azure Iç yük dengeleyicinin arkasına dağıtılabilir.
![Sanal ağ dokunma çalışma şekli](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Önkoşullar

Bir sanal ağ TAP 'ı oluşturmadan önce, önizlemeye kaydettiğiniz bir onay e-postası almış ve [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) dağıtım modeli kullanılarak oluşturulmuş bir veya daha fazla sanal makineniz ve aynı Azure bölgesindeki dokunma trafiğini toplamak için bir iş ortağı çözümünün olması gerekir. Sanal ağınızda bir iş ortağı çözümünüz yoksa, bir tane dağıtmak için [iş ortağı çözümlerine](#virtual-network-tap-partner-solutions) bakın. Aynı veya farklı aboneliklerdeki birden çok ağ arabiriminden gelen trafiği toplamak için aynı sanal ağ TAP kaynağını kullanabilirsiniz. İzlenen ağ arabirimleri farklı aboneliklerdeyse, aboneliklerin aynı Azure Active Directory kiracısıyla ilişkilendirilmesi gerekir. Ayrıca, dokunma trafiğini toplamak için izlenen ağ arabirimleri ve hedef uç noktası aynı bölgedeki eşlenmiş sanal ağlarda bulunabilir. Bu dağıtım modelini kullanıyorsanız, sanal ağ [dokuyla yapılandırmadan önce sanal ağ](virtual-network-peering-overview.md) eşlemesinin etkinleştirildiğinden emin olun.

## <a name="permissions"></a>İzinler

Ağ arabirimlerinde yapılandırma uygulamak için kullandığınız hesapların [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya aşağıdaki tablodan gerekli eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanması gerekir:

| Eylem | Name |
|---|---|
| Microsoft. Network/virtualNetworkTaps/* | Bir sanal ağ dokunma kaynağı oluşturmak, güncelleştirmek, okumak ve silmek için gereklidir |
| Microsoft. Network/NetworkInterfaces/Read | TAP 'ın yapılandırıldığı ağ arabirimi kaynağını okumak için gereklidir |
| Microsoft. Network/tapConfigurations/* | Bir ağ arabiriminde dokunma yapılandırması oluşturmak, güncelleştirmek, okumak ve silmek için gereklidir |


## <a name="virtual-network-tap-partner-solutions"></a>Sanal ağ TAP iş ortağı çözümleri

### <a name="network-packet-brokers"></a>Ağ paketi aracıları

- [Azure için GigaVUE bulut paketi](https://www.gigamon.com/solutions/cloud/public-cloud/gigavue-cloud-suite-azure.html)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva PRMS](https://www.nubeva.com/azurevtap)
- [Büyük anahtar büyük Izleme yapısı](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>Güvenlik analizi, ağ/uygulama performansı yönetimi

- [Uyanık güvenlik](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch bulutu](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Koyu izleme](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis siber güvenliği](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/marketplace-azure)
- [Noname güvenliği](https://nonamesecurity.com/)
- [Riveryatak Steelmerkezi AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RSA Nettanık® platformu](https://www.rsa.com/content/dam/en/solution-brief/rsa-netwitness-platform-overview-for-federal-agencies.pdf)
- [Vectra Cognito](https://vectra.ai/microsoftazure)



## <a name="next-steps"></a>Sonraki adımlar

- [Bir sanal ağa dokunma oluşturmayı](tutorial-tap-virtual-network-cli.md)öğrenin.
