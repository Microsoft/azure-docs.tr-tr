---
title: Azure AD Uygulama Ara Sunucusu uygulamalarda çoklu oturum açma | Microsoft Docs
description: Azure portal Azure AD Uygulama Ara Sunucusu ile yayınlanmış şirket içi uygulamalarınız için çoklu oturum açmayı açın.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/12/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0cb2830c019635e9020a4b586bdc370450fddb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254011"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Uygulama proxy 'Si ile çoklu oturum açma için parola oluşturma

Azure Active Directory Uygulama Ara Sunucusu, uzak çalışanların da güvenli bir şekilde erişebilmesi için şirket içi uygulamaları yayımlayarak üretkenliği artırmanıza yardımcı olur. Azure portal, bu uygulamalar için çoklu oturum açma (SSO) da ayarlayabilirsiniz. Kullanıcılarınızın yalnızca Azure AD ile kimlik doğrulaması yapması gerekir ve bu uygulamalar, yeniden oturum açmak zorunda kalmadan kurumsal uygulamanıza erişebilirler.

Uygulama proxy 'Si birden çok [Çoklu oturum açma modunu](sso-options.md#choosing-a-single-sign-on-method)destekler. Parola tabanlı oturum açma, kimlik doğrulaması için bir Kullanıcı adı/parola birleşimi kullanan uygulamalara yöneliktir. Uygulamanız için parola tabanlı oturum açmayı yapılandırdığınızda kullanıcılarınızın şirket içi uygulamada bir kez oturum açması gerekir. Bundan sonra, Azure Active Directory oturum açma bilgilerini depolar ve kullanıcılarınız bu uygulamaya uzaktan erişirken uygulamayı otomatik olarak uygulamaya sağlar.

Uygulamanızı zaten uygulama proxy 'Si ile yayımlamış ve test etmeniz gerekir. Aksi takdirde, [Azure kullanarak uygulama yayımlama ad uygulama ara sunucusu](application-proxy-add-on-premises-application.md) adımları izleyin ve sonra buraya geri dönün.

## <a name="set-up-password-vaulting-for-your-application"></a>Uygulamanız için parola ayarlamayı ayarlama

1. [Azure Portal](https://portal.azure.com)’da yönetici olarak oturum açın.
1. **Azure Active Directory**  >  **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Listeden, SSO ile ayarlamak istediğiniz uygulamayı seçin.  
1. **Uygulama proxy 'si** seçin. 
1. **Ön kimlik doğrulaması türünü** **PASSTHROUGH** olarak değiştirin ve **Kaydet**' i seçin. Daha sonra yeniden **Azure Active Directory** türüne dönebilirsiniz! 
1. **Çoklu oturum açma** seçeneğini belirleyin.

   ![Uygulamanın genel bakış sayfasından çoklu oturum açma seçeneğini belirleyin](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. SSO modu için **parola tabanlı oturum açma**' yı seçin.
1. Oturum açma URL 'SI için, kullanıcıların şirket ağı dışında uygulamanızda oturum açmak için Kullanıcı adını ve parolasını girmesi gereken sayfanın URL 'sini girin. Bu, uygulamayı uygulama proxy 'Si aracılığıyla yayımladığınızda oluşturduğunuz dış URL olabilir.

   ![Parola tabanlı oturum açma seçin ve URL 'nizi girin](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. **Kaydet**’i seçin.
1. **Uygulama proxy 'si** seçin. 
1. **Ön kimlik doğrulama türünü** **Azure Active Directory** olarak değiştirin ve **Kaydet**' i seçin. 
1. **Kullanıcılar ve gruplar ' ı** seçin.
1. Kullanıcıları **Kullanıcı Ekle** seçeneğini belirleyerek uygulamaya atayın. 
1. Bir kullanıcı için kimlik bilgilerini önceden tanımlamak istiyorsanız, Kullanıcı adının önünü işaretleyin ve **kimlik bilgilerini güncelleştir**' i seçin.
1.   >    >  **Tüm uygulamalar** uygulama kayıtları Azure Active Directory seçin.
1. Listeden parola SSO ile yapılandırdığınız uygulamayı seçin.
1. **Marka** seçin. 
1. **Giriş sayfası URL 'Sini** parola SSO sayfasında **oturum açma URL 'si** ile güncelleştirin ve **Kaydet**' i seçin.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Uygulamanızı test etme

Uygulamalarım portalına gidin. Kimlik bilgilerinizle (veya erişim ile ayarladığınız bir test hesabının kimlik bilgileriyle) oturum açın. Başarıyla oturum açtıktan sonra uygulamanın simgesine tıklayın. Bu, uygulamalarımın güvenli oturum açma tarayıcı uzantısının yüklenmesini tetikleyebilir. Kullanıcılarınızın önceden tanımlanmış kimlik bilgileri varsa, uygulamanın kimlik doğrulaması otomatik olarak gerçekleşmelidir, aksi takdirde Kullanıcı adını veya parolayı ilk kez belirtmeniz gerekir. 

## <a name="next-steps"></a>Sonraki adımlar

- [Çoklu oturum açmayı](what-is-single-sign-on.md) uygulamak için diğer yollar hakkında bilgi edinin
- [Azure AD uygulama ara sunucusu ile uygulamalara uzaktan erişmenin güvenlik konuları](application-proxy-security.md) hakkında bilgi edinin
