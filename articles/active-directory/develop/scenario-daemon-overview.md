---
title: Web API 'Lerini çağıran bir Daemon uygulaması oluşturun | Mavisi
titleSuffix: Microsoft identity platform
description: Web API 'Lerini çağıran bir Daemon uygulaması derlemeyi öğrenin
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 00a70b585ddf522a25e81703fe5bdf55efbcb7e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582783"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Senaryo: Web API 'Lerini çağıran Daemon uygulaması

Web API 'Lerini çağıran bir Daemon uygulaması oluşturmak için ihtiyacınız olan her şey hakkında bilgi edinin.

## <a name="overview"></a>Genel Bakış

Uygulamanız, bir Web API 'sini kendi adına (Kullanıcı adına değil) çağırmak için bir belirteç alabilir. Bu senaryo, Daemon uygulamaları için yararlıdır. Standart OAuth 2,0 [istemci kimlik bilgileri](v2-oauth2-client-creds-grant-flow.md) izni ' nı kullanır.

![Daemon uygulamaları](./media/scenario-daemon-app/daemon-app.svg)

Daemon uygulamaları için kullanım örneklerinin bazı örnekleri aşağıda verilmiştir:

- Bir dizinde Kullanıcı sağlamak veya yönetmek ya da toplu işlem yapmak için kullanılan Web uygulamaları
- Toplu işler veya arka planda çalışan bir işletim sistemi hizmeti olan masaüstü uygulamaları (Windows üzerinde Windows Hizmetleri veya Linux üzerinde arka plan işlemleri gibi)
- Belirli kullanıcıları değil, dizinleri işlemek zorunda olan Web API 'Leri

Daemon olmayan uygulamaların istemci kimlik bilgilerini kullandıklarında bile, bir Web API 'sine veya bir kaynağa, teknik nedenlerden dolayı kendi kimlikleri altına erişmesi gerekir. Bir önbellek için Azure Key Vault veya Azure SQL veritabanı 'nda gizli dizi erişimine bir örnektir.

Kendi kimlikleri için belirteç elde eden uygulamalar:

- Gizli istemci uygulamaları. Kaynaklara kullanıcılardan bağımsız olarak eriştikleri belirtilen bu uygulamalar, kimliklerini kanıtlamaları gerekir. Bunlara duyarlı uygulamalar de vardır. Azure Active Directory (Azure AD) kiracı yöneticileri tarafından onaylanması gerekir.
- Azure AD ile bir gizli dizi (uygulama parolası veya sertifika) kaydettiniz. Bu gizli dizi, bir belirteç almak için Azure AD çağrısı sırasında geçirilir.

## <a name="specifics"></a>Özelliklerini

> [!IMPORTANT]
>
> - Kullanıcılar bir Daemon uygulamasıyla etkileşime giremiyorum. Bir Daemon uygulaması kendi kimliğini gerektirir. Bu tür bir uygulama, uygulama kimliğini kullanarak bir erişim belirteci ister ve uygulama KIMLIĞI, kimlik bilgileri (parola veya sertifika) ve uygulama KIMLIĞI URI 'sini Azure AD 'ye sunuyor. Başarılı kimlik doğrulamasından sonra, Daemon, Microsoft Identity platformundan bir erişim belirteci (ve yenileme belirteci) alır. Bu belirteç daha sonra Web API 'sini çağırmak için kullanılır (ve gerektiğinde yenilenir).
> - Kullanıcılar DAEMON uygulamalarıyla etkileşime giremediği için artımlı onay mümkün değildir. Tüm gerekli API izinlerinin uygulama kaydında yapılandırılması gerekir. Uygulamanın kodu yalnızca statik olarak tanımlanan izinleri ister. Bu Ayrıca, Daemon uygulamalarının artımlı onayı desteklememe anlamına gelir.

Geliştiriciler için, bu senaryoya yönelik uçtan uca deneyim aşağıdaki yönlere sahiptir:

- Daemon uygulamaları yalnızca Azure AD kiracılarında çalışabilir. Microsoft kişisel hesaplarını işlemeyi deneyen bir Daemon uygulaması oluşturmak mantıklı değildir. İş kolu (LOB) uygulaması geliştiricisiyseniz, kiracınızda Daemon uygulamanızı oluşturacaksınız. Bir ISV iseniz, çok kiracılı bir Daemon uygulaması oluşturmak isteyebilirsiniz. Her kiracı yöneticisinin izin sağlaması gerekir.
- [Uygulama kaydı](./scenario-daemon-app-registration.md)sırasında, yanıt URI 'si gerekli değildir. Gizli dizileri veya sertifikaları veya imzalı onayları Azure AD ile paylaşabilirsiniz. Ayrıca, uygulama izinleri istemeniz ve bu uygulama izinlerini kullanmak için yönetici onayı vermeniz gerekir.
- Uygulama [yapılandırmasının](./scenario-daemon-app-configuration.md) , uygulama kaydı SıRASıNDA Azure AD ile paylaşılan olarak istemci kimlik bilgilerini sağlaması gerekir.
- İstemci kimlik bilgileri akışı ile belirteç almak için kullanılan [kapsamın](scenario-daemon-acquire-token.md#scopes-to-request) bir statik kapsam olması gerekir.

## <a name="recommended-reading"></a>Önerilen okuma

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryonun [uygulama kaydı](./scenario-daemon-app-registration.md)olan sonraki makaleye geçin.
