---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için primi yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını otomatik olarak sağlamak ve yeniden sağlamak üzere Azure Active Directory nasıl yapılandırılacağını öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 692eff26dbbc2289ec589a327dbe58958b414a56
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842734"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Öğretici: Otomatik Kullanıcı sağlaması için primi yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, kullanıcıları ve/veya grupları otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Bonor ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında önemli ayrıntılar için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, zaten aşağıdakilere sahip olduğunuzu varsayar:

* Bir Azure AD kiracısı
* [Primi kiracı](https://bonus.ly/pricing)
* Yönetici izinlerine sahip olan bir kullanıcı hesabı

> [!NOTE]
> Azure AD sağlama tümleştirmesi, pribilen geliştiriciler tarafından kullanılabilen [primi REST API](https://konghq.com/solutions/gateway/)'sine dayanır.

## <a name="adding-bonusly-from-the-gallery"></a>Galeriden primi ekleme

Azure AD ile otomatik Kullanıcı sağlama için priliksiz yapılandırmadan önce Azure AD uygulama galerisindeki yönetilen SaaS uygulamaları listenize primi eklemeniz gerekir.

**Azure AD uygulama galerisinden primi eklemek için aşağıdaki adımları uygulayın:**

1. İçinde **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde tıklayın **Azure Active Directory** simgesi.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için tıklatın **yeni uygulama** iletişim üst kısmındaki düğmesi.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **bonikisi**yazın, sonuç panelinden **primi** ' yi seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

    ![Sonuçlar listesinde](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Kullanıcıları primi atama

Azure Active Directory, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyebilmek için "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya "atanmış" olan kullanıcılar ve/veya gruplar eşitlenir. 

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Bonor 'a erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları primi atayabilirsiniz:

* [Kurumsal uygulamaya Kullanıcı veya Grup atama](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Kullanıcıları Priye atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için tek bir Azure AD kullanıcısının Pride bir şekilde atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Pril 'e atarken atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Otomatik Kullanıcı sağlamayı primi olarak yapılandırma

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak Kullanıcı ve/veya grupları Azure AD 'de Kullanıcı ve/veya Grup atamaları temelinde oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

> [!TIP]
> Ayrıca, primi [Çoklu oturum açma öğreticisinde](bonus-tutorial.md)sunulan yönergeleri izleyerek, her bononun için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Azure AD 'de otomatik Kullanıcı sağlamasını yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin, sonra da **primi**' yi seçin

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **primi**' yi seçin.

    ![Uygulamalar listesindeki primi bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Primi sağlama](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Primi sağlama](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. **Yönetici kimlik bilgileri** bölümünde, adım 6 ' da açıklandığı gibi, primi hesabınızın **gizli belirtecini** girin.

    ![Primi sağlama](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. Primi hesabınız için **gizli anahtar** , **yönetici > Şirket > tümleştirmelerle**bulunur. **Kod eklemek** istiyorsanız, yeni bir gizli belirteç oluşturmak için **API > yeni API erişim belirteci oluştur** ' a tıklayın.

    ![Primi sağlama](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Primi sağlama](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Primi sağlama](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. Aşağıdaki ekranda, erişim belirteci için, girilen metin kutusuna bir ad yazın ve ardından **API anahtarı oluştur**' a basın. Yeni erişim belirteci açılan pencerede birkaç saniye görünür.

    ![Primi sağlama](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Primi sağlama](./media/bonusly-provisioning-tutorial/Token02.png)

8. 5\. adımda gösterilen alanları doldurulmaya yönelik olarak, Azure AD 'nin primi 'ya bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, primi hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Primi sağlama](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin ve **bir hata oluştuğunda e-posta bildirimi gönder**onay kutusunu işaretleyin.

    ![Primi sağlama](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. **Kaydet**’e tıklayın.

11. **Eşlemeler** bölümünde, **kullanıcıları Azure Active Directory eşitler**' ı seçin.

    ![Primi sağlama](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. **Öznitelik eşleme** bölümünde Azure AD 'den pribir şekilde eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için primi 'daki Kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Primi sağlama](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Kapsam filtrelerini yapılandırmak için, [kapsam filtresi öğreticisinde](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)sunulan aşağıdaki yönergelere bakın.

14. Azure AD sağlama hizmetini primi için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Primi sağlama](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. **Ayarlar** bölümünde **kapsam** içinde istenen değerleri seçerek, sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Primi sağlama](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Sağlamaya hazırsanız **Kaydet**' e tıklayın.

    ![Primi sağlama](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. İlerleme durumunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilir ve Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm işlemleri ayrıntılarıyla açıklayan etkinlik raporuna yönelik bağlantıları izleyebilirsiniz.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
