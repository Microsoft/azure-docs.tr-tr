---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için meta Networks bağlayıcısını yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını meta ağlara otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6e34e540-7761-4bdf-a2f9-8c12becc119d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 40927597031205b5fb1bcc5869922bcc4f3f265c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518739"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için meta Networks bağlayıcısını yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları meta ağlara otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için meta ağlar Bağlayıcısı ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Bir meta Networks bağlayıcı kiracısı](https://www.metanetworks.com/)
* Yönetim izinlerine sahip meta Networks bağlayıcısında bir kullanıcı hesabı.

## <a name="assigning-users-to-meta-networks-connector"></a>Meta ağları bağlayıcıya Kullanıcı atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların meta ağlar bağlayıcısına erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları meta ağları bağlayıcısına atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Meta ağları bağlayıcıya Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için meta ağları bağlayıcıya tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Meta ağlar bağlayıcısına bir Kullanıcı atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Sağlama için meta ağlar bağlayıcısını ayarla

1. Kuruluşunuzun adını kullanarak [meta Networks Bağlayıcısı yönetici konsolunda](https://login.metanetworks.com/login/) oturum açın. **Yönetim > API anahtarlarına**gidin.

    ![Meta Networks bağlayıcı Yönetici Konsolu](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Ekranın sağ üst tarafındaki artı işaretine tıklayarak yeni bir **API anahtarı**oluşturun.

    ![Meta ağlar Bağlayıcısı artı simgesi](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  **API anahtar adı** ve **API anahtarı açıklamasını**ayarlayın.

    ![Meta Networks Bağlayıcısı belirteç oluştur](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  **Gruplar** ve **Kullanıcılar**için **yazma** ayrıcalıklarını açın.

    ![Meta Networks bağlayıcı ayrıcalıkları](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  **Ekle**'ye tıklayın. **Gizli anahtarı** kopyalayın ve tek bir zaman görüntüleyebilmeniz için kaydedin. Bu değer, Azure portal meta Networks bağlayıcı uygulamanızın sağlama sekmesindeki gizli belirteç alanına girilir.

    ![Meta Networks Bağlayıcısı belirteç oluştur](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  **Yönetim > ayarları > ıdp > yeni oluştur**' a giderek IDP ekleyin.

    ![Meta Networks Bağlayıcısı IDP ekleme](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  **IDP yapılandırma** sayfasında IDP **yapılandırmanızı belirtebilir ve** bir **simge**seçebilirsiniz.

    ![Meta Networks bağlayıcı IDP adı](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Meta Networks Bağlayıcısı IDP simgesi](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  **SCıM Yapılandır** altında, önceki ADıMLARDA oluşturulan API anahtarı adını seçin. **Kaydet**'e tıklayın.

    ![Meta Networks Bağlayıcısı SCıM 'yi yapılandırma](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  **Yönetim > ayarları > IDP sekmesine**gidin. **IDP kimliğini**görüntülemek için önceki adımlarda oluşturulan IDP yapılandırmasının adına tıklayın. Bu **kimlik** , Azure Portal meta Networks bağlayıcı uygulamanızın sağlama sekmesinde **kiracı URL 'si** alanına değer girerken **kiracı URL 'sinin** sonuna eklenir.

    ![Meta Networks bağlayıcı IDP KIMLIĞI](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Galeriden meta ağlar Bağlayıcısı ekleme

Azure AD ile otomatik Kullanıcı sağlaması için meta Networks bağlayıcısını yapılandırmadan önce Azure AD uygulama galerisindeki meta ağlar bağlayıcısını yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD uygulama galerisinden meta Networks Bağlayıcısı eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **meta ağlar Bağlayıcısı**' nı girin, sonuçlar panelinde **meta ağlar Bağlayıcısı** ' nı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesindeki meta ağlar Bağlayıcısı](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Meta ağlara otomatik Kullanıcı sağlamayı yapılandırma Bağlayıcısı 

Bu bölümde Azure AD sağlama hizmeti 'ni kullanarak Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan meta ağlar Bağlayıcısı 'nda kullanıcıları ve/veya grupları oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

> [!TIP]
> Meta [Networks Bağlayıcısı çoklu oturum açma öğreticisinde](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial)belirtilen yönergeleri Izleyerek meta ağlar BAĞLAYıCıSı için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilse de

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Azure AD 'de meta ağlar Bağlayıcısı için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com)’ında oturum açın. **Kuruluş uygulamaları**' nı seçin ve ardından **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **meta ağlar Bağlayıcısı**' nı seçin.

    ![Uygulamalar listesindeki meta ağları bağlayıcı bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama sekmesi](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, **kiracı URL 'sindeki**`https://api.metanetworks.com/v1/scim/<IdP ID>` girin. **Gizli belirteçte**daha önce alınan **SCIM kimlik doğrulama belirteci** değerini girin. Azure AD 'nin meta Networks bağlayıcısına bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, meta Networks bağlayıcı hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

7. **Kaydet** düğmesine tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları meta ağlar Bağlayıcısı ile eşitler**' ı seçin.

    ![Meta Networks Bağlayıcısı Kullanıcı eşlemeleri](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. **Öznitelik eşleme** bölümünde Azure AD 'Den meta Networks bağlayıcısına eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için meta ağlar bağlayıcısında Kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Meta Networks Bağlayıcısı Kullanıcı öznitelikleri](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. **Eşlemeler** bölümünde, **Azure Active Directory gruplarını meta Networks Bağlayıcısı olarak eşitler**' ı seçin.

    ![Meta Networks bağlayıcı grubu eşlemeleri](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. **Öznitelik eşleme** bölümünde Azure AD 'Den meta Networks bağlayıcısına eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için meta ağlar bağlayıcısında grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Meta Networks bağlayıcı grubu öznitelikleri](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

13. Meta ağlar Bağlayıcısı için Azure AD sağlama hizmeti 'ni etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek meta ağlar bağlayıcısına sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

15. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. **Eşitleme ayrıntıları** bölümünü Izleyip, meta ağlar bağlayıcısında Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, ilerlemeyi izleyebilir ve sağlama etkinliği raporunu kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)

