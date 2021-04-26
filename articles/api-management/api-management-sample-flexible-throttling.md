---
title: Azure API Management Gelişmiş istek azaltma
description: Azure API Management ile esnek kota ve hız sınırlaması ilkeleri oluşturmayı ve uygulamayı öğrenin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 3f91ed2238a7a23b967cd3e3556c8ade88af0044
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449741"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Azure API Management Gelişmiş istek azaltma
Gelen istekleri kısıtlayamaz, Azure API Management 'nin önemli bir rolü bulunur. İsteklerin oranını veya aktarılan toplam istek/veri sayısını denetleyerek API Management, API sağlayıcılarının API 'Lerini uygunsuz bir şekilde korumasına ve farklı API ürün katmanları için değer oluşturmasına izin verir.

## <a name="rate-limits-and-quotas"></a>Oran limitleri ve kotaları
Hız sınırları ve Kotalar farklı amaçlar için kullanılır.

### <a name="rate-limits"></a>Hız sınırları
Hız sınırları genellikle kısa ve yoğun hacimlere karşı korumak için kullanılır. Örneğin, arka uç hizmetinizin veritabanında yüksek bir çağrı hacminde performans sorunu olduğunu biliyorsanız, `rate-limit-by-key` Bu ayarı kullanarak yüksek çağrı hacmine izin vermiyor olarak bir ilke ayarlayabilirsiniz.

### <a name="quotas"></a>Kotalar
Kotalar genellikle daha uzun bir süre boyunca çağrı hızlarını denetlemek için kullanılır. Örneğin, belirli bir abonenin belirli bir ay içinde yapabilecekleri Toplam çağrı sayısını ayarlayabilirler. API 'nizi paraya çevirmek için, Kotalar katman tabanlı abonelikler için de farklı şekilde ayarlanabilir. Örneğin, temel bir katman aboneliği ayda 10.000 ' den fazla çağrı yapamaz, ancak Premium bir katman her ay 100.000.000 ' e kadar çağrıya gidebilir.

Azure API Management içinde, hız sınırları genellikle, ani artışlar korumak için düğümler genelinde daha hızlı yayılır. Buna karşılık kullanım kotası bilgileri daha uzun bir dönem boyunca kullanılır ve bu nedenle uygulamasının uygulanması farklıdır.

> [!CAUTION]
> Daraltma mimarisinin dağıtılmış doğası nedeniyle, hız sınırlaması hiçbir şekilde tamamen doğru değildir. Yapılandırılan ve gerçek izin verilen istek sayısı arasındaki fark, istek hacmi ve hızı, arka uç gecikmesi ve diğer faktörlere göre değişiklik gösterir.

## <a name="product-based-throttling"></a>Ürün tabanlı azaltma
Belirli bir abonelik kapsamındaki hız azaltma özellikleri API sağlayıcının API 'sini kullanmak üzere kaydolan geliştiriciler üzerinde sınırlar uygulaması için yararlıdır. Bununla birlikte, örneğin, API 'nin tek tek son kullanıcılarını azaltmasına yardımcı değildir. Geliştirici uygulamasının tek bir kullanıcısının tüm kotayı tüketmesi ve ardından geliştiricilerin diğer müşterilerinin uygulamayı kullanmasını engellemek mümkündür. Ayrıca, yüksek hacimli talepler üreten birkaç müşteri, zaman zaman kullanıcılarına erişimi sınırlayabilir.

## <a name="custom-key-based-throttling"></a>Özel anahtar tabanlı azaltma

> [!NOTE]
> `rate-limit-by-key`Ve `quota-by-key` Ilkeleri, Azure API Management tüketim katmanında kullanılabilir değildir. 

Anahtar- [sınır](./api-management-access-restriction-policies.md#LimitCallRateByKey) ve [Kota-anahtar](./api-management-access-restriction-policies.md#SetUsageQuotaByKey) ilkeleri, trafik denetimine daha esnek bir çözüm sağlar. Bu ilkeler, trafik kullanımını izlemek için kullanılan anahtarları belirlemek için ifadeler tanımlamanızı sağlar. Bu yöntem, bir örnek ile en kolay şekilde gösterilmiştir. 

## <a name="ip-address-throttling"></a>IP adresi azaltma
Aşağıdaki ilkeler tek bir istemci IP adresini her dakikada yalnızca 10 çağrı ile kısıtlar ve ayda toplam 1.000.000 çağrı ve 10.000 kilobayt bant genişliğine sahiptir. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Internet üzerindeki tüm istemciler benzersiz bir IP adresi kullandıysanız, bu, kullanımı kullanıcıya sınırlayan etkili bir yol olabilir. Ancak, bir NAT cihazı aracılığıyla Internet 'e eriştiği için birden çok kullanıcının tek bir genel IP adresini paylaşmaları olasıdır. Buna rağmen, kimliği doğrulanmamış erişime izin veren API 'Ler için `IpAddress` en iyi seçenek olabilir.

## <a name="user-identity-throttling"></a>Kullanıcı kimliği azaltma
Bir son kullanıcının kimliği doğrulandıysa, kullanıcıyı benzersiz bir şekilde tanımlayan bilgiler temel alınarak bir daraltma anahtarı oluşturulabilir.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Bu örnek, yetkilendirme üst bilgisinin nasıl ayıklanacağını, bunu nesnesine nasıl dönüştüreceğiniz `JWT` ve kullanıcı tanımlamak için belirtecin konusunu kullanarak, bunu hız sınırlandırma anahtarı olarak kullanacak şekilde gösterir. Kullanıcı kimliği, `JWT` diğer taleplerden biri olarak ' de depolanıyorsa, bu değer bunun yerine kullanılabilir.

## <a name="combined-policies"></a>Birleşik ilkeler
Kullanıcı tabanlı kısıtlama ilkeleri abonelik tabanlı azaltma ilkelerinden daha fazla denetim sağlasa da, her iki özelliği de birleştiren bir değer vardır. Ürün abonelik anahtarına göre daraltma ([çağrı hızını aboneliğe göre sınırla](./api-management-access-restriction-policies.md#LimitCallRate) ve [kullanım kotasını aboneliğe göre ayarla](./api-management-access-restriction-policies.md#SetUsageQuota)), kullanım düzeylerine göre ÜCRETLENDIREREK bir API 'nin iç kullanımını etkinleştirmek için harika bir yoldur. Kullanıcı tarafından kısıtlama sağlayabilmesinin daha ayrıntılı bir denetimi, bir kullanıcının davranışının başka bir deneyim yaşamamasını önler. 

## <a name="client-driven-throttling"></a>İstemci odaklı daraltma
Daraltma anahtarı bir [ilke ifadesi](./api-management-policy-expressions.md)kullanılarak tanımlandığında, bu, azaltın kapsamı belirleme ŞEKLINI seçerek API sağlayıcısıdır. Ancak bir geliştirici kendi müşterilerinin nasıl hızlandıradığını denetlemek isteyebilir. Bu, geliştiricinin istemci uygulamasının anahtarı API ile iletişimine olanak tanımak için özel bir üst bilgi sunarak API sağlayıcısı tarafından etkinleştirilebilir.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Bu, geliştiricinin istemci uygulamasının hız sınırlaması anahtarını nasıl oluşturmak istediğini seçmesini sağlar. İstemci geliştiricileri kullanıcılara anahtar kümeleri ayırarak ve anahtar kullanımını döndürerek kendi hız katmanlarını oluşturabilir.

## <a name="summary"></a>Özet
Azure API Management, API hizmetinize hem koruma hem de değer ekleme için ücret ve kota azaltma sağlar. Özel kapsam kuralları olan yeni kısıtlama ilkeleri, müşterilerinizin daha iyi uygulamalar oluşturmasını sağlamak için bu ilkeler üzerinde daha ayrıntılı denetim imkanı sağlar. Bu makaledeki örneklerde, istemci IP adresleri, Kullanıcı kimliği ve istemci tarafından oluşturulan değerler ile anahtarları sınırlandırma ile ayırarak bu yeni ilkelerin kullanımı gösterilmektedir. Ancak, iletinin Kullanıcı Aracısı, URL yol parçaları, ileti boyutu gibi kullanılabilecek birçok farklı bölümü vardır.

## <a name="next-steps"></a>Sonraki adımlar
Lütfen bu konuda bir GitHub sorunu olarak bize geri bildirimde bulunun. Senaryolarınızda mantıklı bir seçenek olan diğer olası anahtar değerlerini öğrenmek harika olacaktır.
