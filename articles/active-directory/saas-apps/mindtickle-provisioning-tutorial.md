---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Mindtickalıbı yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını mini olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: c158358b1c4fcd72d9189d7a991645cb65a4dc83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348169"
---
# <a name="tutorial-configure-mindtickle-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlama için Mindtickalıbı yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları mini olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Mindticula ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Mini bir kiracı](https://www.mindtickle.com/)
* Yönetici izinleriyle mini Kullanıcı hesabı.

## <a name="assigning-users-to-mindtickle"></a>Kullanıcıları Mindtici 'ye atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Mindticine erişmesi gerektiğine karar vermeniz gerekir. Kararlandıktan sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları mini bir şekilde atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mindtickle"></a>Kullanıcıları mini uçlarına atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için, en az bir Azure AD kullanıcısına en çok bir Azure AD kullanıcısı atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Mindtici 'ye atarken atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-mindtickle-for-provisioning"></a>Sağlama için Mini Kurulum

Azure AD ile otomatik Kullanıcı sağlama için Mindtickalıbı yapılandırmadan önce, mindticon 'da SCıM sağlamasını etkinleştirmeniz gerekir.


1.  SCıM sağlamasını yapılandırmak için gereken JWT belirtecini almak için  [Mindticula 'nin destek ekibine](mailto:help@mindtickle.com) ulaşın.


## <a name="add-mindtickle-from-the-gallery"></a>Galeriden mini Ekle

Azure AD ile otomatik Kullanıcı sağlama için Mindtickalıbı 'yi yapılandırmak için Azure AD Uygulama Galerisi 'nden yönetilen SaaS uygulamaları listenize Mindticsel 'yi eklemeniz gerekir.

**Azure AD uygulama galerisinden mini eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, **mindtickalıbı** girin, sonuçlar panelinde **mindtickalıbı** ' ı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde bir mini](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mindtickle"></a>Mini Kullanıcı sağlamasını yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni kullanarak Kullanıcı ve/veya grup atamalarını Azure AD 'de Kullanıcı ve/veya grup atamalarına göre oluşturma, güncelleştirme ve devre dışı bırakma adımları gösterilmektedir.

> [!TIP]
> Ayrıca, mindticsel için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi tercih edebilirsiniz. Bu, en [az bir çoklu oturum açma öğreticisinde](mindtickle-tutorial.md)sunulan talimatları takip edebilir. Çoklu oturum açma otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilse de

### <a name="to-configure-automatic-user-provisioning-for-mindtickle-in-azure-ad"></a>Azure AD 'de mini Kullanıcı sağlamayı otomatik olarak yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **mini**' i seçin.

    ![Uygulamalar listesindeki en küçük bağlantı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, `https://admin.mindtickle.com/scim` **kiracı URL 'sini** girin. Daha önce gizli bir belirteç metin kutusunda alınan **JWT belirteç** değerini girin, Mindtickalıbı destek ekibi tarafından verilen **JWT belirteç** değerini girin. Azure AD 'nin Ilkelerinize bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Mindtici hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümü altında, **Kullanıcı Azure Active Directory Kullanıcıları mini halinde eşitler**' ı seçin.

    :::image type="content" source="media/mindtickle-provisioning-tutorial/usermapping.png" alt-text="Eşlemeler bölümünün ekran görüntüsü. Ad ' ın altında, kullanıcıları Mindtici ile eşitler Azure Active Directory." border="false":::

9. **Öznitelik eşleme** bölümünde, Azure AD 'Den Mindtickile eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Mindtickalıbı içindeki kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    :::image type="content" source="media/mindtickle-provisioning-tutorial/userattribute.png" alt-text="Öznitelik eşlemeleri sayfasının ekran görüntüsü. Tablo, Azure Active Directory ve Mindtici özniteliklerini ve eşleşen önceliği listeler." border="false":::

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. Mini Azure AD sağlama hizmetini etkinleştirmek için, **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** içindeki Istenen değerleri seçerek, mindticiler için sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin sonraki eşitlemeler daha uzun sürer. Kullanıcıların ve/veya grupların sağlaması için ne kadar süreceğine ilişkin daha fazla bilgi için bkz. [kullanıcıları sağlamak için ne kadar sürer](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users). 

İlerleme durumunu izlemek için **geçerli durum** bölümünü kullanabilir ve Azure AD sağlama hizmeti tarafından, Mindticde üzerinde gerçekleştirilen tüm eylemleri açıklayan sağlama etkinliği raporunuzun bağlantılarını izleyebilirsiniz. Daha fazla bilgi için bkz. [Kullanıcı hazırlama durumunu denetleme](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Azure AD sağlama günlüklerini okumak için bkz. [Otomatik Kullanıcı hesabı sağlama hakkında raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
