---
title: Azure Active Directory olan uygulamalar için grup taleplerini yapılandırma | Microsoft Docs
description: Azure AD ile kullanım için grup taleplerini yapılandırma hakkında bilgiler.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 3cb53656adb1dbeb5e5597d02edfe5be4dbec6a8
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170494"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Azure Active Directory olan uygulamalar için grup taleplerini yapılandırma (Genel Önizleme)

Azure Active Directory, bir Kullanıcı grubu üyelik bilgilerini uygulamalar içinde kullanım için belirteçlerde sağlayabilir.  İki ana desen desteklenir:

- Azure Active Directory nesne tanımlayıcısı (OID) özniteliğiyle tanımlanan gruplar (genel olarak kullanılabilir)
- Active Directory (AD) eşitlenmiş gruplar ve kullanıcılar için sAMAccountName veya Groupsıd öznitelikleriyle tanımlanan gruplar (Genel Önizleme)

> [!IMPORTANT]
> Bu önizleme işlevselliği için dikkat edilecek bir dizi uyarılar vardır:
>
>- Şirket içinden eşitlenen sAMAccountName ve güvenlik tanımlayıcısı (SID) özniteliklerinin kullanımı için destek, mevcut uygulamaların AD FS ve diğer kimlik sağlayıcılarından taşınmasını sağlamak üzere tasarlanmıştır. Azure AD 'de yönetilen gruplar, bu talepleri oluşturmak için gereken öznitelikleri içermez.
>- Daha büyük kuruluşlarda, bir kullanıcının üyesi olduğu grupların sayısı Azure Active Directory bir belirtece eklenecek sınırı aşabilir. bir SAML belirteci için 150 grupları ve JWT için 200. Bu, öngörülemeyen sonuçlara neden olabilir. Bu olası bir sorun varsa, test etmenizi öneririz ve gerekli geliştirmeler, talepleri uygulama için ilgili gruplarla sınırlandırmanıza olanak tanımak için iyileştirmeler ekliyoruz.  
>- Yeni uygulama geliştirme veya uygulamanın kendisi için yapılandırılabileceği ve iç içe geçmiş grup desteğinin gerekmediği durumlarda, uygulama içi yetkilendirmenin gruplar yerine uygulama rollerine dayalı olması önerilir.  Bu, belirtece gitmesi gereken bilgi miktarını kısıtlar, daha güvenlidir ve Kullanıcı atamasını uygulama yapılandırmasından ayırır.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>AD FS ve diğer kimlik sağlayıcılarından geçiş yapan uygulamalar için Grup talepleri

AD FS kimlik doğrulaması yapacak şekilde yapılandırılmış birçok uygulama, Windows AD grup öznitelikleri biçimindeki grup üyeliği bilgilerini kullanır.   Bu öznitelikler, etki alanı adı veya Windows grup güvenlik tanımlayıcısı (Groupsıd) ile nitelenbilen sAMAccountName grubudur.  Uygulama AD FS ile federe olduğunda, AD FS Kullanıcı için grup üyeliklerini almak üzere TokenGroups işlevini kullanır.

Bir uygulamanın AD FS alacağı belirteci eşleştirmek için, Grup ve rol talepleri grubun Azure Active Directory ObjectID yerine nitelikli sAMAccountName etki alanı olabilir.

Grup talepleri için desteklenen biçimler şunlardır:

- **Azure Active Directory grubu ObjectID** (Tüm gruplar için kullanılabilir)
- **sAMAccountName** (Active Directory eşitlenen gruplar için kullanılabilir)
- **Netbiosdomain\samaccountname** (Active Directory eşitlenen gruplar için kullanılabilir)
- **DNSEtkiAlanıAdı sAMAccountName** (Active Directory eşitlenen gruplar için kullanılabilir)
- **Şirket Içi grup güvenlik tanımlayıcısı** (Active Directory eşitlenen gruplar için kullanılabilir)

> [!NOTE]
> sAMAccountName ve on-premises Group SID öznitelikleri yalnızca Active Directory 'ten eşitlenen grup nesnelerinde kullanılabilir.   Azure Active Directory veya Office365 içinde oluşturulan gruplar üzerinde kullanılamaz.   Eşitlenmiş şirket içi grup özniteliklerini almak için Azure Active Directory 'de yapılandırılan uygulamalar, onları yalnızca eşitlenmiş gruplar için alır.

## <a name="options-for-applications-to-consume-group-information"></a>Uygulamaların grup bilgilerini tüketmesi için seçenekler

Uygulamaların grup bilgilerini elde etmek için bir yol, kimliği doğrulanmış kullanıcı için Grup üyeliğini almak üzere Graf grupları uç noktasını çağırmalıdır. Bu çağrı, kullanıcının üye olduğu tüm grupların üyesi olduğu çok sayıda grup olduğunda bile kullanılabilir ve uygulamanın kullanıcının üyesi olduğu tüm grupları numaralandırması gerekir.  Grup numaralandırması, daha sonra belirteç boyutu sınırlamalarından bağımsızdır.

Ancak, mevcut bir uygulama, aldığı belirteçteki talepler aracılığıyla grup bilgilerini tüketmeyi bekliyorsa, Azure Active Directory uygulamanın ihtiyaçlarını karşılayacak sayıda farklı talep seçeneği ile yapılandırılabilir.  Aşağıdaki seçenekleri göz önünde bulundurun:

- Uygulama içi yetkilendirme amacıyla Grup üyeliği kullanırken, Azure Active Directory sabit ve benzersiz olan ve tüm gruplar için kullanılabilen ObjectID grubunu kullanmak tercih edilir.
- Yetkilendirme için şirket içi grup sAMAccountName kullanılıyorsa, etki alanı nitelikli adlar kullanın;  ad çakışması durumunda doğan durumlardan daha az bir olasılığı vardır. tek başına sAMAccountName bir Active Directory etki alanı içinde benzersiz olabilir, ancak birden fazla Active Directory etki alanı bir Azure Active Directory kiracısıyla eşitlenirse, birden fazla grubun aynı ada sahip olması mümkün olur.
- Grup üyeliği ve uygulama arasında bir yöneltme katmanı sağlamak için [uygulama rollerini](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) kullanmayı düşünün.   Daha sonra uygulama, belirteçte rol çakışması temelinde iç yetkilendirme kararları verir.
- Uygulama, Active Directory eşitlenen grup özniteliklerini almak üzere yapılandırıldıysa ve bir grup bu öznitelikleri içermiyorsa, taleplere dahil değildir.
- Belirteçlerde grup talepleri iç içe gruplar içerir.   Bir Kullanıcı GroupB üyesiyse ve GroupB bir GroupA üyesiyse, Kullanıcı için Grup talepleri hem GroupA hem de GroupB 'yi içerecektir. İç içe geçmiş grupların ve kullanıcıların çok sayıda grup üyeliğine sahip olan kuruluşların kullanımı için, belirteçte listelenen grup sayısı belirteç boyutunu büyütebilir.   Azure Active Directory, SAML onaylamaları için bir belirteçte 150 'e yayarak grup sayısını kısıtlar 200 ve belirteçlerin çok büyük bir değer elde etmelerini engellemek için.  Kullanıcı sınırdan daha fazla sayıda grubun üyesiyse gruplar, grup bilgilerini almak için grafik uç noktasına bir bağlantı ile birlikte yayınlanır.

> Active Directory 'ten eşitlenen grup özniteliklerini kullanma önkoşulları:   Grupların Azure AD Connect kullanılarak Active Directory eşitlenmesi gerekir.

Active Directory grupları için Grup adlarını yayma Azure Active Directory yapılandırmanın iki adımı vardır.

1. **Active Directory Grup adlarını eşitler** Azure Active Directory, Grup veya rol taleplerinde grup adlarını veya şirket grubu SID 'sini yaymadan önce, gerekli özniteliklerin Active Directory eşitlenmesi gerekir.  Azure AD Connect Version 1.2.70 veya üstünü çalıştırıyor olmanız gerekir.   Sürüm Azure AD Connect 1.2.70 'tan önce, Grup nesnelerini Active Directory eşitler, ancak gerekli Grup adı özniteliklerini varsayılan olarak içermez.  Geçerli sürüme yükseltmeniz gerekir.

2. **Belirteçleri grup taleplerini içerecek şekilde Azure Active Directory için uygulama kaydını yapılandırma** Grup talepleri, bir galeri veya Galeri dışı SAML SSO uygulaması için portalın kurumsal uygulamalar bölümünde ya da uygulama kayıtları bölümünde uygulama bildirimi kullanılarak yapılandırılabilir.  Uygulama bildiriminde grup taleplerini yapılandırmak için aşağıdaki "grup öznitelikleri için Azure Active Directory Uygulama kaydını yapılandırma" bölümüne bakın.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>SSO yapılandırmasını kullanarak SAML uygulamaları için grup taleplerini yapılandırma

Galeri veya Galeri dışı bir SAML uygulamasının grup taleplerini yapılandırmak için kurumsal uygulamaları açın, listede uygulamaya tıklayın ve çoklu oturum açma yapılandırması ' nı seçin.

"Belirteçte döndürülen gruplar" ın yanındaki Düzenle simgesini seçin

![talepler Kullanıcı arabirimi](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Simgeye dahil edilecek grupları seçmek için radyo düğmelerini kullanın

![talepler Kullanıcı arabirimi](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Seçim | Açıklama |
|----------|-------------|
| **Tüm gruplar** | Güvenlik grupları ve dağıtım listeleri yayar.   Ayrıca, kullanıcının bir ' WIT ' talebinde yayınlanmasına atanan dizin rollerine ve Kullanıcı tarafından rol talebine yayınlanabileceği tüm uygulama rollerine neden olur. |
| **Güvenlik grupları** | Kullanıcının gruplar talebinde üyesi olduğu güvenlik gruplarını yayar |
| **Dağıtım listeleri** | Kullanıcının üyesi olduğu dağıtım listelerini yayar |
| **Dizin rolleri** | Kullanıcıya Dizin rolleri atanırsa, bunlar ' WDS ' talebi olarak dağıtılır (gruplar talebi yayınlanmaz) |

Örneğin, kullanıcının üyesi olduğu tüm güvenlik gruplarını yayma için güvenlik grupları ' nı seçin.

![talepler Kullanıcı arabirimi](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Azure AD ObjectIDs yerine Active Directory eşitlenen Active Directory öznitelikleri kullanarak grupları yaymak için, açılan listeden gerekli biçimi seçin.  Bu, taleplerdeki nesne KIMLIĞINI grup adlarını içeren dize değerleriyle değiştirir.   Taleplere yalnızca Active Directory eşitlenen gruplar dahil edilir.

![talepler Kullanıcı arabirimi](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

### <a name="advanced-options"></a>Gelişmiş seçenekler

Grup taleplerinin Yayınlanma yöntemi, Gelişmiş Seçenekler altındaki ayarlar tarafından değiştirilebilir

Grup talebinin adını özelleştirin:  Seçilirse, Grup talepleri için farklı bir talep türü belirtilebilir.   Ad alanına talep türünü ve isteğe bağlı ad alanını ad alanı alanında girin.

![talepler Kullanıcı arabirimi](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Bazı uygulamalar, Grup üyeliği bilgilerinin ' rol ' talebinde görünmesini gerektirir. İsteğe bağlı olarak, ' grupları bir rol taleplerini yay ' kutusunu işaretleyerek kullanıcının gruplarını roller olarak görüntüleyebilirsiniz.

![talepler Kullanıcı arabirimi](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Grup verilerini roller olarak yayma seçeneği kullanılırsa, rol talebinde yalnızca gruplar görüntülenir.  Kullanıcının atandığı tüm uygulama rolleri rol talebinde görünmez.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Grup öznitelikleri için Azure AD uygulama kaydını yapılandırma

Grup talepleri, [uygulama bildiriminin](../../active-directory/develop/reference-app-manifest.md) [isteğe bağlı talepler](../../active-directory/develop/active-directory-optional-claims.md) bölümünde de yapılandırılabilir.

1. Portal-> Azure Active Directory-> uygulama kayıtları-> Uygulama-> bildirimini seçin

2. GroupMembershipClaim 'i değiştirerek grup üyeliği taleplerini etkinleştirin

   Geçerli değerler şunlardır:

   - Bütün
   - "SecurityGroup"
   - "DistributionList"
   - "DirectoryRole"

   Örneğin:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Varsayılan olarak grup ObjectID 'Ler, Grup talep değeri ' ne yayılır.  Talep değerini şirket içi grup özniteliklerini içerecek şekilde değiştirmek veya talep türünü rol olarak değiştirmek için, Optionalclaim yapılandırması ' nı aşağıdaki gibi kullanın:

3. Grup adı yapılandırması isteğe bağlı taleplerini ayarlayın.

   Belirteçteki grupların, isteğe bağlı talepler bölümünde şirket içi AD grubu özniteliklerini içermesini istiyorsanız, isteğe bağlı talebin adı ve istenen ek özellikleri belirtin.  Birden çok belirteç türü listelenebilir:

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

   | İsteğe bağlı talepler şeması | Value |
   |----------|-------------|
   | **ada** | "Gruplar" olmalıdır |
   | **kaynaktaki** | Kullanılmıyor. Null değerini atla veya belirt |
   | **dir** | Kullanılmıyor. Yoksay veya false belirt |
   | **AdditionalProperties** | Ek özelliklerin listesi.  Geçerli seçenekler şunlardır "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   AdditionalProperties içinde "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" yalnızca biri gereklidir.  Birden fazla varsa, ilki kullanılır ve diğerleri yok sayılır.

   Bazı uygulamalar, rol talebinde Kullanıcı hakkında grup bilgileri gerektirir.  Talep türünü bir grup talebine bir rol talebine değiştirmek için, ek özelliklere "emit_as_roles" ekleyin.  Grup değerleri rol talebinde yayınlanacaktır.

   > [!NOTE]
   > "Emit_as_roles" kullanılırsa, Kullanıcı atandığı yapılandırılmış herhangi bir uygulama rolü rol talebinde görünmez

### <a name="examples"></a>Örnekler

Grupları DNSEtkiAlanıAdı sAMAccountName biçimindeki OAuth erişim belirteçlerinde grup adları olarak yay

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

SAML ve OıDC KIMLIK belirteçlerinde rol talebi olarak netbiosDomain\samAccountName biçiminde döndürülecek grup adlarını yayma:

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

## <a name="next-steps"></a>Sonraki adımlar

[Hibrit kimlik nedir?](whatis-hybrid-identity.md)
