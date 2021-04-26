---
title: Geliştirdiğiniz Daemon uygulamalarında kimlik doğrulama ve yetkilendirme esnekliği artırma
titleSuffix: Microsoft identity platform
description: Microsoft Identity platformunu kullanarak Daemon uygulamasındaki kimlik doğrulama ve yetkilendirme esnekliğini artırma Kılavuzu
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: a7b8f893026bb96c8d768d2e6d07d0240ecb81fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724850"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>Geliştirdiğiniz Daemon uygulamalarında kimlik doğrulama ve yetkilendirme esnekliği artırma

Bu makalede, geliştiricilerin Microsoft Identity platformunu nasıl kullanabileceği ve arka plan uygulamaları esnekliği artırmak için Azure Active Directory yönergeler sunulmaktadır. Bu, arka plan süreçlerini, Hizmetleri, sunucu uygulamalarını ve Kullanıcı olmayan uygulamaları içerir.

![Microsoft Identity çağrısı yapan bir Daemon uygulaması](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>Azure kaynakları için Yönetilen kimlikler kullanma

Microsoft Azure Daemon uygulamaları geliştiren geliştiriciler, [Azure kaynakları Için Yönetilen kimlikler](../managed-identities-azure-resources/overview.md)kullanabilir. Yönetilen kimlikler, geliştiricilerin gizli dizileri ve kimlik bilgilerini yönetmesi gereksinimini ortadan kaldırır. Özelliği, sertifika süre sonu, döndürme hataları veya güvende hatalardan kaçınarak esnekliği geliştirir. Ayrıca, özellikle de esnekliği arttırmak için çeşitli yerleşik özellikler içerir.

Yönetilen kimlikler, mevcut belirtecin süresi dolmadan büyük bir süre içinde yeni belirteçleri önceden almak için Microsoft kimliğinden uzun süreli erişim belirteçleri ve bilgiler kullanır. Uygulamanız yeni bir belirteç alınmaya çalışırken çalışmaya devam edebilir.

Yönetilen kimlikler Ayrıca, bölge dışı hatalara karşı performansı ve esnekliği artırmak için bölgesel uç noktaları kullanır. Bölgesel bir uç nokta kullanılması, tüm trafiğin coğrafi bir alan içinde kalmasına yardımcı olur. Örneğin, Azure kaynağınız WestUS2 ise, Microsoft kimliği tarafından üretilen trafik dahil tüm trafiğin WestUS2 içinde kalması gerekir. Bu, hizmetinizin bağımlılıklarını birleştirerek olası hata noktalarını ortadan kaldırır.

## <a name="use-the-microsoft-authentication-library"></a>Microsoft kimlik doğrulama kitaplığı 'nı kullanma

Yönetilen kimlikleri kullanmayan Daemon uygulamalarının geliştiricileri, kimlik doğrulama ve yetkilendirmeyi basit hale getiren [Microsoft kimlik doğrulama kitaplığı 'nı (msal)](../develop/msal-overview.md)kullanabilir ve otomatik olarak esnekliği için en iyi yöntemleri kullanır. MSAL, gerekli Istemci kimlik bilgilerini sağlama işlemini daha kolay hale getirir. Örneğin, uygulamanızın sertifika tabanlı kimlik bilgilerini kullanırken JSON Web Token onayları oluşturma ve imzalama işlemlerini uygulaması gerekmez.

### <a name="use-microsoftidentityweb-for-net-developers"></a>.NET geliştiricileri için Microsoft. Identity. Web kullanma

ASP.NET Core Daemon uygulamaları oluşturan geliştiriciler [Microsoft. Identity. Web](../develop/microsoft-identity-web.md) kitaplığını kullanabilir. Bu kitaplık, yetkilendirme uygulamanın ASP.NET Core uygulamalar için daha da kolay hale getirmek için MSAL üzerine kurulmuştur. Bu, birden çok bölgede çalışabilen dağıtılmış uygulamalar için birkaç [Dağıtılmış belirteç önbellek](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache) stratejisi içerir.

## <a name="cache-and-store-tokens"></a>Önbellek ve mağaza belirteçleri

Kimlik doğrulama ve yetkilendirme uygulamak için MSAL kullanmıyorsanız, belirteçleri önbelleğe almak ve depolamak için bazı en iyi yöntemleri uygulayabilirsiniz. MSAL, bu en iyi uygulamaları otomatik olarak uygular ve izler.

Uygulama, uygulamayı korunan API 'Leri çağırmak üzere yetkilendirmek için bir kimlik sağlayıcısından belirteçleri alır. Uygulamanız belirteç aldığında, belirteçleri içeren yanıt, \_ uygulamayı ne kadar süre önbellekte ve ne zaman yeniden kullanacağınızı belirten bir "süresi doluyor" özelliği de içerir. Uygulamaların, \_ belirtecin kullanım süresini belirleyebilmek için "süre sonu" özelliğini kullanması önemlidir. Uygulamanın bir API erişim belirtecinin kodunu çözme girişimi hiçbir şekilde denenmelidir. Önbelleğe alınmış belirtecin kullanılması, uygulamanız ve Microsoft kimliğiniz arasında gereksiz trafiği önler. Kullanıcılarınız, söz konusu belirtecin yaşam süresinin uzunluğu için uygulamanızda oturum açmış durumda kalabilir.

## <a name="properly-handle-service-responses"></a>Hizmet yanıtlarını doğru şekilde işleyin

Son olarak, uygulamaların tüm hata yanıtlarını işlemesi, ancak esnekliği etkileyebilecek bazı yanıtlar vardır. Uygulamanız bir HTTP 429 yanıt kodu alırsa çok fazla Istek varsa, Microsoft kimliği isteklerinizi azaltmadır. Uygulamanız çok fazla istek yapmaya devam ederse, uygulamanızın belirteçleri almasına karşı kısıtlanmaya devam eder. Uygulamanız, "yeniden dene-After" yanıt alanı geçtikten sonra saniye olarak bir belirteci almayı denememelidir. 429 yanıtının alınması genellikle uygulamanın önbelleğe alınmadığını ve belirteçleri doğru şekilde yeniden desteklemediğinin göstergesidir. Geliştiriciler, belirteçlerin nasıl önbelleğe alınacağını ve uygulamada nasıl yeniden kullanıldığını incelemelidir.

Bir uygulama bir HTTP 5xx yanıt kodu aldığında, uygulamanın hızlı bir yeniden deneme döngüsü girmemelidir. Mevcut olduğunda, uygulama, 429 yanıtı için yaptığı gibi aynı "yeniden deneme" işlemesini dikkate almalıdır. Yanıt tarafından bir "yeniden deneme-sonrası" üst bilgisi sağlanmadıysa, yanıttan sonra ilk yeniden denemeye en az 5 saniye sonra bir üstel geri dönme işlemi uygulanması önerilir.

İstek zaman aşımına uğramadan uygulamalar hemen yeniden denenmemelidir. Yanıt alındıktan sonra ilk yeniden denemeden önce en az 5 saniye sonra bir üstel geri dönme işlemi uygulayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Esnekliği, oturum açma kullanıcılarına yönelik uygulamalar oluşturun](resilience-client-app.md)
- [Kimlik ve erişim yönetimi altyapınızda esnekliği oluşturma](resilience-in-infrastructure.md)
- [CıHAR sistemlerinizde derleme esnekliği](resilience-b2c.md)