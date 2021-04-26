---
title: "Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Tic-Tac Mobile 'ı yapılandırma | Microsoft Docs"
description: Azure AD 'den mobil Tic-Tac Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: 91ae51b9a2785dbc40c55fa58b26763916e8d16c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644587"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için Tic-Tac Mobile 'ı yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için Tic-Tac Mobile ve Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmeti 'ni kullanarak kullanıcıları ve grupları otomatik olarak [sunarak Tic 'e mobil](https://www.tictacmobile.com/) olarak hazırlar ve hazırlar. Bu hizmetin ne yaptığını, nasıl çalıştığını ve sık sorulan soruları hakkında bilgi için bkz. [Azure AD ile hizmet olarak yazılım (SaaS) uygulamaları için Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Desteklenen özellikler

> [!div class="checklist"]
> * Tic-Tac Mobile 'da kullanıcılar oluşturun.
> * Artık erişim gerektirdiklerinde Tic-Tac mobil kullanıcıları kaldırın.
> * Azure AD ile Tic-Tac Mobile arasında kullanıcı özniteliklerini eşitlenmiş halde tutun.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Azure AD kiracısı](../develop/quickstart-create-new-tenant.md).
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı. Uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici örnekleri gösterilebilir.
* Süper yönetici rolüne sahip bir [Tic-Tac mobil](https://www.tictacmobile.com/) hesabı.


## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama

1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
1. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
1. [Azure AD ile Tic-Tac Mobile arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın.

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için Tic-Tac Mobile 'ı yapılandırma

support@tictacmobile.com **Kiracı URL** 'Nizi ve **gizli belirtecinizi** almak için iletişim kurun. Belirteç almak için Tic-Tac Mobile 'da bir süper yönetici rolüne sahip olmanız gerekir. Belirteç, Azure portal Tic-Tac mobil uygulamanızın **sağlama** sekmesindeki **gizli belirteç** kutusuna girilir.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden Tic-Tac Mobile ekleyin

Sağlamayı Tic-Tac mobil 'e yönetmeye başlamak için Azure AD uygulama galerisinden Tic-Tac Mobile ekleyin. Çoklu oturum açma için daha önce Tic-Tac Mobile ayarladıysanız aynı uygulamayı kullanabilirsiniz. Başlangıçta tümleştirmeyi test ettiğinizde ayrı bir uygulama oluşturun. Galeriden bir uygulama ekleme hakkında daha fazla bilgi edinmek için bkz. [kapsam filtreleri Ile öznitelik tabanlı uygulama sağlama](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama

Azure AD sağlama hizmeti ile, uygulamaya atamaya veya Kullanıcı ya da grubun özniteliklerine göre sağlanacak olan kapsamı belirleyebilirsiniz. Atamaya göre uygulamanıza sağlanacak kapsamı tercih ederseniz, uygulamaya Kullanıcı ve grup atamak için [Azure Active Directory 'de bir uygulama için Kullanıcı atamasını yönetme](../manage-apps/assign-user-or-group-access-portal.md) bölümündeki adımları izleyin. Yalnızca Kullanıcı veya grup özniteliklerine göre sağlanacak olan kapsamı tercih ederseniz, kapsam filtresiyle [öznitelik tabanlı uygulama sağlama](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)bölümünde açıklandığı gibi bir kapsam filtresi kullanın.

* Mobil Tic-Tac kullanıcılar ve gruplar atadığınızda, **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan erişim rolüne sahip kullanıcılar sağlanmasından çıkarılır ve sağlama günlüklerinde etkin değil olarak işaretlenir. Uygulamada kullanılabilen tek rol varsayılan erişim rolü ise, daha fazla rol eklemek için [uygulama bildirimini güncelleştirebilirsiniz](../develop/howto-add-app-roles-in-azure-ad-apps.md) .
* Başlangıçta kapsamı sınırlı tutun. Herkese göndermeden önce küçük bir Kullanıcı ve grup kümesi ile test edin. Sağlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında, uygulamaya bir veya iki kullanıcı veya grup atayarak denetimi koruyabilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında, [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)belirtebilirsiniz.

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>5. Adım. Mobil Tic-Tac için otomatik Kullanıcı sağlamayı yapılandırma

Bu bölümde Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı veya grup atamalarını temel alarak TestApp içinde kullanıcıları veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında size kılavuzluk eder.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Azure AD 'de Tic-Tac Mobile için otomatik Kullanıcı sağlamayı yapılandırma

1. [Azure portalında](https://portal.azure.com) oturum açın. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar bölmesini gösteren ekran görüntüsü.](common/enterprise-applications.png)

1. Uygulamalar listesinde, **Tic-Tac Mobile**' ı seçin.

    ![Uygulamalar listesinde Tic-Tac Mobile bağlantısını gösteren ekran görüntüsü.](common/all-applications.png)

1. **Hazırlama** sekmesini seçin.

    ![Sağlama sekmesini gösteren ekran görüntüsü.](common/provisioning.png)

1. **Sağlama modunu** **Otomatik** olarak ayarlayın.

    ![Sağlama sekmesi otomatik seçeneğini gösteren ekran görüntüsü.](common/provisioning-automatic.png)

1. **Yönetici kimlik bilgileri** bölümünde, Tic-Tac MOBIL **kiracı URL** 'Nizi ve **gizli anahtar belirtecini** girin. Azure AD 'nin mobil Tic-Tac bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' yı seçin. Bağlantı başarısız olursa, Tic-Tac mobil hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

    ![Gizli belirteç kutusunu gösteren ekran görüntüsü.](common/provisioning-testconnection-tenanturltoken.png)

1. **Bildirim e-postası** kutusunda, sağlama hatası bildirimlerini alması gereken kişinin veya grubun e-posta adresini girin. **Bir hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim e-posta kutusunu gösteren ekran görüntüsü.](common/provisioning-notification-email.png)

1. **Kaydet**’i seçin.

1. **Eşlemeler** bölümünde, **Mobil Tic-Tac Azure Active Directory Kullanıcıları eşitler**' ı seçin.

1. **Öznitelik eşleme** bölümünde Azure AD 'Den Tic-Tac Mobile 'a eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme işlemleri Için Tic-Tac Mobile 'daki Kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirirseniz, Tic-Tac Mobile API 'sinin kullanıcıları bu özniteliğe göre filtrelemeyi desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|
   |---|---|
   |userName|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |externalId|Dize|
   |başlık|Dize|
   |emails[type eq "work"].value|Dize|
   |preferredLanguage|Dize|
   |externalId|Dize|
   |userType|Dize|
   |locale|Dize|
   |timezone|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: employeeNumber|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: costCenter|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Organization|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Bölüm|Dize|
   |urn: IETF: params: Scim: schemas: Extension: Enterprise: 2.0: User: Department|Dize|

1. Kapsam filtrelerini yapılandırmak için [kapsam filtresi öğreticisindeki](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)yönergelere bakın.

1. Tic-Tac Mobile için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Sağlama durumunu açık olarak gösteren ekran görüntüsü.](common/provisioning-toggle-on.png)

1. **Ayarlar** bölümünde **kapsam** Içindeki istenen değerleri seçerek mobil Tic-Tac sağlamak istediğiniz kullanıcıları veya grupları tanımlayın.

    ![Sağlama kapsamını gösteren ekran görüntüsü.](common/provisioning-scope.png)

1. Sağlamaya hazırsanız **Kaydet**' i seçin.

    ![Sağlama yapılandırmasının kaydedilmesini gösteren ekran görüntüsü.](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer.

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme

Sağlamayı yapılandırdıktan sonra, dağıtımınızı izlemek için aşağıdaki kaynakları kullanın.

1. Hangi kullanıcıların başarıyla sağlandığını veya başarısız olduğunu öğrenmek için [sağlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın.
1. Sağlama döngüsünün durumunu ve ne kadar kapanmasının tamamlanmasının nasıl yapıldığını görmek için [ilerleme çubuğunu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) kontrol edin.
1. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumları hakkında daha fazla bilgi edinmek için bkz. [karantina durumunda uygulama hazırlama](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)