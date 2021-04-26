---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Clarizen One yapılandırma | Microsoft Docs'
description: Azure AD 'den bir kullanıcı hesabını Clarizen 'ya otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: f3a19d3c3bf3e4340bb36fd683453541fa15eb6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650850"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlama için Clarizen bir yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem Clarizen One hem de Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmeti 'ni kullanarak kullanıcıları ve grupları otomatik olarak sağlar ve [açıklığa kavuşturun](https://www.clarizen.com/) . Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında bilgi için bkz. [Azure AD ile hizmet olarak yazılım (SaaS) uygulamaları için Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Desteklenen özellikler

> [!div class="checklist"]
> * Clarizen 'da kullanıcıları oluşturun.
> * Artık erişim gerektirdiklerinde Clarizen 'da kullanıcıları kaldırın.
> * Kullanıcı özniteliklerini Azure AD ile ve Clarizone arasında eşitlenmiş halde tutun.
> * Clarizen 'da gruplar ve grup üyelikleri sağlayın.
> * Clarizen 'ya yönelik [Çoklu oturum açma (SSO)](./clarizen-tutorial.md) önerilir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Azure AD kiracısı](../develop/quickstart-create-new-tenant.md).
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı. Uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici örnekleri gösterilebilir.
* **Tümleştirme kullanıcısı** ve **Lite yönetici** [izinleri](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)ile Clarizen, bir kullanıcı hesabı.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama

1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
1. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
1. [Azure AD Ile Clarizen arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini belirler.

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için Clarizen One yapılandırma

1. Aşağıdaki üç kiracı URL 'Lerinden birini, Clarizen bir ortamınıza ve veri merkezine göre seçin:
      * ABD üretim veri merkezi: https://servicesapp2.clarizen.com/scim/v2
      * AB üretim veri merkezi: https://serviceseu1.clarizen.com/scim/v2
      * ABD korumalı alan veri merkezi: https://servicesapp.clarizentb.com/scim/v2
      * AB korumalı alan veri merkezi: https://serviceseu.clarizentb.com/scim/v2

1. Bir [API anahtarı](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)oluşturun. Bu değer, Azure portal bir uygulamanızın **sağlama** sekmesindeki **gizli belirteç** kutusuna girilir.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden Clarizen, One ekleyin

Azure AD uygulama galerisindeki Clarizen, bir tane ekleyerek Clarizen 'ya sağlamayı yönetmeye başlayın. Daha önce SSO için Clarizen bir tane ayarladıysanız aynı uygulamayı kullanabilirsiniz. Başlangıçta tümleştirmeyi test ettiğinizde ayrı bir uygulama oluşturun. Galeriden bir uygulama ekleme hakkında daha fazla bilgi edinmek için bkz. [Azure AD kiracınıza uygulama ekleme](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama

Azure AD sağlama hizmeti ile, uygulamaya atamaya veya Kullanıcı ya da grubun özniteliklerine göre sağlanacak olan kapsamı belirleyebilirsiniz. Atamaya göre uygulamanıza sağlanacak kapsamı tercih ederseniz, uygulamaya Kullanıcı ve grup atamak için [Azure Active Directory 'de bir uygulama için Kullanıcı atamasını yönetme](../manage-apps/assign-user-or-group-access-portal.md) bölümündeki adımları izleyin. Yalnızca Kullanıcı veya grup özniteliklerine göre sağlanacak olan kapsamı tercih ederseniz, kapsam filtresiyle [öznitelik tabanlı uygulama sağlama](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)bölümünde açıklandığı gibi bir kapsam filtresi kullanın.

* Kullanıcıları ve grupları Clarizen 'a atadığınızda, **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan erişim rolüne sahip kullanıcılar sağlanmasından çıkarılır ve sağlama günlüklerinde etkin değil olarak işaretlenir. Uygulamada kullanılabilen tek rol varsayılan erişim rolü ise, daha fazla rol eklemek için [uygulama bildirimini güncelleştirebilirsiniz](../develop/howto-add-app-roles-in-azure-ad-apps.md) .
* Başlangıçta kapsamı sınırlı tutun. Herkese göndermeden önce küçük bir Kullanıcı ve grup kümesi ile test edin. Sağlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında, uygulamaya bir veya iki kullanıcı veya grup atayarak denetimi koruyabilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında, [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)belirtebilirsiniz.

## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>5. Adım. Otomatik Kullanıcı sağlamayı Clarizone 'ya yapılandırma

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı veya grup atamalarını temel alarak TestApp içinde kullanıcıları veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında size kılavuzluk eder.

### <a name="configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Azure AD 'de Clarizen için otomatik Kullanıcı sağlamayı yapılandırın

1. [Azure portalında](https://portal.azure.com) oturum açın. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.

      ![Kurumsal uygulamalar bölmesini gösteren ekran görüntüsü.](common/enterprise-applications.png)

1. Uygulamalar listesinde **Clarizen One** öğesini seçin.

      ![Uygulamalar listesinde Clarizen bir bağlantısını gösteren ekran görüntüsü.](common/all-applications.png)

1. **Hazırlama** sekmesini seçin.

      ![Sağlama sekmesini gösteren ekran görüntüsü.](common/provisioning.png)

1. **Sağlama modunu** **Otomatik** olarak ayarlayın.

      ![Sağlama sekmesi otomatik seçeneğini gösteren ekran görüntüsü.](common/provisioning-automatic.png)

1. **Yönetici kimlik bilgileri** bölümünde, en az bir **kiracı URL 'Si** ve **gizli belirteç** girin. Azure AD 'nin Clarizen 'ya bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, en az bir hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Gizli belirteç kutusunu gösteren ekran görüntüsü.](common/provisioning-testconnection-tenanturltoken.png)

1. **Bildirim e-postası** kutusunda, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin. **Bir hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim e-posta kutusunu gösteren ekran görüntüsü.](common/provisioning-notification-email.png)

1. **Kaydet**’i seçin.

1. **Eşlemeler** bölümünde **Azure Active Directory kullanıcıları bir tane Clarizen olacak şekilde eşitleme**' yi seçin.

1. **Öznitelik eşleme** bölümünde Azure AD 'Den Clarizen 'ya eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Clarizen olan kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirirseniz, Clarizen One API 'sinin, bu özniteliğe göre kullanıcıların filtrelenmesini desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|
   |---|---|
   |userName|Dize|
   |displayName|Dize|
   |active|Boole|
   |başlık|Dize|
   |emails[type eq "work"].value|Dize|
   |e-postalar [tür EQ "ana"]. değer|Dize|
   |e-postalar [tür EQ "Other"]. değer|Dize|
   |preferredLanguage|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |ad. biçimlendirildi|Dize|
   |name.honorificPrefix|Dize|
   |Name. Manificsuffix|Dize|
   |adresler [tür EQ "Other"]. biçimlendirildi|Dize|
   |adresler [tür EQ "iş"]. biçimlendirildi|Dize|
   |adresler [tür EQ "iş"]. ülke|Dize|
   |adresler [tür EQ "iş"]. bölge|Dize|
   |adresler [tür EQ "iş"]. konum|Dize|
   |adresler [tür EQ "iş"]. PostaKodu|Dize|
   |adresler [tür EQ "Work"]. streetAddress|Dize|
   |phoneNumbers[type eq "work"].value|Dize|
   |phoneNumbers[type eq "mobile"].value|Dize|
   |phoneNumbers [tür EQ "Faks"]. değer|Dize|
   |phoneNumbers [tür EQ "ana"]. değer|Dize|
   |phoneNumbers [tür EQ "Other"]. değer|Dize|
   |phoneNumbers [tür EQ "sayfalayıcı"]. değer|Dize|
   |externalId|Dize|
   |nickName|Dize|
   |locale|Dize|
   |Roller [PRIMARY EQ "true". Type]|Dize|
   |Roller [birincil EQ "true". Value]|Dize|
   |timezone|Dize|
   |userType|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Department|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: employeeNumber|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Manager|Başvuru|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: costCenter|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Bölüm|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Bölüm|Dize|

1. **Eşlemeler** bölümünde **Azure Active Directory gruplarını Clarizone olarak eşitleme**' yi seçin.

1. **Öznitelik eşleme** bölümünde Azure AD 'Den Clarizen 'ya eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Clarizen ' deki grupları eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

      |Öznitelik|Tür|
      |---|---|
      |displayName|Dize|
      |externalId|Dize|
      |üyeler|Başvuru|

1. Kapsam filtrelerini yapılandırmak için  [kapsam filtresi öğreticisindeki](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)yönergelere bakın.

1. Azure AD sağlama hizmetini Clarizen One için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

      ![Sağlama durumunu açık olarak gösteren ekran görüntüsü.](common/provisioning-toggle-on.png)

1. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek Clarizen 'ya sağlamak istediğiniz kullanıcıları veya grupları tanımlayın.

      ![Sağlama kapsamını gösteren ekran görüntüsü.](common/provisioning-scope.png)

1. Sağlamaya hazırsanız **Kaydet**' i seçin.

      ![Sağlama yapılandırmasının kaydedilmesini gösteren ekran görüntüsü.](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer.

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme

Sağlamayı yapılandırdıktan sonra, dağıtımınızı izlemek için aşağıdaki kaynakları kullanın.

1. Hangi kullanıcıların başarıyla sağlandığını veya başarısız olduğunu öğrenmek için [sağlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın.
1. Sağlama döngüsünün durumunu ve ne kadar kapanmasının tamamlanmasının nasıl yapıldığını görmek için [ilerleme çubuğunu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) kontrol edin.
1. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumları hakkında daha fazla bilgi edinmek için bkz. [karantina durumunda uygulama hazırlama](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Sorun giderme ipuçları

Bir kullanıcıyı Clarizen One Galeri uygulamasına atadığınızda yalnızca **Kullanıcı** rolünü seçin. Şu roller geçersiz:

* Yönetici (yönetici)
* E-posta raporlama kullanıcısı
* Dış Kullanıcı
* Mali Kullanıcı
* Sosyal Kullanıcı
* Süper Kullanıcı
* Gider Kullanıcı & saat

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)