---
title: Azure Uygulama teklifi | Azure Marketi
description: Azure Market 'te Azure uygulaması teklifi yayımlama sürecine genel bakış.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: pabutler
ms.openlocfilehash: 9125b5c71b63b27c58ea72b7bfd49f730854b33d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818801"
---
# <a name="azure-application-offer"></a>Azure uygulama teklifi

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> Bu bölümde, [Azure Market 'te](https://azuremarketplace.microsoft.com)yeni bir Azure uygulaması teklifinin nasıl yayımlanacağı açıklanmaktadır.  Her bir Azure uygulaması, genellikle bir veya daha fazla sanal makine ve diğer Azure veya Web tabanlı Hizmetleri destekleyen bir uygulama tarafından kullanılan tüm teknik varlıkları tanımlayan bir Azure Resource Manager şablonu içerir. Tüm Azure Uygulama teklifleri [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)aracılığıyla erişim güvenliğini sağlamalıdır.  </div> | ![Azure uygulamaları simgesi](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Yayımlamaya genel bakış

Aşağıdaki video, [Çözüm şablonları ve Azure Market Için yönetilen uygulamalar](https://channel9.msdn.com/Events/Build/2018/BRK3603), bir giriş: hangi teklif türlerinin kullanılabildiği, hangi teknik varlıkların gerekli olduğu, Azure Resource Manager şablonu yazma, geliştirme uygulama kullanıcı arabirimini, uygulama teklifini yayımlamayı ve uygulama inceleme sürecini test etme.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Azure uygulama türleri

İki tür Azure uygulaması vardır: yönetilen uygulamalar ve çözüm şablonları. 

- Çözüm şablonları, Market 'te çözüm yayımlamanın ana yöntemlerinden biridir. Bu teklif türü, çözümünüz tek bir sanal makinenin (VM) ötesinde ek dağıtım ve yapılandırma Otomasyonu gerektirdiğinde kullanılır. Bir çözüm şablonu kullanarak birden fazla VM 'yi sağlamayı otomatik hale getirebilirsiniz. Bu Otomasyon, karmaşık IaaS çözümleri sağlamak üzere ağ ve depolama kaynakları sağlamayı içerir. Çözüm şablonu gereksinimlerine ve faturalama modeline genel bir bakış için bkz. [Azure uygulamaları: çözüm şablonları](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Yönetilen uygulamalar, tek bir anahtar farkla çözüm şablonlarına benzerdir. Yönetilen bir uygulamada kaynaklar, uygulamanın yayımcısı tarafından yönetilen bir kaynak grubuna dağıtılır. Kaynak grubu, tüketicinin aboneliğinde mevcuttur ancak yayımcının kiracısındaki bir kimlik, kaynak grubuna erişime sahiptir. Yayımcı olarak, çözümün sürekli desteği için maliyeti belirtirsiniz. Azure yönetilen uygulamaları kullanarak, müşterilerinize tam olarak yönetilen, anahtar uygulamaları kolayca oluşturup sunun.

Azure Marketi ' ne ek olarak, bir hizmet kataloğunda yönetilen uygulamalar da sunabilirsiniz. Hizmet kataloğu, bir kuruluştaki kullanıcılar için onaylı çözümleri içeren bir dahili katalogdur. Kuruluştaki gruplar için çözümler sunarak kurumsal standartları karşılamak için kataloğunu kullanırsınız. Çalışanlar, kataloğu kullanarak BT bölümleri tarafından önerilen ve onaylanan uygulamaları kolayca bulabilir.

>[!Note]
>Bulut çözümü sağlayıcıları (CSP) iş ortağı kanalı kabul etme artık kullanılabilir.  Teklifinizi Microsoft CSP iş ortağı kanalları aracılığıyla pazarlama hakkında daha fazla bilgi için lütfen bkz. [bulut çözümü sağlayıcıları](../../cloud-solution-providers.md) .

Yönetilen uygulamaların avantajları ve türleri hakkında daha fazla bilgi için bkz. [Azure yönetilen uygulamalarına genel bakış](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Yayımlama işlemi iş akışı

Aşağıdaki diyagramda bir Azure Uygulama teklifi yayımlamak için üst düzey işlem gösterilmektedir.

![Yayımlama teklifi için iş akışı](./media/new-offer-process.png)

Azure uygulaması teklifini yayımlamaya yönelik üst düzey adımlar şunlardır:

1. [Önkoşulları](./cpp-prerequisites.md) karşılayın-(gösterilmez) bir Azure uygulamasını Azure Marketi 'nde yayımlamaya yönelik iş ve teknik gereksinimleri karşıladığınızı doğrulayın. 

1. [Teklifi oluşturun](./cpp-create-offer.md) -teklifle ilgili ayrıntılı bilgi sağlayın. Bu bilgiler şunları içerir: teklif açıklaması, pazarlama malzemeleri, destek bilgileri ve varlık belirtimleri.

1. [Mevcut iş ve teknik varlıkları oluşturun veya toplayın](./cpp-create-technical-assets.md) -ilişkili çözüm için iş varlıklarını (yasal belgeler ve pazarlama malzemeleri) ve teknik varlıkları oluşturun.

1. [SKU 'Yu oluşturun](./cpp-skus-tab.md) -TEKLIFLE ilişkili SKU 'ları oluşturun. Yayımlamayı planladığınız her görüntü için benzersiz bir SKU gereklidir.

1. Teklifi onaylama ve [Yayımlama](./cpp-publish-offer.md) -teklif ve teknik varlıklar tamamlandıktan sonra teklifi gönderebilirsiniz. Bu gönderim, yayımlama işlemini başlatır. Bu işlem sırasında çözüm, Azure Marketi 'nde test edilmiş, doğrulanan, sertifikalı ve "canlı gidiyor" olarak yapılır.

## <a name="next-steps"></a>Sonraki adımlar

Bu adımları kabul etmeden önce, Microsoft Azure Market yönetilen bir uygulama yayınlamak için [Teknik ve iş gereksinimlerini](./cpp-prerequisites.md) karşılamanız gerekir.
