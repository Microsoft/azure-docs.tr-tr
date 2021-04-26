---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Salesforce korumalı alanı yapılandırma | Microsoft Docs'
description: Azure AD 'den Salesforce korumalı alana Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Salesforce korumalı alanı ve Azure AD 'de gerçekleştirmeniz gereken adımları öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 7e3f8e5e975468b468712ae8907cdca0e80a5f9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94352617"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlama için Salesforce korumalı alanını yapılandırma

Bu öğreticinin amacı, Azure AD 'den Salesforce korumalı alana Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Salesforce korumalı alanı ve Azure AD 'de gerçekleştirmeniz gereken adımları gösteriyoruz.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide anlatılan senaryoda aşağıdakilere sahip olduğunuz kabul edilmiştir:

*   Azure Active Directory kiracısı.
*   Iş için Salesforce korumalı alanı veya eğitim için Salesforce korumalı alanı için geçerli bir kiracı. Her iki hizmet için de ücretsiz bir deneme hesabı kullanabilirsiniz.
*   Ekip Yöneticisi izinleri ile Salesforce korumalı alanında bir kullanıcı hesabı.

## <a name="assigning-users-to-salesforce-sandbox"></a>Kullanıcıları Salesforce korumalı alana atama

Azure Active Directory, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyebilmek için "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı hesabı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya "atanmış" olan kullanıcılar ve gruplar eşitlenir.

Sağlama hizmetini yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların veya grupların Salesforce korumalı alan uygulamanıza erişmesi gerektiğine karar vermeniz gerekir. Bu kararı verdikten sonra, [bir kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md) bölümündeki yönergeleri izleyerek bu kullanıcıları Salesforce korumalı alan uygulamanıza atayabilirsiniz.

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Salesforce korumalı alana Kullanıcı atamaya yönelik önemli ipuçları

* Sağlama yapılandırmasını test etmek için tek bir Azure AD kullanıcısının Salesforce korumalı alana atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Salesforce korumalı alana atarken geçerli bir kullanıcı rolü seçmeniz gerekir. "Varsayılan erişim" rolü sağlama için çalışmaz.

> [!NOTE]
> Bu uygulama, sağlama sürecinin bir parçası olarak, müşterinin Kullanıcı atarken seçmek isteyebileceğiniz özel rolleri Salesforce korumalı alanından içeri aktarır.

## <a name="enable-automated-user-provisioning"></a>Otomatik Kullanıcı sağlamayı etkinleştir

Bu bölümde, Azure AD 'nizi Salesforce korumalı alanının kullanıcı hesabı sağlama API 'sine bağlama ve sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve grup atamasını temel alan Salesforce korumalı alanında atanan kullanıcı hesaplarını oluşturmak, güncelleştirmek ve devre dışı bırakmak için nasıl yapılandıracağınız konusunda kılavuzluk eder.

>[!Tip]
>Ayrıca, [Azure Portal](https://portal.azure.com)sağlanan yönergeleri Izleyerek Salesforce korumalı alanı için SAML tabanlı tek Sign-On de seçebilirsiniz. Çoklu oturum açma özelliği otomatik sağlanmadan bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="configure-automatic-user-account-provisioning"></a>Otomatik Kullanıcı hesabı sağlamayı yapılandırma

Bu bölümün amacı, Active Directory Kullanıcı hesaplarının Salesforce korumalı alana Kullanıcı tarafından sağlanması için nasıl olanak sağlamasının ana hatlarıyla anlatılmaktadır.

1. [Azure portal](https://portal.azure.com) **Azure Active Directory > Enterprise Apps > tüm uygulamalar** bölümüne gidin.

1. Çoklu oturum açma için zaten Salesforce korumalı alanı yapılandırdıysanız arama alanını kullanarak Salesforce korumalı alanı örneğinizi arayın. Aksi takdirde, uygulama galerisinde, **Ekle** ve **Salesforce korumalı alanını** ara ' yı seçin. Arama sonuçlarından Salesforce korumalı alanı ' nı seçin ve uygulama listenize ekleyin.

1. Salesforce korumalı alanı örneğinizi seçin, sonra **sağlama** sekmesini seçin.

1. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Ekran görüntüsü, sağlama modu otomatik ve ayarlayabileceğiniz diğer değerlere ayarlanmış olan Salesforce korumalı alanı sağlama sayfasını gösterir.](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. **Yönetici kimlik bilgileri** bölümünde aşağıdaki yapılandırma ayarlarını sağlayın:
   
    a. **Yönetici Kullanıcı adı** metin kutusuna Salesforce.com atanmış **Sistem Yöneticisi** profiline sahip bir Salesforce Sandbox hesap adı yazın.
   
    b. **Yönetici parolası** metin kutusuna bu hesabın parolasını yazın.

1. Salesforce korumalı alanı güvenlik belirtecinizi almak için yeni bir sekme açın ve aynı Salesforce korumalı alanı yönetici hesabında oturum açın. Sayfanın sağ üst köşesinde, ad ' a tıklayın ve ardından **Ayarlar**' a tıklayın.

     ![Ekran görüntüsü seçili Ayarlar bağlantısını gösterir.](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Otomatik Kullanıcı sağlamayı etkinleştir")

1. Sol gezinti bölmesinde, **Kişisel bilgilerim** ' a tıklayarak ilgili bölümü genişletin ve ardından **güvenlik belirtecimi Sıfırla**' ya tıklayın.
  
    ![Ekran görüntüsü kişisel bilgilerim için seçili olan güvenlik belirtecinden sıfırlamayı gösterir.](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Otomatik Kullanıcı sağlamayı etkinleştir")

1. **Güvenlik belirtecini Sıfırla** sayfasında **güvenlik belirtecini Sıfırla** düğmesine tıklayın.

    ![Ekran görüntüsünde, açıklayıcı metin ve güvenlik belirtecini sıfırlama seçeneği ile rest güvenlik belirteci sayfası gösterilir](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Otomatik Kullanıcı sağlamayı etkinleştir")

1. Bu yönetici hesabıyla ilişkili e-posta gelen kutusunu kontrol edin. Salesforce Sandbox.com 'tan yeni güvenlik belirtecini içeren bir e-posta arayın.

1. Belirteci kopyalayın, Azure AD pencerenize gidin ve **gizli belirteç** alanına yapıştırın.

1. Azure portal Azure AD 'nin Salesforce korumalı alan uygulamanıza bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın.

1. **Bildirim e-postası** alanına, sağlama hatası bildirimleri alması gereken kişinin veya grubun e-posta adresini girin ve onay kutusunu işaretleyin.

1. Kaydet ' e tıklayın **.**  
    
1.  Eşlemeler bölümünde **Azure Active Directory Kullanıcıları Salesforce korumalı alana eşitler** ' ı seçin.

1. **Öznitelik eşlemeleri** bölümünde, Azure AD 'Den Salesforce korumalı alana eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Salesforce korumalı alanı 'ndaki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için Kaydet düğmesini seçin.

1. Salesforce korumalı alanı için Azure AD sağlama hizmetini etkinleştirmek üzere ayarlar bölümünde **sağlama durumunu** **Açık** olarak değiştirin

1. Kaydet ' e tıklayın **.**

Kullanıcılar ve Gruplar bölümünde Salesforce korumalı alana atanan tüm Kullanıcı ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, hizmetin çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerlemeyi izlemek ve Salesforce korumalı alanı uygulamasında sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan etkinlik günlüklerini sağlamak için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](tutorial-list.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
* [Çoklu oturum açmayı yapılandırma](./salesforce-sandbox-tutorial.md)