---
title: Teklif türü ile yayımlama Kılavuzu-Microsoft ticari Market
description: Bu makalede, Microsoft ticari marketi 'nde bulunan teklif türleri açıklanmaktadır.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 04/06/2021
ms.openlocfilehash: 381ee45bd678ef22222483675ddf001e16970628
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552062"
---
# <a name="publishing-guide-by-offer-type"></a>Teklif türüne göre yayımlama kılavuzu

Bu makalede, ticari Market 'te bulunan teklif türleri açıklanmaktadır. *Teklif türü* , ticari Market 'te sunulan meta verileri, yapıtları ve diğer içerikleri içeren teklif yapısını tanımlar.

[Bir yayımlama seçeneğine karar](determine-your-listing-type.md)verdikten sonra, Iş Ortağı Merkezi 'nde teklifinizi oluşturmaya başlamadan önce bir teklif türü seçmeniz gerekir. Teklif türü, yayımlamak istediğiniz çözüm, uygulama veya hizmet teklifinin türüne, ayrıca Microsoft ürün ve hizmetlerine yönelik hizalamasını karşılayacak.

> [!NOTE]
> Bir teklif türü seçtikten sonra, teklifi başka bir tür olarak değiştiremezsiniz. Farklı bir teklif türü oluşturmak için yeni bir teklif oluşturmanız gerekir.

Farklı yayımlama seçeneklerini etkinleştirmek için farklı yollarla tek bir teklif türü yapılandırabilirsiniz, listeleme seçeneği, sağlama veya fiyatlandırma. Teklif türünün yayımlama seçeneği ve yapılandırması Ayrıca, teklif uygunluğu ve teknik gereksinimlere de uyum sağlar.

Teklifinizi oluşturmadan önce çevrimiçi mağazayı gözden geçirdiğinizden emin olun ve tür uygunluk gereksinimlerini ve teknik yayımlama gereksinimlerini sunun.

## <a name="list-of-offer-types"></a>Teklif türlerinin listesi

Aşağıdaki tabloda, Iş Ortağı Merkezi 'nde ticari Market teklif türleri gösterilmektedir.

| **Teklif türü**    | **Açıklama**  |
| :------------------- | :-------------------|
| [**Azure Uygulaması**](plan-azure-application-offer.md) | İki tür Azure uygulama planı vardır: _çözüm şablonu_ ve _yönetilen uygulama_. Her iki plan türü, tek bir sanal makinenin (VM) ötesinde bir çözümün dağıtımını ve yapılandırılmasını otomatik hale getirmeye yönelik destek. IaaS çözümleri gibi karmaşık çözümler sağlamak üzere VM 'Ler, ağ oluşturma ve depolama kaynakları dahil olmak üzere birden çok kaynak sağlama sürecini otomatikleştirebiliriz. Her iki plan türü de dahil olmak üzere, VM 'Lerle sınırlı olmamak üzere birçok farklı türde Azure kaynağı kullanabilir.<ul><li>**Çözüm şablonu** planları, ticari Market 'te çözüm yayımlamanın ana yollarından biridir. Çözüm şablonu planları ticari Market 'te transactable değildir, ancak ticari Market aracılığıyla faturalandırılan ücretli VM tekliflerini dağıtmak için kullanılabilirler. Müşteri çözümü yöneteceği ve işlemler başka bir plan üzerinden faturalandırılırken çözüm şablonu plan türünü kullanın.</li><br><li>**Yönetilen uygulama** planları, müşterileriniz için tam olarak yönetilen, anahtar uygulamaları kolayca oluşturup sunmanıza olanak tanır. Çözüm şablonu planlarıyla aynı yeteneklere sahiptir ve bazı önemli farklılıklar vardır:</li><ul><li> Kaynaklar bir kaynak grubuna dağıtılır ve uygulamanın yayımcısı tarafından yönetilir. Kaynak grubu, tüketicinin aboneliğinde mevcuttur ancak yayımcının kiracısındaki bir kimlik, kaynak grubuna erişime sahiptir.</li><li>Yayımcı olarak, çözümün sürekli desteğinin maliyetini belirtirsiniz ve işlemler ticari Market aracılığıyla desteklenir.</li></ul>Siz veya müşteriniz çözümün bir iş ortağı tarafından yönetilmesini gerektirdiğinde veya abonelik tabanlı bir çözüm dağıtacaksanız, yönetilen uygulama planı türünü kullanın.</ul> |
| [**Azure kapsayıcısı**](marketplace-containers.md) | Çözümünüz, Kubernetes tabanlı bir Azure Container Service olarak sağlanan bir Docker kapsayıcı görüntüsü olduğunda, Azure Container teklif türünü kullanın. |
| [**Azure sanal makinesi**](marketplace-virtual-machines.md) | Müşterinizin ilişkili aboneliğine bir Sanal Gereç dağıtırken sanal makine teklif türünü kullanın. |
| [**Danışmanlık hizmeti**](./plan-consulting-service-offer.md) | Danışmanlık Hizmetleri, Azure, Dynamics 365 veya Power Suite hizmetlerinin kullanımını desteklemek ve genişletmek için müşterilerin hizmetleriyle bağlantı sağlanmasına yardımcı olur.|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | Dynamics 365 Iş Merkezi, Dynamics 365 müşteri katılımı, Power Apps ve finans ve operasyon uygulamaları üzerinde yapı veya genişletme için AppSource tekliflerini yayımlayabilirsiniz.|
| [**IoT Edge modülü**](iot-edge-module.md) | Azure IoT Edge modüller, IoT Edge tarafından yönetilen en düşük hesaplama birimleridir ve Microsoft hizmetlerini (Azure Stream Analytics), 3. taraf hizmetleri veya kendi çözümüne özgü kodunuzu içerebilir. |
| [**Yönetilen hizmet**](./plan-managed-service-offer.md) | Yönetilen hizmet teklifleri oluşturabilir ve [Azure açık Thouse](../lighthouse/overview.md)aracılığıyla müşteri tarafından atanan abonelikleri veya kaynak gruplarını yönetebilirsiniz.|
| [**Power BI uygulaması** <br/> **Microsoft 365**](appsource-offer-publishing-guide.md) | Power BI ve Microsoft 365 oluşturan AppSource tekliflerini yayımlayabilirsiniz.|
| [**Hizmet olarak yazılım**](plan-saas-offer.md) | Müşterinizin SaaS tabanlı, teknik çözümünüzü bir abonelik olarak satın almasını sağlamak için hizmet olarak yazılım (SaaS) teklif türünü kullanın. SaaS tekliflerine yönelik çoklu oturum açma gereksinimleri hakkında bilgi için bkz. [ticari Market 'Te Azure AD ve transactable SaaS teklifleri](azure-ad-saas.md). |


## <a name="next-steps"></a>Sonraki adımlar

- Teklifinizin seçim ve yapılandırmasını sonuçlandırmak için teklif türü için ilgili makaledeki uygunluk gereksinimlerini gözden geçirin.
- Çözümünüzün bir teklif türü ve yapılandırmaya nasıl eşlendiğini gösteren örnekler için her çevrimiçi mağaza için yayımlama düzenlerini gözden geçirin.