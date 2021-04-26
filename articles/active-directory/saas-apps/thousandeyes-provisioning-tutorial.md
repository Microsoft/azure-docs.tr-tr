---
title: 'Öğretici: ThousandEyes için Kullanıcı hazırlama-Azure AD'
description: ThousandEyes 'ye Kullanıcı hesaplarını otomatik olarak sağlamak ve sağlamak üzere Azure Active Directory yapılandırmayı öğrenin.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: fcb53dc22cfb4bf7308b92ee76e5aaaf3bb0dead
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735124"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı hazırlama için ThousandEyes yapılandırma

Bu öğreticinin amacı, Azure AD 'den ThousandEyes 'ye Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için ThousandEyes ve Azure AD 'de gerçekleştirmeniz gereken adımları gösteriyoruz. 

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide anlatılan senaryoda aşağıdakilere sahip olduğunuz kabul edilmiştir:

* Azure Active Directory kiracısı
* [Standart plan](https://www.thousandeyes.com/pricing) veya daha iyi etkinleştirilmiş bir ThousandEyes kiracısı 
* Yönetici izinlerine sahip ThousandEyes 'de bir kullanıcı hesabı 

> [!NOTE]
> Azure AD sağlama tümleştirmesi, standart plandaki veya daha iyi ThousandEyes takımlar tarafından kullanılabilen [ThousandEyes SCIM API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK)'sini kullanır.

## <a name="assigning-users-to-thousandeyes"></a>ThousandEyes 'e Kullanıcı atama

Azure Active Directory, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyebilmek için "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı hesabı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya "atanmış" olan kullanıcılar ve gruplar eşitlenir. 

Sağlama hizmetini yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların ThousandEyes uygulamanıza erişmesi gereken kullanıcıları temsil ettiğini belirlemeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları ThousandEyes uygulamanıza atayabilirsiniz:

[Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>ThousandEyes 'e Kullanıcı atamaya yönelik önemli ipuçları

* Sağlama yapılandırmasını test etmek için ThousandEyes 'e tek bir Azure AD kullanıcısının atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

* Bir kullanıcıyı ThousandEyes 'e atarken, atama iletişim kutusunda **Kullanıcı** rolünü veya geçerli uygulamaya özgü bir rolü (varsa) seçmeniz gerekir. **Varsayılan erişim** rolü sağlama için çalışmaz ve bu kullanıcılar atlanır.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>ThousandEyes için Kullanıcı sağlamayı yapılandırma 

Bu bölümde, Azure AD 'nizi ThousandEyes 'in Kullanıcı hesabı sağlama API 'sine bağlama ve sağlama hizmetini, Azure AD 'de Kullanıcı ve grup atamasını temel alan ThousandEyes içindeki atanmış kullanıcı hesaplarını oluşturmak, güncelleştirmek ve devre dışı bırakmak için yapılandırma adımları kılavuzluk eder.

> [!TIP]
> Ayrıca, [Azure Portal](https://portal.azure.com)sağlanan yönergeleri Izleyerek, THOUSANDEYES için SAML tabanlı tek Sign-On de seçebilirsiniz. Çoklu oturum açma özelliği otomatik sağlanmadan bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Azure AD 'de ThousandEyes 'e otomatik Kullanıcı hesabı sağlamayı yapılandırma

1. [Azure Portal](https://portal.azure.com) oturum açın. **Kurumsal Uygulamalar**'ı ve ardından **Tüm uygulamalar**'ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Çoklu oturum açma için ThousandEyes zaten yapılandırdıysanız, arama alanını kullanarak ThousandEyes örneğinizi arayın. Aksi takdirde, **Ekle** ' yi seçin ve uygulama galerisinde **ThousandEyes** için arama yapın. Arama sonuçlarından ThousandEyes ' ı seçin ve uygulama listenize ekleyin.

    ![Uygulamalar listesindeki ThousandEyes bağlantısı](common/all-applications.png)
    
3. ThousandEyes örneğinizi seçin, sonra **sağlama** sekmesini seçin.

    ![Hazırlama sekmesi](common/provisioning.png)

4. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın.

![Ekran görüntüsü sağlama modu için otomatik seçiliyken ThousandEyes için sağlama sekmesini gösterir.](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)
    

5. **Yönetici kimlik bilgileri** bölümünde, ThousandEyes hesabı tarafından oluşturulan **OAuth taşıyıcı belirtecini** girin (ThousandEyes hesabı **profiliniz** bölümünde bir belirteç bulabilir ve oluşturabilirsiniz).

    ![Ekran görüntüsü, geçerli hesap grubu için hesap ayarları bağlantısının nerede bulunacağını gösterir.](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. Azure portal, Azure AD 'nin ThousandEyes uygulamanıza bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, ThousandEyes hesabınızın yönetici izinlerine sahip olduğundan emin olun ve 5. adımı yeniden deneyin.

7. **Bildirim E-postası** alanına hazırlama hatası bildirimlerinin gönderilmesini istediğiniz kişinin veya grubun e-posta adresini yazıp **Hata oluştuğunda e-posta bildirimi gönder** onay kutusunu seçin.

    ![Bildirim E-postası](common/provisioning-notification-email.png)

8. **Kaydet**’e tıklayın.

9. Eşlemeler bölümünde **Azure Active Directory Kullanıcıları ThousandEyes olarak eşitler**' ı seçin.

10. **Öznitelik eşleme** bölümünde Azure AD 'den ThousandEyes 'e eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri için ayrıştırılabilir ' deki Kullanıcı hesaplarını eşleştirmek için kullanılır. [Eşleşen hedef özniteliğini](../app-provisioning/customize-application-attributes.md)değiştirmeyi seçerseniz, AYRıŞTıRıLABILIR API 'nin bu özniteliğe göre kullanıcıların filtrelenmesini desteklediğinden emin olmanız gerekir. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.

     |Öznitelik|Tür|Filtreleme için destekleniyor|
     |---|---|---|
     |externalId|Dize|&check;|
     |userName|Dize|&check;|
     |active|Boole|
     |displayName|Dize|
     |emails[type eq "work"].value|Dize|
     |ad. biçimlendirildi|Dize|


11. Kapsam belirleme filtrelerini yapılandırmak için [Kapsam belirleme filtresi öğreticisi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) ile sunulan yönergeleri izleyin.

12. ThousandEyes için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin.

    ![Hazırlama Durumu Açık](common/provisioning-toggle-on.png)

13. **Ayarlar** bölümünde **kapsam** Içindeki istenen değerleri seçerek ThousandEyes için sağlamak istediğiniz kullanıcıları ve/veya grupları tanımlayın.

    ![Hazırlama Kapsamı](common/provisioning-scope.png)

14. Hazırlama işlemini başlatmak için **Kaydet**'e tıklayın.

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