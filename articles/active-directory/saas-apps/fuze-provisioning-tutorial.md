---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için belirsizlik yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını belirsizlik 'e otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: zhchia
ms.openlocfilehash: 809b32fcf6f39ba9dec2980a79e8824e282b54b7
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450098"
---
# <a name="tutorial-configure-fuze-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için belirsizlik yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları [belirsizlik](https://www.fuze.com/)'e otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak Için, belirsizlik ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).

> [!NOTE]
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * Belirsizlik halinde Kullanıcı oluşturma
> * Artık erişim gerektirmeyen kullanıcıları belirsizlik halinde kaldırın
> * Kullanıcı özniteliklerinin Azure AD ve belirsizlik arasında eşitlenmiş olmasını sağlama
> * Belirsizlik için [Çoklu oturum açma](./fuze-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Azure AD kiracısı](../develop/quickstart-create-new-tenant.md).
* Azure AD'de hazırlama [iznine](../roles/permissions-reference.md) sahip bir kullanıcı hesabı (Uygulama Yöneticisi, Bulut Uygulaması Yöneticisi, Uygulama Sahibi veya Genel Yönetici).
* [Bir belirsizlik kiracısı](https://www.fuze.com/).
* Yönetici izinleriyle belirsizlik halinde bir kullanıcı hesabı.


## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD ve belirsizlik arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini belirleme. 

## <a name="step-2-configure-fuze-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için belirsizlik yapılandırma

Azure AD ile otomatik Kullanıcı hazırlama için belirsizlik yapılandırmadan önce, belirsizlik üzerinde SCıM sağlamasını etkinleştirmeniz gerekir. 

1. Aşağıdaki gerekli bilgiler için, belirsizlik temsilcinizle iletişime geçerek başlayın:

    * Şirketinizde kullanılmakta olan belirsizlik Ürün SKU 'Larının listesi.
    * Şirketinizin konumları için konum kodlarının listesi.
    * Şirketiniz için Departman kodlarının listesi.

2. Bu SKU 'Ları ve kodları, belirsizlik ve yapılandırma belgelerinizi veya belirsizlik temsilcinizle iletişime geçerek bulabilirsiniz.

3. Gereksinimler alındıktan sonra, sizin de tümleştirmeyi sağlamak için gerekli olan belirsizlik kimlik doğrulama belirtecini, sizin için belirsizlik temsilciniz sağlar. Bu değer, Azure portal belirsizlik sekmesindeki gizli dizi belirteci alanına girilecektir.

## <a name="step-3-add-fuze-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden belirsizlik ekleyin

Sağlamayı sağlamak için Azure AD uygulama galerisinden belirsizlik ekleyin. Daha önce SSO için belirsizlik ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Kullanıcıları belirsizlik 'e atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkese sunulmadan önce küçük bir Kullanıcı kümesiyle test edin. Sağlama kapsamı atanan kullanıcılara ayarlandığında, uygulamaya bir veya iki Kullanıcı atayarak bunu kontrol edebilirsiniz. Kapsam tüm kullanıcılara ayarlandığında, [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)belirtebilirsiniz. 

## <a name="step-5-configuring-automatic-user-provisioning-to-fuze"></a>5. Adım. Otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak Kullanıcı ve/veya grupları Azure AD 'de Kullanıcı ve/veya grup atamalarına göre yapılandırma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Azure AD 'de belirsizlik için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **belirsizlik**' i seçin.

    ![Uygulamalar listesindeki belirsizlik bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, **kiracı URL 'Si** ve **gizli belirteç** içindeki belirsizlik temsilcisinden daha önce alınan **SCıM 2,0 temel URL 'sini ve SCIM kimlik doğrulama belirteci** değerini girin. Azure AD 'nin belirsizlik 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, belirsizlik hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL belirteci](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları belirsizlik olarak eşitler**' ı seçin.

9. **Öznitelik eşleme** bölümünde Azure AD 'den belirsizlik 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için belirsizlik içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|
   |---|---|
   |userName|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |emails[type eq "work"].value|Dize|
   |active|Boole|

10. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

11. Azure AD sağlama hizmetini belirsizlik için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** Içindeki istenen değerleri seçerek belirsizlik için sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

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

* Belirsizlik, **yetkilendirmeler** ADLı özel SCIM özniteliklerini destekler. Bu öznitelikler yalnızca oluşturulup güncelleştirilemeyebilir. 
* Belirsizlik API 'SI, Kullanıcı adı özniteliğinde filtrelemeyi desteklemez. Sonuç olarak, Kullanıcı adı özniteliği olmayan ancak Azure AD 'de userPrincipalName ile eşleşen bir e-posta ile bulunan mevcut bir kullanıcıyı eşitlemeye çalışırken günlüklerde hatalarla karşılaşabilirsiniz. 

## <a name="change-log"></a>Değişiklik günlüğü

* 06/15/2020-10 istek/saniye olarak ayarlanan tümleştirmenin hız sınırı.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri incelemeyi ve sağlama etkinliğinde rapor almayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md).
