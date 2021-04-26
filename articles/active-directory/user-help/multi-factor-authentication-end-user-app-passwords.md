---
title: Uygulama parolalarını yönetme-Azure Active Directory | Microsoft Docs
description: Uygulama parolaları ve iki adımlı doğrulamaya yönelik olarak kullanıldıkları özellikler hakkında bilgi edinin.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 07303a0b0b3007ade9adb90af7397855a5014cc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98179431"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>İki aşamalı doğrulama için uygulama parolalarını yönetme

> [!Important]
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

- **Kuruluşunuz iki öğeli doğrulama ve ek güvenlik doğrulama sayfası kullanır.** Kuruluşunuzda iki öğeli doğrulama ile iş veya okul hesabınızı (gibi) kullanıyorsanız alain@contoso.com , uygulama parolalarınızı [ek güvenlik doğrulama sayfasından](https://account.activedirectory.windowsazure.com/Proofup.aspx)yönetebilirsiniz. Ayrıntılı yönergeler için, bu makaledeki [ek güvenlik doğrulaması sayfasını kullanarak uygulama parolaları oluşturma ve silme](#create-and-delete-app-passwords-from-the-additional-security-verification-page) bölümüne bakın.

- **Kuruluşunuz iki öğeli doğrulama ve Office 365 portalını kullanır.** İş veya okul hesabınızı (örneğin, alain@contoso.com ), iki faktörlü doğrulamayı ve kuruluşunuzda Microsoft 365 uygulamaları kullanıyorsanız, uygulama parolalarınızı [Office 365 Portal sayfasından](https://www.office.com)yönetebilirsiniz. Ayrıntılı yönergeler için, bu makaledeki [Office 365 portalını kullanarak uygulama parolaları oluşturma ve silme](#create-and-delete-app-passwords-using-the-office-365-portal) bölümüne bakın.

- **Kişisel Microsoft hesabı iki öğeli doğrulama kullanıyorsunuz.** İki öğeli doğrulamayla bir kişisel Microsoft hesabı (gibi alain@outlook.com ) kullanıyorsanız, uygulama parolalarınızı [güvenlik temel bilgileri sayfasından](https://account.microsoft.com/security/)yönetebilirsiniz. Ayrıntılı yönergeler için bkz. [iki aşamalı doğrulamayı desteklemeyen uygulamalarla uygulama parolaları kullanma](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification).

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

   ![Uygulama parolaları sayfasında uygulama parolasının silinmesini gösteren ekran görüntüsü](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Parolayı silmek istediğinizi onaylamak için **Evet** ' i seçin ve ardından **Kapat**' ı seçin.

    Uygulama parolası başarıyla silindi.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Office 365 portalını kullanarak uygulama parolaları oluşturma ve silme

İş veya okul hesabınızla ve Microsoft 365 uygulamalarınızda iki adımlı doğrulama kullanırsanız, Office 365 portalını kullanarak uygulama parolalarınızı oluşturabilir ve silebilirsiniz.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Office 365 portalını kullanarak uygulama parolaları oluşturmak için

1. İş veya okul hesabınızda oturum açın, [Hesabım sayfasına](https://myaccount.microsoft.com)gidin ve **güvenlik bilgileri**' ni seçin.

    ![Güvenlik bilgisi sekmesini gösteren Office portalı](media/multi-factor-authentication-end-user-app-passwords/mfa-security-info.png)

2. **Yöntem Ekle**' yi seçin, açılan listeden **uygulama parolası** ' nı seçin ve ardından **Ekle**' ye tıklayın.

    ![Güvenlik bilgileri sayfası, yöntem ekleme drowplist ile](media/multi-factor-authentication-end-user-app-passwords/mfa-add-method.png)

3. Uygulama parolası için bir ad girin ve ardından **İleri**' yi seçin.

    ![Uygulama parolasının adı ile uygulama parolaları Oluştur sayfası](media/multi-factor-authentication-end-user-app-passwords/mfa-enter-app-password-name.png)

4. **Uygulama parolası** sayfasından parolayı kopyalayın ve ardından **bitti**' yi seçin.

    ![Yeni uygulama parolasıyla oluşturduğunuz uygulama parolası sayfası](media/multi-factor-authentication-end-user-app-passwords/mfa-copy-app-password.png)

5. **Güvenlik bilgileri** sayfasında, uygulama parolanızın listelendiğinden emin olun.

    ![Güvenlik bilgileri sayfası, yeni uygulama parolasıyla listede gösteriliyor](media/multi-factor-authentication-end-user-app-passwords/mfa-verify-app-password.png)  

6. Uygulama parolasını oluşturduğunuz uygulamayı (örneğin, Outlook 2016) açın ve istendiğinde uygulama parolasını yapıştırın. Bunu uygulama başına yalnızca bir kez yapmanız gerekir.

### <a name="to-delete-app-passwords-using-the-security-info-page"></a>Güvenlik bilgileri sayfasını kullanarak uygulama parolalarını silmek için

1. **Güvenlik bilgileri** sayfasında, silmek istediğiniz uygulama parolasının yanındaki **Sil** ' i seçin.

   ![Güvenlik bilgileri sayfasında uygulama parolasının silinmesini gösteren ekran görüntüsü](media/multi-factor-authentication-end-user-app-passwords/mfa-delete-app-password.png)

2. Onay kutusunda **Tamam ' ı** seçin.

    Uygulama parolası başarıyla silindi.

## <a name="if-your-app-passwords-arent-working-properly"></a>Uygulama parolalarınız düzgün çalışmıyorsa

Parolanızı doğru yazdığınızdan emin olun. Parolanızı doğru girdiğinizden emin değilseniz, yeniden oturum açmayı deneyebilir ve yeni bir uygulama parolası oluşturabilirsiniz. Bu seçeneklerden hiçbiri sorununuzu gidermezse, kuruluşunuzun yardım masasına başvurarak mevcut uygulama parolalarınızı silebilmeleri için, yeni markaların oluşturulmasını sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [İki adımlı doğrulama ayarlarınızı yönetme](multi-factor-authentication-end-user-manage-settings.md)

- Metin veya çağrı almak yerine uygulama bildirimleri ile oturum açma işlemlerini doğrulamak için [Microsoft Authenticator uygulamasını](user-help-auth-app-download-install.md) deneyin.
