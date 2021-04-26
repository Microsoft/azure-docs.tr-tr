---
title: PıM-Azure Active Directory için ayrıcalıklı erişim grupları ayarlarını yapılandırma | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) ' de rol atanabilir gruplar ayarlarını yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cfb09f383d8425a644d3e2e87d190b350f5f41a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564645"
---
# <a name="configure-privileged-access-group-settings-preview-in-privileged-identity-management"></a>Privileged Identity Management 'da ayrıcalıklı erişim grubu ayarlarını (Önizleme) yapılandırma

Rol ayarları, Privileged Identity Management (PıM) içindeki grup sahibine ve grup üyesi ayrıcalıklı erişim atamalarına uygulanan varsayılan ayarlardır. Ayrıcalık yükseltme isteklerini kimlerin onaylayamayacağını veya reddettiğini belirtmek üzere onay iş akışını ayarlamak için aşağıdaki adımları kullanın.

## <a name="open-role-settings"></a>Rol ayarlarını aç

Azure ayrıcalıklı erişim grubu rolü ayarlarını açmak için aşağıdaki adımları izleyin.

1. [Ayrıcalıklı rol yöneticisi](../roles/permissions-reference.md#privileged-role-administrator) rolündeki bir kullanıcıyla [Azure Portal](https://portal.azure.com/) için oturum açın.

1. **Azure AD Privileged Identity Management** açın.

1. **Ayrıcalıklı erişim (Önizleme)** seçeneğini belirleyin.

1. Yönetmek istediğiniz grubu seçin.

    ![Bir grup adıyla filtrelenmiş ayrıcalıklı erişim grupları](./media/groups-role-settings/group-select.png)

1. **Ayarlar**'ı seçin.

    ![Ayarlar sayfası seçili grup için Grup ayarlarını listeleme](./media/groups-role-settings/group-settings-select-role.png)

1. Ayarlarını görüntülemek veya değiştirmek istediğiniz sahibi veya üye rolünü seçin. Rol **ayarı ayrıntıları** sayfasında rol için geçerli ayarları görüntüleyebilirsiniz.

    ![Rol ayarı ayrıntıları sayfası çeşitli atama ve etkinleştirme ayarlarını listeleme](./media/groups-role-settings/group-role-setting-details.png)

1. **Rol ayarını Düzenle** sayfasını açmak için **Düzenle** ' yi seçin. **Etkinleştirme** sekmesi, kalıcı olarak uygun ve etkin atamalara izin verilip verilmeyeceğini de içeren rol etkinleştirme ayarlarını değiştirmenize olanak sağlar.

    ![Etkinleştirme sekmesi açık olan rol ayarlarını Düzenle sayfası](./media/groups-role-settings/role-settings-activation-tab.png)

1. Atama ayarları sekmesini açmak için **atama** sekmesini seçin. Bu ayarlar, bu rolün Privileged Identity Management atama ayarlarını denetler.

    ![Rol ayarları sayfasındaki rol atama sekmesi](./media/groups-role-settings/role-settings-assignment-tab.png)

1. Bu rolün bildirim ayarı sekmesine ulaşmak için sayfanın alt kısmındaki **bildirim** sekmesini veya **Sonraki: etkinleştirme** düğmesini kullanın. Bu ayarlar, bu rolle ilgili tüm e-posta bildirimlerini denetler.

    ![Rol ayarları sayfasındaki rol bildirimleri sekmesi](./media/groups-role-settings/role-settings-notification-tab.png)

1. Rol ayarlarını güncelleştirmek için herhangi bir zamanda **Güncelleştir** düğmesini seçin.

Rol ayarları sayfasındaki **Bildirimler** sekmesinde, Privileged Identity Management bildirimleri alan ve aldıkları bildirimler üzerinde ayrıntılı denetim olanağı sunar.

- **E-postayı kapatma**<br>Varsayılan alıcı onay kutusunu temizleyerek ve ek alıcıları silerek belirli e-postaları devre dışı bırakabilirsiniz.  
- **E-postaları belirtilen e-posta adresleriyle sınırla**<br>Varsayılan alıcı onay kutusunu temizleyerek varsayılan alıcılara gönderilen e-postaları devre dışı bırakabilirsiniz. Daha sonra ek alıcılar olarak ek e-posta adresleri ekleyebilirsiniz. Birden fazla e-posta adresi eklemek istiyorsanız, bunları noktalı virgül (;) ile ayırın.
- **Varsayılan alıcılara ve ek alıcılara e-posta gönder**<br>Varsayılan alıcı onay kutusunu seçerek ve ek alıcıların e-posta adreslerini ekleyerek, hem varsayılan alıcıya hem de ek alıcıya e-posta gönderebilirsiniz.
- **Yalnızca kritik e-postalar**<br>Her e-posta türü için, yalnızca kritik e-postaları alma onay kutusunu seçebilirsiniz. Bunun anlamı Privileged Identity Management, yalnızca e-posta acil bir eylem gerektirdiğinde, yapılandırılan alıcılara e-posta gönderilmeye devam edecektir. Örneğin, kullanıcıların rol atamasını genişletmesini isteyen e-postalar tetiklenmeyecektir, bu da yöneticilerin bir uzantı isteğini onaylaması gereken e-posta tetiklenecektir.

## <a name="assignment-duration"></a>Atama süresi

Bir rolün ayarlarını yapılandırırken her atama türü için iki atama süresi seçeneğinden birini seçebilirsiniz (uygun ve etkin). Bu seçenekler, bir Kullanıcı Privileged Identity Management role atandığında varsayılan en uzun süre olur.

**Uygun** atama süresi seçeneklerinden birini belirleyebilirsiniz:

| | Description |
| --- | --- |
| **Kalıcı uygun atamaya izin ver** | Kaynak yöneticileri kalıcı uygun atama atayabilir. |
| **Uygun atamayı sona ermeden önce** | Kaynak yöneticileri, tüm uygun atamaların belirtilen başlangıç ve bitiş tarihine sahip olmasını gerektirebilir. |

Ve bu **etkin** atama süresi seçeneklerinden birini belirleyebilirsiniz:

| | Description |
| --- | --- |
| **Kalıcı etkin atamaya izin ver** | Kaynak yöneticileri, kalıcı etkin atama atayabilir. |
| **Etkin atamanın son kullanım tarihi** | Kaynak yöneticileri, tüm etkin atamaların belirtilen başlangıç ve bitiş tarihine sahip olmasını gerektirebilir. |

> [!NOTE]
> Belirtilen bitiş tarihine sahip tüm atamalar, kaynak yöneticileri tarafından yenilenebilir. Ayrıca, kullanıcılar, [rol atamalarını genişletmek veya yenilemek](pim-resource-roles-renew-extend.md)için self servis istekleri başlatabilir.

## <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication gerektir

Privileged Identity Management, iki ayrı senaryo için isteğe bağlı Azure AD Multi-Factor Authentication zorlaması sağlar.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Etkin atama üzerinde Multi-Factor Authentication gerektir

Bazı durumlarda, bir kullanıcı veya grubu bir rol için kısa bir süre (örneğin, bir gün) atamak isteyebilirsiniz. Bu durumda, atanan kullanıcıların etkinleştirme istemesi gerekmez. Bu senaryoda, Kullanıcı rol atamasını kullandığında, rol atandığı zamandan zaten etkin olduklarından Privileged Identity Management Multi-Factor Authentication 'ı zorlayamıyor.

Atamayı karşılayan kaynak yöneticisinin bunları söyledikleri kim olduğundan emin olmak için, **etkin atama üzerinde Multi-Factor Authentication gerektir** kutusunu işaretleyerek, etkin atamada Multi-Factor Authentication 'ı zorunlu kılabilirsiniz.

### <a name="require-multi-factor-authentication-on-activation"></a>Etkinleştirme üzerinde Multi-Factor Authentication gerektir

Bir rol için uygun olan kullanıcıların etkinleştirebilmeleri için Azure AD Multi-Factor Authentication kimin kullandığını kanıtlamasını zorunlu kılabilirsiniz. Multi-Factor Authentication, kullanıcının makul bir belirsizlik olduğunu söylediklerini sağlar. Bu seçeneğin zorunlu kılınması, Kullanıcı hesabının tehlikeye girdiği durumlarda kritik kaynakları korur.

Etkinleştirmeden önce çok faktörlü kimlik doğrulaması gerektirmek için **etkinleştirme Multi-Factor Authentication gerektir** kutusunu işaretleyin.

Daha fazla bilgi için bkz. [Multi-Factor Authentication ve Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Etkinleştirme en uzun süre

Rolün süresi dolmadan önce etkin kalacağı en uzun süreyi saat cinsinden ayarlamak için **etkinleştirme en uzun süre** kaydırıcısını kullanın. Bu değer bir ile 24 saat arasında olabilir.

## <a name="require-justification"></a>Gerekçe gerektir

Kullanıcıların etkinleştirildiklerinde bir iş doğrulaması girmesini zorunlu kılabilirsiniz. Gerekçe gerektirmek için **etkin atamada gerekçe iste** kutusunu veya **etkinleştirme üzerinde gerekçe iste** kutusunu işaretleyin.

## <a name="require-approval-to-activate"></a>Etkinleştirmek için onay gerektir

Bir rolü etkinleştirmek için onay gerektirmek isterseniz, aşağıdaki adımları izleyin.

1. **Etkinleştirmek için onay gerektir** onay kutusunu işaretleyin.

1. **Bir üye veya grup seçin** sayfasını açmak Için **onaylayanları Seç** ' i seçin.

    ![Onaylayanları seçmek için bir kullanıcı veya grup bölmesi seçin](./media/groups-role-settings/group-settings-select-approvers.png)

1. En az bir kullanıcı veya grup seçin ve ardından **Seç**' e tıklayın. Herhangi bir Kullanıcı ve grup bileşimini ekleyebilirsiniz. En az bir onaylayan seçmelisiniz. Varsayılan onaylayanlar yok.

    Seçimleriniz seçili onaylayanlar listesinde görünür.

1. Tüm rol ayarlarınızı belirledikten sonra, değişikliklerinizi kaydetmek için **Güncelleştir** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [PıM 'de ayrıcalıklı erişim grubu üyeliği veya sahipliği atama](groups-assign-member-owner.md)
