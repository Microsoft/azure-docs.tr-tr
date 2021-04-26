---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için AlertMedia yapılandırma | Microsoft Docs'
description: Azure AD 'den AlertMedia 'e Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: a5df0dd7-05a3-4744-9d51-ec33e89a934f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: Zhchia
ms.openlocfilehash: b15b20ee8e643dc260578d17913810aaf68cc929
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181110"
---
# <a name="tutorial-configure-alertmedia-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için AlertMedia yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem AlertMedia hem de Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmetini kullanarak Kullanıcı ve grupları [Alertmedia](https://www.alertmedia.com/) 'e otomatik olarak sağlar ve hazırlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * Alertmedyada Kullanıcı oluşturma
> * AlertMedia 'daki kullanıcıları artık erişim gerektirdiklerinde kaldırın
> * Kullanıcı özniteliklerinin Azure AD ile AlertMedia arasında eşitlenmiş olmasını sağlama
> * AlertMedia 'te grupları ve grup üyeliklerini sağlama
> * Alertmedyada [Çoklu oturum açma](./alertmedia-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir [Azure AD kiracısı](../develop/quickstart-create-new-tenant.md).
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı (örneğin, uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici). 
* Bir [Alertmedia kiracısı](https://dashboard.alertmedia.com/#/login).
* Bir API Tümleştirmesi yapılandırmak için yönetici izinlerine sahip AlertMedia 'teki bir kullanıcı hesabı.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD Ile AlertMedia arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın. 

## <a name="step-2-configure-alertmedia-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için AlertMedia 'ı yapılandırma

1. AlertMedia hesabınızda oturum açın. **Şirket > API**'sine gidin.
2. **Yeni Ekle**' ye tıklayın.
3. Anahtarların nerede kullanıldığını kolayca tanımanıza yardımcı olması için **API tümleştirmenizi** bir ad vermek için seçin.
4. Tümleştirmeyi ilişkilendirmek istediğiniz yöneticiyi seçin.
5. **Anahtar oluştur** ve **Kaydet** düğmesine tıklayın.
6. **Istemci belirtecini** tümleştirmeden kopyalayın ve kaydedin. Bu, Azure portal AlertMedia uygulamanızın sağlama sekmesinde **gizli belirteç** olarak kullanılır.


## <a name="step-3-add-alertmedia-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden AlertMedia ekleme

Azure AD uygulama galerisinden AlertMedia ' i ekleyerek AlertMedia sağlama işlemini yönetmeye başlayın. Daha önce, SSO için Alertmedıa ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Alertmedyaya Kullanıcı ve grup atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 


## <a name="step-5-configure-automatic-user-provisioning-to-alertmedia"></a>5. Adım. AlertMedia için otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak TestApp içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan bir adım adım yol gösterir.

### <a name="to-configure-automatic-user-provisioning-for-alertmedia-in-azure-ad"></a>Azure AD 'de AlertMedia için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Alertmedia**' i seçin.

    ![Uygulamalar listesindeki AlertMedia bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Hazırlama sekmesi](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Sekme otomatik sağlama](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, AlertMedia **kiracı URL** 'nizi aşağıdakilerden biri olarak girin.
      * (özel etki alanı yok) `https://dashboard.alertmedia.com/api/scim/v3`

      * (özel etki alanı) `https://subdomain.alertmedia.com/api/scim/v3`

      **Gizli anahtarı** adım 2 ' de daha önce alındı olarak girin. Azure AD 'nin AlertMedia 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, AlertMedia hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

      ![Belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları alertmedia ile eşitler**' ı seçin.

9. **Öznitelik eşleme** bölümünde Azure AD 'Den AlertMedia ile eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için AlertMedia içindeki kullanıcı hesaplarıyla eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, AlertMedia API 'sinin kullanıcıları bu özniteliğe göre filtrelemeyi desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|
   |---|---|
   |userName|Dize|
   |active|Boole|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: first_name|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: last_name|Dize|
   |urn: IETF: params: SCIM: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: e-posta|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: Email2|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: Email3|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: title|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: mobile_phone|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: mobile_phone_post_dial|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: mobile_phone2|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: mobile_phone2_post_dial|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: mobile_phone3|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: mobile_phone3_post_dial|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: home_phone|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: home_phone_post_dial|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: office_phone|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: office_phone_post_dial|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: adres|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: Address2|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: City|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: durum|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: ülke|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: ZipCode|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: notlar|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: customer_user_id|Dize|
   |urn: IETF: params: Scim: schemas: Extension: alertmedia: 2.0: CustomAttribute: Kullanıcı: user_type|Dize|

10. **Eşlemeler** bölümünde **Azure Active Directory grupları alertmedia ile eşitler**' ı seçin.

11. **Öznitelik eşleme** bölümünde Azure AD 'Den AlertMedia ile eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için AlertMedia içindeki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

      |Öznitelik|Tür|
      |---|---|
      |displayName|Dize|
      |üyeler|Başvuru|

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. AlertMedia için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek alertmediaalertmedia için sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer. 

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

1. Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
2. Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
3. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)