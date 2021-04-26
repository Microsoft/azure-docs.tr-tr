---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Briat yapılandırma | Microsoft Docs'
description: Azure AD 'den Brit 'e Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 622688b3-9d20-482e-aab9-ce2a1f01e747
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
ms.openlocfilehash: 8bebcb49bc7bf31614a161c08d33d5910679b614
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225822"
---
# <a name="tutorial-configure-britive-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Briat yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem Briat hem de Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmeti ['ni kullanarak Kullanıcı](https://www.britive.com/) ve grupları otomatik olarak sağlar ve geçersiz hale getirmektedir. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen yetenekler
> [!div class="checklist"]
> * Briat içinde Kullanıcı oluşturma
> * Artık erişim gerektirmeyen kullanıcıları kaldırma
> * Azure AD ve Brikeler arasında kullanıcı özniteliklerinin eşitlenmiş olmasını sağlama
> * Briat içinde grupları ve grup üyeliklerini sağlama
> * Briat için [Çoklu oturum açma](britive-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md) 
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı (örneğin, uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici). 
* Bir [Britıcı](https://www.britive.com/) kiracı.
* Yönetici izinleriyle bir kullanıcı hesabı.


## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
1. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
1. [Azure AD ve Brikeler arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın. 

## <a name="step-2-configure-britive-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için Briat yapılandırma

Uygulamanın aşağıda belirtilen adımları kullanarak el ile yapılandırılması gerekir:
1. Yönetici ayrıcalıklarıyla Britıcı uygulamada oturum açın
1. **Yönetici->Kullanıcı yönetimi->kimlik sağlayıcıları** ' na tıklayın
1. **Kimlik sağlayıcısı ekle**' ye tıklayın. Ad ve açıklama girin. Kimlik sağlayıcısı ekle düğmesine tıklayın.

    ![Kimlik Sağlayıcı](media/britive-provisioning-tutorial/identity.png)

1. Aşağıda gösterilen birine benzer bir yapılandırma sayfası gösterilir.

    ![Yapılandırma sayfası](media/britive-provisioning-tutorial/configuration.png)

1. **SCIM** sekmesine tıklayın. SCıM sağlayıcısını genel 'ten Azure 'a değiştirin ve değişiklikleri kaydedin. SCıM URL 'sini kopyalayın ve bir yere göz önünde yazın. Bu değerler, Azure portal Britıcı uygulamanızın sağlama sekmesinde **kiracı URL 'si** kutularına girilir.

    ![SCıM sayfası](media/britive-provisioning-tutorial/scim.png)

1. **Belirteç oluştur**' a tıklayın. Belirtecin geçerliliğini gerekli olarak seçin ve belirteç Oluştur düğmesine tıklayın.

    ![Belirteç oluştur](media/britive-provisioning-tutorial/create-token.png)

1. Oluşturulan belirteci kopyalayın ve ona göz önünde edin. Tamam'a tıklayın. Lütfen kullanıcının belirteci tekrar göremediğini unutmayın. Gerekirse yeni bir belirteç oluşturmak için Re-Create düğmesine tıklayın. Bu değerler, Azure portal getAbstract uygulamanızın sağlama sekmesindeki **gizli belirteç** ve kiracı URL 'si kutularına girilir.

    ![Belirteci Kopyala](media/britive-provisioning-tutorial/copy-token.png) 


## <a name="step-3-add-britive-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden Britıcı ekleme

Sağlamayı daha da yönetmeye başlamak için Azure AD Uygulama Galerisi 'nden Briat ekleyin. Daha önce SSO için daha önceden kurulum yaptıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Kullanıcı ve grupları bir Bria atarken, **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 


## <a name="step-5-configure-automatic-user-provisioning-to-britive"></a>5. Adım. Otomatik Kullanıcı sağlamayı, Briat olarak yapılandırma 

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak Kullanıcı ve/veya grupları Azure AD 'de Kullanıcı ve/veya Grup atamaları temelinde oluşturma, güncelleştirme ve devre dışı bırakma adımları adım adım kılavuzluk eder.

### <a name="to-configure-automatic-user-provisioning-for-britive-in-azure-ad"></a>Azure AD 'de otomatik Kullanıcı sağlamasını yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

1. Uygulamalar listesinde, **Briat**' ı seçin.

    ![Uygulamalar listesindeki Britıcı bağlantı](common/all-applications.png)

1. **Hazırlama** sekmesini seçin.

    ![Hazırlama sekmesi](common/provisioning.png)

1. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Sekme otomatik sağlama](common/provisioning-automatic.png)

1. **Yönetici kimlik bilgileri** bölümü altında, Britıcı KIRACı URL 'Sini ve gizli belirtecinizi girin. Azure AD 'nin Briat 'a bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Britıcı hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Belirteç](common/provisioning-testconnection-tenanturltoken.png)

1. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

1. **Kaydet**’i seçin.

1. **Eşlemeler** bölümünde **Azure Active Directory kullanıcıları da brikeler**' ı seçin.

1. Azure AD 'den eşitlenen Kullanıcı özniteliklerini **öznitelik eşleme** bölümünde brikmek üzere gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Briat içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, BRITıCı API 'nin bu özniteliğe göre kullanıcıların filtrelenmesini desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|Filtreleme için destekleniyor|
   |---|---|---|
   |userName|Dize|&check;
   |active|Boole|
   |displayName|Dize|
   |başlık|Dize|
   |externalId|Dize|
   |preferredLanguage|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |nickName|Dize|
   |userType|Dize|
   |locale|Dize|
   |timezone|Dize|
   |e-postalar [tür EQ "ana"]. değer|Dize|
   |e-postalar [tür EQ "Other"]. değer|Dize|
   |emails[type eq "work"].value|Dize|
   |phoneNumbers [tür EQ "ana"]. değer|Dize|
   |phoneNumbers [tür EQ "Other"]. değer|Dize|
   |phoneNumbers [tür EQ "sayfalayıcı"]. değer|Dize|
   |phoneNumbers[type eq "work"].value|Dize|
   |phoneNumbers[type eq "mobile"].value|Dize|
   |phoneNumbers [tür EQ "Faks"]. değer|Dize|
   |adresler [tür EQ "iş"]. biçimlendirildi|Dize|
   |adresler [tür EQ "Work"]. streetAddress|Dize|
   |adresler [tür EQ "iş"]. konum|Dize|
   |adresler [tür EQ "iş"]. bölge|Dize|
   |adresler [tür EQ "iş"]. PostaKodu|Dize|
   |adresler [tür EQ "iş"]. ülke|Dize|
   |adresler [tür EQ "Home"]. biçimlendirildi|Dize|
   |adresler [tür EQ "ana"]. streetAddress|Dize|
   |adresler [tür EQ "ana"]. konum|Dize|
   |adresler [tür EQ "ana"]. bölge|Dize|
   |adresler [tür EQ "ana"]. PostaKodu|Dize|
   |adresler [tür EQ "ana"]. ülke|Dize|
   |adresler [tür EQ "Other"]. biçimlendirildi|Dize|
   |adresler [tür EQ "Other"]. streetAddress|Dize|
   |adresler [tür EQ "Other"]. konum|Dize|
   |adresler [tür EQ "Other"]. bölge|Dize|
   |adresler [tür EQ "Other"]. PostaKodu|Dize|
   |adresler [tür EQ "Other"]. ülke|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: employeeNumber|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: costCenter|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Organization|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Bölüm|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Department|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Manager|Başvuru|


1. **Eşlemeler** bölümünde **Azure Active Directory gruplarını Brikeler olarak eşitler**' ı seçin.

1. Azure AD 'den eşitlenen grup özniteliklerini **öznitelik eşleme** bölümünde brikmek üzere gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Briat içindeki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

      |Öznitelik|Tür|Filtreleme için destekleniyor|
      |---|---|---|
      |displayName|Dize|&check;
      |externalId|Dize|
      |üyeler|Başvuru|

1. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

1. Azure AD sağlama hizmetini Briat için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

1. **Ayarlar** bölümünde **kapsam** içindeki istenen değerleri seçerek, sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

1. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer. 

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

* Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
* Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
* Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.  

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)