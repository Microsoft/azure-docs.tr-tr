---
title: 'Öğretici: GitHub için Kullanıcı hazırlama-Azure AD'
description: Kullanıcı hesaplarını GitHub 'a otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: Zhchia
ms.openlocfilehash: 9d9699c564476e116654f700c32dd47b7f6d5b81
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504575"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için GitHub 'ı yapılandırma

Bu öğreticinin amacı, Azure AD 'den GitHub ' dan Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için GitHub ve Azure AD 'de gerçekleştirmeniz gereken adımları gösteriyoruz.

> [!NOTE]
> Azure AD sağlama tümleştirmesi, GitHub Enterprise [faturalandırma planındaki](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)GitHub [Kurumsal bulut](https://help.github.com/articles/github-s-products/#github-enterprise) müşterileri tarafından KULLANıLABILEN [GitHub SCIM API](https://developer.github.com/v3/scim/)'sini kullanır.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide anlatılan senaryoda aşağıdakilere sahip olduğunuz kabul edilmiştir:

* Azure Active Directory kiracısı
* GitHub Enterprise [Cloud](https://help.github.com/articles/github-s-products/#github-enterprise)'da oluşturulmuş GitHub [Enterprise faturalandırma planını](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) gerektiren bir GitHub kuruluşu
* Kuruluş için yönetici izinlerine sahip GitHub 'da bir kullanıcı hesabı
* [GitHub Enterprise bulut organizasyonu için yapılandırılmış SAML](./github-tutorial.md)
* [Burada](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization) açıklandığı gibi, kuruluşunuz için OAuth erişiminin sağlandığından emin olun
* Tek bir kuruluşa SCıM sağlama yalnızca, kuruluş düzeyinde SSO etkinleştirildiğinde desteklenir

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="assigning-users-to-github"></a>GitHub 'a Kullanıcı atama

Azure Active Directory, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyebilmek için "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı hesabı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya "atanmış" olan kullanıcılar ve gruplar eşitlenir. 

Sağlama hizmetini yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcı ve/veya grupların GitHub uygulamanıza erişmesi gereken kullanıcıları temsil ettiğini belirlemeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları GitHub uygulamanıza atayabilirsiniz:

[Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>GitHub 'a Kullanıcı atamaya yönelik önemli ipuçları

* Sağlama yapılandırmasını test etmek için tek bir Azure AD kullanıcısının GitHub 'a atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* GitHub 'a bir Kullanıcı atarken, atama iletişim kutusunda **Kullanıcı** rolünü veya geçerli uygulamaya özgü bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolü sağlama için çalışmaz ve bu kullanıcılar atlanır.

## <a name="configuring-user-provisioning-to-github"></a>GitHub 'da Kullanıcı sağlamayı yapılandırma

Bu bölümde, Azure AD 'nizi GitHub 'ın Kullanıcı hesabı sağlama API 'sine bağlama ve sağlama hizmeti 'ni Azure AD 'de Kullanıcı ve grup atamasını temel alan GitHub 'da atanan kullanıcı hesaplarını oluşturmak, güncelleştirmek ve devre dışı bırakmak için yapılandırma işlemi kılavuzluk eder.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Azure AD 'de otomatik Kullanıcı hesabı sağlamayı GitHub 'a yapılandırma

1. [Azure portal](https://portal.azure.com) **Azure Active Directory > Enterprise Apps > tüm uygulamalar** bölümüne gidin.

2. Çoklu oturum açma için GitHub 'ı zaten yapılandırdıysanız arama alanını kullanarak GitHub örneğinizi arayın. Aksi takdirde, **Ekle** ' yi seçin ve uygulama galerisinde **GitHub** için arama yapın. Arama sonuçlarından GitHub ' ı seçin ve uygulama listenize ekleyin.

3. GitHub örneğinizi seçin, sonra **sağlama** sekmesini seçin.

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![GitHub sağlama](./media/github-provisioning-tutorial/GitHub1.png)

5. **Yönetici Kimlik Bilgileri** bölümünde **Yetki Ver**'e tıklayın. Bu işlem yeni bir tarayıcı penceresinde bir GitHub yetkilendirme iletişim kutusu açar. Erişim yetkisi verme konusunda onaylantığınızdan emin olmanız gerektiğini unutmayın. [Burada](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)açıklanan yönergeleri izleyin.

6. Yeni pencerede, yönetici hesabınızı kullanarak GitHub 'da oturum açın. Elde edilen yetkilendirme iletişim kutusunda, sağlamayı etkinleştirmek istediğiniz GitHub ekibini seçin ve sonra **Yetkilendir**' i seçin. İşlem tamamlandıktan sonra hazırlama yapılandırmasını tamamlamak için Azure portalına geri dönün.

    ![Ekran görüntüsü, GitHub için oturum açma sayfasını gösterir.](./media/github-provisioning-tutorial/GitHub2.png)

7. Azure portal, **kiracı URL 'sini** girin ve Azure AD 'nin GitHub uygulamanıza bağlanabildiğinden emin olmak Için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, GitHub hesabınızın yönetici izinlerine sahip olduğundan ve **kiracı URL 'sinin** doğru şekilde yazıldığından emin olun, sonra da "Yetkilendir" adımını tekrar deneyin ( **kiracı URL 'sini** kurala göre oluşturabilirsiniz: `https://api.github.com/scim/v2/organizations/<Organization_name>` , kuruluşlarınızı GitHub hesabınızın altında bulabilirsiniz: **Ayarlar**  >  **kuruluşları**).

    ![Ekran görüntüsü, GitHub 'daki kuruluşlar sayfasını gösterir.](./media/github-provisioning-tutorial/GitHub3.png)

8. **Bildirim e-postası** alanında sağlama hatası bildirimleri alması gereken bir kişinin veya grubun e-posta adresini girin ve "bir hata oluştuğunda e-posta bildirimi gönder" onay kutusunu işaretleyin.

9. **Kaydet**’e tıklayın.

10. Eşlemeler bölümünde **Azure Active Directory Kullanıcıları GitHub**' a eşitler ' ı seçin.

11. **Öznitelik eşlemeleri** bölümünde, Azure AD 'den GitHub 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için GitHub 'daki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için Kaydet düğmesini seçin.

12. GitHub için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin

13. **Kaydet**’e tıklayın.

Bu işlem, kullanıcılar ve Gruplar bölümünde GitHub 'a atanan tüm Kullanıcı ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, hizmetin çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek ve sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan etkinlik günlüklerinin sağlanması için bağlantıları izlemek üzere **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)