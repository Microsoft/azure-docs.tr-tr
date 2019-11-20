---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için ServiceNow 'ı yapılandırın | Microsoft Docs"
description: Azure AD 'den ServiceNow 'a Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85783339c7d1348f598f924f14d9b40cd0c8cd22
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967175"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için ServiceNow 'ı yapılandırma

Bu öğreticinin amacı, Azure AD 'den ServiceNow 'a Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için ServiceNow ve Azure AD 'de gerçekleştirmeniz gereken adımları gösteriyoruz.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini ServiceNow ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

- Azure AD aboneliği
- ServiceNow için, ServiceNow, Calgary sürümü veya üzeri bir örnek veya kiracı
- ServiceNow Express için ServiceNow Express, Helsinki sürümü veya üzeri bir örnek

> [!NOTE]
> Bu öğreticideki adımları test etmek için üretim ortamı kullanarak önermiyoruz.

Bu öğreticideki adımları test etmek için bu önerileri izlemelidir:

- Gerekli olmadıkça, üretim ortamında kullanmayın.
- Azure AD deneme ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

## <a name="assigning-users-to-servicenow"></a>Kullanıcıları ServiceNow 'a atama

Azure Active Directory, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyebilmek için "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı hesabı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya "atanmış" olan kullanıcılar ve gruplar eşitlenir.

Sağlama hizmetini yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların ServiceNow uygulamanıza erişmesi gereken kullanıcıları temsil ettiğini belirlemeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ServiceNow uygulamanıza atayabilirsiniz: [Kurumsal uygulamaya Kullanıcı veya Grup atama](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Sağlama yapılandırmasını test etmek için ServiceNow 'a tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.
>*   Bir kullanıcıyı ServiceNow 'a atarken geçerli bir kullanıcı rolü seçmeniz gerekir. "Varsayılan erişim" rolü sağlama için çalışmaz.
>*   Azure AD 'de rol oluşturma ve yapılandırma hakkında daha fazla bilgi için lütfen bu [bağlantıya](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) başvurun

## <a name="enable-automated-user-provisioning"></a>Otomatik Kullanıcı sağlamayı etkinleştir

Bu bölümde, Azure AD 'nizi ServiceNow 'ın Kullanıcı hesabı sağlama API 'sine bağlama ve sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve grup atamasını temel alan ServiceNow 'da atanan kullanıcı hesaplarını oluşturmak, güncelleştirmek ve devre dışı bırakmak için nasıl yapılandıracağınız konusunda kılavuzluk eder.

> [!TIP]
>Ayrıca, [Azure Portal](https://portal.azure.com)' de sağlanan yönergeleri Izleyerek ServiceNow için SAML tabanlı çoklu oturum açmayı da seçebilirsiniz. Çoklu oturum açma özelliği otomatik sağlanmadan bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="configure-automatic-user-account-provisioning"></a>Otomatik Kullanıcı hesabı sağlamayı yapılandırma

1. [Azure portal](https://portal.azure.com) **Azure Active Directory > Enterprise Apps > tüm uygulamalar** bölümüne gidin.

1. Çoklu oturum açma için ServiceNow 'ı zaten yapılandırdıysanız arama alanını kullanarak ServiceNow örneğinizi arayın. Aksi takdirde, **Ekle** ' yi seçin ve uygulama galerisinde **ServiceNow** için arama yapın. Arama sonuçlarından ServiceNow ' ı seçin ve uygulama listenize ekleyin.

1. ServiceNow örneğinizi seçin, sonra **sağlama** sekmesini seçin.

1. **Sağlama** modunu **Otomatik**olarak ayarlayın. 

    ![sağlama](./media/servicenow-provisioning-tutorial/provisioning.png)

1. Yönetici kimlik bilgileri bölümünde aşağıdaki adımları uygulayın:
   
    a. **ServiceNow örnek adı** metin kutusuna ServiceNow örnek adını yazın.

    b. **ServiceNow Yönetici Kullanıcı adı** metin kutusuna bir yöneticinin kullanıcı adını yazın.

    c. **ServiceNow yönetici parolası** metin kutusunda, yöneticinin parolası.

1. Azure portal, Azure AD 'nin ServiceNow uygulamanıza bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, ServiceNow hesabınızda Takım Yöneticisi izinlerine sahip olduğundan emin olun ve **"yönetici kimlik bilgileri"** adımını yeniden deneyin.

1. **Bildirim e-postası** alanında sağlama hatası bildirimleri alması gereken bir kişinin veya grubun e-posta adresini girin ve onay kutusunu işaretleyin.

1. Kaydet ' e tıklayın **.**

1. Eşlemeler bölümünde **Azure Active Directory Kullanıcıları ServiceNow** ' ı seçin.

1. **Öznitelik eşlemeleri** bölümünde, Azure AD 'Den ServiceNow 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için ServiceNow içindeki kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri kaydetmek için Kaydet düğmesini seçin.

1. ServiceNow için Azure AD sağlama hizmetini etkinleştirmek üzere ayarlar bölümünde **sağlama durumunu** **Açık** olarak değiştirin

1. Kaydet ' e tıklayın **.**

Kullanıcılar ve Gruplar bölümünde ServiceNow 'a atanan tüm Kullanıcı ve/veya grupların ilk eşitlemesini başlatır. İlk eşitleme hizmeti çalışıyor sürece yaklaşık 40 dakikada oluşan sonraki eşitlemeler uzun sürer. İlerlemeyi izlemek ve ServiceNow uygulamanızda sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan etkinlik günlüklerini sağlamak için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](tutorial-list.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
* [Çoklu oturum açmayı yapılandırma](servicenow-tutorial.md)


