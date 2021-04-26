---
title: Azure AD kullanıcıları 'nda oturum açma uygulamaları oluşturun
titleSuffix: Microsoft identity platform
description: Herhangi bir Azure Active Directory kiracısından bir kullanıcının oturum açmasını sağlayan çok kiracılı bir uygulamanın nasıl oluşturulacağını gösterir.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: marsma, jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: 825a7d8c53552120a861657c7f3df7ae8f488c18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581729"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Nasıl yapılır: Çok kiracılı uygulama desenini kullanarak istediğiniz bir Azure Active Directory kullanıcısıyla oturum açma

Birçok kuruluşa bir hizmet olarak yazılım (SaaS) uygulaması sunadıysanız, uygulamanızı herhangi bir Azure Active Directory (Azure AD) kiracısından oturum açma işlemlerini kabul edecek şekilde yapılandırabilirsiniz. Bu yapılandırma, *uygulamanızı çok kiracılı hale getirme* olarak adlandırılır. Herhangi bir Azure AD kiracısındaki kullanıcılar, kendi hesabını uygulamanızla birlikte kullanmak için uygulamanızda oturum açabilirler.

Kendi hesap sistemine sahip mevcut bir uygulamanız varsa veya diğer bulut sağlayıcılarından gelen diğer oturum açma türlerini destekliyorsa, herhangi bir kiracıdan Azure AD oturum açma işlemi basittir. Uygulamanızı kaydedin, OAuth2, OpenID Connect veya SAML aracılığıyla oturum açma kodu ekleyin ve uygulamanıza bir ["Microsoft hesabıyla oturum açın" düğmesi][AAD-App-Branding] koyun.

> [!NOTE]
> Bu makalede, Azure AD için tek kiracılı bir uygulama oluşturma konusunda zaten bilgi sahibi olduğunuz varsayılır. Değilseniz, [Geliştirici Kılavuzu giriş][AAD-Dev-Guide]sayfasındaki Hızlı başlangıçlardan biriyle başlayın.

Uygulamanızı bir Azure AD çok kiracılı uygulamasına dönüştürmek için dört adım vardır:

1. [Uygulama kaydınızı çok kiracılı olarak güncelleştirme](#update-registration-to-be-multi-tenant)
2. [Kodu, sık karşılaşılan uç noktasına istek gönderecek şekilde güncelleştirin](#update-your-code-to-send-requests-to-common)
3. [Kodunuzu birden çok veren değerini işleyecek şekilde güncelleştirin](#update-your-code-to-handle-multiple-issuer-values)
4. [Kullanıcı ve yönetici onayını anlayın ve uygun kod değişikliklerini yapın](#understand-user-and-admin-consent)

Her adıma ayrıntılı olarak bakalım. Ayrıca, [Azure AD ve OpenID Connect kullanarak Microsoft Graph çağıran çok kiracılı SaaS Web uygulamasına](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)doğrudan örnek yapıya atlayabilirsiniz.

## <a name="update-registration-to-be-multi-tenant"></a>Kaydı çok kiracılı olacak şekilde Güncelleştir

Varsayılan olarak, Azure AD 'de Web uygulaması/API kayıtları tek kiracısıdır. [Azure Portal][AZURE-portal] uygulama kaydınızın **kimlik doğrulama** bölmesinde **Desteklenen hesap türleri** anahtarını bularak ve **herhangi bir kuruluş dizinindeki hesaplara** ayarlayarak kaydınızı çok kiracılı hale getirebilirsiniz.

Bir uygulamanın çok kiracılı hale getirilbilmesi için Azure AD, uygulamanın uygulama KIMLIĞI URI 'sinin genel olarak benzersiz olmasını gerektirir. Uygulama Kimliği URI'si, uygulamanın protokol iletileri içinde tanımlanması için kullanılan yollardan biridir. Tek kiracılı bir uygulamada Uygulama Kimliği URI'sinin kiracı içinde benzersiz olması yeterlidir. Azure AD'nin uygulamayı tüm kiracılar arasında bulabilmesi için çok kiracılı uygulamada bu değerin genel olarak benzersiz olması gerekir. Genel olarak benzersiz olma gereksinimi, Uygulama Kimliği URI'sinin Azure AD kiracısının doğrulanmış etki alanı ile eşleşen bir ana bilgisayar adına sahip olması şartıyla sağlanır.

Varsayılan olarak, Azure portal ile oluşturulan uygulamalar, uygulama oluşturma sırasında genel olarak benzersiz bir uygulama KIMLIĞI URI 'SI kümesine sahiptir, ancak bu değeri değiştirebilirsiniz. Örneğin, kiracınızın adı contoso.onmicrosoft.com ise geçerli bir uygulama KIMLIĞI URI 'SI olur `https://contoso.onmicrosoft.com/myapp` . Kiracınızda doğrulanmış bir etki alanı varsa `contoso.com` , geçerli bir uygulama kimliği URI 'si de olur `https://contoso.com/myapp` . Uygulama Kimliği URI'si bu düzene uygun olmadığında uygulamayı çok kiracılı hale getirme işlemi başarısız olur.

## <a name="update-your-code-to-send-requests-to-common"></a>Kodu, istekleri sık karşılaşılan 'a gönderecek şekilde güncelleştirin

Tek kiracılı bir uygulamada, oturum açma istekleri kiracının oturum açma uç noktasına gönderilir. Örneğin, contoso.onmicrosoft.com için uç nokta şöyle olacaktır: `https://login.microsoftonline.com/contoso.onmicrosoft.com` . Bir kiracının uç noktasına gönderilen istekler, bu Kiracıdaki kullanıcılara (veya konuklara) bu Kiracıdaki uygulamalara oturum açabilir.

Çok kiracılı bir uygulamayla, uygulama kullanıcının hangi kiracıya ait olduğunu bilmez, dolayısıyla bir kiracının uç noktasına istek gönderemezsiniz. Bunun yerine, istekler tüm Azure AD kiracılarında çoğullanır olan bir uç noktaya gönderilir: `https://login.microsoftonline.com/common`

Microsoft Identity platform, sık karşılaşılan uç noktasında bir istek aldığında, kullanıcıyı ' de imzalar ve sonuç olarak kullanıcının hangi kiracının olduğunu bulur. Sık karşılaşılan uç noktası, Azure AD 'nin desteklediği tüm kimlik doğrulama protokolleriyle birlikte çalışarak, OpenID Connect, OAuth 2,0, SAML 2,0 ve WS-Federation.

Uygulamanın oturum açma yanıtı, daha sonra kullanıcıyı temsil eden bir belirteç içerir. Belirteçteki veren değeri, bir uygulamaya kullanıcının hangi kiracının olduğunu söyler. Bir yanıt sık karşılaşılan uç noktasından döndüğünde, belirteçteki veren değeri kullanıcının kiracısına karşılık gelir.

> [!IMPORTANT]
> Sık karşılaşılan uç noktası kiracı değil ve veren değil, yalnızca bir çoğullama. /Common kullanılırken, belirteçlerin doğrulanması için uygulamanızın mantığı, bunu hesaba alacak şekilde güncelleştirilmesi gerekir.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Kodunuzu birden çok veren değerini işleyecek şekilde güncelleştirin

Web uygulamaları ve Web API 'Leri Microsoft Identity platform 'dan belirteçleri alır ve doğrular.

> [!NOTE]
> Yerel istemci uygulamaları Microsoft Identity platformundan belirteç talep eder ve bunları aldıktan sonra, bunları doğrulanan API 'lere gönderir. Yerel uygulamalar erişim belirteçlerini doğrulamaz ve bunları donuk olarak kabul etmelidir.

Uygulamanın Microsoft Identity platformundan aldığı belirteçleri nasıl doğruladığına bakalım. Tek kiracılı bir uygulama normalde şöyle bir uç nokta değeri alır:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com
```

... ve bir meta veri URL 'SI (Bu örnekte, OpenID Connect) oluşturmak için aşağıdaki gibi kullanır:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration
```

belirteçleri doğrulamak için kullanılan iki kritik bilgi parçasını indirmek için: kiracının İmzalama anahtarları ve veren değeri.

Her Azure AD kiracısı, Şu biçimdeki benzersiz bir veren değerine sahiptir:

```http
https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/
```

... burada GUID değeri, kiracının kiracı KIMLIĞININ yeniden adlandırma güvenli sürümüdür. İçin önceki meta veri bağlantısını seçerseniz `contoso.onmicrosoft.com` belgesinde bu veren değerini görebilirsiniz.

Tek kiracılı bir uygulama bir belirteci doğrulaırsa, meta veri belgesinden imzalama anahtarlarına karşı belirtecin imzasını denetler. Bu test, belirteçteki veren değerinin meta veri belgesinde bulunan ile eşleştiğinden emin olmanızı sağlar.

Sık karşılaşılan uç noktası bir kiracıya karşılık gelmediğinden ve veren olmadığından, sık karşılaşılan meta verilerinde veren değerini incelediğinizde gerçek bir değer yerine şablonlu bir URL 'ye sahiptir:

```http
https://sts.windows.net/{tenantid}/
```

Bu nedenle, çok kiracılı bir uygulama belirteçleri yalnızca, belirteçteki değerle meta verilerde veren değeriyle eşleştirerek doğrulayamaz `issuer` . Çok kiracılı bir uygulama, hangi veren değerlerinin geçerli olduğunu ve veren değerinin kiracı KIMLIĞI kısmına bağlı olmayan karar vermek için mantığa ihtiyaç duyuyor.

Örneğin, çok kiracılı bir uygulama yalnızca kendi hizmetleri için kaydolan belirli kiracılardan oturum açma izni veriyorsa, `tid` kiracının aboneler listesinde olduğundan emin olmak için belirteçteki veren değerini veya talep değerini denetlemesi gerekir. Çok kiracılı bir uygulama yalnızca bireyler ile ilgilenir ve kiracılar temelinde hiçbir erişim kararı vermezse, verenin değerini tamamen yok sayabilir.

[Çok kiracılı örneklerde][AAD-Samples-MT], BIR Azure AD kiracının oturum açmasını sağlamak için veren doğrulaması devre dışıdır.

## <a name="understand-user-and-admin-consent"></a>Kullanıcı ve yönetici onayını anlama

Bir kullanıcının Azure AD 'de bir uygulamada oturum açması için, uygulamanın kullanıcının kiracısında temsil edilebilmesi gerekir. Bu, kuruluşun kiracılarından kullanıcılar uygulamada oturum açtığında benzersiz ilkeler uygulama gibi işlemler yapmasına olanak sağlar. Tek kiracılı bir uygulama için, bu kayıt daha kolay; Bu, uygulamayı [Azure Portal][AZURE-portal]kaydettiğinizde meydana gelen bir uygulamadır.

Çok kiracılı bir uygulama için, uygulamanın ilk kaydı, geliştirici tarafından kullanılan Azure AD kiracısında bulunur. Farklı bir kiracıya ait bir kullanıcı uygulamada ilk kez oturum açtığında, Azure AD, uygulamanın istediği izinleri onay vermesini ister. Onay varsa, kullanıcının kiracısında *hizmet sorumlusu* adlı uygulamanın bir temsili oluşturulur ve oturum açma işlemi devam edebilir. Kullanıcının uygulamaya iznini kaydeden dizinde da bir temsili oluşturulur. Uygulamanın uygulaması ve ServicePrincipal nesneleri ve birbirleriyle birbirleriyle ilgili ayrıntılar için bkz. [uygulama nesneleri ve hizmet sorumlusu nesneleri][AAD-App-SP-Objects].

![Tek katmanlı uygulamaya onay gösterir][Consent-Single-Tier]

Bu onay deneyimi, uygulama tarafından istenen izinlerden etkilenir. Microsoft Identity platformu, yalnızca uygulama ve temsilci olarak iki tür izinleri destekler.

* Temsilci atanan izin, bir uygulamaya kullanıcının yapabildiği öğelerin bir alt kümesi için oturum açmış bir kullanıcı olarak davranma olanağı verir. Örneğin, bir uygulamaya, oturum açan kullanıcının takvimini okumak için temsilci izni verebilirsiniz.
* Yalnızca uygulama izni, uygulamanın kimliğine doğrudan verilir. Örneğin, uygulamaya kimin oturum açtığından bağımsız olarak, bir Kiracıdaki kullanıcıların listesini okumak için uygulamaya yalnızca uygulamaya izin verebilirsiniz.

Bazı izinler normal bir kullanıcı tarafından alınabilir, diğerleri ise kiracı yöneticisinin iznini gerektirir.

Kullanıcı ve yönetici onayı hakkında daha fazla bilgi edinmek için bkz. [yönetici onayı iş akışını yapılandırma](../manage-apps/configure-admin-consent-workflow.md).

### <a name="admin-consent"></a>Yönetici onayı

Yalnızca uygulama izinleri her zaman kiracı yöneticisinin onayını gerektirir. Uygulamanız yalnızca uygulama izni isterse ve Kullanıcı uygulamada oturum açmaya çalışırsa, kullanıcının izin veremediğini bildiren bir hata iletisi görüntülenir.

Ayrıca, bazı temsilci izinleri kiracı yöneticisinin onayını gerektirir. Örneğin, oturum açmış kullanıcı olarak Azure AD 'ye geri yazma özelliği, kiracı yöneticisinin onayını gerektirir. Yalnızca uygulama izinleri gibi, sıradan bir kullanıcı yönetici onayı gerektiren bir temsilci izni isteyen bir uygulamada oturum açmaya çalışırsa, uygulamanız bir hata alır. Bir iznin yönetici onayı gerektirip gerektirmediğini, kaynağı yayımlayan geliştirici tarafından belirlenir ve kaynağın belgelerinde bulunabilir. [Microsoft Graph API 'si][MSFT-Graph-permission-scopes] için izin belgeleri, hangi izinlerin yönetici onayı gerektirdiğini gösterir.

Uygulamanız yönetici onayı gerektiren izinler kullanıyorsa, yöneticinin eylemi başlatabildiği bir düğme veya bağlantı gibi bir hareketi vardır. Uygulamanızın bu eylem için gönderdiği istek, sorgu dizesi parametresini de içeren olağan OAuth2/OpenID Connect yetkilendirme isteğidir `prompt=admin_consent` . Yönetici bir kez daha alındıktan ve hizmet sorumlusu müşterinin kiracısında oluşturulduktan sonra, sonraki oturum açma isteklerinde parametreye gerek yoktur `prompt=admin_consent` . Yönetici istenen izinleri kabul etmiş olduğundan, Kiracıdaki başka hiçbir kullanıcıya o noktadan sonra izin istenmez.

Bir kiracı yöneticisi, normal kullanıcıların uygulamaları kabul etme yeteneğini devre dışı bırakabilir. Bu yetenek devre dışıysa, uygulamanın kiracıya kullanılması için yönetici izni her zaman gereklidir. Uygulamanızı son kullanıcı onayı devre dışı olmadan test etmek istiyorsanız, **Kurumsal uygulamalar** altındaki **[kullanıcı ayarları](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** bölümünde [Azure Portal][AZURE-portal] yapılandırma anahtarını bulabilirsiniz.

`prompt=admin_consent`Parametresi, yönetici izni gerektirmeyen izinler isteyen uygulamalar tarafından da kullanılabilir. Uygulamanın, kiracı yöneticisinin "bir kez kaydolduğunda" bir deneyim gerektirmesi ve bu noktadan onay istenmediği başka kullanıcılardan ne zaman kullanılacağı hakkında bir örnek vardır.

Bir uygulama yönetici onayı gerektiriyorsa ve bir yönetici, gönderilen parametre olmadan oturum açarsa `prompt=admin_consent` , yönetici uygulamaya başarıyla katıldıysa, **yalnızca kendi Kullanıcı hesapları için** geçerlidir. Normal kullanıcılar yine de uygulama üzerinde oturum açamaz veya onay vermez. Bu özellik, kiracı yöneticisine diğer kullanıcılara erişim izni vermeden önce uygulamanızı keşfetmeye olanak tanımak istiyorsanız yararlıdır.

### <a name="consent-and-multi-tier-applications"></a>Onay ve çok katmanlı uygulamalar

Uygulamanızın her biri Azure AD 'de kendi kaydıyla temsil edilen birden çok katmanı olabilir. Örneğin, bir Web API 'SI veya Web API 'SI çağıran bir Web uygulaması çağıran yerel bir uygulama. Her iki durumda da, istemci (yerel uygulama veya Web uygulaması), kaynağı (Web API) çağırmak için izinler ister. İstemcisinin bir müşterinin kiracısına başarıyla katılmasına izin vermek için, kullanıcının kiracısında izinleri talep eden tüm kaynakların zaten mevcut olması gerekir. Bu koşul karşılanmazsa, Azure AD kaynağın önce eklenmesi gerektiğini belirten bir hata döndürür.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Tek bir kiracıda birden çok katman

Mantıksal uygulamanız iki veya daha fazla uygulama kaydı içeriyorsa (örneğin, ayrı bir istemci ve kaynak) Bu bir sorun olabilir. İlk olarak kaynağı Müşteri kiracısına nasıl alabilirim? Azure AD, istemci ve kaynağın tek bir adımda toplanmasına olanak tanıyarak bu durumu ele alır. Kullanıcı, izin sayfasında hem istemci hem de kaynak tarafından istenen izinlerin toplam toplamını görür. Bu davranışı etkinleştirmek için, kaynağın uygulama kaydı, uygulamanın uygulama KIMLIĞINI `knownClientApplications` [uygulama bildiriminde][AAD-App-Manifest]bir olarak içermelidir. Örnek:

```json
"knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]
```

Bu makalede, bu makalenin sonundaki [ilgili içerik](#related-content) bölümünde yer aldığı çok katmanlı yerel istemci BIR Web API örneğinde arama gösterilmiştir. Aşağıdaki diyagramda, tek bir kiracıda kayıtlı çok katmanlı bir uygulama için izin özeti sağlanmaktadır.

![Çok katmanlı bilinen istemci uygulamasına onay gösterir][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Birden çok kiracıda birden çok katman

Benzer bir durum, uygulamanın farklı katmanları farklı kiracılarda kayıtlıysa meydana gelir. Örneğin, Exchange Online API 'sini çağıran bir yerel istemci uygulaması oluşturma durumunu göz önünde bulundurun. Yerel uygulamayı geliştirmek ve daha sonra yerel uygulamanın bir müşterinin kiracısında çalışması için, Exchange Online hizmet sorumlusu mevcut olmalıdır. Bu durumda, geliştirici ve müşterinin kiracılarında oluşturulacak hizmet sorumlusu için Exchange Online satın alması gerekir.

Microsoft dışında bir kuruluş tarafından oluşturulan bir API ise, API 'nin geliştiricisi, müşterilerinin müşterilerinin kiracılarına uygulamayı onaylaması için bir yol sağlamalıdır. Önerilen tasarım, üçüncü taraf geliştiricisinin, oturum açma işlemi için bir Web istemcisi olarak işlev uygulayabilen API 'yi oluşturması için de kullanılır. Bunu yapmak için:

1. API 'nin çok kiracılı uygulama kaydı/kod gereksinimlerini uyguladığından emin olmak için önceki bölümleri izleyin.
2. API 'nin kapsamlarını/rollerini açığa çıkarmak için, kaydın "oturum aç ve kullanıcı profilini oku" izninin (varsayılan olarak sağlanmış) içerdiğinden emin olun.
3. Web istemcisinde bir oturum açma/kaydolma sayfası uygulayın ve [Yönetici onay](#admin-consent) kılavuzunu izleyin.
4. Kullanıcı uygulamaya ulaştıktan sonra kiracısında hizmet sorumlusu ve onay temsili bağlantıları oluşturulur ve yerel uygulama API için belirteçleri alabilir.

Aşağıdaki diyagramda, farklı kiracılarda kayıtlı çok katmanlı bir uygulama için izin özeti sağlanmaktadır.

![Çok katmanlı çok taraflı uygulamanın onayını gösterir][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Onay iptal ediliyor

Kullanıcılar ve yöneticiler dilediğiniz zaman uygulamanıza yönelik onayı iptal edebilir:

* Kullanıcılar, [erişim paneli uygulamalar][AAD-Access-Panel] listesinden kaldırarak tek tek uygulamalara erişimi iptal eder.
* Yöneticiler, [Azure Portal][AZURE-portal] [Kurumsal uygulamalar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) bölümünü kullanarak uygulamaları kaldırarak uygulamalara erişimi iptal eder.

Bir yönetici bir Kiracıdaki tüm kullanıcılar için bir uygulamaya onay verirse, kullanıcılar erişimi ayrı ayrı iptal edemez. Yalnızca yönetici, erişimi iptal edebilir ve yalnızca tüm uygulama için geçerlidir.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Çok kiracılı uygulamalar ve önbelleğe alma erişimi belirteçleri

Çok kiracılı uygulamalar, Azure AD tarafından korunan API 'Leri çağırmak için de erişim belirteçleri alabilir. Çok kiracılı bir uygulamayla Microsoft kimlik doğrulama kitaplığı (MSAL) kullanılırken, başlangıçta/Common kullanarak bir kullanıcı belirteci istemesi, yanıt alacak ve ardından aynı kullanıcı için/commonkullanarak daha sonra bir belirteç istemesi için sık karşılaşılan bir hata. Azure AD 'nin yanıtı/Common değil bir kiracıdan geldiğinden, MSAL belirteci kiracıdan olduğu gibi önbelleğe alır. Sonraki sık karşılaşılan çağrısı, Kullanıcı için önbellek girdisini isabetsiz bir erişim belirteci alır ve kullanıcıdan yeniden oturum açması istenir. Önbelleğin eksik olmaması için, zaten oturum açmış olan bir kullanıcıya yönelik sonraki çağrıların kiracının uç noktasına yapıldığından emin olun.

## <a name="related-content"></a>İlgili içerik

* [Çok kiracılı uygulama örneği](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Uygulamalar için markalama yönergeleri][AAD-App-Branding]
* [Uygulama nesneleri ve hizmet sorumlusu nesneleri][AAD-App-SP-Objects]
* [Uygulamaları Azure Active Directory ile tümleştirme][AAD-Integrating-Apps]
* [Onay çerçevesine genel bakış][AAD-Consent-Overview]
* [Microsoft Graph API izin kapsamları][MSFT-Graph-permission-scopes]

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, herhangi bir Azure AD kiracısından bir kullanıcının oturum açmasını sağlayan bir uygulama oluşturmayı öğrendiniz. Uygulamanız ve Azure AD arasında tek Sign-On (SSO) etkinleştirildikten sonra, uygulamanızı Microsoft 365 gibi Microsoft kaynakları tarafından sunulan API 'Lere erişmek için de güncelleştirebilirsiniz. Bu, uygulamanızda, profil resmi veya bir sonraki takvim randevusu gibi bağlamsal bilgileri göstermek gibi kişiselleştirilmiş bir deneyim sunmanızı sağlar.

Azure AD 'ye yönelik API çağrılarını ve Exchange, SharePoint, OneDrive, OneNote gibi Microsoft 365 hizmetleri ve daha fazlasını yapma hakkında daha fazla bilgi edinmek için [MICROSOFT Graph API][MSFT-Graph-overview]adresini ziyaret edin.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: /samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: /graph/
[MSFT-Graph-permission-scopes]: /graph/permissions-reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken