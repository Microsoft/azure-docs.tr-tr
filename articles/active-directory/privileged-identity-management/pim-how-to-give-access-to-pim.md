---
title: PıM-Azure Active Directory 'yi yönetmek için erişim izni ver | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) yönetmek için diğer yönetimlere nasıl erişim sağlayacağınızı öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7424e92f8520d13137b6ac8787523095058a005f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022122"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>Privileged Identity Management yönetmek için diğer yöneticilere erişim izni verin

Bir kuruluş için Privileged Identity Management (PıM) etkinleştiren genel yönetici, rol atamaları ve Privileged Identity Management erişimi otomatik olarak alır. Azure Active Directory (Azure AD) kuruluşunuzda başka hiç kimse varsayılan olarak yazma erişimi alır, ancak diğer genel yöneticiler dahil değildir. Diğer genel Yöneticiler, güvenlik yöneticileri ve güvenlik okuyucuları Privileged Identity Management için salt okuma erişimine sahiptir. Privileged Identity Management erişim izni vermek için, ilk Kullanıcı diğerlerini **ayrıcalıklı rol yöneticisi** rolüne atayabilir.

> [!NOTE]
> Privileged Identity Management yönetimi için Azure Multi-Factor Authentication gerekir. Microsoft hesapları Azure Multi-Factor Authentication için kaydettirilemediğinden, bir Microsoft hesabı ile oturum açan bir Kullanıcı Privileged Identity Management erişemez.

Bir kullanıcının kilitli olması veya hesaplarının silinmesi durumunda, ayrıcalıklı rol yöneticisi rolünde her zaman en az iki kullanıcı bulunduğundan emin olun.

## <a name="grant-access-to-manage-pim"></a>PıM 'yi yönetmek için erişim izni verme

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Azure AD 'de **Privileged Identity Management**açın.

1. **Azure AD rolleri**' ni seçin.

1. **Rolleri**seçin.

    ![Azure AD rollerini Privileged Identity Management-roller](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Üyeler sayfasını açmak için **ayrıcalıklı rol yöneticisi** rolünü seçin.

    ![Ayrıcalıklı rol yöneticisi-Üyeler](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Yönetilen Üyeler Ekle bölmesini açmak için **üye Ekle** ' yi seçin.

1. Üyeleri Seç bölmesini açmak için **üyeleri** Seç ' i seçin.

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

1. **Azure AD Privileged Identity Management**açın.

1. **Azure AD rolleri**' ni seçin.

1. **Rolleri**seçin.

1. Üyeler sayfasını açmak için **ayrıcalıklı rol yöneticisi** rolünü seçin.

1. Kaldırmak istediğiniz kullanıcının yanındaki onay kutusunu işaretleyin ve ardından **üyeyi kaldır**' ı seçin.

    ![Ayrıcalıklı rol yöneticisi-üyeyi kaldır](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Üyeyi rolden kaldırmak istediğinizi onaylamanız istendiğinde **Evet**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management kullanmaya başlayın](pim-getting-started.md)
