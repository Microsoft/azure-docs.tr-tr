---
title: PıM-Azure AD 'de Azure AD rol ayarlarını yapılandırma | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içinde Azure AD rol ayarlarını yapılandırmayı öğrenin.
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
ms.openlocfilehash: ee99a8e75fe8da85b1cf82623ed110991db24b66
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021806"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Privileged Identity Management Azure AD rol ayarlarını yapılandırma

Ayrıcalıklı bir rol yöneticisi, uygun rol atamasını etkinleştiren bir kullanıcının deneyimini değiştirme de dahil olmak üzere, Azure Active Directory (Azure AD) kuruluşunda Privileged Identity Management (PıM) özelleştirebilir.

## <a name="open-role-settings"></a>Rol ayarlarını aç

Bir Azure AD rolünün ayarlarını açmak için aşağıdaki adımları izleyin.

1. **Azure AD Privileged Identity Management**açın.

1. **Azure AD rolleri**' ne tıklayın.

1. **Ayarlar**' a tıklayın.

    ![Azure AD rolleri-ayarlar](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. **Roller**' e tıklayın.

1. Ayarlarını yapılandırmak istediğiniz role tıklayın.

    ![Azure AD rolleri-ayarlar roller](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Her rolün ayarlar sayfasında yapılandırabilmeniz için çeşitli ayarlar vardır. Bu ayarlar yalnızca **uygun** atamalar olan kullanıcıları etkiler, **kalıcı** atamalar değildir.

## <a name="activations"></a>Etkinleştir

Rolün süresi dolmadan önce etkin kalacak en uzun süreyi saat cinsinden ayarlamak için **etkinleştirmeler** kaydırıcısını kullanın. Bu değer 1 ila 72 saat arasında olabilir.

## <a name="notifications"></a>Bildirimler

Roller etkinleştirildiğinde yöneticilerin e-posta bildirimleri alıp almamayacağını belirtmek için **Bildirimler** anahtarını kullanın. Bu bildirim, yetkisiz veya ılyasal etkinleştirmeleri saptamak için yararlı olabilir.

**Etkin**olarak ayarlandığında, bildirimlerin gönderileceği yer:

- Ayrıcalıklı rol yöneticisi
- Güvenlik yöneticisi
- Genel yönetici

Daha fazla bilgi için bkz. [Privileged Identity Management e-posta bildirimleri](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Olay/Istek bileti

Uygun yöneticilerin rolünü etkinleştirdiklerinde bilet numarası içermesini gerektirmek için **olay/istek bileti** anahtarını kullanın. Bu uygulama, rol erişim denetimleri daha etkili hale getirir.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Kullanıcıların rollerini etkinleştirebilmeleri için önce MFA ile kimliklerini doğrulamaları gerekip gerekmediğini belirtmek için **Multi-Factor Authentication** anahtarını kullanın. Her bir rolü etkinleştirdiklerinde değil, her oturum için kimliklerini yalnızca bir kez doğrulamalıdır. MFA 'yı etkinleştirdiğinizde göz önünde bulundurmanız gereken iki ipucu vardır:

- E-posta adresleri (genellikle @outlook.com, ancak her zaman değil) için Microsoft hesabı olan kullanıcılar Azure Multi-Factor Authentication için kayıt olamaz. Kullanıcılara Microsoft hesaplarıyla roller atamak istiyorsanız, bu rol için kalıcı Yöneticiler yapmanız veya Multi-Factor Authentication 'ı devre dışı bırakmanız gerekir.
- Azure AD ve Office 365 için yüksek ayrıcalıklı roller için Azure Multi-Factor Authentication 'yi devre dışı bırakamezsiniz. Bu güvenlik özelliği aşağıdaki rollerin korunmasına yardımcı olur:  
  
  - Azure Information Protection Yöneticisi
  - Faturalama yöneticisi
  - Bulut uygulaması Yöneticisi
  - Uyumluluk Yöneticisi
  - Koşullu erişim yöneticisi
  - Dynamics 365 Yöneticisi
  - Müşteri Kasası erişim onaylayıcısı
  - Dizin yazarları
  - Exchange Yöneticisi
  - Genel yönetici
  - Intune Yöneticisi
  - Power BI Yöneticisi
  - Ayrıcalıklı rol yöneticisi
  - Güvenlik yöneticisi
  - SharePoint yöneticisi
  - Skype Kurumsal Yöneticisi
  - Kullanıcı Yöneticisi

Daha fazla bilgi için bkz. [Multi-Factor Authentication ve Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Onay gerektir

Bir rolü etkinleştirmek için gerekli onayı atamak istiyorsanız, bu adımları izleyin.

1. **Onay gerektir** anahtarını **etkin**olarak ayarlayın. Bölme, onaylayanları seçmek için seçeneklerle birlikte genişletilir.

    ![Azure AD rolleri-ayarlar-onay gerektir](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Herhangi bir onaylayan belirtmezseniz, ayrıcalıklı rol yöneticisi varsayılan onaylayan olur ve ardından bu rolün tüm etkinleştirme isteklerini onaylaması gerekir.

1. Onaylayanları belirtmek için **onaylayanları Seç**' e tıklayın.

    ![Azure AD rolleri-ayarlar-onay gerektir](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Ayrıcalıklı rol yöneticisine ek olarak bir veya daha fazla onaylayan seçin ve ardından **Seç**' e tıklayın. Kullanıcılar veya gruplar ' ı seçebilirsiniz. En az iki onaylayan olması önerilir. Kendinizi onaylayan olarak eklemeseniz bile, bir rol etkinleştirmeyi kendi kendine onaylayamazsınız. Seçimleriniz seçili onaylayanlar listesinde görünür.

1. Tüm rol ayarlarınızı belirledikten sonra değişikliklerinizi kaydetmek için **Kaydet** ' i seçin.

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure AD rolleri atama](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management Azure AD rolleri için güvenlik uyarılarını yapılandırma](pim-how-to-configure-security-alerts.md)
