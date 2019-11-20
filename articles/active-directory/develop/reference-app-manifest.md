---
title: Azure Active Directory uygulama bildirimini anlama | Microsoft Docs
description: Uygulamanın Azure AD kiracısındaki kimlik yapılandırmasını temsil eden ve OAuth yetkilendirme, onay deneyimini ve daha fazlasını kolaylaştırmak için kullanılan Azure Active Directory Uygulama bildiriminin ayrıntılı kapsamı.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: e44c3e607f3d5a5ea8269b9885a4fec54000bb5f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389577"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory uygulama bildirimi

Uygulama bildirimi Microsoft Identity platformunda bir uygulama nesnesinin tüm özniteliklerinin tanımını içerir. Ayrıca uygulama nesnesini güncelleştirmek için bir mekanizma işlevi görür. Uygulama varlığı ve şeması hakkında daha fazla bilgi için [Graph API uygulama varlığı belgelerine](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)bakın.

Bir uygulamanın özniteliklerini Azure portal veya [REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) veya [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications)kullanarak program aracılığıyla yapılandırabilirsiniz. Ancak, bir uygulamanın özniteliğini yapılandırmak için uygulama bildirimini düzenlemeniz gereken bazı senaryolar vardır. Bu senaryolar şunlardır:

* Uygulamayı Azure AD çok kiracılı ve kişisel Microsoft hesapları olarak kaydettiniz, Kullanıcı arabirimindeki desteklenen Microsoft hesaplarını değiştiremezsiniz. Bunun yerine, desteklenen hesap türünü değiştirmek için uygulama bildirimi düzenleyicisini kullanmanız gerekir.
* Uygulamanızın desteklediği izinleri ve rolleri tanımlamanız gerekiyorsa, uygulama bildirimini değiştirmeniz gerekir.

## <a name="configure-the-app-manifest"></a>Uygulama bildirimini yapılandırma

Uygulama bildirimini yapılandırmak için:

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. **Azure Active Directory** hizmetini seçin ve ardından **uygulama kayıtları**' ı seçin.
1. Yapılandırmak istediğiniz uygulamayı seçin.
1. Uygulamanın **Genel Bakış** sayfasında, **Bildirim** bölümünü seçin. Web tabanlı bir bildirim Düzenleyicisi açılır ve bu, portalı içindeki bildirimi düzenlemenize olanak tanır. İsteğe bağlı olarak, bildirimi yerel olarak düzenlemek için **İndir** ' i seçip uygulamanıza yeniden uygulamak Için **karşıya yükle** ' yi kullanabilirsiniz.

## <a name="manifest-reference"></a>Bildirim başvurusu

> [!NOTE]
> **Açıklama**sonrasında **örnek değer** sütununu göremiyorsanız, **örnek değer** sütununu görene kadar tarayıcı pencerenizi en üst düzeye çıkarın ve kaydırın/çekin.

| Anahtar  | Değer türü | Açıklama  | Örnek değer |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Null yapılabilir Int32 | Kaynak tarafından beklenen erişim belirteci sürümünü belirtir. Bu, erişim belirtecini istemek için kullanılan uç noktadan veya istemciden bağımsız olarak üretilen JWT sürümünü ve biçimini değiştirir.<br/><br/>Kullanılan uç nokta, v 1.0 veya v 2.0, istemci tarafından seçilir ve yalnızca id_tokens sürümünü etkiler. Kaynakların desteklenen erişim belirteci biçimini belirtecek şekilde `accesstokenAcceptedVersion` ' i açıkça yapılandırması gerekir.<br/><br/>@No__t-0 için olası değerler 1, 2 veya null değerlerdir. Değer null ise, bu, v 1.0 uç noktasına karşılık gelen varsayılan olarak 1 ' dir. <br/><br/>@No__t-0 `AzureADandPersonalMicrosoftAccount` ise, değer `2` olmalıdır  | `2` |
| `addIns` | Koleksiyon | Bir tüketen hizmetin belirli bağlamlarda uygulama çağırmak için kullanabileceği özel davranışı tanımlar. Örneğin, dosya akışlarını işleyebilen uygulamalar, "FileHandler" işlevselliği için AddIns özelliğini ayarlayabilir. Bu, Office 365 gibi hizmetlerin uygulamayı kullanıcının üzerinde çalıştığı bir belge bağlamında çağırmasını sağlar. | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Boole | Geri dönüş uygulama türünü belirtir. Azure AD, varsayılan olarak, bir uygulama türünü replyUrlsWithType öğesinden algılar. Azure AD 'nin istemci uygulama türünü belirleyememesinin bazı senaryoları vardır (örneğin, bir URL yeniden yönlendirmesi olmadan HTTP isteğinin gerçekleştiği [Ropc](https://tools.ietf.org/html/rfc6749#section-4.3) akışı). Bu durumlarda Azure AD, uygulama türünü bu özelliğin değerine göre yorumlayacak. Bu değer true olarak ayarlanırsa, geri dönüş uygulama türü, bir mobil cihazda çalışan yüklü uygulama gibi ortak istemci olarak ayarlanır. Varsayılan değer false 'dur. Bu, geri dönüş uygulama türünün Web uygulaması gibi gizli bir istemci olduğu anlamına gelir. | `false` |
| `availableToOtherTenants` | Boole | uygulama diğer kiracılar ile paylaşılmışsa doğru; Aksi takdirde, false. <br><br> _Note: Bu yalnızca Uygulama kayıtları (eski) deneyimde mevcuttur. [Uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyiminde `signInAudience` ile değiştirilmiştir._ | |
| `appId` | Dize | Azure AD tarafından bir uygulamaya atanan uygulama için benzersiz tanımlayıcıyı belirtir. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Koleksiyon | Bir uygulamanın bildirebilen rollerin koleksiyonunu belirtir. Bu roller kullanıcılara, gruplara veya hizmet sorumlularına atanabilir. Daha fazla örnek ve bilgi için bkz. [uygulamanıza uygulama rolleri ekleme ve bunları belirtece alma](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | Dize | Uygulamanın görünen adı. <br><br> _Note: Bu yalnızca Uygulama kayıtları (eski) deneyimde mevcuttur. [Uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyiminde `name` ile değiştirilmiştir._ | `"MyRegisteredApp"` |
| `errorUrl` | Dize | Desteklenen. | |
| `groupMembershipClaims` | Dize | Uygulamanın beklediği bir kullanıcı veya OAuth 2,0 erişim belirtecinde verilen `groups` talebini yapılandırır. Bu özniteliği ayarlamak için aşağıdaki geçerli dize değerlerinden birini kullanın:<br/><br/>- `"None"`<br/>-  @ no__t-1 (güvenlik grupları ve Azure AD rolleri için)<br/>-  @ no__t-1 (Bu, oturum açan kullanıcının üyesi olduğu tüm güvenlik gruplarını, dağıtım gruplarını ve Azure AD dizin rollerini alır. | `"SecurityGroup"` |
| `homepage` | Dize | Uygulamanın giriş sayfasının URL 'SI. <br><br> _Note: Bu yalnızca Uygulama kayıtları (eski) deneyimde mevcuttur. [Uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyiminde `signInUrl` ile değiştirilmiştir._ | `"https://MyRegisteredApp"` |
| `objectId` | Dize | Dizindeki uygulamanın benzersiz tanımlayıcısı. <br><br> _Note: Bu yalnızca Uygulama kayıtları (eski) deneyimde mevcuttur. [Uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyiminde `id` ile değiştirilmiştir._ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | Dize | Bu belirli uygulama için güvenlik belirteci hizmeti tarafından belirteçte döndürülen isteğe bağlı talepler.<br>Şu anda, hem kişisel hesapları hem de Azure AD 'yi (uygulama kayıt portalı üzerinden kaydedilir) destekleyen uygulamalar isteğe bağlı talepler kullanamaz. Ancak, yalnızca Azure AD için kaydedilmiş uygulamalar, v 2.0 uç noktasını kullanarak, bildirimde istedikleri isteğe bağlı talepleri alabilir. Daha fazla bilgi için bkz. [isteğe bağlı talepler](active-directory-optional-claims.md). | `null` |
| `id` | Dize | Dizindeki uygulamanın benzersiz tanımlayıcısı. Bu KIMLIK, herhangi bir protokol işleminde uygulamayı tanımlamak için kullanılan tanımlayıcı değildir. Dizin sorgularında nesnesine başvurmak için kullanılır. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | Dize dizisi | Bir Web uygulamasını Azure AD kiracısı içinde benzersiz bir şekilde tanımlayan Kullanıcı tanımlı URI 'ler veya uygulama çok kiracılı ise doğrulanmış bir özel etki alanı içinde. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | Dize | Uygulamanın hizmet koşulları ve gizlilik bildirimi bağlantılarını belirtir. Hizmet koşulları ve gizlilik bildirimi, kullanıcılar tarafından Kullanıcı onay deneyimi aracılığıyla ortaya çıkmış. Daha fazla bilgi için bkz. [nasıl yapılır: kayıtlı Azure AD uygulamaları için hizmet koşulları ve gizlilik bildirimi ekleme](howto-add-terms-of-service-privacy-statement.md). | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Koleksiyon | Uygulama tarafından atanan kimlik bilgileri, dize tabanlı paylaşılan gizlilikler ve X. 509.440 sertifikalarına yönelik başvuruları tutar. Bu kimlik bilgileri, erişim belirteçleri istenirken (uygulama kaynak yerine bir istemci görevi gören zaman) kullanılır. | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | Dize dizisi | İki bölümden oluşan bir çözümünüz varsa, bir istemci uygulaması ve özel bir Web API uygulaması varsa, bu izni paketleme için kullanılır. İstemci uygulamasının AppID 'sini bu değere girerseniz, kullanıcının istemci uygulamasına yalnızca bir kez onay girmesi gerekir. Azure AD, istemcinin Web API 'sine örtülü olarak katılmadığını ve aynı anda hem istemci hem de Web API 'SI için hizmet sorumlularını otomatik olarak sağlamasını bilecektir. Hem istemci hem de Web API uygulaması aynı kiracıda kayıtlı olmalıdır. | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | Dize | Portalda karşıya yüklenen logoya yönelik CDN URL 'sine işaret eden salt okuma değeri. | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | Dize | Uygulamanın oturumunu kapatmak için kullanılacak URL. | `"https://MyRegisteredAppLogout"` |
| `name` | Dize | Uygulamanın görünen adı. | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | Boole | Bu Web uygulamasının OAuth 2.0 örtük akış erişim belirteçleri isteyip isteyemeyeceğini belirtir. Varsayılan değer false 'dur. Bu bayrak, JavaScript tek sayfalı uygulamalar gibi tarayıcı tabanlı uygulamalar için kullanılır. Daha fazla bilgi edinmek için içindekiler tablosuna `OAuth 2.0 implicit grant flow` girin ve dolaylı akış hakkındaki konuları görüntüleyin. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Boole | Bu Web uygulamasının OAuth 2.0 örtük akış KIMLIĞI belirteçleri isteyip isteyemeyeceğini belirtir. Varsayılan değer false 'dur. Bu bayrak, JavaScript tek sayfalı uygulamalar gibi tarayıcı tabanlı uygulamalar için kullanılır. | `false` |
| `oauth2Permissions` | Koleksiyon | Web API (kaynak) uygulamasının istemci uygulamalarına sunduğu OAuth 2,0 izin kapsamlarının koleksiyonunu belirtir. Bu izin kapsamları, izin sırasında istemci uygulamalarına verilebilir. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | Boole | OAuth 2,0 belirteç isteklerinin bir parçası olarak, Azure AD 'nin istekleri almak yerine POST isteklerine izin verip vermeyeceğini belirtir. Varsayılan değer, yalnızca GET isteklerinin izin verileceğini belirten false şeklindedir. | `false` |
| `parentalControlSettings` | Dize | `countriesBlockedForMinors`, uygulamanın minors için engellediği ülkeleri belirtir.<br>`legalAgeGroupRule`, uygulamanın kullanıcıları için geçerli olan geçerli yaş grubu kuralını belirtir. @No__t-0, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` veya `BlockMinors` ' e ayarlanabilir.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | Koleksiyon | @No__t-0 özelliğinin açıklamasına bakın. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | Koleksiyon | Kapalı onay için uygulamaları ve istenen izinleri listeler. Uygulamaya onay sağlanması için bir yönetici gerekir. Ön kimlik onayı Kullanıcı tarafından istenen izinlere izin vermesini gerektirmez. Ön kimlik onayı ' nda listelenen izinler için Kullanıcı onayı gerekmez. Bununla birlikte, ön ek Uthorizedapplications içinde listelenmeyen ek istenen izinler için Kullanıcı onayı gerekir. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | Boole | Bu uygulamanın ortak bir istemci olup olmadığını belirtir (örneğin, bir mobil cihazda çalışan yüklü bir uygulama gibi). <br><br> _Note: Bu yalnızca Uygulama kayıtları (eski) deneyimde mevcuttur. [Uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyiminde `allowPublicClient` ile değiştirilmiştir._ | |
| `publisherDomain` | Dize | Uygulama için doğrulanmış yayımcı etki alanı. Salt okunurdur. | https://www.contoso.com |
| `replyUrls` | dize dizisi | Bu çoklu değer özelliği, Azure AD 'nin belirteçleri döndürürken hedef olarak kabul edeceği kayıtlı redirect_uri değerlerinin listesini tutar. <br><br> _Note: Bu yalnızca Uygulama kayıtları (eski) deneyimde mevcuttur. [Uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyiminde `replyUrlsWithType` ile değiştirilmiştir._ | |
| `replyUrlsWithType` | Koleksiyon | Bu çoklu değer özelliği, Azure AD 'nin belirteçleri döndürürken hedef olarak kabul edeceği kayıtlı redirect_uri değerlerinin listesini tutar. Her URI değeri, ilişkili bir uygulama türü değeri içermelidir. Desteklenen tür değerleri şunlardır: <ul><li>`Web`</li><li>`InstalledClient`</li></ul><br> [Replyurl kısıtlamaları ve sınırlamaları](https://docs.microsoft.com/azure/active-directory/develop/reply-url)hakkında daha fazla bilgi edinin. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Koleksiyon | Dinamik onay ile, `requiredResourceAccess` ' ı, statik onay kullanan kullanıcılar için yönetici onay deneyimini ve Kullanıcı onay deneyimini sürücü olarak yürütür. Ancak, bu, genel durum için Kullanıcı onay deneyimini içermez.<br>`resourceAppId`, uygulamanın erişmesi gereken kaynak için benzersiz tanıtıcıdır. Bu değer, hedef kaynak uygulamasında belirtilen uygulama kimliğine eşit olmalıdır.<br>`resourceAccess`, belirtilen kaynaktan uygulamanın gerektirdiği OAuth 2.0 izin kapsamlarını ve uygulama rollerini listeleyen bir dizidir. Belirtilen kaynakların `id` ve `type` değerlerini içerir. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | Dize | Uygulamanın SAML meta verilerinin URL 'SI. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | Dize | Uygulamanın giriş sayfasına yönelik URL 'YI belirtir. | `https://MyRegisteredApp` |
| `signInAudience` | Dize | Geçerli uygulama için hangi Microsoft hesaplarının desteklendiğini belirtir. Desteklenen değerler şunlardır:<ul><li>**Azureadmyorg** -Kuruluşumun Azure AD kiracısında bir Microsoft iş veya okul hesabı olan kullanıcılar (yani tek kiracı)</li><li>**Azureadmultipleorgs** -herhangi bir kuruluşun Azure AD kiracısında Microsoft iş veya okul hesabı olan kullanıcılar (ör. çok kiracılı)</li> <li>**Azureadandpersonmicrosoftaccount** -kişisel Microsoft hesabı olan kullanıcılar veya herhangi bir kuruluşun Azure AD kiracısında bir iş veya okul hesabı</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | Dize dizisi | Uygulamayı kategorilere ayırmak ve tanımlamak için kullanılabilen özel dizeler. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>Genel sorunlar

### <a name="manifest-limits"></a>Bildirim limitleri

Uygulama bildiriminde koleksiyonlar olarak adlandırılan birden çok öznitelik vardır; Örneğin, approles, keycredentials, knownClientApplications, ıdentifieruris, rediretUris, requiredResourceAccess ve oauth2Permissions. Herhangi bir uygulama için tam uygulama bildiriminde, Birleşik tüm koleksiyonlardaki toplam giriş sayısı 1200 ' de kadık. Uygulama bildiriminde zaten 100 yeniden yönlendirme URI 'si varsa, bu durumda yalnızca, bildirimi oluşturan diğer tüm koleksiyonlar genelinde kullanılacak 1100 kalan girişler vardır.

> [!NOTE]
> Uygulama bildiriminde 1200 'den fazla girdi eklemeye çalışırsanız, **"uygulama xxxxxx güncelleştirilemedi" hatasını görebilirsiniz. Hata ayrıntıları: bildirimin boyutu sınırı aştı. Lütfen değer sayısını azaltın ve isteğinizi yeniden deneyin. "**

### <a name="unsupported-attributes"></a>Desteklenmeyen öznitelikler

Uygulama bildirimi, Azure AD 'de temel alınan uygulama modelinin şemasını temsil eder. Temel alınan şema geliştikçe, bildirim Düzenleyicisi yeni şemayı zaman zaman yansıtacak şekilde güncelleştirilecektir. Sonuç olarak, uygulama bildiriminde yeni öznitelikler olduğunu fark edebilirsiniz. Nadir durumlarda, var olan özniteliklerde bir sözdizimsel veya anlamsal değişiklik fark edebilir veya daha önce artık desteklenmeyen bir öznitelik bulabilirsiniz. Örneğin, Uygulama kayıtları (eski) deneyiminden farklı bir adla bilinen [uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) yeni öznitelikleri görürsünüz.


| Uygulama kayıtları (eski)| Uygulama kayıtları           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Bu özniteliklerin açıklamaları için [bildirim başvurusu](#manifest-reference) bölümüne bakın.

Daha önce indirilen bir bildirimi karşıya yüklemeye çalıştığınızda, aşağıdaki hatalardan birini görebilirsiniz. Bu durum, bildirim Düzenleyicisi 'nin artık bir şemanın daha yeni bir sürümünü desteklediğinden, bu, karşıya yüklemeye çalıştığınız bir ile eşleşmemesi nedeniyle olasıdır.

- "**Xxxxxx uygulamasının güncelleştirilmesi başarısız oldu. Hata ayrıntısı: geçersiz nesne tanımlayıcısı ' undefined '. [].** "
- "**Xxxxxx uygulamasının güncelleştirilmesi başarısız oldu. Hata ayrıntısı: belirtilen bir veya daha fazla özellik değeri geçersiz. [].** "
- "**Xxxxxx uygulamasının güncelleştirilmesi başarısız oldu. Hata ayrıntısı: güncelleştirme için bu API sürümünde Availabletootherkiracılar ayarlanamaz. [].** "
- "**Xxxxxx uygulamasının güncelleştirilmesi başarısız oldu. Hata ayrıntısı: Bu uygulama için ' replyUrls ' özelliğine yapılan güncelleştirmelere izin verilmiyor. Bunun yerine ' replyUrlsWithType ' özelliğini kullanın. [].** "
- "**Xxxxxx uygulamasının güncelleştirilmesi başarısız oldu. Hata ayrıntısı: tür adı olmayan bir değer bulundu ve beklenen tür yok. Model belirtildiğinde, yükteki her bir değerin, açıkça çağıran tarafından ya da üst değerden dolaylı olarak belirtilen bir türde olması gerekir. []** "

Bu hatalardan birini gördüğünüzde, şunları öneririz:

1. Daha önce indirilen bir bildirimi karşıya yüklemek yerine, öznitelikleri bildirim düzenleyicisinde tek tek düzenleyin. İlgilendiğiniz öznitelikleri başarıyla düzenleyebilmeniz için, eski ve yeni özniteliklerin sözdizimini ve semantiğini anlamak için [bildirim başvuru](#manifest-reference) tablosunu kullanın. 
1. İş akışınız, daha sonra kullanmak üzere bildirimleri kaynak deponuza kaydetmenizi gerektiriyorsa, kayıtlı bildirimleri deponuzda **uygulama kayıtları** deneyiminde gördüğünüz bir şekilde yeniden temellendirmenizi öneririz.

## <a name="next-steps"></a>Sonraki adımlar

* Bir uygulamanın uygulaması ve hizmet sorumlusu nesneleri arasındaki ilişki hakkında daha fazla bilgi için bkz. [Azure AD 'de uygulama ve hizmet sorumlusu nesneleri](app-objects-and-service-principals.md).
* Bazı temel Microsoft kimlik platformu geliştirici kavramlarının tanımları için [Microsoft Identity Platform geliştirici sözlüğü](developer-glossary.md) ' ne bakın.

İçeriğimizi iyileştirmenize ve şekillendirmeye yardımcı olacak geri bildirimler sağlamak için aşağıdaki açıklamalar bölümünü kullanın.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
