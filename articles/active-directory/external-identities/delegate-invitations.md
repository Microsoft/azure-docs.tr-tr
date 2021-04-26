---
title: B2B dış işbirliği ayarlarını etkinleştirme-Azure AD
description: B2B dış işbirliğini Active Directory etkinleştirmeyi ve konuk kullanıcıları kimlerin davet edebileceğini yönetmeyi öğrenin. Davetleri devretmek için konuk davet eden rolünü kullanın.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 747fa3005930414832878757664f4787157302d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645832"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>B2B dış işbirliğini etkinleştirme ve konuk davet edebilecek kişileri yönetme

Bu makalede Azure Active Directory (Azure AD) B2B işbirliğinin nasıl etkinleştirileceği, konukları kimlerin ziyaretçi davet edebilir ve konuk kullanıcıların Azure AD 'de sahip olduğu izinleri belirleyecekleri açıklanmaktadır. 

Varsayılan olarak, dizininizdeki tüm kullanıcılar ve konuklar bir yönetici rolüne atanmasa bile konukları davet edebilir. Dış işbirliği ayarları, kuruluşunuzdaki farklı türlerdeki kullanıcılar için konuk davetlerini açmanıza veya kapaetmenize olanak tanır. Ayrıca, konukların konuk davet etmesini sağlayan roller atayarak bireysel kullanıcılara davetiye da atayabilirsiniz.

Azure AD, dış konuk kullanıcıların Azure AD dizininizde neleri görebileceğini kısıtlamanıza olanak sağlar. Varsayılan olarak, Konuk kullanıcılar kullanıcıları, grupları veya diğer dizin kaynaklarını listelemelerini engelleyen sınırlı bir izin düzeyine ayarlanır, ancak gizli olmayan grupların üyeliğini görebilirler. Yeni bir önizleme ayarı, konuk erişimini daha da kısıtlamanızı sağlar, böylece konukların yalnızca kendi profil bilgilerini görüntüleyebilmesini sağlayabilirsiniz. Ayrıntılar için bkz. [Konuk erişim Izinlerini kısıtlama (Önizleme)](../enterprise-users/users-restrict-guest-permissions.md).

## <a name="configure-b2b-external-collaboration-settings"></a>B2B dış işbirliği ayarlarını yapılandırma

Azure AD B2B işbirliği ile bir kiracı yöneticisi aşağıdaki davet ilkelerini ayarlayabilir:

- Davetleri kapat
- Yalnızca konuk davet eden rolündeki Yöneticiler ve kullanıcılar davet edebilir
- Yöneticiler, konuk davet eden rolü ve Üyeler davet edebilir
- Konuklar dahil tüm kullanıcılar davet edebilir

Varsayılan olarak, konukları dahil tüm kullanıcılar Konuk kullanıcıları davet edebilir.

### <a name="to-configure-external-collaboration-settings"></a>Dış işbirliği ayarlarını yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) kiracı yöneticisi olarak oturum açın.
2. **Azure Active Directory** seçin.
3. **Dış kimlikler**  >  **dış işbirliği ayarları**' nı seçin.

4. **Konuk Kullanıcı erişimi kısıtlamaları (Önizleme)** altında, konuk kullanıcıların sahip olmasını istediğiniz erişim düzeyini seçin:
  
   - **Konuk kullanıcılar aynı üyelere (en kapsamlı) sahiptir**: Bu seçenek, konuklara Azure AD kaynaklarına ve dizin verilerine üye kullanıcı olarak aynı erişimi sağlar.

   - **Konuk kullanıcıların, dizin nesnelerinin özelliklerine ve üyeliklerine sınırlı erişimi vardır**: (varsayılan) Bu ayar, kullanıcıları, grupları veya diğer dizin kaynaklarını numaralandırma gibi belirli dizin görevlerinin konukları engeller. Konuklar, tüm gizli olmayan grupların üyeliğini görebilir.

   - **Konuk Kullanıcı erişimi, kendi dizin nesnelerinin özellikleri ve üyelikleri (en kısıtlayıcı) ile kısıtlıdır**: Bu ayarla, konuklar yalnızca kendi profillerine erişebilir. Konukların diğer kullanıcıların profillerini, gruplarını veya grup üyeliklerini görmesini izin verilmez.


5. **Konuk davet ayarları** altında, uygun ayarları seçin:

    ![Konuk davet ayarları](./media/delegate-invitations/guest-invite-settings.png)

   - **Konuk davet eden rolündeki Yöneticiler ve kullanıcılar davet edebilir**: yöneticilerin ve kullanıcıların "konuk davet eden" rolünde konukları davet etmesini sağlamak için, bu ilkeyi **Evet** olarak ayarlayın.

   - **Üyeler davet edebilir**: dizininizin yönetici olmayan üyelerinin konukları davet etmesini sağlamak için, bu ilkeyi **Evet** olarak ayarlayın.

   - **Konuklar davet edebilir**: konukların diğer konukları davet etmesini sağlamak için bu ilkeyi **Evet** olarak ayarlayın.

   > [!NOTE]
   > **Üyeler davet** etmek için **Hayır** ve **Yöneticiler ve konuk davet eden rolündeki kullanıcılar davet** **olarak ayarlanırsa,** **konuk davet** eden rolündeki kullanıcılar konukları davet edebilir.

6. **Konuklar için bir kerelik geçiş kodu** altında uygun ayarları seçin (daha fazla bilgi için bkz. e- [posta bir kerelik geçiş kodu kimlik doğrulaması](one-time-passcode.md)):

   - **Ekim 2021 ' de guests için e-posta tek seferlik geçiş kodunu otomatik olarak etkinleştirin**. Varsayılanını E-posta bir kerelik geçiş kodu özelliği kiracınız için zaten etkin değilse, 2021 Ekim 'de otomatik olarak açılır. Özelliğin o anda etkinleştirilmesini istiyorsanız başka bir eylem gerekmez. Özelliği zaten etkinleştirdiyseniz veya devre dışı bıraktığınız takdirde bu seçenek kullanılamaz.

   - **Şimdi geçerli olan konuklar için tek seferlik geçiş kodunu etkinleştirin**. Kiracınız için bir kerelik geçiş kodu özelliğini etkinleştirir.

   - **Konuklar için tek seferlik e-posta geçiş kodunu devre dışı bırakın**. , Kiracınız için bir kerelik geçiş kodu özelliğini kapatır ve özelliğin 2021 Ekim 'de açılmasını önler.

   > [!NOTE]
   > Yukarıdaki seçenekler yerine, bu özelliği etkinleştirdiyseniz veya devre dışı bıraktığınız takdirde veya daha önce önizlemeyi kabul ettiyseniz, aşağıdaki iki durumlu bir geçiş görürsünüz:
   >
   >![E-posta bir kerelik geçiş kodunu etkinleştir](media/delegate-invitations/enable-email-otp-opted-in.png)

7. **Konuk self servis kaydolma özelliğini Kullanıcı akışları aracılığıyla etkinleştir** altında, kullanıcıların uygulamalara kaydolmasına izin veren Kullanıcı akışları oluşturabilmenizi istiyorsanız **Evet** ' i seçin. Bu ayar hakkında daha fazla bilgi için bkz. [bir uygulamaya self servis kaydolma Kullanıcı akışı ekleme](self-service-sign-up-user-flow.md).

    ![Kullanıcı akışları ile Self Servis kaydolma ayarı](./media/delegate-invitations/self-service-sign-up-setting.png)

7. **İşbirliği kısıtlamaları**' nın altında, belirttiğiniz etki alanlarına davetleri izin vermeyi veya reddetmeyi seçebilirsiniz ve metin kutularına belirli etki alanı adlarını girin. Birden çok etki alanı için, her etki alanını yeni bir satıra girin. Daha fazla bilgi için, bkz. [belirli kuruluşlardan B2B kullanıcılarına Izin verme veya bu kullanıcıların davetlerini engelleme](allow-deny-list.md).

    ![İşbirliği kısıtlamaları ayarları](./media/delegate-invitations/collaboration-restrictions.png)
## <a name="assign-the-guest-inviter-role-to-a-user"></a>Konuk davet eden rolünü bir kullanıcıya atama

Konuk davet eden rolü sayesinde, bireysel kullanıcılara bir küresel yönetici veya başka yönetici rolü atamadan konukları davet etme yeteneği verebilirsiniz. Konuk davet eden rolünü kişilere atayın. Daha sonra **, yöneticileri ve konuk davet eden rolünde bulunan kullanıcıları Evet 'e davet edediğinizden** emin olun.

Konuk davet eden rolüne kullanıcı eklemek için PowerShell 'in nasıl kullanılacağını gösteren bir örnek aşağıda verilmiştir:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Sonraki adımlar

Azure AD B2B işbirliği ile ilgili aşağıdaki makalelere bakın:

- [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
- [Bir davetiye olmadan B2B işbirliği Konuk kullanıcıları ekleme](add-user-without-invite.md)
- [Bir role B2B işbirliği kullanıcısı ekleme](add-guest-to-role.md)

