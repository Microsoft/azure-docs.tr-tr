---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Zscaler Zscyüksek 'i yapılandırın | Microsoft Docs"
description: Bu öğreticide, Zscaler Zscyüksek 'ya Kullanıcı hesaplarını otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğreneceksiniz.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: a752be80-d3ef-45d1-ac8f-4fb814c07b07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 34d3a7fb299ba143eee01b6b7184f1c566d41aba
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515451"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Öğretici: Otomatik Kullanıcı sağlaması için Zscaler Zscyüksek 'yi yapılandırma

Bu öğreticide, Kullanıcı ve/veya grupları Zscaler Zscm 'ye otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory (Azure AD) yapılandırma hakkında bilgi edineceksiniz.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti 'nde oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne olduğu ve nasıl çalıştığı hakkında önemli ayrıntılar ve sık sorulan soruların yanıtları için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../active-directory-saas-app-provisioning.md).


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen adımları tamamlayabilmeniz için şunlar gerekir:

* Azure AD kiracısı.
* Zscaler Zscyüksek kiracısı.
* Yönetim izinlerine sahip Zscaler Zscyüksek içindeki bir kullanıcı hesabı.

> [!NOTE]
> Azure AD sağlama tümleştirmesi, kurumsal hesaplar için kullanılabilen Zscaler Zscsesli SCıM API 'sini kullanır.

## <a name="add-zscaler-zscloud-from-the-gallery"></a>Galeriden Zscaler Zscyüksek 'i ekleme

Zscaler Zscı 'yi Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmadan önce, Azure AD uygulama galerisindeki Zscaler Zscı 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

[Azure Portal](https://portal.azure.com)sol bölmede **Azure Active Directory**' i seçin:

![Azure Active Directory'yi seçin](common/select-azuread.png)

**Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin:

![Kurumsal uygulamalar](common/enterprise-applications.png)

Bir uygulama eklemek için pencerenin üst kısmındaki **Yeni uygulama** ' yı seçin:

![Yeni uygulama seçin](common/add-new-app.png)

Arama kutusuna **Zscaler Zscyüksek**yazın. Sonuçlarda **Zscaler Zscyüksek** ' i seçin ve ardından **Ekle**' yi seçin.

![Sonuçlar listesi](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Zscaler Zscyüksek 'e Kullanıcı atama

Azure AD kullanıcılarının, bunları kullanabilmeniz için seçili uygulamalara erişim izni atanması gerekir. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanan kullanıcılar veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Zscaler Zscyüksek 'e erişmesi gerektiğine karar vermeniz gerekir. Bu şekilde karar verdikten sonra, [bir kurumsal uygulamaya Kullanıcı veya Grup atama](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)bölümündeki yönergeleri izleyerek bu kullanıcıları ve grupları Zscaler Zscyüksek 'e atayabilirsiniz.

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Zscaler Zscyüksek 'e Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için ilk olarak Zscaler Zscyüksek 'e tek bir Azure AD kullanıcısı atamanız önerilir. Daha sonra daha fazla Kullanıcı ve grup atayabilirsiniz.

* Zscaler Zscı 'ye bir Kullanıcı atadığınızda, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-automatic-user-provisioning"></a>Otomatik Kullanıcı sağlamayı ayarlama

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve grup atamalarına göre Zscaler Zscı içindeki kullanıcıları ve grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında kılavuzluk eder.

> [!TIP]
> Ayrıca, Zscaler Zscyüksek için SAML tabanlı çoklu oturum açmayı etkinleştirmek isteyebilirsiniz. Bunu yaparsanız, [Zscaler Zscyüksek çoklu oturum açma öğreticisindeki](zscaler-zsCloud-tutorial.md)yönergeleri izleyin. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak iki özellik birbirini tamamlayabilirler.

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Kurumsal uygulamalar** > **tüm uygulamalar** > **Zscaler zscyüksek**' i seçin:

    ![Kurumsal uygulamalar](common/enterprise-applications.png)

2. Uygulamalar listesinde **Zscaler Zscyüksek**' i seçin:

    ![Uygulamalar listesi](common/all-applications.png)

3. **Sağlama** sekmesini seçin:

    ![Zscaler Zscyüksek sağlama](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın:

    ![Sağlama modunu ayarlama](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. **Yönetici kimlik bilgileri** bölümünde, bir sonraki adımda açıklandığı gibi Zscaler Zscı hesabınızın **kiracı URL 'Sini** ve **gizli belirtecini** girin.

6. **Kiracı URL 'si** ve gizli dizi **belirtecini**almak için Zscaler zscı portalındaki **Yönetim** > **kimlik doğrulama ayarları** ' na gidin ve **kimlik doğrulaması türü**altında **SAML** ' yi seçin:

    ![Zscaler Zscyüksek kimlik doğrulama ayarları](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    SAML **Yapılandır penceresini açmak** Için **SAML Yapılandır** ' ı seçin:

    ![SAML penceresini Yapılandır](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    **SCıM tabanlı sağlamayı etkinleştir** ' i seçin ve **temel URL 'Yi** ve **taşıyıcı belirtecini**kopyalayın ve ardından ayarları kaydedin. Azure portal, **temel URL** 'yi **kiracı URL 'Si** kutusuna ve **taşıyıcı belirtecini** **gizli belirteç** kutusuna yapıştırın.

7. **Kiracı URL 'si** ve **gizli dizi belirteci** kutularına değerleri GIRDIKTEN sonra, Azure AD 'Nin Zscaler zscyüksek 'e bağlanabildiğinizden emin olmak için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, Zscaler Zscı hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Bağlantıyı test et](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. **Bildirim e-postası** kutusunda, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin. **Bir hata oluştuğunda e-posta bildirimi gönder**' i seçin:

    ![Bildirim e-postasını Ayarlama](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. **Kaydet**’i seçin.

10. **Eşlemeler** bölümünde, **Kullanıcı Azure Active Directory Kullanıcıları Zscalerzscı olarak eşitler**' ı seçin:

    ![Azure AD kullanıcılarını eşitler](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. **Öznitelik eşlemeleri** bölümünde, Azure AD 'Den Zscaler Zscyüksek ile eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Zscaler Zscyüksek içindeki kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** ' i seçin.

    ![Öznitelik Eşlemeleri](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. **Eşlemeler** bölümünde **Azure Active Directory gruplarını Zscalerzscı olarak eşitler**' ı seçin:

    ![Azure AD gruplarını eşitler](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. **Öznitelik eşlemeleri** bölümünde, Azure AD 'Den Zscaler Zscyüksek ile eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Zscaler Zscyüksek içindeki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** ' i seçin.

    ![Öznitelik Eşlemeleri](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisindeki](./../active-directory-saas-scoping-filters.md)yönergelere bakın.

15. Zscaler Zscyüksek için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin:

    ![Sağlama Durumu](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. **Ayarlar** bölümünde **kapsam** altında Istediğiniz değerleri seçerek Zscaler zscyüksek 'e sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın:

    ![Kapsam değerleri](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Sağlamaya hazırsanız **Kaydet**' i seçin:

    ![Kaydet 'i seçin](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** altında tanımlanan tüm kullanıcılar ve grupların ilk eşitlemesini başlatır. İlk eşitleme, Azure AD sağlama hizmeti çalıştığı sürece, her 40 dakikada bir oluşan sonraki eşitlerden daha uzun sürer. **Eşitleme ayrıntıları** bölümünde ilerlemeyi izleyebilirsiniz. Ayrıca, Zscaler Zscyüksek üzerinde Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan bir sağlama etkinliği raporunun bağlantılarını izleyebilirsiniz.

Azure AD sağlama günlüklerini okuma hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlama hakkında raporlama](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png
