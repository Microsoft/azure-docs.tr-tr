---
title: Faturalandırma & fiyatlandırma modelleri
description: Fiyatlandırma ve faturalama modellerinin nasıl çalıştığı hakkında genel bakış Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: a3c20dd85c94c359259cf69e25bb9083d56857fc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777158"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Azure Logic Apps için fiyatlandırma ve faturalandırma modelleri

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) , bulutta ölçeklenebilen otomatikleştirilmiş Tümleştirme iş akışları oluşturmanıza ve çalıştırmanıza yardımcı olur. Bu makalede, Logic Apps hizmeti ve ilgili kaynaklar için faturalandırma ve fiyatlandırma modellerinin nasıl çalıştığı açıklanır. Belirli fiyatlandırma ücretleri için bkz. [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps). Maliyetleri nasıl planlayabileceğinizi, yönetebileceğinizi ve izleyeceğinizi öğrenmek için bkz. [Azure Logic Apps maliyetlerini planlayın ve yönetin](plan-manage-costs.md).

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Çok kiracılı fiyatlandırma

Kullanım için ödeme tüketimi fiyatlandırma modeli, genel, "genel", çok kiracılı Logic Apps hizmetinde çalışan Logic Apps için geçerlidir. Tüm başarılı ve başarısız çalıştırmalar tarifeli ve faturalandırılır.

Örneğin, bir yoklama tetikleyicisinin yaptığı bir istek, bu tetikleyici atlandığı ve mantıksal uygulama iş akışı örneği oluşturulmasa bile yürütme olarak ölçülmektedir.

| Öğeler | Description |
|-------|-------------|
| [Yerleşik](../connectors/built-in.md) Tetikleyiciler ve eylemler | Logic Apps hizmetinde yerel olarak çalıştırın ve [ **eylem** fiyatı](https://azure.microsoft.com/pricing/details/logic-apps/)kullanılarak Ücretlendirilebilir. <p><p>Örneğin, HTTP tetikleyicisi ve Istek tetikleyicisi yerleşik tetiklerdir, ancak HTTP eylemi ve yanıt eylemi yerleşik eylemlerdir. Veri işlemleri, toplu işlemler, değişken işlemleri ve döngüler, koşullar, anahtar, paralel dallar vb. gibi [iş akışı denetim eylemleri](../connectors/built-in.md)de yerleşik eylemlerdir. |
| [Standart bağlayıcı](../connectors/managed.md) Tetikleyicileri ve eylemleri <p><p>[Özel bağlayıcı](../connectors/apis-list.md#custom-apis-and-connectors) Tetikleyicileri ve eylemleri | [Standart bağlayıcı fiyatı](https://azure.microsoft.com/pricing/details/logic-apps/)kullanılarak Ücretlendirilebilir. |
| [Kurumsal bağlayıcı](../connectors/managed.md) Tetikleyicileri ve eylemleri | [Kurumsal bağlayıcı fiyatı](https://azure.microsoft.com/pricing/details/logic-apps/)kullanılarak Ücretlendirilebilir. Ancak, genel önizleme sırasında kurumsal bağlayıcılar [ *Standart* bağlayıcı fiyatı](https://azure.microsoft.com/pricing/details/logic-apps/)kullanılarak ölçümdedir. |
| [Döngüler](logic-apps-control-flow-loops.md) içindeki eylemler | Bir döngüde çalışan her eylem, çalıştıran her döngü döngüsü için ölçülme yapılır. <p><p>Örneğin, bir listeyi işleyen eylemleri içeren bir "for each" döngüsüne sahip olduğunuzu varsayalım. Logic Apps hizmeti, liste öğelerinin sayısını döngüdeki eylem sayısıyla çarparak bu döngüde çalışan her eylemi ölçümler ve döngüyü başlatan eylemi ekler. Bu nedenle, 10 öğe listesi için hesaplama, 11 işlem yürütmelerinin sonucu olan (10 * 1) + 1 ' dir. |
| Yeniden deneme sayısı | En basit özel durumları ve hataları işlemek için, Tetikleyiciler ve eylemler üzerinde desteklenmiş bir [yeniden deneme ilkesi](logic-apps-exception-handling.md#retry-policies) ayarlayabilirsiniz. Özgün istek ile birlikte bu yeniden denemeler, tetikleyicinin veya eylemin yerleşik, standart ya da kurumsal türe sahip olup olmadığına bağlı olarak ücretlendirilir. Örneğin, 2 yeniden deneme ile yürütülen bir eylem 3 eylem yürütmeleri için ücretlendirilir. |
| [Veri saklama ve depolama tüketimi](#data-retention) | Fiyatlandırma ayrıntıları tablosu altında [Logic Apps fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/logic-apps/)bulabileceğiniz veri saklama fiyatı kullanılarak **Ücretlendirilebilir** . |
|||

Daha fazla bilgi için, aşağıdakilere bakın:

* [Yürütmeler ve depolama tüketimi için ölçümleri görüntüleme](plan-manage-costs.md#monitor-billing-metrics)
* [Azure Logic Apps sınırları](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Tarifeli değil

* Karşılanmayan koşullar nedeniyle atlanan Tetikleyiciler
* Mantıksal uygulama bitmeden önce durdurulduğundan çalıştırılmamış eylemler
* [Devre dışı mantıksal uygulamalar](#disabled-apps)

### <a name="other-related-resources"></a>Diğer ilgili kaynaklar

Logic Apps, tümleştirme hesapları, şirket içi veri ağ geçitleri ve tümleştirme hizmeti ortamları (sesleri) gibi diğer ilgili kaynaklarla çalışır. Bu kaynakların fiyatlandırması hakkında bilgi edinmek için bu konunun ilerleyen kısımlarında bu bölümleri inceleyin:

* [On-premises data gateway (Şirket içi veri ağ geçidi)](#data-gateway)
* [Tümleştirme hesabı fiyatlandırma modeli](#integration-accounts)
* [ISE fiyatlandırma modeli](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Tüketim maliyetlerini tahmin etmeye yönelik ipuçları

Daha doğru tüketim maliyetlerini tahmin etmenize yardımcı olmak için şu ipuçlarını gözden geçirin:

* Yalnızca yoklama aralığındaki hesaplamalarınızı temel maktansa, belirli bir güne ulaşacak olabilecek ileti veya olay sayısını göz önünde bulundurun.

* Bir olay veya ileti tetikleyici ölçütlerini karşıladığında, birçok tetikleyici tüm diğer bekleyen olayları ve ölçütlere uyan iletileri okumayı hemen dener. Bu davranış, daha uzun bir yoklama aralığı seçtiğinizde bile tetikleyici, iş akışlarını başlatmak için uygun olan bekleme olay veya ileti sayısına göre ateşlenir. Bu davranışı izleyen Tetikleyiciler Azure Service Bus ve Azure Olay Hub 'ı içerir.

  Örneğin, her gün bir uç noktayı denetleyen tetikleyiciyi ayarladığınızı varsayalım. Tetikleyici, uç noktayı denetlediğinde ve ölçütlere uyan 15 olay bulduğunda, tetikleyici ateşlenir ve ilgili iş akışını 15 kez çalıştırır. Logic Apps hizmeti, tetikleyici istekleri dahil olmak üzere, bu 15 iş akışlarının gerçekleştirdiği tüm eylemleri ölçümler.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>ISE fiyatlandırması

Sabit bir fiyatlandırma modeli, bir [ *tümleştirme hizmeti ortamında* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)çalışan Logic Apps için geçerlidir. ISE, oluşturduğunuz [Ise düzeyine veya *SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) 'ya bağlı olarak [tümleştirme hizmeti ortamı fiyatı](https://azure.microsoft.com/pricing/details/logic-apps)kullanılarak faturalandırılır. Bu fiyatlandırma, ayrılan kapasite ve adanmış kaynaklar için ödeme yaptığınız gibi çok kiracılı fiyatlandırmadan farklıdır.

| ıSE SKU 'SU | Description |
|---------|-------------|
| **Premium** | Temel birim [sabit kapasiteye](logic-apps-limits-and-config.md#integration-service-environment-ise) sahiptir ve [Premium SKU 'su için saatlik bir ücret üzerinden faturalandırılır](https://azure.microsoft.com/pricing/details/logic-apps). Daha fazla işleme ihtiyacınız varsa, ıSE veya daha sonra oluşturduğunuz zaman [daha fazla ölçek birimi ekleyebilirsiniz](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) . Her ölçek birimi, [temel birim oranının kabaca yarısı olan saatlik bir ücret](https://azure.microsoft.com/pricing/details/logic-apps)üzerinden faturalandırılır. <p><p>Kapasite ve limit bilgilerini, [Azure Logic Apps Içindeki Ise sınırlarına](logic-apps-limits-and-config.md#integration-service-environment-ise)bakın. |
| **Geliştirici** | Temel birim [sabit kapasiteye](logic-apps-limits-and-config.md#integration-service-environment-ise) sahiptir ve [Geliştirici SKU 'su için saatlik bir ücret üzerinden faturalandırılır](https://azure.microsoft.com/pricing/details/logic-apps). Ancak, bu SKU, geri dönüştürme sırasında bir hizmet düzeyi sözleşmesi (SLA), ölçek artırma özelliği veya yedekliliği yoktur, bu da gecikme veya kapalı kalma süresi yaşayabileceğiniz anlamına gelir. Arka uç güncelleştirmeleri zaman zaman kesme hizmeti gerektirebilir. <p><p>**Önemli**: Bu SKU 'yu yalnızca araştırma, denemeleri, geliştirme ve test için (üretim veya performans testi için değil) kullandığınızdan emin olun. <p><p>Kapasite ve limit bilgilerini, [Azure Logic Apps Içindeki Ise sınırlarına](logic-apps-limits-and-config.md#integration-service-environment-ise)bakın. |
|||

### <a name="included-at-no-extra-cost"></a>Ek ücret ödemeden dahil değildir

| Öğeler | Description |
|-------|-------------|
| [Yerleşik](../connectors/built-in.md) Tetikleyiciler ve eylemler | **Çekirdek** etiketi görüntüleyin ve Logic Apps ile aynı Ise 'de çalıştırın. |
| [Standart bağlayıcılar](../connectors/managed.md) <p><p>[Kurumsal bağlayıcılar](../connectors/managed.md#enterprise-connectors) | - **Ise** etiketini görüntüleyen yönetilen bağlayıcılar, şirket içi veri ağ geçidi olmadan çalışacak şekilde özel olarak tasarlanmıştır ve Logic Apps ile aynı çalışma alanında çalışır. ISE fiyatlandırması, istediğiniz kadar kurumsal bağlantı içerir. <p><p>-ISE etiketini görüntülememe dışı bağlayıcılar çok kiracılı Logic Apps hizmetinde çalışır. Ancak, ıSE fiyatlandırması bir ıSE 'de çalışan Logic Apps için bu yürütmeleri içerir. |
| [Döngüler](logic-apps-control-flow-loops.md) içindeki eylemler | ISE fiyatlandırması, çalıştıran her döngü döngüsü için bir döngüde çalışan her eylemi içerir. <p><p>Örneğin, bir listeyi işleyen eylemleri içeren bir "for each" döngüsüne sahip olduğunuzu varsayalım. Toplam eylem yürütmelerinin sayısını almak için, liste öğelerinin sayısını döngüdeki eylem sayısıyla çarpın ve döngüyü başlatan eylemi ekleyin. Bu nedenle, 10 öğe listesi için hesaplama, 11 işlem yürütmelerinin sonucu olan (10 * 1) + 1 ' dir. |
| Yeniden deneme sayısı | En basit özel durumları ve hataları işlemek için, Tetikleyiciler ve eylemler üzerinde desteklenmiş bir [yeniden deneme ilkesi](logic-apps-exception-handling.md#retry-policies) ayarlayabilirsiniz. ISE fiyatlandırması, özgün istekle birlikte yeniden denemeler içerir. |
| [Veri saklama ve depolama tüketimi](#data-retention) | Bir ıSE 'de Logic Apps, bekletme ve depolama maliyetlerine tabi değildir. |
| [Tümleştirme hesaplarına genel bakış](#integration-accounts) | Ek maliyet olmadan ıSE SKU 'SU temelinde tek bir tümleştirme hesabı katmanının kullanımını içerir. |
|||

Sınır bilgileri için [Azure Logic Apps Içindeki Ise sınırları](logic-apps-limits-and-config.md#integration-service-environment-ise)bölümüne bakın.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Tümleştirme hesapları

[Tümleştirme hesabı](../logic-apps/logic-apps-pricing.md#integration-accounts) , [EDI](logic-apps-enterprise-integration-b2b.md) ve [XML işleme](logic-apps-enterprise-integration-xml.md) özelliklerini kullanan B2B tümleştirme çözümlerini araştırabilmeniz, derlemenize ve test edebilmeniz için mantıksal uygulamaları oluşturduğunuz ve bu uygulamalara bağlanan ayrı bir kaynaktır.

Azure Logic Apps, mantıksal uygulamalarınızın tüketim tabanlı veya ıSE tabanlı olup olmadığına bağlı olarak fiyatlandırma ve [faturalandırma modelinde](logic-apps-pricing.md#integration-accounts) [farklılık](https://azure.microsoft.com/pricing/details/logic-apps/) gösteren bu tümleştirme hesabı düzeylerini veya katmanlarını sunar:

| Katman | Description |
|------|-------------|
| **Temel** | Yalnızca ileti işlemenin veya daha büyük bir iş varlığıyla ticari iş ortağı ilişkisine sahip küçük bir iş ortağı olarak hareket etmesini istediğiniz senaryolar için. <p><p>Logic Apps SLA tarafından desteklenir. |
| **Standart** | Daha karmaşık B2B ilişkilerinizin olduğu ve yönetmeniz gereken varlıkların sayısı olan senaryolar için. <p><p>Logic Apps SLA tarafından desteklenir. |
| **Ücretsiz** | Araştırmacı senaryolar için, üretim senaryolarından değil. Bu katmanın bölge kullanılabilirliği, üretilen iş ve kullanım sınırları vardır. Örneğin ücretsiz katman yalnızca Azure 'daki genel bölgeler için kullanılabilir (örneğin, Batı ABD veya Güneydoğu Asya), ancak [Azure Çin 21Vianet](/azure/china/overview-operations) veya [Azure Kamu](../azure-government/documentation-government-welcome.md)için değildir. <p><p>**Not**: Logic Apps SLA tarafından desteklenmez. |
|||

Tümleştirme hesabı limitleri hakkında daha fazla bilgi için bkz. [Azure Logic Apps Için sınırlar ve yapılandırma](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits):

* [Azure aboneliği başına tümleştirme hesabı sınırları](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Tümleştirme hesabı başına çeşitli yapıtların sınırları](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). Yapıtlar, ticari iş ortakları, sözleşmeler, Haritalar, şemalar, derlemeler, sertifikalar, toplu iş yapılandırması vb. içerir.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Tüketim tabanlı Logic Apps için tümleştirme hesapları

Tümleştirme hesapları, kullandığınız hesap katmanını temel alan sabit bir [tümleştirme hesabı fiyatı](https://azure.microsoft.com/pricing/details/logic-apps/) kullanılarak faturalandırılır.

### <a name="ise-based-logic-apps"></a>ISE tabanlı mantıksal uygulamalar

Ek bir ücret ödemeden ıSE, Ise SKU 'nuzu temel alan tek bir tümleştirme hesabı içerir. Ek bir maliyet için, [Toplam Ise sınırına](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)kadar kullanmak üzere Ise için daha fazla tümleştirme hesabı oluşturabilirsiniz. Bu konunun önceki kısımlarında yer aldığı [Ise fiyatlandırma modeli](#fixed-pricing) hakkında daha fazla bilgi edinin.

| ıSE SKU 'SU | Dahil edilen tümleştirme hesabı | Ek maliyet |
|---------|------------------------------|-----------------|
| **Premium** | Tek bir [Standart](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) tümleştirme hesabı | En fazla 19 daha standart hesap. Ücretsiz veya temel hesaba izin verilmez. |
| **Geliştirici** | Tek bir [ücretsiz](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) tümleştirme hesabı | Ücretsiz hesabınız varsa, en fazla 19 daha standart hesap veya ücretsiz bir hesabınız yoksa 20 Toplam standart hesap. Temel hesaplara izin verilmez. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Veri saklama ve depolama tüketimi

Mantıksal uygulamanızın çalışma geçmişinde tüm giriş ve çıkış, bu uygulamanın [çalışma süresi ve geçmiş saklama süresi](logic-apps-limits-and-config.md#run-duration-retention-limits)temel alınarak depolanır ve ölçülecektir.

* Çok kiracılı Logic Apps hizmetindeki Logic Apps için, depolama tüketimi, **fiyatlandırma ayrıntıları** tablosu altında [Logic Apps fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/logic-apps)bulabileceğiniz sabit bir fiyatla faturalandırılır.

* Bir mantıksal uygulamalar için, depolama tüketimi veri saklama maliyetlerine tabi değildir.

Depolama tüketimi kullanımını izlemek için bkz. [yürütmeler ve depolama alanı tüketimi için ölçümleri görüntüleme](plan-manage-costs.md#monitor-billing-metrics).

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Şirket içi veri ağ geçidi

Şirket [içi veri ağ geçidi](../logic-apps/logic-apps-gateway-install.md) , mantıksal uygulamalarınızın belirli ağ geçidi ile desteklenen bağlayıcıları kullanarak şirket içi verilere erişebilmeleri için oluşturduğunuz ayrı bir kaynaktır. Ağ Geçidi üzerinden çalışan bağlayıcılar ücretlendirilir, ancak ağ geçidinin kendisi ücret ödemez.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>Devre dışı mantıksal uygulamalar

Devre dışı bırakıldıklarında yeni örnekler oluşturamadığı için devre dışı bırakılan Logic Apps ücretlendirilmez. Mantıksal uygulamayı devre dışı bıraktıktan sonra, çalışmakta olan tüm örnekler tamamen durmadan önce biraz zaman alabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Logic Apps maliyetlerini planlayın ve yönetin](plan-manage-costs.md)
