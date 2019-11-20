---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Zscaler 'ı yapılandırın | Microsoft Docs"
description: Bu öğreticide, Kullanıcı hesaplarını Zscaler 'ya otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğreneceksiniz.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0a250fcd-6ca1-47c2-a780-7a6278186a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: e254cae22e3991aebc23188b8947f0e7efd9c5f5
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515410"
---
# <a name="tutorial-configure-zscaler-two-for-automatic-user-provisioning"></a>Öğretici: Otomatik Kullanıcı sağlaması için Zscaler 'ı yapılandırma

Bu öğreticide, Kullanıcı ve/veya grupları Zscaler 'ya otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory (Azure AD) yapılandırma hakkında bilgi edineceksiniz.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti 'nde oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne olduğu ve nasıl çalıştığı hakkında önemli ayrıntılar ve sık sorulan soruların yanıtları için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen adımları tamamlayabilmeniz için şunlar gerekir:

* Azure AD kiracısı.
* Zscaler Iki kiracı.
* Yönetim izinlerine sahip Zscaler içindeki bir kullanıcı hesabı.

> [!NOTE]
> Azure AD sağlama tümleştirmesi, kurumsal hesaplar için kullanılabilen Zscaler Iki SCıM API 'sini kullanır.

## <a name="add-zscaler-two-from-the-gallery"></a>Galeriden Zscaler ekleyin

Zscaler 'ı Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmadan önce, Azure AD uygulama galerisindeki Zscaler ' ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

[Azure Portal](https://portal.azure.com)sol bölmede **Azure Active Directory**' i seçin:

![Azure Active Directory'yi seçin](common/select-azuread.png)

**Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin:

![Kurumsal uygulamalar](common/enterprise-applications.png)

Bir uygulama eklemek için pencerenin üst kısmındaki **Yeni uygulama** ' yı seçin:

![Yeni uygulama seçin](common/add-new-app.png)

Arama kutusuna **Zscaler Two**yazın. Sonuçlarda **Zscaler** öğesini seçin ve ardından **Ekle**' yi seçin.

![Sonuçlar listesi](common/search-new-app.png)

## <a name="assign-users-to-zscaler-two"></a>Kullanıcıları Zscaler 'a atama

Azure AD kullanıcılarının, bunları kullanabilmeniz için seçili uygulamalara erişim izni atanması gerekir. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanan kullanıcılar veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Zscaler 'a erişmesi gerektiğine karar vermeniz gerekir. Bu kararı verdikten sonra, [bir kurumsal uygulamaya Kullanıcı veya Grup atama](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)bölümündeki yönergeleri izleyerek bu kullanıcıları ve grupları Zscaler ' a atayabilirsiniz.

### <a name="important-tips-for-assigning-users-to-zscaler-two"></a>Kullanıcıları Zscaler 'ya atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için ilk olarak Zscaler 'ya tek bir Azure AD kullanıcısı atamanız önerilir. Daha sonra daha fazla Kullanıcı ve grup atayabilirsiniz.

* Bir kullanıcıyı Zscaler 'ya atarsanız, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-automatic-user-provisioning"></a>Otomatik Kullanıcı sağlamayı ayarlama

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve grup atamalarını temel alan Zscaler içindeki kullanıcıları ve grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için yapılandırma adımlarında kılavuzluk eder.

> [!TIP]
> Ayrıca, Zscaler için SAML tabanlı çoklu oturum açmayı etkinleştirmek isteyebilirsiniz. Bunu yaparsanız, [Zscaler iki çoklu oturum açma öğreticisindeki](zscaler-two-tutorial.md)yönergeleri izleyin. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak iki özellik birbirini tamamlayabilirler.

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Kurumsal uygulamalar** > **tüm uygulamalar** > **Zscaler**' ı seçin:

    ![Kurumsal uygulamalar](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Zscaler**' ı seçin:

    ![Uygulamalar listesi](common/all-applications.png)

3. **Sağlama** sekmesini seçin:

    ![Zscaler Iki sağlama](./media/zscaler-two-provisioning-tutorial/provisioning-tab.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın:

    ![Sağlama modunu ayarlama](./media/zscaler-two-provisioning-tutorial/provisioning-credentials.png)

5. **Yönetici kimlik bilgileri** bölümünde, bir sonraki adımda açıklandığı gibi Zscaler hesabınızın **kiracı URL 'Sini** ve **gizli belirtecini** girin.

6. **Kiracı URL 'sini** ve **gizli anahtarı**almak için, Zscaler portalında **Yönetim** > **kimlik doğrulama ayarları** ' na gidin ve **kimlik doğrulaması türü**altında **SAML** ' yi seçin:

    ![Zscaler Iki kimlik doğrulama ayarı](./media/zscaler-two-provisioning-tutorial/secret-token-1.png)

    SAML **Yapılandır penceresini açmak** Için **SAML Yapılandır** ' ı seçin:

    ![SAML penceresini Yapılandır](./media/zscaler-two-provisioning-tutorial/secret-token-2.png)

    **SCıM tabanlı sağlamayı etkinleştir** ' i seçin ve **temel URL 'Yi** ve **taşıyıcı belirtecini**kopyalayın ve ardından ayarları kaydedin. Azure portal, **temel URL** 'yi **kiracı URL 'Si** kutusuna ve **taşıyıcı belirtecini** **gizli belirteç** kutusuna yapıştırın.

7. **Kiracı URL 'si** ve **gizli belirteç** kutularına değerleri GIRDIKTEN sonra, Azure AD 'Nin Zscaler 'a bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, Zscaler hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Bağlantıyı test et](./media/zscaler-two-provisioning-tutorial/test-connection.png)

8. **Bildirim e-postası** kutusunda, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin. **Bir hata oluştuğunda e-posta bildirimi gönder**' i seçin:

    ![Bildirim e-postasını Ayarlama](./media/zscaler-two-provisioning-tutorial/notification.png)

9. **Kaydet**’i seçin.

10. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları, ZscalerTwo ile eşitler**' ı seçin:

    ![Azure AD kullanıcılarını eşitler](./media/zscaler-two-provisioning-tutorial/user-mappings.png)

11. **Öznitelik eşlemeleri** bölümünde Azure AD 'Den Zscaler ' a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Zscaler içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** ' i seçin.

    ![Öznitelik Eşlemeleri](./media/zscaler-two-provisioning-tutorial/user-attribute-mappings.png)

12. **Eşlemeler** bölümünde **Azure Active Directory gruplarını ZscalerTwo olarak eşitler**' ı seçin:

    ![Azure AD gruplarını eşitler](./media/zscaler-two-provisioning-tutorial/group-mappings.png)

13. **Öznitelik eşlemeleri** bölümünde Azure AD 'Den Zscaler 'a eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, bu iki güncelleştirme Işlemleri Için Zscaler içindeki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** ' i seçin.

    ![Öznitelik Eşlemeleri](./media/zscaler-two-provisioning-tutorial/group-attribute-mappings.png)

14. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisindeki](./../active-directory-saas-scoping-filters.md)yönergelere bakın.

15. Zscaler için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin:

    ![Sağlama Durumu](./media/zscaler-two-provisioning-tutorial/provisioning-status.png)

16. **Ayarlar** bölümünde **kapsam** altında Istediğiniz değerleri seçerek Zscaler 'a sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın:

    ![Kapsam değerleri](./media/zscaler-two-provisioning-tutorial/scoping.png)

17. Sağlamaya hazırsanız **Kaydet**' i seçin:

    ![Kaydet 'i seçin](./media/zscaler-two-provisioning-tutorial/save-provisioning.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** altında tanımlanan tüm kullanıcılar ve grupların ilk eşitlemesini başlatır. İlk eşitleme, Azure AD sağlama hizmeti çalıştığı sürece, her 40 dakikada bir oluşan sonraki eşitlerden daha uzun sürer. **Eşitleme ayrıntıları** bölümünde ilerlemeyi izleyebilirsiniz. Ayrıca, Zscaler üzerinde Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan bir sağlama etkinliği raporunun bağlantılarını izleyebilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunacağı hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-03.png
