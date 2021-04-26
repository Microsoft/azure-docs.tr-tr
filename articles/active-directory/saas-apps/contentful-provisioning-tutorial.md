---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için çekişme yapılandırma'
description: Azure Active Directory (Azure AD) ' den çekişme için Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: zhchia
ms.openlocfilehash: c9d19624d90b1228b2a44caeff7d103af3172ed9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516338"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için çekişmeden yapılandırma

Bu makalede, otomatik Kullanıcı sağlamayı yapılandırmak için çekişmeden ve Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımlar açıklanmaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama [hizmeti 'ni kullanarak](https://www.contentful.com/) kullanıcıları ve grupları otomatik olarak sağlamasını ve sağlamasını kaldırır. Bu hizmetin ne yaptığı ve nasıl çalıştığı hakkında önemli ayrıntılar ve sık sorulan sorular için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Desteklenen özellikler

> [!div class="checklist"]
> * Contente Kullanıcı oluşturma
> * Artık erişim gerektirmeyen kullanıcıları çekişirken kaldırın
> * Kullanıcı özniteliklerinin Azure AD ile çekişme arasında eşitlenmiş olmasını sağlama
> * Contente grupları ve grup üyeliklerini sağlama
> * Çekişme için [Çoklu oturum açma](contentful-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Azure AD kiracısı](../develop/quickstart-create-new-tenant.md). 
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı (örneğin, uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici). 
* Etki alanları arası kimlik yönetimi (SCıM) sağlama için sistemi destekleyen bir aboneliği olan çekişmeden bir kuruluş hesabı. Kuruluşunuzun aboneliği hakkında sorularınız varsa, [çekişme desteğiyle](mailto:support@contentful.com)iletişim kurun.
 
## <a name="plan-your-provisioning-deployment"></a>Hazırlama dağıtımınızı planlama

1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD ve çekişme arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın. 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>Azure AD ile sağlamayı desteklemek için çekişmeden yapılandırma

1. Çekişme ' de bir **hizmet Kullanıcı** hesabı oluşturun. Azure için tüm sağlama izinleri bu hesap üzerinden sağlanır. Bu hesabın kuruluş rolü olarak **sahip** ' i seçmenizi öneririz.

2. **Hizmet kullanıcısı** olarak çekişme 'de oturum açın.

3. Sol taraftaki menüden **kuruluş ayarları**  >  **erişim araçları**  >  **Kullanıcı hazırlama**' yı seçin.

   ![Erişim araçları altında vurgulanan Kullanıcı sağlama ile çekişmeden Kuruluş Ayarları menüsünün ekran görüntüsü.](media/contentful-provisioning-tutorial/access.png)

4. **SCIM URL 'sini** kopyalayıp kaydedin. Bu değeri, Contensaklayacağımız uygulamanızın **sağlama** sekmesinde Azure Portal girersiniz.

5. **Kişisel erişim belirteci oluştur**' u seçin.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. Kalıcı penceresinde, kişisel erişim belirteciniz için bir ad girin ve ardından **Oluştur**' u seçin.

7. SCıM URL 'SI ve gizli belirteç oluşturulur. Bu değerleri kopyalayın ve kaydedin. Bu değerleri, Azure portal çekişme uygulamanızın **sağlama** sekmesine girersiniz.

    ![C F P A T ve belirteç yer tutucusu adı vurgulanmış şekilde kişisel erişim belirteci bölmesinin ekran görüntüsü.](media/contentful-provisioning-tutorial/token.png)


Contentli yönetim konsolunda sağlamayı yapılandırırken sorularınız varsa, [çekişme desteğiyle](mailto:support@contentful.com)iletişim kurun.

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>Azure AD uygulama galerisinden çekişme ekleme

Çekişmeden sağlamayı yönetmek için Azure AD uygulama galerisinden çekişme ekleyin. Çoklu oturum açma için daha önce çekişme ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak, tümleştirmeyi başlangıçta test etmek için ayrı bir uygulama oluşturmanızı öneririz. [Galerideki bir uygulamayı nasıl ekleyeceğinizi](../manage-apps/add-application-portal.md)öğrenin. 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD sağlama hizmeti 'ni, uygulamaya atamaya veya Kullanıcı ya da grubun özniteliklerine göre sağlanacak şekilde verilecek kapsam için kullanabilirsiniz. 

Atamaya göre uygulamanıza sağlanacak kapsamı tercih ederseniz, [uygulamaya Kullanıcı ve Grup atama](../manage-apps/assign-user-or-group-access-portal.md)adımlarını uygulayın.

Yalnızca Kullanıcı veya grup özniteliklerine göre sağlanacak olan kapsamı tercih ederseniz, [Kullanıcı hesaplarını sağlamaya yönelik koşullu kuralları tanımlamak](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)için bir kapsam filtresi kullanın. 

* Kullanıcıları ve grupları çekişme için atadığınızda, **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan erişim rolüne sahip kullanıcılar, sağlanmasından çıkarılır ve sağlama günlüklerinde etkin değil olarak belirtilir. Uygulamada kullanılabilen tek rol varsayılan erişim rolü ise, daha fazla rol eklemek için [uygulama bildirimini güncelleştirebilirsiniz](../develop/howto-add-app-roles-in-azure-ad-apps.md) . 
* Başlangıçta kapsamı sınırlı tutun. Herkese göndermeden önce küçük bir Kullanıcı ve grup kümesi ile test edin. Sağlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında, uygulamaya bir veya iki kullanıcı veya grup atayarak kapsamı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında, [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)belirtebilirsiniz. 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>Otomatik Kullanıcı sağlamasını çekişme için yapılandırma 

Bu bölümde, Azure AD 'de Kullanıcı veya grup atamalarını temel alan bir test uygulamasındaki kullanıcıları ve grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD sağlama hizmetini ayarlama adımlarında size kılavuzluk eder.

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Azure AD 'de çekişme için otomatik Kullanıcı sağlamayı yapılandırma

1. [Azure portalında](https://portal.azure.com) oturum açın. **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.

   ![Tüm uygulamalar vurgulanarak Azure portal kurumsal uygulamalar menüsünü gösteren ekran görüntüsü.](common/enterprise-applications.png)

2. Uygulamalar listesinde, **çekişme**' yi seçin.

   ![Uygulamalar listesinde döndürülen ilk 20 sonucu gösteren ekran görüntüsü.](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

   ![Sol menünün Yönet bölümünde, sağlama sekmesinin ekran görüntüsü.](common/provisioning.png)

4. **Sağlama modunu** **Otomatik** olarak ayarlayın.

   ![Otomatik vurgulanmış şekilde sağlama modu seçeneklerini gösteren ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, çekişme KIRACı URL 'si ve gizli belirteç girin. Azure AD 'nin çekişme 'ye bağlanabildiğinden emin olmak için **Bağlantıyı Sına**' yı seçin. Bağlantı başarısız olursa, çekişme hesabınızın yönetici izinlerine sahip olduğundan emin olun ve sonra yeniden deneyin.

   ![Bağlantıyı Sına düğmesi vurgulanmış şekilde kiracı U R L ve gizli belirteç metin kutularını gösteren ekran görüntüsü.](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası**' nde, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin ve ardından **bir hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

   ![Bildirim e-postası metin kutusunu gösteren ekran görüntüsü.](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları çekişme olarak eşitler**' ı seçin.

9. **Öznitelik eşleme** bölümünde, Azure AD 'den çekişmeye karşı eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için çekişme içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, çekişme API 'sinin, bu özniteliğe göre kullanıcıların filtrelenmesini desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|Filtreleme için destekleniyor|
   |---|---|---|
   |userName|Dize|&check;|
   |name.givenName|Dize|
   |name.familyName|Dize|

10. **Eşlemeler** bölümünde, **Azure Active Directory gruplarını Contenbir şekilde eşitler**' ı seçin.

11. **Öznitelik eşleme** bölümünde, Azure AD 'den çekişmeye karşı eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için çekişme içindeki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    |Öznitelik|Tür|Filtreleme için destekleniyor|
    |---|---|---|
    |displayName|Dize|&check;|
    |üyeler|Başvuru|

12. Kapsam filtrelerini ayarlamak için, [kapsam filtresi öğreticisinde](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)açıklanan adımları doldurun.

13. Azure AD sağlama hizmetini çekişmeden etkinleştirmek için, **Ayarlar** bölümünde, **sağlama durumu** için **Açık**' ı seçin.

    ![Geçiş durumunun açık ve kapalı olduğunu gösteren ekran görüntüsü.](common/provisioning-toggle-on.png)

14. Çekişme için sağlamak istediğiniz kullanıcıları veya grupları tanımlamak için, **Ayarlar** bölümünde **kapsam** için ilgili seçeneği belirleyin.

    ![Kapsam bölmesinde seçebileceğiniz seçenekleri gösteren ekran görüntüsü.](common/provisioning-scope.png)

15. Sağlamaya hazırsanız **Kaydet**' i seçin.

    ![Kaydet düğmesini ve Iptal düğmesini gösteren ekran görüntüsü.](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** altındaki **kapsamda** tanımlanan tüm Kullanıcı ve grupların ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer. 

## <a name="monitor-your-deployment"></a>Dağıtımınızı izleme

Sağlamayı yapılandırdıktan sonra, dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

* Hangi kullanıcıların başarıyla sağlandığını veya başarısız olduğunu anlamak için, [sağlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md)görüntüleyin.
* Sağlama döngüsünün durumunu ve ne kadar yakın olduğunu görmek için [ilerleme çubuğunu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)kontrol edin.
* Sağlama yapılandırması sağlıksız bir durumda görünüyorsa, uygulama karantinaya alınır. [Karantina durumları](../app-provisioning/application-provisioning-quarantine-status.md)hakkında daha fazla bilgi edinin.  

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
