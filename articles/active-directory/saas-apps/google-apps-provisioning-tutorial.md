---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için G Suite 'i yapılandırma | Microsoft Docs"
description: Azure AD 'den G Suite 'e Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: beaa8561028a9e21d0623c0eb8e19592f3cad055
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167867"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için G Suite 'i yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları G Suite 'e otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için G Suite ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).

> [!NOTE]
> G Suite Bağlayıcısı son zamanlarda 2019 Ekim tarihinde güncelleştirildi. G Suite bağlayıcısında yapılan değişiklikler şunları içerir:
- Ek G Suite Kullanıcı ve grup öznitelikleri için destek eklendi. 
- [Burada](https://developers.google.com/admin-sdk/directory)tanımlananla eşleşecek şekilde, G Suite hedef öznitelik adları güncelleştirildi.
- Varsayılan öznitelik eşlemeleri güncelleştirildi.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini G Suite ile yapılandırmak için aşağıdaki öğeler gereklidir:

- Bir Azure AD kiracısı
- [Bir G Suite kiracısı](https://gsuite.google.com/pricing.html)
- Yönetici izinlerine sahip bir G Suite üzerinde bir kullanıcı hesabı.

## <a name="assign-users-to-g-suite"></a>Kullanıcıları G Suite 'e atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen atama adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların G Suite 'e erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları G Suite 'e atayabilirsiniz:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>G Suite 'e Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için G Suite 'e tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı G Suite 'e atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-g-suite-for-provisioning"></a>Sağlama için G Suite kurulumu

Azure AD ile otomatik Kullanıcı sağlama için G Suite 'i yapılandırmadan önce, G Suite üzerinde SCıM sağlamasını etkinleştirmeniz gerekir.

1. [G Suite yönetici konsolunda](https://admin.google.com/) Yönetici hesabınızla oturum açın ve ardından **güvenlik**' i seçin. Bağlantıyı görmüyorsanız, ekranın alt kısmındaki **daha fazla denetim** menüsünde gizli olabilir.

    ![Güvenlik ' i seçin.][10]

2. **Güvenlik** sayfasında **API başvurusu**' nu seçin.

    ![API başvurusunu seçin.][15]

3. **API erişimini etkinleştir**' i seçin.

    ![API başvurusunu seçin.][16]

   > [!IMPORTANT]
   > G Suite 'e sağlamayı planladığınız her kullanıcı için, Azure AD 'deki Kullanıcı adları özel bir etki alanına bağlı **olmalıdır** . Örneğin, bob@contoso.onmicrosoft.com gibi görünen Kullanıcı adları G Suite tarafından kabul edilmez. Diğer taraftan bob@contoso.com kabul edilir. Mevcut bir kullanıcının etki alanını [buradaki](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)yönergeleri izleyerek değiştirebilirsiniz.

4.  İstediğiniz özel etki alanlarınızı Azure AD ile ekledikten ve doğruladıktan sonra, bunları G Suite ile yeniden doğrulamanız gerekir. G Suite 'teki etki alanlarını doğrulamak için aşağıdaki adımlara bakın:

    a. [G Suite yönetici konsolunda](https://admin.google.com/) **etki alanları**' nı seçin.

    ![Etki alanlarını seçin][20]

    b. **Etki alanı veya etki alanı diğer adı Ekle**' yi seçin.

    ![Yeni etki alanı Ekle][21]

    c. **Başka bir etki alanı Ekle**' yi seçin ve sonra eklemek istediğiniz etki alanının adını yazın.

    ![Etki alanı adınızı yazın][22]

    d. **Devam ' ı seçin ve etki alanı sahipliğini doğrulayın**. Ardından, etki alanı adının sahip olduğunuzu doğrulamak için adımları izleyin. Google ile etki alanınızı doğrulamaya yönelik kapsamlı yönergeler için bkz. [sitenizin sahipliğini doğrulama](https://support.google.com/webmasters/answer/35179).

    e. G Suite 'e eklemek istediğiniz diğer etki alanları için önceki adımları tekrarlayın.

5. Sonra, G Suite 'de Kullanıcı sağlamayı yönetmek için kullanmak istediğiniz yönetici hesabı ' nı saptayın. **Yönetici rolleri**' ne gidin.

    ![Google Apps 'i seçme][26]
    
6. Bu hesabın **yönetici rolü** için, bu rolün **ayrıcalıklarını** düzenleyin. Bu hesabın sağlanması için kullanılabilmesi için tüm **yönetıcı API ayrıcalıklarını** etkinleştirdiğinizden emin olun.

    ![Google Apps 'i seçme][27]

## <a name="add-g-suite-from-the-gallery"></a>Galeriden G Suite ekleme

Azure AD ile otomatik Kullanıcı sağlamaya yönelik G Suite 'i yapılandırmak için Azure AD Uygulama Galerisi 'nden yönetilen SaaS uygulamaları listenize G Suite eklemeniz gerekir. 

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **g Suite**girin, sonuçlar panelinde **g Suite** ' i seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde G Suite](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>G Suite 'e otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan G Suite 'teki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımları boyunca size kılavuzluk eder.

> [!TIP]
> G Suite [Çoklu oturum açma öğreticisinde](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial)sunulan yönergeleri Izleyerek g SUITE için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

> [!NOTE]
> G paketinin Dizin API 'SI uç noktası hakkında daha fazla bilgi edinmek için [DIZIN API](https://developers.google.com/admin-sdk/directory)'sine bakın.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Azure AD 'de G Suite için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın. **Kuruluş uygulamaları**' nı seçin ve ardından **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **G Suite**' i seçin.

    ![Uygulamalar listesindeki G Suite bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama sekmesi](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde **Yetkilendir**' i seçin. Yeni bir tarayıcı penceresinde bir Google yetkilendirmesi iletişim kutusu açar.

    ![G Suite yetkilendirme](media/google-apps-provisioning-tutorial/authorize.png)

6. G Suite kiracınızda değişiklik yapmak için Azure AD izinleri vermek istediğinizi onaylayın. **Kabul Et**’i seçin.

    ![İzinleri onaylayın.][28]

7. Azure portal Azure AD 'nin uygulamanıza bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, G Suite hesabınızın Takım Yöneticisi izinlerine sahip olduğundan emin olun. Sonra **Yetkilendir** adımını yeniden deneyin.

8. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

8. **Save (Kaydet)** düğmesine tıklayın.

9. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları G/Suite ile eşitler**' ı seçin.

    ![G Suite Kullanıcı eşlemeleri](media/google-apps-provisioning-tutorial/usermappings.png)

10. **Öznitelik eşleme** bölümünde Azure AD 'Den G Suite 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için G Suite içindeki kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![G Suite Kullanıcı öznitelikleri](media/google-apps-provisioning-tutorial/userattributes.png)

11. **Eşlemeler** bölümünde **Azure Active Directory gruplarını G Suite olarak eşitler**' ı seçin.

    ![G Suite grubu eşlemeleri](media/google-apps-provisioning-tutorial/groupmappings.png)

12. **Öznitelik eşleme** bölümünde Azure AD 'Den G Suite 'e eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için G Suite grupları ile eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![G Suite grubu öznitelikleri](media/google-apps-provisioning-tutorial/groupattributes.png)

13. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

14. G Suite için Azure AD sağlama hizmeti 'ni etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

15. **Ayarlar** bölümünde **kapsamda** Istenen değerleri seçerek G Suite 'e sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

16. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek için **eşitleme ayrıntıları** bölümünü ve G Suite ÜZERINDE Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinliği raporuna ilişkin bağlantıları takip edebilirsiniz.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)


<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
