---
title: İş veya okul hesabıyla kimlik doğrulaması kullanarak oturum açma-Azure AD
description: Çeşitli iki öğeli doğrulama yöntemlerini kullanarak iş veya okul hesabınızda oturum açmayı öğrenin.
services: active-directory
author: curtand
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/02/2017
ms.author: curtand
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.openlocfilehash: b373288eb5cd47f99bdbb25f961e1330a708d7d1
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739197"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>İki öğeli doğrulama yönteminizi kullanarak iş veya okul hesabınızda oturum açın

> [!NOTE]
> Bu makalenin amacı, tipik bir oturum açma deneyiminde izlenecek bir deneyimdir. Oturum açmaya yönelik yardım veya sorunları gidermek için bkz. [Azure AD Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md)sorun giderme.

## <a name="what-will-your-sign-in-experience-be"></a>Oturum açma deneyiminiz ne olacak?
Oturum açma deneyiminiz, bir telefon araması, kimlik doğrulama uygulaması veya metinler için seçtiğiniz seçeneğe bağlı olarak farklılık gösterir. Yaptığınız işlemleri en iyi şekilde açıklayan seçeneği belirleyin:

| Nasıl oturum açabilirim? |
| --- |
| [Cep telefonu veya ofis telefonuma telefon çağrısıyla](#signing-in-with-a-phone-call) |
| [Cep telefonum ile metin ile](#signing-in-with-a-text-message)
| [Microsoft Authenticator uygulamasındaki bildirimler ile](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Microsoft Authenticator uygulamasındaki doğrulama kodları ile |
| [Şu anda tercih ediyorum yöntemi kullanamadığı için alternatif bir yöntem ile](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Telefon çağrısıyla oturum açma
Aşağıdaki bilgiler, mobil veya Office telefonunuza yapılan bir çağrı ile iki adımlı doğrulama deneyimini açıklar.

1. Kullanıcı adınızı ve parolanızı kullanarak Microsoft 365 gibi bir uygulamada veya hizmette oturum açın.  
2. Microsoft sizi çağırır.  
3. Telefonu yanıtlayın ve # tuşuna basın.  

## <a name="signing-in-with-a-text-message"></a>Kısa mesajla oturum açma
Aşağıdaki bilgiler, cep telefonunuza bir SMS mesajı ile iki adımlı doğrulama deneyimini anlatmaktadır:

1. Kullanıcı adınızı ve parolanızı kullanarak Microsoft 365 gibi bir uygulamada veya hizmette oturum açın.
2. Microsoft size sayı kodu içeren bir kısa mesaj gönderir.
3. Oturum açma sayfasında belirtilen kutuya kodu girin.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator uygulamayla oturum açma
Aşağıdaki bilgiler, iki adımlı doğrulama için Microsoft Authenticator uygulamasını kullanma deneyimini açıklamaktadır. Uygulamayı kullanmanın iki farklı yolu vardır. Cihazınızda anında iletme bildirimleri alabilir veya bir doğrulama kodu almak için uygulamayı açabilirsiniz.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Microsoft Authenticator uygulamasından bir bildirim ile oturum açmak için
1. Kullanıcı adınızı ve parolanızı kullanarak Microsoft 365 gibi bir uygulamada veya hizmette oturum açın.
2. Microsoft, cihazınızda Microsoft Authenticator uygulamasına bir bildirim gönderir.

   ![Microsoft bildirim gönderiyor](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Telefonunuzda bildirimi açın ve **Doğrula** anahtarını seçin. Şirketiniz PIN gerektiriyorsa buraya girin.
4. Artık oturum açmış olmanız gerekir.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator uygulamayla bir doğrulama kodu kullanarak oturum açmak için

Doğrulama kodlarını almak için Microsoft Authenticator uygulamasını kullanıyorsanız, uygulamayı açtığınızda hesap adınızın altına bir sayı görürsünüz. Bu sayı, her 30 saniyede bir değişir; böylece aynı numarayı iki kez kullanamazsınız. Bir doğrulama kodu istendiğinde, uygulamayı açın ve şu anda hangi numaranın görüntülendiğini kullanın.

1. Kullanıcı adınızı ve parolanızı kullanarak Microsoft 365 gibi bir uygulamada veya hizmette oturum açın.
2. Microsoft sizden bir doğrulama kodu ister.

   ![Doğrulama kodunu girin](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Microsoft Authenticator uygulamasını telefonunuzda açın ve oturum açtığınız kutuya kodu girin.

## <a name="signing-in-with-an-alternate-method"></a>Alternatif bir yöntemle oturum açma
Bazen tercih ettiğiniz doğrulama yöntemi olarak ayarladığınız telefonun veya cihazınızın olması gerekmez. Bu durum, hesabınız için yedekleme yöntemleri ayarlamanızı öneririz. Aşağıdaki bölümde, birincil yönteminizin kullanılamadığı durumlarda alternatif bir yöntemle nasıl oturum alabileceğiniz gösterilmektedir.

1. Kullanıcı adınızı ve parolanızı kullanarak Microsoft 365 gibi bir uygulamada veya hizmette oturum açın.
2. **Farklı bir doğrulama seçeneği kullan**' ı seçin. Ayarlamış olduğunuz sayısına göre farklı doğrulama seçenekleri görürsünüz.
3. Alternatif bir yöntem seçin ve oturum açın.

   ![Alternatif yöntemi kullan](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Sonraki adımlar
- İki aşamalı doğrulama ile oturum açma sorunları yaşıyorsanız, [Azure AD Multi-Factor Authentication ile ilgili sorun yaşamakta](multi-factor-authentication-end-user-troubleshoot.md)daha fazla bilgi alın.

- [İki adımlı doğrulama ayarlarınızı yönetme](multi-factor-authentication-end-user-manage-settings.md)hakkında bilgi edinin.

- Metin ve telefon görüşmeleri yerine oturum açmak üzere bildirimleri kullanabilmeniz için [Microsoft Authenticator uygulamasını kullanmaya nasıl başlabileceğinizi](user-help-auth-app-download-install.md) öğrenin.
