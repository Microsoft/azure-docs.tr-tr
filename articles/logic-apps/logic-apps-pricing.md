---
title: Fiyatlandırma & faturalandırma modeli-Azure Logic Apps
description: Fiyatlandırma ve faturalandırma modelinin Azure Logic Apps için nasıl çalıştığı hakkında genel bakış
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: f4651e40a0f4269dc5c8a2fcd4c0e25c1e9bbb3c
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73045101"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure Logic Apps için fiyatlandırma modeli

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) , bulutta ölçeklenebilen otomatikleştirilmiş Tümleştirme iş akışları oluşturmanıza ve çalıştırmanıza yardımcı olur. Bu makalede, faturalandırma ve fiyatlandırmanın Azure Logic Apps nasıl çalıştığı açıklanır. Fiyatlandırma fiyatları için bkz. [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Tüketim fiyatlandırma modeli

Genel veya "küresel" Azure Logic Apps hizmetinde çalışan yeni mantıksal uygulamalar için yalnızca kullandığınız kadar ödersiniz. Bu mantıksal uygulamalar, tüketim tabanlı bir plan ve fiyatlandırma modeli kullanır. Mantıksal uygulamanızda, her adım bir eylemdir ve mantıksal uygulamanızda çalışan tüm eylemlere Azure Logic Apps.

Örneğin, eylemler şunları içerir:

* Özel eylemler olan Tetikleyiciler. Tüm mantıksal uygulamalar ilk adım olarak bir tetikleyici gerektirir.
* ["Yerleşik" veya http gibi yerel eylemler](../connectors/apis-list.md#built-in) , Azure işlevlerine yapılan çağrılar ve API Management vb.)
* Outlook 365, Dropbox vb. gibi [yönetilen bağlayıcılar](../connectors/apis-list.md##managed-connectors) için çağrılar
* Döngüler, koşullu deyimler vb. gibi denetim akışı adımları

Standart [Bağlayıcılar](../connectors/apis-list.md#managed-connectors) [standart bağlayıcı fiyatı](https://azure.microsoft.com/pricing/details/logic-apps)üzerinden ücretlendirilir. Genel kullanıma sunulan [Kurumsal bağlayıcılar](../connectors/apis-list.md#managed-connectors) [Kurumsal bağlayıcı fiyatı](https://azure.microsoft.com/pricing/details/logic-apps)üzerinden ücretlendirilir, ancak genel önizleme kurumsal bağlayıcıları [standart bağlayıcı fiyatı](https://azure.microsoft.com/pricing/details/logic-apps)üzerinden ücretlendirilir.

Faturalandırmayla ilgili [Tetikleyiciler](#triggers) ve [Eylemler](#actions)için nasıl çalıştığı hakkında daha fazla bilgi edinin.

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Sabit fiyatlandırma modeli

Bir [ *tümleştirme hizmeti ortamı* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) , bir Azure sanal ağındaki kaynaklara erişebilen Logic Apps oluşturmanız ve çalıştırmanız için özel, yalıtılmış ve ayrılmış bir yöntem sağlar. Bir ıSE içinde çalışan yeni Logic Apps için, bu yetenekler için [sabit bir aylık fiyat](https://azure.microsoft.com/pricing/details/logic-apps) ödeyin:

* [Yerleşik Tetikleyiciler ve eylemler](../connectors/apis-list.md#built-in)

* [Standart bağlayıcılar](../connectors/apis-list.md#managed-connectors)

* İstediğiniz kadar bağlantı olan [Kurumsal bağlayıcılar](../connectors/apis-list.md#enterprise-connectors)

* [Ise SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)'nuzu temel alarak, ek ücret ödemeden [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) kullanımı:

  * **PREMIUM SKU**: tek bir [Standart katman](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) tümleştirme hesabı

  * **Geliştirici SKU 'su**: tek bir [ücretsiz katman](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) tümleştirme hesabı

  Her ıSE SKU 'SU 5 Toplam tümleştirme hesabıyla sınırlıdır. Ek bir maliyet için, ıSE SKU 'nuzu temel alan daha fazla tümleştirme hesabına sahip olabilirsiniz:

  * **PREMIUM SKU**: en fazla dört standart hesap. Ücretsiz veya temel hesap yok.

  * **Geliştirici SKU 'su**: 4 ' e kadar standart hesap veya toplam 5 Standart hesap. Temel hesap yok.

Tümleştirme hesabı limitleri hakkında daha fazla bilgi için bkz. [Logic Apps sınırları ve yapılandırması](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Bu konunun ilerleyen kısımlarında [tümleştirme hesabı katmanları ve fiyatlandırma modelleri](#integration-accounts) hakkında daha fazla bilgi edinebilirsiniz.

Premium ıSE SKU 'SU için, temel birim sabit kapasiteye sahiptir, bu nedenle daha fazla işleme gerekiyorsa, oluşturma sırasında veya [daha sonra daha fazla ölçek birimi ekleyebilirsiniz](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity). Geliştirici ıSE SKU 'SU daha fazla ölçek birimi ekleme yeteneğine sahip değildir. ISE 'de çalışan Logic Apps veri bekletme maliyetlerine tabi değildir.

> [!NOTE]
> Bir ıSE 'de, yerleşik Tetikleyiciler ve eylemler **temel** etiketi görüntüler ve Logic Apps ile aynı Ise 'de çalışır. **Ise** etiketini görüntüleyen standart ve kurumsal bağlayıcılar, Logic Apps ile aynı çalışma alanında çalışır. ISE etiketini görüntülememe ve genel Logic Apps hizmetinde çalışan bağlayıcılar.

Fiyatlandırma fiyatları için bkz. [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="connectors"></a>

## <a name="connectors"></a>Bağlayıcılar

Azure Logic Apps bağlayıcılar, mantıksal uygulamanızın, [Tetikleyiciler](#triggers), [Eylemler](#actions)veya her ikisini de sağlayarak bulutta veya şirket içinde uygulamalara, hizmetlere ve sistemlere erişmesine yardımcı olur. Bağlayıcılar, standart ya da kurumsal olarak sınıflandırılır. Bu bağlayıcılar hakkında genel bilgi için bkz. [bağlayıcılar Azure Logic Apps](../connectors/apis-list.md). Logic Apps 'te kullanmak istediğiniz REST API 'Leri için önceden oluşturulmuş bağlayıcılar yoksa, bu REST API 'Leri etrafında yalnızca sarmalayıcılar olan [özel bağlayıcılar](https://docs.microsoft.com/connectors/custom-connectors)oluşturabilirsiniz. Özel Bağlayıcılar standart bağlayıcı olarak faturalandırılır. Aşağıdaki bölümlerde, tetikleyicilerinin ve eylemlerin nasıl çalıştığı hakkında daha fazla bilgi sağlanmaktadır.

<a name="triggers"></a>

## <a name="triggers"></a>Tetikleyiciler

Tetikleyiciler, belirli bir olay gerçekleştiğinde mantıksal uygulama örneği oluşturan özel eylemlerdir. Tetikler, mantıksal uygulamanın nasıl ölçüldiğini etkileyen farklı yollarla çalışır. Azure Logic Apps var olan çeşitli tetikleyici türleri şunlardır:

* **Yoklama tetikleyicisi**: Bu tetikleyici, mantıksal uygulama örneği oluşturma ve iş akışını başlatma ölçütlerini karşılayan iletiler için bir uç noktayı sürekli olarak denetler. Mantıksal uygulama örneği oluşturulmasa bile, her yoklama isteğini bir yürütme olarak Logic Apps. Yoklama aralığını belirtmek için, mantıksal uygulama Tasarımcısı aracılığıyla tetikleyiciyi ayarlayın.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Web kancası tetikleyicisi**: Bu tetikleyici, bir istemcinin belirli bir uç noktaya istek göndermesini bekler. Web kancası uç noktasına gönderilen her istek bir eylem yürütmesi olarak sayılır. Örneğin, Istek ve HTTP Web kancası tetikleyicisi, Web kancası tetikleyicilerinde bulunur.

* **Yinelenme tetikleyicisi**: Bu tetikleyici, tetikleyicide ayarladığınız yineleme aralığına göre bir mantıksal uygulama örneği oluşturur. Örneğin, her üç günde bir veya daha karmaşık bir zamanlamaya göre çalışan bir yinelenme tetikleyicisi ayarlayabilirsiniz.

<a name="actions"></a>

## <a name="actions"></a>Eylemler

Yerel eylemler olarak HTTP gibi "yerleşik" eylemleri Azure Logic Apps. Örneğin, yerleşik Eylemler HTTP çağrılarını, Azure Işlevlerinden veya API Management çağrıları ve koşullar, döngüler ve Switch deyimleri gibi denetim akışı adımlarını içerir. Her eylemin kendi eylem türü vardır. Örneğin, [bağlayıcıları](https://docs.microsoft.com/connectors) çağıran eylemler "ApiConnection" türüne sahiptir. Bu bağlayıcılar, kendi [fiyatlandırmasına](https://azure.microsoft.com/pricing/details/logic-apps)göre ölçülen standart veya kurumsal bağlayıcılar olarak sınıflandırılır. *Önizlemede* kurumsal bağlayıcılar standart bağlayıcı olarak ücretlendirilir.

Tüm başarılı ve başarısız eylemleri yürütmeler olarak Azure Logic Apps. Ancak Logic Apps bu eylemleri ölçer:

* Karşılanmayan koşullar nedeniyle atlanan eylemler
* Mantıksal uygulama bitmeden önce durdurulduğundan çalıştırmayan eylemler

Döngülerde çalışan eylemler için Azure Logic Apps döngüdeki her döngü için her bir eylemi sayar. Örneğin, bir listeyi işleyen bir "for each" döngüsüne sahip olduğunuzu varsayalım. , Liste öğelerinin sayısını döngüdeki eylem sayısıyla çarparak ve döngüyü başlatan eylemi eklediğinden, bu döngüde bir eyleme Logic Apps. Bu nedenle, 10 öğe listesi için hesaplama, 11 işlem yürütmelerinin sonucu olan (10 * 1) + 1 ' dir.

## <a name="disabled-logic-apps"></a>Devre dışı mantıksal uygulamalar

Devre dışı bırakıldıklarında yeni örnekler oluşturamadığı için devre dışı bırakılan Logic Apps ücretlendirilmez. Mantıksal uygulamayı devre dışı bıraktıktan sonra, çalışmakta olan tüm örnekler tamamen durmadan önce biraz zaman alabilir.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Tümleştirme hesapları

[Sabit bir fiyatlandırma modeli](https://azure.microsoft.com/pricing/details/logic-apps) , Azure Logic Apps [B2B ve EDI](logic-apps-enterprise-integration-b2b.md) ve [XML işleme](logic-apps-enterprise-integration-xml.md) özelliklerini hiçbir ek ücret ödemeden keşfedebileceğiniz, geliştirebileceğiniz ve test ettiğiniz [tümleştirme hesapları](logic-apps-enterprise-integration-create-integration-account.md) için geçerlidir. Her Azure aboneliğinin [belirli bir tümleştirme hesabı sınırı](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)olabilir. Her bir tümleştirme hesabı, ticari iş ortakları, anlaşmalar, Haritalar, şemalar, derlemeler, sertifikalar, toplu iş yapılandırması vb. dahil olmak üzere belirli [yapıt limitini](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)saklayabilir.

Azure Logic Apps, ücretsiz, temel ve standart tümleştirme hesapları sunmaktadır. Temel ve Standart katmanlar Logic Apps hizmet düzeyi sözleşmesi (SLA) tarafından desteklenir, ancak ücretsiz katman bir SLA tarafından desteklenmez ve üretilen iş ve kullanım sınırları vardır. Ücretsiz katman tümleştirme hesapları haricinde, her bir Azure bölgesinde birden fazla tümleştirme hesabına sahip olabilirsiniz. Fiyatlandırma fiyatları için bkz. [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps/).

[Premium veya geliştirici](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)olarak bir [ *tümleştirme hizmeti ortamınız* (ıSE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)varsa, Ise 'niz 5 Toplam tümleştirme hesabına sahip olabilir. Sabit fiyatlandırma modelinin bir ıSE için nasıl çalıştığını öğrenmek için, bu konunun önceki [sabit fiyatlandırma modeli](#fixed-pricing) bölümüne bakın. Fiyatlandırma fiyatları için bkz. [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps).

Ücretsiz, temel veya standart tümleştirme hesabı arasından seçim yapmak için bu kullanım örneği açıklamalarını gözden geçirin:

* **Ücretsiz**: bir üretim senaryosu değil keşif senaryoları denemek istediğinizde

* **Temel**: yalnızca ileti işleme yapmak veya daha büyük bir iş varlığıyla bir ticari iş ortağı ilişkisine sahip küçük bir iş ortağı olarak hareket etmek istediğinizde

* **Standart**: daha karmaşık B2B ilişkilerine sahip olduğunuzda ve yönetmeniz gereken varlıkların sayısı arttıkça

<a name="data-retention"></a>

## <a name="data-retention"></a>Veri saklama

Bir tümleştirme hizmeti ortamında (ıSE) çalışan Logic Apps dışında, mantıksal uygulamanızın çalışma geçmişinde saklanan tüm giriş ve çıkışlar, mantıksal uygulamanın [çalışma bekletme dönemine](logic-apps-limits-and-config.md#run-duration-retention-limits)göre faturalandırılır. ISE 'de çalışan Logic Apps veri bekletme maliyetlerine tabi değildir. Fiyatlandırma fiyatları için bkz. [Logic Apps fiyatlandırması](https://azure.microsoft.com/pricing/details/logic-apps).

Mantıksal uygulamanızın depolama tüketimini izlemenize yardımcı olması için şunları yapabilirsiniz:

* Mantıksal uygulamanızın aylık kullandığı GB cinsinden depolama birimi sayısını görüntüleyin.
* Mantıksal uygulamanızın çalıştırma geçmişinde belirli bir eylemin giriş ve çıkışları için boyutları görüntüleyin.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Mantıksal uygulama depolama tüketimini görüntüleme

1. Azure portal, mantıksal uygulamanızı bulun ve açın.

1. Mantıksal uygulamanızın menüsünde, **izleme**altında **ölçümler**' i seçin.

1. Sağ bölmedeki **grafik başlığı**altında, **ölçüm** listesinden, **depolama tüketimi yürütmeleri için faturalandırma kullanımı**' nı seçin.

   Bu ölçüm, faturalandırılan ayda GB cinsinden depolama tüketim birimi sayısını sağlar.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Eylem giriş ve çıkış boyutlarını görüntüleme

1. Azure portal, mantıksal uygulamanızı bulun ve açın.

1. Mantıksal uygulamanızın menüsünde **genel bakış**' ı seçin.

1. Sağ bölmede, çalışma **geçmişi**altında, denetlemek istediğiniz girişleri ve çıkışları içeren çalıştırmayı seçin.

1. **Mantıksal uygulama çalıştırma**altında, **Çalıştır ayrıntıları**' nı seçin.

1. **Mantıksal uygulama çalıştırma ayrıntıları** bölmesinde, eylemler tablosunda, her eylemin durumunu ve süresini listeleyen, görüntülemek istediğiniz eylemi seçin.

1. **Mantıksal uygulama eylemi** bölmesinde, bu eylemin giriş ve **çıkış bağlantısı**altında sırasıyla görüntülenecek olan **giriş ve çıkış** boyutlarını bulun.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Logic Apps hakkında daha fazla bilgi edinin](logic-apps-overview.md)
* [İlk mantıksal uygulamanızı oluşturma](quickstart-create-first-logic-app-workflow.md)
