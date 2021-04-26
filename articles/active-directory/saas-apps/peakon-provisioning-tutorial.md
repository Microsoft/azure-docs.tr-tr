---
title: 'Öğretici: Azure Active Directory Peakon otomatik Kullanıcı sağlamasını yapılandırma | Microsoft Docs'
description: Peakon 'ye Kullanıcı hesaplarını otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: c04bbd5459690262b484582e807569b965a0439b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349897"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için Peakon yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları Peakon 'e otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Peakon ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
>  Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten bulunduğunu varsayar

* Azure AD kiracısı.
* [Bir Peakon kiracısı](https://peakon.com/us/pricing/).
* Yönetici izinlerine sahip Peakon içinde bir kullanıcı hesabı.

## <a name="assigning-users-to-peakon"></a>Peakon 'e Kullanıcı atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Peakon 'e erişmesi gerektiğini belirlemeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Peakon 'e atayabilirsiniz:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Peakon 'e Kullanıcı atamaya yönelik önemli ipuçları 

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için Peakon 'e tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Peakon 'e atarken atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-peakon-for-provisioning"></a>Sağlama için Peakon ayarlama

1.  [Peakon Yönetici konsolunuza](https://app.Peakon.com/login)oturum açın. **Yapılandırma**' ya tıklayın. 

    ![Peakon Yönetici Konsolu](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  **Tümleştirmeler**' i seçin.
    
    ![Tümleştirme seçeneği olarak adlandırılan yapılandırma seçeneklerinin ekran görüntüsü.](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  **Çalışan sağlamasını** etkinleştirin.

    ![Etkinleştir seçeneği verilen çalışan sağlama bölümünün ekran görüntüsü.](media/Peakon-provisioning-tutorial/peakon05.png)

4.  **SCIM 2,0 URL 'si** ve **OAuth taşıyıcı belirtecinin** değerlerini kopyalayın. Bu değerler, Azure portal Peakon uygulamanızın sağlama sekmesinde bulunan **kiracı URL 'si** ve **gizli belirteç** alanına girilecektir.

    ![Peakon belirteci oluştur](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Galeriden Peakon ekleme

Peakon 'i Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmak üzere, Azure AD Uygulama Galerisi 'nden yönetilen SaaS uygulamaları listenize Peakon eklemeniz gerekir.

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Peakon** yazın, sonuçlar panelinde **Peakon** ' yi seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuç listesinde Peakon](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Peakon 'e otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak Peakon içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan bir adım adım yol gösterir.

> [!TIP]
> Ayrıca, [Peakon çoklu oturum açma öğreticisinde](peakon-tutorial.md)sunulan yönergeleri Izleyerek, PEAKON için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Azure AD 'de Peakon için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Peakon**' yi seçin.

    ![Uygulamalar listesindeki Peakon bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, sırasıyla **kiracı URL 'Si** ve **gizli belirteç** ' de bulunan **SCIM 2,0 URL 'sini** ve **OAuth taşıyıcı belirteci** değerlerini girin. Azure AD 'nin Peakon 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Peakon hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

7. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

8. **Kaydet**’e tıklayın.

9. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Peakon olarak eşitler**' ı seçin.

    ![Peakon Kullanıcı eşlemeleri](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. **Öznitelik eşleme** bölümünde Azure AD 'den Peakon 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için Peakon içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Peakon Kullanıcı öznitelikleri](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Kapsam filtrelerini yapılandırmak için,       [kapsam filtresi öğreticisinde](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.
    
    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. Peakon üzerinde Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, ilerlemeyi izlemek ve sağlama etkinliği raporunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Peakon ' deki tüm özel kullanıcı özniteliklerinin, Peakon 'ın özel SCıM Kullanıcı uzantısından genişletilmesi gerekir `urn:ietf:params:scim:schemas:extension:peakon:2.0:User` .

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)