---
title: Sanal ağ eşleme sorunlarını giderme
description: Birçok sanal ağ eşleme sorununu çözmeye yardımcı olacak adımlar.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 608f3d72aecb0c6d53325c0d082b1779822e9c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200524"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Sanal ağ eşleme sorunlarını giderme

Bu sorun giderme kılavuzu, çoğu [sanal ağ eşleme](virtual-network-peering-overview.md) sorununu çözmenize yardımcı olacak adımları sağlar.

![Sanal ağ eşlemesi diyagramı](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>İki sanal ağ arasında sanal ağ eşlemesi yapılandırma

Sanal ağlar aynı abonelikte veya farklı aboneliklerde mı?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>Sanal ağlar aynı abonelikte

Aynı abonelikte bulunan sanal ağların sanal ağ eşlemesini yapılandırmak için aşağıdaki makalelerdeki yöntemleri kullanın:

* Sanal ağlar *aynı bölgedeyse*, bkz. [eşleme oluşturma](./virtual-network-manage-peering.md#create-a-peering).
* Sanal ağlar *farklı bölgelerde* ise, bkz. [sanal ağ eşlemesi](./virtual-network-peering-overview.md). 

> [!Note]
> Bağlantı, aşağıdaki kaynaklar için genel sanal ağ eşlemesi üzerinden çalışmaz: 
>
> * Temel iç yük dengeleyici (ıLB) SKU 'SU arkasındaki sanal makineler (VM)
> * Redsıs önbelleği (temel ıLB SKU 'SU kullanır)
> * Application Gateway v1 (temel ıLB SKU 'SU kullanır)
> * Sanal Makine Ölçek Kümeleri (temel ıLB SKU 'SU kullanır)
> * Azure Service Fabric kümeleri (temel ıLB SKU 'SU kullanır)
> * SQL Server her zaman açık (temel ıLB SKU 'SU kullanır)
> * PowerApps için Azure App Service Ortamı (temel ıLB SKU 'SU kullanır)
> * Azure API Management (temel ıLB SKU 'SU kullanır)
> * Azure Active Directory Domain Services (Azure AD DS) (temel ıLB SKU 'SU kullanır)

Daha fazla bilgi için bkz. genel eşleme [gereksinimleri ve kısıtlamaları](./virtual-network-peering-overview.md#requirements-and-constraints) .

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>Sanal ağlar farklı aboneliklerde veya Active Directory kiracılardayken

Farklı aboneliklerdeki veya Active Directory kiracılardaki sanal ağların sanal ağ eşlemesini yapılandırmak için bkz. [Azure CLI için farklı aboneliklerde eşleme oluşturma](./create-peering-different-subscriptions.md#cli).

> [!Note]
> Ağ eşlemesini yapılandırmak için her iki abonelik için de **ağ katılımcısı** izinlerinizin olması gerekir. Daha fazla bilgi için bkz. [eşleme izinleri](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Şirket içi kaynakları kullanan hub-ışınsal topolojisi ile sanal ağ eşlemesini yapılandırma

![Şirket içi bağlı bileşen ile sanal ağ eşlemesi diyagramı](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Siteden siteye bağlantı veya ExpressRoute bağlantısı için

İçindeki adımları izleyin: [sanal ağ eşlemesi IÇIN VPN ağ geçidi geçişi yapılandırma](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="for-point-to-site-connections"></a>Noktadan siteye bağlantılar için

1. İçindeki adımları izleyin: [sanal ağ eşlemesi IÇIN VPN ağ geçidi geçişi yapılandırma](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Sanal ağ eşlemesi kurulduktan veya değiştirildikten sonra, Noktadan siteye istemcilerinin, bağlı olan sanal ağa güncelleştirilmiş yolları almasını sağlamak için Noktadan siteye paketini indirip yeniden yükleyin.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Hub-bağlı bileşen topolojisi sanal ağı ile sanal ağ eşlemesini yapılandırma

![Sanal ağ eşağıyla sanal ağ eşlemesinin diyagramı](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>Sanal ağlar aynı bölgedeyse


1. Hub sanal ağında bir ağ sanal gereci (NVA) yapılandırın.
1. Bağlı olan sanal ağlarda, sonraki atlama türü "ağ sanal gereci" uygulanmış Kullanıcı tanımlı yollara sahip olacak.

Daha fazla bilgi için bkz. [hizmet zincirleme](./virtual-network-peering-overview.md#service-chaining).

> [!Note]
> Bir NVA ayarlamak için yardıma ihtiyacınız varsa [NVA satıcısına başvurun](https://mskb.pkisolutions.com/kb/2984655).

NVA cihaz kurulumu ve yönlendirme sorunlarını gidermeye yönelik yardım için bkz. [Azure 'Da ağ sanal gereç sorunları](./virtual-network-troubleshoot-nva.md).

### <a name="the-virtual-networks-are-in-different-regions"></a>Sanal ağlar farklı bölgelerde

Genel sanal ağ eşlemesi üzerinden aktarım artık desteklenmektedir. Bağlantı, aşağıdaki kaynaklar için genel sanal ağ eşlemesi üzerinden çalışmaz:

* Temel ILB SKU arkasındaki VM’ler
* Redsıs önbelleği (temel ıLB SKU 'SU kullanır)
* Application Gateway (temel ıLB SKU 'SU kullanır)
* Ölçek Kümeleri (temel ıLB SKU 'SU kullanır)
* Service Fabric kümeleri (Temel ILB SKU kullanır)
* SQL Server her zaman açık (temel ıLB SKU 'SU kullanır)
* App Service Ortamı (temel ıLB SKU 'SU kullanır)
* API Management (Temel ILB SKU kullanır)
* Azure AD DS (temel ıLB SKU 'SU kullanır)

Genel eşleme gereksinimleri ve depoları hakkında daha fazla bilgi edinmek için bkz. [sanal ağ eşlemesi](./virtual-network-peering-overview.md#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Eşlenen iki sanal ağ arasında bağlantı sorununu giderme

[Azure Portal](https://portal.azure.com/) , gerekli [Roller ve izinlere](virtual-network-manage-peering.md#permissions)sahip bir hesapla oturum açın. Sanal ağı seçin, **eşleme**' yi seçin ve ardından **durum** alanını denetleyin. Durum nedir?

### <a name="the-peering-status-is-connected"></a>Eşleme durumu "bağlı"

Bu sorunu gidermek için:

1. Ağ trafiği akışlarını denetleyin:

   Trafik akışlarında girişim hatasına neden olan bir NSG veya UDR olup olmadığını öğrenmek için [bağlantı sorunlarını giderme](../network-watcher/network-watcher-connectivity-overview.md) ve kaynak VM 'den hedef sanal makineye [IP akışı doğrulama](../network-watcher/network-watcher-ip-flow-verify-overview.md) ' yı kullanın.

   Bir güvenlik duvarı veya NVA kullanıyorsanız: 
   1. Bu adım tamamlandıktan sonra dosyaları geri yükleyebilmek için UDR parametrelerini belgeleyin.
   2. Bir sonraki atlama olarak NVA 'ya işaret eden kaynak VM alt ağından veya NIC 'den UDR 'yi kaldırın. Kaynak VM 'den, NVA 'yi atlayarak hedefe doğrudan bağlantıyı doğrulayın. Bu adım işe yaramazsa, [NVA sorun gidericisine](./virtual-network-troubleshoot-nva.md)bakın.

2. Ağ izlemesi yapın: 
   1. Hedef VM 'de bir ağ izlemesi başlatın. Windows için **netsh**' i kullanabilirsiniz. Linux için **TCPDump** kullanın.
   2. Kaynaktan hedef IP 'ye **Tcing veya Psping** çalıştırın. 

      Bu, bir **tcma** komutunun örneğidir: `tcping64.exe -t <destination VM address> 3389`

   3. **Tcme** işlemi tamamlandıktan sonra, hedefteki ağ izlemesini durdurun.
   4. Paketler kaynaktan ulaştığında, ağ sorunu yoktur. Yapılandırma sorununun yerini bulmak için hem VM Güvenlik duvarını hem de bu bağlantı noktasını dinleyen uygulamayı inceleyin.

   > [!Note]
   > Genel sanal ağ eşlemesi (farklı bölgelerdeki sanal ağlar) üzerinden aşağıdaki kaynak türlerine bağlanamazsınız:
   >
   > * Temel ILB SKU arkasındaki VM’ler
   > * Redsıs önbelleği (temel ıLB SKU 'SU kullanır)
   > * Application Gateway (temel ıLB SKU 'SU kullanır)
   > * Ölçek Kümeleri (temel ıLB SKU 'SU kullanır)
   > * Service Fabric kümeleri (Temel ILB SKU kullanır)
   > * SQL Server her zaman açık (temel ıLB SKU 'SU kullanır)
   > * App Service Ortamı (temel ıLB SKU 'SU kullanır)
   > * API Management (Temel ILB SKU kullanır)
   > * Azure AD DS (temel ıLB SKU 'SU kullanır)

Daha fazla bilgi için bkz. genel eşleme [gereksinimleri ve kısıtlamaları](./virtual-network-peering-overview.md#requirements-and-constraints) .

### <a name="the-peering-status-is-disconnected"></a>Eşleme durumu "bağlantısı kesildi"

Bu sorunu çözmek için, her iki sanal ağdan eşlemeyi silin ve sonra yeniden oluşturun.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Hub-bağlı sanal ağ ile şirket içi kaynak arasında bağlantı sorununu giderme

Ağınız bir üçüncü taraf NVA veya VPN Gateway kullanıyor mu?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Ağımdaki bir üçüncü taraf NVA veya VPN ağ geçidi kullanılıyor

Üçüncü taraf bir NVA veya VPN ağ geçidini etkileyen bağlantı sorunlarını gidermek için aşağıdaki makalelere bakın:

* [NVA sorun giderici](./virtual-network-troubleshoot-nva.md)
* [Hizmet zinciri](./virtual-network-peering-overview.md#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>Ağımdaki bir üçüncü taraf NVA veya VPN ağ geçidi kullanılmaz

Hub sanal ağı ve bağlı ağ sanal ağı bir VPN ağ geçidine sahip mi?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Hub sanal ağı ve bağlı bileşen sanal ağı bir VPN ağ geçidine sahiptir

Uzak ağ geçidi kullanılması desteklenmez.

Bağlı olan sanal ağda zaten bir VPN ağ geçidi varsa, **uzak ağ geçidi kullan** seçeneği, bağlı olan sanal ağ üzerinde desteklenmez. Bunun nedeni, sanal ağ eşleme sınırlamasıdır.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Hub sanal ağı ve bağlı bileşen sanal ağının VPN Gateway 'i yok

Siteden siteye veya Azure ExpressRoute bağlantılarında, şirket içi olarak uzak sanal ağa bağlantı sorunlarının şu birincil nedenlerini denetleyin:

* Ağ geçidine sahip olan sanal ağda, **iletilen trafiğe Izin ver** onay kutusunun işaretli olduğunu doğrulayın.
* Ağ geçidine sahip olmayan sanal ağda, **uzak ağ geçidini kullan** onay kutusunun işaretli olduğunu doğrulayın.
* Ağ yöneticinizin, tüm uzak sanal ağ adresi alanı eklenmiş olduklarını doğrulamak için şirket içi cihazlarınızı denetlemesini sağlayabilirsiniz.

Noktadan siteye bağlantılar için:

* Ağ geçidine sahip olan sanal ağda, **iletilen trafiğe Izin ver** onay kutusunun işaretli olduğunu doğrulayın.
* Ağ geçidine sahip olmayan sanal ağda, **uzak ağ geçidini kullan** onay kutusunun işaretli olduğunu doğrulayın.
* Noktadan siteye istemci paketini indirip yeniden yükleyin. Yeni eşlenen sanal ağ yolları Noktadan siteye istemcilere otomatik olarak yollar eklemez.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Aynı bölgedeki bağlı olan sanal ağlar arasında hub-bağlı ağ bağlantısı sorununu giderme

Hub ağı bir NVA içermelidir. Bir sonraki atlama olarak ayarlanan bir NVA 'ya sahip olan ve hub sanal ağında **iletilen trafiğe Izin ver** ' i etkinleştirerek udrs 'yi yapılandırın.

Daha fazla bilgi için bkz. [hizmet zincirleme](./virtual-network-peering-overview.md#service-chaining)ve bu gereksinimleri tercih ettiğiniz [NVA satıcısı](https://mskb.pkisolutions.com/kb/2984655) ile tartışın.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Farklı bölgelerde bağlı olan sanal ağlar arasında hub-bağlı ağ bağlantısı sorununu giderme

Genel sanal ağ eşlemesi üzerinden aktarım artık desteklenmektedir. Bağlantı, aşağıdaki kaynaklar için genel sanal ağ eşlemesi üzerinden çalışmaz:

* Temel ILB SKU arkasındaki VM’ler
* Redsıs önbelleği (temel ıLB SKU 'SU kullanır)
* Application Gateway (temel ıLB SKU 'SU kullanır)
* Ölçek Kümeleri (temel ıLB SKU 'SU kullanır)
* Service Fabric kümeleri (Temel ILB SKU kullanır)
* SQL Server her zaman açık (temel ıLB SKU 'SU kullanır)
* App Service Ortamı (temel ıLB SKU 'SU kullanır)
* API Management (Temel ILB SKU kullanır)
* Azure AD DS (temel ıLB SKU 'SU kullanır)

Daha fazla bilgi için bkz. genel eşleme ve [farklı VPN topolojileri](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2) [gereksinimleri ve kısıtlamaları](./virtual-network-peering-overview.md#requirements-and-constraints) .

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Bir Web uygulaması ve bağlı bileşen sanal ağı arasındaki hub-bağlı ağ bağlantısı sorununu giderme

Bu sorunu gidermek için:

1. Azure portalında oturum açın. 
1. Web uygulamasında **ağ**' ı seçin ve ardından **VNET tümleştirmesi**' ni seçin.
1. Uzak sanal ağı görüp görmeyeceğinizi denetleyin. Uzak sanal ağ adres alanını el ile girin (**ağa eşitleyin** ve **yollar ekleyin**).

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

* [Uygulamanızı bir Azure sanal ağı ile tümleştirme](../app-service/web-sites-integrate-with-vnet.md)
* [Noktadan Siteye VPN yönlendirme hakkında](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Sanal ağ eşleme yapılandırmasında sorun giderme hata iletisi 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Geçerli kiracının `<TENANT ID>` bağlantılı aboneliğe erişme yetkisi yok

Bu sorunu çözmek için bkz. [eşleme oluşturma-Azure CLI](./create-peering-different-subscriptions.md#cli).

### <a name="not-connected"></a>Bağlı değil

Bu sorunu çözmek için, her iki sanal ağdan eşlemeyi silin ve sonra yeniden oluşturun.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Databricks sanal ağının eşi yapılamadı

Bu sorunu çözmek için **Azure Databricks** altında sanal ağ eşlemesini yapılandırın ve ardından **kaynak kimliği**' ni kullanarak hedef sanal ağı belirtin. Daha fazla bilgi için bkz. [peer a Databricks sanal ağını uzak bir sanal ağa](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-peering#id2).

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>Uzak sanal ağda ağ geçidi eksik

Bu sorun, farklı kiracılardan sanal ağları eşler ve daha sonra yapılandırmak istediğinizde oluşur `Use Remote Gateways` . Azure portal sınırlaması, başka bir kiracının sanal ağındaki bir sanal ağ geçidinin varlığını doğrulayamasıdır.

Sorunu çözmek için iki yol vardır:

 * Eşlemeleri silin ve `Use Remote Gateways` Yeni bir eşleme oluşturduğunuzda seçeneği etkinleştirin.
 * Etkinleştirmek için Azure portal yerine PowerShell veya CLı kullanın `Use Remote Gateways` .

## <a name="next-steps"></a>Sonraki adımlar

* [Azure VM’leri arasında bağlantı sorunlarını giderme](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)
