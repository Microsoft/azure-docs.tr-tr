---
title: Özel belirteç önbelleği serileştirme (MSAL Python) | Mavisi
titleSuffix: Microsoft identity platform
description: Python için MSAL için belirteç önbelleğini serileştirme hakkında bilgi edinin
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 7aaf59377949101ba8dd5c9454b89229e925e859
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87846200"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Python için MSAL içinde özel belirteç önbelleği serileştirme

MSAL Python 'da, bir [Clientapplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)örneği oluşturduğunuzda, uygulama oturumunun süresi boyunca sürekli olarak devam eden bir bellek içi belirteç önbelleği varsayılan olarak sağlanır.

Uygulamanızın farklı oturumlarına erişebilmesi için, belirteç önbelleğinin serileştirilmesi, bu nedenle "kutudan çıkar" olarak sağlanmaz. Bunun nedeni, MSAL Python 'un Web Apps gibi dosya sistemine erişimi olmayan uygulama türlerinde kullanılabilir. Bir MSAL Python uygulamasında kalıcı belirteç önbelleğine sahip olmak için, özel belirteç önbelleği serileştirmesini sağlamanız gerekir.

Belirteç önbelleğini serileştirme stratejileri, bir genel istemci uygulaması (Masaüstü) veya gizli bir istemci uygulaması (Web uygulaması, Web API 'SI veya Daemon uygulaması) yazmadığınıza bağlı olarak farklılık gösterir.

## <a name="token-cache-for-a-public-client-application"></a>Ortak istemci uygulaması için belirteç önbelleği

Ortak istemci uygulamaları, bir kullanıcının cihazında çalışır ve tek bir kullanıcı için belirteçleri yönetir. Bu durumda, önbelleğin tamamını bir dosyaya serileştirilemiyor. Uygulamanız veya başka bir uygulama önbelleğe eşzamanlı olarak erişebilirken dosya kilitlemeyi sağlamayı unutmayın. Bir belirteç önbelleğinin kilitleme olmadan bir dosyaya serileştirilme konusunda basit bir örnek için, [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) Class Reference belgelerindeki örneğe bakın.

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Web uygulaması için belirteç önbelleği (gizli istemci uygulaması)

Web uygulamaları veya Web API 'Leri için, oturum veya bir Redıs önbelleği ya da belirteç önbelleğini depolamak için bir veritabanını kullanabilirsiniz. Kullanıcı başına bir belirteç önbelleği olmalıdır (hesap başına), bu nedenle hesap başına belirteç önbelleğini serileştirdiğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Bir Windows veya Linux Web uygulaması veya Web API 'SI için belirteç önbelleğinin nasıl kullanılacağına ilişkin bir örnek için bkz. [MS-Identity-Python-WebApp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) . Örnek, Microsoft Graph API 'sini çağıran bir Web uygulaması içindir.
