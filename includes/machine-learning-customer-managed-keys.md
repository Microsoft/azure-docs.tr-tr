---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 2bba53410834aadce5627a34a759e65aa0e11c28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94574274"
---
> [!IMPORTANT]
> Cosmos DB örneği, __aboneliğinizdeki__ Microsoft tarafından yönetilen bir kaynak grubunda ve ihtiyaç duyacağı tüm kaynaklarla birlikte oluşturulur. Bu, Cosmos DB örneği için ücretlendirildiğiniz anlamına gelir. Yönetilen kaynak grubu biçiminde adlandırılır `<AML Workspace Resource Group Name><GUID>` . Azure Machine Learning çalışma alanınız özel bir uç nokta kullanıyorsa, Cosmos DB örneği için bir sanal ağ de oluşturulur. Bu sanal ağ Cosmos DB ve Azure Machine Learning arasındaki iletişimin güvenliğini sağlamak için kullanılır.
> 
> * Bu Cosmos DB örneğini içeren __kaynak grubunu__ veya bu grupta otomatik olarak oluşturulan kaynakları silmeyin. Kaynak grubunu, Cosmos DB örneğini, vb. silmeniz gerekiyorsa, onu kullanan Azure Machine Learning çalışma alanını silmeniz gerekir. Kaynak grubu, Cosmos DB örneği ve diğer otomatik oluşturulan kaynaklar ilişkili çalışma alanı silindiğinde silinir.
> * Bu Cosmos DB hesabının varsayılan [__Istek birimleri__](../articles/cosmos-db/request-units.md) __8000__' de ayarlanır.
> * Oluşturulan __Cosmos DB örneğiyle birlikte kullanmak için kendi VNET 'i sağlayamezsiniz__ . Ayrıca __sanal ağı__ değiştiremezsiniz. Örneğin, kullandığı IP adres aralığını değiştiremezsiniz.