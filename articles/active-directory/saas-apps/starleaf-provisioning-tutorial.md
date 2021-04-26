---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Staryaprak yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını Staryaprak 'ya otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 07c476c0de644ac63c577d466f4691b5cf415334
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357951"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Staryaprak yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, Kullanıcı ve/veya grupları Staryaprak 'ya otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Staryaprak ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
>  Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* [Bir Staryaprak kiracısı](https://starleaf.com/).
* Yönetici izinlerine sahip Staryaprak 'da bir kullanıcı hesabı.

## <a name="assign-users-to-starleaf"></a>Kullanıcıları Staryaprak 'a ata
Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen atama adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve grupların Staryaprak 'a erişmesi gerektiğine karar vermeniz gerekir. Ardından, [Bu yönergeleri](../manage-apps/assign-user-or-group-access-portal.md)izleyerek, Kullanıcı ve grupları staryaprak 'a atayabilirsiniz.

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Staryaprağı 'e Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için Staryaprak 'a tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve gruplar daha sonra atanabilir.

* Bir kullanıcıyı Staryaprak 'a atadığınızda, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. Varsayılan erişim rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-starleaf-for-provisioning"></a>Sağlama için Staryaprak ayarlama

Azure AD ile otomatik Kullanıcı sağlaması için Staryaprak yapılandırmadan önce, Staryaprak 'da SCıM sağlamasını yapılandırmanız gerekir:

1. [Staryaprak yönetici konsolunda](https://portal.starleaf.com/#page=login)oturum açın. **Tümleştirmelere** gidin  >  **tümleştirme Ekle**.

    ![Tümleştirmelerle birlikte Staryaprak yönetici konsolunun ekran görüntüsü ve olarak adlandırılan tümleştirme seçenekleri ekleyin.](media/starleaf-provisioning-tutorial/image00.png)

2. Microsoft Azure Active Directory istediğiniz **türü** seçin. **Ada** uygun bir ad girin. **Uygula**’ya tıklayın.

    ![Tür ve ad metin kutuları olarak adlandırılan tümleştirme Ekle iletişim kutusunun ekran görüntüsü.](media/starleaf-provisioning-tutorial/image01.png)

3.  **SCIM temel URL 'si** ve **erişim belirteci** değerleri görüntülenecektir. Bu değerler, Azure portal Staryaprak uygulamanızın sağlama sekmesinde **kiracı URL 'si** ve **gizli belirteç** alanlarına girilir. 

    ![Tür, ad ve SCıM taban URL 'SI metin kutuları ile adlandırılan tümleştirme düzenleme iletişim kutusunun ekran görüntüsü.](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>Galeriden Staryaprak ekleyin

Staryaprak 'u Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmak üzere, Azure AD uygulama galerisindeki Staryaprak 'u yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden Staryaprak eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Staryaprak** yazın, sonuçlar panelinde **staryaprak** ' ı seçin.
    ![Sonuç listesinde Staryaprak](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Staryaprak için otomatik Kullanıcı sağlamayı yapılandırma

Bu bölümde, Azure AD 'de Kullanıcı ve/veya grup atamalarına göre Staryaprak 'da kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD sağlama hizmetini yapılandırma adımlarında size kılavuzluk eder.

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Staryaprak**' u seçin.

    ![Uygulamalar listesindeki Staryaprak bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. Yönetici kimlik bilgileri bölümünde, sırasıyla **kiracı URL 'si** ve **gizli belirteç** ' de bulunan **SCIM temel URL 'sini** ve **erişim belirteci** değerlerini girin. Azure AD 'nin Staryaprak 'a bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Staryaprak hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin ve bir **hata oluştuğunda e-posta bildirimi gönder** ' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Staryaprak olarak eşitler**' ı seçin.

    ![Azure Active Directory kullanıcılarını Staryaprak 'a eşitlemeyi gösteren eşlemeler bölümünün ekran görüntüsü.](media/starleaf-provisioning-tutorial/usermapping.png)

9. **Öznitelik eşleme** bölümünde, Azure AD 'Den staryaprak 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için staryaprak 'daki Kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Gösterilen dokuz eşlemeyi gösteren öznitelik eşlemeleri bölümünün ekran görüntüsü.](media/starleaf-provisioning-tutorial/userattribute.png)


10. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.


11. Staryaprak için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek staryaprak 'a sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

13. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilir ve staryaprak ÜZERINDE Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinlik raporuna ilişkin bağlantıları izleyebilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Staryaprak Şu anda grup sağlamayı desteklemiyor. 
* Staryaprak, aynı kaynak değerine sahip olmak için **e-posta** ve **Kullanıcı adı** değerleri gerektirir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri incelemeyi ve sağlama etkinliğinde rapor almayı](../app-provisioning/check-status-user-account-provisioning.md)öğrenin.
