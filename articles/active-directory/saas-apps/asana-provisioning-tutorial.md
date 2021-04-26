---
title: 'Öğretici: Asana-Azure AD için Kullanıcı hazırlama'
description: Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırma hakkında bilgi edinin.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.reviewer: celested
ms.openlocfilehash: 4abc117ae0e983cf684f0e70a363758f9be196aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359435"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Asana yapılandırma

Bu öğreticinin amacı, Azure AD 'den Asana 'ya Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için, Asana ve Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları gösteriyoruz.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide anlatılan senaryoda aşağıdakilere sahip olduğunuz kabul edilmiştir:

* Bir Azure AD kiracısı
* [Kurumsal](https://www.asana.com/pricing) plana sahip bir Asana kiracı veya daha iyi etkin
* Yönetici izinlerine sahip Asana 'da Kullanıcı hesabı

> [!NOTE]
> Azure AD sağlama tümleştirmesi, Asana [API 'nin kullanabildiği Asana API](https://asana.com/developers/api-reference/users)'yi kullanır.

## <a name="assign-users-to-asana"></a>Kullanıcıları Asana 'ya ata

Azure AD, seçilen uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adlı bir kavram kullanır. Otomatik Kullanıcı hesabı sağlama bağlamında yalnızca Azure AD 'de bir uygulamaya atanan kullanıcılar eşitlenir.

Sağlama hizmetini yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların Asana uygulamanıza erişmesi gerektiğine karar vermelisiniz. Daha sonra buradaki yönergeleri izleyerek bu kullanıcıları Asana uygulamanıza atayabilirsiniz:

[Kurumsal uygulamaya Kullanıcı atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Kullanıcıları Asana 'ya atamaya yönelik önemli ipuçları

Sağlama yapılandırmasını test etmek için, Asana 'ya tek bir Azure AD kullanıcısı atamanız önerilir. Daha sonra ek kullanıcılar atanabilir.

## <a name="configure-user-provisioning-to-asana"></a>Kullanıcı sağlamasını Asana 'ya yapılandırma

Bu bölüm, Azure AD 'nizi Asana Kullanıcı hesabı sağlama API 'sine bağlama sırasında size rehberlik eder. Ayrıca, Azure AD 'de kullanıcı atamalarını temel alan Asana 'da atanan kullanıcı hesaplarını oluşturmak, güncelleştirmek ve devre dışı bırakmak için sağlama hizmetini yapılandırırsınız.

> [!TIP]
> Asana için SAML tabanlı çoklu oturum açmayı etkinleştirmek üzere [Azure Portal](https://portal.azure.com)belirtilen yönergeleri izleyin. Çoklu oturum açma özelliği otomatik sağlanmadan bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini tamamlayabilse de.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Azure AD 'de otomatik Kullanıcı hesabı sağlamayı Asana olarak yapılandırmak için

1. [Azure Portal](https://portal.azure.com), **Azure Active Directory**  >  **Enterprise Apps**  >  **tüm uygulamalar** bölümüne gidin.

1. Çoklu oturum açma için zaten bir Asana yapılandırdıysanız, arama alanını kullanarak Asana örneğinizi arayın. Aksi takdirde, **Ekle** ve uygulama galerisinde **Asana** için ara ' yı seçin. Arama sonuçlarından **Asana** ' yı seçin ve uygulama listenize ekleyin.

1. Asana örneğinizi seçin ve ardından **sağlama** sekmesini seçin.

1. **Sağlama modunu** **Otomatik** olarak ayarlayın.

    ![Asana sağlama](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. **Yönetici kimlik bilgileri** bölümünde, belirteci oluşturmak ve **gizli belirtece** girmek için bu yönergeleri izleyin:

    a. Yönetici hesabınızı kullanarak [Asana](https://app.asana.com) 'da oturum açın.

    b. Üstteki çubuktan profil fotoğrafı ' nu seçin ve geçerli kuruluş adı ayarlarınızı seçin.

    c. **Hizmet hesapları** sekmesine gidin.

    d. **Hizmet hesabı ekle**' yi seçin.

    e. **Ad** ve **hakkında** ve profil fotoğrafınızı gerektiği şekilde güncelleştirin. Belirteci **belirtece** kopyalayın ve **değişiklikleri kaydedin**' de seçin.

1. Azure portal Azure AD 'nin Asana uygulamanıza bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, Asana hesabınızın yönetici izinlerine sahip olduğundan emin olun ve **bağlantıyı test** etme adımını yeniden deneyin.

1. **Bildirim e-postasında** sağlama hatası bildirimlerini almak istediğiniz kişinin veya grubun e-posta adresini girin. Altındaki onay kutusunu seçin.

1. **Kaydet**’i seçin.

1. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Asana olarak eşitler**' ı seçin.

1. **Öznitelik eşlemeleri** bölümünde, Azure AD 'Den Asana 'a eşitlenecek Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Asana 'daki Kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** ' i seçin. Daha fazla bilgi için bkz. [Kullanıcı sağlama özniteliği eşlemelerini özelleştirme](../app-provisioning/customize-application-attributes.md).

1. Azure AD sağlama hizmetini Asana için etkinleştirmek üzere **Ayarlar** bölümünde, **sağlama durumunu** **Açık** olarak değiştirin.

1. **Kaydet**’i seçin.

Artık ilk eşitleme, **Kullanıcılar** bölümünde Asana 'ya atanan tüm kullanıcılar için başlar. İlk eşitlemenin daha sonra, hizmetin çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek ve etkinlik günlüklerinin sağlanması için bağlantıları izlemek üzere **eşitleme ayrıntıları** bölümünü kullanın. Denetim günlükleri, Asana uygulamanızdaki sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri anlatmaktadır.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlama hakkında rapor](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
* [Çoklu oturum açmayı yapılandırma](asana-tutorial.md)
