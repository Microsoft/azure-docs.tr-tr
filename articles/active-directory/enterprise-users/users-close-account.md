---
title: Yönetilmeyen bir Azure AD kuruluşunda iş veya okul hesabını kapatma
description: Yönetilmeyen bir Azure Active Directory iş veya okul hesabınızı kapatma.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/03/2020
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 252c993f5679d80cda97a6fade08f5f92489840d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575593"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-azure-ad-organization"></a>Yönetilmeyen bir Azure AD kuruluşunda iş veya okul hesabınızı kapatın

Yönetilmeyen bir Azure Active Directory (Azure AD) kuruluşunda bir kullanıcı ise ve bu kuruluştan uygulama kullanmanıza veya bununla ilişkili herhangi bir ilişkilendirmeyi sürdürmenize gerek kalmadığında, hesabınızı dilediğiniz zaman kapatabilirsiniz. Yönetilmeyen bir kuruluşun genel Yöneticisi yoktur. Yönetilmeyen bir kuruluştaki kullanıcılar, bir yönetici ile iletişim kurmak zorunda kalmadan hesaplarını kendi kendilerine kapatabilir.

Yönetilmeyen bir kuruluştaki kullanıcılar genellikle self servis kaydolma sırasında oluşturulur. Örnek, bir kuruluşta ücretsiz bir hizmete kaydolan bir bilgi çalışanı olabilir. Self Servis kaydolma hakkında daha fazla bilgi için bkz. [Azure Active Directory için self servis kaydolma nedir?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Başlamadan önce

Hesabınızı kapatabilmeniz için önce aşağıdaki öğeleri onaylamanız gerekir:

* Yönetilmeyen bir Azure AD kuruluşunun kullanıcısı olduğunuzdan emin olun. Yönetilen bir kuruluşa aitseniz hesabınızı kapatamazsınız. Yönetilen bir kuruluşa aitseniz ve hesabınızı kapatmak istiyorsanız yöneticinize başvurmanız gerekir. Yönetilmeyen bir kuruluşa ait olup olmadığınızı belirleme hakkında daha fazla bilgi için bkz. [kullanıcıyı yönetilmeyen kiracıdan silme](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Tutmak istediğiniz tüm verileri kaydedin. Dışarı aktarma isteği gönderme hakkında daha fazla bilgi için bkz. [yönetilmeyen kiracılar için sistem tarafından oluşturulan günlüklere erişme ve dışarı aktarma](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Hesabınızı kapatmak geri alınamaz. Hesabınızı kapattığınızda tüm kişisel veriler kaldırılır. Hesabınıza ve hesabınızla ilişkili verilerinize artık erişemeyecektir.

## <a name="close-your-account"></a>Hesabınızı kapatma

Yönetilmeyen bir iş veya okul hesabını kapatmak için şu adımları izleyin:

1. Kapatmak istediğiniz hesabı kullanarak [hesabınızı kapatmak](https://go.microsoft.com/fwlink/?linkid=873123)için oturum açın.

1. **Veri isteklerim** sayfasında **Hesabı kapat**' ı seçin.

    ![Veri isteklerim-hesabı kapat](./media/users-close-account/close-account.png)

1. Onay iletisini gözden geçirin ve ardından **Evet**' i seçin.

    ![Veri isteklerim-kapatmayı Onayla](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory için self servis kaydolma nedir?](directory-self-service-signup.md)
- [Yönetilmeyen Kiracıdan kullanıcıyı silme](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Yönetilmeyen Kiracılar için sistem tarafından oluşturulan günlüklere erişme ve bu günlükleri dışarı aktarma](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)