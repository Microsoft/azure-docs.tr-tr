---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Genesys tarafından Pureci 'yi yapılandırma | Microsoft Docs"
description: Genesys tarafından Azure AD 'den Purecm 'ye Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: bbb9b47e42ce195a98801ee08d177efd409c597e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181676"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Genesys tarafından Purecre 'yi yapılandırma

Bu öğreticide, otomatik Kullanıcı sağlamayı yapılandırmak için Genesys ve Azure Active Directory (Azure AD) tarafından hem Purecyüksek hem de gerçekleştirmeniz gereken adımlar açıklanmaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmeti 'ni kullanarak [Genesys tarafından](https://www.genesys.com) Kullanıcı ve grupları otomatik olarak temin etmek ve devre dışı bırakmayı sağlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * Genesys tarafından Pureck 'da Kullanıcı oluşturma
> * Daha önce erişim gerektirdiklerinde Genesys tarafından Pureci 'daki kullanıcıları kaldır
> * Genesys tarafından Azure AD ile Pureck arasında eşitlenmiş Kullanıcı özniteliklerini koruyun
> * Genesys tarafından Purecm 'de grupları ve grup üyeliklerini sağlama
> * Genesys tarafından [Purecg 'de çoklu oturum açma](./purecloud-by-genesys-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md) 
* Azure AD'de hazırlama [iznine](../roles/permissions-reference.md) sahip bir kullanıcı hesabı (Uygulama Yöneticisi, Bulut Uygulaması Yöneticisi, Uygulama Sahibi veya Genel Yönetici). 
* Purecyüksek bir [kuruluş](https://help.mypurecloud.com/?p=81984).
* OAuth Istemcisi oluşturma [izinlerine](https://help.mypurecloud.com/?p=24360) sahip bir kullanıcı.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Genesys tarafından Azure AD Ile Purecre arasında eşlenecek](../app-provisioning/customize-application-attributes.md)verileri belirleme. 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için Genesys tarafından Pureci 'yi yapılandırma

1. Purecyüksek kuruluşunuzda yapılandırılmış bir [OAuth istemcisi](https://help.mypurecloud.com/?p=188023) oluşturun.
2. [OAuth istemcinizle](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html)bir belirteç oluşturun.
3. Purecm içinde Grup üyeliğini otomatik olarak sağlamak istiyorsanız, Azure AD 'deki gruba aynı adı taşıyan purecm 'de [gruplar oluşturmanız](https://help.mypurecloud.com/?p=52397) gerekir.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD Uygulama Galerisi 'nden Genesys tarafından Porecre 'ler ekleyin

Azure AD Uygulama Galerisi 'nden Genesys 'e göre Pureci 'yi Genesys 'e göre Purecre 'e yönetmeye başlamak için bir şekilde ekleyin. SSO için Genesys tarafından daha önce Pureck ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Genesys tarafından Purecm 'ye Kullanıcı ve grup atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>5. Adım. Genesys 'e göre otomatik Kullanıcı sağlamayı otomatik olarak Purecre 'ya yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak TestApp içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan bir adım adım yol gösterir.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Azure AD 'de Genesys tarafından otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Genesys tarafından Purecı**' yi seçin.

    ![Uygulamalar listesindeki Genesys bağlantısına göre Purecme bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, Genesys API URL 'Sine göre Pureck ve **kiracı URL** 'si ve **gizli belirteç** alanlarındaki OAuth belirtecini girin. API URL 'SI `{{API Url}}/api/v2/scim/v2` , [Purecyüksek geliştirici merkezinden Purecim](https://developer.mypurecloud.com/api/rest/index.html)bölgenizin API URL 'si kullanılarak olarak yapılandırılır. Azure AD 'nin Genesys tarafından Purecre 'ya bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Genesys hesabının yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Ekran görüntüsü, kiracı U R L ve gizli belirteç girebileceğiniz yönetici kimlik bilgileri iletişim kutusunu gösterir.](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümü altında, **Genesys tarafından Azure Active Directory Kullanıcıları, Poyana 'yı Pureck olarak eşitler** seçeneğini belirleyin.

9. **Öznitelik eşleme** bölümünde, Azure AD 'Den Maesys tarafından Purecgen 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Genesys tarafından purecr 'teki Kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, Genesys API 'sinin, bu özniteliğe göre kullanıcıların filtrelenmesini desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

     |Öznitelik|Tür|
     |---|---|
     |userName|Dize|
     |active|Boole|
     |displayName|Dize|
     |emails[type eq "work"].value|Dize|
     |başlık|Dize|
     |phoneNumbers[type eq "mobile"].value|Dize|
     |phoneNumbers[type eq "work"].value|Dize|
     |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Department|Dize|
     |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Manager|Başvuru|
     |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: employeeNumber|Dize|
     

10. **Eşlemeler** bölümünde, **Genesys tarafından pureck Ile Azure Active Directory gruplarını eşitler**' ı seçin.

11. **Öznitelik eşleme** bölümünde, Azure AD 'Den Maesys tarafından Purecgen 'a eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Genesys tarafından purecas 'daki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin. Genesys tarafından Purecre, Grup oluşturmayı veya silmeyi desteklemez ve yalnızca grupların güncelleştirilmesini destekler.

      |Öznitelik|Tür|
      |---|---|
      |displayName|Dize|
      |externalId|Dize|
      |üyeler|Başvuru|

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. Azure AD sağlama hizmetini Genesys tarafından Poreck için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsamda** Istenen değerleri seçerek Genesys tarafından purecm 'ye sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer. 

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

* Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
* Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
* Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.

## <a name="change-log"></a>Değişiklik günlüğü

09/10-"employeeNumber" Kurumsal özniteliği için destek eklendi.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)