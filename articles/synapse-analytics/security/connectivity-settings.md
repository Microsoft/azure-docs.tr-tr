---
title: Azure SYNAPSE bağlantı ayarları
description: Azure SYNAPSE Analytics 'te bağlantı ayarlarını yapılandırmanızı öğretir bir makale
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 03/15/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e0d8a8e3320b49b6fbe3e8ab66c0b4569fac9afd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587942"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Azure SYNAPSE Analytics bağlantı ayarları

Bu makale, Azure SYNAPSE Analytics 'teki bağlantı ayarlarını ve bunların uygun olduğu yerlerde nasıl yapılandırılacağını açıklar.


## <a name="connection-policy"></a>Bağlantı ilkesi
Azure SYNAPSE Analytics 'te SYNAPSE SQL bağlantı ilkesi *varsayılan* olarak ayarlanır. Bunu Azure SYNAPSE Analytics 'te değiştiremezsiniz. Bunun Azure [SYNAPSE Analytics 'Te](../../azure-sql/database/connectivity-architecture.md#connection-policy)SYNAPSE SQL bağlantılarını nasıl etkilediği hakkında daha fazla bilgi edinebilirsiniz. 

## <a name="minimal-tls-version"></a>En düşük TLS sürümü
Azure SYNAPSE Analytics 'te SYNAPSE SQL, tüm TLS sürümlerini kullanarak bağlantılara izin verir. Azure SYNAPSE Analytics 'te SYNAPSE SQL için en düşük TLS sürümünü ayarlayamazsınız.

## <a name="next-steps"></a>Sonraki adımlar

[Azure SYNAPSE çalışma alanı](./synapse-workspace-ip-firewall.md) oluşturma