---
title: B2B işbirliği kullanıcılarını bilgi çalışanı olarak ekleme-Azure AD
description: B2B işbirliği, bilgi işçileri ve uygulama sahiplerinin erişim için Azure AD 'ye Konuk kullanıcılar eklemesine olanak tanır | Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 345cbe2efb644fe2b84d4a6021ff962337e29285
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219921"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Kuruluşunuzdaki kullanıcıların bir uygulamaya Konuk kullanıcıları davet edebilir

Bir Konuk Kullanıcı Azure AD 'de dizine eklendikten sonra, bir uygulama sahibi Konuk kullanıcıyı paylaştırmak istedikleri uygulamaya doğrudan bir bağlantı gönderebilir. Azure AD yöneticileri, Azure AD kiracısındaki Galeri veya SAML tabanlı uygulamalar için self servis yönetimini de ayarlayabilir. Bu şekilde, Konuk kullanıcılar henüz dizine eklenmemiş olsa bile, uygulama sahipleri kendi Konuk kullanıcılarını yönetebilir. Bir uygulama self servis için yapılandırıldığında, uygulama sahibi Konuk kullanıcıyı bir uygulamaya davet etmek veya uygulamaya erişimi olan bir gruba Konuk Kullanıcı eklemek için erişim panelini kullanır. Galeri ve SAML tabanlı uygulamalar için self servis uygulama yönetimi, bir yönetici tarafından bazı ilk kurulumu gerektirir. Aşağıda, Kurulum adımlarının bir özeti verilmiştir (daha ayrıntılı yönergeler için bu sayfanın ilerleyen kısımlarında yer alan [Önkoşullar](#prerequisites) bölümüne bakın):

 - Kiracınız için Self Servis Grup yönetimini etkinleştirme
 - Uygulamaya atanacak bir grup oluşturun ve kullanıcıya bir sahip yapın
 - Uygulamayı self servis için yapılandırma ve grubu uygulamaya atama

> [!NOTE]
> * Bu makalede, Azure AD kiracınıza eklediğiniz Galeri ve SAML tabanlı uygulamalar için self servis yönetiminin nasıl ayarlanacağı açıklanır. Ayrıca, kullanıcılarınızın kendi Microsoft 365 gruplarına erişimi yönetebilmeleri için [self servis Microsoft 365 gruplarını da ayarlayabilirsiniz](../enterprise-users/groups-self-service-management.md) . Kullanıcıların Office dosyalarını ve uygulamalarını Konuk kullanıcılarla paylaşmasına yönelik daha fazla bilgi için bkz. [Microsoft 365 gruplardaki konuk erişimi](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) ve [SharePoint dosyalarını veya klasörlerini paylaşma](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).
> * Kullanıcılar yalnızca **konuk davet** eden rolüne sahip olmaları durumunda konukları davet edebilir.
## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Erişim panelinden bir uygulamaya Konuk Kullanıcı davet etme

Bir uygulama self servis için yapılandırıldıktan sonra, uygulama sahipleri bir konuk kullanıcıyı paylaştırmak istedikleri uygulamaya davet etmek için kendi erişim panelini kullanabilir. Konuk kullanıcının Azure AD 'ye önceden eklenmiş olması gerekmez. 

1. Uygulamasına giderek erişim masasını açın `https://myapps.microsoft.com` .
2. Uygulamanın üzerine gelin, üç nokta (**...**) simgesini seçin ve ardından **Uygulamayı Yönet**' i seçin.
 
   ![Salesforce uygulaması için uygulama yönetme alt menüsünü gösteren ekran görüntüsü](media/add-users-iw/access-panel-manage-app.png)
 
3. Kullanıcılar listesinin en üstünde öğesini seçin **+** .
   
   ![Uygulamaya üye eklemek için artı simgesini gösteren ekran görüntüsü](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. **Üye Ekle** arama kutusuna Konuk kullanıcının e-posta adresini yazın. İsteğe bağlı olarak bir hoş geldiniz iletisi ekleyin.
   
   ![Konuk eklemek için üye Ekle penceresini gösteren ekran görüntüsü](media/add-users-iw/access-panel-invitation.png)
   
5. Konuk kullanıcıya davetiye göndermek için **Ekle** ' yi seçin. Daveti göndermenizin ardından kullanıcı hesabı otomatik olarak dizine konuk olarak eklenir.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Bir kişiyi uygulamaya erişimi olan bir gruba katılmaya davet etme
Bir uygulama self servis için yapılandırıldıktan sonra, uygulama sahipleri Konuk kullanıcıları, paylaştığı uygulamalara erişimi olan yönettikleri gruplara davet edebilir. Konuk kullanıcıların dizinde zaten var olması gerekmez. Uygulama sahibi, uygulamaya erişebilmek için bir konuk kullanıcıyı gruba davet etmek için aşağıdaki adımları izler.

1. Paylaşmak istediğiniz uygulamaya erişimi olan Self Servis grubunun sahibi olduğunuzdan emin olun.
2. Uygulamasına giderek erişim masasını açın `https://myapps.microsoft.com` .
3. **Gruplar** uygulamasını seçin.
   
   ![Erişim panelinde Gruplar uygulamasını gösteren ekran görüntüsü](media/add-users-iw/access-panel-groups.png)
   
4. **Sahipim olan gruplar** altında, paylaştırmak istediğiniz uygulamaya erişimi olan grubu seçin.
   
   ![Sahip olduğu gruplar altında bir grubun nerede seçileceğini gösteren ekran görüntüsü](media/add-users-iw/access-panel-groups-i-own.png)
   
5. Grup üyeleri listesinin en üstünde öğesini seçin **+** .
   
   ![Gruba üye eklemek için artı simgesini gösteren ekran görüntüsü](media/add-users-iw/access-panel-groups-add-member.png)
   
6. **Üye Ekle** arama kutusuna Konuk kullanıcının e-posta adresini yazın. İsteğe bağlı olarak bir hoş geldiniz iletisi ekleyin.
   
   ![Konuk eklemek için üye Ekle penceresini gösteren ekran görüntüsü](media/add-users-iw/access-panel-invitation.png)
   
7. Daveti Konuk kullanıcıya otomatik olarak göndermek için **Ekle** ' yi seçin. Daveti göndermenizin ardından kullanıcı hesabı otomatik olarak dizine konuk olarak eklenir.


## <a name="prerequisites"></a>Önkoşullar

Self Servis uygulama yönetimi, bir genel yönetici ve bir Azure AD yöneticisi tarafından bazı ilk kurulum gerektirir. Bu kurulumun bir parçası olarak, uygulamayı self servis için yapılandıracaksınız ve uygulama sahibinin yönetebileceği uygulamaya bir grup atamalısınız. Ayrıca grubu, herkesin üyelik istemesine izin verecek ancak Grup sahibinin onayını gerektirme amacıyla de yapılandırabilirsiniz. ( [Self servis grup yönetimi](../enterprise-users/groups-self-service-management.md)hakkında daha fazla bilgi edinin.) 

> [!NOTE]
> Konuk kullanıcıları dinamik bir gruba veya şirket içi Active Directory eşitlenen bir gruba ekleyemezsiniz.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Kiracınız için Self Servis Grup yönetimini etkinleştirme
1. [Azure Portal](https://portal.azure.com) genel yönetici olarak oturum açın.
2. Gezinti panelinde **Azure Active Directory**' yi seçin.
3. **Grupları** seçin.
4. **Ayarlar** altında **genel**' i seçin.
5. **Self servis grup yönetimi** altında, sahipler ' in yanında, **erişim panelinde grup üyeliği Isteklerini yönetebilir**, **Evet**' i seçin.
6. **Kaydet**’i seçin.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Uygulamaya atanacak bir grup oluşturun ve kullanıcıya bir sahip yapın
1. [Azure Portal](https://portal.azure.com) Azure AD yöneticisi veya genel yönetici olarak oturum açın.
2. Gezinti panelinde **Azure Active Directory**' yi seçin.
3. **Grupları** seçin.
4. **Yeni Grup**' u seçin.
5. **Grup türü** altında **güvenlik**' i seçin.
6. Bir **Grup adı** ve **Grup açıklaması** girin.
7. **Üyelik türü**' nün altında, **atandı**' ı seçin.
8. **Oluştur**' u seçin ve **Grup** sayfasını kapatın.
9. **Gruplar-tüm gruplar** sayfasında, grubu açın. 
10. **Yönet** altında **sahipler** sahipler ' i seçin  >  . Uygulamaya erişimi yönetmesi gereken kullanıcıyı arayın. Kullanıcıyı seçin ve ardından **Seç**' e tıklayın.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Uygulamayı self servis için yapılandırma ve grubu uygulamaya atama
1. [Azure Portal](https://portal.azure.com) Azure AD yöneticisi veya genel yönetici olarak oturum açın.
2. Gezinti bölmesinde **Azure Active Directory**' yi seçin.
3. **Yönet** altında **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
4. Uygulama listesinde, uygulamayı bulun ve açın.
5. **Yönet** altında **Çoklu oturum açma**' yı seçin ve uygulamayı çoklu oturum açma için yapılandırın. (Ayrıntılar için bkz. [Kurumsal uygulamalar için çoklu oturum açmayı yönetme](../manage-apps/add-application-portal-setup-sso.md).)
6. **Yönet** altında **self servis**' i seçin ve self servis uygulama erişimini ayarlayın. (Ayrıntılar için bkz. [self servis uygulama erişimini kullanma](../manage-apps/access-panel-manage-self-service-access.md).) 

    > [!NOTE]
    > **Kullanıcıların hangi gruba atanması gerektiğine** ilişkin ayar için, önceki bölümde oluşturduğunuz grubu seçin.
7. **Yönet**' ın altında **Kullanıcılar ve gruplar**' ı seçin ve oluşturduğunuz self servis grubunun listede göründüğünü doğrulayın.
8. Uygulamayı grup sahibinin erişim paneline eklemek için **Kullanıcı**  >  **kullanıcıları ve grupları** Ekle ' yi seçin. Grup sahibini arayın ve kullanıcıyı seçin, **Seç**' e tıklayın ve ardından kullanıcıyı uygulamaya eklemek için **ata** ' ya tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD B2B işbirliği ile ilgili aşağıdaki makalelere bakın:

- [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
- [Azure Active Directory yöneticileri B2B işbirliği kullanıcılarını nasıl ekler?](add-users-administrator.md)
- [B2B işbirliği daveti satın alma](redemption-experience.md)
- [Dış Kimlikler fiyatlandırması](external-identities-pricing.md)