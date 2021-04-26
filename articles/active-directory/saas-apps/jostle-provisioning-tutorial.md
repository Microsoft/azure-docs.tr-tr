---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için jostle yapılandırma | Microsoft Docs'
description: Azure AD 'den jostle 'ya Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6dbb744f-8b8e-4988-b293-ebe079c8c5c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2021
ms.author: Zhchia
ms.openlocfilehash: d2ab0009f036afa38dc9e401223854a034d45e42
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368721"
---
# <a name="tutorial-configure-jostle-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için jostle 'yi yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem jostle hem de Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmeti ['ni kullanarak kullanıcıları](https://www.jostle.me/) ve grupları otomatik olarak hazırlar ve geri hazırlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen yetenekler
> [!div class="checklist"]
> * Jostle 'da Kullanıcı oluşturma
> * Artık erişim gerektirdiklerinde jostle içindeki kullanıcıları kaldırın
> * Kullanıcı özniteliklerinin Azure AD ile jostle arasında eşitlenmiş olmasını sağlama
> * Jostle ['Da çoklu oturum açma](jostle-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md) 
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı (örneğin, uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici). 
* Bir [jostle kiracısı](https://www.jostle.me/).
* Yönetici izinleriyle jostle içindeki bir kullanıcı hesabı.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama

1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
1. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
1. [Azure AD Ile jostle arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın.

## <a name="step-2-configure-jostle-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için jostle 'yı yapılandırın

### <a name="automation-account"></a>Otomasyon hesabı

Başlamadan önce, jostle intranetinizde bir **Otomasyon kullanıcısı** oluşturmanız gerekecektir. Bu, Azure ile yapılandırmak için kullanacağınız hesaptır. Automation kullanıcıları, **Kullanıcı hesapları ve verileri > Otomasyon kullanıcılarını yönetmek > yönetici ayarlarından** oluşturulabilir.

Otomasyon kullanıcıları ve bir oluşturma hakkında daha fazla bilgi için [Bu makaleye](https://forum.jostle.us/hc/en-us/articles/360057364073)bakın.

Bir kez oluşturduktan sonra, Azure 'u yapılandırmak için kullanılmadan önce Otomasyon Kullanıcı hesabının etkinleştirilmesi (örneğin, intranetinizde en az bir kez oturum açmanız) **gerekir** .

### <a name="manage-user-provisioning"></a>Kullanıcı sağlamayı yönetme

Başlamadan önce, hesap aboneliğinizin **SSO/Kullanıcı hazırlama özelliklerini içerdiğinden** emin olun. Değilse, Müşteri başarı yöneticinize başvurabilirsiniz <success@jostle.me> ve hesabınıza ekleme konusunda size yardımcı olabilir.

Sonraki adım, jostle **API URL 'sini** ve **API anahtarını** edinmektir:

1. Ana gezintiye gidin ve **yönetici ayarları**' na tıklayın.
1. **Diğer sistemlere yönelik kullanıcı verileri** altında, **Kullanıcı sağlamasını Yönet** ' e tıklayın. Burada "Kullanıcı sağlamayı Yönet" i görmüyorsanız ve hesabınızın SSO/Kullanıcı sağlama içerdiğini doğruladıysanız, <support@jostle.me> bu sayfanın yönetici ayarlarınızda etkinleştirilmesi Için desteğe başvurun.
1. **Kullanıcı sağlama API 'si ayrıntıları** bölümünde, **taban URL 'si** alanına gidin, Kopyala düğmesine tıklayın ve URL 'yi daha sonra kolayca erişebileceğiniz bir yere kaydedin.                                                           

   ![Sağlama](media/jostle-provisioning-tutorial/manage-user-provisioning.png)
                
1. Sonra **Yeni anahtar Ekle**... öğesine tıklayın. Bu
1. Aşağıdaki ekranda **Otomasyon Kullanıcı** alanına gidin ve otomasyon kullanıcı hesabınızı seçmek için açılan menüyü kullanın. 

   ![Tümleştirme Hesabı](media/jostle-provisioning-tutorial/select-integration-account.png)                                                                                                                                     
1. **API anahtarını sağlama açıklaması** alanında, anahtarınızı bir ad (örneğin, "Azure") verip **Ekle** düğmesine tıklayın.

1. Anahtarınız oluşturulduktan sonra, bu **dosyayı hemen kaydettiğinizden emin olun** ve URL 'nizi kaydettiğiniz yere kaydedin (çünkü bu, anahtarınızın görüneceği tek zaman olacaktır).                                                               
1. Ardından, Azure 'da tümleştirmeyi yapılandırmak için **API URL 'sini** ve **API anahtarını** kullanacaksınız.
## <a name="step-3-add-jostle-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden jostle ekleme

Azure AD uygulama galerisindeki jostle 'yı ekleyerek jostle sağlama işlemini yönetmeye başlayın. Daha önce SSO için jostle 'yı ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak, başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-gallery-app.md) bakın. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Jostle kullanıcıları ve grupları atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulamada kullanılabilen tek rol varsayılan erişim rolü ise, daha fazla rol eklemek için [uygulama bildirimini güncelleştirebilirsiniz](../develop/howto-add-app-roles-in-azure-ad-apps.md) . 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 


## <a name="step-5-configure-automatic-user-provisioning-to-jostle"></a>5. Adım. Otomatik Kullanıcı sağlamayı jostle 'ya yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni Azure AD 'de Kullanıcı ve grup atamalarına göre jostle uygulamasındaki kullanıcıları ve grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında kılavuzluk eder.

### <a name="to-configure-automatic-user-provisioning-for-jostle-in-azure-ad"></a>Azure AD 'de jostle için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

1. Uygulamalar listesinde **jostle**' yi seçin.

    ![Uygulamalar listesindeki jostle bağlantısı](common/all-applications.png)

1. **Hazırlama** sekmesini seçin.

    ![Hazırlama sekmesi](common/provisioning.png)

1.  **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Sekme otomatik sağlama](common/provisioning-automatic.png)

1. **Yönetici kimlik bilgileri** bölümünde, jostle **kiracı URL 'sini** ve **gizli belirteç** bilgilerini girin. Azure AD 'nin jostle 'ya bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, jostle hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Belirteç](common/provisioning-testconnection-tenanturltoken.png)

1. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin. **Bir hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

1. **Kaydet**’i seçin.

1. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları jostle olarak eşitler**' ı seçin.

1. **Öznitelik eşleme** bölümünde Azure AD 'Den jostle ile eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için jostle içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirirseniz, jostle API 'sinin, bu özniteliğe göre kullanıcıların filtrelenmesini desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** ' i seçin.

   |Öznitelik|Tür|Filtreleme için destekleniyor|
   |---|---|---|
   |userName|Dize|&check;|
   |active|Boole|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |emails[type eq "work"].value|Dize|
   |e-postalar [tür EQ "Personal"]. değer|Dize|
   |e-postalar [tür EQ "alternate1"]. değer|Dize|
   |e-postalar [tür EQ "alternate2"]. değer|Dize|  
   |urn: IETF: params: Scim: schemas: Extension: jostle: 2.0: Kullanıcı: alternateEmail1Label|Dize|
   |urn: IETF: params: Scim: schemas: Extension: jostle: 2.0: Kullanıcı: alternateEmail2Label |Dize|  

1. Kapsam filtrelerini yapılandırmak için [kapsam filtresi öğreticisinde](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)sunulan yönergelere bakın.

1. Jostle için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

1. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek jostle sağlamak istediğiniz kullanıcıları veya grupları tanımlayın.

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