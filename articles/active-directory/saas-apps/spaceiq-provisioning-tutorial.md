---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı hazırlama için SpaceIQ yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırma hakkında bilgi edinin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: a5bd3c39c293880e31ebe63f85b3814725f6027a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96347319"
---
# <a name="tutorial-configure-spaceiq-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için SpaceIQ yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları SpaceIQ 'ya otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için SpaceIQ ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Bir SpaceIQ kiracısı](https://spaceiq.com/)
* Bir kullanıcı hesabı ile SpaceIQ yönetici izinleri.

## <a name="assigning-users-to-spaceiq"></a>Kullanıcıları SpaceIQ 'a atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların SpaceIQ 'a erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları SpaceIQ öğesine atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-spaceiq"></a>SpaceIQ 'a Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için tek bir Azure AD kullanıcısına SpaceIQ atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı SpaceIQ 'a atarken atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-spaceiq-for-provisioning"></a>Sağlama için SpaceIQ ayarlama

1. [Spaceiq Yönetici konsolunuza](https://main.spaceiq.com/login/)oturum açın. Ekranın sağ üst köşesindeki açılır menüden seçerek **Ayarlar** ' a gidin.

    ![SpaceIQ Yönetici Konsolu](media/spaceiq-provisioning-tutorial/admin.png)

2.  **Ayarlar** sayfasında **üçüncü taraf tümleştirmeler**' i seçin.

    ![SpaceIQ SCıM Ekle](media/spaceiq-provisioning-tutorial/thirdparty.png)

3.  **Sağlama ve SSO** sekmesine gidin. **Azure** kutucuğunu arayın. **Etkinleştir**' e tıklayın.

    ![SpaceIQ sağlama ve SSO](media/spaceiq-provisioning-tutorial/provisioning.png)

    ![SpaceIQ Azure 'U etkinleştir ](media/spaceiq-provisioning-tutorial/azure.png)

3.  **SCIM taşıyıcı belirtecini** kopyalayın. Bu değer, Azure portal SpaceIQ uygulamanızın sağlama sekmesindeki gizli belirteç alanına girilir. **Etkinleştir** 'e tıklayın

    ![SpaceIQ belirteç oluştur](media/spaceiq-provisioning-tutorial/token.png)

## <a name="add-spaceiq-from-the-gallery"></a>Galeriden SpaceIQ ekleme

Azure AD ile otomatik Kullanıcı sağlama için SpaceIQ 'ı yapılandırmadan önce, Azure AD uygulama galerisinden yönetilen SaaS uygulamaları listenize SpaceIQ eklemeniz gerekir.

**Azure AD uygulama galerisinden SpaceIQ eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **spaceiq** girin, sonuçlar panelinde **spaceiq** ' ı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuç listesinde SpaceIQ](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-spaceiq"></a>SpaceIQ 'a otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni kullanarak Azure AD 'de Kullanıcı ve/veya grup atamalarına göre SpaceIQ içindeki kullanıcıları ve/veya grupları oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

> [!TIP]
> Spaceq [Çoklu oturum açma öğreticisinde](./spaceiq-tutorial.md)sunulan yönergeleri Izleyerek spaceiq için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilse de

### <a name="to-configure-automatic-user-provisioning-for-spaceiq-in-azure-ad"></a>Azure AD 'de SpaceIQ otomatik Kullanıcı sağlamasını yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Spaceiq**' ı seçin.

    ![Uygulamalar listesindeki SpaceIQ bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, `https://api.spaceiq.com/scim` **kiracı URL 'sini** girin. **Gizli belirteçte** daha önce alınan **SCIM kimlik doğrulama belirteci** değerini girin. Azure AD 'nin SpaceIQ 'a bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, SpaceIQ hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları, Spaceiq olarak eşitler**' ı seçin.

    ![SpaceIQ Kullanıcı eşlemeleri](media/spaceiq-provisioning-tutorial/usermapping.png)

9. **Öznitelik eşleme** bölümünde Azure AD 'Den SpaceIQ 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için SpaceIQ içindeki kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![SpaceIQ Kullanıcı öznitelikleri](media/spaceiq-provisioning-tutorial/userattributes.png)

11. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

12. SpaceIQ için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

13. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek spaceiq için sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

14. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. **Eşitleme ayrıntıları** bölümünü kullanarak ilerleme durumunu izleyebilir ve sağlama etkinlik raporuna ilişkin bağlantıları izleyebilirsiniz. bu Işlem, Azure AD sağlama hizmeti tarafından, SpaceIQ üzerinde gerçekleştirilen tüm eylemleri açıklar.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)