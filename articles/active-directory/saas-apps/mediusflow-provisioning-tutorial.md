---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için MediusFlow yapılandırma | Microsoft Docs'
description: Azure AD 'den MediusFlow 'a Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: a49258208f7a5945ac71c8f17db56fccfdcd6515
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662009"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için MediusFlow yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem MediusFlow hem de Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmeti 'ni kullanarak Kullanıcı ve grupları otomatik olarak [temin etmek ve](https://www.mediusflow.com/) bunların sağlamasını kaldırır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * MediusFlow 'da Kullanıcı oluşturma
> * Artık erişim gerektirmeyen MediusFlow 'daki kullanıcıları kaldırın
> * Kullanıcı özniteliklerinin Azure AD ile MediusFlow arasında eşitlenmiş olmasını sağlama
> * MediusFlow 'da grupları ve grup üyeliklerini sağlama
> * MediusFlow 'da çoklu oturum açma (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md) 
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı (örneğin, uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici). 
* Kalite güvencesi veya üretim kiracısı ile etkin bir MediusFlow aboneliği.
* Mediusflow 'da, yapılandırmayı MediusFlow içinde gerçekleştirebilecek yönetici erişim haklarına sahip bir kullanıcı hesabı.
* Kullanıcılara sağlanması gereken MediusFlow kiracısına eklenen şirketler.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD Ile MediusFlow arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın. 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için MediusFlow yapılandırma

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>MediusFlow içinde Microsoft 365 uygulamasını etkinleştirme
Aşağıdaki adımları gerçekleştirerek, MediusFlow içindeki Azure AD oturum açma ve Azure AD yapılandırma özelliğinin erişimini etkinleştirerek başlayın:

#### <a name="user-login"></a>Kullanıcı oturumu açma
Microsoft 365/Azure AD 'de oturum açma akışını etkinleştirmek için [this] (makalesine başvurun https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) .

#### <a name="user-transfer-configuration"></a>Kullanıcı Aktarım yapılandırması
Azure AD 'den sağlama için kullanıcıların yapılandırma portalını etkinleştirmek üzere [Bu](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) makaleye bakın.

#### <a name="configure-user-provisioning"></a>Kullanıcı sağlamayı yapılandırma

1.  Kiracı KIMLIĞINI sağlayarak [Mediusflow yönetim konsolunda](https://office365.cloudapp.mediusflow.com/) oturum açın.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/1-auth.png" alt-text="MediusFlow yönetim konsolunun ekran görüntüsü. MediusFlow kiracı adı kutusu ve kimlik doğrula düğmesi ilk tümleştirme adımında vurgulanır." border="false":::

2. MediusFlow ile bağlantıyı doğrulayın.

    ![Doğrulama](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Azure AD kiracı KIMLIĞINI belirtin.

    ![Kiracı KIMLIĞINI belirtin](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   Bu konuda daha fazla bilgi edinmek için [SSS bölümüne bakın](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id) .

4. Yapılandırmayı kaydedin.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/4-save-config.png" alt-text="Üçüncü tümleştirme adımını gösteren MediusFlow yönetim konsolunun ekran görüntüsü. Yapılandırma Kaydet düğmesi vurgulanır." border="false":::

5. Kullanıcı hazırlama ' yı seçin ve **Tamam**' ı tıklatın.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png" alt-text="Bir beşinci tümleştirme adımını gösteren MediusFlow yönetim konsolunun ekran görüntüsü. Kullanıcı sağlamasını kullan ve Tamam düğmeleri vurgulanır." border="false":::

6. **Gizli anahtar oluştur**' a tıklayın. Bu değeri kopyalayın ve kaydedin. Bu değer, Azure portal MediusFLow uygulamanızın **sağlama** sekmesindeki **gizli belirteç** alanına girilir.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/6-create-secret-1.png" alt-text="MediusFlow yönetim konsolundaki Kullanıcı sağlama yapılandırması sekmesinin ekran görüntüsü. Gizli anahtar oluştur ve Kopyala düğmeleri vurgulanır." border="false":::

7. **Tamam**' a tıklayın.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/7-confirm-secret.png" alt-text="MediusFlow yönetim konsolunun, kullanıcılara yeni bir gizli anahtar oluşturmak için Tamam ' a tıklamasını söyleyen bir bildirim ile ekran görüntüsü. Tamam düğmesi vurgulanır." border="false":::

8. Daha önceden tanımlanmış roller, şirketler ve diğer genel yapılandırmalarla içeri aktarılan kullanıcıları MediusFlow 'da almak için, önce bunu yapılandırmanız gerekecektir. **Yeni yapılandırma Ekle ' ye** tıklayarak yapılandırmayı ekleyerek başlayın.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png" alt-text="MediusFlow yönetim konsolundaki Kullanıcı sağlama yapılandırması sekmesinin ekran görüntüsü. Yeni yapılandırma Ekle düğmesi vurgulanır." border="false":::

9. Kullanıcılar için varsayılan ayarları sağlayın. Bu görünümde, varsayılan özniteliği ayarlamak mümkündür. Standart ayarlar tamam ise, yalnızca geçerli bir şirket adı sağlamak yeterlidir. Bu yapılandırma ayarları Mediusflow 'dan getirildiğinden, önce yapılandırılması gerekir. Daha fazla bilgi için bu makalenin **Önkoşullar** bölümüne bakın.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png" alt-text="MediusFlow yeni yapılandırma Ekle penceresinin ekran görüntüsü. Yerel ayarlar, bir filtre ve Kullanıcı rolleri dahil olmak üzere birçok ayar görülebilir." border="false":::

10. Kullanıcı yapılandırmasını kaydetmek için **Kaydet** ' e tıklayın.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/10-done-1.png" alt-text="MediusFlow yönetim konsolundaki Kullanıcı sağlama yapılandırması sekmesinin ekran görüntüsü. Kaydet düğmesi vurgulanır." border="false":::

11. Kullanıcı sağlama bağlantısına ulaşmak için, **SCIM bağlantısını Kopyala bağlantısına** tıklayın. Bu değeri kopyalayın ve kaydedin. Bu değer, Azure portal MediusFLow uygulamanızın **sağlama** sekmesinde **kiracı URL 'si** alanına girilir.
 
    :::image type="content" source="./media/mediusflow-provisioning-tutorial/11-get-scim-link.png" alt-text="MediusFlow yönetim konsolundaki Kullanıcı sağlama yapılandırması sekmesinin ekran görüntüsü. Copy S C I M link düğmesi vurgulanır." border="false":::

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden MediusFlow ekleme

Azure AD uygulama galerisinden MediusFlow ekleyerek MediusFlow 'a sağlamayı yönetmeye başlayın. Daha önce, SSO için MediusFlow 'u ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* MediusFlow 'a Kullanıcı ve grup atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Sağlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında, uygulamaya bir veya iki kullanıcı veya grup atayarak bunu denetleyebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>5. Adım. Otomatik Kullanıcı sağlamasını MediusFlow 'a yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak TestApp içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan bir adım adım yol gösterir.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Azure AD 'de MediusFlow için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Mediusflow**' u seçin.

    ![Uygulamalar listesindeki MediusFlow bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde **, kiracı URL 'si içinde daha** önce alınan kiracı URL 'si değerini girin. Gizli belirteç değerini **gizli belirteçte** daha önce alındı olarak girin. Azure AD 'nin MediusFlow 'a bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, MediusFlow hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

      ![Ekran görüntüsü, kiracı U R L ve gizli belirteç girebileceğiniz yönetici kimlik bilgileri iletişim kutusunu gösterir.](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları mediusflow ile eşitler**' ı seçin.

9. **Öznitelik eşleme** bölümünde Azure AD 'Den MediusFlow 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için MediusFlow 'daki Kullanıcı hesaplarıyla eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, MediusFlow API 'sinin, bu özniteliğe göre kullanıcıların filtrelenmesini desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|Filtreleme için destekleniyor|
   |---|---|---|
   |userName|Dize|&check;|
   |emails[type eq "work"].value|Dize|
   |Name. displayName|Dize|
   |active|Boole|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |ad. biçimlendirildi|Dize|
   |externalId|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Manager|Başvuru|
   |urn: IETF: params: Scim: schemas: Extension: medius: 2.0: Kullanıcı: configurationFilter|Dize|
   |urn: IETF: params: Scim: schemas: Extension: medius: 2.0: Kullanıcı: IdentityProvider|Dize|
   |urn: IETF: params: Scim: schemas: Extension: medius: 2.0: Kullanıcı: NameIdentifier|Dize|
   |urn: IETF: params: Scim: schemas: Extension: medius: 2.0: Kullanıcı: customFieldText1|Dize|
   |urn: IETF: params: Scim: schemas: Extension: medius: 2.0: Kullanıcı: customFieldText2|Dize|
   |urn: IETF: params: Scim: schemas: Extension: medius: 2.0: Kullanıcı: customFieldText3|Dize|
   |urn: IETF: params: Scim: schemas: Extension: medius: 2.0: Kullanıcı: customFieldText4|Dize|
   |urn: IETF: params: Scim: schemas: Extension: medius: 2.0: Kullanıcı: customFieldText5|Dize|


10. **Eşlemeler** bölümünde, **Azure Active Directory grupları Mediusflow olarak eşitler**' ı seçin.

11. **Öznitelik eşleme** bölümünde Azure AD 'Den MediusFlow 'a eşitlenen grup özniteliklerini inceleyin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için MediusFlow grupları ile eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    | Öznitelik | Tür |
    |--|--|
    | displayName | Dize |
    | externalID | Dize |
    | üyeler | Başvuru |

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. MediusFlow için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek mediusflow 'a sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

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

* 01/21/2021-özel uzantı öznitelikleri **ConfigurationFilter**, **IdentityProvider**, **NameIdentifier**, **customFieldText1**, **customFieldText2**, **customFieldText3**, **customFieldText3** ve **customFieldText5** eklendi.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)