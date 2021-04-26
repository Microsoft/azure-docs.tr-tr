---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96023216"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Veri deponuzu şirket içi ağ, bir Azure sanal ağı veya Amazon sanal özel bulutu içindeyse, kendisine bağlanmak için şirket içinde [barındırılan bir tümleştirme çalışma zamanı](../articles/data-factory/create-self-hosted-integration-runtime.md) yapılandırmanız gerekir.

Alternatif olarak, veri depolu, yönetilen bir bulut veri hizmeti ise Azure tümleştirme çalışma zamanı ' nı kullanabilirsiniz. Erişim, güvenlik duvarı kurallarında onaylanan IP 'Ler ile sınırlandırılır, izin verilenler listesine [Azure Integration Runtime IP 'leri](../articles/data-factory/azure-integration-runtime-ip-addresses.md) ekleyebilirsiniz. 

Data Factory tarafından desteklenen ağ güvenlik mekanizmaları ve seçenekleri hakkında daha fazla bilgi için bkz. [veri erişim stratejileri](../articles/data-factory/data-access-strategies.md).
