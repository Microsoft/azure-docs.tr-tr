---
title: Sayfa düzeni seçin-Azure Active Directory B2C
description: Azure Active Directory B2C bir sayfa düzeni seçme hakkında bilgi edinin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 108d86e35422e1dc1d10aeb6b2c9488f5067232e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389680"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>Özel ilkeleri kullanarak Azure Active Directory B2C bir sayfa düzeni seçme

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Kullanıcı akışları veya özel ilkeler mi kullandığınızı Azure Active Directory B2C (Azure AD B2C) ilkeleriniz için JavaScript istemci tarafı kodunu etkinleştirebilirsiniz. Uygulamalarınız için JavaScript 'i etkinleştirmek üzere [özel ilkenize](active-directory-b2c-overview-custom.md)bir öğe eklemeniz, bir sayfa düzeni seçmeniz ve isteklerinizin [b2clogin.com](b2clogin.md) kullanmanız gerekir.

Sayfa düzeni, Azure AD B2C sağladığı öğelerin ve sağladığınız içeriğin bir ilişkidir.

Bu makalede, bir özel ilkede yapılandırarak Azure AD B2C bir sayfa düzeninin nasıl görüntüleneceği açıklanır.

> [!NOTE]
> Kullanıcı akışları için JavaScript 'ı etkinleştirmek istiyorsanız, bkz. [Azure Active Directory B2C JavaScript ve sayfa düzeni sürümleri](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>DataUri değerlerini Değiştir

Özel ilkeleriniz içinde, Kullanıcı yolculuğunda kullanılan HTML şablonlarını tanımlayan [ContentDefinitions](contentdefinitions.md) olabilir. **ContentDefinition** , Azure AD B2C tarafından sunulan sayfa öğelerine başvuran bir **datauri** içeriyor. **Loaduri** , sağladığınız HTML ve CSS içeriğinin göreli yoludur.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Bir sayfa düzeni seçmek için ilkeleriniz içindeki [ContentDefinitions](contentdefinitions.md) Içindeki **datauri** değerlerini değiştirirsiniz. Eski **veri URI** değerlerinden yeni değerlere geçiş yaparak, sabit bir paket seçiyorsunuz. Bu paketi kullanmanın avantajı, bu paketin değişmediğini ve sayfanızda beklenmeyen davranışlara neden olduğunu biliyoruz.

Sayfa düzeni ayarlamak için, **Datauri** değerlerini bulmak için aşağıdaki tabloyu kullanın.

| Eski DataUri değeri | Yeni DataUri değeri |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>Sürüm değişiklik günlüğü

Sayfa düzeni paketleri, sayfa öğelerinde düzeltmeler ve iyileştirmeler içerecek şekilde düzenli olarak güncelleştirilir. Aşağıdaki değişiklik günlüğü her sürümde tanıtılan değişiklikleri belirtir.

### <a name="120"></a>1.2.0 
- Tüm sayfalar
  - Erişilebilirlik düzeltmeleri
  - Artık CSS ve JavaScript 'in yükleme sırasını denetlemek için, HTML etiketlerinizi `data-preload="true"` özniteliğini ekleyebilirsiniz. Senaryolar şunlardır:
      - CSS 'yi, dosyaları yükleme arasında ' titreşimsiz ' olacak şekilde HTML 'niz ile aynı anda yüklemek için CSS bağlantılarınızın üzerinde kullanın
      - Bu öznitelik, sayfa yüklemeden önce betik etiketlerinizin getirilme sırasını denetlemenizi sağlar
  - E-posta alanı artık `type=email` ve mobil klavyeler doğru önerileri sağlar
  - Chrome çevirisi desteği
- Birleşik ve kendiliğinden onaylanan sayfa
  - Kullanıcı adı/e-posta ve parola alanları artık HTML öğesi formunu kullanır.  Bu, artık Edge ve IE 'nin bu bilgileri düzgün bir şekilde kaydetmesine izin verir
  
### <a name="110"></a>1.1.0

- Özel durum sayfası (globalexception)
  - Erişilebilirlik onarımı
  - İlkeden hiçbir kişi olmadığında varsayılan ileti kaldırılmıştır
  - Varsayılan CSS kaldırıldı
- MFA sayfası (çok faktörlü)
  - ' Kodu onayla ' düğmesi kaldırıldı
  - Kod için giriş alanı artık altı (6) karaktere kadar olan girişleri alır
  - Sayfa, 6 basamaklı bir kod girildiğinde, tıklanmasına gerek kalmadan, otomatik olarak girilen kodu doğrulamaya çalışır
  - Kod yanlışsa, giriş alanı otomatik olarak temizlenir
  - Üç (3) yanlış kodla çalıştıktan sonra, B2C bağlı olan tarafa geri bir hata gönderir
  - Erişilebilirlik düzeltmeleri
  - Varsayılan CSS kaldırıldı
- Otomatik olarak onaylanan sayfa (selfasted)
  - İptal uyarısı kaldırıldı
  - Hata öğeleri için CSS sınıfı
  - Hata mantığını gelişmiş göster/gizle
  - Varsayılan CSS kaldırıldı
- Birleşik SSP (unifiedssp)
  - Oturumumu Açık tut (KMSı) denetimi eklendi

### <a name="100"></a>1.0.0

- İlk yayın

## <a name="next-steps"></a>Sonraki adımlar

[Azure Active Directory B2C ' de özel bir ilke kullanarak uygulamanızın kullanıcı arabirimini özelleştirme](active-directory-b2c-ui-customization-custom.md)bölümünde uygulamalarınızın Kullanıcı arabirimini özelleştirme hakkında daha fazla bilgi edinin.
