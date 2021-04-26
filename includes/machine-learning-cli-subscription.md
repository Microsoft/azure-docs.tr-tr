---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 493c674fa161bf33436e560fdcbb9196410db931
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102209483"
---
> [!TIP]
> Oturum açtıktan sonra, Azure hesabınızla ilişkili Aboneliklerin listesini görürsünüz. Abonelik bilgileri, `isDefault: true` Azure CLI komutları için şu anda etkinleştirilmiş olan abonelikdir. Bu abonelik, Azure Machine Learning çalışma alanınızı içeren bir aynı olmalıdır. Çalışma alanınızın genel bakış sayfasını ziyaret ederek [Azure Portal](https://portal.azure.com) abonelik kimliğini bulabilirsiniz. Çalışma alanı nesnesinden abonelik KIMLIĞINI almak için SDK 'Yı da kullanabilirsiniz. Örneğin, `Workspace.from_config().subscription_id`.
> 
> Başka bir abonelik seçmek için komutunu kullanın `az account set -s <subscription name or ID>` ve geçiş yapılacak abonelik adını veya kimliğini belirtin. Abonelik seçimi hakkında daha fazla bilgi için bkz. [birden çok Azure aboneliği kullanma](/cli/azure/manage-azure-subscriptions-azure-cli).