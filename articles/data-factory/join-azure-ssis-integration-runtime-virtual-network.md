---
title: Azure-SSIS tümleştirme çalışma zamanını bir sanal ağa katma
description: Azure-SSIS tümleştirme çalışma zamanını bir Azure sanal ağına nasıl katılacağınızı öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: d36900a1ce05eaf022637a6ef6b866fe0d190b17
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672742"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS tümleştirme çalışma zamanını bir sanal ağa katma
Azure Data Factory SQL Server Integration Services (SSIS) kullanırken, Azure-SSIS tümleştirme çalışma zamanı 'nı (IR) aşağıdaki senaryolarda bir Azure sanal ağına katılmanız gerekir: 

- Proxy olarak şirket içinde barındırılan bir IR yapılandırmadan veya yönetmeden Azure-SSIS IR çalışan SSIS paketlerinden şirket içi veri depolarına bağlanmak istiyorsunuz. 

- Azure-SSIS IR çalışan SSIS paketlerindeki sanal ağ hizmet uç noktaları ile desteklenen Azure hizmet kaynaklarına bağlanmak istiyorsunuz.

- Sanal ağ hizmet uç noktaları veya bir sanal ağdaki yönetilen örnek ile Azure SQL veritabanı 'nda bir SSIS katalog veritabanını (SSSıSDB) barındırıyorsanız. 

Data Factory, Azure-SSIS IR klasik dağıtım modeli veya Azure Resource Manager dağıtım modeli aracılığıyla oluşturulan bir sanal ağa eklemenize olanak tanır. 

> [!IMPORTANT]
> Klasik sanal ağ kullanım dışı olduğundan bunun yerine Azure Resource Manager sanal ağı kullanın.  Klasik sanal ağı zaten kullanıyorsanız, Azure Resource Manager sanal ağa mümkün olan en kısa sürede geçiş yapın.

## <a name="access-to-on-premises-data-stores"></a>Şirket içi veri depolarına erişim
SSIS 'niz kuruluşunuzda şirket içi veri depolarına erişebiliyse, Azure-SSIS IR şirket içi ağa bağlı bir sanal ağa ekleyebilirsiniz. Ya da şirket içinde barındırılan bir IR, Azure-SSIS IR için ara sunucu olarak yapılandırabilir veya yönetebilirsiniz. Daha fazla bilgi için bkz. [Azure-SSIS IR için otomatik olarak barındırılan BIR IR ara sunucu olarak yapılandırma](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

Azure-SSIS IR bir sanal ağa katılırken şu önemli noktaları hatırlayın: 

- Şirket içi ağınıza bağlı bir sanal ağ yoksa, önce Azure-SSIS IR katılmaları için bir [Azure Resource Manager sanal ağı](../virtual-network/quick-create-portal.md#create-a-virtual-network) oluşturun. Ardından, bu sanal ağdan şirket içi ağınıza bir siteden siteye [VPN Ağ Geçidi bağlantısı](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) veya [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) bağlantısı yapılandırın. 

- Azure Resource Manager bir sanal ağ, Azure-SSIS IR aynı konumda bulunan şirket içi ağınıza zaten bağlıysa, IR 'ye bu sanal ağa katabilirsiniz. 

- Klasik bir sanal ağ, Azure-SSIS IR farklı bir konumda şirket içi ağınıza zaten bağlıysa, Azure-SSIS IR katılması için [Azure Resource Manager bir sanal ağ](../virtual-network/quick-create-portal.md#create-a-virtual-network) oluşturabilirsiniz. Ardından, [Klasik-Azure Resource Manager sanal ağ](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) bağlantısı yapılandırın. 
 
- Azure Resource Manager bir sanal ağ, Azure-SSIS IR farklı bir konumda şirket içi ağınıza zaten bağlıysa, öncelikle Azure-SSIS IR katılması için bir [Azure Resource Manager sanal ağı](../virtual-network/quick-create-portal.md##create-a-virtual-network) oluşturabilirsiniz. Ardından Azure Resource Manager Azure Resource Manager bir sanal ağ bağlantısı yapılandırın. 

## <a name="access-to-azure-services"></a>Azure hizmetlerine erişim
SSIS paketleriniz, [sanal ağ hizmeti uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md) Ile desteklenen Azure hizmet kaynaklarına erişebiliyorlarsa ve bu kaynakların Azure-SSIS IR güvenliğini sağlamak istiyorsanız, Azure-SSIS IR sanal ağ ile yapılandırılmış sanal ağ alt ağına ekleyebilirsiniz hizmet uç noktaları. Bu arada, aynı alt ağdan erişime izin vermek için Azure hizmet kaynaklarına bir sanal ağ kuralı ekleyin.

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>SQL veritabanında SSIS kataloğunu barındırma
SSIS kataloğunuzu Azure SQL veritabanı 'nda sanal ağ hizmet uç noktaları ile barındırdıysanız, Azure-SSIS IR aynı sanal ağa ve alt ağa kattığınızdan emin olun.

Azure-SSIS IR yönetilen örnekle aynı sanal ağa katmak için, Azure-SSIS IR yönetilen örnekten farklı bir alt ağda olduğundan emin olun. Azure-SSIS IR yönetilen örneğinden farklı bir sanal ağa katmak için sanal ağ eşlemesinin (aynı bölgeyle sınırlı) veya sanal ağ ile sanal ağ arasında bağlantı öneririz. Daha fazla bilgi için bkz. [uygulamanızı Azure SQL veritabanı yönetilen örneğine bağlama](../sql-database/sql-database-managed-instance-connect-app.md).

Her durumda, sanal ağ yalnızca Azure Resource Manager dağıtım modeliyle dağıtılabilir.

Aşağıdaki bölümlerde daha fazla ayrıntı sağlanmaktadır. 

## <a name="virtual-network-configuration"></a>Sanal ağ yapılandırması

Sanal ağınızı bu gereksinimleri karşılayacak şekilde ayarlayın: 

-   `Microsoft.Batch`, Azure-SSIS IR barındıran sanal ağ alt ağınızın aboneliği kapsamında kayıtlı bir sağlayıcı olduğundan emin olun. Klasik bir sanal ağ kullanıyorsanız, bu sanal ağ için klasik sanal makine katılımcısı rolüne de `MicrosoftAzureBatch` katın. 

-   Gerekli izinlere sahip olduğunuzdan emin olun. Daha fazla bilgi için bkz. [Izinleri ayarlama](#perms).

-   Azure-SSIS IR barındırmak için uygun alt ağı seçin. Daha fazla bilgi için bkz. [alt ağı seçme](#subnet). 

-   Sanal ağda kendi etki alanı adı sistemi (DNS) sunucunuzu kullanıyorsanız, bkz. [DNS sunucusunu ayarlama](#dns_server). 

-   Alt ağda bir ağ güvenlik grubu (NSG) kullanıyorsanız bkz. [NSG ayarlama](#nsg). 

-   Azure ExpressRoute veya Kullanıcı tanımlı bir yol (UDR) kullanıyorsanız bkz. [Azure ExpressRoute veya UDR kullanma](#route). 

-   Sanal ağın kaynak grubunun belirli Azure ağ kaynaklarını oluşturup silmesine dikkat edin. Daha fazla bilgi için bkz. [kaynak grubunu ayarlama](#resource-group). 

-   [Azure-SSIS IR Için özel kurulumda](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)açıklandığı gibi Azure-SSIS IR özelleştirirseniz Azure-SSIS IR düğümleriniz, önceden tanımlanmış bir 172.16.0.0 aralığından 172.31.255.255 'e özel IP adresleri alır. Bu nedenle, sanal veya şirket içi ağlarınızın özel IP adresi aralıklarının bu aralıkla çakışmıyor olduğundan emin olun.

Bu diyagramda Azure-SSIS IR için gereken bağlantılar gösterilmektedir:

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>İzinleri ayarla

Azure-SSIS IR oluşturan kullanıcının aşağıdaki izinlere sahip olması gerekir:

- SSIS IR 'nizi Azure Resource Manager sanal bir ağa katılıyorsanız iki seçeneğiniz vardır:

  - Yerleşik ağ katılımcısı rolünü kullanın. Bu rol, gerekenden çok daha büyük bir kapsama sahip olan _Microsoft. Network/\*_ iznine sahiptir.

  - Yalnızca gerekli _Microsoft. Network/virtualNetworks/\*/Join/Action_ iznini içeren özel bir rol oluşturun. 

- SSIS IR 'nizi klasik bir sanal ağa katılıyorsanız yerleşik, klasik sanal makine katılımcısı rolünü kullanmanızı öneririz. Aksi takdirde, sanal ağa ekleme iznini içeren özel bir rol tanımlamanız gerekir.

### <a name="subnet"></a>Alt ağı seçin

Bir alt ağ seçtiğinizde: 

-   Azure-SSIS IR dağıtmak için GatewaySubnet 'i seçmeyin. Sanal ağ geçitleri için ayrılmıştır. 

-   Seçtiğiniz alt ağın Azure-SSIS IR için kullanılabilir adres alanının yeterli olduğundan emin olun. IR düğüm numarasının en az iki katı için kullanılabilir IP adreslerini bırakın. Azure, bazı IP adreslerini her alt ağ içinde ayırır. Bu adresler kullanılamaz. Alt ağların ilk ve son IP adresleri protokol uyumu için ayrılmıştır ve Azure hizmetleri için üç adres daha kullanılır. Daha fazla bilgi için bkz. [Bu alt AĞLARDAKI IP adreslerini kullanma konusunda herhangi bir kısıtlama var mı?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

-   Diğer Azure hizmetleri tarafından (örneğin, SQL veritabanı yönetilen örneği, App Service vb.) özel olarak bulunan bir alt ağ kullanmayın. 

### <a name="dns_server"></a>DNS sunucusunu ayarlama 
Azure-SSIS IR tarafından birleştirilmiş bir sanal ağda kendi DNS sunucunuzu kullanmanız gerekiyorsa, genel Azure ana bilgisayar adlarını (örneğin, `<your storage account>.blob.core.windows.net`adlı bir Azure Depolama Blobu) çözümleyediğinden emin olun. 

Aşağıdaki adımlar önerilir: 

-   İstekleri Azure DNS iletmek için özel DNS 'i yapılandırın. Çözümlenmemiş DNS kayıtlarını kendi DNS sunucunuzdaki Azure özyinelemeli çözümleyiciler 'nin (168.63.129.16) IP adresine iletebilirsiniz. 

-   Özel DNS 'i sanal ağın birincil DNS sunucusu olarak ayarlayın. Azure DNS ikincil DNS sunucusu olarak ayarlayın. Azure özyinelemeli çözümleyicilerine (168.63.129.16) ait IP adresini, kendi DNS sunucunuzun kullanılamaması durumunda ikincil bir DNS sunucusu olarak kaydedin. 

Daha fazla bilgi için, bkz. [kendı DNS sunucunuzu kullanan ad çözümlemesi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a>NSG ayarlama
Azure-SSIS IR tarafından kullanılan alt ağ için bir NSG uygulamanız gerekiyorsa, gelen ve giden trafiğe aşağıdaki bağlantı noktalarıyla izin verin: 

| Yön | Aktarım Protokolü | Kaynak | Kaynak bağlantı noktası aralığı | Hedef | Hedef bağlantı noktası aralığı | Yorumlar |
|---|---|---|---|---|---|---|
| Gelen | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (IR 'ye Kaynak Yöneticisi sanal bir ağa katılırsanız) <br/><br/>10100, 20100, 30100 (IR 'yi klasik bir sanal ağa katılırsanız)| Data Factory hizmeti, sanal ağdaki Azure-SSIS IR düğümlerle iletişim kurmak için bu bağlantı noktalarını kullanır. <br/><br/> Bir alt ağ düzeyinde NSG oluşturup oluşturamadıkça, Data Factory Azure-SSIS IR barındıran sanal makinelere bağlı ağ arabirimi kartları (NIC 'ler) düzeyinde her zaman bir NSG yapılandırır. Yalnızca belirtilen bağlantı noktalarında Data Factory IP adreslerinden gelen trafiğe NIC düzeyinde NSG tarafından izin verilir. Bu bağlantı noktalarını alt ağ düzeyinde internet trafiğine açmış olsanız bile, IP adreslerinden Data Factory IP adreslerinden gelen trafik NIC düzeyinde engellenir. |
| Giden | TCP | VirtualNetwork | * | AzureCloud | 443 | Sanal ağdaki Azure-SSIS IR düğümleri Azure depolama ve Azure Event Hubs gibi Azure hizmetlerine erişmek için bu bağlantı noktasını kullanır. |
| Giden | TCP | VirtualNetwork | * | Internet | 80 | Sanal ağdaki Azure-SSIS IR düğümleri Internet 'ten bir sertifika iptal listesi indirmek için bu bağlantı noktasını kullanır. |
| Giden | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | Sanal ağdaki Azure-SSIS IR düğümleri, SQL veritabanı sunucunuz tarafından barındırılan bir SSıSDB 'ye erişmek için bu bağlantı noktalarını kullanır. SQL veritabanı sunucusu bağlantı ilkeniz **yeniden yönlendirme**yerine **proxy** olarak ayarlandıysa yalnızca bağlantı noktası 1433 gerekir. Bu giden güvenlik kuralı, sanal ağda yönetilen örneğiniz tarafından barındırılan bir SSSıSDB için geçerli değildir. |
||||||||

### <a name="route"></a>Azure ExpressRoute veya UDR kullanma
Şirket içi ağınızı Azure 'a genişletmek için sanal ağ altyapınıza bir [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) bağlantı hattı bağladığınızda, ortak bir yapılandırma Zorlamalı tünel kullanır (bir BGP yolunu, 0.0.0.0/0, sanal ağa duyuruyor). Bu tünel, denetim ve günlüğe kaydetme amacıyla sanal ağ akışından giden internet trafiğini şirket içi ağ gereçine zorlar. 
 
Ya da bir ağ sanal gereci (NVA), İnceleme ve günlüğe kaydetme için güvenlik duvarı veya Azure Güvenlik duvarı olarak barındıran başka bir alt ağa, Azure-SSIS IR barındıran alt ağdan giden internet trafiğini zorlamak için [udrs](../virtual-network/virtual-networks-udr-overview.md) tanımlayabilirsiniz. 

Her iki durumda da trafik yolu, bağımlı Azure Data Factory hizmetlerinden (özellikle de Azure Batch Yönetim Hizmetleri), sanal ağdaki Azure-SSIS IR için gerekli gelen bağlantıyı keser. Bunu önlemek için, Azure-SSIS IR içeren alt ağda bir veya daha fazla UDRs tanımlayın. 

Bir Azure ExpressRoute senaryosunda Azure-SSIS IR barındıran alt ağda **Internet** olarak bir sonraki atlama türü ile bir 0.0.0.0/0 yolu uygulayabilirsiniz. Ya da bir sonraki atlama türünden var olan 0.0.0.0/0 yolunu, bir NVA senaryosunda **Internet** 'e **Sanal Gereç** olarak değiştirebilirsiniz.

![Rota Ekle](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Giden internet trafiğini bu alt ağdan İnceleme özelliğini kaybetme konusunda endişeleriniz varsa, trafiği yalnızca Azure Batch Yönetim Hizmetleri ve Azure-SSIS IR bir sonraki atlama türü ile **İnternet**arasında yönlendirmek üzere belirli udrs tanımlayabilirsiniz.

Örneğin, Azure-SSIS IR `UK South`konumdaysa, hizmet [ETIKETLERI IP aralığı indirme bağlantısı](https://www.microsoft.com/en-us/download/details.aspx?id=56519) veya [hizmet etiketi bulma API 'si](https://aka.ms/discoveryapi)aracılığıyla HIZMET etiketi `BatchNodeManagement.UKSouth` bir IP aralığı listesi alırsınız. Ardından, sonraki atlama türü ile birlikte, ilgili IP aralığı yollarının aşağıdaki UDRs 'lerini **Internet**olarak uygulayın.

![Azure Batch UDR ayarları](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Bu yaklaşım, ek bir bakım maliyeti doğurur. Azure-SSIS IR bozmamak için IP aralığını düzenli olarak kontrol edin ve UDR 'nize yeni IP aralıkları ekleyin. IP aralığını aylık olarak kontrol etmenizi öneririz, çünkü yeni IP hizmet etiketinde göründüğünde IP 'nin başka bir ay süreceği geçerli olur. 

### <a name="resource-group"></a>Kaynak grubunu ayarlama
Azure-SSIS IR, sanal ağ ile aynı kaynak grubunda belirli ağ kaynaklarını oluşturması gerekir. Bu kaynaklar şunları içerir:
   -   *\<guıd >-azurebatch-cloudserviceloaddengeleyici*olan bir Azure yük dengeleyici.
   -   *\<guıd >-azurebatch-cloudservicepublicıp*adlı bir Azure genel IP adresi.
   -   *\<guıd >-azurebatch-cloudservicenetworksecuritygroup*adlı bir ağ iş güvenlik grubu. 

IR başlatıldığında bu kaynaklar oluşturulacaktır. IR durdurulduğunda bunlar silinir. IR durmasını engellemeyi önlemek için, diğer kaynaklarınızda bu ağ kaynaklarını yeniden kullanmayın. 

Kaynak grubunda veya sanal ağın ait olduğu abonelikte kaynak kilidi olmadığından emin olun. Salt bir okuma kilidi veya silme kilidi yapılandırırsanız, IR 'nin başlatılması ve durdurulması başarısız olabilir veya IR yanıt vermeyi durdurabilir. 

Aşağıdaki kaynakların, sanal ağın ait olduğu kaynak grubu veya abonelik altında oluşturulmasını engelleyen bir Azure ilkenizin olmadığından emin olun: 
   -   Microsoft. Network/LoadBalancers 
   -   Microsoft. Network/NetworkSecurityGroups 
   -   Microsoft. Network/Publicıpaddresses 

### <a name="faq"></a>SSS

- Gelen bağlantı için Azure-SSIS IR kullanıma sunulan genel IP adresini nasıl koruyabilirim? Genel IP adresini kaldırmak mümkün mü?
 
    Şu anda, Azure-SSIS IR sanal ağa katıldığında genel IP adresi otomatik olarak oluşturulur. Yalnızca Azure Batch yönetim hizmetlerinin Azure-SSIS IR gelen bağlantı kurulmasına izin vermek için NIC düzeyinde bir NSG sunuyoruz. Ayrıca, gelen koruma için bir alt ağ düzeyi NSG belirtebilirsiniz.

    Genel IP adresinin gösterilmesini istemiyorsanız, senaryonuza geçerliyse, sanal ağ yerine [Azure-SSIS IR için otomatik olarak BARıNDıRıLAN IR 'yi yapılandırmak](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) isteyebilirsiniz.
 
- Azure-SSIS IR statik IP adresini, veri kaynağı için güvenlik duvarının izin verilenler listesine ekleyebilir miyim?
 
    - Veri kaynağınız şirket içinde ise, sanal ağı şirket içi ağınıza bağlayıp Azure-SSIS IR sanal ağ alt ağına ekledikten sonra bu alt ağın IP aralığını izin verilenler listesine ekleyebilirsiniz.
    - Veri kaynağınız bir sanal ağ hizmeti uç noktası ile desteklenen bir Azure hizmeti ise, sanal ağınızda bir sanal ağ hizmet noktası yapılandırabilir ve Azure-SSIS IR bu sanal ağ alt ağına katabilirsiniz. Daha sonra, IP aralığı yerine Azure hizmetlerinin sanal ağ kuralını kullanarak erişime izin verebilirsiniz.
    - Veri kaynağınız farklı türde bir bulut veri kaynağı ise, bir statik genel IP adresi kullanarak Azure-SSIS IR giden trafiği NVA 'ya veya Azure Güvenlik Duvarı 'na yönlendirmek için UDR 'yi kullanabilirsiniz. İzin verilenler listesine NVA veya Azure Güvenlik duvarının genel IP adresini ekleyebilirsiniz.
    - Önceki yanıtlar ihtiyaçlarınızı karşılamıyorsa, [Azure-SSIS IR için ara sunucu olarak şirket içinde barındırılan BIR IR yapılandırarak](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)veri kaynağı erişimi sağlamayı düşünün. Daha sonra, şirket içinde barındırılan IR 'yi barındıran makinenin IP adresini, Azure-SSIS IR sanal ağa katılmak yerine izin verilenler listesine ekleyebilirsiniz.

## <a name="azure-portal-data-factory-ui"></a>Azure portal (Data Factory UI)
Bu bölümde, Azure portal ve Data Factory Kullanıcı arabirimini kullanarak var olan bir Azure-SSIS IR sanal ağa (klasik veya Azure Resource Manager) nasıl katılabilmeniz gösterilmektedir. 

Azure-SSIS IR sanal ağa katılmadan önce sanal ağı düzgün şekilde yapılandırmanız gerekir. Sanal ağ türü (klasik veya Azure Resource Manager) için geçerli olan bölümdeki adımları izleyin. Ardından, Azure-SSIS IR sanal ağa katmak için üçüncü bölümdeki adımları izleyin. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager sanal ağını yapılandırma

Bir Azure-SSIS IR katılmayı denemeden önce Azure Resource Manager sanal ağını yapılandırmak için portalını kullanın.

1. Microsoft Edge veya Google Chrome 'ı başlatın. Şu anda yalnızca bu Web tarayıcıları Data Factory Kullanıcı arabirimini destekler. 

1. [Azure portalında](https://portal.azure.com) oturum açın. 

1. **Diğer hizmetler**' i seçin. **Sanal ağları**filtreleyin ve seçin. 

1. İçin filtre uygulayın ve listedeki Sanal ağınızı seçin. 

1. **Sanal ağ** sayfasında **Özellikler**' i seçin. 

1. Sanal ağ için kaynak KIMLIĞINI panoya kopyalamak üzere **kaynak kimliği** için Kopyala düğmesini seçin. KODU OneNote 'taki panodan veya bir dosyada kaydedin. 

1. Sol taraftaki menüde **alt ağlar**' ı seçin. Kullanılabilir adreslerin sayısının Azure-SSIS IR düğümlerden büyük olduğundan emin olun. 

1. Azure Batch sağlayıcısının sanal ağa sahip Azure aboneliğine kayıtlı olduğunu doğrulayın. Veya Azure Batch sağlayıcıyı kaydedin. Aboneliğinizde zaten bir Azure Batch hesabınız varsa, aboneliğiniz Azure Batch için kaydedilir. (Data Factory portalında Azure-SSIS IR oluşturursanız, Azure Batch sağlayıcı sizin için otomatik olarak kaydedilir.) 

   a. Azure portal, sol taraftaki menüden **abonelikler**' i seçin. 

   b. Aboneliğinizi seçin. 

   c. Sol tarafta **kaynak sağlayıcıları**' nı seçin ve **Microsoft. Batch** ' in kayıtlı bir sağlayıcı olduğunu onaylayın. 

   !["Kayıtlı" durumunun onayı](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Listede **Microsoft. Batch** ' i görmüyorsanız, kaydetmek için aboneliğinizde [boş bir Azure Batch hesabı oluşturun](../batch/batch-account-create-portal.md) . Daha sonra silebilirsiniz. 

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

    a. Sol taraftaki menüden **erişim denetimi (IAM)** öğesini seçin ve **rol atamaları** sekmesini seçin. 

    !["Erişim denetimi" ve "Ekle" düğmeleri](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. **Rol ataması Ekle**' yi seçin.

    c. Rol **ataması Ekle** sayfasında, **rol**Için, **Klasik sanal makine katılımcısı**' nı seçin. **Seç** kutusunda, **ddbf3205-c6bd-46AE-8127-60eb93363864**yapıştırın ve sonra arama sonuçları listesinden **Microsoft Azure Batch** ' yi seçin. 

    !["Rol ataması ekleme" sayfasında sonuçları ara](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Ayarları kaydetmek ve sayfayı kapatmak için **Kaydet** ' i seçin. 

    ![Erişim ayarlarını kaydet](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Katkıda bulunanlar listesinde **Microsoft Azure Batch** görtığınızdan emin olun. 

    ![Azure Batch erişimi onayla](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Azure Batch sağlayıcısının sanal ağa sahip Azure aboneliğine kayıtlı olduğunu doğrulayın. Veya Azure Batch sağlayıcıyı kaydedin. Aboneliğinizde zaten bir Azure Batch hesabınız varsa, aboneliğiniz Azure Batch için kaydedilir. (Data Factory portalında Azure-SSIS IR oluşturursanız, Azure Batch sağlayıcı sizin için otomatik olarak kaydedilir.) 

   a. Azure portal, sol taraftaki menüden **abonelikler**' i seçin. 

   b. Aboneliğinizi seçin. 

   c. Sol tarafta **kaynak sağlayıcıları**' nı seçin ve **Microsoft. Batch** ' in kayıtlı bir sağlayıcı olduğunu onaylayın. 

   !["Kayıtlı" durumunun onayı](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Listede **Microsoft. Batch** ' i görmüyorsanız, kaydetmek için aboneliğinizde [boş bir Azure Batch hesabı oluşturun](../batch/batch-account-create-portal.md) . Daha sonra silebilirsiniz. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Azure-SSIS IR bir sanal ağa ekleyin

Azure Resource Manager Sanal ağınızı veya klasik Sanal ağınızı yapılandırdıktan sonra, Azure-SSIS IR sanal ağa ekleyebilirsiniz:

1. Microsoft Edge veya Google Chrome 'ı başlatın. Şu anda yalnızca bu Web tarayıcıları Data Factory Kullanıcı arabirimini destekler. 

1. [Azure Portal](https://portal.azure.com), sol taraftaki menüden **veri fabrikaları**' nı seçin. Menüde **veri fabrikaları** görmüyorsanız, **diğer hizmetler**' i seçin ve ardından **zekası + analiz** bölümünde **veri fabrikaları**' nı seçin. 

   ![Veri fabrikaları listesi](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Listeden Azure-SSIS IR veri fabrikanızı seçin. Veri fabrikanızın giriş sayfasını görürsünüz. **& dağıtım** kutucuğunu seçin. Data Factory Kullanıcı arabirimini ayrı bir sekmede görürsünüz. 

   ![Data factory giriş sayfası](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Data Factory Kullanıcı arabiriminde, **Düzenle** sekmesine geçin, **Bağlantılar**' ı seçin ve **tümleştirme çalışma zamanları** sekmesine geçin. 

   !["Tümleştirme çalışma zamanları" sekmesi](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Azure-SSIS IR çalışıyorsa, **tümleştirme çalışma zamanları** listesinde, **eylemler** sütununda, Azure-SSIS IR için **Durdur** düğmesini seçin. Bir IR 'yi durduruncaya kadar düzenleyemezsiniz. 

   ![IR 'yi durdur](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Tümleştirme çalışma **zamanları** listesinde, **eylemler** sütununda, Azure-SSIS IR için **Düzenle** düğmesini seçin. 

   ![Tümleştirme çalışma zamanını düzenleme](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. **Integration Runtime kurulum** panelinde, **Ileri** düğmesini seçerek **Genel ayarlar** ve **SQL ayarları** sayfalarından ilerleyin. 

1. **Gelişmiş ayarlar** sayfasında: 

   a. **VNET Seç**' in yanındaki onay kutusunu işaretleyin. 

   b. **Abonelik**için Azure aboneliğinizi seçin. Abonelik altında, var olan bir sanal ağı seçebilirsiniz. 
  
   c. **VNET adı**için Sanal ağınızı seçin. 

   d. **Alt ağ adı**için sanal ağda alt ağınızı seçin. 

   e. Ayrıca, Azure-SSIS IR için bir ara sunucu olarak şirket içinde barındırılan bir IR yapılandırmak veya yönetmek istiyorsanız, **Şirket Içinde barındırılan** kurulum onay kutusunu seçin. Daha fazla bilgi için bkz. [Azure-SSIS IR için otomatik olarak barındırılan BIR IR ara sunucu olarak yapılandırma](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).

   f. **VNET doğrulama** düğmesini seçin. Doğrulama başarılı olursa, **İleri** düğmesini seçin. 

   ![IR kurulumu için Gelişmiş ayarlar](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. **Özet** sayfasında, Azure-SSIS IR için tüm ayarları gözden geçirin. Ardından **Güncelleştir** düğmesini seçin.

1. Azure-SSIS IR **Eylemler** sütunundaki **başlat** düğmesini seçerek Azure-SSIS IR başlatın. Bir sanal ağa katılan Azure-SSIS IR başlatmak için 20 ila 30 dakika sürer. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

### <a name="define-the-variables"></a>Değişkenleri tanımlama
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

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
Azure-SSIS IR sanal ağa katmak üzere yapılandırmak için `Set-AzDataFactoryV2IntegrationRuntime` komutunu çalıştırın: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
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
- [Öğretici: SSIS paketlerini Azure 'A dağıtın](tutorial-create-azure-ssis-runtime-portal.md). Bu öğretici, Azure-SSIS IR oluşturmak için adım adım yönergeler sağlar. SSIS kataloğunu barındırmak için Azure SQL veritabanını kullanır. 
- [Azure-SSIS IR oluşturun](create-azure-ssis-integration-runtime.md). Bu makale öğreticide genişletilir. SSIS kataloğunu barındırmak için sanal ağ hizmet uç noktaları veya yönetilen örnek ile Azure SQL veritabanı kullanımıyla ilgili yönergeler sağlar. Azure-SSIS IR bir sanal ağa nasıl katılagösterdiğini gösterir. 
- [Azure-SSIS IR’yi izleme](monitor-integration-runtime.md#azure-ssis-integration-runtime). Bu makalede, Azure-SSIS IR hakkında nasıl bilgi alabileceğiniz gösterilmektedir. Döndürülen bilgiler için durum açıklamalarını sağlar. 
- [Azure-SSIS IR’yi yönetme](manage-azure-ssis-integration-runtime.md). Bu makalede Azure-SSIS IR nasıl durdurulacağı, başlatılacağı veya silineceği gösterilmektedir. Ayrıca, düğüm ekleyerek Azure-SSIS IR nasıl ölçeklendirilebilen de gösterilmektedir.
