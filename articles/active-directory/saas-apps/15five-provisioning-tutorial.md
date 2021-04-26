---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için 15Beş yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı 15 beş 'a öğrenin.
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
ms.openlocfilehash: 00f713e8d319d7ad8dcea014429c57d7fba40541
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181229"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlama için 15Beş yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları [15Beş](https://www.15five.com/pricing/)' a otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak üzere 15 beş ve Azure Active Directory (Azure AD) olarak gerçekleştirilecek adımları göstermektir. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme.

> [!NOTE]
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * 15 beş üzerinde Kullanıcı oluşturma
> * Artık erişim gerektirmeyen 15 beş ' de kullanıcıları kaldırın
> * Kullanıcı özniteliklerinin Azure AD ile 15 beş arasında eşitlenmiş olmasını sağlama
> * 15 beş üzerinde gruplar ve grup üyelikleri sağlama
> * 15 beş [üzerinde çoklu oturum açma](./15five-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md) .
* Azure AD'de hazırlama [iznine](../roles/permissions-reference.md) sahip bir kullanıcı hesabı (Uygulama Yöneticisi, Bulut Uygulaması Yöneticisi, Uygulama Sahibi veya Genel Yönetici).
* [15 beş kiracı](https://www.15five.com/pricing/).
* Yönetici izinlerine sahip 15 beş bir kullanıcı hesabı.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD Ile 15Beş arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın. 

## <a name="step-2-configure-15five-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için 15Beş yapılandırın

Azure AD ile otomatik Kullanıcı sağlama için 15Beş yapılandırmadan önce, 15Beş üzerinde SCıM sağlamasını etkinleştirmeniz gerekir.

1. [15 beş yönetici konsolunda](https://my.15five.com/)oturum açın. **Özellikler > tümleştirmelere** gidin.

    :::image type="content" source="media/15five-provisioning-tutorial/integration.png" alt-text="15 beş yönetici konsolunun ekran görüntüsü. Tümleştirmeler bir menüdeki Özellikler altında görünür ve her iki özellik ve tümleştirmeler vurgulanır." border="false":::

2.  **SCIM 2,0**' ye tıklayın.

    :::image type="content" source="media/15five-provisioning-tutorial/image00.png" alt-text="15 beş Yönetici konsolundaki tümleştirmeler sayfasının ekran görüntüsü. Araç altında, S C I M 2,0 vurgulanır." border="false":::

3.  Bir **OAuth belirteci oluşturmak > SCIM tümleştirmesi**' ne gidin.

    :::image type="content" source="media/15five-provisioning-tutorial/image02.png" alt-text="15 beş Yönetici konsolundaki S C I M tümleştirme sayfasının ekran görüntüsü. OAuth belirteci oluştur vurgulanacaktır." border="false":::

4.  **SCIM 2,0 temel URL 'si** ve **erişim belirtecinin** değerlerini kopyalayın. Bu değer, Azure portal 15Beş uygulamanızın sağlama sekmesinde **kiracı URL 'si** ve **gizli belirteç** alanına girilir.
    
    :::image type="content" source="media/15five-provisioning-tutorial/image03.png" alt-text="S C I M tümleştirme sayfasının ekran görüntüsü. Belirteç tablosunda, S C I M 2,0 Base U R L ve erişim belirtecinin yanındaki değerler vurgulanır." border="false":::

## <a name="step-3-add-15five-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden 15Beş ekleyin

Sağlamayı 15 beş ' a yönetmeye başlamak için Azure AD uygulama galerisinden 15Beş ekleyin. Daha önce SSO için 15Beş ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Kullanıcı ve grupları 15Beş 'a atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz.

## <a name="step-5-configure-automatic-user-provisioning-to-15five"></a>5. Adım. Otomatik Kullanıcı sağlamayı 15 beş olarak yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni, Kullanıcı ve/veya grup atamalarını Azure AD 'de oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD sağlama hizmetini yapılandırma adımlarında kılavuzluk eder.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Azure AD 'de 15Beş için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **15Beş**' u seçin.

    ![Uygulamalar listesindeki 15 beş bağlantı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5.  Yönetici kimlik bilgileri bölümünde, sırasıyla **kiracı URL 'si** ve **gizli belirteç** alanlarında bulunan **SCIM 2,0 temel URL 'sini ve erişim belirteci** değerlerini girin. Azure AD 'nin 155 ' e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, 15 beş hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde, **Azure Active Directory kullanıcılarını 15 beş ile eşitler**' ı seçin.

9. **Öznitelik eşleme** bölümünde Azure AD 'Den 15beş 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için 15 milyon içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.


   |Öznitelik|Tür|
   |---|---|
   |active|Boole|
   |başlık|Dize|
   |emails[type eq "work"].value|Dize|
   |userName|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |externalId|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Manager|Başvuru|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: employeeNumber|Dize|
   |urn: IETF: params: Scim: schemas: Extension: 15Beş: 2.0: Kullanıcı: location|Dize|
   |urn: IETF: params: Scim: schemas: Extension: 15Beş: 2.0: User: startDate|Dize|

10. **Eşlemeler** bölümünde **Azure Active Directory gruplarını 15Beş olarak eşitler**' ı seçin.

11. **Öznitelik eşleme** bölümünde Azure AD 'Den 15beş 'a eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için 15 beş ' deki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

      |Öznitelik|Tür|
      |---|---|
      |externalId|Dize|
      |displayName|Dize|
      |üyeler|Başvuru|

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. Azure AD sağlama hizmeti 'ni 15Beş için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek 15beş 'a sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

    Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer.

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

1. Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
2. Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
3. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.  
    
## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* 15Beş, kullanıcılar için geçici silmeleri desteklemez.

## <a name="change-log"></a>Değişiklik günlüğü

* 06/16/2020-kullanıcılar için "Manager" kuruluş uzantısı özniteliği ve özel öznitelikler "konum" ve "başlangıç tarihi" desteği eklendi.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri incelemeyi ve sağlama etkinliğinde rapor almayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md).