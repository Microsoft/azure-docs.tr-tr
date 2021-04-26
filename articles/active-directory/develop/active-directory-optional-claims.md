---
title: Azure AD uygulamalarına isteğe bağlı talepler sağlama
titleSuffix: Microsoft identity platform
description: Microsoft Identity platform tarafından yayınlanan SAML 2,0 ve JSON Web belirteçleri (JWT) belirteçlerine özel veya ek talepler ekleme.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 1/06/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 7c0394e765923c027cc15a6278ee451fb13ed1b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104289"
---
# <a name="how-to-provide-optional-claims-to-your-app"></a>Nasıl yapılır: uygulamanıza isteğe bağlı talepler sağlama

Uygulama geliştiricileri, kendi uygulamalarına hangi talepler gönderilmesini istediğinizi belirlemek için Azure AD uygulamalarında isteğe bağlı talepler kullanabilir.

İsteğe bağlı talepleri şu şekilde kullanabilirsiniz:

- Uygulamanızın belirteçlerine dahil etmek için ek talepler ' i seçin.
- Microsoft Identity platformunun belirteçlerde döndürdüğü belirli taleplerin davranışını değiştirin.
- Uygulamanız için özel talepler ekleyin ve erişin.

Standart talepler listesi için bkz. [erişim belirteci](access-tokens.md) ve [id_token](id-tokens.md) talep belgeleri.

İsteğe bağlı talepler hem v 1.0, v 2.0 biçim belirteçlerinde hem de SAML belirteçlerinde desteklenirken, v 1.0 'dan v 2.0'a geçiş yaparken bunların büyük bir kısmını sağlarlar. [Microsoft Identity platformunun](./v2-overview.md) amaçlarından biri, istemciler tarafından en iyi performansı sağlamak için daha küçük belirteç boyutlarıdır. Sonuç olarak, daha önce erişim ve KIMLIK belirteçlerine dahil edilen birkaç talep artık v 2.0 belirteçlerinde mevcut değildir ve özellikle de uygulama başına temelinde sorulmalıdır.

**Tablo 1: uygulanabilirlik**

| Hesap Türü               | v 1.0 belirteçleri | v 2.0 belirteçleri |
|----------------------------|-------------|-------------|
| Kişisel Microsoft hesabı | Yok         | Desteklenir   |
| Azure AD hesabı           | Desteklenir   | Desteklenir   |

## <a name="v10-and-v20-optional-claims-set"></a>v 1.0 ve v 2.0 isteğe bağlı talepler kümesi

Varsayılan olarak, uygulamaların kullanması için kullanılabilen isteğe bağlı talepler kümesi aşağıda listelenmiştir. Uygulamanıza yönelik özel isteğe bağlı talepler eklemek için aşağıdaki [Dizin uzantıları](#configuring-directory-extension-optional-claims)bölümüne bakın. **Erişim belirtecine** talepler eklenirken, talepler uygulama *tarafından* istenen talepler değil, uygulama (bir Web API) *için* istenen erişim belirteçlerine uygulanır. İstemci, API 'nize nasıl eriştiğine bakılmaksızın, API 'niz üzerinde kimlik doğrulaması yapmak için kullanılan erişim belirtecinde doğru veriler vardır.

> [!NOTE]
>Bu taleplerin çoğu, v 1.0 ve v 2.0 belirteçleri için JWTs 'ye dahil edilebilir, ancak belirteç türü sütununda belirtilenler dışında SAML belirteçleri olamaz. Tüketici hesapları, bu taleplerin bir alt kümesini destekler ve "Kullanıcı türü" sütununda işaretlenir.  Listelenen taleplerin birçoğu tüketici kullanıcılarına uygulanmaz (hiçbir kiracı yoktur, bu nedenle bir `tenant_ctry` değer yoktur).

**Tablo 2: v 1.0 ve v 2.0 isteğe bağlı talep kümesi**

| Ad                       |  Açıklama   | Belirteç türü | Kullanıcı türü | Notlar  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Kullanıcının en son kimlik doğrulamasının süresi. Bkz. OpenID Connect spec.| JWT        |           |  |
| `tenant_region_scope`      | Kaynak kiracının bölgesi | JWT        |           | |
| `sid`                      | Oturum başına Kullanıcı oturumu kapatma için kullanılan oturum KIMLIĞI. | JWT        |  Kişisel ve Azure AD hesapları.   |         |
| `verified_primary_email`   | Kullanıcının açık olan Yauthcumtativee-postalarından kaynaklandırılmış      | JWT        |           |         |
| `verified_secondary_email` | Kullanıcının SecondaryAuthoritativeEmail kaynağı   | JWT        |           |        |
| `vnet`                     | VNET tanımlayıcı bilgileri. | JWT        |           |      |
| `fwd`                      | IP adresi.| JWT    |   | İstek sunan istemcinin özgün IPv4 adresini ekler (sanal ağ içinde) |
| `ctry`                     | Kullanıcının ülkesi/bölgesi | JWT |  | Azure AD, varsa `ctry` isteğe bağlı talebi döndürür ve alanın DEĞERI fr, JP, SZ gibi standart bir iki harfli ülke/bölge kodudur. |
| `tenant_ctry`              | Kaynak kiracının ülkesi | JWT | | Bir `ctry` yönetici tarafından kiracı düzeyinde ayarlananların dışında.  Ayrıca, standart iki harfli bir değer olmalıdır. |
| `xms_pdl`             | Tercih edilen veri konumu   | JWT | | Çoklu coğrafi kiracılar için, tercih edilen veri konumu, kullanıcının bulunduğu coğrafi bölgeyi gösteren üç harfli koddur. Daha fazla bilgi için, [tercih edilen veri konumu hakkında Azure AD Connect belgelerine](../hybrid/how-to-connect-sync-feature-preferreddatalocation.md)bakın.<br/>Örneğin: `APC` Asya Pasifik için. |
| `xms_pl`                   | Kullanıcı tarafından tercih edilen dil  | JWT ||Ayarlanmışsa kullanıcının tercih ettiği dil. Konuk erişim senaryolarında, ana kiracılarından kaynaklıdır. Biçimlendirilen LL-CC ("en-US"). |
| `xms_tpl`                  | Kiracının tercih ettiği dil| JWT | | Ayarlanırsa, kaynak kiracının tercih edilen dili. Biçimlendirildim ("en"). |
| `ztdid`                    | Sıfır dokunma dağıtım KIMLIĞI | JWT | | [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) için kullanılan cihaz kimliği |
| `email`                    | Kullanıcının bir tane varsa, bu kullanıcı için adreslenebilir e-posta.  | JWT, SAML | MSA, Azure AD | Bu değer, Kullanıcı Kiracıdaki bir konuk ise varsayılan olarak dahil edilir.  Yönetilen kullanıcılar (kiracının içindeki kullanıcılar) için, bu isteğe bağlı talep veya yalnızca v 2.0 üzerinde, OpenID kapsamı ile istenmesi gerekir.  Yönetilen kullanıcılar için, e-posta adresinin [Office Yönetim Portalı](https://portal.office.com/adminportal/home#/users)'nda ayarlanması gerekir.|
| `acct`                | Kiracıdaki Kullanıcı hesabı durumu | JWT, SAML | | Kullanıcı kiracının üyesiyse, değeri olur `0` . Bunlar bir konuğlarsa değer olur `1` . |
| `groups`| Grup talepleri için isteğe bağlı biçimlendirme |JWT, SAML| |[Uygulama bildiriminde](reference-app-manifest.md), aynı zamanda ayarlanması gereken Groupmembershipclaim ayarıyla birlikte kullanılır. Ayrıntılar için aşağıdaki [Grup taleplerini](#configuring-groups-optional-claims) inceleyin. Grup talepleri hakkında daha fazla bilgi için bkz. [Grup taleplerini yapılandırma](../hybrid/how-to-connect-fed-group-claims.md)
| `upn`                      | UserPrincipalName | JWT, SAML  |           | Kullanıcı için username_hint parametresiyle kullanılabilecek bir tanımlayıcı.  Kullanıcı için dayanıklı bir tanımlayıcı değildir ve Kullanıcı bilgilerini (örneğin, bir veritabanı anahtarı olarak) benzersiz olarak kimlik için kullanılmamalıdır. Bunun yerine, Kullanıcı nesnesi KIMLIĞINI ( `oid` ) bir veritabanı anahtarı olarak kullanın. [Alternatif bir oturum açma kimliğiyle](../authentication/howto-authentication-use-email-signin.md) oturum açan kullanıcılar, Kullanıcı asıl adı (UPN) gösterilmemelidir. Bunun yerine, kullanıcının oturum açma durumunu görüntülemek için aşağıdaki KIMLIK belirteci taleplerini kullanın: `preferred_username` ya da `unique_name` v1 belirteçleri ve `preferred_username` v2 belirteçleri için. Bu talep otomatik olarak dahil edilse de, Konuk Kullanıcı kasasında davranışını değiştirmek üzere ek özellikler eklemek için isteğe bağlı bir talep olarak belirtebilirsiniz.  |
| `idtyp`                    | Belirteç türü   | JWT erişim belirteçleri | Özel: yalnızca yalnızca uygulama erişim belirteçlerinde |  Değer, `app` belirtecin yalnızca uygulama belirteci olduğu değerdir. Bu, bir API 'nin bir uygulama belirteci veya bir uygulama + kullanıcı belirteci olup olmadığını belirlemesi için en doğru yoldur.|

## <a name="v20-specific-optional-claims-set"></a>v 2.0'a özgü isteğe bağlı talepler kümesi

Bu talepler her zaman v 1.0 Azure AD belirteçlerine dahil edilmiştir, ancak istenmediği takdirde v 2.0 belirteçlerine dahil edilmez. Bu talepler yalnızca JWTs (KIMLIK belirteçleri ve erişim belirteçleri) için geçerlidir.

**Tablo 3: v 2.0-yalnızca isteğe bağlı talepler**

| JWT talebi     | Ad                            | Açıklama                                | Notlar |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP Adresi                      | İstemcinin oturum açtığı IP adresi.   |       |
| `onprem_sid`  | Şirket Içi güvenlik tanımlayıcısı |                                             |       |
| `pwd_exp`     | Parola Son Kullanma Tarihi        | Parolanın süresi dolan tarih/saat. |       |
| `pwd_url`     | Parola URL 'sini Değiştir             | Kullanıcının parolalarını değiştirmek için ziyaret edebildikleri bir URL.   |   |
| `in_corp`     | İç Şirket Ağı        | İstemci şirket ağından oturum açıyorsanız bildirir. Aksi takdirde talep dahil edilmez.   |  MFA 'daki [Güvenilen IP 'lerin](../authentication/howto-mfa-mfasettings.md#trusted-ips) ayarlarını temel alarak.    |
| `family_name` | Soyadı                       | Kullanıcı nesnesinde tanımlandığı şekilde kullanıcının soyadı, soyadı veya aile adını sağlar. <br>"family_name": "Miller" | MSA ve Azure AD 'de desteklenir. Kapsam gerektirir `profile` .   |
| `given_name`  | Ad                      | Kullanıcı nesnesinde ayarlandığı gibi, kullanıcının ilk veya "verilen" adını sağlar.<br>"given_name": "filiz"                   | MSA ve Azure AD 'de desteklenir.  Kapsam gerektirir `profile` . |
| `upn`         | Kullanıcı Asıl Adı | Kullanıcı için username_hint parametresiyle kullanılabilecek bir tanımlayıcı.  Kullanıcı için dayanıklı bir tanımlayıcı değildir ve Kullanıcı bilgilerini (örneğin, bir veritabanı anahtarı olarak) benzersiz olarak kimlik için kullanılmamalıdır. Bunun yerine, Kullanıcı nesnesi KIMLIĞINI ( `oid` ) bir veritabanı anahtarı olarak kullanın. [Alternatif bir oturum açma kimliğiyle](../authentication/howto-authentication-use-email-signin.md) oturum açan kullanıcılar, Kullanıcı asıl adı (UPN) gösterilmemelidir. Bunun yerine, `preferred_username` kullanıcının oturum açma durumunu görüntülemek için aşağıdaki talebi kullanın. | Talebin yapılandırması için aşağıdaki [ek özelliklere](#additional-properties-of-optional-claims) bakın. Kapsam gerektirir `profile` .|

## <a name="v10-specific-optional-claims-set"></a>v 1.0 'a özgü isteğe bağlı talepler kümesi

V2 belirteç biçimi geliştirmelerinden bazıları, güvenlik ve güvenilirliği artırmaya yardımcı olduklarından v1 belirteç biçimini kullanan uygulamalar için kullanılabilir. Bu, v2 uç noktasından istenen KIMLIK belirteçleri veya v2 belirteç biçimini kullanan API 'Ler için belirteçlere erişim için geçerli olmayacaktır. Bunlar, SAML belirteçleri değil yalnızca JWTs için geçerlidir. 

**Tablo 4: v 1.0-yalnızca isteğe bağlı talepler**


| JWT talebi     | Ad                            | Açıklama | Notlar |
|---------------|---------------------------------|-------------|-------|
|`aud`          | Hedef kitle | Her zaman JWTs 'de bulunur, ancak v1 erişim belirteçlerinde, her bir AppID URI 'SI, sonunda veya sonunda eğik çizgi ve kaynak istemci KIMLIĞI olmak üzere çeşitli yollarla dağıtılabilir. Bu rastgele seçim, belirteç doğrulaması gerçekleştirilirken kod için zor olabilir.  [Bu talep için ek özellikleri](#additional-properties-of-optional-claims) , her zaman v1 erişim belirteçlerinde KAYNAĞıN istemci kimliğine ayarlandığından emin olmak için kullanın. | yalnızca v1 JWT erişim belirteçleri|
|`preferred_username` | Tercih edilen Kullanıcı adı        | V1 belirteçleri içinde tercih edilen Kullanıcı adı talebini sağlar. Bu, uygulamaların Kullanıcı adı ipuçları sağlamasını ve kendi belirteç türlerine bakılmaksızın okunabilir görünen adları göstermesini kolaylaştırır.  Bunun yerine isteğe bağlı talebi kullanmanız önerilir, örn. `upn` veya `unique_name` . | V1 KIMLIK belirteçleri ve erişim belirteçleri |

### <a name="additional-properties-of-optional-claims"></a>İsteğe bağlı taleplerin ek özellikleri

İsteğe bağlı talepler, talebin döndürdüğü yöntemi değiştirecek şekilde yapılandırılabilir. Bu ek özellikler çoğunlukla, farklı veri beklentilerine sahip şirket içi uygulamaların geçişine yardımcı olmak için kullanılır. Örneğin, `include_externally_authenticated_upn_without_hash` UPN 'de karma işaretlerini () işleyemeyen istemcilerde yardımcı olur `#` .

**Tablo 4: isteğe bağlı talepler yapılandırma değerleri**

| Özellik adı  | Ek özellik adı | Description |
|----------------|--------------------------|-------------|
| `upn`          |                          | Hem SAML hem de JWT yanıtları için ve v 1.0 ve v 2.0 belirteçleri için kullanılabilir. |
|                | `include_externally_authenticated_upn`  | , Kaynak kiracısında depolanan Konuk UPN 'sini içerir. Örneğin, `foo_hometenant.com#EXT#@resourcetenant.com` |
|                | `include_externally_authenticated_upn_without_hash` | Yukarıdaki gibi, karma işaretlerinin ( `#` ) alt çizgi () ile değiştirilmeleri dışında, `_` Örneğin `foo_hometenant.com_EXT_@resourcetenant.com`|
| `aud`          |                          | V1 erişim belirteçlerinde, bu, talebin biçimini değiştirmek için kullanılır `aud` .  Bu, `aud` her zaman ISTEMCI kimliği olan v2 belirteçleri veya sürümün kimlik belirteçleri üzerinde hiçbir etkiye sahip değildir. API 'nizin daha kolay bir şekilde izleyici doğrulaması gerçekleştirmesini sağlamak için bu yapılandırmayı kullanın. Erişim belirtecini etkileyen tüm isteğe bağlı talepler gibi, kaynak erişim belirtecine ait olduğundan istekteki kaynağın bu isteğe bağlı talebi ayarlaması gerekir.|
|                | `use_guid`               | Kaynak (API) öğesinin istemci KIMLIĞINI, `aud` her zaman çalışma zamanına bağımlı olması yerine, her zaman talep olarak GUID biçiminde yayar. Örneğin, bir kaynak bu bayrağı ayarlarsa ve istemci KIMLIĞI ise `bb0a297b-6a42-4a55-ac40-09a501456577` , bu kaynak için erişim belirteci isteyen herhangi bir uygulama, şu ile bir erişim belirteci alır `aud` : `bb0a297b-6a42-4a55-ac40-09a501456577` . </br></br> Bu talep kümesi olmadan, bir API, bu `aud` `api://MyApi.com` `api://MyApi.com/` `api://myapi.com/AdditionalRegisteredField` API için bir uygulama kimliği URI 'si olarak,, ya da başka bir değer kümesi ve kaynağın istemci kimliği için bir talep olan belirteçleri alabilir. |

#### <a name="additional-properties-example"></a>Ek özellikler örneği

```json
"optionalClaims": {
    "idToken": [
        {
            "name": "upn",
            "essential": false,
            "additionalProperties": [
                "include_externally_authenticated_upn"
            ]
        }
    ]
}
```

Bu Optionalclaim nesnesi, istemciye döndürülen KIMLIK belirtecinin `upn` ek ana kiracı ve kaynak kiracı bilgilerini içeren bir talep içermesini sağlar. `upn`Talep yalnızca, Kullanıcı Kiracıdaki bir konuk ise (kimlik doğrulaması için farklı bır ıDP kullanır) belirteçte değiştirilir.

## <a name="configuring-optional-claims"></a>İsteğe bağlı talepler yapılandırılıyor

> [!IMPORTANT]
> Erişim belirteçleri, **her zaman** istemcinin değil kaynağın bildirimi kullanılarak oluşturulur.  Bu nedenle, `...scope=https://graph.microsoft.com/user.read...` kaynak MICROSOFT Graph API 'sidir.  Bu nedenle, erişim belirteci, istemcinin bildirimi değil Microsoft Graph API bildirimi kullanılarak oluşturulur.  Uygulamanızın bildirimini değiştirmek, Microsoft Graph API 'sinin farklı görünmesini hiçbir şekilde neden olmaz.  Değişikliklerinizin geçerli olduğunu doğrulamak için `accessToken` , başka bir uygulama değil, uygulamanız için bir belirteç isteyin.

Kullanıcı arabirimi veya uygulama bildirimi aracılığıyla uygulamanız için isteğe bağlı talepler yapılandırabilirsiniz.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> gidin. 
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet**'in altında **Uygulama kayıtları** nı seçin.
1. Listede için isteğe bağlı talepler yapılandırmak istediğiniz uygulamayı seçin.

**Kullanıcı arabirimi aracılığıyla isteğe bağlı talepler yapılandırma:**

[![Kullanıcı arabiriminde isteğe bağlı talepler yapılandırma](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. **Yönet** altında **belirteç yapılandırması**' nı seçin.
   - UI seçeneği **belirteci yapılandırma** dikey penceresi, uygulama bildirimi değiştirilerek yapılandırılabilen bir Azure AD B2C kiracısında kayıtlı uygulamalar için kullanılamaz. Daha fazla bilgi için bkz.  [Azure Active Directory B2C talepler ekleme ve Kullanıcı girişini özelleştirme, özel ilkeleri kullanma](../../active-directory-b2c/configure-user-input.md)  

1. **İsteğe bağlı talep Ekle**' yi seçin.
1. Yapılandırmak istediğiniz belirteç türünü seçin.
1. Eklenecek isteğe bağlı talepler ' i seçin.
1. **Add (Ekle)** seçeneğini belirleyin.


**Uygulama bildirimi aracılığıyla isteğe bağlı talepler Yapılandırılıyor:**

[![Uygulama bildirimini kullanarak isteğe bağlı taleplerin nasıl yapılandırılacağını gösterir](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. **Yönet** altında **bildirim**' ı seçin. Web tabanlı bir bildirim Düzenleyicisi açılarak, bildirimi düzenlemenize olanak tanır. İsteğe bağlı olarak **İndir** seçeneğini belirleyip bildirimi yerel ortamda düzenledikten sonra **Yükle** seçeneğiyle uygulamanıza yeniden uygulayabilirsiniz. Uygulama bildirimi hakkında daha fazla bilgi için bkz. [Azure AD uygulama bildirimini anlama makalesi](reference-app-manifest.md).

    Aşağıdaki uygulama bildirimi girişi, KIMLIK, erişim ve SAML belirteçlerine auth_time, IPADDR ve UPN isteğe bağlı taleplerini ekler.

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "accessToken": [
            {
                "name": "ipaddr",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "upn",
                "essential": false
            },
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": false
            }
        ]
    }
    ```

2. İşiniz bittiğinde **Kaydet**' i seçin. Artık belirtilen isteğe bağlı talepler, uygulamanızın belirteçlerine dahil edilecek.


### <a name="optionalclaims-type"></a>Optionalclaim türü

Bir uygulama tarafından istenen isteğe bağlı talepleri bildirir. Bir uygulama, güvenlik belirteci hizmetinden alabileceği üç tür belirteç türünden (KIMLIK belirteci, erişim belirteci, SAML 2 belirteci) döndürülecek isteğe bağlı talepler yapılandırabilir. Uygulama, her bir belirteç türünde döndürülecek farklı bir isteğe bağlı talepler kümesi yapılandırabilir. Uygulama varlığının Optionalclaim özelliği bir Optionalclaim nesnesidir.

**Tablo 5: Optionalclaim türü özellikleri**

| Ad          | Tür                       | Description                                           |
|---------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Koleksiyon (OptionalClaim) | JWT KIMLIK belirtecinde döndürülen isteğe bağlı talepler.     |
| `accessToken` | Koleksiyon (OptionalClaim) | JWT erişim belirtecinde döndürülen isteğe bağlı talepler. |
| `saml2Token`  | Koleksiyon (OptionalClaim) | SAML belirtecinde döndürülen isteğe bağlı talepler.       |

### <a name="optionalclaim-type"></a>OptionalClaim türü

Bir uygulama veya hizmet sorumlusu ile ilişkili isteğe bağlı bir talep içerir. [Optionalclaim](/graph/api/resources/optionalclaims) türünün ıdtoken, accesstoken ve saml2Token özellikleri bir optionalclaim koleksiyonudur.
Belirli bir talep tarafından destekleniyorsa, Additionalclaim 'nin davranışını AdditionalProperties alanını kullanarak da değiştirebilirsiniz.

**Tablo 6: OptionalClaim türü özellikleri**

| Ad                   | Tür                    | Description                                                                                                                                                                                                                                                                                                   |
|------------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | İsteğe bağlı talebin adı.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | Talebin kaynağı (Dizin nesnesi). Uzantı özelliklerinden önceden tanımlı talepler ve Kullanıcı tanımlı talepler vardır. Kaynak değeri null ise, talep önceden tanımlanmış isteğe bağlı bir talep olur. Kaynak değeri kullanıcı ise, ad özelliğindeki değer kullanıcı nesnesinden uzantı özelliğidir. |
| `essential`            | Edm.Boolean             | Değer true ise, istemci tarafından belirtilen talep, son kullanıcı tarafından istenen belirli bir görev için sorunsuz bir yetkilendirme deneyimi sağlamak için gereklidir. Varsayılan değer false'tur.                                                                                                                 |
| `additionalProperties` | Koleksiyon (EDM. String) | Talebin ek özellikleri. Bu koleksiyonda bir özellik varsa, ad özelliğinde belirtilen isteğe bağlı talebin davranışını değiştirir.                                                                                                                                                   |

## <a name="configuring-directory-extension-optional-claims"></a>Dizin uzantısı isteğe bağlı taleplerini yapılandırma

Standart isteğe bağlı talepler kümesine ek olarak, belirteçleri uzantıları içerecek şekilde de yapılandırabilirsiniz. Daha fazla bilgi için [Microsoft Graph extensionProperty belgelerine](/graph/api/resources/extensionproperty)bakın.

Şema ve açık uzantılar isteğe bağlı talepler tarafından desteklenmez; yalnızca AAD-Graph Style Dizin uzantıları. Bu özellik, uygulamanızın kullanabileceği ek kullanıcı bilgilerini eklemek için yararlıdır; Örneğin, kullanıcının ayarlamış olduğu ek bir tanımlayıcı veya önemli bir yapılandırma seçeneği. Bir örnek için bu sayfanın en altına bakın.

Dizin şeması uzantıları yalnızca Azure AD özellikli bir özelliktir. Uygulama bildiriminiz bir özel uzantı isterse ve bir MSA kullanıcısı uygulamanızda oturum açarsa, bu uzantılar döndürülmez.

### <a name="directory-extension-formatting"></a>Dizin uzantısı biçimlendirmesi

Uygulama bildirimini kullanarak dizin uzantısı isteğe bağlı taleplerini yapılandırırken, uzantının tam adını kullanın (biçiminde: `extension_<appid>_<attributename>` ). `<appid>`Talep isteyen UYGULAMANıN kimliğiyle eşleşmesi gerekir.

JWT içinde bu talepler şu ad biçimiyle yayınlanacaktır:  `extn.<attributename>` .

SAML belirteçlerinde Bu talepler aşağıdaki URI biçimiyle alınacaktır: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Grupları yapılandırma isteğe bağlı talepler

Bu bölüm, Grup taleplerinde kullanılan grup özniteliklerinin, şirket içi Windows Active Directory eşitlenen özniteliklere varsayılan gruptan değiştirilmesini sağlamak için isteğe bağlı talepler altındaki yapılandırma seçeneklerini anlatmaktadır. Kullanıcı arabirimi veya uygulama bildirimi aracılığıyla uygulamanız için isteğe bağlı talepler yapılandırabilirsiniz.

> [!IMPORTANT]
> Şirket içi özniteliklerdeki grup taleplerine ilişkin önemli uyarılar dahil olmak üzere daha fazla ayrıntı için bkz. [Azure AD ile uygulamalar için grup taleplerini yapılandırma](../hybrid/how-to-connect-fed-group-claims.md).

**Grupları kullanıcı arabirimi aracılığıyla isteğe bağlı talepler yapılandırma:**

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Kimlik doğrulamasından geçtikten sonra, sayfanın sağ üst köşesinden seçerek Azure AD kiracınızı seçin.
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet**'in altında **Uygulama kayıtları** nı seçin.
1. Listede için isteğe bağlı talepler yapılandırmak istediğiniz uygulamayı seçin.
1. **Yönet** altında **belirteç yapılandırması**' nı seçin.
1. **Grup Ekle talebi**' ni seçin.
1. Döndürülecek Grup türlerini (**güvenlik grupları** veya **Dizin rolleri**, **tüm gruplar** ve/veya **uygulamaya atanan gruplar**) seçin. **Uygulama seçeneğine atanan gruplar** yalnızca uygulamaya atanan grupları içerir. **Tüm gruplar** seçeneği **SecurityGroup**, **directoryrole** ve **distributionlist** içerir, ancak **uygulamaya atanan grupları** içermez. 
1. İsteğe bağlı: gruplar talep değerini, şirket içi grup özniteliklerini içerecek şekilde değiştirmek veya talep türünü bir rol olarak değiştirmek için belirli belirteç türü özelliklerini seçin.
1. **Kaydet**’i seçin.

**Grupları uygulama bildirimi aracılığıyla isteğe bağlı talepler yapılandırma:**

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Kimlik doğrulamasından geçtikten sonra, sayfanın sağ üst köşesinden seçerek Azure AD kiracınızı seçin.
1. **Azure Active Directory**'yi bulun ve seçin.
1. Listede için isteğe bağlı talepler yapılandırmak istediğiniz uygulamayı seçin.
1. **Yönet** altında **bildirim**' ı seçin.
1. Bildirim düzenleyicisini kullanarak aşağıdaki girişi ekleyin:

   Geçerli değerler şunlardır:

   - "All" (Bu seçenek SecurityGroup, DirectoryRole ve DistributionList içerir)
   - "SecurityGroup"
   - "DirectoryRole"
   - "ApplicationGroup" (Bu seçenek yalnızca uygulamaya atanan grupları içerir)

   Örnek:

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   Varsayılan olarak grup ObjectID 'Ler, Grup talep değeri ' ne yayılır.  Talep değerini şirket içi grup özniteliklerini içerecek şekilde değiştirmek veya talep türünü rol olarak değiştirmek için, Optionalclaim yapılandırması ' nı aşağıdaki gibi kullanın:

1. Grup adı yapılandırması isteğe bağlı taleplerini ayarlayın.

   Belirteçteki grupların isteğe bağlı talepler bölümünde şirket içi AD grubu özniteliklerini içermesini istiyorsanız, isteğe bağlı talep için hangi belirteç türünün uygulanacağını, istenen isteğe bağlı talebin adını ve istediğiniz ek özellikleri belirtin.  Birden çok belirteç türü listelenebilir:

   - OıDC KIMLIK belirtecinin ıdtoken 'ı
   - OAuth erişim belirteci için accessToken
   - SAML belirteçleri için Saml2Token.

   Saml2Token türü, SAML 1.1 ve SAML 2.0 biçim belirteçleri için geçerlidir.

   Her ilgili belirteç türü için, gruplar talebini, bildirimdeki Optionalclaim bölümünü kullanacak şekilde değiştirin. Optionalclaim şeması aşağıdaki gibidir:

    ```json
    {
        "name": "groups",
        "source": null,
        "essential": false,
        "additionalProperties": []
    }
    ```

   | İsteğe bağlı talepler şeması | Değer |
   |----------|-------------|
   | **ada** | "Gruplar" olmalıdır |
   | **kaynaktaki** | Kullanılmadı. Null değerini atla veya belirt |
   | **dir** | Kullanılmadı. Yoksay veya false belirt |
   | **AdditionalProperties** | Ek özelliklerin listesi.  Geçerli seçenekler şunlardır "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   AdditionalProperties içinde "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" yalnızca biri gereklidir.  Birden fazla varsa, ilki kullanılır ve diğerleri yok sayılır.

   Bazı uygulamalar, rol talebinde Kullanıcı hakkında grup bilgileri gerektirir.  Talep türünü bir grup talebine rol talebine değiştirmek için, ek özelliklere "emit_as_roles" ekleyin.  Grup değerleri rol talebinde yayınlanacaktır.

   "Emit_as_roles" kullanılırsa, kullanıcının atandığı tüm uygulama rolleri rol talebinde görünmez.

**Örnekler:**

1) Grupları DNSEtkiAlanıAdı sAMAccountName biçimindeki OAuth erişim belirteçlerinde grup adları olarak yay

    **Kullanıcı arabirimi yapılandırması:**

    [![İsteğe bağlı talepleri yapılandırma](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)

    **Uygulama bildirim girdisi:**

    ```json
    "optionalClaims": {
        "accessToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "dns_domain_and_sam_account_name"
                ]
            }
        ]
    }
    ```

2) SAML ve OıDC KIMLIK belirteçlerinde rol talebi olarak, netbiosDomain\sAMAccountName biçiminde döndürülecek grup adlarını yay

    **Kullanıcı arabirimi yapılandırması:**

    [![Bildirimde isteğe bağlı talepler](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Uygulama bildirim girdisi:**

    ```json
    "optionalClaims": {
        "saml2Token": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ],
        "idToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ]
    }
    ```

## <a name="optional-claims-example"></a>İsteğe bağlı talepler örneği

Bu bölümde, uygulamanız için isteğe bağlı talepler özelliğini nasıl kullanabileceğinizi görmek için bir senaryoya yol açabilir.
İsteğe bağlı talepler etkinleştirmek ve yapılandırmak için bir uygulamanın kimlik yapılandırmasındaki özellikleri güncelleştirmek üzere kullanılabilecek birden fazla seçenek vardır:

- **Belirteç yapılandırması** Kullanıcı arabirimini kullanabilirsiniz (aşağıdaki örneğe bakın)
- **Bildirimi** kullanabilirsiniz (aşağıdaki örneğe bakın). Bildirime giriş için önce [Azure AD uygulama bildirimi belgesini anlama](./reference-app-manifest.md) makalesini okuyun.
- Uygulamanızı güncelleştirmek için [MICROSOFT Graph API](/graph/use-the-api) kullanan bir uygulama yazmak da mümkündür. Microsoft Graph API başvuru kılavuzundaki [optionalclaim](/graph/api/resources/optionalclaims) türü, isteğe bağlı talepleri yapılandırmanıza yardımcı olabilir.

**Örnek:**

Aşağıdaki örnekte, uygulamanız için tasarlanan erişim, KIMLIK ve SAML belirteçlerine isteğe bağlı talepler eklemek için **belirteç yapılandırması** Kullanıcı arabirimini ve **bildirimini** kullanacaksınız. Uygulamanın alabileceği her bir belirteç türüne farklı isteğe bağlı talepler eklenecektir:

- KIMLIK belirteçleri artık, Federasyon kullanıcıları için UPN 'yi tam biçimde ( `<upn>_<homedomain>#EXT#@<resourcedomain>` ) içerir.
- Bu uygulama için diğer istemcilerin talep aldığı erişim belirteçleri artık auth_time talebi içerecektir.
- SAML belirteçleri artık Sktypeınfo dizin şema uzantısını içerecektir (Bu örnekte, bu uygulamanın uygulama KIMLIĞI ab603c56068041afb2f6832e2a17e237 ' dir). SAML belirteçleri, Skype KIMLIĞINI olarak kullanıma sunacaktır `extension_skypeId` .

**Kullanıcı arabirimi yapılandırması:**

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Kimlik doğrulamasından geçtikten sonra, sayfanın sağ üst köşesinden seçerek Azure AD kiracınızı seçin.

1. **Azure Active Directory**'yi bulun ve seçin.

1. **Yönet**'in altında **Uygulama kayıtları** nı seçin.

1. Listede için isteğe bağlı talepler yapılandırmak istediğiniz uygulamayı bulun ve seçin.

1. **Yönet** altında **belirteç yapılandırması**' nı seçin.

1. **İsteğe bağlı talep Ekle**' yi seçin **, kimlik** belirteci türünü seçin, talepler listesinden **UPN** ' yi seçin ve ardından **Ekle**' yi seçin.

1. **İsteğe bağlı talep Ekle**' yi seçin **, erişim** belirteci türünü seçin, talepler listesinden **auth_time** ' ı seçin ve ardından **Ekle**' yi seçin.

1. Belirteç yapılandırması genel bakış ekranından **UPN**' nin yanındaki kurşun kalem simgesini seçin, **dışarıdan kimliği doğrulanmış** geçiş ' i seçin ve ardından **Kaydet**' i seçin.

1. **İsteğe bağlı talep Ekle**' yi seçin, **SAML** belirteci türünü seçin, talepler listesinden **Extn. sktypeınfo kimliğini** seçin (yalnızca sktypeınfo adlı bir Azure AD Kullanıcı nesnesi oluşturduysanız geçerlidir) ve ardından **Ekle**' yi seçin.

    [![SAML belirteci için isteğe bağlı talepler](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Bildirim yapılandırması:**

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Kimlik doğrulamasından geçtikten sonra, sayfanın sağ üst köşesinden seçerek Azure AD kiracınızı seçin.
1. **Azure Active Directory**'yi bulun ve seçin.
1. Listede için isteğe bağlı talepler yapılandırmak istediğiniz uygulamayı bulun ve seçin.
1. **Yönet** altında, satır içi bildirim düzenleyicisini açmak için **bildirim** ' ı seçin.
1. Bu düzenleyiciyi kullanarak bildirimi doğrudan düzenleyebilirsiniz. Bildirim, [uygulama varlığının](./reference-app-manifest.md)şemasını izler ve bir kez kaydedildikten sonra bildirimi otomatik biçimlendirir. Özelliğe yeni öğeler eklenecektir `OptionalClaims` .

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "upn",
                "essential": false,
                "additionalProperties": [
                    "include_externally_authenticated_upn"
                ]
            }
        ],
        "accessToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": true
            }
        ]
    }
    ```

1. Bildirimi güncelleştirmeyi tamamladığınızda, bildirimi kaydetmek için **Kaydet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD tarafından sunulan standart talepler hakkında daha fazla bilgi edinin.

- [Kimlik belirteçleri](id-tokens.md)
- [Erişim belirteçleri](access-tokens.md)
