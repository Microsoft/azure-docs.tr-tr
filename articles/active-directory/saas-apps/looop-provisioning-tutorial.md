---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Gevop yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını Gevyana sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 528003ac482da6f254bf437321c70c389d23844b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835093"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Gevteri yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları Gevşekilde sağlamak ve devre dışı bırakmak üzere yapılandırmak için, Gevteri ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Bir Gevteri kiracısı](https://www.looop.co/pricing/)
* Yönetici izinleriyle bir Gevteri üzerinde kullanıcı hesabı.

## <a name="assign-users-to-looop"></a>Gevteri 'e Kullanıcı atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen atama adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Gevteri 'e erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Gevteri 'e atayabilirsiniz:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Gevteri 'e Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için tek bir Azure AD kullanıcısının Gevylede atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Gevteri 'e atarken atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-looop-for-provisioning"></a>Sağlama için Gevteri ayarla

Azure AD ile otomatik Kullanıcı hazırlama için gevyleop 'ı yapılandırmadan önce, bazı sağlama bilgilerini gevyleden almanız gerekir.

1. [Gevpop yönetim konsolunuza](https://app.looop.co/#/login) oturum açın ve **Hesap**' ı seçin. **Hesap ayarları** altında **kimlik doğrulaması**' nı seçin.

    :::image type="content" source="media/looop-provisioning-tutorial/admin.png" alt-text="Gevpop yönetim konsolunun ekran görüntüsü. Hesap sekmesi vurgulanır ve açıktır. Hesap ayarları altında kimlik doğrulaması vurgulanır." border="false":::

2. **SCIM tümleştirmesi** altında **belirteci Sıfırla** ' ya tıklayarak yeni bir belirteç oluşturun.

    :::image type="content" source="media/looop-provisioning-tutorial/resettoken.png" alt-text="Gevteri yönetim konsolundaki bir sayfanın S C I-tümleştirme bölümünün ekran görüntüsü. Belirteci Sıfırla düğmesi vurgulanır." border="false":::

3. **SCIM uç noktasını** ve **belirtecini** kopyalayın. Bu değerler, Azure portal **Gevrme** uygulamanızın sağlama sekmesinde **kiracı URL 'Si** ve gizli belirteç alanlarına girilir. 

    ![Gevteri oluşturma belirteci oluştur](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Galeriden Gevteri ekleyin

Azure AD ile otomatik Kullanıcı sağlaması için Gevfiop 'yi yapılandırmak için Azure AD uygulama galerisindeki bir Gevteri yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **gevleop** girin, sonuçlar panelinde **gevleop** ' ı seçin. 

    ![Sonuçlar listesinde gevteri](common/search-new-app.png)

5. Sizi gevrme 'nin oturum açma sayfasına yönlendiren, **Gevrop Için kaydolma** düğmesini seçin. 

    ![Gevşop OıDC Add](media/looop-provisioning-tutorial/signup.png)

6. Gevteri bir Openıdconnect uygulaması olduğu için Microsoft iş hesabınızı kullanarak Gevşekilde oturum açmak için seçin.

    ![Gevşop OıDC oturumu](media/looop-provisioning-tutorial/msftlogin.png)

7. Başarılı bir kimlik doğrulamasından sonra, onay sayfasının onay isteğini kabul edin. Uygulama daha sonra kiracınıza otomatik olarak eklenir ve Gevleop hesabınıza yönlendirilirsiniz.

    ![Gevşop OIDC onayı](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Gevteri için otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni kullanarak, Azure AD 'de Kullanıcı ve/veya Grup atamaları temelinde bulunan kullanıcıları ve/veya grupları Gevşekilde oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Azure AD 'de Gevteri 'in otomatik Kullanıcı sağlamasını yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Gevteri**' ı seçin.

    ![Uygulamalar listesindeki Gevteri bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, `https://<organisation_domain>.looop.co/scim/v2` **kiracı URL 'sini** girin. Örneğin, `https://demo.looop.co/scim/v2`. Daha önce aldığınız ve **gizli bir belirteçte** daha önce bulunan bir değeri girin. Azure AD ' ın Gevteri 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Gevteri hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları gevşekilde zamanla**' yı seçin.

    ![Gevteri Kullanıcı eşlemeleri](media/looop-provisioning-tutorial/usermappings.png)

9. **Öznitelik eşleme** bölümünde, Azure AD 'Den yedekop 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme işlemleri Için, Gevvop 'daki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|Filtreleme için destekleniyor|
   |---|---|---|
   |userName|Dize|&check;|
   |active|Boole|
   |emails[type eq "work"].value|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |externalId|Dize|
   |urn: IETF: params: SCIM: schemas: Extension: Gevop: 2.0: Kullanıcı: alan|Dize|
   |urn: IETF: params: SCIM: schemas: Extension: Gevop: 2.0: Kullanıcı: custom_1|Dize|
   |urn: IETF: params: SCIM: schemas: Extension: Gevop: 2.0: Kullanıcı: custom_2|Dize|
   |urn: IETF: params: SCIM: schemas: Extension: Gevop: 2.0: Kullanıcı: custom_3|Dize|
   |urn: IETF: params: SCIM: schemas: Extension: Gevop: 2.0: Kullanıcı: departman|Dize|
   |urn: IETF: params: SCIM: schemas: Extension: Gevop: 2.0: Kullanıcı: employee_id|Dize|
   |urn: IETF: params: SCIM: schemas: Extension: Gevop: 2.0: Kullanıcı: location|Dize|
   |urn: IETF: params: SCIM: schemas: Extension: Gevop: 2.0: Kullanıcı: konum|Dize|
   |urn: IETF: params: SCIM: schemas: Extension: Gevop: 2.0: Kullanıcı: startAt|Dize|

10. **Eşlemeler** bölümünde, **Azure Active Directory gruplarını meta Networks Bağlayıcısı olarak eşitler**' ı seçin.

    ![GEVME grubu eşlemeleri](media/looop-provisioning-tutorial/groupmappings.png)

11. **Öznitelik eşleme** bölümünde Azure AD 'Den meta Networks bağlayıcısına eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için meta ağlar bağlayıcısında grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    |Öznitelik|Tür|Filtreleme için destekleniyor|
    |---|---|---|
    |displayName|Dize|&check;|
    |üyeler|Başvuru|
    |externalId|Dize|


10. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

11. Azure AD sağlama hizmetini Gevteri için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** Içinde istenen değerleri seçerek GEVME sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

13. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. Devam eden işlemleri izlemek ve sağlama etkinliği raporunu izlemek için **eşitleme ayrıntıları** bölümünü kullanarak, Gevylede Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklar.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)


