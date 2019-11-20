---
title: 'Öğretici: Azure Active Directory ile otomatik kullanıcı hazırlama için tuş takımı yapılandırma | Microsoft Docs'
description: Otomatik olarak sağlama ve sağlamasını tuş takımı için kullanıcı hesapları için Azure Active Directory yapılandırmayı öğrenin.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 32e634bc089417aaa8080b30a5f77f663a3d8b33
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611775"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Öğretici: Tuş takımı için otomatik kullanıcı hazırlama yapılandırın

Bu öğreticinin amacı otomatik olarak sağlamak ve kullanıcılara ve/veya tuş takımı gruplarına sağlamasını tuş takımı ve Azure Active Directory (Azure AD) Azure AD yapılandırmak için gerçekleştirilmesi gereken adımlar göstermektir.

> [!NOTE]
>  Bu öğreticide, Azure AD kullanıcı sağlama hizmeti üzerinde oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmet yapar, nasıl çalıştığını ve sık sorulan sorular önemli ayrıntılar için bkz. [otomatik kullanıcı hazırlama ve sağlamayı kaldırma Azure Active Directory ile SaaS uygulamalarına](../manage-apps/user-provisioning.md).

> Bu bağlayıcı, şu anda Önizleme aşamasındadır. Genel Microsoft Azure için kullanım koşulları Önizleme özellikleri hakkında daha fazla bilgi için bkz. [ek kullanım koşulları, Microsoft Azure önizlemeleri için](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulları zaten sahip olduğunuzu varsayar:

* Azure AD kiracısı.
* [Bir tuş takımı Kiracı](https://www.dialpad.com/pricing/).
* Tuş takımı yönetici izinlerine sahip bir kullanıcı hesabı.

## <a name="assign-users-to-dialpad"></a>Tuş takımı için kullanıcı atama
Azure Active Directory atamaları adlı bir kavram, hangi kullanıcıların seçilen uygulamalara erişimi alması belirlemek için kullanır. Otomatik kullanıcı hazırlama bağlamında, yalnızca kullanıcı ve/veya Azure AD'de bir uygulamaya atanan gruplar eşitlenir.

Yapılandırma ve otomatik kullanıcı hazırlama etkinleştirmeden önce hangi kullanıcılara ve/veya Azure AD'de grupları tuş takımı erişmesi karar vermeniz gerekir. Karar sonra buradaki yönergeleri izleyerek tuş takımı için bu kullanıcılara ve/veya grupları atayabilirsiniz:
 
* [Kurumsal bir uygulamayı kullanıcı veya grup atama](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Tuş takımı için kullanıcı atama önemli ipuçları

 * Önerilir tek bir Azure AD kullanıcı sağlama yapılandırmasını otomatik kullanıcı test etmek için tuş takımı atanır. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Tuş takımı için kullanıcı atama, atama iletişim kutusunda (varsa) geçerli bir uygulamaya özgü rolü seçmeniz gerekir. Varsayılan erişim rolüne sahip kullanıcılar, sağlamasından bırakılır.

## <a name="setup-dialpad-for-provisioning"></a>Sağlama için Kurulum tuş takımı

Tuş takımı için otomatik kullanıcı hazırlama Azure AD'ye yapılandırmadan önce tuş takımı bazı sağlama bilgilerini almak gerekir.

1. Oturum açın, [tuş takımı Yönetici Konsolu](https://dialpadbeta.com/login) seçip **yönetici ayarları**. Emin **My Company** açılan listeden seçilir. Gidin **kimlik doğrulama > API anahtarları**.

    ![SCIM tuş takımı Ekle](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Tıklayarak yeni bir anahtar oluşturmak **bir anahtar ekleyin** ve gizli belirtecinizi özelliklerini yapılandırma.

    ![SCIM tuş takımı Ekle](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![SCIM tuş takımı Ekle](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Tıklayın **değer göstermek için tıklayın** düğme için son oluşturulan API anahtarınızı ve gösterilen değerini kopyalayın. Bu değer girilir **gizli belirteç** tuş takımı uygulamanızı Azure portalında sağlama sekmesindeki alan. 

    ![Tuş takımı belirteci oluşturma](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Tuş takımı Galeriden Ekle

Tuş takımı için otomatik kullanıcı hazırlama Azure AD ile yapılandırmak için tuş takımı Azure AD uygulama galerideki yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Azure AD uygulama galerisinden tuş takımı eklemek için aşağıdaki adımları gerçekleştirin:**

1. İçinde  **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde seçin **Azure Active Directory**.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. Git **kurumsal uygulamalar**ve ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için seçin **yeni uygulama** bölmenin üstünde düğme.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **tuş takımı**seçin **tuş takımı** Sonuçlar panelinde.
    ![Sonuç listesinde tuş takımı](common/search-new-app.png)

5. Gidin **URL** vurgulanmış altındaki ayrı bir tarayıcı. 

    ![SCIM tuş takımı Ekle](media/dialpad-provisioning-tutorial/dialpad05.png)

6. Sağ üst köşede seçin **oturum açın > çevrimiçi kullanım tuş takımı**.

    ![SCIM tuş takımı Ekle](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Tuş takımı Openıdconnect uygulama olduğundan, Microsoft iş hesabınızı kullanarak tuş takımı için oturum açmak seçin.

    ![SCIM tuş takımı Ekle](media/dialpad-provisioning-tutorial/loginpage.png)

8. Başarılı bir kimlik doğrulamasından sonra onay sayfası için onay istemi kabul edin. Uygulama, kiracınıza sonra bir otomatik olarak eklenir ve tuş takımı hesabınıza yönlendirilir.

    ![SCIM tuş takımı Ekle](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Tuş takımı için otomatik kullanıcı sağlamayı yapılandırma

Bu bölümde oluşturmak, güncelleştirmek ve kullanıcılar devre dışı bırakmak için sağlama hizmetini Azure AD'yi yapılandırma adımlarında size kılavuzluk eder ve/veya Azure AD'de kullanıcı ve/veya grup atamaları bu tuş takımı olarak gruplandırır.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Azure AD'de tuş takımı için otomatik kullanıcı hazırlama yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. Seçin **kurumsal uygulamalar**, ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **tuş takımı**.

    ![Uygulamalar listesinde tuş takımı bağlantı](common/all-applications.png)

3. Seçin **sağlama** sekmesi.

    ![Sağlama sekmesinde](common/provisioning.png)

4. Ayarlama **hazırlama modu** için **otomatik**.

    ![Sağlama sekmesinde](common/provisioning-automatic.png)

5. Altında **yönetici kimlik bilgileri** giriş bölümünde `https://dialpad.com/scim` içinde **Kiracı URL'si**. Giriş değeri alındı ve tuş takımı daha önce kaydedilen **gizli belirteç**. Tıklayın **Test Bağlantısı** Azure emin olmak için AD, tuş takımı için bağlanabilirsiniz. Bağlantı başarısız olursa, tuş takımı hesabının yönetici izinlerine sahip olun ve yeniden deneyin.

    ![Kiracı URL'si + simgesi](common/provisioning-testconnection-tenanturltoken.png)

6. İçinde **bildirim e-posta** alanında, bir kişi veya grubun ve sağlama hata bildirimleri almak - onay e-posta adresi girin **birhataoluşursa,bire-postabildirimigönder**.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory Kullanıcıları için tuş takımı**.

    ![Tuş takımı kullanıcı eşlemeleri](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. İçinde tuş takımı için Azure AD'den eşitlenen kullanıcı özniteliklerini gözden **eşleme özniteliği** bölümü. Seçilen öznitelikler **eşleşen** özellikleri tuş takımı güncelleştirme işlemleri için kullanıcı hesaplarını eşleştirmek için kullanılır. Seçin **Kaydet** düğmesine değişiklikleri uygulayın.

    ![Tuş takımı kullanıcı öznitelikleri](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Kapsam belirleme filtrelerini yapılandırmak için aşağıdaki yönergelere bakın [Scoping filtre öğretici](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Azure AD sağlama hizmeti için tuş takımı etkinleştirmek için değiştirin **sağlama durumu** için **üzerinde** içinde **ayarları** bölümü.

    ![Açıkken sağlama durumu](common/provisioning-toggle-on.png)

12. Kullanıcılara ve/veya istediğiniz grupları tuş takımı sağlamak için istenen değerleri seçerek tanımlamak **kapsam** içinde **ayarları** bölümü.

    ![Kapsam sağlama](common/provisioning-scope.png)

13. Sağlama için hazır olduğunuzda, tıklayın **Kaydet**.

    ![Sağlama yapılandırmasını kaydetme](common/provisioning-configuration-save.png)

Bu işlem, tüm kullanıcıların ilk eşitleme başlar ve/veya tanımlı gruplar **kapsam** içinde **ayarları** bölümü. İlk eşitleme yaklaşık 40 dakikada Azure AD sağlama hizmeti çalışıyor sürece oluşan sonraki eşitlemeler uzun sürer. Kullanabileceğiniz **eşitleme ayrıntıları** bölüm ilerlemeyi izlemek ve Azure AD sağlama hizmeti üzerinde tuş takımı tarafından gerçekleştirilen tüm eylemler açıklayan Etkinlik Raporu sağlama için bağlantıları izleyin.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [raporlama hesabı otomatik kullanıcı hazırlama](../manage-apps/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Bağlayıcı sınırlamaları
* Tuş takımı grubu yeniden adlandırma şu anda desteklemiyor. Buna herhangi bir değişiklik **displayName** azure'da bir grubun AD değil güncelleştirilecek ve tuş takımı yansıtılır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kullanıcı hesabı, kurumsal uygulamalar için sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri gözden geçirin ve sağlama etkinliği raporları alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)
