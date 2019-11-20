---
title: Microsoft Identity platformu ile uygulama kaydetme-Microsoft Identity platform
description: Microsoft kimlik platformu ile uygulama eklemeyi ve kaydetmeyi öğrenin.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 329e4b262731ae1b23fb94da7b937c9c1549185f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473663"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Hızlı başlangıç: Microsoft Identity platformu ile uygulama kaydetme

Kurumsal geliştiriciler ve hizmet olarak yazılım (SaaS) sağlayıcıları, hizmetleri için güvenli oturum açma ve yetkilendirme imkanı sunmak için Microsoft kimlik platformu ile tümleştirilebilen ticari bulut hizmetleri veya iş kolu uygulamaları geliştirebilir.

Bu hızlı başlangıçta, uygulamanızın Microsoft Identity platformu ile tümleştirilebilmesi için Azure portal **uygulama kayıtları** deneyimini kullanarak bir uygulama ekleme ve kaydetme işlemi gösterilmektedir. Yeni uygulama kayıtları deneyiminde yeni özellikler ve geliştirmeler hakkında daha fazla bilgi edinmek için [Bu blog gönderisine](https://developer.microsoft.com/graph/blogs/new-app-registration/)bakın.

## <a name="register-a-new-application-using-the-azure-portal"></a>Yeni bir uygulamayı Azure portalını kullanarak kaydetme

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalında](https://portal.azure.com) oturum açın.
1. Hesabınız size birden fazla Azure AD kiracısına erişim veriyorsa sağ üst köşeden hesabınızı seçin ve portal oturumunuzu istediğiniz Azure AD kiracısına ayarlayın.
1. Arama yapın ve **Azure Active Directory**seçin. **Active Directory** sayfasında, **uygulama kayıtları** ' i seçin ve sonra **Yeni kayıt**' yi seçin.
1. **Uygulama kaydet** sayfası göründüğünde uygulamanızın kayıt bilgilerini girin:

   - **Ad** - Uygulama kullanıcılarına gösterilecek anlamlı bir uygulama adı girin.
   - **Desteklenen hesap türleri** - Uygulamanızın desteklemesini istediğiniz hesapları seçin.

       | Desteklenen hesap türleri | Açıklama |
       |-------------------------|-------------|
       | **Yalnızca bu kuruluş dizinindeki hesaplar** | Bir iş kolu uygulaması (LOB) oluşturuyorsanız bu seçeneği belirtin. Uygulamayı bir dizine kaydetmiyorsanız bu seçenek kullanılamaz.<br><br>Bu seçenek, yalnızca Azure AD tek kiracılı hesaba eşlenir.<br><br>Uygulamayı bir dizinin dışına kaydetmediğiniz sürece, bu varsayılan seçenektir. Uygulamanın bir dizin dışında kaydedildiği durumlarda, varsayılan seçenek Azure çok kiracılı ve kişisel Microsoft kişisel hesaplarıdır. |
       | **Herhangi bir kuruluş dizinindeki hesaplar** | Tüm iş ve eğitim müşterilerini hedeflemek istiyorsanız bu seçeneği belirleyin.<br><br>Bu seçenek, bir yalnızca Azure AD çok kiracılı hesaba eşlenir.<br><br>Uygulamayı yalnızca Azure AD tek kiracılı olarak kaydettiyseniz, **Kimlik doğrulaması** dikey penceresinden Azure AD çok kiracılı olacak şekilde ve tekrar tek kiracılı olarak güncelleştirebilirsiniz. |
       | **Herhangi bir kuruluş dizinindeki hesaplar ve kişisel Microsoft hesapları** | En geniş müşteri kümesini hedeflemek için bu seçeneği belirleyin.<br><br>Bu seçenek Azure AD çok kiracılı ve kişisel Microsoft hesaplarına eşlenir.<br><br>Uygulamayı Azure AD çok kiracılı ve kişisel Microsoft hesapları olarak kaydettiyseniz, bunu kullanıcı arabiriminde değiştiremezsiniz. Bunun yerine, desteklenen hesap türlerini değiştirmek için uygulama bildirimi düzenleyicisini kullanmanız gerekir. |

   - **Yeniden yönlendirme URI’si (isteğe bağlı)** - **Web** veya **Genel istemci (mobil ve masaüstü)** seçenekleri arasından oluşturduğunuz uygulamanın türünü seçin ve ardından uygulamanız için yeniden yönlendirme URI’sini (veya yanıt URL’sini) girin.
       - Web uygulamaları için, uygulamanızın temel URL'sini girin. Örneğin `https://localhost:31544` yerel makinenizde çalışan bir web uygulamasının URL'si olabilir. Kullanıcılar, bir web istemci uygulamasında oturum açmak için bu URL'yi kullanır.
       - Genel istemci uygulamaları için, Azure AD'nin belirteç yanıtlarını döndürmek üzere kullandığı URI'yi girin. Uygulamanıza özgü bir değer girin, örn. `myapp://auth`.

     Web uygulamalarına veya yerel uygulamalara özgü örnekler görmek için, [hızlı başlangıçlara](https://docs.microsoft.com/azure/active-directory/develop) göz atın.

1. Bittiğinde **Kaydet**’i seçin.

    [![, Azure portal yeni bir uygulamanın kaydedileceği ekranı gösterir](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

Azure AD, uygulamanıza benzersiz bir uygulama (istemci) kimliği atar ve uygulamanızın **Genel Bakış** sayfasına yönlendirilirsiniz. Uygulamanıza ek özellikler eklemek için, markalama, sertifikalar ve gizli diziler, API izinleri ve daha fazlasını içeren diğer yapılandırma seçeneklerini belirleyebilirsiniz.

[yeni kaydedilen uygulamanın genel bakış sayfası ![örneği](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

- [İzinler ve onay](v2-permissions-and-consent.md) hakkında bilgi edinin.
- Uygulama kaydınızda kimlik bilgileri ve izinler gibi ek yapılandırma özelliklerini etkinleştirme ve başka kiracılardan kullanıcıların oturum açmasını etkinleştirme için bu hızlı başlangıçlara bakın:
    - [Bir istemci uygulamasını web API'lerine erişecek şekilde yapılandırma](quickstart-configure-app-access-web-apis.md)
    - [Bir uygulamayı web API'lerini kullanıma sunacak şekilde yapılandırma](quickstart-configure-app-expose-web-apis.md)
    - [Bir uygulama tarafından desteklenen hesapları değiştirme](quickstart-modify-supported-accounts.md)
- Hızlıca uygulama derlemek ve belirteç alma, belirteçleri yenileme, kullanıcı oturumu açma ve kullanıcı bilgilerini görüntüleme gibi işlevler eklemek için bir [hızlı başlangıç](https://docs.microsoft.com/azure/active-directory/develop) seçin.
- Kayıtlı uygulamayı temsil eden iki Azure AD nesnesi ve bunlar aralarındaki ilişki hakkında daha fazla bilgi edinmek için bkz. [Uygulama nesneleri ve hizmet sorumlusu nesneleri](app-objects-and-service-principals.md).
- Uygulama geliştirirken kullanmanız gereken markalama yönergeleri hakkında daha fazla bilgi edinmek için bkz. [Uygulamalar için markalama yönergeleri](howto-add-branding-in-azure-ad-apps.md).
