---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Visitly yapılandırma | Microsoft Docs'
description: Visitly 'ye Kullanıcı hesaplarını otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
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
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 942f0aa685ff7e2278aae159f7e97917a105f5fa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840157"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için Visitly yapılandırma

Bu öğreticinin amacı, Visitly ve Azure Active Directory (Azure AD) ' de gerçekleştirdiğiniz adımları göstermek için Azure AD 'yi otomatik olarak sağlamak ve Visitly 'e Kullanıcı veya grupları sağlamak üzere yapılandırmak için kullanabilirsiniz.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için, [Azure Active Directory ile hizmet olarak yazılım (SaaS) uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md)konusuna bakın.
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisites

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Bir Visitly kiracısı](https://www.visitly.io/pricing/)
* Yönetici izinlerine sahip Visitly 'de bir kullanıcı hesabı

## <a name="assign-users-to-visitly"></a>Visitly 'e Kullanıcı atama 

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanan kullanıcılar veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların veya grupların Visitly 'e erişmesi gerektiğine karar verin. Ardından buradaki yönergeleri izleyerek bu kullanıcıları veya grupları Visitly ' ye atayın:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Visitly 'e Kullanıcı atamaya yönelik önemli ipuçları 

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için Visitly 'e tek bir Azure AD kullanıcısı atamanızı öneririz. Ek kullanıcılar veya gruplar daha sonra atanabilir.

* Visitly 'e bir Kullanıcı atadığınızda, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. Varsayılan erişim rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-visitly-for-provisioning"></a>Sağlama için Visitly ayarlama

Visitly 'yi Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmadan önce, Visitly üzerinde etki alanları arası kimlik yönetimi (SCıM) sağlaması için sistemi etkinleştirmeniz gerekir.

1. [Visitly](https://app.visitly.io/login)'de oturum açın. @No__t **tümleştirme**seçin-1**konak eşitleme**.

    ![Konak eşitleme](media/Visitly-provisioning-tutorial/login.png)

2. **Azure AD** bölümünü seçin.

    ![Azure AD bölümü](media/Visitly-provisioning-tutorial/integration.png)

3. **API anahtarını**kopyalayın. Bu değerler, Azure portal Visitly uygulamanızın **sağlama** sekmesindeki **gizli belirteç** kutusuna girilir.

    ![API anahtarı](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Galeriden Visitly ekleme

Visitly 'i Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmak üzere, Azure AD Uygulama Galerisi 'nden yönetilen SaaS uygulamaları listenize Visitly ekleyin.

Azure AD Uygulama Galerisi 'nden Visitly eklemek için aşağıdaki adımları izleyin.

1. [Azure Portal](https://portal.azure.com), sol gezinti bölmesinde **Azure Active Directory**' ı seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Visitly**yazın, sonuçlar panelinde **Visitly** ' yi seçin ve ardından uygulamayı eklemek için **Ekle** ' yi seçin.

    ![Sonuç listesinde Visitly](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Visitly 'e otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı veya grup atamalarını temel alarak Visitly içinde kullanıcıları veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında size kılavuzluk eder.

> [!TIP]
> Visitly için SAML tabanlı çoklu oturum açmayı etkinleştirmek için, [Visitly çoklu oturum açma öğreticisindeki](Visitly-tutorial.md)yönergeleri izleyin. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini tamamlayabilse de.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Azure AD 'de Visitly için otomatik Kullanıcı sağlamayı yapılandırma

1. [Azure Portal](https://portal.azure.com)oturum açın. **Tüm uygulamalar**@no__t **Kurumsal uygulamalar**' ı seçin.

    ![Tüm uygulamalar](common/enterprise-applications.png)

2. Uygulamalar listesinde **Visitly**' yi seçin.

    ![Uygulamalar listesindeki Visitly bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama modu otomatik olarak ayarlandı](common/provisioning-automatic.png)

5. Yönetici kimlik bilgileri bölümünde, sırasıyla **kiracı URL 'si** ve **gizli belirteç**' de alınan `https://api.visitly.io/v1/usersync/SCIM` ve **API anahtarı** değerlerini girin. Azure AD 'nin Visitly 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, Visitly hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** kutusunda, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin. **Bir hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

7. **Kaydet**' i seçin.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Visitly olarak eşitler**' ı seçin.

    ![Visitly Kullanıcı eşlemeleri](media/visitly-provisioning-tutorial/usermapping.png)

9. **Öznitelik eşlemeleri** bölümünde Azure AD 'den Visitly 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için Visitly içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** ' i seçin.

    ![Visitly Kullanıcı öznitelikleri](media/visitly-provisioning-tutorial/userattribute.png)

10. Kapsam filtrelerini yapılandırmak için [kapsam filtresi öğreticisindeki](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)yönergeleri izleyin.

11. Visitly için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** Içindeki istenen değerleri seçerek Visitly için sağlamak istediğiniz kullanıcıları veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

13. Sağlamaya hazırsanız **Kaydet**' i seçin.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsamda** tanımlanan tüm kullanıcıların veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin sonraki eşitlemeler daha uzun sürer. Kullanıcıların veya grupların sağlaması için ne kadar süreceği hakkında daha fazla bilgi için bkz. [Kullanıcı sağlama ne kadar sürer?](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

İlerlemeyi izlemek ve Visitly üzerinde Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan sağlama etkinliği raporunuzun bağlantılarını izlemek için **geçerli durum** bölümünü kullanabilirsiniz. Daha fazla bilgi için bkz. [Kullanıcı hazırlama durumunu denetleme](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Azure AD sağlama günlüklerini okumak için bkz. [Otomatik Kullanıcı hesabı sağlama hakkında raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

Visitly, sabit silmeleri desteklemez. Her şey yalnızca geçici silme amaçlıdır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)
