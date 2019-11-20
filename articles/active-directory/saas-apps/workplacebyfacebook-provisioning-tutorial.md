---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için çalışma alanını Facebook ile yapılandırma | Microsoft Docs'
description: Facebook tarafından Azure Active Directory ve çalışma alanı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ee091d1c8f0f477354f6bb422d041278ec5668e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73574259"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için çalışma alanını Facebook ile yapılandırma

Bu öğreticinin amacı, Facebook ve Azure AD çalışma alanında yapmanız gereken adımları, Azure AD 'den Facebook tarafından çalışma alanına otomatik olarak sağlamak ve devre dışı bırakmak için yapmanız gereken adımları gösterir.

## <a name="prerequisites"></a>Ön koşullar

Azure AD tümleştirmesini Facebook tarafından çalışma alanıyla yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

- Azure AD aboneliği
- Facebook tarafından çoklu oturum açma özellikli abonelikte çalışma alanı

> [!NOTE]
> Bu öğreticideki adımları test etmek için, üretim ortamının kullanılmasını önermiyoruz.

Bu öğreticideki adımları test etmek için aşağıdaki önerileri izlemeniz gerekir:

- Gerekli olmadığı takdirde üretim ortamınızı kullanmayın.
- Azure AD deneme ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık deneme sürümü edinebilirsiniz.

## <a name="assigning-users-to-workplace-by-facebook"></a>Facebook tarafından çalışma alanına kullanıcı atama

Azure Active Directory, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyebilmek için "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı hesabı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya "atanmış" olan kullanıcılar ve gruplar eşitlenir.

Sağlama hizmetini yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların çalışma alanınıza Facebook uygulaması tarafından erişmesi gereken kullanıcıları temsil ettiğini belirlemeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları Facebook uygulaması tarafından çalışma alanınıza atayabilirsiniz:

[Kurumsal uygulamaya Kullanıcı veya Grup atama](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Facebook tarafından çalışma alanına Kullanıcı atamaya yönelik önemli ipuçları

*   Sağlama yapılandırmasını test etmek için Facebook tarafından çalışma alanına tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

*   Facebook tarafından çalışma alanına bir Kullanıcı atarken geçerli bir kullanıcı rolü seçmeniz gerekir. "Varsayılan erişim" rolü sağlama için çalışmaz.

## <a name="enable-user-provisioning"></a>Kullanıcı sağlamayı etkinleştir

Bu bölüm, Azure AD 'nizi Facebook 'un Kullanıcı hesabı sağlama API 'SI ile çalışma alanına bağlama ve sağlama hizmetini Kullanıcı ve gruba göre Facebook tarafından çalışma alanında atanan kullanıcı hesaplarını oluşturmak, güncelleştirmek ve devre dışı bırakmak için size rehberlik eder Azure AD 'de atama.

>[!Tip]
>[Azure Portal](https://portal.azure.com)' de belirtilen talimatları izleyerek Facebook tarafından çalışma alanı için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma özelliği otomatik sağlanmadan bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Kullanıcı hesabı sağlamayı Azure AD 'de Facebook tarafından çalışma alanına yapılandırmak için:

Bu bölümün amacı, Facebook tarafından çalışma alanına Active Directory Kullanıcı hesaplarının sağlanması için nasıl olanak sağlamasının ana hatlarıyla anlatılmaktadır.

Azure AD, atanan kullanıcıların hesap ayrıntılarını Facebook ile çalışma alanına otomatik olarak eşitleyebilme olanağını destekler. Bu otomatik eşitleme, Facebook tarafından, kullanıcıların, ilk kez oturum açmaya çalışmadan önce kullanıcılara yetkilendirmek için ihtiyaç duyacağı verileri almasını sağlar. Ayrıca, Azure AD 'de erişim iptal edildiğinde, kullanıcıların Facebook tarafından çalışma alanından de sağlamasını yapar.

1. [Azure portal](https://portal.azure.com) **Azure Active Directory** > **Enterprise Apps** > **tüm uygulamalar** bölümüne gidin.

2. Zaten Facebook ile çoklu oturum açma için çalışma alanı yapılandırdıysanız, arama alanını kullanarak Facebook ile çalışma alanınızın örneğini arayın. Aksi halde, uygulama galerisinde **Facebook tarafından çalışma alanı** için **Ekle** ve ara ' yı seçin. Arama sonuçlarından Facebook tarafından çalışma alanı ' nı seçin ve uygulama listenize ekleyin.

3. Facebook tarafından Iş yeriniz örneğinizi seçin, sonra **sağlama** sekmesini seçin.

4. **Sağlama modunu** **Otomatik**olarak ayarlayın. 

    ![alınıyor](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. **Yönetici kimlik bilgileri** bölümünde, Facebook Yöneticisi tarafından çalışma alanınızın erişim belirtecini girin ve kiracı URL 'si değerini `https://www.facebook.com/scim/v1/` olarak ayarlayın. Çalışma alanı için erişim belirteci oluşturma hakkındaki [yönergelere](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) bakın. 

6. Azure portal Azure AD 'nin Facebook uygulaması tarafından çalışma alanınıza bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Facebook hesabına göre çalışma alanınızın Takım Yöneticisi izinlerine sahip olduğundan emin olun.

7. **Bildirim e-postası** alanında sağlama hatası bildirimleri alması gereken bir kişinin veya grubun e-posta adresini girin ve onay kutusunu işaretleyin.

8. Kaydet ' e tıklayın **.**

9. Eşlemeler bölümünde, **Facebook ile Azure Active Directory kullanıcıları çalışma alanına eşitler** ' ı seçin.

10. **Öznitelik eşlemeleri** bölümünde, Azure AD 'den Facebook tarafından çalışma alanına eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için Facebook tarafından çalışma alanındaki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için Kaydet düğmesini seçin.

11. Facebook tarafından çalışma alanı için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin

12. Kaydet ' e tıklayın **.**

Otomatik sağlamayı yapılandırma hakkında daha fazla bilgi için bkz. [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Artık bir test hesabı oluşturabilirsiniz. Hesabın Facebook tarafından çalışma alanına eşitlendiğinden emin olmak için 20 dakikaya kadar bekleyin.

> [!NOTE]
> Azure AD uygulamasının onaylandığından ve yeni yönergelerini karşıladığından emin olmak için Facebook ekibi tarafından çalışma alanı ile yakından çalışıyoruz. Facebook son tarihine göre çalışma alanı, 16 Aralık ve bunu karşılamaları beklenir. Bu anda müşterilerin hiçbir iş beklenmez. 28-Şubat-2020 tarihine kadar müşterilerin yeni tümleştirmeye geçiş yapması gerekir. Geçiş yolu kullanılabilir duruma geldiğinde buraya buraya göndereceğiz.    

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](tutorial-list.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
* [Çoklu oturum açmayı yapılandırma](workplacebyfacebook-tutorial.md)
