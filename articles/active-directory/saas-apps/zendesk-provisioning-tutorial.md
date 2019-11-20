---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Zendesk yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını Zendesk 'e otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f9d819533b97a126a324ab867b7185fd6415847
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851973"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Öğretici: Otomatik Kullanıcı sağlaması için Zendesk yapılandırma

Bu öğreticide, Kullanıcı ve grupları Zendesk 'e otomatik olarak sağlamak ve sağlamak üzere Azure AD 'yi yapılandırmak için Zendesk ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımlar gösterilmektedir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti 'nin üzerine kurulmuş bir bağlayıcı açıklanmaktadır. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları öğrenmek için bkz. [Azure Active Directory ile hizmet olarak yazılım (SaaS) uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo şunları olduğunu varsayar:

* Azure AD kiracısı.
* Profesyonel plan veya daha iyi etkinleştirilmiş bir Zendesk kiracısı.
* Yönetici izinleriyle Zendesk 'te bir kullanıcı hesabı.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Azure Marketi 'nden Zendesk ekleyin

Azure AD ile otomatik Kullanıcı sağlaması için Zendesk yapılandırmadan önce Azure Marketi 'nden yönetilen SaaS uygulamaları listenize Zendesk ekleyin.

Market 'ten Zendesk eklemek için aşağıdaki adımları izleyin.

1. [Azure Portal](https://portal.azure.com)sol taraftaki gezinti bölmesinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory simgesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için iletişim kutusunun üst kısmındaki **Yeni uygulama** ' yı seçin.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Zendesk** yazın ve sonuç panelinden **Zendesk** ' i seçin. Uygulamayı eklemek için **Ekle**' yi seçin.

    ![Sonuçlar listesinde Zendesk](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Zendesk 'e Kullanıcı atama

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanan kullanıcılar veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların veya grupların Zendesk 'e erişmesi gerektiğine karar verin. Bu kullanıcıları veya grupları Zendesk 'e atamak için, [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)' daki yönergeleri izleyin.

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Zendesk 'e Kullanıcı atamaya yönelik önemli ipuçları

* Günümüzde, Zendesk rolleri Azure portal Kullanıcı arabiriminde otomatik olarak ve dinamik olarak doldurulur. Kullanıcılara Zendesk rolleri atamadan önce, Zendesk kiracınızdaki en son rolleri almak için Zendesk 'e yönelik bir ilk eşitlemenin tamamlandığından emin olun.

* İlk otomatik Kullanıcı sağlama yapılandırmanızı test etmek için Zendesk 'e tek bir Azure AD kullanıcısı atamanız önerilir. Testler başarılı olduktan sonra daha sonra ek kullanıcı veya grup atayabilirsiniz.

* Zendesk 'e bir Kullanıcı atadığınızda atama iletişim kutusunda varsa uygulamaya özgü geçerli herhangi bir rolü seçin. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Zendesk 'e otomatik Kullanıcı sağlamayı yapılandırma 

Bu bölüm, Azure AD sağlama hizmetini yapılandırma adımlarında size rehberlik eder. Azure AD 'de Kullanıcı veya grup atamalarına göre Zendesk 'te kullanıcıları veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için kullanın.

> [!TIP]
> Ayrıca, Zendesk için SAML tabanlı çoklu oturum açmayı etkinleştirebilirsiniz. [Zendesk çoklu oturum açma öğreticisindeki](zendesk-tutorial.md)yönergeleri izleyin. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini tamamlayabilse de.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Azure AD 'de Zendesk için otomatik Kullanıcı sağlamayı yapılandırma

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal uygulamalar** > **tüm uygulamalar** > **Zendesk**' i seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Zendesk**' i seçin.

    ![Uygulamalar listesindeki Zendesk bağlantısı](common/all-applications.png)

3. **Sağlama** sekmesini seçin.

    ![Zendesk sağlama](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

    ![Zendesk sağlama modu](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. **Yönetici kimlik bilgileri** bölümü altında, Zendesk hesabınızın yönetici kullanıcı adını, gizli belirtecini ve etki alanını girin. Bu değerlere örnek olarak şunlar verilebilir:

   * **Yönetici Kullanıcı adı** kutusunda, Zendesk kiracınızdaki yönetici hesabının kullanıcı adını girin. admin@contoso.com bunun bir örneğidir.

   * **Gizli belirteç** kutusunda adım 6 ' da açıklanan gizli anahtarı girin.

   * **Etki alanı** kutusunda, Zendesk kiracınızın alt etki alanını girin. Örneğin, kiracı URL 'si `https://my-tenant.zendesk.com`olan bir hesap için, alt etki **alanım-kiracım**.

6. Zendesk hesabınız için gizli belirteç, **Yönetim** > **API 'si** > **ayarlarında**bulunur. **Belirteç erişiminin** **etkin**olarak ayarlandığından emin olun.

    ![Zendesk yönetici ayarları](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk gizli belirteci](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. 5\. adımda gösterilen kutuları doldurduktan sonra Azure AD 'nin Zendesk 'e bağlanabildiğine emin olmak için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, Zendesk hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Zendesk test bağlantısı](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. **Bildirim e-postası** kutusunda, sağlama hatası bildirimlerini alacak kişinin veya grubun e-posta adresini girin. **Bir hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Zendesk bildirim e-postası](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. **Kaydet**’i seçin.

10. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Zendesk ' e eşitler**' ı seçin.

    ![Zendesk Kullanıcı eşitlemesi](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. **Öznitelik eşlemeleri** bölümünde, Azure AD 'den Zendesk 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için Zendesk içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri kaydetmek için **Kaydet**' i seçin.

    ![Zendesk ile eşleşen Kullanıcı öznitelikleri](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. **Eşlemeler** bölümünde **Azure Active Directory grupları Zendesk olarak eşitler**' ı seçin.

    ![Zendesk grubu eşitleme](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. **Öznitelik eşlemeleri** bölümünde, Azure AD 'den Zendesk 'e eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için Zendesk içindeki grupları eşleştirmek için kullanılır. Değişiklikleri kaydetmek için **Kaydet**' i seçin.

    ![Zendesk eşleşen grup öznitelikleri](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Kapsam filtrelerini yapılandırmak için [kapsam filtresi öğreticisindeki](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)yönergeleri izleyin.

15. Zendesk için Azure AD sağlama hizmetini etkinleştirmek üzere, **Ayarlar** bölümünde **sağlama durumunu** **Açık**olarak değiştirin.

    ![Zendesk sağlama durumu](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Zendesk 'e sağlamak istediğiniz kullanıcıları veya grupları tanımlayın. **Ayarlar** bölümünde, **kapsam**içinde istediğiniz değerleri seçin.

    ![Zendesk kapsamı](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Sağlamaya hazırsanız **Kaydet**' i seçin.

    ![Zendesk Kaydet](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Bu işlem, **Ayarlar** bölümünde **kapsamda** tanımlanan tüm kullanıcıların veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra eşitlenmesi daha uzun sürer. Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir gerçekleşir. 

İlerleme durumunu izlemek ve sağlama etkinliği raporunun bağlantılarını izlemek için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz. Rapor Zendesk üzerinde Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklar.

Azure AD sağlama günlüklerini okuma hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlama hakkında raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Zendesk yalnızca **Aracı** rollerine sahip kullanıcılar için grupların kullanımını destekler. Daha fazla bilgi için [Zendesk belgelerine](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)bakın.

* Bir kullanıcıya veya gruba özel bir rol atandığında, Azure AD otomatik Kullanıcı sağlama hizmeti de varsayılan rol **aracısını**atar. Yalnızca aracıların özel bir rol ataması yapılabilir. Daha fazla bilgi için [ZENDESK API belgelerine](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests)bakın. 

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Günlükleri İnceleme ve sağlama etkinliğinde rapor alma hakkında bilgi edinin](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
