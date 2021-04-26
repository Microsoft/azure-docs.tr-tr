---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için genel geçiş kimliği eşitlemesini yapılandırma | Microsoft Docs'
description: Azure AD 'den genel geçiş kimlik eşitlemesine Kullanıcı hesaplarını otomatik olarak sağlamayı ve devre dışı bırakmayı öğrenin.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 0c4a3bf0-d0a6-4eab-909b-6cf9f9234e4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: Zhchia
ms.openlocfilehash: d003a512ebde626b8726dfccc58110e53f1cd467
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180923"
---
# <a name="tutorial-configure-global-relay-identity-sync-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlama için genel geçiş kimlik eşitlemesini yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem genel geçiş kimlik eşitleme hem de Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmeti 'ni kullanarak kullanıcıları ve grupları küresel geçiş kimlik eşitlemesine otomatik olarak sağlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * Küresel geçiş kimlik eşitlemeyle Kullanıcı oluşturma
> * Artık erişim gerektirmeyen kullanıcıları genel geçiş kimlik Eşitlede kaldır
> * Azure AD ile genel geçiş kimlik eşitleme arasında kullanıcı özniteliklerini eşitlenmiş olarak tut
> * Genel geçiş kimlik eşitlemede grupları ve grup üyeliklerini sağlama


> [!NOTE]
> Küresel geçiş kimlik eşitleme sağlama Bağlayıcısı, güvenlik sorunları nedeniyle artık desteklenmeyen bir SCıM yetkilendirme yöntemi kullanır. Çabalar, daha güvenli bir yetkilendirme yöntemine geçiş yapmak için küresel geçişle devam eden çalışmalardır.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md) 
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı (örneğin, uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici).

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD Ile genel geçiş kimlik eşitlemesi arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın. 

## <a name="step-2-configure-global-relay-identity-sync-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için küresel geçiş kimlik eşitlemesini yapılandırın

Kiracı URL 'sini almak için genel geçiş kimliği eşitleme temsilcinizle iletişime geçin. Bu değer, Azure portal genel geçiş kimlik eşitleme uygulamanızın sağlama sekmesinde **kiracı URL 'si** alanına girilir.

## <a name="step-3-add-global-relay-identity-sync-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden genel geçiş kimlik eşitlemesi ekleme

Küresel geçiş kimlik eşitlemesine sağlamayı yönetmeye başlamak için Azure AD uygulama galerisinden genel geçiş kimlik eşitlemesi ekleyin. Galeriden bir uygulamayı [buradan](../manage-apps/add-application-portal.md)ekleme hakkında daha fazla bilgi edinin. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 


## <a name="step-5-configure-automatic-user-provisioning-to-global-relay-identity-sync"></a>5. Adım. Otomatik Kullanıcı sağlamayı genel geçiş kimlik eşitlemesine yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak genel geçiş kimlik eşitleme uygulamasındaki kullanıcıları ve/veya grupları Azure AD 'de Kullanıcı ve/veya grup atamalarına göre oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

### <a name="to-configure-automatic-user-provisioning-for-global-relay-identity-sync-in-azure-ad"></a>Azure AD 'de küresel geçiş kimlik eşitlemesi için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **genel geçiş kimlik eşitleme**' yi seçin.

    ![Uygulamalar listesindeki genel geçiş kimlik eşitleme bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Hazırlama sekmesi](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Sekme otomatik sağlama](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, genel geçiş kimliği eşitleme **kiracı URL**'nizi girin. Azure AD 'nin genel geçiş kimlik eşitlemesine bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, genel geçiş kimlik eşitleme hesabınızın yönetici izinlerine sahip olduğundan emin olun ve sorunu çözmek için küresel geçiş temsilcinizle iletişime geçin.

    ![Yetkilendirme düğmesi](media/global-relay-identity-sync-provisioning-tutorial/authorization.png)

6. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde **Azure Active Directory kullanıcıları genel geçiş kimlik eşitlemesi olarak eşitle**' yi seçin.

9. **Öznitelik eşleme** bölümünde, Azure AD 'Den genel geçiş kimlik eşitlemesine eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için genel geçiş kimliği eşitleme içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, genel geçiş kimlik eşitleme API 'sinin kullanıcıları bu özniteliğe göre filtrelemeyi desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|
   |---|---|
   |userName|Dize|
   |active|Boole|
   |displayName|Dize|
   |başlık|Dize|
   |preferredLanguage|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |ad. biçimlendirildi|Dize|
   |adresler [tür EQ "iş"]. biçimlendirildi|Dize|
   |adresler [tür EQ "Work"]. streetAddress|Dize|
   |emails[type eq "work"].value|Dize|
   |adresler [tür EQ "iş"]. konum|Dize|
   |adresler [tür EQ "iş"]. bölge|Dize|
   |adresler [tür EQ "iş"]. PostaKodu|Dize|
   |adresler [tür EQ "iş"]. ülke|Dize|
   |adresler [tür EQ "Other"]. biçimlendirildi|Dize|
   |phoneNumbers[type eq "work"].value|Dize|
   |phoneNumbers[type eq "mobile"].value|Dize|
   |phoneNumbers [tür EQ "Faks"]. değer|Dize|
   |externalId|Dize|
   |name.honorificPrefix|Dize|
   |Name. Manificsuffix|Dize|
   |nickName|Dize|
   |userType|Dize|
   |locale|Dize|
   |timezone|Dize|
   |e-postalar [tür EQ "ana"]. değer|Dize|
   |e-postalar [tür EQ "Other"]. değer|Dize|
   |phoneNumbers [tür EQ "ana"]. değer|Dize|
   |phoneNumbers [tür EQ "Other"]. değer|Dize|
   |phoneNumbers [tür EQ "sayfalayıcı"]. değer|Dize|
   |adresler [tür EQ "ana"]. streetAddress|Dize|
   |adresler [tür EQ "ana"]. konum|Dize|
   |adresler [tür EQ "ana"]. bölge|Dize|
   |adresler [tür EQ "ana"]. PostaKodu|Dize|
   |adresler [tür EQ "ana"]. ülke|Dize|
   |adresler [tür EQ "Home"]. biçimlendirildi|Dize|
   |adresler [tür EQ "Other"]. streetAddress|Dize|
   |adresler [tür EQ "Other"]. konum|Dize|
   |adresler [tür EQ "Other"]. bölge|Dize|
   |adresler [tür EQ "Other"]. PostaKodu|Dize|
   |adresler [tür EQ "Other"]. ülke|Dize|
   |Roller [birincil EQ "true"]. Display|Dize|
   |Roller [birincil EQ "true"]. tür|Dize|
   |roles[primary eq "True"].value|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: employeeNumber|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: costCenter|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Organization|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Bölüm|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Department|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Manager|Başvuru|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: proxyAddresses|Dize|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: extensionAttribute1|Dize|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: extensionAttribute2|Dize|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: extensionAttribute3|Dize|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: extensionAttribute4|Dize|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: extensionAttribute5|Dize|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: extensionAttribute6|Dize|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: extensionAttribute7|Dize|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: extensionAttribute8|Dize|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: extensionAttribute9|Dize|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: extensionAttribute10|Dize|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: extensionAttribute11|Dize|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: extensionAttribute12|Dize|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: extensionAttribute13|Dize|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: extensionAttribute14|Dize|
   |urn: IETF: params: Scim: schemas: EXTENSION: GlobalRelay: 2.0: User: extensionAttribute15|Dize|



10. **Eşlemeler** bölümünde **Azure Active Directory gruplarını genel geçiş kimlik eşitlemesi olarak eşitle**' yi seçin.

11. **Öznitelik eşleme** bölümünde, Azure AD 'Den genel geçiş kimlik eşitlemesine eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için genel geçiş kimliği eşitleme 'deki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

      |Öznitelik|Tür|
      |---|---|
      |displayName|Dize|
      |üyeler|Başvuru|

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. Küresel geçiş kimlik eşitleme için Azure AD sağlama hizmeti 'ni etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek genel geçiş kimlik eşitlemesine sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer. 

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

1. Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
2. Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
3. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.  

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)