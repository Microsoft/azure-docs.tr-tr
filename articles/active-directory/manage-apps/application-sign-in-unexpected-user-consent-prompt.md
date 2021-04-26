---
title: Bir uygulamada oturum açarken beklenmeyen onay istemi | Microsoft Docs
description: Bir Kullanıcı Azure AD ile tümleştirmiş olduğunuz bir uygulama için bir onay istemi gördüğünde sorun giderme
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37e4384b5a1b400f11b7b7d6ab15beec4d2f8de9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378081"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Bir uygulamada oturum açarken beklenmeyen onay istemi

Azure Active Directory ile tümleştirilen birçok uygulama, çalıştırmak için çeşitli kaynaklara izinler gerektirir. Bu kaynaklar ayrıca Azure Active Directory ile tümleştirildiğinde, bunlara erişim izinleri Azure AD onay çerçevesi kullanılarak istenir. 

Bu, genellikle tek seferlik bir işlem olan bir uygulama ilk kez kullanıldığında bir onay istemi gösterilmekte olur. 

> [!VIDEO https://www.youtube.com/embed/a1AjdvNDda4]

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Kullanıcıların onay istemlerini göreceği senaryolar

Çeşitli senaryolarda ek istemler beklenebilir:

* Uygulamanın gerektirdiği izinler kümesi değişti.

* Uygulamaya ilk olarak bağlanan kullanıcı yönetici değildi ve artık farklı (yönetici olmayan) bir Kullanıcı uygulamayı ilk kez kullanıyor.

* Uygulamayı ilk olarak uygulamaya veren kullanıcı bir yöneticiydi, ancak kuruluşun tamamı adına izin vermedi.

* Uygulama, izin ilk kez verildikten sonra ek izinler istemek için [artımlı ve dinamik onay](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) kullanıyor. Bu, genellikle bir uygulamanın isteğe bağlı özelliklerinin temel işlevselliği için gerekenlerden daha fazla izin gerektirmesi durumunda kullanılır.

* Onay, başlangıçta verildikten sonra iptal edildi.

* Geliştirici uygulamayı her kullanıldığında bir onay istemi gerektirecek şekilde yapılandırmıştır (Not: Bu en iyi yöntem değildir).

## <a name="next-steps"></a>Sonraki adımlar

-   [Azure Active Directory uygulamalar, izinler ve onay (v 1.0 uç noktası)](../develop/quickstart-register-app.md)

-   [Azure Active Directory (v 2.0 uç noktası) kapsamlar, izinler ve onay](../develop/v2-permissions-and-consent.md)
