---
title: Taleplerde Azure AD şema uzantısı özniteliklerini kullanma
titleSuffix: Microsoft identity platform
description: Belirteç taleplerindeki uygulamalara Kullanıcı verileri göndermek için dizin şema uzantısı özniteliklerinin nasıl kullanılacağını açıklar.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 0127c8d796126d1e99b1fa38a9506df477c7eb49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755740"
---
# <a name="using-directory-schema-extension-attributes-in-claims"></a>Taleplerde dizin şema uzantısı özniteliklerini kullanma

Dizin şeması uzantı öznitelikleri, kullanıcı nesnelerinde Azure Active Directory ek verileri ve gruplar, kiracı ayrıntıları, hizmet sorumluları gibi diğer dizin nesnelerini depolamak için bir yol sağlar.  Yalnızca Kullanıcı nesnelerindeki uzantı öznitelikleri, uygulamalara talepler aktarmak için kullanılabilir. Bu makalede, belirteç taleplerindeki uygulamalara Kullanıcı verileri göndermek için dizin şema uzantısı özniteliklerinin nasıl kullanılacağı açıklanır.

> [!NOTE]
> Microsoft Graph Graph nesnelerini özelleştirmek için iki diğer uzantı mekanizması sağlar. Bunlar Microsoft Graph Open uzantıları ve Microsoft Graph şema uzantıları olarak bilinir. Ayrıntılar için [Microsoft Graph belgelerine](/graph/extensibility-overview) bakın. Bu özellikleri kullanan Microsoft Graph nesnelerinde depolanan veriler, belirteçlerdeki talepler için kaynaklar olarak kullanılamaz.

Dizin şeması uzantı öznitelikleri her zaman Kiracıdaki bir uygulamayla ilişkilendirilir ve kendi adında uygulamanın *ApplicationId* 'si tarafından başvurulur.

Bir dizin şeması uzantısı özniteliği için tanımlayıcı *Extension_xxxxxxxxx_AttributeName* formundadır.  Burada *xxxxxxxxx* , uzantının tanımlandığı uygulamanın *ApplicationId* olduğu yerdir.

## <a name="registering-and-using-directory-schema-extensions"></a>Dizin şema uzantılarını kaydetme ve kullanma
Dizin şeması uzantı öznitelikleri, iki şekilde kaydedilebilir ve doldurulabilir:

- AD Connect 'i oluşturmak ve şirket içi AD 'den verileri onlarla eşitlemek üzere yapılandırarak. Bkz. [Azure AD Connect eşitleme dizini uzantıları](../hybrid/how-to-connect-sync-feature-directory-extensions.md).
- Kaydetmek için Microsoft Graph kullanarak, değerlerini ayarlayın ve [şema uzantılarından](/graph/extensibility-overview)oku. [PowerShell cmdlet 'leri](/powershell/azure/active-directory/using-extension-attributes-sample) de kullanılabilir.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-with-ad-connect"></a>AD Connect ile oluşturulan dizin şeması uzantısı özniteliklerinden gelen verilerle talepleri yayma
AD Connect kullanılarak oluşturulan ve eşitlenen Dizin şeması uzantı öznitelikleri, her zaman AD Connect tarafından kullanılan uygulama KIMLIĞIYLE ilişkilendirilir. Bunlar her ikisi de talepler için Portal Kullanıcı arabirimindeki **Kurumsal** uygulamalar yapılandırmasında bir kaynak olarak ve **Kurumsal uygulamalar** altında Galeri dışı uygulama yapılandırma deneyimi ve uygulama kayıt deneyimi aracılığıyla kaydedilen uygulamalar için bir talep eşleme ilkesi aracılığıyla kullanılabilir.  AD Connect aracılığıyla oluşturulan dizin uzantısı özniteliği dizinde olduğunda, SAML SSO talep yapılandırması Kullanıcı arabiriminde gösterilir.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-for-an-application-using-graph-or-powershell"></a>Graph veya PowerShell kullanarak bir uygulama için oluşturulan dizin şeması uzantı özniteliklerinden gelen verilerle talepleri yayma
Bir dizin şeması uzantısı özniteliği Microsoft Graph veya PowerShell kullanan bir uygulama için kayıtlıysa (bir uygulamalar ilk kurulumu veya örnek için sağlama adımı aracılığıyla), aynı uygulama, Kullanıcı oturum açtığında bir talep içindeki bir kullanıcı nesnesinden bu öznitelik içindeki verileri almak üzere Azure Active Directory yapılandırılabilir.  Uygulama, [isteğe bağlı talepler](active-directory-optional-claims.md#configuring-directory-extension-optional-claims)kullanılarak aynı uygulamada kayıtlı olan dizin şema uzantılarında veri alacak şekilde yapılandırılabilir.  Bunlar uygulama bildiriminde ayarlanabilir.  Bu, çok kiracılı bir uygulamanın, kendi kullanımı için Dizin şeması uzantısı özniteliklerini kaydetmesini sağlar. Uygulama bir kiracıya sağlandığında, ilişkili dizin şeması uzantıları söz konusu Kiracıdaki kullanıcılara ayarlanabilir ve tüketilebilir.  Kiracıda yapılandırıldıktan ve onay verildiğinde, verileri Graph aracılığıyla depolamak ve almak ve Microsoft kimlik platformunun uygulamalara yaydığı belirteçlerdeki taleplerle eşlemek için kullanılabilir.

Dizin şeması uzantı öznitelikleri, herhangi bir uygulama için kaydedilebilir ve doldurulabilir.

Bir uygulamanın farklı bir uygulamada kayıtlı bir uzantı özniteliğinden veri ile talepler gönderebilmesi gerekiyorsa, uzantı özniteliğini talebe eşlemek için bir [talep eşleme ilkesi](active-directory-claims-mapping.md) kullanılmalıdır.  Dizin şeması uzantısı özniteliklerinin yönetilmesi için ortak bir düzen, özellikle ihtiyacınız olan tüm şema uzantılarının kayıt noktası olmak üzere bir uygulama oluşturmaktır.  Gerçek bir uygulama olması gerekmez ve bu teknik, tüm uzantıların adında aynı uygulama KIMLIĞINE sahip olduğu anlamına gelir.

Örneğin, bir OAuth/OıDC belirtecindeki Dizin şeması uzantısı özniteliğinden tek bir talebi yayma için bir talep eşleme ilkesi aşağıda verilmiştir:

```json
{
    "ClaimsMappingPolicy": {
        "Version": 1,
        "IncludeBasicClaimSet": "false",
        "ClaimsSchema": [{
                "Source": "User",
                "ExtensionID": "extension_xxxxxxx_test",
                "JWTClaimType": "http://schemas.contoso.com/identity/claims/exampleclaim"
            },
        ]
    }
}
```

Burada *xxxxxxx* , uzantının KAYDEDILDIĞI uygulama kimliğidir.

> [!TIP]
> Nesnelerde dizin uzantısı öznitelikleri ayarlanırken büyük/küçük harf tutarlılığı önemlidir.  Uzantı öznitelik adları, ayarlandığında önemli değildir, ancak belirteç hizmeti tarafından dizinden okunduklarında büyük/küçük harfe duyarlıdır.  Bir uzantı özniteliği, "legacyıd" adlı bir kullanıcı nesnesi üzerinde ve "legacyıd" adlı başka bir kullanıcı nesnesi üzerinde ayarlandıysa, öznitelik "Legacyıd" adı kullanılarak bir talebe eşlendiğinde, veriler başarılı bir şekilde alınır ve talep ilk kullanıcı için belirtece dahil değildir, ikincisi değildir.
>
> Yerleşik dizin öznitelikleri için kullanılan talep şemasındaki "ID" parametresi, dizin uzantısı öznitelikleri için "extensionID" dir.

## <a name="next-steps"></a>Sonraki adımlar
- [SAML 2,0 ve JSON Web belirteçleri (JWT) belirteçlerine özel veya ek talepler eklemeyi](active-directory-optional-claims.md)öğrenin.
- [Belirli bir uygulama için belirteçlerde yayılan talepleri özelleştirmeyi](active-directory-claims-mapping.md)öğrenin.