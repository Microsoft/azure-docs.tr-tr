---
title: "Öğretici: Azure Active Directory ile otomatik kullanıcı hazırlama için iş için Dropbox'ı yapılandırma | Microsoft Docs"
description: Otomatik olarak sağlama ve sağlamasını dropbox'a iş için kullanıcı hesapları için Azure Active Directory yapılandırmayı öğrenin.
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
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: d7a7a76c86100041b544916c7d10e43bf3aaa44d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672902"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Öğretici: İş için Dropbox için otomatik kullanıcı hazırlama yapılandırın

Bu öğreticinin amacı, Dropbox ve iş için Azure Active Directory (Azure AD) Azure AD yapılandırmak otomatik olarak sağlamak ve kullanıcılara ve/veya iş için Dropbox gruplarına sağlamasını gerçekleştirilmesi gereken adımlar göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD kullanıcı sağlama hizmeti üzerinde oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmet yapar, nasıl çalıştığını ve sık sorulan sorular önemli ayrıntılar için bkz. [otomatik kullanıcı hazırlama ve sağlamayı kaldırma Azure Active Directory ile SaaS uygulamalarına](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulları zaten sahip olduğunuzu varsayar:

* Azure AD kiracısı
* [Bir Dropbox iş Kiracı için](https://www.dropbox.com/business/pricing)
* İş için Dropbox yönetici izinlerine sahip bir kullanıcı hesabı.

## <a name="add-dropbox-for-business-from-the-gallery"></a>İş için Dropbox Galeriden Ekle

İçin otomatik kullanıcı hazırlama Azure AD ile iş için Dropbox yapılandırmadan önce iş için Dropbox Azure AD uygulama galerisinden yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Azure AD uygulama galerisinden iş için Dropbox eklemek için aşağıdaki adımları gerçekleştirin:**

1. İçinde  **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde seçin **Azure Active Directory**.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. Git **kurumsal uygulamalar**ve ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için seçin **yeni uygulama** bölmenin üstünde düğme.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **iş için Dropbox**seçin **iş için Dropbox** sonuçlar paneli ve ardından **Ekle** uygulama eklemek için Ekle düğmesine.

    ![Sonuç listesinde iş için Dropbox](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>İş için Dropbox kullanıcıları atama

Azure Active Directory kullanan adlı bir kavram *atamaları* hangi kullanıcıların seçilen uygulamalara erişimi alması belirlemek için. Otomatik kullanıcı hazırlama bağlamında, yalnızca kullanıcı ve/veya Azure AD'de bir uygulamaya atanan gruplar eşitlenir.

Yapılandırma ve otomatik kullanıcı hazırlama etkinleştirmeden önce hangi kullanıcılara ve/veya Azure AD'de grupları, iş için Dropbox erişmesi gereken karar vermeniz gerekir. Karar sonra bu kullanıcılara ve/veya grupları dropbox'a iş için buradaki yönergeleri izleyerek atayabilirsiniz:

* [Kurumsal bir uygulamayı kullanıcı veya grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>İş için Dropbox kullanıcıları atamak için önemli ipuçları

* Önerilir tek bir Azure AD kullanıcı sağlama yapılandırmasını otomatik kullanıcı test etmek iş için Dropbox atanır. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* İş için Dropbox için kullanıcı atama, atama iletişim kutusunda (varsa) geçerli bir uygulamaya özgü rolü seçmeniz gerekir. Kullanıcılarla **varsayılan erişim** rol sağlamasından dışlanır.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>İş için Dropbox için otomatik kullanıcı sağlamayı yapılandırma 

Bu bölümde oluşturmak, güncelleştirmek ve kullanıcılar devre dışı bırakmak için sağlama hizmetini Azure AD'yi yapılandırma adımlarında size kılavuzluk eder ve/veya Azure AD'de kullanıcı ve/veya grup atamaları bu iş için dropbox'ta gruplandırır.

> [!TIP]
> Uygulamayı da seçebilirsiniz SAML tabanlı çoklu oturum açma için iş için Dropbox etkinleştirmek, yönergeleri izleyerek sağlanan [Dropbox oturum açma iş tek öğretici](dropboxforbusiness-tutorial.md). Bu iki özellik birbirine tamamlayıcı rağmen otomatik kullanıcı hazırlama bağımsız olarak, çoklu oturum açma yapılandırılabilir.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Azure AD'de iş için Dropbox için otomatik kullanıcı hazırlama yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. Seçin **kurumsal uygulamalar**, ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **iş için Dropbox**.

    ![Uygulamalar listesini iş bağlantısı için Dropbox](common/all-applications.png)

3. Seçin **sağlama** sekmesi.

    ![Sağlama sekmesinde](common/provisioning.png)

4. Ayarlama **hazırlama modu** için **otomatik**.

    ![Sağlama sekmesinde](common/provisioning-automatic.png)

5. Altında **yönetici kimlik bilgileri** bölümünde **Authorize**. Bir Dropbox iş oturum açma iletişim kutusu için yeni bir tarayıcı penceresinde açar.

    ![Sağlama ](common/provisioning-oauth.png)

6. Üzerinde **oturum açma için Azure AD'ye bağlamak iş dropbox'a** iletişim kutusunda, iş Kiracı için Dropbox'için oturum açın ve kimliğinizi doğrulayın.

    ![Dropbox için iş oturum açma](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. 5 ve 6 adımları tamamladıktan sonra tıklayın **Test Bağlantısı** Azure emin olmak için AD iş için Dropbox bağlanabilirsiniz. Bağlantı başarısız olursa, Dropbox'iş hesabı için yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Belirteç](common/provisioning-testconnection-oauth.png)

8. İçinde **bildirim e-posta** alanında, bir kişi veya grubun ve sağlama hata bildirimleri almak - onay e-posta adresi girin **birhataoluşursa,bire-postabildirimigönder**.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

9. **Kaydet**’e tıklayın.

10. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory Kullanıcıları dropbox'a**.

    ![Dropbox kullanıcı eşlemeleri](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Dropbox için Azure AD'den eşitlenen kullanıcı özniteliklerini gözden **eşleme özniteliği** bölümü. Seçilen öznitelikler **eşleşen** özellikleri güncelleştirme işlemleri için Dropbox kullanıcı hesaplarını eşleştirmek için kullanılır. Seçin **Kaydet** düğmesine değişiklikleri uygulayın.

    ![Dropbox kullanıcı öznitelikleri](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory grupları dropbox'a**.

    ![Dropbox Grup Eşlemeleri](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Dropbox için Azure AD'den eşitlenen grup öznitelikleri gözden **eşleme özniteliği** bölümü. Seçilen öznitelikler **eşleşen** özellikleri güncelleştirme işlemleri için Dropbox gruplarında eşleştirmek için kullanılır. Seçin **Kaydet** düğmesine değişiklikleri uygulayın.

    ![Dropbox grup öznitelikleri](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Kapsam belirleme filtrelerini yapılandırmak için aşağıdaki yönergelere bakın [Scoping filtre öğretici](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Azure AD sağlama hizmeti için Dropbox'ı etkinleştirmek için değiştirin **sağlama durumu** için **üzerinde** içinde **ayarları** bölümü.

    ![Açıkken sağlama durumu](common/provisioning-toggle-on.png)

16. Kullanıcılara ve/veya istediğiniz grupları dropbox'a sağlamak için istenen değerleri seçerek tanımlamak **kapsam** içinde **ayarları** bölümü.

    ![Kapsam sağlama](common/provisioning-scope.png)

17. Sağlama için hazır olduğunuzda, tıklayın **Kaydet**.

    ![Sağlama yapılandırmasını kaydetme](common/provisioning-configuration-save.png)

Bu işlem, tüm kullanıcıların ilk eşitleme başlar ve/veya tanımlı gruplar **kapsam** içinde **ayarları** bölümü. İlk eşitleme yaklaşık 40 dakikada Azure AD sağlama hizmeti çalışıyor sürece oluşan sonraki eşitlemeler uzun sürer. Kullanabileceğiniz **eşitleme ayrıntıları** bölüm ilerlemeyi izlemek ve sağlama hizmeti dropbox'ta Azure AD tarafından gerçekleştirilen tüm eylemler açıklayan Etkinlik Raporu sağlama için bağlantıları izleyin.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları
 
* Dropbox, davet edilen kullanıcılar askıya desteklemez. Bu kullanıcı, davet edilen kullanıcıları askıya alınırsa, silinecek.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kullanıcı hesabı, kurumsal uygulamalar için sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri gözden geçirin ve sağlama etkinliği raporları alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)

