---
title: 'Öğretici: Azure Active Directory ile otomatik kullanıcı hazırlama için dinamik sinyal yapılandırma | Microsoft Docs'
description: Otomatik olarak sağlama ve sağlamasını dinamik sinyal kullanıcı hesaplarını Azure Active Directory yapılandırmayı öğrenin.
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
ms.openlocfilehash: f9bfa0eaea67919bad775af5cf9071cd3d6973ca
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672660"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>Öğretici: Otomatik kullanıcı hazırlama için dinamik sinyalini Yapılandır

Bu öğreticinin amacı otomatik olarak sağlamak ve kullanıcılara ve/veya gruplara dinamik sinyal sağlamasını için dinamik sinyal ve Azure Active Directory (Azure AD) Azure AD yapılandırmak için gerçekleştirilmesi gereken adımlar göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD kullanıcı sağlama hizmeti üzerinde oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmet yapar, nasıl çalıştığını ve sık sorulan sorular önemli ayrıntılar için bkz. [otomatik kullanıcı hazırlama ve sağlamayı kaldırma Azure Active Directory ile SaaS uygulamalarına](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı, şu anda genel Önizleme aşamasındadır. Genel Microsoft Azure için kullanım koşulları Önizleme özellikleri hakkında daha fazla bilgi için bkz. [ek kullanım koşulları, Microsoft Azure önizlemeleri için](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulları zaten sahip olduğunuzu varsayar:

* Azure AD kiracısı
* [Dinamik sinyal Kiracı](https://dynamicsignal.com/)
* Dinamik sinyal yönetici izinlerine sahip bir kullanıcı hesabı.

## <a name="add-dynamic-signal-from-the-gallery"></a>Dinamik sinyal Galeriden Ekle

Otomatik kullanıcı hazırlama Azure AD ile dinamik sinyal yapılandırmadan önce dinamik sinyal Azure AD uygulama galerisinden yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Azure AD uygulama galerisinden dinamik sinyal eklemek için aşağıdaki adımları gerçekleştirin:**

1. İçinde  **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde seçin **Azure Active Directory**.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. Git **kurumsal uygulamalar**ve ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için seçin **yeni uygulama** bölmenin üstünde düğme.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **dinamik sinyal**seçin **dinamik sinyal** sonuçlar paneli ve ardından **Ekle** uygulama eklemek için Ekle düğmesine.

    ![Sonuç listesinde dinamik sinyali](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Dinamik sinyale kullanıcıları atama

Azure Active Directory kullanan adlı bir kavram *atamaları* hangi kullanıcıların seçilen uygulamalara erişimi alması belirlemek için. Otomatik kullanıcı hazırlama bağlamında, yalnızca kullanıcı ve/veya Azure AD'de bir uygulamaya atanan gruplar eşitlenir.

Yapılandırma ve otomatik kullanıcı hazırlama etkinleştirmeden önce hangi kullanıcılara ve/veya Azure AD'de grupları dinamik sinyal erişmesi karar vermeniz gerekir. Karar sonra buradaki yönergeleri izleyerek dinamik sinyale bu kullanıcılara ve/veya grupları atayabilirsiniz:

* [Kurumsal bir uygulamayı kullanıcı veya grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Dinamik sinyale kullanıcılara atama önemli ipuçları

* Önerilir tek bir Azure AD kullanıcı sağlama yapılandırmasını otomatik kullanıcı test etmek için dinamik sinyale atanır. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Dinamik bir sinyal için kullanıcı atama, atama iletişim kutusunda (varsa) geçerli bir uygulamaya özgü rolü seçmeniz gerekir. Kullanıcılarla **varsayılan erişim** rol sağlamasından dışlanır.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Dinamik sinyaline otomatik kullanıcı sağlamayı yapılandırma 

Bu bölümde oluşturmak, güncelleştirmek ve kullanıcılara ve/veya Azure AD'de kullanıcı ve/veya grup atamalarını göre dinamik bir sinyal gruplarında devre dışı bırakmak için Azure AD sağlama hizmeti yapılandırmak için gereken adımları size kılavuzluk eder.

> [!TIP]
> Uygulamayı da seçebilirsiniz SAML tabanlı çoklu oturum açma dinamik sinyal etkinleştirmek, yönergeleri izleyerek sağlanan [oturum açma dinamik sinyal tek öğretici](dynamicsignal-tutorial.md). Bu iki özellik birbirine tamamlayıcı rağmen otomatik kullanıcı hazırlama bağımsız olarak, çoklu oturum açma yapılandırılabilir.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Azure AD'de dinamik bir sinyal için otomatik kullanıcı hazırlama yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. Seçin **kurumsal uygulamalar**, ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **dinamik sinyal**.

    ![Uygulamalar listesinde dinamik sinyal bağlantı](common/all-applications.png)

3. Seçin **sağlama** sekmesi.

    ![Sağlama sekmesinde](common/provisioning.png)

4. Ayarlama **hazırlama modu** için **otomatik**.

    ![Sağlama sekmesinde](common/provisioning-automatic.png)

5. Altında **yönetici kimlik bilgileri** giriş bölümünde **Kiracı URL'si** ve **gizli belirteç** adım 6'da açıklandığı gibi dinamik sinyalini'nin hesabının.

6. Dinamik sinyal Yönetici konsolunda gidin **yönetici > Gelişmiş > API**.

    ![Dinamik sinyal sağlama](./media/dynamic-signal-provisioning-tutorial/secret-token-1.png)

    Kopyalama **SCIM API URL'si** için **Kiracı URL'si**. Tıklayarak **yeni belirteç Oluştur** oluşturmak için bir **taşıyıcı belirteci** ve değeri kopyalayın **gizli belirteç**.

    ![Dinamik sinyal sağlama](./media/dynamic-signal-provisioning-tutorial/secret-token-2.png)

7. 5\. adımda gösterilen alanlar doldurma üzerine tıklayın **Test Bağlantısı** Azure emin olmak için AD, dinamik sinyale bağlanabilirsiniz. Bağlantı başarısız olursa, dinamik sinyal hesabının yönetici izinlerine sahip olun ve yeniden deneyin.

    ![Kiracı URL'si + simgesi](common/provisioning-testconnection-tenanturltoken.png)

8. İçinde **bildirim e-posta** alanında, bir kişi veya grubun ve sağlama hata bildirimleri almak - onay e-posta adresi girin **birhataoluşursa,bire-postabildirimigönder**.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

9. **Kaydet**’e tıklayın.

10. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory Kullanıcıları için dinamik sinyal**.

    ![Dinamik sinyal kullanıcı eşlemeleri](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. Dinamik sinyalin için Azure AD'den eşitlenen kullanıcı özniteliklerini gözden **eşleme özniteliği** bölümü. Seçilen öznitelikler **eşleşen** özellikleri, dinamik güncelleştirme işlemlerini sinyal kullanıcı hesaplarını eşleştirmek için kullanılır. Seçin **Kaydet** düğmesine değişiklikleri uygulayın.

    ![Dinamik sinyal kullanıcı öznitelikleri](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. Kapsam belirleme filtrelerini yapılandırmak için aşağıdaki yönergelere bakın [Scoping filtre öğretici](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Azure AD sağlama hizmeti dinamik sinyal etkinleştirmek için değiştirin **sağlama durumu** için **üzerinde** içinde **ayarları** bölümü.

    ![Açıkken sağlama durumu](common/provisioning-toggle-on.png)

14. Kullanıcılara ve/veya istediğiniz grupları dinamik sinyal sağlamak için istenen değerleri seçerek tanımlamak **kapsam** içinde **ayarları** bölümü.

    ![Kapsam sağlama](common/provisioning-scope.png)

15. Sağlama için hazır olduğunuzda, tıklayın **Kaydet**.

    ![Sağlama yapılandırmasını kaydetme](common/provisioning-configuration-save.png)

Bu işlem, tüm kullanıcıların ilk eşitleme başlar ve/veya tanımlı gruplar **kapsam** içinde **ayarları** bölümü. İlk eşitleme yaklaşık 40 dakikada Azure AD sağlama hizmeti çalışıyor sürece oluşan sonraki eşitlemeler uzun sürer. Kullanabileceğiniz **eşitleme ayrıntıları** bölüm ilerlemeyi izlemek ve Azure AD dinamik sinyal hizmette sağlama tarafından gerçekleştirilen tüm eylemler açıklayan Etkinlik Raporu sağlama için bağlantıları izleyin.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Dinamik sinyal kalıcı kullanıcı siler Azure AD'den desteklemez. Dinamik sinyal bir kullanıcının kalıcı olarak silmek için işlemi dinamik sinyal Yönetim Konsolu kullanıcı arabirimini çıkarılmak zorunda. 
* Dinamik sinyal gruplar şu anda desteklemiyor.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kullanıcı hesabı, kurumsal uygulamalar için sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri gözden geçirin ve sağlama etkinliği raporları alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)

