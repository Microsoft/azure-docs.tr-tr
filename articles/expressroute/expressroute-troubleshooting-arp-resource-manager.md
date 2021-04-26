---
title: 'Azure ExpressRoute: ARP tabloları-sorun giderme'
description: Bu sayfa, bir ExpressRoute bağlantı hattı için Adres Çözümleme Protokolü (ARP) tablolarını alma hakkında yönergeler sağlar
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 12/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 7d8ae2c58979c66ebbbab366d172179bdeee4253
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97561588"
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

ARP tablosu, her eşleme türü için hem birincil hem de ikincil arabirimler için aşağıdaki bilgileri sağlar:

1. Şirket içi yönlendirici arabirimi IP adresini MAC adresine eşleme
2. ExpressRoute yönlendirici arabirimi IP adresini MAC adresine eşleme
3. Eşlemenin yaşı

ARP tabloları katman 2 yapılandırmasını doğrulamaya ve temel katman 2 bağlantı sorunlarını gidermenize yardımcı olabilir. 

Örnek ARP tablosu: 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


Aşağıdaki bölümde, ExpressRoute Edge yönlendiricileri tarafından görülen ARP tablolarını nasıl görüntüleyegörebileceğiniz hakkında bilgi verilmektedir. 

## <a name="prerequisites-for-learning-arp-tables"></a>ARP tablolarını öğrenme önkoşulları
Daha fazla ilerlemeniz için aşağıdaki bilgilerin doğru olduğundan emin olun:

* En az bir eşleme ile yapılandırılmış geçerli bir ExpressRoute devresi. Devre bağlantı sağlayıcısı tarafından tam olarak yapılandırılmalıdır. Siz veya bağlantı sağlayıcınız bu devrede en az Azure özel, Azure genel veya Microsoft eşlemesi yapılandırmış olmalıdır.
* Eş ayarlarını yapılandırmak için kullanılan IP adresi aralıkları. IP adreslerinin arabirimlere nasıl eşlenildiğini anlamak için [ExpressRoute yönlendirme gereksinimleri sayfasındaki](expressroute-routing.md) IP adresi atama örneklerini gözden geçirin. [ExpressRoute eşleme yapılandırması sayfasını](expressroute-howto-routing-arm.md)inceleyerek eşleme yapılandırması hakkında bilgi edinebilirsiniz.
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

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure private peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

# ARP table for Azure private peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 
```

Örnek çıktı, yollardan biri için aşağıda gösterilmektedir

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>Azure genel eşlemesi için ARP tabloları
Aşağıdaki cmdlet, Azure genel eşleme için ARP tabloları sağlar

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure public peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

# ARP table for Azure public peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 
```


Örnek çıktı, yollardan biri için aşağıda gösterilmektedir

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>Microsoft eşlemesi için ARP tabloları
Aşağıdaki cmdlet, Microsoft eşlemesi için ARP tabloları sağlar

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Microsoft peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

# ARP table for Microsoft peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 
```


Örnek çıktı, yollardan biri için aşağıda gösterilmektedir

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Bu bilgileri kullanma
Eşleme ARP tablosu, katman 2 yapılandırmasını ve bağlantısını Doğrula ' yı tespit etmek için kullanılabilir. Bu bölümde, ARP tablolarının farklı senaryolarda nasıl görüneceğine ilişkin bir genel bakış sunulmaktadır.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Bir devre çalışma durumunda olduğunda ARP tablosu (beklenen durum)
* ARP tablosunun, geçerli bir IP adresi ve MAC adresi olan şirket içi tarafa yönelik bir girişi olacaktır. Aynı şekilde Microsoft tarafı için de görünebilirler. 
* Şirket içi IP adresinin son sekizlisinin her zaman tek bir sayı olması gerekir.
* Microsoft IP adresinin son sekizlisinin her zaman çift sayı olması gerekir.
* Aynı MAC adresi, her üç eşleme için de Microsoft tarafında görünür (birincil/ikincil). 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Şirket içi/bağlantı sağlayıcı tarafında sorun yaşandığında ARP tablosu
Şirket içi veya bağlantı sağlayıcıdaki bir sorun oluşursa ARP tablosu iki işlemlerden birini gösterir. Şirket içi MAC adresinde eksik göster ' i görürsünüz veya yalnızca ARP tablosunda Microsoft girişi ' ni görürsünüz.
  
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------   
  0 On-Prem           65.0.0.1   Incomplete
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```
veya
   
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```  

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
* ExpressRoute devreniz için katman 3 yapılandırmasını doğrulayın.
  * BGP oturumlarının durumunu öğrenmek için rota özeti alın.
  * ExpressRoute genelinde hangi öneklerin tanıtıldığı için rota tablosunu alın.
* Gelen/giden baytları inceleyerek veri aktarımını doğrulayın.
* Sorun yaşamaya devam ediyorsanız [Microsoft desteği](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ile bir destek bileti açın.

