---
title: Microsoft Identity Platform geliştirici sözlüğü | Mavisi
description: Yaygın olarak kullanılan Microsoft Identity Platform geliştirici kavramlarının ve özelliklerinin bir listesi.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/21/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6b7c732a0af7fb3519cf255fa26478cd9ae82d2
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835109"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Microsoft Identity Platform geliştirici sözlüğü

Bu makale, Microsoft Identity platform kullanarak uygulama geliştirmeyi öğrenirken yararlı olan bazı temel geliştirici kavramları ve terminolojisi için tanımlar içerir.

## <a name="access-token"></a>erişim belirteci

Bir [yetkilendirme sunucusu](#authorization-server)tarafından verilen ve [korunan bir kaynak sunucusuna](#resource-server)erişmek için bir [istemci uygulaması](#client-application) tarafından kullanılan [güvenlik belirteci](#security-token) türü. Genellikle [JSON Web token (JWT)][JWT]biçiminde, belirteç, istenen erişim düzeyi için [kaynak sahibi](#resource-owner)tarafından istemciye verilen yetkilendirmeyi içerir. Belirteç, konuyla ilgili tüm ilgili [talepleri](#claim) içerir ve bu, istemci uygulamanın belirli bir kaynağa erişirken kimlik bilgileri olarak kullanılmasını sağlar. Bu Ayrıca, kaynak sahibinin kimlik bilgilerini istemciye kullanıma sunma gereksinimini ortadan kaldırır.

Erişim belirteçleri, temsil edilen kimlik bilgilerine bağlı olarak bazen "Kullanıcı + uygulama" veya "yalnızca uygulama" olarak adlandırılır. Örneğin, bir istemci uygulaması şunu kullandığında:

* ["Yetkilendirme kodu" yetkilendirmesi verme](#authorization-grant), Son Kullanıcı ilk olarak kaynak sahibi olarak kimlik doğrulaması yapar, kaynağa erişmek için istemciye yetkilendirme yetkisi verir. İstemci, erişim belirtecini alırken daha sonra kimliğini doğrular. Belirteç bazen "Kullanıcı + uygulama" belirteci olarak daha belirgin bir şekilde adlandırılır, bu da hem istemci uygulamasını yetkili olan kullanıcıyı hem de uygulamayı temsil eder.
* ["İstemci kimlik bilgileri" yetkilendirmesi verme](#authorization-grant), istemci tek kimlik doğrulamasını kaynak sahibinin kimlik doğrulaması/yetkilendirme olmadan çalışır, bu nedenle belirteç bazen "yalnızca uygulama" belirteci olarak adlandırılabilir.

Daha fazla bilgi için bkz. [Microsoft Identity platform belirteci başvurusu][AAD-Tokens-Claims] .

## <a name="application-id-client-id"></a>uygulama KIMLIĞI (istemci KIMLIĞI)

Azure AD 'nin, belirli bir uygulamayı ve ilişkili konfigürasyonları tanımlayan bir uygulama kaydıyla ilgili benzersiz tanımlayıcısı. Bu uygulama KIMLIĞI ([ISTEMCI kimliği](https://tools.ietf.org/html/rfc6749#page-15)) kimlik doğrulama istekleri gerçekleştirilirken ve geliştirme zamanında kimlik doğrulama kitaplıklarına sağlandığında kullanılır. Uygulama KIMLIĞI (istemci KIMLIĞI) gizli değil.

## <a name="application-manifest"></a>uygulama bildirimi

Uygulamanın kimlik yapılandırmasının JSON gösterimini üreten, ilişkili [uygulama][AAD-Graph-App-Entity] ve [ServicePrincipal][AAD-Graph-Sp-Entity] varlıklarını güncelleştirmek için bir mekanizma olarak kullanılan [Azure Portal][AZURE-portal]tarafından sunulan bir özellik. Daha fazla ayrıntı için bkz. [Azure Active Directory uygulama bildirimini anlama][AAD-App-Manifest] .

## <a name="application-object"></a>Uygulama nesnesi

[Azure Portal][AZURE-portal]bir uygulamayı kaydettiğinizde/güncelleştirdiğinizde, Portal bu kiracı için hem bir uygulama nesnesi hem de karşılık gelen bir [hizmet sorumlusu nesnesi](#service-principal-object) oluşturur/güncelleştirir. Uygulama nesnesi, uygulamanın kimlik yapılandırmasını küresel olarak (erişim sahibi olduğu tüm kiracılarda) *tanımlar* , buna karşılık gelen hizmet sorumlusu nesnesinin (ler) yerel olarak kullanım için *türetildiği* bir şablon sağlar çalışma zamanı (belirli bir kiracıda).

Daha fazla bilgi için bkz. [uygulama ve hizmet sorumlusu nesneleri][AAD-App-SP-Objects].

## <a name="application-registration"></a>uygulama kaydı

Bir uygulamanın kimlik ve erişim yönetimi işlevlerini Azure AD 'ye tümleştirmesini ve temsilciliğini sağlamak için bir Azure AD [kiracısıyla](#tenant)kayıtlı olması gerekir. Uygulamanızı Azure AD 'ye kaydettiğinizde, uygulamanız için bir kimlik yapılandırması sağladığınızda, Azure AD ile tümleştirilmesine ve şu gibi özellikler kullanmasına izin verilir:

* Azure AD kimlik yönetimi ve [OpenID Connect][OpenIDConnect] protokol uygulamasını kullanarak çoklu oturum açma 'nın güçlü yönetimi
* [İstemci uygulamalarına](#client-application)yönelik, OAuth 2,0 [yetkilendirme sunucusu](#authorization-server) aracılığıyla [korumalı kaynaklara](#resource-server) aracılı erişim
* Kaynak sahibi yetkilendirmesi temelinde korumalı kaynaklara istemci erişimini yönetmeye yönelik [onay çerçevesi](#consent) .

Daha fazla bilgi için bkz. [uygulamaları Azure Active Directory Ile tümleştirme][AAD-Integrating-Apps] .

## <a name="authentication"></a>kimlik doğrulaması

Kimlik ve erişim denetimi için kullanılacak bir güvenlik sorumlusu oluşturulmasına olanak sağlayan, meşru kimlik bilgileri için bir tarafı zorlu bir şekilde hareket etme. Bir [OAuth2 yetkilendirmesi](#authorization-grant) sırasında, kimlik doğrulaması yapılan kişinin kimliği, kullanılan erişime bağlı olarak, [kaynak sahibi](#resource-owner) veya [istemci uygulamasının](#client-application)rolünü doldurmasından oluşur.

## <a name="authorization"></a>authorization

Kimliği doğrulanmış bir güvenlik sorumlusu iznini verme eylemi. Azure AD programlama modelinde iki temel kullanım örneği vardır:

* [OAuth2 yetkilendirme verme](#authorization-grant) akışı sırasında: [kaynak sahibi](#resource-owner) [istemci uygulamaya](#client-application)yetkilendirme yaparken, istemcinin kaynak sahibinin kaynaklarına erişmesine izin verir.
* İstemci tarafından kaynak erişimi sırasında: [kaynak sunucu](#resource-server)tarafından uygulandığı gibi, erişim denetim kararlarını bunlara dayalı hale getirmek için [erişim belirtecinde](#access-token) bulunan [talep](#claim) değerleri kullanılarak.

## <a name="authorization-code"></a>yetkilendirme kodu

Dört OAuth2 [yetkilendirmesinin](#authorization-grant)bir parçası olarak, "yetkilendirme kodu" akışının bir parçası olarak, bir [istemci uygulamasına](#client-application) [Yetkilendirme uç noktası](#authorization-endpoint)tarafından bir kısa süreli "belirteç" sağlanmış. Kod, kaynak sahibinin kimlik doğrulamasının istenen kaynaklara erişim yetkisi olduğunu gösteren bir [kaynak sahibinin](#resource-owner)kimlik doğrulamasına yanıt olarak istemci uygulamasına döndürülür. Akışın bir parçası olarak, kod daha sonra bir [erişim belirteci](#access-token)için daha sonra kullanılır.

## <a name="authorization-endpoint"></a>Yetkilendirme uç noktası

[Yetkilendirme sunucusu](#authorization-server)tarafından uygulanan uç noktalardan biri, bir OAuth2 yetkilendirme verme akışı sırasında [yetkilendirme izni](#authorization-grant) sağlamak üzere [kaynak sahibiyle](#resource-owner) etkileşim kurmak için kullanılır. Kullanılan yetkilendirme verme akışına bağlı olarak, sağlanmış olan gerçek izin, [yetkilendirme kodu](#authorization-code) veya [güvenlik belirteci](#security-token)dahil değişiklik gösterebilir.

Daha fazla ayrıntı için OAuth2 belirtiminin [Yetkilendirme verme türleri][OAuth2-AuthZ-Grant-Types] ve [Yetkilendirme uç noktası][OAuth2-AuthZ-Endpoint] bölümlerine ve [openıdconnect belirtimine][OpenIDConnect-AuthZ-Endpoint] bakın.

## <a name="authorization-grant"></a>yetkilendirme izni

[İstemci uygulamasına](#client-application)verilen korunan kaynaklarına erişim için [kaynak sahibinin](#resource-owner) [yetkilendirmesini](#authorization) temsil eden bir kimlik bilgisi. İstemci uygulaması, istemci türüne/gereksinimlerine bağlı olarak, bir izin almak için [OAuth2 yetkilendirme çerçevesi tarafından tanımlanan dört izin türünden][OAuth2-AuthZ-Grant-Types] birini kullanabilir: "yetkilendirme kodu verme", "istemci kimlik bilgileri verme", "örtük izin" ve "kaynak sahip parola kimlik bilgileri verme ". İstemciye döndürülen kimlik bilgileri, kullanılan yetkilendirme verme türüne bağlı olarak bir [erişim belirteci](#access-token)ya da bir [yetkilendirme kodu](#authorization-code) (daha sonra bir erişim belirteci için değiştirilir).

## <a name="authorization-server"></a>yetkilendirme sunucusu

[OAuth2 yetkilendirme çerçevesi][OAuth2-Role-Def]tarafından tanımlandığı gibi, [kaynak sahibine](#resource-owner) başarıyla kimlik doğrulamasından ve yetkilendirmesini aldıktan sonra, [istemciye](#client-application) erişim belirteçleri vermekten sorumlu sunucu. [İstemci uygulaması](#client-application) , OAuth2 tanımlı [Yetkilendirme onayları](#authorization-grant)doğrultusunda yetkilendirme ve [belirteç](#token-endpoint) uç [](#authorization-endpoint) noktaları aracılığıyla çalışma zamanında yetkilendirme sunucusuyla etkileşime girer.

Microsoft Identity Platform uygulaması tümleştirmesinde Microsoft Identity platformu, Azure AD uygulamaları ve Microsoft hizmet API 'Leri için yetkilendirme sunucusu rolünü uygular, örneğin [Microsoft Graph API 'leri][Microsoft-Graph].

## <a name="claim"></a>talep

Bir [güvenlik belirteci](#security-token) , bir varlığa (örneğin, bir [istemci uygulaması](#client-application) veya [kaynak sahibi](#resource-owner)) başka bir varlığa (örneğin, [kaynak sunucusu](#resource-server)) yönelik onaylar sağlayan talepler içerir. Talepler, belirteç konusuyla ilgili olguları (örneğin, [yetkilendirme sunucusu](#authorization-server)tarafından kimliği doğrulanmış güvenlik sorumlusu) geçirerek ad/değer çiftleridir. Belirli bir belirteçte mevcut talepler, belirteç türü, konunun kimliğini doğrulamak için kullanılan kimlik bilgisi türü, uygulama yapılandırması vb. gibi çeşitli değişkenlere bağımlıdır.

Daha fazla bilgi için bkz. [Microsoft Identity platform belirteci başvurusu][AAD-Tokens-Claims] .

## <a name="client-application"></a>istemci uygulaması

[OAuth2 yetkilendirme çerçevesi][OAuth2-Role-Def]tarafından tanımlandığı gibi, [kaynak sahibi](#resource-owner)adına korumalı kaynak isteklerini yapan bir uygulamadır. "Client" terimi belirli bir donanım uygulama özelliği göstermez (örneğin, uygulamanın bir sunucuda, masaüstünde veya diğer cihazlarda yürütülüp yürütülmediği).

İstemci uygulaması, bir [OAuth2 yetkilendirme verme](#authorization-grant) akışına katılmak için bir kaynak sahibinden [Yetkilendirme](#authorization) ister ve kaynak sahibinin adına API 'ler/veriye erişebilir. OAuth2 yetkilendirme çerçevesi, istemci kimlik bilgilerinin gizliliğini koruma özelliğine bağlı olarak, "gizli" ve "genel" olmak üzere [iki tür istemci tanımlar][OAuth2-Client-Types]. Uygulamalar bir Web sunucusu üzerinde çalışan bir [Web istemcisi (gizli)](#web-client) , bir cihazda yüklü bir [yerel istemci (genel)](#native-client) veya bir cihazın tarayıcısında çalışan [Kullanıcı Aracısı tabanlı bir istemci (genel)](#user-agent-based-client) uygulayabilir.

## <a name="consent"></a>izniniz

Kaynak sahibinin adına, belirli [izinler](#permissions)altında korumalı kaynaklara erişmek için bir [istemci uygulamasına](#client-application)yetkilendirme izni veren [kaynak sahibi](#resource-owner) işlemi. İstemci tarafından istenen izinlere bağlı olarak, bir yöneticiye veya kullanıcıya sırasıyla kuruluş/bireysel verilerine erişim izni vermeniz istenir. [Birden çok kiracılı](#multi-tenant-application) bir senaryoda, uygulamanın [hizmet sorumlusu](#service-principal-object) Ayrıca, Kullanıcı kiracının kiracısına da kaydedilir.

Daha fazla bilgi için bkz. [onay çerçevesi](consent-framework.md) .

## <a name="id-token"></a>KIMLIK belirteci

Bir son kullanıcı [kaynak sahibinin](#resource-owner)kimlik doğrulamasıyla ilgili [talepleri](#claim) içeren bir [Yetkilendirme sunucusunun](#authorization-server) [Yetkilendirme uç noktası](#authorization-endpoint)tarafından sağlanmış bir [OpenID Connect][OpenIDConnect-ID-Token] [güvenlik belirteci](#security-token) . Erişim belirteci gibi, KIMLIK belirteçleri de dijital olarak imzalanmış [JSON Web token (JWT)][JWT]olarak gösterilir. Ancak, bir erişim belirtecinin aksine, kaynak erişimiyle ve özellikle erişim denetimiyle ilgili amaçlar için KIMLIK belirtecinin talepleri kullanılmaz.

Daha fazla bilgi için bkz. [Microsoft Identity platform belirteci başvurusu][AAD-Tokens-Claims] .

## <a name="microsoft-identity-platform"></a>Microsoft kimlik platformu

Microsoft kimlik platformu, Azure Active Directory (Azure AD) kimlik hizmeti ve geliştirici platformunun geliştirilmesiyle ortaya çıkmıştır. Bu platform geliştiricilerin tüm Microsoft kimlikleriyle oturum açan ve Microsoft Graph veya diğer Microsoft API'leri ya da geliştiricilerin derlemiş olduğu API'lere çağrı göndermek için gerekli belirteçleri alan uygulamalar derlemesini sağlar. Bu, bir kimlik doğrulama hizmeti, kitaplıklar, uygulama kaydı ve yapılandırma, tam geliştirici belgeleri, kod örnekleri ve diğer geliştirici içeriğinden oluşan tam özellikli bir platformdur. Microsoft Identity Platform OAuth 2.0 ve OpenID Connect gibi sektör standardı protokolleri destekler. Daha fazla bilgi için bkz. [Microsoft Identity platform hakkında](about-microsoft-identity-platform.md) .

## <a name="multi-tenant-application"></a>çok kiracılı uygulama

İstemcinin kaydolmasından farklı olan kiracılar dahil olmak üzere herhangi bir Azure AD kiracısında sağlanan kullanıcıların [](#tenant)oturum açmasını ve [onayını](#consent) sağlayan bir uygulama sınıfı. [Yerel istemci](#native-client) uygulamaları varsayılan olarak çok kiracılı olduğundan [Web istemcisi](#web-client) ve [Web kaynağı/API](#resource-server) uygulamaları, tek veya çok kiracılı arasında seçim yapabilme olanağına sahiptir. Bunun aksine, tek kiracılı olarak kaydedilen bir Web uygulaması, yalnızca uygulamanın kayıtlı olduğu bir kiracıda sağlanan kullanıcı hesaplarından oturum açma işlemleri için izin verir.

Daha fazla ayrıntı için bkz. [çok kiracılı uygulama modelini kullanarak herhangi bir Azure AD kullanıcısına kaydolma][AAD-Multi-Tenant-Overview] .

## <a name="native-client"></a>Yerel istemci

Bir cihaza yerel olarak yüklenen bir [istemci uygulaması](#client-application) türü. Tüm kod bir cihazda yürütüldüğü için, özel olarak/confidentially kimlik bilgilerini depolayamamasından dolayı "genel" istemci olarak kabul edilir. Daha fazla bilgi için bkz. [OAuth2 istemci türleri ve profilleri][OAuth2-Client-Types] .

## <a name="permissions"></a>izinler

[İstemci uygulaması](#client-application) , izin istekleri bildirerek bir [kaynak sunucusuna](#resource-server) erişim kazanır. İki tür mevcuttur:

* Oturum açan [kaynak sahibinden](#resource-owner)Temsilcili yetkilendirme kullanılarak [kapsam tabanlı](#scopes) erişim belirten "temsilci" izinleri, istemcinin [erişim belirtecindeki](#access-token) ["SCP" talepleri](#claim) olarak çalışma zamanında kaynağa sunulur.
* İstemci uygulamasının kimlik bilgilerini/kimliğini kullanarak [rol tabanlı](#roles) erişimi belirten "uygulama" izinleri, istemci erişim belirtecindeki ["Roller" talepleri](#claim) olarak çalışma zamanında kaynağa sunulur.

Ayrıca, [izin](#consent) süreci sırasında da yüzey, yönetici veya kaynak sahibine, kiracısındaki kaynaklara istemci erişimini verme/reddetme fırsatı verir.

İzin istekleri, istenen "temsilci Izinleri" ve "uygulama Izinleri" (ikincisi genel yönetici rolünde üyelik gerektirir) seçilerek [Azure Portal][AZURE-portal]bir uygulama için **API izinleri** sayfasında yapılandırılır. Ortak bir [istemci](#client-application) kimlik bilgilerini güvenli bir şekilde koruyamadığı için, bir [Gizli istemci](#client-application) hem temsilci hem de uygulama izinlerini isteme olanağına sahip olsa da yalnızca temsilci izinleri isteyebilir. İstemcinin [uygulama nesnesi](#application-object) , belirtilen Izinleri [requiredResourceAccess özelliğinde][AAD-Graph-App-Entity]depolar.

## <a name="resource-owner"></a>kaynak sahibi

[OAuth2 yetkilendirme çerçevesi][OAuth2-Role-Def]tarafından tanımlandığı gibi, korumalı bir kaynağa erişim izni veren bir varlıktır. Kaynak sahibi bir kişiyse, bu, son kullanıcı olarak adlandırılır. Örneğin, bir [istemci uygulaması](#client-application) , bir kullanıcının posta kutusuna [Microsoft Graph API][Microsoft-Graph]aracılığıyla erişmek istediğinde, bu, posta kutusunun kaynak sahibinden izin gerektirir.

## <a name="resource-server"></a>Kaynak sunucu

[OAuth2 yetkilendirme çerçevesi][OAuth2-Role-Def]tarafından tanımlandığı gibi, korumalı kaynakları barındıran bir sunucu, bir [erişim belirteci](#access-token)sunan [istemci uygulamalarına](#client-application) göre korunan kaynak isteklerini kabul edip yanıt verebilir. Korumalı kaynak sunucu veya kaynak uygulama olarak da bilinir.

Kaynak sunucu API 'Leri kullanıma sunar ve OAuth 2,0 yetkilendirme çerçevesini kullanarak [kapsamlar](#scopes) ve [Roller](#roles)aracılığıyla korunan kaynaklarına erişimi zorlar. Azure AD kiracı verilerine erişim sağlayan Azure AD Graph API ve posta ve takvim gibi verilere erişim sağlayan Office 365 API 'Lerine örnek olarak verilebilir. Bunların her ikisine de [Microsoft Graph API 'si][Microsoft-Graph]aracılığıyla erişilebilir.

Bir istemci uygulaması gibi, kaynak uygulamanın kimlik yapılandırması da bir Azure AD kiracısında [kayıt](#application-registration) yoluyla oluşturulur ve hem uygulama hem de hizmet sorumlusu nesnesi sağlanır. Azure AD Graph API gibi bazı Microsoft tarafından sağlanan API 'Ler, sağlama sırasında tüm kiracılarda önceden kaydedilmiş hizmet sorumlularına sahip olarak sunulur.

## <a name="roles"></a>roles

[Kapsamlar](#scopes)gibi roller, [kaynak sunucunun](#resource-server) korunan kaynaklarına erişimi yönetebilmeleri için bir yol sağlar. İki tür vardır: bir "Kullanıcı" rolü, kaynağa erişmesi gereken kullanıcılar/gruplar için rol tabanlı erişim denetimi uygular, ancak bir "uygulama" rolü erişim gerektiren [istemci uygulamalar](#client-application) için aynısını uygular.

Roller, kaynak tanımlı dizelerdir (örneğin, "gider onaylayan", "salt okunurdur", "Directory. ReadWrite. All"), kaynağın [uygulama bildirimi](#application-manifest)aracılığıyla [Azure Portal][AZURE-portal] yönetilir ve kaynağın [approles özelliğinde][AAD-Graph-Sp-Entity] depolanır . Azure portal, kullanıcıları "Kullanıcı" rollerine atamak ve "uygulama" rolüne erişmek için istemci [uygulama izinlerini](#permissions) yapılandırmak için de kullanılır.

Azure AD 'nin Graph API tarafından sunulan uygulama rollerinin ayrıntılı bir açıklaması için bkz. [Graph API Izin kapsamları][AAD-Graph-Perm-Scopes]. Adım adım bir uygulama örneği için bkz. [RBAC kullanarak erişimi yönetme ve Azure Portal][AAD-RBAC].

## <a name="scopes"></a>scopes

[Roller](#roles)gibi, kapsamlar da [kaynak sunucunun](#resource-server) korunan kaynaklarına erişimi yönetebilmeleri için bir yol sağlar. Kapsamlar, kaynağa sahibine göre atanmış erişim verilmiş bir [istemci uygulaması](#client-application) için [kapsam tabanlı][OAuth2-Access-Token-Scopes] erişim denetimi uygulamak için kullanılır.

Kapsamlar, kaynağın [uygulama bildirimi](#application-manifest)aracılığıyla [Azure Portal][AZURE-portal] yönetilen ve kaynağın [oauth2Permissions özelliğinde][AAD-Graph-Sp-Entity]depolanan kaynak tanımlı dizelerdir (örneğin, "posta. Read", "Directory. ReadWrite. All"). Azure portal Ayrıca, bir kapsama erişmek için istemci uygulaması için [temsilci izinleri](#permissions) yapılandırmak üzere kullanılır.

En iyi yöntem adlandırma kuralı, "Resource. Operation. Constraint" biçimini kullanmaktır. Azure AD 'nin Graph API tarafından sunulan kapsamlar hakkında ayrıntılı bir tartışma için bkz. [Graph API Izin kapsamları][AAD-Graph-Perm-Scopes]. Office 365 hizmetleri tarafından sunulan kapsamlar için bkz. [office 365 API izinleri başvurusu][O365-Perm-Ref].

## <a name="security-token"></a>güvenlik belirteci

OAuth2 belirteci veya SAML 2,0 onayı gibi talepler içeren imzalı bir belge. Bir OAuth2 [yetkilendirme izni](#authorization-grant)için, bir [erişim belirteci](#access-token) (OAuth2) ve [kimlik belirteci](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) , her ikisi de [JSON Web token (JWT)][JWT]olarak uygulanan güvenlik belirteçleri türleridir.

## <a name="service-principal-object"></a>hizmet sorumlusu nesnesi

[Azure Portal][AZURE-portal]bir uygulamayı kaydettiğinizde/güncelleştirdiğinizde, Portal bu kiracı için hem bir [uygulama nesnesi](#application-object) hem de karşılık gelen bir hizmet sorumlusu nesnesi oluşturur/güncelleştirir. Uygulama nesnesi, uygulamanın kimlik yapılandırmasını küresel olarak (ilişkili uygulamaya erişim izni verilen tüm kiracılarda) *tanımlar* ve buna karşılık gelen hizmet sorumlusu nesnesinin (ler) *bulunduğu şablondur* çalışma zamanında yerel olarak kullanılmak üzere türetilmiş (belirli bir kiracıda).

Daha fazla bilgi için bkz. [uygulama ve hizmet sorumlusu nesneleri][AAD-App-SP-Objects].

## <a name="sign-in"></a>oturum açma

Bir [güvenlik belirteci](#security-token) alma ve uygulama oturumunun bu duruma kapsamını belirleme amacıyla son kullanıcı kimlik doğrulamasını başlatan ve ilgili durumu yakalayan bir [istemci uygulaması](#client-application) işlemi. Durum, Kullanıcı profili bilgileri gibi yapıtları ve belirteç taleplerinden türetilen bilgileri içerebilir.

Uygulamanın oturum açma işlevi genellikle çoklu oturum açma (SSO) uygulamak için kullanılır. Ayrıca, son kullanıcının bir uygulamaya erişim kazanması için giriş noktası olarak bir "kaydolma" işlevi de olabilir (ilk oturum açma işlemi sırasında). Kaydolma işlevi, kullanıcıya özgü ek durumu toplayıp kalıcı hale getirmek için kullanılır ve [Kullanıcı onayı](#consent)gerekebilir.

## <a name="sign-out"></a>oturumu kapatma

[Oturum açma](#sign-in) sırasında [istemci uygulama](#client-application) oturumuyla ilişkili kullanıcı durumunu ayırmak için son kullanıcının kimlik doğrulamasını kaldırma işlemi

## <a name="tenant"></a>tenant

Azure AD dizininin bir örneği Azure AD kiracısı olarak adlandırılır. Aşağıdakiler dahil olmak üzere çeşitli özellikler sağlar:

* Tümleşik uygulamalar için bir kayıt defteri hizmeti
* Kullanıcı hesaplarının ve kayıtlı uygulamaların kimlik doğrulaması
* [Yetkilendirme uç noktası](#authorization-endpoint), [belirteç uç](#token-endpoint) noktası ve [çok kiracılı uygulamalar](#multi-tenant-application)tarafından kullanılan "ortak" uç noktası da dahil olmak üzere OAuth2 ve SAML DAHIL çeşitli protokolleri desteklemek için gereken REST uç noktaları.

Azure AD kiracılar kayıt sırasında Azure ve Office 365 abonelikleri tarafından oluşturulur/ilişkilendirilir ve abonelik için Kimlik ve Erişim Yönetimi özellikler sağlar. Azure abonelik yöneticileri Azure portal aracılığıyla ek Azure AD kiracıları da oluşturabilir. Bir kiracıya erişim sağlamak için kullanabileceğiniz çeşitli yollarla ilgili ayrıntılar için bkz. [Azure Active Directory kiracı alma][AAD-How-To-Tenant] . Abonelikler ve bir Azure AD kiracısı arasındaki ilişki hakkındaki ayrıntılar için bkz. [Azure aboneliklerinin Azure Active Directory ile ilişkilendirilmesi][AAD-How-Subscriptions-Assoc] .

## <a name="token-endpoint"></a>belirteç uç noktası

OAuth2 [yetkilendirmesini](#authorization-grant)desteklemek için [yetkilendirme sunucusu](#authorization-server) tarafından uygulanan uç noktalardan biri. Grant öğesine bağlı olarak, bir [erişim belirteci](#access-token) (ve ilgili "yenileme" belirteci) bir [Istemciye](#client-application)veya [OpenID Connect][OpenIDConnect] protokolüyle birlikte kullanıldığında [kimlik belirtecine](#id-token) sahip olmak için kullanılabilir.

## <a name="user-agent-based-client"></a>Kullanıcı Aracısı tabanlı istemci

Bir Web sunucusundan kod indiren ve tek sayfalı uygulama (SPA) gibi bir Kullanıcı aracısında (örneğin, bir Web tarayıcısı) yürütülen bir tür [istemci uygulaması](#client-application) . Tüm kod bir cihazda yürütüldüğü için, özel olarak/confidentially kimlik bilgilerini depolayamamasından dolayı "genel" istemci olarak kabul edilir. Daha fazla bilgi için bkz. [OAuth2 istemci türleri ve profilleri][OAuth2-Client-Types].

## <a name="user-principal"></a>Kullanıcı sorumlusu

Bir hizmet sorumlusu nesnesinin bir uygulama örneğini temsil etmek için kullanıldığı yönteme benzer şekilde, bir Kullanıcı asıl nesnesi bir kullanıcıyı temsil eden başka bir güvenlik sorumlusu türüdür. Azure AD Graph [Kullanıcı varlığı][AAD-Graph-User-Entity] , Kullanıcı ile ilgili, ad, Kullanıcı asıl adı, dizin rolü üyeliği vb. gibi özellikler de dahil olmak üzere bir kullanıcı nesnesi için şemayı tanımlar. Bu, çalışma zamanında bir Kullanıcı sorumlusu oluşturmak için Azure AD 'nin Kullanıcı kimliği yapılandırmasını sağlar. Kullanıcı sorumlusu, kimliği doğrulanmış bir kullanıcıyı çoklu oturum açma, [izin](#consent) temsilcisini kaydetme, erişim denetimi kararları alma, vb. için temsil etmek üzere kullanılır.

## <a name="web-client"></a>Web istemcisi

Bir Web sunucusundaki tüm kodu yürüten ve kimlik bilgilerini sunucuda güvenli bir şekilde depolayarak bir "gizli" istemcisi olarak işlev görebilecek bir tür [istemci uygulaması](#client-application) . Daha fazla bilgi için bkz. [OAuth2 istemci türleri ve profilleri][OAuth2-Client-Types].

## <a name="next-steps"></a>Sonraki adımlar

[Microsoft Identity platform Geliştirici Kılavuzu][AAD-Dev-Guide] , [uygulama tümleştirmeye][AAD-How-To-Integrate] genel bakış ve Microsoft Identity 'nin temelleri dahil olmak üzere tüm Microsoft kimlik platformu geliştirmeyle ilgili konular için kullanılacak giriş sayfasıdır. [ Platform kimlik doğrulaması ve desteklenen kimlik doğrulama senaryoları][AAD-Auth-Scenarios]. Ayrıca, [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=)'da hızlıca çalışmaya başlama hakkında Öğreticiler & kod örnekleri bulabilirsiniz.

Yeni tanımlara yönelik istekler de dahil olmak üzere bu içeriği iyileştirmek ve şekillendirmek için aşağıdaki açıklamalar bölümünü kullanın!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
