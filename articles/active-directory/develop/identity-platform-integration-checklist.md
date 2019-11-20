---
title: Microsoft Identity platform için en iyi uygulamalar | Mavisi
description: Microsoft Identity platformu ile tümleştirilirken en iyi uygulamalar, öneriler ve ortak aşırı bakış hakkında bilgi edinin.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 1ce33e90cab1c5172cbf14470f24345d0446b0de
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200290"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Microsoft Identity platform en iyi uygulamaları ve önerileri

Bu makalede, Microsoft Identity platformu ile tümleştirilirken en iyi uygulamalar, öneriler ve ortak aşırı Özellikler vurgulanır.  Bu denetim listesi, sizi yüksek kaliteli ve güvenli bir tümleştirmede size rehberlik edecektir. Kimlik platformuyla uygulamanızın tümleştirmesinin kalitesini ve güvenliğini koruduğunuzdan emin olmak için bu listeyi düzenli aralıklarla gözden geçirin. Denetim listesi, tüm uygulamanızı gözden geçirmeyi amaçlanmamış. Platform üzerinde geliştirmeler yaptığımız için denetim listesinin içeriği değişebilir.

Yeni başladıysanız, kimlik doğrulama temelleri, Microsoft Identity platformunda uygulama senaryoları ve daha fazlası hakkında bilgi edinmek için [Microsoft Identity Platform belgelerine](index.yml) göz atın.

Uygulamanızın [Microsoft Identity platformu](https://docs.microsoft.com/azure/active-directory/develop/)ile etkin bir şekilde tümleştirildiğinden emin olmak için aşağıdaki denetim listesini kullanın.

## <a name="basics"></a>Temel Bilgiler

|   |   |
|---|---|
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | [Microsoft Platformu ilkelerini](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409)okuyun ve anlayın. Uygulamanızın, kullanıcıları ve platformu korumak üzere tasarlandıkları koşullara uyduğundan emin olun. |

## <a name="ownership"></a>Fazlasının

|   |   |
|---|---|
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Uygulamaları kaydetmek ve yönetmek için kullandığınız hesapla ilişkili bilgilerin güncel olduğundan emin olun. |

## <a name="branding"></a>Olanağı

|   |   |
|---|---|
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | [Uygulamalar Için marka yönergelerine](howto-add-branding-in-azure-ad-apps.md)uyar. |
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Uygulamanız için anlamlı bir ad ve logo sağlayın. Bu bilgiler, [uygulamanızın izin isteminde](application-consent-experience.md)görüntülenir. Kullanıcılarınızın bilinçli kararlar verebilmeleri için, adınızın ve logonuzun şirketinizin/ürününüzün temsilciyle aynı olduğundan emin olun. Herhangi bir ticari marka ihlal olmadığından emin olun. |

## <a name="privacy"></a>Gizlilik

|   |   |
|---|---|
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Uygulamanızın hizmet koşulları ve gizlilik bildirimi için bağlantılar sağlar. |

## <a name="security"></a>Güvenlik

|   |   |
|---|---|
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Yeniden yönlendirme URI 'larınızı yönetin: <ul><li>Tüm yeniden yönlendirme URI 'Lerinin sahipliğini koruyun ve DNS kayıtlarını güncel tutun.</li><li>URI 'larınız için joker karakterler (*) kullanmayın.</li><li>Web uygulamaları için tüm URI 'Lerin güvenli ve şifreli olduğundan emin olun (örneğin, https şemaları kullanarak).</li><li>Genel istemciler için, varsa platforma özgü yeniden yönlendirme URI 'Lerini kullanın (temel olarak iOS ve Android için). Aksi takdirde, uygulamanıza geri çağrı yaparken çarpışmaları engellemek için yüksek miktarda rasgelelik ile yeniden yönlendirme URI 'Leri kullanın.</li><li>Uygulamanız yalıtılmış bir Web aracısından kullanılıyorsa, https://login.microsoftonline.com/common/oauth2/nativeclient ' ı kullanabilirsiniz.</li><li>Tüm kullanılmayan veya gereksiz yeniden yönlendirme URI 'Lerini düzenli olarak gözden geçirin ve kırpın.</li></ul> |
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Uygulamanız bir dizine kayıtlıysa, uygulama kayıt sahiplerinin listesini simge durumuna küçültün ve el ile izleyin. |
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Açıkça gerekli olmadığı takdirde [OAuth2 örtük verme akışı](v2-oauth2-implicit-grant-flow.md) desteğini etkinleştirmeyin. Geçerli senaryo hakkında [buradan](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)bilgi edinin. |
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Kullanıcı adı/parola ötesine geçin. Kullanıcıların parolalarını doğrudan işleyen [kaynak sahibi parola kimlik bilgisi akışını (ROPC)](v2-oauth-ropc.md)kullanmayın. Bu akış, yüksek derecede güven ve Kullanıcı pozlaması gerektirir ve yalnızca diğer, daha güvenli ve akış kullanılmıyorsa kullanılmalıdır. Bu akış, bazı senaryolarda (DevOps gibi) hala gereklidir, ancak bunu kullanmanın uygulamanıza kısıtlamalar getirdiğinden emin olun.  Daha modern yaklaşımlar için [kimlik doğrulama akışlarını ve uygulama senaryolarını](authentication-flows-app-scenarios.md)okuyun.|
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Web uygulamaları, Web API 'Leri ve Daemon uygulamaları için gizli uygulama kimlik bilgilerinizi koruyun ve yönetin. Parola kimlik bilgilerini (istemci gizli dizileri) değil [sertifika kimlik bilgilerini](active-directory-certificate-credentials.md)kullanın. Parola kimlik bilgisi kullanmanız gerekiyorsa, el ile ayarlama. Kimlik bilgilerini kod veya yapılandırmaya depolamayın ve bu kullanıcıların insanların işlenmesine izin vermez. Mümkünse, [Azure kaynakları için Yönetilen kimlikler](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) veya kimlik bilgilerinizi depolamak ve düzenli aralıklarla döndürmek için [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) kullanın. |
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Uygulamanızın en az ayrıcalık izinleri istediğinizden emin olun. Yalnızca uygulamanızın kesinlikle ihtiyacı olan ve yalnızca ihtiyacınız olduğunda izin ister. Farklı [izin türlerini](v1-permissions-and-consent.md#types-of-permissions)anlayın. Yalnızca gerekli olduğunda uygulama izinlerini kullanın; mümkün olduğunca temsilci izinleri kullanın. Microsoft Graph izinlerinin tam listesi için bu [izin başvurusuna](https://docs.microsoft.com/graph/permissions-reference)bakın. |
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Bir API 'YI Microsoft Identity platformunu kullanarak güvenli hale getiriyorsanız, ortaya çıkarmak gereken izinleri dikkatle düşünün. Çözümünüz için doğru ayrıntı düzeyini ve hangi izinlerin yönetici onayı gerektirdiğini göz önünde bulundurun. Herhangi bir yetkilendirme kararı vermeden önce gelen belirteçlerde beklenen izinleri denetleyin. |

## <a name="implementation"></a>Uygulama

|   |   |
|---|---|
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Kullanıcıların güvenli bir şekilde oturum açması için modern kimlik doğrulama çözümlerini (OAuth 2,0, [OpenID Connect](v2-protocols-oidc.md)) kullanın. |
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) |  OAuth 2,0 ve açık KIMLIK gibi protokollere doğrudan programmayın. Bunun yerine, [Microsoft kimlik doğrulama kitaplığı 'ndan (msal)](msal-overview.md)yararlanın. MSAL kitaplıkları, güvenlik protokollerini kullanımı kolay bir kitaplıkta güvenli bir şekilde sarmalıdır ve [koşullu erişim](/azure/active-directory/conditional-access/overview) senaryoları, cihaz genelinde [Çoklu oturum açma (SSO)](/azure/active-directory/manage-apps/what-is-single-sign-on)ve yerleşik belirteç önbelleğe alma desteği için yerleşik destek alabilirsiniz. Daha fazla bilgi için bkz. Microsoft tarafından desteklenen [istemci kitaplıkları](reference-v2-libraries.md#microsoft-supported-client-libraries) ve [Ara yazılım kitaplıkları](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) ve [uyumlu üçüncü taraf istemci kitaplıkları](reference-v2-libraries.md#compatible-client-libraries)listesi.<br/><br/>Kimlik doğrulama protokolleri için kod oluşturmanız gerekiyorsa, [MICROSOFT SDL](https://www.microsoft.com/sdl/default.aspx)gibi bir metodolojiyi izlemelisiniz. Her protokol için standartlar belirtimlerinde güvenlik açısından dikkat edilmesi gereken noktaları ödeyin.|
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) |  Mevcut uygulamaları [Azure Active Directory kimlik doğrulama kitaplığından (ADAL)](active-directory-authentication-libraries.md) [Microsoft kimlik doğrulama kitaplığı](msal-overview.md)'na geçirin. MSAL, Microsoft 'un en son kimlik platformu çözümüdür ve ADAL için tercih edilir. .NET, JavaScript, Android, iOS, macOS ve ayrıca Python ve Java için genel önizlemede bulunur. [Adal.net](msal-net-migration.md), [adal. js](msal-compare-msal-js-and-adal-js.md)ve [adal.net ve iOS Broker](msal-net-migration-ios-broker.md) uygulamalarını geçirme hakkında daha fazla bilgi edinin.|
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) |  Mobil uygulamalar için, her platformu uygulama kayıt deneyimini kullanarak yapılandırın. Uygulamanızın çoklu oturum açma için Microsoft Authenticator veya Microsoft Şirket Portalı avantajlarından yararlanması için, uygulamanızın yapılandırılmış bir "aracı yeniden yönlendirme URI 'SI" olması gerekir. Bu sayede, kimlik doğrulamasından sonra Microsoft 'un uygulamanıza denetim döndürmesini sağlar. Her platformu yapılandırırken, uygulama kayıt deneyimi süreç boyunca size yol gösterir. Çalışan bir örnek indirmek için hızlı başlangıcı kullanın. İOS 'ta, mümkün olduğunda aracılar ve sistem Web Görünümü ' ni kullanın.|
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) |  Web Apps veya Web API 'Lerinde, hesap başına tek bir belirteç önbelleği tutun.  Web Apps için, belirteç önbelleğinin hesap KIMLIĞI tarafından anahtarlanır olması gerekir.  Web API 'Leri için, bu hesabın API 'yi çağırmak için kullanılan belirtecin karması ile anahtarlanır olması gerekir. MSAL.NET, .NET Framework ve .NET Core alt platformlarında özel belirteç önbelleği serileştirmesini sağlar. Güvenlik ve performans nedenleriyle, önerimiz Kullanıcı başına bir önbellek serileştirilmemiz olur. Daha fazla bilgi için [belirteç önbelleği serileştirme](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)hakkında makalesini okuyun.|
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Uygulamanızın gerektirdiği veriler [Microsoft Graph](https://developer.microsoft.com/graph)aracılığıyla kullanılabiliyorsa, bireysel apı yerine Microsoft Graph uç noktasını kullanarak bu veriler için izinleri isteyin. |
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) |Erişim belirteci değerine bakmayın veya bir istemci olarak ayrıştırmaya çalışın.  Bunlar, hiçbir uyarı olmadan değerleri, biçimleri değiştirebilir veya hatta şifreli hale gelebilir-istemci hakkında bir şey öğrenmek veya Microsoft Graph çağırmak gerekiyorsa, her zaman id_token kullanın.  Yalnızca Web API 'Leri, erişim belirteçlerini ayrıştırmalıdır (Bu, biçimi ve şifreleme anahtarlarını ayarlamaklardır). |

## <a name="end-user-experience"></a>Son Kullanıcı deneyimi

|   |   |
|---|---|
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Son kullanıcılar ve yöneticilerin uygulamanıza güvenip güvenmediğine yönelik yeterli bilgiye sahip olması için [onay deneyimini anlayın](application-consent-experience.md) ve uygulamanızın izin sorma parçalarını yapılandırın. |
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Etkileşimli akışlardan önce sessiz kimlik doğrulaması (sessiz belirteç alımı) gerçekleştirmeye çalışırken uygulamanızı kullanırken, kullanıcının oturum açma kimlik bilgilerini kaç kez girmesi gerektiğini en aza indirin. |
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Her oturum açma için "Prompt = onay" kullanmayın. Yalnızca ek izinler için onay sormanız gerektiğini belirlerseniz (örneğin, uygulamanızın gerekli izinlerini değiştirdiyseniz), yalnızca Prompt = onay kullanın. |
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Uygun olduğunda, uygulamanızı kullanıcı verileriyle zenginleştirin. [Microsoft Graph API 'sinin](https://developer.microsoft.com/graph) kullanılması, bunu yapmanın kolay bir yoludur. Başlamanıza yardımcı olabilecek [grafik Gezgini](https://developer.microsoft.com/graph/graph-explorer) aracı. |
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Yöneticilerin kiracılarına kolayca onay verebilmeleri için uygulamanızın gerektirdiği tüm izin kümesini kaydedin. Kullanıcıların, uygulamanın ilk kez başlatıldığında istekte bulunma veya karışmasına neden olabilecek izinler istediğini anlamasına yardımcı olmak için çalışma zamanında [artımlı onay](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) kullanın. |
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Temiz bir [Çoklu oturum açma deneyimi](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)uygulayın. Bu bir gizlilik ve güvenlik gereksinimidir ve iyi bir kullanıcı deneyimi sunar. |

## <a name="testing"></a>Test Etme

|   |   |
|---|---|
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Kullanıcılarınızın uygulamanızı kullanma yeteneğini etkileyebilecek [koşullu erişim ilkeleri](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) için test. |
| ![kutusunun](./media/active-directory-integration-checklist/checkbox-two.svg) | Uygulamanızı, desteklemeyi planladığınız tüm olası hesapları (örneğin, iş veya okul hesapları, kişisel Microsoft hesapları, alt hesaplar ve bağımsız hesaplar) test edin. |

## <a name="additional-resources"></a>Ek kaynaklar

V2.0 hakkında ayrıntılı bilgileri keşfedin:

* [Microsoft Identity platform (v 2.0 genel bakış)](v2-overview.md)
* [Microsoft Identity platform protokolleri başvurusu](active-directory-v2-protocols.md)
* [Erişim belirteçleri başvurusu](access-tokens.md)
* [Kimlik belirteçleri başvurusu](id-tokens.md)
* [Kimlik doğrulama kitaplıkları başvurusu](reference-v2-libraries.md)
* [Microsoft Identity platformunda izinler ve onay](v2-permissions-and-consent.md)
* [Microsoft Graph API'si](https://developer.microsoft.com/graph)
