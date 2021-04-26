---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Oracle bulut altyapı konsolunu yapılandırma | Microsoft Docs'
description: Azure AD 'den Oracle bulut altyapısı konsoluna Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 94de0ca0a5393c891e567e558cbbadd0ca1f453b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97832100"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Oracle bulut altyapı konsolunu yapılandırma

Bu öğreticide, otomatik Kullanıcı sağlamayı yapılandırmak için hem Oracle bulut altyapısı konsolunda hem de Azure Active Directory (Azure AD) gerçekleştirmeniz gereken adımlar açıklanmaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmetini kullanarak kullanıcıları ve grupları [Oracle bulut altyapısı konsoluna](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) otomatik olarak sağlar ve hazırlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * Oracle bulut altyapısı konsolunda kullanıcı oluşturma
> * Oracle bulut altyapısı konsolundaki kullanıcıları artık erişim gerektirdiklerinde kaldırın
> * Kullanıcı özniteliklerinin Azure AD ile Oracle bulut altyapı konsolu arasında eşitlenmiş olmasını sağlama
> * Oracle bulut altyapısı konsolunda grupları ve grup üyeliklerini sağlama
> * Oracle bulut altyapı konsolu 'nda [Çoklu oturum açma](./oracle-cloud-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md) 
* Azure AD'de hazırlama [iznine](../roles/permissions-reference.md) sahip bir kullanıcı hesabı (Uygulama Yöneticisi, Bulut Uygulaması Yöneticisi, Uygulama Sahibi veya Genel Yönetici). 
* Oracle bulut altyapısı konsol [kiracısı](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton).
* Oracle bulut altyapısı konsolundaki yönetici izinleriyle bir kullanıcı hesabı.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD Ile Oracle bulut altyapısı konsolu arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın. 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Adım 2. Oracle bulut altyapı konsolunu Azure AD ile sağlamayı destekleyecek şekilde yapılandırma

1. Oracle bulut altyapısı konsolunun yönetici portalında oturum açın. Ekranın sol üst köşesinde **kimlik > Federasyonu**' ne gidin.

    ![Oracle Yöneticisi](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Oracle Identity Cloud Service konsolunun yanındaki sayfada görünen URL 'ye tıklayın.

    ![Oracle URL 'SI](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Yeni bir kimlik sağlayıcısı oluşturmak için **kimlik sağlayıcısı ekle** ' ye tıklayın. Kiracı URL 'sinin bir parçası olarak kullanılacak IDP kimliğini kaydedin. Bir OAuth Istemcisi oluşturmak ve ıDCS kimlik alanı yöneticisi AppRole sağlamak için **uygulamalar** sekmesinin yanındaki artı simgesine tıklayın.

    ![Oracle bulut simgesi](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Uygulamanızı yapılandırmak için aşağıdaki ekran görüntülerini izleyin. Yapılandırma tamamlandıktan sonra **Kaydet**' e tıklayın.

    ![Oracle yapılandırması](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Oracle belirteci verme Ilkesi](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Uygulamanızın konfigürasyonlar sekmesinde, istemci KIMLIĞINI ve istemci gizli anahtarını almak için **genel bilgiler** seçeneğini genişletin.

    ![Oracle belirteci oluşturma](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Gizli bir belirteç oluşturmak için istemci KIMLIĞINI ve istemci gizli anahtarını istemci **kimliği: Istemci parolası** biçiminde kodlayın. Gizli dizi belirtecini kaydedin. Bu değer, Azure portal Oracle bulut altyapısı konsol uygulamanızın sağlama sekmesinde bulunan **gizli dizi belirteci** alanına girilecektir.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD Uygulama Galerisi 'nden Oracle bulut altyapısı konsolunu ekleme

Oracle bulut altyapısı konsoluna sağlamayı yönetmeye başlamak için Azure AD uygulama galerisinden Oracle bulut altyapısı konsolunu ekleyin. Daha önce, SSO için Oracle bulut altyapısı konsolunu ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Oracle bulut altyapısı konsoluna Kullanıcı ve grup atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>5. Adım. Oracle bulut altyapısı konsoluna otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak TestApp içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan bir adım adım yol gösterir.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Azure AD 'de Oracle bulut altyapısı konsoluna yönelik otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Oracle Cloud Infrastructure konsolu**' nu seçin.

    ![Uygulamalar listesinde Oracle Cloud Infrastructure konsol bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde **kiracı URL** 'sini biçimde girin `https://<IdP ID>.identity.oraclecloud.com/admin/v1` . Örneğin, `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Gizli belirteç değerini **gizli belirteçte** daha önce alındı olarak girin. Azure AD 'nin Oracle bulut altyapısı konsoluna bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Oracle Cloud Infrastructure konsolu hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Ekran görüntüsü, kiracı U R L ve gizli belirteç girebileceğiniz yönetici kimlik bilgileri iletişim kutusunu gösterir.](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Oracle bulut altyapısı konsolu ile eşitler**' ı seçin.

9. **Öznitelik eşleme** bölümünde, Azure AD 'Den Oracle bulut altyapısı konsoluna eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Oracle bulut altyapısı konsolundaki Kullanıcı hesaplarıyla eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, Oracle bulut altyapısı konsolu API 'sinin, bu özniteliğe göre kullanıcıların filtrelemesini desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    |Öznitelik|Tür|
    |---|---|
    |displayName|Dize|
    |userName|Dize|
    |active|Boole|
    |başlık|Dize|
    |emails[type eq "work"].value|Dize|
    |preferredLanguage|Dize|
    |name.givenName|Dize|
    |name.familyName|Dize|
    |adresler [tür EQ "iş"]. biçimlendirildi|Dize|
    |adresler [tür EQ "iş"]. konum|Dize|
    |adresler [tür EQ "iş"]. bölge|Dize|
    |adresler [tür EQ "iş"]. PostaKodu|Dize|
    |adresler [tür EQ "iş"]. ülke|Dize|
    |adresler [tür EQ "Work"]. streetAddress|Dize|
    |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: employeeNumber|Dize|
    |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Department|Dize|
    |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: costCenter|Dize|
    |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Bölüm|Dize|
    |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Manager|Başvuru|
    |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Organization|Dize|
    |urn: IETF: params: Scim: schemas: Oracle: IDCs: Extension: User: User: bypassNotification|Boole|
    |urn: IETF: params: Scim: schemas: Oracle: IDCs: Extension: User: User: ısfederateduser|Boole|

10. **Eşlemeler** bölümünde, **Azure Active Directory grupları Oracle bulut altyapısı konsolu olarak eşitler**' ı seçin.

11. **Öznitelik eşleme** bölümünde, Azure AD 'Den Oracle bulut altyapısı konsoluna eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Oracle bulut altyapısı konsolundaki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    | Öznitelik | Tür |
    |--|--|
    | displayName | Dize |
    | externalId | Dize |
    | üyeler | Başvuru |

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. Oracle bulut altyapısı konsolu için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içinde Istenen değerleri seçerek Oracle bulut altyapısı konsoluna sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer. 

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

* Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
* Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
* Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
