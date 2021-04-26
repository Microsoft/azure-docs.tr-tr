---
title: Azure AD Domain Services için ağ planlama ve bağlantılar | Microsoft Docs
description: Azure Active Directory Domain Services çalıştırdığınızda bağlantı için kullanılan bazı sanal ağ tasarımı konuları ve kaynakları hakkında bilgi edinin.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: justinha
ms.openlocfilehash: d1a3ab5face03754bf84f442ac0fa73768b0fc80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97615827"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services için sanal ağ tasarımı konuları ve yapılandırma seçenekleri

Azure Active Directory Domain Services (Azure AD DS), diğer uygulamalara ve iş yüklerine kimlik doğrulama ve yönetim hizmetleri sağlar. Ağ bağlantısı, önemli bir bileşendir. Doğru şekilde yapılandırılmış sanal ağ kaynakları, uygulamalar ve iş yükleri ile iletişim kuramaz ve Azure AD DS tarafından sunulan özelliklerle birlikte kullanamaz. Azure AD DS 'nin uygulamalarınıza ve iş yüklerinize gerektiği şekilde ulaşacağına emin olmak için sanal ağ gereksinimlerinizi planlayın.

Bu makalede, Azure AD DS 'yi destekleyecek bir Azure sanal ağının tasarım konuları ve gereksinimleri özetlenmektedir.

## <a name="azure-virtual-network-design"></a>Azure sanal ağ tasarımı

Ağ bağlantısı sağlamak ve uygulamaların ve hizmetlerin Azure AD DS yönetilen bir etki alanında kimlik doğrulamasına izin vermek için bir Azure sanal ağı ve alt ağı kullanın. İdeal olarak, yönetilen etki alanının kendi sanal ağına dağıtılması gerekir.

Yönetim VM 'nizi veya hafif uygulama iş yüklerinizi barındırmak için aynı sanal ağa ayrı bir uygulama alt ağını dahil edebilirsiniz. Azure AD DS sanal ağı 'nda bulunan daha büyük veya karmaşık uygulama iş yükleri için ayrı bir sanal ağ, genellikle en uygun tasarımdır.

Diğer tasarım seçimleri geçerli olduğundan, sanal ağ ve alt ağ için aşağıdaki bölümlerde özetlenen gereksinimleri karşıladınız.

Azure AD DS sanal ağını tasarlarken aşağıdaki noktalar geçerlidir:

* Azure AD DS, sanal ağınızla aynı Azure bölgesine dağıtılmalıdır.
    * Şu anda, her Azure AD kiracısı için yalnızca bir yönetilen etki alanı dağıtabilirsiniz. Yönetilen etki alanı tek bir bölgeye dağıtılır. [Azure AD DS destekleyen bir bölgede](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)bir sanal ağ oluşturun veya seçtiğinizden emin olun.
* Diğer Azure bölgelerinin ve uygulama iş yüklerinizi barındıran sanal ağların yakınlığını göz önünde bulundurun.
    * Gecikme süresini en aza indirmek için, temel uygulamalarınızı, yönetilen etki alanınız için sanal ağ alt ağı ile aynı bölgede veya ' ye yakın tutun. Azure sanal ağları arasında sanal ağ eşlemesi veya sanal özel ağ (VPN) bağlantıları kullanabilirsiniz. Bu bağlantı seçenekleri aşağıdaki bölümde ele alınmıştır.
* Sanal ağ, yönetilen etki alanı tarafından sunulan hizmetlerden farklı DNS hizmetlerinden yararlanmaz.
    * Azure AD DS kendi DNS hizmetini sağlar. Sanal ağın bu DNS hizmeti adreslerini kullanacak şekilde yapılandırılması gerekir. Ek ad alanları için ad çözümlemesi, koşullu ileticiler kullanılarak gerçekleştirilebilir.
    * Diğer DNS sunucularından gelen sorguları VM 'Ler dahil olmak üzere yönlendirmek için özel DNS sunucusu ayarlarını kullanamazsınız. Sanal ağdaki kaynakların, yönetilen etki alanı tarafından sağlanmış DNS hizmetini kullanması gerekir.

> [!IMPORTANT]
> Hizmeti etkinleştirdikten sonra Azure AD DS farklı bir sanal ağa taşıyamazsınız.

Yönetilen bir etki alanı, Azure sanal ağındaki bir alt ağa bağlanır. Bu alt ağı Azure AD DS için aşağıdaki noktalara göre tasarlayın:

* Yönetilen bir etki alanının kendi alt ağında dağıtılması gerekir. Mevcut bir alt ağı veya ağ geçidi alt ağını kullanmayın.
* Yönetilen bir etki alanının dağıtımı sırasında bir ağ güvenlik grubu oluşturulur. Bu ağ güvenlik grubu, doğru hizmet iletişimi için gerekli kuralları içerir.
    * Kendi özel kurallarınız ile mevcut bir ağ güvenlik grubu oluşturmayın veya kullanmayın.
* Yönetilen etki alanı 3-5 IP adresi gerektirir. Alt ağ IP adresi aralığınızı bu sayıda adres sağlayasağlayadığınızdan emin olun.
    * Kullanılabilir IP adreslerini kısıtlamak, yönetilen etki alanının iki etki alanı denetleyicisini kullanmasını engelleyebilir.

Aşağıdaki örnek diyagramda, yönetilen etki alanının kendi alt ağına sahip olduğu geçerli bir tasarım özetlenmektedir, dış bağlantı için bir ağ geçidi alt ağı vardır ve uygulama iş yükleri sanal ağ içindeki bağlı bir alt ağda bulunur:

![Önerilen alt ağ tasarımı](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Azure AD DS sanal ağına bağlantılar

Önceki bölümde belirtildiği gibi, Azure 'daki tek bir sanal ağda yalnızca yönetilen bir etki alanı oluşturabilir ve Azure AD kiracısı başına yalnızca bir yönetilen etki alanı oluşturulabilir. Bu mimariye bağlı olarak, uygulama iş yüklerinizi barındıran bir veya daha fazla sanal ağa, yönetilen etki alanının sanal ağına bağlamanız gerekebilir.

Aşağıdaki yöntemlerden birini kullanarak, diğer Azure sanal ağlarında barındırılan uygulama iş yüklerini bağlayabilirsiniz:

* Sanal ağ eşleme
* Sanal özel ağ (VPN)

### <a name="virtual-network-peering"></a>Sanal ağ eşleme

Sanal ağ eşlemesi, Azure omurga ağı aracılığıyla aynı bölgedeki iki sanal ağı birbirine bağlayan bir mekanizmadır. Küresel sanal ağ eşlemesi, Azure bölgeleri arasında sanal ağa bağlanabilir. Eşlendikten sonra iki sanal ağ, VM 'Ler gibi kaynakların, özel IP adresleri kullanarak birbirleriyle doğrudan iletişim kurmasına olanak tanır. Sanal ağ eşlemesinin kullanılması, diğer sanal ağlarda dağıtılan uygulama iş yükleriyle yönetilen bir etki alanı dağıtmanızı sağlar.

![Eşleme kullanarak sanal ağ bağlantısı](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Daha fazla bilgi için bkz. [Azure sanal ağ eşlemesi genel bakış](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking-vpn"></a>Sanal özel ağ (VPN)

Bir sanal ağı başka bir sanal ağa (VNet-VNet), bir sanal ağı şirket içi site konumuna yapılandırabileceğiniz şekilde bağlayabilirsiniz. Her iki bağlantı da IPSec/ıKE kullanarak güvenli bir tünel oluşturmak için bir VPN ağ geçidi kullanır. Bu bağlantı modeli, yönetilen etki alanını bir Azure sanal ağına dağıtmanıza sonra şirket içi konumlara veya diğer bulutlara bağlanmanızı sağlar.

![VPN Gateway kullanarak sanal ağ bağlantısı](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Sanal özel ağ kullanma hakkında daha fazla bilgi için, [Azure Portal kullanarak VNET-VNET VPN Ağ Geçidi bağlantısı yapılandırma](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)makalesini okuyun.

## <a name="name-resolution-when-connecting-virtual-networks"></a>Sanal ağları bağlarken ad çözümlemesi

Yönetilen etki alanının sanal ağına bağlı sanal ağların genellikle kendi DNS ayarları vardır. Sanal ağları bağladığınızda, yönetilen etki alanı tarafından sunulan hizmetleri çözümlemek üzere bağlanılan sanal ağ için ad çözümlemesini otomatik olarak yapılandırmaz. Bağlanan sanal ağlarda ad çözümlemesi, uygulama iş yüklerinin yönetilen etki alanını bulmasını sağlamak için yapılandırılmalıdır.

Sanal ağları bağlayan DNS sunucusunda koşullu DNS ileticileri veya yönetilen etki alanının sanal ağından aynı DNS IP adreslerini kullanarak ad çözümlemesini etkinleştirebilirsiniz.

## <a name="network-resources-used-by-azure-ad-ds"></a>Azure AD DS tarafından kullanılan ağ kaynakları

Yönetilen bir etki alanı, dağıtım sırasında bazı ağ kaynakları oluşturur. Bu kaynaklar, yönetilen etki alanının başarılı bir şekilde çalışması ve yönetimi için gereklidir ve el ile yapılandırılmamalıdır.

| Azure kaynağı                          | Description |
|:----------------------------------------|:---|
| Ağ arabirim kartı                  | Azure AD DS, Windows Server 'da Azure sanal makineleri olarak çalışan iki etki alanı denetleyicisinde (DC) yönetilen etki alanını barındırır. Her VM 'nin sanal ağ alt ağınıza bağlanan bir sanal ağ arabirimi vardır. |
| Dinamik standart genel IP adresi      | Azure AD DS, standart SKU genel IP adresini kullanarak eşitleme ve yönetim hizmetiyle iletişim kurar. Genel IP adresleri hakkında daha fazla bilgi için bkz. [Azure 'Da IP adresi türleri ve ayırma yöntemleri](../virtual-network/public-ip-addresses.md). |
| Azure Standart yük dengeleyici            | Azure AD DS, ağ adresi çevirisi (NAT) ve Yük Dengeleme (Güvenli LDAP ile kullanıldığında) için standart bir SKU yük dengeleyici kullanır. Azure yük dengeleyiciler hakkında daha fazla bilgi için bkz. [Azure Load Balancer nedir?](../load-balancer/load-balancer-overview.md) |
| Ağ adresi çevirisi (NAT) kuralları | Azure AD DS, yük dengeleyici üzerinde üç NAT kuralı oluşturup, güvenli HTTP trafiği için bir kural ve güvenli PowerShell uzaktan iletişim için iki kural kullanır. |
| Yük dengeleyici kuralları                     | Yönetilen bir etki alanı, TCP bağlantı noktası 636 üzerinde güvenli LDAP için yapılandırıldığında, trafiği dağıtmak için bir yük dengeleyicide üç kural oluşturulur ve kullanılır. |

> [!WARNING]
> Yük dengeleyiciyi veya kuralları el ile yapılandırma gibi Azure AD DS tarafından oluşturulan ağ kaynağını silmeyin veya değiştirmeyin. Herhangi bir ağ kaynağını siler veya değiştirirseniz, bir Azure AD DS hizmet kesintisi meydana gelebilir.

## <a name="network-security-groups-and-required-ports"></a>Ağ güvenlik grupları ve gerekli bağlantı noktaları

Bir [ağ güvenlik grubu (NSG)](../virtual-network/network-security-groups-overview.md) , bir Azure sanal ağındaki trafiğe ağ trafiğine izin veren veya reddeden kuralların listesini içerir. Hizmetin kimlik doğrulama ve yönetim işlevleri sağlamasına izin veren bir kurallar kümesi içeren bir yönetilen etki alanını dağıtırken bir ağ güvenlik grubu oluşturulur. Bu varsayılan ağ güvenlik grubu, yönetilen etki alanının dağıtıldığı sanal ağ alt ağı ile ilişkilendirilir.

Yönetilen etki alanı için kimlik doğrulama ve yönetim hizmetleri sağlamak üzere aşağıdaki ağ güvenlik grubu kuralları gereklidir. Yönetilen etki alanının dağıtıldığı sanal ağ alt ağı için bu ağ güvenlik grubu kurallarını düzenlemeyin veya silmeyin.

| Bağlantı noktası numarası | Protokol | Kaynak                             | Hedef | Eylem | Gerekli | Amaç |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Herhangi biri         | İzin Ver  | Yes      | Etki alanınızı yönetme. |
| 3389        | TCP      | Corpnetgördünüz                         | Herhangi biri         | İzin Ver  | İsteğe Bağlı      | Destek için hata ayıklama. |

Bu kuralların gerçekleşmesini gerektiren bir Azure Standart yük dengeleyici oluşturulur. Bu ağ güvenlik grubu, Azure AD DS güvenliğini sağlar ve yönetilen etki alanının düzgün çalışması için gereklidir. Bu ağ güvenlik grubunu silmeyin. Yük dengeleyici bu olmadan düzgün çalışmaz.

Gerekirse, [gerekli ağ güvenlik grubunu ve kuralları Azure PowerShell kullanarak oluşturabilirsiniz](powershell-create-instance.md#create-a-network-security-group).

> [!WARNING]
> Bu ağ kaynaklarını ve konfigürasyonları el ile düzenlemeyin. Yanlış yapılandırılmış bir ağ güvenlik grubunu veya Kullanıcı tanımlı bir yol tablosunu yönetilen etki alanının dağıtıldığı alt ağla ilişkilendirdiğinizde, Microsoft 'un etki alanını hizmet etme ve yönetme yeteneğini kesintiye uğratabilir. Azure AD kiracınız ile yönetilen etki alanınız arasında eşitleme de kesintiye uğrar.
>
> Güvenli LDAP kullanıyorsanız, gerekirse dış trafiğe izin vermek için gerekli TCP bağlantı noktası 636 kuralını ekleyebilirsiniz. Bu kuralın eklenmesi, ağ güvenlik grubu kurallarınızı desteklenmeyen bir duruma yerleştirmez. Daha fazla bilgi için bkz [. internet üzerinden GÜVENLI LDAP erişimini kilitleme](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)
>
> Ağ güvenlik grubu için *Allowvnetınbound*, *AllowAzureLoadBalancerInBound*, *ınyallinbound*, *allowvnetoutbound*, *Allowınternetoutbound* ve *denyalloutbound* varsayılan kuralları da mevcuttur. Bu varsayılan kuralları düzenlemeyin veya silmeyin.
>
> Azure SLA, etki alanınızı güncelleştirme ve yönetme konusunda Azure AD DS engelleyen, yanlış yapılandırılmış bir ağ güvenlik grubu ve/veya Kullanıcı tanımlı yol tablolarının uygulandığı dağıtımlar için uygulanmaz.

### <a name="port-5986---management-using-powershell-remoting"></a>Bağlantı noktası 5986-PowerShell uzaktan iletişimini kullanan yönetim

* Yönetilen etki alanında PowerShell uzaktan iletişimini kullanarak yönetim görevlerini gerçekleştirmek için kullanılır.
* Bu bağlantı noktasına erişim olmadan, yönetilen etki alanınız güncelleştirilemiyor, yapılandırılamaz, yedeklenmez veya izlenemez.
* Kaynak Yöneticisi tabanlı bir sanal ağ kullanan yönetilen etki alanları için, bu bağlantı noktasına gelen erişimi *AzureActiveDirectoryDomainServices* hizmeti etiketiyle kısıtlayabilirsiniz.
    * Klasik tabanlı bir sanal ağ kullanan eski yönetilen etki alanları için, bu bağlantı noktasına gelen erişimi şu kaynak IP adreslerine kısıtlayabilirsiniz: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18* ve *104.40.87.209*.

    > [!NOTE]
    > 2017 ' de Azure AD Domain Services Azure Resource Manager ağda barındırana bilgisayar için kullanılabilir duruma geldi. Bu tarihten sonra, Azure Resource Manager modern yeteneklerini kullanarak daha güvenli bir hizmet oluşturuyoruz. Azure Resource Manager dağıtımları klasik dağıtımları tamamen yerine getirmek için Azure AD DS klasik sanal ağ dağıtımları 1 Mart 2023 ' de kullanımdan kaldırılacaktır.
    >
    > Daha fazla bilgi için bkz. [resmi kullanımdan kaldırma bildirimi](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

### <a name="port-3389---management-using-remote-desktop"></a>Bağlantı noktası 3389-Uzak Masaüstü kullanarak yönetim

* Yönetilen etki alanındaki etki alanı denetleyicilerine Uzak Masaüstü bağlantıları için kullanılır.
* Varsayılan ağ güvenlik grubu kuralı, trafiği kısıtlamak için *Corpnetgördünüz* hizmet etiketini kullanır.
    * Bu hizmet etiketi, yalnızca Microsoft Kurumsal ağındaki güvenli erişim iş istasyonlarının, yönetilen etki alanına uzak masaüstü 'nü kullanmasına izin verir.
    * Erişime yalnızca, yönetim veya sorun giderme senaryoları gibi iş gerekçimiyle izin verilir.
* Bu kural *Reddet* olarak ayarlanabilir ve yalnızca gerektiğinde *izin ver* olarak ayarlanabilir. Çoğu yönetim ve izleme görevi, PowerShell uzaktan iletişim kullanılarak gerçekleştirilir. RDP yalnızca Microsoft 'un, gelişmiş sorun giderme için yönetilen etki alanına uzaktan bağlanması gereken nadir bir olayda kullanılır.

> [!NOTE]
> Bu ağ güvenlik grubu kuralını düzenlemeye çalışırsanız portaldan *Corpnetgördünüz* hizmet etiketini el ile seçemezsiniz. *Corpnetgördünüz* hizmet etiketini kullanan bir kuralı el ile yapılandırmak için Azure PowerShell veya Azure CLI kullanmanız gerekir.
>
> Örneğin, RDP 'ye izin veren bir kural oluşturmak için aşağıdaki betiği kullanabilirsiniz: 
>
> `Get-AzureRmNetworkSecurityGroup -Name "nsg-name" -ResourceGroupName "resource-group-name" | Add-AzureRmNetworkSecurityRuleConfig -Name "new-rule-name" -Access "Allow" -Protocol "TCP" -Direction "Inbound" -Priority "priority-number" -SourceAddressPrefix "CorpNetSaw" -SourcePortRange "" -DestinationPortRange "3389" -DestinationAddressPrefix "" | Set-AzureRmNetworkSecurityGroup`

## <a name="user-defined-routes"></a>Kullanıcı tanımlı yollar

Kullanıcı tanımlı yollar varsayılan olarak oluşturulmaz ve Azure AD DS 'nin düzgün çalışması için gerekli değildir. Yol tabloları kullanmanız gerekiyorsa *0.0.0.0* yolunda herhangi bir değişiklik yapmaktan kaçının. Bu rotadaki değişiklikler Azure AD DS kesintiye uğratır ve yönetilen etki alanını desteklenmeyen bir duruma geçirir.

Ayrıca, ilgili Azure hizmet etiketlerine dahil edilen IP adreslerinden gelen trafiği yönetilen etki alanının alt ağına yönlendirmelidir. Hizmet etiketleri ve ilgili IP adresleri hakkında daha fazla bilgi için bkz. [Azure IP aralıkları ve hizmet etiketleri-genel bulut](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Bu Azure veri merkezi IP aralıkları bildirimde bulunmaksızın değiştirilebilir. En son IP adreslerine sahip olduğunuzdan emin olmak için işlemlere sahip olduğunuzdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD DS tarafından kullanılan bazı ağ kaynakları ve bağlantı seçenekleri hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure sanal ağ eşlemesi](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN ağ geçitleri](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure ağ güvenlik grupları](../virtual-network/network-security-groups-overview.md)