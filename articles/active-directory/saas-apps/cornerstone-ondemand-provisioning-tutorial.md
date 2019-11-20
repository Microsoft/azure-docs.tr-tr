---
title: 'Öğretici: Azure Active Directory ile otomatik kullanıcı hazırlama için temel taşıdır OnDemand yapılandırma | Microsoft Docs'
description: Otomatik olarak sağlamak ve kullanıcı hesaplarına dönüm OnDemand sağlamasını kaldırmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8170fc094f1515783fa51a0d579bc12094aab836
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672951"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Öğretici: Otomatik kullanıcı hazırlama için temel taşıdır OnDemand yapılandırın

Bu öğreticide, otomatik olarak sağlamak ve kullanıcılar veya gruplar için dönüm OnDemand sağlamasını kaldırmak için dönüm OnDemand ve Azure Active Directory (Azure AD) Azure AD yapılandırmak için gerçekleştirme adımları gösterilmektedir.

> [!NOTE]
> Bu öğreticide, Azure AD kullanıcı sağlama hizmeti üzerinde oluşturulmuş bir bağlayıcı açıklanmaktadır. Bu hizmet yapar, nasıl çalıştığını ve sık sorulan sorular hakkında daha fazla bilgi için bkz: [kullanıcı sağlamayı ve Azure Active Directory ile hizmet olarak yazılım-a-(SaaS) uygulamalarına sağlama kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, olduğunu varsayar:

* Azure AD kiracısı.
* Bir dönüm OnDemand Kiracı.
* Temel taşıdır OnDemand yönetici izinlerine sahip bir kullanıcı hesabı.

> [!NOTE]
> Azure AD tümleştirmesi sağlama dayanan [dönüm OnDemand web hizmeti](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf). Bu hizmet, temel taşıdır OnDemand takımlar için kullanılabilir.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Azure Market'ten dönüm OnDemand Ekle

Otomatik kullanıcı hazırlama ile Azure AD için dönüm OnDemand yapılandırmadan önce temel taşıdır OnDemand marketten yönetilen SaaS uygulamaları listenize ekleyin.

Marketten dönüm OnDemand eklemek için aşağıdaki adımları izleyin.

1. İçinde [Azure portalında](https://portal.azure.com), sol taraftaki gezinti bölmesinde seçin **Azure Active Directory**.

    ![Azure Active Directory simgesi](common/select-azuread.png)

2. Git **kurumsal uygulamalar**ve ardından **tüm uygulamaları**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için seçin **yeni uygulama** iletişim kutusunun üst.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **dönüm OnDemand** seçip **dönüm OnDemand** sonucu panelinden. Uygulama eklemek için seçin **Ekle**.

    ![Sonuç listesinde dönüm OnDemand](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Temel taşıdır OnDemand için kullanıcı atama

Azure Active Directory kullanan adlı bir kavram *atamaları* hangi kullanıcıların seçilen uygulamalara erişimi alması belirlemek için. Otomatik kullanıcı hazırlama bağlamında, yalnızca kullanıcılar veya Azure AD'de bir uygulamaya atanan gruplar eşitlenir.

Yapılandırma ve otomatik kullanıcı sağlamayı etkinleştirmek için önce hangi kullanıcıların veya grupların Azure AD'de dönüm OnDemand erişmesi karar verin. Bu kullanıcılar veya gruplar için dönüm OnDemand atamak için yönergeleri izleyin. [kurumsal bir uygulamayı kullanıcı veya grup atama](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Kullanıcılar için dönüm OnDemand atamak için önemli ipuçları

* Tek bir atamanızı öneririz Azure AD kullanıcı sağlama yapılandırmasını otomatik kullanıcı test etmek için dönüm OnDemand için. Daha sonra ek kullanıcılar veya gruplar atayabilirsiniz.

* Bir kullanıcı için dönüm OnDemand atadığınızda, geçerli herhangi bir uygulamaya özgü rol ataması iletişim kutusunda kullanılabilir olması durumunda seçin. Kullanıcılarla **varsayılan erişim** rol sağlamasından dışlanır.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Temel taşıdır OnDemand için otomatik kullanıcı sağlamayı yapılandırma

Bu bölümde Azure AD sağlama hizmeti yapılandırma adımlarında size kılavuzluk eder. Oluşturma, güncelleştirme ve kullanıcılar veya gruplar, Azure AD'de kullanıcı veya grup atamalarını göre dönüm OnDemand devre dışı bırakmak için kullanın.

Azure AD'de dönüm OnDemand için otomatik kullanıcı hazırlama yapılandırmak için aşağıdaki adımları izleyin.

1. [Azure Portal](https://portal.azure.com) oturum açın. Seçin **kurumsal uygulamalar** > **tüm uygulamaları** > **dönüm OnDemand**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **dönüm OnDemand**.

    ![Uygulamalar listesinde dönüm OnDemand bağlantı](common/all-applications.png)

3. Seçin **sağlama** sekmesi.

    ![OnDemand dönüm sağlama](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Ayarlama **hazırlama modu** için **otomatik**.

    ![Temel taşıdır OnDemand sağlama modu](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Altında **yönetici kimlik bilgileri** bölümünde, yönetici kullanıcı adı, yönetici parolası ve temel taşıdır OnDemand'ın hesabın etki alanını girin:

    * İçinde **yönetici kullanıcı adı** kutusunda, etki alanı ya da temel taşıdır OnDemand kiracınıza yönetici hesabının kullanıcı adı girin. Contoso\admin buna bir örnektir.

    * İçinde **yönetici parolası** kutusunda, yönetici kullanıcı adı için karşılık gelen parolayı girin.

    * İçinde **etki alanı** kutusunda, temel taşıdır OnDemand Kiracı web hizmeti URL'sini girin. Örneğin, hizmet konumu `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, ve Contoso etki alanı `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Web hizmeti URL'si alma hakkında daha fazla bilgi için bkz. [bu pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Adım 5'te gösterilen kutuları doldurduktan sonra seçin **Test Bağlantısı** Azure'un emin olmak için AD için dönüm OnDemand bağlanabilirsiniz. Bağlantı başarısız olursa dönüm OnDemand hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Temel taşıdır OnDemand Bağlantıyı Sına](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. İçinde **bildirim e-posta** kutusunda kişinin e-posta adresi girin veya sağlama hata bildirimleri almak için Grup. Seçin **bir hata oluştuğunda e-posta bildirimi gönder** onay kutusu.

    ![Temel taşıdır OnDemand bildirim e-postası](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. **Kaydet**’i seçin.

9. Altında **eşlemeleri** bölümünden **eşitleme Azure Active Directory Kullanıcıları için dönüm OnDemand**.

    ![Temel taşıdır OnDemand eşitleme](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. İçinde dönüm OnDemand için Azure AD'den eşitlenen kullanıcı özniteliklerini gözden **öznitelik eşlemelerini** bölümü. Seçilen öznitelikler **eşleşen** özellikleri dönüm OnDemand kullanıcı hesaplarını güncelleştirme işlemleri eşleştirmek için kullanılır. Değişiklikleri kaydetmek için seçmeniz **Kaydet**.

    ![Temel taşıdır OnDemand öznitelik eşlemeleri](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Kapsam belirleme filtrelerini yapılandırmak için yönergeleri izleyin. [kapsam belirleme filtresi öğretici](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Azure AD hizmeti için temel taşıdır OnDemand sağlamayı etkinleştirmek için **ayarları** bölümünde **sağlama durumu** için **üzerinde**.

    ![Temel taşıdır OnDemand sağlama durumu](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Temel taşıdır OnDemand sağlamak için kullanıcıları veya istediğiniz grupları tanımlayın. İçinde **ayarları** bölümünde, istediğiniz değerleri seçin **kapsam**.

    ![Temel taşıdır OnDemand kapsamı](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Sağlama için hazır olduğunuzda **Kaydet**.

    ![Temel taşıdır OnDemand Kaydet](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Bu işlem, tüm kullanıcıların ilk eşitleme başlar veya tanımlı gruplar **kapsam** içinde **ayarları** bölümü. İlk eşitleme daha sonraki eşitlemeler gerçekleştirmek için daha uzun sürer. Azure AD sağlama hizmeti çalıştırdığı sürece, yaklaşık 40 dakikada oluşur. 

Kullanabileceğiniz **eşitleme ayrıntıları** bölüm ilerlemeyi izlemek ve sağlama etkinliği raporunu için bağlantıları izleyin. Raporun temel taşıdır OnDemand hizmette sağlama Azure AD tarafından gerçekleştirilen tüm eylemler açıklanmaktadır.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz: [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

Temel taşıdır OnDemand **konumu** özniteliği dönüm OnDemand portalında rollerine karşılık gelen bir değer bekliyor. Geçerli bir listesini almak için **konumu** değerleri, Git **kullanıcı kaydını Düzenle > kuruluş yapısı > Konum** dönüm OnDemand portalında.

![OnDemand dönüm sağlama kullanıcı kaydını düzenleme](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Temel taşıdır OnDemand sağlama konumu](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Temel taşıdır OnDemand sağlama konum listesi](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Ek kaynaklar

* [Kullanıcı, kurumsal uygulamalar için hesabı hazırlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri gözden geçirin ve sağlama etkinliği raporları alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
