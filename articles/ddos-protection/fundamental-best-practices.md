---
title: Azure DDoS koruması temel en iyi uygulamaları
description: DDoS koruması 'nı kullanarak en iyi güvenlik uygulamalarını öğrenin.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: ea5e9e9883c8c1da17cf99dabc72f2339c8dbe1a
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107102986"
---
# <a name="fundamental-best-practices"></a>Temel en iyi yöntemler

Aşağıdaki bölümler Azure 'da DDoS-dayanıklı hizmetler oluşturmaya yönelik öngörülü rehberlik sağlar.

## <a name="design-for-security"></a>Güvenlik için tasarlama

Tasarımın, bir uygulamanın tüm yaşam döngüsünün tamamında, tasarım ve uygulama ile dağıtım ve işlemlere kadar öncelikli olduğundan emin olun. Uygulamalar görece düşük bir istek hacmine izin veren hatalara sahip olabilir ve bu da hizmet kesintisi oluşmasına neden olur. 

Microsoft Azure üzerinde çalışan bir hizmetin korunmasına yardımcı olmak için, uygulama mimarinizi iyi bir şekilde kavramanız ve [yazılım kalitesinin beş](/azure/architecture/guide/pillars)ile ilgili olarak odaklanmanız gerekir.
Tipik trafik birimlerini, uygulama ve diğer uygulamalar arasındaki bağlantı modelini ve genel İnternet 'e açık olan hizmet uç noktalarını bilmeniz gerekir.

Bir uygulamanın, uygulamanın kendisi için hedeflenen bir hizmet reddine yetecek kadar dayanıklı olmasını sağlamak, en önemli öneme sahiptir. Güvenlik ve gizlilik, [güvenlik geliştirme yaşam döngüsü (SDL)](https://www.microsoft.com/sdl/default.aspx)Ile başlayan Azure platformunda yerleşik olarak bulunur. SDL her geliştirme aşamasında güvenliği adresleyen ve Azure 'un sürekli olarak daha güvenli hale getirmek için güncelleştirilmesini sağlar.

## <a name="design-for-scalability"></a>Ölçeklenebilirlik için tasarım

Ölçeklenebilirlik, sistemin artan yükü işleyebilme konusunda ne kadar iyi bir sistem olabilir. Uygulamalarınızı, özellikle de DDoS saldırısı durumunda, yükseltilmiş bir yükün talebini karşılayacak şekilde [ölçeklendirmek](/azure/architecture/guide/design-principles/scale-out) üzere tasarlayın. Uygulamanız bir hizmetin tek bir örneğine bağımlıysa, tek bir hata noktası oluşturur. Birden çok örneği sağlamak, sisteminizi daha dayanıklı ve daha ölçeklenebilir hale getirir.

[Azure App Service](../app-service/overview.md)için, birden çok örnek sunan bir [App Service planı](../app-service/overview-hosting-plans.md) seçin. Azure Cloud Services için, rollerinizin her birini [birden çok örnek](../cloud-services/cloud-services-choose-me.md)kullanacak şekilde yapılandırın. [Azure sanal makineler](../virtual-machines/index.yml)için, sanal makıne (VM) mimarinizin bırden fazla VM içerdiğinden ve her VM 'nin bir [kullanılabilirlik kümesine](../virtual-machines/windows/tutorial-availability-sets.md)eklendiğinden emin olun. Otomatik ölçeklendirme özellikleri için [Sanal Makine Ölçek Kümeleri](../virtual-machine-scale-sets/overview.md) kullanmanızı öneririz.

## <a name="defense-in-depth"></a>Derinlemesine savunma

Derinlemesine savunma 'nın arkasındaki fikir, farklı savunma stratejileri kullanarak riskleri yönetmenizde yarar vardır. Bir uygulamadaki güvenlik savunmaları katmanlama, başarılı bir saldırı olasılığını azaltır. Azure platformunun yerleşik yeteneklerini kullanarak uygulamalarınız için güvenli tasarımlar uygulamanızı öneririz.

Örneğin, saldırı riski uygulamanın boyutuyla (*yüzey alanı*) artar. Açık IP adresi alanını ve yük dengeleyiciler üzerinde gerekli olmayan dinleme bağlantı noktalarını ([Azure Load Balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md) ve [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)) kapatmak için bir onay listesi kullanarak yüzey alanını azaltabilirsiniz. [Ağ güvenlik grupları (NSG 'ler)](../virtual-network/network-security-groups-overview.md) , saldırı yüzeyini azaltmak için başka bir yoldur.
Uygulama yapısının doğal bir uzantısı olarak güvenlik kuralları oluşturma ve ağ güvenliğini yapılandırma karmaşıklığını en aza indirmek için [hizmet etiketlerini](../virtual-network/network-security-groups-overview.md#service-tags) ve [uygulama güvenlik gruplarını](../virtual-network/network-security-groups-overview.md#application-security-groups) kullanabilirsiniz.

Mümkün olduğunda Azure hizmetlerini bir [Sanal ağda](../virtual-network/virtual-networks-overview.md) dağıtmanız gerekir. Bu uygulama, hizmet kaynaklarının özel IP adresleri üzerinden iletişim kurmasına izin verir. Bir sanal ağdan gelen Azure hizmet trafiği, varsayılan olarak kaynak IP adresleri olarak genel IP adreslerini kullanır. [Hizmet uç noktalarının](../virtual-network/virtual-network-service-endpoints-overview.md) kullanılması, hizmet trafiğini bir sanal ağdan Azure hizmetine ERIŞIRKEN kaynak IP adresleri olarak sanal ağ özel adreslerini kullanacak şekilde geçer.

Müşterilerin Şirket içi kaynaklarını Azure 'daki kaynaklarıyla birlikte saldırıya uğradığını görtik. Şirket içi bir ortamı Azure 'a bağlıyorsanız, şirket içi kaynakların açık internet 'te etkilenme olasılığını en aza indirmenizi öneririz. Azure 'un ölçek ve gelişmiş DDoS koruma özelliklerini kullanarak Azure 'da iyi bilinen genel varlıkları dağıtabilirsiniz. Bu genel olarak erişilebilen varlıklar, DDoS saldırıları için genellikle bir hedef olduğundan, bunları Azure 'a koymak, şirket içi kaynaklarınızın etkisini azaltır.

## <a name="next-steps"></a>Sonraki adımlar

- [DDoS koruma planı oluşturma](manage-ddos-protection.md)hakkında bilgi edinin.