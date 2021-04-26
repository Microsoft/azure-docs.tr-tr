---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Soloinsight-CloudGate SSO yapılandırma | Microsoft Docs'
description: Soloinsight-CloudGate SSO 'ya Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 5c42102c0704d7212411d6b86f4210ad8ecd885c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96347523"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Soloinsight-CloudGate SSO 'yu yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları Soloinsight-CloudGate SSO 'ya otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Soloinsight-CloudGate SSO ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Soloinsight-CloudGate SSO kiracısı](https://www.soloinsight.com/)
* Soloinsight-CloudGate SSO 'da yönetici izinleriyle bir kullanıcı hesabı.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Soloinsight-CloudGate SSO 'ya Kullanıcı atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Soloinsight-CloudGate SSO 'ya erişmesi gerektiğini belirlemeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Soloinsight-CloudGate SSO 'ya atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Soloinsight-CloudGate SSO 'ya Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için Soloinsight-CloudGate SSO 'ya tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Soloinsight-CloudGate SSO 'ya atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Sağlama için Soloinsight-CloudGate SSO 'yu ayarlama

1. [Soloinsight-CloudGate SSO Yönetici konsolunuza](https://soloinsight.sigateway.com/login)oturum açın. **Yönetim > sistem ayarları**' na gidin.

    ![Soloinsight-CloudGate SSO Yönetici Konsolu](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  **Genel**' e gidin.

    ![Soloinsight-CloudGate SSO SCıM Ekle](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  **Kiracı URL 'si** ve **gizli dizi belirtecini** almak için sayfanın sonuna kadar aşağı doğru kaydırın. Gizli dizi **belirtecini** kopyalayın. Bu değer, Azure portal Soloinsight-CloudGate SSO uygulamanızın sağlama sekmesindeki gizli belirteç alanına girilir.

    ![Soloinsight-CloudGate SSO oluşturma belirteci](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Galeriden Soloinsight-CloudGate SSO 'SU ekleme

Azure AD ile otomatik Kullanıcı sağlama için Soloinsight-CloudGate SSO 'yu yapılandırmadan önce Azure AD uygulama galerisinden yönetilen SaaS uygulamaları listenize Soloinsight-CloudGate SSO eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden Soloinsight-CloudGate SSO eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Soloinsight-CLOUDGATE SSO** yazın, sonuçlar panelinde **Soloinsight-cloudgate SSO** ' yı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuç listesinde SSO Soloinsight-CloudGate](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Soloinsight-CloudGate SSO 'ya otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde, Azure AD 'de Kullanıcı ve/veya grup atamalarına göre Soloinsight-CloudGate SSO 'daki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD sağlama hizmetini yapılandırma adımlarında size kılavuzluk eder.

> [!TIP]
> [Soloinsight-CloudGate SSO çoklu oturum açma öğreticisinde](./soloinsight-cloudgate-sso-tutorial.md)sunulan yönergeleri IZLEYEREK Soloinsight-CloudGate SSO için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilse de

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Azure AD 'de Soloinsight-CloudGate SSO için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Soloinsight-CloudGate SSO**' yı seçin.

    ![Uygulamalar listesindeki Soloinsight-CloudGate SSO bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, `https://sigateway.com/scim/v2/sync/serviceproviderconfig` **kiracı URL 'sini** girin. **Gizli belirteçte** daha önce alınan **SCIM kimlik doğrulama belirteci** değerini girin. Azure AD 'nin Soloinsight-CloudGate SSO 'ya bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Soloinsight-CloudGate SSO hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları SSO Soloinsight-CloudGate olarak eşitler**' ı seçin.

    ![Soloinsight-CloudGate SSO Kullanıcı eşlemeleri](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. **Öznitelik eşleme** bölümünde Azure AD 'den Soloinsight-CloudGate SSO 'ya eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme işlemleri IÇIN Soloinsight-CloudGate SSO 'daki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Soloinsight-CloudGate SSO Kullanıcı öznitelikleri](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. **Eşlemeler** bölümünde **Azure ACTIVE DIRECTORY gruplarını Soloinsight-CloudGate SSO ile eşitler**' ı seçin.

    ![Soloinsight-CloudGate SSO grubu eşlemeleri](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. **Öznitelik eşleme** bölümünde Azure AD 'den Soloinsight-CloudGate SSO 'ya eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme işlemleri IÇIN Soloinsight-CloudGate SSO 'daki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Soloinsight-CloudGate SSO grubu öznitelikleri](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. Soloinsight-CloudGate SSO için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsamda** Istenen değerleri seçerek Soloinsight-CloudGate SSO 'ya sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerlemeyi izlemek ve Soloinsight-CloudGate SSO üzerinde Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinliği raporuna yönelik bağlantıları izlemek için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)