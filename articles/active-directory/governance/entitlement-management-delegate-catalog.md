---
title: Azure AD Yetkilendirme Yönetimi 'nde Katalog oluşturucuları için erişim yönetimine temsilci seçme-Azure Active Directory
description: BT yöneticilerinde erişim yönetimini, erişimi yönetebilmeleri için Katalog oluşturucuları ve proje yöneticilerine nasıl atayacağınızı öğrenin.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f71007b886d3cc25a7cf9dc23d784144ed4e1fbd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174386"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Azure AD yetkilendirme yönetiminde Katalog oluşturucuları için erişim yönetimine temsilci seçme

Yönetici olmayan kullanıcılara temsilci seçmek için, bu kullanıcıları kendi kataloglarını oluşturabilmeleri için Azure AD Yetkilendirme Yönetimi tanımlı Katalog Oluşturucu rolüne ekleyebilirsiniz. Bireysel kullanıcılar ekleyebilir veya üyeleri, daha sonra kataloglar oluşturabilebilen bir grup ekleyebilirsiniz.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>BT Yöneticisi olarak, bir katalog oluşturucuya temsilci seçme

Bir kullanıcıyı Katalog Oluşturucu rolüne atamak için aşağıdaki adımları izleyin.

**Önkoşul rolü:** Genel yönetici veya Kullanıcı Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol taraftaki menüde, **Yetkilendirme Yönetimi** bölümünde, **Ayarlar**' a tıklayın.

1. **Düzenle**’ye tıklayın.

    ![Katalog oluşturucuları ekleme ayarları](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. **Temsilci yetkilendirme yönetimi** bölümünde, bu yetkilendirme yönetimi rolünü atamak istediğiniz kullanıcıları veya grupları seçmek için **Katalog oluşturucuları Ekle** ' ye tıklayın.

1. **Seç**'e tıklayın.

1. **Kaydet** düğmesine tıklayın.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Temsilci rollerinin Azure portal erişmesine izin ver

Katalog oluşturucuları ve erişim paket yöneticileri gibi temsilci rollere izin vermek için, erişim paketlerini yönetmek üzere Azure portal erişim sağlamak için yönetim portalı ayarını denetlemeniz gerekir.

**Önkoşul rolü:** Genel yönetici veya Kullanıcı Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **Kullanıcılar**' a tıklayın.

1. Sol taraftaki menüden **Kullanıcı ayarları**' na tıklayın.

1. **Azure AD Yönetim Portalı 'na erişimi kısıtla** ' nın **Hayır**olarak ayarlandığından emin olun.

    ![Azure AD Kullanıcı ayarları-yönetim portalı](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Kaynak kataloğu oluşturma ve yönetme](entitlement-management-catalog-create.md)
- [Paket yöneticilerine erişim yönetimi yetkisi verme](entitlement-management-delegate-managers.md)

