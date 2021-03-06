---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Zscaler 'yi yapılandırma | Microsoft Docs"
description: Kullanıcı hesaplarını otomatik olarak sağlamak ve, Zscaler 'ya yeniden sağlamak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: f6725045064b74079e00ca5bbe1d560f3b19f3ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937140"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Zscaler 'ı yapılandırma

Bu öğreticide, Azure AD 'yi otomatik olarak sağlamak ve Kullanıcı ve grupları Zscaler 'ya yeniden sağlamak üzere yapılandırmak için Zscaler bir ve Azure Active Directory (Azure AD) içinde gerçekleştirme adımları gösterilmektedir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti 'nin üzerine kurulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları öğrenmek için bkz. [Azure Active Directory ile hizmet olarak yazılım (SaaS) uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../app-provisioning/user-provisioning.md).


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo şunları olduğunu varsayar:

* Azure AD kiracısı.
* Zscaler bir kiracı.
* Yönetim izinlerine sahip Zscaler içindeki bir kullanıcı hesabı.

> [!NOTE]
> Azure AD sağlama tümleştirmesi, Zscaler One SCıM API 'sini kullanır. Bu API, kurumsal pakete sahip hesaplara yönelik bir geliştirici için kullanılabilir.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Azure Marketi 'nden Zscaler ekleyin

Zscaler 'ı Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmadan önce, Azure Marketi 'nden yönetilen SaaS uygulamaları listenize Zscaler ' ı ekleyin.

Market 'ten Zscaler eklemek için aşağıdaki adımları izleyin.

1. [Azure Portal](https://portal.azure.com)sol taraftaki gezinti bölmesinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory simgesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için iletişim kutusunun üst kısmındaki **Yeni uygulama** ' yı seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Zscaler One** girin ve sonuç panelinden **Zscaler** ' ı seçin. Uygulamayı eklemek için **Ekle**' yi seçin.

    ![Sonuç listesinde Zscaler](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Kullanıcıları Zscaler 'a atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanan kullanıcılar veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların veya grupların Zscaler 'ya erişmesi gerektiğini belirleyin. Bu kullanıcıları veya grupları Zscaler 'ya atamak için, [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)' daki yönergeleri izleyin.

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Kullanıcıları Zscaler 'ya atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için Zscaler 'ya tek bir Azure AD kullanıcısı atamanız önerilir. Daha sonra ek kullanıcı veya grup atayabilirsiniz.

* Bir kullanıcıyı Zscaler 'ya atadığınızda atama iletişim kutusunda varsa uygulamaya özgü geçerli herhangi bir rolü seçin. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Otomatik Kullanıcı sağlamasını Zscaler 'ya yapılandırma

Bu bölüm, Azure AD sağlama hizmetini yapılandırma adımlarında size rehberlik eder. Azure AD 'de Kullanıcı veya grup atamalarını temel alan Zscaler 'da kullanıcıları veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için kullanın.

> [!TIP]
> Ayrıca, Zscaler için SAML tabanlı çoklu oturum açmayı etkinleştirebilirsiniz. [Zscaler tek bir oturum açma öğreticisindeki](zscaler-One-tutorial.md)yönergeleri izleyin. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini tamamlayabilse de.

> [!NOTE]
> Kullanıcılar ve gruplar sağlandığında veya sağlanmak istendiğinde, grup üyeliklerinin düzgün şekilde güncelleştirildiğinden emin olmak için düzenli aralıklarla sağlamayı yeniden başlatmanızı öneririz. Yeniden başlatma işlemi yapıldığında, hizmetimizi tüm grupları yeniden değerlendirmeye ve üyelikleri güncelleştirmeye zorlar.  

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Azure AD 'de Zscaler için otomatik Kullanıcı sağlamayı yapılandırma

1. [Azure portalında](https://portal.azure.com) oturum açın. **Kurumsal uygulamalar**  >  **tüm uygulamalar**  >  **Zscaler**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Zscaler One** öğesini seçin.

    ![Uygulamalar listesinde Zscaler bir bağlantı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Zscaler bir sağlama](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Zscaler bir sağlama modu](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. **Yönetici kimlik bilgileri** bölümü altında, **kiracı URL 'si** ve gizli dizi **belirteci** kutularını, 6. adımda anlatıldığı gibi Zscaler hesabınız için ayarlarla birlikte girin.

6. Kiracı URL 'sini ve gizli anahtarı almak için,   >  tek bir Portal Kullanıcı arabirimindeki Yönetim **kimlik doğrulaması ayarları** ' na gidin. **Kimlik doğrulama türü** altında **SAML**' yi seçin.

    ![Zscaler bir kimlik doğrulama ayarları](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. **SAML Yapılandır seçeneklerini açmak** Için **SAML Yapılandır** ' ı seçin.

    ![Zscaler bir SAML Yapılandır](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. **Temel URL** ve **taşıyıcı belirteçteki** ayarları almak için **SCIM-Based sağlamayı etkinleştir** ' i seçin. Sonra ayarları kaydedin. **Taban URL** ayarını Azure Portal **kiracı URL** 'sine kopyalayın. **Taşıyıcı belirteç** ayarını Azure Portal **gizli belirteç** olarak kopyalayın.

7. 5. adımda gösterilen kutuları doldurduktan sonra, Azure AD 'nin Zscaler 'a bağlanabildiğine emin olmak için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, Zscaler hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Zscaler bir test bağlantısı](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. **Bildirim e-postası** kutusunda, sağlama hatası bildirimlerini alacak kişinin veya grubun e-posta adresini girin. **Bir hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Zscaler bir bildirim e-postası](./media/zscaler-one-provisioning-tutorial/notification.png)

9. **Kaydet**’i seçin.

10. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Zscaler ile eşitler**' ı seçin.

    ![Zscaler bir Kullanıcı eşitlemesi](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. **Öznitelik eşlemeleri** bölümünde Azure AD 'Den Zscaler 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, bir güncelleştirme Işlemleri Için Zscaler içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri kaydetmek için **Kaydet**' i seçin.

    ![Zscaler eşleşen bir kullanıcı özniteliği](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. **Eşlemeler** bölümünde **Azure Active Directory gruplarını Zscaler ile eşitler**' ı seçin.

    ![Zscaler bir grup eşitleme](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. **Öznitelik eşlemeleri** bölümünde Azure AD 'Den Zscaler 'a eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, tek bir güncelleştirme Işlemleri Için Zscaler içindeki grupları eşleştirmek için kullanılır. Değişiklikleri kaydetmek için **Kaydet**' i seçin.

    ![Zscaler bir eşleşen Grup özniteliği](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Kapsam filtrelerini yapılandırmak için [kapsam filtresi öğreticisindeki](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)yönergeleri izleyin.

15. Zscaler için Azure AD sağlama hizmetini etkinleştirmek üzere, **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Zscaler bir sağlama durumu](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Zscaler 'a sağlamak istediğiniz kullanıcıları veya grupları tanımlayın. **Ayarlar** bölümünde, **kapsam** içinde istediğiniz değerleri seçin.

    ![Zscaler bir kapsam](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Sağlamaya hazırsanız **Kaydet**' i seçin.

    ![Zscaler One kaydetme](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Bu işlem, **Ayarlar** bölümünde **kapsamda** tanımlanan tüm kullanıcıların veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra eşitlenmesi daha uzun sürer. Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir gerçekleşir. 

İlerleme durumunu izlemek ve sağlama etkinliği raporunun bağlantılarını izlemek için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz. Rapor, Zscaler üzerinde Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklar.

Azure AD sağlama günlüklerini okuma hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlama hakkında raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png