---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Iş Dropbox 'ı yapılandırın | Microsoft Docs"
description: Kullanıcı hesaplarını Iş için otomatik olarak sağlamak ve devre dışı bırakmak üzere Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 4e718ef30d029995c49efe36f19a6efcf7de0616
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589610"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için Iş Dropbox 'ı yapılandırma

Bu öğreticinin amacı, Kullanıcı ve/veya grupları otomatik olarak sağlamak ve devre dışı bırakmak için Azure AD 'yi yapılandırmak üzere Iş için Dropbox ve Azure Active Directory (Azure AD) ' de gerçekleştirilecek adımları göstermektir.

> [!IMPORTANT]
> Gelecekte, Microsoft ve Dropbox eski Dropbox tümleştirmesini kullanımdan kaldırır. Bu, başlangıçta 4/1/2021 için planlanmıştı, ancak süresiz olarak ertelendi. Ancak, hizmetin kesintiye uğramaması için, grupları destekleyen yeni SCıM 2,0 Dropbox tümleştirmesi ' ne geçiş yapmanızı öneririz. Yeni Dropbox tümleştirmesine geçiş yapmak için aşağıdaki adımları kullanarak Azure AD kiracınızda sağlama için yeni bir Dropbox örneği ekleyin ve yapılandırın. Yeni Dropbox tümleştirmesini yapılandırdıktan sonra, sağlama çakışmalarını önlemek için eski Dropbox tümleştirmesinde sağlamayı devre dışı bırakın. Yeni Dropbox tümleştirmesini geçirmeye yönelik daha ayrıntılı adımlar için bkz. [Azure ad kullanarak iş için en yeni Dropbox 'A güncelleştirme](https://help.dropbox.com/installs-integrations/third-party/update-dropbox-azure-ad-connector).

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Iş kiracısı için Dropbox](https://www.dropbox.com/business/pricing)
* Yönetici izinlerine sahip Iş için Dropbox 'ta bir kullanıcı hesabı.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Galeriden Iş için Dropbox ekleyin

Azure AD ile otomatik Kullanıcı sağlaması için Iş için Dropbox 'u Iş için yapılandırmadan önce, Azure AD Uygulama Galerisi 'nden yönetilen SaaS uygulamaları listenize Iş için Dropbox eklemeniz gerekir.

**Azure AD uygulama galerisinden Iş için Dropbox eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Ara kutusuna **iş Için Dropbox**' u girin, sonuçlar panelinde **iş için Dropbox** ' u seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuç listesinde Iş için Dropbox](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Iş için Dropbox 'a Kullanıcı atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Iş için Dropbox 'a erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Iş için Dropbox 'a atayabilirsiniz:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Iş için Dropbox 'a Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek üzere Iş için Dropbox 'a tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Iş için Dropbox 'a bir Kullanıcı atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Iş için Dropbox 'a otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan Iş için Dropbox 'ta bulunan kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında kılavuzluk eder.

> [!TIP]
> İş için Dropbox tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Bu işlem [Için Dropbox for Business Single oturum açma öğreticisinde](dropboxforbusiness-tutorial.md)sunulan talimatları takip edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Azure AD 'de Iş için Dropbox otomatik Kullanıcı sağlamasını yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **iş Için Dropbox**' u seçin.

    ![Uygulamalar listesinde Iş için Dropbox bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici Kimlik Bilgileri** bölümünde **Yetki Ver**'e tıklayın. Yeni bir tarayıcı penceresinde Iş oturum açma iletişim kutusu açılır.

    ![Sağlama ](common/provisioning-oauth.png)

6. Iş için **Dropbox 'Ta Azure AD iletişim kutusuyla bağlantı sağlamak için** , iş kiracınızda Dropbox 'ta oturum açın ve kimliğinizi doğrulayın.

    ![Iş oturumu açma için Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. 5 ve 6. adımları tamamladıktan sonra, Azure AD 'nin Iş için Dropbox 'a bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa Iş hesabınız için Dropbox 'ın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Belirteç](common/provisioning-testconnection-oauth.png)

8. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

9. **Kaydet**’e tıklayın.

10. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Dropbox**' a eşitler ' ı seçin.

    ![Dropbox Kullanıcı eşlemeleri](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. **Öznitelik eşleme** bölümünde Azure AD 'den Dropbox 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için Dropbox 'taki Kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Dropbox Kullanıcı öznitelikleri](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. **Eşlemeler** bölümünde **Azure Active Directory gruplarını Dropbox 'a eşitler**' ı seçin.

    ![Dropbox grubu eşlemeleri](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. **Öznitelik eşleme** bölümünde Azure AD 'den Dropbox 'a eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için Dropbox 'taki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Dropbox grubu öznitelikleri](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

15. Dropbox için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

16. **Ayarlar** bölümünde **kapsam** Içindeki istenen değerleri seçerek Dropbox 'a sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

17. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek ve Dropbox 'ta Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinlik raporuna yönelik bağlantıları izlemek için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları
 
* Dropbox, davet edilen kullanıcıların askıya alınmasını desteklemez. Davet edilen bir Kullanıcı askıya alınırsa, bu kullanıcı silinir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)

