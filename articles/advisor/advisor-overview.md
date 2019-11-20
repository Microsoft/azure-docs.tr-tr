---
title: Azure Advisor 'a giriş | Microsoft Docs
description: Azure dağıtımlarınızı iyileştirmek için Azure Advisor 'ı kullanın.
services: advisor
documentationcenter: NA
author: sagupt
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: sagupt
ms.openlocfilehash: 1a1ed13697d838041d1ac98b966695e24c06edbe
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145358"
---
# <a name="introduction-to-azure-advisor"></a>Azure Advisor 'a giriş

Azure Advisor 'ın temel özellikleri hakkında bilgi edinin ve sık sorulan soruların yanıtlarını alın.

## <a name="what-is-advisor"></a>Advisor nedir?
Danışman, Azure dağıtımlarınızı iyileştirmek için en iyi yöntemleri izlemenize yardımcı olan kişiselleştirilmiş bir bulut danışmanıdır. Danışman, kaynak yapılandırmanızı ve kullanım telemetrinizi analiz ederek Azure kaynaklarınızın maliyet verimliliğini, performansını, yüksek kullanılabilirliğini ve güvenliğini geliştirmenize yardımcı olabilecek çözümler önerir.

Advisor ile şunları yapabilirsiniz:
* Proaktif, eyleme dönüştürülebilir ve kişiselleştirilmiş en iyi yöntem önerileri alın. 
* Genel Azure harcamalarınızı azaltmaya yönelik fırsatları belirlerken kaynaklarınızın performansını, güvenliğini ve yüksek oranda kullanılabilirliğini geliştirme.
* Önerilen eylemlerin satır içi önerilerini alın.

[Azure Portal](https://aka.ms/azureadvisordashboard)aracılığıyla Advisor 'a erişebilirsiniz. [Portalda](https://portal.azure.com)oturum açın, gezinti menüsünde **Advisor** 'ı bulun veya **tüm hizmetler** menüsünde aramak için arama yapın.

Danışman panosu, tüm abonelikleriniz için kişiselleştirilmiş öneriler görüntüler.  Belirli abonelikler ve kaynak türleri için önerileri görüntüleme için filtre uygulayabilirsiniz.  Öneriler dört kategoriye ayrılır: 

* **Yüksek kullanılabilirlik**: iş açısından kritik uygulamalarınızın sürekliliğini sağlamak ve artırmak için. Daha fazla bilgi için bkz. [danışman yüksek kullanılabilirlik önerileri](advisor-high-availability-recommendations.md).
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

### <a name="how-do-i-access-advisor"></a>Danışman’a nasıl erişirim?
[Azure Portal](https://aka.ms/azureadvisordashboard)aracılığıyla Advisor 'a erişebilirsiniz. [Portalda](https://portal.azure.com)oturum açın, gezinti menüsünde **Advisor** 'ı bulun veya **tüm hizmetler** menüsünde aramak için arama yapın.

Danışman önerilerini sanal makine kaynak arabirimi aracılığıyla da görüntüleyebilirsiniz. Bir sanal makine seçin ve sonra menüdeki danışman önerileri ' ne gidin. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Danışman’a erişmek için sahip olmam gereken izinler nelerdir?
 
Danışman önerilerine bir aboneliğin *sahibi*, *katkıda bulunan*veya *okuyucu* olarak erişebilirsiniz.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Danışman hangi kaynakları öneri sağlar?

Danışman Application Gateway, App Services, kullanılabilirlik kümeleri, Azure önbelleği, Azure Data Factory, MySQL için Azure veritabanı, PostgreSQL için Azure veritabanı, MariaDB için Azure veritabanı, Azure ExpressRoute, Azure Cosmos DB, Azure genel IP adresleri, SQL veri ambarı, SQL sunucuları, depolama hesapları, Traffic Manager profilleri ve sanal makineler.

Azure Danışmanı, [Azure Güvenlik Merkezi](https://docs.microsoft.com/azure/security-center/security-center-recommendations) 'nin önerilerini de içerir ve bu da ek kaynak türleri için öneriler içerebilir.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Öneriyi erteleyebilir veya kapatabilir miyim?

Bir öneriyi erteleyip kapatmak için **erteleme** bağlantısına tıklayın. Bir erteleme dönemi belirtebilir veya öneriyi ortadan kapatabilmeniz için **hiçbir zaman** öğesini seçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Danışman önerileri hakkında daha fazla bilgi edinmek için bkz.:

* [Danışman’ı kullanmaya başlama](advisor-get-started.md)
* [Danışman yüksek kullanılabilirlik önerileri](advisor-high-availability-recommendations.md)
* [Danışman güvenlik önerileri](advisor-security-recommendations.md)
* [Advisor performans önerileri](advisor-performance-recommendations.md)
* [Danışman maliyet önerileri](advisor-cost-recommendations.md)
