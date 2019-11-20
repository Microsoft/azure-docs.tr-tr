---
title: Güvenlik bilgilerinde uygulama parolaları oluşturma (Önizleme)-Azure AD
description: Herhangi bir tarayıcı olmayan uygulamayla veya iki öğeli doğrulamayı desteklemeyen herhangi bir uygulamayla kullanmak için otomatik olarak oluşturulan parolalar (Uygulama parolaları) ayarlayın. Bu uygulama parolası normal bir paroladan ayrıdır ve güvenlik bilgileri sayfasından ayarlanabilir.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb26d90772fc9e3e3e506946363c76cf02e6b2ef
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820335"
---
# <a name="create-app-passwords-from-your-security-info-preview-page"></a>Güvenlik bilgileri (Önizleme) sayfasından uygulama parolaları oluşturma

Outlook 2010 gibi bazı uygulamalar iki aşamalı doğrulamayı desteklemez. Bu destek eksikliği, kuruluşunuzda iki aşamalı doğrulama kullanıyorsanız uygulamanın çalışmamasıdır. Bu sorunu çözmek için, her tarayıcı olmayan uygulamayla birlikte kullanmak üzere otomatik olarak oluşturulan bir parola oluşturarak normal parolanınızdan ayırabilirsiniz.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Yöneticiniz uygulama parolalarını kullanmanıza izin vermeyebilir. **Uygulama parolalarını** bir seçenek olarak görmüyorsanız, kuruluşunuzda mevcut değildir.

Uygulama parolalarını kullanırken, dikkat edilmesi önemlidir:

- Uygulama parolaları otomatik olarak oluşturulur ve uygulama başına bir kez oluşturulup girilmelidir.

- Kullanıcı başına 40 parola sınırı vardır. Bu sınırdan sonra bir tane oluşturmayı denerseniz, yenisini oluşturmaya izin verilmeden önce mevcut bir parolayı silmeniz istenir.

    >[!Note]
    >Office 2013 istemcileri (Outlook dahil) yeni kimlik doğrulama protokollerini destekler ve iki adımlı doğrulama ile kullanılabilir. Bu destek, iki adımlı doğrulama etkinleştirildikten sonra Office 2013 istemcileri için uygulama parolalarına gerek kalmaz. Daha fazla bilgi için bkz. [office 2013 ve office 2016 istemci uygulamalarında modern kimlik doğrulaması nasıl kullanılır](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) makalesi.

## <a name="create-new-app-passwords"></a>Yeni uygulama parolaları oluşturma

İş veya okul hesabınızla iki aşamalı doğrulama kullanırsanız ve yöneticiniz güvenlik bilgileri deneyimini kullanıyorsa, **güvenlik bilgileri** sayfasını kullanarak uygulama parolalarınızı oluşturabilir ve silebilirsiniz.

>[!Note]
>Yöneticiniz güvenlik bilgileri deneyimini etkinleştirmediyse, [iki adımlı doğrulama için uygulama parolalarını yönetme](multi-factor-authentication-end-user-app-passwords.md) bölümünde yer alan yönergeleri ve bilgileri izlemeniz gerekir.

### <a name="to-create-a-new-app-password"></a>Yeni bir uygulama parolası oluşturmak için

1. İş veya okul hesabınızda oturum açın ve ardından https://myprofile.microsoft.com/ sayfanıza gidin.

    ![, Vurgulanan güvenlik bilgisi bağlantılarını gösteren profilim sayfası](media/security-info/securityinfo-myprofile-page.png)

2. Sol gezinti bölmesinden veya **güvenlik bilgileri** bloğundaki bağlantıdan **güvenlik bilgileri** ' ni seçin ve ardından **güvenlik bilgileri** sayfasından **Yöntem Ekle** ' yi seçin.

    ![Vurgulanan yöntem ekleme seçeneğiyle güvenlik bilgileri sayfası](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. **Yöntem Ekle** sayfasında, açılan listeden **uygulama parolası** ' nı seçin ve ardından **Ekle**' yi seçin.

    ![Uygulama parolası seçiliyken Yöntem kutusu Ekle](media/security-info/securityinfo-myprofile-addpassword.png)

4. Uygulama parolasını gerektiren uygulamanın adını yazın ve ardından **İleri**' yi seçin.

    ![Uygulama parolası sayfası, uygulamanın adı](media/security-info/securityinfo-myprofile-password-appname.png)

5. **Parola** kutusundan metni kopyalayın, parolayı uygulamanın parola alanına yapıştırın (Bu örnekte, Outlook 2010) ve **bitti**' yi seçin.

    ![Uygulama parolası sayfası, uygulamanın adı](media/security-info/securityinfo-myprofile-password-copytext.png)

    Parola eklenir ve uygulamanızda başarılı bir şekilde oturum açabilirsiniz.

## <a name="delete-your-app-passwords"></a>Uygulama parolalarınızı silme

Uygulama parolası gerektiren bir uygulamayı artık kullanmanız gerekmiyorsa, ilişkili uygulama parolasını silebilirsiniz. Uygulama parolasının silinmesi, gelecekte kullanılmak üzere kullanılabilir uygulama parolası noktalarından birini serbest bırakır.

>[!Important]
>Bir uygulama parolasını yanlışlıkla silerseniz geri alma yöntemi yoktur. Bu makalenin [Yeni uygulama parolaları oluşturma](#create-new-app-passwords) bölümündeki adımları izleyerek yeni bir uygulama parolası oluşturmanız ve uygulamayı yeniden girmeniz gerekir.

### <a name="to-delete-an-app-password"></a>Uygulama parolasını silmek için

1. **Güvenlik bilgileri** sayfasında, belirli bir uygulama için **uygulama parolası** seçeneğinin yanındaki **Sil** bağlantısını seçin.

    ![Güvenlik bilgilerinizden uygulama parolası yöntemini silmeye yönelik bağlantı](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. **Uygulama parolasını**silmek için onay kutusundan **Evet** ' i seçin. Uygulama parolası silindikten sonra, güvenlik bilgileriniz kaldırılır ve **güvenlik bilgileri** sayfasından kaybolur.

## <a name="for-more-information"></a>Daha fazla bilgi edinmek için

- **Güvenlik bilgileri** sayfası ve nasıl ayarlanacağı hakkında daha fazla bilgi için bkz. [güvenlik bilgilerine genel bakış](user-help-security-info-overview.md)
