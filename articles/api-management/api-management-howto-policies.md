---
title: Azure API Management ilkeler | Microsoft Docs
description: API Management ilke oluşturmayı, düzenlemeyi ve yapılandırmayı öğrenin. Kod örneklerine bakın ve kullanılabilir ek kaynakları görüntüleyin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 37ac6369790ed526fd923819558863ae84432aed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358125"
---
# <a name="policies-in-azure-api-management"></a>Azure API Management ilkeleri

Azure API Management (APıM) ' de, ilkeler, yayımcının yapılandırma aracılığıyla API 'nin davranışını değiştirmesine olanak tanıyan güçlü bir yetenektir. İlkeler, bir API 'nin isteği veya yanıtı üzerinde sırayla yürütülen deyimler koleksiyonudur. Popüler deyimler, XML 'den JSON 'a biçim dönüştürmeyi ve bir geliştiriciden gelen çağrıların miktarını kısıtlamak için çağrı hızı sınırını içerir. Birçok daha fazla ilke, kutudan daha fazla kullanılabilir.

İlkeler, API tüketicisi ile yönetilen API arasında yer alan ağ geçidi içinde uygulanır. Ağ Geçidi tüm istekleri alır ve genellikle bunları temel API 'ye değiştirilmemiş şekilde iletir. Ancak bir ilke hem gelen istek hem de giden yanıtta değişiklik uygulayabilir.

İlke ifadeleri herhangi bir API Management ilkesinde, ilke aksini belirtmedikçe, öznitelik değerleri ya da metin değerleri olarak kullanılabilir. [Akışı denetle][Control flow] ve [Değişken ayarla][Set variable] gibi bazı ilkeler ilke ifadelerini temel alır. Daha fazla bilgi için bkz. [Gelişmiş ilkeler][Advanced policies] ve [İlke ifadeleri][Policy expressions].

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>İlke yapılandırmasını anlama

İlke tanımı, bir dizi gelen ve giden deyimleri açıklayan basit bir XML belgesidir. XML doğrudan tanım penceresinde düzenlenebilir. Sağ tarafta bir deyim listesi sağlanır ve geçerli kapsam için geçerli olan deyimler etkinleştirilir ve vurgulanır.

Etkin bir ifadeye tıkladığınızda, açıklama görünümündeki imlecin bulunduğu konuma uygun XML eklenir. 

> [!NOTE]
> Eklemek istediğiniz ilke etkinleştirilmemişse, bu ilke için doğru kapsamda olduğunuzdan emin olun. Her ilke açıklaması belirli kapsamlar ve ilke bölümlerinde kullanılmak üzere tasarlanmıştır. İlke bölümlerini ve bir ilkeye ait kapsamları gözden geçirmek için [Ilke başvurusunda][Policy Reference]bu ilkenin **kullanım** bölümünü denetleyin.
> 
> 

Yapılandırma,, ve olarak `inbound` bölünür `backend` `outbound` `on-error` . Belirtilen ilke deyimlerinin serisi bir istek ve yanıt için sırayla yürütülür.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Bir isteğin işlenmesi sırasında bir hata oluşursa,, `inbound` `backend` , veya bölümlerindeki kalan adımlar `outbound` atlanır ve yürütme bölümündeki deyimlere atlar `on-error` . Bölümüne ilke deyimleri yerleştirerek `on-error` , özelliğini kullanarak hatayı gözden geçirebilir `context.LastError` , ilkeyi kullanarak hata yanıtını inceleyebilir ve özelleştirebilir `set-body` ve bir hata oluşursa ne olacağını yapılandırabilirsiniz. Yerleşik adımlar ve ilke deyimlerinin işlenmesi sırasında ortaya çıkabilecek hatalar için hata kodları vardır. Daha fazla bilgi için bkz. [API Management Ilkelerinde hata işleme](./api-management-error-handling-policies.md).

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>İlkeleri yapılandırma

İlkeleri yapılandırma hakkında daha fazla bilgi için bkz. [Ilkeleri ayarlama veya düzenleme](set-edit-policies.md).

## <a name="policy-reference"></a>İlke başvurusu

İlke deyimlerinin ve ayarlarının tam listesi için [ilke başvurusuna](./api-management-policies.md) bakın.

## <a name="policy-samples"></a>İlke örnekleri

Daha fazla kod örneği için bkz. [ilke örnekleri](./policy-reference.md) .

## <a name="examples"></a>Örnekler

### <a name="apply-policies-specified-at-different-scopes"></a>Farklı kapsamlarda belirtilen ilkeleri uygulama

Genel düzeyde bir ilke ve API için yapılandırılmış bir ilke varsa, her iki bu API her kullanıldığında her iki ilke de uygulanır. API Management, temel öğe aracılığıyla birleştirilmiş ilke deyimlerinin belirleyici sıralamasına izin verir. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

Yukarıdaki örnek ilke tanımında, `cross-domain` daha yüksek ilkelerden önce deyimin ardından `find-and-replace` ilke gelmelidir. 

### <a name="restrict-incoming-requests"></a>Gelen istekleri kısıtla

Gelen istekleri belirtilen IP adreslerine kısıtlamak üzere yeni bir ifade eklemek için imleci XML öğesinin içeriğinin içine yerleştirin `inbound` ve **arayanın IP 'lerini kısıtla** bildirimine tıklayın.

![Kısıtlama ilkeleri][policies-restrict]

Bu, `inbound` deyimin nasıl yapılandırılacağı hakkında rehberlik sağlayan öğesine BIR XML kod parçacığı ekler.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Gelen istekleri sınırlandırmak ve yalnızca 1.2.3.4 IP adresinden yalnızca birini kabul etmek için XML 'yi şöyle değiştirin:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Sonraki adımlar

İlkelerle çalışma hakkında daha fazla bilgi için bkz.

+ [API dönüştürme](transform-api.md)
+ İlke deyimlerinin ve ayarlarının tam listesi için [Ilke başvurusu](./api-management-policies.md)
+ [İlke örnekleri](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
