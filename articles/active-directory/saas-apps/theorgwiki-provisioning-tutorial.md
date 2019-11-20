---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Orgwiki 'yi yapılandırma | Microsoft Docs"
description: Kullanıcı hesaplarını otomatik olarak sağlamak ve bunun için Orgwiki 'ye kaldırmak üzere Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: f691b3e80bdde546074b9f243add82ace00c6301
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576387"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Öğretici: Otomatik Kullanıcı sağlaması için TheOrgWiki 'yi yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, Kullanıcı ve/veya grupları Orgwiki 'ye otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için, The Orgwiki ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* [Bir OrgWiki kiracısı](https://www.theorgwiki.com/welcome/).
* Yönetici izinlerine sahip olan bir kullanıcı hesabı.

## <a name="assign-users-to-theorgwiki"></a>Kullanıcıları Orgwiki 'ye atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen atama adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların TheOrgWiki 'ye erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları bir Orgwiki 'ye atayabilirsiniz:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Orgwiki 'ye Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için, tek bir Azure AD kullanıcısının TheOrgWiki 'ye atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Orgwiki 'ye atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-theorgwiki-for-provisioning"></a>Sağlama için TheOrgWiki 'yi ayarlama

Orgwiki 'yi Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmadan önce, Orgwiki üzerinde SCıM sağlamasını etkinleştirmeniz gerekir.

1. [Theorgwiki Yönetici konsolunuza](https://www.theorgwiki.com/login/)oturum açın. **Yönetici Konsolu**' na tıklayın.

    ![TheOrgWiki SCıM Ekle](media/theorgwiki-provisioning-tutorial/login.png)

2. Yönetici Konsolu 'nda **Ayarlar sekmesine**tıklayın. 

    ![TheOrgWiki SCıM Ekle](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. **Hizmet hesaplarına**gidin.

    ![TheOrgWiki SCıM Ekle](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. **+ Hizmet hesabı**' na tıklayın. **Hizmet hesabı türü**altında **belirteç tabanlı**' i seçin. **Kaydet**’e tıklayın.

    ![TheOrgWiki SCıM Ekle](media/theorgwiki-provisioning-tutorial/auth.png)

5.  **Etkin belirteçleri**kopyalayın. Bu değer, Azure portal TheOrgWiki uygulamanızın sağlama sekmesindeki gizli belirteç alanına girilir.
     
    ![TheOrgWiki SCıM Ekle](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Galeriden TheOrgWiki ekleme

Azure AD ile otomatik Kullanıcı sağlamak üzere Orgwiki 'yi yapılandırmak için Azure AD uygulama galerisindeki Orgwiki 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **theorgwiki**yazın, sonuçlar panelinde **theorgwiki** ' yi seçin. 

    ![Sonuçlar listesinde TheOrgWiki](common/search-new-app.png)

5. Sizi Orgwiki 'nin oturum açma sayfasına yönlendirecek olan **orgwiki** düğmesini seçin. 

    ![TheOrgWiki SCıM Ekle](media/theorgwiki-provisioning-tutorial/image00.png)

6.  Sağ üst köşede **oturum aç**' ı seçin.

    ![TheOrgWiki SCıM Ekle](media/theorgwiki-provisioning-tutorial/image02.png)

7. Orgwiki bir Openıdconnect uygulaması olduğu için, Microsoft iş hesabınızı kullanarak OrgWiki 'de oturum açmayı seçin.

    ![TheOrgWiki SCıM Ekle](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Başarılı bir kimlik doğrulamasından sonra uygulama, kiracınıza otomatik olarak eklenir ve TheOrgWiki hesabınıza yönlendirilirsiniz.

    ![OrgWiki SCıM Ekle](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Otomatik Kullanıcı sağlamayı, Orgwiki 'ye yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni kullanarak Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan Kullanıcı ve/veya grupları oluşturma, güncelleştirme ve devre dışı bırakma adımları anlatılmaktadır.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Azure AD 'de TheOrgWiki için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kuruluş uygulamaları**' nı seçin ve ardından **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Theorgwiki**' yi seçin.

    ![Uygulamalar listesindeki OrgWiki bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama sekmesi](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` **kiracı URL 'sini**girin. 

    Örnek: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> Alt **etki alanı değeri** yalnızca, TheOrgWiki için ilk kaydolma işlemi sırasında ayarlanabilir.
 
6. Daha önce Orgwiki 'den aldığınız **gizli belirteç** alanında belirteç değerini girin. Azure AD 'nin bir Orgwiki 'ye bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, TheOrgWiki hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

7. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

8. **Kaydet**’e tıklayın.

9. **Eşlemeler** bölümünde **Kullanıcı Azure Active Directory Kullanıcıları Orgwiki olarak eşitler**' ı seçin.

    ![TheOrgWiki Kullanıcı eşlemeleri](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. **Öznitelik eşleme** bölümünde Azure AD 'Den TheOrgWiki 'ye eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için TheOrgWiki içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![TheOrgWiki Kullanıcı öznitelikleri](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

12. ' Orgwiki ' için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

13. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek orgwiki 'ye sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

14. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin sonraki eşitlemeler daha uzun sürer. Kullanıcıların ve/veya grupların sağlaması için ne kadar süreceğine ilişkin daha fazla bilgi için bkz. [kullanıcıları sağlamak için ne kadar sürer](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

İlerleme durumunu izlemek ve sağlama etkinliğinizdeki bağlantıları izlemek için **geçerli durum** bölümünü kullanabilirsiniz. bu Işlem, Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklar. Daha fazla bilgi için bkz. [Kullanıcı hazırlama durumunu denetleme](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Azure AD sağlama günlüklerini okumak için bkz. [Otomatik Kullanıcı hesabı sağlama hakkında raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri incelemeyi ve sağlama etkinliğinde rapor almayı öğrenin](../manage-apps/check-status-user-account-provisioning.md).