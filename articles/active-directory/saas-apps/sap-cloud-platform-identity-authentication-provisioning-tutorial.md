---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için SAP bulut platformu kimlik doğrulamasını yapılandırma | Microsoft Docs'
description: SAP bulut platformu kimlik doğrulaması için Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak üzere Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 419f25ee3df471bc2fc4526254f5677b8bd71856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96342762"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlama için SAP bulut platformu kimlik doğrulamasını yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları SAP bulut platformu kimlik doğrulamasına göre otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için SAP Cloud Platform kimlik kimlik doğrulaması ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [SAP bulut platformu kimlik doğrulama kiracısı](https://cloudplatform.sap.com/pricing.html)
* SAP Cloud Platform kimliği kimlik doğrulamasında yönetici izinleriyle bir kullanıcı hesabı.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>SAP bulut platformu kimlik doğrulaması 'na Kullanıcı atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların SAP Cloud Platform Identity Authentication 'a erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları SAP bulut platformu kimliği kimlik doğrulamasına atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>SAP bulut platformu kimlik doğrulaması için Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için SAP bulut platformu kimlik kimlik doğrulamasına tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* SAP Cloud Platform Identity Authentication 'a bir Kullanıcı atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Sağlama için SAP Cloud Platform kimlik doğrulamasını ayarlama

1. [SAP Cloud Platform kimliği kimlik doğrulama yönetici konsolunda](https://sapmsftintegration.accounts.ondemand.com/admin)oturum açın. **Yöneticiler > & yetkilendirmelerini kullanıcılara** gidin.

    ![SAP bulut platformu kimlik doğrulama Yönetici Konsolu](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Listeye yeni bir yönetici eklemek için sol bölmedeki **+ Ekle** düğmesine basın. **Sistem Ekle** ' yi seçin ve sistemin adını girin.   

> [!NOTE]
> SAP bulut platformu kimlik doğrulaması içindeki adminınistrator kullanıcısı **System** türünde olmalıdır. Normal Yönetici Kullanıcı oluşturma, sağlama sırasında *yetkisiz* hatalara yol açabilir.   

3.  Yetkilendirmeleri Yapılandır altında, **kullanıcıları yönetme** ve **grupları yönetme** için iki durumlu düğmeye geçiş yapın.

    ![SAP Cloud Platform kimlik doğrulaması SCıM ekleme](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Hesabınızı etkinleştirmek ve **SAP bulut platformu kimlik doğrulama hizmeti** için bir parola ayarlamak üzere bir e-posta gönderilir.

4.  **Kullanıcı kimliğini** ve **parolasını** kopyalayın. Bu değerler, Azure portal SAP bulut platformu kimlik doğrulama uygulamanızın sağlama sekmesinde sırasıyla Yönetici Kullanıcı adı ve yönetici parolası alanlarına girilir.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Galeriden SAP bulut platformu kimlik doğrulaması ekleme

Azure AD ile otomatik Kullanıcı sağlama için SAP Cloud Platform kimlik kimlik doğrulamasını yapılandırmadan önce, Azure AD uygulama galerisindeki SAP Cloud Platform kimlik kimlik doğrulamasını yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**SAP Cloud Platform kimliği kimlik doğrulamasını Azure AD Uygulama Galerisi 'nden eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **SAP Cloud Platform kimlik doğrulaması**' nı girin, sonuçlar panelinde **SAP Cloud Platform kimliği kimlik doğrulaması** ' nı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![SAP bulut platformu kimlik doğrulaması sonuçlar listesinde](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>SAP bulut platformu kimlik doğrulaması için otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak SAP bulut platformu kimlik doğrulama içindeki kullanıcıları ve/veya grupları Azure AD 'de Kullanıcı ve/veya grup atamalarına göre oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

> [!TIP]
> SAP bulut platformu kimlik [doğrulaması çoklu oturum açma öğreticisinde](./sap-hana-cloud-platform-identity-authentication-tutorial.md)belirtilen YÖNERGELERI Izleyerek SAP bulut platformu kimlik doğrulaması için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilse de

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>SAP bulut platformu kimlik doğrulaması için Azure AD 'de otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **SAP Cloud Platform kimlik doğrulaması**' nı seçin.

    ![Uygulamalar listesindeki SAP bulut platformu kimlik doğrulama bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, `https://<tenantID>.accounts.ondemand.com/service/scim ` **kiracı URL 'sini** girin. Daha önce **Yönetici Kullanıcı adı** ve **yönetici parolası** ' nda alınan **Kullanıcı kimliği** ve **parola** değerlerini girin. Azure AD 'nin SAP bulut platformu kimliği kimlik doğrulamasına bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, SAP bulut platformu kimlik doğrulama hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde, **Kullanıcı Azure Active Directory Kullanıcıları SAP bulut platformu kimlik doğrulaması ile eşitler**' ı seçin.

    ![SAP Cloud Platform kimlik doğrulama Kullanıcı eşlemeleri](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. **Öznitelik eşleme** bölümünde, Azure AD 'Den SAP bulut platformu kimlik doğrulaması ile eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme IŞLEMLERI Için SAP bulut platformu kimlik doğrulama 'daki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![SAP Cloud Platform kimlik doğrulama Kullanıcı öznitelikleri](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

11. SAP bulut platformu kimlik doğrulaması için Azure AD sağlama hizmeti 'ni etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** IÇINDE Istenen değerleri seçerek SAP bulut platformu kimlik doğrulaması için sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

13. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. SAP bulut platformu kimlik doğrulaması üzerinde Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, ilerlemeyi izlemek ve sağlama etkinliği raporunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* SAP Cloud Platform kimliği kimlik doğrulamasının SCıM uç noktası, belirli özniteliklerin belirli biçimde olmasını gerektirir. Bu öznitelikler ve bunlara [özgü biçim hakkında](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#)daha fazla bilgi edinebilirsiniz.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)