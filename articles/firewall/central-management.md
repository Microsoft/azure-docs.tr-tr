---
title: Azure Güvenlik duvarı merkezi yönetimi
description: Azure Güvenlik Duvarı Yöneticisi Merkezi Yönetimi hakkında bilgi edinin
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: victorh
ms.openlocfilehash: 23a7682d8a64de57db4ff9ae785ada90d4a06944
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87084679"
---
# <a name="azure-firewall-central-management"></a>Azure Güvenlik duvarı merkezi yönetimi

Birden çok güvenlik duvarını yönetiyorsanız, Güvenlik Duvarı kurallarının sürekli olarak değiştirilmesini, bunları eşitlenmiş halde tutmayı zorlaştırır. Merkezi BT ekiplerinin, temel güvenlik duvarı ilkelerini tanımlamak ve bunları birden çok iş biriminde zorunlu kılmak için bir yol gerekir. Aynı zamanda DevOps ekipleri, daha iyi çeviklik için kendi yerel türetilmiş güvenlik duvarı ilkelerini oluşturmak ister.

Azure Güvenlik Duvarı Yöneticisi bu sorunların çözülmesine yardımcı olabilir.


## <a name="azure-firewall-manager"></a>Azure Güvenlik Duvarı Yöneticisi

Azure Güvenlik Duvarı Yöneticisi, bulut tabanlı güvenlik perimeters için merkezi güvenlik ilkesi ve Yönlendirme yönetimi sağlayan bir ağ güvenliği yönetim hizmetidir. Kurumsal BT ekiplerinin birden çok Azure Güvenlik Duvarı örneği arasında trafik filtrelemesi için ağ ve uygulama düzeyi kuralları merkezi olarak tanımlamasını kolaylaştırır. Trafiği idare ve koruma için hub ve bağlı bileşen mimarilerinde farklı Azure bölgelerine ve aboneliklerine yayılabilir. Ayrıca, kuruluşlar genelinde uygulanan türetilmiş yerel güvenlik duvarı güvenlik ilkeleriyle DevOps daha iyi bir çeviklik sağlar.

### <a name="firewall-policy"></a>Güvenlik duvarı ilkesi

Güvenlik duvarı ilkesi NAT, ağ ve uygulama kuralı koleksiyonlarını ve tehdit bilgileri ayarlarını içeren bir Azure kaynağıdır. Bu, *güvenli sanal hub 'larda* ve *hub sanal ağlarında* bulunan birden çok Azure Güvenlik Duvarı örneği genelinde kullanılabilen küresel bir kaynaktır. Yeni ilkeler sıfırdan oluşturulabilir veya var olan ilkelerden devralınabilir. Devralma DevOps 'ın kuruluş uygulanan temel ilkesi üzerinde yerel güvenlik duvarı ilkeleri oluşturmasına izin verir. İlkeler bölgeler ve abonelikler arasında çalışır.
 
Azure Güvenlik Duvarı Yöneticisi ile güvenlik duvarı Ilkesi ve ilişkilendirmeleri oluşturabilirsiniz. Ancak, REST API, şablonları, Azure PowerShell ve CLı kullanarak da bir ilke oluşturabilir ve yönetebilirsiniz. Bir ilke oluşturduktan sonra, bunu sanal bir WAN hub 'ında *güvenli bir sanal hub* ve/veya bir sanal ağ üzerinde bir güvenlik duvarı oluşturarak BT *hub 'ı sanal ağı* yapan bir güvenlik duvarıyla ilişkilendirebilirsiniz.

### <a name="pricing"></a>Fiyatlandırma

İlkeler, güvenlik duvarı ilişkilerine göre faturalandırılır. Sıfır veya bir güvenlik duvarı ilişkilendirmesine sahip bir ilke ücretsizdir. Birden çok güvenlik duvarı ilişkilendirmesi olan bir ilke sabit bir fiyat üzerinden faturalandırılır. Daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Yöneticisi fiyatlandırması](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="azure-firewall-management-partners"></a>Azure Güvenlik Duvarı Yönetim iş ortakları

Aşağıdaki önde gelen üçüncü taraf çözümler, standart Azure REST API 'Lerini kullanarak Azure Güvenlik duvarı merkezi yönetimini destekler. Bu çözümlerin her biri kendi benzersiz özelliklerine ve özelliklerine sahiptir:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barbcuda bulut güvenlik koruyucu](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Sonraki adımlar

Azure Güvenlik Duvarı Yöneticisi hakkında daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Yöneticisi nedir?](../firewall-manager/overview.md)