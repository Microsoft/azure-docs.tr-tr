---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlaması için Coda yapılandırma | Microsoft Docs'
description: Azure AD 'den Coda 'ya Kullanıcı hesaplarını otomatik olarak sağlamayı ve sağlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: Zhchia
ms.openlocfilehash: 8df1588a78829252d55f82349d6889c754c989e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673237"
---
# <a name="tutorial-configure-coda-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Coda yapılandırma

Bu öğretici, otomatik Kullanıcı sağlamayı yapılandırmak için hem Coda hem de Azure Active Directory (Azure AD) içinde gerçekleştirmeniz gereken adımları açıklamaktadır. Yapılandırıldığında, Azure AD, kullanıcıları Azure AD sağlama hizmetini kullanarak [Coda](https://coda.io/) 'ya otomatik olarak sağlar ve hazırlar. Hizmetin işlevleri ve çalışma şekli hakkında daha fazla bilgi edinmek ve sık sorulan soruları incelemek için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Desteklenen yetenekler
> [!div class="checklist"]
> * CODA 'da Kullanıcı oluşturma
> * Artık erişim gerektirmeyen Coda kullanıcıları kaldırın
> * Azure AD ve Coda arasında kullanıcı özniteliklerinin eşitlenmiş olmasını sağlama
> * CODA ['Da çoklu oturum açma](./coda-tutorial.md) (önerilir)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki önkoşulların zaten olduğunu varsayar:

* [Bir Azure AD kiracısı](../develop/quickstart-create-new-tenant.md)
* Azure AD'de hazırlama [iznine](../roles/permissions-reference.md) sahip bir kullanıcı hesabı (Uygulama Yöneticisi, Bulut Uygulaması Yöneticisi, Uygulama Sahibi veya Genel Yönetici).
* Bir [Coda kurumsal](https://help.coda.io/en/articles/3520174-getting-started-with-sso) yönetici hesabı.

## <a name="step-1-plan-your-provisioning-deployment"></a>Adım 1. Hazırlama dağıtımınızı planlama
1. [Hazırlama hizmetinin nasıl çalıştığı](../app-provisioning/user-provisioning.md) hakkında bilgi edinin.
2. [Hazırlık kapsamına](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) dahil edilecek kullanıcıları seçin.
3. [Azure AD ve Coda arasında](../app-provisioning/customize-application-attributes.md)hangi verilerin eşlendiğini saptayın.

## <a name="step-2-configure-coda-to-support-provisioning-with-azure-ad"></a>Adım 2. Azure AD ile sağlamayı desteklemek için Coda yapılandırma

1. Kuruluş Yöneticisi konsolunuzu açın... altında kuruluş ayarları ' nı seçin. çalışma alanınızın altındaki menü.

    ![CODA kurumsal kuruluş SCıM ayarları](media/coda-provisioning-tutorial/coda-scim-enable.png)

2. SCıM ile sağlama özelliğinin etkinleştirildiğinden emin olun.
3. SCıM temel URL 'sini ve SCıM taşıyıcı belirtecini aklınızda yazın. Taşıyıcı belirteç yoksa, yeni belirteç oluştur ' a tıklayın.

## <a name="step-3-add-coda-from-the-azure-ad-application-gallery"></a>3. Adım Azure AD uygulama galerisinden Coda ekleme

Azure AD uygulama galerisinden CODA ' yı ekleyerek Coda sağlama ' ya bir başlangıç yapın. Daha önceden SSO için Coda kurulumunu yaptıysanız aynı uygulamayı kullanabilirsiniz. Ancak başlangıçta tümleştirmeyi test ederken ayrı bir uygulama oluşturmanız önerilir. Galeriden uygulama ekleme hakkında daha fazla bilgi için [buraya](../manage-apps/add-application-portal.md) bakın.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. Adım: Hazırlık kapsamına dahil edilecek kullanıcıları tanımlama

Azure AD sağlama hizmeti, uygulamaya atamaya ve Kullanıcı özniteliklerine göre sağlanacak olan kapsamınızı sağlar. Atamaya göre uygulamanıza sağlanacak kapsamı tercih ederseniz, kullanıcıları uygulamaya atamak için aşağıdaki [adımları](../manage-apps/assign-user-or-group-access-portal.md) kullanabilirsiniz. Yalnızca kullanıcının özniteliklerine göre sağlanacak olan kapsamı tercih ederseniz, [burada](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)açıklandığı gibi bir kapsam filtresi kullanabilirsiniz.

* CODA 'ya Kullanıcı atarken **varsayılan erişim** dışında bir rol seçmelisiniz. Varsayılan Erişim rolüne sahip kullanıcılar hazırlama kapsamından hariç tutulur ve hazırlama günlüklerinde yeterli yetkiye sahip olmadıkları belirtilir. Uygulama için kullanılabilen tek rol varsayılan erişim rolüyse [uygulama bildirimini güncelleştirerek](../develop/howto-add-app-roles-in-azure-ad-apps.md) daha fazla rol ekleyebilirsiniz.

* Başlangıçta kapsamı sınırlı tutun. Herkese sunulmadan önce küçük bir Kullanıcı kümesiyle test edin. Sağlama kapsamı atanan kullanıcılara ayarlandığında, uygulamaya bir veya iki Kullanıcı atayarak bunu kontrol edebilirsiniz. Kapsam tüm kullanıcılara ayarlandığında, [öznitelik tabanlı kapsam filtresi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)belirtebilirsiniz.


## <a name="step-5-configure-automatic-user-provisioning-to-coda"></a>5. Adım. CODA otomatik Kullanıcı sağlamasını yapılandırma

Bu bölümde, Azure AD sağlama hizmeti 'ni, Azure AD 'de Kullanıcı atamalarına göre TestApp 'deki kullanıcıları oluşturmak, güncelleştirmek ve devre dışı bırakmak üzere yapılandırma adımlarında kılavuzluk eder.

### <a name="to-configure-automatic-user-provisioning-for-coda-in-azure-ad"></a>Azure AD 'de Coda otomatik Kullanıcı sağlamasını yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Coda**' yı seçin.

    ![Uygulamalar listesindeki Coda bağlantısı](common/all-applications.png)

3. **Hazırlama** sekmesini seçin.

    ![Sağlama seçeneğinin kullanıma aldığı yönetim seçeneklerinin ekran görüntüsü.](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

    ![Otomatik seçeneği olarak adlandırılan sağlama modu açılan listesinin ekran görüntüsü.](common/provisioning-automatic.png)

5. **Yönetici kimlik bilgileri** bölümünde, 1. adımdaki Coda kiracı URL 'Nizi ve gizli anahtar belirtecinizi girin. Azure AD 'nin Coda 'ya bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Coda hesabınızın yönetici izinlerine sahip olduğundan emin olun ve yeniden deneyin.

     ![Ekran görüntüsü, kiracı U R L ve gizli belirteç girebileceğiniz yönetici kimlik bilgileri iletişim kutusunu gösterir.](./media/coda-provisioning-tutorial/provisioning.png)

6. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

7. **Kaydet**’i seçin.

8. **Eşlemeler** bölümünde **Azure Active Directory Kullanıcıları Coda**' ya eşitler ' ı seçin.

9. **Öznitelik eşleme** bölümünde Azure AD 'den Coda 'ya eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için Coda 'daki Kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, Coda API 'sinin kullanıcıları bu özniteliğe göre filtrelemeyi desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

   |Öznitelik|Tür|
   |---|---|
   |userName|Dize|
   |active|Boole|
   |name.givenName|Dize|
   |name.familyName|Dize|


10. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

11. CODA Azure AD sağlama hizmetini etkinleştirmek için, **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

12. **Ayarlar** bölümünde **kapsam** Içindeki istenen değerleri seçerek Coda sağlamak istediğiniz kullanıcıları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

13. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

    ![Hazırlama Yapılandırmasını Kaydetme](common/provisioning-configuration-save.png)

Bu işlem, **Ayarlar** bölümünde **kapsamda** tanımlanan tüm kullanıcıların ilk eşitleme döngüsünü başlatır. İlk döngünün tamamlanması, Azure AD hazırlama hizmetinin çalıştığı süre boyunca yaklaşık olarak 40 dakikada bir gerçekleştirilen sonraki döngülerden daha uzun sürer.

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