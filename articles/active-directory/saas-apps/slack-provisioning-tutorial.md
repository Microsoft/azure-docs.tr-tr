---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için bolluk yapılandırma | Microsoft Docs'
description: Kullanıcı hesaplarını bolluk 'e otomatik olarak sağlamak ve devre dışı bırakmak için Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a294254bd52db89179c5644ea7a0f0f04027f30
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932483"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Öğretici: Otomatik Kullanıcı sağlaması için bolluk yapılandırma

Bu öğreticinin amacı, Azure AD 'den bolluk 'ye Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için bolluk ve Azure AD 'de gerçekleştirmeniz gereken adımları gösteriyoruz.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide özetlenen senaryo, aşağıdaki öğelerin zaten olduğunu varsayar:

* Azure Active Directory kiracısı
* [Artı planı](https://aadsyncfabric.slack.com/pricing) veya daha iyi etkinleştirilmiş bir bolluk kiracısı
* Ekip Yöneticisi izinleri ile bolluk içinde bir kullanıcı hesabı

Not: Azure AD sağlama tümleştirmesi, artı planı veya daha iyi bir deyişle bolluk ekipleri tarafından kullanılabilen [bolluk SCIM API](https://api.slack.com/scim)'sini kullanır.

## <a name="assigning-users-to-slack"></a>Kullanıcıları bolluk 'e atama

Azure Active Directory, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyebilmek için "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı hesabı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya "atanmış" olan kullanıcılar ve gruplar eşitlenir.

Sağlama hizmetini yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların bolluk uygulamanıza erişmesi gereken kullanıcıları temsil ettiğini belirlemeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları bolluk uygulamanıza atayabilirsiniz:

[Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Bolluk 'e Kullanıcı atamaya yönelik önemli ipuçları

* Sağlama yapılandırmasını test etmek için tek bir Azure AD kullanıcısına bolluk atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı bolluk 'e atarken, atama iletişim kutusunda **Kullanıcı** veya "Grup" rolünü seçmeniz gerekir. "Varsayılan erişim" rolü sağlama için çalışmaz.

## <a name="configuring-user-provisioning-to-slack"></a>Kullanıcı sağlamayı bolluk olarak yapılandırma 

Bu bölümde, Azure AD 'nizi bolluk 'in Kullanıcı hesabı sağlama API 'sine bağlama ve sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve grup atamasına göre bolluk içinde atanan kullanıcı hesaplarını oluşturmak, güncelleştirmek ve devre dışı bırakmak için yapılandırma işlemi kılavuzluk eder.

**İpucu:** Ayrıca, [Azure Portal](https://portal.azure.com)' de sağlanan yönergeleri izleyerek bolluk için SAML tabanlı çoklu oturum açma 'yı da tercih edebilirsiniz. Çoklu oturum açma özelliği otomatik sağlanmadan bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Azure AD 'de otomatik Kullanıcı hesabı sağlamayı bolluk olarak yapılandırmak için:

1. [Azure portal](https://portal.azure.com) **Azure Active Directory > Enterprise Apps > tüm uygulamalar** bölümüne gidin.

2. Çoklu oturum açma için bolluk 'i zaten yapılandırdıysanız arama alanını kullanarak bolluk örneğinizi arayın. Aksi takdirde, **Ekle** ' yi seçin ve uygulama galerisinde **bolluk** araması yapın. Arama sonuçlarından bolluk ' i seçin ve uygulama listenize ekleyin.

3. Bolluk örneğinizi seçin, sonra **sağlama** sekmesini seçin.

4. **Sağlama modunu** **Otomatik**olarak ayarlayın.

   ![Bolluk sağlama](./media/slack-provisioning-tutorial/Slack1.PNG)

5. **Yönetici kimlik bilgileri** bölümünde **Yetkilendir**' e tıklayın. Bu, yeni bir tarayıcı penceresinde bir bolluk yetkilendirme iletişim kutusu açar.

6. Yeni pencerede, ekip yönetici hesabınızı kullanarak bolluk içinde oturum açın. elde edilen yetkilendirme iletişim kutusunda, sağlamayı etkinleştirmek istediğiniz bolluk ekibini seçin ve sonra **Yetkilendir**' i seçin. Tamamlandıktan sonra sağlama yapılandırmasını gerçekleştirmek için Azure portal döndürün.

    ![Yetkilendirme Iletişim kutusu](./media/slack-provisioning-tutorial/Slack3.PNG)

7. Azure portal, Azure AD 'nin bolluk uygulamanıza bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, bolluk hesabınızda Takım Yöneticisi izinlerine sahip olduğundan emin olun ve "Yetkilendir" adımını yeniden deneyin.

8. **Bildirim e-postası** alanında sağlama hatası bildirimleri alması gereken bir kişinin veya grubun e-posta adresini girin ve aşağıdaki onay kutusunu işaretleyin.

9. **Kaydet**’e tıklayın.

10. Eşlemeler bölümünde **Azure Active Directory Kullanıcıları bolluğu ' ne kadar yap**' ı seçin.

11. **Öznitelik eşlemeleri** bölümünde, Azure AD 'den bolluk 'e eşitlenecek Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen özniteliklerin, güncelleştirme Işlemleri için bolluk içindeki kullanıcı hesaplarıyla eşleşmesi için kullanılacağını unutmayın. Değişiklikleri kaydetmek için Kaydet düğmesini seçin.

12. Azure AD sağlama hizmetini bolluk için etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin

13. **Kaydet**’e tıklayın.

Bu, kullanıcılar ve Gruplar bölümünde bolluk 'e atanan tüm Kullanıcı ve/veya grupların ilk eşitlemesini başlatır. İlk eşitlemenin daha sonra, hizmetin çalıştığı sürece yaklaşık 10 dakikada bir sonraki eşitlemeler tarafından gerçekleştirilmesi daha uzun sürer. İşlem ilerlemesini izlemek ve sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri bolluk uygulamanızda açıklayan etkinlik raporlarını sağlamak için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>Seçim Grup nesnesi sağlamayı bolluk olarak yapılandırma

İsteğe bağlı olarak, Azure AD 'den bolluk 'e Grup nesneleri sağlamayı etkinleştirebilirsiniz. Bu, üyelerine ek olarak gerçek grup nesnesinin Azure AD 'den bolluk 'e çoğaltılacağı "Kullanıcı grupları atama" işleminden farklıdır. Örneğin, Azure AD 'de "grubum" adlı bir grubunuz varsa, bolluk içinde "My Group" adlı özdeş bir grup oluşturulur.

### <a name="to-enable-provisioning-of-group-objects"></a>Grup nesnelerinin sağlamasını etkinleştirmek için:

1. Eşlemeler bölümünde **Azure Active Directory gruplarını bolluk olarak eşitler**' ı seçin.

2. Öznitelik eşleme dikey penceresinde, etkin ' i Evet olarak ayarlayın.

3. **Öznitelik eşlemeleri** bölümünde, Azure AD 'den bolluk 'e eşitlenecek grup özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen özniteliklerin, güncelleştirme Işlemleri için bolluk içindeki grupları eşleştirmek için kullanılacağını unutmayın. 

4. **Kaydet**’e tıklayın.

Bu, **Kullanıcılar ve gruplar** bölümündeki bolluk 'e atanan tüm grup NESNELERININ Azure AD 'den bolluk 'e tam olarak eşitlendiği bir sonuç olarak sonuçlanır. İşlem ilerlemesini izlemek ve sağlama hizmeti tarafından gerçekleştirilen tüm eylemleri bolluk uygulamanızda açıklayan etkinlik günlüklerini sağlamak için **eşitleme ayrıntıları** bölümünü kullanabilirsiniz.

Azure AD günlüklerini sağlama okuma hakkında daha fazla bilgi için bkz. [hesabı otomatik kullanıcı hazırlama raporlama](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Bağlayıcı sınırlamaları

* Bolluk **DisplayName** özniteliğini yapılandırırken aşağıdaki davranışlardan haberdar olun:

  * Değerler tamamen benzersiz değildir (ör. 2 Kullanıcı aynı görünen ada sahip olabilir)

  * Ingilizce olmayan karakterleri, boşlukları, büyük harfleri destekler. 
  
  * İzin verilen noktalama noktalar, alt çizgiler, tireler, kesme işareti, köşeli ayraçlar (ör. **([{}])** ) ve ayırıcılar (ör. **,/;** ) içerir.
  
  * Yalnızca bu iki ayar, bolluk 'in çalışma alanında/kuruluşta yapılandırılmışsa, **profil eşitlemesi etkinleştirilir** ve **Kullanıcılar kendi görünen adlarını değiştiremezler**.
  
* Bolluk **Kullanıcı adı** özniteliği 21 karakterden oluşmalıdır ve benzersiz bir değere sahip olmalıdır.

* Bolluk yalnızca **Kullanıcı adı** ve **e-posta**öznitelikleriyle eşleştirmeye izin verir.  

## <a name="additional-resources"></a>Ek Kaynaklar

* [Kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
