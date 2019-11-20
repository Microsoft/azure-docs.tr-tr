---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Wrike yapılandırma | Microsoft Docs'
description: "' Nin, yazma için Kullanıcı hesaplarını otomatik olarak sağlaması ve sağlaması için Azure Active Directory nasıl yapılandırılacağını öğrenin."
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
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 46972209a8fa509ff2f17832ab8329aa3cef2548
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840321"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Wrike yapılandırma

Bu öğreticinin amacı, Azure AD 'yi otomatik olarak sağlamak ve Kullanıcı veya grupları Wrike 'ye sağlamak üzere yapılandırmak için, Wrike ve Azure Active Directory (Azure AD) ' de gerçekleştirdiğiniz adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için, [Azure Active Directory ile hizmet olarak yazılım (SaaS) uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)konusuna bakın.
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisites

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Bir Wrike kiracısı](https://www.wrike.com/price/)
* Yönetici izinleriyle yazma sırasında bir kullanıcı hesabı

## <a name="assign-users-to-wrike"></a>Kullanıcıları Wrike 'a atama
Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanan kullanıcılar veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların veya grupların Wrike 'ye erişmesi gerektiğine karar verin. Ardından buradaki yönergeleri izleyerek bu kullanıcıları veya grupları yazarak yazın:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Kullanıcıların Wrike 'a atanması için önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için, Wrike 'ye tek bir Azure AD kullanıcısı atamanız önerilir. Ek kullanıcılar veya gruplar daha sonra atanabilir.

* Bir kullanıcıyı Wrike 'a atadığınızda, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. Varsayılan erişim rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-wrike-for-provisioning"></a>Sağlama için Wrike ayarlama

Azure AD ile otomatik Kullanıcı sağlaması için Wrike 'yi yapılandırmadan önce, Wrike üzerinde etki alanları arası kimlik yönetimi (SCıM) sağlama sistemini etkinleştirmeniz gerekir.

1. [Wrike Yönetici konsolunuza](https://www.Wrike.com/login/)oturum açın. Kiracı KIMLIĞINIZE gidin. **Tümleştirmeler & uygulamalar**' ı seçin.

    ![Tümleştirme & uygulamalar](media/Wrike-provisioning-tutorial/admin.png)

2.  **Azure AD** 'ye gidip seçin.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  SCıM öğesini seçin. **Temel URL 'yi**kopyalayın.

    ![Taban URL 'SI](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. **Apı** > **Azure SCIM öğesini**seçin.

    ![Azure SCıM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Açılır pencere açılır. Hesap oluşturmak için daha önce oluşturduğunuz parolayı girin.

    ![Wrike oluşturma belirteci](media/Wrike-provisioning-tutorial/password.png)

6.  Gizli dizi **belirtecini**kopyalayın ve Azure AD 'ye yapıştırın. Yazma kurulumunu sona **kazanmak Için kaydet** ' i seçin.

    ![Kalıcı erişim belirteci](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Galeriden Wrike ekleme

Azure AD ile otomatik Kullanıcı sağlaması için Wrike yapılandırmadan önce, Azure AD uygulama galerisinden yönetilen SaaS uygulamaları listenize Wrike ekleyin.

Azure AD Uygulama Galerisi 'nden Wrike eklemek için aşağıdaki adımları izleyin.

1. [Azure Portal](https://portal.azure.com), sol gezinti bölmesinde **Azure Active Directory**' ı seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Wrike**yazın, sonuçlar panelinde **Wrike** ' ı seçin ve ardından uygulamayı eklemek için **Ekle** ' yi seçin.

    ![Sonuçlar listesinde Wrike](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Otomatik Kullanıcı sağlamayı, Wrike için yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni Azure AD 'de Kullanıcı veya grup atamalarına göre yazma işlemlerinde kullanıcıları veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımları adım adım kılavuzluk eder.

> [!TIP]
> Yazma için SAML tabanlı çoklu oturum açmayı etkinleştirmek için, [Wrike çoklu oturum açma öğreticisindeki](wrike-tutorial.md)yönergeleri izleyin. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini tamamlayabilse de.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Azure AD 'de Wrike için otomatik Kullanıcı sağlamayı yapılandırma

1. [Azure Portal](https://portal.azure.com)oturum açın. **Tüm uygulamalar**@no__t **Kurumsal uygulamalar**' ı seçin.

    ![Tüm uygulamalar](common/enterprise-applications.png)

2. Uygulamalar listesinde **Wrike**öğesini seçin.

    ![Uygulamalar listesindeki Wrike bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama modu otomatik olarak ayarlandı](common/provisioning-automatic.png)

5. Yönetici kimlik bilgileri bölümünde, sırasıyla **kiracı URL 'si** ve **gizli belirteç**içinde alınan **temel URL** 'yi ve **kalıcı erişim belirteci** değerlerini girin. Azure AD 'nin Wrike 'a bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, Wrike hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

7. **Bildirim e-postası** kutusunda, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin. **Bir hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

8. **Kaydet**' i seçin.

9. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Wrike olarak eşitler**' ı seçin.

    ![Wrike Kullanıcı eşlemeleri](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. **Öznitelik eşlemeleri** bölümünde, Azure AD 'Den Wrike 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Wrike içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** ' i seçin.

    ![Wrike Kullanıcı öznitelikleri](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Kapsam filtrelerini yapılandırmak için [kapsam filtresi öğreticisindeki](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)yönergeleri izleyin.

12. Wrike için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

13. **Ayarlar** bölümünde **kapsam** içindeki Istenen değerleri seçerek, yazma için sağlamak istediğiniz kullanıcıları veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

14. Sağlamaya hazırsanız **Kaydet**' i seçin.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsamda** tanımlanan tüm kullanıcıların veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin sonraki eşitlemeler daha uzun sürer. Kullanıcıların veya grupların sağlaması için ne kadar süreceği hakkında daha fazla bilgi için bkz. [Kullanıcı sağlama ne kadar sürer?](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

İlerleme durumunu izlemek için **geçerli durum** bölümünü kullanabilir ve Azure AD sağlama hizmeti tarafından, Wrike üzerinde gerçekleştirilen tüm eylemleri açıklayan sağlama etkinliği raporunuzun bağlantılarını izleyebilirsiniz. Daha fazla bilgi için bkz. [Kullanıcı hazırlama durumunu denetleme](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Azure AD sağlama günlüklerini okumak için bkz. [Otomatik Kullanıcı hesabı sağlama hakkında raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)
