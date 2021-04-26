---
title: 'Öğretici: Pingboard için Kullanıcı hazırlama-Azure AD'
description: Kullanıcı hesaplarını ping panosuna otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: ac36f5d6d1f57fd8453c54bcc8cf19dd964f47f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357904"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Pingboard 'ı yapılandırma

Bu öğreticinin amacı, Kullanıcı hesaplarının Azure Active Directory (Azure AD) kaynağından Pingboard 'a otomatik sağlamayı ve sağlamayı kaldırmayı etkinleştirmek için izlemeniz gereken adımları göstersağlamaktır.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide anlatılan senaryoda aşağıdakilere sahip olduğunuz kabul edilmiştir:

* Bir Azure AD kiracısı
* Pingboard kiracı [Pro hesabı](https://pingboard.com/pricing)
* Ping panosunda yönetici izinlerine sahip bir kullanıcı hesabı

> [!NOTE]
> Azure AD sağlama tümleştirmesi, hesabınız için kullanılabilen [Pingboard API](https://pingboard.docs.apiary.io/#)'sini kullanır.

## <a name="assign-users-to-pingboard"></a>Ping panosuna Kullanıcı atama

Azure AD, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyen "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı hesabı sağlama bağlamında yalnızca Azure AD 'de bir uygulamaya atanan kullanıcılar eşitlenir. 

Sağlama hizmetini yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların Pingboard uygulamanıza erişmesi gerektiğine karar vermelisiniz. Daha sonra buradaki yönergeleri izleyerek, bu kullanıcıları Pingboard uygulamanıza atayabilirsiniz:

[Kurumsal uygulamaya Kullanıcı atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Ping panosuna Kullanıcı atamaya yönelik önemli ipuçları

Sağlama yapılandırmasını test etmek için Pingboard 'e tek bir Azure AD kullanıcısı atamanız önerilir. Daha sonra ek kullanıcılar atanabilir.

## <a name="configure-user-provisioning-to-pingboard"></a>Ping panosuna Kullanıcı sağlamasını yapılandırma 

Bu bölüm, Azure AD 'nizi Pingboard Kullanıcı hesabı sağlama API 'sine bağlama sırasında size rehberlik eder. Ayrıca, Azure AD 'de kullanıcı atamalarını temel alan Pingboard 'de atanan kullanıcı hesaplarını oluşturmak, güncelleştirmek ve devre dışı bırakmak için sağlama hizmetini yapılandırırsınız.

> [!TIP]
> Pingboard için SAML tabanlı çoklu oturum açmayı etkinleştirmek üzere [Azure Portal](https://portal.azure.com)belirtilen yönergeleri izleyin. Çoklu oturum açma özelliği otomatik sağlanmadan bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini tamamlayabilse de.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Azure AD 'de ping panosuna otomatik Kullanıcı hesabı sağlamayı yapılandırmak için

1. [Azure Portal](https://portal.azure.com), **Azure Active Directory**  >  **Enterprise Apps**  >  **tüm uygulamalar** bölümüne gidin.

1. Zaten çoklu oturum açma için Pingboard yapılandırdıysanız arama alanını kullanarak Pingboard örneğinizi arayın. Aksi takdirde uygulama galerisinde, **Ekle** ve **pingboard** ara ' yı seçin. Arama sonuçlarından **Pingboard** ' ı seçin ve uygulama listenize ekleyin.

1. Pingboard örneğinizi seçin ve **sağlama** sekmesini seçin.

1. **Sağlama modunu** **Otomatik** olarak ayarlayın.

    ![Pingboard sağlama](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. **Yönetici kimlik bilgileri** bölümünde aşağıdaki adımları kullanın:

    a. **Kiracı URL 'si**' nde girin `https://your_domain.pingboard.com/scim/v2` ve "your_domain" değerini gerçek etki alanınız ile değiştirin.

    b. Yönetici hesabınızı kullanarak [Pingboard](https://pingboard.com/) 'da oturum açın.

    c. **Eklentiler Azure Active Directory eklentiler**' i seçin  >    >  .

    d. **Yapılandır** sekmesine gidin ve **Azure 'Dan Kullanıcı sağlamayı etkinleştir**' i seçin.

    e. Belirteci **OAuth taşıyıcı belirtecine** kopyalayın ve **gizli belirtece** girin.

1. Azure portal Azure AD ' ı test etmek için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, Pingboard hesabınızın yönetici izinlerine sahip olup olmadığını test edin ve **bağlantıyı test** etme adımını yeniden deneyin.

1. **Bildirim e-postasında** sağlama hatası bildirimlerini almak istediğiniz kişinin veya grubun e-posta adresini girin. Altındaki onay kutusunu seçin.

1. **Kaydet**’i seçin.

1. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Pingboard olarak eşitler**' ı seçin.

1. **Öznitelik eşlemeleri** bölümünde, Azure AD 'Den pingboard 'a eşitlenecek Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için pingboard içindeki kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** ' i seçin. Daha fazla bilgi için bkz. [Kullanıcı hazırlama öznitelik eşlemelerini özelleştirme](../app-provisioning/customize-application-attributes.md).

1. Pingboard için Azure AD sağlama hizmetini etkinleştirmek üzere, **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

1. Pingboard 'e atanan kullanıcıların ilk eşitlemesini başlatmak için **Kaydet** ' i seçin.

İlk eşitleme, hizmetin çalıştığı sürece yaklaşık olarak yaklaşık 40 dakikada bir oluşan aşağıdaki eşitlemeden sonra çalıştırılması daha uzun sürer. İlerleme durumunu izlemek ve etkinlik günlüklerinin sağlanması için bağlantıları izlemek üzere **eşitleme ayrıntıları** bölümünü kullanın. Günlükler, Pingboard uygulamanızda sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri anlatmaktadır.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlama hakkında rapor](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
* [Çoklu oturum açmayı yapılandırma](pingboard-tutorial.md)
