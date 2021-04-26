---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Olfeo SAAS 'yi yapılandırma | Microsoft Docs"
description: Azure AD 'den Olfeo SAAS 'ye Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f6b0320-dfe7-451c-8cd8-6ba7f2e40434
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2021
ms.author: Zhchia
ms.openlocfilehash: b74175c7847bb19aa9410edd613afbfe1d762d05
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105549318"
---
# <a name="tutorial-configure-olfeo-saas-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için Olfeo SAAS 'yi yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem Olfeo SAAS hem de Azure Active Directory (Azure AD) içinde yapmanız gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmeti ['ni kullanarak Kullanıcı](https://www.olfeo.com) ve grupları otomatik olarak temin etmek ve sağlaması için kullanıcıları ve grupları otomatik olarak sağlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen yetenekler
> [!div class="checklist"]
> * Olfeo SAAS 'de Kullanıcı oluşturma
> * Olfeo SAAS 'deki kullanıcıları artık erişim gerektirdiklerinde kaldırın
> * Kullanıcı özniteliklerinin Azure AD ve Olfeo SAAS arasında eşitlenmiş olmasını sağlama
> * Olfeo SAAS 'de gruplar ve grup üyelikleri sağlama
> * Olfeo SAAS ['de çoklu oturum açma](olfeo-saas-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md) 
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı (örneğin, uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici). 
* Bir [Olfeo SaaS kiracısı](https://www.olfeo.com/).
* Yönetici izinlerine sahip Olfeo SAAS 'de bir kullanıcı hesabı.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama

1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
1. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
1. [Azure AD ve Olfeo SaaS arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın.

## <a name="step-2-configure-olfeo-saas-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için Olfeo SAAS 'yi yapılandırma

1. Olfeo SAAS Yönetici Konsolu 'nda oturum açın. 
1. **> yıllık yıllık yapılandırma** bölümüne gidin.
1. Yeni bir dizin oluşturun ve bunu adlandırın.
1. **Azure** sağlayıcısı ' nı seçin ve ardından yeni dizini kaydetmek için **CR** ' ye tıklayın. 
1. **KIRACı URL** 'Sini ve **jeton gizliliğini** görmek için **eşitleme sekmesine gidin** . Bu değerler, Azure portal Olfeo SAAS uygulamanızın sağlama sekmesinde **kiracı URL 'si** ve **gizli belirteç** alanlarına kopyalanıp yapıştırılacaktır.

## <a name="step-3-add-olfeo-saas-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD Uygulama Galerisi 'nden Olfeo SAAS ekleme

Azure AD Uygulama Galerisi 'nden Olfeo SAAS ekleyerek, Olfeo SAAS 'ye sağlamayı yönetmeye başlayın. Daha önce SSO için Olfeo SAAS 'yi ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak, başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-gallery-app.md) bakın. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Olfeo SAAS 'ye Kullanıcı ve grup atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulamada kullanılabilen tek rol varsayılan erişim rolü ise, daha fazla rol eklemek için [uygulama bildirimini güncelleştirebilirsiniz](../develop/howto-add-app-roles-in-azure-ad-apps.md) . 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 


## <a name="step-5-configure-automatic-user-provisioning-to-olfeo-saas"></a>5. Adım. Olfeo SAAS için otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve grup atamalarını temel alan Olfeo SAAS uygulamasındaki kullanıcıları ve grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında size kılavuzluk eder.

### <a name="to-configure-automatic-user-provisioning-for-olfeo-saas-in-azure-ad"></a>Azure AD 'de Olfeo SAAS için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

1. Uygulamalar listesinde **Olfeo SaaS**' yi seçin.

    ![Uygulamalar listesindeki Olfeo SAAS bağlantısı](common/all-applications.png)

1. **Hazırlama** sekmesini seçin.

    ![Hazırlama sekmesi](common/provisioning.png)

1.  **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Sekme otomatik sağlama](common/provisioning-automatic.png)

1. **Yönetici kimlik bilgileri** bölümünde, Olfeo SaaS **kiracı URL 'sini** ve **gizli belirteç** bilgilerini girin. Azure AD 'nin Olfeo SAAS 'ye bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, Olfeo SAAS hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Belirteç](common/provisioning-testconnection-tenanturltoken.png)

1. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin. **Bir hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

1. **Kaydet**’i seçin.

1. **Eşlemeler** bölümünde, **Azure Active Directory Kullanıcıları FEFEO SaaS olarak eşitler**' ı seçin.

1. **Öznitelik eşleme** bölümünde, Azure AD 'Den Olfeo SaaS 'ye eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, Olfeo SaaS 'deki Kullanıcı hesaplarını güncelleştirme işlemleri için eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirirseniz, Olfeo SaaS API 'sinin, bu özniteliğe göre kullanıcıların filtrelenmesini desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** ' i seçin.

   |Öznitelik|Tür|Filtreleme için destekleniyor|
   |---|---|---|
   |userName|Dize|&check;|
   |displayName|Dize|
   |active|Boole|
   |emails[type eq "work"].value|Dize|
   |preferredLanguage|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |ad. biçimlendirildi|Dize|
   |externalId|Dize|  

1. **Eşlemeler** bölümünde, **FEFEO SaaS Için Azure Active Directory gruplarını eşitler**' ı seçin.

1. **Öznitelik eşleme** bölümünde, Azure AD 'Den Olfeo SaaS 'ye eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, Olfeo SaaS 'deki grupları güncelleştirme işlemleri için eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

      |Öznitelik|Tür|Filtreleme için destekleniyor|
      |---|---|---|
      |displayName|Dize|&check;|
      |externalId|Dize|
      |üyeler|Başvuru|

1. Kapsam filtrelerini yapılandırmak için [kapsam filtresi öğreticisinde](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)sunulan yönergelere bakın.

1. Olfeo SAAS için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

1. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek olfeo SaaS 'ye sağlamak istediğiniz kullanıcıları veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

1. Sağlamaya hazırsanız **Kaydet**' i seçin.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngü, Azure AD sağlama hizmeti çalıştığı sürece her 40 dakikada bir sonraki döngüden daha uzun sürer.

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme

Sağlamayı yapılandırdıktan sonra, dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

* Hangi kullanıcıların başarıyla sağlandığını veya başarısız olduğunu öğrenmek için [sağlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın.
* Sağlama döngüsünün durumunu ve tamamlama işleminin tamamlanmasına nasıl yakın olduğunu görmek için [ilerleme çubuğunu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) kontrol edin.
* Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumları hakkında daha fazla bilgi edinmek için bkz. [karantinanın uygulama sağlama durumu](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="more-resources"></a>Diğer kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)