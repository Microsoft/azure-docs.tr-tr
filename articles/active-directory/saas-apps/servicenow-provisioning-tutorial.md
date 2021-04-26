---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için ServiceNow 'ı yapılandırma | Microsoft Docs"
description: Azure AD 'den ServiceNow 'a Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: b3b62e7c16106fd9d94d4a3438331dab4ce8b6e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539054"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için ServiceNow 'ı yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem ServiceNow hem de Azure Active Directory (Azure AD) içinde gerçekleştirdiğiniz adımları açıklamaktadır. Azure AD yapılandırıldığında, Azure AD sağlama hizmeti 'ni kullanarak kullanıcıları ve grupları [ServiceNow](https://www.servicenow.com/) için otomatik olarak sağlar ve hazırlar. 

Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * ServiceNow 'da Kullanıcı oluşturma
> * ServiceNow 'daki kullanıcıları artık erişime gerek duyduklarında kaldırın
> * Kullanıcı özniteliklerinin Azure AD ile ServiceNow arasında eşitlenmiş olmasını sağlama
> * ServiceNow 'da grupları ve grup üyeliklerini sağlama
> * ServiceNow ['da çoklu oturum açmaya](servicenow-tutorial.md) izin ver (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md) 
* Azure AD 'de sağlamayı yapılandırma (uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici) [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı
* Calgary veya üzeri bir [ServiceNow örneği](https://www.servicenow.com/)
* Helsinki veya üzeri bir [ServiceNow Express örneği](https://www.servicenow.com/)
* Yönetim rolüyle ServiceNow 'da bir kullanıcı hesabı

## <a name="step-1-plan-your-provisioning-deployment"></a>1. Adım: sağlama dağıtımınızı planlayın
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD ve ServiceNow arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın. 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>2. Adım: ServiceNow 'ı Azure AD ile sağlamayı destekleyecek şekilde yapılandırma

1. ServiceNow örnek adınızı belirler. Örnek adını ServiceNow 'e erişmek için kullandığınız URL 'de bulabilirsiniz. Aşağıdaki örnekte, örnek adı **dev35214**' dir.

   ![ServiceNow örneğini gösteren ekran görüntüsü.](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. ServiceNow 'da yönetici için kimlik bilgilerini alın. ServiceNow içindeki kullanıcı profiline gidin ve kullanıcının yönetici rolüne sahip olduğunu doğrulayın. 

   ![ServiceNow yönetici rolünü gösteren ekran görüntüsü.](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>3. Adım: Azure AD uygulama galerisinden ServiceNow ekleme

ServiceNow 'ı Azure AD Uygulama Galerisi ' ne ekleyerek ServiceNow 'a sağlamayı yönetmeye başlayın. Çoklu oturum açma (SSO) için ServiceNow ' ı daha önce ayarlarsanız aynı uygulamayı kullanabilirsiniz. Ancak, tümleştirmeyi test ederken ayrı bir uygulama oluşturmanızı öneririz. [Galeriden uygulama ekleme hakkında daha fazla bilgi edinin](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: sağlama için kapsam içinde olacağını tanımlama 

Azure AD sağlama hizmeti, uygulamaya atamaya veya Kullanıcı ya da grubun özniteliklerine göre sağlanacak olan kapsamları kullanmanıza olanak sağlar. Atamaya göre uygulamanıza sağlanacak kapsamı tercih ederseniz, [Uygulamayı kullanıcılara ve gruplara atamak için bu adımları](../manage-apps/assign-user-or-group-access-portal.md)kullanabilirsiniz. Yalnızca Kullanıcı veya grubun özniteliklerine göre sağlanacak olan kapsamı tercih ederseniz, [bir kapsam filtresi kullanabilirsiniz](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Bu ipuçlarını göz önünde bulundurun:

* ServiceNow 'a Kullanıcı ve grup atarken, varsayılan erişim dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulamada kullanılabilen tek rol varsayılan erişim rolü ise, daha fazla rol eklemek için [uygulama bildirimini güncelleştirebilirsiniz](../develop/howto-add-app-roles-in-azure-ad-apps.md) . 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Sağlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında, uygulamaya bir veya iki kullanıcı veya grup atayarak bunu kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında, [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)belirtebilirsiniz. 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>5. Adım: ServiceNow için otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölüm, TestApp içinde kullanıcıları ve grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD sağlama hizmetini yapılandırma adımlarında size rehberlik eder. Azure AD 'de Kullanıcı ve grup atamalarında yapılandırmayı temel alabilirsiniz.

Azure AD 'de ServiceNow için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar bölmesini gösteren ekran görüntüsü.](common/enterprise-applications.png)

2. Uygulamalar listesinde **ServiceNow**' ı seçin.

    ![Uygulamaların listesini gösteren ekran görüntüsü.](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Sağlama modunu** **Otomatik** olarak ayarlayın.

    ![Otomatik seçenek olarak adlandırılan sağlama modu açılır listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde ServiceNow yönetici kimlik bilgilerinizi ve Kullanıcı adınızı girin. Azure AD 'nin ServiceNow 'a bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, ServiceNow hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Yönetici kimlik bilgilerini girebileceğiniz hizmet sağlama sayfasını gösteren ekran görüntüsü.](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. **Bildirim e-postası** kutusunda, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin. Sonra **bir hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim e-postası için kutuları gösteren ekran görüntüsü.](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları ServiceNow olarak eşitler**' ı seçin.

9. **Öznitelik eşleme** bölümünde Azure AD 'Den ServiceNow 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için ServiceNow içindeki kullanıcı hesaplarıyla eşleştirmek için kullanılır. 

   [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz ServiceNow API 'sinin, bu özniteliğe göre kullanıcıların filtrelemesini desteklediğinden emin olmanız gerekir. 
   
   Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

10. **Eşlemeler** bölümünde, **Azure Active Directory gruplarını ServiceNow olarak eşitler**' ı seçin.

11. **Öznitelik eşleme** bölümünde Azure AD 'Den ServiceNow 'a eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, ServiceNow güncelleştirme işlemlerinde grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

12. Kapsam filtrelerini yapılandırmak için [kapsam filtresi öğreticisindeki](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)yönergelere bakın.

13. ServiceNow için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumunun üzerinde geçiş olduğunu gösteren ekran görüntüsü.](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içinde Istenen değerleri seçerek ServiceNow 'a sağlamak istediğiniz kullanıcıları ve grupları tanımlayın.

    ![Sağlama kapsamı seçeneklerini gösteren ekran görüntüsü.](common/provisioning-scope.png)

15. Sağlamaya hazırsanız **Kaydet**' i seçin.

    ![Sağlama yapılandırmasını kaydetme düğmesinin ekran görüntüsü.](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngü sonraki Döngülerde daha uzun sürer. Azure AD sağlama hizmeti çalıştığı sürece sonraki döngüler her 40 dakikada bir gerçekleşir. 

## <a name="step-6-monitor-your-deployment"></a>6. Adım: dağıtımınızı Izleme
Sağlamayı yapılandırdıktan sonra, dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

- Hangi kullanıcıların başarıyla sağlandığını veya başarısız olduğunu öğrenmek için [sağlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın.
- Sağlama döngüsünün durumunu ve ne kadar kapanmasının tamamlanmasının nasıl yapıldığını görmek için [ilerleme çubuğunu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) kontrol edin.
- Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. [Karantina durumları hakkında daha fazla bilgi edinin](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>Sorun giderme ipuçları
* ServiceNow içinde belirli öznitelikleri (örneğin, **bölüm** ve **konum**) sağlıyorsanız, bu değerler ServiceNow içindeki bir başvuru tablosunda zaten olmalıdır. Aksi takdirde, bir **ınvalidlookupreference** hatası alırsınız. 

   Örneğin, ServiceNow 'daki belirli bir tabloda iki konumunuz (Seattle, Los Angeles) ve üç departman (Sales, finans, pazarlama) olabilir. Departmanı "Sales" ve konumu "Seattle" olan bir Kullanıcı sağlamaya çalışırsanız, bu kullanıcı başarılı bir şekilde temin edilir. Departmanı "Sales" ve konumu "LA" olan bir Kullanıcı sağlamaya çalışırsanız, Kullanıcı sağlanmayacaktır. ServiceNow 'daki başvuru tablosuna "LA" konumu eklenmelidir, ya da Azure AD 'deki User özniteliği ServiceNow ile eşleşecek şekilde güncelleştirilmeleri gerekir. 
* Bir **Entryjoiningpropertyvalueısmissing** hatası alırsanız, eşleşen özniteliği tanımlamak için [öznitelik eşlemelerinizi](../app-provisioning/customize-application-attributes.md) gözden geçirin. Sağlamaya çalıştığınız kullanıcı veya grupta bu değer bulunmalıdır. 
* Tüm gereksinimleri veya sınırlamaları anlamak için (örneğin, bir kullanıcı için bir ülke kodu belirtme biçimi) [ServiceNow soap API 'sini](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html)gözden geçirin.
* Sağlama istekleri varsayılan olarak https://{-örnek-adı}. Service-Now. com/{Table-Name} olarak gönderilir. Özel bir kiracı URL 'SI gerekiyorsa, örnek adı olarak tüm URL 'YI sağlayabilirsiniz.
* **ServiceNowInstanceInvalid** hatası ServiceNow örneğiyle iletişim kurmada bir sorun olduğunu gösterir. Hatanın metni aşağıda verilmiştir:
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

  Test bağlantı sorunlarıyla karşılaşıyorsanız ServiceNow 'da aşağıdaki ayarlar için **Hayır** ' ı seçmeyi deneyin:
   
  - **Sistem güvenliği**  >  **Yüksek güvenlik ayarları**  >  **Gelen şema istekleri için temel kimlik doğrulaması gerektir**
  - **Sistem Özellikleri**  >  **Web Hizmetleri**  >  **Gelen SOAP istekleri için temel yetkilendirme iste**

     ![SOAP isteklerini yetkilendirme seçeneğini gösteren ekran görüntüsü.](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

  Hala sorununuzu çözemezseniz ServiceNow desteği 'ne başvurun ve sorun gidermeye yardımcı olması için SOAP hata ayıklamayı etkinleştirmesini isteyin. 

* Azure AD sağlama hizmeti şu anda belirli [IP aralıkları](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges)altında çalışır. Gerekirse, diğer IP aralıklarını kısıtlayabilir ve bu belirli IP aralıklarını uygulamanızın izin verilenler listesine ekleyebilirsiniz. Bu teknik, Azure AD sağlama hizmeti 'nden uygulamanıza trafik akışına izin verir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
