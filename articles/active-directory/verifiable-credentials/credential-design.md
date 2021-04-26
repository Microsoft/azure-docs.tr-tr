---
title: Azure Active Directory doğrulanabilir kimlik bilgilerinizi özelleştirme (Önizleme)
description: Bu makalede kendi özel doğrulanabilir kimlik bilgilerinizi nasıl oluşturacağınız gösterilmektedir
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: a43e734c0a5bfa7c3698dcde5cb5b17f15575d90
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222937"
---
# <a name="how-to-customize-your-verifiable-credentials-preview"></a>Doğrulanabilir kimlik bilgilerinizi özelleştirme (Önizleme)

Doğrulanabilir kimlik bilgileri, kurallar ve görüntü dosyaları olmak üzere iki bileşenden oluşur. Kurallar dosyası, kullanıcının doğrulanabilir bir kimlik bilgisi almadan önce sağlaması gereken öğeleri belirler. Görüntüleme dosyası, taleplerin kimlik bilgisinin ve stil oluşturma markasını denetler. Bu kılavuzda, kuruluşunuzun gereksinimlerini karşılamak için her iki dosyayı da nasıl değiştireceğiniz açıklanacaktır. 

> [!IMPORTANT]
> Azure Active Directory doğrulanabilir kimlik bilgileri şu anda genel önizlemede.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="rules-file-requirements-from-the-user"></a>Kurallar dosyası: kullanıcıdan gereksinimler

Kurallar dosyası, doğrulanabilir kimlik bilgilerinin önemli özelliklerini açıklayan basit bir JSON dosyasıdır. Özellikle, taleplerin doğrulanabilir kimlik bilgilerinizi doldurmak için nasıl kullanıldığını açıklar.

Şu anda kural dosyasında yapılandırabileceğiniz üç giriş türü vardır. Bu türler, doğrulanabilir bir kimlik bilgisine talepler eklemek ve bu bilgilere yaptığınız bu bilgilere yönelik test yapmak için doğrulanabilir kimlik bilgisi veren hizmeti tarafından kullanılır. Açıklamaları olan üç tür aşağıda verilmiştir.

- KIMLIK belirteci
- Doğrulanabilir bir sunum aracılığıyla doğrulanabilir kimlik bilgileri.
- Self-Attested talepler

**Kimlik belirteci:** Örnek uygulama ve öğretici, KIMLIK belirtecini kullanır. Bu seçenek yapılandırıldığında, açık bir KIMLIK Connect yapılandırma URI 'SI sağlamanız ve VC 'ye dahil edilecek talepleri dahil etmeniz gerekir. Bu gereksinimi karşılamak ve ilgili talepleri hesabından eklemek için kullanıcıdan kimlik doğrulayıcı uygulamasında "oturum aç" istenir. 

**Doğrulanabilir kimlik bilgileri:** Bir verme akışının nihai sonucu doğrulanabilir bir kimlik bilgisi oluşturmak, ancak kullanıcıdan bir tane vermek için doğrulanabilir bir kimlik bilgisi sunmasını de isteyebilirsiniz. Kurallar dosyası, sunulan doğrulanabilen kimlik bilgilerinden belirli talepleri alabilir ve bu talepleri kuruluşunuzdan yeni verilen doğrulanabilir kimlik bilgilerine dahil edebilir. 

**Kendi kendine test edilmiş talepler:** Bu seçenek belirlendiğinde, Kullanıcı doğrudan kimlik doğrulayıcısına bilgi yazabilir. Şu anda dizeler, kendi kendine test edilmiş talepler için desteklenen tek giriştir. 

![doğrulanabilir kimlik bilgisi kartının ayrıntılı görünümü](media/credential-design/issuance-doc.png) 

**Statik talepler:** Ayrıca, kurallar dosyasında bir statik talep bildiriyoruz, ancak bu giriş kullanıcıdan gelmiyor. Veren, kurallar dosyasında bir statik talep tanımlar ve doğrulanabilir kimlik bilgilerinde başka herhangi bir talep gibi görünür. VC. Type öğesine sonra bir credentialSubject ekleyin ve özniteliği ve talebi bildirin. 

```json
"vc": {
    "type": [ "StaticClaimCredential" ],
    "credentialSubject": {
      "staticClaim": true,
      "anotherClaim": "Your Claim Here"
    },
  }
}
```


## <a name="input-type-id-token"></a>Giriş türü: KIMLIK belirteci

KIMLIK belirtecini giriş olarak almak için, kurallar dosyasının OıDC uyumlu kimlik sisteminin iyi bilinen uç noktasını yapılandırması gerekir. Bu sistemde, bir uygulamayı [veren hizmeti iletişim örneklerinden](issuer-openid.md)doğru bilgilerle kaydetmeniz gerekir. Ayrıca, client_id kural dosyasına konulması ve bir kapsam parametresinin doğru kapsamlar ile doldurulması gerekir. Örneğin, KIMLIK belirtecinde bir e-posta talebi döndürmek istiyorsanız Azure Active Directory e-posta kapsamına ihtiyaç duyuyor.
```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
            "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["https://schema.org/EducationalCredential", "https://schemas.ed.gov/universityDiploma2020", "https://schemas.contoso.edu/diploma2020" ]
      }
    }
```

| Özellik | Açıklama |
| -------- | ----------- |
| `attestations.idTokens` | Kullanıcı bilgilerinin kaynağını belirleme için desteklenen bir OpenID Connect Identity sağlayıcıları dizisi. |
| `...mapping` | Her KIMLIK belirtecindeki taleplerin, sonuçta elde edilen doğrulanabilir kimlik bilgilerinde özniteliklerle nasıl eşleştirildiğini açıklayan bir nesne. |
| `...mapping.{attribute-name}` | Elde edilen doğrulanabilir kimlik bilgilerinde doldurulması gereken öznitelik. |
| `...mapping.{attribute-name}.claim` | Özniteliği doldurmak için değeri kullanılacak olan KIMLIK belirteçlerinde talep. |
| `...configuration` | Kimlik sağlayıcınızın yapılandırma belgesinin konumu. Bu URL, [kimlik sağlayıcısı meta verileri Için OpenID Connect standardına](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata)bağlı olmalıdır. Yapılandırma belgesi,,, `issuer` `authorization_endpoint` `token_endpoint` ve `jwks_uri` alanlarını içermelidir. |
| `...client_id` | İstemci kayıt işlemi sırasında alınan istemci KIMLIĞI. |
| `...scope` | IDP 'nin KIMLIK belirtecine doğru talepleri döndürmesi gereken, boşlukla ayrılmış kapsamların bir listesi. |
| `...redirect_uri` | Her zaman değer kullanılmalıdır `vcclient://openid/` . |
| `validityInterval` | Doğrulanabilir kimlik bilgilerinizin ömrünü temsil eden saniye cinsinden süre. Bu süre dolduktan sonra doğrulanabilir kimlik bilgisi artık geçerli olmayacaktır. Bu değerin atlanması, açıkça iptal edilene kadar her doğrulanabilir kimlik bilgisinin geçerli kalacağı anlamına gelir. |
| `vc.type` | Doğrulanabilir kimlik bilgilerinizin karşıkullandığı şemayı belirten dizeler dizisi. Daha ayrıntılı bilgi için aşağıdaki bölüme bakın. |

### <a name="vctype-choose-credential-types"></a>VC. Type: kimlik bilgisi türlerini seçin 

Tüm doğrulanabilir kimlik bilgileri, kendi kural dosyaları içinde "tür" bilgisini bildirmelidir. Kimlik bilgisinin türü, diğer kuruluşlar tarafından verilen kimlik bilgilerinden doğrulanabilir kimlik bilgilerinizi ayırır ve verenler ve doğrulayıcılar arasında birlikte çalışabilirlik sağlar. Bir kimlik bilgisi türü belirtmek için, kimlik bilgisinin karşıkullandığı bir veya daha fazla kimlik bilgisi türü sağlamanız gerekir. Her tür benzersiz bir dize ile temsil edilir-genellikle genel benzersizlik sağlamak için bir URI kullanılır. URI 'nin adreslenebilir olması gerekmez; bir dize olarak kabul edilir. 

Örnek olarak, Contoso Üniversitesi tarafından verilen bir DIMA kimlik bilgisi aşağıdaki türleri bildirebilir:

| Tür | Amaç |
| ---- | ------- |
| `https://schema.org/EducationalCredential` | Contoso Üniversitesi tarafından verilen DISA 'nın, Schema. org nesnesinin nesnesi tarafından tanımlanan öznitelikleri içerdiğini bildirir `EducationaCredential` . |
| `https://schemas.ed.gov/universityDiploma2020` | Contoso Üniversitesi tarafından verilen sevenmas 'in eğitim Birleşik Devletler departmanı tarafından tanımlanan öznitelikleri içerdiğini bildirir. |
| `https://schemas.contoso.edu/diploma2020` | Contoso Üniversitesi tarafından verilen DISA 'nın Contoso University tarafından tanımlanan öznitelikleri içerdiğini bildirir. |

Üç tür de bildirerek, Contoso University 'in DISA 'ları, Doğrulayıcıları 'ndan farklı istekleri karşılamak için kullanılabilir. Bir banka, bir kullanıcıdan bir kümesini talep edebilir `EducationCredential` ve isteği karşılamak için dıloma kullanılabilir. Ancak Contoso Üniversitesi Alumnı Ilişkilendirmesi türünde bir kimlik bilgisi isteyebilir `https://schemas.contoso.edu/diploma2020` ve dıloma isteği de karşılanacak.

Kimlik bilgilerinizin birlikte çalışabilirliğini sağlamak için, sektörde kullanım için kimlik bilgisi türlerini, şemaları ve URI 'Leri tanımlamak üzere ilgili kuruluşlarla yakından çalışmanız önerilir. Birçok sektör gövdesi, doğrulanabilen kimlik bilgilerinin içeriğini tanımlamaya yönelik olarak yeniden kullanılabilen resmi belgelerin yapısına kılavuzluk sağlar. Ayrıca, kimlik bilgilerinizin Doğrulayıcıları ile birlikte çalışarak doğrulanabilir kimlik bilgilerinizi nasıl isteyeceğini ve tükettiğinizde öğrenin.

## <a name="input-type-verifiable-credential"></a>Giriş türü: doğrulanabilir kimlik bilgisi

>[!NOTE]
>Doğrulanabilir kimlik bilgileri isteyen kurallar, kimlik bilgilerini istemek için sunu değişim biçimini kullanmaz. Bu, veren hizmeti standart, kimlik bilgisi bildirimini desteklediğinde güncelleştirilecektir. 

```json
{
    "attestations": {
      "presentations": [
        {
          "mapping": {
            "first_name": {
              "claim": "$.vc.credentialSubject.firstName",
            },
            "last_name": {
              "claim": "$.vc.credentialSubject.lastName",
              "indexed": true
            }
          },
          "credentialType": "VerifiedCredentialNinja",
          "contracts": [
            "https://beta.did.msidentity.com/v1.0/3c32ed40-8a10-465b-8ba4-0b1e86882668/verifiableCredential/contracts/VerifiedCredentialNinja"
          ],
          "issuers": [
            {
              "iss": "did:ion:123"
            }
          ]
        }
      ]
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }
  ```

| Özellik | Açıklama |
| -------- | ----------- |
| `attestations.presentations` | Giriş olarak istenen doğrulanabilir kimlik bilgileri dizisi. |
| `...mapping` | Sunulan her bir doğrulanabilen kimlik bilgisi için taleplerin, sonuçta elde edilen doğrulanabilir kimlik bilgileri ile nasıl eşleştirildiğini açıklayan bir nesne. |
| `...mapping.{attribute-name}` | Elde edilen doğrulanabilir kimlik bilgilerinde doldurulması gereken öznitelik. |
| `...mapping.{attribute-name}.claim` | Özniteliği doldurmak için değeri kullanılan doğrulanabilir kimlik bilgilerinde talep. |
| `...mapping.{attribute-name}.indexed` | Yalnızca bir tane, iptal için kaydedilecek doğrulanabilir kimlik bilgileri başına etkinleştirilebilir. Daha fazla bilgi için lütfen [kimlik bilgisinin nasıl iptal edileceği hakkındaki makaleye](how-to-issuer-revoke.md) bakın. |
| `credentialType` | Kullanıcıdan sunmayı sormak istediğiniz doğrulanabilir kimlik bilgisinin credentialType 'ı. |
| `contracts` | Doğrulanabilen kimlik bilgisi hizmeti portalındaki sözleşmenin URI 'SI. |
| `issuers.iss` | Veren, kullanıcıdan istenmekte olan doğrulanabilir kimlik bilgileri için oldu. |
| `validityInterval` | Doğrulanabilir kimlik bilgilerinizin ömrünü temsil eden saniye cinsinden süre. Bu süre dolduktan sonra doğrulanabilir kimlik bilgisi artık geçerli olmayacaktır. Bu değerin atlanması, açıkça iptal edilene kadar her doğrulanabilir kimlik bilgisinin geçerli kalacağı anlamına gelir. |
| `vc.type` | Doğrulanabilir kimlik bilgilerinizin karşıkullandığı şemayı belirten dizeler dizisi. |


## <a name="input-type-self-attested-claims"></a>Giriş türü: kendi kendine test edilmiş talepler

Verme akışı sırasında, kullanıcıdan bazı kendi kendine sınanmış bilgiler girmesini istenebilir. Şu andan itibaren, tek giriş türü bir ' dize ' olur. 
```json
{
  "attestations": {
    "selfIssued": {
      "mapping": {
        "alias": {
          "claim": "name"
        }
      },
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }



```
| Özellik | Açıklama |
| -------- | ----------- |
| `attestations.selfIssued` | Kullanıcıdan giriş gerektiren bir kendi kendine verilen talepler dizisi. |
| `...mapping` | Kendi kendine verilen taleplerin, sonuçta elde edilen doğrulanabilir kimlik bilgilerinde özniteliklerle nasıl eşleştirildiğini açıklayan bir nesne. |
| `...mapping.alias` | Elde edilen doğrulanabilir kimlik bilgilerinde doldurulması gereken öznitelik. |
| `...mapping.alias.claim` | Özniteliği doldurmak için değeri kullanılan doğrulanabilir kimlik bilgilerinde talep. |
| `validityInterval` | Doğrulanabilir kimlik bilgilerinizin ömrünü temsil eden saniye cinsinden süre. Bu süre dolduktan sonra doğrulanabilir kimlik bilgisi artık geçerli olmayacaktır. Bu değerin atlanması, açıkça iptal edilene kadar her doğrulanabilir kimlik bilgisinin geçerli kalacağı anlamına gelir. |
| `vc.type` | Doğrulanabilir kimlik bilgilerinizin karşıkullandığı şemayı belirten dizeler dizisi. |


## <a name="display-file-verifiable-credentials-in-microsoft-authenticator"></a>Görüntü dosyası: Microsoft Authenticator doğrulanabilir kimlik bilgileri

Doğrulanabilir kimlik bilgileri, markanızı yansıtmak için kullanılabilecek sınırlı bir seçenek kümesi sunar. Bu makalede, kimlik bilgilerinizi nasıl özelleştireceğiniz ve kullanıcılara bir kez daha fazla bilgi sunan kimlik bilgilerini tasarlamak için en iyi yöntemler sağlanmaktadır.

Kullanıcılara verilen doğrulanabilir kimlik bilgileri Microsoft Authenticator kart olarak görüntülenir. Yönetici olarak, kuruluşunuzun markasını eşleştirmek için kart rengi, simge ve metin dizelerini seçebilirsiniz.

![verme belgeleri](media/credential-design/detailed-view.png) 

Kartlar, kullanıcıların kartın amacını, içerdiği öznitelikleri ve daha fazlasını bilmesini sağlamak için kullanabileceğiniz özelleştirilebilir alanları da içerir.

## <a name="create-a-credential-display-file"></a>Kimlik bilgisi görüntüleme dosyası oluşturma

Kurallar dosyasında çok benzer şekilde, görüntüleme dosyası, doğrulanabilir kimlik bilgilerinizin içeriklerinin Microsoft Authenticator uygulamasında nasıl görüntüleneceğini açıklayan basit bir JSON dosyasıdır. 

>[!NOTE]
> Şu anda, bu görüntüleme modeli yalnızca Microsoft Authenticator tarafından kullanılır.

Görüntüleme dosyası aşağıdaki yapıya sahiptir.

```json
{
    "default": {
      "locale": "en-US",
      "card": {
        "title": "University Graduate",
        "issuedBy": "Contoso University",
        "backgroundColor": "#212121",
        "textColor": "#FFFFFF",
        "logo": {
          "uri": "https://contoso.edu/images/logo.png",
          "description": "Contoso University Logo"
        },
        "description": "This digital diploma is issued to students and alumni of Contoso University."
      },
      "consent": {
        "title": "Do you want to get your digital diploma from Contoso U?",
        "instructions": "Please log in with your Contoso U account to receive your digital diploma."
      },
      "claims": {
        "vc.credentialSubject.name": {
          "type": "String",
          "label": "Name"
        }
      }
    }
}
```

| Özellik | Açıklama |
| -------- | ----------- |
| `locale` | Doğrulanabilir kimlik bilgisinin dili. Daha sonraki kullanımlar için ayrılmıştır. | 
| `card.title` | Kullanıcının kimlik bilgilerinin türünü görüntüler. En fazla 25 karakter uzunluğunda olması önerilir. | 
| `card.issuedBy` | Kullanıcıya veren kuruluşun adını görüntüler. Önerilen en fazla 40 karakter uzunluğu. |
| `card.backgroundColor` | Kartın arka plan rengini onaltılık biçimde belirler. Tüm kartlara hafif bir gradyan uygulanacak. |
| `card.textColor` | Kartın metin rengini onaltılık biçimde belirler. Siyah veya beyaz kullanmanız önerilir. |
| `card.logo` | Kartta görüntülenen bir logo. Belirtilen URL genel olarak adreslenebilir olmalıdır. Maksimum 36 piksel yükseklik ve telefon boyutundan bağımsız olarak en fazla 100 piksel genişliği önerilir. Saydam arka plan ile PNG önerilir. | 
| `card.description` | Her kartın yanında görünen ek metin. Herhangi bir amaçla kullanılabilir. Önerilen en fazla 100 karakter uzunluğu. |
| `consent.title` | Kart verildiğinde ek metin görüntülenir. Verme işlemiyle ilgili ayrıntıları sağlamak için kullanılır. Önerilen 100 karakter uzunluğu. |
| `consent.instructions` | Kart verildiğinde ek metin görüntülenir. Verme işlemiyle ilgili ayrıntıları sağlamak için kullanılır. Önerilen 100 karakter uzunluğu. |
| `claims` | Her kimlik bilgisinin içerdiği öznitelikler için Etiketler sağlamanıza olanak tanır. |
| `claims.{attribute}` | Etiketin geçerli olduğu kimlik bilgisinin özniteliğini gösterir. |
| `claims.{attribute}.type` | Öznitelik türünü gösterir. Şu anda yalnızca ' String ' öğesini destekliyoruz. |
| `claims.{attribute}.label` | Kimliği Doğrulayıcısı içinde görünecek olan özniteliği için etiket olarak kullanılması gereken değer. Bu, kurallar dosyasında kullanılan etiketten farklı olabilir. Önerilen en fazla 40 karakter uzunluğu. |

>[!NOTE]
  >Kurallar dosyasına bir talep dahil edilirse ve görüntü dosyasında atlanırsa, iki farklı deneyim türü vardır. İOS üzerinde, talep, yukarıdaki görüntüde gösterilen Ayrıntılar bölümünde gösterilmez, ancak Android 'de talep gösterilir.  

## <a name="next-steps"></a>Sonraki adımlar

Artık doğrulanabilir kimlik bilgisi tasarımını daha iyi anlayabiliyor ve gereksinimlerinizi karşılamak için kendinizinkini nasıl oluşturabileceğiniz hakkında daha fazla anlayışınız vardır.

- [Veren hizmeti iletişim örnekleri](issuer-openid.md)
