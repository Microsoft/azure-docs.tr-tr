---
title: 'Öğretici: Azure Active Directory için Federasyon dizinini otomatik Kullanıcı sağlaması için yapılandırın | Microsoft Docs'
description: Federasyon dizinine kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: bc19a64fc72fef6407416f65cdc3b60a4c4f9464
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296081"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Öğretici: Otomatik Kullanıcı sağlaması için Federasyon dizinini yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları Federasyon dizinine otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Federasyon dizini ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
>  Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Azure AD kiracısı.
* [Federasyon dizini](https://www.federated.directory/pricing).
* Federasyon dizininde yönetici izinlerine sahip bir kullanıcı hesabı.

## <a name="assign-users-to-federated-directory"></a>Federasyon dizinine kullanıcı atama
Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen atama adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Federasyon dizinine erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Federasyon dizinine atayabilirsiniz:

 * [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Federasyon dizinine Kullanıcı atamaya yönelik önemli ipuçları
 * Otomatik Kullanıcı sağlama yapılandırmasını test etmek için Federasyon dizinine tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Federasyon dizinine bir Kullanıcı atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. Varsayılan erişim rolüne sahip kullanıcılar, sağlanmasından çıkarılır.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Federasyon dizinini sağlama için ayarlama

Federasyon dizinini Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmadan önce, Federasyon dizininde SCıM sağlamasını etkinleştirmeniz gerekir.

1. [Federasyon Dizin Yöneticisi konsolunuza](https://federated.directory/of) oturum açın

    ![Federasyon Dizin öğreticisi](media/federated-directory-provisioning-tutorial/companyname.png)

2. **Dizinler > Kullanıcı dizinleri** ' ne gidin ve kiracınızı seçin. 

    ![Federasyon dizini](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Kalıcı bir taşıyıcı belirteci oluşturmak için **Dizin anahtarları > yeni anahtar oluştur** ' a gidin. 

    ![Federasyon dizini](media/federated-directory-provisioning-tutorial/federated01.png)

4. Bir dizin anahtarı oluşturun. 

    ![Federasyon dizini](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. **Erişim belirteci** değerini kopyalayın. Bu değer, Azure portal Federasyon Dizin uygulamanızın sağlama sekmesinde bulunan **gizli dizi belirteci** alanına girilecektir. 

    ![Federasyon dizini](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Galeriden Federasyon dizini ekleme

Azure AD ile otomatik Kullanıcı sağlaması için Federasyon dizinini yapılandırmak üzere, Azure AD uygulama galerisindeki Federasyon dizinini yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden federe dizin eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **federe Dizin**girin, sonuçlar panelinde **federe Dizin** ' i seçin.

    ![Sonuç listesinde Federasyon dizini](common/search-new-app.png)

5. Aşağıda vurgulanan **URL** 'yi ayrı bir tarayıcıda gezin. 

    ![Federasyon dizini](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. **Oturum aç**' a tıklayın.

    ![Federasyon dizini](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Federasyon dizini bir Openıdconnect uygulaması olduğundan, Federasyon dizininde Microsoft iş hesabınızı kullanarak oturum açmayı seçin.
    
    ![Federasyon dizini](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Başarılı bir kimlik doğrulamasından sonra, onay sayfasının onay isteğini kabul edin. Uygulama daha sonra kiracınıza otomatik olarak eklenir ve Federasyon Dizin hesabınıza yönlendirilirsiniz.

    ![Federasyon dizini SCıM Ekle](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Federasyon dizinine otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni kullanarak Federasyon dizininde bulunan kullanıcıları ve/veya grupları Azure AD 'de Kullanıcı ve/veya grup atamalarına göre oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Azure AD 'de Federasyon dizini için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kuruluş uygulamaları**' nı seçin ve ardından **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Federasyon dizini**' ni seçin.

    ![Uygulamalar listesindeki Federasyon dizini bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama sekmesi](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, kiracı `https://api.federated.directory/v2/` URL 'sini girin. Daha önce aldığınız ve daha önce Federasyon dizininden **gizli bir belirteçte**kaydettiğiniz değeri girin. Azure AD 'nin Federasyon dizinine bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Federasyon Dizin hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

8. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

9. **Kaydet**’e tıklayın.

10. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Federasyon dizini olarak eşitler**' ı seçin.

    ![Federasyon Dizin öğreticisi](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. **Öznitelik eşleme** bölümünde Azure AD 'Den Federasyon dizinine eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşlenen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Federasyon dizinindeki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Federasyon Dizin öğreticisi](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

13. Federasyon dizini için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek Federasyon dizinine sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

15. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerlemeyi izlemek ve Federasyon dizininde Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinliği raporuna ilişkin bağlantıları izlemek için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../manage-apps/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)
