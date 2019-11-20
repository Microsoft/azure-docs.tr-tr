---
title: 'Azure ExpressRoute: ARP tabloları-sorun giderme'
description: Bu sayfa, bir ExpressRoute devresine ait ARP tablolarını alma hakkında yönergeler sağlar
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 4f1bd064dbc0909be3deba9180be1d8b3c066fd4
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076573"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Kaynak Yöneticisi dağıtım modelinde ARP tabloları alma
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - Klasik](expressroute-troubleshooting-arp-classic.md)
> 
> 

Bu makalede, ExpressRoute bağlantı hattı için ARP tablolarını öğrenme adımlarında izlenecek yol gösterilmektedir.

> [!IMPORTANT]
> Bu belge, basit sorunları tanılamanıza ve düzeltmenize yardımcı olmaya yöneliktir. Microsoft desteği için bir değişiklik olması amaçlanmamıştır. Aşağıdaki adımları kullanarak sorunu çözemediğiniz [Microsoft desteği](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ile bir destek bileti açmanız gerekir.
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Adres Çözümleme Protokolü (ARP) ve ARP tabloları
Adres Çözümleme Protokolü (ARP), [RFC 826](https://tools.ietf.org/html/rfc826)' de tanımlanan bir katman 2 protokolüdür. ARP, Ethernet adresini (MAC adresi) bir IP adresi ile eşlemek için kullanılır.

ARP tablosu, belirli bir eşleme için IPv4 adresi ve MAC adresi eşleştirmesi sağlar. Bir ExpressRoute bağlantı hattı eşlemesi için ARP tablosu, her arabirim için aşağıdaki bilgileri sağlar (birincil ve ikincil)

1. Şirket içi yönlendirici arabirimi IP adresini MAC adresine eşleme
2. ExpressRoute yönlendirici arabirimi IP adresini MAC adresine eşleme
3. Eşlemenin yaşı

ARP tabloları katman 2 yapılandırmasını doğrulamaya ve temel katman 2 bağlantı sorunlarını gidermenize yardımcı olabilir. 

Örnek ARP tablosu: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


Aşağıdaki bölümde, ExpressRoute Edge yönlendiricileri tarafından görülen ARP tablolarını nasıl görüntüleyegörebileceğiniz hakkında bilgi verilmektedir. 

## <a name="prerequisites-for-learning-arp-tables"></a>ARP tablolarını öğrenme önkoşulları
Devam etmeden önce aşağıdakilere sahip olduğunuzdan emin olun

* En az bir eşleme ile yapılandırılmış geçerli bir ExpressRoute devresi. Devre bağlantı sağlayıcısı tarafından tam olarak yapılandırılmalıdır. Siz (veya bağlantı sağlayıcınız) Bu bağlantı üzerinde en az bir eşleme (Azure Private, Azure genel ve Microsoft) yapılandırılmış olmalıdır.
* Eşayarları yapılandırmak için kullanılan IP adresi aralıkları (Azure özel, Azure genel ve Microsoft). IP adreslerinin, sizin ve ExpressRoute tarafında bulunan arabirimlere nasıl eşlenildiğini anlamak için [ExpressRoute yönlendirme gereksinimleri sayfasındaki](expressroute-routing.md) IP adresi atama örneklerini gözden geçirin. [ExpressRoute eşleme yapılandırması sayfasını](expressroute-howto-routing-arm.md)inceleyerek eşleme yapılandırması hakkında bilgi edinebilirsiniz.
* Bu IP adresleriyle kullanılan arabirimlerin MAC adreslerinde ağ takımınızdan/bağlantı sağlayıcınızdan alınan bilgiler.
* Azure için en son PowerShell modülüne (sürüm 1,50 veya daha yeni) sahip olmanız gerekir.

> [!NOTE]
> Katman 3, hizmet sağlayıcısı tarafından sağlanıyorsa ve ARP tabloları aşağıdaki portalda/çıktıda boşsa, portalda Yenile düğmesini kullanarak devre yapılandırmasını yenileyin. Bu işlem, devreniz için doğru yönlendirme yapılandırmasını uygular. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>ExpressRoute devreniz için ARP tablolarını alma
Bu bölüm, PowerShell kullanarak her eşleme için ARP tablolarını nasıl görüntüleyekullanabileceğinizi gösteren yönergeler sağlar. Siz veya bağlantı sağlayıcınız daha fazla ilerlemeden eşlemeyi yapılandırmış olmalıdır. Her devrenin iki yolu vardır (birincil ve ikincil). Her yol için ARP tablosuna bağımsız olarak bakabilirsiniz.

### <a name="arp-tables-for-azure-private-peering"></a>Azure özel eşleme için ARP tabloları
Aşağıdaki cmdlet, Azure özel eşlemesi için ARP tabloları sağlar

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Örnek çıktı, yollardan biri için aşağıda gösterilmektedir

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Azure genel eşlemesi için ARP tabloları
Aşağıdaki cmdlet, Azure genel eşleme için ARP tabloları sağlar

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Örnek çıktı, yollardan biri için aşağıda gösterilmektedir

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Microsoft eşlemesi için ARP tabloları
Aşağıdaki cmdlet, Microsoft eşlemesi için ARP tabloları sağlar

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Örnek çıktı, yollardan biri için aşağıda gösterilmektedir

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Bu bilgileri kullanma
Eşleme ARP tablosu, katman 2 yapılandırmasını ve bağlantısını Doğrula ' yı tespit etmek için kullanılabilir. Bu bölümde, ARP tablolarının farklı senaryolarda nasıl görüneceğine ilişkin bir genel bakış sunulmaktadır.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Bir devre çalışma durumunda olduğunda ARP tablosu (beklenen durum)
* ARP tablosu, şirket içi taraf için geçerli bir IP adresi ve MAC adresi ve Microsoft tarafı için benzer bir giriş içeren bir girişe sahip olacaktır. 
* Şirket içi IP adresinin son sekizlisinin her zaman tek bir sayı olması gerekir.
* Microsoft IP adresinin son sekizlisinin her zaman çift sayı olması gerekir.
* Aynı MAC adresi, tüm 3 eşleme (birincil/ikincil) için Microsoft tarafında görünür. 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Şirket içi/bağlantı sağlayıcı tarafında sorunlar olduğunda ARP tablosu
Şirket içi veya bağlantı sağlayıcısında sorun varsa, ARP tablosunda yalnızca bir girişin göründüğünü veya şirket içi MAC adresi ' nin tamamlanmamış olduğunu görebilirsiniz. Bu işlem, Microsoft tarafında kullanılan MAC adresi ve IP adresi arasındaki eşlemeyi gösterir. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

veya
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Bu tür sorunların hatalarını ayıklamak için bağlantı sağlayıcınızla bir destek isteği açın. ARP tablosunun MAC adreslerine eşlenen arabirimlerin IP adresleri yoksa, aşağıdaki bilgileri gözden geçirin:
> 
> 1. MSEE-PR ve MSEE arasında bağlantı için atanan/30 alt ağının ilk IP adresi MSEE-PR arabiriminde kullanılırsa. Azure, Mgördüğü için her zaman ikinci IP adresini kullanır.
> 2. Müşterinin (C-Tag) ve hizmet (S-Tag) VLAN etiketlerinin hem MSEE-PR hem de MSEE çiftinin ile eşleşip eşleşmediğinden emin olun.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Microsoft tarafında sorun olduğunda ARP tablosu
* Microsoft tarafında sorun varsa, eşleme için gösterilen bir ARP tablosu görmezsiniz. 
* [Microsoft desteği](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ile bir destek bileti açın. Katman 2 bağlantısıyla ilgili bir sorun olduğunu belirtin. 

## <a name="next-steps"></a>Sonraki Adımlar
* ExpressRoute devreniz için katman 3 yapılandırmasını doğrulama
  * BGP oturumlarının durumunu öğrenmek için rota özeti al 
  * ExpressRoute genelinde hangi öneklerinin tanıtıldığı için yol tablosu al
* Gelen/giden baytları inceleyerek veri aktarımını doğrulama
* Bir destek bileti açın [Microsoft Destek](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) sorunları yaşamaya devam ediyorsanız.

