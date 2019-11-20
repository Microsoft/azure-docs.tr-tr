---
title: Sanal ağlarla Azure API Management kullanma
description: Azure API Management 'de bir sanal ağa bağlantı ayarlamayı ve üzerinden Web hizmetlerine erişmeyi öğrenin.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/13/2019
ms.author: apimpm
ms.openlocfilehash: 4a188a8de4f1cbf9d5bc20f7e514e3f5a2c752dc
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074621"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Sanal ağlarla Azure API Management kullanma
Azure Sanal Ağları (VNET’ler) Azure kaynaklarınızdan herhangi birini, erişimini denetlediğiniz İnternet tabanlı olmayan ve yönlendirilebilir bir ağa yerleştirmenizi sağlar. Bu ağlar daha sonra, çeşitli VPN teknolojileri kullanılarak şirket içi ağlarınıza bağlanabilir. Azure sanal ağları hakkında daha fazla bilgi edinmek için buradaki bilgilerle başlayın: [Azure sanal ağına genel bakış](../virtual-network/virtual-networks-overview.md).

Azure API Management, sanal ağ (VNET) içinde dağıtılabilir ve bu sayede ağ içindeki arka uç hizmetlerine erişebilir. Geliştirici portalı ve API ağ geçidi, Internet 'ten ya da yalnızca sanal ağ içinden erişilebilir olacak şekilde yapılandırılabilir.

> [!NOTE]
> API içeri aktarma belgesi URL 'SI, genel olarak erişilebilen bir internet adresinde barındırılmalıdır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu makalede açıklanan adımları gerçekleştirmek için şunları yapmanız gerekir:

+ Etkin bir Azure aboneliği.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Bir APıM örneği. Daha fazla bilgi için bkz. [Azure API Management örneği oluşturma](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>VNET bağlantısını etkinleştir

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Azure portal kullanarak VNET bağlantısını etkinleştirme

1. [Azure Portal](https://portal.azure.com/)APIM örneğinize gidin.
2. **Sanal ağ**' ı seçin.
3. API Management örneğini bir sanal ağın içine dağıtılacak şekilde yapılandırın.

    ![API Management sanal ağ menüsü][api-management-using-vnet-menu]
4. İstenen erişim türünü seçin:

   * **Kapalı**: bu varsayılandır. API Management bir sanal ağa dağıtılmadı.

   * **Harici**: API Management ağ geçidi ve geliştirici portalına bir dış yük dengeleyici aracılığıyla genel İnternet üzerinden erişilebilir. Ağ Geçidi, sanal ağ içindeki kaynaklara erişebilir.

     ![Ortak eşleme][api-management-vnet-public]

   * **İç**: API Management ağ geçidi ve geliştirici portalına yalnızca iç yük dengeleyici aracılığıyla sanal ağ içinden erişilebilir. Ağ Geçidi, sanal ağ içindeki kaynaklara erişebilir.

     ![Özel eşleme][api-management-vnet-private]

     Artık API Management hizmetinizin sağlandığı tüm bölgelerin bir listesini görürsünüz. Her bölge için bir VNET ve alt ağ seçin. Liste, yapılandırdığınız bölgede kurulu olan Azure aboneliklerinizde bulunan hem klasik hem de Kaynak Yöneticisi sanal ağlarla doldurulur.

     > [!IMPORTANT]
     > Bir Azure API Management örneğini bir Kaynak Yöneticisi VNET 'e dağıttığınızda, hizmet Azure API Management örnekleri dışında başka hiçbir kaynak içermeyen bir ayrılmış alt ağda olmalıdır. Azure API Management örneğini diğer kaynakları içeren bir Kaynak Yöneticisi VNET alt ağına dağıtmaya yönelik bir girişim yapılırsa, dağıtım başarısız olur.
     >

     ![VPN seçin][api-management-setup-vpn-select]

5. Üst gezinti çubuğunda **Kaydet** ' e tıklayın.
6. Üst gezinti çubuğunda **ağ yapılandırması Uygula** ' ya tıklayın.

> [!NOTE]
> API Management örneğinin VIP adresi, VNET 'in her etkin veya devre dışı bırakıldığı her seferinde değişecektir.
> API Management **dış** sunucudan **iç** veya tam tersi yönde taşındığında VIP adresi de değişir
>

> [!IMPORTANT]
> VNET 'ten API Management kaldırır veya içinde dağıtıldığı bir değişikliği değiştirirseniz, daha önce kullanılmış olan VNET, altı saate kadar kilitli kalabilir. Bu süre boyunca VNET 'i silmek veya yeni bir kaynak dağıtmak mümkün olmayacaktır. Bu davranış, API-sürüm 2018-01-01 ve öncesini kullanan istemciler için geçerlidir. API-sürüm 2019-01-01 ve üzeri kullanan istemciler, ilişkili API Management hizmeti silindikten hemen sonra sanal ağ serbest bırakılır.

## <a name="enable-vnet-powershell"> </a>PowerShell cmdlet 'LERINI kullanarak VNET bağlantısını etkinleştirme
Ayrıca, PowerShell cmdlet 'lerini kullanarak VNET bağlantısını etkinleştirebilirsiniz

* **VNET içinde API Management hizmeti oluşturma**: sanal ağ Içinde bir Azure API Management hizmeti oluşturmak için [New-Azapimana,](/powershell/module/az.apimanagement/new-azapimanagement) cmdlet 'ini kullanın.

* **Mevcut bir API Management HIZMETINI VNET Içinde dağıtma**: var olan bir Azure API Management hizmetini bir sanal ağ içinde taşımak için [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) cmdlet 'ini kullanın.

## <a name="connect-vnet"> </a>Sanal ağ içinde barındırılan bir Web hizmetine bağlanma
API Management hizmetiniz VNET 'e bağlandıktan sonra, içindeki arka uç hizmetlerine erişmek, ortak hizmetlere erişmeden farklı değildir. Yeni bir API oluştururken veya var olan bir API 'yi düzenlediğinizde, Web hizmetinizin Web hizmeti **URL 'si** ALANıNA yerel IP adresini veya ana bilgisayar adını (VNet IÇIN bir DNS sunucusu yapılandırılmışsa) yazmanız yeterlidir.

![VPN 'den API ekleme][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Ortak ağ yapılandırma sorunları
Aşağıda, API Management hizmeti bir sanal ağa dağıttığınızda oluşabilecek yaygın yanlış yapılandırma sorunlarının bir listesi verilmiştir.

* **Özel DNS sunucusu kurulumu**: API Management hizmeti, birkaç Azure hizmetine bağlıdır. API Management, özel bir DNS sunucusu olan bir VNET 'te barındırılıyorsa, bu Azure hizmetlerinin ana bilgisayar adlarını çözmesi gerekir. Lütfen özel DNS kurulumu için [Bu](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) yönergeleri izleyin. Başvuru için aşağıdaki bağlantı noktaları tablosuna ve diğer ağ gereksinimlerine bakın.

> [!IMPORTANT]
> VNET için özel bir DNS sunucusu kullanmayı planlıyorsanız, API Management bir hizmeti dağıtmaya **başlamadan önce** onu ayarlamanız gerekir. Aksi takdirde, [ağ yapılandırmasını Uygula işlemini](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/ApplyNetworkConfigurationUpdates) çalıştırarak DNS sunucusunu her değiştirişinizde API Management hizmetini güncelleştirmeniz gerekir

* **API Management için gereken bağlantı noktaları**: gelen ve giden trafik, API Management dağıtıldığı alt ağa [ağ güvenlik grubu][Network Security Group]kullanılarak denetlenebilir. Bu bağlantı noktalarından herhangi biri kullanılamıyorsa API Management düzgün çalışmayabilir ve erişilemez hale gelebilir. Bu bağlantı noktalarından biri veya daha fazlası engelleniyorsa, VNET ile API Management kullanırken başka bir yaygın yanlış yapılandırma sorunu vardır.

<a name="required-ports"></a> Bir API Management hizmet ÖRNEĞI bir sanal ağda barındırıldığı zaman, aşağıdaki tablodaki bağlantı noktaları kullanılır.

| Kaynak/hedef bağlantı noktaları | Yön          | Aktarım Protokolü |   [Hizmet etiketleri](../virtual-network/security-overview.md#service-tags) <br> Kaynak/hedef   | Amaç (*)                                                 | Sanal ağ türü |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Gelen            | TCP                | ıNTERNET/VIRTUAL_NETWORK            | API Management istemci iletişimi                      | Dış             |
| * / 3443                     | Gelen            | TCP                | Apimanaya/VIRTUAL_NETWORK       | Azure portal ve PowerShell için yönetim uç noktası         | Dış & Iç  |
| * / 80, 443                  | Giden           | TCP                | VIRTUAL_NETWORK/depolama             | **Azure Storage bağımlılığı**                             | Dış & Iç  |
| * / 80, 443                  | Giden           | TCP                | VIRTUAL_NETWORK/AzureActiveDirectory | Azure Active Directory (uygun olduğunda)                   | Dış & Iç  |
| * / 1433                     | Giden           | TCP                | VIRTUAL_NETWORK/SQL                 | **Azure SQL uç noktalarına erişim**                           | Dış & Iç  |
| */5671, 5672, 443          | Giden           | TCP                | VIRTUAL_NETWORK/EventHub            | Olay Hub 'ı ilkesine ve izleme aracısına günlük bağımlılığı | Dış & Iç  |
| * / 445                      | Giden           | TCP                | VIRTUAL_NETWORK/depolama             | GIT için Azure dosya paylaşımında bağımlılık                      | Dış & Iç  |
| */1886                     | Giden           | TCP                | VIRTUAL_NETWORK/INTERNET            | Kaynak Durumu sistem durumu yayımlamak için gereklidir          | Dış & Iç  |
| */443                     | Giden           | TCP                | VIRTUAL_NETWORK/AzureMonitor         | Tanılama günlüklerini ve ölçümlerini yayımlama                        | Dış & Iç  |
| */25                       | Giden           | TCP                | VIRTUAL_NETWORK/INTERNET            | E-posta göndermek için SMTP geçişine Bağlan                    | Dış & Iç  |
| */587                      | Giden           | TCP                | VIRTUAL_NETWORK/INTERNET            | E-posta göndermek için SMTP geçişine Bağlan                    | Dış & Iç  |
| * / 25028                    | Giden           | TCP                | VIRTUAL_NETWORK/INTERNET            | E-posta göndermek için SMTP geçişine Bağlan                    | Dış & Iç  |
| * / 6381 - 6383              | Gelen & giden | TCP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Roleınstances arasında Redsıs örnekleri için Azure önbelleğine erişin          | Dış & Iç  |
| * / *                        | Gelen            | TCP                | AZURE_LOAD_BALANCER/VIRTUAL_NETWORK | Azure altyapı Load Balancer                          | Dış & Iç  |

>[!IMPORTANT]
> API Management hizmetin başarıyla dağıtılması için , amacının **kalın** olduğu bağlantı noktaları gereklidir. Diğer bağlantı noktalarını engellemek, çalışan hizmeti kullanma ve izleme yeteneğinin azalmasına neden olur.

+ **SSL işlevselliği**: SSL sertifika zinciri oluşturma ve doğrulamayı etkinleştirmek için API Management hizmeti, ocsp.msocsp.com, mscrl.microsoft.com ve CRL.Microsoft.com giden ağ bağlantısı gerektirir. API Management karşıya yüklediğiniz herhangi bir sertifika, CA köküne tam zinciri içeriyorsa, bu bağımlılık gerekli değildir.

+ **DNS erişimi**: bağlantı noktası 53 üzerinde giden ERIŞIM, DNS sunucularıyla iletişim için gereklidir. Bir VPN ağ geçidinin diğer ucunda özel bir DNS sunucusu varsa, DNS sunucusuna API Management barındıran alt ağdan erişilebilir olması gerekir.

+ **Ölçümler ve sistem durumu izleme**: Azure izleme uç noktalarına giden ağ bağlantısı, aşağıdaki etki alanları altında çözümlenir:

    | Azure ortamı | Uç Noktalar                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure genel      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`. warm.ingestion.msftcloudes.com burada `East US 2` eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Kamu  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure Çin       | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **SMTP geçişi**: konak `smtpi-co1.msn.com`, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` ve `ies.global.microsoft.com` altında çözümlenen SMTP geçişi için giden ağ bağlantısı

+ **Geliştirici PORTALı CAPTCHA**: ana bilgisayar `client.hip.live.com` ve `partner.hip.live.com`altında çözümlenen geliştirici portalının CAPTCHA için giden ağ bağlantısı.

+ **Tanılama Azure Portal**: Azure Portal bir sanal ağ içinden API Management uzantısı kullanılırken tanılama günlüklerinin akışını etkinleştirmek için, 443 numaralı bağlantı noktası üzerinde `dc.services.visualstudio.com` giden erişim gerekir. Bu, uzantıyı kullanırken karşılaşabileceğiniz sorunları gidermeye yardımcı olur.

+ **Express Route veya ağ sanal gereci kullanarak tünel trafiğini şirket Içi güvenlik duvarına zorla**: ortak bir müşteri yapılandırması, API Management Temsilcili alt ağdan gelen tüm trafiği şirket içi güvenlik duvarı veya ağ sanal gereci üzerinden akışa zorlayan kendi varsayılan yolunu (0.0.0.0/0) tanımlamaktır. Giden trafik, şirket içi veya NAT 'ın, artık çeşitli Azure uç noktalarıyla çalışmayan tanınmayan bir adres kümesine engellediği için bu trafik akışı, Azure API Management ile bağlantıyı keser. Çözüm için birkaç şey yapmanız gerekir:

  * API Management hizmetinin dağıtıldığı alt ağda hizmet uç noktalarını etkinleştirin. Azure SQL, Azure depolama, Azure EventHub ve Azure ServiceBus için [hizmet uç noktalarının][ServiceEndpoints] etkinleştirilmesi gerekir. Uç noktaların bu hizmetlere doğrudan API Management atanmış alt ağdan etkinleştirilmesi, hizmet trafiği için en iyi yönlendirmeyi sağlayan Microsoft Azure omurga ağını kullanmasına izin verir. Hizmet uç noktalarını zorlamalı bir tünel oluşturma API Yönetimi ile birlikte kullanıyorsanız, yukarıdaki Azure hizmetleri trafiğine Zorlamalı tünel sağlanmaz. Diğer API Management hizmet bağımlılığı trafiği Zorlamalı tünel yapılabilir ve kaybedilmez veya API Management hizmeti düzgün şekilde çalışmaz.
    
  * Internet 'ten gelen tüm denetim düzlemi trafiği, API Management hizmetinizin yönetim uç noktasına, API Management tarafından barındırılan belirli bir gelen IP kümesiyle yönlendirilir. Trafiğe Zorlamalı tünel yapıldığında yanıtlar, bu gelen kaynak IP 'lerine simetrik bir şekilde geri eşleşmeyecektir. Sınırlamanın üstesinden gelmek için, bu konak yollarının hedefini "Internet" olarak ayarlayarak aşağıdaki Kullanıcı tanımlı yolları ([udrs][UDRs]) Azure 'a geri ekleyebilmemiz gerekir. Denetim düzlemi trafiği için gelen IP 'lerin kümesi aşağıdaki gibidir:
    
     | Azure ortamı | Yönetim IP adresleri                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure genel      | 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 52.159.16.255/32, 40.82.157.167/32, 51.137.136.0/32, 40.81.185.8/32, 40.81.47.216/32, 51.145.56.125/32, 40.81.89.24/32, 52.224.186.99/32, 51.145.179.78/32, 52.140.238.179/32, 40.66.60.111/32, 52.139.80.117/32, 20.46.144.85/32, 191.233.24.179/32, 40.90.185.46/32, 102.133.130.197/32, 52.139.20.34/32, 40.80.232.185/32, 13.71.49.1/32, 13.64.39.16/32, 20.40.160.107/32 20.37.52.67/32, 20.44.33.246/32, 13.86.102.66/32, 20.40.125.155/32, 51.143.127.203/32, 52.253.225.124/32, 52.253.159.160/32, 20.188.77.119/32, 20.44.72.3/32, 52.142.95.35/32, 52.139.152.27/32, 20.39.80.2/32, 51.107.96.8/32, 20.39.99.81/32, 20.37.81.41/32, 51.107.0.91/32, 102.133.0.79/32, 51.116.96.0/32, 51.116.0.0 |
    | Azure Kamu  | 52.127.42.160/32, 52.127.34.192/32 |
    | Azure Çin       | 139.217.51.16/32, 139.217.171.176/32 |

  * Zorlamalı tünel sağlayan diğer API Management hizmet bağımlılıkları için, ana bilgisayar adını çözümlemek ve uç noktaya ulaşmak için bir yol olmalıdır. Bunlar şunlardır
      - Ölçümler ve sistem durumu Izleme
      - Azure portal tanılama
      - SMTP geçişi
      - Geliştirici portalı CAPTCHA

## <a name="troubleshooting"> </a>Sorun giderme
* **Ilk kurulum**: API Management hizmetinin bir alt ağa ilk dağıtımı başarısız olduğunda, önce bir sanal makineyi aynı alt ağa dağıtmanız önerilir. Sanal makineye sonraki uzak masaüstü ve Azure aboneliğinizde aşağıdaki her bir kaynaktan birine bağlantı olduğunu doğrulama
    * Azure Depolama Blobu
    * Azure SQL Veritabanı
    * Azure depolama tablosu

  > [!IMPORTANT]
  > Bağlantıyı doğruladıktan sonra, alt ağa API Management dağıtılmadan önce alt ağda dağıtılan tüm kaynakları kaldırdığınızdan emin olun.

* **Artımlı güncelleştirmeler**: ağınızda değişiklik yaparken, API Management hizmetinin bağlı olduğu kritik kaynaklara erişimi kaybetmediğinden emin olmak Için [networkstatus API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/networkstatus)'sine bakın. Bağlantı durumu 15 dakikada bir güncelleştirilir.

* **Kaynak Gezinti bağlantıları**: Kaynak Yöneticisi Style VNET alt ağına dağıtım yaparken, API Management bir kaynak gezinti bağlantısı oluşturarak alt ağı ayırır. Alt ağ, farklı bir sağlayıcıdan bir kaynağı zaten içeriyorsa, dağıtım **başarısız**olur. Benzer şekilde, bir API Management hizmetini farklı bir alt ağa taşıdığınızda veya sildiğinizde, bu kaynak gezinti bağlantısını kaldıracağız.

## <a name="subnet-size"></a> Alt ağ boyutu gereksinimi
Azure, bazı IP adreslerini her alt ağ içinde ayırır ve bu adresler kullanılamaz. Alt ağların ilk ve son IP adresleri protokol uyumu için ayrılmıştır ve Azure hizmetleri için kullanılan üç adres daha vardır. Daha fazla bilgi için bkz. [Bu alt AĞLARDAKI IP adreslerini kullanma konusunda herhangi bir kısıtlama var mı?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Azure VNET altyapısı tarafından kullanılan IP adreslerine ek olarak, alt ağdaki her API Yönetimi örneği, geliştirici SKU 'su için her bir Premium SKU veya bir IP adresi birimi başına iki IP adresi kullanır. Her örnek, dış yük dengeleyici için ek bir IP adresi ayırır. Iç VNET 'e dağıtım yaparken, iç yük dengeleyici için ek bir IP adresi gerektirir.

API Management dağıtılacağı, alt ağın en düşük boyutunun üzerinde hesaplama verilme ve bu, üç IP adresi sağlayan/29 ' dır.

## <a name="routing"></a> Yönlendirme
+ Tüm hizmet uç noktalarına erişim sağlamak için, yük dengeli bir genel IP adresi (VIP) ayrılır.
+ VNET içindeki kaynaklara erişmek için bir alt ağ IP aralığından (DIP) bir IP adresi kullanılır ve VNET dışındaki kaynaklara erişmek için bir genel IP adresi (VIP) kullanılır.
+ Yük dengeli genel IP adresi, Azure portal genel bakış/Essentials dikey penceresinde bulunabilir.

## <a name="limitations"> </a>Sınırlamalar
* API Management örnekleri içeren bir alt ağ başka hiçbir Azure Kaynak türünü içeremez.
* Alt ağ ve API Management hizmeti aynı abonelikte olmalıdır.
* API Management örnekleri içeren bir alt ağ abonelikler arasında taşınamaz.
* Dahili sanal ağ modunda yapılandırılan çok bölgeli API Management dağıtımlarda, kullanıcılar, yönlendirmeye sahip oldukları gibi birden çok bölgede yük dengelemeyi yönetmekten sorumludur.
* Başka bir bölgedeki genel olarak eşlenmiş VNET 'teki bir kaynağın Iç moddaki API Management hizmete bağlantısı, platform sınırlaması nedeniyle çalışmayacak. Daha fazla bilgi için bkz. [bir sanal ağdaki kaynaklar, eşlenen sanal ağdaki Azure iç yük dengeleyici ile iletişim](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) kuramıyor


## <a name="related-content"> </a>İlgili içerik
* [VPN ağ geçidi kullanarak bir sanal ağı arka uca bağlama](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Farklı dağıtım modellerinden bir sanal ağ bağlama](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Azure API Management çağrıları izlemek için API denetçisini kullanma](api-management-howto-api-inspector.md)
* [Sanal ağ hakkında SSS](../virtual-network/virtual-networks-faq.md)
* [Hizmet etiketleri](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
