---
title: Microsoft Identity platformunda kimlik doğrulaması | Mavisi
description: Microsoft Identity platformunda (v 2.0) kimlik doğrulamasının temelleri hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.collection: M365-identity-device-management
ms.openlocfilehash: 317efa17b294e859ef8a092451aca70b5b836fe7
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167811"
---
# <a name="authentication-basics"></a>Kimlik doğrulaması temel bilgileri

## <a name="what-is-authentication"></a>Kimlik doğrulaması nedir?

Bu makalede, korunan Web uygulamaları, Web API 'Leri veya korumalı Web API 'Lerini çağıran uygulamalar oluşturmak için anlamanız gereken birçok kimlik doğrulama kavramı yer almaktadır.

**Kimlik doğrulama** , sizin olduğunu söylediğinizde sizin olduğunu kanıtlama işlemidir. Kimlik doğrulaması bazen AuthN şeklinde kısaltılabilir.

**Yetkilendirme** , kimliği doğrulanmış bir tarafın bir şeyi yapmak için izin verme işlemidir. Hangi verilerin erişebileceğini ve bu verilerle yapabileceklerinizi belirtir. Yetkilendirme bazen AuthZ şeklinde kısaltılabilir.

Her birinin kendi Kullanıcı adı ve parola bilgilerini korumalarına veya birden çok uygulama arasında Kullanıcı eklemeniz veya kaldırmanız gerektiğinde yüksek bir yönetim yükü sunan uygulamalar oluşturmak yerine, uygulamalar bu sorumluluğu merkezi bir kimlik sağlayıcısına devredebilir.

Azure Active Directory (Azure AD), bulutta merkezi bir kimlik sağlayıcıdır. Kimlik doğrulama ve yetkilendirmeyi yetkilendirme, bir kullanıcının belirli bir konumda olmasını gerektiren koşullu erişim ilkeleri, çok faktörlü kimlik doğrulamasının kullanılması ve bir kullanıcının bir kez oturum açmasını ve sonra otomatik olarak kullanılabilmesini sağlar. aynı merkezi dizini paylaşan tüm Web uygulamalarında oturum açıldı. Bu yetenek, çoklu oturum açma (SSO) olarak adlandırılır.

Merkezi bir kimlik sağlayıcısı, dünyanın her yerindeki kullanıcıların, kuruluşun ağından oturum açması gerekmeyen uygulamalar için daha da önemlidir. Azure AD, kullanıcıların kimliğini doğrular ve erişim belirteçleri sağlar. Erişim belirteci, bir yetkilendirme sunucusu tarafından verilen bir güvenlik belirtecidir. Web API 'Lerine ve diğer korumalı kaynaklara erişmek için kullanılabilecek, Kullanıcı ve belirtecin istendiği uygulama hakkında bilgiler içerir.

Microsoft Identity platform, OAuth 2,0 ve OpenID Connect gibi sektör standardı protokoller desteğiyle ve farklı bir şekilde açık kaynak kitaplıkları sunarak uygulama geliştiricileri için kimlik doğrulamasını basitleştirir. kodlamaya hızlı bir şekilde başlamanıza yardımcı olacak platformlar. Bu platform geliştiricilerin tüm Microsoft kimlikleriyle oturum açan ve Microsoft Graph veya diğer Microsoft API'leri ya da geliştiricilerin derlemiş olduğu API'lere çağrı göndermek için gerekli belirteçleri alan uygulamalar derlemesini sağlar. Daha fazla bilgi için bkz. [Microsoft Identity platform 'un gelişi](about-microsoft-identity-platform.md).

### <a name="tenants"></a>Kira

Bulut kimlik sağlayıcısı birçok kuruluşa hizmet eder. Kullanıcıları farklı kuruluşlardan ayrı tutmak için, Azure AD kiracılar halinde bölümlenmiştir ve kuruluşa göre tek bir kiracı olur.

Kiracılar kullanıcıları ve bunlarla ilişkili uygulamaları izler. Microsoft Identity platformu, kişisel Microsoft hesaplarıyla oturum açarken kullanılan kullanıcıları da destekler.

Azure AD Ayrıca, kuruluşların kullanıcılara, genellikle Google hesabı gibi sosyal kimlikler kullanarak oturum açmalarına olanak tanıyan Azure Active Directory B2C de sağlar. Daha fazla bilgi için bkz. [Azure Active Directory B2C belgeleri](https://docs.microsoft.com/azure/active-directory-b2c) .

### <a name="security-tokens"></a>Güvenlik belirteçleri

Güvenlik belirteçleri, kullanıcılar ve uygulamalar hakkında bilgi içerir. Azure AD, talepler içeren JSON tabanlı belirteçleri (JWTs) kullanır. Bir talep, bir varlıkla ilgili onaylama işlemleri sağlar. Uygulamalar, şu gibi çeşitli görevler için talepler kullanabilir:

* Belirteç doğrulanıyor
* Konunun Dizin kiracısını tanımlama
* Kullanıcı bilgilerini görüntüleme
* Konunun yetkilendirmesini belirleme

Bir talep, şöyle bilgiler sağlayan anahtar-değer çiftlerinden oluşur:

- belirteci oluşturan güvenlik belirteci sunucusu.
- belirtecin oluşturulduğu tarih.
- Konu, Kullanıcı (daemon 'ları dışında) gibi.
- belirtecin oluşturulduğu uygulama olan hedef kitle.
- belirteç için istenen uygulama (istemci). Web uygulamaları söz konusu olduğunda, bu, hedef kitle ile aynı olabilir.

Daha ayrıntılı talep bilgileri için bkz. [erişim belirteçleri](access-tokens.md) ve [Kimlik belirteçleri](id-tokens.md).

Belirteç doğrulamak için, belirtecin oluşturulduğu uygulamaya, kullanıcının oturum açmış olduğu Web uygulamasına veya Web API 'sinin çağrılmakta olduğu uygulamaya kadar olur. Belirteç, güvenlik belirteci sunucusu (STS) tarafından özel anahtarla imzalanır. STS ilgili ortak anahtarı yayımlar. Bir belirteci doğrulamak için, uygulama, imzayı özel anahtar kullanılarak oluşturulduğunu doğrulamak üzere STS ortak anahtarını kullanarak imzayı doğrular.

Belirteçler yalnızca sınırlı bir süre için geçerlidir. STS, genellikle bir dizi belirteç sağlar: uygulamaya veya korunan kaynağa erişmek için bir erişim belirteci ve erişim belirtecinin süresi dolmak üzere kapatıldığında erişim belirtecini yenilemek için kullanılan yenileme belirteci. 

Erişim belirteçleri, `Authorization` üst bilgisinde taşıyıcı belirteci olarak bir Web API 'sine geçirilir. Bir uygulama STS 'ye yenileme belirteci sağlayabilir ve uygulamaya yönelik kullanıcı erişimi iptal edilmediği takdirde, yeni bir erişim belirteci ve yeni bir yenileme belirteci geri alır. Bu, kuruluşa ayrılmaya yönelik senaryonun işlenme yönteminden oluşur. STS yenileme belirtecini aldığında, Kullanıcı artık yetkilendirilmezse başka bir geçerli erişim belirteci vermez.

## <a name="application-model"></a>Uygulama modeli

Uygulamalar, kullanıcıların kendilerine oturum açmasını veya bir kimlik sağlayıcısı için oturum açma yetkisini devredebilir. Azure AD tarafından desteklenen oturum açma senaryoları hakkında bilgi edinmek için bkz. [kimlik doğrulama akışları ve uygulama senaryoları](authentication-flows-app-scenarios.md) .

Bir kimlik sağlayıcısının bir kullanıcının belirli bir uygulamaya erişimi olduğunu bilmesini sağlamak için, hem Kullanıcı hem de uygulamanın kimlik sağlayıcısına kayıtlı olması gerekir. Uygulamanızı Azure AD 'ye kaydettiğinizde, uygulamanız için Azure AD ile tümleşmesini sağlayan bir kimlik yapılandırması sağladığınızda. Uygulamayı kaydetmek şunları da sağlar:

- oturum açma iletişim kutusunda uygulamanızın markasını özelleştirin. Bu, bir kullanıcının uygulamanız için sahip olacağı ilk deneyim olduğu için önemlidir.
- Kullanıcıların yalnızca kuruluşunuza ait olmaları durumunda oturum açmalarına izin vermek istediğinize karar verin. Bu tek kiracılı bir uygulamadır. Ya da kullanıcıların herhangi bir iş veya okul hesabını kullanarak oturum açmalarına izin verin. Bu, çok kiracılı bir uygulamadır. Ayrıca, kişisel Microsoft hesaplarına veya bir sosyal hesaba LinkedIn, Google, vb. izin verebilirsiniz.
- kapsam izinleri iste. Örneğin, oturum açmış kullanıcının profilini okuma izni veren "User. Read" kapsamını isteyebilirsiniz.
- Web API 'nize erişimi tanımlayan kapsamları tanımlayın. Genellikle, bir uygulama API 'nize erişmek istediğinde tanımladığınız kapsamlar için izin istemesi gerekecektir.
- Azure AD ile bir gizli anahtar paylaşarak, uygulamanın kimliğini Azure AD 'ye kanıtlar.  Bu, uygulamanın gizli bir istemci uygulaması olduğu durum ile ilgilidir. Gizli bir istemci uygulaması, kimlik bilgilerini güvenli bir şekilde tutan bir uygulamadır. Kimlik bilgilerini depolamak için güvenilir bir arka uç sunucusu gerekir.

Kaydolduktan sonra uygulamaya, belirteç istediğinde uygulamanın Azure AD ile paylaştığı bir GUID gönderilir. Uygulama gizli bir istemci uygulaması ise, sertifikaların veya gizli anahtarların kullanılıp kullanılmadığını bağlı olarak gizli anahtar veya ortak anahtarı da paylaşır.

Microsoft Identity platform iki ana işlevi yerine getiren bir modeli kullanarak uygulamaları temsil eder:

Uygulamayı desteklediği kimlik doğrulama protokollerine göre belirleyip kimlik doğrulaması için gereken tüm tanımlayıcıları, URL 'Leri, gizli dizileri ve ilgili bilgileri sağlayın.
Microsoft Identity platformu:

* Çalışma zamanında kimlik doğrulamasını desteklemek için gereken tüm verileri tutar.
* Bir uygulamanın erişmesi gerekebilecek kaynakları ve belirli bir isteğin yerine getirilmesi gereken koşulları belirlemek için tüm verileri tutar.
* Uygulama geliştiricisinin kiracısında ve diğer Azure AD kiracılarında uygulama sağlamayı uygulamak için altyapı sağlar.
* Belirteç istek süresi boyunca Kullanıcı onayını işler ve kiracılar genelinde uygulamaların dinamik sağlamasını kolaylaştırır

Onay, bir istemci uygulamanın, kaynak sahibi adına belirli izinler altında korumalı kaynaklara erişmesi için bir kaynak sahibi verme yetkilendirmesi işlemidir. Microsoft Identity platformu:

* Kullanıcıların ve yöneticilerin uygulamanın kendileri adına kaynaklara erişmesine dinamik olarak onay vermesini veya reddetmesini sağlar.
* Yöneticilerin uygulamaların gerçekleştirebilecekleri işlemler, belirli uygulamalara erişebilecek kullanıcılar ve erişilen dizin kaynakları hakkında son kararı vermesini sağlar.

Microsoft Identity platformunda bir **uygulama nesnesi** , bir uygulamayı soyut bir varlık olarak açıklar. Dağıtım zamanında, Microsoft Identity platform bir dizin veya kiracı içindeki bir uygulamanın somut örneğini temsil eden bir **hizmet sorumlusu**oluşturmak için uygulama nesnesini bir şema olarak kullanır. Hizmet sorumlusu, uygulamanın belirli bir hedef dizinde gerçekten ne yapabileceğini, bunu kullanabilecek kaynakları, hangi kaynakların erişebileceğini ve bu şekilde olduğunu tanımlar. Microsoft Identity platformu, bir uygulama nesnesinden **onay**aracılığıyla bir hizmet sorumlusu oluşturur.

Aşağıdaki diyagramda, izin tarafından yönetilen basitleştirilmiş bir Microsoft Identity platformu sağlama akışı gösterilmektedir. İki kiracı (A ve B) gösterir. Kiracı, uygulamanın sahibi. B kiracısı, bir hizmet sorumlusu aracılığıyla uygulamayı örnekleniyor.  

![Onay temelli basitleştirilmiş sağlama akışı](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Bu sağlama akışında:

1. B kiracısından bir kullanıcı uygulamayla oturum açmaya çalışır, yetkilendirme uç noktası uygulama için bir belirteç ister.
1. Kimlik doğrulaması için Kullanıcı kimlik bilgileri alındı ve doğrulanır.
1. Kullanıcıdan, B kiracısına erişim kazanmak için uygulamanın onayını sağlaması istenir.
1. Microsoft Identity platformu, B kiracısında bir hizmet sorumlusu oluşturmak için bir şema olarak kiracı 'daki uygulama nesnesini kullanır.
1. Kullanıcı istenen belirteci alır.

Bu işlemi, ek kiracılar için yineleyebilirsiniz. Kiracı A, uygulama için şemayı saklar (uygulama nesnesi). Uygulamanın izin verdiği tüm diğer kiracıların kullanıcıları ve yöneticileri, uygulamanın her Kiracıdaki ilgili hizmet sorumlusu nesnesi aracılığıyla ne yapmasına izin verileceğini kontrol edin. Daha fazla bilgi için bkz. [Microsoft Identity platformunda uygulama ve hizmet sorumlusu nesneleri](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Azure AD ile Web uygulaması oturum açma akışı

Bir kullanıcı tarayıcıda bir Web uygulamasına gittiğinde aşağıdakiler olur:

- Web uygulaması, kullanıcının kimlik doğrulamasının yapılıp yapılmayacağını belirler.
- Kullanıcının kimliği doğrulanmadıysa, Web uygulaması Kullanıcı oturumu açmak için Azure AD 'ye temsilciler atar. Bu oturum açma, kuruluşun ilkesiyle uyumlu olacaktır. Bu, kullanıcıdan çok faktörlü kimlik doğrulaması kullanarak veya bir parola (örneğin, Windows Hello kullanarak) kullanarak kimlik bilgilerini girmesini isteyebilir.
- Kullanıcıdan, istemci uygulamasına gereken erişimi onaylaması istenir. Bu nedenle, Azure AD 'nin Kullanıcı tarafından onaylanan erişimi temsil eden belirteçleri sunabilmesi için istemci uygulamalarının Azure AD 'ye kaydedilmesi gerekir.

Kullanıcının kimliği başarıyla doğrulandı:

- Azure AD, Web uygulamasına bir belirteç gönderir.
- Kullanıcının tarayıcı tanımlama bilgisi jar içindeki kimliğini içeren Azure AD 'nin etki alanı ile ilişkili bir tanımlama bilgisi kaydedilir. Bir uygulama Azure AD yetkilendirme uç noktasına gitmek için tarayıcıyı bir dahaki sefer kullandığında, tarayıcı tanımlama bilgisini Kullanıcı yeniden oturum açmak zorunda kalmayacak şekilde gösterir. Bu, SSO 'nun elde edilmesi için de bir yoldur. Tanımlama bilgisi Azure AD tarafından üretilir ve yalnızca Azure AD tarafından anlaşılabilirler.
- Web uygulaması, belirteci doğrular. Doğrulama başarılı olursa, Web uygulaması korumalı sayfayı görüntüler ve tarayıcının tanımlama bilgisi jar 'e bir oturum tanımlama bilgisi kaydeder. Kullanıcı başka bir sayfaya gittiğinde, Web uygulaması, oturum tanımlama bilgisine göre kullanıcının kimliğinin doğrulandığını bilir.

Aşağıdaki sıra diyagramı bu etkileşimi özetler:

![Web uygulaması kimlik doğrulama işlemi](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Web uygulaması, kullanıcının kimliğinin doğrulanmadığını nasıl belirler

Web uygulaması geliştiricileri, kimlik doğrulaması gerektirdiğini veya yalnızca belirli sayfaların olduğunu belirtebilir. Örneğin, ASP.NET/ASP.NET Core 'da, denetleyici eylemlerine `[Authorize]` özniteliği eklenerek yapılır. 

Bu öznitelik, ASP.NET Kullanıcı kimliğini içeren bir oturum tanımlama bilgisinin varlığını denetlamasına neden olur. Bir tanımlama bilgisi yoksa, ASP.NET kimlik doğrulamasını belirtilen kimlik sağlayıcısına yönlendirir. Kimlik sağlayıcısı Azure AD ise, Web uygulaması, kimlik doğrulamasını https://login.microsoftonline.comolarak yeniden yönlendirir ve bu da bir oturum açma iletişim kutusu görüntüler.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Bir Web uygulamasının Azure AD 'de oturum açması ve bir belirteç alacağı

Tarayıcı aracılığıyla Kullanıcı kimlik doğrulaması gerçekleşir. OpenID Protokolü standart HTTP protokol iletilerini kullanır.
- Web uygulaması, Azure AD 'yi kullanmak için tarayıcıya bir HTTP 302 (Redirect) gönderir.
- Kullanıcının kimliği doğrulandığında Azure AD, tarayıcıyı kullanarak bir yeniden yönlendirme kullanarak belirteci Web uygulamasına gönderir.
- Yeniden yönlendirme, Web uygulaması tarafından yeniden yönlendirme URI 'SI biçiminde sağlanır. Bu yeniden yönlendirme URI 'SI, Azure AD uygulama nesnesi ile kaydedilir. Uygulama çeşitli URL 'Lerde dağıtılabilmesi için birkaç yeniden yönlendirme URI 'si olabilir. Bu nedenle, Web uygulamasının kullanılacak yeniden yönlendirme URI 'sini belirtmesi de gerekecektir.
- Azure AD, Web uygulaması tarafından gönderilen yeniden yönlendirme URI 'sinin, uygulamanın kayıtlı yeniden yönlendirme URI 'lerinden biri olduğunu doğrular.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Azure AD ile masaüstü ve mobil uygulama oturum açma akışı

Yukarıda açıklanan akış, küçük farklılıklar ile masaüstü ve mobil uygulamalar için geçerlidir.

Masaüstü ve mobil uygulamalar, kimlik doğrulaması için katıştırılmış bir Web denetimi veya bir sistem tarayıcısı kullanabilir. Aşağıdaki diyagramda, bir masaüstü veya mobil uygulamanın, erişim belirteçleri almak ve Web API 'Lerini çağırmak için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) nasıl kullandığı gösterilmektedir.

![Masaüstü uygulaması nasıl görünür](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL, belirteçleri almak için bir tarayıcı kullanır ve Web uygulamalarında olduğu gibi, kimlik doğrulamasını Azure AD 'ye devreder.

Azure AD, Web Apps için olduğu gibi tarayıcıda aynı kimlik tanımlama bilgisini kaydettiği için, yerel veya mobil uygulama sistem tarayıcısını kullanıyorsa, karşılık gelen Web uygulamasıyla SSO 'yu hemen alır.

Varsayılan olarak, MSAL daha tümleşik bir kullanıcı deneyimi sağlamak için gömülü bir denetimin kullanıldığı .NET Framework masaüstü uygulamaları hariç sistem tarayıcısını kullanır.

## <a name="next-steps"></a>Sonraki adımlar

Ortak koşulları öğrenmek için [Microsoft Identity Platform geliştirici sözlüğü](developer-glossary.md) ' ne bakın.
Microsoft Identity platform tarafından desteklenen kullanıcıların kimliğini doğrulamak için diğer senaryolar hakkında daha fazla bilgi edinmek için bkz. [kimlik doğrulama akışları ve uygulama senaryoları](authentication-flows-app-scenarios.md) .
Microsoft hesapları, Azure AD hesapları ve Azure AD B2C kullanıcıları tek ve kolaylaştırılmış bir programlama modelinde çalışan uygulamalar geliştirmenize yardımcı olan Microsoft kitaplıkları hakkında bilgi edinmek için bkz. [msal kitaplıkları](msal-overview.md) .
