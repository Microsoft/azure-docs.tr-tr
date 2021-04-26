---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için dinamik sinyal yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını dinamik Sinyalte otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 263a67fd8fba2c336d1ed4d91475386a8ae175dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005801"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için dinamik sinyal yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları dinamik sinyalde otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için dinamik sinyal ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Dinamik bir sinyal kiracısı](https://dynamicsignal.com/)
* Yönetici izinleriyle dinamik Sinyalteki bir kullanıcı hesabı.

## <a name="add-dynamic-signal-from-the-gallery"></a>Galeriden dinamik sinyal Ekle

Azure AD ile otomatik Kullanıcı sağlama için dinamik sinyal yapılandırmadan önce, Azure AD uygulama galerisinden yönetilen SaaS uygulamaları listenize dinamik sinyal eklemeniz gerekir.

**Azure AD uygulama galerisinden dinamik sinyal eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **dinamik sinyal** girin, sonuçlar panelinde **dinamik sinyal** ' ı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde dinamik sinyal](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Kullanıcıları dinamik sinyale atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların dinamik sinyaline erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları dinamik sinyalde atayabilirsiniz:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Dinamik sinyale Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için dinamik sinyalle tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı dinamik sinyale atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Dinamik Sinyalte otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alarak dinamik sinyalde kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD sağlama hizmetini yapılandırma adımlarında size kılavuzluk eder.

> [!TIP]
> Dinamik sinyal [Çoklu oturum açma öğreticisinde](dynamicsignal-tutorial.md)sunulan yönergeleri Izleyerek dinamik SINYAL için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Azure AD 'de dinamik sinyal için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **dinamik sinyal**' ı seçin.

    ![Uygulamalar listesindeki dinamik sinyal bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, adım 6 ' da açıklandığı gibi, dinamik sinyaliniz hesabının **kiracı URL 'Sini** ve **gizli belirtecini** girin.

6. Dinamik sinyal Yönetici Konsolu 'nda **yönetim > gelişmiş > API**'sine gidin.

    :::image type="content" source="./media/dynamic-signal-provisioning-tutorial/secret-token-1.png" alt-text="Dinamik sinyal yönetici konsolunun ekran görüntüsü. Gelişmiş, Yönetici menüsünde vurgulanır. Gelişmiş Menü, vurguladığım bir P ile de görünür." border="false":::

    **SCIM API URL 'Sini** **kiracı URL**'sine kopyalayın. Bir **taşıyıcı belirteci** oluşturmak Için **Yeni belirteç oluştur** ' a tıklayın ve değeri **gizli belirtece** kopyalayın.

    :::image type="content" source="./media/dynamic-signal-provisioning-tutorial/secret-token-2.png" alt-text="Belirteçler sayfasının ekran görüntüsü, S C M A P I U R i m, yeni belirteç oluştur ve daha sonra taşıyıcı belirteci kutusunda yer tutucu." border="false":::

7. 5. adımda gösterilen alanları doldurarak Azure AD 'nin dinamik **sinyalle** bağlantı kuramadığından emin olmak Için Bağlantıyı Sına ' ya tıklayın. Bağlantı başarısız olursa, dinamik sinyal hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

8. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

9. **Kaydet**’e tıklayın.

10. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları dinamik sinyal ile eşitler**' ı seçin.

    ![Dinamik sinyal Kullanıcı eşlemeleri](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. **Öznitelik eşleme** bölümünde Azure AD 'Den dinamik sinyale eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için dinamik sinyaldeki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Dinamik sinyal Kullanıcı öznitelikleri](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. Azure AD sağlama hizmetini dinamik sinyal için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek dinamik sinyalle sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek ve Azure AD sağlama hizmeti tarafından dinamik sinyal üzerinde gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinliği raporunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Dinamik sinyal, Azure AD 'den kalıcı Kullanıcı silmeleri desteklemez. Dinamik sinyalde bir kullanıcıyı kalıcı olarak silmek için, işlemin dinamik sinyal Yönetici Konsolu kullanıcı arabiriminden yapılması gerekir. 
* Dinamik sinyal şu anda grupları desteklemiyor.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)

