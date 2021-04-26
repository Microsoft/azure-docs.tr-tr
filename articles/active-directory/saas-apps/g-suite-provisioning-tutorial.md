---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için G Suite 'i yapılandırma | Microsoft Docs"
description: Azure AD 'den G Suite 'e Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.author: Zhchia
ms.openlocfilehash: b8513f62b6f181a1490d136062c5de81db847ba7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533398"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için G Suite 'i yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem G Suite hem de Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmetini kullanarak kullanıcıları ve grupları [G Suite](https://gsuite.google.com/) 'e otomatik olarak sağlar ve hazırlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * G Suite 'te Kullanıcı oluşturma
> * Artık erişim gerektirmeyen G Suite kullanıcıları kaldır
> * Azure AD ve G Suite arasında kullanıcı özniteliklerini eşitlenmiş tut
> * G Suite 'te grupları ve grup üyeliklerini sağlama
> * G Suite ['Te çoklu oturum açma](./google-apps-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md) 
* Azure AD'de hazırlama [iznine](../roles/permissions-reference.md) sahip bir kullanıcı hesabı (Uygulama Yöneticisi, Bulut Uygulaması Yöneticisi, Uygulama Sahibi veya Genel Yönetici). 
* [Bir G Suite kiracısı](https://gsuite.google.com/pricing.html)
* Yönetici izinlerine sahip bir G Suite üzerinde bir kullanıcı hesabı.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD ve G Suite arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın. 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için G Suite 'i yapılandırma

Azure AD ile otomatik Kullanıcı sağlama için G Suite 'i yapılandırmadan önce, G Suite üzerinde SCıM sağlamasını etkinleştirmeniz gerekir.

1. [G Suite yönetici konsolunda](https://admin.google.com/) Yönetici hesabınızla oturum açın ve ardından **güvenlik**' i seçin. Bağlantıyı görmüyorsanız, ekranın alt kısmındaki **daha fazla denetim** menüsünde gizli olabilir.

    ![G Suite güvenliği](./media/g-suite-provisioning-tutorial/gapps-security.png)

2. **Güvenlik** sayfasında **API başvurusu**' nu seçin.

    ![G Suite API 'SI](./media/g-suite-provisioning-tutorial/gapps-api.png)

3. **API erişimini etkinleştir**' i seçin.

    ![G Suite API 'SI etkin](./media/g-suite-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > G Suite 'e sağlamayı planladığınız her kullanıcı için, Azure AD 'deki Kullanıcı adları özel bir etki alanına bağlı **olmalıdır** . Örneğin, gibi görünen Kullanıcı adları bob@contoso.onmicrosoft.com G Suite tarafından kabul edilmez. Öte yandan bob@contoso.com kabul edilir. Mevcut bir kullanıcının etki alanını [buradaki](../fundamentals/add-custom-domain.md)yönergeleri izleyerek değiştirebilirsiniz.

4. İstediğiniz özel etki alanlarınızı Azure AD ile ekledikten ve doğruladıktan sonra, bunları G Suite ile yeniden doğrulamanız gerekir. G Suite 'teki etki alanlarını doğrulamak için aşağıdaki adımlara bakın:

    a. [G Suite yönetici konsolunda](https://admin.google.com/) **etki alanları**' nı seçin.

    ![G Suite etki alanları](./media/g-suite-provisioning-tutorial/gapps-domains.png)

    b. **Etki alanı veya etki alanı diğer adı Ekle**' yi seçin.

    ![G Suite etki alanı Ekle](./media/g-suite-provisioning-tutorial/gapps-add-domain.png)

    c. **Başka bir etki alanı Ekle**' yi seçin ve sonra eklemek istediğiniz etki alanının adını yazın.

    ![G Suite başka bir Ekle](./media/g-suite-provisioning-tutorial/gapps-add-another.png)

    d. **Devam ' ı seçin ve etki alanı sahipliğini doğrulayın**. Ardından, etki alanı adının sahip olduğunuzu doğrulamak için adımları izleyin. Google ile etki alanınızı doğrulamaya yönelik kapsamlı yönergeler için bkz. [sitenizin sahipliğini doğrulama](https://support.google.com/webmasters/answer/35179).

    e. G Suite 'e eklemek istediğiniz diğer etki alanları için önceki adımları tekrarlayın.

5. Sonra, G Suite 'de Kullanıcı sağlamayı yönetmek için kullanmak istediğiniz yönetici hesabı ' nı saptayın. **Yönetici rolleri**' ne gidin.

    ![G Suite Yöneticisi](./media/g-suite-provisioning-tutorial/gapps-admin.png)

6. Bu hesabın **yönetici rolü** için, bu rolün **ayrıcalıklarını** düzenleyin. Bu hesabın sağlanması için kullanılabilmesi için tüm **yönetıcı API ayrıcalıklarını** etkinleştirdiğinizden emin olun.

    ![G Suite yönetici ayrıcalıkları](./media/g-suite-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden G Suite ekleme

Azure AD uygulama galerisinden g Suite 'i, G Suite 'e sağlamayı yönetmeye başlamak için ekleyin. Daha önce SSO için G Suite 'i ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* G Suite 'e Kullanıcı ve grup atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>5. Adım. G Suite 'e otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak TestApp içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan bir adım adım yol gösterir.

> [!NOTE]
> G paketinin Dizin API 'SI uç noktası hakkında daha fazla bilgi edinmek için [DIZIN API](https://developers.google.com/admin-sdk/directory)'sine bakın.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Azure AD 'de G Suite için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin. Kullanıcıların portal.azure.com 'de oturum açması ve aad.portal.azure.com kullanamayacak şekilde,

    ![Kurumsal uygulamalar dikey penceresi](./media/g-suite-provisioning-tutorial/enterprise-applications.png)

    ![Tüm uygulamalar dikey penceresi](./media/g-suite-provisioning-tutorial/all-applications.png)

2. Uygulamalar listesinde, **G Suite**' i seçin.

    ![Uygulamalar listesindeki G Suite bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin. **Başlarken**' e tıklayın.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

      ![Kullanmaya başlama dikey penceresi](./media/g-suite-provisioning-tutorial/get-started.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde **Yetkilendir**' e tıklayın. Yeni bir tarayıcı penceresinde bir Google yetkilendirmesi iletişim kutusuna yönlendirilirsiniz.

      ![G Suite yetkilendirme](./media/g-suite-provisioning-tutorial/authorize-1.png)

6. G Suite kiracınızda değişiklik yapmak için Azure AD izinleri vermek istediğinizi onaylayın. **Kabul Et**’i seçin.

     ![G Suite Kiracı kimlik doğrulaması](./media/g-suite-provisioning-tutorial/gapps-auth.png)

7. Azure portal, Azure AD 'nin G Suite 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, G Suite hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin. Sonra **Yetkilendir** adımını yeniden deneyin.

6. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları sağla**' yı seçin.

9. **Öznitelik eşleme** bölümünde Azure AD 'Den G Suite 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için G Suite içindeki kullanıcı hesaplarıyla eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, G SUITE API 'sinin kullanıcıları bu özniteliğe göre filtrelemeyi desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|
   |---|---|
   |Her ne kadar Yemail|Dize|
   |ilişkilerin. [tür EQ "Manager"]. değer|Dize|
   |name.familyName|Dize|
   |name.givenName|Dize|
   |alın|Dize|
   |externalIds. [tür EQ "Custom"]. değer|Dize|
   |externalIds. [tür EQ "kuruluş"]. değer|Dize|
   |gideren. [tür EQ "iş"]. ülke|Dize|
   |gideren. [tür EQ "iş"]. streetAddress|Dize|
   |gideren. [tür EQ "iş"]. bölge|Dize|
   |gideren. [tür EQ "iş"]. konum|Dize|
   |gideren. [tür EQ "iş"]. PostaKodu|Dize|
   |-. [tür EQ "iş"]. adres|Dize|
   |ları. [tür EQ "iş"]. departman|Dize|
   |ları. [tür EQ "iş"]. başlık|Dize|
   |phoneNumbers. [tür EQ "iş"]. değer|Dize|
   |phoneNumbers. [tür EQ "mobil"]. değer|Dize|
   |phoneNumbers. [tür EQ "work_fax"]. değer|Dize|
   |-. [tür EQ "iş"]. adres|Dize|
   |ları. [tür EQ "iş"]. departman|Dize|
   |ları. [tür EQ "iş"]. başlık|Dize|
   |phoneNumbers. [tür EQ "iş"]. değer|Dize|
   |phoneNumbers. [tür EQ "mobil"]. değer|Dize|
   |phoneNumbers. [tür EQ "work_fax"]. değer|Dize|
   |gideren. [tür EQ "giriş"]. ülke|Dize|
   |gideren. [tür EQ "ana"]. biçimlendirildi|Dize|
   |gideren. [tür EQ "ana"]. konum|Dize|
   |gideren. [tür EQ "ana"]. PostaKodu|Dize|
   |gideren. [tür EQ "ana"]. bölge|Dize|
   |gideren. [tür EQ "ana"]. streetAddress|Dize|
   |gideren. [tür EQ "Other"]. ülke|Dize|
   |gideren. [tür EQ "Other"]. biçimlendirildi|Dize|
   |gideren. [tür EQ "Other"]. konum|Dize|
   |gideren. [tür EQ "Other"]. PostaKodu|Dize|
   |gideren. [tür EQ "Other"]. bölge|Dize|
   |gideren. [tür EQ "Other"]. streetAddress|Dize|
   |gideren. [tür EQ "iş"]. biçimlendirildi|Dize|
   |changePasswordAtNextLogin|Dize|
   |-. [tür EQ "ana"]. adres|Dize|
   |-. [tür EQ "Other"]. adres|Dize|
   |externalIds. [tür EQ "Account"]. değer|Dize|
   |externalIds. [tür EQ "Custom"]. customType|Dize|
   |externalIds. [tür EQ "müşteri"]. değer|Dize|
   |externalIds. [tür EQ "login_id"]. değer|Dize|
   |externalIds. [tür EQ "ağ"]. değer|Dize|
   |cinsiyet. Type|Dize|
   |Generatedimmutableıd|Dize|
   |Tanımlayıcı|Dize|
   |IMS. [tür EQ "ana"]. protokol|Dize|
   |IMS. [tür EQ "Other"]. protokol|Dize|
   |IMS. [tür EQ "iş"]. protokol|Dize|
   |includeInGlobalAddressList|Dize|
   |ıpwhitelistelenmiş|Dize|
   |ları. [tür EQ "okul"]. costCenter|Dize|
   |ları. [tür EQ "okul"]. Departmanı|Dize|
   |ları. [tür EQ "okul"]. etki alanı|Dize|
   |ları. [tür EQ "okul"]. Fulltimeeşdeğerini|Dize|
   |ları. [tür EQ "okul"]. konum|Dize|
   |ları. [tür EQ "okul"]. ad|Dize|
   |ları. [tür EQ "okul"]. simge|Dize|
   |ları. [tür EQ "okul"]. title|Dize|
   |ları. [tür EQ "iş"]. costCenter|Dize|
   |ları. [tür EQ "iş"]. etki alanı|Dize|
   |ları. [tür EQ "Work"]. Fulltimeeşdeğerini|Dize|
   |ları. [tür EQ "iş"]. konum|Dize|
   |ları. [tür EQ "iş"]. ad|Dize|
   |ları. [tür EQ "Work"]. Symbol|Dize|
   |OrgUnitPath|Dize|
   |phoneNumbers. [tür EQ "ana"]. değer|Dize|
   |phoneNumbers. [tür EQ "Other"]. değer|Dize|
   |siteleriniz. [tür EQ "ana"]. değer|Dize|
   |siteleriniz. [tür EQ "Other"]. değer|Dize|
   |siteleriniz. [tür EQ "iş"]. değer|Dize|
   

10. **Eşlemeler** bölümünde **Azure Active Directory grupları sağla**' yı seçin.

11. **Öznitelik eşleme** bölümünde Azure AD 'Den G Suite 'e eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için G Suite grupları ile eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

      |Öznitelik|Tür|
      |---|---|
      |e-posta|Dize|
      |Üyeler|Dize|
      |name|Dize|
      |açıklama|Dize|

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. G Suite için Azure AD sağlama hizmeti 'ni etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsamda** Istenen değerleri seçerek G Suite 'e sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer.

> [!NOTE]
> Kullanıcıların, Azure AD kullanıcısının e-posta adresini kullanan bir kişisel/tüketici hesabı zaten varsa, dizin eşitlemesi gerçekleştirilmeden önce Google aktarım aracı kullanılarak çözümlenebilen bazı sorunlara neden olabilir.

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

1. Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
2. Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
3. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.  

## <a name="change-log"></a>Değişiklik günlüğü

* 10/17/2020-ek G Suite Kullanıcı ve grup öznitelikleri için destek eklendi.
* 10/17/2020- [burada](https://developers.google.com/admin-sdk/directory)tanımlananla eşleşecek şekilde, G Suite hedef öznitelik adları güncelleştirildi.
* 10/17/2020-varsayılan öznitelik eşlemeleri güncelleştirildi.
* 03/18/2021-yönetici e-postası artık tüm yeni kullanıcılar için KIMLIK yerine eşitleniyor. KIMLIK olarak bir yönetici ile sağlanan mevcut tüm kullanıcılar için, e-postanın sağlandığından emin olmak için "Full" kapsamına sahip [Microsoft Graph](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http&preserve-view=true) aracılığıyla yeniden başlatma yapabilirsiniz. Bu değişiklik, Goov2OutDelta ile başlayan eski sağlama işini değil, yalnızca GSuite sağlama işini etkiler. Yönetici e-postası, Kullanıcı ilk oluşturulduğunda veya yönetici değiştiğinde sağlanır. Yönetici e-posta adresini değiştirirse yönetici e-postası sağlanmaz. 

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
