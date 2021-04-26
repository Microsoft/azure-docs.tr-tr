---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için dilbilgisi yapılandırma | Microsoft Docs'
description: Azure AD 'den Grammarly 'e Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: cd2dd9d7-4901-40c8-8888-98850557b072
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2021
ms.author: Zhchia
ms.openlocfilehash: ca01289ce66afe642081e5be17373e640dd1e46d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864831"
---
# <a name="tutorial-configure-grammarly-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlama için Grammarly yapılandırma

Bu öğreticide, otomatik Kullanıcı sağlamayı yapılandırmak için hem dilbilgisi hem de Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımlar açıklanmaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmeti 'ni kullanarak kullanıcıları ve grupları otomatik [olarak sağlar ve](https://www.grammarly.com/) geçersiz şekilde hazırlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Desteklenen yetenekler
> [!div class="checklist"]
> * Dilbilgisi içinde Kullanıcı oluşturma
> * Artık erişim gerektirmeyen kullanıcıları Dilmde kaldır
> * Kullanıcı özniteliklerinin Azure AD ile dilbilgisi arasında eşitlenmiş olmasını sağlama

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Azure AD'de hazırlama [iznine](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) sahip bir kullanıcı hesabı (Uygulama Yöneticisi, Bulut Uygulaması Yöneticisi, Uygulama Sahibi veya Genel Yönetici). 
* Yönetici erişimi olan bir Grammarly Iş hesabı.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) hakkında bilgi edinin.
1. [Hazırlık kapsamına](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) dahil edilecek kullanıcıları seçin.
1. [Azure AD Ile dilbilgisi arasında](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)hangi verilerin eşlendiğini saptayın. 

## <a name="step-2-configure-grammarly-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için Grammarly 'yi yapılandırma

Grammarly temsilcinize ulaşın veya <support@grammarly.com> sağlama belirtecinizin isteğine yazın.

## <a name="step-3-add-grammarly-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden dilbilgisi ekleme

Azure AD uygulama galerisindeki dilbilgisi ' yı, hazırlama işlemini Grammarly 'e yönetmeye başlamak için bir şekilde ekleyin. SSO için daha önce dilbilgisi ayarladıysanız aynı uygulamayı kullanabilirsiniz. Başlangıçta tümleştirmeyi test ettiğinizde ayrı bir uygulama oluşturmanızı öneririz. Galeriden bir uygulama ekleme hakkında daha fazla bilgi edinmek için [Bu hızlı](../manage-apps/add-application-portal.md)başlangıca bakın.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama

Azure AD sağlama hizmeti 'ni, uygulamaya atamaya veya Kullanıcı ya da grubun özniteliklerine göre sağlanacak şekilde verilecek kapsam için kullanabilirsiniz. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Yalnızca Kullanıcı veya grup özniteliklerine göre sağlanacak olan kapsamı tercih ederseniz, kapsamları [kapsam filtreleriyle sağlama](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)bölümünde açıklandığı gibi bir kapsam filtresi kullanabilirsiniz.

* Kullanıcıları ve grupları dilbilgisi olarak atadığınızda, **varsayılan erişim** dışında bir rol seçmeniz gerekir. Varsayılan erişim rolüne sahip kullanıcılar sağlanmasından çıkarılır ve sağlama günlüklerinde etkin değil olarak işaretlenir. Uygulamada kullanılabilen tek rol varsayılan erişim rolü ise, daha fazla rol eklemek için [uygulama bildirimini güncelleştirebilirsiniz](../develop/howto-add-app-roles-in-azure-ad-apps.md) .

* Başlangıçta kapsamı sınırlı tutun. Herkese göndermeden önce küçük bir Kullanıcı ve grup kümesi ile test edin. Sağlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında, uygulamaya bir veya iki Kullanıcı ya da grup atayarak bu seçeneği kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında, [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)belirtebilirsiniz.


## <a name="step-5-configure-automatic-user-provisioning-to-grammarly"></a>5. Adım. Otomatik Kullanıcı sağlamayı Grammarly 'e yapılandırma

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı veya grup atamalarını temel alarak TestApp içinde kullanıcıları veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında size kılavuzluk eder.

### <a name="configure-automatic-user-provisioning-for-grammarly-in-azure-ad"></a>Azure AD 'de dilbilgisi için otomatik Kullanıcı sağlamayı yapılandırma

1. [Azure portalında](https://portal.azure.com) oturum açın. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar bölmesini gösteren ekran görüntüsü.](common/enterprise-applications.png)

1. Uygulamalar listesinde, **dilbilgisi**' yı seçin.

    ![Uygulamalar listesindeki dilbilgisi bağlantısını gösteren ekran görüntüsü.](common/all-applications.png)

1. **Hazırlama** sekmesini seçin.

    ![Sağlama sekmesini gösteren ekran görüntüsü.](common/provisioning.png)

1. **Sağlama modunu** **Otomatik** olarak ayarlayın.

    ![Sağlama modunun otomatik olarak ayarlandığını gösteren ekran görüntüsü.](common/provisioning-automatic.png)

1. **Yönetici kimlik bilgileri** bölümünde, **kiracı URL 'si** girin alanına girin `https://sso.grammarly.com/scim/v2` ve **gizli belirteç** alanına, dilbilgisi tarafından sunulan belirteci girin (yukarıdaki 2. adıma bakın). Azure AD 'nin Grammarly 'e bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Grammarly hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Kiracı URL 'SI ve gizli belirteç kutularını gösteren ekran görüntüsü.](common/provisioning-testconnection-tenanturltoken.png)

1. **Bildirim e-postası** kutusunda, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin. **Bir hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim e-posta kutusunu gösteren ekran görüntüsü.](common/provisioning-notification-email.png)

1. **Kaydet**’i seçin.

1. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları, dilbilgisi olarak eşitler**' ı seçin.

1. **Öznitelik eşleme** bölümünde Azure AD 'Den Grammarly 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için dilbilgisi içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirirseniz, Grammarly API 'sinin kullanıcıları bu özniteliğe göre filtrelemeyi desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** ' i seçin.

   |Öznitelik|Tür|Filtreleme için destekleniyor|
   |---|---|---|
   |userName|Dize|&check;|
   |externalId|Dize|
   |active|Boole|
   |displayName|Dize|
   |emails[type eq "work"].value|Dize|


1. Kapsam filtrelerini yapılandırmak için [kapsam filtresi öğreticisinde](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)sunulan yönergelere bakın.

1. Azure AD sağlama hizmetini dilbilgisi için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumunu açık olarak gösteren ekran görüntüsü.](common/provisioning-toggle-on.png)

1. **Ayarlar** bölümünde **kapsam** Içindeki istenen değerleri seçerek dilbilgisi için sağlamak istediğiniz kullanıcıları veya grupları tanımlayın.

    ![Sağlama kapsamını gösteren ekran görüntüsü.](common/provisioning-scope.png)

1. Sağlamaya hazırsanız **Kaydet**' i seçin.

    ![Kaydet düğmesini gösteren ekran görüntüsü.](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngü, Azure AD sağlama hizmeti çalıştığı sürece her 40 dakikada bir gerçekleştirilen sonraki Döngülerde daha uzun sürer.

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme

Sağlamayı yapılandırdıktan sonra, dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

* Hangi kullanıcıların başarıyla sağlandığını veya başarısız olduğunu öğrenmek için [sağlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın.
* Sağlama döngüsünün durumunu ve ne kadar kapanmasının tamamlanmasının nasıl yapıldığını görmek için [ilerleme çubuğunu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) kontrol edin.
* Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumları hakkında daha fazla bilgi edinmek için bkz. [karantinanın uygulama sağlama durumu](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)
