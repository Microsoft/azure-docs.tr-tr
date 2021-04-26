---
title: 'Öğretici: Azure Active Directory ile otomatik Kullanıcı sağlama için Concur yapılandırma | Microsoft Docs'
description: Azure Active Directory ve Concur arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: edb21287b30f8ba77d6312ec6b456e20aa260598
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358221"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Öğretici: otomatik Kullanıcı sağlaması için Concur yapılandırma

Bu öğreticinin amacı, Azure AD 'de Kullanıcı hesaplarını otomatik olarak sağlamak ve devre dışı bırakmak için Concur ve Azure AD 'de gerçekleştirmeniz gereken adımları gösterir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide anlatılan senaryoda aşağıdakilere sahip olduğunuz kabul edilmiştir:

*   Azure Active Directory kiracısı.
*   Concur çoklu oturum açma etkin aboneliği.
*   Ekip Yöneticisi izinlerine sahip bir kullanıcı hesabı.

## <a name="assigning-users-to-concur"></a>Kullanıcıları Concur atama

Azure Active Directory, hangi kullanıcıların seçili uygulamalara erişim alacağını belirleyebilmek için "atamalar" adlı bir kavram kullanır. Otomatik Kullanıcı hesabı sağlama bağlamında, yalnızca Azure AD 'de bir uygulamaya "atanmış" olan kullanıcılar ve gruplar eşitlenir.

Sağlama hizmetini yapılandırmadan ve etkinleştirmeden önce, Azure AD 'deki hangi kullanıcıların ve/veya grupların Concur uygulamanıza erişmesi gereken kullanıcıları temsil ettiğini belirlemeniz gerekir. Karar verdikten sonra buradaki yönergeleri izleyerek bu kullanıcıları Concur uygulamanıza atayabilirsiniz:

[Kurumsal uygulamaya Kullanıcı veya Grup atama](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-concur"></a>Kullanıcıları Concur 'e atamaya yönelik önemli ipuçları

*   Sağlama yapılandırmasını test etmek için tek bir Azure AD kullanıcısına yarışma atanması önerilir. Ek kullanıcılar ve/veya grupları daha sonra atanabilir.

*   Bir kullanıcıyı Concur 'e atarken geçerli bir kullanıcı rolü seçmeniz gerekir. "Varsayılan erişim" rolü sağlama için çalışmaz.

## <a name="enable-user-provisioning"></a>Kullanıcı sağlamayı etkinleştir

Bu bölümde, Azure AD 'nizi Concur Kullanıcı hesabı sağlama API 'sine bağlama ve sağlama hizmeti 'ni, Azure AD 'de Kullanıcı ve grup atamasına göre Concur ' de atanan kullanıcı hesaplarını oluşturmak, güncelleştirmek ve devre dışı bırakmak için yapılandırma işlemi kılavuzluk eder.

> [!Tip] 
> Ayrıca, [Azure Portal](https://portal.azure.com)sağlanan talimatları Izleyerek, Concur için SAML tabanlı tek Sign-On de seçebilirsiniz. Çoklu oturum açma özelliği otomatik sağlanmadan bağımsız olarak yapılandırılabilir, ancak bu iki özellik birbirini karmaşıdirebilirler.

### <a name="to-configure-user-account-provisioning"></a>Kullanıcı hesabı sağlamayı yapılandırmak için:

Bu bölümün amacı, Active Directory Kullanıcı hesaplarının hazırlığını sağlamayı nasıl etkinleştireceğinizi özetler.

Gider hizmetindeki uygulamaları etkinleştirmek için, bir Web hizmeti yönetici profilinin doğru bir şekilde kurulması ve kullanılması gerekir. WS yönetici rolünü T&E yönetim işlevleri için kullandığınız mevcut yönetici profiline eklemeyin.

Concur danışmanları veya istemci Yöneticisi farklı bir Web hizmeti yönetici profili oluşturmalı ve Istemci yöneticisinin Web Hizmetleri Yöneticisi işlevleri için bu profili kullanması gerekir (örneğin, uygulamaları etkinleştirme). Bu profillerin istemci yöneticisinin günlük T&E yönetici profilinden ayrı tutulması gerekir (T&E yönetici profilinde WSAdmin rolü atanmamalıdır).

Uygulamayı etkinleştirmek için kullanılacak profili oluştururken, Kullanıcı profili alanlarına istemci yöneticisinin adını girin. Bu, sahipliği profile atar. Bir veya daha fazla profil oluşturulduktan sonra, Web Hizmetleri menüsünde bir Iş ortağı uygulamasının "*Etkinleştir*" düğmesine tıklayarak istemcinin bu profille oturum açması gerekir.

Aşağıdaki nedenlerden dolayı bu eylem, normal T&E yönetimi için kullandıkları profille yapılmamalıdır.

* İstemci, bir uygulama etkinleştirildikten sonra görüntülenen iletişim penceresinde "*Evet*" i tıkladığı bir değer olmalıdır. Bu, istemcinin Iş ortağı uygulamasının verilerine erişmesini istediğinden emin olur, böylece siz veya Iş ortağı bu Evet düğmesine tıklamamasını sağlar.

* T&E yönetici profilini kullanarak bir uygulamayı etkinleştirmiş bir istemci yöneticisi şirketten ayrılırsa (profilin devre dışı bırakılmakta olması halinde), bu profil kullanılarak etkinleştirilen tüm uygulamalar, uygulama başka bir etkin WS yönetici profiliyle etkinleştirilene kadar çalışmaz. Bu nedenle ayrı WS Yönetici profilleri oluşturmanız gerekir.

* Bir yönetici şirketten ayrılırsa, bu profile izin verilmediğinden, WS yönetici profiliyle ilişkili ad, etkin uygulamayı etkilemeden istenirse değiştirme Yöneticisi olarak değiştirilebilir.

**Kullanıcı sağlamayı yapılandırmak için aşağıdaki adımları uygulayın:**

1. **Concur** kiracınızda oturum açın.

2. **Yönetim** menüsünden **Web Hizmetleri**' ni seçin.
   
    ![Concur kiracısı](./media/concur-provisioning-tutorial/IC721729.png "Concur kiracısı")

3. Sol tarafta, **Web Hizmetleri** bölmesinden **Iş ortağı uygulamasını etkinleştir**' i seçin.
   
    ![Iş ortağı uygulamasını etkinleştir](./media/concur-provisioning-tutorial/ic721730.png "Iş ortağı uygulamasını etkinleştir")

4. **Uygulamayı etkinleştir** listesinden **Azure Active Directory**' yi seçin ve ardından **Etkinleştir**' e tıklayın.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. **Eylemi Onayla** iletişim kutusunu kapatmak için **Evet** ' i tıklatın.
   
    ![Eylemi Onayla](./media/concur-provisioning-tutorial/ic721732.png "Eylemi Onayla")

6. [Azure portal](https://portal.azure.com) **Azure Active Directory > Enterprise Apps > tüm uygulamalar** bölümüne gidin.

7. Zaten çoklu oturum açma için Concur yapılandırdıysanız arama alanını kullanarak Concur örneğinizi arayın. Aksi takdirde, **Ekle** ' yi seçin ve uygulama galerisinde **Concur** araması yapın. Arama sonuçlarından Concur ' ı seçin ve uygulama listenize ekleyin.

8. Concur örneğinizi seçin ve **sağlama** sekmesini seçin.

9. **Hazırlama Modu**'nu **Otomatik** olarak ayarlayın. 
 
    ![Azure portal Concur için sağlama sekmesinin ekran görüntüsü. Sağlama modu otomatik olarak ayarlanır ve Bağlantıyı Sına düğmesi vurgulanır.](./media/concur-provisioning-tutorial/provisioning.png)

10. **Yönetici kimlik bilgileri** bölümü altında, **Kullanıcı adını** ve Concur yöneticinizin **parolasını** girin.

11. Azure portal Azure AD 'nin Concur uygulamanıza bağlanabildiğinden emin olmak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı başarısız olursa, Concur hesabınızın Takım Yöneticisi izinlerine sahip olduğundan emin olun.

12. **Bildirim e-postası** alanında sağlama hatası bildirimleri alması gereken bir kişinin veya grubun e-posta adresini girin ve onay kutusunu işaretleyin.

13. Kaydet ' e tıklayın **.**

14. Eşlemeler bölümünde **Azure Active Directory Kullanıcıları Concur olarak eşitler** ' ı seçin.

15. **Öznitelik eşlemeleri** bölümünde, Azure AD 'Den Concur ile eşitlenen Kullanıcı özniteliklerini gözden geçirin. **Eşleşen** özellikler olarak seçilen öznitelikler, güncelleştirme Işlemleri Için Concur içindeki kullanıcı hesaplarını eşleştirmek için kullanılır. Değişiklikleri uygulamak için Kaydet düğmesini seçin.

16. Concur için Azure AD sağlama hizmetini etkinleştirmek üzere **Ayarlar** bölümünde **sağlama durumunu** **Açık** olarak değiştirin

17. Kaydet ' e tıklayın **.**

Artık bir test hesabı oluşturabilirsiniz. Hesabın Concur ile eşitlendiğinden emin olmak için 20 dakikaya kadar bekleyin.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](tutorial-list.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)
* [Çoklu oturum açmayı yapılandırma](concur-tutorial.md)