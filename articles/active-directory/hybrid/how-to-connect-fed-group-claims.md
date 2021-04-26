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
ms.topic: how-to
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: bef5942707c1ded22ba82bdb0d945b9fdb23fffa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349359"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory"></a>Azure Active Directory olan uygulamalar için grup taleplerini yapılandırma

Azure Active Directory, bir Kullanıcı grubu üyelik bilgilerini uygulamalar içinde kullanım için belirteçlerde sağlayabilir.  İki ana desen desteklenir:

- Azure Active Directory nesne tanımlayıcısı (OID) özniteliğiyle tanımlanan gruplar
- Active Directory (AD) eşitlenmiş gruplar ve kullanıcılar için sAMAccountName veya Groupsıd öznitelikleriyle tanımlanan gruplar

> [!IMPORTANT]
> Bu işlevsellik için dikkat edilecek bir dizi uyarılar vardır:
>
>- Şirket içinden eşitlenen sAMAccountName ve güvenlik tanımlayıcısı (SID) özniteliklerinin kullanımı için destek, mevcut uygulamaların AD FS ve diğer kimlik sağlayıcılarından taşınmasını sağlamak üzere tasarlanmıştır. Azure AD 'de yönetilen gruplar, bu talepleri oluşturmak için gereken öznitelikleri içermez.
>- Daha büyük kuruluşlarda, bir kullanıcının üyesi olduğu grupların sayısı Azure Active Directory bir belirtece eklenecek sınırı aşabilir. bir SAML belirteci için 150 grupları ve JWT için 200. Bu, öngörülemeyen sonuçlara neden olabilir. Kullanıcılarınızın çok sayıda grup üyeliği varsa, taleplerde yayınlanan grupları uygulamanın ilgili gruplarıyla kısıtlama seçeneğini kullanmanızı öneririz.  
>- Yeni uygulama geliştirme veya uygulamanın kendisi için yapılandırılabileceği ve iç içe geçmiş grup desteğinin gerekmediği durumlarda, uygulama içi yetkilendirmenin gruplar yerine uygulama rollerine dayalı olması önerilir.  Bu, belirtece gitmesi gereken bilgi miktarını kısıtlar, daha güvenlidir ve Kullanıcı atamasını uygulama yapılandırmasından ayırır.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>AD FS ve diğer kimlik sağlayıcılarından geçiş yapan uygulamalar için Grup talepleri

AD FS kimlik doğrulaması için yapılandırılan birçok uygulama, Windows AD grup öznitelikleri biçimindeki grup üyeliği bilgilerini kullanır.   Bu öznitelikler, etki alanı adı veya Windows grup güvenlik tanımlayıcısı (Groupsıd) ile nitelenbilen sAMAccountName grubudur.  Uygulama AD FS ile federe olduğunda, AD FS Kullanıcı için grup üyeliklerini almak üzere TokenGroups işlevini kullanır.

Aynı biçimdeki AD FS gereksinimlerine taşınan bir uygulama. Grup ve rol talepleri, Grup Azure Active Directory ObjectID yerine Active Directory tarafından eşitlenmiş olan etki alanı üyesi olan Azure Active Directory dağıtılabilir.

Grup talepleri için desteklenen biçimler şunlardır:

- **Azure Active Directory grubu ObjectID** (tüm gruplar için kullanılabilir)
- **sAMAccountName** (Active Directory eşitlenen gruplar için kullanılabilir)
- **Netbiosdomain\samaccountname** (Active Directory ile eşitlenen gruplar için kullanılabilir)
- **DNSEtkiAlanıAdı sAMAccountName** (Active Directory eşitlenen gruplar için kullanılabilir)
- **Şirket Içi grup güvenlik tanımlayıcısı** (Active Directory ile eşitlenen gruplar için kullanılabilir)

> [!NOTE]
> sAMAccountName ve on-premises Group SID öznitelikleri yalnızca Active Directory 'ten eşitlenen grup nesnelerinde kullanılabilir.   Azure Active Directory veya Office365 içinde oluşturulan gruplar üzerinde kullanılamaz.   Eşitlenmiş şirket içi grup özniteliklerini almak için Azure Active Directory 'de yapılandırılan uygulamalar, onları yalnızca eşitlenmiş gruplar için alır.

## <a name="options-for-applications-to-consume-group-information"></a>Uygulamaların grup bilgilerini tüketmesi için seçenekler

Uygulamalar, kimliği doğrulanmış kullanıcı için grup bilgilerini almak üzere MS Graph Groups uç noktasını çağırabilir. Bu çağrı, bir kullanıcının üyesi olduğu tüm grupların, çok sayıda grup dahil olduğunda bile kullanılabilir olmasını sağlar.  Grup numaralandırması, daha sonra belirteç boyutu sınırlamalarından bağımsızdır.

Ancak, var olan bir uygulama talepler aracılığıyla grup bilgilerini tüketmeyi bekliyorsa, Azure Active Directory bir dizi farklı talep biçimi ile yapılandırılabilir.  Aşağıdaki seçenekleri göz önünde bulundurun:

- Uygulama içi yetkilendirme amacıyla Grup üyeliği kullanılırken, ObjectID grubunu kullanmak tercih edilir. Azure Active Directory Grup ObjectID sabit ve benzersiz ve tüm gruplar için kullanılabilir.
- Yetkilendirme için şirket içi grup sAMAccountName kullanılıyorsa, etki alanı nitelikli adlar kullanın;  ad çakışması daha az olabilir. sAMAccountName bir Active Directory etki alanı içinde benzersiz olabilir, ancak birden fazla Active Directory etki alanı bir Azure Active Directory kiracısıyla eşitlenirse, birden fazla grubun aynı ada sahip olması mümkün olur.
- Grup üyeliği ve uygulama arasında bir yöneltme katmanı sağlamak için [uygulama rollerini](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) kullanmayı düşünün.   Daha sonra uygulama, belirteçteki rol taleplerini temel alarak iç yetkilendirme kararları verir.
- Uygulama, Active Directory eşitlenen grup özniteliklerini almak üzere yapılandırıldıysa ve bir grup bu öznitelikleri içermiyorsa, taleplere dahil değildir.
- Belirteçlerdeki grup talepleri, grup taleplerini uygulamaya atanan gruplarla sınırlamak için seçeneğinin kullanılması dışında iç içe gruplar içerir.  Bir Kullanıcı GroupB üyesiyse ve GroupB bir GroupA üyesiyse, Kullanıcı için Grup talepleri hem GroupA hem de GroupB 'yi içerecektir. Bir kuruluşun kullanıcıları çok sayıda grup üyeliğine sahip olduğunda, belirteçte listelenen grup sayısı belirteç boyutunu büyütebilir.  Azure Active Directory, SAML onaylamaları için 150 'e ve JWT için 200 ' e yayalacak grup sayısını sınırlar.  Bir Kullanıcı daha fazla sayıda grubun üyesiyse, gruplar atlanır ve bunun yerine grup bilgilerini almak için grafik uç noktasına bir bağlantı eklenir.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Active Directory 'ten eşitlenen grup özniteliklerini kullanma önkoşulları

ObjectId biçimini kullanıyorsanız her grup için grup üyeliği talepleri belirteçlerde yayılabilir. Grup taleplerini grup ObjectID dışında bir biçimde kullanmak için, Active Directory Azure AD Connect kullanılarak grupların eşitlenmesi gerekir.

Active Directory grupları için Grup adlarını yayma Azure Active Directory yapılandırmanın iki adımı vardır.

1. **Active Directory Grup adlarını eşitler** Azure Active Directory, Grup veya rol taleplerinde grup adlarını veya şirket grubu SID 'sini yaymadan önce, gerekli özniteliklerin Active Directory eşitlenmesi gerekir.  Azure AD Connect Version 1.2.70 veya üstünü çalıştırıyor olmanız gerekir.   Daha eski Azure AD Connect sürümleri, Grup nesnelerini Active Directory eşitler, ancak gerekli Grup adı özniteliklerini içermez.  Geçerli sürüme yükseltin.

2. **Belirteçleri grup taleplerini içerecek şekilde Azure Active Directory için uygulama kaydını yapılandırma** Grup talepleri, portalın kurumsal uygulamalar bölümünde veya uygulama kayıtları bölümünde uygulama bildirimi kullanılarak yapılandırılabilir.  Uygulama bildiriminde grup taleplerini yapılandırmak için aşağıdaki "grup öznitelikleri için Azure Active Directory Uygulama kaydını yapılandırma" bölümüne bakın.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>SSO yapılandırması kullanarak SAML uygulamaları için belirteçlere grup talepleri ekleme

Galeri veya Galeri dışı bir SAML uygulamasının grup taleplerini yapılandırmak için, **Kurumsal uygulamaları** açın, listede uygulamaya tıklayın, **Çoklu oturum açma yapılandırması**' nı seçin ve ardından **Kullanıcı öznitelikleri & talepler**' i seçin.

**Grup talebi ekle** ' ye tıklayın  

!["Grup talebi ekle" seçiliyken "Kullanıcı öznitelikleri & talepler" sayfasını gösteren ekran görüntüsü.](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Simgeye dahil edilecek grupları seçmek için radyo düğmelerini kullanın

!["Güvenlik grupları" seçiliyken "talepleri Gruplandır" penceresini gösteren ekran görüntüsü.](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Seçim | Açıklama |
|----------|-------------|
| **Tüm gruplar** | Güvenlik grupları ve dağıtım listeleri ve rolleri yayar.  |
| **Güvenlik grupları** | Kullanıcının gruplar talebinde üyesi olduğu güvenlik gruplarını yayar |
| **Dizin rolleri** | Kullanıcıya Dizin rolleri atanırsa, bunlar ' WDS ' talebi olarak dağıtılır (gruplar talebi yayınlanmaz) |
| **Uygulamaya atanan gruplar** | Yalnızca uygulamaya açıkça atanmış olan grupları yayar ve Kullanıcı |

Örneğin, kullanıcının üyesi olduğu tüm güvenlik gruplarını yayma için güvenlik grupları ' nı seçin.

!["Güvenlik grupları" seçiliyken ve "kaynak özniteliği" açılan menüsü açık olan "talepleri Gruplandır" penceresini gösteren ekran görüntüsü.](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Azure AD ObjectIDs yerine Active Directory eşitlenen Active Directory öznitelikleri kullanarak grupları yaymak için, açılan listeden gerekli biçimi seçin. Taleplere yalnızca Active Directory eşitlenen gruplar dahil edilir.

!["Kaynak özniteliği" açılan menüsünün açık olduğunu gösteren ekran görüntüsü.](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Yalnızca uygulamaya atanan grupları yaymak için, **uygulamaya atanan grupları** seçin

!["Talepleri uygulamaya atanan gruplar" seçiliyken "talepleri Gruplandır" penceresini gösteren ekran görüntüsü.](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

Uygulamaya atanan gruplar belirtece dahil edilir.  Kullanıcının üyesi olduğu diğer gruplar da atlanır.  Bu seçenekle iç içe gruplar dahil edilmez ve Kullanıcı, uygulamaya atanan grubun doğrudan bir üyesi olmalıdır.

Uygulamaya atanan grupları değiştirmek için, **Kurumsal uygulamalar** listesinden uygulamayı seçin ve ardından uygulamanın sol taraftaki gezinti menüsünden **Kullanıcılar ve gruplar** ' a tıklayın.

Uygulamalara grup atamasını yönetme hakkında ayrıntılar için bkz. belge, bir [Kurumsal uygulamaya Kullanıcı veya Grup atama](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) .

### <a name="advanced-options"></a>Gelişmiş seçenekler

Grup taleplerinin Yayınlanma yöntemi, Gelişmiş Seçenekler altındaki ayarlar tarafından değiştirilebilir

Grup talebinin adını özelleştirin: seçilirse, Grup talepleri için farklı bir talep türü belirtilebilir.   Ad alanına talep türünü ve isteğe bağlı ad alanını ad alanı alanında girin.

!["Grup talebinin adını özelleştirin" ve "ad" ve "ad alanı" değerlerini girilen "Gelişmiş Seçenekler" bölümünü gösteren ekran görüntüsü.](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Bazı uygulamalar, Grup üyeliği bilgilerinin ' rol ' talebinde görünmesini gerektirir. İsteğe bağlı olarak, ' grupları bir rol taleplerini yay ' kutusunu işaretleyerek kullanıcının gruplarını roller olarak görüntüleyebilirsiniz.

!["Grup talebinin adını özelleştirme" ve "grupları rol talepleri olarak yay" seçiliyken "Gelişmiş Seçenekler" bölümünü gösteren ekran görüntüsü.](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Grup verilerini roller olarak yayma seçeneği kullanılırsa, rol talebinde yalnızca gruplar görüntülenir.  Kullanıcının atandığı tüm uygulama rolleri rol talebinde görünmez.

### <a name="edit-the-group-claims-configuration"></a>Grup talep yapılandırmasını düzenleme

Kullanıcı özniteliklerine & talep yapılandırmasına bir grup talebi yapılandırması eklendikten sonra, bir grup talebi ekleme seçeneği gri kalır.  Grup talebi yapılandırmasını değiştirmek için **ek talep** listesindeki grup talebine tıklayın.

![talepler Kullanıcı arabirimi](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Grup öznitelikleri için Azure AD uygulama kaydını yapılandırma

Grup talepleri, [uygulama bildiriminin](../../active-directory/develop/reference-app-manifest.md) [isteğe bağlı talepler](../../active-directory/develop/active-directory-optional-claims.md) bölümünde de yapılandırılabilir.

1. Portal->Azure Active Directory-> uygulama kayıtları->uygulama->bildirimini seçin

2. GroupMembershipClaim 'i değiştirerek grup üyeliği taleplerini etkinleştirin

Geçerli değerler:

| Seçim | Açıklama |
|----------|-------------|
| **Bütün** | Güvenlik gruplarını, dağıtım listelerini ve rolleri yayar |
| **"SecurityGroup"** | Kullanıcının gruplar talebinde üyesi olduğu güvenlik gruplarını yayar |
| **"DirectoryRole"** | Kullanıcıya Dizin rolleri atanırsa, bunlar ' WDS ' talebi olarak dağıtılır (gruplar talebi yayınlanmaz) |
| **ApplicationGroup** | Yalnızca uygulamaya açıkça atanmış olan grupları yayar ve Kullanıcı |
| **Seçim** | Hiçbir grup döndürülmedi. (Büyük/küçük harf olmaması bu nedenle, hiçbiri de işe yarar ve doğrudan uygulama bildiriminde ayarlanabilir.) |

   Örnek:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Varsayılan olarak grup ObjectID 'Ler, Grup talep değeri ' ne yayılır.  Talep değerini şirket içi grup özniteliklerini içerecek şekilde değiştirmek veya talep türünü rol olarak değiştirmek için, Optionalclaim yapılandırması ' nı aşağıdaki gibi kullanın:

3. Grup adı yapılandırması isteğe bağlı taleplerini ayarlayın.

   Belirteçteki grupların şirket içi AD grubu özniteliklerini içermesini istiyorsanız, isteğe bağlı talepler bölümünde hangi belirteç türü isteğe bağlı talebin uygulanacağını belirtin.  Birden çok belirteç türü listelenebilir:

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
   | **kaynaktaki** | Kullanılmadı. Null değerini atla veya belirt |
   | **dir** | Kullanılmadı. Yoksay veya false belirt |
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

- [Grupları & grupları kullanarak yetkilendirme ekleme ASP.NET Core bir Web uygulamasına talepler (kod örneği)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md)
- [Kurumsal uygulamaya Kullanıcı veya Grup atama](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)
- [Rol taleplerini yapılandırma](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
