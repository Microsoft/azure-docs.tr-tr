---
title: PıM-Azure AD 'de Azure AD rol ayarlarını yapılandırma | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içinde Azure AD rol ayarlarını yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 676fdf264cb930069425a330c3da9cd753bf8e0a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567042"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Privileged Identity Management Azure AD rol ayarlarını yapılandırma

Ayrıcalıklı bir rol yöneticisi, uygun rol atamasını etkinleştiren bir kullanıcının deneyimini değiştirme de dahil olmak üzere, Azure Active Directory (Azure AD) kuruluşunda Privileged Identity Management (PıM) özelleştirebilir.

## <a name="determine-your-version-of-pim"></a>PıM sürümünüzü belirleme

2019 Kasım 'Dan başlayarak Privileged Identity Management Azure AD rolleri bölümü, Azure Kaynak rolleri deneyimleriyle eşleşen yeni bir sürüme güncelleştiriliyor. Bu, ek özellikleri [ve var olan API üzerinde yapılan değişiklikleri](azure-ad-roles-features.md#api-changes)de oluşturur. Yeni sürüm kullanıma sunulurken, bu makalede izlediğiniz yordamlar Şu anda sahip olduğunuz Privileged Identity Management sürümüne bağlıdır. Hangi Privileged Identity Management sürümünü istediğinizi öğrenmek için bu bölümdeki adımları izleyin. Privileged Identity Management Sürümünüzü öğrendikten sonra bu makaledeki sürümle eşleşen yordamları seçebilirsiniz.

1. [Ayrıcalıklı rol yöneticisi](../roles/permissions-reference.md#privileged-role-administrator) rolünde olan bir kullanıcıyla [Azure Portal](https://portal.azure.com/) oturum açın.
1. **Azure AD Privileged Identity Management** açın. Genel Bakış sayfasının üst kısmında yer alan bir başlık varsa, bu makalenin **Yeni sürüm** sekmesinde yer alan yönergeleri izleyin. Aksi takdirde, **önceki sürüm** sekmesindeki yönergeleri izleyin.

  [![Azure AD > Privileged Identity Management seçin.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Azure AD rolleri için istekleri onaylamak veya reddetmek için bu makaledeki adımları izleyin.

# <a name="new-version"></a>[Yeni sürüm](#tab/new)

## <a name="open-role-settings"></a>Rol ayarlarını aç

Bir Azure AD rolünün ayarlarını açmak için aşağıdaki adımları izleyin.

1. [Ayrıcalıklı rol yöneticisi](../roles/permissions-reference.md#privileged-role-administrator) rolündeki bir kullanıcıyla [Azure Portal](https://portal.azure.com/) için oturum açın.

1.  &gt; **Azure AD rolleri** &gt; **rol ayarlarını** Azure AD Privileged Identity Management açın.

    ![Rol ayarları sayfası Azure AD rollerini listeleme](./media/pim-how-to-change-default-settings/role-settings.png)

1. Ayarlarını yapılandırmak istediğiniz rolü seçin.

    ![Rol ayarı ayrıntıları sayfası çeşitli atama ve etkinleştirme ayarlarını listeleme](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Rol ayarları sayfasını açmak için **Düzenle** ' yi seçin.

    ![Rol ayarları sayfasını düzenleme atama ve etkinleştirme ayarlarını güncelleştirme seçenekleri](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    Her rolün rol ayarı bölmesinde yapılandırabileceğiniz çeşitli ayarlar vardır.

## <a name="assignment-duration"></a>Atama süresi

Bir rolün ayarlarını yapılandırırken her atama türü için iki atama süresi seçeneğinden birini seçebilirsiniz (uygun ve etkin). Bu seçenekler, bir Kullanıcı Privileged Identity Management role atandığında varsayılan en uzun süre olur.

**Uygun** atama süresi seçeneklerinden birini belirleyebilirsiniz:

| | Description |
| --- | --- |
| **Kalıcı uygun atamaya izin ver** | Genel Yöneticiler ve ayrıcalıklı rol yöneticileri kalıcı uygun atama atayabilir. |
| **Uygun atamayı sona ermeden önce** | Genel Yöneticiler ve ayrıcalıklı rol yöneticileri, tüm uygun atamaların belirtilen başlangıç ve bitiş tarihine sahip olmasını gerektirebilir. |

Ve bu **etkin** atama süresi seçeneklerinden birini belirleyebilirsiniz:

| | Description |
| --- | --- |
| **Kalıcı etkin atamaya izin ver** | Genel Yöneticiler ve ayrıcalıklı rol yöneticileri kalıcı etkin atama atayabilir. |
| **Etkin atamanın son kullanım tarihi** | Genel Yöneticiler ve ayrıcalıklı rol yöneticileri, tüm etkin atamaların belirtilen başlangıç ve bitiş tarihine sahip olmasını gerektirebilir. |

> [!NOTE]
> Belirtilen bitiş tarihine sahip tüm atamalar, genel Yöneticiler ve ayrıcalıklı rol yöneticileri tarafından yenilenebilir. Ayrıca, kullanıcılar, [rol atamalarını genişletmek veya yenilemek](pim-resource-roles-renew-extend.md)için self servis istekleri başlatabilir.

## <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication gerektir

Privileged Identity Management, iki ayrı senaryo için isteğe bağlı Azure AD Multi-Factor Authentication zorlaması sağlar.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Etkin atama üzerinde Multi-Factor Authentication gerektir

Bazı durumlarda, bir kullanıcıyı bir rol için kısa bir süre (örneğin, bir gün) atamak isteyebilirsiniz. Bu durumda, atanan kullanıcıların etkinleştirme istemesi gerekmez. Bu senaryoda, Kullanıcı rol atamasını kullandığında, rol atandığı zamandan zaten etkin olduklarından Privileged Identity Management Multi-Factor Authentication 'ı zorlayamıyor.

Atamayı karşılayan yöneticinin bunları söyledikleri kim olduğundan emin olmak için, etkin atama üzerinde **Multi-Factor Authentication gerektir** kutusunu işaretleyerek, etkin atamada Multi-Factor Authentication 'ı zorunlu kılabilirsiniz.

### <a name="require-multi-factor-authentication-on-activation"></a>Etkinleştirme üzerinde Multi-Factor Authentication gerektir

Bir rol için uygun olan kullanıcıların etkinleştirebilmeleri için Azure AD Multi-Factor Authentication kimin kullandığını kanıtlamasını zorunlu kılabilirsiniz. Multi-Factor Authentication, kullanıcının makul bir belirsizlik olduğunu söylediklerini sağlar. Bu seçeneğin zorunlu kılınması, Kullanıcı hesabının tehlikeye girdiği durumlarda kritik kaynakları korur.

Etkinleştirmeden önce çok faktörlü kimlik doğrulaması gerektirmek için, **rol Düzenle ayarının** atama sekmesinde **etkinleştirme Multi-Factor Authentication gerektir** kutusunu işaretleyin.

Daha fazla bilgi için bkz. [Multi-Factor Authentication ve Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Etkinleştirme en uzun süre

Rolün süresi dolmadan önce etkin kalacağı en uzun süreyi saat cinsinden ayarlamak için **etkinleştirme en uzun süre** kaydırıcısını kullanın. Bu değer bir ile 24 saat arasında olabilir.

## <a name="require-justification"></a>Gerekçe gerektir

Kullanıcıların etkinleştirildiklerinde bir iş doğrulaması girmesini zorunlu kılabilirsiniz. Gerekçe gerektirmek için **etkin atamada gerekçe iste** kutusunu veya **etkinleştirme üzerinde gerekçe iste** kutusunu işaretleyin.

## <a name="require-approval-to-activate"></a>Etkinleştirmek için onay gerektir

Birden çok onaylayan ayarlarsanız onay, bunlardan biri onaylar veya reddeder başlamaz tamamlanır. En az iki kullanıcıdan onay almanız gerekmez. Bir rolü etkinleştirmek için onay gerektirmek üzere bu adımları izleyin.

1. **Etkinleştirmek için onay gerektir** onay kutusunu işaretleyin.

1. **Onaylayanları Seç**' i seçin.

    ![Onaylayanları seçmek için bir kullanıcı veya grup bölmesi seçin](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. En az bir kullanıcı seçin ve ardından **Seç**' e tıklayın. En az bir onaylayan seçmelisiniz. Varsayılan onaylayanlar yok.

    Seçimleriniz seçili onaylayanlar listesinde görünür.

1. Tüm rol ayarlarınızı belirledikten sonra, değişikliklerinizi kaydetmek için **Güncelleştir** ' i seçin.

# <a name="previous-version"></a>[Önceki sürüm](#tab/previous)

## <a name="open-role-settings-previous-version"></a>Rol ayarlarını Aç (önceki sürüm)

Bir Azure AD rolünün ayarlarını açmak için aşağıdaki adımları izleyin.

1. **Azure AD Privileged Identity Management** açın.

1. **Azure AD rolleri**' ni seçin.

1. **Ayarlar**'ı seçin.

    ![Azure AD rolleri-ayarlar](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. **Rolleri** seçin.

1. Ayarlarını yapılandırmak istediğiniz rolü seçin.

    ![Azure AD rolleri-ayarlar roller](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Her rolün ayarlar sayfasında yapılandırabilmeniz için çeşitli ayarlar vardır. Bu ayarlar yalnızca **uygun** atamalar olan kullanıcıları etkiler, **kalıcı** atamalar değildir.

## <a name="activations"></a>Etkinleştir

Rolün süresi dolmadan önce etkin kalacak en uzun süreyi saat cinsinden ayarlamak için **etkinleştirmeler** kaydırıcısını kullanın. Bu değer 1 ila 72 saat arasında olabilir.

## <a name="notifications"></a>Bildirimler

Roller etkinleştirildiğinde yöneticilerin e-posta bildirimleri alıp almamayacağını belirtmek için **Bildirimler** anahtarını kullanın. Bu bildirim, yetkisiz veya ılyasal etkinleştirmeleri saptamak için yararlı olabilir.

**Etkin** olarak ayarlandığında, bildirimlerin gönderileceği yer:

- Ayrıcalıklı rol yöneticisi
- Güvenlik yöneticisi
- Genel yönetici

Daha fazla bilgi için bkz. [Privileged Identity Management e-posta bildirimleri](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Olay/Istek bileti

Uygun yöneticilerin rolünü etkinleştirdiklerinde bilet numarası içermesini gerektirmek için **olay/istek bileti** anahtarını kullanın. Bu uygulama, rol erişim denetimleri daha etkili hale getirir.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Kullanıcıların rollerini etkinleştirebilmeleri için önce MFA ile kimliklerini doğrulamaları gerekip gerekmediğini belirtmek için **Multi-Factor Authentication** anahtarını kullanın. Her bir rolü etkinleştirdiklerinde değil, her oturum için kimliklerini yalnızca bir kez doğrulamalıdır. MFA 'yı etkinleştirdiğinizde göz önünde bulundurmanız gereken iki ipucu vardır:

- Kendi e-posta adresleri (genellikle @outlook.com , ancak her zaman değil) Için Microsoft hesabı olan kullanıcılar, Azure AD Multi-Factor Authentication için kayıt yaptıramaz. Kullanıcılara Microsoft hesaplarıyla roller atamak istiyorsanız, bu rol için kalıcı Yöneticiler yapmanız veya Multi-Factor Authentication 'ı devre dışı bırakmanız gerekir.
- Azure AD ve Microsoft 365 için yüksek ayrıcalıklı roller için Azure AD Multi-Factor Authentication devre dışı bırakılamaz. Bu güvenlik özelliği aşağıdaki rollerin korunmasına yardımcı olur:  
  
  - Azure Information Protection Yöneticisi
  - Faturalama yöneticisi
  - Bulut uygulaması Yöneticisi
  - Uyumluluk Yöneticisi
  - Koşullu erişim yöneticisi
  - Dynamics 365 Yöneticisi
  - Müşteri Kasası erişim onaylayıcısı
  - Dizin yazarları
  - Exchange yöneticisi
  - Genel yönetici
  - Intune yöneticisi
  - Power BI Yöneticisi
  - Ayrıcalıklı rol yöneticisi
  - Güvenlik yöneticisi
  - SharePoint yöneticisi
  - Skype Kurumsal yöneticisi
  - Kullanıcı yöneticisi

Daha fazla bilgi için bkz. [Multi-Factor Authentication ve Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Onay gerektir

Bir rolü etkinleştirmek için gerekli onayı atamak istiyorsanız, bu adımları izleyin.

1. **Onay gerektir** anahtarını **etkin** olarak ayarlayın. Bölme, onaylayanları seçmek için seçeneklerle birlikte genişletilir.

    !["Onay ıste" anahtarını "etkinleştir" seçiliyken gösteren ekran görüntüsü.](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Herhangi bir onaylayan belirtmezseniz, ayrıcalıklı rol yöneticisi varsayılan onaylayan olur ve ardından bu rolün tüm etkinleştirme isteklerini onaylaması gerekir.

1. Onaylayanları eklemek için **onaylayanları Seç**' e tıklayın.

    ![Azure AD rolleri-ayarlar-onay gerektir](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Ayrıcalıklı rol yöneticisine ek olarak bir veya daha fazla onaylayan seçin ve ardından **Seç**' e tıklayın. En az iki onaylayan eklemenizi öneririz. Kendinizi onaylayan olarak eklemeseniz bile, bir rol etkinleştirmeyi kendi kendine onaylayamazsınız. Seçimleriniz seçili onaylayanlar listesinde görünür.

1. Tüm rol ayarlarınızı belirledikten sonra değişikliklerinizi kaydetmek için **Kaydet** ' i seçin.

---

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure AD rolleri atama](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management Azure AD rolleri için güvenlik uyarılarını yapılandırma](pim-how-to-configure-security-alerts.md)
