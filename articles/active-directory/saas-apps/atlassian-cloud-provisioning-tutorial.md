---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Atlasduyma bulutu yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını Atlasme bulutuna otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 691d35267c255c933a8098b99301fbb795a3cd0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181076"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için Atlasduyma bulutu yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları [Atlasme bulutuna](https://www.atlassian.com/licensing/cloud)otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak Için Atlasme bulutu ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * Atlasduyi bulutu 'nda Kullanıcı oluşturma
> * Artık erişim gerektirdiklerinde Atlasbir bulutta kullanıcıları kaldırın
> * Kullanıcı özniteliklerinin Azure AD ile Atlasduyi bulutu arasında eşitlenmiş olmasını sağlama
> * Atlasduyi bulutu 'nda grupları ve grup üyeliklerini sağlama
> * Atlasme bulutu ['Nda çoklu oturum açma](./atlassian-cloud-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Azure AD kiracısı](../develop/quickstart-create-new-tenant.md).
* Azure AD'de hazırlama [iznine](../roles/permissions-reference.md) sahip bir kullanıcı hesabı (Uygulama Yöneticisi, Bulut Uygulaması Yöneticisi, Uygulama Sahibi veya Genel Yönetici).
* [Atlasbir bulut kiracısı](https://www.atlassian.com/licensing/cloud)
* Yönetici izinlerine sahip bir kullanıcı hesabı ile atlasi bulutu.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD Ile Atlasdui bulutu arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini belirleme.

## <a name="step-2-configure-atlassian-cloud-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için Atlasme bulutunu yapılandırma

1. [Atlasme kuruluş yöneticisi](https://admin.atlassian.com) ' ne gidin **> kuruluş > dizinini seçin**.

    ![Dizin seçeneği olarak adlandırılan Yönetim sayfasının ekran görüntüsü.](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

2. **Kullanıcı hazırlama** ' ya tıklayın ve **Dizin Oluştur ' a** tıklayın. Sırasıyla Azure AD portalındaki Atlasme bulut uygulamanızın sağlama sekmesinde bulunan **kiracı URL 'si** ve **gizli belirteç** ALANLARıNA girilecek **Dizin temel URL** 'sini ve **taşıyıcı belirtecini** kopyalayın.

    ![Kullanıcı sağlama seçeneği olarak adlandırılan Yönetim sayfasının ekran görüntüsü. ](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Belirteç Oluştur sayfasının ekran görüntüsü.](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)
    ![Tanıtım saati Dizin belirteci sayfasının ekran görüntüsü.](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)


## <a name="step-3-add-atlassian-cloud-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD Uygulama Galerisi 'nden Atlasme bulutu ekleme

Azure AD Uygulama Galerisi 'nden atlasme bulutu 'nı, Atlasduyma bulutuna sağlamayı yönetmeye başlamak için ekleyin. Daha önceden SSO için Atlasduyi bulutu ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Atlasme bulutuna Kullanıcı ve grup atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 


## <a name="step-5-configuring-automatic-user-provisioning-to-atlassian-cloud"></a>5. Adım. Atlasme bulutuna otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan atlasi bulutu 'nda bulunan kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımları adım adım kılavuzluk eder.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Azure AD 'de Atlasduyi bulutu için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **atlasduyi bulutu**' nı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Atlasduyi bulutu**' nı seçin.

    ![Uygulamalar listesindeki Atlasduyi bulutu bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, daha önce Atlasme bulutunuzun hesabından alınan **kiracı URL 'Sini** ve **gizli anahtar belirtecini** girin. Azure AD 'nin Atlasme bulutuna bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Atlasder bulut hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları atlasi bulutu ' nı ile eşitler**' ı seçin.

9. **Öznitelik eşleme** bölümünde Azure AD 'Den Atlasme bulutuna eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için atlasduyma bulutundaki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|
   |---|---|
   |userName|Dize|
   |active|Boole|
   |name.familyName|Dize|
   |name.givenName|Dize|
   |emails[type eq "work"].value|Dize|   

10. **Eşlemeler** bölümünde, **Atlasme bulutu Için Azure Active Directory gruplarını eşitler**' ı seçin.

11. **Öznitelik eşleme** bölümünde Azure AD 'Den Atlasme bulutuna eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için atlasduyma bulutu 'ndaki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

      |Öznitelik|Tür|
      |---|---|
      |displayName|Dize|
      |externalId|Dize|
      |üyeler|Başvuru|

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. Atlasi bulutu için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek Atlasme bulutuna sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

16. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer.

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

1. Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
2. Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
3. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.  

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Atlasme bulutu, kullanıcıların yalnızca [doğrulanan etki alanlarından](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)sağlanmasına izin verir.
* Atlasme bulutu, Grup yeniden adlandırmaları bugün desteklemez. Bu, Azure AD 'deki bir grubun displayName 'e yapılan tüm değişikliklerin, Atlasduyi bulutu 'nda güncellenmeyeceği ve yansıtılmayacağı anlamına gelir.
* Azure AD 'deki **posta** Kullanıcı özniteliğinin değeri yalnızca kullanıcının bir Microsoft Exchange posta kutusu varsa doldurulur. Kullanıcı bir tane içermiyorsa, Atlasme bulutundaki **e-posta** özniteliğine farklı bir Desired özniteliği eşlemeniz önerilir.

## <a name="change-log"></a>Değişiklik günlüğü

* 06/15/2020-gruplar için Batch PATCH desteği eklendi.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png