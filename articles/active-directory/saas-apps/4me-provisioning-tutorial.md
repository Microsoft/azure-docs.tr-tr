---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için 4me yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını 4me 'ye otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: c0c428997cfba8871a29d9bfe0df0a6920a1d22f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998677"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için 4me yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları 4me 'ye otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için 4me ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Bir 4me kiracısı](https://www.4me.com/trial/)
* Yönetici izinleriyle 4me 'de bir kullanıcı hesabı.

## <a name="add-4me-from-the-gallery"></a>Galeriden 4me ekleyin

Azure AD ile otomatik Kullanıcı sağlaması için 4me 'yi yapılandırmadan önce Azure AD uygulama galerisindeki 4me 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden 4me eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **4me** girin, sonuçlar panelinde **4me** ' yi seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuç listesinde 4me](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Kullanıcıları 4me 'ye atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcı ve/veya grupların 4me 'ye erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları 4me 'ye atayabilirsiniz:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>4me 'ye Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için tek bir Azure AD kullanıcısının 4me 'ye atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı 4me 'ye atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Otomatik Kullanıcı sağlamayı 4me 'ye yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak Kullanıcı ve/veya grupları Azure AD 'de Kullanıcı ve/veya grup atamalarına göre oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

> [!TIP]
> Ayrıca, 4me [Çoklu oturum açma öğreticisinde](4me-tutorial.md)sunulan yönergeleri izleyerek 4ME için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Azure AD 'de 4me için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **4me**' yi seçin.

    ![Uygulamalar listesindeki 4me bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. 4me hesabınızın **kiracı URL 'sini** ve **gizli belirtecini** almak için adım 6 ' da anlatıldığı şekilde izlenecek yolu izleyin.

6. 4me Yönetici konsolunuza oturum açın. **Ayarlar**' a gidin.

    ![4me ayarları](media/4me-provisioning-tutorial/4me01.png)

    Arama çubuğuna **uygulamalar** yazın.

    ![4me uygulamaları](media/4me-provisioning-tutorial/4me02.png)

    Gizli belirteç ve SCıM uç noktasını almak için **SCIM** açılan listesini açın.

    ![4me SCıM](media/4me-provisioning-tutorial/4me03.png)

7. 5. adımda gösterilen alanları doldurarak Azure AD 'nin 4me 'ye bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, 4me hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Belirteç](common/provisioning-testconnection-tenanturltoken.png)

8. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

9. **Kaydet**’e tıklayın.

10. Eşlemeler bölümünde, **kullanıcıları Azure Active Directory** **eşleme** ' yi seçin.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-user-mapping.png" alt-text="Eşlemeler sayfasının ekran görüntüsü. Ad ' ın altında, Azure Active Directory Kullanıcılar ' ı on onme ile eşitler." border="false":::
    
11. **Öznitelik eşleme** bölümünde Azure AD 'den 4me 'ye eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme işlemleri için 4me 'deki Kullanıcı hesaplarını eşleştirmek için kullanılır. Lütfen 4me 'nin seçtiğiniz eşleşen öznitelikte [filtrelemeyi desteklediğinden](https://developer.4me.com/v1/scim/users/) emin olun. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-user-attributes.png" alt-text="Öznitelik eşlemeleri sayfasının ekran görüntüsü. Bir tablo Azure Active Directory öznitelikleri, karşılık gelen on onme özniteliklerini ve eşleşen durumu listeler." border="false":::
    
12. **Eşlemeler** bölümünde **Azure Active Directory gruplarını 4Me olarak eşitler**' ı seçin.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-group-mapping.png" alt-text="Eşlemeler sayfasının ekran görüntüsü. Ad ' ın altında, Azure Active Directory gruplarını on dört olarak Eşitlerme vurgulanır." border="false":::
    
13. **Öznitelik eşleme** bölümünde Azure AD 'den 4me 'ye eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme işlemleri için 4me 'deki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![4me grup eşlemeleri](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

15. Azure AD sağlama hizmetini 4me için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

16. **Ayarlar** bölümünde **kapsamda** istenen değerleri seçerek 4me 'ye sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

17. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek ve Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri 4me üzerinde gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinliği raporuna yönelik bağlantıları izlemek için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* 4me, test ve üretim ortamları için farklı SCıM uç noktası URL 'Lerine sahiptir. İkincisi,. **com** ile sona erdiğinde **. qa** ile biter
* 4me üretilen gizli belirteçleri, oluşturma işleminden itibaren bir ayın sona erme tarihine sahiptir.
* 4me **silme** işlemlerini desteklemiyor

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
