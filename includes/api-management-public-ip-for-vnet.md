---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 6ed30ed2333393e9d8c0222500aaf3f17da6d33d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531637"
---
* İstemciniz API sürüm 2021-01-01-önizleme veya üzerini kullanıyorsa **Standart SKU [genel IPv4 adresi](../articles/virtual-network/public-ip-addresses.md#standard)**. Dış veya iç erişim için sanal ağ ayarlanırken genel IP adresi kaynağı gereklidir. Bir iç sanal ağ ile genel IP adresi yalnızca yönetim işlemleri için kullanılır. [API Management IP adresleri](../articles/api-management/api-management-howto-ip-addresses.md)hakkında daha fazla bilgi edinin.

  * IP adresi, API Management örneğiyle ve sanal ağla aynı bölgede ve abonelikte olmalıdır.

  * IP adresinin değeri, bu bölgedeki API Management örneğinin sanal ortak IPv4 adresi olarak atanır. 

  * Dış sanal ağdan (veya tersi) değişiklik yaparken, ağdaki alt ağları değiştirirken veya API Management örneği için kullanılabilirlik bölgelerini güncelleştirerek, farklı bir genel IP adresi yapılandırmanız gerekir. 

  > [!IMPORTANT]
  > Şu anda Azure portal, bir API Management örneği oluştururken veya güncelleştirirken API sürüm 2021-01-01 önizlemesi kullanır. Bu API sürümünü, bir Azure Resource Manager şablonu veya API Management REST API kullanarak belirtebilirsiniz. Azure CLı ve Azure PowerShell Şu anda API sürüm 2020-12-01 ' i desteklemektedir.
