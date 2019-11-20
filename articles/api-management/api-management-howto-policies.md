---
title: Azure API Management ilkeler | Microsoft Docs
description: API Management ilke oluşturmayı, düzenlemeyi ve yapılandırmayı öğrenin.
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
ms.openlocfilehash: c10939b50a66cd608d27a71f02d959fbc2380f59
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072316"
---
# <a name="policies-in-azure-api-management"></a>Azure API Management ilkeler

Azure API Management (APıM) ' de, ilkeler, yayımcının yapılandırma aracılığıyla API 'nin davranışını değiştirmesine olanak tanıyan güçlü bir yetenektir. İlkeler, bir API 'nin isteği veya yanıtı üzerinde sırayla yürütülen deyimler koleksiyonudur. Popüler deyimler, XML 'den JSON 'a biçim dönüştürmeyi ve bir geliştiriciden gelen çağrıların miktarını kısıtlamak için çağrı hızı sınırını içerir. Birçok daha fazla ilke, kutudan daha fazla kullanılabilir.

İlkeler, API tüketicisi ile yönetilen API arasında yer alan ağ geçidi içinde uygulanır. Ağ Geçidi tüm istekleri alır ve genellikle bunları temel API 'ye değiştirilmemiş şekilde iletir. Ancak bir ilke hem gelen istek hem de giden yanıtta değişiklik uygulayabilir.

İlke ifadeleri herhangi bir API Management ilkesinde, ilke aksini belirtmedikçe, öznitelik değerleri ya da metin değerleri olarak kullanılabilir. [Akışı denetle][Control flow] ve [Değişken ayarla][Set variable] gibi bazı ilkeler ilke ifadelerini temel alır. Daha fazla bilgi için bkz. [Gelişmiş ilkeler][Advanced policies] ve [İlke ifadeleri][Policy expressions].

## <a name="sections"> </a>İlke yapılandırmasını anlama

İlke tanımı, bir dizi gelen ve giden deyimleri açıklayan basit bir XML belgesidir. XML doğrudan tanım penceresinde düzenlenebilir. Sağ tarafta bir deyim listesi sağlanır ve geçerli kapsam için geçerli olan deyimler etkinleştirilir ve vurgulanır.

Etkin bir ifadeye tıkladığınızda, açıklama görünümündeki imlecin bulunduğu konuma uygun XML eklenir. 

> [!NOTE]
> Eklemek istediğiniz ilke etkinleştirilmemişse, bu ilke için doğru kapsamda olduğunuzdan emin olun. Her ilke açıklaması belirli kapsamlar ve ilke bölümlerinde kullanılmak üzere tasarlanmıştır. İlke bölümlerini ve bir ilkeye ait kapsamları gözden geçirmek için [Ilke başvurusunda][Policy Reference]bu ilkenin **kullanım** bölümünü denetleyin.
> 
> 

Yapılandırma `inbound` ,`backend`, ve`on-error`olarak bölünür. `outbound` Belirtilen ilke deyimlerinin serisi bir istek ve yanıt için sırayla yürütülür.

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

Bir isteğin işlenmesi sırasında bir `inbound`hata oluşursa,, `backend`, veya `outbound` bölümlerindeki kalan adımlar atlanır `on-error` ve yürütme bölümündeki deyimlere atlar. `on-error` Bölümüne ilke deyimleri yerleştirerek, `context.LastError` özelliğini kullanarak hatayı gözden geçirebilir, `set-body` ilkeyi kullanarak hata yanıtını inceleyebilir ve özelleştirebilir ve bir hata oluşursa ne olacağını yapılandırabilirsiniz. Yerleşik adımlar ve ilke deyimlerinin işlenmesi sırasında ortaya çıkabilecek hatalar için hata kodları vardır. Daha fazla bilgi için bkz. [API Management Ilkelerinde hata işleme](/azure/api-management/api-management-error-handling-policies).

## <a name="scopes"> </a>İlkeleri yapılandırma

İlkeleri yapılandırma hakkında daha fazla bilgi için bkz. [Ilkeleri ayarlama veya düzenleme](set-edit-policies.md).

## <a name="policy-reference"></a>İlke başvurusu

İlke deyimlerinin ve ayarlarının tam listesi için [ilke başvurusuna](api-management-policy-reference.md) bakın.

## <a name="policy-samples"></a>İlke örnekleri

Daha fazla kod örneği için bkz. [ilke örnekleri](policy-samples.md) .

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

Gelen istekleri belirtilen IP adreslerine kısıtlamak üzere yeni bir ifade eklemek için imleci `inbound` XML öğesinin içeriğinin içine yerleştirin ve **arayanın IP 'lerini kısıtla** bildirimine tıklayın.

![Kısıtlama ilkeleri][policies-restrict]

Bu, deyimin nasıl yapılandırılacağı hakkında rehberlik sağlayan `inbound` öğesine bir XML kod parçacığı ekler.

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
+ İlke deyimlerinin ve ayarlarının tam listesi için [Ilke başvurusu](api-management-policy-reference.md)
+ [İlke örnekleri](policy-samples.md)   

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
