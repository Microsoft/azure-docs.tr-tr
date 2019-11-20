---
title: 'Azure ExpressRoute: ARP tabloları-sorun giderme: klasik'
description: Bu sayfada, bir ExpressRoute bağlantı hattı klasik dağıtım modeli için ARP tablolarını alma yönergeleri sağlanır.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: 7c223e3802d499e002b12580b17cb9ee3f1bea97
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076604"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Klasik dağıtım modelinde ARP tabloları alma
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - Klasik](expressroute-troubleshooting-arp-classic.md)
> 
> 

Bu makalede, Azure ExpressRoute bağlantı hattı için Adres Çözümleme Protokolü (ARP) tablolarını alma adımlarında adım adım açıklanmaktadır.

> [!IMPORTANT]
> Bu belge, basit sorunları tanılamanıza ve düzeltmenize yardımcı olmaya yöneliktir. Microsoft desteği için bir değişiklik olması amaçlanmamıştır. Aşağıdaki kılavuzu kullanarak sorunu çözemezseniz [Microsoft Azure yardım + destek](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ile bir destek isteği açın.
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Adres Çözümleme Protokolü (ARP) ve ARP tabloları
ARP, [RFC 826](https://tools.ietf.org/html/rfc826)' de tanımlanan bir katman 2 protokolüdür. ARP, bir Ethernet adresini (MAC adresi) bir IP adresi ile eşlemek için kullanılır.

Bir ARP tablosu, belirli bir eşleme için IPv4 adresi ve MAC adresi eşleştirmesi sağlar. Bir ExpressRoute bağlantı hattı eşlemesi için ARP tablosu, her arabirim (birincil ve ikincil) için aşağıdaki bilgileri sağlar:

1. Şirket içi yönlendirici arabirimi IP adresini bir MAC adresine eşleme
2. ExpressRoute yönlendirici arabirimi IP adresini bir MAC adresine eşleme
3. Eşlemenin yaşı

ARP tabloları, katman 2 yapılandırmasını doğrulamaya ve temel katman 2 bağlantı sorunlarıyla ilgili sorunları gidermenize yardımcı olabilir.

ARP tablosuna bir örnek aşağıda verilmiştir:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Aşağıdaki bölümde, ExpressRoute Edge yönlendiricileri tarafından görülen ARP tablolarının nasıl görüntüleneceği hakkında bilgi verilmektedir.

## <a name="prerequisites-for-using-arp-tables"></a>ARP tablolarını kullanma önkoşulları
Devam etmeden önce aşağıdakilere sahip olduğunuzdan emin olun:

* En az bir eşleme ile yapılandırılmış geçerli bir ExpressRoute devresi. Devre bağlantı sağlayıcısı tarafından tam olarak yapılandırılmalıdır. Siz (veya bağlantı sağlayıcınız) Bu bağlantı noktasında en az birini (Azure Private, Azure genel veya Microsoft) yapılandırmanız gerekir.
* Eş ayarlarını yapılandırmak için kullanılan IP adresi aralıkları (Azure özel, Azure genel ve Microsoft). IP adreslerinin, sizin ve ExpressRoute tarafında bulunan arabirimlere nasıl eşlenildiğini anlamak için [ExpressRoute yönlendirme gereksinimleri SAYFASıNDAKI](expressroute-routing.md) IP adresi atama örneklerini gözden geçirin. [ExpressRoute eşleme yapılandırması sayfasını](expressroute-howto-routing-classic.md)inceleyerek eşleme yapılandırması hakkında bilgi edinebilirsiniz.
* Ağ takımınızdan veya bağlantı sağlayıcınızdan bu IP adresleriyle kullanılan arabirimlerin MAC adresleriyle ilgili bilgiler.
* Azure için en son Windows PowerShell modülü (sürüm 1,50 veya üzeri).

## <a name="arp-tables-for-your-expressroute-circuit"></a>ExpressRoute devreniz için ARP tabloları
Bu bölüm, PowerShell kullanarak her bir eşleme türü için ARP tablolarının nasıl görüntüleneceği hakkında yönergeler sağlar. Devam etmeden önce, sizin veya bağlantı sağlayıcınızın eşlemeyi yapılandırması gerekir. Her devrenin iki yolu vardır (birincil ve ikincil). Her yol için ARP tablosuna bağımsız olarak bakabilirsiniz.

### <a name="arp-tables-for-azure-private-peering"></a>Azure özel eşleme için ARP tabloları
Aşağıdaki cmdlet, Azure özel eşlemesi için ARP tabloları sağlar:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Aşağıda yollardan biri için örnek çıktı verilmiştir:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Azure genel eşlemesi için ARP tabloları:
Aşağıdaki cmdlet, Azure genel eşlemesi için ARP tabloları sağlar:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Aşağıda yollardan biri için örnek çıktı verilmiştir:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Aşağıda yollardan biri için örnek çıktı verilmiştir:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Microsoft eşlemesi için ARP tabloları
Aşağıdaki cmdlet, Microsoft eşlemesi için ARP tabloları sağlar:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Aşağıdaki yollardan biri için örnek çıktı aşağıda gösterilmiştir:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Bu bilgileri kullanma
Eşleme ARP tablosu, katman 2 yapılandırmasını ve bağlantısını doğrulamak için kullanılabilir. Bu bölüm, ARP tablolarının farklı senaryolarda nasıl görüneceğine ilişkin bir genel bakış sağlar.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Bağlantı hattı işletimsel (beklenen) durumda olduğunda ARP tablosu
* ARP tablosunda, geçerli bir IP ve MAC adresi ile şirket içi taraf için bir giriş ve Microsoft tarafı için de benzer bir giriş bulunur.
* Şirket içi IP adresinin son sekizli her zaman tek bir sayıdır.
* Microsoft IP adresinin son sekizli her zaman çift bir sayıdır.
* Aynı MAC adresi, her üç eşleme için de Microsoft tarafında görünür (birincil/ikincil).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Şirket içinde olduğunda veya bağlantı sağlayıcı tarafında sorun olduğunda ARP tablosu
 ARP tablosunda yalnızca bir giriş görüntülenir. MAC adresi ile Microsoft tarafında kullanılan IP adresi arasındaki eşlemeyi gösterir.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Bunun gibi bir sorunla karşılaşırsanız, bu sorunu çözmek için bağlantı sağlayıcınızla bir destek isteği açın.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Microsoft tarafında sorun olduğunda ARP tablosu
* Microsoft tarafında sorun varsa, eşleme için gösterilen bir ARP tablosu görmezsiniz.
* [Microsoft Azure yardım + desteğiyle](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)bir destek isteği açın. Katman 2 bağlantısıyla ilgili bir sorun olduğunu belirtin.

## <a name="next-steps"></a>Sonraki adımlar
* ExpressRoute devreniz için katman 3 yapılandırmasını doğrulayın:
  * BGP oturumlarının durumunu öğrenmek için bir rota özeti alın.
  * ExpressRoute genelinde hangi öneklerin tanıtıldığı hakkında daha fazla yol tablosu alın.
* Gelen ve giden baytları inceleyerek veri aktarımını doğrulayın.
* Sorun yaşamaya devam ediyorsanız [Microsoft Azure yardım + destek](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ile bir destek isteği açın.

