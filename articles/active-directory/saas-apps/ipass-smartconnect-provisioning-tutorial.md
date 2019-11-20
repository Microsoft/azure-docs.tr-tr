---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için IPASS SmartConnect 'i yapılandırma | Microsoft Docs"
description: Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırma hakkında bilgi edinin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 43d6de4a-b3a2-439b-95f2-8883fffded52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 3141a32437e56bcefe830bc94109e93208e0258d
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232163"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Öğretici: Otomatik Kullanıcı sağlama için IPASS SmartConnect 'i yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları IPASS SmartConnect 'e otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için IPASS SmartConnect ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* [Bir IPASS SmartConnect kiracısı](https://www.ipass.com/buy-ipass/).
* Yönetici izinleriyle IPASS SmartConnect ' de bir kullanıcı hesabı.

## <a name="assigning-users-to-ipass-smartconnect"></a>Kullanıcıları IPASS SmartConnect 'e atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların IPASS SmartConnect 'e erişmesi gerektiğine karar vermeniz gerekir. Kararlandıktan sonra buradaki yönergeleri izleyerek şu kullanıcıları ve/veya grupları IPASS SmartConnect 'e atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>IPASS SmartConnect 'e Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için, tek bir Azure AD kullanıcısının IPASS SmartConnect 'e atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı IPASS SmartConnect 'e atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>Sağlama için IPASS SmartConnect kurulumu

Azure AD ile otomatik Kullanıcı sağlama için IPASS SmartConnect 'i yapılandırmadan önce, IPASS SmartConnect Yönetici konsolundan yapılandırma bilgilerini almanız gerekir:

1. IPASS SmartConnect SCıM uç noktanıza göre kimlik doğrulaması yapmak için gereken taşıyıcı belirtecini almak için, bu değer yalnızca daha sonra sağlandığı için IPASS SmartConnect 'i ilk kez ayarlamanız gerekir. 
2. Taşıyıcı belirteciniz yoksa, yeni bir tane almak için [IPASS SmartConnect destek ekibine](mailto:help@ipass.com) ulaşın.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>Galeriden IPASS SmartConnect ekleyin

Azure AD ile otomatik Kullanıcı sağlaması için IPASS SmartConnect 'i yapılandırmak için Azure AD uygulama galerisindeki IPASS SmartConnect 'i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden IPASS SmartConnect eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **IPASS SmartConnect**yazın, sonuçlar panelinde **IPASS SmartConnect** ' i seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![sonuçlar listesinde IPASS SmartConnect](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Otomatik Kullanıcı sağlamayı IPASS SmartConnect 'e yapılandırma 

Bu bölümde, Azure AD 'de Kullanıcı ve/veya grup atamalarına göre IPASS SmartConnect içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD sağlama hizmetini yapılandırma adımlarında size kılavuzluk eder.

> [!TIP]
>  Ayrıca, Bıtabiz için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi tercih edebilirsiniz. Bu, [IPASS SmartConnect çoklu oturum açma öğreticisinde](ipasssmartconnect-tutorial.md)sunulan talimatları takip edebilir. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Azure AD 'de IPASS SmartConnect için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kuruluş uygulamaları**' nı seçin ve ardından **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **IPASS SmartConnect**' i seçin.

    ![Uygulamalar listesindeki IPASS SmartConnect bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama sekmesi](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, `https://openmobile.ipass.com/moservices/scim/v1` **kiracı URL 'sini**girin. Daha önce **gizli bir belirteçte**alınan taşıyıcı belirtecini girin. Azure AD 'nin IPASS SmartConnect 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, IPASS SmartConnect hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları IPASS SmartConnect ile eşitler**' ı seçin.

    ![IPASS SmartConnect Kullanıcı eşlemeleri](media/ipass-smartconnect-provisioning-tutorial/usermapping.png)

9. **Öznitelik eşleme** bölümünde, Azure AD 'Den IPASS SmartConnect 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için IPASS SmartConnect içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![IPASS SmartConnect Kullanıcı eşlemeleri](media/ipass-smartconnect-provisioning-tutorial/userattribute.png)


10. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

11. IPASS SmartConnect için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** Içinde Istenen değerleri seçerek IPASS SmartConnect 'e sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

13. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İşlem ilerlemesini izlemek ve Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri IPASS SmartConnect üzerinde açıklayan etkinlik raporuna yönelik bağlantıları izlemek için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* IPASS SmartConnect yalnızca etki alanları IPASS SmartConnect Yönetici konsoluna kayıtlı olan kullanıcı adlarını kabul eder.  

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)
