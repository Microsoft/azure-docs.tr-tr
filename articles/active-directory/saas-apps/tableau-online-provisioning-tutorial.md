---
title: 'Öğretici: Azure Active Directory ile otomatik kullanıcı hazırlama için Tableau Online yapılandırma | Microsoft Docs'
description: Otomatik olarak sağlama ve sağlamasını kaldırma Tableau çevrimiçi kullanıcı hesapları için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e203e88de8d806f489e5a7ab9bfd227c8232f84
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670906"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Öğretici: Tableau çevrimiçi yapılandırmak için otomatik kullanıcı hazırlama

Bu öğreticide, Tableau çevrimiçi ve Azure Active Directory (Azure AD) Azure AD yapılandırmak için otomatik olarak sağlamak ve kullanıcıların ve grupların Tableau çevrimiçi sağlamasını kaldırmak için gerçekleştirme adımları gösterilmektedir.

> [!NOTE]
> Bu öğreticide, Azure AD kullanıcı sağlama hizmeti üzerinde oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmet yapar, nasıl çalıştığını ve sık sorulan sorular hakkında daha fazla bilgi için bkz: [kullanıcı sağlamayı ve Azure Active Directory ile hizmet olarak yazılım-a-(SaaS) uygulamalarına sağlama kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, olduğunu varsayar:

*   Azure AD kiracısı.
*   A [Tableau çevrimiçi Kiracı](https://www.tableau.com/).
*   Yönetici izinlerine sahip bir kullanıcı hesabı Tableau çevrimiçi.

> [!NOTE]
> Azure AD tümleştirmesi sağlama dayanan [Tableau çevrimiçi Rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Bu API, Tableau Online geliştiricileri için kullanılabilir.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Azure Market'ten Tableau çevrimiçi Ekle
Tableau çevrimiçi otomatik kullanıcı hazırlama ile Azure AD için yapılandırmadan önce Tableau çevrimiçi listenize yönetilen SaaS uygulamalarını Azure Marketi'nde ekleyin.

Marketten Tableau çevrimiçi eklemek için aşağıdaki adımları izleyin.

1. İçinde [Azure portalında](https://portal.azure.com), sol taraftaki gezinti bölmesinde seçin **Azure Active Directory**.

    ![Azure Active Directory simgesi](common/select-azuread.png)

2. Git **kurumsal uygulamalar**ve ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için seçin **yeni uygulama** iletişim kutusunun üst.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Tableau çevrimiçi** seçip **Tableau çevrimiçi** sonucu panelinden. Uygulama eklemek için seçin **Ekle**.

    ![Sonuç listesinde tableau çevrimiçi](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Kullanıcıları için çevrimiçi Tableau atama

Azure Active Directory kullanan adlı bir kavram *atamaları* hangi kullanıcıların seçilen uygulamalara erişimi alması belirlemek için. Otomatik kullanıcı hazırlama bağlamında, yalnızca kullanıcılar veya Azure AD'de bir uygulamaya atanan gruplar eşitlenir.

Yapılandırma ve otomatik kullanıcı sağlamayı etkinleştirmek için önce hangi kullanıcıların veya grupların Azure AD'de Tableau Online'a erişmesi gereken karar verin. Bu kullanıcılar veya gruplar için çevrimiçi Tableau atamak için yönergeleri izleyin. [kurumsal bir uygulamayı kullanıcı veya grup atama](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Kullanıcıların çevrimiçi Tableau atamak için önemli ipuçları

*   Tek bir atamanızı öneririz Azure AD kullanıcı sağlama yapılandırmasını otomatik kullanıcı test etmek için çevrimiçi Tableau. Daha sonra ek kullanıcılar veya gruplar atayabilirsiniz.

*   Bir kullanıcı Tableau çevrimiçi olarak atadığınızda, geçerli herhangi bir uygulamaya özgü rol ataması iletişim kutusunda varsa, seçin. Kullanıcılarla **varsayılan erişim** rol sağlamasından dışlanır.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Tableau Online'a otomatik kullanıcı sağlamayı yapılandırma

Bu bölümde Azure AD sağlama hizmeti yapılandırma adımlarında size kılavuzluk eder. Oluşturma, güncelleştirme ve kullanıcılar veya gruplar, Tableau ile Azure AD'de kullanıcı veya grup atamalarını göre çevrimiçi devre dışı bırakmak için kullanın.

> [!TIP]
> SAML tabanlı çoklu oturum açma için Tableau çevrimiçi etkinleştirebilirsiniz. Bölümündeki yönergeleri [Tableau çevrimiçi tek oturum açma öğretici](tableauonline-tutorial.md). Bu iki özellik birbirini tamamlar ancak otomatik kullanıcı hazırlama bağımsız olarak, çoklu oturum açma yapılandırılabilir.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Azure AD'de Tableau Online için otomatik kullanıcı sağlamayı yapılandırma

1. [Azure Portal](https://portal.azure.com) oturum açın. Seçin **kurumsal uygulamalar** > **tüm uygulamaları** > **Tableau çevrimiçi**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Tableau çevrimiçi**.

    ![Uygulamalar listesinde Tableau çevrimiçi bağlantı](common/all-applications.png)

3. Seçin **sağlama** sekmesi.

    ![Tableau çevrimiçi sağlama](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Ayarlama **hazırlama modu** için **otomatik**.

    ![Tableau çevrimiçi sağlama modu](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Altında **yönetici kimlik bilgileri** bölümünde, etki alanı, yönetici kullanıcı adı, yönetici parolası ve içerik Tableau Online hesabınızın URL'sini girin:

   * İçinde **etki alanı** kutusunda, adım 6'da temel bir alt etki alanı doldurun.

   * İçinde **yönetici kullanıcı adı** kutusunda, yönetici hesabı Clarizen kiracınıza kullanıcı adı girin. admin@contoso.com bunun bir örneğidir.

   * İçinde **yönetici parolası** kutusunda, yönetici kullanıcı adı için karşılık gelen yönetici hesabının parolasını girin.

   * İçinde **içerik URL'si** kutusunda, adım 6'da temel bir alt etki alanı doldurun.

6. Yönetici hesabınıza çevrimiçi Tableau oturum açtıktan sonra değerleri alabilirsiniz **etki alanı** ve **içerik URL'si** gelen yönetim sayfasının URL'si.

    * **Etki alanı** Tableau çevrimiçi için hesap URL'sinin bu bölümünden kopyalanabilir:

        ![Tableau çevrimiçi etki alanı](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * **İçerik URL'si** Tableau çevrimiçi için hesap sayfasından Bu bölüm kopyalanabilir. Hesap Kurulumu sırasında tanımlanan bir değerdir. Bu örnekte, "contoso" değeridir:

        ![Tableau çevrimiçi içerik URL'si](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > **Etki alanı** burada gösterilen farklı olabilir.

7. Adım 5'te gösterilen kutuları doldurduktan sonra seçin **Test Bağlantısı** Azure'un emin olmak için AD Tableau Online'a bağlayabilirsiniz. Bağlantı başarısız olursa, Tableau Online hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Tableau çevrimiçi Bağlantıyı Sına](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. İçinde **bildirim e-posta** kutusunda kişinin e-posta adresi girin veya sağlama hata bildirimleri almak için Grup. Seçin **bir hata oluştuğunda e-posta bildirimi gönder** onay kutusu.

    ![Tableau çevrimiçi bildirim e-posta](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. **Kaydet**’i seçin.

10. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory Kullanıcıları için Tableau**.

    ![Tableau çevrimiçi kullanıcı eşitleme](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Tableau çevrimiçi Azure AD'den eşitlenen kullanıcı özniteliklerini gözden **öznitelik eşlemelerini** bölümü. Seçilen öznitelikler **eşleşen** özellikleri, kullanıcı hesaplarını Tableau çevrimiçi güncelleştirme işlemleri eşleştirmek için kullanılır. Değişiklikleri kaydetmek için seçmeniz **Kaydet**.

    ![Tableau çevrimiçi eşleşen kullanıcı öznitelikleri](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory gruplarına Tableau**.

    ![Tableau çevrimiçi Grup eşitleme](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Tableau çevrimiçi Azure AD'den eşitlenen grup öznitelikleri gözden **öznitelik eşlemelerini** bölümü. Seçilen öznitelikler **eşleşen** özellikleri, kullanıcı hesaplarını Tableau çevrimiçi güncelleştirme işlemleri eşleştirmek için kullanılır. Değişiklikleri kaydetmek için seçmeniz **Kaydet**.

    ![Tableau çevrimiçi eşleşen grup öznitelikleri](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Kapsam belirleme filtrelerini yapılandırmak için yönergeleri izleyin. [kapsam belirleme filtresi öğretici](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Azure AD içinde sağlama hizmeti için Tableau çevrimiçi etkinleştirmeyi **ayarları** bölümünde **sağlama durumu** için **üzerinde**.

    ![Tableau çevrimiçi sağlama durumu](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Sağlama Tableau çevrimiçi olmasını istediğiniz grupları ve kullanıcıları tanımlar. İçinde **ayarları** bölümünde, istediğiniz değerleri seçin **kapsam**.

    ![Tableau Online kapsamı](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Sağlama için hazır olduğunuzda **Kaydet**.

    ![Tableau çevrimiçi Kaydet](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Bu işlem, tüm kullanıcıların ilk eşitleme başlar veya tanımlı gruplar **kapsam** içinde **ayarları** bölümü. İlk eşitleme daha sonraki eşitlemeler gerçekleştirmek için daha uzun sürer. Azure AD sağlama hizmeti çalıştırdığı sürece, yaklaşık 40 dakikada oluşur. 

Kullanabileceğiniz **eşitleme ayrıntıları** bölüm ilerlemeyi izlemek ve sağlama etkinliği raporunu için bağlantıları izleyin. Rapor sağlama Tableau Online hizmeti Azure AD tarafından gerçekleştirilen tüm eylemler açıklanmaktadır.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz: [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kullanıcı, kurumsal uygulamalar için hesabı hazırlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri gözden geçirin ve sağlama etkinliği raporları alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
