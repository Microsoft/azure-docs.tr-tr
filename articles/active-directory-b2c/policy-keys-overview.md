---
title: İlke anahtarlarına genel bakış-Azure Active Directory B2C
description: Belirteçleri, istemci gizli dizilerini, sertifikaları ve parolaları imzalamak ve doğrulamak için Azure Active Directory B2C kullanılabilecek şifreleme ilkesi anahtarlarının türleri hakkında bilgi edinin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a41717e9be0918dead9f77a5f5472494d734b38a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717540"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 'deki ilke anahtarlarına genel bakış

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C), tümleştiği hizmetlerle güven sağlamak için gizli dizileri ve sertifikaları ilke anahtarları biçiminde depolar. Bu güvenler aşağıdakilerden oluşur:

- Harici kimlik sağlayıcıları
- [REST API hizmetleriyle](restful-technical-profile.md) bağlantı kurma
- Belirteç imzalama ve şifreleme

 Bu makalede, Azure AD B2C tarafından kullanılan ilke anahtarları hakkında bilmeniz gerekenler açıklanmaktadır.

> [!NOTE]
> Şu anda, ilke anahtarlarının yapılandırması yalnızca [özel ilkelerle](./user-flow-overview.md) sınırlıdır.

**İlke anahtarları** menüsündeki Azure Portal hizmetler arasında güven oluşturmak için gizli dizileri ve sertifikaları yapılandırabilirsiniz. Anahtarlar simetrik veya asimetrik olabilir. *Simetrik* şifreleme veya özel anahtar şifrelemesi, verileri şifrelemek ve şifrelerini çözmek için paylaşılan bir gizliliğin kullanıldığı yerdir. *Asimetrik* şifreleme veya ortak anahtar şifrelemesi, yalnızca Azure AD B2C bilinen bağlı olan taraf uygulamasıyla ve özel anahtarlarla paylaşılan ortak anahtarlardan oluşan, anahtar çiftleri kullanan bir şifreleme sistemidir.

## <a name="policy-keyset-and-keys"></a>İlke anahtar kümesi ve anahtarları

Azure AD B2C içindeki ilke anahtarlarına yönelik en üst düzey kaynak, **anahtar kümesi** kapsayıcısıdır. Her anahtar kümesi en az bir **anahtar** içerir. Anahtar aşağıdaki özniteliklere sahiptir:

| Öznitelik |  Gerekli | Açıklamalar |
| --- | --- |--- |
| `use` | Yes | Kullanım: ortak anahtarın amaçlanan kullanımını tanımlar. Verileri şifreleme `enc` veya veri üzerindeki imzayı doğrulama `sig` .|
| `nbf`| No | Etkinleştirme tarihi ve saati. |
| `exp`| No | Sona erme tarihi ve saati. |

Anahtar etkinleştirme ve süre sonu değerlerini PKI standartlarınıza göre ayarlamayı öneririz. Güvenlik veya ilke nedenleriyle bu sertifikaları düzenli aralıklarla döndürmenize gerek duyabilirsiniz. Örneğin, tüm sertifikalarınızı her yıl döndürmek için bir ilkenize sahip olabilirsiniz.

Bir anahtar oluşturmak için aşağıdaki yöntemlerden birini seçebilirsiniz:

- **El ile** -tanımladığınız dizeyle bir gizli dizi oluşturun. Gizli dizi bir simetrik anahtardır. Etkinleştirme ve sona erme tarihlerini ayarlayabilirsiniz.
- **Oluşturuldu** -bir anahtarı otomatik olarak oluştur. Etkinleştirme ve sona erme tarihlerini ayarlayabilirsiniz. İki seçenek vardır:
  - **Gizli** -simetrik anahtar oluşturur.
  - **RSA** -bir anahtar çifti (asimetrik anahtarlar) oluşturur.
- **Karşıya yükle** -bir sertifikayı veya bir PKCS12 anahtarını karşıya yükleyin. Sertifikanın özel ve ortak anahtarları (asimetrik anahtarlar) içermesi gerekir.

## <a name="key-rollover"></a>Anahtar geçişi

Güvenlik nedeniyle, Azure AD B2C anahtarları düzenli aralıklarla veya acil durumda anında alabilir. Azure AD B2C ile tümleştirilen herhangi bir uygulama, kimlik sağlayıcısı veya REST API, ne sıklıkta gerçekleşebileceğini önemli bir şekilde bir anahtar rollover olayını işleyecek şekilde hazırlanmalıdır. Aksi takdirde, uygulamanız veya Azure AD B2C şifreleme işlemi gerçekleştirmek için süre sonu bir anahtar kullanmayı denerse, oturum açma isteği başarısız olur.

Azure AD B2C anahtar kümesi birden fazla anahtara sahipse, aşağıdaki ölçütlere göre her bir anahtardan yalnızca biri etkin olur:

- Anahtar etkinleştirme, **etkinleştirme tarihini** temel alır.
  - Anahtarlar etkinleştirme tarihine göre artan sırada sıralanır. Etkinleştirme tarihleri daha sonra ileride daha sonra gelen anahtarlar listede daha düşüktür. Etkinleştirme tarihi olmayan anahtarlar listenin en altında bulunur.
  - Geçerli tarih ve saat bir anahtarın etkinleştirme tarihinden daha büyükse, Azure AD B2C anahtarı etkinleştirir ve önceki etkin anahtarı kullanmayı durdurur.
- Geçerli anahtarın sona erme saati geçtiğinde ve anahtar kapsayıcısı, geçerli *olmayan* ve *süresi* dolmadan yeni bir anahtar içeriyorsa, yeni anahtar otomatik olarak etkin hale gelir.
- Geçerli anahtarın sona erme süresi geçtiğinde ve anahtar kapsayıcısı, geçerli *olmayan* ve *süresi* dolmadan yeni bir *anahtar içermiyorsa,* Azure AD B2C süresi doldu anahtarını kullanamaz. Azure AD B2C, özel ilkenizin bağımlı bir bileşeni içinde bir hata mesajı oluşturacak. Bu sorundan kaçınmak için, etkinleştirme ve sona erme tarihleri için bir güvenlik ağı olarak varsayılan bir anahtar oluşturabilirsiniz.
- OpenID Connect iyi bilinen yapılandırma uç noktasının anahtarın uç noktası (JWKS URI), anahtar, [Jwtissuer Technical profile](./jwt-issuer-technical-profile.md)içinde başvuruluyorsa, anahtar kapsayıcısında yapılandırılan anahtarları yansıtır. OıDC kitaplığı kullanan bir uygulama, belirteçleri doğrulamak için doğru anahtarları kullandığından emin olmak üzere bu meta verileri otomatik olarak getirecek. Daha fazla bilgi için [Microsoft kimlik doğrulama kitaplığı](../active-directory/develop/msal-b2c-overview.md)'nı nasıl kullanacağınızı öğrenin. Bu, her zaman en son belirteç imzalama anahtarlarını otomatik olarak getirir.

## <a name="policy-key-management"></a>İlke anahtarı yönetimi

Bir anahtar kapsayıcısı içinde geçerli etkin anahtarı almak için Microsoft Graph API [getActiveKey](/graph/api/trustframeworkkeyset-getactivekey) uç noktasını kullanın.

İmzalama ve şifreleme anahtarları eklemek veya silmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. Genel Bakış sayfasında, **ilkeler** altında **kimlik deneyimi çerçevesi**' ni seçin.
1. **Ilke anahtarlarını** seçin 
    1. Yeni bir anahtar eklemek için **Ekle**' yi seçin.
    1. Yeni bir anahtarı kaldırmak için, anahtarı seçin ve **Sil**' i seçin. Anahtarı silmek için, silinecek anahtar kapsayıcısının adını yazın. Azure AD B2C, anahtarı silecek ve son ek. bak ile anahtarın bir kopyasını oluşturacak.

### <a name="replace-a-key"></a>Bir anahtarı değiştirme

Anahtar kümesi içindeki anahtarlar değiştirilebilir veya kaldırılabilir. Var olan bir anahtarı değiştirmeniz gerekiyorsa:

- **Etkinleştirme tarihi** geçerli tarih ve saate ayarlanmış olarak yeni bir anahtar eklemeniz önerilir. Azure AD B2C, yeni anahtarı etkinleştirir ve önceki etkin anahtarı kullanmayı durdurur.
- Alternatif olarak, doğru anahtarlarla yeni bir anahtar kümesi de oluşturabilirsiniz. Yeni anahtar kümesi kullanmak için ilkenizi güncelleştirin ve ardından eski anahtar kümesi kaldırın. 

## <a name="next-steps"></a>Sonraki adımlar

- [Anahtar kümesi](microsoft-graph-operations.md#trust-framework-policy-keyset) ve [ilke anahtarları](microsoft-graph-operations.md#trust-framework-policy-key) dağıtımını otomatik hale getirmek için Microsoft Graph nasıl kullanacağınızı öğrenin.

::: zone-end
