---
title: 'Öğretici: Yakınlaştırma, Azure Active Directory ile otomatik kullanıcı hazırlama için yapılandırma | Microsoft Docs'
description: Azure Active Directory küçültmek için otomatik olarak sağlama ve devre dışı bırakma sağlama kullanıcı hesapları için yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 0a21a35e6d4a938d41e8bc11cebc1be5738d893e
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671000"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Öğretici: Yakınlaştırma için otomatik kullanıcı hazırlama yapılandırın

Bu öğreticinin amacı yakınlaştırma ve Azure AD yapılandırmak için Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları otomatik olarak sağlama ve kullanıcı devre dışı bırakma sağlama ve/veya gruplara yakınlaştırma göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD kullanıcı sağlama hizmeti üzerinde oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmet yapar, nasıl çalıştığını ve sık sorulan sorular önemli ayrıntılar için bkz. [otomatik kullanıcı hazırlama ve sağlamayı kaldırma Azure Active Directory ile SaaS uygulamalarına](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı, şu anda genel Önizleme aşamasındadır. Genel Microsoft Azure için kullanım koşulları Önizleme özellikleri hakkında daha fazla bilgi için bkz. [ek kullanım koşulları, Microsoft Azure önizlemeleri için](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulları zaten sahip olduğunuzu varsayar:

* Azure AD kiracısı
* [Yakınlaştırma Kiracı](https://zoom.us/pricing)
* Yakınlaştırma yönetici izinlerine sahip bir kullanıcı hesabı

## <a name="add-zoom-from-the-gallery"></a>Yakınlaştırma Galeriden Ekle

Azure AD ile otomatik kullanıcı hazırlama için yakınlaştırma yapılandırmadan önce yakınlaştırma Azure AD uygulama galerisinden yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Azure AD uygulama galerisinden yakınlaştırma eklemek için aşağıdaki adımları gerçekleştirin:**

1. İçinde  **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde seçin **Azure Active Directory**.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. Git **kurumsal uygulamalar**ve ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için seçin **yeni uygulama** bölmenin üstünde düğme.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **yakınlaştırma**seçin **yakınlaştırma** sonuçlar paneli ve ardından **Ekle** uygulama eklemek için Ekle düğmesine.

    ![Sonuçlar listesinde Yakınlaştır](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>Yakınlaştırmak için kullanıcı atama

Azure Active Directory kullanan adlı bir kavram *atamaları* hangi kullanıcıların seçilen uygulamalara erişimi alması belirlemek için. Otomatik kullanıcı hazırlama bağlamında, yalnızca kullanıcı ve/veya Azure AD'de bir uygulamaya atanan gruplar eşitlenir.

Yapılandırma ve otomatik kullanıcı hazırlama etkinleştirmeden önce hangi kullanıcılara ve/veya Azure AD'de grupları yakınlaştırma erişmesi karar vermeniz gerekir. Karar sonra buradaki yönergeleri izleyerek yakınlaştırmak için bu kullanıcılara ve/veya grupları atayabilirsiniz:

* [Kurumsal bir uygulamayı kullanıcı veya grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>Yakınlaştırmak için kullanıcı atama önemli ipuçları

* Önerilir tek bir Azure AD kullanıcı sağlama yapılandırmasını otomatik kullanıcı test etmek için yakınlaştırmak için atanır. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Yakınlaştırmak için kullanıcı atama, geçerli herhangi bir uygulamaya özgü rol (varsa) atama iletişim kutusunda seçmelisiniz. Kullanıcılarla **varsayılan erişim** rol sağlamasından dışlanır.

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Yakınlaştırmak için otomatik kullanıcı sağlamayı yapılandırma 

Bu bölümde oluşturmak, güncelleştirmek ve kullanıcılar devre dışı bırakmak için sağlama hizmetini Azure AD'yi yapılandırma adımlarında size kılavuzluk eder veya Azure AD'de kullanıcı ve/veya grup atamaları bu yakınlaştırır gruplandırır.

> [!TIP]
> Uygulamayı da seçebilirsiniz SAML tabanlı çoklu oturum açma için yakınlaştırma etkinleştirmek, yönergeleri izleyerek sağlanan [oturum açma yakınlaştırma tek öğretici](zoom-tutorial.md). Bu iki özellik birbirine tamamlayıcı rağmen otomatik kullanıcı hazırlama bağımsız olarak, çoklu oturum açma yapılandırılabilir.

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Azure AD'de yakınlaştırma için otomatik kullanıcı sağlamayı yapılandırma

1. [Azure Portal](https://portal.azure.com) oturum açın. Seçin **kurumsal uygulamalar**, ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **yakınlaştırma**.

    ![Uygulamaları listeden yakınlaştırma bağlantıyı](common/all-applications.png)

3. Seçin **sağlama** sekmesi.

    ![Sağlama sekmesinde](common/provisioning.png)

4. Ayarlama **hazırlama modu** için **otomatik**.

    ![Sağlama sekmesinde](common/provisioning-automatic.png)

5. Altında **yönetici kimlik bilgileri** bölümünde, girin `https://api.zoom.us/scim` içinde **Kiracı URL'si**. Alınacak **gizli belirteç** yakınlaştırma hesabınızı adım 6'da açıklandığı gibi Kılavuzu izleyin.

6. Oturum açın, [Yönetici Konsolu yakınlaştırma](https://zoom.us/signin). Gidin **Gelişmiş > geliştiriciler için yakınlaştırma** sol gezinti bölmesinde.

    ![Yakınlaştırma tümleştirmeleri](media/zoom-provisioning-tutorial/zoom01.png)

    Gidin **Yönet** sayfanın sağ üst köşesindeki içinde. 

    ![Yakınlaştırma yükleme](media/zoom-provisioning-tutorial/zoom02.png)

    Gezinmek için oluşturduğunuz Azure AD uygulaması. 
    
    ![Yakınlaştırma uygulama](media/zoom-provisioning-tutorial/zoom03.png)

    Seçin **uygulama kimlik** sol gezinti bölmesinde.

    ![Yakınlaştırma uygulama](media/zoom-provisioning-tutorial/zoom04.png)

    Aşağıda gösterilen JWT belirteci değeri alabilir ve bu giriş **gizli belirteç** Azure ad alanı. Süresiz yeni bir belirteç gerekiyorsa, sona erme yeniden yapılandırmanız gerekecektir otomatik zaman yeni bir belirteç oluşturur. 

    ![Yakınlaştırma yükleme](media/zoom-provisioning-tutorial/zoom05.png)

7. 5\. adımda gösterilen alanlar doldurma üzerine tıklayın **Test Bağlantısı** Azure emin olmak için AD yakınlaştırmak için bağlanabilirsiniz. Bağlantı başarısız olursa, yakınlaştırma hesabının yönetici izinlerine sahip olun ve yeniden deneyin.

    ![Belirteç](common/provisioning-testconnection-tenanturltoken.png)

8. İçinde **bildirim e-posta** alanında, bir kişi veya grubun ve sağlama hata bildirimleri almak - onay e-posta adresi girin **birhataoluşursa,bire-postabildirimigönder**.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

9. **Kaydet**’e tıklayın.

10. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory Kullanıcıları yakınlaştırma**.

    ![Yakınlaştırma kullanıcı eşlemeleri](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. Yakınlaştırma için Azure AD'den eşitlenen kullanıcı özniteliklerini gözden **eşleme özniteliği** bölümü. Seçilen öznitelikler **eşleşen** özellikleri yakınlaştırma kullanıcı hesaplarını güncelleştirme işlemleri eşleştirmek için kullanılır. Seçin **Kaydet** düğmesine değişiklikleri uygulayın.
    
     ![Yakınlaştırma kullanıcı eşlemeleri](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. Kapsam belirleme filtrelerini yapılandırmak için aşağıdaki yönergelere bakın [Scoping filtre öğretici](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Azure AD sağlama hizmeti için yakınlaştırma etkinleştirmek için değiştirin **sağlama durumu** için **üzerinde** içinde **ayarları** bölümü.
    
    ![Açıkken sağlama durumu](common/provisioning-toggle-on.png)

14. Kullanıcılara ve/veya istediğiniz grupları yakınlaştırma sağlamak için istenen değerleri seçerek tanımlamak **kapsam** içinde **ayarları** bölümü.

    ![Kapsam sağlama](common/provisioning-scope.png)

15. Sağlama için hazır olduğunuzda, tıklayın **Kaydet**.

    ![Sağlama yapılandırmasını kaydetme](common/provisioning-configuration-save.png)

Bu işlem, tüm kullanıcıların ilk eşitleme başlar ve/veya tanımlı gruplar **kapsam** içinde **ayarları** bölümü. İlk eşitleme yaklaşık 40 dakikada Azure AD sağlama hizmeti çalışıyor sürece oluşan sonraki eşitlemeler uzun sürer. Kullanabileceğiniz **eşitleme ayrıntıları** bölüm ilerlemeyi izlemek ve sağlama hizmeti yakınlaştırma Azure AD tarafından gerçekleştirilen tüm eylemler açıklayan Etkinlik Raporu sağlama için bağlantıları izleyin.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Yakınlaştırma grupları için sağlama desteklemez.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kullanıcı hesabı, kurumsal uygulamalar için sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri gözden geçirin ve sağlama etkinliği raporları alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)
