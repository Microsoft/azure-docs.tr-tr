---
title: PıM-Azure Active Directory 'yi yönetmek için erişim izni ver | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) yönetmek için diğer yönetimlere nasıl erişim sağlayacağınızı öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fba46ee5632f7411c433e4bba29201c59c552f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835212"
---
# <a name="delegate-access-to-privileged-identity-management"></a>Privileged Identity Management erişim yetkisi verme

Privileged Identity Management (PıM) erişimi sağlamak için bir genel yönetici, ayrıcalıklı rol yöneticisi rolüne diğer kullanıcıları atayabilir. Varsayılan olarak, güvenlik yöneticileri ve güvenlik okuyucuları Privileged Identity Management için salt okuma erişimine sahiptir. Privileged Identity Management erişim izni vermek için, ilk Kullanıcı diğerlerini **ayrıcalıklı rol yöneticisi** rolüne atayabilir. Ayrıcalıklı rol yöneticisi rolü yalnızca Azure AD rollerinin yönetilmesi için gereklidir. Ayrıcalıklı rol Yöneticisi izinleri, Azure kaynakları ayarlarını yönetmek için gerekli değildir.

> [!NOTE]
> Privileged Identity Management yönetimi için Azure AD Multi-Factor Authentication gerekir. Microsoft hesapları Azure AD Multi-Factor Authentication için kayıt yaptıramadığı için, bir Microsoft hesabı ile oturum açan bir Kullanıcı Privileged Identity Management erişemez.

Bir kullanıcının kilitli olması veya hesaplarının silinmesi durumunda, ayrıcalıklı rol yöneticisi rolünde her zaman en az iki kullanıcı bulunduğundan emin olun.

## <a name="delegate-access-to-manage-pim"></a>PıM 'yi yönetmek için erişim yetkisi verme

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Azure AD 'de **Privileged Identity Management** açın.

1. **Azure AD rolleri**' ni seçin.

1. **Rolleri** seçin.

    ![Azure AD rollerini Privileged Identity Management-roller](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Üyeler sayfasını açmak için **ayrıcalıklı rol yöneticisi** rolünü seçin.

    ![Ayrıcalıklı rol yöneticisi-Üyeler](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. **Yönetilen Üyeler Ekle** bölmesini açmak Için **üye Ekle** ' yi seçin.

1. **Üyeleri Seç bölmesini açmak** için **üyeleri** Seç ' i seçin.

    ![Ayrıcalıklı rol yöneticisi-üyeleri seçin](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Bir üye seçip **Seç**' e tıklayın.

1. Üyeyi **ayrıcalıklı rol yöneticisi** rolüne uygun hale getirmek için **Tamam ' ı** seçin.

    Privileged Identity Management birine yeni bir rol atadığınızda, rol etkinleştirmek için **uygun** şekilde otomatik olarak yapılandırılır.

1. Üyeyi kalıcı hale getirmek için ayrıcalıklı rol yöneticisi üye listesinden kullanıcıyı seçin.

1. **Daha fazla** ' yı seçin ve sonra atamayı kalıcı hale getirmek Için **kalıcı hale getirin** .

    ![Ayrıcalıklı rol yöneticisi-kalıcı yap](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Kullanıcıya [Privileged Identity Management kullanmaya başlamak](pim-getting-started.md)için bir bağlantı gönderin.

## <a name="remove-access-to-manage-pim"></a>PıM 'yi yönetmek için erişimi kaldır

Ayrıcalıklı rol Yöneticisi rolünden birini kaldırmadan önce, en az iki kullanıcı atanmış olduğundan emin olun.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. **Azure AD Privileged Identity Management** açın.

1. **Azure AD rolleri**' ni seçin.

1. **Rolleri** seçin.

1. Üyeler sayfasını açmak için **ayrıcalıklı rol yöneticisi** rolünü seçin.

1. Kaldırmak istediğiniz kullanıcının yanındaki onay kutusunu işaretleyin ve ardından **üyeyi kaldır**' ı seçin.

    ![Ayrıcalıklı rol yöneticisi-üyeyi kaldır](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Üyeyi rolden kaldırmak istediğinizi onaylamanız istendiğinde **Evet**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management'ı kullanmaya başlama](pim-getting-started.md)
