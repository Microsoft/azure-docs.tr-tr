---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için myPolicies yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını myPolicies 'e otomatik olarak sağlamak ve yeniden sağlamak üzere Azure Active Directory nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 221f63ab9a7eb3f71a4c730a11565dda64c9edc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353595"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlama için myPolicies yapılandırma

Bu öğreticinin amacı, Kullanıcı ve/veya grupları myPolicies 'e otomatik olarak sağlamak ve devre dışı bırakmak üzere Azure AD 'yi yapılandırmak için myPolicies ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* [MyPolicies kiracısı](https://mypolicies.com/index.html#section10).
* Yönetici izinlerine sahip myPolicies içindeki bir kullanıcı hesabı.

## <a name="assigning-users-to-mypolicies"></a>Kullanıcıları myPolicies 'a atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların myPolicies 'e erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları myPolicies 'e atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>MyPolicies 'e Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için myPolicies 'e tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı myPolicies 'e atarken atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-mypolicies-for-provisioning"></a>Sağlama için myPolicies kurulum

Azure AD ile otomatik Kullanıcı hazırlama için Ilkelerinizi yapılandırmadan önce myPolicies üzerinde SCıM sağlamasını etkinleştirmeniz gerekir.

1. **support@mypolicies.com** SCIM sağlamasını yapılandırmak için gereken gizli belirteci almak için, myPolicies temsilcinize ulaşın.

2.  MyPolicies temsilcisi tarafından sunulan belirteç değerini kaydedin. Bu değer, Azure portal myPolicies uygulamanızın sağlama sekmesindeki **gizli belirteç** alanına girilir.

## <a name="add-mypolicies-from-the-gallery"></a>Galeriden myPolicies ekleme

Azure AD ile otomatik Kullanıcı sağlaması için myPolicies yapılandırmak üzere, Azure AD uygulama galerisindeki myPolicies öğesini yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD Uygulama Galerisi ' nden myPolicies eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **mypolicies** yazın, sonuçlar panelinde **mypolicies** ' i seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesindeki myPolicies](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>Otomatik Kullanıcı sağlamayı myPolicies 'e yapılandırma 

Bu bölümde, Azure AD 'de Kullanıcı ve/veya grup atamalarına göre myPolicies içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD sağlama hizmetini yapılandırma adımlarında size kılavuzluk eder.

> [!TIP]
> [Mypolicies çoklu oturum açma öğreticisinde](mypolicies-tutorial.md)belirtilen yönergeleri Izleyerek mypolicies için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>Azure AD 'de myPolicies için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Mypolicies**' ı seçin.

    ![Uygulamalar listesindeki myPolicies bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, `https://<myPoliciesCustomDomain>.mypolicies.com/scim` mypolicies özel etki alanınız olan **kiracı URL 'sine** giriş yapın `<myPoliciesCustomDomain>` . MyPolicies müşteri etki alanınızı URL 'nizden elde edebilirsiniz.
Örnek: `<demo0-qa>` . mypolicies.com.

6. **Gizli belirteç**' de, daha önce alınan belirteç değerini girin. Azure AD 'nin Ilkelerinize bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, myPolicies hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

7. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

8. **Kaydet**’e tıklayın.

9. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Mypolicies**' a eşitler ' ı seçin.

    :::image type="content" source="media/mypolicies-provisioning-tutorial/usermapping.png" alt-text="Eşlemeler bölümünün ekran görüntüsü. Ad ' ın altında, Azure Active Directory Kullanıcıları customappsso ile eşitler görünür." border="false":::

10. **Öznitelik eşleme** bölümünde, Azure AD 'Den mypolicies ile eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için mypolicies içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|
   |---|---|
   |userName|Dize|
   |active|Boole|
   |emails[type eq "work"].value|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |ad. biçimlendirildi|Dize|
   |externalId|Dize|
   |adresler [tür EQ "iş"]. ülke|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Manager|Başvuru|


11. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

12. MyPolicies için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

13. **Ayarlar** bölümünde **kapsam** Içinde Istenen değerleri seçerek mypolicies 'a sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

14. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerlemeyi izlemek ve sağlama etkinliği raporunu izlemek için **eşitleme ayrıntıları** bölümünü kullanarak mypolicies ÜZERINDE Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklar.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* myPolicies her zaman **Kullanıcı adı**, **e-posta** ve **externalId** gerektirir.
* myPolicies, Kullanıcı öznitelikleri için sabit silmeleri desteklemez.

## <a name="change-log"></a>Değişiklik günlüğü

* 09/15/2020-kullanıcılar için "Country" özniteliği için destek eklendi.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
