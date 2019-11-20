---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Brivo onair Identity bağlayıcısını yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını Brivo onair Identity bağlayıcısını otomatik olarak sağlamak ve yeniden sağlamak üzere Azure Active Directory nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 542ce04c-ef7d-4154-9b0e-7f68e1154f6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: c2db09d48f9efc22134f930b367ef16eda62ac6f
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174518"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Brivo onair Identity bağlayıcısını yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, Kullanıcı ve/veya grupları Brivo onair Identity Connector 'a otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Brivo onair Identity Bağlayıcısı ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Brivo onair Identity bağlayıcı kiracısı](https://www.brivo.com/quote)
* Üst düzey yönetici izinleriyle Brivo onair Identity bağlayıcısında bir kullanıcı hesabı.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Kullanıcıları Brivo onair Identity Connector 'a atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Brivo onair Identity Connector 'a erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Brivo onair Identity Connector 'a atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Brivo onair Identity Connector 'a Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için Brivo onair Identity bağlayıcısına tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Brivo onair Identity bağlayıcısına atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Sağlama için Brivo onair Identity bağlayıcısını ayarlayın

1.  [Brivo ONAIR Identity Connector yönetici konsolunda](https://acs.brivo.com/login/)oturum açın. Hesap **> Hesap ayarları**' na gidin.

    ![Brivo onair Identity Connector Yönetici Konsolu](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2.  **Azure AD** sekmesine tıklayın. **Azure AD** ayrıntıları sayfasında, üst düzey yönetici hesabınızın parolasını yeniden girin. **Gönder**' e tıklayın.

    ![Brivo onair Identity Bağlayıcısı Azure](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3.  **Belirteç Kopyala** düğmesine tıklayın ve **gizli belirteç**kaydedin. Bu değer, Azure portal Brivo onair Identity bağlayıcı uygulamanızın sağlama sekmesindeki gizli belirteç alanına girilir.

    ![Brivo onair Identity bağlayıcı belirteci](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Galeriden Brivo onair Identity bağlayıcısını ekleyin

Azure AD ile otomatik Kullanıcı sağlaması için Brivo onair Identity bağlayıcısını yapılandırmadan önce Azure AD uygulama galerisindeki Brivo onair Identity bağlayıcısını yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden Brivo onair Identity Bağlayıcısı eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Brivo ONAIR Identity Connector**girin, sonuçlar panelinde **Brivo onair Identity Connector** ' ı seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde Brivo onair Identity Bağlayıcısı](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Brivo onair Identity Connector 'a otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan Brivo onair Identity Bağlayıcısı 'nda kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımları adım adım kılavuzluk eder.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Azure AD 'de Brivo onair Identity bağlayıcısının otomatik Kullanıcı sağlamasını yapılandırmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın. **Kuruluş uygulamaları**' nı seçin ve ardından **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Brivo onair Identity Bağlayıcısı**' nı seçin.

    ![Uygulamalar listesindeki Brivo onair Identity bağlayıcı bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama sekmesi](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, **kiracı URL 'sindeki**`https://scim.brivo.com/ActiveDirectory/v2/` girin. **Gizli belirteçte**daha önce alınan **SCIM kimlik doğrulama belirteci** değerini girin. Azure AD 'nin Brivo onair Identity bağlayıcısına bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Brivo onair Identity bağlayıcı hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

7. **Save (Kaydet)** düğmesine tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Brivo onair Identity Connector**' a eşitler ' ı seçin.

    ![Brivo onair Identity Bağlayıcısı Kullanıcı eşlemeleri](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. **Öznitelik eşleme** bölümünde Azure AD 'Den Brivo onair Identity Connector 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Brivo ONAIR Identity Connector 'daki Kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Brivo onair Identity Bağlayıcısı Kullanıcı öznitelikleri](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. **Eşlemeler** bölümünde **Azure Active Directory gruplarını Brivo onair Identity Connector olarak eşitler**' ı seçin.

    ![Brivo onair Identity bağlayıcı grubu eşlemeleri](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. **Öznitelik eşleme** bölümünde Azure AD 'Den Brivo onair Identity Connector ile eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Brivo ONAIR Identity Connector 'daki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Brivo onair Identity bağlayıcı grubu öznitelikleri](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

13. Brivo onair Identity Connector için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsamda** Istenen değerleri seçerek Brivo onair Identity bağlayıcısına sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

15. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerlemeyi izlemek için **eşitleme ayrıntıları** bölümünü ve Brivo onair Identity Connector ÜZERINDE Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinlik raporuna yönelik bağlantıları takip edebilirsiniz.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)

