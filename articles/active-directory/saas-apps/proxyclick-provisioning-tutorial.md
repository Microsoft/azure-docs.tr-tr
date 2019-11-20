---
title: 'Öğretici: Azure Active Directory ile otomatik kullanıcı hazırlama için Proxyclick yapılandırma | Microsoft Docs'
description: Otomatik olarak sağlama ve sağlamasını Proxyclick kullanıcı hesaplarını Azure Active Directory yapılandırmayı öğrenin.
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
ms.openlocfilehash: c1656e6cc0c690e5a2bccfd2efab02aa843875b8
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672900"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Öğretici: Otomatik kullanıcı hazırlama için Proxyclick yapılandırın

Bu öğreticinin amacı otomatik olarak sağlamak ve kullanıcılara ve/veya gruplara Proxyclick sağlamasını Proxyclick ve Azure Active Directory (Azure AD) Azure AD yapılandırmak için gerçekleştirilmesi gereken adımlar göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD kullanıcı sağlama hizmeti üzerinde oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmet yapar, nasıl çalıştığını ve sık sorulan sorular önemli ayrıntılar için bkz. [otomatik kullanıcı hazırlama ve sağlamayı kaldırma Azure Active Directory ile SaaS uygulamalarına](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı, şu anda genel Önizleme aşamasındadır. Genel Microsoft Azure için kullanım koşulları Önizleme özellikleri hakkında daha fazla bilgi için bkz. [ek kullanım koşulları, Microsoft Azure önizlemeleri için](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulları zaten sahip olduğunuzu varsayar:

* Azure AD kiracısı
* [Proxyclick Kiracı](https://www.proxyclick.com/pricing)
* Proxyclick yönetici izinlerine sahip bir kullanıcı hesabı.

## <a name="add-proxyclick-from-the-gallery"></a>Galeriden Proxyclick Ekle

Otomatik kullanıcı hazırlama ile Azure AD için Proxyclick yapılandırmadan önce Proxyclick Azure AD uygulama Galerisi yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Azure AD uygulama galerisinden Proxyclick eklemek için aşağıdaki adımları gerçekleştirin:**

1. İçinde  **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde seçin **Azure Active Directory**.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. Git **kurumsal uygulamalar**ve ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için seçin **yeni uygulama** bölmenin üstünde düğme.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Proxyclick**seçin **Proxyclick** sonuçlar paneli ve ardından **Ekle** uygulama eklemek için Ekle düğmesine.

    ![Sonuç listesinde Proxyclick](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Proxyclick için kullanıcı atama

Azure Active Directory kullanan adlı bir kavram *atamaları* hangi kullanıcıların seçilen uygulamalara erişimi alması belirlemek için. Otomatik kullanıcı hazırlama bağlamında, yalnızca kullanıcı ve/veya Azure AD'de bir uygulamaya atanan gruplar eşitlenir.

Yapılandırma ve otomatik kullanıcı hazırlama etkinleştirmeden önce hangi kullanıcılara ve/veya Azure AD'de grupları Proxyclick erişmesi karar vermeniz gerekir. Karar sonra buradaki yönergeleri izleyerek Proxyclick için bu kullanıcılara ve/veya grupları atayabilirsiniz:

* [Kurumsal bir uygulamayı kullanıcı veya grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Kullanıcılar için Proxyclick atamak için önemli ipuçları

* Önerilir tek bir Azure AD kullanıcı sağlama yapılandırmasını otomatik kullanıcı test etmek için Proxyclick atanır. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcı için Proxyclick atarken, (varsa) geçerli bir uygulamaya özgü rol ataması iletişim kutusunda seçmeniz gerekir. Kullanıcılarla **varsayılan erişim** rol sağlamasından dışlanır.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Proxyclick için otomatik kullanıcı sağlamayı yapılandırma 

Bu bölümde oluşturmak, güncelleştirmek ve kullanıcılar devre dışı bırakmak için sağlama hizmetini Azure AD'yi yapılandırma adımlarında size kılavuzluk eder ve/veya Proxyclick gruplarında Azure AD'de kullanıcı ve/veya grup atamaları temel alarak.

> [!TIP]
> Uygulamayı da seçebilirsiniz SAML tabanlı çoklu oturum açma için Proxyclick etkinleştirmek, yönergeleri izleyerek sağlanan [oturum açma Proxyclick tek öğretici](proxyclick-tutorial.md). Bu iki özellik birbirine tamamlayıcı rağmen otomatik kullanıcı hazırlama bağımsız olarak, çoklu oturum açma yapılandırılabilir.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Azure AD'de Proxyclick için otomatik kullanıcı hazırlama yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. Seçin **kurumsal uygulamalar**, ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Proxyclick**.

    ![Uygulamalar listesinde Proxyclick bağlantı](common/all-applications.png)

3. Seçin **sağlama** sekmesi.

    ![Sağlama sekmesinde](common/provisioning.png)

4. Ayarlama **hazırlama modu** için **otomatik**.

    ![Sağlama sekmesinde](common/provisioning-automatic.png)

5. Alınacak **Kiracı URL'si** ve **gizli belirteç** Proxyclick hesabınızı adım 6'da açıklandığı gibi Kılavuzu izleyin.

6. Oturum açın, [Proxyclick Yönetici Konsolu](https://app.proxyclick.com/login//?destination=%2Fdefault). Gidin **ayarları** > **tümleştirmeler** > **Gözat Market**.

    ![Proxyclick ayarları](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Proxyclick tümleştirmeleri](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Market](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Seçin **Azure AD'ye**. Tıklayın **Şimdi Yükle**.

    ![Azure AD Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Proxyclick yükleme](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Seçin **kullanıcı sağlamayı** tıklatıp **Başlat tümleştirme**. 

    ![Proxyclick kullanıcı sağlama](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    UI artık görünmesini altında uygun ayarların yapılandırmasını **ayarları** > **tümleştirmeler**. Seçin **ayarları** altında **(kullanıcı sağlama) Azure AD**.

    ![Proxyclick oluşturma](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Bulabilirsiniz **Kiracı URL'si** ve **gizli belirteç** burada.

    ![Proxyclick belirteci oluşturma](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. 5\. adımda gösterilen alanlar doldurma üzerine tıklayın **Test Bağlantısı** Azure emin olmak için AD için Proxyclick bağlanabilirsiniz. Bağlantı başarısız olursa Proxyclick hesabınız yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Belirteç](common/provisioning-testconnection-tenanturltoken.png)

8. İçinde **bildirim e-posta** alanında, bir kişi veya grubun ve sağlama hata bildirimleri almak - onay e-posta adresi girin **birhataoluşursa,bire-postabildirimigönder**.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

9. **Kaydet**’e tıklayın.

10. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory Kullanıcıları Proxyclick**.

    ![Proxyclick kullanıcı eşlemeleri](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. İçinde Proxyclick için Azure AD'den eşitlenen kullanıcı özniteliklerini gözden **eşleme özniteliği** bölümü. Seçilen öznitelikler **eşleşen** özellikleri Proxyclick kullanıcı hesaplarını güncelleştirme işlemleri eşleştirmek için kullanılır. Seçin **Kaydet** düğmesine değişiklikleri uygulayın.

    ![Proxyclick kullanıcı öznitelikleri](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Kapsam belirleme filtrelerini yapılandırmak için aşağıdaki yönergelere bakın [Scoping filtre öğretici](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Azure AD sağlama hizmeti için Proxyclick etkinleştirmek için değiştirin **sağlama durumu** için **üzerinde** içinde **ayarları** bölümü.

    ![Açıkken sağlama durumu](common/provisioning-toggle-on.png)

15. Kullanıcılara ve/veya istediğiniz grupları Proxyclick sağlamak için istenen değerleri seçerek tanımlamak **kapsam** içinde **ayarları** bölümü.

    ![Kapsam sağlama](common/provisioning-scope.png)

16. Sağlama için hazır olduğunuzda, tıklayın **Kaydet**.

    ![Sağlama yapılandırmasını kaydetme](common/provisioning-configuration-save.png)

Bu işlem, tüm kullanıcıların ilk eşitleme başlar ve/veya tanımlı gruplar **kapsam** içinde **ayarları** bölümü. İlk eşitleme yaklaşık 40 dakikada Azure AD sağlama hizmeti çalışıyor sürece oluşan sonraki eşitlemeler uzun sürer. Kullanabileceğiniz **eşitleme ayrıntıları** bölüm ilerlemeyi izlemek ve sağlama hizmeti Proxyclick üzerinde Azure AD tarafından gerçekleştirilen tüm eylemler açıklayan Etkinlik Raporu sağlama için bağlantıları izleyin.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Proxyclick gerektirir **e-postaları** ve **kullanıcıadı** aynı kaynak değerine sahip olacak şekilde. Her iki öznitelikleri için herhangi bir güncelleştirme başka bir değer değiştirir.
* Gruplar için sağlama Proxyclick desteklemez.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kullanıcı hesabı, kurumsal uygulamalar için sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri gözden geçirin ve sağlama etkinliği raporları alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)

