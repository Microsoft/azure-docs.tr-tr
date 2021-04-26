---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Zscaler Beta 'yı yapılandırın | Microsoft Docs"
description: Kullanıcı hesaplarını Zscaler Beta 'ya otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 0d4945ee97a46c78aac3c4ac508c5f89f5942296
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937170"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Zscaler Beta 'yı yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, Kullanıcı ve/veya grupları Zscaler Beta sürümüne otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Zscaler Beta ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, zaten aşağıdakilere sahip olduğunuzu varsayar:

* Bir Azure AD kiracısı
* Zscaler Beta kiracısı
* Zscaler Beta 'da yönetici izinlerine sahip bir kullanıcı hesabı

> [!NOTE]
> Azure AD sağlama tümleştirmesi, kurumsal pakete sahip hesaplar için Zscaler Beta geliştiricileri tarafından kullanılabilen Zscaler Beta SCıM API 'sini kullanır.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Galeriden Zscaler Beta ekleme

Azure AD ile otomatik Kullanıcı sağlaması için Zscaler Beta 'yı yapılandırmadan önce Azure AD uygulama galerisindeki Zscaler Beta sürümünü yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden Zscaler Beta eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Zscaler Beta** yazın, sonuç panelinden **Zscaler Beta** ' yı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde Zscaler Beta](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Zscaler Beta sürümüne Kullanıcı atama

Azure Active Directory, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyebilmek için "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya "atanmış" olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Zscaler Beta 'ya erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Zscaler Beta sürümüne atayabilirsiniz:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Zscaler Beta sürümüne Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için, Zscaler Beta sürümüne tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Zscaler Beta sürümüne atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Zscaler Beta sürümüne otomatik Kullanıcı sağlamayı yapılandırma

Bu bölümde Azure AD sağlama hizmeti 'ni kullanarak, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan Zscaler Beta 'daki kullanıcıları ve/veya grupları oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

> [!TIP]
> Zscaler Beta [Çoklu oturum açma öğreticisinde](zscaler-beta-tutorial.md)sunulan yönergeleri Izleyerek Zscaler Beta için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

> [!NOTE]
> Kullanıcılar ve gruplar sağlandığında veya sağlanmak istendiğinde, grup üyeliklerinin düzgün şekilde güncelleştirildiğinden emin olmak için düzenli aralıklarla sağlamayı yeniden başlatmanızı öneririz. Yeniden başlatma işlemi yapıldığında, hizmetimizi tüm grupları yeniden değerlendirmeye ve üyelikleri güncelleştirmeye zorlar.  

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Azure AD 'de Zscaler Beta için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Zscaler Beta**' yı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Zscaler Beta**' yı seçin.

    ![Uygulamalar listesindeki Zscaler Beta bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Kategoriler halinde düzenlenmiş sekmelerin listesi ve ZScaler Beta hazırlama/kuruluş uygulaması başlıklı bir liste vardır. Yönet kategorisinin sağlama sekmesi seçilidir.](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik mod, sağlama modu açılır listesinden seçildi. Yönetim kimlik bilgileri için, Zscaler Beta 'un API 'sine bağlanmak için kullanılan alanlar bulunur ve bir test bağlantısı düğmesi vardır.](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. **Yönetici kimlik bilgileri** bölümünde, 6. adımda açıklanan şekilde Zscaler Beta hesabınızın **kiracı URL 'Sini** ve **gizli belirtecini** girin.

6. **Kiracı URL 'sini** ve **gizli anahtarı** almak Için, Zscaler Beta portalı Kullanıcı arabirimindeki **Yönetim > kimlik doğrulama ayarları** ' na gidin ve **kimlik doğrulaması türü** altında **SAML** ' ye tıklayın.

    ![Kimlik doğrulama ayarlarındaki kimlik doğrulama profilinde, seçili dizin türü barındırılan VERITABANı ve seçilen kimlik doğrulama türü SAML ' dir.](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    **Yapılandırma SAML** seçeneklerini açmak için **SAML** yapılandırması ' na tıklayın.

    ![SAML Yapılandır sayfasında SAML otomatik sağlamayı etkinleştir ve SCIM-Based sağlama seçeneklerini etkinleştir seçeneği seçilidir. Temel URL ve taşıyıcı belirteci metin kutuları vurgulanır.](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    **Temel URL** ve **taşıyıcı belirtecini** almak için **SCIM-Based sağlamayı etkinleştir** ' i seçin, sonra ayarları kaydedin. **Temel URL** 'YI **kiracı URL**'sine ve **taşıyıcı belirtecini** Azure Portal **gizli belirtece** kopyalayın.

7. 5. adımda gösterilen alanları doldurarak Azure AD 'nin Zscaler Beta 'ya bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Zscaler Beta hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Yönetici kimlik bilgileri ' nde, kiracı URL 'SI ve gizli dizi belirteci alanlarında değerler vardır ve Bağlantıyı Sına düğmesi vurgulanır.](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin ve **bir hata oluştuğunda e-posta bildirimi gönder** onay kutusunu işaretleyin.

    ![Bildirim e-postası metin kutusu boştur ve bir hata oluştuğunda e-posta bildirimi gönder onay kutusu temizlenir.](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. **Kaydet**’e tıklayın.

10. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Zscaler Beta olarak eşitler**' ı seçin.

    ![Azure Active Directory Kullanıcıları ZScalerBeta ile eşitler ve etkinleştirilir.](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. **Öznitelik eşleme** bölümünde Azure AD 'Den Zscaler Beta sürümüne eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Zscaler Beta 'daki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Kullanıcı özniteliklerinin öznitelik eşlemeleri bölümünde, Active Directory öznitelikleri, eşitlendikleri Zskaler Beta özniteliklerinin yanında gösterilir. Bir çift öznitelik eşleşme olarak gösterilir.](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. **Eşlemeler** bölümünde **Azure Active Directory gruplarını Zscaler Beta olarak eşitler**' ı seçin.

    ![Azure Active Directory gruplarını ZScalerBeta olarak eşitler ve etkinleştirilir.](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. **Öznitelik eşlemeleri** bölümünde Azure AD 'Den Zscaler Beta sürümüne eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Zscaler Beta 'daki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Grup özniteliklerinin öznitelik eşlemeleri bölümünde Active Directory öznitelikleri, eşitlendikleri Zskaler Beta özniteliklerinin yanında gösterilir. Bir çift öznitelik eşleşme olarak gösterilir.](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

15. Zscaler Beta için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu gösterilir ve açık olarak ayarlanır.](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. **Ayarlar** bölümünde **kapsam** Içinde Istenen değerleri seçerek Zscaler Beta sürümüne sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Kapsam açılan listesi gösterilir ve eşitleme yalnızca atanan kullanıcılar ve gruplar seçilidir. Diğer kullanılabilir değer tüm kullanıcıları ve grupları eşitler.](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Zscaler Beta sürümü sağlamanın en üstündeki Kaydet düğmesi vurgulanır. Ayrıca bir atma düğmesi de vardır.](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. , Zscaler Beta üzerinde Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, ilerlemeyi izlemek ve sağlama etkinliği raporunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png