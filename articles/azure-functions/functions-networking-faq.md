---
title: Azure Işlevlerinde ağ hakkında sık sorulan sorular
description: Azure Işlevleri ile ağ iletişimi için en yaygın soruların ve senaryolardan bazılarına yanıtlar.
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.reviewer: glenga
ms.openlocfilehash: 24afeeee3207127bb9404156dc390433671dd5da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592311"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Azure Işlevlerinde ağ hakkında sık sorulan sorular

Bu makalede, Azure Işlevlerinde ağlarla ilgili sık sorulan sorular listelenmektedir. Daha kapsamlı bir genel bakış için bkz. [işlevler ağ seçenekleri](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Işlevlerde statik IP Nasıl yaparım? mı?

Bir App Service Ortamı işlevi dağıtmak, işlevleriniz için statik gelen ve giden IP adresleri sağlamanın birincil yoludur. App Service Ortamı kullanma hakkında ayrıntılı bilgi için, bir [App Service ortamı ile iç yük dengeleyici oluşturma ve kullanma](../app-service/environment/create-ilb-ase.md)makalesini başlatın.

Ayrıca, kontrol ettiğiniz bir genel IP adresi aracılığıyla giden trafiği yönlendirmek için bir sanal ağ NAT ağ geçidi kullanabilirsiniz. Daha fazla bilgi edinmek için bkz. [öğretici: Azure işlevleri gıden IP 'Yi Azure sanal ağ NAT ağ geçidiyle denetleme](functions-how-to-use-nat-gateway.md). 

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Nasıl yaparım? Internet erişimini işlevme kısıtlamak istiyor musunuz?

Internet erişimini birkaç yolla kısıtlayabilirsiniz:

* [IP kısıtlamaları](../app-service/app-service-ip-restrictions.md): IP aralığı ile işlev uygulamanıza gelen trafiği kısıtlayın.
    * IP kısıtlamaları ' nın altında, işlevinizi yalnızca belirli bir sanal ağdan gelen trafiği kabul edecek şekilde kısıtlayan [hizmet uç noktalarını](../virtual-network/virtual-network-service-endpoints-overview.md)de yapılandırabileceksiniz.
* Tüm HTTP Tetikleyicileri kaldırılıyor. Bazı uygulamalarda, yalnızca HTTP tetikleyicilerinin oluşmaması ve işlevinizin tetiklenmesi için başka herhangi bir olay kaynağının kullanılması yeterlidir.

Azure portal düzenleyicisinin çalışan işlevinize doğrudan erişim gerektirdiğini unutmayın. Azure portal üzerinden yapılan herhangi bir kod değişikliği, IP 'nin onaylanan listeye eklenmesini sağlamak için kullandığınız cihazın portala göz atamasını gerektirir. Ancak, ağ kısıtlamalarına sahip olan platform özellikleri sekmesinde herhangi bir şeyi kullanmaya devam edebilirsiniz.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>İşlev uygulamamı bir sanal ağ ile kısıtlıyor Nasıl yaparım??

[Hizmet uç noktalarını](./functions-networking-options.md#use-service-endpoints)kullanarak bir işlev uygulaması için **gelen** trafiği bir sanal ağ ile kısıtlayabileceksiniz. Bu yapılandırma, işlev uygulamasının internet 'e giden çağrılar yapmasına hala izin verir.

Tüm trafiğin bir sanal ağ üzerinden akmasını sağlayan bir işlevi tamamen kısıtlamak için, giden sanal ağ tümleştirmesiyle veya bir App Service Ortamı [Özel uç noktalar](./functions-networking-options.md#private-endpoint-connections) kullanabilirsiniz. Daha fazla bilgi için bkz. [Özel uç noktaları kullanarak Azure Işlevlerini Azure sanal ağı Ile tümleştirme](functions-create-vnet.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Bir işlev uygulamasından bir sanal ağdaki kaynaklara nasıl erişebilirim?

Sanal ağ tümleştirmesini kullanarak, çalışan bir işlevden bir sanal ağdaki kaynaklara erişebilirsiniz. Daha fazla bilgi için bkz. [sanal ağ tümleştirmesi](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Hizmet uç noktaları tarafından korunan Nasıl yaparım? erişim kaynakları?

Sanal ağ tümleştirmesini kullanarak, çalışan bir işlevden hizmet uç nokta korumalı kaynaklara erişebilirsiniz. Daha fazla bilgi için bkz. [sanal ağ tümleştirmesi](functions-networking-options.md#virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Sanal ağdaki bir kaynaktan bir işlevi nasıl tetikleyebilirim?

[Hizmet uç noktaları](./functions-networking-options.md#use-service-endpoints) veya [Özel uç nokta bağlantıları](./functions-networking-options.md#private-endpoint-connections)KULLANıLARAK sanal bir ağdan http tetikleyicilerinin çağrılmasına izin verebilirsiniz. 

Ayrıca, işlev uygulamanızı bir Premium plana, App Service plana veya App Service Ortamı dağıtarak bir sanal ağdaki diğer tüm kaynaklardaki bir işlevi tetikleyebilirsiniz. Daha fazla bilgi için bkz. [http olmayan sanal ağ Tetikleyicileri](./functions-networking-options.md#virtual-network-triggers-non-http)

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>İşlev uygulamamı sanal bir ağda nasıl dağıtabilirim?

Bir App Service Ortamı dağıtım, bir sanal ağın içinde bir işlev uygulaması oluşturmanın tek yoludur. Bir App Service Ortamı iç yük dengeleyiciyi kullanma hakkında ayrıntılı bilgi için, bir [App Service ortamı ile iç yük dengeleyici oluşturma ve kullanma](../app-service/environment/create-ilb-ase.md)makalesini başlatın.

Sanal ağ kaynaklarına yalnızca tek yönlü erişiminizin olması veya daha az kapsamlı ağ yalıtımı olması gereken senaryolarda, bkz. [işlevlere genel bakış](functions-networking-options.md).

## <a name="next-steps"></a>Sonraki adımlar

Ağ ve işlevler hakkında daha fazla bilgi için: 

* [Sanal ağ tümleştirmesiyle çalışmaya başlama öğreticisini izleyin](./functions-create-vnet.md)
* [Azure Işlevleri 'ndeki ağ seçenekleri hakkında daha fazla bilgi edinin](./functions-networking-options.md)
* [App Service ve Işlevlerle sanal ağ tümleştirmesi hakkında daha fazla bilgi edinin](../app-service/web-sites-integrate-with-vnet.md)
* [Azure 'da sanal ağlar hakkında daha fazla bilgi edinin](../virtual-network/virtual-networks-overview.md)
* [App Service ortamlarla daha fazla ağ özelliği ve denetimi etkinleştirin](../app-service/environment/intro.md)
