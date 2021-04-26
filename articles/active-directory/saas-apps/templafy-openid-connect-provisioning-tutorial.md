---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Templamy OpenID Connect 'i yapılandırma | Microsoft Docs"
description: Kullanıcı hesaplarını Templamy OpenID Connect ' e otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.assetid: 8cbb387a-e3fb-4588-bb87-bf4f88144361
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: zhchia
ms.openlocfilehash: 6b1894d832c3a74916418aa06885c2917267f4b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652598"
---
# <a name="tutorial-configure-templafy-openid-connect-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Templamy OpenID Connect 'i yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, Kullanıcı ve/veya grupları Templamy OpenID Connect ' e otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Templamy OpenID Connect ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* [Bir Templamy kiracısı](https://www.templafy.com/pricing/).
* Yönetici izinleriyle Templamy 'daki bir kullanıcı hesabı.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD Ile Templamy OpenID Connect arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini belirleme. 

## <a name="assigning-users-to-templafy-openid-connect"></a>Kullanıcıları Templamy OpenID Connect 'e atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Templamy OpenID Connect 'e erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Templamy OpenID Connect öğesine atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy-openid-connect"></a>Kullanıcıları Templamy OpenID Connect 'e atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için, OpenID Connect 'e tek bir Azure AD kullanıcısının atanması önerilir. Daha sonra daha fazla Kullanıcı ve/veya grup atanabilir.

* Bir kullanıcıyı Templamy OpenID Connect 'e atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="step-2-configure-templafy-openid-connect-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için Templamy OpenID Connect 'i yapılandırma

Azure AD ile otomatik Kullanıcı sağlaması için Templamy OpenID Connect 'i yapılandırmadan önce, Templamy OpenID Connect üzerinde SCıM sağlamasını etkinleştirmeniz gerekir.

1. Templamy yönetici konsolunda oturum açın. **Yönetim**' e tıklayın.

    ![Templamy Yönetici Konsolu](media/templafy-openid-connect-provisioning-tutorial/templafy-admin.png)

2. **Kimlik doğrulama yöntemi**' ne tıklayın.

    ![Kimlik doğrulama yöntemi seçeneği olarak adlandırılan Templamy yönetim bölümünün ekran görüntüsü.](media/templafy-openid-connect-provisioning-tutorial/templafy-auth.png)

3. **SCIM api-Key** değerini kopyalayın. Bu değer, Azure portal Templamy OpenID Connect uygulamanızın sağlama sekmesindeki **gizli belirteç** alanına girilir.

    ![S C M A P ı anahtar ekran görüntüsü.](media/templafy-openid-connect-provisioning-tutorial/templafy-token.png)

## <a name="step-3-add-templafy-openid-connect-from-the-gallery"></a>3. Adım Galeriden Templamy OpenID Connect 'i ekleme

Azure AD ile otomatik Kullanıcı hazırlama için Templamy OpenID Connect 'i yapılandırmak için, Azure AD uygulama galerisindeki Şablonınlist OpenID Connect 'i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden Templamy OpenID Connect eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Templamy OpenID Connect** yazın, sonuçlar panelinde **Templamy OpenID Connect** ' i seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde templamy OpenID Connect](common/search-new-app.png)

## <a name="step-4-configure-automatic-user-provisioning-to-templafy-openid-connect"></a>4. Adım: Otomatik Kullanıcı sağlamayı Şablonaç OpenID Connect 'e yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni kullanarak Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan Templamy OpenID Connect 'te kullanıcıları ve/veya grupları oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

> [!TIP]
> Ayrıca, [Templamy çoklu oturum açma öğreticisinde](templafy-tutorial.md)belirtilen yönergeleri Izleyerek templamy Için OpenID Connect tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-templafy-openid-connect-in-azure-ad"></a>Azure AD 'de Templamy OpenID Connect için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Templamy OpenID Connect**' i seçin.

    ![Uygulamalar listesinde Templamy OpenID Connect bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, `https://scim.templafy.com/scim` **kiracı URL 'sini** girin. **Gizli belirteçte** daha önce alınan **SCIM API anahtar** değerini girin. Azure AD 'nin Templamy 'a bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Templamy hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları, OpenID Connect ' e** tıklayın.

    ![Templamy OpenID Connect Kullanıcı eşlemeleri](media/templafy-openid-connect-provisioning-tutorial/user-mapping.png)

9. **Öznitelik eşleme** bölümünde, Azure AD 'Den Templamy OpenID Connect ' e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Templamy OpenID Connect içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|Filtreleme için destekleniyor|
   |---|---|---|
   |userName|Dize|&check;|
   |active|Boole|
   |displayName|Dize|
   |başlık|Dize|
   |preferredLanguage|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |phoneNumbers[type eq "work"].value|Dize|
   |phoneNumbers[type eq "mobile"].value|Dize|
   |phoneNumbers [tür EQ "Faks"]. değer|Dize|
   |externalId|Dize|
   |adresler [tür EQ "iş"]. konum|Dize|
   |adresler [tür EQ "iş"]. PostaKodu|Dize|
   |adresler [tür EQ "iş"]. bölge|Dize|
   |adresler [tür EQ "Work"]. streetAddress|Dize|
   |adresler [tür EQ "iş"]. ülke|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Department|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Organization|Dize|

10. **Eşlemeler** bölümünde, **Azure Active Directory grupları Şablonlarım**' a eşitler ' ı seçin.

    ![Templamy OpenID Connect grup eşlemeleri](media/templafy-openid-connect-provisioning-tutorial/group-mapping.png)

11. **Öznitelik eşleme** bölümünde, Azure AD 'Den Templamy OpenID Connect ' e eşitlenen grup özniteliklerini inceleyin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Templamy OpenID Connect içindeki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

      |Öznitelik|Tür|Filtreleme için destekleniyor|
      |---|---|---|
      |displayName|Dize|&check;|
      |üyeler|Başvuru|
      |externalId|Dize|      

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. Azure AD sağlama hizmetini Templamy OpenID Connect için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsamda** Istenen değerleri seçerek OpenID Connect 'e sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

    Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilir ve Azure AD sağlama hizmeti tarafından Templamy OpenID Connect üzerinde gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinlik raporuna yönelik bağlantıları izleyebilirsiniz.

## <a name="step-5-monitor-your-deployment"></a>5. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

* Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
* Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
* Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)