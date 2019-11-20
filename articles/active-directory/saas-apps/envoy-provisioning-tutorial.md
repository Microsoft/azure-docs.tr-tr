---
title: 'Öğretici: Azure Active Directory ile otomatik kullanıcı hazırlama için Envoy yapılandırma | Microsoft Docs'
description: Otomatik olarak sağlama ve sağlamasını Envoy kullanıcı hesaplarını Azure Active Directory yapılandırmayı öğrenin.
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
ms.openlocfilehash: df4c5895e15e7e9e63ad1f3d273af1c3fdab2e90
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672719"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Öğretici: Envoy otomatik kullanıcı hazırlama için yapılandırma

Bu öğreticinin amacı Envoy ve Azure otomatik olarak sağlamak için Azure AD'de yapılandırmak için Active Directory'yi (Azure AD) ve kullanıcı devre dışı bırakma sağlama ve/veya gruplara Envoy gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD kullanıcı sağlama hizmeti üzerinde oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmet yapar, nasıl çalıştığını ve sık sorulan sorular önemli ayrıntılar için bkz. [otomatik kullanıcı hazırlama ve sağlamayı kaldırma Azure Active Directory ile SaaS uygulamalarına](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı, şu anda genel Önizleme aşamasındadır. Genel Microsoft Azure için kullanım koşulları Önizleme özellikleri hakkında daha fazla bilgi için bkz. [ek kullanım koşulları, Microsoft Azure önizlemeleri için](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulları zaten sahip olduğunuzu varsayar:

* Azure AD kiracısı
* [Envoy Kiracı](https://envoy.com/pricing/)
* Envoy yönetici izinlerine sahip bir kullanıcı hesabı.

## <a name="add-envoy-from-the-gallery"></a>Envoy Galeriden Ekle

Azure AD ile otomatik kullanıcı hazırlama için Envoy yapılandırmadan önce Envoy Azure AD uygulama galerisinden yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Azure AD uygulama galerisinden Envoy eklemek için aşağıdaki adımları gerçekleştirin:**

1. İçinde  **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde seçin **Azure Active Directory**.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. Git **kurumsal uygulamalar**ve ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için seçin **yeni uygulama** bölmenin üstünde düğme.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Envoy**seçin **Envoy** sonuçlar paneli ve ardından **Ekle** uygulama eklemek için Ekle düğmesine.

    ![Sonuç listesinde envoy](common/search-new-app.png)

## <a name="assigning-users-to-envoy"></a>Envoy için kullanıcı atama

Azure Active Directory kullanan adlı bir kavram *atamaları* hangi kullanıcıların seçilen uygulamalara erişimi alması belirlemek için. Otomatik kullanıcı hazırlama bağlamında, yalnızca kullanıcı ve/veya Azure AD'de bir uygulamaya atanan gruplar eşitlenir.

Yapılandırma ve otomatik kullanıcı hazırlama etkinleştirmeden önce hangi kullanıcılara ve/veya Azure AD'de grupları Envoy erişmesi karar vermeniz gerekir. Karar sonra buradaki yönergeleri izleyerek Envoy için bu kullanıcılara ve/veya grupları atayabilirsiniz:

* [Kurumsal bir uygulamayı kullanıcı veya grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-envoy"></a>Envoy için kullanıcı atama önemli ipuçları

* Önerilir tek bir Azure AD kullanıcı sağlama yapılandırmasını otomatik kullanıcı test etmek için Envoy atanır. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Envoy için kullanıcı atama, atama iletişim kutusunda (varsa) geçerli bir uygulamaya özgü rolü seçmeniz gerekir. Kullanıcılarla **varsayılan erişim** rol sağlamasından dışlanır.

## <a name="configuring-automatic-user-provisioning-to-envoy"></a>Envoy için otomatik kullanıcı sağlamayı yapılandırma 

Bu bölümde oluşturmak, güncelleştirmek ve kullanıcılar devre dışı bırakmak için sağlama hizmetini Azure AD'yi yapılandırma adımlarında size kılavuzluk eder ve/veya Envoy gruplarında Azure AD'de kullanıcı ve/veya grup atamaları temel alarak.

> [!TIP]
> Uygulamayı da seçebilirsiniz SAML tabanlı çoklu oturum açma için Envoy etkinleştirmek, yönergeleri izleyerek sağlanan [oturum açma Envoy tek öğretici](envoy-tutorial.md). Bu iki özellik birbirine tamamlayıcı rağmen otomatik kullanıcı hazırlama bağımsız olarak, çoklu oturum açma yapılandırılabilir.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Azure AD'de Envoy için otomatik kullanıcı hazırlama yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. Seçin **kurumsal uygulamalar**, ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Envoy**.

    ![Uygulamalar listesinde Envoy bağlantı](common/all-applications.png)

3. Seçin **sağlama** sekmesi.

    ![Sağlama sekmesinde](common/provisioning.png)

4. Ayarlama **hazırlama modu** için **otomatik**.

    ![Sağlama sekmesinde](common/provisioning-automatic.png)

5. Altında **yönetici kimlik bilgileri** giriş bölümünde `https://app.envoy.com/scim/v2` içinde **Kiracı URL'si**. Alınacak **gizli belirteç** Envoy hesabınızı adım 6'da açıklandığı gibi Kılavuzu izleyin.

6. Oturum açın, [Envoy Yönetici Konsolu](https://dashboard.envoy.com/login). Tıklayarak **tümleştirmeler**.

    ![Envoy tümleştirmeleri](media/envoy-provisioning-tutorial/envoy01.png)

    Tıklayarak **yükleme** için **Microsoft Azure SCIM tümleştirme**.

    ![Envoy yükleme](media/envoy-provisioning-tutorial/envoy02.png)

    Tıklayarak **Kaydet** için **tüm kullanıcıları eşitleme**. 

    ![Envoy Kaydet](media/envoy-provisioning-tutorial/envoy03.png)

    Gizli dizi doldurulmuş belirteci alın.
    
    ![Envoy OAUTH](media/envoy-provisioning-tutorial/envoy04.png)

7. 5\. adımda gösterilen alanlar doldurma üzerine tıklayın **Test Bağlantısı** Azure emin olmak için AD için Envoy bağlanabilirsiniz. Bağlantı başarısız olursa Envoy hesabınız yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Belirteç](common/provisioning-testconnection-tenanturltoken.png)

8. İçinde **bildirim e-posta** alanında, bir kişi veya grubun ve sağlama hata bildirimleri almak - onay e-posta adresi girin **birhataoluşursa,bire-postabildirimigönder**.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

9. **Kaydet**’e tıklayın.

10. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory Kullanıcıları Envoy**.
    
    ![Envoy kullanıcı öznitelikleri](media/envoy-provisioning-tutorial/envoy-user-mappings.png)
    
11. Envoy içinde için Azure AD'den eşitlenen kullanıcı özniteliklerini gözden **eşleme özniteliği** bölümü. Seçilen öznitelikler **eşleşen** özellikleri Envoy kullanıcı hesaplarını güncelleştirme işlemleri eşleştirmek için kullanılır. Seçin **Kaydet** düğmesine değişiklikleri uygulayın.

    ![Envoy kullanıcı öznitelikleri](media/envoy-provisioning-tutorial/envoy-user-attribute.png)

12. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory gruplarına Envoy**.

    ![Envoy kullanıcı eşlemeleri](media/envoy-provisioning-tutorial/envoy-group-mapping.png)

13. Envoy içinde için Azure AD'den eşitlenen grup öznitelikleri gözden **eşleme özniteliği** bölümü. Seçilen öznitelikler **eşleşen** özellikleri Envoy gruplarında güncelleştirme işlemleri eşleştirmek için kullanılır. Seçin **Kaydet** düğmesine değişiklikleri uygulayın.

    ![Envoy kullanıcı eşlemeleri](media/envoy-provisioning-tutorial/envoy-group-attributes.png)
    
14. Kapsam belirleme filtrelerini yapılandırmak için aşağıdaki yönergelere bakın [Scoping filtre öğretici](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Azure AD sağlama hizmeti için Envoy etkinleştirmek için değiştirin **sağlama durumu** için **üzerinde** içinde **ayarları** bölümü.

    ![Açıkken sağlama durumu](common/provisioning-toggle-on.png)

16. Kullanıcılara ve/veya istediğiniz grupları Envoy sağlamak için istenen değerleri seçerek tanımlamak **kapsam** içinde **ayarları** bölümü.

    ![Kapsam sağlama](common/provisioning-scope.png)

17. Sağlama için hazır olduğunuzda, tıklayın **Kaydet**.

    ![Sağlama yapılandırmasını kaydetme](common/provisioning-configuration-save.png)

Bu işlem, tüm kullanıcıların ilk eşitleme başlar ve/veya tanımlı gruplar **kapsam** içinde **ayarları** bölümü. İlk eşitleme yaklaşık 40 dakikada Azure AD sağlama hizmeti çalışıyor sürece oluşan sonraki eşitlemeler uzun sürer. Kullanabileceğiniz **eşitleme ayrıntıları** bölüm ilerlemeyi izlemek ve sağlama hizmeti Envoy üzerinde Azure AD tarafından gerçekleştirilen tüm eylemler açıklayan Etkinlik Raporu sağlama için bağlantıları izleyin.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kullanıcı hesabı, kurumsal uygulamalar için sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri gözden geçirin ve sağlama etkinliği raporları alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)

