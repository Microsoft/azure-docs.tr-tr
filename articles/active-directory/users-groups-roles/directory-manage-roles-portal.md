---
title: Yönetici rolü izinlerini görüntüleme ve atama-Azure AD | Microsoft Docs
description: Artık portalda bir Azure AD yönetici rolünün üyelerini görebilir ve yönetebilirsiniz. Rol atamalarını sıklıkla yönetenler için.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ac62a4f318690a1e8156d619c82e9b936973159
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027892"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Azure Active Directory yönetici rollerini görüntüleme ve atama

Artık Azure Active Directory portalında yönetici rollerinin tüm üyelerini görebilir ve yönetebilirsiniz. Rol atamalarını sık sık yönetiyorsanız, büyük olasılıkla bu deneyimi tercih edersiniz. "Bu rollerin ne kadar iyi olduğunu merak edebilir?" seçeneğini aldıysanız, her bir Azure AD yönetici rolü için izinlerin ayrıntılı bir listesini görebilirsiniz.

## <a name="view-all-roles"></a>Tüm rolleri görüntüle

Azure Active Directory, tüm kullanılabilir rollerin listesini görmek için **Roller ve yöneticiler** ' i seçin. 

Rolün ayrıntılı açıklamasını açmak için her satırın sağ tarafındaki üç noktaya tıklayın.

![Azure AD portalındaki rollerin listesi](./media/directory-manage-roles-portal/role-list.png)

## <a name="view-my-roles"></a>Rollerimi görüntüle

Kendi izinlerinizi de kolayca görüntüleyebilirsiniz. Size atanmış olan rolleri görmek için **Roller ve yöneticiler** sayfasında **rolünüzü** seçin.

## <a name="view-assignments-for-a-role"></a>Bir rol için atamaları görüntüleme

Role atanan kullanıcıları görüntülemek için bir role tıklayın. Ek yönetim özellikleri için **PIM 'de Yönet '** i seçebilirsiniz. Ayrıcalıklı rol yöneticileri "kalıcı" (rol içinde her zaman etkin) atamalarıyla "uygun" (yalnızca yükseltilmiş olduğunda rol) olarak değişiklik yapabilir. PıM yoksa, denemeye kaydolmak için **PIM 'de Yönet** ' i yine de seçebilirsiniz. Privileged Identity Management, bir [Azure AD Premium P2 lisans planı](../privileged-identity-management/subscription-requirements.md)gerektirir.

![Yönetici rolünün üyelerinin listesi](./media/directory-manage-roles-portal/member-list.png)

Genel yönetici veya ayrıcalıklı rol yöneticisiyseniz, üyeleri kolayca ekleyebilir veya kaldırabilir, listeyi filtreleyebilir veya etkin atanan rollerini görmek için bir üyeyi seçebilirsiniz.

## <a name="view-a-users-role-permissions"></a>Kullanıcının rol izinlerini görüntüleme

Rolün üyelerini görüntülerken, rol ataması tarafından verilen izinlerin tüm listesini görmek için **Açıklama** ' yı seçin. Sayfa, Dizin rollerini yönetirken size yol göstermeye yardımcı olacak ilgili belgelerin bağlantılarını içerir.

![Yönetici rolü için izin listesi](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD Yönetim rolleri forumundan](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)bizimle paylaşabilirsiniz.
* Roller ve yönetici rolü atama hakkında daha fazla bilgi için bkz. [yönetici rolleri atama](directory-assign-admin-roles.md).
* Varsayılan Kullanıcı izinleri için bkz. [varsayılan Konuk ve üye Kullanıcı izinlerinin karşılaştırması](../fundamentals/users-default-permissions.md).
