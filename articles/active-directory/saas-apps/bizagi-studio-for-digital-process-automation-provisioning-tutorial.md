---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Bizagı Studio 'Yu dijital Işlem otomasyonu için yapılandırma | Microsoft Docs"
description: Dijital Işlem otomasyonu için Azure AD 'den Bizagı Studio 'ya Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: 72e021f47bb8db4dedf0e434d0d94bb2118a4c00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728209"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Bizagı Studio 'Yu dijital Işlem otomasyonu için yapılandırma

Bu öğreticide, otomatik Kullanıcı sağlamayı yapılandırmak için hem Bizagı Studio hem de dijital Işlem otomasyonu için Azure Active Directory (Azure AD) için gerçekleştirmeniz gereken adımlar açıklanmaktadır. Bunu yapmak için yapılandırıldığında Azure AD, Azure AD sağlama hizmeti 'ni kullanarak, [dijital Işlem otomasyonu için](https://www.bizagi.com/) kullanıcıları ve grupları otomatik olarak sağlar ve hazırlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * Dijital Işlem otomasyonu için Bizagı Studio 'da Kullanıcı oluşturma
> * Artık erişim gerektirdiklerinde, Bizagı Studio 'daki kullanıcıları dijital Işlem otomasyonu için kaldırma
> * Dijital Işlem otomasyonu için Azure AD ile Bizagı Studio arasında eşitlenmiş Kullanıcı özniteliklerini koruyun
> * Dijital Işlem otomasyonu için Bizagı Studio ['Da çoklu oturum açma](./bizagi-studio-for-digital-process-automation-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, zaten aşağıdakilere sahip olduğunuzu varsayar:

* [Azure AD kiracısı](../develop/quickstart-create-new-tenant.md). 
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı. Uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici örnekleri bulunur. 
* Digital Process Automation sürüm 11.2.4.2 X veya üzeri için bizagı Studio.

## <a name="plan-your-provisioning-deployment"></a>Hazırlama dağıtımınızı planlama
Planlama için şu adımları izleyin:

1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Sağlama için kimin kapsam içinde](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)olacağını belirleme.
3. [Dijital Işlem Otomasyonu Için Azure AD Ile Bizagı Studio arasında eşlenecek](../app-provisioning/customize-application-attributes.md)verileri belirleme. 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>Azure AD ile sağlamayı destekleyecek şekilde yapılandırma
Azure AD ile sağlamayı desteklemek için Bizagı Studio 'Yu dijital Işlem otomasyonu için yapılandırmak üzere aşağıdaki adımları izleyin:

1. **Yönetici izinlerine** sahip bir kullanıcı olarak iş portalınızda oturum açın.

2. **Yönetici**  >  **güvenliği**  >  **OAuth 2 uygulamalarına** gidin.

   ![OAuth 2 uygulamaları vurgulanmış olan Bizagı 'nin ekran görüntüsü.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. **Add (Ekle)** seçeneğini belirleyin.
4. **Verme türü** için **taşıyıcı belirteci**' ni seçin. **Izin verilen kapsam** için **API** ve **Kullanıcı eşitleme**' yi seçin. Sonra **Kaydet**'i seçin.

   ![Atama türü ve Izin verilen kapsam vurgulanmış şekilde, kayıt uygulamasının ekran görüntüsü.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. **Istemci gizliliğini** kopyalayın ve kaydedin. Azure portal, Bizagı Studio for Digital Process Automation uygulamanız için **sağlama** sekmesinde, **gizli belirteç** alanına istemci gizli değeri girilir.

   ![Istemci gizli highlighed ile OAuth ekran görüntüsü.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>Uygulamayı Azure AD Galerisi 'nden ekleyin

Dijital Işlem otomasyonu için Bizagı Studio 'ya sağlamayı yönetmeye başlamak için, uygulamayı Azure AD Uygulama Galerisi ' nden ekleyin. Çoklu oturum açma için Bizagı Studio 'Yu daha önce dijital Işlem otomasyonu için ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak, tümleştirmeyi ilk kez test ederken ayrı bir uygulama oluşturmanız gerekir. Daha fazla bilgi için bkz. [hızlı başlangıç: Azure Active Directory (Azure AD) kiracınıza uygulama ekleme](../manage-apps/add-application-portal.md). 

## <a name="define-who-is-in-scope-for-provisioning"></a>Sağlama için kimin kapsam içinde olduğunu tanımlama 

Azure AD sağlama hizmeti ile, Kullanıcı ve grubun özniteliklerine veya her ikisine de göre, uygulamaya atamaya göre sağlanan kapsamı belirleyebilirsiniz. Atama temelinde kapsam yaparsanız, uygulamaya Kullanıcı ve grupları atamak için [Graph API kullanan bir uygulama için kullanıcılar ve gruplar atama veya atamayı kaldırma](../manage-apps/assign-user-or-group-access-portal.md) bölümündeki adımlara bakın. Yalnızca kullanıcının veya grubun özniteliklerine göre kapsam yaparsanız, bir kapsam filtresi kullanabilirsiniz. Daha fazla bilgi için bkz. [kapsam filtreleri Ile öznitelik tabanlı uygulama sağlama](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Kapsamı belirleme hakkında aşağıdaki noktalara göz önünde edin:

* Dijital Işlem otomasyonu için kullanıcıları ve grupları Bizagı Studio 'ya atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan erişim rolüne sahip kullanıcılar, sağlanmasından dışlanır ve etkin değil olarak işaretlenecek şekilde, sağlama günlüklerinde işaretlenir. Uygulamada kullanılabilen tek rol varsayılan erişim rolü ise, daha fazla rol eklemek için [uygulama bildirimini güncelleştirebilirsiniz](../develop/howto-add-app-roles-in-azure-ad-apps.md) . 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Sağlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında, uygulamaya bir veya iki kullanıcı veya grup atayarak bunu kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında, [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)belirtebilirsiniz. 


## <a name="configure-automatic-user-provisioning"></a>Otomatik kullanıcı sağlamayı yapılandırma 

Bu bölüm, Kullanıcı ve grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD sağlama hizmetini yapılandırma adımlarında size rehberlik eder. Bunu, Azure AD 'de Kullanıcı ve grup atamalarına göre test uygulamanızda yapıyorsunuz.

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Azure AD 'de dijital Işlem otomasyonu için Bizagı Studio için otomatik Kullanıcı sağlamayı yapılandırma

1. [Azure portalında](https://portal.azure.com) oturum açın. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar ve tüm uygulamalar vurgulanarak Azure portal ekran görüntüsü.](common/enterprise-applications.png)

2. Uygulamalar listesinde, **dijital Işlem Otomasyonu Için Bizagı Studio**' yı seçin.

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneklerinin, sağlama vurgulandığı ekran görüntüsü.](common/provisioning.png)

4. **Sağlama modunu** **Otomatik** olarak ayarlayın.

    ![Otomatik vurgulanmış şekilde screenshotof sağlama modu denetimi.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, dijital işlem otomasyonu Için Bizagı Studio IÇIN kiracı URL 'nizi ve gizli belirtecinizi girin. 

      * **Kiracı URL 'si:** Bizagi SCıM uç noktasını aşağıdaki yapıyla girin:  `<Your_Bizagi_Project>/scim/v2/` .
         Örneğin: `https://my-company.bizagi.com/scim/v2/`.

      * **Gizli dizi belirteci:** Bu değer, bu makalenin önceki kısımlarında açıklanan adımdan alınır.

      Azure AD 'nin dijital Işlem otomasyonu için Bizagı Studio 'ya bağlanabildiğinden emin olmak için **Bağlantıyı Sına**' yı seçin. Bağlantı başarısız olursa, Bizagı Studio 'Nun dijital Işlem Otomasyonu hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

   ![Test bağlantısı vurgulanmış şekilde yönetici kimlik bilgilerinin ekran görüntüsü.](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** için, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin. **Bir hata oluştuğunda e-posta bildirimi gönderme** seçeneğini belirleyin.

    ![Bildirim e-postası seçeneklerinin ekran görüntüsü.](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde, **dijital işlem otomasyonu Için Azure Active Directory Kullanıcıları Bizagı Studio ile eşitler**' ı seçin.

9. **Öznitelik eşleme** bölümünde, dijital işlem otomasyonu IÇIN Azure AD 'Den Bizagı Studio 'ya eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemlerinde dijital işlem otomasyonu Için Bizagı Studio 'daki Kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirirseniz, Digital Process Automation API Için Bizagı Studio 'nun kullanıcıları bu özniteliğe göre filtrelemeyi desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** ' i seçin.

   |Öznitelik|Tür|Filtreleme için destekleniyor|
   |---|---|---|
   |userName|Dize|&check;|
   |active|Boole|
   |emails[type eq "work"].value|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |ad. biçimlendirildi|Dize|
   |phoneNumbers[type eq "mobile"].value|Dize|

   Özel uzantı öznitelikleri, **Bizagı için öznitelik listesini düzenle > gelişmiş seçenekleri göster**' e gidilerek eklenebilir. Özel uzantı özniteliklerine önek eklenmelidir: IETF: **params: Scim: schemas: Extension: bizagı: 2.0: UserProperties:**. Örneğin, özel uzantı özniteliği bir **ıdentifiationnumber** ise, öznitelik **urn: IETF: params: Scim: schemas: Extension: bizagı: 2.0: UserProperties: ıdencertificate: ationnumber** olarak eklenmelidir. Değişiklikleri uygulamak için **Kaydet** ' i seçin.
   
    ![Öznitelik listesini düzenleyin.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   Özel öznitelikler ekleme hakkında daha fazla bilgi, [uygulama özniteliklerini özelleştirme](../app-provisioning/customize-application-attributes.md)bölümünde bulunabilir.

> [!NOTE]
> Yalnızca temel tür özellikleri desteklenir (örneğin, String, Integer, Boolean, DateTime, vb.). Parametrik tablolara veya birden çok türe bağlı özellikler henüz desteklenmiyor.

10. Kapsam filtrelerini yapılandırmak için bkz. [kapsam filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Dijital Işlem otomasyonu için Bizagı Studio için Azure AD sağlama hizmeti 'ni etkinleştirmek üzere **Ayarlar** bölümünde, **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu geçiş ekranının ekran görüntüsü.](common/provisioning-toggle-on.png)

12. Dijital Işlem otomasyonu için Bizagı Studio 'ya sağlamak istediğiniz kullanıcıları ve grupları tanımlayın. **Ayarlar** bölümünde, **kapsam** içinde istenen değerleri seçin.

    ![Kapsam seçeneklerinin ekran görüntüsü.](common/provisioning-scope.png)

13. Sağlamaya hazırsanız **Kaydet**' i seçin.

    ![Denetim kaydetme ekran görüntüsü.](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer. 

## <a name="monitor-your-deployment"></a>Dağıtımınızı izleme
Sağlamayı yapılandırdıktan sonra, dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

- Hangi kullanıcıların başarıyla sağlandığını veya başarısız olduğunu öğrenmek için [sağlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın.
- Sağlama döngüsünün durumunu ve ne kadar yakın olduğunu görmek için [ilerleme çubuğunu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) kontrol edin.
- Sağlama yapılandırması sağlıksız durumdaysa, uygulama karantinaya alınır. Daha fazla bilgi için bkz. [karantina durumunda uygulama hazırlama](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)