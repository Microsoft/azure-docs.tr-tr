---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için GitHub kurumsal yönetilen kullanıcısını yapılandırın | Microsoft Docs'
description: Azure AD 'den GitHub kurumsal yönetilen kullanıcısına otomatik olarak Kullanıcı hesapları sağlamayı ve sağlamayı nasıl sağlayacağınızı öğrenin.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6aee39c7-08a1-4110-b936-4c85d129743b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
ms.openlocfilehash: cbae87a005240c15a2c3c28dcb8ab126d9957ba6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801239"
---
# <a name="tutorial-configure-github-enterprise-managed-user-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için GitHub kurumsal yönetilen kullanıcısını yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için GitHub kurumsal yönetilen Kullanıcı ve Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmeti 'ni kullanarak kullanıcıları ve grupları GitHub kurumsal yönetilen Kullanıcı tarafından otomatik olarak sağlar ve geçersiz bir şekilde hazırlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Desteklenen yetenekler
> [!div class="checklist"]
> * GitHub kurumsal yönetilen Kullanıcı içinde Kullanıcı oluşturma
> * GitHub kurumsal yönetilen kullanıcıdaki kullanıcıları artık erişim gerektirdiklerinde kaldırın
> * Kullanıcı özniteliklerinin Azure AD ile GitHub kurumsal yönetilen kullanıcısı arasında eşitlenmiş olmasını sağlama
> * GitHub kurumsal yönetilen Kullanıcı 'da grupları ve grup üyeliklerini sağlama
> * GitHub kurumsal yönetilen Kullanıcı için çoklu oturum açma (önerilir)

> [!NOTE]
> Bu sağlama Bağlayıcısı yalnızca kurumsal yönetilen kullanıcılar Beta katılımcıları için etkinleştirilmiştir.


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md)
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı (örneğin, uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici).
* Kurumsal olarak yönetilen kullanıcılar GitHub Enterprise 'ı etkinleştirdi ve Azure AD kiracınız aracılığıyla SAML SSO ile oturum açmak üzere yapılandırılmıştır.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD Ile GitHub kurumsal yönetilen kullanıcısı arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın.

## <a name="step-2-configure-github-enterprise-managed-user-to-support-provisioning-with-azure-ad"></a>Adım 2. GitHub kurumsal yönetilen kullanıcıyı Azure AD ile sağlamayı destekleyecek şekilde yapılandırma

1. Kiracı URL 'SI `https://api.github.com/scim/v2/enterprises/{enterprise}` . Bu değer, Azure portal GitHub kurumsal yönetilen kullanıcı uygulamanızın sağlama sekmesinde kiracı URL 'SI alanına girilecektir.

2. GitHub kurumsal yönetilen yönetici olarak sağ üst köşeye gidin > profil fotoğrafınızı tıklatın > ardından **Ayarlar**' a tıklayın.

3. Sol kenar çubuğunda, **Geliştirici ayarları**' na tıklayın.

4. Sol kenar çubuğunda **kişisel erişim belirteçleri**' ne tıklayın.

5. **Yeni belirteç oluştur**' a tıklayın.

6. Bu belirteç için **yönetici: Kurumsal** kapsamını seçin.

7. **Belirteç oluştur**' a tıklayın.

8. **Gizli belirteç** kopyalayın ve kaydedin. Bu değer, Azure portal GitHub kurumsal yönetilen kullanıcı uygulamanızın sağlama sekmesindeki gizli belirteç alanına girilir.

## <a name="step-3-add-github-enterprise-managed-user-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden GitHub kurumsal yönetilen Kullanıcı ekleme

GitHub kurumsal yönetilen kullanıcısına sağlamayı yönetmeye başlamak için Azure AD uygulama galerisinden GitHub kurumsal yönetilen kullanıcı ekleyin. Daha önce GitHub kurumsal yönetilen kullanıcısını SSO için ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz.

* GitHub kurumsal yönetilen kullanıcısına Kullanıcı ve grup atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir.

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz.


## <a name="step-5-configure-automatic-user-provisioning-to-github-enterprise-managed-user"></a>5. Adım. GitHub kurumsal yönetilen kullanıcısına otomatik Kullanıcı sağlamayı yapılandırma

Bu bölümde, Azure AD sağlama hizmeti 'ni kullanarak TestApp içindeki kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak için Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan bir adım adım yol gösterir.

### <a name="to-configure-automatic-user-provisioning-for-github-enterprise-managed-user-in-azure-ad"></a>Azure AD 'de GitHub kurumsal yönetilen kullanıcısının otomatik Kullanıcı sağlamasını yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **GitHub kurumsal yönetilen Kullanıcı**' yı seçin.

    ![Uygulamalar listesindeki GitHub kurumsal yönetilen Kullanıcı bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Hazırlama sekmesi](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Sekme otomatik sağlama](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde GitHub kurumsal yönetilen Kullanıcı kiracı URL 'Si ve gizli belirteç girin. Azure AD 'nin GitHub kurumsal yönetilen kullanıcısına bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, GitHub kurumsal yönetilen Kullanıcı hesabınızın gizli belirteci bir kurumsal sahip olarak oluşturduğunuzdan emin olun ve yeniden deneyin.

    ![Belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları GitHub kurumsal yönetilen Kullanıcı**' ya eşitler ' ı seçin.

9. **Öznitelik eşleme** bölümünde Azure AD 'Den GitHub kurumsal yönetilen kullanıcısına eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için GitHub kurumsal yönetilen kullanıcısının kullanıcı hesaplarıyla eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, GitHub kurumsal yönetilen Kullanıcı API 'sinin, bu özniteliğe göre kullanıcıların filtrelenmesini desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|Filtreleme Için destekleniyor|
   |---|---|---|
   |externalId|Dize|&check;|
   |userName|Dize|
   |active|Boole|
   |roller|Dize|
   |displayName|Dize|
   |name.givenName|Dize|
   |name.familyName|Dize|
   |ad. biçimlendirildi|Dize|
   |emails[type eq "work"].value|Dize|
   |e-postalar [tür EQ "ana"]. değer|Dize|
   |e-postalar [tür EQ "Other"]. değer|Dize|

10. **Eşlemeler** bölümünde, **Azure Active Directory grupları GitHub kurumsal yönetilen Kullanıcı olarak eşitler**' ı seçin.

11. **Öznitelik eşleme** bölümünde Azure AD 'Den GitHub kurumsal yönetilen kullanıcısına eşitlenen grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için GitHub kurumsal yönetilen kullanıcısının gruplarıyla eşleştirmek için kullanılır. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

      |Öznitelik|Tür|Filtreleme Için destekleniyor|
      |---|---|---|
      |externalId|Dize|&check;|
      |displayName|Dize|
      |üyeler|Başvuru|

12. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

13. GitHub kurumsal yönetilen Kullanıcı için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

14. **Ayarlar** bölümünde **kapsam** Içindeki Istenen değerleri seçerek GitHub kurumsal yönetilen kullanıcısına sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

15. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümündeki **Kapsam** alanında tanımlanan tüm kullanıcılar ve gruplar için ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer.

## <a name="step-6-monitor-your-deployment"></a>6. Adım. Dağıtımınızı izleme
Hazırlama ayarlarını yapılandırdıktan sonra dağıtımınızı izlemek için aşağıdaki kaynakları kullanın:

1. Hazırlama işlemi başarılı ve başarısız olan kullanıcıları belirlemek için [hazırlama günlüklerini](../reports-monitoring/concept-provisioning-logs.md) kullanın
2. Hazırlama döngüsünün durumunu ve tamamlanması için kalan miktarı görmek için [ilerleme çubuğuna](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) bakın
3. Hazırlama yapılandırmasının durumu iyi görünmüyorsa uygulama karantinaya geçer. Karantina durumu hakkında daha fazla bilgi edinmek için [buraya](../app-provisioning/application-provisioning-quarantine-status.md) bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Hazırlama etkinliği günlüklerini incelemeyi ve rapor oluşturmayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md)