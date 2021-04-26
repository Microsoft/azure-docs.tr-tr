---
title: Azure AD rol tanımlarını listeleme-Azure AD
description: Azure yerleşik ve özel rollerini nasıl listeleyeceğinizi öğrenin.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37b2988d32c854e4184adee998341ebadcee053
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467922"
---
# <a name="list-azure-ad-role-definitions"></a>Azure AD rol tanımlarını listeleyin

Rol tanımı, okuma, yazma ve silme gibi gerçekleştirilebileceği izinlerin bir koleksiyonudur. Genellikle bir rol olarak adlandırılır. Azure Active Directory, 60 yerleşik rolüne sahiptir veya kendi özel rollerinizi oluşturabilirsiniz. "Bu rollerin ne yapacaktı?" seçeneğini hiç merak ediyorsanız, her bir rol için izinlerin ayrıntılı bir listesini görebilirsiniz.

Bu makalede, Azure AD yerleşik ve özel rollerinin izinlerinin yanı sıra nasıl listeleneceğini açıklanmaktadır.

## <a name="list-all-roles"></a>Tüm rolleri Listele

1. [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) oturum açın ve **Azure Active Directory**' yi seçin.

1. Tüm kullanılabilir rollerin listesini görmek için **Roller ve yöneticiler '** i seçin.

    ![Azure AD portalındaki rollerin listesi](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. Sağ tarafta, bir rolün izin listesini görmek için üç noktayı ve sonra **Açıklama** ' yı seçin.

    Bu sayfa, rolleri yönetirken size yol göstermeye yardımcı olacak ilgili belgelerin bağlantılarını içerir.

    !["Genel yönetici-açıklama" sayfasını gösteren ekran görüntüsü.](./media/role-definitions-list/role-description.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD Yönetim rolleri forumundan](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)bizimle paylaşabilirsiniz.
* Roller ve yönetici rolü atama hakkında daha fazla bilgi için bkz. [yönetici rolleri atama](permissions-reference.md).
* Varsayılan Kullanıcı izinleri için bkz. [varsayılan Konuk ve üye Kullanıcı izinlerinin karşılaştırması](../fundamentals/users-default-permissions.md).
