---
title: Son silinen kullanıcıyı geri yükleme veya kalıcı olarak kaldırma-Azure AD
description: Yeniden yüklenebilen kullanıcıları görüntüleme, silinen bir kullanıcıyı geri yükleme veya Azure Active Directory olan bir kullanıcıyı kalıcı olarak silme.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/23/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4d42e1cafa58aaf6a47188c9e86c24ac175047
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514398"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Azure Active Directory kullanarak yakın zamanda silinen bir kullanıcıyı geri yükleme veya kaldırma
Bir kullanıcıyı sildikten sonra, hesap 30 gün boyunca askıya alınmış durumda kalır. Bu 30 günlük pencerede, Kullanıcı hesabı tüm özellikleriyle birlikte geri yüklenebilir. 30 günlük pencere geçtikten sonra kalıcı silme işlemi otomatik olarak başlatılır.

Yeniden yüklenebilen kullanıcılarınızı görüntüleyebilir, silinen bir kullanıcıyı geri yükleyebilir veya Azure portal Azure Active Directory (Azure AD) kullanarak bir kullanıcıyı kalıcı olarak silebilirsiniz.

>[!Important]
>Kalıcı olarak silinen bir kullanıcıyı ne de Microsoft müşteri desteği geri yükleyebilir.

## <a name="required-permissions"></a>Gerekli izinler
Kullanıcıları geri yüklemek ve kalıcı olarak silmek için aşağıdaki rollerden birine sahip olmanız gerekir.

- Genel yönetici

- Partner Katman1 desteği

- Partner Katman2 desteği

- Kullanıcı yöneticisi

## <a name="view-your-restorable-users"></a>Geri yüklenebilen kullanıcılarınızı görüntüleme
30 günden daha önce silinen tüm kullanıcıları görebilirsiniz. Bu kullanıcılar geri yüklenebilir.

### <a name="to-view-your-restorable-users"></a>Geri yüklenebilen kullanıcılarınızı görüntülemek için
1. Kuruluşun genel yönetici hesabını kullanarak [Azure Portal](https://portal.azure.com/) oturum açın.

2. **Azure Active Directory** seçin, **Kullanıcılar**' ı seçin ve ardından **silinen kullanıcılar**' ı seçin.

    Geri yükleme için kullanılabilen kullanıcıların listesini gözden geçirin.

    ![Kullanıcılar tarafından silinen kullanıcılar sayfası, hala geri yüklenebilecek kullanıcılar](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Son silinen bir kullanıcıyı geri yükleme

Bir kullanıcı hesabı kuruluştan silindiğinde, hesap askıya alınma durumundadır ve ilgili tüm kuruluş bilgileri korunur. Bir kullanıcıyı geri yüklediğinizde, bu kuruluş bilgileri de geri yüklenir.

> [!Note]
> Kullanıcı geri yüklendikten sonra, silme sırasında kullanıcıya atanan lisanslar da bu lisanslar için kullanılabilir bir bilgisayar olmasa bile geri yüklenir. Satın aldığınızdan daha fazla lisans kullanıyorsanız, kuruluşunuz lisans kullanımı için geçici olarak uyumsuz olabilir.

### <a name="to-restore-a-user"></a>Bir kullanıcıyı geri yüklemek için
1. **Kullanıcılar tarafından silinen kullanıcılar** sayfasında, kullanılabilir kullanıcılardan birini arayıp seçin. Örneğin, _Mary Parker_.

2. **Kullanıcıyı geri yükle**' yi seçin.

    ![Kullanıcılar tarafından silinen kullanıcılar sayfası, restore user seçeneği vurgulanmış](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Bir kullanıcıyı kalıcı olarak silme
Otomatik silme işlemi için 30 gün beklemeden bir kullanıcıyı kuruluşunuzdan kalıcı olarak silebilirsiniz. Kalıcı olarak silinen bir Kullanıcı siz, başka bir yönetici veya Microsoft müşteri desteği tarafından geri yüklenemez.

>[!Note]
>Bir kullanıcıyı yanlışlıkla kalıcı olarak silerseniz, yeni bir kullanıcı oluşturmanız ve önceki tüm bilgileri el ile girmeniz gerekir. Yeni bir Kullanıcı oluşturma hakkında daha fazla bilgi için bkz. [Kullanıcı ekleme veya silme](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Bir kullanıcıyı kalıcı olarak silmek için

1. **Kullanıcılar tarafından silinen kullanıcılar** sayfasında, kullanılabilir kullanıcılardan birini arayıp seçin. Örneğin, _Oye Huff_.

2. **Kalıcı olarak sil**' i seçin.

    ![Kullanıcıları silinen kullanıcılar sayfası, Kullanıcı Sil seçeneği vurgulandı](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Sonraki adımlar
Kullanıcılarınızı geri yükledikten veya sildikten sonra şunları yapabilirsiniz:

- [Kullanıcı ekleme veya silme](add-users-azure-active-directory.md)

- [Kullanıcılara rol atama](active-directory-users-assign-role-azure-portal.md)

- [Profil bilgilerini ekleme veya değiştirme](active-directory-users-profile-azure-portal.md)

- [Başka bir kuruluştan Konuk kullanıcılar ekleme](../external-identities/what-is-b2b.md)

Diğer mevcut kullanıcı yönetim görevleri hakkında daha fazla bilgi için, [Azure AD Kullanıcı yönetimi belgeleri](../enterprise-users/index.yml).
