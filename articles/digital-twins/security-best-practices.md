---
title: En iyi güvenlik yöntemlerini anlama-Azure dijital TWINS | Microsoft Docs
description: Azure dijital TWINS ve Nesnelerin İnterneti için en iyi güvenlik yöntemleri hakkında bilgi edinin.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 9f3f98863f9a7cd0e6328ddc75b1154ee933fe0b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74009245"
---
# <a name="azure-digital-twins-security-best-practices"></a>Azure Digital TWINS güvenliği en iyi yöntemleri

Azure dijital TWINS güvenliği, IoT grafiğinizde belirli kaynaklara ve eylemlere kesin erişim sağlar. Bu, ayrıntılı rol ve [rol tabanlı erişim denetimi](./security-role-based-access-control.md)adlı izin yönetimini kolaylaştırır.

Azure dijital TWINS Ayrıca, Azure Active Directory (Azure AD) dahil olmak üzere Azure IoT 'de mevcut olan diğer güvenlik özelliklerini de kullanır. Bu nedenle, Azure dijital TWINS üzerinde oluşturulmuş uygulamaların yapılandırılması ve güvenliğini sağlamak, şu anda önerilen aynı [Azure IoT güvenlik uygulamalarının](../iot-fundamentals/iot-security-best-practices.md) birçoğunu kullanmayı içerir.

Bu makalede, izlenecek başlıca en iyi yöntemler özetlenmektedir.

> [!IMPORTANT]
> IoT alanınız için maxhayvan güvenliği sağlamak üzere ek güvenlik kaynakları ' nı gözden geçirin. Cihaz satıcılarınızı eklediğinizden emin olun.

> [!TIP]
> IoT güvenlik tehditlerini ve güvenlik açıklarını algılamaya yardımcı olması için [IoT Için Azure Güvenlik Merkezi](https://docs.microsoft.com/azure/asc-for-iot/) 'ni kullanın.

## <a name="iot-security-best-practices"></a>IoT güvenliği için en iyi uygulamalar

IoT cihazlarınızı güvenle güvenli hale getirmeye yönelik bazı önemli uygulamalar şunlardır:

> [!div class="checklist"]
> * IoT alanınıza bağlı her cihazı, yetkisiz kullanım için güvenli hale getirin.
> * IoT alanınızdaki her bir cihaz, algılayıcı ve kişinin rolünü sınırlayın. Tehlikeye atılırsa, efekt en aza indirilir.
> * Cihaz IP adresi filtrelemesi ve bağlantı noktası kısıtlamasının olası kullanımını göz önünde bulundurun.
> * Performansı artırmak için g/ç ve cihaz bant genişliğini sınırlayın. Hız sınırlaması, hizmet reddi saldırılarını önlemek için güvenliği iyileştirebilir.
> * Cihaz bellenimini güncel tutun.
> * Geliştirme ve gelişmeye devam ettikleri için cihaz, ağ ve ağ geçidi güvenlik en iyi uygulamalarını düzenli olarak denetleyin ve inceleyin.

IoT alanını güvenle güvenli hale getirmeye yönelik bazı önemli uygulamalar şunlardır:

> [!div class="checklist"]
> * Kaydedilmiş, depolanmış veya kalıcı verileri şifreleyin.
> * Parolaların veya anahtarların düzenli aralıklarla değiştirilmesini veya yenilenmesini gerektir.
> * Erişimi ve izinleri role göre dikkatle kısıtlayın. Aşağıda, [rol tabanlı erişim denetimi en iyi uygulamaları](#role-based-access-control-best-practices) bölümüne bakın.
> * Her ağdaki cihazların diğerlerinden yalıtılmış olması için bölünmüş bir ağ topolojisini göz önünde bulundurun.
> * Güçlü şifreleme kullanın. Uzun parolalar gerektir ve güvenli protokoller ve iki öğeli kimlik doğrulama kullanın.

[İzleme](./how-to-configure-monitoring.md) Her zamanki işlem aralığının dışında kalan aykırı ler, tehditler veya kaynak parametreleri için izlenecek IoT kaynakları. İzleme Yönetimi için Azure Analytics 'i kullanın.

> [!NOTE]
> Olay işleme ve izleme hakkında daha fazla bilgi için bkz. [Azure Digital TWINS ile olayları ve Iletileri yönlendirme](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>En iyi Azure Active Directory uygulamalar

Azure dijital TWINS, kullanıcıların kimliğini doğrulamak ve uygulamaları korumak için Azure Active Directory kullanır. Azure Active Directory, çeşitli modern mimarilerin kimlik doğrulamasını destekler. Bunlar, OAuth 2,0 veya OpenID Connect gibi endüstri standardı protokoller temel alınarak yapılır. Azure Active Directory IoT alanınızı güvenli hale getirmeye yönelik birkaç temel uygulama şunlardır:

> [!div class="checklist"]
> * Azure Active Directory uygulama gizli dizilerini ve anahtarlarını [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)gibi güvenli bir konuma depolayın.
> * Kimlik doğrulaması yapmak için uygulama gizli dizileri yerine güvenilen bir [sertifika yetkilisi](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) tarafından verilen bir sertifika kullanın.
> * Belirteç için OAuth 2,0 erişim kapsamını sınırlayın.
> * Belirtecin geçerli olduğu sürenin uzunluğunu ve belirtecin geçerli olmaya devam edilip edilmeyeceğini doğrulayın.
> * Belirteçlerin geçerli olduğu sürenin uzunluğunu ayarlayın.
> * Süre dolma belirteçleri yenileyin.

## <a name="role-based-access-control-best-practices"></a>Rol tabanlı erişim denetimi en iyi yöntemleri

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Sonraki adımlar

* Azure IoT en iyi uygulamaları hakkında daha fazla bilgi edinmek için [IoT güvenliği en iyi yöntemlerini](../iot-fundamentals/iot-security-best-practices.md)okuyun.

* Rol tabanlı erişim denetimi hakkında bilgi edinmek için [rol tabanlı erişim denetimi](./security-role-based-access-control.md)makalesini okuyun.

* Kimlik doğrulaması hakkında bilgi edinmek için, [API 'Lerle kimlik doğrulamasını](./security-authenticating-apis.md)okuyun.