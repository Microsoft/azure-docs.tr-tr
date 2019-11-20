---
title: 'Öğretici: Azure Active Directory ile otomatik kullanıcı hazırlama için Keeper parola Yöneticisi & dijital kasa yapılandırma | Microsoft Docs'
description: Otomatik olarak sağlama ve sağlamasını Keeper parola Yöneticisi & dijital kasa için kullanıcı hesapları için Azure Active Directory yapılandırmayı öğrenin.
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 74bfe37323a17bde19e4a9bf4ec28c9c3910b37f
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666243"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Öğretici: Otomatik kullanıcı hazırlama için Keeper parola Yöneticisi & dijital kasa yapılandırma

Bu öğreticinin amacı Keeper parola Yöneticisi & dijital kasası ve Azure Active Directory (Azure AD) Azure AD yapılandırmak için otomatik olarak sağlamak ve kullanıcılara ve/veya gruplara Keeper parola sağlamasını için gerçekleştirilmesi gereken adımlar göstermektir. & Dijital kasası Yöneticisi.

> [!NOTE]
> Bu öğreticide, Azure AD kullanıcı sağlama hizmeti üzerinde oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmet yapar, nasıl çalıştığını ve sık sorulan sorular önemli ayrıntılar için bkz. [otomatik kullanıcı hazırlama ve sağlamayı kaldırma Azure Active Directory ile SaaS uygulamalarına](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı, şu anda genel Önizleme aşamasındadır. Genel Microsoft Azure için kullanım koşulları Önizleme özellikleri hakkında daha fazla bilgi için bkz. [ek kullanım koşulları, Microsoft Azure önizlemeleri için](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulları zaten sahip olduğunuzu varsayar:

* Azure AD kiracısı
* [Parola Yöneticisi Keeper & dijital kasası Kiracı](https://keepersecurity.com/pricing.html?t=e)
* Yönetici izinlerine sahip bir kullanıcı hesabı Keeper parola Yöneticisi ve dijital kasası.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Parola Yöneticisi Keeper & dijital kasa Galeriden Ekle

Keeper parola Yöneticisi & dijital kasası Azure AD ile otomatik kullanıcı hazırlama için yapılandırmadan önce Keeper parola Yöneticisi & dijital kasası Azure AD uygulama galerisinden yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Azure AD uygulama galerisinden Keeper parola Yöneticisi & dijital kasa eklemek için aşağıdaki adımları gerçekleştirin:**

1. İçinde  **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde seçin **Azure Active Directory**.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. Git **kurumsal uygulamalar**ve ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için seçin **yeni uygulama** bölmenin üstünde düğme.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Keeper parola Yöneticisi & dijital kasası**seçin **Keeper parola Yöneticisi & dijital kasası** sonuçlar paneli ve ardından **Ekle**uygulama eklemek için Ekle düğmesine.

    ![Parola Yöneticisi keeper & sonuçları listesinde dijital kasası](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Kullanıcılar Keeper parola Yöneticisi ve dijital kasa atanıyor

Azure Active Directory kullanan adlı bir kavram *atamaları* hangi kullanıcıların seçilen uygulamalara erişimi alması belirlemek için. Otomatik kullanıcı hazırlama bağlamında, yalnızca kullanıcı ve/veya Azure AD'de bir uygulamaya atanan gruplar eşitlenir.

Yapılandırma ve otomatik kullanıcı hazırlama etkinleştirmeden önce hangi kullanıcılara ve/veya Azure AD'de grupları Keeper parola Yöneticisi & dijital kasasına erişmesi gereken karar vermeniz gerekir. Karar sonra bu kullanıcılara ve/veya grupları Keeper parola Yöneticisi & dijital kasa için buradaki yönergeleri izleyerek atayabilirsiniz:

* [Kurumsal bir uygulamayı kullanıcı veya grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Kullanıcılar Keeper parola Yöneticisi ve dijital kasa atanıyor önemli ipuçları

* Önerilir tek bir Azure AD kullanıcı atandığı Keeper parola Yöneticisi için dijital kasa & sağlama yapılandırmasını otomatik kullanıcı test etmek için. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcı için parola Yöneticisi Keeper & dijital kasa atarken, (varsa) geçerli bir uygulamaya özgü rol ataması iletişim kutusunda seçmeniz gerekir. Kullanıcılarla **varsayılan erişim** rol sağlamasından dışlanır.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Keeper parola Yöneticisi için dijital kasa & Otomatik kullanıcı sağlamayı yapılandırma 

Bu bölümde, oluşturmak, güncelleştirmek ve kullanıcılara ve/veya grupları Keeper parola Yöneticisi'nde devre dışı bırakmak için Azure AD sağlama hizmeti yapılandırmak için kılavuzluk & dijital kasası Azure AD'de kullanıcı ve/veya grup atamalarını bağlı.

> [!TIP]
> Uygulamayı da seçebilirsiniz SAML tabanlı çoklu oturum açma için parola Yöneticisi Keeper & dijital kasa etkinleştirmek, yönergeleri izleyerek sağlanan [Keeper parola Yöneticisi & dijital kasa tek oturum açma öğretici](keeperpasswordmanager-tutorial.md). Bu iki özellik birbirine tamamlayıcı rağmen otomatik kullanıcı hazırlama bağımsız olarak, çoklu oturum açma yapılandırılabilir.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Azure AD'de parola Yöneticisi Keeper & dijital kasa için otomatik kullanıcı hazırlama yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. Seçin **kurumsal uygulamalar**, ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Keeper parola Yöneticisi & dijital kasası**.

    ![Uygulamalar listesinde Keeper parola Yöneticisi & dijital kasa bağlantısı](common/all-applications.png)

3. Seçin **sağlama** sekmesi.

    ![Sağlama sekmesinde](common/provisioning.png)

4. Ayarlama **hazırlama modu** için **otomatik**.

    ![Sağlama sekmesinde](common/provisioning-automatic.png)

5. Altında **yönetici kimlik bilgileri** giriş bölümünde **Kiracı URL'si** ve **gizli belirteç** Keeper parola Yöneticisi'ni ve adım 6'da açıklandığı gibi dijital kasanın hesabı.

6. Oturum açın, [Keeper Yönetici Konsolu](https://keepersecurity.com/console/#login). Tıklayarak **yönetici** ve var olan bir düğüm seçin veya yeni bir tane oluşturun. Gidin **sağlama** sekmenize **Ekle yöntemi**.

    ![Keeper Yönetici Konsolu](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Seçin **SCIM (etki alanları arası kimlik yönetimi sistemi**.

    ![SCIM keeper Ekle](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Tıklayın **sağlama belirteci oluşturma**.

    ![Keeper uç nokta oluşturma](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Değerlerini kopyalayın **URL** ve **belirteci** ve bunları yapıştırın **Kiracı URL'si** ve **gizli belirteç** Azure AD'de. Tıklayın **Kaydet** Keeper üzerinde sağlama Kurulumu tamamlamak için.

    ![Keeper belirteci oluşturma](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. 5\. adımda gösterilen alanlar doldurma üzerine tıklayın **Test Bağlantısı** Azure emin olmak için AD Keeper parola Yöneticisi için dijital kasa & bağlanabilirsiniz. Bağlantı başarısız olursa Keeper parola Yöneticisi & dijital kasası hesabınız yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL'si + simgesi](common/provisioning-testconnection-tenanturltoken.png)

8. İçinde **bildirim e-posta** alanında, bir kişi veya grubun ve sağlama hata bildirimleri almak - onay e-posta adresi girin **birhataoluşursa,bire-postabildirimigönder**.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

9. **Kaydet**’e tıklayın.

10. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory Kullanıcıları Keeper parola Yöneticisi & dijital kasası**.

    ![Keeper kullanıcı eşlemeleri](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Parola Yöneticisi Keeper & dijital kasada Azure AD'den eşitlenen kullanıcı özniteliklerini gözden **eşleme özniteliği** bölümü. Seçilen öznitelikler **eşleşen** özellikleri Keeper parola Yöneticisi & dijital kasa kullanıcı hesaplarını güncelleştirme işlemleri eşleştirmek için kullanılır. Seçin **Kaydet** düğmesine değişiklikleri uygulayın.

    ![Keeper kullanıcı öznitelikleri](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory grupları Keeper parola Yöneticisi & dijital kasası**.

    ![Keeper Grup Eşlemeleri](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Parola Yöneticisi Keeper & dijital kasada Azure AD'den eşitlenen grup öznitelikleri gözden **eşleme özniteliği** bölümü. Seçilen öznitelikler **eşleşen** özellikleri Keeper parola Yöneticisi ve dijital kasa grupları güncelleştirme işlemleri eşleştirmek için kullanılır. Seçin **Kaydet** düğmesine değişiklikleri uygulayın.

    ![Keeper grup öznitelikleri](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Kapsam belirleme filtrelerini yapılandırmak için aşağıdaki yönergelere bakın [Scoping filtre öğretici](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Azure AD parola Yöneticisi Keeper ve dijital kasası hizmeti sağlama etkinleştirmek için değiştirin **sağlama durumu** için **üzerinde** içinde **ayarları** bölümü.

    ![Açıkken sağlama durumu](common/provisioning-toggle-on.png)

16. Kullanıcılara ve/veya istediğiniz grupları Keeper parola Yöneticisi & dijital kasa sağlamak için istenen değerleri seçerek tanımlamak **kapsam** içinde **ayarları** bölümü.

    ![Kapsam sağlama](common/provisioning-scope.png)

17. Sağlama için hazır olduğunuzda, tıklayın **Kaydet**.

    ![Sağlama yapılandırmasını kaydetme](common/provisioning-configuration-save.png)

Bu işlem, tüm kullanıcıların ilk eşitleme başlar ve/veya tanımlı gruplar **kapsam** içinde **ayarları** bölümü. İlk eşitleme yaklaşık 40 dakikada Azure AD sağlama hizmeti çalışıyor sürece oluşan sonraki eşitlemeler uzun sürer. Kullanabileceğiniz **eşitleme ayrıntıları** ilerlemeyi izlemek ve Azure AD sağlama hizmeti üzerinde Keeper parola Yöneticisi tarafından gerçekleştirilen tüm eylemler açıklayan Etkinlik Raporu sağlama için bağlantıları izleyin bölüm & Dijital kasası.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Parola Yöneticisi keeper & dijital kasa gerektiriyor **e-postaları** ve **kullanıcı adı** her iki öznitelikleri için herhangi bir güncelleştirme başka bir değer değiştirecek şekilde aynı kaynak değeri sağlamak için.
* Keeper parola Yöneticisi & dijital kasa değil kullanıcı silme desteği, yalnızca devre dışı bırakın. Devre dışı bırakılmış kullanıcıların Keeper Yönetim Konsolu kullanıcı arabirimini kilitli olarak görünür.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kullanıcı hesabı, kurumsal uygulamalar için sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri gözden geçirin ve sağlama etkinliği raporları alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)

