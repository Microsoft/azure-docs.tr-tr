---
title: Azure ExpressRoute çapraz geçiş API 'leri geliştirme ve Tümleştirme
description: Bu makalede expressRouteCrossConnections kaynak türü hakkında ExpressRoute iş ortakları için ayrıntılı bir genel bakış sunulmaktadır.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: b80392231dba26a10141dcd8247b092e8171894c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011991"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>ExpressRoute çapraz geçiş API 'leri geliştirme ve Tümleştirme

ExpressRoute Iş ortağı Kaynak Yöneticisi API 'SI ExpressRoute iş ortaklarının, Customer ExpressRoute devreleri 'nin katman 2 ve katman 3 yapılandırmasını yönetmesine olanak tanır. ExpressRoute Iş ortağı Kaynak Yöneticisi API 'SI, **Expressroutecrossconnections** yeni bir kaynak türü sunar. İş ortakları, bu kaynağı Müşteri ExpressRoute devreleri yönetmek için kullanır.

## <a name="workflow"></a>İş akışı

ExpressRouteCrossConnections kaynağı, ExpressRoute devresine yönelik bir gölge kaynaktır. Bir Azure müşterisi bir ExpressRoute bağlantı hattı oluşturduğunda ve belirli bir ExpressRoute iş ortağı seçtiğinde, Microsoft ortağın Azure ExpressRoute yönetim aboneliğinde bir expressRouteCrossConnections kaynağı oluşturur. Bu durumda, Microsoft ' de expressRouteCrossConnections kaynağını oluşturmak için bir kaynak grubu tanımlar. Kaynak grubu için adlandırma standardı, **Crossconnection-* peeringlocation * * *; Burada PeeringLocation = ExpressRoute konumu. Örneğin, bir müşteri Denver 'de bir ExpressRoute bağlantı hattı oluşturursa, çapraz bağlantı aşağıdaki kaynak grubundaki iş ortağının Azure aboneliğinde oluşturulur: **çapraz** bağlantı oluşturma.

ExpressRoute iş ortakları, expressRouteCrossConnections kaynağına karşı REST işlemleri vererek katman 2 ve katman 3 yapılandırmasını yönetir.

## <a name="benefits"></a>Avantajlar

ExpressRouteCrossConnections kaynağına geçmesinin avantajları:

* ExpressRoute iş ortakları için gelecekteki tüm geliştirmeler ExpressRouteCrossConnection kaynağında kullanıma sunulacaktır.

* İş ortakları, expressRouteCrossConnection kaynağına [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md) uygulayabilir. Bu denetimler, Kullanıcı hesaplarının expressRouteCrossConnection kaynağını değiştirebileceği izinleri tanımlayabilir ve eşleme yapılandırmasını ekleyebilir/güncelleştirebilir/silebilir.

* ExpressRouteCrossConnection kaynağı, ExpressRoute bağlantılarında sorun gidermeye yardımcı olabilecek API 'Ler sunar. Buna ARP tablosu, BGP yol tablosu Özeti ve BGP yol tablosu ayrıntıları dahildir. Bu özellik klasik dağıtım API 'Leri tarafından desteklenmez.

* İş ortakları, *Routefilter* kaynağını kullanarak Microsoft eşlemesindeki tanıtılan toplulukları da arayabilir.

## <a name="api-development-and-integration-steps"></a>API Geliştirme ve tümleştirme adımları

ExpressRoute iş ortakları, Iş ortağı API 'sine karşı geliştirmek için bir test müşterisi ve test iş ortağı kurulumundan faydalanır. Test müşterisi kurulumu, sınama eşleme konumlarında, kukla cihazlarla ve bağlantı noktalarıyla eşlenen ExpressRoute devreleri oluşturmak için kullanılacaktır. Test ortağı kurulumu, test eşleme konumunda oluşturulan ExpressRoute devreleri yönetmek için kullanılır.

### <a name="1-enlist-subscriptions"></a>1. listeleme abonelikleri

Test ortağını ve test müşteri kurulumunu istemek için ExpressRoute mühendislik Kişinizden iki Kullandıkça Öde Azure aboneliklerini listeleme:
* **ExpressRoute_API_Dev_Provider_Sub:** Bu abonelik, kukla cihazlarda ve bağlantı noktalarında test eşleme konumlarında oluşturulan ExpressRoute devreleri yönetmek için kullanılacaktır.

* **ExpressRoute_API_Dev_Customer_Sub:** Bu abonelik, kukla cihazlarla ve bağlantı noktalarıyla eşlenen test eşleme konumlarında ExpressRoute devreleri oluşturmak için kullanılacaktır.

Test eşleme konumları: kukla cihazlar ve bağlantı noktaları, varsayılan olarak üretim müşterilerine gösterilmez. Test kurulumuna eşlenen ExpressRoute devreleri oluşturmak için bir abonelik özelliği bayrağının etkinleştirilmesi gerekir.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. expressRouteCrossConnections API 'sine erişmek için Dev_Provider aboneliğini kaydedin

ExpressRouteCrossConnections API 'sine erişebilmek için iş ortağı aboneliğinin **Microsoft. Network kaynak sağlayıcısına** kaydedilmesi gerekir. Kayıt işlemini gerçekleştirmek için [Azure kaynak sağlayıcıları ve türleri](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) makalesindeki adımları izleyin.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Azure Resource Manager REST API çağrılarının kimlik doğrulamasını ayarlama

Çoğu Azure hizmeti, hizmet API 'Leri çağrılmadan önce geçerli kimlik bilgilerini kullanarak Kaynak Yöneticisi kimlik doğrulaması yapmak için istemci kodu gerektirir. Kimlik doğrulaması, Azure AD tarafından çeşitli aktörler arasında düzenlenir ve istemciye kimlik doğrulama kanıtı olarak bir erişim belirteci sağlar.

Kimlik doğrulama işlemi iki ana adımdan oluşur:

1. [Istemcisini kaydedin](/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Erişim Isteği oluşturun](/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. istemci uygulamasına ağ katılımcısı izni sağlayın

Kimlik doğrulaması başarıyla yapılandırıldıktan sonra, Dev_Provider_Sub kapsamında, istemci uygulamanıza ağ katılımcısı erişimi vermeniz gerekir. İzin vermek için [Azure Portal](https://ms.portal.azure.com/#home) oturum açın ve aşağıdaki adımları uygulayın:

1. Abonelikler ' e gidin ve Dev_Provider_Sub seçin
2. Access Control git (ıAM)
3. Rol ataması Ekle
4. Ağ katılımcısı rolünü seçin
5. Azure AD kullanıcısına, grubuna veya hizmet sorumlusuna erişim atama
6. İstemci uygulamanızı seçin
7. Değişiklikleri kaydetme

### <a name="5-develop"></a>5. geliştirme

[Expressroutecrossconnections API 'sinde](/rest/api/expressroute/expressroutecrossconnections)geliştirme yapın.

## <a name="rest-api"></a>REST API

REST API belgeler için bkz. [ExpressRoute çapraz bağlantıları REST API](/rest/api/expressroute/expressroutecrossconnections) .

## <a name="next-steps"></a>Sonraki adımlar

Tüm ExpressRoute REST API 'Leri hakkında daha fazla bilgi için bkz. [ExpressRoute REST API 'leri](/rest/api/expressroute/).