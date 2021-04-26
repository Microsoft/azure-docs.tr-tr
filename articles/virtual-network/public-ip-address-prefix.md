---
title: Azure genel IP adresi ön eki
description: Azure genel IP adresi ön ekinin ne olduğunu ve kaynaklarınıza öngörülebilir genel IP adresleri atamanıza nasıl yardımcı olabileceğini öğrenin.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2020
ms.author: allensu
ms.openlocfilehash: 8bfc76afc1ef799be0fb29654bd341f53d1b0a8c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048468"
---
# <a name="public-ip-address-prefix"></a>Genel IP adresi ön eki

Genel IP adresi ön eki, Azure 'da ayrılmış bir IP adresi aralığıdır. Azure, belirttiğiniz kadar, aboneliğinize göre aboneliğiniz için bitişik bir adres aralığı sağlar. 

Ortak adreslere alışkın değilseniz bkz [. genel IP adresleri.](./public-ip-addresses.md#public-ip-addresses)

Genel IP adresleri, her bir Azure bölgesindeki bir adres havuzundan atanır. Her bölge için Azure tarafından kullanılan aralıkların listesini [indirebilirsiniz](https://www.microsoft.com/download/details.aspx?id=56519) . Örneğin, 40.121.0.0/16, Azure 'un Doğu ABD bölgesinde kullandığı 100 'ten biridir. Aralık, 40.121.0.1-40.121.255.254 öğesinin kullanılabilir adreslerini içerir.

Bir ad belirterek bir Azure bölgesinde ve abonelikte genel IP adresi ön eki ve önekin hangi sayıda adres içermesini istediğinizi belirtin. 

Genel IP adresi aralıkları, seçtiğiniz bir ön ekiyle atanır. /28 için bir ön ek oluşturursanız Azure, aralıklarından birinden 16 IP adresi verir.

Aralığı oluşturana kadar Azure 'un atayabileceği aralığı bilemezsiniz, ancak adresler bitişik olur. 

Genel IP adresi önekleri ücretlendirmesi ve daha fazla bilgi için bkz. [genel IP adresi fiyatlandırması](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Neden genel IP adresi öneki oluşturulsun?

Genel IP adresi kaynakları oluşturduğunuzda, Azure bu bölgede kullanılan aralıklardan herhangi birinden kullanılabilir bir genel IP adresi atar. 

Azure IP adresini atamaz kadar, tam IP 'yi bilemezsiniz. Bu işlem, belirli IP adreslerine izin veren güvenlik duvarı kuralları oluştururken sorunlu olabilir. Eklenen her IP adresi için, karşılık gelen bir güvenlik duvarı kuralı eklenmelidir.

Kaynaklarınıza genel IP adresi önekinden adres atadığınızda, güvenlik duvarı kuralı güncelleştirmeleri gerekli değildir. Tüm Aralık kurala eklenir.

## <a name="benefits"></a>Avantajlar

- Bilinen bir aralıktan ortak IP adresi kaynakları oluşturma.
- Şu anda atadığınız genel IP adreslerini içeren aralıklardan güvenlik duvarı kuralı yapılandırması ve henüz atamamış olduğunuz adresler. Bu yapılandırma, yeni kaynaklara IP adresleri atarken güvenlik duvarı kurallarını değiştirme gereksinimini ortadan kaldırır.
- Oluşturabileceğiniz bir aralığın varsayılan boyutu/28 veya 16 IP adresleridir.
- Kaç tane Aralık oluşturabileceğiniz ile sınırlı değildir. Bir Azure aboneliğinde, en fazla statik genel IP adresi sayısı sınırı vardır. Oluşturduğunuz aralıkların sayısı, aboneliğinizde bulunandan daha fazla statik genel IP adresi kapsayamaz. Daha fazla bilgi için bkz. [Azure Limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Önekten adresler kullanarak oluşturduğunuz adresler, genel bir IP adresi atayabilmeniz için herhangi bir Azure kaynağına atanabilir.
- Aralık dahilinde verilen ve verilmeyen IP adreslerini kolayca görebilirsiniz.

## <a name="scenarios"></a>Senaryolar
Aşağıdaki kaynakları bir önekten statik bir genel IP adresi ile ilişkilendirebilirsiniz:

|Kaynak|Senaryo|Adımlar|
|---|---|---|
|Sanal makineler| Azure 'daki sanal makinelerinize genel IP 'Lerin bir önekden ilişkilendirilmesi, güvenlik duvarında bir izin verilenler listesine IP adresleri eklerken yönetim yükünü azaltır. Tek bir güvenlik duvarı kuralıyla tüm ön eki ekleyebilirsiniz. Azure 'daki sanal makinelerle ölçeklendirdiğinizde, IP 'Leri aynı önek tasarrufu, zaman ve yönetim yüküyle ilişkilendirebilirsiniz.| IP 'Leri bir önekten sanal makinenize ilişkilendirmek için: </br> 1. [bir ön ek oluşturun.](manage-public-ip-address-prefix.md) </br> 2. [önekten BIR IP oluşturun.](manage-public-ip-address-prefix.md) </br> 3. [IP 'yi sanal makinenizin ağ arabirimiyle ilişkilendirin.](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> Ayrıca [, IP 'leri bir sanal makine ölçek kümesi ile ilişkilendirebilirsiniz](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Standart yük dengeleyiciler | Genel IP 'Leri bir önekten ön uç IP yapılandırmanıza veya bir yük dengeleyicinin giden kuralına ilişkilendirirken, Azure genel IP adresi alanının basitleştirilmesini sağlar. Bitişik bir IP adresi aralığından giden bağlantıları taşıyarak senaryonuzu kolaylaştırın. | IP 'Leri bir önekden yük dengeleyicinizle ilişkilendirmek için: </br> 1. [bir ön ek oluşturun.](manage-public-ip-address-prefix.md) </br> 2. [önekten BIR IP oluşturun.](manage-public-ip-address-prefix.md) </br> 3. yük dengeleyici oluştururken yukarıdaki 2. adımda oluşturulan IP 'yi yük dengeleyicinizin ön uç IP 'si olarak seçin veya güncelleştirin. |
| Azure Güvenlik Duvarı | Giden SNAT için bir önekte ortak IP kullanabilirsiniz. Tüm giden sanal ağ trafiği [Azure Güvenlik Duvarı](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) genel IP 'ye çevrilir. | Bir IP 'yi bir önekten güvenlik duvarınızla ilişkilendirmek için: </br> 1. [bir ön ek oluşturun.](manage-public-ip-address-prefix.md) </br> 2. [önekten BIR IP oluşturun.](manage-public-ip-address-prefix.md) </br> 3. [Azure Güvenlik duvarını dağıtırken](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), daha önce ÖNEKDEN verdiğiniz IP 'yi seçtiğinizden emin olun.|
| VPN Gateway (AZ SKU) veya Application Gateway v2 | Bölgesel olarak yedekli VPN veya Application Gateway v2 için bir önekten ortak IP kullanabilirsiniz. | Bir IP 'yi bir önekten ağ geçidinize ilişkilendirmek için: </br> 1. [bir ön ek oluşturun.](manage-public-ip-address-prefix.md) </br> 2. [önekten BIR IP oluşturun.](manage-public-ip-address-prefix.md) </br> 3. [VPN Gateway](../vpn-gateway/tutorial-create-gateway-portal.md) veya [Application Gateway](../application-gateway/quick-create-portal.md#create-an-application-gateway)dağıtırken, ön EKTEN daha önce verdiğiniz IP 'yi seçtiğinizden emin olun.|

## <a name="constraints"></a>Kısıtlamalar

- Ön ek için IP adreslerini belirtemezsiniz. Azure, belirttiğiniz boyuta göre önek için IP adreslerini sağlar.
- En fazla 16 IP adresi veya/28 varsayılan olarak bir ön ek oluşturabilirsiniz. [Ağ sınırlarını](../azure-portal/supportability/networking-quota-requests.md) gözden geçirin daha fazla bilgi için Istekleri ve [Azure sınırlarını](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) arttırın.
- Öneki oluşturduktan sonra aralığı değiştiremezsiniz.
- Ön ek aralığından yalnızca standart SKU ile oluşturulan statik genel IP adresleri atanabilir. Genel IP adresi SKU 'Ları hakkında daha fazla bilgi için bkz. [genel IP adresi](./public-ip-addresses.md#public-ip-addresses).
- Aralıktaki adresler yalnızca Azure Resource Manager kaynaklarına atanabilir. Adresler, klasik dağıtım modelindeki kaynaklara atanamaz.
- Önekten oluşturulan tüm genel IP adreslerinin, ön ek olarak aynı Azure bölgesinde ve abonelikte mevcut olması gerekir. Adreslerin aynı bölgedeki ve abonelikteki kaynaklara atanması gerekir.
- İçindeki herhangi bir adres bir kaynakla ilişkili genel IP adresi kaynaklarına atanırsa bir ön eki silemezsiniz. İlk olarak, IP adreslerinin atandığı tüm genel IP adresi kaynaklarının ilişkisini kaldırın.


## <a name="next-steps"></a>Sonraki adımlar

- Genel IP adresi öneki [oluşturma](manage-public-ip-address-prefix.md)