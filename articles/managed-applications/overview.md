---
title: Azure yönetilen uygulamalarına genel bakış | Microsoft Docs
description: Müşterilerin dağıtımı ve çalışması için kolay olan bulut çözümleri sağlayan Azure yönetilen uygulamalar için kavramları açıklar.
author: tfitzmac
ms.service: managed-applications
ms.topic: overview
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: fa37536b800203e981a841d049dc385640733f92
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332680"
---
# <a name="azure-managed-applications-overview"></a>Azure yönetilen uygulamalarına genel bakış

Azure yönetilen uygulamaları, tüketicilerin kolayca dağıtıp çalıştırabileceği bulut çözümleri sunmanızı sağlar. Altyapıyı uygular ve sürekli destek sağlarsınız. Yönetilen bir uygulamayı tüm müşterilerin kullanımına sunmak için Azure markette yayımlayın. Uygulamayı yalnızca kuruluşunuzdaki kullanıcıların kullanımına sunmak için bir iç katalogda yayımlayın. 

Yönetilen bir uygulama, tek bir önemli farkla, Market’teki bir çözüm şablonuna benzer. Yönetilen bir uygulamada kaynaklar, uygulamanın yayımcısı tarafından yönetilen bir kaynak grubuna dağıtılır. Kaynak grubu, tüketicinin aboneliğinde mevcuttur ancak yayımcının kiracısındaki bir kimlik, kaynak grubuna erişime sahiptir. Yayımcı olarak, çözümün sürekli desteği için maliyeti belirtirsiniz.

## <a name="advantages-of-managed-applications"></a>Yönetilen uygulamaların avantajları

Yönetilen uygulamalar, çözümlerinizi kullanan tüketicilerin önündeki engelleri azaltır. Çözümünüzü kullanmak için bulut altyapısında uzmanlığa gerek yoktur. Tüketiciler, kritik kaynaklara sınırlı erişime sahiptir ve yönetim sırasında hata yapma konusunda endişelenmeleri gerekmez. 

Yönetilen uygulamalar, tüketicilerinizle sürekli bir ilişki kurmanızı sağlar. Uygulamayı yönetme koşullarını siz tanımlarsınız ve tüm ücretler Azure faturalama üzerinden işleme alınır.

Müşteriler bu yönetilen uygulamaları aboneliklerinde dağıtsa da, uygulamaların bakım, güncelleştirme ve servisinden sorumlu olmazlar. Tüm müşterilerin onaylanan sürümleri kullandığından emin olabilirsiniz. Müşteriler, bu uygulamaları yönetmek için uygulamaya özgü bir etki alanı geliştirmek zorunda değildir. Müşteriler, uygulamalarla ilgili sorun giderme ve sorunları tanılama konusunda endişelenmeye gerek olmadan uygulama güncelleştirmelerini otomatik olarak alır. 

BT ekipleri için yönetilen uygulamalar, kuruluştaki kullanıcılara önceden onaylanmış çözümler sunmanızı sağlar. Bu çözümlerin kuruluş standartlarıyla uyumlu olduğunu bilirsiniz.

Yönetilen uygulamalar, [Azure kaynakları için yönetilen kimlikleri](./publish-managed-identity.md)destekler.

## <a name="types-of-managed-applications"></a>Yönetilen uygulamaların türleri

Yönetilen uygulamanızı harici veya dahili olarak yayımlayabilirsiniz.

![Dahili veya harici olarak yayımlama](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>Hizmet kataloğu

Hizmet kataloğu, bir kuruluştaki kullanıcılar için onaylı çözümleri içeren bir dahili katalogdur. Bazı kuruluş standartlarının karşılanması için, kuruluşlar için çözümler sunan kataloğu kullanabilirsiniz. Çalışanlar, kataloğu kullanarak BT bölümleri tarafından önerilen ve onaylanan uygulamaları kolayca bulabilir. Kuruluşlarındaki diğer kişilerin kendileriyle paylaştıkları yönetilen uygulamaları görebilirler.

Bir Hizmet Kataloğu yönetilen uygulaması yayımlama hakkında bilgi için bkz. [Hizmet kataloğu uygulaması oluşturma](publish-service-catalog-app.md).

### <a name="marketplace"></a>Market

Hizmetlerini faturalandırmak isteyen satıcılar, Azure market aracılığıyla bir yönetilen uygulamayı kullanılabilir hale getirebilir. Satıcı bir uygulamayı yayımladıktan sonra, uygulama kuruluşun dışındaki kullanıcıların kullanımına sunulur. Bu yaklaşımla, yönetilen hizmet sağlayıcıları (MSP), bağımsız yazılım satıcıları (ISV) ve sistem tümleştiricileri (SI) tüm Azure müşterilerine çözümlerini sunabilir.

Market'te yönetilen uygulama yayımlama hakkında daha fazla bilgi için [Market uygulaması oluşturma](publish-marketplace-app.md) konusunu inceleyin.

## <a name="resource-groups-for-managed-applications"></a>Yönetilen uygulamalar için kaynak grupları

Genellikle, yönetilen bir uygulamanın kaynakları iki kaynak grubunda bulunur. Tüketici, bir kaynak grubunu yönetirken yayımcı diğer kaynak grubunu yönetir. Yayımcı, yönetilen uygulamayı tanımlarken erişim düzeylerini belirtir. Yayımcı, bir zaman aralığı ile kısıtlanan bir atama için kalıcı bir rol ataması ya da [tam zamanında erişim](request-just-in-time-access.md) isteğinde bulunabilir.

[Veri işlemlerine](../role-based-access-control/role-definitions.md) yönelik erişim kısıtlama şu anda Azure’daki tüm veri sağlayıcıları için desteklenmiyor.

Aşağıdaki görüntüde, yayımcının yönetilen kaynak grubu için sahip rolünü istediği bir senaryo gösterilmektedir. Yayımcı, tüketici için bu kaynak grubuna salt okunur bir kilit yerleştirmiştir. Yönetilen kaynak grubuna erişim izni verilen yayımcı kimlikleri kilitli olmaz.

![Kaynak grubu erişimi](./media/overview/access.png)

### <a name="application-resource-group"></a>Uygulama kaynak grubu

Bu kaynak grubu, yönetilen uygulama örneğini tutar. Bu kaynak grubu yalnızca bir kaynak içerebilir. Yönetilen uygulamanın kaynak türü **Microsoft.Solutions/applications** şeklindedir.

Tüketici, kaynak grubuna tam erişime sahiptir ve yönetilen uygulamanın yaşam döngüsünü yönetmek için bu erişimi kullanır.

### <a name="managed-resource-group"></a>Yönetilen kaynak grubu

Bu kaynak grubu, yönetilen uygulamanın gerektirdiği tüm kaynakları tutar. Örneğin, bu kaynak grubu, çözümün sanal makinelerini, depolama hesaplarını ve sanal ağlarını içerir. Tüketici, yönetilen uygulamanın kaynaklarını tek tek yönetmediği için bu kaynak grubuna sınırlı erişime sahiptir. Yayımcının bu kaynak grubuna erişimi, yönetilen uygulama tanımında belirtilen role karşılık gelir. Örneğin, yayımcı bu kaynak grubu için Sahip veya Katkıda Bulunan rolünü isteyebilir. Erişim kalıcı veya belirli bir zaman ile sınırlıdır.

[Yönetilen uygulamayı Market](publish-marketplace-app.md)'te yayımlarken, yayımcı tüketicilere yönetilen kaynak grubundaki kaynaklar üzerinde belirli eylemleri gerçekleştirme yeteneği verebilir. Örneğin, Yayımcı, tüketicilerin sanal makineleri yeniden başlatabileceği belirtebilir. Okuma eylemlerinin ötesinde diğer tüm eylemler yine de reddedilir.

Tüketici yönetilen uygulamayı sildiğinde, yönetilen kaynak grubu da silinir.

## <a name="azure-policy"></a>Azure İlkesi

Yönetilen uygulamanıza [Azure İlkesi](../governance/policy/overview.md) uygulayabilirsiniz. Yönetilen uygulamanızın dağıtılan örneklerinin veri ve güvenlik gereksinimlerini yerine getirdiğinden emin olmak için ilkeleri uygularsınız. Uygulamanız hassas verilerle etkileşime geçiyorsa, bu etkileşimin nasıl koruma altına alınacağını değerlendirdiğinizden emin olun. Örneğin uygulamanız Office 365’teki verilerle etkileşimli çalışıyorsa veri şifrelemenin etkin olduğunu doğrulayan bir ilke uygulayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede yönetilen uygulamaları kullanmanın avantajları hakkında bilgi edindiniz. Yönetilen uygulama tanımı oluşturmak için bir sonraki makaleye gidin.

> [!div class="nextstepaction"]
> [Hızlı başlangıç: Azure yönetilen uygulaması tanımı yayımlama](publish-managed-app-definition-quickstart.md)
