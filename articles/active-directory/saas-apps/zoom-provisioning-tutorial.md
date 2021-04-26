---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için yakınlaştırmayı yapılandırma | Microsoft Docs'
description: Azure AD 'den yakınlaştırmak için Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: c87c3692d7e1106e0952b85791da663e03c6001d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182487"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için yakınlaştırmayı yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem yakınlaştırma hem de Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmeti 'ni kullanarak [yakınlaştırmak](https://zoom.us/pricing/) için kullanıcıları ve grupları otomatik olarak hazırlar ve serbest hazırlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen özellikler
> [!div class="checklist"]
> * Yakınlaştırma halinde Kullanıcı oluşturma
> * Artık erişim gerektirmeyen kullanıcıları yakınlaştırmadan kaldır
> * Azure AD ve yakınlaştırma arasında kullanıcı özniteliklerinin eşitlenmiş olmasını sağlama
> * Yakınlaştırmak için [Çoklu oturum açma](./zoom-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Azure AD kiracısı](../develop/quickstart-create-new-tenant.md).
* Azure AD'de hazırlama [iznine](../roles/permissions-reference.md) sahip bir kullanıcı hesabı (Uygulama Yöneticisi, Bulut Uygulaması Yöneticisi, Uygulama Sahibi veya Genel Yönetici). 
* [Bir yakınlaştırma kiracısı](https://zoom.us/pricing).
* Yönetici izinleriyle yakınlaştırılmış bir kullanıcı hesabı.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD ve yakınlaştırma arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın. 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için yakınlaştırmayı yapılandırma

1. [Yakınlaştırma yönetici konsolunda](https://zoom.us/signin)oturum açın. Sol gezinti bölmesinde **gelişmiş > Uygulama marketi** ' ne gidin.

    ![Tümleştirmeleri Yakınlaştır](media/zoom-provisioning-tutorial/zoom01.png)

2. Sayfanın sağ üst köşesindeki **Yönet** ' e gidin. 

    ![Manage App Market 'in çağrılan Yönet seçeneği ile ekran görüntüsü.](media/zoom-provisioning-tutorial/zoom02.png)

3. Oluşturulan Azure AD uygulamanıza gidin. 
    
    ![Azure A D uygulaması olarak adlandırılan oluşturulan uygulamalar bölümünün ekran görüntüsü.](media/zoom-provisioning-tutorial/zoom03.png)

4. Sol gezinti bölmesindeki **uygulama kimlik bilgilerini** seçin.

    ![Uygulama kimlik bilgileri seçeneğinin vurgulandığı sol gezinti bölmesinin ekran görüntüsü.](media/zoom-provisioning-tutorial/zoom04.png)

5. **JWT belirtecini** kopyalayın ve kaydedin. Bu değer, Azure portal yakınlaştırma uygulamanızın sağlama sekmesindeki **gizli belirteç** alanına girilir. Süresi dolmayan yeni bir belirtece ihtiyacınız varsa, otomatik olarak yeni bir belirteç oluşturacak olan sona erme süresini yeniden yapılandırmanız gerekecektir. 

    ![Uygulama kimlik bilgileri sayfasının ekran görüntüsü.](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD Uygulama Galerisi 'nden yakınlaştırma ekleme

Hazırlama işlemini önizlemeye başlamak için Azure AD Uygulama Galerisi 'nden yakınlaştırma ekleyin. Daha önce daha önce SSO 'yu Yakınlaştır ayarını yaptıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Kullanıcıları ve grupları önizlemeye atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>5. Adım. Otomatik Kullanıcı sağlamayı yakınlaştırmak için yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak TestApp içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan bir adım adım yol gösterir.

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Azure AD 'de yakınlaştırma için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Yakınlaştır**' ı seçin.

    ![Uygulamalar listesindeki yakınlaştırma bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde `https://api.zoom.us/scim` **kiracı URL 'sini** girin. Daha önce **gizli bir belirteçte** alınan **JWT belirteci** değerini girin. Azure AD 'nin yakınlaştırmasına bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, yakınlaştırma hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Yakınlaştırma sağlama](./media/zoom-provisioning-tutorial/provisioning.png)

6. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde, **Kullanıcılar Azure Active Directory Yakınlaştır**' ı seçin.

9. **Öznitelik eşleme** bölümünü yakınlaştırmak IÇIN Azure AD 'den eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme işlemlerinde yakınlaştırma aşamasında Kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, yakınlaştırma API 'sinin kullanıcıları bu özniteliğe göre filtrelemeyi desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|
   |---|---|
   |userName|Dize|
   |active|Boole|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |e-postalar [tür EQ "iş"]|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Department|Dize|

10. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

11. Azure AD sağlama hizmeti 'ni yakınlaştırma için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsamda** istenen değerleri seçerek yakınlaştırmak için sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

13. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer. 

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

1. Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
2. Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
3. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.  

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları
* Yakınlaştırma yalnızca en fazla 9.999 temel kullanıcıya izin verir.

## <a name="change-log"></a>Değişiklik günlüğü
* 05/14/2020-e-postalar [Type EQ "Work"] özniteliği için eklenen GÜNCELLEŞTIRME işlemleri için destek.
* 10/20/2020-var olan "Pro" ve "Corp" rollerini değiştirmek için "lisanslı" ve "on-Pred" iki yeni rol için destek eklendi. "Pro" ve "Corp" rollerinin desteği gelecekte kaldırılacak.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)