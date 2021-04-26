---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Zscaler özel erişimini (ZPA) yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını Zscaler özel erişimine (ZPA) otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 14708ddcc5c0e06ee58f5e9db5945c4e9f1a1d08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937153"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Zscaler özel erişimini (ZPA) yapılandırma

Bu öğreticinin amacı, Azure AD 'yi, Kullanıcı ve/veya grupları Zscaler özel erişimi 'ne (ZPA) otomatik olarak sağlamak ve devre dışı bırakmak üzere yapılandırmak için Zscaler özel erişimi (ZPA) ve Azure Active Directory (Azure AD) içinde gerçekleştirilecek adımları göstermektir.

> [!NOTE]
> Bu öğreticide, Azure AD Kullanıcı sağlama hizmeti ' nin üzerine oluşturulmuş bir bağlayıcı açıklanmaktadır. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).
>
> Bu bağlayıcı Şu anda genel önizleme aşamasındadır. Önizleme özellikleri için genel Microsoft Azure kullanım koşulları hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* Bir Azure AD kiracısı
* [Zscaler özel erişim (ZPA) kiracısı](https://www.zscaler.com/pricing-and-plans#contact-us)
* Yönetim izinlerine sahip Zscaler özel erişimi (ZPA) içinde bir kullanıcı hesabı.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Kullanıcıları Zscaler özel erişimine atama (ZPA)

Azure Active Directory seçili uygulamalara hangi kullanıcıların erişimi alacağını belirleyen *atama* adı verilen bir kavram kullanır. Otomatik Kullanıcı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya atanmış olan kullanıcılar ve/veya gruplar eşitlenir.

Otomatik Kullanıcı sağlamayı yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Zscaler özel erişimine (ZPA) erişmesi gerektiğine karar vermeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ve/veya grupları Zscaler özel erişimine (ZPA) atayabilirsiniz:
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Zscaler özel erişimine (ZPA) Kullanıcı atamaya yönelik önemli ipuçları

* Otomatik Kullanıcı sağlama yapılandırmasını test etmek için, Zscaler özel erişimine (ZPA) tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı Zscaler özel erişimi 'ne (ZPA) atarken atama iletişim kutusunda uygulamaya özgü geçerli herhangi bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolüne sahip kullanıcılar, sağlanmasından çıkarılır.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Sağlama için Zscaler özel erişimi (ZPA) ayarlama

1. [Zscaler özel erişim (ZPA) yönetici konsolunda](https://admin.private.zscaler.com/)oturum açın. **Yönetim > IDP yapılandırması**' na gidin.

    ![Zscaler özel erişim (ZPA) Yönetici Konsolu](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  **Çoklu oturum açma** Için bir IDP yapılandırıldığından emin olun. IDP kurulumu yoksa, ekranın sağ üst köşesindeki artı simgesine tıklayarak bir tane ekleyin.

    ![Zscaler özel erişimi (ZPA) SCıM ekleme](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. IDP **yapılandırması** Ekleme Sihirbazı ' nı kullanarak IDP Ekleme Sihirbazı ' nı izleyin. **Çoklu oturum açma** alanını **Kullanıcı** olarak ayarlanmış şekilde bırakın. Bir **ad** girin ve açılan listeden **etki alanlarını** seçin. Sonraki pencereye gitmek için **İleri** 'ye tıklayın.

    ![Zscaler özel erişimi (ZPA) IDP ekleme](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. **Hizmet sağlayıcı sertifikasını** indirin. Sonraki pencereye gitmek için **İleri** 'ye tıklayın.

    ![Zscaler özel erişim (ZPA) SP sertifikası](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. Sonraki pencerede, daha önce indirilen **hizmet sağlayıcısı sertifikasını** karşıya yükleyin.

    ![Zscaler özel erişimi (ZPA) sertifikayı karşıya yükle](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  **Çoklu oturum açma URL 'si** ve **ıDP varlık kimliğini** sağlamak için aşağı kaydırın.

    ![Zscaler özel erişimi (ZPA) IDP KIMLIĞI](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Aşağı kaydırarak **SCıM eşitlemesini etkinleştirin**. **Yeni belirteç oluştur** düğmesine tıklayın. **Taşıyıcı belirtecini** kopyalayın. Bu değer, Azure portal Zscaler özel erişim (ZPA) uygulamanızın sağlama sekmesindeki gizli belirteç alanına girilir.

    ![Zscaler özel erişimi (ZPA) belirteç oluştur](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  **Kiracı URL 'sini** bulmak için **Yönetim > IDP yapılandırması**' na gidin. Sayfada listelenen yeni eklenen IDP yapılandırmasının adına tıklayın.

    ![Zscaler özel erişimi (ZPA) IDP adı](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Sayfanın sonundaki **SCIM hizmet sağlayıcısı uç noktasını** görüntülemek için aşağı kaydırın. **SCIM hizmet sağlayıcısı uç noktasını** kopyalayın. Bu değer, Azure portal Zscaler özel erişim (ZPA) uygulamanızın sağlama sekmesinde kiracı URL 'SI alanına girilecektir.

    ![Zscaler özel erişimi (ZPA) SCıM URL 'SI](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Galeriden Zscaler özel erişimi (ZPA) ekleme

Azure AD ile otomatik Kullanıcı sağlama için Zscaler özel erişimini (ZPA) yapılandırmadan önce, Azure AD uygulama galerisindeki Zscaler özel erişimi (ZPA) yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Azure AD Uygulama Galerisi 'nden Zscaler özel erişimi (ZPA) eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni bir uygulama eklemek için bölmenin üst kısmındaki **Yeni uygulama** düğmesini seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Zscaler özel erişimi (ZPA)** girin, sonuçlar panelinde **Zscaler özel erişimi (ZPA)** seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın.

    ![Sonuçlar listesinde Zscaler özel erişimi (ZPA)](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Zscaler özel erişimine otomatik Kullanıcı sağlamayı yapılandırma (ZPA) 

Bu bölümde Azure AD sağlama hizmeti 'ni kullanarak, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan Zscaler özel erişimi (ZPA) içindeki kullanıcıları ve/veya grupları oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

> [!TIP]
> [Zscaler özel erişimi (ZPA) çoklu oturum açma öğreticisinde](./zscalerprivateaccess-tutorial.md)sunulan yönergeleri Izleyerek Zscaler özel ERIŞIMI (ZPA) için SAML tabanlı çoklu oturum açmayı etkinleştirmeyi de tercih edebilirsiniz. Çoklu oturum açma, otomatik Kullanıcı sağlamasından bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini tamamlayabilse de.

> [!NOTE]
> Kullanıcılar ve gruplar sağlandığında veya sağlanmak istendiğinde, grup üyeliklerinin düzgün şekilde güncelleştirildiğinden emin olmak için düzenli aralıklarla sağlamayı yeniden başlatmanızı öneririz. Yeniden başlatma işlemi yapıldığında, hizmetimizi tüm grupları yeniden değerlendirmeye ve üyelikleri güncelleştirmeye zorlar.  

> [!NOTE]
> Zscaler özel erişiminin SCıM uç noktası hakkında daha fazla bilgi edinmek için, [buna](https://www.zscaler.com/partners/microsoft)bakın.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Azure AD 'de Zscaler özel erişimi (ZPA) için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Zscaler özel erişimi (ZPA)** öğesini seçin.

    ![Uygulamalar listesindeki Zscaler özel erişim (ZPA) bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, **kiracı URL**'Sinde daha önce alınan **SCIM hizmet sağlayıcısı uç noktası** değerini girin. Daha önce **gizli bir belirteçte** alınan **taşıyıcı belirteç** değerini girin. Azure AD 'nin Zscaler özel erişimine (ZPA) bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Zscaler özel erişim (ZPA) hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI + belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Zscaler özel erişimi (ZPA) ile eşitler**' ı seçin.

    ![Zscaler özel erişim (ZPA) Kullanıcı eşlemeleri](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. **Öznitelik eşleme** bölümünde, Azure AD 'Den Zscaler özel erişimi (ZPA) ile eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Zscaler özel erişimi (ZPA) içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Zscaler özel erişim (ZPA) Kullanıcı öznitelikleri](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. **Eşlemeler** bölümünde **Azure Active Directory gruplarını Zscaler özel erişimi (ZPA) olarak eşitler**' ı seçin.

    ![Zscaler özel erişim (ZPA) Grup eşlemeleri](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. **Öznitelik eşleme** bölümünde, Azure AD 'Den Zscaler özel erişimi (ZPA) ile eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Zscaler özel erişimi (ZPA) içindeki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

    ![Zscaler özel erişim (ZPA) grubu öznitelikleri](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. Zscaler özel erişimi (ZPA) için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek Zscaler özel erişimine (ZPA) sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer. , Zscaler özel erişimi (ZPA) üzerinde Azure AD sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri açıklayan, ilerlemeyi izlemek ve sağlama etkinliği raporunu izlemek için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD sağlama günlüklerinin nasıl okunduğu hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı hesabı sağlamayı raporlama](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)