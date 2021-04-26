---
title: Azure Iletişim hizmetlerinde kimlik doğrulama
titleSuffix: An Azure Communication Services concept document
description: Bir uygulamanın veya hizmetin Iletişim hizmetlerinde kimlik doğrulaması yapabileceği çeşitli yollar hakkında bilgi edinin.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 9edfb63f5ce43ed325b4c4a1fa67e0e9ca52dc89
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110874"
---
# <a name="authenticate-to-azure-communication-services"></a>Azure Iletişim hizmetlerinde kimlik doğrulama

Azure Iletişim Hizmetleri ile her istemci etkileşiminin kimliğinin doğrulanması gerekir. Tipik bir mimaride, bkz. [istemci ve sunucu mimarisi](./client-and-server-architecture.md), *erişim anahtarları* veya *Yönetilen kimlikler* kimlik doğrulaması için kullanılır.

Başka bir kimlik doğrulama türü, Kullanıcı katılımı gerektiren hizmetlerde kimlik doğrulaması yapmak için *Kullanıcı erişim belirteçlerini* kullanır. Örneğin, sohbet veya çağırma hizmeti, kullanıcıların bir iş parçacığına eklenmesine ve birbirleriyle konuşmalar yapmasına izin vermek için *Kullanıcı erişim belirteçlerini* kullanır.

## <a name="authentication-options"></a>Kimlik doğrulama seçenekleri

Aşağıdaki tabloda, Azure Iletişim Hizmetleri SDK 'Ları ve kimlik doğrulama seçenekleri gösterilmektedir:

| SDK    | Kimlik doğrulama seçeneği                               |
| ----------------- | ----------------------------------------------------|
| Kimlik          | Erişim anahtarı veya yönetilen kimlik                      |
| SMS               | Erişim anahtarı veya yönetilen kimlik                      |
| Telefon numaraları     | Erişim anahtarı veya yönetilen kimlik                      |
| Events           | Kullanıcı erişim belirteci                                   |
| Sohbet              | Kullanıcı erişim belirteci                                   |

Her yetkilendirme seçeneği kısaca aşağıda açıklanmıştır:

### <a name="access-key"></a>Erişim Anahtarı

Erişim anahtarı kimlik doğrulaması, güvenilir bir hizmet ortamında çalışan hizmet uygulamaları için uygundur. Erişim anahtarınız Azure Iletişim Hizmetleri portalında bulunabilir. Hizmet uygulaması, ilgili SDK 'Ları başlatmak için bunu bir kimlik bilgisi olarak kullanır. [Kimlik SDK 'sında](../quickstarts/access-tokens.md)nasıl kullanıldığına ilişkin bir örnek görüntüleyin. 

Erişim anahtarı, kaynağınızın bağlantı dizesinin bir parçası olduğundan, bir bağlantı dizesiyle kimlik doğrulaması bir erişim anahtarı ile kimlik doğrulamaya eşdeğerdir.

Bir erişim anahtarı kullanarak ACS ' API 'Lerini el ile çağırmak isterseniz, isteği imzalamanız gerekir. İsteğin imzalanması, ayrıntılı olarak bir [öğretici](../tutorials/hmac-header-tutorial.md)dahilinde açıklanmıştır.

### <a name="managed-identity"></a>Yönetilen Kimlik

Yönetilen kimlikler, diğer yetkilendirme seçenekleri üzerinde üstün güvenlik ve kullanım kolaylığı sağlar. Örneğin, Azure AD 'yi kullanarak, erişim anahtarı yetkilendirmesi ile yaptığınız gibi, kodunuzun içinde hesap erişim anahtarınızı depolamak zorunda kalmaktan kaçının. İletişim Hizmetleri uygulamalarıyla erişim anahtarı yetkilendirmesini kullanmaya devam edebilirsiniz, Microsoft, mümkün olduğunda Azure AD 'ye geçmeyi önerir. 

Yönetilen bir kimlik ayarlamak için [Azure CLI 'dan kayıtlı bir uygulama oluşturun](../quickstarts/managed-identity-from-cli.md). Ardından, uç nokta ve kimlik bilgileri SDK 'ların kimliğini doğrulamak için kullanılabilir. [Yönetilen kimliğin](../quickstarts/managed-identity.md) nasıl kullanıldığına ilişkin örneklere bakın.

### <a name="user-access-tokens"></a>Kullanıcı erişim belirteçleri

Kullanıcı erişim belirteçleri, kimlik SDK 'Sı kullanılarak oluşturulur ve kimlik SDK 'sında oluşturulan kullanıcılarla ilişkilendirilir. [Kullanıcı oluşturma ve belirteç oluşturma](../quickstarts/access-tokens.md)hakkında bir örnek görüntüleyin. Daha sonra Kullanıcı erişim belirteçleri, sohbet veya çağrı SDK 'sında konuşmaları doğrulamak için kullanılır. Daha fazla bilgi için bkz. [uygulamanıza sohbet ekleme](../quickstarts/chat/get-started.md). Kullanıcı erişim belirteci kimlik doğrulaması, erişim anahtarı ve yönetilen kimlik doğrulamasına kıyasla, güvenli bir Azure kaynağı yerine bir kullanıcının kimliğini doğrulamak için kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Iletişim Hizmetleri kaynaklarını](../quickstarts/create-communication-resource.md) 
>  oluşturma ve yönetme [Azure CLI](../quickstarts/managed-identity-from-cli.md) 
>  'dan Azure Active Directory yönetilen bir kimlik uygulaması oluşturma [Kullanıcı erişim belirteçleri oluştur](../quickstarts/access-tokens.md)

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:
- [İstemci ve sunucu mimarisi hakkında bilgi edinin](../concepts/client-and-server-architecture.md)
