---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Symantec Web Security hizmeti 'ni (WSS) yapılandırma | Microsoft Docs"
description: Kullanıcı hesaplarını Symantec Web Security Service 'e (WSS) otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: cfdf5487bcd4c33a21fca593fef829c58d2d099d
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576205"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Öğretici: Otomatik Kullanıcı sağlaması için Symantec Web Security hizmeti 'ni (WSS) yapılandırma

Bu öğreticinin amacı Symantec Web Security Service (WSS) ve Azure Active Directory (Azure AD) ' de gerçekleştirilecek adımları göstermek üzere Azure AD 'yi, kullanıcıları ve/veya grupları Symantec Web güvenliğine otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Hizmet (WSS).

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Symantec Web Security hizmeti (WSS) kiracısı](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Symantec Web Security hizmetinde (WSS) yönetici izinleri olan bir kullanıcı hesabı.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Symantec Web Security Service 'e (WSS) Kullanıcı atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Symantec Web Security Service (WSS) erişimine ihtiyacı olduğuna karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek Symantec Web Security Service (WSS) hizmetine bu kullanıcıları ve/veya grupları atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Symantec Web Security Service 'e (WSS) Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için Symantec Web Security Service (WSS) ' ye tek bir Azure AD kullanıcısı atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Symantec Web Security Service 'e (WSS) bir Kullanıcı atarken atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Sağlama için Symantec Web Security Service (WSS) ayarlama

Azure AD ile otomatik Kullanıcı sağlama için Symantec Web Security hizmeti 'ni (WSS) yapılandırmadan önce, Symantec Web Security Service (WSS) üzerinde SCıM sağlamasını etkinleştirmeniz gerekir.

1. [Symantec Web Security hizmeti yönetici konsolunda](https://portal.threatpulse.com/login.jsp)oturum açın. **Solutions** > **Service**'e gidin.

    ![Symantec Web güvenlik hizmeti (WSS)](media/symantec-web-security-service/service.png)

2. **Hesap bakımı** > **tümleştirmeleri** > **yeni tümleştirme**' e gidin.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  **Gruplar eşitleme & üçüncü taraf kullanıcıları**seçin. 

    ![Symantec Web Security hizmeti](media/symantec-web-security-service/third-party-users.png)

4.  **SCIM URL 'sini** ve **belirtecini**kopyalayın. Bu değerler, Azure portal Symantec Web Security Service (WSS) uygulamanızın sağlama sekmesinde bulunan **kiracı URL 'si** ve **gizli dizi belirteci** alanına girilecektir.

    ![Symantec Web Security hizmeti](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Galeriden Symantec Web Security Service (WSS) ekleme

Azure AD ile otomatik Kullanıcı sağlama için Symantec Web Security hizmeti 'ni (WSS) yapılandırmak üzere, Azure AD uygulama galerisindeki Symantec Web Security Service (WSS) öğesini yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden Symantec Web Security hizmeti (WSS) eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Symantec Web Security Service**yazın, sonuçlar panelinde **Symantec Web Security hizmeti** ' ni seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

    ![Sonuç listesinde Symantec Web Security Service (WSS)](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Symantec Web Security Service (WSS) için otomatik Kullanıcı sağlamayı yapılandırma

Bu bölümde, Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan Symantec Web Security Service (WSS) içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında kılavuzluk eder.

> [!TIP]
> Symantec Web Security Service [(WSS) çoklu oturum açma öğreticisinde](symantec-tutorial.md)sunulan yönergeleri Izleyerek Symantec Web Security HIZMETI (WSS) için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini tamamlayabilse de.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Azure AD 'de Symantec Web Security Service (WSS) için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kuruluş uygulamaları**' nı seçin ve ardından **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Symantec Web Security Service**' i seçin.

    ![Uygulamalar listesinde Symantec Web Security Service (WSS) bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama sekmesi](common/provisioning-automatic.png)

5. Yönetici kimlik bilgileri bölümünde, sırasıyla **KIRACı URL** 'Si ve **gizli belirteç** ' de bulunan **SCIM URL 'sini** ve **belirteç** değerlerini girin. Azure AD 'nin Symantec Web Security hizmetine bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Symantec Web Security Service (WSS) hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Symantec Web SECURITY Service (WSS) ile eşitler**' ı seçin.

    ![Symantec Web Security hizmeti (WSS) Kullanıcı eşlemeleri](media/symantec-web-security-service/usermapping.png)

9. **Öznitelik eşleme** bölümünde Azure AD 'Den Symantec Web Security SERVICE (WSS) ile eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Symantec Web Security SERVICE (WSS) içindeki kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Symantec Web Security hizmeti (WSS) Kullanıcı eşlemeleri](media/symantec-web-security-service/userattribute.png)

10. **Eşlemeler** bölümünde, **Azure Active Directory gruplarını Symantec Web Security hizmeti olarak eşitler**' ı seçin.

    ![Symantec Web Security hizmeti (WSS) Kullanıcı eşlemeleri](media/symantec-web-security-service/groupmapping.png)

11. **Öznitelik eşleme** bölümünde Azure AD 'Den Symantec Web Security SERVICE (WSS) ile eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Symantec Web Security SERVICE (WSS) gruplarındaki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Symantec Web Security hizmeti (WSS) Kullanıcı eşlemeleri](media/symantec-web-security-service/groupattribute.png)

12. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

13. Symantec Web Security hizmeti için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içinde Istenen değerleri seçerek Symantec Web SECURITY hizmeti 'ne (WSS) sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

15. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin sonraki eşitlemeler daha uzun sürer. Kullanıcıların ve/veya grupların sağlaması için ne kadar süreceğine ilişkin daha fazla bilgi için bkz. [kullanıcıları sağlamak için ne kadar sürer](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

İlerleme durumunu izlemek için **geçerli durum** bölümünü kullanabilir ve Symantec Web Security SERVICE (WSS) ÜZERINDE Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan sağlama etkinliği raporunuzun bağlantılarını izleyebilirsiniz. Daha fazla bilgi için bkz. [Kullanıcı hazırlama durumunu denetleme](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Azure AD sağlama günlüklerini okumak için bkz. [Otomatik Kullanıcı hesabı sağlama hakkında raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)
