---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için kar tanesi yapılandırma | Microsoft Docs'
description: Azure Active Directory nasıl yapılandırılacağını ve Kullanıcı hesaplarını kar.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 06f11763498e3e8393d688a71e1c37b466be3f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539544"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için kar ke 'yi yapılandırma

Bu öğreticide, Azure AD 'yi [, kar/](https://www.Snowflake.com/pricing/)Kullanıcı ve grupları otomatik olarak sağlamak ve yeniden sağlamak için, kar ve Azure Active Directory (Azure AD) ' de gerçekleştirdiğiniz adımlar gösterilmektedir. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları öğrenmek için bkz. [Azure AD 'de otomatik SaaS uygulaması Kullanıcı sağlama nedir?](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Kullanım koşulları hakkında bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * Kar halinde Kullanıcı oluşturma
> * Artık erişim gerektirdiklerinde, kar içindeki kullanıcıları kaldırın
> * Kullanıcı özniteliklerinin Azure AD ile kar tanesi arasında eşitlenmiş olmasını sağlama
> * Kar grubundaki grupları ve grup üyeliklerini sağlama
> * Kar için [Çoklu oturum](./snowflake-tutorial.md) açmaya izin ver (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md)
* Azure AD 'de sağlamayı yapılandırma (uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici) [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı
* [Bir kar tanesi kiracısı](https://www.Snowflake.com/pricing/)
* Bir kullanıcı hesabı ile birlikte, yönetici izinleriyle parça

## <a name="step-1-plan-your-provisioning-deployment"></a>1. Adım: sağlama dağıtımınızı planlayın
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD ve kar tanesi arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini belirleme. 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>2. Adım: Azure AD ile sağlama desteği için kar ke 'yi yapılandırma

Azure AD ile otomatik Kullanıcı sağlaması için kar \ ' ı yapılandırmadan önce, sistemi kar üzerinde etki alanları arası kimlik yönetimi (SCıM) sağlama için etkinleştirmeniz gerekir.

1. Kar Yönetici Konsolu 'nda oturum açın. Vurgulanan çalışma sayfasına aşağıdaki sorguyu girin ve sonra **Çalıştır**' ı seçin.

    ![Sorgu ve Çalıştır düğmesi ile kar yönetici konsolunun ekran görüntüsü.](media/Snowflake-provisioning-tutorial/image00.png)

2.  Kar tanesi kiracınız için bir SCıM erişim belirteci oluşturulur. Bunu almak için aşağıdaki ekran görüntüsünde vurgulanan bağlantıyı seçin.

    ![S C I M erişim belirtecine sahip olan kar özellikli çalışma sayfasının ekran görüntüsü.](media/Snowflake-provisioning-tutorial/image01.png)

3. Oluşturulan belirteç değerini kopyalayın ve **bitti**' yi seçin. Bu değer, Azure portal kar uygulama uygulamanızın **sağlama** sekmesindeki **gizli belirteç** kutusuna girilir.

    ![Metin alanına kopyalanmış belirtecin gösterildiği ve bitti seçeneğinin gösterildiği Ayrıntılar bölümünün ekran görüntüsü.](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>3. Adım: Azure AD uygulama galerisinden kar ekleme

Azure AD uygulama galerisindeki kar. Daha önce çoklu oturum açma (SSO) için kar kümesi ayarlarsanız aynı uygulamayı kullanabilirsiniz. Ancak, ilk olarak tümleştirmeyi test ederken ayrı bir uygulama oluşturmanızı öneririz. [Galeriden uygulama ekleme hakkında daha fazla bilgi edinin](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: sağlama için kapsam içinde olacağını tanımlama 

Azure AD sağlama hizmeti, uygulamaya atamaya veya Kullanıcı ya da grubun özniteliklerine göre sağlanacak olan kapsamları kullanmanıza olanak sağlar. Atamaya göre uygulamanıza sağlanacak kapsamı tercih ederseniz, [Uygulamayı kullanıcılara ve gruplara atamak için bu adımları](../manage-apps/assign-user-or-group-access-portal.md)kullanabilirsiniz. Yalnızca Kullanıcı veya grubun özniteliklerine göre sağlanacak olan kapsamı tercih ederseniz, [bir kapsam filtresi kullanabilirsiniz](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Bu ipuçlarını göz önünde bulundurun:

* Kar için Kullanıcı ve grup atarken varsayılan erişim dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulamada kullanılabilen tek rol varsayılan erişim rolü ise, daha fazla rol eklemek için [uygulama bildirimini güncelleştirebilirsiniz](../develop/howto-add-app-roles-in-azure-ad-apps.md) . 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Sağlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında, uygulamaya bir veya iki kullanıcı veya grup atayarak bunu kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında, [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)belirtebilirsiniz. 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>5. Adım: kar tanesi için otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölüm, Azure AD sağlama hizmetini, kar. Kullanıcı ve grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için yapılandırma adımlarında size rehberlik eder. Azure AD 'de Kullanıcı ve grup atamalarında yapılandırmayı temel alabilirsiniz.

Azure AD 'de kar için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar bölmesini gösteren ekran görüntüsü.](common/enterprise-applications.png)

2. Uygulamalar listesinde, **kar tanesi**' ni seçin.

    ![Uygulamaların listesini gösteren ekran görüntüsü.](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Sağlama modunu** **Otomatik** olarak ayarlayın.

    ![Otomatik seçenek olarak adlandırılan sağlama modu açılır listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, sırasıyla **kiracı URL 'Si** ve **gizli belirteç** kutularında daha önce aldığınız SCIM 2,0 temel URL 'sini ve kimlik doğrulama belirtecini girin. 

   Azure AD 'nin kar \ ' a bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, kar. hesabınızda Yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Bağlantıyı Sına düğmesi ile birlikte kiracı U R L ve gizli belirteç kutularını gösteren ekran görüntüsü.](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** kutusunda, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin. Sonra **bir hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim e-postası için kutuları gösteren ekran görüntüsü.](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde, **Azure Active Directory Kullanıcıları kar/at olarak zamanla**' yı seçin.

9. **Öznitelik eşleme** bölümünde, Azure AD 'den kar tanesi ile eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için kar tanesi içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|
   |---|---|
   |active|Boole|
   |displayName|Dize|
   |emails[type eq "work"].value|Dize|
   |userName|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: defaultRole|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: Kullanıcı: defaultWarehouse|Dize|

10. **Eşlemeler** bölümünde **Azure Active Directory gruplarını kar/zaman olarak eşitler**' ı seçin.

11. **Öznitelik eşleme** bölümünde Azure AD 'den kar tanesi ile eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için kar tanesi içindeki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    |Öznitelik|Tür|
    |---|---|
    |displayName|Dize|
    |üyeler|Başvuru|

12. Kapsam filtrelerini yapılandırmak için [kapsam filtresi öğreticisindeki](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)yönergelere bakın.

13. Kar tanesi için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

     ![Sağlama durumunun üzerinde geçiş olduğunu gösteren ekran görüntüsü.](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içindeki istenen değerleri seçerek kar için sağlamak istediğiniz kullanıcıları ve grupları tanımlayın. 

    Bu seçenek kullanılamıyorsa, **yönetici kimlik bilgileri** altında gerekli alanları yapılandırın, **Kaydet**' i seçin ve sayfayı yenileyin. 

     ![Sağlama kapsamı seçeneklerini gösteren ekran görüntüsü.](common/provisioning-scope.png)

15. Sağlamaya hazırsanız **Kaydet**' i seçin.

     ![Sağlama yapılandırmasını kaydetme düğmesinin ekran görüntüsü.](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsamda** tanımlanan tüm Kullanıcı ve grupların ilk eşitlemesini başlatır. İlk eşitlemenin sonraki eşitlemeler daha uzun sürer. Sonraki eşitlemeler, Azure AD sağlama hizmeti çalıştığı sürece her 40 dakikada bir gerçekleşir.

## <a name="step-6-monitor-your-deployment"></a>6. Adım: dağıtımınızı Izleme
Sağlamayı yapılandırdıktan sonra, dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

- Hangi kullanıcıların başarıyla sağlandığını veya başarısız olduğunu öğrenmek için [sağlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın.
- Sağlama döngüsünün durumunu ve ne kadar kapanmasının tamamlanmasının nasıl yapıldığını görmek için [ilerleme çubuğunu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) kontrol edin.
- Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. [Karantina durumları hakkında daha fazla bilgi edinin](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

Kar tanesi tarafından oluşturulan SCıM belirteçleri 6 ay içinde sona erer. Sağlama eşitlerinin çalışmaya devam etmesine izin vermek için, bu belirteçleri kullanım süreleri dolmadan önce yenilemeniz gerektiğini unutmayın. 

## <a name="troubleshooting-tips"></a>Sorun giderme ipuçları

Azure AD sağlama hizmeti şu anda belirli [IP aralıkları](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges)altında çalışır. Gerekirse, diğer IP aralıklarını kısıtlayabilir ve bu belirli IP aralıklarını uygulamanızın izin verilenler listesine ekleyebilirsiniz. Bu teknik, Azure AD sağlama hizmeti 'nden uygulamanıza trafik akışına izin verir.

## <a name="change-log"></a>Değişiklik günlüğü

* 07/21/2020: tüm kullanıcılar için (etkin özniteliği aracılığıyla) geçici silme etkinleştirildi.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar
* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
