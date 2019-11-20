---
title: Azure sanal ağlarına erişim-Azure Logic Apps
description: Tümleştirme hizmeti ortamlarının (sesleri) yardım Logic Apps 'in Azure sanal ağlarına (VNet 'ler) erişme konusunda genel bakış
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: e06ac0c0647d9a9a6b5adee289b497f2fe94b222
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889383"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Tümleştirme hizmeti ortamlarını (sesleri) kullanarak Azure Logic Apps Azure sanal ağ kaynaklarına erişim

Bazen mantıksal uygulamalarınızın ve tümleştirme hesaplarınızın, bir [Azure sanal ağı](../virtual-network/virtual-networks-overview.md)içindeki sanal makineler (VM) ve diğer sistemler veya hizmetler gibi güvenli kaynaklara erişmesi gerekir. Bu erişimi ayarlamak için, mantıksal uygulamalarınızı çalıştırabileceğiniz ve tümleştirme hesaplarınızı oluşturabileceğiniz [bir *tümleştirme hizmeti ortamı* (ISE) oluşturabilirsiniz](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) .

Bir ıSE oluşturduğunuzda, Azure sanal ağınıza ait olan ve Azure sanal ağınıza Logic Apps hizmetin özel ve yalıtılmış bir örneğini *dağıtan Azure sanal* ağınıza yönelik olarak Azure sanal ağınıza yönelik bir sanal makine oluşturun. Bu özel örnek, depolama gibi özel kaynaklar kullanır ve genel "küresel" Logic Apps hizmetinden ayrı olarak çalışır. Yalıtılmış özel örneğinizi ve genel Global örneği ayırmak, diğer Azure kiracılarının uygulamalarınızın performansı üzerinde sahip olabileceği etkileri azaltmaya yardımcı olur ve bu da ["gürültülü komşular" etkisi](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)olarak da bilinir.

ISE 'nizi oluşturduktan sonra mantıksal uygulamanızı veya tümleştirme hesabınızı oluşturmaya geçtiğinizde, mantıksal uygulamanız veya tümleştirme hesabınızın konumu olarak ıSE 'yi seçebilirsiniz:

![Tümleştirme hizmeti ortamını seçin](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Mantıksal uygulamanız artık bu öğelerden herhangi birini kullanarak sanal ağınıza doğrudan veya bağlı olan sistemlere doğrudan erişebilir:

* Bu sistem için **Ise**etiketli bağlayıcı
* HTTP tetikleyicisi veya eylemi gibi **çekirdek**etiketli bir yerleşik tetikleyici veya eylem
* Özel bağlayıcı

Bu genel bakışta, bir ıSE 'nin mantıksal uygulamalarınızın ve tümleştirme hesaplarınızın Azure sanal ağınıza doğrudan erişimini nasıl verdiği ve bir ıSE ile küresel Logic Apps hizmeti arasındaki farkları karşılaştıran daha fazla ayrıntı açıklanmaktadır.

> [!IMPORTANT]
> Logic Apps, yerleşik Tetikleyiciler, yerleşik Eylemler ve ıSE 'de çalışan bağlayıcılar, tüketim tabanlı fiyatlandırma planından farklı bir fiyatlandırma planı kullanır. Fiyatlandırma ve faturalandırma işinin nasıl sesleri olduğunu öğrenmek için [Logic Apps fiyatlandırma modeline](../logic-apps/logic-apps-pricing.md#fixed-pricing)bakın. Fiyatlandırma fiyatları için bkz. [Logic Apps fiyatlandırması](../logic-apps/logic-apps-pricing.md).
>
> Ayrıca, çalışma süresi, depolama tutma, aktarım hızı, HTTP isteği ve yanıt zaman aşımları, ileti boyutları ve özel bağlayıcı istekleri için de sınırlar artmıştır. 
> Daha fazla bilgi için bkz. [Azure Logic Apps Için sınırlar ve yapılandırma](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Yalıtılmış ve Global

Azure 'da tümleşik bir hizmet ortamı (ıSE) oluşturduğunuzda, ıSE *'nizi eklemek Istediğiniz Azure* sanal ağını seçebilirsiniz. Azure daha sonra Logic Apps hizmetinin özel bir örneğini sanal ağınıza çıkartır veya dağıtır. Bu eylem, mantıksal uygulamalarınızı özel kaynaklarda oluşturabileceğiniz ve çalıştırabileceğiniz bir yalıtılmış ortam oluşturur. Mantıksal uygulamanızı oluşturduğunuz zaman, mantıksal uygulamanızı sanal ağınıza ve bu ağdaki kaynaklara doğrudan erişmenizi sağlayan uygulamanızın konumu olarak ıSE 'nizi seçersiniz.

Bir ıSE 'de Logic Apps, genel Logic Apps hizmetiyle aynı kullanıcı deneyimlerini ve benzer özellikleri sağlar. Yalnızca aynı yerleşik Tetikleyicileri, yerleşik eylemleri ve bağlayıcıları küresel Logic Apps hizmetinden kullanamazsınız, ancak aynı zamanda ıSE 'ye özgü bağlayıcılar da kullanabilirsiniz. Örneğin, bir ıSE 'de çalışan sürümler sunan bazı standart bağlayıcılar aşağıda verilmiştir:

* Azure Blob depolama, dosya depolama ve tablo depolama
* Azure kuyrukları, Azure Service Bus, Azure Event Hubs ve IBM MQ
* Dosya sistemi, FTP ve SFTP-SSH
* SQL Server, Azure SQL veri ambarı Azure Cosmos DB
* AS2, x12 ve EDIOLGU

ISE ve ıSE olmayan bağlayıcılar arasındaki fark, Tetikleyiciler ve eylemlerin çalıştırıldığı konumlardır:

* ISE 'de, HTTP gibi yerleşik Tetikleyiciler ve eylemler her zaman mantıksal uygulamanızla aynı çalışma alanında çalışır ve **çekirdek** etiketi görüntüler.

  !["Çekirdek" yerleşik Tetikleyiciler ve Eylemler ' i seçin](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* Bir ıSE 'de çalışan bağlayıcıların genel Logic Apps hizmetinde bulunan genel olarak barındırılan sürümleri vardır. İki sürüm sunan bağlayıcılar için **Ise** etiketine sahip bağlayıcılar her zaman mantıksal uygulamanızla aynı Ise çalıştırılır. **Ise** etiketi olmayan bağlayıcılar küresel Logic Apps hizmetinde çalışır.

  ![ISE bağlayıcıları seçin](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

ISE Ayrıca çalışma süresi, depolama tutma, aktarım hızı, HTTP isteği ve yanıt zaman aşımları, ileti boyutları ve özel bağlayıcı istekleri için daha fazla sınır sağlar. Daha fazla bilgi için bkz. [Azure Logic Apps Için sınırlar ve yapılandırma](logic-apps-limits-and-config.md).

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU 'Ları

ISE 'yi oluşturduğunuzda, geliştirici SKU 'SU veya Premium SKU 'YU seçebilirsiniz. Bu SKU 'Lar arasındaki farklar şunlardır:

* **Geliştirici**

  Deneme, geliştirme ve test için kullanabileceğiniz, ancak üretim ya da performans testi için kullanabileceğiniz düşük maliyetli bir ıSE sağlar. Geliştirici SKU 'SU, sabit bir aylık fiyat için yerleşik Tetikleyiciler ve Eylemler, standart bağlayıcılar, kurumsal bağlayıcılar ve tek bir [ücretsiz katman](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) tümleştirme hesabı içerir. Ancak, bu SKU herhangi bir hizmet düzeyi sözleşmesi (SLA), kapasite ölçekleme seçenekleri veya geri dönüştürme sırasında, gecikme veya kapalı kalma süresi yaşayabileceğiniz anlamına gelir.

* **Premium**

  Üretim için kullanabileceğiniz ve SLA desteği, yerleşik Tetikleyiciler ve Eylemler, standart bağlayıcılar, kurumsal bağlayıcılar, tek bir [Standart katman](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) tümleştirme hesabı, kapasiteyi ölçeklendirme seçenekleri ve sabit bir aylık fiyat için geri dönüştürme.

> [!IMPORTANT]
> SKU seçeneği yalnızca ıSE oluşturma sırasında kullanılabilir ve daha sonra değiştirilemez.

Fiyatlandırma fiyatları için bkz. [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps/). Fiyatlandırma ve faturalandırma işinin nasıl sesleri olduğunu öğrenmek için [Logic Apps fiyatlandırma modeline](../logic-apps/logic-apps-pricing.md#fixed-pricing)bakın.

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE uç noktası erişimi

ISE 'yi oluşturduğunuzda, iç veya dış erişim uç noktaları kullanmayı tercih edebilirsiniz. Bu uç noktalar, işinizdeki Logic Apps 'teki istek veya Web kancasının, sanal ağınızın dışından çağrı alıp almamadığını belirtir. Bu uç noktalar Ayrıca Logic App çalıştırma geçmişinde girişlere ve çıkışlara erişimi de etkiler.

* **İç**: e-postalarda Logic Apps çağrılarına ve yalnızca *sanal ağınızın* içinden çalıştırma geçmişinde girişlere ve çıkışlara erişilmesine izin veren özel uç noktalar

* **Dış**: işinizdeki Logic Apps çağrılarına ve *sanal ağınızın dışından* çalıştırma geçmişindeki giriş ve çıkışlara erişilmesine izin veren genel uç noktalar

> [!IMPORTANT]
> Erişim uç noktası seçeneği yalnızca ıSE oluşturma sırasında kullanılabilir ve daha sonra değiştirilemez.

<a name="on-premises"></a>

## <a name="access-to-on-premises-data-sources"></a>Şirket içi veri kaynaklarına erişim

Bir Azure sanal ağına bağlı şirket içi sistemler için, mantıksal uygulamalarınızın şu öğelerden herhangi birini kullanarak bu sistemlere doğrudan erişebilmesi için o ağa bir ıSE ekleyin:

* HTTP eylemi

* O sistem için ıSE etiketli bağlayıcı

  > [!NOTE]
  > Windows kimlik doğrulamasını bir [tümleştirme hizmeti ortamında (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)SQL Server Bağlayıcısı ile kullanmak için, Şirket [içi veri ağ geçidiyle](../logic-apps/logic-apps-gateway-install.md)bağlayıcının Ise sürümünü kullanın. ISE etiketli sürüm Windows kimlik doğrulamasını desteklemez.

* Özel bağlayıcı

  * Şirket içi veri ağ geçidini gerektiren özel bağlayıcılarınız varsa ve bu bağlayıcıları bir ıSE dışında oluşturduysanız, bir ıSE içindeki Logic Apps de bu bağlayıcıları kullanabilir.
  
  * Bir ıSE içinde oluşturulan özel bağlayıcılar şirket içi veri ağ geçidi ile çalışmaz. Ancak, bu bağlayıcılar, ıSE 'yi barındıran sanal ağa bağlı şirket içi veri kaynaklarına doğrudan erişebilir. Bu nedenle, bir ıSE içindeki Logic Apps, bu kaynaklarla iletişim kurarken veri ağ geçidine ihtiyaç duymamasından kaynaklanıyor olabilir.

Bir sanal ağa bağlı olmayan veya ıSE-lablı bağlayıcılar bulunmayan şirket içi sistemler için, mantıksal uygulamalarınızın bu sistemlere bağlanabilmesi için önce şirket [içi veri ağ geçidini ayarlamanız](../logic-apps/logic-apps-gateway-install.md) gerekir.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>ISE ile tümleştirme hesapları

Tümleştirme hesaplarını bir tümleştirme hizmeti ortamı (ıSE) içinde Logic Apps ile kullanabilirsiniz. Ancak, bu tümleştirme hesaplarının bağlı Logic Apps ile *aynı Ise* kullanması gerekir. Bir ıSE içindeki Logic Apps yalnızca aynı ıSE içindeki tümleştirme hesaplarına başvurabilir. Bir tümleştirme hesabı oluşturduğunuzda, çalışma hesabınızı tümleştirme hesabınızın konumu olarak seçebilirsiniz. Fiyatlandırma ve faturalandırma 'nin bir ıSE ile tümleştirme hesaplarında nasıl çalıştığını öğrenmek için [Logic Apps fiyatlandırma modeline](../logic-apps/logic-apps-pricing.md#fixed-pricing)bakın. Fiyatlandırma fiyatları için bkz. [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Sonraki adımlar

* [Yalıtılmış Logic Apps 'ten Azure sanal ağlarına bağlanma](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Tümleştirme hizmeti ortamlarına yapıt ekleme](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Tümleştirme hizmeti ortamlarını yönetme](../logic-apps/ise-manage-integration-service-environment.md)
* [Azure sanal ağ](../virtual-network/virtual-networks-overview.md) hakkında daha fazla bilgi edinin
* [Azure hizmetleri için sanal ağ tümleştirmesi](../virtual-network/virtual-network-for-azure-services.md) hakkında bilgi edinin
