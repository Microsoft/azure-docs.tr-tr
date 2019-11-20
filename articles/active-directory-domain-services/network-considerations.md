---
title: Azure AD Domain Services için ağ planlama ve bağlantılar | Microsoft Docs
description: Azure Active Directory Domain Services çalıştırdığınızda bağlantı için kullanılan bazı sanal ağ tasarımı konuları ve kaynakları hakkında bilgi edinin.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: iainfou
ms.openlocfilehash: 325b9e8edc997e41e48e11b3ee752bc38d7dc4a1
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024019"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Azure AD Domain Services için sanal ağ tasarımı konuları ve yapılandırma seçenekleri

Azure Active Directory Domain Services (AD DS), diğer uygulamalara ve iş yüklerine kimlik doğrulama ve yönetim hizmetleri sağladığından, ağ bağlantısı önemli bir bileşendir. Uygun şekilde yapılandırılmış sanal ağ kaynakları, uygulamalar ve iş yükleri ile iletişim kuramaz ve Azure AD DS tarafından sağlanan özellikleri kullanabilir. Sanal ağınızı doğru planlamanız durumunda Azure AD DS 'nin uygulamalarınıza ve iş yüklerinize gerektiği gibi hizmeti verebildiğinizden emin olun.

Bu makalede, Azure AD DS 'yi destekleyen bir Azure sanal ağı için tasarım konuları ve gereksinimleri özetlenmektedir.

## <a name="azure-virtual-network-design"></a>Azure sanal ağ tasarımı

Ağ bağlantısı sağlamak ve uygulamaların ve hizmetlerin Azure AD DS karşı kimlik doğrulamasına izin vermek için bir Azure sanal ağı ve alt ağı kullanın. İdeal olarak, Azure AD DS kendi sanal ağına dağıtılmalıdır. Yönetim VM 'nizi veya hafif uygulama iş yüklerinizi barındırmak için aynı sanal ağa ayrı bir uygulama alt ağını dahil edebilirsiniz. Azure AD DS sanal ağı 'nda bulunan daha büyük veya karmaşık uygulama iş yükleri için ayrı bir sanal ağ, genellikle en uygun tasarımdır. Diğer tasarım seçimleri geçerli olduğundan, sanal ağ ve alt ağ için aşağıdaki bölümlerde özetlenen gereksinimleri karşıladınız.

Azure AD DS sanal ağını tasarlarken aşağıdaki noktalar geçerlidir:

* Azure AD DS, sanal ağınızla aynı Azure bölgesine dağıtılmalıdır.
    * Şu anda Azure AD kiracısı başına yalnızca bir Azure AD DS yönetilen etki alanı dağıtabilirsiniz. Azure AD DS yönetilen etki alanı tek bir bölgeye dağıtılır. [Azure AD DS destekleyen bir bölgede](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)bir sanal ağ oluşturun veya seçtiğinizden emin olun.
* Diğer Azure bölgelerinin ve uygulama iş yüklerinizi barındıran sanal ağların yakınlığını göz önünde bulundurun.
    * Gecikme süresini en aza indirmek için, çekirdek uygulamalarınızı Azure AD DS yönetilen etki alanınız için sanal ağ alt ağı ile aynı bölgede veya ' a yakın tutun. Azure sanal ağları arasında sanal ağ eşlemesi veya sanal özel ağ (VPN) bağlantıları kullanabilirsiniz.
* Sanal ağ, Azure AD DS tarafından sağlananlardan farklı DNS hizmetlerinden yararlanmaz.
    * Azure AD DS kendi DNS hizmetini sağlar. Sanal ağın bu DNS hizmeti adreslerini kullanacak şekilde yapılandırılması gerekir. Ek ad alanları için ad çözümlemesi, koşullu ileticiler kullanılarak gerçekleştirilebilir.
    * Diğer DNS sunucularından gelen sorguları VM 'Ler dahil olmak üzere yönlendirmek için özel DNS sunucusu ayarlarını kullanamazsınız. Sanal ağdaki kaynakların Azure AD DS tarafından sunulan DNS hizmetini kullanması gerekir.

> [!IMPORTANT]
> Hizmeti etkinleştirdikten sonra Azure AD DS farklı bir sanal ağa taşıyamazsınız.

Azure AD DS yönetilen etki alanı, bir Azure sanal ağındaki bir alt ağa bağlanır. Bu alt ağı Azure AD DS için aşağıdaki noktalara göre tasarlayın:

* Azure AD DS kendi alt ağında dağıtılmalıdır. Mevcut bir alt ağı veya ağ geçidi alt ağını kullanmayın.
* Azure AD DS yönetilen bir etki alanının dağıtımı sırasında bir ağ güvenlik grubu oluşturulur. Bu ağ güvenlik grubu, doğru hizmet iletişimi için gerekli kuralları içerir.
    * Kendi özel kurallarınız ile mevcut bir ağ güvenlik grubu oluşturmayın veya kullanmayın.
* Azure AD DS, 3-5 IP adresi gerektirir. Alt ağ IP adresi aralığınızı bu sayıda adres sağlayasağlayadığınızdan emin olun.
    * Kullanılabilir IP adreslerini kısıtlamak, Azure AD Domain Services iki etki alanı denetleyicisinin çalışmasını engelleyebilir.

Aşağıdaki örnek diyagramda, Azure AD DS kendi alt ağına sahip olan geçerli bir tasarım özetlenmektedir, dış bağlantı için bir ağ geçidi alt ağı vardır ve uygulama iş yükleri sanal ağ içindeki bağlı bir alt ağda bulunur:

![Önerilen alt ağ tasarımı](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Azure AD DS sanal ağına bağlantılar

Önceki bölümde belirtildiği gibi, Azure 'daki tek bir sanal ağda yalnızca bir Azure AD Domain Services yönetilen etki alanı oluşturabilir ve Azure AD kiracısı başına yalnızca bir yönetilen etki alanı oluşturulabilir. Bu mimariye bağlı olarak, uygulama iş yüklerinizi barındıran bir veya daha fazla sanal ağı Azure AD DS sanal ağınıza bağlamanız gerekebilir.

Aşağıdaki yöntemlerden birini kullanarak, diğer Azure sanal ağlarında barındırılan uygulama iş yüklerini bağlayabilirsiniz:

* Sanal ağ eşlemesi
* Sanal özel ağ (VPN)

### <a name="virtual-network-peering"></a>Sanal ağ eşlemesi

Sanal ağ eşlemesi, Azure omurga ağı aracılığıyla aynı bölgedeki iki sanal ağı birbirine bağlayan bir mekanizmadır. Küresel sanal ağ eşlemesi, Azure bölgeleri arasında sanal ağa bağlanabilir. Eşlendikten sonra iki sanal ağ, VM 'Ler gibi kaynakların, özel IP adresleri kullanarak birbirleriyle doğrudan iletişim kurmasına olanak tanır. Sanal ağ eşlemesi kullanmak, diğer sanal ağlarda dağıtılan uygulama iş yükleriyle Azure AD DS yönetilen bir etki alanı dağıtmanızı sağlar.

![Eşleme kullanarak sanal ağ bağlantısı](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Daha fazla bilgi için bkz. [Azure sanal ağ eşlemesi genel bakış](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking"></a>Sanal özel ağ

Bir sanal ağı başka bir sanal ağa (VNet-VNet), bir sanal ağı şirket içi site konumuna yapılandırabileceğiniz şekilde bağlayabilirsiniz. Her iki bağlantı da IPSec/ıKE kullanarak güvenli bir tünel oluşturmak için bir VPN ağ geçidi kullanır. Bu bağlantı modeli Azure AD DS Azure sanal ağına dağıtmanıza ve ardından Şirket içi konumlara veya diğer bulutlara bağlanmanızı sağlar.

![VPN Gateway kullanarak sanal ağ bağlantısı](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Sanal özel ağ kullanma hakkında daha fazla bilgi için, [Azure Portal kullanarak VNET-VNET VPN Ağ Geçidi bağlantısı yapılandırma](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)makalesini okuyun.

## <a name="name-resolution-when-connecting-virtual-networks"></a>Sanal ağları bağlarken ad çözümlemesi

Azure AD Domain Services sanal ağa bağlı sanal ağların genellikle kendi DNS ayarları vardır. Sanal ağları bağladığınızda, Azure AD DS yönetilen etki alanı tarafından sunulan hizmetleri çözümlemek üzere bağlanılan sanal ağ için ad çözümlemesini otomatik olarak yapılandırmaz. Bağlanan sanal ağlarda ad çözümlemesi, uygulama iş yüklerinin Azure AD Domain Services bulmasını sağlamak için yapılandırılmalıdır.

DNS sunucusunda, bağlanan sanal ağları destekleyen ve Azure AD etki alanı hizmeti sanal ağı 'ndan aynı DNS IP adreslerini kullanarak ad çözümlemesini etkinleştirebilirsiniz.

## <a name="network-resources-used-by-azure-ad-ds"></a>Azure AD DS tarafından kullanılan ağ kaynakları

Azure AD DS yönetilen bir etki alanı, dağıtım sırasında bazı ağ kaynakları oluşturur. Bu kaynaklar, Azure AD DS yönetilen etki alanının başarılı bir şekilde çalışması ve yönetimi için gereklidir ve el ile yapılandırılmamalıdır.

| Azure kaynağı                          | Açıklama |
|:----------------------------------------|:---|
| Ağ arabirim kartı                  | Azure AD DS, Windows Server 'da Azure sanal makineleri olarak çalışan iki etki alanı denetleyicisinde (DC) yönetilen etki alanını barındırır. Her VM 'nin sanal ağ alt ağınıza bağlanan bir sanal ağ arabirimi vardır. |
| Dinamik standart genel IP adresi         | Azure AD DS, standart SKU genel IP adresini kullanarak eşitleme ve yönetim hizmetiyle iletişim kurar. Genel IP adresleri hakkında daha fazla bilgi için bkz. [Azure 'Da IP adresi türleri ve ayırma yöntemleri](../virtual-network/virtual-network-ip-addresses-overview-arm.md). |
| Azure Standart yük dengeleyici               | Azure AD DS, ağ adresi çevirisi (NAT) ve Yük Dengeleme (Güvenli LDAP ile kullanıldığında) için standart bir SKU yük dengeleyici kullanır. Azure yük dengeleyiciler hakkında daha fazla bilgi için bkz. [Azure Load Balancer nedir?](../load-balancer/load-balancer-overview.md) |
| Ağ adresi çevirisi (NAT) kuralları | Azure AD DS, yük dengeleyici üzerinde üç NAT kuralı oluşturup, güvenli HTTP trafiği için bir kural ve güvenli PowerShell uzaktan iletişim için iki kural kullanır. |
| Yük dengeleyici kuralları                     | Azure AD DS yönetilen etki alanı, TCP bağlantı noktası 636 üzerinde güvenli LDAP için yapılandırıldığında, trafiği dağıtmak için bir yük dengeleyicide üç kural oluşturulur ve kullanılır. |

> [!WARNING]
> Azure AD DS tarafından oluşturulan ağ kaynağını silmeyin. Herhangi bir ağ kaynağını silerseniz bir Azure AD DS hizmet kesintisi meydana gelir.

## <a name="network-security-groups-and-required-ports"></a>Ağ güvenlik grupları ve gerekli bağlantı noktaları

Bir [ağ güvenlik grubu (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) , bir Azure sanal ağındaki trafiğe ağ trafiğine izin veren veya reddeden kuralların listesini içerir. Azure AD DS dağıtırken, hizmetin kimlik doğrulama ve yönetim işlevleri sağlamasına izin veren bir kurallar kümesi içeren bir ağ güvenlik grubu oluşturulur. Bu varsayılan ağ güvenlik grubu, Azure AD DS yönetilen etki alanının dağıtıldığı sanal ağ alt ağı ile ilişkilendirilir.

Azure AD DS kimlik doğrulaması ve yönetim hizmetleri sağlamak için aşağıdaki ağ güvenlik grubu kuralları gereklidir. Azure AD DS yönetilen etki alanının dağıtıldığı sanal ağ alt ağı için bu ağ güvenlik grubu kurallarını düzenlemeyin veya silmeyin.

| Bağlantı noktası numarası | Protokol | Kaynak                             | Hedef | Eylem | Gereklidir | Amaç |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Herhangi biri         | Allow  | Yes      | Azure AD kiracınızla eşitleme. |
| 3389        | TCP      | Corpnetgördünüz                         | Herhangi biri         | Allow  | Yes      | Etki alanınızı yönetme. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Herhangi biri         | Allow  | Yes      | Etki alanınızı yönetme. |
| 636         | TCP      | Herhangi biri                                | Herhangi biri         | Allow  | Hayır       | Yalnızca Güvenli LDAP (LDAPS) yapılandırdığınızda etkinleştirilir. |

> [!WARNING]
> Bu ağ kaynaklarını ve konfigürasyonları el ile düzenlemeyin. Yanlış yapılandırılmış bir ağ güvenlik grubunu veya Kullanıcı tanımlı bir yol tablosunu Azure AD DS 'nin dağıtıldığı alt ağla ilişkilendirdiğinizde, Microsoft 'un etki alanını hizmet etme ve yönetme yeteneğini kesintiye uğratabilir. Azure AD kiracınız ile Azure AD DS yönetilen etki alanınız arasında eşitleme de bozulur.
>
> Ağ güvenlik grubu için *Allowvnetınbound*, *AllowAzureLoadBalancerInBound*, *ınyallinbound*, *allowvnetoutbound*, *Allowınternetoutbound*ve *denyalloutbound* varsayılan kuralları da mevcuttur. Bu varsayılan kuralları düzenlemeyin veya silmeyin.
>
> Azure SLA, etki alanınızı güncelleştirme ve yönetme konusunda Azure AD DS engelleyen, yanlış yapılandırılmış bir ağ güvenlik grubu ve/veya Kullanıcı tanımlı yol tablolarının uygulandığı dağıtımlar için uygulanmaz.

### <a name="port-443---synchronization-with-azure-ad"></a>Bağlantı noktası 443-Azure AD ile eşitleme

* Azure AD kiracınızı Azure AD DS yönetilen etki alanınız ile senkronize etmek için kullanılır.
* Bu bağlantı noktasına erişim olmadan Azure AD DS yönetilen etki alanınız Azure AD kiracınızla eşitlenemiyor. Kullanıcılar parolalarında değişiklik yapamayabilir ve Azure AD DS yönetilen etki alanınız ile eşitlenmez.
* Bu bağlantı noktasına IP adreslerine gelen erişim, **AzureActiveDirectoryDomainServices** Service etiketi kullanılarak varsayılan olarak kısıtlıdır.
* Bu bağlantı noktasından giden erişimi kısıtlamayın.

### <a name="port-3389---management-using-remote-desktop"></a>Bağlantı noktası 3389-Uzak Masaüstü kullanarak yönetim

* Azure AD DS yönetilen etki alanında etki alanı denetleyicilerine Uzak Masaüstü bağlantıları için kullanılır.
* Varsayılan ağ güvenlik grubu kuralı, trafiği kısıtlamak için *Corpnetgördünüz* hizmet etiketini kullanır.
    * Bu hizmet etiketi, yalnızca Microsoft Kurumsal ağındaki güvenli erişim iş istasyonlarının, uzak masaüstünü Azure AD DS tarafından yönetilen etki alanına kullanmasına izin verir.
    * Erişime yalnızca, yönetim veya sorun giderme senaryoları gibi iş gerekçimiyle izin verilir.
* Bu kural *Reddet*olarak ayarlanabilir ve yalnızca gerektiğinde *izin ver* olarak ayarlanabilir. Çoğu yönetim ve izleme görevi, PowerShell uzaktan iletişim kullanılarak gerçekleştirilir. RDP yalnızca Microsoft 'un, gelişmiş sorun giderme için yönetilen etki alanına uzaktan bağlanması gereken nadir bir olayda kullanılır.

> [!NOTE]
> Bu ağ güvenlik grubu kuralını düzenlemeye çalışırsanız portaldan *Corpnetgördünüz* hizmet etiketini el ile seçemezsiniz. *Corpnetgördünüz* hizmet etiketini kullanan bir kuralı el ile yapılandırmak için Azure PowerShell veya Azure CLI kullanmanız gerekir.

### <a name="port-5986---management-using-powershell-remoting"></a>Bağlantı noktası 5986-PowerShell uzaktan iletişimini kullanan yönetim

* Azure AD DS yönetilen etki alanında PowerShell uzaktan iletişimini kullanarak yönetim görevlerini gerçekleştirmek için kullanılır.
* Bu bağlantı noktasına erişim olmadan Azure AD DS yönetilen etki alanınız güncelleştirilemiyor, yapılandırılamaz, yedeklenmez veya izlenemez.
* Kaynak Yöneticisi tabanlı bir sanal ağ kullanan Azure AD DS yönetilen etki alanları için, bu bağlantı noktasına gelen erişimi *AzureActiveDirectoryDomainServices* Service etiketiyle kısıtlayabilirsiniz.
    * Klasik tabanlı bir sanal ağ kullanan eski Azure AD DS yönetilen etki alanları için, bu bağlantı noktasına gelen erişimi şu kaynak IP adreslerine kısıtlayabilirsiniz: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223* , *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*ve *104.40.87.209*.

## <a name="user-defined-routes"></a>Kullanıcı tanımlı yollar

Kullanıcı tanımlı yollar varsayılan olarak oluşturulmaz ve Azure AD DS 'nin düzgün çalışması için gerekli değildir. Yol tabloları kullanmanız gerekiyorsa *0.0.0.0* yolunda herhangi bir değişiklik yapmaktan kaçının. Bu rotadaki değişiklikler Azure AD Domain Services kesintiye uğratabilir.

Ayrıca, ilgili Azure hizmet etiketlerine dahil edilen IP adreslerinden gelen trafiği Azure AD Domain Services alt ağına yönlendirmelidir. Hizmet etiketleri ve ilgili IP adresleri hakkında daha fazla bilgi için bkz. [Azure IP aralıkları ve hizmet etiketleri-genel bulut](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Bu Azure veri merkezi IP aralıkları bildirimde bulunmaksızın değiştirilebilir. En son IP adreslerine sahip olduğunuzdan emin olmak için işlemlere sahip olduğunuzdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD DS tarafından kullanılan bazı ağ kaynakları ve bağlantı seçenekleri hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure sanal ağ eşlemesi](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN ağ geçitleri](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure ağ güvenlik grupları](../virtual-network/security-overview.md)

<!-- INTERNAL LINKS -->

<!-- EXTERNAL LINKS -->
