---
title: Azure Resource Manager şablonu örnekleri-Azure ön kapısı
description: Temel bir ön kapı oluşturmaya ve ön kapı oranı sınırlandırmasını yapılandırmaya yönelik şablonlar da dahil olmak üzere Azure ön kapısının Kaynak Yöneticisi şablon örnekleri hakkında bilgi edinin.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2020
ms.author: duau
ms.openlocfilehash: bac1df020bf2a683fc04a4d05ae73311e149f70c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97511782"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Front Door için Azure Resource Manager dağıtım modeli şablonları

Aşağıdaki tabloda, Azure ön kapısına yönelik Azure Resource Manager dağıtım modeli şablonlarının bağlantıları yer almaktadır. 

| Şablon | Description |
| ---| ---|
| [Temel Front Door oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic)| Tek bir arka uçla temel Front Door yapılandırması oluşturur. |
| [Birden çok arka uçla, arka uç havuzuyla ve URL tabanlı yönlendirmeyle Front Door oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends)| Arka uç havuzundaki birden çok arka uç için ve URL yolu temelinde arka uç havuzları arasında yük dengeleme yapılandırmasına sahip bir Front Door oluşturur. |
| [Ön kapıya sahip özel bir etki alanı ekleme](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| Ön kapıya özel bir etki alanı ekleyin. |
| [Coğrafi filtreleme ile ön kapı oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| Belirli ülkelerden/bölgelerden gelen trafiğe izin veren/engelleyen bir ön kapı oluşturun. |
| [Front Door'da arka uçlarınız için Durum Yoklamalarını denetleme](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes)| Yoklama yolunu ve ayrıca yoklamaların gönderileceği zaman aralıklarını güncelleştirerek durum yoklaması ayarlarını değiştirmek için Front Door'unuzu güncelleştirin. |
| [Etkin/Beklemede arka uç yapılandırmasıyla Front Door oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb)| Etkin/Beklemede uygulama topolojisi için önceliklere dayalı yönlendirme gösteren, başka bir deyişle varsayılan olarak tüm trafiği birincil (en yüksek öncelikli) arka uca gönderen ve arka uç kullanılamaz duruma gelene kadar buna devam eden bir Front Door oluşturur. |
| [Belirli yollar için önbelleğin etkinleştirildiği bir Front Door oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching)| Tanımlanan yönlendirme yapılandırması için önbelleğin etkinleştirildiği, böylelikle iş yükünüz için tüm statik varlıkların önbelleğe alındığı bir Front Door oluşturur. |
| [Front Door konak adlarınız için Oturum Benzeşimi'ni yapılandırma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) | Ön uç konağınız için oturum benzeşimini etkinleştirmek, böylelikle aynı kullanıcı oturumundan daha sonra gelen trafiği aynı arka ucu göndermek üzere Front Door'u güncelleştirir. |
| [İstemci IP 'si allowlist veya Blocklist için ön kapıyı yapılandırma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)| Front Door'u istemci IP'lerini kullanıp özel erişim denetiminden yararlanarak belirli istemci IP'lerinin trafiğini kısıtlayacak şekilde yapılandırır. |
| [Ön kapıyı belirli http parametreleriyle işlem yapması için yapılandırma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Front Door'u http parametrelerini kullanıp erişim denetimi için özel kurallardan yararlanarak gelen istekteki http parametreleri temelinde belirli trafiklere izin verecek veya engelleyecek şekilde yapılandırır. |
| [Ön kapı hız sınırını yapılandırma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Front Door'u belirli bir ön uç konağı için gelen trafiğin hızını sınırlandıracak şekilde yapılandırır. |
| | |

## <a name="next-steps"></a>Sonraki adımlar

- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.
