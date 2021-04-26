---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için tuş takımını yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırma hakkında bilgi edinin.
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
ms.openlocfilehash: b88e618da3f8a23c0517aaeb251e54bf559fc468
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014525"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için tuş takımını yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, Kullanıcı ve/veya grupları Dialpad 'e otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için, Dialpad ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
>  Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).

> Bu bağlayıcı Şu anda önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* [Bir Dialpad kiracısı](https://www.dialpad.com/pricing/).
* Yönetici izinlerine sahip tuş takımındaki bir kullanıcı hesabı.

## <a name="assign-users-to-dialpad"></a>Çevir paneline Kullanıcı atama
Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen atama adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Dialpad 'e erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Dialpad 'e atayabilirsiniz:
 
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Kullanıcıların Dialpad 'e atanması için önemli ipuçları

 * Otomatik Kullanıcı sağlama yapılandırmasını test etmek için, tek bir Azure AD kullanıcısının, Dialpad 'e atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı bir Dialpad 'e atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. Varsayılan erişim rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-dialpad-for-provisioning"></a>Sağlama için tuş takımını ayarla

Azure AD ile otomatik Kullanıcı sağlama için tuş takımını yapılandırmadan önce, bazı sağlama bilgilerini Dialpad 'ten almanız gerekir.

1. [Dialpad Yönetici konsolunuza](https://dialpadbeta.com/login) oturum açın ve **yönetici ayarları**' nı seçin. **Şirketimin** açılan listeden seçildiğinden emin olun. **Kimlik doğrulama > API anahtarları**' na gidin.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad01.png" alt-text="Ayarlar simgesiyle, şirketim, kimlik doğrulamasından ve bir P ı tuşlarından oluşan ve şirketimizin seçiliyken, Dialpad yönetici konsolunun ekran görüntüsü." border="false":::

2. **Anahtar Ekle** ' ye tıklayıp gizli belirtecinizin özelliklerini yapılandırarak yeni bir anahtar oluşturun.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad02.png" alt-text="Dialpad yönetim konsolundaki bir P ı anahtarları sayfasının ekran görüntüsü. Anahtar ekleme vurgulanacaktır." border="false":::

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad03.png" alt-text="Dialpad yönetim konsolundaki P. anahtarı Düzenle sayfasının ekran görüntüsü. Kaydet düğmesi vurgulanır." border="false":::

3. Son oluşturduğunuz API anahtarınız için **değer göster** düğmesine tıklayın ve gösterilen değeri kopyalayın. Bu değer, Azure portal Dialpad uygulamanızın sağlama sekmesindeki **gizli belirteç** alanına girilir. 

    ![Tuş takımı belirteç oluştur](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Galeriden Dialpanel ekleme

Azure AD ile otomatik Kullanıcı sağlamak üzere Dialpad 'i yapılandırmak için Azure AD uygulama galerisindeki Aramapaneli yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD uygulama galerisinden Dialpad eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, **dialpad** yazın, sonuçlar panelinde **dialpad** ' i seçin.
    ![Sonuçlar listesinde tuş takımı](common/search-new-app.png)

5. Aşağıda vurgulanan **URL** 'yi ayrı bir tarayıcıda gezin. 

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad05.png" alt-text="Dialpad uygulaması hakkında bilgi görüntüleyen bir sayfanın ekran görüntüsü. U R L 'nin altında bir adres listelenir ve vurgulanacaktır." border="false":::

6. Sağ üst köşede oturum aç ' ı seçin **> çevrimiçi tuş takımını kullanın**.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad06.png" alt-text="Dialpad Web sitesinin ekran görüntüsü. Oturum açma vurgulanır ve oturum aç sekmesi açıktır. Çevrimiçi tuş takımını kullan seçeneği de vurgulanmıştır." border="false":::

7. Dialpad bir Openıdconnect uygulaması olduğundan, Microsoft iş hesabınızı kullanarak giriş paneli 'Nde oturum açmayı seçin.

    :::image type="content" source="media/dialpad-provisioning-tutorial/loginpage.png" alt-text="Dialpad Web sitesinde çağrı yapmayı Başlat sayfasının ekran görüntüsü. Office 365 ile oturum açma düğmesi vurgulanır." border="false":::

8. Başarılı bir kimlik doğrulamasından sonra, onay sayfasının onay isteğini kabul edin. Uygulama daha sonra kiracınıza otomatik olarak eklenir ve bu, Dialpad hesabınıza yönlendirilirsiniz.

    :::image type="content" source="media/dialpad-provisioning-tutorial/redirect.png" alt-text="Bir Microsoft kimlik doğrulama sayfasını gösteren ve çevir paneli uygulamasının bazı verilere erişim isteğinde bulunduğunu belirten ekran görüntüsü. Kabul et düğmesi vurgulanır." border="false":::

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Otomatik Kullanıcı sağlamasını Dialpad 'e yapılandırma

Bu bölümde Azure AD sağlama hizmeti 'ni kullanarak, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan Dialpad 'teki kullanıcıları ve/veya grupları oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Azure AD 'de Dialpad için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Dialpad**' i seçin.

    ![Uygulamalar listesindeki tuş takımı bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, `https://dialpad.com/scim` **kiracı URL 'sini** girin. Daha önce aldığınız ve **gizli belirteçteki** dialpad 'ten kaydettiğiniz değeri girin. Azure AD 'nin Dialpad 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Dialpad hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları çevir paneli**' ni seçin.

    ![Tuş takımı Kullanıcı eşlemeleri](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. **Öznitelik eşleme** bölümünde Azure AD 'Den dialpad 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme işlemleri Için, dialpad 'teki Kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Tuş takımı Kullanıcı öznitelikleri](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

11. Azure AD sağlama hizmetini Dialpad için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** içindeki Istenen değerleri seçerek, çevirme paneline sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

13. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek için **eşitleme ayrıntıları** bölümünü ve Azure AD sağlama hizmeti tarafından, dialpad üzerinde gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinlik raporuna ilişkin bağlantıları takip edebilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Bağlayıcı sınırlamaları
* Dialpad, Grup yeniden adlandırmaları bugün desteklemez. Bu, Azure AD 'deki bir grubun **DisplayName** 'e yapılan tüm değişikliklerin, tuş takımında güncellenmeyeceği ve yansıtılmayacağı anlamına gelir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
