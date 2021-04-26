---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için atea yapılandırma | Microsoft Docs'
description: Azure AD 'den atea 'ya Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b788328b-10fd-4eaa-a4bc-909d738d8b8b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 1991c27ee992a08c3f31cd90df016bf19405f56a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653227"
---
# <a name="tutorial-configure-atea-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için atea 'yi yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem atea hem de Azure Active Directory (Azure AD) içinde yapmanız gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmetini kullanarak kullanıcıları ve grupları otomatik olarak temin etmek ve devre dışı [bırakmayı sağlar.](https://www.atea.com/) Bu hizmetin ne yaptığı hakkında önemli ayrıntılar için, nasıl çalıştığı ve sık sorulan sorular, [Azure Active Directory Ile SaaS uygulamalarına otomatik olarak Kullanıcı hazırlama ve sağlamayı kaldırma](../app-provisioning/user-provisioning.md)hakkında bilgi verir. 


## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * Atea 'da Kullanıcı oluşturma
> * Artık erişim gerektirmeyen kullanıcıları atea 'da kaldırın
> * Kullanıcı özniteliklerinin Azure AD ile atea arasında eşitlenmiş olmasını sağlama

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md) 
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı (örneğin, uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici). 
* Yönetici izinleriyle atea 'da bir kullanıcı hesabı.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD Ile atea arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın. 

## <a name="step-2-configure-atea-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için atea 'yi yapılandırma

Atea 'yi Azure AD ile sağlamayı destekleyecek şekilde yapılandırmak için lütfen atea destek ekibine bir e-posta yazın <SSO.Support@atea.com>

## <a name="step-3-add-atea-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden atea ekleyin

Azure AD uygulama galerisinden atea ' yı ekleyerek atea 'ya sağlamayı yönetmeye başlayın. Daha önce atea 'yı SSO için ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak, başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD sağlama hizmeti, uygulamaya atamaya ve Kullanıcı ve grup özniteliklerine göre sağlanacak olan kapsamınızı kullanmanıza olanak sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Atea 'ya Kullanıcı ve grup atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulamada kullanılabilen tek rol varsayılan erişim rolü ise, diğer rolleri eklemek için [uygulama bildirimini güncelleştirebilirsiniz](../develop/howto-add-app-roles-in-azure-ad-apps.md) . 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Sağlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında, uygulamaya bir veya iki kullanıcı veya grup atayarak bunu denetleyebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 


## <a name="step-5-configure-automatic-user-provisioning-to-atea"></a>5. Adım. Atea 'ya otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölüm, Azure AD 'de Kullanıcı ve grup atamalarını temel alarak atea 'daki kullanıcıları ve grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD sağlama hizmetini yapılandırma adımlarında size rehberlik eder.

### <a name="to-configure-automatic-user-provisioning-for-atea-in-azure-ad"></a>Azure AD 'de atea için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **atea**' yı seçin.

    ![Uygulamalar listesindeki atea bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Hazırlama sekmesi](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Sekme otomatik sağlama](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde **Yetkilendir**' i seçin. Yeni bir tarayıcı penceresinde bir atea oturum açma iletişim kutusu açar.

     ![Atea yetkilendirme](media/atea-provisioning-tutorial/provisioning-authorize.png)

6. Atea 'nın oturum açma iletişim kutusunda, atea 'nın kiracısında oturum açın ve kimliğinizi doğrulayın.
       
      ![Atea oturum açma iletişim kutusu](media/atea-provisioning-tutorial/atea-login.png)

7. 5 ve 6. adımları tamamladıktan sonra, Azure AD 'nin atea 'ya bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, atea 'nizin yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.
        
      ![Atea test bağlantısı](media/atea-provisioning-tutorial/test-connection.png)

8. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin. Sonra bir **hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

9. **Kaydet**’i seçin.

10. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları atea ile eşitler**' ı seçin.

11. **Öznitelik eşleme** bölümünde Azure AD 'Den atea 'ya eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için atea 'daki Kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, atea API 'sinin kullanıcıları bu özniteliğe göre filtrelemeyi desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

      |Öznitelik|Tür|Filtreleme için destekleniyor|
      |---|---|---|
      |userName|Dize|&check;|
      |active|Boole|
      |emails[type eq "work"].value|Dize|
      |name.givenName|Dize|
      |name.familyName|Dize|
      |ad. biçimlendirildi|Dize|
      |phoneNumbers[type eq "mobile"].value|Dize|
      |locale|Dize|
      |nickName|Dize|

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. Atea için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içinde Ilgili değeri seçerek atea 'ya sağlamak istediğiniz kullanıcıları ve grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngü sonraki döngüden daha uzun sürer. Bu, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir gerçekleşir. 

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

* Hangi kullanıcıların başarıyla sağlandığını veya başarısız olduğunu öğrenmek için [sağlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın.
* Sağlama döngüsünün durumunu ve tamamlama işleminin tamamlanmasına nasıl yakın olduğunu görmek için [ilerleme çubuğunu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) kontrol edin.
* Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.  

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)