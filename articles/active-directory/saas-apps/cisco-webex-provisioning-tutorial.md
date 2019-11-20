---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı hazırlama için Cisco WebEx yapılandırma | Microsoft Docs'
description: Cisco WebEx 'e Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: f4089d690602aa761b1942bd3d538e2ef305aa19
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73570386"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Cisco WebEx 'ı yapılandırma

Bu öğreticinin amacı Cisco WebEx ve Azure Active Directory (Azure AD) ' de gerçekleştirilecek adımları göstermek için Azure AD 'yi otomatik olarak kullanıcıları Cisco WebEx 'a sağlamak ve sağlamak üzere yapılandırır.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı Şu anda önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* [Cisco WebEx kiracısı](https://www.webex.com/pricing/index.html).
* Cisco WebEx içindeki yönetici izinleriyle bir kullanıcı hesabı.

## <a name="adding-cisco-webex-from-the-gallery"></a>Galeriden Cisco WebEx ekleme

Azure AD ile otomatik Kullanıcı sağlama için Cisco WebEx 'ı yapılandırmadan önce, Azure AD uygulama galerisinden yönetilen SaaS uygulamaları listenize Cisco WebEx eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden Cisco WebEx eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Cisco WebEx**yazın, sonuç panelinden **Cisco WebEx** ' ı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

    ![Sonuç listesinde Cisco WebEx](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Cisco WebEx 'e Kullanıcı atama

Azure Active Directory, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyebilmek için "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya "atanmış" olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların Cisco WebEx 'e erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları Cisco WebEx 'e atayabilirsiniz:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Cisco WebEx 'e Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için Cisco WebEx 'e tek bir Azure AD kullanıcısının atanması önerilir. Daha sonra ek kullanıcılar atanabilir.

* Cisco WebEx 'e Kullanıcı atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Cisco WebEx için otomatik Kullanıcı sağlamayı yapılandırma

Bu bölüm, Azure AD sağlama hizmetini Azure AD 'de kullanıcı atamalarını temel alarak Cisco WebEx içindeki kullanıcıları oluşturmak, güncelleştirmek ve devre dışı bırakmak için yapılandırmanıza yardımcı olacak adımlarda size rehberlik eder.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Azure AD 'de Cisco WebEx için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve **Cisco WebEx**' yi seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Cisco WebEx**öğesini seçin.

    ![Uygulamalar listesindeki Cisco WebEx bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Cisco WebEx sağlama](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Cisco WebEx sağlama](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümü altında, **kiracı URL 'Sini**ve Cisco WebEx hesabınızın **gizli belirtecini** girin.

    ![Cisco WebEx sağlama](./media/cisco-webex-provisioning-tutorial/secrettoken1.png)

6.  **Kiracı URL 'si** alanında, `https://api.ciscoweb.com/v1/scim/[OrgId]`biçiminde bir değer girin. `[OrgId]`edinmek için [Cisco WebEx denetim hub 'ında](https://admin.webex.com/login)oturum açın. Sol alt kısımdaki kuruluş adına tıklayın ve değeri **kuruluş kimliği**' nden kopyalayın. 

    * **Gizli belirteç**değerini almak Için bu [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose)'ye gidin. Görüntülenen WebEx oturum açma sayfasında, kuruluşunuzun tam Cisco WebEx yönetici hesabıyla oturum açın. Siteye ulaşılamadığını gösteren bir hata sayfası görüntülenir, ancak bu normaldir.

        ![Cisco WebEx sağlama](./media/cisco-webex-provisioning-tutorial/test.png)
 
    * Oluşturulan taşıyıcı belirtecinin değerini, URL 'den aşağıda vurgulanan şekilde kopyalayın. Bu belirteç 365 gün için geçerlidir.
        
        ![Cisco WebEx sağlama](./media/cisco-webex-provisioning-tutorial/test1.png)

7. 5\. adımda gösterilen alanları doldurarak Azure AD 'nin Cisco WebEx 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Cisco WebEx hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)
   
8. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

9. **Kaydet** düğmesine tıklayın.

10. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Cisco WebEx olarak eşitler**' ı seçin.

    ![Cisco WebEx sağlama](./media/cisco-webex-provisioning-tutorial/usermapping.png)

11. **Öznitelik eşleme** bölümünde Azure AD 'Den Cisco WebEx 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Cisco WebEx içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Cisco WebEx sağlama](./media/cisco-webex-provisioning-tutorial/usermappingattributes.png)

12. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

13. Cisco WebEx için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsamda** Istenen değerleri seçerek Cisco WebEx 'e sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

15. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. **Eşitleme ayrıntıları** bölümünü kullanarak, Cisco WebEx ÜZERINDE Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, ilerlemeyi izleyebilir ve sağlama etkinliği raporuna yönelik bağlantıları izleyebilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Cisco WebEx Şu anda Cisco 'nun erken alan testi (EFT) aşamasındadır. Daha fazla bilgi için lütfen [Cisco 'nun destek ekibine](https://www.webex.co.in/support/support-overview.html)başvurun. 
* Cisco WebEx yapılandırması hakkında daha fazla bilgi için [buradaki](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub)Cisco belgelerine bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)