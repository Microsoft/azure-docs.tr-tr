---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için öncelik matrisi yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını öncelik matrisine otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a4598a99-3c98-4c14-86c2-95cc562e2439
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: 15ec201851cf52f651e32959b30c1d8579eb1cea
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152404"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlama için öncelik matrisini yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları öncelik matrisine otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için öncelik matrisi ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Bir öncelik matrisi kiracısı](https://appfluence.com/pricing/)
* Yönetici izinlerine sahip bir öncelik matrisine sahip bir kullanıcı hesabı.

## <a name="assign-users-to-priority-matrix"></a>Kullanıcıları öncelik matrisine ata

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen atama adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların öncelik matrisine erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları öncelik matrisine atayabilirsiniz:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Öncelik matrisine Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için tek bir Azure AD kullanıcısının öncelik matrisine atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı öncelik matrisine atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-priority-matrix-for-provisioning"></a>Sağlama için öncelik matrisi ayarla

Azure AD ile otomatik Kullanıcı sağlama için öncelik matrisini yapılandırmadan önce, öncelik matrisinden bazı sağlama bilgileri almanız gerekir.

1. [Öncelik matrisi Yönetici konsolunuza](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning)oturum açın.

3. Öncelik matrisi için **OAuth oturum açma belirteci** ' ne tıklayın

    ![Öncelik matrisi SCıM Ekle](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. **Yenı belirteç al** düğmesine tıklayın. **Belirteç dizesini**kopyalayın. Bu değer, Azure portal öncelik matrisi uygulamanızın sağlama sekmesindeki **gizli belirteç** alanına girilir. 

    ![Öncelik matrisi belirteç oluştur](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Galeriden öncelik matrisi Ekle

Azure AD ile otomatik Kullanıcı sağlamaya yönelik öncelik matrisini yapılandırmak için Azure AD uygulama galerisinden yönetilen SaaS uygulamaları listenize öncelik matrisi eklemeniz gerekir.

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Öncelik matrisi**girin, sonuçlar panelinde **Öncelik matrisi** ' ni seçin. 

    ![Sonuçlar listesindeki öncelik matrisi](common/search-new-app.png)

5. Öncelik matrisinin oturum açma sayfasına yönlendiren **Öncelik matrisi Için kaydolun** düğmesini seçin. 

    ![Öncelik matrisi OıDC Add](media/priority-matrix-provisioning-tutorial/signup.png)

6. Öncelik matrisi bir Openıdconnect uygulaması olduğundan, Microsoft iş hesabınızı kullanarak öncelik matrisine oturum açmayı seçin.

    ![Öncelik matrisi OıDC oturum açma](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Başarılı bir kimlik doğrulamasından sonra, onay sayfasının onay isteğini kabul edin. Uygulama daha sonra kiracınıza otomatik olarak eklenir ve öncelik matrisi hesabınıza yönlendirilirsiniz.

    ![Öncelik matrisi OIDC onayı](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Otomatik Kullanıcı sağlamayı öncelik matrisine yapılandırma 

Bu bölüm, Azure AD sağlama hizmetini kullanarak Kullanıcı ve/veya grup atamalarını Azure AD 'de Kullanıcı ve/veya Grup atamaları temelinde oluşturma, güncelleştirme ve devre dışı bırakma adımlarında size rehberlik eder.

> [!NOTE]
> Öncelik matrisinin SCıM uç noktası hakkında daha fazla bilgi edinmek için [Kullanıcı hazırlama ve öncelik matrisine](https://appfluence.com/help/article/user-provisioning/)bakın.

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Azure AD 'de öncelik matrisi için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın. **Kuruluş uygulamaları**' nı seçin ve ardından **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Öncelik matrisi**' nı seçin.

    ![Uygulamalar listesindeki öncelik matrisi bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama sekmesi](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, **kiracı URL 'sindeki**`https://sync.appfluence.com/scim/v2/` girin. Daha önce aldığınız ve daha önce **gizli bir belirteçte**öncelik matrisinden kaydettiğiniz değeri girin. Azure AD 'nin öncelik matrisine bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, öncelik matrisi hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları öncelik matrisine eşitler**' ı seçin.

    ![Öncelikli matris Kullanıcı eşlemeleri](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. **Öznitelik eşleme** bölümünde Azure AD 'Den öncelik matrisine eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için öncelik matrisindeki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Öncelik matrisi Kullanıcı öznitelikleri](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

11. Azure AD sağlama hizmetini öncelik matrisi için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsamda** Istenen değerleri seçerek öncelik matrisine sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

13. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek için **eşitleme ayrıntıları** bölümünü ve Azure AD sağlama hizmeti 'Nin öncelik matrisi üzerinde gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinlik raporuna ilişkin bağlantıları takip edebilirsiniz.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)


