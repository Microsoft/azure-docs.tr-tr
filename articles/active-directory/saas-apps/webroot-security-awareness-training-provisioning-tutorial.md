---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Webroot güvenlik tanıma eğitimini yapılandırma | Microsoft Docs'
description: Azure AD 'den Webroot Security tanıma eğitimine Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 455f4396-930e-4db5-a167-d3ea6a860a17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: Zhchia
ms.openlocfilehash: f015718c59178b8094f535280edadd641d22a806
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741936"
---
# <a name="tutorial-configure-webroot-security-awareness-training-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için Webroot güvenlik tanıma eğitimini yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem Webroot Security tanıma eğitiminde hem de Azure Active Directory (Azure AD) gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, kullanıcıları ve grupları Azure AD sağlama hizmeti kullanarak [Webroot güvenlik tanıma eğitimine](https://www.webroot.com/) otomatik olarak sağlar ve hazırlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * Webroot güvenlik tanıma eğitiminde Kullanıcı oluşturma
> * Webroot güvenlik tanıma eğitiminde kullanıcıları artık erişim gerektirdiklerinde kaldırın
> * Kullanıcı özniteliklerinin Azure AD ile Webroot güvenlik tanıma eğitimi arasında eşitlenmiş olmasını sağlama
> * Webroot güvenlik tanıma eğitiminde grupları ve grup üyeliklerini sağlama

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md) 
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı (örneğin, uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici).
* En az bir adet sitelerinizden biri için, Webroot güvenlik tanıma Eğitimi özellikli yönetilen bir hizmet sağlayıcı konsolu.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD Ile Webroot güvenlik tanıma eğitimi arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini belirleme. 

## <a name="step-2-configure-webroot-security-awareness-training-to-support-provisioning-with-azure-ad"></a>Adım 2. Webroot Security tanıma eğitimini Azure AD ile sağlamayı destekleyecek şekilde yapılandırma

### <a name="obtain-a-secret-token"></a>Gizli dizi belirteci al

Sitenizi Azure AD 'ye bağlamak için, Webroot yönetim konsolundaki bu site için **gizli bir belirteç** edinmeniz gerekir.

1. [Webroot yönetim konsolunuzun](https://identity.webrootanywhere.com/v1/Account/login#tab_customers) oturumunu açın

2. **Siteler** sekmesinde, Azure AD ile bağlanmak Istediğiniz sitenin güvenlik tanıma Eğitimi sütunundaki dişli simgesine tıklayın.

    ![Dişli simgesi](./media/webroot-security-awareness-training-provisioning-tutorial/gear-icon.png)

3. **Azure AD tümleştirmesini yapılandırmak** için düğmeye tıklayın.

    ![Azure AD tümleştirmesini yapılandırma](./media/webroot-security-awareness-training-provisioning-tutorial/configure-azure-ad-integration.png)

4. **Gizli belirteç** kopyalayın ve kaydedin. Bu değer, Azure portal Webroot güvenlik tanıma eğitim uygulamanızın sağlama sekmesindeki gizli belirteç alanına girilir.

5. **Bitti**’ye tıklayın.

    ![Gizli dizi belirtecini Kopyala](./media/webroot-security-awareness-training-provisioning-tutorial/copy-secret-token.png)

## <a name="step-3-add-webroot-security-awareness-training-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden Webroot güvenlik tanıma Eğitimi ekleme

Azure AD uygulama galerisinden Webroot Security tanıma Eğitimi ekleyerek Webroot güvenlik tanıma eğitimine sağlamayı yönetmeye başlayın. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Webroot güvenlik tanıma eğitimine Kullanıcı ve grup atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 


## <a name="step-5-configure-automatic-user-provisioning-to-webroot-security-awareness-training"></a>5. Adım. Webroot güvenlik tanıma eğitimine otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak TestApp içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan bir adım adım yol gösterir.

### <a name="to-configure-automatic-user-provisioning-for-webroot-security-awareness-training-in-azure-ad"></a>Azure AD 'de Webroot güvenlik tanıma eğitiminde otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Webroot güvenlik tanıma Eğitimi**' ni seçin.

    ![Uygulamalar listesindeki Webroot güvenlik tanıma Eğitimi bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, `https://awarenessapi.webrootanywhere.com/api/v2/scim` **kiracı URL 'sini** girin. Gizli belirteç değerini **gizli belirteçte** daha önce alındı olarak girin. Azure AD 'nin Webroot güvenlik tanıma eğitimine bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Webroot güvenlik tanıma eğitim hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Ekran görüntüsü, kiracı U R L ve gizli belirteç girebileceğiniz yönetici kimlik bilgileri iletişim kutusunu gösterir.](./media/webroot-security-awareness-training-provisioning-tutorial/provisioning.png)

6. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları sağla**' yı seçin.

9. **Öznitelik eşleme** bölümünde Azure AD 'Den Webroot güvenlik tanıma eğitimine eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Webroot güvenlik tanıma eğitiminde Kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, Webroot güvenlik tanıma Eğitimi API 'sinin kullanıcıları bu özniteliğe göre filtrelemeyi desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|Filtreleme için destekleniyor|
   |---|---|---|
   |userName|Dize|&check;|
   |externalId|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |emails[type eq "work"].value|Dize|

10. **Eşlemeler** bölümünde **Azure Active Directory grupları sağla**' yı seçin.

11. **Öznitelik eşleme** bölümünde Azure AD 'Den Webroot güvenlik tanıma eğitimine eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Webroot güvenlik tanıma eğitiminde grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

      |Öznitelik|Tür|Filtreleme için destekleniyor|
      |---|---|---|
      |displayName|Dize|&check;|
      |üyeler|Başvuru|
      |externalId|Dize|

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. Webroot güvenlik tanıma eğitimi için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsamda** Istenen değerleri seçerek Webroot güvenlik tanıma eğitimine sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer. 

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

1. Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
2. Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
3. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.

## <a name="change-log"></a>Değişiklik günlüğü
* 1/21/2021-kullanıcılar için "userName" çekirdek özniteliği için destek eklendi.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)