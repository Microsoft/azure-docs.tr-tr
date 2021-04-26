---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı hazırlama için Cisco WebEx yapılandırma | Microsoft Docs'
description: Cisco WebEx 'e Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: bc05e83ac6c7f0f7c5e9a571c1fa7397af858f44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180133"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Cisco WebEx 'ı yapılandırma

Bu öğreticinin amacı Cisco WebEx ve Azure Active Directory (Azure AD) ' de gerçekleştirilecek adımları göstermek için Azure AD 'yi otomatik olarak kullanıcıları Cisco WebEx 'a sağlamak ve sağlamak üzere yapılandırır.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

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

4. Arama kutusuna **Cisco WebEx** yazın, sonuç panelinden **Cisco WebEx** ' ı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

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

2. Uygulamalar listesinde **Cisco WebEx** öğesini seçin.

    ![Uygulamalar listesindeki Cisco WebEx bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    :::image type="content" source="common/provisioning.png" alt-text="Azure portal bir menünün ekran görüntüsü. Yönet ' in altında sağlama vurgulanır." border="false":::

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    :::image type="content" source="common/provisioning-automatic.png" alt-text="Otomatik vurgulanmış şekilde sağlama modu liste kutusunun ekran görüntüsü." border="false":::

5. **Yönetici kimlik bilgileri** bölümü altında, **kiracı URL 'Sini** ve Cisco WebEx hesabınızın **gizli belirtecini** girin.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/secrettoken1.png" alt-text="Yönetici kimlik bilgileri bölümünün ekran görüntüsü. Kiracı U R L ve gizli belirteç kutuları vurgulanır ancak boştur." border="false":::

6.  **Kiracı URL 'si** alanında, biçiminde bir değer girin `https://api.ciscospark.com/v1/scim/[OrgId]` . `[OrgId]` [Cisco WebEx denetim hub](https://admin.webex.com/login)'ınızı almak için oturum açın. Sol alt kısımdaki kuruluş adına tıklayın ve değeri **kuruluş kimliği**' nden kopyalayın. 

    * **Gizli belirteç** değerini almak Için bu [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%2f%2flocalhost%253A3000%2fauth%2fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose)'ye gidin. Görüntülenen WebEx oturum açma sayfasında, kuruluşunuzun tam Cisco WebEx yönetici hesabıyla oturum açın. Siteye ulaşılamadığını gösteren bir hata sayfası görüntülenir, ancak bu normaldir.

        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test.png" alt-text="Hata iletisi görüntüleyen bir Web sayfasının ekran görüntüsü. İleti, siteye ulaşılamadığından ve birkaç sorun giderme ipucu içerdiğini belirtir." border="false":::
 
    * Oluşturulan taşıyıcı belirtecinin değerini, URL 'den aşağıda vurgulanan şekilde kopyalayın. Bu belirteç 365 gün için geçerlidir.
        
        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test1.png" alt-text="Uzun U R L 'yi gösteren ekran görüntüsü. adresin bir kısmı dizinlenebilir ancak vurgulanmış ve etiketlendirilmiş bir taşıyıcı belirteç." border="false":::

7. 5. adımda gösterilen alanları doldurarak Azure AD 'nin Cisco WebEx 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Cisco WebEx hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)
   
8. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

9. **Kaydet**’e tıklayın.

10. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Cisco WebEx olarak eşitler**' ı seçin.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermapping.png" alt-text="Azure portal eşlemeler bölümünün ekran görüntüsü. Ad ' ın altında, Azure Active Directory Kullanıcıları Cıcospark ile eşitler." border="false":::

11. **Öznitelik eşleme** bölümünde Azure AD 'Den Cisco WebEx 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Cisco WebEx içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermappingattributes.png" alt-text="Azure Active Directory özniteliklerini, karşılık gelen Cıcospark özniteliklerini ve eşleşen durumu gösteren öznitelik eşlemeleri bölümünün ekran görüntüsü." border="false":::

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. Cisco WebEx için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsamda** Istenen değerleri seçerek Cisco WebEx 'e sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. **Eşitleme ayrıntıları** bölümünü kullanarak, Cisco WebEx ÜZERINDE Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, ilerlemeyi izleyebilir ve sağlama etkinliği raporuna yönelik bağlantıları izleyebilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Cisco WebEx Şu anda Cisco 'nun erken alan testi (EFT) aşamasındadır. Daha fazla bilgi için lütfen [Cisco 'nun destek ekibine](https://www.webex.co.in/support/support-overview.html)başvurun. 
* Cisco WebEx yapılandırması hakkında daha fazla bilgi için [buradaki](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub)Cisco belgelerine bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
