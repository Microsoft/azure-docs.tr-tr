---
title: ISE ile Azure sanal ağlarına bağlanma
description: Azure Logic Apps Azure sanal ağlarına (VNet) erişebilen bir tümleştirme hizmeti ortamı (ıSE) oluşturun
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: bfef9f2b5420ac9377cc369d7bf9a9bdac76743b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874234"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Tümleştirme hizmeti ortamını (ISE) kullanarak Azure Logic Apps'ten Azure sanal ağlarına bağlanma

Mantıksal uygulamalarınızın ve tümleştirme hesaplarınızın bir [Azure sanal ağına](../virtual-network/virtual-networks-overview.md)erişmesi gereken senaryolarda, bir [ *tümleştirme hizmeti ortamı* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)oluşturun. ISE, "küresel" çok kiracılı Logic Apps hizmetinden ayrı tutulan, ayrılmış depolama alanının ve diğer kaynakların kullanıldığı ayrılmış bir ortamdır. Bu ayrım Ayrıca diğer Azure kiracılarının uygulamalarınızın performansı üzerinde sahip olabileceği etkileri azaltır. ISE, size kendi statik IP adreslerinizi de sağlar. Bu IP adresleri, genel, çok kiracılı hizmette Logic Apps tarafından paylaşılan statik IP adreslerinden ayrıdır.

Bir ıSE oluşturduğunuzda, Azure sanal ağınıza ait olan ve Logic Apps hizmetini sanal ağınıza dağıtan Azure sanal ağınız üzerinde yer alan Azure 'u sanal ağınıza *çıkartır* . Bir mantıksal uygulama veya tümleştirme hesabı oluşturduğunuzda, çalışma alanınızı konum olarak seçin. Mantıksal uygulamanız veya tümleştirme hesabınız, sanal ağınızdaki sanal makineler (VM 'Ler), sunucular, sistemler ve hizmetler gibi kaynaklara doğrudan erişim sağlayabilir.

![Tümleştirme hizmeti ortamını seçin](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Mantıksal uygulamalar ve tümleştirme hesaplarının bir ıSE 'de birlikte çalışması için, her ikisi de konumuyla *aynı Ise* 'yı kullanmalıdır.

Bir ıSE çalışma süresi, depolama tutma, aktarım hızı, HTTP isteği ve yanıt zaman aşımları, ileti boyutları ve özel bağlayıcı istekleri için sınırları artmıştır. Daha fazla bilgi için bkz. [Azure Logic Apps Için sınırlar ve yapılandırma](../logic-apps/logic-apps-limits-and-config.md). Sesleri hakkında daha fazla bilgi edinmek için bkz. [Azure Logic Apps Azure sanal ağ kaynaklarına erişim](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Bu makalede, Azure portal kullanarak bu görevlerin nasıl tamamlanacağı gösterilmektedir:

* ISE için erişimi etkinleştirin.
* ISE 'nizi oluşturun.
* ISE 'nize ekstra kapasite ekleyin.

Ayrıca, [örnek Azure Resource Manager hızlı başlangıç şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) kullanarak veya müşteri tarafından yönetilen anahtarları ayarlama dahil Logic Apps REST API kullanarak da bır Ise oluşturabilirsiniz:

* [Logic Apps REST API'sini kullanarak tümleştirme hizmeti ortamı (ISE) oluşturma](../logic-apps/create-integration-service-environment-rest-api.md)
* [Rest için bekleyen verileri şifrelemek üzere müşteri tarafından yönetilen anahtarlar ayarlama](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure hesabı ve aboneliği Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Logic Apps, yerleşik Tetikleyiciler, yerleşik Eylemler ve ıSE 'de çalışan bağlayıcılar, tüketim tabanlı fiyatlandırma planından farklı bir fiyatlandırma planı kullanır. Fiyatlandırma ve faturalandırma işinin nasıl sesleri olduğunu öğrenmek için [Logic Apps fiyatlandırma modeline](../logic-apps/logic-apps-pricing.md#fixed-pricing)bakın. Fiyatlandırma fiyatları için bkz. [Logic Apps fiyatlandırması](../logic-apps/logic-apps-pricing.md).

* Her yerde kaynak oluşturup dağıtmak için gereken ve bu iç ve gizli bileşenler tarafından kullanılan dört *boş* alt ağa sahip bir [Azure sanal ağı](../virtual-network/virtual-networks-overview.md) :

  * Logic Apps Işlem
  * İç App Service Ortamı (bağlayıcılar)
  * İç API Management (bağlayıcılar)
  * Önbelleğe alma ve performans için iç redin
  
  Alt ağları önceden oluşturabilirsiniz, böylece alt ağları aynı anda oluşturabilmeniz için ıSE 'yi oluşturun. Ancak, alt ağlarınızı oluşturmadan önce, [alt ağ gereksinimlerini](#create-subnet)gözden geçirdiğinizden emin olun.

  * Sanal ağınızın, ıSE 'nin doğru çalışabilmesi ve erişilebilir kalabilmesi [IÇIN Ise için erişim sağladığından](#enable-access) emin olun.

  * Ya da [Zorlamalı tünel](../firewall/forced-tunneling.md)Ile [ExpressRoute](../expressroute/expressroute-introduction.md) 'u kullanmak istiyorsanız, aşağıdaki belirli yolu içeren [bir yol tablosu oluşturmanız](../virtual-network/manage-route-table.md) ve yol tablosunu Ise tarafından kullanılan her alt ağa bağlamanız gerekir:

    **Ad**: <*yol adı*><br>
    **Adres ön eki**: 0.0.0.0/0<br>
    **Sonraki durak**: Internet
    
    Bu belirli yol tablosu, Logic Apps bileşenlerinin Azure depolama ve Azure SQL VERITABANı gibi diğer bağımlı Azure hizmetleriyle iletişim kurabilmesi için gereklidir. Bu yol hakkında daha fazla bilgi için bkz. [0.0.0.0/0 adres ön eki](../virtual-network/virtual-networks-udr-overview.md#default-route). ExpressRoute ile Zorlamalı tünel kullanmıyorsanız, bu özel yol tablosuna gerek kalmaz.
    
    ExpressRoute, şirket içi ağlarınızı Microsoft bulutuna genişletmenizi ve bağlantı sağlayıcısı tarafından kolaylaştırmaya yönelik özel bir bağlantı üzerinden Microsoft bulut hizmetlerine bağlanmanızı sağlar. ExpressRoute özellikle, trafiği genel İnternet üzerinden değil, özel bir ağ üzerinden yönlendiren bir sanal özel ağ. Mantıksal uygulamalarınız, ExpressRoute veya bir sanal özel ağ aracılığıyla bağlandıklarında aynı sanal ağdaki şirket içi kaynaklara bağlanabilir.
   
  * Bir [ağ sanal gereci (NVA)](../virtual-network/virtual-networks-udr-overview.md#user-defined)KULLANıYORSANıZ, TLS/SSL sonlandırmasını etkinleştirmeyin veya giden TLS/SSL trafiğini değiştiremezsiniz. Ayrıca, ıSE 'nin alt ağından kaynaklanan trafik için incelemeyi etkinleştirdiğinizden emin olun. Daha fazla bilgi için bkz. [sanal ağ trafiği yönlendirme](../virtual-network/virtual-networks-udr-overview.md).

  * Azure sanal ağınız için özel DNS sunucuları kullanmak istiyorsanız, ıSE 'nizi sanal ağınıza dağıtmadan önce [Bu adımları izleyerek bu sunucuları ayarlayın](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) . DNS sunucusu ayarlarını yönetme hakkında daha fazla bilgi için bkz. [sanal ağ oluşturma, değiştirme veya silme](../virtual-network/manage-virtual-network.md#change-dns-servers).

    > [!NOTE]
    > DNS sunucunuzu veya DNS sunucusu ayarlarını değiştirirseniz, ıSE 'yi bu değişiklikleri çekebilecek şekilde yeniden başlatmanız gerekir. Daha fazla bilgi için bkz. [Ise 'Nizi yeniden başlatma](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>ISE erişimini etkinleştirin

Bir Azure sanal ağı ile bir ıSE kullandığınızda, yaygın bir kurulum sorunu bir veya daha fazla engellenen bağlantı noktasına sahiptir. ISE ve hedef sistemleriniz arasında bağlantı oluşturmak için kullandığınız bağlayıcılar kendi bağlantı noktası gereksinimlerine da sahip olabilir. Örneğin, FTP bağlayıcısını kullanarak bir FTP sistemiyle iletişim kurdıysanız, FTP sisteminizde kullandığınız bağlantı noktasının kullanılabilir olması gerekir, örneğin, komut göndermek için bağlantı noktası 21.

ISE 'nizin erişilebilir olduğundan ve bu ıSE 'deki mantıksal uygulamaların sanal ağınızdaki her alt ağ üzerinde iletişim kurabildiğinden emin olmak için, [her alt ağ için bu tabloda açıklanan bağlantı noktalarını açın](#network-ports-for-ise). Gerekli bağlantı noktaları kullanılamıyorsa, ıSE 'niz doğru çalışmaz.

* IP kısıtlamalarına sahip diğer uç noktalara erişmesi gereken birden çok ıSE örneğiniz varsa, sanal ağınıza bir [Azure Güvenlik Duvarı](../firewall/overview.md) veya [ağ sanal](../virtual-network/virtual-networks-overview.md#filter-network-traffic) gereci dağıtın ve giden trafiği bu güvenlik duvarı veya ağ sanal gereci üzerinden yönlendirin. Daha sonra, sanal ağınızdaki tüm ıSE örneklerinin hedef sistemlerle iletişim kurmak için kullanabileceği [tek, giden, genel, statik ve öngörülebilir BIR IP adresi ayarlayabilirsiniz](connect-virtual-network-vnet-set-up-single-ip-address.md) . Bu şekilde, her bir ıSE için bu hedef sistemlerde ek güvenlik duvarı boş olanları ayarlamanız gerekmez.

   > [!NOTE]
   > Bu yaklaşımı, senaryolarınız erişmesi gereken IP adresi sayısını sınırlandırmaya gerektirdiğinde tek bir ıSE için kullanabilirsiniz. Güvenlik duvarı veya sanal ağ gereci için ek maliyetlerin senaryonuz için anlamlı olup olmadığını göz önünde bulundurun. [Azure Güvenlik Duvarı fiyatlandırması](https://azure.microsoft.com/pricing/details/azure-firewall/)hakkında daha fazla bilgi edinin.

* Herhangi bir kısıtlama olmadan yeni bir Azure sanal ağı ve alt ağı oluşturduysanız, alt ağlardaki trafiği denetlemek için sanal ağınızda [ağ güvenlik grupları (NSG 'ler)](../virtual-network/network-security-groups-overview.md#network-security-groups) ayarlamanıza gerek yoktur.

* Mevcut bir sanal ağ için, *isteğe bağlı olarak* ağ [trafiğini filtrelemek](../virtual-network/tutorial-filter-network-traffic.md)için [ağ güvenlik grupları (NSG 'ler)](../virtual-network/network-security-groups-overview.md#network-security-groups) ayarlayabilirsiniz. Bu rotaya gitmek istiyorsanız veya zaten NSG 'ler kullanıyorsanız, [Bu tabloda açıklanan bağlantı noktalarını](#network-ports-for-ise) bu NSG 'ler için açtığınız emin olun.

  [NSG güvenlik kurallarını](../virtual-network/network-security-groups-overview.md#security-rules)ayarlarken, hem **TCP** hem *de* **UDP** protokollerini kullanmanız gerekir, aksi **durumda her protokol** için ayrı kurallar oluşturmanız gerekmez. NSG güvenlik kuralları, bu bağlantı noktalarına erişmesi gereken IP adresleri için açmanız gereken bağlantı noktalarını anlatmaktadır. Bu uç noktalar arasındaki tüm güvenlik duvarlarının, yönlendiricilerin veya diğer öğelerin bu IP adresleriyle erişilebilir olan bağlantı noktalarını da tutduğundan emin olun.

* Internet 'e ait trafiği yeniden yönlendirmek için güvenlik duvarınız aracılığıyla Zorlamalı tünel ayarlarsanız, [Zorlamalı tünel gereksinimlerini](#forced-tunneling)gözden geçirin.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>ISE'niz tarafından kullanılan ağ bağlantı noktaları

Bu tabloda, ıSE 'nizin erişilebilir olması ve bu bağlantı noktalarının amacı açıklanmaktadır. Güvenlik kurallarını ayarlarken karmaşıklığı azaltmaya yardımcı olmak için, tablo belirli bir Azure hizmeti için IP adresi ön eklerinin gruplarını temsil eden [hizmet etiketlerini](../virtual-network/service-tags-overview.md) kullanır. Belirtildiği yerde, *Iç Ise* ve *dış ıSE* , [Ise oluşturma sırasında seçilen erişim uç noktasına](connect-virtual-network-vnet-isolated-environment.md#create-environment)başvurur. Daha fazla bilgi için bkz. [uç nokta erişimi](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access).

> [!IMPORTANT]
> Tüm kurallar için kaynak bağlantı `*` noktaları kısa ömürlü olduğundan kaynak bağlantı noktalarını ayarladığınızdan emin olun.

#### <a name="inbound-security-rules"></a>Gelen güvenlik kuralları

| Amaç | Kaynak hizmet etiketi veya IP adresleri | Kaynak bağlantı noktaları | Hedef hizmet etiketi veya IP adresleri | Hedef bağlantı noktaları | Notlar |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Sanal ağ içinde ıntersubnet iletişimi | ISE alt ağları ile sanal ağın adres alanı | * | ISE alt ağları ile sanal ağın adres alanı | * | Sanal ağınızdaki alt ağlar *arasında* akış için gereken trafik. <p><p>**Önemli**: her bir alt ağdaki *Bileşenler* arasında akış yapılacak trafik için, her alt ağ içinde tüm bağlantı noktalarını açtığınızdan emin olun. |
| İs <p>Mantıksal uygulamanıza yönelik iletişim <p><p>Mantıksal uygulama için geçmişi çalıştırır| İç ıSE: <br>**VirtualNetwork** <p><p>Dış ıSE: **Internet** veya **notlara** bakın | * | **VirtualNetwork** | 443 | **Internet** Service etiketini kullanmak yerine, bu ÖĞELERIN kaynak IP adresini belirtebilirsiniz: <p><p>-Mantıksal uygulamanızda herhangi bir istek tetikleyicisi veya Web kancası çağıran bilgisayar veya hizmet <p>-Mantıksal uygulama çalıştırma geçmişine erişmek istediğiniz bilgisayar veya hizmet <p><p>**Önemli**: Bu bağlantı noktasını kapatmak veya engellemek, istek Tetikleyicileri veya Web kancaları olan Logic Apps çağrılarını engeller. Ayrıca, çalışma geçmişinde her adımın giriş ve çıkışlara erişmenizi de engellemiş olursunuz. Ancak, mantıksal uygulama çalıştırmaları geçmişine erişim engellenmiyor.|
| Logic Apps Designer-Dynamic özellikleri | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | İstekler, bu bölge için Logic Apps erişim uç noktasının [gelen IP adreslerinden](../logic-apps/logic-apps-limits-and-config.md#inbound) gelir. <p><p>**Önemli**: Azure Kamu bulutu ile çalışıyorsanız, **Logicappsmanagement** hizmet etiketi çalışmaz. Bunun yerine, Azure Kamu için Logic Apps [gelen IP adreslerini](../logic-apps/logic-apps-limits-and-config.md#azure-government-inbound) sağlamanız gerekir. |
| Ağ durumu denetimi | **LogicApps** | * | **VirtualNetwork** | 454 | İstekler, bu bölge için Logic Apps erişim uç noktasının [gelen IP adreslerinden](../logic-apps/logic-apps-limits-and-config.md#inbound) ve [giden IP adreslerinden](../logic-apps/logic-apps-limits-and-config.md#outbound) gelir. <p><p>**Önemli**: Azure Kamu bulutu ile çalışıyorsanız, **logicapps** hizmet etiketi çalışmaz. Bunun yerine, Azure Kamu için hem Logic Apps [gelen IP adreslerini](../logic-apps/logic-apps-limits-and-config.md#azure-government-inbound) hem de [giden IP adreslerini](../logic-apps/logic-apps-limits-and-config.md#azure-government-outbound) sağlamanız gerekir. |
| Bağlayıcı dağıtımı | **AzureConnectors** | * | **VirtualNetwork** | 454 | Bağlayıcıları dağıtmak ve güncelleştirmek için gereklidir. Bu bağlantı noktasını kapatmak veya engellemek, ıSE dağıtımlarının başarısız olmasına neden olur ve bağlayıcı güncelleştirmelerini ve düzeltmelerini engeller. <p><p>**Önemli**: Azure Kamu bulutu ile çalışıyorsanız, **AzureConnectors** Service etiketi çalışmaz. Bunun yerine, Azure Kamu için [yönetilen bağlayıcı gıden IP adreslerini](../logic-apps/logic-apps-limits-and-config.md#azure-government-outbound) sağlamanız gerekir. |
| App Service yönetimi bağımlılığı | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Azure Traffic Manager iletişimi | **AzureTrafficManager** | * | **VirtualNetwork** | İç ıSE: 454 <p><p>Dış ıSE: 443 ||
| İs <p>Bağlayıcı İlkesi dağıtımı <p>API Management yönetim uç noktası | **APIManagement** | * | **VirtualNetwork** | 3443 | Bağlayıcı İlkesi dağıtımı için, bağlayıcıları dağıtmak ve güncelleştirmek için bağlantı noktası erişimi gereklidir. Bu bağlantı noktasını kapatmak veya engellemek, ıSE dağıtımlarının başarısız olmasına neden olur ve bağlayıcı güncelleştirmelerini ve düzeltmelerini engeller. |
| Rol örnekleri arasında Redsıs örnekleri için Azure önbelleğine erişin | **VirtualNetwork** | * | **VirtualNetwork** | 6379-6383, ve **notlara** bakın| ISE 'nin Redsıs için Azure Cache ile çalışması için, [REDSıS SSS Için Azure önbelleği tarafından tanımlanan bu giden ve gelen bağlantı noktalarını](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)açmanız gerekir. |
|||||||

#### <a name="outbound-security-rules"></a>Giden güvenlik kuralları

| Amaç | Kaynak hizmet etiketi veya IP adresleri | Kaynak bağlantı noktaları | Hedef hizmet etiketi veya IP adresleri | Hedef bağlantı noktaları | Notlar |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Sanal ağ içinde ıntersubnet iletişimi | ISE alt ağları ile sanal ağın adres alanı | * | ISE alt ağları ile sanal ağın adres alanı | * | Sanal ağınızdaki alt ağlar *arasında* akış için gereken trafik. <p><p>**Önemli**: her bir alt ağdaki *Bileşenler* arasında akış yapılacak trafik için, her alt ağ içinde tüm bağlantı noktalarını açtığınızdan emin olun. |
| Mantıksal uygulamanızdan iletişim | **VirtualNetwork** | * | Hedefe göre farklılık gösterir | Hedefe göre farklılık gösterir | Hedef bağlantı noktaları, mantıksal uygulamanızın iletişim kurması gereken dış hizmetlerin uç noktalarına bağlı olarak farklılık gösterir. <p><p>Örneğin, hedef bağlantı noktası bir Web hizmeti için 443, bir SMTP hizmeti için bağlantı noktası 25, bir SFTP hizmeti için bağlantı noktası 22 ve bu şekilde devam eder. |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80, 443 ||
| Azure depolama bağımlılığı | **VirtualNetwork** | * | **Depolama** | 80, 443, 445 ||
| Bağlantı yönetimi | **VirtualNetwork** | * | **AppService** | 443 ||
| Ölçümleri & tanılama günlüklerini yayımlayın | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Azure SQL bağımlılığı | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Kaynak Durumu | **VirtualNetwork** | * | **AzureMonitor** | 1886 | Kaynak Durumu sistem durumu yayımlama için gereklidir. |
| Günlüğe Olay Hub 'ı ilke ve izleme aracısına bağımlılık | **VirtualNetwork** | * | **EventHub** | 5672 ||
| Rol örnekleri arasında Redsıs örnekleri için Azure önbelleğine erişin | **VirtualNetwork** | * | **VirtualNetwork** | 6379-6383, ve **notlara** bakın| ISE 'nin Redsıs için Azure Cache ile çalışması için, [REDSıS SSS Için Azure önbelleği tarafından tanımlanan bu giden ve gelen bağlantı noktalarını](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)açmanız gerekir. |
| DNS ad çözümlemesi | **VirtualNetwork** | * | Sanal ağınızdaki tüm özel etki alanı adı sistemi (DNS) sunucuları için IP adresleri | 53 | Yalnızca sanal ağınızda özel DNS sunucuları kullandığınızda gereklidir |
|||||||

Ayrıca, [App Service ortamı (Ao)](../app-service/environment/intro.md)için giden kuralları eklemeniz gerekir:

* Azure Güvenlik duvarı kullanıyorsanız, asa platformu trafiğine giden erişime izin veren App Service Ortamı (ASA) [tam etki alanı adı (FQDN) etiketiyle](../firewall/fqdn-tags.md#current-fqdn-tags)güvenlik duvarınızı ayarlamanız gerekir.

* Azure Güvenlik Duvarı dışında bir güvenlik duvarı gereci kullanıyorsanız, güvenlik duvarınızı App Service Ortamı için gereken [Güvenlik Duvarı Tümleştirme bağımlılıklarında](../app-service/environment/firewall-integration.md#dependencies) listelenen *Tüm* kurallarla ayarlamanız gerekir.

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>Zorlamalı tünel gereksinimleri

Güvenlik duvarınız aracılığıyla [Zorlamalı tünel](../firewall/forced-tunneling.md) ayarlarsanız veya kullanıyorsanız, Ise 'niz için ek dış bağımlılıklara izin vermeniz gerekir. Zorlamalı tünel, giden ağ trafiğini incelemenize ve denetlemenize olanak sağlamak için internet 'e göre, sanal özel ağınız (VPN) veya bir Sanal Gereç gibi belirli bir sonraki atlama için Internet 'e bağlanan trafiği yeniden yönlendirmenizi sağlar.

Bu bağımlılıklar için erişime izin vermezseniz, ıSE dağıtımınız başarısız olur ve dağıtılan ıSE çalışmanız çalışmayı durduruyor.

* Kullanıcı tanımlı yollar

  Asimetrik yönlendirmeyi engellemek için, sonraki atlama olarak **Internet** ile aşağıda LISTELENEN her IP adresi için bir yol tanımlamanız gerekir.
  
  * [Yönetim adreslerini App Service Ortamı](../app-service/environment/management-addresses.md)
  * [Bu indirme dosyasında bulunan ıSE bölgesindeki bağlayıcılar için Azure IP adresleri](https://www.microsoft.com/download/details.aspx?id=56519)
  * [Azure Traffic Manager yönetim adresleri](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)
  * [ISE bölgesinin gelen ve giden adreslerini Logic Apps](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)
  * [Bu indirme dosyasındaki ıSE bölgesindeki bağlayıcılar için Azure IP adresleri](https://www.microsoft.com/download/details.aspx?id=56519)

* Hizmet uç noktaları

  Bu hizmetlere bir güvenlik duvarı üzerinden trafik gönderemediğinden Azure SQL, Storage, Service Bus, Keykasası ve Event Hubs için hizmet uç noktalarını etkinleştirmeniz gerekir.

*  Diğer gelen ve giden bağımlılıklar

   Güvenlik duvarınız aşağıdaki gelen ve giden bağımlılıklara izin *vermelidir* :
   
   * [Azure App Service bağımlılıklar](../app-service/environment/firewall-integration.md#deploying-your-ase-behind-a-firewall)
   * [Azure önbellek hizmeti bağımlılıkları](../azure-cache-for-redis/cache-how-to-premium-vnet.md#what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks)
   * [Azure API Management bağımlılıkları](../api-management/api-management-using-with-vnet.md#-common-network-configuration-issues)

<a name="create-environment"></a>

## <a name="create-your-ise"></a>ISE'nizi oluşturun

1. [Azure Portal](https://portal.azure.com), ana Azure Arama kutusuna `integration service environments` filtreniz olarak girin ve **tümleştirme hizmeti ortamları**' nı seçin.

   !["Tümleştirme hizmeti ortamları" nı bulun ve seçin](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. **Tümleştirme hizmeti ortamları** bölmesinde **Ekle**' yi seçin.

   ![Tümleştirme hizmeti ortamı oluşturmak için "Ekle" yi seçin](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Ortamınız için bu ayrıntıları girin ve ardından **gözden geçir + oluştur**' u seçin. Örneğin:

   ![Ortam ayrıntılarını sağlama](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Özellik | Gerekli | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Abonelik** | Yes | <*Azure-abonelik-adı*> | Ortamınız için kullanılacak Azure aboneliği |
   | **Kaynak grubu** | Yes | <*Azure-Resource-Group-Name*> | Ortamınızı oluşturmak istediğiniz yeni veya mevcut bir Azure Kaynak grubu |
   | **Tümleştirme hizmeti ortam adı** | Yes | <*ortam-adı*> | Yalnızca harf, sayı, kısa çizgi ( `-` ), alt çizgi () `_` ve nokta () içerebilen Ise adınız `.` . |
   | **Konum** | Yes | <*Azure-Datacenter-Region*> | Ortamınızı dağıtacağınız Azure veri merkezi bölgesi |
   | **SKU** | Yes | **Premium** veya **Geliştirici (SLA yok)** | Oluşturulacak ve kullanılacak ıSE SKU 'SU. Bu SKU 'Lar arasındaki farklar için bkz. [Ise SKU 'ları](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Önemli**: Bu seçenek yalnızca Ise oluşturma sırasında kullanılabilir ve daha sonra değiştirilemez. |
   | **Ek kapasite** | Premium: <br>Yes <p><p>Tasarımcı <br>Uygulanamaz | Premium: <br>0 ila 10 <p><p>Tasarımcı <br>Uygulanamaz | Bu ıSE kaynağı için kullanılacak ek işleme birimi sayısı. Oluşturulduktan sonra kapasite eklemek için, bkz. [Ise kapasitesi ekleme](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Erişim uç noktası** | Yes | **İç** veya **dış** | ISE için kullanılacak erişim uç noktalarının türü. Bu uç noktalar, işinizdeki Logic Apps 'teki istek veya Web kancasının, sanal ağınızın dışından çağrı alıp almamadığını belirtir. <p><p>Örneğin, aşağıdaki Web kancası tabanlı Tetikleyicileri kullanmak istiyorsanız, **dış** öğesini seçtiğinizden emin olun: <p><p>-Azure DevOps <br>-Azure Event Grid <br>-Common Data Service <br>-Office 365 <br>-SAP (ıSE sürümü) <p><p>Seçiminiz, mantıksal uygulama çalışma geçmişinizdeki girdileri ve çıkışları görüntüleme ve erişme şeklini de etkiler. Daha fazla bilgi için bkz. [Ise uç noktası erişimi](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Önemli**: yalnızca Ise oluşturma sırasında erişim uç noktasını seçebilir ve bu seçeneği daha sonra değiştiremezsiniz. |
   | **Sanal ağ** | Yes | <*Azure-sanal-ağ-adı*> | Ortamınızı eklemek istediğiniz Azure sanal ağı, bu ortamdaki Logic Apps 'in sanal ağınıza erişebilmesi için kullanabilirsiniz. Ağınız yoksa, [önce bir Azure sanal ağı oluşturun](../virtual-network/quick-create-portal.md). <p><p>**Önemli**: Bu ekleme işlemini *yalnızca* Ise 'nizi oluştururken gerçekleştirebilirsiniz. |
   | **Alt ağlar** | Yes | <*alt ağ-kaynak listesi*> | ISE, ortamınızda kaynak oluşturup dağıtmak için gereken dört *boş* alt ağ gerektirir ve bağlayıcılar ve performans için önbelleğe alma gibi dahili Logic Apps bileşenleri tarafından kullanılır. <p>**Önemli**: alt [ağlarınızı oluşturmak için bu adımlarla devam etmeden önce alt ağ gereksinimlerini gözden geçirdiğinizden](#create-subnet)emin olun. |
   |||||

   <a name="create-subnet"></a>

   **Alt ağ oluşturma**

   ISE, her ne kadar kaynak oluşturup dağıtmak için gereken dört *boş* alt ağ gerektirir ve bağlayıcılar ve performans için önbelleğe alma gibi dahili Logic Apps bileşenleri tarafından kullanılır. Ortamınızı oluşturduktan sonra bu alt ağ *adreslerini değiştiremezsiniz.* Azure portal aracılığıyla ıSE 'yi oluşturup dağıtırsanız, bu alt ağları herhangi bir Azure hizmetine temsilcdiğinizden emin olun. Ancak, REST API, Azure PowerShell veya bir Azure Resource Manager şablonu aracılığıyla ıSE 'yi oluşturup dağıtırsanız, bir boş alt ağın ' e [atamasını](../virtual-network/manage-subnet-delegation.md) yapmanız gerekir `Microsoft.integrationServiceEnvironment` . Daha fazla bilgi için bkz. [alt ağ temsili ekleme](../virtual-network/manage-subnet-delegation.md).

   Her alt ağın bu gereksinimleri karşılaması gerekir:

   * Alfabetik bir karakter veya alt çizgi (sayı yok) ile başlayan bir ad kullanır ve şu karakterleri kullanmaz: `<` , `>` ,, `%` `&` , `\\` , `?` , `/` .

   * [Sınıfsız Inter-Domain yönlendirme (CIDR) biçimini](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)kullanır.
   
     > [!IMPORTANT]
     >
     > Azure Logic Apps tarafından çözümlenemediğinden, sanal ağınız veya alt ağlarınız için aşağıdaki IP adresi alanlarını kullanmayın:<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * `/27`Her alt ağ 32 adresi gerektirdiğinden adres alanındaki bir kullanır. Örneğin, `10.0.0.0/27` 2<sup>(32-27)</sup> 2<sup>5</sup> veya 32 olduğundan 32 adresi vardır. Daha fazla adres daha fazla avantaj sağlamaz. Adresleri hesaplama hakkında daha fazla bilgi edinmek için bkz. [ıPV4 CIDR blokları](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * [ExpressRoute](../expressroute/expressroute-introduction.md)kullanırsanız, aşağıdaki rotayı içeren [bir yol tablosu oluşturmanız](../virtual-network/manage-route-table.md) ve bu tabloyu, Ise tarafından kullanılan her alt ağ ile bağlamanız gerekir:

     **Ad**: <*yol adı*><br>
     **Adres ön eki**: 0.0.0.0/0<br>
     **Sonraki durak**: Internet

   1. **Alt ağlar** listesinde, **alt ağ yapılandırmasını Yönet**' i seçin.

      ![Alt ağ yapılandırmasını yönetme](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. **Alt ağlar** bölmesinde **alt ağ**' ı seçin.

      ![Dört boş alt ağ ekleyin](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. **Alt ağ ekle** bölmesinde, bu bilgileri sağlayın.

      * **Ad**: alt ağınızın adı
      * **Adres aralığı (CIDR bloğu)**: alt ağınızın sanal AĞıNıZDA ve CIDR biçimindeki aralığı

      ![Alt ağ ayrıntıları ekleme](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. İşiniz bittiğinde **Tamam**’ı seçin.

   1. Üç alt ağ için bu adımları tekrarlayın.

      > [!NOTE]
      > Oluşturmayı denerseniz alt ağlar geçerli değilse, Azure portal bir ileti gösterir, ancak ilerleme durumunu engellemez.

   Alt ağlar oluşturma hakkında daha fazla bilgi için bkz. [sanal ağ alt ağı ekleme](../virtual-network/virtual-network-manage-subnet.md).

1. Azure, ıSE bilgilerinizi başarıyla doğruladıktan sonra **Oluştur**' u seçin, örneğin:

   ![Doğrulama başarılı olduktan sonra "Oluştur" u seçin](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure, ortamınızı dağıtmaya başlar ve genellikle iki saat içinde tamamlanır. Bazen dağıtım dört saate kadar sürebilir. Dağıtım durumunu denetlemek için, Azure araç çubuğınızdan bildirimler bölmesini açan Bildirimler simgesini seçin.

   ![Dağıtım durumunu denetle](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Dağıtım başarıyla tamamlandığında, Azure bu bildirimi gösterir:

   ![Dağıtım başarılı oldu](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Aksi takdirde, dağıtım sorunlarını gidermeye yönelik Azure portal yönergeleri izleyin.

   > [!NOTE]
   > Dağıtım başarısız olursa veya ıSE 'yi silerseniz Azure, alt ağlarınızı serbest bırakmadan önce bir saat veya daha uzun bir durumda olabilir. Bu nedenle, başka bir ıSE 'de bu alt ağları yeniden kullanabilmeniz için beklemeniz gerekebilir.
   >
   > Sanal ağınızı silerseniz Azure, alt ağlarınızı serbest bırakmadan genellikle iki saate kadar sürer, ancak bu işlem daha uzun sürebilir. 
   > Sanal ağları silerken, hala bağlı hiçbir kaynak bulunmadığından emin olun. 
   > Bkz. [sanal ağı silme](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Ortamınızı görüntülemek için, dağıtım bittikten sonra Azure ortamınıza otomatik olarak gitmezse **Kaynağa Git** ' i seçin.

1. *Dış* uç nokta erişimi olan bir Ise için, zaten yoksa bir ağ güvenlik grubu oluşturmanız ve yönetilen BAĞLAYıCıNıN giden IP adreslerinden gelen trafiğe izin vermek için bir gelen güvenlik kuralı eklemeniz gerekir. Bu kuralı ayarlamak için şu adımları izleyin:

   1. ISE menüsünde **Ayarlar**' ın altında **Özellikler**' i seçin.

   1. **Bağlayıcının gıden IP adresleri** altında, genel IP adresi aralıklarını kopyalayın, bu makalede, [sınırlar ve yapılandırma-giden IP adresleri](../logic-apps/logic-apps-limits-and-config.md#outbound)de görüntülenir.

   1. Henüz yoksa bir ağ güvenlik grubu oluşturun.
   
   1. Aşağıdaki bilgilere bağlı olarak, kopyaladığınız genel giden IP adresleri için bir gelen güvenlik kuralı ekleyin. Daha fazla bilgi için bkz. [öğretici: Azure Portal kullanarak ağ trafiğini ağ güvenlik grubuyla filtreleme](../virtual-network/tutorial-filter-network-traffic.md#create-a-network-security-group).

      | Amaç | Kaynak hizmet etiketi veya IP adresleri | Kaynak bağlantı noktaları | Hedef hizmet etiketi veya IP adresleri | Hedef bağlantı noktaları | Notlar |
      |---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
      | Bağlayıcının giden IP adreslerinden gelen trafiğe izin ver | <*bağlayıcı-genel-giden-IP-adresler*> | * | ISE alt ağları ile sanal ağın adres alanı | * | |
      |||||||

1. ISE 'nizin ağ durumunu denetlemek için bkz. [tümleştirme hizmeti ortamınızı yönetme](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

   > [!CAUTION]
   > ISE 'nin ağı sağlıksız hale gelirse, ıSE 'niz tarafından kullanılan dahili App Service Ortamı (ASE) de sağlıksız hale gelebilir. ASE, yedi günden daha uzun bir süre boyunca uygun değilse, ASE askıya alınır. Bu durumu çözümlemek için, sanal ağ kurulumunuzu denetleyin. Bulduğunuz sorunları çözün ve ardından ıSE 'nizi yeniden başlatın. Aksi takdirde, 90 gün sonra askıya alınan ASE silinir ve ıSE kullanılamaz hale gelir. Bu nedenle, gerekli trafiğe izin vermek için ıSE 'nizi sağlıklı tutduğunuzdan emin olun.
   > 
   > Daha fazla bilgi için şu konulara bakın:
   >
   > * [Azure App Service tanılamada genel bakış](../app-service/overview-diagnostics.md)
   > * [Azure App Service Ortamı için ileti günlüğe kaydetme](../app-service/environment/using-an-ase.md#logging)

1. Çalışma ortamınızda mantıksal uygulamalar ve diğer yapıtlar oluşturmaya başlamak için bkz. [Integration Service ortamlarına kaynak ekleme](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > ISE 'nizi oluşturduktan sonra, yönetilen ıSE bağlayıcıları kullanabilmeniz için kullanılabilir hale gelir, ancak mantıksal uygulama Tasarımcısı 'ndaki bağlayıcı seçicisinde otomatik olarak görünmez. Bu ıSE bağlayıcılarını kullanabilmeniz için, mantıksal uygulama Tasarımcısı 'nda görünmesi için [Bu bağlayıcıları el ile eklemeniz ve ıSE 'ye dağıtmanız](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* [Tümleştirme hizmeti ortamlarına kaynaklar ekleme](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Tümleştirme hizmeti ortamlarını yönetme](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* [Azure sanal ağ](../virtual-network/virtual-networks-overview.md) hakkında daha fazla bilgi edinin
* [Azure hizmetleri için sanal ağ tümleştirmesi](../virtual-network/virtual-network-for-azure-services.md) hakkında bilgi edinin
