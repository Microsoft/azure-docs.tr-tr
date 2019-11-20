---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için 15Beş yapılandırın | Microsoft Docs'
description: Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı 15 beş 'a öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: c49d6fff50e74e67a4fef4ba9915efb72698a5b4
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641805"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Öğretici: Otomatik Kullanıcı sağlama için 15Beş yapılandırın

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları 15Beş ' a otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak üzere 15 beş ve Azure Active Directory (Azure AD) olarak gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* [15 beş kiracı](https://www.15five.com/pricing/).
* Yönetici izinlerine sahip 15 beş bir kullanıcı hesabı.

## <a name="assigning-users-to-15five"></a>15 beş 'e Kullanıcı atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların 15Beş 'a erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları 15 beş 'e atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-15five"></a>15 beş 'e Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için tek bir Azure AD kullanıcısının 15 milyon 'e atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı 15Beş 'a atarken atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-15five-for-provisioning"></a>Sağlama için 15 milyon kurulum

Azure AD ile otomatik Kullanıcı sağlama için 15Beş yapılandırmadan önce, 15Beş üzerinde SCıM sağlamasını etkinleştirmeniz gerekir.

1. [15 beş yönetici konsolunda](https://my.15five.com/)oturum açın. **Özellikler > tümleştirmelere**gidin.

    ![15Beş Yönetici Konsolu](media/15five-provisioning-tutorial/integration.png)

2.  **SCIM 2,0**' ye tıklayın.

    ![15Beş Yönetici Konsolu](media/15five-provisioning-tutorial/image00.png)

3.  Bir **OAuth belirteci oluşturmak > SCIM tümleştirmesi**' ne gidin.

    ![15Beş SCıM Ekle](media/15five-provisioning-tutorial/image02.png)

4.  **SCIM 2,0 temel URL 'si** ve **erişim belirtecinin**değerlerini kopyalayın. Bu değer, Azure portal 15Beş uygulamanızın sağlama sekmesinde **kiracı URL 'si** ve **gizli belirteç** alanına girilir.
    
    ![15Beş SCıM Ekle](media/15five-provisioning-tutorial/image03.png)

## <a name="add-15five-from-the-gallery"></a>Galeriden 15Beş ekleyin

Azure AD ile otomatik Kullanıcı sağlamak üzere 15Beş yapılandırmak için, Azure AD uygulama galerisinden yönetilen SaaS uygulamaları listenize 15Beş eklemeniz gerekir.

**Azure AD uygulama galerisinden 15Beş eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **15beş**girin, sonuçlar panelinde **15** milyon seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuç listesinde 15 beş](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-15five"></a>Otomatik Kullanıcı sağlamayı 15 beş olarak yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni, Kullanıcı ve/veya grup atamalarını Azure AD 'de oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD sağlama hizmetini yapılandırma adımlarında kılavuzluk eder.

> [!TIP]
> Ayrıca, 15 [adet çoklu oturum](15five-tutorial.md)açma öğreticisinde sunulan yönergeleri Izleyerek, 15 mılyon için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Azure AD 'de 15Beş için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kuruluş uygulamaları**' nı seçin ve ardından **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **15Beş**' u seçin.

    ![Uygulamalar listesindeki 15 beş bağlantı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama sekmesi](common/provisioning-automatic.png)

5.  Yönetici kimlik bilgileri bölümünde, sırasıyla **kiracı URL 'si** ve **gizli belirteç** ' de bulunan **SCIM 2,0 temel URL 'sini ve erişim belirteci** değerlerini girin. Azure AD 'nin 155 ' e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, 15 beş hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde, **Azure Active Directory kullanıcılarını 15 beş ile eşitler**' ı seçin.

    ![15Beş kullanıcı eşlemesi](media/15five-provisioning-tutorial/usermapping.png)

9. **Öznitelik eşleme** bölümünde Azure AD 'Den 15beş 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için 15 milyon içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![15 beş kullanıcı özniteliği](media/15five-provisioning-tutorial/userattribute.png)

10. **Eşlemeler** bölümünde **Azure Active Directory gruplarını 15Beş olarak eşitler**' ı seçin.

    ![15Beş grup eşlemesi](media/15five-provisioning-tutorial/groupmapping.png)

11. **Öznitelik eşleme** bölümünde Azure AD 'Den 15beş 'a eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için 15 beş ' deki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![15 beş Grup özniteliği](media/15five-provisioning-tutorial/groupattribute.png)

12. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

13. Azure AD sağlama hizmeti 'ni 15Beş için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek 15beş 'a sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

15. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

    Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilir ve Azure AD sağlama hizmeti tarafından 15 milyon üzerinde gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinlik raporuna ilişkin bağlantıları izleyebilirsiniz.

    Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../manage-apps/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* 15Beş, kullanıcılar için sabit silmeleri desteklemez.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri incelemeyi ve sağlama etkinliğinde rapor almayı öğrenin](../manage-apps/check-status-user-account-provisioning.md).
