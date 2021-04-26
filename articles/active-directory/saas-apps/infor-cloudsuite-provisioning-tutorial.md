---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Infor CloudSuite 'i yapılandırın | Microsoft Docs"
description: Kullanıcı hesaplarını Infor CloudSuite 'e otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 8fdd2c8a326fbdc68d1aec65377f4c465c5ee4c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353910"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Infor CloudSuite 'i yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları Infor CloudSuite 'e otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Infor CloudSuite ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Bir Infor CloudSuite kiracısı](https://www.infor.com/products/infor-os)
* Yönetici izinleriyle CloudSuite 'e ait bir kullanıcı hesabı.

## <a name="assigning-users-to-infor-cloudsuite"></a>Kullanıcılar Infor CloudSuite 'e atanıyor

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların CloudSuite 'e erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Into CloudSuite 'e atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Kullanıcıları CloudSuite 'e atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek üzere CloudSuite 'e tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Infor CloudSuite 'e atarken atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Sağlama için Infor CloudSuite 'i ayarlama

1. [Infor CloudSuite yönetici konsolunda](https://www.infor.com/customer-center)oturum açın. Kullanıcı simgesine tıklayın ve ardından **Kullanıcı yönetimi**' ne gidin.

    ![Infor CloudSuite Yönetici Konsolu](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Ekranın sol üst köşesindeki menü simgesine tıklayın. **Yönet**' e tıklayın.

    ![Infor CloudSuite SCıM Ekle](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  **SCIM hesaplarına** gidin.

    ![Cloudsuite Scim hesabı için ın](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Artı simgesine tıklayarak bir yönetici kullanıcı ekleyin. Bir **SCIM parolası** sağlayın ve **Parolayı Onayla**' nın altında aynı parolayı yazın. Parolayı kaydetmek için klasör simgesine tıklayın. Daha sonra yönetici kullanıcı için oluşturulan bir **Kullanıcı tanımlayıcısı** görürsünüz.

    ![Cloudsuite Yönetici kullanıcısı için in](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![CloudSuite parolası eksik](media/infor-cloudsuite-provisioning-tutorial/password.png)

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/identifier.png" alt-text="Bir vurgulanan tablo satırını gösteren CloudSuite yönetici konsolunun ekran görüntüsü. Bu satır bir kullanıcı tanımlayıcısı, parolalar ve bir zaman damgası içerir." border="false":::

5. Taşıyıcı belirtecini oluşturmak için **Kullanıcı tanımlayıcısı** ve **SCIM parolasını** kopyalayın. Bunları, iki nokta üst üste ile ayırarak Not defteri + + içine yapıştırın. **Eklenti > MIME araçları > Basic64 kodlama**' ya giderek dize değerini kodlayın. 

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/token.png" alt-text="Bir not defteri + + belgesinin ekran görüntüsü. Eklentiler menüsünde, MIME araçları vurgulanır. MIME araçları menüsünde, Base64 kodlaması vurgulanır." border="false":::

3.  Taşıyıcı belirtecini kopyalayın. Bu değer, Azure portal Infor CloudSuite uygulamanızın sağlama sekmesindeki gizli belirteç alanına girilir.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Galeriden CloudSuite 'i ekleme

Azure AD ile otomatik Kullanıcı sağlama için Infor CloudSuite 'i yapılandırmadan önce, Azure AD uygulama galerisindeki yönetilen SaaS uygulamaları listenize CloudSuite 'i eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden CloudSuite Için Infor CloudSuite eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Ara kutusuna **Cloudsuite Için Infor** cloudsuite yazın, sonuçlar panelinde **cloudsuite** ' i seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde CloudSuite Infor CloudSuite](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Otomatik Kullanıcı sağlamasını Infor CloudSuite 'e yapılandırma 

Bu bölümde, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan CloudSuite içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD sağlama hizmetini yapılandırma adımlarında size kılavuzluk eder.

> [!TIP]
> Infor [Cloudsuite çoklu oturum açma öğreticisinde](./infor-cloud-suite-tutorial.md)belirtilen yönergeleri Izleyerek, Infor CLOUDSUITE için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

> [!NOTE]
> CloudSuite 'in SCıM uç noktası hakkında daha fazla bilgi edinmek için [buna](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#)bakın.

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Azure AD 'de Infor CloudSuite için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **CloudSuite Için ıntıl** öğesini seçin.

    ![Uygulamalar listesindeki CloudSuite Için Infor CloudSuite bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` **kiracı URL 'sini** girin. Daha önce **gizli bir belirteçte** alınan taşıyıcı belirteç değerini girin. Azure AD 'nin Infor CloudSuite 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, CloudSuite hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları cloudsuite ' e eşitler**' ı seçin.

    ![Cloudsuite Kullanıcı eşlemeleri için ın](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. **Öznitelik eşleme** bölümünde, Azure AD 'Den Infor cloudsuite 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Infor cloudsuite içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![CloudSuite Kullanıcı öznitelikleri için Infor](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. **Eşlemeler** bölümünde, **Azure Active Directory gruplarını Cloudsuite için ınsınıgt**' yi seçin.

    ![CloudSuite Grup eşlemelerini geçersiz](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. **Öznitelik eşleme** bölümünde, Azure AD 'Den Infor cloudsuite 'e eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Infor cloudsuite içindeki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![CloudSuite grubu öznitelikleri için Infor](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. Infor CloudSuite için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek cloudsuite 'e sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. **Eşitleme ayrıntıları** bölümünü kullanarak ilerlemeyi izleyebilir ve sağlama etkinlik raporuna ilişkin bağlantıları izleyebilirsiniz. Bu, Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri cloudsuite üzerinde açıklar.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)