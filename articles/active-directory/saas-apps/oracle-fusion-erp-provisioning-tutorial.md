---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Oracle Fusion ERP 'yi yapılandırma | Microsoft Docs"
description: Oracle Fusion ERP 'ye Kullanıcı hesaplarını otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
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
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 73991efa2e98ff033987f1ce172d24fe3ecddb96
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144615"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Oracle Fusion ERP 'yi yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları Oracle Fusion ERP 'ye otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Oracle Fusion ERP ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
>  Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).
>
> Bu bağlayıcı Şu anda önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Oracle FUSION ERP kiracısı](https://www.oracle.com/applications/erp/).
* Oracle Fusion ERP 'de yönetici izinleriyle bir kullanıcı hesabı.

## <a name="assign-users-to-oracle-fusion-erp"></a>Oracle Fusion ERP 'ye Kullanıcı atama 
Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen atama adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Oracle Fusion ERP 'ye erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Oracle Fusion ERP 'ye atayabilirsiniz:
 
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Oracle Fusion ERP 'ye Kullanıcı atamaya yönelik önemli ipuçları 

 * Otomatik Kullanıcı sağlama yapılandırmasını test etmek için Oracle Fusion ERP 'ye tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Oracle Fusion ERP 'ye bir Kullanıcı atarken, atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. Varsayılan erişim rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Sağlama için Oracle Fusion ERP 'yi ayarlama

Oracle Fusion ERP 'yi Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmadan önce, Oracle Fusion ERP üzerinde SCıM sağlamasını etkinleştirmeniz gerekir.

1. [Oracle FUSION ERP Yönetici konsolunuza](https://cloud.oracle.com/sign-in) oturum açın

2. Sol üst köşedeki Gezgin ' e tıklayın. **Araçlar**altında **Güvenlik Konsolu**' nu seçin.

    ![Oracle Fusion ERP SCıM Ekle](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. **Kullanıcılara**gidin.
    
    ![Oracle Fusion ERP SCıM Ekle](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. Oracle Fusion ERP Yönetici Konsolu 'nda oturum açmak için kullanacağınız Yönetici Kullanıcı hesabının kullanıcı adını ve parolasını kaydedin. Bu değerlerin, Azure portal Oracle Fusion ERP uygulamanızın sağlama sekmesinde **Yönetici Kullanıcı adı** ve **parola** alanlarına girilmesi gerekir.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Galeriden Oracle Fusion ERP ekleme

Oracle Fusion ERP 'yi Azure AD ile otomatik Kullanıcı sağlaması için yapılandırmak üzere, Azure AD uygulama galerisindeki Oracle Fusion ERP 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden Oracle Fusion ERP eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Oracle FUSION ERP**girin, sonuçlar panelinde **Oracle Fusion ERP** ' yi seçin.

    ![Sonuç listesinde Oracle Fusion ERP](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Oracle Fusion ERP için otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölümde, Azure AD sağlama hizmetini kullanarak Oracle Fusion ERP 'deki kullanıcıları ve/veya grupları Azure AD 'de Kullanıcı ve/veya grup atamalarına göre oluşturma, güncelleştirme ve devre dışı bırakma adımlarında size kılavuzluk eder.

> [!TIP]
> Oracle Fusion ERP [Çoklu oturum açma öğreticisinde](oracle-fusion-erp-tutorial.md)sunulan yönergeleri Izleyerek Oracle Fusion ERP için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini tamamlayabilse de.

> [!NOTE]
> Oracle Fusion ERP 'nin SCıM uç noktası hakkında daha fazla bilgi edinmek için bkz. [Oracle uygulamaları bulutu 'Nda ortak özellikler için REST API](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html).

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Azure AD 'de belirsizlik için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın. **Kuruluş uygulamaları**' nı seçin ve ardından **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Oracle FUSION ERP**' yi seçin.

    ![Uygulamalar listesinde Oracle Fusion ERP bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Sağlama sekmesi](common/provisioning.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Sağlama sekmesi](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünün altında, **kiracı URL 'sindeki**`https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` girin. Daha önce **yönetici** Kullanıcı **adı ve parola alanlarına alınan** yönetici kullanıcı adını ve parolasını girin. Azure AD ile Oracle Fusion ERP arasındaki **test bağlantısı** ' na tıklayın. 

    ![Oracle Fusion ERP SCıM Ekle](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim e-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory KULLANıCıLARı Oracle Fusion ERP ile eşitler**' ı seçin.

    ![Oracle Fusion ERP SCıM Ekle](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. **Öznitelik eşleme** bölümünde, Azure AD 'Den Oracle Fusion ERP 'ye eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Oracle Fusion ERP içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Oracle Fusion ERP SCıM Ekle](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. **Eşlemeler** bölümünde, **Azure Active Directory GRUPLARıNı Oracle Fusion ERP ile eşitler**' ı seçin.

    ![Oracle Fusion ERP grup eşlemeleri](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. **Öznitelik eşleme** bölümünde, Azure AD 'Den Oracle Fusion ERP 'ye eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Oracle Fusion ERP içindeki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Oracle Fusion ERP grup öznitelikleri](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

13. Oracle Fusion ERP için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumu değiştirildi](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içinde Istenen DEĞERLERI seçerek Oracle Fusion ERP 'ye sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Sağlama kapsamı](common/provisioning-scope.png)

15. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Sağlama yapılandırması kaydediliyor](common/provisioning-configuration-save.png)

    Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İşlem ilerlemesini izlemek ve Oracle Fusion ERP üzerinde Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, sağlama etkinlik raporuna yönelik bağlantıları izlemek için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

    Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Oracle Fusion ERP yalnızca SCıM uç noktası için temel kimlik doğrulamasını destekler.
* Oracle Fusion ERP, Grup sağlamayı desteklemez.
* Oracle Fusion ERP 'deki roller, Azure AD 'deki gruplarla eşleştirilir. Azure AD 'den Oracle Fusion ERP 'de kullanıcılara roller atamak için, Oracle Fusion ERP içindeki rollerden sonra adlandırılmış istenen Azure AD gruplarına kullanıcı atamanız gerekir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)
