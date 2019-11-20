---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Reward Gateway yapılandırma | Microsoft Docs'
description: Ağ geçidini yeniden almak için Kullanıcı hesaplarını otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 4d07fa66374908781fd1d637fd722fd60d1352b3
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737881"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Öğretici: Otomatik Kullanıcı sağlama için Reward Gateway yapılandırma

Bu öğreticinin amacı, Azure AD 'yi otomatik olarak sağlamak ve devre dışı bırakmak üzere kullanıcıları ve/veya grupları otomatik olarak sağlamak ve yeniden sağlamak üzere yapılandırmak için, Reward Gateway ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* Bir [Reward Gateway kiracısı](https://www.rewardgateway.com/).
* Yönetici izinleriyle yeniden ağ geçidinde bir kullanıcı hesabı.

## <a name="assigning-users-to-reward-gateway"></a>Kullanıcıları ağ geçidine yeniden dengeme atama 

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların ağ geçidine yönelik erişimine ihtiyacı olduğuna karar vermeniz gerekir. Karar verdikten sonra, [bir kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)bölümündeki yönergeleri izleyerek bu kullanıcıları ve/veya grupları, ağ geçidini yeniden dengeleyerek atayabilirsiniz.


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Ağ geçidini yeniden almak için Kullanıcı atamaya yönelik önemli ipuçları 

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek üzere ağ geçidini yeniden denemek için tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı ağ geçidine yeniden almak için atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-reward-gateway--for-provisioning"></a>Sağlama için ağ geçidini ayarlama
Azure AD ile otomatik Kullanıcı sağlama için Reward Gateway 'i yapılandırmadan önce, Reward ağ geçidinde SCıM sağlamasını etkinleştirmeniz gerekir.

1. [Reward Gateway yönetici konsolunda](https://rewardgateway.photoshelter.com/login/)oturum açın. **Tümleştirmeler**' e tıklayın.

    ![Reward Gateway Yönetici Konsolu](media/reward-gateway-provisioning-tutorial/image00.png)

2.  **Tümleştirmi**seçin.

    ![Reward Gateway Yönetici Konsolu](media/reward-gateway-provisioning-tutorial/image001.png)

3.  **SCIM URL 'si (v2)** ve **OAuth taşıyıcı belirtecinin**değerlerini kopyalayın. Bu değerler, Azure portal Reward Gateway uygulamanızın sağlama sekmesinde kiracı URL 'SI ve gizli belirteç alanına girilir.

    ![Reward Gateway Yönetici Konsolu](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Galeriden Reward Gateway ekleme

Azure AD ile otomatik Kullanıcı sağlama için Reward Gateway 'i yapılandırmak üzere, Azure AD uygulama galerisinden yönetilen SaaS uygulamaları listenize Reward Gateway eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden Reward ağ geçidi eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, **Reward Gateway**yazın, sonuçlar panelinde **ağ geçidini yeniden** seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde ağ geçidini yeniden dengeleme](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Ağ geçidini yeniden almak için otomatik Kullanıcı sağlamayı yapılandırma  

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak Kullanıcı ve/veya Grup atamaları için Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan kullanıcıları ve/veya grupları oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

> [!TIP]
> [Reward Gateway çoklu oturum](reward-gateway-tutorial.md)açma öğreticisinde belirtilen yönergeleri Izleyerek, Reward ağ GEÇIDI için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>Azure AD 'de otomatik Kullanıcı sağlamasını yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kuruluş uygulamaları**' nı seçin ve ardından **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Reward Gateway**' i seçin.

    ![Uygulamalar listesinde Reward Gateway bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama sekmesi](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, önceki kiracı URL 'si **(v2)** ve **OAuth taşıyıcı belirteç** değerlerini sırasıyla **kiracı URL 'si** ve **gizli belirteç** ' de girin. Azure AD 'nin ağ geçidine bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, ödül Gateway hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde, **ağ geçidini yeniden getirmek Için Azure Active Directory Kullanıcıları eşitler**' ı seçin.

    ![Reward Gateway Yönetici Konsolu](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. **Öznitelik eşleme** bölümünde, Azure AD 'Den ağ geçidine yeniden gitmek için eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Reward Gateway 'teki Kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Reward Gateway Yönetici Konsolu](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

11. Reward Gateway için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek ağ geçidini yeniden almak için sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

13. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilir ve Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan etkinlik raporuna izleyebilirsiniz.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

Reward Gateway, Grup sağlamayı Şu anda desteklemiyor.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

[Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)
