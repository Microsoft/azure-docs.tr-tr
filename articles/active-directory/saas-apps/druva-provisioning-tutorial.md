---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Druva yapılandırma | Microsoft Docs'
description: Druva 'ye Kullanıcı hesaplarını otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
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
ms.openlocfilehash: 2ef564ae61aab10e1d100ccaf2547248e4a6a8e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96352176"
---
# <a name="tutorial-configure-druva-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için Druva yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları Druva 'e otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Druva ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* [Bir Druva kiracısı](https://www.druva.com/products/pricing-plans/).
* Yönetici izinlerine sahip Druva içinde bir kullanıcı hesabı.

## <a name="assigning-users-to-druva"></a>Druva 'e Kullanıcı atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Druva 'e erişmesi gerektiğini belirlemeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Druva 'e atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-druva"></a>Druva 'e Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için Druva 'e tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Druva 'e atarken atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-druva-for-provisioning"></a>Sağlama için kurulum Druva

Druva 'i Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmadan önce, Druva üzerinde SCıM sağlamasını etkinleştirmeniz gerekir.

1. [Druva Yönetici konsolunuza](https://console.druva.com)oturum açın. **Druva > InSync** adresine gidin.

    ![Druva Yönetici Konsolu](media/druva-provisioning-tutorial/menubar.png)

2. Dağıtımları **Yönet**  >    >  **kullanıcılarını** gezin.

    :::image type="content" source="media/druva-provisioning-tutorial/manage.png" alt-text="Druva yönetici konsolunun ekran görüntüsü. Yönet vurgulanır ve Yönet menüsü görünür olur. Bu menüde, dağıtımlar altında, kullanıcılar vurgulanır." border="false":::

3.  **Ayarlar**' a gidin. **Belirteç oluştur**' a tıklayın.

    :::image type="content" source="media/druva-provisioning-tutorial/settings.png" alt-text="Druva yönetim konsolundaki bir sayfanın ekran görüntüsü. Ayarlar vurgulanır ve ayarlar sekmesi açıktır. Belirteç oluştur düğmesi vurgulanır." border="false":::

4.  **Kimlik doğrulama belirteci** değerini kopyalayın. Bu değer, Azure portal Druva uygulamanızın sağlama sekmesindeki **gizli belirteç** alanına girilir.
    
    :::image type="content" source="media/druva-provisioning-tutorial/auth.png" alt-text="Druva yönetim konsolundaki belirteç Oluştur sayfasının ekran görüntüsü. Kimlik doğrulama belirteci değerini kopyalamak için, kopya belirteci etiketli bir bağlantı mevcuttur." border="false":::

## <a name="add-druva-from-the-gallery"></a>Galeriden Druva ekleme

Druva 'i Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmak üzere, Azure AD Uygulama Galerisi 'nden yönetilen SaaS uygulamaları listenize Druva eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden Druva eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Druva** yazın, sonuçlar panelinde **Druva** ' yi seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuç listesinde Druva](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-druva"></a>Druva 'e otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak Druva içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan bir adım adım yol gösterir.

> [!TIP]
> Ayrıca, [Druva çoklu oturum açma öğreticisinde](druva-tutorial.md)sunulan yönergeleri Izleyerek, DRUVA için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-druva-in-azure-ad"></a>Azure AD 'de Druva için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Druva**' yi seçin.

    ![Uygulamalar listesindeki Druva bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5.  Yönetici kimlik bilgileri bölümünün altında, `https://apis.druva.com/insync/scim` **kiracı URL 'sini** girin. **Kimlik doğrulama belirteci** değerini **gizli belirteç** olarak girin. Azure AD 'nin Druva 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Druva hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin ve **bir hata oluştuğunda e-posta bildirimi gönder**' i seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Druva olarak eşitler**' ı seçin.

    ![Druva Kullanıcı eşlemeleri](media/druva-provisioning-tutorial/usermapping.png)

9. **Öznitelik eşleme** bölümünde Azure AD 'den Druva 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için Druva içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Druva Kullanıcı öznitelikleri](media/druva-provisioning-tutorial/userattribute.png)


10. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

11. Druva için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** Içindeki istenen değerleri seçerek Druva için sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

13. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

    Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. Druva üzerinde Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, ilerlemeyi izlemek ve sağlama etkinliği raporunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

    Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).
    
## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Druva zorunlu bir öznitelik olarak **e-posta** gerektirir. 

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri incelemeyi ve sağlama etkinliğinde rapor almayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md).
