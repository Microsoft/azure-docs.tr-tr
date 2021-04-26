---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı hazırlama için Proxyclick yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını Proxyclick 'e otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
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
ms.openlocfilehash: f7d2a6f01e891a7fb1c14cde552d66679e474139
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359179"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için Proxyclick yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları Proxyclick 'e otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için, Proxyclick ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Bir Proxyclick kiracısı](https://www.proxyclick.com/pricing)
* Yönetim izinlerine sahip bir kullanıcı hesabı.

## <a name="add-proxyclick-from-the-gallery"></a>Galeriden Proxyclick ekleyin

Azure AD ile otomatik Kullanıcı hazırlama için Proxyclick 'i yapılandırmadan önce Azure AD Uygulama Galerisi 'nden yönetilen SaaS uygulamaları listenize Proxyclick eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden Proxyclick eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, **proxyclick** yazın, sonuçlar panelinde **proxyclick** ' i seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Proxyclick sonuçlar listesinde](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Kullanıcıları Proxyclick 'e atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Proxyclick 'e erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Proxyclick 'e atayabilirsiniz:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Kullanıcıları Proxyclick 'e atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için, Proxyclick 'e tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Proxyclick 'e atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Otomatik Kullanıcı sağlamayı Proxyclick 'e yapılandırma 

Bu bölüm, Azure AD sağlama hizmetini Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan Kullanıcı ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında size rehberlik eder.

> [!TIP]
> Ayrıca, proxyclick [Çoklu oturum açma öğreticisinde](proxyclick-tutorial.md)belirtilen yönergeleri Izleyerek, proxytıklamı için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Azure AD 'de Proxyclick otomatik Kullanıcı sağlamasını yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Proxyclick**' i seçin.

    ![Uygulamalar listesinde Proxyclick bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. Proxyclick hesabınızın **kiracı URL 'sini** ve **gizli belirtecini** almak için adım 6 ' da anlatıldığı şekilde izlenecek yolu izleyin.

6. [Proxyclick yönetim konsolunda](https://app.proxyclick.com/login//?destination=%2Fdefault)oturum açın. **Ayarlar**  >  **tümleştirmelere** git  >  **Market**'e gidin.

    ![Proxyclick ayarları](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Proxyclick tümleştirmeleri](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick marketi](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    **Azure AD**'yi seçin. **Şimdi yüklensin**' e tıklayın.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Proxyclick yüklemesi](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    **Kullanıcı hazırlama** ' yı seçin ve **tümleştirmeyi Başlat**' a tıklayın. 

    ![Proxyclick Kullanıcı sağlama](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    Uygun ayarlar yapılandırma kullanıcı arabirimi artık **Ayarlar**  >  **tümleştirmeleri** altında gösterilmelidir. Azure AD altında **ayarları** seçin **(Kullanıcı hazırlama)**.

    ![Proxyclick oluştur](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    **Kiracı URL 'sini** ve **gizli anahtar belirtecini** buradan bulabilirsiniz.

    ![Proxyclick belirteç oluştur](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. 5. adımda gösterilen alanları doldurarak Azure AD 'nin Proxyclick 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Proxyclick hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Belirteç](common/provisioning-testconnection-tenanturltoken.png)

8. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

9. **Kaydet**’e tıklayın.

10. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Proxyclick olarak eşitler**' ı seçin.

    ![Proxyclick Kullanıcı eşlemeleri](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. **Öznitelik eşleme** bölümünde, Azure AD 'den Proxya ' ya eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, proxyclick içindeki kullanıcı hesaplarıyla eşleştirmek için, güncelleştirme işlemleri için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Proxyclick Kullanıcı öznitelikleri](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

14. Azure AD sağlama hizmetini Proxyclick için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

15. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek proxytıklamaya sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

16. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. **Eşitleme ayrıntıları** bölümünü Izleyip, proxyclick ÜZERINDE Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan sağlama etkinliği raporunu kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Proxyclick, aynı kaynak değerine sahip olmak için **e-posta** ve **Kullanıcı adı** gerektirir. Her iki özniteliğe yönelik güncelleştirme, diğer değeri değiştirir.
* Proxyclick, gruplar için sağlamayı desteklemez.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)

