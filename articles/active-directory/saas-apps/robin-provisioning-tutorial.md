---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için bir kez deneme yapılandırın | Microsoft Docs'
description: Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 178ca5b3-4155-43ab-84f5-650d25c5a74a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2019
ms.author: Zhchia
ms.openlocfilehash: a3c65fcf224134d816f2bb15cdd9a6b82ec4bd02
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106157"
---
# <a name="tutorial-configure-robin-for-automatic-user-provisioning"></a>Öğretici: Otomatik Kullanıcı sağlama için bir kez deneme yapılandırın

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları bir kez deneme için otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak üzere bir kez deneme ve Azure Active Directory (Azure AD) olarak gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Bir deneme kiracısı](https://robinpowered.com/pricing/)
* Yönetici izinlerine sahip bir kullanıcı hesabı.

## <a name="assigning-users-to-robin"></a>Kullanıcıları bir kez deneme için atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların bir kez deneme için erişim ihtiyacı olduğuna karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları bir kez deneme için atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-robin"></a>Kullanıcıları bir kez deneme için atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için tek bir Azure AD kullanıcısının bir kez deneme için atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı bir kez deneme için atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-robin-for-provisioning"></a>Sağlama için bir kez deneme ayarlama

1. Bir [deneme yönetim konsolunda](https://dashboard.robinpowered.com/login)oturum açın. Manage **> tümleştirmelerini yönetme > SCıM >** Yönet ' e gidin.

    ![bir kez desteklenen Yönetici Konsolu](media/robin-provisioning-tutorial/robin-admin.png)

2.  Yeni bir kuruluş belirteci oluştur. Bu belirteci kaybederseniz, mevcut kullanıcıları etkilemeden her zaman yeni bir tane yapabilirsiniz.

    ![deneme destekli bir SCıM Ekle](media/robin-provisioning-tutorial/robin-token.png)

3.  **SCIM kimlik doğrulama belirtecini**kopyalayın. Bu değer, Azure portal deneme uygulamanızın sağlama sekmesindeki gizli belirteç alanına girilir.



## <a name="add-robin-from-the-gallery"></a>Galeriden bir kez deneme ekleyin

Azure AD ile otomatik Kullanıcı sağlama için bir kez deneme yapılandırmadan önce Azure AD uygulama galerisindeki yönetilen SaaS uygulamaları listenize bir deneme sürümü eklemeniz gerekir.

**Azure AD uygulama galerisinden bir kez deneme eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Ara kutusuna bir **kez deneme**yazın, sonuçlar panelinde bir **kez deneme** ' yı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde bir kez deneme](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-robin"></a>Otomatik Kullanıcı sağlamayı bir kez deneme için yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak Kullanıcı ve/veya grupları Azure AD 'de Kullanıcı ve/veya Grup atamaları temelinde bir kez oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımları adım adım kılavuzluk eder.

> [!TIP]
> Ayrıca, [Çoklu oturum açma öğreticisinde](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial)sunulan yönergeleri Izleyerek, tek deneme için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilse de

### <a name="to-configure-automatic-user-provisioning-for-robin-in-azure-ad"></a>Azure AD 'de otomatik Kullanıcı sağlamayı bir kez deneme için yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kuruluş uygulamaları**' nı seçin ve ardından **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, bir **kez deneme**' yı seçin.

    ![Uygulamalar listesindeki bir kez güçlendirilmiş bağlantı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama sekmesi](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, `https://api.robinpowered.com/v1.0/scim-2` **kiracı URL 'sini**girin. **Gizli belirteçte**daha önce alınan **SCIM kimlik doğrulama belirteci** değerini girin. Azure AD 'nin bir kez deneme için bağlanabilmesi için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, deneme hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory kullanıcıları bir kez deneme olacak şekilde eşitler**' ı seçin.

    ![bir kez desteklenen Kullanıcı eşlemeleri](media/robin-provisioning-tutorial/robin-user-mapping.png)

9. Azure AD 'den eşitlenen Kullanıcı özniteliklerini **öznitelik eşleme** bölümünde bir kez deneme için gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için deneme sürümündeki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![bir kez desteklenen Kullanıcı öznitelikleri](media/robin-provisioning-tutorial/robin-user-attribute-mapping.png)

10. **Eşlemeler** bölümünde **Azure Active Directory gruplarını bir kez deneme olacak şekilde eşitler**' ı seçin.

    ![bir kez desteklenen Grup eşlemeleri](media/robin-provisioning-tutorial/robin-group-mapping.png)

11. Azure AD 'den eşitlenen grup özniteliklerini **öznitelik eşleme** bölümünde bir kez deneme için gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme işlemleri için, grupları bir kez deneme halinde eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![bir kez desteklenen Grup öznitelikleri](media/robin-provisioning-tutorial/robin-group-attribute-mapping.png)

12. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

13. Azure AD sağlama hizmetini bir kez deneme için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** içindeki istenen değerleri seçerek, yeniden deneme için sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

15. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilir ve Azure AD sağlama hizmeti tarafından tek bir kez gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinlik raporuna yönelik bağlantıları izleyebilirsiniz.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).



## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)

