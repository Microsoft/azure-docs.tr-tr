---
title: Azure Güvenlik Duvarı hizmet etiketlerine genel bakış
description: Hizmet etiketi, güvenlik kuralı oluşturma sırasındaki karmaşıklığı en aza indirmeye yardımcı olmak için bir IP adresi ön eki grubunu temsil eder.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ec9fb3c9b27d21ac65888379f59d51ecc8042eb0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168682"
---
# <a name="azure-firewall-service-tags"></a>Azure Güvenlik Duvarı hizmeti etiketleri

Hizmet etiketi, güvenlik kuralı oluşturma sırasındaki karmaşıklığı en aza indirmeye yardımcı olmak için bir IP adresi ön eki grubunu temsil eder. Kendi hizmet etiketinizi oluşturamaz veya bir etiket içinde yer alacak IP adreslerini belirleyemezsiniz. Hizmet etiketine dahil olan adres ön ekleri Microsoft tarafından yönetilir ve hizmet etiketi adresler değiştikçe otomatik olarak güncelleştirilir.

Azure Güvenlik Duvarı hizmet etiketleri ağ kuralları hedef alanında kullanılabilir. Bunları belirli IP adresleri yerine kullanabilirsiniz.

## <a name="supported-service-tags"></a>Desteklenen hizmet etiketleri

Azure Güvenlik Duvarı ağ kurallarında kullanılabilecek hizmet etiketlerinin listesi için bkz. [güvenlik grupları](../virtual-network/security-overview.md#service-tags) .

## <a name="next-steps"></a>Sonraki adımlar

Azure Güvenlik duvarı kuralları hakkında daha fazla bilgi edinmek için bkz. [Azure Güvenlik Duvarı kural işleme mantığı](rule-processing.md).