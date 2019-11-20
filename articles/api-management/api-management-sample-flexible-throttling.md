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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 467d9cee74567fc0d19031773415675ae7c51818
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066762"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Azure API Management Gelişmiş istek azaltma
Gelen istekleri kısıtlayamaz, Azure API Management 'nin önemli bir rolü bulunur. İsteklerin oranını veya aktarılan toplam istek/veri sayısını denetleyerek API Management, API sağlayıcılarının API 'Lerini uygunsuz bir şekilde korumasına ve farklı API ürün katmanları için değer oluşturmasına izin verir.

## <a name="product-based-throttling"></a>Ürün tabanlı azaltma
Tarih olarak, hız azaltma özellikleri Azure portal tanımlı belirli bir ürün aboneliğine kapsama sahip olacak şekilde sınırlandırılmıştır. Bu, API sağlayıcının API 'leri kullanmak üzere kaydolan geliştiriciler üzerinde sınırlar uygulaması için yararlıdır, ancak örneğin, API 'nin bireysel kullanıcılarını azaltmasına yardımcı değildir. Geliştirici uygulamasının tek bir kullanıcısının tüm kotayı tüketmesi ve ardından geliştiricilerin diğer müşterilerinin uygulamayı kullanmasını engellemek mümkündür. Ayrıca, yüksek hacimli talepler üreten birkaç müşteri, zaman zaman kullanıcılarına erişimi sınırlayabilir.

## <a name="custom-key-based-throttling"></a>Özel anahtar tabanlı azaltma

> [!NOTE]
> `rate-limit-by-key` Ve`quota-by-key` ilkeleri, Azure API Management tüketim katmanında kullanılabilir değildir. 

Yeni [hız-limit](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) ve [Kota-anahtar](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) ilkeleri, trafik denetimine daha esnek bir çözüm sağlar. Bu yeni ilkeler, trafik kullanımını izlemek için kullanılan anahtarları belirlemek için ifadeler tanımlamanızı sağlar. Bu yöntem, bir örnek ile en kolay şekilde gösterilmiştir. 

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

Internet üzerindeki tüm istemciler benzersiz bir IP adresi kullandıysanız, bu, kullanımı kullanıcıya sınırlayan etkili bir yol olabilir. Ancak, bir NAT cihazı aracılığıyla Internet 'e eriştiği için birden çok kullanıcının tek bir genel IP adresini paylaşmaları olasıdır. Buna rağmen, kimliği doğrulanmamış erişime `IpAddress` izin veren API 'ler için en iyi seçenek olabilir.

## <a name="user-identity-throttling"></a>Kullanıcı kimliği azaltma
Bir son kullanıcının kimliği doğrulandıysa, kullanıcıyı benzersiz bir şekilde tanımlayan bilgiler temel alınarak bir daraltma anahtarı oluşturulabilir.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Bu örnek, yetkilendirme üst bilgisinin nasıl ayıklanacağını, bunu `JWT` nesnesine nasıl dönüştüreceğiniz ve kullanıcı tanımlamak için belirtecin konusunu kullanarak, bunu hız sınırlandırma anahtarı olarak kullanacak şekilde gösterir. Kullanıcı kimliği, `JWT` diğer taleplerden biri olarak ' de depolanıyorsa, bu değer bunun yerine kullanılabilir.

## <a name="combined-policies"></a>Birleşik ilkeler
Yeni kısıtlama ilkeleri var olan daraltma ilkelerinden daha fazla denetim sağlamasına karşın, her iki özelliği de birleştiren bir değer vardır. Ürün abonelik anahtarına göre daraltma ([çağrı hızını aboneliğe göre sınırla](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) ve [kullanım kotasını aboneliğe göre ayarla](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)), kullanım düzeylerine göre ÜCRETLENDIREREK bir API 'nin iç kullanımını etkinleştirmek için harika bir yoldur. Kullanıcı tarafından kısıtlama sağlayabilmesinin daha ayrıntılı bir denetimi, bir kullanıcının davranışının başka bir deneyim yaşamamasını önler. 

## <a name="client-driven-throttling"></a>İstemci odaklı daraltma
Daraltma anahtarı bir [ilke ifadesi](/azure/api-management/api-management-policy-expressions)kullanılarak tanımlandığında, bu, azaltın kapsamı belirleme ŞEKLINI seçerek API sağlayıcısıdır. Ancak bir geliştirici kendi müşterilerinin nasıl hızlandıradığını denetlemek isteyebilir. Bu, geliştiricinin istemci uygulamasının anahtarı API ile iletişimine olanak tanımak için özel bir üst bilgi sunarak API sağlayıcısı tarafından etkinleştirilebilir.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Bu, geliştiricinin istemci uygulamasının hız sınırlaması anahtarını nasıl oluşturmak istediğini seçmesini sağlar. İstemci geliştiricileri kullanıcılara anahtar kümeleri ayırarak ve anahtar kullanımını döndürerek kendi hız katmanlarını oluşturabilir.

## <a name="summary"></a>Özet
Azure API Management hem koruma hem de API hizmetinize değer ekleme için fiyat ve teklif azaltma sağlar. Özel kapsam kuralları olan yeni kısıtlama ilkeleri, müşterilerinizin daha iyi uygulamalar oluşturmasını sağlamak için bu ilkeler üzerinde daha ayrıntılı denetim imkanı sağlar. Bu makaledeki örneklerde, istemci IP adresleri, Kullanıcı kimliği ve istemci tarafından oluşturulan değerler ile anahtarları sınırlandırma ile ayırarak bu yeni ilkelerin kullanımı gösterilmektedir. Ancak, iletinin Kullanıcı Aracısı, URL yol parçaları, ileti boyutu gibi kullanılabilecek birçok farklı bölümü vardır.

## <a name="next-steps"></a>Sonraki adımlar
Lütfen bu konuda bir GitHub sorunu olarak bize geri bildirimde bulunun. Senaryolarınızda mantıklı bir seçenek olan diğer olası anahtar değerlerini öğrenmek harika olacaktır.

