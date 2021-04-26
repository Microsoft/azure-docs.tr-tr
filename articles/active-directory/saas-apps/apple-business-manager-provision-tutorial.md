---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Apple Business Manager 'ı yapılandırma | Microsoft Docs"
description: Azure AD 'den Apple Business Manager 'a Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4ad30031-9904-4ac3-a4d2-e8c28d44f319
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: b4f24c9beffcd67fb84940c2e159da615496d9aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180379"
---
# <a name="tutorial-configure-apple-business-manager-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Apple Business Manager 'ı yapılandırma



Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem Apple Business Manager hem de Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında Azure AD, Azure AD sağlama hizmetini kullanarak kullanıcıları otomatik olarak [Apple Business Manager](https://business.apple.com/) 'a hazırlar ve hazırlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Desteklenen yetenekler
> [!div class="checklist"]
> * Apple Business Manager 'da Kullanıcı oluşturma
> * Apple Business Manager 'daki kullanıcıları artık erişim gerektirdiklerinde kaldırın
> * Kullanıcı özniteliklerinin Azure AD ile Apple Business Manager arasında eşitlenmiş olmasını sağlama

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Azure AD kiracısı](../develop/quickstart-create-new-tenant.md).
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı (örneğin, uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici).
* Yönetici veya kişiler yöneticisi rolüne sahip bir Apple Business Manager hesabı.

> [!NOTE]
> Azure AD 'ye belirteç aktarımı ve başarılı bir bağlantı oluşturma işleminin 4 takvim gününde tamamlanması veya işlemin yeniden başlatılması gerekebilir.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD Ile Apple Business Manager arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın. 

## <a name="step-2-configure-apple-business-manager-to-support-provisioning-with-azure-ad"></a>Adım 2. Apple Business Manager 'ı Azure AD ile sağlamayı destekleyecek şekilde yapılandırma

1. Apple Business Manager 'da yönetici veya kişiler yöneticisi rolüne sahip bir hesapla oturum açın.
2. Kenar çubuğunun altındaki ayarlar ' a tıklayın ve ardından veri kaynağı kuruluş ayarları ' na tıklayın ve ardından veri kaynağına bağlan ' a tıklayın.
3. SCıM ' in yanındaki Bağlan ' a tıklayın, uyarıyı dikkatle okuyun, Kopyala ' ya ve ardından Kapat ' a tıklayın
[İçinde bir belirteç ve bir kopya düğmesi sağlayan SCıM 'e Bağlan penceresi.] Kiracı URL 'sini Apple Business Manager 'dan Azure AD 'ye kopyalamak için bu pencereyi açık bırakın: ' https://federation.apple.com/feeds/business/scim '

    ![Apple İşletme Yönetimi](media/applebusinessmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> Gizli belirteç Azure AD Yöneticisi dışında bir kişiyle paylaşılmamalıdır.

## <a name="step-3-add-apple-business-manager-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden Apple Business Manager ekleme

Apple Business Manager ' a sağlamayı yönetmeye başlamak için Azure AD uygulama galerisinden Apple Business Manager ' a ekleyin. Daha önce, SSO için Apple Business Manager 'ı ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama 

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz. 

* Apple Business Manager 'a Kullanıcı atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz. 

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-business-manager"></a>5. Adım. Apple Business Manager 'a otomatik Kullanıcı sağlamayı yapılandırma

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Apple Business Manager**' ı seçin.

    ![Uygulamalar listesindeki Apple Business Manager](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Hazırlama sekmesi](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Sekme otomatik sağlama](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, **kiracı URL 'Si** ve **gizli belirteç** sırasıyla Apple Business Manager 'dan alınan **SCIM 2,0 temel URL 'sini ve erişim belirteci** değerlerini girin. Azure AD 'nin Apple Business Manager 'a bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Apple Business Manager hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Belirteç](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Bağlantı başarılı olursa, Apple Business Manager, SCıM bağlantısını etkin olarak gösterir. Bu işlem, Apple Business Manager 'ın en son bağlantı durumunu yansıtması için 60 saniyeye kadar sürebilir.

6. **Bildirim e-postası** alanına, sağlama hatası bildirimlerini alması gereken bir kişinin veya grubun e-posta adresini girin ve hata oluştuğunda onay kutusu- **e-posta bildirimi gönder**' i işaretleyin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’e tıklayın.

8. **Eşlemeler** bölümünde **Azure Active Directory kullanıcıları Apple Business Manager ile eşitler**' ı seçin.

9. **Öznitelik eşleme** bölümünde, Azure AD 'Den Apple Business Manager 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Apple Business Manager 'daki Kullanıcı hesaplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|
   |---|---|
   |active|Boole|
   |userName|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |name.givenName|Dize|
   |externalId|Dize|
   |locale|Dize|
   |timezone|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: employeeNumber|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: costCenter|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Bölüm|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Department|Dize|

10. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

11. Apple Business Manager için Azure AD sağlama hizmetini etkinleştirmek üzere ayarlar bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek Apple Business Manager 'a sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

13. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsam** içinde tanımlanan tüm kullanıcılar ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, Azure AD sağlama hizmeti çalıştığı sürece yaklaşık 40 dakikada bir oluşan sonraki eşitlemeler yerine gerçekleştirilmesi daha uzun sürer.

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

1. Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
2. Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
3. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.  

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
* [Apple Business Manager için SCıM gereksinimlerini gözden geçirme](https://support.apple.com/guide/apple-business-manager/apdd88331cd6)
* [Apple Business Manager 'da bir kişi KIMLIĞI nasıl kullanılır](https://support.apple.com/guide/apple-business-manager/apd69e1e48e9)
* [SCıM kullanarak kullanıcıları Apple Business Manager 'a aktarma](https://support.apple.com/guide/apple-business-manager/apd3ec7b95ad)
* [Apple Business Manager 'da SCıM Kullanıcı hesabı çakışmalarını çözme](https://support.apple.com/guide/apple-business-manager/apd313013d12)
* [Apple Business Manager 'da görünen Azure AD hesaplarını silme](https://support.apple.com/guide/apple-business-manager/apdaa5798fbe)
* [Apple Business Manager 'da SCıM etkinliğini görüntüleme](https://support.apple.com/guide/apple-business-manager/apd1bfd8dfde)
* [Apple Business Manager 'da mevcut SCıM belirtecini ve bağlantılarını yönetme](https://support.apple.com/guide/apple-business-manager/apdc9a823611)
* [Apple Business Manager 'da SCıM bağlantısının bağlantısını kesme](https://support.apple.com/guide/apple-business-manager/apd609be3a61)
* [Apple Business Manager 'da SCıM bağlantısının sorunlarını giderme](https://support.apple.com/guide/apple-business-manager/apd403a0f3bd/web)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)