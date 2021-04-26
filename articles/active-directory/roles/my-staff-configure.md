---
title: Kullanıcı yönetimini atamak için personelimi kullanma-Azure AD | Microsoft Docs
description: Personel ve yönetim birimlerimi kullanarak Kullanıcı yönetimi temsilcisi seçme
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 03/11/2021
ms.author: rolyon
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 1a380c8a3d766c3c11d8cba1148383d924f65a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225005"
---
# <a name="manage-your-users-with-my-staff"></a>Personelim ile kullanıcılarınızı yönetme

Personeliniz, personelin Azure AD hesaplarına erişebildiğinden emin olmak için mağaza yöneticisi veya ekip lideri gibi bir yetkili için izin atamanıza olanak sağlar. Kuruluşlar, merkezi bir yardım masasına güvenmek yerine, parolaları sıfırlama veya telefon numaralarını yerel bir ekip Yöneticisi olarak değiştirme gibi genel görevleri temsilcilibilirler. Personeli sayesinde, hesaplarına erişemesiz bir Kullanıcı, yardım masası veya BT personeli gerekmeden yalnızca birkaç tıklamayla erişim elde edebilir.

Kurumumu kuruluşunuz için yapılandırmadan önce, nasıl çalıştığını ve kullanıcılarınızı nasıl etkilediğini anladığınızdan emin olmak için bu belgenin yanı sıra [Kullanıcı belgelerini](../user-help/my-staff-team-manager.md) incelemenizi öneririz. Kullanıcıları yeni deneyimle eğitmeniz ve hazırlamak için Kullanıcı belgelerinden yararlanabilir ve başarılı bir dağıtım sağlanmasına yardımcı olabilirsiniz.

## <a name="how-my-staff-works"></a>Personeliniz nasıl işe yarar?

Personeliniz, bir rol atamasının yönetim denetiminin kapsamını kısıtlamak için kullanılabilecek bir kaynak kapsayıcısı olan yönetim birimlerini temel alır. Daha fazla bilgi için bkz. [Azure Active Directory yönetim birimleri yönetimi](administrative-units.md). Kullanıcımda, yönetici birimleri bir mağaza veya departmandaki bir kullanıcı grubunu içerecek şekilde kullanılabilir. Bir Team Manager daha sonra bir veya daha fazla birim kapsamındaki bir Yönetim rolüne atanabilir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.

  * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Aboneliğinizle ilişkili bir Azure Active Directory kiracısı.

  * Gerekirse, [bir Azure Active Directory kiracı oluşturun](../fundamentals/sign-up-organization.md) veya [bir Azure aboneliğini hesabınızla ilişkilendirin](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
* SMS tabanlı kimlik doğrulamasını etkinleştirmek için Azure AD kiracınızda *genel yönetici* ayrıcalıklarına sahip olmanız gerekir.
* Kısa mesaj kimlik doğrulama yöntemi ilkesinde etkin olan her kullanıcının, kullanmasa bile lisanslanması gerekir. Her etkin kullanıcı aşağıdaki Azure AD veya Microsoft 365 lisanslarından birine sahip olmalıdır:

  * [Azure AD Premium P1 veya P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [Microsoft 365 (M365) F1 veya F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [Enterprise Mobility + Security (EMS) E3 veya E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) ya da [Microsoft 365 (M365) E3 veya E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>Personelimi etkinleştirme

Yönetim birimlerini yapılandırdıktan sonra, bu kapsamı personelime erişen kullanıcılarınıza uygulayabilirsiniz. Yalnızca bir yönetim rolü atanmış kullanıcılar personelime erişebilir. Personelimi etkinleştirmek için aşağıdaki adımları izleyin:

1. Azure portal Kullanıcı Yöneticisi olarak oturum açın.
2. Kullanıcı ayarlarına **Azure Active Directory**, Kullanıcı  >    >  **özelliği önizlemeleri**  >  **Kullanıcı özelliği Önizleme ayarlarını yönet**' e gidin.
3. **Yöneticiler temsilcime erişebilme** altında, tüm kullanıcılar, seçili kullanıcılar veya Kullanıcı erişimi olmadan ' yi etkinleştirmeyi seçebilirsiniz.

> [!Note]
> Yalnızca bir yönetici rolü atanmış kullanıcılar personelime erişebilir. Yönetici rolü atanmamış bir kullanıcı için personelimi etkinleştirirseniz, personelime erişemezler.

## <a name="conditional-access"></a>Koşullu erişim

Azure AD koşullu erişim ilkesini kullanarak personel portalından koruma sağlayabilirsiniz. Personelinize erişmeden önce Multi-Factor Authentication gerektirme gibi görevler için kullanın.

[Azure AD koşullu erişim ilkelerini](../conditional-access/index.yml)kullanarak personelimi korumanızı kesinlikle öneririz. Çalışanlarıma koşullu erişim ilkesi uygulamak için, koşullu erişim tarafından kullanılmak üzere kiracınızda hizmet sorumlusunu otomatik olarak sağlamak üzere birkaç dakika sonra personel sitesini bir kez ziyaret etmeniz gerekir.

Personel bulut uygulaması için geçerli bir koşullu erişim ilkesi oluşturduğunuzda hizmet sorumlusu ' nı görürsünüz.

![Personel uygulaması için koşullu erişim ilkesi oluşturma](./media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Personelimi kullanma

Bir Kullanıcı personelime gittiğinde, yönetim izinlerinin bulunduğu [yönetim birimlerinin](administrative-units.md) adları gösterilir. [Personelin kullanıcı belgelerinde](../user-help/my-staff-team-manager.md), yönetim birimlerine başvurmak için "konum" terimini kullanırız. Yöneticinin izinlerinin yönetim birimi kapsamı yoksa, bu izinler kuruluş genelinde uygulanır. Personeliniz etkinleştirildikten sonra, etkin olan ve bir yönetim rolü atanmış olan kullanıcılar buna aracılığıyla erişebilir [https://mystaff.microsoft.com](https://mystaff.microsoft.com) . Bu birimdeki kullanıcıları görüntülemek için bir yönetim birimi seçebilir ve profilini açmak için bir kullanıcı seçebilirsiniz.

## <a name="reset-a-users-password"></a>Kullanıcı parolasını sıfırlama

Şirket içi kullanıcıların parolalarını geri yükleyebilmeniz için önce aşağıdaki önkoşul koşullarını karşılamanız gerekir. Ayrıntılı yönergeler için bkz. [self servis parola sıfırlama](../authentication/tutorial-enable-sspr-writeback.md) öğreticisini etkinleştirme.

* Parola geri yazma izinlerini yapılandırma
* Azure AD Connect parola geri yazmayı etkinleştirme
* Azure AD self servis parola sıfırlama (SSPR) içinde parola geri yazmayı etkinleştirme

Aşağıdaki rollerin bir kullanıcının parolasını sıfırlama izni vardır:

* [Kimlik doğrulama Yöneticisi](permissions-reference.md#authentication-administrator)
* [Ayrıcalıklı kimlik doğrulama Yöneticisi](permissions-reference.md#privileged-authentication-administrator)
* [Genel yönetici](permissions-reference.md#global-administrator)
* [Yardım Masası Yöneticisi](permissions-reference.md#helpdesk-administrator)
* [Kullanıcı yöneticisi](permissions-reference.md#user-administrator)
* [Parola yöneticisi](permissions-reference.md#password-administrator)

**Personelimin** içinden bir kullanıcının profilini açın. **Parolayı Sıfırla**' yı seçin.

* Kullanıcı yalnızca bulutta ise, kullanıcıya verebileceğiniz geçici bir parola görebilirsiniz.
* Kullanıcı şirket içi Active Directory eşitlendiğinde, şirket içi AD ilkelerinizi karşılayan bir parola girebilirsiniz. Daha sonra bu parolayı kullanıcıya verebilirsiniz.

    ![Parola sıfırlama ilerleme göstergesi ve başarı bildirimi](./media/my-staff-configure/reset-password.png)

Kullanıcının bir sonraki oturum açışlarında parolasını değiştirmesi gerekir.

## <a name="manage-a-phone-number"></a>Telefon numarasını yönetme

**Personelimin** içinden bir kullanıcının profilini açın.

* Kullanıcı için telefon numarası eklemek üzere **telefon numarası Ekle** bölümünü seçin
* Telefon numarasını değiştirmek için **telefon numarasını Düzenle** ' yi seçin
* Kullanıcının telefon numarasını kaldırmak için **telefon numarasını Kaldır** ' ı seçin

Ayarlarınıza bağlı olarak, kullanıcı SMS ile oturum açmak için ayarladığınız telefon numarasını kullanabilir, çok faktörlü kimlik doğrulaması gerçekleştirebilir ve self servis parola sıfırlama işlemini gerçekleştirebilir.

Bir kullanıcının telefon numarasını yönetmek için aşağıdaki rollerden birine atanmış olmanız gerekir:

* [Kimlik doğrulama Yöneticisi](permissions-reference.md#authentication-administrator)
* [Ayrıcalıklı kimlik doğrulama Yöneticisi](permissions-reference.md#privileged-authentication-administrator)
* [Genel yönetici](permissions-reference.md#global-administrator)

## <a name="search"></a>Arayın

Kuruluşumdaki arama çubuğunu kullanarak kuruluşunuzda yönetim birimleri ve kullanıcılar için arama yapabilirsiniz. Kuruluşunuzdaki tüm yönetim birimlerinde ve kullanıcılar üzerinde arama yapabilirsiniz, ancak yalnızca yönetici izinlerine sahip olduğunuz bir yönetim birimindeki kullanıcılara değişiklik yapabilirsiniz.

Ayrıca, bir kullanıcı için yönetim birimi içinde arama yapabilirsiniz. Bunu yapmak için, kullanıcı listesinin en üstündeki arama çubuğunu kullanın.

## <a name="audit-logs"></a>Denetim günlükleri

Azure Active Directory portalında personelimde gerçekleştirilen eylemler için Denetim günlüklerini görüntüleyebilirsiniz. Bir denetim günlüğü, personelimde gerçekleştirilen bir eylem tarafından oluşturulduysa, bunu denetim olayında ek ayrıntılar altında görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar

[Personel Kullanıcı belgeleri](../user-help/my-staff-team-manager.md) 
 [Yönetim birimleri belgeleri](administrative-units.md)
