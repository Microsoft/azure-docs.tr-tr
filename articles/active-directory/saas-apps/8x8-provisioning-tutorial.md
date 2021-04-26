---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için 8x8 yapılandırma | Microsoft Docs'
description: Azure AD 'den 8x8 ' e Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/15/2020
ms.author: Zhchia
ms.openlocfilehash: a6aa4ad009d037e6ea0d1ade3cc9735351bd634a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96558870"
---
# <a name="tutorial-configure-8x8-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için 8x8 yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem 8x8 yönetici konsolunda hem de Azure Active Directory (Azure AD) gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmetini kullanarak kullanıcıları ve grupları [8x8](https://www.8x8.com) ' e otomatik olarak sağlar ve hazırlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * 8x8 ' de Kullanıcı oluşturma
> * 8x8 ' de artık erişim gerektirmeyen kullanıcıları kaldır
> * Kullanıcı özniteliklerinin Azure AD ve 8x8 arasında eşitlenmiş kalmasını sağlama
> * 8x8 [' e çoklu oturum açma](./8x8virtualoffice-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md) 
* Azure AD'de hazırlama [iznine](../roles/permissions-reference.md) sahip bir kullanıcı hesabı (Uygulama Yöneticisi, Bulut Uygulaması Yöneticisi, Uygulama Sahibi veya Genel Yönetici).
* Herhangi bir düzeyin bir 8x8 X serisi aboneliği.
* Yönetici [konsolu](https://vo-cm.8x8.com)'nda yönetici iznine sahip bir 8x8 Kullanıcı hesabı.
* [Azure AD Ile tek Sign-On](./8x8virtualoffice-tutorial.md) zaten yapılandırılmış.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD ile 8x8 arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın.

## <a name="step-2-configure-8x8-to-support-provisioning-with-azure-ad"></a>Adım 2. 8x8 ' i Azure AD ile sağlamayı destekleyecek şekilde yapılandırma

Bu bölüm, Azure AD ile sağlamayı desteklemek için 8x8 ' i yapılandırma adımlarında size rehberlik eder.

### <a name="to-configure-a-user-provisioning-access-token-in-8x8-admin-console"></a>8x8 yönetici konsolunda kullanıcı sağlama erişim belirteci yapılandırmak için:

1. [Yönetici Konsolu](https://admin.8x8.com)'nda oturum açın. **Kimlik yönetimi**' ni seçin.

   ![Yönetici](./media/8x8-provisioning-tutorial/8x8-identity-management.png)

2. Belirteç oluşturmak için **Kullanıcı sağlama bilgilerini göster** bağlantısına tıklayın.

   ![Göster](./media/8x8-provisioning-tutorial/8x8-show-user-provisioning.png)

3. **8X8 URL** ve **8X8 API belirteç** değerlerini kopyalayın. Bu değerler, Azure portal 8x8 uygulamanızın sağlama sekmesinde sırasıyla **kiracı URL 'si** ve **gizli belirteç** alanlarına girilir.

   ![Belirteç](./media/8x8-provisioning-tutorial/8x8-copy-url-token.png)

## <a name="step-3-add-8x8-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden 8x8 ekleyin

Azure AD uygulama galerisinden 8x8 ' e ekleyerek 8x8 ' e sağlamayı yönetmeye başlayın. Daha önce, SSO için 8x8 ' i ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Bu, daha basit bir seçenektir ve çoğu kişi tarafından kullanılır.

Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* 8x8 ' e Kullanıcı ve grup atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 

## <a name="step-5-configure-automatic-user-provisioning-to-8x8"></a>5. Adım. Otomatik Kullanıcı sağlamayı 8x8 ' e yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve/veya Grup atamaları temelinde 8x8 ' deki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında size rehberlik eder.

### <a name="to-configure-automatic-user-provisioning-for-8x8-in-azure-ad"></a>Azure AD 'de 8x8 için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](./media/8x8-provisioning-tutorial/enterprise-applications.png)

    ![Tüm uygulamalar dikey penceresi](./media/8x8-provisioning-tutorial/all-applications.png)

2. Uygulamalar listesinde **8x8**' i seçin.

    ![Uygulamalar listesindeki 8x8 bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin. **Başlarken**' e tıklayın.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

   ![Kullanmaya başlama dikey penceresi](./media/8x8-provisioning-tutorial/get-started.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, yönetim konsolundan **8X8 URL** 'sini **kiracı URL**'sine kopyalayın. **8x8 API belirtecini** Yönetici konsolundan **gizli belirtece** kopyalayın. Azure AD 'nin 8x8 ' e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, 8x8 hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Ekran görüntüsü, kiracı U R L ve gizli belirteç girebileceğiniz yönetici kimlik bilgileri iletişim kutusunu gösterir.](./media/8x8-provisioning-tutorial/provisioning.png)

6. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları sağla**' yı seçin.

9. **Öznitelik eşleme** bölümünde Azure AD 'den 8x8 ' e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme işlemleri için 8x8 ' deki Kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, 8X8 API 'sinin kullanıcıları bu özniteliğe göre filtrelemeyi desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|Notlar|
   |---|---|---|
   |userName|Dize|Hem Kullanıcı adını hem de Federasyon KIMLIĞINI ayarlar|
   |externalId|Dize||
   |active|Boole||
   |başlık|Dize||
   |emails[type eq "work"].value|Dize||
   |name.givenName|Dize||
   |name.familyName|Dize||
   |phoneNumbers[type eq "mobile"].value|Dize|Kişisel kişi numarası|
   |phoneNumbers[type eq "work"].value|Dize|Kişisel kişi numarası|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Department|Dize||
   |urn: IETF: params: Scim: schemas: extenm: 8x8:1.1: User: site|Dize|Kullanıcı oluşturulduktan sonra güncelleştirilemez|
   |locale|Dize|Varsayılan olarak eşlenmedi|
   |timezone|Dize|Varsayılan olarak eşlenmedi|

10. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

11. 8x8 için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsamda** istenen değerleri seçerek 8x8 ' e kadar sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

13. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer. 

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

1. Hangi kullanıcıların başarıyla sağlandığını veya başarısız olduğunu öğrenmek için [sağlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın.
2. Sağlama döngüsünün durumunu ve ne kadar kapanmasının tamamlanmasının nasıl yapıldığını görmek için [ilerleme çubuğunu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) kontrol edin.
3. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)