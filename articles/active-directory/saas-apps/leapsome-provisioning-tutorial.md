---
title: 'Öğretici: Azure Active Directory ile otomatik kullanıcı hazırlama için Leapsome yapılandırma | Microsoft Docs'
description: Otomatik olarak sağlama ve sağlamasını Leapsome kullanıcı hesaplarını Azure Active Directory yapılandırmayı öğrenin.
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
ms.date: 06/28/2019
ms.author: jeedes
ms.openlocfilehash: e02deaa29b40e64b63d9afb471717feef78b3cee
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672690"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>Öğretici: Otomatik kullanıcı hazırlama için Leapsome yapılandırın

Bu öğreticinin amacı otomatik olarak sağlamak ve kullanıcılara ve/veya gruplara Leapsome sağlamasını Leapsome ve Azure Active Directory (Azure AD) Azure AD yapılandırmak için gerçekleştirilmesi gereken adımlar göstermektir.

> [!NOTE]
>  Bu öğreticide, Azure AD kullanıcı sağlama hizmeti üzerinde oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmet yapar, nasıl çalıştığını ve sık sorulan sorular önemli ayrıntılar için bkz. [otomatik kullanıcı hazırlama ve sağlamayı kaldırma Azure Active Directory ile SaaS uygulamalarına](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı, şu anda Önizleme aşamasındadır. Genel Microsoft Azure için kullanım koşulları Önizleme özellikleri hakkında daha fazla bilgi için bkz. [ek kullanım koşulları, Microsoft Azure önizlemeleri için](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulları zaten sahip olduğunuzu varsayar:

* Azure AD kiracısı.
* A [Leapsome](https://www.Leapsome.com/en/pricing) Kiracı.
* Leapsome yönetici izinlerine sahip bir kullanıcı hesabı.

## <a name="assigning-users-to-leapsome"></a>Leapsome için kullanıcı atama

Azure Active Directory kullanan adlı bir kavram *atamaları* hangi kullanıcıların seçilen uygulamalara erişimi alması belirlemek için. Otomatik kullanıcı hazırlama bağlamında, yalnızca kullanıcı ve/veya Azure AD'de bir uygulamaya atanan gruplar eşitlenir.

Yapılandırma ve otomatik kullanıcı hazırlama etkinleştirmeden önce hangi kullanıcılara ve/veya Azure AD'de grupları Leapsome erişmesi karar vermeniz gerekir. Karar sonra buradaki yönergeleri izleyerek Leapsome için bu kullanıcılara ve/veya grupları atayabilirsiniz:
* [Kurumsal bir uygulamayı kullanıcı veya grup atama](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>Kullanıcılar için Leapsome atamak için önemli ipuçları

* Önerilir tek bir Azure AD kullanıcı sağlama yapılandırmasını otomatik kullanıcı test etmek için Leapsome atanır. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcı için Leapsome atarken, (varsa) geçerli bir uygulamaya özgü rol ataması iletişim kutusunda seçmeniz gerekir. Kullanıcılarla **varsayılan erişim** rol sağlamasından dışlanır.


## <a name="setup-leapsome-for-provisioning"></a>Kurulum Leapsome sağlama

1. Oturum açın, [Leapsome Yönetici Konsolu](https://www.Leapsome.com/app/#/login). Gidin **Ayarları > yönetici ayarları**.

    ![Leapsome Yönetici Konsolu](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  Gidin **tümleştirmeler > SCIM kullanıcı sağlama**.

    ![SCIM Leapsome Ekle](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  Kopyalama **SCIM kimlik doğrulama belirteci**. Bu değer Leapsome uygulamanızın Azure portalında sağlama sekmesindeki belirteci gizli anahtarı alanına girilir.

    ![Leapsome belirteci oluşturma](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>Galeriden Leapsome Ekle

Otomatik kullanıcı hazırlama ile Azure AD için Leapsome yapılandırmadan önce Leapsome Azure AD uygulama Galerisi yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Azure AD uygulama galerisinden Leapsome eklemek için aşağıdaki adımları gerçekleştirin:**

1. İçinde  **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde seçin **Azure Active Directory**.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. Git **kurumsal uygulamalar**ve ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için seçin **yeni uygulama** bölmenin üstünde düğme.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Leapsome**seçin **Leapsome** sonuçlar paneli ve ardından **Ekle** uygulama eklemek için Ekle düğmesine.

    ![Sonuç listesinde Leapsome](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>Leapsome için otomatik kullanıcı sağlamayı yapılandırma 

Bu bölümde oluşturmak, güncelleştirmek ve kullanıcılar devre dışı bırakmak için sağlama hizmetini Azure AD'yi yapılandırma adımlarında size kılavuzluk eder ve/veya Leapsome gruplarında Azure AD'de kullanıcı ve/veya grup atamaları temel alarak.

> [!TIP]
> Uygulamayı da seçebilirsiniz SAML tabanlı çoklu oturum açma için Leapsome etkinleştirmek, yönergeleri izleyerek sağlanan [Leapsome tek oturum açma öğretici](Leapsome-tutorial.md). Bu iki özellik birbirine tamamlayıcı ancak çoklu oturum açma otomatik kullanıcı hazırlama bağımsız olarak yapılandırılabilir.

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>Azure AD'de Leapsome için otomatik kullanıcı hazırlama yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. Seçin **kurumsal uygulamalar**, ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Leapsome**.

    ![Uygulamalar listesinde Leapsome bağlantı](common/all-applications.png)

3. Seçin **sağlama** sekmesi.

    ![Sağlama sekmesinde](common/provisioning.png)

4. Ayarlama **hazırlama modu** için **otomatik**.

    ![Sağlama sekmesinde](common/provisioning-automatic.png)

5. Altında **yönetici kimlik bilgileri** giriş bölümünde `https://www.leapsome.com/api/scim` içinde **Kiracı URL'si**. Giriş **SCIM kimlik doğrulama belirtecini** değeri daha önce aldığınız **gizli belirteç**. Tıklayın **Test Bağlantısı** Azure emin olmak için AD için Leapsome bağlanabilirsiniz. Bağlantı başarısız olursa Leapsome hesabınız yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL'si + simgesi](common/provisioning-testconnection-tenanturltoken.png)

6. İçinde **bildirim e-posta** alanında, bir kişi veya grubun ve sağlama hata bildirimleri almak - onay e-posta adresi girin **birhataoluşursa,bire-postabildirimigönder**.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory Kullanıcıları Leapsome**.

    ![Leapsome kullanıcı eşlemeleri](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. İçinde Leapsome için Azure AD'den eşitlenen kullanıcı özniteliklerini gözden **eşleme özniteliği** bölümü. Seçilen öznitelikler **eşleşen** özellikleri Leapsome kullanıcı hesaplarını güncelleştirme işlemleri eşleştirmek için kullanılır. Seçin **Kaydet** düğmesine değişiklikleri uygulayın.

    ![Leapsome kullanıcı öznitelikleri](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory gruplarına Leapsome**.

    ![Leapsome Grup Eşlemeleri](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. İçinde Leapsome için Azure AD'den eşitlenen grup öznitelikleri gözden **eşleme özniteliği** bölümü. Seçilen öznitelikler **eşleşen** özellikleri Leapsome gruplarında güncelleştirme işlemleri eşleştirmek için kullanılır. Seçin **Kaydet** düğmesine değişiklikleri uygulayın.

    ![Leapsome grup öznitelikleri](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. Kapsam belirleme filtrelerini yapılandırmak için aşağıdaki yönergelere bakın [Scoping filtre öğretici](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Azure AD sağlama hizmeti için Leapsome etkinleştirmek için değiştirin **sağlama durumu** için **üzerinde** içinde **ayarları** bölümü.

    ![Açıkken sağlama durumu](common/provisioning-toggle-on.png)

14. Kullanıcılara ve/veya istediğiniz grupları Leapsome sağlamak için istenen değerleri seçerek tanımlamak **kapsam** içinde **ayarları** bölümü.

    ![Kapsam sağlama](common/provisioning-scope.png)

15. Sağlama için hazır olduğunuzda, tıklayın **Kaydet**.

    ![Sağlama yapılandırmasını kaydetme](common/provisioning-configuration-save.png)

Bu işlem, tüm kullanıcıların ilk eşitleme başlar ve/veya tanımlı gruplar **kapsam** içinde **ayarları** bölümü. İlk eşitleme yaklaşık 40 dakikada Azure AD sağlama hizmeti çalışıyor sürece oluşan sonraki eşitlemeler uzun sürer. Kullanabileceğiniz **eşitleme ayrıntıları** bölüm ilerlemeyi izlemek ve sağlama hizmeti Leapsome üzerinde Azure AD tarafından gerçekleştirilen tüm eylemler açıklayan Etkinlik Raporu sağlama için bağlantıları izleyin.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Leapsome gerektirir **kullanıcıadı** benzersiz olmalıdır.
* Leapsome yalnızca iş e-posta adresleri kaydedilmesine izin verir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kullanıcı hesabı, kurumsal uygulamalar için sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri gözden geçirin ve sağlama etkinliği raporları alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)
