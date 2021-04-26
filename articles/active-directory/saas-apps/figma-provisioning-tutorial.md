---
title: 'Öğretici: figma otomatik Kullanıcı sağlamasını Azure Active Directory ile yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını figma 'ya otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: ec509cc04db87602b3e29230253d9b363a5e30d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353927"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için figma yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları figma 'e otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için figma ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* [Figma kiracısı](https://www.figma.com/pricing/).
* Yönetici izinleriyle figma 'teki bir kullanıcı hesabı.

## <a name="assign-users-to-figma"></a>Kullanıcıları figma 'a atayın.
Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen atama adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların figma erişimine ihtiyacı olduğuna karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları figma 'ler 'e atayabilirsiniz:
 
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Figma 'e Kullanıcı atamaya yönelik önemli ipuçları

 * Otomatik Kullanıcı sağlama yapılandırmasını test etmek için figma 'e tek bir Azure AD kullanıcısı atanmalıdır. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Figma 'e Kullanıcı atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. Varsayılan erişim rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-figma-for-provisioning"></a>Sağlama için figma ayarlama

Azure AD ile otomatik Kullanıcı sağlaması için figma 'yi yapılandırmadan önce, figma 'lerden bazı sağlama bilgileri almanız gerekir.

1. [Figma Yönetici konsolunuza](https://www.Figma.com/)oturum açın. Kiracınızın yanındaki dişli simgesine tıklayın.

    :::image type="content" source="media/Figma-provisioning-tutorial/image0.png" alt-text="Figma yönetim konsolunun ekran görüntüsü. D SCIM testi adlı bir kiracı görünür. Kiracının yanında dişli simgesi vurgulanır." border="false":::

2. **Ayarlar ' da genel > güncelleştirme günlüğü '** ne gidin.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma03.png" alt-text="Figma yönetim konsolunun Genel sekmesinin ekran görüntüsü. Oturum açma ve sağlama altında, güncelleştirme günlüğü ayarları vurgulanır." border="false":::

3. **KIRACı kimliğini** kopyalayın. Bu değer, Azure portal figma uygulamanızın sağlama sekmesinde **KIRACı URL** 'si alanına girilecek SCIM uç noktası URL 'sini oluşturmak için kullanılır.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma-tenantid.png" alt-text="Figma yönetim konsolundaki S A M L s S O bölümünün ekran görüntüsü. Bir kiracı KIMLIĞI etiketi ve kopyayı yazan bitişik bir bağlantı vurgulanır." border="false":::

4. Aşağı kaydırın ve **API belirteci oluştur**' a tıklayın.

    :::image type="content" source="media/Figma-provisioning-tutorial/token.png" alt-text="Figma yönetim konsolundaki S C I M sağlama bölümünün ekran görüntüsü. P I belirteci oluştur etiketli bir bağlantı vurgulanır." border="false":::

5. **API belirteç** değerini kopyalayın. Bu değer, Azure portal figma uygulamanızın sağlama sekmesindeki **gizli belirteç** alanına girilir. 

    :::image type="content" source="media/Figma-provisioning-tutorial/figma04.png" alt-text="Figma yönetim konsolundaki bir sayfanın ekran görüntüsü. Bir P I belirtecini sağlamaktan sonra, belirteç için bir yer tutucu vurgulanır." border="false":::

## <a name="add-figma-from-the-gallery"></a>Galeriden figma ekleme

Azure AD ile otomatik Kullanıcı sağlaması için figma 'yi yapılandırmak için Azure AD uygulama galerisindeki figma 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **figma** girin, sonuçlar panelinde **fıgma** ' i seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde figma](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Figma 'e otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde, Azure AD sağlama hizmetini kullanarak figma 'teki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan Kullanıcı ve/veya grupları yapılandırma adımları adım adım izlenecek yol

> [!TIP]
> [Figma çoklu oturum açma öğreticisinde](figma-tutorial.md)sunulan yönergeleri Izleyerek figma için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Azure AD 'de figma için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **figma**' i seçin.

    ![Uygulamalar listesindeki figma bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, `https://www.figma.com/scim/v2/<TenantID>` **tenantıd** 'nin daha önce yer aldığı değer olan **kiracı URL 'sini** girin. **API belirteç** değerini **gizli belirteç** olarak girin. Azure AD 'nin figma 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, figma hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

8. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

9. **Kaydet**’e tıklayın.

10. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları figma olarak eşitler**' ı seçin.

    ![Figma Kullanıcı eşlemeleri](media/Figma-provisioning-tutorial/figma05.png)

11. **Öznitelik eşleme** bölümünde Azure AD 'Den fıgma 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemlerinde figma içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Figma Kullanıcı öznitelikleri](media/Figma-provisioning-tutorial/figma06.png)

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. Figma için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek figma 'a sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilir ve figma ÜZERINDE Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan etkinlik raporuna yönelik bağlantıları izleyebilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)