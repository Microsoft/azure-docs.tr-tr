---
title: 'Öğretici: Slack için kullanıcı hazırlama - Azure AD'
description: Azure Active Directory'yi Slack için kullanıcı hesaplarını otomatik olarak hazırlayacak ve kaldıracak şekilde yapılandırmayı öğrenin.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: arvinh
ms.openlocfilehash: 92c2ae13b840d7a73d86365ce88584bcafc878e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181467"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Öğretici: Slack’i otomatik kullanıcı hazırlama için yapılandırma

Bu öğreticinin amacı Azure AD'deki kullanıcı hesaplarını Slack için otomatik olarak hazırlamak ve kaldırmak üzere Slack ve Azure AD'de gerçekleştirmeniz gereken adımları göstermektir. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * Slack'te kullanıcı oluşturma
> * Slack'e erişimine artık ihtiyaç duymayan kullanıcıları kaldırma
> * Kullanıcı özniteliklerinin Azure AD ile Slack arasında eşitlenmesini sağlama
> * Grupları ve grup üyeliklerini Slack'te hazırlama
> * Slack için [çoklu oturum açma](./slack-tutorial.md) (önerilir)


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide anlatılan senaryoda aşağıdakilere sahip olduğunuz kabul edilmiştir:

* [Azure AD kiracısı](../develop/quickstart-create-new-tenant.md).
* Azure AD'de hazırlama [iznine](../roles/permissions-reference.md) sahip bir kullanıcı hesabı (Uygulama Yöneticisi, Bulut Uygulaması Yöneticisi, Uygulama Sahibi veya Genel Yönetici).
* [Plus planı](https://aadsyncfabric.slack.com/pricing) veya üzeri etkinleştirilmiş Slack kiracısı.
* Slack'te Ekip Yöneticisi (Team Admin) izinlerine sahip bir kullanıcı hesabı.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD ile Slack arasında eşlenecek](../app-provisioning/customize-application-attributes.md) verileri belirleyin. 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Adım 2. Azure AD uygulama galerisinden Slack'i ekleme

Slack'e kullanıcı hazırlamaya başlamak için Azure AD uygulama galerisinden Slack'i ekleyin. Daha önce Slack for SSO'yu ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın. 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>3. Adım Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Slack'e kullanıcı ve grup atarken **Varsayılan Erişim** dışında bir rol seçmeniz gerekir. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz.

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>4. Adım: Slack'e otomatik kullanıcı hazırlamayı yapılandırma 

Bu bölümde Azure AD ortamınızı Slack'in kullanıcı hesabı hazırlama API'sine bağlama ve hazırlama hizmetini yapılandırarak Azure AD'deki kullanıcı ve grup atamalarına göre Slack'te atanan kullanıcı hesabı oluşturma, güncelleştirme ve devre dışı bırakma adımları anlatılmıştır.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Azure AD'de Slack'e otomatik kullanıcı hesabı hazırlama adımlarını yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Slack**'i seçin.

    ![Uygulamalar listesinde Slack bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici Kimlik Bilgileri** bölümünde **Yetki Ver**'e tıklayın. Bunu yaptığınızda yeni bir tarayıcı penceresinde Slack yetkilendirme iletişim kutusu açılır.

    ![Yönetici Kimlik Bilgileri bölümündeki Yetki Ver düğmesini gösteren ekran görüntüsü.](media/slack-provisioning-tutorial/authorization.png)


6. Yeni pencerede Team Admin hesabınızı kullanarak Slack'te oturum açın. Açılan yetkilendirme iletişim kutusunda hazırlığı etkinleştirmek istediğiniz Slack ekibini belirledikten sonra **Yetki Ver**'i seçin. İşlem tamamlandıktan sonra hazırlama yapılandırmasını tamamlamak için Azure portalına geri dönün.

    ![Yetkilendirme İletişim Kutusu](./media/slack-provisioning-tutorial/slackauthorize.png)

7. Azure portalında **Bağlantıyı Test Et**'e tıklayarak Azure AD'nin Slack uygulamanıza bağlanabildiğinden emin olun. Bağlantı başarısız olursa Slack hesabınızın Team Admin izinlerine sahip olduğundan emin olun ve "Yetki Ver" adımını tekrar deneyin.

8. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

9. **Kaydet**’i seçin.

10. Eşlemeler bölümünde **Azure Active Directory Kullanıcılarını Slack ile Eşitle**'yi seçin.

11. **Öznitelik Eşlemeleri** bölümünde Azure AD ile Slack arasında eşitlenecek kullanıcı özniteliklerini gözden geçirin. Güncelleştirme işlemi için Slack'teki kullanıcı hesaplarını eşlemek için **Eşleşen** özellikler olarak seçilen özniteliklerin kullanılacağına dikkat edin. Değişiklikleri uygulamak için Kaydet düğmesini seçin.

   |Öznitelik|Tür|
   |---|---|
   |active|Boole|
   |externalId|Dize|
   |displayName|Dize|
   |name.familyName|Dize|
   |name.givenName|Dize|
   |başlık|Dize|
   |emails[type eq "work"].value|Dize|
   |userName|Dize|
   |nickName|Dize|
   |addresses[type eq "untyped"].streetAddress|Dize|
   |addresses[type eq "untyped"].locality|Dize|
   |addresses[type eq "untyped"].region|Dize|
   |addresses[type eq "untyped"].postalCode|Dize|
   |addresses[type eq "untyped"].country|Dize|
   |phoneNumbers[type eq "mobile"].value|Dize|
   |phoneNumbers[type eq "work"].value|Dize|
   |roles[primary eq "True"].value|Dize|
   |locale|Dize|
   |name.honorificPrefix|Dize|
   |photos[type eq "photo"].value|Dize|
   |profileUrl|Dize|
   |timezone|Dize|
   |userType|Dize|
   |urn:scim:schemas:extension:enterprise:1.0.department|Dize|
   |urn:scim:schemas:extension:enterprise:1.0.manager|Başvuru|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|Dize|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|Dize|
   |urn:scim:schemas:extension:enterprise:1.0.organization|Dize|
   |urn:scim:schemas:extension:enterprise:1.0.division|Dize|

12. **Eşlemeler** bölümünde **Azure Active Directory Gruplarını Slack ile Eşitle**'yi seçin.

13. **Öznitelik Eşlemeleri** bölümünde Azure AD ile Slack arasında eşitlenecek grup özniteliklerini gözden geçirin. Güncelleştirme işlemi için Slack'teki grupları eşlemek için **Eşleşen** özellikler olarak seçilen özniteliklerin kullanılacağına dikkat edin. Değişiklikleri uygulamak için Kaydet düğmesini seçin.

      |Öznitelik|Tür|
      |---|---|
      |displayName|Dize|
      |üyeler|Başvuru|

14. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

15. Slack için Azure AD hazırlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **Hazırlama Durumu** ayarını **Açık** duruma getirin

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

16. **Ayarlar** bölümündeki **Kapsam** alanında istediğiniz değerleri seçerek Slack'te hazırlanmasını istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

17. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer. 

## <a name="step-5-monitor-your-deployment"></a>5. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

1. Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
2. Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
3. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.

## <a name="troubleshooting-tips"></a>Sorun Giderme İpuçları

* Slack'in **displayName** özniteliğini yapılandırırken aşağıdaki durumlara dikkat edin:

  * Değerler tamamen benzersiz değildir (örn. 2 kullanıcının da görünen adı aynı olabilir)

  * İngilizce olmayan karakterleri, boşlukları ve büyük harfleri destekler. 
  
  * İzin verilen noktalama işaretleri: nokta, alt çizgi, kısa çizgi, tırnak işareti, ayraç (örn. **( [ { } ] )** ) ve ayırıcılar (örn. **, / ;** ).
  
  * displayName özelliğinde "@" karakteri kullanılamaz. "@" eklenirse hazırlama günlüklerinde "AttributeValidationFailed" açıklamasına sahip bir atlama olayı görebilirsiniz.

  * Güncelleştirme için Slack çalışma alanında/kuruluşta şu iki ayarın yapılandırılmış olması gerekir: **Profil eşitleme etkin** ve **Kullanıcılar görünen adını değiştiremez**.

* Slack'in **userName** özniteliğinin en fazla 21 karakter uzunluğunda benzersiz bir değer olması gerekir.

* Slack yalnızca **userName** ve **email** öznitelikleriyle eşleşmeye izin verir.  
  
* Sık karşılaşılan hata kodları resmi Slack belgelerinde açıklanmıştır: https://api.slack.com/scim#errors

## <a name="change-log"></a>Değişiklik günlüğü

* 16.06.2020: DisplayName özniteliği yalnızca yeni kullanıcı oluşturma adımında güncelleştirilecek şekilde değiştirildi.

## <a name="additional-resources"></a>Ek Kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)