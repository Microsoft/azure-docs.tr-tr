---
title: Azure Service Fabric ağı 'na giriş
description: Service Fabric ağı 'nda ağlar, ağ geçitleri ve akıllı trafik yönlendirmesi hakkında bilgi edinin.
author: georgewallace
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: ef8b40be366afe24b960f7419d84cc2a9976b592
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625710"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Service Fabric kafes uygulamalarında ağa giriş

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

Bu makalede, farklı türlerde yük dengeleyiciler, ağ geçitlerinin ağ uygulamalarınızı diğer ağlara nasıl bağlanacağı ve uygulamalardaki hizmetler arasında trafiğin nasıl yönlendirildiği açıklanmaktadır.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Katman 4 vs katman 7 yük dengeleyiciler
Yük Dengeleme, genellikle katman 4 ' te (L4) ve katman 7 ' de (L7) ağ için [OSI modelinde](https://en.wikipedia.org/wiki/OSI_model) farklı katmanlarda gerçekleştirilebilir.  Genellikle, iki tür yük dengeleyici vardır:

- Bir L4 yük dengeleyici, paketlerin içeriğiyle ilgili olmayan paketlerin tesliminde ilgilenen ağ aktarım katmanında çalışmaktadır. Yük dengeleyici tarafından yalnızca paket üstbilgileri inceleniyor, bu nedenle dengeleme ölçütleri IP adresleriyle ve bağlantı noktalarıyla sınırlandırılır. Örneğin, bir istemci yük dengeleyiciye TCP bağlantısı yapar. Yük dengeleyici bağlantıyı sonlandırır (doğrudan SYN 'a yanıt vererek), bir arka uç seçer ve arka uca yeni bir TCP bağlantısı yapar (yeni bir SYN gönderir). Bir L4 yük dengeleyici tipik olarak yalnızca L4 TCP/UDP bağlantısı veya oturumu düzeyinde çalışır. Bu nedenle, Yük Dengeleme baytları etrafında yeniden yönlendirir ve aynı oturumdaki baytların aynı arka uçta de gezinmesini sağlar. L4 yük dengeleyici, taşınmakta olduğu baytların uygulama ayrıntılarının farkında değildir. Baytlar herhangi bir uygulama protokolü olabilir.

- Bir L7 yük dengeleyici, her bir paketin içeriğiyle ilgilenen uygulama katmanında çalışmaktadır. HTTP, HTTPS veya WebSockets gibi protokolleri anladığından paket içeriğini inceler. Bu, yük dengeleyiciye gelişmiş yönlendirme gerçekleştirme olanağı sunar. Örneğin, bir istemci yük dengeleyiciye tek bir HTTP/2 TCP bağlantısı yapar. Yük dengeleyici daha sonra iki arka uç bağlantısı yapmaya devam eder. İstemci yük dengeleyiciye iki HTTP/2 akışı gönderdiğinde, bir akış arka uca bir gönderilir ve iki akış ikiye uca gönderilir. Bu nedenle, büyük ölçüde farklı istek yükleri olan çoğullama istemcileri bile arka uçlarda verimli bir şekilde dengelenir. 

## <a name="networks-and-gateways"></a>Ağlar ve ağ geçitleri
[Service Fabric kaynak modelinde](service-fabric-mesh-service-fabric-resources.md), bir ağ kaynağı, bağımlılığı olarak başvurabilen bir uygulama veya hizmet kaynağıyla bağımsız olarak tek tek dağıtılabilir bir kaynaktır. İnternet 'e açık uygulamalarınız için bir ağ oluşturmak üzere kullanılır. Farklı uygulamalardan gelen birden çok hizmet aynı ağın parçası olabilir. Bu özel ağ Service Fabric tarafından oluşturulup yönetilir ve bir Azure sanal ağı (VNET) değildir. VNET bağlantısını etkinleştirmek ve devre dışı bırakmak için uygulamalar, ağ kaynağından dinamik olarak eklenebilir ve kaldırılabilir. 

Ağ Geçidi, iki ağı köprülemek için kullanılır. Ağ Geçidi kaynağı, gelişmiş HTTP (ler) uygulama yönlendirmesi için herhangi bir protokol ve L7 yönlendirmesi için L4 yönlendirmesi sağlayan bir [Envoy proxy](https://www.envoyproxy.io/) dağıtır. Ağ Geçidi, trafiği bir dış ağdan ağınıza yönlendirir ve trafiğin hangi hizmete yönlendirileceğini belirler.  Dış ağ, diğer Azure uygulamalarınıza ve kaynaklarınıza bağlanmanızı sağlayan açık bir ağ (temelde, genel İnternet) veya bir Azure sanal ağı olabilir. 

![Ağ ve ağ geçidi][Image1]

Ağ kaynağı ile oluşturulduğunda `ingressConfig` , ağ kaynağına genel BIR IP atanır. Genel IP, ağ kaynağının kullanım ömrüne bağlı olacaktır.

Bir kafes uygulaması oluşturulduğunda, var olan bir ağ kaynağına başvurmalıdır. Yeni genel bağlantı noktaları eklenebilir veya mevcut bağlantı noktaları giriş yapılandırmasından kaldırılabilir. Bir uygulama kaynağına başvuruyorsa bir ağ kaynağına yönelik silme işlemi başarısız olur. Uygulama silindiğinde, ağ kaynağı kaldırılır.

## <a name="next-steps"></a>Sonraki adımlar 
Service Fabric ağ hakkında daha fazla bilgi edinmek için genel bakışı okuyun:
- [Service Fabric kafese bakış](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png