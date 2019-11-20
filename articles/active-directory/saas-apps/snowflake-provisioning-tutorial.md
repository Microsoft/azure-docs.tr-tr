---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için kar ke 'yi yapılandırma | Microsoft Docs"
description: "' Nin kar/veya Kullanıcı hesaplarını otomatik olarak sağlaması ve serbest bırakmak üzere Azure Active Directory nasıl yapılandırılacağını öğrenin."
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 04d9ec8cad2404466d2df649df4d5c461768b76f
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693553"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Öğretici: Otomatik Kullanıcı sağlaması için kar ke 'yi yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları kar/veya grupları otomatik olarak sağlamak ve serbest bırakmak üzere yapılandırmak için kar ve Azure Active Directory (Azure AD) ' de gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* [Bir kar tanesi kiracısı](https://www.Snowflake.com/pricing/).
* Kar ortamında yönetici izinleriyle bir kullanıcı hesabı.

## <a name="assigning-users-to-snowflake"></a>Kullanıcıları kar alanına atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların kar/veya gruplarının erişimine ihtiyacı olduğuna karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları kar için atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Kullanıcıları kar alanına atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek üzere kar için tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı kar alanına atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-snowflake-for-provisioning"></a>Sağlama için kar ke ayarla

Azure AD ile otomatik Kullanıcı sağlaması için kar \ ' ı yapılandırmadan önce, kar için SCıM sağlamasını etkinleştirmeniz gerekecektir.

> [!NOTE]
> Bu tümleştirme, bugün kar için özel önizlemededir. Bu özelliği kar hesabı 'nda etkinleştirmek istiyorsanız, kar/satış temsilcinizle iletişime geçin.

1. Kar Yönetici Konsolu 'nda oturum açın. Aşağıda gösterilen sorguyu çalışma alanında vurgulanmış şekilde girin ve **Çalıştır**' a tıklayın.

    ![Kar tanesi Yönetici Konsolu](media/Snowflake-provisioning-tutorial/image00.png)

2.  Kar tanesi kiracınız için bir SCıM erişim belirteci oluşturulacaktır. Bunu almak için aşağıda vurgulanan bağlantıya tıklayın.

    ![Kar tanesi ekleme SCıM](media/Snowflake-provisioning-tutorial/image01.png)

3. Oluşturulan belirteç değerini kopyalayın ve **bitti**' ye tıklayın. Bu değer, Azure portal kar uygulama uygulamanızın sağlama sekmesindeki **gizli belirteç** alanına girilir.

    ![Kar tanesi ekleme SCıM](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>Galeriden kar Ekle

Azure AD ile otomatik Kullanıcı sağlamaya yönelik kar kümesini yapılandırmak için Azure AD uygulama galerisindeki yönetilen SaaS uygulamaları listenize Addkar etmeniz gerekir.

**Azure AD Uygulama Galerisi 'nden kar tanesi eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **kar tanesi**' ni girin, sonuçlar panelinde **kar** tanesi ' ni seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde kar tanesi](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Kar tanesi için otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alarak kar grubundaki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında size kılavuzluk eder.

> [!TIP]
> Ayrıca, [kar tanesi çoklu oturum](Snowflake-tutorial.md)açma öğreticisinde sunulan yönergeleri izleyerek kar için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Azure AD 'de kar için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kuruluş uygulamaları**' nı seçin ve ardından **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **kar tanesi**' ni seçin.

    ![Uygulamalar listesinde kar tanesi bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama sekmesi](common/provisioning-automatic.png)

5. Yönetici kimlik bilgileri bölümünün altında, kiracı `https://<Snowflake Account URL>/scim/v2` URL 'sini girin. Kiracı URL 'sine bir örnek:`https://acme.snowflakecomputing.com/scim/v2`

6. **Gizli belirteçte**daha önce alınan **SCIM kimlik doğrulama belirteci** değerini girin. Azure AD 'nin kar alanına bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, kar için hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

7. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

8. **Kaydet**’e tıklayın.

9. **Eşlemeler** bölümü altında, **kullanıcıları kar alarak Azure Active Directory eşitler**' ı seçin.

    ![Kar tanesi Kullanıcı eşlemeleri](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. **Öznitelik eşleme** bölümünde, Azure AD 'den kar tanesi ile eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için kar tanesi içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Kar tanesi Kullanıcı öznitelikleri](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. **Eşlemeler** bölümünde, **Azure Active Directory gruplarını kar/zaman olarak eşitler**' ı seçin.

    ![Kar tanesi grup eşlemeleri](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. **Öznitelik eşleme** bölümünde Azure AD 'den kar tanesi ile eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için kar tanesi içindeki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Kar tanesi grubu öznitelikleri](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

14. Kar tanesi için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

15. **Ayarlar** bölümünde **kapsam** Içindeki istenen değerleri seçerek kar için sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

16. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

    Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilir ve Azure AD sağlama hizmeti tarafından kar tanesi üzerinde gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinlik raporuna yönelik bağlantıları izleyebilirsiniz.

    Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../manage-apps/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Kar tanesi tarafından oluşturulan SCıM belirteçleri 6 ay içinde sona erer. Sağlama eşitlenmesinin çalışmaya devam etmesine izin vermek için bunların süreleri dolmadan önce yenilenmesi gerektiğini unutmayın. 

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar
* [Günlükleri incelemeyi ve sağlama etkinliğinde rapor almayı öğrenin](../manage-apps/check-status-user-account-provisioning.md).