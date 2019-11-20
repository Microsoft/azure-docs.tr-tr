---
title: Uygulama parolalarını yönetme-Azure Active Directory | Microsoft Docs
description: Uygulama parolaları ve iki adımlı doğrulamaya yönelik olarak kullanıldıkları özellikler hakkında bilgi edinin.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: lizross
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78acb1f27b27c53aaaac508e0aa99a5b9c14ff2d
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914928"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>İki adımlı doğrulama için uygulama parolalarını yönetme

>[!Important]
>Yöneticiniz uygulama parolalarını kullanmanıza izin vermeyebilir. **Uygulama parolalarını** bir seçenek olarak görmüyorsanız, kuruluşunuzda mevcut değildir.

Uygulama parolalarını kullanırken, dikkat edilmesi önemlidir:

- Uygulama parolaları otomatik olarak oluşturulur ve uygulama başına bir kez oluşturulup girilmelidir.

- Kullanıcı başına 40 parola sınırı vardır. Bu sınırdan sonra bir tane oluşturmayı denerseniz, yenisini oluşturmaya izin verilmeden önce mevcut bir parolayı silmeniz istenir.

    >[!Note]
    >Office 2013 istemcileri (Outlook dahil) yeni kimlik doğrulama protokollerini destekler ve iki adımlı doğrulama ile kullanılabilir. Bu destek, iki adımlı doğrulama etkinleştirildikten sonra Office 2013 istemcileri için uygulama parolalarına gerek kalmaz. Daha fazla bilgi için bkz. [office 2013 ve office 2016 istemci uygulamalarında modern kimlik doğrulaması nasıl kullanılır](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) makalesi.

## <a name="create-new-app-passwords"></a>Yeni uygulama parolaları oluşturma

İlk iki öğeli doğrulama kayıt işleminiz sırasında tek bir uygulama parolası sunulur. Birden fazla ihtiyacınız varsa, bunları kendiniz oluşturmanız gerekir. Kuruluşunuzda iki öğeli doğrulamanın nasıl ayarlandığına bağlı olarak, birden çok alandan uygulama parolaları oluşturabilirsiniz. İş veya okul hesabınızla iki öğeli doğrulama kullanmaya kaydolma hakkında daha fazla bilgi için bkz. [iki öğeli doğrulamaya ve iş veya okul hesabınıza](multi-factor-authentication-end-user-first-time.md) ve Ilgili makalelere genel bakış.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Uygulama parolalarınızı oluşturma ve silme

İki öğeli doğrulamayı nasıl kullanacağınızı temel alarak uygulama parolaları oluşturabilir ve silebilirsiniz:

- **Kuruluşunuz iki öğeli doğrulama ve ek güvenlik doğrulama sayfası kullanır.** Kuruluşunuzda iki öğeli doğrulama ile iş veya okul hesabınızı ( alain@contoso.comgibi) kullanıyorsanız, uygulama parolalarınızı [ek güvenlik doğrulama sayfasından](https://account.activedirectory.windowsazure.com/Proofup.aspx)yönetebilirsiniz. Ayrıntılı yönergeler için, bu makaledeki [ek güvenlik doğrulaması sayfasını kullanarak uygulama parolaları oluşturma ve silme](#create-and-delete-app-passwords-from-the-additional-security-verification-page) bölümüne bakın.

- **Kuruluşunuz iki öğeli doğrulama ve Office 365 portalını kullanır.** Kuruluşunuzda iş veya okul hesabınızı (örneğin, alain@contoso.com), iki faktörlü doğrulamayı ve Office 365 uygulamalarını kullanıyorsanız, uygulama parolalarınızı [Office 365 Portal sayfasından](https://www.office.com)yönetebilirsiniz. Ayrıntılı yönergeler için, bu makaledeki [Office 365 portalını kullanarak uygulama parolaları oluşturma ve silme](#create-and-delete-app-passwords-using-the-office-365-portal) bölümüne bakın.

- **Kişisel Microsoft hesabı iki öğeli doğrulama kullanıyorsunuz.** İki öğeli doğrulamayla bir kişisel Microsoft hesabı ( alain@outlook.comgibi) kullanıyorsanız, uygulama parolalarınızı [güvenlik temel bilgileri sayfasından](https://account.microsoft.com/security/)yönetebilirsiniz. Ayrıntılı yönergeler için bkz. [iki aşamalı doğrulamayı desteklemeyen uygulamalarla uygulama parolaları kullanma](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification).

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Ek güvenlik doğrulama sayfasından uygulama parolaları oluşturma ve silme

İş veya okul hesabınız için **ek güvenlik doğrulama** sayfasından uygulama parolaları oluşturabilir ve silebilirsiniz.

1. [Ek güvenlik doğrulama sayfasında](https://account.activedirectory.windowsazure.com/Proofup.aspx)oturum açın ve ardından **Uygulama parolaları**' nı seçin.

    ![Uygulama parolaları sayfası, uygulama parolaları sekmesi vurgulanmış şekilde](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. **Oluştur**' u seçin, uygulama parolasını gerektiren uygulamanın adını yazın ve ardından **İleri**' yi seçin.

    ![Parola gerektiren uygulama adı ile uygulama parolaları Oluştur sayfası](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. **Uygulama parolası** sayfasından parolayı kopyalayın ve ardından **Kapat**' ı seçin.

    ![Belirtilen uygulamanızın parolasıyla birlikte uygulama parolası sayfanız](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. **Uygulama parolaları** sayfasında, uygulamanızın listelendiğinden emin olun.

     ![Listede yeni uygulamayla gösterilen uygulama parolaları sayfası](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Uygulama parolasını oluşturduğunuz uygulamayı (örneğin, Outlook 2010) açın ve istendiğinde uygulama parolasını yapıştırın. Bunu uygulama başına yalnızca bir kez yapmanız gerekir.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Uygulama parolaları sayfasını kullanarak bir uygulama parolasını silmek için

1. **Uygulama parolaları** sayfasında, silmek istediğiniz uygulama parolasının yanındaki **Sil** ' i seçin.

   ![Uygulama parolasını silme](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Parolayı silmek istediğinizi onaylamak için **Evet** ' i seçin ve ardından **Kapat**' ı seçin.

    Uygulama parolası başarıyla silindi.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Office 365 portalını kullanarak uygulama parolaları oluşturma ve silme

İş veya okul hesabınızla ve Office 365 uygulamalarınızda iki aşamalı doğrulama kullanırsanız, Office 365 portalını kullanarak uygulama parolalarınızı oluşturabilir ve silebilirsiniz.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Office 365 portalını kullanarak uygulama parolaları oluşturmak için

1. Office 365 ' te oturum açın ve [Hesabım sayfasına](https://portal.office.com)gidin, **Güvenlik & Gizlilik**' i seçin ve ardından **ek güvenlik doğrulaması**' nı genişletin.

    ![Genişletilmiş ek güvenlik doğrulama alanını gösteren Office portalı](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Uygulama **Parolaları** sayfasını açmak için **Uygulama parolaları oluştur ve Yönet** ' in bulunduğu metni seçin.

    ![Uygulama parolaları sayfası, uygulama parolaları sekmesi vurgulanmış şekilde](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. **Oluştur**' u seçin, uygulama parolasını gerektiren uygulamanın adını yazın ve ardından **İleri**' yi seçin.

    ![Parola gerektiren uygulama adı ile uygulama parolaları Oluştur sayfası](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. **Uygulama parolası** sayfasından parolayı kopyalayın ve ardından **Kapat**' ı seçin.

    ![Belirtilen uygulamanızın parolasıyla birlikte uygulama parolası sayfanız](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. **Uygulama parolaları** sayfasında, uygulamanızın listelendiğinden emin olun.

     ![Listede yeni uygulamayla gösterilen uygulama parolaları sayfası](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Uygulama parolasını oluşturduğunuz uygulamayı (örneğin, Outlook 2010) açın ve istendiğinde uygulama parolasını yapıştırın. Bunu uygulama başına yalnızca bir kez yapmanız gerekir.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>Uygulama parolaları sayfasını kullanarak uygulama parolalarını silme

1. **Uygulama parolaları** sayfasında, silmek istediğiniz uygulama parolasının yanındaki **Sil** ' i seçin.

   ![Uygulama parolasını silme](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Onay kutusunda **Evet** ' i seçin ve ardından **Kapat**' ı seçin.

    Uygulama parolası başarıyla silindi.

## <a name="if-your-app-passwords-arent-working-properly"></a>Uygulama parolalarınız düzgün çalışmıyorsa

Parolanızı doğru yazdığınızdan emin olun. Parolanızı doğru girdiğinizden emin değilseniz, yeniden oturum açmayı deneyebilir ve yeni bir uygulama parolası oluşturabilirsiniz. Bu seçeneklerden hiçbiri sorununuzu gidermezse, kuruluşunuzun yardım masasına başvurarak mevcut uygulama parolalarınızı silebilmeleri için, yeni markaların oluşturulmasını sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [İki adımlı doğrulama ayarlarınızı yönetme](multi-factor-authentication-end-user-manage-settings.md)

- Metin veya çağrı almak yerine uygulama bildirimleri ile oturum açma işlemlerini doğrulamak için [Microsoft Authenticator uygulamasını](user-help-auth-app-download-install.md) deneyin.
