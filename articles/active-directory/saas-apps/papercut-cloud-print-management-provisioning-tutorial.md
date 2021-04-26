---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Paperkes bulut yazdırma yönetimini yapılandırın | Microsoft Docs'
description: Azure AD 'den otomatik olarak Kullanıcı hesaplarını nasıl sağlayacağınızı ve serbest bırakma hakkında bilgi edinmek için bkz. bulut yazdırma yönetimi.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 7e65d727-2951-4aec-a7a3-7bde49ed09e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: c0738e7033f0aa39ca5f12a44c06d086175eb8fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731491"
---
# <a name="tutorial-configure-papercut-cloud-print-management-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için Paperkes bulut yazdırma yönetimini yapılandırın

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem Paperkes bulut yazdırma yönetimi hem de Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, Azure AD sağlama hizmeti 'ni kullanarak [bulut yazdırma yönetimini](https://www.papercut.com/products/papercut-pocket/) otomatik olarak hazırlar ve serbest hazırlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Desteklenen yetenekler

> [!div class="checklist"]
> * Paperkes bulut yazdırma yönetiminde Kullanıcı oluşturma
> * Artık erişim gerektirdiklerinde Paperkes bulut yazdırma yönetiminde kullanıcıları kaldırma
> * Azure AD ile Paperkes bulut yazdırma yönetimi arasında kullanıcı özniteliklerini eşitlenmiş olarak tut

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Azure AD kiracısı](../develop/quickstart-create-new-tenant.md). 
* Azure AD 'de sağlamayı yapılandırma [izni](../roles/permissions-reference.md) olan bir kullanıcı hesabı (örneğin, uygulama Yöneticisi, bulut uygulaması Yöneticisi, uygulama sahibi veya genel yönetici). 
* Bir PaperCut bulut yazdırma yönetimi yönetici hesabı.


## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama

1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD Ile Paperkes bulut yazdırma yönetimi arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini belirleme.

## <a name="step-2-configure-papercut-cloud-print-management-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için Paperkes bulut yazdırma yönetimini yapılandırma

1. [Pocket Yönetici Konsolu](https://pocket.papercut.com/) 'nda oturum açın veya [Hive Yönetici Konsolu 'nu kesin](https://hive.papercut.com/).

2. Eklentiler tüm eklentiler ' **e gidin**  >  ve **Microsoft Azure AD Kullanıcı eşitleme eklentisi**' ni bulun.

3. **Daha fazla bilgi** düğmesine tıklayın ve yüklemek için **Ekle** 'ye tıklayın.



4. Yüklendikten sonra, **KIRACı URL** 'Niz ve **gizli belirteçle** bir eklenti ayrıntıları sayfası gösterilir. Bu değerler, \* \* Azure Portal, Paperkes bulut yazdırma yönetimi uygulamanızın sağlama sekmesinde bulunan kiracı URL 'si alanına ve gizli belirteç alanına girilecektir.



## <a name="step-3-add-papercut-cloud-print-management-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD Uygulama Galerisi 'nden Paperkes bulut yazdırma yönetimi ekleme

Azure AD Uygulama Galerisi 'nden Paperkes bulut yazdırma yönetimi ekleyerek, bir bulut yazdırma yönetimi için sağlamayı yönetmeye başlayın. Daha önce, diğer bir deyişle, daha önce paperiçin bulut yazdırma yönetimini ayarladıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama

Azure AD hazırlama hizmeti, uygulama atamasına veya kullanıcının/grubun özniteliklerine göre hazırlanacak kişilerin kapsamını belirlemenizi sağlar. Uygulamanız için hazırlanacak kişilerin kapsamını atamaya göre belirlemeyi seçerseniz kullanıcıları ve grupları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Hazırlanacak kişilerin kapsamını yalnızca kullanıcı veya grup özniteliklerine göre belirlemeyi seçerseniz [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) anlatılan kapsam belirleme filtresini kullanabilirsiniz.

* Kullanıcı ve grupları Paperkes bulut yazdırma yönetimine atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz.

* Başlangıçta kapsamı sınırlı tutun. Herkesi hazırlamadan önce birkaç kullanıcı ve grupla test yapın. Hazırlama kapsamı atanan kullanıcılar ve gruplar olarak ayarlandığında uygulamaya bir veya iki kullanıcı ya da grup atayarak bu adımı kontrol edebilirsiniz. Kapsam tüm kullanıcılar ve gruplar olarak ayarlandığında [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) belirtebilirsiniz. 

## <a name="step-5-configure-automatic-user-provisioning-to-papercut-cloud-print-management"></a>5. Adım. Bulut yazdırma yönetimini kesmek için otomatik Kullanıcı sağlamayı yapılandırın

Bu bölüm, Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve/veya grup atamalarını temel alan Paperkes bulut yazdırma yönetimi 'nde kullanıcıları ve/veya grupları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında size rehberlik eder.

### <a name="to-configure-automatic-user-provisioning-for-papercut-cloud-print-management-in-azure-ad"></a>Azure AD 'de Paperkes bulut yazdırma yönetimi için otomatik Kullanıcı sağlamayı yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

   ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Paperkes bulut yazdırma yönetimi**' ni seçin.

   ![Uygulamalar listesindeki Paperkes bulut yazdırma yönetimi bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

   ![Hazırlama sekmesi](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

   ![Sekme otomatik sağlama](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, Paperkes bulut yazdırma yönetimi kiracı URL 'Si ve gizli belirteç girin. Azure AD 'nin Paperkes bulut yazdırma yönetimine bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Paperkesilen bulut yazdırma yönetimi hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

   ![Belirteç](common/provisioning-testconnection-tenanturltoken.png)

6. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

   ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları sağla**' yı seçin.
   ![AAD eşleme](media/papercut-cloud-print-management-provisioning-tutorial/mapping.png)

9. **Öznitelik eşleme** bölümünde, Azure AD 'den Paper, bulut yazdırma yönetimi ile eşitlenen Kullanıcı özniteliklerini inceleyin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Paperkes bulut yazdırma yönetimi 'ndeki Kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, paperkes 'in, bu özniteliğe göre kullanıcıların filtrelemesini desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|Filtreleme için destekleniyor|
   |---|---|---|
   |userName|Dize|&check;|
   |displayName|Dize|
   |emails[type eq "work"].value|Dize|

10. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

11. Azure AD sağlama hizmetini Paperkes bulut yazdırma yönetimi için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** içinde istenen değerler ' i seçerek bulut yazdırma yönetimi 'ni kesmek için sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

13. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

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