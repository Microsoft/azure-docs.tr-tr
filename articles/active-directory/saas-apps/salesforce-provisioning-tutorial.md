---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Salesforce yapılandırma | Microsoft Docs'
description: Azure Active Directory ve Salesforce arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d87f935f503098757e4efe402b37958283431b6e
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74120542"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Salesforce yapılandırma

Bu öğreticinin amacı, Salesforce ve Azure AD 'de, Kullanıcı hesaplarını Salesforce 'a otomatik olarak sağlamak ve devre dışı bırakmak için gereken adımları gösterir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki öğelerin zaten olduğunu varsayar:

* Azure Active Directory kiracısı
* Bir Salesforce.com kiracısı

> [!IMPORTANT]
> Bir Salesforce.com deneme hesabı kullanıyorsanız otomatik Kullanıcı sağlamayı yapılandıracaksınız. Deneme hesaplarında, satın alınana kadar gerekli API erişimi etkin değildir. Bu öğreticiyi tamamlamaya yönelik ücretsiz bir [Geliştirici hesabı](https://developer.salesforce.com/signup) kullanarak bu sınırlamayı aşabilirsiniz.

Salesforce korumalı alanı ortamı kullanıyorsanız lütfen [Salesforce korumalı alanı tümleştirme öğreticisine](https://go.microsoft.com/fwLink/?LinkID=521879)bakın.

## <a name="assigning-users-to-salesforce"></a>Kullanıcıları Salesforce 'a atama

Azure Active Directory, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyebilmek için "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı hesabı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya "atanmış" olan kullanıcılar ve gruplar eşitlenir.

Sağlama hizmetini yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların veya grupların Salesforce uygulamanıza erişmesi gerektiğine karar vermeniz gerekir. Bu kararı verdikten sonra, [bir kurumsal uygulamaya Kullanıcı veya Grup atama](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) bölümündeki yönergeleri izleyerek bu kullanıcıları Salesforce uygulamanıza atayabilirsiniz.

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Salesforce 'a Kullanıcı atamaya yönelik önemli ipuçları

* Sağlama yapılandırmasını test etmek için tek bir Azure AD kullanıcısının Salesforce 'a atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Salesforce 'a bir Kullanıcı atarken geçerli bir kullanıcı rolü seçmeniz gerekir. "Varsayılan erişim" rolü sağlama için çalışmıyor

    > [!NOTE]
    > Bu uygulama, kullanıcıların Azure AD 'de Kullanıcı atarken seçmek isteyebileceğiniz sağlama sürecinin bir parçası olarak Salesforce 'tan profilleri içeri aktarır. Salesforce 'tan içeri aktarılan profillerin Azure AD 'de roller olarak göründüğünü lütfen unutmayın.

## <a name="enable-automated-user-provisioning"></a>Otomatik Kullanıcı sağlamayı etkinleştir

Bu bölümde, Azure AD 'nizi [Salesforce 'ın Kullanıcı hesabı sağlama API 'si-V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)' ye bağlama ve sağlama hizmeti 'Ni, Azure AD 'de Kullanıcı ve grup atamasını temel alarak Salesforce 'ta atanan kullanıcı hesaplarını oluşturmak, güncelleştirmek ve devre dışı bırakmak için nasıl yapılandıracağınız konusunda kılavuzluk eder.

> [!Tip]
> [Azure Portal](https://portal.azure.com)' de sağlanan yönergeleri izleyerek SALESFORCE için SAML tabanlı çoklu oturum açmayı da tercih edebilirsiniz. Çoklu oturum açma özelliği otomatik sağlanmadan bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="configure-automatic-user-account-provisioning"></a>Otomatik Kullanıcı hesabı sağlamayı yapılandırma

Bu bölümün amacı, Salesforce 'a Active Directory Kullanıcı hesaplarının Kullanıcı sağlamasını nasıl etkinleştireceğinizi özetler.

1. [Azure portal](https://portal.azure.com) **Azure Active Directory > Enterprise Apps > tüm uygulamalar** bölümüne gidin.

2. Çoklu oturum açma için zaten Salesforce yapılandırdıysanız, arama alanını kullanarak Salesforce örneğinizi arayın. Aksi takdirde, **Ekle** ' yi seçin ve uygulama galerisinde **Salesforce** için arama yapın. Arama sonuçlarından Salesforce ' ı seçin ve uygulama listenize ekleyin.

3. Salesforce örneğinizi seçin, sonra **sağlama** sekmesini seçin.

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![alınıyor](./media/salesforce-provisioning-tutorial/provisioning.png)

5. **Yönetici kimlik bilgileri** bölümünde aşağıdaki yapılandırma ayarlarını sağlayın:

    a. **Yönetici Kullanıcı adı** metin kutusuna Salesforce.com atanmış **Sistem Yöneticisi** profiline sahip bir Salesforce hesap adı yazın.

    b. **Yönetici parolası** metin kutusuna bu hesabın parolasını yazın.

6. Salesforce güvenlik belirtecinizi almak için yeni bir sekme açın ve aynı Salesforce yönetici hesabında oturum açın. Sayfanın sağ üst köşesinde, ad ' a tıklayın ve ardından **Ayarlar**' a tıklayın.

    ![Otomatik Kullanıcı sağlamayı etkinleştir](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Otomatik Kullanıcı sağlamayı etkinleştir")

7. Sol gezinti bölmesinde, **Kişisel bilgilerim** ' a tıklayarak ilgili bölümü genişletin ve ardından **güvenlik belirtecimi Sıfırla**' ya tıklayın.
  
    ![Otomatik Kullanıcı sağlamayı etkinleştir](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Otomatik Kullanıcı sağlamayı etkinleştir")

8. **Güvenlik belirtecini Sıfırla** sayfasında **güvenlik belirtecini Sıfırla** düğmesine tıklayın.

    ![Otomatik Kullanıcı sağlamayı etkinleştir](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Otomatik Kullanıcı sağlamayı etkinleştir")

9. Bu yönetici hesabıyla ilişkili e-posta gelen kutusunu kontrol edin. Yeni güvenlik belirtecini içeren Salesforce.com adresinden bir e-posta arayın.

10. Belirteci kopyalayın, Azure AD pencerenize gidin ve **gizli belirteç** alanına yapıştırın.

11. Salesforce örneği Salesforce kamu bulutunda ise **kiracı URL 'si** girilmelidir. Aksi takdirde, isteğe bağlıdır. "Https://\<-instance\>. my.salesforce.com" biçimini kullanarak kiracı URL 'sini girin. Örneğin, "örneğini \<\>, Salesforce örneğinizin adıyla değiştirin.

12. Azure portal, Azure AD 'nin Salesforce uygulamanıza bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın.

13. **Bildirim e-postası** alanına, sağlama hatası bildirimleri alması gereken kişinin veya grubun e-posta adresini girin ve aşağıdaki onay kutusunu işaretleyin.

14. Kaydet ' e tıklayın **.**  

15. Eşlemeler bölümünde **Azure Active Directory Kullanıcıları Salesforce** ' a eşitler ' ı seçin.

16. **Öznitelik eşlemeleri** bölümünde, Azure AD 'den Salesforce 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşlenen** özellikler olarak seçilen özniteliklerin, güncelleştirme Işlemleri için Salesforce 'daki Kullanıcı hesaplarıyla eşleşecek şekilde kullanıldığını unutmayın. Değişiklikleri kaydetmek için Kaydet düğmesini seçin.

17. Salesforce için Azure AD sağlama hizmetini etkinleştirmek üzere ayarlar bölümünde **sağlama durumunu** **Açık** olarak değiştirin

18. Kaydet ' e tıklayın **.**

> [!NOTE]
> Kullanıcılar Salesforce uygulamasında sağlandıktan sonra, yönetici için dile özgü ayarları yapılandırmak gerekir. Dil yapılandırması hakkında daha fazla bilgi için lütfen [Bu](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) makaleye bakın.

Bu, kullanıcılar ve Gruplar bölümünde Salesforce 'a atanan tüm Kullanıcı ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, hizmetin çalıştığı sürece yaklaşık 40 dakikada bir gerçekleşen sonraki eşitlemeler yerine gerçekleştirilmesini daha uzun sürdüğüne unutmayın. İlerleme durumunu izlemek ve Salesforce uygulamanızdaki sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan etkinlik günlüklerinin sağlanması için bağlantıları izlemek üzere **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="common-issues"></a>Genel sorunlar
* Salesforce 'a erişimi yetkilendirmek için sorun yaşıyorsanız aşağıdakileri doğrulayın:
    * Kullanılan kimlik bilgileri Salesforce 'a yönetici erişimine sahiptir.
    * Kullanmakta olduğunuz Salesforce sürümü Web Erişimi destekler (örneğin, geliştirici, Enterprise, Sandbox ve Salesforce 'un sınırsız sürümleri).)
    * Web API erişimi kullanıcı için etkinleştirildi.
* Azure AD sağlama hizmeti, bir kullanıcı için dil, yerel ayar ve saat dilimini sağlamayı destekler. Bu öznitelikler varsayılan öznitelik eşlemeleridir ancak varsayılan bir kaynak özniteliği yoktur. Varsayılan kaynak özniteliğini seçtiğinizden ve kaynak özniteliğin SalesForce tarafından beklenen biçimde olduğundan emin olun. Örneğin, İngilizce (Birleşik Devletler) için Localesıdkey en_US. Doğru Localesıdkey biçimini öğrenmek için [burada](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) sunulan kılavuzu gözden geçirin. LanguageLocaleKey biçimleri [burada](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5)bulunabilir. Biçimin doğru olduğundan emin olmanın yanı sıra, [burada](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)açıklandığı gibi, dilin kullanıcılarınız için de etkinleştirildiğinden emin olmanız gerekebilir. 
* **Salesforcelicenselimitexcelıo:** Bu Kullanıcı için kullanılabilir lisans olmadığından, Kullanıcı hedef uygulamada oluşturulamadı. Hedef uygulama için ek lisanslar temin edebilir ya da doğru özniteliklerin doğru özniteliklerle atandığından emin olmak için Kullanıcı atamalarınızı ve öznitelik eşleme yapılandırmanızı gözden geçirin.
* **Salesforceduplicateusername:** Kullanıcı, başka bir Salesforce.com kiracısında çoğaltılan bir Salesforce.com ' username ' içerdiğinden sağlanamıyor.  Salesforce.com ' de, ' username ' özniteliğinin değerleri tüm Salesforce.com kiracılar genelinde benzersiz olmalıdır.  Varsayılan olarak, Azure Active Directory bir kullanıcının userPrincipalName 'i, Salesforce.com içinde ' username ' olur.   İki seçeneğiniz vardır.  Bir seçenek, başka bir kiracıyı de yönetiyorsanız, kullanıcıyı diğer Salesforce.com kiracısında yinelenen ' username ' ile bulup yeniden adlandırmaya yönelik bir seçenektir.  Diğer seçenek, Azure Active Directory kullanıcıdan, dizininizin tümleştirildiği Salesforce.com kiracısına erişimi kaldırdır. Sonraki eşitleme denemesinde bu işlemi yeniden deneyeceğiz. 
* **Salesforcerequiredfieldeksik:** Salesforce Kullanıcı oluşturmak veya güncelleştirmek için Kullanıcı üzerinde belirli özniteliklerin mevcut olmasını gerektirir. Bu Kullanıcı, gerekli özniteliklerin birini içermiyor. E-posta ve diğer ad gibi özniteliklerin Salesforce 'a sağlanmasını istediğiniz tüm kullanıcılara doldurulduğundan emin olun. [Öznitelik tabanlı kapsam filtrelerini](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)kullanarak bu özniteliklere sahip olmayan kullanıcıların kapsamını belirleyebilirsiniz. 
* Salesforce 'a sağlama için varsayılan öznitelik eşlemesi, Azure AD 'deki Approlet atamalarını Salesforce 'ta ProfileName 'e eşlemek için Singleapprotaatamalar ifadesini içerir. Öznitelik eşlemesi yalnızca bir rol sağlamayı desteklediğinden, kullanıcıların Azure AD 'de birden çok uygulama rolü ataması olmadığından emin olun. 


## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](tutorial-list.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
* [Çoklu oturum açmayı yapılandırma](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
