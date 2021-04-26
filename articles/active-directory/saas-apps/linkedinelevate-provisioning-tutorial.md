---
title: 'Öğretici: LinkedIn yükseltme için Kullanıcı hazırlama-Azure AD'
description: Kullanıcı hesaplarını LinkedIn 'e otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: 5e972475530ad36a188f73990bb9eca35748c36c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358958"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için LinkedIn yükseltmeyi yapılandırma

Bu öğreticinin amacı, Azure AD 'den LinkedIn 'e yönelik kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için LinkedIn ve Azure AD 'de gerçekleştirmeniz gereken adımları gösteriyoruz.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide anlatılan senaryoda aşağıdakilere sahip olduğunuz kabul edilmiştir:

* Azure Active Directory kiracısı
* LinkedIn 'i yükselt
* LinkedIn 'de LinkedIn hesap merkezine erişimi olan bir yönetici hesabı

> [!NOTE]
> Azure Active Directory, [SCIM](http://www.simplecloud.info/) protokolünü kullanarak LinkedIn yükseltmesine tümleştirilir.

## <a name="assigning-users-to-linkedin-elevate"></a>Kullanıcıları LinkedIn 'e atama

Azure Active Directory, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyebilmek için "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı hesabı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya "atanmış" olan kullanıcılar ve gruplar eşitlenir.

Sağlama hizmetini yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcı ve/veya grupların LinkedIn 'e erişmesi gereken kullanıcıları temsil ettiğini belirlemeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları LinkedIn 'e atayabilirsiniz:

[Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>LinkedIn 'e Kullanıcı atamaya yönelik önemli ipuçları

* Sağlama yapılandırmasını test etmek üzere LinkedIn 'e tek bir Azure AD kullanıcısı atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı LinkedIn 'e atarken atama iletişim kutusunda **Kullanıcı** rolünü seçmeniz gerekir. "Varsayılan erişim" rolü sağlama için çalışmaz.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Kullanıcı sağlamayı LinkedIn 'e yapılandırma

Bu bölümde Azure AD 'nizi LinkedIn 'in SCıM Kullanıcı hesabı sağlama API 'sine bağlama ve sağlama hizmeti 'nin, Azure AD 'de Kullanıcı ve grup atamasına göre LinkedIn 'de atanan kullanıcı hesaplarını oluşturmak, güncelleştirmek ve devre dışı bırakmak için nasıl yapılandırılacağı hakkında rehberlik sağlanır.

**İpucu:** Ayrıca, [Azure Portal](https://portal.azure.com)sağlanan yönergeleri izleyerek LINKEDıN için SAML tabanlı tek Sign-On yükseltmeyi de seçebilirsiniz. Çoklu oturum açma özelliği otomatik sağlanmadan bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini tamamlayabilse de.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Azure AD 'de otomatik Kullanıcı hesabı sağlamayı LinkedIn 'e yükseltmek için:

İlk adım, LinkedIn erişim belirtecinizi almak için kullanılır. Kuruluş yöneticisiyseniz, kendi kendine erişim belirteci sağlayabilirsiniz. Hesap merkezinizde **Ayarlar &gt; Genel ayarlar** ' a gidin ve **SCIM kurulum** panelini açın.

> [!NOTE]
> Hesap merkezine bir bağlantı yerine doğrudan erişiyorsanız, aşağıdaki adımları kullanarak buna ulaşabilirsiniz.

1. Hesap Merkezi 'nde oturum açın.

2. **Yönetici &gt; Yöneticisi ayarları** ' nı seçin.

3. Sol kenar çubuğunda **Gelişmiş tümleştirmeler** ' e tıklayın. Hesap merkezine yönlendirilirsiniz.

4. **+ Yenı SCIM Yapılandırması Ekle** ' ye tıklayın ve her alanı doldurarak yordamı izleyin.

    > [!NOTE]
    > Yeniden atama lisansları etkin olmadığında, yalnızca Kullanıcı verilerinin eşitlendiği anlamına gelir.

    ![Ekran görüntüsü LinkedIn hesap merkezi genel ayarlarını gösterir.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > Oto lisans ataması etkinleştirildiğinde, uygulama örneği ve lisans türünü not etmeniz gerekir. Lisanslar ilk olarak atanır, ilk olarak tüm lisanslar alınana kadar temel alınır.

    ![Ekran görüntüsü S C I M kurulum sayfasını gösterir.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. **Belirteç oluştur**' a tıklayın. Erişim belirteci, erişim **belirteci** alanının altında görüntülenir.

6. Sayfadan çıkmadan önce erişim belirtecinizi panonuza veya bilgisayarınıza kaydedin.

7. Ardından [Azure Portal](https://portal.azure.com)oturum açın ve **Azure Active Directory > Enterprise Apps > tüm uygulamalar**  bölümüne gidin.

8. Çoklu oturum açma için LinkedIn yükseltmeyi zaten yapılandırdıysanız arama alanını kullanarak LinkedIn 'in örneğini arayın. Aksi takdirde, uygulama galerisinde **LinkedIn** 'e **Ekle** ve ara ' yı seçin. Arama sonuçlarından LinkedIn Yükselt ' i seçin ve uygulama listenize ekleyin.

9. LinkedIn yükseltme örneğinizi seçin, sonra **sağlama** sekmesini seçin.

10. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Ekran görüntüsünde LinkedIn hazırlama sağlama sayfası gösterilmektedir.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. **Yönetici kimlik bilgileri** altında aşağıdaki alanları girin:

    * **Kiracı URL 'si** alanına, girin `https://api.linkedin.com` .

    * **Gizli belirteç** alanına, adım 1 ' de oluşturduğunuz erişim belirtecini girin ve **Bağlantıyı Sına** ' yı tıklatın.

    * Portalınızın sağ tarafında bir başarı bildirimi görmeniz gerekir.

12. **Bildirim e-postası** alanında sağlama hatası bildirimleri alması gereken bir kişinin veya grubun e-posta adresini girin ve aşağıdaki onay kutusunu işaretleyin.

13. **Kaydet**’e tıklayın.

14. **Öznitelik eşlemeleri** bölümünde, Azure AD 'Den LinkedIn yükseltmesine eşitlenecek Kullanıcı ve grup özniteliklerini gözden geçirin. Güncelleştirme işlemleri için, **eşleşen** özellikler olarak seçilen özniteliklerin LinkedIn yükseltme içindeki kullanıcı hesaplarını ve grupları eşleştirmek için kullanılacağını unutmayın. Değişiklikleri uygulamak için Kaydet düğmesini seçin.

    ![Ekran görüntüsünde öznitelik eşlemeleri dahil olmak üzere eşlemeler gösterilmektedir.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. LinkedIn 'in Azure AD sağlama hizmetini etkinleştirmek için **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin

16. **Kaydet**’e tıklayın.

Bu, kullanıcılar ve Gruplar bölümünde LinkedIn 'e atanan tüm Kullanıcı ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, hizmetin çalıştığı sürece yaklaşık 40 dakikada bir gerçekleşen sonraki eşitlemeler yerine daha uzun süreceğini unutmayın. Eşitleme ayrıntıları bölümünü izlemek ve LinkedIn uygulamanızın sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan etkinlik günlüklerinin sağlanması için bağlantıları izlemek üzere **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek Kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
