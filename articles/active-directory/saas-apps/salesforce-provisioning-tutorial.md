---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Salesforce yapılandırma | Microsoft Docs'
description: Azure AD 'den Salesforce 'a Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Salesforce ve Azure AD 'de gerçekleştirmek üzere gereken adımları öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: c5df0a5fc054a12e3fa2ef1e352645c57c357b01
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798740"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Salesforce yapılandırma

Bu öğreticinin amacı, Salesforce ve Azure AD 'de, Kullanıcı hesaplarını Salesforce 'a otomatik olarak sağlamak ve devre dışı bırakmak için gereken adımları gösterir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide anlatılan senaryoda aşağıdakilere sahip olduğunuz kabul edilmiştir:

* Azure Active Directory kiracısı
* Bir Salesforce.com kiracısı

> [!Note]
> Rol içeri aktarmaları sırasında Azure Active Directory roller el ile düzenlenmemelidir.

> [!IMPORTANT]
> Bir Salesforce.com deneme hesabı kullanıyorsanız otomatik Kullanıcı sağlamayı yapılandıracaksınız. Deneme hesaplarında, satın alınana kadar gerekli API erişimi etkin değildir. Bu öğreticiyi tamamlamaya yönelik ücretsiz bir [Geliştirici hesabı](https://developer.salesforce.com/signup) kullanarak bu sınırlamayı aşabilirsiniz.

Salesforce korumalı alanı ortamı kullanıyorsanız lütfen [Salesforce korumalı alanı tümleştirme öğreticisine](./salesforce-sandbox-tutorial.md)bakın.

## <a name="assigning-users-to-salesforce"></a>Kullanıcıları Salesforce 'a atama

Azure Active Directory, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyebilmek için "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı hesabı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya "atanmış" olan kullanıcılar ve gruplar eşitlenir.

Sağlama hizmetini yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların veya grupların Salesforce uygulamanıza erişmesi gerektiğine karar vermeniz gerekir. Bu kararı verdikten sonra, [bir kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md) bölümündeki yönergeleri izleyerek bu kullanıcıları Salesforce uygulamanıza atayabilirsiniz.

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Salesforce 'a Kullanıcı atamaya yönelik önemli ipuçları

* Sağlama yapılandırmasını test etmek için tek bir Azure AD kullanıcısının Salesforce 'a atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Salesforce 'a bir Kullanıcı atarken geçerli bir kullanıcı rolü seçmeniz gerekir. "Varsayılan erişim" rolü sağlama için çalışmıyor

    > [!NOTE]
    > Bu uygulama, kullanıcıların Azure AD 'de Kullanıcı atarken seçmek isteyebileceğiniz sağlama sürecinin bir parçası olarak Salesforce 'tan profilleri içeri aktarır. Salesforce 'tan içeri aktarılan profillerin Azure AD 'de roller olarak göründüğünü lütfen unutmayın.

## <a name="enable-automated-user-provisioning"></a>Otomatik Kullanıcı sağlamayı etkinleştir

Bu bölümde, Azure AD 'nizi [Salesforce 'ın Kullanıcı hesabı sağlama API 'si-V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)' ye bağlama ve sağlama hizmeti 'Ni, Azure AD 'de Kullanıcı ve grup atamasını temel alarak Salesforce 'ta atanan kullanıcı hesaplarını oluşturmak, güncelleştirmek ve devre dışı bırakmak için nasıl yapılandıracağınız konusunda kılavuzluk eder.

> [!Tip]
> Ayrıca, [Azure Portal](https://portal.azure.com)sağlanan yönergeleri izleyerek SALESFORCE için SAML tabanlı tek Sign-On de seçebilirsiniz. Çoklu oturum açma özelliği otomatik sağlanmadan bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="configure-automatic-user-account-provisioning"></a>Otomatik Kullanıcı hesabı sağlamayı yapılandırma

Bu bölümün amacı, Salesforce 'a Active Directory Kullanıcı hesaplarının Kullanıcı sağlamasını nasıl etkinleştireceğinizi özetler.

1. [Azure portal](https://portal.azure.com) **Azure Active Directory > Enterprise Apps > tüm uygulamalar** bölümüne gidin.

2. Çoklu oturum açma için zaten Salesforce yapılandırdıysanız, arama alanını kullanarak Salesforce örneğinizi arayın. Aksi takdirde, **Ekle** ' yi seçin ve uygulama galerisinde **Salesforce** için arama yapın. Arama sonuçlarından Salesforce ' ı seçin ve uygulama listenize ekleyin.

3. Salesforce örneğinizi seçin, sonra **sağlama** sekmesini seçin.

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Ekran görüntüsü, sağlama modu otomatik ve ayarlayabileceğiniz diğer değerlere ayarlanmış olan Salesforce sağlama sayfasını gösterir.](./media/salesforce-provisioning-tutorial/provisioning.png)

5. **Yönetici kimlik bilgileri** bölümünde aşağıdaki yapılandırma ayarlarını sağlayın:

    a. **Yönetici Kullanıcı adı** metin kutusuna Salesforce.com atanmış **Sistem Yöneticisi** profiline sahip bir Salesforce hesap adı yazın.

    b. **Yönetici parolası** metin kutusuna bu hesabın parolasını yazın.

6. Salesforce güvenlik belirtecinizi almak için yeni bir sekme açın ve aynı Salesforce yönetici hesabında oturum açın. Sayfanın sağ üst köşesinde, ad ' a tıklayın ve ardından **Ayarlar**' a tıklayın.

    ![Ekran görüntüsü seçili Ayarlar bağlantısını gösterir.](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Otomatik Kullanıcı sağlamayı etkinleştir")

7. Sol gezinti bölmesinde, **Kişisel bilgilerim** ' a tıklayarak ilgili bölümü genişletin ve ardından **güvenlik belirtecimi Sıfırla**' ya tıklayın.
  
    ![Ekran görüntüsü kişisel bilgilerim için seçili olan güvenlik belirtecinden sıfırlamayı gösterir.](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Otomatik Kullanıcı sağlamayı etkinleştir")

8. **Güvenlik belirtecini Sıfırla** sayfasında **güvenlik belirtecini Sıfırla** düğmesine tıklayın.

    ![Ekran görüntüsünde, açıklayıcı metin ve güvenlik belirtecini sıfırlama seçeneği ile rest güvenlik belirteci sayfası gösterilir](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Otomatik Kullanıcı sağlamayı etkinleştir")

9. Bu yönetici hesabıyla ilişkili e-posta gelen kutusunu kontrol edin. Yeni güvenlik belirtecini içeren Salesforce.com adresinden bir e-posta arayın.

10. Belirteci kopyalayın, Azure AD pencerenize gidin ve **gizli belirteç** alanına yapıştırın.

11. Salesforce örneği Salesforce kamu bulutunda ise **kiracı URL 'si** girilmelidir. Aksi takdirde, isteğe bağlıdır. "Https:// \<your-instance\> . My.Salesforce.com" biçimini kullanarak, \<your-instance\> Salesforce örneğinizin adıyla birlikte kiracı URL 'sini girin.

12. Azure portal, Azure AD 'nin Salesforce uygulamanıza bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın.

13. **Bildirim e-postası** alanına, sağlama hatası bildirimleri alması gereken kişinin veya grubun e-posta adresini girin ve aşağıdaki onay kutusunu işaretleyin.

14. Kaydet ' e tıklayın **.**  

15. Eşlemeler bölümünde **Azure Active Directory Kullanıcıları Salesforce** ' a eşitler ' ı seçin.

16. **Öznitelik eşlemeleri** bölümünde, Azure AD 'den Salesforce 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşlenen** özellikler olarak seçilen özniteliklerin, güncelleştirme Işlemleri için Salesforce 'daki Kullanıcı hesaplarıyla eşleşecek şekilde kullanıldığını unutmayın. Değişiklikleri uygulamak için Kaydet düğmesini seçin.

17. Salesforce için Azure AD sağlama hizmetini etkinleştirmek üzere ayarlar bölümünde **sağlama durumunu** **Açık** olarak değiştirin

18. Kaydet ' e tıklayın **.**

> [!NOTE]
> Kullanıcılar Salesforce uygulamasında sağlandıktan sonra, yönetici için dile özgü ayarları yapılandırmak gerekir. Dil yapılandırması hakkında daha fazla bilgi için lütfen [Bu](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) makaleye bakın.

Bu, kullanıcılar ve Gruplar bölümünde Salesforce 'a atanan tüm Kullanıcı ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, hizmetin çalıştığı sürece yaklaşık 40 dakikada bir gerçekleşen sonraki eşitlemeler yerine gerçekleştirilmesini daha uzun sürdüğüne unutmayın. İlerleme durumunu izlemek ve Salesforce uygulamanızdaki sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan etkinlik günlüklerinin sağlanması için bağlantıları izlemek üzere **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="common-issues"></a>Genel sorunlar
* Salesforce 'a erişimi yetkilendirmek için sorun yaşıyorsanız aşağıdakileri doğrulayın:
    * Kullanılan kimlik bilgileri Salesforce 'a yönetici erişimine sahiptir.
    * Kullanmakta olduğunuz Salesforce sürümü Web Erişimi destekler (örneğin, geliştirici, Enterprise, Sandbox ve Salesforce 'un sınırsız sürümleri).)
    * Web API erişimi kullanıcı için etkinleştirildi.
* Azure AD sağlama hizmeti, bir kullanıcı için dil, yerel ayar ve saat dilimini sağlamayı destekler. Bu öznitelikler varsayılan öznitelik eşlemeleridir ancak varsayılan bir kaynak özniteliği yoktur. Varsayılan kaynak özniteliğini seçtiğinizden ve kaynak özniteliğin SalesForce tarafından beklenen biçimde olduğundan emin olun. Örneğin, İngilizce (Birleşik Devletler) için Localesıdkey en_US. Doğru Localesıdkey biçimini öğrenmek için [burada](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) sunulan kılavuzu gözden geçirin. LanguageLocaleKey biçimleri [burada](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5)bulunabilir. Biçimin doğru olduğundan emin olmanın yanı sıra, [burada](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)açıklandığı gibi, dilin kullanıcılarınız için de etkinleştirildiğinden emin olmanız gerekebilir. 
* **Salesforcelicenselimitexcelıo:** Bu Kullanıcı için kullanılabilir lisans olmadığından, Kullanıcı hedef uygulamada oluşturulamadı. Hedef uygulama için ek lisanslar temin edebilir ya da doğru özniteliklerin doğru özniteliklerle atandığından emin olmak için Kullanıcı atamalarınızı ve öznitelik eşleme yapılandırmanızı gözden geçirin.
* **Salesforceduplicateusername:** Kullanıcı, başka bir Salesforce.com kiracısında çoğaltılan bir Salesforce.com ' username ' içerdiğinden sağlanamıyor.Salesforce.com ' de, ' username ' özniteliğinin değerleri tüm Salesforce.com kiracılar genelinde benzersiz olmalıdır.Varsayılan olarak, Azure Active Directory bir kullanıcının userPrincipalName 'i, Salesforce.com içinde ' username ' olur.İki seçenek sunulur.Bir seçenek, başka bir kiracıyı de yönetiyorsanız, kullanıcıyı diğer Salesforce.com kiracısında yinelenen ' username ' ile bulup yeniden adlandırmaya yönelik bir seçenektir.Diğer seçenek, Azure Active Directory kullanıcıdan, dizininizin tümleştirildiği Salesforce.com kiracısına erişimi kaldırdır. Sonraki eşitleme denemesinde bu işlemi yeniden deneyeceğiz. 
* **Salesforcerequiredfieldeksik:** Salesforce, kullanıcının başarıyla oluşturulmasını veya güncelleştirilmesini sağlamak için kullanıcının belirli özniteliklerin mevcut olmasını gerektirir. Bu Kullanıcı, gerekli özniteliklerin birini içermiyor. E-posta ve diğer ad gibi özniteliklerin Salesforce 'a sağlanmasını istediğiniz tüm kullanıcılara doldurulduğundan emin olun. [Öznitelik tabanlı kapsam filtrelerini](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)kullanarak bu özniteliklere sahip olmayan kullanıcıların kapsamını belirleyebilirsiniz. 
* Salesforce 'a sağlama için varsayılan öznitelik eşlemesi, Azure AD 'deki Approlet atamalarını Salesforce 'ta ProfileName 'e eşlemek için Singleapprotaatamalar ifadesini içerir. Öznitelik eşlemesi yalnızca bir rol sağlamayı desteklediğinden, kullanıcıların Azure AD 'de birden çok uygulama rolü ataması olmadığından emin olun. 
* Salesforce, e-posta güncelleştirmelerinin değiştirilmeden önce el ile onaylanmasını gerektirir. Sonuç olarak, kullanıcının e-postasını güncelleştirmek için sağlama günlüklerinde birden çok giriş görebilirsiniz (e-posta değişikliği onaylanana kadar).


## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](tutorial-list.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
* [Çoklu oturum açmayı yapılandırma](./salesforce-tutorial.md)
