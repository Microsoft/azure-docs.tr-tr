---
title: API Management genel bakış ile Azure Service Fabric | Microsoft Docs
description: Bu makale, Azure API Management Service Fabric uygulamalarınıza yönelik bir ağ geçidi olarak kullanılmasına giriş niteliğindedir.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 52f9584a2f793ff513100afcb7b7bd6acd2a4742
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900539"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric Azure API Management genel bakış

Bulut uygulamalarının normalde kullanıcılar, cihazlar ve diğer uygulamalara tek giriş noktası sağlamak için bir ön uç ağ geçidine ihtiyacı vardır. Service Fabric, bir ağ geçidi, [ASP.NET Core uygulama](service-fabric-reliable-services-communication-aspnetcore.md)veya [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/)veya [Azure API Management](https://docs.microsoft.com/azure/api-management/)gibi trafik girişi için tasarlanan başka bir hizmet gibi durum bilgisi olmayan herhangi bir hizmet olabilir.

Bu makale, Azure API Management Service Fabric uygulamalarınıza yönelik bir ağ geçidi olarak kullanılmasına giriş niteliğindedir. API Management, arka uç Service Fabric hizmetlerinize zengin bir yönlendirme kuralları kümesiyle API 'Ler yayımlamanıza olanak tanıyan doğrudan Service Fabric ile tümleşir. 

## <a name="availability"></a>Kullanılabilirlik

> [!IMPORTANT]
> Bu özellik, gerekli sanal ağ desteği nedeniyle API Management **Premium** ve **Geliştirici** katmanlarında kullanılabilir.

## <a name="architecture"></a>Mimari

Ortak bir Service Fabric mimarisi, HTTP API 'Leri kullanıma sunan arka uç hizmetlerine HTTP çağrıları yapan tek sayfalı bir Web uygulaması kullanır. [Service Fabric Başlarken örnek uygulaması](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) bu mimariye bir örnek gösterir.

Bu senaryoda, bir durum bilgisiz Web hizmeti, Service Fabric uygulamasına ağ geçidi olarak görev yapar. Bu yaklaşım, aşağıdaki diyagramda gösterildiği gibi arka uç hizmetlerine Proxy HTTP istekleri sağlayan bir Web hizmeti yazmanızı gerektirir:

![Azure API Management topolojisine genel bakış ile Service Fabric][sf-web-app-stateless-gateway]

Uygulamalar karmaşıklıkla büyüdükçe, sayısız arka uç hizmetlerinin önünde bir API sunması gereken ağ geçitlerini yapın. Azure API Management, iş akışı kuralları, erişim denetimi, hız sınırlandırma, izleme, olay günlüğü ve yanıt önbelleğe alma ile karmaşık API 'Leri, sizin bölüminizdeki en az çalışma ile işleyecek şekilde tasarlandı. Azure API Management, istek temelli API ağ geçidinizi yazmak zorunda kalmadan istekleri doğrudan Service Fabric arka uç hizmetlerine yönlendirmek için Service Fabric hizmet bulmayı, Bölüm çözünürlüğünü ve çoğaltma seçimini destekler. 

Bu senaryoda, Web Kullanıcı arabirimi hala bir Web hizmeti aracılığıyla sunulurken, HTTP API çağrıları aşağıdaki diyagramda gösterildiği gibi Azure API Management aracılığıyla yönetilir ve yönlendirilir:

![Azure API Management topolojisine genel bakış ile Service Fabric][sf-apim-web-app]

## <a name="application-scenarios"></a>Uygulama senaryoları

Service Fabric hizmetler durum bilgisiz ya da durum bilgisi olabilir ve üç düzenden biri kullanılarak bölümlenebilir: Singleton, INT-64 aralığı ve adlandırılmış. Hizmet uç noktası çözümlemesi, belirli bir hizmet örneğinin belirli bir bölümünü tanımlamayı gerektirir. Bir hizmetin uç noktasını çözümlerken, tek bir bölüm olması dışında hizmet örneği adının (örneğin, `fabric:/myapp/myservice`) yanı sıra hizmetin belirli bir bölümünün belirtilmesi gerekir.

Azure API Management, herhangi bir durum bilgisi olmayan hizmetler, durum bilgisi olan hizmetler ve herhangi bir bölümleme şeması ile birlikte kullanılabilir.

## <a name="send-traffic-to-a-stateless-service"></a>Durum bilgisi olmayan bir hizmete trafik gönder

En basit durumda, trafik durum bilgisi olmayan bir hizmet örneğine iletilir. Bunu başarmak için bir API Management işlemi, Service Fabric arka ucunda belirli bir durum bilgisi olmayan hizmet örneğiyle eşleşen bir Service Fabric arka ucu olan bir gelen işlem ilkesi içerir. Bu hizmete gönderilen istekler hizmetin rastgele bir örneğine gönderilir.

#### <a name="example"></a>Örnek
Aşağıdaki senaryoda, bir Service Fabric uygulaması, iç HTTP API 'sini kullanıma sunan `fabric:/app/fooservice`, adlı, durum bilgisi olmayan bir hizmet içerir. Hizmet örneği adı iyi bilinmektedir ve API Management gelen işlem ilkesinde doğrudan sabit kodlanmış olabilir. 

![Azure API Management topolojisine genel bakış ile Service Fabric][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Durum bilgisi olan bir hizmete trafik gönderme

Durum bilgisi olmayan hizmet senaryosuna benzer şekilde trafik, durum bilgisi olan bir hizmet örneğine iletilebilir. Bu durumda, bir API Management işlemi, bir isteği belirli bir *durum bilgisi* olan hizmet örneğinin belirli bir bölümüne eşleyen Service Fabric arka ucuna sahip bir gelen işlem ilkesi içerir. Her bir isteğin eşlenecek bölüm, URL yolundaki bir değer gibi gelen HTTP isteğinden bazı girişler kullanılarak bir lambda yöntemi aracılığıyla hesaplanır. İlke, istekleri yalnızca birincil çoğaltmaya veya okuma işlemleri için rastgele bir çoğaltmaya gönderecek şekilde yapılandırılmış olabilir.

#### <a name="example"></a>Örnek

Aşağıdaki senaryoda bir Service Fabric uygulaması, iç HTTP API 'sini kullanıma sunan adlandırılmış `fabric:/app/userservice` bir durum bilgisi olmayan hizmet içerir. Hizmet örneği adı iyi bilinmektedir ve API Management gelen işlem ilkesinde doğrudan sabit kodlanmış olabilir.  

Hizmet, iki bölümden oluşan Int64 bölüm şeması ve ile yayılan `Int64.MinValue` `Int64.MaxValue`bir anahtar aralığı kullanılarak bölümlenir. Arka uç ilkesi, burada, Bölüm anahtarını hesaplamak için herhangi bir algoritma kullanılabilse de `id` , URL isteği yolunda belirtilen değeri 64 bitlik bir tamsayıya dönüştürerek bu aralıktaki bir bölüm anahtarını hesaplar. 

![Azure API Management topolojisine genel bakış ile Service Fabric][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Birden çok durum bilgisi olmayan hizmete trafik gönderme

Daha Gelişmiş senaryolarda istekleri birden fazla hizmet örneğiyle eşleştiren bir API Management işlemi tanımlayabilirsiniz. Bu durumda, her işlem, istek URL yolu veya sorgu dizesi ve durum bilgisi olan hizmetler söz konusu olduğunda, hizmet örneği içindeki bir bölüm gibi, istekleri belirli bir hizmet örneğiyle eşleyen bir ilke içerir. 

Bunu başarmak için bir API Management işlemi, gelen HTTP isteğinden alınan değerlere göre Service Fabric arka uçta durum bilgisi olmayan bir hizmet örneğiyle eşleşen bir Service Fabric arka ucu olan bir gelen işlem ilkesi içerir. Hizmet istekleri, hizmetin rastgele bir örneğine gönderilir.

#### <a name="example"></a>Örnek

Bu örnekte, aşağıdaki formül kullanılarak dinamik olarak oluşturulan bir ada sahip bir uygulamanın her kullanıcısı için yeni bir durum bilgisi olmayan hizmet örneği oluşturulur:
 
- `fabric:/app/users/<username>`

  Her hizmetin benzersiz bir adı vardır, ancak hizmetler Kullanıcı veya yönetici girişine yanıt olarak oluşturulduğundan ve bu nedenle APIM ilkelerine veya yönlendirme kurallarına sabit olarak kodlanamadığından bilinen adlar bilinmez. Bunun yerine, bir isteğin gönderileceği hizmetin adı, arka uç ilke tanımında `name` URL istek yolunda belirtilen değerden oluşturulur. Örneğin:

  - Hizmet örneğine yönlendirilme isteği `/api/users/foo``fabric:/app/users/foo`
  - Hizmet örneğine yönlendirilme isteği `/api/users/bar``fabric:/app/users/bar`

![Azure API Management topolojisine genel bakış ile Service Fabric][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Birden çok durum bilgisi olan hizmete trafik gönderme

Durum bilgisi olmayan hizmet örneğine benzer şekilde, API Management bir işlem istekleri birden fazla **durum bilgisi olan** hizmet örneğiyle eşleyebilir, bu durumda durum bilgisi olan her hizmet örneği için de bölüm çözümlemesi gerçekleştirmeniz gerekebilir.

Bunu başarmak için bir API Management işlemi, gelen HTTP isteğinden alınan değerlere göre Service Fabric arka uçta durum bilgisi olan bir hizmet örneğiyle eşleşen bir Service Fabric arka ucu olan bir gelen işlem ilkesi içerir. İsteği belirli bir hizmet örneğine eşlemenin yanı sıra, istek aynı zamanda hizmet örneği içindeki belirli bir bölüme ve isteğe bağlı olarak birincil çoğaltmaya ya da bölüm içindeki bir rastgele ikincil çoğaltmaya eşleştirilebilir.

#### <a name="example"></a>Örnek

Bu örnekte, aşağıdaki formül kullanılarak dinamik olarak oluşturulan bir ada sahip uygulamanın her kullanıcısı için yeni bir durum bilgisi olan hizmet örneği oluşturulur:
 
- `fabric:/app/users/<username>`

  Her hizmetin benzersiz bir adı vardır, ancak hizmetler Kullanıcı veya yönetici girişine yanıt olarak oluşturulduğundan ve bu nedenle APIM ilkelerine veya yönlendirme kurallarına sabit olarak kodlanamadığından bilinen adlar bilinmez. Bunun yerine, bir isteğin gönderileceği hizmetin adı, arka uç ilke tanımında `name` URL istek yolu sağlanmış değerden oluşturulur. Örneğin:

  - Hizmet örneğine yönlendirilme isteği `/api/users/foo``fabric:/app/users/foo`
  - Hizmet örneğine yönlendirilme isteği `/api/users/bar``fabric:/app/users/bar`

Her hizmet örneği aynı zamanda iki bölümden oluşan Int64 bölüm şeması ve ile yayılan `Int64.MinValue` `Int64.MaxValue`bir anahtar aralığı kullanılarak bölümlenir. Arka uç ilkesi, burada, Bölüm anahtarını hesaplamak için herhangi bir algoritma kullanılabilse de `id` , URL isteği yolunda belirtilen değeri 64 bitlik bir tamsayıya dönüştürerek bu aralıktaki bir bölüm anahtarını hesaplar. 

![Azure API Management topolojisine genel bakış ile Service Fabric][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Sonraki adımlar

API Management ve Flow istekleri ile API Management hizmetlere kadar ilk Service Fabric kümenizi ayarlamak için [öğreticiyi](service-fabric-tutorial-deploy-api-management.md) izleyin.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png