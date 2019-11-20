---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Storegate 'i yapılandırma | Microsoft Docs"
description: Kullanıcı hesaplarını Storeg'e otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 628fa804-c0e6-4db1-ab6b-46ee9aab4d41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: 1769b46210c766adc876dc36bf4e646fb23a6823
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905309"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için Storegate yapılandırma

Bu öğreticinin amacı, Azure AD 'yi otomatik olarak sağlamak ve devre dışı bırakmak üzere Kullanıcı ve/veya grupları otomatik olarak sağlamak üzere yapılandırmak için, Storegate ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Bir Storegate kiracısı](https://www.storegate.com)
* Yönetici izinleriyle bir Storegüzerinde Kullanıcı hesabı.

## <a name="assign-users-to-storegate"></a>Kullanıcıları Storeg'e ata

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen atama adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Storeg'e erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Storeg'e atayabilirsiniz:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Kullanıcıları Stokmaya atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için tek bir Azure AD kullanıcısının Storeg'e atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Storeg'e atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-storegate-for-provisioning"></a>Sağlama için Storegate ayarlama

Azure AD ile otomatik Kullanıcı sağlama için storeg'ü yapılandırmadan önce, bazı sağlama bilgilerini storegden almanız gerekir.

1. [Storegate Yönetici konsolunuza](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) oturum açın ve sağ üst köşedeki Kullanıcı simgesine tıklayarak ayarlar ' a gidin ve **Hesap ayarları**' nı seçin.

    ![SCıM ekleme](media/storegate-provisioning-tutorial/admin.png)

2. Ayarlar içinde **takım > ayarlar** ' a gidin ve **Çoklu oturum açma** bölümünde geçiş anahtarının açık olduğunu doğrulayın.

    ![Ayarları Sonlandır](media/storegate-provisioning-tutorial/team.png)

    ![İki durumlu düğme](media/storegate-provisioning-tutorial/sso.png)

3. **Kiracı URL 'sini** ve **belirtecini**kopyalayın. Bu değerler, Azure portal Storegate uygulamanızın sağlama sekmesinde sırasıyla **kiracı URL 'si** ve **gizli belirteç** alanlarına girilir. 

    ![Oluşturma belirteci oluştur](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Galeriden Storegate ekleme

Azure AD ile otomatik Kullanıcı sağlama için storegu yapılandırmak üzere, Azure AD uygulama galerisindeki yönetilen SaaS uygulamaları listenize Storegate öğesini eklemeniz gerekir.

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama **kutusuna, Sonlandır ' ı girin,** sonuçlar panelinde **storegate** ' i seçin. 

    ![Sonuçlar listesinde durduruluyor](common/search-new-app.png)

5. **Storegate** düğmesini seçerek, sizi storeg'in oturum açma sayfasına yönlendirecektir. 

    ![Storegate OıDC Add](media/storegate-provisioning-tutorial/signup.png)

6.  [Storegate Yönetici konsolunuza](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) oturum açın ve sağ üst köşedeki Kullanıcı simgesine tıklayarak ayarlar ' a gidin ve **Hesap ayarları**' nı seçin.

    ![Oturum açma durduruluyor](media/storegate-provisioning-tutorial/admin.png)

7. Ayarlar içinde **takım > ayarlar** ' a gidin ve çoklu oturum açma bölümünde geçiş anahtarı ' na tıklayın, bu onay akışını başlatır. **Etkinleştir**' e tıklayın.

    ![Takım durduruluyor](media/storegate-provisioning-tutorial/team.png)

    ![SSO durduruluyor](media/storegate-provisioning-tutorial/sso.png)

    ![Etkinleştirme durduruluyor](media/storegate-provisioning-tutorial/activate.png)

8. Storeg, bir Openıdconnect uygulaması olduğundan, Microsoft iş hesabınızı kullanarak Storeg'i açmak için oturum açmayı seçin.

    ![OIDC oturumu storeg](media/storegate-provisioning-tutorial/login.png)

9. Başarılı bir kimlik doğrulamasından sonra, onay sayfasının onay isteğini kabul edin. Uygulama daha sonra kiracınıza otomatik olarak eklenir ve Storegate hesabınıza yönlendirilirsiniz.

    ![OIDC onayı storeg](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Otomatik Kullanıcı sağlamayı Storeg'e yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alarak Storegiçindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında size kılavuzluk eder.

> [!NOTE]
> Storeg'in SCıM uç noktası hakkında daha fazla bilgi edinmek için [buna](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)bakın.

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Azure AD 'de Storeg'in otomatik Kullanıcı sağlamasını yapılandırmak için

1. [Azure portalında](https://portal.azure.com) oturum açın. **Kuruluş uygulamaları**' nı seçin ve ardından **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Storegate**' i seçin.

    ![Uygulamalar listesindeki Storegate bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama sekmesi](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, **kiracı URL 'sindeki**`https://dialpad.com/scim` girin. Daha önce aldığınız ve daha önce **gizli bir belirteçte**daha önce kaydettiğiniz değeri girin. Azure AD 'nin Storeg'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Storegate hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

7. **Save (Kaydet)** düğmesine tıklayın.

8. **Eşlemeler** bölümünde, **kullanıcıları stokmak için Azure Active Directory eşitlemeyi**seçin.

    ![Kullanıcı eşlemelerini stoate](media/storegate-provisioning-tutorial/usermappings.png)

9. **Öznitelik eşleme** bölümünde Azure AD 'Den storegile eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Storegate içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Kullanıcı özniteliklerini stoate](media/storegate-provisioning-tutorial/userattributes.png)

10. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

11. Azure AD sağlama hizmetini Storegiçin etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** Içindeki istenen değerleri seçerek stokmek için sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

13. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilir ve Azure AD sağlama hizmeti tarafından durdurulmakta olan tüm eylemleri açıklayan etkinlik raporuna yönelik bağlantıları izleyebilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)
