---
title: Geri yükleme ya da-Azure Active Directory yakın zamanda silinen bir kullanıcıyı kalıcı olarak Kaldır | Microsoft Docs
description: Nasıl geri yüklenebilen kullanıcıları görüntüleyin, silinen bir kullanıcıyı geri yükleme veya Azure Active Directory ile bir kullanıcıyı kalıcı olarak sil.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9864634020ad115a48bdb70a6736733919d0f0d3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561681"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Geri yükleme ya da Azure Active Directory kullanarak son silinen bir kullanıcıyı kaldırma
Bir kullanıcı sildikten sonra hesabı 30 gün için askıya alınmış durumda kalır. Bu 30 günlük penceresi sırasında kullanıcı hesabı, tüm özellikleriyle birlikte geri yüklenebilir. Bu 30 günlük penceresini geçtikten sonra kullanıcı, otomatik olarak ve kalıcı olarak silinir.

Geri yüklenebilen kullanıcılarınızın görüntülemek, silinen bir kullanıcıyı geri yükleme veya Azure portalında Azure Active Directory (Azure AD) kullanarak bir kullanıcıyı kalıcı olarak sil.

>[!Important]
>Ne, ne de Microsoft müşteri desteği kalıcı olarak silinmiş bir kullanıcıyı geri yükleyebilirsiniz.

## <a name="required-permissions"></a>Gerekli izinler
Geri yüklemek ve kullanıcılar kalıcı olarak silmek için aşağıdaki rollerden biri olması gerekir.

- Genel yönetici

- Partner Tier1 Desteği

- Partner Tier2 Desteği

- Kullanıcı yöneticisi

## <a name="view-your-restorable-users"></a>Geri yüklenebilen kullanıcılarınızın görüntüleyin
30 günden kısa süre önce silinmiş olan tüm kullanıcıları görebilirsiniz. Bu kullanıcılar geri yüklenebilir.

### <a name="to-view-your-restorable-users"></a>Geri yüklenebilen kullanıcılarınızın görüntülemek için
1. Kuruluşun genel yönetici hesabını kullanarak [Azure Portal](https://portal.azure.com/) oturum açın.

2. Seçin **Azure Active Directory**seçin **kullanıcılar**ve ardından **silinmiş kullanıcılarını**.

    Geri yüklemek kullanılabilir olan kullanıcıların listesini gözden geçirin.

    ![Kullanıcılar - silinen Kullanıcılar sayfasında, yine de geri kullanıcılarla](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Yakın zamanda silinen bir kullanıcıyı geri yükleme

Bir kullanıcı hesabı kuruluştan silindiğinde, hesap askıya alınma durumundadır ve ilgili tüm kuruluş bilgileri korunur. Bir kullanıcıyı geri yüklediğinizde, bu kuruluş bilgileri de geri yüklenir.

> [!Note]
> Kullanıcı geri yüklendikten sonra, silme sırasında kullanıcıya atanan lisanslar da bu lisanslar için kullanılabilir bir bilgisayar olmasa bile geri yüklenir. Satın aldığınızdan daha fazla lisans kullanıyorsanız, kuruluşunuz lisans kullanımı için geçici olarak uyumsuz olabilir.

### <a name="to-restore-a-user"></a>Bir kullanıcıyı geri yükleme için
1. Üzerinde **kullanıcılar - silinen kullanıcılar** sayfasında aramak ve kullanıcıların kullanımına birini seçin. Örneğin, _Mary Parker_.

2. Seçin **geri yükleme kullanıcı**.

    ![Kullanıcılar - geri yükleme kullanıcı seçeneğinin vurgulandığı ile silinen kullanıcılar sayfası](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Bir kullanıcıyı kalıcı olarak sil
Otomatik silme işlemi için 30 gün beklemeden bir kullanıcıyı kuruluşunuzdan kalıcı olarak silebilirsiniz. Siz başka bir yönetici ya da Microsoft müşteri desteği tarafından kalıcı olarak silinmiş bir kullanıcıyı geri yüklenemez.

>[!Note]
>Kalıcı olarak bir kullanıcı yanlışlıkla silerseniz, yeni bir kullanıcı oluşturun ve önceki tüm bilgileri el ile girmeniz gerekir. Yeni kullanıcı oluşturma hakkında daha fazla bilgi için bkz. [ekleme veya silme kullanıcılar](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Bir kullanıcıyı kalıcı olarak silmek için

1. Üzerinde **kullanıcılar - silinen kullanıcılar** sayfasında aramak ve kullanıcıların kullanımına birini seçin. Örneğin, _Rae Huff_.

2. Seçin **kalıcı olarak silmek**.

    ![Kullanıcılar - geri yükleme kullanıcı seçeneğinin vurgulandığı ile silinen kullanıcılar sayfası](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Sonraki adımlar
Geri veya kullanıcılarınızın silinmiş sonra aşağıdaki temel işlemleri gerçekleştirebilirsiniz:

- [Ekleme veya kullanıcıları Sil](add-users-azure-active-directory.md)

- [Kullanıcılara rol atama](active-directory-users-assign-role-azure-portal.md)

- [Profil bilgileri ekleme veya değiştirme](active-directory-users-profile-azure-portal.md)

- [Başka bir kuruluştan Konuk kullanıcılar ekleme](../b2b/what-is-b2b.md)

Diğer mevcut kullanıcı yönetim görevleri hakkında daha fazla bilgi için, [Azure AD Kullanıcı yönetimi belgeleri](../users-groups-roles/index.yml).
