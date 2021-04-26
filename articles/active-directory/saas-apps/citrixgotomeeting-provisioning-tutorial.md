---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Sayfaymeeting yapılandırma | Microsoft Docs'
description: Azure Active Directory ve Sayfaytoplantısı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: ff377b0f93968eb6743187e4e659f4e888e5010e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358908"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Sayfaymeeting yapılandırma

Bu öğreticinin amacı, Azure AD 'den Sayfaymeeting 'e Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için, Azure AD 'de yapmanız gereken adımları size gösterir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide anlatılan senaryoda aşağıdakilere sahip olduğunuz kabul edilmiştir:

*   Azure Active Directory kiracısı.
*   Bir Sayfaymeeting çoklu oturum açma etkin aboneliği.
*   Ekip Yöneticisi izinleri ile Sayfaytoplantısında bir kullanıcı hesabı.

## <a name="assigning-users-to-gotomeeting"></a>Kullanıcıları Sayfayan toplantısına atama

Azure Active Directory, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyebilmek için "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı hesabı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya "atanmış" olan kullanıcılar ve gruplar eşitlenir.

Sağlama hizmetini yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcı ve/veya grupların, Sayfaymeeting uygulamanıza erişmesi gereken kullanıcıları temsil ettiğini belirlemeniz gerekir. Kararlandıktan sonra buradaki yönergeleri izleyerek bu kullanıcıları Sayfaytoplantısı uygulamanıza atayabilirsiniz:

[Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Kullanıcıları Sayfayan toplantısına atamaya yönelik önemli ipuçları

*   Sağlama yapılandırmasını test etmek için, tek bir Azure AD kullanıcısının, Sayfaymeeting 'e atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

*   Bir kullanıcıyı, Sayfaymeeting 'e atarken geçerli bir kullanıcı rolü seçmeniz gerekir. "Varsayılan erişim" rolü sağlama için çalışmaz.

## <a name="enable-automated-user-provisioning"></a>Otomatik Kullanıcı sağlamayı etkinleştir

Bu bölümde, Azure AD 'nizi Sayfaymeeting 'in Kullanıcı hesabı sağlama API 'sine bağlama ve sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve grup atamasını temel alan Sayfaymeeting 'de atanan kullanıcı hesaplarını oluşturmak, güncelleştirmek ve devre dışı bırakmak için yapılandırma işlemi kılavuzluk eder.

> [!TIP]
> Ayrıca, [Azure Portal](https://portal.azure.com)sağlanan yönergeleri Izleyerek, SAYFAYMEETING için SAML tabanlı tek Sign-On de seçebilirsiniz. Çoklu oturum açma özelliği otomatik sağlanmadan bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-account-provisioning"></a>Otomatik Kullanıcı hesabı sağlamayı yapılandırmak için:

1. [Azure portal](https://portal.azure.com) **Azure Active Directory > Enterprise Apps > tüm uygulamalar** bölümüne gidin.

1. Çoklu oturum açma için zaten Sayfaymeeting yapılandırdıysanız, arama alanını kullanarak Sayfaytoplantısı örneğinizi arayın. Aksi takdirde uygulama galerisinde, **Ekle** ' yi seçin ve **sayfaytoplantısı** ara ' yı seçin. Arama sonuçlarından Sayfaymeeting ' i seçin ve uygulama listenize ekleyin.

1. Sayfaytoplantısı örneğinizi seçin, sonra **sağlama** sekmesini seçin.

1. **Sağlama** modunu **Otomatik** olarak ayarlayın. 

    ![Azure portal ' de Sayfaymeeting için sağlama sekmesinin ekran görüntüsü. Sağlama modu otomatik ve yönetici kullanıcı adına ayarlanır, parola ve test bağlantısı vurgulanır.](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. Yönetici kimlik bilgileri bölümünde aşağıdaki adımları uygulayın:
   
    a. **Sayfaymeeting Yönetici Kullanıcı adı** metin kutusuna bir yöneticinin kullanıcı adını yazın.

    b. **Sayfaymeeting yönetici parolası** metin kutusunda, yöneticinin parolası.

1. Azure portal, Azure AD 'nin Sayfaymeeting uygulamanıza bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Sayfaytoplantısı hesabınızın ekip Yöneticisi izinlerine sahip olduğundan emin olun ve **"yönetici kimlik bilgileri"** adımını yeniden deneyin.

1. **Bildirim e-postası** alanında sağlama hatası bildirimleri alması gereken bir kişinin veya grubun e-posta adresini girin ve onay kutusunu işaretleyin.

1. Kaydet ' e tıklayın **.**

1. Eşlemeler bölümünde **Azure Active Directory Kullanıcıları sayfaydığı şekilde eşitler** ' ı seçin.

1. **Öznitelik eşlemeleri** bölümünde, Azure AD 'Den sayfaymeeting 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, bu güncelleştirme Işlemleri Için sayfaymeeting içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için Kaydet düğmesini seçin.

1. Sayfaymeeting için Azure AD sağlama hizmetini etkinleştirmek üzere ayarlar bölümünde **sağlama durumunu** **Açık** olarak değiştirin

1. Kaydet ' e tıklayın **.**

Kullanıcılar ve Gruplar bölümünde, Sayfaymeeting 'e atanan tüm Kullanıcı ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, hizmetin çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. Kaldırma işlemini izlemek için **eşitleme ayrıntıları** bölümünü kullanabilir ve bu işlem Için, sayfaytoplantısı uygulamanızdaki sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan etkinlik günlüklerinin sağlanması için bağlantıları izleyebilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](tutorial-list.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
* [Çoklu oturum açmayı yapılandırma](./citrix-gotomeeting-tutorial.md)