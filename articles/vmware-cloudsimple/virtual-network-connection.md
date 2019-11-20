---
title: ExpressRoute kullanarak Azure sanal ağını CloudSimple 'a bağlama
description: Azure sanal ağı ile CloudSimple ortamınız arasındaki bir bağlantı için eşleme bilgilerinin nasıl alınacağını açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2e2195c61acbf39b40a7659335afff78ac03cb4b
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881415"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>ExpressRoute kullanarak Azure sanal ağını CloudSimple 'a bağlama

Özel bulut ağınızı Azure sanal ağınıza ve Azure kaynaklarınıza genişletebilirsiniz. ExpressRoute bağlantısı, özel bulutunuzda Azure aboneliğinizde çalışan kaynaklara erişmenize olanak tanır.

## <a name="request-authorization-key"></a>İstek yetkilendirme anahtarı

Özel bulutunuz ve Azure sanal ağı arasındaki ExpressRoute bağlantısı için bir yetkilendirme anahtarı gereklidir. Anahtar almak için, <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">destek</a>ile bir bilet dosyası yapın.  İstekte aşağıdaki bilgileri kullanın:

* Sorun türü: **Teknik**
* Abonelik: **CloudSimple hizmetinin dağıtıldığı aboneliği seçin**
* Hizmet: **CloudSimple tarafından VMware çözümü**
* Sorun türü: **hizmet isteği**
* Sorun alt türü: **Azure VNet bağlantısı Için yetkilendirme anahtarı**
* Subject: **Azure VNet bağlantısı için yetkilendirme anahtarı isteği**

## <a name="get-peering-information-from-cloudsimple-portal"></a>CloudSimple portalından eşleme bilgilerini al

Bağlantıyı kurmak için, Azure sanal ağı ile CloudSimple ortamınız arasında bir bağlantı kurmanız gerekir.  Yordamın bir parçası olarak, eşdüzey devre URI 'sini ve yetkilendirme anahtarını sağlamalısınız. [Cloudsimple PORTALıNDAN](access-cloudsimple-portal.md)URI ve yetkilendirme anahtarını edinin.  Yan menüden **ağ** ' ı seçin ve ardından **Azure ağ bağlantısı**' nı seçin. Veya yan menüden **Hesap** ' ı seçin ve ardından **Azure ağ bağlantısı**' nı seçin.

*Kopya* simgesini kullanarak her bir bölgenin yetkilendirme anahtarını ve eş devre URI 'sini kopyalayın. Bağlamak istediğiniz her CloudSimple bölgesi için:

1. URI 'yi kopyalamak için **Kopyala** ' ya tıklayın. Azure portal eklemek için kullanılabilecek bir dosyaya yapıştırın.  
2. Yetkilendirme anahtarını kopyalamak için **Kopyala** ' ya tıklayın ve dosyayı da dosyaya yapıştırın.

**Kullanılabilir** durumda olan yetkilendirme anahtarını ve eş devre dışı URI 'yi kopyalayın.  **Kullanılan** durum, anahtarın zaten bir sanal ağ bağlantısı oluşturmak için kullanıldığını gösterir.

![Sanal ağ bağlantısı sayfası](media/virtual-network-connection.png)

Azure sanal ağını CloudSimple bağlantısına ayarlama hakkında daha fazla bilgi için bkz. [ExpressRoute kullanarak Cloudsimple özel bulut ortamınızı Azure sanal ağına bağlama](azure-expressroute-connection.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Özel buluta Azure sanal ağ bağlantısı](azure-expressroute-connection.md)
* [Azure ExpressRoute kullanarak şirket içi ağa bağlanma](on-premises-connection.md)
