---
title: App Service ortamları olan katmanlı güvenlik mimarisi-Azure
description: App Service ortamlarıyla katmanlı bir güvenlik mimarisi uygulama.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 2d9eedcdc66dceabdd6506c5b64f0c15c874efee
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070137"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>App Service ortamları ile katmanlı güvenlik mimarisi uygulama
## <a name="overview"></a>Genel Bakış
App Service ortamları bir sanal ağa dağıtılan yalıtılmış bir çalışma zamanı ortamı sağladığından, geliştiriciler her fiziksel uygulama katmanı için farklı düzeylerde ağ erişimi sağlayan katmanlı bir güvenlik mimarisi oluşturabilir.

API arka uçlarını genel Internet erişimi 'nden gizlemek ve yalnızca API 'Lerin yukarı akış Web uygulamaları tarafından çağrılmasına izin vermek yaygın bir uygulamadır.  [Ağ güvenlik grupları (NSG 'ler)][NetworkSecurityGroups] , API uygulamalarına genel erişimi kısıtlamak Için App Service ortamları içeren alt ağlarda kullanılabilir.

Aşağıdaki diyagramda, bir App Service Ortamı üzerinde dağıtılmış bir WebAPI tabanlı uygulamayla örnek bir mimari gösterilmektedir.  Üç ayrı App Service ortamda dağıtılan üç ayrı Web uygulaması örneği, aynı WebAPI uygulamasına arka uç çağrıları yapın.

![Kavramsal mimari][ConceptualArchitecture] 

Yeşil artı işaretleri, "apıai" içeren alt ağdaki ağ güvenlik grubunun, yukarı akış Web uygulamalarından gelen çağrılara izin verdiğini belirtir.  Ancak aynı ağ güvenlik grubu, Internet 'ten gelen genel trafiğe erişimi açıkça reddeder. 

Bu makalenin geri kalanında, "apıai" içeren alt ağda ağ güvenlik grubunu yapılandırmak için gereken adımlar gösterilir.

## <a name="determining-the-network-behavior"></a>Ağ davranışını belirleme
Hangi ağ güvenlik kurallarının gerekli olduğunu bilmek için, hangi ağ istemcilerinin API uygulamasını içeren App Service Ortamı erişmelerine izin verileceğini ve hangi istemcilerin engelleneceğini belirlemeniz gerekir.

[Ağ güvenlik grupları (NSG 'ler)][NetworkSecurityGroups] alt ağlara uygulandığından ve App Service ortamları alt ağlara dağıtıldığından, BIR NSG 'de bulunan kurallar, bir App Service ortamı çalıştıran **Tüm** uygulamalar için geçerlidir.  Bu makale için örnek mimariyi kullanarak, "apıai" içeren alt ağa bir ağ güvenlik grubu uygulandıktan sonra, "apıai" App Service Ortamı üzerinde çalışan tüm uygulamalar aynı güvenlik kuralları kümesiyle korunacaktır. 

* **Yukarı akış çağıranlarının giden IP adresini belirleme:**  Yukarı akış çağıranlarının IP adresi veya adresleri nedir?  Bu adreslerin NSG 'de açıkça erişime izin verilmesi gerekir.  App Service ortamları arasındaki çağrılar "Internet" çağrıları olarak kabul edildiği için, üç yukarı akış App Service ortamının her birine atanan giden IP adresinin "apıai" alt ağı için NSG 'de erişime izin verilmesi gerekir.   Bir App Service Ortamı çalışan uygulamalar için giden IP adresini belirleme hakkında daha fazla bilgi için, bkz. [ağ mimarisine][NetworkArchitecture] genel bakış makalesi.
* **Arka uç API 'SI uygulamasının kendisini çağırması gerekir mi?**  Bazen daha fazla ve hafif bir nokta, arka uç uygulamasının kendisini çağırması gereken senaryosudur.  Bir App Service Ortamı arka uç API uygulamasının kendisini çağırması gerekiyorsa, ayrıca "Internet" çağrısı olarak kabul edilir.  Örnek mimaride, "apıai" App Service Ortamı giden IP adresinden da erişime izin vermeyi gerektirir.

## <a name="setting-up-the-network-security-group"></a>Ağ güvenlik grubu ayarlanıyor
Giden IP adresleri kümesi bilindiğinde, bir sonraki adım bir ağ güvenlik grubu oluşturmak için kullanılır.  Ağ güvenlik grupları hem Kaynak Yöneticisi tabanlı sanal ağlar hem de klasik sanal ağlar için oluşturulabilir.  Aşağıdaki örneklerde, PowerShell kullanarak klasik bir sanal ağ üzerinde NSG oluşturma ve yapılandırma gösterilmektedir.

Örnek mimaride, ortamlar Orta Güney ABD bulunur, bu nedenle bu bölgede boş bir NSG oluşturulur:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

İlk olarak, App Service ortamları için [gelen trafikle][InboundTraffic] ilgili makalede belirtildiği gibi Azure Yönetim altyapısı için bir açık izin verme kuralı eklenmiştir.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Sonra, ilk yukarı akış App Service Ortamı HTTP ve HTTPS çağrılarına izin vermek için iki kural eklenmiştir ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

İkinci ve üçüncü yukarı akış App Service ortamları için ("fe2ase" ve "fe3ase") ile yeniden yinelenir.

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Son olarak, arka uç API 'sinin kendisine tekrar arayabilmesi için App Service Ortamı giden IP adresine erişim izni verin.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Her NSG, varsayılan olarak Internet 'ten gelen erişimi engelleyen bir varsayılan kurallar kümesine sahip olduğundan başka bir ağ güvenlik kuralı gerekli değildir.

Ağ güvenlik grubundaki kuralların tam listesi aşağıda gösterilmiştir.  Vurgulanan Son kuralın, açıkça erişim izni verilen çağıranlar dışındaki tüm çağıranlardan gelen erişimi engellediğini unutmayın.

![NSG yapılandırması][NSGConfiguration] 

Son adım, NSG 'yi "apıai" App Service Ortamı içeren alt ağa uygulamaktır.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

NSG alt ağa uygulandıktan sonra, yalnızca üç yukarı akış App Service ortamı ve API arka ucu içeren App Service Ortamı "apıai" ortamına çağrı yapılmasına izin verilir.

## <a name="additional-links-and-information"></a>Ek bağlantılar ve bilgiler
[Ağ güvenlik grupları](../../virtual-network/security-overview.md)hakkında bilgi.

[Gıden IP adreslerini][NetworkArchitecture] ve App Service ortamlarını anlama.

App Service ortamları tarafından kullanılan [ağ bağlantı noktaları][InboundTraffic] .

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
