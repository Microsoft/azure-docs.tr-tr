---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Smartsheet yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı Smartsheet 'e öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: e9ee994564e175d3c41cfd5ce415ead8c67df353
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103592"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Smartsheet yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları [Smartsheet](https://www.smartsheet.com/pricing)'e otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak Için Smartsheet ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * Smartsheet 'te Kullanıcı oluşturma
> * Artık erişim gerektirmeyen kullanıcıları Smartsheet 'e kaldır
> * Kullanıcı özniteliklerinin Azure AD ile Smartsheet arasında eşitlenmiş olmasını sağlama
> * Smartsheet 'te çoklu oturum açma (önerilir)

> [!NOTE]
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Azure AD kiracısı](../develop/quickstart-create-new-tenant.md).
* Azure AD'de hazırlama [iznine](../roles/permissions-reference.md) sahip bir kullanıcı hesabı (Uygulama Yöneticisi, Bulut Uygulaması Yöneticisi, Uygulama Sahibi veya Genel Yönetici).
* [Bir Smartsheet kiracısı](https://www.smartsheet.com/pricing).
* Bir Smartsheet kurumsal veya kurumsal Premier planında Sistem Yöneticisi izinlerine sahip bir kullanıcı hesabı.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD Ile Smartsheet arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın. 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için Smartsheet yapılandırma

Azure AD ile otomatik Kullanıcı sağlama için Smartsheet 'i yapılandırmadan önce, Smartsheet üzerinde SCıM sağlamasını etkinleştirmeniz gerekir.

1. **[Smartsheet portalında](https://app.smartsheet.com/b/home)** **sysadmin** olarak oturum açın ve **Hesap Yöneticisi**' ne gidin.

    ![Smartsheet Hesap Yöneticisi](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. **Kullanıcı otomatik sağlama > Düzenle ' > güvenlik denetimlerine** gidin.

    ![Smartsheet güvenlik denetimleri](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Azure AD 'den Smartsheet 'e sağlamayı planladığınız kullanıcıların e-posta etki alanlarını ekleyin ve doğrulayın. Tüm sağlama eylemlerinin yalnızca Azure AD 'den kaynaklanmasından emin olmak ve ayrıca Smartsheet Kullanıcı listenizin Azure AD atamalarıyla eşitlenmiş olduğundan emin olmak için **etkin değil** ' i seçin.

    ![Smartsheet Kullanıcı sağlama](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Doğrulama tamamlandıktan sonra etki alanını etkinleştirmeniz gerekir. 

    ![Smartsheet etki alanını etkinleştir](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. **Uygulamalar ve tümleştirmelere** gıderek Azure AD ile otomatik Kullanıcı sağlamayı yapılandırmak Için gereken **gizli dizi belirtecini** oluşturun.

    ![Kullanıcı avatar ve uygulamalar & tümleştirme seçeneği olarak adlandırılan Smartsheet Yönetim sayfasının ekran görüntüsü.](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. **API erişimi** seçin. **Yeni erişim belirteci oluştur**' a tıklayın.

    ![API erişimi olan kişisel ayarlar iletişim kutusunun ekran görüntüsü ve olarak adlandırılan yeni erişim belirteci seçenekleri oluştur.](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. API erişim belirtecinin adını tanımlayın. **Tamam**'a tıklayın.

    ![Adım 1 ' in ekran görüntüsü: Tamam seçeneğiyle birlikte API erişim belirteci oluşturma.](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. API erişim belirtecini kopyalayın ve tek bir zaman görüntüleyebilmeniz için bunu kaydedin. Bu, Azure AD 'deki **gizli belirteç** alanında gereklidir.

    ![Smartsheet belirteci](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden Smartsheet ekleme

Akıllı sayfa sağlamayı yönetmeye başlamak için Azure AD uygulama galerisinden Smartsheet ekleyin. Daha önceden SSO için Smartsheet ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Kullanıcıları ve grupları Smartsheet 'e atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Smartsheet ve Azure AD arasında Kullanıcı rolü atamalarında eşlik sağlamak için, tam Smartsheet Kullanıcı listesinde doldurulmuş rol atamalarının aynısını kullanmanız önerilir. Bu Kullanıcı listesini Smartsheet 'ten almak için **hesap yöneticisi > Kullanıcı yönetimi > daha fazla eylem > Kullanıcı Listesi (CSV) yükle**' ye gidin.

* Uygulamadaki belirli özelliklere erişmek için, Smartsheet bir kullanıcının birden çok rolüne sahip olmasını gerektirir. Smartsheet 'teki Kullanıcı türleri ve izinler hakkında daha fazla bilgi edinmek için [Kullanıcı türleri ve izinler](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)' e gidin.

*  Smartsheet 'te bir kullanıcının birden çok rolü atanmışsa, kullanıcıların Smartsheet nesnelerine kalıcı olarak erişimini kaybedebileceği bir senaryoya engel olmak için bu rol atamalarının Azure AD 'de çoğaltılmasını **sağlamalısınız** . Smartsheet 'teki her benzersiz rolün Azure AD 'de farklı bir gruba atanması **gerekir** . Daha sonra Kullanıcı, istenen rollere karşılık gelen grupların her **birine eklenmelidir.** 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>5. Adım. Otomatik Kullanıcı sağlamasını Smartsheet 'e yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni kullanarak Smartsheet 'te kullanıcıları ve/veya grupları Azure AD 'de Kullanıcı ve/veya grup atamalarına göre oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Azure AD 'de Smartsheet için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Smartsheet**' i seçin.

    ![Uygulamalar listesindeki Smartsheet bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümü altında, daha önce **kiracı URL 'sindeki** Smartsheet ve **gizli belirteç** sırasıyla bulunan **SCIM 2,0 temel URL 'sini ve erişim belirteci** değerlerini girin. Azure AD 'nin Smartsheet 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Smartsheet hesabınızın SysAdmin izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Smartsheet ile eşitler**' ı seçin.

9. **Öznitelik eşleme** bölümünde Azure AD 'Den Smartsheet 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Smartsheet içindeki kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|Filtreleme için destekleniyor|
   |---|---|---|
   |userName|Dize|&check;|
   |active|Boole|
   |başlık|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |phoneNumbers[type eq "work"].value|Dize|
   |phoneNumbers[type eq "mobile"].value|Dize|
   |phoneNumbers [tür EQ "Faks"]. değer|Dize|
   |emails[type eq "work"].value|Dize|
   |externalId|Dize|
   |roller|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Department|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Bölüm|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: costCenter|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Manager|Dize|


10. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

11. Smartsheet için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek Smartsheet 'e sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

13. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. 

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

1. Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
2. Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
3. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.  

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Smartsheet, geçici silmeleri desteklemez. Bir kullanıcının **etkin** özniteliği false olarak ayarlandığında, Smartsheet kullanıcıyı kalıcı olarak siler.

## <a name="change-log"></a>Değişiklik günlüğü

* 06/16/2020-kullanıcılar için "maliyet merkezi", "bölme", "Yönetici" ve "Departman" Kurumsal uzantı özniteliklerine yönelik destek eklendi.
* 02/10/2021-kullanıcılar için "e-posta [tür EQ" Work "]" temel özniteliklerine yönelik destek eklendi.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)