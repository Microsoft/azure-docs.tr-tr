---
title: Azure-SSIS Integration Runtime'ı sanal ağa bağlama
description: Azure-SSIS tümleştirme çalışma zamanını bir Azure sanal ağına nasıl katılacağınızı öğrenin.
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/02/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 9a82b305adec1385bf659987ea39df6bb953cd70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370980"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS Integration Runtime'ı sanal ağa bağlama

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory SQL Server Integration Services (SSIS) kullanırken, Azure-SSIS tümleştirme çalışma zamanı 'nı (IR) aşağıdaki senaryolarda bir Azure sanal ağına katılmanız gerekir:

- Proxy olarak şirket içinde barındırılan bir IR yapılandırmadan veya yönetmeden Azure-SSIS IR çalışan SSIS paketlerindeki şirket içi veri depolarına bağlanmak istiyorsunuz. 

- Azure SQL veritabanı 'nda SSIS katalog veritabanını (SSSıSDB) IP güvenlik duvarı kuralları/sanal ağ hizmeti uç noktaları veya SQL yönetilen örneği ile özel uç nokta ile barındırmak istiyorsunuz. 

- Azure-SSIS IR çalışan SSIS paketlerindeki sanal ağ hizmet uç noktaları ile yapılandırılmış Azure kaynaklarına bağlanmak istiyorsunuz.

- Azure-SSIS IR çalışan SSIS paketlerinden IP güvenlik duvarı kurallarıyla yapılandırılmış veri depolarına/kaynaklara bağlanmak istiyorsunuz.

Data Factory, Azure-SSIS IR klasik dağıtım modeli veya Azure Resource Manager dağıtım modeli aracılığıyla oluşturulan bir sanal ağa eklemenize olanak tanır.

> [!IMPORTANT]
> Klasik sanal ağ kullanım dışı olduğundan bunun yerine Azure Resource Manager sanal ağı kullanın.  Klasik sanal ağı zaten kullanıyorsanız, Azure Resource Manager sanal ağa mümkün olan en kısa sürede geçiş yapın.

Bir [sanal ağ öğreticisine katılması Için Azure-SQL Server Integration Services (SSIS) tümleştirme çalışma zamanı (IR) yapılandırma](tutorial-deploy-ssis-virtual-network.md) , Azure Portal üzerinden en düşük adımları gösterir. Bu makale öğreticiye genişleterek tüm isteğe bağlı görevleri açıklar:

- Sanal ağ kullanıyorsanız (klasik).
- Azure-SSIS IR için kendi genel IP adreslerini getir.
- Kendi etki alanı adı sistemi (DNS) sunucunuzu kullanıyorsanız.
- Alt ağda bir ağ güvenlik grubu (NSG) kullanıyorsanız.
- Azure ExpressRoute veya Kullanıcı tanımlı yol (UDR) kullanıyorsanız.
- Özelleştirilmiş Azure-SSIS IR kullanıyorsanız.
- Azure PowerShell sağlama kullanıyorsanız.

## <a name="access-to-on-premises-data-stores"></a>Şirket içi veri depolarına erişim

SSIS 'niz kuruluşunuzda şirket içi veri depolarına erişebiliyse, Azure-SSIS IR şirket içi ağa bağlı bir sanal ağa ekleyebilirsiniz. İsterseniz, Azure-SSIS IR için otomatik olarak barındırılan bir IR yapılandırabilir ve yönetebilirsiniz. Daha fazla bilgi için bkz. [Azure-SSIS IR için otomatik olarak barındırılan BIR IR ara sunucu olarak yapılandırma](./self-hosted-integration-runtime-proxy-ssis.md). 

Azure-SSIS IR bir sanal ağa katılırken şu önemli noktaları hatırlayın: 

- Şirket içi ağınıza bağlı bir sanal ağ yoksa, önce Azure-SSIS IR katılmaları için bir [Azure Resource Manager sanal ağı](../virtual-network/quick-create-portal.md#create-a-virtual-network) oluşturun. Ardından, bu sanal ağdan şirket içi ağınıza bir siteden siteye [VPN Ağ Geçidi bağlantısı](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) veya [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) bağlantısı yapılandırın. 

- Azure Resource Manager bir sanal ağ, Azure-SSIS IR aynı konumda bulunan şirket içi ağınıza zaten bağlıysa, IR 'ye bu sanal ağa katabilirsiniz. 

- Klasik bir sanal ağ, Azure-SSIS IR farklı bir konumda şirket içi ağınıza zaten bağlıysa, Azure-SSIS IR katılması için [Azure Resource Manager bir sanal ağ](../virtual-network/quick-create-portal.md#create-a-virtual-network) oluşturabilirsiniz. Ardından, [Klasik-Azure Resource Manager sanal ağ](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) bağlantısı yapılandırın. 
 
- Azure Resource Manager bir sanal ağ, Azure-SSIS IR farklı bir konumda şirket içi ağınıza zaten bağlıysa, öncelikle Azure-SSIS IR katılması için bir [Azure Resource Manager sanal ağı](../virtual-network/quick-create-portal.md#create-a-virtual-network) oluşturabilirsiniz. Ardından Azure Resource Manager Azure Resource Manager bir sanal ağ bağlantısı yapılandırın. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>SQL veritabanında SSIS kataloğunu barındırma

SSIS kataloğunuzu sanal ağ hizmeti uç noktaları içeren bir Azure SQL veritabanında barındırdıysanız, Azure-SSIS IR aynı sanal ağa ve alt ağa eklediğinizden emin olun.

SSIS kataloğunuza özel uç nokta ile SQL yönetilen örneğinde barındırdığınızda, Azure-SSIS IR aynı sanal ağa, ancak yönetilen örnekten farklı bir alt ağda eklediğinizden emin olun. Azure-SSIS IR SQL yönetilen örneğinden farklı bir sanal ağa katmak için sanal ağ eşlemesinin (aynı bölgeyle sınırlı) veya sanal ağ ile sanal ağ arasında bağlantı öneririz. Daha fazla bilgi için bkz. [uygulamanızı Azure SQL yönetilen örneğine bağlama](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="access-to-azure-services"></a>Azure hizmetlerine erişim

SSIS paketleriniz [sanal ağ hizmet uç noktalarını](../virtual-network/virtual-network-service-endpoints-overview.md) destekleyen Azure kaynaklarına erişebiliyorlarsa ve bu kaynaklara Azure-SSIS IR erişimi güvenli hale getirmek istiyorsanız, Azure-SSIS IR sanal ağ hizmeti uç noktaları için yapılandırılmış bir sanal ağ alt ağına katılabilir ve ardından aynı alt ağdan erişime izin vermek Için ilgili Azure kaynaklarına bir sanal ağ kuralı ekleyebilirsiniz.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>IP güvenlik duvarı kuralıyla korunan veri kaynaklarına erişim

SSIS 'niz, yalnızca belirli statik genel IP adreslerine izin veren veri depolarına/kaynaklara erişir ve bu kaynaklara Azure-SSIS IR erişimi güvenli hale getirmek istiyorsanız, [genel IP adreslerini](../virtual-network/virtual-network-public-ip-address.md) bir sanal ağa katılırken Azure-SSIS IR ile ilişkilendirebilir ve ardından bu IP adreslerinden erişime izin vermek için ilgili kaynaklara bir IP güvenlik kuralı ekleyebilirsiniz. Bunu gerçekleştirmenin iki farklı yolu vardır: 

- Azure-SSIS IR oluşturduğunuzda, kendi genel IP adreslerinizi getirebilir ve bunları [Data Factory Kullanıcı arabirimi veya SDK](#join-the-azure-ssis-ir-to-a-virtual-network)aracılığıyla belirtebilirsiniz. Yalnızca Azure-SSIS IR giden internet bağlantısı, sağlanmış genel IP adreslerinizi kullanır ve alt ağdaki diğer cihazlar bunları kullanmaz.
- Ayrıca, Azure-SSIS IR katılacak alt ağ için [sanal ağ NAT](../virtual-network/nat-overview.md) kurulumunu ve bu alt ağdaki tüm giden bağlantıların BELIRTILEN genel IP adreslerini kullanmasını sağlayabilirsiniz.

Her durumda, sanal ağ yalnızca Azure Resource Manager dağıtım modeliyle dağıtılabilir.

Aşağıdaki bölümlerde daha fazla ayrıntı sağlanmaktadır. 

## <a name="virtual-network-configuration"></a>Sanal ağ yapılandırması

Sanal ağınızı bu gereksinimleri karşılayacak şekilde ayarlayın: 

- ' Nin, `Microsoft.Batch` Azure-SSIS IR barındıran sanal ağ alt ağınızın aboneliği altında kayıtlı bir sağlayıcı olduğundan emin olun. Klasik bir sanal ağ kullanıyorsanız, `MicrosoftAzureBatch` Bu sanal ağ Için klasik sanal makine katılımcısı rolüne de katın. 

- Gerekli izinlere sahip olduğunuzdan emin olun. Daha fazla bilgi için bkz. [Izinleri ayarlama](#perms).

- Azure-SSIS IR barındırmak için uygun alt ağı seçin. Daha fazla bilgi için bkz. [alt ağı seçme](#subnet). 

- Azure-SSIS IR için kendi genel IP adreslerinizi alırsanız bkz [. statik genel IP adreslerini seçme](#publicIP)

- Sanal ağda kendi etki alanı adı sistemi (DNS) sunucunuzu kullanıyorsanız, bkz. [DNS sunucusunu ayarlama](#dns_server). 

- Alt ağda bir ağ güvenlik grubu (NSG) kullanıyorsanız bkz. [NSG ayarlama](#nsg). 

- Azure ExpressRoute veya Kullanıcı tanımlı bir yol (UDR) kullanıyorsanız bkz. [Azure ExpressRoute veya UDR kullanma](#route). 

- Sanal ağın kaynak grubunun (veya kendi genel IP adreslerini getirdiğinizden genel IP adreslerinin ' kaynak grubunun) belirli Azure ağ kaynaklarını oluşturup silebileceklerini doğrulayın. Daha fazla bilgi için bkz. [kaynak grubunu ayarlama](#resource-group). 

- [Azure-SSIS IR Için özel kurulum](./how-to-configure-azure-ssis-ir-custom-setup.md)'da açıklandığı gibi Azure-SSIS IR özelleştirirseniz, düğümlerini yönetme iç sürecimiz, önceden tanımlanmış bir 172.16.0.0 aralığından 172.31.255.255 'e özel IP adresleri tüketir. Sonuç olarak, lütfen sanal veya şirket içi ağlarınızın özel IP adresi aralıklarının bu aralıkla çakışmıyor olduğundan emin olun.

Bu diyagramda Azure-SSIS IR için gereken bağlantılar gösterilmektedir:

![Azure-SSIS IR için gereken bağlantıları gösteren diyagram.](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a> İzinleri ayarla

Azure-SSIS IR oluşturan kullanıcının aşağıdaki izinlere sahip olması gerekir:

- SSIS IR 'nizi Azure Resource Manager sanal bir ağa katılıyorsanız iki seçeneğiniz vardır:

  - Yerleşik ağ katılımcısı rolünü kullanın. Bu rol, gerekenden çok daha büyük bir kapsama sahip olan _Microsoft. Network/ \*_ Permission ile birlikte gelir.

  - Yalnızca gerekli _Microsoft. Network/virtualNetworks/ \* /Join/Action_ iznini içeren özel bir rol oluşturun. Ayrıca, Azure Resource Manager bir sanal ağa katılırken Azure-SSIS IR için kendi genel IP adreslerini getirmek istiyorsanız, lütfen role _Microsoft. Network/publicIPAddresses/*/Join/Action_ iznini de ekleyin.

- SSIS IR 'nizi klasik bir sanal ağa katılıyorsanız yerleşik, klasik sanal makine katılımcısı rolünü kullanmanızı öneririz. Aksi takdirde, sanal ağa ekleme iznini içeren özel bir rol tanımlamanız gerekir.

### <a name="select-the-subnet"></a><a name="subnet"></a> Alt ağı seçin

Bir alt ağ seçtiğinizde: 

- Azure-SSIS IR dağıtmak için GatewaySubnet 'i seçmeyin. Sanal ağ geçitleri için ayrılmıştır. 

- Seçtiğiniz alt ağın Azure-SSIS IR için kullanılabilir adres alanının yeterli olduğundan emin olun. IR düğüm numarasının en az iki katı için kullanılabilir IP adreslerini bırakın. Azure, bazı IP adreslerini her alt ağ içinde ayırır. Bu adresler kullanılamaz. Alt ağların ilk ve son IP adresleri protokol uyumu için ayrılmıştır ve Azure hizmetleri için üç adres daha kullanılır. Daha fazla bilgi için bkz. [Bu alt AĞLARDAKI IP adreslerini kullanma konusunda herhangi bir kısıtlama var mı?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Diğer Azure hizmetleri tarafından (örneğin, SQL veritabanı SQL yönetilen örneği, App Service vb.) özel olarak bulunan bir alt ağ kullanmayın. 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Statik genel IP adreslerini seçin

Bir sanal ağa katılırken Azure-SSIS IR için kendi statik genel IP adreslerini getirmek istiyorsanız, aşağıdaki gereksinimleri karşıladığından emin olun:

- Diğer Azure kaynaklarıyla zaten ilişkilendirilmemiş iki adet kullanılmamış tane sağlanmalıdır. Azure-SSIS IR, her zaman düzenli olarak yükseltireceğiz kullanılacaktır. Tek bir genel IP adresinin, etkin Azure-SSIS IRS 'niz arasında paylaşılacağını unutmayın.

- Her ikisi de standart türden statik olmalıdır. Daha fazla ayrıntı için [genel IP adresi SKU 'larına](../virtual-network/public-ip-addresses.md#sku) bakın.

- Her ikisi de bir DNS adına sahip olmalıdır. Bunları oluştururken bir DNS adı sağlamadıysanız, bunu Azure portal yapabilirsiniz.

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Bunlar ve sanal ağ aynı abonelikte ve aynı bölgede olmalıdır.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a> DNS sunucusunu ayarlama 
Özel ana bilgisayar adınızı çözümlemek için Azure-SSIS IR tarafından birleştirilmiş bir sanal ağda kendi DNS sunucunuzu kullanmanız gerekiyorsa, aynı zamanda küresel Azure ana bilgisayar adlarını (örneğin, adlı bir Azure Depolama Blobu) çözümleyebiliyorsanız emin olun `<your storage account>.blob.core.windows.net` . 

Önerilen bir yaklaşım aşağıda verilmiştir: 

-   İstekleri Azure DNS iletmek için özel DNS 'i yapılandırın. Çözümlenmemiş DNS kayıtlarını kendi DNS sunucunuzdaki Azure özyinelemeli çözümleyiciler 'nin (168.63.129.16) IP adresine iletebilirsiniz. 

Daha fazla bilgi için, bkz. [kendı DNS sunucunuzu kullanan ad çözümlemesi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Lütfen özel ana bilgisayar adınız (örneğin yerine kullanın) için tam etki alanı adı (FQDN) kullanın `<your_private_server>.contoso.com` `<your_private_server>` . Alternatif olarak, kendi DNS son ekini (örneğin `contoso.com` ) herhangi bir Nitelenmemiş tek etiketli etki alanı adına otomatik olarak eklemek ve bunu DNS sorgularında kullanmadan önce BIR FQDN 'ye dönüştürmek için Azure-SSIS IR standart özel bir kurulum kullanabilirsiniz, bkz. [Standart özel kurulum örnekleri](./how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples). 

### <a name="set-up-an-nsg"></a><a name="nsg"></a> NSG ayarlama
Azure-SSIS IR tarafından kullanılan alt ağ için bir NSG uygulamanız gerekiyorsa, gelen ve giden trafiğe aşağıdaki bağlantı noktalarıyla izin verin: 

-   **Azure-SSIS IR gelen gereksinimi**

| Yön | Aktarım Protokolü | Kaynak | Kaynak bağlantı noktası aralığı | Hedef | Hedef bağlantı noktası aralığı | Yorumlar |
|---|---|---|---|---|---|---|
| Gelen | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (IR 'ye Kaynak Yöneticisi sanal bir ağa katılırsanız) <br/><br/>10100, 20100, 30100 (IR 'yi klasik bir sanal ağa katılırsanız)| Data Factory hizmeti, sanal ağdaki Azure-SSIS IR düğümlerle iletişim kurmak için bu bağlantı noktalarını kullanır. <br/><br/> Bir alt ağ düzeyinde NSG oluşturup oluşturamadıkça, Data Factory Azure-SSIS IR barındıran sanal makinelere bağlı ağ arabirimi kartları (NIC 'ler) düzeyinde her zaman bir NSG yapılandırır. Yalnızca belirtilen bağlantı noktalarında Data Factory IP adreslerinden gelen trafiğe NIC düzeyinde NSG tarafından izin verilir. Bu bağlantı noktalarını alt ağ düzeyinde internet trafiğine açmış olsanız bile, IP adreslerinden Data Factory IP adreslerinden gelen trafik NIC düzeyinde engellenir. |
| Gelen | TCP | Corpnetgördünüz | * | VirtualNetwork | 3389 | Seçim Bu kural yalnızca Microsoft Supporter 'ın müşteriyi gelişmiş sorun giderme için açmasını istemesi ve sorun giderirken hemen kapatılabilir olması durumunda gereklidir. **Corpnetgördünüz** hizmet etiketi, yalnızca Microsoft Kurumsal ağındaki güvenli erişim iş istasyonlarının uzak masaüstünü kullanmasına izin verir. Ve bu hizmet etiketi portaldan seçilemez ve yalnızca Azure PowerShell veya Azure CLı aracılığıyla kullanılabilir. <br/><br/> NIC düzeyi NSG 'de, bağlantı noktası 3389 varsayılan olarak açıktır ve Windows Güvenlik Duvarı 'nda, koruma için her IR düğümünde Varsayılan olarak giden bağlantı noktası 3389 Azure-SSIS IR izin vermediği sırada 3389 numaralı bağlantı noktasını denetlemenize izin veririz. |
||||||||

-   **Azure-SSIS IR giden gereksinimi**

| Yön | Aktarım Protokolü | Kaynak | Kaynak bağlantı noktası aralığı | Hedef | Hedef bağlantı noktası aralığı | Yorumlar |
|---|---|---|---|---|---|---|
| Giden | TCP | VirtualNetwork | * | AzureCloud | 443 | Sanal ağdaki Azure-SSIS IR düğümleri Azure depolama ve Azure Event Hubs gibi Azure hizmetlerine erişmek için bu bağlantı noktasını kullanır. |
| Giden | TCP | VirtualNetwork | * | İnternet | 80 | Seçim Sanal ağdaki Azure-SSIS IR düğümleri Internet 'ten bir sertifika iptal listesi indirmek için bu bağlantı noktasını kullanır. Bu trafiği engellerseniz, IR 'yi başlatır ve sertifika kullanımı için sertifika iptal listesini denetleme yeteneğini kaybederseniz performansı indirgeyede karşılaşabilirsiniz. Hedefi belirli FQDN 'lere daha fazla daraltmak istiyorsanız lütfen **Azure ExpressRoute veya UDR kullanma** bölümüne bakın|
| Giden | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | Seçim Bu kural yalnızca, sanal ağdaki Azure-SSIS IR düğümleri sunucunuz tarafından barındırılan bir SSıSDB 'ye erişirken gereklidir. Sunucu bağlantı ilkeniz **yeniden yönlendirme** yerine **proxy** olarak ayarlandıysa yalnızca bağlantı noktası 1433 gerekir. <br/><br/> Bu giden güvenlik kuralı, sanal ağ veya özel uç noktayla yapılandırılmış SQL veritabanı 'nda SQL yönetilen örneğiniz tarafından barındırılan bir SSSıSDB için geçerli değildir. |
| Giden | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | Seçim Bu kural yalnızca sanal ağdaki Azure-SSIS IR düğümleri, sanal ağda veya özel uç noktayla yapılandırılmış SQL veritabanı tarafından barındırılan bir SSıSDB 'ye erişirken gereklidir. Sunucu bağlantı ilkeniz **yeniden yönlendirme** yerine **proxy** olarak ayarlandıysa yalnızca bağlantı noktası 1433 gerekir. |
| Giden | TCP | VirtualNetwork | * | Depolama | 445 | Seçim Bu kural yalnızca Azure dosyalarında depolanan SSIS paketini yürütmek istediğinizde gereklidir. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a> Azure ExpressRoute veya UDR kullanma
Azure-SSIS IR giden trafiği incelemek istiyorsanız, [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) Zorlamalı tünel oluşturma (bir BGP yolu, 0.0.0.0/0, sanal ağa bildirme) veya [udrs](../virtual-network/virtual-networks-udr-overview.md)aracılığıyla bir güvenlik duvarı ya da [Azure Güvenlik Duvarı](../firewall/index.yml) olarak ağ sanal gereci (NVA) ile Azure-SSIS IR başlatılan trafiği şirket içi güvenlik duvarı aracısına yönlendirebilir. 

![Azure-SSIS IR için NVA senaryosu](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

Tüm senaryonun çalışmasını sağlamak için aşağıdaki işlemleri yapmanız gerekir
   -   Azure Batch Yönetim Hizmetleri ve Azure-SSIS IR arasındaki gelen trafik güvenlik duvarı gereci üzerinden yönlendirilemez.
   -   Güvenlik Duvarı gereci Azure-SSIS IR için gereken giden trafiğe izin verir.

Azure Batch Yönetim Hizmetleri ve Azure-SSIS IR arasındaki gelen trafik, güvenlik duvarı gerecine yönlendirilemez, aksi halde asimetrik yönlendirme sorunu nedeniyle trafik bozulur. Trafiğin, içinde geldiği şekilde tekrar yanıt vermesi için gelen trafik için yolların tanımlanması gerekir. Azure Batch Yönetim Hizmetleri ve Azure-SSIS IR sonraki atlama türü ile **Internet** arasında trafiği yönlendirmek Için belirli udrs tanımlayabilirsiniz.

Örneğin, Azure-SSIS IR, `UK South` ve Azure Güvenlik Duvarı aracılığıyla giden trafiği incelemek istiyorsanız, hizmet `BatchNodeManagement.UKSouth` [etiketleri IP aralığı Indirme bağlantısı](https://www.microsoft.com/download/details.aspx?id=56519) veya [hizmet etiketi bulma API 'SI](../virtual-network/service-tags-overview.md#service-tags-on-premises)aracılığıyla hizmet etiketinin bir IP aralığı listesini alacaksınız. Ardından, sonraki atlama türü ile birlikte, sonraki atlama türü ile ilgili IP aralığı yollarının aşağıdaki UDRs değerini, sonraki atlama türü **Sanal Gereç** olan 0.0.0.0/0 rotasıyla birlikte **Internet** olarak uygulayın.

![Azure Batch UDR ayarları](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Bu yaklaşım, ek bir bakım maliyeti doğurur. Azure-SSIS IR bozmamak için IP aralığını düzenli olarak kontrol edin ve UDR 'nize yeni IP aralıkları ekleyin. IP aralığını aylık olarak kontrol etmenizi öneririz, çünkü yeni IP hizmet etiketinde göründüğünde IP 'nin başka bir ay süreceği geçerli olur. 

UDR kurallarının kurulumunu kolaylaştırmak için aşağıdaki PowerShell komut dosyasını çalıştırarak Azure Batch Yönetim Hizmetleri için UDR kuralları ekleyebilirsiniz:
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

Giden trafiğe izin vermek için güvenlik duvarı gerecinin, NSG giden kurallarındaki gereksinimlerle aynı bağlantı noktalarıyla aynı olması gerekir.
-   Azure Cloud Services olarak hedefe sahip bağlantı noktası 443.

    Azure Güvenlik duvarı kullanıyorsanız, Azurecyüksek hizmet etiketiyle ağ kuralı belirtebilirsiniz. Diğer türlerin güvenlik duvarı için, yalnızca hedefe izin ver 443 numaralı bağlantı noktası veya Azure ortamınızın türüne bağlı olarak FQDN 'lerin altına izin verebilirsiniz:

    | Azure ortamı | Uç Noktalar                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Genel      | <ul><li><b>Azure Data Factory (Yönetim)</b><ul><li>\*. frontend.clouddatahub.net</li></ul></li><li><b>Azure depolama (Yönetim)</b><ul><li>\*.blob.core.windows.net</li><li>\*. table.core.windows.net</li></ul></li><li><b>Azure Container Registry (özel kurulum)</b><ul><li>\*. azurecr.io</li></ul></li><li><b>Olay Hub 'ı (günlük)</b><ul><li>\*. servicebus.windows.net</li></ul></li><li><b>Microsoft günlük hizmeti (Iç kullanım)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Kamu  | <ul><li><b>Azure Data Factory (Yönetim)</b><ul><li>\*. frontend.datamovement.azure.us</li></ul></li><li><b>Azure depolama (Yönetim)</b><ul><li>\*. blob.core.usgovcloudapi.net</li><li>\*. table.core.usgovcloudapi.net</li></ul></li><li><b>Azure Container Registry (özel kurulum)</b><ul><li>\*. azurecr.us</li></ul></li><li><b>Olay Hub 'ı (günlük)</b><ul><li>\*. servicebus.usgovcloudapi.net</li></ul></li><li><b>Microsoft günlük hizmeti (Iç kullanım)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Azure Data Factory (Yönetim)</b><ul><li>\*. frontend.datamovement.azure.cn</li></ul></li><li><b>Azure depolama (Yönetim)</b><ul><li>\*. blob.core.chinacloudapi.cn</li><li>\*. table.core.chinacloudapi.cn</li></ul></li><li><b>Azure Container Registry (özel kurulum)</b><ul><li>\*. azurecr.cn</li></ul></li><li><b>Olay Hub 'ı (günlük)</b><ul><li>\*. servicebus.chinacloudapi.cn</li></ul></li><li><b>Microsoft günlük hizmeti (Iç kullanım)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Azure depolama, Azure Container Registry ve Olay Hub 'ının FQDN 'leri itibariyle, bu uç noktalara giden ağ trafiğinin güvenlik duvarı gerecinize yönlendirilmek yerine Azure omurga ağı üzerinden geçmesi için sanal ağınız için aşağıdaki hizmet uç noktalarını etkinleştirmeyi de tercih edebilirsiniz:
    -  Microsoft.Storage
    -  Microsoft. ContainerRegistry
    -  Microsoft. EventHub


-   Kaynak ile CRL indirme siteleri olan bağlantı noktası 80.

    CRL (sertifika Iptal listesi) olarak kullanılan FQDN 'lerin altına Azure-SSIS IR yönetim amacına yönelik sertifikaların sitelerini indirmelerine izin vermeniz gerekir:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Farklı CRL içeren sertifikalar kullanıyorsanız, bunları da dahil etmeniz önerilir. [Sertifikayı, sertifika Iptal listesi](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx)hakkında daha fazla bilgi edinmek için okuyabilirsiniz.

    Bu trafiğe izin vermemek için Azure-SSIS IR başlatıldığında ve güvenlik noktasından önerilmeyen sertifika kullanımı için sertifika iptal listesini denetleme yeteneğini kaybetmeniz durumunda performans düşürme işlemi yaşayabilirsiniz.

-   Azure SQL veritabanı olarak hedef ile bağlantı noktası 1433, 11000-11999 (yalnızca sanal ağdaki Azure-SSIS IR düğümleri sunucunuz tarafından barındırılan bir SSıSDB 'ye erişirken gereklidir).

    Azure Güvenlik duvarı kullanıyorsanız, Azure SQL hizmeti etiketiyle ağ kuralı belirtebilirsiniz, aksi takdirde hedefe güvenlik duvarı gereci içinde belirli Azure SQL URL 'si olarak izin verebilirsiniz.

-   Azure depolama olarak hedefe sahip bağlantı noktası 445 (yalnızca Azure dosyalarında depolanan SSIS paketini yürüttüğünüzde gereklidir).

    Azure Güvenlik duvarı kullanırsanız, depolama hizmeti etiketiyle ağ kuralı belirtebilirsiniz, aksi takdirde hedefe güvenlik duvarı gereci içinde belirli Azure dosya depolama URL 'si olarak izin verebilirsiniz.

> [!NOTE]
> Azure SQL ve depolama için, alt ağınızda sanal ağ hizmeti uç noktalarını yapılandırırsanız, aynı bölgedeki veya eşleştirilmiş bölgedeki Azure-SSIS IR ile Azure SQL arasındaki trafik, güvenlik duvarı gereci yerine doğrudan Microsoft Azure omurga ağına yönlendirilir.

Azure-SSIS IR giden trafiği inceleme yeteneğine ihtiyacınız yoksa, tüm trafiği bir sonraki **atlama türüne zorlamak** için yalnızca Yönlendirme uygulayabilirsiniz:

-   Bir Azure ExpressRoute senaryosunda, Azure-SSIS IR barındıran alt ağda **Internet** olarak bir sonraki atlama türü ile bir 0.0.0.0/0 yolu uygulayabilirsiniz. 
-   Bir NVA senaryosunda, sonraki atlama türünden Azure-SSIS IR barındıran alt ağda geçerli olan 0.0.0.0/0 yolunu, **Internet**'e **Sanal Gereç** olarak değiştirebilirsiniz.

![Rota Ekle](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Sonraki atlama türü **İnternet** ile rota belirtme tüm trafiğin internet üzerinden gidebileceği anlamına gelmez. Hedef adres Azure hizmetlerinden birine ait olduğu sürece Azure, trafiği Internet 'e yönlendirmek yerine doğrudan Azure omurga ağı üzerinden hizmete yönlendirir.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a> Kaynak grubunu ayarlama

Azure-SSIS IR, sanal ağ ile aynı kaynak grubunda belirli ağ kaynaklarını oluşturması gerekir. Bu kaynaklar şunları içerir:
- *\<Guid> -Azurebatch-cloudserviceloaddengeleyici* adlı bir Azure yük dengeleyici.
- *\<Guid> -Azurebatch-cloudservicepublicıp* adlı BIR Azure genel IP adresi.
- *\<Guid> -Azurebatch-cloudservicenetworksecuritygroup* adlı bir ağ iş güvenlik grubu. 

> [!NOTE]
> Artık Azure-SSIS IR için kendi statik genel IP adreslerinizi getirebilirsiniz. Bu senaryoda, sanal ağ yerine yalnızca statik genel IP adreslerinizle aynı kaynak grubu altında Azure yük dengeleyici ve ağ güvenlik grubunu oluşturacağız.

Azure-SSIS IR, bu kaynaklar oluşturulur. Azure-SSIS IR durdurulduğunda bunlar silinir. Azure-SSIS IR için kendi statik genel IP adreslerinizi getiriyorsa, Azure-SSIS IR durdurulduğunda kendi statik genel IP adresleri silinmez. Azure-SSIS IR durdurmadan engellemeden kaçınmak için, diğer kaynaklarınızda bu ağ kaynaklarını yeniden kullanmayın.

Sanal ağ/statik genel IP adreslerinizin ait olduğu kaynak grubunda/abonelikte kaynak kilidi olmadığından emin olun. Salt bir okuma/silme kilidi yapılandırırsanız, Azure-SSIS IR başlatıp durdurma başarısız olur veya yanıt vermeyi durdurur.

Sanal ağın/statik genel IP adreslerinin ait olduğu kaynak grubu/abonelik altında aşağıdaki kaynakların oluşturulmasını önleyen bir Azure Ilke atamasından olmadığından emin olun: 
- Microsoft. Network/LoadBalancers 
- Microsoft. Network/NetworkSecurityGroups 
- Microsoft. Network/Publicıpaddresses 

Aboneliğinizin kaynak kotasının Yukarıdaki üç ağ kaynağı için yeterli olduğundan emin olun. Özellikle, sanal ağda oluşturulan her bir Azure-SSIS IR için, yukarıdaki üç ağ kaynağı için iki adet boş kota ayırmanız gerekir. Azure-SSIS IR, düzenli aralıklarla yükseltireceğiz, daha fazla bir kota kullanılacaktır.

### <a name="faq"></a><a name="faq"></a> SSS

- Gelen bağlantı için Azure-SSIS IR kullanıma sunulan genel IP adresini nasıl koruyabilirim? Genel IP adresini kaldırmak mümkün mü?
 
  Şu anda, Azure-SSIS IR bir sanal ağa katıldığında bir genel IP adresi otomatik olarak oluşturulur. Yalnızca Azure Batch yönetim hizmetlerinin Azure-SSIS IR gelen bağlantı kurulmasına izin vermek için NIC düzeyinde bir NSG sunuyoruz. Ayrıca, gelen koruma için bir alt ağ düzeyi NSG belirtebilirsiniz.

  Herhangi bir genel IP adresinin gösterilmesini istemiyorsanız, senaryonuza geçerliyse, Azure-SSIS IR bir sanal ağa katılmasını sağlamak yerine, [kendinden konak IR 'yi Azure-SSIS IR için ara sunucu olarak yapılandırmayı](./self-hosted-integration-runtime-proxy-ssis.md) düşünün.
 
- Azure-SSIS IR genel IP adresini, Veri Kaynaklarım için güvenlik duvarının izin verilenler listesine ekleyebilir miyim?

  Artık Azure-SSIS IR için kendi statik genel IP adreslerinizi getirebilirsiniz. Bu durumda, IP adreslerinizi, veri kaynaklarınızın güvenlik duvarı izin verilenler listesine ekleyebilirsiniz. Senaryonuza bağlı olarak Azure-SSIS IR veri erişiminin güvenliğini sağlamak için aşağıdaki diğer seçenekleri de göz önünde bulundurmanız gerekir:

  - Veri kaynağınız şirket içinde ise, bir sanal ağı şirket içi ağınıza bağladıktan ve Azure-SSIS IR sanal ağ alt ağına katıldıktan sonra bu alt ağın özel IP adresi aralığını, veri kaynağınız için güvenlik duvarının izin verilenler listesine ekleyebilirsiniz.
  - Veri kaynağınız sanal ağ hizmet uç noktalarını destekleyen bir Azure hizmeti ise, sanal ağ alt ağınızda bir sanal ağ hizmeti uç noktası yapılandırabilir ve Azure-SSIS IR bu alt ağa katabilirsiniz. Daha sonra veri kaynağınıza yönelik güvenlik duvarına bu alt ağa sahip bir sanal ağ kuralı ekleyebilirsiniz.
  - Veri kaynağınız Azure dışı bir bulut hizmeti ise, Azure-SSIS IR giden trafiği statik bir genel IP adresi aracılığıyla NVA/Azure Güvenlik Duvarı 'na yönlendirmek için UDR kullanabilirsiniz. Daha sonra, NVA/Azure Güvenlik duvarınızın statik genel IP adresini, veri kaynağınız için güvenlik duvarının izin verilenler listesine ekleyebilirsiniz.
  - Yukarıdaki seçeneklerden hiçbiri gereksinimlerinizi karşılamıyorsa, [Azure-SSIS IR için otomatik olarak barındırılan BIR IR 'yi proxy olarak yapılandırmayı](./self-hosted-integration-runtime-proxy-ssis.md)düşünün. Daha sonra, şirket içinde barındırılan IR 'yi barındıran makinenin statik genel IP adresini, veri kaynağınız için güvenlik duvarının izin verilenler listesine ekleyebilirsiniz.

- Neden Azure-SSIS IR için kendi kendinize getirmek istersem iki statik genel adres sağlamam gerekir mi?

  Azure-SSIS IR düzenli aralıklarla otomatik olarak güncelleştirilir. Yeni düğümler yükseltme sırasında oluşturulur ve eskiler silinir. Ancak, kapalı kalma süresini önlemek için eski düğümler, yeni olanlar hazırlanana kadar silinmeyecektir. Bu nedenle, eski düğümler tarafından kullanılan ilk statik genel IP adresiniz hemen yayımlanamazlar ve yeni düğümleri oluşturmak için ikinci statik genel IP adresiniz olması gerekir.

- Azure-SSIS IR için kendi statik genel IP adreslerimi aldım, ancak neden hala veri kaynaklarıma erişemiyorum?

  - İki statik genel IP adresinin, veri kaynaklarınızın güvenlik duvarı izin verilenler listesine eklendiğinden emin olun. Azure-SSIS IR her yükseltildikten sonra, statik genel IP adresi sizin tarafınızdan getirilen iki arasında geçiş yapılır. Bunlardan yalnızca birini izin verilenler listesine eklerseniz, Azure-SSIS IR için veri erişimi yükseltmeden sonra bozulur.
  - Veri kaynağınız bir Azure hizmeti ise, lütfen sanal ağ hizmeti uç noktalarıyla yapılandırılıp yapılandırılmadığını denetleyin. Bu durumda, Azure-SSIS IR veri kaynağınıza giden trafik, Azure hizmetleri tarafından yönetilen özel IP adreslerini kullanacak şekilde geçiş yapar ve kendi statik genel IP adreslerinizi, veri kaynağınız için güvenlik duvarının izin verilenler listesine ekler.

## <a name="azure-portal-data-factory-ui"></a>Azure portal (Data Factory UI)

Bu bölümde, Azure portal ve Data Factory Kullanıcı arabirimini kullanarak var olan bir Azure-SSIS IR sanal ağa (klasik veya Azure Resource Manager) nasıl katılabilmeniz gösterilmektedir. 

Azure-SSIS IR sanal ağa katılmadan önce sanal ağı düzgün şekilde yapılandırmanız gerekir. Sanal ağ türü (klasik veya Azure Resource Manager) için geçerli olan bölümdeki adımları izleyin. Ardından, Azure-SSIS IR sanal ağa katmak için üçüncü bölümdeki adımları izleyin. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager sanal ağını yapılandırma

Bir Azure-SSIS IR katılmayı denemeden önce Azure Resource Manager sanal ağını yapılandırmak için portalını kullanın.

1. Microsoft Edge veya Google Chrome 'ı başlatın. Şu anda yalnızca bu Web tarayıcıları Data Factory Kullanıcı arabirimini destekler. 

1. [Azure portalında](https://portal.azure.com) oturum açın. 

1. **Diğer hizmetler**' i seçin. **Sanal ağları** filtreleyin ve seçin. 

1. İçin filtre uygulayın ve listedeki Sanal ağınızı seçin. 

1. **Sanal ağ** sayfasında **Özellikler**' i seçin. 

1. Sanal ağ için kaynak KIMLIĞINI panoya kopyalamak üzere **kaynak kimliği** için Kopyala düğmesini seçin. KODU OneNote 'taki panodan veya bir dosyada kaydedin. 

1. Sol taraftaki menüde **alt ağlar**' ı seçin. Kullanılabilir adreslerin sayısının Azure-SSIS IR düğümlerden büyük olduğundan emin olun. 

1. Azure Batch sağlayıcısının sanal ağa sahip Azure aboneliğine kayıtlı olduğunu doğrulayın. Veya Azure Batch sağlayıcıyı kaydedin. Aboneliğinizde zaten bir Azure Batch hesabınız varsa, aboneliğiniz Azure Batch için kaydedilir. (Data Factory portalında Azure-SSIS IR oluşturursanız, Azure Batch sağlayıcı sizin için otomatik olarak kaydedilir.) 

   1. Azure portal, sol taraftaki menüden **abonelikler**' i seçin. 

   1. Aboneliğinizi seçin. 

   1. Sol tarafta **kaynak sağlayıcıları**' nı seçin ve **Microsoft.Batch** 'in kayıtlı bir sağlayıcı olduğunu onaylayın. 

   !["Kayıtlı" durumunun onayı](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Listede **Microsoft.Batch** görmüyorsanız, bunu kaydetmek için aboneliğinizde [boş bir Azure Batch hesabı oluşturun](../batch/batch-account-create-portal.md) . Daha sonra silebilirsiniz. 

### <a name="configure-a-classic-virtual-network"></a>Klasik sanal ağ yapılandırma

Bir Azure-SSIS IR katılmayı denemeden önce, klasik sanal ağı yapılandırmak için portalını kullanın. 

1. Microsoft Edge veya Google Chrome 'ı başlatın. Şu anda yalnızca bu Web tarayıcıları Data Factory Kullanıcı arabirimini destekler. 

1. [Azure portalında](https://portal.azure.com) oturum açın. 

1. **Diğer hizmetler**' i seçin. **Sanal ağlar (klasik)** için filtre uygulayın ve seçin. 

1. İçin filtre uygulayın ve listedeki Sanal ağınızı seçin. 

1. **Sanal ağ (klasik)** sayfasında **Özellikler**' i seçin. 

   ![Klasik sanal ağ kaynağı KIMLIĞI](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Klasik ağın kaynak KIMLIĞINI panoya kopyalamak için **kaynak kimliği** için Kopyala düğmesini seçin. KODU OneNote 'taki panodan veya bir dosyada kaydedin. 

1. Sol taraftaki menüde **alt ağlar**' ı seçin. Kullanılabilir adreslerin sayısının Azure-SSIS IR düğümlerden büyük olduğundan emin olun. 

   ![Sanal ağdaki kullanılabilir adreslerin sayısı](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Sanal ağ için **MicrosoftAzureBatch** Ile **Klasik sanal makine katılımcısı** rolüne katın. 

   1. Sol taraftaki menüden **erişim denetimi (IAM)** öğesini seçin ve **rol atamaları** sekmesini seçin. 

       !["Erişim denetimi" ve "Ekle" düğmeleri](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. **Rol ataması ekle**’yi seçin.

   1. Rol **ataması Ekle** sayfasında, **rol** Için, **Klasik sanal makine katılımcısı**' nı seçin. **Seç** kutusunda, **ddbf3205-c6bd-46AE-8127-60eb93363864** yapıştırın ve sonra arama sonuçları listesinden **Microsoft Azure Batch** ' yi seçin. 

       !["Rol ataması ekleme" sayfasında sonuçları ara](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Ayarları kaydetmek ve sayfayı kapatmak için **Kaydet** ' i seçin. 

       ![Erişim ayarlarını kaydet](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Katkıda bulunanlar listesinde **Microsoft Azure Batch** görtığınızdan emin olun. 

       ![Azure Batch erişimi onayla](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Azure Batch sağlayıcısının sanal ağa sahip Azure aboneliğine kayıtlı olduğunu doğrulayın. Veya Azure Batch sağlayıcıyı kaydedin. Aboneliğinizde zaten bir Azure Batch hesabınız varsa, aboneliğiniz Azure Batch için kaydedilir. (Data Factory portalında Azure-SSIS IR oluşturursanız, Azure Batch sağlayıcı sizin için otomatik olarak kaydedilir.) 

   1. Azure portal, sol taraftaki menüden **abonelikler**' i seçin. 

   1. Aboneliğinizi seçin. 

   1. Sol tarafta **kaynak sağlayıcıları**' nı seçin ve **Microsoft.Batch** 'in kayıtlı bir sağlayıcı olduğunu onaylayın. 

   !["Kayıtlı" durumunun onayı](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Listede **Microsoft.Batch** görmüyorsanız, bunu kaydetmek için aboneliğinizde [boş bir Azure Batch hesabı oluşturun](../batch/batch-account-create-portal.md) . Daha sonra silebilirsiniz. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Azure-SSIS IR bir sanal ağa ekleyin

Azure Resource Manager Sanal ağınızı veya klasik Sanal ağınızı yapılandırdıktan sonra, Azure-SSIS IR sanal ağa ekleyebilirsiniz:

1. Microsoft Edge veya Google Chrome 'ı başlatın. Şu anda yalnızca bu Web tarayıcıları Data Factory Kullanıcı arabirimini destekler. 

1. [Azure Portal](https://portal.azure.com), sol taraftaki menüden **veri fabrikaları**' nı seçin. Menüde **veri fabrikaları** görmüyorsanız, **diğer hizmetler**' i seçin ve ardından **zekası + analiz** bölümünde **veri fabrikaları**' nı seçin. 

   ![Veri fabrikaları listesi](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Listeden Azure-SSIS IR veri fabrikanızı seçin. Veri fabrikanızın giriş sayfasını görürsünüz. **Yazar & İzleyicisi** kutucuğunu seçin. Data Factory Kullanıcı arabirimini ayrı bir sekmede görürsünüz. 

   ![Data factory giriş sayfası](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Data Factory Kullanıcı arabiriminde, **Düzenle** sekmesine geçin, **Bağlantılar**' ı seçin ve **tümleştirme çalışma zamanları** sekmesine geçin. 

   !["Tümleştirme çalışma zamanları" sekmesi](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Azure-SSIS IR çalışıyorsa, **tümleştirme çalışma zamanları** listesinde, **eylemler** sütununda, Azure-SSIS IR için **Durdur** düğmesini seçin. Azure-SSIS IR durduruncaya kadar düzenleyemezsiniz. 

   ![IR 'yi durdur](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Tümleştirme çalışma **zamanları** listesinde, **eylemler** sütununda, Azure-SSIS IR için **Düzenle** düğmesini seçin. 

   ![Tümleştirme çalışma zamanını düzenleme](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Tümleştirme çalışma zamanı kurulum panelinde, **İleri** düğmesini seçerek **Genel ayarlar** ve **SQL ayarları** bölümlerinde ilerleyin. 

1. **Gelişmiş ayarlar** bölümünde: 

   1. **Azure-SSIS Integration Runtime katılması için bir VNET seçin, ADF 'nin belirli ağ kaynaklarını oluşturmasına izin verin ve isteğe bağlı olarak kendi statik ortak IP adreslerinizi getirin** onay kutusunu seçin. 

   1. **Abonelik** için sanal ağınıza sahip Azure aboneliğini seçin.

   1. **Konum** için, tümleştirme çalışma zamanından aynı konum seçilidir.

   1. **Tür** için, sanal ağınızın türünü seçin: klasik veya Azure Resource Manager. Klasik sanal ağların yakında kullanım dışı olacağı için bir Azure Resource Manager sanal ağı seçmenizi öneririz.

   1. **VNET adı** için sanal ağınızın adını seçin. Sanal ağ hizmet uç noktaları veya SQL yönetilen örneği, SSıSDB barındırmak için özel uç nokta ile SQL veritabanı için kullanılan bir tane olmalıdır. Ya da şirket içi ağınıza bağlı bir aynı olmalıdır. Aksi takdirde, Azure-SSIS IR için kendi statik genel IP adreslerinizi getirmek için herhangi bir sanal ağ olabilir.

   1. **Alt ağ adı** için, sanal ağınız için alt ağ adını seçin. SSıSDB 'yi barındırmak için sanal ağ hizmet uç noktaları ile SQL veritabanı için kullanılan bir tane olmalıdır. Ya da SSıSDB barındırmak için özel uç nokta olan SQL yönetilen örneği için kullanılan farklı bir alt ağ olmalıdır. Aksi takdirde, Azure-SSIS IR için kendi statik genel IP adreslerinizi getirmek için herhangi bir alt ağ olabilir.

   1. Azure-SSIS IR için kendi statik genel IP adreslerini getirmek isteyip istemediğinizi seçmek üzere **Azure-SSIS Integration Runtime için statik genel IP adreslerini getir** onay kutusunu seçin. bu sayede, veri kaynaklarınıza yönelik güvenlik duvarında bunlara izin verebilirsiniz.

      Onay kutusunu seçerseniz, aşağıdaki adımları uygulayın.

      1. **İlk statik genel IP adresi** için Azure-SSIS IR [gereksinimlerini karşılayan](#publicIP) ilk statik genel IP adresini seçin. Herhangi bir hesabınız yoksa, yeni bağlantı **Oluştur** ' a tıklayarak Azure Portal STATIK genel IP adresleri oluşturun ve ardından buradan Yenile düğmesine tıklayın, böylece bunları seçebilirsiniz.
      
      1. **İkinci statik genel IP adresi** için Azure-SSIS IR [gereksinimlerini karşılayan](#publicIP) ikinci statik genel IP adresini seçin. Herhangi bir hesabınız yoksa, yeni bağlantı **Oluştur** ' a tıklayarak Azure Portal STATIK genel IP adresleri oluşturun ve ardından buradan Yenile düğmesine tıklayın, böylece bunları seçebilirsiniz.

   1. **VNET doğrulaması**' nı seçin. Doğrulama başarılı olursa **devam**' ı seçin. 

   ![Bir sanal ağda gelişmiş ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. **Özet** bölümünde, Azure-SSIS IR için tüm ayarları gözden geçirin. Ardından **Güncelleştir**' i seçin.

1. Azure-SSIS IR **Eylemler** sütunundaki **başlat** düğmesini seçerek Azure-SSIS IR başlatın. Bir sanal ağa katılan Azure-SSIS IR başlatmak için 20 ila 30 dakika sürer. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>Değişkenleri tanımlama

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Sanal ağ yapılandırma

Azure-SSIS IR bir sanal ağa katılabilmeniz için önce sanal ağı yapılandırmanız gerekir. Sanal ağa katılması Azure-SSIS IR için sanal ağ izinlerini ve ayarlarını otomatik olarak yapılandırmak için aşağıdaki betiği ekleyin:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Azure-SSIS IR oluşturma ve sanal ağa ekleme

Bir Azure-SSIS IR oluşturabilir ve aynı anda bir sanal ağa katabilirsiniz. Tüm betik ve yönergeler için bkz. [Azure-SSIS IR oluşturma](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Mevcut bir Azure-SSIS IR sanal ağa ekleme

[Azure-SSIS IR oluştur](create-azure-ssis-integration-runtime.md) makalesinde, bir Azure-SSIS IR oluşturma ve aynı betikteki bir sanal ağa ekleme işlemlerinin nasıl yapılacağı gösterilir. Zaten bir Azure-SSIS IR varsa, sanal ağa katmak için aşağıdaki adımları izleyin: 
1. Azure-SSIS IR durdurun. 
1. Azure-SSIS IR sanal ağa katılacak şekilde yapılandırın. 
1. Azure-SSIS IR başlatın. 

### <a name="stop-the-azure-ssis-ir"></a>Azure-SSIS IR durdur

Bir sanal ağa katılabilmeniz için önce Azure-SSIS IR durdurmanız gerekir. Bu komut tüm düğümlerini serbest bırakır ve faturalandırmayı durduruyor:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Azure-SSIS IR katılacak sanal ağ ayarlarını yapılandırın

Azure-SSIS 'nin birleştirileceği sanal ağın ayarlarını yapılandırmak için şu betiği kullanın: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Azure-SSIS IR yapılandırma

Azure-SSIS IR bir sanal ağa katmak için şu `Set-AzDataFactoryV2IntegrationRuntime` komutu çalıştırın: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>Azure-SSIS IR başlatın

Azure-SSIS IR başlatmak için aşağıdaki komutu çalıştırın: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Bu komutun tamamlanabilmesi için 20 ila 30 dakika sürer.

## <a name="next-steps"></a>Sonraki adımlar

Azure-SSIS IR hakkında daha fazla bilgi için aşağıdaki makalelere bakın: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Bu makalede, Azure-SSIS IR dahil olmak üzere IRS hakkında genel kavramsal bilgiler sağlanmaktadır. 
- [Öğretici: SSIS paketlerini Azure 'A dağıtın](./tutorial-deploy-ssis-packages-azure.md). Bu öğretici, Azure-SSIS IR oluşturmak için adım adım yönergeler sağlar. SSIS kataloğunu barındırmak için Azure SQL veritabanını kullanır. 
- [Azure-SSIS IR oluşturun](create-azure-ssis-integration-runtime.md). Bu makale öğreticide genişletilir. SSIS kataloğunu barındırmak için sanal ağ hizmet uç noktaları veya bir sanal ağdaki SQL yönetilen örneği ile Azure SQL veritabanı kullanımıyla ilgili yönergeler sağlar. Azure-SSIS IR bir sanal ağa nasıl katılagösterdiğini gösterir. 
- [Azure-SSIS IR’yi izleme](monitor-integration-runtime.md#azure-ssis-integration-runtime). Bu makalede, Azure-SSIS IR hakkında nasıl bilgi alabileceğiniz gösterilmektedir. Döndürülen bilgiler için durum açıklamalarını sağlar. 
- [Azure-SSIS IR’yi yönetme](manage-azure-ssis-integration-runtime.md). Bu makalede Azure-SSIS IR nasıl durdurulacağı, başlatılacağı veya silineceği gösterilmektedir. Ayrıca, düğüm ekleyerek Azure-SSIS IR nasıl ölçeklendirilebilen de gösterilmektedir.
