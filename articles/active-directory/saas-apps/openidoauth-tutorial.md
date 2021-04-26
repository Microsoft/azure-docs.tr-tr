---
title: Azure AD uygulama galerisinden bir OpenID/OAuth uygulaması yapılandırma | Microsoft Docs
description: Azure AD uygulama galerisinden bir OpenID/OAuth uygulaması yapılandırma adımları.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.custom: has-adal-ref
ms.openlocfilehash: 32f79f24df6fe705146b39750c710450ef8f1f7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735950"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Azure AD uygulama galerisinden bir OpenID/OAuth uygulaması yapılandırma

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Galeriden bir OpenID uygulaması ekleme işlemi

1. [Azure portalda](https://portal.azure.com)**Azure Active Directory**'yi seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' a gidin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. İletişim kutusunun üst kısmındaki **Yeni uygulama** ' yı seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna uygulama adını yazın. Sonuç panelinden istediğiniz uygulamayı seçin ve uygulamaya kaydolun.

    ![Sonuç listesinde OpenID](common/search-new-app.png)


1. Uygulama adı sayfasında, **Kaydol** düğmesine tıklayın.

    ![Ekle düğmesi](./media/openidoauth-tutorial/addbutton.png)

    > [!NOTE]
    > Burada, kiracı yöneticisi kaydolma düğmesini seçip uygulamaya onay vermenizi sağlamalıdır. Uygulama daha sonra, konfigürasyonları yapabileceğiniz, müşteri kiracısına eklenir. Uygulamayı açıkça eklemeniz gerekmez.

5. Uygulama oturum açma sayfası veya Azure Active Directory (Azure AD) sayfasına yönlendiriliyorsunuz, oturum açma kimlik bilgileri için.

6. Başarılı kimlik doğrulamasından sonra onay sayfasından onay kabul etmiş olursunuz. Bundan sonra uygulama giriş sayfası görüntülenir.

    > [!NOTE]
    > Uygulamanın yalnızca bir örneğini ekleyebilirsiniz. Zaten bir tane eklediyseniz ve yeniden onay sağlamaya çalıştıysanız, kiracıya yeniden eklenmeyecektir. Mantıksal olarak, kiracıda yalnızca bir uygulama örneği kullanabilirsiniz.

1. Galeriden bir OpenID uygulaması eklemek için aşağıdaki videoyu izleyin.
    >[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="authentication-flow-using-openid-connect"></a>OpenID Connect kullanarak kimlik doğrulama akışı

En temel oturum açma akışı aşağıdaki adımları içerir:

![OpenID Connect kullanarak kimlik doğrulama akışı](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Çok kiracılı uygulama
Çok kiracılı bir uygulama, yalnızca bir kuruluş değil birçok kuruluşta kullanılmak üzere tasarlanmıştır. Bunlar, genellikle bağımsız bir yazılım satıcısı (ISV) tarafından yazılan hizmet olarak yazılım (SaaS) uygulamalardır.

Çok kiracılı uygulamaların kullanılabilecekleri her dizinde sağlanması gerekir. Bunları kaydetmek için Kullanıcı veya yönetici onayı gerekir. Bu onay işlemi, bir uygulama dizine kaydedildiğinde başlar ve Graph API veya belki başka bir Web API 'sine erişim izni verilir. Farklı bir kuruluştan bir kullanıcı veya yönetici uygulamayı kullanmak üzere kaydolduğunda, uygulamanın ihtiyacı olan izinleri görüntüleyen bir iletişim kutusu görüntülenir.

Kullanıcı veya yönetici daha sonra uygulamayı kabul edebilir. Onay, uygulamanın belirtilen verilere erişmesini sağlar ve son olarak uygulamayı dizine kaydeder.

> [!NOTE]
> Uygulamanızı birden çok dizindeki kullanıcılar için kullanılabilir hale getiriyorsanız, hangi kiracının bulunduğunu belirlemeniz için bir mekanizmaya ihtiyacınız vardır. Tek kiracılı bir uygulamanın, bir kullanıcı için kendi dizinine bakması gerekir. Çok kiracılı bir uygulamanın, Azure AD 'deki tüm dizinlerden belirli bir kullanıcıyı tanımlaması gerekir.
>
> Bu görevi gerçekleştirmek için Azure AD, herhangi bir çok kiracılı uygulamanın kiracıya özgü bir uç nokta yerine, oturum açma isteklerini doğrudan yönlendirbildiği ortak bir kimlik doğrulama uç noktası sağlar. Bu uç nokta, `https://login.microsoftonline.com/common` Azure AD 'deki tüm dizinlere yöneliktir. Kiracıya özgü bir uç nokta olabilir `https://login.microsoftonline.com/contoso.onmicrosoft.com` .
>
> Ortak uç nokta, uygulamanızı geliştirirken göz önünde bulundurmanız gereken önemli bir noktadır. Oturum açma, oturum kapatma ve belirteç doğrulama sırasında birden fazla kiracıyı işlemek için gerekli mantığa ihtiyacınız olacaktır.

Azure AD, varsayılan olarak çok kiracılı uygulamalar yükseltir. Bunlar kuruluşlar genelinde kolayca erişilir ve onayı kabul ettikten sonra kolayca kullanılabilir.

## <a name="consent-framework"></a>Onay çerçevesi

Azure AD onay çerçevesini, çok kiracılı web ve yerel istemci uygulamaları geliştirmek için kullanabilirsiniz. Bu uygulamalar, bir Azure AD kiracısından Kullanıcı hesaplarına, uygulamanın kaydedildiği bilgisayardan farklı olarak oturum açma izni verir. Ayrıca, şunlar gibi Web API 'Lerine erişim de gerekebilir:
- Microsoft 365 Azure AD, Intune ve hizmetlere erişmek için Microsoft Graph API 'SI.
- Diğer Microsoft Hizmetleri API 'Leri.
- Kendi Web API 'leriniz.

Çerçeve, bir kullanıcıyı veya bir yöneticiyi temel alarak, dizinde kaydedilmesini isteyen bir uygulamaya onay verir. Kayıt, dizin verilerine erişmeyi gerektirebilir. Onay verildikten sonra istemci uygulaması, Microsoft Graph API 'sini Kullanıcı adına çağırabilir ve gerekli bilgileri kullanabilir.

[Microsoft Graph API 'si](https://developer.microsoft.com/graph/) , şunun gibi Microsoft 365 verilere erişim sağlar:

- Exchange 'den takvimler ve mesajlar.
- SharePoint 'ten siteler ve listeler.
- OneDrive 'daki belgeler.
- OneNote 'taki Not defterleri.
- Planlayıcılardan görevler.
- Excel 'deki çalışma kitapları.

Graph API Ayrıca, Azure AD 'den ve diğer Microsoft bulut hizmetlerinden diğer veri nesnelerinden kullanıcılara ve gruplara erişim sağlar.

Aşağıdaki adımlarda, uygulama geliştiricisi ve Kullanıcı için onay deneyiminin nasıl çalıştığı gösterilmektedir:

1. Bir kaynağa veya API 'ye erişmek için belirli izinler istemesi gereken bir Web istemci uygulamanız olduğunu varsayalım. Azure portal, yapılandırma sırasında izin isteklerini bildirmek için kullanılır. Diğer yapılandırma ayarları gibi, uygulamanın Azure AD kayıtlarının bir parçası haline gelir. Gereken Izin isteği yolu için aşağıdaki adımları izleyin:

    a. Menünün sol tarafındaki **uygulama kayıtları** tıklayın ve uygulama adını arama kutusuna yazarak uygulamanızı açın.

    ![Sol taraftaki menüden "Uygulama kayıtları" seçilen ve "uygulama g D" arama kutusunun vurgulandığı ekran görüntüsü.](./media/openidoauth-tutorial/application.png)

    b. **API Izinlerini görüntüle**' ye tıklayın.

    !["P i Izinleri görüntüle" düğmesinin seçili olduğu "bir P I çağrısı" sayfasını gösteren ekran görüntüsü.](./media/openidoauth-tutorial/api-permission.png)

    c. **Izin Ekle**' ye tıklayın.

    !["İzin Ekle" düğmesinin seçili olduğu "bir P I izinleri" bölümünü gösteren ekran görüntüsü.](./media/openidoauth-tutorial/add-permission.png)

    d. **Microsoft Graph**' ye tıklayın.

    !["Microsoft A P I" sekmesi ve "Microsoft Graph" kutucuğu seçiliyken "P I izinleri ıste" sayfasını gösteren ekran görüntüsü.](./media/openidoauth-tutorial/microsoft-graph.png)

    e. **Temsilci izinleri** ve **uygulama izinlerinden** gerekli seçenekleri belirleyin.

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Uygulamanızın izinlerinin güncelleştirildiğini göz önünde bulundurun. Uygulama çalışıyor ve bir Kullanıcı ilk kez kullanmak üzere. İlk olarak, uygulamanın Azure AD/Yetkilendir uç noktasından bir yetkilendirme kodu alması gerekir. Yetkilendirme kodu daha sonra yeni bir erişim ve yenileme belirteci almak için kullanılabilir.

3. Kullanıcının kimliği doğrulanmıyorsa, Azure AD/Yetkilendir uç noktası oturum açma için istemde bulunur.

    ![Hesap için oturum açma isteminin ekran görüntüsü](./media/openidoauth-tutorial/authentication.png)

4. Kullanıcı oturum açtıktan sonra, Azure AD kullanıcının bir onay sayfası gösterilmesi gerekip gerekmediğini belirler. Bu belirleme, kullanıcının (veya kuruluşun yöneticisinin) uygulama iznini zaten vermiş olup olmadığına bağlıdır.

   İzin verilmemişse, Azure AD kullanıcıdan onay ister ve çalışması için gereken izinleri görüntüler. Onay iletişim kutusunda görüntülenen izinler, Azure portal temsilci izinleri içinde seçili olanlarla eşleşir.

    ![Onay sayfası](./media/openidoauth-tutorial/consentpage.png)

Normal bir Kullanıcı bazı izinleri kabul edebilir. Diğer izinler, kiracı yöneticisinin onayını gerektirir.

## <a name="difference-between-admin-consent-and-user-consent"></a>Yönetici onayı ve Kullanıcı Onayı arasındaki fark

Yönetici olarak, kiracınızdaki tüm kullanıcılar adına bir uygulamanın temsilci izinlerini de kabul edebilirsiniz. Yönetici onayı, izin iletişim kutusunun Kiracıdaki her kullanıcı için görünmesini engeller. Yönetici rolüne sahip kullanıcılar Azure portal izin verebilir. Uygulamanızın **Ayarlar** sayfasından **gerekli izinler**  >  **yönetici izni ver**' i seçin.

![Izin verme düğmesi](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> **Yönetici onayı verme** düğmesi kullanılarak açık onay verilmesi artık ADAL.js kullanan tek sayfalı uygulamalar (maça 'lar) için gereklidir. Aksi takdirde, erişim belirteci istendiğinde uygulama başarısız olur.

Yalnızca uygulama izinleri her zaman kiracı yöneticisinin onayını gerektirir. Uygulamanız yalnızca uygulama izni isterse ve Kullanıcı uygulamada oturum açmaya çalışırsa bir hata iletisi görüntülenir. İleti, kullanıcının onay veremediği diyor.

Uygulamanız yönetici onayı gerektiren izinler kullanıyorsa, yöneticinin eylemi başlatabileceği bir düğme veya bağlantı gibi bir hareketinizin olması gerekir. Uygulamanızın bu eylem için gönderdiği istek, olağan OAuth2/OpenID Connect yetkilendirme isteğidir. Bu istek *Prompt = admin_consent* sorgu dizesi parametresini içerir.

Yönetici onaylı olduktan ve hizmet sorumlusu müşterinin kiracısında oluşturulduktan sonra, sonraki oturum açma istekleri *Prompt = admin_consent* parametresine gerek kalmaz. Yönetici İstenen izinlerin kabul edilebilir olduğunu karardığı için, kiracıya başka hiçbir Kullanıcı o noktadan sonra onay istenmez.

Bir kiracı yöneticisi, normal kullanıcıların uygulamaları kabul etme yeteneğini devre dışı bırakabilir. Bu yetenek devre dışıysa, uygulamanın kiracıya kullanılması için yönetici izni her zaman gereklidir. Uygulamanızı son kullanıcı onayı devre dışı olmadan test etmek istiyorsanız, [Azure Portal](https://portal.azure.com/)yapılandırma anahtarını bulabilirsiniz. Bu, **Kurumsal uygulamalar** altındaki [Kullanıcı ayarları](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) bölümünde yer alabilir.

*Prompt = admin_consent* parametresi, yönetici onayı gerektirmeyen izinler isteyen uygulamalar tarafından da kullanılabilir. Bir örnek, kiracı yöneticisinin "bir kez kaydolması" ve bu noktadan onay istenmediği bir deneyim gerektiren bir uygulamadır.

Bir uygulamanın yönetici onayı gerektirdiğini ve bir yönetici, *Prompt = admin_consent* parametresi olmadan oturum açmakta olduğunu düşünün. Yönetici uygulamaya başarıyla katıldıysa, yalnızca kendi Kullanıcı hesapları için geçerlidir. Normal kullanıcılar hala uygulamayı oturum açamıyor veya uygulamayı onaylayamayacak. Bu özellik, kiracı yöneticisine diğer kullanıcıların erişimine izin vermeden önce uygulamanızı keşfetmenizi sağlamak istiyorsanız yararlıdır.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Active Directory (Azure AD) kiracınızdaki bir uygulama için OıDC tabanlı çoklu oturum açma (SSO) ayarlama](../manage-apps/add-application-portal-setup-oidc-sso.md)