---
title: Bir uygulamaya kiracı genelinde yönetici onayı verme-Azure AD
description: Uygulamaya oturum açarken son kullanıcılardan onay istenmemesi için bir uygulamaya kiracı genelinde onay verme hakkında bilgi edinin.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: iangithinji
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd5017b1437b0f07553e798ab1d96de15fafb3f9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374191"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Uygulamaya kiracı genelinde yönetici onayı verme

  Bir uygulamaya kiracı genelinde yönetici onayı verme hakkında bilgi edinin. Bu makale, bunu başarmanın farklı yollarını sağlar.

Uygulamalara yönelik yarışmaya yönelik daha fazla bilgi için bkz. [Azure Active Directory izin çerçevesi](../develop/consent-framework.md).

## <a name="prerequisites"></a>Önkoşullar

Kiracı genelinde yönetici onayı verilmesi, kuruluş adına onay yetkisi veren bir kullanıcı olarak oturum açmanızı gerektirir. Bu, [genel yönetici](../roles/permissions-reference.md#global-administrator) ve [ayrıcalıklı rol yöneticisini](../roles/permissions-reference.md#privileged-role-administrator)ve bazı uygulamalar, [Uygulama Yöneticisi](../roles/permissions-reference.md#application-administrator) ve [bulut uygulaması Yöneticisi](../roles/permissions-reference.md#cloud-application-administrator)için içerir. Kullanıcılara, [uygulamalara izin verme iznini](../roles/custom-consent-permissions.md)içeren [özel bir dizin rolü](../roles/custom-create.md) atanırsa, kiracı genelinde izin verme yetkisi de verebilir.

> [!WARNING]
> Bir uygulamaya kiracı genelinde yönetici onayı verilmesi, uygulamanın ve uygulamanın yayımcısının kuruluşunuzun verilerine erişmesini sağlar. İzin vermeden önce uygulamanın istediği izinleri dikkatle gözden geçirin.

> [!IMPORTANT]
> Bir uygulamaya kiracı genelinde yönetici onayı verildiğinde, Kullanıcı Ataması gerektirecek şekilde yapılandırılmadığı takdirde tüm kullanıcılar uygulamada oturum açabilirler. Hangi kullanıcıların bir uygulamada oturum açmasını kısıtlamak için Kullanıcı Ataması gerekli kılın ve ardından uygulamaya Kullanıcı veya grup atayabilirsiniz. Daha fazla bilgi için bkz. [kullanıcıları ve grupları atamaya yönelik yöntemler](./assign-user-or-group-access-portal.md).

## <a name="grant-admin-consent-from-the-azure-portal"></a>Azure portal yönetici onayı verme

### <a name="grant-admin-consent-in-enterprise-apps"></a>Kurumsal uygulamalarda yönetici onayı verme

Uygulama kiracınızda zaten sağlanmış ise *Kurumsal uygulamalar* aracılığıyla kiracı genelinde yönetici izni verebilirsiniz. Örneğin, en az bir Kullanıcı uygulamaya zaten verilmişse, kiracınızda bir uygulama sağlanabilir. Daha fazla bilgi için bkz. [uygulamaların nasıl ve neden eklendiği Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

**Kuruluş uygulamalarında** listelenen bir uygulamaya kiracı genelinde yönetici onayı vermek için:

1. [Azure Portal](https://portal.azure.com) [genel yönetici](../roles/permissions-reference.md#global-administrator), [Uygulama Yöneticisi](../roles/permissions-reference.md#application-administrator)veya [bulut uygulama Yöneticisi](../roles/permissions-reference.md#cloud-application-administrator)olarak oturum açın.
2. **Azure Active Directory** **Kurumsal uygulamalar**' ı seçin.
3. Kiracı genelinde yönetici onayı vermek istediğiniz uygulamayı seçin.
4. **İzinler** ' i seçin ve ardından **yönetici izni ver**' e tıklayın.
5. Uygulamanın gerektirdiği izinleri dikkatle gözden geçirin.
6. Uygulamanın gerektirdiği izinleri kabul ediyorsanız izin verin. Aksi takdirde, **iptal** ' e tıklayın veya pencereyi kapatın.

> [!WARNING]
> **Kurumsal uygulamalar** aracılığıyla kiracı genelinde yönetici onayı verilmesi, daha önce kiracı genelinde verilen tüm izinleri iptal eder. Kullanıcıları kendi adına önceden vermiş olan izinler etkilenmez. 

### <a name="grant-admin-consent-in-app-registrations"></a>Uygulama kayıtları yönetici onayı verme

Kuruluşunuzun geliştirdiği veya doğrudan Azure AD kiracınızda kayıtlı olduğu uygulamalar için, Azure portal **uygulama kayıtları** kiracı genelinde yönetici onayı da verebilirsiniz.

**Uygulama kayıtları** kiracı genelinde yönetici onayı vermek için:

1. [Azure Portal](https://portal.azure.com) [genel yönetici](../roles/permissions-reference.md#global-administrator), [Uygulama Yöneticisi](../roles/permissions-reference.md#application-administrator)veya [bulut uygulama Yöneticisi](../roles/permissions-reference.md#cloud-application-administrator)olarak oturum açın.
2. **Azure Active Directory** **uygulama kayıtları** seçin.
3. Kiracı genelinde yönetici onayı vermek istediğiniz uygulamayı seçin.
4. **API izinleri** ' ni seçin ve ardından **yönetici izni ver**' e tıklayın.
5. Uygulamanın gerektirdiği izinleri dikkatle gözden geçirin.
6. Uygulamanın gerektirdiği izinleri kabul ediyorsanız izin verin. Aksi takdirde, **iptal** ' e tıklayın veya pencereyi kapatın.

> [!WARNING]
> **Uygulama kayıtları** aracılığıyla kiracı genelinde yönetici onayı verilmesi, daha önce kiracı genelinde verilen tüm izinleri iptal eder. Kullanıcıları kendi adına önceden vermiş olan izinler etkilenmez. 

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Kiracı genelinde yönetici onayı vermek için URL oluşturun

Yukarıda açıklanan yöntemlerden birini kullanarak kiracı genelinde yönetici onayı verirken, kiracı genelinde yönetici onayı istemek için Azure portal bir pencere açılır. Uygulamanın istemci KIMLIĞINI (uygulama KIMLIĞI olarak da bilinir) biliyorsanız, kiracı genelinde yönetici izni vermek için aynı URL 'YI de oluşturabilirsiniz.

Kiracı genelinde yönetici onay URL 'SI aşağıdaki biçime uyar:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

burada:

* `{client-id}` uygulamanın istemci KIMLIĞI (uygulama KIMLIĞI olarak da bilinir) olur.
* `{tenant-id}` Kuruluşunuzun kiracı KIMLIĞI veya doğrulanmış herhangi bir etki alanı adıdır.

Her zaman olduğu gibi, izin vermeden önce uygulama isteklerinin izinlerini dikkatle gözden geçirin.

> [!WARNING]
> Bu URL aracılığıyla kiracı genelinde yönetici onayı verilmesi, daha önce kiracı genelinde verilen tüm izinleri iptal eder. Kullanıcıları kendi adına önceden vermiş olan izinler etkilenmez. 

## <a name="next-steps"></a>Sonraki adımlar

[Son kullanıcıların uygulamalara onay verme şeklini yapılandırma](configure-user-consent.md)

[Yönetici onayı iş akışını yapılandırma](configure-admin-consent-workflow.md)

[Microsoft Identity platformunda izinler ve onay](../develop/v2-permissions-and-consent.md)

[Microsoft Q&A 'da Azure AD](/answers/topics/azure-active-directory.html)
