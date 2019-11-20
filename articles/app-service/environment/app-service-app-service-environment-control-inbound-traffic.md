---
title: App Service ortamı için gelen trafiği denetleme-Azure
description: Bir App Service Ortamı gelen trafiği denetlemek için ağ güvenlik kurallarını yapılandırma hakkında bilgi edinin.
services: app-service
documentationcenter: ''
author: ccompy
manager: erikre
editor: ''
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: c887ae5568bfd0f72f8d90daecd95547ed7b8b7d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070413"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>App Service Ortamı gelen trafiği denetleme
## <a name="overview"></a>Genel Bakış
Bir App Service Ortamı Azure Resource Manager Sanal ağda **veya** klasik dağıtım modeli [sanal ağında][virtualnetwork]oluşturulabilir.  Yeni bir sanal ağ ve yeni alt ağ bir App Service Ortamı oluşturulduğu sırada tanımlanabilir.  Alternatif olarak, önceden var olan bir sanal ağda ve önceden var olan alt ağda bir App Service Ortamı oluşturulabilir.  Haziran 2016 ' de yapılan bir değişiklik ile, ASE 'ler ortak adres aralıkları veya RFC1918 adres alanları (örn. özel adresler) kullanan sanal ağlara da dağıtılabilir.  App Service Ortamı oluşturma hakkında daha fazla bilgi için bkz. [App Service ortamı oluşturma][HowToCreateAnAppServiceEnvironment].

Bir alt ağ, HTTP ve HTTPS trafiğinin yalnızca belirli bir yukarı akış üzerinden kabul edildiği gibi, yukarı akış cihazları ve Hizmetleri arkasındaki gelen trafiği kilitlemek için kullanılabilecek bir ağ sınırı sağladığından, her zaman bir alt ağ içinde oluşturulmalıdır. App Service Ortamı IP adresleri.

Bir alt ağdaki gelen ve giden ağ trafiği bir [ağ güvenlik grubu][NetworkSecurityGroups]kullanılarak denetlenir. Gelen trafiği denetlemek için bir ağ güvenlik grubunda ağ güvenlik kuralları oluşturulması ve ardından ağ güvenlik grubu App Service Ortamı içeren alt ağa atanması gerekir.

Bir ağ güvenlik grubu bir alt ağa atandıktan sonra, ağ güvenlik grubunda tanımlanan izin verme ve reddetme kurallarına göre App Service Ortamı uygulamalara gelen trafiğe izin verilir/engellenir.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>App Service Ortamı kullanılan gelen ağ bağlantı noktaları
Ağ güvenlik grubuyla gelen ağ trafiğini kilitlemeden önce, bir App Service Ortamı tarafından kullanılan gerekli ve isteğe bağlı ağ bağlantı noktaları kümesini bilmeniz önemlidir.  Bazı bağlantı noktalarına gelen trafiği yanlışlıkla kapatmak bir App Service Ortamı işlev kaybına neden olabilir.

Bir App Service Ortamı tarafından kullanılan bağlantı noktalarının listesi aşağıda verilmiştir. Tersi belirtilmedikçe, tüm bağlantı noktaları **TCP**ile yapılır:

* 454:  Azure altyapısı tarafından SSL aracılığıyla App Service ortamları yönetmek ve sürdürmek için kullanılan **bağlantı noktası** .  Bu bağlantı noktasına giden trafiği engellemez.  Bu bağlantı noktası her zaman bir ASE 'nin Genel VIP 'sine bağlanır.
* 455:  Azure altyapısı tarafından SSL aracılığıyla App Service ortamları yönetmek ve sürdürmek için kullanılan **bağlantı noktası** .  Bu bağlantı noktasına giden trafiği engellemez.  Bu bağlantı noktası her zaman bir ASE 'nin Genel VIP 'sine bağlanır.
* 80:  Bir App Service Ortamı App Service planlarında çalışan uygulamalara gelen HTTP trafiği için varsayılan bağlantı noktası.  ILB etkin bir Ao, bu bağlantı noktası Ao 'nun ıLB adresine bağlıdır.
* 443: Bir App Service Ortamı App Service planlarında çalışan uygulamalara gelen SSL trafiği için varsayılan bağlantı noktası.  ILB etkin bir Ao, bu bağlantı noktası Ao 'nun ıLB adresine bağlıdır.
* 21  FTP için denetim kanalı.  FTP kullanılmıyorsa bu bağlantı noktası güvenle engellenebilir.  ILB özellikli bir AO 'da, bu bağlantı noktası bir AO için ıLB adresine bağlanabilir.
* 990:  FTPS için denetim kanalı.  Bu bağlantı noktası, FTPS kullanılmıyorsa güvenle engellenebilir.  ILB özellikli bir AO 'da, bu bağlantı noktası bir AO için ıLB adresine bağlanabilir.
* 10001-10020: FTP için veri kanalları.  Denetim kanalında olduğu gibi, FTP kullanılmıyorsa bu bağlantı noktaları güvenle engellenebilir.  ILB özellikli bir AO 'da, bu bağlantı noktası ATıCı 'in ıLB adresine bağlanabilir.
* 4016: Visual Studio 2012 ile uzaktan hata ayıklama için kullanılır.  Bu bağlantı noktası, özellik kullanılmıyorsa güvenle engellenebilir.  ILB etkin bir Ao, bu bağlantı noktası Ao 'nun ıLB adresine bağlıdır.
* 4018: Visual Studio 2013 ile uzaktan hata ayıklama için kullanılır.  Bu bağlantı noktası, özellik kullanılmıyorsa güvenle engellenebilir.  ILB etkin bir Ao, bu bağlantı noktası Ao 'nun ıLB adresine bağlıdır.
* 4020: Visual Studio 2015 ile uzaktan hata ayıklama için kullanılır.  Bu bağlantı noktası, özellik kullanılmıyorsa güvenle engellenebilir.  ILB etkin bir Ao, bu bağlantı noktası Ao 'nun ıLB adresine bağlıdır.

## <a name="outbound-connectivity-and-dns-requirements"></a>Giden bağlantı ve DNS gereksinimleri
App Service Ortamı düzgün bir şekilde çalışması için, ayrıca çeşitli uç noktalara giden erişim gerekir. AX tarafından kullanılan dış uç noktaların tam listesi [ExpressRoute Için ağ yapılandırması](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) makalesinin "gerekli ağ bağlantısı" bölümünde bulunur.

App Service ortamları, sanal ağ için yapılandırılmış geçerli bir DNS altyapısı gerektirir.  Bir App Service Ortamı oluşturulduktan sonra DNS yapılandırması değiştirilirse, geliştiriciler bir App Service Ortamı yeni DNS yapılandırmasını çekmeye zorlayabilir.  [Azure portal][NewPortal] App Service ortamı yönetim dikey penceresinin en üstünde yer alan "yeniden Başlat" simgesi kullanılarak sıralı bir ortamı yeniden başlatmanın tetiklenmesi, ORTAMıN yeni DNS yapılandırmasını başlatmasına neden olur.

Ayrıca, VNET üzerindeki özel DNS sunucularının, bir App Service Ortamı oluşturmadan önce bir süre önce kurulumunu da öneririz.  Bir App Service Ortamı oluşturulurken bir sanal ağın DNS yapılandırması değiştirilirse, bu, App Service Ortamı oluşturma işleminin başarısız olmasına neden olur.  Benzer bir Vein içinde, bir VPN ağ geçidinin diğer ucunda özel bir DNS sunucusu varsa ve DNS sunucusu ulaşılamaz durumdaysa veya kullanılamıyorsa, App Service Ortamı oluşturma işlemi de başarısız olur.

## <a name="creating-a-network-security-group"></a>Ağ güvenlik grubu oluşturma
Ağ güvenlik gruplarının nasıl çalıştığı hakkında tam Ayrıntılar için aşağıdaki [bilgileri][NetworkSecurityGroups]görün.  Aşağıdaki Azure hizmet yönetimi örneği, bir ağ güvenlik grubunu yapılandırma ve bir App Service Ortamı içeren bir alt ağa uygulamayla ilgili bir odak içeren ağ güvenlik gruplarının vurgulanması üzerine dokunmalıdır.

**Not:** Ağ güvenlik grupları, [Azure portalı](https://portal.azure.com) kullanılarak veya Azure PowerShell üzerinden grafiksel olarak yapılandırılabilir.

Ağ güvenlik grupları öncelikle bir abonelikle ilişkili bir tek başına varlık olarak oluşturulur. Ağ güvenlik grupları bir Azure bölgesinde oluşturulduğundan, ağ güvenlik grubunun App Service Ortamı aynı bölgede oluşturulduğundan emin olun.

Aşağıda bir ağ güvenlik grubu oluşturulması gösterilmektedir:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Bir ağ güvenlik grubu oluşturulduktan sonra, bir veya daha fazla ağ güvenlik kuralı eklenir.  Kuralların zaman içinde değişebilmesi için, zaman içinde ek kurallar eklemeyi kolaylaştıran kural öncelikleri için kullanılan numaralandırma düzeninin üzerinde yer açmak önerilir.

Aşağıdaki örnekte, App Service Ortamı yönetilmesi ve bakımını yapmak için Azure altyapısının gerek duyduğu yönetim bağlantı noktalarına açıkça erişim veren bir kural gösterilmektedir.  Tüm yönetim trafiğinin SSL üzerinden akar ve istemci sertifikaları tarafından güvenli hale getirildiğine ve bu nedenle, bağlantı noktalarının açık olmasına rağmen, Azure Yönetim alt yapısı dışında herhangi bir varlık tarafından erişilemez durumda olduğundan emin olmanız gerekir.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


80 ve 443 numaralı bağlantı noktasına erişimi kilitleyerek yukarı akış cihazlarının veya hizmetlerinin arkasındaki bir App Service Ortamı "gizlemek" için yukarı akış IP adresini bilmeniz gerekecektir.  Örneğin, bir Web uygulaması güvenlik duvarı (WAF) kullanıyorsanız WAF, trafiği bir aşağı akış App Service Ortamı ara sunucusunu kullanarak kendi IP adresine (veya adreslerine) sahip olur.  Bir ağ güvenlik kuralının *Sourceaddresspredüzeltmesini* PARAMETRESINDE bu IP adresini kullanmanız gerekir.

Aşağıdaki örnekte, belirli bir yukarı akış IP adresinden gelen trafiğe açıkça izin verilir.  *1.2.3.4* adresi, bir yukarı akış WAF IP adresi için yer tutucu olarak kullanılır.  Değeri, yukarı akış cihazınız veya hizmetiniz tarafından kullanılan adresle eşleşecek şekilde değiştirin.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

FTP desteği isteniyorsa, FTP denetim bağlantı noktası ve veri kanalı bağlantı noktalarına erişim vermek için şablon olarak aşağıdaki kurallar kullanılabilir.  FTP, durum bilgisi olmayan bir protokol olduğundan, FTP trafiğini geleneksel bir HTTP/HTTPS güvenlik duvarı veya proxy cihazından yönlendirmeyebilirsiniz.  Bu durumda, *Sourceaddresspredüzeltmesini* farklı bir değere ayarlamanız gerekir. ÖRNEĞIN, FTP istemcilerinin üzerinde çalıştığı geliştirici veya DAĞıTıM makinelerinin IP adresi aralığı. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Note:** önizleme döneminde veri kanalı bağlantı noktası aralığı değişebilir.)

Visual Studio ile uzaktan hata ayıklama kullanılıyorsa aşağıdaki kurallar nasıl erişim izni verildiğini gösterir.  Her sürüm uzaktan hata ayıklama için farklı bir bağlantı noktası kullandığından, Visual Studio 'nun desteklenen her sürümü için ayrı bir kural vardır.  FTP erişiminde olduğu gibi, uzaktan hata ayıklama trafiği geleneksel bir WAF veya proxy cihazından düzgün şekilde akamayabilir.  *Sourceaddresspredüzeltmesini* , Visual Studio çalıştıran GELIŞTIRICI makinelerinin IP adresi aralığına ayarlanabilir.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Bir alt ağa ağ güvenlik grubu atama
Bir ağ güvenlik grubunun tüm dış trafiğe erişimi reddeden bir varsayılan güvenlik kuralı vardır.  Yukarıda açıklanan ağ güvenlik kurallarını birleştirmenin sonucu ve gelen trafiği engelleyen Varsayılan güvenlik kuralı, yalnızca bir *Izin verme* eylemiyle ilişkili kaynak adres aralıklarından gelen trafiğin, bir üzerinde çalışan uygulamalara trafik gönderebilmesini sağlar. App Service Ortamı.

Bir ağ güvenlik grubu güvenlik kuralları ile doldurulduktan sonra, App Service Ortamı içeren alt ağa atanması gerekir.  Atama komutu, hem App Service Ortamı bulunduğu sanal ağın adına hem de App Service Ortamı oluşturulduğu alt ağın adına başvurur.  

Aşağıdaki örnekte, bir alt ağa ve sanal ağa atanmış bir ağ güvenlik grubu gösterilmektedir:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Ağ güvenlik grubu ataması başarılı olduktan sonra (atama uzun süre çalışan bir işlemdir ve tamamlanması birkaç dakika sürebilir), yalnızca *Izin verme* kuralları ile eşleşen gelen trafik App Service ortamı uygulamalara başarılı bir şekilde ulaşacaktır.

Tamamlana için aşağıdaki örnekte, ağ güvenlik grubunun alt ağdan nasıl kaldırılacağı ve bundan sonra dış ilişkilendirme yapılacağı gösterilmektedir:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Açık IP-SSL için özel konular
Bir uygulama açık bir IP-SSL adresiyle yapılandırıldıysa ( *yalnızca* genel VIP 'Si olan ASE 'ler için geçerlidir), App SERVICE ORTAMı varsayılan IP adresini kullanmak yerine, hem http hem de HTTPS trafiği, farklı bir bağlantı noktası kümesi üzerinden alt ağa akar 80 ve 443 bağlantı noktaları dışında.

Her IP SSL adresi tarafından kullanılan her bir bağlantı noktası çifti, App Service Ortamı Ayrıntılar UX dikey penceresinden Portal Kullanıcı arabiriminde bulunabilir.  "Tüm ayarlar" ' ı seçin--"IP adresleri" >.  "IP adresleri" dikey penceresinde, her IP SSL adresiyle ilişkili HTTP ve HTTPS trafiğini yönlendirmek için kullanılan özel bağlantı noktası çiftiyle birlikte App Service Ortamı için açıkça yapılandırılmış tüm IP-SSL adreslerinin bir tablosu gösterilmektedir.  Bir ağ güvenlik grubundaki kuralları yapılandırırken DestinationPortRange parametreleri için kullanılması gereken bu bağlantı noktası çiftidir.

Ao üzerindeki bir uygulama IP-SSL kullanmak üzere yapılandırıldığında, dış müşteriler özel bağlantı noktası çifti eşleme konusunda endişelenmenize gerek kalmaz.  Uygulamalara giden trafik normal olarak yapılandırılan IP-SSL adresine akacaktır.  Özel bağlantı noktası çiftinin çevirisi, trafiği Ao 'un bulunduğu alt ağa yönlendirmesinin son BAI sırasında otomatik olarak gerçekleşir. 

## <a name="getting-started"></a>Başlarken
App Service ortamlarıyla çalışmaya başlamak için bkz. [giriş App Service ortamı][IntroToAppServiceEnvironment]

Bir App Service Ortamı arka uç kaynağına güvenli bir şekilde bağlanan uygulamalar hakkındaki ayrıntılar için bkz. [bir App Service ortamı arka uç kaynaklarına güvenli bir şekilde bağlanma][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

