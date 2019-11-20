---
title: Kendi parolanızı sıfırlamak için kaydolun-Azure AD
description: Kimlik doğrulama verilerinizi Azure AD self servis parola sıfırlama için kaydedin, böylece yönetici yardımı olmadan kendi parolanızı sıfırlayabilirsiniz.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 796702b2189dcfb001f72913e4a01c0ae01462ad
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820442"
---
# <a name="register-to-reset-your-own-password"></a>Kendi parolanızı sıfırlamak için kaydolun

> [!IMPORTANT]
> Oturumunuzu açamıyor musunuz? Bu durumda, bkz. [iş veya okul parolanızı sıfırlama](active-directory-passwords-update-your-own-password.md).

Son Kullanıcı olarak, Azure Active Directory (Azure AD) self servis parola sıfırlama (SSPR) kullanıyorsanız, parolanızı sıfırlayabilir veya hesabınızı kendiniz açabilirsiniz. Bu işlevi kullanabilmeniz için önce kimlik doğrulama yöntemlerinizi kaydetmeniz veya yöneticinizin doldurduğu önceden tanımlanmış kimlik doğrulama yöntemlerini onaylamanız gerekir.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>SSPR ile kimlik doğrulama verilerini kaydetme veya onaylama

1. Cihazınızda Web tarayıcısını açın ve [parola sıfırlama kaydı sayfasına](https://aka.ms/ssprsetup)gidin.
2. Kullanıcı adınızı ve yöneticinizin sağlandığı parolayı girin.
3. BT personelinizin her şeyi nasıl yapılandırdığına bağlı olarak, aşağıdaki seçeneklerden biri veya daha fazlası, yapılandırıp doğrulamanız için kullanılabilir. Yöneticinizin bilgilerinizi kullanma izni varsa, bazı bilgileri sizin için doldurabilir.
    * **Ofis telefonu**: yalnızca yöneticiniz bu seçeneği belirleyebilir.
    * **Kimlik doğrulama telefonu**: Bu seçeneği, erişiminiz olan başka bir telefon numarası olarak ayarlayın. Örnek, bir metin veya çağrı alabilen bir cep telefonunun örneğidir.
    * **Kimlik doğrulama e-postası**: Bu seçeneği, sıfırlamak istediğiniz parolayı kullanmadan erişebileceğiniz alternatif bir e-posta adresi olarak ayarlayın.
    * **Güvenlik soruları**: yöneticiniz, yanıtlamanıza yönelik bu soruların listesini onayladı. Aynı soruyu veya yanıtı birden çok kez kullanamazsınız.
4. Yöneticinizin gerektirdiği bilgileri sağlayın ve doğrulayın. Birden fazla seçenek varsa, birden çok yöntem kaydetmenizi öneririz. Bu, yöntemlerden biri kullanılabilir olmadığında size esneklik sağlar. Bir örnek yolculukta olduğunuz ve ofis telefonunuza erişemedik.

    ![Kimlik doğrulama yöntemlerini Kaydet ve son ' u seçin][Register]

5. **Son**' u seçin. Artık, daha sonra ihtiyacınız olduğunda SSPR 'yi kullanabilirsiniz.

**Kimlik doğrulama telefonu** veya **kimlik doğrulama e-postası**için veri girerseniz, genel dizinde görünmez. Bu verileri yalnızca siz ve yöneticileriniz görebilir. Yalnızca güvenlik sorularınıza yanıt verebilirsiniz.

Yöneticileriniz, uygun yöntemlerin kayıtlı olduğundan emin olmak için bir süre sonra kimlik doğrulama yöntemlerinizi onaylamanız gerekebilir.

## <a name="common-problems-and-their-solutions"></a>Yaygın sorunlar ve çözümleri

 Aşağıda bazı yaygın hata durumları ve çözümleri verilmiştir:

| Hata durumu| Hangi hatayı görüyorsunuz?| Çözüm |
| --- | --- | --- |
| Kullanıcı kimliğimi girdikten sonra "lütfen yöneticinize başvurun" sayfasına aldım | Lütfen yöneticinize başvurun. <br> <br> Kullanıcı hesabınızın parolasının Microsoft tarafından yönetilmediğini belirledik. Sonuç olarak, parolanızı otomatik olarak sıfırlayamıyoruz. <br> <br> Daha fazla yardım için BT personelinize başvurun. | BT personeliniz parolanızı şirket içi ortamınızda yönettiğinden ve parolanızı **Hesap bağlantıınızdan** sıfırlamanıza izin vermediğinden bu iletiyi görüyorsunuz. <br> <br> Parolanızı sıfırlamak için doğrudan yardım için BT personelinize başvurun. Bu özelliği sizin yerinize etkinleştirebilmeniz için parolanızı sıfırlamak istediğinizi bilmesini sağlayın.|
| Kullanıcı kimliğimi girdikten sonra "hesabınız parola sıfırlama için etkinleştirilmemiş" hatası alıyorum | Hesabınız parola sıfırlama için etkinleştirilmemiş. <br> <br> Üzgünüz, ancak BT personeliniz hesabınızı bu hizmetle kullanılmak üzere ayarlamadı. <br> <br> İsterseniz, parolanızı sizin için sıfırlayabilmeniz için kuruluşunuzdaki bir yöneticiye başvururuz. | BT personelinizin kuruluşunuz için parola sıfırlamasını **Hesap bağlantısına erişemediğinden** veya özelliği kullanmak için size lisanslamadığı için bu iletiyi görüyorsunuz. <br> <br> Parolanızı sıfırlamak için **yönetici ile iletişim kurun** bağlantısını seçin. Şirketinizin BT personeline bir e-posta gönderilir. E-posta, parolanızı sıfırlamak istediğinizi ve bu özelliği sizin için etkinleştirebilmesini sağlar. |
| Kullanıcı kimliğimi girdikten sonra "Hesap Kimliğiniz doğrulanamadı" hatası alıyorum | Hesabınızı doğrulayamıyoruz. <br> <br> İsterseniz, parolanızı sizin için sıfırlayabilmeniz için kuruluşunuzdaki bir yöneticiye başvururuz. | Parola sıfırlama için etkin olduğunuzdan, ancak hizmeti kullanmak için kaydolmadığınız için bu iletiyi görüyorsunuz. Parola sıfırlama için kaydolmak üzere hesabınıza erişim izni verdikten sonra [parola sıfırlama kaydı sayfasına](https://aka.ms/ssprsetup) gidin. <br> <br> Parolanızı sıfırlamak için şirketinizin BT personeline bir e-posta göndermek üzere **yönetici ile iletişim kurun** bağlantısını seçin. |

## <a name="next-steps"></a>Sonraki adımlar

* [Self servis parola sıfırlama kullanarak parolanızı değiştirme](active-directory-passwords-update-your-own-password.md)
* [Parola sıfırlama kayıt sayfası](https://aka.ms/ssprsetup)
* [Parola sıfırlama portalı](https://passwordreset.microsoftonline.com/)
* [Microsoft hesabı oturum açamazsınız](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Kayıtlı yöntemlerin ve son düğmesinin gösterildiği parola sıfırlama kayıt sayfası"

