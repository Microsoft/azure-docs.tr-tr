---
title: Azure ön kapısı-joker karakter etki alanları desteği
description: Bu makale, Azure ön kapısının özel etki alanları listesinde joker karakter etki alanlarını eşlemeyi ve yönetmeyi nasıl desteklediğini anlamanıza yardımcı olur.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 18504f1ed4200889b20c9608c9c0ad2c13c9aaa5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94425771"
---
# <a name="wildcard-domains"></a>Joker karakter etki alanları

Tepesinde etki alanları ve alt etki alanlarının yanı sıra, Azure ön kapılarınızın ön uç konaklarınıza veya özel etki alanlarına bir joker etki alanı da eşleyebilirsiniz. Azure ön Kapıınızın yapılandırmasında joker etki alanlarının olması, aynı yönlendirme kuralından bir API, uygulama veya Web sitesi için birden çok alt etki alanı için trafik yönlendirme davranışını basitleştirir. Her alt etki alanını ayrı olarak eklemek veya belirtmek için yapılandırmayı değiştirmeniz gerekmez. Örnek olarak,,, ve için yönlendirmeyi tanımlayabilir ve `customer1.contoso.com` `customer2.contoso.com` `customerN.contoso.com` aynı yönlendirme kuralını kullanarak joker karakter etki alanını ekleyebilirsiniz `*.contoso.com` .

Joker karakter etki alanları desteğiyle geliştirilmiş önemli senaryolar şunlardır:

- Her alt etki alanını Azure ön kapı profilinize eklemek ve ardından HTTPS 'yi her alt etki alanı için bir sertifika bağlamak üzere etkinleştirmek zorunda değilsiniz.
- Artık bir uygulama yeni bir alt etki alanı eklerse üretim Azure ön kapısının yapılandırmasını değiştirmeniz gerekli değildir. Daha önce, alt etki alanını eklemeniz, ona bir sertifika bağlamanız, bir Web uygulaması güvenlik duvarı (WAF) ilkesi bağlamanız ve ardından etki alanını farklı yönlendirme kurallarına eklemeniz gerekiyordu.

> [!NOTE]
> Şu anda, yalnızca API, PowerShell ve Azure CLı aracılığıyla Azure DNS aracılığıyla joker karakter alanları eklemek desteklenir. Azure portal joker etki alanlarını ekleme ve yönetme desteği kullanılamaz.

## <a name="adding-wildcard-domains"></a>Joker karakter etki alanları ekleme

Ön uç Konakları veya etki alanları için bölümün altına bir joker karakter etki alanı ekleyebilirsiniz. Alt etki alanlarına benzer şekilde Azure ön kapısı, joker etki alanınız için CNAME kaydı eşlemesinin bulunduğunu doğrular. Bu DNS eşlemesi, ile eşlenmiş gibi bir doğrudan CNAME kayıt eşlemesi olabilir `*.contoso.com` `contoso.azurefd.net` . Ya da afdverify geçici eşlemesini kullanabilirsiniz. Örneğin, `afdverify.contoso.com` eşlenmiş olarak, `afdverify.contoso.azurefd.net` joker karakter için CNAME kayıt haritasını doğrular.

> [!NOTE]
> Azure DNS joker kayıtlarını destekler.

Ön uç konaklarına kadar, ön uç konaklarının sınırına kadar, joker karakter etki alanının çok sayıda tek düzeyli alt etki alanı ekleyebilirsiniz. Bu işlev için gerekli olabilir:

- Diğer etki alanları için farklı bir rota tanımlama (joker etki alanından).

- Belirli bir alt etki alanı için farklı bir WAF ilkesi vardır. Örneğin, `*.contoso.com` `foo.contoso.com` etki alanı sahipliğini yeniden kanıtlamaya gerek kalmadan eklemeye izin verir. Ancak `foo.bar.contoso.com` , öğesinin tek düzey alt etki alanı olmadığından izin vermez `*.contoso.com` . `foo.bar.contoso.com`Ek etki alanı sahipliği doğrulaması olmadan eklemek için `*.bar.contosonews.com` eklenmesi gerekir.

Belirli sınırlamalara sahip joker karakter etki alanları ve alt etki alanları ekleyebilirsiniz:

- Bir Azure ön kapısı profiline bir joker karakter etki alanı eklenirse:
  - Joker karakter etki alanı başka hiçbir Azure ön kapı profiline eklenemez.
  - Joker karakter etki alanının ilk düzey alt etki alanı başka bir Azure ön kapısına veya Azure Content Delivery Network profiline eklenemiyor.
- Bir Azure ön kapısı profiline veya bir Azure Content Delivery Network profiline bir joker karakter etki alanının alt etki alanı zaten eklendiyse, joker etki alanı diğer Azure ön kapı profili için kullanılamaz.
- İki profil (Azure ön kapısı veya Azure Content Delivery Network), bir kök etki alanının çeşitli alt etki alanlarına sahip ise, joker karakter etki alanları profillerden birine eklenemez.

## <a name="certificate-binding"></a>Sertifika bağlama

Joker karakter etki alanında HTTPS trafiğini kabul etmek için, joker karakter etki alanında HTTPS 'yi etkinleştirmeniz gerekir. Joker bir etki alanı için sertifika bağlama joker bir sertifika gerektirir. Diğer bir deyişle, sertifikanın konu adının joker etki alanı da olmalıdır.

> [!NOTE]
> Şu anda yalnızca kendi özel SSL sertifikanızı kullanmak, joker karakter etki alanları için HTTPS 'yi etkinleştirmek üzere kullanılabilir. Azure ön kapısının yönetilen sertifikaları joker etki alanları için kullanılamaz.

Azure Key Vault veya alt etki alanları için Azure ön kapısının yönetilen sertifikalarından aynı joker sertifikayı kullanmayı seçebilirsiniz.

Zaten onunla ilişkili bir sertifikası olan bir joker etki alanı için bir alt etki alanı eklenirse, alt etki alanı için HTTPS 'yi devre dışı bırakabilirsiniz. Diğer etki alanı, farklı bir Key Vault veya Azure ön kapısıyla yönetilen sertifika tarafından geçersiz kılınmadığı takdirde, joker etki alanı için sertifika bağlamasını kullanır.

## <a name="waf-policies"></a>WAF ilkeleri

WAF ilkeleri, diğer etki alanlarına benzer şekilde joker etki alanlarına eklenebilir. Farklı bir WAF ilkesi, joker bir etki alanının alt etki alanına uygulanabilir. Alt etki alanları için, joker karakterle aynı ilke olsa bile, kullanılacak WAF ilkesini belirtmeniz gerekir. Alt etki alanları WAF ilkesini joker etki *alanından otomatik olarak almıyor.*

Bir alt etki alanı için WAF ilkesinin çalıştırılmasını istemiyorsanız, yönetilen veya özel RuleSets içermeyen boş bir WAF ilkesi oluşturabilirsiniz.

## <a name="routing-rules"></a>Yönlendirme kuralları

Bir yönlendirme kuralı yapılandırırken, ön uç ana bilgisayarı olarak bir joker karakter seçebilirsiniz. Joker karakterler ve alt etki alanları için farklı yönlendirme davranışlarına de sahip olabilirsiniz. [Azure ön kapısının eşleme Ile nasıl yol olduğu konusunda](front-door-route-matching.md)açıklandığı gibi, çalışma zamanında farklı yönlendirme kuralları genelinde etki alanı için en özel eşleşme seçilir.

> [!IMPORTANT]
> Yönlendirme kurallarınız genelinde eşleşen yol desenleriniz olmalıdır, aksi olarak istemcileriniz başarısız olur. Örneğin, Route 1 ( `*.foo.com/*` arka uç havuzu A ile eşlenmiş) ve Route 2 ( `/bar.foo.com/somePath/*` arka uç havuzu B ile eşlenmiş) gibi iki yönlendirme kuralı vardır. Ardından, için bir istek ulaşır `bar.foo.com/anotherPath/*` . Azure ön kapısı, daha belirli bir etki alanı eşleştirmesine göre Route 2 ' yi, yalnızca rotalar genelinde eşleşen yol desenleri bulmak için seçer.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ön kapı profili oluşturmayı](quickstart-create-front-door.md)öğrenin.
- [Azure ön kapısına özel etki alanı eklemeyi](front-door-custom-domain.md)öğrenin.
- [Özel bir etki alanında https 'yi etkinleştirmeyi](front-door-custom-domain-https.md)öğrenin.
