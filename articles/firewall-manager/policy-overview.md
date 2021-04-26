---
title: Azure Güvenlik Duvarı Yöneticisi ilkesine genel bakış
description: Azure Güvenlik Duvarı Yöneticisi ilkeleri hakkında bilgi edinin
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: victorh
ms.openlocfilehash: 73a07af0fa98adf66d6104f1ab545d31a0cfd6d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95802020"
---
# <a name="azure-firewall-manager-policy-overview"></a>Azure Güvenlik Duvarı Yöneticisi ilkesine genel bakış

Güvenlik Duvarı Ilkesi NAT, ağ ve uygulama kuralı koleksiyonlarını ve tehdit bilgileri ayarlarını içeren bir Azure kaynağıdır. Bu, güvenli sanal hub 'Larda ve hub sanal ağlarında bulunan birden çok Azure Güvenlik Duvarı örneği genelinde kullanılabilen küresel bir kaynaktır. İlkeler bölgeler ve abonelikler arasında çalışır.

![Azure Güvenlik Duvarı Yöneticisi ilkesi](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>İlke oluşturma ve ilişkilendirme

Bir ilke, Azure portal, REST API, şablonlar, Azure PowerShell ve CLı dahil olmak üzere birden çok şekilde oluşturulabilir ve yönetilebilir.

Ayrıca, portala veya Azure PowerShell ilke oluşturmak için Portal kullanarak Azure güvenlik duvarından mevcut kuralları geçirebilirsiniz. Daha fazla bilgi için bkz. [Azure Güvenlik duvarı yapılandırmalarının Azure Güvenlik Duvarı ilkesine geçirilmesi](migrate-to-policy.md). 

İlkeler, bir veya daha fazla sanal hub veya VNET ile ilişkilendirilebilir. Güvenlik Duvarı, hesabınızla ve herhangi bir bölgede ilişkili herhangi bir abonelikte olabilir.

## <a name="hierarchical-policies"></a>Hiyerarşik ilkeler

Yeni ilkeler sıfırdan oluşturulabilir veya var olan ilkelerden devralınabilir. Devralma DevOps 'ın kuruluş uygulanan temel ilkesi üzerinde yerel güvenlik duvarı ilkeleri oluşturmasına izin verir.

Boş olmayan üst ilkelerle oluşturulan ilkeler, tüm kural koleksiyonlarını üst ilkeden alırlar. Bir üst ilkeden devralınan ağ kuralı koleksiyonları, yeni bir ilkenin bir parçası olarak tanımlanan ağ kuralı koleksiyonlarının üzerine her zaman öncelik atanır. Aynı mantık, uygulama kuralı koleksiyonları için de geçerlidir. Bununla birlikte, devralma ne olursa olsun, ağ kuralı koleksiyonları her zaman uygulama kuralı koleksiyonlarından önce işlenir.

Tehdit bilgileri modu üst ilkeden de devralınır. Bu davranışı geçersiz kılmak için tehdit bilgileri modınızı farklı bir değere ayarlayabilirsiniz, ancak bunu devre dışı bırakabilirsiniz. Yalnızca daha sıkı bir değerle geçersiz kılmak mümkündür. Örneğin, ana ilkeniz **yalnızca uyarı** olarak ayarlandıysa, bu yerel ilkeyi **uyarı ve reddetme** için yapılandırabilirsiniz.

Tehdit zekası modu gibi tehdit bilgileri izin verilenler listesi üst ilkeden devralınır. Alt ilke, izin verilenler listesine ek IP adresleri ekleyebilir.

NAT kuralı koleksiyonları, belirli bir güvenlik duvarına özgü olduklarından devralınmaz.

Devralma ile, üst ilkede yapılan tüm değişiklikler otomatik olarak ilişkili güvenlik duvarı alt ilkelerine uygulanır.

## <a name="traditional-rules-and-policies"></a>Geleneksel kurallar ve ilkeler

Azure Güvenlik Duvarı hem geleneksel kuralları hem de ilkeleri destekler. Aşağıdaki tabloda ilkeleri ve kuralları karşılaştırılmaktadır:


| Konu | İlke  | Kurallar |
| ------- | ------- | ----- |
|Contains     |NAT, ağ, uygulama kuralları, özel DNS ve DNS proxy ayarları, IP grupları ve tehdit zekası ayarları (izin verilenler listesi dahil)|NAT, ağ ve uygulama kuralları, özel DNS ve DNS proxy ayarları, IP grupları ve tehdit zekası ayarları (izin verilenler listesi dahil)|
|Koru     |Sanal hub 'lar ve sanal ağlar|Yalnızca sanal ağlar|
|Portal deneyimi     |Güvenlik Duvarı Yöneticisi kullanarak merkezi yönetim|Tek başına güvenlik duvarı deneyimi|
|Birden çok güvenlik duvarı desteği     |Güvenlik Duvarı Ilkesi, güvenlik duvarları genelinde kullanılabilecek ayrı bir kaynaktır|Kuralları el ile dışarı ve içeri aktarma veya üçüncü taraf yönetim çözümlerini kullanma |
|Fiyatlandırma     |Güvenlik Duvarı ilişkilendirmesine göre faturalandırılır. Bkz. [fiyatlandırma](#pricing).|Ücretsiz|
|Desteklenen Dağıtım mekanizmaları     |Portal, REST API, şablonlar, Azure PowerShell ve CLı|Portal, REST API, şablonlar, PowerShell ve CLı. |

## <a name="pricing"></a>Fiyatlandırma

İlkeler, güvenlik duvarı ilişkilerine göre faturalandırılır. Sıfır veya bir güvenlik duvarı ilişkilendirmesine sahip bir ilke ücretsizdir. Birden çok güvenlik duvarı ilişkilendirmesi olan bir ilke sabit bir fiyat üzerinden faturalandırılır. Daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Yöneticisi fiyatlandırması](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="next-steps"></a>Sonraki adımlar

Azure Güvenlik duvarının nasıl dağıtılacağını öğrenmek için bkz. [öğretici: Azure Portal kullanarak bulut ağınızı Azure Güvenlik Duvarı Yöneticisi Ile güvenli hale getirme](secure-cloud-network.md).
