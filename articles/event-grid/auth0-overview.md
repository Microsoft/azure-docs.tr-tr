---
title: Azure Event Grid ile Auth0 iş ortağı konuları
description: Azure Event Grid ile Azure hizmetlerine olay Auth0 gönderin.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 340403111c094540ba7e66faf71ebf3cfe24fe7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101298"
---
# <a name="auth0-partner-topics"></a>Auth0 iş ortağı konuları
![Auth0 logosu](./media/auth0-overview/auth0-logo.png)

Uygulama oluşturucular için kimlik platformu olan Auth0, geliştiricilere ve kuruluşlara uygulamalarını güvenli hale getirmek için ihtiyaç duydukları yapı taşları sağlar.

Auth0 iş ortağı konusu, bir dizi görevi gerçekleştirmek için Auth0's sistemi tarafından yayılan olayları kullanmanıza olanak sağlar. Kimlik doğrulamasından sonra kullanıcılara anlamlı yollarla katılın veya güvenlik ve altyapı görevlerini otomatik hale getirin.

Tümleştirme, Auth0 günlük olaylarınızı Azure 'a yüksek güvenilirlikle akışla göndermenize olanak tanır. Burada, en sevdiğiniz Azure kaynaklarıyla olayları kullanabilirsiniz. Bu tümleştirme, olaylara tepki vermenize, Öngörüler elde etmenize, güvenlik sorunlarını izlemenize ve diğer güçlü veri işlem hatlarından etkileşime geçmesini sağlar.

Auth0 ve Azure kullanan kuruluşlar için bu tümleştirme, verileri tüm yığınınızın genelinde sorunsuzca tümleştirmenize olanak tanır. 
 
## <a name="available-event-types"></a>Kullanılabilir olay türleri
Kullanılabilir Auth0 olay türlerinin tam listesi ve açıklamaları [Bu web sitesinde](https://auth0.com/docs/logs/references/log-event-type-codes)bulunabilir.

## <a name="use-cases"></a>Uygulama alanları

### <a name="engage-with-your-users"></a>Kullanıcılarınıza katılın
Güçlü bir kullanıcı deneyimi sunmak, karmaşıklığı azaltmak ve kullanıcılarınızı korumak için kritik öneme sahiptir. Azure Işlevleri ve Azure Logic Apps ile Auth0 olaylarını kullanarak daha özelleştirilmiş uygulama deneyimleri sunun. 

### <a name="understand-user-behavior"></a>Kullanıcı davranışını anlama
Kullanıcıların ürününüze ne zaman erişebileceğini ve nerede oturum açtıkları ve kullandıkları aygıtları anlayın. Bu sinyalleri takip ederek en çok ürün alanı hakkında daha iyi bir şekilde geliştirin. Bu sinyaller şunları belirlemenize yardımcı olur:
- Hangi tarayıcıları ve cihazları destekliyoruz. 
- Uygulamanızı hangi dillerde yerelleştirebileceğinize. 
- En yoğun trafik zamanlarınız. 

### <a name="manage-user-data"></a>Kullanıcı verilerini yönetme
Güvenlik ve sektör düzenlemelerine karşı Kullanıcı eylemlerinizi korumak ve denetlemek çok önemlidir. Kullanıcı verilerini düzenleme, kaldırma veya dışa aktarma özelliği, Avrupa Birliği 'nin Genel Veri Koruma Yönetmeliği (GDPR) gibi gizlilik yasaları için giderek daha fazla önem taşımaktadır.

### <a name="secure-your-application"></a>Uygulamanızın güvenliğini sağlama
Dağıtılmış bir sistemi koruduğunuzda güvenlik izleme ve olay yanıtı yordamlarını birleştirmek önemlidir. Bu nedenle, tüm verileri tek bir yerde tutmanız ve yığının tamamını izlemeniz önemlidir. 

## <a name="next-steps"></a>Sonraki adımlar

- [İş ortağı konularına genel bakış](partner-events-overview.md)
- [Auth0 iş ortağı konusunu kullanma](auth0-how-to.md)
- [Auth0 belgeleri](https://auth0.com/docs/azure-tutorial)
- [Event Grid iş ortağı olun](partner-onboarding-overview.md)

