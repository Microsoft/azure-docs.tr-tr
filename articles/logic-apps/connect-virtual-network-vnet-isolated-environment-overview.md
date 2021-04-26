---
title: Azure sanal ağlarına erişme
description: Tümleştirme hizmeti ortamlarının (sesleri) yardım Logic Apps 'in Azure sanal ağlarına (VNet 'ler) erişme konusunda genel bakış
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 3070083040424b877159955dc2138f15319f05c8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766398"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Tümleştirme hizmeti ortamlarını (sesleri) kullanarak Azure Logic Apps Azure sanal ağ kaynaklarına erişim

Bazen mantıksal uygulamalarınızın, sanal makineler (VM 'Ler) ve diğer sistemler veya hizmetler gibi bir [Azure sanal ağı](../virtual-network/virtual-networks-overview.md)içinde veya bağlı olan güvenli kaynaklara erişmesi gerekir. Bu erişimi ayarlamak için [bir *tümleştirme hizmeti ortamı* (ISE) oluşturabilirsiniz](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). ISE, adanmış kaynakları kullanan ve "küresel" çok kiracılı Logic Apps hizmetinden ayrı olarak çalıştırılan Logic Apps hizmetin bir örneğidir. Bir ıSE 'deki veriler, [Bu ıSE 'yi oluşturduğunuz ve dağıttığınız bölgede](https://azure.microsoft.com/global-infrastructure/data-residency/)kalır.

Örneğin, bazı Azure sanal ağları, Azure depolama, Azure Cosmos DB veya Azure SQL veritabanı, iş ortağı hizmetleri veya Azure 'da barındırılan müşteri hizmetleri gibi Azure PaaS hizmetlerine erişim sağlamak için [Azure özel bağlantısı](../private-link/private-link-overview.md)aracılığıyla ayarlayabileceğiniz özel uç noktalar kullanır. Mantıksal uygulamalarınızın özel uç noktaları kullanan sanal ağlara erişmesi gerekiyorsa, bu mantık uygulamalarını bir ıSE içinde oluşturmanız, dağıtmanız ve çalıştırmanız gerekir.

Bir ıSE oluşturduğunuzda, Azure bu ıSE 'yi Azure sanal ağınıza *çıkartır* veya dağıtır. Daha sonra bu ıSE 'yi, erişmesi gereken Logic Apps ve tümleştirme hesaplarının konumu olarak kullanabilirsiniz.

![Tümleştirme hizmeti ortamını seçin](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Bu genel bakış, [BIR ıSE 'yi nasıl kullanmak istediğinizle](#benefits)ilgili daha fazla bilgi, [adanmış ve çok kiracılı Logic Apps hizmeti arasındaki farkları](#difference)ve Azure sanal ağınızda bulunan veya bağlı kaynaklara doğrudan erişme hakkında daha fazla bilgi sağlar.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Neden bir ıSE kullanılmalıdır?

Mantıksal uygulamaları kendi ayrı ayrılmış Örneğinizde çalıştırmak, diğer Azure kiracılarının ["gürültülü komşular" etkisi](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)olarak da bilinen uygulamalarınızın performansı üzerinde sahip olabileceği etkiyi azaltmaya yardımcı olur. ISE Ayrıca bu avantajları sağlar:

* Sanal ağınıza doğrudan veya bağlı olan kaynaklara doğrudan erişim

  Bir ıSE içinde oluşturduğunuz ve çalıştırdığınız Logic Apps, [özel olarak tasarlanmış ve çalışır durumda çalışan bağlayıcılar](../connectors/managed.md#ise-connectors)kullanabilir. Şirket içi bir sistem veya veri kaynağı için ıSE Bağlayıcısı varsa, Şirket [içi veri ağ geçidini](../logic-apps/logic-apps-gateway-connection.md)kullanmak zorunda kalmadan doğrudan bağlanabilirsiniz. Daha fazla bilgi için, bu konunun ilerleyen kısımlarında, [adanmış ve çok kiracılı](#difference) ve [Şirket içi sistemlere erişim](#on-premises) bölümüne bakın.

* Sanal ağınıza bağlı olan veya olmayan kaynaklara devam eden erişim

  Bir ıSE içinde oluşturduğunuz ve çalıştırdığınız Logic Apps, Işe özel bir bağlayıcı kullanılamadığında çok kiracılı Logic Apps hizmetinde çalışan bağlayıcıları kullanmaya devam edebilir. Daha fazla bilgi için bkz. [adanmış ve çok kiracılı](#difference).

* Çoklu kiracı hizmetindeki Logic Apps tarafından paylaşılan statik IP adreslerinden ayrı olan kendi statik IP adresleriniz. Hedef sistemlerle iletişim kurmak için tek bir genel, statik ve öngörülebilir giden IP adresi de ayarlayabilirsiniz. Bu şekilde, her bir ıSE için bu hedef sistemlerde ek güvenlik duvarı açılışlarını ayarlamanız gerekmez.

* Çalışma süresi, depolama tutma, aktarım hızı, HTTP isteği ve yanıt zaman aşımları, ileti boyutları ve özel bağlayıcı istekleri için artan sınırlar. Daha fazla bilgi için bkz. [Azure Logic Apps Için sınırlar ve yapılandırma](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Adanmış ve çok kiracılı

Bir ıSE 'de Logic Apps oluşturup çalıştırdığınızda, aynı kullanıcı deneyimlerini ve çok kiracılı Logic Apps hizmetiyle benzer özellikleri alırsınız. Çok kiracılı Logic Apps hizmetinde bulunan tüm yerleşik Tetikleyicileri, eylemleri ve yönetilen bağlayıcıları kullanabilirsiniz. Bazı yönetilen bağlayıcılar ek ıSE sürümlerini sunar. Ise bağlayıcılar ve ıSE olmayan bağlayıcılar arasındaki fark, çalıştırıldıkları yerde ve bir ıSE içinde çalışırken mantıksal uygulama tasarımcısında bulunan Etiketler arasında bulunur.

![ISE 'de etiketleri olan ve olmayan bağlayıcılar](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Yerleşik Tetikleyiciler ve HTTP gibi eylemler, **çekirdek** etiketi görüntüler ve mantıksal uygulamanız ile aynı çalışma alanında çalışır.

* **Ise** etiketini görüntüleyen yönetilen bağlayıcılar, özel olarak TASE için tasarlanmıştır ve *mantıksal uygulamanızla aynı çalışma alanında çalışır*. Örneğin, [Ise sürümlerini sunan bazı bağlayıcılar](../connectors/managed.md#ise-connectors)aşağıda verilmiştir:<p>

  * Azure Blob depolama, dosya depolama ve tablo depolama
  * Azure Service Bus, Azure kuyrukları, Azure Event Hubs
  * Azure Otomasyonu, Azure Key Vault, Azure Event Grid ve Azure Izleyici günlükleri
  * FTP, SFTP-SSH, dosya sistemi ve SMTP
  * SAP, IBM MQ, IBM DB2 ve IBM 3270
  * SQL Server, Azure SYNAPSE Analytics, Azure Cosmos DB
  * AS2, x12 ve EDIOLGU

  Nadir özel durumlar sayesinde, şirket içi sistem veya veri kaynağı için ıSE Bağlayıcısı varsa, Şirket [içi veri ağ geçidini](../logic-apps/logic-apps-gateway-connection.md)kullanmadan doğrudan bağlanabilirsiniz. Daha fazla bilgi için bu konunun ilerleyen kısımlarında yer [alan şirket içi sistemlere erişim](#on-premises) bölümüne bakın.

* **Ise** etiketini görüntülememeyen yönetilen bağlayıcılar, BIR Ise içindeki Logic Apps için çalışmaya devam eder. Bu bağlayıcılar her zaman ıSE 'de değil *çok kiracılı Logic Apps hizmetinde çalışır*.

* *BIR Ise dışında* oluşturduğunuz ve [Şirket içi veri ağ geçidi](../logic-apps/logic-apps-gateway-connection.md)GEREKTIRMEKSIZIN, bir Ise içindeki Logic Apps için çalışmaya devam etmeksizin özel bağlayıcılar. Ancak, *BIR ıSE içinde* oluşturduğunuz özel bağlayıcılar şirket içi veri ağ geçidi ile çalışmaz. Daha fazla bilgi için bkz. Şirket [içi sistemlere erişim](#on-premises).

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Şirket içi sistemlere erişim

Bir ıSE içinde çalışan Logic Apps, bu öğeleri kullanarak bir Azure sanal ağı içinde veya içinde bulunan şirket içi sistemlere ve veri kaynaklarına doğrudan erişebilir:<p>

* **Çekirdek** etıketı görüntüleyen http tetikleyicisi veya eylemi

* Şirket içi sistem veya veri kaynağı için varsa **Ise** Bağlayıcısı

  ISE Bağlayıcısı varsa, [Şirket içi veri ağ geçidi](../logic-apps/logic-apps-gateway-connection.md)olmadan sisteme veya veri kaynağına doğrudan erişebilirsiniz. Ancak, bir ıSE 'den SQL Server erişmeniz ve Windows kimlik doğrulaması kullanmanız gerekiyorsa, bağlayıcının ıSE sürümü ve şirket içi veri ağ geçidi kullanmanız gerekir. Bağlayıcının ıSE sürümü Windows kimlik doğrulamasını desteklemez. Daha fazla bilgi için bkz. [Ise bağlayıcıları](../connectors/managed.md#ise-connectors) ve [bir tümleştirme hizmeti ortamından bağlanma](../connectors/managed.md#integration-account-connectors).

* Özel bir bağlayıcı

  * *BIR Ise dışında* oluşturduğunuz ve [Şirket içi veri ağ geçidi](../logic-apps/logic-apps-gateway-connection.md)GEREKTIRMEKSIZIN, bir Ise içindeki Logic Apps için çalışmaya devam etmeksizin özel bağlayıcılar.

  * *BIR ıSE içinde* oluşturduğunuz özel bağlayıcılar, şirket içi veri ağ geçidi ile çalışmaz. Ancak, bu bağlayıcılar, şirket içi sistemlere ve şirket içinde bulunan ya da bağlı olan sanal ağ üzerindeki veri kaynaklarına doğrudan erişebilir. Bu nedenle, bir ıSE içindeki Logic Apps genellikle bu kaynaklara erişirken veri ağ geçidine gerek kalmaz.

ISE bağlayıcıları olmayan, sanal ağınızın dışında olan veya sanal ağınıza bağlı olmayan şirket içi sistemlere ve veri kaynaklarına erişmek için, hala şirket içi veri ağ geçidini kullanmanız gerekir. Bir ıSE içindeki Logic Apps, **çekirdek** veya **Ise** etiketi olmayan bağlayıcılar kullanmaya devam edebilir. Bu bağlayıcılar, ıSE yerine çok kiracılı Logic Apps hizmetinde çalışır. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU 'Ları

ISE 'yi oluşturduğunuzda, geliştirici SKU 'SU veya Premium SKU 'YU seçebilirsiniz. Bu SKU seçeneği yalnızca ıSE oluşturma sırasında kullanılabilir ve daha sonra değiştirilemez. Bu SKU 'Lar arasındaki farklar şunlardır:

* **Geliştirici**

  Araştırma, denemeleri, geliştirme ve test için kullanabileceğiniz, ancak üretim ya da performans testi için kullanabileceğiniz düşük maliyetli bir ıSE sağlar. Geliştirici SKU 'SU, [sabit bir aylık fiyat](https://azure.microsoft.com/pricing/details/logic-apps)için yerleşik Tetikleyiciler ve Eylemler, standart bağlayıcılar, kurumsal bağlayıcılar ve tek bir [ücretsiz katman](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) tümleştirme hesabı içerir. 

  > [!IMPORTANT]
  > Bu SKU, geri dönüştürme sırasında bir hizmet düzeyi sözleşmesi (SLA), ölçek artırma yeteneği veya yedekliliği olmadığından gecikme veya kapalı kalma süresi yaşayabileceğiniz anlamına gelir. Arka uç güncelleştirmeleri zaman zaman kesme hizmeti gerektirebilir.

  Kapasite ve limit bilgilerini, [Azure Logic Apps Içindeki Ise sınırlarına](logic-apps-limits-and-config.md#integration-service-environment-ise)bakın. Faturanızı nasıl çalıştığını öğrenmek için [Logic Apps fiyatlandırma modeline](../logic-apps/logic-apps-pricing.md#fixed-pricing)bakın.

* **Premium**

  , Üretim ve performans testi için kullanabileceğiniz bir ıSE sağlar. Premium SKU; SLA desteği, yerleşik Tetikleyiciler ve Eylemler, standart bağlayıcılar, kurumsal bağlayıcılar, tek bir [Standart katman](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) tümleştirme hesabı, ölçek artırma özelliği ve artıklığı [sabit bir aylık fiyat](https://azure.microsoft.com/pricing/details/logic-apps)için geri dönüştürme sırasında içerir.

  Kapasite ve limit bilgilerini, [Azure Logic Apps Içindeki Ise sınırlarına](logic-apps-limits-and-config.md#integration-service-environment-ise)bakın. Faturanızı nasıl çalıştığını öğrenmek için [Logic Apps fiyatlandırma modeline](../logic-apps/logic-apps-pricing.md#fixed-pricing)bakın.

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE uç noktası erişimi

ISE 'yi oluşturduğunuzda, iç veya dış erişim uç noktaları kullanmayı tercih edebilirsiniz. Seçiminiz, işinizdeki Logic Apps 'teki istek veya Web kancasının, sanal ağınızın dışından çağrı alıp alamayacağını belirler. Bu uç noktalar Ayrıca, Logic Apps 'in çalışma geçmişinden gelen girişlere ve çıkışlara erişmenin yolunu da etkiler.

> [!IMPORTANT]
> Erişim uç noktasını yalnızca ıSE oluşturma sırasında seçebilir ve bu seçeneği daha sonra değiştiremezsiniz.

* **İç**: özel uç noktalar, ortamınızdaki Logic Apps çağrılarına izin verir ve Logic Apps 'in çalışma geçmişinden gelen giriş ve çıkışları *yalnızca sanal ağınızın* içinden görüntüleyebilir ve bunlara erişebilirsiniz.

  > [!IMPORTANT]
  > Bu Web kancası tabanlı Tetikleyicileri kullanmanız gerekiyorsa, ıSE 'yi oluşturduğunuzda iç uç noktalar *değil* dış uç noktaları kullanın:
  > 
  > * Azure DevOps
  > * Azure Event Grid
  > * Common Data Service
  > * Office 365
  > * SAP (ıSE sürümü)
  > 
  > Ayrıca, Özel uç noktalar ve çalıştırma geçmişine erişmek istediğiniz bilgisayar arasında ağ bağlantısına sahip olduğunuzdan emin olun. Aksi takdirde, mantıksal uygulamanızın çalıştırma geçmişini görüntülemeye çalıştığınızda, "beklenmeyen hata" ifadesini içeren bir hata alırsınız. Alınamadı ".
  >
  > ![Azure depolama eylem hatası, güvenlik duvarı üzerinden trafik gönderememesine neden oldu](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Örneğin, istemci bilgisayarınız ıSE 'nin sanal ağı içinde veya eşleme veya sanal özel ağ aracılığıyla ıSE 'nin sanal ağına bağlı bir sanal ağ içinde bulunabilir. 

* **Dış**: genel uç noktaları, *sanal ağınızın dışından* Logic Apps 'in çalışma geçmişinden gelen giriş ve çıkışları görüntüleyebileceğiniz ve erişebileceğiniz, ortamınızda Logic Apps çağrılarına izin verir. Ağ güvenlik grupları (NSG 'ler) kullanıyorsanız, çalıştırma geçmişinin giriş ve çıkışlarına erişime izin vermek için gelen kurallarla ayarlandıklarından emin olun. Daha fazla bilgi için bkz. [Ise için erişimi etkinleştirme](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

ISE 'nizin iç veya dış erişim uç noktası kullanıp kullanmadığını belirlemek için, ıSE menüsünde, **Ayarlar**' ın altında **Özellikler**' i seçin ve **erişim uç noktası** özelliğini bulun:

![ISE erişim uç noktası bul](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Fiyatlandırma modeli

Logic Apps, yerleşik Tetikleyiciler, yerleşik Eylemler ve ıSE 'de çalışan bağlayıcılar, tüketim tabanlı fiyatlandırma planından farklı olan sabit bir fiyatlandırma planı kullanır. Daha fazla bilgi için bkz. [Logic Apps fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md#fixed-pricing). Fiyatlandırma fiyatları için bkz. [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>ISE ile tümleştirme hesapları

Tümleştirme hesaplarını bir tümleştirme hizmeti ortamı (ıSE) içinde Logic Apps ile kullanabilirsiniz. Ancak, bu tümleştirme hesaplarının bağlı Logic Apps ile *aynı Ise* kullanması gerekir. Bir ıSE içindeki Logic Apps yalnızca aynı ıSE içindeki tümleştirme hesaplarına başvurabilir. Bir tümleştirme hesabı oluşturduğunuzda, çalışma hesabınızı tümleştirme hesabınızın konumu olarak seçebilirsiniz. Fiyatlandırma ve faturalandırma 'nin bir ıSE ile tümleştirme hesaplarında nasıl çalıştığını öğrenmek için [Logic Apps fiyatlandırma modeline](../logic-apps/logic-apps-pricing.md#fixed-pricing)bakın. Fiyatlandırma fiyatları için bkz. [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps/). Sınır bilgileri için bkz. [tümleştirme hesabı sınırları](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Logic Apps Azure sanal ağlarına bağlanma](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Azure sanal ağ](../virtual-network/virtual-networks-overview.md) hakkında daha fazla bilgi edinin
* [Azure hizmetleri için sanal ağ tümleştirmesi](../virtual-network/virtual-network-for-azure-services.md) hakkında bilgi edinin
