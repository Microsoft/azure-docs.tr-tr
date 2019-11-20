---
title: 'Azure ExpressRoute: bağlantıyı doğrulama-sorun giderme kılavuzu'
description: Bu sayfa, bir ExpressRoute devresine yönelik uçtan uca bağlantıyı gidermeye ve doğrulamaya ilişkin yönergeler sağlar.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 10/31/2019
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 833e7788a5b17b12ad883f705d56f660c2f1d832
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033783"
---
# <a name="verifying-expressroute-connectivity"></a>ExpressRoute bağlantısını doğrulama
Bu makale, ExpressRoute bağlantısını doğrulamanıza ve sorunlarını gidermenize yardımcı olur. ExpressRoute, bir bağlantı sağlayıcısı tarafından yaygın olarak kullanılan özel bir bağlantı üzerinden şirket içi bir ağı Microsoft bulutuna genişletir. ExpressRoute bağlantısı, geleneksel olarak üç farklı ağ bölgesi içerir ve aşağıdaki gibi:

-   Müşteri ağı
-   Sağlayıcı ağı
-   Microsoft veri merkezi

> [!NOTE]
> ExpressRoute doğrudan bağlantı modelinde (10/100 GB/sn 'lik bant genişliğine sunulmaktadır), bu, Microsoft Enterprise Edge (MSEE) yönlendiricileri bağlantı noktasına doğrudan bağlanabilir. Bu nedenle, doğrudan bağlantı modelinde yalnızca müşteri ve Microsoft ağ alanları vardır.
>


Bu belgenin amacı, kullanıcının bir bağlantı sorunu olup olmadığını ve nerede olduğunu belirlemesine yardımcı olur. Bu nedenle, bir sorunu çözmek için uygun takımdan destek sağlanmasına yardımcı olma. Bir sorunu çözmek için Microsoft desteği gerekiyorsa [Microsoft desteği][Support]bir destek bileti açın.

> [!IMPORTANT]
> Bu belge basit sorunları tanılamaya ve düzeltmeye yardımcı olmaya yöneliktir. Microsoft desteği için bir değişiklik olması amaçlanmamıştır. Belirtilen Kılavuzu kullanarak sorunu çözemediğiniz bir destek bileti [Microsoft desteği][Support] açın.
>
>

## <a name="overview"></a>Genel Bakış
Aşağıdaki diyagramda, ExpressRoute kullanarak bir müşteri ağının Microsoft Network 'e mantıksal bağlantısı gösterilmektedir.
[![1]][1]

Yukarıdaki diyagramda, numaralar anahtar ağ noktalarını gösterir. Bu ağ noktalarına, bu makalede ilişkili oldukları numara ile başvurulur. ExpressRoute bağlantı modeline bağlı olarak, bulut Exchange ortak konumu, noktadan noktaya Ethernet bağlantısı veya herhangi bir (ıPVPN) arasında, ağ noktaları 3 ve 4 (katman 2 cihazları) veya yönlendiriciler (katman 3 cihazları) olabilir. Doğrudan bağlantı modelinde, 3 ve 4 ağ noktaları yoktur; Bunun yerine, (2), koyu bir fiber aracılığıyla MSEE 'e doğrudan bağlanır. Gösterilen anahtar ağ noktaları aşağıdaki gibidir:

1.  Müşteri işlem cihazı (örneğin, bir sunucu veya BILGISAYAR)
2.  CEs: müşteri Edge yönlendiricileri 
3.  PEs (CE 'ye yönelik): sağlayıcının Edge yönlendiricileri/ilgili müşteri Edge yönlendiricileriyle anahtarlar. Bu belgede PE-CEs adı verilir.
4.  PEs (MSEE 'e bakan): sağlayıcıya yönelik olan sağlayıcı Edge yönlendiricileri/anahtarları. Bu belgede PE-Mas olarak adlandırılır.
5.  MSEE: Microsoft Enterprise Edge (MSEE) ExpressRoute yönlendiricileri
6.  Sanal ağ (VNet) ağ geçidi
7.  Azure VNet 'te işlem cihazı

Bulut değişim ortak konumu, noktadan noktaya Ethernet veya doğrudan bağlantı modelleri kullanılırsa, CEs (2) MSEE (5) ile BGP eşlemesi oluşturun. 

Any-any (ıPVPN) bağlantı modeli kullanılırsa, PE-MSEE (4) MSEE (5) ile BGP eşlemesi oluşturun. PE-MSEE, Microsoft 'tan alınan yolları ıPVPN hizmet sağlayıcısı ağı aracılığıyla müşteri ağına yayar.

> [!NOTE]
>Microsoft, yüksek kullanılabilirlik için MSEE (5) ve PE-MSEE (4) çiftleri arasında tam olarak yedekli bir paralel bağlantı kurar. Müşteri ağı ile PE-CEs çifti arasında tam olarak yedekli bir paralel ağ yolu da önerilir. Yüksek kullanılabilirlik hakkında daha fazla bilgi için bkz. [ExpressRoute ile yüksek kullanılabilirlik Için tasarlama][HA] makalesi
>
>

Aşağıda, ExpressRoute bağlantı hattının giderilmesi için mantıksal adımlar verilmiştir:

* [Devre sağlamayı ve durumu doğrulama](#verify-circuit-provisioning-and-state)
  
* [Eşleme yapılandırmasını doğrula](#validate-peering-configuration)
  
* [ARP doğrulama](#validate-arp)
  
* [MSEE BGP ve rotalar doğrulama](#validate-bgp-and-routes-on-the-msee)
  
* [Trafik akışını onaylama](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Devre sağlamayı ve durumu doğrulama
ExpressRoute devresini sağlamak, CEs/PE-MSEE (2)/(4) ve MSEE (5) arasında yedekli katman 2 bağlantıları kurar. ExpressRoute bağlantı hattı oluşturma, değiştirme, sağlama ve doğrulama hakkında daha fazla bilgi için, [bir ExpressRoute bağlantı hattı oluşturma ve değiştirme][CreateCircuit]makalesine bakın.

>[!TIP]
>Bir hizmet anahtarı, bir ExpressRoute devresini benzersiz bir şekilde tanımlar. ExpressRoute sorununu gidermek için Microsoft 'tan veya bir ExpressRoute ortağından yardım almanız gerekiyorsa, devreyi kolay bir şekilde tanımlamak için hizmet anahtarını sağlayın.
>
>

### <a name="verification-via-the-azure-portal"></a>Azure portal aracılığıyla doğrulama
Azure portal ExpressRoute devre dikey penceresini açın. Dikey pencerenin ![3][3] bölümünde ExpressRoute Essentials aşağıdaki ekran görüntüsünde gösterildiği gibi listelenir:

![4][4]    

ExpressRoute Essentials 'da, *devre durumu* Microsoft tarafında devre 'nın durumunu gösterir. *Sağlayıcı durumu* , devre dışı bırakıldığını/hizmet sağlayıcı tarafında *sağlanıp sağlanmadığını* gösterir. 

Bir ExpressRoute bağlantı hattının işlemsel olması için *devre durumunun* *etkinleştirilmesi* ve *sağlayıcı durumunun* *sağlanması*gerekir.

> [!NOTE]
> Bir ExpressRoute devresini yapılandırdıktan sonra, *devre durumu* etkin değil durumunda olduğunda [Microsoft desteği][Support]başvurun. Öte yandan, *sağlayıcının durumu* sağlanmamış durumunda değilse hizmet sağlayıcınıza başvurun.
>
>

### <a name="verification-via-powershell"></a>PowerShell aracılığıyla doğrulama
Bir kaynak grubundaki tüm ExpressRoute devrelerini listelemek için aşağıdaki komutu kullanın:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Bir kaynak grubunun adını arıyorsanız, aboneliğinizdeki tüm kaynak gruplarını, *Get-AzResourceGroup* komutunu kullanarak bu alana alabilirsiniz.
>


Bir kaynak grubunda belirli bir ExpressRoute devresini seçmek için aşağıdaki komutu kullanın:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Örnek yanıt:

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

Bir ExpressRoute bağlantı hattının çalışır durumda olup olmadığını doğrulamak için, aşağıdaki alanlara özellikle dikkat edin:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Bir ExpressRoute devresini yapılandırdıktan sonra, *devre durumu* etkin değil durumunda olduğunda [Microsoft desteği][Support]başvurun. Öte yandan, *sağlayıcının durumu* sağlanmamış durumunda değilse hizmet sağlayıcınıza başvurun.
>
>

## <a name="validate-peering-configuration"></a>Eşleme yapılandırmasını doğrula
Hizmet sağlayıcı ExpressRoute bağlantı hattını sağlamayı tamamladıktan sonra, CEs/MSEE-PEs (2)/(4) ve MSEE (5) arasındaki ExpressRoute bağlantı hattı üzerinden birden çok eBGP tabanlı Yönlendirme yapılandırması oluşturulabilir. Her ExpressRoute bağlantı hattı şu olabilir: Azure özel eşlemesi (Azure 'da özel sanal ağlara trafik) ve/veya Microsoft eşlemesi (PaaS ve SaaS 'nin Genel uç noktalarına giden trafik). Yönlendirme yapılandırması oluşturma ve değiştirme hakkında daha fazla bilgi için, [bir ExpressRoute bağlantı hattı için yönlendirme oluşturma ve değiştirme][CreatePeering]makalesine bakın.

### <a name="verification-via-the-azure-portal"></a>Azure portal aracılığıyla doğrulama

> [!NOTE]
> IPVPN bağlantı modelinde, hizmet sağlayıcılar, eşlemeleri yapılandırma sorumluluğunu (katman 3 Hizmetleri) işler. Böyle bir modelde, hizmet sağlayıcı bir eşleme yapılandırdıktan sonra ve portalda eşleme boşsa, portalda Yenile düğmesini kullanarak devre yapılandırmasını yenilemeyi deneyin. Bu işlem, geçerli yönlendirme yapılandırmasını devreye alacak. 
>

Azure portal, ExpressRoute bağlantı hattı eşlemesinin durumu ExpressRoute bağlantı hattı dikey penceresinde denetlenebilir. Dikey pencerenin ![3][3] bölümünde ExpressRoute eşayarları aşağıdaki ekran görüntüsünde gösterildiği gibi listelenir:

![5][5]

Yukarıdaki örnekte, belirtilen Azure özel eşlemesi sağlandığı için Azure genel ve Microsoft eşlemeleri sağlanmamıştır. Başarılı bir şekilde sağlanmış eşleme bağlamı, listelenen birincil ve ikincil noktadan noktaya alt ağları da içermelidir. /30 alt ağları, Mgördüğü ve CEs/PE-MSEE Arabirim IP adresi için kullanılır. Sağlanan eşlemeler için, liste yapılandırmayı en son kimin değiştirdiğini de belirtir. 

> [!NOTE]
> Bir eşlemenin etkinleştirilmesi başarısız olursa, atanan birincil ve ikincil alt ağların bağlı CE/PE-MSEE yapılandırmasıyla eşleşip eşleşmediği kontrol edin. Ayrıca, MSEE doğru *Vlanıd*, *Azureasn*ve *peerasn* 'nın kullanıldığını ve bu değerlerin bağlı CE/PE-MSEE ile eşlenenlere eşlendiğini kontrol edin. MD5 karma seçilirse, paylaşılan anahtar MSEE ve PE-MSEE/CE çifti ile aynı olmalıdır. Önceden yapılandırılmış paylaşılan anahtar, güvenlik nedenleriyle gösterilmeyecek. Bir MSEE yönlendiricisinde bu yapılandırmanın herhangi birini değiştirmeniz gerekir, [bir ExpressRoute bağlantı hattı için yönlendirme oluşturma ve değiştirme][CreatePeering]bölümüne bakın.  
>

> [!NOTE]
> Arabirim için atanan bir/30 alt ağda, Microsoft MSEE arabirimi için alt ağın kullanılabilir ikinci IP adresini seçer. Bu nedenle, alt ağın ilk kullanılabilir IP adresinin eşlenen CE/PE-MSEE atanmış olduğundan emin olun.
>


### <a name="verification-via-powershell"></a>PowerShell aracılığıyla doğrulama
Azure özel eşleme yapılandırma ayrıntılarını almak için aşağıdaki komutları kullanın:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Başarılı bir şekilde yapılandırılmış özel eşleme için örnek yanıt:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : 123##
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 200
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Başarıyla etkinleştirilen bir eşleme bağlamı, birincil ve ikincil adres ön eklerinin listelenmesine neden olur. /30 alt ağları, Mgördüğü ve CEs/PE-MSEE Arabirim IP adresi için kullanılır.

Azure genel eşleme yapılandırma ayrıntılarını almak için aşağıdaki komutları kullanın:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Microsoft eşleme yapılandırma ayrıntılarını almak için aşağıdaki komutları kullanın:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Bir eşleme yapılandırılmamışsa, bir hata iletisi olur. Bir örnek yanıt, belirtilen eşleme (Bu örnekteki Azure ortak eşlemesi) devre içinde yapılandırılmadı:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Bir eşlemenin etkinleştirilmesi başarısız olursa, atanan birincil ve ikincil alt ağların bağlı CE/PE-MSEE yapılandırmasıyla eşleşip eşleşmediği kontrol edin. Ayrıca, MSEE doğru *Vlanıd*, *Azureasn*ve *peerasn* 'nın kullanıldığını ve bu değerlerin bağlı CE/PE-MSEE ile eşlenenlere eşlendiğini kontrol edin. MD5 karma seçilirse, paylaşılan anahtar MSEE ve PE-MSEE/CE çifti ile aynı olmalıdır. Önceden yapılandırılmış paylaşılan anahtar, güvenlik nedenleriyle gösterilmeyecek. Bir MSEE yönlendiricisinde bu yapılandırmanın herhangi birini değiştirmeniz gerekir, [bir ExpressRoute bağlantı hattı için yönlendirme oluşturma ve değiştirme][CreatePeering]bölümüne bakın.  
>
>

> [!NOTE]
> Arabirim için atanan bir/30 alt ağda, Microsoft MSEE arabirimi için alt ağın kullanılabilir ikinci IP adresini seçer. Bu nedenle, alt ağın ilk kullanılabilir IP adresinin eşlenen CE/PE-MSEE atanmış olduğundan emin olun.
>

## <a name="validate-arp"></a>ARP doğrulama

ARP tablosu, belirli bir eşleme için IP adresi ve MAC adresi eşleştirmesi sağlar. Bir ExpressRoute bağlantı hattı eşlemesi için ARP tablosu, her arabirim (birincil ve ikincil) için aşağıdaki bilgileri sağlar:
* Şirket içi yönlendirici arabirimi IP adresini MAC adresine eşleme
* ExpressRoute yönlendirici arabirimi IP adresini MAC adresine eşleme
* Eşleme ARP tablolarının yaşı katman 2 yapılandırmasını doğrulamaya ve temel katman 2 bağlantı sorunlarını gidermenize yardımcı olabilir.


Bkz. [Kaynak Yöneticisi dağıtım modeli BELGESINDE ARP tablolarını alma][ARP] , bir EXPRESSROUTE eşlemesinin ARP tablosunu görüntüleme ve bu bilgilerin katman 2 bağlantı sorununu gidermek için nasıl kullanılacağı.


## <a name="validate-bgp-and-routes-on-the-msee"></a>MSEE BGP ve rotalar doğrulama

*Özel* yönlendirme bağlamı için *birincil* yolda MSEE yönlendirme tablosunu almak için aşağıdaki komutu kullanın:

    Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****

Örnek yanıt:

    Network : 10.1.0.0/16
    NextHop : 10.17.17.141
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.1.0.0/16
    NextHop : 10.17.17.140*
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.2.20.0/25
    NextHop : 172.16.0.1
    LocPrf  : 
    Weight  : 0
    Path    : 123##


> [!NOTE]
> Bir MSEE ve bir CE/PE-MSEE arasındaki eBGP eşlemesinin durumu etkin veya boşta ise, birincil ve ikincil eş alt ağlarının bağlı CE/PE-MSEE yapılandırmasıyla eşleşip eşleşmediğinden emin olun. Ayrıca, MSEE doğru *Vlanıd*, *Azureasn*ve *peerasn* 'nın kullanıldığını ve bu değerlerin bağlı PE-MSEE/CE üzerinde kullanılanlara eşlendiğini kontrol edin. MD5 karma seçilirse, paylaşılan anahtar MSEE ve CE/PE-MSEE çiftiyle aynı olmalıdır. Bir MSEE yönlendiricisinde bu yapılandırmanın herhangi birini değiştirmeniz gerekir, [bir ExpressRoute bağlantı hattı için yönlendirme oluşturma ve değiştirme][CreatePeering]bölümüne bakın.
>


> [!NOTE]
> Belirli hedeflere bir eşleme üzerinden ulaşılamıyorsa, karşılık gelen eşleme bağlamı için Mgördüğü yol tablosunu kontrol edin. Yönlendirme tablosunda eşleşen bir ön ek (IP olabilir) varsa, trafiği engelleyen yolda güvenlik duvarları/NSG/ACL 'ler olup olmadığını kontrol edin.
>


Aşağıdaki örnek, varolmayan bir eşleme için komutun yanıtını gösterir:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="confirm-the-traffic-flow"></a>Trafik akışını onaylama
Birleşik birincil ve ikincil yol trafiği istatistiklerini almak için, bir eşleme bağlamının içindeki ve çıkan baytlar, aşağıdaki komutu kullanın:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Komutun örnek çıktısı şu şekilde olur:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Mevcut olmayan bir eşleme için komutun örnek çıktısı:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="next-steps"></a>Sonraki Adımlar
Daha fazla bilgi veya Yardım için aşağıdaki bağlantıları kontrol edin:

- [Microsoft Desteği][Support]
- [ExpressRoute bağlantı hattı oluşturma ve değiştirme][CreateCircuit]
- [ExpressRoute devresi için yönlendirme oluşturma ve değiştirme][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "mantıksal Express yol bağlantısı"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Tüm kaynaklar simgesi"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Genel Bakış simgesi"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "ExpressRoute Essentials örnek ekran görüntüsü"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "ExpressRoute Essentials örnek ekran görüntüsü"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[DR-Pvt]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering





