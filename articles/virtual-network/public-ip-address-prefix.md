---
title: Azure genel IP adresi ön eki | Microsoft Docs
description: Azure genel IP adresi ön ekinin ne olduğunu ve kaynaklarınıza öngörülebilir genel IP adresleri atamanıza nasıl yardımcı olabileceğini öğrenin.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: f89218b066b0a22559c00c4a53316f0df9c0bb8f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488450"
---
# <a name="public-ip-address-prefix"></a>Genel IP adresi ön eki

Genel IP adresi ön eki, Azure 'daki genel uç noktalarınız için ayrılmış bir IP adresi aralığıdır. Azure, belirttiğiniz kadar, aboneliğinize göre aboneliğiniz için bitişik bir adres aralığı ayırır. Ortak adreslere alışkın değilseniz bkz [. genel IP adresleri.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Genel IP adresleri, her bir Azure bölgesindeki bir adres havuzundan atanır. Her bölge için Azure tarafından kullanılan aralıkların listesini [indirebilirsiniz](https://www.microsoft.com/download/details.aspx?id=56519) . Örneğin, 40.121.0.0/16, Azure 'un Doğu ABD bölgesinde kullandığı 100 'ten biridir. Aralık, 40.121.0.1-40.121.255.254 öğesinin kullanılabilir adreslerini içerir.

Bir ad belirterek bir Azure bölgesinde ve abonelikte genel IP adresi ön eki ve önekin hangi sayıda adres içermesini istediğinizi belirtin. Örneğin,/28 genel IP adresi ön ekini oluşturursanız, Azure kendi aralıklarından birinden 16 adres ayırır. Aralığı oluşturana kadar Azure 'un atayabileceği aralığı bilemezsiniz, ancak adresler bitişik olur. Genel IP adresi ön eklerinin ücreti vardır. Ayrıntılar için bkz. [genel IP adresi fiyatlandırması](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Neden genel IP adresi öneki oluşturulsun?

Genel IP adresi kaynakları oluşturduğunuzda, Azure bölgede kullanılan aralıklardan herhangi birinden kullanılabilir bir genel IP adresi atar. Azure adresi atadıktan sonra, adresin ne olduğunu, ancak Azure 'un adresi atamayacağını bilmeniz, hangi adresin atanabileceği bilinmiyor. Bu sorun, Örneğin siz veya iş ortaklarınız, belirli IP adreslerine izin veren güvenlik duvarı kuralları kurulumu sırasında sorunlu olabilir. Bir kaynağa yeni bir genel IP adresi atadığınızda, adresin güvenlik duvarı kuralına eklenmesi gerekir. Kaynaklarınıza genel IP adresi önekinden adres atadığınızda, tüm Aralık bir kurala eklenebildiğinden Güvenlik Duvarı kurallarının, adreslerden birini atadığınız her seferinde güncelleştirilmesini gerekmez.

## <a name="benefits"></a>Avantajlar

- Bilinen bir aralıktan ortak IP adresi kaynakları oluşturabilirsiniz.
- Siz veya iş ortaklarınız, şu anda atamış olduğunuz genel IP adreslerini içeren aralıklar ile güvenlik duvarı kuralları oluşturabilir ve henüz atamamış olduğunuz adresleri de oluşturabilirsiniz. Bu, yeni kaynaklara IP adresleri atarken güvenlik duvarı kurallarını değiştirme gereksinimini ortadan kaldırır.
- Oluşturabileceğiniz bir aralığın varsayılan boyutu/28 veya 16 IP adresleridir.
- Ancak, kaç aralığın oluşturabileceğiniz için bir sınır yoktur, ancak bir Azure aboneliğinde bulunan en fazla statik genel IP adresi sayısı için sınırlar vardır. Sonuç olarak, oluşturduğunuz aralıkların sayısı aboneliğinizde bulunandan daha statik genel IP adreslerini kapsayamaz. Daha fazla bilgi için bkz. [Azure Limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Önekten adresler kullanarak oluşturduğunuz adresler, genel bir IP adresi atayabilmeniz için herhangi bir Azure kaynağına atanabilir.
- Ayrılan ve henüz Aralık içinde ayrılmamış IP adreslerini görebilirsiniz.

## <a name="scenarios"></a>Senaryolar
Aşağıdaki kaynakları bir önekten statik bir genel IP adresi ile ilişkilendirebilirsiniz:

|Kaynak|Senaryo|Adımlar|
|---|---|---|
|Virtual Machines| Azure 'daki sanal makinelerinize genel IP 'Lerin bir önekden ilişkilendirilmesi, bir güvenlik duvarındaki IP 'Leri beyaz listeye alırken yönetim yükünü azaltır. Tek bir güvenlik duvarı kuralıyla, tüm ön eki kolayca listeleyebilirsiniz. Azure 'daki sanal makinelerle ölçeklendirdiğinizde, IP 'Leri aynı önek tasarrufu, zaman ve yönetim yüküyle ilişkilendirebilirsiniz.| IP 'Leri bir önekten sanal makinenize ilişkilendirmek için: 1. [Ön ek oluşturun.](manage-public-ip-address-prefix.md) 2. [Önekten bir IP oluşturun.](manage-public-ip-address-prefix.md) 3. [IP 'yi sanal makinenizin ağ arabirimiyle ilişkilendirin.](virtual-network-network-interface-addresses.md#add-ip-addresses) Ayrıca [, IP 'leri bir sanal makine ölçek kümesi ile ilişkilendirebilirsiniz](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Standart yük dengeleyiciler | Genel IP 'Leri bir önekten ön uç IP yapılandırmanıza veya bir Load Balancer giden kuralına ilişkilendirerek, Azure genel IP adresi alanınızı basitleştirilmesini sağlar. Genel IP öneki tarafından tanımlanan bir dizi bitişik IP adresinden kaynaklayarak giden bağlantıları temizliği yaparak senaryonuzu kolaylaştırabilirsiniz. | IP 'Leri bir önekden yük dengeleyicinizle ilişkilendirmek için: 1. [Ön ek oluşturun.](manage-public-ip-address-prefix.md) 2. [Önekten bir IP oluşturun.](manage-public-ip-address-prefix.md) 3. Load Balancer oluştururken yukarıdaki 2. adımda oluşturulan IP 'yi Load Balancer ön uç IP 'si olarak seçin veya güncelleştirin. |
| Azure Güvenlik Duvarı | Giden SNAT için bir önekte ortak IP kullanabilirsiniz. Bu, tüm giden sanal ağ trafiğinin [Azure Güvenlik Duvarı](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) genel IP 'ye çevrildiği anlamına gelir. Bu IP önceden belirlenmiş bir önekden geldiği için, Azure 'daki genel IP parmak izinizden nasıl görüneceğine ilişkin bir süre önce daha kolay bir şekilde haberdar olmak çok kolaydır. | 1. [bir ön ek oluşturun.](manage-public-ip-address-prefix.md) 2. [Önekten bir IP oluşturun.](manage-public-ip-address-prefix.md) 3. [Azure Güvenlik duvarını dağıtırken](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), ön ekten daha önce AYıRMıŞ olduğunuz IP 'yi seçtiğinizden emin olun.|
| Application Gateway v2 | Otomatik ölçeklendirme ve bölgesel olarak yedekli Application Gateway v2 için bir önekten ortak IP kullanabilirsiniz. Bu IP önceden belirlenmiş bir önekden geldiği için, Azure 'daki genel IP parmak izinizden nasıl görüneceğine ilişkin bir süre önce daha kolay bir şekilde haberdar olmak çok kolaydır. | 1. [bir ön ek oluşturun.](manage-public-ip-address-prefix.md) 2. [Önekten bir IP oluşturun.](manage-public-ip-address-prefix.md) 3. [Application Gateway dağıttığınızda](../application-gateway/quick-create-portal.md#create-an-application-gateway), daha önce önekden AYıRMıŞ olduğunuz IP 'yi seçtiğinizden emin olun.|

## <a name="constraints"></a>Kısıtlamalar

- Ön ek için IP adreslerini belirtemezsiniz. Azure, belirttiğiniz boyuta göre önek için IP adreslerini ayırır.
- 16 IP adresi veya/28 ön eki oluşturabilirsiniz. Daha fazla bilgi için bkz. [Azure Limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Öneki oluşturduktan sonra aralığı değiştiremezsiniz.
- Ön ek aralığından yalnızca standart SKU ile oluşturulan statik genel IP adresleri atanabilir. Genel IP adresi SKU 'Ları hakkında daha fazla bilgi için bkz. [genel IP adresi](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Aralıktaki adresler yalnızca Azure Resource Manager kaynaklarına atanabilir. Adresler, klasik dağıtım modelindeki kaynaklara atanamaz.
- Önekten oluşturulan tüm genel IP adresleri, ön ekiyle aynı Azure bölgesinde ve abonelikte bulunmalı ve aynı bölgedeki ve abonelikteki kaynaklara atanmalıdır.
- İçindeki herhangi bir adres bir kaynakla ilişkili genel IP adresi kaynaklarına atanırsa bir ön eki silemezsiniz. İlk olarak, IP adreslerinin atandığı tüm genel IP adresi kaynaklarının ilişkisini kaldırın.


## <a name="next-steps"></a>Sonraki adımlar

- Genel IP adresi öneki [oluşturma](manage-public-ip-address-prefix.md)
