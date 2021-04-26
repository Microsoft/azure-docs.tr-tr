---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Zscaler üçünü yapılandırma | Microsoft Docs'
description: Bu öğreticide, Kullanıcı hesaplarını Zscaler 'ya otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğreneceksiniz.
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
ms.openlocfilehash: c16c02a870edb1a777b63da6fea57fc02136d643
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936609"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlama için Zscaler üçünü yapılandırma

Bu öğreticide, kullanıcıları ve/veya grupları Zscaler 'ya otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory (Azure AD) yapılandırma hakkında bilgi edineceksiniz.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti 'nde oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne olduğu ve nasıl çalıştığı hakkında önemli ayrıntılar ve sık sorulan soruların yanıtları için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen adımları tamamlayabilmeniz için şunlar gerekir:

* Azure AD kiracısı.
* Zscaler üç kiracı.
* Yönetim izinlerine sahip Zscaler içindeki bir kullanıcı hesabı.

> [!NOTE]
> Azure AD sağlama tümleştirmesi, kurumsal hesaplar için kullanılabilen Zscaler Zscsesli SCıM API 'sini kullanır.

## <a name="adding-zscaler-three-from-the-gallery"></a>Galeriden Zscaler üç ekleme

Zscaler 'ı Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmadan önce, Azure AD uygulama galerisindeki Zscaler ' ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

[Azure Portal](https://portal.azure.com)sol bölmede **Azure Active Directory**' i seçin:

![Azure Active Directory'yi seçin](common/select-azuread.png)

**Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin:

![Kurumsal uygulamalar](common/enterprise-applications.png)

Bir uygulama eklemek için pencerenin üst kısmındaki **Yeni uygulama** ' yı seçin:

![Yeni uygulama seçin](common/add-new-app.png)

Arama kutusuna **Zscaler Three** yazın. Sonuçlarda **Zscaler** ' ı seçin ve ardından **Ekle**' yi seçin.

![Sonuçlar listesi](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Kullanıcıları Zscaler 'a ata

Azure AD kullanıcılarının, bunları kullanabilmeniz için seçili uygulamalara erişim izni atanması gerekir. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanan kullanıcılar veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Zscaler 'nun üç erişimine ihtiyacı olduğuna karar vermeniz gerekir. Bu kararı verdikten sonra, [bir kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)bölümündeki yönergeleri izleyerek bu kullanıcıları ve grupları Zscaler ' a atayabilirsiniz.

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Zscaler 'a Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için ilk olarak Zscaler 'ya tek bir Azure AD kullanıcısı atamanız önerilir. Daha sonra daha fazla Kullanıcı ve grup atayabilirsiniz.

* Zscaler ' a bir Kullanıcı atadığınızda, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-automatic-user-provisioning"></a>Otomatik Kullanıcı sağlamayı ayarlama

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve grup atamalarını temel alan Zscaler içindeki kullanıcıları ve grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında kılavuzluk eder.

> [!TIP]
> Ayrıca, Zscaler için SAML tabanlı çoklu oturum açmayı etkinleştirmek isteyebilirsiniz. Bunu yaparsanız, [Zscaler üç çoklu oturum açma öğreticisindeki](zscaler-three-tutorial.md)yönergeleri izleyin. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak iki özellik birbirini tamamlayabilirler.

> [!NOTE]
> Kullanıcılar ve gruplar sağlandığında veya sağlanmak istendiğinde, grup üyeliklerinin düzgün şekilde güncelleştirildiğinden emin olmak için düzenli aralıklarla sağlamayı yeniden başlatmanızı öneririz. Yeniden başlatma işlemi yapıldığında, hizmetimizi tüm grupları yeniden değerlendirmeye ve üyelikleri güncelleştirmeye zorlar. 

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Kurumsal uygulamalar**  >  **tüm uygulamalar**  >  **Zscaler üç**' u seçin:

    ![Kurumsal uygulamalar](common/enterprise-applications.png)

2. Uygulamalar listesinde **Zscaler üç**' u seçin:

    ![Uygulamalar listesi](common/all-applications.png)

3. **Sağlama** sekmesini seçin:

    ![Zscaler üç sağlama](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. **Sağlama modunu** **Otomatik** olarak ayarlayın:

    ![Sağlama modunu ayarlama](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. **Yönetici kimlik bilgileri** bölümünde, bir sonraki adımda açıklandığı gibi Zscaler hesabınızın **kiracı URL 'Sini** ve **gizli belirtecini** girin.

6. **Kiracı URL 'sini** ve **gizli anahtarı** almak için,   >  Zscaler portalında Yönetim **kimlik doğrulama ayarları** ' na gidin ve **kimlik doğrulaması türü** altında **SAML** ' yi seçin:

    ![Zscaler üç kimlik doğrulama ayarı](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    **SAML Yapılandır penceresini açmak** Için **SAML Yapılandır** ' ı seçin:

    ![SAML penceresini Yapılandır](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    **Sağlamayı SCIM-Based etkinleştir** ' i seçin ve **temel URL** 'yi ve **taşıyıcı belirtecini** kopyalayın ve ardından ayarları kaydedin. Azure portal, **temel URL** 'yi **kiracı URL 'Si** kutusuna ve **taşıyıcı belirtecini** **gizli belirteç** kutusuna yapıştırın.

7. **Kiracı URL 'si** ve **gizli dizi belirteci** kutularına değerleri GIRDIKTEN sonra, Azure AD 'Nin Zscaler 'a bağlanabildiğinizden emin olmak için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, Zscaler hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Bağlantıyı test etme](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. **Bildirim e-postası** kutusunda, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin. **Bir hata oluştuğunda e-posta bildirimi gönder**' i seçin:

    ![Bildirim e-postasını Ayarlama](./media/zscaler-three-provisioning-tutorial/notification.png)

9. **Kaydet**’i seçin.

10. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları ZscalerThree ile eşitler**' ı seçin:

    ![Azure AD kullanıcılarını eşitler](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. **Öznitelik eşlemeleri** bölümünde Azure AD 'Den Zscaler ' a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Zscaler 'daki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** ' i seçin.

    ![Yedi eşleşme görüntülenirken öznitelik eşlemeleri bölümünün ekran görüntüsü.](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. **Eşlemeler** bölümünde **Azure Active Directory gruplarını ZscalerThree olarak eşitler**' ı seçin:

    ![Azure AD gruplarını eşitler](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. **Öznitelik eşlemeleri** bölümünde Azure AD 'Den Zscaler ' a eşitlenen grup özniteliklerini inceleyin. **Eşleşen** özellikler olarak seçilen öznitelikler, Zscaler 'daki grupları güncelleştirme işlemleri için eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** ' i seçin.

    ![Üç eşleme görüntülenirken öznitelik eşlemeleri bölümünün ekran görüntüsü.](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisindeki](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)yönergelere bakın.

15. Zscaler için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin:

    ![Sağlama durumu](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. **Ayarlar** bölümünde **kapsam** altında Istediğiniz değerleri seçerek Zscaler 'a sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın:

    ![Kapsam değerleri](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. Sağlamaya hazırsanız **Kaydet**' i seçin:

    ![Kaydet'i seçme](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** altında tanımlanan tüm kullanıcılar ve grupların ilk eşitlemesini başlatır. İlk eşitleme, Azure AD sağlama hizmeti çalıştığı sürece, her 40 dakikada bir oluşan sonraki eşitlerden daha uzun sürer. **Eşitleme ayrıntıları** bölümünde ilerlemeyi izleyebilirsiniz. Ayrıca, Zscaler üzerinde Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan bir sağlama etkinliği raporunun bağlantılarını izleyebilirsiniz.

Azure AD sağlama günlüklerini okuma hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlama hakkında raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png