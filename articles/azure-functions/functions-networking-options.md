---
title: Azure Işlevleri ağ seçenekleri
description: Azure Işlevlerinde kullanılabilen tüm ağ seçeneklerine genel bakış.
author: alexkarcher-msft
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 4e55932d47389e09b135d571d0e000b9795e6edc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884958"
---
# <a name="azure-functions-networking-options"></a>Azure Işlevleri ağ seçenekleri

Bu makalede, Azure Işlevleri için barındırma seçenekleri genelinde kullanılabilen ağ özellikleri açıklanmaktadır. Aşağıdaki tüm ağ seçenekleri, internet 'e yönlendirilebilir adresler kullanmadan veya bir işlev uygulamasına internet erişimini kısıtlamak için bazı özellikleri sağlar.

Barındırma modellerinin farklı düzeylerde ağ yalıtımı vardır. Doğru olanı seçtiğinizde, ağ yalıtımı gereksinimlerinizi karşılamanıza yardımcı olur.

İşlev uygulamalarını birkaç yolla barındırabilirsiniz:

* Çeşitli düzeylerde sanal ağ bağlantısı ve ölçeklendirme seçenekleri içeren çok kiracılı bir altyapıda çalışan bir plan seçenekleri kümesi vardır:
    * Yükleme için dinamik olarak ölçeklendirilen ve en düşük ağ yalıtımı seçeneklerini sunan [Tüketim planı](functions-scale.md#consumption-plan).
    * Ayrıca, dinamik olarak ölçeklendirilirken, daha kapsamlı ağ yalıtımı sunan [Premium planı](functions-scale.md#premium-plan).
    * Sabit ölçekte çalışan ve Premium plan olarak benzer ağ yalıtımı sunan Azure [App Service planı](functions-scale.md#app-service-plan).
* İşlevleri bir [App Service ortamı](../app-service/environment/intro.md)çalıştırabilirsiniz. Bu yöntem, işlevinizi sanal ağınıza dağıtır ve tam ağ denetimi ve yalıtımı sağlar.

## <a name="matrix-of-networking-features"></a>Ağ özellikleri matrisi

|                |[Tüketim planı](functions-scale.md#consumption-plan)|[Premium plan](functions-scale.md#premium-plan)|[App Service planı](functions-scale.md#app-service-plan)|[App Service Ortamı](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Özel site erişimini & gelen IP kısıtlamaları](#inbound-ip-restrictions)|Evet ✅|Evet ✅|Evet ✅|Evet ✅|
|[Sanal ağ tümleştirmesi](#virtual-network-integration)|❌Hayır|✅Evet (bölgesel)|✅Evet (bölgesel ve ağ geçidi)|Evet ✅|
|[Sanal ağ Tetikleyicileri (HTTP olmayan)](#virtual-network-triggers-non-http)|❌Hayır| ❌Hayır|Evet ✅|Evet ✅|
|[Karma bağlantılar](#hybrid-connections)|❌Hayır|Evet ✅|Evet ✅|Evet ✅|
|[Giden IP kısıtlamaları](#outbound-ip-restrictions)|❌Hayır| ❌Hayır|❌Hayır|Evet ✅|

## <a name="inbound-ip-restrictions"></a>Gelen IP kısıtlamaları

Uygulamanıza erişim izni verilen veya reddedilen IP adreslerinin öncelik sırasına sahip bir listesini tanımlamak için IP kısıtlamalarını kullanabilirsiniz. Liste, IPv4 ve IPv6 adreslerini içerebilir. Bir veya daha fazla giriş olduğunda, listenin sonunda örtülü bir "Tümünü Reddet" bulunur. IP kısıtlamaları tüm işlev barındırma seçenekleriyle çalışır.

> [!NOTE]
> Ağ kısıtlamalarına sahip olmak üzere, Portal düzenleyicisini yalnızca sanal ağınızdan veya Güvenli Alıcılar listesindeki Azure portal erişmek için kullandığınız makinenin IP adresini girdiğinizde kullanabilirsiniz. Ancak, herhangi bir makineden **platform özellikleri** sekmesindeki herhangi bir özelliğe erişmeye devam edebilirsiniz.

Daha fazla bilgi için bkz. [Azure App Service statik erişim kısıtlamaları](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Özel site erişimi

Özel site erişimi, uygulamanızı yalnızca Azure sanal ağı gibi özel bir ağdan erişilebilir hale getirme anlamına gelir.

* Özel site erişimi, hizmet uç noktaları yapılandırıldığında [Premium](./functions-premium-plan.md), [Tüketim](functions-scale.md#consumption-plan)ve [App Service](functions-scale.md#app-service-plan) planlarında kullanılabilir.
    * Hizmet uç noktaları, **platform özellikleri** > **ağ Iletişimi** > **erişim kısıtlamalarını yapılandırma** > **Kural Ekle**' nin altında uygulama temelinde yapılandırılabilir. Sanal ağlar artık bir kural türü olarak seçilebilir.
    * Daha fazla bilgi için bkz. [sanal ağ hizmeti uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md).
    * Hizmet uç noktalarında, işlevinizin hala internet 'e yönelik sanal ağ tümleştirmesiyle birlikte tam giden erişimi olduğunu aklınızda bulundurun.
* Özel site erişimi, iç yük dengeleyici (ıLB) ile yapılandırılmış bir App Service Ortamı içinde de kullanılabilir. Daha fazla bilgi için bkz. [bir App Service ortamı iç yük dengeleyici oluşturma ve kullanma](../app-service/environment/create-ilb-ase.md).

## <a name="virtual-network-integration"></a>Sanal ağ tümleştirmesi

Sanal Ağ tümleştirmesi, işlev uygulamanızın bir sanal ağ içindeki kaynaklara erişmesine olanak sağlar. Bu özellik hem Premium planda hem de App Service planında kullanılabilir. Uygulamanız bir App Service Ortamı ise, zaten bir sanal ağda bulunur ve sanal ağ tümleştirmesinin aynı sanal ağdaki kaynaklara ulaşması gerekmez.

Sanal ağ tümleştirmesini, uygulamalardan sanal ağınızda çalışan veritabanlarına ve Web hizmetlerine erişimi etkinleştirmek için kullanabilirsiniz. Sanal ağ tümleştirmesiyle, VM 'inizdeki uygulamalar için genel bir uç nokta kullanıma almanız gerekmez. Bunun yerine internet 'e ait olmayan özel yönlendirilebilen adresler kullanabilirsiniz.

Sanal ağ tümleştirmesinin iki biçimi vardır:

+ **Bölgesel sanal ağ tümleştirmesi (Önizleme)** : aynı bölgedeki sanal ağlarla tümleştirmeyi mümkün bir şekilde sunar. Bu tür bir tümleştirme, aynı bölgedeki bir sanal ağ için bir alt ağ gerektirir. Bu özellik hala önizlemededir, ancak aşağıdaki sorun/çözüm tablosundan sonra açıklanan uyarılar ile Windows üzerinde çalışan işlev uygulamaları için desteklenir.
+ **Ağ geçidi gereken sanal ağ tümleştirmesi**: uzak bölgelerdeki sanal ağlarla veya klasik sanal ağlarla tümleştirmeyi mümkün. Bu tür bir tümleştirme, sanal ağ geçidinin VNet 'iniz üzerinde dağıtılmasını gerektirir. Bu, yalnızca Windows üzerinde çalışan işlev uygulamaları için desteklenen Noktadan siteye VPN tabanlı bir özelliktir.

Bir uygulama aynı anda yalnızca bir sanal ağ tümleştirme özelliğinin türünü kullanabilir. Her ikisi de birçok senaryo için yararlı olsa da, aşağıdaki tablo her birinin nerede kullanılması gerektiğini göstermektedir:

| Sorun  | Çözüm |
|----------|----------|
| Aynı bölgedeki bir RFC 1918 adresine (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) ulaşmak ister | Bölgesel sanal ağ tümleştirmesi |
| Klasik bir sanal ağdaki veya başka bir bölgedeki sanal ağdaki kaynaklara ulaşmak ister | Ağ Geçidi gereken sanal ağ tümleştirmesi |
| Azure ExpressRoute üzerinden RFC 1918 uç noktalarına erişmek ister misiniz? | Bölgesel sanal ağ tümleştirmesi |
| Hizmet uç noktaları genelinde kaynaklara ulaşmak ister misiniz? | Bölgesel sanal ağ tümleştirmesi |

Her iki özellik de ExpressRoute üzerinden RFC 1918 olmayan adreslere ulaşmanıza imkan tanır. Bunu yapmak için şu anda bir App Service Ortamı kullanmanız gerekir.

Bölgesel sanal ağ tümleştirmesinin kullanılması, Sanal ağınızı şirket içi uç noktalara bağlama veya hizmet uç noktalarını yapılandırma. Bu, ayrı bir ağ yapılandırması. Bölgesel sanal ağ tümleştirmesi yalnızca uygulamanızın bu bağlantı türleri arasında çağrı yapmasına olanak sağlar.

Sanal Ağ tümleştirmesi, kullanılan sürümden bağımsız olarak, işlev uygulamanızın sanal ağınızdaki kaynaklara erişmesini sağlar, ancak sanal ağdan işlev uygulamanıza özel site erişimi vermez. Özel site erişimi, uygulamanızı yalnızca bir Azure sanal ağı gibi özel bir ağdan erişilebilir hale getirir. Sanal Ağ tümleştirmesi yalnızca uygulamanızdan sanal ağınıza giden çağrılar yapmak içindir.

Sanal ağ tümleştirme özelliği:

* Standart, Premium veya PremiumV2 App Service planı gerektirir
* TCP ve UDP 'yi destekler
* App Service uygulamalar ve işlev uygulamalarıyla çalışır

Sanal ağ tümleştirmesinin desteklemediği bazı şeyler vardır, örneğin:

* Sürücü takma
* Active Directory tümleştirmesi
* Tanımlaya

Azure Işlevlerinde sanal ağ tümleştirmesi App Service Web Apps ile paylaşılan altyapıyı kullanır. İki tür sanal ağ tümleştirmesi hakkında daha fazla bilgi edinmek için bkz.:

* [Bölgesel sanal ağ tümleştirmesi](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Ağ Geçidi gereken sanal ağ tümleştirmesi](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Sanal ağ tümleştirmesini kullanma hakkında daha fazla bilgi için bkz. [bir işlev uygulamasını bir Azure sanal ağı Ile tümleştirme](functions-create-vnet.md).

## <a name="connecting-to-service-endpoint-secured-resources"></a>Hizmet uç noktası güvenliği sağlanmış kaynaklara bağlanılıyor

> [!NOTE]
> Şimdilik, aşağı akış kaynağında erişim kısıtlamalarını yapılandırdıktan sonra yeni hizmet uç noktalarının işlev uygulamanız için kullanılabilir hale gelmesi 12 saate kadar sürebilir. Bu süre boyunca, kaynak uygulamanız için tamamen kullanılamaz olur.

Daha yüksek bir güvenlik düzeyi sağlamak için, hizmet uç noktalarını kullanarak bir dizi Azure hizmetini bir sanal ağ ile kısıtlayabilirsiniz. Daha sonra, kaynak erişimi için işlev uygulamanızı bu sanal ağla tümleştirmeniz gerekir. Bu yapılandırma, sanal ağ tümleştirmesini destekleyen tüm planlarda desteklenir.

[Sanal ağ hizmeti uç noktaları hakkında daha fazla bilgi edinin.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>Depolama hesabınızı bir sanal ağla kısıtlama

Bir işlev uygulaması oluşturduğunuzda, blob, kuyruk ve tablo depolamayı destekleyen genel amaçlı bir Azure depolama hesabı oluşturmanız veya bağlamanız gerekir. Şu anda bu hesapta herhangi bir sanal ağ kısıtlaması kullanamazsınız. İşlev uygulamanız için kullandığınız depolama hesabında bir sanal ağ hizmeti uç noktası yapılandırırsanız, bu, uygulamanızı bozacaktır.

[Depolama hesabı gereksinimleri hakkında daha fazla bilgi edinin.](./functions-create-function-app-portal.md#storage-account-requirements)

### <a name="using-key-vault-references"></a>Key Vault başvurularını kullanma 

Key Vault başvurular, Azure Işlevleri uygulamanızda herhangi bir kod değişikliğine gerek kalmadan Azure Key Vault gizli dizileri kullanmanıza olanak sağlar. Azure Key Vault, erişim ilkeleri ve denetim geçmişi üzerinde tam denetim ile merkezi gizli dizi yönetimi sağlayan bir hizmettir.

Key Vault hizmet uç noktalarıyla güvenlik altına alınırsa, şu anda [Key Vault başvuruları](../app-service/app-service-key-vault-references.md) çalışmayacaktır. Sanal ağ tümleştirmesini kullanarak bir Key Vault bağlanmak için uygulama kodunuzda Anahtar Kasası 'nı çağırmanız gerekir.

## <a name="virtual-network-triggers-non-http"></a>Sanal ağ Tetikleyicileri (HTTP olmayan)

Şu anda, bir sanal ağ içinden HTTP dışında işlev tetiklerinin kullanılması için, işlev uygulamanızı bir App Service planında veya bir App Service Ortamı çalıştırmanız gerekir.

Örneğin, yalnızca bir sanal ağdan gelen trafiği kabul etmek için Azure Cosmos DB yapılandırmak istediğinizi varsayalım. İşlev uygulamanızı, bu kaynaktaki Azure Cosmos DB Tetikleyicileri yapılandırmak üzere bu sanal ağla sanal ağ tümleştirmesi sağlayan bir App Service planında dağıtmanız gerekir. Önizleme süresince, sanal ağ tümleştirmesini yapılandırmak Premium planın bu Azure Cosmos DB kaynağını tetiklemesine izin vermez.

Nelerin desteklendiğini iki kez kontrol etmek için [http olmayan tüm tetikleyiciler için bu listeye](./functions-triggers-bindings.md#supported-bindings) bakın.

## <a name="hybrid-connections"></a>Karma Bağlantılar

[Karma bağlantılar](../service-bus-relay/relay-hybrid-connections-protocol.md) , diğer ağlardaki uygulama kaynaklarına erişmek için kullanabileceğiniz bir Azure Relay özelliğidir. Uygulamadan bir uygulama uç noktasına erişim sağlar. Uygulamanıza erişmek için kullanamazsınız. Karma Bağlantılar, tüketim planı hariç tüm işlevler için kullanılabilir.

Azure Işlevlerinde kullanıldığında, her karma bağlantı tek bir TCP ana bilgisayarı ve bağlantı noktası bileşimiyle söz konusu. Bu, karma bağlantının uç noktasının, TCP dinleme bağlantı noktasına eriştiğiniz sürece herhangi bir işletim sisteminde ve herhangi bir uygulamada olabileceği anlamına gelir. Karma Bağlantılar özelliği, uygulama protokolünün ne olduğunu veya ne erişmekte olduğunu bilmez veya ilgilenmez. Yalnızca ağ erişimi sağlar.

Daha fazla bilgi edinmek için [Karma Bağlantılar App Service belgelerine](../app-service/app-service-hybrid-connections.md)bakın. Aynı yapılandırma adımları Azure Işlevlerini destekler.

## <a name="outbound-ip-restrictions"></a>Giden IP kısıtlamaları

Giden IP kısıtlamaları yalnızca bir App Service Ortamı dağıtılan işlevler için kullanılabilir. App Service Ortamı dağıtıldığı sanal ağın giden kısıtlamalarını yapılandırabilirsiniz.

Bir Premium planda veya bir sanal ağ ile App Service planında bir işlev uygulamasını tümleştirdiğinizde, uygulama yine de internet 'e giden çağrılar yapabilir.

## <a name="next-steps"></a>Sonraki adımlar

Ağ ve Azure Işlevleri hakkında daha fazla bilgi edinmek için:

* [Sanal ağ tümleştirmesiyle çalışmaya başlama öğreticisini izleyin](./functions-create-vnet.md)
* [Ağ iletişimi SSS Işlevlerini okuyun](./functions-networking-faq.md)
* [App Service/Işlevlerle sanal ağ tümleştirmesi hakkında daha fazla bilgi edinin](../app-service/web-sites-integrate-with-vnet.md)
* [Azure 'da sanal ağlar hakkında daha fazla bilgi edinin](../virtual-network/virtual-networks-overview.md)
* [App Service ortamlarla daha fazla ağ özelliği ve denetimi etkinleştirin](../app-service/environment/intro.md)
* [Karma Bağlantılar kullanarak, güvenlik duvarı değişikliği yapmadan tek tek şirket içi kaynaklara bağlanma](../app-service/app-service-hybrid-connections.md)
