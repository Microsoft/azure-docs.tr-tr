---
title: Native client uygulamaları - Azure AD yayımlama | Microsoft Docs
description: Yerel istemci uygulamaların, şirket içi uygulamalara güvenli uzaktan erişim sağlamak için Azure AD uygulama ara sunucusu Bağlayıcısı ile iletişim kurmasını sağlamak nasıl etkinleştireceğinizi de açıklar.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cbee6bfcca3ddb356abe9dceab2fca07c152b07
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961803"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Yerel istemci uygulamalarının proxy uygulamalarıyla etkileşime geçmesini sağlama

Web uygulamaları yayımlamak için Azure Active Directory (Azure AD) uygulama proxy 'Si kullanabilirsiniz, ancak Azure AD kimlik doğrulama kitaplığı (ADAL) ile yapılandırılmış yerel istemci uygulamalarını yayımlamak için de kullanılabilir. Yerel istemci uygulamaları bir cihaza yüklendiğinden, Web uygulamalarına bir tarayıcıdan erişildiği için Web Apps 'ten farklıdır.

Yerel istemci uygulamalarını desteklemek için, uygulama proxy 'Si, üst bilgide gönderilen Azure AD tarafından verilen belirteçleri kabul eder. Uygulama proxy hizmeti, kullanıcıların kimlik doğrulamasını yapar. Bu çözüm, kimlik doğrulaması için uygulama belirteçlerini kullanmaz.

![Son kullanıcılar, Azure AD ve yayımlanan uygulamalar arasındaki ilişki](./media/application-proxy-configure-native-client-application/richclientflow.png)

Yerel uygulamaları yayımlamak için, kimlik doğrulamasından geçen ve birçok istemci ortamını destekleyen Azure AD kimlik doğrulama kitaplığı 'nı kullanın. Uygulama proxy'si uygun içine [yerel uygulaması Web API'si senaryosu](../develop/native-app.md).

Bu makalede uygulama ara sunucusu ve Azure AD kimlik doğrulama kitaplığı ile yerel bir uygulamayı yayımlamak için dört adımlarında size kılavuzluk eder.

## <a name="step-1-publish-your-proxy-application"></a>1\. Adım: proxy uygulamanızı yayımlama

Diğer uygulamalarda olduğu gibi ara sunucu uygulamasını yayımlayın ve uygulamanıza erişmek için kullanıcı atama. Daha fazla bilgi için [uygulama ara sunucusu ile uygulama yayımlama](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>2\. Adım: yerel Uygulamanızı kaydetme

Artık uygulamanızı şu şekilde Azure AD 'ye kaydetmeniz gerekir:

1. [Azure Active Directory portalında](https://aad.portal.azure.com/)oturum açın. **Azure Active Directory Yönetim Merkezi** **panosu** görüntülenir.
1. Kenar çubuğunda **Azure Active Directory**' yi seçin. **Azure Active Directory** genel bakış sayfası görüntülenir.
1. Azure AD Genel Bakış kenar çubuğunda **uygulama kayıtları**' yi seçin. Tüm uygulama kayıtlarının listesi görüntülenir.
1. **Yeni kayıt**seçeneğini belirleyin. **Uygulama kaydetme** sayfası görüntülenir.

   ![Azure portal yeni bir uygulama kaydı oluşturun](./media/application-proxy-configure-native-client-application/create.png)

1. **Ad** başlığında, uygulamanız için kullanıcıya dönük bir görünen ad belirtin.
1. **Desteklenen hesap türleri** başlığı altında, aşağıdaki yönergeleri kullanarak bir erişim düzeyi seçin:

   - Yalnızca kuruluşunuz için dahili olan hesapları hedeflemek için **yalnızca bu kuruluş dizininde hesaplar**' ı seçin.
   - Yalnızca iş veya Eğitim müşterilerini hedeflemek için **herhangi bir kuruluş dizininde hesaplar**' ı seçin.
   - En geniş Microsoft kimlikleri kümesini hedeflemek için **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesaplar**' ı seçin.

1. **Yeniden yönlendirme URI 'si** başlığında **ortak istemci (mobil & Masaüstü)** öğesini seçin ve ardından uygulamanızın yeniden yönlendirme URI 'sini yazın.
1. **Microsoft Platformu ilkelerini**seçin ve okuyun ve ardından **Kaydet**' i seçin. Yeni uygulama kaydı için bir genel bakış sayfası oluşturulur ve görüntülenir.

Yeni bir uygulama kaydı oluşturma hakkında daha ayrıntılı bilgi için bkz. [uygulamaları Azure Active Directory tümleştirme](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>3\. Adım: proxy uygulamanıza erişim Izni verme

Yerel uygulamanızı kaydettirdiğiniz için, bu durumda ara sunucu uygulamasına erişmek üzere dizininizdeki diğer uygulamalara erişim izni verebilirsiniz. Yerel uygulamanın proxy uygulamasına gösterilmesini sağlamak için:

1. Yeni uygulama kaydı sayfasının kenar çubuğunda, **API izinleri**' ni seçin. Yeni uygulama kaydı için **API izinleri** sayfası görüntülenir.
1. Seçin **bir izin eklemek**. **İstek API 'si izinleri** sayfası görüntülenir.
1. **BIR API seçin** ayarı altında **Kuruluşumun kullandığı API 'ler**' i seçin. Dizininizde API 'Leri kullanıma sunan uygulamaları içeren bir liste görüntülenir.
1. [1. Adım: ara sunucu uygulamanızı yayımlama](#step-1-publish-your-proxy-application)bölümünde yayımladığınız proxy uygulamasını bulmak için arama kutusuna veya kaydırın, sonra da ara sunucu uygulamasını seçin.
1. **Uygulamanızın ne tür izinler gerekiyor?** başlığına izin türünü seçin. Yerel uygulamanızın oturum açmış kullanıcı olarak proxy uygulama API 'sine erişmesi gerekiyorsa, **temsilci izinleri**' ni seçin.
1. **Izinleri Seç** başlığında, istenen izni seçin ve **izin Ekle**' yi seçin. Yerel uygulamanız için **API izinleri** sayfası artık eklediğiniz proxy uygulamasını ve izin API 'sini gösterir.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>4\. adım: Active Directory kimlik doğrulama kitaplığı Düzenle

Kimlik doğrulaması bağlamı, Active Directory Authentication Library (aşağıdaki metni eklemek için ADAL) yerel uygulama kodunda düzenleyin:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

Örnek kodda gerekli bilgiler, Azure AD portalında şu şekilde bulunabilir:

| Bilgi gerekiyor | Azure AD portalında bulma |
| --- | --- |
| \<kiracı KIMLIĞI > | **Azure Active Directory** > **ÖZELLIKLER** > **dizin kimliği** |
| Proxy uygulamasının dış URL 'Sini \<> | **Kurumsal uygulamalar** > *proxy uygulamanızın* > **uygulama proxy** 'si > **dış URL** 'si |
| Yerel uygulamanın \<uygulama KIMLIĞI > | **Kurumsal uygulamalar** > *Yerel uygulamanızı* > **Özellikler** > **uygulama kimliği** |
| Yerel uygulamanın yeniden yönlendirme URI 'sini \<> | **Azure Active Directory** >  > uygulama kayıtları *Yerel uygulamanız* > **yeniden yönlendirme URI 'leri** |
| Proxy uygulama API 'Si \<> | **Azure Active Directory** > **Uygulama kayıtları** *yerel uygulamanızı* > API **izinleri** > **API/izin adı** >  |

ADAL 'yi bu parametrelerle düzenledikten sonra, kullanıcılarınız şirket ağının dışında olduklarında bile yerel istemci uygulamalarında kimlik doğrulaması yapabilir.

## <a name="next-steps"></a>Sonraki adımlar

Yerel uygulama akışı hakkında daha fazla bilgi için, [Azure Active Directory Içindeki yerel uygulamalar](../develop/native-app.md)bölümüne bakın.

[Azure Active Directory uygulamalarda çoklu oturum açmayı](what-is-single-sign-on.md#choosing-a-single-sign-on-method)ayarlama hakkında bilgi edinin.
