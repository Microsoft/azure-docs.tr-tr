---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için SmartFile 'ı yapılandırma | Microsoft Docs"
description: Akıllı dosya için Kullanıcı hesaplarını otomatik olarak sağlamak ve yeniden sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
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
ms.openlocfilehash: 03c7efd390d4714680ed2ccd54d297b8137aaafc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348679"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için SmartFile 'ı yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları SmartFile 'a otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için SmartFile ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* [SmartFile kiracısı](https://www.SmartFile.com/pricing/).
* Akıllı dosyada yönetici izinlerine sahip bir kullanıcı hesabı.

## <a name="assigning-users-to-smartfile"></a>Kullanıcıları SmartFile 'a atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların SmartFile erişimine ihtiyacı olduğuna karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları SmartFile öğesine atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>SmartFile 'a Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için SmartFile 'a tek bir Azure AD kullanıcısı atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı SmartFile 'a atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-smartfile-for-provisioning"></a>Sağlama için SmartFile kurulumu

SmartFile 'ı Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmadan önce, SmartFile üzerinde SCıM sağlamasını etkinleştirmeniz ve gereken ek ayrıntıları toplamanız gerekecektir.

1. SmartFile Yönetici konsolunuzun oturumunu açın. SmartFile yönetici konsolunun sağ üst köşesine gidin. **Ürün anahtarı** seçin.

    ![SmartFile Yönetici Konsolu](media/smartfile-provisioning-tutorial/login.png)

2. Bir taşıyıcı belirteci oluşturmak için **ürün anahtarını** ve **Ürün parolasını** kopyalayın. Bunları aralarında iki nokta üst üste ekleyerek not defterine yapıştırın.
    
     ![Ürün anahtarı ve ürün parolası metin kutuları ile adlandırılan ürün anahtarı bölümünün ekran görüntüsü.](media/smartfile-provisioning-tutorial/auth.png)

    ![Ürün anahtarını ve Ürün parolasını iki noktayla ayırarak gösteren düz metin ekran görüntüsü.](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>Galeriden akıllı dosya ekleme

SmartFile 'ı Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmak üzere, Azure AD uygulama galerisindeki SmartFile 'ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD uygulama galerisinden SmartFile eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **SmartFile** yazın, sonuçlar panelinde **SmartFile** ' ı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuç listesinde SmartFile](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>SmartFile için otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni kullanarak SmartFile içindeki kullanıcıları ve/veya grupları Azure AD 'de Kullanıcı ve/veya grup atamalarına göre oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

> [!TIP]
> SmartFile [Çoklu oturum açma öğreticisinde](SmartFile-tutorial.md)sunulan yönergeleri Izleyerek SmartFile için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilse de

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Azure AD 'de SmartFile için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **SmartFile**' ı seçin.

    ![Uygulamalar listesindeki SmartFile bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5.  **Yönetici kimlik bilgileri** bölümünün altında, `https://<SmartFile sitename>.smartfile.com/ftp/scim` **kiracı URL 'sini** girin. Bir örnek şöyle görünmelidir `https://demo1test.smartfile.com/ftp/scim` . Daha önce **gizli bir belirteçte** aldığınız **taşıyıcı belirteç** değerini (ProductKey: productpassword) girin. Azure AD 'nin SmartFile 'a bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, SmartFile hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları SmartFile olarak eşitler**' ı seçin.

    ![SmartFile Kullanıcı eşlemeleri](media/smartfile-provisioning-tutorial/usermapping.png)

9. **Öznitelik eşleme** bölümünde Azure AD 'Den SmartFile 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, SmartFile içindeki kullanıcı hesaplarını güncelleştirme işlemleri için eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![SmartFile Kullanıcı öznitelikleri](media/smartfile-provisioning-tutorial/userattribute.png)

10. **Eşlemeler** bölümünde **Azure Active Directory grupları SmartFile olarak eşitler**' ı seçin.

    ![SmartFile grup eşlemeleri](media/smartfile-provisioning-tutorial/groupmapping.png)

11. **Öznitelik eşleme** bölümünde Azure AD 'Den SmartFile 'a eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, SmartFile içindeki grupları güncelleştirme işlemleri için eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![SmartFile grup öznitelikleri](media/smartfile-provisioning-tutorial/groupattribute.png)

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. SmartFile için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsamda** Istenen değerleri seçerek SmartFile 'a sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

    Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İşlem ilerlemesini izlemek ve akıllı dosya üzerinde Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinliği raporunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

    Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* SmartFile yalnızca sabit silmeleri destekler. 

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

 [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
