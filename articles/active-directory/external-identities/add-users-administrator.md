---
title: Azure portal B2B işbirliği kullanıcılarını ekleme-Azure AD
description: Bir yöneticinin Azure Active Directory (Azure AD) B2B işbirliği kullanarak bir iş ortağı kuruluştan kendi dizinine nasıl Konuk kullanıcılar ekleyekullanabileceğinizi gösterir.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2566b427777aec273df17863f06040de20649d97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581856"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Azure portal Azure Active Directory B2B işbirliği kullanıcıları ekleyin

Sınırlı yönetici dizin rollerinin herhangi birini atayan bir kullanıcı olarak, B2B işbirliği kullanıcılarını davet etmek için Azure portal kullanabilirsiniz. Konuk kullanıcıları dizine, gruba veya bir uygulamaya davet edebilirsiniz. Bu yöntemlerin herhangi biriyle bir kullanıcıyı davet ettikten sonra, *davet edilen kullanıcının* hesabı, bir kullanıcı türü olan Azure Active Directory (Azure AD) öğesine eklenir. Konuk Kullanıcı, kaynaklara erişmek için davetlerinden önce kullanılmalıdır. Kullanıcının bir davetinin kullanım süreleri dolmaz.

Bir konuk kullanıcıyı dizine ekledikten sonra, Konuk kullanıcıyı paylaşılan bir uygulamaya doğrudan bir bağlantı gönderebilirsiniz veya Konuk Kullanıcı davet e-postasında kullanım URL 'sini tıklatabilir. Kullanım süreci hakkında daha fazla bilgi için bkz. [B2B işbirliği daveti](redemption-experience.md)kullanım.

> [!IMPORTANT]
> Kuruluşunuzun gizlilik bildiriminin URL 'sini eklemek için [nasıl yapılır: kuruluşunuzun gizlilik Azure Active Directory bilgilerini ekleme](../fundamentals/active-directory-properties-area.md) bölümündeki adımları izlemeniz gerekir. İlk davet kullanım sürecinin bir parçası olarak, davet edilen bir kullanıcının devam etmesi için Gizlilik koşullarınızı onaylaması gerekir. 

## <a name="before-you-begin"></a>Başlamadan önce

Kuruluşunuzun dış işbirliği ayarlarının, konukları davet etmenize izin verilecek şekilde yapılandırıldığından emin olun. Varsayılan olarak, tüm kullanıcılar ve yöneticiler konukları davet edebilir. Ancak kuruluşunuzun dış işbirliği ilkeleri, belirli türdeki Kullanıcı veya yöneticilerin konukları davet etmelerini engelleyecek şekilde yapılandırılmış olabilir. Bu ilkeleri görüntüleme ve ayarlama hakkında bilgi edinmek için bkz. [B2B dış Işbirliğini etkinleştirme ve konukları davet edebilen kişileri yönetme](delegate-invitations.md).

## <a name="add-guest-users-to-the-directory"></a>Dizine Konuk kullanıcılar ekleme

Dizine B2B işbirliği kullanıcıları eklemek için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com) , sınırlı bir yönetici dizin rolü veya konuk davet eden rolü atanan bir kullanıcı olarak oturum açın.
2. Herhangi bir sayfadan **Azure Active Directory** arayın ve seçin.
3. **Yönet** bölümünde **Kullanıcılar**'ı seçin.
4. **Yeni konuk kullanıcı**’yı seçin.

   ![Yeni Konuk kullanıcının Kullanıcı arabiriminde nerede olduğunu gösterir](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
5. **Yeni Kullanıcı** sayfasında, **kullanıcıyı davet et** ' i seçin ve ardından Konuk kullanıcının bilgilerini ekleyin. 

    > [!NOTE]
    > Grup e-posta adresleri desteklenmez; bir bireyin e-posta adresini girin. Ayrıca, bazı e-posta sağlayıcıları, kullanıcıların gelen kutusu filtrelemesi gibi şeylere yardım etmek için e-posta adreslerine bir artı simgesi (+) ve ek metin eklemesini sağlar. Ancak, Azure AD Şu anda e-posta adreslerinde artı sembolleri desteklememektedir. Teslim sorunlarından kaçınmak için, artı simgesini ve @ simgesine kadar izleyen karakterleri atlayın.

   - **Ada.** Konuk kullanıcının adı ve soyadı.
   - **E-posta adresi (gerekli)**. Konuk kullanıcının e-posta adresi.
   - **Kişisel ileti (isteğe bağlı)** Konuk kullanıcıya kişisel bir hoş geldiniz iletisi ekleyin.
   - **Gruplar**: Konuk kullanıcıyı bir veya daha fazla var olan gruba ekleyebilir veya daha sonra yapabilirsiniz.
   - **Dizin rolü**: Kullanıcı IÇIN Azure AD Yönetim izinlerine ihtiyacınız varsa, bunları BIR Azure AD rolüne ekleyebilirsiniz. 

7. Konuk kullanıcıya otomatik olarak daveti göndermek için **Davet Et**’i seçin. 
 
Daveti göndermenizin ardından kullanıcı hesabı otomatik olarak dizine konuk olarak eklenir.


![, Konuk Kullanıcı türüne sahip B2B kullanıcısını gösterir](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Gruba konuk kullanıcılar ekleme
B2B işbirliği kullanıcılarını bir gruba el ile eklemeniz gerekiyorsa, şu adımları izleyin:

1. [Azure portalda](https://portal.azure.com) Azure AD yöneticisi olarak oturum açın.
2. Herhangi bir sayfadan **Azure Active Directory** arayın ve seçin.
3. **Yönet** altında **gruplar**' ı seçin.
4. Bir grup seçin (veya yeni bir grup oluşturmak için **Yeni Grup** ' a tıklayın). Grubun B2B Konuk kullanıcılarını içerdiğini grup açıklamasına eklemek iyi bir fikirdir.
5. **Üyeler**’i seçin. 
6. Aşağıdakilerden birini yapın:
   - Konuk Kullanıcı dizinde zaten mevcutsa, B2B kullanıcısı için arama yapın. Kullanıcıyı seçin ve ardından kullanıcıyı gruba eklemek için **Seç** ' e tıklayın.
   - Konuk Kullanıcı zaten dizinde yoksa, arama kutusuna e-posta adresini yazarak, isteğe bağlı bir kişisel ileti yazıp **Seç**' e tıklayarak onları gruba davet edin. Davet otomatik olarak davet edilen kullanıcıya gider.
     
     ![Konuk üye eklemek için davet Ekle düğmesi](./media/add-users-administrator/GroupInvite.png)
   
Ayrıca, Azure AD B2B işbirliğiyle dinamik grupları kullanabilirsiniz. Daha fazla bilgi için bkz. [Dinamik Gruplar ve B2B işbirliği Azure Active Directory](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Uygulamaya konuk kullanıcılar ekleme

Bir uygulamaya B2B işbirliği kullanıcıları eklemek için aşağıdaki adımları izleyin:

1. [Azure portalda](https://portal.azure.com) Azure AD yöneticisi olarak oturum açın.
2. Herhangi bir sayfadan **Azure Active Directory** arayın ve seçin.
3. **Yönet** altında **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
4. Konuk kullanıcıları eklemek istediğiniz uygulamayı seçin.
5. Uygulamanın panosunda, **Kullanıcılar ve gruplar** bölmesini açmak Için **Toplam Kullanıcı** ' yı seçin.

    ![Açık kullanıcılar ve gruplar eklemek için Toplam Kullanıcı düğmesi](./media/add-users-administrator/AppUsersAndGroups.png)

6. **Kullanıcı ekle**'yi seçin.
7. **Atama Ekle** altında **Kullanıcı ve gruplar**' ı seçin.
8. Aşağıdakilerden birini yapın:
   - Konuk Kullanıcı dizinde zaten mevcutsa, B2B kullanıcısı için arama yapın. Kullanıcıyı seçin, **Seç**' e tıklayın ve ardından kullanıcıyı uygulamaya eklemek için **ata** ' ya tıklayın.
   - Konuk Kullanıcı zaten dizinde yoksa, **üye Seç veya dış kullanıcı davet** altında kullanıcının e-posta adresini yazın. İleti kutusuna isteğe bağlı bir kişisel ileti yazın. İleti kutusunda **davet et**' e tıklayın.
           
       ![Kullanıcının e-posta adresini, kişiselleştirilmiş iletiyi nereye ekleneceğini vurgulayan ve ayrıca davet düğmesini vurgulayan ekran görüntüsü.](./media/add-users-administrator/AppInviteUsers.png)
   
      **Seç**' e tıklayın ve kullanıcıyı uygulamaya eklemek için **ata** ' ya tıklayın. Davet otomatik olarak davet edilen kullanıcıya gider.

9. Konuk Kullanıcı, **varsayılan erişim** atanmış rolü olan uygulamanın **Kullanıcılar ve gruplar** listesinde görünür. Rolü değiştirmek istiyorsanız, aşağıdakileri yapın:
   - Konuk kullanıcıyı seçin ve ardından **Düzenle**' yi seçin. 
   - **Atamayı Düzenle**' nin altında **Rol Seç**' e tıklayın ve Seçili kullanıcıya atamak istediğiniz rolü seçin.
   - **Seç**’e tıklayın.
   - **Ata**'ya tıklayın.
 
## <a name="resend-invitations-to-guest-users"></a>Konuk kullanıcılara davetleri yeniden gönderme

Bir Konuk Kullanıcı henüz davetini henüz kullanmıyorsanız, davet e-postasını yeniden gönderebilirsiniz.

1. [Azure portalda](https://portal.azure.com) Azure AD yöneticisi olarak oturum açın.
2. Herhangi bir sayfadan **Azure Active Directory** arayın ve seçin.
3. **Yönet** bölümünde **Kullanıcılar**'ı seçin.
5. Kullanıcı hesabını seçin.
6. **Yönet** altında **profil**' i seçin.
7. Kullanıcı daveti henüz kabul etmediyse, **kimlik** bölümünde **kabul edilen davet** **Hayır** olarak ayarlanır. Daveti yeniden göndermek için **(Yönet)** seçeneğini belirleyin. Ardından, **davetleri Yönet** sayfasında, * * daveti yeniden gönder ' in yanındaki **Evet**' i seçin ve **bitti**' yi seçin.

> [!NOTE]
> Kullanıcıyı ilk olarak belirli bir uygulamaya yönlendiren bir daveti yeniden gönderebilirsiniz, yeni davetteki bağlantının kullanıcıyı bunun yerine en üst düzey erişim paneline aldığını anlayın.
> Ayrıca, yalnızca izinleri davet eden kullanıcılar davetleri yeniden gönderebilecektir.

## <a name="next-steps"></a>Sonraki adımlar

- Azure olmayan AD yöneticilerinin B2B Konuk kullanıcıları nasıl ekleyebileceğinizi öğrenmek için bkz. [bilgi ÇALıŞANLARı B2B işbirliği kullanıcılarını nasıl ekler?](add-users-information-worker.md)
- Davet e-postası hakkında daha fazla bilgi için [B2B işbirliği davetiyesi e-postası öğelerine](invitation-email-elements.md)bakın.
