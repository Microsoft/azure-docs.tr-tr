---
title: 'Öğretici: Azure Active Directory ile otomatik kullanıcı hazırlama için Samanage yapılandırma | Microsoft Docs'
description: Otomatik olarak sağlamak ve kullanıcı hesaplarına Samanage sağlamasını kaldırmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67cfe5a26740837508ea3a3e76295a896c3cc107
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670916"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Öğretici: Otomatik kullanıcı hazırlama için Samanage yapılandırın

Bu öğreticide, otomatik olarak sağlamak ve kullanıcıların ve grupların Samanage sağlamasını kaldırmak için Samanage ve Azure Active Directory'de (Azure AD) Azure AD'ye yapılandırmak için gerçekleştirme adımları gösterilmektedir.

> [!NOTE]
> Bu öğreticide, Azure AD kullanıcı sağlama hizmeti üzerinde oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmet yapar, nasıl çalıştığını ve sık sorulan sorular hakkında daha fazla bilgi için bkz: [kullanıcı sağlamayı ve Azure Active Directory ile hizmet olarak yazılım-a-(SaaS) uygulamalarına sağlama kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, olduğunu varsayar:

* Azure AD kiracısı.
* A [Samanage Kiracı](https://www.samanage.com/pricing/) profesyonel paket.
* Samanage yönetici izinlerine sahip bir kullanıcı hesabı.

> [!NOTE]
> Azure AD tümleştirmesi sağlama dayanan [Samanage Rest API](https://www.samanage.com/api/). Bu API Samanage geliştiriciler profesyonel paket sahip hesaplar için kullanılabilir.

## <a name="add-samanage-from-the-azure-marketplace"></a>Azure Market'ten Samanage Ekle

Otomatik kullanıcı hazırlama ile Azure AD için Samanage yapılandırmadan önce Samanage Azure Market'te yönetilen SaaS uygulamalarının listenize ekleyin.

Marketten Samanage eklemek için aşağıdaki adımları izleyin.

1. İçinde [Azure portalında](https://portal.azure.com), sol taraftaki gezinti bölmesinde seçin **Azure Active Directory**.

    ![Azure Active Directory simgesi](common/select-azuread.png)

2. Git **kurumsal uygulamalar**ve ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için seçin **yeni uygulama** iletişim kutusunun üst.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Samanage** seçip **Samanage** sonucu panelinden. Uygulama eklemek için seçin **Ekle**.

    ![Sonuç listesinde Samanage](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Samanage için kullanıcı atama

Azure Active Directory kullanan adlı bir kavram *atamaları* hangi kullanıcıların seçilen uygulamalara erişimi alması belirlemek için. Otomatik kullanıcı hazırlama bağlamında, yalnızca kullanıcılar veya Azure AD'de bir uygulamaya atanan gruplar eşitlenir.

Yapılandırma ve otomatik kullanıcı sağlamayı etkinleştirmek için önce hangi kullanıcıların veya grupların Azure AD'de Samanage erişmesi karar verin. Bu kullanıcılar veya gruplar için Samanage atamak için yönergeleri izleyin. [kurumsal bir uygulamayı kullanıcı veya grup atama](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Kullanıcılar için Samanage atamak için önemli ipuçları

*    Bugün, Samanage rolleri Azure portalı kullanıcı arabirimini otomatik olarak ve dinamik olarak doldurulur. Kullanıcılara Samanage rolleri atamadan önce bir ilk eşitleme Samanage kiracınızda bulunan en son rollerini almak üzere Samanage karşı tamamlanır emin olun.

*    Tek bir atamanızı öneririz Samanage ilk otomatik kullanıcı sağlama yapılandırmasını test etmek için Azure AD kullanıcısı. Testler başarılı olduktan sonra ek kullanıcılar ve gruplar daha sonra atayabilirsiniz.

*    Bir kullanıcı için Samanage atadığınızda, geçerli herhangi bir uygulamaya özgü rol ataması iletişim kutusunda varsa, seçin. Kullanıcılarla **varsayılan erişim** rol sağlamasından dışlanır.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Samanage için otomatik kullanıcı sağlamayı yapılandırma

Bu bölümde Azure AD sağlama hizmeti yapılandırma adımlarında size kılavuzluk eder. Oluşturma, güncelleştirme ve kullanıcılar veya gruplar, Azure AD'de kullanıcı veya grup atamaları temel alınarak Samanage devre dışı bırakmak için kullanın.

> [!TIP]
> SAML tabanlı çoklu oturum açma Samanage için etkinleştirebilirsiniz. Bölümündeki yönergeleri [oturum açma Samanage tek öğretici](samanage-tutorial.md). Bu iki özellik birbirini tamamlar ancak otomatik kullanıcı hazırlama bağımsız olarak, çoklu oturum açma yapılandırılabilir.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Azure AD'de Samanage için otomatik kullanıcı sağlamayı yapılandırma

1. [Azure Portal](https://portal.azure.com) oturum açın. Seçin **kurumsal uygulamalar** > **tüm uygulamaları** > **Samanage**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Samanage**.

    ![Uygulamalar listesinde Samanage bağlantı](common/all-applications.png)

3. Seçin **sağlama** sekmesi.

    ![Samanage sağlama](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Ayarlama **hazırlama modu** için **otomatik**.

    ![Samanage sağlama modu](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. Altında **yönetici kimlik bilgileri** bölümünde, yönetici kullanıcı adı ve Samanage hesabınızın yönetici parolasını girin. Bu değerleri örnekleri şunlardır:

   * İçinde **yönetici kullanıcı adı** kutusunda, yönetici hesabı Samanage kiracınıza kullanıcı adı girin. admin@contoso.com bunun bir örneğidir.

   * İçinde **yönetici parolası** kutusunda, yönetici kullanıcı adı için karşılık gelen yönetici hesabının parolasını girin.

6. Adım 5'te gösterilen kutuları doldurduktan sonra seçin **Test Bağlantısı** Azure'un emin olmak için AD için Samanage bağlanabilirsiniz. Bağlantı başarısız olursa Samanage hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Samanage Bağlantıyı Sına](./media/samanage-provisioning-tutorial/TestConnection.png)

7. İçinde **bildirim e-posta** kutusunda kişinin e-posta adresi girin veya sağlama hata bildirimleri almak için Grup. Seçin **bir hata oluştuğunda e-posta bildirimi gönder** onay kutusu.

    ![Samanage bildirim e-postası](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. **Kaydet**’i seçin.

9. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory Kullanıcıları Samanage**.

    ![Samanage kullanıcı eşitleme](./media/samanage-provisioning-tutorial/UserMappings.png)

10. İçinde Samanage için Azure AD'den eşitlenen kullanıcı özniteliklerini gözden **öznitelik eşlemelerini** bölümü. Seçilen öznitelikler **eşleşen** özellikleri Samanage kullanıcı hesaplarını güncelleştirme işlemleri eşleştirmek için kullanılır. Değişiklikleri kaydetmek için seçmeniz **Kaydet**.

    ![Samanage eşleşen kullanıcı öznitelikleri](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Grup eşlemelerini altında etkinleştirmek için **eşlemeleri** bölümünden **eşitleme Azure Active Directory gruplarına Samanage**.

    ![Samanage Grup eşitleme](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Ayarlama **etkin** için **Evet** grupları eşitlenecek. İçinde Samanage için Azure AD'den eşitlenen grup öznitelikleri gözden **öznitelik eşlemelerini** bölümü. Seçilen öznitelikler **eşleşen** özellikleri Samanage kullanıcı hesaplarını güncelleştirme işlemleri eşleştirmek için kullanılır. Değişiklikleri kaydetmek için seçmeniz **Kaydet**.

    ![Samanage eşleşen grup öznitelikleri](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Kapsam belirleme filtrelerini yapılandırmak için yönergeleri izleyin. [kapsam belirleme filtresi öğretici](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Azure AD içinde sağlama hizmeti için Samanage, etkinleştirmek için **ayarları** bölümünde **sağlama durumu** için **üzerinde**.

    ![Samanage sağlama durumu](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Samanage sağlamak için kullanıcıları veya istediğiniz grupları tanımlayın. İçinde **ayarları** bölümünde, istediğiniz değerleri seçin **kapsam**. Seçtiğinizde, **tüm kullanıcıları ve grupları eşitleme** seçeneğinde, aşağıdaki "Bağlayıcı sınırlamaları" bölümünde açıklandığı gibi sınırlamaları göz önünde bulundurun

    ![Samanage kapsamı](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Sağlama için hazır olduğunuzda **Kaydet**.

    ![Samanage Kaydet](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Bu işlem, tüm kullanıcıların ilk eşitleme başlar veya tanımlı gruplar **kapsam** içinde **ayarları** bölümü. İlk eşitleme daha sonraki eşitlemeler gerçekleştirmek için daha uzun sürer. Azure AD sağlama hizmeti çalıştırdığı sürece, yaklaşık 40 dakikada oluşur. 

Kullanabileceğiniz **eşitleme ayrıntıları** bölüm ilerlemeyi izlemek ve sağlama etkinliği raporunu için bağlantıları izleyin. Rapor hizmette Samanage sağlama Azure AD tarafından gerçekleştirilen tüm eylemler açıklanmaktadır.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz: [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

Seçerseniz **tüm kullanıcıları ve grupları eşitleme** seçenek ve değerini yapılandırmak için Samanage **rolleri** özniteliği, değerin altında **null ise varsayılan değer (isteğe bağlıdır)** kutusu şu biçimde ifade edilmesi gerekir:

- {"displayName": "rolü"}, rol istediğiniz varsayılan değerdir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kullanıcı, kurumsal uygulamalar için hesabı hazırlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri gözden geçirin ve sağlama etkinliği raporları alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
