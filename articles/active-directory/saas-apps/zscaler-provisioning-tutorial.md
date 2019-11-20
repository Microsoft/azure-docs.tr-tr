---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Zscaler yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını Zscaler 'ya otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: d88aae8887cbfd872a8d3e82fb2166043c312e5a
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515406"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Öğretici: Otomatik Kullanıcı sağlaması için Zscaler 'ı yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları Zscaler 'ya otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Zscaler ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../active-directory-saas-app-provisioning.md).
>

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, zaten aşağıdakilere sahip olduğunuzu varsayar:

* Azure AD kiracısı.
* Zscaler kiracısı.
* Yönetim izinlerine sahip Zscaler 'da bir kullanıcı hesabı.

> [!NOTE]
> Azure AD sağlama tümleştirmesi, Zscaler geliştiricileri tarafından kurumsal pakete sahip hesaplar için kullanılabilen Zscaler SCıM API 'sini kullanır.

## <a name="adding-zscaler-from-the-gallery"></a>Galeriden Zscaler ekleme

Azure AD ile otomatik Kullanıcı sağlama için Zscaler 'ı yapılandırmadan önce Azure AD uygulama galerisindeki Zscaler 'ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD uygulama galerisinden Zscaler eklemek için aşağıdaki adımları uygulayın:**

1. İçinde **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde tıklayın **Azure Active Directory** simgesi.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için tıklatın **yeni uygulama** iletişim üst kısmındaki düğmesi.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Zscaler**yazın, sonuç panelinden **Zscaler** ' ı seçin, sonra da uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde Zscaler](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Kullanıcıları Zscaler 'a atama

Azure Active Directory, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyebilmek için "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya "atanmış" olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Zscaler 'a erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Zscaler 'a atayabilirsiniz:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Zscaler 'ya Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için, Zscaler 'a tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Zscaler 'ya bir Kullanıcı atarken atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Zscaler 'ya otomatik Kullanıcı sağlamayı yapılandırma

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan Zscaler içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında size kılavuzluk eder.

> [!TIP]
> Zscaler [Çoklu oturum](zscaler-tutorial.md)açma öğreticisinde belirtilen yönergeleri Izleyerek Zscaler için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Azure AD 'de Zscaler için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Zscaler**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Zscaler**' ı seçin.

    ![Uygulamalar listesindeki Zscaler bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Zscaler sağlama](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Zscaler sağlama](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. **Yönetici kimlik bilgileri** bölümünde, 6. adımda açıklandığı gibi Zscaler hesabınızın **kiracı URL 'Sini** ve **gizli belirtecini** girin.

6. **Kiracı URL 'sini** ve **gizli anahtarı**almak Için, Zscaler portal Kullanıcı arabirimindeki **Yönetim > kimlik doğrulama ayarları** ' na gidin ve **kimlik doğrulaması türü**altında **SAML** ' ye tıklayın.

    ![Zscaler sağlama](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    **Yapılandırma SAML** seçeneklerini açmak Için **SAML** yapılandırması ' na tıklayın.

    ![Zscaler sağlama](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    **Temel URL** ve **taşıyıcı belirtecini**almak Için **SCIM tabanlı sağlamayı etkinleştir** ' i seçin, sonra ayarları kaydedin. **Temel URL** 'YI **kiracı URL**'sine ve **taşıyıcı belirtecini** Azure Portal **gizli belirtece** kopyalayın.

7. Adım 5 ' te gösterilen alanlar doldurulduğdan sonra Azure AD 'nin Zscaler 'a bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Zscaler hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Zscaler sağlama](./media/zscaler-provisioning-tutorial/test-connection.png)

8. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin ve **bir hata oluştuğunda e-posta bildirimi gönder**onay kutusunu işaretleyin.

    ![Zscaler sağlama](./media/zscaler-provisioning-tutorial/notification.png)

9. **Kaydet**’e tıklayın.

10. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Zscaler ile eşitler**' ı seçin.

    ![Zscaler sağlama](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. **Öznitelik eşleme** bölümünde Azure AD 'Den Zscaler 'dan eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Zscaler içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Zscaler sağlama](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. **Eşlemeler** bölümünde **Azure Active Directory gruplarını Zscaler olarak eşitler**' ı seçin.

    ![Zscaler sağlama](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. **Öznitelik eşleme** bölümünde Azure AD 'Den Zscaler 'dan eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, bu güncelleştirme Işlemleri Için Zscaler içindeki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Zscaler sağlama](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](./../active-directory-saas-scoping-filters.md)sunulan aşağıdaki yönergelere bakın.

15. Zscaler için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Zscaler sağlama](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. **Ayarlar** bölümünde **kapsam** Içinde Istenen değerleri seçerek Zscaler 'a sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Zscaler sağlama](./media/zscaler-provisioning-tutorial/scoping.png)

17. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Zscaler sağlama](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. **Eşitleme ayrıntıları** bölümünü Izleyip, Zscaler ÜZERINDE Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, ilerlemeyi izleyebilir ve sağlama etkinliği raporuna yönelik bağlantıları izleyebilirsiniz.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
