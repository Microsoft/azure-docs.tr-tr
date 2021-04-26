---
title: Azure Active Directory B2C yönetin
titleSuffix: Azure Active Directory B2C
description: Azure Active Directory B2C kiracınızı yönetme hakkında bilgi edinin. Azure AD B2C hangi Azure AD özelliklerinin desteklendiğini, kaynakları yönetmek için yönetici rollerini nasıl kullanacağınızı ve Azure AD B2C kiracınıza iş hesapları ve konuk kullanıcıların nasıl ekleneceğini öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c3ea17a4f6dc2fb5134c6ceb1ae37d25e0881365
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715522"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C kiracınızı yönetin

Azure Active Directory B2C (Azure AD B2C) ' de, bir kiracı tüketici kullanıcıları dizininizi temsil eder. Her Azure AD B2C kiracısı farklı ve diğer Azure AD B2C kiracılarından ayrıdır. Bir Azure AD B2C kiracısı, zaten sahip olabilecek bir Azure Active Directory kiracısından farklı. Bu makalede, Azure AD B2C kiracınızı yönetmeyi öğreneceksiniz.

## <a name="supported-azure-ad-features"></a>Desteklenen Azure AD özellikleri

Azure AD B2C Azure AD platformunu kullanır. Aşağıdaki Azure AD özellikleri Azure AD B2C kiracınızda kullanılabilir.

|Özellik  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [Gruplar](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | Gruplar, yönetim ve Kullanıcı hesaplarını yönetmek için kullanılabilir.| Gruplar, yönetim hesaplarını yönetmek için kullanılabilir. [Tüketici hesapları](user-overview.md#consumer-user) grupları desteklemez. |
| [Dış kimlikler konukları davet ediliyor](../active-directory//external-identities/add-users-administrator.md)| Konuk kullanıcıları davet edebilir ve Facebook ve Google hesaplarıyla, Federasyon ve oturum açma gibi dış kimlik özelliklerini yapılandırabilirsiniz. | Uygulamalara erişmek veya kiracılar yönetmek için Azure AD kiracınıza Konuk olarak yalnızca bir Microsoft hesabı veya bir Azure AD kullanıcısı davet edebilirsiniz. [Tüketici hesaplarında](user-overview.md#consumer-user), kullanıcıları yönetmek ve Google veya Facebook gibi dış kimlik sağlayıcılarıyla kaydolmak veya oturum açmak için Kullanıcı akışları ve özel ilkeler Azure AD B2C kullanırsınız. |
| [Roller ve yöneticiler](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| Yönetim ve Kullanıcı hesapları için tam olarak desteklenir. | Roller, [Tüketici hesaplarıyla](user-overview.md#consumer-user)desteklenmez. Tüketici hesaplarının hiçbir Azure kaynağına erişimi yok.|
| [Özel etki alanı adları](../active-directory/roles/permissions-reference.md#) |  Azure AD özel etki alanlarını yalnızca yönetim hesapları için kullanabilirsiniz. | [Tüketici hesapları](user-overview.md#consumer-user) bir Kullanıcı adı, telefon numarası veya herhangi bir e-posta adresiyle oturum açabilir. Yeniden yönlendirme URL 'Lerinde [özel etki alanları](custom-domain.md) kullanabilirsiniz.|
| [Koşullu Erişim](../active-directory/roles/permissions-reference.md#) | Yönetim ve Kullanıcı hesapları için tam olarak desteklenir. | Azure AD koşullu erişim özelliklerinin bir alt kümesi, [Tüketici hesaplarıyla](user-overview.md#consumer-user) Azure AD B2C [özel etki alanını](conditional-access-user-flow.md)nasıl yapılandıracağınızı destekler.|

## <a name="other-azure-resources-in-your-tenant"></a>Kiracınızdaki diğer Azure kaynakları

Azure AD B2C kiracısında sanal makineler, Azure Web uygulamaları veya Azure işlevleri gibi diğer Azure kaynaklarını sağlayamazsınız. Bu kaynakları Azure AD kiracınızda oluşturmanız gerekir.

## <a name="azure-ad-b2c-accounts-overview"></a>Azure AD B2C hesaplara genel bakış

Aşağıdaki hesap türleri Azure AD B2C kiracısında oluşturulabilir:

Azure AD B2C kiracısında, [Azure Active Directory B2C makalesinde Kullanıcı hesaplarına genel bakış](user-overview.md) bölümünde açıklandığı gibi oluşturulabilecek birkaç hesap türü vardır.

- **İş hesabı** -bir iş hesabı Kiracıdaki kaynaklara erişebilir ve bir yönetici rolüyle, kiracılar yönetebilir.
- **Konuk hesabı** -bir Konuk hesabı yalnızca uygulamalara erişmek veya kiracılar yönetmek için kullanılabilecek bir Microsoft hesabı veya Azure Active Directory Kullanıcı olabilir.
- **Tüketici hesabı** -bir tüketici hesabı, Azure AD B2C kaydettiğiniz uygulamaların bir kullanıcısı tarafından kullanılır.

Bu hesap türleri hakkında daha fazla bilgi için bkz. [Azure Active Directory B2C Kullanıcı hesaplarına genel bakış](user-overview.md). Azure AD B2C kiracınızı yönetmek için atanacak tüm kullanıcıların, Azure ile ilgili hizmetlere erişebilmeleri için bir Azure AD Kullanıcı hesabı olması gerekir. Azure AD B2C kiracınızda bir hesap (iş hesabı) [oluşturarak](#add-an-administrator-work-account) veya bunları Konuk kullanıcı olarak Azure AD B2C kiracınıza [davet](#invite-an-administrator-guest-account) ederek bu tür bir kullanıcı ekleyebilirsiniz.

## <a name="use-roles-to-control-resource-access"></a>Rolleri kullanarak kaynak erişimini denetleme

Erişim denetimi stratejinizi planlarken, kullanıcılara kaynaklara erişmek için gereken en az ayrıcalıklı rolü atamak en iyisidir. Aşağıdaki tabloda, Azure AD B2C kiracınızdaki birincil kaynaklar ve bunları yöneten kullanıcılar için en uygun yönetim rolleri açıklanmaktadır.

|Kaynak  |Açıklama  |Rol  |
|---------|---------|---------|
|[Uygulama kayıtları](tutorial-register-applications.md) | Azure AD B2C içindeki Web, mobil ve yerel uygulama kayıtlarınızın tüm yönlerini oluşturun ve yönetin.|[Uygulama Yöneticisi](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Kimlik sağlayıcıları](add-identity-provider.md)| [Yerel kimlik sağlayıcısını](identity-provider-local.md) ve dış sosyal veya kurumsal kimlik sağlayıcılarını yapılandırın. | [Dış kimlik sağlayıcısı Yöneticisi](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[API bağlayıcıları](add-api-connector.md)| Kullanıcı deneyimini özelleştirmek ve dış sistemlerle bütünleştirmek için Kullanıcı akışlarınızı Web API 'Leriyle tümleştirin.|[Dış KIMLIK Kullanıcı akışı öznitelik Yöneticisi](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Şirket markası](customize-ui.md#configure-company-branding)| Kullanıcı akış sayfalarınızı özelleştirin.| [Genel Yönetici](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Kullanıcı öznitelikleri](user-flow-custom-attributes.md)| Tüm Kullanıcı akışları için kullanılabilen özel öznitelikleri ekleyin veya silin.| [Dış KIMLIK Kullanıcı akışı öznitelik Yöneticisi](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|Kullanıcıları yönetme| Bu makalede açıklandığı gibi [Tüketici hesaplarını](manage-users-portal.md) ve yönetim hesaplarını yönetin.| [Kullanıcı Yöneticisi](../active-directory/roles/permissions-reference.md#user-administrator)|
|Roller ve yöneticiler| Rol atamalarını Azure AD B2C dizininde yönetin. Azure AD B2C rollere atanabilecek grupları oluşturun ve yönetin. |[Genel yönetici](../active-directory/roles/permissions-reference.md#global-administrator), [ayrıcalıklı rol yöneticisi](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[Kullanıcı akışları](user-flow-overview.md)|Kaydolma, oturum açma ve profil düzenlemesi gibi ortak kimlik görevlerinin hızlı yapılandırılması ve etkinleştirilmesi için.| [Dış KIMLIK Kullanıcı akışı öznitelik Yöneticisi](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Özel ilkeler](user-flow-overview.md)| Azure AD B2C tüm özel ilkeleri oluşturun, okuyun, güncelleştirin ve silin.| [B2C ıEF Ilke Yöneticisi](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[İlke anahtarları](policy-keys-overview.md)|Özel ilkelerde kullanılan belirteçleri, istemci gizli dizilerini, sertifikaları ve parolaları imzalamak ve doğrulamak için şifreleme anahtarları ekleyin ve yönetin.|[B2C ıEF anahtar kümesi Yöneticisi](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|


## <a name="add-an-administrator-work-account"></a>Yönetici ekleme (iş hesabı)

Yeni bir yönetim hesabı oluşturmak için aşağıdaki adımları izleyin:

1. Genel yönetici veya ayrıcalıklı rol yöneticisi izinleriyle [Azure Portal](https://portal.azure.com/) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. **Azure hizmetleri** altında **Azure AD B2C**' yi seçin. İsterseniz arama kutusunu kullanarak **Azure AD B2C** bulun ve seçin.
1. **Yönet** bölümünde **Kullanıcılar**'ı seçin.
1. **Yeni Kullanıcı**' yı seçin.
1. **Kullanıcı** sayfasında, bu kullanıcı için bilgi girin:

   - **Ad**. Gereklidir. Yeni kullanıcının adı ve soyadı. Örneğin, *Mary Parker*.
   - **Kullanıcı adı**. Gereklidir. Yeni kullanıcının Kullanıcı adı. Örneğin, `mary@contoso.com`.
     Kullanıcı adının etki alanı bölümü, ilk varsayılan etki alanı adını ( *\<yourdomainname> . onmicrosoft.com*) kullanmalıdır.
   - **Gruplar**. İsteğe bağlı olarak, kullanıcıyı bir veya daha fazla var olan gruba ekleyebilirsiniz. Kullanıcıyı daha sonra gruplara da ekleyebilirsiniz. 
   - **Dizin rolü**: Kullanıcı IÇIN Azure AD Yönetim izinlerine ihtiyacınız varsa, bunları BIR Azure AD rolüne ekleyebilirsiniz. Kullanıcıyı bir genel yönetici veya Azure AD 'de sınırlı bir veya daha fazla yönetici rolünden atayabilirsiniz. Rol atama hakkında daha fazla bilgi için bkz. [kaynak erişimini denetlemek için rolleri kullanma](#use-roles-to-control-resource-access).
   - **İş bilgisi**: Kullanıcı hakkında buraya daha fazla bilgi ekleyebilir ya da daha sonra bunu yapabilirsiniz. 

1. **Parola** kutusunda belirtilen otomatik oluşturulan parolayı kopyalayın. İlk kez oturum açmak için kullanıcıya bu parolayı vermeniz gerekir.
1. **Oluştur**’u seçin.

Kullanıcı oluşturulup Azure AD B2C kiracısına eklenir. Azure AD B2C kiracınızda genel yönetici rolüne atanmış en az bir iş hesabı olması tercih edilir. Bu hesap, bir kesme camı hesabı olarak düşünülebilir.

## <a name="invite-an-administrator-guest-account"></a>Yönetici davet etme (konuk hesabı)

Ayrıca kiracınızı yönetmek için yeni bir Konuk Kullanıcı davet edebilirsiniz. Bu kimliğin yaşam döngüsü dışarıdan yönetilebilmesi için, kuruluşunuzda Azure AD de olduğunda, Konuk hesabı tercih edilen seçenektir. 

Bir kullanıcıyı davet etmek için şu adımları izleyin:

1. Genel yönetici veya ayrıcalıklı rol yöneticisi izinleriyle [Azure Portal](https://portal.azure.com/) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. **Azure hizmetleri** altında **Azure AD B2C**' yi seçin. İsterseniz arama kutusunu kullanarak **Azure AD B2C** bulun ve seçin.
1. **Yönet** bölümünde **Kullanıcılar**'ı seçin.
1. **Yeni Konuk hesabı**' nı seçin.
1. **Kullanıcı** sayfasında, bu kullanıcı için bilgi girin:

   - **Ad**. Gereklidir. Yeni kullanıcının adı ve soyadı. Örneğin, *Mary Parker*.
   - **E-posta adresi**. Gereklidir. Davet etmek istediğiniz kullanıcının e-posta adresi. Örneğin, `mary@contoso.com`.   
   - **Kişisel ileti**: davet e-postasına dahil edilecek bir kişisel ileti eklersiniz.
   - **Gruplar**. İsteğe bağlı olarak, kullanıcıyı bir veya daha fazla var olan gruba ekleyebilirsiniz. Kullanıcıyı daha sonra gruplara da ekleyebilirsiniz.
   - **Dizin rolü**: Kullanıcı IÇIN Azure AD Yönetim izinlerine ihtiyacınız varsa, bunları BIR Azure AD rolüne ekleyebilirsiniz. Kullanıcıyı bir genel yönetici veya Azure AD 'de sınırlı bir veya daha fazla yönetici rolünden atayabilirsiniz. Rol atama hakkında daha fazla bilgi için bkz. [kaynak erişimini denetlemek için rolleri kullanma](#use-roles-to-control-resource-access).
   - **İş bilgisi**: Kullanıcı hakkında buraya daha fazla bilgi ekleyebilir ya da daha sonra bunu yapabilirsiniz.

1. **Oluştur**’u seçin.

Kullanıcıya bir davet e-postası gönderilir. Kullanıcının oturum açabililmesi için daveti kabul etmesi gerekir.

### <a name="resend-the-invitation-email"></a>Davet e-postasını yeniden gönderin

Konuk davet e-postasını almadıysa veya davetin geçerliliği dolmuşsa, daveti yeniden gönderebilirsiniz. Davet e-postasına alternatif olarak, bir konuğa daveti kabul etmek için doğrudan bağlantı verebilirsiniz. Daveti yeniden göndermek ve doğrudan bağlantıyı almak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. **Azure hizmetleri** altında **Azure AD B2C**' yi seçin. İsterseniz arama kutusunu kullanarak **Azure AD B2C** bulun ve seçin.
1. **Yönet** bölümünde **Kullanıcılar**'ı seçin.
1. Daveti yeniden göndermek istediğiniz kullanıcıyı bulun ve seçin.
1. **Kullanıcı | Profil** sayfası, **kimlik** altında **(Yönet)** seçeneğini belirleyin.
    
    ![Ekran görüntüsü, Konuk hesabı davetiyesi e-postasına nasıl yeniden gönderileceğini gösterir.](./media/tenant-management/guest-account-resend-invite.png)

1. **Daveti yeniden göndermek** Için **Evet 'i** seçin. **Daveti yeniden göndermek istediğinizden emin misiniz?** görüntülenirse, **Evet**' i seçin.
1. Azure AD B2C daveti gönderir. Ayrıca, davet URL 'sini kopyalayabilir ve doğrudan konuğa sağlayabilirsiniz.
    
    ![Ekran görüntüsünde davetiye URL 'sini alma gösterilmektedir.](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>Rol ataması ekleyin

Bir [Kullanıcı oluştururken](#add-an-administrator-work-account) veya [Konuk Kullanıcı davet](#invite-an-administrator-guest-account)ettiğinizde bir rol atayabilirsiniz. Bir rol ekleyebilir, rolü değiştirebilir veya bir kullanıcı için rolü kaldırabilirsiniz:

1. Genel yönetici veya ayrıcalıklı rol yöneticisi izinleriyle [Azure Portal](https://portal.azure.com/) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. **Azure hizmetleri** altında **Azure AD B2C**' yi seçin. İsterseniz arama kutusunu kullanarak **Azure AD B2C** bulun ve seçin.
1. **Yönet** bölümünde **Kullanıcılar**'ı seçin.
1. Rollerini değiştirmek istediğiniz kullanıcıyı seçin. Ardından **atanan roller**' i seçin.
1. Atama **Ekle**' yi seçin, atanacak rolü (örneğin, *Uygulama Yöneticisi*) seçin ve ardından **Ekle**' yi seçin.

## <a name="remove-a-role-assignment"></a>Rol atamasını kaldırma

Bir kullanıcıdan rol atamasını kaldırmanız gerekiyorsa, şu adımları izleyin:

1. **Azure AD B2C** seçin, **Kullanıcılar**' ı seçin ve ardından kullanıcıyı arayıp seçin.
1. **Atanan roller**' i seçin. Kaldırmak istediğiniz rolü seçin (örneğin, *Uygulama Yöneticisi*) ve sonra **Atamayı Kaldır**' ı seçin.

## <a name="review-administrator-account-role-assignments"></a>Yönetici hesabı rol atamalarını gözden geçirme

Bir denetim sürecinin bir parçası olarak, genellikle Azure AD B2C dizinindeki belirli rollere hangi kullanıcıların atandığını inceleyebilirsiniz. Şu anda ayrıcalıklı rollere hangi kullanıcıların atandığını denetlemek için aşağıdaki adımları kullanın.

1. Genel yönetici veya ayrıcalıklı rol yöneticisi izinleriyle [Azure Portal](https://portal.azure.com/) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. **Azure hizmetleri** altında **Azure AD B2C**' yi seçin. İsterseniz arama kutusunu kullanarak **Azure AD B2C** bulun ve seçin.
1. **Yönet** altında **Roller ve yöneticiler**' i seçin.
1. **Genel yönetici** gibi bir rol seçin. **Rol | Atamalar** sayfasında, bu role sahip kullanıcılar listelenir.

## <a name="delete-an-administrator-account"></a>Yönetici hesabını silme

Var olan bir kullanıcıyı silmek için *genel yönetici* rolü atamanız gerekir. Genel Yöneticiler, diğer yöneticiler dahil tüm kullanıcıları silebilir. *Kullanıcı yöneticileri* yönetici olmayan kullanıcıları silebilir.

1. Azure AD B2C dizininizde **Kullanıcılar**' ı seçin ve ardından silmek istediğiniz kullanıcıyı seçin.
1. **Sil**' i ve sonra silme işlemini onaylamak için **Evet** ' i seçin.

Kullanıcı silinir ve artık **Kullanıcılar-tüm kullanıcılar** sayfasında görünmez. Kullanıcı **silinen kullanıcılar** sayfasında sonraki 30 gün boyunca görünebilir ve bu süre içinde geri yüklenebilir. Bir kullanıcıyı geri yükleme hakkında daha fazla bilgi için, bkz. [Azure Active Directory kullanarak son silinen bir kullanıcıyı geri yükleme veya kaldırma](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="protect-administrative-accounts"></a>Yönetim hesaplarını koruma

Daha fazla güvenlik için Multi-Factor Authentication (MFA) ile tüm yönetici hesaplarını korumanız önerilir. MFA, kullanıcıdan, mobil cihazlarındaki veya Microsoft Authenticator uygulamasındaki bir istek için doğrulama kodu gibi daha fazla tanımlama bilgisi girmesini isteyen oturum açma sırasında bir kimlik doğrulama işlemidir.

![Oturum açma ekran görüntüsünde kullanılan kimlik doğrulama yöntemleri](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

Tüm yönetim hesaplarını MFA kullanmaya zorlamak için [Azure AD güvenlik varsayılanlarını](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) etkinleştirebilirsiniz.



## <a name="next-steps"></a>Sonraki adımlar

- [Azure portalında Azure Active Directory B2C kiracısı oluşturma](tutorial-create-tenant.md)

