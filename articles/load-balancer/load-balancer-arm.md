---
title: Load Balancer için Azure Resource Manager desteği | Microsoft Docs
description: Azure Resource Manager Load Balancer için PowerShell 'i kullanma. Yük Dengeleyici için şablonları kullanma
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 839b607b7787d51151401737848a46d7b66229dd
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275483"
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Azure Load Balancer Azure Resource Manager desteğini kullanma



Azure Resource Manager, Azure 'daki hizmetler için tercih edilen yönetim çerçevesidir. Azure Load Balancer, Azure Resource Manager tabanlı API 'Ler ve araçlar kullanılarak yönetilebilir.

## <a name="concepts"></a>Kavramlar

Kaynak Yöneticisi, Azure Load Balancer aşağıdaki alt kaynakları içerir:

* Ön uç IP yapılandırması – bir yük dengeleyici, sanal IP 'Ler (VIP) olarak da bilinen bir veya daha fazla ön uç IP adresi içerebilir. Bu IP adresleri trafik için bir giriş işlevi görür.
* Arka uç adres havuzu: Bunlar, yükün dağıtıldığı sanal makine ağ arabirimi kartı (NIC) ile ilişkili IP adresleridir.
* Yük Dengeleme kuralları – bir kural özelliği, belirli bir ön uç IP ve bağlantı noktası bileşimini bir arka uç IP adresleri ve bağlantı noktası birleşimine eşler. Tek bir yük dengeleyici birden çok dengeleme kuralına sahip olabilir. Her kural, ön uç IP ve bağlantı noktasının ve VM 'lerle ilişkili bağlantı noktasının bir birleşimidir.
* Yoklamalar – yoklamalar, VM örneklerinin sistem durumunu izlemenize olanak tanır. Bir sistem durumu araştırması başarısız olursa, VM örneği otomatik olarak dönüşten alınır.
* Gelen NAT kuralları – ön uç IP 'si üzerinden akan ve arka uç IP 'ye dağıtılan gelen trafiği tanımlayan NAT kuralları.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Hızlı başlangıç şablonları

Azure Resource Manager, uygulamalarınızı bildirim temelli bir şablon aracılığıyla sağlamanıza olanak tanır. Tek bir şablonda birden çok hizmeti bağımlılıklarıyla birlikte dağıtabilirsiniz. Uygulama yaşam döngüsünün her aşamasında uygulamanızı tekrar tekrar dağıtmak için aynı şablonu kullanırsınız.

Şablonlar, sanal makineler, sanal ağlar, kullanılabilirlik kümeleri, ağ arabirimleri (NIC), depolama hesapları, yük dengeleyiciler, ağ güvenlik grupları ve genel IP 'Ler için tanımlar içerebilir. Şablonlar sayesinde, karmaşık bir uygulama için ihtiyacınız olan her şeyi oluşturabilirsiniz. Şablon dosyası, sürüm denetimi ve işbirliği için içerik yönetim sistemine denetlenebilir.

[Şablonlar hakkında daha fazla bilgi edinin](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Ağ kaynakları hakkında daha fazla bilgi](../networking/networking-overview.md)

Azure Load Balancer kullanan hızlı başlangıç şablonları için, topluluk tarafından oluşturulan şablonlar kümesini barındıran [GitHub deposuna](https://github.com/Azure/azure-quickstart-templates) bakın.

Şablon örnekleri:

* [bir Load Balancer ve yük dengeleme kurallarında 2 VM](https://go.microsoft.com/fwlink/?LinkId=544799)
* [Iç Load Balancer ve Load Balancer kurallarına sahip bir VNET 'te 2 sanal makine](https://go.microsoft.com/fwlink/?LinkId=544800)
* [bir Load Balancer 2 VM ve LB üzerinde NAT kuralları yapılandırma](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>PowerShell veya CLı ile Azure Load Balancer ayarlama

Azure Resource Manager cmdlet 'leri, komut satırı araçları ve REST API 'Leri ile çalışmaya başlama

* [Azure ağ cmdlet 'leri](https://docs.microsoft.com/powershell/module/az.network#networking) , bir Load Balancer oluşturmak için kullanılabilir.
* [Azure Resource Manager kullanarak yük dengeleyici oluşturma](load-balancer-get-started-ilb-arm-ps.md)
* [Azure CLı 'yi Azure Kaynak yönetimi ile kullanma](../xplat-cli-azure-resource-manager.md)
* [REST API 'Leri Load Balancer](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Sonraki adımlar

Ayrıca, [Internet 'e yönelik yük dengeleyici oluşturmaya başlayabilir](load-balancer-get-started-internet-arm-ps.md) ve belirli bir yük dengeleyici ağ trafiği davranışı için hangi tür [Dağıtım modunu](load-balancer-distribution-mode.md) yapılandırabilirsiniz.

[Yük Dengeleyici için BOŞTAKI TCP zaman aşımı ayarlarını](load-balancer-tcp-idle-timeout.md)yönetmeyi öğrenin. Bu, uygulamanızın bir yük dengeleyicinin arkasındaki sunucular için bağlantıları canlı tutması gerektiğinde önemlidir.
