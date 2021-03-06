---
title: Azure Danışmanı’na Giriş
description: Azure dağıtımlarınızı iyileştirmek için Azure Advisor 'ı kullanın.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 12e56bf44a29a32b2149bca14f7c99f319c9c4ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91405216"
---
# <a name="introduction-to-azure-advisor"></a>Azure Danışmanı’na Giriş

Azure Advisor 'ın temel özellikleri hakkında bilgi edinin ve sık sorulan soruların yanıtlarını alın.

## <a name="what-is-advisor"></a>Advisor nedir?
Danışman, Azure dağıtımlarınızı iyileştirmek için en iyi yöntemleri izlemenize yardımcı olan kişiselleştirilmiş bir bulut danışmanıdır. Kaynak yapılandırmanızı ve kullanım telemetrinizi analiz eder ve ardından maliyet verimliliğini, performansı, güvenilirliği (eskiden yüksek kullanılabilirlik adı verilir) ve Azure kaynaklarınızın güvenliğini artırmanıza yardımcı olabilecek çözümler önerir.

Advisor ile şunları yapabilirsiniz:
* Proaktif, eyleme dönüştürülebilir ve kişiselleştirilmiş en iyi yöntem önerileri alabilirsiniz. 
* Genel Azure harcamalarınızı azaltmaya yönelik fırsatları belirlerken kaynaklarınızın performansını, güvenliğini ve güvenilirliğini artırabilirsiniz.
* İşlem sırasında önerilen eylemler halinde öneriler alabilirsiniz.

[Azure Portal](https://aka.ms/azureadvisordashboard)aracılığıyla Advisor 'a erişebilirsiniz. [Portalda](https://portal.azure.com)oturum açın, gezinti menüsünde **Advisor** 'ı bulun veya **tüm hizmetler** menüsünde aramak için arama yapın.

Danışman panosu, tüm abonelikleriniz için kişiselleştirilmiş öneriler görüntüler.  Belirli abonelikler ve kaynak türleri için önerileri görüntüleme için filtre uygulayabilirsiniz.  Öneriler beş kategoriye ayrılmıştır: 

* **Güvenilirlik (eski adıyla yüksek kullanılabilirlik olarak biliniyordu)**: işle ilgili kritik uygulamalarınızın sürekliliğini sağlamak ve geliştirmek için. Daha fazla bilgi için bkz. [Advisor güvenilirlik önerileri](advisor-high-availability-recommendations.md).
* **Güvenlik**: güvenlik ihlallerine neden olabilecek tehditleri ve güvenlik açıklarını algılamak için. Daha fazla bilgi için bkz. [Advisor güvenlik önerileri](advisor-security-recommendations.md).
* **Performans**: uygulamalarınızın hızını artırmak için. Daha fazla bilgi için bkz. [Advisor performans önerileri](advisor-performance-recommendations.md).
* **Maliyet**: Genel Azure harcamalarınızı iyileştirmek ve azaltmak için. Daha fazla bilgi için bkz. [danışman maliyet önerileri](advisor-cost-recommendations.md).
* **Işletimsel üstün**: işlem ve iş akışı verimliliği, kaynak yönetilebilirlik ve dağıtım en iyi yöntemleri elde etmenize yardımcı olmak. . Daha fazla bilgi için bkz. [danışman Işletimsel üstün önerileri](advisor-operational-excellence-recommendations.md).

  ![Danışman öneri türleri](./media/advisor-overview/advisor-dashboard.png)

Bu kategorideki önerilerin listesini göstermek için bir kategoriye tıklayabilir ve bunun hakkında daha fazla bilgi edinmek için bir öneri seçebilirsiniz.  Ayrıca, bir fırsattan yararlanmak veya sorunu çözmek için gerçekleştirebileceğiniz eylemler hakkında bilgi edinebilirsiniz.

![Danışman öneri kategorisi](./media/advisor-overview/advisor-ha-category-example.png) 

Öneriyi uygulamak için öneri için önerilen eylemi seçin.  Öneriyi uygulamanıza yardımcı olan bir basit arabirim açılır. Bu, size uygulama konusunda size yardımcı olan belgeleri size ifade etmenizi sağlar.  Bir öneri uygulandıktan sonra, Advisor 'ın bunu tanıması bir gün sürebilir.

Bir öneri üzerinde acil eylem yapmak istemiyorsanız, belirli bir süre boyunca erteleyebilir veya kapatabilirsiniz.  Belirli bir abonelik veya kaynak grubu için öneriler almak istemiyorsanız, Advisor 'ı yalnızca belirtilen abonelikler ve kaynak grupları için öneriler oluşturacak şekilde yapılandırabilirsiniz.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="how-do-i-access-advisor"></a>Nasıl yaparım? Access Advisor?
[Azure Portal](https://aka.ms/azureadvisordashboard)aracılığıyla Advisor 'a erişebilirsiniz. [Portalda](https://portal.azure.com)oturum açın, gezinti menüsünde **Advisor** 'ı bulun veya **tüm hizmetler** menüsünde aramak için arama yapın.

Danışman önerilerini sanal makine kaynak arabirimi aracılığıyla da görüntüleyebilirsiniz. Bir sanal makine seçin ve sonra menüdeki danışman önerileri ' ne gidin. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Advisor 'a erişmek için hangi izinlere ihtiyacım var?
 
Danışman önerilerine bir abonelik, kaynak grubu veya kaynak *sahibi*, *katkıda bulunan* veya *okuyucu* olarak erişebilirsiniz.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Danışman hangi kaynakları öneri sağlar?

Danışman Application Gateway, App Services, kullanılabilirlik kümeleri, Azure önbelleği, Azure Data Factory, MySQL için Azure veritabanı, PostgreSQL için Azure veritabanı, MariaDB için Azure veritabanı, Azure ExpressRoute, Azure Cosmos DB, Azure genel IP adresleri, Azure SYNAPSE Analytics, SQL sunucuları, depolama hesapları, Traffic Manager profilleri ve sanal makineler için öneriler sağlar.

Azure Danışmanı, [Azure Güvenlik Merkezi](../security-center/security-center-recommendations.md) 'nin önerilerini de içerir ve bu da ek kaynak türleri için öneriler içerebilir.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Öneriyi erteleyebilir veya kapatabilir miyim?

Bir öneriyi erteleyip kapatmak için **erteleme** bağlantısına tıklayın. Bir erteleme dönemi belirtebilir veya öneriyi ortadan kapatabilmeniz için **hiçbir zaman** öğesini seçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Danışman önerileri hakkında daha fazla bilgi edinmek için bkz.:

* [Danışman'ı kullanmaya başlama](advisor-get-started.md)
* [Danışman puanı](azure-advisor-score.md)
* [Advisor güvenilirlik önerileri](advisor-high-availability-recommendations.md)
* [Danışman güvenlik önerileri](advisor-security-recommendations.md)
* [Advisor performans önerileri](advisor-performance-recommendations.md)
* [Danışman maliyet önerileri](advisor-cost-recommendations.md)
* [Danışman işletimsel üstün öneriler](advisor-operational-excellence-recommendations.md)
