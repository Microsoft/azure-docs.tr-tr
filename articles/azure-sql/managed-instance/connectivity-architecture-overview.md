---
title: Bağlantı mimarisi
titleSuffix: Azure SQL Managed Instance
description: Azure SQL yönetilen örneği iletişimi ve bağlantı mimarisi hakkında bilgi edinin ve bileşenlerin yönetilen örneğe trafiği nasıl yönlendirmiş olduğunu öğrenin.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 10/22/2020
ms.openlocfilehash: c91b0231271c6cbcf9ec92c7ad6d25f1bc0f9136
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960478"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Azure SQL Yönetilen Örneği için bağlantı mimarisi
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Bu makalede, Azure SQL yönetilen örneği ile iletişim açıklanır. Ayrıca, bağlantı mimarisini ve bileşenlerin yönetilen örneğe trafiği nasıl yönlendirdiğinden de açıklanmaktadır.  

SQL yönetilen örneği, Azure sanal ağının içine ve yönetilen örneklere ayrılmış alt ağa yerleştirilir. Bu dağıtım şunları sağlar:

- Güvenli bir özel IP adresi.
- Şirket içi bir ağı SQL yönetilen örneğine bağlama özelliği.
- SQL yönetilen örneği bağlantılı sunucuya veya başka bir şirket içi veri deposuna bağlama özelliği.
- SQL yönetilen örneğini Azure kaynaklarına bağlama özelliği.

## <a name="communication-overview"></a>İletişime genel bakış

Aşağıdaki diyagramda, SQL yönetilen örneğine bağlanan varlıklar gösterilmektedir. Ayrıca, yönetilen bir örnekle iletişim kurması gereken kaynakları gösterir. Diyagramın en altındaki iletişim işlemi, SQL yönetilen örneğine veri kaynakları olarak bağlanan müşteri uygulamalarını ve araçları temsil eder.  

![Bağlantı mimarisi varlıkları](./media/connectivity-architecture-overview/connectivityarch001.png)

SQL yönetilen örneği bir hizmet olarak platform (PaaS) teklifi. Azure, bu hizmeti telemetri veri akışlarına göre yönetmek için otomatik aracıları (Yönetim, dağıtım ve bakım) kullanır. Azure yönetiminden sorumlu olduğundan, müşteriler SQL yönetilen örnek sanal küme makinelerine Uzak Masaüstü Protokolü (RDP) üzerinden erişemez.

Son kullanıcılar veya uygulamalar tarafından başlatılan bazı işlemler, platformla etkileşimde bulunmak için SQL yönetilen örneği gerektirebilir. Bir SQL yönetilen örnek veritabanının oluşturulması bir durumdur. Bu kaynak Azure portal, PowerShell, Azure CLı ve REST API aracılığıyla sunulur.

SQL yönetilen örneği, yedeklemeler için Azure depolama, telemetri için Azure Event Hubs, kimlik doğrulaması için Azure Active Directory (Azure AD), Saydam Veri Şifrelemesi için Azure Key Vault (TDE) ve güvenlik ve desteklenebilirlik özellikleri sağlayan birkaç Azure platform hizmeti olan Azure hizmetlerine bağımlıdır. SQL yönetilen örneği, bu hizmetlere bağlantılar oluşturur.

Tüm iletişimler şifrelenir ve sertifikalar kullanılarak imzalanır. İletişim kuran tarafların güvenilirliğini denetlemek için, SQL yönetilen örnek, sertifika iptal listeleri aracılığıyla bu sertifikaları sürekli olarak doğrular. Sertifikalar iptal edildiğinde, SQL yönetilen örnek, verileri korumak için bağlantıları kapatır.

## <a name="high-level-connectivity-architecture"></a>Üst düzey bağlantı mimarisi

Yüksek düzeyde, SQL yönetilen örneği bir hizmet bileşenleri kümesidir. Bu bileşenler, müşterinin sanal ağ alt ağı içinde çalışan ayrılmış bir yalıtılmış sanal makine kümesinde barındırılır. Bu makineler bir sanal küme oluşturur.

Bir sanal küme birden çok yönetilen örneği barındırabilirler. Gerekirse, müşteri alt ağdaki sağlanan örneklerin sayısını değiştirdiğinde küme otomatik olarak genişletilir veya anlaşmaları yapılır.

Müşteri Uygulamaları, SQL yönetilen örneğine bağlanabilir ve sanal ağ, eşlenen sanal ağ veya VPN ya da Azure ExpressRoute ile bağlı ağ içindeki veritabanlarını sorgulayabilir ve güncelleştirebilir. Bu ağ bir uç nokta ve özel bir IP adresi kullanmalıdır.  

![Bağlantı mimarisi diyagramı](./media/connectivity-architecture-overview/connectivityarch002.png)

Azure yönetim ve Dağıtım Hizmetleri sanal ağ dışında çalışır. SQL yönetilen örneği ve Azure Hizmetleri, genel IP adreslerine sahip uç noktalara bağlanır. SQL yönetilen örneği bir giden bağlantı oluşturduğunda, alıcı son ağ adresi çevirisi (NAT), bağlantının bu genel IP adresinden geldiği gibi görünmesine neden olur.

Yönetim trafiği müşterinin sanal ağı üzerinden akar. Diğer bir deyişle, sanal ağın altyapısının öğeleri, örnek başarısız olur ve kullanılamaz hale getirerek Yönetim trafiğine zarar verebilir.

> [!IMPORTANT]
> Azure, müşteri deneyimini ve hizmet kullanılabilirliğini geliştirmek için Azure sanal ağ altyapısı öğelerinde bir ağ hedefi ilkesi uygular. İlke, SQL yönetilen örneğinin nasıl çalıştığını etkileyebilir. Bu platform mekanizması, ağ gereksinimlerini kullanıcılara saydam şekilde iletir. İlkenin ana hedefi, ağ yanlış yapılandırılmasını önlemektir ve normal SQL yönetilen örnek işlemlerini sağlamaktır. Yönetilen bir örneği sildiğinizde, ağ hedefi ilkesi de kaldırılır.

## <a name="virtual-cluster-connectivity-architecture"></a>Sanal küme bağlantısı mimarisi

SQL yönetilen örneği için bağlantı mimarisine daha ayrıntılı bir şekilde bakalım. Aşağıdaki diyagramda, sanal kümenin kavramsal düzeni gösterilmektedir.

![Sanal kümenin bağlantı mimarisi](./media/connectivity-architecture-overview/connectivityarch003.png)

İstemciler, form içeren bir ana bilgisayar adı kullanarak SQL yönetilen örneğine bağlanır `<mi_name>.<dns_zone>.database.windows.net` . Bu ana bilgisayar adı, ortak bir etki alanı adı sistemi (DNS) bölgesinde kayıtlı ve genel olarak çözümlenebildiği halde özel bir IP adresine çözümlenir. , `zone-id` Kümeyi oluşturduğunuzda otomatik olarak oluşturulur. Yeni oluşturulan bir küme, ikincil yönetilen bir örnek barındırıyorsa, birincil kümeyle bölge KIMLIĞINI paylaşır. Daha fazla bilgi için bkz. [otomatik yük devretme gruplarını kullanarak birden çok veritabanının saydam ve eşgüdümlü yük devretmesini etkinleştirme](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Bu özel IP adresi, SQL yönetilen örneği için iç yük dengeleyiciye aittir. Yük dengeleyici, trafiği SQL yönetilen örnek ağ geçidine yönlendirir. Aynı küme içinde birden çok yönetilen örnek çalıştırılabildiğinden ağ geçidi, trafiği doğru SQL altyapısı hizmetine yönlendirmek için SQL yönetilen örnek ana bilgisayar adını kullanır.

Yönetim ve Dağıtım Hizmetleri, dış yük dengeleyiciye eşlenen bir [Yönetim uç noktası](#management-endpoint) kullanarak SQL yönetilen örneği 'ne bağlanır. Trafik yalnızca SQL yönetilen örneğinin yönetim bileşenlerinin kullandığı önceden tanımlanmış bir bağlantı noktası kümesi üzerinde alındığında düğümlere yönlendirilir. Düğümlerdeki yerleşik bir güvenlik duvarı yalnızca Microsoft IP aralıklarından gelen trafiğe izin verecek şekilde ayarlanmıştır. Sertifikalar, Yönetim bileşenleri ile yönetim düzlemi arasındaki iletişimin hepsini karşılıklı olarak doğrular.

## <a name="management-endpoint"></a>Yönetim uç noktası

Azure, yönetim uç noktası kullanarak SQL yönetilen örneğini yönetir. Bu uç nokta, bir örneğin sanal kümesinin içindedir. Yönetim uç noktası, ağ düzeyindeki yerleşik bir güvenlik duvarı tarafından korunur. Uygulama düzeyinde, karşılıklı sertifika doğrulaması tarafından korunur. Uç noktanın IP adresini bulmak için, bkz. [Yönetim uç NOKTASıNıN IP adresini belirleme](management-endpoint-find-ip-address.md).

Bağlantılar SQL yönetilen örneği (yedeklemeler ve denetim günlükleri gibi) içinde başlatıldığında trafik yönetim uç noktasının genel IP adresinden başlar. Güvenlik duvarı kurallarını SQL yönetilen örneği için yalnızca IP adresine izin verecek şekilde ayarlayarak, SQL yönetilen örneğinden ortak hizmetlere erişimi sınırlayabilirsiniz. Daha fazla bilgi için bkz. [SQL yönetilen örneği yerleşik güvenlik duvarını doğrulama](management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> SQL yönetilen örnek bölgesi içindeki Azure hizmetlerine giden trafik en iyi duruma getirilir ve bu nedenle yönetim uç noktası için genel IP adresine verilmez. Bu nedenle, genellikle depolama için, IP tabanlı güvenlik duvarı kuralları kullanmanız gerekiyorsa, hizmetin SQL yönetilen örneğinden farklı bir bölgede olması gerekir.

## <a name="service-aided-subnet-configuration"></a>Hizmet destekli alt ağ yapılandırması

Müşteri güvenliği ve yönetilebilirlik gereksinimlerini karşılamak için, SQL yönetilen örneği el ile hizmet destekli alt ağ yapılandırmasına geçiyor.

Hizmet destekli alt ağ yapılandırması sayesinde, Kullanıcı veri (TDS) trafiği üzerinde tam denetime sahiptir, ancak SQL yönetilen örneği bir SLA 'yı karşılamak için yönetim trafiğinin kesintisiz olarak akmasını sağlamak için sorumluluk kazanır.

Hizmet destekli alt ağ yapılandırması, otomatik ağ yapılandırma yönetimi sağlamak ve hizmet uç noktalarını etkinleştirmek için sanal ağ [alt ağı temsili](../../virtual-network/subnet-delegation-overview.md) özelliğinin üzerine oluşturulur. 

Hizmet uç noktaları, yedeklemeleri ve denetim günlüklerini tutan depolama hesaplarında sanal ağ güvenlik duvarı kurallarını yapılandırmak için kullanılabilir. Hizmet uç noktaları etkin olsa bile, müşterilerin hizmet uç noktaları üzerinde ek güvenlik sağlayan [özel bağlantı](../../private-link/private-link-overview.md) kullanması önerilir.

> [!IMPORTANT]
> Denetim düzlemi yapılandırma karmaşıklığı nedeniyle, hizmet destekli alt ağ yapılandırması Ulusal bulutlarda hizmet uç noktalarını etkinleştirmez. 

### <a name="network-requirements"></a>Ağ gereksinimleri

SQL yönetilen örneğini sanal ağın içindeki ayrılmış bir alt ağda dağıtın. Alt ağ şu özelliklere sahip olmalıdır:

- **Ayrılmış alt ağ:** SQL yönetilen örnek alt ağı kendisiyle ilişkili başka bir bulut hizmeti içeremez ve bir ağ geçidi alt ağı olamaz. Alt ağ, herhangi bir kaynak ancak SQL yönetilen örneği içeremez ve daha sonra alt ağdaki diğer kaynak türlerini ekleyemezsiniz.
- **Alt ağ temsili:** SQL yönetilen örnek alt ağının, kaynak sağlayıcısına atanmış olması gerekir `Microsoft.Sql/managedInstances` .
- **Ağ güvenlik grubu (NSG):** NSG 'nin SQL yönetilen örnek alt ağıyla ilişkilendirilmesi gerekir. SQL yönetilen örneği yeniden yönlendirme bağlantıları için yapılandırıldığında, bağlantı noktası 1433 ve bağlantı noktaları 11000-11999 üzerindeki trafiği filtreleyerek, SQL yönetilen örnek veri uç noktasına erişimi denetlemek için NSG kullanabilirsiniz. Hizmet, yönetim trafiğinin kesintisiz akışına izin vermek için gereken geçerli [kuralları](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) otomatik olarak sağlar ve saklar.
- **Kullanıcı tanımlı yol (UDR) tablosu:** Bir UDR tablosunun SQL yönetilen örnek alt ağıyla ilişkilendirilmesi gerekir. Şirket içi özel IP aralıklarına sahip trafiği, sanal ağ geçidi veya sanal ağ gereci (NVA) aracılığıyla bir hedef olarak yönlendirmek için yol tablosuna giriş ekleyebilirsiniz. Hizmet, kesintisiz yönetim trafiği akışına izin vermek için gerekli olan [girişleri](#user-defined-routes-with-service-aided-subnet-configuration) otomatik olarak sağlayacak ve güncel tutacaktır.
- **Yeterlı IP adresi:** SQL yönetilen örnek alt ağı en az 32 IP adresine sahip olmalıdır. Daha fazla bilgi için bkz. [SQL yönetilen örneği için alt ağın boyutunu belirleme](vnet-subnet-determine-size.md). Yönetilen örnekleri, [SQL yönetilen örneği için ağ gereksinimlerini](#network-requirements)karşılayacak şekilde yapılandırdıktan sonra [, var olan ağda](vnet-existing-add-subnet.md) dağıtabilirsiniz. Bunu yapamazsanız [yeni bir ağ ve alt ağ](virtual-network-subnet-create-arm-template.md) oluşturabilirsiniz.

> [!IMPORTANT]
> Yönetilen bir örnek oluşturduğunuzda, ağ kurulum üzerinde uyumsuz değişiklikler yapılmasını engellemek için alt ağa bir ağ hedefi ilkesi uygulanır. Son örnek alt ağdan kaldırıldıktan sonra, ağ hedefi ilkesi de kaldırılır. Aşağıdaki kurallar yalnızca bilgilendirme amaçlıdır ve ARM şablonu/PowerShell/CLı kullanarak bunları dağıtmamalıdır. En son resmi şablonu kullanmak istiyorsanız, her zaman [portaldan alabilirsiniz](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Hizmet destekli alt ağ yapılandırması ile zorunlu gelen güvenlik kuralları

| Name       |Bağlantı noktası                        |Protokol|Kaynak           |Hedef|Eylem|
|------------|----------------------------|--------|-----------------|-----------|------|
|yönetim  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |Mı ALT AĞı  |İzin Ver |
|            |9000, 9003                  |TCP     |Corpnetgördünüz       |Mı ALT AĞı  |İzin Ver |
|            |9000, 9003                  |TCP     |CorpnetPublic    |Mı ALT AĞı  |İzin Ver |
|mi_subnet   |Herhangi biri                         |Herhangi biri     |Mı ALT AĞı        |Mı ALT AĞı  |İzin Ver |
|health_probe|Herhangi biri                         |Herhangi biri     |AzureLoadBalancer|Mı ALT AĞı  |İzin Ver |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Hizmet destekli alt ağ yapılandırması ile zorunlu giden güvenlik kuralları

| Name       |Bağlantı noktası          |Protokol|Kaynak           |Hedef|Eylem|
|------------|--------------|--------|-----------------|-----------|------|
|yönetim  |443, 12000    |TCP     |Mı ALT AĞı        |AzureCloud |İzin Ver |
|mi_subnet   |Herhangi biri           |Herhangi biri     |Mı ALT AĞı        |Mı ALT AĞı  |İzin Ver |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Hizmet destekli alt ağ yapılandırmasıyla Kullanıcı tanımlı rotalar

|Name|Adres ön eki|Sonraki atlama|
|----|--------------|-------|
|alt ağdan vnetlocal|Mı ALT AĞı|Sanal ağ|
|mı-azurecyüksek-bölge-internet|Azurecyüksek. REGION|İnternet|
|mı-azurecyüksek-REGION_PAIR-internet|AzureCloud.REGION_PAIR|İnternet|
|mı-azuremonitor-Internet|AzureMonitor|İnternet|
|mı-corpnetpublic-Internet|CorpNetPublic|İnternet|
|mı-corpneti-Internet|Corpnetgördünüz|İnternet|
|mı-eventhub-REGION-Internet|EventHub. REGION|İnternet|
|mı-eventhub-REGION_PAIR-Internet|EventHub.REGION_PAIR|İnternet|
|mı-SqlManagement-Internet|SqlManagement|İnternet|
|mı-depolama-Internet|Depolama|İnternet|
|mı-depolama-bölge-internet|Storage. REGION|İnternet|
|mı-depolama-REGION_PAIR-internet|Storage.REGION_PAIR|İnternet|
||||

\* Mı alt ağı, x. x. x. x/y biçimindeki alt ağın IP adresi aralığını ifade eder. Bu bilgileri, Azure portal alt ağ özelliklerinde bulabilirsiniz.

\** Hedef adres Azure hizmetlerinden biri için ise, Azure trafiği Internet 'e yönlendirmek yerine doğrudan Azure omurga ağı üzerinden hizmete yönlendirir. Sanal ağın içinde bulunduğu Azure bölgesi veya Azure hizmeti örneğinin dağıtıldığı Azure bölgesi ne olursa olsun, Azure hizmetleri arasındaki trafik İnternet'i dolaşmaz. Daha fazla ayrıntı için [UDR belgeleri sayfasını](../../virtual-network/virtual-networks-udr-overview.md)inceleyin.

Ayrıca, sanal ağ geçidi veya sanal ağ gereci (NVA) aracılığıyla şirket içi özel IP aralıklarına sahip trafiği bir hedef olarak yönlendirmek için yol tablosuna giriş ekleyebilirsiniz.

Sanal ağ özel bir DNS içeriyorsa, özel DNS sunucusunun ortak DNS kayıtlarını çözümleyebilmesi gerekir. Azure AD kimlik doğrulaması gibi ek özelliklerin kullanılması ek FQDN 'lerin çözümlenmesini gerektirebilir. Daha fazla bilgi için bkz. [özel DNS ayarlama](custom-dns-configure.md).

### <a name="networking-constraints"></a>Ağ kısıtlamaları

**TLS 1,2, giden bağlantılarda zorlanır**: Microsoft 2020 ' de, tüm Azure hizmetlerinde hizmet içi trafik için MICROSOFT için TLS 1,2. Azure SQL yönetilen örneği için, bu, SQL Server ile çoğaltma ve bağlı sunucu bağlantıları için kullanılan giden bağlantılarda TLS 1,2 ile sonuçlandı. SQL yönetilen örneği ile 2016 'den eski SQL Server sürümlerini kullanıyorsanız, lütfen [TLS 1,2 özgü güncelleştirmelerin](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) uygulandığından emin olun.

Şu sanal ağ özellikleri şu anda SQL yönetilen örneği ile *desteklenmiyor* :

- **Microsoft eşlemesi**: ExpressRoute bağlantı hatları üzerinde [Microsoft EŞLEMESINI](../../expressroute/expressroute-faqs.md#microsoft-peering) etkinleştirme, SQL yönetilen örneğinin bulunduğu bir sanal ağ ile doğrudan veya geçişli bir şekilde etkinleştiriliyor, sanal ağ içindeki SQL yönetilen örnek bileşenleri ve bağlı olduğu hizmetler arasındaki trafik akışını etkiler ve kullanılabilirlik sorunlarına neden olur. Microsoft eşlemesi zaten etkinleştirilmiş olan sanal ağa SQL yönetilen örnek dağıtımları başarısız olması beklenir.
- **Küresel sanal ağ eşlemesi**: Azure bölgelerinde [sanal ağ eşlemesi](../../virtual-network/virtual-network-peering-overview.md) bağlantısı, 9/22/2020 ' dan önce oluşturulan alt ağlara yerleştirilmiş SQL yönetilen örnekleri için çalışmaz.
- **AzurePlatformDNS**: platform DNS çözümlemesini engellemek için AzurePlatformDNS [HIZMETI etiketinin](../../virtual-network/service-tags-overview.md) kullanılması SQL yönetilen örneği kullanılamıyor olarak işleyebilir. SQL yönetilen örneği, altyapı içinde DNS çözümlemesi için müşteri tanımlı DNS 'i desteklese de platform için platform DNS işlemleri için bir bağımlılık vardır.
- **NAT ağ geçidi**: belirli BIR genel IP adresiyle giden bağlantıyı denetlemek Için [Azure sanal ağ NAT](../../virtual-network/nat-overview.md) kullanmak, SQL yönetilen örneği kullanılamaz hale gelirse. SQL yönetilen örnek hizmeti şu anda, sanal ağ NAT ile gelen ve giden akışların birlikte bulunmasını sağlamayan temel yük dengeleyicinin kullanımıyla sınırlıdır.
- **Azure sanal ağ Için IPv6**: [çift yığın IPv4/ıPV6 sanal ağlarına](../../virtual-network/ipv6-overview.md) SQL yönetilen örneği dağıtmanın başarısız olması beklenir. IPv6 adresi öneklerini içeren ağ güvenlik grubu (NSG) veya yol tablosu (UDR) SQL yönetilen örnek alt ağına ilişkilendirirken veya zaten yönetilen örnek alt ağıyla ilişkilendirilmiş olan NSG 'ye veya UDR 'ye IPv6 adres öneklerini eklemek, SQL yönetilen örneği kullanılamaz hale gelirse. NSG ve UDR içeren bir alt ağa yönelik SQL yönetilen örnek dağıtımları, zaten IPv6 ön eklerinin başarısız olması beklenir.

## <a name="next-steps"></a>Sonraki adımlar

- Genel bakış için bkz. [Azure SQL yönetilen örneği nedir?](sql-managed-instance-paas-overview.md).
- [Yeni bir Azure sanal ağını](virtual-network-subnet-create-arm-template.md) veya SQL yönetilen örneği dağıtabileceğiniz [mevcut bir Azure sanal ağını](vnet-existing-add-subnet.md) ayarlamayı öğrenin.
- SQL yönetilen örneği dağıtmak istediğiniz [alt ağın boyutunu hesaplayın](vnet-subnet-determine-size.md) .
- Yönetilen bir örnek oluşturmayı öğrenin:
  - [Azure Portal](instance-create-quickstart.md).
  - [PowerShell](scripts/create-configure-managed-instance-powershell.md)kullanarak.
  - [Azure Resource Manager şablonu](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)kullanarak.
  - [Bir Azure Resource Manager şablonu kullanarak (SSMS ile birlikte bulunan JumpBox kullanarak)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).
