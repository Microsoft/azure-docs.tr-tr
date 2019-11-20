---
title: 'Öğretici: Azure Active Directory ile otomatik kullanıcı hazırlama için Smartsheet yapılandırma | Microsoft Docs'
description: Otomatik olarak sağlama ve sağlamasını Smartsheet kullanıcı hesaplarını Azure Active Directory yapılandırmayı öğrenin.
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
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: f0ca2dfa90e1312db664962e7ffbe6b3f4dd96e1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670946"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Öğretici: Otomatik kullanıcı hazırlama için Smartsheet yapılandırın

Bu öğreticinin amacı otomatik olarak sağlamak ve kullanıcılara ve/veya gruplara Smartsheet sağlamasını için Smartsheet ve Azure Active Directory (Azure AD) Azure AD yapılandırmak için gerçekleştirilmesi gereken adımlar göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD kullanıcı sağlama hizmeti üzerinde oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmet yapar, nasıl çalıştığını ve sık sorulan sorular önemli ayrıntılar için bkz. [otomatik kullanıcı hazırlama ve sağlamayı kaldırma Azure Active Directory ile SaaS uygulamalarına](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı, şu anda genel Önizleme aşamasındadır. Genel Microsoft Azure için kullanım koşulları Önizleme özellikleri hakkında daha fazla bilgi için bkz. [ek kullanım koşulları, Microsoft Azure önizlemeleri için](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulları zaten sahip olduğunuzu varsayar:

* Azure AD kiracısı
* [Bir Smartsheet Kiracı](https://www.smartsheet.com/pricing)
* Bir Smartsheet Kurumsal veya Kurumsal Premier plan Sistem Yöneticisi izinlerine sahip bir kullanıcı hesabı.

## <a name="assign-users-to-smartsheet"></a>Smartsheet için kullanıcı atama

Azure Active Directory kullanan adlı bir kavram *atamaları* hangi kullanıcıların seçilen uygulamalara erişimi alması belirlemek için. Otomatik kullanıcı hazırlama bağlamında, yalnızca kullanıcı ve/veya Azure AD'de bir uygulamaya atanan gruplar eşitlenir.

Yapılandırma ve otomatik kullanıcı hazırlama etkinleştirmeden önce hangi kullanıcılara ve/veya Azure AD'de grupları Smartsheet erişmesi karar vermeniz gerekir. Karar sonra buradaki yönergeleri izleyerek Smartsheet için bu kullanıcılara ve/veya grupları atayabilirsiniz:

* [Kurumsal bir uygulamayı kullanıcı veya grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Smartsheet için kullanıcı atama önemli ipuçları

* Önerilir tek bir Azure AD kullanıcı sağlama yapılandırmasını otomatik kullanıcı test etmek için Smartsheet atanır. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcı için Smartsheet atarken, (varsa) geçerli bir uygulamaya özgü rol ataması iletişim kutusunda seçmeniz gerekir. Kullanıcılarla **varsayılan erişim** rol sağlamasından dışlanır.

* Kullanıcı rolü atamaları Smartsheet ve Azure AD arasındaki eşlik emin olmak için tam Smartsheet kullanıcı listesi içinde aynı rol atamaları kullanmak için önerilir. Bu kullanıcı listesini Smartsheet'teki verileri almak için gidin **Hesap Yöneticisi > Kullanıcı Yönetimi > Diğer Eylemler > kullanıcı listesini indirin (csv)** .

* Uygulamasında belirli özelliklerine erişebilmeniz için bir kullanıcı birden çok rolü Smartsheet gerektirir. Kullanıcı türleri ve smartsheet'teki izinler hakkında daha fazla bilgi için Git [kullanıcı türlerinde ve izinlerde](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Birden çok rol Smartsheet'te, atanmış bir kullanıcı varsa, **gerekir** bu rol atamaları burada kullanıcılar kaybeder Smartsheet nesnelere erişimi kalıcı olarak bir senaryonun olmaması için Azure AD'de çoğaltıldığından emin olun. Her benzersiz rolüne smartsheet'teki **gerekir** , Azure AD'de farklı bir gruba atanmış. Kullanıcı **gerekir** sonra istenen rollerine karşılık gelen grupların her biri için eklendi. 

## <a name="set-up-smartsheet-for-provisioning"></a>Smartsheet sağlamak için ayarlayın

Otomatik kullanıcı hazırlama ile Azure AD için Smartsheet yapılandırmadan önce üzerinde Smartsheet SCIM sağlamayı etkinleştirmek gerekir.

1. Olarak oturum bir **SysAdmin** içinde **[Smartsheet portalı](https://app.smartsheet.com/b/home)** gidin **Hesap Yöneticisi**.

    ![Smartsheet Hesap Yöneticisi](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Git **güvenlik denetimleri > kullanıcı otomatik sağlama > Düzenle**.

    ![Smartsheet güvenlik denetimleri](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Ekle ve e-posta etki alanları için Smartsheet Azure AD'den sağlamayı planlama kullanıcılar için doğrulayın. Seçin **etkin** sağlama tüm eylemleri yalnızca Azure AD'den geldiğinden emin olun ve ayrıca, Smartsheet kullanıcı listesi ile Azure AD atamaları eşitlenmiş olduğundan emin olun.

    ![Smartsheet kullanıcı sağlama](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Doğrulama tamamlandıktan sonra etki alanı etkinleştirmeniz gerekir. 

    ![Smartsheet Activate Domain](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Oluşturma **gizli belirteç** giderek Azure AD ile otomatik kullanıcı hazırlama yapılandırmak için gereken **uygulamalar ve tümleştirmeler**.

    ![Smartsheet yükleme](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Seçin **API erişimi**. Tıklayın **oluştur yeni bir erişim belirteci**.

    ![Smartsheet yükleme](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. API erişim belirteci adını tanımlayın.           **Tamam**'ı tıklatın.

    ![Smartsheet yükleme](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. API erişim belirteci kopyalayın ve bu raporu görüntüleyebilirsiniz yalnızca bir kez olarak kaydedin. Bu gereklidir **gizli belirteç** Azure ad alanı.

    ![Smartsheet belirteci](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Smartsheet Galeriden Ekle

Smartsheet otomatik kullanıcı hazırlama Azure AD ile yapılandırmak için Smartsheet Azure AD uygulama galerisinden yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. İçinde  **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde seçin **Azure Active Directory**.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. Git **kurumsal uygulamalar**ve ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için seçin **yeni uygulama** bölmenin üstünde düğme.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Smartsheet**seçin **Smartsheet** Sonuçlar panelinde. 

    ![Sonuç listesinde Smartsheet](common/search-new-app.png)

5. Seçin **Smartsheet kaydolun** düğmesine tıklamamalıdır Smartsheet'ın oturum açma sayfasına yönlendirir. 

    ![Smartsheet OIDC Ekle](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Smartsheet, Openıdconnect uygulama olduğundan, Smartsheet ile Microsoft iş hesabınızı kullanarak oturum açmak seçin.

    ![Smartsheet OIDC oturum açma](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Başarılı bir kimlik doğrulamasından sonra onay sayfası için onay istemi kabul edin. Uygulama, kiracınıza sonra bir otomatik olarak eklenir ve Smartsheet hesabınıza yönlendirilir.

    ![Smartsheet Oıdc onayı](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Smartsheet için otomatik kullanıcı sağlamayı yapılandırma 

Bu bölümde oluşturmak, güncelleştirmek ve kullanıcılar devre dışı bırakmak için sağlama hizmetini Azure AD'yi yapılandırma adımlarında size kılavuzluk eder ve/veya Azure AD'de kullanıcı ve/veya grup atamaları bu smartsheet'teki gruplandırır.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Azure AD'de Smartsheet için otomatik kullanıcı hazırlama yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. Seçin **kurumsal uygulamalar**, ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Smartsheet**.

    ![Uygulamalar listesini Smartsheet bağlantıdaki](common/all-applications.png)

3. Seçin **sağlama** sekmesi.

    ![Sağlama sekmesinde](common/provisioning.png)

4. Ayarlama **hazırlama modu** için **otomatik**.

    ![Sağlama sekmesinde](common/provisioning-automatic.png)

5. Altında **yönetici kimlik bilgileri** giriş bölümünde `https://scim.smartsheet.com/v2/` içinde **Kiracı URL'si**. Giriş değeri alındı ve Smartsheet'te daha önce kaydedilen **gizli belirteç**. Tıklayın **Test Bağlantısı** Azure emin olmak için AD, Smartsheet ile bağlanabilirsiniz. Bağlantı başarısız olursa, Smartsheet hesabınıza SysAdmin izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. İçinde **bildirim e-posta** alanında, bir kişi veya grubun ve sağlama hata bildirimleri almak - onay e-posta adresi girin **birhataoluşursa,bire-postabildirimigönder**.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory Kullanıcıları için Smartsheet**.

    ![Smartsheet kullanıcı eşlemeleri](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Smartsheet'te için Azure AD'den eşitlenen kullanıcı özniteliklerini gözden **eşleme özniteliği** bölümü. Seçilen öznitelikler **eşleşen** özellikleri, Smartsheet kullanıcı hesaplarını güncelleştirme işlemleri eşleştirmek için kullanılır. Seçin **Kaydet** düğmesine değişiklikleri uygulayın.

    ![Smartsheet kullanıcı öznitelikleri](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Kapsam belirleme filtrelerini yapılandırmak için aşağıdaki yönergelere bakın [Scoping filtre öğretici](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Azure AD sağlama hizmeti için Smartsheet etkinleştirmek için değiştirin **sağlama durumu** için **üzerinde** içinde **ayarları** bölümü.

    ![Açıkken sağlama durumu](common/provisioning-toggle-on.png)

12. Kullanıcılara ve/veya istediğiniz grupları Smartsheet sağlamak için istenen değerleri seçerek tanımlamak **kapsam** içinde **ayarları** bölümü.

    ![Kapsam sağlama](common/provisioning-scope.png)

13. Sağlama için hazır olduğunuzda, tıklayın **Kaydet**.

    ![Sağlama yapılandırmasını kaydetme](common/provisioning-configuration-save.png)

Bu işlem, tüm kullanıcıların ilk eşitleme başlar ve/veya tanımlı gruplar **kapsam** içinde **ayarları** bölümü. İlk eşitleme yaklaşık 40 dakikada Azure AD sağlama hizmeti çalışıyor sürece oluşan sonraki eşitlemeler uzun sürer. Kullanabileceğiniz **eşitleme ayrıntıları** bölüm ilerlemeyi izlemek ve sağlama hizmeti Smartsheet üzerinde Azure AD tarafından gerçekleştirilen tüm eylemler açıklayan Etkinlik Raporu sağlama için bağlantıları izleyin.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Smartsheet, geçici silme desteklemez. Bir kullanıcı ayarlandığında **etkin** öznitelik False olarak ayarlandığında, Smartsheet kullanıcıyı kalıcı olarak siler.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kullanıcı hesabı, kurumsal uygulamalar için sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri gözden geçirin ve sağlama etkinliği raporları alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)
