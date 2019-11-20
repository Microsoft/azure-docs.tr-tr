---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Atlasduyma bulutu yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını Atlasme bulutuna otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 0c3173841de25a30b84870332c7334a81773e84d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "68561585"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için Atlasduyma bulutu yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları Atlasme bulutuna otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Atlasme bulutu ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Atlasbir bulut kiracısı](https://www.atlassian.com/licensing/cloud)
* Yönetici izinlerine sahip bir kullanıcı hesabı ile atlasi bulutu.

> [!NOTE]
> Azure AD sağlama tümleştirmesi, Atlasme bulut ekipleri tarafından kullanılabilen **atlasduyma bulutu SCIM API 'sini**kullanır.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Galeriden Atlasme bulutu ekleme

Azure AD ile otomatik Kullanıcı sağlama için Atlasme bulutunu yapılandırmadan önce Azure AD uygulama galerisindeki Atlasduyma bulutu 'nı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden Atlasme bulutu eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Atlasduyi bulutu**girin, sonuçlar panelinde **Atlasduyi bulutu** ' nı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde atlasduyi bulutu](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Kullanıcıları Atlasme bulutuna atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Atlasbir buluta erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Atlasme bulutuna atayabilirsiniz:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Atlasme bulutuna Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için, Atlasme bulutuna tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Atlasme bulutuna bir Kullanıcı atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Atlasme bulutuna otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan atlasi bulutu 'nda bulunan kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımları adım adım kılavuzluk eder.

> [!TIP]
> [Atlasi bulutu çoklu oturum açma öğreticisinde](atlassian-cloud-tutorial.md)sunulan yönergeleri Izleyerek atlasizi bulutu için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Azure AD 'de Atlasduyi bulutu için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **atlasduyi bulutu**' nı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Atlasduyi bulutu**' nı seçin.

    ![Uygulamalar listesindeki Atlasduyi bulutu bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Atlasme bulutu sağlama](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Atlasme bulutu sağlama](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. **Yönetici kimlik bilgileri** bölümünde, Atlasme bulutunuzun hesabınızın **kiracı URL 'Sini** ve **gizli belirtecini** girin. Bu değerlere örnek olarak şunlar verilebilir:

   * **Kiracı URL 'si** alanında, atlasma 'dan aldığınız belirli kiracı uç noktasını adım 6 ' da anlatıldığı gibi doldurabilirsiniz. Örneğin: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * **Gizli belirteç** alanında 6. adımda açıklanan gizli anahtarı doldurun.

6. [Atlasme kuruluş yöneticisi](https://admin.atlassian.com) **> Kullanıcı hazırlama** ' ya gidin ve **belirteç oluştur ' a**tıklayın. **Dizin temel URL** 'Sini ve **taşıyıcı belirtecini** sırasıyla **kiracı URL 'si** ve **gizli belirteç** alanlarına kopyalayın.

    ![Atlassian bulut sağlama ](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian bulut sağlama ](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)

    ![Atlasme bulutu sağlama](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. 5\. adımda gösterilen alanları doldurulmaya göre, Azure AD 'nin Atlasme bulutuna bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Atlasder bulut hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Atlasme bulutu sağlama](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

8. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Atlasme bulutu sağlama](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. **Kaydet** düğmesine tıklayın.

10. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları atlasi bulutu ' nı ile eşitler**' ı seçin.

    ![Atlasme bulutu sağlama](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. **Öznitelik eşleme** bölümünde Azure AD 'Den Atlasme bulutuna eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için atlasduyma bulutundaki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Atlasme bulutu sağlama](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. **Eşlemeler** bölümünde, **Atlasme bulutu Için Azure Active Directory gruplarını eşitler**' ı seçin.

    ![Atlasme bulutu sağlama](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. **Öznitelik eşleme** bölümünde Azure AD 'Den Atlasme bulutuna eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için atlasduyma bulutu 'ndaki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Atlasme bulutu sağlama](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

15. Atlasi bulutu için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Atlasme bulutu sağlama](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek Atlasme bulutuna sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Atlasme bulutu sağlama](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Atlasme bulutu sağlama](./media/atlassian-cloud-provisioning-tutorial/save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. **Eşitleme ayrıntıları** bölümünü Izleyip, Atlaslik bulutu ÜZERINDE Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan sağlama etkinliği raporunu kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Atlasme bulutu, kullanıcıların yalnızca [doğrulanan etki alanlarından](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)sağlanmasına izin verir.
* Atlasme bulutu, Grup yeniden adlandırmaları bugün desteklemez. Bu, Azure AD 'deki bir grubun displayName 'e yapılan tüm değişikliklerin, Atlasduyi bulutu 'nda güncellenmeyeceği ve yansıtılmayacağı anlamına gelir.
* Azure AD 'deki **posta** Kullanıcı özniteliğinin değeri yalnızca kullanıcının bir Microsoft Exchange posta kutusu varsa doldurulur. Kullanıcı bir tane içermiyorsa, Atlasme bulutundaki **e-posta** özniteliğine farklı bir Desired özniteliği eşlemeniz önerilir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
