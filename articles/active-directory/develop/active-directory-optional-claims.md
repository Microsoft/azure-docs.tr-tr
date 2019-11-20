---
title: Azure AD uygulamanıza isteğe bağlı talepler sağlamayı öğrenin
titleSuffix: Microsoft identity platform
description: SAML 2,0 ve Azure Active Directory tarafından verilen JSON Web belirteçleri (JWT) belirteçlerine özel veya ek talepler eklemeye yönelik bir kılavuz.
documentationcenter: na
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/03/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b74e680979ccbcc94f8a49e993c6d64797ab80b1
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803416"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Nasıl yapılır: Azure AD uygulamanıza isteğe bağlı talepler sağlama

Uygulama geliştiricileri, hangi taleplerin uygulamalarına gönderileceğini belirlemek için Azure AD uygulamalarında isteğe bağlı talepler kullanabilir. 

İsteğe bağlı talepleri şu şekilde kullanabilirsiniz:

- Uygulamanızın belirteçlerine dahil etmek için ek talepler ' i seçin.
- Azure AD 'nin belirteçlerde döndürdüğü belirli taleplerin davranışını değiştirin.
- Uygulamanız için özel talepler ekleyin ve erişin.

Standart talepler listesi için bkz. [erişim belirteci](access-tokens.md) ve [id_token](id-tokens.md) talepleri belgeleri. 

İsteğe bağlı talepler hem v 1.0, v 2.0 biçim belirteçlerinde hem de SAML belirteçlerinde desteklenirken, v 1.0 'dan v 2.0'a geçiş yaparken bunların büyük bir kısmını sağlarlar. [V 2.0 Microsoft Identity platform uç noktasının](active-directory-appmodel-v2-overview.md) amaçlarından biri, istemciler tarafından en iyi performansı elde etmek için daha küçük belirteç boyutlarıdır. Sonuç olarak, daha önce erişim ve KIMLIK belirteçlerine dahil edilen birkaç talep artık v 2.0 belirteçlerinde mevcut değildir ve özellikle de uygulama başına temelinde sorulmalıdır.

**Tablo 1: uygulanabilirlik**

| Hesap türü | v 1.0 belirteçleri | v 2.0 belirteçleri  |
|--------------|---------------|----------------|
| Kişisel Microsoft hesabı  | Yok  | Desteklenen |
| Azure AD hesabı      | Desteklenen | Desteklenen |

## <a name="v10-and-v20-optional-claims-set"></a>v 1.0 ve v 2.0 isteğe bağlı talepler kümesi

Varsayılan olarak, uygulamaların kullanması için kullanılabilen isteğe bağlı talepler kümesi aşağıda listelenmiştir. Uygulamanıza yönelik özel isteğe bağlı talepler eklemek için aşağıdaki [Dizin uzantıları](#configuring-directory-extension-optional-claims)bölümüne bakın. **Erişim belirtecine**talepler eklenirken, uygulama (BIR Web API 'si) *için* istenen erişim belirteçleri uygulamaya *göre* değil, bu işlem için geçerlidir. Bu, istemci API 'nize erişirken, API 'niz üzerinde kimlik doğrulaması yapmak için kullandıkları erişim belirtecinde doğru verilerin mevcut olmasını sağlar.

> [!NOTE]
> Bu taleplerin çoğu, v 1.0 ve v 2.0 belirteçleri için JWTs 'ye dahil edilebilir, ancak belirteç türü sütununda belirtilenler dışında SAML belirteçleri olamaz. Tüketici hesapları, bu taleplerin bir alt kümesini destekler ve "Kullanıcı türü" sütununda işaretlenir.  Listelenen taleplerin birçoğu tüketici kullanıcılarına uygulanmaz (hiçbir kiracıya sahip değildir, bu nedenle `tenant_ctry` hiçbir değere sahip değildir).  

**Tablo 2: v 1.0 ve v 2.0 isteğe bağlı talep kümesi**

| Adı                       |  Açıklama   | Belirteç türü | Kullanıcı türü | Notlar  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Kullanıcının en son kimlik doğrulamasının süresi. Bkz. OpenID Connect spec.| JWT        |           |  |
| `tenant_region_scope`      | Kaynak kiracının bölgesi | JWT        |           | |
| `home_oid`                 | Konuk kullanıcılar için kullanıcının ana kiracısındaki kullanıcının nesne KIMLIĞI.| JWT        |           | |
| `sid`                      | Oturum başına Kullanıcı oturumu kapatma için kullanılan oturum KIMLIĞI. | JWT        |  Kişisel ve Azure AD hesapları.   |         |
| `platf`                    | Cihaz platformu    | JWT        |           | Cihaz türünü doğrulayabileceği yönetilen cihazlarla kısıtlıdır.|
| `verified_primary_email`   | Kullanıcının açık olan Yauthcumtativee-postalarından kaynaklandırılmış      | JWT        |           |         |
| `verified_secondary_email` | Kullanıcının SecondaryAuthoritativeEmail kaynağı   | JWT        |           |        |
| `enfpolids`                | Zorunlu kılınan ilke kimlikleri. Geçerli Kullanıcı için değerlendirilen ilke kimliklerinin bir listesi. | JWT |  |  |
| `vnet`                     | VNET tanımlayıcı bilgileri. | JWT        |           |      |
| `fwd`                      | IP adresi.| JWT    |   | İstek sunan istemcinin özgün IPv4 adresini ekler (sanal ağ içinde) |
| `ctry`                     | Kullanıcının ülkesi | JWT |  | Azure AD, varsa isteğe bağlı `ctry` talebi döndürür ve talebin değeri FR, JP, SZ vb. gibi standart iki harfli bir ülke kodudur. |
| `tenant_ctry`              | Kaynak kiracının ülkesi | JWT | | |
| `xms_pdl`          | Tercih edilen veri konumu   | JWT | | Çoklu coğrafi kiracılar için, bu, kullanıcının içinde bulunduğu coğrafi bölgeyi gösteren 3 harfli koddur. Daha fazla bilgi için, [tercih edilen veri konumu hakkında Azure AD Connect belgelerine](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation)bakın.<br/>Örneğin: Asya Pasifik için `APC`. |
| `xms_pl`                   | Kullanıcı tarafından tercih edilen dil  | JWT ||Ayarlanmışsa kullanıcının tercih ettiği dil. Konuk erişim senaryolarında, ana kiracılarından kaynaklıdır. Biçimlendirilen LL-CC ("en-US"). |
| `xms_tpl`                  | Kiracının tercih ettiği dil| JWT | | Ayarlanırsa, kaynak kiracının tercih edilen dili. Biçimlendirildim ("en"). |
| `ztdid`                    | Sıfır dokunma dağıtım KIMLIĞI | JWT | | [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) için kullanılan cihaz kimliği |
| `email`                    | Kullanıcının bir tane varsa, bu kullanıcı için adreslenebilir e-posta.  | JWT, SAML | MSA, Azure AD | Bu değer, Kullanıcı Kiracıdaki bir konuk ise varsayılan olarak dahil edilir.  Yönetilen kullanıcılar (kiracının içindeki) için, bu isteğe bağlı talep aracılığıyla veya yalnızca v 2.0 'da, OpenID kapsamıyla istenir.  Yönetilen kullanıcılar için, e-posta adresinin [Office Yönetim Portalı](https://portal.office.com/adminportal/home#/users)'nda ayarlanması gerekir.| 
| `groups`| Grup talepleri için isteğe bağlı biçimlendirme |JWT, SAML| |[Uygulama bildiriminde](reference-app-manifest.md), aynı zamanda ayarlanması gereken Groupmembershipclaim ayarıyla birlikte kullanılır. Ayrıntılar için aşağıdaki [Grup taleplerini](#Configuring-group-optional claims) inceleyin. Grup talepleri hakkında daha fazla bilgi için bkz. [Grup taleplerini yapılandırma](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Kiracıdaki Kullanıcı hesabı durumu. | JWT, SAML | | Kullanıcı kiracının üyesiyse, değer `0`. Bu bir konuğlarsa değer `1`. |
| `upn`                      | UserPrincipalName talebi. | JWT, SAML  |           | Bu talep otomatik olarak dahil edilse de, Konuk Kullanıcı kasasında davranışını değiştirmek üzere ek özellikler eklemek için isteğe bağlı bir talep olarak belirtebilirsiniz.  |

### <a name="v20-optional-claims"></a>v 2.0 isteğe bağlı talepler

Bu talepler her zaman v 1.0 Azure AD belirteçlerine dahil edilmiştir, ancak istenmediği takdirde v 2.0 belirteçlerine dahil edilmez. Bu talepler yalnızca JWTs (KIMLIK belirteçleri ve erişim belirteçleri) için geçerlidir. 

**Tablo 3: v 2.0-yalnızca isteğe bağlı talepler**

| JWT talebi     | Adı                            | Açıklama                                | Notlar |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP Adresi                      | İstemcinin oturum açtığı IP adresi.   |       |
| `onprem_sid`  | Şirket Içi güvenlik tanımlayıcısı |                                             |       |
| `pwd_exp`     | Parola sona erme saati        | Parolanın süresi dolan tarih/saat. |       |
| `pwd_url`     | Parola URL 'sini Değiştir             | Kullanıcının parolalarını değiştirmek için ziyaret edebildikleri bir URL.   |   |
| `in_corp`     | Şirket içi ağ        | İstemci şirket ağından oturum açıyorsanız bildirir. Aksi takdirde talep dahil edilmez.   |  MFA 'daki [Güvenilen IP 'lerin](../authentication/howto-mfa-mfasettings.md#trusted-ips) ayarlarını temel alarak.    |
| `nickname`    | Takma ad                        | Kullanıcı için, adı ilk veya soyadı olan ek bir ad. | 
| `family_name` | Soyadı                       | Kullanıcı nesnesinde tanımlandığı şekilde kullanıcının soyadı, soyadı veya aile adını sağlar. <br>"family_name": "Miller" | MSA ve Azure AD 'de desteklenir   |
| `given_name`  | Ad                      | Kullanıcı nesnesinde ayarlandığı gibi, kullanıcının ilk veya "verilen" adını sağlar.<br>"given_name": "filiz"                   | MSA ve Azure AD 'de desteklenir  |
| `upn`         | Kullanıcı Asıl Adı | Kullanıcı için username_hint parametresiyle kullanılabilecek bir tanımlayıcı.  Kullanıcı için dayanıklı bir tanımlayıcı değildir ve anahtar verileri için kullanılmamalıdır. | Talebin yapılandırması için aşağıdaki [ek özelliklere](#additional-properties-of-optional-claims) bakın. |

### <a name="additional-properties-of-optional-claims"></a>İsteğe bağlı taleplerin ek özellikleri

İsteğe bağlı talepler, talebin döndürdüğü yöntemi değiştirecek şekilde yapılandırılabilir. Bu ek özellikler çoğunlukla, farklı veri beklentilerine sahip şirket içi uygulamaların geçişine yardımcı olmak için kullanılır (örneğin, `include_externally_authenticated_upn_without_hash` UPN 'de karma işaretlerini (`#`) işleyemeyen istemcilerde yardımcı olur)

**Tablo 4: isteğe bağlı talepler yapılandırma değerleri**

| Özellik adı  | Ek özellik adı | Açıklama |
|----------------|--------------------------|-------------|
| `upn`          |                          | Hem SAML hem de JWT yanıtları için ve v 1.0 ve v 2.0 belirteçleri için kullanılabilir. |
|                | `include_externally_authenticated_upn`  | , Kaynak kiracısında depolanan Konuk UPN 'sini içerir. Örneğin, `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Yukarıdaki gibi, karma işaretlerinin (`#`) alt çizgi (`_`) ile değiştirilmeleri dışında, örneğin `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Ek özellikler örneği

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

Bu Optionalclaim nesnesi, istemciye döndürülen KIMLIK belirtecinin ek ana kiracı ve kaynak kiracı bilgilerine sahip başka bir UPN içermesini sağlar. Bu, Kullanıcı Kiracıdaki bir konuk ise (kimlik doğrulaması için farklı bir ıDP kullanan), belirteçteki `upn` talebini değiştirir. 

## <a name="configuring-optional-claims"></a>İsteğe bağlı talepler yapılandırılıyor

Uygulama bildirimini değiştirerek uygulamanız için isteğe bağlı talepler yapılandırabilirsiniz (aşağıdaki örneğe bakın). Daha fazla bilgi için bkz. [Azure AD uygulama bildirimini anlama makalesi](reference-app-manifest.md).

> [!IMPORTANT]
> Erişim belirteçleri, **her zaman** istemcinin değil kaynağın bildirimi kullanılarak oluşturulur.  Bu nedenle, istek `...scope=https://graph.microsoft.com/user.read...` kaynak grafiktir.  Bu nedenle, erişim belirteci, istemci bildirimi değil, grafik bildirimi kullanılarak oluşturulur.  Uygulamanızın bildirimini değiştirmek hiçbir şekilde grafiğin belirteçlerinin farklı görünmesine neden olmaz.  `accessToken` değişikliklerinizin geçerli olduğunu doğrulamak için, başka bir uygulama değil, uygulamanız için bir belirteç isteyin.  

**Örnek şema:**

```json
"optionalClaims":  
   {
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

### <a name="optionalclaims-type"></a>Optionalclaim türü

Bir uygulama tarafından istenen isteğe bağlı talepleri bildirir. Bir uygulama, güvenlik belirteci hizmetinden alabileceği üç tür belirteç türünden (KIMLIK belirteci, erişim belirteci, SAML 2 belirteci) döndürülecek isteğe bağlı talepler yapılandırabilir. Uygulama, her bir belirteç türünde döndürülecek farklı bir isteğe bağlı talepler kümesi yapılandırabilir. Uygulama varlığının Optionalclaim özelliği bir Optionalclaim nesnesidir.

**Tablo 5: Optionalclaim türü özellikleri**

| Adı        | Tür                       | Açıklama                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Koleksiyon (OptionalClaim) | JWT KIMLIK belirtecinde döndürülen isteğe bağlı talepler. |
| `accessToken` | Koleksiyon (OptionalClaim) | JWT erişim belirtecinde döndürülen isteğe bağlı talepler. |
| `saml2Token`  | Koleksiyon (OptionalClaim) | SAML belirtecinde döndürülen isteğe bağlı talepler.   |

### <a name="optionalclaim-type"></a>OptionalClaim türü

Bir uygulama veya hizmet sorumlusu ile ilişkili isteğe bağlı bir talep içerir. [Optionalclaim](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) türünün ıdtoken, accesstoken ve saml2Token özellikleri bir optionalclaim koleksiyonudur.
Belirli bir talep tarafından destekleniyorsa, Additionalclaim 'nin davranışını AdditionalProperties alanını kullanarak da değiştirebilirsiniz.

**Tablo 6: OptionalClaim türü özellikleri**

| Adı                 | Tür                    | Açıklama                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | İsteğe bağlı talebin adı.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | Talebin kaynağı (Dizin nesnesi). Uzantı özelliklerinden önceden tanımlı talepler ve Kullanıcı tanımlı talepler vardır. Kaynak değeri null ise, talep önceden tanımlanmış isteğe bağlı bir talep olur. Kaynak değeri kullanıcı ise, ad özelliğindeki değer kullanıcı nesnesinden uzantı özelliğidir. |
| `essential`            | Edm.Boolean             | Değer true ise, istemci tarafından belirtilen talep, son kullanıcı tarafından istenen belirli bir görev için sorunsuz bir yetkilendirme deneyimi sağlamak için gereklidir. Varsayılan değer false 'dur.                                                                                                             |
| `additionalProperties` | Koleksiyon (EDM. String) | Talebin ek özellikleri. Bu koleksiyonda bir özellik varsa, ad özelliğinde belirtilen isteğe bağlı talebin davranışını değiştirir.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Dizin uzantısı isteğe bağlı taleplerini yapılandırma

Standart isteğe bağlı talepler kümesine ek olarak, belirteçleri Dizin şeması uzantıları içerecek şekilde de yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Dizin şeması uzantıları](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). Bu özellik, uygulamanızın kullanabileceği ek kullanıcı bilgilerini eklemek için yararlıdır; Örneğin, kullanıcının ayarlamış olduğu ek bir tanımlayıcı veya önemli bir yapılandırma seçeneği. 

> [!Note]
> - Dizin şeması uzantıları yalnızca Azure AD özellikli bir özelliktir, bu nedenle uygulama bildiriminiz uygulamanızda özel bir uzantı ve bir MSA kullanıcısı oturum açarsa, bu uzantılar döndürülmez.
> - Azure AD isteğe bağlı talepler yalnızca Azure AD uzantısıyla çalışır ve Microsoft Graph Directory uzantısıyla çalışmaz. Her iki API de yalnızca yöneticiler tarafından onaylanan `Directory.ReadWriteAll` iznini gerektirir.

### <a name="directory-extension-formatting"></a>Dizin uzantısı biçimlendirmesi

Uzantı öznitelikleri için, uygulama bildiriminde uzantının tam adını (: `extension_<appid>_<attributename>`) kullanın. `<appid>` talep isteyen uygulamanın KIMLIĞIYLE eşleşmelidir. 

JWT içinde bu talepler şu ad biçimiyle yayılır: `extn.<attributename>`.

SAML belirteçlerinde Bu talepler şu URI biçimiyle yayınlanacaktır: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-group-optional-claims"></a>Grup isteğe bağlı talepler yapılandırılıyor

   > [!NOTE]
   > Şirket içinden eşitlenen kullanıcılar ve gruplar için Grup adlarını yayma özelliği genel önizlemeye sunuldu.

Bu bölüm, Grup taleplerinde kullanılan grup özniteliklerinin, şirket içi Windows Active Directory eşitlenen özniteliklere varsayılan gruptan değiştirilmesini sağlamak için isteğe bağlı talepler altındaki yapılandırma seçeneklerini anlatmaktadır.

> [!IMPORTANT]
> Şirket içi özniteliklerdeki grup taleplerinin genel önizlemesine ilişkin önemli uyarılar da dahil olmak üzere [Azure AD ile uygulamalar için grup taleplerini yapılandırma](../hybrid/how-to-connect-fed-group-claims.md) konusuna bakın.

1. Portal-> Azure Active Directory-> uygulama kayıtları-> Uygulama-> bildirimini seçin

2. GroupMembershipClaim 'i değiştirerek grup üyeliği taleplerini etkinleştirin

   Geçerli değerler şunlardır:

   - Bütün
   - "SecurityGroup"
   - "DistributionList"
   - "DirectoryRole"

   Örnek:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Varsayılan olarak grup ObjectID 'Ler, Grup talep değeri ' ne yayılır.  Talep değerini şirket içi grup özniteliklerini içerecek şekilde değiştirmek veya talep türünü rol olarak değiştirmek için, Optionalclaim yapılandırması ' nı aşağıdaki gibi kullanın:

3. Grup adı yapılandırması isteğe bağlı taleplerini ayarlayın.

   Belirteçte isteğe bağlı talepler bölümünde şirket içi AD grubu özniteliklerini içeren gruplar istiyorsanız, isteğe bağlı talebin adı, istenen isteğe bağlı talep ve istediğiniz ek özellikleri belirtin.  Birden çok belirteç türü listelenebilir:

   - OıDC KIMLIK belirtecinin ıdtoken 'ı
   - OAuth/OıDC erişim belirteci için accessToken
   - SAML belirteçleri için Saml2Token.

   > [!NOTE]
   > Saml2Token türü SAML 1.1 ve SAML 2.0 biçim belirteçleri için geçerlidir

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
   | **kaynaktaki** | Kullanılmıyor. Null değerini atla veya belirt |
   | **dir** | Kullanılmıyor. Yoksay veya false belirt |
   | **AdditionalProperties** | Ek özelliklerin listesi.  Geçerli seçenekler şunlardır "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   AdditionalProperties içinde "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" yalnızca biri gereklidir.  Birden fazla varsa, ilki kullanılır ve diğerleri yok sayılır.

   Bazı uygulamalar, rol talebinde Kullanıcı hakkında grup bilgileri gerektirir.  Talep türünü bir grup talebine bir rol talebine değiştirmek için, ek özelliklere "emit_as_roles" ekleyin.  Grup değerleri rol talebinde yayınlanacaktır.

   > [!NOTE]
   > "Emit_as_roles" kullanılırsa, Kullanıcı atandığı yapılandırılmış herhangi bir uygulama rolü rol talebinde görünmez

**Örnekler:** Grupları DNSEtkiAlanıAdı sAMAccountName biçimindeki OAuth erişim belirteçlerinde grup adları olarak yay

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

SAML ve OıDC KIMLIK belirteçlerinde rol talebi olarak netbiosDomain\sAMAccountName biçiminde döndürülecek grup adlarını yayma:

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }

 ```

## <a name="optional-claims-example"></a>İsteğe bağlı talepler örneği

Bu bölümde, uygulamanız için isteğe bağlı talepler özelliğini nasıl kullanabileceğinizi görmek için bir senaryoya yol açabilir.
İsteğe bağlı talepler etkinleştirmek ve yapılandırmak için bir uygulamanın kimlik yapılandırmasındaki özellikleri güncelleştirmek üzere kullanılabilecek birden fazla seçenek vardır:
-   Uygulama bildirimini değiştirebilirsiniz. Aşağıdaki örnekte, yapılandırma yapmak için bu yöntem kullanılacaktır. Bildirime giriş için önce [Azure AD uygulama bildirimi belgesini anlama](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) makalesini okuyun.
-   Uygulamanızı güncelleştirmek için [Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) kullanan bir uygulama yazmak da mümkündür. Graph API başvuru kılavuzundaki [varlık ve karmaşık tür başvurusu](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) , isteğe bağlı talepleri yapılandırmanıza yardımcı olabilir.

**Örnek:** Aşağıdaki örnekte, uygulama için tasarlanan erişim, KIMLIK ve SAML belirteçlerine talepler eklemek için bir uygulamanın bildirimini değiştirirsiniz.

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.
1. Kimlik doğrulamasından geçtikten sonra, sayfanın sağ üst köşesinden seçerek Azure AD kiracınızı seçin.
1. Sol taraftaki **uygulama kayıtları** ' nı seçin.
1. Listede için isteğe bağlı talepler yapılandırmak istediğiniz uygulamayı bulun ve üzerine tıklayın.
1. Uygulama sayfasında, **bildirim** ' a tıklayarak satır içi bildirim düzenleyicisini açın. 
1. Bu düzenleyiciyi kullanarak bildirimi doğrudan düzenleyebilirsiniz. Bildirim, [uygulama varlığı](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)için şemayı izler ve kaydedildikten sonra bildirimi otomatik biçimlendirir. Yeni öğeler `OptionalClaims` özelliğine eklenecektir.

    ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
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

    Bu durumda, uygulamanın alabileceği her bir belirteç türüne farklı isteğe bağlı talepler eklenmiştir. KIMLIK belirteçleri artık Federasyon kullanıcıları için UPN 'yi tam biçimde (`<upn>_<homedomain>#EXT#@<resourcedomain>`) içerecektir. Bu uygulama için diğer istemcilerin talep aldığı erişim belirteçleri artık auth_time talebini içerecektir. SAML belirteçleri artık Sktypeınfo dizin şema uzantısını içerecektir (Bu örnekte, bu uygulamanın uygulama KIMLIĞI ab603c56068041afb2f6832e2a17e237 ' dir). SAML belirteçleri, Skype KIMLIĞINI `extension_skypeId`olarak kullanıma sunacaktır.

1. Bildirimi güncelleştirmeyi tamamladığınızda, bildirimi kaydetmek için **Kaydet** ' e tıklayın

## <a name="next-steps"></a>Sonraki adımlar

Azure AD tarafından sunulan standart talepler hakkında daha fazla bilgi edinin.

- [KIMLIK belirteçleri](id-tokens.md)
- [Erişim belirteçleri](access-tokens.md)
