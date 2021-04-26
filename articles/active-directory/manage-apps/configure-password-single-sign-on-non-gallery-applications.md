---
title: Azure Active Directory uygulamalar için parola tabanlı çoklu oturum açmayı (SSO) anlayın
description: Azure Active Directory uygulamalar için parola tabanlı çoklu oturum açmayı (SSO) anlayın
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: iangithinji
ms.openlocfilehash: ffa517f068dbc13f2734630216466373d9014ae6
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374565"
---
# <a name="understand-password-based-single-sign-on"></a>Parola tabanlı çoklu oturum açmayı anlama

Uygulama yönetiminde [hızlı başlangıç serisinde](view-applications-portal.md) , Azure AD 'yi bir uygulama Için kimlik sağlayıcısı (IDP) olarak kullanmayı öğrendiniz. Hızlı başlangıç kılavuzunda SAML tabanlı veya OıDC tabanlı SSO 'yu yapılandırırsınız. Başka bir seçenek de parola tabanlı SSO ' dır. Bu makale, parola tabanlı SSO seçeneği hakkında daha fazla ayrıntıya gider. 

Bu seçenek, HTML oturum açma sayfasına sahip tüm Web siteleri için kullanılabilir. Parola tabanlı SSO, parola oluşturma olarak da bilinir. Parola tabanlı SSO, Kimlik Federasyonu desteklemeyen Web uygulamalarına Kullanıcı erişimini ve parolalarını yönetmenizi sağlar. Ayrıca, çeşitli kullanıcıların, kuruluşunuzun sosyal medya uygulaması hesapları gibi tek bir hesabı paylaşması gerektiği durumlar da yararlıdır.

Parola tabanlı SSO, uygulamaları Azure AD 'ye hızlı bir şekilde tümleştirmenize başlamak için harika bir yoldur ve şunları yapmanızı sağlar:

- Kullanıcı adları ve parolaları güvenli bir şekilde depolayıp kaydederek kullanıcılarınız için çoklu oturum açmayı etkinleştirin

- Yalnızca Kullanıcı adı ve parola alanlarının oturum açmasını gerektiren uygulamalar için çoklu oturum açma alanları gerektiren uygulamalar desteklenir

- Kullanıcılarınızın, kimlik bilgilerini girerken [uygulamalarım](../user-help/my-apps-portal-end-user-access.md) üzerinde göreceği Kullanıcı adı ve parola alanlarının etiketlerini özelleştirin

- Kullanıcılarınızın, el ile yazdıkları mevcut uygulama hesapları için kendi kullanıcı adlarını ve parolalarını sağlamasına izin verin.

- İş grubunun bir üyesinin, [self servis uygulama erişimi](./manage-self-service-access.md) özelliğini kullanarak bir kullanıcıya atanan kullanıcı adlarını ve parolaları belirtmesini sağlar

-   Bir yöneticinin kimlik bilgilerini güncelleştir özelliği ile uygulamada oturum açtıklarında bireyler veya gruplar tarafından kullanılacak kullanıcı adını ve parolayı belirtmesini sağlar 

## <a name="before-you-begin"></a>Başlamadan önce

Azure AD 'nin kimlik sağlayıcısı (IDP) olarak kullanılması ve çoklu oturum açma (SSO) yapılandırması, kullanılan uygulamaya bağlı olarak basit veya karmaşık olabilir. Bazı uygulamalar yalnızca birkaç eylem ile yapılandırılabilir. Başkalarının derinlemesine yapılandırılması gerekir. Bilgi almak için uygulama yönetiminde [hızlı başlangıç serisini](view-applications-portal.md) gözden geçirebilirsiniz. Eklemekte olduğunuz uygulama basittir, büyük olasılıkla bu makaleyi okumanız gerekmez. Eklemekte olduğunuz uygulama özel yapılandırma gerektiriyorsa ve parola tabanlı SSO kullanmanız gerekiyorsa, bu makale sizin için de kullanılır.

> [!IMPORTANT] 
> **Çoklu oturum açma** seçeneğinin **Kurumsal uygulamalarda** bir uygulama için gezinmede olmadığı bazı senaryolar vardır. 
>
> Uygulama **uygulama kayıtları** kullanılarak kaydedilmişse, çoklu oturum açma özelliği varsayılan olarak OIDC OAuth kullanacak şekilde yapılandırılır. Bu durumda, **Çoklu oturum açma** seçeneği, **Kurumsal uygulamalar** altındaki gezinmede gösterilmez. Özel uygulamanızı eklemek için **uygulama kayıtları** kullandığınızda, bildirim dosyasındaki seçenekleri yapılandırırsınız. Bildirim dosyası hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory uygulama bildirimi](../develop/reference-app-manifest.md). SSO standartları hakkında daha fazla bilgi edinmek için bkz. [Microsoft Identity platform kullanarak kimlik doğrulama ve yetkilendirme](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform). 
>
> Bir uygulamanın başka bir kiracıda barındırıldığı veya hesabınızın gerekli izinleri (genel yönetici, bulut uygulaması Yöneticisi, uygulama Yöneticisi veya hizmet sorumlusu sahibi) yoksa, **Çoklu oturum açma** 'nın gezinmede eksik olduğu diğer senaryolar. İzinler Ayrıca **Çoklu oturum** açmayı açabiliyor ancak kaydedemeyeceksiniz bir senaryoya neden olabilir. Azure AD Yönetim rolleri hakkında daha fazla bilgi için bkz https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) . (.


## <a name="basic-configuration"></a>Temel yapılandırma

[Hızlı başlangıç serisinde](view-applications-portal.md), kiracınıza bir uygulama eklemeyi öğrendiniz. Bu, Azure AD 'nin uygulama Için kimlik sağlayıcısı (IDP) olarak kullanıldığını bilmesini sağlar. Bazı uygulamalar önceden yapılandırılmış ve Azure AD galerisinde gösteriliyor. Diğer uygulamalar galeride değildir ve genel bir uygulama oluşturup el ile yapılandırmanız gerekir. Uygulamaya bağlı olarak, parola tabanlı SSO seçeneği kullanılamayabilir. Uygulamanın çoklu oturum açma sayfasında parola tabanlı seçenek listesini görmüyorsanız, kullanılabilir değildir.

> [!IMPORTANT]
> Parola tabanlı SSO için My Apps Browser uzantısı gereklidir. Daha fazla bilgi için bkz. [uygulamalarımı dağıtım planı](my-apps-deployment-plan.md).

Parola tabanlı SSO yapılandırma sayfası basittir. Yalnızca uygulamanın kullandığı oturum açma sayfasının URL 'sini içerir. Bu dize, Kullanıcı adı giriş alanını içeren sayfa olmalıdır.

URL 'YI girdikten sonra **Kaydet**' i seçin. Azure AD, Kullanıcı adı ve parola giriş alanları için oturum açma sayfasının HTML 'sini ayrıştırır. Deneme başarılı olursa işiniz bitti demektir.
 
Sonraki adımınız, [uygulamaya Kullanıcı veya grup atamak](./assign-user-or-group-access-portal.md)olur. Kullanıcıları ve grupları atadıktan sonra, uygulamada oturum açtıklarında Kullanıcı için kullanılacak kimlik bilgilerini sağlayabilirsiniz. **Kullanıcılar ve gruplar**' ı seçin, kullanıcının veya grubun satırı için onay kutusunu işaretleyin ve ardından **kimlik bilgilerini güncelleştir**' i seçin. Son olarak, Kullanıcı veya grup için kullanılacak kullanıcı adını ve parolayı girin. Bunu yapmazsanız, kullanıcılardan başlatma sırasında kimlik bilgilerini girmesi istenir.
 

## <a name="manual-configuration"></a>El ile yapılandırma

Azure AD 'nin Ayrıştırma girişimi başarısız olursa, oturum açmayı el ile yapılandırabilirsiniz.

1. **\<application name> Yapılandırma** altında, **oturum açmayı Yapılandır** sayfasını göstermek Için **\<application name> parola çoklu oturum açma ayarlarını yapılandır** ' ı seçin. 

2. **Oturum açma alanlarını el ile Algıla**' yı seçin. Oturum açma alanlarının el ile algılanmasını açıklayan ek yönergeler görüntülenir.

   ![Parola tabanlı çoklu oturum açma için el ile yapılandırma](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. **Kayıt açma alanlarını yakala '** yı seçin. **Şu anda devam eden ileti meta veri yakalama olduğunu** gösteren yeni bir sekmede yakalama durumu sayfası açılır.

4. Uygulamalarım **uzantısı gerekli** kutusu yeni bir sekmede görünürse **uygulamalarım güvenli oturum açma uzantısı** tarayıcı uzantısını yüklemek için **Şimdi yüklensin** ' i seçin. (Tarayıcı uzantısı Microsoft Edge, Chrome veya Firefox gerektirir.) Sonra uzantıyı yükleyip etkinleştirin, sonra da yakalama durumu sayfasını yenileyin.

   Tarayıcı uzantısı daha sonra, girilen URL 'YI görüntüleyen bir sekme açar.
5. Girilen URL 'nin bulunduğu sekmede, oturum açma işlemine gidin. Kullanıcı adı ve parola alanlarını doldurup oturum açmayı deneyin. (Doğru parolayı sağlamanız gerekmez.)

   Bir istem, yakalanan oturum açma alanlarını kaydetmenizi ister.
6. **Tamam**’ı seçin. Tarayıcı uzantısı, **uygulama için Ileti meta verileri güncelleştirildiğinde** yakalama durumu sayfasını güncelleştirir. Tarayıcı sekmesi kapanır.

7. Azure AD **oturum açma yapılandırma** sayfasında **Tamam ' ı seçin, uygulamada başarıyla oturum açabildim**.

8. **Tamam**’ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulamaya Kullanıcı veya Grup atama](./assign-user-or-group-access-portal.md)
- [Otomatik Kullanıcı hesabı sağlamayı yapılandırma](../app-provisioning/configure-automatic-user-provisioning-portal.md)
